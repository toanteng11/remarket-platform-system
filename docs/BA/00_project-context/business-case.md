# Business Case – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Tên dự án | ReMarket |
| Loại tài liệu | Business Case |
| Phiên bản | 1.2 |
| Trạng thái | Bản nháp |
| Ngày cập nhật | 11/11/2026 |
| Tài liệu liên quan | project-brief.md, scope-and-boundaries.md, assumptions-constraints-dependencies.md |

## 1. Mục đích

Tài liệu này trình bày lý do thực hiện ReMarket, giá trị kỳ vọng, các phương án kiến trúc cần xem xét, chi phí, rủi ro và tiêu chí đánh giá. Đây là business case cho **dự án phân tích và thiết kế**, không phải kế hoạch đầu tư hoặc cam kết đưa sàn thương mại điện tử vào vận hành.

Các nhận định về nhu cầu dùng làm bối cảnh thiết kế. Tài liệu không đưa ra số liệu thị trường, doanh thu, quy mô người dùng hay dự báo tài chính vì chưa có dữ liệu được cung cấp và xác minh.

## 2. Tóm tắt lập luận

ReMarket là dự án phân tích và thiết kế một hệ thống thương mại điện tử mua bán đồ đã qua sử dụng, tập trung nghiên cứu Microservices, hệ thống phân tán và hạ tầng AWS Cloud. Dự án cần tạo ra một thiết kế giải thích được mối liên hệ giữa nhu cầu nghiệp vụ, ranh giới dịch vụ, dữ liệu, giao tiếp liên dịch vụ, chất lượng hệ thống và hạ tầng.

Giá trị trực tiếp nằm ở các sản phẩm phân tích và thiết kế có thể rà soát, truy vết và làm cơ sở cho giai đoạn triển khai sau này nếu được phê duyệt. Dự án không mặc định cần vận hành giao dịch thật để đạt mục tiêu hiện tại.

## 3. Bối cảnh và cơ hội

Hàng đã qua sử dụng có tình trạng riêng; người bán cần mô tả đúng món hàng; nhiều người có thể quan tâm cùng lúc; và giao dịch gồm các bước liên quan như đặt hàng, giữ hàng, thanh toán và giao nhận.

Nếu các nghiệp vụ được phân chia thành nhiều dịch vụ, dữ liệu có thể cập nhật không đồng thời, một bước có thể thành công trong khi bước sau thất bại, dịch vụ phụ thuộc có thể không phản hồi, và tải có thể tập trung vào một số chức năng. Đây là bối cảnh phù hợp để nghiên cứu hệ thống phân tán và đánh giá đánh đổi của Microservices.

Cơ hội của dự án là tạo ra trường hợp thiết kế cụ thể để khảo sát các vấn đề này mà không tuyên bố trước hiệu quả kinh doanh chưa được đo lường.

## 4. Vấn đề cần giải quyết

Dự án cần trả lời:

1. Năng lực nghiệp vụ nào thuộc phạm vi của một sàn mua bán đồ đã qua sử dụng?
2. Nên chia trách nhiệm và quyền sở hữu dữ liệu giữa các dịch vụ thế nào?
3. Làm sao hạn chế cùng một món hàng được xác nhận cho nhiều đơn?
4. Làm sao xử lý quy trình nhiều dịch vụ khi một bước lỗi hoặc phản hồi trễ?
5. Cần xác định yêu cầu nào về hiệu suất, mở rộng, sẵn sàng, bảo mật và vận hành?
6. Giải pháp hạ tầng AWS nào có thể đáp ứng các yêu cầu đó, với đánh đổi và chi phí nào?
7. Tình huống nào biện minh cho Microservices, và tình huống nào khiến độ phức tạp này không cần thiết?

## 5. Mục tiêu và kết quả mong đợi

| Mã | Mục tiêu | Kết quả |
|---|---|---|
| BC-O1 | Làm rõ bài toán và phạm vi nghiệp vụ | Vai trò, luồng chính, quy tắc và giới hạn có thể được rà soát. |
| BC-O2 | Xây dựng cơ sở thiết kế Microservices | Ranh giới dịch vụ, trách nhiệm, dữ liệu sở hữu và giao tiếp được giải thích. |
| BC-O3 | Phân tích tính phân tán | Lỗi từng phần, cập nhật không đồng thời và phương án phục hồi được nhận diện. |
| BC-O4 | Định hình giải pháp AWS | Sơ đồ và nguyên tắc hạ tầng gắn với mở rộng, sẵn sàng, bảo mật và quan sát. |
| BC-O5 | Hỗ trợ quyết định giai đoạn sau | Giả định, câu hỏi mở, rủi ro và chi phí cần xác minh được ghi lại. |
| BC-O6 | Thiết kế dữ liệu và khả năng phục hồi đáng tin cậy | Có phương án database, bảo vệ dữ liệu, backup, restore và diễn tập khôi phục gắn với yêu cầu và đánh đổi. |
| BC-O7 | Hỗ trợ nhiều kênh và đối tác giao hàng | Marketplace, Seller Center và back-office dùng chung service phù hợp; tích hợp hãng vận chuyển qua adapter và trạng thái chuẩn. |
| BC-O8 | Áp dụng stack dữ liệu và AWS có thể vận hành, phục hồi | SQL Server, MongoDB, cache, load balancer, S3 và backup/restore được gắn với access pattern, yêu cầu, chi phí và trách nhiệm vận hành. |

