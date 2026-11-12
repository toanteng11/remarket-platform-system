# User Stories – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Loại tài liệu | User Stories (US) |
| Phiên bản | 1.0 |
| Trạng thái | Backlog đề xuất; ưu tiên chờ Product Owner duyệt |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | functional-requirements.md, use-cases.md, acceptance-criteria.md, 01/user-needs-and-pain-points.md |

## 1. Quy ước

- Mẫu: **Là** `<vai trò>`, **tôi muốn** `<mục tiêu>`, **để** `<giá trị>`.
- ID: `US-<epic>-<số>`. Epic: ACC, LST, SRC, CHK, PAY, SHP, NTF, TRU, DSP, SUP, ADM, OPS.
- Ưu tiên MoSCoW (đề xuất). **Kích cỡ**: S/M/L tương đối, chưa phải ước lượng công sức chính thức.
- Mỗi story trỏ tới: `NEED-*`, `FR-*`, `UC-*`, `AC-*` (tiêu chí chấp nhận ở `acceptance-criteria.md`).
- Story kỹ thuật/nền tảng (OPS) viết theo vai trò người vận hành để vẫn nối được giá trị nghiệp vụ.
- Definition of Ready: có owner, AC, phụ thuộc chính sách đã rõ hoặc ghi giả định. Definition of Done: đạt AC, kiểm thử luồng lỗi/idempotency/quyền liên quan, có log/metric cần thiết.

## 2. Epic ACC – Tài khoản và quyền

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-ACC-01 | Là khách, tôi muốn đăng ký và xác minh tài khoản, để mua hoặc bán trên ReMarket. | M | M | FR-ACC-01; UC-01; AC-ACC-01 |
| US-ACC-02 | Là người dùng, tôi muốn đăng nhập và đăng xuất an toàn, để bảo vệ tài khoản của mình. | M | M | FR-ACC-02..03; UC-02; AC-ACC-02 |
| US-ACC-03 | Là người dùng, tôi muốn khôi phục mật khẩu qua kênh đã xác minh, để lấy lại quyền truy cập. | M | S | FR-ACC-04; AC-ACC-03 |
| US-ACC-04 | Là người mua, tôi muốn quản lý sổ địa chỉ giao hàng, để đặt hàng nhanh. | M | S | FR-ACC-06; UC-03; AC-ACC-04 |
| US-ACC-05 | Là người dùng, tôi muốn dùng một tài khoản vừa mua vừa bán, để không phải tạo nhiều tài khoản. | M | M | FR-ACC-05; BRQ-ACC-02; AC-ACC-05 |
| US-ACC-06 | Là người dùng, tôi muốn thông tin cá nhân của mình chỉ được người cần thiết nhìn thấy, để tôi yên tâm về quyền riêng tư. | M | M | FR-ACC-07; NEED-BUY-08; AC-ACC-06 |
| US-ACC-07 | Là quản trị viên, tôi muốn hạn chế hoặc khóa tài khoản vi phạm kèm lý do, để bảo vệ cộng đồng. | M | M | FR-ACC-08, FR-ADM-05; AC-ACC-07 |
| US-ACC-08 | Là người bán/nhân viên, tôi muốn bật xác thực hai yếu tố, để giảm rủi ro bị chiếm tài khoản. | S | M | FR-ACC-09; AC-ACC-08 |

## 3. Epic LST – Tin đăng

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-LST-01 | Là người bán, tôi muốn tạo tin với mô tả tình trạng có hướng dẫn, để người mua hiểu đúng món hàng. | M | L | FR-LST-01..04; NEED-SELL-01; UC-04; AC-LST-01 |
| US-LST-02 | Là người bán, tôi muốn tải nhiều ảnh và thử lại ảnh lỗi, để không phải làm lại từ đầu. | M | M | FR-MED-01..06; AC-LST-02 |
| US-LST-03 | Là người bán, tôi muốn xem trước và lưu nháp tin, để hoàn chỉnh trước khi đăng. | M | S | FR-LST-04; AC-LST-03 |
| US-LST-04 | Là người bán, tôi muốn biết lý do khi tin bị chặn hoặc từ chối, để tôi sửa lại. | M | S | FR-LST-05; AC-LST-04 |
| US-LST-05 | Là người bán, tôi muốn sửa giá/mô tả, tạm ẩn, đánh dấu đã bán hoặc gỡ tin, để thông tin luôn đúng. | M | M | FR-LST-07..09; NEED-SELL-02; UC-05; AC-LST-05 |
| US-LST-06 | Là người bán, tôi muốn hệ thống chặn thao tác gây xung đột khi tin đang được giữ, để không phá đơn của người mua. | M | M | FR-LST-08; EX-004; AC-LST-06 |
| US-LST-07 | Là người mua, tôi muốn ảnh tải lên được kiểm tra an toàn, để không gặp nội dung độc hại. | M | M | FR-MED-02..04; AC-LST-07 |
| US-LST-08 | Là người bán, tôi muốn được nhắc khai báo thuộc tính bắt buộc theo từng danh mục (ví dụ IMEI cho điện tử), để tin đủ thông tin. | S | M | FR-LST-02; BR-PRO-002; AC-LST-08 |

