# Business Flow 5 — Giám sát tồn → Tự động tạo PO + Cổng NCC/EDI

## Tác nhân
- Tồn kho tại kho đạt ngưỡng `reorder_point` (Amagasaki/Gifu)

## Hệ thống chính
- Kintone: `Stock_App`, `Suppliers_App`, `PO_App`, `Audit_Log`
- Ruby_Server: giám sát, sinh PO, gửi email, portal/EDI, retry, audit
- S3: lưu PO PDF, lịch sử gửi/nhận
- Supplier Guest Portal / EDI REST (nếu có)

## Các bước

1) Giám sát tồn và `reorder_point`  
   - Batch/Realtime theo SKU×Kho; khi `on_hand ≤ reorder_point` → tạo trigger PO theo nhà cung cấp mặc định của SKU.

2) Chuẩn hóa dữ liệu master  
   - Kiểm tra `supplier_id`, `reorder_point`, điều khoản thanh toán; cảnh báo nếu thiếu/không hợp lệ.

3) Sinh PO PDF  
   - Nội dung: mặt hàng, số lượng, ngày mong muốn, điều khoản thanh toán.  
   - Lưu PDF lên S3 và đính vào `PO_App`.

4) Email PO tự động  
   - Gửi email kèm link tải PDF; theo dõi mở/nhận; auto resend khi không phản hồi theo SLA.

5) Quy tắc mua (lot/gom/batch)  
   - Áp dụng lot size, gom theo nhà cung cấp, batch ngày/tuần; hợp nhất dòng cùng NCC.

6) Phê duyệt nội bộ  
   - Khi vượt ngưỡng/ngoại lệ: yêu cầu duyệt; hỗ trợ tách đơn, đặt lại, hủy, versioning.

7) Ngoại lệ vận hành  
   - Chênh lệch, thiếu/phần, trễ hạn → cảnh báo, tạo nhiệm vụ tái kế hoạch.

8) Giám sát & log kiểm toán  
   - Metrics, alert khi NCC không phản hồi; ghi `Audit_Log` đầy đủ.

9) Cổng NCC (Guest)  
   - NCC xem PO, nhập ngày hứa giao, cho phép phân lô (分納), bình luận; mọi thao tác có audit.

10) EDI (nếu có)  
   - Webhook từ Kintone → Supplier REST; có retry, log và đối soát phản hồi.

## Đầu ra
- PO PDF trên S3, email đã gửi, nhiệm vụ trên cổng NCC, PO đã duyệt, bản ghi ngoại lệ

## Q&A: Supplier & 発注
- Low stock threshold nằm ở đâu? → `Stock_App.reorder_point` theo SKU×Kho.  
- Output 発注書: mặc định PDF+Email; Portal Guest cho NCC chính; EDI cho NCC lớn (phase theo lộ trình).  
- Cần chốt danh sách NCC dùng Portal/EDI ở phase 1 để cấu hình quyền & endpoint.

