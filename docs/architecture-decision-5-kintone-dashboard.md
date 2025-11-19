# Architecture Decision 5: Kintone Dashboard Approach

**Date:** 2025-11-19  
**Status:** Approved  
**Decision Maker:** Potato

---

## Decision

**Dashboard sẽ được tạo hoàn toàn trên Kintone, không sử dụng Rails.**

Dashboard UI, data aggregation, và realtime updates sẽ được implement 100% trên Kintone platform sử dụng Kintone native features và plugins. Rails application chỉ có trách nhiệm sync data lên Kintone apps, không tham gia vào việc serve dashboard data hay render dashboard UI.

---

## Documentation Basis

**Lưu ý:** Decision này KHÔNG được đề cập rõ ràng trong `initial-docs`. Đây là một **architectural decision** được đề xuất dựa trên:

1. **Inference từ tài liệu:**
   - `technical_stack.md`: Kintone là "Nền tảng nghiệp vụ trung tâm (apps)"
   - `kintone_apps.md`: Liệt kê 23 Kintone apps đang được sử dụng
   - `user_hearing_info.md`: Operations team đã quen với Kintone interface

2. **Business context:**
   - Operations team (Nhân viên đơn hàng, Nhân viên kho, etc.) đã làm việc với Kintone hàng ngày
   - Data đã được lưu trong Kintone apps (Orders, Stock, Rewards, etc.)
   - Kintone có native dashboard capabilities

3. **Best practices:**
   - Tận dụng platform có sẵn thay vì build từ scratch
   - Giảm training overhead
   - Single source of truth

**Các tài liệu trong `initial-docs` chỉ đề cập:**
- `project_overview.md`: "Cung cấp dashboard tổng hợp realtime" (không nói nơi build)
- `user_hearing_info.md`: "Dividend Dashboard" trong sequence diagram (không rõ implementation)
- Các business flows: Đề cập dashboard nhưng không nói implementation

---

## Rationale

### 1. Operations Team Familiarity
- Operations team đã quen với Kintone interface
- Không cần training lại cho dashboard mới
- Consistent user experience với các Kintone apps hiện có

### 2. Kintone Native Capabilities
- **Kintone Dashboard Plugin (krewDashboard):**
  - Chuyển đổi dữ liệu thành bảng pivot và biểu đồ từ nhiều apps
  - Các loại biểu đồ: Gauge, Radar, Bubble, Funnel
  - Drill-down từ biểu đồ để phân tích
  - Export sang PDF, Excel, CSV
  - Real-time updates tự động khi data thay đổi

- **Kintone Native Features:**
  - Realtime refresh khi records được update
  - Cross-app data aggregation
  - User permissions và access control
  - Mobile-friendly interface

### 3. Development Efficiency
- Không cần build dashboard UI từ scratch
- Không cần Rails API endpoints cho dashboard
- Tận dụng Kintone plugins và features có sẵn
- Faster time to market
- Easier maintenance

### 4. Data Consistency
- Dashboard hiển thị data trực tiếp từ Kintone apps
- Không cần sync data qua Rails (tránh data inconsistency)
- Single source of truth: Kintone apps
- No additional latency từ Rails API calls

### 5. Separation of Concerns
- **Rails responsibility:** Process webhooks, business logic, sync data to Kintone
- **Kintone responsibility:** Data storage, dashboard display, user interface
- Clear separation giữa backend processing và frontend display

---

## Architecture Implications

### Data Flow

```
Rails Application
  ↓ (Process webhooks, business logic)
  ↓ (Sync data to Kintone via API)
Kintone Apps (Orders, Inventory, Stock Movements, etc.)
  ↓ (Native realtime updates)
Kintone Dashboard (krewDashboard hoặc native)
  ↓ (Display to users)
Operations Team
```

**Key Point:** Rails KHÔNG tham gia vào dashboard data flow. Rails chỉ sync data lên Kintone, còn dashboard hoàn toàn dựa trên Kintone apps.

### Rails Responsibilities

