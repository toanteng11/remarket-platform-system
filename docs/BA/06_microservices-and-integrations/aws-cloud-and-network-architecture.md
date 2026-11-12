# Kiến Trúc Hạ Tầng AWS Cloud & Bảo Mật Mạng — AWS Cloud Architecture

| Tài liệu | Kiến Trúc Hạ Tầng Điện Toán Đám Mây AWS & Mạng Bảo Mật (AWS Cloud & VPC Architecture) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-06-AWS` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phân hệ cha** | [docs/BA/06_microservices-and-integrations/README.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/README.md) |
| **Liên kết truy vết** | [open-questions-and-decisions.md](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/open-questions-and-decisions.md) (`DEC-DIR-03..04`, `Q-DATA-06`, `Q-DR-06`), [non-functional-requirements.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/non-functional-requirements.md) (`NFR-SEC-01..05`, `NFR-COST-01`) |

---

## 1. Kiến Trúc Mạng AWS VPC Đa Tầng Bảo Mật (Multi-AZ VPC with Network Firewall & WAF)

Hệ thống ReMarket được triển khai trên nền tảng **AWS Cloud (Vùng `ap-southeast-1` Singapore)** với kiến trúc mạng VPC cô lập trải rộng trên 3 Availability Zones (`AZ-a`, `AZ-b`, `AZ-c`) kết hợp hệ thống tường lửa **AWS WAF v2** và **AWS Network Firewall** bảo vệ đa tầng (xem đặc tả chi tiết tại [network-firewall-and-waf-security.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/network-firewall-and-waf-security.md)):

```mermaid
flowchart TB
    INTERNET(("Internet Users & Webhooks")) --> CLOUDFRONT["Amazon CloudFront CDN + AWS WAF v2 (Edge WebACL)\n(DDoS Shield, Geo-blocking VN, OWASP Top 10, Rate Limits)"]
    
    subgraph AWS_VPC["Amazon Virtual Private Cloud (VPC: 10.0.0.0/16)"]
        CLOUDFRONT --> IGW["Internet Gateway (IGW)"]

        subgraph TIER_FIREWALL["TẦNG 0: Firewall Subnets (10.0.0.0/28, 10.0.0.16/28)"]
            NET_FW["AWS Network Firewall Endpoints\n(Stateful Packet Inspection, DPI Suricata, Egress Domain Filtering)"]
        end

        subgraph TIER_PUBLIC["TẦNG 1: Public Subnets (10.0.1.0/24, 10.0.2.0/24)"]
            ALB["Application Load Balancer (ALB) + Regional WAF v2\n(Multi-AZ Ingress Router & Webhook Whitelisting)"]
            NAT_GW["NAT Gateways (Định tuyến Internet 1 chiều qua Network Firewall)"]
        end

        subgraph TIER_PRIVATE["TẦNG 2: Private Application Subnets (10.0.10.0/24, 10.0.20.0/24)"]
            subgraph COMPUTE_CLUSTER["Amazon EKS / ECS Fargate Cluster"]
                POD_GW["SVC-00: API Gateway"]
                POD_SVCS["SVC-01..09: 9 Microservices Containers"]
            end
            MSK_KAFKA["Amazon MSK (Apache Kafka Cluster)\n(Message Bus Phân Tán)"]
        end

        subgraph TIER_ISOLATED["TẦNG 3: Isolated Data Subnets (10.0.100.0/24, 10.0.200.0/24)"]
            RDS_SQL[("AWS RDS for SQL Server\n(Primary AZ-a & Sync Standby AZ-b)")]
            ELASTI_CACHE[("Amazon ElastiCache for Redis\n(Multi-AZ Redis Cluster)")]
            OPEN_SEARCH[("Amazon OpenSearch Service\n(Search Read Model)")]
            MONGO_VPC[("MongoDB Atlas VPC Peering\n(CatalogDB, ShippingDB)")]
        end
    end

    subgraph AWS_MANAGED_SERVICES["Dịch Vụ Lưu Trữ & An Ninh AWS Quản Trị"]
        S3_MEDIA["Amazon S3 Media Buckets\n(Listing Photos, Unboxing Videos)"]
        AWS_KMS["AWS Key Management Service (KMS)\n(Customer Managed Keys)"]
        AWS_SECRETS["AWS Secrets Manager\n(Tự Động Xoay Vòng Mật Khẩu DB)"]
    end

    %% Network Connections
    IGW <--> NET_FW
    NET_FW <--> ALB
    ALB --> POD_GW
    POD_GW --> POD_SVCS
    POD_SVCS --> MSK_KAFKA
    POD_SVCS --> RDS_SQL
    POD_SVCS --> ELASTI_CACHE
    POD_SVCS --> OPEN_SEARCH
    POD_SVCS --> MONGO_VPC
    POD_SVCS --> NAT_GW
    NAT_GW <--> NET_FW

    %% Storage & Security
    POD_SVCS -.->|IAM Roles for Service Accounts (IRSA)| S3_MEDIA
    POD_SVCS -.->|Decrypt / Encrypt PII| AWS_KMS
    POD_SVCS -.->|Fetch Credentials| AWS_SECRETS
