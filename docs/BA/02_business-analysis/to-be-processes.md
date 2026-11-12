# To-Be Processes – ReMarket

## Thông tin tài liệu

| Thuộc tính | Giá trị |
|---|---|
| Dự án | ReMarket |
| Phiên bản | 1.1 |
| Trạng thái | Mô hình mục tiêu đề xuất; chính sách sản phẩm và tích hợp còn mở |
| Ngày cập nhật | 12/11/2026 |
| Tài liệu liên quan | scope-and-boundaries.md, user-journeys.md, business-rules.md, business-scenarios-and-exceptions.md, open-questions-and-decisions.md |

## 1. Mục đích và nguyên tắc

Mô tả quy trình nghiệp vụ đích để phân tích yêu cầu và kiến trúc ReMarket. Đây là luồng logic độc lập với công nghệ; tên dịch vụ/dữ liệu chỉ giúp xác định trách nhiệm sơ bộ. Chưa phải đặc tả API, quyết định triển khai hoặc chính sách thương mại đã được phê duyệt.

### Nguyên tắc thiết kế quy trình

- Marketplace Web/App, Seller Center và Admin/Back-office là các kênh khác nhau; truy cập cùng năng lực backend theo quyền phù hợp.
- Mỗi hành động quan trọng cần phản hồi rõ trạng thái, lỗi, bước kế tiếp và mã tham chiếu khi cần hỗ trợ.
- Khi bắt đầu checkout, xác minh lại listing với năng lực sở hữu trạng thái khả dụng; không dựa duy nhất vào kết quả tìm kiếm/cache.
- Thể hiện trạng thái pending khi kết quả thanh toán hoặc provider chưa xác định; không suy đoán thất bại chỉ vì timeout.
- Ghi nhận lịch sử thay đổi cần cho hỗ trợ, an ninh và đối soát; hạn chế PII trong log/analytics.
- Sự kiện bất đồng bộ có thể trễ hoặc lặp; quy trình cần giữ idempotency và phát hiện trạng thái lệch.
- Thời hạn giữ hàng, COD, refund, hủy, giao nhận và dispute cần business owner quyết định.

## 2. P-01: onboarding và quản lý hồ sơ

**Tác nhân:** khách truy cập, buyer/seller, Identity & Access, User & Seller Profile, Admin khi cần kiểm tra bổ sung.

1. Người dùng chọn đăng ký/đăng nhập trên kênh được hỗ trợ.
2. Hệ thống xác thực theo chính sách; không lưu credential trong hồ sơ seller nếu identity được tách.
3. Người dùng cung cấp hồ sơ và thông tin cần cho hoạt động mua/bán.
4. Hệ thống kiểm tra trường bắt buộc và nêu lý do nếu chưa thể hoàn thành.
5. Tài khoản chuyển sang trạng thái phù hợp với quy trình xác minh/moderation.
6. Người dùng quản lý địa chỉ/thông tin liên hệ; quyền xem/chia sẻ dựa trên mục đích giao dịch.
7. Khi khóa, đóng hoặc xóa tài khoản, xử lý order đang mở, nghĩa vụ lưu trữ và yêu cầu pháp lý.

**Cần quyết định:** email/điện thoại/SSO; mức xác minh seller; tài khoản doanh nghiệp và nhiều nhân viên; xóa/anonymize dữ liệu khi còn order/case.

## 3. P-02: tạo, kiểm tra và công bố listing

**Tác nhân:** seller, Seller Center, Catalog & Listing, kho media (S3 là lựa chọn), Search & Discovery, Trust & Moderation nếu cần duyệt.

