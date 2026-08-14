# RQ08 — Evidence Index

**Ngày research toàn bộ entry trong file này: 2026-08-14** (trừ khi ghi khác).

Format theo `04_evidence/EVIDENCE_POLICY.md`. Tag đọc:
- **MUST READ** — primary source cho RQ08, tối đa 5.
- **SHOULD READ** — hỗ trợ một argument cụ thể, đọc khi cần chi tiết.
- **DISCOVERY ONLY** — dùng để phát hiện entity/hướng, **không** dùng làm evidence cho recommendation.

Ghi chú chung về độ tin cậy: mọi số liệu trong file này được lấy qua **fetch tự động** trang arXiv/GitHub/docs. Với các con số sẽ đưa vào báo cáo/bảo vệ (đặc biệt bảng điểm PresentBench và bảng ablation PPTAgent), **phải mở PDF gốc và đối chiếu tay trước khi trích dẫn**.

---

## MUST READ (5 nguồn)

### 1. PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation

- **Level:** E1 (paper gốc + official repo + project page)
- **Ngày research:** 2026-08-14
- **Link:** arXiv:2603.07244 (`https://arxiv.org/abs/2603.07244`) · repo `https://github.com/PresentBench/PresentBench` (Apache-2.0, created 2026-03-20, last push 2026-07-29, 17 stars) · project page `https://presentbench.github.io/` · dataset `https://huggingface.co/datasets/lynnzuo/PresentBench`
- **Tác giả / ngày nộp:** Xin-Sheng Chen, Jiayu Zhu, Pei-lin Li, Hanzheng Wang, Shuojin Yang, Meng-Hao Guo — nộp 2026-03-07
- **Áp dụng cho RQ:** RQ08 (chính), và liên quan RQ01/RQ06/RQ07 (route ngoài phạm vi RQ08)
- **Nguồn này thực sự support điều gì:**
  1. **Protocol so sánh nhiều hệ thống sinh slide là khả thi và đã được thực hiện:** *"For each method, we provide the same background materials together with the corresponding instructions, and require the model to produce a complete slide deck (either in PDF or PPTX format)"*, mọi hệ thống chạy ở default setting. → Đây là evidence cho việc external comparison **có thể** làm fair ở mức "sản phẩm ở cấu hình mặc định".
  2. **Có sẵn điểm tham chiếu cho 8 hệ thống** (overall score): NotebookLM 62.5 · Manus 1.6 57.8 · Tiangong 54.7 · Zhipu 53.6 · PPTAgent v2 50.2 · Gamma 49.2 · Doubao 48.0 · Qwen 35.9.
  3. **Rubric checklist nhị phân per-instance tương quan với human tốt hơn holistic LLM-judge:** Spearman 0.532 vs PPTEval 0.303.
  4. **Giải mã Q-005:** "PresentBench" mà `kien-truc-do-an.pdf` §8 nhắc tới là entity có thật, và mô tả trong §8 ("checklist chi tiết riêng cho từng input, judge chấm từng mục kèm bằng chứng") **khớp** với thiết kế thật của benchmark (238 instance, trung bình 54.1 binary checklist item/instance).
- **Nguồn này KHÔNG support:** không cung cấp protocol để so *kiến trúc*; không giải quyết vấn đề model drift của sản phẩm đóng; 5 chiều của nó chưa được xác nhận là phù hợp cho Deck Agent (đó là việc của RQ01).
- **Mức áp dụng cho Deck Agent:** trực tiếp cho protocol; cần thích nghi cho dimension/checklist.

### 2. PPTAgent: Generating and Evaluating Presentations Beyond Text-to-Slides

