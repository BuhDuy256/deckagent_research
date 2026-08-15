# RQ02 — Evidence Index (Source Quality: hallucination / incorrectness / coverage gap)

**Ngày research:** 2026-08-15 (mọi entry dưới đây được truy cập trong ngày này, trừ khi ghi khác)
**Evidence level theo `04_evidence/EVIDENCE_POLICY.md`:** E1 (paper gốc / official repo / official docs) > E2 (survey, technical report uy tín, hoặc claim trích qua nguồn thứ cấp chưa verify bản gốc) > E3 (blog/tutorial/community) > UNVERIFIED.

**Quy ước áp dụng (giữ nguyên như RQ06):** arXiv preprint là bản gốc nên xếp **E1**, nhưng nếu chưa peer-review thì ghi rõ `E1 (preprint)`. Với preprint ít tác giả mang claim mạnh → có ghi chú cảnh báo riêng.

**Tag đọc:**
- **MUST READ** — 5 nguồn quyết định kết luận của RQ02 (đúng giới hạn 3–5).
- **SHOULD READ** — cần cho một sub-question cụ thể.
- **DISCOVERY ONLY** — định vị landscape / route sang RQ khác, không dùng làm nền cho recommendation.

⚠ **Ghi chú về độ sâu verification.** File này phân biệt hai mức:
- **[FETCHED]** — đã fetch trực tiếp trang arXiv/ACL Anthology/repo chính chủ trong session này, số liệu trích từ nội dung trả về.
- **[SEARCH-ONLY]** — chỉ thấy qua kết quả search, **chưa fetch bản gốc**. Theo quy tắc 3 của `EVIDENCE_POLICY`, các claim từ nhóm này được hạ xuống mức "trích qua nguồn thứ cấp" và **không được dùng một mình** để chống đỡ recommendation.

Các nguồn đã verify ở Wave 1 (FActScore, AlignScore, SummaC, TICK, PresentBench, PPTEval, HealthBench, G-Eval, alt-test…) **không verify lại** ở đây — xem `03_research/RQ06/evidence.md` (ngày research 2026-08-14). RQ02 chỉ ghi lại phần **mới** hoặc phần **thay đổi cách hiểu** về chúng.

---

## 0. Bảng tổng: nguồn nào trả lời câu hỏi nào của RQ02

| Sub-question của RQ02 | Nguồn chính | Độ mạnh |
| --- | --- | --- |
| Precision-only metric có phát hiện được coverage gap không? | §1.1 *All Claims Are Equal* | **Mạnh** — có bảng số trực tiếp |
| Có một instrument nào tách được cả 3 failure không? | §1.2 FineSurE | **Mạnh** — E1 peer-reviewed, có công thức + số |
| F2 nào **thực sự** usable (G-05)? | §1.3 *Stress Testing*, §1.4 *Do Automatic Factuality Metrics…*, §1.5 MiniCheck | **Trung bình–mạnh** — 3 nguồn độc lập, nhưng không nguồn nào test trên slide deck |
| Case bullet bị **tính toán lại** (G-05, ví dụ 51% YoY) | §2.1 EQUATE, §2.6 Molecular Facts | **Trung bình** — EQUATE là 2019, cần thận trọng về độ mới |
| LLM judge có tự phát hiện bỏ sót được không? | §2.2 AbsenceBench, §1.2 FineSurE | **Mạnh** |
| Cách **tạo** per-instance criteria (G-06) | §2.3 rubric survey, §2.4 ExpertLongBench/CLEAR, §2.5 Comprehensiveness Metrics, + TICK/HealthBench từ RQ06 | **Trung bình** — có taxonomy tốt, thiếu so sánh head-to-head có số |
| Chi phí đủ rẻ cho daily | §1.5 MiniCheck repo, §2.9 VeriFastScore | **Trung bình–mạnh** |
| Noise floor của evaluator (G-09) | §1.3, §1.4 + RQ06 §3.6 | **Trung bình** |
| Đã có ai áp F2 lên slide deck chưa? | §4 (negative finding) | **Mạnh** (là negative finding) |

---

## 1. MUST READ (5 nguồn)

### 1.1 All Claims Are Equal, but Some Claims Are More Equal Than Others: Importance-Sensitive Factuality Evaluation of LLM Generations

```
Tác giả: Miriam Wanner, Leif Azzopardi, Paul Thomas, Soham Dan, Benjamin Van Durme, Nick Craswell
Năm: 2025 (arXiv 2510.07083v1, tháng 10/2025)
Link: https://arxiv.org/abs/2510.07083 · https://arxiv.org/html/2510.07083v1
Level: E1 (preprint, chưa xác nhận peer-review)
Ngày research: 2026-08-15 · [FETCHED]
Áp dụng cho RQ: RQ02 (chính) — route sang RQ07 (critical_facts weighting)
Tag: MUST READ
```

**Nguồn này THỰC SỰ support điều gì — đây là bằng chứng số quan trọng nhất của cả RQ02:**

1. **Precision-based factuality metric KHÔNG phân biệt được "bỏ sót" với "đúng và đủ".** Bảng 4 của paper (theo nội dung fetch) trên single-answer query:

   | Loại response | FActScore | VitalPrec (metric của họ) |
   | --- | --- | --- |
   | normal (đúng, đủ) | 82.58% | 72.81% |
   | **missing** (thiếu đúng câu trả lời chính) | **82.75%** | 72.22% |
   | **wrong** (trả lời sai) | 76.63% | **48.73%** |

   → **Response "missing" ăn điểm FActScore *cao hơn* response "normal"** (82.75 vs 82.58). Đây là bằng chứng trực tiếp, có số, cho luận điểm trung tâm của RQ02: **một metric chỉ chạy theo chiều output → source thì mù hoàn toàn với coverage gap.**
2. Khoảng cách giữa "wrong" và "normal" ở FActScore chỉ ~6 điểm (76.63 vs 82.58), nhưng ở metric có trọng số salience là **24 điểm** (48.73 vs 72.81) → **không phải mọi fact đều đáng cân bằng nhau**; nếu không phân biệt fact quan trọng với fact phụ, tín hiệu "nói sai" bị pha loãng.
3. Cách xác định importance của họ: LLM (GPT-4o) phân rã response thành atomic subclaim, xếp hạng theo mức liên quan tới query, gán nhãn 3 mức **vital / okay / less-important**, rồi chỉ tính metric trên nhóm vital. Ở mức response-level: **89.53%** response "wrong" có lỗi ở subclaim vital, so với **45.03%** ở response "normal".

**Nguồn này KHÔNG support điều gì:**
- Setup là **query-based long-form QA**, không phải document→deck. "Importance" của họ neo vào **query**, còn Deck Agent neo vào **source PDF + slide_type**. Chuyển giao được về mặt nguyên lý, nhưng cơ chế xác định importance phải thiết kế lại.
- Không chứng minh cách gán importance bằng LLM là đúng; paper tự nói *human weighting preferences remain an acknowledged open question*.
- Preprint, chưa peer-review.

