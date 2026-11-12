# BA 02 – Business Analysis

## Mục đích

Thư mục này mô tả vấn đề nghiệp vụ, mục tiêu, năng lực, quy trình hiện trạng tham chiếu, quy trình mục tiêu, quy tắc và các quyết định còn mở của ReMarket. Tài liệu tạo nền tảng để xác định yêu cầu chức năng/phi chức năng, thiết kế miền và kiến trúc ở các thư mục BA tiếp theo.

ReMarket hiện là dự án phân tích và thiết kế hệ thống thương mại điện tử mua bán hàng đã qua sử dụng. Tài liệu trong thư mục này không xác nhận rằng sàn đã được xây dựng, đang vận hành hoặc đã có người dùng thật.

## Danh mục tài liệu

| Tài liệu | Mục đích |
|---|---|
| problem-statement.md | Mô tả vấn đề, cơ hội, phạm vi phân tích và giả thuyết cần kiểm chứng. |
| business-objectives-and-success-metrics.md | Mục tiêu, chỉ số đo, công thức, nguồn dữ liệu dự kiến và trạng thái mục tiêu. |
| as-is-processes.md | Các quy trình hiện trạng tham chiếu có tính giả thuyết; dùng để khảo sát, không phải kết quả quan sát. |
| to-be-processes.md | Các quy trình mục tiêu đề xuất cho buyer, seller, vận hành và phục hồi dữ liệu. |
| business-rules.md | Danh mục quy tắc nghiệp vụ cần xác nhận, cùng nguyên tắc kỹ thuật liên quan. |
| business-capability-map.md | Phân rã năng lực kinh doanh và liên hệ sơ bộ tới vai trò/dịch vụ. |
| value-streams.md | Các dòng giá trị chính và kết quả cần tạo ra cho người dùng. |
| business-scenarios-and-exceptions.md | Kịch bản thành công, ngoại lệ, tác động và hành vi phục hồi cần phân tích. |
| open-questions-and-decisions.md | Câu hỏi chưa chốt, chủ sở hữu quyết định, tác động và bằng chứng cần thu thập. |

## Quy ước trạng thái

- **Định hướng đã nêu:** được yêu cầu trực tiếp trong phạm vi dự án, ví dụ mua bán đồ đã qua sử dụng, nghiên cứu Microservices/AWS, SQL Server, MongoDB, cache, load balancer và S3.
- **Giả thuyết cần xác minh:** nhận định hợp lý để định hướng phân tích nhưng chưa có phỏng vấn, quan sát hoặc quyết định phê duyệt.
- **Đề xuất:** phương án để thảo luận; không được hiểu là quyết định kiến trúc hoặc chính sách đã chốt.
- **Cần quyết định:** thiếu chủ sở hữu, chính sách, dữ liệu hoặc tiêu chí để chốt.
- **Ngoài phạm vi hiện tại:** chưa nằm trong dự án phân tích và thiết kế nếu chưa được bổ sung.

Tất cả luồng và quy tắc vận hành ở đây là đề xuất hoặc giả thuyết, trừ khi có nguồn quyết định được ghi lại. Không tự điền mục tiêu KPI, SLA, phí, thời hạn giữ hàng, thời hạn lưu dữ liệu, RPO/RTO hay chính sách hoàn tiền khi chưa có người có thẩm quyền xác nhận.

## Nguyên tắc sử dụng

1. Đọc problem statement và objectives trước, rồi rà soát as-is/to-be cùng stakeholder trong BA 01.
2. Mỗi yêu cầu phát sinh cần dẫn chiếu ID mục tiêu, nhu cầu, quy trình hoặc quy tắc có liên quan.
3. Ghi bằng chứng và người phê duyệt vào open-questions-and-decisions.md; cập nhật trạng thái sau workshop hoặc quyết định.
4. Tách nhu cầu nghiệp vụ khỏi lựa chọn giải pháp. Ví dụ, người dùng cần biết sản phẩm còn bán được; SQL Server, MongoDB, S3, cache hoặc dịch vụ AWS là nội dung thiết kế riêng.
5. Không coi trạng thái trong chỉ mục tìm kiếm, cache hoặc bản sao báo cáo là nguồn quyết định cho giao dịch nếu chưa có quy tắc nhất quán được chấp thuận.
6. Khi quy trình thay đổi, đánh giá ảnh hưởng đến yêu cầu, service boundary, quyền sở hữu dữ liệu, bảo mật, chi phí và kịch bản sao lưu/khôi phục.

## Tài liệu liên quan

- BA 00 Project Context: định hướng, phạm vi, giả định và công nghệ ưu tiên.
- BA 01 Stakeholders and Users: stakeholder register, persona, needs, journeys và quyền sơ bộ.
- BA 03 Requirements: yêu cầu chức năng, phi chức năng, use case và truy vết.
- BA 05 Domain and Data: thuật ngữ miền, mô hình dữ liệu và quyền sở hữu dữ liệu.
- BA 06 Microservices and Integrations: service boundaries, API, sự kiện và tích hợp.
- BA 08 Validation and Governance: xác nhận, phê duyệt và kiểm soát thay đổi.

## Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo bộ phân tích nghiệp vụ ban đầu; nội dung được đánh dấu rõ giả thuyết và câu hỏi cần xác nhận. |
| 1.1 | 12/11/2026 | Bổ sung sơ đồ trực quan (Capability, Sequence, Flowchart), quy trình P-10 (Notification), các nhóm quy tắc BR-SRC/NTF/MED/PRO/DSP, metric chống bán trùng và ma trận truy vết mục tiêu. |
