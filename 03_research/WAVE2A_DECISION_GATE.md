# Wave 2A Decision Gate — human review đã áp dụng

**Ngày lập:** 2026-08-15  
**Phạm vi:** RQ02 (source quality) + RQ03 (`slide_type`)  
**Trạng thái:** **HUMAN REVIEW + CONSISTENCY REVIEW APPLIED — PILOTS NOT RUN**  
**Decision record mới:** D-015 → D-020 (`05_decisions/DECISION_LOG.md`)

File này **không tóm tắt lại** RQ02/RQ03. Nó chuyển các finding thành disposition và ghi human review ngày 2026-08-15. Các mục có link D-015…D-020 là decision đã được promote đúng phạm vi; các mục còn `HOLD` vẫn chưa phải project decision.

## 0. Cách đọc và nguồn authority

- `PROJECT SOURCE` — yêu cầu/kiến trúc nội bộ; xác lập điều team hiện đề xuất build, không xác lập technical truth.
- `EXTERNAL EVIDENCE` — nguồn E1/E2 đã ghi trong `03_research/RQ02/evidence.md` hoặc `03_research/RQ03/evidence.md`.
- `INFERENCE` — kết luận khi nối evidence với Deck Agent.
- `RECOMMENDATION` — hành động đề xuất, vẫn cần review.

**Nguồn trực tiếp của gate:**

- `03_research/RQ02/landscape.md`, `evidence.md`, `recommendation.md`
- `03_research/RQ03/landscape.md`, `evidence.md`, `recommendation.md`
- `03_research/WAVE1_SYNTHESIS.md`

**Cảnh báo verification còn hiệu lực:** hai nguồn then chốt của RQ03 — *X+Slides* và *Learning to Present* — mới được đọc qua HTML/tool summary, chưa verify lại từ PDF gốc. Vì vậy các con số cụ thể từ hai nguồn này **không được** đưa vào báo cáo bảo vệ trước khi hoàn thành RQ03 P-11. Cơ chế thí nghiệm được phép dùng làm candidate design; con số chưa được dùng làm threshold.

## 1. Bảng disposition

| ID | Candidate | Disposition đề xuất | Phạm vi chính xác |
| --- | --- | --- | --- |
| W2A-01 | Giữ riêng `coverage / unsupported / incorrectness` | **ACCEPT — confirmed (D-008, D-015)** | Giữ vector riêng; không cộng thành một score |
| W2A-02 | Source quality cần hai chiều | **ACCEPT — principle** | `deck → source` và `source → deck` |
| W2A-03 | Fact pool là basis cho coverage | **ACCEPT — working pilot policy (D-016)** | Dùng `universal_core/mode_required/optional/not_expected`; authoring method vẫn HOLD |
| W2A-04 | LLM generate candidate fact records + human review một lần + freeze | **HOLD — pending pilot** | Phải review riêng fact identity/topic key, gold claim và mode category; chưa có head-to-head evidence |
| W2A-05 | Coverage = topic/fact presence; correctness riêng | **ACCEPT — human decision (D-015)** | Present-but-wrong không phải missing; report incorrectness riêng |
| W2A-06 | MiniCheck là candidate daily grounding/correct-support evaluator | **HOLD — pending pilot** | **Không phải ACCEPT**; transfer sang slide deck chưa được validate |
| W2A-07 | MiniCheck reverse-direction cho presence coverage | **HOLD — conceptual conflict fixed** | Full gold claim làm hypothesis đo correct support/entailment, không đo thuần presence theo D-015 |
| W2A-08 | Tách hallucination/incorrectness bằng counterpart | **HOLD — pending pilot** | Inference mới của RQ02, chưa có precedent |
| W2A-09 | Derived numbers cần `derived_number_queue` riêng | **ACCEPT — pilot routing rule (D-019)** | Queue không tự chứng minh phép tính đúng |
| W2A-10 | FR-02 được evaluate như staged manipulation check | **ACCEPT — pilot design (D-020)** | B0 descriptive trước; B1 confirmatory chỉ freeze sau B0 |
| W2A-11 | Không tạo một `Structure Score` chung | **ACCEPT — principle** | Tách hard constraint, contrast và quality judgment |
| W2A-12 | `slide_type` cần same-source paired design | **ACCEPT — human-confirmed (D-020)** | Same source × 3 mode × repeats |
| W2A-13 | Đo descriptive WITHIN/BETWEEN ở B0 trước B1 | **ACCEPT — staged validity rule (D-020)** | B0 không dùng p-value/CI làm project evidence |
| W2A-14 | Team viết expected behavior spec trước khi test | **ACCEPT — governance condition (D-017)** | Specific mode definitions vẫn cần team review |
| W2A-15 | Coverage conditioned theo `slide_type` bằng Option C | **ACCEPT — working pilot policy (D-016)** | Metric riêng; không aggregate; chưa là external/final best practice |
| W2A-16 | Forced-choice recovery test | **HOLD — pending pilot** | Candidate later check; phải đo noise và kiểm chéo length cue |
| W2A-17 | `speaker_notes` giữ P3, không core trong pilot | **ACCEPT — human decision (D-017)** | `speaker_support` phải phân biệt bằng purpose/information selection + feature khác |

