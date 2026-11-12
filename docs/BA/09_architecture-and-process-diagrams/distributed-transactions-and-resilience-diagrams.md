# Sơ Đồ Giao Dịch Phân Tán, Khóa Đồng Thời & Khả Năng Chịu Lỗi (Distributed Transactions & Resilience Diagrams) – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Tài liệu | Tập hợp Sơ đồ Khóa Giữ hàng Hai Tầng (`INV-01`), Saga Đặt hàng, Webhook Idempotent (`INV-02`), Transactional Outbox (`INV-03`), Circuit Breaker & DLQ |
| Mã tài liệu | `DOC-BA-09-DST` |
| Phiên bản | 1.0 |
| Trạng thái | Hoàn thiện (Baseline Kiến trúc Giao dịch Phân tán & Chịu lỗi) |
| Ngày cập nhật | 12/11/2026 |
| Phân hệ cha | [docs/BA/09_architecture-and-process-diagrams/README.md](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md) |
| Tài liệu liên quan | [concurrency-locking-and-distributed-transactions.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/concurrency-locking-and-distributed-transactions.md), [resilience-fault-tolerance-and-rate-limiting.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/resilience-fault-tolerance-and-rate-limiting.md) |

---

## 1. Sơ Đồ Động Cơ Khóa Giữ Hàng Hai Tầng 15 Phút (`DIAG-DST-01`: Two-Tier Reservation Lock – Bảo Vệ `INV-01`)

Sơ đồ Sequence chi tiết từng mili-giây khi 100 Người mua cùng bấm "Mua Ngay" một món hàng độc bản (`Inventory = 1`) tại cùng một thời điểm:

```mermaid
sequenceDiagram
    autonumber
    actor BuyerA as Người Mua A (Đến trước 1ms)
    actor BuyerB as 99 Người Mua Khác (Đến đồng thời)
    participant GW as SVC-00: API Gateway
    participant OrdSvc as SVC-05: Order Service
    participant Redis as ElastiCache Redis (Tầng 1 Lock)
    participant SQL as OrderDB SQL Server (Tầng 2 ACID Guard)
    participant CatSvc as SVC-02: CatalogDB

    par 100 Requests đồng thời chạm vào Order Service
        BuyerA->>GW: POST /api/v1/checkout/reserve (ListingId = L-99)
        GW->>OrdSvc: Chuyển tiếp Request của Buyer A
    and
        BuyerB->>GW: POST /api/v1/checkout/reserve (ListingId = L-99)
        GW->>OrdSvc: Chuyển tiếp 99 Requests còn lại
    end

    Note over OrdSvc,Redis: TẦNG 1: KHÓA PHÂN TÁN TỐC ĐỘ CAO TRÊN REDIS (< 2ms)
    OrdSvc->>Redis: Buyer A: SET lock:listing:L-99 Order_A NX EX 900
    Redis-->>OrdSvc: OK (Buyer A giành được khóa độc quyền 900 giây)

    OrdSvc->>Redis: 99 Buyers khác: SET lock:listing:L-99 Order_X NX EX 900
    Redis-->>OrdSvc: NIL (Key đã tồn tại -> Chặn đứng ngay tại RAM)
    OrdSvc-->>BuyerB: HTTP 409 Conflict ("Món hàng đang có người giữ chỗ giao dịch")

    Note over OrdSvc,SQL: TẦNG 2: KHÓA BỀN VỮNG ACID TRÊN SQL SERVER (UPDLOCK + FILTERED UNIQUE INDEX)
    OrdSvc->>CatSvc: gRPC: VerifyAndReserveListing(L-99, ExpectedStatus = ACTIVE)
    CatSvc-->>OrdSvc: Thành công (CatalogDB.listings.status -> RESERVED, version++)

    OrdSvc->>SQL: BEGIN TRAN (Isolation: READ COMMITTED + UPDLOCK, ROWLOCK)
    OrdSvc->>SQL: INSERT Orders (AWAITING_PAYMENT) + INSERT OrderReservations (ACTIVE, Expires = +15m)
    Note right of SQL: Filtered Unique Index:\nCREATE UNIQUE INDEX UX_Active_Listing\nON OrderReservations(ListingPublicId)\nWHERE ReservationStatus = 'ACTIVE'
    OrdSvc->>SQL: INSERT OutboxMessages (EVT-08 OrderCreatedEvent)
    OrdSvc->>SQL: COMMIT TRAN
    SQL-->>OrdSvc: Commit thành công

    OrdSvc-->>BuyerA: HTTP 201 Created (ReservationId, Countdown 15:00, PaymentUrl)
```

