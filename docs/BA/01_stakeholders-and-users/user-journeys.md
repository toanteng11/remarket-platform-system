# User Journeys – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Luồng giả thuyết để validation; chính sách nghiệp vụ còn mở |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | user-personas.md, user-needs-and-pain-points.md, user-roles-and-permissions.md, use-cases.md |

## 1. Mục đích và cách đọc

Journey mô tả mục tiêu, bước người dùng, điểm tiếp xúc, nhu cầu và điểm rủi ro ở mức trải nghiệm. Các journey không thay thế use case, business rules, API flow hoặc trạng thái service.

- Các bước hiện là baseline giả thuyết; cần walkthrough với đại diện người dùng.
- Luồng thanh toán, COD, giữ hàng, phí giao, hủy/hoàn tiền và dispute chưa được chốt.
- Kênh dự kiến gồm Marketplace, Seller Center và Admin/Back-office; cổng giao nhận riêng chỉ cần nếu ReMarket tự vận hành đội ship.
- Hệ thống chưa được coi là đang vận hành thật.

## 2. Journey J-01 – Người mua tìm và mua một món hàng

**Persona:** P-01 Người mua cá nhân  
**Mục tiêu:** Tìm đúng món, hiểu tình trạng và hoàn tất mua hàng với trạng thái giao dịch rõ ràng.  
**Điểm kết thúc thành công:** Đơn hợp lệ được xác nhận, người mua biết payment/shipping tiếp theo là gì.  
**Điểm cần xác nhận:** Phương thức thanh toán, phí giao, thời gian giữ hàng, COD và chính sách đổi/trả.

| Giai đoạn | Hành động người mua | Phản hồi hệ thống dự kiến | Nhu cầu / rủi ro | Service liên quan |
|---|---|---|---|---|
| 1. Vào marketplace | Mở web/app, có thể chưa đăng nhập. | Hiển thị danh mục, tìm kiếm và tin công khai; không yêu cầu dữ liệu cá nhân khi chưa cần. | Dễ bắt đầu; quyền riêng tư. | Marketplace UI, Search |
| 2. Tìm kiếm | Nhập từ khóa, lọc theo danh mục/giá/tình trạng/vị trí nếu có. | Trả kết quả và trạng thái khả dụng gần nhất; cho biết khi không có kết quả. | Index có thể chậm hơn Listing; không hứa giữ hàng chỉ vì kết quả hiện ra. | Search, Catalog & Listing |
| 3. Đánh giá tin | Xem ảnh, tình trạng, mô tả, giá, người bán và điều kiện giao. | Hiển thị thông tin rõ ràng, thời điểm cập nhật nếu phù hợp; cho phép báo cáo tin. | Ảnh/mô tả không đủ; hàng cũ có tình trạng riêng. | Listing, User Profile, Trust |
| 4. Đăng nhập/địa chỉ | Tạo tài khoản hoặc xác thực; nhập/chọn địa chỉ giao. | Chỉ yêu cầu thông tin cần cho checkout; xác nhận địa chỉ và consent theo chính sách. | PII, lỗi đăng nhập, không rõ dữ liệu được dùng thế nào. | Identity, User/Profile |
| 5. Checkout và kiểm tra hàng | Xác nhận món, số tiền, địa chỉ và phương thức giao. | Order yêu cầu Listing kiểm tra và giữ món theo quy tắc; từ chối nếu vừa hết hàng. | Hai người có thể checkout cùng lúc; giá/khả dụng cần được kiểm tra lại. | Order, Listing, Shipping quote |
| 6. Thanh toán | Chọn phương thức và xác nhận. | Payment tạo yêu cầu có idempotency; trạng thái pending/success/fail rõ ràng. | Timeout/callback trễ; tránh tạo giao dịch trùng hoặc báo thành công sai. | Payment, Order |
| 7. Theo dõi | Mở lịch sử đơn, xem trạng thái chuẩn và tracking. | Hiển thị trạng thái đơn và vận chuyển; thông báo thay đổi quan trọng. | Hãng có trạng thái khác nhau; webhook bị trễ/trùng. | Order, Shipping, Notification |
| 8. Nhận hàng | Xác nhận nhận nếu quy trình yêu cầu; đánh giá hoặc báo vấn đề. | Ghi nhận kết thúc giao dịch; mở luồng hỗ trợ/dispute theo policy. | Giao sai/hỏng, thiếu bằng chứng hoặc không biết cách báo sự cố. | Shipping, Review, Support |
| 9. Sau giao dịch | Gửi đánh giá, báo cáo hoặc yêu cầu hỗ trợ. | Xác nhận tiếp nhận, mã case và bước tiếp theo. | Lạm dụng review/report; thời hạn giải quyết chưa định nghĩa. | Reviews, Trust, Support |

### Ngoại lệ cần đặc tả

