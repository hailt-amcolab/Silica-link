# **0. Mục tiêu của Risk Management Kickoff**

“Kickoff tốt không phải là khi mọi người hiểu kế hoạch, mà là khi

**mọi rủi ro đã được lường trước và có người chịu trách nhiệm.**

# 1. Nhận diện Risk

## **1️⃣ Định nghĩa chính xác và dễ hiểu nhất**

> Risk là khả năng xảy ra một sự kiện trong tương lai, có thể gây ảnh hưởng tiêu cực đến mục tiêu của dự án.
> 

### **✅ Gồm 3 yếu tố:**

1. **Chưa xảy ra (Future event)** – nếu nó đã xảy ra → đó là *Issue*.
2. **Có khả năng xảy ra (Probability)** – không chắc chắn 100%.
3. **Nếu xảy ra → gây tác động tiêu cực (Impact)** – đến Scope / Timeline / Quality / Cost.

## **2️⃣ Phân biệt rõ Risk vs Issue vs Assumption vs Problem**

| **Loại** | **Thời điểm** | **Trạng thái** | **Cần làm gì** |
| --- | --- | --- | --- |
| 🟢 **Risk** | Chưa xảy ra | Có thể xảy ra | Chuẩn bị trước (Mitigation) |
| 🔴 **Issue** | Đã xảy ra | Đang ảnh hưởng | Giải quyết hậu quả (Resolution) |
| 🔵 **Assumption** | Là **một giả định được dùng để lập kế hoạch**,nếu giả định đó **sai**, nó **có thể biến thành Risk.** | Không tiêu cực ngay | Assumption phải được **ghi rõ trong kickoff**,và **được kiểm chứng định kỳ** (nếu sai → chuyển thành Risk ngay). |
| ⚫ **Problem** | Lỗi hiện hữu trong process | Đang lặp lại | Cần corrective action |

Assumption sai
↓
Risk xảy ra (realized)
↓
→ trở thành Issue (sự cố)
↓
→ phân tích nguyên nhân gốc
↓
→ xác định Problem (vấn đề hệ thống)

## **3️⃣ Cấu trúc của một Risk tốt (SMART RISK)**

| **Thành phần** | **Câu hỏi kiểm tra** | **Ví dụ Amcolab** |
| --- | --- | --- |
| **Situation (Hiện tượng)** | Có dấu hiệu gì thực tế? | QA đang log >120%, test chưa bắt đầu regression |
| **Risk Event (Điều có thể xảy ra)** | Nếu tiếp tục như vậy, chuyện gì sẽ xảy ra? | Regression test không kịp, release trễ |
| **Impact (Ảnh hưởng)** | Sẽ ảnh hưởng tới cái gì (S/T/Q/C)? | Timeline + Quality |
| **Owner** | Ai chịu trách nhiệm chính để theo dõi? | PM |
| **Mitigation** | Làm gì để giảm nguy cơ? | Assign thêm QA backup, chia test critical trước |

→ PM không được chỉ ghi “QA bận”, mà phải ghi:

> “QA log 120%, nguy cơ regression không kịp trước release → ảnh hưởng timeline và quality.”
> 

## **4️⃣ Ví dụ cụ thể: Risk thật vs không phải Risk**

| **Câu PM hay ghi** | **Có phải Risk?** | **Giải thích** |
| --- | --- | --- |
| “QA bận” | ❌ Không | Đó là hiện tượng – chưa gắn hậu quả. |
| “QA bận → regression có thể không xong trước release” | ✅ Có | Chưa xảy ra, có khả năng, có hậu quả. |
| “Vendor UI trễ” | ❌ Issue nếu đã trễ | Nếu mock chưa về thì là issue, nếu *nguy cơ* trễ → risk. |
| “Spec còn chưa confirm” | ❌ Chưa đủ | Phải là “Spec chưa confirm → dev có thể bị rework → ảnh hưởng timeline.” |
| “Server AWS lỗi hôm qua” | ❌ Không | Đó là Issue – đã xảy ra rồi. |
| “PO confirm chậm → nguy cơ backlog không kịp test tuần này” | ✅ Có | Có điều kiện, có hậu quả, chưa xảy ra. |

