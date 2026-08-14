# RQ01 — Evidence

Ngày research: **2026-08-14**. Toàn bộ nguồn dưới đây được tìm bằng web search/fetch thật trong session này.

Quy ước theo `04_evidence/EVIDENCE_POLICY.md`: **E1** (paper gốc / official benchmark repo / official docs) · **E2** (survey / technical report uy tín) · **E3** (blog / community) · **UNVERIFIED**.

Tag ưu tiên đọc: **MUST READ** (tối đa 5 primary source cho cả RQ01) · **SHOULD READ** · **DISCOVERY ONLY**.

## Cảnh báo về độ sâu verification (đọc trước khi dùng số liệu)

- Với mỗi nguồn dưới đây, phần **"Đã verify trực tiếp"** ghi rõ tôi đã đọc gì (abstract page / full-text page / repo README / PDF page thật).
- Một số nội dung được trích qua **fetch tool summarization** (một model nhỏ đọc trang và tóm tắt) chứ không phải tôi tự đọc nguyên văn. Những chỗ đó được đánh dấu `[qua tool summary]` — **coi như paraphrase đáng tin ở mức tên dimension/định nghĩa ngắn, KHÔNG dùng làm quote nguyên văn trong báo cáo cuối mà chưa đọc lại bản gốc.**
- Chỗ nào tôi đọc PDF trang thật bằng Read tool thì đánh dấu `[đọc trực tiếp]` — mức tin cậy cao nhất.

---

## MUST READ

### 1. PPTAgent / PPTEval

- **Tên:** PPTAgent: Generating and Evaluating Presentations Beyond Text-to-Slides
- **Tác giả:** Hao Zheng, Xinyan Guan, Hao Kong, Jia Zheng, Weixiang Zhou, Hongyu Lin, Yaojie Lu, Ben He, Xianpei Han, Le Sun
- **Năm/version:** arXiv 2501.03936 (nộp 2025-01-07, revised 2025-02-21); bản hội nghị: EMNLP 2025 Main, ACL Anthology `2025.emnlp-main.728`
- **Link:** https://arxiv.org/abs/2501.03936 · https://aclanthology.org/2025.emnlp-main.728/ · repo: https://github.com/icip-cas/PPTAgent
- **Level:** **E1**
- **Đã verify trực tiếp:** `[đọc trực tiếp]` PDF trang 4–9 (Table 1 tiêu chí PPTEval, §4.4 Evaluation Metrics, Table 3/4 kết quả, Table 5 human correlation, Limitations).

**Nguồn này thực sự support điều gì:**

1. PPTEval chấm presentation trên **đúng 3 dimension**, thang **1–5**, tiêu chí (Table 1, nguyên văn tiếng Anh):
   - **Content** — "Text should be concise and grammatically sound, supported by relevant images."
   - **Design** — "Harmonious colors and proper layout ensure readability, while visual elements like geometric shapes enhance the overall appeal."
   - **Coherence** — "Structure develops progressively, incorporating essential background information."
2. **Content và Design chấm ở slide level; Coherence chấm ở presentation level** (§3 + §4.4). → Đây là bằng chứng rằng dimension có **granularity khác nhau**, không phải 4 ô ngang hàng.
3. **Success Rate (SR) là metric TÁCH RIÊNG khỏi PPTEval** (§4.4): "percentage of successfully completed tasks... success requires the generation of all slides without execution errors after self-correction". → External work tách **artifact validity (deterministic)** khỏi **quality (judged)**.
4. **Reference-based metric là proxy kém cho chất lượng.** §5.5 + Figure 7: Pearson correlation giữa PPL/FID và Content/Design gần 0 (PPL–Content −0.02, FID–Content −0.09). Table 3: KCTV đạt ROUGE-L cao nhất (16.76) nhưng Content thấp (2.55); PPTAgent ROUGE-L thấp hơn (14.25) nhưng Content cao (3.28) — **ROUGE-L đi ngược chiều với content quality**.
5. **Human correlation của judge không đồng đều giữa các dimension** (Table 5): Pearson Content 0.70 / Design 0.90 / Coherence 0.55 (avg 0.71); Spearman 0.73 / 0.88 / 0.57 (avg 0.74). → **Coherence (structure) là dimension judge yếu nhất.**
6. Limitations: model vẫn tạo "design flaws, such as overlapping elements, which can compromise the readability" — tức overlap là failure mode thật, và nó là thứ đo được bằng hình học.

