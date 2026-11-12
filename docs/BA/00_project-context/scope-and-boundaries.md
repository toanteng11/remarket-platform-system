# Scope and Boundaries – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Tên dự án | ReMarket |
| Loại tài liệu | Scope and Boundaries |
| Phiên bản | 1.2 |
| Trạng thái | Bản nháp |
| Ngày cập nhật | 11/11/2026 |
| Tài liệu liên quan | project-brief.md, business-case.md, assumptions-constraints-dependencies.md |

## 1. Mục đích

Tài liệu xác định nội dung ReMarket sẽ phân tích và thiết kế, ranh giới của hệ thống được mô tả, những phần chưa nằm trong phạm vi đã xác nhận và cách xem xét thay đổi phạm vi.

Đây là **phạm vi phân tích và thiết kế**. Việc một năng lực xuất hiện trong tài liệu không có nghĩa năng lực đó đã được xây dựng, tích hợp hoặc đưa vào vận hành.

## 2. Tuyên bố phạm vi

ReMarket là dự án phân tích và thiết kế hệ thống thương mại điện tử chuyên mua bán đồ đã qua sử dụng. Dự án tập trung nghiên cứu kiến trúc Microservices, hệ thống phân tán và giải pháp hạ tầng AWS Cloud, hướng đến khả năng mở rộng, tính sẵn sàng cao và tối ưu hiệu suất.

Phạm vi hiện tại gồm làm rõ nghiệp vụ, yêu cầu, luồng dữ liệu, ranh giới dịch vụ và định hướng hạ tầng để đánh giá giải pháp. Chưa xác nhận yêu cầu xây dựng hoặc vận hành sàn thương mại thực tế.

## 3. Cấp độ phạm vi

| Cấp độ | Nội dung | Trạng thái |
|---|---|---|
| Phân tích nghiệp vụ | Bên liên quan, nhu cầu, năng lực, quy trình, quy tắc, phạm vi và yêu cầu. | Trong phạm vi |
| Thiết kế hệ thống | Mô hình miền, ngữ cảnh, dịch vụ, dữ liệu, giao tiếp, xử lý lỗi và chất lượng. | Trong phạm vi |
| Thiết kế hạ tầng | Định hướng AWS, mở rộng, sẵn sàng, bảo mật, quan sát và chi phí. | Trong phạm vi |
| Xây dựng phần mềm | Lập trình dịch vụ, giao diện, API hoạt động hoặc kiểm thử tự động. | Chưa xác nhận |
| Triển khai môi trường | Tạo tài khoản, tài nguyên AWS, môi trường demo hoặc production. | Chưa xác nhận |
| Vận hành thương mại | Người dùng thật, giao dịch thật, hỗ trợ khách hàng và vận hành kinh doanh. | Ngoài phạm vi hiện tại |

## 4. Tác nhân và hệ thống liên quan

### 4.0. Sơ đồ ngữ cảnh hệ thống (System Context Diagram)

``mermaid
flowchart TB
    subgraph Users["Người dùng & Tác nhân"]
        Buyer["Người mua (Buyer)"]
        Seller["Người bán (Seller)"]
        Admin["Quản trị / Hỗ trợ (Staff)"]
        Ops["Vận hành nền tảng (DevOps/SRE/DBA)"]
    end

    subgraph Channels["Kênh giao diện (Frontend Channels)"]
        MktApp["Marketplace Web/App"]
        SellerCenter["Seller Center"]
        Backoffice["Admin / Back-office Portal"]
    end

    subgraph ReMarketBoundary["Ranh giới hệ thống ReMarket"]
        APIGW["API Gateway / Application Load Balancer"]
        subgraph CoreServices["Nghiệp vụ Core Microservices"]
            ListingSvc["Catalog & Listing Service (MongoDB)"]
            OrderSvc["Order & Checkout Service (SQL Server)"]
            PaymentSvc["Payment Service (SQL Server)"]
            ShipSvc["Shipping & Fulfillment (SQL Server)"]
            ProfileSvc["User & Profile Service (SQL Server)"]
            AuthSvc["Identity & Access Service"]
            NotifSvc["Notification Service"]
            SearchSvc["Search & Discovery (Projection)"]
        end
        MediaStore[("Amazon S3 (Media Storage)")]
        CacheStore[("ElastiCache (Redis/Valkey)")]
    end

    subgraph ExternalPartners["Hệ thống đối tác bên ngoài"]
        PaymentGW["Cổng thanh toán (Payment Gateway)"]
        Carriers["Hãng vận chuyển (Adapter/API)"]
        NotifProviders["Dịch vụ gửi Email/SMS/Push"]
    end

    Buyer --> MktApp
    Seller --> SellerCenter
    Admin --> Backoffice
    Ops -.-> Backoffice

    MktApp & SellerCenter & Backoffice --> APIGW
    APIGW --> CoreServices
    ListingSvc -.-> MediaStore
    CoreServices -.-> CacheStore
    PaymentSvc <-->|API / Webhook| PaymentGW
    ShipSvc <-->|Adapter / Webhook| Carriers
    NotifSvc --> NotifProviders
