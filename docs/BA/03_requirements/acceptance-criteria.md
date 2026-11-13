# Acceptance Criteria – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Loại tài liệu | Acceptance Criteria (AC) |
| Phiên bản | 1.0 |
| Trạng thái | Bản nháp; giá trị trong ngoặc là mặc định đề xuất, cấu hình được |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | user-stories.md, functional-requirements.md, non-functional-requirements.md, business-scenarios-and-exceptions.md |

## 1. Quy ước

- Dạng **Given / When / Then**. ID: `AC-<epic>-<số>` khớp với `US-<epic>-<số>`.
- Mỗi AC gồm đường chính (happy path), đường âm (negative) và khi liên quan: lặp/idempotency, quyền, đồng thời, hồi phục.
- Giá trị chuẩn hóa theo `DEC-BIZ-01..10`: `(15 phút giữ hàng)`, `(72 giờ cửa sổ khiếu nại / 48 giờ đóng gói)`, `(phí sàn 5%)`, `(1–10 ảnh ≤10 MB/ảnh)`, `(22:00–08:00)`; kiểm thử dùng giá trị cấu hình hiện hành.
- Các AC nhất quán liên service dùng thuật ngữ trạng thái trong `glossary.md` §3.
- Các tiêu chí hiệu năng/sẵn sàng/phục hồi mang tính định lượng chỉ kiểm thử khi owner đã duyệt ngưỡng (xem NFR §14).

## 2. Tài khoản (AC-ACC)

**AC-ACC-01 – Đăng ký**
- Given khách nhập định danh hợp lệ và chưa tồn tại, When gửi đăng ký và xác minh bằng mã hợp lệ, Then tài khoản chuyển Hoạt động và hồ sơ rỗng được tạo.
- Given định danh đã tồn tại, When đăng ký, Then hệ thống từ chối bằng thông báo không lộ thông tin tài khoản (theo chính sách).
- Given mã xác minh hết hạn, When nhập mã, Then báo hết hạn và cho gửi lại; gửi lại vượt giới hạn tần suất bị chặn tạm.
- Given mật khẩu yếu, When đăng ký, Then bị từ chối kèm yêu cầu cụ thể.

**AC-ACC-02 – Đăng nhập/đăng xuất**
- Given thông tin đúng, When đăng nhập, Then cấp phiên có thời hạn.
- Given sai mật khẩu liên tiếp vượt ngưỡng, When thử tiếp, Then tài khoản bị khóa tạm và thông báo chung, không lộ tài khoản có tồn tại.
- Given tài khoản Khóa, When đăng nhập, Then bị từ chối kèm hướng dẫn liên hệ hỗ trợ.
- Given đã đăng xuất hoặc phiên bị thu hồi, When dùng lại token cũ, Then bị từ chối.
- Mật khẩu không được lưu dạng đọc được (kiểm tra qua review/dữ liệu kiểm thử).

**AC-ACC-03 – Khôi phục mật khẩu**
- Given người dùng yêu cầu khôi phục, When hoàn tất xác minh kênh, Then đặt được mật khẩu mới và các phiên cũ bị thu hồi.
- Liên kết khôi phục chỉ dùng được một lần và hết hạn.

**AC-ACC-04 – Sổ địa chỉ**
- Given buyer đã đăng nhập, When thêm/sửa/xóa/đặt mặc định địa chỉ, Then thay đổi được lưu và hiển thị đúng ở checkout.
- Given địa chỉ đang gắn đơn mở, When xóa khỏi sổ, Then đơn vẫn giữ địa chỉ gốc trong snapshot.

**AC-ACC-05 – Một tài khoản hai vai trò**
- Given tài khoản Buyer, When kích hoạt vai trò Seller đủ thông tin, Then truy cập được Seller Center, đồng thời vẫn mua hàng; chỉ thấy tin/đơn của chính mình.
- Given tài khoản vừa là buyer vừa là seller, When checkout tin của chính mình, Then bị từ chối.

