# Problem Statement – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Bản phân tích ban đầu; vấn đề thị trường cần được xác minh |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | project-brief.md, scope-and-boundaries.md, stakeholder-register.md, user-needs-and-pain-points.md, business-objectives-and-success-metrics.md |

## 1. Mục đích

Tài liệu phát biểu vấn đề mà dự án ReMarket dự định phân tích, mô tả nhóm bị ảnh hưởng, hậu quả có thể xảy ra và cơ hội mà một nền tảng chợ trực tuyến có thể tạo ra. Đây là problem framing cho dự án thiết kế, không phải bằng chứng nghiên cứu thị trường.

## 2. Phát biểu vấn đề

Người mua và người bán hàng đã qua sử dụng có thể phải phối hợp qua nhiều kênh rời rạc để đăng sản phẩm, đánh giá tình trạng thực tế, kiểm tra món hàng còn khả dụng, thống nhất giao dịch và theo dõi thanh toán/giao nhận. Khi thông tin về tình trạng món hàng, độ tin cậy, trạng thái giao dịch và trách nhiệm xử lý ngoại lệ không tập trung hoặc không cập nhật đồng nhất, các bên có thể mất thời gian, bỏ lỡ giao dịch hoặc khó giải quyết khi có sự cố.

ReMarket được đề xuất như một hệ thống thương mại điện tử chuyên mua bán đồ đã qua sử dụng, nhằm phân tích cách tập trung các bước khám phá sản phẩm, quản lý tin đăng, đặt mua, thanh toán, giao nhận và hỗ trợ giao dịch. Dự án đồng thời nghiên cứu cách thiết kế các năng lực này trên kiến trúc Microservices và AWS Cloud với yêu cầu về khả năng mở rộng, tính sẵn sàng, bảo mật, hiệu suất và khả năng phục hồi dữ liệu.

Mức độ phổ biến, nguyên nhân và tác động định lượng của các vấn đề trên hiện **chưa được kiểm chứng**. Chúng phải được xác nhận qua phỏng vấn, quan sát, dữ liệu hoặc quyết định của chủ dự án trước khi dùng làm business case.

## 3. Bối cảnh và nhóm bị ảnh hưởng

| Nhóm | Vấn đề có thể gặp – giả thuyết | Tác động có thể có |
|---|---|---|
| Người mua | Khó so sánh tình trạng món hàng; không biết tin còn hiệu lực; thiếu thông tin chi phí hoặc trạng thái sau khi đặt. | Giảm niềm tin, bỏ dở hành trình, phát sinh khiếu nại hoặc mua nhầm kỳ vọng. |
| Người bán cá nhân | Mất công đăng tin, trả lời nhiều câu hỏi lặp lại, phải cập nhật tình trạng ở nhiều nơi. | Mất thời gian, bỏ lỡ người mua, bán trùng hoặc giữ tin đã bán. |
| Người bán có quy mô lớn hơn | Khó theo dõi nhiều tin, đơn và giao hàng nếu công cụ chỉ phù hợp với tác vụ đơn lẻ. | Tăng thao tác thủ công và sai sót; phân khúc này chưa xác nhận thuộc phạm vi sản phẩm. |
| Nhân viên kiểm duyệt/hỗ trợ | Có thể thiếu hồ sơ thống nhất về tin đăng, đơn, thanh toán, vận chuyển và lịch sử xử lý. | Điều tra chậm, quyết định không nhất quán hoặc khó truy vết trách nhiệm. |
| Nhóm vận hành kỹ thuật | Hệ thống nhiều service/database có thể gặp lỗi từng phần, dữ liệu lệch hoặc khôi phục không đồng bộ. | Gián đoạn nghiệp vụ, tồn tại giao dịch dở dang hoặc khôi phục vượt thời gian kỳ vọng. |
| Đối tác thanh toán/giao nhận | Hợp đồng API, callback, timeout và trạng thái khác nhau giữa nhà cung cấp. | Tích hợp phức tạp, cập nhật chậm hoặc cần đối soát thủ công. |

