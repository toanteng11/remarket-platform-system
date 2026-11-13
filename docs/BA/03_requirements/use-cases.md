# Use Cases – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Loại tài liệu | Use Cases (UC) |
| Phiên bản | 1.0 |
| Trạng thái | Bản nháp; luồng phụ thuộc chính sách chưa duyệt được đánh dấu |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | functional-requirements.md, business-rules.md, business-scenarios-and-exceptions.md, acceptance-criteria.md |

## 1. Quy ước

- ID: `UC-<số>`. Actor tham chiếu role ở `user-roles-and-permissions.md`.
- Mỗi use case: mục tiêu, actor, tiền điều kiện, luồng chính, luồng thay thế/ngoại lệ, hậu điều kiện, FR/BR/EX liên quan.
- Giá trị trong ngoặc như (15 phút) là **đề xuất**, cấu hình được, chờ duyệt.
- "Hệ thống" gồm các service nội bộ; tên service nêu khi quyết định trách nhiệm.

## 2. Danh mục use case

| ID | Tên | Actor chính | Ưu tiên |
|---|---|---|---|
| UC-01 | Đăng ký và kích hoạt tài khoản | Guest | M |
| UC-02 | Đăng nhập và quản lý phiên | Buyer/Seller/Staff | M |
| UC-03 | Quản lý hồ sơ và địa chỉ | Buyer/Seller | M |
| UC-04 | Tạo và công bố tin đăng | Seller | M |
| UC-05 | Chỉnh sửa, tạm ẩn, đánh dấu bán, gỡ tin | Seller | M |
| UC-06 | Tìm kiếm và xem chi tiết tin | Guest/Buyer | M |
| UC-07 | Checkout và giữ hàng | Buyer | M |
| UC-08 | Thanh toán đơn hàng | Buyer | M |
| UC-09 | Xử lý kết quả thanh toán (callback) | Payment Provider | M |
| UC-10 | Hủy đơn | Buyer/Seller | M |
| UC-11 | Bàn giao và tạo vận đơn | Seller | M |
| UC-12 | Theo dõi và cập nhật trạng thái giao hàng | Carrier/Buyer | M |
| UC-13 | Xác nhận hoàn tất đơn | Buyer/Hệ thống | M |
| UC-14 | Báo cáo tin/người dùng | Buyer/Seller | M |
| UC-15 | Kiểm duyệt vụ việc | Moderator | M |
| UC-16 | Mở và giải quyết khiếu nại | Buyer/Support | S |
| UC-17 | Hoàn tiền | Support/Admin | S |
| UC-18 | Tra cứu giao dịch hỗ trợ khách | Support | M |
| UC-19 | Đối soát thanh toán | Finance/Admin | S |
| UC-20 | Giải ngân cho người bán | Hệ thống/Finance | S |
| UC-21 | Gửi thông báo | Hệ thống | M |
| UC-22 | Quản lý danh mục và chính sách | Business Admin | S |
| UC-23 | Đánh giá người bán | Buyer | C |
| UC-24 | Khôi phục dữ liệu | Platform Operator | M |
| UC-25 | Giám sát và xử lý cảnh báo | Platform Operator | M |
| UC-26 | Đối soát giao nhận khi trạng thái không rõ | Hệ thống/Support | S |

## 3. Đặc tả chi tiết

### UC-01 – Đăng ký và kích hoạt tài khoản

| Mục | Nội dung |
|---|---|
| Mục tiêu | Tạo tài khoản đã xác minh để mua/bán. |
| Actor | Guest. |
| Tiền điều kiện | Chưa có tài khoản với định danh này. |
| Luồng chính | 1. Guest chọn đăng ký, nhập định danh và mật khẩu. 2. Hệ thống kiểm tra định dạng, độ mạnh mật khẩu, trùng định danh. 3. Hệ thống gửi mã/liên kết xác minh. 4. Guest xác minh. 5. Tài khoản chuyển Hoạt động; hệ thống tạo hồ sơ rỗng. |
| Luồng thay thế | 2a. Định danh trùng: báo lỗi trung tính (không lộ tài khoản tồn tại nếu chính sách yêu cầu). 4a. Mã hết hạn: cho gửi lại có giới hạn tần suất. |
| Ngoại lệ | Kênh gửi lỗi: retry và thông báo thử lại sau; lỗi không tạo tài khoản kích hoạt. |
| Hậu điều kiện | Tài khoản Hoạt động hoặc ở Chờ xác minh. |
| Liên quan | FR-ACC-01..03, FR-NTF-06; BR-ACT-001. |

