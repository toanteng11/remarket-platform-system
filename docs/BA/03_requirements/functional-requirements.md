# Functional Requirements – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Loại tài liệu | Functional Requirements (FR) |
| Phiên bản | 1.0 |
| Trạng thái | Bản nháp; thông số định lượng là đề xuất cần Product Owner duyệt |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | business-requirements.md, use-cases.md, user-stories.md, acceptance-criteria.md, 02/business-rules.md |

## 1. Quy ước

- **ID:** `FR-<module>-<số>`. Module: ACC (tài khoản), LST (listing), MED (media), SRC (tìm kiếm), CHK (checkout/giữ hàng), ORD (đơn), PAY (thanh toán), SHP (giao nhận), NTF (thông báo), TRU (report/moderation), DSP (khiếu nại), REV (đánh giá), ADM (quản trị), SUP (hỗ trợ), OPS (vận hành/dữ liệu).
- **Từ khóa:** "phải" = bắt buộc; "nên" = khuyến nghị; "có thể" = tùy chọn.
- **Cột Kênh:** M = Marketplace, S = Seller Center, B = Back-office, SYS = hệ thống/service nội bộ.
- **Ưu tiên:** M/S/C theo MoSCoW. Truy vết tới `BRQ-*` và `BR-*`; kiểm thử ở `acceptance-criteria.md` (AC-*).
- Service ghi theo service map ở 00 (Identity, Profile, Catalog & Listing, Search, Order & Checkout, Payment, Shipping & Fulfillment, Notification, Trust & Moderation).
- Mọi thao tác ghi quan trọng phải có mã tương quan (correlation ID), idempotency khi có thể gửi lặp, và audit khi là hành động nhạy cảm.

## 2. Tài khoản và định danh (FR-ACC) – Identity, Profile

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-ACC-01 | Hệ thống phải cho phép đăng ký tài khoản bằng định danh được chính sách chọn (email/điện thoại/SSO – Q: BA 01 §8) và xác minh định danh trước khi kích hoạt. | M, S | M | BRQ-ACC-01 |
| FR-ACC-02 | Hệ thống phải xác thực đăng nhập, quản lý phiên và cho phép đăng xuất/thu hồi phiên. | M, S, B | M | BRQ-ACC-01 |
| FR-ACC-03 | Hệ thống phải chống dò mật khẩu (giới hạn số lần thử, khóa tạm) và không lưu mật khẩu dạng đọc được. | SYS | M | BRQ-ACC-03 |
| FR-ACC-04 | Hệ thống phải hỗ trợ khôi phục mật khẩu qua kênh đã xác minh. | M, S | M | BRQ-ACC-01 |
| FR-ACC-05 | Người dùng phải xem/sửa hồ sơ cá nhân; một tài khoản có thể kích hoạt vai trò người bán sau khi bổ sung thông tin cần thiết. | M, S | M | BRQ-ACC-02 |
| FR-ACC-06 | Người dùng phải quản lý sổ địa chỉ giao nhận (thêm, sửa, xóa, chọn mặc định). | M | M | BRQ-ACC-01 |
| FR-ACC-07 | Hệ thống phải kiểm tra quyền phía server ở mọi yêu cầu theo vai trò và quyền sở hữu (ownership); mặc định từ chối. | SYS | M | BRQ-ACC-03; BR-ACT-002 |
| FR-ACC-08 | Hệ thống phải hỗ trợ trạng thái tài khoản (Hoạt động, Hạn chế, Khóa, Đã đóng) và chặn tạo listing/order khi tài khoản bị hạn chế/khóa theo phạm vi. | SYS, B | M | BR-ACT-001; EX-013 |
| FR-ACC-09 | Hệ thống nên hỗ trợ xác thực đa yếu tố cho vai trò Seller/Support/Admin (cần xác nhận). | S, B | S | BA 01 §8 |
| FR-ACC-10 | Hệ thống phải ghi audit cho thay đổi vai trò, khóa/mở khóa tài khoản và xem/xuất PII. | B | M | BR-ACT-004 |

