# Decision Log

Ghi lại (append-only) các decision **đã thực sự được chốt** (bởi team/giảng viên, không chỉ được đề xuất) trong dự án này. Mỗi entry phải giúp người đọc sau này hiểu *vì sao* mà không cần đọc lại toàn bộ cuộc thảo luận.

## Format

```
## D-001 — <tên ngắn>
Ngày: YYYY-MM-DD
Decision: <đã quyết định gì>
Vì sao: <lý do / evidence dẫn tới quyết định>
Phương án khác đã xét: <nếu có — link REJECTED_APPROACHES.md nếu chi tiết>
Ảnh hưởng: <RQ / design doc / component nào bị ảnh hưởng>
```

---

## D-001 — RQ00 accepted; giữ nguyên RQ01–RQ08

Ngày: 2026-08-14
Decision: Accept output của RQ00 (`03_research/current_evaluation_audit.md`). Không merge/split/xóa bất kỳ RQ nào trong RQ01–RQ08 — audit không tìm thấy lý do để làm vậy.
Vì sao: User review trực tiếp bảng audit + đề xuất routing từ RQ00, xác nhận 8 RQ hiện tại đều hợp lý về phạm vi.
Ảnh hưởng: `RQ_INDEX.md`, `RESEARCH_STATUS.md`

## D-002 — Research theo Wave, không theo thứ tự số RQ01→RQ08

Ngày: 2026-08-14
Decision: Research theo 3 wave thay vì tuần tự RQ01→RQ02→...→RQ08:
- **Wave 1 (song song, unblock ngay):** RQ01 (dimensions — trả lời WHAT), RQ06 (judge/evaluator landscape — trả lời HOW), RQ08 (baseline landscape — trả lời AGAINST WHAT)
- **Wave 2 (sau Wave 1):** RQ02, RQ03, RQ04, RQ05 — cần output của RQ01 (dimension nào cần đo) và RQ06 (method nào khả dụng) để bớt mơ hồ
- **Wave 3 (sau Wave 2):** RQ07 — benchmark design cần biết trước dimension nào phải đo (từ Wave 2)
Vì sao: RQ01/RQ06/RQ08 là 3 trục nền tảng (WHAT/HOW/AGAINST WHAT) mà RQ02–RQ05 phụ thuộc vào; RQ07 (benchmark) cần biết đo gì trước khi thiết kế input set.
Ảnh hưởng: `RQ_INDEX.md`, `RESEARCH_STATUS.md`, status của từng RQ0x

## D-003 — Câu hỏi "ngưỡng đo được/định tính chấp nhận cho đồ án" (Q-006) là design policy, không phải RQ

Ngày: 2026-08-14
Decision: Không tạo RQ09 cho câu hỏi này. Thay vào đó, thêm section "Evaluation Rigor Policy" vào `06_design/EVALUATION_FRAMEWORK.md` (bảng Minimum / Better / Overkill cho từng quality), nội dung được điền dần trong Wave 2.
Vì sao: Đây là quyết định về mức độ rigor/scope, không phải một technical research question kiểu "phương pháp nào đúng" — không cần literature review riêng, chỉ cần một khung phân loại evidence tối thiểu/tốt hơn/dư thừa.
Ảnh hưởng: `06_design/EVALUATION_FRAMEWORK.md`, RQ01 (không nhồi câu hỏi rigor vào RQ01)

## D-004 — Adversarial/prompt-injection (NFR-42, Q-008) route vào benchmark category, không tạo RQ riêng

Ngày: 2026-08-14
Decision: Không tạo RQ riêng cho adversarial robustness ở giai đoạn này. Route thành một benchmark category (`benchmark/adversarial/`) trong `06_design/BENCHMARK_SPEC.md`, đánh giá bằng deterministic/task-success test thay vì judge/human machinery đầy đủ.
Vì sao: Tạo RQ riêng cho scope này dễ làm phình research scope ra ngoài mission chính (đo improvement, so sánh version/baseline). NFR-42 vẫn là yêu cầu evaluation hợp lệ nhưng độ ưu tiên thấp hơn 4 dimension AI-quality chính.
Revisit nếu: Giảng viên/team coi security/prompt-injection là contribution quan trọng của đồ án — khi đó cần RQ riêng.
Ảnh hưởng: `06_design/BENCHMARK_SPEC.md`, `02_research_questions/RQ07_benchmark.md`

