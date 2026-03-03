# FE-02: Quản Lý Sân & Hạ Tầng — Đặc Tả Chức Năng

---

## 1. Mô tả chung

Module **FE-02** là module quản lý toàn bộ hạ tầng sân cầu lông, bao gồm: thêm/sửa/xóa sân, phân loại sân, cấu hình giá động, xem sơ đồ mặt bằng thời gian thực, lập lịch bảo trì, xem lịch sử sử dụng, tìm kiếm sân và quản lý thiết bị tồn kho.

Module này đóng vai trò **lõi vận hành** của hệ thống, kết nối trực tiếp với module đặt sân (FE-03), module thanh toán (FE-04) và module thông báo. Mọi thay đổi về trạng thái sân, lịch bảo trì hay giá đều ảnh hưởng tức thời đến trải nghiệm người dùng và khả năng đặt sân.

---

## 2. Actor / Roles

| Role | Mô tả | Quyền hạn |
|---|---|---|
| **Admin** | Quản trị viên hệ thống | Toàn quyền: thêm/sửa/xóa sân, cấu hình giá, lên lịch bảo trì, xem báo cáo |
| **Staff (Nhân viên)** | Nhân viên quầy lễ tân / vận hành | Xem sơ đồ mặt bằng, quản lý tồn kho thiết bị, hỗ trợ tìm kiếm sân cho khách |
| **User (Khách hàng)** | Người dùng cuối | Tìm kiếm và xem thông tin sân, không can thiệp được vào cấu hình hệ thống |

---

## 3. Purpose (Mục tiêu)

| Mục tiêu | Chi tiết |
|---|---|
| **Quản lý tập trung** | Tất cả thông tin sân (tên, loại, giá, trạng thái, ảnh) được quản lý tại một nơi duy nhất |
| **Giá động linh hoạt** | Hỗ trợ nhiều mức giá (giờ thấp điểm, giờ cao điểm, giờ vàng) và bật/tắt Giờ Vàng theo từng giai đoạn |
| **Minh bạch vận hành** | Sơ đồ mặt bằng real-time giúp nhân viên biết sân nào đang dùng, đang bảo trì hay sẵn sàng |
| **Bảo trì chủ động** | Lên lịch bảo trì trước, tự động cảnh báo các booking bị ảnh hưởng để sắp xếp lại |
| **Kiểm soát tồn kho** | Theo dõi thiết bị theo từng vị trí, cảnh báo khi số lượng dưới ngưỡng tối thiểu |
| **Phân tích kinh doanh** | Lịch sử sử dụng sân giúp Admin nhận diện sân phổ biến, giờ cao điểm và doanh thu |

---

## 4. Interface (Giao diện)

### 4.1 Admin — Courts List (`/admin/courts`)
- Bảng danh sách sân với cột: Tên sân, Loại, Trạng thái, Giá/giờ, Hành động
- Nút **Thêm sân** → mở Modal nhập tên, vị trí, loại, tiện ích, ảnh
- Nút **Sửa** → mở Modal chỉnh sửa các trường tương ứng
- Nút **Xóa** → xác nhận xóa sân (disabled nếu sân đang có booking active)
- Badge trạng thái màu: `Xanh = Sẵn sàng`, `Vàng = Đang dùng`, `Đỏ = Bảo trì`, `Xám = Đóng cửa`

### 4.2 Admin — Pricing (`/admin/courts/pricing`)
- Ma trận giờ × mức giá (17 khung giờ từ 06:00 đến 23:00)
- Toggle bật/tắt **Giờ Vàng** → tự động nhân giá +10% tất cả khung giờ
- Chỉnh trực tiếp giá từng tier (Thấp điểm / Cao điểm / Giờ VIP)
- Preview bảng tóm tắt giá sau khi chỉnh

### 4.3 Admin — Floor Plan (`/admin/courts/floor-plan`)
- Lưới sân hình chữ nhật, màu phân biệt theo trạng thái
- Click vào sân → Modal hiển thị thông tin booking hiện tại (khách hàng, giờ, SĐT)
- Dropdown đổi trạng thái sân trực tiếp từ Modal
- Refresh tự động mỗi 30 giây (giả lập real-time)

