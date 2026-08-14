# RQ01 — Landscape: Evaluation Dimensions trong AI Presentation Generation

Ngày research: **2026-08-14**. Chi tiết từng nguồn + mức verify: `evidence.md` (cùng thư mục).

Tài liệu này trả lời câu hỏi 1–4 của RQ01 (dimension nào tồn tại · định nghĩa thế nào · overlap ở đâu · cái nào thực chất deterministic). Câu 5–7 (applicability, gap của §8, taxonomy đề xuất) nằm ở `recommendation.md`.

---

## 0. Terminology mà community thực sự dùng

Trước khi vào nội dung — các keyword ban đầu (`AI presentation generation`, `document-to-presentation`) **có bắt được literature**, nhưng cụm cho kết quả tốt nhất là:

- `slide generation` / `presentation generation` (phổ biến nhất trong tên benchmark)
- `automated slide generation` (dùng trong abstract)
- `poster generation` (task họ hàng gần, cùng nhóm tác giả/venue, metric chuyển được một phần)
- `document-to-slides` / `doc2ppt` (thế hệ 2021–2022)
- `agentic slide generation` (2026, khi hệ thống có editing loop)
- `audience-conditioned slide generation` (2026, mới)
- `slide design flaw` / `design critique` (nhánh HCI, tách khỏi nhánh NLP)

**EXTERNAL EVIDENCE:** nhánh **NLP/ML** (PPTAgent, PresentBench, AutoPresent) và nhánh **HCI** (SlideAudit, UIST) gần như không cite nhau và định nghĩa dimension khác hẳn nhau. NLP nghĩ theo "chấm điểm output"; HCI nghĩ theo "phân loại lỗi thiết kế". Đây là điểm quan trọng: **không có một taxonomy chuẩn nào được cộng đồng chia sẻ** — mỗi paper tự dựng.

**INFERENCE:** Vì vậy, kỳ vọng "tìm ra taxonomy đúng đã có sẵn ngoài kia rồi copy về" là không thực tế. Việc của RQ01 là **tổng hợp có nguyên tắc**, không phải chọn một cái có sẵn.

---

## 1. Bảng tổng hợp: mỗi nguồn tổ chức dimension thế nào

**EXTERNAL EVIDENCE** — mọi ô đều trích từ nguồn tương ứng trong `evidence.md`.

| Nguồn (năm) | Artifact | Số dimension top-level | Tên dimension | Method |
| --- | --- | --- | --- | --- |
| **DOC2PPT** (2021/22) | slides | 2 auto + human | ROUGE-SL, mIoU | reference-based tự động + human eval |
| **PPTAgent / PPTEval** (2025) | PPTX | 3 (+1 tách riêng) | Content, Design, Coherence · *(Success Rate tách riêng)* | MLLM-judge 1–5 · SR deterministic |
| **AutoPresent / SlidesBench** (CVPR 2025) | slides từ code | 3 nhóm | ref-based (element/content/color/position) · ref-free (Text/Image/Layout/Color) · Executability | ref-based tự động · ref-free GPT-4o 0–5 · exec deterministic |
| **Paper2Poster** (NeurIPS 2025) | poster | 4 | Visual Quality, Textual Coherence, Holistic (6 tiêu chí), PaperQuiz | ref-based + PPL + VLM-judge + **extrinsic quiz** |
| **SlideAudit** (UIST 2025) | slide có sẵn | 5 dim / 27 flaw | Composition & Layout, Typography, Color, Imagery, Animation & Interaction | human annotation + so sánh khả năng detect của LLM |
| **SlidesGen-Bench** (2026-01) | slides | 3 | Content (QuizBank), Aesthetics (4 metric), Editability (PEI L0–L5) | **extrinsic quiz** + **deterministic** + human gating |
| **PresentBench** (2026-03) | slide deck | 5 | Presentation Fundamentals, Visual Design & Layout, Content Completeness, Content Correctness, Content Fidelity | checklist per-instance, binary, judge từng item + evidence |
| **Learning to Present** (2026-03) | slides | 6 reward | Code Rules, Render Quality, Aesthetic HTML, Aesthetic Visual, Content Quality, Inverse Specification | **2 deterministic + 3 judged + 1 hỗn hợp** |
| **AeSlides** (2026-04) | slides | 4 (chỉ layout) | Distorted Aspect Ratio, Excessive Whitespace, Element Collision, Visual Imbalance | **100% deterministic/programmatic** |
| **UniPPTBench** (2026-05) | slides | 2 tầng | shared metrics + scenario-specific metrics | không xác định được từ abstract |
| **X+Slides** (2026-06) | slides | 4 | Audience Coverage, Domain-wise Coverage, Efficiency, Correctness | source-grounded probe + audience-specific weight |
| **AI-Generated Slides study** (2026-05) | lecture slides | 3 | Factual accuracy, Completeness, Pedagogical soundness | human raters (instructor + student) |
| **§8 architecture doc** (PROJECT SOURCE) | PPTX | 4 | Content fidelity, Ready-to-use, Design consistency, Structure | MLLM-as-judge cho **cả 4** + human sample |