**Mức áp dụng cho Deck Agent:** trực tiếp về mặt kết luận (precision-only ≠ coverage), cần thích nghi về mặt cơ chế.

---

### 1.2 FineSurE: Fine-grained Summarization Evaluation using LLMs

```
Tác giả: Hwanjun Song, Hang Su, Igor Shalyminov, Jason Cai, Saab Mansour
Năm: 2024 (ACL 2024, Long Papers)
Link: https://aclanthology.org/2024.acl-long.51/ · https://arxiv.org/abs/2407.00908
      code: https://github.com/DISL-Lab/FineSurE-ACL24
Level: E1 (peer-reviewed, ACL 2024)
Ngày research: 2026-08-15 · [FETCHED] (arxiv.org/html/2407.00908v1)
Áp dụng cho RQ: RQ02 (chính)
Tag: MUST READ
```

**Nguồn này THỰC SỰ support điều gì — đây là nguồn duy nhất tìm được cấu trúc đúng 3 failure mà RQ02 cần:**

1. **Hai task tách rời, chạy ngược chiều nhau:**
   - **Fact checking** (chiều output → source): mỗi câu của summary được phân loại vào 9 category — 7 loại lỗi factuality (*out-of-context, entity, predicate, circumstance, coreference, discourse link, grammatical*) + "other error" + "no error".
   - **Keyfact alignment** (chiều source/keyfact → output): với mỗi keyfact, kiểm tra nó có được suy ra từ summary không, và nếu có thì chỉ ra **số dòng** của các câu tương ứng.
2. **Ba công thức tách bạch:**
   - Faithfulness = `|S_fact| / |S|` (tỷ lệ câu không lỗi)
   - Completeness = `|{k | (k,s) ∈ E}| / |K|` (tỷ lệ keyfact được align)
   - Conciseness = `|{s | (k,s) ∈ E}| / |S|` (tỷ lệ câu output có align với keyfact)
3. **Nguồn của keyfact:** *"provided by humans; if unavailable, it can be automatically derived from the reference summary"*, và hệ thống hỗ trợ **LLM tự extract** bằng prompt tùy biến. → G-06: có precedent cho cả 2 nhánh (người viết / máy sinh) trong **cùng một** framework.
4. **Số liệu:**
   - Faithfulness (FRANK, sentence-level): FineSurE (GPT-4) **86.4% balanced accuracy**; so sánh Spearman: SummaC-Conv (NLI) 0.814 · QAFactEval (QA) 0.813 · G-Eval (LLM) 0.834.
   - **Completeness (REALSumm, summary-level): FineSurE 0.688 Pearson / 0.677 Spearman, so với G-Eval 0.314 / 0.295 và UniEval 0.134 / 0.180.** → **Khoảng cách lớn nhất trong toàn bộ evidence của RQ02**: LLM judge dạng rubric chung (G-Eval) gần như **không** đo được completeness, trong khi keyfact alignment thì đo được.
   - Conciseness: FineSurE 0.505 / 0.451 vs G-Eval 0.314 / 0.277.
5. **Giới hạn paper tự nêu — rất quan trọng cho một đồ án:**
   - Open-source LLM **kém ở khả năng tuân thủ prompt**: chỉ Llama3-70B-Inst đạt success ratio ~98%, các model khác 50–90%. Context 8K vs 128K của GPT-4 gây **truncation** với input dài.
   - **Localize lỗi khó hơn detect lỗi rất nhiều**: GPT-4 đạt 86.4% ở detect nhị phân nhưng chỉ **42.2%** khi phải nói *loại lỗi nào*.
   - Chỉ test trên **news domain**.
   - Trần người: FRANK Cohen's κ ≈ **0.58**; REALSumm Krippendorff α ≈ **0.66**.

**Nguồn này KHÔNG support điều gì:**
- FineSurE **không** tách "bịa" khỏi "nói sai" thành 2 con số riêng — 7 error type của nó là taxonomy ngôn ngữ học (entity/predicate/circumstance…), không phải trục *có counterpart trong source hay không*. Việc map 7 type đó sang 2 failure của Deck Agent là **INFERENCE của RQ02**, không phải điều paper phát biểu.
- Keyfact trong setup của họ đến từ **reference summary**, không phải từ source document. Deck Agent không có gold deck (RQ01 §5.3) → phải lấy keyfact từ **source PDF**, là một thay đổi có thật, chưa được nguồn nào validate.
- Con số 42.2% cảnh báo: đừng kỳ vọng evaluator tự phân loại đúng *loại* lỗi.

**Mức áp dụng cho Deck Agent:** cần thích nghi (news → PDF, sentence → bullet, reference-derived keyfact → source-derived keyfact), nhưng **cấu trúc 2-task ngược chiều là thứ chuyển giao được nguyên vẹn**.

---

### 1.3 Stress Testing Factual Consistency Metrics for Long-Document Summarization

```
Tác giả: Zain Muhammad Mujahid, Dustin Wright, Isabelle Augenstein
Năm: 2025 (arXiv 2511.07689; ID cho biết v1 nộp 2025-11. ⚠ trang fetch trả về một dòng ngày
     mâu thuẫn ("published April 29, 2025") — KHÔNG trích ngày cụ thể trước khi đọc bản gốc)
Link: https://arxiv.org/abs/2511.07689 · https://arxiv.org/html/2511.07689v2
Level: E1 (preprint, chưa peer-review)
Ngày research: 2026-08-15 · [FETCHED]
Áp dụng cho RQ: RQ02 (G-05 — F2 nào usable)
Tag: MUST READ
```

**Nguồn này THỰC SỰ support điều gì — đây là nguồn trực tiếp nhất cho G-05:**

1. **6 metric reference-free được stress-test:** BARTScore, SummaC-Conv, SummaC-ZS, **AlignScore**, UniEval, **MiniCheck (7B)**.
2. **7 phép biến đổi giữ nguyên nghĩa:** paraphrase, simplify, synonym replace, less diverse, **negation tương đương logic**, **summarize (nén thêm)**, **thêm câu từ source**.
3. **Kết quả xếp hạng độ bền:**
   - **Tương đối bền:** **MiniCheck** và UniEval — *"comparatively stable performance"*; MiniCheck vẫn **vấp ở negation tương đương logic**.
   - **Rủi ro cao:** **AlignScore** và SummaC-ZS — *"particularly unreliable across domains and perturbation types"*; AlignScore tụt điểm mạnh ở domain pháp lý với paraphrase và negation.
   - BARTScore: nhạy cảm nghiêm trọng ở văn bản trang trọng/pháp lý.