### UC-02 – Đăng nhập và quản lý phiên

| Mục | Nội dung |
|---|---|
| Actor | Buyer, Seller, Moderator, Support, Admin. |
| Tiền điều kiện | Tài khoản Hoạt động. |
| Luồng chính | 1. Nhập thông tin đăng nhập. 2. Hệ thống xác thực (và MFA nếu áp dụng). 3. Cấp phiên có thời hạn. 4. Chuyển tới kênh phù hợp vai trò. |
| Luồng thay thế | 2a. Sai thông tin: tăng bộ đếm, thông báo chung. 2b. Vượt giới hạn: khóa tạm. 5. Đăng xuất hoặc thu hồi phiên từ thiết bị khác. |
| Ngoại lệ | Tài khoản Hạn chế/Khóa: từ chối kèm hướng dẫn hỗ trợ. |
| Hậu điều kiện | Phiên hợp lệ hoặc bị từ chối, có audit đăng nhập bất thường. |
| Liên quan | FR-ACC-02..04, FR-ACC-08; EX-024. |

### UC-03 – Quản lý hồ sơ và địa chỉ

| Mục | Nội dung |
|---|---|
| Actor | Buyer, Seller. |
| Luồng chính | 1. Mở hồ sơ. 2. Sửa thông tin, thêm/sửa/xóa địa chỉ, đặt địa chỉ mặc định. 3. Hệ thống kiểm tra và lưu. 4. (Seller) Kích hoạt vai trò người bán bổ sung thông tin cần thiết. |
| Luồng thay thế | 2a. Xóa địa chỉ đang dùng cho đơn mở: giữ bản snapshot ở đơn, địa chỉ chỉ mất ở sổ. |
| Hậu điều kiện | Hồ sơ cập nhật; thay đổi nhạy cảm được audit. |
| Liên quan | FR-ACC-05..06; BRQ-ACC-02. |

### UC-04 – Tạo và công bố tin đăng

| Mục | Nội dung |
|---|---|
| Actor | Seller. Hệ thống: Listing, Media, Moderation, Search. |
| Tiền điều kiện | Tài khoản có vai trò Seller, không bị hạn chế. |
| Luồng chính | 1. Seller chọn "Đăng tin"; hệ thống tạo Nháp. 2. Chọn danh mục; hệ thống hiển thị thuộc tính bắt buộc theo danh mục. 3. Nhập tiêu đề, giá, tình trạng, mô tả, điều kiện giao. 4. Tải 1–10 ảnh; hệ thống kiểm tra loại/kích thước, lưu object storage, quét mã độc. 5. Seller xem trước. 6. Seller gửi công bố. 7. Hệ thống kiểm tra trường bắt buộc và từ khóa/hàng cấm. 8. Nếu đạt và không cần duyệt trước: tin Published, phát `ListingPublished`; Search cập nhật chỉ mục. 9. Seller nhận xác nhận. |
| Luồng thay thế | 7a. Cần duyệt trước: tin Pending Review, vào hàng đợi moderation (chờ kết quả, UC-15). 7b. Vi phạm rõ ràng: từ chối, nêu lý do. 4a. Ảnh lỗi: thử lại riêng từng ảnh. 3a. Lưu nháp rồi thoát. |
| Ngoại lệ | Mất kết nối: nháp được tự lưu; ảnh đang tải có thể tải lại. Search chậm: tin vẫn Published, hiển thị trong tìm kiếm sau khi đồng bộ (EX-001). |
| Hậu điều kiện | Tin ở Draft/Pending Review/Published/Rejected, có chủ sở hữu và lịch sử. |
| Liên quan | FR-LST-01..06, FR-MED-01..06; BR-LST, BR-MED, BR-PRO; SCN-001. |

### UC-05 – Chỉnh sửa, tạm ẩn, đánh dấu bán, gỡ tin