## **5️⃣ Phân loại Risk theo nhóm**

| **Nhóm Risk** | **Mô tả** | **Ví dụ Amcolab** |
| --- | --- | --- |
| **Scope Risk** | Phạm vi thay đổi, mơ hồ | “PO confirm chậm → CR nhiều” |
| **Schedule Risk** | Tiến độ chậm, task chồng | “Vendor giao UI trễ” |
| **Quality Risk** | Test thiếu, leak bug | “QA bận → regression không đủ” |
| **Resource Risk** | Thiếu nhân lực, dev nghỉ | “Dev chính nghỉ phép giữa sprint” |
| **Technical Risk** | Công nghệ, API, performance | “API đối tác không ổn định” |
| **Communication Risk** | Feedback delay, khác múi giờ | “Khách không online cuối tuần” |

# **2. Quản trị Risk (thực tế AMCOLAB)**

**Phần lớn PM dừng lại ngay sau khi “ghi risk vào bảng”**,

tức là mới chỉ *nhận thức được nguy cơ*, chứ **chưa thật sự quản trị**.

→ Giống như phát hiện “ổ gà trên đường” nhưng không làm gì để tránh, vá hay cảnh báo.

Tóm tắt chu trình quản trị Risk

### **1️⃣ Identify Risks — Nhận diện rủi ro**

PM liệt kê mọi nguy cơ *có thể* ảnh hưởng tới Scope / Time / Quality / Cost.

✅ Output: danh sách risk có nguyên nhân – sự kiện – hậu quả.

Ví dụ:

> “QA đang log 120%, có khả năng regression không xong trước release, ảnh hưởng timeline.”
> 

📍Trạng thái trên Dona: New Risk

---

### **2️⃣ Analyze Risks — Phân tích rủi ro**

Đây là bước *đa số PM bỏ qua*.

Mục tiêu: **xác định mức độ nghiêm trọng (Risk Score)** để biết risk nào ưu tiên xử lý.

### **⚙️ Cách làm:**

1. Xác định **Xác suất xảy ra (Probability)**:
    - Cao (3), Trung bình (2), Thấp (1)
2. Xác định **Mức độ ảnh hưởng (Impact)**:
    - Lớn (3), Trung bình (2), Nhỏ (1)
3. Tính:
    
    > Risk Score = Probability × Impact
    > 

| **isk** | **P** | **I** | **Score** | **Level** |
| --- | --- | --- | --- | --- |
| QA bận → release chậm | 3 | 3 | 9 | 🔴 High |
| PO confirm chậm | 2 | 3 | 6 | 🟠 Medium |
| Dev mới → bug | 2 | 2 | 4 | 🟡 Low |

### **3️⃣ Plan Mitigation — Lập kế hoạch đối phó**

Bước này PM cần viết rõ:

- **Chiến lược:** Avoid / Reduce / Transfer / Accept
- **Hành động cụ thể:** Làm gì, ai làm, khi nào, trigger là gì

✅ Output: Mitigation plan rõ ràng (actionable).

Ví dụ:

> “Assign QA backup 0.5 tuần khi coverage <60%.”
> 

📍Trạng thái Dona: Mitigation Planned

---

### **4️⃣ Assign Owner & Trigger — Phân công và thiết lập cảnh báo**

- Gán **Owner** chịu trách nhiệm theo dõi risk.
- Đặt **Trigger condition** để biết khi nào cần hành động.
- Nếu có thể → thiết lập **auto-reminder** (Slack / Notion / Dona alert).

Ví dụ:

> “Trigger: coverage <60% khi còn 5 ngày”
> 

> “Owner: PMO”
> 

📍Trạng thái Dona: Assigned

---

### **5️⃣ Monitor & Review — Theo dõi và cập nhật định kỳ**

PM phải theo dõi **ít nhất mỗi tuần một lần**,

