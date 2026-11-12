# User Roles and Permissions – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.0 |
| Trạng thái | Định hướng phân tích; chính sách cuối cần Security/Product approval |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | stakeholder-register.md, user-personas.md, scope-and-boundaries.md, functional-requirements.md |

## 1. Mục đích

Mô tả vai trò người dùng và service account, các quyền nghiệp vụ dự kiến và nguyên tắc phân quyền. Đây là ma trận BA sơ bộ; chưa thay thế authorization design, API policy, threat model hoặc cấu hình IAM.

## 2. Nguyên tắc phân quyền

1. Deny by default: chỉ cấp quyền được nêu rõ.
2. Least privilege: quyền theo nhiệm vụ, thời gian và phạm vi cần thiết.
3. Kiểm tra quyền server-side ở từng request; ẩn nút trên UI không được coi là bảo vệ.
4. Kết hợp RBAC với ownership/context: seller chỉ quản lý listing/đơn thuộc mình; buyer chỉ xem giao dịch của mình.
5. Tách role người dùng sản phẩm khỏi IAM role của AWS và service identity.
6. Tách quyền business operations với platform operations; admin ứng dụng không mặc định truy cập hạ tầng/database.
7. Sensitive action cần audit, idempotency, reason code và có thể cần phê duyệt hai bước.
8. PII và payment data chỉ hiển thị tối thiểu, mask theo vai trò; không cấp raw credential/card data.
9. Hãng giao hàng/payment provider chỉ nhận đúng scope API của integration.
10. Mọi đặc quyền, tài khoản máy và emergency access cần owner, rotation, audit và expiry.

## 3. Vai trò

| Role ID | Vai trò | Người dùng | Phạm vi mặc định |
|---|---|---|---|
| ROLE-00 | Guest | Người chưa đăng nhập | Nội dung/listing công khai, tìm kiếm và trang thông tin. |
| ROLE-01 | Buyer | Người mua đã xác thực | Hồ sơ, địa chỉ, giỏ/checkout, đơn, payment/shipping metadata và review của chính mình. |
| ROLE-02 | Seller | Người bán đã xác thực | Tin đăng, đơn bán, shipment và thông tin buyer tối thiểu liên quan đến đơn của mình. |
| ROLE-03 | Moderator | Người kiểm duyệt | Listing/report/case được giao và hành động moderation đã cấp. |
| ROLE-04 | Support Agent | Nhân viên hỗ trợ | Tra cứu giao dịch/case được phép; hành động nhạy cảm theo workflow/phê duyệt. |
| ROLE-05 | Business Administrator | Quản trị nội dung/nghiệp vụ | Danh mục, trạng thái tài khoản/chính sách ở mức được cấp; không có quyền cloud. |
| ROLE-06 | Platform Operator | Cloud/DevOps/SRE/DBA | Logs, metrics, deployment, database operations, backup/restore theo nhiệm vụ; không mặc định đọc PII nghiệp vụ. |
| SVC-01 | Payment Integration Identity | Service-to-service | Payment create/status/callback/refund endpoints theo policy. |
| SVC-02 | Carrier Integration Identity | Service-to-service | Quote/create/cancel/track/webhook cho Shipping Service. |
| SVC-03 | Internal Service Identity | Microservice | API/event scope cụ thể giữa các service; không có quyền truy cập database khác. |

Một tài khoản cá nhân có thể đồng thời mang Buyer và Seller. Quyền cần được tính theo resource ownership và policy; không nhân bản một account cho từng vai trò nếu không có lý do.

## 4. Ma trận quyền nghiệp vụ sơ bộ

Chú giải: **R** đọc; **C** tạo; **U** cập nhật; **A** hành động quản trị; **—** không có quyền mặc định; **Own** chỉ tài nguyên của chính mình; **Assigned** chỉ hồ sơ/case được phân công. Các quyền phải được giới hạn thêm theo policy.