**Mức áp dụng cho Deck Agent:** trực tiếp (cùng artifact PPTX, cùng bài toán document→deck). Cảnh báo: PPTEval "Content" **KHÔNG** đo fidelity so với tài liệu nguồn — nó đo chất lượng văn bản trên slide. Đừng nhầm với "Content fidelity" của §8.

---

### 2. PresentBench

- **Tên:** PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation
- **Tác giả:** Xin-Sheng Chen, Jiayu Zhu, Pei-lin Li, Hanzheng Wang, Shuojin Yang, Meng-Hao Guo (Tsinghua University)
- **Năm/version:** arXiv 2603.07244 [cs.CV], v1, 2026-03-07
- **Link:** https://arxiv.org/abs/2603.07244 · repo: https://github.com/PresentBench/PresentBench (Apache-2.0) · dataset: https://huggingface.co/datasets/PresentBench/PresentBench · homepage: https://presentbench.github.io/
- **Level:** **E1**
- **Đã verify trực tiếp:** arXiv abstract page (abstract nguyên văn), HF paper page, GitHub README `[qua tool summary]`, arXiv HTML full-text cho phần rubric `[qua tool summary]`.

> **Q-005 RESOLVED (về mặt tồn tại):** "PresentBench" mà architecture doc §8 trích dẫn **là entity có thật, verify được ở mức E1** — có paper arXiv, repo chính chủ Apache-2.0, dataset HF, homepage. Nó **không còn là UNVERIFIED ENTITY**. Việc route evidence sang RQ06 (judge protocol) / RQ07 (benchmark design) vẫn thuộc các RQ đó, không phải RQ01. RQ01 chỉ lấy phần **dimension taxonomy**.

**Nguồn này thực sự support điều gì:**

1. **5 rubric category** (không phải 4, không phải 3) `[qua tool summary]`:
   - **Presentation Fundamentals** — "assesses whether the slide deck presents information in a clear, coherent, and appropriate manner": theme clarity, cross-slide logical flow, title relevance, conciseness, safety.
   - **Visual Design and Layout** — design uniformity (fonts, colors, layout), balance text/visual, image quality, legibility, absence of clutter or overlapping elements.
   - **Content Completeness** — "verifies coverage using an instance-specific, instruction-derived checklist"; tất cả section bắt buộc xuất hiện đúng thứ tự, key point được xử lý đủ.
   - **Content Correctness** — "instruction-mandated content is presented correctly, i.e., consistent with the provided background materials"; thiếu item = lỗi.
   - **Content Fidelity** — "verifies strict adherence to the provided background materials" theo từng trang; "any unsupported, contradictory, or newly introduced detail is treated as hallucinated".
2. **Đây là nguồn duy nhất tách rõ 3 khái niệm content** mà §8 gộp thành một: Completeness (đủ) ≠ Correctness (đúng so với brief + nguồn) ≠ Fidelity (không bịa). → Bằng chứng mạnh nhất cho việc §8 "content fidelity (đúng/đủ nội dung so với nguồn, không bịa)" đang gộp 3 thứ khác nhau vào 1 dimension.
3. **Checklist per-instance, binary, có evidence localization**: 238 instance, trung bình **54.1 binary checklist item/instance**; judge chấm **từng item trong một call riêng**; mỗi item cho verdict nhị phân + localized evidence. Judge model: `gemini-3-flash-preview` `[qua tool summary]`.
4. **Reliability:** Spearman với human = **0.532**, so với PPTEval **0.303** và MLLM-as-a-Judge ranking **0.258**; human–human agreement = **0.664**. → checklist per-instance tốt hơn rubric holistic, **nhưng vẫn dưới mức human agreement**.
5. **Finding quan trọng cho RQ01:** ablation cho thấy human trong tình huống rank nhanh ưu tiên "readily perceivable cues such as structure, layout, and overall coherence" hơn là fine-grained fidelity verification. → **Human preference ≠ fidelity**; nếu Deck Agent chỉ đo bằng human ranking, fidelity sẽ bị under-weighted.
6. Domain: Academia, Education, Economics, Talk, Advertising; input trung bình 22.2k token / ~34 trang.

**Mức áp dụng cho Deck Agent:** trực tiếp. Đây là nguồn gần nhất về cả taxonomy lẫn method mà §8 đã (đúng) trỏ tới.

---

### 3. AutoPresent / SlidesBench

