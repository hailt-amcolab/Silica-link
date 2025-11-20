# RISK-026: Khách Hàng Non-Tech → Documents Không Được Confirm Chính Xác → Nghiệm Thu Không Đúng Ý

**Date:** 2025-11-20  
**Project:** Silica-link  
**Risk ID:** RISK-026  
**Category:** Product/Requirement Risk + Communication Risk

---

## Phân tích Risk theo Framework SMART RISK

| **Thành phần** | **Nội dung** |
|----------------|--------------|
| **Situation (Hiện tượng)** | CREDIE Management là người không biết tech. Documents về dự án và solution (PRD, Architecture, Technical Spec) được gửi nhưng khách không hiểu hoặc hiểu sai |
| **Risk Event (Điều có thể xảy ra)** | Khách confirm documents một cách qua loa hoặc không chính xác → khi nghiệm thu sản phẩm, khách phát hiện không đúng ý → rework lớn → timeline delay → budget overrun |
| **Impact (Ảnh hưởng)** | **Scope + Schedule + Quality:** Rework lớn, timeline delay, budget overrun, customer dissatisfaction, project reputation damage |
| **Probability** | High (3) - Khách non-tech confirm documents không chính xác là rất phổ biến trong software projects |
| **Impact** | High (3) - Rework lớn có thể delay project đáng kể |
| **Risk Score** | **9** → **HIGH** |
| **Owner** | PM / BA |
| **Mitigation Strategy** | **Avoid + Reduce + Transfer** |

---

## Phân loại Risk

**Nhóm chính:**
- **Product/Requirement Risk:** Requirement mơ hồ, PO/client không phản hồi kịp
- **Communication Risk:** Feedback delay, khác ngôn ngữ/kỹ năng

**Known-Unknown:** ✅ (Biết khách non-tech, chưa biết cách confirm hiệu quả)

---

## Mitigation Plan Chi Tiết

### Strategy 1: AVOID - Thiết Kế Quy Trình Confirm Phù Hợp Ngay Từ Đầu

**Mục tiêu:** Loại bỏ nguyên nhân gốc - không gửi tech documents cho khách non-tech confirm trực tiếp

#### Action 1.1: Tạo "Business-Friendly" Documents
**Action:** Tạo 2 phiên bản documents:
- **Technical Version:** Cho team dev (PRD, Architecture, Technical Spec)
- **Business Version:** Cho CREDIE Management (Business Summary, User Stories, Visual Mockups, Demo Videos)

**Owner:** BA / PM  
**Timing:** Từ Epic 1 - tạo business version cho mỗi document quan trọng  
**Expected Result:** Khách chỉ cần confirm business version - dễ hiểu, không cần tech knowledge  
**Deadline:** Tạo business version ngay sau technical version

#### Action 1.2: Baseline Confirmation Process
**Action:** Thiết lập quy trình confirm rõ ràng:
1. **Kickoff Meeting:** Giải thích documents bằng ngôn ngữ business (không dùng tech jargon)
2. **Visual Confirmation:** Sử dụng wireframes, mockups, flowcharts thay vì text documents
3. **Demo/Prototype:** Tạo interactive prototype hoặc demo video để khách "thấy" thay vì "đọc"
4. **Confirmation Form:** Tạo form đơn giản với câu hỏi Yes/No thay vì open-ended questions

**Owner:** PM  
**Timing:** Ngay từ kickoff meeting  
**Expected Result:** Khách hiểu rõ và confirm chính xác  
**Deadline:** Setup trong Epic 1

---

### Strategy 2: REDUCE - Giảm Thiểu Rủi Ro Confirm Sai

#### Action 2.1: Visual Communication Tools
**Action:** Sử dụng visual tools thay vì text documents:

**a) User Journey Maps (Visual)**
- Vẽ flow bằng hình ảnh: "Nhân viên đơn hàng nhận order → check tồn kho → confirm order"
- Khách chỉ cần xem và confirm: "Đúng, đây là quy trình tôi muốn"

