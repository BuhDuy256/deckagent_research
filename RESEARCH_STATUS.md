# Research Status

Bảng trạng thái tổng hợp — mở file này để biết ngay "đang ở đâu", không cần lục qua từng RQ.

## Wave 1 — nền tảng (READY, chạy song song)

| RQ | Chủ đề | Trạng thái | Ghi chú |
| --- | --- | --- | --- |
| [RQ01](02_research_questions/RQ01_evaluation_dimensions.md) | Evaluation dimensions (WHAT) | **READY** | Không nhồi câu hỏi rigor (Q-006) vào đây — đã route sang design policy |
| [RQ06](02_research_questions/RQ06_llm_judge.md) | Judge/evaluator landscape (HOW) | **READY** | Có 3 sub-question mới: bias mitigation, checklist-per-instance vs rubric, NFR-52/53 landscape comparison |
| [RQ08](02_research_questions/RQ08_baselines.md) | Baseline landscape (AGAINST WHAT) | **READY** | — |

**Việc đầu tiên của Wave 1:** verify UNVERIFIED ENTITY "PresentBench" (`05_decisions/OPEN_QUESTIONS.md` Q-005), route evidence sang RQ liên quan.

## Wave 2 — chi tiết hóa dimension (Blocked by Wave 1)

| RQ | Chủ đề | Trạng thái |
| --- | --- | --- |
| [RQ02](02_research_questions/RQ02_content_fidelity.md) | Content fidelity | Blocked — chờ RQ01 + RQ06 |
| [RQ03](02_research_questions/RQ03_structure_planning.md) | Structure / planning quality | Blocked — chờ RQ01 + RQ06 |
| [RQ04](02_research_questions/RQ04_design_quality.md) | Design quality | Blocked — chờ RQ01 + RQ06 |
| [RQ05](02_research_questions/RQ05_ready_to_use.md) | Ready-to-use | Blocked — chờ RQ01 + RQ06 |

## Wave 3 — benchmark (Blocked by Wave 2)

| RQ | Chủ đề | Trạng thái |
| --- | --- | --- |
| [RQ07](02_research_questions/RQ07_benchmark.md) | Benchmark dataset | Blocked — chờ Wave 2 |

## RQ00 — audit (hoàn tất)

| RQ | Chủ đề | Trạng thái |
| --- | --- | --- |
| [RQ00](02_research_questions/RQ00_current_evaluation_audit.md) | Audit evaluation proposal hiện tại | **ACCEPTED** |

## Trạng thái workspace

**RQ00 ACCEPTED, Wave 1 đang mở (RQ01/RQ06/RQ08).** 6 quyết định đã log ở `05_decisions/DECISION_LOG.md` (D-001 → D-006): giữ nguyên RQ01-08, research theo wave, rigor-threshold → design policy, adversarial → benchmark category, operational layer (latency/cost/reliability) → thêm vào `EVALUATION_FRAMEWORK.md`, dev evaluation cadence → design workstream sau Wave 1.

**Từ đây trở đi mỗi RQ cần external evidence thật** (paper, benchmark, official repo, related open-source system) — không chỉ reasoning trên source docs nội bộ. Xem `04_evidence/EVIDENCE_POLICY.md`.

## Legend

`READY` — sẵn sàng bắt đầu · `IN PROGRESS` — đang research · `BLOCKED` — chờ wave/RQ khác · `ANSWERED` — có proposal, chờ review · `ACCEPTED` — đã confirm, đã promote vào `06_design/`

Cập nhật file này mỗi khi trạng thái một RQ thay đổi.