4. **Phát hiện quan trọng nhất cho Deck Agent — metric kém đi khi claim bị NÉN:** trên SQuALITY và LexAbSumm, *"metric scores consistently decrease as claim similarity increases"*, tức metric **hoạt động tệ hơn trên claim nén, mật độ thông tin cao**. AlignScore và BARTScore *"exhibit sharp declines for high-similarity claims"* vì phụ thuộc alignment cục bộ.
5. **NLI-based metric không tận dụng được context rộng hơn:** *"NLI-based metrics remain insensitive to increasing context windows"* — chúng so sánh cục bộ. Ví dụ số: MiniCheck trên LexAbSumm tăng từ 0.47 (w=0) lên 0.60 (w=2) khi mở rộng context.
6. **Kết luận của tác giả:** *"no metric consistently maintains factual alignment under long-context conditions"*; cần metric có **multi-span reasoning** và context-aware calibration.

**Nguồn này KHÔNG support điều gì:**
- Preprint, chưa peer-review. 3 domain (legal / sci-fi / scientific), **không có slide deck**.
- Không test FActScore hay LLM judge làm metric → không kết luận được F2c hay F3/F4 bền hơn/kém hơn.
- Không đo coverage — chỉ đo factual consistency (precision side).

**Vì sao đây là nguồn quyết định G-05:** nó nói thẳng rằng **AlignScore là candidate rủi ro cao**, còn **MiniCheck là candidate bền nhất trong nhóm**, và rằng **claim bị nén chính là chỗ metric yếu nhất** — mà bullet của Deck Agent *về bản chất* là claim bị nén. Đây là evidence để **không** implement AlignScore chỉ vì Wave 1 nêu tên nó.

---

### 1.4 Do Automatic Factuality Metrics Measure Factuality? A Critical Evaluation

```
Tác giả: Sanjana Ramprasad, Byron C. Wallace
Năm: 2024 (arXiv 2411.16638, nộp 2024-11-25; bản mới nhất 2025-11-05).
     ⚠ Search trả về một trang poster NeurIPS 2025 cho đúng tên paper này
     (https://neurips.cc/virtual/2025/loc/san-diego/poster/116764) — CHƯA fetch trang đó để xác nhận,
     nên KHÔNG ghi "NeurIPS 2025" như venue đã verify.
Link: https://arxiv.org/abs/2411.16638 · https://arxiv.org/html/2411.16638v1
Level: E1 (preprint; khả năng đã peer-review nhưng chưa xác nhận)
Ngày research: 2026-08-15 · [FETCHED]
Áp dụng cho RQ: RQ02 (G-05, và G-09 noise/gaming)
Tag: MUST READ
```

**Nguồn này THỰC SỰ support điều gì:**

1. **6 metric được test, phủ 4 nhóm phương pháp:** QuestEval (QA-based) · SummaC-Conv (NLI) · UniEval, **AlignScore**, **MiniCheck** (specialized model) · ChatGPT-DA (LLM-based).
2. **Một MLP chỉ dùng feature bề mặt cạnh tranh được với SOTA.** Trên document/query-focused summarization, model nông *"generally competitive with SOTA factuality consistency metrics"*; nó **vượt** các metric này trên dialogue summarization, nhưng **thua** AlignScore, MiniCheck và ChatGPT-DA. → Một phần điểm số của các metric này giải thích được bằng đặc trưng bề mặt, không phải bằng "hiểu tính đúng".
3. **Nhạy với sửa đúng vs nhạy với sửa vô hại:** **MiniCheck và AlignScore có dịch chuyển điểm dương mạnh nhất sau khi sửa lỗi factual** (điểm cộng cho hai metric này). QuestEval **không đổi** (trung bình) sau khi summary được sửa đúng. Ngược lại, **SummaC-Conv và UniEval nhạy với sửa vô hại hơn là với sửa đúng**.
4. **Gaming được:** chèn một câu ngẫu nhiên lấy từ source làm điểm tăng, thường **bằng hoặc hơn** mức tăng do sửa đúng thật. Chèn cụm từ hằng định làm điểm tăng **0.1–0.15 điểm**, và chiến lược gaming cho mức cải thiện **gấp 3–8 lần** so với cải thiện thật.
5. **ChatGPT-DA là bền nhất** trong nhóm, nhưng paper cảnh báo LLM judge có thể dựa vào **parametric knowledge** thay vì source được cấp.

**Nguồn này KHÔNG support điều gì:**
- Không test trên slide/multimodal.
- Không đo coverage.
- Không đưa ra metric thay thế — đây là paper phê phán, không phải paper đề xuất.

**Hệ quả trực tiếp cho Deck Agent (INFERENCE, ghi ở đây vì nó thay đổi cách dùng nguồn):** kết quả (4) là một failure mode **cụ thể và nguy hiểm** cho Deck Agent, vì một deck "dày chữ, copy nhiều câu nguyên văn từ PDF" sẽ **ăn điểm fidelity cao** trong khi FR-22/ready-to-use lại coi đó là lỗi. Tức metric fidelity có thể **thưởng cho đúng cái sản phẩm coi là hỏng** — cùng dạng rủi ro mà RQ06 §C3 nêu cho verbosity bias của judge, nhưng ở đây là với metric tự động.

---

### 1.5 MiniCheck: Efficient Fact-Checking of LLMs on Grounding Documents

```
Tác giả: Liyan Tang, Philippe Laban, Greg Durrett
Năm: 2024 (EMNLP 2024)
Link: https://aclanthology.org/2024.emnlp-main.499/ · https://arxiv.org/abs/2404.10774
      repo chính chủ: https://github.com/Liyan06/MiniCheck
Level: E1 (peer-reviewed, EMNLP 2024)
Ngày research: 2026-08-15 · [FETCHED] (ACL Anthology + repo)
Áp dụng cho RQ: RQ02 (candidate signal chạy daily)
Tag: MUST READ
```

**Nguồn này THỰC SỰ support điều gì:**

1. **Bài toán họ giải đúng bài toán của Deck Agent:** *"recognizing if LLM output can be grounded in evidence"* — kiểm tra một claim có được chống đỡ bởi **grounding document** hay không.
2. **Chi phí:** model nhỏ đạt mức GPT-4 với **chi phí thấp hơn 400×**. Model tốt nhất dưới 1B là **MiniCheck-FT5 (Flan-T5-Large, ~770M)**.
3. **Model có sẵn (repo):** MiniCheck-RoBERTa-Large · MiniCheck-DeBERTa-V3-Large · MiniCheck-Flan-T5-Large (~770M) · Bespoke-MiniCheck-7B (SOTA hiện tại theo README).
4. **Tốc độ đo được (repo):** inference trên tập test 29K là **30 phút** (có APC) / **55 phút** (không APC) trên **một GPU NVIDIA A6000**. → **INFERENCE của RQ02:** ~16 claim/giây; một deck 10 slide × 4 bullet ≈ 40 claim → **vài giây/deck**, chi phí API = 0.
5. **LLM-AggreFact:** benchmark hợp nhất **11 dataset công khai**, ~**29K test example**, phủ nhiều nguồn document (Wikipedia, phỏng vấn, web) và nhiều domain (news, dialogue, science, healthcare).
6. **Điểm kỹ thuật đáng chú ý nhất cho Deck Agent:** dữ liệu synthetic của họ được thiết kế để dạy model *"check each fact in the claim and recognize **synthesis of information across sentences**"* — tức **claim tổng hợp thông tin từ nhiều câu nguồn**. Đây chính là hình dạng của một bullet trong deck.