- **Tên:** AutoPresent: Designing Structured Visuals from Scratch
- **Tác giả:** Jiaxin Ge, Zora Zhiruo Wang, Xuhui Zhou, Yi-Hao Peng, Sanjay Subramanian, Qinyue Tan, Maarten Sap, Alane Suhr, Daniel Fried, Graham Neubig, Trevor Darrell
- **Năm/version:** arXiv 2501.00912 (2025-01-01); CVPR 2025
- **Link:** https://arxiv.org/abs/2501.00912 · PDF đọc được: https://nlp.cs.berkeley.edu/pubs/Ge-Wang-Zhou-Peng-Subramanian-Tan-Sap-Suhr-Fried-Neubig-Darrell_2025_AutoPresent_paper.pdf
- **Level:** **E1**
- **Đã verify trực tiếp:** `[đọc trực tiếp]` PDF trang 4–6 (§3 Evaluation Metrics đầy đủ, Table 1 reference-free criteria, §3.3 Executability, Table 3 kết quả).

**Nguồn này thực sự support điều gì:**

1. **Ba nhóm metric tách biệt rõ ràng** (§3):
   - **Reference-based** (§3.1) — 4 chiều: **Element matching** (tổng size element khớp / tổng size mọi element, dùng thư viện `match`), **Content similarity** (cosine sim embedding `all-MiniLM-L6-v2` cho text; CLIP score cho ảnh), **Color similarity** (CIEDE2000 color difference cho text color, element background, và slide background), **Position similarity** (`sim(r,g) = 1 − max(|xr−xg|, |yr−yg|)` trên toạ độ normalize [0,1]).
   - **Reference-free** (§3.2) — 4 tiêu chí, thang **0–5**, chấm bằng `gpt-4o` trên **ảnh render của slide** (Table 1):
     - **Text** — "The title should be simple and clear to indicate the main point. For main contents, avoid using too many texts and keep words concise. Use a consistent and readable font size, style, and color."
     - **Image** — "Use high-quality images with a reasonable proportion."
     - **Layout** — "Elements should be aligned, do not overlap, and have sufficient margins to each other. All elements should not exceed the page."
     - **Color** — "Use high-contrast color especially between the text and the background. Avoid using high-glaring colors."
   - **Executability** (§3.3) — % program sinh ra chạy được; **slide không execute được bị gán điểm 0 trong cột 'Overall'**.
2. **Reliability của reference-free judge:** ICC giữa 2 human annotator và model = **73.8%–85.3%** trên ground-truth slides — tác giả gọi là "high agreement".
3. Tác giả tự cảnh báo về reference-based: điểm thấp "could come from differences in text, color, and positions, or derivative errors caused by the inaccurate element-matching process" → reference-based **không phân biệt được "khác" với "kém"**.
4. Table 3: **Reference slide do người làm** đạt reference-free text 59.7 / image 81.5 / layout 73.5 / color 65.7 — tức ngay cả deck của người cũng không đạt trần. → Có sẵn một *human ceiling* để đặt kỳ vọng, không kỳ vọng 100%.
5. Execution% dao động cực rộng (LLaMA-8B 2.1% → GPT-4o 89.2%) → **artifact validity là biến chi phối, phải report riêng**.

**Mức áp dụng cho Deck Agent:** phần reference-free + executability áp dụng trực tiếp. Phần reference-based **không áp dụng** trừ khi benchmark có gold deck do người làm (hiện chưa có — thuộc RQ07).

---

### 4. SlideAudit

- **Tên:** SlideAudit: A Dataset and Taxonomy for Automated Evaluation of Presentation Slides
- **Tác giả:** Zhuohao (Jerry) Zhang, Ruiqi Chen, Mingyuan Zhong, Jacob O. Wobbrock (University of Washington)
- **Năm/version:** arXiv 2508.03630 (2025-08); UIST '25, DOI `10.1145/3746059.3747736`
- **Link:** https://arxiv.org/abs/2508.03630 · https://arxiv.org/html/2508.03630v1 · https://dl.acm.org/doi/10.1145/3746059.3747736
- **Level:** **E1**
- **Đã verify trực tiếp:** arXiv HTML full-text `[qua tool summary]` (taxonomy + bảng F1); ACM DL trả 403 nên không đọc được bản của publisher.

**Nguồn này thực sự support điều gì:**

