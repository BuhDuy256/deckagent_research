# RQ01 — Recommendation: Evaluation Dimensions cho Deck Agent

Ngày research: **2026-08-14**. Đọc trước: `landscape.md` (câu 1–4), `evidence.md` (nguồn + mức verify).

Tài liệu này trả lời câu 5, 6, 7 của RQ01, rồi kết bằng 4 mục bắt buộc.

**Phạm vi:** RQ01 chỉ trả lời **WHAT** (đo cái gì). Không chọn metric/công thức (Wave 2: RQ02–RQ05). Không quyết định ngưỡng rigor (D-003). Không quyết định method (RQ06).

---

## 5. Câu hỏi 5 — Dimension nào applicable / not applicable với Deck Agent

Xét theo pipeline trong `SYSTEM_MAP.md`: `Input Adapters → Extractor → ContentPlanner → Deck IR → DesignSystem/Editor Loop → (Selection Resolver / Preview Renderer) → Exporters`.

### 5.1 Applicable trực tiếp

| Concept (từ `landscape.md`) | Vì sao hợp Deck Agent | Neo vào FR/NFR |
| --- | --- | --- |
| **C1 Source grounding / fidelity** | Deck Agent là document→deck, có nguồn xác định. Đây là claim trung tâm của sản phẩm. | FR-03, NFR-53, FR-18b |
| **C2 Coverage / completeness** | Cùng lý do; PDF nguồn có "ý quan trọng" xác định được. | FR-03, `EVALUATION_MISSION.md` (`critical_facts`, `expected_topics` trong benchmark metadata) |
| **C3 Brief compliance** | Deck Agent nhận `slide_type` + `target_slides` + ràng buộc thời gian. | **FR-02, FR-20, FR-23** |
| **C4 Narrative coherence** | Deck nhiều slide, có mạch. | FR-02, FR-07 |
| **C5 Decomposition / density** | ContentPlanner quyết định chia slide và phân bổ nội dung. | FR-02, FR-21 |
| **C8 Layout integrity** | FR-22 nói thẳng về tràn text. | **FR-22**, NFR-80–82 |
| **C9 Artifact validity** | Deck Agent xuất file thật, có exporter thứ 2. | FR-01, FR-04, FR-05, FR-19, NFR-21/50 |
| **C10 Audience/scenario appropriateness** | `slide_type = teaching \| catchup \| speaker_support` **chính là** audience conditioning. | **FR-02, FR-20** |
| **C12 Editability** | Deck Agent claim PPTX native + design token lan truyền + Editor Loop. | NFR-10–12, FR-10–13, FR-14–17 |

### 5.2 Applicable nhưng cần thích nghi rõ ràng

| Concept | Cần thích nghi thế nào | Rủi ro nếu bê nguyên |
| --- | --- | --- |
| **C7 Visual/aesthetic quality** | Deck Agent áp **một DesignSystem cố định** với design token. → Phần lớn "đẹp/hài hoà" là thuộc tính của **design system do người chọn**, không phải output của AI. Chỉ phần "AI có áp token đúng chỗ, chọn layout hợp nội dung không" mới là AI quality. | Đo aesthetic mỗi run sẽ cho ra một hằng số + nhiễu → **không phát hiện được regression của AI**, tốn tiền judge vô ích. Đây là điểm khác biệt lớn so với PPTAgent/AutoPresent (sinh design tự do). |
| **C11 Communicative effectiveness (quiz)** | Adapt được: Deck Agent có nguồn PDF → sinh MCQ từ nguồn, cho model chỉ đọc deck rồi trả lời. Đặc biệt hợp `slide_type=teaching` và `catchup`. | Tốn LLM call; kết quả phụ thuộc model trả lời quiz. Thuộc loại "final report" hơn là "daily". |
| **C6 Textual quality** | Deck Agent song ngữ Anh–Việt ở roadmap bước 9 → perplexity-based metric (Paper2Poster) không chuyển sang tiếng Việt an toàn. | Dùng PPL sẽ vô nghĩa; **PPTAgent đã chứng minh PPL gần như không tương quan với content quality (Pearson −0.02)**. |
| **UniPPTBench 2-tầng** | Mô hình "shared metric + input-type-specific metric" hợp FR-18 (mở rộng Word/web/YouTube, phải chứng minh fidelity vẫn giữ). | Nếu chỉ có shared metric, không phát hiện được adapter mới làm hỏng fidelity. |

### 5.3 NOT applicable (hoặc applicable rất yếu)

