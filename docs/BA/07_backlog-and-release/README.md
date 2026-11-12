# Mục 07: Backlog, Ưu Tiên Hóa & Kế Hoạch Phát Hành – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Thư mục | `docs/BA/07_backlog-and-release/` |
| Tài liệu | Chỉ mục Điều hướng & Ma trận Truy vết Tổng thể Backlog & Release |
| Mã tài liệu | `DOC-BA-07-IDX` |
| Phiên bản | 1.0 |
| Trạng thái | Hoàn thiện (Baseline cho Quản trị Phát triển & Bàn giao Kỹ thuật) |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | Toàn bộ các thư mục BA từ `00` đến `06` và `09_architecture-and-process-diagrams` |

---

## 1. Mục đích & Vai trò của Mục 07

Mục **`07_backlog-and-release`** đóng vai trò là chiếc cầu nối chiến lược chuyển hóa toàn bộ các phân tích nghiệp vụ (`00_project-context` đến `03_requirements`), giải pháp trải nghiệm người dùng (`04_product-and-ux`), kiến trúc dữ liệu phân tán (`05_domain-and-data`), thiết kế dịch vụ đám mây (`06_microservices-and-integrations`) và hệ thống sơ đồ kiến trúc trực quan (`09_architecture-and-process-diagrams`) thành một **Kế hoạch Thực thi Định lượng, Rõ ràng, Mạch lạc và Khả thi Tuyệt đối**.

Tài liệu bảo đảm nguyên tắc **Không lỗi sai (Zero-Defect)**:
- Không có bất kỳ sự sai lệch hay bỏ sót mã định danh nào giữa các phân hệ.
- Mọi User Story đều có nguồn gốc từ yêu cầu nghiệp vụ, gắn liền với màn hình giao diện, dịch vụ xử lý và cơ sở dữ liệu lưu trữ.
- 5 Bất biến Kiến trúc (`INV-01` đến `INV-05`) được bảo vệ nghiêm ngặt xuyên suốt từng Sprint.

---

## 2. Cấu trúc Tài liệu trong Thư mục `07_backlog-and-release/`

Thư mục bao gồm **5 tài liệu chuyên sâu** phối hợp chặt chẽ với nhau:

