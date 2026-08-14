# Chỉ mục Research Question

> RQ00 đã **ACCEPTED** (2026-08-14) sau audit toàn bộ FR/NFR/architecture — xem `03_research/current_evaluation_audit.md` và `05_decisions/DECISION_LOG.md` D-001/D-002. RQ01–RQ08 giữ nguyên (không merge/split/xóa), nhưng research chạy theo **wave** thay vì tuần tự theo số.

## Wave 1 — nền tảng, chạy song song (đã unblock)

| ID | Câu hỏi | Trạng thái |
| --- | --- | --- |
| [RQ01](RQ01_evaluation_dimensions.md) | Evaluation dimensions đúng cho Deck Agent là gì? (trả lời **WHAT**) | READY |
| [RQ06](RQ06_llm_judge.md) | Subjective quality được evaluate reliably bằng cách nào? (trả lời **HOW**) | READY |
| [RQ08](RQ08_baselines.md) | So sánh Deck Agent với cái gì? (trả lời **AGAINST WHAT**) | READY |

Việc đầu tiên của Wave 1: verify UNVERIFIED ENTITY "PresentBench" (xem `05_decisions/OPEN_QUESTIONS.md` Q-005) — route evidence sang RQ liên quan sau khi xác định nó là gì.

## Wave 2 — chi tiết hóa từng dimension (blocked by Wave 1)

| ID | Câu hỏi | Trạng thái |
| --- | --- | --- |
| [RQ02](RQ02_content_fidelity.md) | Content fidelity nên đo thế nào? | Blocked — chờ RQ01 + RQ06 |
| [RQ03](RQ03_structure_planning.md) | Structure/planning quality nên đo thế nào? | Blocked — chờ RQ01 + RQ06 |
| [RQ04](RQ04_design_quality.md) | Design/presentation quality nên đo thế nào? | Blocked — chờ RQ01 + RQ06 |
| [RQ05](RQ05_ready_to_use.md) | "Ready-to-use" nên đo thế nào? | Blocked — chờ RQ01 + RQ06 |

## Wave 3 — benchmark, cần biết đo gì trước (blocked by Wave 2)

| ID | Câu hỏi | Trạng thái |
| --- | --- | --- |
| [RQ07](RQ07_benchmark.md) | Frozen benchmark dataset nên có hình dạng gì? | Blocked — chờ Wave 2 |

## Đã resolve, không còn là RQ riêng

Xem `05_decisions/OPEN_QUESTIONS.md` (Q-006, Q-008, Q-010) và `DECISION_LOG.md` (D-003, D-004, D-006): câu hỏi về rigor threshold, adversarial robustness, và development evaluation cadence đã được route thành design workstream trong `06_design/`, không phải RQ literature-heavy.

## Chú giải trạng thái

`READY` — sẵn sàng bắt đầu · `Blocked` — chờ wave trước · `In progress` — đang research · `Answered` — có proposal, chờ review · `Accepted` — đã promote vào `06_design/`

Xem `RESEARCH_STATUS.md` ở root cho bảng trạng thái gộp.