| Mục | Nội dung |
|---|---|
| Actor | Seller. |
| Tiền điều kiện | Seller sở hữu tin. |
| Luồng chính | 1. Mở danh sách tin. 2. Chọn thao tác: sửa, tạm ẩn, hiện lại, đánh dấu đã bán, gỡ. 3. Hệ thống kiểm tra trạng thái hiện tại cho phép thao tác. 4. Lưu thay đổi và lịch sử; phát sự kiện. 5. Search cập nhật. |
| Luồng thay thế | 3a. Tin đang Reserved/có đơn mở: từ chối sửa giá/gỡ, nêu lý do. 3b. Thay đổi nhạy cảm (giá/tình trạng): có thể yêu cầu duyệt lại. |
| Ngoại lệ | Xung đột đồng thời (seller sửa lúc buyer checkout): checkout xử lý theo UC-07 bước xác minh, EX-004. |
| Hậu điều kiện | Tin ở trạng thái hợp lệ mới. |
| Liên quan | FR-LST-07..11; BR-LST-006..008. |

### UC-06 – Tìm kiếm và xem chi tiết tin

| Mục | Nội dung |
|---|---|
| Actor | Guest, Buyer. |
| Luồng chính | 1. Nhập từ khóa/chọn danh mục. 2. Áp bộ lọc, sắp xếp. 3. Hệ thống trả kết quả từ Search (chỉ tin Published). 4. Người dùng mở chi tiết; hệ thống hiển thị ảnh, tình trạng, giá, người bán, thời điểm cập nhật. 5. Có thể báo cáo tin (UC-14) hoặc bắt đầu checkout (UC-07). |
| Luồng thay thế | 3a. Không có kết quả: gợi ý nới lọc. 4a. Tin vừa chuyển Sold/ẩn: trang chi tiết đọc từ nguồn chuẩn hiển thị không khả dụng. |
| Ngoại lệ | Search không khả dụng: duyệt theo danh mục từ Listing ở chế độ suy giảm (EX-016). |
| Hậu điều kiện | Không đổi dữ liệu. |
| Liên quan | FR-SRC-01..08; BR-SRC; EX-001. |

### UC-07 – Checkout và giữ hàng

| Mục | Nội dung |
|---|---|
| Actor | Buyer. Hệ thống: Order, Listing, Shipping (báo giá). |
| Tiền điều kiện | Buyer đã đăng nhập, không phải chủ tin; có địa chỉ giao. |
| Luồng chính | 1. Buyer bấm "Mua/Thanh toán". 2. Order xác thực buyer, nhận idempotency key. 3. Order gọi Listing: xác nhận tin Published, giá hiện hành. 4. Listing thực hiện reservation nguyên tử (hạn 15 phút), trả mã reservation. 5. Order lấy báo giá giao hàng, tính tổng. 6. Order tạo đơn Pending Payment kèm snapshot. 7. Hệ thống hiển thị tóm tắt, mã đơn, đếm ngược. 8. Buyer xác nhận và chuyển sang UC-08. |
| Luồng thay thế | 3a. Giá/tình trạng đã đổi: yêu cầu buyer xác nhận lại hoặc dừng (EX-004). 5a. Không báo giá được: dùng phí ước tính hoặc chặn theo chính sách. 2a. Yêu cầu lặp cùng key: trả đơn/reservation hiện có, không tạo mới (EX-003). |
| Ngoại lệ | 4a. Tin đã bị giữ/bán (cạnh tranh): từ chối rõ ràng, không tạo đơn, gợi ý tin tương tự (EX-001, EX-002). 4b. Listing không phản hồi: không báo thành công, trả trạng thái chờ/lỗi kèm mã tham chiếu; kiểm tra reservation đã commit chưa khi retry (EX-015). 8a. Hết hạn: đơn Cancelled, reservation giải phóng (UC-10). |
| Hậu điều kiện | Có đúng một đơn Pending Payment + một reservation, hoặc không có gì. |
| Liên quan | FR-CHK-01..12, FR-ORD-01..02, FR-LST-12; BR-ORD-001..004; SCN-002. |

### UC-08 – Thanh toán đơn hàng