1. Seller bắt đầu tạo listing; hệ thống tạo draft thuộc seller đó.
2. Seller chọn danh mục, nhập title, mô tả, giá, tình trạng, thuộc tính, vị trí/điều kiện giao hàng.
3. Seller tải ảnh; hệ thống kiểm tra định dạng/kích thước/quyền truy cập và lưu media an toàn. Database giữ metadata/object key.
4. Hệ thống kiểm tra dữ liệu bắt buộc, nội dung bị cấm và tính hợp lệ theo danh mục.
5. Seller submit. Nếu policy yêu cầu duyệt trước, listing vào queue và chưa công khai.
6. Khi đạt điều kiện, listing chuyển published; seller nhận xác nhận và hướng dẫn chỉnh sửa.
7. Search nhận thay đổi để cập nhật chỉ mục; độ trễ đồng bộ được theo dõi.
8. Seller sửa listing; thay đổi nhạy cảm có thể phải review lại theo policy.
9. Seller tạm ẩn, gỡ hoặc đánh dấu đã bán nếu không xung đột reservation/order.
10. Thay đổi quản trị/moderation có lý do và audit.
11. Xóa ảnh/listing phải xét tới report, bằng chứng giao dịch và retention.

**Điểm kiểm soát:** listing có seller owner và trạng thái; không đủ điều kiện thì không thể mua. Giả định một listing là một món hàng đơn chiếc cần Product Owner xác nhận. Phân biệt thông tin seller cung cấp với điều đã được nền tảng xác minh.

## 4. P-03: buyer khám phá và quyết định mua

1. Buyer tìm từ khóa/danh mục và dùng bộ lọc.
2. Hệ thống hiển thị listing, tình trạng, ảnh, giá, seller, thời điểm cập nhật và trạng thái gần nhất.
3. Buyer xem chi tiết và có thể lưu/so sánh/liên hệ nếu tính năng thuộc phạm vi.
4. Trước giao dịch, hệ thống xác minh listing còn published, không bị block/reserved/sold và seller đủ điều kiện.
5. Nếu trạng thái vừa đổi, giải thích món không còn khả dụng và cho buyer tiếp tục tìm.
6. Hiển thị giá/phí dự kiến theo policy; phân biệt khoản đã xác định với khoản ước tính.
7. Không để cache hoặc search projection quyết định giao dịch cuối cùng.

## 5. P-04: Giỏ hàng, Checkout, Giữ hàng (Reservation) và Chống bán trùng

### 5.1. Luồng thực thi chi tiết
1. **Quản lý Giỏ hàng (Cart):** Buyer có thể lưu các món hàng quan tâm vào giỏ. Giỏ hàng chỉ lưu ý định mua phía client/service; hệ thống **KHÔNG khóa hay giữ hàng** khi sản phẩm chỉ mới nằm trong giỏ.
2. **Khởi tạo Checkout:** Khi Buyer bấm "Tiến hành thanh toán", hệ thống xác thực tài khoản, kiểm tra địa chỉ và phương thức giao hàng.
3. **Giữ hàng nguyên tử (Atomic Reservation):** Order Service gửi lệnh yêu cầu Listing Service thực hiện khóa tạm thời (Reserve) món hàng đơn chiếc với thời hạn (mặc định 15 phút).
4. **Xử lý cạnh tranh (Concurrency Control):** Nếu hai Buyer cùng checkout một món hàng, chỉ một yêu cầu khóa thành công. Buyer thứ hai nhận ngay thông báo món hàng đã không còn khả dụng và được gợi ý tìm sản phẩm tương tự.
5. **Snapshot dữ liệu đơn:** Sau khi giữ hàng thành công, Order Service tạo bản ghi Order (trạng thái Pending Payment) với Idempotency Key duy nhất, đồng thời lưu snapshot toàn bộ thuộc tính, mô tả và giá tại thời điểm mua để làm bằng chứng pháp lý độc lập.
6. **Thời hạn thanh toán:** Buyer nhận mã đơn hàng và bộ đếm ngược 15 phút để hoàn tất thanh toán.
7. **Giải phóng giữ hàng khi hết hạn:** Nếu Buyer không hoàn tất thanh toán trong 15 phút, worker nền sẽ kích hoạt sự kiện hết hạn, hủy Order và chuyển Listing trở lại trạng thái Published.

### 5.2. Sơ đồ tuần tự Checkout & Reservation (Sequence Diagram)