## 3. Quản lý tin đăng (FR-LST) – Catalog & Listing

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-LST-01 | Người bán phải tạo tin ở trạng thái Nháp, gắn với người bán sở hữu và một danh mục. | S | M | BR-LST-001 |
| FR-LST-02 | Tin phải có: tiêu đề, danh mục, giá, tình trạng (theo bộ giá trị chuẩn), mô tả, ≥1 ảnh, điều kiện giao hàng; các thuộc tính bắt buộc thay đổi theo danh mục. | S | M | BR-LST-004 |
| FR-LST-03 | Hệ thống phải xác thực dữ liệu bắt buộc và hiển thị lỗi theo từng trường, giữ nguyên dữ liệu đã nhập. | S | M | NEED-SELL-01 |
| FR-LST-04 | Hệ thống phải lưu nháp, cho xem trước và gửi công bố. | S | M | BRQ-MKT-01 |
| FR-LST-05 | Khi gửi công bố, hệ thống phải kiểm tra nội dung theo danh sách từ khóa/hàng cấm; vi phạm thì chặn hoặc chuyển Chờ duyệt theo chính sách. | SYS | M | BR-PRO-003; BR-LST-002 |
| FR-LST-06 | Tin đạt điều kiện chuyển sang Đang bán (Published) và phát sự kiện `ListingPublished`. | SYS | M | BR-LST-002 |
| FR-LST-07 | Người bán phải sửa tin theo trạng thái hợp lệ; thay đổi giá/tình trạng phải lưu lịch sử phiên bản; thay đổi nhạy cảm có thể yêu cầu duyệt lại. | S | M | BR-LST-008 |
| FR-LST-08 | Người bán phải tạm ẩn, hiện lại, đánh dấu đã bán ngoài nền tảng hoặc gỡ tin; hệ thống phải từ chối thao tác xung đột với reservation/order đang mở. | S | M | BR-LST-006; EX-004 |
| FR-LST-09 | Hệ thống phải quản lý các trạng thái tin: Draft, Pending Review, Published, Reserved, Sold, Hidden, Rejected, Removed, Expired và chỉ cho phép chuyển trạng thái hợp lệ. | SYS | M | glossary §3.1 |
| FR-LST-10 | Hệ thống phải tự động chuyển Expired khi tin quá thời hạn niêm yết (thời hạn: cần xác nhận). | SYS | C | glossary §3.1 |
| FR-LST-11 | Mặc định một tin là một món hàng đơn chiếc; khi chuyển Reserved/Sold, tin không còn nhận checkout mới. | SYS | M | A-05; BR-LST-005, BR-LST-007 |
| FR-LST-12 | Hệ thống phải cung cấp API đọc trạng thái khả dụng có thẩm quyền (nguồn chuẩn) cho Order. | SYS | M | BR-ORD-001 |
| FR-LST-13 | Hệ thống phải phát sự kiện thay đổi trạng thái tin (publish, reserve, release, sold, hide, remove) theo cơ chế đảm bảo không mất sự kiện (outbox). | SYS | M | BR-SYS-010 |
| FR-LST-14 | Khi xóa tin/ảnh, hệ thống phải kiểm tra nghĩa vụ giữ bằng chứng (order, report, dispute) trước khi xóa vật lý. | SYS | S | BR-LST-009 |

## 4. Ảnh và tệp (FR-MED)

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-MED-01 | Người bán phải tải 1–10 ảnh/tin; định dạng JPEG/PNG/WebP; ≤10 MB/ảnh (và video bằng chứng ≤50 MB/clip theo `DEC-BIZ-08`). | S | M | BR-MED-001 |
| FR-MED-02 | Hệ thống phải kiểm tra loại tệp thực tế (không chỉ phần mở rộng) và kích thước trước khi chấp nhận. | SYS | M | BR-MED-003 |
| FR-MED-03 | Tệp phải được lưu ở object storage; cơ sở dữ liệu chỉ lưu khóa đối tượng và metadata; tải lên/xem qua URL có thời hạn. | SYS | M | BR-MED-002 |
| FR-MED-04 | Ảnh phải được quét mã độc và chỉ công khai sau khi đạt. | SYS | M | BR-MED-003 |
| FR-MED-05 | Người bán phải sắp xếp thứ tự, chọn ảnh đại diện, xóa ảnh khỏi tin nháp. | S | S | NEED-SELL-01 |
| FR-MED-06 | Khi tải ảnh lỗi, hệ thống phải cho thử lại từng ảnh mà không mất các ảnh đã tải. | S | S | J-02 |