**Nguồn này KHÔNG support điều gì:**
- **Không** validate trên slide deck (xem §4).
- **Không** đo coverage — MiniCheck trả lời "claim này có được support không", một chiều duy nhất.
- **Không** phân biệt "bịa" với "nói sai" — output là nhị phân supported/not supported.
- Con số 400× là so với LLM API dùng để fact-check, **không phải** so với chi phí generation của Deck Agent.
- Con số tốc độ lấy từ README, phụ thuộc GPU cụ thể (A6000) — team không chắc có GPU tương đương.
- Theo §1.3, MiniCheck vẫn **vấp ở negation tương đương logic**; theo §1.4, MiniCheck vẫn **game được** bằng cách chèn câu từ source.

---

## 2. SHOULD READ

### 2.1 EQUATE: A Benchmark Evaluation Framework for Quantitative Reasoning in Natural Language Inference

```
Tác giả: Abhilasha Ravichander, Aakanksha Naik, Carolyn Rose, Eduard Hovy
Năm: 2019 (CoNLL 2019)
Link: https://arxiv.org/abs/1901.03735 · https://aclanthology.org/K19-1033/
Level: E1 (peer-reviewed)
Ngày research: 2026-08-15 · [FETCHED]
Áp dụng cho RQ: RQ02 (case bullet bị TÍNH TOÁN LẠI — G-05)
Tag: SHOULD READ
```

**Nguồn này THỰC SỰ support điều gì:** đây là nguồn duy nhất tìm được nói **trực tiếp** về điểm yếu định lượng của NLI — đúng cơ chế nằm dưới ví dụ "~51% YoY growth" của G-05.

- 5 tập đánh giá: **Stress Test, AwpNLI, NewsNLI, RTE-Quant, RedditNLI**, phủ so sánh khoảng, suy luận số học, và suy luận bằng lời về lượng.
- Benchmark **9 NLI model** đã công bố: *"on average, state-of-the-art methods do not achieve an absolute improvement over a majority-class baseline, suggesting that they do not implicitly learn to reason with quantities."*
- Baseline Q-REAS thao tác **ký hiệu trên các đại lượng**: **+24.2%** so với NLI model tốt nhất ở nhóm numerical reasoning, nhưng **−8.1%** ở nhóm verbal reasoning.

**Nguồn này KHÔNG support điều gì — cảnh báo bắt buộc:**
- **2019.** 9 model được test thuộc thế hệ trước AlignScore/MiniCheck/LLM hiện đại. **Không được kết luận "AlignScore/MiniCheck không làm được số học" từ nguồn này.**
- Điều nguồn này support đúng mức: **suy luận định lượng là một trục năng lực TÁCH RỜI khỏi entailment ngôn ngữ**, và các model học entailment không tự nhiên học được nó. Đây là lý do **kiến trúc** để tách phép kiểm tra số ra khỏi phép kiểm tra entailment, chứ không phải bằng chứng về một model cụ thể của 2026.
- Cần một nguồn 2024–2026 đo lại trên model hiện đại → xem `recommendation.md` §3 (unresolved).

### 2.2 AbsenceBench: Language Models Can't Tell What's Missing

```
Tác giả: Harvey Yiyun Fu, Aryan Shrivastava, Jared Moore, Peter West, Chenhao Tan, Ari Holtzman
Năm: 2025 (arXiv 2506.11440, nộp 2025-06-13).
     Search trả về trang poster NeurIPS 2025 (Datasets & Benchmarks) — nhất quán về tên và tác giả,
     nhưng ⚠ trang poster CHƯA được fetch trực tiếp.
Link: https://arxiv.org/abs/2506.11440
Level: E1
Ngày research: 2026-08-15 · [FETCHED] (trang abstract arXiv)
Áp dụng cho RQ: RQ02 (vì sao không giao coverage cho LLM judge)
Tag: SHOULD READ
```

**Nguồn này THỰC SỰ support điều gì:**
- Abstract nguyên văn (trích từ trang arXiv): *"while models excel at recalling surprising information, they still struggle to identify clearly omitted information… even state-of-the-art models like Claude-3.7-Sonnet achieve only **69.6% F1-score** with a modest average context length of **5K tokens**."*
- Giải thích cơ chế: *"Transformer attention mechanisms cannot easily attend to 'gaps' in documents since these absences don't correspond to any specific keys that can be attended to."*
- 3 domain: dãy số, thơ, GitHub pull request. Setup: model được cấp **cả bản gốc lẫn bản đã bị xóa bớt** và phải chỉ ra phần nào bị xóa.

**Nguồn này KHÔNG support điều gì:**
- Setup là "so 2 văn bản, tìm phần bị xóa" — **dễ hơn** bài toán của Deck Agent ("đọc PDF 20 trang + deck 10 slide, ý quan trọng nào bị bỏ sót") vì ở AbsenceBench, phần bị thiếu là một chuỗi khớp chính xác. → Con số 69.6% F1 nên đọc là **cận trên lạc quan**, không phải kỳ vọng cho Deck Agent.
- Không nói gì về metric fidelity hay về slide.

**Vì sao vẫn quan trọng:** cùng với FineSurE (completeness: G-Eval chỉ 0.314 Pearson), đây là nguồn thứ hai, **độc lập về phương pháp**, chỉ cùng một hướng: **phát hiện thiếu sót là điểm yếu có tính cấu trúc của LLM khi được hỏi trực tiếp.** Hệ quả: coverage **không nên** giao cho một prompt kiểu "deck này có bỏ sót ý quan trọng nào không?" — phải chuyển thành bài toán **đối chiếu từng item trong danh sách đóng**.

### 2.3 From Holistic Evaluation to Structured Criteria: Rubrics Across the Evolving LLM Landscape (survey)

```
Tác giả: Hao Chen, Ziyu Han, Yukun Yan, Qingfu Zhu, Maosong Sun, Wanxiang Che
Năm: 2026 (arXiv 2606.08625, v2 2026-07-01)
Link: https://arxiv.org/abs/2606.08625 · https://arxiv.org/html/2606.08625v2
Level: E2 (survey)
Ngày research: 2026-08-15 · [FETCHED]
Áp dụng cho RQ: RQ02 (G-06 — CÁCH TẠO criteria)
Tag: SHOULD READ
```

**Nguồn này THỰC SỰ support điều gì — đây là taxonomy trực tiếp trả lời G-06:**

