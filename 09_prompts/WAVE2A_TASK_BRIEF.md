# Task Brief — Wave 2A Decision Gate + Pilot Design

> **Đây là task brief cho một session mới (Claude hoặc Codex).** Nó tự chứa đủ context để bắt đầu từ đầu. Trạng thái workspace tại thời điểm viết: 2026-08-14, Wave 2A research vừa xong, chưa qua gate.

## 0. Bạn đang ở đâu

Workspace này thiết kế Evaluation Framework cho **Deck Agent** (hệ thống AI sinh deck PPTX từ tài liệu nguồn). Đã đi qua:

```
RQ00 (audit nội bộ) → ACCEPTED
   ↓
WAVE 1: RQ01 (WHAT) · RQ06 (HOW) · RQ08 (AGAINST WHAT) → ANSWERED
   ↓
WAVE 1 DECISION GATE (03_research/WAVE1_SYNTHESIS.md) → 8 ACCEPT / 3 HOLD / 2 REJECT / 1 BLOCKING
   ↓
WAVE 2A: RQ02 (source quality) · RQ03 (slide_type) → ANSWERED, CHƯA QUA GATE  ← BẠN Ở ĐÂY
```

**Nhiệm vụ của bạn: 4 artifact, rồi DỪNG.** Không mở Wave 2B (RQ04/RQ05), không mở RQ07, không chạy pilot.

## 1. BẮT BUỘC đọc trước (theo thứ tự)

1. `AGENTS.md` (hoặc `CLAUDE.md` — byte-identical) — research discipline
2. `00_context/PROJECT_CONTEXT.md`, `EVALUATION_MISSION.md`, `SYSTEM_MAP.md`, `TERMINOLOGY.md`
3. `01_source_docs/SOURCE_INDEX.md` — lưu ý phần **authority scope**
4. `04_evidence/EVIDENCE_POLICY.md` — E1/E2/E3
5. **`03_research/WAVE1_SYNTHESIS.md`** — Decision Gate Wave 1, ràng buộc còn hiệu lực
6. **`03_research/RQ02/`** — `landscape.md`, `evidence.md`, `recommendation.md`
7. **`03_research/RQ03/`** — `landscape.md`, `evidence.md`, `recommendation.md`
8. `05_decisions/DECISION_LOG.md` (D-001…D-014), `OPEN_QUESTIONS.md` (Q-001…Q-014), `REJECTED_APPROACHES.md` (R-001, R-002)
9. `RESEARCH_STATUS.md`

Tham khảo khi cần: `03_research/RQ01/`, `RQ06/`, `RQ08/`, `current_evaluation_audit.md`, và PDF gốc trong `01_source_docs/`.

## 2. Nguyên tắc bất di bất dịch

- Tài liệu trong `01_source_docs/` là **PROJECT SOURCE**, không phải Source of Truth.
- Phân loại mọi claim: `PROJECT SOURCE` / `EXTERNAL EVIDENCE` / `INFERENCE` / `RECOMMENDATION`.
- **Không bịa** paper/số liệu/repo. Không verify được → `UNVERIFIED`.
- **Working hypothesis ≠ decision.** Không dùng "supersedes", "we will use", "the correct approach is".
- **Research không tự thành design.** KHÔNG ghi vào `05_decisions/DECISION_LOG.md`, KHÔNG sửa `06_design/` (trừ file draft mới ở Task 3).
- **Ngôn ngữ: tiếng Việt.** Giữ tiếng Anh: tên paper/metric/benchmark/project, code identifier, schema field (`slide_type`, `source_ref`, `critical_facts`, `ContentPlanner`, `Deck IR`).

### Ràng buộc từ Wave 1 Gate còn hiệu lực

| ID | Nội dung |
| --- | --- |
| G-03 (HOLD) | Taxonomy 5 lớp L0–L5 chưa freeze. RQ03 vừa tìm ra 3 chỗ nó gãy — đáng chú ý là **thiếu contrastive reference frame** (so với output anh em), lớp chứa cả regression tracking lẫn ablation. |
| G-05 (HOLD) | F2 (NLI/AlignScore/FActScore) chưa accept. **RQ02 đã đảo chiều: AlignScore là lựa chọn kém nhất, SummaC-Conv tệ hơn nữa. Chỉ MiniCheck đi tiếp — và vẫn là HOLD.** |
| G-07 (HOLD) | Không chốt cỡ mẫu human. Ghi `TBD`. |
| G-09 (ACCEPT) | Mọi model-based evaluator phải đo noise floor trước khi dùng track regression. |
| R-001 (REJECT) | "Judge ≥80% human ceiling" **không** được dùng làm threshold pass/fail. |
| R-002 (REJECT) | Baseline B-1 **không** chứng minh giá trị Deck IR — chỉ chứng minh giá trị việc tách planning stage. |
| Q-014 (BLOCKING) | A1 (`without ContentPlanner`) và B-1 đang trùng nhau, phải resolve trước implementation. |

