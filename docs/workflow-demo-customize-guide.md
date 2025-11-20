# Hướng Dẫn Customize Workflow Demo HTML

**File:** `workflow-demo-template.html`  
**Mục đích:** Tạo visual demo đơn giản cho khách hàng non-tech để confirm workflow

---

## Cách Sử Dụng

### 1. Mở File HTML
- Double-click file `workflow-demo-template.html`
- Hoặc mở bằng browser (Chrome, Firefox, Edge)

### 2. Customize Workflow Data

Tìm section này trong file HTML (dòng ~200):

```javascript
const workflowData = {
    title: "Order Processing Automation",
    steps: [
        {
            number: 1,
            icon: "🛒",
            title: "Shopify Order Created",
            description: "Khách hàng đặt hàng trên Shopify",
            details: {
                title: "Chi tiết:",
                items: [
                    "Order được tạo trên Shopify",
                    "Shopify gửi webhook đến Yoom",
                    "Yoom forward webhook đến Rails server"
                ]
            }
        },
        // ... more steps
    ]
};
```

### 3. Thay Đổi Nội Dung

**a) Thay đổi Title:**
```javascript
title: "Epic 2: Order Processing Automation"
```

**b) Thêm/Sửa/Xóa Steps:**
```javascript
{
    number: 1,                    // Số thứ tự
    icon: "🛒",                   // Emoji icon
    title: "Step Title",          // Tiêu đề ngắn
    description: "Mô tả ngắn",    // Mô tả 1-2 câu
    details: {
        title: "Chi tiết:",
        items: [
            "Chi tiết 1",
            "Chi tiết 2",
            "Chi tiết 3"
        ]
    }
}
```

**c) Icons có thể dùng:**
- 🛒 🛍️ 📦 📱 💻 🔄 ✅ ❌ ⚠️ 🔐 📊 📝 🎉
- Hoặc bất kỳ emoji nào

---

## Examples Cho Các Epic Khác

### Example 1: Epic 3 - Fulfillment Automation

```javascript
const workflowData = {
    title: "Fulfillment Automation",
    steps: [
        {
            number: 1,
            icon: "✅",
            title: "Order Confirmed",
            description: "Order đã được confirm trong Kintone",
            details: {
                title: "Chi tiết:",
                items: [
                    "Order status = '受注確定'",
                    "Inventory đã được reserve",
                    "Ready để tạo picking task"
                ]
            }
        },
        {
            number: 2,
            icon: "📋",
            title: "Create Picking Task",
            description: "Tự động tạo picking task trong Kintone Task App",
            details: {
                title: "Chi tiết:",
                items: [
                    "Tạo task trong Kintone Task App",
                    "Assign cho warehouse team",
                    "Include SKU, quantity, location"
                ]
            }
        },
        {
            number: 3,
            icon: "📱",
            title: "Warehouse Staff Picks",
            description: "Nhân viên kho scan barcode và pick hàng",
            details: {
                title: "Chi tiết:",
                items: [
                    "Scan barcode trên mobile app",
                    "Confirm picking location",
                    "Update physical inventory"
                ]
            }
        },
        {
            number: 4,
            icon: "🚚",
            title: "Generate Shipping Label",
            description: "Tự động tạo shipping label qua Ship&Co API",
            details: {
                title: "Chi tiết:",
                items: [
                    "Call Ship&Co API",
                    "Generate shipping label PDF",
                    "Save to S3"
                ]
            }
        },
        {
            number: 5,
            icon: "📦",
            title: "Sync Tracking to Shopify",
            description: "Đồng bộ tracking number về Shopify",
            details: {
                title: "Chi tiết:",
                items: [
                    "Update Shopify order với tracking",
                    "Notify customer",
                    "Update Kintone order status"
                ]
            }
        }
    ]
};
```

### Example 2: Epic 5 - BtoB Portal

