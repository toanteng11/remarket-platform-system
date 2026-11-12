# Glossary – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Tên dự án | ReMarket |
| Loại tài liệu | Glossary (Từ điển thuật ngữ) |
| Phiên bản | 1.3 |
| Trạng thái | Đã chuẩn hóa và đồng bộ với toàn bộ phân hệ BA |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | project-brief.md, scope-and-boundaries.md, to-be-processes.md, business-rules.md |

## 1. Mục đích và quy ước

Glossary quy định cách dùng thuật ngữ nghiệp vụ và kỹ thuật trong tài liệu ReMarket. Định nghĩa áp dụng cho phạm vi dự án này khi có nhiều cách hiểu trong thực tế.

- Giữ thuật ngữ tiếng Anh trong cột riêng khi thường gặp trong tài liệu kiến trúc hoặc AWS.
- Định nghĩa không khẳng định mọi công nghệ hoặc mẫu thiết kế nêu ra sẽ được chọn.
- Quy tắc trạng thái cuối cùng được quy định chi tiết trong [business-rules.md](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/business-rules.md) và tài liệu quy trình.
- Khi thuật ngữ đổi nghĩa hoặc phát sinh mới, cập nhật Glossary và rà soát các tài liệu liên quan.

## 2. Thuật ngữ nghiệp vụ

| Thuộc tính / Thuật ngữ | English term | Định nghĩa trong ReMarket |
|---|---|---|
| ReMarket | ReMarket | Tên dự án phân tích và thiết kế hệ thống thương mại điện tử mua bán hàng đã qua sử dụng. |
| Sàn thương mại điện tử | Marketplace | Nền tảng kết nối người bán với người mua và hỗ trợ đăng bán, tìm kiếm, đặt mua. Trong dự án, đây là hệ thống được thiết kế, chưa mặc định đang hoạt động. |
| Người mua | Buyer | Người tìm kiếm hoặc mua sản phẩm qua nền tảng. |
| Người bán | Seller | Người dùng hoặc tổ chức tạo tin đăng và cung cấp sản phẩm để bán. Mô hình cá nhân/cửa hàng/doanh nghiệp cần xác nhận. |
| Sản phẩm | Product | Loại hoặc mô tả chung của mặt hàng, có thể gồm tên, thương hiệu, danh mục và thuộc tính chung. |
| Món hàng | Item | Đơn vị hàng cụ thể đang được chào bán; có tình trạng vật lý riêng và có thể là duy nhất (đơn chiếc). Phân biệt với loại sản phẩm. |
| Tin đăng | Listing | Bản chào bán do người bán tạo, gắn với món hàng hoặc số lượng xác định, có nội dung, giá, ảnh và trạng thái. |
| Danh mục | Category | Cách phân loại sản phẩm để tổ chức tin đăng, điều hướng và lọc kết quả. |
| Tình trạng sản phẩm | Item condition | Mô tả mức sử dụng, hao mòn hoặc chất lượng hiện tại (ví dụ: Mới nguyên hộp, 99%, Khá, Cũ có trầy xước). Bộ giá trị và tiêu chí xác định trong yêu cầu. |
| Tồn khả dụng | Available inventory | Số lượng hoặc món hàng đang có thể được đặt mua. Với món đơn chiếc, chỉ có 2 giá trị: còn hoặc không còn khả dụng. |
| Giữ hàng | Reservation | Đánh dấu tạm một món hàng cho quy trình mua trong thời hạn hoặc điều kiện xác định nhằm chống bán trùng (overselling). |
| Giỏ hàng | Cart | Tập hợp sản phẩm người mua cân nhắc trước khi gửi yêu cầu đặt hàng; giỏ hàng chỉ lưu ý định mua phía client/service, chưa đồng nghĩa hàng đã được giữ. |
| Đơn hàng | Order | Yêu cầu mua của người mua gồm các món hàng, giá trị và trạng thái. Đơn hàng không đồng nghĩa thanh toán thành công. |
| Dòng đơn hàng | Order line | Một món hàng hoặc dòng số lượng cụ thể trong đơn hàng. |
| Thanh toán | Payment | Quy trình yêu cầu, xác nhận hoặc từ chối khoản tiền cho đơn hàng qua đối tác thanh toán. |
| Thanh toán khi nhận hàng | Cash on Delivery (COD) | Phương thức người mua trả tiền mặt khi đối tác vận chuyển giao hàng; cần đối soát qua hãng. |
| Hoàn tiền | Refund | Hoàn một phần hoặc toàn bộ khoản đã thanh toán theo quy tắc nghiệp vụ khi hủy đơn, trả hàng hoặc có tranh chấp. |
| Giải ngân / Đối soát người bán | Payout / Settlement | Quy trình chuyển tiền thu hộ sau khi đơn hoàn tất thành công từ tài khoản nền tảng/cổng thanh toán cho người bán theo chu kỳ đối soát. |
| Giao nhận | Shipping / Delivery | Quá trình chuyển món hàng từ người bán đến người mua hoặc hoàn tất bàn giao. |
| Đơn giao hàng | Shipment | Bản ghi/yêu cầu giao nhận có mã theo dõi (tracking number) và trạng thái chuẩn hóa. |
| Đánh giá | Review | Phản hồi về giao dịch, người bán hoặc trải nghiệm theo chính sách sau khi đơn hàng hoàn tất. |
| Báo cáo vi phạm | Report | Yêu cầu gửi về nội dung, tin đăng hoặc hành vi bị cho là không phù hợp hoặc vi phạm chính sách sàn. |
| Hồ sơ vụ việc | Case | Thực thể theo dõi vòng đời giải quyết một khiếu nại, báo cáo kiểm duyệt, tranh chấp hoặc ngoại lệ đối soát. |
| Kiểm duyệt | Moderation | Xem xét, phê duyệt, ẩn hoặc xử lý tin đăng, tài khoản và nội dung theo quy tắc của sàn. |
| Tranh chấp | Dispute | Bất đồng liên quan sản phẩm, giao dịch, thanh toán hoặc giao nhận cần tiếp nhận và xử lý qua support/moderation. |
| Nhà cung cấp bên ngoài | External provider | Tổ chức/hệ thống ngoài ReMarket cung cấp dịch vụ thanh toán, vận chuyển hoặc thông báo. |

