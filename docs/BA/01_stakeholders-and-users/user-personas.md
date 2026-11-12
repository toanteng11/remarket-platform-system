# User Personas – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Giả thuyết để xác nhận; chưa dựa trên nghiên cứu người dùng |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | stakeholder-register.md, user-needs-and-pain-points.md, user-journeys.md |

## 1. Cách sử dụng persona

Các persona dưới đây là **mẫu người dùng giả định** để tổ chức câu hỏi phân tích, thiết kế hành trình và kiểm tra yêu cầu. Chúng không đại diện cho người đã được phỏng vấn, cũng không khẳng định hành vi thị trường đã được xác minh.

- Không dùng persona như bằng chứng thống kê hoặc căn cứ duy nhất để chốt chính sách.
- Các chi tiết về tuổi, thu nhập, nghề nghiệp, vùng miền hoặc thiết bị không được giả định khi chưa có dữ liệu.
- Seller doanh nghiệp/chuyên nghiệp là persona cần xác nhận; nếu ngoài phạm vi thì không đưa vào MVP.
- Cần kiểm chứng nhu cầu về thanh toán, hoàn trả, giao hàng, kiểm duyệt, tin cậy và accessibility với người thật.

## 1.1. Persona P-00 – Khách vãng lai (Guest / Unauthenticated User)

**Mục tiêu:** Khám phá nhanh các sản phẩm cũ theo danh mục/từ khóa, kiểm tra giá và độ tin cậy của sàn trước khi quyết định tạo tài khoản hoặc đăng nhập.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Người dùng chưa đăng nhập truy cập Marketplace Web/App. |
| Ngữ cảnh | Tìm kiếm qua Google hoặc mạng xã hội dẫn đến trang chi tiết sản phẩm hoặc trang chủ. |
| Nhu cầu hệ thống | Xem tin đăng, xem ảnh chất lượng tốt, tìm kiếm và lọc mà không bị bắt buộc đăng nhập; chỉ yêu cầu đăng nhập khi ấn nút Mua/Chat. |
| Mối quan tâm | Tốc độ tải trang, trải nghiệm mượt mà, tính minh bạch về giá và không bị lộ thông tin cá nhân. |

## 2. Persona P-01 – Người mua cá nhân

**Mục tiêu:** tìm món đồ cũ phù hợp nhu cầu/ngân sách, hiểu tình trạng trước khi mua, hoàn tất giao dịch và biết đơn đang ở đâu.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Người mua lướt Marketplace Web/App; có thể là khách chưa đăng nhập hoặc tài khoản đã xác thực. |
| Ngữ cảnh | Tìm theo danh mục/nhu cầu; cân nhắc giá, tình trạng, hình ảnh, người bán và phí giao nhận. |
| Hành vi | So sánh nhiều tin; xem ảnh/mô tả; hỏi thêm khi thông tin thiếu; cân nhắc rủi ro giao dịch. |
| Mục tiêu | Tìm đúng sản phẩm; biết các dấu hiệu sử dụng; thấy tổng chi phí và điều kiện giao dịch; tránh thanh toán/đặt mua trùng. |
| Khó khăn có thể gặp | Ảnh không phản ánh tình trạng; mô tả thiếu chuẩn; món đã bán nhưng còn hiện; phí giao hàng hoặc trạng thái thanh toán không rõ; khó xử lý sự cố. |
| Nhu cầu hệ thống | Tìm kiếm/lọc; thông tin tình trạng có cấu trúc; trạng thái còn bán cập nhật; tổng giá dễ hiểu; checkout xác nhận khả dụng; cập nhật đơn/giao hàng; kênh báo cáo/hỗ trợ. |
| Mối quan tâm tin cậy | Độ đúng của tin, người bán, bảo vệ dữ liệu/payment, cách xử lý đơn lỗi và tranh chấp. |
| Hành trình chính | Tìm kiếm → xem tin → kiểm tra người bán/tình trạng → đặt mua → thanh toán → theo dõi giao hàng → đánh giá hoặc xin hỗ trợ. |
| Điều kiện thành công | Có thể ra quyết định dựa trên thông tin tin đăng; không gặp đơn trùng; hiểu bước tiếp theo sau mỗi hành động. |
| Câu hỏi kiểm chứng | Thông tin tình trạng nào quan trọng nhất? Có cần hỏi người bán? Cách chọn hãng giao? Mong muốn khi payment timeout/đơn hết hạn? |

