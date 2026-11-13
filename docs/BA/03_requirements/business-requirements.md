# Business Requirements – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Loại tài liệu | Business Requirements (BRQ) |
| Phiên bản | 1.0 |
| Trạng thái | Bản nháp; các mục "Cần xác nhận" phụ thuộc open-questions (02) |
| Ngày cập nhật | 12/11/2026 |
| Đầu vào | 00 Project Context, 01 Stakeholders, 02 Business Analysis |
| Tài liệu liên quan | functional-requirements.md, non-functional-requirements.md, use-cases.md, user-stories.md, acceptance-criteria.md |

## 1. Mục đích và phạm vi

Tài liệu phát biểu **yêu cầu nghiệp vụ** (cái tổ chức cần đạt) ở mức cao, độc lập giải pháp. Yêu cầu chức năng chi tiết nằm ở `functional-requirements.md`, chất lượng ở `non-functional-requirements.md`.

ReMarket là dự án **phân tích và thiết kế**; BRQ mô tả hệ thống đích cần thiết kế, không khẳng định đã vận hành. Các giá trị chưa có chủ sở hữu quyết định (phí, thời hạn, SLA, RPO/RTO, retention) được ghi là **Cần xác nhận** và dẫn về mã `Q-*` trong `open-questions-and-decisions.md`.

## 2. Quy ước

- **ID:** `BRQ-<nhóm>-<số>`. Nhóm: MKT (thị trường/listing), TXN (giao dịch), FUL (giao nhận), TRU (tin cậy), ACC (tài khoản), PLT (nền tảng/dữ liệu), GOV (quản trị dự án).
- **Ưu tiên (MoSCoW):** M = Must, S = Should, C = Could, W = Won't (đợt này). Ưu tiên là đề xuất, chờ Product Owner duyệt.
- **Trạng thái:** Đề xuất / Cần xác nhận / Đã duyệt (chưa có mục nào Đã duyệt).
- Mỗi BRQ truy vết về mục tiêu `OBJ-*`, nhu cầu `NEED-*`, quy tắc `BR-*`.

## 3. Danh mục yêu cầu nghiệp vụ

### 3.1. Tài khoản và quyền (CAP-CUS)

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Truy vết | Trạng thái |
|---|---|---|---|---|
| BRQ-ACC-01 | Nền tảng cho phép người dùng đăng ký, đăng nhập và có hồ sơ cá nhân để mua và/hoặc bán. | M | OBJ-01/02; CAP-CUS-01/03 | Đề xuất |
| BRQ-ACC-02 | Một tài khoản có thể đồng thời là người mua và người bán; quyền xác định theo quyền sở hữu tài nguyên. | M | BR-ACT-002; Q-BIZ-01 | Cần xác nhận |
| BRQ-ACC-03 | Nền tảng kiểm soát truy cập theo vai trò (Guest, Buyer, Seller, Moderator, Support, Business Admin, Platform Ops), mặc định từ chối. | M | NEED-BUY-08; BR-ACT-001..004 | Đề xuất |
| BRQ-ACC-04 | Dữ liệu cá nhân (PII) chỉ được dùng và hiển thị tối thiểu theo mục đích giao dịch/hỗ trợ. | M | NEED-BUY-08, NEED-SELL-06, BR-ACT-005 | Đề xuất |

### 3.2. Đăng bán và khám phá (CAP-MKT)

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Truy vết | Trạng thái |
|---|---|---|---|---|
| BRQ-MKT-01 | Người bán đăng được tin hàng cũ với mô tả tình trạng có cấu trúc, giá, danh mục và ảnh thật. | M | OBJ-02; NEED-SELL-01; BR-LST-001..004 | Đề xuất |
| BRQ-MKT-02 | Người bán quản lý vòng đời tin (nháp, công bố, tạm ẩn, đã bán, gỡ) theo trạng thái hợp lệ. | M | NEED-SELL-02; BR-LST-006..008 | Đề xuất |
| BRQ-MKT-03 | Người mua tìm kiếm, lọc và sắp xếp tin theo danh mục, tình trạng, giá. | M | OBJ-01; NEED-BUY-02; BR-SRC-003 | Đề xuất |
| BRQ-MKT-04 | Người mua thấy thông tin tình trạng, lỗi và ảnh đủ để quyết định; nền tảng không ngụ ý "đã kiểm định" khi chưa xác minh. | M | NEED-BUY-01; BR-LST-003 | Đề xuất |
| BRQ-MKT-05 | Nền tảng ngăn niêm yết hàng cấm/hạn chế và nội dung vi phạm chính sách. | M | BR-PRO-001..003; Q-BIZ-04 | Cần xác nhận |
| BRQ-MKT-06 | Quản trị viên quản lý danh mục, thuộc tính bắt buộc theo danh mục. | S | CAP-MKT-01; J-06 | Đề xuất |

