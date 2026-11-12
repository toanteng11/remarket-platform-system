# ReMarket – Business Analysis (BA) Documentation Hub

## 1. Giới thiệu dự án

**ReMarket** là dự án phân tích và thiết kế một nền tảng thương mại điện tử chuyên mua bán hàng đã qua sử dụng (C2C / Second-hand Marketplace). Dự án tập trung nghiên cứu ứng dụng kiến trúc **Microservices**, nguyên lý thiết kế **hệ thống phân tán** và giải pháp hạ tầng cloud trên nền tảng **AWS Cloud** (với các công nghệ cốt lõi: SQL Server, MongoDB, Cache, Load Balancer, Amazon S3).

> [!NOTE]
> ReMarket ở giai đoạn hiện tại là dự án **phân tích, mô hình hóa nghiệp vụ và thiết kế kiến trúc hệ thống**. Mọi số liệu đo lường, quy mô vận hành thương mại hoặc hợp đồng đối tác thật đều được đặt ở dạng giả thuyết hoặc chờ phê duyệt chính thức.

---

## 2. Cấu trúc thư mục tài liệu BA

Bộ tài liệu phân tích nghiệp vụ của ReMarket được phân bổ theo 10 thư mục chuẩn hóa:

| Thư mục | Tên phân hệ tài liệu | Trạng thái | Mô tả vắn tắt |
|---|---|---|---|
| **[00_project-context](file:///d:/remarket-platform-system/docs/BA/00_project-context/README.md)** | Ngữ cảnh & Định hướng dự án | Hoàn thiện v1.2 | Project brief, business case, phạm vi, giả định, ràng buộc và glossary thuật ngữ. |
| **[01_stakeholders-and-users](file:///d:/remarket-platform-system/docs/BA/01_stakeholders-and-users/README.md)** | Bên liên quan & Người dùng | Hoàn thiện v1.1 | Danh bạ stakeholder, kế hoạch tham vấn, personas, needs & pain points, user journeys và ma trận phân quyền. |
| **[02_business-analysis](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/README.md)** | Phân tích nghiệp vụ chi tiết | Hoàn thiện v1.1 | Problem statement, mục tiêu & KPI, quy trình as-is & to-be, business rules, capability map, kịch bản & câu hỏi mở. |
| **[03_requirements](file:///d:/remarket-platform-system/docs/BA/03_requirements/README.md)** | Đặc tả yêu cầu (BRD / SRS) | Hoàn thiện v1.0 | Yêu cầu chức năng, phi chức năng, use cases, user stories, acceptance criteria. |
| **[04_product-and-ux](file:///d:/remarket-platform-system/docs/BA/04_product-and-ux/README.md)** | Thiết kế sản phẩm & Trải nghiệm | Hoàn thiện v1.0 | Kiến trúc thông tin (IA), sitemap, screen inventory, wireframes & screen flows. |
| **[05_domain-and-data](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/README.md)** | Mô hình miền & Dữ liệu | Hoàn thiện v1.0 | Domain model DDD, Polyglot ERD, Data dictionary, Sharding, Indexing, Sagas & DR. |
| **[06_microservices-and-integrations](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/README.md)** | Kiến trúc dịch vụ & Tích hợp | Hoàn thiện v1.1 | Microservices catalog, Service boundaries, APIs, Event catalog, Adapters, AWS Cloud & 5-Layer Firewall/WAF Security. |
| **[07_backlog-and-release](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/README.md)** | Kế hoạch phát hành & Backlog | Hoàn thiện v1.1 | 12 Epics, 32 Features, 81 User Stories, MoSCoW & WSJF, Release Waves R1/R2/R3, DAG & CPM. |
| **[08_validation-and-governance](file:///d:/remarket-platform-system/docs/BA/08_validation-and-governance/README.md)** | Kiểm thử, Nghiệm thu & Quản trị | Hoàn thiện v1.1 | Ma trận RTM 100%, Kịch bản kiểm thử Invariants & E2E, NFR/WAF/Firewall/Chaos/DR Drill, Sign-Off & Change Log. |
| **[09_architecture-and-process-diagrams](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md)** | Trung tâm sơ đồ trực quan | Hoàn thiện v2.0 | 9 tài liệu chuyên đề & 44 sơ đồ Mermaid: C4 L1/L2, DDD, Hexagonal, 6 Value Streams, 6 State Machines, Polyglot DB/Sharding, Sagas/Outbox, AWS VPC/WAF/Firewall, DR Drill & CI/CD. |

---

## 3. Quy ước và nguyên tắc đọc tài liệu

1. **Deny by default & Least Privilege:** Áp dụng xuyên suốt từ ma trận phân quyền người dùng đến kết nối giữa các microservices.
2. **Source of Truth vs. Derived Data:** Dữ liệu khả dụng (Listing), đơn hàng (Order) và thanh toán (Payment) có kho dữ liệu nguồn chuẩn (Source of Truth). Dữ liệu Search Index và Cache là dữ liệu dẫn xuất (Derived Data), cho phép hội tụ chậm (eventual consistency) và không được phép đưa ra quyết định giao dịch cuối cùng.
3. **Traceability:** Mọi yêu cầu kỹ thuật hoặc kiến trúc đều phải truy vết được về Nhu cầu người dùng (`NEED-*`), Quy tắc nghiệp vụ (`BR-*`), Mục tiêu dự án (`OBJ-*` / `BC-O*`) và Năng lực kinh doanh (`CAP-*`).

