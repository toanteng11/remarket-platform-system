# Business Rules – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Danh mục quy tắc đề xuất; chưa có policy được business owner phê duyệt |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | to-be-processes.md, user-roles-and-permissions.md, business-scenarios-and-exceptions.md, open-questions-and-decisions.md |

## 1. Mục đích và cách đọc

Danh mục này tạo baseline để xác định quy tắc nghiệp vụ cần chốt. Quy tắc ở trạng thái **Đề xuất** hoặc **Cần quyết định** không được coi là yêu cầu đã duyệt. Khi có quyết định, ghi người duyệt, ngày, bằng chứng và phiên bản policy trong open-questions-and-decisions.md; sau đó cập nhật trạng thái tại đây.

Mã ID ổn định để truy vết từ yêu cầu, use case, test scenario, service/API và quyết định kiến trúc.

## 2. Trạng thái

- **Định hướng đã nêu:** được người yêu cầu xác nhận ở mức mục tiêu dự án.
- **Đề xuất cần duyệt:** mặc định hợp lý để phân tích, chưa phải chính sách đã chốt.
- **Cần quyết định:** có nhiều phương án hoặc thiếu thông tin.
- **Bất biến thiết kế:** nguyên tắc kiến trúc/phân tích; không phải chính sách thị trường.
- **Đã duyệt:** chỉ dùng sau khi ghi rõ owner, ngày và bằng chứng.

## 3. Listing và hàng hóa

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-LST-001 | Listing phải gắn với một seller owner và danh mục xác định. | Đề xuất cần duyệt | Quyền sở hữu và báo cáo |
| BR-LST-002 | Listing chỉ được công khai khi vượt qua kiểm tra trường bắt buộc và nội dung theo policy. | Cần quyết định | Có duyệt trước hay hậu kiểm |
| BR-LST-003 | Listing hiển thị rõ tình trạng hàng đã qua sử dụng; không biểu đạt thông tin seller khai báo như kiểm định độc lập nếu chưa xác minh. | Đề xuất cần duyệt | Bảo vệ kỳ vọng buyer |
| BR-LST-004 | Listing phải phân biệt giá, tình trạng, ảnh, phụ kiện và khuyết điểm theo cấu trúc phù hợp danh mục. | Đề xuất cần duyệt | Taxonomy/required attributes còn mở |
| BR-LST-005 | Mặc định phân tích ban đầu: một listing đại diện một món hàng đơn chiếc. | Cần quyết định | 00 ghi là giả định ban đầu; cần xác nhận biến thể/số lượng |
| BR-LST-006 | Chỉ seller sở hữu hoặc người được ủy quyền mới được sửa/ẩn/gỡ listing; admin hành động theo role và audit. | Đề xuất cần duyệt | Xem role matrix 01 |
| BR-LST-007 | Listing đã sold/reserved/ineligible không được tiếp tục hiển thị như chắc chắn có thể mua. | Đề xuất cần duyệt | Search/cache có thể trễ |
| BR-LST-008 | Sửa thuộc tính ảnh hưởng giá, tình trạng hoặc khả năng mua phải được lưu lịch sử; có thể cần review lại. | Cần quyết định | Xác định loại thay đổi và review |
| BR-LST-009 | Xóa listing/media phải xét nghĩa vụ evidence của order, report, dispute và retention. | Cần quyết định | Data retention/legal policy |
| BR-MED-001 | Mỗi tin đăng phải có tối thiểu 01 ảnh thực tế và tối đa 10 ảnh; hỗ trợ JPEG, PNG, WebP; tối đa 5MB/ảnh. | Đề xuất cần duyệt | Tiêu chuẩn hóa dữ liệu media |
| BR-MED-002 | Ảnh được lưu trữ trực tiếp trên Amazon S3 với URL pre-signed; database chỉ lưu object key và metadata. | Bất biến thiết kế | Tối ưu hóa database performance |
| BR-MED-003 | Ảnh upload phải được quét virus/malware và kiểm tra MIME type thực tế trước khi hiển thị công khai. | Đề xuất bảo mật | Bảo đảm an toàn nền tảng |
| BR-PRO-001 | Nghiêm cấm tuyệt đối niêm yết vũ khí, chất cháy nổ, ma túy, động vật hoang dã, hàng giả và thuốc kê đơn. | Bất biến pháp lý | Tuân thủ quy định pháp luật |
| BR-PRO-002 | Hàng điện tử/công nghệ cũ bắt buộc phải khai báo số IMEI/Serial (nếu có) và mô tả trung thực khuyết tật. | Đề xuất cần duyệt | Bảo vệ người mua hàng cũ |
| BR-PRO-003 | Tin đăng chứa từ khóa trong Blacklist sẽ bị chặn đăng tự động hoặc chuyển sang hàng đợi duyệt thủ công. | Đề xuất cần duyệt | Kiểm soát rủi ro vi phạm |