Không có candidate nào bị `REJECT` ở gate này. `REJECT` chỉ được dùng khi approach đã được research và có quyết định loại; ở đây các phần chưa chắc được giữ ở `HOLD` hoặc `OPEN`.

---

## 2. Review từng candidate

### W2A-01 — Giữ riêng `coverage / unsupported / incorrectness`, không gộp score

**Finding / Claim**  
`RECOMMENDATION`, kế thừa decision D-008: source quality phải được báo cáo dưới dạng các signal riêng cho **bỏ sót**, **không được source hỗ trợ**, và **nói sai**. `unsupported` là signal trung gian; chỉ được gọi là `hallucination` hay `incorrectness` sau khi bước phân loại W2A-08 được validate.

**Disposition đề xuất**  
**ACCEPT — principle.** Không accept công thức cụ thể hay evaluator cụ thể.

**Evidence strength**  
**Cao cho việc không gộp; trung bình cho tên/ranh giới vận hành.** `EXTERNAL EVIDENCE`: *All Claims Are Equal* cho thấy FActScore precision-only chấm response thiếu ý chính 82,75%, cao hơn response đúng/đủ 82,58%; FineSurE tách faithfulness/completeness/conciseness thành các phép đo riêng. `PROJECT DECISION`: G-02/D-008 đã accept working distinction bịa/sai/bỏ sót.

**Reason**  
`INFERENCE`: một score gộp không cho biết regression đến từ bỏ ý, bịa ý hay làm sai giá trị; ba failure cần hành động sửa khác nhau. Gộp score sẽ phá khả năng chẩn đoán của development evaluation.

**What changes if accepted?**  
Sau human approval, cập nhật `06_design/EVALUATION_FRAMEWORK.md` và tạo các entry riêng trong `06_design/METRIC_REGISTRY.md`; report/harness phải lưu vector, không tạo `Content Fidelity Score` tổng hợp.

**What must be tested before acceptance?**  
Không cần pilot thêm để accept **principle không gộp**. Công thức grounding vẫn phải qua SQ-P1; mapping `unsupported → hallucination/incorrectness` chỉ được kiểm ở SQ-P4 trước khi đăng ký metric.

### W2A-02 — Source quality cần hai chiều `deck → source` và `source → deck`

**Finding / Claim**  
`INFERENCE + EXTERNAL EVIDENCE`: pass `deck → source` duyệt claim được nói ra nên đo precision/grounding; pass `source → deck` duyệt fact cần có nên đo recall/coverage. Một pass không thể quan sát tập của pass kia.

**Disposition đề xuất**  
**ACCEPT — principle.**

**Evidence strength**  
**Cao.** FineSurE (ACL 2024, E1) triển khai hai task ngược chiều: fact checking và keyfact alignment; *All Claims Are Equal* cho negative result trực tiếp của metric precision-only đối với omission.

**Reason**  
`INFERENCE`: giới hạn nằm ở tập được duyệt, không phải chỉ ở model. Khi deck không nói D/E, pass duyệt claim deck không có item nào để kiểm D/E.

**What changes if accepted?**  
`EVALUATION_FRAMEWORK.md`, `METRIC_REGISTRY.md` và interface evaluator sau này phải nhận cả source-side criteria lẫn deck-side claim unit; `BENCHMARK_SPEC.md` của RQ07 phải có dữ liệu source-side tương ứng nếu W2A-03/04 được accept.