## 6. Giá trị kỳ vọng

### 6.1. Giá trị trực tiếp

- Thống nhất cách hiểu về mục tiêu, phạm vi, nghiệp vụ và thuật ngữ.
- Cho thấy cách yêu cầu nghiệp vụ dẫn đến quyết định về dịch vụ, dữ liệu và giao tiếp.
- Tạo trường hợp để đánh giá Microservices trong miền nghiệp vụ có các luồng giao dịch liên quan nhau.
- Nhận diện sớm quyết định về nhất quán, lỗi, bảo mật, quan sát và vận hành.
- Định hình kiến trúc AWS dựa trên yêu cầu thay vì lựa chọn dịch vụ tùy ý.
- Giảm mơ hồ và việc phải thiết kế lại do giả định chưa được ghi nhận.

### 6.2. Giá trị tiềm năng sau này

Nếu có giai đoạn triển khai, các tài liệu có thể hỗ trợ lập kế hoạch thử nghiệm hoặc phát triển sản phẩm. Khi đó, khả năng phục vụ người dùng, độ tin cậy giao dịch, thời gian phản hồi và chi phí vận hành phải được đo trên môi trường và dữ liệu thực tế. Đây chưa phải kết quả đã đạt của dự án phân tích và thiết kế.

## 7. Phương án cần xem xét

| Phương án | Mô tả | Lợi ích có thể có | Hạn chế hoặc rủi ro | Vai trò |
|---|---|---|---|---|
| A. Ứng dụng nguyên khối | Nghiệp vụ nằm trong một ứng dụng và thường phát hành cùng nhau. | Dễ bắt đầu, triển khai và theo dõi; ít thành phần phân tán. | Khó mở rộng hoặc phát hành độc lập theo nghiệp vụ; không thể hiện đầy đủ mục tiêu nghiên cứu Microservices. | Đối chiếu cơ bản. |
| B. Monolith có mô-đun | Một ứng dụng triển khai thống nhất, chia mã nguồn theo nghiệp vụ. | Vận hành đơn giản hơn, vẫn giữ ranh giới nghiệp vụ rõ hơn ứng dụng không cấu trúc. | Mô-đun chưa triển khai hoặc mở rộng độc lập; cần kỷ luật giữ ranh giới. | Đối chiếu quan trọng về mức độ cần thiết của Microservices. |
| C. Microservices trên AWS | Năng lực được chia thành dịch vụ có trách nhiệm rõ, giao tiếp qua mạng và triển khai trên AWS. | Phù hợp trọng tâm dự án; có thể phân tích triển khai, mở rộng, lỗi từng phần và quyền sở hữu dữ liệu. | Tăng độ phức tạp giao dịch phân tán, quan sát, bảo mật, triển khai, kiểm thử và chi phí nền tảng. | Phương án trọng tâm cần phân tích và thiết kế. |

Dự án lấy Microservices và AWS làm trọng tâm theo mục tiêu đã xác nhận. So sánh các phương án đơn giản hơn giúp giải thích đánh đổi, không thay thế trọng tâm này.

## 8. Chi phí và tính khả thi

### 8.1. Nhóm chi phí cần xem xét

Chưa có dữ liệu để ước tính ngân sách cụ thể. Cần xem xét thời gian phân tích và thiết kế; công sức xây dựng mẫu thử nếu được duyệt; chi phí AWS cho mạng, tính toán, lưu trữ, cơ sở dữ liệu, truyền dữ liệu, log, giám sát và bảo mật; duy trì nhiều môi trường; công sức vận hành, sao lưu và phục hồi; cùng chi phí dịch vụ bên thứ ba nếu tích hợp.

### 8.2. Tính khả thi

Giai đoạn phân tích và thiết kế có thể tiến hành mà chưa cần tài khoản AWS hoặc tích hợp nhà cung cấp thật nếu giả định được ghi rõ. Triển khai thử nghiệm sẽ phụ thuộc vào ngân sách, quyền truy cập AWS, năng lực vận hành, mục tiêu thử nghiệm và lựa chọn dịch vụ.

Microservices có thể phù hợp với mục tiêu nghiên cứu của dự án nhưng không mặc nhiên giảm chi phí hoặc tăng hiệu suất. Các lợi ích đó chỉ được kết luận sau khi có yêu cầu, thiết kế và phương pháp đo.

## 9. Tiêu chí đánh giá business case

