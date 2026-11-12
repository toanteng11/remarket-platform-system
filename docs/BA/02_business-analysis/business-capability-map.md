# Business Capability Map – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Capability map khái niệm; phạm vi và mức ưu tiên cần xác nhận |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | project-brief.md, scope-and-boundaries.md, stakeholder-register.md, value-streams.md, service map trong BA 00 |

## 1. Mục đích

Capability map mô tả ReMarket cần làm được những gì ở góc độ kinh doanh, không phụ thuộc màn hình, nhóm phát triển hay công nghệ. Map giúp kiểm tra phạm vi, tổ chức quy trình và phân tích ranh giới service mà không đồng nhất một kênh giao diện với một microservice.

Đây là mô hình năng lực đề xuất cho sàn marketplace; chưa phải sơ đồ tổ chức hay cam kết tất cả capability sẽ xuất hiện trong MVP.

## 2. Nguyên tắc phân rã

- Capability diễn đạt năng lực ổn định: “quản lý listing”, không phải một nút/màn hình.
- Phân biệt năng lực tạo giá trị trực tiếp với năng lực hỗ trợ/kiểm soát và năng lực nền tảng.
- Một capability có thể được nhiều kênh sử dụng; Marketplace, Seller Center và Admin/Back-office không tự động là backend service.
- Service map chỉ là ánh xạ sơ bộ. Chốt service dựa trên bounded context, ownership dữ liệu, consistency, tải và khả năng vận hành.
- Trạng thái capability chưa đồng nghĩa đã triển khai hay đã có owner thực tế.

### 2.1. Sơ đồ phân rã năng lực kinh doanh (Capability Diagram)

``mermaid
graph TD
    ReMarketCap["ReMarket Business Capabilities"]

    ReMarketCap --> Core["Năng lực Cốt lõi (Core Business)"]
    ReMarketCap --> Support["Năng lực Hỗ trợ & An toàn (Trust & Support)"]
    ReMarketCap --> Platform["Năng lực Nền tảng (Platform & Governance)"]

    Core --> CAP_CUS["CAP-CUS: Tài khoản & Hồ sơ người dùng"]
    Core --> CAP_MKT["CAP-MKT: Cung cấp & Khám phá hàng hóa"]
    Core --> CAP_TXN["CAP-TXN: Điều phối Giao dịch & Giữ hàng"]
    Core --> CAP_FUL["CAP-FUL: Fulfillment & Vận chuyển"]

    Support --> CAP_TRU["CAP-TRU: Kiểm duyệt, Đánh giá & Tranh chấp"]
    Support --> CAP_SUP["CAP-SUP: Hỗ trợ, Tra cứu & Thông báo"]

    Platform --> CAP_PLT["CAP-PLT: Nền tảng Cloud, Dữ liệu, Backup & DR"]
    Platform --> CAP_GOV["CAP-GOV: Quản trị dự án & Truy vết yêu cầu"]
``

## 3. Cấp 1 – capability map

| ID | Nhóm capability | Mục đích | Ưu tiên phân tích | Trạng thái |
|---|---|---|---|---|
| CAP-CUS | Tài khoản và quan hệ người dùng | Buyer/seller có danh tính, hồ sơ, trạng thái và quyền rõ. | Cao | Trong phạm vi phân tích |
| CAP-MKT | Khám phá và cung cấp hàng hóa | Seller đưa hàng lên; buyer tìm và đánh giá thông tin. | Cao | Trong phạm vi phân tích |
| CAP-TXN | Điều phối giao dịch | Chuyển ý định mua thành order, payment và kết quả có thể theo dõi. | Cao | Trong phạm vi phân tích |
| CAP-FUL | Fulfillment và giao nhận | Thực hiện, theo dõi và xử lý giao hàng/hoàn trả. | Cao nếu giao hàng nằm trong scope | Cần xác nhận chính sách |
| CAP-TRU | Tin cậy và an toàn | Quản lý report, moderation, review, fraud/dispute. | Cao về thiết kế; phạm vi chức năng cần xác nhận | Đề xuất |
| CAP-SUP | Hỗ trợ và quản trị vận hành nghiệp vụ | Hỗ trợ user, xử lý case và quản trị policy/catalog theo quyền. | Trung bình/cao | Trong phạm vi phân tích |
| CAP-PLT | Nền tảng và dữ liệu | Bảo mật, tích hợp, quan sát, backup, restore và năng lực thay đổi hệ thống. | Cao cho system design | Định hướng dự án |
| CAP-GOV | Quản trị sản phẩm/dự án | Quyết định phạm vi, đo kết quả, phê duyệt rủi ro và thay đổi. | Cao | Cần owner |

## 4. Cấp 2 – năng lực và mô tả

### 4.1. Tài khoản và quan hệ người dùng (CAP-CUS)

