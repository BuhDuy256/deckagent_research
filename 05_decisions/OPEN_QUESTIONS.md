# Open Questions

Câu hỏi và đề xuất cần một decision, owner, hoặc external input trước khi design work có thể tiếp tục. Chỉ chuyển một item sang `DECISION_LOG.md` khi nó **thực sự được confirm** (bởi team/giảng viên), không phải khi mới được đề xuất.

## Format

```
## Q-001 — <câu hỏi ngắn>
Nêu ra: YYYY-MM-DD
Bối cảnh: <vì sao cần quyết định>
Đề xuất (nếu có): <một câu trả lời candidate, và ai/cái gì đề xuất>
Đang chờ: <cần gì để giải quyết>
```

---

## Q-001 — Có nên restructure 4 quality dimension phẳng thành taxonomy phân cấp?

Nêu ra: 2026-08-14
Bối cảnh: Architecture doc §8 liệt kê content fidelity / ready-to-use / design consistency / structure như 4 metric phẳng, ngang hàng. Scoping ban đầu (`00_context/EVALUATION_MISSION.md`) đề xuất thay bằng taxonomy phân cấp (Content / Planning / Presentation / Usability quality — xem `00_context/TERMINOLOGY.md`).
Đề xuất: Áp dụng taxonomy này — **đang chờ team/giảng viên confirm**, vì về bản chất đây là việc challenge lại §8 hiện có của architecture doc.
Đang chờ: RQ00 hoàn tất (audit toàn bộ requirement) rồi mới tới RQ01 (trả lời chính thức câu hỏi này bằng evidence).

## Q-002 — Xác nhận phân bổ effort evaluation theo pipeline stage

Nêu ra: 2026-08-14
Bối cảnh: Scoping ban đầu gợi ý ContentPlanner xứng đáng effort evaluation lớn nhất, Adapter/Exporter chủ yếu là engineering correctness (xem `00_context/SYSTEM_MAP.md`). Cơ sở là một ghi chú trong architecture doc ("nên tập trung nghiên cứu nhiều nhất" ở ContentPlanner) — đây là PROJECT SOURCE, chưa phải kết luận evaluation đã audit.
Đề xuất: Dùng allocation này làm working hypothesis cho `06_design/EVALUATION_FRAMEWORK.md`.
Đang chờ: Validate sau khi RQ00 map đầy đủ FR/NFR trong `01_source_docs/requirements/`.

## Q-003 — `slide_type` có nên đổi tên/định nghĩa lại không?

Nêu ra: 2026-08-14
Bối cảnh: `slide_type` hiện đặt ở Deck-level metadata (`teaching | catchup | speaker_support`), không phải per-Slide, nhưng tên gọi gợi ý ngược lại (xem `00_context/TERMINOLOGY.md`).
Đề xuất: Không có — đây không phải việc evaluation workspace phải giải quyết. Chỉ cần research agent hiểu đúng schema hiện tại khi viết RQ/metric liên quan.
Đang chờ: Team quyết định (nếu có) đổi tên thành `deck_type`/`presentation_type` trước khi implementation freeze — nằm ngoài phạm vi evaluation research.

## Q-004 — UNVERIFIED ENTITY: "test hapkin slide"

Nêu ra: 2026-08-14
Bối cảnh: User nhắc tới một tên project/test gần giống "test hapkin slide" từ trao đổi với team, nhưng tự nhận tên có thể sai hoặc chưa chính xác.
Đề xuất: Không có — **không được đoán entity này là gì**. Khi research, thử search để verify tên chính xác; nếu không xác định được, giữ nguyên trạng thái UNVERIFIED, không dùng làm evidence.
Đang chờ: User/team cung cấp tên chính xác hoặc nguồn gốc của entity này.

---

## Đề xuất từ RQ00 (`03_research/current_evaluation_audit.md`) — chờ review, chưa áp dụng vào `RQ_INDEX.md`

## Q-005 — "PresentBench" — **ĐÃ VERIFY, entity có thật** (còn phần hệ quả chờ review)

Nêu ra: 2026-08-14 · Verify xong: 2026-08-14 (Wave 1)
Bối cảnh: Architecture doc §8 viết: *"MLLM-as-judge (theo hướng PresentBench - checklist chi tiết riêng cho từng input, judge chấm từng mục kèm bằng chứng)"*. Trước Wave 1 đây là UNVERIFIED ENTITY.

**Kết quả verify (EXTERNAL EVIDENCE, E1):** *PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation*, arXiv 2603.07244 (v1, 2026-03-07); repo chính chủ `github.com/PresentBench/PresentBench` (Apache-2.0); dataset trên HuggingFace. **Ba session Wave 1 (RQ01, RQ06, RQ08) verify độc lập đều ra cùng arXiv ID.** RQ06 đã phân biệt rõ với các entity khác tên gần giống (UniPPTBench, SlidesBench, PPTEval, SlidesGen-Bench).

