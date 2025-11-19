## Flow 5 — Giám sát tồn → Tự động tạo PO + cổng NCC/EDI

![Screenshot 2025-09-25 at 14.15.15.png](attachment:2fd8604b-8325-461c-99a7-51ef485d02e7:Screenshot_2025-09-25_at_14.15.15.png)

Tác nhân: Tồn kho tại kho đạt ngưỡng reorder_point.

Các bước:

1) Giám sát tồn (Amagasaki/Gifu) và reorder_point; tạo trigger PO theo SKU/nhà cung cấp.

2) Chuẩn hóa dữ liệu master; kiểm tra supplier_id và thiết lập ngưỡng.

3) Sinh PO PDF (mặt hàng, số lượng, hạn mong muốn, điều khoản thanh toán); lưu S3 và đính vào PO.

4) Gửi email PO tự động; theo dõi mở/nhận; tự động gửi lại khi cần.

5) Áp dụng quy tắc mua (lot, gom, batch ngày/tuần). Gộp theo nhà cung cấp.

6) Phê duyệt nội bộ khi vượt ngưỡng; hỗ trợ tách/đặt lại/hủy/sửa version.

7) Ngoại lệ: chênh lệch, thiếu/phần, trễ hạn → cảnh báo và tái kế hoạch.

8) Giám sát vận hành: metrics, alert khi không phản hồi; log kiểm toán.

9) Cổng NCC (guest): xem PO, nhập ngày hứa giao, cho phép chia lô, bình luận; có audit.

10) Nếu có EDI: Webhook Kintone → REST nhà cung cấp; retry + audit.

Đầu ra: PO PDF, email/nhiệm vụ cổng, đơn mua đã duyệt, bản ghi ngoại lệ.

### **Q&A: Supplier & 発注**

- Low stock threshold → được set ở đâu (Stock.ReorderPoint?).
- Output 発注書: PDF + Email (default), Portal Guest (主要仕入先), hay EDI (大手)?
- Confirm nhà cung cấp nào cần portal/EDI ngay phase 1.