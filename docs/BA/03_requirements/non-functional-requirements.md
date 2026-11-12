# Non-Functional Requirements – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Loại tài liệu | Non-Functional Requirements (NFR) |
| Phiên bản | 1.0 |
| Trạng thái | Bản nháp; **các ngưỡng định lượng chưa được phê duyệt** |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | business-requirements.md, functional-requirements.md, 02/business-objectives-and-success-metrics.md, 02/open-questions-and-decisions.md |

## 1. Nguyên tắc và cách đọc

- Chưa có mô hình tải, SLA, RPO/RTO, ngân sách hay thị trường được phê duyệt (C-03, C-04; Q-DR-01, Q-DR-06). Vì vậy tài liệu **không tự đặt số cam kết**.
- Mỗi NFR có: **yêu cầu định tính bắt buộc** (có thể kiểm tra được ngay) và **ngưỡng định lượng** ghi dạng `[TBD: Q-xx]`. Khi owner duyệt, điền ngưỡng vào cột "Ngưỡng" và đổi trạng thái.
- Cột **Cách kiểm chứng** mô tả phép đo/kịch bản; chỉ số tương ứng ở `MET-*`.
- ID: `NFR-<nhóm>-<số>`. Nhóm: PERF, SCAL, AVL, REL, CON (nhất quán), SEC, PRV (riêng tư), OBS, REC (phục hồi), DAT, MNT, USA, INT, COST, CMP.
- Ưu tiên MoSCoW; "Phụ thuộc" chỉ câu hỏi mở cần chốt.

## 2. Hiệu năng (NFR-PERF)

| ID | Yêu cầu | Ngưỡng | Cách kiểm chứng | Ưu tiên |
|---|---|---|---|---|
| NFR-PERF-01 | Tìm kiếm và xem chi tiết tin phản hồi đủ nhanh để duyệt liên tục, đo p95 theo kịch bản tải chuẩn. | p95 `[TBD: Q-DR-01]` | Kiểm thử tải; MET-PLAT-02 | M |
| NFR-PERF-02 | Thao tác checkout (xác nhận khả dụng + giữ hàng + tạo đơn) phản hồi p95 trong ngưỡng, không tính thời gian chờ nhà cung cấp thanh toán. | p95 `[TBD]` | Trace; MET-BUY-04 | M |
| NFR-PERF-03 | API ghi gây tác động nghiệp vụ phải trả về trong thời hạn timeout xác định; vượt thì trả trạng thái chờ có mã tham chiếu, không treo. | Timeout `[TBD]` | Kiểm thử lỗi; EX-015 | M |
| NFR-PERF-04 | Trang tải hình ảnh dùng kích thước/định dạng phù hợp thiết bị, tải chậm (lazy) và có thể dùng CDN cho nội dung tĩnh. | Thời gian tải trang `[TBD]` | Lighthouse/RUM | S |
| NFR-PERF-05 | Độ trễ đồng bộ Listing → Search đo được và có ngưỡng cảnh báo. | p95 `[TBD]` | MET-SRC-01 | M |
| NFR-PERF-06 | Truy vấn có chỉ mục phù hợp access pattern; không có truy vấn quét toàn bộ (full scan) ở luồng nóng. | — | Review kế hoạch truy vấn | M |

## 3. Khả năng mở rộng (NFR-SCAL)

| ID | Yêu cầu | Ngưỡng | Cách kiểm chứng | Ưu tiên |
|---|---|---|---|---|
| NFR-SCAL-01 | Service không giữ trạng thái phiên cục bộ để mở rộng ngang bằng cách thêm instance sau load balancer. | — | Review kiến trúc; test scale-out | M |
| NFR-SCAL-02 | Từng service mở rộng độc lập theo tải của chính nó (Search/Listing đọc nhiều, Order/Payment ghi quan trọng). | — | Test tải theo service | M |
| NFR-SCAL-03 | Hệ thống chịu đỉnh tải theo mô hình tải được duyệt, tăng/giảm tài nguyên tự động theo chính sách. | Tải đỉnh `[TBD: Q-DR-01, A-13]` | Test tải/soak; MET-PLAT-01..03 | M |
| NFR-SCAL-04 | Thiết kế dữ liệu nêu rõ điểm nóng (hot key: tin được xem/mua nhiều) và phương án giảm tải (cache đọc, partition/sharding nếu cần). | — | Review dữ liệu | S |
| NFR-SCAL-05 | Hàng đợi/sự kiện chịu được backlog khi consumer chậm; có giám sát độ tuổi message. | Backlog tối đa `[TBD]` | Test consumer lag | M |
| NFR-SCAL-06 | Tăng trưởng dữ liệu (ảnh, tin, đơn, log) được ước lượng và có chính sách lifecycle/lưu trữ. | Dung lượng `[TBD]` | Capacity plan | S |

