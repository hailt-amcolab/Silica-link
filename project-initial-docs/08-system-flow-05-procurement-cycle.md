# System Flow 5: 資材購買サイクル

## Mục tiêu

Quy trình này quản lý toàn bộ vòng đời **mua hàng vật tư (資材購買)** từ việc giám sát tồn kho, tạo đơn đặt hàng (PO), phê duyệt, thanh toán, đến nhập kho nhiều đợt (分納).

Hệ thống được tích hợp chặt chẽ với **Meka Portal** và **GMO銀行API** để tự động hóa luồng dữ liệu giữa nhà cung cấp và Kintone.

Mục tiêu chính:
- **Loại bỏ WMS cũ**, thay bằng **Warehouse_UI (ReactJS)** để quét barcode khi nhập kho
- Thanh toán **1 lần duy nhất trước khi nhận hàng**, nhưng **cho phép chia nhiều lần giao hàng (分納)**
- Quy trình **2 cấp phê duyệt**:
  - Cấp 1: duyệt PO trong Kintone (Purchasing_Staff)
  - Cấp 2: duyệt thanh toán trong Payout App (CEO)
- Sau phê duyệt cuối → tự động **chuyển khoản qua GMO API**, cập nhật trạng thái thanh toán & giao hàng giữa Kintone và Meka Portal

## Thành phần Hệ thống tham gia

| Thành phần | Vai trò |
| --- | --- |
| 🗂️ Partner_Master | Quản lý thông tin nhà cung cấp Meka (tài khoản ngân hàng, liên hệ, ID portal) |
| 📈 Stock_App | Theo dõi tồn kho vật tư và ngưỡng đặt hàng (発注点) |
| 🖥️ Ruby_Server | Xử lý logic tự động: tạo PO, gọi API GMO, đồng bộ dữ liệu với Meka Portal |
| 🙋‍♀️ Purchasing_Staff | Nhân viên mua hàng — phê duyệt cấp 1 (一次承認) |
| 👑 Representative | CEO — phê duyệt cấp 2 (最終承認) trước khi chuyển khoản |
| 📝 PO_App | Quản lý đơn đặt hàng (PO), phê duyệt cấp 1 và trạng thái nhập kho |
| 💸 Payout_App | Quản lý thanh toán (tạo sau duyệt cấp 2, gắn link PO) |
| 🌐 Meka_Portal | Hệ thống portal nhà cung cấp (xác nhận đơn, phát hành invoice, chia batch giao hàng) |
| 🚚 Meka | Nhà cung cấp vật tư |
| 🏦 GMO_API | API ngân hàng thực hiện chuyển khoản tự động |
| 📱 Warehouse_UI | Màn hình nhập kho (ReactJS), dùng để quét barcode, kệ, LOT khi nhập hàng |

## Quy trình chi tiết

### 1️⃣ Đăng ký nhà cung cấp (Meka)
- Nhân viên mua hàng tạo record **Meka Supplier** trong `Partner_Master` (bao gồm tên, email, tài khoản ngân hàng).
- Ruby_Server tự động phát hành tài khoản Meka trên **Meka Portal** và gửi email đăng nhập.

---

### 2️⃣ Giám sát tồn kho và tạo PO tự động
- Ruby_Server định kỳ kiểm tra dữ liệu tồn kho từ **Stock_App**.
- Khi phát hiện SKU có số lượng < 発注点 (ngưỡng đặt hàng):
  1. Ruby_Server tự động tạo record **PO** trong `PO_App`.
  2. Gửi thông báo cho **Purchasing_Staff** để phê duyệt cấp 1.
  3. Purchasing_Staff xem xét và phê duyệt (`一次承認`).
  4. Ruby_Server gửi PO sang **Meka Portal**.
  5. Meka xác nhận đơn hàng, chọn ngày giao và số lần chia lô (分納).
  6. Lịch giao hàng được phản ánh ngược lại về `PO_App`.

---

### 3️⃣ Thanh toán (一次承認 + 最終承認 + 自動振込)

#### 🧾 Bước 1: Nhận invoice và tạo record thanh toán
- Meka Portal gửi dữ liệu **invoice (請求書)** sang **Ruby_Server**.
- Ruby_Server tạo record **支払依頼 (Yêu cầu thanh toán)** trong `Payout_App`, gắn link đến PO tương ứng.

#### 🧍‍♀️ Bước 2: Phê duyệt cấp 1 (PO)
- Trước đó, **PO_App** đã được duyệt cấp 1 bởi **Purchasing_Staff** khi xác nhận đơn hàng.
- Đây là xác nhận nội bộ cho phép tiếp tục quá trình thanh toán.

