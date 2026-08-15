# Draft — `slide_type` Behavior Specification

**Ngày lập draft:** 2026-08-15  
**Trạng thái:** **CORE HIERARCHY ACCEPTED (D-017) — AUTHORITY REVIEWED — SPECIFIC MODE DEFINITIONS/OBLIGATIONS STILL DRAFT**  
**Owner cần chốt:** team sản phẩm + giảng viên  
**Phụ thuộc:** RQ03, Wave 2A Decision Gate W2A-14/W2A-17

File này nằm trong `06_design/drafts/` vì nó **không phải external truth và chưa phải design đã accept**. Nó chỉ biến các câu hỏi sản phẩm còn mơ hồ thành một spec có thể review và pre-register. Không được copy các dấu `?`/`TBD` dưới đây vào pilot như expected truth.

## 0. Boundary của draft

**PROJECT REQUIREMENT.** `slide_type` hiện là metadata ở **Deck-level**, nhận `teaching | catchup | speaker_support`; không phải loại của từng slide. Draft này không rename schema.

**INFERENCE.** Các vòng search của RQ03 không tìm thấy nguồn external nào định nghĩa đúng bộ ba nhãn trên hoặc một bộ tương đương; đây là kết quả search, không chứng minh tuyệt đối rằng nguồn đó không tồn tại. *X+Slides* dùng audience `specialist | learner | decision-maker`; đó là trục **người nghe**, còn Deck Agent đang mô tả trục **cách deck được dùng**. Vì vậy cơ chế conditioning/measurement có thể tham khảo, nhưng nội dung từng profile **không được chuyển nguyên**.

**RECOMMENDATION.** Team phải chốt behavior trước Pilot B. Nếu behavior được viết sau khi xem output, manipulation check không còn khả năng fail và không còn giá trị làm evidence.

**HUMAN DECISION D-017.** Trong pilot hiện tại:

- **CORE:** purpose + information selection;
- **SECONDARY/DIAGNOSTIC:** text density, image ratio, layout, compression và surface observable khác, trừ khi team explicit promote bằng decision/version mới;
- **NON-CORE:** `speaker_notes`; FR-55 giữ P3.

Hierarchy này đã chốt. Nội dung cụ thể của teaching/catchup/speaker_support vẫn là `TEAM DECISION NEEDED`, không được invent từ external literature.

**HUMAN DECISION / ACCEPTED PILOT POLICY D-015/D-016.** Information selection phải vận hành trên `fact_identity/topic_key`; correctness/grounding vận hành riêng trên `gold_claim`. Mode expectation (`universal_core | required | optional | not_expected`) gắn với fact identity, không gắn với việc full gold claim có được entail hay không. Vì vậy một topic được nhắc nhưng nói sai vẫn present cho coverage và fail correctness riêng. Đây là conceptual contract, không phải một implementation/YAML đã freeze.

### Nhãn hợp lệ cho từng dòng

- `PROJECT REQUIREMENT` — source nội bộ xác lập requirement/scope hiện tại, không phải external truth.
- `EXTERNAL EVIDENCE` — nguồn E1/E2 support đúng claim ở mức ghi trong dòng; không suy rộng sang ba mode nếu nguồn không support.
- `PRODUCT HYPOTHESIS` — hướng hợp lý để thử, chưa được team accept và chưa được data validate.
- `HUMAN DECISION / ACCEPTED PILOT POLICY` — policy đã được human promote trong D-015/D-016/D-017; không phải original requirement hoặc external best practice.
- `EXPERIMENT CONTROL` — biến được giữ cố định để cô lập `slide_type`; không phát biểu product behavior ngoài experiment đó.
- `TEAM DECISION NEEDED` — literature/evaluation không thể quyết định thay product owner.

---

## 1. Behavior matrix cần team review

Mỗi dòng phải ghi đủ provenance. Một dòng có thể cần nhãn kết hợp khi một phần đã được accept nhưng value/direction cụ thể vẫn cần team quyết định. Dấu `?` và `TBD` là **blocking state**, không phải soft default.