- Món hàng bị reserve/sold ngay trước khi buyer xác nhận order.
- Payment timeout nhưng nhà cung cấp đã thu tiền; payment callback gửi lại nhiều lần.
- Hết thời hạn giữ hàng trong khi người mua đang chuyển sang cổng thanh toán.
- Hãng không nhận đơn hoặc webhook không tới; shipment status không khớp Order.
- Người mua hủy trước/sau payment hoặc giao hàng không thành công.
- Listing bị moderator ẩn khi buyer đang checkout.
- Buyer muốn thanh toán COD, nếu phương thức này được chấp nhận.

## 3. Journey J-02 – Người bán đăng món hàng và hoàn tất đơn

**Persona:** P-02 Người bán cá nhân  
**Mục tiêu:** Tạo tin có thông tin chính xác, nhận đơn hợp lệ và bàn giao món hàng.  
**Điểm cần xác nhận:** Có kiểm duyệt trước khi công khai không; seller có chọn hãng ship/đàm phán/nhận tiền theo cách nào?

| Giai đoạn | Hành động người bán | Phản hồi hệ thống dự kiến | Nhu cầu / rủi ro | Service liên quan |
|---|---|---|---|---|
| 1. Vào Seller Center | Đăng nhập, mở khu vực bán hàng. | Phân quyền seller; hiển thị listing/đơn của chính seller. | Không lộ đơn hoặc PII người bán khác. | Identity, Profile |
| 2. Tạo listing | Nhập tiêu đề, danh mục, giá, tình trạng và ảnh. | Hướng dẫn field bắt buộc, upload ảnh, autosave/draft nếu phù hợp. | Mô tả tình trạng không chuẩn; upload lỗi; mất dữ liệu form. | Listing, S3 media |
| 3. Xem trước/công bố | Kiểm tra nội dung; submit. | Validate rules; đưa sang Draft/Pending/Active theo chính sách được chọn. | Lỗi validation không giải thích; kiểm duyệt không rõ thời hạn. | Listing, Trust/Moderation |
| 4. Quản lý tin | Sửa giá/mô tả, ẩn hoặc đánh dấu không còn bán. | Chỉ cho sửa trạng thái hợp lệ; cập nhật Search projection. | Có đơn/hold đang tồn tại; cập nhật gây sai thông tin trong order snapshot. | Listing, Search, Order |
| 5. Nhận đơn | Xem order, hạn xử lý và payment state. | Chỉ hiện đơn thuộc listing của seller và thông tin cần thiết. | Không nhầm pending payment với order đã xác nhận. | Order, Payment |
| 6. Chuẩn bị giao | Đóng gói, chọn/tạo yêu cầu giao theo policy. | Shipping hiển thị quote/options và tạo shipment qua adapter. | Hãng không khả dụng; phí/timing thay đổi; seller hủy đơn. | Shipping, partner adapter |
| 7. Bàn giao | Đưa hàng cho hãng/đội giao nhận. | Tracking bắt đầu; trạng thái hiển thị đồng bộ buyer/seller. | Quét lấy hàng trễ; callback ngoài thứ tự; mã vận đơn sai. | Shipping, Notification |
| 8. Hoàn tất/ngoại lệ | Theo dõi giao, xử lý hoàn hàng/hủy/tranh chấp. | Hiển thị bước tiếp theo, bằng chứng và lịch sử; giữ audit. | Trách nhiệm và bồi hoàn chưa có chính sách. | Order, Shipping, Support |

## 4. Journey J-03 – Moderator xử lý tin bị báo cáo

**Persona:** P-04 Moderator  
**Mục tiêu:** xử lý nội dung theo chính sách và giữ dấu vết quyết định.

1. Nhận report với lý do, nội dung, người báo cáo và thời điểm.
2. Hệ thống chống report trùng, phân loại/ưu tiên theo policy.
3. Moderator xem listing, ảnh và lịch sử liên quan theo quyền; không cần truy cập raw payment data.
4. Chọn hành động: không vi phạm, yêu cầu chỉnh sửa, ẩn tạm, gỡ hoặc escalates; các lựa chọn còn cần policy approval.
5. Hệ thống ghi actor, lý do, timestamp, trạng thái trước/sau và case reference.
6. Thông báo cho người bị ảnh hưởng theo nội dung policy; hỗ trợ appeal nếu được quy định.
7. Nếu tin đang có order/reservation, hệ thống không tự xóa trạng thái transaction; cần xử lý liên service theo business rule.

**Điểm cần kiểm chứng:** tiêu chí cấm/hạn chế, thời gian phản hồi, bằng chứng lưu trữ, cơ chế appeal và quyền của moderator.

## 5. Journey J-04 – Nhân viên hỗ trợ xử lý đơn/giao hàng có vấn đề

**Persona:** P-05 Customer Support  
**Mục tiêu:** tìm nguyên nhân, giải thích trạng thái và chuyển đúng việc cho đúng chủ sở hữu.

