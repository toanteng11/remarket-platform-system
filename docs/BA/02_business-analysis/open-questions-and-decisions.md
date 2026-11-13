# Open Questions and Decisions – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Danh sách khởi tạo; câu hỏi đều đang mở trừ khi có decision record |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | business-rules.md, project-brief.md, assumptions-constraints-dependencies.md, non-functional-requirements.md, database-and-backup design thuộc BA 05/06 |

## 1. Mục đích

Tập trung các câu hỏi ảnh hưởng phạm vi, policy, mô hình dữ liệu, kiến trúc, vận hành và phục hồi. Giúp nhóm không biến giả định thành quyết định bằng cách viết chúng vào quy trình.

Không ghi tên cá nhân hoặc quyết định thay cho Sponsor/Product Owner/Business Owner. Trạng thái chỉ đổi khi có owner, phương án được chọn, lý do, bằng chứng, ngày hiệu lực và artefact cần cập nhật.

## 2. Quy ước trạng thái và ưu tiên

- **Mở:** cần khảo sát hoặc quyết định.
- **Đang phân tích:** có người phụ trách và đang thu thập thông tin.
- **Đã quyết định:** có decision record được phê duyệt.
- **Hoãn:** quyết định được dời, có ngày rà soát/lý do.
- **P0:** chặn scope/MVP hoặc kiến trúc nền.
- **P1:** cần trước khi đặc tả/đánh giá thiết kế hoàn tất.
- **P2:** có thể để giai đoạn sau nếu đã ghi rõ giả định.

## 3. Câu hỏi nghiệp vụ và sản phẩm

| ID | Câu hỏi cần chốt | Các phương án/điểm cần khảo sát | Owner đề xuất | Ưu tiên | Ảnh hưởng |
|---|---|---|---|---|---|
| Q-BIZ-01 | ReMarket phục vụ C2C, seller chuyên nghiệp hay cả hai? | Chỉ cá nhân; cá nhân + cửa hàng; doanh nghiệp có nhiều staff. | Sponsor/Product Owner | P0 | Profile, role, onboarding, bulk workflow |
| Q-BIZ-02 | Thị trường, vùng giao hàng, ngôn ngữ và tiền tệ ban đầu là gì? | Một quốc gia/địa bàn trước hay đa vùng ngay. | Sponsor | P0 | Catalog, address, tax, carrier, currency |
| Q-BIZ-03 | Mô hình hàng hóa có mặc định một listing = một món hàng duy nhất không? | Đồ đơn chiếc; listing có số lượng; biến thể/SKU. | Product Owner | P0 | Reservation, inventory, checkout |
| Q-BIZ-04 | Nhóm ngành hàng và mặt hàng cấm/hạn chế nào thuộc scope? | Taxonomy ban đầu, yêu cầu disclosure, danh sách policy. | Product Owner / Legal Lead (STK-16) | P0 | Form, moderation, compliance |
| Q-BIZ-05 | Seller có cần xác minh danh tính hoặc quyền sở hữu hàng không? | Không; xác minh mức cơ bản; xác minh tăng cường theo category/value. | Trust & Support / Legal Lead (STK-16) | P0 | Onboarding, privacy, risk |
| Q-BIZ-06 | Có kiểm duyệt trước khi đăng hay hậu kiểm theo risk? | Pre-moderation; post-moderation; hybrid. | Business Owner/Trust | P0 | Publish flow, operations capacity |
| Q-BIZ-07 | Marketplace có thu phí/hoa hồng, quảng cáo hoặc subscription không? | Không trong scope; phí theo đơn; dịch vụ bổ sung. | Sponsor / Finance Lead (STK-15) | P1 | Pricing, payment, reporting |
| Q-BIZ-08 | Ai chịu trách nhiệm về độ chính xác mô tả và hàng không đúng kỳ vọng? | Seller, nền tảng hay trách nhiệm chia sẻ theo loại case. | Product/Legal | P0 | Terms, evidence, dispute |
| Q-BIZ-09 | Buyer có thể thương lượng/chat trước khi mua không? | Không; nhắn tin; offer/counter-offer. | Product Owner | P1 | Messaging scope, abuse controls |
| Q-BIZ-10 | Một order có thể chứa listing từ nhiều seller không? | Chỉ một seller/order; multi-seller cart với split fulfillment/payment. | Product Owner | P0 | Order aggregate, shipment, fees, refunds |
| Q-BIZ-11 | Quy tắc hủy, đổi trả, refund, giao thất bại và tranh chấp là gì? | Theo trạng thái, loại hàng, lỗi seller/provider/buyer; thời hạn và bằng chứng. | Product Owner / Finance Lead (STK-15) / Legal (STK-16) | P0 | Order/payment/support state machine |
| Q-BIZ-12 | Khi nào order được xem là hoàn tất? | Carrier delivered; buyer confirmation; timeout; manual review. | Product/Operations | P0 | Settlement, review eligibility, dispute window |
| Q-BIZ-13 | Reviews có thuộc MVP không và ai đủ điều kiện đánh giá? | Sau delivered/completed; một review/order; có sửa/phản hồi. | Product Owner | P2 | Reviews/trust scope |
| Q-BIZ-14 | Có cần seller professional center, nhiều nhân viên hoặc bulk import không? | Chỉ cá nhân ở đợt đầu; enterprise seller sau. | Sponsor/Product Owner | P1 | Identity/permission/UI/integration |
| Q-BIZ-15 | Kênh nào là phạm vi đầu tiên? | Marketplace web, mobile app/web responsive, Seller Center, Admin. | Sponsor/Product Owner | P0 | UX, API/BFF, delivery scope |
| Q-BIZ-16 | Buyer/seller cần thông báo qua kênh nào? | In-app, email, SMS, push; opt-in, quiet hours. | Product Owner | P1 | Notification provider, consent/cost |