## 4. Epic SRC – Tìm kiếm và khám phá

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-SRC-01 | Là khách, tôi muốn tìm theo từ khóa và danh mục mà không cần đăng nhập, để xem trước khi quyết định đăng ký. | M | M | FR-SRC-01..04; UC-06; AC-SRC-01 |
| US-SRC-02 | Là người mua, tôi muốn lọc theo tình trạng, giá và thuộc tính, để tìm món phù hợp ngân sách. | M | M | FR-SRC-02..03; NEED-BUY-02; AC-SRC-02 |
| US-SRC-03 | Là người mua, tôi muốn xem ảnh, tình trạng, khuyết điểm và người bán trên trang chi tiết, để đánh giá trước khi mua. | M | M | FR-SRC-07; NEED-BUY-01; AC-SRC-03 |
| US-SRC-04 | Là người mua, tôi muốn không thấy tin đã bán hoặc đang giữ trong kết quả, để không mất thời gian. | M | M | FR-SRC-05; BR-SRC-002; AC-SRC-04 |
| US-SRC-05 | Là người mua, tôi muốn biết khi tin tôi bấm vừa hết hàng và được gợi ý tin tương tự, để tiếp tục tìm. | M | S | FR-CHK-11; BR-SRC-004; AC-SRC-05 |
| US-SRC-06 | Là người vận hành, tôi muốn xem độ trễ đồng bộ Listing→Search và dựng lại chỉ mục khi cần, để kết quả tìm kiếm đáng tin. | M | M | FR-SRC-06; FR-OPS-08; AC-SRC-06 |

## 5. Epic CHK – Checkout, giữ hàng, đơn

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-CHK-01 | Là người mua, tôi muốn hệ thống xác nhận món còn mua được ngay khi checkout, để không đặt phải hàng đã bán. | M | L | FR-CHK-03; NEED-BUY-03; UC-07; AC-CHK-01 |
| US-CHK-02 | Là người bán, tôi muốn một món đơn chiếc không bị bán cho hai người cùng lúc, để tránh hủy đơn và mất uy tín. | M | L | FR-CHK-04; NEED-SELL-03; EX-002; AC-CHK-02 |
| US-CHK-03 | Là người mua, tôi muốn thấy đếm ngược thời gian giữ hàng, để biết còn bao lâu để thanh toán. | M | S | FR-CHK-05; AC-CHK-03 |
| US-CHK-04 | Là người mua, tôi muốn hàng được tự động giải phóng khi tôi không thanh toán kịp, để người khác có cơ hội mua. | M | M | FR-CHK-06; FR-ORD-07; AC-CHK-04 |
| US-CHK-05 | Là người mua, tôi muốn bấm gửi nhiều lần hoặc mạng chập chờn mà không tạo đơn trùng, để tránh rắc rối. | M | M | FR-CHK-07; EX-003; AC-CHK-05 |
| US-CHK-06 | Là người mua, tôi muốn thấy rõ giá hàng, phí giao và tổng tiền trước khi đặt, để không bị phát sinh bất ngờ. | M | M | FR-CHK-08; NEED-BUY-04; AC-CHK-06 |
| US-CHK-07 | Là người mua, tôi muốn được hỏi lại khi giá/tình trạng tin vừa thay đổi, để quyết định đúng. | M | S | FR-CHK-10; EX-004; AC-CHK-07 |
| US-CHK-08 | Là người mua, tôi muốn xem lịch sử và timeline đơn của tôi, để biết đơn đang ở đâu. | M | M | FR-ORD-09..10; NEED-BUY-05; AC-CHK-08 |
| US-CHK-09 | Là người mua, tôi muốn hủy đơn khi còn được phép, để không phải nhận hàng không cần. | M | M | FR-ORD-05; UC-10; AC-CHK-09 |
| US-CHK-10 | Là người bán, tôi muốn xem đơn bán của tôi và phân biệt đơn đã xác nhận với đơn đang chờ thanh toán, để biết khi nào cần làm gì. | M | M | FR-ORD-09; NEED-SELL-04; AC-CHK-10 |
| US-CHK-11 | Là người mua, tôi muốn đơn lưu lại thông tin sản phẩm và giá lúc mua, để có bằng chứng nếu có tranh chấp. | M | S | FR-ORD-02; AC-CHK-11 |

