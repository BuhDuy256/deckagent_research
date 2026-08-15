# Pilot Plan Wave 2A — Source Quality + `slide_type`

**Ngày thiết kế:** 2026-08-15  
**Trạng thái:** **CONSISTENCY REVIEW APPLIED — DESIGN ONLY — BLOCKED BEFORE SQ-P1/B0 — CHƯA ĐƯỢC PHÉP CHẠY**  
**Nguồn:** `03_research/RQ02/`, `03_research/RQ03/`, `03_research/WAVE2A_DECISION_GATE.md`

Hai pilot dưới đây độc lập về câu hỏi và artifact. Pilot SQ kiểm một candidate evaluator cho source quality. Pilot `slide_type` chạy B0 smoke trước; B1 confirmatory chỉ được thiết kế/freeze sau B0. Không dùng kết quả của pilot này làm ground truth cho pilot kia.

## 0. Quy tắc pre-registration chung

1. **Freeze trước run:** source/fixture, expected label, model/weights, prompt, threshold, seed policy, dependency version, metric formula, exclusion rule và pass/fail rule phải có hash/version trước lần chạy đầu tiên.
2. **Không sửa oracle theo output.** Nếu phát hiện oracle sai, dừng run, tạo version pre-registration mới và ghi lý do; không ghi đè bản cũ.
3. **Mọi model-based step phải đo repeatability/noise floor** theo G-09/D-011. `temperature=0` không được coi là bằng chứng determinism.
4. **Các threshold `0,80`, `70%`, `80%`, `0,50`, `p<0,05` chỉ là candidate engineering gate, chưa phải accepted requirement.** Với smoke fixture rất nhỏ, ưu tiên explicit expected-case behavior và error table; không diễn giải phần trăm/p-value như statistical evidence.
5. **Cỡ mẫu human vẫn `TBD`** theo G-07/Q-013. Fixture count nhỏ trong Pilot SQ là smoke-test engineering, không phải human-study sample-size claim.
6. **Lưu run manifest theo D-014**, gồm cả `generated_deck_ir` và `plan_outline_artifact`.
7. **Pilot có quyền fail.** Fail nghĩa là candidate/method chưa được promote; không phải lý do sửa threshold hoặc loại case khó.

### Trạng thái outcome dùng trong file này

- `PROCEED` — qua engineering gate đã pre-register; vẫn chưa tự động thành project decision.
- `HOLD` — signal chưa đủ hoặc interpretation còn phụ thuộc team decision.
- `STOP` — fail hard gate; không được promote candidate cho vai trò đang test.

---

# PILOT SQ — Source Quality Evaluator (RQ02)

## SQ.0 Mục tiêu và logic tổng

**Hypothesis tổng (`RECOMMENDATION`, chưa accept):** một pipeline xếp tầng có thể tạo signal riêng cho coverage, unsupported claim và incorrectness đủ rẻ để development tracking. MiniCheck chỉ là **candidate** trong pipeline này, vẫn `HOLD`.

**Mục tiêu thật:** tìm cách làm candidate **fail sớm và rẻ** trên fixture rõ ràng. Pilot không nhằm chứng minh recommendation đúng.

**Thứ tự:** SQ-P1 → SQ-P2 → SQ-P3 → SQ-P4. Tên `SQ-*` tránh trùng ablation A1/B-1 đã resolve ở D-018.

**Quyết định được unblock:**

- W2A-03/04: có tiếp tục theo fact record tách `fact_identity/topic_key` + `gold_claim` + mode expectation và frozen criteria không;
- W2A-06: MiniCheck có đủ sane như grounding/correct-support auxiliary để tiếp tục không, **không phải tự động ACCEPT**;
- W2A-07: reverse MiniCheck **không** được dùng mặc định cho presence coverage; presence detector vẫn `OPEN/HOLD`;
- W2A-08: có được báo riêng hallucination/incorrectness tự động không;
- W2A-09: derived-number queue phải vận hành ở mức nào;
- Q-012/G-05: chỉ có thể đóng sau human review kết quả pilot.

### SQ.0.1 Conceptual signal model — bắt buộc giữ tách biệt

Theo D-015, source-quality pipeline phải giữ ba object/signal khác nhau:

| Layer | Input target | Câu hỏi | Không được suy thành |
| --- | --- | --- | --- |
| Presence / coverage | `fact_identity/topic_key` | Deck có đề cập topic/fact identity này không? | Gold claim có đúng không? |
| Correctness / grounding | `gold_claim` + deck claim | Nội dung được nói có được source support không? | Topic vắng mặt |
| Error subtype | unsupported claim + source counterpart | Là hallucination, incorrectness hay ambiguous? | Presence/coverage |

`mode_expectation` (`universal_core | required | optional | not_expected`) gắn với fact identity và chỉ condition selection/coverage. Reverse MiniCheck với full `gold_claim` chỉ có thể là candidate cho layer correctness/grounding (`correctly_supported_fact`), không phải presence detector.

