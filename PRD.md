# Tài liệu yêu cầu nghiệp vụ (PRD) — VietTrails

| Thuộc tính | Giá trị |
|---|---|
| Sản phẩm | VietTrails — nền tảng khám phá và đặt tour du lịch Việt Nam |
| Phiên bản tài liệu | 1.0 |
| Trạng thái | Đặc tả cho bản frontend hiện tại và định hướng mở rộng |
| Đối tượng đọc | Chủ sản phẩm, thiết kế, frontend, backend, QA và quản trị |

## 1. Tổng quan

VietTrails giúp khách du lịch khám phá tour theo địa điểm, vùng miền và loại hình; xem lịch trình, giá và ngày khởi hành; gửi yêu cầu giữ chỗ; sau đó xem lại chuyến đi và cập nhật thông tin liên hệ.

Ứng dụng hiện là bản frontend minh họa. Dữ liệu tour là dữ liệu mẫu được đóng gói trong mã nguồn; hồ sơ và đặt chỗ được lưu trong trình duyệt. Chưa có API, xác thực người dùng, thanh toán trực tuyến hoặc giao diện quản trị vận hành thực tế. Tài liệu này phân biệt rõ chức năng có trong bản hiện tại với phần cần xây dựng để đưa vào vận hành.

## 2. Mục tiêu và chỉ số thành công

### Mục tiêu

1. Giúp khách tìm được tour phù hợp nhanh chóng.
2. Trình bày đủ thông tin để khách đánh giá chuyến đi trước khi đặt.
3. Thu thập thông tin liên hệ và ghi nhận yêu cầu đặt tour với tổng tiền minh bạch.
4. Tạo nền tảng để nhân viên quản trị tour và xử lý đơn đặt trong giai đoạn tiếp theo.

### Chỉ số đề xuất

- Tỷ lệ khách mở trang chi tiết sau khi xem danh sách tour.
- Tỷ lệ hoàn thành biểu mẫu đặt chỗ trên số lượt bắt đầu đặt.
- Tỷ lệ lỗi xác thực biểu mẫu và tỷ lệ lỗi khi tải/lưu dữ liệu.
- Thời gian tải nội dung chính trên thiết bị di động.
- Với admin: thời gian xử lý đơn và tỷ lệ đơn được xử lý đúng trạng thái.

Các chỉ số cần được gắn phân tích trước khi đo; bản hiện tại chưa tích hợp analytics.

## 3. Đối tượng sử dụng và quyền hạn

| Vai trò | Nhu cầu | Quyền hạn mục tiêu |
|---|---|---|
| Khách truy cập/khách du lịch | Tìm tour, xem chi tiết, gửi yêu cầu đặt tour | Đọc thông tin công khai; tạo và xem đặt chỗ của chính mình sau khi có xác thực |
| Quản trị viên | Cập nhật tour, địa điểm, danh mục, người dùng và đơn đặt | Quản lý dữ liệu theo quyền được cấp; mọi thao tác thay đổi dữ liệu phải được kiểm tra và ghi nhận |
| Nhân viên xử lý đặt chỗ (đề xuất) | Xác nhận, liên hệ và cập nhật tình trạng đặt chỗ | Chỉ xem/cập nhật đơn đặt chỗ, không quản lý tài khoản quản trị |

Ở bản hiện tại chưa có đăng nhập hoặc phân quyền; không xem các trang hồ sơ/đặt chỗ trên trình duyệt là ranh giới bảo mật.

## 4. Phạm vi sản phẩm

### Có trong frontend hiện tại

- Trang chủ với tour nổi bật, khu vực điểm đến và đường dẫn khám phá.
- Danh sách tour có tìm kiếm theo tên/điểm đến/vùng, lọc danh mục, vùng miền và giá tối đa, sắp xếp theo giá hoặc độ nổi bật.
- Trang chi tiết tour gồm mô tả, lịch trình, đánh giá, giá, ngày khởi hành và số người.
- Biểu mẫu giữ chỗ, xác thực dữ liệu liên hệ, tính tổng tiền, màn hình xác nhận.
- Danh sách đặt chỗ và biểu mẫu cập nhật thông tin cá nhân.
- Điều hướng responsive và trạng thái không tìm thấy trang/tour.
- Tour là dữ liệu mẫu; đặt chỗ và hồ sơ dùng Zustand persist trên trình duyệt.

### Ngoài phạm vi bản hiện tại / cần triển khai