| Mục | Nội dung |
|---|---|
| Actor | Buyer, Cổng thanh toán. |
| Tiền điều kiện | Đơn Pending Payment, reservation còn hiệu lực. |
| Luồng chính | 1. Buyer chọn phương thức. 2. Order yêu cầu Payment tạo giao dịch (số tiền, mã tham chiếu, idempotency). 3. Payment gọi cổng, nhận URL/thông tin thanh toán. 4. Buyer thanh toán tại cổng. 5. Cổng gửi kết quả (UC-09). 6. Giao diện hiển thị kết quả hoặc "đang xử lý". |
| Luồng thay thế | 1a. **COD (Hoãn sang R3+ theo DEC-BIZ-03):** Trong R1/R2 chỉ áp dụng 100% Prepaid Escrow (`BR-PAY-015`). 4a. Buyer thoát giữa chừng: có thể thử lại trong hạn reservation (FR-PAY-09). |
| Ngoại lệ | 3a. Cổng timeout: Payment ở Pending, không tạo giao dịch trùng khi thử lại (EX-005). 6a. Hết hạn giữ hàng khi đang trả: xử lý theo UC-09 (xung đột hết hạn – EX-007). |
| Hậu điều kiện | Payment Pending/Captured/Failed. |
| Liên quan | FR-PAY-01..07, FR-PAY-09; BR-PAY-001..005. |

### UC-09 – Xử lý kết quả thanh toán (callback)

| Mục | Nội dung |
|---|---|
| Actor | Cổng thanh toán (hệ thống ngoài). |
| Luồng chính | 1. Cổng gửi callback. 2. Payment xác minh nguồn/chữ ký, mã tham chiếu, số tiền, tiền tệ. 3. Khử trùng theo mã sự kiện. 4. Cập nhật trạng thái hợp lệ. 5. Thành công: phát `PaymentCaptured`; Order → Confirmed; Listing → Sold; Shipping bắt đầu; Notification gửi tin. 6. Thất bại: phát `PaymentFailed`; Order → Cancelled; Listing giải phóng reservation. |
| Luồng thay thế | 3a. Callback trùng: bỏ qua tác động, trả phản hồi thành công cho cổng. 4a. Sai thứ tự: không lùi trạng thái, đối chiếu thời gian/phiên bản (EX-006). |
| Ngoại lệ | 2a. Chữ ký sai/số tiền lệch: từ chối, ghi cảnh báo bảo mật, mở hồ sơ đối soát. 5a. Order cập nhật lỗi sau khi Payment đã Captured: Payment giữ bằng chứng, phát lại sự kiện, cảnh báo nếu quá ngưỡng (EX-008). 6a. Reservation đã hết hạn và tin đã bán cho người khác trước khi nhận thanh toán thành công: tạo vụ việc đối soát, hoàn tiền theo chính sách, thông báo buyer. |
| Hậu điều kiện | Trạng thái Payment/Order/Listing nhất quán hoặc ở Cần đối soát. |
| Liên quan | FR-PAY-03..08, FR-ORD-04, FR-ORD-12; BR-PAY-002..004; EX-005..008. |

### UC-10 – Hủy đơn

| Mục | Nội dung |
|---|---|
| Actor | Buyer, Seller, Hệ thống (hết hạn). |
| Tiền điều kiện | Đơn ở trạng thái cho phép hủy theo chính sách. |
| Luồng chính | 1. Actor yêu cầu hủy (kèm lý do). 2. Order kiểm tra trạng thái hiện tại. 3. Pending Payment: hủy, giải phóng reservation. 4. Đã thanh toán, chưa bàn giao: hủy shipment (nếu có), tạo hoàn tiền (UC-17), giải phóng/đóng tin theo chính sách. 5. Thông báo các bên. |
| Luồng thay thế | 2a. Đã bàn giao hãng: không hủy trực tiếp; chuyển luồng trả hàng/khiếu nại (UC-16). |
| Ngoại lệ | Hủy lặp: idempotent. Hủy cùng lúc với thanh toán thành công: Order phân giải theo thứ tự sự kiện hợp lệ, trạng thái cuối nhất quán, có hoàn tiền nếu đã thu (EX-009). |
| Hậu điều kiện | Đơn Cancelled hoặc yêu cầu hủy bị từ chối có lý do. |
| Liên quan | FR-ORD-05..07; BR-ORD-007; EX-009. |

### UC-11 – Bàn giao và tạo vận đơn

| Mục | Nội dung |
|---|---|
| Actor | Seller; Shipping; Hãng vận chuyển. |
| Tiền điều kiện | Đơn Confirmed. |
| Luồng chính | 1. Seller nhận thông báo đơn mới. 2. Seller xác nhận chuẩn bị hàng (trong hạn xử lý – cần xác nhận). 3. Shipping chọn hãng theo chính sách, tạo shipment qua adapter (idempotency). 4. Lưu mã vận đơn, hiển thị hướng dẫn bàn giao. 5. Seller bàn giao cho hãng. 6. Hãng báo Picked Up (UC-12). |
| Luồng thay thế | 2a. Seller từ chối/không xử lý quá hạn: hủy đơn và hoàn tiền (UC-10). |
| Ngoại lệ | 3a. Hãng không phản hồi: shipment chờ, tra cứu/ retry giới hạn, không tạo vận đơn trùng (EX-010). 3b. Hãng không phục vụ khu vực: chọn hãng khác hoặc báo seller/buyer. |
| Hậu điều kiện | Shipment Created, đơn Fulfillment Pending. |
| Liên quan | FR-SHP-01..03, FR-SHP-07..09; BR-SHP-001. |

