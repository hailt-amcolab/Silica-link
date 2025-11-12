# Product Brief: Silica-link

**Date:** 2025-11-12
**Author:** amcolab
**Context:** Enterprise Platform Integration

---

## Executive Summary

Silica-link là một nền tảng tích hợp tự động hóa quy trình Đặt hàng – Giao hàng – Nhập hàng – Quản lý hoa hồng/cổ tức cho CREDIE. Hệ thống kết nối các hệ thống rời rạc (Shopify, Ship&Co, WMS, GMO Bank API) thành một nền tảng thống nhất với Kintone làm trung tâm, giảm 90% thao tác thủ công và tăng độ chính xác lên 99.9%.

---

## Core Vision

### Problem Statement

CREDIE hiện đang vận hành quy trình Đặt hàng – Giao hàng – Nhập hàng – Quản lý hoa hồng/cổ tức trên nhiều hệ thống rời rạc, dẫn đến:

- **Quy trình thủ công tốn thời gian**: EC đơn hàng từ Shopify phải nhập thủ công qua Excel rồi mới đưa vào Ship&Co; Procurement từ Supplier cũng qua Excel thủ công; B2B Agency đặt hàng qua thủ công với CREDIE
- **Dễ sai sót**: Nhập liệu thủ công dẫn đến lỗi, thiếu minh bạch trong quy trình
- **Thiếu tích hợp**: Các hệ thống không kết nối với nhau, không có công cụ tự động hóa
- **Khó theo dõi**: Thiếu dashboard realtime, khó audit trail, khó quản lý tồn kho và thanh toán

### Problem Impact

**Tác động đến hiệu quả hoạt động:**
- Tốn thời gian xử lý đơn hàng: Mỗi đơn EC phải qua nhiều bước thủ công
- Giảm độ chính xác: Lỗi nhập liệu ảnh hưởng đến tồn kho, thanh toán, hoa hồng
- Thiếu minh bạch: Khó theo dõi trạng thái đơn hàng, thanh toán, tồn kho realtime
- Khó scale: Khi số lượng đơn tăng, quy trình thủ công không thể đáp ứng

**Tác động đến người dùng:**
- **Sales**: Phải nhập đơn thủ công, khó theo dõi tồn kho
- **Warehouse**: Phải đặt giao hàng thủ công trên Ship&Co, quét mã thủ công
- **Finance**: Đối soát thanh toán thủ công, dễ sai sót
- **Management**: Thiếu dashboard tổng hợp, khó ra quyết định
- **Agencies**: Phải đặt hàng qua thủ công, không biết tiến độ
- **Suppliers**: Nhận PO qua email, xác nhận thủ công

### Why Existing Solutions Fall Short

Các hệ thống hiện có (Shopify, Ship&Co, WMS, Excel) được mua/triển khai ở các thời điểm khác nhau, không có kế hoạch tích hợp từ đầu. Quy trình được thiết kế cho thao tác thủ công, không có công cụ tự động hóa và API integration.

### Proposed Solution

**Silica-link** là nền tảng tích hợp với Kintone làm Database & Workflow trung tâm, tự động hóa toàn bộ quy trình:

1. **Tự động hóa đơn hàng**: Yoom → Kintone (Webhook + Batch), tự động phân bổ tồn kho, Portal đại lý B2B
2. **Tự động hóa xuất kho**: Scan UI ReactJS, tự động phát hành vận đơn Ship&Co, đồng bộ tồn kho Logic ↔ Physical
3. **Tự động hóa nhập hàng**: Cảnh báo tồn thấp, tự động tạo PO PDF, Portal nhà cung cấp, EDI integration
4. **Tự động hóa thanh toán**: Đối soát GMO tự động (AR), quản lý AR/AP tích hợp, thanh toán trước cho Supplier
5. **Quản lý đại lý & hoa hồng**: Tự động tính rank đại lý, tích hợp GoAffPro, quản lý hoa hồng/cổ tức (RewardsLedger)
6. **Dashboard realtime**: Tracking, audit trail, báo cáo tổng hợp

