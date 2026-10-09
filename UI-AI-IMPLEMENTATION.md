# Tài liệu giao diện và quy trình AI thực thi — VietTrails

| Thuộc tính | Giá trị |
|---|---|
| Phiên bản tài liệu | 1.0 |
| Phạm vi | Đặc tả giao diện khách hàng hiện tại và quy trình dùng AI hỗ trợ phát triển |
| Người dùng | Designer, frontend developer, reviewer, QA |

## 1. Mục đích và giới hạn

Tài liệu này quy định giao diện cần tạo/duy trì và cách giao nhiệm vụ giao diện cho AI lập trình. “AI thực thi” ở đây là AI hỗ trợ tạo/chỉnh sửa mã nguồn theo đặc tả; **không phải** một chatbot hay tính năng AI đã được tích hợp cho khách du lịch.

AI không được tự suy diễn chính sách kinh doanh, dữ liệu thật, endpoint, vai trò hay khả năng chưa có. Nếu đầu vào thiếu quyết định ảnh hưởng hành vi, AI phải nêu giả định có giới hạn hoặc yêu cầu người phụ trách xác nhận. Mọi thay đổi do AI tạo ra cần được con người đọc, chạy kiểm tra và duyệt trước khi commit.

## 2. Ngôn ngữ hình ảnh và quy tắc UI

- Thương hiệu: VietTrails; cảm xúc khám phá Việt Nam, gần gũi, bình tĩnh và đáng tin cậy.
- Bảng màu hiện dùng: xanh lá đậm `#244b39`, nền ấm `#f8f7f4`, màu nhấn đất nung `#cf865b`, đường viền trung tính.
- Typography hiện dùng DM Sans cho nội dung và Playfair Display cho tiêu đề có tính biên tập.
- Card ảnh dùng crop thống nhất, thông tin giá/thời lượng dễ quét; CTA chính phân biệt rõ với hành động phụ.
- Responsive theo cấu trúc CSS hiện có; kiểm tra desktop, tablet và mobile (đặc biệt chiều rộng 360–650px).
- Duy trì nội dung tiếng Việt tự nhiên, định dạng giá theo VND và ngày theo locale `vi-VN`.
- Không thêm thư viện UI hoặc hệ thống thiết kế mới khi CSS/component hiện có đáp ứng được yêu cầu.

## 3. Bản đồ màn hình và trạng thái

| Route | Mục đích | Thành phần/chuyển trạng thái |
|---|---|---|
| `/` | Giới thiệu, khám phá tour | Header, hero/search, tour nổi bật, khu vực, CTA, footer |
| `/tours` | Duyệt tour | Ô tìm kiếm, chips danh mục, lọc vùng/giá, sắp xếp, grid, trạng thái không có kết quả |
| `/tours/:id` | Thông tin và bắt đầu đặt | Ảnh, mô tả, lịch trình, giá, chọn ngày/số người, tổng tiền; tour không tồn tại có đường quay lại |
| `/booking` | Gửi yêu cầu giữ chỗ | Tóm tắt đơn, thông tin liên hệ, lỗi xác thực, xác nhận thành công |
| `/my-bookings` | Xem các đơn đã lưu | Danh sách mã đơn/trạng thái/tổng tiền hoặc empty state |
| `/profile` | Sửa thông tin liên hệ | Form, lỗi, trạng thái đã lưu |
| `*` | Route không xác định | Thông báo thân thiện và liên kết về trang chủ |

`/destinations` và `/categories` hiện được điều hướng tới trang danh sách tour; chưa phải trang phân loại riêng. Header/footer dùng chung được khai báo trong các component hiện có.

## 4. Quy tắc tương tác

### Tìm kiếm và lọc

- Tìm kiếm từ trang chủ chuyển tới `/tours?location=...`; truy vấn phải được mã hóa URL.
- Kết quả khớp trên tên tour, địa điểm hoặc vùng miền, không phân biệt hoa thường.
- Bộ lọc vùng, danh mục và giá áp dụng đồng thời; sắp xếp chỉ thay đổi thứ tự, không làm mất điều kiện lọc.
- Khi không có kết quả, hiển thị giải thích và hành động xóa bộ lọc.

### Chi tiết và đặt tour

- Ngày phải không sớm hơn ngày hiện tại; không cho số khách nhỏ hơn 1.
- Giá tổng luôn hiển thị theo `đơn giá × số khách`.
- Tiếp tục đặt phải chuyển các giá trị đã chọn qua URL hoặc state an toàn phù hợp với kiến trúc.
- Biểu mẫu liên hệ nêu rõ trường bắt buộc, lỗi email/điện thoại cụ thể; giữ nguyên dữ liệu người dùng khi có lỗi.
- Sau khi lưu thành công mới hiển thị màn hình xác nhận. Khi tích hợp API, loading/error phải được thể hiện và lỗi mạng không được báo thành công.
- Thông báo trạng thái dùng semantics phù hợp (`role="alert"`/`role="status"` khi cần) và không chỉ dựa vào màu sắc.