**What must be tested before acceptance?**  
Không cần pilot để accept cấu trúc hai chiều. SQ-P1 cần hai signal độc lập cho presence và grounding; SQ-P2 chỉ kiểm candidate auxiliary cho `correctly_supported_fact`, không validate presence coverage.

### W2A-03 — Dùng fact pool làm basis cho coverage

**Finding / Claim**  
`HUMAN DECISION D-016`: pilot dùng fact pool categorical gồm `universal_core`, `mode_required`, `optional/not_expected`; presence được báo theo từng layer.

**Disposition đề xuất**  
**ACCEPT — working pilot policy.** Cách tự động sinh/review fact pool ở W2A-04 vẫn HOLD.

**Evidence strength**  
**Trung bình.** `EXTERNAL EVIDENCE`: FineSurE keyfact alignment đạt completeness correlation cao hơn generic judges; AbsenceBench cho thấy hỏi mở về omission yếu. Nhưng FineSurE lấy keyfact từ reference summary/human, không từ source PDF cho deck; không nguồn nào validate granularity/salience cho Deck Agent.

**Reason**  
`INFERENCE`: danh sách đóng là hình thái phù hợp cho coverage. Tuy nhiên mỗi record phải tách `fact_identity/topic_key` khỏi `gold_claim`; nếu dùng một full-claim string cho cả hai, present-but-wrong sẽ bị gộp lại thành missing. D-016 xử lý conflict mode bằng category thay vì continuous weight, nhưng chất lượng authoring/granularity vẫn chưa được validate.

**What changes if accepted?**  
Pilot B0 dùng working schema categorical và metric riêng. Conceptual record tối thiểu gồm: (1) `fact_identity/topic_key` cho presence, (2) `gold_claim` cho correctness/grounding, và (3) `mode_expectation` cho `universal_core | required | optional | not_expected`. Không bắt buộc một YAML implementation cụ thể. **Không mở/sửa RQ07** trong bước này; final benchmark schema vẫn để Wave 3.

**What must be tested before acceptance?**  
SQ-P1 phải có fixture/oracle presence độc lập khỏi correctness; hiện presence detector tự động vẫn `OPEN/HOLD`. SQ-P2 **không** lấp gap này. Trước B0, team phải author/review fact identity, gold claim và category mà không nhìn output. W2A-04 vẫn cần pilot nếu muốn LLM generate fact pool.

### W2A-04 — LLM generate candidate fact records + human review một lần + freeze

**Finding / Claim**  
`RECOMMENDATION`: LLM trích candidate fact records từ source, một human review/sửa riêng `fact_identity/topic_key`, `gold_claim` và provenance; mode category được author/review theo D-016. Sau đó freeze; không sinh lại mỗi run.

**Disposition đề xuất**  
**HOLD — pending pilot.**

**Evidence strength**  
**Trung bình cho mô hình human-in-the-loop; yếu cho workflow cụ thể.** FineSurE, CLEAR/ExpertLongBench và HealthBench tạo precedent cho phân vai người định nghĩa/duyệt criteria và model đối chiếu. Không có so sánh head-to-head định lượng giữa criteria người viết và LLM sinh cho Deck Agent.

**Reason**  
`INFERENCE`: freeze ngăn thước đo thay đổi theo output/version; human review giảm chi phí so với expert authoring toàn bộ. Nhưng chưa biết extraction có bỏ sót/sai granularity hoặc bias theo mode không.

**What changes if accepted?**  
`BENCHMARK_SPEC.md` cần authoring workflow, reviewer/provenance, schema version và policy invalidation; `EVALUATION_PIPELINE.md` phải cấm runtime regeneration của criteria.

**What must be tested before acceptance?**  
Sinh criteria độc lập ít nhất hai lần cho cùng source, đo overlap sau semantic matching, review lỗi bỏ/sai/thừa, và đo effort người thật. Ngưỡng acceptance phải được pre-register trước pilot, không suy ra sau khi xem output.

### W2A-05 — Coverage đo topic presence hay correct presence?

**Finding / Claim**  
`HUMAN DECISION D-015`: coverage đo topic/fact presence. Fact có mặt nhưng sai vẫn là present; incorrectness được báo riêng.

**Disposition đề xuất**  
**ACCEPT — human decision.**