``


### 4.1. Tác nhân người dùng và vận hành

- **Người mua:** tìm kiếm, xem tin đăng, đặt mua, theo dõi giao dịch và gửi đánh giá hoặc yêu cầu hỗ trợ.
- **Người bán:** tạo/cập nhật tin đăng, cung cấp mô tả và hình ảnh, quản lý khả năng bán và xử lý đơn.
- **Quản trị viên:** quản lý danh mục, tài khoản, báo cáo và nội dung theo quyền được định nghĩa.
- **Nhân viên hỗ trợ:** tiếp nhận câu hỏi, báo cáo hoặc tranh chấp và hỗ trợ quy trình xử lý.
- **Nhân viên vận hành:** theo dõi sức khỏe dịch vụ, tài nguyên, log, chỉ số, cảnh báo và phục hồi.
- **Nhóm dự án:** BA, kiến trúc sư, lập trình viên, kiểm thử viên và người phê duyệt.

Các vai trò quản trị viên, hỗ trợ và vận hành có thể được gộp trong mô hình minh họa. Phân quyền chi tiết sẽ được xác định trong tài liệu yêu cầu.

### 4.2. Hệ thống bên ngoài tiềm năng

Chưa chọn nhà cung cấp cụ thể. Các hệ thống dưới đây có thể được mô tả qua vai trò, luồng dữ liệu, hợp đồng tích hợp và tình huống lỗi:

- Cổng hoặc nhà cung cấp thanh toán.
- Đơn vị vận chuyển hoặc dịch vụ tính phí giao hàng.
- Nhà cung cấp email, SMS hoặc thông báo đẩy.
- Nhà cung cấp định danh hoặc đăng nhập liên kết, nếu cần.
- Dịch vụ tìm kiếm hoặc lưu trữ ảnh, nếu được lựa chọn.

Tích hợp thật cần được xác nhận riêng.

### 4.3. Kênh giao diện và đối tác

| Kênh | Người dùng | Phạm vi |
|---|---|---|
| Marketplace Web/App | Người mua | Khám phá sản phẩm, xem tin, đặt mua, thanh toán và theo dõi giao hàng. |
| Seller Center | Người bán | Đăng/quản lý món hàng, xem đơn bán, cập nhật bước xử lý và giao hàng. |
| Admin/Back-office | Quản trị viên, hỗ trợ | Kiểm duyệt, quản lý tài khoản/nội dung, tra cứu đơn và xử lý báo cáo theo quyền. |
| Cổng vận chuyển | Nhân viên logistics của ReMarket nếu tự vận hành | Chỉ có giao diện riêng nếu ReMarket trực tiếp quản lý đội giao nhận. Đối tác ngoài mặc định kết nối API/webhook. |

Các kênh là những ứng dụng phía người dùng. Chúng dùng chung backend services với quyền và giao diện phù hợp; không tạo database hoặc backend độc lập cho mỗi website. Admin/Back-office không mặc định là service nghiệp vụ riêng.

## 5. Năng lực nghiệp vụ trong phạm vi phân tích

Các năng lực dưới đây cần được phân tích; danh sách này không phải cam kết triển khai tất cả trong một phiên bản sản phẩm.

| Mã | Năng lực | Nội dung cần phân tích |
|---|---|---|
| SC-01 | Tài khoản và hồ sơ | Đăng ký, đăng nhập, xác thực, phân quyền, hồ sơ người mua/người bán và trạng thái tài khoản. |
| SC-02 | Quản lý tin đăng | Tạo, sửa, lưu nháp, công bố, tạm ẩn, đánh dấu bán, hết hạn hoặc gỡ tin; ảnh, mô tả, giá, danh mục và tình trạng. |
| SC-03 | Tìm kiếm và khám phá | Tìm từ khóa, danh mục, thuộc tính; lọc, sắp xếp và hiển thị trạng thái khả dụng. |
| SC-04 | Ý định mua và giữ hàng | Chọn sản phẩm, kiểm tra khả dụng, giữ hàng có thời hạn nếu quy định và xử lý hết hạn giữ hàng. |
| SC-05 | Đơn hàng | Tạo đơn, xác nhận dòng hàng, tổng tiền ở mức nghiệp vụ, vòng đời, hủy hoặc đóng đơn. |
| SC-06 | Thanh toán và hoàn tiền | Mô hình hóa yêu cầu, kết quả, timeout, callback, chống xử lý lặp và hoàn tiền. Nhà cung cấp chưa chọn. |
| SC-07 | Giao nhận | Thông tin giao hàng, yêu cầu vận chuyển, theo dõi trạng thái và lỗi tích hợp. Quy trình thực tế chưa xác nhận. |
| SC-08 | Thông báo | Thông báo thay đổi cho người mua, người bán hoặc quản trị viên; xác định sự kiện và kênh ở mức yêu cầu. |
| SC-09 | Đánh giá và tin cậy | Đánh giá sau giao dịch, báo cáo nội dung/người dùng và quy trình xử lý nghiệp vụ. |
| SC-10 | Quản trị và hỗ trợ | Quản lý nội dung, danh mục, tài khoản, báo cáo, yêu cầu hỗ trợ và quyền quản trị. |
| SC-11 | Vận hành kỹ thuật | Theo dõi dịch vụ, log, chỉ số, trace, cảnh báo, sao lưu và phục hồi ở mức kiến trúc. |