| Bước | Nhân viên hỗ trợ | Hệ thống cần hỗ trợ | Kiểm soát |
|---|---|---|---|
| 1 | Tra cứu bằng order ID/case ID. | Tóm tắt order, payment reference, shipment và timeline. | Mask PII; giới hạn kết quả theo quyền/case. |
| 2 | Xác định trạng thái thật của từng domain. | Phân biệt trạng thái Order, Payment và Shipment, hiển thị nguồn và thời gian cập nhật. | Không để một màn hình gộp che mất tình trạng không nhất quán. |
| 3 | So sánh với thông điệp/callback đối tác. | Có correlation ID và trạng thái webhook/adapter. | Không hiển thị credential hoặc bí mật. |
| 4 | Thực hiện hoặc yêu cầu hành động. | Cung cấp workflow có quyền: mở case, yêu cầu hủy, xin refund, escalation. | Sensitive action cần policy, phê duyệt và idempotency. |
| 5 | Phản hồi buyer/seller và đóng case. | Lưu nội dung, quyết định, người xử lý và bước tiếp theo. | Audit trail; chỉ lưu dữ liệu cần thiết. |

## 6. Journey J-05 – Nhân viên vận hành khôi phục database

**Persona:** P-06 Người vận hành nền tảng  
**Mục tiêu:** khôi phục dữ liệu đúng phạm vi, xác minh tính toàn vẹn và đưa dịch vụ trở lại an toàn.

1. Nhận alert về database/backup/failure; xác định service, dữ liệu và mức ảnh hưởng.
2. Chọn runbook theo loại store: SQL Server, MongoDB/Atlas, S3, search projection hoặc cache.
3. Kiểm tra identity, role, encryption key, network và recovery point trước restore.
4. Restore vào môi trường cách ly hoặc resource mới theo kịch bản; không ghi đè nguồn đang lỗi khi chưa được duyệt.
5. Kiểm tra dữ liệu, quyền truy cập, record consistency, index và business invariants.
6. Khởi động dịch vụ phụ thuộc theo thứ tự; rebuild cache/search khi phù hợp.
7. Chuyển traffic hoặc thực hiện failover; theo dõi error rate và dữ liệu mới phát sinh.
8. Ghi RTO/RPO thực tế, nguyên nhân, data loss và hành động cải thiện.

**Điểm cần xác nhận:** RPO/RTO, ai có quyền phê duyệt restore, quy trình cross-account/Region và tần suất diễn tập.

## 6.1. Journey J-06 – Quản trị viên cập nhật danh mục và quy tắc kiểm duyệt

**Persona:** P-08 Quản trị viên kinh doanh (Business Administrator)  
**Mục tiêu:** Bổ sung danh mục phụ mới hoặc cập nhật danh sách từ khóa/hàng cấm niêm yết mà không làm gián đoạn các tin đăng hiện hữu.

1. **Đăng nhập và xác thực:** Quản trị viên đăng nhập vào Back-office Portal với quyền ROLE-05 (bắt buộc xác thực đa yếu tố MFA nếu được cấu hình).
2. **Truy cập quản lý phân loại (Taxonomy):** Mở giao diện cấu hình danh mục; hệ thống tải cây danh mục hiện hành từ Catalog Service.
3. **Thêm danh mục / thuộc tính:** Nhập tên danh mục, danh mục cha, định nghĩa bộ thuộc tính bắt buộc (ví dụ: đối với đồ điện tử cũ bắt buộc có trường "Dung lượng pin", "Mức độ trầy xước").
4. **Kiểm tra tác động:** Hệ thống hiển thị cảnh báo về số lượng tin đăng hiện hữu có thể bị ảnh hưởng nếu áp dụng quy tắc hồi tố.
5. **Cấu hình danh mục hàng cấm:** Thêm từ khóa hoặc nhóm mặt hàng bị cấm bán (theo yêu cầu từ Pháp lý / STK-16).
6. **Lưu và kích hoạt:** Admin ấn xuất bản cấu hình mới; hệ thống ghi log audit (Actor, Timestamp, Old Value, New Value) và phát sự kiện đồng bộ sang Listing Service và Search Service.
7. **Xác nhận:** Thông báo áp dụng thành công; người bán tạo tin mới trong danh mục này sẽ thấy các trường thuộc tính mới ngay lập tức.

## 7. Chỉ số trải nghiệm cần định nghĩa

Chưa gán mục tiêu số trước khi có baseline. Các chỉ số đề xuất để xem xét:

- Tỷ lệ hoàn tất tạo listing và tỷ lệ lỗi upload ảnh.
- Tỷ lệ checkout bị từ chối vì hết hàng sau tìm kiếm.
- Tỷ lệ order/payment trạng thái không xác định hoặc cần support.
- Thời gian từ order confirmed tới shipment created/first scan.
- Tỷ lệ cập nhật tracking đến được buyer/seller.
- Thời gian moderator/support xử lý case theo loại.
- Khả năng hoàn tất restore trong RTO và dữ liệu phục hồi trong RPO.

## 8. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo các journey buyer, seller, moderator, support và operations dưới dạng baseline cần validation. |
