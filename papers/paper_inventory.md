# Paper Inventory — toàn bộ paper được reference trong repository

**Ngày lập:** 2026-08-17
**Phạm vi scan:** toàn bộ file `evidence.md` trong repository. Tìm thấy đúng **5 file**:

| File | RQ | Ngày research ghi trong file |
| --- | --- | --- |
| `03_research/RQ01/evidence.md` | RQ01 — Evaluation dimensions | 2026-08-14 |
| `03_research/RQ06/evidence.md` | RQ06 — LLM-as-judge | 2026-08-14 |
| `03_research/RQ08/evidence.md` | RQ08 — Baselines & comparison | 2026-08-14 |
| `03_research/RQ02/evidence.md` | RQ02 — Content fidelity / coverage | 2026-08-15 |
| `03_research/RQ03/evidence.md` | RQ03 — Structure & slide_type conditioning | 2026-08-15 |

**Tổng số paper duy nhất sau khi khử trùng lặp: 97.**

---

## Cách đọc file này — 4 cảnh báo bắt buộc

**1. File này là bản kiểm kê, KHÔNG phải bản verify độc lập.**
Mọi thông tin (tác giả, năm, venue, link, số liệu) được **transcribe từ 5 file `evidence.md`**. Tôi không fetch lại bất kỳ paper nào trong session này. Nếu một file evidence ghi "chưa đọc PDF gốc" hoặc "[qua tool summary]", thì entry tương ứng ở đây **kế thừa nguyên trạng thái đó**. Trước khi trích vào báo cáo bảo vệ, phải verify lại theo `04_evidence/EVIDENCE_POLICY.md`.

**2. Trường "Paper nói gì?" chỉ được viết từ nội dung repo đã ghi lại.**
Với paper mà repo chỉ ghi ở mức DISCOVERY (một dòng, chưa đọc), câu mô tả contribution được đánh dấu `[mức repo ghi nhận — chưa đọc sâu]`. Tôi **không** bổ sung kiến thức ngoài repo vào các entry này, đúng theo kỷ luật của `CLAUDE.md` ("không invent").

**3. Trường "Tại sao paper này xuất hiện trong project?" phản ánh vai trò evidence trong repo,** tức nó support assumption/decision nào của **Deck Agent evaluation framework** — chứ không phải "vì sao nó hữu ích khi build một sample-deck agent". Hai câu hỏi đó khác nhau; câu thứ hai được trả lời ở `sample_deck_agent_knowledge_roadmap.md`.

**4. Repo này là research về ĐÁNH GIÁ deck, không phải về SINH deck.**
Hệ quả trực tiếp lên inventory: mật độ paper rất cao ở nhóm evaluation/metric/judge, và rất thấp ở nhóm generation (layout generation, typography, visual asset). Đây không phải lỗi kiểm kê — đây là **hình dạng thật của evidence base**, và nó là input chính cho phần "Knowledge gaps" của deliverable 2.

**Ký hiệu "Referenced from":** `RQ01`, `RQ02`, `RQ03`, `RQ06`, `RQ08` = file `03_research/RQ0X/evidence.md`. Tag trong ngoặc là tag đọc mà chính file evidence đó gán (`MUST` / `SHOULD` / `DISCOVERY` / `ROUTED` / `UNVERIFIED`).

**Quy tắc dedup đã áp dụng:** một paper xuất hiện ở nhiều file evidence → **một entry duy nhất**, liệt kê đủ mọi file reference nó. Paper chỉ xuất hiện trong Related Work của paper khác mà repo không tự reference → **không đưa vào** (ví dụ: KCTV/Cachola et al. chỉ được nhắc như baseline bên trong PPTAgent, không có entry evidence riêng → không tạo entry).

---

## §0 — Tổng quan phân bố

| Research area | Số paper | Ghi chú |
| --- | --- | --- |
| A. Presentation generation — systems & benchmarks | 22 | Lõi domain |
| B. Poster / adjacent artifact generation | 4 | Cùng họ, khác artifact |
| C. Factuality / coverage / summarization evaluation | 26 | Nhóm đông nhất |
| D. LLM & MLLM-as-a-judge | 17 | |
| E. Rubric / checklist construction | 5 | |
| F. Controllability & conditioning | 8 | |
| G. Presentation design — human factors | 3 | Nhóm mỏng nhất, đáng chú ý |
| H. Methodology / reproducibility / statistics | 11 | |
| I. Compound system behavior | 1 | |
| **Tổng** | **97** | |

**Quan sát rút ra ngay từ bảng này:** 26 + 17 + 5 = **48/97 paper (≈ 49%) thuộc về "làm sao đo"**, trong khi chỉ 3 paper nói về **nguyên lý thiết kế slide cho con người**, và **0 paper** nói về cách *sinh* layout. Đây là dấu vân tay của một repo evaluation, và là ràng buộc quan trọng nhất khi dùng bộ paper này làm giáo trình xây agent.

---

# A. Presentation generation — systems & benchmarks

### `PPTAgent: Generating and Evaluating Presentations Beyond Text-to-Slides`

- **Authors:** Hao Zheng, Xinyan Guan, Hao Kong, Jia Zheng, Weixiang Zhou, Hongyu Lin, Yaojie Lu, Ben He, Xianpei Han, Le Sun
- **Year:** 2025 (arXiv v1 2025-01-07, v2 2025-02-21)
- **Venue / Source:** EMNLP 2025 Main
- **DOI / URL:** https://arxiv.org/abs/2501.03936 · https://aclanthology.org/2025.emnlp-main.728/ · repo https://github.com/icip-cas/PPTAgent (MIT, 4.918 stars tại 2026-08-14)
- **Referenced from:** RQ01 (MUST), RQ06 (MUST), RQ08 (MUST), RQ03 (ROUTED)
- **Research area:** Presentation generation · Agent architecture · Evaluation

**Paper nói gì?**
Thay vì sinh slide từ text theo một lượt, PPTAgent **chỉnh sửa một presentation tham chiếu có sẵn** qua các component tách rời (Outline → Schema → Structure → CodeRender) có self-correction, và đóng gói kèm **PPTEval** — bộ đo 3 chiều Content/Design/Coherence chấm bằng MLLM trên **ảnh render** của slide, tách riêng khỏi **Success Rate** (deck có sinh ra được không).

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn E1 gần Deck Agent nhất về cả artifact (PPTX) lẫn bài toán, nên nó chống đỡ ba thứ cùng lúc: (a) bằng chứng rằng **artifact validity phải tách khỏi quality** (Success Rate ≠ PPTEval); (b) bằng chứng số rằng **reference-based metric là proxy kém** (ROUGE-L cao nhất lại đi kèm Content thấp nhất, tương quan PPL/FID với chất lượng ≈ 0); (c) bảng ablation thật để RQ08 lập luận rằng **không được đọc ablation trên một metric tổng hợp**. Con số human correlation theo chiều (Content 0.70 / Design 0.90 / **Coherence 0.55**) và Fleiss' κ ≈ 0.59 giữa 4 người chấm là hai hiệu chỉnh kỳ vọng mạnh nhất trong cả repo.

---

### `PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation`

- **Authors:** Xin-Sheng Chen, Jiayu Zhu, Pei-lin Li, Hanzheng Wang, Shuojin Yang, Meng-Hao Guo (Tsinghua University)
- **Year:** 2026 (arXiv v1, nộp 2026-03-07)
- **Venue / Source:** arXiv preprint (chưa xác nhận peer-review)
- **DOI / URL:** https://arxiv.org/abs/2603.07244 · repo https://github.com/PresentBench/PresentBench (Apache-2.0) · https://presentbench.github.io/ · HF dataset `PresentBench/PresentBench`
- **Referenced from:** RQ01 (MUST), RQ06 (MUST), RQ08 (MUST), RQ03 (ROUTED)
- **Research area:** Evaluation · Benchmark design · Presentation generation

**Paper nói gì?**
Chất lượng deck được phân rã thành **5 chiều** (Presentation Fundamentals, Visual Design & Layout, Content Completeness, Content Correctness, Content Fidelity) và chấm bằng **checklist nhị phân soạn riêng cho từng instance** (238 instance, trung bình 54,1 item/instance, mỗi item một call judge riêng kèm localized evidence) — cách này đạt Spearman 0,532 với human, so với 0,303 của rubric holistic PPTEval, trần human–human 0,664.

**Tại sao paper này xuất hiện trong project?**
Đây là entity mà architecture doc §8 trích dẫn và từng bị đánh dấu UNVERIFIED (Q-005); RQ06 verify được nó **có thật** ở mức E1. Về nội dung, nó là **bằng chứng mạnh nhất** cho luận điểm trung tâm của RQ01/RQ02: cái mà §8 gọi gộp là "content fidelity" thực ra là **ba khái niệm khác nhau** (đủ ≠ đúng ≠ không bịa). Nó cũng cung cấp bảng điểm 8 hệ thống cho RQ08 và protocol judge cho RQ06.

---

### `AutoPresent: Designing Structured Visuals from Scratch` (kèm SlidesBench)

- **Authors:** Jiaxin Ge, Zora Zhiruo Wang, Xuhui Zhou, Yi-Hao Peng, Sanjay Subramanian, Qinyue Tan, Maarten Sap, Alane Suhr, Daniel Fried, Graham Neubig, Trevor Darrell
- **Year:** 2025 (arXiv 2025-01-01, revised 2025-06-19)
- **Venue / Source:** CVPR 2025
- **DOI / URL:** https://arxiv.org/abs/2501.00912 · PDF: https://nlp.cs.berkeley.edu/pubs/Ge-Wang-Zhou-Peng-Subramanian-Tan-Sap-Suhr-Fried-Neubig-Darrell_2025_AutoPresent_paper.pdf
- **Referenced from:** RQ01 (MUST), RQ06 (SHOULD), RQ08 (SHOULD)
- **Research area:** Presentation generation · Program synthesis · Evaluation · Layout

**Paper nói gì?**
Sinh slide bằng cách **sinh chương trình** (code) thay vì sinh ảnh cho kết quả người dùng còn sửa được, và bộ metric đi kèm tách thành **3 nhóm không trộn lẫn** — reference-based (element matching, content/color/position similarity), reference-free (Text/Image/Layout/Color chấm 0–5 bằng GPT-4o trên ảnh render, ICC với người 73,8–85,3%), và **Executability** (slide không chạy được bị gán 0).

**Tại sao paper này xuất hiện trong project?**
Nó là nguồn tốt nhất trong repo cho câu hỏi "**phần nào của chất lượng slide đo được bằng code**" — hậu thuẫn candidate deterministic của NFR-52 và phần geometry của "ready-to-use". Ngoài ra Table 3 cho một **human ceiling** (deck do người làm chỉ đạt text 59,7 / layout 73,5) để team không đặt kỳ vọng 100%, và Execution% dao động 2,1%–89,2% là bằng chứng rằng artifact validity là biến chi phối phải report riêng.

---

### `SlidesGen-Bench: Evaluating Slides Generation via Computational and Quantitative Metrics`

- **Authors:** Yunqiao Yang, Wenbo Li, Houxing Ren, Zimu Lu, Ke Wang, Zhiyuan Huang, Zhuofan Zong, Mingjie Zhan, Hongsheng Li
- **Year:** 2026 (arXiv 2026-01-14)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2601.09487 · https://arxiv.org/html/2601.09487v1
- **Referenced from:** RQ01 (SHOULD), RQ06 (DISCOVERY — RQ06 đánh dấu UNVERIFIED vì chưa fetch)
- **Research area:** Evaluation · Editable graphics representation · Visual design · Rendering

**Paper nói gì?**
Đề xuất thay "subjective or reference-dependent proxies" bằng metric tái lập được trên 3 chiều — Content (QuizBank: LLM trả lời câu hỏi *chỉ dựa trên slide*), Aesthetics (4 metric deterministic: Harmony/Engagement/Usability/Visual Rhythm), và **Editability qua thang PEI 6 mức có knockout rule** (L0 Static → L1 Patchwork → L2 Vector → **L3 Structural/master-based** → L4 Parametric → L5 Cinematic).

**Tại sao paper này xuất hiện trong project?**
Thang PEI là nguồn external **duy nhất** trong repo mô tả được điều NFR-10–12 muốn (xuất PPTX native, master-based, design token lan truyền) như một thuộc tính **có thang đo**. RQ01 dùng nó để lập luận rằng "editability" là một chiều chất lượng độc lập chứ không phải chi tiết kỹ thuật. ⚠ Lưu ý mâu thuẫn nội bộ repo: RQ01 xếp E1 (đã fetch), RQ06 xếp UNVERIFIED (chưa fetch) — hai file research ở hai thời điểm khác nhau.

---

### `UniPPTBench: A Unified Benchmark for Presentation Generation Across Diverse Input Settings`

- **Authors:** Bo Zhao, Maosheng Pang, Chen Zhang, Huan Yang, Yixin Cao, Wei Ji
- **Year:** 2026 (arXiv 2026-05-17)
- **Venue / Source:** arXiv preprint (chưa peer-review)
- **DOI / URL:** https://arxiv.org/abs/2605.17356
- **Referenced from:** RQ01 (SHOULD), RQ06 (DISCOVERY), RQ08 (DISCOVERY)
- **Research area:** Benchmark design · Evaluation · Document understanding

**Paper nói gì?**
Một benchmark **2 tầng metric** — shared metric để so chéo mọi setting + scenario-specific metric riêng cho từng loại input (vague-prompt / long-document / multimodal-document / multi-source, 126 task, 6 domain) — với phát hiện then chốt rằng *"strong performance on generic presentation-quality metrics does not necessarily imply strong task fulfillment in grounded scenarios"*.

**Tại sao paper này xuất hiện trong project?**
Mô hình 2 tầng khớp trực tiếp với FR-18 (Deck Agent mở rộng sang Word/web/YouTube và vẫn phải giữ fidelity): cần metric chung để so chéo input type, cộng metric riêng cho từng nguồn. Kết luận "rubric chung không phát hiện được failure về grounding" là counter-evidence quan trọng cho việc chỉ dựa vào một rubric tổng quát. RQ08 giữ ở mức DISCOVERY vì preprint chưa peer-review và code/data chưa xác nhận public.

---

### `X+Slides: Benchmarking Audience-Conditioned Slide Generation`

- **Authors:** Haodong Chen, Xuanhe Zhou, Wei Zhou, Xinyue Shao, Yanbing Zhu, Bo Wang, Jiawei Hong, Anya Jia, Fan Wu
- **Year:** 2026 (arXiv, nộp 2026-06-17)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2606.19256 · https://arxiv.org/html/2606.19256v1
- **Referenced from:** RQ01 (SHOULD), RQ03 (MUST)
- **Research area:** Conditioning / personalization · Evaluation · Planning

**Paper nói gì?**
Cùng một tài liệu nguồn sinh ra **một bộ probe chung** (8.133 probe source-grounded, sinh audience-agnostic), rồi **gán utility weight khác nhau theo audience** (Specialist / Learner / Decision Maker) — nhờ đó đo được Audience Coverage, Efficiency theo slide và theo thời gian (`M ← 0.25·K + Words/130`), và đặc biệt là **contrast chéo** giữa các audience trên cùng nguồn.

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn external **gần `slide_type` nhất** (teaching | catchup | speaker_support) và là nguồn duy nhất đo được điều FR-02/FR-20 đòi hỏi: "cùng PDF, đổi tham số phải tạo khác biệt đo được". Quan trọng hơn, nó mang **negative finding** quyết định: audience conditioning có thể **thất bại theo hướng ngược** (target advantage −0,117 cho specialist ở một hệ thống), và hiệu ứng khi thành công cũng chỉ ~+0,08 — tức nhỏ, đòi hỏi thiết kế đo ghép cặp mới thấy được.

---

### `Learning to Present: Inverse Specification Rewards for Agentic Slide Generation`