**AC-ACC-06 – Quyền và riêng tư**
- Given buyer A, When truy cập (kể cả sửa ID trong URL/API) đơn của buyer B, Then bị từ chối và không lộ sự tồn tại của đơn.
- Given moderator, When mở vụ việc, Then không thấy dữ liệu thanh toán và PII được che.
- Mọi API đều kiểm tra quyền phía server (kiểm thử bằng gọi API trực tiếp, không qua giao diện).

**AC-ACC-07 – Hạn chế/khóa tài khoản**
- Given admin khóa tài khoản kèm lý do, When tài khoản cố tạo tin hoặc đặt đơn, Then bị chặn theo phạm vi; đơn/vụ việc đang mở không bị xóa.
- Hành động khóa ghi audit (tác nhân, lý do, thời điểm).

**AC-ACC-08 – MFA** (nếu được duyệt)
- Given vai trò yêu cầu MFA, When đăng nhập, Then bắt buộc bước xác thực thứ hai; không có bước này thì không truy cập Back-office.

## 3. Tin đăng (AC-LST)

**AC-LST-01 – Tạo tin**
- Given seller đủ điều kiện, When gửi công bố đủ trường bắt buộc (tiêu đề, danh mục, giá, tình trạng, mô tả, ≥1 ảnh), Then tin chuyển Published (hoặc Pending Review nếu bật duyệt trước) và sự kiện `ListingPublished` được phát.
- Given thiếu trường bắt buộc, When gửi, Then lỗi theo từng trường, dữ liệu đã nhập được giữ.
- Given danh mục có thuộc tính bắt buộc riêng, When thiếu thuộc tính đó, Then không thể công bố.
- Giá không hợp lệ (âm, quá giới hạn cấu hình) bị từ chối.

**AC-LST-02 – Ảnh**
- Given tin nháp, When tải ảnh JPEG/PNG/WebP ≤10 MB, Then ảnh được lưu và hiển thị; 10 ảnh là giới hạn trên, ảnh thứ 11 bị từ chối (video bằng chứng ≤50 MB).
- Given tệp có đuôi .jpg nhưng nội dung thực không phải ảnh, When tải lên, Then bị từ chối.
- Given một ảnh tải lỗi, When thử lại, Then các ảnh đã tải thành công không mất.
- Database chỉ chứa object key/metadata, không chứa binary ảnh.

**AC-LST-03 – Nháp/xem trước**
- Given nháp đang soạn, When mất kết nối rồi quay lại, Then nội dung đã lưu tự động còn nguyên.
- Xem trước hiển thị giống trang chi tiết công khai.

**AC-LST-04 – Chặn/từ chối**
- Given nội dung chứa từ khóa cấm hoặc hàng cấm, When gửi công bố, Then tin bị chặn hoặc vào Pending Review theo chính sách, và seller thấy lý do (không tiết lộ toàn bộ danh sách từ khóa).
- Tin Rejected không xuất hiện trong tìm kiếm.

**AC-LST-05 – Quản lý vòng đời**
- Given tin Published, When seller tạm ẩn, Then tin biến mất khỏi tìm kiếm sau khi đồng bộ và không nhận checkout mới; hiện lại thì khôi phục.
- When seller đánh dấu đã bán ngoài nền tảng, Then tin chuyển Sold/không khả dụng và không mua được.
- When seller sửa giá, Then phiên bản trước được lưu; đơn cũ giữ giá snapshot.
- Chuyển trạng thái không hợp lệ (ví dụ Removed → Published trực tiếp) bị từ chối.

**AC-LST-06 – Xung đột với reservation**
- Given tin đang Reserved/có đơn Pending Payment, When seller cố sửa giá hoặc gỡ, Then thao tác bị từ chối với lý do; tin không đổi.
- Given seller sửa giá đúng lúc buyer bắt đầu checkout, Then checkout dùng phiên bản đã xác minh hoặc yêu cầu buyer xác nhận lại (xem AC-CHK-07).