### 4.4 Admin — Maintenance (`/admin/courts/maintenance`)
- Danh sách lịch bảo trì với cột: Sân, Ngày, Giờ, Kỹ thuật viên, Chi phí, Trạng thái
- Nút **Thêm lịch** → Form chọn sân, ngày, giờ bắt đầu, thời lượng, ghi chú
- Nếu có booking bị trùng → hiển thị Alert cảnh báo danh sách booking bị ảnh hưởng
- Bộ lọc theo Sân / Trạng thái bảo trì

### 4.5 Admin — Court Usage History (`/admin/courts/history`)
- Bảng lịch sử sử dụng có phân trang
- Bộ lọc: Sân, Khoảng ngày, Loại sân
- Thẻ thống kê tổng hợp: Tổng lượt, Tổng doanh thu, Thời lượng TB, Sân phổ biến nhất
- Biểu đồ cột (BarChart) doanh thu theo từng sân

### 4.6 Staff — Equipment Inventory (`/staff/equipment`)
- Bảng thiết bị với Badge trạng thái: `Đủ hàng`, `Sắp hết`, `Hết hàng`
- Banner Danger nổi bật khi có mục hết hàng hoàn toàn
- Nút Sửa nhanh số lượng tồn kho ngay trong bảng
- Form Báo hỏng → ghi nhận số lượng thiết bị hư hỏng và tự trừ kho

### 4.7 User — Dashboard (`/user/dashboard`)
- Form tìm kiếm sân nâng cao: ngày, khung giờ, loại sân
- Kết quả hiển thị dạng thẻ với giá, tiện ích, trạng thái
- Nút **Đặt ngay** chuyển đến quy trình đặt sân (FE-03)

---

## 5. Data Processing (Xử lý dữ liệu)

| Thao tác | Luồng xử lý |
|---|---|
| Thêm sân | Nhập dữ liệu → Validate trùng tên → Lưu vào DB → Cập nhật danh sách sân và sơ đồ mặt bằng |
| Cấu hình giá | Admin thay đổi tier/toggle → Hệ thống tính lại toàn bộ priceMatrix → Lưu → Áp dụng ngay cho booking mới |
| Lên lịch bảo trì | Admin chọn sân + thời gian → Kiểm tra trùng booking → Cảnh báo nếu có → Lưu → Đổi trạng thái sân → Gửi thông báo cho khách bị ảnh hưởng |
| Cập nhật tồn kho | Staff nhập số lượng điều chỉnh → Kiểm tra >= 0 → Cập nhật → So sánh với minQuantity → Kích hoạt cảnh báo nếu cần |
| Xem lịch sử | Lấy UsageRecord theo bộ lọc → Tính tổng hợp (session count, revenue) → Render bảng và biểu đồ |

---

## 6. Function Detail (Chi tiết chức năng)

### FE-02.1 — Thêm / Sửa Sân
- Validate tên sân không trùng với sân hiện có (case-insensitive)
- Bắt buộc: Tên, Loại sân, Giá/giờ
- Tùy chọn: Mô tả, Văn phòng/Vị trí, Danh sách tiện ích (checkbox)
- Sau khi lưu thành công: đóng Modal, cập nhật bảng, hiện Toast thành công

### FE-02.2 — Upload ảnh sân
- Cho phép upload tối đa 5 ảnh/sân (JPG, PNG, WebP)
- Preview ảnh trước khi lưu
- Hiển thị ảnh đầu tiên làm thumbnail trong danh sách

### FE-02.3 — Phân loại sân
- Loại sân: `Standard` | `VIP` | `Double` (Sân đôi)
- Badge màu khác nhau cho từng loại trong bảng và sơ đồ
- Giá VIP mặc định nhân 1.5× giá Standard