- **Level:** E1 (paper peer-reviewed + official repo)
- **Ngày research:** 2026-08-14
- **Link:** arXiv:2501.03936 · ACL Anthology `2025.emnlp-main.728` (EMNLP 2025 Main) · repo `https://github.com/icip-cas/PPTAgent` (MIT, created 2025-01-04, last push 2026-08-10, 4,918 stars)
- **Áp dụng cho RQ:** RQ08 (chính — ablation template + external system), RQ06 (PPTEval)
- **Nguồn này thực sự support điều gì:**
  1. **Một ablation study thật trong đúng domain sinh presentation**, bỏ một component mỗi lần, giữ nguyên LM/VM (Qwen2.5 + Qwen2-VL): full 95.0% SR / 3.67 PPTEval; `w/o Outline` 91.0 / 3.30; `w/o Schema` 78.8 / 3.45; `w/o Structure` 92.2 / 3.32; `w/o CodeRender` 74.6 / **3.66**.
  2. **Bằng chứng rằng component khác nhau hỏng theo trục metric khác nhau:** `w/o CodeRender` gần như không đổi PPTEval nhưng sập success rate → **không được đọc ablation trên một metric tổng hợp**. Đây là argument mạnh nhất của RQ08 cho §3.3 của `landscape.md`.
  3. **Định nghĩa Success Rate vận hành được:** "success requires the generation of all slides without execution errors after self-correction".
  4. **Reliability của một judge framework được validate bằng human:** Pearson trung bình 0.71 (Content 0.70 / Design 0.90 / **Coherence 0.55**), 250 presentation, 4 nghiên cứu sinh, Fleiss' Kappa trung bình 0.59 → cho thấy chiều "coherence/structure" là chiều khó đạt tương quan nhất.
  5. **Baseline họ chọn là prior published method**, không phải naive LLM: DocPres (Bandyopadhyay et al. 2024), KCTV (Cachola et al. 2024) — cả hai không dùng vision model.
- **Nguồn này KHÔNG support:** PPTAgent **cần một reference presentation làm input**, nên không phải hệ thống cùng bài toán với Deck Agent; con số PPTEval của họ không so trực tiếp được với bất kỳ số nào của Deck Agent.
- **Mức áp dụng cho Deck Agent:** trực tiếp cho **phương pháp ablation**; chỉ tham khảo cho vai trò baseline.

### 3. Ferrari Dacrema, Cremonesi & Jannach — *Are We Really Making Much Progress? A Worrying Analysis of Recent Neural Recommendation Approaches*