### UC-12 – Theo dõi và cập nhật trạng thái giao hàng

| Mục | Nội dung |
|---|---|
| Actor | Hãng vận chuyển (webhook), Buyer, Seller. |
| Luồng chính | 1. Hãng gửi webhook trạng thái. 2. Shipping xác thực, khử trùng. 3. Ánh xạ sang trạng thái chuẩn. 4. Cập nhật shipment, phát sự kiện; Order → Shipped/Delivered tương ứng. 5. Notification gửi tin. 6. Buyer/Seller xem timeline. |
| Luồng thay thế | 3a. Trạng thái lạ: ghi Unknown, cảnh báo (EX-023). 2a. Trùng/trễ/sai thứ tự: bỏ/giữ trạng thái hợp lệ (EX-011). |
| Ngoại lệ | Giao thất bại/hoàn hàng/mất: mở hồ sơ vụ việc, không tự Completed (EX-012). Không nhận được webhook lâu: UC-26. |
| Hậu điều kiện | Shipment/Order phản ánh trạng thái chuẩn hiện tại. |
| Liên quan | FR-SHP-04..06, FR-SHP-10; BR-SHP-001..005. |

### UC-13 – Xác nhận hoàn tất đơn

| Mục | Nội dung |
|---|---|
| Actor | Buyer, Hệ thống. |
| Tiền điều kiện | Đơn Delivered. |
| Luồng chính | 1. Buyer xác nhận đã nhận hàng, hoặc hết cửa sổ khiếu nại không có khiếu nại. 2. Order chuyển Completed. 3. Mở quyền đánh giá. 4. Kích hoạt giải ngân (UC-20). |
| Luồng thay thế | 1a. Buyer mở khiếu nại trong cửa sổ: UC-16, đơn Disputed. |
| Hậu điều kiện | Đơn Completed hoặc Disputed. |
| Liên quan | FR-ORD-08, FR-DSP-01; BR-DSP-001; Q-BIZ-12. |

### UC-14 – Báo cáo tin/người dùng

| Mục | Nội dung |
|---|---|
| Actor | Buyer, Seller. |
| Luồng chính | 1. Chọn "Báo cáo" trên tin/hồ sơ. 2. Chọn lý do, mô tả, đính kèm bằng chứng. 3. Hệ thống kiểm tra trùng, cấp mã vụ việc. 4. Xác nhận tiếp nhận cho người báo. 5. Vụ việc vào hàng đợi (Open). |
| Luồng thay thế | 3a. Báo cáo trùng: gộp vào vụ việc hiện có. |
| Ngoại lệ | Lạm dụng báo cáo: giới hạn tần suất, đánh dấu để xem xét. |
| Hậu điều kiện | Vụ việc Open. |
| Liên quan | FR-TRU-01..02; BR-MOD-001. |

### UC-15 – Kiểm duyệt vụ việc

| Mục | Nội dung |
|---|---|
| Actor | Moderator. |
| Tiền điều kiện | Có vụ việc được giao. |
| Luồng chính | 1. Moderator mở vụ việc; hệ thống hiển thị tin, ảnh, lịch sử theo quyền (che PII/thanh toán). 2. Đánh giá theo chính sách. 3. Chọn hành động: không vi phạm / yêu cầu sửa / ẩn tạm / gỡ / chuyển cấp. 4. Ghi lý do, bằng chứng tham chiếu. 5. Hệ thống thực thi qua Listing/Identity, phát sự kiện, ghi audit. 6. Thông báo bên liên quan. 7. Đóng vụ việc hoặc chờ phản hồi/kháng nghị. |
| Luồng thay thế | 3a. Vượt thẩm quyền: Escalated cho cấp cao hơn. |
| Ngoại lệ | Tin đang có reservation/đơn: tin bị hạn chế hiển thị nhưng đơn/thanh toán không bị sửa ngầm; tạo vụ việc giao dịch riêng (EX-014). |
| Hậu điều kiện | Quyết định có audit; tin/tài khoản ở trạng thái mới. |
| Liên quan | FR-TRU-03..09; BR-MOD-001..006; SCN-006; J-03. |