3 paradigm dựng rubric/checklist:
1. **Human expert construction** — ví dụ HealthBench (262 bác sĩ, 48.562 criteria), PRBench/ProfBench. Survey đánh giá: cho *"irreplaceable domain priors and value alignment"* nhưng chi phí *"prohibitive"*.
2. **Automated LLM construction**, chia 5 nhánh con:
   - **Deductive** — trích checklist từ mô tả task (ví dụ survey nêu: TICK, ARES)
   - **Inductive** — chưng cất criteria từ dữ liệu mẫu
   - **Transfer-based** — khai thác chuẩn từ kho tri thức bên ngoài
   - **On-the-fly** — sinh criteria ngay lúc evaluate
   - **Query-adaptive** — criteria riêng cho từng query
3. **Human-in-the-loop** — người can thiệp ở *"highest-value steps"*, phần chạy quy mô giao cho LLM.

Cảnh báo của survey: *"low-quality rubrics do not merely fail to provide useful signals but can actively mislead reward models and degrade evaluation accuracy."*

Taxonomy 2 chiều: **structural** (output vs process × holistic / analytic / **atomic**) và **content** (task-grounded / behavior-grounded / knowledge-grounded).

**Nguồn này KHÔNG support điều gì:**
- Là **survey (E2)** — theo `EVIDENCE_POLICY` quy tắc 3, các tên hệ thống nó nhắc (AutoRubric-R1V, OpenRubrics, CDRRM, ARCANE, AdaRubric, DR Tulu, ResearchQA, PRBench/ProfBench) **chưa được RQ02 verify bản gốc** → giữ ở mức **DISCOVERY**, không dùng làm evidence.
- **Không** cung cấp so sánh số head-to-head giữa rubric người viết và rubric LLM sinh. Câu hỏi "mất bao nhiêu chất lượng khi chọn đường rẻ" (U-4 của RQ06) **vẫn chưa được nguồn nào trả lời bằng số.**

### 2.4 ExpertLongBench / CLEAR

```
Tên: ExpertLongBench: Benchmarking Language Models on Expert-Level Long-Form Generation Tasks
     with Structured Checklists
Tác giả: Jie Ruan, Inderjeet Nair, Shuyang Cao, Amy Liu, Sheza Munir, Micah Pollens-Dempsey,
         Tiffany Chiang, Lucy Kates, Nicholas David, Sihan Chen, Ruxin Yang, Yuqian Yang,
         Jasmine Gump, Tessa Bialek, Vivek Sankaran, Margo Schlanger, Lu Wang
Năm: 2025 (arXiv 2506.01241, v3)
Link: https://arxiv.org/abs/2506.01241 · https://arxiv.org/html/2506.01241v3
Level: E1 (preprint)
Ngày research: 2026-08-15 · [FETCHED]
Áp dụng cho RQ: RQ02 (G-06 — mô hình 2 lớp cho criteria)
Tag: SHOULD READ
```

**Nguồn này THỰC SỰ support điều gì:**
- **CLEAR** = Checklist-based Expert-level Assessment with Rubric. Cơ chế: phân rã **cả output của model lẫn reference của người** thành checklist theo **rubric do expert viết**, rồi so khớp ngữ nghĩa **từng item**.
- **Phân vai rõ ràng:** rubric do **expert** viết; LLM chỉ làm **checklist mapper** — GPT-4o trích thông tin từ reference theo từng rubric item (trả `N/A` nếu vắng), Qwen2.5-72B trích từ output của model.
- Phát biểu của paper về năng lực LLM: *"these rubrics are written by experts, ensuring high-quality criteria that **existing LLMs cannot yet replicate**"*; việc sinh checklist chất lượng cao tự động được để lại là **future work**.

**Nguồn này KHÔNG support điều gì:**
- **Không** có so sánh số trực tiếp expert-checklist vs LLM-checklist (đã fetch bản HTML v3 và xác nhận không có). Câu *"LLMs cannot yet replicate"* là **phát biểu định tính của tác giả**, không kèm thí nghiệm đối chứng trong phần đã đọc → **không được trích như một kết quả đo được**.
- Domain là văn bản chuyên môn dài (pháp lý, y tế…), không phải slide.

**Giá trị thật cho Deck Agent:** củng cố **mô hình 2 lớp** (người/nguồn định nghĩa *cái gì* phải có — model làm việc *đối chiếu*), giống HealthBench (RQ06 §3.5). Đây là kiến trúc criteria khả thi nhất cho team 4 người.

⚠ **UNVERIFIED — không dùng:** trong kết quả search có một đoạn số so sánh checklist expert (26, 29, 6 item) với checklist model sinh (20, 22, 7 item). **Không xác định được nguồn gốc của đoạn số này** trong bất kỳ paper nào đã fetch. Ghi lại để không ai vô tình dùng; **không được trích**.

### 2.5 Comprehensiveness Metrics for Automatic Evaluation of Factual Recall in Text Generation

```
Tác giả: Adam Dejl, James Barry, Alessandra Pascale, Javier Carnerero Cano
Năm: 2025 (arXiv 2510.07926, nộp 2025-10-09, revised 2026-05-07).
     Trang arXiv ghi ACL 2026 Findings — ⚠ chưa đối chiếu ACL Anthology.
Link: https://arxiv.org/abs/2510.07926
Level: E1 (preprint; venue chưa verify độc lập)
Ngày research: 2026-08-15 · [FETCHED] (trang abstract)
Áp dụng cho RQ: RQ02 (đo coverage — G-06)
Tag: SHOULD READ
```

**Nguồn này THỰC SỰ support điều gì:** đây là nguồn duy nhất tìm được **so sánh trực tiếp 3 cách đo coverage** — đúng 3 nhánh mà G-06 yêu cầu so sánh:
1. **NLI-based** — phân rã văn bản thành atomic statement, dùng NLI để tìm fact bị thiếu;
2. **Q&A-based** — trích cặp câu hỏi–trả lời rồi so sánh câu trả lời giữa các nguồn;
3. **End-to-end** — hỏi thẳng LLM chỗ nào thiếu.

Đóng khung vấn đề đúng như G-02: *"omissions can result in significant harm **comparable to** that posed by factual inaccuracies, including hallucinations."*

Kết quả (theo nội dung fetch): **metric end-to-end đơn giản lại hiệu quả một cách bất ngờ** so với các metric phức tạp hơn, nhưng **đánh đổi ở robustness và interpretability**.

**Nguồn này KHÔNG support điều gì:**
- ⚠ **Chưa trích xuất được con số correlation cụ thể** — chỉ có kết luận định tính từ abstract. **Không được dùng một mình để chọn method.**
- Kết luận "end-to-end hiệu quả" **căng thẳng trực tiếp** với AbsenceBench (§2.2) và FineSurE (§1.2, G-Eval chỉ 0.314). → Đây là một **mâu thuẫn evidence chưa giải quyết**, ghi vào `recommendation.md` §3, **không** dùng để biện minh cho việc hỏi thẳng LLM về coverage.