### 2.1. Kênh giao diện và tích hợp

| Thuật ngữ | English term | Định nghĩa trong ReMarket |
|---|---|---|
| Marketplace Web/App | Marketplace frontend | Kênh người mua dùng để tìm kiếm, xem tin đăng, đặt mua và theo dõi giao dịch. |
| Seller Center | Seller portal | Kênh người bán dùng để đăng món hàng, quản lý tin và xử lý các đơn bán. Dùng chung backend với Marketplace theo quyền phù hợp. |
| Admin/Back-office | Admin portal / Back office | Kênh quản trị/hỗ trợ để kiểm duyệt, tra cứu và xử lý nghiệp vụ được cấp quyền; không tự xem là một domain microservice độc lập. |
| API Gateway | API Gateway | Điểm vào duy nhất tiếp nhận/định tuyến request từ một hoặc nhiều frontend tới backend microservices; chịu trách nhiệm xác thực, rate limit và logging. |
| BFF | Backend for Frontend | Lớp API riêng cho một kênh giao diện khi kênh đó cần tổ hợp dữ liệu/hành vi khác biệt; không bắt buộc tạo cho mọi frontend. |
| Adapter hãng vận chuyển | Carrier adapter | Thành phần chuyển hợp đồng chung của Shipping Service thành API riêng của từng đối tác và chuẩn hóa phản hồi về mô hình ReMarket. |
| Trạng thái giao hàng chuẩn | Canonical shipping status | Bộ trạng thái nội bộ thống nhất của ReMarket, ánh xạ từ các trạng thái khác nhau của các đối tác vận chuyển. |
| Projection tìm kiếm | Search projection | Bản dữ liệu đọc được tạo từ Listing hoặc sự kiện; có thể trễ (eventual consistency), cần khả năng tái tạo và không phải nguồn chuẩn để xác nhận giao dịch. |
| Webhook | Webhook | Cơ chế HTTP callback bất đồng bộ để nhà cung cấp bên ngoài (thanh toán, vận chuyển) chủ động gửi thông báo cập nhật trạng thái vào ReMarket. |
| Correlation ID | Correlation ID | Mã định danh duy nhất được sinh ra khi một yêu cầu bắt đầu, truyền qua toàn bộ các service và hệ thống ngoài để phục vụ truy vết phân tán (tracing/logging). |

