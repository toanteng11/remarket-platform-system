# Bộ Sơ đồ Đường ống CI/CD, Triển khai Zero-Downtime & Lộ trình Phát hành (CI/CD, Deployment & Release Roadmap Diagrams)

> **Mã tài liệu:** `DOC-BA-09-CIC`  
> **Phiên bản:** `v2.0`  
> **Ngày cập nhật:** `2026-11-12`  
> **Tác giả:** Senior Principal DevOps Architect & Lead Business Analyst  
> **Liên kết đối chiếu:** [non-functional-requirements.md](../03_requirements/non-functional-requirements.md), [dependencies-and-risks.md](../07_backlog-and-release/dependencies-and-risks.md), [story-mapping-and-delivery-roadmap.md](../07_backlog-and-release/story-mapping-and-delivery-roadmap.md), [non-functional-and-resilience-validation-plan.md](../08_validation-and-governance/non-functional-and-resilience-validation-plan.md)

---

## 1. Mục đích Tài liệu

Tài liệu này mô hình hóa trực quan quy trình tự động hóa tích hợp và triển khai liên tục (**DevSecOps CI/CD Pipeline**), chiến lược phát hành không gián đoạn dịch vụ (**Blue/Green & Canary Zero-Downtime Deployment**), quy trình tiến hóa lược đồ cơ sở dữ liệu không khóa bảng (**Expand-and-Contract Database Migration**), và **Sơ đồ Đường găng Dự án (Critical Path DAG)** trải dài qua 12 Sprints (24 tuần) của hệ thống **ReMarket Platform**.

Toàn bộ các sơ đồ dưới đây đảm bảo tuân thủ nghiêm ngặt chỉ tiêu `NFR-MNT-01..03` (Thời gian từ Git Commit đến Production `< 15 phút`, Tỷ lệ kiểm thử tự động `>= 80%`, Zero-Downtime Deployment `100%`) và `NFR-SEC-14` (Quét lỗ hổng bảo mật SAST/SCA/Container Image 100% trước khi phát hành).

---

## 2. `DIAG-CIC-01` — Đường ống CI/CD DevSecOps Tự động & Cổng Chất lượng (`Automated DevSecOps Pipeline & Quality Gates`)

### 2.1. Bối cảnh & Mục tiêu Kỹ thuật
Mỗi khi lập trình viên tạo Pull Request hoặc hợp nhất mã nguồn vào nhánh `main`, đường ống **GitHub Actions** kết hợp **AWS CodeDeploy** sẽ thực thi 6 chặng kiểm duyệt tự động (Quality Gates). Bất kỳ vi phạm nào về độ bao phủ kiểm thử (`< 80%`), lỗi hợp đồng giao tiếp giữa các Microservices (**Pact Consumer-Driven Contract Testing**), hoặc phát hiện lỗ hổng bảo mật mức `High/Critical` từ **SonarQube / Snyk / Trivy** đều ngay lập tức chặn đứng tiến trình Build (`Fail-Fast Gate`).

### 2.2. Sơ đồ Luồng Đường ống CI/CD (`DIAG-CIC-01`)