### 2.6 Molecular Facts: Desiderata for Decontextualization in LLM Fact Verification

```
Tác giả: Anisha Gunjal, Greg Durrett (UT Austin)
Năm: 2024 (Findings of EMNLP 2024)
Link: https://arxiv.org/abs/2406.20079 · https://aclanthology.org/2024.findings-emnlp.215/
      code: https://github.com/anisha2102/molecular_facts
Level: E1 (peer-reviewed) — nhưng ⚠ [SEARCH-ONLY], chưa fetch bản gốc trong session này
Ngày research: 2026-08-15
Áp dụng cho RQ: RQ02 (bullet là fragment, không phải câu đầy đủ)
Tag: SHOULD READ
```

**Nguồn này THỰC SỰ support điều gì (ở mức trích qua search, chưa verify bản gốc):**
- Nêu đúng tension mà Deck Agent gặp: *đơn vị càng lớn càng khó fact-check, nhưng đơn vị càng atomic thì càng **thiếu context để diễn giải đúng***.
- Đề xuất 2 tiêu chí cho "molecular fact": **decontextuality** (đứng một mình được) và **minimality** (thêm càng ít thông tin càng tốt).

**Nguồn này KHÔNG support điều gì:** chưa fetch bản gốc → **không trích số**, chỉ dùng để đặt tên cho vấn đề. Cần đọc bản gốc trước khi dùng trong báo cáo.

**Vì sao liên quan trực tiếp:** bullet trên slide (`"— Doanh thu +51% YoY"`) là **fragment cực đoan hơn cả atomic fact** — mất chủ ngữ, mất mốc thời gian, mất đơn vị. Bất kỳ evaluator textual nào cũng phải giải bài toán decontextualization **trước** khi verify, và đó là một bước có thể tự nó sinh lỗi.

### 2.7 On Positional Bias of Faithfulness for Long-form Summarization

```
Tác giả: David Wan, Jesse Vig, Mohit Bansal, Shafiq Joty
Năm: 2024/2025 (arXiv 2410.23609, nộp 2024-10-31, revised 2025-07-06; trang ghi NAACL 2025)
Link: https://arxiv.org/abs/2410.23609
Level: E1
Ngày research: 2026-08-15 · [FETCHED] (trang abstract)
Áp dụng cho RQ: RQ02 (PDF nguồn dài → vị trí thông tin ảnh hưởng đo lường)
Tag: SHOULD READ
```

**Support:** (a) mẫu **hình chữ U** của faithfulness theo vị trí trong document — model tốt ở đầu và cuối, **kém ở giữa**; đặt tài liệu quan trọng vào giữa làm faithfulness tụt rõ; (b) trên 8 dataset long-form summarization có annotation người, các **LLM-based faithfulness metric** *"remain sensitive to document order, indicating positional bias"*.

**KHÔNG support:** không có con số cụ thể trong abstract; không test NLI-based metric nhỏ.

**Hệ quả cho Deck Agent (INFERENCE):** PDF nguồn của Deck Agent dài. Nếu evaluator dựa vào LLM đọc toàn bộ PDF, **ý nằm giữa tài liệu bị đo kém hơn ý ở đầu/cuối** — nghĩa là điểm fidelity/coverage **phụ thuộc vị trí**, một confounder không liên quan gì tới chất lượng ContentPlanner. Đây là lập luận ủng hộ việc **chia nhỏ + đối chiếu theo item** thay vì nhét cả PDF vào một prompt.

### 2.8 QAFactEval: Improved QA-Based Factual Consistency Evaluation for Summarization

```
Tác giả: Alexander R. Fabbri, Chien-Sheng Wu, Wenhao Liu, Caiming Xiong
Năm: 2021/2022 (NAACL 2022)
Link: https://arxiv.org/abs/2112.08542
Level: E1 (peer-reviewed)
Ngày research: 2026-08-15 · [FETCHED]
Áp dụng cho RQ: RQ02 (nhánh question/probe-based)
Tag: SHOULD READ
```

**Support (abstract nguyên văn được fetch):** so sánh diện rộng entailment-based vs QA-based; *"carefully choosing the components of a QA-based metric, especially **question generation** and **answerability classification**, is critical to performance"*; QAFactEval cải thiện **14% trung bình** so với QA-based metric trước đó trên benchmark SummaC và **vượt entailment-based metric tốt nhất**; và — điểm quan trọng nhất — *"QA-based and entailment-based metrics can offer **complementary signals** and be combined into a single metric for a further performance boost."*

**KHÔNG support:** không có số về chi phí/tốc độ/số lần gọi model trong phần đã fetch → **không được dùng để claim QA-based rẻ hay đắt**. Đây là điểm yếu thật: pipeline QA (answer selection → question generation → QA → answer overlap) về cấu trúc là **nhiều bước hơn** NLI một bước; nhưng RQ02 **không có nguồn** đo chi phí đó.

⚠ Đối chiếu ngược: theo §1.4, **QuestEval** (QA-based, thế hệ trước QAFactEval) là metric **không phản ứng** với việc sửa đúng. Hai nguồn này không mâu thuẫn (khác metric), nhưng đủ để **không mặc định QA-based là an toàn**.

### 2.9 VeriFastScore: Speeding up long-form factuality evaluation

```
Tác giả: Rishanth Rajendhran, Amir Zadeh, Matthew Sarte, Chuan Li, Mohit Iyyer
Năm: 2025 (Findings of EMNLP 2025)
Link: https://aclanthology.org/2025.findings-emnlp.491/ · https://arxiv.org/abs/2505.16973
Level: E1 (peer-reviewed)
Ngày research: 2026-08-15 · [FETCHED] (ACL Anthology)
Áp dụng cho RQ: RQ02 (làm sao để decomposition-based đủ rẻ cho daily)
Tag: SHOULD READ
```

**Support:** thay vì phân rã claim tuần tự rồi verify từng cái, fine-tune **Llama3.1 8B** để **đồng thời trích và verify** mọi claim verify được trong một lượt. Kết quả: **speedup 6.6×** tổng thể (**9.9×** nếu trừ phần retrieval) so với VeriScore, giữ tương quan **r = 0.80 ở mức example** và **r = 0.94 ở mức system**.

**KHÔNG support:** verify với **Google Search** làm evidence, không phải với một document cho trước → **không** chuyển thẳng sang Deck Agent (Deck Agent có source đóng, không cần retrieval web). Không đo coverage.

**Giá trị cho Deck Agent:** (a) bằng chứng E1 rằng **gộp decompose + verify vào một lượt** là hướng giảm chi phí có hiệu quả đo được; (b) con số **r ở mức system (0.94) cao hơn hẳn mức example (0.80)** — INFERENCE: metric rẻ có thể **đủ tin để so version** (system-level) ngay cả khi chưa đủ tin để phán xét một deck cụ thể (example-level). Đây đúng là use case daily tracking của Deck Agent.