**Quan sát 1 — số dimension dao động 3–6, nhưng cách nhóm mới là điểm khác biệt thật.** Không nguồn nào đồng ý với nguồn nào về top-level. Nhưng khi phân rã xuống sub-aspect thì các concept lặp lại rất nhất quán (xem §2).

**Quan sát 2 — mọi nguồn từ 2025 trở đi đều tách ít nhất một metric deterministic ra khỏi phần judged.** PPTAgent (SR), AutoPresent (Executability), Learning to Present (Code Rules + Render Quality), SlidesGen-Bench (toàn bộ Aesthetics), AeSlides (toàn bộ). **§8 là nguồn duy nhất trong bảng đặt MLLM-judge lên cả 4 dimension mà không tách phần deterministic nào ra.**

---

## 2. Câu hỏi 1 & 2 — Dimension nào thực sự xuất hiện, và định nghĩa cụ thể

Dưới đây là **13 concept** xuất hiện lặp lại. Với mỗi concept: định nghĩa từ nguồn (không phải tôi tự viết), số nguồn độc lập support, và method mà external work dùng.

### C1. Source grounding / faithfulness (không bịa)

> PresentBench **Content Fidelity**: "verifies strict adherence to the provided background materials" theo từng trang; "any unsupported, contradictory, or newly introduced detail is treated as hallucinated".
> X+Slides **Correctness**: "verifies whether slide claims are supported by the source".
> AI-Generated Slides study **Factual accuracy**: "whether content was correct and free of hallucinations".

- **Số nguồn:** 4 (PresentBench, X+Slides, AI-slides study, Learning to Present "factual grounding")
- **Method external:** checklist binary per-item (PresentBench) hoặc source-grounded probe (X+Slides). **Không nguồn nào dùng holistic 1–5 cho fidelity.**
- **Ghi chú:** đây là chiều mà PresentBench nói rõ human ranking nhanh **không** bắt được (human ưu tiên structure/layout dễ thấy hơn).

### C2. Coverage / Completeness (đủ ý quan trọng)

> PresentBench **Content Completeness**: "verifies coverage using an instance-specific, instruction-derived checklist"; đúng thứ tự section, key point được xử lý đủ.
> Paper2Poster **Content Completeness** (trong Information Score).
> X+Slides **Audience Coverage** + **Domain-wise Coverage**.
> DocPres (2406.06556) **Coverage** ở 2 mức: paragraph–slide và sentence–bullet.

- **Số nguồn:** 4–5
- **Method:** checklist / probe list dựng sẵn từ nguồn, không phải judge tự do.
- **Ghi chú:** X+Slides là nguồn duy nhất nói coverage **phụ thuộc audience** — cùng thông tin, trọng số khác nhau.