**AC-LST-07 – An toàn tệp**
- Given tệp chứa mã độc (mẫu thử chuẩn), When tải lên, Then bị chặn hoặc cách ly và không hiển thị công khai.

**AC-LST-08 – Thuộc tính theo danh mục**
- Given danh mục điện tử cũ yêu cầu IMEI/Serial (nếu có), When bỏ trống trường bắt buộc, Then không công bố được.

## 4. Tìm kiếm (AC-SRC)

**AC-SRC-01 – Tìm kiếm cơ bản**
- Given khách chưa đăng nhập, When tìm theo từ khóa/danh mục, Then thấy kết quả tin Published; không yêu cầu đăng nhập.
- Given không có kết quả, Then hiển thị thông báo và gợi ý nới lọc.
- Kết quả được phân trang; sắp xếp mới nhất/giá tăng/giá giảm hoạt động đúng.

**AC-SRC-02 – Bộ lọc**
- Given áp bộ lọc danh mục + khoảng giá + tình trạng, When tìm, Then mọi kết quả thỏa đồng thời tất cả điều kiện.

**AC-SRC-03 – Trang chi tiết**
- Given tin Published, When mở chi tiết, Then thấy ảnh, tình trạng, giá, người bán, thời điểm cập nhật và nút báo cáo.
- Tin không ngụ ý "đã kiểm định" nếu chưa có xác minh.

**AC-SRC-04 – Loại tin không khả dụng**
- Given tin chuyển Reserved/Sold/Hidden/Removed, When Search nhận sự kiện, Then tin không còn trong kết quả (trong ngưỡng độ trễ đã duyệt).
- Given tin vừa Sold nhưng chỉ mục chưa cập nhật, When người mua mở chi tiết, Then trang chi tiết đọc nguồn chuẩn và hiển thị không khả dụng.

**AC-SRC-05 – Hết hàng khi checkout**
- Given buyer bấm mua tin vừa bị người khác giữ, When checkout, Then nhận thông báo "không còn khả dụng" và danh sách tin tương tự; không có đơn nào được tạo.

**AC-SRC-06 – Độ trễ và dựng lại**
- Given Search bị xóa, When chạy quy trình dựng lại từ Listing, Then chỉ mục phục hồi khớp với tin Published trong nguồn chuẩn (kiểm tra số lượng và mẫu).
- Độ trễ đồng bộ được đo và có cảnh báo khi vượt ngưỡng.

## 5. Checkout, giữ hàng, đơn (AC-CHK)

**AC-CHK-01 – Xác nhận khả dụng**
- Given tin Published, When buyer bắt đầu checkout, Then Order hỏi Listing (nguồn chuẩn) và chỉ tiếp tục khi Listing xác nhận.
- Given Search báo còn nhưng Listing báo Sold, Then checkout bị từ chối.

**AC-CHK-02 – Chống bán trùng**
- Given một tin đơn chiếc, When N (≥2) buyer gửi checkout đồng thời, Then đúng một reservation/đơn thành công; các yêu cầu còn lại nhận kết quả không khả dụng; số đơn hợp lệ cho tin này luôn ≤1.
- Kiểm thử đồng thời lặp nhiều lần (ví dụ ≥1000 vòng) không có trường hợp bán trùng.
- Buyer không checkout được tin của chính mình.

**AC-CHK-03 – Đếm ngược**
- Given reservation thành công, Then buyer thấy thời hạn còn lại (15 phút) khớp với thời hạn lưu ở hệ thống.

**AC-CHK-04 – Giải phóng giữ hàng**
- Given đơn Pending Payment quá thời hạn, When hết hạn, Then đơn thành Cancelled, tin quay về Published và buyer được thông báo.
- Given thanh toán thất bại, Then reservation được giải phóng theo chính sách (sau khi cho thử lại nếu còn thời hạn).
- Giải phóng chạy đúng ngay cả khi worker khởi động lại giữa chừng (không bỏ sót, không giải phóng hai lần).

