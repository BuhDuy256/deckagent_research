# Research Status

Bảng trạng thái tổng hợp — mở file này để biết ngay "đang ở đâu", không cần lục qua từng RQ.

## Wave 1 — nền tảng (**RESEARCH DONE — chờ human review**, 2026-08-14)

Cả 3 session đã hoàn tất, mỗi RQ đủ 3 file. (Ghi chú lịch sử: 3 session bị ngắt giữa chừng do hit API session limit, sau đó được resume từ transcript cũ với context research còn nguyên — không phải research lại từ đầu.)

| RQ | Chủ đề | Trạng thái | Output |
| --- | --- | --- | --- |
| [RQ01](02_research_questions/RQ01_evaluation_dimensions.md) | Evaluation dimensions (WHAT) | **ANSWERED — chờ review** | `03_research/RQ01/` — landscape 340 / evidence 312 / recommendation 369 dòng |
| [RQ06](02_research_questions/RQ06_llm_judge.md) | Judge/evaluator landscape (HOW) | **ANSWERED — chờ review** | `03_research/RQ06/` — landscape 363 / evidence 548 / recommendation 309 dòng |
| [RQ08](02_research_questions/RQ08_baselines.md) | Baseline landscape (AGAINST WHAT) | **ANSWERED — chờ review** | `03_research/RQ08/` — landscape 330 / evidence 205 / recommendation 219 dòng |

`03_research/external_systems/opendesign.md` đã được RQ08 cập nhật từ stub (Open Design = `nexu-io/open-design`, VERIFIED E1; nhưng **liên kết với NFR mục 0.1 vẫn là INFERENCE chưa confirm**).

### Chưa làm — cố ý

- **Chưa consolidate ~26 proposed decision** từ mục 4 của 3 file `recommendation.md` vào `OPEN_QUESTIONS.md`. Chờ human review trước.
- **Chưa tạo `03_research/WAVE1_SYNTHESIS.md`.** Theo quy trình: user đọc 3 file `recommendation.md` trước → rồi mới cho một session riêng làm synthesis.
- **Wave 2 vẫn BLOCKED.** Không mở trước checkpoint review.

### Cross-check giữa 3 session (giá trị của việc chạy độc lập)

- **PresentBench: cả 3 session độc lập đều verify ra cùng arXiv 2603.07244.** RQ06 (session chịu trách nhiệm chính) cung cấp đầy đủ tác giả + repo Apache-2.0 + HF dataset. Q-005 coi như đã trả lời được phần "entity có thật".
- **Hai session hội tụ về cùng một mối lo:** RQ01 báo PPTEval Coherence human-correlation 0.55 (trong khi Design 0.90); RQ06 báo PresentBench ρ=0.532 so với trần người–người 0.664. Cả hai đều chỉ về cùng một hướng: **judge yếu nhất đúng ở dimension Deck Agent claim giá trị nhất**. Đây là điểm quan trọng nhất cần xử lý ở synthesis.
- **Một tension cần giải ở synthesis:** RQ06 cảnh báo ngưỡng agreement "đủ tin" (Krippendorff) chỉ ở mức E2 và **mâu thuẫn với thực tế domain** — PPTEval đo κ≈0.59 *giữa người với người*, dưới cả ngưỡng tentative. Tức chuẩn agreement chuẩn mực có thể không áp dụng được cho domain này.

**Quy ước Wave 1:**
- 3 session chạy độc lập để không anchor lẫn nhau. **Không session nào được tự synthesize cả Wave 1.**
- Không session nào được sửa file dùng chung (`OPEN_QUESTIONS.md`, `EVIDENCE_INDEX.md`, `RESEARCH_STATUS.md`, `RQ_INDEX.md`, `06_design/`, `DECISION_LOG.md`) — tránh ghi đè khi chạy song song. Open question mới nằm ở mục 4 của từng `recommendation.md`, consolidate sau.
- Mỗi RQ đánh dấu nguồn theo **MUST READ** (tối đa 3–5 primary source) / **SHOULD READ** / **DISCOVERY ONLY** — để đọc có trọng tâm, không chìm trong literature.
- Việc đầu tiên của RQ06: verify UNVERIFIED ENTITY "PresentBench" (Q-005), route evidence sang RQ liên quan nếu nó thật.

**Checkpoint tiếp theo sau khi cả 3 xong:** đọc theo thứ tự `RQ01/recommendation.md` → `RQ06/recommendation.md` → `RQ08/recommendation.md`, rồi mới tạo `03_research/WAVE1_SYNTHESIS.md` bằng một session riêng. **Không mở Wave 2 trước checkpoint này.**

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
