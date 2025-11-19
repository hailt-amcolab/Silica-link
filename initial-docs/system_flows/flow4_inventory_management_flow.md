# Flow4: 製品・資材・製造・在庫管理フロー

```mermaid
sequenceDiagram
    title Flow Y (改訂版): 製品・資材・生産・在庫管理フロー - CRUD + 生産記録 + 出入庫タスク連携

    participant Master_Admin as 👨‍💻 運用者 (CRUD操作)
    participant Product_App as 🗂️ Kintone 製品マスタ
    participant Material_App as 📉 Kintone 資材マスタ
    participant BOM_App as 📦 Kintone BOMアプリ
    participant Production_App as 🧾 Kintone 製造実績アプリ
    participant Task_App as 📋 Kintone タスクアプリ（出入庫管理）
    participant Stock_App as 📈 Kintone 在庫アプリ（論理在庫）
    participant Physical_DB as 🏭 実在庫DB（棚・LOT単位）
    participant Ruby_Server as 🖥️ Ruby Server（業務ロジック）
    participant Warehouse_UI as 📱 倉庫端末（ReactJS Barcode）

    note over Master_Admin, Warehouse_UI: 【目的】製品・資材・生産・棚・LOT単位の在庫を一元的に管理。<br>バーコードは外部印刷で運用。

    %% === 1. 製品・資材・BOMマスタ管理 ===
    Master_Admin->>Product_App: 1. 製品マスタ登録/更新 (SKU, JAN, 名称, 単位, 規格など)
    Master_Admin->>Material_App: 2. 資材マスタ登録 (資材コード, 種別[原料/OEM], 発注点)
    Master_Admin->>BOM_App: 3. BOM登録 (製品SKU + 資材 + 消費数量)
    note right of BOM_App: 各製品と資材の構成関係を管理

    %% === 2. 生産開始・完了 ===
    Master_Admin->>Production_App: 4. 製造レコード作成 (製品, 数量, 担当者, 日付)
    note right of Production_App: 製造開始時・完了時に同一レコードを更新

    Production_App->>Ruby_Server: 5. [Trigger] 出入庫タスク作成プロセス起動

    %% === 3. 出入庫タスク自動生成 ===
    Ruby_Server->>Task_App: 6a. 出庫タスク生成 (資材出庫)
    Ruby_Server->>Task_App: 6b. 入庫タスク生成 (製品入庫)
    note right of Task_App: 資材を出庫し、完成品を入庫するタスクが自動生成される

    %% === 4. 倉庫担当者の実作業（ReactJS UI） ===
    Task_App->>Warehouse_UI: 7. タスク一覧を表示
    Warehouse_UI->>Warehouse_UI: 8. バーコード／棚コード／LOT番号をスキャンして作業実施

    alt 資材出庫
        Warehouse_UI->>Physical_DB: 9a. 実在庫を減算 (棚・LOT単位)
        Warehouse_UI->>Stock_App: 10a. 論理在庫を減算
        Warehouse_UI->>Task_App: 11a. 出庫タスク完了に更新
    else 製品入庫
        Warehouse_UI->>Physical_DB: 9b. 実在庫を追加 (棚・LOT単位)
        Warehouse_UI->>Stock_App: 10b. 論理在庫を加算
        Warehouse_UI->>Task_App: 11b. 入庫タスク完了に更新
    end

    %% === 5. 製造完了・バーコード運用 ===
    Warehouse_UI->>Production_App: 12. 製造完了報告 (完了数, LOT番号, ExpiredDate)
    note right of Production_App: LOT番号・期限日は手動で入力（自動生成なし）

    note over Warehouse_UI: バーコードはKintoneで生成しない。<br>外部印刷システムまたはテンプレートで現場担当者が自ら印刷して貼付。

    %% === 6. 在庫データの照合・報告 ===
    Stock_App->>Master_Admin: 13. 在庫一覧／棚卸レポートを表示
    Physical_DB->>Master_Admin: 14. 棚・LOT単位の実在庫詳細を照会
    note over Stock_App, Physical_DB: 論理在庫と実在庫の整合性を維持するバッチが定期実行される
```

### 🎯 Mục tiêu

Xây dựng luồng quản lý toàn bộ quy trình sản phẩm – vật tư – BOM – sản xuất – nhập xuất kho

trên nền Kintone, tích hợp màn hình quét barcode ReactJS, đảm bảo nhất quán giữa:

- **論理在庫 (Logic Stock)** – tồn kho hệ thống
- **実在庫 (Physical Stock)** – tồn kho thực tế theo từng **kệ (Location)** và **LOT番号**
- Không sử dụng WMS bên ngoài, barcode do **nhân viên tự in bên ngoài hệ thống**

---

### 🧩 Thành phần hệ thống tham gia

| Thành phần | Vai trò |
| --- | --- |
| 👨‍💻 Master_Admin | Người vận hành, quản lý CRUD master & ghi nhận sản xuất |
| 🗂️ Product_App | App quản lý sản phẩm (SKU, tên, quy cách, đơn vị, JAN code) |
| 📉 Material_App | App quản lý vật tư/nguyên liệu |
| 📦 BOM_App | App quản lý định mức vật tư cho từng sản phẩm |
| 🧾 Production_App | App ghi nhận sản xuất (số lượng, LOT, hạn sử dụng, người phụ trách) |
| 📋 Task_App | App quản lý task nhập kho / xuất kho sinh tự động |
| 📈 Stock_App | App quản lý tồn kho logic (論理在庫) |
| 🏭 Physical_DB | Database quản lý tồn kho vật lý (実在庫) theo kệ và LOT |
| 🖥️ Ruby_Server | Middleware xử lý nghiệp vụ (tạo task, cập nhật kho, điều phối dữ liệu) |
| 📱 Warehouse_UI | Màn hình ReactJS để nhân viên kho quét barcode, nhập LOT, hoàn tất task |