## 4. Tài khoản và quyền

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-ACT-001 | Tài khoản bị khóa hoặc không đủ điều kiện không được tạo listing/order theo quyền bị đình chỉ. | Đề xuất cần duyệt | Cần định nghĩa trạng thái account |
| BR-ACT-002 | Buyer/seller chỉ xem dữ liệu giao dịch thuộc phạm vi được phép. | Định hướng bảo mật | Xác nhận role/field level |
| BR-ACT-003 | Moderator/support chỉ truy cập dữ liệu cần thiết cho case được giao. | Đề xuất cần duyệt | Least privilege, audit |
| BR-ACT-004 | Thao tác nhạy cảm phải có danh tính người thực hiện, thời điểm, đối tượng, lý do và kết quả. | Đề xuất cần duyệt | Xác định audit retention |
| BR-ACT-005 | Chia sẻ địa chỉ/số điện thoại với carrier/đối tác phải phục vụ mục đích giao dịch và giới hạn người nhận. | Cần quyết định | Consent, masking và retention |

## 5. Checkout, giữ hàng và order

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-ORD-001 | Khả dụng phải được xác nhận tại checkout bởi năng lực sở hữu listing/inventory; search index không phải nguồn chuẩn. | Bất biến thiết kế đề xuất | Tránh mua hàng đã bán |
| BR-ORD-002 | Cùng listing đơn chiếc không được có hai reservation/order hợp lệ đồng thời. | Đề xuất cần duyệt | Atomicity, cạnh tranh checkout |
| BR-ORD-003 | Mọi checkout/request có thể gửi lại cần idempotency để không nhân đôi order hoặc side effect. | Bất biến thiết kế đề xuất | Timeout/retry |
| BR-ORD-004 | Order lưu snapshot giá, mô tả tối thiểu và thuộc tính quan trọng tại thời điểm xác nhận. | Đề xuất cần duyệt | Lịch sử không đổi theo listing |
| BR-ORD-005 | Chuyển trạng thái order chỉ qua transition hợp lệ; ghi nguồn sự kiện/hành động. | Đề xuất cần duyệt | State machine trong BA 05/06 |
| BR-ORD-006 | Order chỉ vào fulfillment khi đạt điều kiện payment/COD đã được policy định nghĩa. | Cần quyết định | Phụ thuộc payment model |
| BR-ORD-007 | Quy tắc hủy phải xác định bên yêu cầu, trạng thái cho phép, thời hạn và tác động reservation/payment/shipping. | Cần quyết định | Hủy theo từng giai đoạn |
| BR-ORD-008 | Order chứa nhiều listing/seller chỉ hỗ trợ khi có quy tắc tách payment, shipment, cancellation và refund rõ ràng. | Cần quyết định | Khuyến nghị xác định MVP |
| BR-ORD-009 | Thời hạn giữ hàng và hành vi giải phóng phải được cấu hình theo policy, không hard-code trước khi có quyết định. | Cần quyết định | Cần owner và thời hạn |
| BR-ORD-010 | Không hoàn tất order chỉ dựa trên một trạng thái search/cache hoặc callback chưa xác minh. | Bất biến thiết kế đề xuất | Nguồn chuẩn và kiểm chứng |
| BR-ORD-011 | Giỏ hàng (Cart) chỉ lưu ý định mua phía client/service; hệ thống KHÔNG giữ hàng khi sản phẩm chỉ mới ở trong giỏ. | Bất biến thiết kế | Tránh chiếm giữ hàng ảo |
| BR-ORD-012 | Thao tác Giữ hàng (Reservation) chỉ kích hoạt khi người mua bấm "Thanh toán/Checkout" và chuyển sang màn hình trả tiền. | Bất biến thiết kế | Atomic lock trên Listing |
| BR-ORD-013 | Thời hạn giữ hàng (Hold Window) mặc định là 15 phút. Quá thời hạn này mà chưa thanh toán xong, hệ thống tự động giải phóng hàng về trạng thái Published. | Đề xuất cần duyệt | Tránh treo hàng vô hạn |