| Concept | Vì sao không | Evidence |
| --- | --- | --- |
| **C13 Reference similarity** (ROUGE-SL, mIoU, element/position/color match, FID) | (a) Deck Agent **không có gold deck do người làm** cho mỗi PDF nguồn — và tạo ra chúng là một dự án riêng. (b) Ngay cả khi có, metric này không phân biệt "khác" với "kém". | **EXTERNAL EVIDENCE:** PPTAgent Table 3 — KCTV ROUGE-L cao nhất (16.76) nhưng Content thấp nhất (2.55); Figure 7 — PPL/FID vs Content/Design correlation ≈ 0. AutoPresent §3.1 tự cảnh báo lỗi do element-matching. |
| **SlideAudit dim 5 — Animation & Interaction** | NFR-82: animation tắt mặc định. | PROJECT SOURCE (NFR-82) |
| **Paper2Poster Visual Quality** (semantic alignment với poster người làm) | Poster = 1 canvas; deck = chuỗi slide. Metric không chuyển được về mặt hình học, và lại là reference-based. | — |
| **SlidesBench "Image" criterion** (chất lượng ảnh sinh ra) | P0 của Deck Agent dùng ảnh **từ nguồn**, không sinh ảnh. AI sinh ảnh nằm ở roadmap bước 9. | PROJECT SOURCE (architecture §5 roadmap) |
| **SlidesGen-Bench Aesthetics 4 công thức màu** | Cùng lý do 5.2 — Deck Agent dùng DesignSystem cố định, các chỉ số hoà sắc gần như không đổi giữa các version. | INFERENCE |

### 5.4 Điểm quan trọng nhất của câu 5 — external work **không** evaluate planner