1. **Taxonomy design flaw của slide: 5 high-level dimension / 27 category**, xây cùng design expert `[qua tool summary]`:
   - **Composition & Layout** — Cluttered Layout, Occluded Content, Unbalanced Space/Margin Distribution, **Content Overflow**, Misaligned Elements, Lack of Visual Hierarchy
   - **Typography** — Inappropriate Font Sizing, Inconsistent Text Styling, Illegible Typeface Usage, Poor Text Alignment
   - **Color** — Insufficient Contrast, Poor Color Usage, Mismatched Color Combinations
   - **Imagery** — Irrelevant Imagery, Low-Quality Images, Inappropriate Image Placement
   - **Animation & Interaction** — Distracting Animations, Inconsistent Visual Style, Unclear Interactive Elements
2. Dataset 2400 slide (600 thu thập + 1800 synthesize có chèn lỗi có kiểm soát về positioning/layout/typography/color), annotate qua Prolific có training.
3. **LLM phát hiện design flaw kém**: F1 **0.331–0.655**. Zero-shot baseline 0.476–0.519; prompt kèm full taxonomy tốt nhất 0.589–0.655 (GPT-4o + image + object description). CrowdCrit 7-principles 0.577; CrowdCrit 70-critique 0.331; UICrit adapted 0.477.
4. → **Đây là bằng chứng trực tiếp rằng "design/layout quality" chấm bằng MLLM-judge là không đáng tin ở mức per-flaw.** Cấu trúc taxonomy giúp nhưng không cứu được.

**Mức áp dụng cho Deck Agent:** cần thích nghi (taxonomy này là về *slide đã có*, không phải về pipeline sinh slide), nhưng là nguồn tốt nhất tìm được để **phân rã "Design consistency" và "Ready-to-use"** thành các lỗi cụ thể, và để biết lỗi nào deterministic-checkable.

---

### 5. AeSlides

- **Tên:** AeSlides: Incentivizing Aesthetic Layout in LLM-Based Slide Generation via Verifiable Rewards
- **Tác giả:** Yiming Pan, Chengwei Hu, Xuancheng Huang, Can Huang, Mingming Zhao, Yuean Bi, Xiaohan Zhang, Aohan Zeng, Linmei Hu
- **Năm/version:** arXiv 2604.22840, 2026-04-21
- **Link:** https://arxiv.org/abs/2604.22840 · https://arxiv.org/html/2604.22840v1
- **Level:** **E1**
- **Đã verify trực tiếp:** arXiv abstract page (abstract nguyên văn), arXiv HTML full-text `[qua tool summary]`.

**Nguồn này thực sự support điều gì:**

1. **4 verifiable metric đo layout quality, tính hoàn toàn bằng chương trình** `[qua tool summary]`:
   - **Distorted Aspect Ratio** — đo tỉ lệ render thật so với target 16:9.
   - **Excessive Whitespace** — local variance map trên ảnh grayscale + box filter + threshold.
   - **Element Collision** — heuristic trên DOM tree, **3 loại**: bounding box chồng nhau giữa element không liên quan, element tràn ra ngoài parent container, và **overflow vượt ranh giới slide**.
   - **Visual Imbalance** — độ lệch visual centroid so với tâm canvas.
2. Tác giả lập luận rõ: "many fundamental aesthetic properties of slide layouts are inherently structured and can be precisely verified through programmatic analysis" và **bác bỏ VLM-based reward** vì trong test của họ model "performed worse than random guessing on layout detection" `[qua tool summary]`.
3. Abstract (nguyên văn, verify trực tiếp) xác nhận động cơ: "the generation process is text-centric, whereas its quality is governed by visual aesthetics... Existing solutions typically rely either on heavy visual reflection, which incurs high inference cost yet yields limited gains".

**Mức áp dụng cho Deck Agent:** trực tiếp cho **FR-22 (chống tràn text) và phần geometry của "Ready-to-use"**. Đây là external evidence xác nhận INFERENCE #2 của RQ00.

> **Lưu ý honesty:** claim "VLM worse than random" tôi mới đọc qua tool summary, **chưa đọc bảng số gốc**. Trước khi đưa vào báo cáo cuối, cần đọc lại full-text và trích số cụ thể. Hiện coi là **claim mạnh nhưng chưa quote-verified**.

---

## SHOULD READ

### 6. Paper2Poster

- **Tên:** Paper2Poster: Towards Multimodal Poster Automation from Scientific Papers
- **Tác giả:** Wei Pang, Kevin Qinghong Lin, Xiangru Jian, Xi He, Philip Torr
- **Năm/version:** arXiv 2505.21497 (2025-05-27, revised 2025-10-30); NeurIPS 2025 Datasets & Benchmarks Track
- **Link:** https://arxiv.org/abs/2505.21497 · https://paper2poster.github.io/ · https://openreview.net/forum?id=p0E74lpRBD
- **Level:** **E1** cho 4 dimension; **E1 nhưng chưa quote-verified** cho breakdown 6 tiêu chí
- **Đã verify trực tiếp:** arXiv abstract page. **Không đọc được full PDF** (NeurIPS proceedings PDF vượt giới hạn 10MB của fetch tool; arXiv HTML v3 trả 404). Breakdown 6 tiêu chí lấy qua **search-result snippet trích từ paper** — chưa đọc trong bản gốc.

