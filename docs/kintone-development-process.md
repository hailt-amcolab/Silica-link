# Silica-link - Kintone Development Process

**Author:** Potato (PM & Scrum Master)  
**Date:** 2025-11-20  
**Version:** 1.0

---

## 📋 Overview

Document này mô tả quy trình development khi làm việc với Kintone trong Silica-link project. Vì Kintone là brownfield system (hệ thống hiện có), cần có quy trình đặc biệt cho testing, review, và deployment.

---

## 🎯 Kintone Integration Context

### Architecture Summary

- **Rails Responsibility:** Process webhooks, business logic, sync data to Kintone
- **Kintone Responsibility:** Data storage, dashboard display, user interface
- **Data Flow:** `Rails → Kintone API → Kintone Apps → Kintone Dashboard`

### Key Points

1. **Rails sync data lên Kintone apps** (Orders, Stock, Rewards, etc.)
2. **Kintone dashboard 100% native** (không phải Rails)
3. **Kintone apps là source of truth** cho business data
4. **Rails chỉ temporary storage** cho webhook processing

---

## 🔄 Development Workflow với Kintone

### Story Types Involving Kintone

1. **Kintone API Integration Stories:**
   - Sync data từ Rails → Kintone
   - Kintone adapter implementation
   - Error handling cho Kintone API

2. **Kintone App Configuration Stories:**
   - Create/update Kintone app schemas
   - Configure fields, permissions, workflows
   - Setup Kintone app relationships

3. **Kintone Dashboard Stories:**
   - Configure Kintone dashboard (krewDashboard)
   - Setup data sources từ apps
   - Configure visualizations

4. **Hybrid Stories:**
   - Rails code + Kintone app changes
   - Data sync + dashboard updates

---

## 🧪 Testing Process với Kintone

### 1. Kintone Sandbox Environment

**Purpose:**
- Test Kintone integration without affecting production
- Test app schema changes
- Test dashboard configurations

**Setup:**
- Separate Kintone sandbox subdomain
- Copy production app schemas (nếu cần)
- Use test data only

**Access:**
- All developers cần access
- Separate credentials từ production
- Document credentials trong secure location

### 2. Testing Checklist

#### Rails Code Testing

**Unit Tests:**
```ruby
# Test Kintone adapter
RSpec.describe KintoneAdapter do
  it "syncs order to Kintone" do
    # Mock Kintone API response
    # Test adapter logic
  end
end
```

**Integration Tests:**
```ruby
# Test với Kintone sandbox
RSpec.describe "Order Sync Integration" do
  it "syncs order to Kintone sandbox" do
    # Real API call to sandbox
    # Verify data in Kintone
  end
end
```

**Test Coverage:**
- ✅ Kintone API calls
- ✅ Error handling (rate limits, timeouts)
- ✅ Data transformation (Rails → Kintone format)
- ✅ Retry logic
- ✅ Idempotency

#### Kintone App Testing

**Schema Testing:**
- ✅ Verify app fields exist
- ✅ Verify field types correct
- ✅ Verify field relationships
- ✅ Verify permissions correct

**Data Testing:**
- ✅ Verify data synced correctly
- ✅ Verify data format correct
- ✅ Verify relationships maintained
- ✅ Verify calculated fields work

**Workflow Testing:**
- ✅ Verify workflows trigger correctly
- ✅ Verify notifications sent
- ✅ Verify status updates

#### Kintone Dashboard Testing

**Configuration Testing:**
- ✅ Verify data sources configured
- ✅ Verify visualizations display correctly
- ✅ Verify realtime updates work
- ✅ Verify permissions correct

**Functional Testing:**
- ✅ Verify dashboard loads
- ✅ Verify data displays correctly
- ✅ Verify filters work
- ✅ Verify export functions work

### 3. Testing Environment Setup

**Rails Test Environment:**
```ruby
# config/environments/test.rb
config.kintone_domain = ENV['KINTONE_SANDBOX_DOMAIN']
config.kintone_api_token = ENV['KINTONE_SANDBOX_API_TOKEN']
```

**Test Data:**
- Use test data only trong sandbox
- Don't use production data
- Clean up test data after tests

**Test Isolation:**
- Each test should be independent
- Don't rely on data from other tests
- Use factories/fixtures cho test data