```

---

## 2. Đặc Tả Chi Tiết 4 Tầng Mạng (VPC Subnet Tiers with Network Firewall)

1. **Tầng 0 — Firewall Subnets (Tầng Tường Lửa Mạng Chuyên Dụng):**
   - Chứa các **AWS Network Firewall Endpoints** (`10.0.0.0/28`, `10.0.0.16/28`) đa AZ.
   - Sử dụng VPC Ingress Routing: 100% lưu lượng từ Internet Gateway (IGW) đi vào ALB hoặc từ NAT Gateway đi ra ngoài Internet đều bị buộc phải chuyển hướng qua cụm Network Firewall để thực hiện kiểm tra gói tin chuyên sâu (Stateful DPI Suricata) và lọc tên miền Egress (xem chi tiết tại [network-firewall-and-waf-security.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/network-firewall-and-waf-security.md)).
2. **Tầng 1 — Public Subnets (Tầng Công Khai):**
   - Chỉ chứa **Application Load Balancer (ALB)** và **NAT Gateways**.
   - Tuyệt đối **không đặt bất kỳ máy chủ ứng dụng hoặc cơ sở dữ liệu nào** tại tầng này.
   - ALB gắn với AWS WAF v2 Regional WebACL để lọc chuyên sâu các truy vấn HTTP/HTTPS và kiểm tra chữ ký Webhook của đối tác.
3. **Tầng 2 — Private Application Subnets (Tầng Ứng Dụng Nội Bộ):**
   - Chứa cụm máy chủ container **Amazon EKS (Kubernetes) / ECS Fargate** chạy 9 Microservices và cụm **Amazon MSK (Kafka)**.
   - Các Container không có Public IP; chỉ có thể truy cập Internet 1 chiều qua NAT Gateway (được kiểm soát bởi Network Firewall Egress Whitelist) để gọi API bên ngoài (ngân hàng, 3PL).
4. **Tầng 3 — Isolated Data Subnets (Tầng Dữ Liệu Cách Ly Tuyệt Đối):**
   - Chứa CSDL **AWS RDS SQL Server, Redis Cluster, OpenSearch Cluster**.
   - **Đặc điểm an ninh tối cao:** Không có bất kỳ Route Table nào dẫn ra Internet Gateway hay NAT Gateway. Cấm hoàn toàn mọi truy cập từ bên ngoài mạng VPC.

---

## 3. Kiến Trúc Bảo Mật Zero-Trust & Quản Trị Bí Mật (`NFR-SEC-01..05`)

### 3.1. Ma Trận Tường Lửa Security Groups (Deny by Default)
Mọi luồng lưu lượng mạng đều bị chặn mặc định, chỉ mở cổng khi thỏa mãn nguyên tắc đặc quyền tối thiểu (Least Privilege):

| Security Group Đích | Port Cho Phép | Nguồn Được Phép Kết Nối (Source Security Group) | Ý Nghĩa Kỹ Thuật |
| :--- | :---: | :--- | :--- |
| `sg_alb_ingress` | `443 (HTTPS)` | `CloudFront IP Prefix List` | Chỉ chấp nhận lưu lượng đã đi qua CloudFront CDN và AWS WAF. |
| `sg_gateway_app` | `8080 (HTTP)` | `sg_alb_ingress` | API Gateway chỉ nhận kết nối từ Load Balancer. |
| `sg_microservices`| `5000 (gRPC)` | `sg_gateway_app`, `sg_microservices` | Các service chỉ nhận kết nối nội bộ từ Gateway hoặc service đồng cấp. |
| `sg_rds_sqlserver`| `1433 (TDS)` | `sg_order_svc`, `sg_payment_svc`, `sg_identity_svc` | **Chỉ đúng 3 service nghiệp vụ này được kết nối DB; cấm toàn bộ các service khác**. |
| `sg_elasticache` | `6379 (Redis)` | `sg_microservices` | Chỉ cho phép các microservices trong Private Subnet truy cập cache. |

---

### 3.2. Quản Trị Khóa Mã Hóa (AWS KMS) & Bí Mật (AWS Secrets Manager)
1. **AWS Key Management Service (KMS):**
   - Sử dụng **Customer Managed Key (CMK)** độc lập để mã hóa toàn bộ dữ liệu ở trạng thái nghỉ (Encryption-at-Rest) trên ổ đĩa EBS của RDS, S3 Buckets, và mẩu dữ liệu nhạy cảm PII trong CSDL (`Field-Level Encryption`).
2. **AWS Secrets Manager:**
   - Lưu trữ toàn bộ chuỗi kết nối Database, API Secret Keys của VNPAY, MoMo, GHN.
   - Kích hoạt tính năng **Automatic Rotation**: Mật khẩu quản trị CSDL được tự động xoay vòng định kỳ mỗi 30 ngày mà không làm gián đoạn kết nối của ứng dụng.
3. **Định Danh Dịch Vụ Không Dùng Khóa Cứng (IAM Roles for Service Accounts - IRSA):**
   - Mỗi Pod Microservice trong EKS được gắn với một IAM Role độc lập thông qua OIDC Provider.
   - Dịch vụ `MediaService` tự động nhận quyền cấp Presigned URL trên S3 mà **hoàn toàn không cần lưu trữ cứng AWS Access Key / Secret Key** trong file cấu hình hay mã nguồn (`NFR-SEC-03`).

---

## 4. Chiến Lược Auto-Scaling & Tối Ưu Hóa Chi Phí (`NFR-COST-01`)

### 4.1. Chính Sách Tự Động Co Giãn (Auto-Scaling Policies)
- **Tầng Ứng Dụng (Container Compute):**
  - Cấu hình Horizontal Pod Autoscaler (HPA) dựa trên 2 chỉ số kết hợp: CPU Utilization $\ge 70\%$ và Số lượng Request đồng thời.
  - Tự động co giãn từ **2 pods (lúc thấp điểm ban đêm) lên tới 20 pods (giờ cao điểm 12:00 và 20:00)** chỉ trong vòng 60 giây.
- **Tầng Cơ Sở Dữ Liệu (RDS Storage Auto-scaling):**
  - Tự động mở rộng dung lượng ổ đĩa lưu trữ (Storage Auto-scaling) khi dung lượng trống còn dưới 15%, tránh sự cố tràn ổ cứng gây sập CSDL.

### 4.2. Lộ Trình Hạ Tầng Tối Ưu Chi Phí Theo Giai Đoạn Dự Án
- **Giai đoạn 1: Demo & Kiểm thử Nghiệp vụ (Cost Ceiling Optimization):**
  - Tận dụng chia sẻ CSDL hợp lý (Logical Database Isolation trên 1 RDS Instance `db.t3.medium`).
  - Sử dụng AWS Fargate Spot để giảm 70% chi phí compute cho các dịch vụ phụ trợ.
- **Giai đoạn 2: Vận Hành Thương Mại Quy Mô Lớn (Production SLA 99.9%):**
  - Kích hoạt cụm RDS Multi-AZ chuyên dụng cho `OrderDB` và `PaymentDB`.
  - Ký kết AWS Savings Plans 1 năm để giảm 40% chi phí hạ tầng tổng thể.