## 3. Trạng thái nghiệp vụ tham chiếu chuẩn hóa

Các trạng thái được chuẩn hóa và đồng bộ giữa Glossary, Quy trình To-Be (	o-be-processes.md) và Quy tắc nghiệp vụ (usiness-rules.md):

### 3.1. Tin đăng (Listing States)
- **Draft (Nháp):** Tin vừa tạo, đang soạn thảo, chưa công khai.
- **Pending Review (Chờ duyệt):** Tin gửi duyệt nội dung nếu có chính sách kiểm duyệt trước.
- **Published / Active (Đang bán):** Tin công khai, tìm thấy được và sẵn sàng nhận checkout.
- **Reserved (Đang giữ):** Món hàng tạm thời khóa cho một người mua đang tiến hành checkout/thanh toán.
- **Sold (Đã bán):** Giao dịch mua được xác nhận thành công; món hàng không còn chào bán.
- **Hidden / Paused (Tạm ẩn):** Người bán hoặc hệ thống tạm thời ẩn khỏi tìm kiếm, không nhận order mới.
- **Rejected (Bị từ chối):** Kiểm duyệt từ chối công khai do vi phạm chính sách hoặc thiếu thông tin.
- **Removed (Đã gỡ):** Tin bị gỡ vĩnh viễn bởi người bán hoặc ban quản trị.
- **Expired (Hết hạn):** Tin quá thời hạn niêm yết mà chưa bán được.

### 3.2. Đơn hàng (Order States)
- **Draft (Đơn nháp):** Khởi tạo phiên checkout, thu thập địa chỉ và tùy chọn vận chuyển.
- **Pending Payment (Chờ thanh toán):** Đơn đã tạo, đang giữ hàng, chờ kết quả thanh toán từ gateway hoặc người mua.
- **Confirmed (Đã xác nhận):** Thanh toán thành công (hoặc xác nhận COD); đơn sẵn sàng bàn giao cho khâu vận chuyển.
- **Fulfillment Pending (Chờ chuẩn bị hàng):** Người bán chuẩn bị đóng gói và lấy mã vận đơn.
- **Shipped (Đang giao hàng):** Hãng vận chuyển đã lấy hàng và đang chuyển phát.
- **Delivered (Đã giao hàng):** Hãng vận chuyển giao thành công đến người mua.
- **Completed (Hoàn tất):** Quá thời hạn khiếu nại/trả hàng hoặc người mua xác nhận hài lòng; đơn đóng thành công và kích hoạt đối soát.
- **Cancelled (Đã hủy):** Đơn bị hủy bởi người mua, người bán hoặc timeout hệ thống; giải phóng giữ hàng/hoàn tiền.
- **Disputed (Đang khiếu nại/Tranh chấp):** Đơn có phát sinh khiếu nại cần nhân viên can thiệp xử lý.

### 3.3. Thanh toán (Payment States)
- **Initiated (Khởi tạo):** Yêu cầu thanh toán được tạo trong hệ thống với reference duy nhất.
- **Pending (Đang chờ):** Chờ người mua thao tác tại cổng thanh toán hoặc chờ phản hồi từ ngân hàng.
- **Authorized (Đã ủy quyền):** Tiền được khóa/giữ trên tài khoản người mua (nếu áp dụng mô hình auth-capture).
- **Captured (Đã thu tiền):** Tiền đã được trừ thành công từ tài khoản người mua vào tài khoản sàn.
- **Failed (Thất bại):** Giao dịch thanh toán bị từ chối, timeout hoặc người mua hủy bỏ.
- **Refund Pending (Chờ hoàn tiền):** Lệnh hoàn tiền đã gửi sang cổng thanh toán, chờ xử lý.
- **Refunded (Đã hoàn tiền):** Cổng thanh toán xác nhận đã hoàn lại tiền cho người mua.
- **Reconciliation Required (Cần đối soát):** Số liệu hoặc trạng thái thanh toán lệch giữa sàn và đối tác.