---

## 📝 Code Review Process với Kintone

### Review Checklist

#### Rails Code Review

**Kintone Adapter/Service:**
- ✅ Proper error handling
- ✅ Rate limiting handled
- ✅ Retry logic implemented
- ✅ Idempotency ensured
- ✅ Data transformation correct
- ✅ API versioning considered

**Example:**
```ruby
# Good: Proper error handling
def sync_order(order)
  kintone_adapter.create_record(order)
rescue KintoneAPIError => e
  logger.error("Kintone sync failed: #{e.message}")
  raise SyncError, "Failed to sync order to Kintone"
end
```

#### Kintone App Configuration Review

**Schema Review:**
- ✅ Fields properly named
- ✅ Field types appropriate
- ✅ Required fields set correctly
- ✅ Field relationships correct
- ✅ Permissions appropriate

**Workflow Review:**
- ✅ Workflows trigger correctly
- ✅ Notifications configured
- ✅ Status transitions correct

**Documentation:**
- ✅ App schema documented
- ✅ Field purposes documented
- ✅ Workflow logic documented

#### Kintone Dashboard Review

**Configuration Review:**
- ✅ Data sources correct
- ✅ Visualizations appropriate
- ✅ Refresh intervals set
- ✅ Permissions correct

**Functional Review:**
- ✅ Dashboard displays correctly
- ✅ Data updates realtime
- ✅ Filters work correctly
- ✅ Export functions work

### Review Process

1. **Rails Code Review:**
   - Standard code review process
   - Focus on Kintone integration code
   - Verify error handling
   - Verify test coverage

2. **Kintone App Review:**
   - PM/Scrum Master reviews app configurations
   - Verify schema changes documented
   - Verify permissions appropriate
   - Test trên sandbox

3. **Kintone Dashboard Review:**
   - PM/Scrum Master reviews dashboard configuration
   - Verify data sources correct
   - Test visualizations
   - Verify realtime updates

---

## 🚀 Deployment Process với Kintone

### Deployment Steps

#### 1. Rails Code Deployment

**Standard Process:**
- Deploy Rails code to staging
- Run tests
- Verify functionality
- Deploy to production

**Kintone-Specific:**
- ✅ Verify Kintone API credentials correct
- ✅ Test Kintone sync trên staging
- ✅ Monitor Kintone API usage
- ✅ Verify rate limiting works

#### 2. Kintone App Deployment

**Sandbox First:**
1. Deploy app changes to sandbox
2. Test với test data
3. Verify schema changes
4. Verify workflows

**Production Deployment:**
1. **Backup production app** (nếu schema changes)
2. Deploy app changes to production
3. Verify data integrity
4. Monitor for issues

**Rollback Plan:**
- Have backup của app schema
- Can restore từ backup nếu issues
- Document rollback procedure

#### 3. Kintone Dashboard Deployment

**Configuration:**
1. Configure dashboard trên sandbox
2. Test visualizations
3. Verify data sources
4. Deploy to production

**No Code Deployment:**
- Dashboard là configuration, không phải code
- Changes made directly trong Kintone
- Document configuration changes

### Deployment Checklist

**Before Deployment:**
- [ ] Kintone sandbox testing complete
- [ ] App schema changes documented
- [ ] Dashboard configuration documented
- [ ] Rollback plan prepared
- [ ] Stakeholders notified (nếu major changes)

**During Deployment:**
- [ ] Deploy Rails code first
- [ ] Deploy Kintone app changes (nếu có)
- [ ] Configure Kintone dashboard (nếu có)
- [ ] Monitor for errors
- [ ] Verify data sync works

**After Deployment:**
- [ ] Verify functionality
- [ ] Monitor Kintone API usage
- [ ] Check for errors
- [ ] Update documentation
- [ ] Notify stakeholders

---

## 📊 Monitoring & Metrics

### Kintone API Metrics

**Track These:**
- API call success rate
- API call latency
- Rate limit usage
- Error rate
- Retry count

**Tools:**
- Application logs
- Kintone API monitoring
- Custom metrics dashboard

### Kintone Sync Metrics

**Track These:**
- Sync success rate
- Sync latency (Rails → Kintone)
- Failed syncs
- Retry attempts
- Data validation errors

### Kintone App Metrics

