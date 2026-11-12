# Stakeholder Engagement Plan – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.0 |
| Trạng thái | Kế hoạch đề xuất; chưa có hoạt động tham vấn nào được xác nhận |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | stakeholder-register.md, user-personas.md, user-needs-and-pain-points.md, user-journeys.md |

## 1. Mục đích

Định hướng cách thu thập, kiểm tra và duyệt thông tin với các nhóm liên quan. Tài liệu này chỉ là kế hoạch; không khẳng định đã liên hệ hoặc thay mặt dự án gửi lời mời cho bất kỳ cá nhân/đối tác nào.

Engagement cần giúp dự án:
- xác nhận phạm vi và quy tắc marketplace;
- phân biệt nhu cầu đã có bằng chứng với giả thuyết;
- rà soát journey và quyền;
- định nghĩa yêu cầu database, tích hợp carrier, cloud, backup và phục hồi với người có chuyên môn;
- ghi quyết định, người duyệt và phần còn mở.

## 2. Nguyên tắc tham gia

1. Xác định người có quyền quyết định trước mỗi workshop; không để nhóm tham khảo vô tình trở thành người duyệt.
2. Thu thập nhu cầu từ buyer/seller thực tế nếu có thể; không suy luận thị trường chỉ từ nhóm dự án.
3. Giải thích mục tiêu, phạm vi dữ liệu và cách ghi nhận phản hồi trước khi hỏi người tham gia.
4. Hạn chế dữ liệu cá nhân trong ghi chú; không thu credential, payment information hoặc nội dung không cần thiết.
5. Tạo cơ hội phản hồi cho nhóm ít quyền lực như người mua/người bán, không chỉ nghe Sponsor/nhóm kỹ thuật.
6. Khi có ý kiến trái chiều, ghi cả quan điểm, tác động và người có thẩm quyền quyết định.
7. Tách business decisions khỏi technical recommendations; giữ log quyết định và lý do.
8. Mọi nội dung chưa xác minh được đánh dấu hypothesis/open question.

## 3. Kế hoạch tham gia theo nhóm

| Nhóm | Phương pháp đề xuất | Chủ đề cần thu thập | Đầu ra | Người điều phối cần có |
|---|---|---|---|---|
| Sponsor / Product Owner | Kickoff, scope workshop, decision review | Mục tiêu, thị trường, phạm vi, mô hình seller, MVP/deliverables, người duyệt. | Project decisions, approved scope, open questions. | BA, Sponsor |
| Người mua | Phỏng vấn, journey walkthrough, prototype review nếu có | Tìm kiếm, đánh giá tình trạng, niềm tin, checkout, payment, delivery, hỗ trợ. | Buyer needs, journey edits, acceptance examples. | BA/UX |
| Seller cá nhân | Phỏng vấn/quan sát tác vụ đăng tin, test form | Ảnh, condition, giá, sửa/ẩn tin, đơn, đóng gói/giao, hủy. | Seller needs, listing fields, exception cases. | BA/UX |
| Seller business (nếu trong scope) | Workshop theo quy trình, không giả định trước có mặt | Nhiều tin/nhân viên, import, báo cáo, số lượng/tồn, giao hàng, payout. | Scope decision: include/defer/exclude. | Product Owner, BA |
| Moderator | Quy trình/case workshop | Triage, lý do moderation, appeal, audit, xung đột với order/hold. | Moderation workflow, roles, business rules. | BA, Security |
| Customer Support | Scenario walkthrough | Tra cứu order/payment/shipping, dispute, refund, escalation, PII. | Support needs, permission cases, exception flows. | BA, Product Owner |
| Architect / Dev | Architecture review | Service boundaries, API/events, consistency, idempotency, failure modes, observability. | Service design decisions, ADRs, technical questions. | Architect |
| DBA / Data Architect | Data workshop | SQL Server/MongoDB mapping, schema, query/access patterns, migration, consistency, backup. | Data ownership, database options, risks. | Data owner, Architect |
| Cloud / SRE / Security | Cloud and recovery review | AWS account/Region, network, ALB, cache, IAM, secrets, backup, RPO/RTO, restore drill. | NFR, controls, deployment and recovery decisions. | Cloud owner, Security |
| Carrier / Payment partner | Technical discovery when providers chosen | API, auth, sandbox, events/callback, limits, timeout, duplicate, cancellation/refund, reconciliation. | Integration contract and dependency register. | Integration owner, Architect |

## 4. Gợi ý các workshop

### W-01: Project framing and scope

- Mục tiêu: chốt dự án là phân tích/thiết kế hay còn có prototype/deployment.
- Chủ đề: thị trường, loại seller, kênh Marketplace/Seller Center/Admin, ngoài phạm vi.
- Chuẩn bị: Project Brief, Business Case, Scope and Boundaries.
- Đầu ra: scope decision, sponsor/approver, câu hỏi cần xử lý.

### W-02: Buyer and seller journeys

- Mục tiêu: xác nhận các bước quan trọng và ngoại lệ thực tế.
- Chủ đề: tình trạng món hàng, ảnh, tìm kiếm, hỏi người bán, giữ hàng, payment, ship, hủy/tranh chấp.
- Chuẩn bị: persona và user journey hypotheses.
- Đầu ra: journey sửa đổi, business rules, evidence/status.

### W-03: Trust, moderation and support