- Đăng ký, đăng nhập, đăng xuất thực và phục hồi mật khẩu.
- API, cơ sở dữ liệu dùng chung, đồng bộ đa thiết bị và kiểm soát quyền.
- Thanh toán, hoàn tiền, hóa đơn và gửi email/SMS.
- Giao diện admin đang được mô tả trong tài liệu kỹ thuật nhưng chưa có route hoặc chức năng trong ứng dụng.
- AI tư vấn tour cho khách cuối; không được hiểu AI hỗ trợ lập trình trong tài liệu dự án là chatbot đã tích hợp vào website.
- Bản đồ/GPS, quản lý tồn chỗ theo thời gian thực, báo cáo kinh doanh production.

## 5. Yêu cầu chức năng

| ID | Yêu cầu | Mức ưu tiên | Tiêu chí nghiệm thu |
|---|---|---|---|
| FR-01 | Xem trang chủ và các tour nổi bật | Must | Các thẻ tour mở đúng trang chi tiết; liên kết điều hướng hợp lệ |
| FR-02 | Duyệt, tìm kiếm và lọc tour | Must | Tìm theo tên/điểm đến/vùng; bộ lọc danh mục, vùng và ngân sách phối hợp được; xóa bộ lọc trả về mặc định |
| FR-03 | Sắp xếp danh sách | Must | Có giá tăng dần, giá giảm dần và thứ tự nổi bật; số lượng kết quả phản ánh kết quả lọc |
| FR-04 | Xem chi tiết tour | Must | Trang hiển thị mô tả, hành trình, giá, thời lượng, ngày khởi hành, đánh giá và trạng thái tour không tồn tại |
| FR-05 | Chọn ngày và số khách | Must | Ngày không sớm hơn ngày hiện tại; số khách dương; tổng tiền bằng giá mỗi người nhân số khách |
| FR-06 | Gửi yêu cầu giữ chỗ | Must | Bắt buộc họ tên, email, điện thoại; email/điện thoại hợp lệ; sau xác nhận hiển thị tóm tắt và ghi nhận đơn |
| FR-07 | Xem các đơn của khách | Must | Hiển thị mã đơn, tour, ngày khởi hành, ngày đặt, số khách, tổng tiền và trạng thái |
| FR-08 | Cập nhật thông tin liên hệ | Should | Kiểm tra trường bắt buộc và định dạng email/điện thoại; thông báo lưu thành công |
| FR-09 | Quản lý tour/danh mục/địa điểm qua admin | Future | Người không có quyền bị chặn cả ở giao diện lẫn API; CRUD có kiểm tra dữ liệu và xác nhận thao tác phá hủy |
| FR-10 | Quản lý và xử lý đơn qua admin | Future | Nhân viên xem/lọc đơn, cập nhật trạng thái theo luồng được cho phép và lưu lịch sử thay đổi |
| FR-11 | Quản lý người dùng và vai trò | Future | Chỉ admin được ủy quyền; không hiển thị mật khẩu hoặc bí mật xác thực |

## 6. Luồng nghiệp vụ chính

### 6.1 Khám phá và đặt tour

1. Khách vào trang chủ hoặc danh sách tour.
2. Tìm kiếm/lọc/sắp xếp theo nhu cầu.
3. Mở chi tiết, xem lịch trình và chọn ngày khởi hành/số người.
4. Đi tới biểu mẫu giữ chỗ; rà soát tour, ngày và số khách.
5. Nhập thông tin liên hệ; hệ thống kiểm tra dữ liệu.
6. Khi xác nhận, tạo mã đơn, ghi nhận ngày đặt và tổng tiền, hiển thị xác nhận.
7. Khách mở danh sách chuyến đi để xem đơn.

Trong bản hiện tại bước 6 chỉ ghi vào bộ nhớ trình duyệt hiện hành qua Zustand persist; không có xác nhận từ nhà cung cấp tour, giữ chỗ thực tế hoặc thanh toán.

### 6.2 Xử lý đơn trong admin (luồng mục tiêu)

1. Nhân viên đăng nhập và được máy chủ xác thực quyền.
2. Mở danh sách đơn, lọc theo mã/trạng thái/ngày.
3. Mở chi tiết, đối chiếu thông tin liên hệ và chỗ trống.
4. Cập nhật trạng thái hợp lệ; hệ thống lưu người thao tác, thời điểm và giá trị trước/sau.
5. Thông báo kết quả chỉ được gửi sau khi cập nhật máy chủ thành công.

Luồng trạng thái và chính sách hủy cần chủ sản phẩm phê duyệt trước khi phát triển; đề xuất ban đầu: `pending → confirmed → completed`, kèm nhánh `cancelled`. Không coi đây là trạng thái đã có trong ứng dụng hiện tại.

## 7. Quy tắc nghiệp vụ và dữ liệu