## 4. Tính sẵn sàng (NFR-AVL)

| ID | Yêu cầu | Ngưỡng | Cách kiểm chứng | Ưu tiên |
|---|---|---|---|---|
| NFR-AVL-01 | Thành phần quan trọng triển khai tối thiểu hai Availability Zone; không có điểm lỗi đơn ở luồng mua–bán trọng yếu. | Số AZ `[TBD: Q-DR-06]` | Review topology; chaos/failover test | M |
| NFR-AVL-02 | Load balancer kiểm tra sức khỏe và loại bỏ instance lỗi tự động. | — | Test health check | M |
| NFR-AVL-03 | Khi Search, Notification, Shipping hoặc một đối tác bên ngoài lỗi, luồng đặt mua cốt lõi bị suy giảm có kiểm soát thay vì dừng toàn bộ (degraded mode). | — | Kịch bản EX-016, EX-010 | M |
| NFR-AVL-04 | Gọi service/đối tác có timeout, retry giới hạn với backoff, circuit breaker và bulkhead để tránh lỗi lan truyền. | Tham số `[TBD]` | Test lỗi chèn; MET-PLAT-03 | M |
| NFR-AVL-05 | Mục tiêu sẵn sàng của luồng nghiệp vụ trọng yếu (đăng tin, tìm kiếm, checkout, thanh toán) được đo theo luồng, không chỉ theo endpoint. | SLO `[TBD: Q-DR-01]` | MET-PLAT-01 | M |
| NFR-AVL-06 | Bảo trì và triển khai không gây gián đoạn (rolling/blue-green) cho luồng trọng yếu. | — | Quy trình release | S |
| NFR-AVL-07 | Multi-AZ/replication hỗ trợ sẵn sàng nhưng **không** được tính là bản sao lưu (xem NFR-REC). | — | Review thiết kế | M |

## 5. Độ tin cậy và nhất quán (NFR-REL, NFR-CON)

| ID | Yêu cầu | Ngưỡng | Cách kiểm chứng | Ưu tiên |
|---|---|---|---|---|
| NFR-CON-01 | Với một tin đơn chiếc, không có hai reservation/đơn hợp lệ đồng thời kể cả khi nhiều yêu cầu đến cùng lúc hoặc client gửi lặp. | Tỷ lệ bán trùng = 0 | Test đồng thời EX-002; MET-ORD-01 | M |
| NFR-CON-02 | Reservation dùng cập nhật có điều kiện nguyên tử trên nguồn chuẩn (Listing), không dựa trên cache/search. | — | Review/test | M |
| NFR-CON-03 | Nhất quán giữa Order, Payment, Listing, Shipping đạt qua sự kiện + bù trừ + đối soát; trạng thái không nhất quán tạm thời phải hội tụ hoặc bị phát hiện. | Cửa sổ hội tụ `[TBD]` | Kịch bản EX-008, EX-019 | M |
| NFR-CON-04 | Mọi sự kiện quan trọng phát ra từ dữ liệu đã commit (outbox); không mất sự kiện khi lỗi giữa chừng. | — | Test crash giữa commit và publish | M |
| NFR-CON-05 | Consumer sự kiện idempotent; xử lý trùng/sai thứ tự không tạo tác động lặp. | — | Test replay | M |
| NFR-CON-06 | Dữ liệu dẫn xuất (Search, cache, báo cáo) chấp nhận nhất quán cuối cùng và có khả năng dựng lại. | — | MET-PLAT-07 | M |
| NFR-REL-01 | Callback/webhook nhận được xử lý ít nhất một lần và hiệu ứng đúng một lần (nhờ khử trùng). | — | Test trùng callback; EX-006 | M |
| NFR-REL-02 | Không báo thành công cho người dùng khi chưa ghi bền vững; thất bại tạm thời trả trạng thái chờ + mã tham chiếu. | — | Test EX-015 | M |
| NFR-REL-03 | Hàng đợi lỗi (DLQ) có giám sát, cảnh báo và quy trình xử lý lại. | Ngưỡng cảnh báo `[TBD]` | Test redrive | M |
| NFR-REL-04 | Giao dịch trạng thái chưa xác định (thanh toán/shipment) được đối soát tự động theo lịch và có báo cáo tồn đọng. | Chu kỳ `[TBD]` | MET-PLAT-03 | M |

