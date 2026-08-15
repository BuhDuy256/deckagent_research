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

## Q-011 — [HOLD từ Wave 1 Gate G-03] Reference-frame taxonomy 5 lớp có đứng vững không?

Nêu ra: 2026-08-14 (Wave 1 Decision Gate)
Bối cảnh: RQ01 đề xuất tổ chức evaluation theo **reference frame** (deck đang được so với cái gì) thành L0 Artifact Integrity → L1 Source Grounding → L2 Information Selection → L3 Brief Compliance → L4 Presentation Quality → L5 Outcome/Usability, thay cho 4 dimension phẳng của §8.
Vì sao HOLD: Cách nghĩ có nền tảng tốt (reference frame thường dự đoán được method). Nhưng **lý do biện minh không đủ** — nó dựa vào so sánh human-correlation giữa các paper khác nhau về dataset/judge/task/protocol; cross-paper comparison chỉ đủ để gợi ý, không đủ để chứng minh taxonomy tốt nhất. RQ01 cũng tự nhận cách nhóm 5 lớp là INFERENCE của chính nó, không nguồn nào phát biểu.
Đang chờ: Wave 2 stress-test. Dùng như working taxonomy, đồng thời tích cực tìm chỗ nó gãy. **Không** viết vào `06_design/` như taxonomy chính thức trước khi Wave 2 xong.

## Q-012 — [HOLD từ Wave 1 Gate G-05] F2 (NLI/AlignScore/FActScore) có usable cho Deck Agent không?

Nêu ra: 2026-08-14 (Wave 1 Decision Gate)
Bối cảnh: RQ06 đề xuất family F2 (NLI/alignment/atomic-fact) là family duy nhất chạm được fidelity ở tần suất daily, và chỉ ra nó vắng mặt hoàn toàn trong §8/NFR.
Vì sao HOLD: Các method này validate chủ yếu trên summarization/factuality/biography, **chưa có nguồn nào áp lên slide deck** (RQ06 tự nhận, U-2). Deck Agent sinh bullet đã nén/diễn giải/**tính toán lại** — vd. source *"Revenue increased from 12.4M to 18.7M because enterprise adoption accelerated"* → slide *"Enterprise adoption drove ~51% YoY revenue growth"*. Evaluator textual có thể hiểu đúng hoặc fail.
Đang chờ: RQ02 phải trả lời "**F2 nào thực sự usable**", không phải "đã tìm thấy AlignScore → implement AlignScore". Cần thí nghiệm nhỏ trước khi cam kết.

## Q-013 — [HOLD từ Wave 1 Gate G-07] Cỡ mẫu human evaluation

Nêu ra: 2026-08-14 (Wave 1 Decision Gate)
Bối cảnh: RQ06 đưa candidate 3 annotator × 50–100 deck, có nguồn (alt-test, Calderon/Reichart/Dror 2025).
Vì sao HOLD: Con số hữu ích cho scale intuition, nhưng paper dùng để suy ra nó trả lời một câu hỏi thống kê **khác** với "bao nhiêu deck để validate correlation giữa judge của Deck Agent và human". Sample size thật phụ thuộc: validate metric nào, unit là deck hay slide, pairwise hay absolute, variance thực tế, benchmark size, budget/time.
Đang chờ: RQ07 + pilot data. Ghi trong design doc là `Human sample size = TBD; candidate scale from literature: tens to ~100`. **Không** ghi `Requirement = 100 decks`.

## Q-014 — [OPEN, CHẶN IMPLEMENTATION — Wave 1 Gate G-12] A1 và B-1 đang trùng nhau

Nêu ra: 2026-08-14 (Wave 1 Decision Gate)
Bối cảnh: RQ08 định nghĩa B-1 (single-shot planning baseline) là `Extractor output → ONE prompt → Deck IR`, và A1 (ablation `without ContentPlanner`) có nguy cơ là **cùng một experiment**.
Rủi ro: implement hai experiment giống hệt nhau rồi gọi hai tên khác nhau, rồi báo cáo như hai evidence độc lập.
Cần quyết định: **merge chúng**, HOẶC định nghĩa A1 khác — vd. A1 = full pipeline nhưng bypass explicit plan/outline artifact; B-1 = one-shot generation hoàn toàn.
Đang chờ: **Phải resolve TRƯỚC khi chạy experiment**, không phải sau. Đây là mục duy nhất của Gate đang chặn implementation.

## Q-010 — [RESOLVED → D-006] Development Evaluation Protocol / cadence

Nêu ra: 2026-08-14 (do user thêm khi review RQ00) · Resolved: 2026-08-14
Bối cảnh: Mission đặc thù là chứng minh cải thiện "mỗi ngày", nhưng RQ hiện tại (RQ06 judge, RQ07 benchmark, RQ08 baseline) tập trung vào final quality measurement — chưa có câu hỏi rõ "bộ nào chạy mỗi commit/PR/nightly/milestone". Một metric tốt cho final report (vd. human evaluation) có thể là "terrible daily metric".
Quyết định: Không tạo RQ09 literature-heavy — thêm workstream thiết kế "Development Evaluation Protocol" (Tier 0: mỗi commit — deterministic test; Tier 1: PR/thay đổi quan trọng — small frozen AI eval set; Tier 2: nightly/weekly — full automated benchmark; Tier 3: milestone/thesis — human-calibrated final eval), lên lịch sau Wave 1 + RQ07. Xem `05_decisions/DECISION_LOG.md` D-006.