## 3. Persona P-02 – Người bán cá nhân / bán không thường xuyên

**Mục tiêu:** đăng một vài món hàng đã qua sử dụng nhanh, mô tả trung thực, nhận đơn và hoàn tất giao hàng ít thao tác.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Cá nhân đăng bán tài sản riêng qua Seller Center. |
| Ngữ cảnh | Không nhất thiết quen với thuật ngữ thương mại điện tử; mỗi món có tình trạng riêng, số lượng thường là một. |
| Hành vi | Chụp ảnh bằng điện thoại; nhập tiêu đề/giá/mô tả; cập nhật tin khi đã bán hoặc không còn muốn bán. |
| Mục tiêu | Biết cần khai báo gì; đăng tin không mất nhiều thời gian; tránh bị hỏi lại vì thiếu thông tin; biết cách đóng gói/giao. |
| Khó khăn có thể gặp | Không rõ cách chấm tình trạng; ảnh sai định dạng/thiếu; bị nhiều người mua cùng lúc; không biết đơn đã thanh toán chưa; chọn hãng giao hoặc xử lý giao thất bại khó. |
| Nhu cầu hệ thống | Form đăng tin có hướng dẫn; ảnh upload ổn định; preview; sửa/tạm ẩn tin; khóa/reservation rõ; thông báo đơn; hướng dẫn giao hàng; lịch sử giao dịch. |
| Mối quan tâm | Bảo mật thông tin cá nhân/địa chỉ; tránh phạt nhầm; rõ phí, nghĩa vụ và trạng thái tiền nhận được. |
| Hành trình chính | Tạo tin → mô tả/chụp ảnh → xem trước/công bố → nhận đơn → xác nhận/chuẩn bị → bàn giao hãng giao → theo dõi/hoàn tất. |
| Điều kiện thành công | Đăng được tin đầy đủ; hệ thống không nhận nhiều đơn cho cùng món; seller hiểu trách nhiệm ở từng bước. |
| Câu hỏi kiểm chứng | Người bán có muốn thương lượng không? Có chấp nhận hãng do buyer chọn? Quy trình COD/payout nào mong muốn? |

## 4. Persona P-03 – Người bán chuyên nghiệp / cửa hàng đồ cũ

**Trạng thái:** Persona dự kiến, cần xác nhận seller business có nằm trong thị trường và phạm vi không.

**Mục tiêu:** quản lý nhiều tin/đơn, cập nhật tồn/giá theo lô và theo dõi hoạt động bán có thể lặp lại.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Cửa hàng hoặc người bán có nhiều món/tần suất bán cao. |
| Ngữ cảnh | Có thể cần nhập nhiều tin, phân quyền nhân viên, quản lý danh mục hoặc số lượng. |
| Khó khăn có thể gặp | Tạo tin từng món tốn công; trạng thái giữa kênh không đồng nhất; khó theo dõi nhiều shipment/hoàn tiền; dữ liệu báo cáo không đủ. |
| Nhu cầu tiềm năng | Import hàng loạt/API; dashboard; nhiều người dùng seller; thao tác hàng loạt; báo cáo; chính sách uy tín riêng. |
| Rủi ro nếu đưa vào sớm | Tăng độ phức tạp về seller verification, stock quantities, quyền nhân viên, payout và SLA support. |
| Câu hỏi xác nhận | Seller business có được phép? Mỗi món vẫn là hàng đơn chiếc? Có nhiều chi nhánh/nhân viên? Cần tích hợp POS/ERP không? |

