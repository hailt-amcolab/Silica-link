## Flow 1 — Yoom → Kintone: Tự động nhận đơn + phân bổ tồn kho

![](attachment:9b6feed7-99b0-4572-b5a8-e940a8b9b5e5:Screenshot_2025-09-25_at_14.12.39.png)

**Tác nhân: Đơn hàng EC được tạo (Shopify本店/FIS qua Webhook Yoom hoặc batch)**

**Các bước:**

1) Khi có đơn hàng mới trên Yoom, Yoom sẽ gửi đơn hàng sang CREDIE bằng cách call API tiếp nhận (ưu tiên Webhook, dự phòng batch). 
Đầu vào: payload đơn hàng.

2) Chuẩn hóa và ghi vào **app** **Orders** (store_id, channel). Kiểm tra trùng lặp và cấu trúc.

3) Phân bổ tồn kho theo SKU từ **app** **Stock**. Nếu thiếu, tạo cảnh báo và gắn trạng thái “要在庫確認”.

4) Tự động chọn kho xuất theo Products.route_rule (MWATER=Gifu, còn lại=Amagasaki).

5) Chọn mẫu chứng từ giao hàng/hóa đơn và hồ sơ người gửi theo client_id / ngôn ngữ.

6) Cập nhật trạng thái đơn “受注確定/引当済み”, ghi log bất thường.

**Đầu ra**: đơn hàng mới, bản ghi phân bổ tồn, cảnh báo.

![](attachment:0ef5a786-2bd9-4655-9902-b95f2a1d2251:Untitled_diagram___Mermaid_Chart-2025-09-29-142402.png)