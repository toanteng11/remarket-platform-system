# Sơ Đồ Máy Trạng Thái & Vòng Đời Thực Thể (State Machine & Lifecycle Diagrams) – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Tài liệu | Tập hợp 6 Sơ đồ Máy Trạng thái Hữu hạn (Finite State Machines): Tài khoản/KYC, Tin đăng, Đơn hàng, Thanh toán Ký quỹ, Vận đơn 3PL & Khiếu nại |
| Mã tài liệu | `DOC-BA-09-STM` |
| Phiên bản | 1.0 |
| Trạng thái | Hoàn thiện (Baseline Quy tắc Chuyển dịch Trạng thái Miền) |
| Ngày cập nhật | 12/11/2026 |
| Phân hệ cha | [docs/BA/09_architecture-and-process-diagrams/README.md](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md) |
| Tài liệu liên quan | [domain-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/domain-model.md), [functional-requirements.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/functional-requirements.md), [event-catalog.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/event-catalog.md) |

---

## 1. Máy Trạng Thái Tài Khoản & Định Danh KYC Người Bán (`DIAG-STM-01`: Account & KYC State Machine)

Quản lý bởi `SVC-01 Identity & Access Service` trên `IdentityDB`. Đảm bảo phân tách rõ trạng thái xác thực người mua, hồ sơ KYC người bán và các chế tài khóa tài khoản:

```mermaid
stateDiagram-v2
    [*] --> PENDING_OTP: Đăng ký SĐT/Email (US-ACC-01)
    PENDING_OTP --> ACTIVE_BUYER: Xác thực OTP thành công (EVT-01)
    PENDING_OTP --> EXPIRED_REGISTRATION: Quá hạn 10 phút không nhập OTP

    ACTIVE_BUYER --> SELLER_ONBOARDED: Đăng ký bán hàng & Thêm TK Ngân hàng (US-ACC-04)
    SELLER_ONBOARDED --> KYC_PENDING: Tải lên CCCD 2 mặt & Ảnh chân dung (US-ACC-05)
    KYC_PENDING --> VERIFIED_SELLER: Admin phê duyệt KYC (SCR-ADM-01 / EVT-02)
    KYC_PENDING --> KYC_REJECTED: Ảnh mờ / Thông tin không khớp
    KYC_REJECTED --> KYC_PENDING: Người bán gửi lại hồ sơ KYC hợp lệ

    ACTIVE_BUYER --> SUSPENDED: Vi phạm chính sách / Đáng ngờ gian lận (US-ACC-06)
    SELLER_ONBOARDED --> SUSPENDED: Đăng hàng cấm lặp lại / Gian lận
    VERIFIED_SELLER --> SUSPENDED: Tỷ lệ tranh chấp vượt ngưỡng cho phép

    SUSPENDED --> ACTIVE_BUYER: Kháng nghị thành công / Hết hạn tạm khóa
    SUSPENDED --> VERIFIED_SELLER: Khôi phục quyền bán hàng sau thẩm định
    SUSPENDED --> BANNED_PERMANENTLY: Vi phạm nghiêm trọng / Lừa đảo (Thu hồi toàn bộ Token)
    BANNED_PERMANENTLY --> [*]
```

### Bảng Quy tắc Chuyển trạng thái Tài khoản:
- Khi chuyển sang `SUSPENDED` hoặc `BANNED_PERMANENTLY`: Toàn bộ Refresh Token trên Redis bị thu hồi tức thời; các tin đăng đang `ACTIVE` của người bán đó bị ẩn tạm thời khỏi sàn (`HIDDEN`), nhưng các đơn hàng đang trong quá trình giao hoặc đang giữ tiền ký quỹ vẫn tiếp tục được xử lý hoàn tất/hoàn tiền để bảo vệ người mua.

---

## 2. Máy Trạng Thái Vòng Đời Tin Đăng Đồ Cũ Đơn Chiếc (`DIAG-STM-02`: Listing Lifecycle State Machine)

Quản lý bởi `SVC-02 Catalog & Listing Service` trên `CatalogDB`. Bảo vệ nghiêm ngặt nguyên tắc **Món hàng đơn chiếc (`Inventory = 1`)** và **Bất biến `INV-01` (Zero Overselling)**:

```mermaid
stateDiagram-v2
    [*] --> DRAFT: Người bán lưu nháp (US-LST-01)
    DRAFT --> PENDING_MODERATION: Gửi duyệt chứa từ khóa nhạy cảm (US-LST-06)
    DRAFT --> REJECTED_AUTO: Chứa từ khóa hàng cấm tuyệt đối (EVT-05)
    DRAFT --> ACTIVE: Hợp lệ qua cổng lọc tự động (EVT-03 ListingPublished)

    PENDING_MODERATION --> ACTIVE: Moderator phê duyệt (US-LST-07 / SCR-ADM-03)
    PENDING_MODERATION --> REJECTED_MOD: Moderator từ chối kèm lý do
    REJECTED_MOD --> PENDING_MODERATION: Người bán sửa tin & Gửi kháng nghị (US-TRU-06)

    ACTIVE --> HIDDEN: Người bán chủ động tạm ẩn tin (US-LST-03)
    HIDDEN --> ACTIVE: Người bán bật hiển thị lại

    ACTIVE --> RESERVED: Buyer đặt giữ chỗ 15 phút thành công (INV-01 / EVT-04)
    RESERVED --> ACTIVE: Hết hạn 15 phút không thanh toán hoặc Buyer hủy (EVT-10)
    RESERVED --> SOLD: Thanh toán thành công vào Ký quỹ Escrow (EVT-06 / EVT-14)

    SOLD --> ACTIVE: Đơn hủy trước khi giao & Seller chọn đăng bán lại
    SOLD --> COMPLETED_ARCHIVED: Đơn hàng hoàn tất giải ngân (Lưu trữ lịch sử)

    ACTIVE --> EXPIRED: Quá 30 ngày chưa bán được (FR-LST-10)
    EXPIRED --> ACTIVE: Người bán bấm gia hạn tin đăng

    ACTIVE --> REMOVED_BY_TRUST: Bị gỡ do nhận >= 5 báo cáo vi phạm xác thực (US-TRU-05)
    REMOVED_BY_TRUST --> ACTIVE: Kháng nghị Appeal thành công (US-TRU-06)
    REMOVED_BY_TRUST --> [*]
    COMPLETED_ARCHIVED --> [*]
```

### Bảng Ràng buộc Bất biến Trạng thái Tin đăng:
- **Chỉ trạng thái `ACTIVE`** mới được phép hiển thị trên kết quả tìm kiếm OpenSearch và mới được phép chuyển sang `RESERVED`.
- Khi ở trạng thái `RESERVED` hoặc `SOLD`: Hệ thống khóa cứng các trường `Price`, `ConditionGrade`, `Images`, `Description` (cấm Người bán chỉnh sửa làm sai lệch bằng chứng mua hàng).

---

## 3. Máy Trạng Thái Vòng Đời Đơn Hàng (`DIAG-STM-03`: Order Lifecycle State Machine)

Quản lý bởi `SVC-05 Order Processing Service` trên `OrderDB` (SQL Server). Mọi chuyển dịch trạng thái đều kiểm tra `RowVersion` và ghi vào bảng `OrderStatusAudit` + `OutboxMessages`:

```mermaid
stateDiagram-v2
    [*] --> CREATED: Khởi tạo phiên Checkout & Snapshot sản phẩm (US-CHK-07)
    CREATED --> AWAITING_PAYMENT: Đã khóa giữ chỗ 15p & Tạo PaymentIntent (EVT-08)

    AWAITING_PAYMENT --> CANCELLED_TIMEOUT: Quá 15 phút không nhận được tiền (EVT-10)
    AWAITING_PAYMENT --> CANCELLED_BY_BUYER: Buyer chủ động hủy phiên giữ chỗ (US-CHK-06)
    AWAITING_PAYMENT --> PAYMENT_ESCROWED: Webhook PSP báo thu tiền thành công (EVT-14)

    PAYMENT_ESCROWED --> AWAITING_FULFILLMENT: Thông báo Seller chuẩn bị đóng gói (Hạn 48h)
    PAYMENT_ESCROWED --> CANCELLED_BEFORE_SHIP: Buyer/Seller hủy trước khi tạo vận đơn (Hoàn tiền 100%)

    AWAITING_FULFILLMENT --> CANCELLED_SELLER_OVERDUE: Quá 48h Seller không xác nhận (Auto-Refund 100%)
    AWAITING_FULFILLMENT --> READY_TO_SHIP: Seller xác nhận đóng gói & Tạo vận đơn 3PL (EVT-17)

    READY_TO_SHIP --> SHIPPING: Bưu tá 3PL lấy hàng thành công (PICKED_UP / EVT-18)
    SHIPPING --> DELIVERED: 3PL giao thành công kèm ảnh POD (EVT-19 / Mở cửa sổ 72h)
    SHIPPING --> DELIVERY_FAILED_RTS: Giao thất bại 3 lần -> Chuyển hoàn về Seller (EVT-20)

    DELIVERY_FAILED_RTS --> CANCELLED_RTS_REFUNDED: Seller nhận lại hàng -> Hoàn tiền cho Buyer

    DELIVERED --> COMPLETED: Buyer bấm Đã nhận hàng HOẶC hết 72h không khiếu nại (EVT-12)
    DELIVERED --> DISPUTED: Buyer mở khiếu nại trong vòng 72h (INV-04 / EVT-22)

    DISPUTED --> COMPLETED: Trọng tài bác bỏ khiếu nại -> Giải ngân cho Seller
    DISPUTED --> SPLIT_RESOLVED: Trọng tài phán quyết hoàn tiền 1 phần & giải ngân 1 phần
    DISPUTED --> RETURN_IN_PROGRESS: Trọng tài yêu cầu Buyer gửi trả hàng cho Seller (Hạn 72h)
    RETURN_IN_PROGRESS --> RETURN_EXPIRED_TO_SELLER: Buyer quá 72h không gửi hàng trả -> Đóng khiếu nại & Giải ngân Seller
    RETURN_EXPIRED_TO_SELLER --> [*]: Giải phóng Escrow cho Seller
    RETURN_IN_PROGRESS --> DISPUTE_ESCALATED_SELLER: Seller mở phản khiếu nại sau POD (Kèm video khui hàng)
    DISPUTE_ESCALATED_SELLER --> REFUNDED_AFTER_RETURN: Trọng tài xác minh hàng hoàn đúng -> Hoàn tiền 100% Buyer
    DISPUTE_ESCALATED_SELLER --> SPLIT_RESOLVED: Trọng tài xử chia Escrow / Bồi thường
    RETURN_IN_PROGRESS --> REFUNDED_AFTER_RETURN: Seller xác nhận nhận hàng HOẶC im lặng > 48h sau POD (Auto-Refund 100%)

    COMPLETED --> [*]
    SPLIT_RESOLVED --> [*]
    REFUNDED_AFTER_RETURN --> [*]
    CANCELLED_TIMEOUT --> [*]
    CANCELLED_BY_BUYER --> [*]
    CANCELLED_BEFORE_SHIP --> [*]
    CANCELLED_SELLER_OVERDUE --> [*]
    CANCELLED_RTS_REFUNDED --> [*]
```

---

## 4. Máy Trạng Thái Thanh Toán & Sổ Cái Ký Quỹ (`DIAG-STM-04`: Payment & Escrow State Machine)

Quản lý bởi `SVC-06 Payment & Escrow Service` trên `PaymentDB` (SQL Server). Bảo vệ **Bất biến `INV-02` (Zero Double Charge)** và **`INV-04` (Escrow Freeze on Dispute)**:

```mermaid
stateDiagram-v2
    [*] --> INTENT_CREATED: Tạo PaymentIntent & Chữ ký HMAC-SHA512 (US-PAY-01)
    INTENT_CREATED --> PAYMENT_FAILED: Cổng PSP báo lỗi thẻ / Không đủ số dư
    PAYMENT_FAILED --> INTENT_CREATED: Buyer thử thanh toán lại (Trong hạn 15p)
    INTENT_CREATED --> INTENT_EXPIRED: Hết hạn giữ chỗ 15 phút

    INTENT_EXPIRED --> EXPIRED_PAYMENT_CAPTURED: Ngoại lệ tiền về trễ ở phút thứ 16+ (INV-02)
    EXPIRED_PAYMENT_CAPTURED --> AUTO_REFUNDING: Tự động phát lệnh hoàn tiền khẩn cấp (< 30s)

    INTENT_CREATED --> CAPTURED_IN_ESCROW: Webhook PSP hợp lệ & Idempotent (EVT-14)
    CAPTURED_IN_ESCROW --> AUTO_REFUNDING: Đơn hủy trước khi giao / Seller quá hạn 48h

    CAPTURED_IN_ESCROW --> RELEASE_TIMER_72H: Đơn hàng chuyển sang DELIVERED (Đếm ngược 72h)
    RELEASE_TIMER_72H --> FROZEN_BY_DISPUTE: Nhận sự kiện EVT-22 DisputeOpened (< 500ms - INV-04)

    FROZEN_BY_DISPUTE --> PAYOUT_SCHEDULED: Phán quyết thắng cho Seller (EVT-24)
    FROZEN_BY_DISPUTE --> AUTO_REFUNDING: Phán quyết hoàn 100% cho Buyer (EVT-25)
    FROZEN_BY_DISPUTE --> SPLIT_SETTLEMENT_EXEC: Phán quyết chia tiền Buyer X% & Seller (100-X)%

    RELEASE_TIMER_72H --> PAYOUT_SCHEDULED: Buyer xác nhận sớm HOẶC hết 72h không tranh chấp
    PAYOUT_SCHEDULED --> RELEASED_TO_SELLER: Chuyển khoản ngân hàng thành công cho Seller (EVT-16)

    AUTO_REFUNDING --> REFUNDED_TO_BUYER: Cổng PSP xác nhận hoàn tiền thành công (EVT-15)
    SPLIT_SETTLEMENT_EXEC --> SPLIT_COMPLETED: Hoàn tất cả lệnh Refund một phần & Payout một phần

    RELEASED_TO_SELLER --> [*]
    REFUNDED_TO_BUYER --> [*]
    SPLIT_COMPLETED --> [*]
```