### C3. Instruction / brief compliance (deck có làm đúng thứ được yêu cầu không)

> PresentBench **Content Correctness**: "instruction-mandated content is presented correctly, i.e., consistent with the provided background materials"; thiếu item = lỗi.
> Learning to Present **Inverse Specification**: cho LLM khôi phục spec gốc **chỉ từ deck**, đo topic similarity, **audience match**, **slide count accuracy**, theme coverage.
> Learning to Present **Code Rules**: title presence, section count adherence, word count ratios (deterministic).

- **Số nguồn:** 2–3
- **Method:** hỗn hợp — phần đếm được (số slide, độ dài) deterministic; phần ngữ nghĩa (audience match) judged.
- **Ghi chú:** đây là dimension **§8 hoàn toàn không có**, và cũng là dimension map thẳng nhất vào FR-02/FR-20/FR-23.

### C4. Narrative coherence / logical flow (mạch của cả deck)

> PPTEval **Coherence**: "Structure develops progressively, incorporating essential background information." — chấm ở **presentation level**, không phải slide level.
> Paper2Poster **Logical Flow** (Information Score).
> PresentBench **Presentation Fundamentals**: theme clarity, cross-slide logical flow, title relevance.
> UniPPTBench: "overall coherence" là generic criterion.

- **Số nguồn:** 4
- **Method:** MLLM-judge holistic là chuẩn hiện tại.
- **Cảnh báo mạnh:** đây là dimension **judge yếu nhất**. PPTEval human correlation: Coherence Pearson **0.55** / Spearman **0.57**, so với Design 0.90 / 0.88.

### C5. Slide decomposition & information allocation / density

> PPTEval **Content** (một phần): "Text should be concise... avoid using too many texts".
> SlidesBench **Text**: "avoid using too many texts and keep words concise".
> PresentBench **Presentation Fundamentals**: conciseness.
> X+Slides **Efficiency**: "delivered utility per unit of attention cost".

- **Số nguồn:** 4
- **Method:** judged, trừ X+Slides Efficiency (tính từ coverage/attention cost).
- **Ghi chú:** **không nguồn nào coi "chia bao nhiêu slide" là dimension riêng.** Nó xuất hiện dưới dạng conciseness/density trên từng slide, hoặc slide count accuracy trong brief compliance. Đây là finding quan trọng cho Deck Agent (xem `recommendation.md` §6).

### C6. Textual/linguistic quality trên slide

> PPTEval **Content**: "grammatically sound".
> PresentBench **Presentation Fundamentals**: linguistic quality.
> Paper2Poster **Textual Coherence**: perplexity dưới Llama-2-7b-hf.
> SlidesBench **Text**: title "simple and clear to indicate the main point".

- **Số nguồn:** 4
- **Method:** judged; Paper2Poster dùng PPL (deterministic nhưng PPTAgent chứng minh PPL gần như không tương quan với content quality — Pearson −0.02).

### C7. Visual / aesthetic quality

> PPTEval **Design**: "Harmonious colors and proper layout ensure readability, while visual elements like geometric shapes enhance the overall appeal."
> Paper2Poster Aesthetic Score = {Element Quality, Layout Balance, Engagement}.
> SlidesBench **Color**, **Image**.
> SlideAudit: **Typography** (4 flaw), **Color** (3 flaw), **Imagery** (3 flaw).
> SlidesGen-Bench Aesthetics: Harmony, Engagement, Usability (contrast), VisualHRV — **tất cả deterministic**.

- **Số nguồn:** 5+
- **Method:** chia rẽ. NLP branch dùng judge (và có correlation cao nhất: PPTEval Design Pearson 0.90). HCI branch (SlideAudit) cho thấy LLM detect flaw kém (F1 0.331–0.655). SlidesGen-Bench chuyển hẳn sang deterministic.
- **INFERENCE về mâu thuẫn này:** PPTEval Design correlation 0.90 đo **judge có xếp hạng giống người ở mức tổng thể không**; SlideAudit F1 0.65 đo **judge có chỉ đúng lỗi cụ thể nào không**. Hai câu hỏi khác nhau → không mâu thuẫn. Judge tốt cho ranking, kém cho diagnosis. Điều này ảnh hưởng trực tiếp tới việc dùng judge cho **development tracking** (cần diagnosis) so với **final report** (cần ranking).