## 5. Tìm kiếm và khám phá (FR-SRC) – Search

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-SRC-01 | Người dùng (kể cả Guest) phải tìm theo từ khóa trên tiêu đề/mô tả. | M | M | BRQ-MKT-03 |
| FR-SRC-02 | Hệ thống phải lọc theo danh mục, thuộc tính, tình trạng, khoảng giá, vị trí người bán (nếu có dữ liệu). | M | M | BR-SRC-003 |
| FR-SRC-03 | Hệ thống phải sắp xếp theo mới nhất, giá tăng/giảm, liên quan. | M | M | BR-SRC-003 |
| FR-SRC-04 | Hệ thống phải phân trang kết quả và thông báo khi không có kết quả kèm gợi ý nới bộ lọc. | M | M | J-01 |
| FR-SRC-05 | Search phải dựng chỉ mục từ sự kiện của Listing; chỉ hiển thị tin Published; loại bỏ tin Reserved/Sold/Hidden/Removed. | SYS | M | BR-SRC-002 |
| FR-SRC-06 | Search phải có khả năng dựng lại toàn bộ chỉ mục từ nguồn chuẩn; theo dõi độ trễ đồng bộ. | SYS | M | BR-SYS-002; MET-SRC-01 |
| FR-SRC-07 | Trang chi tiết tin phải hiển thị ảnh, tình trạng, giá, người bán, thời điểm cập nhật và nút báo cáo. | M | M | NEED-BUY-01 |
| FR-SRC-08 | Kết quả tìm kiếm không được là căn cứ xác nhận mua; checkout luôn xác nhận lại với Listing. | SYS | M | BR-ORD-001; BR-SRC-001 |
| FR-SRC-09 | Người mua có thể lưu tin yêu thích (cần xác nhận phạm vi). | M | C | Q-BIZ-09 |

## 6. Checkout và giữ hàng (FR-CHK) – Order & Checkout, Listing

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-CHK-01 | Người mua phải đăng nhập để checkout; Guest được chuyển tới đăng nhập và quay lại đúng tin. | M | M | J-01 |
| FR-CHK-02 | Hệ thống phải cho thêm tin vào giỏ chỉ như ý định mua; **không giữ hàng** khi ở trong giỏ. | M | S | BR-ORD-011 |
| FR-CHK-03 | Khi bắt đầu checkout, hệ thống phải xác minh với Listing: tin Published, người bán đủ điều kiện, giá hiện hành. | SYS | M | BR-ORD-001 |
| FR-CHK-04 | Hệ thống phải giữ hàng nguyên tử (atomic reservation): với một tin đơn chiếc, chỉ một yêu cầu thành công; yêu cầu còn lại nhận kết quả không khả dụng. | SYS | M | BR-ORD-002; EX-002 |
| FR-CHK-05 | Reservation phải có thời hạn cấu hình được (đề xuất mặc định 15 phút), hiển thị đếm ngược cho người mua. | M, SYS | M | BR-ORD-009, BR-ORD-013 |
| FR-CHK-06 | Hệ thống phải tự giải phóng reservation khi hết hạn, hủy hoặc thanh toán thất bại, đưa tin về Published và phát sự kiện. | SYS | M | BR-ORD-013; EX-007 |
| FR-CHK-07 | Yêu cầu checkout phải mang idempotency key; gửi lặp trả lại cùng kết quả, không tạo order/reservation thứ hai. | SYS | M | BR-ORD-003; EX-003 |
| FR-CHK-08 | Hệ thống phải tính và hiển thị tổng chi phí: giá hàng, phí giao (báo giá), phí khác theo chính sách; phân biệt khoản ước tính và khoản xác định. | M | M | NEED-BUY-04 |
| FR-CHK-09 | Người mua chọn địa chỉ giao và phương thức giao/thanh toán đã hỗ trợ. | M | M | J-01 |
| FR-CHK-10 | Nếu tin thay đổi giá/tình trạng giữa chừng, hệ thống phải yêu cầu người mua xác nhận lại hoặc dừng checkout. | M, SYS | M | EX-004 |
| FR-CHK-11 | Khi không giữ được hàng, hệ thống phải báo lỗi dễ hiểu, không tạo đơn có thể thanh toán, và gợi ý tin tương tự. | M | M | BR-SRC-004 |
| FR-CHK-12 | Người mua không được checkout tin của chính mình. | SYS | M | BR-ORD-002 |
| FR-CHK-13 | Hệ thống áp dụng Anti-Hoarding khi giữ hàng 15 phút: tối đa 02 đơn `AWAITING_PAYMENT` đồng thời/Buyer; cooldown 30 phút và tối đa 02 lần hold/ngày cho cùng `(BuyerId, ListingId)` sau hủy/hết hạn. | SYS | M | BR-ORD-014 |

