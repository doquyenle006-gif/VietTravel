# Tài liệu kỹ thuật — VietTrails

| Thuộc tính | Giá trị |
|---|---|
| Phiên bản tài liệu | 1.0 |
| Phạm vi | Frontend hiện có và đặc tả kỹ thuật cho frontend/admin |
| Công nghệ frontend hiện tại | React 19, TypeScript, Vite, React Router 7, Zustand 5 |
| Trạng thái admin/backend | Chưa được triển khai; nội dung được đánh dấu là mục tiêu/đề xuất |

## 1. Phân định trạng thái

- **Đang có:** SPA React/TypeScript; các trang khách hàng; dữ liệu tour mẫu; Zustand persist hồ sơ và đặt chỗ vào localStorage.
- **Chưa có:** API server, cơ sở dữ liệu, đăng nhập/phân quyền, thanh toán, admin UI/API, kiểm thử tự động được khai báo trong package scripts.
- **Không được suy diễn:** các route `/admin` trong README cũ là dự kiến, không phải route hiện hành. Route thực tế được khai báo trong `src/app/App.tsx`.
- Các cấu trúc API/database bên dưới là hợp đồng đề xuất để triển khai; không phải endpoint/schema đã tồn tại.

# Phần A — Kỹ thuật Frontend

## 2. Cấu trúc và khởi chạy

```text
src/
  app/                 # App routes, providers và theme context
  components/          # Layout và component dùng chung
  features/
    tours/             # Tour types, dữ liệu mẫu, store/helper định dạng
    bookings/          # Booking types
    auth/              # Profile types và store
    admin/             # Chỉ có file types rỗng; chưa có admin feature
  pages/               # Trang khách hàng theo route
  styles.css           # CSS toàn cục và responsive
```

- `src/main.tsx` mount React, `BrowserRouter`, `App` và stylesheet.
- `src/app/App.tsx` khai báo trang chủ, tour, chi tiết, booking, lịch sử và hồ sơ.
- `src/components/SiteLayout.tsx` chứa header/footer, thẻ tour, search bar và trust strip.
- `src/features/tours/store.ts` chứa danh sách tour cùng helpers và Zustand store đặt chỗ; dữ liệu tour là seed cục bộ.
- `src/features/auth/store.ts` chứa hồ sơ cục bộ.
- `package.json` có `dev`, `build`, `preview`; build chạy `tsc -b && vite build`.

Khởi chạy local:

```bash
npm install
npm run dev
npm run build
```

## 3. Routes và luồng dữ liệu hiện tại

| Route | Component |
|---|---|
| `/` | `HomePage` |
| `/tours` | `ToursPage` |
| `/tours/:id` | `TourDetailPage` |
| `/booking` | `BookingPage` |
| `/my-bookings` | `MyBookingsPage` |
| `/profile` | `ProfilePage` |
| `/destinations`, `/categories` | Đang dùng lại `ToursPage` |
| `*` | Trang 404 nội tuyến trong `App.tsx` |

Chi tiết tour tìm theo ID trong mảng dữ liệu mẫu. Trang danh sách lọc dữ liệu ở client. Trang đặt tour nhận tour/số người/ngày từ query string, kiểm tra thông tin liên hệ rồi cập nhật profile và booking store. Danh sách/hồ sơ đọc từ Zustand store. Các thao tác này không đồng bộ với server.

### Persistence và dữ liệu

- Booking được persist dưới key `viettrails-bookings`.
- Profile được persist dưới key `viettrails-profile`.
- Tour được import từ dữ liệu tĩnh, không tải từ API.
- Dữ liệu localStorage có thể bị sửa/xóa bởi người dùng, không được tin cậy làm nguồn sự thật hay dùng để xác thực quyền.
- ID tạo từ timestamp là ID minh họa; backend cần sinh ID duy nhất.

## 4. Quy ước frontend

- Giữ TypeScript strict; định nghĩa kiểu cho payload/response, tránh `any`.
- Component trang xử lý UI và điều hướng; khi thêm API, gom truy cập mạng vào module service/repository theo feature, không gọi `fetch` rải rác trong JSX.
- Phân biệt trạng thái `idle/loading/success/error`; lỗi mạng/validation phải hiển thị minh bạch.
- Không chuyển secret/API private key vào bundle client. Biến Vite phía client không phải secret.
- Kiểm tra lại ở server mọi trường do trình duyệt gửi, kể cả giá, ngày, số lượng, user ID và role.
- Escape/render nội dung do người dùng nhập theo cơ chế an toàn; không đưa nội dung chưa tin cậy vào HTML thô.
- Lưu ý refresh deep-link khi deploy SPA: host phải rewrite route về `index.html`.
- Dùng `Intl.NumberFormat("vi-VN", { style: "currency", currency: "VND" })` hoặc helper hiện có nhất quán; quy định locale/múi giờ cho ngày.

## 5. Hợp đồng API mục tiêu (chưa triển khai)

Đề xuất REST JSON, HTTPS, xác thực qua session cookie an toàn hoặc access token theo quyết định kiến trúc. Không lưu token nhạy cảm vào localStorage nếu có lựa chọn cookie `HttpOnly`, `Secure`, `SameSite`. Endpoint mẫu:

| Method | Endpoint | Mục đích | Quyền |
|---|---|---|---|
| GET | `/api/tours` | Danh sách, query tìm/lọc/sắp xếp/pagination | Public |
| GET | `/api/tours/{id}` | Chi tiết tour | Public |
| POST | `/api/bookings` | Tạo đặt chỗ; server tính lại giá và kiểm tra chỗ | Khách/guest policy được phê duyệt |
| GET | `/api/me/bookings` | Đọc đặt chỗ của người dùng hiện tại | Authenticated |
| GET/PATCH | `/api/me/profile` | Xem/cập nhật hồ sơ | Authenticated |
| GET/POST/PATCH/DELETE | `/api/admin/tours` | Quản lý tour | Admin |
| GET/POST/PATCH/DELETE | `/api/admin/destinations` | Quản lý điểm đến | Admin |
| GET/POST/PATCH/DELETE | `/api/admin/categories` | Quản lý danh mục | Admin |
| GET | `/api/admin/bookings` | Tìm kiếm/phân trang đơn | Admin hoặc booking staff |
| PATCH | `/api/admin/bookings/{id}/status` | Chuyển trạng thái đơn có audit | Booking staff/Admin |
| GET/PATCH | `/api/admin/users` | Quản lý hồ sơ/vai trò theo policy | Admin được ủy quyền |

API trả lỗi theo một schema nhất quán, ví dụ `{ "error": { "code": "VALIDATION_ERROR", "message": "...", "fields": {} } }`. Dùng HTTP status phù hợp; không trả stack trace hoặc dữ liệu bí mật. `POST /bookings` cần chống gửi lặp/idempotency và tính tổng tiền ở server từ giá hiện hành/giá snapshot cùng chính sách vận hành.

## 6. Kiểm thử và chất lượng frontend

Tối thiểu trước khi phát hành:

- Build/type-check bằng `npm run build`.
- Unit test cho lọc/sắp xếp, kiểm tra form, format giá/ngày, quy tắc số lượng và tính tổng.
- Component/integration test cho empty/error/success và chuyển route.
- E2E cho luồng tìm tour → chi tiết → đặt chỗ → xem booking, bao gồm dữ liệu sai và reload.
- Kiểm tra responsive/accessibility thủ công hoặc tự động và console trên route bị ảnh hưởng.

Hiện `package.json` chưa có script test/lint; cần bổ sung công cụ và pipeline khi nhóm chốt tiêu chuẩn CI. Không báo cáo test đạt nếu lệnh chưa được chạy.

# Phần B — Kỹ thuật Admin

## 7. Tình trạng và mục tiêu

Ứng dụng chưa có trang admin, route `/admin`, API hay xác thực role. `src/features/admin/types.ts` hiện rỗng. Phần này là đặc tả để phát triển sau, không mô tả chức năng đã chạy.

Admin là ứng dụng quản trị cùng frontend hoặc khu vực route riêng, dùng chung component design system ở mức phù hợp nhưng có shell/layout tách biệt. Bảo vệ dữ liệu bằng xác thực và kiểm tra quyền tại server; route guard UI chỉ cải thiện UX, không thay thế bảo mật.

## 8. Khu vực chức năng admin

| Khu vực | Khả năng mục tiêu |
|---|---|
| Dashboard | Tổng quan đơn mới, đơn theo trạng thái, tour đang hoạt động; chỉ hiển thị số liệu có nguồn server xác định |
| Tour | Danh sách có tìm kiếm/phân trang/trạng thái; tạo/sửa/xem trước/ẩn; kiểm tra giá, ngày, ảnh, lịch trình |
| Điểm đến | CRUD thông tin tên, vùng/tỉnh, mô tả, ảnh và trạng thái |
| Danh mục | CRUD tên/mô tả; ngăn xóa nếu còn tour liên kết hoặc quy định rõ hành vi |
| Đơn đặt | Lọc theo mã/trạng thái/ngày; xem chi tiết; chuyển trạng thái đúng workflow; ghi audit |
| Người dùng | Tìm/xem hồ sơ tối thiểu, khóa/mở theo chính sách; quản lý role có kiểm soát đặc biệt |
| Nhật ký | Tra cứu thao tác nhạy cảm: ai, khi nào, đối tượng nào, thay đổi gì |

Các quyết định về dashboard, trạng thái booking, xóa/ẩn, hủy/hoàn tiền và quản lý tài khoản cần được duyệt bởi chủ sản phẩm trước khi implement.

## 9. Phân quyền đề xuất

| Vai trò | Tour/điểm đến/danh mục | Đơn đặt | Người dùng/role |
|---|---|---|---|
| `admin` | Đọc/ghi | Đọc/ghi theo policy | Quản lý có kiểm soát/audit |
| `booking_staff` | Chỉ đọc nếu cần | Đọc và cập nhật trạng thái cho phép | Không |
| `customer` | Chỉ đọc dữ liệu public | Chỉ đơn của mình qua API customer | Chỉ hồ sơ của mình |

Yêu cầu bắt buộc:

- Server xác thực danh tính và kiểm tra quyền trên từng request/resource; áp dụng deny-by-default.
- Kiểm tra quyền trên object, ngăn IDOR, kể cả khi người dùng sửa URL/body.
- Thay đổi role, khóa tài khoản, cập nhật trạng thái và thao tác xóa phải có audit trail.
- Không lưu mật khẩu dạng rõ; dùng hệ thống xác thực đã được đánh giá, chính sách reset/session và MFA cho admin nếu phù hợp.
- Giới hạn tốc độ đăng nhập/API; chống CSRF nếu dùng cookie; kiểm soát CORS theo origin; log không chứa token/mật khẩu/thông tin nhạy cảm.
- UI ẩn nút theo quyền để dễ dùng nhưng API vẫn là ranh giới thực thi quyền.

## 10. Quy tắc ghi dữ liệu và audit

- Tạo/sửa tour, danh mục, điểm đến có validation server-side và response lỗi theo field.
- Ưu tiên archive/deactivate thay vì xóa cứng dữ liệu có tham chiếu hoặc lịch sử giao dịch.
- Booking chỉ chuyển qua các trạng thái được cấu hình; kiểm tra chuyển trạng thái trong transaction.
- Thay đổi trạng thái ghi actor, timestamp UTC, trạng thái trước/sau và ghi chú (nếu có); người thường không sửa audit record.
- Giá booking được chụp tại lúc tạo; admin không sửa trực tiếp tổng tiền đơn lịch sử nếu không có quy trình điều chỉnh/audit.
- Thao tác ghi hỗ trợ concurrency control (version/ETag hoặc transaction), chống double-submit.
- Xác nhận riêng với hành động nguy hiểm; hiển thị lỗi rõ và chỉ báo thành công sau response server thành công.

## 11. Mô hình lưu trữ/API admin đề xuất

Admin dùng cùng domain model với public API, không duy trì danh sách tour hoặc booking riêng trên localStorage. Tối thiểu cần:

- `Tour`, `Destination`, `Category`, `User` và `Booking` theo mô hình ở [PRD](./PRD.md).
- Khóa ngoại và index cho `booking.status`, `booking.createdAt`, `booking.departureDate`, `booking.tourId`, `booking.userId`, cùng trường tìm kiếm thường dùng.
- `AuditLog` append-only cho thao tác nhạy cảm; hạn chế nội dung thông tin cá nhân trong log.
- Pagination/filter/sort phía server; giới hạn page size; danh sách admin không tải toàn bộ bảng dữ liệu.
- API chỉ trả trường cần thiết; không gửi password hash, reset secret hoặc dữ liệu payment cho trình duyệt.
- Giao dịch tạo booking: kiểm tra tour hoạt động/ngày/chỗ, tính tiền phía server, tạo booking và ghi nhận idempotency trong cùng đơn vị nhất quán.

## 12. Tiêu chí nghiệm thu admin

1. Không đăng nhập/không đúng quyền không thể truy cập dữ liệu/thao tác admin qua UI **hoặc gọi API trực tiếp**.
2. CRUD có kiểm tra dữ liệu; trạng thái loading/empty/error/success rõ; xóa/ẩn có xác nhận theo policy.
3. Tìm kiếm, bộ lọc, phân trang phản ánh truy vấn server và không bỏ qua phân quyền.
4. Cập nhật đơn từ trạng thái không hợp lệ bị từ chối; lịch sử người thao tác được ghi nhận.
5. Dữ liệu nhạy cảm không lộ trong response, log, URL hoặc storage phía client.
6. Có test cho quyền, IDOR, validation, chuyển trạng thái, audit và các lỗi mạng/concurrency.
7. Giao diện admin hỗ trợ bàn phím, focus, label, thông báo screen reader và viewport tablet/desktop; các thao tác thiết yếu vẫn dùng được trên mobile.

## 13. Lộ trình triển khai gợi ý

1. Chốt nghiệp vụ: quyền/role, trạng thái đặt chỗ, chính sách khách vãng lai, hủy và lưu trữ dữ liệu.
2. Chốt backend/database/identity provider và OpenAPI; xây migrations, validation, audit, authorization.
3. Tích hợp API cho luồng khách; chuyển persistence nghiệp vụ khỏi localStorage.
4. Xây admin shell và danh sách/chỉnh sửa tour, điểm đến, danh mục.
5. Xây xử lý booking, lịch sử trạng thái, user management có bảo vệ.
6. Thêm test tích hợp/E2E, pipeline CI, logging/monitoring, backup/restore và kiểm thử bảo mật trước production.

## 14. Tham chiếu trong repository

- Yêu cầu nghiệp vụ: [PRD](./PRD.md).
- Đặc tả giao diện và quy trình AI: [UI-AI-IMPLEMENTATION](./UI-AI-IMPLEMENTATION.md).
- Route hiện tại: `src/app/App.tsx`.
- Dữ liệu tour và store booking: `src/features/tours/store.ts`.
- Store hồ sơ: `src/features/auth/store.ts`.
- Trang đặt chỗ: `src/pages/BookingPage.tsx`.
