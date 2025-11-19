# Brainstorming Session Results

**Session Date:** 2025-11-19
**Facilitator:** Business Analyst Mary
**Participant:** Potato

## Session Start

### Kế hoạch khởi động

1. Nạp toàn bộ bối cảnh dự án Silica-link (Project Why, User Hearing, Technical Stack) để làm rõ chuỗi giá trị đặt hàng → giao hàng → nhập hàng → rebate.
2. Xác nhận 5 mục tiêu phiên: vấn đề người dùng, tính năng mới, hướng kỹ thuật cho một tính năng, rủi ro/thách thức kỹ thuật, chỉ số thành công.
3. Chọn phương pháp *Kỹ thuật do bạn chọn* và quyết định đi sâu hai cụm kỹ thuật STRUCTURED + DEEP (4 kỹ thuật mỗi cụm) rồi kết hợp Mind Mapping cho nhánh Order Management.
4. Khóa cách giao tiếp tiếng Việt, tuân thủ nguyên tắc “không ước lượng thời gian”, chuẩn bị sẵn file kết quả `docs/brainstorming-session-results-2025-11-19.md` để cập nhật sau mỗi checkpoint.

## Executive Summary

**Topic:** Dự án Silica-link - Hệ thống quản lý đặt hàng, giao hàng, nhập hàng và quản lý hoa hồng/cổ tức cho CREDIE

**Session Goals:** Khám phá 5 khía cạnh chính:
1. Vấn đề người dùng cần giải quyết
2. Tính năng mới cần phát triển
3. Cách tiếp cận kỹ thuật cho một tính năng cụ thể
4. Rủi ro và thách thức kỹ thuật
5. Chỉ số thành công

**Techniques Used:** SCAMPER • Six Thinking Hats • Mind Mapping (Order Management focus)

**Total Ideas Generated:** 27

### Key Themes Identified:

{{key_themes}}

## Technique Sessions

### SCAMPER Method
- **Substitute:** Hệ thống rời rạc → nền tảng thống nhất; nhập liệu thủ công → webhook/API tự động.
- **Combine:** Ghép Order+Inventory để check tồn tự động; ghép Sales+Rebate để auto tính hoa hồng.
- **Adapt:** Mang khả năng ERP sản xuất (BOM, WIP, đa kho), affiliate tracking chuẩn e-commerce, và mô hình fulfillment kiểu Amazon FBA cho FIS.
- **Modify:** Cho phép partial shipment/backorder tự động; mở rộng routing đa kho A/B.
- **Put to other uses:** Dùng hệ thống kho để quản lý nguyên liệu OEM theo “kho ảo” từng đối tác.
- **Eliminate:** Bỏ nhập liệu thủ công và các bước trung gian không cần thiết.
- **Reverse:** Chưa phát hiện quy trình cần đảo chiều lúc này.

### Six Thinking Hats
- **Mũ trắng:** Liệt kê dữ liệu lõi (2 shop Shopify, 7-8 đại lý, kho A hiện hữu, kho B sắp mở, stack: Shopify/Kintone/Ship&Co/GMO/Yoom/Rails/AWS).
- **Mũ đỏ:** Cảm xúc vừa háo hức với tự động hóa, vừa lo rủi ro vận hành.
- **Mũ vàng:** Lợi thế chính là dashboard realtime và quản trị đa kho minh bạch.
- **Mũ đen:** Rủi ro lớn là đại lý/nhân sự vận hành chưa quen hệ thống mới.
- **Mũ xanh lá:** Khuyến khích tiếp tục các kỹ thuật sáng tạo/ghi chú ý tưởng mở rộng.
- **Mũ xanh dương:** Chốt chiến lược đi tuần tự theo từng nhóm kỹ thuật, linh hoạt chuyển nhánh khi cần.

### Mind Mapping – Nhánh Order Management
- Phân rã 4 luồng chính: BtoC, BtoB, FIS Fulfillment, Affiliate.
- **BtoC**: Realtime webhook → validate/stock check → no backorder → auto fulfillment (picking task, Ship&Co API, tracking sync).
- **BtoB**: Portal riêng (lịch sử, reorder, SKU search), discount/markup theo rank, mọi order cần review + approval + thanh toán trước.
- **FIS**: Đồng bộ Shopify riêng, tính phí fulfillment (shipping + warehouse), hiển thị trạng thái cho FIS.
- **Affiliate**: Link tracking, bảng điều khiển realtime, auto payout.

## Idea Categorization

### Immediate Opportunities

_Ideas ready to implement now_

1. Thay thế nhập liệu thủ công bằng webhook/API (Shopify → Yoom → Rails → Kintone) với retry & audit log.
2. Tự động check tồn kho BtoC trước khi cho phép checkout, hiển thị trạng thái out-of-stock realtime.
3. Sinh task picking + gọi Ship&Co API ngay khi order BtoC được thanh toán, auto đồng bộ tracking về Shopify.
4. Áp dụng partial shipment + backorder tự động cho BtoB, gắn với trạng thái `partial_shipped` và `backorder_pending`.
5. Bắt buộc thanh toán trước và workflow duyệt với Proforma Invoice + xác nhận từ GMO Bank API trước khi ship.
6. Gộp dữ liệu Sales + Rebate để auto tính hoa hồng, lock theo quý và show dashboard realtime.
7. Thiết lập portal BtoB hiển thị lịch sử, reorder, tra SKU và trạng thái thanh toán/shipment.

### Future Innovations

_Ideas requiring development/research_

