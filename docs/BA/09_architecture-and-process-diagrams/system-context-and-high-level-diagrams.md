# Sơ Đồ Bối Cảnh Hệ Thống & Kiến Trúc Tổng Thể (C4 High-Level Diagrams) – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Tài liệu | Tập hợp Sơ đồ Bối cảnh Hệ thống (C4 Level 1), Kiến trúc Container (C4 Level 2), Luồng North-South/East-West & Bounded Contexts |
| Mã tài liệu | `DOC-BA-09-SYS` |
| Phiên bản | 1.0 |
| Trạng thái | Hoàn thiện (Baseline Kiến trúc Hệ thống Mức cao) |
| Ngày cập nhật | 12/11/2026 |
| Phân hệ cha | [docs/BA/09_architecture-and-process-diagrams/README.md](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md) |
| Tài liệu liên quan | [system-context.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/system-context.md), [service-catalog.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/service-catalog.md), [domain-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/domain-model.md) |

---

## 1. Sơ Đồ Bối Cảnh Hệ Thống Tổng Thể (`DIAG-SYS-01`: C4 Level 1 – System Context Diagram)

Sơ đồ xác định ranh giới của nền tảng **ReMarket** ở trung tâm, tương tác với **6 nhóm Người dùng (Actors)** và **5 Hệ thống Đối tác Bên ngoài (External Systems)**:

```mermaid
flowchart TB
    subgraph ACTORS_EXTERNAL ["1. NHÓM NGƯỜI DÙNG THƯƠNG MẠI (EXTERNAL USERS)"]
        BUYER["Người Mua (Buyer - STK-09)\nTìm kiếm đồ cũ, đặt mua đơn chiếc,\nthanh toán ký quỹ, nhận hàng & khiếu nại"]
        SELLER["Người Bán Cá Nhân / Shop (Seller - STK-10/11)\nĐăng tin đồ cũ (Inventory = 1), tải ảnh,\nxác nhận đóng gói, nhận tiền giải ngân T+3"]
    end

    subgraph ACTORS_INTERNAL ["2. NHÓM VẬN HÀNH & QUẢN TRỊ NỘI BỘ (BACK-OFFICE STAFF)"]
        MOD["Chuyên Viên Kiểm Duyệt (Moderator - STK-12)\nDuyệt tin nghi vấn, xử lý báo cáo vi phạm,\nthẩm định kháng nghị & khóa tài khoản"]
        SUP["Nhân Viên CSKH & Trọng Tài (Support - STK-13)\nTra cứu Order 360°, xử lý ticket SLA,\nphán quyết tranh chấp & đóng băng ký quỹ"]
        FIN["Kế Toán Đối Soát (Finance Lead - STK-15)\nGiám sát sổ cái ký quỹ, đối soát 3 bên T+1,\nxử lý lệnh hoàn tiền & giải ngân ngoại lệ"]
        ADM["Quản Trị Viên & SRE (Admin / SRE - STK-07/18)\nCấu hình danh mục động, từ khóa cấm,\ngiám sát RED/USE, DLQ & diễn tập DR"]
    end

    subgraph REMARKET_BOUNDARY ["RANH GIỚI HỆ THỐNG REMARKET (REMARKET PLATFORM BOUNDARY)"]
        REMARKET["NỀN TẢNG THƯƠNG MẠI ĐỒ CŨ REMARKET\n(Distributed Microservices Platform trên AWS Cloud)\n- Bảo đảm 100% không bán trùng món hàng đơn chiếc (INV-01)\n- Thanh toán tạm giữ Ký quỹ Escrow & chống trừ tiền trùng (INV-02)\n- Tích hợp vận chuyển 3PL tự động & đối soát tài chính 3 bên"]
    end

    subgraph EXT_PARTNERS ["3. HỆ THỐNG ĐỐI TÁC & HẠ TẦNG BÊN NGOÀI (EXTERNAL SYSTEMS)"]
        EXT_PSP["Cổng Thanh Toán Điện Tử (EXT-02)\n(VNPAY / MoMo / VietQR)\nXử lý thu tiền, hoàn tiền & gửi IPN Webhook"]
        EXT_3PL["Đối Tác Vận Chuyển 3PL (EXT-01)\n(Giao Hàng Nhanh - GHN / GHTK)\nBáo giá ship, tạo vận đơn, thu gom & gửi Webhook POD"]
        EXT_S3["Amazon S3 & CloudFront CDN (EXT-03)\nNhận upload ảnh/video trực tiếp qua Presigned URL\n& phân phối nội dung toàn cầu"]
        EXT_MSG["Nhà Cung Cấp Email / SMS / ZNS (EXT-05)\n(Amazon SES / SNS / Zalo ZNS)\nGửi mã OTP xác thực & thông báo giao dịch"]
        EXT_IDP["Cơ Sở Hạ Tầng Đám Mây & Bảo Mật (EXT-03/04)\n(AWS KMS, Secrets Manager, AWS Backup, MongoDB Atlas)"]
    end

    %% Luồng tương tác từ Actors vào Hệ thống
    BUYER -->|"HTTPS / TLS 1.3 (Tìm kiếm, Checkout, Thanh toán)"| REMARKET
    SELLER -->|"HTTPS / TLS 1.3 (Đăng tin, In vận đơn, Xem doanh thu)"| REMARKET
    MOD -->|"HTTPS + MFA (Kiểm duyệt tin & tài khoản)"| REMARKET
    SUP -->|"HTTPS + MFA (Tra cứu Order 360° & Phán quyết Dispute)"| REMARKET
    FIN -->|"HTTPS + MFA (Đối soát tài chính 3 bên & Quyết toán)"| REMARKET
    ADM -->|"HTTPS + MFA (Quản trị danh mục, Cấu hình & Vận hành)"| REMARKET

    %% Luồng tương tác giữa Hệ thống và Đối tác ngoài
    REMARKET <-->|"HTTPS REST + HMAC-SHA512 Signature\n(Tạo Payment Intent, IPN Webhook, QueryDR, Refund)"| EXT_PSP
    REMARKET <-->|"HTTPS REST + Token/HMAC\n(Tính phí ship, Tạo vận đơn, Webhook Tracking & POD)"| EXT_3PL
    REMARKET <-->|"AWS SDK / Presigned PUT URLs\n(Upload ảnh/video bằng chứng & Quét ClamAV)"| EXT_S3
    REMARKET -->|"SMTP / HTTPS API\n(Gửi OTP, Email đơn hàng, Cảnh báo bảo mật)"| EXT_MSG
    REMARKET <-->|"PrivateLink / VPC Endpoint\n(Mã hóa KMS, Xoay vòng khóa, Sao lưu PITR)"| EXT_IDP

    classDef actor fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef staff fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef core fill:#fff3e0,stroke:#e65100,stroke-width:3px;
    classDef ext fill:#f3e5f5,stroke:#6a1b9a,stroke-width:1.5px;
    class BUYER,SELLER actor;
    class MOD,SUP,FIN,ADM staff;
    class REMARKET core;
    class EXT_PSP,EXT_3PL,EXT_S3,EXT_MSG,EXT_IDP ext;
```

### Bảng Đặc tả Giao diện Bối cảnh Hệ thống:

| Tác nhân / Đối tác | Mã Định Danh | Chiều Tương Tác | Giao Thức & Cơ Chế Bảo Mật | Dữ Liệu Trao Đổi Chính |
|---|---|---|---|---|
| **Người Mua (Buyer)** | `STK-09` | Hai chiều | HTTPS TLS 1.3, JWT Bearer, WAF Rate-Limit | Tìm kiếm, đặt giữ chỗ 15p, thanh toán, theo dõi vận đơn, mở khiếu nại. |
| **Người Bán (Seller)** | `STK-10`, `STK-11` | Hai chiều | HTTPS TLS 1.3, JWT + 2FA (TOTP/OTP) | Hồ sơ KYC, thông tin tin đăng, ảnh S3, xác nhận đóng gói, sao kê ký quỹ. |
| **Nhân sự Back-office** | `STK-12..16` | Hai chiều | HTTPS TLS 1.3, RBAC/ABAC + 2FA bắt buộc | Hàng đợi kiểm duyệt, hồ sơ `Order 360°`, phán quyết tranh chấp, bảng đối soát. |
| **Cổng Thanh toán PSP** | `EXT-02` | Hai chiều | REST HTTPS, IP Whitelist, HMAC-SHA512 | URL thanh toán, IPN Webhook kết quả, truy vấn `QueryDR`, lệnh hoàn tiền `Refund`. |
| **Đối tác Vận chuyển 3PL** | `EXT-01` | Hai chiều | REST HTTPS, Adapter Pattern, Webhook | Báo giá cước phí, mã vận đơn (`Waybill`), hành trình bưu tá, ảnh POD. |

---

## 2. Sơ Đồ Kiến Trúc Container Toàn Hệ Thống (`DIAG-SYS-02`: C4 Level 2 – Container Architecture)

Sơ đồ mô tả chi tiết cấu trúc bên trong của ReMarket gồm: **3 Phân hệ Giao diện (Frontend Portals)**, **Lớp Phòng thủ Biên (WAF/Firewall/ALB)**, **API Gateway (`SVC-00`)**, **9 Core Microservices (`SVC-01..09`)**, **Trục Sự kiện (`Apache Kafka / EventBridge`)** và **Hệ thống Cơ sở Dữ liệu Đa mô hình (Polyglot Persistence)**:

```mermaid
flowchart TB
    subgraph FE_PORTALS ["1. TẦNG GIAO DIỆN NGƯỜI DÙNG ĐA KÊNH (CLIENT PORTALS)"]
        MKT_WEB["Marketplace Web & Mobile App\n(Next.js SSR / React Native)\nSCR-MKT-01..12"]
        SEL_WEB["Seller Center Portal\n(React SPA / Next.js)\nSCR-SEL-01..07"]
        ADM_WEB["Back-Office Admin & Support Portal\n(React SPA nội bộ)\nSCR-ADM-01..08"]
    end

    subgraph EDGE_SECURITY ["2. TẦNG BIÊN & TƯỜNG LỬA BẢO VỆ (EDGE & PERIMETER SECURITY)"]
        CF_WAF["Amazon CloudFront CDN + AWS WAF v2\n(DDoS Shield, Geo-Block VN, OWASP Rules, Rate Limit)"]
        NET_FW["AWS Network Firewall (Firewall Subnets)\n(Stateful DPI Suricata & Egress Domain Whitelist)"]
        ALB["Application Load Balancer (ALB)\n+ Regional WAF v2 (TLS 1.3 Termination)"]
        CF_WAF --> NET_FW --> ALB
    end

    subgraph API_GW_LAYER ["3. CỔNG ĐIỀU PHỐI API TRUNG TÂM"]
        SVC00["SVC-00: API Gateway & BFF Router\n(Xác thực JWT, Phân quyền RBAC/ABAC, Gắn X-Trace-Id, Kiểm tra Idempotency-Key)"]
    end

    subgraph CORE_MICROSERVICES ["4. TẦNG 9 DỊCH VỤ MICROSERVICES CỐT LÕI (AWS EKS / ECS FARGATE)"]
        SVC01["SVC-01: Identity & Access\n(Auth, KYC, RBAC, 2FA)"]
        SVC02["SVC-02: Catalog & Listing\n(Vòng đời tin đơn chiếc, Danh mục động)"]
        SVC03["SVC-03: Media Storage\n(Presigned URL S3, ClamAV, Nén WebP)"]
        SVC04["SVC-04: Search & Discovery\n(Tìm kiếm tiếng Việt, Bộ lọc đa diện)"]
        SVC05["SVC-05: Order Processing\n(Checkout, Khóa 15p Two-Tier, Vòng đời đơn)"]
        SVC06["SVC-06: Payment & Escrow\n(Payment Intent, Sổ cái Escrow, Giải ngân, Đối soát)"]
        SVC07["SVC-07: Shipping & Logistics\n(3PL Adapter GHN/GHTK, Vận đơn, Tracking, POD)"]
        SVC08["SVC-08: Trust, Dispute & Support\n(Kiểm duyệt, Khiếu nại, Order 360°, Đánh giá)"]
        SVC09["SVC-09: Notification Dispatcher\n(WebSocket In-App, Email SES, SMS OTP, Quiet Hours)"]
    end

    subgraph EVENT_BUS ["5. TRỤC SỰ KIỆN BẤT ĐỒNG BỘ (EVENT STREAMING BACKBONE)"]
        KAFKA["Amazon MSK (Apache Kafka) & AWS EventBridge\n(Transactional Outbox CDC Debezium -> 28 Sự kiện EVT-01..28 + Retry Topics + SQS DLQ)"]
    end

    subgraph DATA_LAYER ["6. TẦNG DỮ LIỆU ĐA MÔ HÌNH CÁCH LY (ISOLATED POLYGLOT PERSISTENCE)"]
        subgraph SQL_CLUSTER ["AWS RDS for SQL Server Multi-AZ (Dual-Key ACID)"]
            DB_IDN[("IdentityDB\n(Users, Roles, KYC)")]
            DB_ORD[("OrderDB\n(Orders, Reservations, Outbox)")]
            DB_PAY[("PaymentDB\n(Payments, Escrow, Ledgers)")]
        end
        subgraph MONGO_CLUSTER ["MongoDB Atlas Replica Set (Document Schemas)"]
            DB_CAT[("CatalogDB\n(listings, categories, media)")]
            DB_SHP[("ShippingDB\n(shipments, tracking_events)")]
            DB_SUP[("SupportDB\n(disputes, tickets, order_360)")]
            DB_NTF[("NotificationDB\n(notifications, preferences)")]
        end
        subgraph CACHE_SEARCH ["Bộ Nhớ Đệm & Công Cụ Tìm Kiếm"]
            REDIS[("Amazon ElastiCache Redis\n(Khóa Giữ Chỗ NX EX 900 & Cache)")]
            OPENSEARCH[("Amazon OpenSearch\n(listings_index_v1)")]
            S3_BUCKET[("Amazon S3 Buckets\n(Media & WORM Audit Vault)")]
        end
    end

    %% Kết nối từ Portals qua Edge vào Gateway
    MKT_WEB & SEL_WEB & ADM_WEB --> CF_WAF
    ALB --> SVC00

    %% Kết nối từ Gateway tới 9 Microservices
    SVC00 --> SVC01 & SVC02 & SVC03 & SVC04 & SVC05 & SVC06 & SVC07 & SVC08 & SVC09

    %% Kết nối Microservices với Database sở hữu riêng (Database-per-Service)
    SVC01 --> DB_IDN
    SVC02 --> DB_CAT
    SVC03 --> DB_CAT & S3_BUCKET
    SVC04 --> OPENSEARCH & REDIS
    SVC05 --> DB_ORD & REDIS
    SVC06 --> DB_PAY
    SVC07 --> DB_SHP
    SVC08 --> DB_SUP
    SVC09 --> DB_NTF

    %% Kết nối qua Event Bus
    SVC01 & SVC02 & SVC03 & SVC05 & SVC06 & SVC07 & SVC08 <--> KAFKA
    KAFKA --> SVC04 & SVC08 & SVC09

    classDef portal fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef sec fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    classDef gw fill:#fff9c4,stroke:#f57f17,stroke-width:2px;
    classDef svc fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef bus fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef db fill:#f3e5f5,stroke:#6a1b9a,stroke-width:1.5px;
    class MKT_WEB,SEL_WEB,ADM_WEB portal;
    class CF_WAF,NET_FW,ALB sec;
    class SVC00 gw;
    class SVC01,SVC02,SVC03,SVC04,SVC05,SVC06,SVC07,SVC08,SVC09 svc;
    class KAFKA bus;
    class DB_IDN,DB_ORD,DB_PAY,DB_CAT,DB_SHP,DB_SUP,DB_NTF,REDIS,OPENSEARCH,S3_BUCKET db;
```