### UC-16 – Mở và giải quyết khiếu nại

| Mục | Nội dung |
|---|---|
| Actor | Buyer, Seller, Support. |
| Tiền điều kiện | Đơn Delivered trong cửa sổ khiếu nại (72 giờ theo `DEC-BIZ-04`; xem thêm quy tắc hoàn trả `72h/48h` tại `BR-DSP-005`). |
| Luồng chính | 1. Buyer mở khiếu nại với lý do và bằng chứng. 2. Hệ thống chuyển đơn Disputed, khóa giải ngân. 3. Hệ thống yêu cầu seller phản hồi/bằng chứng (24 giờ – đề xuất). 4. Support xem xét timeline (UC-18). 5. Support ra quyết định: hoàn toàn phần + trả hàng / hoàn một phần / bác. 6. Hệ thống thực thi (UC-17 hoặc mở giải ngân), thông báo, đóng vụ việc. |
| Luồng thay thế | 3a. Bên không phản hồi đúng hạn: xử lý theo chính sách (cần xác nhận). 6a. Kháng nghị: vụ việc Reopened. |
| Hậu điều kiện | Đơn trở lại Completed hoặc Cancelled/Refunded; có audit đầy đủ. |
| Liên quan | FR-DSP-01..06; BR-DSP-001..004. |

### UC-17 – Hoàn tiền

| Mục | Nội dung |
|---|---|
| Actor | Support/Admin (có quyền); Payment; Cổng thanh toán. |
| Tiền điều kiện | Có thanh toán Captured và lý do hợp lệ. |
| Luồng chính | 1. Người có quyền tạo yêu cầu hoàn (số tiền, lý do). 2. Vượt ngưỡng: cần phê duyệt thêm. 3. Payment kiểm tra số tiền ≤ còn lại có thể hoàn, tạo lệnh với idempotency. 4. Gửi cổng; nhận kết quả qua callback. 5. Cập nhật Refund Pending → Refunded; thông báo. |
| Ngoại lệ | Cổng timeout: Refund Pending/Cần đối soát, không gửi lệnh trùng. Hoàn thất bại: giữ trạng thái, vụ việc thủ công. |
| Hậu điều kiện | Có bản ghi hoàn tiền, audit người thực hiện/duyệt. |
| Liên quan | FR-PAY-10, FR-DSP-05, FR-SUP-04; BR-PAY-006..007. |

### UC-18 – Tra cứu giao dịch hỗ trợ khách

| Mục | Nội dung |
|---|---|
| Actor | Support. |
| Luồng chính | 1. Nhập mã đơn/mã vụ việc/mã tương quan. 2. Hệ thống kiểm tra quyền và vụ việc được giao. 3. Hiển thị trạng thái riêng từng miền (Order, Payment, Shipment), nguồn và thời điểm cập nhật; timeline; trạng thái webhook/adapter. 4. PII/payment tham chiếu bị che; xem đầy đủ cần lý do và được audit. 5. Support tạo ghi chú hoặc yêu cầu hành động (UC-17, UC-10, UC-26). |
| Ngoại lệ | Các miền mâu thuẫn: hiển thị cảnh báo không nhất quán, không gộp che (EX-021). |
| Hậu điều kiện | Có ghi chú/vụ việc; audit truy cập. |
| Liên quan | FR-SUP-01..05; NEED-SUP-01..03; J-04. |

### UC-19 – Đối soát thanh toán

| Mục | Nội dung |
|---|---|
| Actor | Finance/Admin; Hệ thống. |
| Luồng chính | 1. Đến chu kỳ, hệ thống lấy báo cáo giao dịch từ cổng. 2. So khớp với bản ghi Payment/Order. 3. Giao dịch khớp: đánh dấu đã đối soát. 4. Lệch: tạo hồ sơ Reconciliation Required. 5. Finance xử lý (hoàn tiền, cập nhật trạng thái có audit, liên hệ đối tác). |
| Ngoại lệ | Báo cáo cổng thiếu/trễ: đánh dấu chưa đối soát, thử lại theo lịch. |
| Hậu điều kiện | Mọi giao dịch ở Đã đối soát hoặc Cần xử lý. |
| Liên quan | FR-PAY-05, FR-PAY-11; BR-PAY-009; EX-021. |

