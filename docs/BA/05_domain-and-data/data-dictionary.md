# Từ Điển Dữ Liệu Toàn Hệ Thống — Data Dictionary

| Tài liệu | Từ Điển Dữ Liệu Chi Tiết (Comprehensive Data Dictionary) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-05-DIC` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phân hệ cha** | [docs/BA/05_domain-and-data/README.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/README.md) |
| **Liên kết truy vết** | [entity-relationship-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/entity-relationship-model.md), [domain-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/domain-model.md), [business-rules.md](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/business-rules.md) |

---

## 1. Tiêu Chuẩn Định Dạng & Quy Tắc Bảo Mật Dữ Liệu

### 1.1. Tiêu Chuẩn Kiểu Dữ Liệu (Data Standards)
- **Tiền tệ (Currency & Financial Amounts):** Luôn sử dụng kiểu `DECIMAL(18, 2)` trên SQL Server hoặc `BSON Decimal128` (hoặc đơn vị xu/integer) trên MongoDB. Tuyệt đối không dùng `FLOAT` hay `DOUBLE` để tránh sai số dấu phẩy động. Kèm theo trường mã tiền tệ `CurrencyCode CHAR(3) = 'VND'`.
- **Dấu thời gian (Timestamps):** Chuẩn hóa 100% thời gian lưu trữ theo múi giờ quốc tế **UTC**. Định dạng SQL Server: `DATETIME2(3)` (chính xác đến mili-giây); MongoDB: `ISODate`.
- **Văn bản Tiếng Việt:** Chuỗi văn bản tiếng Việt sử dụng `NVARCHAR` có Collation `Vietnamese_100_CI_AS` (hoặc UTF-8).
- **Mã định danh:**
  - `InternalId`: `BIGINT IDENTITY(1,1)` (Clustered PK nội bộ).
  - `PublicId`: `VARCHAR(26)` (ULID 26 ký tự) hoặc `VARCHAR(36)` (UUID).

### 1.2. Phân Loại Cấp Độ Bảo Mật Dữ Liệu (Security & PII Classification)
Mỗi trường dữ liệu được gán một trong 4 cấp độ bảo mật:
- `[PUB]` **Public:** Công khai cho tất cả người dùng (Tiêu đề tin, ảnh, giá bán, điểm uy tín).
- `[INT]` **Internal:** Chỉ lưu thông trong các microservice (Mã ID nội bộ, độ trễ, số lần retry).
- `[CON]` **Confidential:** Dữ liệu kinh doanh nhạy cảm (Số dư Escrow, doanh thu ròng, phí sàn).
- `[PII]` **Restricted PII:** Dữ liệu định danh cá nhân nhạy cảm (SĐT, Địa chỉ, Email, Số CCCD, Serial/IMEI, Số tài khoản). Bắt buộc mã hóa hoặc che giấu khi hiển thị theo vai trò (`BR-SEC-003`, `BR-SEC-004`).

---

## 2. Từ Điển Dữ Liệu Cụm SQL Server

### 2.1. Bảng `InventoryReservations` (CSDL `OrderDB`)
*Mục đích: Chốt chặn giao dịch giữ chỗ độc quyền 15 phút cho món hàng đồ cũ (`INV-01`, `BR-ORD-003`).*

| Tên Cột | Kiểu Dữ Liệu | Nullable | Khóa / Index | Mặc Định / Enums | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :--- | :---: | :--- |
| `InternalId` | `BIGINT` | No | PK (Clustered) | `IDENTITY(1,1)` | `[INT]` | Khóa chính vật lý tăng dần nội bộ. |
| `ReservationId` | `VARCHAR(26)` | No | UK | ULID string | `[PUB]` | Mã định danh giữ hàng công khai (`RES-...`). |
| `ListingId` | `VARCHAR(26)` | No | UK (Filtered) | — | `[PUB]` | Mã tin đăng bị khóa giữ. `INV-01`: Chỉ 1 dòng Active. |
| `BuyerId` | `VARCHAR(26)` | No | FK (Logic) | — | `[INT]` | Mã người mua được quyền giữ hàng độc quyền. |
| `ReservedAt` | `DATETIME2(3)`| No | — | `SYSUTCDATETIME()` | `[INT]` | Thời điểm bắt đầu tạo giữ chỗ. |
| `ExpiresAt` | `DATETIME2(3)` | No | Index | — | `[INT]` | Thời điểm hết hạn (`ReservedAt + 15 phút`). |
| `Status` | `VARCHAR(20)` | No | Index | `Active`, `Consumed`, `Expired`, `Cancelled` | `[INT]` | `Active`: đang giữ; `Consumed`: đã mua; `Expired`: hết 15p. |
| `RowVersion` | `ROWVERSION` | No | — | — | `[INT]` | Concurrency token chống ghi đè đồng thời. |

---

### 2.2. Bảng `Orders` (CSDL `OrderDB`)
*Mục đích: Quản lý thông tin thanh toán, giá trị và vòng đời đơn hàng.*

| Tên Cột | Kiểu Dữ Liệu | Nullable | Khóa / Index | Mặc Định / Enums | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :--- | :---: | :--- |
| `InternalId` | `BIGINT` | No | PK (Clustered) | `IDENTITY(1,1)` | `[INT]` | Khóa chính vật lý nội bộ. |
| `OrderId` | `VARCHAR(26)` | No | UK | ULID string | `[PUB]` | Mã đơn hàng (`ORD-...`). |
| `BuyerId` | `VARCHAR(26)` | No | Index | — | `[INT]` | Mã người mua (liên kết sang `IdentityDB`). |
| `SellerId` | `VARCHAR(26)` | No | Index | — | `[INT]` | Mã người bán (liên kết sang `IdentityDB`). |
| `ListingId` | `VARCHAR(26)` | No | Index | — | `[PUB]` | Mã sản phẩm đồ cũ được mua. |
| `ReservationId` | `VARCHAR(26)` | No | — | — | `[INT]` | Mã Reservation đã được tiêu thụ. |
| `ItemPrice` | `DECIMAL(18,2)`| No | — | — | `[PUB]` | Giá bán của sản phẩm tại thời điểm chốt mua. |
| `BuyerProtectionFee` | `DECIMAL(18,2)`| No | — | — | `[PUB]` | Phí bảo vệ người mua sàn thu (`BR-PAY-010`). |
| `ShippingFee` | `DECIMAL(18,2)`| No | — | — | `[PUB]` | Cước vận chuyển do đơn vị 3PL báo giá. |
| `DiscountAmount` | `DECIMAL(18,2)`| No | — | `0.00` | `[PUB]` | Số tiền voucher / khuyến mãi (nếu có). |
| `TotalAmount` | `DECIMAL(18,2)`| No | — | — | `[PUB]` | `ItemPrice + ProtectionFee + ShippingFee - Discount`. |
| `OrderStatus` | `VARCHAR(20)` | No | Index | `Created`, `Confirmed`, `Completed`, `Cancelled`, `Refunded` | `[PUB]` | Trạng thái đơn hàng chuẩn ([glossary.md](file:///d:/remarket-platform-system/docs/BA/00_project-context/glossary.md) §3). |
| `DeliveryMethod`| `VARCHAR(20)` | No | — | `Standard3PL`, `Pickup` | `[INT]` | Phương thức giao nhận. |
| `RowVersion` | `ROWVERSION` | No | — | — | `[INT]` | Kiểm soát đồng thời lạc quan (Optimistic Concurrency). |
| `CreatedAt` | `DATETIME2(3)`| No | Index | `SYSUTCDATETIME()` | `[INT]` | Thời điểm đặt đơn. |

---

### 2.3. Bảng `OrderItemSnapshots` (CSDL `OrderDB`)
*Mục đích: Bản chụp bất biến toàn bộ dữ liệu tin đăng lúc bấm mua (`INV-05`, `BR-ORD-009`).*

| Tên Cột | Kiểu Dữ Liệu | Nullable | Khóa / Index | Mặc Định / Enums | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :--- | :---: | :--- |
| `InternalId` | `BIGINT` | No | PK (Clustered) | `IDENTITY(1,1)` | `[INT]` | Khóa chính vật lý. |
| `OrderId` | `VARCHAR(26)` | No | UK | — | `[PUB]` | Mã đơn hàng sở hữu snapshot. |
| `TitleAtPurchase`| `NVARCHAR(255)`| No | — | — | `[PUB]` | Tiêu đề sản phẩm chính xác lúc mua. |
| `DescriptionAtPurchase`| `NVARCHAR(MAX)`| No | — | — | `[PUB]` | Mô tả chi tiết lúc mua. |
| `ConditionGrade`| `VARCHAR(20)` | No | — | `COND-*` | `[PUB]` | Cấp độ tình trạng lúc mua. |
| `DefectDisclosureJSON`| `NVARCHAR(MAX)`| No | — | — | `[PUB]` | Toàn bộ khuyết điểm người bán đã khai báo. |
| `SerialIMEIMasked`| `VARCHAR(50)` | Yes | — | — | `[PII]` | Số Serial/IMEI đã che 4 số cuối. |
| `PhotosSnapshotJSON`| `NVARCHAR(MAX)`| No | — | — | `[PUB]` | Mảng URL danh sách ảnh lúc mua (lưu trên S3). |
| `PriceAtPurchase`| `DECIMAL(18,2)`| No | — | — | `[PUB]` | Giá bán chốt lúc mua. |

---

### 2.4. Bảng `PaymentIntents` (CSDL `PaymentDB`)
*Mục đích: Quản lý giao dịch thanh toán cổng đối tác, chống trừ tiền 2 lần (`INV-03`).*

| Tên Cột | Kiểu Dữ Liệu | Nullable | Khóa / Index | Mặc Định / Enums | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :--- | :---: | :--- |
| `InternalId` | `BIGINT` | No | PK (Clustered) | `IDENTITY(1,1)` | `[INT]` | Khóa chính vật lý. |
| `PaymentIntentId`| `VARCHAR(26)` | No | UK | ULID string | `[PUB]` | Mã định danh ý định thanh toán (`PAY-...`). |
| `OrderId` | `VARCHAR(26)` | No | Index | — | `[PUB]` | Mã đơn hàng thanh toán. |
| `BuyerId` | `VARCHAR(26)` | No | Index | — | `[INT]` | Mã người thanh toán. |
| `Amount` | `DECIMAL(18,2)`| No | — | — | `[CON]` | Số tiền thanh toán thực tế. |
| `GatewayProvider`| `VARCHAR(20)` | No | — | `VNPAY`, `MOMO`, `CREDIT_CARD` | `[INT]` | Cổng thanh toán bên thứ ba. |
| `IdempotencyKey`| `VARCHAR(64)` | No | UK | Hash string | `[INT]` | Khóa chống trùng lặp (`INV-03`). |
| `PaymentStatus` | `VARCHAR(20)` | No | Index | `Pending`, `Authorized`, `Captured`, `Refunded`, `Failed` | `[PUB]` | Máy trạng thái thanh toán ([glossary.md](file:///d:/remarket-platform-system/docs/BA/00_project-context/glossary.md)). |
| `GatewayTransactionId`| `VARCHAR(100)`| Yes | Index | — | `[INT]` | Mã đối soát trả về từ ngân hàng. |

---

### 2.5. Bảng `EscrowAccounts` (CSDL `PaymentDB`)
*Mục đích: Quản lý quỹ tạm giữ tiền người bán đồ cũ (`BR-PAY-007`).*

| Tên Cột | Kiểu Dữ Liệu | Nullable | Khóa / Index | Mặc Định / Enums | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :--- | :---: | :--- |
| `InternalId` | `BIGINT` | No | PK (Clustered) | `IDENTITY(1,1)` | `[INT]` | Khóa chính vật lý. |
| `EscrowId` | `VARCHAR(26)` | No | UK | ULID string | `[INT]` | Mã tài khoản tạm giữ (`ESC-...`). |
| `OrderId` | `VARCHAR(26)` | No | UK | — | `[PUB]` | Mã đơn hàng liên kết. |
| `SellerId` | `VARCHAR(26)` | No | Index | — | `[INT]` | Mã người bán thụ hưởng. |
| `GrossAmount` | `DECIMAL(18,2)`| No | — | — | `[CON]` | Tổng tiền người mua đã trả. |
| `PlatformFeeAmount`| `DECIMAL(18,2)`| No | — | — | `[CON]` | Phí hoa hồng sàn thu từ người bán (3-5%). |
| `NetSellerAmount`| `DECIMAL(18,2)`| No | — | — | `[CON]` | Số tiền ròng Seller sẽ nhận được. |
| `EscrowStatus` | `VARCHAR(20)` | No | Index | `Holding`, `Released`, `FrozenByDispute`, `Refunded` | `[CON]` | Trạng thái giữ tiền. |
| `ReleaseHoldUntil`| `DATETIME2(3)`| Yes | Index | — | `[INT]` | Mốc giải phóng tiền (`Delivered + 72h`). |

---

### 2.6. Bảng `SupportPIIAuditLogs` (CSDL `TrustSupportDB`)
*Mục đích: Lưu vết kiểm toán bất biến mỗi khi nhân sự Support bấm mở khóa PII (`FR-SUP-02`).*

| Tên Cột | Kiểu Dữ Liệu | Nullable | Khóa / Index | Mặc Định / Enums | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :--- | :---: | :--- |
| `InternalId` | `BIGINT` | No | PK (Clustered) | `IDENTITY(1,1)` | `[INT]` | Khóa chính vật lý. |
| `AuditId` | `VARCHAR(26)` | No | UK | ULID string | `[INT]` | Mã bản ghi kiểm toán (`AUD-...`). |
| `StaffId` | `VARCHAR(26)` | No | Index | — | `[INT]` | Mã nhân viên Support (`ROLE-04`). |
| `TargetUserId` | `VARCHAR(26)` | No | Index | — | `[INT]` | Mã người dùng bị truy cập dữ liệu PII. |
| `TicketId` | `VARCHAR(50)` | No | Index | — | `[INT]` | Mã ticket hỗ trợ tương ứng. |
| `AccessReason` | `VARCHAR(100)` | No | — | — | `[INT]` | Lý do nghiệp vụ nhân viên nhập vào. |
| `FieldsAccessed`| `NVARCHAR(255)`| No | — | — | `[INT]` | Danh sách trường được xem (SĐT, Địa chỉ). |
| `IpAddress` | `VARCHAR(45)` | No | — | — | `[INT]` | Địa chỉ IP của máy nhân viên. |
| `AccessedAt` | `DATETIME2(3)` | No | Index | `SYSUTCDATETIME()` | `[INT]` | Thời điểm thực hiện mở khóa. |

---

## 3. Từ Điển Dữ Liệu Cụm MongoDB (Document Schemas)

### 3.1. Collection `listings` (CSDL `CatalogDB`)

| Tên Trường (Field Path) | Kiểu BSON | Bắt Buộc | Index Type | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :---: | :--- |
| `_id` | `String` | Yes | PK (Unique) | `[PUB]` | Mã ULID của tin đăng (`LST-...`). |
| `seller.seller_id` | `String` | Yes | Compound | `[PUB]` | Mã người bán đăng tin. |
| `seller.display_name` | `String` | Yes | — | `[PUB]` | Tên hiển thị người bán (lưu snapshot). |
| `seller.reputation_score` | `Double` | Yes | — | `[PUB]` | Điểm sao trung bình của người bán. |
| `title` | `String` | Yes | Text Index | `[PUB]` | Tiêu đề tin đăng đồ cũ. |
| `category_id` | `String` | Yes | Compound | `[PUB]` | Mã danh mục lá. |
| `category_path` | `String` | Yes | Prefix Regex | `[PUB]` | Đường dẫn phân cấp (`/electronics/phones`). |
| `pricing.listing_price` | `Decimal128 / Long` | Yes | Compound | `[PUB]` | Giá bán hiện tại (VND). |
| `condition.grade` | `String` | Yes | Compound | `[PUB]` | Cấp độ tình trạng (`COND-NEW`..`COND-PART`). |
| `condition.defects` | `Array of Object` | Yes | — | `[PUB]` | Mảng khai báo chi tiết khuyết điểm. |
| `condition.serial_masked`| `String` | No | — | `[PII]` | Serial/IMEI đã che 4 số cuối ngoài sàn. |
| `dynamic_attributes` | `Array of Object` | Yes | Wildcard / Compound | `[PUB]` | Mảng thuộc tính động `[{k, v, u}]`. |
| `media_gallery` | `Array of Object` | Yes | — | `[PUB]` | Danh sách URLs ảnh trên S3 (tối thiểu 3 ảnh). |
| `package_spec.weight_gram`| `Int` | Yes | — | `[INT]` | Trọng lượng kiện hàng tính phí ship. |
| `status` | `String` | Yes | Compound | `[PUB]` | `Draft`, `PendingModeration`, `Published`, `Reserved`, `Sold`. |
| `created_at` | `Date` | Yes | Compound | `[PUB]` | Thời điểm đăng tin. |

---

### 3.2. Collection `shipments` (CSDL `ShippingDB`)

| Tên Trường (Field Path) | Kiểu BSON | Bắt Buộc | Index Type | Cấp Độ | Mô Tả Nghiệp Vụ & Quy Tắc |
| :--- | :--- | :---: | :---: | :---: | :--- |
| `_id` | `String` | Yes | PK | `[PUB]` | Mã vận đơn hệ thống (`SHP-...`). |
| `order_id` | `String` | Yes | Unique | `[PUB]` | Mã đơn hàng tương ứng. |
| `carrier.code` | `String` | Yes | — | `[INT]` | Đơn vị 3PL (`GHN`, `GHTK`). |
| `carrier.tracking_number`| `String` | Yes | Unique | `[PUB]` | Mã vận đơn của bên chuyển phát. |
| `recipient.full_name` | `String` | Yes | — | `[PII]` | Tên người nhận (Ẩn trước khi `Confirmed`). |
| `recipient.phone` | `String` | Yes | — | `[PII]` | SĐT người nhận (Mặc định che ngoài giao diện). |
| `recipient.shipping_address`| `String`| Yes | — | `[PII]` | Địa chỉ giao hàng đầy đủ. |
| `status` | `String` | Yes | Compound | `[PUB]` | Trạng thái vận chuyển ([glossary.md](file:///d:/remarket-platform-system/docs/BA/00_project-context/glossary.md) §3). |
| `tracking_events` | `Array of Object` | Yes | — | `[PUB]` | Lịch sử các mốc quét barcode kiện hàng. |
| `created_at` | `Date` | Yes | Compound | `[INT]` | Thời điểm tạo vận đơn. |