| Tài nguyên / hành động | Guest | Buyer | Seller | Moderator | Support | Business Admin | Platform Ops |
|---|---:|---:|---:|---:|---:|---:|---:|
| Listing công khai | R | R | R | R | R | R/A | — |
| Tạo/sửa listing | — | — | C/U Own | U theo moderation workflow | — | A theo policy | — |
| Listing lifecycle / giữ hàng | — | — | U Own theo rule | A chỉ moderation hold | Yêu cầu workflow | A theo policy | Không thao tác nghiệp vụ trực tiếp |
| Hồ sơ cá nhân/địa chỉ | — | R/U Own | R/U Own | R tối thiểu khi cần | R mask theo case | A có audit | — |
| Đơn mua | — | R Own/C | — | R metadata nếu case | R Assigned | R/A theo policy | — |
| Đơn bán | — | — | R Own/U bước seller | R metadata khi moderation | R Assigned | R/A theo policy | — |
| Payment details | — | R trạng thái Own | R trạng thái liên quan đơn Own | — | R mask/Assigned | R/A theo policy | Logs kỹ thuật đã mask |
| Refund/cancel payment | — | Request/cancel nếu policy | Request/cancel nếu policy | — | Request/perform nếu được duyệt | Approve/perform theo phân quyền | Không mặc định |
| Shipment/tracking | — | R Own | R Own | R khi case | R Assigned | R/A theo policy | Logs kỹ thuật |
| Chọn/tạo shipment | — | theo checkout policy | C/U đơn bán Own | — | Request workflow | A theo policy | — |
| Review/report | — | C Own/transaction | C Own/transaction | R/A report | R Assigned | R/A policy | — |
| Categories/taxonomy | — | R | R | R | R | C/U/A | — |
| Role assignment | — | — | — | — | — | Hạn chế/approval | IAM riêng; không gộp với app role |
| Logs/metrics/traces | — | — | — | — | Case-level sanitized | Dashboard nghiệp vụ | R/A theo platform role |
| Backup/restore | — | — | — | — | — | — | R/A chuyên biệt, approval/audit |

## 5. Giới hạn dữ liệu theo vai trò

- **Buyer:** chỉ xem hồ sơ, đơn, payment status và shipment của chính mình; payment reference hiển thị đã mask.
- **Seller:** chỉ xem listing và đơn bán của mình; buyer details giới hạn ở thông tin cần để thực hiện giao hàng.
- **Moderator:** xem nội dung và dữ liệu case tối thiểu; không xem payment details trừ khi một quy trình được duyệt yêu cầu.
- **Support:** truy cập theo case và thời hạn; thao tác refund/cancel theo quyền và approval workflow.
- **Business Admin:** quyền quản trị nội dung không đồng nghĩa quyền truy cập database hoặc AWS.
- **Platform Ops:** truy cập kỹ thuật theo service; logs phải mask PII; production data access cần break-glass approval và audit.
- **Carrier identity:** chỉ shipment fields cần giao hàng; không đọc hồ sơ/tài khoản/payment của người dùng.
- **Payment identity:** payment intent/status và các callback cần thiết; không đọc listing/profile rộng hơn nhu cầu.

## 6. Service-to-service authorization

- Mỗi service identity có scope riêng, audience/endpoint cụ thể và credential được quản lý an toàn.
- Service gọi service qua API/event contract; không chia sẻ SQL Server login hoặc MongoDB credential giữa service.
- Event consumer kiểm tra nguồn, schema/version và khả năng xử lý lặp.
- Webhook từ đối tác cần xác minh nguồn/chữ ký theo khả năng nhà cung cấp, chống replay và idempotency key.
- Các quyền database của service chỉ truy cập database/schema/collection sở hữu.
- Quyền vận hành backup/restore tách khỏi quyền ứng dụng bình thường; các thao tác restore cần ghi audit.

## 7. Sensitive actions cần policy riêng

| Hành động | Kiểm soát đề xuất | Câu hỏi cần xác nhận |
|---|---|---|
| Khóa tài khoản / gỡ listing | Reason code, actor, thời hạn, audit, appeal path. | Ai phê duyệt; có tự động hóa không? |
| Hủy đơn / giải phóng reservation | Kiểm tra trạng thái hiện tại, idempotency, thông báo các bên. | Cửa sổ hủy và tác động payment là gì? |
| Refund | Role riêng, giới hạn giá trị, approval khi vượt ngưỡng, đối soát. | Ai có quyền và policy refund nào áp dụng? |
| Xem/export PII | Justification, scope, masking, audit và retention. | Khi nào support/moderator cần xem dữ liệu nhạy cảm? |
| Restore database / đổi backup policy | Approval, change record, recovery runbook, kiểm tra hậu restore. | Ai là người có quyền cuối và ai xác minh? |
| Thay đổi role admin | Tách người đề xuất/phê duyệt, MFA, audit, expiry. | Quy trình cấp/thu hồi role? |

## 8. Các quyết định còn mở

1. Có yêu cầu MFA cho seller/admin/support không?
2. Identity do ReMarket quản lý hay tích hợp identity provider?
3. Một người có thể dùng cùng account với Buyer/Seller không? Có business seller không?
4. Moderator được ẩn tạm hay gỡ vĩnh viễn listing?
5. Support có thể thực hiện refund hay chỉ tạo yêu cầu duyệt?
6. Có yêu cầu hai người duyệt sensitive actions không?
7. PII nào được seller nhìn thấy để giao hàng, và bao lâu được giữ?
8. Platform Ops có cần emergency access vào production data không? Quy trình là gì?
9. Token/secret rotation, audit retention và session expiry cần theo policy nào?

## 9. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo role catalogue và ma trận quyền sơ bộ theo kênh/service. |