**OPEN/HOLD:** Wave 2A hiện chưa chọn hoặc validate một candidate tự động cho presence detection trên `fact_identity/topic_key`. Không được invent lexical matching, embedding threshold, QA probe hoặc method khác để lấp gap. Vì vậy SQ-P1 chưa được phép chuẩn bị preregistration/run cho tới khi team chọn rõ component presence nào đang được test và freeze oracle contract tương ứng.

## SQ-P1 — Grounding/coverage/derived-routing smoke test

### Hypothesis

**H-SQ-P1:** trên source cực nhỏ và output dựng tay, pipeline phải giữ độc lập bốn hành vi nền: nhận diện fact identity được present, phát hiện coverage giảm, đánh dấu claim không support gold content, và route số derived sang queue mà không kết tội. SQ-P1 **không** có trách nhiệm tách hallucination khỏi incorrectness; việc đó chỉ thuộc SQ-P4.

**Trạng thái:** `BLOCKED — PRESENCE METHOD OPEN/HOLD`. Bảng dưới đây là oracle/contract để review, chưa phải preregistration có thể chạy. Manual presence label chỉ định nghĩa expected behavior; nó không thay thế một detector tự động.

### Controlled variables

- Cùng một source `S`, cùng parser/source segmentation, cùng fact records đã tách `fact_identity/topic_key` và `gold_claim`.
- Freeze riêng version/config/threshold của từng component đã chọn (presence, grounding, number routing), cùng claim-unit rule và number normalizer; không giả định một evaluator/threshold phục vụ cả presence lẫn correctness.
- Không dùng render/visual; chỉ text để cô lập source-quality logic.
- Mỗi output chỉ khác nội dung fixture được khai báo; mọi metadata khác giống nhau.

### Input

Source synthetic gồm 5 fact record. Bảng chỉ minh họa conceptual split, không freeze một YAML implementation:

| ID | `fact_identity/topic_key` | `gold_claim` |
| --- | --- | --- |
| A | company expansion / geography + period | Công ty mở rộng sang Đông Nam Á trong Q2/2025. |
| B | revenue change + driver | Doanh thu tăng từ 12,4M lên 18,7M do enterprise adoption tăng tốc. |
| C | operating cost / change | Chi phí vận hành giữ nguyên ở 8,1M. |
| D | churn / change | Churn giảm từ 6,2% xuống 3,9%. |
| E | 2026 product plan | Kế hoạch 2026 là ra mắt sản phẩm self-serve. |

### Manipulated variable

Output text được dựng tay:

- `O0 = A B C D E` — correct.
- `O1 = A B C` — coverage gap D/E.
- `O2 = A B C D F`, với F = “Công ty huy động Series C 40M.” — hallucination. Theo fixture literal này, E cũng vắng; phải báo coverage riêng, không để omission che F.
- `O3 = A B C D'`, với D' = “Churn giảm từ 6,2% xuống 1,9%.” — incorrectness. Theo fixture literal này, E cũng vắng.
- `O4 = A B' C D E`, với B' = “Enterprise adoption drove ~51% revenue growth.” — transformed-but-correct; phép tính `(18,7−12,4)/12,4 ≈ 50,8%`.

**Fixture kiểm soát một biến bổ sung (khuyến nghị, không thay năm output bắt buộc):** tạo `O2_iso = A B C D E F` và `O3_iso = A B C D' E`. Hai fixture này cô lập claim unsupported khỏi việc E bị bỏ. Chúng chỉ là diagnostic; không dùng để yêu cầu SQ-P1 tách O2 khỏi O3.

### Expected output — pre-register trước run

D-015 đã chốt **coverage = topic/fact presence**; correctness được báo riêng. Vì vậy D' ở O3 vẫn tính là D đã xuất hiện, dù nội dung sai.

| Output | Coverage presence | Unsupported claim | Derived-number route | Expected behavior |
| --- | ---: | ---: | ---: | --- |
| O0 | `5/5` | 0 | 0 | Supported |
| O1 | `3/5` | 0 | 0 | Coverage giảm |
| O2 | `4/5` | ≥1 (`F`) | 0 final derived item | Unsupported; không cần gắn hallucination ở SQ-P1 |
| O3 | `4/5` (`D` có mặt; `E` vắng) | ≥1 (`D'`) | 0 final derived item | Unsupported; không cần gắn incorrectness ở SQ-P1 |
| O4 | `5/5` | 0 final conviction | 1 | Không kết tội; route `REVIEW_DERIVED` |

Ở O2/O3, raw number trace có thể được log để debug, nhưng không được biến claim unsupported thành accepted derived claim. `O4.Derived = 1` nghĩa là **đi vào queue**, không phải error verdict.

### Metric candidate