## 6. Luồng nghiệp vụ trọng tâm cần làm rõ

### 6.1. Người bán đăng tin

1. Người bán nhập thông tin, danh mục, giá, tình trạng và hình ảnh.
2. Hệ thống kiểm tra dữ liệu bắt buộc và quy tắc đăng tin.
3. Tin được lưu nháp, chờ duyệt hoặc công khai theo chính sách được xác định.
4. Tin công khai có thể xuất hiện trong tìm kiếm.
5. Người bán có thể sửa, tạm ẩn, đánh dấu đã bán hoặc gỡ tin theo quyền và trạng thái cho phép.

### 6.2. Người mua tìm và đặt mua

1. Người mua tìm kiếm và xem thông tin, tình trạng, người bán và trạng thái khả dụng.
2. Khi bắt đầu đặt hàng, hệ thống kiểm tra lại khả năng bán để không dựa vào kết quả tìm kiếm cũ.
3. Hệ thống ghi nhận ý định mua hoặc tạo đơn và giữ hàng nếu có quy tắc.
4. Người mua tiếp tục thanh toán theo phương thức thuộc phạm vi tương lai.
5. Kết quả thanh toán cập nhật trạng thái giao dịch. Khi thất bại hoặc hết hạn, hệ thống cần quy định cách giải phóng hàng và khôi phục trạng thái.
6. Sau xác nhận, giao dịch chuyển sang giao nhận.

### 6.3. Giao dịch lỗi hoặc bị hủy

1. Một dịch vụ có thể không phản hồi đúng thời hạn hoặc trả kết quả không xác định.
2. Hệ thống cần hạn chế tạo đơn hoặc ghi nhận thanh toán nhiều lần khi thông điệp được gửi lại.
3. Bước đã hoàn thành có thể cần hành động bù trừ, ví dụ hủy giữ hàng hoặc khởi tạo hoàn tiền.
4. Trạng thái đơn, thanh toán và tin đăng cần có quy tắc chuyển trạng thái.
5. Lỗi không tự phục hồi cần được quan sát và có quy trình hỗ trợ.

Trạng thái hợp lệ, ngoại lệ và hành động chi tiết được đặc tả trong use case, business rules và tài liệu quy trình.

## 7. Phạm vi kỹ thuật và kiến trúc

### 7.1. Trong phạm vi thiết kế

- Sơ đồ ngữ cảnh, kênh giao diện, ranh giới hệ thống và các nhóm tác nhân.
- Phân tích miền nghiệp vụ, ranh giới service, trách nhiệm, dữ liệu sở hữu và quan hệ phụ thuộc.
- Hợp đồng giao tiếp API/sự kiện, luồng đồng bộ/bất đồng bộ, lỗi, retry, timeout, idempotency và dead-letter handling.
- Tính nhất quán trong giữ hàng, tạo đơn, thanh toán, hủy và hoàn tiền; không dùng giao dịch cơ sở dữ liệu xuyên service.
- Kiến trúc AWS ở mức các lớp mạng, edge, API, compute, event/message, database, object storage, security, observability và backup.
- Thiết kế database chi tiết theo access pattern, mô hình dữ liệu, toàn vẹn, index, concurrency, migration, hiệu suất, bảo mật và quyền sở hữu.
- Backup, restore và disaster recovery, gồm chính sách, RPO/RTO, retention, bảo vệ bản sao, quy trình khôi phục, kiểm tra tính toàn vẹn và diễn tập.
- So sánh phương án kiến trúc và ghi rõ giả định, rủi ro, chi phí cùng quyết định còn mở.

#### 7.1.1. Service map ban đầu

Đây là danh mục service logic để phân tích. Việc tách thành tiến trình/cluster triển khai độc lập sẽ được quyết định sau khi xác định tải, ranh giới dữ liệu, nhu cầu phát hành và chi phí vận hành.