#### 👑 Bước 3: Phê duyệt cấp 2 (Payout)
- **Representative (CEO)** mở record trong `Payout_App` và thực hiện **最終承認 (duyệt cấp 2)**.
- Sau khi duyệt, Kintone gửi trigger Webhook tới **Ruby_Server**.

#### 🤖 Bước 4: Tự động thực hiện chuyển khoản
- Ruby_Server gọi **GMO_API /payments** để thực hiện chuyển khoản.
- Khi giao dịch thành công:
  - Cập nhật `Payout_App.Status = 完了`
  - Cập nhật `PO_App.Status = PAID`
  - Gửi phản hồi sang **Meka Portal** (hiển thị “支払い済み”)
- Nếu lỗi khi gọi API → ghi log vào `Payment Error App` và gửi email cảnh báo cho nhân viên mua hàng.

---

### 4️⃣ Nhập kho nhiều đợt (分納対応)
Sau khi Meka nhận được thông báo thanh toán hoàn tất:
1. Meka chia đơn hàng thành các **batch giao hàng**.
2. Mỗi batch được xác nhận trên **Meka Portal** → gửi API trigger đến Ruby_Server.
3. Ruby_Server:
   - Cập nhật `PO_App.Status = 部分入庫中`
   - Tự động tạo **Task nhập kho (入庫タスク)** trong **Warehouse_UI**
4. Nhân viên kho thực hiện nhập hàng qua **Warehouse_UI**:
   - Quét **barcode sản phẩm**
   - Quét **mã kệ (棚コード)**
   - Nhập **LOT番号**
   - Xác nhận hoàn tất → hệ thống:
     - Cộng tồn kho logic trong **Stock_App**
     - Ghi nhận batch nhập vào **PO_App**
     - Nếu chưa hoàn tất toàn bộ: `PO_App.Status = 部分完了`
     - Nếu đã nhập đủ: `PO_App.Status = 完了`
5. Ruby_Server đồng bộ trạng thái nhập kho sang **Meka Portal** (đánh dấu “納品済み”).

---

## 🧮 Logic tồn kho

| Loại tồn kho | Thời điểm cập nhật | Khi tăng | Khi giảm |
| --- | --- | --- | --- |
| **論理在庫 (Logic Stock)** | Khi xác nhận nhập hàng trên Warehouse_UI | Sau khi nhập batch vật tư | Khi vật tư được sử dụng cho sản xuất |
| **実在庫 (Physical Stock)** | Quản lý tại Warehouse_UI (棚・LOT単位) | Khi nhân viên quét barcode và xác nhận nhập | Khi xuất vật tư thực tế |

---

## ⚠️ Ngoại lệ & Xử lý lỗi

| Tình huống | Hành động |
| --- | --- |
| Lỗi khi chuyển khoản (GMO_API Error) | Ruby ghi log vào `Error App` và gửi thông báo cho nhân viên mua hàng |
| Giao thiếu hàng | Giữ `PO_App.Status = 部分入庫中` đến khi batch kế tiếp được nhập |
| Hủy PO sau khi đã thanh toán | Tạo record hoàn tiền (Refund) thủ công trong `Payout_App` |
| Không nhập đủ thông tin LOT hoặc mã kệ | Warehouse_UI hiển thị cảnh báo, không cho phép “完了” |
| Sai lệch tồn kho | Cho phép điều chỉnh thủ công trong `Stock_History_App` (ghi log lịch sử) |

---

## ✅ Kết quả mong đợi

- Quy trình mua hàng được tự động hóa hoàn toàn, hỗ trợ chia batch giao hàng (分納)
- Phê duyệt 2 cấp được tách rõ ràng giữa `PO_App` và `Payout_App`
- Thanh toán được thực hiện tự động qua **GMO API**, không cần thao tác thủ công
- Trạng thái thanh toán và nhập kho được đồng bộ 2 chiều giữa **Kintone** và **Meka Portal**
- Mọi hành động đều được ghi lại trên các app liên quan (`PO_App`, `Payout_App`, `Stock_App`, `Warehouse_UI`) giúp audit dễ dàng

## Sequence Diagram