## 6. Epic PAY – Thanh toán, hoàn tiền, giải ngân

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-PAY-01 | Là người mua, tôi muốn thanh toán trực tuyến và thấy kết quả rõ ràng, để biết đơn đã được xác nhận chưa. | M | L | FR-PAY-01..07; UC-08..09; AC-PAY-01 |
| US-PAY-02 | Là người mua, tôi muốn thấy trạng thái "đang xử lý" thay vì lỗi khi cổng chậm, để không thanh toán lần hai. | M | M | FR-PAY-05; EX-005; AC-PAY-02 |
| US-PAY-03 | Là người mua, tôi muốn không bị tính tiền hai lần khi kết quả được gửi lặp, để tiền của tôi an toàn. | M | M | FR-PAY-04; EX-006; AC-PAY-03 |
| US-PAY-04 | Là người mua, tôi muốn thử lại thanh toán trong thời gian giữ hàng, để hoàn tất nếu lần đầu thất bại. | S | S | FR-PAY-09; AC-PAY-04 |
| US-PAY-05 | Là người mua, tôi muốn nhận hoàn tiền đúng chính sách khi đơn bị hủy hoặc tranh chấp, để không bị mất tiền. | S | L | FR-PAY-10; UC-17; AC-PAY-05 |
| US-PAY-06 | Là nhân viên tài chính, tôi muốn đối soát giao dịch với cổng thanh toán và xử lý chênh lệch, để số liệu chính xác. | S | L | FR-PAY-11; UC-19; AC-PAY-06 |
| US-PAY-07 | Là hệ thống, tôi muốn giữ bằng chứng thanh toán thành công khi cập nhật đơn lỗi và phát lại sự kiện, để đơn không bị đánh dấu thất bại giả. | M | M | FR-PAY-08; EX-008; AC-PAY-07 |
| US-PAY-08 | Là người bán, tôi muốn nhận giải ngân sau khi đơn hoàn tất và không bị chi trả khi có khiếu nại, để thu tiền đúng và công bằng. | S | L | FR-PAY-13; UC-20; AC-PAY-08 |
| US-PAY-09 | Là người mua, tôi muốn thông tin thẻ của tôi không bị lưu trên ReMarket, để an toàn. | M | S | FR-PAY-12; AC-PAY-09 |
| US-PAY-10 | Là người mua, tôi muốn biết khi tôi bị trừ tiền nhưng đơn chưa hiện, và được mở hồ sơ hỗ trợ, để được xử lý. | M | M | EX-021; FR-PAY-05, FR-SUP-01; AC-PAY-10 |