| Service | Trách nhiệm chính | Dữ liệu service sở hữu |
|---|---|---|
| Identity & Access | Đăng nhập, xác thực, vai trò, quyền và trạng thái danh tính. | Credential/identity, vai trò và chính sách truy cập. |
| User & Seller Profile | Hồ sơ người mua/người bán, địa chỉ và thông tin cửa hàng nếu có. | Profile và địa chỉ; không quản lý mật khẩu đăng nhập. |
| Catalog & Listing | Danh mục và tin đăng; mô tả, tình trạng, ảnh tham chiếu, giá; khả dụng, giữ hàng, bán hoặc gỡ tin. | Danh mục, tin đăng, trạng thái món hàng và reservation. Đây là nguồn chuẩn để xác nhận món còn có thể bán. |
| Search & Discovery | Tìm kiếm, lọc, sắp xếp và khám phá sản phẩm. | Chỉ mục/projection đọc được đồng bộ từ Listing; có thể tái tạo và không quyết định giao dịch. |
| Order & Checkout | Giỏ hàng ở giai đoạn đầu, tạo đơn, lưu snapshot dòng hàng/giá và điều phối vòng đời đơn. | Đơn hàng, dòng đơn, lịch sử trạng thái và khóa idempotency. |
| Payment | Yêu cầu thanh toán, kết quả, giao dịch, hoàn tiền và tham chiếu đối tác. | Trạng thái thanh toán/refund và tham chiếu đối tác; không mặc định lưu dữ liệu thẻ trong ReMarket, phương thức và ranh giới tuân thủ cần xác nhận. |
| Shipping & Fulfillment | Báo giá, tạo/hủy lô giao hàng, mã vận đơn, theo dõi và chuẩn hóa trạng thái. | Shipment và tracking state của ReMarket. |
| Notification | Gửi email/SMS/push hoặc thông báo trong ứng dụng theo sự kiện. | Tùy chọn gửi và trạng thái giao thông báo. |
| Trust & Moderation | Báo cáo tin đăng/người dùng, hàng đợi kiểm duyệt, quyết định xử lý và lịch sử khiếu nại. | Có thể tách sau khi chính sách, vai trò và tải nghiệp vụ đủ rõ. |
| Reviews & Ratings | Đánh giá gắn với giao dịch hoàn tất, xếp hạng người bán và kiểm soát đánh giá lạm dụng. | Có thể tách sau khi quy tắc đánh giá và yêu cầu truy vấn được xác định. |
| Disputes & Support | Hồ sơ tranh chấp, trao đổi và kết quả hỗ trợ sau giao dịch. | Có thể thuộc Trust/Back-office ban đầu; tách nếu có vòng đời và quy trình riêng. |

Seller Center và Marketplace gọi cùng các service, nhưng qua quyền khác nhau. Admin/Back-office gọi các chức năng quản trị của service nghiệp vụ theo role-based access; không đọc trực tiếp database. Cart, Trust/Moderation, Reviews và Disputes chỉ tách riêng khi có ranh giới nghiệp vụ và lợi ích vận hành rõ.

#### 7.1.2. Tích hợp đối tác giao hàng

Shipping & Fulfillment quản lý vòng đời giao hàng của ReMarket; adapter bên trong service chuẩn hóa giao tiếp với từng đối tác. Các hãng ví dụ như GHTK, GHN, GrabExpress hoặc đối tác khác không trở thành microservice riêng ở bước đầu.

Hợp đồng adapter cần bao quát lấy báo giá, tạo yêu cầu giao, hủy, theo dõi và nhận webhook/callback. Dữ liệu từ từng hãng được quy đổi sang trạng thái giao hàng chuẩn của ReMarket. Tích hợp cần có xác thực bí mật, timeout, giới hạn retry, idempotency, xác minh chữ ký callback, xử lý sự kiện trùng/sai thứ tự, hàng đợi lỗi và đối soát khi trạng thái chưa rõ. Nhà cung cấp cụ thể và khả năng API/sandbox cần được xác minh trước khi thiết kế tích hợp chi tiết.

#### 7.1.3. AWS cloud reference architecture

Kiến trúc tham chiếu được phân thành các lớp; danh sách sản phẩm AWS dưới đây là ứng viên để đánh giá, chưa phải quyết định triển khai:

1. **Kênh và edge:** Marketplace, Seller Center, Admin/Back-office; có thể đánh giá CloudFront và AWS WAF cho phân phối nội dung/lọc lưu lượng.
2. **Điểm vào API:** đánh giá Amazon API Gateway hoặc Application Load Balancer theo giao thức, routing, auth, giới hạn tải và nhu cầu BFF. BFF chỉ cần nếu mỗi kênh cần tổ hợp API riêng.
3. **Compute service:** đánh giá Amazon ECS với Fargate, Amazon EKS hoặc mô hình phù hợp khác. Dịch vụ và database đặt trong mạng riêng; chỉ lớp edge cần nhận lưu lượng công khai theo thiết kế.
4. **Giao tiếp:** API đồng bộ cho thao tác cần phản hồi tức thời; sự kiện/hàng đợi cho cập nhật Search, Notification, Shipping và các tác vụ cần xử lý bất đồng bộ. Amazon SQS, SNS hoặc EventBridge là các lựa chọn cần so sánh.
5. **Data stores:** ưu tiên SQL Server cho relational workload và MongoDB cho document/NoSQL workload theo định hướng đã nêu; đánh giá Amazon RDS for SQL Server, MongoDB Atlas trên AWS hoặc tự quản lý MongoDB. Amazon DocumentDB chỉ là phương án MongoDB-compatible cần kiểm tra tương thích tính năng, không tự xem là cùng engine. ElastiCache và S3 cũng là lựa chọn ưu tiên cho cache và object media.
6. **Bảo mật và vận hành:** VPC/subnet/security groups, IAM quyền tối thiểu, mã hóa bằng KMS, quản lý secret, log/metrics/trace, cảnh báo, IaC và CI/CD. Cần tách môi trường và kiểm soát chi phí theo phạm vi được duyệt.
7. **Tính sẵn sàng:** đánh giá triển khai nhiều Availability Zone cho các thành phần quan trọng; Region dự phòng chỉ chọn khi yêu cầu RTO/RPO, thị trường, chi phí và vận hành biện minh.

#### 7.1.4. Database và data ownership

| Năng lực/service | Database hoặc storage ưu tiên | Trách nhiệm và quy tắc |
|---|---|---|
| Catalog & Listing | MongoDB | Mô hình document phù hợp với thuộc tính đa dạng theo danh mục. Listing sở hữu mô tả/tình trạng, giá, trạng thái và reservation; cập nhật giữ hàng phải có điều kiện nguyên tử/idempotent để tránh bán trùng. |
| User & Seller Profile | SQL Server | Hồ sơ, địa chỉ và cửa hàng; dữ liệu đăng nhập/credential thuộc Identity service hoặc nhà cung cấp identity, không trộn bí mật xác thực vào hồ sơ. |
| Order & Checkout | SQL Server | Đơn, dòng đơn, snapshot sản phẩm/giá, lịch sử trạng thái và idempotency; tham chiếu Listing bằng ID, không join trực tiếp sang MongoDB. |
| Payment | SQL Server | Ý định, trạng thái thanh toán/refund và tham chiếu giao dịch; dữ liệu nhạy cảm được tối thiểu hóa, phương thức thanh toán và ranh giới tuân thủ cần xác nhận. |
| Shipping & Fulfillment | SQL Server | Shipment ReMarket, hãng được chọn, mã vận đơn và trạng thái chuẩn hóa; dữ liệu webhook được xử lý idempotent. |
| Search & Discovery | Search index, OpenSearch là ứng viên | Index/projection đọc được từ Listing; dữ liệu dẫn xuất, không phải nguồn chuẩn và cần khả năng tái tạo. |
| Ảnh và tài liệu sản phẩm | Amazon S3 | Object storage; MongoDB/SQL chỉ lưu metadata và object key, không nhét binary ảnh vào database. |
| Cache | Amazon ElastiCache for Valkey hoặc Redis OSS | Cache kết quả đọc, session/rate-limit nếu phù hợp; không làm nguồn chuẩn cho hàng, đơn hoặc thanh toán. |

Đây là mapping khởi đầu để phân tích, cần xác nhận bằng access patterns, quan hệ dữ liệu, giao dịch, mức tải, tính năng MongoDB cần dùng và kỹ năng vận hành.

- Mỗi service sở hữu dữ liệu của mình; các service khác dùng API/sự kiện, không đọc trực tiếp database nội bộ.
- Có thể dùng chung một SQL Server instance hoặc MongoDB cluster cho demo nếu mỗi service có database/schema, collection, credential và quyền riêng. Cần ghi rõ shared infrastructure làm tăng phạm vi backup/restore và có thể buộc khôi phục đồng thời nhiều service. Production cần cân nhắc hạ tầng tách riêng khi yêu cầu restore, bảo mật hoặc mở rộng độc lập đòi hỏi.
- Ưu tiên SQL Server cho transaction có quan hệ và ràng buộc; MongoDB cho dữ liệu document có cấu trúc thay đổi theo danh mục. Không phân tán một aggregate cần transaction mạnh qua cả hai database nếu chưa thiết kế cách đồng bộ và phục hồi.
- Order lưu snapshot các thuộc tính/giá quan trọng lúc mua. Order không dựa vào join runtime qua hai database để hiển thị lịch sử.
- Search index và cache có thể bị stale hoặc mất; phải rebuild từ nguồn chuẩn. Cache eviction/failover không được làm mất dữ liệu nghiệp vụ.
- Thiết kế phải xác định index/query, concurrency, connection management, migration/versioning, audit, encryption, quyền truy cập và data lifecycle. RPO/RTO của từng service và khả năng khôi phục riêng cần có trong yêu cầu.