- candidate presence vector theo từng `fact_identity/topic_key` — **method hiện OPEN/HOLD**, chưa gọi là accepted `critical_fact_coverage`;
- `unsupported_claim_count`;
- `derived_number_queue_size`;
- explicit expected-case table theo output, không gộp thành score;
- correctness report riêng cho O3, nhưng **không** dùng report này để đổi coverage presence hoặc đòi SQ-P1 phân loại O3.

### Pass/fail pre-registered

**PASS / PROCEED khi đồng thời:**

1. O0 được nhận là supported và không có false positive;
2. `coverage(O1) < coverage(O0)`;
3. O2 có ít nhất một claim unsupported;
4. O3 có ít nhất một claim unsupported, đồng thời D vẫn được tính là present cho coverage;
5. O4 không bị kết tội hallucination/incorrectness/unsupported product defect và được route vào `derived_number_queue`;
6. kết quả lặp lại cùng cấu hình không đổi verdict.

**HARD FAIL / STOP:** một trong sáu expected behavior trên không đạt. Việc O2 và O3 cùng nhận nhãn `unsupported` ở SQ-P1 là **đúng phân vai**, không phải class collapse hay hard fail; chỉ SQ-P4 được phép kiểm tra tách hallucination/incorrectness.

### Failure interpretation

| Failure | Interpretation |
| --- | --- |
| O1 không giảm coverage | Pass source→deck không quan sát omission; W2A-03/07 tiếp tục HOLD |
| O2/O3 cùng chỉ ra `unsupported` | Expected ở SQ-P1; chỉ được báo riêng nếu SQ-P4 pass |
| O0 có false positive | Threshold/segmentation/claim-unit lỗi; candidate chưa usable |
| O4 bị kết tội | Textual evaluator không xử lý transformation số; queue là bắt buộc |
| Kết quả đổi giữa repeat | Chưa đo/kiểm soát noise; vi phạm G-09 |

### Artifacts cần lưu khi chạy

- `preregistration.yaml` + hash;
- source + O0–O4 + optional isolation fixtures;
- expected oracle table versioned;
- raw signal từng component, threshold/config tương ứng và final labels;
- environment/weights/dependency manifest của từng model-based component;
- repeatability outputs và một report giải thích mọi mismatch.

### Estimated implementation effort

**0,5–1 ngày kỹ thuật** nếu model/runtime đã cài; thêm thời gian setup model nếu chưa có. Đây là estimate, không commitment.

### Prerequisite code/data

- Frozen text fixture + oracle;
- deterministic number normalizer có unit test;
- adapter cho từng selected component trả raw signal và label đúng namespace;
- conceptual fact records tách `fact_identity/topic_key` khỏi `gold_claim`;
- presence component được team chọn rõ và oracle semantics theo D-015 được đóng băng. Hiện prerequisite này chưa đạt.

### Decision được unblock

Smoke gate đầu tiên cho W2A-03/06/07/09. Pass SQ-P1 chỉ cho phép **đi tiếp**, không accept MiniCheck và không unblock W2A-08; W2A-08 chỉ được xem xét sau SQ-P4.

---

## SQ-P2 — Reverse MiniCheck auxiliary test cho `correctly_supported_fact`

### Signal đang được test — không phải presence

**H-SQ-P2:** khi `premise = text deck` và `hypothesis = gold_claim`, MiniCheck có thể phân biệt gold claim được deck support đúng khỏi gold claim không được support. Đây là candidate correctness/grounding auxiliary signal (`correctly_supported_fact`), **không** phải detector cho topic/fact presence.

Contradicted và absent đều có expected MiniCheck label `NOT_SUPPORTED_BY_DECK`, nhưng presence oracle khác nhau. Chính cặp này chứng minh tại sao reverse MiniCheck không được dùng để redefine coverage thành correct presence.

### Controlled variables

- Cùng `fact_identity/topic_key`, gold-claim granularity, deck length envelope, language, formatting và threshold cho các fixture paired khi phù hợp.
- Cùng MiniCheck weights/runtime/batch size.
- Chỉ thay quan hệ giữa `gold_claim` và deck text: support đúng, contradiction, hoặc absence.
- Presence label do manual oracle trên `fact_identity/topic_key` cung cấp để audit semantics; SQ-P2 không train/evaluate một presence detector.
- Không dùng source PDF dài làm premise; mục tiêu chỉ là test reverse gold-claim support.

### Input

Tạo candidate **5 fixture cho mỗi category** (25 total; engineering sanity set, không phải benchmark):

1. topic present và gold claim được support **verbatim**;
2. topic present và gold claim được support **paraphrased**;
3. topic present và gold claim được support sau khi **compressed**;
4. topic **present nhưng contradicted** so với gold claim;
5. topic **absent**.

Mỗi fixture lưu hai oracle label độc lập:

- `topic_presence ∈ {PRESENT, ABSENT}` dựa trên `fact_identity/topic_key`;
- `gold_claim_support ∈ {SUPPORTED_BY_DECK, NOT_SUPPORTED_BY_DECK}` dựa trên `gold_claim`.