## 7. Epic SHP – Giao nhận

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-SHP-01 | Là người bán, tôi muốn hệ thống tạo vận đơn và cho biết cách bàn giao, để giao hàng đúng hạn. | M | L | FR-SHP-01..03, FR-SHP-08; UC-11; AC-SHP-01 |
| US-SHP-02 | Là người mua, tôi muốn theo dõi trạng thái giao hàng dễ hiểu bất kể hãng nào, để biết khi nào nhận hàng. | M | M | FR-SHP-04..06; UC-12; AC-SHP-02 |
| US-SHP-03 | Là hệ thống, tôi muốn xử lý thông báo trạng thái trùng, trễ hoặc sai thứ tự từ hãng, để trạng thái luôn đúng. | M | M | FR-SHP-06; EX-011; AC-SHP-03 |
| US-SHP-04 | Là hệ thống, tôi muốn không tạo vận đơn trùng khi hãng không phản hồi, để tránh phát sinh phí. | M | M | FR-SHP-07; EX-010; AC-SHP-04 |
| US-SHP-05 | Là người mua/bán, tôi muốn được thông báo khi giao thất bại hoặc hoàn hàng và có đường xử lý, để không bị bỏ rơi. | M | M | FR-SHP-10; EX-012; AC-SHP-05 |
| US-SHP-06 | Là nhóm tích hợp, tôi muốn thêm một hãng vận chuyển mới mà không đổi nghiệp vụ lõi, để mở rộng lựa chọn giao hàng. | S | L | FR-SHP-03; DEC-DIR-05; AC-SHP-06 |
| US-SHP-07 | Là người dùng, tôi muốn địa chỉ và số điện thoại chỉ chia sẻ cho người cần để giao hàng, để bảo vệ thông tin. | M | S | FR-SHP-09; NEED-SELL-06; AC-SHP-07 |
| US-SHP-08 | Là nhân viên hỗ trợ, tôi muốn hệ thống tự đối soát với hãng khi trạng thái không rõ, để bớt xử lý thủ công. | S | M | FR-SHP-12; UC-26; AC-SHP-08 |

## 8. Epic NTF – Thông báo

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-NTF-01 | Là người dùng, tôi muốn nhận thông báo khi đơn, thanh toán, giao hàng thay đổi, để theo dõi giao dịch. | M | M | FR-NTF-01..03; UC-21; AC-NTF-01 |
| US-NTF-02 | Là người dùng, tôi muốn tùy chọn tắt thông báo tiếp thị nhưng vẫn nhận thông báo đơn và bảo mật, để không bị làm phiền. | S | S | FR-NTF-04; AC-NTF-02 |
| US-NTF-03 | Là người dùng, tôi muốn không nhận tin tiếp thị trong giờ yên tĩnh, để không bị quấy rầy. | S | S | FR-NTF-05; AC-NTF-03 |
| US-NTF-04 | Là hệ thống, tôi muốn lỗi gửi thông báo không làm hỏng đơn hàng và được thử lại, để giao dịch luôn tiến triển. | M | M | FR-NTF-06; AC-NTF-04 |
| US-NTF-05 | Là người dùng, tôi muốn xem lại lịch sử thông báo trong ứng dụng, để không bỏ lỡ thông tin. | S | S | FR-NTF-07; AC-NTF-05 |

## 9. Epic TRU – Báo cáo và kiểm duyệt

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-TRU-01 | Là người mua, tôi muốn báo cáo tin hoặc người bán đáng ngờ và nhận mã theo dõi, để nền tảng xử lý. | M | M | FR-TRU-01..02; UC-14; AC-TRU-01 |
| US-TRU-02 | Là moderator, tôi muốn có hàng đợi vụ việc với tin, ảnh và lịch sử trong một nơi, để xử lý nhanh và nhất quán. | M | L | FR-TRU-03, FR-TRU-07; NEED-OPS-01; UC-15; AC-TRU-02 |
| US-TRU-03 | Là moderator, tôi muốn ẩn hoặc gỡ tin kèm lý do, để áp dụng chính sách có thể giải trình. | M | M | FR-TRU-04; AC-TRU-03 |
| US-TRU-04 | Là moderator, tôi muốn việc ẩn/gỡ tin không làm sai lệch đơn/thanh toán đang mở, để giao dịch vẫn đúng. | M | M | FR-TRU-05; NEED-OPS-02; EX-014; AC-TRU-04 |
| US-TRU-05 | Là moderator, tôi muốn chỉ thấy dữ liệu tối thiểu và không thấy thông tin thanh toán, để bảo vệ quyền riêng tư. | M | S | FR-TRU-03; AC-TRU-05 |
| US-TRU-06 | Là người bị gỡ tin, tôi muốn được thông báo lý do và có cách kháng nghị, để công bằng. | S | M | FR-TRU-06; BR-MOD-005; AC-TRU-06 |
| US-TRU-07 | Là người mua, tôi muốn đánh giá người bán sau khi nhận hàng, để giúp người khác chọn. | C | M | FR-REV-01..03; UC-23; AC-TRU-07 |