đặc biệt trong Daily/Weekly meeting.

### **6️⃣ Control & Update — Kiểm soát và cải tiến**

Sau khi risk được xử lý:

- Ghi nhận **hiệu quả mitigation** (hiệu quả, không hiệu quả, một phần).
- Nếu risk trở thành issue thật → ghi vào “Lesson Learned”.
- Nếu mitigation hiệu quả → thêm vào “Risk Library” để dùng cho các dự án sau.

📍Trạng thái Dona: Closed

📗 Output: Risk Register cập nhật + Lesson Learned.

# **3. MITIGATION PLAN = KẾ HOẠCH GIẢM ẢNH HƯỞNG RỦI RO**

## **🧩 1️⃣ Mitigation Plan là gì?**

> Là kế hoạch cụ thể, có người, có hành động, có điều kiện kích hoạt, có thời gian rõ ràng để giảm xác suất hoặc giảm tác động của rủi ro.
> 

🧠 Khác biệt:

- “Mitigation” ≠ “Cố gắng để không xảy ra”.
- “Mitigation Plan” = “Làm gì, ai làm, khi nào, đo bằng gì”.

## **2️⃣ 5 Thành phần của 1 Mitigation Plan hoàn chỉnh**

| **hành phần** | **Câu hỏi cần trả lời** | **Ví dụ Amcolab** |
| --- | --- | --- |
| 🎯 **Action** | Cụ thể làm gì để giảm rủi ro? | “Assign QA backup”, “Auto Slack reminder sau 48h”, “BA tạo wireframe tạm” |
| 👤 **Owner** | Ai chịu trách nhiệm thực thi? | “PMO”, “BA”, “PM” |
| 🕒 **Timing / Trigger** | Khi nào cần làm? Kích hoạt khi nào? | “Khi QA log >120%”, “Mock chưa về T–7” |
| 📏 **Expected Result / Metric** | Làm xong thì risk giảm thế nào? | “Regression test 70% coverage”, “Không trễ release” |
| 📅 **Deadline / Review** | Thời điểm review hiệu quả | “Check lại mỗi tuần”, “Trước T–3” |

## **3️⃣**Hiện trạng Mitigation Plan ở AMCOLAB

## **Dự án 1: LOOVIC – Flutter + CMS**

**Risk:** Vendor UI/UX giao design chậm → dev không thể bắt đầu.

| **Loại** | **Nội dung thực tế PM ghi** | **Vấn đề** | **Mitigation đúng** |
| --- | --- | --- | --- |
| ⚠️ Risk | “Vendor đang bận, có thể trễ design. Sẽ cố gắng nhắc vendor sớm.” | Không có hành động cụ thể, không có trigger, không ai chịu trách nhiệm. | “Nếu mock chưa về trước T–7, PM assign BA tạo wireframe tạm để dev start layout. PM nhắc vendor trước T–10. PMO review lại resource vendor nếu trễ 2 lần.” |

🧠 **Phân tích:**

- Câu “sẽ cố gắng nhắc vendor” không thể đo.
- Khi trễ thật → vẫn trễ, không ai có cơ chế phản ứng.
    
    → PM cần hiểu mitigation phải “đo được, có deadline, có người follow”.
    

## **Dự án 2: MatchHub Admin – React + Supabase**

**Risk:** QA đang share 4 dự án, test không kịp regression.

| **Loại** | **Nội dung PM ghi** | **Vấn đề** | **Mitigation đúng** |
| --- | --- | --- | --- |
| ⚠️ Risk | “QA bận, sẽ cố gắng test sớm hơn, push task cho dev fix nhanh.” | Mơ hồ, không đo được, không có owner cụ thể. | “QA đang log 125% → PMO assign backup QA 0.5 tuần. PM yêu cầu test coverage report mỗi 3 ngày. Nếu coverage <60% khi còn 5 ngày, escalate BOM.” |

🧠 **Phân tích:**

