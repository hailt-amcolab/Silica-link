# Business Flow 2 — Cổng đại lý B2B: nhập đơn + phê duyệt

## Tác nhân

- Đại lý (guest) gửi đơn trên Portal
- Nhân sự nội bộ nhập đơn FAX thay đại lý (đính kèm ảnh)

## Hệ thống chính

- Kintone Guest Space (Portal đại lý)
- Ruby_Server (điều phối, phê duyệt theo ngưỡng/ngoại lệ, tạo PDF)
- Kintone Apps: `Orders_App`, `Route/Products`, `Audit_Attachments`
- S3 (lưu chứng từ FAX/PDF phục vụ kiểm toán)

## Các bước xử lý

1) Đăng nhập & nhập đơn
- Đại lý đăng nhập guest space
- Form: tìm/nhập SKU, số lượng, nơi giao, ngày mong muốn, ghi chú

2) Tự hiển thị kho ứng viên & tồn tham chiếu
- Theo `route_rule` của `Products`, hiển thị kho đề xuất (Gifu/Amagasaki)
- Hiển thị tồn tham chiếu để đại lý cân nhắc

3) Gửi đơn → Phê duyệt nội bộ
- Gửi đơn kích hoạt kiểm tra ngưỡng (threshold) và ngoại lệ (rules)
- Nếu hợp lệ: phê duyệt, gửi thông báo kết quả cho đại lý
- Nếu ngoại lệ: xếp vào hàng đợi ngoại lệ để rà soát/nhập lại

4) Xử lý FAX (tuỳ chọn)
- Nhân sự tải ảnh FAX, lưu S3, link vào record để audit
- Trạng thái và lịch sử được ghi nhận (người xử lý, thời gian)

5) Tạo Order & PDF
- Khi được duyệt: tạo record `Orders_App`
- Sinh PDF: Phiếu nhận đơn / Báo giá / Phiếu giao
- Tạo link tải (S3) và gửi cho đại lý

6) Ngoại lệ
- Thiếu hàng hoặc vi phạm quy tắc → đẩy vào hàng đợi ngoại lệ để nhập lại

## Đầu ra

- Đơn đã duyệt trong `Orders_App`
- PDF chứng từ (acknowledgement/quote/delivery note) và link tải
- Dấu vết kiểm toán (ảnh FAX, metadata) lưu S3 + Kintone
- Hàng đợi ngoại lệ (nếu có) cho xử lý tiếp theo

## Lưu ý/ngoại lệ

- Route_rule: `MWATER = Gifu`, còn lại = `Amagasaki`
- PDF template và hồ sơ người gửi chọn theo `client_id`/ngôn ngữ
- Audit trail đầy đủ cho cả online submission và FAX upload