## 5. Persona P-04 – Moderator / quản trị nội dung

**Mục tiêu:** phát hiện và xử lý tin đăng/tài khoản bị báo cáo theo chính sách có thể giải thích, có audit và hạn chế xử lý nhầm.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Nhân viên duyệt tin hoặc xử lý nội dung và báo cáo trong Admin/Back-office. |
| Ngữ cảnh | Có thể xem tin, ảnh, lịch sử báo cáo và quyết định trước; cần xử lý theo mức độ ưu tiên. |
| Khó khăn có thể gặp | Thiếu tiêu chí rõ; báo cáo trùng; khó biết tin đang có đơn; quyết định ẩn tin có thể ảnh hưởng buyer/seller. |
| Nhu cầu hệ thống | Hàng đợi; bộ lọc; trạng thái case; lý do/action codes; audit trail; hướng dẫn escalation; hạn chế PII/payment data. |
| Quyền hạn cần xác nhận | Ẩn/gỡ tin, yêu cầu sửa, khóa tạm tài khoản, khôi phục và xử lý kháng nghị. |
| Điều kiện thành công | Quyết định có lý do, được ghi log và chỉ người đủ quyền thực hiện. |
| Câu hỏi kiểm chứng | Nội dung nào cần kiểm duyệt trước? Có tự động kiểm duyệt không? Ai xử lý appeal? |

## 6. Persona P-05 – Nhân viên hỗ trợ khách hàng

**Mục tiêu:** hiểu nhanh tình trạng giao dịch và hướng dẫn buyer/seller khi đơn, thanh toán hoặc giao hàng có vấn đề.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Tra cứu case trong Back-office; phối hợp Order, Payment và Shipping. |
| Khó khăn có thể gặp | Trạng thái phân tán qua nhiều hệ thống; không biết callback payment/giao hàng đã nhận; xử lý lặp gây hoàn tiền/hủy sai. |
| Nhu cầu hệ thống | Tổng quan đơn có ID liên kết; trạng thái từng domain; timeline sự kiện; case note; hành động được kiểm soát và audit; chỉ xem payment data tối thiểu. |
| Giới hạn quyền | Không sửa trực tiếp database hoặc xem credential; refund/cancel cần quyền và điều kiện phê duyệt. |
| Điều kiện thành công | Tra cứu được nguồn sự thật; biết chủ sở hữu bước tiếp theo; phản hồi người dùng nhất quán. |
| Câu hỏi kiểm chứng | Kênh hỗ trợ nào? Có escalation theo mức độ? Hành động nào cần hai người duyệt? |

## 7. Persona P-06 – Người vận hành nền tảng

**Mục tiêu:** phát hiện lỗi và phục hồi dịch vụ/dữ liệu an toàn, biết mức độ ảnh hưởng tới người mua/người bán.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Cloud/DevOps/SRE/DBA chịu trách nhiệm hạ tầng, service health, backup và restore. |
| Khó khăn có thể gặp | Nhiều service và database; lỗi phụ thuộc; backup chưa chắc restore được; thiếu trace xuyên service; shared infrastructure làm khó cô lập khôi phục. |
| Nhu cầu hệ thống | Dashboards/alerts; runbooks; backup job status; RPO/RTO; restore drills; access theo least privilege; audit; deployment rollback; cost visibility. |
| Điều kiện thành công | Có thể xác định service/data bị ảnh hưởng, chọn runbook và kiểm tra tính toàn vẹn sau restore. |
| Câu hỏi kiểm chứng | Ai trực vận hành? Có môi trường staging? Tần suất diễn tập? RTO/RPO service nào quan trọng nhất? |

## 8. Persona P-07 – Đầu mối đối tác giao nhận

