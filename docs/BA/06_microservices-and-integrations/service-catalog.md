# Danh Mục Dịch Vụ — Service Catalog & Specifications

| Tài liệu | Danh Mục Dịch Vụ Microservices & Đặc Tả Kỹ Thuật (Service Catalog) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-06-CAT` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phân hệ cha** | [docs/BA/06_microservices-and-integrations/README.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/README.md) |
| **Liên kết truy vết** | [domain-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/domain-model.md), [polyglot-persistence-and-db-architecture.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/polyglot-persistence-and-db-architecture.md), [non-functional-requirements.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/non-functional-requirements.md) (`NFR-PERF-*`, `NFR-SCAL-*`, `NFR-AVL-*`) |

---

## 1. Bảng Tổng Hợp Danh Mục Dịch Vụ (`SVC-00` Đến `SVC-09`)

Hệ thống ReMarket bao gồm 1 tầng điều phối biên (API Gateway/BFF) và 9 Microservices nghiệp vụ độc lập, ánh xạ chính xác 1-1 với 9 Bounded Contexts đã định nghĩa tại mục [05_domain-and-data/domain-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/domain-model.md):

| Mã Service | Tên Microservice | Bounded Context | CSDL Sở Hữu Độc Quyền | Tech Stack Runtime | Giao Thức Expose | Cam Kết SLA/SLO |
| :--- | :--- | :--- | :--- | :--- | :--- | :---: |
| `SVC-00` | **API Gateway & BFF Layer** | Biên hệ thống | Redis ElastiCache (Token/Cache) | Node.js / Go (Krakend/Kong) | HTTPS REST, WSS | 99.95% / p95 < 50ms |
| `SVC-01` | **Identity & Access Service** | `BC-01 Identity` | SQL Server (`IdentityDB`) | .NET 8 / C# | REST, gRPC Server | 99.95% / p95 < 80ms |
| `SVC-02` | **Catalog & Listing Service** | `BC-02 Catalog` | MongoDB (`CatalogDB`) | Node.js / TypeScript | REST, gRPC, Kafka Pub | 99.9% / p95 < 100ms |
| `SVC-03` | **Media Service** | `BC-03 Media` | Amazon S3 Buckets | Go / Python FastApi | REST | 99.9% / p95 < 60ms |
| `SVC-04` | **Search & Discovery Service** | `BC-04 Search` | OpenSearch Cluster | Java / Spring Boot | REST, Kafka Sub | 99.9% / p95 < 120ms |
| `SVC-05` | **Order & Reservation Service** | `BC-05 Order` | SQL Server (`OrderDB`) | .NET 8 / C# | REST, gRPC, Kafka Pub/Sub | **99.99% / p95 < 80ms** |
| `SVC-06` | **Payment & Escrow Service** | `BC-06 Payment` | SQL Server (`PaymentDB`) | .NET 8 / C# | REST, gRPC, Kafka Pub/Sub | **99.99% / p95 < 90ms** |
| `SVC-07` | **Shipping & Fulfillment Service** | `BC-07 Shipping` | MongoDB (`ShippingDB`) | Node.js / TypeScript | REST, Kafka Pub/Sub | 99.9% / p95 < 150ms |
| `SVC-08` | **Trust, Moderation & Support** | `BC-08 Trust` | SQL Server (`TrustSupportDB`) | .NET 8 / C# | REST, Kafka Sub | 99.9% / p95 < 100ms |
| `SVC-09` | **Notification Service** | `BC-09 Notif` | MongoDB (`NotificationDB`) | Node.js / TypeScript | REST, Kafka Sub, WSS | 99.5% / Async Event |

---

## 2. Đặc Tả Chi Tiết 10 Thành Phần Dịch Vụ

### `SVC-00`: API Gateway & Backend-For-Frontend (BFF Layer)
- **Năng lực nghiệp vụ:** Điểm tiếp nhận lưu lượng duy nhất cho 3 kênh Frontend (`Marketplace`, `Seller Center`, `Back-office`).
- **Trách nhiệm cốt lõi:**
  - Xác thực Token JWT, kiểm tra Blacklist Token trên Redis (`session:{token}`).
  - Phân quyền RBAC/ABAC (`ROLE-00` đến `ROLE-06`) trước khi chuyển tiếp yêu cầu.
  - Rate Limiting phân tầng bằng thuật toán Token Bucket chống brute-force và DDoS.
  - Phối hợp (Orchestration) và chuyển đổi dữ liệu (Data Transformation) tối ưu hóa payload cho Mobile/Web.
- **Auto-scaling Metric:** CPU Utilization > 70% hoặc Request Count > 2.000 req/s/pod.

---

### `SVC-01`: Identity & Access Service
- **Năng lực nghiệp vụ:** Quản lý vòng đời tài khoản người dùng, định danh người bán (KYC), sổ địa chỉ và vai trò nội bộ (`CAP-ACC-01..03`).
- **Trách nhiệm cốt lõi:**
  - Đăng ký, đăng nhập OTP/Mật khẩu, quản lý phiên làm việc.
  - Tiếp nhận và lưu trữ hồ sơ kích hoạt Người bán (`SCR-SEL-01`), quản lý tài khoản ngân hàng nhận tiền.
  - Cung cấp Endpoint gRPC nội bộ cho `OrderService` và `PaymentService` xác thực thông tin tài khoản người dùng với độ trễ siêu thấp (< 10ms).
- **Kho dữ liệu độc quyền:** `IdentityDB` (SQL Server) — Quản lý bảng `Users`, `SellerProfiles`, `UserAddresses`, `UserRoles`.
- **Auto-scaling Metric:** CPU > 75%, Min 2 pods, Max 10 pods.

---

### `SVC-02`: Catalog & Listing Service
- **Năng lực nghiệp vụ:** Quản lý vòng đời tin đăng đồ cũ, phân cấp danh mục, bảng khai báo tình trạng và khuyết điểm (`CAP-LST-01..05`).
- **Trách nhiệm cốt lõi:**
  - Soạn thảo, cập nhật và hiển thị chi tiết tin đăng đồ cũ (`SCR-MKT-03`, `SCR-SEL-04`).
  - Quản lý mảng thuộc tính động lồng nhau theo danh mục (`Dynamic Attributes`).
  - Kiểm tra và quét từ khóa cấm tức thì (`BR-LST-002`) trước khi xuất bản tin.
  - Phát sự kiện miền qua Transactional Outbox: `ListingCreatedEvent`, `ListingUpdatedEvent`, `ListingPublishedEvent`, `ListingSoldEvent`.
- **Kho dữ liệu độc quyền:** `CatalogDB` (MongoDB) — Collections `listings`, `categories`, `reviews`.
- **Auto-scaling Metric:** CPU > 70%, Memory > 80%, Min 3 pods, Max 15 pods.

---

### `SVC-03`: Media Service
- **Năng lực nghiệp vụ:** Cấp phép và kiểm soát tải tệp tin ảnh/video lên Cloud Object Storage (`CAP-MED-01..03`).
- **Trách nhiệm cốt lõi:**
  - Tạo **Presigned PUT URL** cho Amazon S3 với chữ ký số SigV4, giới hạn thời gian 5 phút (`FR-MED-01`).
  - Xác thực kích thước file (tối đa 10MB cho ảnh, 50MB cho video unboxing khiếu nại) và định dạng MIME Type hợp lệ (`image/jpeg`, `image/png`, `image/webp`, `video/mp4`).
  - Tích hợp dịch vụ quét ảnh nhạy cảm/bạo lực tự động bằng AWS Rekognition trước khi cho phép gắn ảnh vào tin đăng.
- **Lưu trữ liên kết:** Amazon S3 Buckets (`remarket-listing-media`, `remarket-dispute-evidences`).
- **Auto-scaling Metric:** Request Count > 500 req/s/pod, Min 2 pods.

---

### `SVC-04`: Search & Discovery Service (Read Model)
- **Năng lực nghiệp vụ:** Tìm kiếm toàn văn tiếng Việt, lọc đa diện và sắp xếp theo vị trí địa lý (`CAP-SRC-01..04`).
- **Trách nhiệm cốt lõi:**
  - Tiêu thụ (Consume) các sự kiện `ListingPublished`, `ListingReserved`, `ListingSold` từ Kafka để cập nhật Inverted Index trên OpenSearch.
  - Cung cấp API tìm kiếm hỗ trợ xử lý tiếng Việt có dấu/không dấu, từ đồng nghĩa và tự động gợi ý từ khóa (Autocomplete).
  - Tính toán bộ lọc đa diện (Faceted Aggregations) theo Danh mục, Khoảng giá, Tình trạng `ConditionGrade` và Khoảng cách bán kính địa lý (`geo_distance`).
- **Kho dữ liệu độc quyền:** `OpenSearch Cluster` (Index `listings-read-v1`).
- **Auto-scaling Metric:** CPU > 75%, Query Latency p95 > 150ms, Min 2 pods, Max 8 pods.

---

### `SVC-05`: Order & Reservation Service (Dịch Vụ Trọng Yếu)
- **Năng lực nghiệp vụ:** Quản lý giao dịch giữ hàng 15 phút, tính toán chi phí minh bạch, lưu trữ snapshot sản phẩm và điều phối Saga đặt hàng (`CAP-ORD-01..05`).
- **Trách nhiệm cốt lõi:**
  - Chốt chặn bảo vệ bất biến `INV-01`: Thực hiện giữ chỗ độc quyền 15 phút cho hàng đơn chiếc (`Stock = 1`) bằng Atomic State Transition trên SQL Server.
  - Khởi tạo và điều phối **Checkout Saga**: Kết hợp giữa `OrderService` và `PaymentService`.
  - Lưu trữ bản chụp bất biến `OrderItemSnapshots` (`BR-ORD-009`) làm chứng cứ đối soát pháp lý.
  - Tiến trình Background Sweeper tự động quét dọn và thu hồi các đơn giữ hàng quá hạn 15 phút.
- **Kho dữ liệu độc quyền:** `OrderDB` (SQL Server Multi-AZ) — Bảng `InventoryReservations`, `Orders`, `OrderItemSnapshots`, `OutboxMessages`.
- **Auto-scaling Metric:** CPU > 60%, IOPS > 70%, Min 4 pods, Max 20 pods (Ưu tiên sẵn sàng cao).

---

### `SVC-06`: Payment & Escrow Service (Dịch Vụ Trọng Yếu)
- **Năng lực nghiệp vụ:** Quản lý ý định thanh toán, tiếp nhận Webhook ngân hàng, quản lý quỹ tạm giữ Escrow và điều phối hoàn tiền (`CAP-PAY-01..05`).
- **Trách nhiệm cốt lõi:**
  - Tích hợp cổng thanh toán ngoài qua Adapter: Sinh URL thanh toán có chữ ký HMAC-SHA512.
  - Tiếp nhận IPN/Webhook từ ngân hàng: Kiểm tra chữ ký số, xác thực tính Idempotency (`INV-03`), cập nhật trạng thái `Captured` và khởi tạo quỹ tạm giữ `EscrowAccounts`.
  - Quản lý đóng băng quỹ Escrow khi có tranh chấp (`INV-04`) và giải ngân sau 72 giờ hoàn tất đơn (`BR-PAY-007`).
  - Thực hiện lệnh hoàn tiền (Refund) và đối soát doanh thu định kỳ (`T+1 Reconciliation`).
- **Kho dữ liệu độc quyền:** `PaymentDB` (SQL Server Multi-AZ) — Bảng `PaymentIntents`, `PaymentTransactions`, `EscrowAccounts`, `SettlementEntries`.
- **Auto-scaling Metric:** CPU > 60%, Min 4 pods, Max 20 pods.

---

### `SVC-07`: Shipping & Fulfillment Service
- **Năng lực nghiệp vụ:** Kết nối đối tác giao vận 3PL, sinh mã vận đơn, theo dõi hành trình và chuẩn hóa trạng thái vận chuyển (`CAP-SHP-01..04`).
- **Trách nhiệm cốt lõi:**
  - Tính cước vận chuyển tự động dựa trên trọng lượng kiện hàng và khoảng cách giao nhận (`BR-SHP-001`).
  - Giao tiếp với API của GHN và GHTK để tạo vận đơn khi đơn hàng đã chuyển `Confirmed`.
  - Tiếp nhận Webhook từ 3PL và chuẩn hóa về 6 trạng thái giao nhận chuẩn của ReMarket.
  - Cung cấp Carrier Webhook Simulator phục vụ môi trường kiểm thử.
- **Kho dữ liệu độc quyền:** `ShippingDB` (MongoDB) — Collection `shipments`.
- **Auto-scaling Metric:** CPU > 75%, Min 2 pods, Max 8 pods.

---

### `SVC-08`: Trust, Moderation & Support Service
- **Năng lực nghiệp vụ:** Kiểm duyệt nội dung tin đăng, quản lý khiếu nại tranh chấp và cung cấp bàn tra cứu 360° có kiểm toán an ninh (`CAP-TRU-01..04`, `CAP-SUP-01..03`).
- **Trách nhiệm cốt lõi:**
  - Quản lý hàng đợi tin đăng cần duyệt và các báo cáo vi phạm từ người dùng (`SCR-ADM-01`).
  - Quản lý hồ sơ khiếu nại tranh chấp (`DisputeCases`), đối chiếu bằng chứng song song và xử lý lệnh hoàn tiền cho Chuyên viên Hỗ trợ (`ROLE-04`).
  - Duy trì mô hình đọc CQRS `Order360View` phục vụ tra cứu đa miền siêu tốc.
  - Ghi nhận nhật ký kiểm toán bất biến `SupportPIIAuditLogs` mỗi khi có thao tác mở khóa PII (`FR-SUP-02`).
- **Kho dữ liệu độc quyền:** `TrustSupportDB` (SQL Server) — Bảng `ModerationTickets`, `DisputeCases`, `SupportPIIAuditLogs`.
- **Auto-scaling Metric:** CPU > 70%, Min 2 pods, Max 6 pods.

---

### `SVC-09`: Notification Service
- **Năng lực nghiệp vụ:** Điều phối và gửi thông báo đa kênh (In-app Bell, Push Notification, Email) (`CAP-NTF-01..03`).
- **Trách nhiệm cốt lõi:**
  - Lắng nghe toàn bộ các sự kiện domain từ Kafka (`OrderStateChanged`, `PaymentCaptured`, `ShipmentDelivered`, `DisputeOpened`).
  - Định tuyến thông báo theo cấu hình ưu tiên của người dùng (`NotificationPreferences`).
  - Đẩy thông báo thời gian thực về trình duyệt qua WebSocket (WSS) và gửi Email xác nhận qua Amazon SES.
- **Kho dữ liệu độc quyền:** `NotificationDB` (MongoDB) — Collections `notifications`, `notification_preferences`.
- **Auto-scaling Metric:** Kafka Consumer Lag > 1.000 messages, Min 2 pods, Max 10 pods.