Không dùng derived-number case ở đây; nó thuộc SQ-P3.

### Expected output

| Category | Manual `topic_presence` | Expected MiniCheck `gold_claim_support` |
| --- | --- | --- |
| Verbatim correct | `PRESENT` | `SUPPORTED_BY_DECK` |
| Paraphrased correct | `PRESENT` | `SUPPORTED_BY_DECK` |
| Compressed correct | `PRESENT` | `SUPPORTED_BY_DECK` |
| Contradicted | `PRESENT` | `NOT_SUPPORTED_BY_DECK` |
| Absent | `ABSENT` | `NOT_SUPPORTED_BY_DECK` |

MiniCheck prediction chỉ được chấm với cột thứ ba. Cột presence tồn tại để ngăn downstream code diễn giải `NOT_SUPPORTED_BY_DECK` thành `ABSENT`.

### Metric candidate

- precision, recall, F1 cho candidate `gold_claim_support`;
- balanced accuracy, macro-F1 và confusion matrix theo 5 category;
- raw-score distribution theo category;
- explicit cross-tab `topic_presence × gold_claim_support` để audit semantic separation;
- repeatability: score spread + verdict flip rate.

Không tính precision/recall presence từ MiniCheck output trong SQ-P2.

### Pass/fail pre-registered

Với sanity set rất nhỏ, pass/fail dựa trước hết vào expected gold-claim-support behavior của từng fixture `must_pass`, error table và repeatability. Fixture stress có thể mismatch nhưng phải được phân tích theo category; không diễn giải tỷ lệ trên 25 case như statistical evidence.

Các mức cũ — verbatim/absent `5/5`, nhóm khác `4/5`, balanced accuracy/macro-F1 `≥0,80`, verdict flip rate `0` — chỉ là **candidate engineering gates** cho một gate sau này. Chúng chưa phải accepted requirement.

Nếu expected behavior nền tảng fail, outcome là **HOLD reverse MiniCheck auxiliary**. Ngay cả khi toàn bộ case pass, MiniCheck chỉ eligible cho larger correctness/grounding review; nó không được dùng cho `critical_fact_coverage` hoặc presence. Không tự động đổi sang AlignScore, QA/probe, lexical matching hay method khác.

### Failure interpretation

- Fail paraphrase/compression → bullet representation là domain gap của gold-claim support.
- Absent được `SUPPORTED` → false support ở correctness/grounding layer.
- Contradicted được `SUPPORTED` → candidate yếu ở correctness.
- Contradicted và absent cùng `NOT_SUPPORTED` → **expected**, đồng thời xác nhận signal không phân biệt presence.
- Score gần threshold và flip qua repeat → evaluator không đủ ổn định cho development use.

### Artifacts cần lưu khi chạy

25 fixture, separated fact records, hai oracle label, evidence spans, raw scores, threshold, confusion matrices, cross-tab, repeat logs, hardware/runtime và error-analysis table.

### Estimated implementation effort

**1–2 ngày** gồm authoring fixture, adapter và report. Đây là estimate, không commitment.

### Prerequisite code/data

MiniCheck local runtime; canonical text extraction từ Deck IR; frozen `fact_identity/topic_key` + `gold_claim` contract; two-label oracle; repeat runner.

### Decision được unblock

Chỉ inform W2A-06 về reverse MiniCheck như candidate correctness/grounding auxiliary. W2A-07 (presence coverage) vẫn `OPEN/HOLD`; pass SQ-P2 không promote MiniCheck và không giải quyết presence detector gap.

---

## SQ-P3 — Numeric transformation probe

### Hypothesis

**H-SQ-P3:** evaluator textual có thể phân biệt valid numeric transformation khỏi invalid transformation. Đây là hypothesis để bác bỏ; không giả định MiniCheck có numerical reasoning.

### Controlled variables

- Mỗi pair dùng cùng subject, metric, period và source sentence; chỉ thay arithmetic/transformation.
- Freeze tolerance cho rounding và canonical unit conversion trước run.
- Không dùng web/parametric knowledge.
- Cùng evaluator threshold và number-normalization pipeline.

### Input

Tối thiểu 5 case/category (khoảng 25–30 synthetic pair):

| Category | Example shape | Gold |
| --- | --- | --- |
| Correct derivation | `100 → 120` / `+20%` | supported |
| Wrong derivation | `100 → 120` / `+25%` | unsupported |
| Rounding | `12,4 → 18,7` / `~51%` | supported theo tolerance frozen |
| Unit conversion | `1,2B` / `1.200M` | supported |
| Percentage-point error | `20% → 25%` / `+25 percentage points` | unsupported |

### Manipulated variable

Loại và tính đúng/sai của transformation số.

### Expected output