## D-005 — Thêm Operational Evaluation làm layer chính thức trong Evaluation Framework

Ngày: 2026-08-14
Decision: Accept Q-009. Restructure `06_design/EVALUATION_FRAMEWORK.md` thành 3 layer: Quality Evaluation, Correctness/Functional Evaluation, Operational Evaluation (Latency, Cost, Reliability).
Vì sao: §8 (architecture doc) chỉ nói AI-quality, bỏ sót hoàn toàn latency/cost/reliability dù NFR-01-03/20-22/90-92 yêu cầu rõ và mission cần dữ liệu này để trả lời "improvement quality có đánh đổi latency/cost/reliability không". Không cần external research nhiều — đo lường operational khá chuẩn.
Ảnh hưởng: `06_design/EVALUATION_FRAMEWORK.md`

## D-006 — Development Evaluation Protocol (Q-010) là design workstream, lên lịch sau Wave 1

Ngày: 2026-08-14
Decision: Thêm workstream "Development Evaluation Protocol" — phân tier metric nào chạy mỗi commit / PR / nightly / milestone (Tier 0–3). Không phải RQ literature-heavy; là design task, lên lịch sau khi Wave 1 (RQ01, RQ06) và RQ07 (benchmark) có output.
Vì sao: Mission đặc thù là chứng minh cải thiện "mỗi ngày" — một metric tốt cho final report chưa chắc usable hằng ngày (vd. human evaluation là evidence tốt cho thesis nhưng quá chậm/đắt cho mỗi commit). Audit hiện chỉ đánh dấu Dev Harness? ✓/trống, chưa biến thành protocol/tiering thật.
Ảnh hưởng: `06_design/EVALUATION_PIPELINE.md` (nơi protocol này cuối cùng sẽ nằm)

---

## Wave 1 Decision Gate (2026-08-14) — D-007 → D-014

Nguồn: human review Wave 1, ghi ở `03_research/WAVE1_SYNTHESIS.md`. Các mục HOLD nằm ở `OPEN_QUESTIONS.md`, các mục REJECT ở `REJECTED_APPROACHES.md`.

## D-007 — Tách deterministic evaluation khỏi judged evaluation

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-01)
Decision: Mọi RQ và mọi metric phải tách rõ phần deterministic khỏi phần cần judge/human, không gộp thành một score.
Vì sao: Evidence hội tụ ở cả 3 RQ Wave 1. Việc code kiểm được (overflow, token conformance, schema validity) thì để code kiểm — rẻ hơn, nhanh hơn, lặp lại được, và theo RQ01 còn **chính xác hơn** judge ở đúng loại việc này (SlideAudit: LLM detect design flaw chỉ F1 0.331–0.655).
Ảnh hưởng: Mọi RQ Wave 2, `06_design/EVALUATION_FRAMEWORK.md` §2

## D-008 — Tách "Content fidelity" thành 3 failure riêng biệt

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-02)
Decision: Coi hallucination (bịa) / incorrectness (nói sai) / coverage gap (bỏ sót) là ba failure riêng, không gộp thành một score "Content Fidelity". Đây là working distinction.
Vì sao: Score gộp không cho biết hỏng ở đâu. Deck nói 5/10 ý đều đúng và deck nói đủ 10 ý nhưng 2 ý sai có thể cùng ra "7/10" nhưng là hai loại lỗi khác nhau. Trong development, "hallucination ↓ nhưng coverage ↓" không cho phép kết luận version mới tốt hơn. External work cũng tách các khái niệm này.
Ảnh hưởng: RQ02 (câu hỏi trung tâm), `06_design/METRIC_REGISTRY.md`