---

## 2. Sơ Đồ Saga Điều Phối Đặt Hàng & Bù Trừ Khi Quá Hạn 15 Phút (`DIAG-DST-02`: Checkout & Expiration Compensation Saga)

Sơ đồ mô tả đầy đủ hai nhánh của Saga Đặt hàng: **Nhánh Thành công (Forward Recovery)** và **Nhánh Bù trừ Tự động khi Hết hạn 15 phút (Backward Compensation)**:

```mermaid
flowchart TB
    subgraph SAGA_START ["GIAI ĐOẠN 1: KHỞI TẠO & GIỮ CHỖ 15 PHÚT"]
        T1["Bước T1 (OrderSvc):\nKhóa Redis + Tạo Order (AWAITING_PAYMENT)\n& OrderReservation (TTL 15p)"] --> T2["Bước T2 (CatalogSvc):\nChuyển Listing.STATUS = RESERVED\n& Xóa Cache chi tiết tin"]
        T2 --> T3["Bước T3 (PaymentSvc):\nTạo PaymentIntent (INTENT_CREATED)\n& Chờ Webhook PSP (Tối đa 900s)"]
    end

    T3 --> OUTCOME{"Kết quả sau 15 phút?"}

    subgraph FORWARD_PATH ["NHÁNH A: THANH TOÁN THÀNH CÔNG TRONG HẠN 15 PHÚT (HAPPY PATH)"]
        F1["Bước F1 (PaymentSvc):\nNhận Webhook hợp lệ -> Ghi sổ cái\nEscrowHoldings (CAPTURED)"] --> F2["Bước F2 (OrderSvc):\nChuyển Order -> PAYMENT_ESCROWED\n& OrderReservation -> CONSUMED"]
        F2 --> F3["Bước F3 (CatalogSvc):\nChuyển Listing -> SOLD vĩnh viễn\n& Xóa khỏi chỉ mục OpenSearch"]
        F3 --> F4["Bước F4 (NotificationSvc):\nThông báo Seller đóng gói hàng"]
    end

    subgraph COMPENSATION_PATH ["NHÁNH B: QUÁ HẠN 15 PHÚT HOẶC BUYER HỦY (COMPENSATION SAGA)"]
        C1["Bước Bù trừ C1 (OrderSvc Reaper Worker):\nChuyển OrderReservation -> EXPIRED\n& Order -> CANCELLED_TIMEOUT\n& Xóa khóa Redis lock:listing:{id}"] --> C2["Bước Bù trừ C2 (PaymentSvc):\nChuyển PaymentIntent -> INTENT_EXPIRED\n(Nếu tiền về trễ sau bước này -> Auto-Refund)"]
        C2 --> C3["Bước Bù trừ C3 (CatalogSvc):\nKhôi phục Listing.STATUS = ACTIVE\n& Đồng bộ lại lên OpenSearch"]
    end

    OUTCOME -- "Webhook <= 15p" --> F1
    OUTCOME -- "Timeout > 15p / Hủy" --> C1

    classDef init fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef ok fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef comp fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    class T1,T2,T3 init;
    class F1,F2,F3,F4 ok;
    class C1,C2,C3 comp;
```

---

## 3. Sơ Đồ Xử Lý Idempotent Webhook & Tự Động Hoàn Tiền Khi Thanh Toán Trễ Phút 16 (`DIAG-DST-03`: Bảo Vệ `INV-02`)