```mermaid
flowchart TB
    classDef dev fill:#1e293b,stroke:#475569,color:#ffffff,stroke-width:2px
    classDef ci fill:#1e3a8a,stroke:#1d4ed8,color:#ffffff,stroke-width:2px
    classDef sec fill:#881337,stroke:#e11d48,color:#ffffff,stroke-width:2px
    classDef test fill:#0f766e,stroke:#0d9488,color:#ffffff,stroke-width:2px
    classDef art fill:#4c1d95,stroke:#6d28d9,color:#ffffff,stroke-width:2px
    classDef cd fill:#065f46,stroke:#059669,color:#ffffff,stroke-width:2px
    classDef fail fill:#7f1d1d,stroke:#dc2626,color:#ffffff,stroke-width:2px,stroke-dasharray: 5 5

    subgraph SG_SOURCE["1. Quản lý Mã nguồn (Source Control & GitOps)"]
        DEV["Lập trình viên (Developer)<br/>Git Feature Branch -> Pull Request"]:::dev
        GH["GitHub Enterprise Repository<br/>Branch Protection & Code Owners (2 Approvals)"]:::dev
    end

    subgraph SG_STAGE1["2. Chặng Kiểm tra Tĩnh & Bảo mật Mã nguồn (SAST & SCA Gate)"]
        LINT["Build & Linter Check<br/>(.NET 8 Roslyn Analyzer / TypeScript ESLint)"]:::ci
        SONAR["SonarQube Quality Gate<br/>(Code Smells, Bugs = 0, Duplication < 3%)"]:::sec
        SNYK["Snyk / Dependabot SCA Scan<br/>(Quét thư viện NuGet / NPM — Zero Critical/High CVE)"]:::sec
        GITLEAKS["GitLeaks Secret Scanner<br/>(Chặn tuyệt đối lộ API Key / Connection String)"]:::sec
    end

    subgraph SG_STAGE2["3. Chặng Kiểm thử Tự động Đa tầng (Automated Testing Pyramid)"]
        UNIT["Unit & Domain Invariant Tests<br/>(xUnit / Jest — Coverage >= 80% cho Domain Core)"]:::test
        INTEG["Integration Tests với Testcontainers<br/>(Khởi chạy SQL Server, MongoDB, Redis, Kafka thật trong Docker)"]:::test
        PACT["Pact Broker Contract Testing<br/>(Xác minh OpenAPI v3 & AsyncAPI Schema giữa Consumer/Provider)"]:::test
    end

    subgraph SG_STAGE3["4. Đóng gói & Quét Bảo mật Container (Artifact & Image Security)"]
        DOCKER["Multi-Stage Docker Build<br/>(Distroless / Alpine Non-Root Container Image)"]:::art
        TRIVY["Trivy Container Image Scanner<br/>(Quét OS Packages & Layer Vulnerabilities)"]:::sec
        COSIGN["Cosign Image Signing & SBOM<br/>(Ký số Image bằng AWS KMS & Xuất danh mục SBOM)"]:::art
        ECR[("Amazon ECR Private Registry<br/>(Immutable Image Tag: git-sha + semver)")]:::art
    end

    subgraph SG_STAGE4["5. Triển khai Môi trường Staging & Kiểm thử Chịu tải (Pre-Prod Verification)"]
        STAGE_DEP["Triển khai tự động lên EKS/ECS Staging<br/>(Terraform IaC + Flyway/EF Migration Dry-Run)"]:::cd
        E2E["Playwright E2E & API Regression Suite<br/>(Kiểm thử luồng VS-01..VS-06 với Sandbox VNPay/GHN)"]:::test
        K6["k6 Performance & Chaos Smoke Test<br/>(Verify p95 <= 200ms, Zero Double-Sell INV-01)"]:::test
    end

    subgraph SG_STAGE5["6. Phê duyệt & Phát hành Production (Production Release)"]
        APPROVAL{"Cổng Phê duyệt Phát hành<br/>(Release Manager / CAB Sign-Off)"}:::cd
        PROD_DEP["AWS CodeDeploy Blue/Green Canary<br/>(Triển khai lên Production Multi-AZ)"]:::cd
    end

    BLOCK["CHẶN ĐỨNG PIPELINE (PIPELINE FAILED)<br/>Gửi cảnh báo Slack/PagerDuty & Chặn Merge PR"]:::fail

    DEV -->|"Tạo PR / Merge vào main"| GH
    GH -->|"Trigger Webhook"| LINT
    LINT --> SONAR & SNYK & GITLEAKS

    SONAR & SNYK & GITLEAKS -->|"Pass 100%"| UNIT
    SONAR -.->|"Vi phạm ngưỡng / Phát hiện CVE"| BLOCK
    GITLEAKS -.->|"Phát hiện lộ Secret"| BLOCK

    UNIT --> INTEG --> PACT
    UNIT -.->|"Coverage < 80% hoặc Test Fail"| BLOCK
    PACT -.->|"Vỡ hợp đồng API/Event"| BLOCK

    PACT -->|"Pass 100%"| DOCKER
    DOCKER --> TRIVY
    TRIVY -.->|"Phát hiện lỗ hổng OS Image"| BLOCK
    TRIVY -->|"Image Sạch"| COSIGN --> ECR

    ECR --> STAGE_DEP --> E2E --> K6
    E2E -.->|"Lỗi luồng E2E"| BLOCK
    K6 -.->|"Vi phạm SLA p95"| BLOCK

    K6 -->|"Đạt chuẩn Pre-Prod"| APPROVAL
    APPROVAL -->|"Approved"| PROD_DEP
```