**Nguồn này thực sự support điều gì:**

1. 4 dimension: **Visual Quality** (semantic alignment với poster do người làm), **Textual Coherence** (perplexity dưới Llama-2-7b-hf; thấp hơn = fluent hơn), **Holistic Assessment** (6 tiêu chí, VLM-as-judge), **PaperQuiz**.
2. **PaperQuiz** — dimension *extrinsic*, khác hẳn mọi dimension khác: sinh MCQ từ paper gốc bằng LLM, rồi cho VLM "đóng vai" nhiều mức chuyên môn (student, professor) **chỉ đọc poster** và trả lời quiz. Poster nào giúp trả lời đúng nhiều nhất là poster truyền đạt tốt nhất. → **đo hiệu quả truyền đạt thông tin, không đo hình thức**.
3. 6 tiêu chí Holistic `[qua search snippet, chưa quote-verified]`: Aesthetic Score = {Element Quality, Layout Balance, Engagement}; Information Score = {Clarity, Content Completeness, Logical Flow}; thang 1–5, VLM (GPT-4o) làm judge.

**Mức áp dụng cho Deck Agent:** cần thích nghi (poster = 1 canvas, deck = chuỗi slide → Visual Quality kiểu poster không chuyển được). **PaperQuiz thì chuyển được rất tốt** và đặc biệt hợp với `slide_type = teaching | catchup`.

---

### 7. SlidesGen-Bench

- **Tên:** SlidesGen-Bench: Evaluating Slides Generation via Computational and Quantitative Metrics
- **Tác giả:** Yunqiao Yang, Wenbo Li, Houxing Ren, Zimu Lu, Ke Wang, Zhiyuan Huang, Zhuofan Zong, Mingjie Zhan, Hongsheng Li
- **Năm/version:** arXiv 2601.09487, 2026-01-14
- **Link:** https://arxiv.org/abs/2601.09487 · https://arxiv.org/html/2601.09487v1
- **Level:** **E1**
- **Đã verify trực tiếp:** arXiv abstract page + HTML full-text `[qua tool summary]`.

**Nguồn này thực sự support điều gì:**

1. **3 dimension: Content / Aesthetics / Editability** — và nêu mục tiêu thay "subjective or reference-dependent proxies" bằng "reproducible metrics".
2. **Content** = QuizBank: pipeline multi-agent trích 10 câu hỏi/document (5 concept, 5 data), parse slide thành markdown, LLM trả lời **chỉ dựa trên nội dung slide**. → Cùng ý tưởng extrinsic với PaperQuiz, nhưng dùng làm **content metric chính** chứ không phải phụ.
3. **Aesthetics = 4 metric deterministic** (không dùng judge): Harmony Score (fit hue template trong không gian HSV), Engagement Score (opponent color channels rg/yb + biến thiên theo chuỗi slide), Usability Score (figure-ground contrast dùng relative luminance BT.709), Visual Rhythm/VisualHRV (subband entropy đo clutter + RMSSD đo biến thiên giữa slide).
4. **Editability = PEI (Presentation Editability Intelligence), 6 level, có knockout rule** (fail level dưới thì không được tính level trên):
   - L0 Static — slide chỉ là ảnh phẳng, không sửa được.
   - L1 Patchwork — text tách rời và sửa được; ảnh vẫn raster.
   - L2 Vector — visual element là vector, sửa được shape.
   - L3 Structural — có tổ chức master-based phân cấp (vd. `<p:sldMaster>`), cho phép lan truyền layout toàn hệ thống.
   - L4 Parametric — table/chart là native data object có dữ liệu nền, không phải hình vẽ giả.
   - L5 Cinematic — có animation/multimedia hoạt động như narrative.
   - Đánh giá bằng human gating qua 5 test gate T1–T5.
5. Validate metric bằng dataset human-preference-aligned "Slides-Align1.5k".