- **Authors:** Karthik Ragunath Ananda Kumar (Tavus Inc. / UT Dallas), Subrahmanyam Arunachalam (Texas A&M)
- **Year:** 2026 (arXiv 2026-03-17)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2603.16839 · https://arxiv.org/html/2603.16839v1
- **Referenced from:** RQ01 (SHOULD), RQ03 (MUST)
- **Research area:** Agent architecture · Reward design · Evaluation

**Paper nói gì?**
Đề xuất **inverse specification reward**: cho một LLM **chỉ nhìn deck đã sinh** (HTML + PNG render) và cố khôi phục lại brief gốc dưới dạng JSON (`topic`, `audience`, `num_slides`, `key_themes`) — biến câu hỏi "deck có tuân thủ yêu cầu không" thành một điểm số, nằm trong hệ 6 reward tách rõ deterministic (Code Rules, Render Quality) khỏi model-judged.

**Tại sao paper này xuất hiện trong project?**
Cơ chế "khôi phục spec từ output" ánh xạ sạch sang FR-02/FR-20/FR-23: nếu từ deck không recover được `slide_type` và `target_slides` đã yêu cầu thì claim "khác biệt có chủ đích" chưa đứng vững. Cả hai file evidence đều hạ mức tin cậy (affiliation nhỏ, không phải benchmark cộng đồng dùng) và coi đây là **idea source**, không phải authoritative benchmark. RQ03 còn chỉ ra cách chấm `s_audience` bằng word-overlap là điểm yếu — với 3 nhãn đóng nên dùng forced-choice 3 lớp.

---

### `AeSlides: Incentivizing Aesthetic Layout in LLM-Based Slide Generation via Verifiable Rewards`

- **Authors:** Yiming Pan, Chengwei Hu, Xuancheng Huang, Can Huang, Mingming Zhao, Yuean Bi, Xiaohan Zhang, Aohan Zeng, Linmei Hu
- **Year:** 2026 (arXiv 2026-04-21)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2604.22840 · https://arxiv.org/html/2604.22840v1
- **Referenced from:** RQ01 (MUST), RQ03 (ROUTED)
- **Research area:** Layout · Visual design · Reward design · Rendering

**Paper nói gì?**
Nhiều thuộc tính thẩm mỹ của layout slide *"are inherently structured and can be precisely verified through programmatic analysis"*, nên paper thay VLM-based reward bằng **4 metric tính hoàn toàn bằng chương trình** — Distorted Aspect Ratio, Excessive Whitespace (local variance map), **Element Collision** (3 loại: chồng lấn, tràn container, tràn slide), Visual Imbalance (lệch visual centroid) — và báo rằng trong test của họ VLM *"performed worse than random guessing on layout detection"*.

**Tại sao paper này xuất hiện trong project?**
Đây là external evidence xác nhận INFERENCE #2 của RQ00 và chống đỡ trực tiếp FR-22 (chống tràn text) + phần geometry của "ready-to-use": cái gì quy được về predicate hình học thì **không nên** giao cho judge. ⚠ RQ01 ghi rõ claim "VLM worse than random" mới đọc qua tool summary, **chưa quote-verified**.

---

### `SlideAudit: A Dataset and Taxonomy for Automated Evaluation of Presentation Slides`

- **Authors:** Zhuohao (Jerry) Zhang, Ruiqi Chen, Mingyuan Zhong, Jacob O. Wobbrock (University of Washington)
- **Year:** 2025 (arXiv 2025-08)
- **Venue / Source:** UIST '25
- **DOI / URL:** https://arxiv.org/abs/2508.03630 · DOI `10.1145/3746059.3747736`
- **Referenced from:** RQ01 (MUST), RQ03 (ROUTED)
- **Research area:** Visual design · Typography · Layout · Evaluation · HCI

**Paper nói gì?**
Xây **taxonomy 5 nhóm / 27 category lỗi thiết kế slide** cùng design expert (Composition & Layout, Typography, Color, Imagery, Animation & Interaction) trên dataset 2.400 slide, và chứng minh **LLM phát hiện lỗi thiết kế rất kém** — F1 chỉ 0,331–0,655 ngay cả khi được cấp full taxonomy + ảnh + object description.

**Tại sao paper này xuất hiện trong project?**
Nó làm hai việc trái chiều nhau và cả hai đều cần: (a) là nguồn tốt nhất để **phân rã "Design consistency" và "Ready-to-use"** thành danh sách lỗi cụ thể, từ đó biết lỗi nào deterministic-checkable; (b) là bằng chứng trực tiếp rằng chấm design bằng MLLM-judge **không đáng tin ở mức per-flaw**. Cần thích nghi: taxonomy này nói về *slide đã có*, không phải về pipeline sinh slide.

---

### `DOC2PPT: Automatic Presentation Slides Generation from Scientific Documents`

- **Authors:** Tsu-Jui Fu, William Yang Wang, Daniel McDuff, Yale Song
- **Year:** 2021 (arXiv); AAAI 2022, 36(1):634–642
- **Venue / Source:** AAAI 2022
- **DOI / URL:** https://arxiv.org/abs/2101.11796 · https://ojs.aaai.org/index.php/AAAI/article/view/19943
- **Referenced from:** RQ01 (SHOULD)
- **Research area:** Presentation generation · Document understanding · Evaluation (lịch sử)

**Paper nói gì?**
Bài đặt ra task document→slides như một bài toán học máy có dataset (~6K cặp document–deck), tự thừa nhận *"no established evaluation metrics and baselines"* nên tự đề xuất **ROUGE-SL** (ROUGE ở slide level) và **mIoU** (so layout với slide gốc) cộng human evaluation.

**Tại sao paper này xuất hiện trong project?**
Vai trò **mốc lịch sử**: nó đại diện cho thế hệ metric reference-based đầu tiên mà PPTAgent §5.5 sau đó chứng minh là proxy kém. RQ01 giữ nó để có thể nói "không dùng ROUGE-SL/mIoU" **kèm lý do có evidence**, thay vì loại bỏ theo cảm tính.

---

### `Enhancing Presentation Slide Generation by LLMs with a Multi-Staged End-to-End Approach` (DocPres)

- **Authors:** Sambaran Bandyopadhyay, Himanshu Maheshwari, Anandhavelu Natarajan, Apoorv Saxena
- **Year:** 2024
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2406.06556
- **Referenced from:** RQ01 (DISCOVERY), RQ02 (DISCOVERY)
- **Research area:** Presentation generation · Evaluation

**Paper nói gì?**
Một pipeline document→slide nhiều tầng, với evaluation riêng gồm **Coverage đo bằng cosine similarity của embedding ở 2 mức** (paragraph↔slide và sentence↔bullet), PPL bằng GPT-2, LLM-Eval bằng Mistral-7B, và human trên 6 chiều.

**Tại sao paper này xuất hiện trong project?**
Hai vai trò ngược nhau. RQ08 ghi nhận nó là **baseline mà PPTAgent so sánh** (tức prior published method, không phải naive LLM). RQ02 dùng nó làm **negative evidence**: ngay một paper document→slide chuyên biệt cũng không dùng metric NLI/atomic-fact, và "coverage" kiểu embedding similarity **không** phân biệt được coverage gap — deck lặp chữ của source vẫn ăn điểm cao. Repo cũng cảnh báo rõ: paper có nhắc việc gắn slide vào section giúp giảm hallucination nhưng **không có phép đo nào cô lập tác dụng đó** → không được trích như bằng chứng cho `source_ref`.

---

### `SlideTailor: Personalized Presentation Slide Generation for Scientific Papers`

- **Authors:** Wenzheng Zeng, Mingyu Ouyang, Langyuan Cui, Hwee Tou Ng
- **Year:** 2025 (arXiv, nộp 2025-12-23)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2512.20292 · https://arxiv.org/html/2512.20292v1
- **Referenced from:** RQ01 (DISCOVERY), RQ03 (SHOULD)
- **Research area:** Conditioning / personalization · Planning · Evaluation

**Paper nói gì?**
Conditioning việc sinh deck bằng **deck mẫu của chính user** thay vì bằng nhãn rời rạc, và tách metric thành **preference-based** (Coverage = IoU tập subtopic; **Flow = 1 − NGLD trên chuỗi thứ tự subtopic**; Content Structure LLM-judged cố tình bỏ qua chủ đề; Aesthetic MLLM-judged) vs **preference-independent**.

**Tại sao paper này xuất hiện trong project?**
Ba thứ chuyển được sang Deck Agent: (a) **Flow = 1−NGLD trên thứ tự chủ đề** là metric structure deterministic cụ thể nhất trong toàn bộ landscape, dùng được ngay trên `source_ref` của Deck IR; (b) cách tách preference-based / preference-independent ánh xạ sạch sang "metric nào phụ thuộc `slide_type`, metric nào không"; (c) **chain-of-speech** (sinh slide + lời nói kèm) là precedent gần nhất cho `speaker_notes` — và việc chính họ **không evaluate** phần lời nói là tín hiệu rằng đây là vùng chưa ai giải quyết.

---

### `MemSlides`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-06
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2606.17162
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Presentation generation · Iterative refinement / editing

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Personalized slide generation kèm **multi-turn local revision** (sửa cục bộ qua nhiều lượt).

**Tại sao paper này xuất hiện trong project?**
Repo ghi lại vì multi-turn local revision là thứ gần nhất với **Editor Loop (FR-14–17)** của Deck Agent — sửa một phần deck mà không phá phần còn lại. Chưa đọc, chưa dùng làm evidence cho recommendation nào.

---

### `DeepSlide: From Artifacts to Presentation Delivery`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-05
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/html/2605.15202v1
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Presentation generation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Không xác định rõ từ evidence hiện tại; repo chỉ ghi tên và đánh dấu "chỉ discovery".

**Tại sao paper này xuất hiện trong project?**
Không xác định rõ từ evidence hiện tại — được ghi lại trong bảng discovery của RQ01 để định vị landscape, không gắn với assumption nào.

---

### `OmniPresent: Generating Coherent Presentation Suites from Scientific Papers`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-07
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/html/2607.02590v1
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Presentation generation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Sinh **bộ** presentation nhất quán (suite) từ paper khoa học — tức đơn vị output lớn hơn một deck đơn lẻ.

**Tại sao paper này xuất hiện trong project?**
Không xác định rõ từ evidence hiện tại — chỉ nằm trong bảng discovery.

---

### `PaperX: A Unified Framework for Multimodal Academic Presentation Generation with Scholar DAG`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-02
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2602.03866
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Presentation generation · Planning (graph-based) · Multimodal

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Framework sinh presentation học thuật multimodal dựa trên một biểu diễn **DAG** của nội dung paper.