### Key Differentiators

1. **Tích hợp toàn diện**: Kết nối tất cả hệ thống (EC, WMS, Ship&Co, GMO Bank, Supplier) thành một nền tảng thống nhất
2. **Tự động hóa 90%**: Loại bỏ Excel, manual data entry, duplicate validation
3. **Realtime & minh bạch**: Dashboard realtime, tracking, audit trail đầy đủ
4. **Kiến trúc linh hoạt**: Kintone trung tâm + Ruby on Rails + Redis + Sidekiq, dễ scale và mở rộng
5. **Tối ưu dòng tiền**: Phê duyệt 2 cấp, đối soát tự động, quản lý AR/AP tích hợp

---

## Target Users

### Primary Users

**1. CREDIE Internal Users:**

- **Sales Team**: Nhập đơn, theo dõi tồn kho, quản lý đại lý
- **Warehouse Staff**: Quét mã xuất kho, xử lý đơn hàng, quản lý tồn kho
- **Finance Team**: Đối soát thanh toán, quản lý AR/AP, chi trả hoa hồng
- **Management**: Dashboard tổng hợp, báo cáo, ra quyết định

**2. External Partners:**

- **Agencies (Đại lý B2B)**: Đặt hàng qua Portal, theo dõi đơn hàng, xem hoa hồng
- **Suppliers (Nhà cung cấp)**: Nhận PO, xác nhận giao hàng qua Portal/EDI
- **Affiliate Partners (取次店)**: Tạo link giới thiệu, theo dõi commission, nhận payout

### Secondary Users

- **End Customers (B2C)**: Nhận thông báo đơn hàng, tracking (qua Ship&Co)
- **Directors**: Dashboard cổ tức, báo cáo quý

### User Journey

**Journey 1: EC Order Processing (B2C)**
```
Customer đặt hàng trên Shopify
  → Yoom nhận webhook
  → Silica-link tự động nhập đơn vào Kintone
  → Tự động phân bổ tồn kho
  → Warehouse quét mã xuất kho (Scan UI)
  → Tự động phát hành vận đơn Ship&Co
  → Customer nhận thông báo tracking
  → GMO đối soát thanh toán tự động
```

**Journey 2: B2B Agency Order**
```
Agency đăng nhập Portal
  → Đặt hàng qua Portal Kintone
  → Phê duyệt 2 cấp (nếu cần)
  → Tự động phân bổ tồn kho
  → Warehouse xử lý xuất kho
  → Tự động phát hành vận đơn
  → GMO tạo Virtual Account cho thanh toán
  → Đối soát tự động khi nhận thanh toán
```

**Journey 3: Procurement Cycle**
```
Tồn kho xuống thấp
  → Hệ thống cảnh báo tự động
  → Tự động tạo PO PDF
  → Gửi email cho Supplier
  → Supplier xác nhận qua Portal/EDI
  → Phê duyệt 2 cấp (nếu cần)
  → GMO thanh toán trước
  → Supplier giao hàng
  → Nhập kho phân lô
```

---

## Success Metrics

### Business Objectives

1. **Efficiency**: Giảm 90% thao tác thủ công, giảm 50% thời gian xử lý đơn hàng
2. **Accuracy**: Tăng độ chính xác lên 99.9%, giảm lỗi nhập liệu
3. **Transparency**: Realtime tracking, dashboard, audit trail đầy đủ
4. **Scalability**: Dễ scale, thêm kênh mới, tích hợp dễ dàng
5. **Cost Savings**: Giảm chi phí nhân công, giảm lỗi, tối ưu tồn kho

### Key Performance Indicators

**Operational KPIs:**
- **Tự động hóa rate**: ≥90% đơn hàng được xử lý tự động
- **Processing time**: Giảm từ X giờ → Y giờ (50% reduction)
- **Error rate**: <0.1% lỗi nhập liệu
- **Uptime**: ≥99.5% system availability