- Câu “sẽ cố gắng test sớm hơn” không giải quyết nguyên nhân (resource thiếu).
- Mitigation tốt phải:
    
    ① đo coverage,
    
    ② hành động cụ thể (add resource),
    
    ③ có điều kiện trigger (coverage <60%).
    

## **Dự án 3: AI Order – LINE + POS Integration**

**Risk:** API nhà hàng có thể lỗi, ảnh hưởng release.

| **Loại** | **Nội dung PM ghi** | **Vấn đề** | **Mitigation đúng** |
| --- | --- | --- | --- |
| ⚠️ Risk | “API có thể lỗi, sẽ test kỹ trước release.” | Vô nghĩa – test kỹ không ngăn lỗi API đối tác. | “Mock API fallback trước release. Nếu đối tác không confirm health check 3 ngày trước release → tạm dùng stub để release đúng hạn.” |

🧠 **Phân tích:**

- “Test kỹ” không giảm xác suất rủi ro.
- “Mock fallback” **giảm ảnh hưởng** → đúng tinh thần mitigation.

## **5️⃣ Các chiến lược Mitigation (Risk Response Strategy)**

| **Chiến lược** | **Khi nào dùng** | **Ví dụ Amcolab** |
| --- | --- | --- |
| 🟢 **Avoid** | Khi có thể loại bỏ nguyên nhân gốc | Không bắt đầu dev khi chưa confirm spec |
| 🟠 **Reduce** | Khi có thể giảm xác suất hoặc giảm tác động | Giao test critical path trước, chia QA backup |
| 🟣 **Transfer** | Khi risk thuộc bên khác, mình không kiểm soát | Yêu cầu vendor ký cam kết, chuyển sang hợp đồng |
| ⚫ **Accept** | Khi cost mitigation > cost risk | Chấp nhận minor UI bug bản beta, log vào known issue |

# **4. Vì sao PM thường chọn “tránh” trước?**

Bạn sẽ thấy ở đâu đó, PM phản ứng 

- Load dev non vào thì risk → Không load
- Dự án kĩ thuật rủi ro cao → Không nhận
- Dự án deadline gấp → Từ chối

Đây là Avoid ở cấp Quản trị (PMO), cấp công ty 

C**ách chọn cấp độ Mitigation phù hợp**

| **Tình huống** | **Hành động nên chọn** |
| --- | --- |
| Dự án khách hàng lớn, deadline gắt | **Avoid** (chưa đủ lực thì không dùng fresher) |
| Dự án internal, có thời gian training | **Reduce** (mentoring, pair programming) |
| Dự án outsource, nhiều module phức tạp | **Transfer** (giao critical task cho senior, hoặc vendor) |
| Dự án nhỏ, non-critical, khách linh hoạt | **Accept** (cho thử sức, nhưng monitor) |

**“bản năng sinh tồn” của PM mới lên cấp quản lý** — họ phản ứng đầu tiên bằng **“tránh né” (avoid)**, chứ không phải **“quản trị” (control hoặc reduce)**.

Nó là **tâm lý phổ biến nhưng cực nguy hiểm**, vì tuy trông như giảm rủi ro, nhưng thực ra là **đẩy rủi ro sang người khác hoặc làm tê liệt hệ thống.**

### **⚙️ 1.1 Tâm lý sợ trách nhiệm**

“Nếu mình tránh được việc đó, thì không thể bị đổ lỗi.”

- Họ chưa quen chịu áp lực từ việc **ra quyết định trong vùng rủi ro.**
- Họ nghĩ **tránh = an toàn**, nhưng thực ra là **bỏ cơ hội cải thiện.**

### **⚙️ 1.2 Thiếu kỹ năng “giảm thiểu” (reduce)**

“Em biết risk đó, nhưng không biết phải làm gì ngoài né.”

- PM nhận diện được vấn đề, nhưng **không biết cách kiểm soát yếu tố ảnh hưởng.**
- Vì vậy chọn “avoid” cho nhanh — **đỡ phải design mitigation phức tạp.**

---