**EXTERNAL EVIDENCE (negative finding, xem `evidence.md` §"Những gì KHÔNG tìm được" #1):** Không tìm thấy work nào chấm điểm trực tiếp một intermediate plan / outline / IR như một evaluation target. PPTAgent có ablation `w/o Outline` và `w/o Structure`, nhưng đo **tác động lên điểm deck cuối** (Coherence 4.48 → 3.36 / 3.45), không chấm chất lượng outline.

**INFERENCE:** Điều này chạm trực tiếp vào 2 giả thuyết nội bộ:
- **Q-002** ("ContentPlanner xứng đáng effort evaluation lớn nhất"): external practice cho thấy cách chuẩn để chứng minh giá trị của một component là **ablation trên end-deck score**, không phải dựng một bộ metric riêng chấm component đó. Điều này **không bác bỏ** Q-002, nhưng nó gợi ý rằng effort nên đổ vào **end-deck dimension đủ nhạy để ablation nhìn thấy được**, chứ không phải vào việc phát minh "ContentPlanner quality metric".
- **FR-07** (plan/outline kiểm tra được trước khi generate): giá trị của artifact plan theo external evidence là **debuggability/controllability**, không phải một dimension chấm điểm.

---

## 6. Câu hỏi 6 — §8 thiếu gì, thừa gì, trộn sai abstraction level ở đâu

**PROJECT SOURCE** — §8 (`kien-truc-do-an.pdf`, trang 6), nguyên văn:

> Phương pháp: kết hợp MLLM-as-judge (theo hướng PresentBench - checklist chi tiết riêng cho từng input, judge chấm từng mục kèm bằng chứng) với human annotation trên mẫu con để kiểm tra độ tin cậy (đo tương quan điểm judge vs người).
> Các khía cạnh chấm:
> - Content fidelity (đúng/đủ nội dung so với nguồn, không bịa)
> - Ready-to-use (không lỗi layout, chữ tràn, hình vỡ - khớp yêu cầu "1 prompt dùng được luôn")
> - Design consistency (tuân theo design token đã lock)
> - Structure (số slide, cách chia nội dung hợp lý với loại slide)

### 6.0 Điều §8 làm ĐÚNG (ghi trước để không đọc lệch phần sau)

**EXTERNAL EVIDENCE:** §8 trỏ tới **PresentBench**, và PresentBench **là entity có thật, verify được ở mức E1** (arXiv 2603.07244, repo Apache-2.0, dataset HF — xem `evidence.md` #2). Không những thế, mô tả của §8 ("checklist chi tiết riêng cho từng input, judge chấm từng mục kèm bằng chứng") **khớp chính xác** với method thật của PresentBench: 54.1 binary checklist item/instance, judge chấm từng item trong call riêng, kèm localized evidence.

§8 cũng đúng khi yêu cầu human annotation trên mẫu con để đo tương quan, và khi tự nhắc self-preference bias.

→ **Vấn đề của §8 không phải là nó trỏ sai nguồn. Vấn đề là nó lấy method của PresentBench nhưng KHÔNG lấy taxonomy của PresentBench** — trong khi taxonomy mới là thứ RQ01 cần.

### 6.1 THIẾU — dimension có ở ≥2 external source nhưng §8 không có

| Thiếu | Evidence external | Neo vào Deck Agent |
| --- | --- | --- |
| **Tách Completeness ra khỏi Fidelity** | PresentBench tách 3 chiều (Completeness / Correctness / Fidelity); Paper2Poster tách Content Completeness; X+Slides tách Coverage khỏi Correctness | FR-03; benchmark metadata đã có sẵn `expected_topics` **và** `critical_facts` — tức chính team đã ngầm phân biệt 2 thứ |
| **Brief compliance** (deck có làm đúng thứ được yêu cầu không) | PresentBench Content Correctness (instruction-derived); Learning to Present Inverse Specification (audience match, slide count accuracy); X+Slides toàn bộ | **FR-02** ("đổi slide_type → cấu trúc khác **có chủ đích**"), **FR-20** ("khác biệt **đo được**"), **FR-23** (ràng buộc thời gian/độ dài) |
| **Artifact validity như một số được report riêng** | PPTAgent Success Rate; AutoPresent Executability (**non-executing slide = 0 điểm Overall**); Learning to Present Render Quality | FR-04, FR-19, NFR-21/50/80–82 |
| **Editability level của file xuất ra** | SlidesGen-Bench PEI L0–L5 (L3 = master-based, lan truyền layout — đúng thứ NFR-10–12 mô tả) | Claim "PPTX native", FR-10–13, FR-14–17 |
| **Communicative effectiveness** | Paper2Poster PaperQuiz; SlidesGen-Bench QuizBank (2 nhóm độc lập, 2 artifact khác nhau) | `slide_type=teaching\|catchup`; NFR-53 |
| **Editor Loop behaviour** | không có precedent external mạnh (MemSlides có multi-turn revision nhưng chưa đọc) | FR-14–17 — **RQ00 đã ghi nhận §8 hoàn toàn không nhắc Editor Loop** |
| Operational (latency/cost/reliability) | — | **Đã được xử lý ở D-005**, không lặp lại ở đây |

### 6.2 THỪA / sai vị trí

**"Design consistency" không nên là 1 trong 4 top-level AI-quality dimension.**

`problem → evidence → reasoning`:
- **PROJECT SOURCE:** §8 định nghĩa nó là "tuân theo design token đã lock" — tức là **conformance**, không phải aesthetic. NFR-52 cũng tự đề xuất candidate `% element dùng đúng token` — một tỉ lệ đếm được.
- **EXTERNAL EVIDENCE:** không tìm được metric external nào cho design-token conformance (`evidence.md` negative finding #2). Cái external work gọi là "Design" (PPTEval) hay "Visual Design and Layout" (PresentBench) là **aesthetic quality**, một thứ khác — xem overlap O6 trong `landscape.md`.
- **INFERENCE:** §8 đặt tên theo conformance nhưng giao cho MLLM-judge — mà judge chấm aesthetic. Hai thứ không trùng nhau: một deck có thể **nhất quán mà xấu đều**, hoặc **đẹp từng slide mà lệch nhau**. Thêm nữa, vì Deck Agent áp một DesignSystem cố định, aesthetic gần như là hằng số giữa các version → **giá trị chẩn đoán cho development tracking rất thấp**.

→ Phần conformance thuộc lớp deterministic. Phần aesthetic tồn tại nhưng không xứng vị trí 1/4.

**"Structure" không thừa, nhưng đang gánh 3 việc** — xem 6.3.

### 6.3 TRỘN SAI ABSTRACTION LEVEL — đây là vấn đề lớn nhất

#### (a) §8 gán **một method duy nhất** cho **mọi dimension**

**EXTERNAL EVIDENCE:** trong bảng 13 nguồn ở `landscape.md` §1, **§8 là nguồn duy nhất** dùng MLLM-judge cho toàn bộ dimension. 5/8 nguồn có tách deterministic ra, và xu hướng 2026 (SlidesGen-Bench 01/2026, AeSlides 04/2026) là tách triệt để hơn.

**INFERENCE:** §8 đang sập một ma trận 2 chiều (`dimension × method`) thành 1 chiều. Hệ quả cụ thể: mọi thứ đo được miễn phí bằng hàm hình học đều bị đẩy vào đường ống đắt nhất, chậm nhất, và (theo evidence dưới) kém chính xác nhất.

#### (b) "Ready-to-use" gộp 3 abstraction level khác nhau

§8: *"không lỗi layout, chữ tràn, hình vỡ - khớp yêu cầu '1 prompt dùng được luôn'"*.

| Thành phần | Abstraction level thật | Evidence |
| --- | --- | --- |
| chữ tràn, overlap, hình vỡ | **deterministic geometry** | **AeSlides Element Collision** — 3 loại: bounding box chồng nhau, tràn parent container, **overflow vượt ranh giới slide**; toàn bộ programmatic. SlideAudit liệt kê "Content Overflow" là một flaw category. |
| file có sửa tiếp được không | **cấu trúc artifact** | SlidesGen-Bench PEI L0–L5 |
| "1 prompt dùng được luôn" | **human judgement về edit effort** | Không có metric external nào lượng hoá được (`evidence.md` negative finding #3) |

**INFERENCE:** Đây xác nhận **RQ00 INFERENCE #2** — và external evidence còn mạnh hơn kỳ vọng: không chỉ là "judge tốn kém hơn cần thiết", mà **judge làm việc này kém hơn**:
- SlideAudit: LLM detect design flaw **F1 0.331–0.655** kể cả khi được cấp full taxonomy.
- AeSlides: tác giả nói VLM "performed worse than random guessing on layout detection" `[chưa quote-verified]`.
- PPTAgent Limitations: chính hệ thống của họ vẫn tạo "overlapping elements" — judge trong loop không chặn được.

#### (c) "Structure" gộp 3 thứ thuộc 3 reference frame khác nhau

§8: *"số slide, cách chia nội dung hợp lý với loại slide"*.

| Thành phần | So với cái gì | Method phù hợp |
| --- | --- | --- |
| **số slide** đúng constraint | so với **brief** (`target_slides`) | deterministic (đếm) — Learning to Present "Code Rules: section count adherence" |
| **cách chia nội dung hợp lý** | so với **chuẩn trình bày** (nội tại) | judged — PPTEval Coherence, C5 density |
| **hợp với loại slide** (`slide_type`) | so với **brief** | judged + differentiability test — X+Slides, Inverse Specification |

**INFERENCE:** ba thứ này không thể cho ra một điểm số có nghĩa. Và điều đáng lo hơn: phần "cách chia nội dung hợp lý" ánh xạ gần nhất sang **PPTEval Coherence** — dimension có **human correlation thấp nhất** (Pearson 0.55 / Spearman 0.57, so với Design 0.90 / 0.88). Tức **dimension mà Deck Agent claim giá trị nhiều nhất (ContentPlanner quyết định structure) chính là dimension MLLM-judge yếu nhất.**

#### (d) "Content fidelity" gộp precision + recall

§8: *"đúng/đủ nội dung so với nguồn, không bịa"* — một dòng ngoặc chứa **3 loại lỗi khác nhau**: thiếu ý (recall), sai ý (accuracy), bịa ý (precision). PresentBench tách đúng 3 cái này thành 3 category riêng.

**INFERENCE:** gộp lại thì tín hiệu triệt tiêu nhau. Một deck bỏ sót 40% ý quan trọng nhưng không bịa gì sẽ cho ra một điểm trung tính — development tracking mất khả năng chẩn đoán chính thứ nó cần chẩn đoán.

### 6.4 Đối chiếu trung lập với taxonomy candidate nội bộ

**PROJECT SOURCE** (`00_context/TERMINOLOGY.md`, working hypothesis): `Content Quality / Planning Quality / Presentation Quality / Usability–Ready-to-use`.

| Nhánh | External support | Nhận xét trung lập |
| --- | --- | --- |
| **Content Quality** (fidelity, coverage, hallucination) | **Mạnh** — PresentBench, X+Slides, AI-slides study | Đúng hướng. Nhưng vẫn gộp 3 thứ vào 1 nhánh giống §8; PresentBench tách 3. |
| **Planning Quality** (decomposition, allocation, slide_type appropriateness) | **Yếu / không có** | Hai vấn đề: (1) **không external work nào chấm planner như một evaluation target** — họ dùng ablation trên end-deck score. (2) Ba sub-item bên trong thuộc **2 reference frame khác nhau**: decomposition/allocation so với chuẩn trình bày (nội tại), còn `slide_type` appropriateness so với **brief**. → "Planning Quality" đang lấy **tên một component của kiến trúc** làm **tên một dimension chất lượng** — cùng loại lỗi abstraction mà nó đang cố sửa ở §8. |
| **Presentation Quality** (readability, density, layout appropriateness, visual coherence) | **Mạnh** | Đúng hướng, nhưng **phần geometry (tràn/overlap) phải được kéo ra khỏi đây** sang lớp deterministic. |
| **Usability / Ready-to-use** (edit effort, readiness) | **Yếu** | Không có metric external nào lượng hoá edit effort. Và nhánh này vẫn gộp 3 level như §8 (geometry / editability / human effort). |

**INFERENCE:** Taxonomy candidate nội bộ **cải thiện §8** ở chỗ nó phân rã chi tiết hơn và nêu được hallucination/coverage riêng. Nhưng nó **giữ nguyên 2 lỗi gốc**: (i) vẫn tổ chức theo trục "khía cạnh cảm nhận" nên không nói gì về method → lớp deterministic vẫn vô hình; (ii) thêm một lỗi mới là dùng tên component (`Planning`) làm tên dimension. → **Không nên adopt nguyên trạng, cũng không nên bác bỏ** — nên tái tổ chức trên một trục khác.

---

## 7. Câu hỏi 7 — Proposed taxonomy cho Deck Agent

### 7.1 Nguyên tắc tổ chức được chọn, và vì sao

**Đề xuất tổ chức theo trục "deck đang được so với cái gì" (reference frame), không theo trục "khía cạnh cảm nhận".**

`problem → evidence → reasoning`:
- **Problem:** cả §8 lẫn taxonomy candidate nội bộ đều tổ chức theo khía cạnh cảm nhận (content/design/structure/usability), và cả hai đều bị trộn abstraction level ở cùng những chỗ (§6.3).
- **Evidence:** trong `landscape.md` §5, ba trục tổ chức đang tồn tại. Trục **reference frame** (PresentBench, X+Slides) là trục của nguồn có **human correlation cao nhất trong nhóm reference-free** (PresentBench Spearman 0.532 vs PPTEval 0.303 vs MLLM-judge ranking 0.258).
- **Reasoning:** reference frame **quyết định luôn method** — nếu so với source thì cần checklist trích từ source; nếu so với brief thì brief phải parse được; nếu là thuộc tính nội tại của file thì tính bằng hình học. Trục cảm nhận không có tính chất đó, nên nó mời gọi việc gán một method cho tất cả — đúng cái bẫy §8 rơi vào.

### 7.2 RECOMMENDATION — taxonomy đề xuất (5 lớp quality + 1 lớp operational đã có)

> Đây là **đề xuất**, không phải quyết định. Tên lớp có thể đổi; điều quan trọng là **ranh giới**, không phải nhãn.

```
DECK AGENT — EVALUATION DIMENSIONS (đề xuất RQ01)

L0. ARTIFACT INTEGRITY            [so với: chính nó — quy tắc kiểm được]
    L0.1 Validity        — export/render thành công, schema Deck IR hợp lệ,
                           cross-format consistency (PPTX vs Marp/HTML)
    L0.2 Geometry        — tràn text, overlap/che nhau, ngoài trang,
                           margin/alignment, aspect ratio
    L0.3 Token conformance — % element dùng đúng design token; vùng lock nguyên vẹn
    L0.4 Editability     — mức editability của file xuất ra (PEI-style)
    → Là ĐIỀU KIỆN CỔNG: deck fail L0.1 thì điểm L1–L4 không có nghĩa.

L1. SOURCE GROUNDING              [so với: tài liệu nguồn]
    L1.1 Fidelity        — không có chi tiết nào ngoài nguồn (chống bịa)
    L1.2 Factual correctness — điều đã nói thì nói đúng
    L1.3 Traceability    — source_ref tồn tại và trỏ đúng   ← phần này deterministic

L2. INFORMATION SELECTION         [so với: tài liệu nguồn]
    L2.1 Coverage        — critical_facts / expected_topics có mặt
    L2.2 Salience        — ý quan trọng được ưu tiên hơn ý phụ
    L2.3 Non-redundancy  — không lặp ý giữa các slide

L3. BRIEF COMPLIANCE              [so với: yêu cầu của user]
    L3.1 Hard constraints — số slide, độ dài/thời gian     ← deterministic
    L3.2 slide_type conditioning — cùng nguồn, khác slide_type
                                   → khác biệt ĐO ĐƯỢC và ĐÚNG HƯỚNG
    L3.3 Instruction following — Editor Loop hiểu đúng ý lệnh tự nhiên (FR-14b)

L4. PRESENTATION QUALITY          [so với: chuẩn trình bày — nội tại]
    L4.1 Narrative coherence — mạch deck tiến triển hợp lý
    L4.2 Decomposition & density — chia slide hợp lý, mật độ chữ vừa phải
    L4.3 Layout appropriateness — layout được chọn hợp loại nội dung (FR-21)
    L4.4 Aesthetic quality — phần thẩm mỹ còn lại
                             (LOW PRIORITY: phần lớn do DesignSystem quyết định,
                              không phải do AI → ít giá trị chẩn đoán)

L5. OUTCOME / USABILITY           [so với: người dùng & người xem]
    L5.1 Comprehension transfer — người/model chỉ đọc deck có nắm được ý không
    L5.2 Edit effort — cần sửa bao nhiêu để dùng được thật
    → Đắt, chậm. Ứng viên cho evidence bảo vệ, không cho daily tracking.

L6. OPERATIONAL — latency / cost / reliability
    → ĐÃ ĐƯỢC QUYẾT ĐỊNH ở D-005. Không thuộc phạm vi RQ01, ghi ở đây
      chỉ để bản đồ đầy đủ.
```

### 7.3 Trace: từng lớp neo vào đâu

| Lớp | External evidence chính | FR/NFR | Thay thế phần nào của §8 |
| --- | --- | --- | --- |
| **L0.1** | PPTAgent Success Rate; AutoPresent Executability (non-exec = 0) | FR-01/04/05/19, NFR-21/50/80–82 | §8 **không có** |
| **L0.2** | **AeSlides** (4 metric programmatic); SlideAudit "Content Overflow" | **FR-22** | Kéo ra khỏi §8 "Ready-to-use" |
| **L0.3** | không có precedent external | **NFR-52**, FR-10–13, FR-14a/15–17 | Thay §8 "Design consistency" |
| **L0.4** | SlidesGen-Bench **PEI L0–L5** | claim PPTX native, NFR-10–12 | §8 **không có** |
| **L1** | **PresentBench Content Fidelity**; X+Slides Correctness | FR-03, NFR-53, FR-18b | Phần "không bịa" của §8 Content fidelity |
| **L2** | **PresentBench Content Completeness**; X+Slides Coverage; DocPres Coverage | FR-03, benchmark `critical_facts` | Phần "đủ" của §8 Content fidelity — hiện bị gộp |
| **L3.1** | Learning to Present Code Rules | **FR-23** | Kéo ra khỏi §8 "Structure" |
| **L3.2** | **X+Slides** (audience-conditioned); Learning to Present Inverse Specification | **FR-02, FR-20** | §8 chỉ chạm gián tiếp qua "Structure" |
| **L3.3** | không có precedent mạnh (MemSlides — chưa đọc) | **FR-14b** | §8 **không có** (RQ00 đã ghi nhận) |
| **L4.1** | PPTEval Coherence; PresentBench Presentation Fundamentals | FR-02, FR-07 | Phần "chia nội dung hợp lý" của §8 Structure |
| **L4.2** | PPTEval Content (conciseness); X+Slides Efficiency | FR-02, FR-21 | — |
| **L4.3** | SlidesBench Layout (phần judged) | **FR-21** | — |
| **L4.4** | PPTEval Design (Pearson 0.90); Paper2Poster Aesthetic Score | — | Phần aesthetic của §8 Design consistency |
| **L5.1** | **Paper2Poster PaperQuiz**; **SlidesGen-Bench QuizBank** | `slide_type=teaching\|catchup` | §8 **không có** |
| **L5.2** | không có metric external | §8 "1 prompt dùng được luôn" | Phần human của §8 Ready-to-use |

### 7.4 Ba tính chất của taxonomy này mà §8 và candidate nội bộ đều không có

1. **L0 là điều kiện cổng, không phải một dimension ngang hàng.** Precedent: AutoPresent gán 0 cho slide không execute được; PPTAgent report SR tách riêng. **INFERENCE:** nếu Deck Agent report "fidelity 84" trong khi 30% deck không mở được, con số 84 là sai lệch.

2. **Mỗi lớp có reference frame khác nhau → method khác nhau là hệ quả tự nhiên, không phải ngoại lệ.** Điều này để ngỏ đúng chỗ cho RQ06 (chọn method), thay vì khoá cứng như §8.

3. **Không có lớp nào mang tên một component của kiến trúc.** `ContentPlanner` không xuất hiện. **INFERENCE:** đóng góp của ContentPlanner được đo bằng **ablation trên L2/L3.2/L4.1** — đúng cách external work làm (PPTAgent ablation `w/o Outline` / `w/o Structure` đo trên điểm deck cuối). Điều này cũng để ngỏ Q-002 thay vì bake giả thuyết vào taxonomy.

---

# 1. Recommendation

**RECOMMENDATION.** Đề xuất Deck Agent **không giữ 4 dimension phẳng của §8**, cũng **không adopt nguyên trạng taxonomy candidate nội bộ** (Content/Planning/Presentation/Usability), mà tái tổ chức thành **5 lớp quality được phân theo reference frame + 1 lớp operational đã quyết định ở D-005**, như §7.2:

**L0 Artifact Integrity** (gate, deterministic) · **L1 Source Grounding** · **L2 Information Selection** · **L3 Brief Compliance** · **L4 Presentation Quality** · **L5 Outcome/Usability** · *(L6 Operational — đã có ở D-005)*.

Trace đầy đủ:

**Problem.** §8 đặt 4 dimension phẳng và gán MLLM-as-judge cho cả 4. RQ00 đã nghi ngờ (INFERENCE #2) rằng phần deterministic-checkable (FR-22 overflow) đang bị gộp vào nhóm cần judge, và chưa validate được.

**Evidence.**
- Trong 13 nguồn khảo sát (`landscape.md` §1), **§8 là nguồn duy nhất dùng một method cho mọi dimension**; 5/8 nguồn có tách deterministic, và xu hướng 2026 tách triệt để hơn (SlidesGen-Bench, AeSlides).
- **AeSlides** (E1) chứng minh overflow/overlap/margin/aspect-ratio tính được hoàn toàn bằng chương trình, và lập luận rằng "many fundamental aesthetic properties of slide layouts are inherently structured and can be precisely verified through programmatic analysis".
- **SlideAudit** (E1, UIST '25) đo được rằng LLM phát hiện design flaw ở mức **F1 0.331–0.655** kể cả khi được cấp full taxonomy.
- **PPTEval** (E1) cho thấy độ tin cậy của judge **không đồng đều theo dimension**: Design Pearson 0.90 nhưng **Coherence 0.55**.
- **PresentBench** (E1) — chính nguồn §8 trích dẫn — **tách 3 khái niệm content** (Completeness / Correctness / Fidelity) mà §8 gộp thành một dòng.
- **X+Slides** (E1) và **Learning to Present** (E1) cho thấy "deck có tuân thủ brief/audience không" là một dimension riêng, đo được — §8 không có.
- **Negative finding:** không tìm được external work nào chấm điểm trực tiếp một planner/outline như evaluation target.

**Reasoning.**
1. Nếu độ tin cậy của judge phụ thuộc dimension, và nếu một phần dimension đo được chính xác hơn bằng hình học, thì **dimension và method không thể bị buộc chặt** — phải tách thành 2 chiều. §8 buộc chặt → phải sửa.
2. Trục tổ chức tốt nhất là trục **dự đoán được method**. "Deck đang so với cái gì" làm được điều đó; "khía cạnh cảm nhận" thì không. Đó là lý do chọn reference frame.
3. Ba chỗ §8 trộn level (Ready-to-use / Structure / Content fidelity) đều là chỗ **nhiều reference frame bị gộp vào một tên**. Tách theo reference frame giải quyết cả ba bằng một nguyên tắc, không phải ba patch riêng.
4. Không đặt tên lớp theo component kiến trúc, để không bake sẵn giả thuyết Q-002 vào chính hệ đo dùng để kiểm tra Q-002.

**Recommendation.** Như §7.2. Wave 2 (RQ02–RQ05) nhận bàn giao theo lớp: L1+L2 → RQ02; L4 → RQ03; L0.2+L0.3+L4.4 → RQ04; L0.4+L5 → RQ05. **L3 hiện chưa có RQ nào cover** — xem mục 4 bên dưới.

---

# 2. Confidence level

**Tổng thể: MEDIUM-HIGH cho các thành phần; MEDIUM cho cách nhóm.**

### Nơi evidence MẠNH (confidence cao)

| Kết luận | Vì sao mạnh |
| --- | --- |
| Fidelity / Coverage / Correctness là 3 thứ khác nhau, không nên gộp | 3 nguồn E1 độc lập tách rõ (PresentBench, X+Slides, Paper2Poster); PresentBench tách đúng 3 |
| Phần geometry của "Ready-to-use" là deterministic, không cần judge | AeSlides (E1) làm hoàn toàn bằng chương trình + SlideAudit (E1) đo được LLM kém ở việc này (F1 ≤0.655) + PPTAgent tự thừa nhận overlap vẫn lọt |
| Artifact validity phải report riêng và gate quality score | 3 nguồn E1 làm đúng vậy; AutoPresent gán 0 explicit; Execution% dao động 2.1%–89.2% |
| Reference-based similarity không dùng được | PPTAgent Table 3 + Figure 7 (số cụ thể, đọc trực tiếp) + AutoPresent tự cảnh báo |
| Judge không đồng đều theo dimension; structure/coherence là chỗ yếu nhất | PPTEval Table 5 (đọc trực tiếp): Coherence 0.55 vs Design 0.90 |
| "PresentBench" là entity thật, E1 | arXiv + repo Apache-2.0 + HF dataset + homepage, đều verify được |

### Nơi evidence YẾU (confidence thấp — cần đánh dấu rõ khi dùng)

| Kết luận | Vì sao yếu |
| --- | --- |
| **Cách nhóm 5 lớp theo reference frame** | **Đây là INFERENCE/tổng hợp của tôi, không nguồn nào phát biểu taxonomy này.** PresentBench gần nhất nhưng không nói ra nguyên tắc tổ chức. → Đây là chỗ dễ bị challenge nhất và **nên bị challenge**. |
| **L5.2 Edit effort** | Không tìm được **bất kỳ** metric external nào lượng hoá. Nguồn gần nhất (arXiv 2605.13532) chỉ mô tả định tính ("little effort"). Literature post-editing effort chỉ có ở MT/summarization. |
| **L0.3 Token conformance** | Không có precedent external nào. Chỉ có NFR-52 (PROJECT SOURCE). Có thể là đóng góp riêng — nhưng cũng nghĩa là không có baseline. |
| **L1.3 Traceability (`source_ref`)** | Không có precedent external. PresentBench có "localized evidence" nhưng đó là evidence của **judge**, không phải của **deck**. |
| **L3.3 Editor instruction-following** | Không tìm được precedent mạnh; MemSlides có multi-turn revision nhưng **chưa đọc**. |
| **Kết luận "aesthetic ít giá trị chẩn đoán vì DesignSystem cố định"** | INFERENCE thuần từ kiến trúc, **không có evidence external** — vì mọi hệ thống được khảo sát đều sinh design tự do. Cần validate bằng thực nghiệm nội bộ. |
| Claim "VLM worse than random on layout detection" (AeSlides) | Mới đọc qua tool summary, **chưa đọc bảng số gốc**. Không dùng làm quote trong báo cáo trước khi verify. |
| Breakdown 6 tiêu chí của Paper2Poster | Lấy qua search snippet trích paper, **chưa đọc trong bản gốc** (PDF NeurIPS vượt giới hạn fetch, arXiv HTML v3 404). |

### Rủi ro về độ mới của nguồn

**INFERENCE:** 7/13 nguồn chính có ngày 2026 (SlidesGen-Bench 01, PresentBench 03, Learning to Present 03, AeSlides 04, AI-slides study 05, UniPPTBench 05, X+Slides 06). Chúng đã verify được là **tồn tại** (arXiv abstract page, repo, dataset), nhưng **chưa có thời gian được cộng đồng kiểm chứng/replicate**. Với 4 nguồn tôi chỉ đọc abstract (X+Slides, UniPPTBench, SlidesGen-Bench, Learning to Present), độ sâu verification thấp hơn hẳn 4 nguồn tôi đọc PDF trực tiếp (PPTAgent, AutoPresent) — **không nên treo một major decision chỉ lên nhóm đầu**.

---

# 3. Unresolved questions

1. **Trục reference frame có thực sự tốt hơn trục cảm nhận cho bối cảnh đồ án không?** Trục cảm nhận dễ trình bày trong báo cáo/bảo vệ hơn. Có thể cần **2 view trên cùng một tập dimension** (view kỹ thuật theo reference frame, view báo cáo theo cảm nhận) — nhưng như vậy có làm phức tạp thêm không?

2. **Bao nhiêu lớp là quá nhiều cho một đồ án?** 5 lớp quality × nhiều sub-dimension là nhiều hơn §8 rất nhiều. Cắt xuống thì cắt ở đâu? (L5 là ứng viên rõ nhất để hoãn.)

3. **`slide_type` differentiation (L3.2) đo bằng cách nào?** Ba hướng ứng viên xuất hiện trong research nhưng RQ01 không được chọn: (a) inverse specification — recover `slide_type` từ deck; (b) audience-weighted probe kiểu X+Slides; (c) checklist riêng cho từng `slide_type` (FR-20 tự nói "có checklist eval"). → **Route sang Wave 2.**

4. **Có đo được aesthetic một cách có ý nghĩa khi DesignSystem cố định không, hay nên bỏ hẳn L4.4?** Cần một thực nghiệm nội bộ nhỏ: chạy 2 version Deck Agent, xem điểm aesthetic có dịch chuyển không.

5. **Deck Agent có thể có gold reference deck không?** Quyết định này (thuộc RQ07) xác định liệu nhánh reference-based có tồn tại như một lựa chọn hay bị loại hoàn toàn. Hiện RQ01 giả định là **không có**.

6. **L5.2 Edit effort đo thế nào khi không có precedent?** Đề xuất khả dĩ nhưng chưa research: đếm thao tác trong Editor Loop cho tới khi user dừng. Nhưng điều đó lại phụ thuộc Editor Loop có telemetry — **chưa verify là có**.

7. **Có nên đánh giá plan/outline (FR-07) riêng không?** External work nói không (đo bằng ablation). Nhưng Deck Agent có FR riêng cho artifact plan. Chưa giải quyết.

8. **Chưa verify:** breakdown 6 tiêu chí Paper2Poster; số liệu gốc của claim "VLM worse than random" trong AeSlides; danh sách tác giả X+Slides; UniPPTBench có tách deterministic/judge không.

9. **Chưa đọc, có thể đổi kết luận:** MemSlides (multi-turn local revision → L3.3), OutlineSpark (outline-driven → FR-07), EfficientPosterGen (violation detection → L0.2), SlideTailor (personalization → L3.2). Xem `evidence.md` mục DISCOVERY ONLY.

---

# 4. Proposed decisions cần human/team review

> Tất cả dưới đây là **ĐỀ XUẤT**, chưa quyết định. Không ghi vào `DECISION_LOG.md`, không sửa `OPEN_QUESTIONS.md` (3 session đang chạy song song). Người điều phối quyết định có promote hay không.

**P-01 — Tổ chức dimension theo reference frame, tách `dimension` khỏi `method`.**
Đề xuất adopt taxonomy §7.2 làm khung. Ảnh hưởng: `06_design/EVALUATION_FRAMEWORK.md`, phân công RQ02–RQ05.
*Đây là đề xuất có confidence thấp nhất trong nhóm — nên được challenge trước khi accept.*

**P-02 — Tách §8 "Content fidelity" thành L1 (Grounding) + L2 (Selection).**
Evidence: PresentBench tách 3; benchmark metadata của chính team đã có `expected_topics` và `critical_facts` riêng. Confidence: cao.

**P-03 — Tách §8 "Ready-to-use" thành 3: L0.2 geometry (deterministic) / L0.4 editability / L5.2 edit effort (human).**
Evidence: AeSlides + SlideAudit + SlidesGen-Bench PEI. Đây là xác nhận có external evidence cho **RQ00 INFERENCE #2**. Confidence: cao cho phần geometry; thấp cho phần edit effort.

**P-04 — Hạ §8 "Design consistency" khỏi vị trí top-level AI-quality: phần token conformance → L0.3 deterministic; phần aesthetic → L4.4 low-priority.**
Evidence: NFR-52 tự đề xuất `% element dùng đúng token`; không có external metric cho token conformance; aesthetic phần lớn do DesignSystem cố định quyết định. Confidence: cao cho phần tách; **thấp cho việc hạ ưu tiên aesthetic** (INFERENCE thuần).

**P-05 — Thêm L3 Brief Compliance làm dimension first-class.**
Evidence: PresentBench Content Correctness, X+Slides, Learning to Present Inverse Specification. **Đây là gap lớn nhất của §8**: FR-02, FR-20, FR-23 hiện không có dimension nào nhận. Confidence: cao.
→ **Kèm theo: L3 hiện không nằm trong phạm vi RQ02–RQ05 nào.** Cần quyết định route vào đâu (mở rộng một RQ có sẵn, hay thêm task Wave 2).

**P-06 — L0 Artifact Integrity là điều kiện cổng, report tách khỏi quality score.**
Evidence: PPTAgent SR; AutoPresent gán 0 cho slide không execute. Confidence: cao.

**P-07 — Không tạo dimension mang tên component kiến trúc ("Planning Quality"/"ContentPlanner quality").**
Đo đóng góp của ContentPlanner bằng **ablation trên L2/L3.2/L4.1**. Evidence: không external work nào chấm planner trực tiếp; PPTAgent dùng ablation. Ảnh hưởng: **Q-002 vẫn mở** và taxonomy không bake sẵn câu trả lời. Confidence: trung bình-cao.

**P-08 — L5 (Outcome/Usability) xếp ưu tiên thấp nhất, ứng viên hoãn nếu scope quá tải.**
Evidence: quiz-based có precedent tốt (2 nguồn độc lập) nhưng đắt; edit effort không có precedent nào. Confidence: trung bình.

**P-09 — Ghi nhận "PresentBench" đã được verify E1; Q-005 có thể đóng ở phần "entity tồn tại".**
arXiv 2603.07244 (2026-03-07), repo `PresentBench/PresentBench` Apache-2.0, dataset HF, homepage. Mô tả trong §8 khớp method thật. **Phần "nó đề xuất protocol judge gì và có nên theo không" vẫn thuộc RQ06 — RQ01 không kết luận thay.**
*Việc cập nhật `OPEN_QUESTIONS.md`/`EVIDENCE_INDEX.md` do người điều phối làm, không phải session này.*

**P-10 — Loại nhánh reference-based similarity khỏi phạm vi cân nhắc, trừ khi RQ07 quyết định benchmark có gold deck.**
Evidence: PPTAgent Table 3/Figure 7 (ROUGE-L đi ngược chiều content quality); AutoPresent tự cảnh báo. Confidence: cao — nhưng phụ thuộc quyết định của RQ07.