### 3.3. Giao dịch (CAP-TXN)

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Truy vết | Trạng thái |
|---|---|---|---|---|
| BRQ-TXN-01 | Mỗi món hàng đơn chiếc không được xác nhận cho nhiều đơn cùng lúc (không bán trùng). | M | OBJ-03; NEED-BUY-03, NEED-SELL-03; BR-ORD-001..003 | Đề xuất |
| BRQ-TXN-02 | Người mua đặt mua và thanh toán trực tuyến; kết quả thanh toán được phản ánh đúng trong đơn. | M | OBJ-04; BR-PAY-001..005 | Cần xác nhận (COD: Q-OPS-05) |
| BRQ-TXN-03 | Hàng được giữ có thời hạn trong checkout và tự giải phóng khi hết hạn hoặc thanh toán thất bại. | M | BR-ORD-009, BR-ORD-011..013; Q-BIZ-03 | Cần xác nhận (thời hạn) |
| BRQ-TXN-04 | Đơn lưu bản ghi (snapshot) giá và thuộc tính tại thời điểm mua để làm bằng chứng. | M | BR-ORD-004 | Đề xuất |
| BRQ-TXN-05 | Người mua/bán hủy đơn theo quy tắc xác định; tác động tới giữ hàng, thanh toán, giao hàng được xử lý nhất quán. | M | BR-ORD-007; Q-BIZ-11 | Cần xác nhận |
| BRQ-TXN-06 | Người mua nhận tiền hoàn theo chính sách khi hủy/trả/tranh chấp hợp lệ. | S | BR-PAY-006..007; BR-DSP-004 | Cần xác nhận |
| BRQ-TXN-07 | Tiền thu hộ được giải ngân cho người bán sau khi đơn hoàn tất; phạm vi đợt 1 là giải ngân theo lô, không gồm ví nội bộ/escrow đa tầng. | S | BR-PAY-010..012; Q-OPS-06 | Cần xác nhận |
| BRQ-TXN-08 | Giao dịch lỗi giữa chừng (timeout, callback trùng, thất bại một phần) được phát hiện, bù trừ hoặc đối soát, không để trạng thái dở dang bị bỏ sót. | M | OBJ-03; EX-005..009, EX-015; BR-PAY-004 | Đề xuất |

### 3.4. Giao nhận (CAP-FUL)

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Truy vết | Trạng thái |
|---|---|---|---|---|
| BRQ-FUL-01 | Đơn đã xác nhận được chuyển sang giao nhận qua hãng vận chuyển; người mua và người bán theo dõi trạng thái thống nhất. | M | OBJ-04; BR-SHP-001..003 | Cần xác nhận (hãng: Q-OPS-04) |
| BRQ-FUL-02 | Nền tảng hỗ trợ nhiều hãng vận chuyển qua một hợp đồng tích hợp chung, không phụ thuộc một hãng. | S | DEC-DIR-05; CAP-FUL-05 | Đề xuất |
| BRQ-FUL-03 | Giao thất bại/hoàn hàng/mất hàng kích hoạt quy trình xử lý, không tự hoàn tất đơn. | M | BR-SHP-005; EX-012 | Đề xuất |

### 3.5. Tin cậy, hỗ trợ và thông báo (CAP-TRU, CAP-SUP)

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Truy vết | Trạng thái |
|---|---|---|---|---|
| BRQ-TRU-01 | Người dùng báo cáo tin/người dùng vi phạm; moderator xử lý có lý do, quyền hạn và dấu vết. | M | OBJ-05; BR-MOD-001..006 | Đề xuất |
| BRQ-TRU-02 | Người mua mở khiếu nại sau khi nhận hàng; nền tảng đóng băng giải ngân trong lúc xử lý. | S | BR-DSP-001..004 | Cần xác nhận |
| BRQ-TRU-03 | Người mua đánh giá người bán sau giao dịch đủ điều kiện. | C | BR-REV-001..002; Q-BIZ-13 | Cần xác nhận |
| BRQ-TRU-04 | Nhân viên hỗ trợ tra cứu timeline đơn–thanh toán–giao hàng theo quyền, dữ liệu nhạy cảm được che. | M | NEED-SUP-01..03 | Đề xuất |
| BRQ-TRU-05 | Các bên nhận thông báo kịp thời khi trạng thái đơn, thanh toán, giao hàng, kiểm duyệt thay đổi. | M | BR-NTF-001..004 | Đề xuất |

