# RQ03 — Evidence

**Ngày research: 2026-08-15.** Toàn bộ nguồn dưới đây được tìm và verify bằng web search/fetch thật trong session này, trừ các mục ghi rõ là **routed từ Wave 1** (đã research ở RQ01/RQ06, không research lại — xem `03_research/RQ01/evidence.md` và `03_research/RQ06/evidence.md`).

Quy ước theo `04_evidence/EVIDENCE_POLICY.md`: **E1** (paper gốc / official benchmark repo / official docs) · **E2** (survey / reference work / technical report uy tín) · **E3** (blog / community) · **UNVERIFIED**.

## Cảnh báo về độ sâu verification (đọc trước khi dùng số liệu)

- `[abstract-verified]` — đã đọc trang abstract arXiv trực tiếp.
- `[qua tool summary]` — nội dung do fetch tool (một model nhỏ đọc trang rồi tóm tắt) trả về, **không phải tôi đọc nguyên văn**. Coi là paraphrase đáng tin ở mức tên metric/định nghĩa ngắn. **KHÔNG dùng làm quote nguyên văn trong báo cáo cuối khi chưa đọc lại bản gốc.**
- Mọi con số trích trong file này ở mức `[qua tool summary]` đều phải verify lại từ PDF gốc trước khi đưa vào báo cáo bảo vệ.
- Với X+Slides và Learning to Present — hai nguồn quan trọng nhất của RQ03 — tôi **chưa đọc PDF gốc trang thật**, chỉ đọc HTML full-text qua tool summary. Đây là điểm yếu verification lớn nhất của RQ03.

---

## MUST READ (5 nguồn)

### 1. X+Slides — nguồn gần nhất với `slide_type`, và là nguồn có **negative finding** quan trọng nhất

- **Tên:** X+Slides: Benchmarking Audience-Conditioned Slide Generation
- **Tác giả:** Haodong Chen, Xuanhe Zhou, Wei Zhou, Xinyue Shao, Yanbing Zhu, Bo Wang, Jiawei Hong, Anya Jia, Fan Wu
- **Năm/version:** arXiv 2606.19256, nộp 2026-06-17
- **Link:** https://arxiv.org/abs/2606.19256 · full-text: https://arxiv.org/html/2606.19256v1
- **Level:** **E1**
- **Đã verify:** abstract page `[abstract-verified]` (abstract nguyên văn + danh sách tác giả đầy đủ); HTML full-text `[qua tool summary]` cho phần metric/table.
- **Ngày research:** 2026-08-15

**Nguồn này thực sự support điều gì:**

1. **Cơ chế conditioning:** cùng một bộ **source-grounded probe** (8.133 probe đã khử trùng lặp, sinh bằng LLM từ tài liệu nguồn với instruction *audience-agnostic*), rồi gán **audience-specific utility weight** lên từng probe. Weight do một "LLM-based utility judge" gán theo rubric 0–1 (1.0 essential / 0.6 important / 0.3 useful background / 0.0 irrelevant), prompt có audience profile + presentation scene nhưng **giấu benchmark condition và source identity** `[qua tool summary]`.
2. **3 audience type + thời lượng gắn kèm:** Specialist (expert, 12 phút), Learner (intermediate, 15 phút), Decision Maker (nontechnical, 8 phút). Corpus 113 topic, 7 presentation scene.
3. **Công thức Efficiency — đây là thứ dùng được ngay cho Deck Agent** `[qua tool summary]`:
   - `Eff_slide = R_A / K` (utility trên mỗi slide, K = số slide)
   - `Eff_time = R_A / M`, với **`M ← 0.25·K + Words(T)/130`** — xấp xỉ thời lượng trình bày tính bằng phút (0,25 phút/slide + tốc độ nói 130 từ/phút).
   - `Audience Coverage = R_A / V_A` (R_A = tổng weight của probe được trả lời đúng; V_A = tổng weight của mọi probe essential với audience đó).
4. **Đo CONTRAST giữa các audience — Table 9 "cross-audience scoring"**: deck sinh cho audience X được chấm bằng utility function của **cả ba** audience, rồi tính "target advantage". Với hệ thống DeepPresenter `[qua tool summary]`: specialist prompt → **−0.117** (âm!), learner prompt → +0.079, decision maker prompt → +0.082.
5. **Kết luận của chính tác giả** `[qua tool summary]`: *"While audience prompts are effective, they do not consistently align with the intended profile without audience-conditioned evaluation."* và *"This suggests that audience adaptation mainly changes information selection, with grounding remaining a separate but compatible requirement."*
6. **Table 6 paired delta** (conditioned − agnostic): *"Audience conditioning gives positive shifts in Audience Coverage for all rows"*, lớn nhất ~+0,083. Tức hiệu ứng **có nhưng nhỏ**.
7. **Xử lý bất định thống kê:** bootstrap 10.000 lần resample, báo cáo khoảng 2,5–97,5 percentile `[qua tool summary]`.