**AC-CHK-05 – Idempotency**
- Given cùng idempotency key, When gửi lại sau timeout hoặc nhấn nhiều lần, Then trả lại cùng đơn/kết quả; không tạo đơn hoặc reservation thứ hai.
- Given khác key cùng buyer/tin đang có reservation, Then bị từ chối là đã đang giữ (hoặc trả đơn hiện có theo quy tắc).

**AC-CHK-06 – Giá và phí**
- Given buyer ở màn xác nhận, Then thấy giá hàng, phí giao, các khoản khác và tổng; khoản ước tính được gắn nhãn.
- Tổng ở đơn bằng tổng đã hiển thị và đã xác nhận (làm tròn theo quy tắc BR-PAY-005).

**AC-CHK-07 – Thay đổi giữa chừng**
- Given giá/tình trạng tin đổi sau khi buyer xem, When bắt đầu checkout, Then buyer phải xác nhận lại với giá mới hoặc dừng; không đặt với giá cũ ngầm.

**AC-CHK-08 – Lịch sử/timeline**
- Given buyer có đơn, Then thấy danh sách và chi tiết gồm trạng thái, timeline sự kiện (đặt, thanh toán, giao, hủy) với thời gian và nguồn; không thấy đơn của người khác.

**AC-CHK-09 – Hủy đơn**
- Given đơn Pending Payment, When buyer hủy, Then đơn Cancelled và tin về Published.
- Given đơn đã thanh toán, chưa bàn giao, When hủy, Then shipment (nếu có) bị hủy, hoàn tiền được tạo, buyer và seller được thông báo.
- Given đã bàn giao hãng, When hủy, Then bị từ chối với hướng dẫn trả hàng/khiếu nại.
- Hủy lặp lại cho cùng đơn không tạo tác động thứ hai.
- Given hủy và thanh toán thành công đến cùng lúc, Then trạng thái cuối nhất quán và nếu đã thu tiền thì có hoàn tiền (EX-009).

**AC-CHK-10 – Góc nhìn người bán**
- Given đơn Pending Payment, Then seller thấy nhãn "chờ thanh toán" và không được yêu cầu bàn giao; chỉ đơn Confirmed mới có hành động chuẩn bị hàng.
- Seller chỉ thấy đơn thuộc tin của mình và thông tin buyer tối thiểu.

**AC-CHK-11 – Snapshot**
- Given đơn đã tạo, When seller sau đó sửa/gỡ tin, Then đơn vẫn hiển thị giá, tình trạng, mô tả đã chốt lúc mua.

## 6. Thanh toán (AC-PAY)

**AC-PAY-01 – Thành công**
- Given đơn Pending Payment, When cổng báo thành công (callback hợp lệ), Then Payment = Captured, Order = Confirmed, Listing = Sold, Shipping được kích hoạt, buyer/seller được thông báo.
- Số tiền và tiền tệ callback phải khớp với đơn; lệch thì không xác nhận, tạo hồ sơ đối soát.
- Callback có chữ ký sai hoặc nguồn không hợp lệ bị từ chối và ghi cảnh báo bảo mật.

**AC-PAY-02 – Timeout**
- Given cổng không phản hồi, When quá thời hạn gọi, Then Payment = Pending (hoặc Cần đối soát), giao diện hiển thị "đang xử lý", không gửi giao dịch mới, không giao hàng.
- Hệ thống truy vấn lại cổng theo lịch giới hạn; kết quả cuối cập nhật idempotent.
- Hệ thống không chuyển Failed chỉ vì timeout.

**AC-PAY-03 – Callback trùng/sai thứ tự**
- Given cùng sự kiện callback gửi 3 lần, Then chỉ một lần tạo tác động (một Confirmed, một bản ghi Captured).
- Given callback "Failed" đến sau "Captured", Then trạng thái Captured không bị lùi; ghi nhận và đối soát.

