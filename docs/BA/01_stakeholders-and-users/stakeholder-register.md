# Stakeholder Register – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.0 |
| Trạng thái | Bản nháp; chưa xác nhận người đại diện cụ thể |
| Ngày cập nhật | 12/11/2026 |
| Phạm vi | Dự án phân tích và thiết kế marketplace đồ đã qua sử dụng, Microservices và AWS |
| Tài liệu liên quan | project-brief.md, scope-and-boundaries.md, stakeholder-engagement-plan.md |

## 1. Mục đích

Đăng ký các cá nhân/nhóm có ảnh hưởng hoặc chịu tác động bởi dự án, quyền quyết định, nhu cầu thông tin và cách cần tham gia. Tài liệu hiện ghi **vai trò cần có**, chưa gán tên người thật hoặc khẳng định đã phỏng vấn họ.

ReMarket hiện là dự án phân tích và thiết kế. Các nhóm người mua, người bán, nhân viên vận hành và đối tác được xem là nhóm người dùng/bên liên quan của giải pháp dự kiến; chưa có nghĩa sản phẩm đã hoạt động.

## 2. Quy ước

- **Ảnh hưởng:** mức độ nhóm có thể thay đổi mục tiêu, phạm vi, ngân sách, chính sách hoặc thiết kế.
- **Quan tâm:** mức độ nhóm cần hệ thống/tài liệu đáp ứng nhu cầu của họ.
- **Tham gia:** Mời xem xét là kế hoạch đề xuất, không có nghĩa đã liên hệ hoặc đã nhận lời.
- **Trạng thái:** các nhu cầu chưa có bằng chứng phỏng vấn được xem là giả thuyết cần xác nhận.
- Các tên nhà vận chuyển như GHTK, GHN và GrabExpress là ví dụ đối tác tiềm năng; hãng được chọn còn mở.

## 3. Danh sách bên liên quan nội bộ và dự án