## 7. Quản lý đơn hàng (FR-ORD) – Order & Checkout

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-ORD-01 | Sau reservation thành công, hệ thống phải tạo đơn trạng thái Pending Payment với mã đơn duy nhất. | SYS | M | BRQ-TXN-04 |
| FR-ORD-02 | Đơn phải lưu snapshot: tiêu đề, giá, tình trạng, thuộc tính trọng yếu, ảnh đại diện (tham chiếu), người bán, địa chỉ giao tại thời điểm mua. | SYS | M | BR-ORD-004 |
| FR-ORD-03 | Hệ thống phải quản lý trạng thái đơn: Draft, Pending Payment, Confirmed, Fulfillment Pending, Shipped, Delivered, Completed, Cancelled, Disputed; chỉ cho phép chuyển trạng thái hợp lệ và ghi nguồn, thời điểm, tác nhân. | SYS | M | BR-ORD-005 |
| FR-ORD-04 | Hệ thống phải xác nhận đơn (Confirmed) khi thanh toán thành công đã được xác minh (hoặc điều kiện COD nếu được duyệt). | SYS | M | BR-ORD-006, BR-ORD-010 |
| FR-ORD-05 | Người mua hủy đơn khi ở trạng thái cho phép; hệ thống xử lý giữ hàng, thanh toán, shipment theo giai đoạn. | M | M | BR-ORD-007 |
| FR-ORD-06 | Người bán hủy/từ chối đơn trước khi bàn giao với lý do; hệ thống kích hoạt hoàn tiền và thông báo. | S | S | BR-ORD-007 |
| FR-ORD-07 | Hệ thống phải tự hủy đơn Pending Payment quá hạn và phát sự kiện giải phóng giữ hàng. | SYS | M | BR-ORD-013 |
| FR-ORD-08 | Hệ thống phải chuyển Completed khi thỏa tiêu chí (đề xuất: giao thành công và hết hạn khiếu nại không có khiếu nại, hoặc người mua xác nhận) – cần xác nhận. | SYS | M | Q-BIZ-12 |
| FR-ORD-09 | Người mua xem lịch sử và chi tiết đơn của mình; người bán xem đơn bán của mình; mỗi bên chỉ thấy dữ liệu trong quyền. | M, S | M | BR-ACT-002 |
| FR-ORD-10 | Hệ thống phải hiển thị timeline sự kiện của đơn (đặt, thanh toán, giao, hủy) kèm nguồn và thời gian. | M, S, B | S | NEED-BUY-05 |
| FR-ORD-11 | Mỗi đơn chỉ chứa một người bán và một tin (đợt đầu); mở rộng cần quyết định riêng. | SYS | M | BR-ORD-008 |
| FR-ORD-12 | Hệ thống phải điều phối chuỗi Order–Listing–Payment–Shipping theo mẫu saga: mỗi bước có hành động bù trừ tương ứng; lỗi không tự phục hồi chuyển vào hàng đợi xử lý thủ công. | SYS | M | BRQ-TXN-08 |