**Phần chưa đóng — cần review:** RQ06 báo §8 mô tả **đúng cơ chế** (checklist per-input, item nhị phân, judge chấm kèm bằng chứng) nhưng bỏ sót 3 điều quan trọng: (a) quy mô authoring rất lớn (54.1 item/instance × 238 instance, expert soạn tay), (b) trần tin cậy thực tế ρ=0.532 so với trần người–người 0.664, (c) nó là benchmark end-to-end chứ không phải một judge protocol đóng gói sẵn để mượn về dùng.

Chi tiết + đề xuất route sang RQ01/RQ02/RQ04/RQ07/RQ08 nằm ở `03_research/RQ06/evidence.md` và `recommendation.md`. **Chưa quyết định gì** về việc có dùng PresentBench hay không — chờ human review + Wave 1 synthesis.

## Q-006 — [RESOLVED → D-003] Ngưỡng "đo được vs định tính" chấp nhận cho đồ án

Nêu ra: 2026-08-14 · Resolved: 2026-08-14
Bối cảnh: `03-research-evaluation.pdf` (task brief) hỏi trực tiếp: *"Cái nào đo được, cái nào chỉ quan sát định tính - và chấp nhận mức nào cho đồ án?"*
Quyết định: **Không** tạo RQ09. Đây là design policy, không phải technical research question — route thành section "Evaluation Rigor Policy" trong `06_design/EVALUATION_FRAMEWORK.md` (bảng Minimum/Better/Overkill), điền dần trong Wave 2. Xem `05_decisions/DECISION_LOG.md` D-003.

## Q-007 — [RESOLVED → applied vào RQ06] Explicit hóa 3 sub-question trong RQ06

Nêu ra: 2026-08-14 · Resolved: 2026-08-14
Bối cảnh: Audit RQ00 tìm thấy 3 điểm task brief/NFR đã hỏi/gợi ý cụ thể nhưng RQ06 viết chung chung: (a) cách giảm self-preference bias, (b) checklist theo instance vs. rubric chung, (c) NFR-52/NFR-53 đã có candidate method cụ thể cần đưa vào landscape comparison (không phải preferred method mặc định).
Quyết định: Accept — đã cập nhật `02_research_questions/RQ06_llm_judge.md` với 3 sub-question này.

## Q-008 — [RESOLVED → D-004] Adversarial/prompt-injection robustness (NFR-42)

Nêu ra: 2026-08-14 · Resolved: 2026-08-14
Bối cảnh: NFR-42 yêu cầu rõ: *"Prompt injection từ tài liệu: sanitize... eval có case adversarial nhẹ"*, nhưng §8 và RQ01–08 không cover việc này.
Quyết định: **Không** tạo RQ riêng ở giai đoạn này — route thành benchmark category (`benchmark/adversarial/`) trong `06_design/BENCHMARK_SPEC.md`, đánh giá bằng deterministic/task-success test. Xem `05_decisions/DECISION_LOG.md` D-004. Revisit nếu security/prompt-injection trở thành contribution chính của đồ án.

## Q-009 — [RESOLVED → D-005] Latency/cost/reliability cần vào `EVALUATION_FRAMEWORK.md`

Nêu ra: 2026-08-14 · Resolved: 2026-08-14
Bối cảnh: §8 chỉ đề cập AI-quality, bỏ sót latency (NFR-01-03), reliability (NFR-20-22), cost (NFR-90-92) dù mission cần dữ liệu này.
Quyết định: Accept — `06_design/EVALUATION_FRAMEWORK.md` đã được restructure thành 3 layer (Quality / Correctness-Functional / Operational Evaluation). Xem `05_decisions/DECISION_LOG.md` D-005.

## Q-010 — [RESOLVED → D-006] Development Evaluation Protocol / cadence

Nêu ra: 2026-08-14 (do user thêm khi review RQ00) · Resolved: 2026-08-14
Bối cảnh: Mission đặc thù là chứng minh cải thiện "mỗi ngày", nhưng RQ hiện tại (RQ06 judge, RQ07 benchmark, RQ08 baseline) tập trung vào final quality measurement — chưa có câu hỏi rõ "bộ nào chạy mỗi commit/PR/nightly/milestone". Một metric tốt cho final report (vd. human evaluation) có thể là "terrible daily metric".
Quyết định: Không tạo RQ09 literature-heavy — thêm workstream thiết kế "Development Evaluation Protocol" (Tier 0: mỗi commit — deterministic test; Tier 1: PR/thay đổi quan trọng — small frozen AI eval set; Tier 2: nightly/weekly — full automated benchmark; Tier 3: milestone/thesis — human-calibrated final eval), lên lịch sau Wave 1 + RQ07. Xem `05_decisions/DECISION_LOG.md` D-006.