### 3.4. Giao nhận (Shipment Canonical States)
- **Quote Pending (Chờ báo giá):** Đang tính cước phí giao hàng dựa trên trọng lượng/khoảng cách.
- **Created (Đã tạo vận đơn):** Đã tạo mã vận đơn thành công với hãng vận chuyển.
- **Picked Up (Đã lấy hàng):** Shipper đã nhận hàng từ người bán.
- **In Transit (Đang trung chuyển):** Hàng đang được lưu chuyển qua các bưu cục.
- **Delivered (Đã phát thành công):** Hàng đã giao tới tay người nhận.
- **Delivery Failed (Giao thất bại):** Không liên lạc được người mua hoặc người mua từ chối nhận.
- **Returned (Đang/Đã chuyển hoàn):** Hàng chuyển hoàn về lại cho người bán.
- **Cancelled (Hủy vận đơn):** Đơn giao bị hủy trước khi shipper nhận hàng.
- **Unknown (Chưa rõ trạng thái):** Trạng thái hãng trả về chưa chuẩn hóa được, cần kiểm tra đối soát.

### 3.5. Hồ sơ vụ việc (Case States)
- **Open (Mới tiếp nhận):** Vụ việc được tạo từ người dùng báo cáo hoặc hệ thống cảnh báo.
- **Triaged (Đã phân loại):** Đã đánh giá mức độ ưu tiên và gán cho nhóm phụ trách.
- **Investigating (Đang điều tra):** Nhân viên đang rà soát dữ liệu, bằng chứng và trao đổi với các bên.
- **Waiting (Chờ phản hồi):** Đang chờ người dùng hoặc đối tác bổ sung thông tin chứng cứ.
- **Escalated (Chuyển cấp duyệt):** Vụ việc vượt thẩm quyền, chuyển lên cấp quản trị cao hơn.
- **Resolved (Đã xử lý):** Đã ra quyết định và áp dụng chế tài/hành động bù trừ.
- **Closed (Đã đóng):** Kết thúc vụ việc và lưu trữ hồ sơ.
- **Reopened (Mở lại):** Người dùng khiếu nại quyết định (appeal) và vụ việc được mở lại.

## 4. Thuật ngữ BA và quản lý dự án

| Thuật ngữ | English term | Định nghĩa trong ReMarket |
|---|---|---|
| Bên liên quan | Stakeholder | Cá nhân/nhóm có nhu cầu, quyền quyết định, ảnh hưởng hoặc chịu tác động từ dự án. |
| Năng lực nghiệp vụ | Business capability | Khả năng cấp cao cần có của tổ chức/nền tảng (ví dụ: Quản lý tin đăng, Điều phối giao dịch). |
| Quy trình hiện tại | As-is process | Cách thức công việc diễn ra trước khi có giải pháp mới; dùng làm cơ sở khảo sát. |
| Quy trình mục tiêu | To-be process | Mô hình quy trình nghiệp vụ dự kiến vận hành khi giải pháp ReMarket được áp dụng. |
| Quy tắc nghiệp vụ | Business rule | Điều kiện/chính sách cốt lõi quyết định dữ liệu, hành vi hoặc tính hợp lệ của giao dịch. |
| Yêu cầu chức năng | Functional requirement | Hành vi hoặc năng lực cụ thể mà hệ thống phần mềm phải cung cấp. |
| Yêu cầu phi chức năng | Non-functional requirement | Thuộc tính chất lượng của hệ thống như hiệu năng, độ tin cậy, an toàn, phục hồi. |
| Trường hợp sử dụng | Use case | Tập hợp tương tác giữa tác nhân (actor) và hệ thống nhằm đạt được một mục tiêu cụ thể. |
| Hành trình người dùng | User journey | Chuỗi trải nghiệm từng bước của người dùng qua các điểm tiếp xúc để đạt mục tiêu. |
| User story | User story | Mô tả nhu cầu từ góc nhìn người dùng theo cấu trúc: Là ai, Tôi muốn gì, Để làm gì. |
| Tiêu chí chấp nhận | Acceptance criteria | Điều kiện cụ thể, kiểm tra được để xác nhận yêu cầu hoặc user story đã hoàn thành đạt chuẩn. |
| Ma trận truy vết yêu cầu | Requirements traceability matrix (RTM) | Ma trận liên kết từ mục tiêu, nhu cầu, yêu cầu, ca kiểm thử đến quyết định thiết kế. |
| Giả định | Assumption | Điều tạm thời công nhận là đúng trong phân tích nhưng chưa có quyết định phê duyệt chính thức. |
| Ràng buộc | Constraint | Giới hạn bắt buộc ảnh hưởng tới phạm vi, thời gian, giải pháp kỹ thuật hoặc chi phí. |
| Phụ thuộc | Dependency | Yếu tố, quyết định hoặc thành phần bên ngoài cần phải có trước khi tiến hành bước tiếp theo. |
| MoSCoW | MoSCoW prioritization | Phương pháp phân loại mức độ ưu tiên yêu cầu: Must have, Should have, Could have, Won't have (lần này). |
| Quyết định kiến trúc | Architecture Decision Record (ADR) | Văn bản ghi nhận bối cảnh, các phương án cân nhắc, lý do lựa chọn và hệ quả của quyết định kỹ thuật. |