## D-009 — ContentPlanner chứng minh bằng end-to-end + ablation, không bằng "ContentPlanner Score"

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-04)
Decision: Evidence về contribution của ContentPlanner đến từ **end-to-end metric + ablation**. Component-level metric vẫn được dùng nhưng **chỉ với vai trò diagnostic/debug trong development**, không phải evidence chứng minh giá trị kiến trúc.
Vì sao: Literature chủ yếu đánh giá deck cuối rồi dùng ablation để chứng minh component có giá trị. Nếu invent "ContentPlanner Quality = 8.3" thì câu hỏi ngay lập tức là "8.3 có làm deck cuối tốt hơn không?" — không trả lời được. Nhưng bỏ hẳn component metric cũng sai: khi deck xấu, cần truy ngược planner bỏ mất critical topic nào.
Ảnh hưởng: RQ03, RQ08, `06_design/BASELINE_SPEC.md`

## D-010 — Material-dependent quality cần criteria per-instance (principle)

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-06)
Decision: Chấp nhận **nguyên tắc**: quality phụ thuộc nội dung nguồn cần criteria/probes phụ thuộc từng instance. **Cách tạo** criteria đó vẫn là câu hỏi mở của RQ02/RQ07.
Vì sao: PDF A nói `Revenue = 18.7M`, PDF B nói `23.4M` → không thể dùng generic rubric "check whether numbers are correct". Nhưng "per-instance" KHÔNG bắt buộc nghĩa là expert viết tay 54 item/PDF như PresentBench (đó là benchmark-scale effort); còn nhiều cách khác cần so sánh.
Ảnh hưởng: RQ02, RQ07

## D-011 — Mọi model-based evaluator phải đo noise floor trước khi dùng track regression

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-09)
Decision: Không dùng bất kỳ model-based evaluator nào để track regression trước khi đo repeatability/noise floor của chính nó.
Vì sao: Cùng deck, cùng prompt, judge chạy 5 lần có thể ra 81/85/80/84/82. Nếu đổi Planner thấy 82 → 84 mà không biết noise floor, "+2 improvement" có thể chỉ là nhiễu. Không đo thì mọi delta nhỏ hơn noise sẽ bị đọc nhầm thành tiến bộ — phá thẳng nền của mission "chứng minh cải thiện mỗi ngày". temperature=0 không đảm bảo determinism.
Ảnh hưởng: Mọi RQ đề xuất model-based evaluator; `06_design/EVALUATION_PIPELINE.md`. Còn mở: N=?, delta bao nhiêu là significant, aggregate thế nào → pilot + RQ07.

## D-012 — 4 baseline family là 4 câu hỏi khác nhau, không thay thế nhau

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-10)
Decision: Giữ 4 family: historical (hôm nay vs hôm qua) · naive (pipeline nhiều bước có đáng?) · ablation (component đóng góp gì?) · external (ta ở đâu trên bản đồ?). Không coi cái này thay được cái kia.
Vì sao: Bốn câu hỏi thực sự khác nhau về mặt logic; trộn chúng dẫn tới claim vượt quá điều kiện đo.
Ảnh hưởng: `06_design/BASELINE_SPEC.md`

## D-013 — External comparison ở mức optional / contextual

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-13)
Decision: External comparison là optional, dùng để định vị ("trong điều kiện benchmark X, ngày Y, hệ thống chúng tôi ở vị trí Z so với một số hệ thống liên quan"), **không** dùng để chứng minh causal contribution của ContentPlanner hay của kiến trúc.
Vì sao: Open Design input là brief và HTML-first; Deck Agent document-grounded và IR-first — hai bên chưa chắc chơi cùng bài. Thắng Open Design không chứng minh architecture Deck Agent tốt hơn. Bộ baseline defensible cho thesis nằm trọn trong tầm kiểm soát team.
Ảnh hưởng: `06_design/BASELINE_SPEC.md`, `03_research/external_systems/`

## D-014 — Freeze reproducibility / run manifest ngay, không chờ Wave 2

Ngày: 2026-08-14 (Wave 1 Decision Gate, G-14)
Decision: Yêu cầu team implementation lưu run manifest đầy đủ **từ bây giờ** (danh sách field ở `06_design/EVALUATION_PIPELINE.md` §Run Manifest). Đặc biệt: **plan/outline artifact phải persist**.
Vì sao: Chưa biết final metric nhưng đã chắc chắn evaluation cần reproducibility. Run comparison vô nghĩa nếu model/prompt/benchmark thay đổi ngầm (Atil 2024: dao động ~15% giữa các run được cho là "deterministic"). Plan artifact cần persist không phải vì plan là metric cuối, mà vì khi output hỏng cần reconstruct được "planner đã quyết định gì". Đây là engineering requirement độc lập với kết quả research nên không cần chờ.
Ảnh hưởng: `06_design/EVALUATION_PIPELINE.md`; yêu cầu gửi team implementation