---

## 3. Sơ Đồ Phân Luồng Giao Tiếp North-South & East-West (`DIAG-SYS-03`)

Sơ đồ làm rõ quy tắc giao tiếp hai tầng:
- **Lưu lượng Bắc-Nam (North-South Traffic):** Từ Client bên ngoài đi vào hệ thống qua HTTPS REST JSON, được bảo vệ bởi WAF, Network Firewall và API Gateway.
- **Lưu lượng Đông-Tây (East-West Traffic):** Giao tiếp nội bộ giữa các Microservices chỉ sử dụng **gRPC Protobuf** (cho truy vấn đồng bộ cần độ trễ thấp < 15ms) và **Kafka CloudEvents** (cho toàn bộ các thay đổi trạng thái nghiệp vụ bất đồng bộ):

```mermaid
flowchart LR
    subgraph NORTH_SOUTH ["1. LUỒNG BẮC - NAM (NORTH-SOUTH: PUBLIC EDGE TRAFFIC)"]
        direction TB
        CLI["Web / Mobile Clients"] -->|"HTTPS TLS 1.3 (REST JSON)\n+ Header: Idempotency-Key"| WAF["CloudFront + AWS WAF v2\n+ AWS Network Firewall"]
        WAF -->|"HTTPS 443"| ALB["Application Load Balancer"]
        ALB -->|"HTTP 8080"| GW["SVC-00: API Gateway\n(Verify JWT, Inject X-Trace-Id)"]
    end

    subgraph EAST_WEST_SYNC ["2. LUỒNG ĐÔNG - TÂY ĐỒNG BỘ (EAST-WEST SYNC: gRPC PROTOBUF)"]
        direction TB
        ORD["SVC-05: Order Service"]
        CAT["SVC-02: Catalog Service"]
        SHP["SVC-07: Shipping Service"]
        PAY["SVC-06: Payment Service"]

        ORD -->|"gRPC: VerifyListingAvailability()\n(Timeout: 300ms)"| CAT
        ORD -->|"gRPC: CalculateShippingQuote()\n(Timeout: 500ms)"| SHP
        ORD -->|"gRPC: CreatePaymentIntent()\n(Timeout: 500ms)"| PAY
    end

    subgraph EAST_WEST_ASYNC ["3. LUỒNG ĐÔNG - TÂY BẤT ĐỒNG BỘ (EAST-WEST ASYNC: KAFKA / EVENTBRIDGE)"]
        direction TB
        OUTBOX["Transactional Outbox Tables\n(OrderDB, PaymentDB, CatalogDB)"] -->|"Debezium CDC\n(< 500ms)"| BROKER["Apache Kafka / EventBridge\n(28 Domain Events EVT-01..28)"]
        BROKER -->|"Async Consumer\n(Inbox Idempotent)"| SRC["SVC-04: Search Service\n(Cập nhật OpenSearch)"]
        BROKER -->|"Async Consumer\n(Inbox Idempotent)"| TRU["SVC-08: Trust & Support\n(Cập nhật Order360View)"]
        BROKER -->|"Async Consumer\n(Inbox Idempotent)"| NTF["SVC-09: Notification\n(Đẩy WebSocket/Email/SMS)"]
    end

    GW -->|"Định tuyến REST/gRPC"| ORD
    ORD --> OUTBOX

    classDef ns fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef sync fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef async fill:#fff3e0,stroke:#ef6c00,stroke-width:1.5px;
    class CLI,WAF,ALB,GW ns;
    class ORD,CAT,SHP,PAY sync;
    class OUTBOX,BROKER,SRC,TRU,NTF async;
```