### FE-02.4 — Cấu hình giá động
- 3 mức giá: Thấp điểm (06-09h, 14-17h), Cao điểm (17-21h), Giờ VIP (09-14h, 21-23h)
- Toggle Giờ Vàng áp dụng thêm +10% toàn bộ khung giờ
- Admin có thể chỉnh tay từng mức giá; hệ thống tự cập nhật priceMatrix

### FE-02.5 — Sơ đồ mặt bằng real-time
- Hiển thị tất cả sân trên lưới 2D
- Màu: `Xanh = available`, `Xanh dương = in_use`, `Đỏ = maintenance`, `Xám = closed`
- Click sân → Modal: thông tin booking, SĐT khách, giờ bắt đầu/kết thúc
- Cho phép đổi trạng thái sân trực tiếp từ Modal (Admin/Staff)

### FE-02.6 — Lịch bảo trì
- Tạo lịch bảo trì: chọn sân, ngày, giờ, thời lượng, kỹ thuật viên, ước tính chi phí
- Auto-detect booking trùng giờ → hiển thị danh sách cụ thể
- Sau khi lưu: sân tự chuyển sang trạng thái `maintenance`
- Khi hoàn thành: sân tự chuyển về `available`

### FE-02.7 — Tìm kiếm sân nâng cao (User/Staff)
- Bộ lọc: Ngày, Khung giờ, Loại sân, Từ khóa
- Kết quả chỉ hiển thị sân `available` trong khung giờ đã chọn
- Sắp xếp theo giá tăng dần mặc định

### FE-02.8 — Sơ đồ mặt bằng real-time (xem bên trên FE-02.5)

### FE-02.9 — Lịch sử sử dụng sân
- Phân trang 10 bản ghi/trang
- Xuất dữ liệu (chuẩn bị cho kết nối API)
- Thống kê: Tổng lượt dùng, Doanh thu, Thời lượng TB, Sân hot nhất, Giờ hot nhất

### FE-02.10 — Quản lý tồn kho thiết bị (Staff)
- Cảnh báo tự động khi `quantity < minQuantity`
- Banner Danger nổi bật khi `quantity === 0`
- Sửa nhanh số lượng tồn kho trực tiếp trên bảng
- Form báo hỏng: nhập số lượng hỏng → tự trừ vào kho

---

## 7. Validation (Kiểm tra đầu vào)

| Trường | Quy tắc |
|---|---|
| Tên sân | Bắt buộc, tối đa 100 ký tự, không trùng tên sân đã có |
| Loại sân | Bắt buộc, chỉ chấp nhận: Standard / VIP / Double |
| Giá/giờ | Bắt buộc, số dương, tối thiểu 10.000 VNĐ |
| Ảnh sân | JPG / PNG / WebP, tối đa 5MB/ảnh, tối đa 5 ảnh |
| Ngày bảo trì | Phải >= ngày hiện tại |
| Thời lượng bảo trì | Tối thiểu 1 giờ, tối đa 24 giờ |
| Số lượng tồn kho | Không âm (>= 0) |
| Ngày tìm kiếm | Phải >= ngày hiện tại |

---

## 8. Business Rules (Quy tắc nghiệp vụ)

1. **Không xóa sân có booking active:** Sân đang có booking chưa hoàn thành không được phép xóa.
2. **Bảo trì ưu tiên hơn booking:** Khi tạo lịch bảo trì trùng giờ, hệ thống cảnh báo nhưng vẫn cho phép tạo; Admin/Staff phải liên hệ khách để hủy booking thủ công.
3. **Giá áp dụng ngay:** Mọi thay đổi cấu hình giá có hiệu lực với **booking mới**; booking đã xác nhận trước đó không bị thay đổi giá.
4. **Toggle Giờ Vàng là toàn cục:** Bật/tắt Giờ Vàng áp dụng cho **tất cả sân**, không thể cấu hình riêng từng sân.
5. **Sân bảo trì không thể đặt:** Sân ở trạng thái `maintenance` hoặc `closed` không xuất hiện trong kết quả tìm kiếm của User.
6. **Tồn kho âm bị ngăn chặn:** Hệ thống không cho phép lưu số lượng kho âm dù Admin/Staff nhập tay.
7. **Phân quyền rõ ràng:** User không thể truy cập trang Cấu hình giá, Bảo trì, hay Sơ đồ mặt bằng đầy đủ; Staff không thể xóa hoặc tạo sân mới.

