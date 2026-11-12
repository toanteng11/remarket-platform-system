# Business Objectives and Success Metrics – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Mục tiêu định hướng; KPI và ngưỡng chưa được phê duyệt |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | problem-statement.md, scope-and-boundaries.md, user-needs-and-pain-points.md, non-functional-requirements.md |

## 1. Mục đích và nguyên tắc

Tài liệu xác định kết quả mà dự án phân tích/thiết kế cần hướng tới và cách đo lường để đánh giá giải pháp. Vì ReMarket hiện là dự án phân tích và thiết kế, các chỉ số bên dưới là **định nghĩa đo lường dự kiến**, không phải số liệu hiện có, KPI đã cam kết hay mục tiêu đã phê duyệt.

Không tự gán target số, baseline, SLA hoặc mức tăng trưởng. Chủ dự án cần xác nhận định nghĩa, cửa sổ thời gian, nguồn dữ liệu, phân khúc và ngưỡng sau khi có phạm vi vận hành/dữ liệu thử nghiệm.

## 2. Cấp mục tiêu

| Cấp | Nội dung |
|---|---|
| Outcome kinh doanh | Giá trị người dùng và kết quả thương mại của marketplace khi có vận hành. |
| Outcome trải nghiệm/quy trình | Mức dễ hoàn tất hành trình, minh bạch và xử lý ngoại lệ. |
| Outcome tin cậy | Chất lượng listing, kiểm soát lạm dụng, hỗ trợ giao dịch công bằng. |
| Outcome nền tảng | Khả năng hoạt động, hiệu suất, bảo mật, quan sát và phục hồi dữ liệu. |
| Outcome dự án | Bộ phân tích và thiết kế có thể truy vết, có quyết định và rủi ro rõ ràng. |

Các cấp mục tiêu cần báo cáo riêng. Hệ thống kỹ thuật tốt không tự chứng minh marketplace đạt product-market fit.

## 3. Mục tiêu sơ bộ

| ID | Mục tiêu | Giá trị mong đợi | Chủ sở hữu cần xác nhận | Trạng thái |
|---|---|---|---|---|
| OBJ-01 | Giảm trở ngại để buyer tìm món phù hợp và hiểu tình trạng trước khi đặt. | Buyer quyết định dựa trên thông tin listing rõ ràng. | Product Owner | Định hướng |
| OBJ-02 | Giúp seller tạo và quản lý listing/đơn theo một quy trình nhất quán. | Ít thao tác lặp và ít nhầm trạng thái. | Product Owner | Định hướng |
| OBJ-03 | Bảo vệ tính đúng đắn của giao dịch cho mặt hàng đã qua sử dụng. | Giảm bán trùng, trạng thái sai và giao dịch dở dang không được nhận biết. | Product Owner, Architect | Đề xuất cần xác nhận |
| OBJ-04 | Minh bạch trạng thái đơn, thanh toán và giao hàng. | Buyer/seller biết bước tiếp theo và nơi cần hỗ trợ. | Product Owner, Operations | Đề xuất cần xác nhận |
| OBJ-05 | Hỗ trợ xử lý nội dung và giao dịch có trách nhiệm. | Quyết định moderation/support có lý do, quyền hạn và lịch sử. | Business Owner, Security | Cần xác nhận policy |
| OBJ-06 | Thiết kế nền tảng có thể mở rộng và vận hành được trên AWS. | Ranh giới dịch vụ, dữ liệu, tích hợp và chi phí được giải thích. | Sponsor, Architect | Định hướng dự án |
| OBJ-07 | Có kế hoạch backup, restore và phục hồi đã kiểm chứng. | Phục hồi theo RPO/RTO do chủ dự án duyệt. | Cloud/DBA, Sponsor | Mục tiêu; ngưỡng cần xác nhận |
| OBJ-08 | Hoàn thiện bộ tài liệu BA/system design có truy vết và quyết định. | Từng yêu cầu/thiết kế có nguồn, trạng thái và người duyệt. | Project Owner | Định hướng dự án |

## 4. Danh mục chỉ số đề xuất

### 4.1. Hành trình buyer