## 4. Câu hỏi quy trình và vận hành nghiệp vụ

| ID | Câu hỏi cần chốt | Dữ liệu/bằng chứng cần | Owner đề xuất | Ưu tiên | Tài liệu ảnh hưởng |
|---|---|---|---|---|---|
| Q-OPS-01 | Ai chịu trách nhiệm xử lý listing, account, payment và delivery case? | RACI và escalation path. | Business Owner | P0 | 01 roles, business rules |
| Q-OPS-02 | Moderation/support cần severity, SLA, appeal và approval nào? | Quy trình mẫu, volume dự kiến, giờ hỗ trợ. | Trust/Support Owner | P1 | To-be, case model, NFR |
| Q-OPS-03 | Có nhân viên logistics của ReMarket hay chỉ kết nối carrier? | Operating model, các bước seller phải tự làm. | Sponsor/Operations | P0 | Shipping scope, portal/interface |
| Q-OPS-04 | Carrier nào cần xem xét trước và API/sandbox nào sẵn có? | Hợp đồng, vùng phủ, callback, quote, cancel, tracking. | Partner Integration Lead (STK-17) | P1 | Shipping adapter, test strategy |
| Q-OPS-05 | Payment provider/phương thức nào có thể dùng? Có COD không? | API capability, refund, reconciliation, settlement, fees. | Finance Lead (STK-15) / Product Owner | P0 | Payment flow, security/compliance |
| Q-OPS-06 | Quy trình đối soát giao dịch và settlement ai sở hữu? | Bộ phận/role, chu kỳ, file/report, xử lý lệch. | Finance Lead (STK-15) / Ops | P1 | Payment, audit, reporting |
| Q-OPS-07 | Case được đóng khi nào và reopen/appeal trong bao lâu? | Loại case, kết quả có thể, bằng chứng. | Support/Trust | P1 | Case lifecycle/retention |

## 5. Câu hỏi dữ liệu, AWS và phục hồi

Các câu hỏi sau không thay thế thiết kế chi tiết tại BA 05/06. Chúng được đặt ở đây vì ảnh hưởng trực tiếp tới mục tiêu nghiệp vụ, chi phí và khả năng khôi phục.