- Mục tiêu: xác định quy trình an toàn và xử lý case.
- Chủ đề: tin sai, hàng hạn chế, report, appeal, dispute, refund, audit, dữ liệu support cần xem.
- Chuẩn bị: vai trò/quyền sơ bộ và case scenarios.
- Đầu ra: moderation/support workflows, policy owner, permission decisions.

### W-04: Data, services and AWS architecture

- Mục tiêu: nối yêu cầu nghiệp vụ với service/data boundaries.
- Chủ đề: SQL Server, MongoDB, Listing reservation, Order/Payment/Shipping ownership, cache, search projection, API/event.
- Chuẩn bị: service map và data hypotheses.
- Đầu ra: data ownership map, service boundaries, ADR/open decisions.

### W-05: Database backup and recovery

- Mục tiêu: thiết kế khả năng khôi phục có thể kiểm chứng.
- Chủ đề: data classification, RPO/RTO theo service, retention, SQL Server PITR/snapshot, MongoDB hosting and backup, S3 object recovery, AWS Backup support, cross-account/Region, restore test.
- Chuẩn bị: data inventory, workload assumptions, AWS budget/Region.
- Đầu ra: backup matrix, restore order, runbook owner, test criteria.

### W-06: Shipping/provider integration

- Mục tiêu: xác nhận adapter contract và quy tắc vận hành nhiều hãng.
- Chủ đề: quote/create/cancel/track, callback security, duplicate/out-of-order events, retry, DLQ, reconciliation, provider selection.
- Chuẩn bị: danh sách hãng ưu tiên và sandbox/API docs nếu có.
- Đầu ra: integration contract, provider dependency/risk, support owner.

## 5. Bộ câu hỏi phỏng vấn gợi ý

### Buyer

- Khi mua đồ cũ, thông tin nào giúp bạn tin tưởng tình trạng món hàng?
- Bạn so sánh tin đăng theo thuộc tính nào?
- Điều gì làm bạn dừng trước checkout?
- Bạn muốn biết điều gì về phí, hãng giao, thời gian và hoàn tiền?
- Khi payment pending nhưng tiền đã bị trừ, bạn cần thấy gì?

### Seller

- Bạn hiện quyết định giá và mô tả tình trạng như thế nào?
- Bước tạo tin nào mất thời gian hoặc dễ sai?
- Khi có hai người cùng muốn mua, bạn mong hệ thống xử lý ra sao?
- Bạn cần thông tin gì để chuẩn bị và bàn giao món hàng?
- Seller chuyên nghiệp có nằm trong scope không?

### Moderator / Support

- Những report nào cần ưu tiên?
- Ai có quyền ẩn/gỡ tin, khóa tài khoản hoặc yêu cầu sửa?
- Khi tin có order đang xử lý thì moderation ảnh hưởng thế nào?
- Support được xem dữ liệu nào và hành động nào cần phê duyệt?

### DBA / Cloud / Security

- SQL Server/MongoDB sẽ được phân vai theo access pattern nào?
- Shared cluster có tạo rủi ro về quyền, backup scope hoặc restore granularity không?
- Những database/object nào là source of truth?
- RPO/RTO nào cần đạt theo domain?
- Ai thực hiện/duyệt restore và kiểm tra tính toàn vẹn?
- Tài khoản/Region/budget và keys/secrets recovery đã có chưa?

### Carrier / Payment provider

- Có sandbox, API version và tài liệu webhook không?
- Request nào an toàn để retry? Dùng idempotency key nào?
- Callback có ký số/xác minh nguồn không?
- Làm sao đối soát trạng thái nếu callback bị trễ/mất?
- Quy tắc cancellation/refund/duplicate request là gì?

## 6. Ghi nhận và phê duyệt kết quả

Mỗi buổi tham gia cần có:
- mục tiêu, nhóm tham gia và người điều phối;
- ngày, phạm vi và tài liệu đã xem;
- nhu cầu/quy tắc được ghi nhận, nguồn và mức độ bằng chứng;
- điểm chưa thống nhất và người ra quyết định;
- hành động tiếp theo, owner và due date;
- tài liệu bị ảnh hưởng và traceability IDs.

Không ghi tên hoặc chi tiết nhạy cảm vào tài liệu chia sẻ nếu không cần. Yêu cầu quan trọng cần được xác nhận lại với người có thẩm quyền trước khi xem là approved.

## 7. Cadence đề xuất

| Hoạt động | Nhịp đề xuất | Đối tượng | Mục tiêu |
|---|---|---|---|
| Discovery workshop | Theo từng nhóm nghiệp vụ trước khi khóa requirements | Product, user representatives, BA | Làm rõ nhu cầu/quy tắc |
| Architecture review | Sau khi có service/data/cloud draft và trước khi chốt | Architect, DBA, Cloud, Security, Dev | Rà soát trade-offs và rủi ro |
| Decision review | Mỗi khi có quyết định ảnh hưởng scope/cost/RPO/RTO | Sponsor, Product Owner, decision owners | Duyệt hoặc trả lại quyết định |
| Document walkthrough | Cuối mỗi nhóm tài liệu | Stakeholders có liên quan | Xác minh đúng ý và hoàn chỉnh |
| Revalidation | Khi provider, workload, hosting hoặc scope thay đổi | Nhóm liên quan | Cập nhật giả định và thiết kế |

Cadence có thể điều chỉnh theo tiến độ; hiện chưa có lịch họp thực tế được xác nhận.

## 8. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo engagement plan đề xuất; chưa gửi lời mời/thực hiện tham vấn. |