## 10. Epic DSP – Khiếu nại

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-DSP-01 | Là người mua, tôi muốn mở khiếu nại khi hàng không đúng mô tả trong thời hạn, để được bảo vệ quyền lợi. | S | L | FR-DSP-01; UC-16; AC-DSP-01 |
| US-DSP-02 | Là người mua, tôi muốn tiền của người bán bị giữ lại khi tôi khiếu nại, để việc hoàn tiền khả thi. | S | M | FR-DSP-02; BR-DSP-002; AC-DSP-02 |
| US-DSP-03 | Là nhân viên hỗ trợ, tôi muốn thu thập bằng chứng từ hai bên trong thời hạn và ra quyết định, để giải quyết công bằng. | S | L | FR-DSP-03..04; AC-DSP-03 |
| US-DSP-04 | Là nhân viên hỗ trợ, tôi muốn hoàn tiền một phần hoặc toàn phần theo quyết định có phê duyệt và audit, để đúng quy trình. | S | M | FR-DSP-05, FR-PAY-10; AC-DSP-04 |

## 11. Epic SUP – Hỗ trợ khách hàng

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-SUP-01 | Là nhân viên hỗ trợ, tôi muốn tra cứu một giao dịch và thấy trạng thái Order/Payment/Shipment riêng biệt, để xác định nguyên nhân. | M | L | FR-SUP-01..02; NEED-SUP-01; UC-18; AC-SUP-01 |
| US-SUP-02 | Là nhân viên hỗ trợ, tôi muốn dữ liệu cá nhân được che và chỉ xem rõ khi có lý do được ghi nhận, để không vượt quyền. | M | M | FR-SUP-03; NEED-SUP-03; AC-SUP-02 |
| US-SUP-03 | Là nhân viên hỗ trợ, tôi muốn gửi yêu cầu hủy/hoàn tiền qua quy trình có phê duyệt, để không tự ý tác động tiền. | M | M | FR-SUP-04; NEED-SUP-02; AC-SUP-03 |
| US-SUP-04 | Là nhân viên hỗ trợ, tôi muốn ghi chú, gán và chuyển cấp vụ việc, để phối hợp xử lý. | M | S | FR-SUP-05; AC-SUP-04 |

## 12. Epic ADM – Quản trị

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-ADM-01 | Là quản trị viên, tôi muốn quản lý danh mục và thuộc tính bắt buộc theo danh mục, để tin đăng đúng chuẩn. | S | L | FR-ADM-01; UC-22; AC-ADM-01 |
| US-ADM-02 | Là quản trị viên, tôi muốn quản lý danh sách từ khóa/hàng cấm và các tham số chính sách, để nhanh chóng điều chỉnh khi luật/chính sách đổi. | S | M | FR-ADM-02; AC-ADM-02 |
| US-ADM-03 | Là quản trị viên, tôi muốn mọi thay đổi cấu hình được ghi lịch sử (ai, khi nào, trước/sau), để truy vết. | M | S | FR-ADM-03; AC-ADM-03 |
| US-ADM-04 | Là quản trị viên, tôi muốn xem dashboard nghiệp vụ tổng hợp, để theo dõi sức khỏe của sàn. | C | M | FR-ADM-04; AC-ADM-04 |

## 13. Epic OPS – Vận hành, dữ liệu, phục hồi