| Attribute | Teaching | Catchup | Speaker Support | Pilot role | Nhãn |
| --- | --- | --- | --- | --- | --- |
| **Primary use / purpose** | Học/hiểu nội dung? | Đọc lại nhanh để nắm điều đã bỏ lỡ? | Hỗ trợ người thuyết trình khi nói? | **CORE — definition chưa chốt** | `HUMAN DECISION / ACCEPTED PILOT POLICY (CORE: D-017) + TEAM DECISION NEEDED (definition)` |
| **Primary user context** | Người xem cần được dẫn dắt từ nền tảng? | Người đọc tự đọc, có thể đã biết background? | Có speaker hiện diện và diễn giải ngoài slide? | DEFINITION CONTEXT hỗ trợ purpose; chưa phải metric CORE độc lập | `TEAM DECISION NEEDED` |
| **Expected information selection** | Giữ prerequisite + main contribution + explanation chain? | Ưu tiên update/key takeaway, có thể bỏ background? | Ưu tiên cue/evidence phục vụ lời nói, không nhất thiết standalone? | **CORE — definition chưa chốt; apply trên fact identity/topic key** | `HUMAN DECISION / ACCEPTED PILOT POLICY (CORE: D-017) + TEAM DECISION NEEDED (definition)` |
| **`universal_core` coverage** | Fact identity chung bắt buộc phải present | Như teaching | Như teaching | **CORE quality floor (D-016)** | `HUMAN DECISION / ACCEPTED PILOT POLICY (D-016)` |
| **`mode_required` coverage** | TBD theo purpose teaching | TBD theo purpose catchup | TBD theo purpose speaker_support | **CORE information-selection policy trên fact identity** | `HUMAN DECISION / ACCEPTED PILOT POLICY (category: D-016) + TEAM DECISION NEEDED (mapping)` |
| **`optional/not_expected` presence** | Diagnostic, không aggregate | Diagnostic, không aggregate | Diagnostic, không aggregate | SELECTION DIAGNOSTIC; không phải penalty/CORE metric mặc định | `HUMAN DECISION / ACCEPTED PILOT POLICY (D-016)` |
| **Background explanation** | Higher? | Lower? | Minimal? | SECONDARY semantic hypothesis; chỉ promote nếu team quyết định | `PRODUCT HYPOTHESIS` |
| **Prerequisite before use** | Có giải thích prerequisite trước khi dùng khái niệm? | Chỉ nhắc prerequisite khi cần hiểu update? | Speaker tự bổ sung prerequisite? | SECONDARY semantic hypothesis | `PRODUCT HYPOTHESIS` |
| **Standalone readability** | High? | High? | Lower? | SECONDARY semantic hypothesis | `PRODUCT HYPOTHESIS` |
| **Text density (`words_per_slide`)** | TBD | TBD | TBD | **SECONDARY/DIAGNOSTIC** | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (direction)` |
| **Bullet density (`bullets_per_slide`, `words_per_bullet`)** | TBD | TBD | TBD | **SECONDARY/DIAGNOSTIC** | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (direction)` |
| **Visual reliance (`image_slide_ratio`)** | TBD | TBD | TBD | **SECONDARY/DIAGNOSTIC** | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (direction)` |
| **Text:image block mix** | TBD | TBD | TBD | **SECONDARY/DIAGNOSTIC** | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (direction)` |
| **Layout mix (`layout_histogram`)** | TBD | TBD | TBD | **SECONDARY/DIAGNOSTIC** | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (direction)` |
| **Compression ratio** | TBD | TBD | TBD | **SECONDARY/DIAGNOSTIC** | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (direction)` |
| **Raw source-region coverage** | TBD | TBD | TBD | **SECONDARY/DIAGNOSTIC**; không thay Option C metric | `TEAM DECISION NEEDED` |
| **Source-order similarity** | Có giữ progression gần source hơn? | Có reorder theo takeaway/update? | Có reorder theo speaking arc? | **SECONDARY/DIAGNOSTIC** | `PRODUCT HYPOTHESIS` |
| **Topic/fact selection** | Phải khác theo definition nào? | Phải khác theo definition nào? | Phải khác theo definition nào? | **CORE outcome trên fact identity/topic key — definition chưa chốt** | `HUMAN DECISION / ACCEPTED PILOT POLICY (CORE: D-017) + TEAM DECISION NEEDED (obligations)` |
| **Speaker notes** | Không core | Không core | **Không core; FR-55=P3** | **NON-CORE / NOT TESTED trong pilot hiện tại** | `PROJECT REQUIREMENT (FR-55=P3) + HUMAN DECISION / ACCEPTED PILOT POLICY (non-core: D-017)` |
| **Target slide count** | Tuân thủ `target_slides` từ brief | Như teaching | Như teaching | CONTROL / hard constraint | `PROJECT REQUIREMENT + EXPERIMENT CONTROL` |
| **Mode-specific default slide count** | TBD | TBD | TBD | Không dùng làm CORE nếu chưa có decision | `TEAM DECISION NEEDED` |
| **Time-limit behavior trong causal pilot** | Giữ cùng `time_limit_minutes` | Giữ cùng `time_limit_minutes` | Giữ cùng `time_limit_minutes` | CONTROL | `EXPERIMENT CONTROL` |
| **Mode-specific default time limit ngoài causal pilot** | TBD | TBD | TBD | Ngoài B0 hiện tại | `TEAM DECISION NEEDED` |
| **Language behavior** | Feature/tokenizer phải tách theo `meta.language` | Như teaching | Như teaching | CONTROL | `EXPERIMENT CONTROL` |
| **Correctness / unsupported via `gold_claim`** | Báo riêng, không đổi presence theo mode | Như teaching | Như teaching | CORE quality floor; không aggregate coverage | `HUMAN DECISION / ACCEPTED PILOT POLICY (D-015)` |

