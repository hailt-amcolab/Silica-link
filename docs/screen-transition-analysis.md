# Phân Tích Feedback: Screen Transition & Actual Screen Movement

## Vấn Đề Sếp Đề Cập

Từ feedback, sếp muốn thể hiện:

1. **"Screen Transition"** - Chuyển đổi màn hình
   - Cách màn hình này chuyển sang màn hình khác
   - Hiệu ứng chuyển đổi (slide, fade, zoom)
   - Flow giữa các screens

2. **"Actual Screen Movement"** - Di chuyển màn hình thực tế
   - Thể hiện user di chuyển từ screen A → screen B
   - Không chỉ là list steps, mà là **flow diagram** với screens
   - Visual connection giữa các screens

3. **"BtoB context khó thể hiện"**
   - B2B workflows phức tạp hơn B2C
   - Nhiều screens, nhiều decision points
   - Cần thể hiện rõ flow path

4. **"Figma không thể hiện được"**
   - Figma chỉ có static screens
   - Không thể hiện được transition animation
   - Không thể hiện được flow/connection giữa screens

---

## Phương Pháp Sếp Có Thể Muốn

### Option 1: **Screen Flow Diagram với Transitions**

Thể hiện như một **flowchart** với:
- Mỗi node = một screen (screenshot)
- Arrows = transition path
- Animation khi click vào arrow → screen slide/fade transition

```
[Screen 1: Shopify Order] 
    ↓ (click arrow)
[Screen 2: Kintone Order App]
    ↓ (click arrow)
[Screen 3: Inventory Check]
```

### Option 2: **Interactive Screen Transitions**

Thể hiện như một **carousel/slideshow** với:
- Screens hiển thị full-screen hoặc large
- Transition animation giữa screens (slide left/right, fade)
- Visual indicator cho flow path
- Back/Next buttons để navigate

### Option 3: **User Journey Map với Screens**

Kết hợp:
- **Flow diagram** (top) - thể hiện path
- **Screen previews** (bottom) - screenshots tương ứng
- Click vào flow node → screen transition animation

### Option 4: **Screen State Changes**

Thể hiện:
- Cùng một screen nhưng có **state changes**
- Ví dụ: Click button → Form xuất hiện (overlay, modal)
- Before/After của cùng screen

---

## Đề Xuất: Hybrid Approach

Kết hợp **Flow Diagram + Screen Transitions**:

### Layout:
```
┌─────────────────────────────────────┐
│  Flow Diagram (Top)                  │
│  [Screen1] → [Screen2] → [Screen3]  │
└─────────────────────────────────────┘
         ↓ (click node)
┌─────────────────────────────────────┐
│  Screen Preview (Large)             │
│  [Screenshot với transition effect]  │
└─────────────────────────────────────┘
```

### Features:
1. **Flow diagram** ở trên - thể hiện path
2. **Screen preview** ở dưới - hiển thị screen tương ứng
3. **Transition animation** khi chuyển screen (slide/fade)
4. **Visual connection** - arrows giữa các nodes
5. **Interactive** - click vào flow node → transition đến screen

---

## Implementation Options

### A. **Simple: Screen Carousel với Flow Indicator**
- Screens hiển thị như carousel
- Flow indicator ở trên/bên cạnh
- Next/Prev buttons
- Transition: slide hoặc fade

### B. **Medium: Flow Diagram + Screen Preview**
- Flow diagram interactive ở top
- Screen preview lớn ở center
- Click flow node → screen transition
- Arrows thể hiện path

### C. **Advanced: Full Screen Transitions**
- Mỗi screen full-screen hoặc large
- Transition animation mượt (slide, fade, zoom)
- Flow path overlay
- Navigation controls

---

## Recommendation

**Option B: Flow Diagram + Screen Preview** vì:
- ✅ Thể hiện được flow path (quan trọng cho B2B)
- ✅ Thể hiện được screen transitions
- ✅ Không quá phức tạp
- ✅ Dễ customize cho từng Epic
- ✅ Visual và interactive

---

## Next Steps

1. **Thêm Flow Diagram** vào HTML template
2. **Thêm Screen Transition Animation** (slide/fade)
3. **Kết nối Flow Nodes với Screens** (click node → show screen)
4. **Thêm Visual Arrows** giữa nodes
5. **Test với Epic 2** (Order Processing)