**Evidence strength**  
**Không có external truth trực tiếp; authority là human product/evaluation decision.** Evidence chỉ support việc tách failure, không tự quyết semantics.

**Reason**  
Topic/fact presence giữ coverage độc lập với correctness và tránh đếm cùng một present-but-wrong fact vừa missing vừa incorrect.

**What changes if accepted?**  
SQ-P1 oracle phải coi O3 là present cho coverage nhưng unsupported cho grounding. SQ-P2 có thể kiểm một signal `correctly_supported_fact` phụ; chỉ SQ-P4 mới thử gắn subtype `incorrectness` thay vì `hallucination`. Future metric/schema phải giữ presence và correctness/grounding thành field riêng.

**What must be tested before acceptance?**  
Không cần test thêm để accept semantics. Pilot chỉ kiểm implementation có tuân decision này không.

### W2A-06 — MiniCheck là candidate daily evaluator

**Finding / Claim**  
`RECOMMENDATION`: MiniCheck là F2 candidate duy nhất của RQ02 đáng đưa vào pilot cho daily source grounding.

**Disposition đề xuất**  
**HOLD — pending pilot. MiniCheck KHÔNG được ACCEPT ở gate này.**

**Evidence strength**  
**Trung bình trong summarization/grounding; thấp khi transfer sang slide deck.** MiniCheck là E1 peer-reviewed, có official repo, được stress-test tương đối bền và phản ứng với factual repair. Nhưng hai vòng search độc lập tìm **0 nguồn** áp MiniCheck/AlignScore/SummaC/FActScore lên slide deck; bullet nén, fragment, multi-slide context và bảng/hình là domain gap thật.

**Reason**  
`INFERENCE`: MiniCheck đáng thử hơn AlignScore/SummaC/FActScore, nhưng “candidate tốt nhất trong nhóm” không đồng nghĩa “đủ tin cho Deck Agent”. Dùng nó trước pilot sẽ biến một working hypothesis thành evaluator production.

**What changes if accepted?**  
Chỉ sau pilot pass mới được thêm MiniCheck vào `METRIC_REGISTRY.md` hoặc development cadence. AlignScore/SummaC/FActScore không được tự động quay lại làm fallback nếu MiniCheck fail; cần quyết định mới.

**What must be tested before acceptance?**  
SQ-P1 kiểm pipeline có giữ độc lập supported/presence/unsupported/derived routing; SQ-P2 chỉ kiểm reverse MiniCheck như candidate auxiliary `correctly_supported_fact`; SQ-P3 kiểm numeric transformations; kèm repeatability/noise floor và runtime trên hạ tầng thật của team. SQ-P1 chỉ cần O2/O3 đều là `unsupported`; **không** được yêu cầu bước này tách hallucination khỏi incorrectness. Chưa có presence detector tự động nào được select/validate.

### W2A-07 — Reverse MiniCheck không mặc định là presence detector

**Finding / Claim**  
`INFERENCE từ D-015`: dùng text deck làm premise và **full `gold_claim`** làm hypothesis trả lời “deck có support đúng claim này không?”. Contradicted và absent đều có thể ra `NOT_SUPPORTED_BY_DECK`, nên signal đó đo correct support/entailment, không tách được topic presence theo D-015.

**Disposition đề xuất**  
**HOLD cho presence coverage.** Giữ reverse MiniCheck chỉ như candidate auxiliary cho `correctly_supported_fact`, pending SQ-P2; không promote thành presence detector.

**Evidence strength**  
**Thấp.** Không có nguồn external validate chiều này. MiniCheck được huấn luyện với grounding document làm premise; deck text ngắn/dạng bullet là chế độ ngoài phân phối huấn luyện.

**Reason**  
`INFERENCE`: reuse cùng model rẻ và hấp dẫn về hạ tầng, nhưng ngoài domain-transfer risk, label semantics còn không khớp D-015. Một contradicted fact vẫn present theo topic identity nhưng không entail full gold claim. Không được suy `NOT_SUPPORTED_BY_DECK` thành `missing`.

**What changes if accepted?**  
Nếu SQ-P2 đủ sane, output chỉ được gọi là candidate `correctly_supported_fact`/gold-claim support signal và báo riêng với presence. `critical_fact_coverage` vẫn `OPEN/HOLD`; Wave 2A chưa chọn candidate presence method và không tự động chuyển sang source-derived QA/probe, lexical matching hay method khác để lấp gap.

