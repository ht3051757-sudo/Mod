# UGPHONE MOD — bản sửa lỗi đăng nhập/Admin

## Lỗi đã sửa
- Không còn lỗi `Cannot read properties of null (reading 'auth')` khi người dùng bấm Đăng ký/Đăng nhập/Admin trước lúc Supabase tải xong.
- Nếu Supabase chưa cấu hình, giao diện báo rõ cần sửa `config.js` thay vì lỗi JavaScript.
- Nếu CDN Supabase không tải được, giao diện báo lỗi kết nối.
- Admin login hiện báo rõ lỗi xác thực hoặc tài khoản chưa có `role=admin`.
- Bảo vệ `role`/`banned` trong database để user thường không tự sửa quyền của mình.

## Cấu hình bắt buộc
Sửa `config.js`:

```js
window.UG_SUPABASE_URL = "https://YOUR-PROJECT.supabase.co";
window.UG_SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_KEY";
```

Sau đó chạy toàn bộ `supabase_schema.sql` trong Supabase SQL Editor.

## Cấp quyền Admin
1. Tạo tài khoản bằng giao diện web.
2. Xác nhận email nếu Supabase đang bật Email Confirmation.
3. Trong Supabase SQL Editor chạy:

```sql
update public.profiles
set role='admin'
where username='TEN_ADMIN';
```

Admin login dùng **email + mật khẩu Supabase**, không phải KEY nhận hằng ngày.

## server.js
`server.js` là backend Node.js. Nó không chạy trên GitHub Pages. Chạy ở VPS/Node hosting riêng và đặt:

```env
SUPABASE_URL=https://YOUR-PROJECT.supabase.co
SUPABASE_SERVICE_ROLE_KEY=YOUR_SERVICE_ROLE_KEY
PORT=3000
```

Không đưa `SUPABASE_SERVICE_ROLE_KEY` vào GitHub hoặc `config.js`.


## Đổi mật khẩu / thông tin triển khai
- Trang **ĐĂNG KÝ / ĐĂNG NHẬP** → sau khi đăng nhập sẽ có mục **Đổi mật khẩu**.
- Đổi mật khẩu gọi `supabase.auth.updateUser({ password })`; không lưu plaintext password vào `profiles`.
- Email Admin được điền sẵn là `namn63657@gmail.com`.
- KEY được seed trong `supabase_schema.sql` là `6677028@` cho ngày `2026-10-03`, giới hạn 999999 lượt.
- Sau khi tạo user `namn63657@gmail.com` trong Supabase Auth, chạy:
  `update public.profiles set role='admin' where id=(select id from auth.users where email='namn63657@gmail.com');`
- Không đặt mật khẩu Admin trong source code, ZIP hoặc `config.js`.