**Mức áp dụng cho Deck Agent:** **Editability áp dụng rất trực tiếp** — Deck Agent claim xuất PPTX native + Editor Loop + design token; PEI L3 (master-based, lan truyền layout) chính là điều NFR-10–12 mô tả. Aesthetics deterministic thì cần thích nghi (Deck Agent dùng DesignSystem cố định nên các metric màu phần lớn là thuộc tính của design system, không phải của AI).

---

### 8. X+Slides

- **Tên:** X+Slides: Benchmarking Audience-Conditioned Slide Generation
- **Năm/version:** arXiv 2606.19256, 2026-06
- **Link:** https://arxiv.org/abs/2606.19256
- **Level:** **E1** (abstract-level verify)
- **Đã verify trực tiếp:** arXiv abstract page `[qua tool summary]`. Chưa đọc full-text. **Chưa lấy được danh sách tác giả** — cần bổ sung trước khi cite.

**Nguồn này thực sự support điều gì:**

1. **4 metric**: **Audience Coverage** (bao nhiêu thông tin thiết yếu với audience được truyền đạt), **Domain-wise Coverage** (loại thông tin nào được phủ), **Efficiency** (utility trên mỗi đơn vị attention cost), **Correctness** (claim trên slide có được nguồn support không).
2. **Cơ chế conditioning**: gán **audience-specific utility weight lên cùng một bộ source-grounded probe** — cùng thông tin nền, nhưng giá trị của nó thay đổi theo audience. Abstract nêu ví dụ "specialists demand rigorous proofs, whereas decision-makers prioritize actionable conclusions". Phạm vi: 7 presentation scene, 113 topic.

**Mức áp dụng cho Deck Agent:** đây là external work **gần nhất với `slide_type` (teaching | catchup | speaker_support)** và với FR-02/FR-20 ("cùng PDF, đổi slide_type phải tạo khác biệt đo được"). Cơ chế "cùng probe, khác weight" là một cách operationalize "khác biệt có chủ đích" mà không cần định nghĩa lại metric cho từng slide_type.

---

### 9. Learning to Present (Inverse Specification Rewards)

- **Tên:** Learning to Present: Inverse Specification Rewards for Agentic Slide Generation
- **Tác giả:** Karthik Ragunath Ananda Kumar (Tavus Inc. / UT Dallas), Subrahmanyam Arunachalam (Texas A&M)
- **Năm/version:** arXiv 2603.16839, 2026-03-17
- **Link:** https://arxiv.org/abs/2603.16839 · https://arxiv.org/html/2603.16839v1
- **Level:** **E1** (nhưng affiliation nhỏ, không phải benchmark được cộng đồng dùng rộng — coi là *idea source*, không phải authoritative benchmark)
- **Đã verify trực tiếp:** arXiv HTML `[qua tool summary]`.

**Nguồn này thực sự support điều gì:**

1. Reward system 6 thành phần, **tách rõ deterministic vs model-judged** `[qua tool summary]`:
   - Code Rules (w 1.0) — **deterministic**: có title, số section, tỉ lệ word count, section được điền.
   - Render Quality (w 2.0) — **deterministic**: số slide, render PNG thành công, HTML element hợp lệ.
   - Aesthetic HTML (w 1.5) — **model-judged** trên HTML/CSS thô.
   - Aesthetic Visual (w 1.5) — **model-judged** trên ảnh render.
   - Content Quality (w 2.0) — hỗn hợp: topic relevance, factual grounding, uniqueness, narrative flow.
   - **Inverse Specification** (w 2.0) — **model-judged**: cho LLM cố **khôi phục lại spec ban đầu chỉ từ deck sinh ra**, đo topic similarity, **audience match**, **slide count accuracy**, theme coverage.
2. **Inverse Specification là ý tưởng đáng chú ý nhất cho RQ01**: nó biến "deck có tuân thủ brief không" thành một dimension đo được, thay vì một checklist thủ công.

**Mức áp dụng cho Deck Agent:** chỉ tham khảo về mặt ý tưởng. Nhưng mapping sang FR-02/FR-20/FR-23 rất sạch: nếu từ deck sinh ra mà không recover được `slide_type` và `target_slides` đã yêu cầu, thì claim "slide_type tạo khác biệt đo được" chưa đứng vững.

---

### 10. UniPPTBench

- **Tên:** UniPPTBench: A Unified Benchmark for Presentation Generation Across Diverse Input Settings
- **Tác giả:** Bo Zhao, Maosheng Pang, Chen Zhang, Huan Yang, Yixin Cao, Wei Ji
- **Năm/version:** arXiv 2605.17356, 2026-05-17
- **Link:** https://arxiv.org/abs/2605.17356
- **Level:** **E1** (abstract-level verify)
- **Đã verify trực tiếp:** arXiv abstract page `[qua tool summary]`. Chưa đọc full-text.