**Tại sao paper này xuất hiện trong project?**
Không xác định rõ từ evidence hiện tại — chỉ discovery. (Ghi chú của tôi cho deliverable 2: tên gọi "Scholar DAG" gợi ý một biểu diễn phụ thuộc giữa các ý, liên quan tới negative finding #4 của RQ03 về prerequisite-ordering — nhưng repo **chưa đọc** nên đây là suy đoán, không phải evidence.)

---

### `Presenting a Paper is an Art: Self-Improvement Aesthetic Agents for Academic Presentations` (EvoPresent)

- **Authors:** Không ghi đầy đủ trong evidence
- **Year:** 2025-10 (arXiv); repo tự ghi **ICLR 2026**
- **Venue / Source:** arXiv preprint + repo `UCSB-AI/EvoPresent`
- **DOI / URL:** https://arxiv.org/pdf/2510.05571 · https://github.com/UCSB-AI/EvoPresent · https://evopresent.github.io/
- **Referenced from:** RQ01 (DISCOVERY), RQ08 (DISCOVERY)
- **Research area:** Agent architecture · Self-critique · Visual design

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc paper]` Một agent tự cải thiện thẩm mỹ presentation qua **Checker Agent** dựa trên model RL đa nhiệm `PresAesth` để phê bình và sửa draft.

**Tại sao paper này xuất hiện trong project?**
RQ08 ghi nhận nó là **inspiration cho self-critique loop (FR-14–17)**, không phải baseline — vì bài toán hẹp hơn (academic paper → presentation). Chưa đọc paper nên không dùng làm evidence.

---

### `OutlineSpark`

- **Authors:** Không ghi trong evidence
- **Year:** 2024-03
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2403.09121
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Planning · Presentation generation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Sinh slide theo hướng **outline-driven** từ computational notebook.

**Tại sao paper này xuất hiện trong project?**
Repo ghi lại vì liên quan **FR-07 (plan/outline)** — tức khâu tạo cấu trúc trung gian trước khi sinh slide. Chưa đọc.

---

### `AI-Generated Slides: Are They Good? Can Students Tell?`

- **Authors:** Leinonen, Zhang, Hellas
- **Year:** 2026-05-13
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/html/2605.13532v1
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Evaluation (human study) · Education

**Paper nói gì?**
Một human study thật trên slide do AI sinh, chấm theo 3 tiêu chí **Factual accuracy / Completeness / Pedagogical soundness**: instructor vẫn phải sửa styling, xoá slide, chỉnh font code (mô tả **định tính**, không lượng hoá edit effort), còn **sinh viên không phân biệt được** slide AI với slide người (median 5.0/7.0).

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn gần nhất repo tìm được cho hai thứ Deck Agent quan tâm: khái niệm **"ready-to-use"** hiểu theo nghĩa *người dùng còn phải sửa bao nhiêu*, và `slide_type = teaching`. Nhưng chính vì nó **chỉ mô tả định tính**, RQ01 dùng nó để chứng minh negative finding #3: **không tồn tại metric external nào lượng hoá edit effort trên deck sinh tự động**.

---

### `Slide Deck Q&A Quality Assurance App: A Multi-Stage Pipeline for Pedagogical Question Generation`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-05
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2605.26428
- **Referenced from:** RQ03 (DISCOVERY)
- **Research area:** Evaluation · Education · Question generation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Có bộ metric **Coverage / Fidelity / Scaffolding** (scaffolding = "logical progression and pedagogical relevance") chấm thang 1–5, nhưng scaffolding ở đây là của **câu hỏi sinh ra**, không phải của slide.

**Tại sao paper này xuất hiện trong project?**
RQ03 ghi lại vì "Scaffolding" là tên gọi gần nhất cho thuộc tính mà `slide_type = teaching` cần (tiến trình sư phạm). Repo ghi rõ **transfer chưa rõ** vì đối tượng chấm khác nhau.

---

### `CourseBlueprint: A Structured Pipeline for Adaptive Pedagogical Video Generation Grounded in Course Corpora`

- **Authors:** Md Zabirul Islam, Md Motaleb Hossen Manik, Ge Wang
- **Year:** 2026 (arXiv 2026-06-23)
- **Venue / Source:** arXiv preprint (nhóm nhỏ — repo đánh giá authority thấp)
- **DOI / URL:** https://arxiv.org/pdf/2606.20608
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Conditioning · Education · Evaluation

**Paper nói gì?**
Conditioning nội dung theo **trình độ người học** qua adaptive prompting, với bộ metric trộn 1 deterministic (Flesch-Kincaid) và 5 LLM-judge, trong đó 3 metric được đánh dấu **level-aware** (phụ thuộc audience) — và đáng chú ý nhất: việc kiểm "khái niệm được giới thiệu đúng thứ tự phụ thuộc" vẫn được giao cho **LLM judge**, không phải kiểm bằng graph.

**Tại sao paper này xuất hiện trong project?**
Hai thứ: (a) precedent cho việc **một số metric là level-aware, một số thì không** — trùng cách tách của SlideTailor, ánh xạ sang "metric nào phụ thuộc `slide_type`"; (b) **negative finding hữu ích** — ngay cả khi có dependency structure từ source, chưa ai kiểm prerequisite-ordering bằng phương pháp deterministic. RQ03 cũng cảnh báo Flesch-Kincaid **không hợp lệ cho tiếng Việt**, nên nhánh `meta.language = "vi"` của Deck Agent không mượn được metric này.

---

### `A Good Talk Does not Look Like a Summary, It Teaches You! Measuring Takeaways from Paper-to-Video Talks` (EffectivePresentationScorer)

- **Authors:** Ishani Mondal, Aparna Garimella, Ananya Sai, Pannaga Shivaswamy, Jordan Boyd-Graber
- **Year:** 2026 (arXiv 2026-06-26)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2606.28531
- **Referenced from:** RQ03 (MUST)
- **Research area:** Evaluation · Education · Presentation (video)

**Paper nói gì?**
Metric hiện tại chỉ đo *"whether key points from the paper appear in the video"* chứ không đo **liệu người xem có hiểu ra không**, nên paper đề xuất chấm việc video có **giải thích khái niệm nền (prerequisite)** và nối chi tiết kỹ thuật với đóng góp chính hay không — và phát hiện hệ thống hiện tại *"mention the correct topics and follow the structure of the paper but fail to explain prerequisite concepts or clarify why the method works"*.

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn external **duy nhất** phát biểu tường minh rằng "chế độ giảng dạy" khác "chế độ tóm tắt" ở chỗ **giải thích prerequisite** — chính là trục `teaching` vs `catchup` của FR-02. Đồng thời nó là cảnh báo: thuộc tính đó **không đo được bằng coverage**, vì hệ thống fail đúng ở chỗ coverage không nhìn thấy. Cần thích nghi: artifact là video, không phải deck.

---

# B. Poster / adjacent artifact generation

### `Paper2Poster: Towards Multimodal Poster Automation from Scientific Papers`

- **Authors:** Wei Pang, Kevin Qinghong Lin, Xiangru Jian, Xi He, Philip Torr
- **Year:** 2025 (arXiv 2025-05-27, revised 2025-10-30)
- **Venue / Source:** NeurIPS 2025 Datasets & Benchmarks Track
- **DOI / URL:** https://arxiv.org/abs/2505.21497 · https://paper2poster.github.io/ · https://openreview.net/forum?id=p0E74lpRBD
- **Referenced from:** RQ01 (SHOULD)
- **Research area:** Multimodal generation · Evaluation · Document understanding

**Paper nói gì?**
Đưa ra 4 chiều đánh giá poster tự động, trong đó **PaperQuiz** là chiều khác hẳn phần còn lại: sinh câu hỏi trắc nghiệm từ paper gốc, rồi cho VLM đóng vai nhiều mức chuyên môn **chỉ đọc poster** và trả lời — tức đo **hiệu quả truyền đạt thông tin**, không đo hình thức.

**Tại sao paper này xuất hiện trong project?**
PaperQuiz là mô hình **extrinsic evaluation** chuyển được rất tốt sang Deck Agent, đặc biệt hợp `slide_type = teaching | catchup` (deck tốt = deck khiến người đọc trả lời đúng). Phần Visual Quality kiểu poster thì **không** chuyển được (poster = 1 canvas, deck = chuỗi slide). ⚠ Repo không đọc được full PDF; breakdown 6 tiêu chí Holistic chỉ lấy từ search snippet, **chưa quote-verified**.

---

### `PosterForest`

- **Authors:** Không ghi trong evidence
- **Year:** 2025-08
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2508.21720
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Multimodal generation · Agent architecture

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Sinh poster bằng kiến trúc **multi-agent**.

**Tại sao paper này xuất hiện trong project?**
Không xác định rõ từ evidence hiện tại — repo ghi "chỉ discovery".

---

### `Any2Poster`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-06
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2606.02915
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Multimodal generation · Input adaptation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Sinh poster từ **nhiều loại modality nguồn** khác nhau.

**Tại sao paper này xuất hiện trong project?**
Không xác định rõ từ evidence hiện tại — chỉ discovery. (Liên quan gián tiếp tới FR-18 "nhiều loại input", nhưng repo không phát biểu điều đó.)

---

### `EfficientPosterGen` (có phần "Accurate Violation Detection")

- **Authors:** Không ghi trong evidence
- **Year:** 2026-03
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2603.00155
- **Referenced from:** RQ01 (DISCOVERY)
- **Research area:** Layout · Rendering · Evaluation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Sinh poster hiệu quả kèm một cơ chế **"violation detection"** cho lỗi layout.

**Tại sao paper này xuất hiện trong project?**
RQ01 ghi lại vì "violation detection" nghe như **kiểm layout deterministic** — nếu đúng thì nó **củng cố AeSlides**. Chưa đọc nên chưa dùng.

---

# C. Factuality / coverage / summarization evaluation

> Nhóm này đông nhất (26 paper) vì RQ02 phải trả lời câu hỏi "làm sao biết deck có bịa, có nói sai, có bỏ sót". Với người xây agent, đây đồng thời là **giáo trình về cách định nghĩa đơn vị nội dung** — xem Stage 2 của roadmap.

### `FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation`

- **Authors:** Sewon Min, Kalpesh Krishna, Xinxi Lyu, Mike Lewis, Wen-tau Yih, Pang Wei Koh, Mohit Iyyer, Luke Zettlemoyer, Hannaneh Hajishirzi
- **Year:** 2023
- **Venue / Source:** EMNLP 2023 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2305.14251 · https://github.com/shmsw25/FActScore
- **Referenced from:** RQ06 (SHOULD), RQ02 (tham chiếu lại)
- **Research area:** Evaluation · Factuality

**Paper nói gì?**
Phân rã một đoạn sinh dài thành **atomic fact** (mỗi mệnh đề chứa đúng một thông tin), gán nhãn nhị phân support/không-support theo một knowledge source, và lấy điểm = % atomic fact được support — với estimator tự động sai lệch < 2% so với bản do người chấm.

**Tại sao paper này xuất hiện trong project?**
RQ06 xếp nó vào family F2c và coi đây là **family mà §8 và NFR bỏ sót hoàn toàn**: cùng nguyên lý với checklist per-instance (phân rã thành mệnh đề nhị phân verify được) nhưng checklist **sinh tự động từ output** nên chi phí authoring ≈ 0 → ứng viên cho content fidelity chạy hằng ngày. RQ02 sau đó dùng chính FActScore làm **ví dụ phản chứng** (xem entry *All Claims Are Equal*): nó mù với coverage gap.

---

### `All Claims Are Equal, but Some Claims Are More Equal Than Others: Importance-Sensitive Factuality Evaluation of LLM Generations`

- **Authors:** Miriam Wanner, Leif Azzopardi, Paul Thomas, Soham Dan, Benjamin Van Durme, Nick Craswell
- **Year:** 2025 (arXiv 2025-10)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2510.07083 · https://arxiv.org/html/2510.07083v1
- **Referenced from:** RQ02 (MUST)
- **Research area:** Evaluation · Factuality · Salience

**Paper nói gì?**
Metric factuality **chỉ đo precision thì mù với việc bỏ sót**: bảng số của paper cho thấy response thiếu hẳn câu trả lời chính lại đạt FActScore **cao hơn** response đúng-và-đủ (82,75 vs 82,58), và chỉ khi gán **trọng số tầm quan trọng** (vital / okay / less-important) cho từng subclaim thì khoảng cách "đúng" vs "sai" mới giãn từ ~6 điểm lên 24 điểm.

**Tại sao paper này xuất hiện trong project?**
Đây là **bằng chứng số quan trọng nhất của cả RQ02**: nó chứng minh bằng con số rằng một metric chạy một chiều output→source **không thể** dùng để trả lời "deck có bỏ sót ý quan trọng không". Hệ quả thiết kế: Deck Agent cần một chiều đo thứ hai (source→output) và một cơ chế **salience**. Cảnh báo transfer: "importance" của họ neo vào **query**, còn Deck Agent phải neo vào **source PDF + slide_type** — cơ chế phải thiết kế lại.

---

### `FineSurE: Fine-grained Summarization Evaluation using LLMs`

- **Authors:** Hwanjun Song, Hang Su, Igor Shalyminov, Jason Cai, Saab Mansour
- **Year:** 2024
- **Venue / Source:** ACL 2024 (Long Papers, peer-reviewed)
- **DOI / URL:** https://aclanthology.org/2024.acl-long.51/ · https://arxiv.org/abs/2407.00908 · https://github.com/DISL-Lab/FineSurE-ACL24
- **Referenced from:** RQ02 (MUST)
- **Research area:** Evaluation · Factuality · Coverage

**Paper nói gì?**
Tách đánh giá thành **hai task chạy ngược chiều nhau** — *fact checking* (output→source, phân loại mỗi câu vào 7 loại lỗi + no error) và *keyfact alignment* (source/keyfact→output, mỗi keyfact có được suy ra từ output không và ở dòng nào) — rồi tính ba số riêng biệt Faithfulness / Completeness / Conciseness; kết quả cho thấy rubric chung kiểu G-Eval gần như **không đo được completeness** (Pearson 0,314 so với 0,688 của keyfact alignment).

**Tại sao paper này xuất hiện trong project?**
Đây là **nguồn duy nhất trong repo có đúng cấu trúc 3 failure mà RQ02 cần** (bịa / sai / thiếu), và cấu trúc 2-pass ngược chiều là thứ chuyển giao được nguyên vẹn sang Deck Agent. Hai cảnh báo repo ghi rõ: (a) keyfact của họ đến từ **reference summary**, Deck Agent không có gold deck nên phải lấy keyfact từ **source PDF** — thay đổi thật, chưa ai validate; (b) GPT-4 detect lỗi nhị phân đạt 86,4% nhưng **localize loại lỗi chỉ 42,2%** → đừng kỳ vọng evaluator phân loại đúng *loại* lỗi.

---

### `MiniCheck: Efficient Fact-Checking of LLMs on Grounding Documents`

- **Authors:** Liyan Tang, Philippe Laban, Greg Durrett
- **Year:** 2024
- **Venue / Source:** EMNLP 2024 (peer-reviewed)
- **DOI / URL:** https://aclanthology.org/2024.emnlp-main.499/ · https://arxiv.org/abs/2404.10774 · https://github.com/Liyan06/MiniCheck
- **Referenced from:** RQ02 (MUST)
- **Research area:** Factuality · Grounding · Efficiency

**Paper nói gì?**
Một model nhỏ (từ ~770M tới 7B) được train trên dữ liệu synthetic dạy nó *"check each fact in the claim and recognize synthesis of information across sentences"* đạt mức GPT-4 về fact-checking claim với **chi phí thấp hơn ~400×**, kèm benchmark hợp nhất LLM-AggreFact (11 dataset, ~29K example).

**Tại sao paper này xuất hiện trong project?**
Nó giải **đúng** bài toán của Deck Agent (claim có được grounding document chống đỡ không) ở mức chi phí chạy được hằng ngày — repo ước tính ~16 claim/giây trên A6000, tức vài giây cho một deck 40 bullet, API cost = 0. Điểm hợp nhất: dữ liệu train của họ nhắm vào **claim tổng hợp từ nhiều câu nguồn**, đúng hình dạng một bullet. Nhưng repo cũng ghi rõ nó **không** đo coverage, **không** phân biệt bịa với sai, vẫn game được và vẫn vấp negation.

---

### `Stress Testing Factual Consistency Metrics for Long-Document Summarization`

- **Authors:** Zain Muhammad Mujahid, Dustin Wright, Isabelle Augenstein
- **Year:** 2025 (arXiv; ⚠ repo ghi trang fetch trả về ngày mâu thuẫn — không trích ngày cụ thể)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2511.07689 · https://arxiv.org/html/2511.07689v2
- **Referenced from:** RQ02 (MUST)
- **Research area:** Evaluation · Robustness

**Paper nói gì?**
Stress-test 6 metric reference-free dưới 7 phép biến đổi giữ nguyên nghĩa và tìm ra thứ hạng độ bền — **MiniCheck và UniEval tương đối ổn định**, **AlignScore và SummaC-ZS là nhóm rủi ro cao** — cùng phát hiện then chốt rằng mọi metric **kém đi khi claim bị nén, mật độ thông tin cao**, và *"no metric consistently maintains factual alignment under long-context conditions"*.

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn quyết định câu hỏi G-05 ("F2 nào thực sự dùng được"): nó là lý do RQ02 **không** implement AlignScore chỉ vì Wave 1 nêu tên, và là lý do MiniCheck được ưu tiên. Quan trọng hơn cho Deck Agent: **bullet trên slide về bản chất là claim bị nén** — tức deck rơi đúng vào vùng metric yếu nhất.

---

### `Do Automatic Factuality Metrics Measure Factuality? A Critical Evaluation`

- **Authors:** Sanjana Ramprasad, Byron C. Wallace
- **Year:** 2024 (arXiv 2024-11-25, bản mới 2025-11-05) — ⚠ repo thấy trang poster NeurIPS 2025 nhưng **chưa fetch xác nhận venue**
- **Venue / Source:** arXiv preprint (khả năng đã peer-review, chưa xác nhận)
- **DOI / URL:** https://arxiv.org/abs/2411.16638 · https://arxiv.org/html/2411.16638v1
- **Referenced from:** RQ02 (MUST)
- **Research area:** Evaluation · Metric criticism

**Paper nói gì?**
Một MLP chỉ dùng **feature bề mặt** cạnh tranh được với các metric factuality SOTA, và các metric này **game được**: chèn một câu ngẫu nhiên lấy từ source làm điểm tăng bằng hoặc hơn mức tăng do sửa lỗi thật, với chiến lược gaming cho cải thiện **gấp 3–8 lần** so với cải thiện thật.

**Tại sao paper này xuất hiện trong project?**
Repo rút ra một failure mode cụ thể và nguy hiểm cho Deck Agent: một deck **dày chữ, copy nguyên văn nhiều câu từ PDF** sẽ ăn điểm fidelity cao, trong khi FR-22/ready-to-use coi đó là lỗi — tức **metric fidelity có thể thưởng cho đúng cái sản phẩm coi là hỏng**. Đây cũng là evidence cho việc phải đo noise/gaming của evaluator (G-09).

---

### `AlignScore: Evaluating Factual Consistency with a Unified Alignment Function`

- **Authors:** Yuheng Zha và cộng sự (repo `yuh-zha/AlignScore`)
- **Year:** 2023
- **Venue / Source:** ACL 2023 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2305.16739 · https://github.com/yuh-zha/AlignScore
- **Referenced from:** RQ06 (SHOULD, family F2b), RQ02 (đối chiếu ở §1.3/§1.4)
- **Research area:** Factuality · NLI / alignment

**Paper nói gì?**
Một **alignment function thống nhất**, train trên ~4,7M example gộp từ 7 task (NLI, QA, paraphrase, fact verification, IR, STS, summarization), dùng làm metric factual consistency và đạt SOTA trên benchmark SummaC và TRUE. ⚠ Repo ghi con số 4,7M **chưa verify từ PDF gốc**.

**Tại sao paper này xuất hiện trong project?**
RQ06 đưa vào như candidate cho family F2b (NLI/alignment-based fidelity). Nhưng RQ02 sau đó **hạ cấp** nó: §1.3 xếp AlignScore vào nhóm **rủi ro cao** dưới stress test, trong khi §1.4 lại cho nó điểm tốt về độ nhạy với sửa đúng → repo kết luận **hai nguồn không hoàn toàn cùng chiều**, giữ ở mức "trung bình". Giới hạn chung: chỉ áp dụng cho quan hệ text↔text, **mù hoàn toàn với layout/visual**.

---

### `SummaC: Re-Visiting NLI-based Models for Inconsistency Detection in Summarization`

- **Authors:** Philippe Laban và cộng sự
- **Year:** 2022
- **Venue / Source:** TACL (peer-reviewed)
- **DOI / URL:** https://aclanthology.org/2022.tacl-1.10/
- **Referenced from:** RQ06 (SHOULD, family F2b), RQ02 (đối chiếu)
- **Research area:** Factuality · NLI

**Paper nói gì?**
Chia document thành đơn vị câu rồi aggregate điểm NLI theo cặp câu (`SummaC-Conv`), qua đó operationalize "consistency" thành mệnh đề kiểm được: **mỗi câu của summary phải được source entail và không bị mâu thuẫn** — kèm benchmark 6 dataset inconsistency detection.

**Tại sao paper này xuất hiện trong project?**
Cùng vai trò với AlignScore: candidate F2b và là **định nghĩa hình thức** của "không nói sai". RQ02 ghi nhận SummaC-ZS thuộc nhóm rủi ro cao dưới stress test, còn SummaC-Conv thì "nhạy với sửa vô hại hơn là với sửa đúng" — tức không phải lựa chọn mặc định tốt.

---

### `AbsenceBench: Language Models Can't Tell What's Missing`

- **Authors:** Harvey Yiyun Fu, Aryan Shrivastava, Jared Moore, Peter West, Chenhao Tan, Ari Holtzman
- **Year:** 2025 (arXiv 2025-06-13); repo thấy trang poster NeurIPS 2025 D&B nhưng **chưa fetch**
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2506.11440
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · LLM capability limits

**Paper nói gì?**
Model rất giỏi nhớ ra thông tin bất ngờ nhưng **rất kém chỉ ra thông tin bị bỏ đi** — ngay cả model mạnh chỉ đạt ~69,6% F1 với context ~5K token — và giải thích cơ chế: attention **không có "key" nào để attend vào một khoảng trống**.

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn thứ hai, **độc lập về phương pháp** với FineSurE, cùng chỉ một hướng: **không được giao coverage cho một prompt kiểu "deck này có bỏ sót ý quan trọng nào không?"**. Phải chuyển thành bài toán đối chiếu từng item trong một **danh sách đóng**. Repo nhấn mạnh setup của họ còn *dễ hơn* Deck Agent (so 2 văn bản, phần thiếu khớp chính xác) nên 69,6% phải đọc là **cận trên lạc quan**.

---

### `Comprehensiveness Metrics for Automatic Evaluation of Factual Recall in Text Generation`

- **Authors:** Adam Dejl, James Barry, Alessandra Pascale, Javier Carnerero Cano
- **Year:** 2025 (arXiv 2025-10-09, revised 2026-05-07); trang arXiv ghi ACL 2026 Findings — ⚠ chưa đối chiếu ACL Anthology
- **Venue / Source:** arXiv preprint (venue chưa verify độc lập)
- **DOI / URL:** https://arxiv.org/abs/2510.07926
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · Coverage

**Paper nói gì?**
So sánh trực tiếp **3 cách đo coverage** — NLI-based (phân rã atomic + tìm fact thiếu), Q&A-based (trích cặp hỏi–đáp rồi so câu trả lời), và end-to-end (hỏi thẳng LLM chỗ nào thiếu) — và thấy metric **end-to-end đơn giản lại hiệu quả bất ngờ**, đổi lại kém về robustness và interpretability.

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn duy nhất so head-to-head đúng 3 nhánh mà G-06 cần chọn. Nhưng repo đánh dấu nó tạo **mâu thuẫn evidence chưa giải quyết**: kết luận "end-to-end hiệu quả" đi ngược AbsenceBench và FineSurE → **không** được dùng để biện minh cho việc hỏi thẳng LLM về coverage. ⚠ Chưa trích xuất được con số correlation cụ thể.

---

### `Molecular Facts: Desiderata for Decontextualization in LLM Fact Verification`

- **Authors:** Anisha Gunjal, Greg Durrett (UT Austin)
- **Year:** 2024
- **Venue / Source:** Findings of EMNLP 2024 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2406.20079 · https://aclanthology.org/2024.findings-emnlp.215/ · https://github.com/anisha2102/molecular_facts
- **Referenced from:** RQ02 (SHOULD) — ⚠ `[SEARCH-ONLY]`, repo chưa fetch bản gốc
- **Research area:** Factuality · Representation of claims

**Paper nói gì?**
Đặt tên cho một tension cốt lõi: **đơn vị càng lớn càng khó fact-check, nhưng đơn vị càng atomic thì càng thiếu context để diễn giải đúng** — và đề xuất 2 tiêu chí cho một "molecular fact": **decontextuality** (đứng một mình hiểu được) và **minimality** (thêm càng ít thông tin càng tốt).

**Tại sao paper này xuất hiện trong project?**
Bullet trên slide (`"— Doanh thu +51% YoY"`) là **fragment cực đoan hơn cả atomic fact**: mất chủ ngữ, mất mốc thời gian, mất đơn vị. Nghĩa là bất kỳ evaluator textual nào của Deck Agent cũng phải giải bài toán decontextualization **trước khi** verify — và bước đó tự nó sinh lỗi. Repo dùng nguồn này để **đặt tên cho vấn đề**, không trích số (chưa fetch bản gốc).

---

### `QAFactEval: Improved QA-Based Factual Consistency Evaluation for Summarization`

- **Authors:** Alexander R. Fabbri, Chien-Sheng Wu, Wenhao Liu, Caiming Xiong
- **Year:** 2021/2022
- **Venue / Source:** NAACL 2022 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2112.08542
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Factuality · QA-based evaluation

**Paper nói gì?**
Khi xây metric QA-based thì **chọn đúng component mới là thứ quyết định** (đặc biệt question generation và answerability classification) — làm đúng thì vượt cả metric entailment-based tốt nhất, và quan trọng nhất: QA-based với entailment-based cho **tín hiệu bổ sung nhau**, gộp lại còn tốt hơn.

**Tại sao paper này xuất hiện trong project?**
Là đại diện cho nhánh question/probe-based mà Deck Agent có thể dùng (cùng họ với PaperQuiz và QuizBank). Repo cảnh báo hai chiều: (a) **không có số về chi phí** nên không được claim QA-based rẻ; (b) đối chiếu với §1.4, QuestEval (thế hệ trước) là metric **không phản ứng** với việc sửa đúng → không mặc định QA-based là an toàn.

---

### `VeriFastScore: Speeding up long-form factuality evaluation`

- **Authors:** Rishanth Rajendhran, Amir Zadeh, Matthew Sarte, Chuan Li, Mohit Iyyer
- **Year:** 2025
- **Venue / Source:** Findings of EMNLP 2025 (peer-reviewed)
- **DOI / URL:** https://aclanthology.org/2025.findings-emnlp.491/ · https://arxiv.org/abs/2505.16973
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Factuality · Efficiency

**Paper nói gì?**
Fine-tune một model 8B để **đồng thời trích và verify** mọi claim trong một lượt thay vì phân rã tuần tự rồi verify từng cái, đạt speedup 6,6× (9,9× nếu trừ retrieval) mà vẫn giữ tương quan r = 0,80 ở mức example và **r = 0,94 ở mức system**.

**Tại sao paper này xuất hiện trong project?**
Hai giá trị: (a) bằng chứng E1 rằng **gộp decompose + verify** là hướng giảm chi phí có hiệu quả đo được; (b) con số system-level (0,94) cao hơn hẳn example-level (0,80) → INFERENCE của repo: một metric rẻ có thể **đủ tin để so version** dù chưa đủ tin để phán xét một deck cụ thể — đúng use case daily tracking của Deck Agent.

---

### `EQUATE: A Benchmark Evaluation Framework for Quantitative Reasoning in Natural Language Inference`

- **Authors:** Abhilasha Ravichander, Aakanksha Naik, Carolyn Rose, Eduard Hovy
- **Year:** 2019
- **Venue / Source:** CoNLL 2019 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/1901.03735 · https://aclanthology.org/K19-1033/
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** NLI · Quantitative reasoning

**Paper nói gì?**
Benchmark 9 NLI model đã công bố trên 5 tập suy luận định lượng và thấy *"on average, state-of-the-art methods do not achieve an absolute improvement over a majority-class baseline"* — tức **model học entailment không tự nhiên học được suy luận với con số**, trong khi một baseline thao tác ký hiệu trên đại lượng vượt +24,2% ở nhóm numerical.

**Tại sao paper này xuất hiện trong project?**
Là nguồn duy nhất nói trực tiếp về cơ chế dưới case "bullet bị tính toán lại" (ví dụ `~51% YoY`). Điều nó support đúng mức là **lý do kiến trúc**: tách phép kiểm số ra khỏi phép kiểm entailment. Repo cảnh báo rất mạnh: paper **2019**, model thế hệ trước → **không được** kết luận "AlignScore/MiniCheck không làm được số học" từ nguồn này; chưa ai đo lại trên model 2024–2026.

---

### `On Positional Bias of Faithfulness for Long-form Summarization`

- **Authors:** David Wan, Jesse Vig, Mohit Bansal, Shafiq Joty
- **Year:** 2024/2025 (arXiv 2024-10-31, revised 2025-07-06; trang ghi NAACL 2025)
- **Venue / Source:** arXiv preprint / NAACL 2025 (repo chưa đối chiếu)
- **DOI / URL:** https://arxiv.org/abs/2410.23609
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · Long-context behavior

**Paper nói gì?**
Faithfulness theo vị trí trong tài liệu có dạng **hình chữ U** — model tốt ở đầu và cuối, **kém ở giữa** — và các LLM-based faithfulness metric *"remain sensitive to document order, indicating positional bias"*.

**Tại sao paper này xuất hiện trong project?**
PDF nguồn của Deck Agent dài. Nếu evaluator nhét cả PDF vào một prompt thì **ý nằm giữa tài liệu bị đo kém hơn** — một confounder không liên quan gì tới chất lượng ContentPlanner. Đây là lập luận ủng hộ **chia nhỏ + đối chiếu theo item** thay vì một prompt lớn.

---

### `Revisiting the Gold Standard: Grounding Summarization Evaluation with Robust Human Evaluation` (RoSE / ACU)

- **Authors:** Không ghi đầy đủ trong evidence (repo `Yale-LILY/ROSE`)
- **Year:** 2023
- **Venue / Source:** ACL 2023 (peer-reviewed) — ⚠ `[SEARCH-ONLY]`, repo chưa fetch bản gốc
- **DOI / URL:** https://aclanthology.org/2023.acl-long.228/ · https://github.com/Yale-LILY/ROSE
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · Human annotation protocol · Coverage

**Paper nói gì?**
Định nghĩa **ACU (Atomic Content Unit)** làm đơn vị chấm coverage — annotator chuyển reference thành các mệnh đề atomic rồi đối chiếu output với chúng, điểm = `|A_s| / |A|` tức **thuần recall** — kèm bộ ~22.000 annotation trên 28 hệ thống.

**Tại sao paper này xuất hiện trong project?**
Đây là nơi định nghĩa **đơn vị** của coverage, thứ mà metadata `critical_facts` của benchmark Deck Agent sẽ phải tự định nghĩa. Cảnh báo transfer nặng: ACU neo vào **reference summary**, Deck Agent **không có gold deck** → chuyển sang "ACU trích từ source" làm mất tính chất "đây là ý người viết cho là quan trọng", tức **bài toán salience quay lại**.

---

### `QAPyramid: Fine-grained Evaluation of Content Selection for Text Summarization`

- **Authors:** Không ghi đầy đủ trong evidence (repo `ZhangShiyue/QAPyramid`)
- **Year:** 2024
- **Venue / Source:** arXiv preprint — ⚠ `[SEARCH-ONLY]`
- **DOI / URL:** https://arxiv.org/abs/2412.07096 · https://github.com/ZhangShiyue/QAPyramid
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · Coverage · Annotation granularity

**Paper nói gì?**
Thay vì để annotator tự chọn đơn vị nội dung, phân rã reference thành **cặp QA theo khung QA-SRL**, vì giao thức Pyramid gốc *"suffers from a lack of systematicity in the definition and granularity of the sub-units"*.

**Tại sao paper này xuất hiện trong project?**
Vấn đề "granularity của đơn vị không hệ thống" chính là vấn đề `critical_facts` của Deck Agent sẽ gặp: nếu mỗi người viết key fact ở một độ mịn khác nhau thì điểm coverage không so sánh được giữa các instance. Đây là nguồn đề xuất một cách **chuẩn hoá độ mịn**.

---

### `UniSumEval: Towards Unified, Fine-Grained, Multi-Dimensional Summarization Evaluation for LLMs`

- **Authors:** Yuho Lee, Taewon Yun, Jason Cai, Hang Su, Hwanjun Song
- **Year:** 2024
- **Venue / Source:** Findings of EMNLP 2024 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2409.19898
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · Benchmark design · Annotation

**Paper nói gì?**
Một benchmark có input scenario đa dạng và annotation fine-grained đa chiều, trong đó **AI được dùng để hỗ trợ annotator** — vừa phát hiện source text có vấn đề, vừa giảm độ khó của việc annotate fine-grained.

**Tại sao paper này xuất hiện trong project?**
Mô hình "AI hỗ trợ annotator" là **mô hình chi phí** đáng chú ý cho G-06 và cho việc dựng benchmark ở RQ07 với team 4 người. ⚠ Repo **không trích xuất được** danh sách dimension cụ thể hay số liệu → các dimension nghe được qua search (faithfulness/completeness/conciseness/abstractiveness/domain stability) **chưa verify**, không dùng làm evidence.

---

### `LongSumEval: Question-Answering Based Evaluation and Feedback-Driven Refinement for Long Document Summarization`

- **Authors:** Huyen Nguyen, Haoxuan Zhang, Yang Zhang, Junhua Ding, Haihua Chen
- **Year:** 2026 (arXiv v2)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2604.25130
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Evaluation · Coverage · Iterative refinement

**Paper nói gì?**
Sinh câu hỏi **từ chính source document** (không phải từ reference summary) rồi tách coverage khỏi faithfulness bằng cách trả lời câu hỏi trên cả summary lẫn source, và dùng kết quả làm feedback để refine.

**Tại sao paper này xuất hiện trong project?**
Repo ghi "**rất gần với thứ Deck Agent cần**" — probe sinh từ source, không cần gold deck. Nhưng ⚠ không trích xuất được correlation với người, baseline hay chi phí → **không dùng làm evidence**, cần đọc lại PDF.

---

### `SummEval: Re-evaluating Summarization Evaluation`

- **Authors:** Alexander R. Fabbri, Wojciech Kryściński, Bryan McCann, Caiming Xiong, Richard Socher, Dragomir Radev
- **Year:** 2021
- **Venue / Source:** TACL 9:391–409 (peer-reviewed)
- **DOI / URL:** https://aclanthology.org/2021.tacl-1.24/
- **Referenced from:** RQ06 (DISCOVERY)
- **Research area:** Evaluation · Meta-evaluation

**Paper nói gì?**
Re-evaluate **14 metric tự động** trên output model neural cùng annotation của cả expert lẫn crowd (23 hệ thống summarization), với mục tiêu tường minh *"advance research in developing evaluation metrics that better correlate with human judgments"*.

**Tại sao paper này xuất hiện trong project?**
Dùng để định vị: cộng đồng summarization đã **tự thừa nhận metric sẵn có chưa đủ**. ⚠ Repo cảnh báo rõ: **không trích xuất được con số correlation của ROUGE** → không được viết "ROUGE tương quan r = 0,x với người".

---

### `VeriFact`

- **Authors:** Không ghi trong evidence
- **Year:** 2025
- **Venue / Source:** EMNLP 2025 Main — ⚠ `[SEARCH-ONLY]`
- **DOI / URL:** https://aclanthology.org/2025.emnlp-main.905/
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Factuality · Decontextualization

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Xử lý **missing comparandum / omitted conditions** khi decontextualize một claim.

**Tại sao paper này xuất hiện trong project?**
Đúng dạng lỗi của bullet "51% YoY" (thiếu mốc so sánh: so với năm nào, trên chỉ số nào). Ghi lại để đọc tiếp, chưa dùng.

---

### `DnDScore`

- **Authors:** Không ghi trong evidence
- **Year:** 2025
- **Venue / Source:** EMNLP 2025 Main — ⚠ `[SEARCH-ONLY]`
- **DOI / URL:** https://aclanthology.org/2025.emnlp-main.1205.pdf
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Factuality · Decomposition

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Kết hợp **decontextualization + decomposition** trong đánh giá factuality.

**Tại sao paper này xuất hiện trong project?**
Cùng cụm vấn đề với Molecular Facts và VeriFact: làm sao tách claim thành đơn vị verify được mà không mất context. Chưa đọc.

---

### `SAFE / LongFact`

- **Authors:** Wei et al.
- **Year:** 2024
- **Venue / Source:** arXiv preprint — ⚠ `[SEARCH-ONLY]`
- **DOI / URL:** https://arxiv.org/abs/2403.18802
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Factuality · Long-form evaluation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Đề xuất **F1@K** gộp precision và recall của fact, nhưng phần recall neo vào **K = số fact "lý tưởng"** chứ không vào key fact của source.

**Tại sao paper này xuất hiện trong project?**
RQ02 ghi lại rồi **loại**: chính vì recall neo vào một con số K giả định chứ không vào danh sách ý quan trọng của tài liệu nguồn, nó không giải được bài toán coverage của Deck Agent. **Không dùng làm evidence.**

---

### `TriQua`

- **Authors:** Không ghi trong evidence
- **Year:** 2026
- **Venue / Source:** arXiv preprint — ⚠ `[SEARCH-ONLY]`
- **DOI / URL:** https://arxiv.org/abs/2608.05228
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Factuality · Granularity

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Về quan hệ **granularity vs context** trong đánh giá factuality.

**Tại sao paper này xuất hiện trong project?**
Cùng cụm với Molecular Facts (chọn độ mịn của đơn vị verify). Chưa đọc.

---

### `ACUEval`

- **Authors:** Không ghi trong evidence
- **Year:** Không ghi
- **Venue / Source:** ⚠ **chưa verify link gốc** (repo chỉ thấy qua ResearchGate)
- **DOI / URL:** Không có link gốc verify được
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Factuality · Coverage

**Paper nói gì?** `[mức repo ghi nhận — chưa verify]` Đánh giá hallucination dựa trên **ACU**.

**Tại sao paper này xuất hiện trong project?**
Không xác định rõ từ evidence hiện tại. Repo đánh dấu chưa verify link gốc → theo `EVIDENCE_POLICY`, **không được dùng làm evidence** cho tới khi xác nhận sự tồn tại.

---

### `QuanTemp / CheckThat! 2025 Task 3`

- **Authors:** Không ghi trong evidence
- **Year:** 2025
- **Venue / Source:** ⚠ **UNVERIFIED** — repo chưa fetch nguồn chính thức
- **DOI / URL:** Không có
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Fact verification · Quantitative/temporal claims

**Paper nói gì?** `[mức repo ghi nhận — chưa verify]` Verify claim mang tính **định lượng / thời gian**.

**Tại sao paper này xuất hiện trong project?**
Liên quan trực tiếp case "51% YoY" (claim có số + có mốc thời gian). Repo đánh dấu UNVERIFIED → không dùng.

---

# D. LLM & MLLM-as-a-judge

> Nhóm này trả lời "**dùng model để chấm model** thì tin được tới đâu". Với người xây agent, nó quan trọng gấp đôi: judge vừa là công cụ đo, vừa là **thành phần bên trong self-critique loop** — critique kém thì refinement loop tự làm hỏng deck.

### `Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena`

- **Authors:** Lianmin Zheng, Wei-Lin Chiang, Ying Sheng, Siyuan Zhuang, Zhanghao Wu, Yonghao Zhuang, Zi Lin, Zhuohan Li, Dacheng Li, Eric P. Xing, Hao Zhang, Joseph E. Gonzalez, Ion Stoica
- **Year:** 2023
- **Venue / Source:** NeurIPS 2023 Datasets & Benchmarks Track (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2306.05685
- **Referenced from:** RQ06 (MUST)
- **Research area:** Evaluation · LLM-as-judge

**Paper nói gì?**
Judge mạnh đạt **>80% agreement** với human preference (*"the same level of agreement between humans"*), nhưng cùng lúc đặt tên và đo **3 bias nền tảng** — position, verbosity, self-enhancement — cộng giới hạn về khả năng suy luận, với verbosity bias chứng minh bằng "repetitive list attack".

**Tại sao paper này xuất hiện trong project?**
Đây là **danh mục bias gốc** mà mọi thảo luận judge của repo dựa vào, và là **cận trên lạc quan** để hiệu chỉnh kỳ vọng. Repo cảnh báo rõ: con số >80% là trên chat assistant response với protocol pairwise, **không** phải trên deck/multimodal → không được suy ra Deck Agent sẽ đạt 80%.

---

### `MLLM-as-a-Judge: Assessing Multimodal LLM-as-a-Judge with Vision-Language Benchmark`

- **Authors:** Dongping Chen, Ruoxi Chen, Shilin Zhang, Yinuo Liu, Yaochen Wang, Huichi Zhou, Qihui Zhang, Yao Wan, Pan Zhou, Lichao Sun
- **Year:** 2024
- **Venue / Source:** ICML 2024 (Oral, peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2402.04788 · https://github.com/Dongping-Chen/MLLM-Judge
- **Referenced from:** RQ06 (MUST), RQ01 (DISCOVERY), RQ03 (ROUTED)
- **Research area:** Evaluation · Multimodal LLM

**Paper nói gì?**
So sánh trực tiếp 3 protocol judge cho **model multimodal** và kết luận nhất quán rằng MLLM thể hiện *"remarkable human-like discernment in **Pair Comparison**"* nhưng *"significant divergence from human preferences in **Scoring Evaluation** and **Batch Ranking**"*, kèm failure mode *"diverse biases, hallucinatory responses, and inconsistencies"* xảy ra kể cả với GPT-4V.

**Tại sao paper này xuất hiện trong project?**
Deck là artifact multimodal, nên đây là nguồn **trực tiếp về lựa chọn protocol**: khi buộc phải dùng judge cho `slide_type` hay design, nên dùng **forced-choice/so sánh** thay vì chấm điểm tuyệt đối. ⚠ Repo ghi con số % agreement **trích xuất không nhất quán giữa hai lần fetch** (79,3% vs 78%) → chỉ dùng kết luận định tính.

---

### `G-Eval: NLG Evaluation using GPT-4 with Better Human Alignment`

- **Authors:** Yang Liu et al.
- **Year:** 2023
- **Venue / Source:** EMNLP 2023 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2303.16634 · https://aclanthology.org/2023.emnlp-main.153/
- **Referenced from:** RQ06 (SHOULD), RQ02 (dùng làm baseline đối chiếu)
- **Research area:** Evaluation · LLM-as-judge

**Paper nói gì?**
Framework judge dạng **form-filling + chain-of-thought** đạt Spearman **0,514** với human trên summarization (vượt các method trước "by a large margin"), với CoT cải thiện tương quan trên mọi dimension.

**Tại sao paper này xuất hiện trong project?**
Hai vai trò trái ngược. RQ06 dùng 0,514 để **hiệu chỉnh kỳ vọng**: đối chiếu PresentBench 0,532 / trần người 0,664 → **ρ ≈ 0,5 là vùng hoạt động thực tế của LLM judge**, không phải 0,9. RQ02 thì dùng G-Eval làm **ví dụ về giới hạn**: cùng framework này chỉ đạt Pearson 0,314 khi phải đo completeness, so với 0,688 của keyfact alignment.

---

### `Aligning with Human Judgement: The Role of Pairwise Preference in LLM Evaluators` (PairS)

- **Authors:** Liu et al. (Cambridge LTL)
- **Year:** 2024 (arXiv v5 2025-01-17)
- **Venue / Source:** COLM 2024 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2403.16950 · https://github.com/cambridgeltl/PairS
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Ranking

**Paper nói gì?**
Đặt lại bài toán evaluation thành **ranking**: dùng LLM so sánh pairwise cục bộ rồi xếp hạng toàn cục bằng **uncertainty-guided search**, cho kết quả *"substantially outperforming traditional direct scoring evaluations"*.

**Tại sao paper này xuất hiện trong project?**
Củng cố hướng "pairwise đáng tin hơn pointwise". **Nhưng repo ghi rõ giới hạn quyết định**: pairwise **không cho điểm tuyệt đối** → không giải quyết được nhu cầu của mission ("so v0.2 với v0.9 mà không chạy lại v0.2"). Tức đây là evidence cho *độ tin cậy*, không phải cho *khả năng tracking theo thời gian*.

---

### `Large Language Models are not Fair Evaluators` (FairEval)

- **Authors:** Peiyi Wang, Lei Li, Liang Chen và cộng sự
- **Year:** 2023 (arXiv) / ACL 2024
- **Venue / Source:** ACL 2024 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2305.17926 · https://aclanthology.org/2024.acl-long.511 · https://github.com/i-Eval/FairEval
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Bias mitigation

**Paper nói gì?**
Xếp hạng có thể bị "hack" **chỉ bằng cách đổi thứ tự xuất hiện** của ứng viên, và paper đưa ra 2 mitigation có tên + có code: **MEC** (bắt model sinh evidence trước rồi mới chấm) và **BPC** (chạy cả hai thứ tự rồi cân bằng).

**Tại sao paper này xuất hiện trong project?**
Là mitigation cụ thể cho position bias nếu Deck Agent dùng protocol pairwise. Repo ghi rõ phạm vi: **chỉ áp dụng cho protocol so sánh**, không nói gì về pointwise binary checklist — nơi bias này về cơ bản không phát sinh.

---

### `LLM Evaluators Recognize and Favor Their Own Generations`

- **Authors:** Arjun Panickssery, Samuel R. Bowman, Shi Feng (⚠ repo lấy từ trang tổng hợp, chưa đối chiếu PDF gốc)
- **Year:** 2024
- **Venue / Source:** NeurIPS 2024 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2404.13076
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Self-preference bias

**Paper nói gì?**
LLM **nhận ra được output của chính mình** với độ chính xác trên ngẫu nhiên, và tồn tại **tương quan tuyến tính giữa năng lực tự-nhận-diện và độ mạnh self-preference** — tức bias này có một cơ chế nhân quả cụ thể, không phải hiện tượng ngẫu nhiên.

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn E1 **hậu thuẫn đúng cơ chế** mà §8 khuyến nghị mà không nêu cơ sở ("dùng model khác họ để generate và để judge"): nếu bias sinh ra từ tự-nhận-diện thì tách generator/judge sang họ khác tấn công trực diện vào nguyên nhân. Repo lưu ý: paper **không** chứng minh tách họ *loại bỏ* được bias.

---

### `Quantifying and Mitigating Self-Preference Bias of LLM Judges`

- **Authors:** Jinming Yang, Zheng Hu, Chuxian Qiu, Zhenyu Deng, Xinshan Jiao, Tao Zhou
- **Year:** 2026 (arXiv v1 2026-04-24, revised 2026-06-02)
- **Venue / Source:** arXiv preprint (chưa peer-review)
- **DOI / URL:** https://arxiv.org/abs/2604.22891
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Bias mitigation

**Paper nói gì?**
Lượng hoá self-preference bias **không cần human gold standard** (bằng cách dựng cặp response chất lượng ngang nhau), khảo sát 20 LLM và thấy *"advanced capabilities are often uncorrelated, or even negatively correlated, with low SPB"* — trong khi **structured multi-dimensional evaluation** giảm bias trung bình 31,5%.

**Tại sao paper này xuất hiện trong project?**
Đây là evidence rằng **cách hỏi** (phân rã thành nhiều chiều/item) là một lever giảm bias **độc lập với model nào hỏi** — bổ sung chứ không thay thế khuyến nghị "khác họ" của §8, và trùng hướng với cơ chế checklist của PresentBench. Cũng là một trong **ba nguồn độc lập** cùng chỉ ra "chọn model mạnh nhất không tự động tốt hơn".

---

### `JudgeSense: A Benchmark for Prompt Sensitivity in LLM-as-a-Judge Systems`

- **Authors:** Rohith Reddy Bellibatlu, Edward Raff, Wenbin Zhang
- **Year:** 2026 (arXiv 2026-04-26, revised 2026-05-07)
- **Venue / Source:** arXiv preprint (chưa peer-review)
- **DOI / URL:** https://arxiv.org/abs/2604.23478
- **Referenced from:** RQ06 (SHOULD), RQ03 (ROUTED)
- **Research area:** Evaluation · Robustness

**Paper nói gì?**
Verdict của judge **thay đổi dưới các cách diễn đạt prompt tương đương ngữ nghĩa**, và *"model scale is not a reliable proxy for consistency; the largest and newest models are not the most consistent"* — với **coherence là dimension bất ổn nhất**, factuality ổn định nhất.

**Tại sao paper này xuất hiện trong project?**
Hai hệ quả thực hành: (a) justify việc **đóng băng + version hoá judge prompt như một phần của benchmark**; (b) cảnh báo trực tiếp cho RQ03 — dimension kiểu "coherence/structure" (chính là Structure trong §8, và là trục của `slide_type`) nằm trong nhóm bất ổn nhất, nên **không** đặt judge làm trục chính cho FR-02.

---

### `Judging the Judges: A Systematic Evaluation of Bias Mitigation Strategies in LLM-as-a-Judge Pipelines`

- **Authors:** Sadman Kabir Soumik
- **Year:** 2026 (arXiv 2026-04-25, revised 2026-06-24)
- **Venue / Source:** arXiv preprint (chưa peer-review, **một tác giả**)
- **DOI / URL:** https://arxiv.org/abs/2604.23178
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Bias · Cost

**Paper nói gì?**
So sánh 9 chiến lược debiasing trên 5 judge model từ 4 họ và tìm ra rằng **style bias là bias trội** (0,10–0,76, thiên vị markdown hơn văn xuôi thuần) — vượt xa position bias (≤0,04) và *"rarely studied"* — đồng thời một model tier trung + debiasing đạt agreement cao nhất toàn bộ cấu hình test ở chi phí **rẻ hơn ~15×** so với frontier.

**Tại sao paper này xuất hiện trong project?**
Hai công dụng: cảnh báo về một bias chưa được nghiên cứu kỹ (deck có markdown-like structure → rủi ro thật), và là nguồn thứ ba xác nhận **judge đắt tiền không tự động tốt hơn**. ⚠ Repo cấm dùng một mình để chống đỡ major recommendation (preprint 1 tác giả, claim mạnh và mới).

---

### `Replacing Judges with Juries: Evaluating LLM Generations with a Panel of Diverse Models` (PoLL)

- **Authors:** Pat Verga, Sebastian Hofstätter, Sophia Althammer, Yixuan Su, Aleksandra Piktus, Arkady Arkhangorodsky, Minjie Xu, Naomi White, Patrick Lewis (Cohere)
- **Year:** 2024
- **Venue / Source:** arXiv preprint (lab công nghiệp, nhiều tác giả)
- **DOI / URL:** https://arxiv.org/abs/2404.18796
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Ensemble

**Paper nói gì?**
Một **panel nhiều model nhỏ từ các họ rời nhau** vượt một judge lớn đơn lẻ, thể hiện ít intra-model bias hơn và rẻ hơn **>7×**.

**Tại sao paper này xuất hiện trong project?**
Là một trong hai vế của **mâu thuẫn evidence chưa giải quyết** trong repo (vế kia là Kohli 2026 ngay dưới). RQ06 ghi rõ: **không được dùng làm nền cho recommendation khi mâu thuẫn chưa được giải quyết**.

---

### `Nine Judges, Two Effective Votes: Correlated Errors Undermine LLM Evaluation Panels`

- **Authors:** Guneet Kohli
- **Year:** 2026 (arXiv 2026-05-28)
- **Venue / Source:** arXiv preprint (chưa peer-review, **một tác giả**)
- **DOI / URL:** https://arxiv.org/abs/2605.29800
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Ensemble

**Paper nói gì?**
Panel 9 LLM frontier từ 7 họ chỉ cung cấp lượng thông tin bằng **~2 vote độc lập** vì các model **sai cùng chỗ trên cùng item** (~75% độ độc lập danh nghĩa bị mất), và **judge đơn tốt nhất bằng hoặc vượt cả panel** trong mọi điều kiện.

**Tại sao paper này xuất hiện trong project?**
Đây là vế phản bác PoLL. ⚠ Repo ghi rõ giới hạn: test trên **NLI** (task có ground truth khách quan), không phải open-ended generation → có thể không chuyển được sang đánh giá deck. Mâu thuẫn được ghi vào unresolved question, không kết luận.

---

### `Necessary but Not Sufficient: Temperature Control and Reproducibility in LLM-as-Judge Safety Evaluations`

- **Authors:** Hiroki Tamba
- **Year:** 2026 (arXiv 2026-06-24)
- **Venue / Source:** arXiv preprint (chưa peer-review, **một tác giả**)
- **DOI / URL:** https://arxiv.org/abs/2606.26185
- **Referenced from:** RQ06 (SHOULD), RQ02 (tham chiếu về noise floor)
- **Research area:** Evaluation · Reproducibility

**Paper nói gì?**
Không set temperature → item gần ranh giới quyết định **lật pass/fail giữa các lần chạy giống hệt nhau** (disagreement tới ~50% qua 20 run), và pin temperature = 0 **giảm nhưng không loại bỏ** — nên tác giả khuyến nghị coi **grader disagreement là health metric hạng nhất**, báo cáo song song với điểm.

**Tại sao paper này xuất hiện trong project?**
Đủ để justify **một hành động bắt buộc** cho Deck Agent — đo noise floor của judge bằng cách chạy lặp — mà **không cần tin vào con số cụ thể** của paper. Repo cảnh báo mạnh về độ tin cậy (1 tác giả, 1 codebase, 7 borderline item) và cảnh báo thêm: một số model thế hệ mới đã **bỏ hẳn tham số temperature**.

---

### `Beyond correlation: The Impact of Human Uncertainty in Measuring the Effectiveness of Automatic Evaluation and LLM-as-a-Judge`

- **Authors:** Aparna Elangovan, Lei Xu, Jongwoo Ko, Mahsa Elyasi, Ling Liu, Sravan Bodapati, Dan Roth
- **Year:** 2024/2025 (arXiv 2024-10-03, revised 2025-01-27)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2410.03775
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Evaluation · Meta-methodology

**Paper nói gì?**
Khi nhãn của người có **độ bất định cao**, nhãn máy có thể **trông như** tương quan tốt ngang hoặc hơn tương quan người–người dù thực chất không phải; và các hệ số kiểu Krippendorff α vốn thiết kế để đo reliability **của người**, mang giả định về hành vi người mà hệ thống tự động không thỏa.

**Tại sao paper này xuất hiện trong project?**
Đây là phê phán phương pháp luận **nhắm thẳng vào cách NFR-53 đề xuất validate judge**: không được kết luận "judge đủ tin cậy" chỉ bằng một con số correlation, phải báo cáo kèm phân bố bất đồng của người. Kết hợp với PPTEval κ ≈ 0,59 (bất định người **cao** trong đúng domain này) → cảnh báo này áp dụng mạnh cho Deck Agent.

---

### `From Generation to Judgment: Opportunities and Challenges of LLM-as-a-judge` (survey)

- **Authors:** Dawei Li, Bohan Jiang, Liangjie Huang, Alimohammad Beigi, Chengshuai Zhao, Zhen Tan, Amrita Bhattacharjee, Yuxuan Jiang, Canyu Chen, Tianhao Wu, Kai Shu, Lu Cheng, Huan Liu
- **Year:** 2024 (arXiv v7 2025-09-29)
- **Venue / Source:** arXiv survey
- **DOI / URL:** https://arxiv.org/abs/2411.16594
- **Referenced from:** RQ06 (DISCOVERY)
- **Research area:** Evaluation · Survey

**Paper nói gì?**
Survey tổ chức lĩnh vực LLM-as-a-judge theo **taxonomy 3 chiều: what to judge / how to judge / how to benchmark**.

**Tại sao paper này xuất hiện trong project?**
Repo dùng làm **bản đồ để tìm nguồn E1 gốc**, không dùng làm evidence trực tiếp (E2 = survey). ⚠ Chưa trích xuất được danh mục bias chi tiết bên trong.

---

### `MLLM-as-a-Judge Exhibits Model Preference Bias`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-04
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2604.11589
- **Referenced from:** RQ01 (DISCOVERY, route sang RQ06)
- **Research area:** Evaluation · Multimodal bias

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Self-preference bias tồn tại trong **MLLM judge** (không chỉ LLM text).

**Tại sao paper này xuất hiện trong project?**
§8 của architecture doc tự nhắc tới bias này; RQ01 ghi lại rồi **route sang RQ06** vì thuộc phạm vi judge protocol. Vẫn ở mức discovery.

---

### `MM-JudgeBias: A Benchmark for Evaluating Compositional Biases in MLLM-as-a-Judge`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-04
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2604.18164
- **Referenced from:** RQ01 (DISCOVERY, route sang RQ06)
- **Research area:** Evaluation · Multimodal bias

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Benchmark đo **position bias và verbosity bias** trong MLLM judge.

**Tại sao paper này xuất hiện trong project?**
Cùng lý do trên — bias catalogue cho judge multimodal, route sang RQ06, chưa đọc.

---

### `The Alternative Annotator Test for LLM-as-a-Judge` (alt-test)

- **Authors:** Nitay Calderon, Roi Reichart, Rotem Dror
- **Year:** 2025 (arXiv v1 2025-01-19, bản mới 2025-08-08)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2501.10970
- **Referenced from:** RQ06 (MUST), RQ03 (ROUTED)
- **Research area:** Evaluation · Statistics · Human annotation

**Paper nói gì?**
Một **thủ tục thống kê để quyết định có được phép thay annotator người bằng LLM hay không**, kèm con số thực hành: **≥ 3 annotator** (2 người không cho tín hiệu consensus), **50–100 instance** với ε = 0,2 (100–150 với ε = 0,1, tối thiểu 30 cho giả định normality), pass khi winning rate ω ≥ 0,5 ở FDR q = 0,05.

**Tại sao paper này xuất hiện trong project?**
Đây là nguồn E1 **duy nhất** trong repo trả lời trực tiếp câu hỏi "cần bao nhiêu mẫu human" — câu hỏi mà NFR-53 đặt ra nhưng không trả lời. Repo ghi rõ đây là thủ tục cho **annotation task**, áp vào Deck Agent là **thích nghi**, và 50–100 là điều kiện để *test có lực*, không đảm bảo LLM sẽ pass.

---

# E. Rubric / checklist construction

### `TICKing All the Boxes: Generated Checklists Improve LLM Evaluation and Generation`

- **Authors:** ⚠ **chưa trích xuất được** trong session research của repo
- **Year:** 2024 (arXiv 2024-10-04)
- **Venue / Source:** arXiv preprint (có trang OpenReview)
- **DOI / URL:** https://arxiv.org/abs/2410.03608
- **Referenced from:** RQ06 (SHOULD), RQ02 (tham chiếu)
- **Research area:** Evaluation · Checklist generation

**Paper nói gì?**
**TICK** để LLM **tự sinh checklist** cho từng instruction, phân rã thành chuỗi câu hỏi YES/NO — cách này tăng exact agreement với human preference từ **46,4% → 52,2%** so với để LLM chấm điểm trực tiếp, và biến thể **STICK** dùng chính checklist đó cho self-refinement và Best-of-N.

**Tại sao paper này xuất hiện trong project?**
Đây là **đường thứ ba** giữa "checklist expert-authored đắt đỏ" (PresentBench, HealthBench) và "rubric chung rẻ nhưng yếu" — cực kỳ liên quan với một đồ án 4 người không thể thuê expert soạn 54 item × N instance. Repo cảnh báo: mức cải thiện (+5,8 điểm) **nhỏ hơn nhiều** so với khoảng cách PresentBench (0,532) vs PPTEval (0,303) → **không** được suy ra checklist LLM-sinh mạnh ngang expert-authored.

---

### `HealthBench: Evaluating Large Language Models Towards Improved Human Health`

- **Authors:** OpenAI
- **Year:** 2025
- **Venue / Source:** Official technical report (E1 — từ chính nhà phát triển)
- **DOI / URL:** https://openai.com/index/healthbench/ · https://cdn.openai.com/pdf/bd7a39d5-9e9f-47b3-903c-8b847ca650c7/healthbench_paper.pdf
- **Referenced from:** RQ06 (SHOULD), RQ02 (tham chiếu)
- **Research area:** Evaluation · Rubric at scale

**Paper nói gì?**
Ví dụ lớn nhất có thật về **rubric riêng cho từng instance**: 5.000 hội thoại, mỗi hội thoại một rubric riêng do bác sĩ viết, tổng **48.562 criteria** từ **262 bác sĩ**, mỗi criterion **có trọng số**, mọi criteria được ≥3 bác sĩ adjudicate — và **model** (không phải người) là bên chấm từng criterion.

**Tại sao paper này xuất hiện trong project?**
Chứng minh **mô hình 2 lớp**: người viết tiêu chí — model chấm tiêu chí. Đây là kiến trúc khả thi hơn nhiều cho team nhỏ so với "người chấm mọi thứ", và là bằng chứng rằng per-instance rubric là pattern **được dùng thật ở quy mô sản xuất**, không phải ý tưởng học thuật. Quy mô thì ngoài tầm với — dùng làm bằng chứng về **pattern**, không phải mục tiêu quy mô.

---

### `From Holistic Evaluation to Structured Criteria: Rubrics Across the Evolving LLM Landscape` (survey)

- **Authors:** Hao Chen, Ziyu Han, Yukun Yan, Qingfu Zhu, Maosong Sun, Wanxiang Che
- **Year:** 2026 (arXiv v2 2026-07-01)
- **Venue / Source:** arXiv survey (**E2**)
- **DOI / URL:** https://arxiv.org/abs/2606.08625 · https://arxiv.org/html/2606.08625v2
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · Rubric taxonomy

**Paper nói gì?**
Phân loại **3 paradigm dựng rubric** — human expert construction (chất lượng cao nhưng chi phí *"prohibitive"*), automated LLM construction (5 nhánh con: deductive / inductive / transfer-based / on-the-fly / query-adaptive), human-in-the-loop — kèm cảnh báo rằng *"low-quality rubrics do not merely fail to provide useful signals but can **actively mislead** reward models"*.

**Tại sao paper này xuất hiện trong project?**
Đây là taxonomy trực tiếp trả lời G-06 ("làm sao **tạo** criteria per-instance"). Repo ghi rõ hai giới hạn: là survey (E2) nên mọi hệ thống nó nhắc **chưa được verify bản gốc**; và nó **không** có so sánh số head-to-head giữa rubric người viết và rubric LLM sinh → câu hỏi "mất bao nhiêu chất lượng khi chọn đường rẻ" **vẫn chưa nguồn nào trả lời bằng số**.

---

### `ExpertLongBench: Benchmarking Language Models on Expert-Level Long-Form Generation Tasks with Structured Checklists` (CLEAR)

- **Authors:** Jie Ruan, Inderjeet Nair, Shuyang Cao, Amy Liu, Sheza Munir, Micah Pollens-Dempsey, Tiffany Chiang, Lucy Kates, Nicholas David, Sihan Chen, Ruxin Yang, Yuqian Yang, Jasmine Gump, Tessa Bialek, Vivek Sankaran, Margo Schlanger, Lu Wang
- **Year:** 2025 (arXiv v3)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2506.01241 · https://arxiv.org/html/2506.01241v3
- **Referenced from:** RQ02 (SHOULD)
- **Research area:** Evaluation · Checklist · Long-form generation

**Paper nói gì?**
**CLEAR** phân rã cả output của model lẫn reference của người thành checklist theo **rubric do expert viết**, rồi so khớp ngữ nghĩa từng item — với phân vai rất rõ: expert viết rubric, LLM chỉ làm **checklist mapper** (trích thông tin theo từng rubric item, trả `N/A` nếu vắng).

**Tại sao paper này xuất hiện trong project?**
Củng cố **mô hình 2 lớp** giống HealthBench: nguồn/người định nghĩa *cái gì phải có*, model làm việc *đối chiếu*. ⚠ Repo ghi rõ: câu *"these rubrics are written by experts... existing LLMs cannot yet replicate"* là **phát biểu định tính của tác giả**, không kèm thí nghiệm đối chứng trong phần đã đọc → **không trích như một kết quả đo được**. Repo cũng đánh dấu một đoạn số so sánh checklist expert vs model-sinh xuất hiện trong search là **UNVERIFIED — không dùng**.

---

### `Can LLMs Write Reliable Rubrics? A Meta-Evaluation for Experiment Reproduction`

- **Authors:** Hanhua Hong, Yizhi Li, Jiaoyan Chen, Luu Gia Huy, Sophia Ananiadou, Jung-jae Kim, Chenghua Lin
- **Year:** 2026 (arXiv v1)
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2607.12835
- **Referenced from:** RQ02 (DISCOVERY)
- **Research area:** Evaluation · Rubric quality

**Paper nói gì?** `[mức repo ghi nhận — PDF không parse được, mọi con số CHƯA verify]` Đặt đúng câu hỏi "LLM có viết được rubric đáng tin không" và làm meta-evaluation cho nó.

**Tại sao paper này xuất hiện trong project?**
Trúng đúng gap G-06 của RQ02 và câu hỏi mở U-4 của RQ06 (mất bao nhiêu chất lượng khi để LLM sinh criteria thay vì người viết). Repo đánh dấu đây là **nguồn ưu tiên số 1 để đọc tiếp**, nhưng hiện **không dùng làm evidence**.

---

# F. Controllability & conditioning

> Nhóm này trả lời: "làm sao **bắt** output mang một thuộc tính được yêu cầu, và làm sao **chứng minh** nó đã mang thuộc tính đó". Đây là nền lý thuyết cho `slide_type` và cho mọi tham số user đưa vào brief.

### `Instruction-Following Evaluation for Large Language Models` (IFEval)

- **Authors:** Jeffrey Zhou, Tianjian Lu, Swaroop Mishra, Siddhartha Brahma, Sujoy Basu, Yi Luan, Denny Zhou, Le Hou
- **Year:** 2023
- **Venue / Source:** arXiv preprint (Google)
- **DOI / URL:** https://arxiv.org/abs/2311.07911
- **Referenced from:** RQ03 (MUST)
- **Research area:** Evaluation · Instruction following

**Paper nói gì?**
Thay vì hỏi judge "model có nghe lời không", hãy chỉ đánh giá trên **verifiable instruction** — 25 loại, ~500 prompt, mỗi loại kiểm được **bằng code** ("write more than 400 words", "write exactly four paragraphs", "write in JSON format") — vì human eval đắt/chậm/không tái lập, còn LLM auto-eval thì bị giới hạn bởi chính năng lực của evaluator.

**Tại sao paper này xuất hiện trong project?**
Là chỗ dựa phương pháp luận cho lập luận trung tâm của RQ03 (G-01): **phần nào của brief diễn đạt lại được thành predicate kiểm bằng code thì đừng đưa cho judge** — áp thẳng vào FR-23 (`target_slides`, `time_limit_minutes`). Repo cảnh báo rõ giới hạn: IFEval **không** giải được phần khó của FR-02, vì "khác biệt có chủ đích" là quan hệ giữa **nhiều output**, không phải thuộc tính của **một** output.

---

### `Generating Summaries with Controllable Readability Levels`

- **Authors:** Leonardo F. R. Ribeiro, Mohit Bansal, Markus Dreyer
- **Year:** 2023
- **Venue / Source:** EMNLP 2023 (main, peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/2310.10623
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Controllable generation · Evaluation

**Paper nói gì?**
Precedent E1 cho mô hình *"đặt một mức attribute mục tiêu, rồi tối ưu trực tiếp khoảng cách giữa mức được yêu cầu và mức quan sát được trên output"* — với readability được mô tả là phụ thuộc *"the complexity of the text, its subject matter, and the reader's background knowledge"*.

**Tại sao paper này xuất hiện trong project?**
Cùng **hình thái bài toán** với `slide_type`: conditioning output theo một mức yêu cầu rồi đo output có tới mức đó không. Nhưng repo chỉ ra khác biệt quyết định: readability **có thang đo có sẵn**, còn `teaching/catchup/speaker_support` **không có** — đó chính là phần Deck Agent phải tự dựng. ⚠ Repo ghi rõ trang abstract **không nêu tên metric readability cụ thể** nên không khẳng định họ dùng FKGL hay gì khác.

---

### `Evaluating the Evaluators: Are readability metrics good measures of readability?`

- **Authors:** Isabel Cachola, Daniel Khashabi, Mark Dredze
- **Year:** 2025 (arXiv 2025-08-26)
- **Venue / Source:** EMNLP 2025 (`aclanthology.org/2025.emnlp-main.1225/`)
- **DOI / URL:** https://arxiv.org/abs/2508.19221
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Evaluation · Metric criticism

**Paper nói gì?**
Đánh giá 8 metric readability và thấy **phần lớn tương quan kém với phán đoán của người, kể cả metric phổ biến nhất là FKGL**, trong khi LM làm judge tốt hơn — nhưng model tốt nhất cũng chỉ đạt Pearson **0,56**.

**Tại sao paper này xuất hiện trong project?**
Đây là **counter-evidence cho phản xạ tự nhiên** "cứ lấy một proxy deterministic (words/slide, FKGL, bullets/slide) rồi coi nó *là* thuộc tính muốn đo". Hệ quả cho RQ03 rất sắc: deterministic feature dùng được để chứng minh **có khác biệt**, **không** dùng được để chứng minh khác biệt đó **đúng hướng/có giá trị**. Con số 0,56 cũng lại rơi vào vùng ρ ≈ 0,5 mà RQ06 đã ghi nhận.

---

### `ReadCtrl: Personalizing text generation with readability-controlled instruction learning`

- **Authors:** Không ghi trong evidence
- **Year:** 2024
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/html/2406.09205v1
- **Referenced from:** RQ03 (DISCOVERY)
- **Research area:** Controllable generation

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Điều khiển readability như một **thang liên tục** thay vì phân loại rời rạc expert/layperson.

**Tại sao paper này xuất hiện trong project?**
Liên quan nếu team muốn `slide_type` là **một thang** chứ không phải 3 nhãn rời — một lựa chọn thiết kế còn mở. Chưa đọc.

---

### `Readability Controllable Biomedical Document Summarization`

- **Authors:** Không ghi trong evidence
- **Year:** 2022
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/pdf/2210.04705
- **Referenced from:** RQ03 (DISCOVERY)
- **Research area:** Controllable generation · Human evaluation

**Paper nói gì?** `[mức repo ghi nhận — chưa verify]` Chứa một finding rằng **expert cảm nhận khác biệt readability nhỏ hơn nhiều so với layperson**.

**Tại sao paper này xuất hiện trong project?**
Nếu finding đó đúng, nó ảnh hưởng trực tiếp tới human eval của RQ03: người chấm là **sinh viên trong team** (đã gần expert với nội dung) có thể **không nhìn thấy** khác biệt mà người dùng thật nhìn thấy → thiết kế human study sai đối tượng. ⚠ Chưa verify.

---

### `Plug and Play Language Models: A Simple Approach to Controlled Text Generation` (PPLM)

- **Authors:** Sumanth Dathathri, Andrea Madotto, Janice Lan, Jane Hung, Eric Frank, Piero Molino, Jason Yosinski, Rosanne Liu
- **Year:** 2019/2020
- **Venue / Source:** ICLR 2020 (peer-reviewed)
- **DOI / URL:** https://arxiv.org/abs/1912.02164
- **Referenced from:** RQ03 (DISCOVERY)
- **Research area:** Controllable generation

**Paper nói gì?**
Đại diện kinh điển của họ **controllable text generation**, nơi chuẩn đánh giá là **external attribute classifier accuracy** — dùng một classifier độc lập để kiểm xem output có thực sự mang attribute được yêu cầu hay không.

**Tại sao paper này xuất hiện trong project?**
Đây là **gốc phương pháp** của "recovery test" mà RQ03 đề xuất cho FR-02: muốn chứng minh `slide_type` tạo khác biệt thì để một bộ phân loại độc lập đoán lại `slide_type` từ deck. ⚠ Repo ghi rất rõ: đã fetch abstract và trang đó **KHÔNG xác nhận tên metric** — mô tả "external classifier accuracy là chuẩn trong lĩnh vực" hiện chỉ ở mức search summary, **chưa đủ để trích số hay quote**.

---

### `Quantifying the Persona Effect in LLM Simulations`

- **Authors:** Tiancheng Hu, Nigel Collier
- **Year:** 2024
- **Venue / Source:** ACL 2024 (`aclanthology.org/2024.acl-long.554/`)
- **DOI / URL:** https://arxiv.org/abs/2402.10811
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Conditioning · LLM behavior

**Paper nói gì?**
Persona variable chỉ giải thích **<10% variance** trong annotation của các dataset NLP chủ quan, và đưa persona vào prompt cho cải thiện **khiêm tốn nhưng có ý nghĩa thống kê**.

**Tại sao paper này xuất hiện trong project?**
Dùng để **hiệu chỉnh kỳ vọng**: conditioning theo profile thường tạo hiệu ứng nhỏ so với variance tổng thể — nhất quán với delta ~0,05–0,08 của X+Slides. Hệ quả thiết kế cho Deck Agent: **thiết kế đo ghép cặp theo source là bắt buộc**, không phải tùy chọn, vì hiệu ứng cần đo nhỏ hơn nhiễu tự nhiên.

---

### `Stable Behavior, Limited Variation: Persona Validity in LLM Agents for Urban Sentiment Perception`

- **Authors:** Không ghi trong evidence
- **Year:** 2026-04
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2604.28048
- **Referenced from:** RQ03 (DISCOVERY)
- **Research area:** Conditioning · LLM behavior

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc]` Persona tạo *"statistically detectable but practically modest variation"*.

