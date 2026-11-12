# Sơ Đồ Kiến Trúc Đám Mây AWS, Tường Lửa Mạng (Firewall), WAF v2 & An Ninh Dữ Liệu – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Tài liệu | Tập hợp 5 Sơ đồ Trực quan Hạ tầng AWS Cloud, VPC 4 Tầng, Phòng thủ 5 Lớp, AWS Network Firewall, AWS WAF v2 & KMS/PII Security |
| Mã tài liệu | `DOC-BA-09-AWS-SEC` |
| Phiên bản | 1.1 |
| Trạng thái | Hoàn thiện (Chuẩn hóa Trực quan cho Kiến trúc An ninh Hạ tầng & Tường lửa) |
| Ngày cập nhật | 12/11/2026 |
| Phân hệ cha | [docs/BA/09_architecture-and-process-diagrams/README.md](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md) |
| Tài liệu liên quan | [aws-cloud-and-network-architecture.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/aws-cloud-and-network-architecture.md), [network-firewall-and-waf-security.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/network-firewall-and-waf-security.md) |

---

## 1. Sơ Đồ Mạng AWS Multi-AZ 4 Tầng Subnet với Network Firewall & WAF (`DIAG-SEC-01`)

Sơ đồ thể hiện kiến trúc mạng AWS VPC (`10.0.0.0/16`) tại vùng `ap-southeast-1` (Singapore) phân tách thành **4 Tầng Subnet độc lập** trên 3 Availability Zones (`AZ-a`, `AZ-b`, `AZ-c`):