| ID | Chỉ số | Định nghĩa/công thức dự kiến | Phân đoạn và cảnh báo | Nguồn đo dự kiến |
|---|---|---|---|---|
| MET-BUY-01 | Tỷ lệ xem listing → bắt đầu checkout | Số phiên bắt đầu checkout sau khi xem listing / số phiên đủ điều kiện đã xem listing. | Phân biệt kênh, danh mục và nguồn traffic; đọc cùng chất lượng traffic. | Event analytics, listing/checkout |
| MET-BUY-02 | Tỷ lệ checkout hoàn tất | Số checkout chuyển tới kết quả nghiệp vụ xác định / số checkout bắt đầu trong cohort. | Định nghĩa rõ completed, pending, cancelled và cửa sổ cohort. | Order và payment events |
| MET-BUY-03 | Tỷ lệ checkout thất bại do listing không khả dụng | Số checkout bị từ chối do sold/reserved/ineligible / số lần xác nhận khả dụng. | Có thể phản ánh dữ liệu stale hoặc cạnh tranh cao; cần điều tra nguyên nhân. | Listing, checkout decision |
| MET-BUY-04 | Thời gian hoàn tất checkout | p50/p95 từ bắt đầu checkout đến kết quả; báo cáo pending riêng. | Không tính thời gian chờ giao hàng; cần chốt điểm bắt đầu/kết thúc. | Business events/tracing |
| MET-BUY-05 | Tỷ lệ giao dịch có yêu cầu hỗ trợ | Số đơn có case hỗ trợ / số đơn trong cùng cohort. | Phân loại theo lý do, carrier và phương thức thanh toán. | Support case và order |
| MET-ORD-01 | Tỷ lệ xung đột giữ hàng / bán trùng (Oversell Conflict Rate) | Số phiên checkout bị từ chối do món đơn chiếc đã bị giữ/mua bởi phiên khác / Tổng số phiên bắt đầu checkout. | Đo hiệu quả của cơ chế Atomic Reservation; nếu quá cao có thể do Search hiển thị tin cũ. | Order & Listing Service telemetry |
| MET-SRC-01 | Độ trễ đồng bộ chỉ mục tìm kiếm (Search Projection Lag) | Thời gian p95 từ khi Listing chuyển trạng thái (Published/Sold) đến khi Search Index phản ánh. | Đảm bảo tính tươi mới của kết quả tìm kiếm, giảm thiểu checkout hàng đã bán. | Event consumer lag metrics |

### 4.2. Hành trình seller và listing

| ID | Chỉ số | Định nghĩa/công thức dự kiến | Phân đoạn và cảnh báo | Nguồn đo dự kiến |
|---|---|---|---|---|
| MET-SELL-01 | Tỷ lệ listing hoàn tất đăng | Số listing đạt published / số listing bắt đầu tạo trong cohort. | Tách draft, bị từ chối, abandoned; quy tắc duyệt phải rõ. | Seller Center/listing |
| MET-SELL-02 | Thời gian tạo listing | Thời gian từ bắt đầu nhập tới submit/publish; báo cáo p50/p95. | Tách thời gian chờ duyệt khỏi thời gian thao tác seller. | Client/server timestamps |
| MET-SELL-03 | Tỷ lệ listing bị trả về/chặn | Listing bị từ chối/yêu cầu sửa / listing được review. | Chia nguyên nhân; không tối ưu bằng cách giảm kiểm duyệt mù quáng. | Moderation decisions |
| MET-SELL-04 | Tỷ lệ listing sold nhưng còn hiển thị khả dụng | Listing sold vẫn nhận tín hiệu khả dụng sau ngưỡng đồng bộ / listing sold. | Cần định nghĩa nguồn chuẩn và ngưỡng hội tụ. | Listing/search telemetry |
| MET-SELL-05 | Tỷ lệ seller hoàn tất nghĩa vụ giao hàng | Đơn không có bằng chứng bàn giao đúng quy tắc / đơn đủ điều kiện. | Policy thời hạn và bằng chứng còn mở. | Order/shipping events |

### 4.3. Giao dịch và tin cậy

