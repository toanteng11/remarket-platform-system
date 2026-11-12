# Kiểm Soát Đồng Thời, Khóa Tồn Kho & Giao Dịch Phân Tán — Concurrency, Locking & Sagas

| Tài liệu | Kiểm Soát Đồng Thời, Khóa Tồn Kho & Giao Dịch Phân Tán (Concurrency & Sagas) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-05-CON` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phân hệ cha** | [docs/BA/05_domain-and-data/README.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/README.md) |
| **Liên kết truy vết** | [domain-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/domain-model.md), [business-rules.md](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/business-rules.md) (`BR-ORD-003`), [functional-requirements.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/functional-requirements.md) (`INV-01`, `INV-03`, `INV-04`) |

---

## 1. Giải Quyết Bài Toán Cốt Lõi `INV-01`: Chống Bán Trùng Hàng Đơn Chiếc (`Stock = 1`)

Khác với hàng hóa thông thường có số lượng tồn kho lớn, sản phẩm C2C đồ cũ tồn tại **duy nhất 1 món hàng vật lý** (`Stock = 1`). Khi một món đồ giá tốt xuất hiện, có thể có 50 người cùng bấm nút "Mua ngay" tại cùng một giây.

### 1.1. Kiến Trúc Phòng Thủ 2 Lớp (Two-Tier Reservation Guard)

```mermaid
flowchart TD
    BUYERS["50 Người Mua cùng bấm 'MUA NGAY' tại t = 0"] --> GATEWAY["API Gateway / BFF Service"]
    
    subgraph TIER_1["LỚP 1: Chặn Tải Nhanh Tại Biên (Redis Distributed Lock)"]
        GATEWAY --> REDIS_CMD["Lệnh Nguyên Tử: SET lock:listing:{id} {buyer_id} NX PX 900000"]
        REDIS_CMD -- "Thành công (1 người đầu tiên)" --> TIER_2
        REDIS_CMD -- "Thất bại (49 người chậm hơn)" --> FAST_FAIL["Trả về Lỗi Tức Thì (< 2ms):\n'Sản phẩm đang được người khác đặt mua'\n(Giảm 98% tải đánh vào Database)"]
    end

    subgraph TIER_2["LỚP 2: Chốt Chặn Toàn Vẹn ACID Tuyệt Đối (SQL Server OrderDB)"]
        TIER_2 --> SQL_TX["Atomic INSERT & Filtered Unique Index:\nINSERT INTO InventoryReservations ... WHERE Status = 'Active'"]
        SQL_TX -- "Cam kết Thành công" --> HOLD_SUCCESS["Khởi tạo Giữ hàng 15:00 Thành công\n(Bật đồng hồ đếm ngược SCR-MKT-06)"]
        SQL_TX -- "Xung đột Khóa (Unique Violation)" --> DB_FAIL["Rollback & Trả về Lỗi Xung Đột EX-002"]
    end
```

### 1.2. Phân Tích So Sánh: Optimistic Locking vs Pessimistic Locking
Tại sao ReMarket **không sử dụng Pessimistic Lock dài hạn (`UPDLOCK, HOLDLOCK`)** trong suốt 15 phút người dùng ở màn hình Checkout?
- **Pessimistic Locking dài hạn:** Mở một Transaction và giữ khóa dòng trên CSDL suốt 15 phút chờ người dùng quét mã QR thanh toán.
  - *Hậu quả tai hại:* Chiếm dụng toàn bộ Connection Pool của dịch vụ, gây nghẽn kết nối diện rộng (Connection Pool Starvation), làm sập các luồng giao dịch khác và gây Deadlock dây chuyền.
- **Giải Pháp Chuẩn Của ReMarket: Atomic State Transition & Optimistic Concurrency:**
  - Khóa vật lý trên CSDL chỉ tồn tại trong **vài mili-giây** lúc thực thi câu lệnh chèn bản ghi giữ chỗ. Sau khi chèn xong, Transaction lập tức `COMMIT` và giải phóng toàn bộ khóa CSDL.
  - Tính toàn vẹn 15 phút được duy trì thông qua dữ liệu logic: Cột `ExpiresAt = SYSUTCDATETIME() + 15 phút` kết hợp ràng buộc **Filtered Unique Index** trên SQL Server (`UQ_Active_Listing_Reservation` - chỉ cho phép duy nhất 1 bản ghi `Status = 'Active'` cho một `ListingId`).

```sql
-- Lệnh chốt chặn ACID giữ chỗ 15 phút (Chạy trong Transaction cực ngắn < 5ms)
BEGIN TRANSACTION;

