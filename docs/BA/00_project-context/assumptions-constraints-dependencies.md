# Assumptions, Constraints and Dependencies – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Tên dự án | ReMarket |
| Loại tài liệu | Assumptions, Constraints and Dependencies |
| Phiên bản | 1.2 |
| Trạng thái | Bản nháp; các mục cần xác nhận vẫn đang mở |
| Ngày cập nhật | 11/11/2026 |
| Tài liệu liên quan | project-brief.md, business-case.md, scope-and-boundaries.md |

## 1. Mục đích

Tài liệu ghi lại giả định đang dùng để tiếp tục phân tích, giới hạn đã biết và điều kiện bên ngoài mà dự án phụ thuộc. Mục tiêu là tránh để giả định bị hiểu nhầm thành yêu cầu đã thống nhất hoặc quyết định kiến trúc cuối cùng.

Các mục đánh dấu **Cần xác nhận** phải được rà soát khi có thêm thông tin. Nếu một giả định thay đổi, cần xem lại yêu cầu, mô hình, phạm vi và quyết định bị ảnh hưởng.

## 2. Trạng thái quản lý

| Trạng thái | Ý nghĩa |
|---|---|
| Đã xác nhận | Được người có thẩm quyền thống nhất và có thể dùng làm cơ sở thiết kế. |
| Giả định tạm thời | Được dùng để tiếp tục phân tích; cần kiểm tra trước khi chốt thiết kế. |
| Cần xác nhận | Chưa đủ thông tin để kết luận; không trình bày như quyết định đã thống nhất. |
| Bị thay thế | Không còn áp dụng; cần dẫn tới mục hoặc quyết định mới hơn. |

## 3. Giả định