| STT | Tài liệu | Liên kết File | Tóm tắt Nội dung Cốt lõi |
|:---:|---|---|---|
| **01** | **Epics & Features** | [epics-and-features.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/epics-and-features.md) | Phân rã 4 tầng Backlog: 12 Business Capabilities → 12 Epics (`EP-ACC..OPS`) → 32 Product Features (`FT-01..32`) & 10 Architectural Enablers (`EN-01..10`) → 81 User Stories (`US-*`). Ánh xạ 100% sang `FR-*`, `UC-*`, `SCR-*`, `SVC-*`. |
| **02** | **Khung Ưu tiên hóa** | [prioritization.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/prioritization.md) | Phương pháp luận ưu tiên kép MoSCoW & WSJF (Weighted Shortest Job First). Bảng chấm điểm định lượng toàn bộ 32 Features, 10 Enablers và phân loại chi tiết 81 User Stories (56 Must, 23 Should, 2 Could, Won't Have). |
| **03** | **Kế hoạch Phát hành** | [release-scope.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/release-scope.md) | Quy hoạch 3 đợt phát hành (R1 MVP Giao dịch Ký quỹ An toàn, R2 Quản trị Tranh chấp & Đối soát Tự động, R3 Tối ưu Tăng trưởng & Mở rộng Sharding). Bộ tiêu chuẩn DoR / DoD 3 cấp, 5 Cổng Release Gates và chiến lược Blue/Green Zero-Downtime. |
| **04** | **Phụ thuộc & Rủi ro** | [dependencies-and-risks.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/dependencies-and-risks.md) | Ma trận phụ thuộc DAG, Sơ đồ đường găng Critical Path (CPM). Ánh xạ chốt 38 câu hỏi mở (`Q-BIZ-*`, `Q-OPS-*`, `Q-DATA-*`, `Q-DR-*`). Sổ đăng ký 15 rủi ro phát hành (`RSK-REL-01..15`) kèm phương án phòng ngừa và dự phòng. |
| **05** | **User Story Map & Lộ trình** | [story-mapping-and-delivery-roadmap.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/story-mapping-and-delivery-roadmap.md) | Bản đồ User Story Mapping 2 chiều (6 chặng Value Streams). Lộ trình thực thi chi tiết qua 12 Sprints (24 tuần) và cơ cấu 4 Đội ngũ đa chức năng (Cross-functional Squads). |

---

## 3. Hệ thống Quy ước Mã Định danh Chuẩn hóa trong Mục 07

Mọi tài liệu trong mục 07 tuân thủ quy chuẩn đặt tên mã thống nhất:

- **`EP-[XXX]`:** Mã Epic nghiệp vụ cốt lõi (Gồm 12 Epics: `EP-ACC`, `EP-LST`, `EP-SRC`, `EP-CHK`, `EP-PAY`, `EP-SHP`, `EP-NTF`, `EP-TRU`, `EP-DSP`, `EP-SUP`, `EP-ADM`, `EP-OPS`).
- **`FT-[01..32]`:** Mã Tính năng Sản phẩm chức năng (32 Product Features).
- **`EN-[01..10]`:** Mã Hạng mục Nền tảng Kỹ thuật & Dữ liệu (10 Architectural Enablers).
- **`US-[XXX]-[01..NN]`:** Mã Câu chuyện Người dùng (Tổng cộng 81 User Stories từ `03_requirements/user-stories.md`).
- **`VS-[01..06]`:** 6 Chặng Dòng Giá trị Khách hàng trên Story Map.
- **`REL-[1..3]`:** 3 Đợt Phát hành Lũy tiến (`R1`, `R2`, `R3`).
- **`RSK-REL-[01..15]`:** 15 Rủi ro Kế hoạch & Phát hành.

---

## 4. Thống kê Định lượng Toàn cảnh Backlog ReMarket

Dưới đây là bảng tổng hợp các chỉ số định lượng của toàn bộ hệ thống yêu cầu và phát hành:

```
+-------------------------------------------------------------------------------+
|                      REMARKET BACKLOG AT A GLANCE                             |
+-------------------------------------------------------------------------------+
|  Capabilities Nghiệp vụ:        12  (CAP-01 .. CAP-12)                        |
|  Mục tiêu Kinh doanh:            7  (BO-01 .. BO-07)                          |
|  Epics Nghiệp vụ & Vận hành:    12  (EP-ACC .. EP-OPS)                        |
|  Product Features:              32  (FT-01 .. FT-32)                          |
|  Architectural Enablers:        10  (EN-01 .. EN-10)                          |
|  User Stories Hoàn chỉnh:       81  (US-ACC-01..08 .. US-OPS-01..12)          |
|  Bất biến Kiến trúc:             5  (INV-01 .. INV-05)                        |
|  Màn hình & Modals UX:          34  (27 Screens SCR-* + 7 Modals MOD-*)       |
|  Microservices Kiến trúc:       10  (SVC-00 Gateway + SVC-01..09)             |
|  Sự kiện Miền Phân tán:        28  (EVT-01 .. EVT-28)                        |
|  Đợt Phát hành (Waves):          3  (R1 MVP, R2 Governance, R3 Scaling)       |
|  Lộ trình Thực thi (Sprints):   12  (24 tuần, 4 Squads song song)             |
|  Câu hỏi Mở được Ánh xạ:        38  (Q-BIZ: 16, Q-OPS: 7, Q-DATA: 9, Q-DR: 6) |
|  Rủi ro Phát hành Quản trị:     15  (RSK-REL-01 .. RSK-REL-15)                |
+-------------------------------------------------------------------------------+
```

---

## 5. Ma trận Truy vết Tổng thể (Master Traceability Matrix)

Bảng dưới đây thiết lập mối liên kết toàn vẹn xuyên suốt toàn bộ vòng đời phân tích và phát triển phần mềm ReMarket:

| Epic | Feature Cốt lõi | User Stories Đại diện | Màn hình Giao diện | Dịch vụ Xử lý | Cơ sở Dữ liệu Lưu trữ | Bất biến Bảo vệ | Release Wave |
|---|---|---|---|---|---|:---:|:---:|
| **`EP-ACC`** | `FT-01` (Auth), `FT-02` (KYC), `FT-03` (Lock) | `US-ACC-01..08` | `SCR-MKT-01, 08`, `SCR-SEL-01`, `SCR-ADM-01, 02` | `SVC-01 Identity` | `IdentityDB` (SQL Server) | An toàn danh tính | **R1 / R2** |
| **`EP-LST`** | `FT-04` (Listing), `FT-05` (Media), `FT-06` (Mod) | `US-LST-01..08` | `SCR-SEL-02, 03`, `SCR-ADM-03` | `SVC-02 Catalog`, `SVC-03 Media` | `CatalogDB` (MongoDB), S3 | Hàng đơn chiếc | **R1 / R2** |
| **`EP-SRC`** | `FT-07` (OpenSearch), `FT-08` (PDP) | `US-SRC-01..06` | `SCR-MKT-01, 02, 03` | `SVC-04 Search` | AWS OpenSearch Cluster | Đồng bộ NRT | **R1** |
| **`EP-CHK`** | `FT-09` (Quote), `FT-10` (Lock 15p), `FT-11` (Order) | `US-CHK-01..11` | `SCR-MKT-04, 05, 06`, `SCR-SEL-04`, `MOD-01` | `SVC-05 Order Processing` | `OrderDB` (SQL Server) | **`INV-01` (Chống bán trùng)** | **R1** |
| **`EP-PAY`** | `FT-12` (Intent), `FT-13` (Idempotent), `FT-14` (Escrow) | `US-PAY-01..10` | `SCR-MKT-04, 06`, `SCR-SEL-05`, `SCR-ADM-06` | `SVC-06 Payment & Escrow` | `PaymentDB` (SQL Server) | **`INV-02` (Chống trừ tiền trùng)** | **R1 / R2** |
| **`EP-SHP`** | `FT-16` (3PL Adapter), `FT-17` (Tracking), `FT-18` (RTS) | `US-SHP-01..08` | `SCR-SEL-04`, `SCR-MKT-07`, `SCR-ADM-05` | `SVC-07 Shipping & Logistics` | `ShippingDB` (MongoDB) | Toàn vẹn giao vận | **R1 / R2** |
| **`EP-NTF`** | `FT-19` (Dispatcher), `FT-20` (Quiet Hours) | `US-NTF-01..05` | `SCR-MKT-09`, Header Notifications | `SVC-09 Notification` | `NotificationDB` (MongoDB) | Bất đồng bộ | **R1 / R2** |
| **`EP-TRU`** | `FT-21` (Abuse), `FT-22` (Appeal), `FT-23` (Review) | `US-TRU-01..07` | `SCR-ADM-03`, `SCR-SEL-03`, `SCR-MKT-10`, `MOD-03` | `SVC-08 Trust & Support` | `SupportDB` (MongoDB) | Minh bạch đánh giá | **R1 / R2 / R3** |
| **`EP-DSP`** | `FT-24` (Freeze), `FT-25` (Adjudication) | `US-DSP-01..04` | `SCR-MKT-11`, `SCR-SEL-06`, `SCR-ADM-04`, `MOD-04` | `SVC-08 Trust`, `SVC-06 Payment` | `SupportDB`, `PaymentDB` | **`INV-04` (Đóng băng khi tranh chấp)** | **R2** |
| **`EP-SUP`** | `FT-26` (Cases), `FT-27` (Order 360°) | `US-SUP-01..04` | `SCR-MKT-12`, `SCR-ADM-07` | `SVC-08 Trust & Support` | `SupportDB` (MongoDB) | SLA phản hồi | **R1** |
| **`EP-ADM`** | `FT-28` (Taxonomy), `FT-29` (Keywords), `FT-30` (BI) | `US-ADM-01..04` | `SCR-ADM-07`, `SCR-ADM-08` | `SVC-02 Catalog`, `SVC-08 Trust` | `CatalogDB`, Analytics Replica | Quản trị linh hoạt | **R1 / R2 / R3** |
| **`EP-OPS`** | `FT-31` (Observability), `FT-32` (DR PITR, FinOps) | `US-OPS-01..12` | Monitoring Dashboards, AWS Console | Toàn bộ 10 thành phần `SVC-00..09` | Centralized Logs, RDS PITR, S3 WORM | **`INV-03` (Outbox CDC)<br>`INV-05` (Bảo toàn dữ liệu)** | **R1 / R2** |

---

## 6. Hướng dẫn Sử dụng & Bàn giao Kỹ thuật

Tài liệu trong mục 07 phục vụ trực tiếp cho các nhóm đối tượng sau:
- **Product Owners & Business Analysts:** Sử dụng [epics-and-features.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/epics-and-features.md) và [prioritization.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/prioritization.md) để quản lý phạm vi, sàng lọc Product Backlog và giải quyết các câu hỏi thay đổi phạm vi.
- **Scrum Masters & Agile Delivery Leads:** Sử dụng [story-mapping-and-delivery-roadmap.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/story-mapping-and-delivery-roadmap.md) và [dependencies-and-risks.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/dependencies-and-risks.md) để điều phối các Sprint, gỡ bỏ điểm nghẽn và theo dõi đường găng Critical Path.
- **Tech Leads & Software Engineers:** Sử dụng kết hợp [release-scope.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/release-scope.md) với tài liệu yêu cầu chi tiết tại `03_requirements/` và kiến trúc tại `05`, `06` để lập trình theo đúng tiêu chuẩn DoR / DoD.
- **QA Automation & Test Engineers:** Sử dụng các cổng kiểm soát tại [release-scope.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/release-scope.md) để thiết kế các kịch bản kiểm thử tải, kiểm thử tích hợp và kiểm chứng 5 bất biến kiến trúc.
- **SRE & Cloud DevOps Engineers:** Sử dụng kế hoạch khôi phục thảm họa, kịch bản Blue/Green và quản lý chi phí FinOps tại [dependencies-and-risks.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/dependencies-and-risks.md) và `06_microservices-and-integrations/`.

---

## 7. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Phân tích Nghiệp vụ & Kiến trúc | Khởi tạo tài liệu tổng quan, quy ước mã định danh và ma trận truy vết tổng thể cho toàn bộ Mục 07. |