## 6. Bảo mật (NFR-SEC)

| ID | Yêu cầu | Cách kiểm chứng | Ưu tiên |
|---|---|---|---|
| NFR-SEC-01 | Mọi kết nối bên ngoài và liên service dùng mã hóa khi truyền (TLS). | Quét cấu hình | M |
| NFR-SEC-02 | Dữ liệu lưu (cơ sở dữ liệu, object storage, backup) được mã hóa; khóa quản lý tập trung, có xoay vòng. | Review IaC | M |
| NFR-SEC-03 | Xác thực mạnh cho người dùng; phiên có thời hạn và thu hồi được; hỗ trợ MFA cho vai trò đặc quyền (xác nhận phạm vi). | Pen test | M |
| NFR-SEC-04 | Phân quyền deny-by-default, kiểm tra server-side, RBAC kết hợp ownership; không tin ẩn nút giao diện. | Test quyền; ma trận 01 | M |
| NFR-SEC-05 | Bí mật (API key, credential, khóa) lưu trong dịch vụ quản lý bí mật; không có trong mã nguồn, log, tài liệu. | Quét bí mật trong repo/log | M |
| NFR-SEC-06 | Service identity có phạm vi nhỏ nhất; không chia sẻ login/credential cơ sở dữ liệu giữa service. | Review IAM/DB | M |
| NFR-SEC-07 | Bảo vệ lớp biên bằng AWS WAF v2: lọc request độc hại (OWASP Top 10, SQLi, XSS, Bad Inputs), Geo-blocking (VN only), giới hạn tốc độ (Rate-based rules 50 req/5m cho checkout), chống bot và brute-force đăng nhập/OTP. | Test rate limit, WAF scan | M |
| NFR-SEC-08 | Chống các lỗ hổng phổ biến (injection, XSS, CSRF, IDOR, SSRF, tải tệp độc hại) theo danh mục OWASP. | SAST/DAST, pen test | M |
| NFR-SEC-09 | Webhook bên ngoài được xác minh nguồn/chữ ký, chống phát lại (replay), IP whitelisting qua WAF. | Test replay, IP filter | M |
| NFR-SEC-10 | Mạng: service và cơ sở dữ liệu ở mạng riêng; chỉ lớp biên nhận lưu lượng công khai qua Application Load Balancer. | Review topology | M |
| NFR-SEC-11 | Audit log bất biến cho hành động nhạy cảm (đổi quyền, khóa tài khoản, hoàn tiền, xem PII, restore). | Test audit | M |
| NFR-SEC-12 | Quyền vận hành hạ tầng/database tách khỏi quyền nghiệp vụ; truy cập dữ liệu production cần phê duyệt (break-glass) và ghi vết. | Review quy trình | M |
| NFR-SEC-13 | Phụ thuộc phần mềm được quét lỗ hổng và cập nhật theo chu kỳ. | Quét SCA | S |
| NFR-SEC-14 | Ảnh/tệp tải lên được kiểm tra loại thực, quét mã độc, lưu bucket riêng tư, truy cập bằng URL có thời hạn. | Test upload độc hại | M |
| NFR-SEC-15 | Tường lửa mạng (AWS Network Firewall): Kiểm tra gói tin trạng thái (Stateful DPI Suricata), phát hiện xâm nhập (IDS/IPS) và lọc tên miền Egress (Egress Domain Whitelist) chỉ cho phép kết nối tới cổng thanh toán và 3PL hợp lệ. | Test egress drop, IDS alert | M |

## 7. Quyền riêng tư và tuân thủ (NFR-PRV, NFR-CMP)