| Tiêu chí | Cách đánh giá |
|---|---|
| Phù hợp mục tiêu | Thể hiện rõ đây là dự án phân tích và thiết kế, tập trung Microservices, hệ thống phân tán và AWS. |
| Có căn cứ | Quyết định gắn với yêu cầu, giả định hoặc đánh đổi; không trình bày dự báo chưa kiểm chứng như sự thật. |
| Truy vết được | Mục tiêu liên kết với sản phẩm bàn giao, yêu cầu và tiêu chí đánh giá. |
| Đánh giá được kiến trúc | Có phương án đối chiếu, lợi ích, hạn chế và rủi ro. |
| Có ranh giới kênh và service rõ | Mỗi giao diện có vai trò riêng nhưng dùng backend phù hợp; service sở hữu dữ liệu và tích hợp vận chuyển không ràng buộc toàn hệ thống vào một hãng. |
| Stack công nghệ có căn cứ | SQL Server, MongoDB, cache, load balancer và S3 được gán vai trò; hosting, compatibility, license và chi phí còn mở được đánh giá trước khi chốt. |
| Khả thi | Phụ thuộc về nguồn lực, AWS, ngân sách, tải, đối tác và phạm vi được nêu. |
| Bảo vệ và phục hồi dữ liệu | Thiết kế xác định phạm vi backup, retention, bảo mật bản sao, quy trình restore và cách kiểm chứng; RPO/RTO chưa có thì được ghi là cần xác nhận. |
| Không cam kết quá mức | Không khẳng định có vận hành, SLA, doanh thu hay người dùng thật khi chưa xác nhận. |

## 10. Rủi ro và cách xử lý

| Rủi ro | Tác động | Cách xử lý |
|---|---|---|
| Chọn Microservices chỉ vì mục tiêu công nghệ | Thiết kế phức tạp hơn nhu cầu. | So sánh với monolith có mô-đun; giải thích từng ranh giới dịch vụ. |
| Nghiệp vụ chưa rõ | Thiết kế dịch vụ và dữ liệu phải thay đổi. | Ghi câu hỏi mở, giả định và người cần xác nhận trong tài liệu BA. |
| Chưa có tải hoặc SLA | Không thể đánh giá cấu hình AWS hoặc hiệu năng định lượng. | Chốt mô hình tải và chỉ tiêu trong yêu cầu phi chức năng trước khi kiểm chứng. |
| Chuyển sang triển khai mà không cập nhật phạm vi | Phát sinh chi phí và trách nhiệm vận hành chưa dự kiến. | Phê duyệt deliverables, ngân sách và môi trường trước khi triển khai. |
| Chưa chọn nhà cung cấp | Luồng tích hợp có thể thay đổi. | Mô tả hợp đồng khái quát và đánh dấu chi tiết cần xác nhận. |
| Phân tách sai database hoặc service | Có thể tạo chia sẻ dữ liệu chặt, lỗi lan rộng hoặc vận hành quá phức tạp. | Chốt quyền sở hữu dữ liệu, access patterns, ranh giới service và mức cô lập vật lý theo yêu cầu/tải. |
| Không kiểm soát chi phí AWS khi làm demo | Tài nguyên thử nghiệm có thể tiếp tục tính phí. | Nếu triển khai, đặt ngân sách, cảnh báo, giới hạn và quy trình tắt môi trường. |

## 11. Khuyến nghị

Tiếp tục dự án theo hướng phân tích và thiết kế có trọng tâm. Trước tiên, thống nhất phạm vi nghiệp vụ, mô hình người bán, vòng đời món hàng, luồng đặt hàng và mức độ triển khai. Sau đó xác định yêu cầu chất lượng và mô hình tải rồi đề xuất ranh giới Microservices và hạ tầng AWS cụ thể.

Kết quả là bộ thiết kế và cơ sở quyết định. Triển khai thử nghiệm hoặc vận hành thương mại cần được phê duyệt riêng cùng đánh giá nguồn lực, ngân sách và trách nhiệm vận hành.

## 12. Câu hỏi và quyết định còn mở

| Mã | Nội dung cần quyết định | Trạng thái |
|---|---|---|
| BC-D1 | Bàn giao chỉ gồm tài liệu hay có thêm mẫu thử/triển khai minh họa? | Cần xác nhận |
| BC-D2 | Thị trường, ngôn ngữ và tiền tệ mục tiêu là gì? | Cần xác nhận |
| BC-D3 | Tải, thời gian phản hồi và tính sẵn sàng mong muốn là gì? | Cần xác nhận |
| BC-D4 | Có giới hạn chi phí AWS cho môi trường thử nghiệm không? | Cần xác nhận |
| BC-D5 | Cần tích hợp nhà cung cấp thật hay chỉ mô hình hóa hợp đồng? | Cần xác nhận |
| BC-D6 | Ai phê duyệt phạm vi và quyết định kiến trúc? | Cần xác nhận |

## 13. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 11/11/2026 | Tạo lập luận dự án, giá trị, phương án kiến trúc, chi phí, rủi ro và quyết định còn mở. |
| 1.1 | 11/11/2026 | Bổ sung giá trị đa kênh, tích hợp đối tác giao hàng, ranh giới dữ liệu và phục hồi. |
| 1.2 | 11/11/2026 | Ghi nhận stack dữ liệu/cloud ưu tiên cùng các điểm hosting và chi phí cần đánh giá. |




