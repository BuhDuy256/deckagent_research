# Cross-RQ Working Policy — Source Coverage vs `slide_type`

**Ngày lập:** 2026-08-15  
**Phạm vi:** giao diện RQ02 ↔ RQ03 ↔ RQ07  
**Trạng thái:** **OPTION C SELECTED AS WORKING PILOT POLICY — FINAL POLICY NOT FROZEN**  
**Human decision:** D-015, D-016 (2026-08-15)

File này đưa conflict ra khỏi RQ03 để nó không bị chôn trong một recommendation riêng lẻ. Human review đã chọn **Option C** cho pilot: `universal_core` + `mode_required` + `optional/not_expected`, metric riêng, không aggregate. Đây là **working product/evaluation policy**, không phải external best practice và chưa phải final benchmark decision.

## 0. Câu hỏi và quyết định cho pilot

> Một fact quan trọng có quan trọng như nhau cho cả `teaching`, `catchup`, `speaker_support` không?

**HUMAN DECISION D-015/D-016.** Không ép mọi fact có cùng status ở mọi mode. Coverage đo **topic/fact presence**; fact present-but-wrong vẫn present và correctness được báo riêng. Pilot dùng hai layer categorical:

```
universal_core
mode_required
optional / not_expected
```

Không aggregate các layer hoặc correctness thành một score.

**INFERENCE — conflict cụ thể:** cùng một quan sát có thể nhận hai nhãn đối lập.

```
Quan sát: phần background của source không xuất hiện trên deck catchup

RQ02 / source frame → coverage gap: source có fact quan trọng nhưng deck bỏ
RQ03 / brief frame  → intentional omission: mode cố ý dành attention cho update chính
```

Nếu evaluator chỉ dùng source frame, nó có thể phạt một output vì tuân thủ mode. Nếu evaluator chỉ dùng brief frame, nó có thể hợp thức hóa omission sau khi thấy output. Đây là conflict giữa **source completeness** và **purpose-conditioned information selection**, không phải lỗi nhỏ của công thức.

## 1. Evidence cho conflict — và giới hạn của evidence

**EXTERNAL EVIDENCE.** FineSurE (ACL 2024) cho thấy keyfact alignment là một cách mạnh hơn generic judge để đo completeness, nhưng keyfact trong setup của họ đến từ human/reference summary, không từ source PDF cho ba mode Deck Agent. Nguồn này support **item-wise coverage**, không support mode-specific weighting.

**EXTERNAL EVIDENCE.** *All Claims Are Equal* cho thấy importance weighting có thể làm factuality metric nhạy hơn với claim quan trọng; “importance” của paper neo vào query, không neo vào `slide_type`. Nguồn này support việc không coi mọi fact ngang nhau trong mọi task, nhưng không cho weight của ba mode.

**EXTERNAL EVIDENCE.** *X+Slides* dùng cùng source-grounded probe nhưng gán audience-specific utility weight, và báo rằng audience adaptation chủ yếu thay đổi information selection. Đây là precedent gần nhất cho Option B. Tuy nhiên `specialist | learner | decision-maker` không tương đương `teaching | catchup | speaker_support`; profile/weight không được import.

**INFERENCE.** Các vòng search của RQ03 không tìm thấy nguồn external nào định nghĩa ba mode Deck Agent; đây không phải bằng chứng tuyệt đối rằng không có nguồn nào tồn tại. Với evidence hiện có, literature không thể quyết định fact nào được phép bỏ trong từng mode. Đây là product specification work.

**Cảnh báo verification.** Chi tiết *X+Slides* hiện mới được verify sâu qua HTML/tool summary, chưa đọc lại PDF gốc. Không dùng con số/quote của paper trong báo cáo bảo vệ trước RQ03 P-11. Option comparison bên dưới dựa vào **hình thái cơ chế**, không dựa vào một threshold số từ paper.

---

## 2. Pilot invariants sau human review

Các invariant sau áp dụng cho working pilot policy; phần final benchmark vẫn chưa được mở:

1. **Mode chỉ condition coverage/selection, không condition factual correctness.** Một fact present-but-wrong vẫn present cho coverage nhưng được báo incorrect riêng; claim unsupported vẫn unsupported. Không được cho `speaker_support` “dễ” hơn về bịa/sai.
2. **Author criteria trước khi xem output đang được evaluate.** Mọi fact identity/topic key, gold claim và mode category phải freeze trước generation/run; không tune benchmark để khớp behavior hiện tại.
3. **Giữ raw evidence.** Lưu per-fact presence theo topic key và correctness theo gold claim + rationale; không chỉ lưu một weighted aggregate.
4. **Version mọi policy.** Đổi fact pool, weight hay mode category làm đổi benchmark semantics; comparison lịch sử qua version đó không còn trực tiếp tương đương.
5. **Giữ metric riêng:** `universal_core_coverage`, `mode_required_coverage`, presence diagnostic của `optional/not_expected`, và correctness/unsupported. Không tạo composite.
6. **Mọi benchmark case phải chạy paired theo cùng source** khi đánh giá `slide_type`; không author criteria khác nhau từ đầu bằng cách dùng source khác.

Đây là pilot policy đã human-confirm ở D-015/D-016; quality của fact authoring/category vẫn phải review trước B0.

### 2.1 Conceptual fact model bắt buộc tách presence khỏi correctness

**HUMAN DECISION D-015/D-016.** Một fact record không được chỉ là một string đầy đủ, vì so cả string với deck sẽ biến “có nhắc nhưng nói sai” thành “không present”. Data model dùng cho pilot phải support tối thiểu ba lớp khái niệm, dù implementation cuối không bắt buộc dùng đúng YAML dưới đây:

1. **`fact_identity` / `topic_key`** — định danh nội dung đang được nói tới, dùng cho presence/coverage. Nó chứa các field nhận diện ổn định như subject + attribute; không chứa value làm điều kiện bắt buộc để được coi là present.
2. **`gold_claim` / factual content** — phát biểu chuẩn từ source, gồm value, direction, period, qualifier và provenance cần thiết; dùng cho correctness/grounding, không dùng để redefine presence.
3. **`mode_expectation`** — obligation của fact identity theo D-016: `universal_core`, hoặc theo từng mode là `required | optional | not_expected`.

Ví dụ conceptual:

```yaml
id: fact_churn_change
fact_identity:
  topic_key:
    subject: churn
    attribute: change
gold_claim:
  text: "Churn giảm từ 6.2% xuống 3.9%"
  source_ref: ...
universal_core: true
mode_expectation:
  teaching: required
  catchup: required
  speaker_support: required
```

Hai câu hỏi được chấm độc lập:

```text
presence:    deck có đề cập fact_identity/topic_key không?
correctness: nội dung deck nói về topic đó có phù hợp gold_claim/source không?
```

Với câu “Churn giảm từ 6,2% xuống 1,9%”, `presence = true` cho topic churn/change, còn correctness/grounding fail. Hiện Wave 2A **chưa chọn hoặc validate một presence detector tự động**; conceptual model này là input contract, không phải một method mới.

---

## 3. Option A — Cùng `critical_facts`, cùng weight cho mọi mode (**không chọn cho pilot**)

### Định nghĩa

**OPTION A.** Mỗi source có một fact pool `F={f1...fn}`; mỗi item vẫn là record tách fact identity và gold claim theo §2.1. Mọi fact identity có expectation bằng nhau cho teaching/catchup/speaker_support.

```
coverage_A(mode) = số fact trong F xuất hiện trên deck_mode / |F|
```

Theo D-015, nếu dùng Option A thì coverage vẫn đo topic/fact presence; present-but-wrong được báo correctness riêng. Option A không thay đổi semantics đã chốt này.

### Điều Option A đo

**INFERENCE.** Nó đo **source-centric recall** ổn định qua mode: deck nào giữ lại nhiều fact trong pool hơn. Nó không đo việc omission có phù hợp với mục đích sử dụng hay không.

### Ảnh hưởng

| Dimension | Option A |
| --- | --- |
| **Benchmark authoring** | Đơn giản nhất: một fact pool/source, không cần mode matrix |
| **Fairness** | Công bằng theo source-centric definition; có thể không công bằng theo intent nếu mode cố ý chọn lọc khác |
| **Reproducibility** | Cao nhất nếu fact pool frozen; ít parameter cần version |
| **Interpretability** | Cao: “x/n fact có mặt”; dễ drill xuống từng omission |
| **Risk of benchmark gaming** | Thấp hơn B/C ở khâu weight, nhưng model có thể copy nhiều fact để tăng coverage và làm deck dày chữ |
| **RQ07 impact** | Schema nhẹ: `critical_facts[]`, label/provenance/version; benchmark vẫn nhân 3 mode cho RQ03 |
| **Regression comparability** | Mạnh: cùng ruler cho mọi mode/version |
| **Product-intent sensitivity** | Thấp: không phân biệt omission cố ý với omission vô tình |