Các dòng trên là giả thuyết để thiết kế nghiên cứu. Không đại diện cho kết quả phỏng vấn hay mức độ nghiêm trọng đã đo được.

## 4. Nguyên nhân gốc có thể có

1. Thông tin tin đăng và giao dịch phân tán giữa các kênh, bảng tính hoặc trao đổi trực tiếp.
2. Không có trạng thái chuẩn dùng chung cho vòng đời tin đăng, giữ hàng, thanh toán, đơn hàng và giao nhận.
3. Hàng đã qua sử dụng thường có khác biệt về tình trạng, phụ kiện, nguồn gốc và số lượng; form chung có thể không biểu đạt đủ.
4. Tìm kiếm có thể dùng dữ liệu trễ so với trạng thái khả dụng thật của listing.
5. Quy trình thanh toán và giao hàng phụ thuộc bên ngoài, có thể có timeout, callback trùng hoặc trạng thái chưa rõ.
6. Quy trình kiểm duyệt, báo cáo, hoàn tiền, hủy và tranh chấp chưa được xác định ở cấp chính sách.
7. Khi nhiều dịch vụ và kho dữ liệu được tách biệt, thiếu observability, backup, restore drill hoặc quyền sở hữu dữ liệu rõ ràng có thể gây khó phục hồi.

Các nguyên nhân này cần được kiểm tra với nhóm người dùng và người sở hữu nghiệp vụ; không mặc định tất cả đều đúng với mọi thị trường.

## 5. Cơ hội và kết quả mong muốn

Dự án phân tích nên xác định giải pháp có thể:

- Cho người bán một quy trình nhất quán để mô tả, đăng và quản lý món đồ.
- Giúp người mua khám phá món phù hợp và xác minh lại khả dụng trước bước tạo giao dịch.
- Tạo trạng thái đơn, thanh toán và giao hàng dễ hiểu, có lịch sử và xử lý ngoại lệ.
- Hỗ trợ kiểm duyệt, hỗ trợ khách hàng và kiểm soát quyền theo trách nhiệm.
- Thiết kế các service sở hữu dữ liệu rõ ràng; tránh phụ thuộc truy cập database chéo service.
- Đánh giá lựa chọn SQL Server, MongoDB, cache, load balancer, Amazon S3 và các dịch vụ AWS dựa trên access pattern, tải, tính nhất quán, khôi phục và chi phí.
- Xác định giải pháp backup/restore và disaster recovery có thể kiểm chứng, không nhầm tính sẵn sàng nhiều AZ với backup.
- Trình bày rõ đánh đổi giữa độ phức tạp vận hành, độ sẵn sàng, mức khôi phục, bảo mật và ngân sách.

Đây là mục tiêu phân tích/thiết kế; không cam kết sản phẩm thương mại hoặc tích hợp thật.

## 6. Phạm vi vấn đề

### Trong phạm vi phân tích

- Khám phá, đăng bán và quản lý tin đồ đã qua sử dụng.
- Tìm kiếm, kiểm tra khả dụng, tạo đơn và mô hình hóa giữ hàng.
- Điều phối thanh toán, giao hàng, thông báo và trạng thái giao dịch ở mức quy trình.
- Quản trị, kiểm duyệt, hỗ trợ và quyền truy cập sơ bộ.
- Dữ liệu nghiệp vụ và các luồng đồng bộ/bất đồng bộ giữa service.
- AWS cloud, database SQL Server/MongoDB, cache, S3, load balancing, backup, restore và phục hồi ở mức thiết kế.

### Chưa được xác nhận hoặc ngoài phạm vi hiện tại

- Triển khai production, vận hành sàn thật, cam kết SLA hoặc lưu lượng người dùng.
- Quốc gia/thị trường, ngôn ngữ, tiền tệ, ngành hàng bị cấm và quy định áp dụng.
- Ví điện tử nội bộ, trung gian ký quỹ (escrow) đa tầng; giai đoạn 1 chỉ xem xét tích hợp cổng thanh toán để thu tiền và mô hình hóa quy trình đối soát giải ngân cơ bản (payout batch) cho người bán khi đơn hoàn tất.
- Phí nền tảng, hoa hồng, quảng cáo, thuế hoặc mô hình doanh thu chi tiết.
- Nhà cung cấp thanh toán, giao vận, email/SMS, hãng cụ thể hoặc hợp đồng đã ký.
- KYC/AML, quy trình xác minh người bán, chính sách đổi trả, hoàn tiền và tranh chấp.
- Mục tiêu số cho KPI, SLA, RPO, RTO, retention, ngân sách AWS và quy mô dữ liệu.

