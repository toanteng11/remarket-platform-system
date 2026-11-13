# Business Scenarios and Exceptions – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.0 |
| Trạng thái | Kịch bản phân tích; policy/acceptance cụ thể cần xác nhận |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | to-be-processes.md, business-rules.md, user-journeys.md, non-functional-requirements.md, open-questions-and-decisions.md |

## 1. Mục đích

Tập hợp các tình huống bình thường và ngoại lệ có thể xảy ra trong marketplace phân tán. Dùng để elicitation yêu cầu, thiết kế state transition, xác định nguồn dữ liệu có thẩm quyền, xử lý idempotency, consistency, reconciliation và kiểm thử phục hồi.

Các expected outcome ở đây là đề xuất. Chính sách thương mại, cách thông báo, thời hạn chờ, ngưỡng retry, RPO/RTO và mức bồi hoàn đều cần người có thẩm quyền quyết định.

## 2. Quy ước

- **Nguồn chuẩn:** service sở hữu aggregate/dữ liệu nghiệp vụ; search/cache không phải nguồn cuối cho giao dịch.
- **Idempotency:** gửi lại cùng yêu cầu/sự kiện không tạo thêm order, payment, shipment hoặc side effect.
- **Pending/Unknown:** kết quả chưa xác định; không tự chuyển thành thành công hoặc thất bại.
- **Compensation:** hành động nghiệp vụ bù trừ khi nhiều bước đã hoàn tất một phần; không giả định rollback phân tán nguyên tử.
- **Correlation:** giữ mã tham chiếu xuyên order, payment, shipping, support và log mà không đưa PII vào ID.

## 3. Kịch bản thành công chuẩn

| ID | Tình huống | Kết quả nghiệp vụ kỳ vọng | Rules/process |
|---|---|---|---|
| SCN-001 | Seller tạo listing hợp lệ, tải ảnh và công bố. | Listing có owner, ID, trạng thái published; media tham chiếu được bảo vệ; search được cập nhật sau đó. | P-02; BR-LST-001…004 |
| SCN-002 | Buyer tìm listing còn bán và hoàn tất checkout. | Availability được xác nhận, order snapshot được tạo đúng một lần, buyer thấy trạng thái bước tiếp. | P-03/P-04; BR-ORD-001…005 |
| SCN-003 | Payment được xác nhận thành công. | Payment gắn đúng order/amount; order tiến sang trạng thái hợp lệ; fulfillment chỉ bắt đầu theo policy. | P-05; BR-PAY-001…005 |
| SCN-004 | Carrier cập nhật trạng thái giao hàng theo hợp đồng. | Event được xác thực, chuẩn hóa, chống trùng và hiển thị trong timeline. | P-06; BR-SHP-001/002 |
| SCN-005 | Đơn giao thành công và đủ điều kiện review. | Order được hoàn tất theo rule; buyer/seller được thông báo; review eligibility được áp dụng. | P-07; BR-REV-001 |
| SCN-006 | User report listing đáng ngờ. | Case ID được cấp; moderator có queue, quyền tối thiểu và audit; quyết định theo policy. | P-08; BR-MOD-001…006 |

## 4. Kịch bản ngoại lệ và hành vi cần định nghĩa