## 8. Thanh toán và hoàn tiền (FR-PAY) – Payment

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-PAY-01 | Hệ thống phải tạo yêu cầu thanh toán gắn đơn, số tiền, tiền tệ và mã tham chiếu/tương quan duy nhất, kèm idempotency key. | SYS | M | BR-PAY-001 |
| FR-PAY-02 | Hệ thống phải giao tiếp với cổng thanh toán qua hợp đồng tích hợp trừu tượng (tạo giao dịch, truy vấn, hoàn tiền, nhận callback); nhà cung cấp cụ thể thay được. | SYS | M | C-06; Q-OPS-05 |
| FR-PAY-03 | Hệ thống phải xác thực callback (nguồn/chữ ký), đối chiếu mã tham chiếu, số tiền, tiền tệ trước khi cập nhật trạng thái. | SYS | M | BR-PAY-002, BR-PAY-005 |
| FR-PAY-04 | Callback/yêu cầu trùng hoặc sai thứ tự không được tạo tác động lặp hoặc lùi trạng thái hợp lệ. | SYS | M | BR-PAY-003; EX-006 |
| FR-PAY-05 | Khi timeout hoặc kết quả không xác định, hệ thống phải giữ trạng thái Pending/Cần đối soát, truy vấn lại nhà cung cấp theo lịch giới hạn, không tự kết luận thất bại và không kích hoạt giao hàng. | SYS | M | BR-PAY-004; EX-005 |
| FR-PAY-06 | Hệ thống phải quản lý trạng thái thanh toán: Initiated, Pending, Authorized, Captured, Failed, Refund Pending, Refunded, Reconciliation Required. | SYS | M | glossary §3.3 |
| FR-PAY-07 | Khi thanh toán thành công, hệ thống phát `PaymentCaptured`; khi thất bại/hết hạn phát `PaymentFailed` để kích hoạt bù trừ. | SYS | M | EX-007, EX-008 |
| FR-PAY-08 | Nếu thanh toán thành công nhưng cập nhật đơn lỗi, Payment phải giữ bằng chứng và phát lại sự kiện/đối soát; không đánh dấu đơn thất bại giả. | SYS | M | EX-008 |
| FR-PAY-09 | Hệ thống phải cho người mua thử lại thanh toán trong thời hạn reservation mà không tạo giao dịch trùng. | M | S | EX-007 |
| FR-PAY-10 | Hệ thống phải khởi tạo hoàn tiền (toàn phần/một phần) theo quyền và chính sách, ghi lý do, số tiền, tham chiếu thanh toán gốc, kết quả. | B, SYS | S | BR-PAY-006..007 |
| FR-PAY-11 | Hệ thống phải đối soát định kỳ giữa ReMarket và nhà cung cấp; chênh lệch tạo hồ sơ đối soát, không xóa lịch sử. | B, SYS | S | BR-PAY-009 |
| FR-PAY-12 | Hệ thống không lưu dữ liệu thẻ nhạy cảm; chỉ lưu tham chiếu giao dịch và thông tin tối thiểu. | SYS | M | BR-PAY-008 |
| FR-PAY-13 | Hệ thống phải tạo lô giải ngân cho người bán sau khi đơn Completed theo chu kỳ `T+3` (`72 giờ` bảo vệ sau Delivered + lô đối soát ngân hàng kế tiếp), trừ 5% phí sàn và `OverweightSurcharge` (nếu có), đồng thời khóa giải ngân khi đơn Disputed. | B, SYS | S | BR-PAY-011..014; BR-DSP-002 |
| FR-PAY-14 | Người mua/bán xem trạng thái thanh toán/tiền hoàn/giải ngân của mình (che tham chiếu nhạy cảm). | M, S | S | NEED-BUY-06 |
| FR-PAY-15 | *(Hoãn sang R3+ theo DEC-BIZ-03)* Hỗ trợ COD sau khi có cơ chế đối soát thu hộ 3PL và ký quỹ người bán; R1/R2 chỉ áp dụng 100% Prepaid Escrow (`BR-PAY-015`). | SYS | W (R3+) | Q-OPS-05; BR-PAY-015 |

## 9. Giao nhận (FR-SHP) – Shipping & Fulfillment

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-SHP-01 | Hệ thống phải lấy báo giá giao hàng từ một hoặc nhiều hãng theo địa chỉ và thông số kiện hàng. | SYS | M | CAP-FUL-01 |
| FR-SHP-02 | Hệ thống phải tạo shipment qua adapter của hãng được chọn, với idempotency; lưu mã vận đơn và tham chiếu hãng. | SYS | M | BR-SHP-001 |
| FR-SHP-03 | Adapter phải chuẩn hóa API từng hãng về hợp đồng chung: báo giá, tạo, hủy, theo dõi, nhận webhook; thêm hãng mới không đổi phần còn lại. | SYS | M | DEC-DIR-05 |
| FR-SHP-04 | Hệ thống phải ánh xạ trạng thái hãng sang trạng thái chuẩn: Quote Pending, Created, Picked Up, In Transit, Delivered, Delivery Failed, Returned, Cancelled, Unknown. | SYS | M | glossary §3.4 |
| FR-SHP-05 | Trạng thái hãng không có trong ánh xạ phải ghi là Unknown, cảnh báo, không ép thành thành công. | SYS | M | EX-023 |
| FR-SHP-06 | Webhook phải được xác thực, chống trùng, xử lý trễ/sai thứ tự; không lùi trạng thái chuẩn sai. | SYS | M | BR-SHP-002; EX-011 |
| FR-SHP-07 | Khi hãng không phản hồi lúc tạo shipment, hệ thống giữ trạng thái chờ, tra cứu theo idempotency/tham chiếu, retry có giới hạn, tránh tạo vận đơn trùng. | SYS | M | EX-010 |
| FR-SHP-08 | Người bán xem hướng dẫn bàn giao, mã vận đơn, hạn xử lý; người mua theo dõi tiến trình. | M, S | M | NEED-SELL-05 |
| FR-SHP-09 | Địa chỉ/điện thoại chỉ chia sẻ cho hãng và vai trò cần thiết, tối thiểu hóa. | SYS | M | BR-ACT-005 |
| FR-SHP-10 | Giao thất bại/hoàn hàng/mất hàng: hệ thống mở hồ sơ vụ việc và không tự chuyển Completed. | SYS | M | BR-SHP-005; EX-012 |
| FR-SHP-11 | Hệ thống phải cho hủy shipment trước khi hãng lấy hàng khi đơn bị hủy. | SYS | S | CAP-FUL-02 |
| FR-SHP-12 | Hệ thống phải đối soát trạng thái định kỳ với hãng cho shipment ở trạng thái không rõ/quá hạn. | SYS | S | BR-SHP-003 |
| FR-SHP-13 | *(Loại trừ khỏi R1/R2 theo DEC-BIZ-07)* Hệ thống chỉ tích hợp 3PL đối tác cho kiện hàng <= 30kg, không có cổng đội giao nhận riêng trong R1/R2. | B | W | Q-OPS-03; BR-SHP-007 |
| FR-SHP-14 | Hệ thống bắt buộc Bảo hiểm khai giá 3PL cho đơn >= 3.000.000 VNĐ, khấu trừ `OverweightSurcharge` vào `NetSellerAmount` nếu Seller khai thiếu cân nặng, và hoàn tiền ngay cho Buyer từ Escrow khi 3PL xác nhận `LOST`/`DAMAGED`. | SYS | M | BR-PAY-014; BR-SHP-006 |

