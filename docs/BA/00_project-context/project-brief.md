# Project Brief – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Tên dự án | ReMarket |
| Loại tài liệu | Project Brief |
| Phiên bản | 1.2 |
| Trạng thái | Bản nháp để thống nhất định hướng |
| Ngày cập nhật | 11/11/2026 |
| Đối tượng đọc | Chủ dự án, BA, kiến trúc sư hệ thống, lập trình viên, kiểm thử viên và người đánh giá dự án |

## 1. Tóm tắt dự án

**ReMarket là dự án phân tích và thiết kế một hệ thống thương mại điện tử chuyên mua bán đồ đã qua sử dụng.** Dự án tập trung nghiên cứu kiến trúc **Microservices**, cách thiết kế một **hệ thống phân tán**, và xây dựng giải pháp hạ tầng trên **AWS Cloud**. Thiết kế hướng đến khả năng mở rộng, tính sẵn sàng cao, hiệu suất tốt, bảo mật và khả năng vận hành.

Ở phạm vi hiện tại, ReMarket được xem là một dự án phân tích, thiết kế và định hình giải pháp kỹ thuật. Tài liệu này không giả định rằng nền tảng đã được đưa vào vận hành thương mại, đang xử lý giao dịch thật, hoặc đã có người dùng thực tế. Mức độ triển khai thử nghiệm hay triển khai môi trường thật cần được xác định riêng trong phạm vi dự án.

## 2. Bối cảnh

Mua bán đồ đã qua sử dụng có những đặc điểm nghiệp vụ cần được phản ánh ngay từ khâu phân tích và thiết kế:

- Mỗi sản phẩm có thể là món hàng đơn chiếc, có tình trạng, lịch sử sử dụng và mức độ hao mòn khác nhau.
- Người bán cần mô tả tình trạng và cung cấp hình ảnh đủ rõ để người mua đánh giá trước khi đặt hàng.
- Một tin đăng có thể được nhiều người xem hoặc đặt mua cùng lúc; hệ thống cần tránh xác nhận nhiều đơn cho cùng một món hàng.
- Trạng thái tin đăng, đơn hàng, thanh toán và giao nhận liên quan với nhau nhưng có thể được xử lý bởi các thành phần riêng biệt.
- Một thành phần hoặc dịch vụ phụ thuộc có thể chậm, lỗi hoặc tạm thời không truy cập được; thiết kế cần xác định cách hệ thống phản hồi và phục hồi.

Các điểm trên là bối cảnh để phân tích yêu cầu và kiến trúc. Chúng không khẳng định trước giải pháp chi tiết hoặc quy mô kinh doanh của nền tảng.

## 3. Vấn đề và cơ hội cần giải quyết

Dự án cần xác định cách tổ chức một hệ thống mua bán hàng cũ sao cho các nghiệp vụ chính được phân tách hợp lý, dữ liệu có chủ sở hữu rõ ràng và các thành phần có thể giao tiếp an toàn trong môi trường phân tán. Thiết kế cần đặc biệt xem xét tính nhất quán khi giữ hàng và tạo đơn, xử lý lỗi giữa các bước, khả năng mở rộng khi lưu lượng tăng, và tính sẵn sàng khi một thành phần gặp sự cố.

Cơ hội của dự án là xây dựng một giải pháp tham chiếu có thể trình bày rõ mối liên hệ giữa yêu cầu nghiệp vụ, ranh giới dịch vụ, luồng dữ liệu, cơ chế giao tiếp và hạ tầng AWS. Qua đó, các quyết định kiến trúc có thể được giải thích và đánh giá dựa trên yêu cầu thay vì chỉ dựa trên lựa chọn công nghệ.

## 4. Tầm nhìn

Định hình một thiết kế có thể mở rộng cho nền tảng mua bán đồ đã qua sử dụng, trong đó các nghiệp vụ được chia thành những dịch vụ có trách nhiệm rõ ràng, có khả năng giao tiếp và phục hồi trong môi trường phân tán, đồng thời có kiến trúc hạ tầng AWS phù hợp với các mục tiêu về hiệu suất, tính sẵn sàng, bảo mật và chi phí.

## 5. Mục tiêu dự án

### 5.1. Mục tiêu phân tích nghiệp vụ