| ID | Trigger/điều kiện | Kết quả tức thời đề xuất | Phục hồi/đối soát | Chủ thể/quyết định cần chốt |
|---|---|---|---|---|
| EX-001 | Search hiển thị listing đã bán do index trễ. | Checkout xác nhận lại với Listing và từ chối an toàn; buyer thấy thông báo dễ hiểu. | Gửi lại sự kiện/index update; theo dõi lag. | Listing/Search owner; freshness expectation |
| EX-002 | Hai buyer checkout đồng thời một món đơn chiếc. | Chỉ một reservation/order được chấp nhận; request còn lại nhận kết quả không khả dụng. | Idempotent retry; kiểm tra trạng thái reservation/order. | Product Owner xác nhận single-item model; Architect |
| EX-003 | Buyer nhấn submit nhiều lần hoặc client retry sau timeout. | Cùng idempotency key trả cùng kết quả; không nhân đôi order/payment intent. | Tra cứu theo request key và xử lý response. | Order/Payment owners; TTL/key policy |
| EX-004 | Listing bị seller ẩn/sửa trong lúc buyer checkout. | Checkout dùng phiên bản/giá được xác minh; nếu không còn hợp lệ thì yêu cầu buyer xác nhận lại hoặc dừng. | Lưu lịch sử phiên bản/snapshot. | Product Owner xác định sửa nào làm mất hiệu lực |
| EX-005 | Payment provider timeout sau khi đã nhận yêu cầu. | Đơn/payment ở trạng thái pending/unknown; không gửi charge mù và không giao hàng. | Query provider/reconciliation; kết quả cuối chuyển trạng thái idempotent. | Payment/Finance; provider contract; thời hạn chờ |
| EX-006 | Callback payment trùng hoặc đến sai thứ tự. | Xác minh chữ ký/ref/event; bỏ qua side effect trùng; không lùi trạng thái hợp lệ. | Lưu event ID, version/time; reconciliation nếu mâu thuẫn. | Payment owner; provider semantics |
| EX-007 | Payment thất bại sau khi listing được giữ. | Hiển thị lỗi/trạng thái và áp dụng retry/cancel theo policy. | Giải phóng reservation sau điều kiện/thời hạn đã duyệt. | Product Owner: hold window, retry, expiry |
| EX-008 | Payment thành công nhưng cập nhật Order bị lỗi. | Payment giữ bằng chứng thành công; order không bị đánh dấu thất bại giả. | Outbox/event retry hoặc reconciliation; tạo cảnh báo nếu vượt ngưỡng. | Order/Payment/Ops; compensation/refund policy |
| EX-009 | Buyer hủy sau khi đã payment nhưng trước khi shipment. | Yêu cầu vào trạng thái review/hủy hợp lệ; không tự khẳng định refund tức thì. | Refund theo quyền; giải phóng hàng chỉ khi policy cho phép. | Cancellation/refund/fees policy |
| EX-010 | Carrier API không phản hồi khi tạo shipment. | Shipment creation ở pending/unknown; tránh tạo vận đơn trùng. | Tra cứu bằng idempotency/provider reference; retry có giới hạn hoặc xử lý thủ công. | Shipping/Ops; partner contract |
| EX-011 | Carrier gửi webhook trùng, trễ hoặc lùi trạng thái. | Xác thực event; giữ trạng thái chuẩn không lùi sai; lưu raw/canonical reference cần thiết. | Re-query/đối soát với provider. | Shipping owner; thứ tự event/canonical mapping |
| EX-012 | Shipment giao thất bại, hoàn về hoặc tracking mâu thuẫn. | Order không tự chuyển completed; mở/cập nhật case và thông báo phù hợp. | Điều tra, return/refund/reship theo policy. | Logistics/Product Owner |
| EX-013 | Seller bị suspend khi có listing/order đang mở. | Chặn hành động mới theo phạm vi suspension; xử lý đơn/case đang mở riêng. | Handover/escalation, không xóa lịch sử. | Trust/Legal/Product Owner |
| EX-014 | Moderator gỡ listing đang gắn với order/payment. | Listing bị hạn chế hiển thị; order/payment không bị sửa ngầm. | Case điều tra và quyết định riêng cho giao dịch đang mở. | Moderation/Order/Payment owners |
| EX-015 | Service/database tạm unavailable trong lúc checkout. | Không báo thành công khi chưa ghi durable; trả lỗi/pending có mã tham chiếu. | Retry an toàn; phát hiện request đã commit trước timeout. | Architect/SRE; SLO/error policy |
| EX-016 | Cache/search được xóa hoặc hỏng. | Core order/listing/payment vẫn dựa trên nguồn chuẩn; khám phá có thể degraded. | Rebuild từ nguồn chuẩn/event history; theo dõi thời gian rebuild. | Search/Platform; RTO cho projection |
| EX-017 | SQL Server hoặc MongoDB bị lỗi logic/xóa nhầm. | Dừng thao tác gây thêm sai lệch; phân loại ảnh hưởng và điểm khôi phục. | Restore isolated, validate, reconcile event/service data, business approval. | DBA/Business owner; RPO/RTO/retention |
| EX-018 | Restore database thành công nhưng object ảnh trong S3 thiếu/không đọc được. | Listing thiếu media không được âm thầm coi là đầy đủ; đánh dấu phạm vi ảnh hưởng. | Đối soát object key/version, phục hồi object và kiểm tra quyền mã hóa. | Storage owner; S3 version/replication policy |
| EX-019 | Khôi phục một service tới thời điểm khác service phụ thuộc. | Trạng thái liên service coi là cần reconciliation; không mở toàn bộ traffic ngay. | So khớp order/payment/listing/shipping; replay an toàn hoặc sửa có audit. | Incident commander, DBA và business owner |
| EX-020 | Key/credential cần cho restore không khả dụng. | Không mở quyền rộng để “cho chạy”; dùng quy trình break-glass được phê duyệt. | Khôi phục quyền/key theo tách biệt nhiệm vụ và log audit. | Security/Cloud; key recovery control |
| EX-021 | Người dùng report charge nhưng order không hiện. | Mở case với correlation; hiển thị trạng thái pending điều tra. | Reconciliation payment ↔ order ↔ provider; quyết định tiếp tục/refund. | Support/Payment/Finance |
| EX-022 | Restore drill thất bại hoặc vượt mục tiêu. | Ghi nhận mục tiêu chưa đạt; không đánh dấu continuity đạt chỉ vì backup thành công. | RCA, cập nhật kiến trúc/runbook/permission, diễn tập lại. | Sponsor/DBA/SRE/Security |
| EX-023 | API đối tác trả trạng thái không nằm trong mapping. | Ghi nhận unknown, không ép vào trạng thái success; cảnh báo/queue xử lý. | Cập nhật mapping có version và kiểm tra hồi quy. | Integration owner |
| EX-024 | Người dùng có nhiều session yêu cầu cùng thao tác nhạy cảm. | Kiểm tra quyền và trạng thái mới nhất trước khi thực hiện; audit actor/context. | Nếu conflict thì yêu cầu tải lại/xác nhận; không overwrite âm thầm. | Security/Product Owner |
| EX-025 | Buyer tạo đơn `AWAITING_PAYMENT` rồi liên tục hủy/bỏ ngang để găm tin đăng `Inventory = 1` (Anti-Hoarding Abuse). | Giới hạn tối đa 02 đơn `AWAITING_PAYMENT` đồng thời/Buyer; áp dụng cooldown 30 phút và tối đa 02 lần hold/ngày cho cùng `(BuyerId, ListingId)` (`BR-ORD-014`). | Trả HTTP `429` kèm thời điểm mở khóa; ghi log hành vi bất thường vào Fraud/Risk signal. | Product Owner/Backend |
| EX-026 | Seller khai báo thiếu cân nặng/kích thước khiến 3PL thu phụ trội khi cân đo thực tế tại bưu cục. | Nhận webhook/đối soát phụ phí từ 3PL; ghi nhận `OverweightSurcharge` vào sổ phụ đơn hàng (`BR-PAY-014`). | Khấu trừ tự động `OverweightSurcharge` vào `NetSellerAmount` trước khi giải ngân; cảnh báo Seller tái phạm. | Finance/Operations |
| EX-027 | Phán quyết `VERDICT_RETURN_AND_REFUND` nhưng Buyer không gửi trả hàng qua 3PL trong vòng 72 giờ. | Timer tự động kiểm tra trạng thái vận đơn chiều về sau `72 giờ` kể từ khi ra phán quyết (`BR-DSP-005`). | Chuyển khiếu nại sang `RETURN_EXPIRED_TO_SELLER`, đóng dispute và giải phóng Escrow giải ngân cho Seller. | Trust & Safety/Product |
| EX-028 | Hàng hoàn về Seller có `POD` từ 3PL nhưng Seller im lặng quá 48 giờ, hoặc Seller mở phản khiếu nại nhận nhầm/rỗng hộp. | Nếu im lặng > 48 giờ sau `POD`, hệ thống chuyển `RETURN_AUTO_REFUNDED` và hoàn 100% Buyer; nếu Seller gửi video khui hàng phản khiếu nại, chuyển `RETURN_DISPUTED_BY_SELLER` cho Trọng tài xử lý (`BR-DSP-005`). | Giữ `Escrow FROZEN` trong lúc Trọng tài xem xét video khui hàng của Seller; lưu bằng chứng vào S3 WORM. | Trust & Safety/Operations |
| EX-029 | 3PL làm mất hoặc hư hỏng nặng món hàng giá trị cao (`>= 3.000.000 VNĐ`) trong quá trình vận chuyển. | Xác nhận trạng thái `LOST`/`DAMAGED` từ webhook 3PL; kích hoạt cơ chế hoàn tiền ngay cho Buyer (`BR-SHP-006`). | Hoàn 100% cho Buyer từ Escrow (không bắt chờ 7–15 ngày) và tạo hồ sơ đòi bồi thường Bảo hiểm khai giá 3PL về quỹ sàn. | Finance/Operations |
| EX-030 | Giao hàng thất bại 3 lần liên tiếp do Buyer không nghe máy/từ chối nhận hàng (`RTS`). | 3PL hoàn tất chuyển hoàn về Seller (`RETURNED`); tính cước giao đi và cước chuyển hoàn (`BR-SHP-005`). | Hoàn tiền hàng cho Buyer sau khi khấu trừ cước 3PL hai chiều; miễn phí sàn (`0 VNĐ`) và hạ điểm uy tín Buyer nếu tái phạm. | Finance/Product |
| EX-031 | Seller quá hạn 48 giờ kể từ `PAID_ESCROW` nhưng không xác nhận đóng gói/bàn giao 3PL (`BR-ORD-015`). | Job quét quá hạn đóng gói `48 giờ` tự động hủy đơn (`CANCELLED_SELLER_TIMEOUT`), giải phóng/khóa tin đăng tùy lý do và hoàn tiền Buyer. | Hoàn tiền 100% qua Payment Service, trừ điểm tin cậy Seller và ẩn tin nếu tái phạm. | Operations/Product |