---

## 4. Sơ Đồ Ánh Xạ 9 Bounded Contexts Sang 9 Microservices (`DIAG-SYS-04`: DDD Context Map)

Sơ đồ thể hiện mối quan hệ chiến lược giữa **9 Bounded Contexts (`BC-01..09`)** theo thiết kế hướng miền (Domain-Driven Design) và các mẫu tích hợp (`Customer-Supplier`, `Conformist`, `Anti-Corruption Layer - ACL`, `Published Language`):

```mermaid
flowchart TB
    subgraph CORE_DOMAIN ["MIỀN NGHIỆP VỤ CỐT LÕI (CORE DOMAINS - LỢI THẾ CẠNH TRANH)"]
        BC05["BC-05: Order & Reservation Context\n(SVC-05 Order Processing)\nAggregate: Order, OrderReservation\nBảo vệ INV-01 (Khóa 15p)"]
        BC06["BC-06: Payment & Escrow Context\n(SVC-06 Payment & Escrow)\nAggregate: PaymentIntent, EscrowAccount\nBảo vệ INV-02 & INV-04"]
        BC08["BC-08: Trust, Dispute & Support Context\n(SVC-08 Trust & Support)\nAggregate: DisputeCase, ModerationTask, Order360View\nBảo vệ INV-04"]
    end

    subgraph SUPPORTING_DOMAIN ["MIỀN NGHIỆP VỤ HỖ TRỢ (SUPPORTING DOMAINS)"]
        BC02["BC-02: Catalog & Listing Context\n(SVC-02 Catalog Service)\nAggregate: Listing (Inventory = 1), Category"]
        BC04["BC-04: Search & Discovery Context\n(SVC-04 Search Service)\nRead Model: SearchListingDocument"]
        BC07["BC-07: Shipping & Fulfillment Context\n(SVC-07 Shipping Service)\nAggregate: Shipment, Waybill"]
    end

    subgraph GENERIC_DOMAIN ["MIỀN NGHIỆP VỤ DÙNG CHUNG (GENERIC SUBDOMAINS)"]
        BC01["BC-01: Identity & Access Context\n(SVC-01 Identity Service)\nAggregate: User, SellerProfile, Role"]
        BC03["BC-03: Media Asset Context\n(SVC-03 Media Service)\nAggregate: MediaAsset"]
        BC09["BC-09: Notification Context\n(SVC-09 Notification Service)\nAggregate: NotificationDelivery, UserPreference"]
    end

    subgraph EXTERNAL_ACL ["LỚP CHỐNG HỦY HOẠI (ANTI-CORRUPTION LAYER - ACL)"]
        ACL_PSP["ACL Payment Adapters\n(VNPAY / MoMo / VietQR Translators)"]
        ACL_3PL["ACL Carrier Adapters\n(GHN / GHTK Canonical Status Mappers)"]
    end

    %% Quan hệ DDD giữa các Bounded Contexts
    BC01 -->|"Upstream / Published Language (JWT Claims & User Events)"| BC02 & BC05 & BC08
    BC03 -->|"Customer-Supplier (EVT-07 MediaUploadedEvent)"| BC02
    BC02 -->|"Published Language (EVT-03..06 Listing Events)"| BC04
    BC02 <-->|"Customer-Supplier (Reserve / Release / MarkSold)"| BC05
    BC05 <-->|"Partnership Saga (Order <-> Payment Escrow)"| BC06
    BC05 <-->|"Customer-Supplier (Order Paid -> Create Shipment)"| BC07
    BC08 -->|"Upstream Event (EVT-22 DisputeOpened -> Freeze Escrow)"| BC06
    BC05 & BC06 & BC07 -->|"Published Language Events -> CQRS Projection"| BC08
    BC05 & BC06 & BC07 & BC08 -->|"Published Language Events"| BC09

    BC06 -->|"Anti-Corruption Layer (ACL)"| ACL_PSP
    BC07 -->|"Anti-Corruption Layer (ACL)"| ACL_3PL

    classDef core fill:#ffebee,stroke:#c62828,stroke-width:2px;
    classDef supp fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef gen fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef acl fill:#fff3e0,stroke:#ef6c00,stroke-width:2px,stroke-dasharray: 5 5;
    class BC05,BC06,BC08 core;
    class BC02,BC04,BC07 supp;
    class BC01,BC03,BC09 gen;
    class ACL_PSP,ACL_3PL acl;
```

