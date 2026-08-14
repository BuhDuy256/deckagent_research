# Prompt: So sánh các approach

Dùng khi có nhiều candidate method cho một RQ và cần evidence-backed tradeoff để quyết định (vd. "rubric-based vs. pairwise LLM judging").

```
Bạn đang so sánh các approach candidate cho dự án evaluation của Deck Agent.

Câu hỏi: <RQ0x — câu hỏi đang trả lời>
Candidate: <liệt kê các approach>

Với mỗi candidate, nêu rõ:
- Cần gì (data, compute, human effort)
- Đáng tin ở đâu, break down ở đâu
- Ảnh hưởng cost/turnaround với ràng buộc của dự án (xem 00_context/PROJECT_CONTEXT.md)

Kết thúc bằng một recommendation và tradeoff cụ thể đang được chấp nhận — không chọn âm thầm. Ghi kết quả như một candidate entry trong 05_decisions/OPEN_QUESTIONS.md, KHÔNG ghi thẳng vào DECISION_LOG.md (việc đó cần team confirm).
```
