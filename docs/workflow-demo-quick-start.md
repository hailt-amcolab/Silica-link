# Quick Start: Workflow Demo HTML

## Tổng Quan

File `workflow-demo-template.html` là một visual demo interactive cho khách hàng non-tech để confirm workflow.

**Đặc điểm:**
- ✅ Chỉ hiển thị những gì khách hàng có thể **THẤY** và **THAO TÁC**
- ✅ Không có technical jargon (HMAC, Idempotency, Redis, etc.)
- ✅ Có ảnh màn hình để minh họa từng bước
- ✅ Interactive: Click "Next Step" để xem từng bước
- ✅ Auto Play: Tự động chạy qua các bước

---

## Cách Sử Dụng

### 1. Mở File

Double-click file `workflow-demo-template.html` hoặc mở bằng browser.

### 2. Xem Demo

- Click **"Next Step"** để xem từng bước
- Click **"Auto Play"** để tự động chạy
- Click **"Reset"** để quay lại đầu

### 3. Thêm Ảnh (Optional)

1. Chụp ảnh màn hình các app (Shopify, Kintone, Mobile App)
2. Đặt ảnh vào thư mục `docs/screenshots/`
3. Đặt tên theo format: `01-shopify-order.png`, `02-kintone-order-app.png`, etc.
4. Refresh browser để xem ảnh

**Nếu chưa có ảnh:** File vẫn hoạt động, chỉ hiển thị placeholder.

---

## Workflow Steps (Epic 2 - Order Processing)

Template hiện tại có **10 steps** focus vào user-facing actions:

1. **Khách Hàng Đặt Hàng Trên Shopify** - Khách hàng thấy gì
2. **Order Tự Động Xuất Hiện Trong Kintone** - Nhân viên thấy gì
3. **Kiểm Tra Tồn Kho Tự Động** - Nhân viên thấy kết quả
4. **Nhân Viên Xác Nhận Order** - Nhân viên thao tác gì
5. **Tồn Kho Tự Động Được Trừ** - Nhân viên thấy trong Stock App
6. **Picking Task Tự Động Được Tạo** - Nhân viên kho thấy gì
7. **Nhân Viên Kho Scan Barcode** - Nhân viên kho thao tác gì
8. **Shipping Label Tự Động Được Tạo** - Nhân viên thấy gì
9. **Khách Hàng Nhận Email Tracking** - Khách hàng thấy gì
10. **Hoàn Thành - Zero Manual Entry** - Kết quả cuối cùng

---

## Customize Cho Epic Khác

### Thay Đổi Steps

Tìm section này trong file HTML:

```javascript
const workflowData = {
  title: "Order Processing Automation - Từ Góc Nhìn Người Dùng",
  steps: [
    {
      number: 1,
      icon: "🛒",
      title: "Khách Hàng Đặt Hàng Trên Shopify",
      description: "Khách hàng vào website Shopify, chọn sản phẩm và đặt hàng",
      screenshot: "screenshots/01-shopify-order.png",
      screenshotCaption: "Màn hình Shopify - Khách hàng đặt hàng",
      details: {
        title: "Khách hàng thấy:",
        items: [
          "Website Shopify với catalog sản phẩm",
          // ... more items
        ]
      }
    },
    // ... more steps
  ]
};
```

### Rules Khi Customize

1. **Chỉ user-facing actions:**
   - ❌ Không: "HMAC Verification", "Idempotency Check", "Log to S3"
   - ✅ Có: "Khách hàng đặt hàng", "Nhân viên xác nhận", "Order xuất hiện"

2. **Focus vào "THẤY" và "THAO TÁC":**
   - "Khách hàng **thấy** gì?"
   - "Nhân viên **thao tác** gì?"
   - "Hệ thống **hiển thị** gì?"

3. **Dùng ngôn ngữ business:**
   - ❌ "Call Kintone API"
   - ✅ "Order xuất hiện trong Kintone"

4. **Thêm screenshot:**
   - Mỗi step nên có ảnh màn hình
   - Đặt ảnh trong `screenshots/` folder
   - Reference trong `screenshot: "screenshots/XX-name.png"`

---

## Example: Epic 3 - Fulfillment

```javascript
const workflowData = {
  title: "Fulfillment Automation - Từ Góc Nhìn Người Dùng",
  steps: [
    {
      number: 1,
      icon: "✅",
      title: "Order Đã Được Xác Nhận",
      description: "Nhân viên thấy order trong Kintone với status '受注確定'",
      screenshot: "screenshots/fulfillment-01-order-confirmed.png",
      screenshotCaption: "Màn hình Kintone - Order đã được xác nhận",
      details: {
        title: "Nhân viên thấy:",
        items: [
          "Order status = '受注確定'",
          "Tồn kho đã được reserve",
          "Sẵn sàng để tạo picking task"
        ]
      }
    },
    {
      number: 2,
      icon: "📋",
      title: "Picking Task Tự Động Xuất Hiện",
      description: "Task xuất kho tự động xuất hiện trong Kintone Task App",
      screenshot: "screenshots/fulfillment-02-picking-task.png",
      screenshotCaption: "Màn hình Kintone Task App - Picking task",
      details: {
        title: "Nhân viên kho thấy:",
        items: [
          "Task mới trong Task App",
          "Danh sách SKU cần pick",
          "Vị trí kệ chứa hàng"
        ]
      }
    },
    // ... more steps
  ]
};
```

---

## Checklist Trước Khi Gửi Khách Hàng

- [ ] Test workflow (click Next Step, Auto Play)
- [ ] Kiểm tra tất cả steps hiển thị đúng
- [ ] Thêm ảnh màn hình (hoặc để placeholder)
- [ ] Review language (không có tech jargon)
- [ ] Check spelling và grammar
- [ ] Test trên mobile (responsive)
- [ ] Anonymize data trong ảnh (nếu có)

---

## Time Required

- **Setup:** 0 phút (đã có template)
- **Customize steps:** 30-60 phút
- **Chụp/thêm ảnh:** 1-2 giờ (optional)
- **Review & test:** 15 phút

**Total:** 45 phút - 3 giờ (tùy có ảnh hay không)

---

## Tips

1. **Nếu chưa có app thật:**
   - Dùng mockup tools (Figma, Balsamiq)
   - Hoặc để placeholder (file vẫn hoạt động)

2. **Nếu có app thật:**
   - Chụp ảnh màn hình thật
   - Anonymize data nhạy cảm
   - Dùng fake data cho demo

3. **Share với khách:**
   - Gửi file HTML qua email
   - Hoặc host online (GitHub Pages)
   - Hoặc convert to PDF (nhưng mất tính interactive)

---

**Questions?** Xem file `workflow-demo-customize-guide.md` để biết chi tiết hơn.