**Business KPIs:**
- **Order volume**: Xử lý được X đơn/ngày (tăng Y% so với hiện tại)
- **Inventory accuracy**: 99.9% accuracy giữa Logic Stock và Physical Stock
- **Payment reconciliation**: 100% đối soát tự động trong 24h
- **Commission processing**: Xử lý X commission transactions/tháng

**User Satisfaction:**
- **Sales team**: Giảm thời gian nhập đơn từ X → Y phút
- **Warehouse**: Giảm thời gian xử lý xuất kho từ X → Y phút
- **Finance**: Giảm thời gian đối soát từ X → Y giờ
- **Agencies**: Portal response time <2s, 24/7 availability

---

## MVP Scope

### Core Features

**Phase 1: Foundation (MVP) - 4-6 tuần**

1. **Tự động nhập đơn EC từ Yoom**
   - Webhook + Batch dự phòng
   - Redis + Sidekiq queue system
   - Idempotency handling
   - Race condition handling cho check tồn kho

2. **Tự động phân bổ tồn kho**
   - Check tồn kho realtime
   - Không reserve (chỉ check)
   - Race condition handling

3. **Basic Scan UI cho Warehouse**
   - ReactJS Scan UI (JP only)
   - Quét mã vạch xuất kho
   - Cập nhật tồn kho

4. **Tự động phát hành vận đơn Ship&Co**
   - Ship&Co API integration
   - Retry mechanism
   - Error handling và logging

5. **Đối soát GMO tự động (AR)**
   - GMO API integration
   - Virtual Account management
   - Đối soát tự động
   - Cập nhật PayStatus

**Phase 2: Automation - 6-8 tuần**

6. **Portal đại lý B2B**
   - Kintone-based Portal
   - JP language, mobile-friendly
   - Phân quyền
   - Đặt hàng, theo dõi đơn hàng

7. **Cảnh báo tồn thấp và tạo PO tự động**
   - Reorder point admin setup
   - Daily/after outbound check
   - Tự động tạo PO PDF
   - Email tracking, 2-level approval, reminder

8. **Portal nhà cung cấp**
   - Kintone Guest Space (cần điều tra)
   - Xác nhận PO, giao hàng

9. **Quản lý nhập kho phân lô**
   - Pre-payment cho tất cả B2B
   - GMO API integration
   - Nhập kho theo LOT

10. **Quản lý AR/AP tích hợp**
    - Invoice_App, Payment_Request_App
    - 2-level approval
    - GMO batch payment

**Phase 3: Optimization - 8-10 tuần**

11. **Tự động tính rank đại lý**
    - Starter/Silver/Gold/Platinum
    - Batch calculation
    - Cập nhật rank & hệ số chiết khấu

12. **Tích hợp GoAffPro**
    - Affiliate commission
    - Link generation, cookie tracking
    - Monthly aggregation

13. **Quản lý hoa hồng/cổ tức**
    - RewardsLedger
    - Quarterly calculation
    - GMO payout
    - PDF generation

14. **Dashboard tổng hợp realtime**
    - Tracking, audit trail
    - Báo cáo tổng hợp
    - Analytics

### Out of Scope for MVP

- **AI/ML Features**: Demand forecasting, predictive analytics, auto-routing (Future)
- **Advanced Analytics**: Real-time insights, trend analysis (Future)
- **Mobile Apps**: Native mobile apps (có thể truy cập qua web portal)
- **Voice Commands**: Scan UI với voice input (Future)
- **Blockchain Integration**: Supply chain transparency (Future)
- **Fully Autonomous Warehouse**: Robot picking, AI optimization (Moonshot)

### MVP Success Criteria

