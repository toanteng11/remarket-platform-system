# Value Streams – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.0 |
| Trạng thái | Value stream đề xuất; kết quả và scope cần xác nhận |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | business-capability-map.md, business-objectives-and-success-metrics.md, to-be-processes.md, user-journeys.md |

## 1. Khái niệm và mục đích

Value stream mô tả các giai đoạn cần hoàn tất để một người nhận được kết quả có giá trị. Tài liệu này đặt trọng tâm vào kết quả buyer/seller và vận hành, không phải luồng gọi API hay sơ đồ triển khai cloud.

Tất cả value stream bên dưới là mục tiêu phân tích cho một marketplace; chưa có bằng chứng vận hành ReMarket. Các stage có thể có nhánh tùy phương thức thanh toán, loại sản phẩm, vùng giao hàng và policy.

## 2. Danh mục value stream

| ID | Value stream | Bắt đầu | Kết thúc | Người nhận giá trị | Mục tiêu |
|---|---|---|---|---|---|
| VS-01 | Buyer khám phá tới nhận món hàng | Buyer có nhu cầu tìm món đồ cũ | Buyer nhận hàng, giao dịch kết thúc hoặc case được chuyển xử lý | Buyer; seller nhận giao dịch thành công | OBJ-01, OBJ-03, OBJ-04 |
| VS-02 | Seller đưa món hàng lên tới hoàn tất giao dịch | Seller quyết định bán món hàng | Món hàng được giao/đóng giao dịch hoặc xử lý ngoại lệ | Seller; buyer nhận hàng phù hợp | OBJ-02, OBJ-03, OBJ-04 |
| VS-03 | Tín hiệu rủi ro tới quyết định có thể giải trình | Report/signal được ghi nhận | Có quyết định, biện pháp và thông báo phù hợp | Người dùng, moderator, marketplace | OBJ-05 |
| VS-04 | Sự cố dữ liệu tới khôi phục hoạt động | Alert/incident/restore request | Luồng nghiệp vụ được xác nhận an toàn trở lại | Buyer, seller, business owner, ops | OBJ-06, OBJ-07 |
| VS-05 | Nhu cầu thay đổi tới quyết định được truy vết | Câu hỏi/yêu cầu mới được nêu | Quyết định, yêu cầu và tài liệu được cập nhật | Project team/sponsor | OBJ-08 |

## 3. VS-01: Buyer khám phá tới nhận món hàng

| Stage | Hành động chính | Tác nhân/năng lực | Kết quả cần cho stage tiếp | Rủi ro/handoff |
|---|---|---|---|---|
| 1. Khám phá | Tìm từ khóa, danh mục, bộ lọc hoặc xem gợi ý. | Buyer; Search & Discovery | Tập listing liên quan có thông tin đủ dùng. | Search projection có thể trễ; relevance chưa có baseline. |
| 2. Đánh giá | Xem ảnh, tình trạng, giá, seller, vị trí và điều kiện giao. | Buyer; Catalog & Listing/Profile | Buyer hiểu món hàng và điểm chưa chắc chắn. | Listing thiếu disclosure; không được ngụ ý verified nếu chưa kiểm định. |
| 3. Kiểm tra khả dụng | Hệ thống xác nhận trạng thái listing trước khi checkout. | Listing/Order | Kết luận có thể tiếp tục hoặc giải thích không khả dụng. | Hai buyer cạnh tranh; stale search/cache. |
| 4. Xác nhận giao dịch | Buyer rà giá/phí, địa chỉ và đồng ý tạo order. | Buyer; Order & Checkout | Order tham chiếu duy nhất và snapshot. | Phí, seller split và reservation policy còn mở. |
| 5. Thanh toán | Buyer thanh toán/được ghi nhận phương thức đã duyệt. | Payment; provider | Trạng thái đã xác định hoặc pending có hướng dẫn. | Timeout không đồng nghĩa thất bại; callback trùng. |
| 6. Giao nhận | Seller/partner bàn giao, tracking được cập nhật. | Shipping & Fulfillment | Buyer biết tiến độ và bước tiếp theo. | Carrier outage, mất tracking, PII exposure. |
| 7. Kết thúc | Giao dịch đạt định nghĩa completed, review/case mở nếu phù hợp. | Order, Reviews, Support | Buyer có trạng thái cuối và kênh hỗ trợ. | Return/refund/dispute window chưa duyệt. |