- Xác định các nhóm người dùng, nhu cầu, mục tiêu và hành trình chính.
- Làm rõ phạm vi nghiệp vụ cốt lõi, quy tắc quản lý tin đăng hàng cũ và vòng đời giao dịch.
- Mô tả các luồng quan trọng như đăng bán, tìm kiếm, đặt mua, giữ hàng, thanh toán, giao nhận, hủy đơn và hỗ trợ sau giao dịch.
- Tạo cơ sở để truy vết yêu cầu từ mục tiêu dự án đến thiết kế và các tiêu chí nghiệm thu.

### 5.2. Mục tiêu thiết kế hệ thống

- Xác định ranh giới dịch vụ Microservices theo nghiệp vụ và quyền sở hữu dữ liệu.
- Thiết kế cách các dịch vụ giao tiếp đồng bộ và bất đồng bộ, cùng cách xử lý lỗi, gửi lặp, timeout và dữ liệu cập nhật không đồng thời.
- Xem xét các yêu cầu về mở rộng, tính sẵn sàng, hiệu suất, bảo mật, khả năng quan sát và khả năng vận hành.
- Đề xuất kiến trúc hạ tầng trên AWS đáp ứng các yêu cầu đã xác định; lựa chọn dịch vụ AWS cụ thể sẽ được ghi nhận trong tài liệu kiến trúc tương ứng.
- Đưa ra các tiêu chí hoặc chỉ số đánh giá chất lượng ở tài liệu yêu cầu phi chức năng. Các ngưỡng cụ thể như thời gian phản hồi, tải đồng thời, SLA và ngân sách chưa được xác định trong Project Brief này.

### 5.3. Ưu tiên thiết kế cơ sở dữ liệu và khôi phục dữ liệu

Thiết kế database và bảo vệ dữ liệu là trọng tâm kỹ thuật của dự án, cần được phân tích chi tiết ở cấp yêu cầu và kiến trúc. Stack ưu tiên là SQL Server, MongoDB, cache, load balancer và Amazon S3 theo mục 7.3. Hosting, version/edition, loại cache/load balancer và dịch vụ AWS quản lý cụ thể vẫn cần đánh giá theo access pattern, RPO/RTO, chi phí và năng lực vận hành.

Phạm vi phân tích database cần bao gồm:

- Quyền sở hữu dữ liệu theo dịch vụ, mô hình dữ liệu logic và vật lý, các quan hệ, khóa, ràng buộc và quy tắc toàn vẹn.
- Đánh giá loại database theo đặc tính truy cập và nhất quán của từng miền; chỉ dùng nhiều loại database khi có căn cứ nghiệp vụ/kỹ thuật và đánh giá được chi phí vận hành.
- Chiến lược đọc/ghi, transaction boundary, concurrency control, idempotency, xử lý giữ hàng để tránh bán trùng, và đồng bộ dữ liệu giữa các dịch vụ.
- Index, query pattern, phân vùng/sharding nếu phù hợp, replication, connection management, migration/versioning schema và phương án tương thích khi triển khai thay đổi.
- Bảo mật dữ liệu khi truyền và khi lưu, quyền truy cập tối thiểu, mã hóa, quản lý khóa/bí mật, audit và thời hạn lưu dữ liệu.
- Cách phân tích tải đọc/ghi, dung lượng tăng trưởng, điểm nóng dữ liệu và phương án theo dõi hiệu suất.

Chiến lược backup và khôi phục trên cloud cần được thiết kế riêng cho từng loại dữ liệu và mức độ quan trọng, bao gồm:

- Phân loại dữ liệu và xác định mức mất dữ liệu, thời gian khôi phục chấp nhận được; RPO/RTO và retention phải được thống nhất, không tự đặt số liệu khi chưa có căn cứ.
- Lịch backup, loại bản sao, thời gian lưu, mã hóa, quyền truy cập, giám sát kết quả và cảnh báo khi backup thất bại.
- Bảo vệ bản backup khỏi xóa nhầm, lỗi logic, hỏng dữ liệu hoặc truy cập trái phép; đánh giá bản sao tách biệt tài khoản/quyền và sao chép khác vùng khi yêu cầu, chi phí và phạm vi triển khai cho phép.
- Quy trình khôi phục point-in-time hoặc từ snapshot, thứ tự khôi phục các thành phần phụ thuộc, kiểm tra tính toàn vẹn và xác minh ứng dụng hoạt động sau phục hồi.
- Kịch bản sự cố, thời gian khôi phục dự kiến, trách nhiệm vận hành và kế hoạch diễn tập restore định kỳ.
- Phân biệt rõ backup, replication và high availability: replication hoặc Multi-AZ hỗ trợ tính sẵn sàng nhưng không thay thế bản backup có thể khôi phục.