**Nguồn này thực sự support điều gì:**

1. **Kiến trúc metric 2 tầng**: "shared metrics for cross-setting comparison" + "scenario-specific metrics tailored to the core requirements of each setting".
2. 4 input setting: vague-prompt, long-document, multimodal-document, multi-source.
3. Capability theo scenario: grounded compression, visual-text alignment, cross-source synthesis. Tiêu chí generic: visual appeal, layout quality, overall coherence.
4. **Không xác định được** nó có tách deterministic/rule-based khỏi judge-based hay không — abstract không nói.

**Mức áp dụng cho Deck Agent:** cần thích nghi, nhưng mô hình **2 tầng (shared + input-type-specific)** rất hợp với FR-18 (Deck Agent mở rộng sang Word/web/YouTube và phải chứng minh fidelity vẫn giữ được) — cùng shared metric để so chéo input type, cộng metric riêng cho từng loại nguồn.

---

### 11. DOC2PPT

- **Tên:** DOC2PPT: Automatic Presentation Slides Generation from Scientific Documents
- **Tác giả:** Tsu-Jui Fu, William Yang Wang, Daniel McDuff, Yale Song
- **Năm/version:** arXiv 2101.11796 (2021); AAAI 2022, 36(1):634–642
- **Link:** https://arxiv.org/abs/2101.11796 · https://ojs.aaai.org/index.php/AAAI/article/view/19943
- **Level:** **E1**
- **Đã verify trực tiếp:** search result metadata + được PPTAgent cite trong reference list `[đọc trực tiếp]` (trang 9 PDF PPTAgent). Chưa đọc full-text DOC2PPT.

**Nguồn này thực sự support điều gì:**

1. Là **mốc lịch sử**: bài đầu đặt ra task document→slides và tự nhận "no established evaluation metrics and baselines", nên tự đề xuất metric.
2. Metric: **ROUGE-SL** (ROUGE ở slide level) và **mIoU** (đo layout so với slide gốc), cộng human evaluation. Dataset ~6K cặp document–deck.
3. → Cho thấy **thế hệ metric reference-based đầu tiên** đã bị các paper sau (PPTAgent §5.5) chứng minh là proxy kém.

**Mức áp dụng cho Deck Agent:** chỉ mang tính tham khảo lịch sử. **Không khuyến nghị dùng ROUGE-SL/mIoU**, và lý do đã có evidence (PPTAgent Table 3 + Figure 7).

---

## DISCOVERY ONLY

Các nguồn dưới đây đã verify tồn tại nhưng **chưa đọc đủ sâu để dùng làm evidence cho recommendation**. Ghi lại để RQ khác (đặc biệt RQ06, RQ07, RQ08) có thể đi tiếp.