### C8. Layout integrity / vi phạm hình học

> SlidesBench **Layout**: "Elements should be aligned, do not overlap, and have sufficient margins to each other. All elements should not exceed the page." — **chấm bằng GPT-4o**.
> SlideAudit **Composition & Layout**: Cluttered Layout, Occluded Content, Unbalanced Space/Margin, **Content Overflow**, Misaligned Elements, Lack of Visual Hierarchy.
> AeSlides: **Element Collision** (3 loại: bounding box chồng nhau, element tràn parent container, overflow vượt ranh giới slide), **Excessive Whitespace**, **Visual Imbalance**, **Distorted Aspect Ratio** — **100% programmatic**.
> PresentBench **Visual Design and Layout**: "absence of clutter or overlapping elements".

- **Số nguồn:** 4
- **Method: đây là chỗ chia rẽ rõ nhất trong toàn bộ landscape.** Cùng một hiện tượng (overlap, tràn, lệch), 3 nguồn dùng judge và 1 nguồn (AeSlides) chứng minh làm được bằng chương trình — và lập luận rằng "many fundamental aesthetic properties of slide layouts are inherently structured and can be precisely verified through programmatic analysis".
- **Xem §4 bên dưới.**

### C9. Artifact validity / executability

> PPTAgent **Success Rate**: "success requires the generation of all slides without execution errors after self-correction".
> AutoPresent **Executability** (§3.3): % program chạy được; **slide không execute được bị gán 0 trong 'Overall'**.
> Learning to Present **Render Quality** + **Code Rules**.

- **Số nguồn:** 3
- **Method:** deterministic 100%, ở mọi nguồn.
- **Ghi chú quan trọng:** AutoPresent Execution% dao động 2.1% → 89.2% giữa các model. **Nếu không report riêng, quality score sẽ vô nghĩa** (một model chỉ tạo được 2% slide có thể có điểm quality cao trên 2% đó).

### C10. Audience / scenario appropriateness

> X+Slides: toàn bộ premise. Gán "audience-specific utility weights to the same source-grounded probes". Ví dụ trong abstract: "specialists demand rigorous proofs, whereas decision-makers prioritize actionable conclusions".
> PresentBench **Presentation Fundamentals**: "scenario appropriateness".
> AI-Generated Slides study: **Pedagogical soundness** — cognitive load management, dual coding, scaffolding.
> Learning to Present **Inverse Specification**: "audience match".

- **Số nguồn:** 4
- **Method:** X+Slides = re-weight probe; các nguồn khác = judged criterion.
- **Ghi chú:** đây là dimension mới nổi (2026), và là dimension gần nhất với `slide_type` của Deck Agent.

### C11. Communicative effectiveness (extrinsic — đo bằng kết quả, không đo bằng hình thức)

> Paper2Poster **PaperQuiz**: sinh MCQ từ paper, cho VLM ở nhiều mức chuyên môn **chỉ đọc poster** rồi trả lời; poster nào giúp trả lời đúng nhất là hiệu quả nhất.
> SlidesGen-Bench **Content = QuizBank**: 10 câu/document (5 concept, 5 data), LLM trả lời **chỉ từ nội dung slide**.

- **Số nguồn:** 2 (độc lập, khác nhóm tác giả, khác artifact)
- **Method:** deterministic ở phần chấm (accuracy trên MCQ), model-dependent ở phần sinh câu hỏi và trả lời.
- **Ghi chú:** SlidesGen-Bench dùng quiz làm **content metric chính**, thay cho fidelity judged. Đây là một lựa chọn thiết kế đáng chú ý: nó biến "content có tốt không" thành "content có truyền đạt được không".