Sơ đồ mô tả cách `SVC-06 Payment Service` xử lý hai bài toán hóc búa nhất của thanh toán phân tán: (1) Đối tác PSP bắn lặp nhiều lần cùng một Webhook và (2) Người mua thanh toán sát giờ khiến tiền về ở phút thứ 16 sau khi khóa giữ hàng đã hết hạn:

```mermaid
sequenceDiagram
    autonumber
    participant PSP as Cổng PSP (VNPAY / MoMo)
    participant WAF as AWS WAF v2 & ALB
    participant PaySvc as SVC-06: Payment Service
    participant PayDB as PaymentDB (SQL Server)
    participant OrdSvc as SVC-05: Order Service
    actor Buyer as Người Mua

    PSP->>WAF: POST /api/v1/webhook/psp (ProviderTxnRef = TXN-888, HMAC Signature)
    WAF->>PaySvc: Kiểm tra IP Whitelist & Chuyển tiếp

    PaySvc->>PaySvc: 1. Xác thực chữ ký số HMAC-SHA512 (Chống giả mạo)
    PaySvc->>PayDB: 2. Kiểm tra Idempotency: SELECT 1 FROM PaymentTransactions WHERE ProviderTxnRef = 'TXN-888'

    alt Kịch bản 1: Webhook bị bắn lặp lần thứ 2..10 (Đã tồn tại TXN-888)
        PayDB-->>PaySvc: Đã tồn tại bản ghi giao dịch
        PaySvc-->>PSP: Trả về ngay HTTP 200 OK (Không cộng tiền lần 2 - Bảo vệ INV-02)
    else Kịch bản 2: Giao dịch mới chưa từng xử lý
        PaySvc->>OrdSvc: 3. Kiểm tra trạng thái hiện tại của Đơn hàng & Khóa giữ chỗ
        alt Nhánh 2A: Đơn hàng vẫn đang AWAITING_PAYMENT (Trong hạn 15 phút)
            PaySvc->>PayDB: BEGIN TRAN -> INSERT PaymentTransactions + INSERT EscrowHoldings (CAPTURED) -> COMMIT
            PaySvc->>OrdSvc: Phát sự kiện EVT-14 PaymentSucceededEvent
            PaySvc-->>PSP: HTTP 200 OK
        else Nhánh 2B: Đơn hàng ĐÃ HỦY QUÁ HẠN (Phút thứ 16+ / CANCELLED_TIMEOUT)
            Note over PaySvc,PayDB: BẤT BIẾN INV-01 & INV-02: Tuyệt đối không giao hàng đã hết khóa,\nhoàn trả lại 100% tiền ngay lập tức cho Buyer
            PaySvc->>PayDB: BEGIN TRAN -> INSERT PaymentTransactions (EXPIRED_PAYMENT_CAPTURED) + INSERT Refunds (PENDING_AUTO_REFUND) -> COMMIT
            PaySvc-->>PSP: HTTP 200 OK (Ghi nhận đã nhận thông báo)
            PaySvc->>PSP: 4. Gọi bất đồng bộ API Refund 100% số tiền (Trong vòng < 30 giây)
            PSP-->>PaySvc: Xác nhận hoàn tiền thành công (REFUNDED)
            PaySvc->>Buyer: 5. Gửi thông báo: "Đơn đã quá hạn giữ chỗ 15p, hệ thống đã tự động hoàn tiền 100%"
        end
    end
```

---

## 4. Sơ Đồ Kiến Trúc Transactional Outbox, Debezium CDC & Idempotent Inbox (`DIAG-DST-04`: Bảo Vệ `INV-03`)

Sơ đồ mô tả cơ chế đảm bảo **Zero Lost Events (`INV-03`)** và **Exactly-Once Processing Semantics** giữa các Microservices mà không cần dùng giao dịch 2 pha (2PC) gây nghẽn cổ chai:

```mermaid
flowchart LR
    subgraph PRODUCER_SVC ["1. DỊCH VỤ PHÁT SỰ KIỆN (PRODUCER: VD SVC-05 ORDER)"]
        direction TB
        BIZ_LOGIC["Thực thi Nghiệp vụ:\nCập nhật trạng thái Đơn hàng"]
        LOCAL_TX["ACID Local Transaction trên OrderDB:\n1. UPDATE Orders SET Status = 'PAID'\n2. INSERT OutboxMessages (EventId, Payload, Processed=0)"]
        BIZ_LOGIC --> LOCAL_TX
    end

    subgraph RELAY_ENGINE ["2. ĐỘNG CƠ TRÍCH XUẤT & TRUYỀN TẢI (CDC & POLLED FALLBACK)"]
        direction TB
        CDC["Luồng Chính: Debezium CDC Connector\nĐọc SQL Transaction Log (< 200ms)"]
        POLLER["Luồng Dự phòng: Outbox Fallback Poller\nQuét bản ghi Processed=0 quá 60s"]
        BROKER["Apache Kafka / AWS EventBridge\n(Partition Key = OrderPublicId\nĐảm bảo đúng thứ tự FIFO)"]
        CDC & POLLER --> BROKER
    end

    subgraph CONSUMER_SVC ["3. DỊCH VỤ TIÊU THỤ SỰ KIỆN (CONSUMER: VD SVC-08 / SVC-09)"]
        direction TB
        INBOX_CHK{"Kiểm tra bảng InboxMessages:\nEventId đã tồn tại?"}
        SKIP["Bỏ qua (Idempotent Ack)\nTránh xử lý lặp lần 2"]
        EXEC_CONS["ACID Local Transaction tại Đích:\n1. INSERT InboxMessages (EventId, ProcessedAt)\n2. Thực thi cập nhật nghiệp vụ đích"]

        INBOX_CHK -- "Đã có" --> SKIP
        INBOX_CHK -- "Chưa có" --> EXEC_CONS
    end

    LOCAL_TX --> CDC
    LOCAL_TX -.->|"Nếu CDC gián đoạn"| POLLER
    BROKER -->|"At-Least-Once Delivery"| INBOX_CHK

    classDef prod fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef rel fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef cons fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class BIZ_LOGIC,LOCAL_TX prod;
    class CDC,POLLER,BROKER rel;
    class INBOX_CHK,SKIP,EXEC_CONS cons;
```

---

## 5. Sơ Đồ Máy Trạng Thái Circuit Breaker, Bulkhead Isolation & Timeout Budget (`DIAG-DST-05`)

Sơ đồ mô tả cơ chế tự bảo vệ của ReMarket khi các hệ thống bên ngoài (VNPAY, MoMo, GHN, GHTK) hoặc một thành phần phụ thuộc gặp sự cố chậm/treo:

```mermaid
stateDiagram-v2
    [*] --> CLOSED: Trạng thái Bình thường (Cho phép 100% Request đi qua)

    CLOSED --> OPEN: Tỷ lệ lỗi HTTP 5xx / Timeout >= 50%\ntrong cửa sổ 20 requests liên tiếp (Hoặc 5 lần Timeout 10s)

    state OPEN {
        [*] --> FAST_FAIL
        FAST_FAIL: Ngắt mạch hoàn toàn trong 30 giây\n- Không gọi sang đối tác đang lỗi\n- Trả về Fallback ngay (< 5ms)\n- Hoặc chuyển sang đối tác 3PL dự phòng
    }

    OPEN --> HALF_OPEN: Sau khi hết 30 giây chờ (Cool-down Timer)

    state HALF_OPEN {
        [*] --> PROBE_TEST
        PROBE_TEST: Cho phép thử nghiệm đúng 3 Requests thăm dò
    }

    HALF_OPEN --> CLOSED: Cả 3 Requests thăm dò đều Thành công\n(Đối tác đã hồi phục hoàn toàn)
    HALF_OPEN --> OPEN: Có bất kỳ Request thăm dò nào Thất bại\n(Quay lại ngắt mạch thêm 30 giây)
```

### Sơ đồ Cô lập Khoang Chịu Lỗi (Bulkhead Thread/Connection Pool Isolation):

