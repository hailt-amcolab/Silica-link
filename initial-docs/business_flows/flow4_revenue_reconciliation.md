## Flow 4 — Đối soát thu (GMO) + Thực hiện chi

![Screenshot 2025-09-25 at 14.14.59.png](attachment:8e80da21-c8da-498e-b0dc-36877e0632ac:Screenshot_2025-09-25_at_14.14.59.png)

Tác nhân: Ngân hàng phát sinh giao dịch (polling/webhook); phiếu chi được duyệt trong Kintone.

Các bước (AR/thu):

1) Nạp /transactions (ký số). Ánh xạ ưu tiên: tài khoản ảo → số tiền → ngày/ghi chú.

2) Ghép vào Orders/Invoices; cập nhật PayStatus.

3) Không khớp → vào danh sách quá hạn/nhắc và báo cáo.

Các bước (AP/chi):

4) Sau khi duyệt trên Kintone, gọi /payments (chuyển khoản tức thời). Cập nhật Payouts/Orders kèm phí và kết quả.

5) Ngoại lệ (sai tên, trùng, lệch số tiền) → đẩy hàng đợi Exceptions, có UI xử lý tay.

6) Sinh CSV/PDF sao kê Payouts lên S3; tra cứu/xuất phục vụ kiểm toán.

Đầu ra: giao dịch đã đối soát, khoản chi đã thực hiện, chứng từ sao kê, bản ghi ngoại lệ.

### **Q&A:Bank (GMO あおぞら)**

- API scope: chỉ入出金照合 hay cả **振込実行**?
- Có dùng仮想口座 (Virtual Account) cho từng partner không?
- Error handling: 名義不一致 thì xử lý ở đâu (Exceptions queue?).