| ID | Chỉ số | Định nghĩa/công thức dự kiến | Phân đoạn và cảnh báo | Nguồn đo dự kiến |
|---|---|---|---|---|
| MET-TRUST-01 | Tỷ lệ giao dịch hoàn tất | Đơn đạt trạng thái complete theo định nghĩa đã duyệt / đơn đủ điều kiện trong cohort. | Chốt rõ hủy, refund, dispute và giao hàng hoàn tất. | Order/payment/shipping |
| MET-TRUST-02 | Tỷ lệ report được xác nhận | Report có quyết định vi phạm / report đã xử lý. | Không dùng để khuyến khích kết luận quá mức; đọc cùng độ chính xác review. | Moderation case |
| MET-TRUST-03 | Thời gian xử lý case | Thời gian từ mở đến quyết định/đóng; p50/p95 theo loại case. | Theo dõi case đang mở riêng; không gộp severity. | Support/moderation case |
| MET-TRUST-04 | Tỷ lệ review hợp lệ sau đơn | Review gắn với đơn đủ điều kiện / đơn hoàn tất có quyền review. | Eligibility và chống review giả cần được duyệt. | Reviews/Order |
| MET-NTF-01 | Tỷ lệ gửi thông báo thành công | Số thông báo gửi thành công (Email/SMS/Push/In-app) / Tổng số sự kiện cần thông báo phát sinh. | Đo độ tin cậy của Notification Service và adapter nhà cung cấp. | Notification delivery logs |
| MET-NTF-02 | Độ trễ gửi thông báo trọng yếu | Thời gian p95 từ khi Domain Event phát sinh đến khi thông báo được gửi đi (cho đơn hàng/thanh toán). | Đảm bảo người mua và người bán nắm bắt tiến độ giao dịch kịp thời. | Notification tracing metrics |

### 4.4. Nền tảng, database và phục hồi

Các chỉ số này thuộc yêu cầu chất lượng/thiết kế. Target số cần được phê duyệt trong BA 03 hoặc quyết định kiến trúc.

| ID | Chỉ số | Định nghĩa/công thức dự kiến | Lưu ý | Nguồn đo dự kiến |
|---|---|---|---|---|
| MET-PLAT-01 | Tính sẵn sàng luồng nghiệp vụ | Thời gian luồng trọng yếu đạt tiêu chí / tổng thời gian đo trong cửa sổ đã thống nhất. | Endpoint riêng lẻ tốt không đồng nghĩa checkout hoàn chỉnh. | Synthetic checks, telemetry |
| MET-PLAT-02 | Độ trễ API/event | p50/p95/p99 theo endpoint, consumer và loại tải. | Tách latency nội bộ, provider và queue age. | CloudWatch/APM/tracing |
| MET-PLAT-03 | Tỷ lệ lỗi giao dịch kỹ thuật | Lỗi kỹ thuật không phải business rejection / yêu cầu đủ điều kiện. | Phân biệt validation, conflict hàng đã bán và lỗi hạ tầng. | Logs, metrics, trace |
| MET-PLAT-04 | Tình trạng backup | Backup thành công / lần dự kiến; kèm tuổi bản sao gần nhất. | Job thành công không chứng minh restore được. | Backup/database logs |
| MET-PLAT-05 | Tỷ lệ restore drill thành công | Lần restore đạt checklist/đối soát / lần diễn tập. | Đo riêng từng datastore và version. | Recovery records |
| MET-PLAT-06 | Sai lệch RPO/RTO thực tế | Mất dữ liệu/thời gian khôi phục trong test hoặc incident so với mục tiêu đã duyệt. | Không đặt target cho tới khi owner phê duyệt. | Drill/incident timeline |
| MET-PLAT-07 | Khả năng dựng lại dữ liệu dẫn xuất | Thời gian tái tạo search/cache từ nguồn chuẩn. | Không xem chúng là bản sao dữ liệu giao dịch. | Rebuild exercise |
| MET-PLAT-08 | Chi phí nền tảng theo đơn/workload | Chi phí cloud phân bổ / đơn hoặc workload cùng kỳ. | Cần tagging và quy tắc phân bổ thống nhất. | AWS Cost and Usage data |

### 5.1. Ma trận truy vết mục tiêu dự án (Master Goal & Metric Traceability)

Ma trận dưới đây liên kết xuyên suốt từ Mục tiêu Business Case (BC-O), Mục tiêu phân tích (OBJ), Năng lực (CAP), Dòng giá trị (VS) đến Chỉ số đo lường (MET):

