# Danh Mục Màn Hình — Screen Inventory & Specifications

| Tài liệu | Danh Mục Màn Hình & Đặc Tả Chi Tiết (Screen Inventory) |
| :--- | :--- |
| **Dự án** | Nền tảng Thương mại Điện tử Đồ cũ C2C ReMarket |
| **Mã tài liệu** | `DOC-BA-04-SCR` |
| **Phiên bản** | v1.0 |
| **Trạng thái** | Chấp thuận (Approved) |
| **Phạm vi** | 27 Màn hình chính và các Modal dùng chung trên 3 Kênh tương tác |
| **Liên kết truy vết** | [sitemap.md](file:///d:/remarket-platform-system/docs/BA/04_product-and-ux/sitemap.md), [information-architecture.md](file:///d:/remarket-platform-system/docs/BA/04_product-and-ux/information-architecture.md), [use-cases.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/use-cases.md), [user-stories.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/user-stories.md), [functional-requirements.md](file:///d:/remarket-platform-system/docs/BA/03_requirements/functional-requirements.md) |

---

## 1. Quy Ước Mã Định Danh Màn Hình

Hệ thống đặt mã màn hình chuẩn hóa theo định dạng: `SCR-[KÊNH]-[SỐ THỨ TỰ]`
- `SCR-MKT-xx`: Kênh Marketplace Web / Mobile App (12 màn hình)
- `SCR-SEL-xx`: Kênh Seller Center (7 màn hình)
- `SCR-ADM-xx`: Kênh Admin / Back-office Portal (8 màn hình)
- `MOD-[PHÂN HỆ]-[SỐ]`: Các hộp thoại tương tác nổi (Modal / Drawer)

---

## 2. Bảng Danh Mục Tổng Hợp 27 Màn Hình (Master Screen Inventory Table)

| Mã Màn Hình | Tên Màn Hình | Kênh Giao Diện | Vai Trò (Roles) | Mức Ưu Tiên | Use Case Liên Kết | User Story & FR |
| :--- | :--- | :--- | :--- | :---: | :--- | :--- |
| `SCR-MKT-01` | Trang chủ Khám phá | Marketplace | `ROLE-00`, `01` | Must | `UC-06`, `UC-07` | `US-SRC-01`, `FR-SRC-01` |
| `SCR-MKT-02` | Tìm kiếm & Phân loại Danh mục | Marketplace | `ROLE-00`, `01` | Must | `UC-06`, `UC-07` | `US-SRC-02`, `FR-SRC-02..05` |
| `SCR-MKT-03` | Chi tiết Tin đăng Sản phẩm | Marketplace | `ROLE-00`, `01` | Must | `UC-07`, `UC-08`, `UC-18` | `US-SRC-03`, `FR-SRC-06`, `FR-CHK-01` |
| `SCR-MKT-04` | Hồ sơ Người bán Công khai | Marketplace | `ROLE-00`, `01` | Should | `UC-07`, `UC-19` | `US-TRU-02`, `FR-TRU-02`, `FR-REV-02` |
| `SCR-MKT-05` | Giỏ hàng (Ý định mua) | Marketplace | `ROLE-01` | Must | `UC-08` | `US-CHK-01`, `FR-CHK-02` |
| `SCR-MKT-06` | Checkout & Giữ hàng 15 phút | Marketplace | `ROLE-01` | Must | `UC-09` | `US-CHK-02`, `FR-CHK-03..06` |
| `SCR-MKT-07` | Kết quả & Trạng thái Thanh toán | Marketplace | `ROLE-01` | Must | `UC-10` | `US-PAY-01`, `FR-PAY-01..05` |
| `SCR-MKT-08` | Quản lý Danh sách Đơn mua | Marketplace | `ROLE-01` | Must | `UC-11`, `UC-12` | `US-ORD-01`, `FR-ORD-01..05` |
| `SCR-MKT-09` | Chi tiết Đơn mua & Tracking | Marketplace | `ROLE-01` | Must | `UC-12`, `UC-13` | `US-SHP-02`, `FR-SHP-04..06` |
| `SCR-MKT-10` | Gửi Khiếu nại & Đánh giá | Marketplace | `ROLE-01` | Must | `UC-17`, `UC-19` | `US-DSP-01`, `US-REV-01`, `FR-DSP-01`, `FR-REV-01` |
| `SCR-MKT-11` | Trung tâm Thông báo Cá nhân | Marketplace | `ROLE-01`, `02` | Must | `UC-14` | `US-NTF-01`, `FR-NTF-01..04` |
| `SCR-MKT-12` | Quản lý Tài khoản & Sổ địa chỉ | Marketplace | `ROLE-01` | Must | `UC-01`, `UC-02` | `US-ACC-01`, `FR-ACC-01..05` |
| `SCR-SEL-01` | Kích hoạt Hồ sơ Người bán | Seller Center | `ROLE-01` | Must | `UC-03` | `US-ACC-02`, `FR-ACC-06` |
| `SCR-SEL-02` | Bảng Điều khiển Seller Dashboard | Seller Center | `ROLE-02` | Must | `UC-04`, `UC-12` | `US-LST-01`, `FR-LST-01` |
| `SCR-SEL-03` | Quản lý Kho Tin đăng | Seller Center | `ROLE-02` | Must | `UC-04`, `UC-05` | `US-LST-02`, `FR-LST-06..08` |
| `SCR-SEL-04` | Wizard Tạo & Sửa Tin Đăng | Seller Center | `ROLE-02` | Must | `UC-04`, `UC-20` | `US-LST-01`, `US-MED-01`, `FR-LST-01..05`, `FR-MED-01..03` |
| `SCR-SEL-05` | Quản lý Đơn hàng Bán | Seller Center | `ROLE-02` | Must | `UC-11`, `UC-12` | `US-ORD-02`, `FR-ORD-06..08` |
| `SCR-SEL-06` | Chi tiết Đơn bán & Tạo Vận đơn | Seller Center | `ROLE-02` | Must | `UC-12`, `UC-13` | `US-SHP-01`, `FR-SHP-01..03` |
| `SCR-SEL-07` | Doanh thu Tạm giữ & Đối soát | Seller Center | `ROLE-02` | Should | `UC-15` | `US-PAY-03`, `FR-PAY-07..09` |
| `SCR-ADM-01` | Hàng đợi Tin & Báo cáo Vi phạm | Back-office | `ROLE-03`, `05` | Must | `UC-20`, `UC-21` | `US-TRU-01`, `FR-TRU-01..03` |
| `SCR-ADM-02` | Thẩm định Chi tiết Tin đăng | Back-office | `ROLE-03` | Must | `UC-20`, `UC-21` | `US-TRU-01`, `FR-TRU-01`, `FR-LST-09` |
| `SCR-ADM-03` | Bàn Tra cứu Giao dịch 360° | Back-office | `ROLE-04` | Must | `UC-22`, `UC-24` | `US-SUP-01`, `FR-SUP-01..03` |
| `SCR-ADM-04` | Phân xử Khiếu nại & Hoàn tiền | Back-office | `ROLE-04` | Must | `UC-17`, `UC-23` | `US-DSP-02`, `FR-DSP-02..05` |
| `SCR-ADM-05` | Quản lý Cây Danh mục & Thuộc tính | Back-office | `ROLE-05` | Must | `UC-25` | `US-ADM-01`, `FR-ADM-01..03` |
| `SCR-ADM-06` | Quản lý Từ khóa Cấm & Chính sách | Back-office | `ROLE-05` | Must | `UC-25` | `US-ADM-02`, `FR-ADM-04..06` |
| `SCR-ADM-07` | Quản lý Tài khoản & Phân quyền | Back-office | `ROLE-05` | Must | `UC-25` | `US-ADM-03`, `FR-ADM-07..09` |
| `SCR-ADM-08` | Giám sát Lệch pha & Báo cáo DR | Back-office | `ROLE-06` | Should | `UC-26` | `US-OPS-01`, `FR-OPS-01..03` |

---

## 3. Đặc Tả Chi Tiết 27 Màn Hình

### 3.1. Phân Hệ Marketplace (`SCR-MKT-01..12`)

#### `SCR-MKT-01`: Trang chủ Khám phá (Marketplace Home)
- **Mục đích:** Cung cấp điểm tiếp cận ban đầu, điều hướng người dùng vào cây danh mục và hiển thị các sản phẩm đồ cũ nổi bật.
- **Dữ liệu hiển thị:**
  - *Derived Data (Cache):* Banner khuyến mãi; Danh mục đồ cũ phổ biến; Danh sách "Vừa mới đăng" (phân trang lướt); Danh sách "Giá tốt quanh bạn" dựa trên vị trí IP/định vị.
- **Hành động chính (CTAs):** Nhập từ khóa tìm kiếm (Primary); Bấm chọn Danh mục; Bấm nút "Đăng bán đồ cũ" (Chuyển sang `SCR-SEL-04` hoặc `SCR-SEL-01`).
- **Trạng thái màn hình:**
  - *Loading:* Skeleton placeholder dạng lưới sản phẩm.
  - *Degraded Mode:* Khi dịch vụ gợi ý AI/Elasticsearch nghẽn, tự động fallback về danh sách sản phẩm tĩnh sắp xếp theo thời gian mới nhất.
- **Truy vết:** `UC-06`, `US-SRC-01`, `FR-SRC-01`, `NFR-PERF-01`.

#### `SCR-MKT-02`: Tìm kiếm & Phân loại Danh mục (Search & Faceted Catalog)
- **Mục đích:** Cho phép tìm kiếm chính xác và lọc sản phẩm theo các tiêu chí đồ cũ đặc thù.
- **Dữ liệu hiển thị:**
  - *Derived Data (Search Engine):* Số lượng kết quả tìm thấy; Lưới danh sách sản phẩm (Ảnh, Tên, Giá, Tình trạng `Condition Grade`, Khoảng cách km); Bộ lọc đa chiều (Faceted filters: Danh mục, Khoảng giá Min-Max, Tình trạng hàng, Phương thức vận chuyển).
- **Hành động chính (CTAs):** Áp dụng bộ lọc; Đổi tiêu chí sắp xếp; Bấm vào thẻ sản phẩm để mở `SCR-MKT-03`.
- **Trạng thái ngoại lệ:**
  - *Empty State:* Không tìm thấy sản phẩm nào phù hợp từ khóa -> Hiển thị thông báo thân thiện kèm nút "Xóa toàn bộ bộ lọc" và 4 từ khóa gợi ý phổ biến.
- **Truy vết:** `UC-06`, `US-SRC-02`, `FR-SRC-02..05`.

#### `SCR-MKT-03`: Chi tiết Tin đăng Sản phẩm (Listing Detail)
- **Mục đích:** Trình bày minh bạch toàn bộ tình trạng hàng cũ và kiểm tra trạng thái khả dụng thực tế của món hàng.
- **Dữ liệu hiển thị:**
  - *Real-time Source of Truth:* Trạng thái khả dụng thực (`Published`, `Reserved`, `Sold`); Giá bán; Danh sách ảnh độ phân giải cao; Thang tình trạng (`COND-*`); Bảng khai báo khuyết điểm (vết xước, lỗi chức năng, phụ kiện thiếu); Số Serial/IMEI (đã che 4 số cuối); Khối thông tin Người bán (Tên, Đánh giá sao, Tỷ lệ phản hồi).
- **Hành động chính (CTAs):**
  - **"Mua ngay"** (Primary CTA): Chuyển thẳng đến Checkout (`SCR-MKT-06`) và bắt đầu giữ hàng 15 phút.
  - **"Thêm vào giỏ"** (Secondary CTA): Lưu ý định mua vào `SCR-MKT-05`.
  - **"Báo cáo tin vi phạm"**: Mở modal `MOD-RPT-01`.
- **Trạng thái ngoại lệ & Xung đột:**
  - *Item Reserved (Đang bị giữ chỗ `EX-002`):* Nút Mua ngay đổi thành *"Đang có người đặt mua (Giữ chỗ 15p)"*, nút màu xám vô hiệu hóa.
  - *Item Sold (Đã bán `EX-001`):* Badge màu xám *"ĐÃ BÁN"*, nút Mua biến mất, gợi ý các sản phẩm cùng loại.
  - *Price Changed (`EX-004`):* Nếu giá vừa được cập nhật, hiển thị banner thông báo giá mới.
- **Truy vết:** `UC-07`, `UC-08`, `UC-18`, `US-SRC-03`, `FR-SRC-06`, `FR-CHK-01`, `BR-PRO-001..003`.

#### `SCR-MKT-04`: Hồ sơ Người bán Công khai (Seller Public Profile)
- **Mục đích:** Cung cấp thông tin lịch sử bán hàng và đánh giá từ cộng đồng để Người mua thẩm định uy tín trước khi giao dịch.
- **Dữ liệu hiển thị:** Tên/Biệt danh người bán; Ngày tham gia sàn; Huy hiệu xác thực (KYC Verified); Điểm đánh giá trung bình (1-5 sao); Tỷ lệ hủy đơn; Danh sách các đánh giá từ người mua trước kèm hình ảnh thực tế; Lưới các sản phẩm đang rao bán khác của người bán này.
- **Hành động chính:** Theo dõi người bán; Lọc đánh giá theo số sao; Bấm xem từng tin đăng của người bán.
- **Truy vết:** `UC-07`, `UC-19`, `US-TRU-02`, `FR-TRU-02`, `FR-REV-02`.

#### `SCR-MKT-05`: Giỏ hàng (Cart - Intent Only)
- **Mục đích:** Lưu trữ các sản phẩm người mua có ý định mua (`BR-ORD-004`).
- **Dữ liệu hiển thị:** Danh sách các món đồ cũ đã thêm; Giá hiện tại; Trạng thái khả dụng thực tế của từng món.
- **Hành động chính:** Tích chọn 1 hoặc nhiều món hàng của cùng 1 người bán; Bấm **"Tiến hành Đặt hàng"**; Xóa khỏi giỏ hàng.
- **Quy tắc đặc thù:** Giỏ hàng **KHÔNG giữ hàng**. Nếu món hàng trong giỏ bị người khác mua mất, hệ thống hiển thị dòng cảnh báo: *"Món hàng này đã được bán cho người khác"* và nút chuyển về dạng lưu trữ.
- **Truy vết:** `UC-08`, `US-CHK-01`, `FR-CHK-02`, `BR-ORD-004`.

#### `SCR-MKT-06`: Checkout & Giữ hàng 15 phút (Checkout & Hold Window)
- **Mục đích:** Xác lập giao dịch mua, khởi tạo khóa giữ hàng tạm thời 15 phút (`BR-ORD-003`) và chọn phương thức thanh toán/vận chuyển.
- **Dữ liệu hiển thị:**
  - **Banner đếm ngược thời gian giữ chỗ (Countdown Timer: 15:00).**
  - Snapshot sản phẩm tại thời điểm mua (Tên, Ảnh, Giá, Tình trạng).
  - Khối Địa chỉ nhận hàng (Chọn từ sổ địa chỉ hoặc thêm mới).
  - Đơn vị vận chuyển 3PL được chỉ định & Phí vận chuyển ước tính (`BR-SHP-001`).
  - Phân rã bảng giá: Tiền hàng + Phí bảo vệ người mua (`BR-PAY-010`) + Phí vận chuyển = Tổng thanh toán.
  - Danh sách phương thức thanh toán khả dụng (VNPAY, MoMo, Thẻ tín dụng/ghi nợ quốc tế).
- **Hành động chính:** Bấm **"Xác nhận Thanh toán"** (Chuyển hướng sang cổng thanh toán đối tác).
- **Trạng thái ngoại lệ & Xử lý hết hạn:**
  - *Hold Timer Expired (`EX-003`):* Khi đồng hồ về `00:00`, màn hình mờ đi, xuất hiện `MOD-EXP-01`: *"Hết thời gian giữ hàng 15 phút. Món hàng đã được mở lại cho người khác"*, cung cấp nút "Tải lại trang" để kiểm tra xem hàng còn hay đã bị mua.
  - *Conflict on Submit (`EX-002`):* Nếu bấm thanh toán đúng lúc hết hạn và có người khác giữ chỗ, báo lỗi xung đột và hủy giao dịch.
- **Truy vết:** `UC-09`, `US-CHK-02`, `FR-CHK-03..06`, `BR-ORD-003`, `BR-ORD-009`, `BR-PAY-010`.

#### `SCR-MKT-07`: Kết quả & Trạng thái Thanh toán (Payment Return / Pending)
- **Mục đích:** Tiếp nhận kết quả điều hướng về từ cổng thanh toán bên thứ ba và phản hồi trạng thái minh bạch cho người mua.
- **Dữ liệu hiển thị:**
  - *Trường hợp Thành công (`Paid`):* Biểu tượng tích xanh, Mã đơn hàng, Số tiền đã thanh toán, Mã chuẩn chi giao dịch ngân hàng, Nút "Xem chi tiết đơn hàng".
  - *Trường hợp Thất bại (`Failed`):* Biểu tượng dấu chéo đỏ, Lý do lỗi từ ngân hàng (Số dư không đủ, Hết hạn OTP, Khách hủy), Nút "Thử thanh toán lại" (nếu thời gian giữ hàng 15p vẫn còn hiệu lực).
  - *Trường hợp Chờ xác nhận (`Pending/Unknown` - `EX-005`):* Biểu tượng đồng hồ cát vàng: *"Hệ thống đang chờ đối soát phản hồi từ ngân hàng"*. Đồng hồ đếm lùi 5 phút, nút "Kiểm tra lại trạng thái", hướng dẫn khách không thanh toán lần 2.
- **Hành động chính:** Xem chi tiết đơn hàng; Quay về trang chủ; Thử lại phương thức thanh toán khác.
- **Truy vết:** `UC-10`, `US-PAY-01`, `FR-PAY-01..05`, `BR-PAY-002..004`.

#### `SCR-MKT-08`: Quản lý Danh sách Đơn mua (Buyer Order List)
- **Mục đích:** Giúp người mua theo dõi toàn bộ các đơn hàng đã đặt theo từng giai đoạn xử lý.
- **Dữ liệu hiển thị:** Danh sách đơn hàng chia theo các Tab: Tất cả, Chờ thanh toán, Chờ người bán đóng gói, Đang giao hàng, Đã giao thành công, Đã hủy, Khiếu nại/Trả hàng. Mỗi thẻ đơn hiển thị Mã đơn, Ngày tạo, Tên người bán, Thumbnail sản phẩm, Tổng tiền và Trạng thái đơn hàng.
- **Hành động chính:** Lọc theo mã đơn; Bấm vào đơn hàng để mở `SCR-MKT-09`; Bấm "Thanh toán ngay" (với đơn còn hạn giữ hàng); Bấm "Mua lại".
- **Truy vết:** `UC-11`, `UC-12`, `US-ORD-01`, `FR-ORD-01..05`.

#### `SCR-MKT-09`: Chi tiết Đơn mua & Tracking (Order Detail & Tracking)
- **Mục đích:** Cung cấp thông tin đầy đủ về đơn mua, xem lại snapshot sản phẩm và theo dõi hành trình vận chuyển theo thời gian thực.
- **Dữ liệu hiển thị:**
  - Snapshot sản phẩm bất biến tại thời điểm đặt hàng.
  - Bảng 3 miền trạng thái: `Trạng thái Đơn hàng` - `Trạng thái Thanh toán` - `Trạng thái Vận chuyển`.
  - Timeline lộ trình vận chuyển 3PL (Các mốc: Người bán đã chuẩn bị hàng -> Shipper đã lấy hàng -> Đang trung chuyển -> Đang giao hàng -> Giao thành công).
  - Thông tin người nhận và người bán (đã áp dụng quy tắc che giấu PII).
- **Hành động chính:**
  - Nút **"Đã nhận được hàng"** (Chuyển trạng thái sang `Completed`, kích hoạt giải ngân tiền cho Seller sau 72h).
  - Nút **"Yêu cầu Trả hàng / Khiếu nại"** (Khả dụng trong vòng 72 giờ kể từ khi Shipper báo `Delivered` - `BR-DSP-001`).
  - Nút **"Đánh giá người bán"** (Mở `SCR-MKT-10`).
- **Truy vết:** `UC-12`, `UC-13`, `US-SHP-02`, `FR-SHP-04..06`, `BR-DSP-001`.

#### `SCR-MKT-10`: Gửi Khiếu nại & Đánh giá (Dispute & Review Action)
- **Mục đích:** Xử lý các nghiệp vụ hậu mãi: gửi khiếu nại tranh chấp hàng hóa hoặc viết đánh giá phản hồi chất lượng.
- **Dữ liệu hiển thị:**
  - *Giao diện Khiếu nại (`Dispute Form`):* Chọn lý do khiếu nại (Hàng không đúng mô tả, Hàng giả/nhái, Hàng vỡ móp do vận chuyển, Thiếu phụ kiện so với cam kết); Ô nhập mô tả chi tiết; Trình tải tối đa 5 ảnh và 1 video bằng chứng lúc bóc hộp (Unboxing video).
  - *Giao diện Đánh giá (`Review Form`):* Chấm điểm sao (1 đến 5 sao); Đánh giá tiêu chí nhanh (Đúng mô tả, Đóng gói cẩn thận, Giao hàng nhanh); Ô viết nhận xét và tải ảnh thực tế.
- **Hành động chính:** Bấm **"Gửi yêu cầu Khiếu nại"** (Hệ thống khóa tiền giải ngân của Seller và tạo hồ sơ `Dispute`); Bấm **"Gửi Đánh giá"**.
- **Truy vết:** `UC-17`, `UC-19`, `US-DSP-01`, `US-REV-01`, `FR-DSP-01`, `FR-REV-01`, `BR-DSP-001`.

#### `SCR-MKT-11`: Trung tâm Thông báo Cá nhân (Notification Center)
- **Mục đích:** Tập trung toàn bộ thông báo hệ thống, biến động giao dịch và cập nhật tin đăng cho người dùng.
- **Dữ liệu hiển thị:** Danh sách thông báo phân nhóm: Giao dịch & Đơn hàng, Biến động số dư/tiền bán, Tin đăng & Khuyến mãi, Cảnh báo an toàn. Hiển thị tiêu đề, trích đoạn nội dung, thời gian nhận và dấu chấm đỏ chưa đọc (Unread badge).
- **Hành động chính:** Bấm vào thông báo (Tự động đánh dấu đã đọc và deep-link đến trang nghiệp vụ tương ứng: `SCR-MKT-09`, `SCR-SEL-06`...); Bấm "Đánh dấu tất cả là đã đọc".
- **Truy vết:** `UC-14`, `US-NTF-01`, `FR-NTF-01..04`.

#### `SCR-MKT-12`: Quản lý Tài khoản & Sổ địa chỉ (Account & Address Book)
- **Mục đích:** Quản lý thông tin định danh cá nhân và danh sách địa chỉ giao nhận hàng hóa.
- **Dữ liệu hiển thị:** Họ tên, Email, Số điện thoại (dạng masked); Danh sách địa chỉ nhận hàng (Gồm địa chỉ mặc định, các địa chỉ phụ: Tên người nhận, SĐT, Số nhà/đường, Phường/Xã, Quận/Huyện, Tỉnh/Thành phố); Cài đặt đổi mật khẩu và bảo mật 2 lớp.
- **Hành động chính:** Thêm địa chỉ mới; Sửa/xóa địa chỉ; Đặt làm địa chỉ mặc định; Cập nhật hồ sơ cá nhân.
- **Truy vết:** `UC-01`, `UC-02`, `US-ACC-01`, `FR-ACC-01..05`.

---

### 3.2. Phân Hệ Seller Center (`SCR-SEL-01..07`)

#### `SCR-SEL-01`: Kích hoạt Hồ sơ Người bán (Seller Onboarding)
- **Mục đích:** Hoàn thiện thủ tục kích hoạt vai trò Người bán (`ROLE-02`) theo quy chế sàn thương mại điện tử.
- **Dữ liệu hiển thị:** Biểu mẫu đăng ký: Họ tên đầy đủ, Số CCCD/Hộ chiếu, Ảnh chụp mặt trước/mặt sau CCCD, Thông tin tài khoản ngân hàng thụ hưởng (Tên ngân hàng, Số tài khoản, Tên chủ tài khoản viết hoa không dấu); Hộp tích cam kết không kinh doanh hàng giả/hàng cấm.
- **Hành động chính:** Tải ảnh giấy tờ; Bấm **"Gửi hồ sơ kích hoạt"**; Kiểm tra tính hợp lệ số tài khoản ngân hàng.
- **Truy vết:** `UC-03`, `US-ACC-02`, `FR-ACC-06`.

#### `SCR-SEL-02`: Bảng Điều khiển Seller Dashboard
- **Mục đích:** Cung cấp bức tranh tổng thể về hiệu suất bán hàng, cảnh báo công việc cần làm ngay và tình trạng tài chính.
- **Dữ liệu hiển thị:**
  - Khối Hành động Cần Làm: Số đơn mới cần đóng gói, Số tin đăng bị báo lỗi/từ chối, Số vụ khiếu nại đang chờ phản hồi bằng chứng.
  - Khối Tài chính Tạm giữ: Số tiền đang tạm giữ trong các đơn đang giao (Escrow Pending), Số tiền khả dụng chuẩn bị payout đợt kế tiếp.
  - Khối Hiệu suất: Tổng doanh thu tháng, Điểm uy tín người bán (Rating trung bình), Tỷ lệ hủy đơn hàng.
- **Hành động chính:** Bấm trực tiếp vào các chỉ số để lọc thẳng đến danh sách đơn/tin đăng tương ứng.
- **Truy vết:** `UC-04`, `UC-12`, `US-LST-01`, `FR-LST-01`.

#### `SCR-SEL-03`: Quản lý Kho Tin đăng (Listing Inventory Management)
- **Mục đích:** Quản lý vòng đời toàn bộ các sản phẩm đã và đang rao bán của người bán.
- **Dữ liệu hiển thị:** Bảng danh sách tin có bộ lọc theo trạng thái: Đang hiển thị (`Published`), Đang giữ chỗ mua (`Reserved`), Chờ duyệt (`PendingModeration`), Đã bán (`Sold`), Bị từ chối (`Rejected`), Bản nháp (`Draft`). Hiển thị Thumbnail, Tiêu đề, Giá, Danh mục, Lượt xem, Ngày đăng.
- **Hành động chính:** Bấm **"Đăng tin mới"** (Mở `SCR-SEL-04`); Chỉnh sửa tin (chỉ khả dụng khi tin chưa bị khóa giữ hàng); Ẩn/Hiện tin đăng; Xóa tin đăng nháp.
- **Truy vết:** `UC-04`, `UC-05`, `US-LST-02`, `FR-LST-06..08`.

#### `SCR-SEL-04`: Wizard Tạo & Sửa Tin Đăng (Listing Creation Wizard)
- **Mục đích:** Hướng dẫn người bán đăng tin đồ cũ chuẩn hóa qua quy trình 3 bước chặt chẽ, tối ưu hóa việc tải ảnh trực tiếp lên Cloud Object Storage.
- **Quy trình 3 Bước:**
  1. *Bước 1 - Thông tin cơ bản & Phân loại:* Chọn Cây danh mục (3 cấp) -> Hệ thống tự động sinh các trường thuộc tính động tương ứng (Hãng sản xuất, Model, Dung lượng, Năm sản xuất...).
  2. *Bước 2 - Tình trạng hàng & Tải ảnh:* Chọn thang tình trạng chuẩn (`COND-NEW`, `COND-EXC`, `COND-GOOD`...); Hoàn thành bảng khai báo khuyết điểm (vết xước, lỗi chức năng); Nhập số Serial/IMEI; Trình tải ảnh trực tiếp lên S3 qua Presigned URL (Tối thiểu 3 ảnh thật, tối đa 10 ảnh, hiển thị thanh tiến trình từng ảnh và nút thử lại nếu ảnh lỗi).
  3. *Bước 3 - Định giá & Vận chuyển:* Nhập giá bán mong muốn; Nhập kích thước & khối lượng đóng gói (D x R x C cm, Gram) để tính cước vận chuyển tự động; Xem trước bản hiển thị ngoài sàn.
- **Kiểm tra tự động tức thì (Real-time Validation):** Quét từ khóa cấm trong tiêu đề và mô tả (`BR-LST-002`); Cảnh báo nếu giá bất thường so với thị trường.
- **Hành động chính:** Bấm "Lưu bản nháp"; Bấm **"Đăng tin"** (Gửi sang hàng đợi kiểm duyệt hoặc xuất bản tự động theo quy tắc tin cậy).
- **Truy vết:** `UC-04`, `UC-20`, `US-LST-01`, `US-MED-01`, `FR-LST-01..05`, `FR-MED-01..03`, `BR-PRO-001..003`.

#### `SCR-SEL-05`: Quản lý Đơn hàng Bán (Seller Order Management)
- **Mục đích:** Theo dõi và xử lý các đơn hàng phát sinh từ người mua.
- **Dữ liệu hiển thị:** Danh sách đơn hàng bán chia theo tab: Đơn mới cần xác nhận, Chờ đóng gói giao hàng, Đang giao hàng, Giao thành công, Đơn bị khiếu nại/Trả hàng. Hiển thị Mã đơn hàng, Tên sản phẩm, Tên người mua (chỉ hiện khi đã xác nhận đơn), Tổng tiền nhận ròng sau trừ phí.
- **Hành động chính:** Lọc theo mã đơn; Bấm vào đơn hàng để mở `SCR-SEL-06`.
- **Truy vết:** `UC-11`, `UC-12`, `US-ORD-02`, `FR-ORD-06..08`.

#### `SCR-SEL-06`: Chi tiết Đơn bán & Tạo Vận đơn (Fulfillment & Shipment Creation)
- **Mục đích:** Xác nhận chuẩn bị đơn hàng, xem địa chỉ giao hàng và tạo mã vận đơn với đối tác vận chuyển 3PL.
- **Dữ liệu hiển thị:**
  - Thông tin sản phẩm và số tiền nhận ròng (đã khấu trừ phí sàn và phí bảo vệ người mua).
  - **Địa chỉ giao hàng & Số điện thoại Người mua:** Chỉ hiển thị đầy đủ khi đơn hàng đã chuyển sang trạng thái `Confirmed` (đã thanh toán thành công - `BR-SEC-004`).
  - Hạn chót đóng gói hàng (Thời hạn 48 giờ kể từ lúc xác nhận đơn - `BR-SHP-002`).
- **Hành động chính:**
  - Bấm **"Tạo vận đơn giao hàng"** (Gửi yêu cầu tới 3PL API để nhận Mã vận đơn Tracking Code và Hạn shipper lấy hàng).
  - Bấm **"In Phiếu gửi hàng"** (Mở modal in nhãn dán dán lên kiện hàng có mã vạch/QR code).
  - Bấm "Hủy đơn" (Kèm lý do hết hàng/hỏng hóc, ghi nhận cảnh báo giảm điểm uy tín người bán).
- **Truy vết:** `UC-12`, `UC-13`, `US-SHP-01`, `FR-SHP-01..03`, `BR-SEC-004`, `BR-SHP-002`.

#### `SCR-SEL-07`: Doanh thu Tạm giữ & Đối soát Payout (Settlement & Payout)
- **Mục đích:** Minh bạch hóa dòng tiền bán hàng, thời gian giải tỏa tiền tạm giữ và lịch sử các đợt đối soát chuyển khoản.
- **Dữ liệu hiển thị:**
  - Số dư tiền đang trong giai đoạn tạm giữ bảo vệ người mua (Escrow Balance).
  - Danh sách từng đơn hàng kèm thời gian giải tỏa dự kiến (Đơn giao thành công + 72 giờ không có khiếu nại).
  - Lịch sử các đợt thanh toán định kỳ (`Batch Payout`): Ngày chuyển, Mã tham chiếu ngân hàng, Số tiền chuyển khoản, Trạng thái thành công/thất bại.
- **Hành động chính:** Xuất báo cáo doanh thu ra file Excel/CSV; Cập nhật tài khoản ngân hàng thụ hưởng.
- **Truy vết:** `UC-15`, `US-PAY-03`, `FR-PAY-07..09`.

---

### 3.3. Phân Hệ Admin / Back-office (`SCR-ADM-01..08`)

#### `SCR-ADM-01`: Hàng đợi Tin & Báo cáo Vi phạm (Moderation Queue)
- **Mục đích:** Cung cấp hàng đợi tập trung các tin đăng cần thẩm định trước khi xuất bản hoặc bị cộng đồng báo cáo vi phạm.
- **Dữ liệu hiển thị:** Danh sách tin chia theo 2 tab:
  1. *Tin nghi vấn tự động:* Bị hệ thống gắn cờ do chứa từ khóa nhạy cảm hoặc người bán mới tạo.
  2. *Tin bị người dùng báo cáo:* Số lượt báo cáo vi phạm, lý do báo cáo phổ biến (Hàng giả, Lừa đảo, Sai giá).
- **Hành động chính:** Chọn tin để mở màn hình thẩm định chi tiết (`SCR-ADM-02`); Phân công hồ sơ cho kiểm duyệt viên khác; Lọc theo mức độ ưu tiên/thời gian chờ.
- **Truy vết:** `UC-20`, `UC-21`, `US-TRU-01`, `FR-TRU-01..03`.

#### `SCR-ADM-02`: Thẩm định Chi tiết Tin đăng (Listing Moderation Review)
- **Mục đích:** Kiểm duyệt viên (`ROLE-03`) thẩm định nội dung, hình ảnh và đưa ra phán quyết phê duyệt hoặc gỡ bỏ tin vi phạm.
- **Dữ liệu hiển thị:** Tiêu đề, danh mục, hình ảnh, văn bản mô tả (từ khóa vi phạm được bôi nền vàng/đỏ); Số Serial/IMEI; Lịch sử vi phạm của tài khoản người bán. **Không hiển thị bất kỳ thông tin thẻ ngân hàng hoặc dữ liệu thanh toán** (`BR-SEC-003`).
- **Hành động chính:**
  - Nút **"Phê duyệt (Approve)"**: Cho phép tin xuất bản công khai lên sàn.
  - Nút **"Từ chối (Reject)"**: Bắt buộc chọn mã lý do vi phạm (Hình ảnh mờ, Nghi vấn hàng giả, Từ khóa cấm) và nhập hướng dẫn sửa đổi gửi cho Seller.
  - Nút **"Khóa tin & Cảnh cáo Seller"**: Áp dụng với các trường hợp vi phạm nghiêm trọng.
- **Truy vết:** `UC-20`, `UC-21`, `US-TRU-01`, `FR-TRU-01`, `FR-LST-09`, `BR-SEC-003`.

#### `SCR-ADM-03`: Bàn Tra cứu Giao dịch Đa miền 360° (Unified Transaction Lookup)
- **Mục đích:** Công cụ trung tâm của Chuyên viên Hỗ trợ Khách hàng (`ROLE-04`) để tra cứu, chẩn đoán sự cố giao dịch và lịch sử tương tác của người dùng.
- **Dữ liệu hiển thị:**
  - Thanh tìm kiếm thông minh: Tìm theo Mã Đơn hàng (`ORD-*`), Mã Tin đăng (`LST-*`), Mã Giao dịch Cổng TT (`TXN-*`), Mã Vận đơn (`TRK-*`), SĐT hoặc Email người dùng.
  - **Khối Đối chiếu 3 Miền Độc lập:** Trình bày song song 3 cột trạng thái:
    1. *Miền Đơn hàng (Order State):* `Created`, `Confirmed`, `Completed`, `Cancelled`, `Refunded`.
    2. *Miền Thanh toán (Payment State):* `Pending`, `Authorized`, `Captured`, `Refunded`, `Failed`.
    3. *Miền Vận chuyển (Shipment State):* `Pending`, `PickingUp`, `InTransit`, `Delivered`, `DeliveryFailed`.
    - **Cảnh báo Lệch pha (Sync Status Indicator):** Hiển thị cảnh báo đỏ nếu `Payment = Captured` nhưng `Order = Created` do lỗi mạng hoặc timeout webhook.
  - **Khối Dữ liệu Nhạy cảm (PII):** Mặc định hiển thị dạng che (`090***1234`). Kèm biểu tượng ổ khóa và nút **"Mở khóa xem PII (Có ghi Audit)"**.
  - **Timeline Nhật Ký Sự Kiện Bất Biến (Event History Timeline):** Danh sách toàn bộ các sự kiện domain xảy ra theo thứ tự thời gian mili-giây.
- **Hành động chính:** Bấm mở khóa PII (Mở `MOD-PII-01`); Bấm "Đồng bộ lại trạng thái cổng thanh toán"; Bấm "Tạo hồ sơ Khiếu nại hỗ trợ".
- **Truy vết:** `UC-22`, `UC-24`, `US-SUP-01`, `FR-SUP-01..03`, `NFR-USA-03`, `NFR-SEC-04`.

#### `SCR-ADM-04`: Phân xử Khiếu nại & Ra lệnh Hoàn tiền (Dispute Resolution & Refund)
- **Mục đích:** Chuyên viên Hỗ trợ (`ROLE-04`) phân xử hồ sơ tranh chấp giữa Người mua và Người bán dựa trên chứng cứ thực tế.
- **Dữ liệu hiển thị:**
  - Bảng so sánh song song chứng cứ: Cột trái là Mô tả & Ảnh lúc Seller rao bán; Cột phải là Khiếu nại & Ảnh/Video lúc Buyer nhận hàng.
  - Snapshot sản phẩm và Bảng phân tích chi phí đơn hàng.
  - Lịch sử thương lượng giữa 2 bên trước khi yêu cầu sàn can thiệp.
- **Hành động chính:**
  - Nút **"Bác bỏ khiếu nại (Đóng tranh chấp)"**: Giải phóng tiền cho Seller tiếp tục quy trình giải ngân.
  - Nút **"Chấp thuận Yêu cầu Trả hàng"**: Sinh mã vận đơn chiều ngược để Buyer gửi trả hàng lại cho Seller.
  - Nút **"Ra lệnh Hoàn tiền (Issue Refund)"**: Bắt buộc chọn mức hoàn (Toàn phần 100% hoặc Một phần) và nhập văn bản giải trình lý do. Hệ thống kích hoạt khóa chống bấm trùng (Idempotent button) và gửi lệnh hoàn tiền sang Cổng thanh toán.
- **Truy vết:** `UC-17`, `UC-23`, `US-DSP-02`, `FR-DSP-02..05`, `BR-DSP-003`, `BR-PAY-008`.

#### `SCR-ADM-05`: Quản lý Cây Danh mục & Thuộc tính (Taxonomy & Dynamic Attributes)
- **Mục đích:** Quản trị viên Nghiệp vụ (`ROLE-05`) thiết lập và bảo trì hệ thống danh mục sản phẩm cùng các trường thông tin đặc thù.
- **Dữ liệu hiển thị:** Cây danh mục phân cấp dạng thư mục (Folder tree); Bảng thuộc tính gắn liền với từng danh mục lá (Tên thuộc tính, Kiểu dữ liệu: Text/Number/Dropdown/Multi-select, Bắt buộc điền hay không, Có dùng làm bộ lọc tìm kiếm hay không).
- **Hành động chính:** Thêm danh mục cha/con; Đổi tên; Kéo thả sắp xếp thứ tự; Thêm trường thuộc tính động mới; Bật/tắt trạng thái hoạt động của danh mục.
- **Truy vết:** `UC-25`, `US-ADM-01`, `FR-ADM-01..03`.

#### `SCR-ADM-06`: Quản lý Từ khóa Cấm & Chính sách (Prohibited Keywords & Policies)
- **Mục đích:** Cấu hình danh mục từ khóa bị cấm đăng bán và các tham số chính sách vận hành của sàn.
- **Dữ liệu hiển thị:** Danh sách từ khóa cấm phân nhóm (Hàng quốc cấm, Vũ khí, Hàng giả/nhái, Từ ngữ thô tục); Cấu hình tỷ lệ Phí bảo vệ người mua (%) và Phí hoa hồng nền tảng (%); Cấu hình thời gian giữ hàng mặc định (15 phút) và thời hạn giải ngân tiền bán (72 giờ).
- **Hành động chính:** Thêm/xóa từ khóa cấm; Tải lên danh sách từ khóa hàng loạt qua file CSV; Cập nhật tỷ lệ phí kèm nhật ký phê duyệt nghiệp vụ.
- **Truy vết:** `UC-25`, `US-ADM-02`, `FR-ADM-04..06`, `BR-LST-002`, `BR-PAY-010`.

#### `SCR-ADM-07`: Quản lý Tài khoản & Phân quyền (User & Role Management)
- **Mục đích:** Quản lý trạng thái tài khoản người dùng ngoài sàn và phân quyền truy cập cho nhân viên vận hành nội bộ.
- **Dữ liệu hiển thị:** Danh sách tài khoản người dùng; Bộ lọc theo vai trò (`ROLE-01` đến `ROLE-06`); Trạng thái tài khoản (Hoạt động, Tạm khóa, Khóa vĩnh viễn); Lịch sử vi phạm và điểm uy tín.
- **Hành động chính:** Khóa/Mở khóa tài khoản người dùng kèm ghi rõ lý do; Gán vai trò nội bộ (`ROLE-03`, `ROLE-04`, `ROLE-05`) cho nhân sự; Thu hồi quyền truy cập khi nhân sự chuyển công tác.
- **Truy vết:** `UC-25`, `US-ADM-03`, `FR-ADM-07..09`.

#### `SCR-ADM-08`: Giám sát Lệch pha & Báo cáo DR (Platform Health & Sync Monitor)
- **Mục đích:** Cung cấp cho Kỹ sư Vận hành (`ROLE-06`) cái nhìn trực quan về tính toàn vẹn dữ liệu phân tán và tình trạng sẵn sàng phục hồi thảm họa.
- **Dữ liệu hiển thị:**
  - Bảng cảnh báo đơn hàng lệch pha trạng thái (`Out-of-sync Orders`): Danh sách đơn hàng có `Payment = Captured` nhưng `Order != Confirmed` quá 60 giây.
  - Trạng thái hàng đợi sự kiện bất đồng bộ (Outbox Queue lag, Dead Letter Queue size).
  - Trạng thái các bản sao lưu tự động (Automated Backups / Snapshots của SQL Server và MongoDB), thời điểm sao lưu gần nhất và cam kết chỉ số RPO/RTO hiện tại (`NFR-REC-01`).
- **Hành động chính:** Kích hoạt lệnh chạy lại đồng bộ hóa thủ công cho từng đơn lệch pha; Tải báo cáo kiểm toán sao lưu định kỳ.
- **Truy vết:** `UC-26`, `US-OPS-01`, `FR-OPS-01..03`, `NFR-REC-01`.

---

## 4. Danh Mục Các Modal & Drawer Dùng Chung

| Mã Modal | Tên Modal / Hộp Thoại | Xuất Hiện Tại | Mục Đích Nghiệp Vụ & Hành Động Chính |
| :--- | :--- | :--- | :--- |
| `MOD-AUTH-01` | **Hộp thoại Đăng nhập / Đăng ký Nhanh** | `SCR-MKT-01..04` | Xuất hiện khi người dùng vãng lai bấm "Mua ngay" hoặc "Đăng tin". Xác thực nhanh qua OTP/Social và tự động redirect về trang đích bảo toàn ngữ cảnh (`FR-CHK-01`). |
| `MOD-RPT-01` | **Hộp thoại Báo cáo Tin vi phạm** | `SCR-MKT-03` | Người dùng chọn lý do báo cáo (Lừa đảo, Hàng giả, Sai giá, Đã bán nhưng không gỡ) và gửi lên hàng đợi `SCR-ADM-01`. |
| `MOD-EXP-01` | **Cảnh báo Hết hạn Giữ hàng 15 Phút** | `SCR-MKT-06` | Xuất hiện khi đồng hồ giữ hàng về `00:00`. Thông báo sản phẩm đã được giải phóng tồn kho kèm nút "Kiểm tra lại sản phẩm". |
| `MOD-UPL-01` | **Trình tải ảnh Presigned S3 & Thử lại** | `SCR-SEL-04` | Cho phép kéo thả ảnh, hiển thị tiến trình tải từng file trực tiếp lên S3. Nút "Thử lại (Retry)" riêng cho từng ảnh bị lỗi mạng. |
| `MOD-PRN-01` | **In Phiếu Gửi Hàng / Mã Vận Đơn** | `SCR-SEL-06` | Hiển thị phiếu in chuẩn khổ A6/A5 chứa Mã vạch 3PL, Địa chỉ người gửi/người nhận và mã đối soát đơn hàng. |
| `MOD-PII-01` | **Yêu cầu Lý do Truy Cập Dữ liệu PII** | `SCR-ADM-03` | Bắt buộc Support Agent (`ROLE-04`) chọn lý do nghiệp vụ và nhập Ticket ID trước khi giải mã hiển thị SĐT/Địa chỉ khách hàng (`FR-SUP-02`). Ghi nhận ngay vào Audit Log. |
| `MOD-REF-01` | **Xác nhận Ra Lệnh Hoàn Tiền** | `SCR-ADM-04` | Hộp thoại cảnh báo rủi ro tài chính trước khi thực hiện hoàn tiền. Bắt buộc nhập lý do hoàn tiền và chọn phương thức hoàn. |

---

## 5. Ma Trận Bao Phủ Use Case ↔ Màn Hình (Coverage Matrix)

Bảng đối soát chứng minh toàn bộ 26 Use Cases trong `docs/BA/03_requirements/use-cases.md` được hiện thực hóa đầy đủ trên các màn hình giao diện:

| Mã Use Case | Tên Use Case | Màn Hình Chính Thực Thi | Modal Hỗ Trợ | Màn Hình Liên Quan Phụ Trợ |
| :--- | :--- | :--- | :--- | :--- |
| `UC-01` | Đăng ký tài khoản người dùng mới | `SCR-MKT-12` | `MOD-AUTH-01` | `SCR-MKT-01` |
| `UC-02` | Đăng nhập và quản lý phiên làm việc | `SCR-MKT-12` | `MOD-AUTH-01` | `SCR-MKT-01` |
| `UC-03` | Kích hoạt hồ sơ người bán (KYC) | `SCR-SEL-01` | — | `SCR-SEL-02` |
| `UC-04` | Tạo mới tin đăng đồ cũ kèm ảnh | `SCR-SEL-04` | `MOD-UPL-01` | `SCR-SEL-02`, `SCR-SEL-03` |
| `UC-05` | Cập nhật, ẩn hoặc xóa tin đăng | `SCR-SEL-03`, `SCR-SEL-04` | — | `SCR-SEL-02` |
| `UC-06` | Tìm kiếm tin đăng theo từ khóa | `SCR-MKT-02` | — | `SCR-MKT-01` |
| `UC-07` | Lọc tin đăng đồ cũ đa tiêu chí | `SCR-MKT-02` | — | `SCR-MKT-03`, `SCR-MKT-04` |
| `UC-08` | Quản lý giỏ hàng ý định mua | `SCR-MKT-05` | — | `SCR-MKT-03` |
| `UC-09` | Đặt hàng và giữ chỗ sản phẩm 15p | `SCR-MKT-06` | `MOD-EXP-01` | `SCR-MKT-03`, `SCR-MKT-05` |
| `UC-10` | Thanh toán đơn hàng qua cổng trực tuyến | `SCR-MKT-07` | — | `SCR-MKT-06` |
| `UC-11` | Người bán tiếp nhận và chuẩn bị đơn | `SCR-SEL-05`, `SCR-SEL-06` | `MOD-PRN-01` | `SCR-SEL-02` |
| `UC-12` | Quản lý và tra cứu danh sách đơn hàng | `SCR-MKT-08`, `SCR-SEL-05` | — | `SCR-MKT-09`, `SCR-SEL-06` |
| `UC-13` | Theo dõi tiến trình vận chuyển theo thời gian thực | `SCR-MKT-09`, `SCR-SEL-06` | — | `SCR-MKT-08`, `SCR-SEL-05` |
| `UC-14` | Nhận và quản lý thông báo đa kênh | `SCR-MKT-11` | — | Tất cả các màn hình (Header Bell) |
| `UC-15` | Quản lý số dư tạm giữ và nhận tiền bán | `SCR-SEL-07` | — | `SCR-SEL-02` |
| `UC-16` | Xác nhận đã nhận hàng và hoàn tất đơn | `SCR-MKT-09` | — | `SCR-MKT-08`, `SCR-SEL-07` |
| `UC-17` | Mở khiếu nại và yêu cầu trả hàng | `SCR-MKT-10` | — | `SCR-MKT-09`, `SCR-ADM-04` |
| `UC-18` | Báo cáo tin đăng hoặc người bán vi phạm | `SCR-MKT-03` | `MOD-RPT-01` | `SCR-ADM-01` |
| `UC-19` | Đánh giá và chấm điểm uy tín người bán | `SCR-MKT-10` | — | `SCR-MKT-04`, `SCR-MKT-09` |
| `UC-20` | Kiểm duyệt tin đăng đồ cũ tự động và thủ công | `SCR-ADM-01`, `SCR-ADM-02` | — | `SCR-SEL-03` |
| `UC-21` | Xử lý báo cáo vi phạm nội dung | `SCR-ADM-01`, `SCR-ADM-02` | — | `SCR-MKT-03` |
| `UC-22` | Tra cứu thông tin giao dịch 360 độ | `SCR-ADM-03` | `MOD-PII-01` | — |
| `UC-23` | Phân xử khiếu nại và thực hiện hoàn tiền | `SCR-ADM-04` | `MOD-REF-01` | `SCR-ADM-03` |
| `UC-24` | Hỗ trợ mở khóa dữ liệu nhạy cảm có ghi log | `SCR-ADM-03` | `MOD-PII-01` | — |
| `UC-25` | Quản lý danh mục, từ khóa và chính sách sàn | `SCR-ADM-05`, `SCR-ADM-06`, `SCR-ADM-07` | — | — |
| `UC-26` | Giám sát sức khỏe hệ thống và sao lưu DR | `SCR-ADM-08` | — | — |

