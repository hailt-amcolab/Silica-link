# Technical Research Report: Thiết kế chuỗi webhook Shopify → Yoom → Rails/Kintone đảm bảo idempotency và độ tin cậy

**Date:** 2025-11-19
**Prepared by:** Potato
**Project Context:** Silica-link là dự án hiện đại hóa chuỗi Đặt hàng → Giao hàng → Nhập hàng → Rebate cho CREDIE, tích hợp nhiều hệ thống sẵn có (Shopify, Yoom, Ruby on Rails backend, Kintone, Ship&Co, GMO Bank API, React-based WMS). Bối cảnh brownfield, yêu cầu realtime, đa kênh (BtoC, BtoB, FIS).

---

## Executive Summary

### Key Recommendation

**Primary Choice:** Rails + Sidekiq + Redis với Idempotency Pattern

**Rationale:** 
Giải pháp này phù hợp nhất với technical stack hiện tại của Silica-link (Rails, Redis đã có sẵn), cung cấp đầy đủ tính năng cần thiết (idempotency, retry, DLQ) với độ phức tạp vừa phải. Sidekiq là gem phổ biến trong Rails ecosystem, dễ maintain và scale.

**Key Benefits:**

- **Familiar Stack**: Sử dụng Rails + Redis đã có, không cần thêm infrastructure mới
- **Proven Pattern**: Sidekiq là battle-tested solution với large community
- **Cost Effective**: Không cần thêm AWS services phức tạp
- **Easy Debugging**: Rails logs và Sidekiq web UI cho monitoring
- **Flexible**: Dễ customize và extend cho future requirements

---

## 1. Research Objectives

### Technical Question

Làm thế nào để thiết kế và triển khai pipeline webhook Shopify → Yoom → Rails/Kintone đảm bảo idempotency, bảo mật chữ ký, cơ chế retry/đảm bảo giao hàng, và quan sát được toàn bộ hành trình sự kiện trong môi trường Silica-link?

### Project Context

Silica-link cần đồng bộ realtime đơn hàng từ nhiều kênh (Shopify chính, Shopify FIS, Rakuten, Amazon) vào backend Rails và Kintone. Hệ thống đang chuyển đổi từ thao tác thủ công sang kiến trúc event-driven với Yoom làm gateway. Tính chất brownfield yêu cầu tích hợp với Kintone, Ship&Co và GMO Bank API, đồng thời duy trì audit log, idempotency và khả năng mở rộng sang đa kho.

### Requirements and Constraints

#### Functional Requirements

1. **Webhook Reception & Validation**
   - Nhận webhook từ Shopify (orders/create, orders/updated, orders/paid, orders/cancelled, orders/fulfilled)
   - Xác thực HMAC SHA256 signature từ Shopify
   - Validate payload structure và required fields
   - Support multiple Shopify shops (CREDIE shop, FIS shop)

2. **Idempotency & Duplicate Prevention**
   - Xử lý duplicate webhook events (Shopify có thể gửi lại)
   - Sử dụng idempotency key (Shopify webhook ID hoặc order ID + timestamp)
   - Đảm bảo mỗi order chỉ được tạo một lần trong Kintone
   - Track processed webhooks trong Redis hoặc database

3. **Order Processing Pipeline**
   - Validate order data (customer, line items, shipping address)
   - Check inventory availability từ Kintone Inventory App (realtime)
   - Tạo order record trong Kintone Orders App
   - Generate Proforma Invoice (cho BtoB) hoặc confirm order (cho BtoC)
   - Trigger downstream processes (picking task, shipping label generation)

4. **Error Handling & Retry**
   - Retry mechanism với exponential backoff
   - Dead Letter Queue (DLQ) cho failed webhooks
   - Manual review interface cho failed events
   - Alerting khi retry threshold exceeded

5. **Audit & Observability**
   - Log toàn bộ webhook events (raw payload) vào S3
   - Audit trail trong Kintone Audit_Log app
   - Track processing time, success/failure rates
   - Dashboard monitoring webhook health

#### Non-Functional Requirements

1. **Performance**
   - Process webhook trong < 2 seconds (p95 latency)
   - Support concurrent processing (multiple webhooks simultaneously)
   - Handle peak load (ví dụ: 1000 orders/hour during sales events)

2. **Reliability**
   - 99.9% webhook processing success rate
   - At-least-once delivery guarantee (có thể duplicate nhưng phải idempotent)
   - Zero data loss (mọi webhook phải được lưu trước khi process)

3. **Security**
   - HMAC signature verification mandatory
   - Encrypt sensitive data (PII) trong audit logs
   - Rate limiting để prevent abuse
   - IP whitelist cho Shopify webhook IPs (nếu có thể)

4. **Scalability**
   - Horizontal scaling capability (multiple Rails workers)
   - Queue-based architecture để handle spikes
   - Database connection pooling
   - Redis for caching và idempotency tracking

5. **Maintainability**
   - Clear error messages và logging
   - Easy debugging và troubleshooting
   - Documentation cho operations team
   - Health check endpoints