**AC-PAY-04 – Thử lại**
- Given thanh toán lần 1 thất bại và reservation còn hạn, When buyer thử lại, Then tạo được lần thử mới mà không để lại giao dịch trùng; hết hạn thì không thử lại được.

**AC-PAY-05 – Hoàn tiền**
- Given Payment Captured, When người có quyền tạo hoàn tiền ≤ số còn có thể hoàn, Then trạng thái đi qua Refund Pending → Refunded và buyer được thông báo.
- Số tiền hoàn vượt số còn lại bị từ chối.
- Hoàn tiền lặp (cùng yêu cầu) không tạo lệnh thứ hai.
- Người không có quyền không tạo được; vượt ngưỡng phải có người phê duyệt; mọi bước có audit.

**AC-PAY-06 – Đối soát**
- Given báo cáo cổng chứa giao dịch không có ở ReMarket (hoặc ngược lại), When chạy đối soát, Then tạo hồ sơ Reconciliation Required, không xóa lịch sử.
- Giao dịch khớp được đánh dấu đã đối soát.

**AC-PAY-07 – Đơn lỗi cập nhật sau thanh toán**
- Given Payment đã Captured nhưng Order tạm không cập nhật được, When Order phục hồi, Then sự kiện được phát lại và đơn thành Confirmed; đơn không bị Cancelled do lỗi nội bộ.
- Nếu quá ngưỡng thời gian, phát cảnh báo.

**AC-PAY-08 – Giải ngân**
- Given đơn Completed không có khiếu nại, When đến chu kỳ, Then đơn được đưa vào lô giải ngân đúng số tiền.
- Given đơn chuyển Disputed, Then bị loại khỏi lô.
- Mỗi đơn chỉ nằm trong tối đa một lô thành công.

**AC-PAY-09 – Dữ liệu thẻ**
- Cơ sở dữ liệu, log, trace không chứa số thẻ đầy đủ/CVV (kiểm tra quét dữ liệu).

**AC-PAY-10 – Bị trừ tiền nhưng đơn chưa hiện**
- Given buyer báo bị trừ tiền, When support tra cứu, Then thấy được trạng thái payment ↔ order ↔ nhà cung cấp, và có thể mở hồ sơ đối soát; buyer thấy trạng thái "đang điều tra" kèm mã vụ việc.

## 7. Giao nhận (AC-SHP)

**AC-SHP-01 – Tạo vận đơn**
- Given đơn Confirmed, When seller xác nhận chuẩn bị, Then shipment được tạo qua adapter, mã vận đơn hiển thị cho seller và buyer.
- Cùng yêu cầu tạo gửi lại không tạo vận đơn thứ hai.
- Seller không xử lý quá hạn (giá trị cấu hình) thì đơn bị hủy và hoàn tiền theo chính sách.

**AC-SHP-02 – Trạng thái chuẩn**
- Given hai hãng dùng mã trạng thái khác nhau cho cùng ý nghĩa, Then cả hai hiển thị cùng trạng thái chuẩn cho buyer/seller.
- Mọi trạng thái hiển thị đều thuộc bộ trạng thái chuẩn.

**AC-SHP-03 – Webhook trùng/trễ/sai thứ tự**
- Given webhook trùng, Then bỏ qua tác động.
- Given "Delivered" đến trước "In Transit" (sai thứ tự), Then trạng thái không bị lùi sai; lịch sử vẫn ghi sự kiện.
- Webhook không có chữ ký hợp lệ bị từ chối.

**AC-SHP-04 – Hãng không phản hồi**
- Given hãng timeout khi tạo vận đơn, Then shipment ở trạng thái chờ; retry dùng cùng idempotency/tham chiếu; sau khi hãng hồi phục không tồn tại hai vận đơn cho một đơn; vượt giới hạn retry thì vào hàng đợi xử lý và cảnh báo.