### Failure mode phải chấp nhận nếu chọn A

- `catchup`/`speaker_support` có thể score thấp hơn vì làm đúng selection intent.
- Team phải mô tả score là **raw source coverage**, không tự động gọi mọi missing fact là product defect.
- Model có incentive giữ/copy nhiều source text; phải đọc cùng density/compression/ready-to-use signal.

### Điều phải test/confirm trước khi chọn A

1. Team có thật sự muốn source completeness là invariant cao hơn mode intent không?
2. Có một subset “non-negotiable facts” đủ nhỏ để mọi mode phải giữ không?
3. Human khi xem mode-valid deck có chấp nhận raw coverage khác nhau không?
4. Cách report có giữ omission list/rationale thay vì chỉ pass/fail không?

---

## 4. Option B — Cùng fact pool, weight theo mode (**không chọn cho pilot**)

### Định nghĩa

**OPTION B.** Mỗi source vẫn có một fact pool chung theo conceptual model §2.1, nhưng mỗi fact identity có weight theo mode:

```yaml
- id: fact_A
  fact_identity:
    topic_key:
      subject: ...
      attribute: ...
  gold_claim:
    text: ...
    source_ref: ...
  mode_weight:
    teaching: 1.0
    catchup: 0.3
    speaker_support: 0.5
```

```
coverage_B(mode) = Σ_i weight[i,mode] × present[i,mode]
                   / Σ_i weight[i,mode]
```

Các số 1,0/0,3/0,5 chỉ minh họa schema, **không phải weight đề xuất**.

### Điều Option B đo

**INFERENCE.** Nó đo coverage dưới một utility/relevance function do team định nghĩa cho mode. Nó gần product intent hơn A, nhưng score không còn là raw source coverage.

### Ảnh hưởng

| Dimension | Option B |
| --- | --- |
| **Benchmark authoring** | Cao: author/review `n_fact × 3` weight, rationale và normalization |
| **Fairness** | Có thể công bằng hơn theo intent; có thể thiên vị implementation nếu weight được author sau khi xem output |
| **Reproducibility** | Tốt nếu weight frozen/versioned; kém nếu sinh/tune lại theo run |
| **Interpretability** | Trung bình: cần hiển thị raw fact result + weight contribution; aggregate một mình khó đọc |
| **Risk of benchmark gaming** | Cao nhất: team có thể giảm weight cho fact hệ thống hay bỏ và tăng weight cho fact hệ thống hay giữ |
| **RQ07 impact** | Schema/authoring/audit phức tạp: `mode_weight`, rationale, author/reviewer, version, normalization, change policy |
| **Regression comparability** | Chỉ mạnh trong cùng weight version; đổi weight phá chuỗi lịch sử |
| **Product-intent sensitivity** | Cao nhất nếu weight thật sự đúng; sai nếu behavior spec sai |

### Safeguard bắt buộc nếu chọn B

1. Author weight **không xem output** của system version đang evaluate.
2. Có reviewer độc lập và rationale per weight hoặc per fact group.
3. Báo song song `raw_unweighted_coverage` và `mode_weighted_coverage`; không chỉ báo weighted score.
4. Freeze normalization và cách xử lý weight=0.
5. Đổi weight tạo benchmark version mới; không nối line chart qua version mà không đánh dấu break.
6. Incorrectness/hallucination không được giảm penalty theo weight mode.

### Điều phải test/confirm trước khi chọn B

- Team có thể gán weight ổn định mà không nhìn output không?
- Independent author/reviewer agreement có đủ cao không?
- Weight có bền khi cùng source được author lại sau một khoảng thời gian không?
- Weighted result có thay đổi ranking một cách hợp lý trên deck dựng tay `good teaching / good catchup / good speaker`, hay chỉ phản ánh author preference?
- Chi phí authoring có phù hợp scope đồ án và RQ07 không?

---

## 5. Option C — Hai layer categorical, báo metric riêng (**selected cho pilot**)

