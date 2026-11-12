# BA 01 – Stakeholders and Users

## Mục đích thư mục

Thư mục này mô tả các bên liên quan, nhóm người dùng, nhu cầu, quyền và hành trình dự kiến của ReMarket. Nội dung persona và nhu cầu hiện là giả thuyết cho tới khi được xác minh qua người dùng hoặc người có thẩm quyền.

ReMarket hiện là dự án phân tích và thiết kế. Các vai trò được nêu không khẳng định đã có người dùng thật hoặc nhân sự cụ thể trong một tổ chức vận hành.

## Danh sách tài liệu

| Tài liệu | Dùng để |
|---|---|
| stakeholder-register.md | Vai trò, mối quan tâm, quyền quyết định và mức ảnh hưởng của bên liên quan. |
| stakeholder-engagement-plan.md | Phương pháp tham vấn, workshop, câu hỏi, ghi nhận và phê duyệt kết quả. |
| user-personas.md | Persona giả thuyết cho buyer, seller, moderator, support, ops và đối tác giao nhận. |
| user-needs-and-pain-points.md | Nhu cầu/điểm khó có ID, mức ưu tiên sơ bộ, bằng chứng và cách kiểm chứng. |
| user-journeys.md | Journey buyer, seller, moderation, support và khôi phục dữ liệu. |
| user-roles-and-permissions.md | Role catalogue, quyền truy cập sơ bộ, least privilege và câu hỏi bảo mật. |

## Cách sử dụng

1. Đọc Stakeholder Register để biết ai cần tham gia và ai có quyền duyệt.
2. Dùng Engagement Plan để chuẩn bị elicitation; không xem đây là bằng chứng đã thực hiện.
3. Xác nhận persona và needs bằng phỏng vấn, quan sát, workshop hoặc quyết định nghiệp vụ có nguồn.
4. Rà journey và role matrix với các nhóm liên quan trước khi tạo use case/functional requirements.
5. Ghi nguồn xác nhận, ngày, người duyệt và tài liệu bị ảnh hưởng.
6. Khi chốt yêu cầu, truy vết ID nhu cầu/journey/role sang user stories, acceptance criteria và requirements traceability matrix.

## Trạng thái validation

| Nhóm | Trạng thái |
|---|---|
| Buyer và seller cá nhân | Giả thuyết cần user research |
| Seller chuyên nghiệp | Phạm vi cần xác nhận |
| Moderator và support | Quy trình/policy cần business owner xác nhận |
| Carrier/payment providers | Danh tính provider, API và sandbox chưa chốt |
| Roles/permissions | Baseline BA; cần Security/Product approval |
| Ops/DB/cloud | Cần xác nhận chủ sở hữu, RPO/RTO, backup và trách nhiệm vận hành |

## Quy ước

- Không thêm tên người thật, thông tin cá nhân hoặc kết quả phỏng vấn nếu chưa được cung cấp và chưa có cơ sở lưu trữ phù hợp.
- Phân biệt hypothesis, confirmed need, business rule và technical solution.
- Điều chưa quyết định được ghi là open question, không tự giả định là đã được phê duyệt.

## Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Khởi tạo tài liệu bên liên quan và người dùng ban đầu. |
| 1.1 | 12/11/2026 | Bổ sung STK-15..18 vào danh bạ và RACI matrix; bổ sung Persona P-00, P-08 và Journey J-06 cho Business Admin. |