| Tên | Link | Năm | Level | Vì sao ghi lại |
| --- | --- | --- | --- | --- |
| Enhancing Presentation Slide Generation by LLMs with a Multi-Staged End-to-End Approach (Bandyopadhyay et al., "DocPres") | https://arxiv.org/abs/2406.06556 | 2024 | E1 | Baseline rule-based mà PPTAgent so sánh. Đề xuất metric **Coverage** ở 2 mức: paragraph–slide và sentence–bullet. Liên quan RQ08 (baseline). |
| AI-Generated Slides: Are They Good? Can Students Tell? (Leinonen, Zhang, Hellas) | https://arxiv.org/html/2605.13532v1 | 2026-05-13 | E1 | Human study thật: 3 tiêu chí **Factual accuracy / Completeness / Pedagogical soundness**. Instructor phải sửa styling, xoá slide, chỉnh font code — mô tả định tính, **không lượng hoá edit effort**. Student không phân biệt được slide AI vs người (median 5.0/7.0). Liên quan trực tiếp tới "ready-to-use" và `slide_type=teaching`. |
| MLLM-as-a-Judge Exhibits Model Preference Bias | https://arxiv.org/abs/2604.11589 | 2026-04 | E1 | Self-preference bias trong MLLM judge — §8 tự nhắc bias này. **Thuộc RQ06**, ghi lại để route. |
| MM-JudgeBias: A Benchmark for Evaluating Compositional Biases in MLLM-as-a-Judge | https://arxiv.org/pdf/2604.18164 | 2026-04 | E1 | Position bias, verbosity bias trong MLLM judge. **Thuộc RQ06.** |
| MLLM-as-a-Judge: Assessing Multimodal LLM-as-a-Judge with Vision-Language Benchmark (Chen et al.) | https://arxiv.org/abs/2402.04788 | 2024 | E1 | Nền tảng MLLM-judge; được cả PPTAgent lẫn PresentBench cite. **Thuộc RQ06.** |
| PosterForest | https://arxiv.org/pdf/2508.21720 | 2025-08 | E1 | Poster generation multi-agent. Chỉ discovery. |
| Any2Poster | https://arxiv.org/pdf/2606.02915 | 2026-06 | E1 | Poster từ nhiều nguồn modality. Chỉ discovery. |
| OmniPresent: Generating Coherent Presentation Suites from Scientific Papers | https://arxiv.org/html/2607.02590v1 | 2026-07 | E1 | Chỉ discovery. |
| PaperX: A Unified Framework for Multimodal Academic Presentation Generation with Scholar DAG | https://arxiv.org/pdf/2602.03866 | 2026-02 | E1 | Chỉ discovery. |
| SlideTailor: Personalized Presentation Slide Generation for Scientific Papers | https://arxiv.org/pdf/2512.20292 | 2025-12 | E1 | Personalization — có thể liên quan `slide_type`. Chưa đọc. |
| MemSlides | https://arxiv.org/pdf/2606.17162 | 2026-06 | E1 | Personalized slide gen + **multi-turn local revision** — gần với Editor Loop (FR-14–17). Chưa đọc. |
| DeepSlide: From Artifacts to Presentation Delivery | https://arxiv.org/html/2605.15202v1 | 2026-05 | E1 | Chỉ discovery. |
| Presenting a Paper is an Art: Self-Improvement Aesthetic Agents for Academic Presentations | https://arxiv.org/pdf/2510.05571 | 2025-10 | E1 | Aesthetic self-improvement. Chưa đọc. |
| EfficientPosterGen (có "Accurate Violation Detection") | https://arxiv.org/pdf/2603.00155 | 2026-03 | E1 | "Violation detection" nghe như deterministic layout check — có thể củng cố AeSlides. Chưa đọc. |
| OutlineSpark | https://arxiv.org/pdf/2403.09121 | 2024-03 | E1 | Outline-driven slide creation từ computational notebook. Liên quan FR-07 (plan/outline). Chưa đọc. |

---

## Những gì KHÔNG tìm được (negative findings — quan trọng không kém)

Đã search chủ động, **không tìm thấy**:

1. **Không có external work nào chấm điểm trực tiếp một intermediate plan / outline / IR như một evaluation target riêng.** PPTAgent có ablation `w/o Outline` và `w/o Structure` nhưng đo tác động lên **điểm deck cuối** (coherence 4.48 → 3.36/3.45), không chấm chất lượng outline. → Ảnh hưởng lớn tới giả thuyết "ContentPlanner đáng đầu tư evaluation nhất" (Q-002) và tới FR-07.
2. **Không tìm thấy metric external nào cho "design token conformance"** kiểu NFR-52 (`% element dùng đúng token`). Cái gần nhất là PresentBench "design uniformity (fonts, colors, and layout)" (judged) và SlidesGen-Bench Harmony Score (deterministic nhưng đo hoà sắc, không đo tuân thủ token). → NFR-52 candidate có thể là **đóng góp riêng**, không có precedent để dựa vào.
3. **Không tìm thấy metric external nào lượng hoá "edit effort" của người dùng trên deck sinh tự động.** Nguồn gần nhất (`arXiv 2605.13532`) chỉ mô tả định tính ("little effort", "deletion is reasonable"). Literature về post-editing effort tồn tại nhưng ở domain **machine translation / text summarization**, chưa thấy chuyển sang deck. → "Ready-to-use" hiểu theo nghĩa edit effort là **chỗ evidence yếu nhất** của cả RQ01.
4. **Không tìm thấy dimension nào tương ứng với `source_ref` traceability** (FR-03) trong external benchmark. PresentBench có "localized evidence" nhưng đó là evidence của **judge**, không phải của **deck**. → Traceability có thể là đóng góp riêng của Deck Agent, nhưng cũng có nghĩa là không có baseline để so.
5. **Không tìm thấy survey/review paper chuyên về evaluation của presentation generation** (mức E2 tổng hợp). Landscape hiện là tập hợp các benchmark paper rời rạc, mỗi bài tự định nghĩa dimension riêng — điều này tự nó là một finding: **chưa có taxonomy chuẩn hoá trong cộng đồng.**