---

## 3. `DIAG-CIC-02` — Chiến lược Triển khai Zero-Downtime (`Blue/Green & Canary Deployment`) & Tự động Quay lui (`Instant Rollback`)

### 3.1. Bối cảnh & Mục tiêu Kỹ thuật
Khi phát hành phiên bản mới của các dịch vụ lõi (như `SVC-04 Order`, `SVC-05 Payment & Escrow`), hệ thống tuyệt đối không được làm rớt bất kỳ kết nối thanh toán hay giữ chỗ nào đang diễn ra. **AWS CodeDeploy** phối hợp cùng **Application Load Balancer (ALB)** và **ECS Fargate / EKS** thực hiện dịch chuyển lưu lượng theo từng nấc (**Canary Traffic Shifting**: `10%` trong 5 phút $\rightarrow$ `50%` trong 10 phút $\rightarrow$ `100%`), đồng thời giám sát chặt chẽ tỷ lệ lỗi `HTTP 5xx` và độ trễ `p95` qua **CloudWatch Alarms** để tự động quay lui (**Auto-Rollback `< 30 giây`**) nếu phát hiện bất thường.

### 3.2. Sơ đồ Tuần tự Triển khai Canary & Tự động Rollback (`DIAG-CIC-02`)

```mermaid
sequenceDiagram
    autonumber
    participant CD as AWS CodeDeploy<br/>(Deployment Controller)
    participant ALB as AWS ALB / Kong Gateway<br/>(Weighted Target Groups)
    participant BLUE as Cụm Blue (Phiên bản v1.0)<br/>(Active Production Tasks)
    participant GREEN as Cụm Green (Phiên bản v1.1)<br/>(New Replacement Tasks)
    participant CW as CloudWatch & Prometheus<br/>(SLO / Error Rate Monitor)

    Note over CD,CW: GIAI ĐOẠN 1: Khởi tạo Cụm Green & Kiểm tra Sức khỏe Nội bộ (Pre-Traffic Hook)
    CD->>GREEN: 1. Khởi chạy các Container Task v1.1 trong Private App Subnet (Multi-AZ)
    activate GREEN
    GREEN-->>CD: 2. Container Ready (Health Check /health/live & /health/ready = 200 OK)
    CD->>GREEN: 3. Chạy Lambda Pre-Traffic Smoke Test (Gọi thử API nội bộ trên cổng Test 8443)
    GREEN-->>CD: 4. Smoke Test thành công 100%

    Note over CD,CW: GIAI ĐOẠN 2: Dịch chuyển Canary Nấc 1 (10% Green / 90% Blue trong 5 phút)
    CD->>ALB: 5. Cập nhật trọng số Target Group: Blue = 90%, Green = 10%
    ALB->>BLUE: 6a. Định tuyến 90% lưu lượng người dùng thật
    ALB->>GREEN: 6b. Định tuyến 10% lưu lượng người dùng thật (Canary Traffic)

    alt Kịch bản A: Phiên bản v1.1 Hoạt động Ổn định (Happy Path)
        CW->>CD: 7. Sau 5 phút: Tỷ lệ HTTP 5xx < 0.05%, Latency p95 <= 180ms, Không có DLQ Spike
        CD->>ALB: 8. Nâng trọng số Nấc 2: Blue = 50%, Green = 50% (Giám sát thêm 10 phút)
        CW->>CD: 9. Sau 10 phút: Các chỉ số SLO/SLA hoàn toàn xanh
        CD->>ALB: 10. Chuyển hoàn toàn 100% lưu lượng sang Cụm Green (Blue = 0%, Green = 100%)
        CD->>BLUE: 11. Bắt đầu Connection Draining (Chờ 60s cho các request đang xử lý hoàn tất)
        Note over BLUE: Giữ Cụm Blue ở trạng thái Standby 60 phút<br/>trước khi thu hồi tài nguyên (Termination)
    else Kịch bản B: Phát hiện Lỗi trên Cụm Green (Auto-Rollback < 30 giây)
        CW-->>CD: 12. CẢNH BÁO ĐỎ (ALARM): Tỷ lệ HTTP 5xx vượt 0.5% hoặc Latency p95 > 500ms!
        CD->>ALB: 13. LẬP TỨC CẮT 100% LƯU LƯỢNG VỀ LẠI CỤM BLUE (Blue = 100%, Green = 0%)
        CD->>GREEN: 14. Cô lập Cụm Green để giữ nguyên Logs/Memory Dump phục vụ điều tra
        CD-->>CW: 15. Phát cảnh báo PagerDuty/Slack: "Auto-Rollback v1.1 -> v1.0 hoàn tất trong 18 giây"
    end
    deactivate GREEN
```

