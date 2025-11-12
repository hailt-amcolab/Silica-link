# Business Flow 7 — Xếp hạng đại lý & quản lý chiết khấu

## Tác nhân
- Job tổng hợp chạy hàng tháng (Scheduler/Lambda)

## Hệ thống chính
- Kintone: `Agency_Rank_App`, `Orders_App`, `Pricing_Rules_App`, `Portal_API`
- Ruby_Server: batch tổng hợp, xác định hạng, sinh/áp giá, thông báo
- Portal (Agent): widget gauge, API áp giá

## Các bước

1) Tổng hợp mua theo đại lý (idempotent)  
   - Gom doanh số/số lượng theo kỳ tháng từ `Orders_App` (đã PAID).  
   - Ghi log idempotent key để có thể re-run an toàn.

2) Xác định hạng và hiệu lực  
   - Tính hạng: `Starter / Silver / Gold / Platinum`.  
   - Lưu hiệu lực (valid_from/to), ưu tiên, và lịch sử thay đổi.

3) Thông báo & áp giá  
   - Gửi thông báo thay đổi hạng; chạy job áp giá vào `Pricing_Rules_App`.  
   - Cảnh báo khi áp giá lỗi; có retry và log.

4) Liên kết Portal (API/Rule)  
   - Cung cấp API/Rule để áp giá “ngay” (realtime) hoặc “ban đêm” (batch).  
   - Hỗ trợ giá đặc biệt/loại trừ theo SKU; kiểm tra nhất quán trước khi publish.

5) API gauge (tiến độ hạng)  
   - API trả về “còn ◯ chai” để đạt hạng kế tiếp, cho widget Portal.

## Đầu ra
- Trạng thái hạng theo đại lý, bộ quy tắc giá đang hiệu lực, thông báo đã gửi, giá trị gauge cho Portal

## Hạng mục công việc (tham chiếu estimate)

| Hạng mục | Nội dung | Effort (h) |
| --- | --- | ---: |
| A. ランク計算Lambda | Tổng hợp nhập bản/đơn vị; phán định hạng; idempotency; audit log | 32 |
| B. 掛け率管理 | Starter/Silver/Gold/Platinum: tỉ lệ, hiệu lực, ưu tiên | 20 |
| C. 自動更新・通知 | Thông báo đổi hạng; job phản ánh giá; alert | 18 |
| D. 掛け率・価格自動適用（Portal連動） NEW | API/Rule áp giá realtime/ban đêm; giá đặc biệt/loại trừ; kiểm tra nhất quán | 20 |
| E. ランク/特典ゲージAPI（あと◯本） NEW | API trả về tiến độ tới hạng kế tiếp (số chai còn thiếu) | 16 |
| F. テスト・UAT | E2E (tổng hợp→phán định→phản ánh giá→UI); biên kịch & hướng dẫn | 12 |

## Q&A: Dividend / Reward
- Logic配当: MW = 2円 cố định; SW = 5円 → 3円 khi vượt 5,000万円 (ngưỡng).  
- Chu kỳ lock: 四半期末 + 1日; xuất PDF và lưu S3.  
- Báo cáo chi tiết partner: khuyến nghị có bảng kê theo partner/tháng/quý; export CSV/PDF.