#### Technical Constraints

1. **Existing Technology Stack**
   - Ruby on Rails (backend orchestrator)
   - MySQL database (Kintone hoặc custom)
   - Redis (caching và idempotency)
   - AWS EC2 + S3 (infrastructure)
   - Kintone API (business apps)
   - Yoom (webhook gateway/orchestrator)

2. **Integration Requirements**
   - Must integrate với Kintone API (REST)
   - Must support Shopify webhook format
   - Must work với Yoom gateway
   - Must store audit logs trên S3

3. **Team & Skills**
   - Team có expertise với Ruby on Rails
   - Familiar với AWS services
   - Cần training về webhook reliability patterns

4. **Budget & Timeline**
   - Brownfield project - cần migration từ manual process
   - Minimize disruption to existing operations
   - Phased rollout approach

5. **Compliance & Regulations**
   - Audit requirements (ai/khi nào/cái gì/bao nhiêu/ở đâu)
   - Data retention policies
   - PII handling compliance

---

## 2. Technology Options Evaluated

### Option 1: Rails + Sidekiq + Redis (Recommended)
- **Queue System**: Sidekiq với Redis backend
- **Idempotency**: Redis-based idempotency keys
- **Retry**: Sidekiq built-in retry với exponential backoff
- **DLQ**: Sidekiq dead job queue
- **Audit**: ActiveJob callbacks + S3 upload

### Option 2: Rails + AWS SQS + Lambda
- **Queue System**: AWS SQS với Rails workers
- **Idempotency**: DynamoDB hoặc Redis
- **Retry**: SQS visibility timeout + DLQ
- **Audit**: Lambda functions cho audit logging
- **Scalability**: Auto-scaling với SQS queue depth

### Option 3: Event Sourcing Pattern
- **Event Store**: Rails EventStore hoặc custom event log
- **Idempotency**: Event ID-based deduplication
- **Replay**: Full event replay capability
- **Audit**: Built-in event history
- **Complexity**: Higher implementation complexity

---

## 3. Detailed Technology Profiles