#### 7.1.5. Cache, load balancing và các dịch vụ AWS

| Thành phần | Vai trò đề xuất | Lưu ý thiết kế |
|---|---|---|
| Application Load Balancer (ALB) | Điểm nhận/routing HTTP/HTTPS từ các kênh tới service targets; lựa chọn ưu tiên cho web/API nếu các yêu cầu khớp. | Dùng health checks, listener/routing rules, TLS và phân vùng public/private phù hợp. Network Load Balancer chỉ cân nhắc nếu có yêu cầu TCP/UDP hoặc đặc tính mạng khác. |
| Amazon ElastiCache for Valkey/Redis OSS | Cache nóng, session hoặc dữ liệu tạm phù hợp. | TTL, eviction, invalidation, cache stampede và hành vi khi cache mất cần thiết kế; không lưu trạng thái đơn/giữ hàng duy nhất ở cache. |
| Amazon S3 | Ảnh/tệp sản phẩm và object tĩnh. | Phân quyền theo role, mã hóa, lifecycle; bật Versioning cho bucket phù hợp nếu cần khôi phục overwrite/xóa nhầm và chấp nhận chi phí các phiên bản. |
| Amazon SQS/SNS/EventBridge | Tách xử lý bất đồng bộ giữa order, notification, search và shipping. | Chọn theo mô hình giao nhận; idempotency, retry, dead-letter queue, retention và replay cần được thiết kế. |
| Amazon ECS with Fargate / Amazon EKS | Ứng viên chạy container services. | So sánh độ phức tạp vận hành, kỹ năng đội ngũ, scale và chi phí; chưa chọn nền tảng triển khai. |
| CloudFront và AWS WAF | Phân phối web/media và lọc request ở edge nếu yêu cầu phù hợp. | Quy tắc bảo mật và cache behavior cần theo từng API/media path. |
| IAM, AWS KMS, Secrets Manager, CloudWatch | Quyền truy cập, encryption keys, secrets, logging/metrics/alarms. | Tách role theo workload và môi trường; theo dõi lỗi database, cache eviction, backup job và restore age. |
| AWS Backup và backup native của database | Chính sách backup tập trung cho resource được hỗ trợ; native engine backup/PITR khi phù hợp. | Kiểm tra feature support, engine/version/topology/Region, retention và restore granularity trước khi chọn. |

#### 7.1.6. Backup, restore và disaster recovery

| Dữ liệu | Cách bảo vệ và khôi phục dự kiến |
|---|---|
| SQL Server transactional databases | Nếu chọn Amazon RDS for SQL Server, đánh giá automated backup/PITR, manual snapshots, retention và Multi-AZ theo phiên bản/edition/Region. Multi-AZ giúp availability/failover, không thay thế backup. Nếu nhiều service dùng chung instance, restore có thể khôi phục cùng phạm vi instance; cần quyết định có chấp nhận khôi phục đồng thời hay cần tách instance. |
| MongoDB listing/catalog | Nếu chọn MongoDB Atlas trên AWS, dùng chính sách Cloud Backup/Continuous Cloud Backup và PITR theo tier/Region/retention được chọn. Nếu tự quản lý trên EC2, chọn phương pháp backup nhất quán cho replica set/sharded deployment; không copy file database trong khi đang ghi nếu chưa bảo đảm snapshot consistency. Nếu cân nhắc Amazon DocumentDB, dùng cơ chế backup/restore riêng và kiểm chứng chức năng tương thích trước migration. |
| Ảnh/tệp trên S3 | Đánh giá Versioning để phục hồi object bị ghi đè/xóa; lifecycle để quản lý versions và chi phí; cross-Region replication chỉ khi có yêu cầu DR và được kiểm thử. Versioning không phải bản sao độc lập thay cho mọi kịch bản mất bucket/account. |
| Cache trên ElastiCache | Mặc định dữ liệu phải có thể tái tạo từ nguồn chuẩn; cache có thể được làm ấm lại. Chỉ bật backup/cache persistence nếu có yêu cầu dữ liệu cụ thể và kiểm tra restore/chi phí. |
| Search index và queue | Index được rebuild từ Listing/events nếu đáp ứng RTO; queue/DLQ dùng retention, replay và quy trình redrive. Backup riêng nếu thời gian rebuild vượt mục tiêu phục hồi. |
| Cấu hình và quyền phục hồi | Lưu IaC có version; bảo đảm role, KMS key, network, secret và runbook cần cho restore vẫn khôi phục/truy cập được. Không commit bí mật vào source. |

Quy tắc vận hành và kiểm soát:

- Chốt phân loại dữ liệu, tần suất backup, retention, RPO/RTO, restore scope và thứ tự phục hồi trước khi cấu hình. Các giá trị này hiện chưa được cung cấp.
- Theo dõi backup job thành công, tuổi recovery point mới nhất, bản copy hoàn tất, dung lượng/chi phí và quyền truy cập; cảnh báo khi policy bị vô hiệu hoặc backup trễ.
- Đánh giá AWS Backup cho SQL Server/S3 và các tài nguyên được hỗ trợ; với MongoDB Atlas hoặc tự quản lý, dùng cơ chế đúng theo nền tảng MongoDB đã chọn thay vì giả định AWS Backup bảo vệ toàn bộ database application.
- Bản sao chéo account/Region có thể bảo vệ khỏi lỗi hoặc xâm nhập vào account nguồn khi resource, mã hóa, key, tổ chức AWS và chi phí cho phép. Vault Lock/immutability là lựa chọn bổ sung; phải duyệt retention và chi phí trước vì một số cấu hình rất khó hoặc không thể đảo ngược.
- Có runbook phục hồi theo thứ tự: account/role/key/network, SQL Server và MongoDB nguồn chuẩn, ứng dụng, S3 assets, event/queue, Search/cache projection, sau đó chuyển traffic.
- Diễn tập restore định kỳ trong môi trường cách ly: xác minh dữ liệu và quyền truy cập, kiểm tra luồng đăng tin/giữ hàng/đơn/thanh toán/giao hàng, đo thời gian thực tế và đối chiếu RPO/RTO. Lưu kết quả và cập nhật runbook.