### **⚙️ 1.3 Bị ảnh hưởng bởi tư duy dev**

“Bug tránh được là bug tốt.”

→ Khi chuyển sang PM, vẫn áp dụng logic “tránh lỗi” vào con người, tiến độ, communication.

---

### **⚙️ 1.4 Thiếu empowerment hoặc sợ bị đánh giá**

“Nếu tôi nói mitigation là ‘add QA’, thì PMO nghĩ tôi yếu.”

→ Họ tránh vì **sợ người khác nghĩ mình không kiểm soát được.**

### **Thoát khỏi tư duy “avoid-first”**

“Avoid không miễn phí.

Khi bạn tránh, bạn đang trả giá bằng một cơ hội.”

### **Kỹ năng “Reduce by design”**

> PM phải biết
> 
> 
> **thiết kế quy trình để risk yếu đi**
> 

Ví dụ:

| **Risk** | **Thay vì Avoid** | **Cách Reduce thực chiến** |
| --- | --- | --- |
| QA bận | Không test phần đó | Test critical path, chia ca test, add QA backup |
| PO confirm chậm | Chờ khách | Auto remind, confirm tạm qua chat, freeze scope 3 ngày |
| Dev mới | Không giao task khó | Pair programming, review checklist, task nhỏ |
| Spec mơ hồ | Không code khi chưa confirm | Dựng wireframe, define assumption rõ |

### **Tư duy “học từ risk” thay vì “né risk”**

Risk xuất hiện là tín hiệu, không phải tai họa.

Nếu risk lặp lại → hệ thống có lỗi.

Nếu risk biến mất → bạn vừa cải thiện hệ thống.

👉 PM viết:

“Tôi không tránh risk, tôi dùng nó để phát hiện chỗ yếu.”

### **Chuyển mindset từ “tránh trách nhiệm” → “chia sẻ trách nhiệm”**

PM cần hiểu:

- Mitigation không phải “làm một mình”, mà là **điều phối hành động trong hệ thống.**
- Bạn không phải **chịu lỗi**, mà là **chịu trách nhiệm điều khiển.**

“Avoid là tránh/trốn.

Reduce là điều phối.

Transfer là thương lượng.

Accept là lựa chọn.”

```bnf
Risk identified
│
├─> Có thể loại bỏ nguyên nhân gốc không?
│       │
│       ├─> YES → Avoid (điều chỉnh scope / resource / plan)
│       └─> NO
│
├─> Có thể làm giảm xác suất hoặc tác động không?
│       │
│       ├─> YES → Reduce (thêm QA, mentor, automation, v.v.)
│       └─> NO
│
├─> Có thể chuyển rủi ro cho bên khác không?
│       │
│       ├─> YES → Transfer (vendor, PO, hợp đồng)
│       └─> NO
│
└─> Accept (chấp nhận, monitor & buffer)
```

👉 Tức là:

- Bạn **luôn bắt đầu từ “Avoid”** để xem có thể triệt nguyên nhân không.
- Nếu không khả thi (do chi phí, quyền hạn, thời gian),
    
    → bạn **chuyển xuống cấp kế tiếp – Reduce**.
    
- Nếu vẫn không kiểm soát được,
    
    → **Transfer** hoặc cuối cùng là **Accept**.
    

# 5. Chọn chiến lược kiểm soát rủi ro như thế nào

Cách loại bỏ nguyên nhân gốc (AVOID)
**1️⃣ Cần tách hai tầng “Avoid”**

| **Cấp độ** | **Ai quyết định** | **Mục tiêu** | **Ví dụ** |
| --- | --- | --- | --- |
| 🧱 **Operational Avoid** | PM / PMO | Loại bỏ nguyên nhân trong phạm vi dự án | “Làm POC trước khi start”, “Không code phần mình không hiểu” |
| 🏢 **Strategic Avoid** | CEO / BOM | Loại bỏ rủi ro nằm ngoài năng lực lõi của công ty | “Không nhận dự án ngoài năng lực chính”, “Không dùng tech stack chưa có kinh nghiệm” |

