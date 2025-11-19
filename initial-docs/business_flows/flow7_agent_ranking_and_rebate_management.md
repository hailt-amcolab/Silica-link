## Flow 7 — Xếp hạng đại lý & quản lý chiết khấu

![Screenshot 2025-09-25 at 14.16.03.png](attachment:1c0ab193-49e9-4e16-babd-bf3a3fa3b721:Screenshot_2025-09-25_at_14.16.03.png)

Tác nhân: Job tổng hợp chạy hàng tháng.

Các bước:

1) Tổng hợp số lượng mua theo đại lý; ghi log idempotent.

2) Xác định hạng (Starter/Silver/Gold/Platinum) với hiệu lực và ưu tiên.

3) Thông báo thay đổi hạng; chạy job áp giá; cảnh báo khi lỗi.

4) Liên kết Portal: cung cấp API/Rule áp giá ngay hoặc ban đêm, hỗ trợ giá đặc biệt/loại trừ và kiểm tra nhất quán.

5) API gauge: trả về “còn ◯ chai” tới hạng tiếp theo cho widget portal.

Đầu ra: trạng thái hạng, quy tắc giá, thông báo, giá trị gauge.

### **Q&A: Dividend / Reward**

- Confirm logic tính配当: MW=2円固定, SW=5円→3円 (5,000万閾値).
- Chu kỳ lock: 四半期末+1日, PDF export.
- Có cần báo cáo chi tiết cho từng partner không?