Tài liệu tham khảo chính thức để kiểm tra khả năng và giới hạn theo phương án: [RDS SQL Server Multi-AZ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_SQLServerMultiAZ.html), [RDS point-in-time restore](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIT.html), [MongoDB Atlas backup](https://www.mongodb.com/docs/atlas/architecture/current/backups/), [MongoDB self-managed backup](https://www.mongodb.com/docs/manual/core/backups/), [DocumentDB functional differences](https://docs.aws.amazon.com/documentdb/latest/devguide/functional-differences.html), [S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html) và [Application Load Balancer listeners](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html).

#### 7.1.7. Luồng liên service trọng tâm

1. Seller Center gửi tin qua Listing; Listing phát sự kiện tin công khai; Search cập nhật projection.
2. Marketplace đọc kết quả từ Search nhưng khi đặt hàng, Order phải yêu cầu Listing xác nhận và giữ món hàng. Listing là nơi quyết định cuối cùng về khả dụng.
3. Sau khi giữ hàng, Order khởi tạo Payment. Kết quả payment được xử lý idempotent; thành công xác nhận đơn và kích hoạt Shipping, thất bại/hết hạn chạy hành động bù trừ như giải phóng giữ hàng.
4. Shipping tạo shipment qua adapter nhà cung cấp; webhook được xác thực, chuẩn hóa rồi phát sự kiện cập nhật trạng thái.
5. Notification phản ứng với các sự kiện liên quan; không tham gia sở hữu trạng thái đơn, thanh toán hay shipment.

### 7.2. Chưa quyết định hoặc chưa cam kết

- Hosting, version, edition và licensing của SQL Server; lựa chọn hosting MongoDB; loại cache/load balancer, cấu hình tài nguyên và topology cuối cùng.
- Ngôn ngữ lập trình, framework, giao thức API và công nghệ message broker.
- Số lượng dịch vụ và cách chia cuối cùng.
- Region, số Availability Zone, RTO, RPO, SLA, SLO, tải và ngân sách.
- Cấu hình bảo mật và tuân thủ theo thị trường mục tiêu.
- Mức độ sử dụng Infrastructure as Code.
- Xây dựng prototype, ứng dụng thật hoặc triển khai production.

Các lựa chọn này cần dựa trên yêu cầu và quyết định kiến trúc được ghi riêng.

## 8. Ranh giới hệ thống

### 8.1. Bên trong ranh giới logic ReMarket

ReMarket chịu trách nhiệm nghiệp vụ với các năng lực được đưa vào phạm vi: tài khoản và quyền truy cập, tin đăng, khám phá, ý định mua và đơn hàng, trạng thái thanh toán ở mức tích hợp, giao nhận, thông báo, đánh giá/báo cáo và quản trị.

Đây là ranh giới trách nhiệm nghiệp vụ, chưa chỉ định mỗi năng lực phải nằm trong một service riêng.

### 8.2. Bên ngoài ranh giới ReMarket

Đối tác thanh toán, vận chuyển, email/SMS, định danh và hạ tầng AWS nằm ngoài ranh giới nghiệp vụ. ReMarket trao đổi với họ qua hợp đồng tích hợp; đối tác chịu trách nhiệm cho quy trình nội bộ của mình.

Người mua, người bán, quản trị viên và nhóm vận hành là tác nhân tương tác, không phải thành phần bên trong kiến trúc ứng dụng.

## 9. Ngoài phạm vi hiện tại

| Nội dung | Lý do |
|---|---|
| Kinh doanh và vận hành sàn thật | Dự án hiện là phân tích và thiết kế; chưa quyết định ra mắt sản phẩm. |
| Tài khoản và dữ liệu giao dịch thật | Chưa có môi trường, chính sách quyền riêng tư hoặc quy trình vận hành được phê duyệt. |
| Tích hợp production dùng credential thật với cổng thanh toán hoặc hãng giao hàng | Chưa chọn nhà cung cấp, hợp đồng hay thông tin xác thực; phân tích hợp đồng API/adapter vẫn nằm trong phạm vi thiết kế. |
| Chính sách hoàn chỉnh về pháp lý, thuế, hóa đơn, bảo vệ người tiêu dùng và hàng cấm | Thị trường và mô hình kinh doanh chưa được xác định đầy đủ. |
| Mua bán ngoài nền tảng, ký gửi hoặc kiểm định vật lý | Mô hình giao dịch và trách nhiệm nền tảng chưa được xác nhận. |
| Cho vay, đấu giá, thuê hàng hoặc bán hàng mới | Không thuộc mục tiêu hiện tại về mua bán hàng đã qua sử dụng. |
| Ví điện tử nội bộ, trung gian ký quỹ (escrow) phức tạp hoặc quản lý payout đa tầng | Nằm ngoài phạm vi giai đoạn 1; ReMarket chỉ mô hình hóa tích hợp cổng thanh toán để thu tiền và ghi nhận dữ liệu đối soát giải ngân cơ bản cho người bán. |
| Cam kết hiệu năng/sẵn sàng bằng số liệu | Chưa có mô hình tải, ngân sách và NFR được phê duyệt. |
| Nhân sự, kho vận hoặc kế toán nội bộ doanh nghiệp | Chưa nằm trong năng lực đã xác nhận. |

## 10. Sản phẩm bàn giao dự kiến

- Bộ tài liệu BA: Project Brief, Business Case, Scope and Boundaries, Assumptions/Constraints/Dependencies và Glossary.
- Tài liệu stakeholder, quy trình, quy tắc và yêu cầu nghiệp vụ.
- User stories, use cases, acceptance criteria và yêu cầu phi chức năng.
- Mô hình miền, danh mục dịch vụ, ranh giới dữ liệu và sơ đồ hệ thống.
- Danh mục API/sự kiện và mô tả tích hợp theo mức độ thiết kế.
- Tài liệu kiến trúc AWS với quyết định, giả định, rủi ro và đánh đổi.
- Ma trận truy vết và tiêu chí rà soát nếu được yêu cầu trong kế hoạch dự án.

- Danh mục kênh giao diện, sơ đồ service, ranh giới dữ liệu, mô hình tích hợp nhiều hãng vận chuyển và kiến trúc AWS/database/backup được truy vết tới yêu cầu.

## 11. Tiêu chí chấp nhận phạm vi phân tích/thiết kế

- Mục tiêu, người dùng, năng lực cốt lõi và nội dung ngoài phạm vi được ghi nhận.
- Các luồng đăng tin, tìm kiếm, đặt mua, giữ hàng, thanh toán và giao nhận được mô tả.
- Điểm chưa quyết định được đánh dấu, có người hoặc giai đoạn cần xác nhận.
- Ranh giới hệ thống và vai trò của đối tác được nêu.
- Thiết kế Microservices và AWS truy vết được tới mục tiêu hoặc yêu cầu chất lượng.
- Không suy ra cam kết triển khai/vận hành chỉ từ việc một năng lực đã được phân tích.

- Thiết kế mô tả database và backup/restore đủ chi tiết để truy vết từ yêu cầu đến quyền sở hữu dữ liệu, chính sách phục hồi và cách kiểm chứng; các con số chưa thống nhất được ghi là cần xác nhận.

## 12. Quản lý thay đổi phạm vi

Mỗi đề xuất thay đổi cần ghi: nội dung và lý do; mục tiêu, người dùng, năng lực và sản phẩm bàn giao bị ảnh hưởng; tác động lên kiến trúc, dữ liệu, tích hợp, bảo mật, thời gian và chi phí; giả định hoặc phụ thuộc mới; người đề xuất/phê duyệt; trạng thái và phiên bản tài liệu được cập nhật.

Một năng lực chỉ là cam kết triển khai khi được phê duyệt trong phạm vi hoặc kế hoạch triển khai tương ứng.

## 13. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 11/11/2026 | Xác định ranh giới phân tích/thiết kế, năng lực, nội dung loại trừ và tiêu chí chấp nhận. |
| 1.1 | 11/11/2026 | Bổ sung service map, đa kênh, adapter vận chuyển, database AWS và phương án backup/restore/DR. |
| 1.2 | 11/11/2026 | Ghi nhận SQL Server, MongoDB, cache, load balancer, S3 và các lựa chọn hosting/backup theo AWS. |