| ID | Yêu cầu | Phụ thuộc | Ưu tiên |
|---|---|---|---|
| NFR-PRV-01 | PII chỉ thu thập khi cần cho mục đích giao dịch; thông báo rõ mục đích và sự đồng ý khi áp dụng. | Q-BIZ-02 (pháp lý thị trường) | M |
| NFR-PRV-02 | PII bị che (mask) theo vai trò; log, analytics và thông báo không chứa PII/secret trực tiếp. | — | M |
| NFR-PRV-03 | Địa chỉ/điện thoại chỉ chia sẻ cho người bán/hãng vận chuyển khi cần thực hiện đơn. | BR-ACT-005 | M |
| NFR-PRV-04 | Chính sách lưu giữ và xóa/ẩn danh dữ liệu cá nhân, có xét bằng chứng đơn/vụ việc. | D-07; retention `[TBD]` | M |
| NFR-PRV-05 | Hỗ trợ yêu cầu truy cập/xóa dữ liệu của chủ thể khi luật địa phương yêu cầu. | EXT-06 | S |
| NFR-CMP-01 | Không lưu dữ liệu thẻ thanh toán; phạm vi tuân thủ thanh toán xác định cùng nhà cung cấp. | Q-OPS-05 | M |
| NFR-CMP-02 | Yêu cầu tuân thủ theo thị trường (thuế, hóa đơn, người tiêu dùng, hàng cấm) ghi nhận ở dạng cần xác nhận, chưa cam kết. | Q-BIZ-02, Q-BIZ-04 | S |

## 8. Quan sát và vận hành (NFR-OBS)

| ID | Yêu cầu | Cách kiểm chứng | Ưu tiên |
|---|---|---|---|
| NFR-OBS-01 | Log có cấu trúc, mang correlation ID, tên service, môi trường, phiên bản; mức độ log điều chỉnh được. | Review log | M |
| NFR-OBS-02 | Metric chuẩn cho mỗi service: lưu lượng, lỗi, độ trễ (p50/p95/p99), mức dùng tài nguyên, độ sâu/độ tuổi hàng đợi. | Dashboard | M |
| NFR-OBS-03 | Truy vết phân tán cho luồng listing→order→payment→shipping. | Trace mẫu | M |
| NFR-OBS-04 | Cảnh báo cho: tỷ lệ lỗi, độ trễ, backlog, DLQ, backup lỗi/trễ, tỷ lệ thanh toán/shipment chưa xác định. | Test cảnh báo | M |
| NFR-OBS-05 | Dashboard nghiệp vụ (checkout funnel, xung đột giữ hàng, lag Search) ngoài dashboard kỹ thuật. | MET-* | S |
| NFR-OBS-06 | Có runbook cho từng cảnh báo trọng yếu; tách môi trường dev/test/prod. | Review runbook | M |
| NFR-OBS-07 | Chi phí hạ tầng gắn thẻ (tag) theo service/môi trường để phân bổ. | MET-PLAT-08 | S |

## 9. Sao lưu và phục hồi (NFR-REC)

