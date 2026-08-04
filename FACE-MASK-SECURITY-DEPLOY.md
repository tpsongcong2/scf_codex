# Triển khai bảo mật FACE MASK

FACE MASK chỉ cho phép tài khoản có vai trò `admin` hoặc `administrator` đăng nhập.

## Thứ tự triển khai Supabase

1. Triển khai Edge Function `supabase/functions/scf-auth`.
2. Khai báo bí mật gửi email cho Edge Function: `RESEND_API_KEY` và `RESET_FROM_EMAIL`.
3. Chạy các migration xác thực/RLS theo thứ tự thời gian, sau cùng chạy:
   `supabase/migrations/20260804_face_mask_admin_rls.sql`.
4. Kiểm tra tài khoản Admin có `recoveryEmail` hoặc `email` hợp lệ trong hồ sơ nhân viên.
5. Sau khi xác nhận Edge Function và RLS hoạt động, đổi `SCF_SERVER_AUTH_ENABLED` trong `server-auth.js` thành `true` rồi phát hành lại cả SCFood và FACE MASK.

Không bật `SCF_SERVER_AUTH_ENABLED=true` trước khi Edge Function và migration đã được triển khai, vì người dùng có thể không đăng nhập hoặc tải dữ liệu được.

## Dữ liệu được khóa Admin tại Supabase

- Dòng tiền, công nợ và số dư đầu kỳ.
- Nhà cung cấp nguyên vật liệu.
- Đơn mua nguyên vật liệu.
- Dữ liệu tồn đầu kỳ nguyên vật liệu.
- Bảng mã khôi phục mật khẩu không cấp quyền trực tiếp cho trình duyệt.

Các dữ liệu đơn hàng, nhiên liệu và chấm công vẫn giữ quyền nghiệp vụ hiện tại vì SCFood cần chúng cho kế toán, lái xe và nhân viên chấm công. Báo cáo FACE MASK chỉ hiển thị cho Admin.