### C12. Editability / khả năng dùng tiếp của artifact

> SlidesGen-Bench **PEI (Presentation Editability Intelligence)**, 6 level, có knockout rule (fail level dưới → không tính level trên):
> L0 Static (ảnh phẳng) → L1 Patchwork (text tách rời) → L2 Vector → L3 Structural (master-based, vd. `<p:sldMaster>`, lan truyền layout) → L4 Parametric (table/chart có data thật) → L5 Cinematic.

- **Số nguồn:** 1 (chỉ SlidesGen-Bench)
- **Method:** human gating qua 5 test gate, **nhưng bản chất là thuộc tính cấu trúc của file** → có thể tự động hoá từ OOXML.
- **Ghi chú:** đây là dimension duy nhất trong landscape nói về "deck này có dùng tiếp được không" thay vì "deck này trông thế nào".

### C13. Reference similarity (so với deck gold do người làm)

> DOC2PPT: ROUGE-SL, mIoU.
> SlidesBench §3.1: Element matching, Content similarity, Color similarity, Position similarity.
> Paper2Poster **Visual Quality**: semantic alignment với poster do người làm.

- **Số nguồn:** 3
- **Trạng thái:** **đang bị bỏ dần.** PPTAgent §5.5 + Figure 7: PPL–Content Pearson −0.02, FID–Content −0.09; Table 3 cho thấy KCTV ROUGE-L cao nhất (16.76) nhưng Content thấp nhất (2.55). AutoPresent tự cảnh báo điểm ref-based thấp có thể do "differences in text, color, and positions, or derivative errors caused by the inaccurate element-matching process".
- **Kết luận từ evidence:** reference similarity **không phân biệt được "khác" với "kém"**.

---

## 3. Câu hỏi 3 — Dimension nào overlap nhau

**EXTERNAL EVIDENCE + INFERENCE.** Các overlap dưới đây là nguồn gây nhầm lẫn thật, không phải chuyện học thuật.

### O1. "Content" nghĩa hoàn toàn khác nhau giữa các nguồn — overlap nguy hiểm nhất

| Nguồn | "Content" thực sự đo gì | So với **nguồn tài liệu** không? |
| --- | --- | --- |
| PPTEval **Content** | text trên slide có súc tích, đúng ngữ pháp, có ảnh phù hợp không | **KHÔNG** |
| SlidesGen-Bench **Content** | người đọc slide có trả lời được quiz về document không | **CÓ**, gián tiếp |
| PresentBench **Content Fidelity** | có bịa gì không so với background material | **CÓ**, trực tiếp |
| Paper2Poster **Information Score** | Clarity + Completeness + Logical Flow | một phần |
| §8 **Content fidelity** | "đúng/đủ nội dung so với nguồn, không bịa" | **CÓ** |

**INFERENCE:** Nếu team đọc PPTEval rồi kết luận "PPTEval đã cover Content fidelity của §8" thì sai. **PPTEval Content không đo fidelity chút nào.** Đây là loại nhầm lẫn dễ xảy ra và tốn kém.

### O2. Completeness ↔ Correctness ↔ Fidelity — §8 gộp cả 3 thành một

PresentBench là nguồn duy nhất tách rõ, và cách tách của nó tương tự recall/precision:

| | Câu hỏi | Loại lỗi bắt được |
| --- | --- | --- |
| **Completeness** | ý quan trọng có đủ trên deck không? | thiếu ý (recall) |
| **Correctness** | ý được yêu cầu có được trình bày **đúng** không? | sai ý (accuracy) |
| **Fidelity** | có ý nào **không có trong nguồn** không? | bịa (precision) |