-- Kiểm tra xem đã có ai giữ chỗ hoặc mua sản phẩm này chưa
IF EXISTS (
    SELECT 1 FROM InventoryReservations WITH (UPDLOCK, HOLDLOCK)
    WHERE ListingId = @ListingId AND Status IN ('Active', 'Consumed')
)
BEGIN
    ROLLBACK TRANSACTION;
    THROW 50001, 'ERR_LISTING_ALREADY_RESERVED_OR_SOLD', 1;
END

-- Chèn bản ghi giữ chỗ độc quyền 15 phút
INSERT INTO InventoryReservations (
    ReservationId, ListingId, BuyerId, ReservedAt, ExpiresAt, Status
) VALUES (
    @ReservationId, @ListingId, @BuyerId, SYSUTCDATETIME(), DATEADD(minute, 15, SYSUTCDATETIME()), 'Active'
);

-- Ghi bản tin Outbox để đồng bộ sang Redis & Search Index
INSERT INTO OutboxMessages (
    MessageId, AggregateType, AggregateId, EventType, PayloadJSON, OccurredOn
) VALUES (
    @MsgId, 'Listing', @ListingId, 'ListingReservedEvent', @PayloadJSON, SYSUTCDATETIME()
);

COMMIT TRANSACTION;
```

---

## 2. Thiết Kế Cơ Chế Hết Hạn Giữ Hàng 15 Phút & Tự Động Thu Hồi (`BR-ORD-003`)

Hệ thống kết hợp 2 cơ chế đánh giá trạng thái song song để đảm bảo giải phóng tồn kho chính xác từng giây:

```mermaid
stateDiagram-v2
    [*] --> Active: Người mua bấm Checkout (Khoá 15:00)
    
    Active --> Consumed: Thanh toán thành công trong 15p (INV-01 hoàn tất)
    Active --> Expired: Quá 15p chưa thanh toán (Thu hồi tồn kho)
    Active --> Cancelled: Người mua chủ động huỷ đơn
    
    Expired --> [*]: Bắn Event ListingPublished (Mở lại cho người khác)
    Consumed --> [*]: Bắn Event ListingSold (Đóng vĩnh viễn)
```

1. **Cơ Chế Thụ Động (Lazy Evaluation):** Mỗi khi có bất kỳ truy vấn nào đọc thông tin tin đăng hoặc khi người dùng bấm nút "Xác nhận thanh toán" tại `SCR-MKT-06`, hệ thống kiểm tra ngay điều kiện:
   $$\text{IF } \texttt{SYSUTCDATETIME()} > \texttt{ExpiresAt} \implies \text{Trạng thái lập tức bị coi là } \texttt{Expired}$$
2. **Cơ Chế Chủ Động (Active Background Sweeper Worker):**
   - Một tiến trình Worker ngầm chạy định kỳ **15 giây một lần**, quét Filtered Index `IX_Reservations_Active_Expiry` để tìm các đơn giữ hàng đã quá 15 phút mà chưa thanh toán:
   ```sql
   -- Worker thu hồi các đơn giữ chỗ quá hạn
   UPDATE TOP (100) InventoryReservations
   SET Status = 'Expired'
   OUTPUT inserted.ReservationId, inserted.ListingId, inserted.BuyerId
   WHERE Status = 'Active' AND ExpiresAt < SYSUTCDATETIME();
   ```
   - Lệnh `UPDATE` tự động sinh sự kiện `ReservationExpiredEvent` ghi vào `OutboxMessages` để thông báo cho `CatalogService` chuyển tin đăng về lại trạng thái `Published` và gửi thông báo `MOD-EXP-01` về trình duyệt của Buyer.

---

## 3. Quản Lý Giao Dịch Phân Tán (Saga Pattern & Outbox)

Do kiến trúc Database-per-Service cấm Distributed Transactions (2PC), các quy trình nghiệp vụ xuyên nhiều dịch vụ được điều phối thông qua **Orchestration-based Saga**.

### 3.1. Luồng Giao Dịch Đặt Hàng & Thanh Toán (Checkout & Payment Saga)

```mermaid
sequenceDiagram
    autonumber
    actor Buyer as Người Mua (ROLE-01)
    participant OrderSvc as Order Service (SQL Server)
    participant Outbox as Order Outbox Table
    participant Relay as Debezium CDC / Relay Worker
    participant Kafka as Kafka Message Bus
    participant PaySvc as Payment Service (SQL Server)
    participant Gateway as Cổng Thanh Toán (VNPAY)

    Buyer->>OrderSvc: POST /api/checkout (Tạo đơn & Giữ hàng 15p)
    OrderSvc->>OrderSvc: Transaction ACID: Chèn Reservation (Active) + Order (Created) + Outbox
    OrderSvc-->>Buyer: 201 Created (Kèm Payment Intent URL)

    Outbox->>Relay: Đọc sự kiện OrderCreatedEvent
    Relay->>Kafka: Publish event: order-created
    Kafka->>PaySvc: Consume event: Khởi tạo PaymentIntent (Pending)

    Buyer->>Gateway: Tiến hành quét mã QR / Điền thông tin thẻ ngân hàng
    Gateway-->>PaySvc: Webhook Callback thông báo kết quả giao dịch

    alt Giao Dịch Thành Công (Payment Success)
        PaySvc->>PaySvc: Transaction: Cập nhật Payment = Captured + Tạo Escrow Holding
        PaySvc->>Kafka: Publish event: payment-captured
        Kafka->>OrderSvc: Consume event: payment-captured
        OrderSvc->>OrderSvc: Transaction: Order = Confirmed + Reservation = Consumed
        OrderSvc->>Kafka: Publish event: order-confirmed (Kích hoạt Shipping)
    else Giao Dịch Thất Bại hoặc Hết Hạn 15 Phút (Payment Failed / Expired)
        PaySvc->>Kafka: Publish event: payment-failed
        Kafka->>OrderSvc: Consume event: Kích hoạt Giao Dịch Bù Trừ (Compensating Transaction)
        OrderSvc->>OrderSvc: Cập nhật Order = Cancelled + Giải phóng Reservation = Expired
        OrderSvc->>Kafka: Publish event: listing-republished (Mở lại tin bán)
    end
