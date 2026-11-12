# User Needs and Pain Points – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.0 |
| Trạng thái | Backlog nhu cầu giả thuyết; chưa xác minh qua nghiên cứu người dùng |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | user-personas.md, user-journeys.md, stakeholder-register.md, functional-requirements.md, non-functional-requirements.md |

## 1. Mục đích

Tập hợp nhu cầu và điểm khó có thể gặp của các nhóm sử dụng ReMarket để chuẩn bị phỏng vấn, phân tích và truy vết sang yêu cầu. Đây không phải danh sách đã được người dùng xác nhận.

## 2. Trạng thái bằng chứng và ưu tiên

- Tất cả dòng có trạng thái **Giả thuyết** cho đến khi có nguồn phỏng vấn, quan sát, khảo sát hoặc quyết định nghiệp vụ.
- MoSCoW dưới đây là ưu tiên ban đầu để thảo luận, không phải backlog đã được phê duyệt.
- Mỗi nhu cầu cần được xác nhận với stakeholder phù hợp trước khi chuyển thành requirement.
- Không ghi KPI số cụ thể nếu chưa có baseline và nguồn đo.

## 3. Nhu cầu người mua

| ID | Nhu cầu | Điểm khó hiện giả định | Ưu tiên sơ bộ | Năng lực/quality liên quan | Cách kiểm chứng |
|---|---|---|---|---|---|
| NEED-BUY-01 | Tôi muốn xem tình trạng, lỗi, hao mòn và ảnh thật của món hàng trước khi quyết định. | Tin đăng thiếu chuẩn hoặc ảnh không đủ góc nhìn. | Must | Listing condition, image media, disclosure rules | Phỏng vấn và review ví dụ listing |
| NEED-BUY-02 | Tôi muốn tìm/lọc sản phẩm theo danh mục và thuộc tính liên quan. | Tìm kiếm thủ công tốn thời gian; bộ lọc không phản ánh mặt hàng cũ. | Must | Search & Discovery, category/attribute model | Task walkthrough |
| NEED-BUY-03 | Tôi muốn biết món còn mua được không trước checkout. | Search index có thể trễ; hai buyer có thể đồng thời checkout. | Must | Listing source of truth, reservation, consistency | Scenario walkthrough/concurrency requirement |
| NEED-BUY-04 | Tôi muốn thấy giá và phí dự kiến trước khi đặt. | Phí giao hoặc điều kiện phát sinh không rõ. | Should | Order/checkout, shipping quote, price snapshot | Interview; payment/shipping policy decision |
| NEED-BUY-05 | Tôi muốn nhận trạng thái đơn, thanh toán và giao hàng dễ hiểu. | Payment callback/ship tracking có thể chậm hoặc dùng thuật ngữ hãng. | Must | Order, Payment, Shipping, Notification | Journey test |
| NEED-BUY-06 | Khi giao dịch lỗi, tôi muốn biết tiền/đơn/món hàng đang ở trạng thái nào và cách yêu cầu hỗ trợ. | Pending không rõ, bị thu tiền nhưng order chưa xác nhận, không biết nơi liên hệ. | Must | Saga/reconciliation, support case, audit | Failure scenario walkthrough |
| NEED-BUY-07 | Tôi muốn đánh giá hoặc báo cáo vấn đề sau giao dịch. | Không có quy trình phản hồi hoặc không biết ai xử lý. | Should | Reviews, Trust/Moderation, Support | Interview and policy review |
| NEED-BUY-08 | Tôi muốn dữ liệu cá nhân và địa chỉ chỉ được dùng trong phạm vi giao dịch cần thiết. | Lo ngại lộ thông tin hoặc bị truy cập sai mục đích. | Must | Privacy, RBAC/ABAC, data retention | Security/privacy review |

## 4. Nhu cầu người bán

| ID | Nhu cầu | Điểm khó hiện giả định | Ưu tiên sơ bộ | Năng lực/quality liên quan | Cách kiểm chứng |
|---|---|---|---|---|---|
| NEED-SELL-01 | Tôi muốn tạo tin đăng nhanh với hướng dẫn rõ về ảnh, tình trạng, giá và mô tả. | Không chắc trường nào bắt buộc hoặc cách đánh giá condition. | Must | Seller Center, Listing, validation | Observe listing task |
| NEED-SELL-02 | Tôi muốn sửa, ẩn, đánh dấu bán hoặc gỡ tin theo trạng thái hợp lệ. | Tin đã bán vẫn có thể hiện; sửa tin ảnh hưởng order cũ. | Must | Listing lifecycle, order snapshot, search projection | State transition review |
| NEED-SELL-03 | Tôi muốn một món hàng không bị bán cho nhiều người cùng lúc. | Hai buyer có thể gửi checkout đồng thời. | Must | Atomic reservation, idempotency, order coordination | Failure/concurrency scenario |
| NEED-SELL-04 | Tôi muốn biết đơn đã được xác nhận và cần làm gì tiếp theo. | Seller có thể nhầm payment pending với paid/confirmed. | Must | Seller order view, event notification | Journey validation |
| NEED-SELL-05 | Tôi muốn quy trình giao hàng cho biết hãng, mã vận đơn, hạn xử lý và trạng thái. | Carrier APIs/statuses khác nhau; seller không rõ bước cần làm. | Should | Shipping adapters, canonical states, notifications | Provider/seller walkthrough |
| NEED-SELL-06 | Tôi muốn bảo vệ địa chỉ, số điện thoại và thông tin cá nhân. | Seller có thể phải chia sẻ PII để bàn giao hàng nhưng không muốn bị lộ rộng. | Must | Profile, shipping access, masking/retention | Privacy interview |
| NEED-SELL-07 | Nếu bán chuyên nghiệp, tôi muốn quản lý nhiều tin/đơn hiệu quả. | Tác vụ đơn lẻ có thể không đủ khi số listing tăng. | Could / needs scope decision | Bulk operations, reporting, role delegation | Confirm seller segment first |