Các dịch vụ AWS cụ thể, topology, retention, RPO/RTO và chi phí chỉ được đề xuất sau khi có yêu cầu tải, phân loại dữ liệu, thị trường triển khai và ngân sách được xác nhận.

## 6. Nhóm người dùng và bên liên quan

| Nhóm | Vai trò khái quát |
|---|---|
| Người mua | Tìm kiếm, xem thông tin và tình trạng sản phẩm; đặt mua; theo dõi giao dịch; gửi đánh giá hoặc yêu cầu hỗ trợ. |
| Người bán | Tạo và quản lý tin đăng; cập nhật tình trạng, giá và khả năng bán; xử lý các đơn liên quan đến sản phẩm. |
| Quản trị viên hoặc nhân viên hỗ trợ | Quản lý nội dung và tài khoản theo quyền hạn; tiếp nhận báo cáo; hỗ trợ quy trình xử lý sự cố hoặc tranh chấp. |
| Nhóm vận hành hệ thống | Theo dõi tình trạng dịch vụ và hạ tầng; xử lý cảnh báo; quản lý cấu hình, triển khai và phục hồi hệ thống. |
| Nhóm dự án | Phân tích yêu cầu, thiết kế kiến trúc, xây dựng và đánh giá các sản phẩm bàn giao trong phạm vi được thống nhất. |

Danh sách vai trò và phạm vi quyền hạn chi tiết sẽ được cập nhật trong tài liệu stakeholder, persona và yêu cầu chức năng.

## 7. Năng lực nghiệp vụ ở mức khái quát

Hệ thống dự kiến cần xem xét các năng lực sau trong quá trình phân tích. Đây là danh sách định hướng, chưa thay thế backlog hoặc đặc tả yêu cầu chi tiết:

1. Quản lý tài khoản, hồ sơ và quyền truy cập.
2. Tạo, chỉnh sửa, quản lý hình ảnh và trạng thái tin đăng sản phẩm cũ.
3. Tìm kiếm, lọc và khám phá sản phẩm.
4. Quản lý giỏ hàng hoặc ý định mua, tạo đơn và giữ sản phẩm trong một khoảng thời gian được quy định.
5. Theo dõi trạng thái đơn hàng, thanh toán và giao nhận.
6. Gửi thông báo về các thay đổi liên quan đến tin đăng và giao dịch.
7. Thu thập đánh giá, báo cáo nội dung và hỗ trợ xử lý vấn đề sau giao dịch.
8. Cung cấp chức năng quản trị và khả năng theo dõi vận hành phù hợp với phạm vi dự án.

### 7.1. Kênh giao diện dự kiến

| Kênh | Người dùng | Trách nhiệm chính |
|---|---|---|
| Marketplace Web/App | Người mua | Tìm kiếm và xem sản phẩm, đặt mua, thanh toán, theo dõi đơn và giao hàng. |
| Seller Center | Người bán | Đăng và quản lý món hàng, theo dõi đơn bán, cập nhật tiến trình xử lý và giao hàng. |
| Admin/Back-office | Quản trị viên và nhân viên hỗ trợ | Kiểm duyệt, quản lý nội dung/tài khoản, tra cứu đơn và hỗ trợ xử lý báo cáo. |
| Cổng đối tác giao hàng | Nhân viên vận hành giao hàng nếu ReMarket tự vận hành | Chỉ cần nếu ReMarket trực tiếp quản lý đội giao nhận; hãng vận chuyển bên ngoài mặc định tích hợp qua API/webhook. |

Các giao diện trên là các kênh sử dụng hệ thống, không phải mỗi giao diện là một backend hoặc database riêng. Admin/Back-office cũng không mặc định là một service nghiệp vụ độc lập.

### 7.2. Service nghiệp vụ nền tảng dự kiến

Danh mục sơ bộ gồm Identity & Access, User/Profile, Catalog & Listing, Search & Discovery, Order & Checkout, Payment, Shipping & Fulfillment, và Notification. Shipping & Fulfillment là đầu mối tích hợp nhiều hãng qua adapter, không tách một microservice cho từng hãng ở giai đoạn đầu. Moderation/Trust, Reviews và Disputes có thể được xem xét tách khi quy tắc và tải nghiệp vụ đủ rõ.

API Gateway hoặc lớp API phù hợp có thể phục vụ nhiều giao diện; mỗi service quản lý dữ liệu và quy tắc nghiệp vụ thuộc trách nhiệm của mình. Search dùng dữ liệu đọc được đồng bộ từ tin đăng và không phải nguồn xác nhận cuối cùng về tình trạng hàng.

