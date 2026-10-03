# UGPHONE MOD — bản sửa lỗi đăng nhập/Admin

## Lỗi trong ảnh
Thông báo `Hãy điền UG_SUPABASE_URL và UG_SUPABASE_ANON_KEY trong config.js.` xuất hiện vì `ug/config.js` đang chứa giá trị mẫu:

- `https://YOUR-PROJECT.supabase.co`
- `YOUR_SUPABASE_ANON_KEY`

Đây **không phải lỗi giao diện**; Supabase client không thể khởi tạo nếu chưa có Project URL + publishable/anon key thật.

### Chỗ cần sửa
Mở `ug/config.js` và thay đúng 2 dòng:

```js
window.UG_SUPABASE_URL = "https://<project-ref>.supabase.co";
window.UG_SUPABASE_ANON_KEY = "<publishable-or-anon-key>";
```

Không đưa `SUPABASE_SERVICE_ROLE_KEY` vào file này.

Sau khi upload lên GitHub Pages, hard-refresh trang (Ctrl+F5 hoặc xóa cache) để lấy `config.js` mới.

## Các thay đổi đã áp dụng trong bản FIX
- `config.js`: chú thích rõ vị trí lấy credentials và phân biệt anon/publishable key với service-role key.
- Tên tài khoản: giới hạn **3–12 ký tự** ở HTML + JavaScript.
- `supabase_schema.sql`: đồng bộ giới hạn username **3–12 ký tự**, kèm migration cho database đã tồn tại.
- Giữ nguyên cơ chế đăng nhập Supabase, role Admin, BAN và các chức năng hiện có.

## Cấu hình Supabase
Sau khi điền `config.js`, chạy `supabase_schema.sql` trong Supabase SQL Editor.

### Cấp quyền Admin
Tạo tài khoản bằng giao diện web, sau đó trong Supabase SQL Editor:

```sql
update public.profiles
set role='admin'
where id=(select id from auth.users where email='EMAIL_ADMIN');
```

Admin đăng nhập bằng email + mật khẩu Supabase. Không lưu mật khẩu trong source code.

## Backend
`server.js` là backend Node.js và không chạy trực tiếp trên GitHub Pages. Nếu cần chức năng BAN theo IP ở mức server, deploy `server.js` lên VPS/Node hosting và đặt:

```env
SUPABASE_URL=https://<project-ref>.supabase.co
SUPABASE_SERVICE_ROLE_KEY=<service-role-key>
PORT=3000
```

`SUPABASE_SERVICE_ROLE_KEY` chỉ được đặt ở server, tuyệt đối không đưa vào `config.js`.


## Bản cập nhật: đăng ký không còn bị chặn khi chưa có Supabase
Nếu `config.js` vẫn còn placeholder, trang sẽ tự chuyển sang **Local Auth** để nút TẠO TÀI KHOẢN và ĐĂNG NHẬP hoạt động ngay trên trình duyệt. Mật khẩu được lưu dưới dạng SHA-256 trong localStorage. Chế độ này chỉ dành cho chạy ngay trên một thiết bị; dữ liệu không đồng bộ giữa các máy và các tính năng dùng database/realtime/Admin vẫn cần Supabase. Khi điền Supabase thật, hệ thống tự quay lại Supabase Auth.