### Responsive và accessibility

- Menu mobile mở/đóng có tên truy cập rõ, trạng thái mở phản ánh được cho công nghệ hỗ trợ.
- Tất cả điều khiển hoạt động bằng bàn phím; focus không bị che bởi header sticky.
- Ảnh nội dung có alt hữu ích; ảnh trang trí dùng alt rỗng khi thích hợp.
- Nút icon-only cần accessible name; thẻ form dùng label liên kết với input.
- Không dựa vào hover là cách duy nhất để phát hiện hành động.

## 5. Quy trình giao nhiệm vụ cho AI

1. **Đọc bối cảnh:** README, PRD, component/style liên quan và trạng thái Git hiện tại.
2. **Xác định phạm vi:** route, hành vi, dữ liệu, trạng thái rỗng/lỗi/loading, responsive và tiêu chí nghiệm thu.
3. **Lập thay đổi tối thiểu:** tái sử dụng component/token hiện có; không tạo backend giả hoặc dữ liệu giả dạng production.
4. **Thực thi:** chỉnh sửa mã trong các file thuộc phạm vi; không tự thay đổi nội dung nghiệp vụ không được yêu cầu.
5. **Kiểm tra:** chạy `npm run build`; nếu có test liên quan, chạy test mục tiêu; kiểm tra giao diện/console ở route bị ảnh hưởng.
6. **Review:** người phụ trách so sánh kết quả với đặc tả, kiểm tra accessibility, dữ liệu và thay đổi Git.
7. **Bàn giao:** tóm tắt file thay đổi, hành vi, lệnh kiểm tra và phần còn thiếu; không tuyên bố test đã chạy nếu chưa chạy.

### Đầu vào bắt buộc cho một yêu cầu UI

- Mục tiêu người dùng và route.
- Trạng thái hiện tại và kết quả mong muốn.
- Quy tắc dữ liệu/validation.
- Các trạng thái loading, success, empty, error.
- Viewport/thiết bị và yêu cầu accessibility.
- Ràng buộc thư viện, kiến trúc, file không được sửa.
- Tiêu chí nghiệm thu có thể quan sát/kiểm thử.

### Mẫu prompt cho AI coding

```text
Bạn đang làm việc trong ứng dụng VietTrails (React 19, TypeScript, Vite,
React Router, Zustand). Trước khi sửa, đọc tài liệu docs/PRD.md,
docs/UI-AI-IMPLEMENTATION.md và các file liên quan.

Mục tiêu: [mục tiêu người dùng]
Route/phạm vi: [route, component, file liên quan]
Hành vi mong muốn: [quy tắc cụ thể]
Trạng thái cần xử lý: [loading / empty / error / success]
Responsive/accessibility: [yêu cầu]
Không làm: [ngoài phạm vi]
Tiêu chí nghiệm thu:
- [điều kiện quan sát/đo được]
- [điều kiện lỗi/biên]

Tái sử dụng pattern hiện có, không bịa API hoặc chức năng production.
Thực hiện thay đổi, chạy npm run build (và test mục tiêu nếu có), sau đó
báo rõ file đã đổi, kết quả lệnh và giới hạn còn lại.
```

## 6. Kiểm tra chấp nhận giao diện

- Route đích mở được trực tiếp và điều hướng nội bộ đúng.
- Tìm kiếm, lọc, sắp xếp và xóa lọc giữ hành vi theo PRD.
- Tính tiền không lệch giữa chi tiết, biểu mẫu và xác nhận.
- Biểu mẫu không cho gửi dữ liệu thiếu/sai; người dùng nhìn thấy lỗi dễ hiểu.
- Trạng thái rỗng, thành công và lỗi không gây hiểu nhầm.
- Bố cục không tràn ngang ở 360px; điều khiển thao tác được trên touch và bàn phím.
- Không có lỗi TypeScript/build; không phát sinh console error liên quan.
- Không đưa secret, dữ liệu cá nhân thật hoặc prompt chứa bí mật vào source/commit.

## 7. Định hướng giao diện quản trị

Admin là phạm vi chưa được triển khai, không có route hay component admin trong ứng dụng hiện tại. Khi xây dựng cần dùng layout riêng gồm điều hướng quản trị, trang tổng quan, danh sách và form CRUD; yêu cầu cụ thể nằm trong phần Admin của [tài liệu kỹ thuật](./TECHNICAL-DOCUMENTATION.md). UI không được xem việc ẩn nút là biện pháp phân quyền; API phải kiểm tra quyền cho mọi thao tác.