Các ưu tiên công nghệ ban đầu được nêu ở mục 7.3. Phần hosting database, compute, messaging, topology, backup services và mức tách môi trường vẫn cần được quyết định theo access pattern, tải, yêu cầu phục hồi, năng lực vận hành và chi phí.

### 7.3. Ưu tiên công nghệ ban đầu

| Thành phần | Ưu tiên | Vai trò dự kiến | Nội dung còn cần quyết định |
|---|---|---|---|
| Cơ sở dữ liệu quan hệ | Microsoft SQL Server | Dữ liệu giao dịch có quan hệ và ràng buộc như đơn hàng, thanh toán, hồ sơ và giao nhận theo ranh giới service. | Hosting trên Amazon RDS for SQL Server hay cách triển khai khác; edition, version, license, topology và restore granularity. |
| NoSQL/document database | MongoDB | Dữ liệu document linh hoạt, dự kiến phù hợp Catalog & Listing sau khi kiểm tra access pattern và consistency. | MongoDB Atlas trên AWS hay tự quản lý; Amazon DocumentDB chỉ được xem là lựa chọn MongoDB-compatible sau khi kiểm tra chức năng. |
| Cache | Caching service trên AWS | Cache dữ liệu đọc/tạm thời; không là nguồn chuẩn cho đơn hoặc khả dụng sản phẩm. | Valkey/Redis OSS, eviction, TTL, HA và chi phí. |
| Load balancer | Load balancer cho web/API | Phân phối request tới các service khỏe mạnh. | ALB là ứng viên cho HTTP/HTTPS; loại cuối cùng phụ thuộc giao thức, routing, bảo mật và topology. |
| Object storage | Amazon S3 | Ảnh/tệp sản phẩm và object tĩnh; database giữ metadata/reference. | Bucket policy, Versioning, lifecycle, encryption và yêu cầu replication. |
| Dịch vụ AWS bổ trợ | Các dịch vụ AWS phù hợp | Xử lý container, API, sự kiện, bảo mật, quan sát và backup. | ECS/Fargate hoặc EKS; SQS/SNS/EventBridge; CloudFront/WAF; IAM/KMS/Secrets Manager; CloudWatch/AWS Backup được so sánh theo yêu cầu. |

Đây là stack ưu tiên do chủ dự án nêu, chưa phải cấu hình triển khai cuối cùng. Amazon DocumentDB là dịch vụ tương thích với MongoDB API, không mặc định thay thế một MongoDB cluster; nếu cân nhắc cần kiểm tra các tính năng, driver và truy vấn thực tế.

## 8. Phạm vi dự án

### 8.1. Trong phạm vi

- Phân tích bối cảnh, mục tiêu, bên liên quan, quy trình và yêu cầu nghiệp vụ của nền tảng mua bán hàng đã qua sử dụng.
- Đặc tả yêu cầu chức năng và yêu cầu phi chức năng ở mức phù hợp với giai đoạn dự án.
- Mô hình hóa miền nghiệp vụ, dữ liệu chính, quy tắc nghiệp vụ và các luồng xử lý quan trọng.
- Thiết kế kiến trúc Microservices, ranh giới và trách nhiệm dịch vụ, quyền sở hữu dữ liệu, API hoặc sự kiện liên dịch vụ.
- Phân tích các tình huống nhất quán dữ liệu, giao dịch phân tán, retry, idempotency, timeout và xử lý lỗi.
- Đề xuất kiến trúc hạ tầng AWS cho các kênh giao diện, gateway, compute, giao tiếp giữa service, database, lưu trữ media, bảo mật, quan sát, mở rộng, sẵn sàng, backup và khôi phục.
- Ghi lại giả định, rủi ro, phụ thuộc, quyết định thiết kế và tiêu chí đánh giá.
- Phân tích và thiết kế chi tiết dữ liệu/database, quyền sở hữu dữ liệu theo dịch vụ, consistency, bảo mật, migration, hiệu suất, backup, restore và disaster recovery trên AWS; chỉ tiêu cùng dịch vụ cụ thể cần được xác nhận ở tài liệu chuyên biệt.

### 8.2. Ngoài phạm vi đã xác nhận

Các nội dung sau chưa được mặc định là một phần của phạm vi hiện tại và chỉ nên đưa vào khi được thống nhất riêng:

- Vận hành sàn thương mại điện tử thực tế hoặc tiếp nhận người dùng và giao dịch thật.
- Tích hợp production dùng credential thật với cổng thanh toán, đơn vị vận chuyển hoặc nhà cung cấp bên ngoài; thiết kế hợp đồng, adapter và luồng thử nghiệm vẫn thuộc phạm vi phân tích.
- Quy trình pháp lý, kế toán, chăm sóc khách hàng và chính sách kinh doanh hoàn chỉnh.
- Cam kết SLA, ngân sách AWS, khu vực triển khai, tải dự kiến hoặc thông số hiệu năng cụ thể.
- Bộ giao diện hoàn chỉnh ở mức thiết kế trực quan chi tiết.

Việc một nội dung nằm ngoài phạm vi đã xác nhận không ngăn dự án phân tích hoặc đưa ra đề xuất cho nội dung đó; việc triển khai hoặc cam kết vận hành cần được quyết định riêng.

## 9. Nguyên tắc định hướng thiết kế

- **Bám theo nghiệp vụ:** phân chia dịch vụ dựa trên năng lực và ranh giới nghiệp vụ, tránh tách dịch vụ chỉ theo bảng dữ liệu hoặc màn hình.
- **Quyền sở hữu dữ liệu rõ ràng:** mỗi dịch vụ chịu trách nhiệm với dữ liệu của mình; hạn chế phụ thuộc trực tiếp vào cơ sở dữ liệu của dịch vụ khác.
- **Thiết kế cho tính phân tán:** xác định rõ điểm giao tiếp, khả năng gửi lặp, thứ tự sự kiện, timeout, phục hồi và cách xử lý trạng thái không đồng bộ.
- **Bảo vệ vòng đời sản phẩm:** kiểm soát việc sản phẩm còn bán, được giữ, đã bán hoặc được mở bán lại để tránh tạo giao dịch không hợp lệ.
- **Tăng khả năng phục hồi:** xem xét cô lập lỗi, retry có kiểm soát, xử lý bất đồng bộ và các phương án suy giảm chức năng phù hợp.
- **Bảo mật theo thiết kế:** xem xét xác thực, phân quyền, bảo vệ dữ liệu nhạy cảm, quản lý bí mật và nguyên tắc quyền tối thiểu.
- **Có thể quan sát:** thiết kế khả năng ghi log, thu thập chỉ số và truy vết yêu cầu xuyên dịch vụ.
- **Cân bằng chất lượng và chi phí:** lựa chọn kiến trúc AWS dựa trên yêu cầu và đánh đổi được ghi nhận, tránh giả định rằng hạ tầng phức tạp hơn luôn tốt hơn.

## 10. Sản phẩm bàn giao dự kiến

- Bộ tài liệu BA gồm project brief, phạm vi, stakeholder, quy trình, quy tắc nghiệp vụ, yêu cầu và backlog.
- Mô hình miền, danh mục dịch vụ và mô tả ranh giới trách nhiệm.
- Sơ đồ ngữ cảnh hệ thống, luồng nghiệp vụ và các luồng giao tiếp chính.
- Đặc tả khái quát API, sự kiện và các tích hợp dự kiến.
- Thiết kế kiến trúc triển khai trên AWS cùng các quyết định về mở rộng, tính sẵn sàng, bảo mật, quan sát và chi phí.
- Tài liệu thiết kế dữ liệu/database và chiến lược backup, restore, kiểm chứng khôi phục, retention và disaster recovery.
- Bộ tiêu chí đánh giá để rà soát mức độ đáp ứng yêu cầu và các rủi ro còn lại.

Hình thức bàn giao cuối cùng và mức độ chi tiết của từng sản phẩm sẽ được xác định trong kế hoạch dự án.

## 11. Tiêu chí đánh giá mức độ hoàn thành

Project Brief và các tài liệu liên quan được xem là tạo đủ nền tảng khi:

- Mục tiêu, phạm vi, nhóm người dùng, giả định và phần ngoài phạm vi được thống nhất.
- Các hành trình nghiệp vụ cốt lõi và quy tắc liên quan đến tình trạng hàng, giữ hàng và đơn hàng được mô tả.
- Ranh giới dịch vụ và quyền sở hữu dữ liệu có lý do giải thích, đồng thời các luồng giao tiếp chính có thể truy vết về yêu cầu.
- Thiết kế phân tích được các tình huống lỗi và nhất quán dữ liệu của hệ thống phân tán.
- Giải pháp AWS gắn với yêu cầu về mở rộng, tính sẵn sàng, hiệu suất, bảo mật và vận hành.
- Thiết kế database có quyền sở hữu dữ liệu, lựa chọn lưu trữ, consistency, bảo mật, khả năng mở rộng và migration được giải thích theo yêu cầu.
- Backup và restore có chính sách, quyền bảo vệ, giám sát, quy trình phục hồi và kế hoạch kiểm chứng; RPO/RTO, retention và dịch vụ AWS được xác nhận hoặc ghi rõ còn mở.
- Các thông số chưa được quyết định được ghi thành câu hỏi, giả định hoặc mục cần xác nhận thay vì trình bày như cam kết.

