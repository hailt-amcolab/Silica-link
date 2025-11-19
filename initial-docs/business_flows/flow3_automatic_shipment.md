## Flow 3 — Kintone → Ship&Co: Tự động giao hàng + quét mã

![Screenshot 2025-09-25 at 14.13.47.png](attachment:299352eb-7b0f-478c-b4f1-0f8a594bbe68:Screenshot_2025-09-25_at_14.13.47.png)

Tác nhân: Kho bắt đầu soạn hàng cho đơn đã duyệt.

Các bước:

1) Kho sử dụng màn Today’s Pick/Scan/Ship.

2) Quét kệ → quét sản phẩm → nhập/quét số lượng → xác nhận xuất cho từng dòng.

3) Gọi API Ship&Co: chọn kho xuất (Gifu/Amagasaki), dịch vụ, tạo nhãn; lưu nhãn vào S3 và đính vào đơn.

4) Khi webhook xác nhận xuất: trừ tồn lý thuyết; ghi số tracking_no về đơn; gửi thông báo cho khách (email/LINE kèm short link).

5) Đồng bộ chênh lệch với WMS ZERO/Lite; đặt lịch retry khi lỗi.

6) Xử lý ngoại lệ (sai địa chỉ, lỗi hãng vận chuyển, giới hạn rate) bằng DLQ và quy trình void/re-ship.

Đầu ra: số tracking, file nhãn, tồn kho cập nhật, thông báo gửi khách.

### **Q & A : Stock & WMS**

- Ở mỗi kho (尼崎・岐阜), confirm hệ thống nào là master (WMS hay Kintone?).
- Cơ chế **減算**: khi 出荷確定 thì giảm stock ở đâu trước?
- Có cần realtime sync hay batch cuối ngày?
- WMS API spec: có API push/pull, hay chỉ CSV import/export?