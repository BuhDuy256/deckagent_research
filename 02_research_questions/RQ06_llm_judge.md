# RQ06 — LLM-as-Judge

**Trạng thái: READY (Wave 1).** Trả lời trục HOW trong bộ 3 câu hỏi nền tảng (RQ01=WHAT, RQ06=HOW, RQ08=AGAINST WHAT) — xem `05_decisions/DECISION_LOG.md` D-002.

**Câu hỏi:** Có nên dùng MLLM-as-judge cho các dimension khó đo deterministic, và reliability của nó được validate với human judgment thế nào?

## Việc đầu tiên: verify "PresentBench"

**PROJECT SOURCE:** Architecture doc §8 đề xuất MLLM-as-judge "theo hướng PresentBench". Đây là tên riêng cụ thể, **UNVERIFIED ENTITY** — chưa xác nhận tồn tại hay đề xuất gì (xem `05_decisions/OPEN_QUESTIONS.md` Q-005). Việc đầu tiên khi bắt đầu RQ06: search verify tên chính xác/tác giả/nội dung. Nếu verify được và nó thật sự liên quan tới dimension khác (RQ01/RQ02/RQ04/RQ07), route evidence sang đúng RQ đó thay vì giữ hết trong RQ06.

## 3 sub-question bắt buộc (từ audit RQ00 + task brief `03-research-evaluation.pdf`)

1. **Bias mitigation cho self-preference.** §8 tự nhắc: *"nêu rõ hạn chế self-preference bias của MLLM-judge (nên dùng model khác họ để generate và để judge)"*. Cần research: self-preference bias trong LLM-as-judge là gì, cách đo, cách giảm — không chỉ nêu tên bias mà không có phương pháp giảm cụ thể.
2. **Checklist theo từng instance vs. rubric chung — ưu nhược.** Task brief hỏi trực tiếp câu này. §8 gợi ý hướng "checklist chi tiết riêng cho từng input" (per-instance) nhưng chưa so sánh với rubric chung áp dụng toàn bộ.
3. **NFR-52/NFR-53 là candidate cần đưa vào landscape comparison, KHÔNG phải preferred method mặc định.** NFR đã gợi ý cụ thể: design consistency = `% element dùng đúng token` hoặc checklist MLLM-judge; content fidelity = checklist theo instance + human correlation trên mẫu con. RQ06 vẫn phải research toàn bộ landscape từ đầu (what methods exist → what do they measure → where do candidates nội bộ fit → sufficient hay không → thiếu gì), rồi mới xếp 2 candidate này vào so sánh — không lấy candidate nội bộ rồi đi tìm paper ủng hộ nó (confirmation research).
4. **Cỡ mẫu human để tin tương quan judge-người.** Task brief hỏi trực tiếp: "cần bao nhiêu mẫu human để tin tương quan judge-người (dù ước lượng)?" — gắn trực tiếp với NFR-53.

## Liên quan

- RQ00 — hoàn tất, xem `03_research/current_evaluation_audit.md`
- RQ02, RQ04, RQ05 (Wave 2 — các dimension có khả năng cần judge scoring, blocked cho tới khi RQ06 xong)
- `03_research/judges/`
- `03_research/human_eval/`
- `04_evidence/EVIDENCE_POLICY.md` (đặc biệt quan trọng — nhiều claim về LLM-judge reliability chỉ có giá trị nếu trace được về paper gốc)
- `05_decisions/OPEN_QUESTIONS.md` Q-005, Q-007

## Findings

_(chưa có)_