**b) Wireframes/Mockups**
- Tạo wireframes cho BtoB Portal, Dashboard
- Khách xem và confirm: "Đúng, tôi muốn giao diện như này"

**c) Demo Videos**
- Quay video demo workflow: "Đây là cách hệ thống sẽ hoạt động"
- Khách xem và confirm: "Đúng, đây là những gì tôi cần"

**d) Interactive Prototypes**
- Tạo clickable prototype (Figma, InVision)
- Khách có thể click và test → confirm dựa trên experience thực tế

**Owner:** BA / UX Designer  
**Timing:** Trước mỗi epic start  
**Expected Result:** Khách confirm dựa trên visual → chính xác hơn  
**Deadline:** Tạo visual materials trước khi gửi documents

#### Action 2.2: Structured Confirmation Questions
**Action:** Thay vì hỏi "Bạn có đồng ý với document này không?", hỏi cụ thể:

**Format: Confirmation Checklist**
```
✅ CONFIRMATION CHECKLIST - Epic 2: Order Processing Automation

Câu hỏi 1: Khi có order từ Shopify, bạn muốn hệ thống tự động tạo order trong Kintone không?
☐ Có, tự động (không cần nhập manual)
☐ Không, tôi muốn review trước

Câu hỏi 2: Nếu hết hàng, bạn muốn hệ thống làm gì?
☐ Từ chối order ngay (BtoC)
☐ Tạo backorder (BtoB)
☐ Gửi email cho tôi để quyết định

Câu hỏi 3: Bạn muốn xem orders mới trong dashboard realtime không?
☐ Có, realtime (< 1 giây)
☐ Không, refresh mỗi 5 phút là đủ
```

**Owner:** PM  
**Timing:** Gửi cùng với documents  
**Expected Result:** Khách confirm cụ thể từng điểm → không có hiểu lầm  
**Deadline:** Tạo checklist cho mỗi epic

#### Action 2.3: "Show, Don't Tell" Approach
**Action:** Thay vì giải thích bằng text, show bằng examples:

**Ví dụ thay vì viết:**
> "Hệ thống sẽ tự động sync orders từ Shopify vào Kintone với idempotency mechanism để đảm bảo zero duplicate orders"

**Show bằng:**
> "Video demo: Khi có 1 order từ Shopify → hệ thống tự động tạo trong Kintone. Nếu Shopify gửi lại order đó → hệ thống không tạo duplicate. Xem video demo tại: [link]"

**Owner:** BA  
**Timing:** Trong mỗi document gửi khách  
**Expected Result:** Khách hiểu rõ hơn → confirm chính xác hơn  
**Deadline:** Tạo demo videos cho mỗi epic

---

### Strategy 3: TRANSFER - Chuyển Trách Nhiệm Confirm Cho Người Hiểu Tech

#### Action 3.1: Technical Liaison
**Action:** Yêu cầu CREDIE assign một technical liaison (có thể là operations team lead hoặc IT person) để:
- Review technical documents
- Giải thích cho CREDIE Management bằng ngôn ngữ dễ hiểu
- Confirm documents thay mặt CREDIE Management

**Owner:** PM  
**Timing:** Ngay từ kickoff  
**Expected Result:** Documents được confirm bởi người hiểu tech → chính xác hơn  
**Deadline:** Identify technical liaison trong Epic 1

#### Action 3.2: MediaBirth as Intermediary
**Action:** Sử dụng MediaBirth (Project Sponsor) như intermediary:
- MediaBirth review technical documents
- MediaBirth giải thích cho CREDIE bằng business language
- MediaBirth confirm documents thay mặt CREDIE

**Owner:** PM  
**Timing:** Từ Epic 1  
**Expected Result:** Documents được confirm qua intermediary hiểu cả tech và business  
**Deadline:** Setup process trong Epic 1

---

### Strategy 4: REDUCE - Tăng Cường Communication và Validation

