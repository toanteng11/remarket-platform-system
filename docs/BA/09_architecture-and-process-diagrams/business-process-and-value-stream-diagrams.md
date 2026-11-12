# Sơ Đồ Luồng Nghiệp Vụ & Chuỗi Giá Trị Đầu-Cuối (Business Process & Value Stream Diagrams) – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket – Nền tảng Thương mại Điện tử Đồ cũ C2C/B2C Phân tán |
| Tài liệu | Tập hợp Sơ đồ Chuỗi Giá trị (`VS-01..06`) & Các Luồng Quy trình Nghiệp vụ Trọng yếu (`UC-01..26`) |
| Mã tài liệu | `DOC-BA-09-BIZ` |
| Phiên bản | 1.0 |
| Trạng thái | Hoàn thiện (Baseline Luồng Nghiệp vụ Đầu-Cuối) |
| Ngày cập nhật | 12/11/2026 |
| Phân hệ cha | [docs/BA/09_architecture-and-process-diagrams/README.md](file:///d:/remarket-platform-system/docs/BA/09_architecture-and-process-diagrams/README.md) |
| Tài liệu liên quan | [use-cases.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/use-cases.md), [business-rules.md](file:///d:/remarket-platform-system/docs/BA/02_business-analysis/business-rules.md), [story-mapping-and-delivery-roadmap.md](file:///d:/remarket-platform-system/docs/BA/07_backlog-and-release/story-mapping-and-delivery-roadmap.md) |

---

## 1. Sơ Đồ Chuỗi Giá Trị Đầu-Cuối 6 Chặng (`DIAG-BIZ-01`: End-to-End Value Stream Map `VS-01..06`)

Sơ đồ mô tả bức tranh tổng thể 6 chặng dòng giá trị cốt lõi của ReMarket, kết nối hành trình của Người bán, Người mua và Đội ngũ Vận hành Sàn:

```mermaid
flowchart LR
    subgraph VS01 ["VS-01: ONBOARDING & ĐĂNG BÁN"]
        direction TB
        V1_1["1. Đăng ký / Đăng nhập OTP\n(US-ACC-01..04)"]
        V1_2["2. Định danh Seller KYC\n(US-ACC-05)"]
        V1_3["3. Tạo tin đồ cũ (Inventory = 1)\n& Upload ảnh S3 (US-LST-01..05)"]
        V1_4["4. Lọc từ cấm & Duyệt tin\n(US-LST-06..07)"]
        V1_1 --> V1_2 --> V1_3 --> V1_4
    end

    subgraph VS02 ["VS-02: KHÁM PHÁ & ĐÁNH GIÁ"]
        direction TB
        V2_1["1. Đồng bộ NRT OpenSearch < 2s\n(US-SRC-06)"]
        V2_2["2. Tìm kiếm tiếng Việt & Lọc đa diện\n(US-SRC-01..03)"]
        V2_3["3. Xem PDP, ảnh thật, tình trạng lỗi\n& Huy hiệu Còn hàng (US-SRC-04)"]
        V2_1 --> V2_2 --> V2_3
    end

    subgraph VS03 ["VS-03: CHECKOUT, KHÓA 15P & ESCROW"]
        direction TB
        V3_1["1. Bấm Mua Ngay & Tính phí ship 3PL\n(US-CHK-01..03)"]
        V3_2["2. Khóa Giữ Chỗ Hai Tầng 15 Phút\n(INV-01 - US-CHK-04..06)"]
        V3_3["3. Thanh toán PSP & Lưu Ký quỹ Escrow\n(INV-02 - US-PAY-01..04)"]
        V3_1 --> V3_2 --> V3_3
    end

    subgraph VS04 ["VS-04: ĐÓNG GÓI & GIAO VẬN 3PL"]
        direction TB
        V4_1["1. Seller xác nhận đóng gói (< 48h)\n(US-CHK-09)"]
        V4_2["2. Tạo vận đơn GHN/GHTK & In nhãn\n(US-SHP-01..02)"]
        V4_3["3. Bưu tá lấy hàng & Cập nhật Tracking\n(US-SHP-03)"]
        V4_4["4. Giao thành công kèm ảnh POD\n(US-SHP-04)"]
        V4_1 --> V4_2 --> V4_3 --> V4_4
    end

    subgraph VS05 ["VS-05: HOÀN TẤT & QUYẾT TOÁN T+3"]
        direction TB
        V5_1["1. Buyer xác nhận nhận hàng\nhoặc hết 72h không khiếu nại (US-CHK-11)"]
        V5_2["2. Tự động giải ngân tiền về NH Seller\n(US-PAY-08)"]
        V5_3["3. Đánh giá uy tín sau mua\n(US-TRU-03, 07)"]
        V5_1 --> V5_2 --> V5_3
    end

    subgraph VS06 ["VS-06: NGOẠI LỆ, TRANH CHẤP & HỖ TRỢ"]
        direction TB
        V6_1["1. Mở khiếu nại trong 72h & Đóng băng tiền\n(INV-04 - US-DSP-01..02)"]
        V6_2["2. Trọng tài phán quyết & Chia/Hoàn tiền\n(US-DSP-03..04)"]
        V6_3["3. Tra cứu CSKH Order 360° & Đối soát 3 bên\n(US-SUP-03, US-PAY-06)"]
        V6_1 --> V6_2 --> V6_3
    end

    VS01 --> VS02 --> VS03 --> VS04 --> VS05
    VS03 -.->|"Thanh toán trễ / Hủy"| VS06
    VS04 -.->|"Giao thất bại / Hàng lỗi"| VS06
    VS06 -.->|"Phán quyết giải ngân"| VS05

    classDef vs1 fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef vs2 fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef vs3 fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef vs4 fill:#f3e5f5,stroke:#6a1b9a,stroke-width:1.5px;
    classDef vs5 fill:#c8e6c9,stroke:#1b5e20,stroke-width:2px;
    classDef vs6 fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    class V1_1,V1_2,V1_3,V1_4 vs1;
    class V2_1,V2_2,V2_3 vs2;
    class V3_1,V3_2,V3_3 vs3;
    class V4_1,V4_2,V4_3,V4_4 vs4;
    class V5_1,V5_2,V5_3 vs5;
    class V6_1,V6_2,V6_3 vs6;
```

---

## 2. Sơ Đồ Luồng Đăng Tin Đồ Cũ, Tải Ảnh S3, Quét Mã Độc & Kiểm Duyệt (`DIAG-BIZ-02`: `UC-05..08`)

Sơ đồ Sequence chi tiết hóa quy trình từ lúc Người bán tạo tin đăng với thuộc tính động theo danh mục, tải ảnh trực tiếp lên Amazon S3 qua Presigned URL, quét virus bằng ClamAV, kiểm tra từ khóa cấm đến khi đồng bộ chỉ mục tìm kiếm OpenSearch dưới 2 giây:

```mermaid
sequenceDiagram
    autonumber
    actor Seller as Người Bán (SCR-SEL-02)
    participant GW as SVC-00: API Gateway
    participant CatSvc as SVC-02: Catalog Service
    participant MedSvc as SVC-03: Media Service
    participant S3 as Amazon S3 & ClamAV
    participant TruSvc as SVC-08: Trust & Moderation
    participant Kafka as Kafka / EventBridge
    participant SrcSvc as SVC-04: Search Service (OpenSearch)

    Seller->>GW: 1. Chọn Danh mục (VD: Điện thoại cũ) -> GET /api/v1/categories/{id}/schema
    GW->>CatSvc: Lấy cấu trúc JSON Schema thuộc tính bắt buộc (Pin, iCloud, IMEI)
    CatSvc-->>Seller: Trả về biểu mẫu thuộc tính động

    Seller->>GW: 2. Yêu cầu tải 5 ảnh thật -> POST /api/v1/media/presign
    GW->>MedSvc: Kiểm tra định dạng (JPEG/PNG/WebP <= 10MB)
    MedSvc-->>Seller: Trả về Presigned PUT URLs (Hiệu lực 5 phút)

    Seller->>S3: 3. Upload trực tiếp tệp ảnh lên S3 Bucket (Direct-to-S3)
    S3->>MedSvc: 4. S3 Event Notification kích hoạt ClamAV Malware Scan & Nén WebP
    alt Phát hiện tệp chứa mã độc / Virus
        MedSvc->>S3: Xóa ngay lập tức tệp nhiễm độc trên S3
        MedSvc-->>Seller: Thông báo lỗi HTTP 422 (Tệp không an toàn)
    else Ảnh sạch & Nén thành công 3 kích thước (Thumb, Medium, Full)
        MedSvc->>Kafka: Phát sự kiện EVT-07 MediaUploadedEvent
    end

    Seller->>GW: 5. Gửi thông tin tin đăng -> POST /api/v1/listings (Inventory = 1)
    GW->>CatSvc: Kiểm tra JSON Schema & Đối chiếu Từ điển Từ khóa Cấm (Redis Cache)

    alt Chứa từ khóa cấm nghiêm trọng (Vũ khí, Hàng cấm)
        CatSvc->>CatSvc: Lưu trạng thái REJECTED
        CatSvc-->>Seller: Từ chối ngay lập tức kèm lý do vi phạm chính sách
    else Chứa dấu hiệu nghi vấn (Giá rẻ bất thường / Nhãn hiệu nhạy cảm)
        CatSvc->>CatSvc: Lưu trạng thái PENDING_REVIEW
        CatSvc->>Kafka: Phát sự kiện tạo ModerationTask cho Moderator (SCR-ADM-03)
        CatSvc-->>Seller: Thông báo tin đang chờ chuyên viên phê duyệt
    else Tin đăng hợp lệ 100%
        CatSvc->>CatSvc: Lưu CatalogDB (STATUS = ACTIVE, Version = 1)
        CatSvc->>Kafka: 6. Phát sự kiện EVT-03 ListingPublishedEvent (qua Outbox)
        CatSvc-->>Seller: HTTP 201 Created (Tin đăng đã lên sàn)
        Kafka->>SrcSvc: 7. Đồng bộ Near-Real-Time (< 2 giây) vào OpenSearch Index
    end
```

---

## 3. Sơ Đồ Luồng Đặt Hàng Đơn Chiếc, Khóa 15 Phút & Thanh Toán Ký Quỹ (`DIAG-BIZ-03`: `UC-11..15`)

Sơ đồ mô tả luồng giao dịch cốt lõi nhất của ReMarket: Đặt mua món hàng đơn chiếc (`Inventory = 1`), kích hoạt khóa giữ chỗ 15 phút chống bán trùng (`INV-01`) và thanh toán qua cổng điện tử vào tài khoản ký quỹ (`INV-02`):

```mermaid
flowchart TD
    START(("Người Mua bấm\nMUA NGAY trên PDP\n(SCR-MKT-03)")) --> CHECK_SELF{"1. Kiểm tra Người mua\ncó trùng Người bán?"}
    CHECK_SELF -- "Trùng (Self-trading)" --> ERR_SELF["Chặn HTTP 400:\nKhông thể tự mua hàng của mình"]
    CHECK_SELF -- "Hợp lệ" --> QUOTE["2. Chọn địa chỉ nhận hàng\n& Tính phí ship 3PL tức thời\n(SCR-MKT-04)"]

    QUOTE --> CLICK_PAY["3. Người mua xác nhận Đặt hàng\n(Kèm Header Idempotency-Key)"]
    CLICK_PAY --> LOCK_TIER1{"4. Tầng 1 Lock (Redis):\nSET lock:listing:{id} NX EX 900"}

    LOCK_TIER1 -- "Thất bại (Key đã tồn tại)" --> ERR_409["Trả về HTTP 409 Conflict:\nMón hàng đang có người giữ chỗ\n(Bảo vệ INV-01)"]
    LOCK_TIER1 -- "Thành công" --> LOCK_TIER2{"5. Tầng 2 Lock (SQL Server):\nUPDLOCK + Kiểm tra RowVersion\n& Listing.STATUS == ACTIVE?"}

    LOCK_TIER2 -- "Thất bại" --> REL_REDIS["Xóa khóa Redis\n& Trả về HTTP 409 Conflict"]
    LOCK_TIER2 -- "Thành công" --> CREATE_ORD["6. Tạo Order (AWAITING_PAYMENT)\n+ OrderReservation (Hạn 15:00)\n+ Snapshot Giá & Thuộc tính"]

    CREATE_ORD --> SYNC_CAT["7. Cập nhật Listing.STATUS = RESERVED\n& Tạo PaymentIntent (HMAC-SHA512)"]
    SYNC_CAT --> REDIRECT_PSP["8. Điều hướng người mua sang\nCổng VNPAY / MoMo / VietQR\n(Đồng hồ đếm ngược 15:00 chạy)"]

    REDIRECT_PSP --> WAIT_WH{"9. Kết quả Thanh toán\ntrong vòng 15 phút?"}

    WAIT_WH -- "Webhook Thành công (<= 15p)" --> IDEM_CHK{"10. Kiểm tra Idempotent\nProcessedWebhooks?"}
    IDEM_CHK -- "Đã xử lý trước đó" --> ACK_200["Trả về HTTP 200 OK\n(Bỏ qua không cộng trùng tiền - INV-02)"]
    IDEM_CHK -- "Giao dịch mới hợp lệ" --> ESCROW_HOLD["11. Ghi sổ cái EscrowHoldings (CAPTURED)\n+ Chuyển Order sang PAYMENT_ESCROWED\n+ Chuyển Listing sang SOLD"]
    ESCROW_HOLD --> NOTIFY_SELLER["12. Thông báo Seller chuẩn bị đóng gói hàng\n(EVT-14 PaymentSucceededEvent)"]

    WAIT_WH -- "Quá hạn 15p không trả tiền\nhoặc Buyer bấm Hủy" --> EXPIRE_LOCK["13. Worker giải phóng khóa giữ chỗ:\n- Order -> CANCELLED_TIMEOUT\n- Listing -> ACTIVE trở lại sàn"]

    EXPIRE_LOCK -.->|"Ngoại lệ: Tiền về trễ ở phút thứ 16"| LATE_PAY["14. Ghi nhận EXPIRED_PAYMENT_CAPTURED\n& Kích hoạt Auto-Refund hoàn tiền 100%\nvề PSP trong 30 giây (INV-02)"]

    classDef pass fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef lock fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef err fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    classDef dec fill:#fff9c4,stroke:#f57f17,stroke-width:1.5px;
    class CREATE_ORD,ESCROW_HOLD,NOTIFY_SELLER pass;
    class LOCK_TIER1,LOCK_TIER2,SYNC_CAT lock;
    class ERR_SELF,ERR_409,REL_REDIS,EXPIRE_LOCK,LATE_PAY err;
    class CHECK_SELF,WAIT_WH,IDEM_CHK dec;
```

---

## 4. Sơ Đồ Luồng Giao Nhận 3PL, Bằng Chứng POD & Tự Động Giải Ngân T+3 (`DIAG-BIZ-04`: `UC-16..20`)

Sơ đồ mô tả luồng thực hiện đơn hàng từ lúc Người bán xác nhận đóng gói, tạo vận đơn tự động qua Adapter 3PL (GHN/GHTK), theo dõi hành trình bưu tá, xử lý giao thất bại/chuyển hoàn (RTS), đến khi tự động giải ngân tiền ký quỹ sau 72 giờ:

```mermaid
sequenceDiagram
    autonumber
    actor Seller as Người Bán (SCR-SEL-04)
    participant OrdSvc as SVC-05: Order Service
    participant ShpSvc as SVC-07: Shipping Service
    participant Carrier as Đối Tác 3PL (GHN / GHTK)
    actor Buyer as Người Mua (SCR-MKT-06/07)
    participant PaySvc as SVC-06: Payment & Escrow

    Note over Seller,OrdSvc: Đơn hàng ở trạng thái PAYMENT_ESCROWED (Hạn xác nhận 48h)
    alt Quá 48h Người bán không xác nhận đóng gói
        OrdSvc->>OrdSvc: Tự động chuyển Order -> CANCELLED_SELLER_OVERDUE
        OrdSvc->>PaySvc: Phát sự kiện EVT-11 kích hoạt Auto-Refund 100% cho Buyer
    else Người bán bấm "Xác nhận & Chuẩn bị hàng" đúng hạn
        Seller->>OrdSvc: POST /api/v1/orders/{id}/confirm-fulfillment
        OrdSvc->>ShpSvc: Phát sự kiện yêu cầu tạo vận đơn 3PL
        ShpSvc->>Carrier: Gọi API tạo vận đơn (Qua GHN/GHTK Adapter + Circuit Breaker)
        Carrier-->>ShpSvc: Trả về TrackingCode, Nhãn in PDF (Barcode/QR) & Lịch thu gom
        ShpSvc-->>Seller: Hiển thị nhãn vận đơn để in dán lên gói hàng
    end

    Carrier->>ShpSvc: 1. Bưu tá lấy hàng thành công -> Webhook: PICKED_UP
    ShpSvc->>OrdSvc: Cập nhật Order.STATUS = SHIPPING
    ShpSvc-->>Buyer: Đẩy thông báo hành trình thời gian thực (IN_TRANSIT -> OUT_FOR_DELIVERY)

    alt Giao hàng thất bại 3 lần liên tiếp (Không liên lạc được Buyer)
        Carrier->>ShpSvc: Webhook: DELIVERY_FAILED -> RETURNING_TO_SENDER (RTS)
        ShpSvc->>OrdSvc: Chuyển trạng thái xử lý chuyển hoàn RTS
        Carrier->>ShpSvc: Đã trả hàng lại cho Seller -> Webhook: RETURNED
        OrdSvc->>PaySvc: Hoàn tiền hàng cho Buyer (Trừ phí vận chuyển theo chính sách)
    else Giao hàng thành công cho Người mua
        Carrier->>ShpSvc: 2. Webhook: DELIVERED (Kèm ảnh chụp Bằng chứng POD & Chữ ký)
        ShpSvc->>OrdSvc: Cập nhật Order.STATUS = DELIVERED & Kích hoạt Timer 72 giờ
        OrdSvc-->>Buyer: Thông báo đã giao hàng (Mở quyền Xác nhận hoặc Khiếu nại trong 72h)
    end

    alt Buyer bấm "Đã nhận hàng đúng mô tả" sớm HOẶC hết 72h không có khiếu nại
        OrdSvc->>OrdSvc: 3. Chuyển Order.STATUS = COMPLETED
        OrdSvc->>PaySvc: Phát sự kiện EVT-12 OrderCompletedEvent
        PaySvc->>PaySvc: 4. Giải phóng EscrowHoldings -> Ghi sổ PayoutLedgers
        PaySvc-->>Seller: Chuyển khoản tiền bán hàng về Ngân hàng của Seller (T+3 Settlement)
    end
```

---

## 5. Sơ Đồ Luồng Khiếu Nại Tranh Chấp, Đóng Băng Ký Quỹ (`INV-04`) & Trọng Tài (`DIAG-BIZ-05`: `UC-25`)

Sơ đồ mô tả chi tiết cơ chế bảo vệ tiền người mua khi phát sinh tranh chấp hàng không đúng mô tả hoặc vỡ hỏng:

```mermaid
flowchart TB
    BUYER_ACT["1. Người mua phát hiện hàng lỗi/sai mô tả\ntrong vòng 72h sau khi DELIVERED\n(SCR-MKT-11)"] --> UPLOAD_EVID["2. Tải lên Video khui hộp liền mạch\n& Ảnh chụp lỗi lên S3 Evidence Vault"]
    UPLOAD_EVID --> SUBMIT_DSP["3. Gửi yêu cầu Mở Khiếu nại\n(POST /api/v1/disputes - US-DSP-01)"]

    SUBMIT_DSP --> FREEZE_IMM["4. BẤT BIẾN INV-04 KÍCH HOẠT TỨC THỜI (< 500ms):\n- Phát sự kiện EVT-22 DisputeOpenedEvent\n- Order.STATUS = DISPUTED\n- EscrowHoldings.STATUS = FROZEN\n- Đình chỉ ngay lập tức bộ đếm tự động giải ngân T+3"]

    FREEZE_IMM --> SELLER_RESP{"5. Người bán phản hồi\ntrong vòng 24 giờ?\n(SCR-SEL-06)"}

    SELLER_RESP -- "Seller đồng ý hoàn tiền ngay" --> EXEC_REFUND["Thực thi hoàn tiền cho Buyer"]
    SELLER_RESP -- "Seller từ chối hoặc quá hạn 24h" --> ARBITRATION["6. Chuyển lên Bàn Trọng Tài ReMarket\n(SCR-ADM-04 - US-DSP-03)"]

    ARBITRATION --> REVIEW_360["7. Trọng tài viên đối chiếu 3 nguồn bằng chứng:\n- Snapshot tin đăng & cam kết tình trạng ban đầu\n- Video đóng gói của Seller & Biên bản POD của 3PL\n- Video mở hộp của Buyer"]

    REVIEW_360 --> VERDICT{"8. Phán quyết cuối cùng\ncủa Trọng tài viên?"}

    VERDICT -- "Phương án A: Bác bỏ khiếu nại\n(Hàng đúng mô tả)" --> V_SELLER["Mở băng Escrow -> Giải ngân 100%\ncho Người bán (RESOLVED_RELEASE_SELLER)"]
    VERDICT -- "Phương án B: Hoàn tiền một phần\n(Lỗi nhẹ, Buyer giữ hàng)" --> V_SPLIT["Chia sổ cái Ký quỹ (Split Settlement):\nHoàn X% cho Buyer + Giải ngân (100-X)% cho Seller"]
    VERDICT -- "Phương án C: Hoàn tiền 100%\nkèm Trả hàng (Lỗi nghiêm trọng)" --> V_RETURN["Tạo vận đơn chiều về 3PL trả hàng cho Seller\n-> Khi Seller nhận lại hàng -> Hoàn 100% tiền cho Buyer"]

    EXEC_REFUND & V_SELLER & V_SPLIT & V_RETURN --> AUDIT_WORM["9. Lưu vết phán quyết bất biến vào Audit Log & S3 WORM\n(Bảo vệ INV-05 - US-DSP-04)"]

    classDef freeze fill:#ffebee,stroke:#c62828,stroke-width:2.5px;
    classDef arb fill:#fff3e0,stroke:#ef6c00,stroke-width:1.5px;
    classDef res fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class FREEZE_IMM freeze;
    class ARBITRATION,REVIEW_360 arb;
    class V_SELLER,V_SPLIT,V_RETURN,EXEC_REFUND,AUDIT_WORM res;
```

---

## 6. Sơ Đồ Luồng Hỗ Trợ Khách Hàng & Tra Cứu Hợp Nhất `Order 360°` (`DIAG-BIZ-06`: `UC-26`)

Sơ đồ minh họa cách nhân viên CSKH tra cứu toàn bộ lịch sử đơn hàng từ 5 dịch vụ phân tán trên một màn hình duy nhất (`SCR-ADM-07`) mà không gây tải lên CSDL giao dịch và bảo đảm che giấu thông tin nhạy cảm PII:

```mermaid
flowchart LR
    subgraph SOURCES ["5 NGUỒN DỮ LIỆU GỐC PHÂN TÁN (SOURCE OF TRUTH)"]
        S1["SVC-05 OrderDB\n(Trạng thái đơn & Khóa giữ chỗ)"]
        S2["SVC-06 PaymentDB\n(Giao dịch PSP, Ký quỹ, Hoàn tiền)"]
        S3["SVC-07 ShippingDB\n(Mã vận đơn, Hành trình 3PL, POD)"]
        S4["SVC-02 CatalogDB\n(Snapshot tin đăng & Ảnh gốc)"]
        S5["SVC-08 SupportDB\n(Lịch sử Ticket, Khiếu nại, Ghi chú)"]
    end

    subgraph CQRS_PIPELINE ["ĐƯỜNG ỐNG ĐỒNG BỘ BẤT ĐỒNG BỘ (CQRS MATERIALIZER)"]
        CDC["Transactional Outbox & Kafka Events\n(EVT-08..25)"] --> AGG["Order360 Projector Worker\n(SVC-08 Trust & Support)"]
        AGG --> VIEW[("SupportDB.order_360_view\n(MongoDB Read-Optimized Document)")]
    end

    subgraph BACKOFFICE ["GIAO DIỆN CHĂM SÓC KHÁCH HÀNG (SCR-ADM-07)"]
        AGENT["Nhân viên CSKH (STK-13)\nTra cứu theo OrderPublicId / SĐT"]
        MASK["Bộ lọc Phân quyền & Che chắn PII:\n- SĐT: 090***456\n- CCCD: ***789\n- Không hiển thị số tài khoản NH đầy đủ"]
        REVEAL["Nút 'Hiện thông tin (Break-Glass)':\nBắt buộc nhập lý do & Ghi vào AuditLog bất biến"]
    end

    S1 & S2 & S3 & S4 & S5 --> CDC
    VIEW -->|"Truy vấn < 400ms\n(Zero Load trên OrderDB/PaymentDB)"| MASK
    MASK --> AGENT
    AGENT -.->|"Khi cần liên hệ khẩn cấp"| REVEAL

    classDef src fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px;
    classDef cqrs fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef bo fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    class S1,S2,S3,S4,S5 src;
    class CDC,AGG,VIEW cqrs;
    class AGENT,MASK,REVEAL bo;
```

---

## 7. Lịch sử cập nhật

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| 1.0 | 12/11/2026 | Nhóm Phân tích Nghiệp vụ & Kiến trúc | Khởi tạo hoàn chỉnh 6 sơ đồ luồng nghiệp vụ và chuỗi giá trị đầu-cuối (`DIAG-BIZ-01..06`). |