### UC-20 – Giải ngân cho người bán

| Mục | Nội dung |
|---|---|
| Actor | Hệ thống, Finance. |
| Tiền điều kiện | Đơn Completed, không Disputed; giải ngân theo chu kỳ `T+3` (`72h` sau Delivered + lô kế tiếp), trừ phí sàn `5%` & `OverweightSurcharge` (`BR-PAY-012..014`). |
| Luồng chính | 1. Hệ thống gom đơn đủ điều kiện theo người bán. 2. Tính số tiền (giá trừ phí/điều chỉnh theo chính sách). 3. Tạo lô giải ngân; Finance duyệt. 4. Thực hiện chuyển (qua cổng/ngân hàng); cập nhật trạng thái. 5. Thông báo người bán. |
| Ngoại lệ | Đơn chuyển Disputed trước khi chuyển: loại khỏi lô. Chuyển thất bại: giữ ở hàng chờ, cảnh báo. |
| Hậu điều kiện | Bản ghi giải ngân có audit. |
| Liên quan | FR-PAY-13; BR-PAY-010..012; BRQ-TXN-07. Phạm vi lô, không phải ví/escrow nội bộ. |

### UC-21 – Gửi thông báo

| Mục | Nội dung |
|---|---|
| Actor | Hệ thống (Notification). |
| Luồng chính | 1. Nhận sự kiện nghiệp vụ. 2. Xác định người nhận và tùy chọn nhận tin. 3. Kiểm tra giờ yên tĩnh với tin tiếp thị. 4. Dựng nội dung từ mẫu, không chứa PII thừa. 5. Gửi qua kênh/đối tác. 6. Ghi trạng thái gửi. |
| Ngoại lệ | Lỗi tạm thời: retry với backoff. Lỗi vĩnh viễn: hàng đợi lỗi. Lỗi gửi không làm hỏng luồng đơn. |
| Hậu điều kiện | Thông báo có trạng thái Sent/Failed/Read. |
| Liên quan | FR-NTF-01..09; BR-NTF-001..004; P-10. |

### UC-22 – Quản lý danh mục và chính sách

| Mục | Nội dung |
|---|---|
| Actor | Business Admin. |
| Luồng chính | 1. Đăng nhập Back-office (MFA nếu áp dụng). 2. Mở quản lý danh mục/thuộc tính/từ khóa cấm/tham số chính sách. 3. Chỉnh sửa; hệ thống hiển thị tác động tới tin hiện hữu. 4. Xác nhận; hệ thống tạo phiên bản cấu hình, ghi audit, phát sự kiện cho Listing/Search. |
| Ngoại lệ | Thay đổi gây xung đột với tin đang có: chặn hoặc áp dụng không hồi tố theo quy tắc. |
| Hậu điều kiện | Cấu hình mới có hiệu lực và có lịch sử. |
| Liên quan | FR-ADM-01..03; J-06. |

### UC-23 – Đánh giá người bán

| Mục | Nội dung |
|---|---|
| Actor | Buyer. |
| Tiền điều kiện | Đơn Completed, chưa đánh giá, trong thời hạn (nếu có). |
| Luồng chính | 1. Chọn điểm và nhận xét. 2. Hệ thống lọc nội dung vi phạm. 3. Lưu đánh giá gắn đơn; cập nhật điểm tổng hợp. |
| Ngoại lệ | Đánh giá lặp: từ chối hoặc theo quy tắc chỉnh sửa. Nội dung vi phạm: vào hàng đợi kiểm duyệt. |
| Liên quan | FR-REV-01..03; BR-REV-001..002. |

### UC-24 – Khôi phục dữ liệu