```mermaid
flowchart LR
    subgraph SHIPPING_CONTAINER ["SVC-07: SHIPPING SERVICE CONTAINER (TỔNG 100 WORKER THREADS)"]
        POOL_GHN["Khoang Bulkhead 1: GHN Pool\n(Giới hạn tối đa: 40 Threads)\nNếu GHN treo -> Chỉ nghẽn khoang này"]
        POOL_GHTK["Khoang Bulkhead 2: GHTK Pool\n(Giới hạn tối đa: 40 Threads)\nHoạt động độc lập 100%"]
        POOL_QUERY["Khoang Bulkhead 3: Tracking Read Pool\n(Dành riêng: 20 Threads)\nLuôn phản hồi tra cứu cho Buyer"]
    end

    POOL_GHN -->|"Timeout 2.5s"| EXT_GHN["API Giao Hàng Nhanh (Đang treo)"]
    POOL_GHTK -->|"Phản hồi 200ms"| EXT_GHTK["API Giao Hàng Tiết Kiệm (Bình thường)"]

    classDef err fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    classDef ok fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class POOL_GHN,EXT_GHN err;
    class POOL_GHTK,POOL_QUERY,EXT_GHTK ok;
```

---

## 6. Sơ Đồ Cô Lập Tin Nhắn Lỗi Vào Dead Letter Queue (DLQ) & Công Cụ Replay (`DIAG-DST-06`)

Sơ đồ mô tả quy trình xử lý tin nhắn lỗi (Poison Pill / Transient Failure) qua 3 cấp thử lại trước khi đưa vào hàng đợi chết (DLQ) và công cụ phát lại (Replay) dành cho SRE (`US-OPS-07`):

```mermaid
flowchart LR
    MAIN_Q["Hàng Đợi Chính\n(Main Kafka Topic / SQS Queue)"] --> CONSUMER["Event Consumer Worker"]

    CONSUMER --> RESULT{"Xử lý thành công?"}
    RESULT -- "Thành công" --> COMMIT["Commit Offset\n& Ghi InboxMessages"]

    RESULT -- "Lỗi tạm thời / Poison Pill" --> RETRY_1["Retry Lần 1\n(Chờ 2 giây + Jitter)"]
    RETRY_1 --> R1_OK{"Thành công?"}
    R1_OK -- "Không" --> RETRY_2["Retry Lần 2\n(Chờ 10 giây + Jitter)"]
    RETRY_2 --> R2_OK{"Thành công?"}
    R2_OK -- "Không" --> RETRY_3["Retry Lần 3\n(Chờ 60 giây + Jitter)"]
    RETRY_3 --> R3_OK{"Thành công?"}

    R1_OK & R2_OK & R3_OK -- "Có" --> COMMIT
    R3_OK -- "Thất bại sau 3 lần" --> DLQ[("Dead Letter Queue (SQS DLQ)\nLưu giữ bản tin lỗi + StackTrace\n(Retention: 14 ngày)")]

    DLQ --> ALERT["Kích hoạt Cảnh báo P2:\nBắn tin nhắn Slack & PagerDuty cho SRE"]
    ALERT --> SRE_TOOL["SRE Admin Replay UI (US-OPS-07):\n1. Kỹ sư kiểm tra & sửa lỗi logic/dữ liệu\n2. Bấm 'Replay Selected Messages'\n3. Đẩy ngược bản tin về Main Queue an toàn"]
    SRE_TOOL -.-> MAIN_Q

    classDef main fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef rty fill:#fff3e0,stroke:#ef6c00,stroke-width:1.5px;
    classDef dlq fill:#ffebee,stroke:#c62828,stroke-width:2px;
    classDef ok fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class MAIN_Q,CONSUMER main;
    class RETRY_1,RETRY_2,RETRY_3 rty;
    class DLQ,ALERT dlq;
    class COMMIT,SRE_TOOL ok;
```

---

## 7. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Kiến trúc Phân tán & SRE | Khởi tạo hoàn chỉnh 6 sơ đồ giao dịch phân tán, khóa đồng thời và khả năng chịu lỗi (`DIAG-DST-01..06`). |