```mermaid
sequenceDiagram
    title 資材購買サイクル: Mekaポータル → 支払い → 入庫 (二段階承認付き)

    participant Partner_Master as 🗂️ Kintone 代理店マスタ
    participant Stock_App as 📈 Kintone 資材在庫アプリ
    participant Ruby_Server as 🖥️ ロジックサーバー
    participant Purchasing_Staff as 🙋‍♀️ 購買担当
    participant Representative as 👑 代表者
    participant PO_App as 📝 Kintone PO App (Flow 5)
    participant Meka_Portal as 🌐 Mekaポータル
    participant Meka as 🚚 Meka (仕入先)
    participant GMO_API as 🏦 GMO銀行 API
    participant Warehouse_UI as 📦 WMS入庫UI/端末
    
    note over Partner_Master, Purchasing_Staff: 【ステップ0】仕入先アカウント発行 (Meka登録)
    
    %% 0. ACCOUNT 発行
    Purchasing_Staff->>Partner_Master: 0. 仕入先情報入力・登録 (Meka情報)
    Partner_Master->>Ruby_Server: [Trigger] ポータルアカウント発行
    Ruby_Server->>Meka: 0. ログイン情報とURLをメール送付
    
    note over Stock_App, Purchasing_Staff: 【ステップ1】在庫監視とPO作成・Meka確認

    %% 1. PO作成とMekaとの確認
    loop 定期バッチ or 在庫変動トリガー
        Ruby_Server->>Stock_App: 1. 在庫残高の取得
        Ruby_Server->>Partner_Master: 2. 発注点/サプライヤーIDを取得
        
        alt 在庫残高 < 発注点
            Ruby_Server->>PO_App: 3. POレコードを自動作成
            PO_App->>Purchasing_Staff: 4. PO作成通知/承認要求 (担当者)
            Purchasing_Staff->>PO_App: 5. POの承認/確定
            PO_App->>Ruby_Server: 6. [Trigger PO確定] 通知プロセス起動
            Ruby_Server->>Meka_Portal: 7. PO情報をポータルへ反映
            Meka->>Meka_Portal: 8. POを確認し、納品日・分納可否を確定
            Meka_Portal->>PO_App: 9. 納品情報(納期/分納)をKintoneへ連携
        else 在庫が発注点を上回っている場合
            Ruby_Server->>Ruby_Server: 3x. 処理終了
        end
    end

    note over PO_App, Warehouse_UI: 【ステップ2】請求と支払い (二段階承認)

    %% 2. 請求と支払い (前払い - 二段階承認)
    Meka_Portal->>Ruby_Server: 10. [Trigger] 請求書データ連携 (Invoice Data)
    Ruby_Server->>Payout_App: 11. 支払依頼レコード作成/POと紐付け
    PO_App->>Purchasing_Staff: 12. 支払い承認要求 (一次承認)
    Purchasing_Staff->>PO_App: 13. 支払い承認 (一次承認)
    PO_App->>Representative: 14. 支払い承認要求 (代表者/最終承認)
    Representative->>PO_App: 15. 支払い承認 (最終承認)
    PO_App->>Ruby_Server: 16. [Trigger 最終承認] 振込実行プロセス起動
    Ruby_Server->>GMO_API: 17. 振込実行APIコール
    GMO_API-->>Ruby_Server: 18. 実行結果返却
    Ruby_Server->>Payout_App: 19. 支払ステータスを 完了 に更新
    PO_App->>PO_App: 20. POステータス更新 (PAID)

    note over Meka, Warehouse_UI: 【ステップ3】出荷と入庫検品 (分納対応)

    %% 3. 出荷と入庫 (分納対応)
    PO_App->>Meka: 21. [Trigger PAID] 支払い完了通知/資材出荷依頼
    loop 分納回数
        Meka->>Warehouse_UI: 22. 資材を出荷・納品 (分納分)
        Warehouse_UI->>PO_App: 23. 入庫予定の確認（該当POの呼び出し）
        Warehouse_UI->>Warehouse_UI: 24. 検品開始（スキャン端末使用）
        Warehouse_UI->>PO_App: 25. 検品結果と部分納品をPOに記録 (残数管理)
        Warehouse_UI->>Warehouse_UI: 26. 保管棚バーコードをスキャンし、検品確定
        Warehouse_UI->>Stock_App: 27. 資材在庫への入庫計上
        note right of Stock_App: 在庫残高を増加 (納品された分のみ)
    end
    PO_App->>PO_App: 28. POのステータスを更新（完了または部分完了）
```

## Liên kết Flow Khác

- **Flow 4**: 製品・資材・製造・在庫管理フロー (xuất vật tư cho sản xuất)
- **Flow 1/2**: Xuất bán (ảnh hưởng tồn)