**AC-SHP-05 – Giao thất bại/hoàn hàng**
- Given hãng báo Delivery Failed hoặc Returned, Then đơn không chuyển Completed, vụ việc được mở và buyer/seller được thông báo bước tiếp theo.

**AC-SHP-06 – Thêm hãng mới**
- Given adapter mới tuân thủ hợp đồng chung, When bật hãng, Then báo giá/tạo/hủy/theo dõi hoạt động qua các service khác mà không sửa Order, Payment, giao diện.
- Trạng thái chưa được ánh xạ ghi Unknown và cảnh báo, không được hiển thị là thành công.

**AC-SHP-07 – Chia sẻ dữ liệu tối thiểu**
- Dữ liệu gửi hãng chỉ gồm trường cần giao hàng; không có email/tài khoản/thanh toán của người dùng.
- Seller chỉ thấy thông tin buyer cần để bàn giao.

**AC-SHP-08 – Đối soát giao nhận**
- Given shipment quá hạn không cập nhật, When chạy đối soát, Then truy vấn hãng và cập nhật trạng thái; nếu vẫn không rõ thì mở vụ việc.

## 8. Thông báo (AC-NTF)

**AC-NTF-01** – Given đơn xác nhận/hủy, thanh toán đổi, giao hàng đổi, Then người nhận phù hợp nhận thông báo đúng nội dung và không chứa PII thừa/bí mật.
**AC-NTF-02** – Given người dùng tắt tin tiếp thị, Then không nhận tin tiếp thị nhưng vẫn nhận thông báo đơn và bảo mật; không tắt được hai loại sau.
**AC-NTF-03** – Given tin tiếp thị SMS/push tạo lúc 23:00, Then gửi sau 08:00 (giờ cấu hình); OTP/giao dịch gửi ngay.
**AC-NTF-04** – Given đối tác gửi tin lỗi tạm thời, Then retry theo backoff, đơn hàng vẫn tiến triển; lỗi vĩnh viễn nằm trong hàng đợi lỗi và có cảnh báo.
**AC-NTF-05** – Given có thông báo, Then người dùng xem lại lịch sử và đánh dấu đã đọc; chỉ thấy thông báo của mình.

## 9. Báo cáo, kiểm duyệt (AC-TRU)

**AC-TRU-01** – Given buyer báo cáo tin, When gửi, Then nhận mã vụ việc; báo cáo trùng được gộp; tần suất báo cáo bị giới hạn.
**AC-TRU-02** – Given moderator mở hàng đợi, Then thấy vụ việc được giao theo bộ lọc; mở vụ việc thấy tin, ảnh, lịch sử; trạng thái vụ việc đi đúng vòng đời (Open → Triaged → Investigating → Resolved/Closed).
**AC-TRU-03** – Given moderator chọn "ẩn/gỡ", Then bắt buộc nhập lý do; hệ thống ghi tác nhân, lý do, thời điểm, trạng thái trước/sau; hành động áp dụng thật cho tin.
**AC-TRU-04** – Given tin đang có đơn Confirmed, When moderator gỡ tin, Then tin biến mất khỏi tìm kiếm nhưng đơn/thanh toán không đổi; một vụ việc giao dịch được tạo để xử lý riêng.
**AC-TRU-05** – Given vai trò moderator, Then API/giao diện không trả dữ liệu thanh toán và che PII không cần thiết (kiểm tra bằng gọi API trực tiếp).
**AC-TRU-06** – Given tin bị gỡ, Then seller nhận thông báo lý do và có đường kháng nghị (nếu chính sách bật); kháng nghị đưa vụ việc về Reopened.
**AC-TRU-07** – Given đơn Completed, When buyer đánh giá, Then lưu được một đánh giá/đơn; đơn chưa Completed hoặc đơn của người khác không đánh giá được.

## 10. Khiếu nại (AC-DSP)