## 10. Thông báo (FR-NTF) – Notification

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-NTF-01 | Hệ thống phải tiêu thụ sự kiện nghiệp vụ và tạo thông báo tương ứng tới đúng người nhận. | SYS | M | BR-NTF-001 |
| FR-NTF-02 | Thông báo bắt buộc cho: đơn xác nhận/hủy, thanh toán thành công/thất bại, đơn mới cho người bán, đã lấy hàng/giao thành công/thất bại, kết quả kiểm duyệt tin, cập nhật khiếu nại. | SYS | M | BR-NTF-001 |
| FR-NTF-03 | Hệ thống phải hỗ trợ kênh trong ứng dụng; email/SMS/push tùy lựa chọn đợt đầu. | SYS | M | Q-BIZ-16 |
| FR-NTF-04 | Người dùng quản lý tùy chọn nhận tin; không tắt được thông báo bảo mật và trạng thái đơn. | M, S | S | BR-NTF-002 |
| FR-NTF-05 | Tin tiếp thị không gửi trong giờ yên tĩnh (đề xuất 22:00–08:00); thông báo giao dịch/OTP gửi ngay. | SYS | S | BR-NTF-003 |
| FR-NTF-06 | Gửi lỗi tạm thời phải retry theo backoff có giới hạn; lỗi vĩnh viễn vào hàng đợi lỗi; lỗi thông báo không chặn luồng chính. | SYS | M | BR-NTF-004 |
| FR-NTF-07 | Hệ thống lưu lịch sử và trạng thái thông báo; người dùng xem và đánh dấu đã đọc. | M, S | S | P-10 |
| FR-NTF-08 | Nội dung thông báo không chứa PII hoặc bí mật ngoài mức cần thiết. | SYS | M | BR-SYS-009 |
| FR-NTF-09 | Notification không sở hữu trạng thái đơn, thanh toán hay shipment. | SYS | M | scope §7.1.7 |

## 11. Báo cáo và kiểm duyệt (FR-TRU) – Trust & Moderation

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-TRU-01 | Người dùng gửi báo cáo tin/người dùng với lý do, mô tả, bằng chứng đính kèm. | M, S | M | BR-MOD-001 |
| FR-TRU-02 | Hệ thống cấp mã vụ việc, chống báo cáo trùng và xác nhận tiếp nhận. | SYS | M | BR-MOD-001; J-03 |
| FR-TRU-03 | Moderator có hàng đợi lọc theo loại, mức ưu tiên, trạng thái; chỉ thấy dữ liệu tối thiểu, che PII/thanh toán. | B | M | BA 01 §5 |
| FR-TRU-04 | Moderator chọn hành động: không vi phạm, yêu cầu sửa, ẩn tạm, gỡ, chuyển cấp; hệ thống ghi lý do, tác nhân, thời điểm, trạng thái trước/sau. | B | M | BR-MOD-002 |
| FR-TRU-05 | Hành động moderation trên tin đang có reservation/order không được làm sai đơn/thanh toán; phải đi qua nghiệp vụ của service sở hữu và tạo vụ việc xử lý giao dịch. | SYS | M | BR-MOD-006; EX-014 |
| FR-TRU-06 | Hệ thống thông báo quyết định cho bên bị ảnh hưởng; hỗ trợ kháng nghị nếu chính sách có. | SYS | S | BR-MOD-005 |
| FR-TRU-07 | Hệ thống quản lý vụ việc theo vòng đời: Open, Triaged, Investigating, Waiting, Escalated, Resolved, Closed, Reopened. | B, SYS | M | glossary §3.5 |
| FR-TRU-08 | Back-office không ghi trực tiếp vào cơ sở dữ liệu nghiệp vụ; mọi hành động qua API của service. | SYS | M | BR-MOD-003 |
| FR-TRU-09 | Người bán bị đình chỉ: chặn hành động mới theo phạm vi, giữ nguyên xử lý đơn/vụ việc đang mở. | SYS | S | EX-013 |

