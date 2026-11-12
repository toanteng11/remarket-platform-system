# Chiến Lược Đánh Index & Tối Ưu Truy Vấn — Indexing & Query Optimization

| Tài liệu | Chiến Lược Đánh Index & Tối Ưu Hóa Truy Vấn CSDL (Indexing & Optimization) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-05-IDX` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phân hệ cha** | [docs/BA/05_domain-and-data/README.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/README.md) |
| **Liên kết truy vết** | [entity-relationship-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/entity-relationship-model.md), [non-functional-requirements.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/non-functional-requirements.md) (`NFR-PERF-01..02`) |

---

## 1. Chiến Lược Đánh Index Chuyên Sâu Trên SQL Server

### 1.1. Tối Ưu Hóa Clustered Index & Chống Phân Mảnh Trang (Page Splits)
- **Vấn đề kỹ thuật:** Khi dùng UUIDv4 ngẫu nhiên làm Clustered Primary Key trên SQL Server, các bản ghi mới sẽ được chèn ngẫu nhiên vào giữa các Data Pages (8KB). Khi một trang đầy (Full Page), SQL Server buộc phải thực hiện **Page Split** (chuyển 50% dữ liệu sang trang mới), gây suy giảm nghiêm trọng thông lượng ghi IOPS và phân mảnh chỉ mục lên tới 90%+.
- **Giải pháp chuẩn hóa của ReMarket:**
  - Khóa Clustered Primary Key luôn là **`InternalId BIGINT IDENTITY(1,1)`** đơn điệu tăng dần. Dữ liệu mới luôn được chèn tuần tự vào cuối trang dữ liệu cuối cùng, đạt tỷ lệ lấp đầy trang (Page Fullness) xấp xỉ 99% mà không phát sinh Page Split.
  - Khóa công khai `PublicId` (ULID) được đặt trên **Non-Clustered Unique Index** riêng biệt.

### 1.2. Chiến Lược Covering Index (`INCLUDE` Clause)
Nhằm triệt tiêu thao tác **Key Lookup / RID Lookup** (vốn phải quay lại Clustered Index để lấy thêm cột, tiêu tốn nhiều vòng đọc đĩa ngẫu nhiên), toàn bộ các truy vấn tra cứu trạng thái đơn hàng và kiểm tra thanh toán đều sử dụng `Covering Index`:

```sql
-- Covering Index phục vụ màn hình Tra cứu Đơn hàng Buyer (SCR-MKT-08):
-- Đạt 100% Index Seek, loại bỏ hoàn toàn Key Lookup
CREATE NONCLUSTERED INDEX IX_Orders_BuyerId_Status
ON Orders(BuyerId, OrderStatus, CreatedAt DESC)
INCLUDE (OrderId, ListingId, TotalAmount, CurrencyCode);

-- Covering Index phục vụ kiểm tra trạng thái thanh toán theo IdempotencyKey:
CREATE UNIQUE NONCLUSTERED INDEX UQ_PaymentIntents_Idempotency_Covering
ON PaymentIntents(IdempotencyKey)
INCLUDE (PaymentIntentId, OrderId, Amount, PaymentStatus, GatewayTransactionId);
```

### 1.3. Chiến Lược Filtered Index (Chỉ Mục Có Điều Kiện)
Filtered Index giúp giảm 90% dung lượng lưu trữ chỉ mục trong bộ nhớ RAM, đồng thời tăng tốc độ quét theo cấp số nhân:

```sql
-- 1. Quét tìm các đơn giữ hàng 15 phút đang còn hiệu lực:
-- Chỉ đánh index trên các dòng Status = 'Active' (bỏ qua hàng triệu dòng Expired/Consumed)
CREATE UNIQUE NONCLUSTERED INDEX IX_Reservations_Active_Listing
ON InventoryReservations(ListingId)
INCLUDE (ReservationId, BuyerId, ExpiresAt, RowVersion)
WHERE Status = 'Active';

-- 2. Worker quét các bản tin Outbox chưa gửi đi (Pending Messages):
-- Bảng Outbox tích lũy hàng chục triệu tin, nhưng Worker chỉ cần quét tin chưa xử lý
CREATE NONCLUSTERED INDEX IX_Outbox_Pending_Messages
ON OutboxMessages(OccurredOn ASC)
INCLUDE (MessageId, AggregateType, AggregateId, EventType, PayloadJSON)
WHERE ProcessedAt IS NULL;
```

---

## 2. Chiến Lược Đánh Index Chuyên Sâu Trên MongoDB

### 2.1. Quy Tắc ESR (Equality → Sort → Range) Cho Compound Index
MongoDB xử lý truy vấn hiệu quả nhất khi các trường trong Compound Index tuân thủ nghiêm ngặt thứ tự **ESR**:
1. **E (Equality):** Các trường so sánh bằng chính xác đặt đầu tiên.
2. **S (Sort):** Các trường sắp xếp thứ tự đặt ở giữa.
3. **R (Range):** Các trường lọc theo khoảng giá, thời gian đặt ở cuối.

```javascript
// Áp dụng cho màn hình Kênh Người Bán xem danh sách tin (SCR-SEL-03):
// Query: { "seller.seller_id": "USR-8812", "status": "Published" }, Sort: { "created_at": -1 }
// Tuân thủ ESR: Equality (seller_id, status) -> Sort (created_at)
db.listings.createIndex(
  { "seller.seller_id": 1, "status": 1, "created_at": -1 },
  { name: "idx_listings_seller_status_created" }
);

