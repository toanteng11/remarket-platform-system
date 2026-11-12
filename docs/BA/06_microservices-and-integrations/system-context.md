# Bối Cảnh Hệ Thống & Sơ Đồ C4 — System Context & Container Architecture

| Tài liệu | Đặc tả Bối Cảnh Hệ Thống & Sơ đồ C4 Level 1 - Level 2 (System Context & Containers) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-06-CTX` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phân hệ cha** | [docs/BA/06_microservices-and-integrations/README.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/README.md) |
| **Liên kết truy vết** | [scope-and-boundaries.md](file:///d:/remarket-platform-system/docs/BA/00_project-context/scope-and-boundaries.md), [user-roles-and-permissions.md](file:///d:/remarket-platform-system/docs/BA/01_stakeholders-and-users/user-roles-and-permissions.md), [domain-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/domain-model.md), [open-questions-and-decisions.md](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/open-questions-and-decisions.md) (`DEC-DIR-01..06`) |

---

## 1. Mô Hình C4 Level 1 — Sơ Đồ Bối Cảnh Hệ Thống (System Context Diagram)

Sơ đồ C4 Level 1 trực quan hóa ranh giới tương tác ngoài của nền tảng **ReMarket Platform** với 7 nhóm tác nhân người dùng và 4 nhóm hệ thống tích hợp đối tác bên ngoài:

```mermaid
flowchart TD
    classDef platform fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    classDef users fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef external fill:#fff3e0,stroke:#f57c00,stroke-width:2px;

    subgraph USERS["Tác Nhân Người Dùng (User Roles)"]
        U_GUEST["ROLE-00: Khách Vãng Lai\n(Lướt xem tin, tìm kiếm đồ cũ)"]:::users
        U_BUYER["ROLE-01: Người Mua Hàng\n(Giữ hàng 15p, Thanh toán, Khiếu nại)"]:::users
        U_SELLER["ROLE-02: Người Bán Đồ Cũ\n(Đăng tin, Tạo vận đơn, Nhận tiền bán)"]:::users
        U_STAFF["ROLE-03..06: Nhân Sự Vận Hành\n(Mod, Support 360°, Admin, Ops)"]:::users
    end

    PLATFORM["NỀN TẢNG THƯƠNG MẠI ĐIỆN TỬ REMARKET\n(Hệ thống C2C đồ cũ phân tán trên AWS)"]:::platform

    subgraph EXT_SYSTEMS["Hệ Thống Tích Hợp Bên Ngoài (External Gateways)"]
        EXT_PAY["Cổng Thanh Toán Đối Tác\n(VNPAY, MoMo - Thanh toán & Hoàn tiền)"]:::external
        EXT_3PL["Đối Tác Vận Chuyển 3PL\n(GHN, GHTK - Vận đơn & Webhook Tracking)"]:::external
        EXT_AWS["Dịch Vụ Hạ Tầng AWS Cloud\n(Amazon S3 Media, CloudFront CDN, SES)"]:::external
        EXT_PUSH["Dịch Vụ Thông Báo Đẩy\n(Google Firebase Cloud Messaging - FCM)"]:::external
    end

    %% Tương tác người dùng
    U_GUEST -->|Duyệt danh mục & Tìm kiếm| PLATFORM
    U_BUYER -->|Đặt hàng, Giữ chỗ 15p, Thanh toán| PLATFORM
    U_SELLER -->|Đăng tin kèm ảnh S3, Quản lý đơn bán| PLATFORM
    U_STAFF -->|Kiểm duyệt tin, Phân xử khiếu nại, Giám sát| PLATFORM

    %% Tương tác hệ thống ngoài
    PLATFORM <-->|Chuyển hướng thanh toán, IPN Webhook, Refund| EXT_PAY
    PLATFORM <-->|Tạo vận đơn, Nhận Webhook trạng thái giao nhận| EXT_3PL
    PLATFORM <-->|Cấp Presigned URL, Lưu trữ ảnh/video, Gửi Email| EXT_AWS
    PLATFORM -->|Bắn push notification di động| EXT_PUSH