```mermaid
flowchart TB
    subgraph EXT_CLIENTS ["NGƯỜI DÙNG & ĐỐI TÁC NGOÀI INTERNET"]
        BUYER_CLI["Khách Mua (Web / Mobile App)"]
        SELLER_CLI["Người Bán (Seller Center)"]
        PSP_WH["Webhook Cổng Thanh Toán (VNPAY / MoMo)"]
        CARRIER_WH["Webhook Hãng Vận Chuyển (GHN / GHTK)"]
    end

    EXT_CLIENTS --> EDGE_WAF

    subgraph AWS_EDGE ["TẦNG BIÊN AWS EDGE INFRASTRUCTURE"]
        EDGE_WAF["Amazon CloudFront CDN + AWS WAF v2 WebACL\n- AWS Shield Standard (Chống DDoS Lớp 3/4)\n- Geo-blocking: Chỉ cho phép IP Việt Nam (CountryCode = VN)\n- Managed Rules: Common, Bad Inputs, SQLi, IP Reputation\n- Rate Limiting: 50 req/5m cho Checkout, 20 req/5m cho Auth"]
    end

    EDGE_WAF -->|"Traffic Hợp Lệ"| IGW["Internet Gateway (IGW)"]

    subgraph AWS_VPC ["AMAZON VPC: 10.0.0.0/16 (VÙNG AP-SOUTHEAST-1 SINGAPORE - MULTI-AZ)"]
        IGW <--> TIER_0

        subgraph TIER_0 ["TẦNG 0: FIREWALL SUBNETS (10.0.0.0/28, 10.0.0.16/28, 10.0.0.32/28)"]
            NET_FW["AWS Network Firewall Endpoints (Multi-AZ)\n- Stateful Packet Inspection (SPI) & DPI Suricata\n- Intrusion Detection & Prevention (IDS/IPS)\n- Egress Domain Whitelist: *.vnpay.vn, *.momo.vn, *.ghn.vn, *.ghtk.vn"]
        end

        TIER_0 <--> TIER_1

        subgraph TIER_1 ["TẦNG 1: PUBLIC SUBNETS (10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24)"]
            ALB["Application Load Balancer (ALB) + Regional WAF v2\n- Kiểm tra IP Whitelist & HMAC cho Webhook\n- TLS 1.3 Termination (AWS Certificate Manager)\n- Ingress Router điều phối traffic"]
            NAT_GW["NAT Gateways (Định tuyến Outbound 1 chiều qua Network Firewall)"]
        end

        ALB -->|"HTTP 8080"| POD_GW

        subgraph TIER_2 ["TẦNG 2: PRIVATE APPLICATION SUBNETS (10.0.10.0/24, 10.0.20.0/24, 10.0.30.0/24)"]
            subgraph COMPUTE_CLUSTER ["Cụm Tính Toán Amazon EKS / ECS Fargate"]
                POD_GW["SVC-00: API Gateway Edge"]
                POD_SVCS["SVC-01..09: 9 Microservices Containers\n(Identity, Catalog, Media, Search, Order, Payment, Shipping, Trust, Notif)"]
            end
            MSK["Amazon MSK (Apache Kafka Cluster)\n(Message Bus Sự Kiện Phân Tán EVT-01..28)"]
            POD_GW --> POD_SVCS
            POD_SVCS <--> MSK
        end

        POD_SVCS --> NAT_GW
        POD_SVCS --> TIER_3

        subgraph TIER_3 ["TẦNG 3: ISOLATED DATA SUBNETS (10.0.100.0/24, 10.0.200.0/24 - ZERO INTERNET ROUTE)"]
            RDS[("AWS RDS for SQL Server Multi-AZ\n(Primary AZ-a & Sync Standby AZ-b)\nOrderDB, PaymentDB, IdentityDB")]
            REDIS[("Amazon ElastiCache for Redis Cluster\n(Khóa Giữ Chỗ Two-Tier INV-01 & Caching)")]
            OS[("Amazon OpenSearch Service Cluster\n(Chỉ mục Tìm kiếm Tiếng Việt)")]
            MONGO[("MongoDB Atlas Replica Set (VPC Peering)\n(CatalogDB, ShippingDB, SupportDB, NotificationDB)")]
        end
    end

    subgraph AWS_SECURITY_SERVICES ["DỊCH VỤ AN NINH & BẢO VỆ DỮ LIỆU AWS"]
        KMS["AWS Key Management Service (KMS)\n(Customer Managed Key - CMK)"]
        SECRETS["AWS Secrets Manager\n(Xoay Vòng Tự Động Mật Khẩu DB 30 Ngày)"]
        WORM["Amazon S3 Audit Vault\n(Chính Sách WORM - Object Lock Compliance Mode)"]
    end

    POD_SVCS -.->|"Mã hóa / Giải mã PII"| KMS
    POD_SVCS -.->|"Lấy thông tin xác thực"| SECRETS
    POD_SVCS -.->|"Lưu trữ log kiểm toán bất biến"| WORM

    classDef edge fill:#ffebee,stroke:#c62828,stroke-width:2px;
    classDef fw fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    classDef pub fill:#fffde7,stroke:#f57f17,stroke-width:2px;
    classDef priv fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef iso fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    classDef sec fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    class EDGE_WAF edge;
    class NET_FW fw;
    class ALB,NAT_GW pub;
    class POD_GW,POD_SVCS,MSK priv;
    class RDS,REDIS,OS,MONGO iso;
    class KMS,SECRETS,WORM sec;
```

---

## 2. Sơ Đồ Kiến Trúc Phòng Thủ Đa Tầng 5 Lớp (`DIAG-SEC-02`: 5-Layer Defense-in-Depth)

Sơ đồ mô tả 5 hàng rào tường lửa nối tiếp nhau bảo vệ từ lớp biên CDN vào đến tận cơ sở dữ liệu lõi:

```mermaid
flowchart TD
    INTERNET(("Internet Clients & Webhooks")) --> L1

    subgraph LAYER_1 ["LỚP 1: TƯỜNG LỬA ỨNG DỤNG WEB BIÊN (EDGE AWS WAF v2 @ CLOUDFRONT)"]
        L1["10 Bộ Quy Tắc WAF WebACL (Priority 1 -> 10):\n- Priority 1-2: Chặn IP Blacklist, Botnet, VPN ẩn danh, Tor\n- Priority 3: Geo-blocking (Chỉ cho phép CountryCode = VN)\n- Priority 4-6: Chặn OWASP Top 10, SQLi, XSS, Bad Inputs\n- Priority 7-9: Rate-based Rules (50 req/5m Checkout, 20 req/5m Auth)"]
    end

    L1 -->|"Request vượt qua WAF Biên"| L2

    subgraph LAYER_2 ["LỚP 2: TƯỜNG LỬA MẠNG TRUNG TÂM (AWS NETWORK FIREWALL @ FIREWALL SUBNETS)"]
        L2["Kiểm Soát Gói Tin Lớp 3/4/7 (Stateful DPI Suricata):\n- Phát hiện & Ngăn chặn Xâm nhập (IDS/IPS Signatures)\n- Chặn SYN Flood, Port Scan, Malformed Packets\n- Kiểm soát Egress Domain Whitelist chống rò rỉ dữ liệu"]
    end

    L2 -->|"Gói tin mạng hợp lệ"| L3

    subgraph LAYER_3 ["LỚP 3: TƯỜNG LỬA MẠNG CON (STATELESS NETWORK ACLs)"]
        L3["Ranh Giới Cứng Cấp Subnet:\n- Chặn hoàn toàn cổng SSH (22), RDP (3389) từ Internet\n- Isolated Data Subnet NACL: Cấm 100% lưu lượng từ Public/Internet"]
    end

    L3 -->|"Đi vào Subnet đích"| L4

    subgraph LAYER_4 ["LỚP 4: TƯỜNG LỬA VI PHÂN ĐOẠN (STATEFUL SECURITY GROUPS)"]
        L4["Zero-Trust Micro-Segmentation (Deny by Default):\n- sg_alb: Chỉ nhận HTTPS 443 từ CloudFront Prefix List\n- sg_gateway: Chỉ nhận HTTP 8080 từ sg_alb\n- sg_rds_sqlserver: Chỉ mở cổng 1433 cho đúng 3 SG (Order, Payment, Identity)"]
    end

    L4 -->|"Kết nối được cấp phép"| L5

    subgraph LAYER_5 ["LỚP 5: WAF NỘI BỘ ALB, XÁC THỰC CHỮ KÝ WEBHOOK & RBAC/ABAC"]
        L5["Kiểm Soát Cấp Ứng Dụng & Nghiệp Vụ:\n- IP Whitelisting + HMAC-SHA512 cho Webhook VNPAY/MoMo/GHN/GHTK\n- Xác thực JWT + Kiểm tra quyền sở hữu tài nguyên (ABAC Resource Ownership)"]
    end

    classDef c1 fill:#ffebee,stroke:#c62828,stroke-width:2px;
    classDef c2 fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef c3 fill:#fff9c4,stroke:#f57f17,stroke-width:2px;
    classDef c4 fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef c5 fill:#e3f2fd,stroke:#1565c0,stroke-width:2px;
    class L1 c1;
    class L2 c2;
    class L3 c3;
    class L4 c4;
    class L5 c5;
```

---

## 3. Sơ Đồ Trình Tự Lọc Gói Tin Qua WAF v2, Network Firewall & Auto-Mitigation (`DIAG-SEC-03`)

Sơ đồ Sequence minh họa cách hệ thống xử lý các cuộc tấn công thực tế và cơ chế tự động thêm IP tấn công vào danh sách đen bằng AWS Lambda:

```mermaid
sequenceDiagram
    autonumber
    actor Client as Người Dùng / Bot Tấn Công
    participant CF_WAF as CloudFront CDN + AWS WAF v2
    participant Kinesis as Kinesis Firehose & CloudWatch
    participant Lambda as AWS Lambda Auto-Blocker
    participant NetFW as AWS Network Firewall (Suricata)
    participant ALB as ALB + Regional WAF
    participant App as SVC-05 Order / SVC-06 Payment

    Client->>CF_WAF: HTTPS Request tới ReMarket
    alt Tấn công 1: IP ngoài Việt Nam hoặc thuộc Botnet/Tor
        CF_WAF-->>Client: HTTP 403 Forbidden (Priority 1-3 Block tại Edge < 5ms)
    else Tấn công 2: Chứa chuỗi SQL Injection (' OR 1=1 --) hoặc XSS
        CF_WAF-->>Client: HTTP 403 Forbidden (Priority 4-6 Managed Rule Block)
        CF_WAF->>Kinesis: Đẩy WAF Block Log sang Kinesis
    else Tấn công 3: Bot spam > 50 requests/5 phút vào /api/v1/checkout/*
        CF_WAF-->>Client: HTTP 429 Too Many Requests (Priority 7 Rate-Based Block)
        CF_WAF->>Kinesis: Ghi nhận vi phạm Rate Limit
        Kinesis->>Lambda: Cảnh báo IP bị chặn > 20 lần/phút
        Lambda->>CF_WAF: Tự động cập nhật WAF Blacklist IP Set (Khóa cứng IP 60 phút)
    else Request Hợp Lệ vượt qua Edge WAF
        CF_WAF->>NetFW: Đi qua Internet Gateway vào Firewall Subnet
        alt Tấn công 4: Gói tin mạng dị dạng / Khai thác lỗ hổng giao thức
            NetFW--xNetFW: Suricata IPS DROP gói tin & Bắn cảnh báo P1
        else Gói tin sạch hoàn toàn
            NetFW->>ALB: Chuyển tiếp tới Application Load Balancer
            ALB->>App: Định tuyến an toàn tới Microservice xử lý
            App-->>Client: HTTP 200 / 201 Thành công
        end
    end
```

---

## 4. Sơ Đồ Kiểm Soát Lưu Lượng Đi Ra Ngoài (`DIAG-SEC-04`: Egress Domain Whitelist Filtering)

Sơ đồ mô tả cơ chế ngăn chặn triệt để nguy cơ mã độc rò rỉ dữ liệu nhạy cảm ra ngoài Internet (Anti-Data Exfiltration):

```mermaid
flowchart LR
    subgraph PRIVATE_APPS ["CÁC CONTAINER TRONG PRIVATE SUBNET (KHÔNG CÓ PUBLIC IP)"]
        PAY_SVC["SVC-06 Payment Service"]
        SHIP_SVC["SVC-07 Shipping Service"]
        COMPROMISED["Container Giả định Bị Nhiễm Mã Độc\n(Cố gửi trộm dữ liệu PII ra ngoài)"]
    end

    PAY_SVC -->|"HTTPS: Gọi cổng thanh toán"| NAT["NAT Gateway"]
    SHIP_SVC -->|"HTTPS: Gọi hãng vận chuyển"| NAT
    COMPROMISED -->|"HTTPS/TCP: Kết nối tới C2 Server\nhoặc IP lạ 185.x.x.x"| NAT

    NAT --> NFW["AWS Network Firewall\n(Stateful Domain List Inspection\nKiểm tra TLS SNI & HTTP Host Header)"]

    subgraph WHITELIST_DOMAINS ["DANH SÁCH TÊN MIỀN ĐƯỢC PHÉP (EGRESS WHITELIST)"]
        W1["*.vnpay.vn & *.momo.vn & api.vietqr.io"]
        W2["*.ghn.vn & *.ghtk.vn"]
        W3["*.ap-southeast-1.amazonaws.com"]
        W4["*.zalo.me & *.esms.vn"]
    end

    NFW -->|"Tên miền nằm trong Whitelist"| EXT_OK(("ĐỐI TÁC HỢP LỆ\nCho phép kết nối"))
    NFW --x|"Tên miền lạ hoặc kết nối IP trực tiếp"| EXT_DROP(("DROP GÓI TIN NGAY LẬP TỨC\n+ Ghi S3 Security Audit Vault\n+ Bắn cảnh báo Slack Security"))

    classDef app fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef bad fill:#ffebee,stroke:#c62828,stroke-width:2px;
    classDef fw fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef ok fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class PAY_SVC,SHIP_SVC app;
    class COMPROMISED,EXT_DROP bad;
    class NFW,NAT fw;
    class W1,W2,W3,W4,EXT_OK ok;
```