**Tại sao paper này xuất hiện trong project?**
Chỉ ghi lại như **tín hiệu hội tụ** với Hu & Collier (hiệu ứng conditioning nhỏ). Domain rất xa (urban sentiment) → không dùng làm evidence.

---

# G. Presentation design — human factors

> **Chỉ 3 paper.** Đây là nhóm mỏng nhất trong toàn bộ inventory và là một trong những knowledge gap lớn nhất của repo — xem §6 của roadmap.

### `Application of multimedia theory to PowerPoint slides created by engineering educators`

- **Authors:** Joanna Garner, Allen Gaudelli, Sarah Zappe, Michael Alley
- **Year:** 2009
- **Venue / Source:** ASEE Annual Conference and Exposition Proceedings (Austin, TX)
- **DOI / URL:** https://pure.psu.edu/en/publications/application-of-multimedia-theory-to-powerpoint-slides-created-by--2/
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Visual design · Human factors · Empirical survey

**Paper nói gì?**
Khảo sát thực nghiệm 72 presentation / 1.381 slide tại một hội nghị kỹ thuật và đo được các con số nền: trung bình **7,5 dòng text và 33,4 từ mỗi slide**, quy đổi ~35 từ/phút người xem tiếp nhận từ slide, và **dưới một nửa số slide có hình**.