---

## 5. Máy Trạng Thái Vận Đơn 3PL & Chuyển Hoàn (`DIAG-STM-05`: Shipment & RTS State Machine)

Quản lý bởi `SVC-07 Shipping & Logistics Service` trên `ShippingDB` (MongoDB). Chuẩn hóa toàn bộ mã trạng thái riêng của GHN, GHTK về **6 nhóm trạng thái chuẩn hóa (Canonical Shipment Statuses)**:

```mermaid
stateDiagram-v2
    [*] --> WAYBILL_CREATED: Gọi API 3PL tạo mã vận đơn thành công (US-SHP-01)
    WAYBILL_CREATED --> PICKUP_SCHEDULED: Đã gán bưu tá đến lấy hàng tại địa chỉ Seller
    WAYBILL_CREATED --> CANCELLED_WAYBILL: Hủy vận đơn trước khi bưu tá lấy hàng

    PICKUP_SCHEDULED --> PICKUP_FAILED_RETRY: Bưu tá không liên hệ được Seller (Hẹn lấy lại)
    PICKUP_FAILED_RETRY --> PICKED_UP: Lấy hàng thành công ở lần thử tiếp theo
    PICKUP_FAILED_RETRY --> CANCELLED_WAYBILL: Quá 3 lần lấy thất bại -> Hủy đơn do lỗi Seller

    PICKUP_SCHEDULED --> PICKED_UP: Bưu tá thu gom kiện hàng thành công (EVT-18)
    PICKED_UP --> IN_TRANSIT: Kiện hàng đang luân chuyển giữa các kho/bưu cục
    IN_TRANSIT --> OUT_FOR_DELIVERY: Bưu tá đang đi phát tới địa chỉ Buyer

    OUT_FOR_DELIVERY --> DELIVERED: Giao thành công kèm ảnh POD & Chữ ký (EVT-19)
    OUT_FOR_DELIVERY --> DELIVERY_ATTEMPT_FAILED: Giao thất bại lần 1 / lần 2 (Hẹn giao lại)
    DELIVERY_ATTEMPT_FAILED --> OUT_FOR_DELIVERY: Bưu tá đi phát lại theo lịch hẹn

    DELIVERY_ATTEMPT_FAILED --> RETURNING_TO_SENDER: Thất bại đủ 3 lần -> Kích hoạt chuyển hoàn RTS (EVT-20)
    RETURNING_TO_SENDER --> RETURNED_TO_SELLER: Đã trả kiện hàng nguyên vẹn về cho Seller

    IN_TRANSIT --> EXCEPTION_LOST_DAMAGED: Hãng vận chuyển báo thất lạc hoặc hư hỏng nặng
    EXCEPTION_LOST_DAMAGED --> CLAIM_3PL_COMPENSATION: Đối soát đền bù bảo hiểm 3PL & Hoàn tiền Buyer

    DELIVERED --> [*]
    RETURNED_TO_SELLER --> [*]
    CANCELLED_WAYBILL --> [*]
    CLAIM_3PL_COMPENSATION --> [*]
```

---

## 6. Máy Trạng Thái Hồ Sơ Khiếu Nại & Tranh Chấp (`DIAG-STM-06`: Dispute Case State Machine)

Quản lý bởi `SVC-08 Trust & Support Service` trên `SupportDB` (MongoDB), phối hợp chặt chẽ với `SVC-06 Payment`:

```mermaid
stateDiagram-v2
    [*] --> DISPUTE_OPENED: Buyer gửi khiếu nại + Video mở hộp trong 72h (US-DSP-01)
    DISPUTE_OPENED --> AWAITING_SELLER_RESPONSE: Đã đóng băng Escrow (INV-04) & Chờ Seller phản hồi (24h)

    AWAITING_SELLER_RESPONSE --> SELLER_ACCEPTED_REFUND: Seller chấp nhận lỗi & Đồng ý hoàn tiền
    AWAITING_SELLER_RESPONSE --> SELLER_COUNTER_EVIDENCE: Seller từ chối & Tải lên video đóng gói đối chứng
    AWAITING_SELLER_RESPONSE --> ESCALATED_TO_ARBITRATION: Quá 24h Seller không phản hồi

    SELLER_COUNTER_EVIDENCE --> ESCALATED_TO_ARBITRATION: Chuyển lên Bàn Trọng Tài ReMarket (SCR-ADM-04)

    ESCALATED_TO_ARBITRATION --> NEED_MORE_EVIDENCE: Trọng tài yêu cầu bổ sung ảnh/video rõ nét hơn
    NEED_MORE_EVIDENCE --> ESCALATED_TO_ARBITRATION: Các bên đã bổ sung bằng chứng

    ESCALATED_TO_ARBITRATION --> VERDICT_REJECT_CLAIM: Bác bỏ khiếu nại -> Giải ngân 100% cho Seller
    ESCALATED_TO_ARBITRATION --> VERDICT_PARTIAL_REFUND: Phán quyết hoàn tiền một phần (Buyer giữ hàng)
    ESCALATED_TO_ARBITRATION --> VERDICT_RETURN_AND_REFUND: Phán quyết trả hàng & Hoàn tiền 100% (Buyer phải gửi trả <= 72h)

    VERDICT_RETURN_AND_REFUND --> RETURN_SHIPPING_ACTIVE: Tạo vận đơn 3PL chiều về từ Buyer -> Seller (Hạn gửi 72h)
    RETURN_SHIPPING_ACTIVE --> RETURN_EXPIRED_TO_SELLER: Buyer quá 72h không giao hàng cho 3PL (BR-DSP-005)
    RETURN_SHIPPING_ACTIVE --> RETURN_DELIVERED_PENDING_CHECK: 3PL trả Webhook POD đã giao hoàn tới Seller (Mở timer 48h)
    RETURN_DELIVERED_PENDING_CHECK --> RETURN_RECEIVED_VERIFIED: Seller xác nhận đã nhận lại đúng món hàng
    RETURN_DELIVERED_PENDING_CHECK --> RETURN_AUTO_REFUNDED: Hết 48h sau POD Seller im lặng -> Tự động hoàn 100% Buyer
    RETURN_DELIVERED_PENDING_CHECK --> RETURN_DISPUTED_BY_SELLER: Seller gửi Video khui hàng phản khiếu nại (< 48h)
    RETURN_DISPUTED_BY_SELLER --> SETTLEMENT_EXECUTED: Trọng tài thẩm định Video & ra phán quyết cuối (Giữ/Chia Escrow)

    SELLER_ACCEPTED_REFUND --> SETTLEMENT_EXECUTED: Phát lệnh hoàn tiền qua Payment Service (Phí sàn = 0đ)
    VERDICT_REJECT_CLAIM --> SETTLEMENT_EXECUTED: Phát lệnh giải ngân qua Payment Service
    VERDICT_PARTIAL_REFUND --> SETTLEMENT_EXECUTED: Phát lệnh chia tiền Split qua Payment Service (5% trên phần Seller)
    RETURN_EXPIRED_TO_SELLER --> SETTLEMENT_EXECUTED: Đóng khiếu nại & Phát lệnh giải ngân cho Seller
    RETURN_RECEIVED_VERIFIED --> SETTLEMENT_EXECUTED: Phát lệnh hoàn tiền 100% qua Payment Service
    RETURN_AUTO_REFUNDED --> SETTLEMENT_EXECUTED: Phát lệnh hoàn tiền tự động 100% qua Payment Service

    SETTLEMENT_EXECUTED --> DISPUTE_CLOSED_IMMUTABLE: Đóng hồ sơ & Lưu trữ WORM Audit Vault (INV-05)
    DISPUTE_CLOSED_IMMUTABLE --> [*]
```

---

## 7. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Phân tích Nghiệp vụ & Kiến trúc | Khởi tạo hoàn chỉnh 6 sơ đồ máy trạng thái hữu hạn (`DIAG-STM-01..06`). |
