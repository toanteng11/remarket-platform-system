# BA 03 – Requirements

## Mục đích

Chuyển kết quả phân tích ở 00–02 thành yêu cầu có thể kiểm tra: nghiệp vụ, chức năng, phi chức năng, use case, user story và tiêu chí chấp nhận. ReMarket là dự án phân tích và thiết kế; các thông số chưa được owner duyệt được đánh dấu `[TBD]` hoặc "đề xuất".

## Danh mục tài liệu

| Tài liệu | Nội dung | Quy mô |
|---|---|---|
| [business-requirements.md](business-requirements.md) | Yêu cầu nghiệp vụ `BRQ-*`, ngoài phạm vi, truy vết mục tiêu | 7 nhóm |
| [functional-requirements.md](functional-requirements.md) | Yêu cầu chức năng `FR-*` theo 15 module, bất biến liên service | ~190 FR |
| [non-functional-requirements.md](non-functional-requirements.md) | NFR theo 13 nhóm; ngưỡng chờ phê duyệt | ~110 NFR |
| [use-cases.md](use-cases.md) | 26 use case, luồng chính/thay thế/ngoại lệ | UC-01..26 |
| [user-stories.md](user-stories.md) | Backlog story theo 12 epic, story map, gợi ý phân đợt | ~100 US |
| [acceptance-criteria.md](acceptance-criteria.md) | Given/When/Then cho từng story, gồm đường âm, lặp, đồng thời, phục hồi | AC-* |

## Chuỗi truy vết

`OBJ` (02) → `BRQ` → `FR` / `NFR` → `UC` → `US` → `AC` → test scenario (08). Quy tắc nguồn: `BR-*`; ngoại lệ: `EX-*`; câu hỏi mở: `Q-*`.

## Thứ tự đọc

1. business-requirements → 2. functional / non-functional → 3. use-cases → 4. user-stories → 5. acceptance-criteria.

## Việc cần chốt trước khi đóng baseline

Xem NFR §14 và `02/open-questions-and-decisions.md` (ưu tiên P0): mô hình seller, thị trường, mô hình hàng hóa, hold window, chính sách hủy/hoàn/khiếu nại, COD, hãng vận chuyển, RPO/RTO, Region.

## Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo bộ yêu cầu 03 lần đầu. |