**What must be tested before acceptance?**  
SQ-P2 dùng correctly supported verbatim/paraphrased/compressed, topic-present-but-contradicted và topic-absent. Oracle phải lưu **hai label riêng**: manual presence theo `fact_identity/topic_key`, và gold-claim support theo `gold_claim`. SQ-P2 chỉ evaluate label thứ hai; các threshold phần trăm vẫn chỉ là candidate engineering gate. Một presence method riêng phải được đề xuất, review và freeze trước SQ-P1; hiện trạng là `OPEN/HOLD`.

### W2A-08 — Phân biệt hallucination vs incorrectness bằng counterpart trong source

**Finding / Claim**  
`RECOMMENDATION`: với claim `unsupported`, nếu source có counterpart cùng subject+attribute thì gán candidate `incorrectness`; nếu không có counterpart thì candidate `hallucination`.

**Disposition đề xuất**  
**HOLD — pending SQ-P4.**

**Evidence strength**  
**Thấp.** Đây là inference thuần của RQ02; không có precedent validate. FineSurE chỉ đạt 42,2% khi localize loại lỗi, cho thấy phân loại chi tiết khó hơn detect lỗi.

**Reason**  
Ranh giới nghe hợp lý nhưng mơ hồ ở semantic neighborhood: `Revenue` vs `ARR`, tổng công ty vs một business unit, cùng metric khác period. Force mọi case thành hai lớp sẽ tạo false certainty.

**What changes if accepted?**  
Harness mới được phép báo `hallucination_count` và `incorrectness_count` tự động. Trước đó chỉ được báo `unsupported_count` + queue cần review.

**What must be tested before acceptance?**  
SQ-P4 phải viết rule trước dữ liệu, có nhãn manual cho hallucination/incorrectness/ambiguous, có cơ chế abstain, và đo riêng lỗi class collapse với false certainty trên ambiguous cases.

### W2A-09 — Derived numbers cần `derived_number_queue` riêng

**Finding / Claim**  
`RECOMMENDATION`: số trên deck không match source sau normalization phải đi vào queue riêng; không tự động đếm là hallucination/incorrectness.

**Disposition đề xuất**  
**ACCEPT — human-confirmed pilot routing rule (D-019), không accept khả năng adjudicate tự động.**

**Evidence strength**  
**Trung bình cho nhu cầu tách riêng; thấp cho implementation cuối.** EQUATE (E1, 2019) cho thấy quantitative reasoning là năng lực tách khỏi textual entailment; stress testing cho thấy claim nén là vùng khó. Không có phép đo hiện đại trực tiếp trên deck, và MiniCheck chưa được test trên transformations số.

**Reason**  
Một derived fact đúng như `12.4M → 18.7M` thành `~51%` không được source chứa verbatim. Nếu xem mọi số unmatched là bịa, evaluator sẽ phạt transformation đúng; nếu bỏ qua, lỗi tính toán sẽ lọt. Queue là boundary an toàn giữa hai lỗi.

**What changes if accepted?**  
`EVALUATION_PIPELINE.md`/`METRIC_REGISTRY.md` sau human approval phải phân biệt `unmatched_number_count`, `derived_number_queue_size` và verdict sau review; queue size là diagnostic signal, **không phải correctness score**.

**What must be tested before acceptance?**  
Principle tách queue đã được human confirm. SQ-P3 quyết định MiniCheck/NLI có được phép hỗ trợ triage queue hay không; nếu fail, queue vẫn tồn tại và cần deterministic calculator/human review.

### W2A-10 — Evaluate FR-02 như staged manipulation check

**Finding / Claim**  
`HUMAN DECISION D-020`: đổi `meta.slide_type` là manipulation được kiểm theo hai stage — B0 smoke/descriptive trước, B1 confirmatory chỉ freeze nếu B0 cho signal đáng theo.

**Disposition đề xuất**  
**ACCEPT — staged pilot design.**

**Evidence strength**  
**Trung bình–cao.** Hoewe (E2) cung cấp khung manipulation check; X+Slides (E1) dùng cross-condition scoring trên slide generation và cho thấy conditioning có thể đi sai hướng; logic quan hệ xuất phát trực tiếp từ FR-02.

**Reason**  
FR-02 là claim về quan hệ giữa nhiều output, không phải quality score của một deck. Manipulation-check framing khiến claim có điều kiện đối chứng và có thể fail.