## 5. Thuật ngữ Microservices và hệ thống phân tán

| Thuật ngữ | English term | Định nghĩa trong ReMarket |
|---|---|---|
| Microservices | Microservices | Phong cách kiến trúc chia hệ thống thành các dịch vụ nhỏ theo nghiệp vụ, phát triển và triển khai độc lập. |
| Ranh giới dịch vụ | Service boundary | Đường biên phân định rõ ràng trách nhiệm nghiệp vụ, mô hình dữ liệu và phạm vi thay đổi của một service. |
| Bounded context | Bounded context | Ranh giới trong Domain-Driven Design (DDD) nơi một mô hình miền và từ vựng mang ý nghĩa nhất quán nhất. |
| Hệ thống phân tán | Distributed system | Hệ thống gồm nhiều thành phần chạy trên các máy chủ khác nhau qua mạng, có độ trễ và lỗi từng phần. |
| Giao tiếp đồng bộ | Synchronous communication | Bên gọi gửi request và chờ phản hồi ngay (thường qua REST/HTTP hoặc gRPC). |
| Giao tiếp bất đồng bộ | Asynchronous communication | Bên gửi phát thông điệp/sự kiện qua hàng đợi (message queue/broker) mà không chờ xử lý xong ngay. |
| Sự kiện nghiệp vụ | Domain Event | Thông điệp phản ánh một sự việc có ý nghĩa đã xảy ra trong hệ thống (ví dụ: OrderCreated, PaymentCaptured). |
| Quyền sở hữu dữ liệu | Data ownership | Nguyên tắc một microservice là đơn vị duy nhất có quyền ghi/cập nhật trực tiếp kho dữ liệu của mình. |
| Database per service | Database per service | Mẫu thiết kế mỗi service sở hữu cơ sở dữ liệu riêng; các service khác chỉ truy cập qua API hoặc sự kiện. |
| Nhất quán cuối cùng | Eventual consistency | Mô hình dữ liệu chấp nhận trạng thái không đồng bộ tạm thời giữa các node, nhưng sẽ hội tụ sau khi sự kiện được xử lý xong. |
| Saga pattern | Saga pattern | Mẫu điều phối giao dịch phân tán qua chuỗi các bước cục bộ kết hợp các hành động bù trừ (compensating actions) khi gặp lỗi. |
| Hành động bù trừ | Compensating action | Hành động nghiệp vụ đưa hệ thống về trạng thái cân bằng an toàn sau khi một bước trong Saga thất bại (ví dụ: hủy giữ hàng khi thanh toán trượt). |
| Idempotency | Idempotency | Tính chất đảm bảo một thao tác thực hiện nhiều lần với cùng dữ liệu đầu vào không tạo ra kết quả phụ ngoài mong muốn. |
| Outbox pattern | Transactional Outbox pattern | Mẫu thiết kế lưu sự kiện nghiệp vụ vào cùng transaction với dữ liệu bảng, sau đó có tiến trình riêng gửi sang Message Broker để chống mất sự kiện. |
| Dead-Letter Queue | Dead-Letter Queue (DLQ) | Hàng đợi đặc biệt chứa các thông điệp bị lỗi xử lý nhiều lần vượt quá ngưỡng retry quy định để nhân viên kiểm tra sau. |
| Circuit breaker | Circuit breaker | Cơ chế tự động ngắt các cuộc gọi tới một service đang bị lỗi nhằm tránh làm sập dây chuyền toàn hệ thống. |
| Read model | Read model | Cấu trúc dữ liệu tối ưu hóa cho mục đích đọc/tìm kiếm, được cập nhật bất đồng bộ từ các sự kiện nghiệp vụ. |
| Cô lập lỗi | Fault isolation | Khả năng ngăn chặn sự cố ở một service lan truyền sang các service khác trong hệ thống. |