## 6. Payment và refund

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-PAY-001 | Mỗi payment attempt gắn với order và correlation/reference duy nhất. | Đề xuất cần duyệt | Đối soát và audit |
| BR-PAY-002 | Callback phải được xác minh nguồn, chữ ký và dữ liệu trọng yếu theo hợp đồng provider. | Đề xuất cần duyệt | Chưa chọn provider |
| BR-PAY-003 | Callback/request trùng không được tạo side effect lặp. | Bất biến thiết kế đề xuất | Idempotent handling |
| BR-PAY-004 | Timeout không chứng minh payment thất bại; trạng thái chưa rõ cần được đối soát. | Bất biến thiết kế đề xuất | Tránh charge kép/giao hàng sai |
| BR-PAY-005 | Số tiền/currency xác nhận phải khớp quote/order theo quy tắc làm tròn và phí đã công bố. | Cần quyết định | Currency, rounding, fees |
| BR-PAY-006 | Chỉ role/quy trình được ủy quyền mới có thể khởi tạo refund; lưu lý do và amount. | Đề xuất cần duyệt | Approval threshold chưa có |
| BR-PAY-007 | Refund có thể toàn phần hoặc một phần chỉ khi chính sách xác định giới hạn và trạng thái hợp lệ. | Cần quyết định | Refund policy |
| BR-PAY-008 | ReMarket không mặc định lưu dữ liệu thẻ nhạy cảm; chọn provider và phạm vi compliance cần được xác nhận. | Định hướng an toàn cần duyệt | Payment architecture |
| BR-PAY-009 | Chênh lệch payment/order/provider tạo trạng thái reconciliation case, không tự xóa lịch sử. | Đề xuất cần duyệt | Quy trình finance/support |
| BR-PAY-010 | Tiền thanh toán của người mua được thu hộ và lưu ký tại tài khoản trung gian của cổng thanh toán. | Đề xuất cần duyệt | Mô hình thanh toán sàn C2C |
| BR-PAY-011 | Lệnh giải ngân (Payout) cho người bán chỉ được kích hoạt sau khi đơn hàng chuyển sang trạng thái Completed (hết hạn khiếu nại). | Đề xuất cần duyệt | Bảo vệ người mua đồ cũ |
| BR-PAY-012 | Đối soát và tạo batch chuyển khoản giải ngân được thực hiện định kỳ (ví dụ T+3 sau khi đơn hoàn tất) qua chuyển khoản ngân hàng. | Cần quyết định | Chu kỳ tài chính |

## 7. Shipping, hoàn tất và hậu giao dịch

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-SHP-001 | Trạng thái hãng vận chuyển được map sang bộ trạng thái chuẩn của ReMarket; lưu provider reference để truy nguyên. | Đề xuất cần duyệt | Hãng chưa chọn |
| BR-SHP-002 | Webhook cần xác thực, idempotency và xử lý sai thứ tự/trễ. | Bất biến thiết kế đề xuất | Mạng/provider có thể lặp |
| BR-SHP-003 | Không xem đơn đã giao nếu thiếu bằng chứng/trạng thái đạt tiêu chí được duyệt. | Cần quyết định | Carrier signal/manual confirmation |
| BR-SHP-004 | Định nghĩa ai trả phí, thời điểm lấy quote, phí phát sinh và bên chịu rủi ro giao nhận. | Cần quyết định | Marketplace policy |
| BR-SHP-005 | Delivery failure/returned/lost phải kích hoạt quy trình case/đối soát; không tự hoàn tất order. | Đề xuất cần duyệt | Refund/return policy |
| BR-REV-001 | Review chỉ gắn với giao dịch đủ điều kiện theo policy. | Đề xuất cần duyệt | Eligibility/window/moderation |
| BR-REV-002 | Một buyer không thể tạo nhiều review làm sai lệch cùng một giao dịch nếu chưa có policy cho chỉnh sửa. | Cần quyết định | Edit/reply rules |