{{#tech_profile_1}}

### Option 1: [Technology Name]

{{tech_profile_1}}
{{/tech_profile_1}}

{{#tech_profile_2}}

### Option 2: [Technology Name]

{{tech_profile_2}}
{{/tech_profile_2}}

{{#tech_profile_3}}

### Option 3: [Technology Name]

{{tech_profile_3}}
{{/tech_profile_3}}

{{#tech_profile_4}}

### Option 4: [Technology Name]

{{tech_profile_4}}
{{/tech_profile_4}}

{{#tech_profile_5}}

### Option 5: [Technology Name]

{{tech_profile_5}}
{{/tech_profile_5}}

---

## 4. Comparative Analysis

{{comparative_analysis}}

### Weighted Analysis

**Decision Priorities:**
{{decision_priorities}}

{{weighted_analysis}}

---

## 5. Trade-offs and Decision Factors

{{use_case_fit}}

### Key Trade-offs

[Comparison of major trade-offs between top options]

---

## 6. Real-World Evidence

{{real_world_evidence}}

---

## 7. Architecture Pattern Analysis

{{#architecture_pattern_analysis}}
{{architecture_pattern_analysis}}
{{/architecture_pattern_analysis}}

---

## 8. Recommendations

### Primary Recommendation: Rails + Sidekiq + Redis

**Architecture Pattern:**
```
Shopify Webhook → Yoom Gateway → Rails API Endpoint
  → HMAC Verification → Idempotency Check (Redis)
  → Sidekiq Job Queue → Process Order → Kintone API
  → Audit Log (S3) → Success/Failure Callback
```

**Key Components:**

1. **Webhook Controller (Rails)**
   - Verify HMAC signature
   - Check idempotency key trong Redis
   - Enqueue Sidekiq job
   - Return 200 OK immediately

2. **Sidekiq Worker**
   - Process order logic
   - Retry với exponential backoff (3 attempts)
   - Dead job queue cho failed events
   - Idempotency check trước khi process

3. **Redis Idempotency Store**
   - Key: `webhook:{shopify_webhook_id}` hoặc `order:{order_id}:{event_type}`
   - TTL: 7 days (đủ để handle late duplicates)
   - Value: processing status + timestamp

4. **S3 Audit Log**
   - Store raw webhook payload
   - Partition by date: `s3://bucket/webhooks/YYYY/MM/DD/{webhook_id}.json`
   - Metadata: shop_id, order_id, event_type, timestamp

### Implementation Roadmap

1. **Proof of Concept Phase (Week 1-2)**
   - Setup Sidekiq với Redis
   - Implement basic webhook endpoint với HMAC verification
   - Test idempotency với duplicate webhooks
   - Validate với test Shopify webhook

2. **Key Implementation Decisions**
   - **Idempotency Key Strategy**: Sử dụng `shopify_webhook_id` (unique per webhook) hoặc `order_id:event_type` (nếu muốn dedupe same event type)
   - **Retry Strategy**: 3 retries với delays [1min, 5min, 15min]
   - **DLQ Handling**: Manual review interface trong Rails admin
   - **Audit Storage**: Async S3 upload để không block processing

3. **Migration Path**
   - **Phase 1**: Deploy webhook endpoint song song với manual process
   - **Phase 2**: Route một số shops sang webhook, monitor closely
   - **Phase 3**: Full migration khi confidence đạt 99%+
   - **Rollback Plan**: Có thể tắt webhook và quay lại manual nếu cần

4. **Success Criteria**
   - 99.9% webhook processing success rate
   - < 2 seconds p95 latency
   - Zero duplicate orders trong production
   - All webhooks có audit trail trong S3

### Risk Mitigation

**Identified Risks:**

1. **Redis Failure**: 
   - Mitigation: Redis cluster với replication, fallback to database idempotency check

2. **Kintone API Rate Limits**:
   - Mitigation: Rate limiting trong Sidekiq, queue throttling

3. **Sidekiq Worker Crash**:
   - Mitigation: Process monitoring, auto-restart, DLQ cho failed jobs

4. **HMAC Secret Rotation**:
   - Mitigation: Support multiple secrets, gradual rotation

**Contingency Options:**
- Nếu Sidekiq không đáp ứng scale: Migrate sang AWS SQS
- Nếu Redis không đủ: Consider DynamoDB cho idempotency
- Nếu cần stronger guarantees: Consider event sourcing pattern

---

## 9. Architecture Decision Record (ADR)

# ADR-001: Webhook Processing với Rails + Sidekiq + Redis

## Status

**Proposed** - Awaiting team review and approval

## Context

Silica-link cần xử lý webhooks từ Shopify một cách reliable, đảm bảo idempotency và có khả năng retry khi lỗi. Hiện tại đang xử lý manual, cần automation để scale và giảm lỗi.

## Decision Drivers

- **Existing Stack**: Rails + Redis đã có sẵn
- **Team Expertise**: Team quen với Rails ecosystem
- **Reliability Requirements**: 99.9% success rate, zero duplicates
- **Budget**: Minimize additional infrastructure costs
- **Time to Market**: Cần solution nhanh, proven patterns

## Considered Options

1. **Rails + Sidekiq + Redis** (Chosen)
2. Rails + AWS SQS + Lambda
3. Event Sourcing Pattern với EventStore

## Decision

Chọn **Rails + Sidekiq + Redis** vì:
- Phù hợp với stack hiện tại, không cần thêm infrastructure
- Sidekiq là battle-tested, có large community support
- Dễ implement và maintain
- Cost-effective (chỉ cần Redis, đã có sẵn)
- Flexible cho future requirements

## Consequences

**Positive:**
- Quick implementation (1-2 weeks POC)
- Familiar technology cho team
- Good observability với Sidekiq web UI
- Easy to debug và troubleshoot

**Negative:**
- Redis là single point of failure (mitigate với cluster)
- Sidekiq workers cần monitoring và scaling
- Manual DLQ review (có thể automate sau)

**Neutral:**
- Có thể migrate sang SQS sau nếu cần scale lớn hơn
- Pattern này phù hợp cho current scale, có thể evolve khi cần

---

## 10. References and Resources

### Documentation

- [Links to official documentation]

### Benchmarks and Case Studies

- [Links to benchmarks and real-world case studies]

### Community Resources

- [Links to communities, forums, discussions]

### Additional Reading

- [Links to relevant articles, papers, talks]

---

## Appendices

### Appendix A: Detailed Comparison Matrix

[Full comparison table with all evaluated dimensions]

### Appendix B: Proof of Concept Plan

[Detailed POC plan if needed]

### Appendix C: Cost Analysis

[TCO analysis if performed]

---

## References and Sources

**CRITICAL: All technical claims, versions, and benchmarks must be verifiable through sources below**

### Official Documentation and Release Notes

{{sources_official_docs}}

### Performance Benchmarks and Comparisons

{{sources_benchmarks}}

### Community Experience and Reviews

{{sources_community}}

### Architecture Patterns and Best Practices

{{sources_architecture}}

### Additional Technical References

{{sources_additional}}

### Version Verification

- **Technologies Researched:** {{technology_count}}
- **Versions Verified (2025):** {{verified_versions_count}}
- **Sources Requiring Update:** {{outdated_sources_count}}

**Note:** All version numbers were verified using current 2025 sources. Versions may change - always verify latest stable release before implementation.

---

## Document Information

**Workflow:** BMad Research Workflow - Technical Research v2.0
**Generated:** 2025-11-19
**Research Type:** Technical/Architecture Research
**Next Review:** [Date for review/update]
**Total Sources Cited:** {{total_sources}}

---

_This technical research report was generated using the BMad Method Research Workflow, combining systematic technology evaluation frameworks with real-time research and analysis. All version numbers and technical claims are backed by current 2025 sources._