**What changes if accepted?**  
Pilot plan dùng comparison unit same source × 3 mode × repeats. Chưa sửa final framework hay mở RQ07.

**What must be tested before acceptance?**  
B0 kiểm feasibility/noise/signal description. Chỉ sau human review B0 mới thiết kế/freeze sample size, threshold, permutation/bootstrap và classifier cho B1.

### W2A-11 — Không tạo một `Structure Score` chung

**Finding / Claim**  
`RECOMMENDATION`: tách “số slide” thành deterministic brief compliance, “hợp với mode” thành ba manipulation checks, và không dùng judge pointwise 1–5 cho “cách chia nội dung hợp lý” trong dev tracking.

**Disposition đề xuất**  
**ACCEPT — principle.**

**Evidence strength**  
**Trung bình–cao.** PPTEval Coherence có human correlation thấp hơn rõ rệt so với Design; JudgeSense ghi coherence bất ổn; IFEval hỗ trợ kiểm hard constraint bằng code. Đây là evidence cho việc tách method, không chứng minh mọi human/judge structure evaluation vô dụng.

**Reason**  
Một score gộp xóa khác biệt giữa failure “không đổi mode”, “đổi sai hướng”, “đổi đúng nhưng quá nhỏ” và “vi phạm target slide count”. Những failure này có owner/hành động sửa khác nhau.

**What changes if accepted?**  
Không đăng ký `Structure Score` trong `METRIC_REGISTRY.md`; sửa candidate row “Planner structure” trong `EVALUATION_FRAMEWORK.md` thành nhóm target/method tách rời sau human approval.

**What must be tested before acceptance?**  
Không cần pilot cho nguyên tắc không gộp. Nếu team vẫn muốn một judged structure measure cho thesis, phải dùng protocol riêng (pairwise + human calibration), không dùng nó làm daily regression signal.

### W2A-12 — `slide_type` cần same-source paired design

**Finding / Claim**  
`RECOMMENDATION`: mọi contrast được tính trong cùng source; không so trung bình mode trên các source khác nhau.

**Disposition đề xuất**  
**ACCEPT — human-confirmed design principle (D-020).**

**Evidence strength**  
**Cao về validity logic; trung bình về effect-size precedent.** Source length/topic tạo variance lớn; X+Slides/Hu & Collier cho thấy conditioning effect có thể nhỏ. Paired design loại confounder source khỏi contrast chính.

**Reason**  
Nếu teaching chạy PDF ngắn còn catchup chạy PDF dài, difference không quy được cho `slide_type`. Đây là lỗi thiết kế, không thể sửa bằng judge tốt hơn.

**What changes if accepted?**  
RQ07 sau này phải lưu ba condition cho từng source; B0 và final comparison phải group theo `source_id`. Nếu B1 có classifier, split candidate là leave-one-source-out để tránh topic leakage; chưa freeze classifier ở B0.

**What must be tested before acceptance?**  
Không cần pilot để accept pairing. B0 mô tả variance decomposition và chi phí generation thực tế; B1 chỉ freeze inference design sau review B0.

### W2A-13 — Đo WITHIN trước khi đọc BETWEEN

**Finding / Claim**  
`RECOMMENDATION`: `WITHIN` (cùng source, cùng mode, nhiều run) là generator noise floor; chỉ đọc `BETWEEN` như mode signal khi vượt `WITHIN`.

**Disposition đề xuất**  
**ACCEPT — staged validity condition (D-020).**

**Evidence strength**  
**Cao cho nguyên tắc, chưa có con số Deck Agent.** Atil et al. chứng minh nondeterminism tồn tại ngay trong cấu hình được kỳ vọng deterministic; RQ03 không tìm thấy nguồn đo run-to-run variance của slide generator, nên magnitude phải tự đo.

**Reason**  
Không có R>1 thì H0 “hai run vốn luôn khác” không có dữ liệu đối chứng. `BETWEEN` đơn độc không thể hỗ trợ causal interpretation cho `slide_type`.

**What changes if accepted?**  
Mọi manifest/experiment theo condition phải có repeat policy; thay generator model hoặc prompt template làm invalid noise calibration cũ và buộc hiệu chuẩn lại.