---

## 5. Sơ Đồ Bảo Mật Dữ Liệu Nhạy Cảm: KMS, Secrets Manager, IRSA & Đường Ống Che Chắn PII (`DIAG-SEC-05`)

Sơ đồ mô tả kiến trúc bảo vệ dữ liệu cá nhân (PII: CCCD, SĐT, Tài khoản ngân hàng) ở cả 3 trạng thái: Khi truyền tải (In-Transit), Khi lưu trữ (At-Rest) và Khi ghi nhật ký/hiển thị (In-Use / Logging):

```mermaid
flowchart TB
    subgraph IDENTITY_ACCESS ["1. ĐỊNH DANH DỊCH VỤ KHÔNG DÙNG KHÓA CỨNG (ZERO HARDCODED SECRETS)"]
        EKS_POD["Microservice Pod (EKS / ECS)"] <-->|"OIDC Federation"| IRSA["AWS IAM Roles for Service Accounts (IRSA)\nCấp quyền tạm thời ngắn hạn theo đúng Pod"]
        EKS_POD <-->|"Lấy Connection String & API Keys"| SECRETS["AWS Secrets Manager\n(Tự động xoay vòng mật khẩu DB mỗi 30 ngày)"]
    end

    subgraph ENCRYPTION_AT_REST ["2. MÃ HÓA DỮ LIỆU TẠI CHỖ (ENCRYPTION AT REST - AWS KMS CMK)"]
        KMS_KEY["AWS KMS Customer Managed Key (CMK)\n(Tự động xoay vòng khóa hàng năm)"]
        KMS_KEY -->|"Field-Level Encryption"| PII_COLS["Cột Dữ Liệu Nhạy Cảm:\n- CCCD / CMND\n- Số Tài Khoản Ngân Hàng Seller\n- Địa chỉ chi tiết"]
        KMS_KEY -->|"Volume / Bucket Encryption"| STORAGE_ENC["Mã Hóa Toàn Bộ Lưu Trữ:\n- RDS SQL Server EBS Volumes\n- S3 Media & KYC Buckets\n- AWS Backup Vaults"]
    end

    subgraph PII_MASKING_PIPELINE ["3. ĐƯỜNG ỐNG CHE CHẮN PII TỰ ĐỘNG TRÊN LOG & GIAO DIỆN (NFR-PRV-02)"]
        RAW_LOG["Structured JSON Log từ Ứng dụng"] --> FLUENT["FluentBit / Logstash PII Sanitizer Filter\nRegex Masking trước khi rời Container:\n- SĐT: 0912***789\n- CCCD: ********1234\n- Token/Password: [REDACTED]"]
        FLUENT --> OS_LOG[("OpenSearch Log Cluster\n(100% Sạch PII thô)")]
    end

    EKS_POD --> KMS_KEY
    EKS_POD --> RAW_LOG

    classDef iam fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef kms fill:#f3e5f5,stroke:#6a1b9a,stroke-width:2px;
    classDef mask fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class EKS_POD,IRSA,SECRETS iam;
    class KMS_KEY,PII_COLS,STORAGE_ENC kms;
    class RAW_LOG,FLUENT,OS_LOG mask;
```

---

## 6. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Kiến trúc An ninh & Đám mây | Khởi tạo 3 sơ đồ mạng VPC 4 tầng, WAF v2, Network Firewall và Egress Filtering. |
| 1.1 | 12/11/2026 | Nhóm Kiến trúc An ninh & Đám mây | Mở rộng hoàn chỉnh lên 5 sơ đồ chuẩn hóa (`DIAG-SEC-01..05`), bổ sung sơ đồ Phòng thủ 5 lớp và KMS/IRSA/PII Masking Pipeline. |
