# Business Flow 1 — Yoom → Kintone: Tự động nhận đơn + phân bổ tồn kho

## Tác nhân kích hoạt

- Đơn hàng EC được tạo trên Shopify本店/FIS
- Yoom gửi dữ liệu qua Webhook (ưu tiên) hoặc batch dự phòng

## Hệ thống chính

- Yoom (Webhook/Batch)
- Ruby_Server (API tiếp nhận, chuẩn hóa, điều phối)
- Kintone: `Orders_App`, `Stock_App`

## Các bước xử lý

1) Nhận đơn từ Yoom
- Yoom gọi API tiếp nhận của CREDIE (Webhook, fallback batch)
- Đầu vào: payload đơn hàng (header + line items)

2) Chuẩn hóa và ghi `Orders_App`
- Chuẩn hóa cấu trúc, map `store_id`, `channel`
- Kiểm tra trùng lặp (idempotent key: channel_order_id + store_id)
- Ghi record vào `Orders_App`

3) Phân bổ tồn kho (論理在庫)
- Tra tồn theo SKU trên `Stock_App`
- Nếu thiếu: tạo alert và gắn trạng thái `要在庫確認`
- Nếu đủ: giữ hàng (giảm 論理在庫 tương ứng)

4) Tự động chọn kho xuất (Routing)
- Theo `Products.route_rule`: `MWATER = Gifu`, còn lại = `Amagasaki`
- Lưu thông tin warehouse_id vào đơn

5) Chọn mẫu chứng từ và hồ sơ người gửi
- Theo `client_id` và ngôn ngữ (JP/EN)
- Gắn template giao hàng/hóa đơn và sender profile

6) Cập nhật trạng thái và log
- Cập nhật `Status = 受注確定/引当済み`
- Ghi log các bất thường (thiếu trường, lệch tổng, v.v.)

## Đầu ra

- Record đơn hàng mới trong `Orders_App`
- Bản ghi phân bổ tồn (giảm 論理在庫) hoặc cảnh báo `要在庫確認`
- Tham số warehouse, template & sender đã được gắn vào đơn

## Lưu ý/ngoại lệ

- Nếu payload lỗi/thiếu: đặt `Status = 要確認`, ghi log chi tiết
- Nếu Webhook thất bại: retry với backoff và chuyển sang batch nếu quá ngưỡng
- Idempotency: bỏ qua tạo mới khi phát hiện trùng channel_order_id + store_id