| Mã | Giả định | Cơ sở | Trạng thái | Cách kiểm chứng/đầu mối |
|---|---|---|---|---|
| A-01 | ReMarket hiện là dự án phân tích và thiết kế, chưa mặc định là nền tảng đang vận hành thương mại. | Định hướng trong Project Brief. | Đã xác nhận | Giữ nhất quán cách xác định phạm vi và diễn đạt tài liệu. |
| A-02 | Microservices, hệ thống phân tán và AWS Cloud là trọng tâm nghiên cứu, phân tích và thiết kế. | Mục tiêu do chủ dự án nêu. | Đã xác nhận | Kiểm tra sản phẩm bàn giao có liên hệ tới ba trọng tâm. |
| A-03 | Nghiệp vụ đích là mua bán hàng đã qua sử dụng qua một nền tảng thương mại điện tử. | Mô tả mục tiêu ReMarket. | Đã xác nhận | Làm rõ mô hình người bán và quy trình cụ thể trong phân tích. |
| A-04 | Mỗi món hàng cũ có tình trạng và mô tả riêng cần được người mua xem xét. | Đặc điểm nghiệp vụ hàng đã qua sử dụng. | Giả định tạm thời | Xác nhận thuộc tính tình trạng, ảnh và tiêu chí mô tả. |
| A-05 | Mặc định một tin đăng đại diện một món hàng đơn chiếc, trừ khi có yêu cầu hỗ trợ nhiều đơn vị. | Cần ngăn nhiều đơn cùng xác nhận một món hàng. | Giả định tạm thời | Xác nhận danh mục, số lượng và biến thể sản phẩm. |
| A-06 | Người mua và người bán là vai trò cốt lõi; một tài khoản có thể có một hoặc cả hai vai trò. | Mô hình marketplace sơ bộ. | Cần xác nhận | Chủ dự án xác nhận tài khoản cá nhân, cửa hàng hoặc doanh nghiệp. |
| A-07 | Luồng đơn hàng, thanh toán và giao nhận cần được phân tích ở mức nghiệp vụ/thiết kế. | Các năng lực đã nêu trong Project Brief. | Giả định tạm thời | Xác nhận mức độ chi tiết và quy trình mong muốn. |
| A-08 | ReMarket có Payment, Shipping & Fulfillment và Notification services; cổng thanh toán, hãng vận chuyển và kênh email/SMS/push là hệ thống bên ngoài được tích hợp. | Cần phân biệt service nội bộ làm chủ vòng đời nghiệp vụ với đối tác thực hiện thanh toán, giao hàng hoặc gửi thông báo. | Định hướng kiến trúc baseline (DEC-DIR-05) | Xác minh API, sandbox, credential và callback của từng đối tác. |
| A-09 | AWS là nền tảng cloud mục tiêu; các ưu tiên công nghệ người dùng nêu gồm SQL Server, MongoDB, cache, load balancer và S3. | Định hướng stack được chủ dự án xác nhận. | Đã xác nhận ở mức ưu tiên; hosting và cấu hình cụ thể cần xác nhận | Chọn dịch vụ AWS, topology và cấu hình sau khi có NFR, tải và ngân sách. |
| A-10 | Ứng dụng nguyên khối và monolith có mô-đun có thể dùng làm phương án so sánh. | Cần giải thích đánh đổi và tránh chia dịch vụ thiếu căn cứ. | Giả định tạm thời | Xác nhận mức độ so sánh trong tài liệu kiến trúc. |
| A-11 | Thị trường, ngôn ngữ, tiền tệ, AWS Region và quy định địa phương chưa được xác định. | Chưa có dữ liệu đầu vào. | Cần xác nhận | Chủ dự án xác định thị trường và yêu cầu địa phương. |
| A-12 | Mức triển khai thực tế, demo hoặc POC chưa được chốt. | Project Brief xác định rõ phân tích và thiết kế, chưa xác nhận triển khai. | Cần xác nhận | Chủ dự án phê duyệt deliverables và môi trường nếu cần. |
| A-13 | Chưa có cam kết về lượng dữ liệu, người dùng, tải cao điểm hoặc số giao dịch. | Chưa có mô hình vận hành hoặc đầu vào định lượng. | Cần xác nhận | Xác lập mô hình tải trong yêu cầu phi chức năng. |
| A-14 | Kênh giao diện logic gồm Marketplace cho người mua, Seller Center cho người bán và Admin/Back-office; cổng nhân viên giao nhận chỉ cần nếu ReMarket tự vận hành delivery. | Các vai trò và kênh đã được thống nhất ở mức khái niệm. | Định hướng kiến trúc baseline (DEC-DIR-06) | Xác nhận danh sách kênh sản phẩm và role cụ thể. |
| A-15 | Shipping & Fulfillment quản lý shipment ReMarket và dùng adapter cho nhiều hãng; không tạo một microservice riêng cho từng carrier ở baseline. | Mô hình tích hợp đa hãng đã được thống nhất ở mức kiến trúc. | Định hướng kiến trúc baseline (DEC-DIR-05) | Xác minh API, sandbox, callback và hợp đồng của từng hãng trước khi tích hợp. |
| A-16 | Mỗi service là chủ sở hữu dữ liệu nghiệp vụ của mình; Search index/cache là dữ liệu dẫn xuất, không quyết định khả dụng hay giao dịch. | Cần tránh truy cập chéo database và dữ liệu đọc cũ làm xác nhận đơn sai. | Định hướng kiến trúc baseline (DEC-DIR-06) | Rà soát trong service catalog, domain model và luồng order. |
| A-17 | Môi trường demo có thể dùng hạ tầng database vật lý dùng chung chỉ khi dữ liệu, schema, credential và quyền được cô lập theo service; production cần đánh giá lại isolation. | Cân bằng chi phí demo với quyền sở hữu độc lập. | Giả định tạm thời | Chốt theo môi trường, rủi ro bảo mật, tải và phương án vận hành. |
| A-18 | Microsoft SQL Server, MongoDB, cache, load balancer và Amazon S3 là stack ưu tiên; SQL Server làm relational store, MongoDB làm NoSQL/document store theo yêu cầu của chủ dự án. | Người dùng đã chỉ định các công nghệ này. | Đã xác nhận về công nghệ ưu tiên | Xác minh service nào dùng từng store, schema/access pattern, version và cấu hình. |
| A-19 | Amazon RDS for SQL Server, MongoDB Atlas trên AWS hoặc tự quản lý MongoDB, ElastiCache, ALB và các dịch vụ AWS nêu trong Scope là phương án hosting/triển khai cần đánh giá, chưa phải lựa chọn cuối. | Cần phân biệt engine/công nghệ với dịch vụ hosting và loại load balancer. | Cần xác nhận | So sánh tính năng, license, feature compatibility, vận hành, backup/restore, Region và chi phí. |