| ID | Bên liên quan | Mục tiêu / mối quan tâm | Ảnh hưởng | Trách nhiệm hoặc quyết định | Mức tham gia đề xuất |
|---|---|---|---|---|---|
| STK-01 | Chủ dự án / Sponsor | Đạt mục tiêu dự án, kiểm soát phạm vi và quyết định các mốc chính. | Cao | Phê duyệt mục tiêu, phạm vi, nguồn lực và quyết định ngoài thẩm quyền nhóm kỹ thuật. | Tham gia kickoff, duyệt phạm vi, review thiết kế và chấp nhận deliverables. |
| STK-02 | Product Owner / Đại diện nghiệp vụ | Nghiệp vụ marketplace, chính sách đăng bán, mua hàng, thanh toán và giao nhận phù hợp. | Cao | Làm rõ ưu tiên, quy tắc nghiệp vụ, mô hình người bán và tiêu chí chấp nhận. | Workshop nghiệp vụ, xác nhận yêu cầu và xử lý câu hỏi mở. |
| STK-03 | Business Analyst | Yêu cầu đủ rõ, có thể truy vết và được thống nhất giữa các nhóm. | Trung bình/Cao | Điều phối elicitation, quản lý yêu cầu, giả định, thay đổi và tài liệu BA. | Làm việc xuyên suốt; duy trì traceability và issue log. |
| STK-04 | Solution Architect | Ranh giới service, giao tiếp, dữ liệu, chất lượng và lựa chọn AWS có căn cứ. | Cao | Đề xuất kiến trúc, đánh giá trade-off, ghi decision/ADR và rủi ro. | Tham gia domain workshop, review service map, database, cloud và recovery. |
| STK-05 | Nhóm phát triển | API, service boundaries và quy tắc đủ thực tế để triển khai trong tương lai. | Trung bình | Đánh giá tính khả thi, độ phức tạp, contract, testability và migration. | Review requirements, API/events và prototype nếu được duyệt. |
| STK-06 | Database/Data Architect hoặc DBA | Toàn vẹn, hiệu suất, quản trị SQL Server/MongoDB, migration và khả năng khôi phục. | Cao | Rà soát mô hình dữ liệu, engine fit, backup/restore, retention và RPO/RTO. | Workshop database; xác nhận recovery scenarios và restore runbook. |
| STK-07 | Cloud/DevOps/SRE | Triển khai AWS, quan sát, tự động hóa, chi phí, sự cố và khả năng phục hồi. | Cao | Đánh giá network, compute, load balancer, IAM, observability, backup và deployment. | Review cloud topology, IaC, alerting, DR và diễn tập khôi phục. |
| STK-08 | Security / Privacy | Bảo vệ tài khoản, dữ liệu cá nhân, thanh toán, bí mật và quyền truy cập. | Cao | Xác nhận phân loại dữ liệu, threat scenarios, access controls và yêu cầu privacy. | Tham gia design review và rà soát quyền/backup copies. |
| STK-09 | Người mua | Tìm được món phù hợp, hiểu chính xác tình trạng và mua an tâm. | Trung bình | Cung cấp nhu cầu, khó khăn và phản hồi hành trình mua. | Phỏng vấn/đi walkthrough; cần người đại diện đa dạng, chưa được tuyển. |
| STK-10 | Người bán cá nhân | Đăng món nhanh, mô tả đúng, quản lý tin và xử lý đơn thuận tiện. | Trung bình | Xác nhận luồng đăng bán, ảnh, tình trạng, giá và giao hàng. | Phỏng vấn/kiểm tra prototype; phân biệt người bán ít và nhiều giao dịch. |
| STK-11 | Người bán cửa hàng / chuyên nghiệp | Quản lý nhiều tin/đơn, tồn và hoạt động bán lặp lại. | Trung bình | Xác nhận nhu cầu quy mô lớn nếu loại người bán này thuộc phạm vi. | Cần xác nhận có nằm trong thị trường mục tiêu hay không. |
| STK-12 | Moderator / Quản trị nội dung | Xem báo cáo, xử lý tin vi phạm và áp dụng chính sách nhất quán. | Trung bình/Cao | Xác nhận hàng đợi kiểm duyệt, lý do ẩn/gỡ, audit và kháng nghị. | Workshop quy trình moderation; chính sách hiện còn mở. |
| STK-13 | Customer Support | Tra cứu đơn, thanh toán, shipment và lịch sử hỗ trợ mà không vượt quyền. | Trung bình | Xác nhận kịch bản tra cứu, tranh chấp, hoàn tiền và escalation. | Scenario walkthrough; làm rõ dữ liệu nào được xem/sửa. |
| STK-14 | Reviewer / Người đánh giá dự án | Deliverables đúng mục tiêu, rõ căn cứ và có thể đánh giá. | Trung bình/Cao | Đánh giá chất lượng hồ sơ hoặc sản phẩm theo tiêu chí đã thống nhất. | Review theo milestone; tiêu chí cụ thể cần được xác nhận. |
| STK-15 | Finance & Reconciliation Lead | Đối soát giao dịch, dòng tiền thanh toán/hoàn tiền, giải ngân seller và phí sàn. | Cao | Phê duyệt quy trình đối soát thanh toán, chính sách hoàn tiền và yêu cầu báo cáo tài chính. | Tham gia workshop thanh toán, rà soát ngoại lệ đối soát và kịch bản refund. |
| STK-16 | Legal & Compliance Representative | Tuân thủ bảo vệ người tiêu dùng, chính sách hàng cấm, quyền riêng tư dữ liệu (PII) và tranh chấp. | Cao | Xác nhận danh mục hàng cấm, chính sách tranh chấp, điều khoản dịch vụ và thời hạn lưu trữ dữ liệu. | Rà soát quy tắc nghiệp vụ (BR-PRO, BR-DSP) và kiểm soát PII. |
| STK-17 | Partner Integration Lead | Độ tin cậy kết nối API, webhook, hợp đồng tích hợp với cổng thanh toán và hãng vận chuyển. | Trung bình/Cao | Thẩm định kỹ thuật hợp đồng tích hợp ngoài, xử lý timeout, retry, sandbox và bảo mật callback. | Tham gia workshop adapter, định nghĩa canonical statuses và kịch bản lỗi đối tác. |
| STK-18 | Incident Commander / SRE Lead | Ứng phó sự cố gián đoạn dịch vụ, khôi phục dữ liệu phân tán và đảm bảo RTO/RPO. | Cao | Chủ trì quy trình phục hồi hệ thống khi có sự cố, phê duyệt mở lại luồng giao dịch sau khôi phục. | Tham gia thiết kế recovery runbook, kịch bản sự cố và diễn tập restore (P-09, EX-017..022). |

## 4. Đối tác và bên liên quan bên ngoài

