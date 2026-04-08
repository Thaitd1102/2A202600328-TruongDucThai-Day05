# UX Exercise - Analysis

## 4 Paths Design Thinking

### 1. Happy Path — AI đúng
- **Test Case**: "VN123 mấy giờ bay?"
- **Điểm tốt**: Bot xác nhận lại thông tin trước khi tra cứu ("Bạn hỏi chuyến VN123, ngày hôm nay?") → Tăng Precision, tránh nhầm lẫn ngày giờ cho khách.
- **Flow**: User hỏi → Bot xác nhận → Bot tra cứu → Hiển thị kết quả chính xác + Nút [Đặt vé]
- **Kết thúc**: Conversion (khách đặt vé hoặc lưu itinerary)

### 2. Low-confidence Path — AI không chắc
- **Test Case**: "Tôi bị mất đồ"
- **Lỗ hổng**: Bot không hỏi clarifying questions → Trả về 1 trang dài (5-6 bước) kèm link web → **Information Overload**.
- **Hậu quả**: User mất thời gian tự lọc, bỏ cuộc gọi tổng đài, hoặc phàn nàn.
- **Detection**: System nhận diện nhu cầu mơ hồ (từ khóa "mất", "thất lạc")
- **Recovery**: Bot hỏi ngược: "Bạn mất ở đâu?" + 2 nút: [Tại sân bay] | [Trên máy bay] → **Interactive Slot-filling** → Thu hẹp phạm vi ngay.

### 3. Failure Path — AI sai (Hallucination)
- **Test Case**: "Thủ tục mang hổ con lên máy bay"
- **Lỗ hổng**: Bot nhầm "hổ" với thú cưng thông thường (chó/mèo) → Xác nhận sai quy định → **Hallucination + Overconfidence**.
- **Hậu quả**: Khách thực hiện theo gợi ý sai → Gây thảm họa an ninh bay, mất tín dụng hãng.
- **Detection**: User khẳng định lỗi ("Không, ấy là hổ con!")" hoặc gọi tổng đài phàn nàn.
- **Recovery**: Bot nhận diện từ khóa nhạy cảm (động vật hoang dã, vật cấm) → Báo: "Tôi không có thông tin chính xác về loài vật này" + Nút [Gọi CSKH] (Graceful fallback, không fake answer).

### 4. Exit Path — User confusion/frustration
- **Test Case**: User gõ "asjdkgh" hoặc "Vào lỗi rồi"
- **Lỗ hổng**: Thiếu **Fallback Mechanism** → Bot lặp lại máy móc, không nhận diện sự ức chế.
- **Hậu quả**: User chửi bới, delete app, hoặc chuyển sang competitor.
- **Detection**: Sau 2 lần bot không hiểu input → trigger fallback.
- **Recovery**: Tự động hiển thị **Suggested Topics Card** hoặc nút [Gặp CSKH] → Cung cấp "lối thoát" mượt mà, giảm frustration.

---

## Comparison: As-is (Current) vs To-be (Improved)

### 1. Xử lý "Information Overload" (Path 2)

| Aspect | As-is | To-be |
|--------|-------|-------|
| **Flow** | Trả danh sách 5-6 bước + link web | Hỏi clarifying question (Where? When?) + 2-3 button |
| **UX Pattern** | Static list (Passive) | Interactive slot-filling (Active) |
| **Outcome** | User tự lọc info → abandon | Bot hẹp phạm vi → Đúng nhu cầu → action |
| **Time to resolution** | 5-10 min (user tự search) | <1 min (guided) |

### 2. Xử lý "Hallucination" (Path 3)

| Aspect | As-is | To-be |
|--------|-------|-------|
| **Wrong answer** | Confident hallucination ("Pets allowed if caged") | N/A |
| **Safety** | High risk (xanh = dangerous) | Guardrails: Detect keywords + [Ask CSKH] |
| **Confidence UI** | Always high (fake confidence) | Show uncertainty: "Không chắc → Hỏi chuyên gia" |
| **Reputational** | Lose trust, regulatory issue | Transparent, safe, compliant |

### 3. Xử lý "Looping" (Path 4)

| Aspect | As-is | To-be |
|--------|-------|-------|
| **Error handling** | Infinite loop: "Tôi không hiểu, vui lòng nhập lại" | Graceful: After 2 fails → Fallback |
| **Fallback UI** | N/A | Suggested topics card + [Contact CSKH] |
| **User mood** | Frustrated, leaves | Satisfied, transitions smoothly |
| **Retention** | Lose customer | Keep engagement loop |

---

## Data Flywheel Strategy (Để AI tự học & cải tiến)

### Problem
Con bot sửa được lỗi hôm nay, nhưng ngày mai vẫn sai kiểu cũ.

### Solution: 3 Feedback Signals

| Signal Type | Collection Method | Example | Use case |
|-------------|------------------|---------|----------|
| **Implicit** | User behavior tracking | User xem quy định mất đồ → không gọi tổng đài | AI đã giải quyết ✓ |
| **Explicit** | 👍/👎 button + Rating | "Câu trả lời này hữu ích không?" | RLHF training |
| **Correction** | Form correction + User edit | Khách sửa info do AI điền sai (OCR error) | Fine-tune NLU/OCR model |

### Flywheel Loop

```
Nhiều user dùng bot
    ↓
Nhiều case "Hổ con" → detect pattern
    ↓
Correction signals: Khách sửa lỗi AI → Dữ liệu vàng
    ↓
Retrain model weekly từ corrections
    ↓
AI tự động từ chối case uncertain (hallucinate less)
    ↓
User tin tưởng hơn → Dùng lại
    ↓
More data → Better model
```

### Key Metrics

| Metric | Current | Target | Owner |
|--------|---------|--------|-------|
| Hallucination rate | ? | < 2% | NLU team |
| Correction signal/week | ? | > 100 | Product |
| User satisfaction (4+ stars) | ? | ≥ 80% | Support |
| Fallback trigger rate | ? | < 5% (precision ok) | Analytics |
