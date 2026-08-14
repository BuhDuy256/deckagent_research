# Prompt: Synthesize Research thành Proposal

Dùng khi một RQ đã tích lũy đủ evidence/finding trong 03_research/ và 04_evidence/ để distill thành một đề xuất cụ thể.

**Quan trọng — workflow bắt buộc:**

```
Research → Evidence → Comparison → Proposed Decision → Human/Team review → Accepted Decision → Design update
```

Research **không được tự động trở thành design**. "Đủ evidence để research" không đồng nghĩa "team đã accept quyết định". Prompt này KHÔNG được tự sửa `06_design/` và KHÔNG được tự ghi vào `05_decisions/DECISION_LOG.md`.

```
Bạn đang synthesize research thành một proposed decision cho dự án evaluation của Deck Agent.

RQ: <RQ0x, kèm mục Findings hiện tại>

Đọc toàn bộ finding và evidence được link từ RQ này. Tạo ra:
- Một câu trả lời cụ thể, implementable (không phải survey) — formula, procedure, hoặc spec entry
- Tradeoff đang được chấp nhận, nêu rõ ràng
- Điều gì vẫn còn open hoặc bị defer, và vì sao
- Phân loại rõ: PROJECT SOURCE / EXTERNAL EVIDENCE / INFERENCE / RECOMMENDATION (xem AGENTS.md)

Ghi kết quả vào 05_decisions/OPEN_QUESTIONS.md dưới dạng một proposal mới (KHÔNG sửa 06_design/, KHÔNG update RQ Status thành "Answered" một cách tự động — chỉ đề xuất). Chỉ khi user/team explicitly nói một decision đã được accept, mới promote nó vào 06_design/ và log vào 05_decisions/DECISION_LOG.md.
```