## 6. Thuật ngữ chất lượng hệ thống và bảo mật

| Thuật ngữ | English term | Định nghĩa trong ReMarket |
|---|---|---|
| Khả năng mở rộng | Scalability | Khả năng đáp ứng lưu lượng truy cập và khối lượng dữ liệu tăng lên bằng cách mở rộng tài nguyên (ngang/dọc). |
| Tính sẵn sàng cao | High availability (HA) | Khả năng hệ thống tiếp tục hoạt động liên tục ngay cả khi một số thành phần phần cứng/hạ tầng bị lỗi. |
| Khả năng phục hồi | Resilience | Khả năng hệ thống tự phục hồi về trạng thái hoạt động bình thường sau sự cố hoặc gián đoạn. |
| RTO | Recovery Time Objective | Mục tiêu thời gian tối đa cho phép để khôi phục dịch vụ sau khi xảy ra sự cố. |
| RPO | Recovery Point Objective | Mục tiêu thời điểm khôi phục; lượng dữ liệu tối đa chấp nhận bị mất (tính theo khoảng thời gian) sau sự cố. |
| Khả năng quan sát | Observability | Năng lực nắm bắt trạng thái nội tại của hệ thống thông qua Metrics, Logs và Distributed Tracing. |
| PII | Personally Identifiable Information | Dữ liệu định danh cá nhân (họ tên, số điện thoại, địa chỉ nhà) cần được bảo vệ, mã hóa và che giấu (masking). |
| RBAC | Role-Based Access Control | Kiểm soát truy cập dựa trên vai trò người dùng (Buyer, Seller, Moderator, Support, Business Admin, Ops). |
| ABAC | Attribute-Based Access Control | Kiểm soát truy cập dựa trên thuộc tính ngữ cảnh (ví dụ: Seller chỉ được cập nhật tin do chính mình sở hữu). |
| Break-glass | Break-glass procedure | Quy trình khẩn cấp có kiểm soát nghiêm ngặt và ghi log đầy đủ để cấp quyền đặc quyền xử lý sự cố ngoài giờ. |

## 7. Thuật ngữ AWS và Cơ sở dữ liệu

| Thuật ngữ | English term | Định nghĩa trong ReMarket |
|---|---|---|
| SQL Server | Microsoft SQL Server | Hệ quản trị cơ sở dữ liệu quan hệ (RDBMS) ưu tiên cho dữ liệu giao dịch: Order, Payment, Profile, Shipping. |
| MongoDB | MongoDB | Hệ quản trị cơ sở dữ liệu hướng tài liệu (NoSQL Document Store) ưu tiên cho dữ liệu Catalog & Listing thuộc tính linh hoạt. |
| Cache | Cache store | Tầng lưu trữ tạm thời tốc độ cao (ElastiCache for Redis/Valkey) cho dữ liệu đọc nóng, không phải nguồn chuẩn. |
| Load Balancer | Application Load Balancer (ALB) | Dịch vụ phân phối lưu lượng HTTP/HTTPS tầng 7 thông minh đến các container microservice khỏe mạnh. |
| Amazon S3 | Simple Storage Service (S3) | Dịch vụ lưu trữ đối tượng (Object Storage) chuyên dụng cho ảnh và tệp đính kèm của tin đăng; database chỉ lưu URL/Object Key. |
| Amazon RDS for SQL Server | RDS for SQL Server | Dịch vụ cơ sở dữ liệu quan hệ được AWS quản lý hoàn toàn cho Microsoft SQL Server, hỗ trợ Multi-AZ và automated backups. |
| MongoDB Atlas | MongoDB Atlas | Dịch vụ database MongoDB trên cloud được nhà cung cấp quản lý, có khả năng triển khai tích hợp trên hạ tầng AWS. |
| Amazon DocumentDB | DocumentDB | Dịch vụ document database của AWS tương thích chuẩn MongoDB API; cần đánh giá sai khác chức năng trước khi sử dụng. |
| Infrastructure as Code | IaC | Quản lý và cung cấp tài nguyên hạ tầng thông qua tệp cấu hình mã nguồn (Terraform, AWS CDK/CloudFormation). |
| Point-in-time recovery | PITR | Khôi phục cơ sở dữ liệu về bất kỳ thời điểm cụ thể nào trong quá khứ nằm trong thời hạn lưu trữ log giao dịch. |
| Đối soát | Reconciliation | Quy trình đối chiếu định kỳ dữ liệu giao dịch giữa hệ thống ReMarket với cổng thanh toán và hãng vận chuyển để phát hiện chênh lệch. |