**Chỉ số hỗ trợ:** MET-BUY-01…05, MET-TRUST-01.  
**Quy tắc trọng yếu:** BR-ORD-001…010, BR-PAY-001…009, BR-SHP-001…005.  
**Tiêu chí giá trị:** buyer có thể xác định món còn khả dụng, chi phí và trạng thái giao dịch; tiêu chí đo số cần Product Owner duyệt.

## 4. VS-02: Seller đưa món hàng lên tới hoàn tất giao dịch

| Stage | Hành động chính | Tác nhân/năng lực | Kết quả cần cho stage tiếp | Rủi ro/handoff |
|---|---|---|---|---|
| 1. Chuẩn bị món | Chọn danh mục, xác định tình trạng, chụp ảnh, giá và điều kiện giao. | Seller; Seller Center | Nội dung có đủ thông tin cho buyer. | Định nghĩa condition/taxonomy không rõ. |
| 2. Tạo listing | Nhập thông tin, media, lưu draft và submit. | Seller; Listing/Media | Listing hợp lệ, gắn seller và ID. | Upload fail, duplicate, nội dung bị chặn. |
| 3. Review/công bố | Hệ thống kiểm tra; moderation nếu policy yêu cầu. | Catalog/Moderation | Listing published hoặc có lý do yêu cầu chỉnh sửa. | Queue delay, appeal chưa xác định. |
| 4. Tiếp nhận nhu cầu | Buyer tìm thấy listing; seller nhận order/notification. | Search, Notification, Seller | Seller biết trạng thái và nghĩa vụ tiếp theo. | Search index cập nhật trễ. |
| 5. Chuẩn bị fulfill | Seller xác nhận/đóng gói/bàn giao theo cách thức được duyệt. | Seller; Shipping | Shipment được tạo hoặc bàn giao được ghi nhận. | Ai mua nhãn/phí; deadline chưa chốt. |
| 6. Theo dõi và kết thúc | Cập nhật tracking; xử lý ngoại lệ; giao dịch hoàn tất. | Shipping, Order, Support | Seller biết kết quả và nghĩa vụ đã xong. | Provider callback sai/thiếu; dispute. |
| 7. Quản lý sau bán | Review, refund/case hoặc lưu lịch sử giao dịch. | Reviews/Support | Vấn đề hậu giao dịch được xử lý theo policy. | Retention và refund policy. |

**Chỉ số hỗ trợ:** MET-SELL-01…05, MET-TRUST-01.  
**Giá trị cần chứng minh:** listing chính xác, seller hiểu bước tiếp theo, trạng thái sold/reserved không gây nhận thêm order.

## 5. VS-03: Tín hiệu rủi ro tới quyết định có thể giải trình

1. **Ghi nhận:** buyer, seller hoặc hệ thống phát hiện listing, tài khoản hay giao dịch đáng ngờ.
2. **Phân loại:** xác định loại case, phạm vi và mức độ ưu tiên; cấp case ID.
3. **Thu thập:** reviewer xem listing/order/payment/shipping/evidence đúng quyền.
4. **Đánh giá:** áp dụng policy; xử lý xung đột quyền lợi hoặc chuyển cấp.
5. **Quyết định:** ghi reason code, người thực hiện, thời điểm, đối tượng và hành động.
6. **Thực hiện:** thay đổi listing/account/order qua service sở hữu nghiệp vụ, có kiểm soát.
7. **Thông báo và appeal:** thông báo phù hợp, bảo vệ dữ liệu và cung cấp kênh phản hồi theo policy.
8. **Đóng và học hỏi:** lưu kết quả/metrics, phát hiện pattern cần cập nhật hướng dẫn hoặc kiểm soát.