**Mục tiêu:** nhận request đúng chuẩn, xử lý shipment và gửi trạng thái để ReMarket cập nhật chính xác.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Đối tác ngoài như GHTK, GHN, GrabExpress hoặc hãng được chọn sau này; không mặc định dùng Admin/Back-office của ReMarket. |
| Tương tác | API tạo/hủy shipment, báo giá, tracking và webhook/callback. |
| Nhu cầu tích hợp | Credential/API version rõ; idempotent request; quy tắc retry; mapping trạng thái; đối soát khi callback bị trễ/mất. |
| Ranh giới | Đối tác quản lý vận chuyển vật lý; ReMarket Shipping Service quản lý trạng thái nghiệp vụ của shipment trong marketplace. |
| Câu hỏi xác nhận | API/sandbox, chữ ký webhook, rate limits, cancellation rules, SLA, xử lý hàng thất bại và dữ liệu cá nhân. |

## 8.1. Persona P-08 – Quản trị viên kinh doanh (Business Administrator)

**Mục tiêu:** Cấu hình và duy trì cây danh mục sản phẩm, bộ thuộc tính bắt buộc, quy tắc niêm yết, chính sách phí/vận chuyển và theo dõi các chỉ số sức khỏe của sàn.

| Khía cạnh | Mô tả giả thuyết |
|---|---|
| Vai trò | Quản trị viên phụ trách vận hành nghiệp vụ trên Admin/Back-office Portal (ROLE-05). |
| Ngữ cảnh | Cần thêm danh mục mới, điều chỉnh thuộc tính theo mùa hoặc sửa đổi danh sách từ khóa cấm/hàng cấm bán. |
| Khó khăn có thể gặp | Thay đổi danh mục ảnh hưởng đến các tin đăng cũ; thiếu audit trail khi nhiều admin cùng cấu hình; không có preview trước khi áp dụng toàn sàn. |
| Nhu cầu hệ thống | Giao diện quản lý danh mục (taxonomy management); cấu hình trường động theo category; kiểm soát phiên bản cấu hình; audit log ghi lại mọi thao tác sửa đổi. |
| Quyền hạn | Quản lý danh mục, cấu hình hệ thống nghiệp vụ, xem báo cáo tổng hợp; tuyệt đối không có quyền truy cập cơ sở dữ liệu vật lý hay console AWS. |
| Điều kiện thành công | Cập nhật chính sách/danh mục có hiệu lực ngay nhưng không làm gián đoạn các luồng checkout hoặc làm hỏng dữ liệu tin cũ. |

## 9. Danh sách giả thuyết cần kiểm chứng

| Mã | Giả thuyết | Cách kiểm chứng đề xuất |
|---|---|---|
| H-P01 | Mô tả tình trạng và ảnh là yếu tố quyết định đối với buyer đồ cũ. | Phỏng vấn/đi walkthrough với người mua mục tiêu. |
| H-P02 | Người bán cá nhân cần hướng dẫn tình trạng và cách giao hàng. | Quan sát tác vụ tạo listing; thử prototype form. |
| H-P03 | Seller business có thể cần bulk listing và đa người dùng. | Xác nhận phạm vi kinh doanh trước khi đưa vào backlog. |
| H-P04 | Admin cần audit và lý do xử lý tin. | Workshop policy/moderation; rà soát privacy. |
| H-P05 | Support cần nhìn timeline liên service nhưng quyền giới hạn. | Kiểm tra kịch bản dispute và least privilege. |
| H-P06 | Carrier sẽ tích hợp qua API/webhook thay vì portal ReMarket. | Xác nhận mô hình vận hành và đối tác được chọn. |
| H-P07 | Ops cần diễn tập restore và đo RPO/RTO theo database/service. | Xác nhận với DBA/SRE; đưa vào NFR/runbook. |

## 10. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo persona giả thuyết, đánh dấu các điểm cần validation. |