### 3.6. Nền tảng và dữ liệu (CAP-PLT)

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Truy vết | Trạng thái |
|---|---|---|---|---|
| BRQ-PLT-01 | Mỗi service sở hữu dữ liệu của mình; dữ liệu tìm kiếm và cache là dữ liệu dẫn xuất, không quyết định giao dịch. | M | BR-SYS-001..002; DEC-DIR-06 | Đề xuất |
| BRQ-PLT-02 | Hệ thống có thể mở rộng, chịu lỗi từng phần và duy trì luồng mua–bán trọng yếu khi một thành phần phụ thuộc gặp sự cố. | M | OBJ-06 | Đề xuất; ngưỡng cần xác nhận (Q-DR-01) |
| BRQ-PLT-03 | Dữ liệu được sao lưu, khôi phục và diễn tập khôi phục có kiểm chứng; HA/replication không thay thế backup. | M | OBJ-07; BR-SYS-004..008 | Cần xác nhận (RPO/RTO) |
| BRQ-PLT-04 | Hệ thống quan sát được xuyên service (log, metric, trace, cảnh báo) và truy vết giao dịch theo mã tương quan. | M | NEED-OPS-03; MET-PLAT-* | Đề xuất |
| BRQ-PLT-05 | Dữ liệu và bí mật được bảo vệ khi truyền và khi lưu; quyền vận hành tách khỏi quyền nghiệp vụ. | M | NEED-OPS-02; BR-SYS-009 | Đề xuất |
| BRQ-PLT-06 | Chi phí hạ tầng được đo lường và kiểm soát theo workload. | S | NEED-OPS-04; MET-PLAT-08 | Đề xuất |

### 3.7. Quản trị dự án (CAP-GOV)

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Truy vết | Trạng thái |
|---|---|---|---|---|
| BRQ-GOV-01 | Mỗi yêu cầu truy vết được tới mục tiêu, nhu cầu, quy tắc, kiểm thử và quyết định. | M | OBJ-08 | Đề xuất |
| BRQ-GOV-02 | Quyết định và giả định được ghi nhận kèm người duyệt, ngày và tài liệu bị ảnh hưởng. | M | VS-05 | Đề xuất |

## 4. Ngoài phạm vi (Won't – đợt này)

| ID | Nội dung | Căn cứ |
|---|---|---|
| BRQ-OUT-01 | Ví điện tử nội bộ, escrow đa tầng. | scope §9 |
| BRQ-OUT-02 | Seller doanh nghiệp: bulk import, nhiều nhân viên, tích hợp POS/ERP. | Q-BIZ-14; capability map §6 |
| BRQ-OUT-03 | Đấu giá, cho thuê, quảng cáo/promoted listing, đề xuất bằng AI. | scope §9 |
| BRQ-OUT-04 | Kiểm định vật lý, ký gửi, KYC/AML đầy đủ. | problem-statement §6 |
| BRQ-OUT-05 | Giỏ hàng nhiều người bán với tách thanh toán/giao hàng. | BR-ORD-008; Q-BIZ-10 |
| BRQ-OUT-06 | Đa quốc gia, đa tiền tệ. | Q-BIZ-02 |

## 5. Ma trận truy vết mục tiêu → yêu cầu nghiệp vụ

| Mục tiêu | Yêu cầu nghiệp vụ |
|---|---|
| OBJ-01 | BRQ-MKT-03, BRQ-MKT-04, BRQ-ACC-01 |
| OBJ-02 | BRQ-MKT-01, BRQ-MKT-02, BRQ-MKT-06 |
| OBJ-03 | BRQ-TXN-01, BRQ-TXN-03, BRQ-TXN-08 |
| OBJ-04 | BRQ-TXN-02, BRQ-TXN-05, BRQ-FUL-01..03, BRQ-TRU-05 |
| OBJ-05 | BRQ-TRU-01..04, BRQ-MKT-05, BRQ-ACC-03 |
| OBJ-06 | BRQ-PLT-01, 02, 04, 05, 06 |
| OBJ-07 | BRQ-PLT-03 |
| OBJ-08 | BRQ-GOV-01, BRQ-GOV-02 |

## 6. Giả định và phụ thuộc

- Mặc định một tin đăng = một món hàng đơn chiếc (A-05, Q-BIZ-03).
- Mặc định một đơn chỉ có một người bán (BR-ORD-008, Q-BIZ-10).
- Phương thức thanh toán, hãng vận chuyển, market, tiền tệ chưa chọn: yêu cầu liên quan chỉ ở mức hợp đồng tích hợp.
- Các thông số định lượng (hold window 15 phút, hạn khiếu nại 72 giờ, phí sàn 5%, ảnh ≤10 MB, giờ yên tĩnh, số ảnh tối đa) đã được chuẩn hóa theo baseline `DEC-BIZ-01..10` trong `open-questions-and-decisions.md` và `business-rules.md`; thiết kế vẫn phải hỗ trợ tham số hóa qua cấu hình.

## 7. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo danh mục yêu cầu nghiệp vụ, phạm vi loại trừ và ma trận truy vết mục tiêu. |
| 1.1 | 13/11/2026 | Đồng bộ thông số chuẩn hóa theo `DEC-BIZ-01..10` (`72h` khiếu nại, `5%` phí sàn, ảnh `≤10 MB`). |