| ID | Câu hỏi cần chốt | Phương án/thông tin cần đánh giá | Owner đề xuất | Ưu tiên | Tác động |
|---|---|---|---|---|---|
| Q-DATA-01 | SQL Server sẽ dùng workload/service nào và phiên bản/edition nào? | Relational transaction; feature/edition/license; managed RDS for SQL Server hay self-managed. | DBA/Architect | P0 | Cost, HA, backup, performance |
| Q-DATA-02 | MongoDB thuộc service nào, hosting theo cách nào? | MongoDB Atlas trên AWS, tự quản lý MongoDB hay phương án tương thích; kiểm tra feature/backup/restore. | DBA/Architect | P0 | Data model, compatibility, operations |
| Q-DATA-03 | Service nào sở hữu từng entity và cần transaction mạnh nào? | Listing reservation, order, payment, profile, shipment; tránh transaction xuyên datastore. | Domain Architect/BA | P0 | Boundaries, consistency, recovery |
| Q-DATA-04 | Có dùng chung database cluster trong demo không? | Tách logical database/schema/collection/credential hay instance riêng; restore granularity. | Architect/DBA | P1 | Isolation, cost, blast radius |
| Q-DATA-05 | Cache dùng cho dữ liệu nào, TTL/invalidation ra sao? | Read cache, session, rate limit; không giữ source-of-truth transaction. | Architect | P1 | Staleness, cost, outage mode |
| Q-DATA-06 | Load balancing/API entry dùng mô hình nào? | ALB, API Gateway hoặc kết hợp; routing, auth, rate limit, BFF needs. | Architect/Cloud | P1 | Security, latency, cost |
| Q-DATA-07 | S3 chứa loại object nào và retention/versioning ra sao? | Listing media, evidence, export; lifecycle, delete, malware scanning, access. | Data/Security | P1 | Privacy, cost, recovery |
| Q-DATA-08 | Search/index dùng lựa chọn nào và rebuild được trong bao lâu? | OpenSearch hoặc phương án khác; nguồn event/rebuild procedure. | Architect/Search Owner | P1 | Discovery availability, recovery |
| Q-DATA-09 | Queue/event cần giữ lịch sử bao lâu và replay được tới đâu? | Outbox, SQS/SNS/EventBridge candidates, DLQ/archive, deduplication. | Architect/Ops | P1 | Cross-service recovery |
| Q-DR-01 | Business owner chấp nhận RPO/RTO nào cho từng data class và journey? | Tách listing, order/payment, profile, shipment, media, search/cache. | Sponsor/Product/DBA | P0 | Backup topology, cost |
| Q-DR-02 | Backup retention, immutable/protected copy, cross-account/region cần mức nào? | Threat model, legal retention, recovery region, budget. | Security Lead / Cloud Lead / Legal (STK-16) | P0 | Ransomware/deletion/DR |
| Q-DR-03 | Ai có quyền khởi chạy/duyệt restore và break-glass? | Separation of duties, account recovery, KMS key lifecycle. | Security/Cloud | P0 | Restore feasibility, insider risk |
| Q-DR-04 | Backup/restore drill bao lâu một lần và đạt checklist nào? | Drill theo database/object/luồng; record evidence and owner. | DBA / SRE Lead (STK-18) / Product Owner | P1 | MET-PLAT-05/06, assurance |
| Q-DR-05 | Restore cần consistency giữa database, S3, events và provider bên ngoài thế nào? | Snapshot point, replay, reconciliation, pending payment/shipment handling. | Solution Architect / DBA / SRE Lead (STK-18) | P0 | Data correctness, incident runbook |
| Q-DR-06 | AWS region/environment/network boundary nào được dùng? | Region, Multi-AZ, dev/test/prod separation, account strategy, cost ceiling. | Sponsor/Cloud/Security | P0 | Availability, compliance, cost |

## 6. Decision log

### Quyết định định hướng đã được người yêu cầu nêu

| ID | Nội dung | Trạng thái | Phạm vi chính xác |
|---|---|---|---|
| DEC-DIR-01 | Ưu tiên xem xét SQL Server cho relational database. | Định hướng được xác nhận | Chưa chốt service mapping, version, hosting, edition hoặc HA/backup configuration. |
| DEC-DIR-02 | Ưu tiên xem xét MongoDB cho NoSQL/document database. | Định hướng được xác nhận | Chưa chốt hosting, feature compatibility, service ownership hoặc topology. |
| DEC-DIR-03 | Có cache, load balancer và Amazon S3 trong kiến trúc cần phân tích. | Định hướng được xác nhận | Chưa chốt loại dịch vụ, workload, topology hay cấu hình. |
| DEC-DIR-04 | Đánh giá các dịch vụ AWS khác phù hợp với microservices/cloud system. | Định hướng được xác nhận | Chưa chốt service catalog, region, account hoặc ngân sách. |
| DEC-DIR-05 | Tích hợp đối tác vận chuyển qua Adapter pattern trong Shipping Service (không tạo microservice riêng cho từng hãng). | Định hướng kiến trúc baseline | Đã thống nhất ở mức nguyên tắc thiết kế; đối tác cụ thể và sandbox chờ xác nhận. |
| DEC-DIR-06 | Đa kênh giao diện (Marketplace, Seller Center, Back-office) dùng chung API theo phân quyền RBAC/ABAC; Service sở hữu dữ liệu nguồn chuẩn (Source of Truth), Search Index và Cache là dữ liệu dẫn xuất (Derived). | Định hướng kiến trúc baseline | Đã thống nhất ở mức nguyên tắc thiết kế nhằm tránh dữ liệu đọc cũ ảnh hưởng giao dịch. |