| ID | Câu chuyện | Ưu | Cỡ | Truy vết |
|---|---|---|---|---|
| US-OPS-01 | Là người vận hành, tôi muốn truy vết một giao dịch qua mã tương quan xuyên service, để tìm nguyên nhân nhanh. | M | M | FR-OPS-01..02; NFR-OBS-03; AC-OPS-01 |
| US-OPS-02 | Là người vận hành, tôi muốn nhận cảnh báo về lỗi, backlog, hàng đợi lỗi, backup, giao dịch chưa xác định, để xử lý trước khi người dùng chịu ảnh hưởng. | M | M | FR-OPS-02; NFR-OBS-04; AC-OPS-02 |
| US-OPS-03 | Là người vận hành, tôi muốn hệ thống vẫn cho đặt mua khi Search hoặc Notification lỗi, để nền tảng không dừng toàn bộ. | M | L | NFR-AVL-03; AC-OPS-03 |
| US-OPS-04 | Là người vận hành, tôi muốn giám sát backup và biết bản sao gần nhất bao lâu, để phát hiện sớm khi backup trễ. | M | M | FR-OPS-05; NFR-REC-05; AC-OPS-04 |
| US-OPS-05 | Là DBA, tôi muốn khôi phục SQL Server và MongoDB về một thời điểm và S3 về phiên bản trước, để xử lý xóa nhầm hoặc hỏng dữ liệu. | M | L | FR-OPS-06; NFR-REC-03; UC-24; AC-OPS-05 |
| US-OPS-06 | Là người vận hành, tôi muốn sau khôi phục có đối soát và phát lại sự kiện liên service, để dữ liệu nhất quán trước khi mở traffic. | M | L | FR-OPS-07; NFR-REC-09; EX-019; AC-OPS-06 |
| US-OPS-07 | Là người vận hành, tôi muốn diễn tập khôi phục định kỳ và ghi lại RPO/RTO thực tế, để biết backup có dùng được. | M | M | NFR-REC-07; MET-PLAT-05/06; AC-OPS-07 |
| US-OPS-08 | Là người vận hành, tôi muốn dựng lại Search và cache từ nguồn chuẩn, để không cần backup dữ liệu dẫn xuất. | M | M | FR-OPS-08; NFR-REC-10; AC-OPS-08 |
| US-OPS-09 | Là nhân viên bảo mật, tôi muốn thao tác restore và đổi chính sách backup phải có phê duyệt và audit, để tránh lạm quyền. | M | M | FR-OPS-09; NFR-SEC-12; AC-OPS-09 |
| US-OPS-10 | Là nhóm phát triển, tôi muốn sự kiện có phiên bản và consumer idempotent với hàng đợi lỗi, để triển khai không phá consumer cũ. | M | M | FR-OPS-04; NFR-DAT-02; AC-OPS-10 |
| US-OPS-11 | Là nhóm tích hợp, tôi muốn có môi trường giả lập cho thanh toán và vận chuyển, để kiểm thử mà không dùng credential thật. | S | M | NFR-INT-03; AC-OPS-11 |
| US-OPS-12 | Là sponsor, tôi muốn xem chi phí hạ tầng theo service/môi trường và cảnh báo vượt ngân sách, để kiểm soát chi phí. | S | M | NFR-COST-02; NFR-OBS-07; AC-OPS-12 |

## 14. Story map theo journey (tham khảo)

| Giai đoạn | Story chính |
|---|---|
| Khám phá | US-SRC-01..05 |
| Đăng ký/đăng nhập | US-ACC-01..06 |
| Đăng bán | US-LST-01..08 |
| Đặt mua & giữ hàng | US-CHK-01..11 |
| Thanh toán | US-PAY-01..04, 07, 09, 10 |
| Giao hàng | US-SHP-01..05, 07 |
| Sau giao dịch | US-DSP-01..04, US-TRU-07, US-PAY-05, 08 |
| Tin cậy & hỗ trợ | US-TRU-01..06, US-SUP-01..04 |
| Quản trị & vận hành | US-ADM-*, US-OPS-* |

## 15. Gợi ý phân đợt (chưa phải quyết định release)

| Đợt đề xuất | Phạm vi story | Lưu ý |
|---|---|---|
| Đợt 1 – Lõi giao dịch | Tất cả Must ở ACC, LST, SRC, CHK, PAY (01–04, 07, 09, 10), SHP (01–05, 07), NTF (01, 04), SUP, TRU (01–05), OPS (01–10) | Cần chốt Q-BIZ-01..03, Q-OPS-03..05, Q-DR-01 trước khi vào thiết kế chi tiết. |
| Đợt 2 – Hoàn thiện | Should: US-ACC-08, PAY-05, 06, 08, SHP-06, 08, DSP, NTF-02, 03, 05, ADM-01, 02 | Phụ thuộc chính sách hoàn tiền/khiếu nại. |
| Đợt 3 – Mở rộng | Could: US-TRU-07, US-ADM-04, các yêu cầu COD, yêu thích | — |

Phân đợt chi tiết thuộc `07_backlog-and-release`.

## 16. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo backlog user story theo 12 epic, story map và gợi ý phân đợt. |