---

## 4. `DIAG-CIC-03` — Quy trình Nâng cấp Cơ sở Dữ liệu Không Khóa Bảng (`Expand-and-Contract Zero-Downtime Schema Migration`)

### 4.1. Bối cảnh & Mục tiêu Kỹ thuật
Trong suốt quá trình triển khai Canary (khi cả hai phiên bản ứng dụng `v1.0 Blue` và `v1.1 Green` cùng đọc/ghi vào một cơ sở dữ liệu **SQL Server** hoặc **MongoDB**), mọi thay đổi cấu trúc bảng (Schema Migration) tuyệt đối không được làm gãy truy vấn của phiên bản cũ (`Backward Compatibility`) và không được gây khóa bảng (`Table Lock`). ReMarket áp dụng bắt buộc mẫu **Expand-and-Contract (Parallel Change)** gồm 3 pha tách biệt.

### 4.2. Sơ đồ Quy trình Tiến hóa Lược đồ 3 Pha (`DIAG-CIC-03`)

```mermaid
flowchart LR
    classDef phase1 fill:#1e3a8a,stroke:#1d4ed8,color:#ffffff,stroke-width:2px
    classDef phase2 fill:#b45309,stroke:#d97706,color:#ffffff,stroke-width:2px
    classDef phase3 fill:#065f46,stroke:#059669,color:#ffffff,stroke-width:2px
    classDef db fill:#4c1d95,stroke:#6d28d9,color:#ffffff,stroke-width:2px

    subgraph SG_P1["PHA 1: MỞ RỘNG (EXPAND PHASE — Release N)"]
        direction TB
        E_DB[("1. Chạy Flyway/EF Migration (Non-Breaking):<br/>- Thêm cột mới cho phép NULL (Nullable)<br/>- Tạo Index với tùy chọn ONLINE = ON<br/>- Tuyệt đối KHÔNG xóa/đổi tên cột cũ")]:::db
        E_APP["2. Triển khai Code v1.1 (Dual-Write / Fallback Read):<br/>- Khi GHI: Ghi đồng thời vào cả cột cũ & cột mới<br/>- Khi ĐỌC: Ưu tiên đọc cột mới, nếu NULL thì đọc cột cũ"]:::phase1
        E_DB --> E_APP
    end

    subgraph SG_P2["PHA 2: ĐỒNG BỘ DỮ LIỆU CŨ (BACKFILL & TRANSITION — Background Job)"]
        direction TB
        B_JOB["3. Chạy Batch Backfill Worker theo lô nhỏ:<br/>- Quét các bản ghi lịch sử có cột mới = NULL<br/>- Cập nhật theo từng Batch 1,000 dòng (Tránh Lock Escalation)<br/>- Kiểm tra đối soát 100% dòng đã có dữ liệu mới"]:::phase2
        B_SWITCH["4. Triển khai Code v1.2 (Cutover Read/Write):<br/>- Ứng dụng chuyển sang ĐỌC và GHI 100% trên cột mới<br/>- Ngừng phụ thuộc hoàn toàn vào cột cũ"]:::phase2
        B_JOB --> B_SWITCH
    end

    subgraph SG_P3["PHA 3: THU HẸP & DỌN DẸP (CONTRACT PHASE — Release N+1)"]
        direction TB
        C_VERIFY["5. Xác nhận Ổn định trên Production:<br/>- Không còn phiên bản v1.0 nào đang chạy<br/>- Telemetry xác nhận 0 truy vấn chạm vào cột cũ trong 7 ngày"]:::phase3
        C_DB[("6. Chạy Migration Dọn dẹp (Cleanup DDL):<br/>- Áp đặt ràng buộc NOT NULL lên cột mới<br/>- Xóa bỏ (DROP) cột cũ không còn sử dụng")]:::db
        C_VERIFY --> C_DB
    end

    SG_P1 ==>|"An toàn Rollback 100%<br/>bất kỳ lúc nào"| SG_P2
    SG_P2 ==>|"Sau khi 100% bản ghi<br/>đã Backfill thành công"| SG_P3
```

