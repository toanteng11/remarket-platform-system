# Sơ Đồ Sao Lưu Phục Hồi Thảm Họa (DR/Backup) & Đối Soát Tài Chính 3 Bên – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Tài liệu | Tập hợp 5 Sơ đồ Sao lưu PITR Liên vùng (`INV-05`), Chuyển đổi Dự phòng Multi-AZ, Kịch bản Bấm giờ DR Drill, Đối soát 3 Bên & Đối soát Hậu Khôi phục |
| Mã tài liệu | `DOC-BA-09-DRR` |
| Phiên bản | 1.0 |
| Trạng thái | Hoàn thiện (Baseline Phục hồi Thảm họa & Kiểm toán Tài chính) |
| Ngày cập nhật | 12/11/2026 |
| Phân hệ cha | [docs/BA/09_architecture-and-process-diagrams/README.md](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md) |
| Tài liệu liên quan | [replication-high-availability-and-consistency.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/replication-high-availability-and-consistency.md), [data-lifecycle-and-retention.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/data-lifecycle-and-retention.md), [observability-tracing-and-reconciliation.md](file:///d:/remarket-platform-system/docs/BA/06_microservices-and-integrations/observability-tracing-and-reconciliation.md) |

---

## 1. Sơ Đồ Kiến Trúc Sao Lưu Tự Động PITR & Sao Chép Liên Vùng (`DIAG-DRR-01`: Bảo Vệ `INV-05`)

Sơ đồ mô tả kiến trúc sao lưu đa tầng tại Vùng chính **Singapore (`ap-southeast-1`)** và sao chép bất biến sang Vùng dự phòng **Tokyo (`ap-northeast-1`)** kết hợp khóa chống xóa mã hóa tống tiền **S3 Object Lock WORM (Write-Once-Read-Many)**:

```mermaid
flowchart LR
    subgraph PRIMARY_REGION ["VÙNG CHÍNH: AWS AP-SOUTHEAST-1 (SINGAPORE - PRODUCTION)"]
        direction TB
        RDS_PROD[("AWS RDS SQL Server Multi-AZ\n(OrderDB, PaymentDB, IdentityDB)\n- Full Snapshot hàng ngày 01:00 UTC\n- Transaction Log Backup mỗi 5 phút")]
        MONGO_PROD[("MongoDB Atlas Cluster\n(CatalogDB, ShippingDB, SupportDB)\n- Continuous Cloud Backup (Oplog Stream)\n- Snapshot mỗi 6 giờ")]
        S3_PROD[("Amazon S3 Production Buckets\n(Ảnh tin đăng, Video bằng chứng Dispute,\nLog kiểm toán WORM)")]

        AWS_BK_SG["AWS Backup Vault (Singapore)\nMã hóa bằng KMS Primary Key\n(Retention: 35 ngày PITR)"]
        RDS_PROD -->|"Tự động mỗi 5 phút"| AWS_BK_SG
    end

    subgraph DR_REGION ["VÙNG DỰ PHÒNG THẢM HỌA: AWS AP-NORTHEAST-1 (TOKYO - DR VAULT)"]
        direction TB
        AWS_BK_TK["AWS Backup Cross-Region Vault (Tokyo)\n- Bật Vault Lock (Compliance WORM Mode)\n- Không ai xóa được kể cả Root Account\n- Mã hóa bằng KMS DR Key độc lập"]
        MONGO_DR[("MongoDB Atlas Cross-Region Snapshot\nLưu trữ tại Tokyo Region")]
        S3_GLACIER[("Amazon S3 Glacier Deep Archive (Tokyo)\n- Cross-Region Replication (CRR)\n- S3 Object Lock Compliance Mode (5-10 năm)")]
    end

    AWS_BK_SG -->|"Sao chép Liên vùng Tự động\n(Async Cross-Region Copy)"| AWS_BK_TK
    MONGO_PROD -->|"Cross-Region Backup Copy"| MONGO_DR
    S3_PROD -->|"S3 Cross-Region Replication\n(< 15 phút)"| S3_GLACIER

    classDef prod fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef bk fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef dr fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    class RDS_PROD,MONGO_PROD,S3_PROD prod;
    class AWS_BK_SG bk;
    class AWS_BK_TK,MONGO_DR,S3_GLACIER dr;
```

### Bảng Cam kết Mục tiêu Phục hồi (`RPO` & `RTO`) theo Phân loại Dữ liệu:

| Phân loại Dữ liệu | Hệ Quản Trị | Cơ Chế Sao Lưu | Cam Kết RPO | Cam Kết RTO | Thời Hạn Lưu Giữ |
|---|---|---|:---:|:---:|---|
| **Hạng 1: Giao dịch & Tài chính** (`OrderDB`, `PaymentDB`, `IdentityDB`) | RDS SQL Server | Multi-AZ Sync + PITR Log 5m | **<= 5 phút** (Multi-AZ = 0s) | **<= 30 phút** (Multi-AZ < 60s) | 35 ngày PITR + 10 năm WORM |
| **Hạng 2: Tin đăng, Vận đơn & Tranh chấp** (`CatalogDB`, `ShippingDB`, `SupportDB`) | MongoDB Atlas | Replica Set 3 Nodes + Continuous Oplog | **<= 5 phút** | **<= 30 phút** | 30 ngày PITR + 5 năm Archive |
| **Hạng 3: Ảnh & Video Bằng chứng** (`S3 Media & Evidence`) | Amazon S3 | Versioning + CRR sang Tokyo | **<= 15 phút** | **<= 15 phút** | 5 năm (WORM cho Dispute) |
| **Hạng 4: Dữ liệu Dẫn xuất** (`OpenSearch`, `Redis Cache`) | OpenSearch / Redis | Rebuild từ Source of Truth / Snapshot | Tái tạo từ DB gốc | **<= 45 phút** | Không yêu cầu lưu lịch sử |

---

## 2. Sơ Đồ Chuyển Đổi Dự Phòng Tự Động Nội Vùng (`DIAG-DRR-02`: Multi-AZ High Availability Failover < 60s)

Sơ đồ mô tả cách hệ thống tự động vượt qua sự cố sập một Trung tâm dữ liệu (Availability Zone `AZ-a`) mà không cần con người can thiệp và không mất bất kỳ giao dịch nào (`RPO = 0`):

```mermaid
sequenceDiagram
    autonumber
    participant App as Microservices (AZ-b & AZ-c)
    participant DNS as RDS / Redis Endpoint DNS
    participant Primary as Node Chính (AZ-a - Gặp Sự Cố)
    participant Standby as Node Dự Phòng Đồng Bộ (AZ-b)
    participant Monitor as AWS Health Check & SRE Alert

    Note over App,Standby: Trạng thái Bình thường: Ghi đồng bộ (Synchronous Replication) từ AZ-a sang AZ-b
    App->>Primary: Thực thi Transaction Đặt hàng / Thanh toán
    Primary->>Standby: Đồng bộ Block/Log tức thời qua cáp quang nội vùng (< 2ms)
    Standby-->>Primary: ACK đã ghi đĩa an toàn
    Primary-->>App: Commit Transaction thành công

    Note over Primary,Monitor: SỰ CỐ XẢY RA: Đứt nguồn điện / Mạng tại AZ-a (Giây thứ 00)
    Monitor--xPrimary: Heartbeat Ping thất bại liên tiếp (Giây thứ 01 - 15)
    Monitor->>Standby: 1. Kích hoạt Automatic Failover: Thăng cấp Standby (AZ-b) lên làm Primary mới (Giây thứ 16 - 35)
    Monitor->>DNS: 2. Cập nhật bản ghi CNAME DNS trỏ sang IP của AZ-b (TTL = 5s) (Giây thứ 36 - 45)
    Monitor->>Monitor: 3. Gửi thông báo thông tin P2 về kênh Slack SRE

    App->>DNS: Polly Connection Retry tự động phân giải lại DNS (Giây thứ 46 - 55)
    DNS-->>App: Trả về địa chỉ IP của Primary mới tại AZ-b
    App->>Standby: Tiếp tục thực thi giao dịch bình thường (Giây thứ 58)
    Note over App,Standby: Tổng thời gian gián đoạn < 60 giây | Mất mát dữ liệu RPO = 0 byte
```

---

## 3. Sơ Đồ Quy Trình Bấm Giờ Khôi Phục Thảm Họa Toàn Vùng (`DIAG-DRR-03`: Stopwatch DR Restore Drill)

Sơ đồ mô tả quy trình khôi phục thảm họa theo kịch bản diễn tập thực tế (đã định nghĩa tại `08_validation-and-governance/non-functional-and-resilience-validation-plan.md`), chứng minh đạt chuẩn **`RPO <= 5 phút`** và **`RTO <= 30 phút`**:

```mermaid
flowchart TB
    subgraph PHASE_1 ["GIAI ĐOẠN 1: PHÁT HIỆN & CÔẬP LƯU LƯỢNG (PHÚT 00:00 -> 05:00)"]
        P1_1["Phút 00:00: Phát hiện thảm họa vùng\nIncident Commander (STK-18) kích hoạt DR Runbook"] --> P1_2["Phút 02:30: Bật chế độ Maintenance Mode tại CloudFront/WAF\nChặn phát sinh đơn mới để bảo toàn điểm khôi phục"]
        P1_2 --> P1_3["Phút 05:00: Chốt mốc thời gian khôi phục (Target Restore Point T - 2m)"]
    end

    subgraph PHASE_2 ["GIAI ĐOẠN 2: KHÔI PHỤC DỮ LIỆU SONG SONG (PHÚT 05:00 -> 21:00)"]
        P2_SQL["Nhánh A (Phút 05:00 -> 15:40):\nKhôi phục RDS SQL Server từ PITR Transaction Log\nKiểm tra toàn vẹn DBCC CHECKDB"]
        P2_MGO["Nhánh B (Phút 05:00 -> 18:20):\nKhôi phục MongoDB Atlas từ Continuous Oplog\nKiểm tra kết nối VPC Peering"]
        P2_ECS["Nhánh C (Phút 18:20 -> 21:00):\nCập nhật Endpoint DB mới vào Secrets Manager\n& Khởi động lại cụm 9 Microservices trên ECS/EKS"]
        P2_SQL & P2_MGO --> P2_ECS
    end

    subgraph PHASE_3 ["GIAI ĐOẠN 3: ĐỐI SOÁT HẬU KHÔI PHỤC & MỞ LẠI SÀN (PHÚT 21:00 -> 28:50)"]
        P3_RECON["Phút 21:00 -> 24:30: CHẠY JOB ĐỐI SOÁT HẬU KHÔI PHỤC\nTruy vấn API QueryDR của VNPAY/MoMo & API GHN/GHTK\nđể đồng bộ bù các giao dịch trong khoảng trống 2 phút RPO"]
        P3_SMOKE["Phút 24:30 -> 27:15: Chạy 15 kịch bản Smoke Test Tự động\n(Xác nhận 5 Bất biến INV-01..05 hoạt động chuẩn)"]
        P3_OPEN["Phút 28:50: SRE Lead (STK-18) & Finance Lead (STK-15)\nký xác nhận mở lại 100% lưu lượng tại API Gateway"]
        P3_RECON --> P3_SMOKE --> P3_OPEN
    end

    P1_3 --> P2_SQL & P2_MGO
    P2_ECS --> P3_RECON

    classDef p1 fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    classDef p2 fill:#fff3e0,stroke:#ef6c00,stroke-width:1.5px;
    classDef p3 fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    class P1_1,P1_2,P1_3 p1;
    class P2_SQL,P2_MGO,P2_ECS p2;
    class P3_RECON,P3_SMOKE,P3_OPEN p3;
```

---

## 4. Sơ Đồ Động Cơ Đối Soát Tài Chính 3 Bên Tự Động Lúc 02:00 Sáng (`DIAG-DRR-04`: 3-Way Financial Reconciliation)

Sơ đồ mô tả luồng vận hành của **Động cơ Đối soát Tài chính 3 Chiều (`FT-15`, `US-PAY-06, 09`)** đối chiếu từng đồng VNĐ giữa `OrderDB`, `PaymentDB` và File Quyết toán từ Cổng PSP / Sao kê Ngân hàng:

```mermaid
flowchart TB
    subgraph THREE_SOURCES ["3 NGUỒN DỮ LIỆU ĐỘC LẬP CẦN ĐỐI CHIẾU (T+1 AT 02:00 AM)"]
        SRC_ORD["Nguồn 1: SVC-05 OrderDB\nDanh sách Đơn hàng đã chuyển sang\nPAYMENT_ESCROWED / COMPLETED / REFUNDED\ntrong ngày T"]
        SRC_PAY["Nguồn 2: SVC-06 PaymentDB\nSổ cái PaymentTransactions,\nEscrowHoldings & PayoutLedgers\ntrong ngày T"]
        SRC_PSP["Nguồn 3: Đối tác PSP & Ngân hàng\nFile Settlement CSV/SFTP từ VNPAY/MoMo\n& Sao kê biến động số dư Ngân hàng"]
    end

    SRC_ORD & SRC_PAY & SRC_PSP --> ENGINE

    subgraph RECON_ENGINE ["ĐỘNG CƠ ĐỐI SOÁT 3 CHIỀU (3-WAY RECONCILIATION ENGINE)"]
        ENGINE["So khớp theo khóa: (OrderPublicId, ProviderTxnRef, AmountVnd, Status)"]
        ENGINE --> CHECK_MATCH{"Kết quả so khớp 3 bên?"}
    end

    CHECK_MATCH -- "Khớp tuyệt đối 100%\n(Order == Payment == PSP)" --> MATCHED["Đánh dấu RECONCILED_OK\nCập nhật Báo cáo Tài chính Sạch\n(Tỷ lệ kỳ vọng >= 99.8%)"]

    CHECK_MATCH -- "Lệch Loại 1: PSP đã thu tiền\nnhưng PaymentDB/OrderDB chưa ghi nhận\n(Do rớt mạng Webhook)" --> FIX_1["Tự động kích hoạt luồng bù:\n- Nếu đơn còn giữ được hàng: Chuyển sang PAID\n- Nếu đơn đã hết hạn 15p: Kích hoạt Auto-Refund ngay"]

    CHECK_MATCH -- "Lệch Loại 2: OrderDB báo PAID\nnhưng File PSP không có tiền\nhoặc Lệch số tiền > 0 VNĐ" --> FIX_2["CẢNH BÁO AN NINH & TÀI CHÍNH MỨC P1:\n1. Tự động ĐÓNG BĂNG giải ngân đơn hàng đó\n2. Tạo bản ghi ReconciliationDiscrepancies\n3. Bắn cảnh báo Slack/PagerDuty cho Finance Lead (STK-15)"]

    MATCHED & FIX_1 & FIX_2 --> DASHBOARD["Hiển thị trên Bảng Điều Khiển Đối Soát Ký Quỹ (SCR-ADM-06)\n& Lưu báo cáo kiểm toán vào S3 WORM Vault"]

    classDef src fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef eng fill:#fff9c4,stroke:#f57f17,stroke-width:2px;
    classDef ok fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef err fill:#ffebee,stroke:#c62828,stroke-width:2px;
    class SRC_ORD,SRC_PAY,SRC_PSP src;
    class ENGINE,CHECK_MATCH eng;
    class MATCHED,DASHBOARD ok;
    class FIX_1,FIX_2 err;
```

---

## 5. Sơ Đồ Quy Trình Đối Soát Bù Đắp Hậu Khôi Phục Thảm Họa (`DIAG-DRR-05`: Post-Restore State Reconciliation)

Sơ đồ giải quyết câu hỏi mở trọng yếu **`Q-DR-05`**: Khi khôi phục cơ sở dữ liệu từ bản sao lưu PITR (ví dụ bị lùi về quá khứ 2 phút), làm thế nào để đồng bộ lại trạng thái với Cổng thanh toán VNPAY/MoMo, Hãng vận chuyển GHN/GHTK và Amazon S3 trước khi mở lại sàn?

```mermaid
sequenceDiagram
    autonumber
    participant ReconJob as Post-Restore Recon Script
    participant SQL as OrderDB & PaymentDB (Vừa Restore)
    participant S3 as Amazon S3 Media & Evidence
    participant PSP as Cổng PSP (API QueryDR)
    participant Carrier as Đối tác 3PL (API Tracking)
    actor SRE as SRE Lead (STK-18) & Finance (STK-15)

    Note over ReconJob,SQL: Hệ thống đang ở chế độ Maintenance Mode (Chưa mở cho User)
    ReconJob->>SQL: 1. Quét toàn bộ các đơn/giao dịch tạo trong khoảng (T_crash - 30m) đến T_restore

    loop Kiểm tra từng PaymentIntent đang ở trạng thái CREATED / PENDING
        ReconJob->>PSP: 2. Gọi API QueryDR(PaymentIntentId) hỏi trạng thái thực tế tại cổng PSP
        alt Tại PSP khách đã thanh toán thành công trong 2 phút bị mất
            PSP-->>ReconJob: Trả về Status = PAID, ProviderTxnRef
            ReconJob->>SQL: Bù bản ghi vào PaymentTransactions & EscrowHoldings (Không để mất tiền khách)
        else Tại PSP giao dịch chưa thanh toán hoặc thất bại
            PSP-->>ReconJob: Trả về Status = UNPAID / EXPIRED
            ReconJob->>SQL: Đánh dấu hủy an toàn & Giải phóng khóa giữ hàng Listing
        end
    end

    loop Kiểm tra từng Shipment đang ở trạng thái READY_TO_SHIP / SHIPPING
        ReconJob->>Carrier: 3. Gọi API GetOrderInfo(TrackingCode) hỏi trạng thái mới nhất từ GHN/GHTK
        Carrier-->>ReconJob: Trả về trạng thái vận đơn hiện tại (VD: PICKED_UP / DELIVERED)
        ReconJob->>SQL: Đồng bộ cập nhật trạng thái đơn hàng khớp 100% với thực tế bưu cục
    end

    ReconJob->>S3: 4. Đối chiếu danh sách Media Keys giữa CatalogDB/SupportDB và S3 Bucket (US-OPS-10)
    S3-->>ReconJob: Xác nhận tính toàn vẹn đối tượng (Đánh dấu các tệp mồ côi nếu có)

    ReconJob-->>SRE: 5. Xuất Biên bản "Post-Restore Zero-Discrepancy Certificate" -> Cho phép mở lại sàn
```

---

## 6. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Kiến trúc Dữ liệu, SRE & Tài chính | Khởi tạo hoàn chỉnh 5 sơ đồ sao lưu PITR liên vùng, Multi-AZ Failover, DR Drill, Đối soát 3 bên và Đối soát hậu khôi phục (`DIAG-DRR-01..05`). |