| Mã mục tiêu | Tên mục tiêu | Mục tiêu Business Case | Năng lực kinh doanh (Capabilities) | Dòng giá trị (Value Stream) | Chỉ số đo lường liên quan (Metrics) |
|---|---|---|---|---|---|
| **OBJ-01** | Trải nghiệm khám phá & Minh bạch tin đăng | BC-O1 | CAP-MKT-01..05 | VS-01 | MET-BUY-01, MET-BUY-02, MET-SRC-01 |
| **OBJ-02** | Quy trình đăng bán & Quản lý tin thuận tiện | BC-O1, BC-O2 | CAP-MKT-01..03 | VS-02 | MET-SELL-01, MET-SELL-02, MET-SELL-03 |
| **OBJ-03** | Bảo vệ tính đúng đắn & Chống bán trùng | BC-O2, BC-O3 | CAP-TXN-01..05 | VS-01, VS-02 | MET-ORD-01, MET-BUY-03, MET-SELL-04 |
| **OBJ-04** | Minh bạch đơn, thanh toán & vận chuyển | BC-O1, BC-O7 | CAP-TXN-03..04, CAP-FUL-01..05 | VS-01, VS-02 | MET-BUY-04, MET-BUY-05, MET-SELL-05, MET-TRUST-01 |
| **OBJ-05** | Xử lý kiểm duyệt, an toàn & hỗ trợ | BC-O1, BC-O7 | CAP-TRU-01..04, CAP-SUP-01..04 | VS-03 | MET-TRUST-02, MET-TRUST-03, MET-NTF-01..02 |
| **OBJ-06** | Mở rộng & vận hành Microservices trên AWS | BC-O2, BC-O4, BC-O8 | CAP-PLT-01..04, CAP-PLT-07 | VS-04 | MET-PLAT-01, MET-PLAT-02, MET-PLAT-03, MET-PLAT-08 |
| **OBJ-07** | Sao lưu, khôi phục dữ liệu & Disaster Recovery | BC-O6, BC-O8 | CAP-PLT-05 | VS-04 | MET-PLAT-04, MET-PLAT-05, MET-PLAT-06, MET-PLAT-07 |
| **OBJ-08** | Hồ sơ phân tích BA có tính truy vết & quyết định | BC-O5 | CAP-GOV | VS-05 | Tỷ lệ bao phủ yêu cầu (100% requirements trace to goals) |

## 5. Nguyên tắc đo lường

- Mỗi KPI cần owner, công thức, đơn vị, bộ lọc, cửa sổ thời gian, cohort, nguồn dữ liệu và quy tắc loại trừ.
- Event có timestamp, correlation ID, service, môi trường và schema version; không ghi PII/secret trực tiếp vào analytics/log.
- Thống nhất định nghĩa event và trạng thái trước khi tính conversion.
- Báo cáo số tuyệt đối cùng tỷ lệ và kích thước mẫu; chia nhóm theo kênh/danh mục/vùng/nhà cung cấp khi có dữ liệu.
- Theo dõi guardrail: conversion cùng refund/fraud; moderation speed cùng độ chính xác; endpoint uptime cùng khả năng hoàn tất luồng.
- Áp dụng consent, retention và quyền truy cập phù hợp cho analytics.

## 6. Giá trị còn để mở

Chưa có baseline/target cho tăng trưởng, conversion, hủy, refund, SLA/SLO, latency, error budget, RPO/RTO, retention, ngân sách AWS, quy mô dữ liệu hoặc thời gian giữ listing/reservation/payment pending. Để trống tới khi có owner, bằng chứng và phê duyệt.

## 7. Quy trình phê duyệt chỉ số

| Bước | Vai trò đề xuất | Đầu ra |
|---|---|---|
| Chọn KPI liên quan | BA/Product Owner | Danh sách KPI và mục tiêu liên quan |
| Chốt định nghĩa | BA/Data/Operations | Công thức, event và ngoại lệ |
| Xác lập baseline | Data/Product Owner | Baseline, kỳ đo và giới hạn mẫu |
| Duyệt target/guardrail | Sponsor/Product Owner | Target, owner và chu kỳ rà soát |
| Duyệt NFR/recovery | Architect/Cloud/Security/Owner | SLO, RPO/RTO, retention và kết quả kiểm thử |

## 8. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Xác định mục tiêu định hướng và công thức chỉ số sơ bộ; chưa điền baseline/target. |