```javascript
const workflowData = {
    title: "BtoB Portal - Đại Lý Đặt Hàng",
    steps: [
        {
            number: 1,
            icon: "🌐",
            title: "Đại Lý Đăng Nhập",
            description: "Đại lý login vào BtoB Portal",
            details: {
                title: "Chi tiết:",
                items: [
                    "Authentication với credentials riêng",
                    "View dashboard với orders history",
                    "Access realtime inventory"
                ]
            }
        },
        {
            number: 2,
            icon: "🛒",
            title: "Chọn Sản Phẩm",
            description: "Đại lý browse và chọn sản phẩm",
            details: {
                title: "Chi tiết:",
                items: [
                    "View catalog với prices",
                    "Check realtime inventory",
                    "Add to cart"
                ]
            }
        },
        {
            number: 3,
            icon: "💰",
            title: "Tính Giá (Với Discount)",
            description: "Hệ thống tự động tính giá với discount rate",
            details: {
                title: "Chi tiết:",
                items: [
                    "Apply discount rate từ Partner Master",
                    "Show final price",
                    "Calculate total"
                ]
            }
        },
        {
            number: 4,
            icon: "📝",
            title: "Submit Order",
            description: "Đại lý submit order",
            details: {
                title: "Chi tiết:",
                items: [
                    "Create order trong Kintone",
                    "Status = 'Pending Approval'",
                    "Notify sales staff"
                ]
            }
        },
        {
            number: 5,
            icon: "✅",
            title: "Sales Staff Approve",
            description: "Sales staff review và approve order",
            details: {
                title: "Chi tiết:",
                items: [
                    "Review order details",
                    "Approve hoặc reject",
                    "Update status = 'Approved'"
                ]
            }
        },
        {
            number: 6,
            icon: "🧾",
            title: "Generate Proforma Invoice",
            description: "Tự động tạo Proforma Invoice",
            details: {
                title: "Chi tiết:",
                items: [
                    "Generate PDF invoice",
                    "Send to đại lý qua email",
                    "Available in portal"
                ]
            }
        }
    ]
};
```

---

## Tips & Tricks

### 1. Giữ Steps Ngắn Gọn
- Mỗi step: 1-2 câu description
- Details: 3-5 bullet points
- Tổng số steps: 5-8 steps (không quá nhiều)

### 2. Dùng Icons Rõ Ràng
- Chọn icons dễ hiểu (🛒, 📦, ✅, ❌)
- Tránh icons quá abstract

### 3. Language
- Dùng ngôn ngữ business (không dùng tech jargon)
- Ví dụ: "Tự động tạo order" thay vì "Call Kintone API"

### 4. Focus Vào Value
- Highlight lợi ích cho khách hàng
- Ví dụ: "Nhân viên không cần nhập manual"

### 5. Test Trước Khi Gửi
- Mở file và test workflow
- Đảm bảo tất cả steps hiển thị đúng
- Check spelling và grammar

---

## Cách Share Với Khách Hàng

### Option 1: Gửi File HTML
- Attach file HTML trong email
- Khách mở bằng browser
- Không cần internet (sau khi download)

### Option 2: Host Online (Nếu Có)
- Upload lên GitHub Pages (free)
- Hoặc web server
- Gửi link cho khách

### Option 3: Convert to PDF
- Mở HTML trong browser
- Print to PDF
- Gửi PDF (nhưng mất tính interactive)

---

## Advanced Customization

### Thay Đổi Colors

Tìm section CSS (dòng ~50):

```css
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
```

Thay đổi màu:
- `#667eea` = Màu chính
- `#764ba2` = Màu phụ

### Thay Đổi Speed Auto Play

Tìm dòng này:

```javascript
}, 3000); // 3 seconds per step
```

Thay đổi `3000` (milliseconds):
- `2000` = 2 giây (nhanh hơn)
- `5000` = 5 giây (chậm hơn)

### Thêm Animation

Có thể thêm CSS animations trong `<style>` section.

---

## Quick Start Checklist

- [ ] Mở file `workflow-demo-template.html`
- [ ] Thay đổi `workflowData.title`
- [ ] Customize `workflowData.steps` cho Epic của bạn
- [ ] Test workflow (click Next Step)
- [ ] Test Auto Play
- [ ] Save file với tên mới (ví dụ: `epic-2-workflow-demo.html`)
- [ ] Gửi cho khách hàng

---

**Time Required:** 30-60 phút để customize cho mỗi Epic  
**Difficulty:** Dễ (chỉ cần edit text)  
**No Coding Required:** Chỉ cần copy/paste và edit text