### Định nghĩa

**HUMAN DECISION D-015/D-016:** tách fact identity, gold claim và mode expectation thay vì dùng một full-claim string vừa làm presence target vừa làm correctness target, đồng thời dùng category thay continuous weight.

1. `universal_core` — fact bắt buộc cho mọi mode.
2. Với từng mode, mỗi fact còn lại mang category `required | optional | not_expected`.

```yaml
- id: fact_A
  fact_identity:
    topic_key:
      subject: ...
      attribute: ...
  gold_claim:
    text: ...
    source_ref: ...
  universal_core: false
  mode_expectation:
    teaching: required
    catchup: optional
    speaker_support: optional
```

Báo ít nhất hai metric, **không gộp**:

```
universal_core_coverage = present(universal_core) / |universal_core|
mode_required_coverage  = present(required_for_mode) / |required_for_mode|
```

`optional` omission không bị phạt; `not_expected` inclusion được báo như selection diagnostic, không tự động coi là error. Hai coverage metric dùng presence của `fact_identity/topic_key`; `gold_claim` chỉ phục vụ correctness/grounding. Fact present-but-wrong vẫn tính present và được ghi incorrectness riêng theo D-015.

### Điều Option C đo

**INFERENCE.** Nó giữ một source-centric floor chung và một brief-conditioned requirement riêng. Categorical status dễ audit hơn continuous weights nhưng vẫn cần product authoring.

### Ảnh hưởng

| Dimension | Option C |
| --- | --- |
| **Benchmark authoring** | Trung bình–cao: ít quyết định số hơn B, nhưng vẫn cần fact × mode category |
| **Fairness** | Cân bằng hai frame nếu `universal_core` hợp lý; có thể bất công nếu core quá lớn hoặc mode-required được chọn thiên vị |
| **Reproducibility** | Cao nếu category frozen; dễ diff/version hơn continuous weight |
| **Interpretability** | Cao: tách “core bị bỏ” khỏi “mode-required bị bỏ”; drill-down tự nhiên |
| **Risk of benchmark gaming** | Trung bình: vẫn có thể chuyển fact khó sang `optional/not_expected`, nhưng audit dễ hơn tuning weight nhỏ |
| **RQ07 impact** | Schema có `universal_core` + `mode_expectation` + rationale/reviewer/version; cần rule cho empty required set |
| **Regression comparability** | Mạnh trong cùng category version; vẫn bị break nếu relabel category |
| **Product-intent sensitivity** | Trung bình–cao; không diễn đạt mức importance tinh tế như B |

### Failure mode phải quản lý nếu chọn C

- `universal_core` quá lớn → C sụp về A.
- `universal_core` quá nhỏ và nhiều `optional` → benchmark dễ dãi/gaming.
- Category boundary vẫn mơ hồ; `required` vs `optional` cần behavior spec.
- Hai metric có thể đi ngược nhau; report phải giữ cả hai thay vì invent aggregate mới.

### Điều phải review trước khi chạy Option C ở B0

- Fact identity/topic key, gold claim và category được author trước output, có reviewer/rationale/version riêng chưa?
- Mỗi source có đủ `universal_core` để tạo quality floor có ý nghĩa không?
- `mode_required` có trace trực tiếp tới definition purpose/information selection đã được team review chưa?
- Empty set, disagreement và category change được xử lý thế nào trong B0 config?

---

## 6. So sánh ngang ba option

| Tiêu chí quyết định | Option A — shared equal | Option B — mode weights | Option C — dual categorical |
| --- | --- | --- | --- |
| Câu hỏi chính | “Giữ bao nhiêu fact nguồn?” | “Giữ bao nhiêu utility theo mode?” | “Giữ core chung và required của mode tới đâu?” |
| Authoring effort | **Thấp** | **Cao nhất** | **Trung bình–cao** |
| Auditability | **Cao** | **Thấp–trung bình** nếu không lưu raw contribution | **Cao** |
| Intent alignment | **Thấp** | **Cao tiềm năng** | **Trung bình–cao** |
| Continuous tuning surface | **Không** | **Có, lớn** | **Không**, nhưng có relabel surface |
| Historical stability | **Cao nhất** | Phụ thuộc weight version | Phụ thuộc category version |
| Nguy cơ phạt intentional omission | **Cao** | **Thấp nếu weight đúng** | **Thấp nếu category đúng** |
| Nguy cơ benchmark fit theo output | **Thấp–trung bình** | **Cao nhất** | **Trung bình** |
| Có cần behavior spec trước authoring? | Nên có để diễn giải | **Bắt buộc** | **Bắt buộc** |
| RQ07 schema complexity | **Thấp** | **Cao nhất** | **Trung bình–cao** |
| Có giữ raw source view? | Mặc định có | Chỉ nếu safeguard bắt buộc | Có qua universal/core + nên báo raw list |