## 5. Nhu cầu moderator và hỗ trợ

| ID | Nhu cầu | Điểm khó hiện giả định | Ưu tiên sơ bộ | Năng lực/quality liên quan | Cách kiểm chứng |
|---|---|---|---|---|---|
| NEED-OPS-01 | Moderator muốn xem report, listing và lịch sử quyết định trong một case. | Thông tin phân tán; report trùng; thiếu lý do hành động. | Should | Moderation queue, audit, case lifecycle | Moderation workshop |
| NEED-OPS-02 | Moderator muốn ẩn/gỡ nội dung nhưng không làm sai trạng thái order/payment. | Moderation và giao dịch có vòng đời khác nhau. | Must | Cross-service business rules, saga/compensation | Exception workshop |
| NEED-SUP-01 | Support muốn tìm một giao dịch và xem timeline Order/Payment/Shipping. | Nhiều dịch vụ; khó xác định nguồn lỗi hoặc bên đang xử lý. | Should | Correlation IDs, case view, observability | Support scenario walkthrough |
| NEED-SUP-02 | Support muốn hành động nhạy cảm theo quyền, có phê duyệt và audit. | Refund/cancel có thể gây tác động tiền và dữ liệu. | Must | RBAC, approval workflow, audit | Role review |
| NEED-SUP-03 | Support chỉ xem PII/payment metadata cần thiết cho case. | Rủi ro xem quá rộng hoặc ghi log dữ liệu nhạy cảm. | Must | Data masking, access logging, retention | Privacy/security review |

## 6. Nhu cầu đối tác và vận hành kỹ thuật

| ID | Nhóm | Nhu cầu | Điểm khó/rủi ro hiện giả định | Ưu tiên sơ bộ | Cách kiểm chứng |
|---|---|---|---|---|---|
| NEED-CAR-01 | Carrier partner | Request/callback ổn định, có mapping trạng thái và cơ chế xử lý request trùng. | API, callback và trạng thái khác nhau giữa hãng. | Must nếu tích hợp thật | API/sandbox contract review |
| NEED-PAY-01 | Payment provider | Payment/refund có correlation, idempotency và reconciliation. | Timeout không xác định; duplicate callback/request. | Must nếu payment online | Provider API and failure review |
| NEED-OPS-01 | DBA/Cloud/SRE | Backup thành công, cảnh báo lỗi, restore có thể kiểm chứng và đạt RPO/RTO đã duyệt. | Backup tồn tại nhưng restore chưa thử; shared DB làm restore scope lớn. | Must | Recovery tabletop/restore drill |
| NEED-OPS-02 | Cloud/Security | Quyền tối thiểu, key/secret recovery và backup copy tách quyền phù hợp. | Account compromise hoặc mất quyền/kms key có thể cản restore. | Must | Threat modeling and access review |
| NEED-OPS-03 | Development/Ops | Quan sát xuyên service và biết failure point trong listing→order→payment→shipping. | Logs/correlation không thống nhất; retry có thể tạo side effects. | Must | Architecture/failure review |
| NEED-OPS-04 | Sponsor/Project Owner | Biết chi phí AWS, mức độ phức tạp và trade-off trước quyết định triển khai. | Dịch vụ nhiều làm tăng chi phí/công sức không dự tính. | Should | Cost model and architecture review |

## 7. Phân biệt need, requirement và solution

- **Need:** mục tiêu/ngữ cảnh của nhóm người dùng, ví dụ buyer cần biết listing còn khả dụng.
- **Requirement:** điều kiện cụ thể có thể kiểm tra, ví dụ hệ thống phải xác nhận lại khả dụng khi checkout.
- **Solution:** cách hiện thực, ví dụ Listing Service dùng conditional atomic update hoặc một lựa chọn tương đương.
- Không viết tên AWS/database như nhu cầu người dùng. SQL Server, MongoDB, cache, load balancer và S3 là lựa chọn công nghệ/kiến trúc cần map vào requirements, không phải need của buyer/seller.

## 8. Quy trình xác nhận và truy vết

1. Gắn nguồn bằng interview ID/workshop/date hoặc quyết định business owner.
2. Phân loại evidence: confirmed by user, stakeholder decision, observed, assumed, disputed.
3. Ghi persona/journey và requirement liên quan.
4. Chỉ đổi priority sau khi Product Owner/decision owner xác nhận.
5. Nếu thay đổi need, đánh giá tác động lên service boundaries, data, security, AWS cost, backup/restore và NFR.
6. Giữ lịch sử thay đổi; không ghi xóa bỏ giả thuyết mà không có decision note.

## 9. Kế hoạch nghiên cứu đề xuất

- Phỏng vấn buyer về trust, condition, search và transaction exceptions.
- Quan sát seller tạo listing trên mobile và chuẩn bị giao món.
- Walkthrough với moderator/support qua report, cancellation, refund và carrier failure.
- Workshop với DBA/Cloud/Security cho SQL Server, MongoDB hosting, cache, S3, backup/restore.
- Review API/sandbox của provider trước khi coi integration need là khả thi.
- Sau mỗi phiên, cập nhật evidence source, confidence, priority và open questions.

## 10. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo nhu cầu giả thuyết cho buyer, seller, operations và đối tác. |