// Áp dụng cho truy vấn lọc danh mục theo khoảng giá:
// Query: { "category_id": "CAT-PHONE", "status": "Published", "pricing.listing_price": { $gte: 5000000, $lte: 15000000 } }
// Tuân thủ ESR: Equality (category_id, status) -> Range (listing_price)
db.listings.createIndex(
  { "category_id": 1, "status": 1, "pricing.listing_price": 1 },
  { name: "idx_listings_category_price_range" }
);
```

### 2.2. Index Trên Mảng Thuộc Tính Động (Dynamic Attribute Pattern)
Để hỗ trợ người dùng lọc theo các thuộc tính đặc thù của đồ cũ (VD: Dung lượng bộ nhớ `128GB`, Hãng `Apple`, RAM `16GB`) mà không cần tạo hàng chục index riêng lẻ gây phình to RAM:

```javascript
// Multikey Compound Index trên mảng dynamic_attributes [{k, v}]
db.listings.createIndex(
  { "category_id": 1, "dynamic_attributes.k": 1, "dynamic_attributes.v": 1, "status": 1 },
  { name: "idx_listings_dynamic_attrs" }
);
```

### 2.3. Tự Động Thu Dọn Bản Ghi Quá Hạn (TTL Index)
Nhằm tự động giải phóng dung lượng đĩa cho các dữ liệu sự kiện tạm thời mà không cần viết batch job:

```javascript
// Tự động xóa thông báo cá nhân sau 90 ngày kể từ ngày tạo
db.notifications.createIndex(
  { "created_at": 1 },
  { expireAfterSeconds: 7776000, name: "ttl_notifications_90days" }
);

