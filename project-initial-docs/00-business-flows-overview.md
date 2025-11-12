# SilicaLink — Luồng nghiệp vụ

> Phạm vi dựa trên estimate “SILICA LINK 受発注管理システム開発_20250827”. Các flow mô tả mục tiêu nghiệp vụ, vai trò, tác nhân kích hoạt, hệ thống chính, đầu vào và đầu ra. Chi tiết kỹ thuật được giản lược có chủ đích.

---

## Vai trò và Hệ thống

- Vai trò: Sales (Kinh doanh), Purchasing (Mua hàng), Kho (Gifu/Amagasaki), Kế toán, Đại lý/Agent (代理店), Nhà cung cấp, Đối tác giới thiệu (取次店), Ban giám đốc, Khách hàng
- Hệ thống lõi: Kintone (các app theo estimate), AWS, Ship&Co, API Ngân hàng GMO, WMS (ZERO/Lite), EC (Shopify本店, FIS), dịch vụ rút gọn link, Email/Slack

---

## Danh sách các System Flow (tóm tắt mục tiêu)

- Flow 1: EC注文の自動取込・在庫引当と出荷処理 (B2C)
  - Mục tiêu: Đồng bộ đơn EC, giữ hàng (論理在庫), xuất kho (実在庫), phát hành vận đơn, đồng bộ tracking.
- Flow 2: B2B 全体プロセス: 代理店/注文 出荷/配送
  - Mục tiêu: Quy trình B2B có phân lô giao (分割出荷), VA, rank/chiết khấu đại lý.
- Flow 3: GoAffPro連携によるアフィリエイト報酬処理
  - Mục tiêu: Đồng bộ rank, tổng hợp commission, duyệt & GMO payout tháng.
- Flow 4: 製品・資材・製造・在庫管理フロー
  - Mục tiêu: Master sản phẩm/vật tư/BOM, ghi nhận sản xuất, nhập-xuất theo LOT/kệ.
- Flow 5: 資材購買サイクル
  - Mục tiêu: Giám sát phát hành PO, 2 cấp phê duyệt, GMO thanh toán trước, nhập kho phân lô.
- Flow 6: サンプル出荷・返品・棚移動・在庫調整
  - Mục tiêu: Xuất mẫu, hoàn hàng, chuyển kệ, điều chỉnh kho qua Scan UI.
- Flow 7: 支払いフロー（AR/AP統合）
  - Mục tiêu: AR qua VA/Invoice, AP qua Payment Request, CEO duyệt, GMO batch.
- Flow 8: 倉庫作業のフロー（出庫・入庫）
  - Mục tiêu: Chuẩn hóa TaskApp trung tâm, Scan UI thực thi, đồng bộ Logic/Physical.
- Flow 9: 全体／一部キャンセル・返品
  - Mục tiêu: Hợp nhất xử lý hủy/hoàn/điều chỉnh; đồng bộ tồn kho và hoa hồng.

---

## Ghi chú phạm vi (Scope assumptions)

- Giai đoạn MVP ưu tiên tính đúng, minh bạch và khả năng audit; automation Ruby tối thiểu khi có thể thực hiện thủ công trong Kintone.
- Tồn kho tách bạch: 論理在庫 (Logic) dùng cho đặt/giữ hàng và báo cáo; 実在庫 (Physical) phản ánh quét thực tế theo Shelf × LOT.
- Phê duyệt 2 cấp áp dụng cho các luồng chi (AP) có rủi ro dòng tiền.

---

## Tài liệu chi tiết

Xem các file Flow 1–9 trong thư mục `documents/` để biết quy trình, cấu phần, logic tồn kho, sequence diagram và ngoại lệ xử lý.