## 12. Giả định và nội dung cần xác nhận

- AWS là nền tảng cloud mục tiêu của định hướng kiến trúc.
- Sản phẩm đã qua sử dụng có tình trạng và mô tả riêng; mặc định ban đầu là một tin đăng đại diện cho một món hàng, trừ khi nghiệp vụ sau này yêu cầu hỗ trợ nhiều đơn vị cùng loại.
- Danh mục hàng hóa, thị trường triển khai, ngôn ngữ, tiền tệ và quy định địa phương chưa được chốt.
- Nhà cung cấp thanh toán, vận chuyển, email, SMS hoặc dịch vụ tìm kiếm chưa được lựa chọn.
- Lưu lượng, số lượng người dùng, dữ liệu, mức tải cao điểm, SLA, ngân sách và các ngưỡng hiệu năng cần được xác định ở giai đoạn yêu cầu phi chức năng.
- Mức độ triển khai thực tế trên AWS, môi trường demo và giới hạn chi phí cần được chốt trước khi thực hiện.

## 13. Rủi ro và phụ thuộc chính

| Rủi ro hoặc phụ thuộc | Tác động cần xem xét |
|---|---|
| Phân rã Microservices quá sớm hoặc quá nhỏ | Tăng số lượng thành phần, độ phức tạp triển khai, kiểm thử và vận hành. |
| Dữ liệu cập nhật không đồng thời | Có thể làm người mua thấy trạng thái hàng hoặc đơn chưa mới nhất; cần quy định hành vi và cơ chế hội tụ dữ liệu. |
| Nhiều bước giao dịch đi qua nhiều dịch vụ | Lỗi giữa chừng có thể để lại trạng thái dở dang; cần thiết kế luồng bù trừ và khả năng phục hồi. |
| Phụ thuộc nhà cung cấp ngoài | Gián đoạn hoặc giới hạn API của đối tác có thể ảnh hưởng thanh toán, giao nhận hoặc thông báo. |
| Hạ tầng cloud và dịch vụ phân tán phát sinh chi phí | Cần đo lường, giới hạn tài nguyên và theo dõi chi phí, nhất là ở môi trường thử nghiệm. |
| Thiếu yêu cầu tải và chất lượng cụ thể | Khó đánh giá hiệu suất hoặc chọn cấu hình phù hợp; cần xác nhận bằng phân tích và mô hình tải. |

## 14. Thuật ngữ

| Thuật ngữ | Ý nghĩa trong dự án |
|---|---|
| ReMarket | Tên dự án phân tích và thiết kế hệ thống thương mại điện tử mua bán hàng đã qua sử dụng. |
| Tin đăng | Bản ghi mô tả một sản phẩm được người bán đưa lên hệ thống để chào bán. |
| Hàng đơn chiếc | Sản phẩm chỉ có một đơn vị có thể bán, thường gặp ở hàng đã qua sử dụng. |
| Microservice | Dịch vụ phần mềm có trách nhiệm nghiệp vụ xác định, có thể được phát triển và triển khai độc lập theo thiết kế. |
| Hệ thống phân tán | Hệ thống gồm nhiều thành phần giao tiếp qua mạng, có thể gặp độ trễ, lỗi từng phần và cập nhật dữ liệu không đồng thời. |
| AWS Cloud | Nền tảng cloud mục tiêu được xem xét cho giải pháp hạ tầng của dự án. |

## 15. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 11/11/2026 | Tạo Project Brief và xác định định hướng phân tích, thiết kế Microservices cùng hạ tầng AWS cho ReMarket. |
| 1.1 | 11/11/2026 | Cập nhật các kênh giao diện, service baseline và định hướng AWS/database/backup/khôi phục. |
| 1.2 | 11/11/2026 | Ghi nhận ưu tiên SQL Server, MongoDB, cache, load balancer, S3 và dịch vụ AWS bổ trợ. |