``mermaid
sequenceDiagram
    autonumber
    actor Buyer as Người mua (Buyer)
    participant UI as Marketplace Frontend
    participant OrderSvc as Order & Checkout Service
    participant ListingSvc as Catalog & Listing Service
    participant SearchSvc as Search Projection

    Buyer->>UI: Bấm "Tiến hành Checkout" (ListingID)
    UI->>OrderSvc: CreateCheckout(ListingID, Address, Method)
    OrderSvc->>ListingSvc: ReserveItem(ListingID, BuyerID, HoldTime: 15m)
    alt Món hàng còn khả dụng (Available)
        ListingSvc-->>OrderSvc: ReservationSuccess(ReservationID, SnapshotPrice)
        OrderSvc->>OrderSvc: Tạo Order (Pending Payment) & Lưu Snapshot
        OrderSvc-->>UI: Trả về OrderID & Hạn thanh toán (15 phút)
        UI-->>Buyer: Hiển thị màn hình Thanh toán
    else Món hàng đã bán hoặc đang bị giữ bởi phiên khác
        ListingSvc-->>OrderSvc: ReservationFailed (Already Reserved / Sold)
        OrderSvc-->>UI: Báo lỗi: Món hàng không còn khả dụng
        UI-->>Buyer: Thông báo sản phẩm đã hết & gợi ý tin tương tự
        ListingSvc-)SearchSvc: Publish Event: ListingUnavailable
    end
``

**Cần quyết định:** Giữ listing trước payment hay authorize trước; thời lượng giữ (15 phút hay cấu hình động); một order có thể chứa nhiều seller hay không; gom shipment/phí/refund.

## 6. P-05: Thanh toán, Xác nhận và Điều phối ngoại lệ (Saga & Compensation)

### 6.1. Luồng thực thi chi tiết
1. **Khởi tạo yêu cầu thanh toán:** Order Service gọi Payment Service tạo PaymentIntent với mã Correlation ID và Idempotency Key duy nhất.
2. **Tương tác cổng thanh toán:** Payment Service chuyển hướng người mua sang trang thanh toán của đối tác hoặc mở popup cổng thanh toán.
3. **Tiếp nhận Webhook:** Kết quả thanh toán được tiếp nhận qua Webhook callback từ nhà cung cấp. Hệ thống xác thực chữ ký số (HMAC signature) và kiểm tra chống xử lý trùng lặp.
4. **Luồng thành công:** Payment chuyển sang Captured, phát sự kiện PaymentSuccess sang Order Service để chuyển đơn sang Confirmed và báo Listing Service đánh dấu Sold.
5. **Luồng bù trừ khi lỗi/hết hạn (Saga Compensation):** Nếu thanh toán thất bại hoặc timeout, sự kiện PaymentFailed được phát ra. Order Service hủy đơn (Cancelled) và gửi lệnh bù trừ giải phóng giữ hàng (ReleaseReservation) về Listing Service để đưa món hàng trở lại Published.
6. **Đối soát & Chênh lệch (Reconciliation):** Các giao dịch timeout không rõ trạng thái được đưa vào hàng đợi đối soát (T+1) với cổng thanh toán để xác nhận dòng tiền.

### 6.2. Sơ đồ tuần tự Thanh toán & Bù trừ (Sequence Diagram)