## 5. Mẫu đặc tả scenario (dùng cho BA 03)

Mỗi scenario được chuyển sang đặc tả chi tiết cần có:

- ID, mục tiêu, actor, trigger và tần suất/criticality nếu biết.
- Preconditions, dữ liệu đầu vào, state trước khi xử lý.
- Main flow và alternate/exception flow theo bước.
- Quyết định nghiệp vụ cần owner duyệt.
- Source of truth và event/API liên quan.
- Postcondition/state transition, side effect và compensation.
- Idempotency, timeout, retry, ordering và reconciliation.
- Permission/privacy/audit requirements.
- Acceptance criteria có thể kiểm tra, gồm negative và recovery path.
- Truy vết tới user need, business rule, NFR, service/data owner.

## 6. Ưu tiên phân tích ngoại lệ

**Ưu tiên trước khi thiết kế checkout:** EX-001…009 và EX-015.  
**Ưu tiên trước khi chọn đối tác giao nhận:** EX-010…012, EX-023.  
**Ưu tiên cho database/cloud design:** EX-016…022.  
**Ưu tiên cho Admin/Back-office:** EX-013/014/024.

Việc xếp nhóm không phải thứ tự triển khai; mức ưu tiên cần được Product Owner/Sponsor xác nhận.

## 7. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo baseline success/exception scenarios phục vụ phân tích yêu cầu và phục hồi. |
| 1.1 | 13/11/2026 | Bổ sung các kịch bản ngoại lệ `EX-025..031` khép kín lỗ hổng găm hàng, phụ trội cân nặng 3PL, Reverse Logistics `72h/48h`, mất hàng giá trị cao, bom hàng `RTS` và quá hạn đóng gói `48h`. |