---

# TASK 1 — `03_research/WAVE2A_DECISION_GATE.md`

**Không tóm tắt lại RQ02/RQ03.** Extract candidate decision và phân loại. Format mỗi mục:

```
Finding / Claim
↓
ACCEPT / HOLD / REJECT / OPEN
↓
Evidence strength
↓
Reason
↓
What changes if accepted?  (file/design nào bị ảnh hưởng)
↓
What must be tested before acceptance?  (pilot/evidence nào resolve được)
```

### Bắt buộc review ít nhất 17 mục sau

| ID | Candidate |
| --- | --- |
| W2A-01 | Content quality giữ riêng `coverage / unsupported / incorrectness`, không gộp score |
| W2A-02 | Source quality cần hai chiều `deck → source` và `source → deck` |
| W2A-03 | `critical_facts` là basis cho coverage |
| W2A-04 | `critical_facts` được LLM generate + human review một lần + freeze |
| W2A-05 | Coverage = topic presence hay correct presence? |
| W2A-06 | MiniCheck là candidate daily evaluator |
| W2A-07 | MiniCheck reverse-direction cho coverage |
| W2A-08 | Logic phân biệt hallucination vs incorrectness bằng counterpart |
| W2A-09 | Derived numbers phải xử lý riêng (`derived_number_queue`) |
| W2A-10 | FR-02 được evaluate như manipulation check |
| W2A-11 | Không tạo một `Structure Score` chung |
| W2A-12 | `slide_type` evaluation cần same-source paired design |
| W2A-13 | Phải đo WITHIN-mode noise trước khi đọc BETWEEN-mode difference |
| W2A-14 | Team phải viết expected behavior spec cho 3 `slide_type` **trước khi** test |
| W2A-15 | Coverage/importance có thể phải conditioned theo `slide_type` |
| W2A-16 | Forced-choice recovery test là candidate |
| W2A-17 | `speaker_notes` không được dùng làm core distinguishing feature nếu FR-55 vẫn P3 |

### Disposition đề xuất từ người review (bạn được phép phản biện, kèm lý do)

```
ACCEPT principle:  W2A-01, 02, 09, 10, 11, 12, 13, 14
HOLD pending pilot: W2A-03, 04, 06, 07, 08, 15, 16
OPEN product decision: W2A-05, 17
```

**Nhấn mạnh: `MiniCheck = HOLD`, KHÔNG phải ACCEPT.** RQ02 tự nói rõ transfer sang slide deck chưa được external evidence validate — search độc lập lần 2 vẫn 0 nguồn áp F2 lên slide deck, và rủi ro số 1 là dùng text deck ngắn làm premise = chế độ ngoài phân phối huấn luyện.

**Không tự promote decision vào `DECISION_LOG.md`.**

---

# TASK 2 — `07_experiments/pilot/PILOT_PLAN_WAVE2A.md`

Chỉ **thiết kế** experiment. **KHÔNG chạy.** Chia 2 phần độc lập: Pilot A (RQ02) và Pilot B (RQ03).

Với **mỗi** pilot ghi đủ: hypothesis · controlled variables · input · manipulated variable · expected output · metric candidate · **pass/fail định trước** · failure interpretation · artifacts cần lưu · estimated implementation effort · prerequisite code/data · decision nào pilot này unblock.

## Pilot A — Source Quality Evaluator

Mục tiêu là **để nó fail được**, không phải để chứng minh recommendation đúng.

### A1 — Discrimination smoke test

Source cực nhỏ ~5 facts `A B C D E`. Output thủ công:

```
O0 = A B C D E        correct
O1 = A B C            coverage gap
O2 = A B C D F        hallucination
O3 = A B C D'         incorrectness
O4 = derived/paraphrased fact   transformed-but-correct
```

**Pre-register bảng kỳ vọng TRƯỚC khi chạy:**

| Output | Coverage | Hallucination | Incorrectness | Derived |
| --- | ---: | ---: | ---: | ---: |
| O0 | high | 0 | 0 | 0 |
| O1 | lower | 0 | 0 | 0 |
| O2 | maybe high | 1 | 0 | 0 |
| O3 | high | 0 | 1 | 0 |
| O4 | high | 0 | 0 | 1 |

**Pass/fail:** nếu evaluator không phân biệt được O1/O2/O3 trên dataset cố tình đơn giản này → **không được promote**.

### A2 — MiniCheck direction test

