Kintone Apps — Danh mục và vai trò
Products App（商品マスタ）: Master sản phẩm, route_rule, giá list, thuộc tính SKU.
Materials App（資材マスタ）: Master vật tư/nguyên liệu/bao bì, đơn vị tính, nhà cung cấp chuẩn.
BOM App（部材表）: Cấu thành sản phẩm (Materials/資材 + 材料), định mức tiêu hao.
Materials Inventory App（資材在庫管理）: Tồn kho vật tư, theo dõi nhập/xuất theo LOT, Location.
Partners App（代理店・仕入先・配当者・取次店管理）: Quản lý Agency/Supplier/Affiliate/Dividend partner; discount_rate, thông tin ngân hàng.
Stock App（在庫管理）: 論理在庫 theo SKU, dùng cho giữ hàng, báo cáo, cảnh báo.
Stock Movements App（入出庫履歴）: Lịch sử nhập/xuất/di chuyển/điều chỉnh (reason, Shelf, LOT, qty).
Tasks App（倉庫作業タスクアプリ）: Tác vụ xuất/nhập (Picking/Putaway/Movement), liên kết Scan UI.
Locations App（ロケーション管理アプリ）: Quản lý kệ/khu vực/kho (Shelf, Zone, Warehouse).
Physical Stock App（倉庫実在庫）: Tồn kho vật lý theo Warehouse × Shelf × LOT, đồng bộ với Movements/Tasks.
Orders App（受注管理）: Đơn bán B2C/B2B, trạng thái (partial shipped/backorder), tracking, liên kết Invoice.
Production App（製造実績アプリ）: Ghi nhận sản xuất (start/complete), LOT/期限日, link BOM.
Purchase Orders App（発注アプリ）: Phát hành PO, theo dõi納期, portal NCC/EDI.
Payouts App（支払管理アプリ）: Yêu cầu/chi trả (AP), theo dõi kết quả GMO, phí, chứng từ.
Rewards App（配当・報酬管理）: Ledger rebate/commission (pending/approved/paid), tổng hợp kỳ.
Rewards Rule App（報酬ルール設定アプリ）: Thiết lập rule rebate/affiliate (unit/rate, phạm vi, hiệu lực).
Invoices App（請求管理）: Hóa đơn/VA (AR), PayStatus, số tiền nhận, kết nối GMO transactions.
Agency Portal（代理店ポータル）: Guest space cho đại lý đặt hàng, xem trạng thái, tải chứng từ.
Supplier Portal（仕入先ポータル）: Guest space cho NCC xác nhận PO, báo ngày giao, phân lô (分納).
Staffs App（スタッフ管理）: Quản lý người dùng nội bộ, quyền/nhóm, liên kết audit.
Error Log App（例外記録アプリ）: Nhật ký ngoại lệ (webhook/API/EDI), DLQ, retry, trạng thái xử lý.