| ID | Yêu cầu | Ngưỡng | Cách kiểm chứng | Ưu tiên |
|---|---|---|---|---|
| NFR-REC-01 | Mỗi datastore (SQL Server, MongoDB, S3, hàng đợi, cấu hình) có chủ sở hữu, phân loại dữ liệu, chính sách backup, mã hóa, quyền restore. | — | Review ma trận backup | M |
| NFR-REC-02 | RPO và RTO được định nghĩa theo từng nhóm dữ liệu/luồng và do owner phê duyệt trước khi thiết kế cấu hình. | RPO/RTO `[TBD: Q-DR-01]` | Phê duyệt | M |
| NFR-REC-03 | Hỗ trợ khôi phục theo thời điểm cho cơ sở dữ liệu quan hệ và document; hỗ trợ phục hồi đối tượng đã ghi đè/xóa ở S3. | Cửa sổ PITR `[TBD]` | Drill; MET-PLAT-05 | M |
| NFR-REC-04 | Backup độc lập với HA: có bản sao bảo vệ trước xóa nhầm, lỗi logic, xâm nhập tài khoản; đánh giá bản sao khác tài khoản/Region và tính bất biến theo yêu cầu/chi phí. | Mức bảo vệ `[TBD: Q-DR-02]` | Review + drill | M |
| NFR-REC-05 | Giám sát backup (thành công/thất bại, tuổi bản gần nhất) và cảnh báo khi vượt ngưỡng. | Ngưỡng `[TBD]` | MET-PLAT-04 | M |
| NFR-REC-06 | Runbook khôi phục theo thứ tự phụ thuộc: tài khoản/quyền/khóa/mạng → cơ sở dữ liệu → ứng dụng → object → hàng đợi → search/cache → chuyển traffic. | — | Review runbook | M |
| NFR-REC-07 | Diễn tập khôi phục định kỳ ở môi trường cách ly, xác minh dữ liệu, quyền và các journey (đăng tin, giữ hàng, đơn, thanh toán, giao hàng); lưu kết quả. | Tần suất `[TBD: Q-DR-04]` | Biên bản drill; MET-PLAT-05/06 | M |
| NFR-REC-08 | Khóa mã hóa, bí mật, IaC cần cho restore vẫn truy cập được khi mất tài nguyên nguồn. | — | Drill mất khóa; EX-020 | M |
| NFR-REC-09 | Sau khôi phục lệch thời điểm giữa các service, có quy trình đối soát và phát lại sự kiện trước khi mở toàn bộ traffic. | — | Drill EX-019 | M |
| NFR-REC-10 | Search index và cache dựng lại từ nguồn chuẩn trong thời gian mục tiêu. | Thời gian dựng `[TBD]` | MET-PLAT-07 | M |
| NFR-REC-11 | Thao tác restore/đổi chính sách backup cần phê duyệt và audit; tách quyền khỏi quyền ứng dụng. | — | Review | M |
| NFR-REC-12 | Chiến lược khôi phục thảm họa theo Region chỉ áp dụng khi RPO/RTO, thị trường và chi phí biện minh. | Q-DR-06 | Quyết định kiến trúc | C |

## 10. Dữ liệu (NFR-DAT)

| ID | Yêu cầu | Ưu tiên |
|---|---|---|
| NFR-DAT-01 | Mỗi service sở hữu dữ liệu riêng; truy cập chéo chỉ qua API/sự kiện. | M |
| NFR-DAT-02 | Schema và sự kiện có phiên bản, tương thích ngược khi triển khai; migration có kế hoạch rollback. | M |
| NFR-DAT-03 | Ràng buộc toàn vẹn (khóa, duy nhất, tham chiếu) được định nghĩa ở nguồn chuẩn; khóa idempotency có thời hạn xác định. | M |
| NFR-DAT-04 | Dữ liệu tiền tệ dùng kiểu số chính xác và quy tắc làm tròn thống nhất (BR-PAY-005); thời gian lưu UTC. | M |
| NFR-DAT-05 | Đơn lưu snapshot không phụ thuộc join runtime sang store khác. | M |
| NFR-DAT-06 | Phân tách dữ liệu theo môi trường; dữ liệu thật không dùng ở dev/test (hoặc phải ẩn danh). | M |
| NFR-DAT-07 | Vòng đời dữ liệu: retention, lưu trữ lạnh, xóa/ẩn danh theo chính sách. | S |
| NFR-DAT-08 | Dùng chung hạ tầng database trong demo chỉ khi cô lập schema/credential/quyền; ghi rõ phạm vi restore chung (A-17). | S |

## 11. Bảo trì, kiểm thử, triển khai (NFR-MNT)

| ID | Yêu cầu | Ưu tiên |
|---|---|---|
| NFR-MNT-01 | Hạ tầng quản lý bằng mã (IaC) có kiểm soát phiên bản; môi trường tái tạo được. | S |
| NFR-MNT-02 | CI/CD tự động hóa kiểm tra, đóng gói, triển khai và rollback. | S |
| NFR-MNT-03 | Hợp đồng API/sự kiện có tài liệu và kiểm thử hợp đồng (contract test). | M |
| NFR-MNT-04 | Độ bao phủ kiểm thử đạt ngưỡng cho luồng trọng yếu (checkout, thanh toán, hoàn tiền, giao nhận). | Ngưỡng `[TBD]` | M |
| NFR-MNT-05 | Cấu hình tham số hóa (thời hạn giữ hàng, khiếu nại, giờ yên tĩnh, retry) thay đổi không cần phát hành lại. | M |
| NFR-MNT-06 | Mỗi service có tài liệu trách nhiệm, chủ sở hữu và runbook. | S |