---

## Wave 2A Human Review (2026-08-15) — D-015 → D-020

Nguồn: human review trực tiếp của Wave 2A handoff. Các decision dưới đây được promote từ candidate/working policy theo đúng phạm vi ghi trong từng mục; chúng **không** biến MiniCheck, threshold pilot hay ba định nghĩa `slide_type` cụ thể thành accepted truth.

## D-015 — Coverage đo topic/fact presence; correctness đo riêng

Ngày: 2026-08-15 (Wave 2A Human Review)
Decision: Coverage đo một topic/fact **có xuất hiện hay không**. Fact đã xuất hiện nhưng nói sai **không** bị gán là missing; lỗi đó được báo riêng trong correctness/incorrectness. Không aggregate coverage và correctness thành một score.
Vì sao: Giữ đúng phân vai của D-008: bỏ sót và nói sai là hai failure khác nhau, cần action khác nhau. Nếu “present nhưng sai” vừa giảm coverage vừa tăng incorrectness, cùng một lỗi bị đếm trên hai trục và coverage không còn độc lập.
Phương án khác đã xét: Coverage = correct presence. Không chọn cho pilot vì nó tái gộp coverage với correctness.
Ảnh hưởng: `03_research/WAVE2A_DECISION_GATE.md` W2A-05; `07_experiments/pilot/PILOT_PLAN_WAVE2A.md` SQ-P1; schema/metric future chỉ sau khi RQ07 được mở.

## D-016 — Option C là working policy cho coverage × `slide_type` trong pilot

Ngày: 2026-08-15 (Wave 2A Human Review)
Decision: Pilot dùng policy categorical hai layer: `universal_core` + `mode_required` + `optional/not_expected`. Báo riêng `universal_core_coverage`, `mode_required_coverage` và diagnostic presence của `optional/not_expected`; **không aggregate** thành một score. Đây là working product/evaluation policy cho pilot, **không claim là external best practice** và chưa freeze thành final benchmark policy.
Vì sao: Cần đồng thời giữ một source-quality floor chung và cho phép information selection phụ thuộc purpose của mode, nhưng tránh continuous weight dễ tune theo output. Categorical policy vẫn cần author/review/freeze trước run.
Phương án khác đã xét: Option A (cùng fact/cùng weight) và Option B (continuous weight theo mode); giữ trong `SOURCE_COVERAGE_VS_SLIDE_TYPE.md` làm alternatives, không chọn cho pilot hiện tại.
Ảnh hưởng: `03_research/cross_rq/SOURCE_COVERAGE_VS_SLIDE_TYPE.md`; `06_design/drafts/SLIDE_TYPE_BEHAVIOR_SPEC.md`; Pilot B0. Không mở RQ07.

## D-017 — Core của `slide_type` là purpose + information selection; surface feature là diagnostic

Ngày: 2026-08-15 (Wave 2A Human Review)
Decision: Trong pilot version hiện tại, semantic/core distinction của `slide_type` ưu tiên **purpose** và **information selection**. `text density`, `image ratio`, `layout`, `compression` và các surface observable khác là **SECONDARY/DIAGNOSTIC**, trừ khi team sau này explicit promote một feature thành CORE. `speaker_notes` giữ FR-55=P3 và **không** là core feature của `speaker_support` trong pilot hiện tại.
Vì sao: RQ03 không tìm thấy external truth cho ba mode; X+Slides chỉ là precedent gần và chỉ ra information selection, không định nghĩa profile Deck Agent. Surface feature dễ game và chỉ chứng minh output khác, không chứng minh purpose đúng. Dùng `speaker_notes` làm core khi FR-55=P3 sẽ âm thầm đổi product priority.
Phương án khác đã xét: coi text/image/density theo FR-20 hoặc `speaker_notes` là core ngay. Không chọn cho pilot version hiện tại.
Ảnh hưởng: `06_design/drafts/SLIDE_TYPE_BEHAVIOR_SPEC.md`; Pilot B0/B1. Định nghĩa cụ thể teaching/catchup/speaker_support vẫn là human blocker.

