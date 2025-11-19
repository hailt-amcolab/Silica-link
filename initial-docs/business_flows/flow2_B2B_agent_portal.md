## Flow 2 — Cổng đại lý B2B: nhập đơn + phê duyệt

![](attachment:0c35712b-08d9-478d-8ed5-76c576a21f54:Screenshot_2025-09-25_at_14.11.59.png)

Tác nhân: Đại lý gửi đơn trên cổng guest hoặc nhân sự nhập đơn FAX.

Các bước:

1) Đại lý đăng nhập guest space. Form gồm tìm SKU, số lượng, nơi giao, ngày mong muốn, ghi chú.

2) Tự hiển thị kho ứng viên và tồn tham chiếu theo route_rule.

3) Gửi đơn → phê duyệt nội bộ (ngưỡng, ngoại lệ). Gửi thông báo kết quả cho đại lý.

4) Nếu là FAX, nhân sự tải ảnh; liên kết S3, lưu phục vụ kiểm toán.

5) Đơn được duyệt → tạo Order, sinh PDF (phiếu nhận đơn/ báo giá/ phiếu giao), tạo link tải.

6) Nếu thiếu hàng hoặc sai quy tắc → đẩy hàng đợi ngoại lệ để nhập lại.

Đầu ra: đơn đã duyệt, PDF, dấu vết kiểm toán, ngoại lệ khi có.

![Untitled diagram _ Mermaid Chart-2025-09-29-145933.png](attachment:015cfa1c-d131-4fd6-a2c8-f306d127da7e:Untitled_diagram___Mermaid_Chart-2025-09-29-145933.png)