1. ✅ **Tự động hóa 90% quy trình nhập đơn EC**: Yoom → Kintone tự động, không cần Excel
2. ✅ **Giảm thời gian xử lý đơn hàng 50%**: Từ X giờ → Y giờ
3. ✅ **Độ chính xác dữ liệu 99.9%**: Giảm lỗi nhập liệu
4. ✅ **Hệ thống hoạt động 24/7**: Uptime ≥99.5%
5. ✅ **Tích hợp thành công với tất cả hệ thống yêu cầu**: Yoom, Ship&Co, WMS, GMO Bank API
6. ✅ **Dashboard realtime**: Tracking, audit trail, báo cáo tổng hợp

### Future Vision

**Phase 4: Advanced Features (Future)**
- AI/ML: Demand forecasting, predictive analytics, auto-routing
- Advanced Analytics: Real-time insights, trend analysis
- Mobile Apps: Warehouse staff, management dashboard
- Voice Commands: Scan UI với voice input
- Blockchain Integration: Supply chain transparency

**Moonshots (Long-term)**
- Fully Autonomous Warehouse: Robot picking, AI optimization
- Predictive Supply Chain: AI predicts demand, auto-reorder
- Global Multi-warehouse Network: Real-time inventory across locations
- Customer Self-Service Portal: End-to-end customer experience
- Ecosystem Integration: Connect với tất cả partners qua API

---

## Market Context

**Industry**: E-commerce Order Management & Supply Chain Integration

**Market Position**: 
- Silica-link là giải pháp tích hợp nội bộ cho CREDIE, không phải sản phẩm thương mại
- Tập trung vào tự động hóa quy trình và tích hợp hệ thống hiện có

**Competitive Landscape**:
- Không có đối thủ trực tiếp vì đây là giải pháp custom cho CREDIE
- Các giải pháp tương tự trên thị trường: SAP, Oracle, nhưng quá phức tạp và đắt cho nhu cầu của CREDIE
- Silica-link tận dụng Kintone (đã có sẵn) + Ruby on Rails (team đã quen) → Chi phí thấp, dễ maintain

---

## Financial Considerations

**Cost Structure:**
- **Development**: Ruby on Rails developer, ReactJS developer (nếu cần)
- **Infrastructure**: AWS Lambda, Redis, S3 storage
- **Third-party APIs**: Yoom, Ship&Co, GMO Bank API (có thể có phí)
- **Maintenance**: Ongoing support và updates

**ROI Expectations:**
- **Cost Savings**: Giảm chi phí nhân công (giảm thời gian xử lý 50%)
- **Error Reduction**: Giảm lỗi nhập liệu → Giảm chi phí sửa lỗi
- **Efficiency**: Tăng năng suất, xử lý được nhiều đơn hơn với cùng số người
- **Scalability**: Dễ scale khi business tăng trưởng

---

## Technical Preferences

**Core Technology Stack:**
- **Backend**: Ruby on Rails (team đã quen)
- **Database & Workflow**: Kintone (đã có sẵn, trung tâm)
- **Queue System**: Redis + Sidekiq
- **Frontend**: ReactJS (cho Scan UI)
- **Cloud**: AWS Lambda (batch processing, monitoring)
- **Storage**: S3 (cho PDF, labels)

**Integration Patterns:**
- **Webhook + Batch**: Yoom integration (webhook primary, batch backup)
- **REST API**: Ship&Co, GMO Bank API
- **EDI**: Supplier integration (nếu cần)
- **Event-driven**: Async processing với Sidekiq

**Architecture Principles:**
- **Kintone-centric**: Kintone là single source of truth
- **Idempotency**: Tất cả operations phải idempotent
- **Retry mechanism**: Tất cả external API calls phải có retry
- **Error handling**: Comprehensive error handling và logging
- **Monitoring**: CloudWatch, Audit Log

**Team Constraints:**
- Team thiếu kinh nghiệm với service bên thứ 3 (ngoài Rails)
- Cần training và documentation chi tiết
- Cần phased approach: MVP trước, optimization sau

---

## Organizational Context