| ID | Capability | Mô tả | Vai trò sử dụng | Service/capability mapping sơ bộ |
|---|---|---|---|---|
| CAP-CUS-01 | Đăng nhập và xác thực | Tạo/duy trì danh tính và kiểm tra người dùng. | Tất cả người dùng | Identity & Access |
| CAP-CUS-02 | Quản lý vai trò/quyền | Gán và kiểm soát quyền của buyer, seller, moderator, support, ops. | Admin/Security/Ops | Identity & Access |
| CAP-CUS-03 | Quản lý hồ sơ và địa chỉ | Lưu thông tin hồ sơ, seller và địa chỉ cần cho giao dịch. | Buyer/Seller | User & Seller Profile |
| CAP-CUS-04 | Trạng thái và vòng đời tài khoản | Kích hoạt, hạn chế, khóa, đóng, xử lý yêu cầu dữ liệu. | User/Admin/Support | Identity + Profile + policy |

### 4.2. Khám phá và cung cấp hàng hóa (CAP-MKT)

| ID | Capability | Mô tả | Vai trò sử dụng | Service/capability mapping sơ bộ |
|---|---|---|---|---|
| CAP-MKT-01 | Quản lý taxonomy | Danh mục, thuộc tính, điều kiện và hướng dẫn listing. | Buyer/Seller/Admin | Catalog & Listing |
| CAP-MKT-02 | Tạo và duy trì listing | Draft, validation, publish, edit, hide, sold, expire. | Seller | Catalog & Listing |
| CAP-MKT-03 | Quản lý nội dung media | Upload, xem, kiểm soát và liên kết ảnh/tài liệu. | Seller/Buyer/Moderator | Catalog + object storage |
| CAP-MKT-04 | Tìm kiếm và lọc | Từ khóa, category, attributes, sort và discovery. | Buyer | Search & Discovery |
| CAP-MKT-05 | Xác nhận khả dụng | Kiểm tra trạng thái có thẩm quyền trước giao dịch. | Buyer/Seller | Catalog & Listing; Order orchestration |

### 4.3. Điều phối giao dịch (CAP-TXN)

| ID | Capability | Mô tả | Vai trò sử dụng | Service/capability mapping sơ bộ |
|---|---|---|---|---|
| CAP-TXN-01 | Checkout | Xác nhận listing, buyer, địa chỉ, giá và ý định mua. | Buyer | Order & Checkout |
| CAP-TXN-02 | Reservation | Hạn chế cạnh tranh bán cùng món; giải phóng khi hết hạn/hủy. | Buyer/Seller | Listing + order coordination |
| CAP-TXN-03 | Order management | Tạo order, snapshot, trạng thái, lịch sử, hủy và hoàn tất. | Buyer/Seller/Support | Order & Checkout |
| CAP-TXN-04 | Payment management | Payment attempt, kết quả, refund và reconciliation. | Buyer/Support/Finance | Payment |
| CAP-TXN-05 | Giao dịch nhiều bước | Xử lý sự kiện, retry, timeout, bù trừ và trạng thái pending. | Platform/Ops | Order, Payment, message/event |

### 4.4. Fulfillment và giao nhận (CAP-FUL)

| ID | Capability | Mô tả | Vai trò sử dụng | Mapping sơ bộ |
|---|---|---|---|---|
| CAP-FUL-01 | Báo giá/chọn phương thức | Tính hoặc thu nhận phương án giao hàng. | Buyer/Seller | Shipping & Fulfillment |
| CAP-FUL-02 | Tạo và quản lý shipment | Mã vận đơn, yêu cầu giao, hủy/điều chỉnh. | Seller/Ops | Shipping & Fulfillment |
| CAP-FUL-03 | Theo dõi vận chuyển | Chuẩn hóa provider state, cập nhật timeline. | Buyer/Seller/Support | Shipping + Notification |
| CAP-FUL-04 | Xử lý giao thất bại/hoàn hàng | Đưa ngoại lệ vào case, quy tắc refund/return. | Buyer/Seller/Support | Shipping + Order + Support |
| CAP-FUL-05 | Quản lý adapter đối tác | Mapping hợp đồng, auth, retry, webhook và đối soát. | Integration/Ops | Adapter trong Shipping service ban đầu |

### 4.5. Tin cậy, hỗ trợ và quản trị (CAP-TRU, CAP-SUP)

| ID | Capability | Mô tả | Vai trò sử dụng | Mapping sơ bộ |
|---|---|---|---|---|
| CAP-TRU-01 | Báo cáo listing/user | Ghi nhận report có đối tượng, lý do, bằng chứng và trạng thái. | Buyer/Seller/Moderator | Trust/Moderation |
| CAP-TRU-02 | Kiểm duyệt nội dung/tài khoản | Review, quyết định, hạn chế, gỡ và appeal. | Moderator/Admin | Trust/Moderation |
| CAP-TRU-03 | Review/rating | Thu nhận phản hồi sau giao dịch hợp lệ. | Buyer/Seller | Reviews; có thể để giai đoạn sau |
| CAP-TRU-04 | Dispute resolution | Điều tra và quyết định tranh chấp giao dịch. | Buyer/Seller/Support | Support/Disputes; scope cần xác nhận |
| CAP-SUP-01 | Quản lý case hỗ trợ | Tiếp nhận, phân loại, gán, điều tra, xử lý, đóng/reopen. | Support | Support capability |
| CAP-SUP-02 | Tra cứu giao dịch | Xem timeline theo quyền qua order/payment/shipping. | Support/Ops | Back-office dùng API/read model |
| CAP-SUP-03 | Quản trị danh mục/chính sách | Thay đổi cấu hình được ủy quyền và có lịch sử. | Admin/Product Owner | Domain services/Admin UI |
| CAP-SUP-04 | Thông báo | Gửi status change và hướng dẫn hành động. | Buyer/Seller/Staff | Notification |