**INFERENCE:** §8 viết "Content fidelity (đúng/đủ nội dung so với nguồn, không bịa)" — một dòng ngoặc chứa cả 3. Một deck bỏ sót 40% ý quan trọng nhưng không bịa gì sẽ đạt điểm cao ở "không bịa" và thấp ở "đủ" — nếu gộp thành 1 điểm thì **tín hiệu triệt tiêu nhau**, và development tracking mất khả năng chẩn đoán.

### O3. Coherence ↔ Structure ↔ Presentation Fundamentals

- PPTEval **Coherence** đứng riêng ở presentation level.
- PresentBench nhét logical flow **vào trong** Presentation Fundamentals, cùng với conciseness, linguistic quality, title relevance, safety.
- Paper2Poster nhét **Logical Flow** vào Information Score, cùng Clarity và Completeness.

→ Cùng một thứ (mạch của deck), 3 nguồn đặt ở 3 chỗ khác nhau. **Không có consensus.** §8 "Structure (số slide, cách chia nội dung hợp lý với loại slide)" lại là thứ thứ tư nữa — nó trộn narrative flow + slide count + slide_type appropriateness.

### O4. Layout xuất hiện đồng thời ở dimension judged và dimension deterministic

- SlidesBench **Layout** (judged, GPT-4o): "do not overlap... should not exceed the page"
- AeSlides **Element Collision** (programmatic): "overlapping bounding boxes... overflow beyond slide boundaries"

→ **Cùng hiện tượng, cùng chữ, hai method.** Nếu framework có cả hai mà không nói rõ ranh giới, sẽ đo trùng và tốn tiền judge cho thứ tính được miễn phí.

### O5. Density ↔ Conciseness ↔ Efficiency ↔ Cognitive load

PPTEval "avoid too many texts" ≈ SlidesBench Text ≈ PresentBench conciseness ≈ X+Slides Efficiency ≈ pedagogical "cognitive load management". **Năm cách gọi cùng một thứ**, khác nhau ở chỗ X+Slides normalize theo attention cost còn các nguồn khác chấm cảm tính.

### O6. "Design consistency" ↔ aesthetic quality — không cùng một thứ

- **Consistency** (nhất quán font/màu/spacing giữa các slide) = SlideAudit "Inconsistent Text Styling", "Inconsistent Visual Style"; PresentBench "design uniformity (fonts, colors, and layout)".
- **Aesthetic quality** (đẹp/hài hoà/cuốn hút) = PPTEval Design, Paper2Poster Engagement, SlidesGen-Bench Harmony.

→ Một deck có thể **cực kỳ nhất quán mà xấu đều** (consistency cao, aesthetic thấp), hoặc **đẹp từng slide nhưng lệch nhau** (ngược lại). §8 gọi dimension là "Design consistency" nhưng giao cho MLLM-judge chấm — mà judge thì chấm cái thứ hai.

---

## 4. Câu hỏi 4 — Cái nào thực chất là **deterministic correctness**, không phải AI quality