Không mở rộng các định hướng trên thành lựa chọn cụ thể ngoài phạm vi user đã nói.

### Decision records nghiệp vụ cốt lõi đã phê duyệt (Baseline R1/R2/R3)

Các quyết định dưới đây khép lại các câu hỏi P0/P1 về mô hình kinh doanh, dòng tiền, khiếu nại và vận hành nhằm đồng bộ hóa toàn bộ bộ tài liệu `02` $\rightarrow$ `09`.

| Mã quyết định | Chủ đề & Phạm vi giải quyết | Nội dung chốt (Baseline) | Khép câu hỏi | Tham chiếu Rule / Tài liệu |
|---|---|---|---|---|
| **DEC-BIZ-01** | Mô hình C2C thuần & Đơn hàng 1-1 | Vận hành C2C hàng cũ thanh lý tại Việt Nam (`VNĐ`). Mỗi tin đăng đại diện cho **01 vật phẩm duy nhất (`Inventory = 1`)**; 01 đơn hàng chỉ gồm 01 sản phẩm của 01 Seller (`Single-item, Single-seller`). | `Q-BIZ-01..03`, `Q-BIZ-10` | `BR-ORD-001`, `INV-01` |
| **DEC-BIZ-02** | Giữ hàng 15 phút & Chống găm hàng | Hold Window mặc định là **15 phút**; mỗi Buyer chỉ được giữ tối đa **2 đơn `AWAITING_PAYMENT` đồng thời**. Sau khi hủy hoặc hết hạn thanh toán, áp dụng **cooldown 30 phút** và tối đa **2 lần hold/ngày** cho cùng `(BuyerId, ListingId)`. | `Q-BIZ-05..06`, `Q-BIZ-11..12` | `BR-ORD-013`, `BR-ORD-014` |
| **DEC-BIZ-03** | Ký quỹ trả trước (Hoãn COD sang R3+) | R1/R2 áp dụng **100% Prepaid Escrow Only** (Ví điện tử/Chuyển khoản QR/Thẻ) để bảo toàn bất biến `INV-02/INV-04`. Hỗ trợ COD (`FR-PAY-15`) được **hoãn sang R3+**. | `Q-OPS-05`, `Q-BIZ-12` | `BR-PAY-015`, `INV-02`, `INV-04` |
| **DEC-BIZ-04** | Cửa sổ khiếu nại `72h` & Chu kỳ `T+3` | Cửa sổ Buyer mở khiếu nại sau `DELIVERED` là **72 giờ** (thay cho đề xuất `48 giờ`). Quy ước **`T+3`** là `72 giờ` bảo vệ người mua + lô giải ngân ngân hàng kế tiếp (`T+1` ngày làm việc). Riêng hạn chót Seller đóng gói giao 3PL giữ nguyên là **48 giờ** từ lúc `PAID_ESCROW`. | `Q-BIZ-13..15` | `BR-DSP-001`, `BR-PAY-012`, `BR-ORD-015` |
| **DEC-BIZ-05** | Phí sàn `5%` & Khấu trừ cước cân nặng | Phí sàn cố định **`5%` trên Giá trị sản phẩm** (không tính trên phí ship 3PL). Hoàn tiền 100% $\rightarrow$ phí sàn = `0 VNĐ`; Split Escrow $\rightarrow$ thu `5%` trên phần tiền Seller thực nhận. Phụ trội cân nặng/kích thước do Seller khai thiếu (`OverweightSurcharge`) được trừ thẳng vào `NetSellerAmount`. | `Q-BIZ-07..09` | `BR-PAY-013`, `BR-PAY-014` |
| **DEC-BIZ-06** | Reverse Logistics & Phân bổ cước RTS | Phán quyết `VERDICT_RETURN_AND_REFUND`: Buyer phải gửi trả hàng qua 3PL trong **72 giờ** (quá hạn $\rightarrow$ đóng khiếu nại & giải ngân Seller); sau khi có `POD` hoàn hàng tới Seller, Seller có **48 giờ** xác nhận hoặc phản khiếu nại kèm video khui hàng (quá `48 giờ` im lặng $\rightarrow$ tự động hoàn tiền 100% Buyer). Lỗi Seller $\rightarrow$ Seller chịu cước 2 chiều; giao thất bại 3 lần do Buyer không nhận (`RTS`) $\rightarrow$ hoàn tiền hàng cho Buyer sau khi trừ cước giao đi & cước chuyển hoàn 3PL. | `Q-OPS-04`, `Q-OPS-06` | `BR-DSP-005`, `BR-DSP-006`, `BR-SHP-005` |
| **DEC-BIZ-07** | Giới hạn 3PL, Bảo hiểm & Mất/Hỏng hàng | R1/R2 chỉ hỗ trợ kiện hàng tiêu chuẩn qua 3PL (`<= 30kg`, kích thước chuẩn 3PL; loại trừ xe cộ, BĐS, nội thất cồng kềnh). Đơn hàng **`>= 3.000.000 VNĐ`** bắt buộc mua **Bảo hiểm khai giá 3PL**. Nếu 3PL xác nhận `LOST`/`DAMAGED`, hoàn tiền ngay cho Buyer từ Escrow và nền tảng tự đối soát thu bồi thường từ 3PL. | `Q-OPS-03`, `Q-BIZ-03` | `BR-SHP-006`, `BR-SHP-007` |
| **DEC-BIZ-08** | Giới hạn Media, eKYC `5M` & Thuế C2C | Chuẩn hóa media: **1–10 ảnh (`<= 10MB/ảnh`, JPEG/PNG/WebP)** và **video bằng chứng/unboxing `<= 50MB`**. Bắt buộc **eKYC (CCCD + khuôn mặt)** cho giao dịch **`>= 5.000.000 VNĐ`**, lúc rút tiền Seller hoặc vượt ngưỡng doanh thu; thu thập **MST/CCCD** theo nghĩa vụ thuế TMĐT hiện hành. | `Q-BIZ-04`, `Q-OPS-01..02`, `Q-DATA-08` | `BR-MED-001`, `BR-ACT-006` |
| **DEC-BIZ-09** | Chat ẩn danh & Private Offer (R3) | Tính năng Chat & Trả giá thuộc phạm vi `R3`, tự động che/chặn PII (SĐT, link Zalo/FB, STK) để chống giao dịch ngoài sàn. Thương lượng giá sử dụng **`Private Price Offer`** gắn riêng cho `BuyerId` (`TTL 24h`), không sửa giá công khai của tin đăng. | Phạm vi `R3` | `BR-MSG-001`, `BR-ACT-005` |
| **DEC-BIZ-10** | Bằng chứng WORM & Kiểm soát bất biến | Mọi phán quyết khiếu nại và log giao dịch tài chính lưu trữ bất biến trên **S3 WORM Object Lock** (`INV-05`); mọi thao tác kho/tiền bắt buộc `Idempotency-Key` & `Row-Level Lock` (`INV-01..04`). | `Q-DR-01..06` | `INV-01..05` |