``mermaid
sequenceDiagram
    autonumber
    actor Buyer as Người mua (Buyer)
    participant UI as Marketplace Frontend
    participant PaymentSvc as Payment Service
    participant Gateway as Cổng thanh toán (PGW)
    participant OrderSvc as Order Service
    participant ListingSvc as Listing Service

    Buyer->>UI: Xác nhận & Bấm "Thanh toán"
    UI->>PaymentSvc: InitPayment(OrderID, Amount, IdempotencyKey)
    PaymentSvc->>Gateway: CreateTransaction / RedirectUrl
    Gateway-->>UI: Chuyển hướng người mua tới trang thanh toán
    Buyer->>Gateway: Nhập thông tin & Xác thực OTP
    alt Thanh toán Thành công
        Gateway-->>PaymentSvc: Webhook Callback (PaymentCaptured, Signature)
        PaymentSvc->>PaymentSvc: VerifySignature & Update: Captured
        PaymentSvc-)OrderSvc: Publish Event: PaymentSuccess(OrderID)
        OrderSvc->>OrderSvc: Chuyển Order: Confirmed
        OrderSvc-)ListingSvc: ConfirmSold(ListingID)
        ListingSvc->>ListingSvc: Chuyển Listing: Sold
    else Thanh toán Thất bại hoặc Hết hạn giữ hàng (Timeout)
        Gateway-->>PaymentSvc: Callback: PaymentFailed / Timeout
        PaymentSvc->>PaymentSvc: Update Payment: Failed
        PaymentSvc-)OrderSvc: Publish Event: PaymentFailed(OrderID)
        OrderSvc->>OrderSvc: Chuyển Order: Cancelled
        Note over OrderSvc,ListingSvc: Hành động bù trừ (Saga Compensation)
        OrderSvc-)ListingSvc: ReleaseReservation(ListingID)
        ListingSvc->>ListingSvc: Mở lại Listing: Published
    end
``

**Cần quyết định:** Cổng thanh toán cụ thể, phương thức thẻ/ví/chuyển khoản, currency, thời hạn, phí hoàn tiền và SLA đối soát tài chính.

## 7. P-06: fulfillment và vận chuyển

1. Order đủ điều kiện chuyển fulfillment theo trạng thái được duyệt.
2. Buyer/seller cung cấp thông tin giao nhận tối thiểu cần thiết.
3. Shipping & Fulfillment lấy báo giá/chọn phương án; chính sách bên trả phí và thời gian giao cần được chốt.
4. Nếu ReMarket tạo shipment, gọi adapter hãng đã chọn bằng yêu cầu có idempotency.
5. Chuẩn hóa mã vận đơn và trạng thái hãng thành trạng thái ReMarket.
6. Webhook/callback được xác thực, chống lặp, lưu tham chiếu và xử lý sai thứ tự.
7. Buyer/seller nhận thông báo; địa chỉ/điện thoại chỉ chia sẻ cho vai trò cần thiết.
8. Provider tạm lỗi: giữ trạng thái trung gian, retry có giới hạn, đưa lỗi không tự hồi phục vào queue xử lý.
9. Tracking mất/mâu thuẫn: mở đường hỗ trợ/đối soát, không tự kết luận đã giao.
10. Order hoàn tất khi thỏa tiêu chí giao hàng/nhận hàng đã duyệt.
11. Giao thất bại/mất hàng/khiếu nại đi theo exception flow.

GHTK, GHN, GrabExpress là ví dụ đối tác tiềm năng, chưa được chọn. Tích hợp qua capability Shipping & Fulfillment và adapter; không mặc định tạo một microservice cho từng hãng.

## 8. P-07: hoàn tất, review và hậu giao dịch

1. Order đạt tiêu chí hoàn tất được business owner duyệt.
2. Buyer/seller nhận thông báo; trạng thái tiền/settlement hiển thị đúng vai trò.
3. Buyer tạo review nếu đáp ứng eligibility; review liên kết order.
4. Buyer/seller có thể mở case trong thời hạn/policy được duyệt.
5. Lịch sử order/payment/shipping và bằng chứng cần thiết được giữ theo retention.
6. Seller xem giao dịch của mình nhưng không đọc dữ liệu buyer ngoài mục đích được phép.

## 9. P-08: report, moderation và hỗ trợ