---

## 5. `DIAG-CIC-04` — Sơ đồ Đường găng Dự án (`Critical Path DAG`) & Lộ trình Thực thi 12 Sprints qua 3 Đợt Phát hành (`R1`, `R2`, `R3`)

### 5.1. Bối cảnh & Mục tiêu Kỹ thuật
Sơ đồ mạng lưới phụ thuộc có hướng (**Directed Acyclic Graph — DAG**) dưới đây tổng hợp toàn bộ lộ trình xây dựng nền tảng ReMarket trong **24 tuần (12 Sprints)** được chia thành 3 đợt phát hành lớn:
- **Release 1.0 — MVP Core Marketplace & Safe Escrow (Sprint 1 $\rightarrow$ 5):** Tập trung vào Đường găng (**Critical Path**) từ Hạ tầng AWS/WAF (`EN-01`), Định danh (`EP-01`), Đăng tin (`EP-02`), Giữ chỗ Độc quyền `Inventory = 1` (`EP-04`), Thanh toán Ký quỹ (`EP-05`) đến Vận chuyển 3PL & Giải ngân `T+3` (`EP-06`).
- **Release 1.5 — Trust, Governance & Dispute Automation (Sprint 6 $\rightarrow$ 8):** Hoàn thiện Khiếu nại & Đóng băng Ký quỹ tức thời (`EP-07`), Trung tâm Quản trị & Kiểm duyệt AI (`EP-10`), Thông báo Đa kênh (`EP-09`), và Đối soát Tài chính 3 chiều (`EN-04`).
- **Release 2.0 — Scale, Intelligence & Ecosystem Expansion (Sprint 9 $\rightarrow$ 12):** Nâng cấp Tìm kiếm NRT OpenSearch nâng cao (`EP-03`), Chat Thời gian thực đính kèm Video (`EP-08`), Khuyến mãi/Seller Pro (`EP-11`) và Kiểm thử Chịu tải/Diễn tập DR quy mô lớn.

### 5.2. Sơ đồ Đường găng & Lộ trình 12 Sprints (`DIAG-CIC-04`)