| ID | Nhóm | Mối quan tâm / dữ liệu trao đổi | Ranh giới ReMarket | Điều cần xác nhận |
|---|---|---|---|---|
| EXT-01 | Nhà vận chuyển | Báo giá, tạo/hủy shipment, mã vận đơn, trạng thái, webhook và đối soát. | Shipping & Fulfillment tích hợp qua adapter; đối tác thực hiện vận chuyển. | Hãng được chọn, API, sandbox, webhook security, SLA, giới hạn gọi và dữ liệu lưu. |
| EXT-02 | Cổng thanh toán | Tạo payment, kết quả, refund, webhook và đối soát. | Payment Service điều phối; nhà cung cấp xử lý phần thanh toán theo hợp đồng. | Nhà cung cấp, phương thức, credential, callback, phí và trách nhiệm tranh chấp. |
| EXT-03 | AWS / Cloud platform | Tài nguyên, account, quyền, giới hạn dịch vụ, hỗ trợ và chi phí cloud. | AWS cung cấp hạ tầng; ReMarket chịu trách nhiệm cấu hình, dữ liệu và vận hành ứng dụng. | AWS account, Region, budget, IAM ownership và policy tổ chức. |
| EXT-04 | Nhà cung cấp MongoDB nếu dùng Atlas | Hosting, backup, monitoring, version và hỗ trợ MongoDB. | MongoDB data remains owned by Catalog & Listing; vendor hosts database if selected. | Atlas organization/project, Region, network, support plan, backup tier và chi phí. |
| EXT-05 | Nhà cung cấp identity/email/SMS | Xác thực liên kết hoặc gửi thông báo. | Tích hợp qua service nội bộ theo hợp đồng và quyền tối thiểu. | Có cần dịch vụ ngoài hay không; dữ liệu, vùng và điều kiện sử dụng. |
| EXT-06 | Pháp lý / tuân thủ | Privacy, người tiêu dùng, thuế, hàng hạn chế và retention. | Quy định áp dụng theo thị trường mục tiêu, hiện chưa xác định. | Thị trường, loại dữ liệu, trách nhiệm pháp lý và người tư vấn phù hợp. |

## 5. Ma trận ảnh hưởng và cách làm việc

| Nhóm quan tâm / ảnh hưởng | Cách tiếp cận đề xuất | Nhóm |
|---|---|---|
| Ảnh hưởng cao, quan tâm cao | Tham gia quyết định và review định kỳ; xử lý blockers sớm. | Sponsor, Product Owner, Architect, DBA/Data, Cloud/SRE, Security |
| Ảnh hưởng cao, quan tâm theo chủ đề | Mời vào đúng workshop; ghi quyết định/phụ thuộc và xác nhận bằng văn bản. | Payment/carrier partners, legal/compliance, AWS account owner |
| Ảnh hưởng trực tiếp, quyền quyết định thấp | Thu thập nhu cầu, thử hành trình và phản hồi; báo lại tác động của quyết định. | Buyer, seller, support, moderator |
| Quan tâm đánh giá / giám sát | Cập nhật tiến độ, scope, tiêu chí và deliverables tại milestone. | Reviewer, project team |
| Chưa xác định | Không giả định quyền hạn hoặc nhu cầu; hỏi sponsor để chỉ định đầu mối. | Nhóm nghiệp vụ hoặc vận hành chưa có đại diện |

## 6. Ma trận trách nhiệm đề xuất (RACI Matrix)

R = Responsible (Thực hiện); A = Accountable (Chịu trách nhiệm / Phê duyệt); C = Consulted (Tham vấn); I = Informed (Thông báo).

| Deliverable / Quyết định trọng yếu | Sponsor | Product Owner | BA | Solution Architect | DBA / Cloud / Security | Finance / Legal | Partner / Ops Lead |
|---|---:|---:|---:|---:|---:|---:|---:|
| Project scope, mục tiêu & ngân sách | **A** | C | R | C | I | C | I |
| User needs, journeys & acceptance | I | **A** | R | C | C | C | C |
| Business rules (Listing, Order, Checkout) | I | **A** | R | C | C | C | I |
| Business rules (Payment, Refund, Payout) | I | C | R | C | I | **A** | C |
| Compliance, PII retention, Hàng cấm | I | C | R | C | C | **A** | I |
| Service boundaries, API & Event catalog | I | C | C | **A/R** | C | I | C |
| Database ownership, Concurrency & Schema | I | C | C | **A** | R | I | I |
| Tích hợp cổng thanh toán & hãng vận chuyển | I | C | C | A | C | C | **R** |
| Cloud topology, Security & IAM policies | I | I | C | **A** | R | C | I |
| Backup policy, RTO/RPO & Phục hồi thảm họa | I | C | C | C | **A** | I | R |
| Nghiệm thu bộ hồ sơ phân tích BA | **A** | C | R | C | C | I | I |

## 7. Mục cần xác nhận

1. Ai là Sponsor và Product Owner; họ có quyền duyệt những quyết định nào?
2. Có đại diện buyer, seller, moderator và support để tham gia phỏng vấn/validation không?
3. Marketplace phục vụ cá nhân, cửa hàng hay cả hai?
4. Có vai trò đội giao hàng nội bộ hay chỉ tích hợp API đối tác?
5. Đối tác vận chuyển/thanh toán nào là ưu tiên; ai chịu trách nhiệm cung cấp API/sandbox?
6. Thị trường, dữ liệu cá nhân và quy định áp dụng ở đâu?
7. Ai phê duyệt NFR, retention, RPO/RTO, AWS budget và chính sách khôi phục?
8. Ai chịu trách nhiệm vận hành database và diễn tập restore trong phạm vi triển khai tương lai?

## 8. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo stakeholder register theo vai trò; chưa gán tên người thật. |