**Rủi ro:** thiếu policy; truy cập PII quá mức; moderation làm sai order/payment; quyết định không có bằng chứng; report abuse.  
**Chỉ số hỗ trợ:** MET-TRUST-02/03.  
**Chưa chốt:** taxonomy report, severity, SLA, quyền phê duyệt, appeal và retention.

## 6. VS-04: Sự cố dữ liệu tới khôi phục hoạt động

1. Alert hoặc người dùng báo sự cố; incident owner xác nhận phạm vi.
2. Bảo vệ dữ liệu khỏi ghi sai thêm và xác định nguồn dữ liệu bị ảnh hưởng.
3. Chọn mục tiêu phục hồi đã được duyệt, nguồn backup phù hợp và môi trường restore.
4. Kiểm tra danh tính/quyền, encryption key, phiên bản schema và tính hợp lệ của bản backup.
5. Restore datastore/object theo từng hướng dẫn; tránh ghi đè không kiểm soát.
6. Đối soát order/payment/listing/shipment giữa các owner; xử lý event/outbox/queue theo quy trình.
7. Dựng lại search/cache từ nguồn chuẩn và kiểm tra freshness.
8. Chạy checklist integrity và giao dịch mẫu; business owner + technical owner phê duyệt mở lại.
9. Theo dõi lỗi, backlog, tác động người dùng, RPO/RTO thực tế; hoàn tất post-incident review.
10. Cập nhật runbook, quyền, lịch backup, cảnh báo và bài diễn tập.

**Tiêu chí thành công:** dữ liệu đúng và nhất quán đủ để tiếp tục nghiệp vụ, chủ sở hữu chấp thuận, khoảng mất dữ liệu/thời gian đo được so với mục tiêu. Chỉ có “backup job thành công” không phải điều kiện kết thúc.

**Chỉ số hỗ trợ:** MET-PLAT-04…07.  
**Quyết định cần có:** RPO/RTO từng datastore và luồng; retention; account/region; quyền restore; người phê duyệt.

## 7. VS-05: Nhu cầu thay đổi tới quyết định được truy vết

1. Ghi nhận nhu cầu, nguồn và người đề xuất.
2. Liên kết với problem/need/value stream/rule hiện có.
3. Đánh giá tác động nghiệp vụ, người dùng, service, SQL Server/MongoDB/S3/cache, bảo mật, backup/restore và chi phí.
4. Đưa ra options, trade-off, rủi ro và câu hỏi còn thiếu.
5. Chủ sở hữu có thẩm quyền chọn phương án hoặc hoãn/từ chối.
6. Ghi quyết định, điều kiện, approver, ngày, phiên bản và tài liệu bị ảnh hưởng.
7. Cập nhật requirement/backlog/architecture; kiểm tra consistency và lịch sử.

## 8. Điểm bàn giao xuyên vai trò

| Handoff | Giao từ → tới | Dữ liệu tối thiểu | Kiểm soát |
|---|---|---|---|
| Listing submit | Seller → Listing/Moderation | Seller ID, listing ID, category, attributes, media references | Validation, ownership |
| Checkout | Buyer → Order/Listing | Buyer ID, listing ID, price/version, address reference, idempotency | Revalidate availability |
| Payment result | Provider → Payment/Order | Provider reference, event ID, amount/currency, signature, timestamp | Verify/idempotent |
| Shipment event | Carrier → Shipping/Order | Tracking reference, canonical event, source timestamp | Authenticate/deduplicate/order |
| Support case | User/System → Support | Case ID, reason, object references, evidence permission | Least privilege/audit |
| Recovery approval | DBA/Ops → Business owner | Restore point, scope, integrity results, measured RPO/RTO | Dual control where required |

## 9. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo value stream đề xuất để nối mục tiêu, năng lực, quy trình và chỉ số. |