```mermaid
flowchart LR
    classDef crit fill:#881337,stroke:#f43f5e,color:#ffffff,stroke-width:3px
    classDef r1 fill:#1e3a8a,stroke:#3b82f6,color:#ffffff,stroke-width:2px
    classDef r2 fill:#0f766e,stroke:#14b8a6,color:#ffffff,stroke-width:2px
    classDef r3 fill:#4c1d95,stroke:#8b5cf6,color:#ffffff,stroke-width:2px
    classDef mile fill:#b45309,stroke:#f59e0b,color:#ffffff,stroke-width:3px

    subgraph SG_R1["RELEASE 1.0: MVP CORE & SAFE ESCROW (Sprint 1 -> Sprint 5)"]
        direction TB
        S1_EN01["[Sprint 1] EN-01 & EN-02<br/>AWS Multi-AZ VPC, WAF v2, Network Firewall,<br/>Kong Gateway, Kafka + Debezium Outbox"]:::crit
        S1_EP01["[Sprint 1-2] EP-01<br/>Identity, OAuth2/PKCE, RBAC & eKYC<br/>(SVC-01)"]:::crit
        S2_EP02["[Sprint 2-3] EP-02<br/>Listing Lifecycle, S3 Pre-signed + ClamAV<br/>& State Machine Inventory=1 (SVC-02)"]:::crit
        S3_EP03_BASIC["[Sprint 3] EP-03 (Basic)<br/>Tìm kiếm & Bộ lọc Danh mục Cơ bản<br/>(SVC-03)"]:::r1
        S3_EP04["[Sprint 3-4] EP-04<br/>Two-Tier Reservation Lock (Redis + SQL UPDLOCK)<br/>& 15m TTL Checkout Saga (SVC-04)"]:::crit
        S4_EP05["[Sprint 4-5] EP-05<br/>Escrow Ledger (Append-Only), VNPay/MoMo/VietQR<br/>& Late-Webhook Auto-Refund (SVC-05)"]:::crit
        S5_EP06["[Sprint 5] EP-06<br/>3PL GHN/GHTK Integration, POD Tracking<br/>& T+3 Auto-Release Settlement (SVC-06)"]:::crit
        M1(("CỘT MỐC GO-LIVE R1.0<br/>(Cuối Sprint 5 — Tuần 10)<br/>MVP Soft Launch")):::mile
    end

    subgraph SG_R2["RELEASE 1.5: TRUST, DISPUTE & GOVERNANCE (Sprint 6 -> Sprint 8)"]
        direction TB
        S6_EP07["[Sprint 6-7] EP-07<br/>Dispute Management, Instant Escrow Freeze<br/>(< 1s INV-04) & Return Shipping (SVC-07)"]:::crit
        S6_EP08_BASIC["[Sprint 6-7] EP-08 (Core)<br/>In-App Contextual Chat 1-1 & PII/Link Masking<br/>(SVC-08)"]:::r2
        S7_EP09["[Sprint 7] EP-09<br/>Multi-Channel Notification Engine<br/>(FCM Push, Zalo ZNS, SES, SMS) (SVC-09)"]:::r2
        S8_EP10["[Sprint 8] EP-10 & EN-04<br/>Admin Order 360, AI Moderation Queue,<br/>3-Way Daily Reconciliation & WORM Audit"]:::crit
        M2(("CỘT MỐC GO-LIVE R1.5<br/>(Cuối Sprint 8 — Tuần 16)<br/>Public Commercial Launch")):::mile
    end

    subgraph SG_R3["RELEASE 2.0: SCALE, NRT SEARCH & GROWTH (Sprint 9 -> Sprint 12)"]
        direction TB
        S9_EP03_ADV["[Sprint 9-10] EP-03 (Advanced)<br/>OpenSearch NRT (< 2s), Geo-Search,<br/>Typo-Tolerance & Dynamic Facets"]:::r3
        S10_EP08_ADV["[Sprint 10] EP-08 (Rich Media)<br/>Video/Image Chat Evidence, Make-an-Offer<br/>trực tiếp trong khung Chat"]:::r3
        S11_EP11["[Sprint 11] EP-11 & EN-03<br/>Voucher/Fee Waiver, Seller Analytics,<br/>OpenTelemetry Full-Stack & Chaos GameDay"]:::r3
        S12_DR["[Sprint 12] DR & Scale Hardening<br/>SQL Sharding (> 2TB Ready), Cross-Region DR Drill<br/>(RPO <= 5m, RTO <= 30m) & Final CAB Sign-Off"]:::crit
        M3(("CỘT MỐC GO-LIVE R2.0<br/>(Cuối Sprint 12 — Tuần 24)<br/>50,000 CCU Scale Ready")):::mile
    end

    %% Đường găng (Critical Path)
    S1_EN01 ==>|"Hạ tầng & Outbox"| S1_EP01
    S1_EP01 ==>|"Xác thực & KYC"| S2_EP02
    S2_EP02 ==>|"Tin đăng ACTIVE"| S3_EP04
    S2_EP02 --> S3_EP03_BASIC
    S3_EP03_BASIC --> S3_EP04
    S3_EP04 ==>|"Lock 15 phút"| S4_EP05
    S4_EP05 ==>|"Escrow HELD"| S5_EP06
    S5_EP06 ==> M1

    M1 ==> S6_EP07
    M1 --> S6_EP08_BASIC
    M1 --> S7_EP09
    S6_EP07 ==>|"Escrow FROZEN & Arbitration"| S8_EP10
    S6_EP08_BASIC --> S8_EP10
    S7_EP09 --> S8_EP10
    S8_EP10 ==> M2

    M2 --> S9_EP03_ADV
    M2 --> S10_EP08_ADV
    S9_EP03_ADV --> S11_EP11
    S10_EP08_ADV --> S11_EP11
    M2 ==> S12_DR
    S11_EP11 ==> S12_DR
    S12_DR ==> M3
```