Đây là câu hỏi RQ00 đã đặt (INFERENCE #2: §8 có thể gộp phần deterministic vào nhóm cần judge). **External evidence xác nhận INFERENCE này, và mạnh hơn tôi kỳ vọng.**

### 4.1 Bằng chứng rằng external work có tách 2 loại

| Nguồn | Tách? | Bằng chứng |
| --- | --- | --- |
| PPTAgent | **Có** | Success Rate là metric riêng, không nằm trong PPTEval |
| AutoPresent | **Có** | §3.3 Executability tách khỏi §3.1/§3.2; non-executing slide = 0 |
| Learning to Present | **Có, rất rõ** | Code Rules + Render Quality deterministic; Aesthetic HTML/Visual model-judged |
| SlidesGen-Bench | **Có, cực đoan** | toàn bộ Aesthetics chuyển sang deterministic, bỏ judge |
| AeSlides | **Có, cực đoan** | toàn bộ layout quality programmatic; bác bỏ VLM reward |
| PresentBench | **Không** | mọi thứ qua checklist judge, kể cả layout/legibility |
| SlidesBench ref-free | **Không** | Layout (overlap, out-of-page) vẫn do GPT-4o chấm |
| **§8** | **Không** | MLLM-judge cho cả 4 dimension |

**EXTERNAL EVIDENCE:** 5/8 nguồn tách. Xu hướng theo thời gian rõ ràng: các nguồn 2026 (SlidesGen-Bench 01/2026, AeSlides 04/2026) tách triệt để hơn các nguồn 2025.

### 4.2 Danh sách thứ thực sự deterministic (có precedent external)

| Thứ cần đo | Deterministic vì | Nguồn precedent | FR/NFR Deck Agent |
| --- | --- | --- | --- |
| Export/render thành công, file mở được | pass/fail của process | PPTAgent SR, AutoPresent Executability, Learning to Present Render Quality | FR-04, NFR-80–82 |
| Schema/IR hợp lệ | validate được | (không có precedent slide-gen; là engineering chuẩn) | FR-01, FR-05, NFR-21 |
| **Text overflow / tràn khỏi slide** | tính từ geometry | **AeSlides Element Collision** (overflow beyond slide boundaries) | **FR-22** |
| **Element overlap / che nhau** | bounding box intersection | **AeSlides Element Collision**; SlideAudit "Occluded Content" | **FR-22** |
| Lệch alignment, margin không đủ | toạ độ | AeSlides Visual Imbalance; SlideAudit "Misaligned Elements" | FR-22 |
| Whitespace quá nhiều/ít | variance map trên ảnh render | AeSlides Excessive Whitespace | — |
| Aspect ratio sai | so với 16:9 | AeSlides Distorted Aspect Ratio | NFR-80 |
| Contrast text/nền | luminance BT.709 | SlidesGen-Bench Usability Score | (chưa có FR/NFR) |
| Số slide đúng constraint | đếm | Learning to Present Code Rules ("section count adherence") | **FR-23** |
| Độ dài/word count theo constraint | đếm | Learning to Present Code Rules ("word count ratios") | FR-23 |
| Editability level của file xuất ra | cấu trúc OOXML | SlidesGen-Bench PEI L0–L5 | (implicit trong claim PPTX native) |
| `source_ref` có tồn tại & trỏ đúng | presence + resolve | **không có precedent external** | FR-03 (phần presence) |
| Design token conformance (`% element dùng đúng token`) | so với token registry | **không có precedent external** | NFR-52 |
| Vùng `locked_by_user` không bị đụng | state diff | không có precedent | FR-14a, FR-15–17 |

### 4.3 Điều khiến kết luận này mạnh hơn: judge làm phần deterministic **kém**

- **SlideAudit:** LLM detect design flaw F1 **0.331–0.655**, kể cả khi được cấp full taxonomy. "Content Overflow" nằm trong danh mục flaw đó.
- **AeSlides:** tác giả nói VLM "performed worse than random guessing on layout detection" `[chưa quote-verified — xem evidence.md]`.
- **PPTAgent Limitations:** chính hệ thống của họ vẫn tạo "overlapping elements, which can compromise the readability" — tức judge không chặn được lỗi này trong loop.

**INFERENCE:** Giao overflow/overlap cho MLLM-judge không chỉ **đắt hơn** cần thiết mà còn **kém chính xác hơn** một hàm hình học ~30 dòng. Với Deck Agent, chi phí này nhân lên vì evaluation phải chạy lặp lại hằng ngày (mission #1).

### 4.4 Cảnh báo ngược chiều — đừng deterministic hoá quá tay

**EXTERNAL EVIDENCE:** SlidesGen-Bench thay toàn bộ aesthetic judge bằng 4 công thức (hue template fit, opponent color channel, luminance contrast, subband entropy). Nhưng:
- 4 công thức đó đo **thuộc tính màu và độ lộn xộn**, không đo "layout này có phù hợp với loại nội dung này không" (FR-21).
- PPTEval Design đạt human correlation Pearson **0.90** — cao nhất trong mọi dimension. Tức judge **không** tệ ở design ở mức ranking.

**INFERENCE:** Ranh giới đúng không phải "deterministic tốt hơn judge" mà là:
- **Vi phạm rời rạc, định nghĩa được bằng hình học** (tràn, chồng, ngoài trang, sai tỉ lệ, sai token) → deterministic. Judge làm kém hơn.
- **Phán đoán liên tục, phụ thuộc ngữ cảnh** (layout này hợp với nội dung này không, mạch có tiến triển không) → judge/human. Không có công thức.

---

## 5. So sánh taxonomy: 3 cách tổ chức khác nhau đang tồn tại

**INFERENCE** — đây là cách tôi đọc landscape, không phải điều nguồn nào tự nói.

Các nguồn không chỉ khác về *số* dimension; chúng khác về **trục tổ chức**:

### Trục A — theo *khía cạnh cảm nhận* (PPTEval, Paper2Poster, §8)
"Nội dung / thiết kế / mạch" — tổ chức theo cái người xem cảm nhận được.
- **Ưu:** trực giác, dễ giải thích cho người không kỹ thuật, hợp báo cáo.
- **Nhược:** không nói gì về cách đo → dễ mặc định "cái gì cũng judge". Đây chính là chỗ §8 mắc.

### Trục B — theo *đối tượng so sánh* (PresentBench, X+Slides)
"So với background material / so với instruction / nội tại" — tổ chức theo **deck đang được so với cái gì**.
- **Ưu:** trục này **quyết định luôn method**. So với source → cần checklist trích từ source. So với instruction → cần instruction parse được. Nội tại → cần rubric hoặc công thức.
- **Nhược:** kém trực giác hơn khi trình bày.
- **Đây là trục của nguồn có human correlation cao nhất trong nhóm reference-free** (PresentBench Spearman 0.532 vs PPTEval 0.303).

### Trục C — theo *method* (Learning to Present, AeSlides, SlidesGen-Bench)
"Verifiable / model-judged" — tổ chức theo cách tính.
- **Ưu:** thẳng thắn về chi phí và độ tin cậy; hợp development tracking.
- **Nhược:** không phải taxonomy chất lượng — nó là taxonomy công cụ. Không trả lời "ta có quên đo gì không".

**INFERENCE quan trọng:** Trục A và Trục C **không thay thế nhau, chúng là 2 chiều của một ma trận**. §8 chọn trục A rồi gán cứng một method (MLLM-judge) cho toàn bộ — tức là **sập ma trận 2 chiều thành 1 chiều**. Taxonomy candidate nội bộ (Content/Planning/Presentation/Usability) cũng chọn trục A, nên **thừa hưởng đúng vấn đề đó**, dù nó phân rã chi tiết hơn.

---

## 6. Ba finding của landscape mà mọi thứ sau này nên dựa vào

1. **Không có taxonomy chuẩn.** 13 nguồn, 13 cách nhóm. Nhưng ở mức sub-aspect thì **13 concept lặp lại nhất quán** (§2). → Nên tổng hợp từ concept, không copy taxonomy của ai.

2. **Dimension ≠ method.** Mọi nguồn từ 2025 trở đi đều dùng nhiều method khác nhau cho các dimension khác nhau. §8 là ngoại lệ duy nhất trong bảng.

3. **Độ tin cậy của judge phụ thuộc mạnh vào dimension.** PPTEval: Design 0.90, Content 0.70, **Coherence 0.55**. PresentBench: tổng thể 0.532, dưới human agreement 0.664. SlideAudit: F1 0.331–0.655 khi phải chỉ ra lỗi cụ thể. → **Không được coi "MLLM-as-judge" là một khối đồng nhất.** Dimension mà Deck Agent claim giá trị nhất (structure, do ContentPlanner quyết định) đúng là dimension judge yếu nhất.