### Purpose là definition/interpretation, không phải fuzzy score

**HUMAN DECISION / ACCEPTED PILOT POLICY D-017 + TEAM DECISION NEEDED.** B0 không được tạo `purpose_alignment = 4/5` hoặc một judge score cảm tính tương tự. Team phải operationalize purpose thành danh sách obligation/observable được pre-register, chủ yếu qua information selection, ví dụ ở mức hình thái:

```text
purpose definition
  → obligation IDs
    → required prerequisite/explanatory-chain categories (nếu team chọn)
    → required fact_identity/topic_key selection
    → explicit expected observable per obligation
```

Các ví dụ trên chỉ mô tả **shape của contract**, không gán nghĩa thật cho teaching/catchup/speaker_support. B0 báo từng obligation đạt/không đạt/không quan sát được và error trace; “purpose phù hợp” chỉ là derived human interpretation từ bảng obligation, **NOT A METRIC** và không được aggregate thành score. Cho tới khi obligation được team định nghĩa/freeze, purpose evaluation là `HOLD`.

### Cách hiểu đúng các dòng `PRODUCT HYPOTHESIS`

**PRODUCT HYPOTHESIS.** Các từ `higher?`, `lower?`, `minimal?` chỉ là ứng viên để team phản biện. Chúng **không** được suy ra thành fact từ các guideline nghề nghiệp hoặc từ cảm giác “speaker slide nên ít chữ”. Nguồn gần nhất cho trục standalone deck vs presentation slide chỉ ở mức E3, không đủ support major recommendation theo `EVIDENCE_POLICY.md`.

**INFERENCE.** EffectivePresentationScorer chỉ là E3 artifact trên video, không đủ làm external evidence cho major recommendation trong Deck Agent. Nó chỉ gợi ý một candidate cần kiểm: presentation có thể đúng topic/structure nhưng thiếu prerequisite/giải thích; nó không support dấu cụ thể cho ba mode.

**EXTERNAL EVIDENCE.** *X+Slides* kết luận audience adaptation chủ yếu thay đổi information selection. Điều này support việc đưa `topic_selection`/source-conditioned coverage vào danh sách candidate, nhưng **không** support rằng teaching/catchup/speaker_support phải chọn topic theo một pattern cụ thể.

---

## 2. Conflict và human resolution cho pilot

### Conflict 1 — FR-20 vs *X+Slides*: hierarchy đã resolve cho pilot