```

### 3.2. Đảm Bảo Tính Bất Khả Trùng Lặp (Idempotent Consumer & Inbox Pattern - `INV-03`)
Để đảm bảo bất biến `INV-03` (xử lý trùng lặp Webhook hoặc Consumer nhận 2 lần một bản tin Kafka không gây lỗi nghiệp vụ):
- Mỗi microservice duy trì bảng `InboxMessages`:
  ```sql
  CREATE TABLE InboxMessages (
      MessageId VARCHAR(64) NOT NULL,              -- IdempotencyKey từ Sender
      ConsumerName VARCHAR(50) NOT NULL,
      ReceivedAt DATETIME2(3) NOT NULL DEFAULT SYSUTCDATETIME(),
      CONSTRAINT PK_InboxMessages PRIMARY KEY (MessageId, ConsumerName)
  );
  ```
- Khi nhận sự kiện, Consumer bắt đầu một Transaction nội bộ: Chèn `MessageId` vào `InboxMessages`. Nếu vi phạm khóa chính trùng lặp (`Primary Key Violation 2627`), Consumer lập tức `ACK` bỏ qua bản tin và không xử lý lại nghiệp vụ.

---

## 4. Cấp Độ Cô Lập Giao Dịch CSDL (SQL Server Isolation Levels)

Để giải quyết bài toán xung đột khóa giữa luồng Ghi giao dịch và luồng Đọc báo cáo:
- Mọi CSDL quan hệ trong hệ sinh thái ReMarket đều bắt buộc kích hoạt chế độ **`READ_COMMITTED_SNAPSHOT` (RCSI)**:
  ```sql
  ALTER DATABASE OrderDB SET READ_COMMITTED_SNAPSHOT ON WITH ROLLBACK IMMEDIATE;
  ALTER DATABASE PaymentDB SET READ_COMMITTED_SNAPSHOT ON WITH ROLLBACK IMMEDIATE;
  ```
- **Lợi ích kỹ thuật vượt trội:**
  - **Đọc không khóa Ghi, Ghi không khóa Đọc:** Các câu lệnh `SELECT` tra cứu của Chuyên viên Hỗ trợ (`SCR-ADM-03`) sử dụng phiên bản bản ghi trong `tempdb` (Row Versioning), hoàn toàn không dựng Shared Lock (`S-Lock`).
  - Triệt tiêu 100% nguy cơ Deadlock giữa luồng Người mua đang chèn bản ghi đặt hàng và luồng Người bán đang đọc danh sách đơn bán!

