# Đặc Tả Yêu Cầu API — API Requirements & Contracts

| Tài liệu | Đặc Tả Hợp Đồng Giao Tiếp API (RESTful & Internal gRPC Specifications) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-06-API` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phân hệ cha** | [docs/BA/06_microservices-and-integrations/README.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/README.md) |
| **Liên kết truy vết** | [screen-inventory.md](file:///d:/remarket-platform-system/docs/BA/04_product-and-ux/screen-inventory.md), [use-cases.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/use-cases.md), [service-catalog.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/service-catalog.md), [business-rules.md](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/business-rules.md) |

---

## 1. Tiêu Chuẩn Thiết Kế API Toàn Hệ Thống

Toàn bộ các giao diện người dùng trên 3 kênh (`Marketplace`, `Seller Center`, `Back-office Portal`) giao tiếp với hệ thống thông qua chuẩn **RESTful JSON trên nền tảng HTTPS/TLS 1.3**. Các dịch vụ nội bộ giao tiếp với nhau bằng **gRPC / Protobuf** nhằm tối ưu hóa độ trễ và băng thông mạng.

### 1.1. Chuẩn Hóa Header Bắt Buộc (Standard Headers)
- `Authorization: Bearer <JWT>`: Bắt buộc cho mọi request yêu cầu xác thực (`[AUTH]`).
- `X-Correlation-Id: <UUIDv4>`: Mã truy vết xuyên suốt toàn bộ vòng đời của một luồng xử lý phục vụ Distributed Tracing (`NFR-OBS-01`).
- `Idempotency-Key: <UUIDv4 / Hash>`: **Bắt buộc đối với các thao tác Ghi nhạy cảm (`POST /orders/checkout`, `POST /payments/charge`, `POST /shipping/waybill`, `POST /disputes/refund`)** nhằm đảm bảo tính bất biến `INV-03` (chống thực hiện 2 lần).
- `Accept-Language: vi-VN`: Mặc định trả về thông báo lỗi và văn bản bằng tiếng Việt.

### 1.2. Chuẩn Hóa Phản Hồi Lỗi Theo RFC 7807 (Problem Details)
Mọi phản hồi lỗi (HTTP Status $\ge 400$) đều tuân thủ cấu trúc chuẩn **RFC 7807**:

```json
{
  "type": "https://errors.remarket.vn/ERR-ORD-RESERVATION-CONFLICT",
  "title": "Món hàng đang được người khác giữ chỗ",
  "status": 409,
  "detail": "Món đồ cũ này vừa được một người mua khác tạo lệnh giữ hàng 15 phút vào lúc 13:45:00 UTC.",
  "instance": "/api/v1/orders/checkout",
  "error_code": "ERR_RESERVATION_CONFLICT",
  "trace_id": "00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01",
  "timestamp": "2026-11-12T13:45:02.120Z"
}
```

---

## 2. Danh Mục Các Nhóm API Phục Vụ 27 Màn Hình (`SCR-*`)

Hệ thống cung cấp danh mục API phân tách theo phân hệ chức năng:

### 2.1. Nhóm API Định Danh & Người Bán (Identity & Seller APIs)
*Phục vụ các màn hình `SCR-MKT-12`, `SCR-SEL-01`, `MOD-AUTH-01`.*

| Mã API | Phương Thức | Tuyến Đường (Endpoint Path) | Quyền Truy Cập | Mục Đích Nghiệp Vụ Chính |
| :--- | :---: | :--- | :--- | :--- |
| `API-IDN-01` | `POST` | `/api/v1/auth/otp/send` | `[PUBLIC]` | Gửi mã OTP xác thực qua SMS hoặc Email. |
| `API-IDN-02` | `POST` | `/api/v1/auth/login` | `[PUBLIC]` | Xác thực OTP/Mật khẩu, cấp cặp Token: Access Token (15p) & Refresh Token (7 ngày). |
| `API-IDN-03` | `GET` | `/api/v1/user/profile` | `[AUTH]` | Lấy thông tin cá nhân của người dùng đang đăng nhập. |
| `API-IDN-04` | `PUT` | `/api/v1/user/profile` | `[AUTH]` | Cập nhật hồ sơ cá nhân và avatar. |
| `API-IDN-05` | `POST` | `/api/v1/user/addresses` | `[AUTH]` | Thêm địa chỉ nhận hàng mới vào sổ địa chỉ (`SCR-MKT-12`). |
| `API-IDN-06` | `POST` | `/api/v1/seller/onboarding` | `[AUTH]` | Gửi hồ sơ đăng ký Người bán kèm CCCD và tài khoản ngân hàng (`SCR-SEL-01`). |

---

### 2.2. Nhóm API Tin Đăng & Tải Ảnh S3 (Catalog, Listing & Media APIs)
*Phục vụ các màn hình `SCR-MKT-03`, `SCR-SEL-03`, `SCR-SEL-04`.*

| Mã API | Phương Thức | Tuyến Đường (Endpoint Path) | Quyền Truy Cập | Mục Đích Nghiệp Vụ Chính |
| :--- | :---: | :--- | :--- | :--- |
| `API-MED-01` | `POST` | `/api/v1/media/presigned-url` | `[ROLE-02]` | Cấp Presigned PUT URL tải ảnh trực tiếp lên Amazon S3 (`FR-MED-01`). |
| `API-CAT-01` | `GET` | `/api/v1/categories` | `[PUBLIC]` | Lấy toàn bộ cây danh mục đa cấp và các định nghĩa thuộc tính động. |
| `API-CAT-02` | `POST` | `/api/v1/seller/listings` | `[ROLE-02]` | Tạo mới tin đăng đồ cũ kèm bảng khai báo khuyết điểm (`SCR-SEL-04`). |
| `API-CAT-03` | `PUT` | `/api/v1/seller/listings/{id}` | `[ROLE-02]` | Chỉnh sửa nội dung tin đăng (chỉ khả dụng khi chưa có người giữ hàng). |
| `API-CAT-04` | `GET` | `/api/v1/seller/listings` | `[ROLE-02]` | Lấy danh sách kho tin của người bán có phân trang (`SCR-SEL-03`). |
| `API-CAT-05` | `GET` | `/api/v1/listings/{id}` | `[PUBLIC]` | **Đọc Source of Truth trạng thái Live** của tin đăng đồ cũ (`SCR-MKT-03`). |
| `API-CAT-06` | `PUT` | `/api/v1/seller/listings/{id}/archive` | `[ROLE-02]` | Ẩn hoặc gỡ tin đăng khỏi sàn. |

---

### 2.3. Nhóm API Khám Phá & Tìm Kiếm Đa Diện (Search & Discovery APIs)
*Phục vụ các màn hình `SCR-MKT-01`, `SCR-MKT-02`.*

| Mã API | Phương Thức | Tuyến Đường (Endpoint Path) | Quyền Truy Cập | Mục Đích Nghiệp Vụ Chính |
| :--- | :---: | :--- | :--- | :--- |
| `API-SRC-01` | `GET` | `/api/v1/search` | `[PUBLIC]` | Tìm kiếm từ khóa toàn văn tiếng Việt, lọc đa diện (Faceted) và khoảng cách (`SCR-MKT-02`). |
| `API-SRC-02` | `GET` | `/api/v1/search/suggestions` | `[PUBLIC]` | Tự động gợi ý từ khóa tìm kiếm khi người dùng gõ phím (Autocomplete). |
| `API-SRC-03` | `GET` | `/api/v1/feed/home` | `[PUBLIC]` | Lấy dữ liệu cấu trúc trang chủ (Banner, Tin mới đăng, Tin quanh bạn) từ Cache. |

---

### 2.4. Nhóm API Đặt Hàng & Giữ Chỗ 15 Phút (Order & Reservation APIs)
*Phục vụ các màn hình `SCR-MKT-05`, `SCR-MKT-06`, `SCR-MKT-08`, `SCR-MKT-09`, `SCR-SEL-05`, `SCR-SEL-06`.*

| Mã API | Phương Thức | Tuyến Đường (Endpoint Path) | Quyền Truy Cập | Mục Đích Nghiệp Vụ Chính |
| :--- | :---: | :--- | :--- | :--- |
| `API-ORD-01` | `POST` | `/api/v1/cart/items` | `[AUTH]` | Thêm món đồ vào giỏ hàng ý định mua (`SCR-MKT-05`, `BR-ORD-004`). |
| `API-ORD-02` | `POST` | `/api/v1/orders/checkout` | `[AUTH]` | **Chốt chặn giữ hàng 15 phút (`INV-01`), khởi tạo đơn hàng và tạo Saga**. |
| `API-ORD-03` | `GET` | `/api/v1/orders/{orderId}/status` | `[AUTH]` | Kiểm tra thời gian còn lại của đồng hồ giữ chỗ 15 phút (`BR-ORD-003`). |
| `API-ORD-04` | `GET` | `/api/v1/user/orders` | `[AUTH]` | Lấy danh sách đơn mua của Buyer có lọc theo trạng thái (`SCR-MKT-08`). |
| `API-ORD-05` | `GET` | `/api/v1/user/orders/{orderId}` | `[AUTH]` | Xem chi tiết đơn mua, snapshot sản phẩm bất biến và tracking 3PL (`SCR-MKT-09`). |
| `API-ORD-06` | `PUT` | `/api/v1/user/orders/{orderId}/confirm-received` | `[AUTH]` | Người mua bấm xác nhận "Đã nhận được hàng", hoàn tất đơn sớm (`SCR-MKT-09`). |
| `API-ORD-07` | `GET` | `/api/v1/seller/orders` | `[ROLE-02]` | Người bán xem danh sách đơn hàng cần đóng gói và gửi vận chuyển (`SCR-SEL-05`). |

---

### 2.5. Nhóm API Thanh Toán, Webhook & Quỹ Tạm Giữ (Payment & Escrow APIs)
*Phục vụ các màn hình `SCR-MKT-07`, `SCR-SEL-07`.*

| Mã API | Phương Thức | Tuyến Đường (Endpoint Path) | Quyền Truy Cập | Mục Đích Nghiệp Vụ Chính |
| :--- | :---: | :--- | :--- | :--- |
| `API-PAY-01` | `POST` | `/api/v1/payments/intent` | `[AUTH]` | Khởi tạo giao dịch thanh toán trực tuyến, lấy URL cổng VNPAY/MoMo. |
| `API-PAY-02` | `POST` | `/api/v1/webhooks/payment/vnpay` | `[PUBLIC / IPN]` | **Webhook tiếp nhận kết quả thanh toán từ ngân hàng (Xác thực chữ ký HMAC-SHA512)**. |
| `API-PAY-03` | `POST` | `/api/v1/webhooks/payment/momo` | `[PUBLIC / IPN]` | Webhook tiếp nhận kết quả từ ví MoMo. |
| `API-PAY-04` | `GET` | `/api/v1/payments/{orderId}/recheck` | `[AUTH]` | Chủ động gọi QueryDR kiểm tra trạng thái ngân hàng khi Webhook bị trễ (`EX-005`). |
| `API-PAY-05` | `GET` | `/api/v1/seller/finances/escrow` | `[ROLE-02]` | Người bán xem số dư tiền đang tạm giữ tại Escrow và lịch sử Payout (`SCR-SEL-07`). |

---

### 2.6. Nhóm API Vận Chuyển 3PL (Shipping & Tracking APIs)
*Phục vụ các màn hình `SCR-SEL-06`, `SCR-MKT-09`.*

| Mã API | Phương Thức | Tuyến Đường (Endpoint Path) | Quyền Truy Cập | Mục Đích Nghiệp Vụ Chính |
| :--- | :---: | :--- | :--- | :--- |
| `API-SHP-01` | `POST` | `/api/v1/shipping/quote` | `[AUTH]` | Tính cước phí vận chuyển ước tính giữa địa chỉ Seller và Buyer (`BR-SHP-001`). |
| `API-SHP-02` | `POST` | `/api/v1/seller/orders/{orderId}/waybill` | `[ROLE-02]` | Người bán bấm tạo mã vận đơn 3PL (GHN/GHTK) sau khi đóng gói (`SCR-SEL-06`). |
| `API-SHP-03` | `POST` | `/api/v1/webhooks/shipping/ghn` | `[PUBLIC / 3PL]` | Tiếp nhận Webhook cập nhật hành trình kiện hàng từ Giao Hàng Nhanh. |
| `API-SHP-04` | `GET` | `/api/v1/seller/orders/{orderId}/print-label` | `[ROLE-02]` | Lấy dữ liệu in phiếu gửi hàng mã vạch dán kiện hàng (`MOD-PRN-01`). |

---

### 2.7. Nhóm API Kiểm Duyệt, Hỗ Trợ 360° & Tranh Chấp (Trust & Back-Office APIs)
*Phục vụ các màn hình `SCR-ADM-01..04`, `SCR-MKT-10`.*

| Mã API | Phương Thức | Tuyến Đường (Endpoint Path) | Quyền Truy Cập | Mục Đích Nghiệp Vụ Chính |
| :--- | :---: | :--- | :--- | :--- |
| `API-TRU-01` | `POST` | `/api/v1/reports` | `[AUTH]` | Người dùng gửi báo cáo tin đăng hoặc người bán vi phạm (`MOD-RPT-01`). |
| `API-TRU-02` | `GET` | `/api/v1/admin/moderation/queue` | `[ROLE-03]` | Kiểm duyệt viên lấy hàng đợi tin đăng cần thẩm định (`SCR-ADM-01`). |
| `API-TRU-03` | `PUT` | `/api/v1/admin/moderation/{id}/verdict` | `[ROLE-03]` | Đưa ra phán quyết Duyệt (`Approve`) hoặc Từ chối (`Reject`) tin đăng (`SCR-ADM-02`). |
| `API-TRU-04` | `GET` | `/api/v1/admin/support/orders/360` | `[ROLE-04]` | **Bàn Tra cứu 360° Đa Miền:** Xem song song trạng thái Order, Payment, Shipment (`SCR-ADM-03`). |
| `API-TRU-05` | `POST` | `/api/v1/admin/support/reveal-pii` | `[ROLE-04]` | **Mở khóa dữ liệu PII nhạy cảm có ghi nhận bản ghi kiểm toán bắt buộc lý do (`FR-SUP-02`)**. |
| `API-TRU-06` | `POST` | `/api/v1/user/orders/{orderId}/disputes` | `[AUTH]` | Người mua mở hồ sơ khiếu nại tranh chấp hàng đồ cũ trong 72h (`SCR-MKT-10`). |
| `API-TRU-07` | `PUT` | `/api/v1/admin/support/disputes/{id}/arbitrate` | `[ROLE-04]` | Phán quyết tranh chấp: Bác bỏ, Yêu cầu trả hàng, hoặc Ra lệnh Hoàn tiền (`SCR-ADM-04`). |

---

## 3. Đặc Tả Mẫu Payload (Request / Response) Cho 3 API Cốt Lõi

### 3.1. API Tạo Đơn & Giữ Hàng 15 Phút (`POST /api/v1/orders/checkout`)
- **Headers:** `Idempotency-Key: 8a7f9b2c-4e1d-482a-9f12-009182abcdef`, `Authorization: Bearer <JWT>`
- **Request Payload:**
```json
{
  "listing_id": "01HXYZ1234567890ABCDEF01",
  "shipping_address_id": "ADDR-1029-01",
  "payment_provider": "VNPAY",
  "delivery_carrier_code": "GHN"
}
```
- **Response Payload (`201 Created`):**
```json
{
  "order_id": "ORD-20261112-9812",
  "reservation_id": "RES-20261112-0081",
  "listing_id": "01HXYZ1234567890ABCDEF01",
  "item_price": 11500000.00,
  "buyer_protection_fee": 50000.00,
  "shipping_fee": 35000.00,
  "total_amount": 11585000.00,
  "currency": "VND",
  "order_status": "Created",
  "hold_expires_at": "2026-11-12T14:00:00.000Z",
  "remaining_seconds": 900,
  "payment_url": "https://sandbox.vnpayment.vn/paymentv2/vpcpay.html?vnp_Amount=1158500000&vnp_TxnRef=ORD-20261112-9812..."
}
```

---

### 3.2. Webhook Tiếp Nhận Kết Quả Ngân Hàng (`POST /api/v1/webhooks/payment/vnpay`)
- **Headers:** `X-Forwarded-For: 113.160.92.202` (IP của Cổng thanh toán)
- **Request Payload (URL Encoded / Form Data):**
```
vnp_Amount=1158500000&vnp_BankCode=NCB&vnp_CardType=ATM&vnp_OrderInfo=Thanh+toan+don+hang+ORD-20261112-9812&vnp_PayDate=20261112134812&vnp_ResponseCode=00&vnp_TmnCode=REMARKET&vnp_TransactionNo=14981290&vnp_TxnRef=PAY-20261112-8812&vnp_SecureHash=3e8a719d2...
```
- **Response Payload (`200 OK` theo chuẩn IPN VNPAY):**
```json
{
  "RspCode": "00",
  "Message": "Confirm Success"
}
```

---

### 3.3. Yêu Cầu Mở Khóa Dữ Liệu PII Có Kiểm Toán (`POST /api/v1/admin/support/reveal-pii`)
- **Mục đích:** Hỗ trợ Chuyên viên Hỗ trợ (`ROLE-04`) mở khóa số điện thoại hoặc địa chỉ khách hàng theo quy trình `FR-SUP-02`.
- **Request Payload:**
```json
{
  "target_user_id": "USR-1029",
  "ticket_id": "TCK-20261112-0045",
  "access_reason": "Khách hàng gọi hotline báo shipper không liên lạc được để giao đơn ORD-9812",
  "fields": ["phone_number", "shipping_address"]
}
```
- **Response Payload (`200 OK` - Dữ liệu tự động hết hạn hiển thị sau 60s):**
```json
{
  "audit_log_id": "AUD-20261112-8921",
  "revealed_data": {
    "phone_number": "0987654321",
    "shipping_address": "123 Đường Nguyễn Trãi, Phường 2, Quận 5, TP.HCM"
  },
  "view_expires_at": "2026-11-12T14:15:00.000Z",
  "warning": "Hành động này đã được ghi vào Nhật ký An ninh bất biến và gửi báo cáo tới Security Lead."
}
```