## 7.1. Tìm kiếm và khám phá (Search & Discovery)

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-SRC-001 | Search Projection là dữ liệu dẫn xuất (derived), độ trễ đồng bộ tối đa cho phép là X giây; không dùng Search làm nguồn quyết định cho checkout. | Bất biến thiết kế | Đảm bảo đúng đắn tồn kho |
| BR-SRC-002 | Khi Listing chuyển sang Reserved, Sold, Hidden hoặc Removed, Search Index phải cập nhật loại bỏ khỏi kết quả tìm kiếm thông thường. | Đề xuất cần duyệt | Tránh người mua click vào tin hết hàng |
| BR-SRC-003 | Bộ lọc tìm kiếm hỗ trợ đa thuộc tính: Danh mục, Tình trạng (Mới/Like new/Cũ), Khoảng giá, Vị trí người bán và Sắp xếp (Mới nhất/Giá thấp nhất). | Đề xuất cần duyệt | Nhu cầu người mua |
| BR-SRC-004 | Nếu người mua checkout một món hàng vừa bị mua trước đó do trễ chỉ mục, hệ thống phải thông báo lịch sự và gợi ý sản phẩm tương tự. | Đề xuất cần duyệt | Trải nghiệm người dùng |

## 7.2. Thông báo (Notification)

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-NTF-001 | Mọi thay đổi trạng thái đơn hàng (Confirmed, Shipped, Delivered, Cancelled) đều phải kích hoạt thông báo tự động tới người mua và người bán. | Đề xuất cần duyệt | Minh bạch giao dịch |
| BR-NTF-002 | Người dùng được tùy chỉnh bật/tắt thông báo tiếp thị, nhưng không được tắt thông báo bảo mật (OTP, đổi mật khẩu) và trạng thái đơn hàng. | Bất biến nghiệp vụ | Bảo mật và cam kết dịch vụ |
| BR-NTF-003 | Tin nhắn SMS/Push tiếp thị phải tuân thủ khung giờ yên tĩnh (Quiet Hours từ 22h00 đến 08h00); tin nhắn OTP/bảo mật gửi tức thời 24/7. | Đề xuất cần duyệt | Tránh làm phiền người dùng |
| BR-NTF-004 | Lỗi gửi thông báo qua đối tác thứ ba (SMS/Email Gateway) phải tự động retry với exponential backoff và không được làm gián đoạn luồng chính của đơn hàng. | Bất biến thiết kế | Tách biệt độ tin cậy |

## 8. Tranh chấp, đổi trả và khiếu nại (Disputes & Returns)

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-DSP-001 | Người mua có quyền mở khiếu nại trong vòng 48 giờ kể từ thời điểm đơn hàng chuyển sang Delivered nếu hàng không đúng mô tả hoặc hư hỏng. | Đề xuất cần duyệt | Chính sách bảo vệ người mua |
| BR-DSP-002 | Khi có khiếu nại, đơn hàng lập tức chuyển sang trạng thái Disputed; khoản giải ngân cho người bán bị khóa tự động. | Bất biến nghiệp vụ | Đóng băng tiền để xử lý |
| BR-DSP-003 | Cả người mua và người bán phải cung cấp hình ảnh/video bằng chứng trong vòng 24 giờ kể từ khi được nhân viên hỗ trợ yêu cầu. | Đề xuất cần duyệt | Căn cứ hòa giải |
| BR-DSP-004 | Quyết định hòa giải có thể là: Hoàn tiền toàn phần & trả hàng, Hoàn tiền một phần giữ hàng, hoặc Bác bỏ khiếu nại (tiếp tục giải ngân cho seller). | Đề xuất cần duyệt | Các kịch bản hòa giải |

## 9. Moderation và hỗ trợ

| ID | Quy tắc dự kiến | Trạng thái | Lý do/điểm cần chốt |
|---|---|---|---|
| BR-MOD-001 | Mọi report/case có ID, loại, thời điểm, trạng thái và liên kết tới đối tượng liên quan. | Đề xuất cần duyệt | Hỗ trợ điều tra |
| BR-MOD-002 | Quyết định moderation lưu reason code, actor, bằng chứng tham chiếu và hành động. | Đề xuất cần duyệt | Nhất quán và appeal |
| BR-MOD-003 | Admin/support không ghi trực tiếp database nghiệp vụ để vượt qua quy tắc service. | Bất biến thiết kế | Giữ audit và integrity |
| BR-MOD-004 | Hành động ảnh hưởng order/payment cần quyền riêng, xác nhận và audit phù hợp mức rủi ro. | Cần quyết định | Approval matrix |
| BR-MOD-005 | Appeal, thời hạn phản hồi, bằng chứng và thông báo cho người bị ảnh hưởng cần policy. | Cần quyết định | Chưa có moderation policy |
| BR-MOD-006 | Xóa/ẩn nội dung không tự xóa lịch sử giao dịch hoặc case đang mở. | Đề xuất cần duyệt | Điều tra và retention |