**INFERENCE.** Không option nào đồng thời tối đa simplicity, intent fairness và chống gaming. Việc chọn C cho pilot không xóa tradeoff hoặc chứng minh C tốt nhất bên ngoài Deck Agent.

---

## 7. Tác động cụ thể tới RQ07

RQ07 chưa được mở, nhưng decision này quyết định schema benchmark tối thiểu.

### Conceptual field chung cho mọi option

```yaml
critical_facts:
  - id: ...
    fact_identity:
      topic_key:
        subject: ...
        attribute: ...
    gold_claim:
      text: ...
      source_ref: ...
    author: ...
    reviewer: ...
    rationale: ...
    version: ...
```

**WORKING PILOT RULE.** Lưu criteria-generation provenance, review date và hash; author/review/freeze trước output. W2A-04 vẫn `HOLD` cho workflow LLM-generate cụ thể; B0 có thể dùng fact/category author thủ công để smoke-test policy.

### Field riêng theo option

- **A:** không thêm mode field; có thể thêm `universal_core` chỉ nếu team muốn subset reporting, nhưng lúc đó đang tiến gần C.
- **B:** `mode_weight` cho đủ ba mode + normalization/version/change rationale.
- **C:** `universal_core` + `mode_expectation` cho đủ ba mode + rule empty set.

### Benchmark authoring workflow bị ảnh hưởng

1. Chọn source và trích candidate fact.
2. Human review riêng fact identity/topic-key granularity và gold-claim accuracy/provenance.
3. **Trước khi xem Deck Agent output**, author phần mode policy tương ứng A/B/C trên fact identity đã freeze.
4. Independent review + disagreement log.
5. Freeze benchmark version.
6. Chạy mỗi source qua cả ba mode cho RQ03 paired design.

Nếu bước 3 xảy ra sau bước 6, benchmark có nguy cơ được tune để biện minh output hiện tại.

---

## 8. Human selection và boundary của Option C

**HUMAN DECISION.** Chọn Option C cho pilot vì nó giữ source floor (`universal_core`) và mode intent (`mode_required`) thành hai metric tách rời, đồng thời tránh continuous weight surface của B. Đây là policy cho dữ liệu Deck Agent pilot, không phải claim literature rằng C là phương pháp tốt nhất.

- Option A vẫn là alternative nếu sau pilot team quyết định source completeness phải hoàn toàn bất biến.
- Option B vẫn là alternative nếu cần utility continuous và có governance đủ mạnh cho weight authoring.
- Pilot result không được dùng để relabel fact/category hồi tố nhằm làm C “pass”.

Không có decision final cho benchmark; RQ07 chưa được mở.

---

## 9. HUMAN/TEAM DECISION còn chặn B0

1. Definition cụ thể purpose/information selection của teaching/catchup/speaker_support và obligation/observable dùng operationalize purpose (Q-015); không dùng fuzzy score.
2. Fact identity/topic key và gold claim nào được dùng; fact identity nào là `universal_core` hoặc `mode_required/optional/not_expected` cho từng mode/source?
3. Ai author, ai review; cả hai không được dùng output hiện tại để tune category.
4. Rule xử lý reviewer disagreement, empty category và version change trong B0.
5. Source set/repeat/exclusion config B0 và expected-case review.

## 10. Điều file này không làm

- Không claim Option C là external best practice hoặc final benchmark policy.
- Không mở hoặc viết `BENCHMARK_SPEC.md` thay RQ07.
- Không gán weight/category cụ thể cho fact thật.
- Không dùng output Deck Agent hiện tại để author criteria.
- Không thay coverage-conditioned score cho hallucination/incorrectness.
- Không aggregate `universal_core`, `mode_required`, optional/not_expected diagnostic hoặc correctness thành một score.