## 4. Ràng buộc

| Mã | Ràng buộc | Tác động | Cách quản lý |
|---|---|---|---|
| C-01 | Thiết kế cần phản ánh trọng tâm Microservices, hệ thống phân tán và AWS. | Cần mô tả dịch vụ, giao tiếp mạng, dữ liệu phân tán và hạ tầng cloud. | Gắn quyết định kiến trúc với mục tiêu hoặc yêu cầu cụ thể. |
| C-02 | AWS là nền tảng cloud mục tiêu. | Phân tích hạ tầng dựa trên AWS; điều này không có nghĩa tài khoản đã sẵn sàng. | Giữ dịch vụ cụ thể ở trạng thái đề xuất đến khi được duyệt. |
| C-03 | Chưa có lưu lượng, SLO/SLA, RTO/RPO hoặc ngưỡng hiệu năng. | Chưa thể cam kết định lượng về phản hồi, tải hoặc phục hồi. | Chốt các đầu vào trong NFR trước khi đánh giá cấu hình. |
| C-04 | Chưa có ngân sách dự án hoặc vận hành AWS. | Chưa thể so sánh chi phí bằng số liệu. | Ghi nhận chi phí là hạng mục cần phân tích; duyệt giới hạn trước khi triển khai thử. |
| C-05 | Chưa xác định thị trường và phạm vi pháp lý. | Chưa thể chốt quy định tuân thủ, lưu trữ dữ liệu, thuế hoặc bảo vệ người tiêu dùng. | Xác định khu vực hoạt động và yêu cầu địa phương trước khi chốt thiết kế liên quan. |
| C-06 | Chưa chọn nhà cung cấp thanh toán, vận chuyển, email/SMS hoặc định danh. | Chưa thể hoàn thiện chi tiết API và xử lý lỗi tích hợp. | Mô tả hợp đồng khái quát, cập nhật sau khi chọn đối tác. |
| C-07 | Chưa có quyết định đưa hệ thống vào production hoặc tiếp nhận giao dịch thật. | Tài liệu thiết kế không phải cam kết vận hành. | Phân biệt kiến trúc dự kiến, prototype và production. |
| C-08 | Chưa có lịch, nguồn lực, kỹ năng đội ngũ hoặc tiêu chí đánh giá cuối cùng. | Chưa xác định được mức chi tiết khả thi và kế hoạch thực hiện. | Làm rõ trước khi lập kế hoạch triển khai. |
| C-09 | Microservices tạo ra nhiều thành phần cần triển khai, quan sát, bảo mật và hỗ trợ. | Có thể tăng công sức, độ phức tạp và chi phí so với giải pháp đơn giản. | Đánh giá kích thước dịch vụ hợp lý và dùng phương án đơn giản làm đối chiếu. |
| C-10 | Thiết kế database phải phân tích sâu; thiết kế cloud phải xác định backup, restore và khôi phục dữ liệu như nội dung trọng tâm. | Ảnh hưởng mô hình dữ liệu, lựa chọn lưu trữ, quyền sở hữu dữ liệu, vận hành, chi phí và khả năng phục hồi. | Xem đây là yêu cầu đã xác nhận; chưa tự chốt engine, dịch vụ AWS, retention, RPO/RTO hoặc topology khi thiếu đầu vào. |
| C-11 | Các kênh, service, database và dịch vụ AWS là ranh giới/ứng viên thiết kế, chưa phải cam kết deploy từng thành phần riêng. | Số lượng thành phần có thể làm tăng chi phí, độ phức tạp và khối lượng vận hành. | Chọn mức triển khai theo nhu cầu, tải, bảo mật, kỹ năng và ngân sách; ghi rõ lựa chọn cuối cùng. |
| C-12 | Stack SQL Server, MongoDB, cache, load balancer và S3 đã được ưu tiên; phiên bản, hosting, license, topology và service assignment chưa chốt. | Các lựa chọn này ảnh hưởng compatibility, backup, recovery, security và chi phí. | Không đổi engine âm thầm; kiểm chứng tính năng và yêu cầu trước khi chốt từng dịch vụ. |

