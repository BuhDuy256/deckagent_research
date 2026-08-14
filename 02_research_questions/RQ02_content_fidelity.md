# RQ02 — Content Fidelity

**Trạng thái: Wave 2 — Blocked by RQ01 + RQ06.**

**Câu hỏi:** Content fidelity — độ chính xác so với nguồn (factual/source accuracy), coverage thông tin quan trọng, và hallucination — nên được đo thế nào?

## Vì sao câu hỏi này quan trọng

**PROJECT SOURCE:** FR-03 (`01_source_docs/requirements/requirements-functional.pdf`) yêu cầu block nội dung phải trace được về vùng nguồn (`source_ref`) và không bịa số liệu/ý chính.

Đây là property có failure mode khá cụ thể (block không trace được nguồn, hoặc bịa nội dung không có trong source), nên là candidate mạnh cho deterministic/automatic metric hơn là chỉ dựa vào LLM-judge — nhưng **đây mới là giả thuyết cần verify bằng external evidence**, không phải kết luận.

## Liên quan

- RQ00, RQ01 (taxonomy cha — "Content Quality")
- RQ06 (LLM-judge, nếu phần nào của fidelity cần scoring qua judge)
- `06_design/METRIC_REGISTRY.md`

## Findings

_(chưa có)_