**PROJECT REQUIREMENT.** FR-20 mô tả khác biệt của ba mode nằm ở “cách dùng chữ/ảnh/mật độ nội dung” và yêu cầu có checklist eval.

**EXTERNAL EVIDENCE.** *X+Slides* (E1, arXiv 2606.19256; hiện mới verify sâu qua HTML/tool summary, chưa đối chiếu PDF gốc) cho thấy trong **audience conditioning**, khác biệt chủ yếu nằm ở **information selection**. Các audience của paper không tương đương ba mode Deck Agent.

**INFERENCE.** Hai frame đang chỉ vào hai locus khác nhau:

```
FR-20                → presentation surface: text / image / density
X+Slides precedent   → content selection: nói fact/topic nào
```

**HUMAN DECISION D-017.** Pilot chọn purpose + information selection làm **CORE**; surface attributes là **SECONDARY/DIAGNOSTIC**. Đây là product/evaluation policy, không phải kết luận external rằng FR-20 sai. Nếu team sau này muốn promote surface feature thành CORE, phải làm explicit bằng decision/version mới trước confirmatory run.

B0 vẫn mô tả surface feature để biết signal nằm ở đâu, nhưng không được dùng surface-only difference làm bằng chứng rằng purpose đã đúng.

### Conflict 2 — `speaker_notes` vs FR-55: resolved cho pilot

**PROJECT REQUIREMENT.** Deck IR có field `speaker_notes`, nhưng FR-55 xếp “speaker notes tự sinh” vào **P3 Could**.

**INFERENCE.** Nếu `speaker_notes` được coi là core distinguishing feature của `speaker_support`, P0/P1 không thể thỏa định nghĩa mode đó. Đây là thay đổi scope/priority FR, không phải lựa chọn metric.

**HUMAN DECISION D-017.** Giữ FR-55=P3; `speaker_notes` không core và không nằm trong pilot hiện tại. `speaker_support` phải được định nghĩa bằng purpose + information selection; surface feature chỉ diagnostic. Nếu team đổi priority sau này, đó là decision/version mới và cần pilot notes riêng.

---

## 3. Sign register candidate cho B1 — chỉ freeze sau B0

Behavior matrix ở §1 là semantic draft. B0 chỉ mô tả direction/error trên source set nhỏ; **không cần** freeze confirmatory sign cho mọi surface feature. Sau B0, nếu có signal đáng theo, B1 mới chọn independent/held-out sample, CORE/SECONDARY hypothesis và freeze sign register trước B1 run.

### Quy ước

- `>`: mode bên trái có giá trị cao hơn mode bên phải.
- `<`: thấp hơn.
- `=`: equivalent trong một margin phải khai báo; “không significant” không được dùng thay equivalence.
- `TBD`: blocking; contrast không được chạy như confirmatory.
- `CORE`: định nghĩa mode; success rule chỉ có hiệu lực sau khi team freeze preregistration B1, không dùng threshold candidate hiện tại làm accepted gate.
- `SECONDARY`: hypothesis phụ; vẫn pre-register nhưng không định nghĩa mode.
- `NOT_TESTED`: không thuộc pilot version này.