- Giá tour là giá mỗi người, tính bằng VND; tổng dự kiến = đơn giá × số người.
- Bản frontend giới hạn số khách đặt ở 1–10; phạm vi thực tế cần xác nhận với vận hành.
- Ngày khởi hành phải là ngày hợp lệ, không nằm trước ngày hiện tại; ngày còn chỗ phải được máy chủ kiểm tra khi có backend.
- Email và điện thoại phải được kiểm tra ở giao diện để hỗ trợ người dùng và được xác thực lại ở máy chủ.
- Mã đặt chỗ phải duy nhất ở backend. Mã tạo từ timestamp hiện tại chỉ đủ minh họa, không đảm bảo không trùng khi có nhiều client.
- Không lưu mật khẩu dạng rõ, thông tin thẻ, token truy cập hay dữ liệu thanh toán trong localStorage.
- Trạng thái đơn do máy chủ quản lý; khách chỉ được đọc đơn gắn với danh tính đã xác thực.

### Mô hình nghiệp vụ đề xuất

| Thực thể | Thuộc tính chính |
|---|---|
| Tour | `id`, `name`, `description`, `destinationId`, `categoryId`, `region`, `duration`, `price`, `image`, `itinerary`, `active` |
| Destination | `id`, `name`, `province`, `region`, `description`, `image`, `active` |
| Category | `id`, `name`, `description`, `active` |
| Booking | `id`, `userId` hoặc thông tin khách được bảo vệ, `tourId`, `departureDate`, `quantity`, `unitPriceSnapshot`, `totalPrice`, `status`, `createdAt`, `updatedAt` |
| User | `id`, `fullName`, `email`, `phone`, `role`, `createdAt`; thông tin xác thực tách riêng và không trả về cho client |
| BookingStatusHistory (đề xuất) | `id`, `bookingId`, `fromStatus`, `toStatus`, `actorId`, `changedAt`, `note` |

`unitPriceSnapshot` lưu giá tại thời điểm đặt để thay đổi giá tour sau này không làm sai tổng tiền của đơn cũ.

## 8. Yêu cầu phi chức năng

- **Usability:** giao diện tiếng Việt, biểu mẫu có nhãn, lỗi gần trường nhập, thao tác chính có trạng thái đang xử lý/thành công/thất bại.
- **Responsive:** hỗ trợ màn hình di động, máy tính bảng và desktop; không tràn nội dung ở chiều rộng nhỏ.
- **Accessibility:** điều khiển bằng bàn phím, focus dễ thấy, tên truy cập cho nút biểu tượng, tương phản đủ và thông báo lỗi có thể được trình đọc màn hình nhận biết.
- **Performance:** ảnh có kích thước hợp lý/lazy load khi phù hợp; mục tiêu Core Web Vitals cần đo trên môi trường triển khai.
- **Reliability:** khi backend lỗi, không hiển thị trạng thái thành công giả; cho phép thử lại an toàn, tránh tạo trùng đơn bằng idempotency key.
- **Security/Privacy:** xác thực và phân quyền ở máy chủ, kiểm tra đầu vào, giới hạn tốc độ, HTTPS, giảm thiểu dữ liệu cá nhân, chính sách lưu/xóa dữ liệu.
- **Maintainability:** TypeScript nghiêm ngặt, miền nghiệp vụ tách khỏi component giao diện, kiểm tra tự động cho quy tắc quan trọng.
- **Localization:** định dạng ngày và tiền nhất quán theo `vi-VN`, múi giờ và quy ước ngày phải được quy định khi triển khai đa vùng.

## 9. Rủi ro, giả định và quyết định còn mở

- Không có backend đồng nghĩa đặt chỗ có thể mất khi xóa dữ liệu trình duyệt và không được nhân viên nhận.
- Tài liệu README mô tả một số route quản trị dự kiến, nhưng ứng dụng hiện không đăng ký các route này; source code là căn cứ cho trạng thái triển khai.
- Chưa xác định chính sách hủy/hoàn tiền, giới hạn khách, tồn chỗ, SLA hỗ trợ, thông báo và quy trình xác minh admin.
- Cần chọn backend, cơ sở dữ liệu, nhà cung cấp email/thanh toán và chính sách bảo vệ dữ liệu trước khi khởi chạy thực tế.

## 10. Phát hành và nghiệm thu

Một bản phát hành được nghiệm thu khi:

1. Các luồng FR-01 đến FR-08 hoạt động, kể cả trường hợp rỗng, dữ liệu sai và tour không tồn tại.
2. Giao diện dùng được trên mobile/desktop và không có lỗi TypeScript/build.
3. Trạng thái đơn và tổng tiền nhất quán trong suốt luồng.
4. Nếu kết nối backend: dữ liệu được kiểm tra ở máy chủ, đơn chỉ thuộc về chủ thể hợp lệ và admin được phân quyền ở server.
5. Tài liệu/README phản ánh chính xác phạm vi đã triển khai.