**Tại sao paper này xuất hiện trong project?**
Cho một **mốc thực nghiệm** để hiệu chỉnh thang đo `words/slide` và `image-bearing slide ratio` của Deck Agent. Repo cảnh báo rõ: đây là mô tả **thực trạng**, **không phải chuẩn** — khác corpus, khác năm (2009), khác ngôn ngữ → **không** dùng làm ngưỡng pass/fail.

---

### `How the design of presentation slides affects audience comprehension: A case for the assertion-evidence approach`

- **Authors:** J. K. Garner, M. P. Alley
- **Year:** 2013
- **Venue / Source:** International Journal of Engineering Education, 29(6):1564–1579
- **DOI / URL:** https://pure.psu.edu/en/publications/how-the-design-of-presentation-slides-affects-audience-comprehens/
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Visual design · Cognition · Slide structure

**Paper nói gì?**
Slide dùng **một câu khẳng định đầy đủ làm headline** (assertion) kèm **evidence chủ yếu bằng hình** cho comprehension và recall tốt hơn slide dùng headline dạng cụm danh từ, đặc biệt với khái niệm phức tạp.

**Tại sao paper này xuất hiện trong project?**
Cung cấp **một attribute quan sát được có nền học thuật** và **đếm được bằng code** trên `Slide.title` của Deck IR: *title có phải một câu khẳng định hoàn chỉnh không*. ⚠ Cảnh báo mạnh của repo: literature này nói về *slide tốt nói chung*, **không** nói gì về phân biệt 3 mode → dùng nó để chứng minh `teaching` phải có nhiều assertion-title hơn là **suy diễn vượt nguồn**. Repo chưa đọc full-text.