| ID | Attribute | Tier | Teaching vs Catchup | Teaching vs Speaker | Catchup vs Speaker | Equivalence margin nếu `=` | Nhãn |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ST-01 | `slide_count` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-02 | `words_per_slide` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-03 | `bullets_per_slide` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-04 | `words_per_bullet` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-05 | `image_slide_ratio` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-06 | `text_image_block_ratio` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-07 | `layout_histogram` distance/profile | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-08 | `compression_ratio` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-09 | raw `source_region_coverage` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-10 | `source_order_similarity` | SECONDARY | TBD sau B0 | TBD sau B0 | TBD sau B0 | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (sign)` |
| ST-11 | topic/fact selection over `fact_identity/topic_key` | CORE | TBD từ mode definition | TBD từ mode definition | TBD từ mode definition | TBD | `HUMAN DECISION / ACCEPTED PILOT POLICY (tier: D-017) + TEAM DECISION NEEDED (obligations/sign)` |
| ST-12 | prerequisite explanation | SECONDARY trừ khi explicit promote | TBD | TBD | TBD | TBD | `PRODUCT HYPOTHESIS` |
| ST-13 | `speaker_notes` length/coverage | NOT_TESTED / NON-CORE | — | — | — | — | `PROJECT REQUIREMENT (FR-55=P3) + HUMAN DECISION / ACCEPTED PILOT POLICY (D-017)` |
| ST-14 | `universal_core_coverage` | CORE quality floor | Không dùng để differentiate; phải report riêng | Như trái | Như trái | TBD nếu B1 dùng equivalence | `HUMAN DECISION / ACCEPTED PILOT POLICY (D-016)` |
| ST-15 | `mode_required_coverage` | CORE | Không so denominator chéo mode; evaluate theo required set của từng mode | Như trái | Như trái | — | `HUMAN DECISION / ACCEPTED PILOT POLICY (category: D-016) + TEAM DECISION NEEDED (mapping)` |
| ST-16 | `optional/not_expected` presence | SELECTION DIAGNOSTIC / NON-AGGREGATED | Descriptive ở B0 | Descriptive ở B0 | Descriptive ở B0 | — | `HUMAN DECISION / ACCEPTED PILOT POLICY (D-016)` |

**TEAM DECISION NEEDED.** B0 bị block bởi definition purpose/information selection và Option C category chưa được review, **không** bởi việc surface sign còn TBD. B1 chỉ được freeze sau B0; khi đó mọi row được chọn confirmatory phải hết TBD và dùng source/sample độc lập hoặc held-out khỏi B0.

---

## 4. Observable behavior không đồng nghĩa quality

**INFERENCE.** `words_per_slide`, image ratio, layout histogram và compression ratio có thể khác rất rõ nhưng vẫn không chứng minh deck phù hợp mục đích. Một system có thể “pass” contrast bằng cách nhồi chữ vào catchup và cắt chữ khỏi speaker_support.

**RECOMMENDATION.** Mọi manipulation-check report phải đi cùng:

- artifact/schema validity;
- source-quality vector đã được accept hoặc manual quality floor pre-registered;
- invalid/retry rate;
- ghi rõ feature nào chỉ là surface cue.

Không được kết luận “mode tốt” chỉ từ `BETWEEN > WITHIN`.

---

## 5. Remaining decision checklist cho team/giảng viên

Các mục sau phải có owner + ngày + rationale trước khi draft có thể thành spec accepted:

1. Định nghĩa **primary use** và user context của từng mode, rồi chuyển purpose thành obligation/observable cụ thể; không tạo fuzzy score.
2. Chốt information selection trên `fact_identity/topic_key`; author/review `gold_claim` riêng cho correctness; gán `universal_core`, `mode_required`, `optional/not_expected` trước output.
3. Chốt rule xử lý disagreement/empty category/version change của Option C.
4. Chốt language stratum/tokenization cho B0; không dùng English readability formula cho tiếng Việt.
5. Chốt source set, repeat count, control và exclusion/retry rule B0; `target_slides`/`time_limit_minutes` giữ cố định giữa mode.
6. Version/hash mode definition + purpose obligations + separated fact records/category data và ghi vào B0 config trước run.
7. **Sau B0:** quyết định có thiết kế B1 không; nếu có, chọn held-out/independent sample, confirmatory hypothesis, sign/equivalence margin, threshold và analysis plan.

Đã resolve trong human review: coverage semantics (D-015), Option C (D-016), core hierarchy + FR-20/X+Slides stance + `speaker_notes` P3/non-core (D-017).

## 6. Điều kiện để promote draft

Draft chỉ được chuyển khỏi `06_design/drafts/` khi:

- mọi `TEAM DECISION NEEDED` có owner và decision record;
- definition cụ thể của ba mode và Option C categories được resolve tường minh;
- B1 sign register chỉ cần hết TBD khi B1 được thiết kế sau B0;
- team/giảng viên xác nhận đây là **định nghĩa sản phẩm**, không phải claim external;
- decision được ghi vào `05_decisions/DECISION_LOG.md` bởi human-approved workflow.

Cho tới lúc đó, status đúng là **core hierarchy accepted; specific definitions block B0; B1 not yet designed/frozen**.