**AC-DSP-01** – Given đơn Delivered trong 72 giờ (`BR-DSP-001`), When buyer mở khiếu nại có lý do/bằng chứng (ảnh ≤10 MB / video ≤50 MB), Then đơn chuyển Disputed và Escrow đóng băng tức thời; quá 72 giờ thì không mở được. Nếu phán quyết trả hàng, Buyer phải gửi hoàn trong 72 giờ và Seller có 48 giờ kiểm tra sau `POD` (`BR-DSP-005`).
**AC-DSP-02** – Given đơn Disputed, Then đơn bị loại khỏi giải ngân; khi quyết định "bác khiếu nại" thì được đưa lại vào giải ngân.
**AC-DSP-03** – Given khiếu nại mở, Then cả hai bên được yêu cầu bằng chứng trong hạn (24 giờ); hết hạn mà không phản hồi thì xử lý theo chính sách và ghi nhận.
**AC-DSP-04** – Given quyết định hoàn tiền, Then hoàn đúng số tiền quyết định, qua đúng quyền/ngưỡng phê duyệt, có audit; quyết định "bác" không phát sinh hoàn tiền.

## 11. Hỗ trợ (AC-SUP)

**AC-SUP-01** – Given support nhập mã đơn, Then thấy riêng trạng thái Order, Payment, Shipment kèm nguồn và thời điểm cập nhật; nếu mâu thuẫn thì có cảnh báo hiển thị.
**AC-SUP-02** – Given support mở PII đầy đủ, Then bắt buộc lý do và có bản ghi audit; mặc định PII bị che.
**AC-SUP-03** – Given support yêu cầu hủy/hoàn, Then đi qua quy trình phê duyệt; thao tác lặp không tác động hai lần; người không có quyền không thực hiện được.
**AC-SUP-04** – Given vụ việc, Then support ghi chú, gán, chuyển cấp, đóng; lịch sử đầy đủ.

## 12. Quản trị (AC-ADM)

**AC-ADM-01** – Given admin thêm danh mục kèm thuộc tính bắt buộc, Then seller tạo tin mới trong danh mục đó thấy thuộc tính; tin cũ không bị hỏng.
**AC-ADM-02** – Given admin cập nhật từ khóa cấm/tham số chính sách, Then có hiệu lực cho yêu cầu mới theo phiên bản cấu hình; không cần phát hành lại phần mềm.
**AC-ADM-03** – Mọi thay đổi cấu hình có bản ghi: ai, khi nào, giá trị cũ/mới; không sửa/xóa được bản ghi audit.
**AC-ADM-04** – Dashboard hiển thị số liệu khớp với dữ liệu nguồn trong sai số đã định; không hiển thị PII.

## 13. Vận hành, dữ liệu, phục hồi (AC-OPS)

**AC-OPS-01 – Truy vết**
- Given một giao dịch qua Listing→Order→Payment→Shipping, When tra theo correlation ID, Then thấy chuỗi log/trace đủ các bước; ID không chứa PII.

**AC-OPS-02 – Cảnh báo**
- Given tỷ lệ lỗi/độ trễ/backlog/DLQ/backup vượt ngưỡng đã duyệt, Then cảnh báo được gửi đúng kênh trong thời gian đã định và gắn runbook.

**AC-OPS-03 – Suy giảm có kiểm soát**
- Given Search ngừng, Then đăng nhập, xem tin theo danh mục, checkout, thanh toán vẫn hoạt động; tìm kiếm báo suy giảm.
- Given Notification ngừng, Then đơn vẫn tiến triển; thông báo xếp hàng và gửi sau khi phục hồi.
- Given đối tác thanh toán/vận chuyển lỗi, Then circuit breaker mở; người dùng thấy trạng thái chờ có mã tham chiếu; lỗi không lan sang service khác.

**AC-OPS-04 – Giám sát backup**
- Given backup thất bại hoặc bản gần nhất quá tuổi ngưỡng, Then có cảnh báo; dashboard hiển thị tuổi recovery point theo từng datastore.