### 2.10 Nhóm nguồn về đơn vị đo coverage: ACU/RoSE và QAPyramid

```
(a) Revisiting the Gold Standard: Grounding Summarization Evaluation with Robust Human Evaluation
    Năm: 2023 (ACL 2023) · https://aclanthology.org/2023.acl-long.228/
    repo: https://github.com/Yale-LILY/ROSE
    Level: E1 (peer-reviewed) — ⚠ [SEARCH-ONLY], chưa fetch bản gốc
(b) QAPyramid: Fine-grained Evaluation of Content Selection for Text Summarization
    Năm: 2024 (arXiv 2412.07096) · https://arxiv.org/abs/2412.07096
    repo: https://github.com/ZhangShiyue/QAPyramid
    Level: E1 (preprint) — ⚠ [SEARCH-ONLY], chưa fetch bản gốc
Ngày research: 2026-08-15
Áp dụng cho RQ: RQ02 (đơn vị đo coverage), route sang RQ07
Tag: SHOULD READ
```

**Support (mức trích qua search, chưa verify bản gốc — không trích số vào báo cáo):**
- **ACU (Atomic Content Unit)**: annotator chuyển reference summary thành các mệnh đề factual atomic, rồi đối chiếu summary được sinh với các ACU đó. Điểm ACU = `|A_s| / |A|` — **thuần recall**. **RoSE** = ~22.000 annotation ở mức summary trên 28 hệ thống, 3 dataset (CNNDM, XSum, SamSum).
- **QAPyramid**: phân rã reference thành cặp QA theo khung **QA-SRL**, thay vì để người tự chọn đơn vị. Lý do nêu: giao thức Pyramid gốc *"suffers from a lack of systematicity in the definition and granularity of the sub-units"*. ~8.9K annotation mức QA trên 10 hệ thống.

**KHÔNG support:** cả hai neo vào **reference summary**, mà Deck Agent **không có gold deck**. Chuyển sang "ACU trích từ **source**" là một thay đổi có thật, làm mất tính chất "đây là những ý người viết tóm tắt cho là quan trọng" — tức **bài toán salience quay lại** (xem §1.1).

**Vì sao vẫn cần đọc:** hai nguồn này là nơi định nghĩa **đơn vị** của coverage. Vấn đề "granularity của đơn vị không hệ thống" mà QAPyramid nêu là **đúng vấn đề** benchmark metadata `critical_facts` của Deck Agent sẽ gặp (PROJECT SOURCE — `EVALUATION_MISSION.md`).

### 2.11 UniSumEval

```
Tên: UniSumEval: Towards Unified, Fine-Grained, Multi-Dimensional Summarization Evaluation for LLMs
Tác giả: Yuho Lee, Taewon Yun, Jason Cai, Hang Su, Hwanjun Song
Năm: 2024 (Findings of EMNLP 2024)
Link: https://arxiv.org/abs/2409.19898
Level: E1 (peer-reviewed)
Ngày research: 2026-08-15 · [FETCHED] (trang abstract — nội dung chi tiết KHÔNG trích xuất được)
Áp dụng cho RQ: RQ02
Tag: SHOULD READ
```

**Support (chỉ ở mức abstract):** benchmark có **input scenario đa dạng** và **annotation fine-grained, đa chiều**; dùng **AI hỗ trợ** để phát hiện source text có vấn đề và để giảm độ khó cho annotator khi làm annotation fine-grained.

**KHÔNG support / cảnh báo trích xuất:** ⚠ **không trích xuất được** danh sách dimension cụ thể, cách định nghĩa đơn vị annotation, hay số liệu so sánh evaluator. Kết quả search có nhắc các dimension *faithfulness / completeness / conciseness / abstractiveness / domain stability* nhưng **không xác nhận được từ trang gốc** → giữ ở mức **chưa verify**, không dùng làm evidence.

**Vì sao vẫn ghi:** ý tưởng "AI hỗ trợ annotator làm fine-grained annotation" là mô hình chi phí đáng chú ý cho G-06 và cho RQ07.

---

## 3. DISCOVERY ONLY

### 3.1 LongSumEval
```
Tên: LongSumEval: Question-Answering Based Evaluation and Feedback-Driven Refinement
     for Long Document Summarization
Tác giả: Huyen Nguyen, Haoxuan Zhang, Yang Zhang, Junhua Ding, Haihua Chen
Năm: 2026 (arXiv 2604.25130, v2) · https://arxiv.org/abs/2604.25130
Level: E1 (preprint) · Ngày research: 2026-08-15 · [FETCHED nhưng KHÔNG trích xuất được số]
Tag: DISCOVERY ONLY
```
Sinh câu hỏi **từ chính source document** (không phải từ reference summary) rồi tách coverage khỏi faithfulness bằng cách trả lời câu hỏi trên cả summary lẫn source. **Rất gần với thứ Deck Agent cần** (source-driven probe, không cần gold deck). ⚠ **Không trích xuất được** correlation với người, so sánh baseline, hay chi phí → **không dùng làm evidence**. Cần đọc lại bản PDF.

### 3.2 Can LLMs Write Reliable Rubrics? A Meta-Evaluation for Experiment Reproduction
```
Tác giả: Hanhua Hong, Yizhi Li, Jiaoyan Chen, Luu Gia Huy, Sophia Ananiadou, Jung-jae Kim, Chenghua Lin
Năm: 2026 (arXiv 2607.12835v1) · https://arxiv.org/abs/2607.12835
Level: E1 (preprint) · Ngày research: 2026-08-15 · [FETCHED nhưng KHÔNG trích xuất được số]
Tag: DISCOVERY ONLY
```
Đúng câu hỏi của G-06 ("LLM có viết được rubric đáng tin không") và đúng câu hỏi mở U-4 của RQ06. ⚠ PDF không parse được thành text; **mọi con số và kết luận đều CHƯA verify** → **không dùng làm evidence**, nhưng là nguồn ưu tiên số 1 để đọc tiếp.

### 3.3 Enhancing Presentation Slide Generation by LLMs with a Multi-Staged End-to-End Approach
```
Tác giả: Sambaran Bandyopadhyay, Himanshu Maheshwari, Anandhavelu Natarajan, Apoorv Saxena
Năm: 2024 (arXiv 2406.06556) · https://arxiv.org/abs/2406.06556
Level: E1 (preprint) · Ngày research: 2026-08-15 · [FETCHED]
Tag: DISCOVERY ONLY
```
Hệ thống document→slide. Evaluation của họ: **Coverage** = cosine similarity của embedding ở 2 mức (paragraph↔slide, sentence↔bullet); **PPL** bằng GPT-2; **LLM-Eval** bằng Mistral-7B (organization/clarity/coherence/complexity); human trên 6 chiều (readability, consistency, coverage, diversity, flow, usability, thang 1–5).