Valid derivation/rounding/unit conversion phải được support; wrong derivation và percentage-point error phải bị reject. Mọi số không match verbatim vẫn được ghi vào `derived_number_queue` bất kể verdict model.

### Metric candidate

- false-unsupported rate trên valid transformation;
- false-supported rate trên invalid transformation;
- balanced accuracy/macro-F1;
- accuracy theo category;
- queue recall: tỷ lệ transformation được number normalizer route vào queue.

### Pass/fail pre-registered

Smoke outcome dựa vào expected behavior của từng sentinel case đã freeze: valid derivation/rounding/unit conversion không bị kết tội; wrong derivation và percentage-point error không được accepted; mọi số không match verbatim được route vào queue. Mỗi mismatch phải được liệt kê và giải thích, không chuyển thành statistical claim.

Các mức `≥4/5`, balanced accuracy `≥0,80` và queue recall `=100%` chỉ là **candidate engineering gates** cho một gate sau này. Cho tới khi team accept một gate trên dữ liệu đủ lớn, model **không được** tự adjudicate derived number; `derived_number_queue` + calculator/human review là bắt buộc ngay cả khi smoke cases đều đúng.

**Lưu ý:** pass không tự động cho phép xóa queue. Nó chỉ cho phép dùng model như signal hỗ trợ; thay đổi policy W2A-09 cần decision riêng.

### Failure interpretation

- Valid case bị unsupported → xác nhận evaluator phạt transformation đúng.
- Invalid case được supported → nguy hiểm hơn: evaluator bỏ lọt sai số.
- Chỉ fail percentage-point → cần explicit deterministic rule cho `%` vs `percentage point`.
- Unit conversion fail → number normalizer/schema unit chưa đủ, không nhất thiết là lỗi MiniCheck.

### Artifacts cần lưu khi chạy

Fixture + công thức gold, tolerance/unit table, raw scores, confusion matrix/category, normalizer trace, queue output, error analysis.

### Estimated implementation effort

**1 ngày** nếu number normalizer tồn tại; **2–3 ngày** nếu phải dựng normalizer tối thiểu và unit test.

### Prerequisite code/data

Calculator/oracle độc lập; unit normalization; raw-score access; không dùng chính evaluator để tạo gold.

### Decision được unblock

W2A-09 implementation level và RQ02 U-4. Không đóng W2A-06 một mình.

---

## SQ-P4 — Hallucination vs Incorrectness classifier test

### Hypothesis

**H-SQ-P4:** rule `unsupported → counterpart exists? yes=incorrectness / no=hallucination` phân biệt được hai class trên case rõ và **abstain** được ở case mơ hồ.

### Rule phải freeze trước test

Với một claim đã bị grounding evaluator gán `unsupported`, counterpart chỉ được coi là tồn tại khi source match đủ:

1. **subject/entity** tương thích;
2. **attribute/metric** tương thích;
3. **scope/population** tương thích;
4. **period/condition** tương thích.

Nếu match đủ bốn trường nhưng value/predicate khác → candidate `incorrectness`. Không tìm thấy match → candidate `hallucination`. Nếu bất kỳ trường nào không đủ rõ → `ABSTAIN/AMBIGUOUS`, không force label.

Ví dụ bắt buộc trong ambiguous set: source “Revenue increased”, deck “ARR increased”. Không được sửa rule sau khi xem prediction.

### Controlled variables

- Chỉ dùng claim đã được gán `unsupported` bởi một fixture/oracle cố định.
- Freeze retrieval model/index, top-k, similarity threshold và normalization.
- Counterpart rule/version giống nhau cho mọi case.
- Gold label không do classifier tạo.

### Input

Manual set nhỏ, candidate **8–10 case/class**:

- hallucination rõ;
- incorrectness rõ;
- ambiguous/counterpart-boundary.

Hai reviewer độc lập gán nhãn bằng rule frozen; disagreement được giữ là `AMBIGUOUS` hoặc adjudicate có log. Đây là pilot label set, không phải final human study; cỡ mẫu final vẫn `TBD`.

### Manipulated variable

Source có counterpart rõ, không có counterpart, hoặc có semantic neighbor mơ hồ.

### Expected output

Hallucination/incorrectness rõ được tách đúng; ambiguous được abstain thay vì tạo false certainty.

### Metric candidate

- balanced accuracy, macro-F1 và recall từng class trên **unambiguous subset**;
- coverage/abstention rate;
- `ambiguous_forced_label_rate`;
- error breakdown theo subject/attribute/scope/period;
- reviewer agreement chỉ mô tả, không dùng threshold final.

### Pass/fail pre-registered

Smoke outcome dựa vào expected behavior của từng case đã freeze: unambiguous hallucination/incorrectness phải đi đúng nhánh; ambiguous phải abstain; không sửa rule/threshold sau khi xem prediction. Mọi mismatch phải được báo theo bốn trường counterpart, không diễn giải tỷ lệ trên set nhỏ như statistical evidence.