**Stakeholders:**
- **CREDIE Management**: Sponsor, decision maker
- **Sales Team**: End users, feedback providers
- **Warehouse Staff**: End users, training needed
- **Finance Team**: End users, compliance requirements
- **Development Team**: Implementers, cần training về third-party APIs

**Change Management:**
- **Training Plan**: Cần training cho team và end users
- **Documentation**: Cần documentation chi tiết cho maintenance
- **Support**: Cần support plan cho go-live và post-launch

---

## Risks and Assumptions

### Technical Risks

1. **Webhook Reliability**: Yoom webhook có thể không reliable → Mitigation: Batch dự phòng
2. **API Rate Limits**: Ship&Co, GMO API có rate limits → Mitigation: Queue system, retry mechanism
3. **Race Conditions**: Check tồn kho có thể có race condition → Mitigation: Proper locking, idempotency
4. **Team Experience**: Team thiếu kinh nghiệm với third-party APIs → Mitigation: Training, documentation, phased approach

### Business Risks

1. **Process Disruption**: Hệ thống mới có thể gây gián đoạn quy trình → Mitigation: Phased rollout, fallback plan
2. **Data Sync Issues**: Dữ liệu không đồng bộ giữa các hệ thống → Mitigation: Comprehensive testing, monitoring
3. **Over-engineering**: Có thể over-engineer một số tính năng → Mitigation: MVP first, validate với users

### Operational Risks

1. **No Offline Mode**: Phụ thuộc vào API bên thứ 3 → Mitigation: Fallback mechanisms, monitoring
2. **Third-party API Dependency**: Phụ thuộc vào Yoom, Ship&Co, GMO API → Mitigation: Retry, error handling, SLA monitoring
3. **Lack of Monitoring**: Thiếu monitoring có thể không phát hiện lỗi sớm → Mitigation: CloudWatch, Audit Log, alerting

### Key Assumptions

1. **Kintone Availability**: Kintone sẽ luôn available và reliable
2. **API Access**: Có access đầy đủ đến Yoom, Ship&Co, GMO API
3. **Team Capacity**: Team có capacity để implement và maintain
4. **User Adoption**: Users sẽ adopt hệ thống mới
5. **Business Continuity**: Business sẽ continue trong quá trình development

---

## Timeline

**Phase 1: Foundation (MVP) - 4-6 tuần**
- Tự động nhập đơn EC từ Yoom
- Tự động phân bổ tồn kho
- Basic Scan UI
- Tự động phát hành vận đơn Ship&Co
- Đối soát GMO tự động (AR)

**Phase 2: Automation - 6-8 tuần**
- Portal đại lý B2B
- Cảnh báo tồn thấp và tạo PO tự động
- Portal nhà cung cấp
- Quản lý nhập kho phân lô
- Quản lý AR/AP tích hợp

**Phase 3: Optimization - 8-10 tuần**
- Tự động tính rank đại lý
- Tích hợp GoAffPro
- Quản lý hoa hồng/cổ tức
- Dashboard tổng hợp realtime

**Total Timeline**: 18-24 tuần (4.5-6 tháng)

---

## Supporting Materials

**Input Documents Used:**
- Brainstorming Session (2025-11-12): 27+ tính năng chính, key themes, risks, priorities
- Project Why: Vấn đề hiện tại, mục tiêu dự án, kết quả mong đợi
- Project Overview: Kiến trúc tổng thể, hệ thống tích hợp, luồng dữ liệu
- Project Scope: 8 chức năng chính, phạm vi ngoài dự án, tiêu chí thành công
- Business Flows Overview: 9 system flows, vai trò, hệ thống lõi

**Next Steps:**
- PRD workflow sẽ transform product brief này thành detailed product requirements
- Architecture workflow sẽ design technical architecture
- Create Epics & Stories workflow sẽ break down thành implementation stories

---

_This Product Brief captures the vision and requirements for Silica-link._

_It was created through collaborative discovery and reflects the unique needs of this Enterprise Platform Integration project._

_Next: PRD workflow will transform this brief into detailed product requirements._