// Tự động xóa raw payload webhook 3PL sau 30 ngày
db.webhook_logs.createIndex(
  { "received_at": 1 },
  { expireAfterSeconds: 2592000, name: "ttl_webhook_logs_30days" }
);
```

---

## 3. Danh Mục Top 15 Truy Vấn Nóng Nhất (Top 15 Hot Queries Catalog)

| Query ID | Tên Nghiệp Vụ | CSDL Đích | Tần Suất | Mẫu Truy Vấn / Lọc Nghiệp Vụ | Index Tương Ứng Tối Ưu |
| :--- | :--- | :--- | :---: | :--- | :--- |
| `Q-01` | Kiểm tra tồn kho giữ chỗ 15p (`INV-01`) | `OrderDB` | Rất cao | `SELECT ReservationId FROM InventoryReservations WHERE ListingId = @id AND Status = 'Active'` | `IX_Reservations_Active_Listing` (Filtered) |
| `Q-02` | Quét dọn giữ chỗ quá hạn 15p | `OrderDB` | 15s/lần | `SELECT ReservationId FROM InventoryReservations WHERE Status = 'Active' AND ExpiresAt < @now` | `IX_Reservations_Active_Expiry` (Filtered) |
| `Q-03` | Lấy chi tiết đơn kèm snapshot | `OrderDB` | Cao | `SELECT * FROM Orders o JOIN OrderItemSnapshots s ON o.OrderId = s.OrderId WHERE o.OrderId = @id` | `UQ_Orders_PublicId` + `UQ_OrderItemSnapshots_OrderId` |
| `Q-04` | Danh sách đơn mua của Buyer (`SCR-MKT-08`) | `OrderDB` | Cao | `SELECT ... FROM Orders WHERE BuyerId = @id ORDER BY CreatedAt DESC` | `IX_Orders_BuyerId_Status` (Covering) |
| `Q-05` | Danh sách đơn bán của Seller (`SCR-SEL-05`) | `OrderDB` | Cao | `SELECT ... FROM Orders WHERE SellerId = @id ORDER BY CreatedAt DESC` | `IX_Orders_SellerId_Status` (Covering) |
| `Q-06` | Polling tin nhắn Transactional Outbox | `OrderDB` | 1s/lần | `SELECT TOP (100) ... FROM OutboxMessages WHERE ProcessedAt IS NULL ORDER BY OccurredOn ASC` | `IX_Outbox_Pending_Messages` (Filtered) |
| `Q-07` | Kiểm tra trùng lặp Webhook ngân hàng | `PaymentDB` | Cao | `SELECT PaymentStatus FROM PaymentIntents WHERE IdempotencyKey = @key` | `UQ_PaymentIntents_Idempotency_Covering` |
| `Q-08` | Tra cứu tài khoản Escrow theo Order | `PaymentDB` | Trung bình | `SELECT * FROM EscrowAccounts WHERE OrderId = @orderId` | `UQ_EscrowAccounts_OrderId` |
| `Q-09` | Quét quỹ Escrow đến hạn giải ngân | `PaymentDB` | 1h/lần | `SELECT ... FROM EscrowAccounts WHERE EscrowStatus = 'Holding' AND ReleaseHoldUntil <= @now` | `IX_Escrow_Eligible_Payout` (Filtered) |
| `Q-10` | Bàn tra cứu 360° theo Ticket/SĐT (`SCR-ADM-03`)| `TrustDB` | Trung bình | `SELECT ... FROM DisputeCases WHERE OrderId = @id` | `IX_Dispute_OrderId` |
| `Q-11` | Tra cứu chi tiết tin đồ cũ Live (`SCR-MKT-03`) | `CatalogDB` | Cực cao | `db.listings.findOne({ "_id": "LST-..." })` | Primary Index `_id` (In-Memory B-Tree) |
| `Q-12` | Danh sách tin đang bán của Seller (`SCR-SEL-03`)| `CatalogDB` | Cao | `db.listings.find({ "seller.seller_id": "...", "status": "Published" }).sort({ "created_at": -1 })` | `idx_listings_seller_status_created` (ESR) |
| `Q-13` | Lọc tin theo Danh mục & Thuộc tính động | `CatalogDB` | Cao | `db.listings.find({ "category_id": "...", "dynamic_attributes": { $elemMatch: { k: "brand", v: "Apple" } } })` | `idx_listings_dynamic_attrs` (Multikey) |
| `Q-14` | Tracking lịch sử vận đơn 3PL (`SCR-MKT-09`) | `ShippingDB`| Cao | `db.shipments.findOne({ "order_id": "ORD-..." }, { "tracking_events": 1 })` | `idx_shipments_order_id` (Unique) |
| `Q-15` | Đếm số thông báo chưa đọc của User | `NotifDB` | Cực cao | `db.notifications.countDocuments({ "recipient_id": "...", "is_read": false })` | Compound Index `{ recipient_id: 1, is_read: 1 }` |

---

## 4. Các Anti-Patterns Bị Cấm & Kỹ Thuật Phân Trang Tối Ưu (Keyset Pagination)

### 4.1. Danh Sách Các Anti-Patterns Bị Nghiêm Cấm Trong Mã Nguồn
1. **Cấm dùng `OFFSET / SKIP` lớn cho danh sách sản phẩm hoặc đơn hàng:**
   - *Lý do:* `SELECT ... OFFSET 50000 ROWS FETCH NEXT 20 ROWS` buộc SQL Server phải đọc và duyệt qua 50.020 bản ghi rồi mới vứt bỏ 50.000 bản ghi đầu, gây nghẽn RAM và tăng thời gian phản hồi từ 10ms lên hàng giây.
2. **Cấm bọc hàm biến đổi quanh cột Index trong mệnh đề `WHERE` (Non-SARGable Queries):**
   - *Ví dụ xấu:* `WHERE YEAR(CreatedAt) = 2026` hoặc `WHERE LOWER(Status) = 'active'`. Các hàm này vô hiệu hóa Index Seek, ép CSDL phải `Table Scan` toàn bộ bảng.
   - *Cách viết chuẩn:* `WHERE CreatedAt >= '2026-01-01' AND CreatedAt < '2027-01-01'`.
3. **Cấm dùng ký tự đại diện đứng đầu trong tìm kiếm quan hệ (`LIKE '%keyword%'`):**
   - Ép SQL Server quét toàn bộ chuỗi. Mọi nhu cầu tìm kiếm từ khóa bắt buộc định tuyến sang **OpenSearch/Elasticsearch**.

### 4.2. Chuẩn Hóa Phân Trang Bằng Con Trỏ (Keyset / Cursor-Based Pagination)
Mọi API danh sách đơn hàng và tin đăng đều chuẩn hóa phân trang theo con trỏ kết hợp `(CreatedAt, InternalId)`:

```sql
-- Thay thế hoàn toàn OFFSET: Tốc độ truy vấn trang 1 hay trang 1.000 đều như nhau (< 5ms)
SELECT TOP (20)
    OrderId,
    TotalAmount,
    OrderStatus,
    CreatedAt,
    InternalId
FROM Orders
WHERE BuyerId = @BuyerId
  AND (
      (CreatedAt < @LastSeenCreatedAt)
      OR (CreatedAt = @LastSeenCreatedAt AND InternalId < @LastSeenInternalId)
  )
ORDER BY CreatedAt DESC, InternalId DESC;
```