Các mức balanced accuracy/macro-F1 `≥0,80`, recall mỗi class `≥0,75` và `ambiguous_forced_label_rate ≤0,25` chỉ là **candidate engineering gates**. Cho tới khi một gate được team accept trên dữ liệu phù hợp, kết quả SQ-P4 chỉ hỗ trợ review; nếu behavior nền tảng không đạt thì **không báo** `hallucination_count` và `incorrectness_count` tự động, chỉ báo `unsupported_count` + `needs_review`.

### Failure interpretation

- Hallucination bị gán incorrectness → retrieval quá rộng hoặc semantic equivalence quá lỏng.
- Incorrectness bị gán hallucination → retrieval/matching bỏ counterpart.
- Ambiguous bị force label → protocol thiếu abstention; metric tạo certainty giả.
- Reviewer bất đồng cao → ontology/rule chưa rõ, không được “sửa bằng model”.

### Artifacts cần lưu khi chạy

Frozen rule, retrieval config/index hash, labeled cases + reviewer logs, raw retrieved spans/scores, predictions, confusion matrix, abstention analysis, mọi error case.

### Estimated implementation effort

**2–3 ngày** cho fixture, reviewer pass, retrieval/matching adapter và analysis.

### Prerequisite code/data

Unsupported-claim list; source chunk index; review protocol; rule version; support cho `ABSTAIN`.

### Decision được unblock

W2A-08. Pass mới cho phép đề xuất tách hai count; human/team vẫn phải review trước khi promote.

---

## SQ.5 — Gate tổng Pilot SQ

| Kết quả | Hành động pre-registered |
| --- | --- |
| Presence component chưa được chọn/freeze | `HOLD BEFORE SQ-P1`: không dùng reverse MiniCheck hoặc gold-claim entailment làm proxy presence |
| SQ-P1 fail | `STOP`: không promote pipeline/evaluator; không chạy SQ-P2–SQ-P4 cùng cấu hình |
| SQ-P1 pass, SQ-P2 fail | Reverse MiniCheck không eligible cho `correctly_supported_fact` auxiliary; presence coverage là signal khác và vẫn theo verdict riêng |
| SQ-P3 fail | Giữ `derived_number_queue`; model không adjudicate số derived |
| SQ-P4 fail | Chỉ báo `unsupported`; không tách hallucination/incorrectness tự động |
| SQ-P1+SQ-P2 đạt expected-case behavior, repeatability/runtime đủ xem xét, SQ-P3/SQ-P4 có boundary rõ | `PROCEED` sang review lớn hơn cho từng signal riêng; **MiniCheck vẫn HOLD**, và SQ-P2 không promote presence coverage |

---

# PILOT B — `slide_type` Manipulation Check theo hai stage

## B.0 — Câu hỏi chung và dependency

**Câu hỏi:** khi chỉ đổi `meta.slide_type`, output có thay đổi vượt run-to-run noise và đi đúng behavior được team khai báo trước không?

Pilot B không hỏi “teaching đẹp hơn catchup?” và không tạo một `Structure Score`. Theo D-017, purpose + information selection là semantic/core distinction; text density, image ratio, layout và compression chỉ là secondary observable/diagnostic feature. Theo D-016, ba coverage bucket của Option C được báo riêng, correctness cũng báo riêng; không aggregate thành một score.

**Blocking prerequisite cho B0:** team phải review definition cụ thể của `teaching`, `catchup`, `speaker_support`, operationalize purpose thành obligation/observable, rồi freeze information selection và phân loại `fact_identity/topic_key` vào `universal_core`, `mode_required`, `optional/not_expected`. Mỗi fact record phải có `gold_claim` riêng cho correctness. Không được invent các definition này từ external evidence. `speaker_notes` giữ FR-55 = P3 và không phải core feature của `speaker_support` trong pilot hiện tại.

### Controlled variables chung

Giữ nguyên source document + extracted representation, system commit, model/prompt version, `target_slides`, `time_limit_minutes`, language, sampling/seed policy, Deck IR schema, design token, exporter/post-processing và retry rule. **Chỉ thay** `meta.slide_type ∈ {teaching, catchup, speaker_support}`. Nếu `time_limit_minutes` được coi là một phần của mode, phải tạo thiết kế hai biến khác; không trộn vào manipulation tối thiểu này.

## B0 — Smoke / feasibility

### Mục tiêu và input

B0 chỉ hỏi liệu có signal đủ đáng theo để thiết kế B1, không cung cấp confirmatory evidence.

- Dùng một source set nhỏ, được freeze trước run; không gọi là benchmark.
- Chạy cùng mỗi source ở cả 3 mode và có repeat trong từng mode: `source × 3 mode × R repeat`.
- `N_source` và `R` là HUMAN/TEAM DECISION trước run. Về toán học cần ít nhất hai repeat để mô tả WITHIN; `R=3` là candidate thực dụng, không phải accepted requirement.
- Không trộn ngôn ngữ trong cùng primary descriptive table; nếu có nhiều ngôn ngữ, báo theo stratum.