```

---

## 2. Mô Hình C4 Level 2 — Sơ Đồ Khối Ứng Dụng (Container Architecture Diagram)

Sơ đồ C4 Level 2 phân rã hệ thống thành các khối ứng dụng (Containers), phân tách rạch ròi giữa Tầng Giao diện người dùng, Tầng Điều phối biên (Ingress/BFF), Tầng 9 Microservices nghiệp vụ và Tầng Cơ sở dữ liệu phân tán:

```mermaid
flowchart TB
    classDef ui fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef edge fill:#fff8e1,stroke:#f57f17,stroke-width:1.5px;
    classDef svc fill:#e1f5fe,stroke:#0288d1,stroke-width:1.5px;
    classDef data fill:#fce4ec,stroke:#c2185b,stroke-width:1.5px;

    subgraph FE_LAYER["1. Tầng Giao Diện (3 Kênh Frontend Portals - DEC-DIR-06)"]
        UI_MKT["Marketplace Web / Mobile App\n(Next.js / Flutter - ROLE-00, 01)"]:::ui
        UI_SEL["Seller Center Portal\n(Next.js Web - ROLE-02)"]:::ui
        UI_ADM["Back-office Portal\n(React Admin - ROLE-03..06)"]:::ui
    end

    subgraph EDGE_LAYER["2. Tầng Điều Phối Biên (Edge & API Gateway)"]
        CF_WAF["CloudFront CDN + AWS WAF\n(DDoS Shield, SSL/TLS, Caching)"]:::edge
        ALB["Application Load Balancer (ALB)\n(Multi-AZ Routing, Path-based)"]:::edge
        GW_BFF["SVC-00: API Gateway & BFF Layer\n(Auth JWT, Rate Limiting, Request Routing)"]:::edge
    end

    subgraph SVC_LAYER["3. Tầng Dịch Vụ Cốt Lõi (9 Core Microservices)"]
        SVC_IDN["SVC-01: IdentityService\n(Users, Roles, KYC)"]:::svc
        SVC_CAT["SVC-02: CatalogService\n(Listings, Categories)"]:::svc
        SVC_MED["SVC-03: MediaService\n(Presigned S3 Upload)"]:::svc
        SVC_SRC["SVC-04: SearchService\n(Vietnamese Search Read)"]:::svc
        SVC_ORD["SVC-05: OrderService\n(15m Hold, Saga Coordinator)"]:::svc
        SVC_PAY["SVC-06: PaymentService\n(Escrow Holding, Settlement)"]:::svc
        SVC_SHP["SVC-07: ShippingService\n(3PL Adapters, Tracking)"]:::svc
        SVC_TRU["SVC-08: TrustSupportService\n(Moderation, Dispute, PII Audit)"]:::svc
        SVC_NTF["SVC-09: NotificationService\n(In-App, Push, Email)"]:::svc
    end

    subgraph DATA_LAYER["4. Tầng Dữ Liệu & Hàng Đợi (Polyglot Persistence)"]
        DB_SQL_ORD[("SQL Server: OrderDB\n(Reservations, Orders, Outbox)")]:::data
        DB_SQL_PAY[("SQL Server: PaymentDB\n(PaymentIntents, Escrow)")]:::data
        DB_SQL_IDN[("SQL Server: IdentityDB\nTrustSupportDB")]:::data
        DB_MNG_CAT[("MongoDB: CatalogDB\n(listings, categories)")]:::data
        DB_MNG_SHP[("MongoDB: ShippingDB\nNotificationDB")]:::data
        CACHE_REDIS[("Redis ElastiCache\n(Locks, Sessions, Cache)")]:::data
        SEARCH_OS[("OpenSearch Cluster\n(Listing Inverted Index)")]:::data
        KAFKA_BUS[("Apache Kafka / MSK\n(Domain Events Message Bus)")]:::data
        STORAGE_S3[("Amazon S3 Buckets\n(Listing Photos & Evidence)")]:::data
    end

    %% Edge Connections
    UI_MKT --> CF_WAF
    UI_SEL --> CF_WAF
    UI_ADM --> CF_WAF
    CF_WAF --> ALB --> GW_BFF

    %% Gateway to Microservices
    GW_BFF -->|HTTP/REST / gRPC| SVC_IDN
    GW_BFF -->|HTTP/REST / gRPC| SVC_CAT
    GW_BFF -->|HTTP/REST| SVC_MED
    GW_BFF -->|HTTP/REST| SVC_SRC
    GW_BFF -->|HTTP/REST / gRPC| SVC_ORD
    GW_BFF -->|HTTP/REST / gRPC| SVC_PAY
    GW_BFF -->|HTTP/REST| SVC_SHP
    GW_BFF -->|HTTP/REST| SVC_TRU
    GW_BFF -->|HTTP/REST| SVC_NTF

    %% Microservices to Storage
    SVC_IDN --> DB_SQL_IDN
    SVC_ORD --> DB_SQL_ORD
    SVC_PAY --> DB_SQL_PAY
    SVC_TRU --> DB_SQL_IDN
    SVC_CAT --> DB_MNG_CAT
    SVC_SHP --> DB_MNG_SHP
    SVC_NTF --> DB_MNG_SHP
    SVC_SRC --> SEARCH_OS
    SVC_MED --> STORAGE_S3
    SVC_ORD --> CACHE_REDIS
    SVC_CAT --> CACHE_REDIS

    %% Event Bus Integration
    SVC_ORD -.->|Publish Outbox Events| KAFKA_BUS
    SVC_PAY -.->|Publish Outbox Events| KAFKA_BUS
    SVC_CAT -.->|Publish Outbox Events| KAFKA_BUS
    SVC_SHP -.->|Publish Events| KAFKA_BUS
    KAFKA_BUS -.->|Consume Events| SVC_SRC
    KAFKA_BUS -.->|Consume Events| SVC_NTF
    KAFKA_BUS -.->|Consume Events| SVC_TRU