## 7. Ràng buộc thiết kế có liên quan

1. Kiến trúc hướng Microservices là trọng tâm nghiên cứu nhưng cần chứng minh ranh giới theo năng lực nghiệp vụ; không tách service chỉ vì có một màn hình riêng.
2. SQL Server và MongoDB là công nghệ dữ liệu được ưu tiên xem xét; chưa quyết định phiên bản, hosting hay service nào sở hữu từng datastore ngoài baseline tài liệu 00.
3. Cache là dữ liệu dẫn xuất/tạm thời theo thiết kế; không được mặc định làm nguồn chuẩn cho tồn kho, đơn hàng hoặc thanh toán.
4. Amazon S3 được xem xét cho ảnh/tệp; cơ sở dữ liệu nên lưu metadata và object key, không lưu binary lớn không cần thiết.
5. Search index, cache và báo cáo có thể hội tụ trễ; luồng checkout phải xác nhận trạng thái với nguồn dữ liệu có thẩm quyền.
6. Backup cần có khả năng phục hồi và kiểm chứng. Bản backup không có quy trình truy cập, khóa mã hóa, kiểm thử và đối soát dữ liệu chưa đủ để chứng minh khả năng phục hồi.
7. Multi-AZ/HA giảm một số dạng gián đoạn nhưng không thay thế bản sao lưu độc lập, có bảo vệ trước xóa nhầm, lỗi logic, compromise tài khoản hoặc mất toàn vùng.

## 8. Giả thuyết và cách kiểm chứng

| ID | Giả thuyết | Bằng chứng cần thu thập | Nhóm cần xác nhận |
|---|---|---|---|
| HYP-PA-01 | Buyer thiếu thông tin tình trạng và độ tin cậy để quyết định mua đồ cũ. | Phỏng vấn, quan sát tác vụ tìm và so sánh listing. | Buyer, Product Owner |
| HYP-PA-02 | Seller cần hướng dẫn chuẩn để chụp ảnh và mô tả tình trạng. | Quan sát tạo listing; phân tích câu hỏi lặp lại. | Seller, Product Owner |
| HYP-PA-03 | Bán trùng là rủi ro đáng kể khi một món đồ có một đơn vị. | Xác nhận mô hình hàng hóa, walkthrough checkout cạnh tranh. | Seller, Product Owner, Architect |
| HYP-PA-04 | Người dùng cần timeline thống nhất qua order, payment và shipping. | Phỏng vấn support/buyer; dựng tình huống lỗi. | Support, buyer |
| HYP-PA-05 | Cần AWS multi-AZ và chiến lược backup/restore cụ thể cho mục tiêu dự án. | Xác định tác động downtime/mất dữ liệu, chi phí và khả năng vận hành. | Sponsor, Cloud/DBA, Security |
| HYP-PA-06 | Seller chuyên nghiệp cần bulk workflow. | Xác nhận phân khúc và quy mô tin/đơn mục tiêu. | Sponsor, seller |

## 9. Tiêu chí để chốt problem statement

- Nhóm buyer/seller đại diện được xác định và tham vấn.
- Có evidence source, ngày, phạm vi mẫu và giới hạn nghiên cứu.
- Vấn đề và tác động được phân biệt khỏi giải pháp mong muốn.
- Mục tiêu kinh doanh có owner và phương pháp đo.
- Các giả định về mô hình marketplace, trách nhiệm thanh toán/giao hàng và danh mục hàng được ghi rõ.
- Sponsor/Product Owner phê duyệt phạm vi phân tích.

## 10. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo problem framing ở dạng giả thuyết, gắn với phạm vi ReMarket đã nêu. |