1. **Data Processing:**
   - Process webhooks từ Shopify
   - Business logic và validation
   - Calculate rebate, commissions
   - Generate reports

2. **Kintone Sync:**
   - Sync orders vào Kintone Orders App
   - Sync inventory vào Kintone Stock App
   - Sync stock movements vào Stock Movements App
   - Update records realtime khi có thay đổi

3. **NO Dashboard Responsibilities:**
   - ❌ Rails KHÔNG cung cấp API endpoints cho dashboard
   - ❌ Rails KHÔNG render dashboard UI
   - ❌ Rails KHÔNG aggregate data cho dashboard
   - ✅ Rails chỉ sync raw data lên Kintone apps

### Kintone Responsibilities

1. **Data Storage:**
   - Orders App: Store order data
   - Stock App: Store inventory data
   - Stock Movements App: Store movement history
   - Rewards App: Store rebate/commission data

2. **Dashboard Display (100% Kintone):**
   - Aggregate data từ multiple apps (native Kintone feature)
   - Real-time updates khi data changes (native Kintone feature)
   - Visualizations (charts, tables, pivot tables) via krewDashboard
   - Export capabilities (native Kintone feature)

3. **User Interface:**
   - Dashboard UI (Kintone native)
   - User permissions (Kintone native)
   - Mobile access (Kintone native)

---

## Implementation Details

### Dashboard Types Needed

**1. Operations Dashboard:**
- Orders mới từ Shopify (realtime)
- Order status tracking
- Inventory levels
- Picking tasks status
- Failed events (DLQ)

**2. Financial Dashboard:**
- Rebate calculations
- Commission tracking
- Payment reconciliation status
- Quarterly summaries

**3. Warehouse Dashboard:**
- Stock levels by location (棚)
- Stock movements
- Inventory accuracy
- Picking/packing status

**4. FIS Dashboard:**
- Orders từ FIS shop
- Fulfillment status
- Fees tracking

**5. Affiliate Dashboard:**
- Link performance
- Commission tracking
- Conversion metrics

### Kintone Apps for Dashboard Data

**Source Apps (Rails sync data vào đây):**
- **Orders App** - Order data
- **Stock App** - Inventory levels
- **Stock Movements App** - Movement history
- **Rewards App** - Rebate/commission data
- **Tasks App** - Picking/packing tasks
- **Error Log App** - Failed events

**Dashboard Configuration (100% Kintone):**
- Use **krewDashboard** plugin hoặc Kintone native dashboard
- Configure data sources từ các apps trên (trong Kintone)
- Set refresh intervals (realtime hoặc periodic) trong Kintone
- Configure user permissions trong Kintone
- **NO Rails involvement**

### Rails → Kintone Sync Strategy

**Realtime Sync:**
- When webhook processed → Update Kintone Orders App immediately
- When inventory changes → Update Kintone Stock App immediately
- When movement recorded → Update Stock Movements App immediately

**Sync Jobs (Sidekiq):**
```ruby
# Example sync job - chỉ sync data, không serve dashboard
class SyncOrderToKintoneJob
  def perform(order_id)
    order = Order.find(order_id)
    kintone_adapter.update_order(order)
    # Kintone dashboard tự động refresh (native feature)
    # Rails không cần làm gì thêm
  end
end
```

**Batch Sync (nếu cần):**
- Periodic sync cho aggregated data
- Reconciliation jobs
- Historical data updates

**Important:** Rails sync jobs chỉ update Kintone records. Dashboard aggregation và display hoàn toàn do Kintone xử lý.

---

## Technical Considerations

### Kintone API Usage (Rails Side)

**REST API (chỉ để sync data):**
- Update records: `PUT /k/v1/record.json`
- Bulk update: `PUT /k/v1/records.json`
- Get records: `GET /k/v1/records.json` (nếu cần validate)

**Rate Limiting:**
- Kintone API có rate limits
- Implement rate limiting trong Rails (NFR26)
- Queue throttling cho Kintone API calls

**NO Dashboard API Calls:**
- Rails KHÔNG cần fetch data từ Kintone để serve dashboard
- Rails KHÔNG cần aggregate data cho dashboard
- Dashboard hoàn toàn tự xử lý trong Kintone

