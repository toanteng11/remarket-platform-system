# Sơ Đồ Kiến Trúc Cơ Sở Dữ Liệu, ERD, Sharding, CQRS & Caching – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Tài liệu | Tập hợp Sơ đồ Kiến trúc Polyglot Persistence, Dual-Key ERD, MongoDB Schemas, CQRS, Sharding & Caching |
| Mã tài liệu | `DOC-BA-09-DBA` |
| Phiên bản | 1.0 |
| Trạng thái | Hoàn thiện (Baseline Kiến trúc Dữ liệu Phân tán) |
| Ngày cập nhật | 12/11/2026 |
| Phân hệ cha | [docs/BA/09_architecture-and-process-diagrams/README.md](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md) |
| Tài liệu liên quan | [entity-relationship-model.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/entity-relationship-model.md), [polyglot-persistence-and-db-architecture.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/polyglot-persistence-and-db-architecture.md), [sharding-and-partitioning-strategy.md](file:///d:/remarket-platform-system/docs/BA/05_domain-and-data/sharding-and-partitioning-strategy.md) |

---

## 1. Sơ Đồ Kiến Trúc Lưu Trữ Đa Mô Hình (`DIAG-DBA-01`: Polyglot Persistence Topology)

Sơ đồ minh họa nguyên tắc **Database-per-Service** và lựa chọn công nghệ lưu trữ tối ưu theo đặc thù tải của từng miền (`DEC-DIR-01`, `DEC-DIR-02`, `DEC-DIR-03`):

```mermaid
flowchart TB
    subgraph RELATIONAL_TIER ["1. TẦNG QUAN HỆ GIAO DỊCH ACID (AWS RDS FOR SQL SERVER MULTI-AZ)"]
        direction LR
        IDN_DB[("IdentityDB (SQL Server)\nSở hữu: SVC-01 Identity\n- Users, Roles, UserRoles\n- SellerProfiles, KYC\n- UserAuditLogs")]
        ORD_DB[("OrderDB (SQL Server)\nSở hữu: SVC-05 Order\n- Orders, OrderItems\n- OrderReservations (INV-01)\n- OrderStatusAudit, OutboxMessages")]
        PAY_DB[("PaymentDB (SQL Server)\nSở hữu: SVC-06 Payment\n- PaymentIntents, Transactions\n- EscrowHoldings (INV-02/04)\n- PayoutLedgers, ReconBatches")]
    end

    subgraph DOCUMENT_TIER ["2. TẦNG TÀI LIỆU ĐA HÌNH & DÒNG SỰ KIỆN (MONGODB ATLAS REPLICA SET)"]
        direction LR
        CAT_DB[("CatalogDB (MongoDB)\nSở hữu: SVC-02 Catalog\n- listings (Polymorphic Attrs)\n- categories, category_schemas\n- prohibited_keywords, outbox")]
        SHP_DB[("ShippingDB (MongoDB)\nSở hữu: SVC-07 Shipping\n- shipments, waybills\n- tracking_events (Bucket Pattern)\n- delivery_evidences, carrier_recon")]
        SUP_DB[("SupportDB (MongoDB)\nSở hữu: SVC-08 Trust & Support\n- dispute_cases, moderation_tasks\n- support_tickets, order_reviews\n- order_360_view (CQRS Read Model)")]
        NTF_DB[("NotificationDB (MongoDB)\nSở hữu: SVC-09 Notification\n- notifications (TTL 90d)\n- user_preferences\n- templates")]
    end

    subgraph DERIVED_AND_OBJECT_TIER ["3. TẦNG BỘ NHỚ ĐỆM, TÌM KIẾM DẪN XUẤT & LƯU TRỮ ĐỐI TƯỢNG"]
        direction LR
        REDIS_CL[("Amazon ElastiCache Redis Cluster\n- Tầng 1 Lock: lock:listing:{id} NX EX 900\n- Rate Limiting Counters\n- Read-Aside Cache (TTL 300s)")]
        OS_CL[("Amazon OpenSearch Cluster\n- Sở hữu: SVC-04 Search\n- Chỉ mục: listings_index_v1\n- Bộ phân tích: vi_analyzer (NRT < 2s)")]
        S3_VAULT[("Amazon S3 Object Storage\n- Bucket 1: remarket-media-prod (WebP/Video)\n- Bucket 2: remarket-evidence-vault (KMS)\n- Bucket 3: remarket-audit-worm (Object Lock)")]
    end

    RELATIONAL_TIER -.->|"Debezium CDC / Outbox Events\n(Tuyệt đối không Join chéo DB)"| DOCUMENT_TIER
    RELATIONAL_TIER & DOCUMENT_TIER -.->|"Near-Real-Time Projection"| DERIVED_AND_OBJECT_TIER

    classDef sql fill:#e3f2fd,stroke:#1565c0,stroke-width:2px;
    classDef mongo fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef drv fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    class IDN_DB,ORD_DB,PAY_DB sql;
    class CAT_DB,SHP_DB,SUP_DB,NTF_DB mongo;
    class REDIS_CL,OS_CL,S3_VAULT drv;
```

---

## 2. Sơ Đồ Quan Hệ Thực Thể Chuẩn Dual-Key Cho SQL Server (`DIAG-DBA-02`: Relational ERD)

Sơ đồ `erDiagram` mô tả kiến trúc **Dual-Key (`InternalId BIGINT IDENTITY` làm Clustered Primary Key chống phân mảnh B-Tree + `PublicId UNIQUEIDENTIFIER UUIDv7` làm Non-Clustered Unique Index tiếp xúc ra API)** trên `IdentityDB`, `OrderDB` và `PaymentDB`:

```mermaid
erDiagram
    USERS ||--o{ USER_ADDRESSES : "has"
    USERS ||--o| SELLER_PROFILES : "upgrades_to"
    USERS {
        bigint InternalId PK "IDENTITY(1,1) Clustered"
        uniqueidentifier PublicId UK "UUIDv7 Public ID"
        varchar PhoneNumber UK "Encrypted / Indexed"
        varchar PasswordHash "BCrypt Cost 12"
        varchar AccountStatus "ACTIVE, SUSPENDED, BANNED"
        bit Is2FAEnabled "TOTP / SMS 2FA"
        rowversion ConcurrencyStamp "Optimistic Lock"
    }
    SELLER_PROFILES {
        bigint InternalId PK
        bigint UserInternalId FK
        varchar KycStatus "PENDING, VERIFIED, REJECTED"
        varchar BankAccountEncrypted "KMS Encrypted"
        decimal ReputationScore "Bayesian Score"
    }

    ORDERS ||--|| ORDER_ITEMS : "contains_single_item"
    ORDERS ||--o{ ORDER_RESERVATIONS : "guards_15m_hold"
    ORDERS ||--o{ ORDER_STATUS_AUDIT : "tracks_history"
    ORDERS {
        bigint InternalId PK "IDENTITY(1,1) Clustered"
        uniqueidentifier PublicId UK "UUIDv7 Order Code"
        uniqueidentifier BuyerPublicId "Logical Ref to IdentityDB"
        uniqueidentifier SellerPublicId "Logical Ref to IdentityDB"
        uniqueidentifier ListingPublicId "Logical Ref to CatalogDB"
        decimal ItemPriceVnd "DECIMAL(18,0)"
        decimal ShippingFeeVnd "DECIMAL(18,0)"
        decimal TotalAmountVnd "DECIMAL(18,0)"
        varchar OrderStatus "CREATED, AWAITING_PAYMENT, PAYMENT_ESCROWED, SHIPPING, DELIVERED, COMPLETED, DISPUTED, CANCELLED"
        datetime2 CreatedAtUtc "Partition Key Candidate"
        rowversion RowVersionStamp "Protects State Transitions"
    }
    ORDER_ITEMS {
        bigint InternalId PK
        bigint OrderInternalId FK
        nvarchar ListingSnapshotJson "Immutable Snapshot of Title, Condition, Images at Buy Time"
    }
    ORDER_RESERVATIONS {
        bigint InternalId PK
        bigint OrderInternalId FK
        uniqueidentifier ListingPublicId "Filtered Unique Index WHERE Status = ACTIVE"
        varchar ReservationStatus "ACTIVE, CONSUMED, EXPIRED, RELEASED"
        datetime2 ExpiresAtUtc "CreatedAt + 15 Minutes"
    }

    PAYMENT_INTENTS ||--o{ PAYMENT_TRANSACTIONS : "records_attempts"
    PAYMENT_INTENTS ||--o| ESCROW_HOLDINGS : "locks_funds"
    ESCROW_HOLDINGS ||--o{ PAYOUT_LEDGERS : "settles_or_refunds"
    PAYMENT_INTENTS {
        bigint InternalId PK
        uniqueidentifier PublicId UK "UUIDv7"
        uniqueidentifier OrderPublicId UK "Logical Ref to OrderDB"
        varchar IdempotencyKey UK "Prevents Duplicate Intent"
        decimal AmountVnd "DECIMAL(18,0)"
        varchar ProviderCode "VNPAY, MOMO, VIETQR"
        varchar IntentStatus "CREATED, CAPTURED, EXPIRED, FAILED"
    }
    PAYMENT_TRANSACTIONS {
        bigint InternalId PK
        bigint PaymentIntentInternalId FK
        varchar ProviderTxnRef UK "Unique Index for Webhook Idempotency (INV-02)"
        varchar TxnType "CAPTURE, AUTO_REFUND, DISPUTE_REFUND"
        varchar TxnStatus "SUCCEEDED, FAILED"
        datetime2 ProcessedAtUtc
    }
    ESCROW_HOLDINGS {
        bigint InternalId PK
        bigint PaymentIntentInternalId FK
        uniqueidentifier OrderPublicId UK
        decimal HeldAmountVnd "DECIMAL(18,0)"
        varchar EscrowStatus "CAPTURED, FROZEN_BY_DISPUTE, RELEASED, REFUNDED, SPLIT_SETTLED"
        datetime2 AutoReleaseAfterUtc "DeliveredAt + 72 Hours"
        rowversion RowVersionStamp "Protects INV-04 Freeze"
    }
    PAYOUT_LEDGERS {
        bigint InternalId PK
        bigint EscrowInternalId FK
        varchar EntryDirection "DEBIT_ESCROW, CREDIT_SELLER, CREDIT_BUYER_REFUND, CREDIT_PLATFORM_FEE"
        decimal EntryAmountVnd "Double-Entry Bookkeeping (Sum = 0)"
        datetime2 CreatedAtUtc
    }
```

---

## 3. Sơ Đồ Cấu Trúc Document Schema & Hybrid Pattern Cho MongoDB (`DIAG-DBA-03`)

Sơ đồ mô tả cách thiết kế các Collection trên MongoDB Atlas để vừa tận dụng tính linh hoạt của JSON Schema đa hình (Polymorphic Attributes cho đồ cũ), vừa tránh lỗi phình to kích thước Document vượt giới hạn 16MB (nhờ Bucket & Outlier Pattern):

```mermaid
flowchart TB
    subgraph CATALOG_MONGO ["1. CatalogDB (MongoDB Atlas)"]
        LST_DOC["Collection: listings\n- _id: ObjectId\n- public_id: UUIDv7 (Unique Index)\n- seller_id: UUIDv7\n- category_id: String\n- status: ACTIVE | RESERVED | SOLD\n- inventory: 1 (Const)\n- price_vnd: NumberLong\n- condition_grade: NEW_99 | GOOD | FAIR\n- attributes: { battery_health: 89, imei_masked: '...456' }\n- media_embedded: [Top 10 Image URLs]\n- version: Int32 (Optimistic Concurrency)"]
        CAT_DOC["Collection: categories & category_schemas\n- _id: 'cat_smartphone'\n- ancestor_path: ['electronics', 'smartphone']\n- required_attributes_json_schema: {...}"]
        LST_DOC -->|"Tham chiếu Schema động"| CAT_DOC
    end

    subgraph SHIPPING_MONGO ["2. ShippingDB (MongoDB Atlas - Bucket Pattern)"]
        SHP_DOC["Collection: shipments\n- _id: ObjectId\n- public_id: UUIDv7\n- order_public_id: UUIDv7 (Unique Index)\n- carrier_code: GHN | GHTK\n- tracking_code: String (Indexed)\n- canonical_status: IN_TRANSIT | DELIVERED\n- pod_evidence: { image_s3_key, signed_by, delivered_at }"]
        TRK_BUCKET["Collection: shipment_tracking_buckets\n(Bucket Pattern: Gom tối đa 50 sự kiện/bucket)\n- shipment_id: UUIDv7\n- bucket_seq: 1\n- events_count: 14\n- events: [ { status, location, timestamp, raw_payload } ]"]
        SHP_DOC -->|"1-to-Many Buckets"| TRK_BUCKET
    end

    subgraph SUPPORT_MONGO ["3. SupportDB (MongoDB Atlas - CQRS & Governance)"]
        DSP_DOC["Collection: dispute_cases\n- public_id: UUIDv7\n- order_public_id: UUIDv7 (Unique Index)\n- status: OPENED | FROZEN | RESOLVED\n- buyer_evidences: [S3 Video/Image Keys]\n- seller_evidences: [S3 Video Keys]\n- arbitration_verdict: { type, buyer_refund_vnd, seller_payout_vnd }"]
        O360_DOC["Collection: order_360_view (CQRS Read Model)\n- _id: order_public_id\n- order_summary: {...}\n- payment_escrow_timeline: [...]\n- shipment_checkpoints: [...]\n- active_disputes_and_tickets: [...]\n- last_projected_at: ISODate"]
    end

    classDef cat fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef shp fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef sup fill:#fff3e0,stroke:#ef6c00,stroke-width:1.5px;
    class LST_DOC,CAT_DOC cat;
    class SHP_DOC,TRK_BUCKET shp;
    class DSP_DOC,O360_DOC sup;
```

---

## 4. Sơ Đồ Kiến Trúc Tách Đọc/Ghi CQRS Read Model (`DIAG-DBA-04`: CQRS Synchronization Pipeline)

Sơ đồ minh họa nguyên tắc **`DEC-DIR-06`**: Dữ liệu giao dịch gốc (Source of Truth) nằm ở `OrderDB`, `PaymentDB`, `CatalogDB`; dữ liệu phục vụ tìm kiếm (`OpenSearch`) và tra cứu hợp nhất CSKH (`Order360View`) là dữ liệu dẫn xuất bất đồng bộ (Derived Read Models):

```mermaid
flowchart LR
    subgraph WRITE_SIDE ["1. NHÁNH GHI CHUẨN (COMMAND / WRITE SIDE - SOURCE OF TRUTH)"]
        CMD1["SVC-02 Catalog\n(Ghi CatalogDB)"]
        CMD2["SVC-05 Order\n(Ghi OrderDB)"]
        CMD3["SVC-06 Payment\n(Ghi PaymentDB)"]
        CMD4["SVC-07 Shipping\n(Ghi ShippingDB)"]
    end

    subgraph CDC_STREAM ["2. TRỤC TRUYỀN SỰ KIỆN THAY ĐỔI (OUTBOX & CDC)"]
        OUT["Transactional Outbox\n& Debezium Connector"] --> BUS["Apache Kafka\n(Partitioned by ListingId / OrderId)\nĐảm bảo đúng thứ tự sự kiện"]
    end

    subgraph READ_SIDE ["3. NHÁNH ĐỌC TỐI ƯU HÓA (QUERY / READ SIDE - DERIVED MODELS)"]
        PROJ_SRC["Search Indexer Worker\n(SVC-04 Search)"] --> OS_IDX[("OpenSearch: listings_index_v1\n- Phục vụ Buyer tìm kiếm < 200ms\n- Độ trễ đồng bộ NRT < 2 giây")]
        PROJ_360["Order360 Materializer Worker\n(SVC-08 Trust & Support)"] --> MONGO_360[("SupportDB: order_360_view\n- Phục vụ CSKH/Admin tra cứu < 400ms\n- Gom đủ 5 dịch vụ vào 1 Document")]
    end

    CMD1 & CMD2 & CMD3 & CMD4 --> OUT
    BUS --> PROJ_SRC & PROJ_360

    classDef wr fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    classDef bus fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef rd fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class CMD1,CMD2,CMD3,CMD4 wr;
    class OUT,BUS bus;
    class PROJ_SRC,OS_IDX,PROJ_360,MONGO_360 rd;
```

---

## 5. Sơ Đồ Chiến Lược Phân Mảnh Ngang Khi Vượt Ngưỡng 2TB (`DIAG-DBA-05`: Horizontal Sharding Architecture)

Sơ đồ mô tả kiến trúc mở rộng quy mô ngang ở **Release 3** khi dung lượng `OrderDB`/`PaymentDB` vượt ngưỡng **2TB** hoặc thông lượng vượt **5.000 TPS**:

```mermaid
flowchart TB
    APP_REQ["Yêu cầu Đọc/Ghi từ SVC-05 Order Service\nhoặc SVC-06 Payment Service"] --> ROUTER

    subgraph SHARD_ROUTER_LAYER ["TẦNG ĐỊNH TUYẾN PHÂN MẢNH (APPLICATION SHARD ROUTER & LOOKUP)"]
        ROUTER["Consistent Hashing Shard Router\nHashFunc = MurmurHash3(OrderPublicId) mod N_VNodes"]
        LOOKUP_CACHE[("Redis Cross-Shard Lookup Cache\nÁnh xạ: BuyerId / SellerId -> Danh sách [OrderPublicId, ShardId]")]
        ROUTER <--> LOOKUP_CACHE
    end

    subgraph SQL_SHARDS ["CỤM PHÂN MẢNH CƠ SỞ DỮ LIỆU QUAN HỆ (SQL SERVER SHARDED CLUSTERS)"]
        SHARD_01[("SQL Shard 01 (Multi-AZ)\nLưu trữ VNodes: 0000 - 0341\n(Orders, Reservations, Payments)")]
        SHARD_02[("SQL Shard 02 (Multi-AZ)\nLưu trữ VNodes: 0342 - 0682\n(Orders, Reservations, Payments)")]
        SHARD_03[("SQL Shard 03 (Multi-AZ)\nLưu trữ VNodes: 0683 - 1023\n(Orders, Reservations, Payments)")]
    end

    subgraph MONGO_SHARDS ["CỤM PHÂN MẢNH TÀI LIỆU (MONGODB ATLAS SHARDED CLUSTER)"]
        MONGOS["mongos Query Router"] --> M_SHARD1[("Mongo Shard A\nHashed Shard Key: { public_id: 'hashed' }")]
        MONGOS --> M_SHARD2[("Mongo Shard B\nHashed Shard Key: { public_id: 'hashed' }")]
    end

    ROUTER -->|"OrderPublicId băm vào VNode 0120"| SHARD_01
    ROUTER -->|"OrderPublicId băm vào VNode 0510"| SHARD_02
    ROUTER -->|"OrderPublicId băm vào VNode 0890"| SHARD_03

    classDef rtr fill:#fff9c4,stroke:#f57f17,stroke-width:2px;
    classDef sql fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef mgo fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class ROUTER,LOOKUP_CACHE rtr;
    class SHARD_01,SHARD_02,SHARD_03 sql;
    class MONGOS,M_SHARD1,M_SHARD2 mgo;
```

---

## 6. Sơ Đồ Chiến Lược Bộ Nhớ Đệm Đa Tầng & Xóa Cache Tức Thời (`DIAG-DBA-06`: Multi-Tier Caching)

Sơ đồ mô tả 3 tầng bộ nhớ đệm và cơ chế chống đọc dữ liệu cũ (Stale Read Prevention) đối với trạng thái tin đăng đồ cũ đơn chiếc:

```mermaid
flowchart TB
    USER(("Người Dùng")) --> L1_CDN["Tầng 1: Amazon CloudFront CDN\n- Cache ảnh/video S3: TTL 365 ngày (Immutable Versioned URL)\n- Cache trang tĩnh / Category Tree: TTL 300s"]

    L1_CDN --> L2_GW["Tầng 2: API Gateway & BFF Response Cache\n- Cache danh sách gợi ý trang chủ: TTL 60s\n- Tuyệt đối KHÔNG cache API Checkout / Payment"]

    L2_GW --> SVC_APP["Tầng 3: Microservices + ElastiCache Redis Cluster\n- Cache-Aside cho Chi tiết tin đăng (listing:detail:{id}, TTL 300s)\n- Cache-Aside cho Từ điển từ khóa cấm (prohibited_keywords, TTL 3600s)\n- Distributed Lock cho Giữ hàng 15p (lock:listing:{id}, TTL 900s)"]

    SVC_APP -->|"Cache Miss"| DB_SOT[("Cơ Sở Dữ Liệu Nguồn Chuẩn (Source of Truth)\nCatalogDB / OrderDB")]

    subgraph INVALIDATION_LOOP ["CƠ CHẾ XÓA CACHE TỨC THỜI KHI ĐỔI TRẠNG THÁI (EVENT-DRIVEN INVALIDATION)"]
        DB_SOT -->|"Khi Listing chuyển sang RESERVED hoặc SOLD"| EVT_CHG["Phát sự kiện EVT-04 / EVT-06\n(hoặc Write-Around Invalidation)"]
        EVT_CHG -->|"Xóa ngay lập tức (< 50ms): DEL listing:detail:{id}"| SVC_APP
    end

    classDef cache fill:#e1f5fe,stroke:#0288d1,stroke-width:1.5px;
    classDef sot fill:#f3e5f5,stroke:#6a1b9a,stroke-width:2px;
    classDef inv fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    class L1_CDN,L2_GW,SVC_APP cache;
    class DB_SOT sot;
    class EVT_CHG inv;
```

---

## 7. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Kiến trúc Dữ liệu & DBA | Khởi tạo hoàn chỉnh 6 sơ đồ kiến trúc cơ sở dữ liệu, Dual-Key ERD, MongoDB Schemas, CQRS, Sharding và Multi-Tier Caching (`DIAG-DBA-01..06`). |
