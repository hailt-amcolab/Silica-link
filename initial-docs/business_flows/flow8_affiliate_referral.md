## Flow 8 — Quản lý giới thiệu (取次店) kiểu affiliate

![Screenshot 2025-09-25 at 14.17.10.png](attachment:53a9ff20-7df7-4a48-86a4-c8f02faeaaad:Screenshot_2025-09-25_at_14.17.10.png)

Tác nhân: Đối tác tạo link; khách đặt hàng; chốt tháng.

Các bước:

1) Tạo link rút gọn [links.credie.jp/p/{token}](http://links.credie.jp/p/{token}); cấu hình domain và TTL; redirect 302.

2) Lần ghé đầu đặt 1st‑party cookie chứa partner_id (có consent/ITP).

3) Ở webhook orders.create/refunds.create, khôi phục partner_id và kiểm chứng; ghi log.

4) Ghi nhận hoa hồng vào RewardsLedger (commission) với đơn giá/tỷ lệ, SKU/kênh, tiền tệ và trạng thái.

5) Máy quy tắc gán công: mặc định “last click 30 ngày”; hỗ trợ ưu tiên/ghi đè; chuẩn bị UI đổi rule.

6) Cuối tháng: tổng hợp; áp tối thiểu thanh toán; (tùy chọn khấu trừ) → tạo Payouts.

7) Chuyển khoản GMO (/payments); phản ánh kết quả; quản lý phí.

8) Sinh phiếu chi PDF; gửi mail/link portal.

9) Giám sát gian lận: click bất thường/tự affiliate; DLQ; alert.

10) Portal đối tác: phát hành link, dashboard doanh thu/hoa hồng, lịch sử chi, CSV.

Đầu ra: bản ghi commission, payout, phiếu chi, số liệu portal.

### **Q&A: Affiliate / Partner (取次店)**

- Xác định mô hình:
    - 1 tầng hay multi-level (đã phát sinh yêu cầu 4階層).
    - Rule: % cố định? khác nhau theo tầng/SKU?
- Cơ chế attribution: last click 30 ngày hay có tuỳ chỉnh?