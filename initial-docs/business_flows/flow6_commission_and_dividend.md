## Flow 6 — Quản lý Hoa hồng & Cổ tức (theo quý)

![Screenshot 2025-09-25 at 14.15.47.png](attachment:eb36ebef-f0db-44b9-ad79-76159c047790:Screenshot_2025-09-25_at_14.15.47.png)

Tác nhân: Đơn xác nhận/Refund phát sinh; chuyển quý.

Các bước:

1) Tính đơn giá MWATER/SILICA_H2 và ngưỡng lũy kế (SW 5→3 yên sau 50 triệu yên).

2) Duy trì danh mục quý; tự chuyển quý; khóa sổ vào ngày +1 sau cuối quý.

3) RewardRules định phạm vi (sku/category/channel), kiểu (unit/rate), thời hạn, ưu tiên.

4) Khi đơn xác nhận: tự ghi vào RewardsLedger; refund ghi âm; trạng thái pending/approved/paid; an toàn đa tiền tệ.

5) Cuối quý: tổng hợp; xuất PDF; khóa quý.

6) Chi qua GMO: tạo Payouts; gọi /payments; phản ánh kết quả & phí.

7) Phát hành PDF sao kê chi; tạo link portal; dashboard cho Ban giám đốc.

8) Giám sát trùng/DLQ; cảnh báo CloudWatch; log kiểm toán.

Đầu ra: bản ghi ledger, payout, PDF, dashboard, quý đã khóa.