### Decision record mới (Mẫu bổ sung cho các thay đổi tương lai)

Các quyết định kiến trúc/chính sách phát sinh ngoài `DEC-BIZ-01..10` cần ghi nhận bổ sung theo mẫu dưới đây:

| Trường | Giá trị cần ghi |
|---|---|
| Decision ID/title | Mã ổn định và câu quyết định ngắn |
| Context | Vấn đề/câu hỏi cần quyết định |
| Options considered | Các phương án thực sự được phân tích |
| Decision and rationale | Phương án chọn và lý do |
| Consequences/risks | Tác động, trade-off và điều kiện |
| Owner/approver/date | Người chịu trách nhiệm, người duyệt, ngày |
| Evidence/source | Workshop/interview/benchmark/policy |
| Affected artifacts | Requirement, process, rule, data, service, security, recovery |
| Review date/status | Ngày xem lại, trạng thái |

## 7. Thứ tự xử lý đề xuất

1. Q-BIZ-01…06 và Q-BIZ-10…12: chốt mô hình marketplace, hàng hóa, moderation và giao dịch cốt lõi.
2. Q-OPS-03…06: xác định trách nhiệm payment/shipping trước khi thiết kế partner integration.
3. Q-DATA-01…04 và Q-DR-01…06: xác định ownership, hosting và recovery goals trước khi chốt AWS/database.
4. Các câu hỏi còn lại ảnh hưởng tới MVP, yêu cầu phi chức năng, security/privacy, chi phí và backlog.

Đây là thứ tự phụ thuộc phân tích, không phải quyết định roadmap.

## 8. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo backlog câu hỏi và decision log; chỉ ghi nhận các định hướng công nghệ đã nêu, chưa tự chốt cấu hình. |
| 1.1 | 13/11/2026 | Phê duyệt bộ quyết định nghiệp vụ baseline `DEC-BIZ-01..10` nhằm khép kín các lỗ hổng P0/P1 và đồng bộ thông số (`72h`, `5%`, `10MB`, Prepaid Escrow, Reverse Logistics, 3PL `<=30kg`) xuyên suốt tài liệu `02`–`09`. |