```

---

## 3. Phân Luồng Giao Tiếp Mạng (Traffic Topology)

Hệ thống phân tách lưu lượng mạng thành 2 hướng giao tiếp độc lập nhằm tối đa hóa bảo mật và hiệu năng:

### 3.1. Luồng Giao Tiếp Bắc - Nam (North-South Traffic)
- **Định nghĩa:** Luồng lưu lượng từ Internet (Client trình duyệt, Mobile App, Webhook của ngân hàng/3PL) đi vào hệ thống qua biên bảo vệ.
- **Tiêu chuẩn kiểm soát tại biên:**
  1. **HTTPS / TLS 1.3:** Mã hóa 100% đường truyền tại CloudFront CDN và kết thúc TLS tại Application Load Balancer (ALB).
  2. **AWS WAF (Web Application Firewall):** Chặn các cuộc tấn công phổ biến (SQL Injection, Cross-Site Scripting XSS, HTTP Flood, Bad Bot).
  3. **Xác thực & Ủy quyền (Authentication & Authorization):** `SVC-00 API Gateway` giải mã Token JWT, kiểm tra chữ ký số, trích xuất Claims vai trò (`roles`) và định tuyến vào các Microservices tương ứng.
  4. **Kiểm soát Tần suất (Rate Limiting):** Áp dụng thuật toán Token Bucket trên Redis để ngăn chặn tấn công từ chối dịch vụ (DDoS) và bot gom hàng.

### 3.2. Luồng Giao Tiếp Đông - Tây (East-West Traffic)
- **Định nghĩa:** Luồng lưu lượng nội bộ giữa các microservices bên trong mạng riêng (Private Subnet).
- **Tiêu chuẩn giao tiếp nội bộ:**
  1. **Truy vấn Đồng bộ Tốc độ Cao (Internal Synchronous Queries):** Sử dụng **gRPC trên nền tảng HTTP/2 với Protobuf Binary Serialization**.
     - Giảm 60% kích thước gói tin so với JSON REST.
     - Áp dụng cho các truy vấn kiểm tra nhanh có ràng buộc thời gian (ví dụ: `OrderService` gọi `CatalogService` để lấy thông tin tạo snapshot, hoặc gọi `IdentityService` để xác thực hồ sơ).
  2. **Giao Tiếp Hướng Sự Kiện Bất Đồng Bộ (Asynchronous Event-Driven Communication):** Sử dụng **Apache Kafka / AWS MSK**.
     - Áp dụng mẫu **Transactional Outbox Pattern** để đảm bảo tính nhất quán (Atomicity) giữa việc ghi CSDL và bắn sự kiện (`At-least-once Delivery`).
     - Tách rời hoàn toàn sự phụ thuộc (Decoupling) giữa dịch vụ phát sinh sự kiện và các dịch vụ lắng nghe (ví dụ: `PaymentCapturedEvent` được phát ra, `OrderService`, `CatalogService` và `NotificationService` đồng thời tiêu thụ độc lập mà không làm chậm luồng thanh toán).