---

### `Advancing the Knowledge Base on Effective Presentation Slide Design: Three Pilot Studies`

- **Authors:** Wolfe, Shanmugaraj, Reineke, Peet, Moreau
- **Year:** 2024
- **Venue / Source:** Journal of Business and Technical Communication
- **DOI / URL:** https://journals.sagepub.com/doi/10.1177/00472816231169433
- **Referenced from:** RQ03 (DISCOVERY) — ⚠ **fetch thất bại (ECONNREFUSED), chưa verify nội dung**
- **Research area:** Visual design · Human factors

**Paper nói gì?** `[mức repo ghi nhận — chưa đọc được]` Có thể chứa evidence **phản bác** các guideline slide-design phổ biến (quy tắc 6×6, "ít chữ luôn tốt hơn").

**Tại sao paper này xuất hiện trong project?**
Nếu đúng, nó ảnh hưởng trực tiếp tới việc **có nên coi `words/slide` là proxy chất lượng hay chỉ là proxy khác biệt** — tức trực tiếp tới cách RQ03 định nghĩa metric cho `slide_type`. Chưa verify được nội dung.

---

# H. Methodology / reproducibility / statistics

> Nhóm này không nói gì về slide. Nó nói về **cách biết một kết quả có thật hay không** — và với người xây agent, đây là nhóm quyết định bạn có phân biệt được "phiên bản mới tốt hơn" với "hôm nay model may mắn" hay không.