**🔍 Tổng hợp cả 3 tình huống**

| **Case** | **Risk Type** | **Strategy** | **Trigger** | **Action** | **Owner** |
| --- | --- | --- | --- | --- | --- |
| Khách feedback quá chi tiết | Scope Risk | Reduce + Transfer | >3 feedback/UI | Freeze scope, CR rule | PM |
| Team chưa quen WP | Technical Risk | Reduce | Trước sprint 1 | R&D + code review | PM + Tech lead |
| Khách non-tech | Communication Risk | Reduce + Transfer | Feedback >3 ngày | Confirm bằng wireframe | BA + PM |

Nhưng cần Tư duy AVOID trong phạm vi dự án 
**Mitigation dạng Avoid**

> Thiết lập rule feedback tối đa 3 vòng từ kickoff, confirm bằng form,baseline style guide được duyệt trước khi bắt đầu design.”
> 

**Hiệu quả quản trị (so sánh Avoid vs Reduce)**

| **Hướng xử lý** | **Cách làm** | **Kết quả** |
| --- | --- | --- |
| **Reduce (giảm thiểu)** | Giới hạn feedback sau khi phát sinh | Risk vẫn có thể xảy ra vài lần đầu, cần xử lý |
| **Avoid (loại bỏ)** | Thiết kế rule và baseline từ đầu | Risk gần như không xuất hiện vì không có điều kiện phát sinh |

Ví dụ tình huống:  Team c

hưa quen với WordPress, không chắc cách làm có đúng hay không → nguy cơ code sai, tốn effort, bug nhiều.

 **Vì sao PM thường nghĩ đến Reduce trước?**

| **Gốc rễ** | **Giải thích** |
| --- | --- |
| **1. Risk đã nằm trong phạm vi dự án rồi.** | PM thấy “Team đã được assign, dự án đã start”, nên mặc định phải xử lý *trong khuôn khổ* hiện có. |
| → Khi đó phản xạ là “giảm thiểu hậu quả” chứ không nghĩ tới “thay đổi điều kiện gốc”. |  |
| **2. PM không có quyền thay đổi input (team, tech).** | Ở nhiều công ty, PM không được phép đổi resource hoặc stack → họ nghĩ “Avoid = không khả thi”. |
| **3. Tư duy dev còn mạnh** | Dev nghĩ: “Mình phải làm dù khó, nên tốt nhất học và làm cẩn thận hơn” → tư duy này dẫn tới “Reduce bằng cố gắng”. |
| **4. Văn hóa phản ứng nhanh hơn là thiết kế lại.** | PM sợ trễ, nên chọn hướng “làm luôn rồi fix dần” → tránh phải dừng lại tái thiết kế. |

Nói cách khác:

> “Reduce” là phản xạ tự nhiên của người
> 
> 
> *chưa được empower để thay đổi hệ thống*
> 
> Còn “Avoid” là hành vi của người
> *dám tái cấu trúc hệ thống để rủi ro không tồn tại nữa.*
> 

## **🧩 2️⃣ Vậy, nếu nhìn theo tư duy**

## **Avoid thật sự, case này xử lý thế nào?**

> “Avoid” ở đây không có nghĩa “né làm WordPress”,
mà là **thiết kế lại điều kiện làm việc để risk không thể xuất hiện.**
> 

**Avoid chỉ triệt tiêu được phần “có thể thiết kế lại”.**

Còn phần “không thể kiểm soát” → ta phải **Reduce tiếp.**

Giả sử PM đã làm mọi thứ “đúng sách vở”:

- Kickoff: đã giới hạn feedback 3 vòng
- Có style guide baseline
- Có wireframe confirm trước
- Feedback chỉ qua form, có deadline confirm

👉 Nhưng sau 3 vòng, **khách vẫn feedback nhỏ giọt, yêu cầu “chút xíu”**,

và thực tế **effort vẫn tăng**.

**🧩 1️⃣ Giải thích tại sao Avoid “đúng” nhưng vẫn chưa đủ**