### Feature-level analysis

**Semantic/core — phải dựa trên oracle được team freeze:**

- Purpose obligation table — **NOT A METRIC**: báo per-obligation `MET | NOT_MET | NOT_OBSERVABLE` kèm evidence/error trace; không chấm kiểu `purpose_alignment = 4/5` và không average thành score;
- `universal_core_coverage` trên `fact_identity/topic_key`;
- `mode_required_coverage` trên `fact_identity/topic_key`;
- `optional_or_not_expected_presence` — diagnostic, không phải penalty mặc định;
- topic/fact identity selection và omission theo từng bucket;
- correctness/grounding report riêng dựa trên `gold_claim`, không thay đổi coverage presence.

Mỗi purpose obligation phải được team pre-register dưới dạng observable cụ thể và trace tới information-selection requirement, ví dụ shape `purpose → obligation_id → required prerequisite/explanatory-chain category hoặc required fact_identity/topic_key → expected observable`. Đây chỉ là shape của contract; file này không điền nghĩa cho ba mode. Nếu obligation chưa operationalize, purpose là `HOLD/NOT EVALUABLE`, không được thay bằng fuzzy judge score.

Nếu evaluator SQ chưa được human accept, các feature semantic dùng review/oracle thủ công nhỏ đã freeze trên fact identity và gold claim tách biệt; không tự động dùng MiniCheck như accepted presence hoặc correctness metric.

**Secondary observable/diagnostic F1:**

| Feature | Định nghĩa B0 | Nguồn |
| --- | --- | --- |
| `slide_count` | `len(slides)` | Deck IR |
| `words_per_slide` | mean số từ trong title + text block; freeze tokenizer | Deck IR |
| `bullets_per_slide` | mean block có `role=bullet` mỗi slide | Deck IR |
| `words_per_bullet` | mean số từ/bullet; missing theo rule frozen | Deck IR |
| `image_slide_ratio` | slide có ≥1 image block / tổng slide | Deck IR |
| `text_image_block_ratio` | text blocks / (text blocks + image blocks) | Deck IR |
| `layout_histogram` | tỷ lệ trên vocabulary layout frozen + `unknown` | Deck IR |
| `compression_ratio` | số từ deck / số từ source normalized | IR + source |
| `source_region_coverage` | `source_ref` hợp lệ phân biệt / region source | IR + source |
| `source_order_similarity` | `1 − NGLD` trên ordinal `source_ref` | IR + source |

Không dùng geometry/area/whitespace khi vertical slice chưa có geometry. Các feature surface không được nâng thành CORE chỉ vì dễ đo hoặc có signal mạnh.

### WITHIN / BETWEEN mô tả

Với feature `x[s,m,r,f]`:

- `WITHIN[s,f]`: median pairwise distance giữa repeat cùng mode, rồi mô tả qua ba mode;
- `BETWEEN[s,f]`: pairwise distance giữa centroid của ba mode;
- báo raw value, median/range và `BETWEEN` so với `WITHIN` theo từng source × feature;
- lập direction/error table: dấu kỳ vọng đã freeze, dấu quan sát, repeat instability, outlier, missing/invalid run và quality regression.

B0 **không** dùng confirmatory p-value, confidence interval, bootstrap interval hay classifier accuracy như project evidence. Không áp threshold `0,80`, `70%`, `80%`, `p<0,05`, `0,50` hoặc `0,45` để biến smoke result thành accepted requirement.

### Outcome B0

| Outcome | Ý nghĩa và hành động |
| --- | --- |
| `INFEASIBLE` | Run/IR/source_ref không đủ ổn định để đo; sửa instrumentation trước |
| `NO_SIGNAL` | BETWEEN không khác rõ WITHIN trong descriptive table; không thiết kế B1 từ cấu hình này |
| `SURFACE_ONLY` | Chỉ density/image/layout/compression đổi; giữ như diagnostic, chưa có semantic evidence |
| `SEMANTIC_CANDIDATE` | Pre-registered purpose obligations/information-selection observables có direction lặp lại và quality không collapse; đưa bảng obligation ra human review để quyết định có thiết kế B1 không |
| `MIXED/UNSTABLE` | Direction thay đổi theo source/repeat hoặc có correctness regression; phân tích lỗi, không gọi là confirmatory signal |

Không có numeric auto-gate cho outcome trên. Team xem explicit expected cases, source-level tables và toàn bộ errors để quyết định signal có đáng theo hay không.

### Artifacts và prerequisite B0