---

### ⚙️ Quy trình tổng thể

### 1️⃣ Tạo Master sản phẩm – vật tư – BOM

- Người vận hành đăng ký:
    - **Product_App**: SKU, tên, đơn vị, quy cách, JAN code
    - **Material_App**: mã vật tư, loại (原料/OEM), điểm đặt hàng
    - **BOM_App**: liên kết sản phẩm + vật tư + định mức tiêu hao
- Kiểm tra tính toàn vẹn dữ liệu (SKU và Material phải tồn tại, active)

---

### 2️⃣ Ghi nhận sản xuất (製造開始・完了)

- Khi bắt đầu hoặc hoàn tất sản xuất, tạo record trong **Production_App**
- Nhập các thông tin:
    - Sản phẩm (SKU), số lượng, người phụ trách, ngày sản xuất
    - Khi hoàn tất: nhập **LOT番号** và **期限日 (Expired Date)**
- LOT và Expired Date do nhân viên nhập thủ công (không tự động gán)
- Khi ghi nhận sản xuất, hệ thống (Ruby_Server) sẽ kích hoạt logic tạo Task

---

### 3️⃣ Sinh Task nhập/xuất kho tự động

- Khi bắt đầu sản xuất (開始):
    - Ruby_Server đọc BOM và tạo **Task 出庫 (xuất kho)** cho vật tư tương ứng
- Khi hoàn tất sản xuất (完了):
    - Ruby_Server tạo **Task 入庫 (nhập kho)** cho thành phẩm vừa sản xuất
- Mỗi Task bao gồm:
    - Loại tác vụ (入庫/出庫), SKU, số lượng, người phụ trách, trạng thái ban đầu = `未開始`

---

### 4️⃣ Thực hiện Task tại kho (Warehouse_UI)

- Nhân viên kho mở **Warehouse_UI (ReactJS)** để thao tác với các Task.
- Các bước thực hiện:
    1. Chọn Task cần xử lý
    2. Quét **Barcode sản phẩm hoặc vật tư**
    3. Quét **Mã kệ (棚コード)**
    4. Nhập hoặc quét **LOT番号**
    5. Nhập **số lượng thực tế** và nhấn **完了**
- Khi hoàn tất:
    - Nếu là **出庫 (vật tư)**:
        - Giảm tồn kho vật lý trong **Physical_DB** (棚・LOT単位)
        - Giảm tồn kho logic trong **Stock_App**
    - Nếu là **入庫 (thành phẩm)**:
        - Tăng tồn kho vật lý trong **Physical_DB**
        - Tăng tồn kho logic trong **Stock_App**
    - Trạng thái Task cập nhật → `完了済み`

---

### 5️⃣ Quản lý LOT và Barcode

- **LOT番号** và **期限日** do nhân viên tự nhập khi hoàn tất sản xuất.
- **Barcode không in từ Kintone**:
    - Nhân viên tự in barcode từ template bên ngoài (Excel hoặc máy in chuyên dụng)
    - Dán thủ công lên sản phẩm trước khi nhập kho
- Hệ thống chỉ dùng barcode để quét nhận diện khi thao tác task

---

### 6️⃣ Quản lý tồn kho & báo cáo

- **Stock_App (論理在庫)**: phản ánh tổng tồn hệ thống theo SKU.
- **Physical_DB (実在庫)**: ghi nhận tồn thực tế theo SKU + Location + LOT.
- Ruby_Server thực hiện batch đối chiếu định kỳ giữa Logic/Physical stock:
    - Báo cáo chênh lệch (SKU, LOT, chênh lệch tồn, %)
- Có thể lọc tồn kho theo:
    - SKU
    - LOT番号
    - 棚コード (mã kệ)

---

### 🧮 Logic tồn kho

| Loại tồn kho | Khi tăng | Khi giảm | Nguồn thay đổi |
| --- | --- | --- | --- |
| **論理在庫 (Logic Stock)** | Nhập thành phẩm | Xuất vật tư hoặc xuất hàng bán | Stock_App |
| **実在庫 (Physical Stock)** | Khi nhân viên xác nhận nhập kho thực tế | Khi nhân viên xác nhận xuất kho thực tế | Physical_DB |

---

### ⚠️ Ngoại lệ & Xử lý đặc biệt

| Trường hợp | Hành động |
| --- | --- |
| Nhập sai LOT hoặc mã kệ | Hiển thị cảnh báo, yêu cầu nhập lại |
| Trùng LOT trên cùng kệ | Cảnh báo nhưng cho phép nếu cùng sản phẩm |
| Hủy sản xuất | Ruby_Server hủy Task và rollback tồn logic |
| Sai lệch kho | Có thể chỉnh thủ công trên Physical_DB (ghi log) |
| Barcode lỗi hoặc mất | Nhân viên tự in lại từ hệ thống ngoài |

---

### ✅ Kết quả mong đợi

- Toàn bộ quy trình sản xuất và kho được quản lý tập trung trong Kintone
- Không phụ thuộc WMS ngoài, phù hợp môi trường vận hành thực tế
- Tồn kho logic và vật lý luôn khớp, có thể audit đến từng **LOT & 棚**
- Nhân viên kho thao tác nhanh bằng **quét barcode ReactJS UI**
- Dữ liệu traceable đầy đủ theo SKU, LOT và thời gian

---