## 12. Khiếu nại, đổi trả (FR-DSP)

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-DSP-01 | Người mua mở khiếu nại trong cửa sổ `72 giờ` sau `Delivered` (`BR-DSP-001`) kèm lý do và bằng chứng ảnh (≤10 MB)/video (≤50 MB). | M | S | BR-DSP-001; BR-MED-001 |
| FR-DSP-02 | Khi mở khiếu nại, đơn chuyển Disputed và khóa giải ngân. | SYS | S | BR-DSP-002 |
| FR-DSP-03 | Hệ thống yêu cầu hai bên bổ sung bằng chứng trong thời hạn cấu hình (đề xuất 24 giờ). | SYS | S | BR-DSP-003 |
| FR-DSP-04 | Nhân viên hỗ trợ ra quyết định: hoàn tiền toàn phần kèm trả hàng, hoàn một phần, hoặc bác khiếu nại; hệ thống thực thi và ghi audit. | B | S | BR-DSP-004 |
| FR-DSP-05 | Hoàn tiền/ giải ngân sau quyết định phải đi qua quyền và phê duyệt theo ngưỡng (cần xác nhận). | B | S | BR-PAY-006 |
| FR-DSP-06 | Đối tượng được thông báo kết quả; vụ việc đóng khi hoàn tất; mở lại theo chính sách kháng nghị. | SYS | S | BR-MOD-005 |
| FR-DSP-07 | Hệ thống tự động quản lý Reverse Logistics theo phán quyết trả hàng: theo dõi hạn 72 giờ Buyer gửi hoàn (quá hạn $\rightarrow$ `RETURN_EXPIRED_TO_SELLER`), mở timer 48 giờ sau `POD` hoàn về Seller (im lặng $\rightarrow$ auto-refund 100%, hoặc cho phép Seller mở phản khiếu nại kèm video khui hàng). | SYS | S | BR-DSP-005; BR-DSP-006 |

## 13. Đánh giá (FR-REV)

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-REV-01 | Người mua chỉ đánh giá được đơn Completed của mình, một lần mỗi đơn (chỉnh sửa theo chính sách). | M | C | BR-REV-001..002 |
| FR-REV-02 | Đánh giá gồm điểm và nhận xét; hệ thống lọc nội dung vi phạm. | M | C | Q-BIZ-13 |
| FR-REV-03 | Hiển thị điểm tổng hợp người bán trên tin/hồ sơ; người bán có thể phản hồi (cần xác nhận). | M, S | C | CAP-TRU-03 |

## 14. Quản trị và hỗ trợ (FR-ADM, FR-SUP)

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-ADM-01 | Business Admin quản lý cây danh mục và thuộc tính bắt buộc theo danh mục; thay đổi có phiên bản và không phá tin hiện hữu. | B | S | J-06; CAP-MKT-01 |
| FR-ADM-02 | Business Admin quản lý danh sách từ khóa/hàng cấm và chính sách cấu hình (thời hạn giữ, khiếu nại, giờ yên tĩnh). | B | S | BR-PRO-003 |
| FR-ADM-03 | Mọi thay đổi cấu hình/chính sách ghi audit (tác nhân, thời điểm, giá trị cũ/mới). | B | M | J-06 |
| FR-ADM-04 | Admin xem dashboard nghiệp vụ tổng hợp (số tin, đơn, tỷ lệ checkout, vụ việc). | B | C | MET-* |
| FR-ADM-05 | Admin quản lý trạng thái tài khoản (hạn chế/khóa/mở) kèm lý do và thời hạn. | B | M | FR-ACC-08 |
| FR-SUP-01 | Support tra cứu theo mã đơn/mã vụ việc/mã tương quan; thấy tóm tắt đơn, thanh toán, shipment, timeline. | B | M | NEED-SUP-01 |
| FR-SUP-02 | Màn hình tra cứu phải tách rõ trạng thái của từng miền (Order, Payment, Shipment) kèm nguồn và thời điểm cập nhật; không gộp che sự không nhất quán. | B | M | J-04 |
| FR-SUP-03 | Dữ liệu cá nhân/thanh toán che theo vai trò và vụ việc được giao; xem PII được ghi audit. | B | M | NEED-SUP-03 |
| FR-SUP-04 | Support gửi yêu cầu hủy/hoàn tiền/đối soát qua quy trình có phê duyệt; thao tác nhạy cảm cần lý do và idempotency. | B | M | NEED-SUP-02 |
| FR-SUP-05 | Support ghi chú, gán, chuyển cấp và đóng vụ việc; lịch sử lưu theo retention. | B | M | CAP-SUP-01 |