| Mục | Nội dung |
|---|---|
| Actor | Platform Operator, Incident Commander, DBA, Business Owner. |
| Tiền điều kiện | Sự cố dữ liệu hoặc diễn tập được khai báo; có phê duyệt. |
| Luồng chính | 1. Khai báo sự cố, ngăn ghi thêm dữ liệu sai. 2. Xác định phạm vi store và thời điểm khôi phục (theo RPO đã duyệt). 3. Kiểm tra quyền, khóa mã hóa, mạng. 4. Khôi phục vào môi trường cách ly/tài nguyên mới (SQL Server, MongoDB, S3 theo công cụ từng nền tảng). 5. Kiểm tra toàn vẹn dữ liệu và liên kết object. 6. Đối soát liên service, phát lại sự kiện thiếu. 7. Dựng lại Search/cache. 8. Smoke test các journey. 9. Phê duyệt và chuyển traffic. 10. Ghi RPO/RTO thực tế và hành động cải tiến. |
| Luồng thay thế | 2a. Chỉ ảnh hưởng dữ liệu dẫn xuất: chỉ dựng lại, không restore nguồn chuẩn. |
| Ngoại lệ | Thiếu khóa/quyền: dùng break-glass có phê duyệt, không mở quyền rộng (EX-020). Lệch thời điểm giữa service: chưa mở toàn bộ traffic cho tới khi đối soát (EX-019). Thiếu ảnh S3: đánh dấu phạm vi, khôi phục object (EX-018). Drill vượt mục tiêu: ghi nhận chưa đạt, RCA, diễn tập lại (EX-022). |
| Hậu điều kiện | Dịch vụ hoạt động với dữ liệu nhất quán; có biên bản. |
| Liên quan | FR-OPS-05..09; NFR-REC; BR-SYS-004..008, BR-SYS-012; J-05; P-09. |

### UC-25 – Giám sát và xử lý cảnh báo

| Mục | Nội dung |
|---|---|
| Actor | Platform Operator. |
| Luồng chính | 1. Nhận cảnh báo (lỗi, độ trễ, backlog, DLQ, backup, đơn/payment chưa xác định). 2. Mở dashboard và trace theo correlation ID. 3. Xác định service/nguyên nhân. 4. Thực hiện runbook (scale, rollback, redrive, chuyển trạng thái chờ). 5. Xác nhận phục hồi, đóng cảnh báo, ghi nhận. |
| Hậu điều kiện | Sự cố xử lý, có hồ sơ. |
| Liên quan | FR-OPS-01..04; NFR-OBS. |

### UC-26 – Đối soát giao nhận khi trạng thái không rõ

| Mục | Nội dung |
|---|---|
| Actor | Hệ thống, Support. |
| Luồng chính | 1. Phát hiện shipment quá hạn không cập nhật hoặc ở Unknown. 2. Shipping truy vấn hãng theo tham chiếu. 3. Nhận trạng thái: cập nhật trạng thái chuẩn. 4. Vẫn không rõ: mở vụ việc cho Support, thông báo bên liên quan. |
| Hậu điều kiện | Trạng thái được xác nhận hoặc có vụ việc xử lý. |
| Liên quan | FR-SHP-12; BR-SHP-003; EX-010..012. |

## 4. Ma trận use case ↔ kịch bản ngoại lệ (02)

| Kịch bản | Use case |
|---|---|
| EX-001..004 | UC-06, UC-07 |
| EX-005..009 | UC-08, UC-09, UC-10 |
| EX-010..012, EX-023 | UC-11, UC-12, UC-26 |
| EX-013..014 | UC-15 |
| EX-015 | UC-07 |
| EX-016..022 | UC-24, UC-25 |
| EX-021 | UC-18, UC-19 |
| EX-025 | UC-07 (Anti-Hoarding Lock & Cooldown 30p) |
| EX-026, EX-029..031 | UC-11, UC-12, UC-20, UC-26 (OverweightSurcharge, Mất/Hỏng >=3M, RTS, Quá hạn đóng gói 48h) |
| EX-027..028 | UC-16 (Reverse Logistics 72h Buyer gửi trả & 48h Seller sau POD) |

## 5. Ma trận use case ↔ journey và persona

| Journey | Use case |
|---|---|
| J-01 (Buyer) | UC-02, 06, 07, 08, 12, 13, 14, 16, 23 |
| J-02 (Seller) | UC-02, 04, 05, 11, 12 |
| J-03 (Moderator) | UC-15 |
| J-04 (Support) | UC-16, 17, 18 |
| J-05 (Operator) | UC-24, 25 |
| J-06 (Admin) | UC-22 |

## 6. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo 26 use case, ma trận truy vết tới ngoại lệ, journey và FR. |
| 1.1 | 13/11/2026 | Đồng bộ `UC-08` (COD hoãn R3+), `UC-16` (`72h` khiếu nại & Reverse Logistics `72h/48h`), `UC-20` (`T+3` & khấu trừ phí/phụ trội) và ánh xạ `EX-025..031`. |