#### Action 4.1: Regular Demo Sessions
**Action:** Thay vì chỉ gửi documents, tổ chức demo sessions định kỳ:
- **Weekly Demo:** Show progress bằng working prototype
- **Sprint Review:** Demo features đã implement
- **Early Feedback:** Khách feedback sớm → không đợi đến nghiệm thu

**Owner:** PM / Tech Lead  
**Timing:** Mỗi sprint (2 tuần)  
**Expected Result:** Khách thấy progress thực tế → feedback sớm → không có surprise ở nghiệm thu  
**Deadline:** Setup từ Epic 1

#### Action 4.2: "Confirmation by Usage" Approach
**Action:** Thay vì confirm documents, cho khách dùng thử sớm:
- **Phased Rollout:** Deploy features từng phần
- **Beta Testing:** Cho operations team dùng thử trước
- **Feedback Loop:** Collect feedback và adjust

**Owner:** PM  
**Timing:** Từ Epic 2 (sau khi có MVP)  
**Expected Result:** Khách confirm dựa trên usage thực tế → chính xác hơn documents  
**Deadline:** Plan phased rollout trong Epic 1

---

## Ý Tưởng Cụ Thể: "Visual Confirmation System"

### Concept: 3-Layer Confirmation System

**Layer 1: Business Summary (1-2 trang)**
- Tóm tắt bằng ngôn ngữ business
- Visual flowcharts
- Key benefits
- **Confirmation:** "Bạn có đồng ý với mục tiêu này không?" (Yes/No)

**Layer 2: User Stories với Visuals**
- Mỗi user story có:
  - Wireframe/Mockup
  - Demo video (30-60 giây)
  - Simple checklist
- **Confirmation:** "Bạn có muốn feature này không?" (Yes/No/Need Clarification)

**Layer 3: Interactive Prototype**
- Clickable prototype
- Khách có thể test workflow
- **Confirmation:** "Bạn có hài lòng với workflow này không?" (Yes/No/Need Changes)

### Implementation Plan

#### Phase 1: Setup Tools (Epic 1)
- Setup Figma/InVision cho wireframes
- Setup screen recording tool cho demo videos
- Create template cho business summaries

#### Phase 2: Create Visual Materials (Trước mỗi Epic)
- Business summary với visuals
- User story wireframes
- Demo videos
- Interactive prototypes (nếu cần)

#### Phase 3: Confirmation Process
- Gửi business summary trước
- Meeting để giải thích
- Gửi wireframes và demo videos
- Khách confirm bằng checklist
- Follow-up meeting nếu cần clarification

---

## Specific Solutions cho Silica-link

### Solution 1: "Epic Confirmation Package"

Cho mỗi Epic, tạo package gồm:

1. **Business Summary (1 trang)**
   - Mục tiêu epic bằng ngôn ngữ business
   - Lợi ích cho CREDIE
   - Visual flowchart

2. **User Story Cards (Visual)**
   - Mỗi story có:
     - Wireframe/Mockup
     - Demo video link
     - Simple checklist

3. **Confirmation Form (Online)**
   - Câu hỏi Yes/No/Need Clarification
   - Không cần viết text dài
   - Có thể comment nếu cần

**Example cho Epic 2:**
```
📦 EPIC 2 CONFIRMATION PACKAGE

1. Business Summary:
   "Epic 2 sẽ tự động hóa việc nhận orders từ Shopify và tạo orders trong Kintone.
   Lợi ích: Nhân viên đơn hàng không cần nhập manual nữa, tiết kiệm 80% thời gian.
   [Flowchart: Shopify → System → Kintone]"

2. User Story Cards:
   Story 2.1: Webhook Reception
   - Wireframe: [link]
   - Demo Video: [link - 30 giây]
   - Checklist: ☐ Đúng, tôi muốn tự động nhận orders
                 ☐ Cần clarification

3. Confirmation Form:
   [Google Form với Yes/No questions]
```

### Solution 2: "Weekly Visual Updates"

Thay vì gửi technical status reports, gửi:

1. **Progress Dashboard (Visual)**
   - Pie chart: % completion
   - Timeline với milestones
   - Screenshots của features đã làm