## 15. Vận hành và dữ liệu (FR-OPS)

| ID | Yêu cầu | Kênh | Ưu tiên | Truy vết |
|---|---|---|---|---|
| FR-OPS-01 | Mỗi yêu cầu phải mang correlation ID xuyên service, log và sự kiện (không chứa PII). | SYS | M | BRQ-PLT-04 |
| FR-OPS-02 | Hệ thống phải xuất log, metric, trace và cảnh báo theo các chỉ số đã định (MET-PLAT-*). | SYS | M | NEED-OPS-03 |
| FR-OPS-03 | Hệ thống cung cấp health check cho từng service để load balancer loại instance lỗi. | SYS | M | BRQ-PLT-02 |
| FR-OPS-04 | Sự kiện liên service phải có phiên bản schema, consumer xử lý idempotent, hàng đợi lỗi và quy trình phát lại. | SYS | M | BR-SYS-010..011 |
| FR-OPS-05 | Hệ thống phải có cơ chế giám sát backup (kết quả, tuổi bản sao gần nhất) và cảnh báo khi trễ/thất bại. | SYS | M | MET-PLAT-04 |
| FR-OPS-06 | Hệ thống phải hỗ trợ khôi phục theo thời điểm cho SQL Server và MongoDB và khôi phục đối tượng S3 theo chính sách đã chọn. | SYS | M | BRQ-PLT-03 |
| FR-OPS-07 | Sau khôi phục, hệ thống cung cấp công cụ/quy trình đối soát liên service (Order–Listing–Payment–Shipping) và phát lại sự kiện. | SYS | M | EX-019 |
| FR-OPS-08 | Search index và cache phải dựng lại được từ nguồn chuẩn. | SYS | M | EX-016; MET-PLAT-07 |
| FR-OPS-09 | Thao tác restore/đổi chính sách backup yêu cầu phê duyệt và ghi audit. | B | M | BA 01 §7 |
| FR-OPS-10 | Cấu hình, bí mật và khóa mã hóa được quản lý ngoài mã nguồn; không ghi vào log. | SYS | M | BR-SYS-009 |

## 16. Ràng buộc chéo giữa các service (bất biến)

| ID | Ràng buộc |
|---|---|
| INV-01 | Listing là nguồn chuẩn khả dụng; Order/Search/cache không tự quyết khả dụng. |
| INV-02 | Không dùng transaction cơ sở dữ liệu xuyên service; nhất quán qua sự kiện, bù trừ và đối soát. |
| INV-03 | Order không join trực tiếp sang cơ sở dữ liệu của service khác; dùng snapshot và API. |
| INV-04 | Payment/Shipping callback chỉ chấp nhận sau xác thực và khử trùng. |
| INV-05 | Back-office và service khác không đọc/ghi trực tiếp cơ sở dữ liệu của service sở hữu. |

## 17. Ma trận bao phủ module ↔ yêu cầu nghiệp vụ

| BRQ | Module FR chính |
|---|---|
| BRQ-ACC-* | FR-ACC, FR-ADM-05 |
| BRQ-MKT-* | FR-LST, FR-MED, FR-SRC, FR-ADM-01..02 |
| BRQ-TXN-* | FR-CHK, FR-ORD, FR-PAY |
| BRQ-FUL-* | FR-SHP |
| BRQ-TRU-* | FR-TRU, FR-DSP, FR-REV, FR-SUP, FR-NTF |
| BRQ-PLT-* | FR-OPS, INV-* |
| BRQ-GOV-* | Ngoài hệ thống (08_validation-and-governance) |

## 18. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo danh mục yêu cầu chức năng theo 15 module, bất biến liên service và ma trận bao phủ. |
| 1.1 | 13/11/2026 | Đồng bộ `FR-MED-01` (`≤10 MB`), `FR-DSP-01` (`72 giờ`), `FR-PAY-13` (`T+3`), hoãn COD (`FR-PAY-15` -> `R3+`), loại trừ đội giao nhận riêng (`FR-SHP-13`) và bổ sung `FR-CHK-13`, `FR-SHP-14`, `FR-DSP-07`. |

