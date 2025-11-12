# Business Flow 4: Đối soát thu (GMO) + Thực hiện chi

## Tác nhân Kích hoạt

- **AR (Thu)**: Ngân hàng phát sinh giao dịch (polling/webhook)
- **AP (Chi)**: Phiếu chi được duyệt trong Kintone

## Hệ thống Chính

- **GMO Aozora Bank API**: API ngân hàng cho đối soát và chuyển khoản
- **Kintone**: Orders/Invoices App, Payouts App
- **Ruby Server**: Xử lý logic đối soát và chuyển khoản
- **S3**: Lưu trữ chứng từ sao kê

## Quy trình Chi tiết

### AR (Thu) - Đối soát Tiền Về

#### Bước 1: Nạp giao dịch từ GMO API

- Gọi API `/transactions` với ký số (signature), xác thực, IP whitelist
- Hỗ trợ cả **Webhook** và **Polling** (có thể chuyển đổi)
- Lấy danh sách giao dịch nhận tiền (入出金明細取得)

#### Bước 2: Đối soát và ánh xạ

- **Ánh xạ ưu tiên**:
  1. **Tài khoản ảo (仮想口座ID)** → khớp chính xác
  2. **Số tiền** → kiểm tra khớp
  3. **Ngày/Ghi chú** → đối chiếu bổ sung
- Ghép giao dịch vào **Orders/Invoices** tương ứng
- Cập nhật **PayStatus** = `PAID` hoặc `PARTIAL_PAID`

#### Bước 3: Xử lý chưa khớp

- Giao dịch không khớp → đẩy vào danh sách **未入金 (Unpaid)**
- Tự động tạo:
  - Cảnh báo **quá hạn thanh toán**
  - **Nhắc nhở** cho khách hàng/đại lý
  - **Báo cáo** tổng hợp chưa thanh toán

---

### AP (Chi) - Thực hiện Chuyển khoản

#### Bước 4: Chuyển khoản tức thời

- Sau khi **duyệt trên Kintone** (Payouts App)
- **Ruby Server** gọi API `/payments` (chuyển khoản tức thời)
- Cập nhật kết quả vào **Payouts/Orders**:
  - Trạng thái: `完了` (Thành công) hoặc `失敗` (Thất bại)
  - **Phí chuyển khoản** (手数料)
  - Mã giao dịch, thời gian

#### Bước 5: Xử lý ngoại lệ

- **Ngoại lệ phát sinh**:
  - **Sai tên** (名義不一致)
  - **Trùng giao dịch** (重複入金)
  - **Lệch số tiền** (金額差異)
- Đẩy vào hàng đợi **Exceptions**
- Cung cấp **UI xử lý thủ công**:
  - Xem chi tiết ngoại lệ
  - Đối soát lại (再照合)
  - Xác nhận thủ công (手動確定)

#### Bước 6: Sinh chứng từ và kiểm toán

- Tự động sinh:
  - **CSV/PDF sao kê Payouts**
  - Lưu lên **S3** (có tổ chức theo tháng/năm)
- Tính năng tra cứu:
  - Tìm kiếm giao dịch (電文検索)
  - Xuất báo cáo phục vụ **kiểm toán** (監査対応)

---

## Đầu ra

- ✅ Giao dịch đã đối soát (Orders/Invoices với PayStatus)
- ✅ Khoản chi đã thực hiện (Payouts với trạng thái và phí)
- ✅ Chứng từ sao kê (CSV/PDF trên S3)
- ✅ Bản ghi ngoại lệ (Exceptions queue với lịch sử xử lý)

---

## Q&A: Bank (GMO あおぞら)

### API Scope

**Q: API scope: chỉ入出金照合 hay cả 振込実行?**

**A**: Hệ thống sử dụng cả hai:
- **入出金照合** (`/transactions`): Đối soát giao dịch nhận tiền (AR)
- **振込実行** (`/payments`): Thực hiện chuyển khoản (AP)

### Virtual Account

**Q: Có dùng仮想口座 (Virtual Account) cho từng partner không?**

**A**: Có, hệ thống tạo **仮想口座ID** cho mỗi đơn hàng/đại lý:
- Mỗi đơn hàng B2B được gán một VA ID duy nhất
- VA ID được lưu trong **Invoice_App**
- GMO API trả về giao dịch kèm VA ID để đối soát tự động

### Error Handling

**Q: Error handling: 名義不一致 thì xử lý ở đâu (Exceptions queue?)**

**A**: Đúng, xử lý qua **Exceptions queue**:
1. Khi phát hiện **名義不一致** (sai tên người nhận)
2. Giao dịch được đẩy vào **Exceptions App** trong Kintone
3. Nhân viên kế toán xử lý thủ công qua UI:
   - Xem chi tiết giao dịch
   - Đối soát lại với đơn hàng
   - Xác nhận hoặc từ chối thủ công
4. Sau khi xử lý, cập nhật trạng thái và ghi log

---

## Hạng mục Công việc (Estimate)

| Hạng mục | Nội dung | Effort (h) |
| --- | --- | --- |
| **A. 銀行API連携開発 (GMO)** | 入出金明細取得/署名・認証・IP許可/Webhook or Polling (/transactions) 切替 | 36 |
| **B. 入金照合ロジック開発** | 仮想口座IDで厳密一致 → 金額 → 日付・メモの優先突合/PayStatus 更新 | 48 |
| **C. 未入金抽出機能** | 期限切れ・リマインド・レポート生成 | 20 |
| **D. 即時振込API実装** | Kintone承認 → /payments 実行 → 結果を Payouts/Orders 反映 (手数料・状態管理) | 24 |
| **E. テスト・運用** | 決済テスト・金融API安定化・運用手順 | 16 |
| **F. 例外処理 (名義不一致) NEW** | 名義不一致・重複入金・金額差異を Exceptions キュー へ送出/再照合・手動確定UI | 12 |
| **G. 明細・帳票 (Payouts) NEW** | Payouts の CSV/PDF 生成・S3保管・電文検索エクスポート (監査対応) | 8 |
| **Tổng cộng** | | **164** |

---

## Luồng Dữ liệu

### AR Flow (Thu)
```
GMO API (/transactions) 
  → Ruby Server (Đối soát)
  → Orders/Invoices App (Cập nhật PayStatus)
  → Thông báo khách hàng
```

### AP Flow (Chi)
```
Kintone Payouts App (Duyệt)
  → Ruby Server (Gọi /payments)
  → GMO API (Chuyển khoản)
  → Payouts App (Cập nhật kết quả)
  → S3 (Lưu chứng từ)
```

---

## Liên kết Flow Khác

- **Flow 2**: B2B注文入力 (Tạo Invoice và VA ID khi đơn được duyệt)
- **Flow 5**: 資材購買サイクル (Thanh toán nhà cung cấp qua Payouts)
- **Flow 7**: 支払いフロー（AR/AP統合）(Chi tiết hơn về luồng thanh toán)