Đây là uncertainty lớn nhất của RQ02. MiniCheck thường chạy `Document → Claim`; đề xuất coverage lại muốn `Deck text → critical fact` — premise ngắn dạng slide, có thể khác distribution huấn luyện.

Thiết kế deck thủ công từ source facts, gồm: fact present **verbatim** · present **paraphrased** · present **compressed** · **absent** · **contradicted**.

Output: precision / recall / confusion matrix. Không cần benchmark lớn. Câu hỏi duy nhất: *"reverse-direction MiniCheck có đủ sane để đi tiếp không?"*

### A3 — Numeric transformation probe

Vài chục synthetic example dạng `source: 100 → 120` / `output: +20%`. Bao gồm: correct derivation · wrong derivation · rounding · unit conversion · percentage-point vs percentage error.

Câu hỏi: **MiniCheck/NLI có phạt derived fact ĐÚNG không?** Nếu có → `derived_number_queue` thực sự cần tồn tại.

### A4 — Hallucination vs Incorrectness classifier test

RQ02 chưa có evidence mạnh cho logic: `unsupported → counterpart exists? yes=incorrect / no=hallucination`.

Tập manual label nhỏ: hallucination cases · incorrectness cases · **ambiguous cases**. Ví dụ ambiguous: source *"Revenue increased"*, deck *"ARR increased"* — Revenue và ARR có phải counterpart không?

**Ghi rule TRƯỚC, rồi mới test. Không được nhìn output rồi sửa rule cho khớp.**

## Pilot B — `slide_type` Manipulation Check

Mục tiêu **không phải** "teaching deck đẹp hơn catchup deck?" mà là *"khi chỉ đổi `slide_type`, output có thay đổi vượt noise và đúng hướng định trước không?"*

**Phụ thuộc:** Pilot B chỉ hoàn thiện được sau khi `SLIDE_TYPE_BEHAVIOR_SPEC.md` (Task 3) đủ chín để pre-register hướng kỳ vọng.

### Thiết kế

Paired theo source: mỗi source × 3 mode × R lần chạy, **chỉ đổi `meta.slide_type`**, mọi thứ khác giữ nguyên.

**Không chốt `R = 5` thành law.** Pilot dùng số nhỏ đủ để estimate noise.

### Hai loại distance

- **WITHIN** — khác biệt khi **không** đổi mode (teaching run1 vs teaching run2) = **noise**
- **BETWEEN** — khác biệt khi đổi mode (teaching vs catchup) = **signal**

```
BETWEEN ≈ WITHIN   → slide_type chưa có signal rõ
BETWEEN >> WITHIN  → có evidence mode thực sự ảnh hưởng output
```

Đây là logic quan trọng nhất của RQ03.

### Feature set khởi đầu — chỉ F1 rẻ và rõ

```
slide_count · words_per_slide · bullets_per_slide · words_per_bullet
image_slide_ratio · text_image_block_ratio · layout_histogram
compression_ratio · source_region_coverage · source_order_similarity
```

Thêm `topic_selection` nếu pipeline extract topic đã sẵn sàng. **Chưa cần** prerequisite-explanation judge, forced-choice MLLM, hay human study ở pilot đầu.

**Ràng buộc đã biết:** `geometry` không có ở vertical slice (architecture doc) → mọi metric diện tích/whitespace chưa khả dụng.

### Ba check

**Check 1 — có khác thật không?** `BETWEEN > WITHIN` + effect size chuẩn hóa theo WITHIN + permutation test (hoán vị nhãn trong từng source) + bootstrap CI. Không có model trong vòng lặp.

**Check 2 — có đúng hướng không?** So với bảng dấu **pre-registered** trong `SLIDE_TYPE_BEHAVIOR_SPEC.md`. Ví dụ spec khai *"speaker_support: words/slide thấp hơn catchup"* → kiểm đúng dấu. **Không nhìn data rồi mới chọn hướng.**

**Check 3 — có nhận diện được mode không?**
- **3a** classifier deterministic trên feature vector. **Split train/test theo source** (leave-one-source-out), KHÔNG random deck — nếu cùng source xuất hiện ở cả train và test, classifier có thể học topic thay vì mode.
- **3b** MLLM forced-choice 3 lớp, chance = 1/3, kiểm định nhị thức + noise floor. **Đây là later pilot**, không phải điều kiện để pilot đầu chạy.

Kiểm chéo 3a ↔ 3b để phát hiện judge chỉ đang đọc độ dài.

---

# TASK 3 — `06_design/drafts/SLIDE_TYPE_BEHAVIOR_SPEC.md`

**Đây là PRODUCT SPEC DRAFT, không phải external truth.** Tạo thư mục `06_design/drafts/` nếu chưa có — đặt ở `drafts/` để phân biệt rõ với design đã accept.

