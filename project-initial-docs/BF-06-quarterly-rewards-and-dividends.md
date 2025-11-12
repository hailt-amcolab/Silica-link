# Business Flow 6 — Quản lý Hoa hồng & Cổ tức (theo quý)

## Tác nhân
- Đơn xác nhận/Refund phát sinh (từ Orders/EC Webhook)
- Chuyển quý (cuối quý → ngày +1)

## Hệ thống chính
- Kintone: `RewardsRules_App`, `RewardsLedger_App`, `Payouts_App`, `Quarter_Master`, `Directors_Dashboard`
- Ruby_Server: tính toán lũy kế/quy tắc, batch cuối quý, PDF, GMO payouts, giám sát
- GMO API: `/payments` (chi trả)
- S3: lưu PDF sao kê/khóa sổ

## Các bước

1) Đơn giá & ngưỡng lũy kế  
   - Tính đơn giá MWATER/SILICA_H2, quản lý ngưỡng lũy kế (ví dụ SW 5→3 yên sau 50 triệu yên).  
   - Lưu cấu hình trong `Quarter_Master` để truy vết.

2) Quản lý quý & khóa sổ  
   - Duy trì danh mục quý; batch tự chuyển quý; khóa sổ vào ngày +1 sau cuối quý.  
   - Khi khóa: chốt số liệu, đóng ghi mới cho kỳ đó (chỉ cho phép điều chỉnh vào kỳ kế tiếp).

3) RewardRules (phạm vi & ưu tiên)  
   - Định phạm vi theo `sku/category/channel`, kiểu tính `unit/rate`, thời hạn hiệu lực, mức ưu tiên.  
   - Hỗ trợ thay đổi quy tắc theo kỳ mà không ảnh hưởng kỳ đã khóa.

4) Ghi Ledger theo sự kiện  
   - Khi đơn xác nhận: tự ghi vào `RewardsLedger` theo Rule và tỷ giá (đa tiền tệ an toàn).  
   - Refund: ghi âm (negative entry).  
   - Trạng thái lifecycle: `pending → approved → paid`.

5) Tổng hợp cuối quý & PDF  
   - Batch tổng hợp ledger theo partner/quý; tạo bảng kê; xuất PDF sao kê; lưu S3; cập nhật `Quarter_Master` là `locked`.

6) Chi trả qua GMO  
   - Tạo `Payouts` cho các partner; gọi GMO `/payments`; ghi kết quả & phí về `Payouts/RewardsLedger`.

7) Phát hành sao kê & Dashboard  
   - Phát hành PDF sao kê chi; tạo link cổng (portal).  
   - Dashboard Directors: tình trạng chi trả, tổng hợp kỳ, xu hướng.

8) Giám sát & kiểm toán  
   - Chống trùng (idempotency), DLQ cho lỗi batch/webhook; cảnh báo CloudWatch; ghi `Audit_Log`.

## Đầu ra
- Bản ghi `RewardsLedger`, `Payouts`, PDF sao kê, dashboard, quý đã `locked`

## Hạng mục công việc (tham chiếu estimate)

| Hạng mục | Nội dung | Effort (h) |
| --- | --- | ---: |
| A. 配当計算エンジン | MWATER/SILICA_H2 đơn giá; quản trị lũy kế | 60 |
| B. 四半期処理 | Quản lý 20 quý (5 năm), tự chuyển/khoá | 40 |
| C. レート管理 | Ngưỡng 50M: SW 5→3 yên; cấu hình theo kỳ | 32 |
| D. 期末ロック・PDF | Lock +1 ngày; xuất PDF; lưu S3 | 28 |
| E. ダッシュボード | Directors dashboard (trạng thái/biểu đồ) | 40 |
| F. テスト・運用 | Kiểm thử & quy trình vận hành | 20 |
| G. RewardRules 設計・実装 NEW | Phạm vi, kiểu, ưu tiên, thời hạn | 20 |
| H. RewardsLedger 自動計上 NEW | Webhook EC; ghi âm refund; trạng thái | 28 |
| I. 返品・調整ロジック NEW | refunds.create, điều chỉnh & audit | 12 |
| J. GMO振込連携 NEW | Payouts → `/payments`, phản ánh kết quả & phí | 20 |
| K. 支払明細PDF生成/配布 NEW | Tạo PDF chi; lưu S3; link email/portal | 8 |
| L. 報酬ダッシュボード統合 NEW | Tổng hợp SaaS + giới thiệu phí; filter/export | 24 |
| M. 監視・例外処理（重複・DLQ） NEW | Ngăn ghi trùng; DLQ; cảnh báo & audit | 12 |
| Tổng cộng |  | 328 |

## Q&A
- Quy tắc thay đổi giữa kỳ? → Tạo Rule mới hiệu lực từ kỳ tiếp theo; không sửa kỳ `locked`.  
- Đa tiền tệ? → Chuẩn hóa về JPY tại thời điểm ghi sổ; lưu nguyên tệ và tỷ giá để audit.  
- Refund muộn sau khoá? → Ghi điều chỉnh vào kỳ kế tiếp với link tham chiếu.