**AC-OPS-05 – Khôi phục theo thời điểm**
- Given xóa nhầm dữ liệu ở T, When khôi phục SQL Server/MongoDB về T−δ vào môi trường cách ly, Then dữ liệu đọc được, toàn vẹn, mất dữ liệu ≤ RPO đã duyệt (khi có RPO).
- Given object S3 bị ghi đè/xóa, When phục hồi phiên bản trước, Then đối tượng đọc lại được và khớp với metadata.

**AC-OPS-06 – Đối soát sau khôi phục**
- Given SQL Server khôi phục về thời điểm khác MongoDB, When đối soát, Then phát hiện đúng các đơn–tin–thanh toán lệch; sau xử lý không còn tin Sold có đơn Cancelled hoặc ngược lại; traffic chỉ mở sau khi được duyệt.

**AC-OPS-07 – Diễn tập**
- Mỗi lần diễn tập có biên bản: phạm vi, điểm khôi phục, thời gian thực tế, kết quả kiểm tra journey, so sánh với RPO/RTO, hành động cải tiến; chưa đạt thì không ghi nhận đạt.

**AC-OPS-08 – Dựng lại dữ liệu dẫn xuất**
- Given xóa cache và index, When dựng lại từ nguồn chuẩn, Then dịch vụ phục hồi mà không mất dữ liệu nghiệp vụ; thời gian dựng được đo.

**AC-OPS-09 – Phê duyệt restore**
- Given yêu cầu restore hoặc đổi chính sách backup, Then không thực hiện được nếu thiếu phê duyệt; người thực hiện không phải người duy nhất phê duyệt; hành động có audit.

**AC-OPS-10 – Sự kiện có phiên bản**
- Given consumer cũ và producer phát sự kiện phiên bản mới tương thích, Then consumer cũ vẫn xử lý được; sự kiện không hợp lệ vào hàng đợi lỗi, có thể phát lại sau khi sửa.
- Consumer nhận trùng sự kiện không tạo tác động hai lần.

**AC-OPS-11 – Giả lập**
- Given cấu hình môi trường test, Then thanh toán và vận chuyển dùng bộ giả lập; không có credential production trong môi trường này.

**AC-OPS-12 – Chi phí**
- Given tài nguyên được gắn thẻ, Then báo cáo chi phí tách theo service/môi trường; chi phí vượt ngưỡng sinh cảnh báo.

## 14. Kịch bản kiểm thử chéo cần có (tham chiếu sang 08)

| Nhóm | Kịch bản | Nguồn |
|---|---|---|
| Đồng thời | N buyer cùng checkout một tin | AC-CHK-02; EX-002 |
| Lặp | Submit/callback/webhook lặp | AC-CHK-05, AC-PAY-03, AC-SHP-03; EX-003, 006, 011 |
| Timeout | Cổng thanh toán/hãng/Listing timeout | AC-PAY-02, AC-SHP-04; EX-005, 010, 015 |
| Bù trừ | Thanh toán lỗi sau khi giữ hàng | AC-CHK-04; EX-007 |
| Đối soát | Payment ↔ Order ↔ nhà cung cấp | AC-PAY-06, AC-PAY-10; EX-008, 021 |
| Quyền | Truy cập chéo tài nguyên, vai trò | AC-ACC-06, AC-TRU-05, AC-SUP-02 |
| Dữ liệu dẫn xuất | Search/cache mất hoặc trễ | AC-SRC-04..06, AC-OPS-08; EX-001, 016 |
| Phục hồi | Restore, lệch thời điểm, thiếu ảnh, mất khóa | AC-OPS-05..07, 09; EX-017..022 |

## 15. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo tiêu chí chấp nhận cho toàn bộ user story, gồm đường âm, lặp, đồng thời và phục hồi. |
| 1.1 | 13/11/2026 | Đồng bộ thông số chuẩn hóa (`72 giờ` cửa sổ khiếu nại, `≤10 MB` ảnh / `≤50 MB` video, phí sàn `5%`, Reverse Logistics `72h/48h`). |