## 10. Dữ liệu, tích hợp và vận hành

Đây là nguyên tắc hệ thống để phân tích/thiết kế, cần ánh xạ sang yêu cầu và kiến trúc chi tiết. Chúng không thay thế policy nghiệp vụ.

| ID | Nguyên tắc/guardrail | Trạng thái | Hệ quả |
|---|---|---|---|
| BR-SYS-001 | Mỗi service có quyền sở hữu dữ liệu; service khác dùng API/event, không truy cập database nội bộ trực tiếp. | Bất biến thiết kế | Ranh giới dữ liệu rõ |
| BR-SYS-002 | Search index/cache là dữ liệu dẫn xuất; có thể dựng lại từ nguồn chuẩn và phải có cơ chế freshness/eviction. | Bất biến thiết kế | Không dùng cho quyết định giao dịch cuối |
| BR-SYS-003 | SQL Server, MongoDB, cache, load balancer và S3 là lựa chọn công nghệ ưu tiên cần đánh giá theo workload. | Định hướng đã nêu | Chưa chọn hosting/version/topology |
| BR-SYS-004 | Mỗi datastore cần xác định owner, backup, retention, mã hóa, quyền restore và quy trình kiểm chứng. | Đề xuất bắt buộc cho thiết kế | Nối BA 03/06 |
| BR-SYS-005 | Multi-AZ/replication không thay thế backup độc lập có khả năng khôi phục điểm thời gian. | Guardrail phục hồi | Tách HA khỏi backup |
| BR-SYS-006 | RPO/RTO đặt theo tác động nghiệp vụ và được owner phê duyệt; không tự giả định giá trị. | Cần quyết định | So sánh chi phí/khả năng |
| BR-SYS-007 | Backup/restore phải tính tới dữ liệu, object, encryption key, secrets, schema, event và thứ tự khôi phục. | Đề xuất | Khôi phục xuyên dịch vụ |
| BR-SYS-008 | Restore drill phải xác nhận tính toàn vẹn và journey nghiệp vụ, không chỉ xác nhận job đã chạy. | Đề xuất | Bằng chứng recoverability |
| BR-SYS-009 | Secret, credential và key không ghi vào tài liệu BA/log; dùng cơ chế quản lý bí mật phù hợp. | Bất biến bảo mật | Tối thiểu hóa rủi ro |
| BR-SYS-010 | Retry có giới hạn, backoff, dead-letter/parking và cảnh báo; thao tác phải an toàn khi lặp. | Đề xuất | Không tạo side effect lặp |
| BR-SYS-011 | Schema/event có version và cách xử lý tương thích/migration/rollback. | Đề xuất | Tránh làm hỏng consumer cũ |
| BR-SYS-012 | Sự cố restore/migration ghi lại người phê duyệt, thời điểm, nguồn backup, dữ liệu thiếu và reconciliation. | Đề xuất | Audit vận hành |

## 11. Quy tắc ưu tiên xử lý

Khi các quy tắc xung đột, chưa tự quyết định theo thứ tự này mà phải chuyển thành decision record. Gợi ý thứ tự để workshop: bảo vệ người dùng và giao dịch; đúng đắn dữ liệu tiền/đơn; quyền riêng tư và tuân thủ; tính toàn vẹn hàng; khả năng vận hành/khôi phục; trải nghiệm và tối ưu chi phí. Business owner có quyền chốt policy, Security/Legal xác nhận các ràng buộc thuộc trách nhiệm của họ.

## 12. Mẫu phê duyệt quy tắc

Với mỗi ID khi chốt, ghi: policy statement cuối, trạng thái, owner, người duyệt, ngày hiệu lực, nguồn/bằng chứng, phạm vi áp dụng, ngoại lệ, yêu cầu liên quan, hệ thống/service chịu trách nhiệm và lịch sử thay đổi. Không đổi trạng thái thành Đã duyệt nếu thiếu owner hoặc nguồn phê duyệt.

## 13. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo baseline quy tắc đề xuất và guardrail; chưa có policy nghiệp vụ nào được đánh dấu đã duyệt. |