- Signed/versioned mode definition + purpose-obligation/observable map + Option C fact-identity map + expected direction table;
- conceptual fact records tách `fact_identity/topic_key`, `gold_claim` và `mode_expectation`, kèm author/reviewer/provenance;
- source IDs/hashes, `N_source`, `R`, commit/model/prompt/seed và invalid/retry rule;
- full run manifests, Deck IR, plan/outline artifact nếu có và render nếu có;
- long-form `features.csv`, semantic oracle/review log, WITHIN/BETWEEN descriptive tables;
- correctness + invalid/retry/exclusion report; không silently drop run;
- Deck Agent chạy cùng source ở đủ ba mode, run manifest D-014, stable IR parser và `source_ref`.

Estimated effort candidate: 1–2 ngày cho feature/oracle setup và khoảng 1 ngày orchestration/report, chưa gồm runtime hoặc sửa product. Estimate này không phải commitment.

## B1 — Confirmatory candidate, chỉ thiết kế/freeze sau B0

B1 chưa được pre-register trong Wave 2A. Chỉ khi B0 cho `SEMANTIC_CANDIDATE` và human review đồng ý tiếp tục mới được chọn estimand, sample size, threshold và statistical protocol trên source set độc lập/held-out. Không dùng lại B0 như confirmatory sample.

### Những quyết định phải làm sau B0

1. Chọn core semantic contrast nào thực sự đáng confirm và giữ surface feature nào chỉ để diagnostic.
2. Chọn sample size/source strata/repeat dựa trên câu hỏi và noise quan sát ở B0, tránh coi B0 effect size là ổn định nếu sample quá nhỏ.
3. Freeze expected direction, equivalence margin nếu có dấu `=`, invalid/exclusion/retry policy và quality floor.
4. Freeze permutation/bootstrap protocol, multiplicity handling và classifier evaluation.
5. Pre-register threshold engineering; mọi mức cũ vẫn là candidate cho đến khi team accept.

### Statistical design hiện có — giữ làm candidate B1

- Candidate difference estimand: `signal_ratio[s,f] = (BETWEEN + ε_f)/(WITHIN + ε_f)` và `T = median(log(signal_ratio))` trên feature set frozen; `ε_f` phải freeze trước run.
- Candidate inference: hoán vị mode label trong source và bootstrap theo source, với seed/resampling count frozen; per-feature multiplicity correction candidate là Holm.
- Candidate direction analysis: paired delta theo source, direction agreement và CI của median delta cho contrast đã pre-register.
- Candidate recoverability check: multinomial logistic regression, leave-one-source-out; không random deck split; báo accuracy, macro-F1, per-class recall và confusion matrix.
- MLLM forced-choice chỉ là later optional check; không thay thế semantic oracle và không được claim semantic understanding nếu chỉ đọc surface cues.

Các mức `p<0,05`, `70%`, `80%`, accuracy `≥0,50`, macro-F1 `≥0,45`, balanced accuracy/macro-F1 `≥0,80` và mọi 95% CI rule là **candidate engineering/governance gates**, không phải accepted requirement hay external best practice. Chúng phải được xem lại và freeze trong preregistration B1; B0 không bị chặn bởi các mức này.

### Câu hỏi B1 candidate

- Difference: mode effect có vượt run-to-run/source noise trên core semantic contrast đã chọn không?
- Direction: effect có đi đúng purpose/information-selection behavior đã freeze không?
- Recoverability: mode có thể được nhận diện trên held-out source mà không dựa vào metadata leakage không?
- Quality floor: contrast có xuất hiện mà không làm correctness/schema/exportability collapse không?

Outcome B1 chỉ có hiệu lực sau preregistration riêng, held-out run và human review. B1 có thể unblock W2A-10/12/13/14 và candidate W2A-16; không tự động accept behavior definition, classifier hay external-best-practice claim.

## B.2 — Sequence và stop point

`SQ-P1 → SQ-P2 → SQ-P3 → SQ-P4 → human review SQ → freeze semantic definitions/Option C map → B0 → human review B0 → [nếu signal đáng theo] design + freeze B1 → B1`

Wave 2A hiện dừng trước khi chuẩn bị/chạy SQ-P1 vì presence method còn `OPEN/HOLD`, và trước B0 vì purpose obligations + fact-identity/gold-claim/mode map chưa freeze. B1 chưa được thiết kế/freeze và không được dùng để chặn feasibility work của B0 sau khi các prerequisite B0 được giải quyết.

---

## 1. Những việc file này không cho phép

- Không chạy pilot trước human approval của preregistration và prerequisites.
- Không promote MiniCheck, forced-choice judge hay behavior hypothesis vào `06_design/` accepted area.
- Không đổi expected sign, threshold, exclusion rule hoặc fixture sau khi thấy output.
- Không gọi Pilot B là evidence “deck tốt” nếu chỉ contrast pass.
- Không dùng Pilot SQ/B0 để chốt sample size human final hoặc B1 trước khi review B0.
- Không mở RQ04/RQ05/RQ07 trong lúc thực hiện pilot này.