### `Non-Determinism of "Deterministic" LLM Settings`

- **Authors:** Berk Atil, Sarp Aykent, Alexa Chittams, Lisheng Fu, Rebecca J. Passonneau, Evan Radcliffe, Guru Rajan Rajagopal, Adam Sloan, Tomasz Tudrej, Ferhan Ture, Zhe Wu, Lixinyu Xu, Breck Baldwin
- **Year:** 2024 (arXiv v5 2025-04-02)
- **Venue / Source:** arXiv preprint (nhiều tác giả, thực nghiệm quy mô)
- **DOI / URL:** https://arxiv.org/abs/2408.04667
- **Referenced from:** RQ08 (MUST), RQ03 (MUST)
- **Research area:** Reproducibility · LLM behavior

**Paper nói gì?**
5 LLM × 8 task × 10 run ở cấu hình được coi là deterministic vẫn cho **accuracy dao động tới ~15% giữa các run**, khoảng cách best–worst tới ~70%, và *"none of the LLMs consistently delivers repeatable accuracy across all tasks"* — kèm 2 metric đề xuất để đo mức determinism (TARr@N, TARa@N).

**Tại sao paper này xuất hiện trong project?**
Đây là evidence **mang tính chặn** cho hai RQ cùng lúc: RQ08 dùng nó để bắt buộc **N run + báo cáo variance** trong mọi so sánh version (không tin một delta nhỏ), RQ03 dùng nó cho mệnh đề nền "chạy lại cùng `slide_type` sẽ ra deck khác nhau" → **không đo noise floor thì mọi khác biệt giữa 3 slide_type không phân biệt được với nhiễu**. Cảnh báo transfer: paper đo trên task **có đáp án đúng**, deck generation thì không → chỉ mượn mệnh đề định tính và **hình thái metric**, không mượn con số 15%.

---

### `Lessons from the Trenches on Reproducible Evaluation of Language Models`

- **Authors:** Biderman et al.
- **Year:** 2024
- **Venue / Source:** arXiv preprint (đi kèm thư viện lm-eval)
- **DOI / URL:** https://arxiv.org/abs/2405.14782
- **Referenced from:** RQ08 (SHOULD)
- **Research area:** Reproducibility · Evaluation infrastructure

**Paper nói gì?**
Xác định **3 vấn đề phương pháp lặp đi lặp lại** trong LM evaluation — độ nhạy với evaluation setup, khó so sánh đúng giữa các method, thiếu reproducibility/transparency — và đề xuất codify các quy ước ngầm thành công cụ.

**Tại sao paper này xuất hiện trong project?**
Nền cho yêu cầu "freeze và version hoá mọi biến của evaluation". ⚠ Repo ghi rõ: trong phần đọc được (abstract-level) **không** trích ra được checklist cụ thể "phải freeze biến nào" → danh sách biến trong `landscape.md` là **INFERENCE của research này**, không phải trích nguyên văn.

---

### `Quantifying Language Models' Sensitivity to Spurious Features in Prompt Design` (FormatSpread)

- **Authors:** Melanie Sclar, Yejin Choi, Yulia Tsvetkov, Alane Suhr
- **Year:** 2023
- **Venue / Source:** arXiv preprint
- **DOI / URL:** https://arxiv.org/abs/2310.11324 · https://github.com/msclar/formatspread
- **Referenced from:** RQ08 (SHOULD)
- **Research area:** Reproducibility · Prompt sensitivity

**Paper nói gì?**
Chênh lệch tới **76 accuracy points** chỉ do đổi **format** prompt (không đổi nội dung), và độ nhạy này **không mất đi** khi tăng model size, số few-shot example, hay instruction tuning — nên nên báo cáo **khoảng** performance trên nhiều format thay vì một con số.

**Tại sao paper này xuất hiện trong project?**
Chống đỡ hai thứ của RQ08: **freeze prompt version** khi so version, và **chống strawman prompt** khi dựng naive baseline. Repo cảnh báo: đo trên task phân loại few-shot → con số 76 điểm **không** chuyển thẳng sang generation task dài như sinh deck; chỉ support argument định tính.

---

### `State of What Art? A Call for Multi-Prompt LLM Evaluation`

- **Authors:** Mizrahi et al.
- **Year:** 2024
- **Venue / Source:** TACL vol 12, pp. 933–949 (peer-reviewed)
- **DOI / URL:** https://aclanthology.org/2024.tacl-1.52/
- **Referenced from:** RQ08 (SHOULD)
- **Research area:** Reproducibility · Evaluation methodology

**Paper nói gì?**
Phân tích 6,5M instance × 20 LLM × 39 task cho thấy instruction template khác nhau đổi performance **cả về giá trị tuyệt đối lẫn thứ hạng tương đối** giữa các model → phải đánh giá trên nhiều paraphrase thay vì single-prompt.

**Tại sao paper này xuất hiện trong project?**
Đây là evidence **trực tiếp nhất** cho luận điểm của RQ08 rằng "một prompt duy nhất cho naive baseline là thiết kế không đủ" — tức nếu Deck Agent thắng một baseline chỉ có một prompt thì kết luận không đứng vững. Repo ghi rõ paper không đưa ra số lượng paraphrase tối thiểu cho một dự án nhỏ.

---