**What must be tested before acceptance?**  
Không cần pilot cho principle. B0 mô tả `WITHIN/BETWEEN`, chọn R smoke và kiểm feasibility. B0 **không** dùng p-value/CI làm project evidence; B1 sample size/statistics chỉ freeze sau B0. **Không freeze R=5 thành law.**

### W2A-14 — Team viết expected behavior spec trước khi test

**Finding / Claim**  
`RECOMMENDATION`: expected direction của ba mode phải được team/giảng viên khai báo, version hóa và freeze trước khi quan sát B0.

**Disposition đề xuất**  
**ACCEPT — governance condition (D-017).** Purpose + information selection là core hierarchy; definition cụ thể vẫn cần team review.

**Evidence strength**  
**Cao về logic falsifiability; external profile evidence không tồn tại.** Không nguồn nào định nghĩa ba nhãn của Deck Agent. Manipulation check chỉ có nghĩa khi “expected difference” tồn tại trước measurement.

**Reason**  
Viết spec sau khi xem output cho phép giải thích mọi difference là intentional, khiến phép đo không thể fail. Đây là confirmation bias ở tầng protocol.

**What changes if accepted?**  
`06_design/drafts/SLIDE_TYPE_BEHAVIOR_SPEC.md` trở thành prerequisite của B0. Core hierarchy đã chốt, nhưng draft chưa được dùng như truth cho tới khi teaching/catchup/speaker_support và Option C categories được team điền/review.

**What must be tested before acceptance?**  
Không pilot nào thay team định nghĩa product. Human/team vẫn phải hoàn tất các definition/sign/core fact category cụ thể. Conflict hierarchy và `speaker_notes` đã resolve ở D-017.

### W2A-15 — Coverage/importance có thể cần condition theo `slide_type`

**Finding / Claim**  
`HUMAN DECISION D-016`: dùng Option C làm working pilot policy: `universal_core`, `mode_required`, `optional/not_expected`; báo metric riêng và không aggregate.

**Disposition đề xuất**  
**ACCEPT — working product/evaluation policy cho pilot.** Không claim external best practice hoặc final benchmark policy.

**Evidence strength**  
**Trung bình cho existence của conflict; authority của Option C là human review.** X+Slides là precedent cho conditioning nhưng không định nghĩa ba mode. Option C là project policy, không phải finding external.

**Reason**  
RQ02 source frame và RQ03 brief frame có thể gán nhãn đối lập cho cùng omission. Categorical dual layer giữ source floor và mode intent riêng, đồng thời tránh continuous weight; vẫn có risk relabel fact khó thành optional.

**What changes if accepted?**  
B0 working data cần category per fact + provenance/reviewer/version. Report tối thiểu `universal_core_coverage` và `mode_required_coverage` riêng; `optional/not_expected` là diagnostic. **Không mở/sửa RQ07** ở bước này.

**What must be tested before acceptance?**  
Option đã được chọn cho pilot. Trước B0, team vẫn phải định nghĩa mode cụ thể, author category trước khi nhìn output và review agreement/stability. Final policy còn có thể revisit sau pilot.

### W2A-16 — Forced-choice recovery test là candidate

**Finding / Claim**  
`RECOMMENDATION`: giấu `meta.slide_type`, cho evaluator chọn một trong ba mode; chance = 1/3. Dùng classifier deterministic 3a trước, MLLM 3b ở later pilot.

**Disposition đề xuất**  
**HOLD — pending pilot.**

**Evidence strength**  
**Trung bình cho task form; thấp cho reliability trong domain.** Learning to Present có inverse specification precedent; Chen et al. cho thấy judge tốt hơn ở comparison/lựa chọn so với absolute scoring. Không nguồn nào validate classifier/judge cho ba nhãn này.

**Reason**  
Recovery test hỏi trực tiếp difference có nhận ra được không, nhưng có thể chỉ đọc length cue. Nếu 3a và 3b ngang nhau, 3b không thêm semantic evidence.

**What changes if accepted?**  
Sau pilot pass, 3a có thể vào nightly manipulation check; 3b chỉ vào milestone/tập nhỏ và phải lưu prompt/model/noise floor.

**What must be tested before acceptance?**  
Chỉ sau khi B0 cho signal đáng theo mới thiết kế B1 classifier candidate: leave-one-source-out, source-grouped permutation, feature/error analysis. MLLM forced-choice là later optional check. Mọi threshold/significance rule phải được review và pre-register ở B1; không dùng classifier để chặn B0.