## 8. Danh mục từ viết tắt chuẩn

| Viết tắt | Dạng đầy đủ | Nghĩa tiếng Việt |
|---|---|---|
| ABAC | Attribute-Based Access Control | Kiểm soát truy cập theo thuộc tính |
| ADR | Architecture Decision Record | Bản ghi quyết định kiến trúc |
| ALB | Application Load Balancer | Bộ cân bằng tải ứng dụng |
| API | Application Programming Interface | Giao diện lập trình ứng dụng |
| AWS | Amazon Web Services | Nền tảng điện toán đám mây Amazon |
| BA | Business Analyst / Business Analysis | Phân tích nghiệp vụ |
| BFF | Backend for Frontend | Lớp dịch vụ hỗ trợ riêng cho từng giao diện |
| C2C | Customer to Customer | Giao dịch thương mại giữa các cá nhân tiêu dùng |
| CI/CD | Continuous Integration / Continuous Deployment | Tích hợp và triển khai tự động |
| COD | Cash on Delivery | Thanh toán tiền mặt khi nhận hàng |
| DLQ | Dead-Letter Queue | Hàng đợi thông điệp lỗi |
| DR | Disaster Recovery | Phục hồi sau thảm họa |
| HA | High Availability | Tính sẵn sàng cao |
| IaC | Infrastructure as Code | Hạ tầng dưới dạng mã nguồn |
| IAM | Identity and Access Management | Quản lý danh tính và quyền truy cập |
| MVP | Minimum Viable Product | Sản phẩm khả dụng tối thiểu |
| NFR | Non-Functional Requirement | Yêu cầu phi chức năng |
| PII | Personally Identifiable Information | Dữ liệu định danh cá nhân |
| PITR | Point-In-Time Recovery | Khôi phục dữ liệu theo thời điểm |
| POC | Proof of Concept | Bằng chứng chứng minh tính khả thi |
| RBAC | Role-Based Access Control | Kiểm soát truy cập theo vai trò |
| RPO | Recovery Point Objective | Mục tiêu điểm khôi phục dữ liệu |
| RTM | Requirements Traceability Matrix | Ma trận truy vết yêu cầu |
| RTO | Recovery Time Objective | Mục tiêu thời gian khôi phục |
| S3 | Simple Storage Service | Dịch vụ lưu trữ đối tượng trên AWS |
| SLA | Service Level Agreement | Cam kết chất lượng dịch vụ |
| SLO | Service Level Objective | Mục tiêu nội bộ về chất lượng dịch vụ |
| UAT | User Acceptance Testing | Kiểm thử chấp nhận người dùng |

## 9. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 11/11/2026 | Khởi tạo Glossary ban đầu. |
| 1.1 | 11/11/2026 | Bổ sung thuật ngữ frontend, carrier adapter và phục hồi dữ liệu. |
| 1.2 | 11/11/2026 | Bổ sung stack SQL Server, MongoDB, cache, ALB, S3. |
| 1.3 | 12/11/2026 | Loại bỏ thuật ngữ trùng lặp (Load balancer, API Gateway, Backup); bổ sung trạng thái chuẩn hóa 5 thực thể và các thuật ngữ COD, PII, RBAC, ABAC, DLQ, Reconciliation, Break-glass, Case, Saga, Outbox. |