**Track These:**
- App usage
- Record creation rate
- Workflow execution rate
- Error rate

---

## 🚨 Error Handling & Troubleshooting

### Common Issues

**Kintone API Errors:**
- Rate limit exceeded
- Authentication errors
- Invalid data format
- Network timeouts

**Solutions:**
- Implement rate limiting
- Retry với exponential backoff
- Validate data before sync
- Handle timeouts gracefully

**Kintone App Issues:**
- Schema mismatch
- Permission errors
- Workflow errors
- Data validation errors

**Solutions:**
- Verify schema before sync
- Check permissions
- Review workflow logic
- Validate data format

### Troubleshooting Process

1. **Identify Issue:**
   - Check application logs
   - Check Kintone error logs
   - Verify API responses

2. **Reproduce:**
   - Reproduce trên sandbox
   - Identify root cause
   - Document issue

3. **Fix:**
   - Fix issue
   - Test fix trên sandbox
   - Deploy fix

4. **Verify:**
   - Verify fix works
   - Monitor for recurrence
   - Update documentation

---

## 📚 Documentation Requirements

### Kintone App Documentation

**Required:**
- App schema documentation
- Field definitions
- Workflow logic
- Permission matrix
- Data relationships

**Location:**
- `docs/kintone-apps/` directory
- One file per app
- Keep up-to-date

### Kintone Integration Documentation

**Required:**
- API integration guide
- Error handling guide
- Rate limiting guide
- Sync process documentation

**Location:**
- `docs/kintone-integration.md`
- Update khi có changes

### Kintone Dashboard Documentation

**Required:**
- Dashboard configuration
- Data sources
- Visualization types
- Refresh intervals
- Permissions

**Location:**
- `docs/kintone-dashboards.md`
- Update khi có changes

---

## ✅ Definition of Done cho Kintone Stories

Story liên quan Kintone is **Done** when:

**Rails Code:**
- ✅ Code implemented và tested
- ✅ Kintone integration tests pass
- ✅ Error handling implemented
- ✅ Rate limiting handled
- ✅ Code review approved

**Kintone App:**
- ✅ App schema changes documented
- ✅ App tested trên sandbox
- ✅ Permissions verified
- ✅ Workflows tested
- ✅ PM/Scrum Master approved

**Kintone Dashboard:**
- ✅ Dashboard configured
- ✅ Data sources verified
- ✅ Visualizations tested
- ✅ Realtime updates verified
- ✅ PM/Scrum Master approved

**Deployment:**
- ✅ Deployed to staging
- ✅ Tested trên staging
- ✅ Deployed to production
- ✅ Verified on production
- ✅ Documentation updated

---

## 🔧 Tools & Resources

### Kintone Development Tools

**Kintone Developer Portal:**
- https://developer.kintone.io/
- API documentation
- SDK documentation
- Best practices

**Kintone REST API:**
- https://developer.kintone.io/hc/en-us/articles/360000313226
- API reference
- Rate limits
- Authentication

**Kintone Plugins:**
- krewDashboard: Dashboard visualization
- Other plugins as needed

### Internal Tools

**Kintone Sandbox:**
- Separate subdomain
- Test environment
- Test data only

**Kintone Production:**
- Production subdomain
- Live data
- Restricted access

**Documentation:**
- `docs/kintone-apps/` - App schemas
- `docs/kintone-integration.md` - Integration guide
- `docs/kintone-dashboards.md` - Dashboard configs

---

## 📋 Quick Reference

### Kintone Story Checklist

**Development:**
- [ ] Review Kintone app schemas
- [ ] Setup Kintone sandbox access
- [ ] Write Kintone integration tests
- [ ] Implement Kintone adapter/service
- [ ] Test trên sandbox

**Review:**
- [ ] Code review (Rails code)
- [ ] App configuration review (Kintone)
- [ ] Dashboard review (Kintone)
- [ ] Documentation review

**Deployment:**
- [ ] Deploy Rails code
- [ ] Deploy Kintone app changes (nếu có)
- [ ] Configure Kintone dashboard (nếu có)
- [ ] Verify on staging
- [ ] Deploy to production
- [ ] Verify on production

---

**Document Status:** Complete  
**Next Steps:** Follow this process for all Kintone-related stories