RQ03 tìm **không thấy** chuẩn external nào định nghĩa `teaching | catchup | speaker_support`. Nên bạn **không thể research ra đáp án đúng** — team phải tự định nghĩa. Bạn chỉ tạo draft để team review.

Format bảng:

| Attribute | Teaching | Catchup | Speaker Support | Nhãn |
| --- | --- | --- | --- | --- |
| Primary use | học/hiểu | đọc lại nhanh | hỗ trợ người thuyết trình | TEAM DECISION NEEDED |
| Expected information selection | TBD | TBD | TBD | |
| Background explanation | higher? | lower? | minimal? | PRODUCT HYPOTHESIS |
| Text density | TBD | TBD | TBD | weak evidence |
| Visual reliance | TBD | TBD | TBD | weak evidence |
| Standalone readability | high? | high? | lower? | PRODUCT HYPOTHESIS |
| Speaker notes | not core | not core | currently P3 | PROJECT SCOPE CONFLICT |
| Target slide count | TBD | TBD | TBD | |
| Time-limit behavior | TBD | TBD | TBD | |

**Mỗi dòng bắt buộc gắn một nhãn:** `PROJECT REQUIREMENT` / `EXTERNAL EVIDENCE` / `PRODUCT HYPOTHESIS` / `TEAM DECISION NEEDED`.

**Không** biến *"speaker_support ít chữ"* thành fact chỉ vì nghe hợp lý.

**Hai conflict phải flag rõ, không được giấu:**
1. **FR-20 vs X+Slides.** FR-20 giả định khác biệt nằm ở chữ/ảnh/mật độ; X+Slides (E1) kết luận audience adaptation chủ yếu đổi **information selection**. RQ03 đã cảnh báo.
2. **`speaker_notes` vs FR-55.** FR-55 là **P3 Could**. Dùng `speaker_notes` làm core distinguishing feature = **thay đổi ưu tiên FR**, không phải chọn metric. Phải flag, không được lặng lẽ dùng.

---

# TASK 4 — `03_research/cross_rq/SOURCE_COVERAGE_VS_SLIDE_TYPE.md`

Conflict này do RQ03 phát hiện, **không được để nó bị chôn trong RQ03**.

**Câu hỏi:** một fact quan trọng có quan trọng như nhau cho cả 3 `slide_type` không?

Vấn đề cụ thể: *"background vắng mặt"* là **coverage gap** dưới frame nguồn, nhưng **intentional omission** dưới frame brief. Nếu RQ02 đo coverage **không** điều kiện hóa theo `slide_type`, nó sẽ **phạt `catchup` vì đã làm đúng**.

So sánh ít nhất:

- **Option A** — cùng `critical_facts`, cùng weight cho mọi mode. Ưu: đơn giản, dễ so version. Nhược: phạt mode vì cố ý bỏ background.
- **Option B** — cùng fact pool, weight theo mode (vd. `fact_A: teaching=1.0, catchup=0.3, speaker=0.5`). Ưu: model đúng mục tiêu presentation. Nhược: phải định nghĩa weight, benchmark authoring phức tạp hơn, **nguy cơ team tune benchmark cho khớp output**.
- **Option C** — nếu có alternative hợp lý khác, đưa thêm.

**KHÔNG tự chọn.** Output mỗi option phải cho biết ảnh hưởng tới: benchmark authoring · fairness · reproducibility · interpretability · risk of benchmark gaming · RQ07.

Lưu ý: RQ03 có precedent gần với cách weight theo condition, nhưng các condition external **không giống** ba mode của Deck Agent.

---

# Khi hoàn thành 4 task

1. Update `RESEARCH_STATUS.md` ở mức **"Wave 2A research complete, decision/pilot review pending"**
2. Báo danh sách file đã tạo/sửa
3. Liệt kê các **HUMAN/TEAM DECISION đang chặn** bước tiếp
4. **DỪNG**

## Cấm

- ❌ Chạy pilot
- ❌ Research RQ04 / RQ05 (Wave 2B)
- ❌ Research RQ07 (benchmark)
- ❌ Tự accept recommendation
- ❌ Ghi vào `05_decisions/DECISION_LOG.md`
- ❌ Sửa `06_design/` (trừ tạo file draft mới ở Task 3)

Vai trò của bạn ở bước này đã chuyển từ **research agent** sang **experiment designer** — nhưng **chưa phải người ra quyết định**. Literature đã tới giới hạn của nó ở vài câu quan trọng: MiniCheck có transfer sang deck không, bịa/sai có tách được không, ba `slide_type` thực tế khác nhau thế nào. Những câu đó từ đây phải trả lời bằng **dữ liệu của chính Deck Agent**, không phải bằng việc tìm thêm 20 paper.
