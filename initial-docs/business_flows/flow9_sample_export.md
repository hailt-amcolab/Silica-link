## Flow 9 — Xuất mẫu dùng thử (Sales → Kho)

![Screenshot 2025-09-25 at 14.16.36.png](attachment:505e28ec-04b5-4ca2-857c-888269847bb6:Screenshot_2025-09-25_at_14.16.36.png)

Tác nhân: Sales yêu cầu xuất mẫu.

Các bước:

1) Xác định quy trình & vai trò; ngưỡng và phê duyệt (nếu có).

2) Tạo bản ghi StockMovements (movement_type = SAMPLE_OUT) với sku, qty, rep_id, lý do, thời gian.

3) Form di động/thiết bị: quét SKU (JAN/EAN), nhập số lượng & lý do; tùy chọn ảnh.

4) Chuỗi quét: kệ → sản phẩm; kiểm tra số lượng; dùng idempotency tránh trùng.

5) Khi xác nhận: trừ tồn tại kho (Amagasaki/Gifu) ngay; đối soát WMS; xử lý chênh lệch.

6) Nhật ký & audit: ai/khi nào/cái gì/bao nhiêu; tìm theo kỳ/nhân sự/SKU; xuất CSV.

7) Ngoại lệ & thông báo: thiếu tồn, sai SKU, lỗi quét → đẩy queue + thông báo.

8) Dashboard (tùy chọn): số mẫu theo tháng, xếp hạng theo nhân sự/SKU, ảnh hưởng tồn.

Đầu ra: bản ghi movement, tồn giảm, nhật ký/audit, thông báo, dashboard (nếu bật).