### `A Metric Learning Reality Check`

- **Authors:** Kevin Musgrave, Serge Belongie, Ser-Nam Lim
- **Year:** 2020
- **Venue / Source:** ECCV 2020 (peer-reviewed)
- **DOI / URL:** https://link.springer.com/chapter/10.1007/978-3-030-58595-2_41 · https://github.com/KevinMusgrave/powerful-benchmarker
- **Referenced from:** RQ08 (SHOULD)
- **Research area:** Evaluation methodology · Fair comparison

**Paper nói gì?**
Cải thiện được công bố suốt nhiều năm trong deep metric learning là **"marginal at best" khi so công bằng**, và paper liệt kê các dạng bất công cụ thể: backbone khác nhau, embedding size khác nhau, và thủ thuật training không được nhắc trong paper.

**Tại sao paper này xuất hiện trong project?**
Là **checklist "cân biến"** khi dựng naive baseline cho Deck Agent. Repo ghi rõ danh sách biến của Deck Agent (model tier, prompt effort, context budget, retry policy) là **INFERENCE ánh xạ**, không phải điều paper nói.

---

### `Troubling Trends in Machine Learning Scholarship`

- **Authors:** Zachary C. Lipton, Jacob Steinhardt
- **Year:** 2018; đăng lại ACM Queue 17(1)
- **Venue / Source:** arXiv + ACM Queue, DOI 10.1145/3317287.3328534
- **DOI / URL:** https://arxiv.org/abs/1807.03341
- **Referenced from:** RQ08 (SHOULD)
- **Research area:** Research methodology

**Paper nói gì?**
Đặt tên cho trend *failure to identify the sources of empirical gains* — *"emphasizing unnecessary modifications to neural architectures when gains actually stem from hyper-parameter tuning"*.

**Tại sao paper này xuất hiện trong project?**
Là evidence cho quy tắc **một thay đổi một lần** khi so version và khi ablation — tức nếu Deck Agent đổi cả prompt lẫn component trong một release thì delta không quy được về nguyên nhân nào. Repo ghi rõ: paper **nêu vấn đề, không đưa protocol**.

---

### `Are We Really Making Much Progress? A Worrying Analysis of Recent Neural Recommendation Approaches`

- **Authors:** Maurizio Ferrari Dacrema, Paolo Cremonesi, Dietmar Jannach
- **Year:** 2019
- **Venue / Source:** RecSys '19 (peer-reviewed), DOI 10.1145/3298689.3347058
- **DOI / URL:** https://arxiv.org/abs/1907.06902 · https://github.com/MaurizioFD/RecSys2019_DeepLearning_Evaluation
- **Referenced from:** RQ08 (MUST)
- **Research area:** Evaluation methodology · Baseline design

**Paper nói gì?**
Reproduce 18 neural method publish ở hội nghị top và thấy **6/7 method reproduce được thường bị đánh bại bởi heuristic đơn giản khi baseline được tune tử tế** — tức baseline yếu tạo ra ảo giác tiến bộ ở quy mô **cả một lĩnh vực**, không phải lỗi cá biệt.

**Tại sao paper này xuất hiện trong project?**
Đây là evidence cứng cho Family B của RQ08 (**chống strawman baseline**): nếu Deck Agent so với một prompt-LLM naive chưa tune thì mọi delta đo được đều đáng ngờ. Repo ghi rõ khác domain hoàn toàn → **argument chuyển được, số liệu không**.

---

### `The Hitchhiker's Guide to Testing Statistical Significance in Natural Language Processing`

- **Authors:** Rotem Dror, Gili Baumer, Segev Shlomov, Roi Reichart
- **Year:** 2018
- **Venue / Source:** ACL 2018, pp. 1383–1392 (peer-reviewed)
- **DOI / URL:** https://aclanthology.org/P18-1128/
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Statistics · Methodology

**Paper nói gì?**
Đưa ra **protocol chọn significance test** phù hợp cho setup NLP, nhấn mạnh rằng đặc điểm của task và của evaluation measure mới quyết định chọn test nào.

**Tại sao paper này xuất hiện trong project?**
RQ03 cần đúng thứ này: khi so 3 điều kiện `slide_type` trên cùng tập source, câu hỏi "có khác biệt không" là một câu **thống kê**, không phải cảm tính. Đây là chỗ dựa để yêu cầu **paired design + permutation/bootstrap test + effect size** thay vì so hai con số trung bình.

---

### `Reliability in Content Analysis: Some Common Misconceptions and Recommendations`

- **Authors:** Klaus Krippendorff
- **Year:** 2004
- **Venue / Source:** Human Communication Research 30(3):411–433
- **DOI / URL:** https://academic.oup.com/hcr/article/30/3/411/4331534 · DOI 10.1111/j.1468-2958.2004.tb00738.x
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Statistics · Inter-annotator agreement

**Paper nói gì?**
Nguồn gốc của các **ngưỡng reliability** được trích rộng rãi trong đánh giá có annotator: α ≥ 0,800 = tin được; 0,667 ≤ α < 0,800 = chỉ rút kết luận tạm thời; α < 0,667 = loại bỏ.

**Tại sao paper này xuất hiện trong project?**
Repo cần một ngưỡng "đủ tin" cho phần human validation của NFR-53. ⚠ Trạng thái verify **hai mức**: paper tồn tại (E1, có DOI) nhưng **ngưỡng số chỉ ở E2** (trích qua nguồn thứ cấp, chưa đọc bản gốc). Và cảnh báo áp dụng rất quan trọng: ngưỡng này đến từ content analysis (giả định có "mã đúng"), trong khi PPTEval đo được κ ≈ 0,59 giữa 4 người chấm chính domain này — **dưới cả ngưỡng tentative** → áp thẳng 0,8 vào Deck Agent là đặt mục tiêu không khả thi.

---

### `Sample size requirements for estimating Pearson, Kendall and Spearman correlations`

- **Authors:** Douglas G. Bonett, Thomas A. Wright
- **Year:** 2000
- **Venue / Source:** Psychometrika 65(1):23–28 (peer-reviewed)
- **DOI / URL:** https://link.springer.com/article/10.1007/BF02294183 · DOI 10.1007/BF02294183
- **Referenced from:** RQ06 (SHOULD)
- **Research area:** Statistics · Sample size

**Paper nói gì?**
Khảo sát các interval estimate cho Pearson / Kendall tau-a / Spearman và **cỡ mẫu cần để khoảng tin cậy đạt độ rộng mong muốn**, kèm một standard error cải tiến cho Spearman.

**Tại sao paper này xuất hiện trong project?**
Là **phương pháp đúng** để tính cỡ mẫu nếu team muốn con số riêng cho ρ mục tiêu của mình. ⚠ Repo **không trích xuất được bảng n cụ thể** → dùng nguồn này một mình để trả lời "cần bao nhiêu mẫu" là **chưa đủ**; con số thực hành đến từ alt-test.

---

### `Manipulation Check` (mục từ bách khoa)

- **Authors:** Jennifer Hoewe
- **Year:** 2017
- **Venue / Source:** *The International Encyclopedia of Communication Research Methods*, Wiley — **E2 (reference work, không phải primary study)**
- **DOI / URL:** https://onlinelibrary.wiley.com/doi/abs/10.1002/9781118901731.iecrm0135 · DOI 10.1002/9781118901731.iecrm0135
- **Referenced from:** RQ03 (SHOULD)
- **Research area:** Experimental methodology

**Paper nói gì?**
Định nghĩa **manipulation check**: phép đo xác minh rằng một thao tác thực nghiệm đã thực sự tác động như dự định, tức nó *"elicits the expected differences between or among experimental conditions"*, đồng thời loại trừ tác động ngoài ý muốn lên biến khác.

**Tại sao paper này xuất hiện trong project?**
Đây là **cái tên đúng** cho việc RQ03 phải làm: FR-02 về bản chất là một manipulation (đổi `slide_type`), và DoD "slide_type có khác biệt đo được" **chính là** một manipulation check. Đặt đúng tên cho phép mượn cả bộ chuẩn phương pháp đi kèm — điều kiện đối chứng, ghép cặp, kiểm định, và **khai báo hướng kỳ vọng TRƯỚC khi chạy**. E2 → chỉ dùng làm khung khái niệm.

---

# I. Compound system behavior

### `Are More LLM Calls All You Need? Towards Scaling Laws of Compound Inference Systems`

- **Authors:** Lingjiao Chen, Matei Zaharia, James Zou
- **Year:** 2024
- **Venue / Source:** arXiv preprint (có bản OpenReview)
- **DOI / URL:** https://arxiv.org/abs/2403.02419
- **Referenced from:** RQ08 (SHOULD)
- **Research area:** Agent architecture · Compound systems

**Paper nói gì?**
Performance của một compound system (Vote, Filter-Vote) có thể **tăng rồi giảm** theo số lần gọi LM, vì độ khó không đồng nhất giữa các query (thêm call giúp query dễ nhưng hại query khó) — từ đó xây được scaling model dự đoán số call tối ưu.

**Tại sao paper này xuất hiện trong project?**
Dùng ở Family C của RQ08 làm **cảnh báo chống cộng dồn kết quả ablation**: đóng góp của các component không cộng tuyến tính. Repo ghi rõ đây là **INFERENCE mở rộng** — kiến trúc họ nghiên cứu là voting/filtering trên **cùng một task**, không phải pipeline nhiều component khác chức năng như Deck Agent.

---

# Appendix A — Nguồn được reference nhưng KHÔNG phải paper

Các mục dưới đây xuất hiện trong `evidence.md` nhưng là repo/docs/blog, nên **không có entry trong inventory chính**. Ghi lại để việc kiểm kê là đầy đủ và không ai tưởng chúng bị bỏ sót.

| Nguồn | Loại | Referenced from | Level repo gán | Vì sao không tính là paper |
| --- | --- | --- | --- | --- |
| `nexu-io/open-design` — Open Design | Official repo + site + docs | RQ08 (MUST) | E1 | Là **hệ thống**, không phải publication. Xem file riêng `03_research/external_systems/opendesign.md` |
| `Westlake-AGI-Lab/Auto-Slides` | Repo (tự ghi ICME 2026) | RQ08 (DISCOVERY) | E1 cho sự tồn tại repo | Repo tồn tại nhưng repo **chưa đọc paper**; không có link paper trong evidence |
| Gamma Generate API | Official product docs | RQ08 (SHOULD) | E1 (official docs) | Product documentation, không phải research |
| Gemini Notebook Enterprise — Notebooks API | Google Cloud official docs | RQ08 (SHOULD) | E1 cho API, **UNVERIFIED** cho khả năng sinh deck | Product documentation |
| `teng-lin/notebooklm-py` | Thư viện Python **không chính thức** | RQ08 (DISCOVERY) | **E3 / UNOFFICIAL** | Reverse-engineered, repo cấm dùng làm đường chạy baseline chính thức |
| `CSHaitao/Awesome-LLMs-as-Judges` | Repo tổng hợp cộng đồng | RQ06 (DISCOVERY) | E3 | Chỉ dùng để **tìm** E1 |
| Presenter's Arena — "Slide Deck vs Presentation" | Blog nghề nghiệp | RQ03 (DISCOVERY) | **E3** | Blog. Đáng chú ý vì là mô tả gần nhất với trục `catchup` vs `speaker_support` (~80–120 từ/slide vs ~6 từ/slide) — nhưng **không có nghiên cứu nền**, không được dùng một mình |
| emergentmind, Baeldung, Grokipedia, envisioning.com, bestaiweb.ai | Trang tổng hợp về ablation methodology | RQ08 (DISCOVERY) | **E3** | Đã bị repo thay bằng nguồn E1 tương ứng |
| Nhóm blog/vendor về cost-latency của LLM judge (Medium, Confident AI, Galileo, Maxim, Label Your Data) | Vendor content | RQ06 (§4.6) | **E3 — ĐÃ LOẠI** | Con số mâu thuẫn nhau (95% / 500–5000× / 20× / ~10×), không truy được primary source |

---

# Appendix B — Entity UNVERIFIED (không được dùng làm evidence)

| Entity | Referenced from | Trạng thái | Ghi chú |
| --- | --- | --- | --- |
| "test hapkin slide" | RQ08 (§20) | **UNVERIFIED ENTITY** | 3 query search khác nhau, **không có** project/tool/benchmark nào tên "hapkin" liên quan slide generation. Kết quả trả về đều là entity **khác tên** (Haiku Deck, Haptik, harpin AI…). Repo kết luận: **KHÔNG đoán**; Q-004 vẫn OPEN, cần team cung cấp tên chính xác |
| Liên kết "Open Design trong NFR" ↔ `nexu-io/open-design` | RQ08 (§21) | **INFERENCE, chưa verify với team** | NFR mục 0.1 chỉ ghi *"gần Open Design"* trong bảng so sánh mô hình trả token, **không có URL/repo**; có ít nhất 5 entity trùng tên gần giống |
| `SlidesGen-Bench` | RQ06 (§4.2) vs RQ01 (§7) | **Mâu thuẫn nội bộ repo** | RQ06 (2026-08-14) xếp UNVERIFIED vì chưa fetch; RQ01 (cùng ngày) xếp E1 vì đã fetch abstract + HTML. Cần thống nhất trước khi trích |
| `ACUEval`, `QuanTemp / CheckThat! 2025 Task 3` | RQ02 (§3.4) | **Chưa verify link gốc** | Đã có entry trong §C với cờ cảnh báo |
| Danh sách tác giả của `TICK`, và của `LLM Evaluators Recognize and Favor Their Own Generations` | RQ06 | **Chưa đối chiếu bản gốc** | Phải verify trước khi trích vào báo cáo |
| Danh sách tác giả của `X+Slides` (trong RQ01) | RQ01 §8 | Đã được **RQ03 bổ sung đầy đủ** ngày 2026-08-15 | Mâu thuẫn đã tự giải quyết giữa hai wave |

---

# Appendix C — Ghi chú về "PPTXAgent" (tên trong yêu cầu vs tên trong repo)

Yêu cầu tạo hai deliverable này nhắc tới ba hệ thống tham chiếu: **OpenDesign, Claude Design, PPTXAgent**. Đối chiếu với repo:

| Tên trong yêu cầu | Entity tương ứng trong repo | Trạng thái |
| --- | --- | --- |
| **OpenDesign** | `nexu-io/open-design` — có file research riêng `03_research/external_systems/opendesign.md`, E1 (repo + site + docs official) | **Khớp** (nhưng repo cảnh báo có ≥5 entity trùng tên gần giống) |
| **Claude Design** | Chỉ được nhắc **một lần**, gián tiếp: Open Design tự mô tả là *"open-source alternative cho Claude Design (công cụ design đóng của Anthropic)"* | ⚠ **Repo KHÔNG có research nào về Claude Design.** Không có evidence entry, không có capability analysis, không có paper. Mọi phát biểu về capability của nó trong deliverable 2 phải được đánh dấu là **chưa có evidence trong repo này** |
| **PPTXAgent** | Không có entity nào tên chính xác "PPTXAgent" trong repo. Entity gần nhất — và gần như chắc chắn là thứ được nhắc tới — là **`PPTAgent`** (arXiv 2501.03936, EMNLP 2025), xuất hiện ở 4/5 file evidence với tag MUST READ | ⚠ **Khác biệt tên đã ghi nhận, không tự ý gộp làm một.** Roadmap sẽ dùng tên `PPTAgent` (tên verify được) và ghi chú chỗ này |

Theo kỷ luật của `CLAUDE.md` ("không invent... nếu tên hoặc sự tồn tại của một entity không verify được thì đánh dấu"), tôi **không** tạo entry inventory cho "PPTXAgent" hay "Claude Design" như thể chúng là paper. Chúng được xử lý ở deliverable 2 với nhãn trạng thái tương ứng, và "Claude Design" được ghi nhận là một **knowledge gap của chính repo** — xem §6 của roadmap.

