# Rejected Approaches

Ghi lại các approach đã được xem xét và loại bỏ **sau khi đã research + có decision**, để không bị đề xuất lại và tranh luận lại vô ích. **Không được reject một approach chỉ vì hiện tại cảm thấy nó không tốt** — nếu chưa có research + decision record, item đó thuộc về `OPEN_QUESTIONS.md`, không phải file này.

## Format

```
## R-001 — <tên ngắn>
Ngày: YYYY-MM-DD
Approach: <cái gì đã được xem xét>
Vì sao bị reject: <lý do, phải trace được về evidence/decision>
Decision liên quan: <link tới DECISION_LOG.md>
Xem lại nếu: <điều kiện khiến approach này đáng cân nhắc lại>
```

---

## R-001 — Dùng "judge đạt ~80% human ceiling" làm threshold pass/fail

Ngày: 2026-08-14
Approach: RQ06 suy từ PresentBench (judge–human ρ≈0.532, human–human ≈0.664 → tỷ lệ ~80%) và gợi ý lấy ~80% human ceiling làm mức "đủ tin" cho judge của Deck Agent.
Vì sao bị reject: Đây là **heuristic suy ra từ một setup cụ thể, không phải threshold đã được literature establish**. Rủi ro cụ thể: sau này viết vào báo cáo "industry accepted threshold is 80%" — sai, và không defensible khi bị hỏi. RQ06 cũng tự cảnh báo chuẩn agreement chuẩn mực có thể không áp dụng được cho domain này (PPTEval đo κ≈0.59 *giữa người với người*, dưới cả ngưỡng tentative).
Decision liên quan: Wave 1 Decision Gate G-08 (`03_research/WAVE1_SYNTHESIS.md`)
**Giữ lại phần nào:** con số vẫn dùng được như **reference point để hiểu scale** của độ tin cậy judge trong domain này. Chỉ bị reject ở vai trò pass/fail criterion.
Xem lại nếu: RQ07 + pilot data cho Deck Agent tạo ra cơ sở riêng để đặt ngưỡng.

## R-002 — Dùng baseline B-1 để claim "ContentPlanner + Deck IR architecture có đáng độ phức tạp"

Ngày: 2026-08-14
Approach: RQ08 định nghĩa B-1 = `source → single-shot LLM → Deck IR → same exporter` và phát biểu nó trả lời câu hỏi "kiến trúc ContentPlanner + Deck IR có đáng không?".
Vì sao bị reject: **Lỗi logic.** B-1 vẫn dùng Deck IR ở cả hai nhánh — nó so `source → ContentPlanner → Deck IR` với `source → one LLM call → Deck IR`. Tức nó trả lời "**tách riêng planning stage có đáng không?**", KHÔNG trả lời "Deck IR có đáng không?". Muốn claim về Deck IR cần experiment khác (IR-first pipeline vs direct generation pipeline), và experiment đó khó làm fair hơn đáng kể.
Decision liên quan: Wave 1 Decision Gate G-11 (`03_research/WAVE1_SYNTHESIS.md`)
**Giữ lại phần nào:** bản thân baseline B-1 vẫn hợp lệ và hữu ích — chỉ **wording và phạm vi claim** bị reject. Đổi tên thành **"Single-shot planning baseline"**, claim thu hẹp về "giá trị của việc tách riêng planning stage".
Xem lại nếu: team thiết kế được một experiment fair riêng cho câu hỏi về Deck IR.