---

## 6. Bảng Đối chiếu Truy vết Sơ đồ (`Traceability Mapping`)

| Mã Sơ đồ | Tên Sơ đồ | Yêu cầu Phi chức năng & Epic Liên kết | Tiêu chuẩn Kiểm chứng (`Section 08`) |
| :--- | :--- | :--- | :--- |
| **`DIAG-CIC-01`** | Đường ống CI/CD DevSecOps Tự động & Cổng Chất lượng | `NFR-MNT-01`, `NFR-MNT-02`, `NFR-SEC-14`, `EN-01` | 100% PR chạy qua SonarQube, Snyk, Unit Test (`>= 80%`), Pact Contract Test và Trivy Scan trước khi lên ECR. |
| **`DIAG-CIC-02`** | Chiến lược Triển khai Zero-Downtime (`Blue/Green & Canary`) | `NFR-MNT-03`, `NFR-AVL-01`, `EP-04`, `EP-05` | Dịch chuyển lưu lượng `10% -> 50% -> 100%`; kích hoạt Auto-Rollback `< 30 giây` khi tỷ lệ `HTTP 5xx > 0.5%`. |
| **`DIAG-CIC-03`** | Nâng cấp Cơ sở Dữ liệu Không Khóa Bảng (`Expand-and-Contract`) | `NFR-MNT-03`, `NFR-PER-05` | Thực hiện 3 pha `Expand -> Backfill -> Contract`; tạo chỉ mục với `ONLINE = ON` không gây khóa giao dịch. |
| **`DIAG-CIC-04`** | Sơ đồ Đường găng Dự án (`Critical Path DAG`) & Lộ trình 12 Sprints | `EP-01..EP-11`, `EN-01..EN-04` | Tuân thủ đúng thứ tự phụ thuộc kỹ thuật qua 3 cột mốc `R1.0` (Sprint 5), `R1.5` (Sprint 8), `R2.0` (Sprint 12). |