1. Người dùng gửi report/case, chọn loại lý do và liên kết listing/order/user.
2. Hệ thống cấp case ID, xác nhận tiếp nhận và phân loại mức độ.
3. Moderator/support xem dữ liệu cần thiết theo role; mask PII khi không cần.
4. Nhân viên ghi bằng chứng, quyết định, policy/căn cứ và bước tiếp theo.
5. Hành động ảnh hưởng listing/tài khoản/order/payment đi qua năng lực sở hữu dữ liệu đó; back-office không sửa database trực tiếp.
6. Trường hợp vượt thẩm quyền được chuyển cấp/phê duyệt.
7. Thông báo cho các bên trong phạm vi không làm lộ thông tin điều tra hoặc PII trái phép.
8. Đóng case theo tiêu chí; reopen/appeal theo policy nếu có.

## 10. P-09: Sao lưu, Khôi phục dữ liệu phân tán & Kiểm chứng toàn vẹn (Backup, Restore & DR)

### 10.1. Kích hoạt (Triggers)
Xóa nhầm/hỏng dữ liệu, deploy/migration lỗi, lỗi logic dữ liệu, database unavailable, thảm họa hạ tầng hoặc diễn tập định kỳ (Restore Drill).

### 10.2. Quy trình mục tiêu
1. **Khai báo & Cô lập:** Incident Commander (STK-18) xác định mức độ sự cố, ngăn chặn ghi thêm dữ liệu sai (tạm ngắt API public sang chế độ bảo trì nếu cần).
2. **Xác định mục tiêu PITR:** Chọn điểm khôi phục (Recovery Point) thỏa mãn mục tiêu RPO được duyệt.
3. **Khôi phục độc lập từng Datastore:** Khôi phục SQL Server (RDS PITR) và MongoDB (Atlas Backup) vào môi trường cách ly hoặc tài nguyên mới; xác minh khóa mã hóa KMS.
4. **Đối soát tính nhất quán liên dịch vụ:** Khi khôi phục các database độc lập có thể xảy ra lệch thời điểm (time skew); thực hiện đối soát dữ liệu giao dịch giữa Order, Payment và Listing.
5. **Replay sự kiện từ Outbox / Event Store:** Bù đắp các sự kiện bị thiếu trong khoảng thời gian khôi phục.
6. **Tái tạo dữ liệu dẫn xuất:** Khởi dựng lại Search Index và Cache từ nguồn chuẩn (Source of Truth).
7. **Kiểm chứng toàn vẹn & Smoke Test:** Chạy bộ kiểm thử tự động kiểm tra số lượng bản ghi, tính toàn vẹn quan hệ và thực hiện thử một chu trình mua hàng mẫu.
8. **Phê duyệt chuyển lưu lượng:** DBA, Security Lead và Business Owner cùng ký duyệt chuyển lưu lượng người dùng trở lại hệ thống.

### 10.3. Sơ đồ quy trình phục hồi dữ liệu (Recovery Flowchart)

``mermaid
flowchart TD
    Start([Phát hiện sự cố dữ liệu / Yêu cầu Restore]) --> Triage[Xác định phạm vi: Service, Database & Thời điểm PITR]
    Triage --> StopWrites[Ngắt kết nối ghi / Bật chế độ bảo trì để bảo vệ dữ liệu]
    StopWrites --> RestoreDB[Khôi phục SQL Server & MongoDB vào môi trường cách ly]
    RestoreDB --> RestoreS3[Kiểm tra tính toàn vẹn Object & Key trên S3]
    RestoreS3 --> IntegrityCheck{Kiểm tra toàn vẹn liên dịch vụ<br/>Order vs Listing vs Payment?}
    IntegrityCheck -- Lệch thời điểm --> ReplayOutbox[Replay sự kiện từ Outbox / Hàng đợi đối soát]
    ReplayOutbox --> IntegrityCheck
    IntegrityCheck -- Đạt tiêu chuẩn --> RebuildProjections[Tái tạo Search Index & Cache từ Source of Truth]
    RebuildProjections --> SmokeTest[Thực hiện giao dịch mua bán mẫu (Smoke Test)]
    SmokeTest --> Approve[Incident Commander & Business Owner phê duyệt]
    Approve --> SwitchTraffic([Chuyển lưu lượng người dùng trở lại])
``