### W2A-17 — `speaker_notes` không là core distinguishing feature khi FR-55 vẫn P3

**Finding / Claim**  
`HUMAN DECISION D-017`: `speaker_notes` giữ FR-55=P3 và không là core của `speaker_support` trong pilot hiện tại.

**Disposition đề xuất**  
**ACCEPT — pilot scope decision.**

**Evidence strength**  
**Cao cho conflict nội bộ; không có external truth cho cách giải.** RQ03 không tìm thấy nguồn evaluate `speaker_notes`; SlideTailor sinh speech nhưng không đánh giá nó.

**Reason**  
Đây là lựa chọn scope sản phẩm: hoặc notes là core thì phải nâng priority, hoặc giữ P3 thì mode phải được định nghĩa bằng signal khác. Evaluation không được âm thầm thay FR priority.

**What changes if accepted?**  
Behavior spec ghi `speaker_notes = P3/non-core`; B0/B1 không dùng notes làm CORE. Nếu team đổi priority sau này, phải tạo decision/version mới.

**What must be tested before acceptance?**  
Không cần test thêm cho pilot scope hiện tại. Nếu sau này promote notes thành core, cần decision + pilot riêng vì hiện không có precedent.

---

## 3. Ràng buộc Wave 1 vẫn còn hiệu lực

Gate này **không thay thế** các quyết định trước:

- **G-03 / Q-011 = HOLD:** taxonomy L0–L5 chưa freeze. RQ03 tìm thêm ba chỗ gãy, đặc biệt thiếu contrastive reference frame.
- **G-05 / Q-012 = HOLD:** MiniCheck chỉ là candidate grounding/correct-support signal; W2A-06/W2A-07 không đóng G-05 và không cung cấp presence detector.
- **G-07 / Q-013 = HOLD:** cỡ mẫu human vẫn `TBD`; không biến candidate scale thành requirement.
- **G-09 / D-011 = ACCEPT:** mọi model-based evaluator phải đo noise floor; RQ03 mở rộng logic này sang generator comparison.
- **R-001:** không dùng “judge ≥80% human ceiling” làm pass/fail.
- **R-002:** B-1 chỉ chứng minh giá trị của planning-stage separation, không chứng minh Deck IR.
- **Q-014 = RESOLVED → D-018:** intervention trùng được merge vào B-1; ablation riêng phải thay một cơ chế khác và hỏi câu causal khác. Source-quality pilot đã rename SQ-P1…SQ-P4 nên không còn collision tên.

## 4. Decision đã promote trong human review này

- D-015: coverage = topic/fact presence; correctness riêng.
- D-016: Option C là working policy cho pilot; metric riêng, không aggregate.
- D-017: purpose + information selection là CORE; surface feature diagnostic; notes P3/non-core.
- D-018: merge A1/B-1 nếu intervention trùng; ablation riêng phải khác.
- D-019: SQ-P1…SQ-P4; SQ-P1 không tách hallucination/incorrectness.
- D-020: B0 descriptive smoke trước; B1 confirmatory chỉ freeze sau B0; threshold hiện là candidate.

## 5. HUMAN/TEAM blocker còn lại

1. Định nghĩa cụ thể teaching/catchup/speaker_support về purpose và information selection, rồi operationalize purpose thành obligation/observable; core hierarchy đã chốt nhưng content chưa chốt (Q-015).
2. Author/review separated fact records (`fact_identity/topic_key`, `gold_claim`, `mode_expectation`) và Option C category cho source set B0 **trước khi nhìn output**.
3. Chọn/review một candidate presence detector hoặc explicit manual presence oracle contract dựa trên `fact_identity/topic_key`; hiện chưa có method tự động nào được accept. Sau đó mới review/freeze SQ smoke fixtures. MiniCheck vẫn HOLD và không thay thế presence detector.
4. Chọn source set, repeat count và exclusion/retry rule cho B0. B0 chỉ descriptive, chưa là project evidence.
5. Sau B0, human/team mới quyết định có signal đáng theo để thiết kế/freeze B1 hay không; sample size, threshold, permutation/bootstrap/classifier hiện chưa accepted.

Trạng thái đúng: **human review applied; pilot prerequisites pending; no pilot run**.