## D-018 — Resolve Q-014 bằng cách merge experiment trùng; ablation riêng phải là intervention khác

Ngày: 2026-08-15 (Wave 2A Human Review)
Decision: Nếu ablation A1 `without ContentPlanner` có implementation `Extractor output → ONE prompt → Deck IR`, nó **được merge vào B-1** dưới tên **Single-shot planning baseline**; không chạy/báo cáo như hai evidence độc lập. Nếu cần ablation riêng, phải định nghĩa intervention khác rõ ràng — candidate: giữ full ContentPlanner nhưng bỏ explicit plan/outline artifact — và ghi câu hỏi causal riêng trước implementation.
Vì sao: Hai experiment có cùng intervention không tạo hai evidence. Merge giữ claim đúng của B-1; ablation riêng chỉ có ý nghĩa khi thay một cơ chế khác và giữ phần còn lại cố định.
Câu hỏi của từng experiment:
- **B-1 / A1 merged — Single-shot planning baseline:** “Việc tách một planning stage rõ ràng có cải thiện end-to-end output so với một LLM call trực tiếp từ Extractor output sang Deck IR không?”
- **Candidate ablation riêng — `without explicit plan/outline artifact`:** “Trong một full ContentPlanner, explicit plan/outline artifact có đóng góp vượt phần planner còn lại không?” Đây chưa phải experiment đã bắt buộc implement; chỉ là definition hợp lệ nếu team cần ablation riêng.
Ảnh hưởng: Q-014 resolved; `06_design/BASELINE_SPEC.md`; mọi experiment registry/config sau này.

## D-019 — Source-quality pilot dùng SQ-P1…SQ-P4 và tách trách nhiệm detection/classification

Ngày: 2026-08-15 (Wave 2A Human Review)
Decision: Rename Pilot A1–A4 thành `SQ-P1`…`SQ-P4` để không trùng ablation A1. `SQ-P1` chỉ smoke-test supported/coverage/unsupported/derived routing: O0 supported; O1 coverage giảm; O2/O3 unsupported; O4 không bị kết tội và vào `derived_number_queue`. `SQ-P1` **không** phải tách hallucination khỏi incorrectness; việc đó chỉ thuộc `SQ-P4`.
Vì sao: Grounding detector nhị phân được kỳ vọng cho O2/O3 cùng signal `unsupported`. Đòi SQ-P1 phân loại hai lỗi sẽ tạo hard-fail mâu thuẫn với chính architecture hai bước của RQ02 và làm lẫn với ablation A1.
Ảnh hưởng: `07_experiments/pilot/PILOT_PLAN_WAVE2A.md`; W2A-08 vẫn HOLD tới SQ-P4.

## D-020 — Pilot `slide_type` chạy B0 smoke trước; B1 confirmatory chỉ freeze sau B0

Ngày: 2026-08-15 (Wave 2A Human Review)
Decision: Tách Pilot B thành:
- **B0 — Smoke/feasibility:** source set nhỏ; same source × 3 mode × repeats; descriptive WITHIN vs BETWEEN; feature-level direction/error analysis; không dùng confirmatory p-value/CI làm project evidence.
- **B1 — Confirmatory:** chỉ thiết kế/freeze sau khi B0 cho thấy signal đáng theo; khi đó mới chọn sample size, freeze permutation/bootstrap, threshold và classifier evaluation.

Các threshold `0,80`, `70%`, `80%`, `accuracy ≥0,50`, `p<0,05` trong draft hiện chỉ là **candidate engineering gates**, không phải accepted requirement hay external standard. Với smoke fixture nhỏ, explicit expected-case behavior được ưu tiên hơn diễn giải phần trăm như statistical evidence.
Vì sao: B0 phải kiểm feasibility và hình dạng signal trước khi giả định effect size/sample size cho confirmatory design. Dùng full confirmatory machinery để chặn smoke test vừa tốn kém vừa tạo vẻ rigor giả trên sample rất nhỏ.
Ảnh hưởng: `07_experiments/pilot/PILOT_PLAN_WAVE2A.md`; B1 chưa được phép freeze/chạy. Human sample size Q-013 vẫn HOLD/TBD.