## 5. Phụ thuộc

| Mã | Phụ thuộc | Đầu mối | Điều kiện cần | Tác động nếu thiếu hoặc chậm |
|---|---|---|---|---|
| D-01 | Phạm vi nghiệp vụ và mô hình người bán | Chủ dự án/đại diện nghiệp vụ | Làm rõ bán cá nhân, cửa hàng, doanh nghiệp, ký gửi hoặc mô hình khác. | Ảnh hưởng hồ sơ, kiểm duyệt, đăng bán và ranh giới dịch vụ. |
| D-02 | Vòng đời sản phẩm và đơn hàng | Chủ dự án/BA | Xác nhận trạng thái tin, giữ hàng, hết hạn, đặt đơn, thanh toán, hủy và bán lại. | Ảnh hưởng tính nhất quán và luồng phân tán. |
| D-03 | NFR và mô hình tải | Chủ dự án, kiến trúc sư, nhóm kỹ thuật | Thống nhất tải, thời gian đáp ứng, sẵn sàng, phục hồi và bảo mật. | Không thể chọn cấu hình hoặc kiểm chứng kiến trúc. |
| D-04 | Thị trường, khu vực địa lý và ngôn ngữ | Chủ dự án | Xác định nơi cung cấp dịch vụ và yêu cầu dữ liệu/quy định. | Ảnh hưởng AWS Region, quyền riêng tư và tích hợp địa phương. |
| D-05 | Ngân sách và quyền truy cập AWS | Chủ dự án/đầu mối cloud | Ngân sách, tài khoản, quyền IAM, môi trường và quy trình cấp quyền nếu có triển khai. | Không thể triển khai/đo chi phí; có rủi ro tài nguyên ngoài dự kiến. |
| D-06 | Tài liệu và sandbox nhà cung cấp | Nhà cung cấp/người phụ trách tích hợp | API docs, credential thử nghiệm, callback và giới hạn sử dụng. | Tích hợp chỉ mô tả ở mức khái quát. |
| D-07 | Quy tắc bảo mật và quyền riêng tư | Chủ dự án/đầu mối pháp lý hoặc bảo mật | Phân loại dữ liệu, thời hạn lưu, quyền truy cập và cách xử lý dữ liệu cá nhân. | Có thể đổi mô hình dữ liệu, luồng truy cập và hạ tầng. |
| D-08 | Phê duyệt tài liệu và quyết định kiến trúc | Người có thẩm quyền | Xác định người rà soát và cách ghi nhận quyết định. | Giả định chưa được duyệt có thể đi tiếp vào thiết kế. |
| D-09 | Mục tiêu và tiêu chí đánh giá dự án | Chủ dự án/người đánh giá | Xác định yêu cầu phải chứng minh trong sản phẩm cuối. | Khó xác định mức chi tiết và kiểm tra tính đầy đủ. |
| D-10 | Đầu vào cho thiết kế database và backup/recovery | Chủ dự án, BA, kiến trúc sư database/cloud, nhóm vận hành | Phân loại dữ liệu; mẫu truy cập và tải; tốc độ tăng dữ liệu; retention; yêu cầu bảo mật; RPO/RTO; AWS Region và ngân sách. | Chưa thể lựa chọn giải pháp, tính chi phí hoặc xác nhận khả năng khôi phục nếu thiếu các đầu vào này. |
| D-11 | API và điều kiện tích hợp các hãng giao hàng | Đầu mối đối tác/tích hợp | Xác nhận hãng cần hỗ trợ, API, sandbox, webhook, credential, giới hạn gọi và quy trình đối soát. | Chưa thể chốt adapter hoặc luồng tích hợp chạy thật nếu thiếu tài liệu và môi trường thử. |
| D-12 | Quyết định hosting và license database/cache | Chủ dự án, đầu mối cloud/database, procurement nếu cần | Chốt Amazon RDS hay cách khác cho SQL Server; MongoDB Atlas/tự quản lý/DocumentDB; version/edition/license; cache engine; loại load balancer. | Chưa thể chốt backup, topology, chi phí hoặc runbook nếu thiếu quyết định và tài liệu hỗ trợ. |