**Điều nguồn này thực sự cho biết — và vì sao nó là DISCOVERY chứ không phải evidence:**
- Nó **có** đề cập rằng gắn mỗi slide vào section/subsection cụ thể của document làm giảm hallucination, **nhưng KHÔNG có phép đo nào cô lập tác dụng đó**. → **Không được trích như bằng chứng cho việc `source_ref` giảm bịa.**
- "Coverage" của họ là **embedding similarity**, không phải đối chiếu ý quan trọng → **không** phân biệt được coverage gap theo nghĩa RQ02; một deck lặp lại nhiều chữ của source vẫn ăn điểm cao.
- Dùng **PPL** — mà PPTAgent đã chứng minh PPL gần như không tương quan với content quality (RQ01 §5.3).

**Giá trị:** là **negative evidence** cho §4 — ngay cả một paper document→slide chuyên biệt cũng **không** dùng NLI/atomic-fact metric, và cách đo "coverage" của họ yếu.

### 3.4 Các nguồn chỉ mới định vị, chưa đọc
| Tên | Link | Vì sao ghi lại |
| --- | --- | --- |
| VeriFact (EMNLP 2025 main) | https://aclanthology.org/2025.emnlp-main.905/ | Xử lý **missing comparandum / omitted conditions** khi decontextualize claim — đúng dạng lỗi của bullet "51% YoY" (thiếu mốc so sánh). ⚠ [SEARCH-ONLY] |
| DnDScore (EMNLP 2025) | https://aclanthology.org/2025.emnlp-main.1205.pdf | Decontextualization + decomposition. ⚠ [SEARCH-ONLY] |
| SAFE / LongFact (Wei et al.) | https://arxiv.org/abs/2403.18802 | F1@K gộp precision và recall — nhưng recall neo vào **K = số fact "lý tưởng"**, không phải vào key fact của source. ⚠ [SEARCH-ONLY], không dùng làm evidence ở RQ02 |
| ACUEval | (chỉ thấy qua ResearchGate) | Hallucination evaluation dựa trên ACU. ⚠ **chưa verify link gốc** |
| TriQua (arXiv 2608.05228) | https://arxiv.org/abs/2608.05228 | Granularity vs context trong factuality evaluation. ⚠ [SEARCH-ONLY] |
| QuanTemp / CheckThat! 2025 Task 3 | (qua search) | Verify claim định lượng/thời gian. Liên quan trực tiếp case "51% YoY". ⚠ **UNVERIFIED**, chưa fetch nguồn chính thức |

---

## 4. Negative finding — điều KHÔNG tìm thấy (quan trọng cho G-05)

**Đã search có chủ đích:** `AlignScore OR SummaC OR MiniCheck OR FActScore applied to slide deck presentation generation evaluation` (2026-08-15).

**Kết quả: KHÔNG tìm thấy nguồn nào áp dụng AlignScore / SummaC / MiniCheck / FActScore lên slide deck.** Toàn bộ kết quả trả về đều là ứng dụng trên summarization, RAG, biomedical abstract, long document — không có domain slide. Kể cả §3.3 (một paper document→slide chuyên biệt) cũng **không** dùng nhóm metric này.

→ **U-2 của RQ06 vẫn mở nguyên vẹn sau Wave 2A.** Sau một vòng search độc lập thứ hai với query khác, kết luận không đổi: **khả năng chuyển giao của F2 sang slide deck là INFERENCE, không phải EXTERNAL EVIDENCE.** Đây là lý do `recommendation.md` yêu cầu một pilot trước khi cam kết, chứ không đề xuất implement thẳng.

**Điều KHÔNG tìm thấy #2:** không có nguồn nào đo **noise floor / repeatability của F2 metric** (NLI/alignment model) như một chỉ số được báo cáo. Nguồn duy nhất RQ02 có về repeatability là RQ06 §3.6 (Tamba 2026), và nó nói về **LLM judge qua API**, không phải về encoder model chạy local. → Cách đo noise floor cho F2 trong `recommendation.md` là **RECOMMENDATION suy ra từ tính chất kỹ thuật**, không có precedent.

**Điều KHÔNG tìm thấy #3:** không có nguồn nào đo lại EQUATE (§2.1) trên **model 2024–2026**. Không thể nói AlignScore/MiniCheck "làm được" hay "không làm được" suy luận số học — **chưa ai đo**.

---

## 5. Tổng kết độ mạnh/yếu của evidence base RQ02

| Kết luận cần chống đỡ | Độ mạnh | Vì sao |
| --- | --- | --- |
| Precision-only metric **không** thấy coverage gap | **Mạnh** | §1.1 có bảng số trực tiếp (FActScore: missing 82.75 > normal 82.58); §1.2 xác nhận bằng khoảng cách completeness 0.688 vs 0.314 |
| Hỏi thẳng LLM "có bỏ sót gì không" là hướng yếu | **Trung bình–mạnh** | 2 nguồn độc lập cùng hướng (§1.2 G-Eval 0.314; §2.2 AbsenceBench 69.6% F1 ở task dễ hơn). ⚠ **Bị §2.5 phản bác một phần** |
| Cần 2 pass ngược chiều mới tách được 3 failure | **Trung bình–mạnh** | §1.2 là hiện thân trực tiếp của kiến trúc này; phần còn lại là INFERENCE cấu trúc của RQ02 |
| AlignScore là candidate rủi ro cao | **Trung bình** | §1.3 (preprint 3 tác giả) nói thẳng; §1.4 lại cho AlignScore điểm tốt ở phản ứng với sửa đúng → **hai nguồn không hoàn toàn cùng chiều** |
| MiniCheck là F2 candidate tốt nhất | **Trung bình** | §1.3 xếp bền nhất; §1.5 là E1 peer-reviewed có repo + số tốc độ. Nhưng vẫn game được (§1.4) và vấp negation (§1.3) |
| Mọi F2 đều game được bằng cách chèn câu từ source | **Trung bình–mạnh** | §1.4, một nguồn nhưng thí nghiệm trực tiếp trên 6 metric |
| Metric kém đi trên claim **bị nén** | **Trung bình** | §1.3, một nguồn (preprint) |
| NLI không tự học suy luận định lượng | **Yếu–trung bình cho 2026** | §2.1 là E1 peer-reviewed nhưng **2019**; không có bản đo lại trên model hiện đại |
| F2 chuyển giao được sang slide deck | **Không có evidence** | §4 — 0 nguồn. Thuần INFERENCE |
| Cách tạo criteria nào tốt nhất (G-06) | **Yếu** | §2.3 cho taxonomy (E2); §2.4 chỉ có phát biểu định tính; **không nguồn nào có so sánh số head-to-head** |
| Chi phí F2 đủ rẻ cho daily | **Trung bình** | §1.5 có số tốc độ từ repo chính chủ (A6000), §2.9 có speedup 6.6×. Nhưng phụ thuộc hạ tầng team chưa xác nhận |
| Noise floor của F2 | **Không có evidence** | §4 #2 |
</content>
</invoke>