| **Lý do** | **Giải thích cụ thể** |
| --- | --- |
| **1. Không phải nguyên nhân nào cũng loại bỏ được** | Hành vi khách hàng thuộc yếu tố con người – “không thể kiểm soát tuyệt đối” bằng rule. |
| **2. Có rủi ro thứ cấp (secondary risk)** | Khi khách bị giới hạn feedback, họ phản ứng lại bằng việc feedback dồn cuối → vẫn tạo rework. |
| **3. Có khoảng “residual risk” (rủi ro còn sót lại)** | Dù loại bỏ phần lớn nguyên nhân, vẫn còn phần ảnh hưởng nhỏ tồn tại. |
| **4. Risk không tĩnh, mà biến động theo giai đoạn** | Lúc đầu khách hợp tác, nhưng về sau họ đổi người review hoặc có thêm stakeholder mới. |

> “Avoid khi có thể.
Reduce khi cần.
Transfer khi nên.
Accept khi đủ trưởng thành để chịu trách nhiệm.”
> 

Càng sớm → càng gần “Avoid”.
Càng trễ → càng phải “Reduce / Transfer / Accept”.



Phân tích risk
1. Phân tích theo 4 nhóm rủi ro chính

Đây là cách nhanh nhất và bao quát nhất.

a) Technical Risk

Công nghệ mới, chưa ai trong team rành

Hệ thống phức tạp / distributed

Tích hợp third-party chưa chắc ổn định (payment, AI, livestream…)

Performance, scaling, bảo mật

Giới hạn của nền tảng (iOS, Android, browser, infra)

b) Product / Requirement Risk

Requirement mơ hồ

Thay đổi liên tục

Không có spec rõ, thiếu acceptance criteria

Người quyết định (PO/client) không phản hồi kịp

Không có prototype, không có UX flow

c) Human / Team Risk

Thiếu nhân lực

Dev chưa đủ skill

Chuyển team, nghỉ việc

Communication kém

Không có người quyết định (decision maker)

Phụ thuộc vào 1 cá nhân (bus factor)

d) Schedule / Management Risk

Deadline không thực tế

Task ước lượng sai

Quản lý scope kém (scope creep)

Không có workflow rõ ràng

QA không đủ thời gian test

Blocker từ team khác

2. Dùng checklist nhận diện nhanh

Team dev thường dùng một checklist kiểu như này:

Technical

Có tech mới?

Có API chưa confirm?

Có phần cần POC trước?

Có dependency giữa module?

Có phần chưa được test môi trường production?

Product

AC rõ chưa?

Mockup đầy đủ chưa?

Flow có conflict không?

Business rule đã confirm chưa?

Team

Ai phụ trách module A? Có backup không?

Dev có kinh nghiệm đúng không?

Team đang overload không?

QA đủ người không?

Timeline

Có milestone khó khăn?

Có task critical bị phụ thuộc?

Ước lượng có nghi ngờ không?

Có nhiều release gấp không?

3. Rà theo từng module

Chia dự án thành nhiều phần:

Auth  
User  
Payment  
Livestream  
Notification  
Admin  
Infra  


Và đặt câu hỏi:

Module nào khó nhất?

Module nào chưa chắc chắn?

Module nào phụ thuộc nơi khác?

Module nào ảnh hưởng trực tiếp user?

Những chỗ đó gần như luôn chứa risk.

4. Dùng mô hình “Unknown-Unknown / Known-Unknown”

Đây là cách chuyên nghiệp hơn.

Known-Known:

Những thứ mình biết rõ → không phải risk.

Known-Unknown:

Những thứ mình biết chưa rõ (ví dụ API đối tác chưa confirm) → risk thật sự.

Unknown-Unknown:

Những thứ mình chưa biết là mình không biết → cần POC sớm để bóc ra.

Dev lead thường yêu cầu:

POC 1–3 ngày cho phần nghi ngờ

Sau POC sẽ lộ ra thêm risk