**Mức áp dụng cho Deck Agent:** **trực tiếp nhất trong toàn bộ landscape.** Đây là nguồn duy nhất tìm được (a) đo audience conditioning trên đúng artifact slide deck, (b) đo **contrast chéo giữa các condition trên cùng source**, (c) báo cáo rằng conditioning **có thể thất bại và thất bại theo hướng ngược** — chính là rủi ro FR-02/FR-20 đang đối mặt.

**Cảnh báo transfer:** audience của X+Slides (specialist / learner / decision-maker) **không trùng** `teaching | catchup | speaker_support`. Hai trục khác nhau: X+Slides phân theo *người nghe là ai*; `slide_type` của Deck Agent phân theo *deck được dùng vào việc gì*. Cơ chế đo thì chuyển được; profile thì **không** chuyển được.

---

### 2. Learning to Present — cơ chế "khôi phục spec từ deck" (Inverse Specification)

- **Tên:** Learning to Present: Inverse Specification Rewards for Agentic Slide Generation
- **Tác giả:** Karthik Ragunath Ananda Kumar (Tavus Inc. / UT Dallas), Subrahmanyam Arunachalam (Texas A&M)
- **Năm/version:** arXiv 2603.16839, 2026-03-17
- **Link:** https://arxiv.org/abs/2603.16839 · full-text: https://arxiv.org/html/2603.16839v1
- **Level:** **E1** — nhưng **affiliation nhỏ, không phải benchmark được cộng đồng dùng rộng**. Coi là *idea source*, không phải authoritative benchmark (đánh giá này kế thừa từ `RQ01/evidence.md` #9 và tôi giữ nguyên).
- **Đã verify:** HTML full-text `[qua tool summary]`.
- **Ngày research:** 2026-08-15

**Nguồn này thực sự support điều gì:**

1. **Định nghĩa** `[qua tool summary]`: *"a new 'inverse task' reward formulation in which an LLM attempts to reconstruct the original specification from the generated slides alone."*
2. **Cơ chế cụ thể:** đưa deck đã sinh (HTML + PNG render) cho một LLM (họ dùng Claude Opus 4.6), model **không được xem brief gốc**, phải dự đoán một JSON gồm `topic`, **`audience`**, `num_slides`, `key_themes`.
3. **Công thức:** `r_recon = 0.40·s_topic + 0.25·s_audience + 0.15·s_count + 0.20·s_themes`.
   - `s_count` (slide count accuracy) = `min(predicted, actual) / max(predicted, actual)` — **deterministic thuần**.
   - `s_topic`, `s_audience`, `s_themes` dùng **word-overlap do LLM đo** — stochastic, họ cache theo content hash để có determinism khi chạy lại.
4. Điểm spec_reconstruction báo cáo: fine-tuned model 0.530 vs Claude Opus 0.616 `[qua tool summary]`.
5. **Tác giả thừa nhận rủi ro gaming** nhưng thảo luận rất ít.

**Mức áp dụng cho Deck Agent:** **trực tiếp về mặt ý tưởng, cần sửa về mặt thủ tục.** Ý tưởng "nếu từ deck không khôi phục lại được `slide_type` đã yêu cầu thì claim *khác biệt có chủ đích* chưa đứng vững" chuyển thẳng sang FR-02/FR-20. **Nhưng cách chấm `s_audience` bằng word-overlap là điểm yếu**: Deck Agent chỉ có **3 nhãn đóng**, nên nên dùng **forced-choice 3 lớp** (chance = 1/3, kiểm định nhị thức được) thay vì word overlap — chặt hơn hẳn và rẻ hơn.

---

### 3. Atil et al. — Non-Determinism of "Deterministic" LLM Settings

- **Tên:** Non-Determinism of "Deterministic" LLM Settings
- **Tác giả:** Berk Atil, Sarp Aykent, Alexa Chittams, Lisheng Fu, Rebecca J. Passonneau, Evan Radcliffe, Guru Rajan Rajagopal, Adam Sloan, Tomasz Tudrej, Ferhan Ture, Zhe Wu, Lixinyu Xu, Breck Baldwin
- **Năm/version:** arXiv 2408.04667, v5 ngày 2025-04-02
- **Link:** https://arxiv.org/abs/2408.04667
- **Level:** **E1** (preprint nhiều tác giả, có thực nghiệm quy mô)
- **Đã verify:** abstract page `[abstract-verified]` + metadata `[qua tool summary]`.
- **Ngày research:** 2026-08-15

**Nguồn này thực sự support điều gì:**

1. Setup: **5 LLM × 8 task × 10 run**, zero-shot và few-shot, ở cấu hình được kỳ vọng là deterministic.
2. Kết quả: **accuracy dao động tới ~15%** giữa các run xảy ra tự nhiên; **khoảng cách best–worst tới ~70%**; *"none of the LLMs consistently delivers repeatable accuracy across all tasks"* `[qua tool summary]`.
3. Đề xuất 2 metric đo mức determinism: **TARr@N** (total agreement rate trên raw output qua N run) và **TARa@N** (trên parsed answer).

**Mức áp dụng cho Deck Agent:** **trực tiếp và mang tính chặn.** Đây là evidence E1 cho mệnh đề nền của RQ03: *chạy lại cùng một `slide_type` sẽ ra deck khác nhau*. Nếu không đo được biên độ dao động này (noise floor), thì mọi khác biệt quan sát được giữa 3 `slide_type` **không phân biệt được với nhiễu**. TARr/TARa cũng là mẫu sẵn có cho cách báo cáo repeatability (khớp G-09).

**Cảnh báo transfer:** paper đo trên task **có đáp án đúng** (accuracy). Deck generation **không có** accuracy như vậy → không mượn được con số 15%, chỉ mượn được **mệnh đề định tính** (nondeterminism là thật, đáng kể, không tắt được bằng temperature) và **hình thái metric** (agreement rate qua N run).

---

### 4. EffectivePresentationScorer — "một bài nói hay không giống một bản tóm tắt"

- **Tên:** A Good Talk Does not Look Like a Summary, It Teaches You! Measuring Takeaways from Paper-to-Video Talks
- **Tác giả:** Ishani Mondal, Aparna Garimella, Ananya Sai, Pannaga Shivaswamy, Jordan Boyd-Graber
- **Năm/version:** arXiv 2606.28531, nộp 2026-06-26
- **Link:** https://arxiv.org/abs/2606.28531
- **Level:** **E1** (abstract-level verify)
- **Đã verify:** abstract page, abstract nguyên văn `[qua tool summary]`. **Chưa đọc full-text** → không biết metric là deterministic hay judged.
- **Ngày research:** 2026-08-15

**Nguồn này thực sự support điều gì (trích abstract, `[qua tool summary]`):**

1. *"existing evaluation metrics mainly measure visual quality or whether key points from the paper appear in the video without assessing whether the video actually helps viewers understand the ideas."*
2. EffectivePresentationScorer *"checks whether a video explains the main ideas clearly, introduces needed background concepts, and connects technical details to the main contribution of the paper."*
3. **Finding then chốt:** *"we find that generated videos mention the correct topics and follow the structure of the paper but fail to explain prerequisite concepts or clarify why the method works. These failures are often ignored by existing video evaluation metrics, which focus on content presence rather than explanatory quality."*

**Mức áp dụng cho Deck Agent:** **cần thích nghi (artifact là video, không phải deck), nhưng nội dung thì trúng đích.** Đây là nguồn external duy nhất tìm được phát biểu tường minh rằng **"chế độ giảng dạy" khác "chế độ tóm tắt" ở chỗ giải thích prerequisite** — tức chính trục `teaching` vs `catchup`. Và nó chỉ ra **hệ thống hiện tại fail đúng ở chỗ đó**, đồng thời **metric hiện tại không bắt được**. Với Deck Agent, đây vừa là hypothesis có nguồn cho attribute *"có giải thích khái niệm nền trước khi dùng không"*, vừa là cảnh báo rằng attribute đó **không** đo được bằng coverage.

---

### 5. IFEval — nguyên tắc "verifiable instruction"

- **Tên:** Instruction-Following Evaluation for Large Language Models
- **Tác giả:** Jeffrey Zhou, Tianjian Lu, Swaroop Mishra, Siddhartha Brahma, Sujoy Basu, Yi Luan, Denny Zhou, Le Hou
- **Năm/version:** arXiv 2311.07911, 2023
- **Link:** https://arxiv.org/abs/2311.07911
- **Level:** **E1**
- **Đã verify:** abstract + mô tả benchmark `[qua tool summary]`.
- **Ngày research:** 2026-08-15

**Nguồn này thực sự support điều gì:**

1. Động cơ tường minh: *human evaluation đắt/chậm/không tái lập được, còn LLM-based auto-eval thì có thể thiên lệch hoặc bị giới hạn bởi chính năng lực của evaluator LLM* `[qua tool summary]` → nên tập trung vào **verifiable instruction**.
2. **25 loại verifiable instruction, ~500 prompt**, mỗi instruction kiểm được **bằng code** (vd. "write in more than 400 words", "write exactly four paragraphs", "write between 100–150 words", "write in JSON format").

**Mức áp dụng cho Deck Agent:** **trực tiếp cho phần "hard constraint" của brief** (FR-23: `target_slides`, `time_limit_minutes`). Nó cũng là chỗ dựa phương pháp luận cho lập luận trung tâm của RQ03: *phần nào của yêu cầu diễn đạt lại được thành predicate kiểm bằng code thì đừng đưa cho judge* — đúng G-01.

**Cảnh báo:** IFEval **không** giải quyết phần khó của FR-02. "Khác biệt **có chủ đích**" không phải một verifiable instruction; nó là quan hệ giữa **nhiều output**, không phải thuộc tính của **một** output.

---

## SHOULD READ

### 6. SlideTailor — preference-conditioned slide generation

- **Tên:** SlideTailor: Personalized Presentation Slide Generation for Scientific Papers
- **Tác giả:** Wenzheng Zeng, Mingyu Ouyang, Langyuan Cui, Hwee Tou Ng
- **Năm/version:** arXiv 2512.20292, nộp 2025-12-23
- **Link:** https://arxiv.org/abs/2512.20292 · full-text: https://arxiv.org/html/2512.20292v1
- **Level:** **E1** · **Đã verify:** abstract `[abstract-verified]` + HTML full-text `[qua tool summary]` · **Ngày research:** 2026-08-15

**Support điều gì:**

1. **Preference-based metric** (đo alignment với preference của user) `[qua tool summary]`:
   - **Coverage** — IoU giữa tập subtopic trích từ deck sinh ra và deck mẫu. Code tính được, nhưng bước trích subtopic dùng model.
   - **Flow** — `1 − NGLD` (Normalized General Levenshtein Distance) trên **chuỗi thứ tự subtopic**. **Đây là metric structure deterministic cụ thể nhất tìm được trong toàn bộ landscape.**
   - **Content Structure** — LLM-judged 1–5, mô tả là chấm *"pace, detail level, visual formatting, and slide transitions"*, cố tình **bỏ qua chủ đề**.
   - **Aesthetic** — MLLM-judged 1–5.
2. **Preference-independent metric:** Content (LLM 1–5), Aesthetic (MLLM 1–5).
3. **Negative finding quan trọng:** *không có* metric nào cho số slide, mật độ chữ/slide, hay tỉ lệ text:image `[qua tool summary]`.
4. **chain-of-speech:** sinh đồng thời slide và lời nói kèm theo cho từng slide. **Nhưng phần lời nói không được evaluate riêng.**

**Mức áp dụng:** cần thích nghi. SlideTailor conditioning bằng **deck mẫu của user** chứ không bằng nhãn rời rạc → không chuyển thẳng sang `slide_type`. Ba thứ chuyển được: (a) **Flow = 1−NGLD trên thứ tự chủ đề** dùng được ngay trên `source_ref` của Deck IR; (b) cách tách **preference-based** khỏi **preference-independent** metric ánh xạ sạch sang "cái phụ thuộc `slide_type`" vs "cái không"; (c) chain-of-speech là precedent gần nhất cho `speaker_notes`, và việc chính họ **không** evaluate nó là tín hiệu rằng đây là vùng chưa ai giải quyết.

---

### 7. Generating Summaries with Controllable Readability Levels

- **Tên:** Generating Summaries with Controllable Readability Levels
- **Tác giả:** Leonardo F. R. Ribeiro, Mohit Bansal, Markus Dreyer
- **Năm/venue:** arXiv 2310.10623 · **EMNLP 2023 (main)**
- **Link:** https://arxiv.org/abs/2310.10623
- **Level:** **E1** · **Đã verify:** abstract page `[qua tool summary]` · **Ngày research:** 2026-08-15

**Support điều gì:** đây là precedent E1 rõ ràng cho mô hình *"đặt một attribute mục tiêu rời rạc/liên tục, rồi đo khoảng cách giữa mức được yêu cầu và mức quan sát được trên output"* — họ tối ưu trực tiếp *"the gap between requested and observed readability"* `[qua tool summary]`. Abstract nêu rõ readability phụ thuộc *"the complexity of the text, its subject matter, and the reader's background knowledge"*.

**Honesty:** trang abstract **không nêu tên các readability metric cụ thể** (FKGL/DCRS/CLI) — tôi **không** khẳng định họ dùng metric nào. Cần đọc full-text nếu muốn trích chi tiết.

**Mức áp dụng:** **transfer trung bình.** Cùng hình thái bài toán (conditioning output theo một mức yêu cầu, rồi đo output có tới mức đó không), khác artifact (đoạn text vs deck) và khác chỗ: readability có **thang đo có sẵn**, còn `teaching/catchup/speaker_support` **không có thang đo có sẵn** — đây chính là phần Deck Agent phải tự dựng.

---

### 8. Evaluating the Evaluators: Are readability metrics good measures of readability?

- **Tên:** Evaluating the Evaluators: Are readability metrics good measures of readability?
- **Tác giả:** Isabel Cachola, Daniel Khashabi, Mark Dredze
- **Năm/venue:** arXiv 2508.19221 (nộp 2025-08-26) · **EMNLP 2025** (`aclanthology.org/2025.emnlp-main.1225/`)
- **Link:** https://arxiv.org/abs/2508.19221
- **Level:** **E1** · **Đã verify:** abstract nguyên văn `[qua tool summary]` · **Ngày research:** 2026-08-15

**Support điều gì (abstract nguyên văn):** *"We evaluate 8 readability metrics and show that most correlate poorly with human judgments, including the most popular metric, FKGL. We then show that Language Models (LMs) are better judges of readability, with the best-performing model achieving a Pearson correlation of 0.56 with human judgments."*

**Mức áp dụng:** **cảnh báo trực tiếp, quan trọng.** Đây là counter-evidence cho phản xạ tự nhiên "cứ lấy một proxy deterministic (words/slide, FKGL, bullets/slide) rồi coi nó **là** thuộc tính muốn đo". Một proxy bề mặt có thể **đo được** mà vẫn **không tương ứng** với thứ con người cảm nhận. Với RQ03 nghĩa là: deterministic feature dùng được để chứng minh *có khác biệt*, **không** dùng được để chứng minh *khác biệt đó là đúng hướng/có giá trị*. Đồng thời lưu ý: 0.56 là mức LM judge tốt nhất — cũng chỉ tương đương vùng ρ≈0.5 mà RQ06 đã ghi nhận.

---

### 9. Quantifying the Persona Effect in LLM Simulations

- **Tên:** Quantifying the Persona Effect in LLM Simulations
- **Tác giả:** Tiancheng Hu, Nigel Collier
- **Năm/venue:** arXiv 2402.10811 · **ACL 2024** (`aclanthology.org/2024.acl-long.554/`)
- **Link:** https://arxiv.org/abs/2402.10811
- **Level:** **E1** · **Đã verify:** ACL Anthology + arXiv listing qua search `[qua search result]` · **Ngày research:** 2026-08-15

**Support điều gì:** persona variable chỉ giải thích **<10% variance** trong annotation của các dataset NLP chủ quan hiện có; đưa persona vào prompt cho cải thiện **khiêm tốn nhưng có ý nghĩa thống kê**; persona prompting hiệu quả nhất ở mẫu mà nhiều annotator bất đồng nhưng bất đồng nhỏ `[qua search result]`.

**Mức áp dụng: chỉ mang tính tham khảo / hiệu chỉnh kỳ vọng.** Task khác hẳn (mô phỏng annotator, không phải sinh deck). Điều mượn được: **conditioning theo profile thường tạo hiệu ứng nhỏ so với variance tổng thể** — nhất quán với X+Slides (delta ~0,05–0,08). Hệ quả cho Deck Agent: phải thiết kế phép đo đủ nhạy để bắt hiệu ứng nhỏ, và **thiết kế ghép cặp theo source** là bắt buộc chứ không phải tùy chọn.

---

### 10. Garner, Gaudelli, Zappe & Alley — thống kê thực nghiệm về mật độ chữ trên slide

- **Tên:** Application of multimedia theory to PowerPoint slides created by engineering educators
- **Tác giả:** Joanna Garner, Allen Gaudelli, Sarah Zappe, Michael Alley
- **Năm/venue:** **ASEE Annual Conference and Exposition Proceedings, 2009** (Austin, TX, 14–17/06/2009)
- **Link:** https://pure.psu.edu/en/publications/application-of-multimedia-theory-to-powerpoint-slides-created-by--2/
- **Level:** **E1** (conference paper gốc) · **Đã verify:** trang record của Penn State Pure, có abstract + số liệu `[qua tool summary]`. Chưa đọc PDF gốc. · **Ngày research:** 2026-08-15

**Support điều gì:** khảo sát **72 presentation / 1.381 slide** tại ASEE 2008: trung bình **7,5 dòng text** và **33,4 từ mỗi slide**; quy đổi ~**35 từ/phút** người xem tiếp nhận từ slide; **dưới một nửa số slide có hình**; gần một nửa tác giả dùng background trang trí `[qua tool summary]`.

**Mức áp dụng:** **tham khảo để hiệu chỉnh thang đo, không phải target.** Cho một mốc thực nghiệm về `words/slide` trong deck học thuật thật (~33), và một mốc về `image-bearing slide ratio` (<50%). **Không** dùng làm ngưỡng pass/fail cho Deck Agent: khác corpus, khác năm (2009), khác ngôn ngữ, và đây là mô tả **thực trạng**, không phải **chuẩn**.

---

### 11. Garner & Alley — assertion-evidence và comprehension

- **Tên:** How the design of presentation slides affects audience comprehension: A case for the assertion-evidence approach
- **Tác giả:** J. K. Garner, M. P. Alley
- **Năm/venue:** **International Journal of Engineering Education, 2013, 29(6):1564–1579**
- **Link:** https://pure.psu.edu/en/publications/how-the-design-of-presentation-slides-affects-audience-comprehens/ · https://www.semanticscholar.org/paper/c44e5deb4f3b8f35eb5d427cacfc07038dd53416
- **Level:** **E1** cho sự tồn tại + metadata; **chỉ mức abstract/summary cho nội dung** — chưa đọc full-text.
- **Ngày research:** 2026-08-15

**Support điều gì:** cấu trúc slide dùng **câu khẳng định đầy đủ làm headline** (assertion) + **evidence chủ yếu bằng hình** cho comprehension/recall tốt hơn headline dạng cụm từ `[qua search result]`. Nguồn liên quan (peer.asee.org, AC 2011-900) báo assertion-evidence cho comprehension và recall tốt hơn ở khái niệm phức tạp.

**Mức áp dụng:** cung cấp **một attribute quan sát được có nền học thuật**: *title có phải một câu khẳng định hoàn chỉnh không* (phân biệt với cụm danh từ). Đây là thuộc tính **đếm được bằng code** trên `Slide.title` của Deck IR. **Cảnh báo:** literature này nói về *slide tốt nói chung*, **không** nói gì về việc phân biệt 3 mode — dùng nó để chứng minh `teaching` phải có nhiều assertion-title hơn là **suy diễn vượt nguồn**.

---

### 12. Dror, Baumer, Shlomov & Reichart — kiểm định ý nghĩa thống kê trong NLP

- **Tên:** The Hitchhiker's Guide to Testing Statistical Significance in Natural Language Processing
- **Tác giả:** Rotem Dror, Gili Baumer, Segev Shlomov, Roi Reichart
- **Năm/venue:** **ACL 2018**, pp. 1383–1392 (`aclanthology.org/P18-1128/`)
- **Level:** **E1** · **Đã verify:** ACL Anthology listing `[qua search result]` · **Ngày research:** 2026-08-15

**Support điều gì:** đề xuất một **protocol chọn significance test** cho setup NLP, kèm khảo sát các test phù hợp; nhấn mạnh đặc điểm của task/eval measure quyết định chọn test nào.

**Mức áp dụng:** **phương pháp luận, chuyển được trực tiếp.** RQ03 cần đúng thứ này: khi so 3 điều kiện `slide_type` trên cùng tập source, câu "có khác biệt không" là một câu **thống kê**, không phải câu cảm tính. Nguồn này là chỗ dựa để yêu cầu **paired design + permutation/bootstrap test + effect size** thay vì so hai con số trung bình.

---

### 13. Hoewe — Manipulation Check (khái niệm neo)

- **Tên:** Manipulation Check (mục từ), trong *The International Encyclopedia of Communication Research Methods*
- **Tác giả:** Jennifer Hoewe · **Năm:** 2017 (Wiley) · **DOI:** `10.1002/9781118901731.iecrm0135`
- **Link:** https://onlinelibrary.wiley.com/doi/abs/10.1002/9781118901731.iecrm0135
- **Level:** **E2** (reference work, không phải primary study) · **Đã verify:** listing + định nghĩa `[qua search result]` · **Ngày research:** 2026-08-15

**Support điều gì:** manipulation check là *phép đo để xác minh rằng thao tác thực nghiệm đã thực sự tác động như dự định*; check thành công nghĩa là nó *"elicits the expected differences between or among experimental conditions"*; nó còn dùng để **loại trừ tác động ngoài ý muốn** lên các biến khác `[qua search result]`.

**Mức áp dụng:** **đây là cái tên đúng cho việc RQ03 phải làm.** FR-02 về bản chất là một manipulation (đổi `slide_type`), và DoD "slide_type có khác biệt đo được" chính là một manipulation check. Đặt đúng tên giúp mượn được cả bộ chuẩn phương pháp đi kèm (điều kiện đối chứng, ghép cặp, kiểm định, và **khai báo hướng kỳ vọng TRƯỚC khi chạy**). E2 → chỉ dùng làm **khung khái niệm**, không phải evidence kỹ thuật đơn lẻ cho một quyết định.

---

### 14. CourseBlueprint — audience-level conditioning cho nội dung giáo dục

- **Tên:** CourseBlueprint: A Structured Pipeline for Adaptive Pedagogical Video Generation Grounded in Course Corpora
- **Tác giả:** Md Zabirul Islam, Md Motaleb Hossen Manik, Ge Wang · **Năm:** arXiv 2606.20608, 2026-06-23
- **Link:** https://arxiv.org/pdf/2606.20608
- **Level:** **E1** (preprint, nhóm nhỏ — authority thấp) · **Đã verify:** PDF `[qua tool summary]` · **Ngày research:** 2026-08-15

**Support điều gì `[qua tool summary]`:** conditioning output theo **trình độ người học** qua adaptive prompting; bộ metric trộn **1 deterministic** (Readability — Flesch-Kincaid) với **5 LLM-judge** (Content Alignment, Pedagogical Coherence, Factual Accuracy, Engagement, Logical Flow), trong đó 3 metric được đánh dấu là **level-aware** (phụ thuộc audience). Việc kiểm "claim được giới thiệu theo thứ tự nhất quán với cấu trúc phụ thuộc" được làm **bằng LLM judge, không phải kiểm tra graph deterministic**.

**Mức áp dụng:** tham khảo. Hai điều mượn được: (a) precedent cho việc **một số metric là level-aware còn một số thì không** — trùng với cách tách của SlideTailor; (b) **negative finding hữu ích**: ngay cả khi có "dependency structure derived from the source", nhóm này vẫn không kiểm bằng graph mà giao cho judge → tức **chưa có ai làm được phép kiểm prerequisite-ordering deterministic**. **Cảnh báo lớn:** Flesch-Kincaid **không hợp lệ cho tiếng Việt**; Deck Agent có `meta.language = "vi" | "en"` (FR-35 song ngữ ở P2), nên mọi readability metric kiểu FK **không chuyển sang nhánh tiếng Việt được**.

---

## DISCOVERY ONLY

Đã verify tồn tại nhưng **chưa đọc đủ sâu để dùng làm evidence cho recommendation**.

| Tên | Link | Năm | Level | Vì sao ghi lại |
| --- | --- | --- | --- | --- |
| PPLM — Plug and Play Language Models (Dathathri, Madotto, Lan, Hung, Frank, Molino, Yosinski, Liu), ICLR 2020 | https://arxiv.org/abs/1912.02164 | 2019/2020 | E1 (metadata verified; **cơ chế eval CHƯA quote-verified**) | Đại diện kinh điển cho họ **controllable text generation**, nơi chuẩn đánh giá là **external attribute classifier accuracy** (dùng classifier độc lập để xem output có mang attribute được yêu cầu không). Đây là gốc phương pháp của "recovery test" mà RQ03 đề xuất. **Tôi đã fetch abstract page và trang đó KHÔNG xác nhận tên metric** — mô tả "external classifier accuracy là chuẩn trong lĩnh vực" hiện chỉ ở mức `[qua search summary]`, **chưa đủ để trích số hay quote**. Cần đọc §4 bản gốc trước khi dùng. |
| Wolfe, Shanmugaraj, Reineke, Peet & Moreau — Advancing the Knowledge Base on Effective Presentation Slide Design: Three Pilot Studies, *J. Business and Technical Communication* | https://journals.sagepub.com/doi/10.1177/00472816231169433 | 2024 | E1 (chưa đọc — fetch bị chặn) | Có thể chứa evidence **phản bác** các guideline slide-design phổ biến (6×6 rule, "ít chữ luôn tốt hơn"). Nếu đúng, nó ảnh hưởng trực tiếp tới việc có nên coi `words/slide` là proxy chất lượng hay chỉ là proxy **khác biệt**. **Fetch thất bại (ECONNREFUSED) — chưa verify nội dung.** |
| Slide Deck Q&A Quality Assurance App: A Multi-Stage Pipeline for Pedagogical Question Generation | https://arxiv.org/abs/2605.26428 | 2026-05 | E1 (chưa đọc) | Có bộ metric Coverage / Fidelity / **Scaffolding** ("logical progression and pedagogical relevance"), chấm 1–5. Scaffolding ở đây là của **câu hỏi**, không phải của slide → transfer chưa rõ. |
| ReadCtrl: Personalizing text generation with readability-controlled instruction learning | https://arxiv.org/html/2406.09205v1 | 2024 | E1 (chưa đọc) | Readability control **liên tục** thay vì phân loại expert/layperson. Liên quan nếu team muốn `slide_type` là thang chứ không phải 3 nhãn rời. |
| Readability Controllable Biomedical Document Summarization | https://arxiv.org/pdf/2210.04705 | 2022 | E1 (chưa đọc) | Chứa một finding có thể rất quan trọng cho human eval của RQ03: **expert cảm nhận khác biệt readability nhỏ hơn nhiều so với layperson** `[qua search result, chưa verify]`. Nếu đúng, người chấm là sinh viên trong team (gần expert với nội dung) có thể **không nhìn thấy** khác biệt mà người dùng thật nhìn thấy. |
| Stable Behavior, Limited Variation: Persona Validity in LLM Agents for Urban Sentiment Perception | https://arxiv.org/abs/2604.28048 | 2026-04 | E1 (chưa đọc) | Báo persona tạo *"statistically detectable but practically modest variation"* `[qua search result]`. Domain rất xa (urban sentiment), chỉ ghi lại như tín hiệu hội tụ với Hu & Collier. |
| Presenter's Arena — "Slide Deck vs Presentation" | https://presentersarena.com/public-speaking/presentation-tips/slide-deck-vs-presentation-whats-the-difference-and-when-to-use-each | n/d | **E3** | Nêu con số cụ thể: *deck standalone ~80–120 từ/slide; presentation slide ~6 từ*, và cách chuyển đổi giữa hai dạng là **đưa speaker notes vào thân slide**. Đây là mô tả gần nhất với trục `catchup` (đọc một mình) vs `speaker_support` (có người nói) mà tôi tìm được — **nhưng là blog, E3, không có nghiên cứu nền**. Theo `EVIDENCE_POLICY.md`, **không được dùng một mình để support recommendation**. Ghi lại vì nó cho thấy trục này tồn tại trong thực hành nghề, chỉ là **chưa được nghiên cứu học thuật**. |

---

## Nguồn ROUTED từ Wave 1 (KHÔNG research lại — dùng nguyên đánh giá của RQ01/RQ06)

RQ03 dựa vào các con số sau nhưng **không** tự verify lại; nguồn gốc và mức verification xem `03_research/RQ01/evidence.md` và `03_research/RQ06/evidence.md`.

| Nguồn | Điều RQ03 dùng | Ảnh hưởng tới RQ03 |
| --- | --- | --- |
| **PPTEval / PPTAgent** (arXiv 2501.03936, EMNLP 2025) | Human correlation theo dimension: Design Pearson **0.90** / Content 0.70 / **Coherence 0.55** (Spearman 0.88/0.73/0.57) | **Chặn trực tiếp:** dimension gần `structure` nhất là dimension judge **yếu nhất**. Không được xây claim FR-02 lên một điểm judge coherence. |
| **PresentBench** (arXiv 2603.07244) | ρ ≈ **0.532** với human ranking, trần người–người ≈ **0.664**; checklist per-instance; human rank nhanh ưu tiên *"readily perceivable cues such as structure, layout, and overall coherence"* | Hai hệ quả ngược chiều nhau: (a) judge tổng thể chỉ ~80% trần người; (b) **human lại rất nhạy với structure** khi rank nhanh → human là công cụ tốt cho phần "profile có đúng không", dở cho phần fidelity. |
| **JudgeSense** (arXiv 2604.23478, 2026) | *coherence là dimension bất ổn nhất* dưới paraphrase của prompt; model lớn hơn **không** ổn định hơn | Judge prompt cho structure phải đóng băng + version hóa; và đây là lý do RQ03 **không** đặt judge làm trục chính. |
| **Chen et al., MLLM-as-a-Judge** (ICML 2024, arXiv 2402.04788) | MLLM judge mạnh nhất ở **Pair Comparison**, lệch human rõ ở **Scoring Evaluation** và **Batch Ranking** | Cơ sở để chọn **forced-choice / so sánh** thay vì chấm điểm tuyệt đối khi buộc phải dùng judge cho `slide_type`. |
| **SlideAudit** (arXiv 2508.03630, UIST '25) | LLM detect design flaw F1 **0.331–0.655** | Củng cố: cái gì quy được về predicate thì đừng giao cho model. |
| **AeSlides** (arXiv 2604.22840) | 4 metric layout **100% programmatic** | Precedent cho việc đẩy tối đa xuống deterministic — RQ03 áp dụng cùng logic lên **feature cấu trúc** thay vì feature hình học. |
| **Calderon, Reichart & Dror — alt-test** (arXiv 2501.10970) | ≥3 annotator; 50–100 instance (ε=0.2) / 100–150 (ε=0.1); tối thiểu 30 | Dùng cho phần human của RQ03 (validate *hướng* của profile), **nhưng G-07 đang HOLD** → RQ03 ghi là candidate scale, không phải requirement. |

---

## Những gì KHÔNG tìm được (negative findings — quan trọng không kém)

Đã search chủ động, **không tìm thấy**:

1. **Không có bất kỳ nguồn external nào dùng bộ nhãn `teaching | catchup | speaker_support`** hoặc tương đương. X+Slides dùng specialist/learner/decision-maker; SlideTailor dùng deck mẫu của user; CourseBlueprint dùng trình độ người học. → **Ba nhãn của Deck Agent là do dự án tự đặt.** Hệ quả nặng: **không có literature nào định nghĩa được "đúng" cho từng mode**, nên **hướng kỳ vọng của khác biệt bắt buộc phải do team khai báo**, không thể tra ra từ nguồn.
2. **Không tìm thấy nguồn nào đo run-to-run variance của một hệ thống sinh slide** (chạy lại cùng input, cùng condition, N lần, báo cáo spread). Các nguồn về nondeterminism (Atil et al.) ở task khác. → **Noise floor cho deck generation là con số chưa ai có; Deck Agent phải tự đo.**
3. **Không tìm thấy metric nào đánh giá `speaker_notes` như một artifact có chất lượng riêng.** SlideTailor sinh "chain-of-speech" nhưng **không evaluate** nó. → Nếu Deck Agent muốn dùng `speaker_notes` làm thuộc tính phân biệt `speaker_support`, đó là **vùng không có precedent**.
4. **Không tìm thấy phép kiểm prerequisite-ordering deterministic** (kiểm bằng graph/dependency thay vì judge). CourseBlueprint có ý tưởng nhưng vẫn giao cho LLM judge. → "progression/scaffolding có tốt hơn không" hiện **không có phương án deterministic nào đã được validate**.
5. **Không tìm thấy nghiên cứu học thuật nào về trục "deck để đọc một mình" vs "deck hỗ trợ người nói"** (`catchup` vs `speaker_support`). Chỉ có tài liệu nghề nghiệp/blog (E3). → Đây là **lỗ hổng literature thật**, và cũng có thể là chỗ đóng góp riêng của đồ án — nhưng đồng nghĩa **không có baseline**.
6. **Không tìm thấy nguồn nào chấm điểm plan/outline trung gian như evaluation target** — xác nhận lại negative finding #1 của RQ01. Củng cố G-04.