## 12. Khả dụng giao diện và tương thích (NFR-USA, NFR-INT)

| ID | Yêu cầu | Phụ thuộc | Ưu tiên |
|---|---|---|---|
| NFR-USA-01 | Giao diện Marketplace và Seller Center hiển thị tốt trên di động và máy tính (responsive); phạm vi app native cần xác nhận. | Q-BIZ-15 | M |
| NFR-USA-02 | Thông báo lỗi rõ ràng, nêu nguyên nhân và bước tiếp theo; không lộ chi tiết kỹ thuật/nhạy cảm. | — | M |
| NFR-USA-03 | Hỗ trợ tiêu chuẩn tiếp cận cơ bản (độ tương phản, điều hướng bàn phím, nhãn cho ảnh). | Mức WCAG `[TBD]` | S |
| NFR-USA-04 | Ngôn ngữ, tiền tệ, múi giờ cấu hình được; ngôn ngữ/tiền tệ khởi điểm theo quyết định thị trường. | Q-BIZ-02 | M |
| NFR-USA-05 | Form đăng tin tự lưu nháp, chịu lỗi mạng và không mất dữ liệu đã nhập. | — | S |
| NFR-INT-01 | Tích hợp bên ngoài qua hợp đồng trừu tượng, thay nhà cung cấp không đổi nghiệp vụ lõi. | C-06 | M |
| NFR-INT-02 | Mọi tích hợp có timeout, giới hạn retry, idempotency, xác thực và xử lý giới hạn tốc độ của đối tác. | D-06 | M |
| NFR-INT-03 | Có chế độ giả lập/sandbox cho thanh toán và vận chuyển để kiểm thử mà không dùng credential thật. | Q-OPS-04 | S |

## 13. Chi phí (NFR-COST)

| ID | Yêu cầu | Ưu tiên |
|---|---|---|
| NFR-COST-01 | Thiết kế nêu ước tính chi phí hạ tầng theo mô hình tải; so sánh phương án (monolith mô-đun, microservices) và đánh đổi. | S |
| NFR-COST-02 | Môi trường thử nghiệm có ngân sách, cảnh báo chi phí, giới hạn tài nguyên và quy trình tắt. | M |
| NFR-COST-03 | Số service/store/instance đề xuất phải có căn cứ; tránh tách quá mức (C-09, C-11). | S |
| NFR-COST-04 | Chi phí backup/bản sao/log có chính sách retention và lifecycle. | S |

## 14. Giá trị chờ phê duyệt (tổng hợp)

| Hạng mục | Câu hỏi | Chủ sở hữu quyết định |
|---|---|---|
| Mô hình tải và đỉnh tải | A-13; Q-DR-01 | Sponsor, Product Owner, Architect |
| SLO/SLA luồng trọng yếu | Q-DR-01 | Sponsor, Product Owner |
| RPO/RTO theo nhóm dữ liệu | Q-DR-01 | Sponsor, Product Owner, DBA |
| Retention, bản sao khác account/Region, bất biến | Q-DR-02 | Security, Cloud, Legal |
| Tần suất diễn tập restore | Q-DR-04 | DBA/SRE, Product Owner |
| Region, số AZ, môi trường, ngân sách | Q-DR-06 | Sponsor, Cloud, Security |
| Ngôn ngữ, tiền tệ, quy định thị trường | Q-BIZ-02 | Sponsor |
| MFA, session, rotation, audit retention | BA 01 §8 | Security, Product Owner |

## 15. Ma trận NFR ↔ mục tiêu

| Mục tiêu | NFR chính |
|---|---|
| OBJ-03 | NFR-CON-01..05, NFR-REL-01..04 |
| OBJ-04 | NFR-PERF-02, NFR-OBS-03, NFR-REL-02 |
| OBJ-05 | NFR-SEC-04, NFR-SEC-11, NFR-PRV-02 |
| OBJ-06 | NFR-PERF, NFR-SCAL, NFR-AVL, NFR-OBS, NFR-COST |
| OBJ-07 | NFR-REC-01..12, NFR-AVL-07 |
| OBJ-08 | NFR-MNT-03, NFR-MNT-05 |

## 16. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo NFR theo 13 nhóm; ngưỡng định lượng đánh dấu chờ phê duyệt; thêm danh mục giá trị chờ quyết định. |