## 11. P-10: Gửi thông báo và cảnh báo (Notification & Alerting)

**Tác nhân:** Notification Service, Domain Services (Order, Payment, Shipping, Moderation), Đối tác gửi tin (Email, SMS, Push Notification).

### 11.1. Luồng thực thi chi tiết
1. **Lắng nghe sự kiện nghiệp vụ:** Notification Service tiêu thụ các Domain Events từ Message Broker (ví dụ: OrderConfirmed, PaymentSuccess, ShipmentPickedUp, ListingRejected).
2. **Xác định đối tượng & tùy chọn nhận tin:** Truy vấn thông tin người nhận và cấu hình tùy chọn thông báo (Notification Preferences) để kiểm tra người dùng có opt-out kênh này không.
3. **Kiểm tra giờ yên tĩnh (Quiet Hours):** Nếu là thông báo tiếp thị gửi qua SMS/Push và rơi vào khung giờ từ 22h00 đến 08h00, thông báo được hoãn gửi cho đến sáng hôm sau. Thông báo giao dịch và OTP bảo mật luôn được gửi ngay lập tức.
4. **Định dạng mẫu thông báo:** Tải template mẫu tương ứng, điền các tham số động (mã đơn, số tiền, tên sản phẩm) theo ngôn ngữ của người dùng.
5. **Gửi qua Adapter đối tác:** Gọi API đối tác gửi tin (Email Gateway / SMS Provider).
6. **Xử lý ngoại lệ & Retry:** Nếu đối tác trả về lỗi tạm thời (network timeout, rate limit), hệ thống tự động retry với cơ chế exponential backoff. Nếu lỗi vĩnh viễn (sai số điện thoại, email không tồn tại), thông báo được đẩy vào DLQ để theo dõi.
7. **Lưu lịch sử thông báo:** Ghi nhận trạng thái gửi (Sent / Failed / Read) để hiển thị trong mục "Thông báo của tôi" trên ứng dụng người dùng.

## 12. Trạng thái nghiệp vụ tham chiếu

Các trạng thái là vocabulary phân tích, chưa phải state machine cuối cùng.

| Đối tượng | Trạng thái tham chiếu | Điều kiện chuyển cần định nghĩa |
|---|---|---|
| Listing | Draft, Pending Review, Published, Reserved, Sold, Hidden, Rejected, Removed, Expired | Ai chuyển, có duyệt lại, reservation/order đang mở không |
| Order | Draft, Pending Payment, Confirmed, Fulfillment Pending, Shipped, Delivered, Completed, Cancelled, Disputed | Trigger, retry, hủy, timeout và bù trừ |
| Payment | Initiated, Pending, Authorized/Captured nếu dùng, Failed, Refund Pending, Refunded, Reconciliation Required | Provider semantics, duplicate, timeout, partial amount |
| Shipment | Quote Pending, Created, Picked Up, In Transit, Delivered, Delivery Failed, Returned, Cancelled, Unknown | Mapping, bằng chứng, callback sai thứ tự, đối soát |
| Case | Open, Triaged, Investigating, Waiting, Escalated, Resolved, Closed, Reopened | Severity, SLA, appeal và quyền quyết định |

Không cho phép đổi trạng thái tùy ý từ giao diện quản trị. Xác định lệnh hợp lệ và nguồn chuẩn cho mỗi aggregate trong BA 05/06.

## 13. Chuyển tiếp sang yêu cầu

Từ mỗi quy trình tạo functional requirements cho trigger/precondition/flow/postcondition; business rules cho điều kiện/quyền/trạng thái; NFR về latency, availability, privacy, audit, retry, consistency và recovery; mô hình dữ liệu/API/sự kiện; acceptance criteria cho timeout, duplicate, concurrency và restore.

## 14. Lịch sử cập nhật

| Phiên bản | Ngày | Mô tả |
|---|---|---|
| 1.0 | 12/11/2026 | Tạo quy trình mục tiêu đề xuất; ghi rõ chính sách chưa quyết định. |