2. **Demo Video (2-3 phút)**
   - Show features đã implement
   - Khách xem và feedback

3. **Simple Questions**
   - "Bạn có hài lòng với progress này không?"
   - "Có gì cần thay đổi không?"

### Solution 3: "Interactive Prototype Testing"

Trước khi implement, cho khách test prototype:

1. **Figma/InVision Prototype**
   - Clickable, interactive
   - Khách có thể test workflow

2. **Guided Testing Session**
   - PM hướng dẫn khách test
   - Khách feedback real-time

3. **Confirmation**
   - "Bạn có hài lòng với prototype này không?"
   - "Có gì cần thay đổi trước khi implement?"

### Solution 4: "Business Language Translation"

Tạo "Translation Layer" cho mỗi technical document:

**Technical Document → Business Summary**

| **Technical Term** | **Business Translation** |
|---------------------|--------------------------|
| "Webhook pipeline" | "Hệ thống tự động nhận orders từ Shopify" |
| "Idempotency mechanism" | "Đảm bảo không tạo duplicate orders" |
| "Event-driven architecture" | "Hệ thống tự động cập nhật realtime" |
| "API integration" | "Kết nối với hệ thống khác" |
| "Database fallback" | "Hệ thống vẫn hoạt động khi có lỗi" |

**Owner:** BA  
**Timing:** Từ Epic 1  
**Expected Result:** Khách hiểu documents → confirm chính xác hơn

---

## Mitigation Plan Summary

| **Strategy** | **Action** | **Owner** | **Timing** | **Expected Result** |
|--------------|------------|-----------|------------|---------------------|
| **Avoid** | Tạo Business-Friendly Documents | BA/PM | Epic 1 | Khách chỉ confirm business version |
| **Avoid** | Baseline Confirmation Process | PM | Epic 1 | Quy trình confirm rõ ràng từ đầu |
| **Reduce** | Visual Communication Tools | BA/UX | Trước mỗi epic | Khách confirm dựa trên visual |
| **Reduce** | Structured Confirmation Questions | PM | Trước mỗi epic | Khách confirm cụ thể từng điểm |
| **Reduce** | "Show, Don't Tell" Approach | BA | Trong documents | Khách hiểu rõ hơn |
| **Transfer** | Technical Liaison | PM | Epic 1 | Documents được confirm bởi người hiểu tech |
| **Transfer** | MediaBirth as Intermediary | PM | Epic 1 | Confirm qua intermediary |
| **Reduce** | Regular Demo Sessions | PM/Tech Lead | Mỗi sprint | Khách thấy progress thực tế |
| **Reduce** | "Confirmation by Usage" | PM | Từ Epic 2 | Khách confirm dựa trên usage |

---

## Trigger Conditions

**Khi nào cần hành động:**
- Khách không confirm documents trong 5 ngày → Escalate
- Khách confirm nhưng feedback mơ hồ → Request clarification meeting
- Khách confirm nhưng sau đó thay đổi ý → Review confirmation process
- Nghiệm thu có nhiều issues → Review confirmation process

---

## Success Metrics

**Đo lường hiệu quả mitigation:**
- Confirmation accuracy: 90%+ (không có rework do confirm sai)
- Confirmation time: < 3 ngày (không delay)
- Rework rate: < 5% (do confirm sai)
- Customer satisfaction: 90%+ (hài lòng với process)

---

## Next Steps

1. **Immediate (Epic 1):**
   - Tạo business summary template
   - Setup visual tools (Figma, screen recording)
   - Identify technical liaison
   - Create confirmation process

2. **Before Epic 2:**
   - Tạo Epic 2 Confirmation Package
   - Create wireframes và demo videos
   - Schedule confirmation meeting

3. **Ongoing:**
   - Weekly visual updates
   - Regular demo sessions
   - Collect feedback và improve process

---

**Document Status:** Risk Analysis Complete  
**Owner:** PM (Potato)  
**Next Review:** 2025-11-27