- **Level:** E1 (paper peer-reviewed RecSys '19 + repo reproduce)
- **Ngày research:** 2026-08-14
- **Link:** arXiv:1907.06902 · ACM DOI 10.1145/3298689.3347058 · repo `https://github.com/MaurizioFD/RecSys2019_DeepLearning_Evaluation`
- **Áp dụng cho RQ:** RQ08 (Family B — chống strawman)
- **Nguồn này thực sự support điều gì:** reproduce 18 neural method publish ở hội nghị top; **6 trong 7 method reproduce được thường bị đánh bại bởi heuristic đơn giản (nearest-neighbor / graph-based) khi baseline được tune tử tế**; method còn lại không nhất quán vượt được một linear ranking method non-neural đã tune tốt. → Evidence cứng rằng **baseline yếu/không tune tạo ra ảo giác tiến bộ ở quy mô cả một lĩnh vực**, không phải lỗi cá biệt.
- **Nguồn này KHÔNG support:** khác domain (recommender, không phải generation); không nói gì về slide/LLM. Đây là evidence về **phương pháp so sánh**, không phải về nội dung bài toán.
- **Mức áp dụng cho Deck Agent:** cần thích nghi — argument chuyển được, số liệu không.

### 4. Atil et al. — *Non-Determinism of "Deterministic" LLM Settings*

- **Level:** E1 (paper gốc, code + dataset công khai)
- **Ngày research:** 2026-08-14
- **Link:** arXiv:2408.04667 (2024)
- **Áp dụng cho RQ:** RQ08 (Family A — historical/regression comparison); ảnh hưởng cả RQ06/RQ07
- **Nguồn này thực sự support điều gì:** 5 LLM × 8 task × 10 run dưới cấu hình được coi là deterministic (cả zero-shot và few-shot): **accuracy dao động tới 15% giữa các run tự nhiên**, khoảng cách best–worst tới 70%; **không LLM nào cho accuracy lặp lại nhất quán trên mọi task**. Đề xuất 2 metric TAR r@N / TAR a@N để đo tính lặp lại.
- **Nguồn này KHÔNG support:** không nói riêng về temperature=0 theo cách tách bạch trong phần tóm tắt đọc được; không nói về multimodal/slide generation.
- **Mức áp dụng cho Deck Agent:** trực tiếp — đây là lý do kỹ thuật để **bắt buộc N run + báo cáo variance** trong mọi so sánh version, và là lý do để không tin một delta nhỏ giữa hai version.

### 5. Open Design — `nexu-io/open-design`

- **Level:** E1 (official repo + official site + GitHub API metadata + official docs trong repo)
- **Ngày research / version:** check **2026-08-14**; latest release tag `open-design-v0.19.1` published **2026-08-14**; repo created 2026-04-28; last push 2026-08-14; Apache-2.0; **85,916 stars**
- **Link:** `https://github.com/nexu-io/open-design` · `https://open-design.ai/` · `https://open-design.ai/official/` · `https://raw.githubusercontent.com/nexu-io/open-design/main/docs/skills-protocol.md`
- **Áp dụng cho RQ:** RQ08 (Family D)
- **Nguồn này thực sự support điều gì:**
  1. **Đây là candidate canonical cho "Open Design"**: trang `/official/` tự tuyên bố GitHub repo + open-design.ai là hai nguồn canonical, và liệt kê các alias (Open Design / OpenDesign / open-design / opendesign / Open Design AI / OD) đều trỏ về cùng project.
  2. **Có sinh deck và xuất PPTX thật**, và **có intermediate representation**: `docs/skills-protocol.md` ghi deck mode có *"Preview: html... Primary output: index.html. Secondary output: slides.json (for PPTX export)"* → **HTML-first, IR là phụ** — ngược với Deck Agent (IR-first).
  3. **Mô hình BYOK có tiền lệ thật**: BYOK proxy cho endpoint OpenAI-compatible, spawn 20+ coding-agent CLI, MCP server, local-first desktop app (Express daemon + SQLite, frontend Next.js).
  4. **Không có evaluation framework công bố** — chỉ có thư mục `e2e/`; README không có benchmark/metric chất lượng deck.
- **Nguồn này KHÔNG support:**
  - **Không** xác nhận rằng đây là "Open Design" mà `requirements-non-functional.pdf` mục 0.1 nhắc tới. NFR doc chỉ ghi *"gần Open Design"* trong bảng so sánh mô hình trả token, **không có URL/repo**. Việc khớp hai cái là **INFERENCE chưa được team confirm**.
  - **Không** phải evidence rằng nó giải cùng bài toán: input là brief mô tả ý tưởng, không phải tài liệu nguồn dài cần trích trung thực.
  - Không có số benchmark nào để so.
- **Cảnh báo trùng tên (đã verify tồn tại, không phải cùng project):** GitHub org `opendesigndev`; `open-design-kit/opendesignkit`; `OpenCoworkAI/open-codesign`; `manalkaff/opendesign` (**tự nhận unofficial**, 239 stars, MIT, có skill `make-a-deck`).
- **Mức áp dụng cho Deck Agent:** chỉ mang tính tham khảo về mặt số liệu; **trực tiếp** về mặt architecture contrast và mô hình vận hành BYOK.

---

## SHOULD READ

### 6. Biderman et al. — *Lessons from the Trenches on Reproducible Evaluation of Language Models*

- **Level:** E1 · **Ngày research:** 2026-08-14 · **Link:** arXiv:2405.14782 (2024)
- **Support:** xác định 3 vấn đề phương pháp lặp lại trong LM evaluation — độ nhạy với evaluation setup, khó so sánh đúng giữa các method, thiếu reproducibility/transparency — và đề xuất codify các quy ước ngầm; đi kèm thư viện lm-eval.
- **KHÔNG support:** trong phần đọc được (abstract-level) **không** trích ra được checklist cụ thể "phải freeze những biến nào". Danh sách biến ở `landscape.md` §1.2 là **INFERENCE của research này**, không phải trích nguyên văn từ paper. Nếu cần trích trực tiếp trong báo cáo, phải đọc full text.
- **Áp dụng:** RQ08 Family A; RQ06.

### 7. Sclar, Choi, Tsvetkov & Suhr — *Quantifying Language Models' Sensitivity to Spurious Features in Prompt Design* (FormatSpread)

- **Level:** E1 · **Ngày research:** 2026-08-14 · **Link:** arXiv:2310.11324 (2023) · repo `https://github.com/msclar/formatspread`
- **Support:** chênh lệch tới **76 accuracy points** trên LLaMA-2-13B chỉ do đổi format prompt trong few-shot; độ nhạy không mất đi khi tăng model size / số few-shot example / instruction tuning. Khuyến nghị: báo cáo **khoảng** performance trên nhiều format thay vì một con số ở một format.
- **KHÔNG support:** đo trên task phân loại/few-shot, **không** đo trên generation task dài như sinh deck → con số 76 điểm **không** chuyển thẳng sang Deck Agent; nó support argument định tính "prompt effort là biến gây nhiễu lớn", không support một magnitude cụ thể.
- **Áp dụng:** RQ08 Family A (freeze prompt version) + Family B (chống strawman prompt).

### 8. Mizrahi et al. — *State of What Art? A Call for Multi-Prompt LLM Evaluation*

- **Level:** E1 (TACL 2024, vol 12, pp. 933–949) · **Ngày research:** 2026-08-14 · **Link:** `https://aclanthology.org/2024.tacl-1.52/`
- **Support:** phân tích 6.5M instance, 20 LLM, 39 task, 3 benchmark: instruction template khác nhau đổi performance **cả tuyệt đối lẫn thứ hạng tương đối**; đề xuất bộ metric trên nhiều paraphrase thay vì single-prompt evaluation.
- **KHÔNG support:** không phải domain generation/slide; không đưa ra số lượng paraphrase tối thiểu cho một dự án nhỏ.
- **Áp dụng:** RQ08 Family B — đây là evidence trực tiếp nhất cho việc "một prompt duy nhất cho naive baseline là thiết kế không đủ".

### 9. Musgrave, Belongie & Lim — *A Metric Learning Reality Check*

- **Level:** E1 (ECCV 2020) · **Ngày research:** 2026-08-14 · **Link:** `https://link.springer.com/chapter/10.1007/978-3-030-58595-2_41` · repo `https://github.com/KevinMusgrave/powerful-benchmarker`
- **Support:** cải thiện được claim trong nhiều năm của deep metric learning là "marginal at best" khi so công bằng; liệt kê các dạng bất công cụ thể — backbone khác nhau (ResNet50 vs GoogleNet/BN-Inception), embedding size khác nhau (512/1536 vs 64), và thủ thuật training không được nhắc trong paper.
- **KHÔNG support:** khác domain hoàn toàn; danh sách "biến phải cân" của Deck Agent (model tier, prompt effort, context budget, retry policy) là **INFERENCE ánh xạ**, không phải điều paper nói.
- **Áp dụng:** RQ08 Family B — checklist "cân biến" khi dựng naive baseline.

### 10. Lipton & Steinhardt — *Troubling Trends in Machine Learning Scholarship*

- **Level:** E1 · **Ngày research:** 2026-08-14 · **Link:** arXiv:1807.03341 (2018); đăng lại ACM Queue 17(1), DOI 10.1145/3317287.3328534
- **Support:** trend (ii) *failure to identify the sources of empirical gains* — *"emphasizing unnecessary modifications to neural architectures when gains actually stem from hyper-parameter tuning"*. → evidence cho quy tắc **một thay đổi một lần** khi so version và khi ablation.
- **KHÔNG support:** phần abstract đọc được **không** chứa hướng dẫn chi tiết về cách thiết kế ablation; nó nêu vấn đề, không đưa protocol.
- **Áp dụng:** RQ08 Family A + Family C.

### 11. Chen, Zaharia & Zou — *Are More LLM Calls All You Need? Towards Scaling Laws of Compound Inference Systems*

- **Level:** E1 (arXiv:2403.02419; có bản OpenReview) · **Ngày research:** 2026-08-14
- **Support:** performance của compound system (Vote, Filter-Vote) có thể **tăng rồi giảm** theo số lần gọi LM; nguyên nhân là độ khó không đồng nhất giữa các query (thêm call giúp query dễ, hại query khó); từ đó xây được scaling model dự đoán số call tối ưu.
- **KHÔNG support:** kiến trúc họ nghiên cứu là voting/filtering trên cùng một task, **không** phải pipeline nhiều component khác chức năng như Deck Agent. Việc dùng nó để nói "đóng góp component không cộng tuyến tính trong Deck Agent" là **INFERENCE mở rộng**, cần nói rõ trong báo cáo.
- **Áp dụng:** RQ08 Family C — cảnh báo chống cộng dồn kết quả ablation.

### 12. AutoPresent / SlidesBench

- **Level:** E1 (CVPR 2025) · **Ngày research:** 2026-08-14 · **Link:** arXiv:2501.00912; CVPR 2025 open access PDF (fetch trực tiếp PDF bị **HTTP 403** tại thời điểm research — thông tin lấy từ trang arXiv abstract)
- **Tác giả:** Jiaxin Ge, Zora Zhiruo Wang, Xuhui Zhou, Yi-Hao Peng, Sanjay Subramanian, Qinyue Tan, Maarten Sap, Alane Suhr, Daniel Fried, Graham Neubig, Trevor Darrell. Nộp 2025-01-01, revised 2025-06-19.
- **Support:** SlidesBench 7k train / 585 test từ 310 deck, 10 domain; đánh giá **reference-based** (giống deck đích) + **reference-free** (chất lượng design tự thân); benchmark cả end-to-end image generation lẫn program generation, kết luận **programmatic method cho slide chất lượng cao hơn ở dạng người dùng còn tương tác được**; self-refinement lặp cải thiện chất lượng; model AutoPresent 8B Llama-based.
- **KHÔNG support:** input là **natural-language instruction**, không phải document grounding → không support việc dùng làm baseline cho Deck Agent; số liệu chi tiết per-model **chưa đọc được** vì không fetch được PDF.
- **Áp dụng:** RQ08 Family B (lựa chọn output space của baseline có ảnh hưởng tới kết luận) + inspiration cho RQ07.

### 13. Gamma Generate API — official developer docs

- **Level:** E1 (official product docs) · **Ngày research / version:** 2026-08-14; API **v1.0 GA từ 2025-11-05**, v0.2 sunset 2026-01-16
- **Link:** `https://developers.gamma.app/` · `https://developers.gamma.app/get-started/access-and-pricing.md` · `https://help.gamma.app/en/articles/11962420-does-gamma-have-an-api`
- **Support:** có REST API tạo presentation (`https://public-api.gamma.app/v1.0/`, header `X-API-KEY`, key dạng `sk-gamma-...`); API key cấp cho gói **Pro / Ultra / Teams / Business**; credit: text **1–3 credits/card** (*"varies by the text generation model Gamma selects internally"*), image 2–15 (standard) / 20–33 (advanced) / 34–75 (premium) / 30–125 (ultra) credits/image; ví dụ chính thức: deck 10 card + 5 ảnh model cơ bản ≈ **20–60 credits**; theme phải tạo sẵn trong Gamma và tham chiếu bằng ID.
- **KHÔNG support:** **không** có rate limit được document; **không** có khả năng chọn/pin model bên dưới; **không** quy đổi được credit → USD từ nguồn đã đọc. Đây là **marketing/product doc**, không phải benchmark evidence.
- **Áp dụng:** RQ08 Family D — Gamma là external system **duy nhất trong nhóm thương mại** có đường chạy lại được bằng API chính thức.

### 14. Gemini Notebook Enterprise — Notebooks API (Google Cloud official docs)

- **Level:** E1 cho phần API quản lý notebook; **UNVERIFIED** cho phần sinh slide deck qua API · **Ngày research:** 2026-08-14
- **Link:** `https://docs.cloud.google.com/gemini/enterprise/notebooklm-enterprise/docs/api-notebooks` · `.../docs/api-notebooks-sources` · `.../docs/overview`
- **Support:** tồn tại API chính thức để tạo/lấy/chia sẻ notebook (`notebooks.create`, `notebooks.get`) và quản lý data source; cần license Gemini Notebook Enterprise + role Cloud NotebookLM User.
- **KHÔNG support:** **chưa verify** rằng API này expose chức năng **sinh slide deck / xuất PPTX**. Vì NotebookLM là hệ thống đứng đầu ở cả PresentBench và UniPPTBench, đây là điểm cần verify riêng nếu team muốn so với nó.
- **Áp dụng:** RQ08 Family D — quyết định tính khả thi của việc so với hệ thống mạnh nhất hiện tại.

---

## DISCOVERY ONLY (không dùng làm evidence)

### 15. UniPPTBench

- **Level:** **E2** (arXiv preprint, chưa peer review) · **Ngày research:** 2026-08-14 · **Link:** arXiv:2605.17356 (2026-05-17); tác giả Bo Zhao, Maosheng Pang, Chen Zhang, Huan Yang, Yixin Cao, Wei Ji
- **Support (ở mức E2):** 126 task, 4 input setting (vague-prompt / long-document / multimodal-document / multi-source), 6 domain; UniPPTEval gồm 5 metric chung (Instruction Fulfillment, Engagement, Content Accuracy, Visual Consistency, Visual Integrity) + metric theo scenario. Protocol: *"For all systems, we provide the same user intent and background materials... Each method is used according to its default or officially recommended configuration"*; output PPTX/PDF được convert thành chuỗi ảnh slide có thứ tự trước khi judge. Chạy Manus (slide mode, Manus 1.6), Canvas (Gemini 3 Flash Canvas interface), Kimi (Agent PPT mode), Skywork (PPT generation mode) + NotebookLM, AutoSlides, AutoPresent, EvoPresent + Ours-Gemini/Ours-Qwen.
- **Vì sao chỉ DISCOVERY ONLY:** preprint chưa peer review, và *"Code and data will be publicly available"* — chưa xác nhận đã public tại ngày research. Theo `EVIDENCE_POLICY.md`, không dùng E2 preprint làm evidence duy nhất cho một major recommendation. **Nhưng** phần "chuẩn hóa output về ảnh slide" của nó **trùng khớp** với protocol PresentBench (E1) → argument đó vẫn đứng được nhờ nguồn E1.

### 16. EvoPresent

- **Level:** E1 cho sự tồn tại của repo/paper; **chưa research sâu** · **Ngày research:** 2026-08-14
- **Link:** `https://github.com/UCSB-AI/EvoPresent` · `https://evopresent.github.io/`
- **Ghi nhận:** repo tự ghi **ICLR 2026**, paper *"Presenting a Paper is an Art: Self-Improvement Aesthetic Agents for Academic Presentations"*; có Checker Agent dựa trên model RL đa nhiệm `PresAesth` để phê bình và tự cải thiện draft.
- **Vì sao DISCOVERY ONLY:** chưa đọc paper; bài toán là academic paper → presentation, hẹp hơn Deck Agent. Đáng theo dõi như inspiration cho self-critique loop (FR-14–17), không phải baseline.

### 17. Auto-Slides

- **Level:** E1 cho sự tồn tại của repo; **chưa research sâu** · **Ngày research:** 2026-08-14
- **Link:** `https://github.com/Westlake-AGI-Lab/Auto-Slides`
- **Ghi nhận:** repo tự ghi **ICME 2026**, "interactive multi-agent system for creating and customizing research presentations".
- **Vì sao DISCOVERY ONLY:** chưa đọc paper/code; bài toán hẹp hơn Deck Agent.

### 18. `teng-lin/notebooklm-py`

- **Level:** **E3 / UNOFFICIAL** · **Ngày research:** 2026-08-14 · **Link:** `https://github.com/teng-lin/notebooklm-py` · PyPI `notebooklm-py`
- **Ghi nhận:** thư viện Python **không chính thức**, reverse-engineer API nội bộ của NotebookLM; tự mô tả có tải deck dạng PPTX/PDF và sửa slide bằng prompt.
- **Vì sao KHÔNG dùng:** unofficial, reverse-engineered → rủi ro vi phạm ToS, có thể hỏng bất kỳ lúc nào, không phải nguồn chính chủ. **Không được dùng làm đường chạy baseline chính thức cho đồ án.**

### 19. Các nguồn tổng hợp về ablation methodology (emergentmind, Baeldung, Grokipedia, envisioning.com, bestaiweb.ai)

- **Level:** **E3** · **Ngày research:** 2026-08-14
- **Ghi nhận:** các trang này mô tả pitfall của ablation (ranh giới ablation mơ hồ, confounded outcome, interaction effect phi tuyến, compensatory effect, thứ tự loại bỏ ảnh hưởng kết quả).
- **Vì sao DISCOVERY ONLY:** E3, không được một mình support recommendation. Các cảnh báo tương ứng trong `landscape.md` §3.3 đã được neo lại vào E1 (Lipton & Steinhardt 2018; Chen et al. 2024; bảng ablation PPTAgent) thay vì vào các trang này.

---

## UNVERIFIED

### 20. "test hapkin slide"

- **Level:** **UNVERIFIED ENTITY** · **Ngày search:** 2026-08-14
- **Đã search:** `"hapkin" slide OR presentation OR deck tool`; `"hapkin" AI tool software`; `hapkin github repository`.
- **Kết quả:** không có project/tool/benchmark nào tên "hapkin" liên quan slide generation. Kết quả trả về là các entity **khác tên**: Haiku Deck, Hapie AI, Haptik, harpin AI, HAPP, HapPhi, Vidvox/hap, HaplyHaptics, Hakkin (GitHub user).
- **Kết luận: KHÔNG đoán.** Không dùng làm evidence. Cần user/team cung cấp tên chính xác. (Q-004 vẫn OPEN.)

### 21. Liên kết "Open Design trong NFR" ↔ `nexu-io/open-design`

- **Level:** **INFERENCE, chưa verify với team** · **Ngày research:** 2026-08-14
- **PROJECT SOURCE:** `requirements-non-functional.pdf` mục 0.1: *"User trả (BYOK) | Chi phí = 0 cho team; gần Open Design | Onboarding khó; không thống nhất model khi eval | Could sau bảo vệ / soft launch"*. Không có URL/repo.
- **Vì sao chưa kết luận:** có ít nhất 5 entity mang tên gần giống (xem entry #5). Câu trong NFR chỉ nói về **mô hình trả token**, không nói về kiến trúc hay chất lượng deck.