### 4.6. Nền tảng và dữ liệu (CAP-PLT)

| ID | Capability | Mô tả | Mapping sơ bộ |
|---|---|---|---|
| CAP-PLT-01 | Tích hợp hệ thống | Quản lý API/event, partner adapter, version và contract. | API Gateway/ALB candidates, SQS/SNS/EventBridge candidates |
| CAP-PLT-02 | Quyền riêng tư và bảo mật | Identity, least privilege, encryption, secrets, audit, data handling. | IAM/KMS/Secrets Manager candidates |
| CAP-PLT-03 | Quản lý dữ liệu | SQL Server, MongoDB, S3, cache, search theo owner/access pattern. | RDS for SQL Server/Atlas or Mongo hosting/ElastiCache/S3/OpenSearch candidates |
| CAP-PLT-04 | Quan sát và cảnh báo | Metrics, logs, traces, business health và sự cố. | CloudWatch/APM candidates |
| CAP-PLT-05 | Sao lưu, khôi phục, DR | Backup policy, protected copy, restore, reconciliation, exercise. | AWS Backup/native database backup/S3 controls candidates |
| CAP-PLT-06 | Phát hành và cấu hình | Môi trường, CI/CD, schema migration, rollback và IaC. | Tooling cần đánh giá |
| CAP-PLT-07 | Quản lý hiệu năng/chi phí | Capacity, scaling, load balancing, budget và cost attribution. | AWS services theo workload |

Dịch vụ AWS ghi ở đây là lựa chọn để đánh giá, không phải cam kết. Cần xác minh hosting MongoDB (Atlas trên AWS, self-managed hoặc phương án tương thích), mức hỗ trợ tính năng, backup và quyền khôi phục trước thiết kế.

## 5. Capability-to-value trace

| Capability | Value stream | Mục tiêu liên quan | Chỉ số có thể hỗ trợ |
|---|---|---|---|
| CAP-MKT-02/04 | VS-01 Buyer khám phá tới nhận hàng | OBJ-01 | MET-BUY-01/02, MET-TRUST-01 |
| CAP-TXN-01/02/03/04 | VS-01, VS-02 Seller đăng tới hoàn tất | OBJ-03/04 | MET-BUY-03/04, MET-TRUST-01 |
| CAP-FUL-02/03/04 | VS-01, VS-02 | OBJ-04 | MET-SELL-05, MET-BUY-05 |
| CAP-TRU-01/02 và CAP-SUP-01 | VS-03 Trust tới giải quyết case | OBJ-05 | MET-TRUST-02/03 |
| CAP-PLT-02/03/04/05 | VS-04 Incident tới phục hồi | OBJ-06/07 | MET-PLAT-01…07 |
| CAP-GOV | Tất cả value stream | OBJ-08 | Tỷ lệ artifact có source/owner/approval |

## 6. Ngoài capability map hiện tại hoặc cần xác nhận

- Cổng seller doanh nghiệp, quản lý nhiều nhân viên, bulk upload/API.
- Ví/escrow/settlement marketplace hoặc mô hình tài chính phức tạp.
- Logistics do ReMarket tự vận hành, đội tài xế hoặc cổng carrier riêng.
- Kiểm định chất lượng/thu hồi sản phẩm hoặc xác thực nguồn gốc.
- Quảng cáo/promoted listing, loyalty, recommendation/AI pricing.
- Multi-country, multi-currency và quy định theo vùng.
- Seller analytics nâng cao và integration ERP.

Không bổ sung capability này vào MVP khi chưa có business owner, phân khúc, quy trình và giá trị đo được.

## 7. Dùng capability map để thiết kế service

1. Nhóm capability theo miền và lifecycle, sau đó xác định aggregate/dữ liệu sở hữu.
2. Kiểm tra điểm cần consistency mạnh, ví dụ reservation/order/payment.
3. Không chia service theo từng capability cấp thấp nếu cùng một vòng đời/dữ liệu.
4. Không để nhiều service ghi cùng một nguồn dữ liệu.
5. Tách capability thành service riêng khi có ownership, thay đổi, tải, bảo mật hoặc vận hành độc lập rõ.
6. Ghi dependency và trade-off trong BA 06; map này chỉ làm đầu vào.

## 8. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo capability map khái niệm và ánh xạ sơ bộ tới value stream/service. |