## 6. Câu hỏi cần xác nhận

1. Marketplace giữa cá nhân với cá nhân, cá nhân với cửa hàng hay hỗ trợ cả hai?
2. Một tài khoản có thể vừa mua vừa bán không?
3. Kết quả dự án chỉ cần tài liệu hay gồm prototype/triển khai demo?
4. Có yêu cầu cụ thể về danh mục hàng, tình trạng, ảnh, kiểm duyệt hoặc nguồn gốc không?
5. Giữ hàng có thời hạn không? Khi đơn/thanh toán thất bại, hàng được mở bán lại theo quy tắc nào?
6. Thanh toán trực tuyến, COD hay cả hai?
7. Ai chịu trách nhiệm đóng gói, giao nhận, phí vận chuyển và giao hàng thất bại?
8. Thị trường, tiền tệ, ngôn ngữ và AWS Region mục tiêu là gì?
9. Có chỉ tiêu định lượng về phản hồi, tải, sẵn sàng hoặc khôi phục không?
10. Có giới hạn chi phí AWS cho môi trường thử nghiệm không?
11. Có cần tích hợp nhà cung cấp thật không?
12. Ai phê duyệt yêu cầu, phạm vi và kiến trúc?
13. Dữ liệu nào cần backup, thời hạn retention, RPO/RTO nào được chấp nhận và cần khôi phục sang vùng/tài khoản nào?
14. Ai chịu trách nhiệm giám sát backup và thực hiện diễn tập restore?
17. MongoDB sẽ chạy trên MongoDB Atlas, tự quản lý trên AWS hay cân nhắc DocumentDB sau khi kiểm tra tương thích?
18. SQL Server dùng edition/version/license nào và có chọn Amazon RDS for SQL Server không?
19. Cache engine và loại load balancer nào phù hợp với yêu cầu HTTP/API?
15. Marketplace, Seller Center và Back-office cần hỗ trợ web, mobile hay cả hai?
16. Lần tích hợp đầu cần hỗ trợ những hãng giao hàng nào, và có cần cổng riêng cho nhân viên giao nhận ReMarket không?

## 7. Quản lý và rà soát

- Mỗi mục có mã ổn định để tham chiếu từ yêu cầu và quyết định kiến trúc.
- Khi xác nhận, ghi người xác nhận, ngày xác nhận và tài liệu bị ảnh hưởng.
- Khi giả định bị bác bỏ, đánh dấu bị thay thế, ghi quyết định mới và rà soát mô hình liên quan.
- Khi phụ thuộc chưa đáp ứng, ghi rõ phần thiết kế bị giới hạn và kết luận chưa thể đưa ra.
- Rà soát tài liệu trước khi chốt NFR, kiến trúc AWS hoặc phạm vi triển khai.

## 8. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 11/11/2026 | Ghi nhận giả định, ràng buộc, phụ thuộc và câu hỏi cần xác nhận ban đầu. |
| 1.1 | 11/11/2026 | Cập nhật các kênh, adapter vận chuyển, quyền sở hữu dữ liệu và đầu vào backup/DR. |
| 1.2 | 11/11/2026 | Ghi nhận SQL Server, MongoDB, cache, load balancer, S3 và hosting/license cần xác nhận. |