---

## 5. Sơ Đồ Kiến Trúc Ports & Adapters (Hexagonal / ACL) Tích Hợp Đối Tác (`DIAG-SYS-05`)

Sơ đồ minh họa nguyên tắc kiến trúc **`DEC-DIR-05`**: Tuyệt đối không tạo microservice riêng cho từng hãng vận chuyển hay cổng thanh toán, mà cô lập bên trong `SVC-06` và `SVC-07` qua **Ports & Adapters (Hexagonal Architecture)** và **Anti-Corruption Layer (ACL)**:

```mermaid
flowchart LR
    subgraph ORDER_CORE ["LÕI NGHIỆP VỤ REMARKET"]
        ORD_SVC["SVC-05: Order Service"]
    end

    subgraph SHIPPING_HEXAGON ["SVC-07: SHIPPING SERVICE (HEXAGONAL ARCHITECTURE)"]
        direction TB
        IN_PORT["Inbound Port:\nIShippingApplicationService\n(Quote, CreateWaybill, Cancel)"]
        DOMAIN["Shipping Domain Core:\n- Shipment Aggregate\n- 6 Trạng thái Chuẩn hóa (Canonical Statuses)\n- Carrier Routing Policy"]
        OUT_PORT["Outbound Port (Interface):\nICarrierProviderPort\n+ IWebhookNormalizer (ACL)"]

        ADP_GHN["Adapter 1: GHNCarrierAdapter\n(Ánh xạ mã trạng thái GHN -> Canonical)"]
        ADP_GHTK["Adapter 2: GHTKCarrierAdapter\n(Ánh xạ mã trạng thái GHTK -> Canonical)"]
        ADP_SIM["Adapter 3: SandboxSimulatorAdapter\n(Giả lập kiểm thử tự động US-OPS-11)"]

        IN_PORT --> DOMAIN --> OUT_PORT
        OUT_PORT --> ADP_GHN & ADP_GHTK & ADP_SIM
    end

    subgraph EXT_CARRIERS ["ĐỐI TÁC 3PL BÊN NGOÀI"]
        GHN_API["GHN REST API & Webhook"]
        GHTK_API["GHTK REST API & Webhook"]
    end

    ORD_SVC -->|"gRPC / Kafka Event"| IN_PORT
    ADP_GHN <-->|"HTTPS + Circuit Breaker"| GHN_API
    ADP_GHTK <-->|"HTTPS + Circuit Breaker"| GHTK_API

    classDef core fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef port fill:#fff9c4,stroke:#f57f17,stroke-width:1.5px;
    classDef adp fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef ext fill:#f3e5f5,stroke:#6a1b9a,stroke-width:1.5px;
    class DOMAIN,ORD_SVC core;
    class IN_PORT,OUT_PORT port;
    class ADP_GHN,ADP_GHTK,ADP_SIM adp;
    class GHN_API,GHTK_API ext;
```

---

## 6. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Kiến trúc Hệ thống | Khởi tạo hoàn chỉnh 5 sơ đồ bối cảnh và kiến trúc mức cao (`DIAG-SYS-01..05`). |