1. Nền tảng thống nhất tích hợp Shopify, Kintone, Ship&Co, GMO, Yoom, Rails, AWS với routing đa kho A/B.
2. ERP-style BOM + WIP tracking cho sản xuất nước, tự trừ nguyên liệu theo batch và trigger sản xuất khi có backorder.
3. Quản lý kho ảo cho OEM/đại lý: mỗi đối tác có “slot” tồn riêng, tự cảnh báo khi gần hết.
4. Affiliate dashboard realtime (link performance, commission, conversion) với auto payout và audit.
5. FIS fulfillment giống Amazon FBA: đồng bộ order, tính phí lưu kho + ship, sync tracking hai chiều.
6. Smart routing engine: chọn kho + carrier tối ưu theo SKU, tồn, khoảng cách, SLA.
7. Backorder dashboard + auto triggers tới production/purchasing với SLA và ưu tiên theo đại lý/giá trị.

### Moonshots

_Ambitious, transformative concepts_

1. Dự báo nhu cầu đa kênh (Shopify/Rakuten/Amazon + BtoB) để lên lịch sản xuất tự động và tối ưu tồn kho.
2. Hệ thống “digital twin” chuỗi cung ứng CREDIE: mô phỏng kho, sản xuất, vận chuyển để thử nghiệm rule mới trước khi áp dụng.
3. Open platform cho đối tác (FIS, OEM, affiliate) với API penuh: tự động hóa hoàn toàn luồng order → rebate → thanh toán.

### Insights and Learnings

_Key realizations from the session_

- BtoC và BtoB cần hai cơ chế tồn kho khác nhau: BtoC phải cứng “no backorder” còn BtoB cần partial shipment + payment theo đợt.
- Prepayment + approval là xương sống để bảo vệ dòng tiền, nên gắn chặt với workflow GMO Bank và audit log.
- Việc mở thêm kho (尼崎 → 岐阜) buộc phải chuẩn hóa routing rule, stock reservation và Ship&Co profile ngay từ giai đoạn thiết kế.
- Rebate thực chất là incentive theo sản lượng, vì vậy hợp nhất dữ liệu sales sẽ giảm rất nhiều thao tác kế toán và tăng minh bạch với đại lý.

## Action Planning

### Top 3 Priority Ideas

#### #1 Priority: Chuỗi realtime BtoC (webhook → stock check → auto fulfillment)

- Rationale: Giảm lỗi và tăng tốc xử lý đơn hàng trực tiếp, tạo niềm tin cho end-user.
- Next steps: Hoàn thiện API contract Shopify/Yoom/Rails, triển khai stock check từ Kintone, thử nghiệm auto Ship&Co.
- Resources needed: BE Rails dev, Kintone app admin, Ship&Co API key, QA cho webhook retry.
- Timeline: 3 sprint nội bộ (chuẩn bị, triển khai, hardening).

#### #2 Priority: Partial shipment + prepayment BtoB

- Rationale: Giải quyết điểm đau backorder, cải thiện dòng tiền và minh bạch với đại lý.
- Next steps: Thiết kế workflow approval/payment, xây Backorder App, tích hợp GMO Bank API cho matching.
- Resources needed: BA nghiệp vụ BtoB, Kintone dev, team tài chính, kết nối ngân hàng.
- Timeline: 4 sprint (thiết kế → build → tích hợp → UAT với 1-2 đại lý).

#### #3 Priority: Rebate/affiliate/FIS dashboards hợp nhất

- Rationale: Tự động hóa hạch toán incentive, giảm sai sót thủ công và tăng minh bạch với đối tác.
- Next steps: Chuẩn hóa schema dữ liệu bán hàng, xây dashboard đa đối tượng, thiết kế cơ chế lock theo quý và auto payout.
- Resources needed: Data engineer, BI/dashboard dev, tích hợp GoAffPro hoặc module nội bộ, stakeholder từ finance & partner success.
- Timeline: 3 sprint (data foundation, dashboard, automation).

## Reflection and Follow-up

### What Worked Well

- Khi ép theo kỹ thuật STRUCTURED + DEEP liên tiếp, team khai thác được cả góc nhìn sáng tạo lẫn rủi ro kỹ thuật.
- Mind map tập trung vào Order Management giúp lộ rõ sự khác biệt giữa BtoC - BtoB - FIS - Affiliate.

### Areas for Further Exploration

- Cần đào sâu thêm các nhánh chưa kịp cover: Inventory, Production, Shipping, Payment, Rebate ở mức chi tiết hệ thống.
- Chưa bàn về chỉ số thành công (KPIs) và cách đo lường sau triển khai.

### Recommended Follow-up Techniques

- **Progressive Technique Flow** (bắt đầu với divergent → convergent) cho phần KPIs & success metrics.
- **Five Whys / Assumption Reversal** cho rủi ro mở rộng đa kho và FIS fulfillment.

### Questions That Emerged

- Cách ưu tiên backorder theo đại lý hay theo giá trị đơn hàng?
- Có cần credit limit hoặc cơ chế tín dụng cho đại lý top-tier?
- Quy trình hủy backorder nếu đại lý không thanh toán đúng hạn sẽ ra sao?

### Next Session Planning

- **Suggested topics:** KPIs & success metrics, Inventory/Production deep dive, Rebate automation.
- **Recommended timeframe:** Trong vòng 1-2 tuần để duy trì đà phân tích.
- **Preparation needed:** Thu thập số liệu tồn kho hiện tại, quy trình production chi tiết, mẫu báo cáo rebate/quý.

---

_Session facilitated using the BMAD CIS brainstorming framework_