---

## 9. Functionalities (Danh sách chức năng)

| Mã | Tên chức năng | Dành cho |
|---|---|---|
| FE-02.1 | Thêm / Sửa sân với kiểm tra trùng tên | Admin |
| FE-02.2 | Upload và quản lý ảnh sân | Admin |
| FE-02.3 | Phân loại sân (VIP / Standard / Double) | Admin |
| FE-02.4 | Cấu hình giá động và Giờ Vàng | Admin |
| FE-02.5 | Xem sơ đồ mặt bằng real-time + đổi trạng thái | Admin / Staff |
| FE-02.6 | Quản lý lịch bảo trì + cảnh báo booking bị ảnh hưởng | Admin |
| FE-02.7 | Tìm kiếm sân nâng cao (theo ngày, giờ, loại) | User / Staff |
| FE-02.8 | Sơ đồ mặt bằng xem nhanh (Staff) | Staff |
| FE-02.9 | Xem lịch sử sử dụng sân + thống kê | Admin |
| FE-02.10 | Quản lý thiết bị tồn kho + cảnh báo sắp hết | Staff |

---

## 10. Normal Cases (Trường hợp thông thường)

| Trường hợp | Kết quả mong đợi |
|---|---|
| Admin thêm sân với tên chưa tồn tại | Sân được lưu thành công, xuất hiện trong danh sách và sơ đồ mặt bằng |
| Admin bật Giờ Vàng | Toàn bộ priceMatrix cập nhật ngay, các booking mới áp dụng giá mới |
| Admin tạo lịch bảo trì cho sân trống lịch | Lịch được lưu, sân chuyển trạng thái `maintenance`, không có cảnh báo booking |
| Staff cập nhật tồn kho tăng lên | Số lượng được lưu, nếu vượt minQuantity thì Alert cảnh báo biến mất |
| User tìm kiếm sân vào ngày/giờ còn trống | Hiển thị danh sách sân available, có thể bấm Đặt ngay |
| Admin xem lịch sử theo bộ lọc | Hiển thị đúng bản ghi khớp điều kiện lọc, thống kê cập nhật theo kết quả |

---

## 11. Abnormal Cases (Trường hợp bất thường)

| Trường hợp | Xử lý hệ thống |
|---|---|
| Admin thêm sân với tên đã tồn tại | Hiển thị thông báo lỗi "Tên sân đã tồn tại", không lưu, giữ nguyên form |
| Admin xóa sân đang có booking active | Nút Xóa bị disabled, tooltip giải thích lý do |
| Admin tạo bảo trì trùng lịch booking | Hiển thị Alert cảnh báo danh sách booking bị ảnh hưởng, vẫn cho phép lưu sau khi xác nhận |
| Staff nhập tồn kho âm | Validation ngăn lưu, hiển thị "Số lượng không được nhỏ hơn 0" |
| User tìm kiếm sân vào ngày/giờ không có sân nào | Hiển thị thông báo "Không tìm thấy sân phù hợp" |
| Mất kết nối API khi tải sơ đồ mặt bằng | Hiển thị Skeleton loading + thông báo "Không thể tải dữ liệu. Thử lại sau." |
| Upload ảnh vượt quá 5MB | Thông báo lỗi "Ảnh quá lớn (tối đa 5MB)", không thêm vào danh sách |
| Upload ảnh sai định dạng | Thông báo "Chỉ hỗ trợ JPG, PNG, WebP" |
| Giá nhập vào là 0 hoặc âm | Validation trường giá, ngăn lưu và hiển thị "Giá phải lớn hơn 0" |
| Ngày bảo trì được chọn là ngày trong quá khứ | Date picker ngăn chọn, hoặc validate khi submit form |
