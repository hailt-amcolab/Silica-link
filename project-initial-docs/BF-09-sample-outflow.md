# Business Flow — Xuất mẫu dùng thử (Sales → Kho)

## Tác nhân
- Sales yêu cầu xuất mẫu (sample request)

## Hệ thống chính
- Kintone: `StockMovement_App` (movement_type = SAMPLE_OUT), `Stock_App`, `Audit_Log`
- Scan UI (Mobile/Device): quét barcode JAN/EAN, kệ, LOT
- Ruby_Server: idempotency, điều phối giảm tồn, đối soát WMS, thông báo/queue
- WMS ZERO/Lite hoặc Physical_DB: tồn kho vật lý theo Kệ×LOT

## Các bước xử lý

1) Quy trình & vai trò, ngưỡng/phê duyệt (tùy chọn)  
   - Xác định vai trò: Sales tạo yêu cầu, Kho thực thi, Ops phê duyệt nếu vượt ngưỡng.  
   - Thiết lập ngưỡng số lượng/giá trị để yêu cầu phê duyệt.

2) Tạo bản ghi StockMovements  
   - Tạo record trong `StockMovement_App` với các trường:  
     - `movement_type = SAMPLE_OUT`  
     - `sku`, `qty`, `rep_id` (nhân sự yêu cầu), `reason`, `requested_at`

3) Form di động/thiết bị (Scan UI)  
   - Quét SKU (JAN/EAN), nhập số lượng & lý do; có thể đính kèm ảnh minh chứng.  
   - Hỗ trợ offline cache và đồng bộ lại khi có mạng.

4) Chuỗi quét & kiểm tra idempotency  
   - Thứ tự: Quét kệ (Shelf) → Quét sản phẩm/LOT → Nhập/Quét số lượng.  
   - Kiểm tra số lượng khả dụng; áp dụng idempotency key theo `movement_id+line_no`.

5) Xác nhận & trừ tồn  
   - Khi xác nhận: giảm tồn vật lý tại kho (Amagasaki/Gifu) theo Kệ×LOT ngay.  
   - Cập nhật/đồng bộ WMS (nếu dùng) và xử lý chênh lệch (delta reconciliation).

6) Nhật ký & audit  
   - Ghi `Audit_Log`: ai/thời điểm/SKU/LOT/Kệ/số lượng/thiết bị/ảnh.  
   - Hỗ trợ tìm theo kỳ/nhân sự/SKU và xuất CSV.

7) Ngoại lệ & thông báo  
   - Thiếu tồn, sai SKU, lỗi quét → đẩy vào queue xử lý (DLQ) + thông báo Slack/Email.  
   - Cho phép retry an toàn (idempotent).

8) Dashboard (tùy chọn)  
   - Số mẫu theo tháng, xếp hạng theo nhân sự/SKU, ảnh hưởng tồn logic/physical.

## Đầu ra
- Record movement (SAMPLE_OUT), tồn kho giảm, nhật ký/audit đầy đủ, thông báo gửi đi, dashboard (nếu bật)

## Cấu trúc dữ liệu mẫu (StockMovement_App)
| Trường | Kiểu | Mô tả |
| --- | --- | --- |
| movement_id | Text | ID bản ghi (unique) |
| movement_type | Enum | `SAMPLE_OUT` |
| sku | Text | Mã sản phẩm |
| qty | Number | Số lượng xuất mẫu |
| rep_id | User | Nhân sự yêu cầu/đại diện bán |
| reason | Text | Lý do xuất mẫu |
| shelf_from | Text | Mã kệ xuất |
| lot_no | Text | LOT sản xuất |
| photo_url | Link | Ảnh minh chứng (tùy chọn) |
| status | Enum | `Pending / In Progress / Completed / Error` |
| idempotency_key | Text | Khóa chống trùng (movement_id + line_no + hash) |
| executed_at | DateTime | Thời điểm hoàn tất |

## Trạng thái & đồng bộ
- `Pending` → tạo yêu cầu; `In Progress` → đang quét; `Completed` → đã trừ tồn & ghi audit; `Error` → vào DLQ.  
- Kết nối WMS/Physical_DB theo mô hình: Physical giảm tại thời điểm xác nhận; Logic giảm đồng bộ/định kỳ tùy kiến trúc.

## Lưu ý vận hành
- Thiết lập giới hạn số mẫu theo kỳ & theo nhân sự/SKU.  
- Gắn cost center/campaign code nếu cần tính chi phí marketing.  
- Báo cáo định kỳ gửi Sales/Ops về tổng tiêu thụ mẫu và chuyển đổi đơn hàng (nếu có).

