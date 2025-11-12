# Business Flow 3 — Kintone → Ship&Co: Tự động giao hàng + quét mã

## Tác nhân

- Kho bắt đầu soạn hàng cho các đơn đã duyệt (Approved/引当済み)

## Hệ thống chính

- Scan UI (Today’s Pick / Scan / Ship)
- Kintone: `Orders_App`, `Task_App`, `Stock_App`
- Ship&Co API (label, service selection, tracking)
- S3 (lưu nhãn PDF)
- WMS ZERO / WMS Lite (đồng bộ tồn vật lý, nếu dùng)
- Notification (Email/LINE + short link)

## Các bước

1) Chọn màn Today’s Pick / Scan / Ship  
   - Hiển thị danh sách đơn/line theo kho và độ ưu tiên.

2) Quét và xác nhận xuất theo từng dòng  
   - Quét kệ (Shelf) → quét sản phẩm/LOT → nhập/quét số lượng → Confirm.
   - Ghi nhận `Scan_Result = {SKU, LOT, Location, Qty_Actual}`.

3) Gọi API Ship&Co và lưu nhãn  
   - Chọn kho xuất (Gifu/Amagasaki) theo route_rule, chọn dịch vụ/packaging.  
   - Tạo label → lưu PDF vào S3 → đính link vào `Orders_App`.

4) Webhook xác nhận xuất và thông báo  
   - Khi Task/出荷確定: giảm tồn (chi tiết xem Q&A bên dưới).  
   - Ghi `tracking_no` về `Orders_App`.  
   - Gửi thông báo cho khách (Email/LINE) kèm short link tới tracking/label.

5) Đồng bộ WMS và retry  
   - Đồng bộ chênh lệch với WMS ZERO/Lite (push/pull).  
   - Lập lịch retry khi lỗi giao tiếp.

6) Ngoại lệ và DLQ  
   - Sai địa chỉ, lỗi carrier, rate limit: đẩy vào DLQ.  
   - Hỗ trợ quy trình label void và re-ship.

## Đầu ra

- Tracking number, file nhãn (PDF) trên S3, tồn kho cập nhật, thông báo đã gửi khách.

## Q & A: Stock & WMS

- Master tồn kho theo kho (尼崎・岐阜)?  
  - Khuyến nghị: Kintone `Stock_App` là master logic; WMS là nguồn physical theo Shelf/LOT.  
  - Nếu kho dùng WMS chặt chẽ, physical side do WMS làm master, Kintone phản chiếu.

- Cơ chế 減算 (khi 出荷確定 giảm ở đâu trước?)  
  - Logic: giảm trên `Stock_App` ngay khi xác nhận (đảm bảo không oversell).  
  - Physical: giảm trong DB/WMS sau khi quét + confirm. Nếu WMS là master physical, cập nhật WMS trước rồi phản chiếu về Kintone.

- Realtime sync hay batch?  
  - Tối ưu: realtime cho tracking và label; tồn kho physical có thể realtime nếu API ổn định, nếu không dùng batch cuối ngày + reconciliation.

- WMS API spec?  
  - Xác nhận khả năng push/pull. Nếu chỉ CSV import/export:  
    - Xuất CSV delta mỗi phiên/ cuối ngày, nhập ngược lại để reconciliation.  
    - Cần mapping Shelf/LOT để không mất chi tiết.

## Lưu ý vận hành

- Idempotency khi tạo label (đảm bảo không sinh trùng trên retry).  
- Lưu đầy đủ audit log: ai scan, thời điểm, thiết bị, mã kệ/LOT/số lượng.  
- Short link nên có TTL hoặc token để kiểm soát truy cập công khai.