### Realtime Updates

**Kintone Native:**
- Kintone tự động refresh dashboard khi records được update
- No additional WebSocket/SSE needed
- Refresh interval có thể configure trong Kintone
- **NO Rails involvement**

**Rails Side:**
- Ensure timely sync to Kintone
- Batch updates nếu có nhiều changes cùng lúc
- Error handling và retry cho failed syncs
- **NO dashboard update logic needed**

### Performance

**Optimization:**
- Cache frequently accessed data trong Rails (cho business logic, không phải dashboard)
- Batch API calls khi sync data lên Kintone
- Use Kintone bulk update APIs
- Monitor API usage và optimize

**Dashboard Performance:**
- Dashboard performance hoàn toàn do Kintone xử lý
- Rails không cần optimize cho dashboard queries
- Kintone có built-in caching và optimization

---

## Benefits

1. **Faster Development:** Không cần build dashboard UI hoặc API
2. **Better UX:** Operations team quen với Kintone
3. **Lower Maintenance:** Kintone handles dashboard updates, Rails chỉ sync data
4. **Data Consistency:** Direct from Kintone apps, no sync delays
5. **Cost Effective:** Tận dụng Kintone features có sẵn
6. **Clear Separation:** Rails = backend processing, Kintone = frontend display
7. **No API Overhead:** Không cần Rails API endpoints cho dashboard

---

## Trade-offs

**Pros:**
- ✅ Faster development
- ✅ Familiar interface
- ✅ Native realtime updates
- ✅ Lower maintenance
- ✅ Clear separation of concerns
- ✅ No API overhead

**Cons:**
- ⚠️ Limited customization (phụ thuộc vào Kintone capabilities)
- ⚠️ Kintone API rate limits cần manage (cho sync, không phải dashboard)
- ⚠️ Dashboard features phụ thuộc vào Kintone plugins

---

## Alternatives Considered

1. **Rails Dashboard với Action Cable:**
   - More customization
   - But requires building UI from scratch
   - More maintenance overhead
   - Rails API endpoints needed
   - ❌ Rejected: Không cần thiết, operations team quen Kintone

2. **Rails API + Separate Frontend (React/Vue):**
   - Modern UI/UX
   - But requires separate deployment và maintenance
   - Rails API endpoints needed
   - ❌ Rejected: Overkill, operations team không cần modern UI

3. **Hybrid: Rails API + Kintone Dashboard:**
   - Rails aggregate data, Kintone display
   - But adds unnecessary complexity
   - Rails API endpoints needed
   - ❌ Rejected: Unnecessary complexity, Kintone có thể aggregate natively

4. **Kintone Dashboard (100% Kintone):**
   - ✅ Chosen: Best fit cho requirements và user familiarity

---

## Related Decisions

- **Decision 8:** Event-Driven Architecture - Rails sync jobs update Kintone
- **Decision 6:** BtoB Portal - Separate from dashboard (Hotwire)
- **NFR26:** Rate limiting cho Kintone API (chỉ cho sync, không phải dashboard)
- **FR32-FR38:** Dashboard requirements (sẽ implement 100% trên Kintone)

---

## References

- Kintone Apps Documentation: `initial-docs/kintone_apps.md`
- Kintone Dashboard Plugin: krewDashboard
- Kintone REST API Documentation: https://developer.kintone.io/hc/en-us/articles/360000313226

---

## Summary

**Key Takeaway:** Dashboard hoàn toàn là Kintone responsibility. Rails chỉ sync data lên Kintone apps, không tham gia vào dashboard data flow hay UI rendering. Đây là clear separation of concerns: Rails = backend processing, Kintone = frontend display.

**Next Steps:**
1. Configure Kintone dashboard với krewDashboard plugin
2. Set up data sources từ Kintone apps (trong Kintone)
3. Configure refresh intervals và permissions (trong Kintone)
4. Test realtime updates với Rails sync jobs (Rails chỉ sync, Kintone tự refresh)

