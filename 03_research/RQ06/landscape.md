# RQ06 — Evaluator Landscape (LLM-as-Judge / Evaluator methodology)

**Ngày research:** 2026-08-14
**Trục trả lời:** HOW — nếu một quality không đo deterministic được thì evaluator nào đáng tin?
**Trạng thái:** research output, KHÔNG phải decision. Mọi câu ở đây là `PROJECT SOURCE` / `EXTERNAL EVIDENCE` / `INFERENCE` / `RECOMMENDATION` — xem nhãn tại chỗ.

Chi tiết từng nguồn + evidence level + tag đọc: `evidence.md`. Mapping về Deck Agent: `recommendation.md`.

---

## 0. Kết quả verify "PresentBench" (việc đầu tiên bắt buộc)

**PROJECT SOURCE** — Architecture doc §8 (`kien-truc-do-an.pdf`, trang 6, đã đọc lại nguyên văn 2026-08-14):

> *"Phương pháp: kết hợp MLLM-as-judge (theo hướng PresentBench - checklist chi tiết riêng cho từng input, judge chấm từng mục kèm bằng chứng) với human annotation trên mẫu con để kiểm tra độ tin cậy (đo tương quan điểm judge vs người)."*

**KẾT QUẢ: VERIFIED (E1).** PresentBench là một benchmark có thật, có paper gốc, repo chính chủ, dataset công khai và project page. Chi tiết search + nguồn: `evidence.md` §1.

- **Tên đầy đủ:** *PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation*
- **Tác giả:** Xin-Sheng Chen, Jiayu Zhu, Pei-lin Li, Hanzheng Wang, Shuojin Yang, Meng-Hao Guo
- **arXiv:** 2603.07244, v1 nộp 2026-03-07
- **Repo chính chủ:** `github.com/PresentBench/PresentBench` (Apache-2.0) · Project page: `presentbench.github.io` · Dataset: Hugging Face

**Mô tả §8 khớp với paper thật ở mức nào?** Khớp khá chính xác về cơ chế: PresentBench đúng là dùng checklist **riêng cho từng instance**, mỗi item là một **câu hỏi nhị phân (binary question)**, và judge phải trả verdict **kèm bằng chứng định vị** (localized evidence — vd. slide index liên quan). §8 mô tả đúng phần "checklist chi tiết riêng cho từng input, judge chấm từng mục kèm bằng chứng".

**Nhưng §8 bỏ sót 3 điều quan trọng** (EXTERNAL EVIDENCE, xem `evidence.md` §1):

1. **Chi phí authoring cực lớn:** trung bình **54.1 checklist item / instance**, trên **238 instance**, do **expert soạn thủ công**. Đây không phải "viết vài checklist" — đây là quy mô dataset-building.
2. **Trần độ tin cậy thực tế:** PresentBench đạt Spearman ρ ≈ **0.532** với human ranking, trong khi **human–human upper bound chỉ ≈ 0.664**. Nghĩa là ngay cả method tốt nhất trong so sánh của họ vẫn chỉ đạt ~80% trần người-với-người, và bản thân trần người cũng không cao.
3. **Nó là benchmark end-to-end cho hệ thống sinh slide, không phải một "judge protocol" đóng gói sẵn để gắn vào bất kỳ pipeline nào.** 5 dimension của nó không trùng khít 4 dimension §8.

**Route sang RQ khác** (ghi nhận, KHÔNG tự làm):

| Nội dung PresentBench | Nên route sang |
| --- | --- |
| 5 dimension: Presentation Fundamentals / Visual Design & Layout / Content Completeness / Content Correctness / Content Fidelity — so với 4 dimension của §8 | **RQ01** (WHAT — dimensions) |
| 238 instance, 5 category (Academia, Education, Economics, Talk, Advertising), mỗi instance kèm background material | **RQ07** (benchmark design) |
| Cách tách Completeness / Correctness / Fidelity thành 3 dimension riêng cho content | **RQ02** (content fidelity) |
| Dimension "Visual Design and Layout" (material-independent) | **RQ04** (design consistency) |
| Leaderboard 9 hệ thống, trong đó NotebookLM dẫn đầu | **RQ08** (baseline landscape) |

**Cảnh báo phân biệt entity:** search có trả về các benchmark tên gần giống nhưng **khác hẳn** — `UniPPTBench` (arXiv 2605.17356), `SlidesBench` (trong paper AutoPresent, arXiv 2501.00912), `PPTEval` (trong paper PPTAgent, arXiv 2501.03936), `SlidesGen-Bench` (arXiv 2601.09487, chưa verify sâu). Không được gộp chung. `SlideBench` (số ít) là cách viết sai của `SlidesBench`.

---

## 1. Khung phân tích: 2 trục độc lập, đừng gộp

**INFERENCE** — Trong hầu hết tài liệu (kể cả §8), "MLLM-as-judge" bị dùng như một khối duy nhất. Thực tế có **2 trục vuông góc nhau**, và phần lớn quyết định thiết kế nằm ở trục 2:

```
TRỤC 1 — AI NÀO SINH RA ĐIỂM?        TRỤC 2 — ĐIỂM ĐƯỢC LẤY RA THẾ NÀO?
(evaluator family)                    (elicitation protocol)

F1  Deterministic / rule-based        P1  Pointwise (absolute scoring)
F2  Model-based automatic metric      P2  Pairwise comparison
F3  LLM-as-a-Judge (text)             P3  Batch ranking
F4  MLLM-as-a-Judge (multimodal)      P4  Binary checklist per instance
F5  Human evaluation                  P5  Generic rubric (Likert)
F6  Hybrid / staged                   P6  Single judge vs panel (jury)
```

Ví dụ: PresentBench = F4 × P4. PPTEval = F4 × P5(pointwise 1–5). MLLM-as-a-Judge benchmark khảo sát F4 × {P1, P2, P3}. Cùng một family, protocol khác nhau → độ tin cậy khác nhau rất xa.

---

## 2. Trục 1 — Evaluator families

### F1. Deterministic / rule-based evaluator

**Cách hoạt động:** kiểm tra bằng code, không có model trong vòng lặp. Trên domain slide: schema validation, text overflow / element overlap / margin / in-bounds check, tỷ lệ element dùng đúng design token, đếm slide, mở được file hay không, diff state trước/sau edit.

**EXTERNAL EVIDENCE:** SlidesBench (trong AutoPresent, arXiv 2501.00912) chứng minh phần lớn "similarity với reference slide" **đo được thuần bằng code**: element matching, content similarity (sentence-transformer embedding cho text, CLIP cho ảnh), color similarity bằng công thức **CIEDE2000**, position similarity bằng Manhattan distance chuẩn hóa. Không cần judge cho nhóm này.

| | |
| --- | --- |
| **Mạnh** | Rẻ gần bằng 0, latency ~ms, **hoàn toàn lặp lại được** (repeatable), không bias, chạy được mỗi commit, không cần API key |
| **Yếu** | Chỉ đo được thứ có định nghĩa hình thức. Không đo được "nội dung có đúng nguồn không", "cách chia slide có hợp lý không". Rule sai → đo sai một cách im lặng |
| **Điều kiện áp dụng** | Quality có thể quy về một predicate trên artifact (Deck IR, geometry, file PPTX) |

**INFERENCE:** đây là family duy nhất thỏa mãn đồng thời "đủ rẻ để chạy mỗi commit" và "đủ ổn định để so version". Mọi thứ đẩy được xuống F1 thì nên đẩy — không phải vì nó "tốt hơn" mà vì nó là thứ duy nhất chịu được tần suất daily.

### F2. Automatic semantic / model-based metric (không phải LLM-judge)

Đây là family mà task brief gọi là "metric tự động". Thuật ngữ cộng đồng thực dùng chia làm 3 nhóm:

**F2a — Embedding-based similarity.** So sánh output với reference bằng embedding (BERTScore và họ hàng). Cần có reference — Deck Agent thường **không có** deck reference "đúng".

**F2b — NLI / alignment-based factual consistency.** Kiểm tra từng câu output có được **entail** bởi source document không.
- **SummaC** (Laban et al., TACL 2022) — benchmark 6 dataset inconsistency detection; `SummaC-Conv` chia document thành sentence unit rồi aggregate điểm NLI theo cặp câu.
- **AlignScore** (Zha et al., ACL 2023) — một alignment function thống nhất, train trên ~4.7M example từ 7 task (NLI, QA, paraphrase, fact verification, IR, STS, summarization); SOTA trên SummaC và TRUE.

**F2c — Decomposition-based factuality.** **FActScore** (Min et al., EMNLP 2023): tách generation thành **atomic fact** (mỗi mệnh đề chứa đúng một thông tin), gán nhãn nhị phân cho từng atomic fact, điểm = % atomic fact được knowledge source support. Estimator tự động của họ báo sai số < 2% so với human FActScore.

| | |
| --- | --- |
| **Mạnh** | Rẻ hơn LLM-judge nhiều bậc, **deterministic nếu pin model weights** (chạy local → lặp lại được thật), có paper validate trên benchmark chuẩn, không có self-preference bias |
| **Yếu** | Chỉ phủ được **text–source relationship**. Mù hoàn toàn với visual/layout. F2a cần reference. F2b/F2c giả định source là văn bản có thể entail được — bảng, biểu đồ, ảnh thì yếu |
| **Điều kiện áp dụng** | Dimension là "output có bám nguồn không" (fidelity, hallucination), source ở dạng text |

**INFERENCE quan trọng — đây là family bị §8 bỏ sót hoàn toàn.** §8 nhảy thẳng từ "khó đo" sang "MLLM-as-judge", không xét F2. Với Deck Agent, phần lớn "content fidelity" là quan hệ **text-của-slide ↔ text-của-PDF-nguồn**, tức là đúng vùng F2b/F2c mạnh nhất. FActScore đặc biệt đáng chú ý vì cơ chế của nó (**tách thành mệnh đề nhị phân rồi verify từng cái**) chính là cùng một ý tưởng với "checklist per instance" của PresentBench — nhưng **sinh tự động từ output** thay vì **soạn tay từ input**.

**Ghi chú lịch sử (E1, để hiệu chỉnh kỳ vọng):** SummEval (Fabbri et al., TACL 2021) re-evaluate 14 automatic metric trên bộ human judgment lớn nhất cho CNN/DailyMail và đặt mục tiêu tường minh là "advance research in developing evaluation metrics that **better** correlate with human judgments" — tức là tại thời điểm đó các metric sẵn có (gồm ROUGE) chưa đủ tương quan. Đừng dùng F2 như ground truth; dùng như **signal rẻ, ổn định, chạy được hằng ngày**.

### F3. LLM-as-a-Judge (text-only)

**Cách hoạt động:** đưa output (và tùy chọn: input, reference, rubric) vào một LLM mạnh, yêu cầu chấm điểm hoặc so sánh.

**EXTERNAL EVIDENCE nền tảng:**
- **Zheng et al., NeurIPS 2023 (MT-Bench / Chatbot Arena, arXiv 2306.05685):** judge mạnh như GPT-4 đạt **>80% agreement** với human preference — *"the same level of agreement between humans"*. Đồng thời chính paper này đặt tên và đo 3 bias: **position, verbosity, self-enhancement**, cộng với **limited reasoning ability**.
- **G-Eval (Liu et al., EMNLP 2023, arXiv 2303.16634):** form-filling + chain-of-thought, đạt Spearman **0.514** với human trên task summarization, vượt các method trước đó. CoT cải thiện tương quan trên mọi dimension.

| | |
| --- | --- |
| **Mạnh** | Đo được thứ không có định nghĩa hình thức; không cần reference; setup nhanh; giải thích được (judge xuất lý do) |
| **Yếu** | Bias hệ thống (§4); **không reproducible ngay cả ở temperature=0** (§4.5); nhạy prompt/rubric; tốn tiền + latency; agreement con số đẹp nhưng phụ thuộc mạnh vào protocol |
| **Điều kiện áp dụng** | Dimension mang tính ngữ nghĩa/phán đoán, artifact biểu diễn được dưới dạng text |

### F4. MLLM-as-a-Judge (multimodal)

**Cách hoạt động:** như F3 nhưng judge nhìn được ảnh/PDF. Với deck, đây là điều kiện cần để chấm bất cứ thứ gì thuộc về **cái mắt thấy** (layout, tràn chữ, tương phản, cân đối, nhất quán thị giác).

**EXTERNAL EVIDENCE:**
- **Chen et al., ICML 2024 (MLLM-as-a-Judge, arXiv 2402.04788)** khảo sát MLLM judge trên 3 task: **Scoring Evaluation, Pair Comparison, Batch Ranking**. Kết luận định tính rõ ràng: MLLM thể hiện *"remarkable human-like discernment in Pair Comparison"* nhưng *"significant divergence from human preferences in Scoring Evaluation and Batch Ranking"*. Paper cũng nêu các failure mode: **diverse biases, hallucinatory responses, inconsistencies in judgment** — kể cả với GPT-4V.
  *(Con số % agreement cụ thể: hai lần trích xuất cho ra 79.3% và 78% cho Pair Comparison, ~70% tổng thể — **không nhất quán, phải đọc lại bản gốc trước khi trích vào báo cáo**. Kết luận định tính thì nhất quán.)*
- Trong domain slide: **PPTEval** (trong PPTAgent, arXiv 2501.03936) dùng GPT-4o làm judge, **render slide thành ảnh** vì presentation *"cannot be easily comprehended in a plain text format"*, chấm 3 dimension (Content / Design / Coherence) thang **1–5**.
- **PresentBench** cũng là F4: chuyển deck sang PDF rồi đưa cho judge (paper báo dùng model họ Gemini flash-preview), **mỗi checklist item một call riêng** để *"reduce cognitive burden on the judge"*.

| | |
| --- | --- |
| **Mạnh** | Family **duy nhất** trong nhóm tự động chạm được vào visual quality mà không cần viết rule cho từng loại lỗi |
| **Yếu** | Kế thừa toàn bộ bias của F3, **cộng thêm** visual hallucination; đắt và chậm hơn F3 (ảnh tốn nhiều token); phần layout mà nó chấm thì phần lớn F1 đã đo được chính xác hơn |
| **Điều kiện áp dụng** | Quality **thực sự** cần nhìn và **không** quy được về rule hình học |

**INFERENCE:** §8 xếp cả 4 dimension vào MLLM-judge. Nhưng theo audit RQ00 (mục 2) và SlidesBench, phần lớn "Ready-to-use" và một phần lớn "Design consistency" (NFR-52: `% element dùng đúng token`) là **F1 thuần**. Dùng F4 cho những phần này là trả giá đắt để lấy một phép đo *kém chính xác hơn* rule.

### F5. Human evaluation

**Cách hoạt động:** người chấm theo rubric/checklist, hoặc xếp hạng so sánh.

**EXTERNAL EVIDENCE về mức agreement thực tế giữa người với người trong đúng domain này** — đây là con số hiệu chỉnh kỳ vọng quan trọng nhất của cả RQ06:

| Nguồn | Setting | Agreement người–người |
| --- | --- | --- |
| PPTEval (arXiv 2501.03936) | 4 graduate student chấm 250 presentation, thang 1–5, 3 dimension | **Fleiss' κ ≈ 0.59** trung bình (Content 0.61, Design 0.61, Coherence 0.54) — paper gọi là "satisfactory" |
| PresentBench (arXiv 2603.07244) | ~24 instance (~10% dataset), xếp hạng output của 5 hệ thống | **Spearman ρ ≈ 0.664** (đây là *trần trên*, human-vs-human) |
| SlidesBench (arXiv 2501.00912) | reference-free design metric vs human study | **ICC 73.8%–85.3%** |

| | |
| --- | --- |
| **Mạnh** | Là thứ duy nhất định nghĩa được "đúng" cho dimension chủ quan; bắt buộc phải có nếu muốn claim rằng judge đáng tin |
| **Yếu** | Đắt, chậm, **không lặp lại được** (chấm lại lần 2 ra khác), agreement thực tế chỉ ~0.6 κ trong domain này → **human cũng là noisy measurement, không phải ground truth tuyệt đối** |
| **Điều kiện áp dụng** | Validation (calibrate judge), final/thesis evidence, tie-break. **Không** dùng cho daily tracking |

### F6. Hybrid / staged evaluation

Không phải một evaluator, mà là một **kiến trúc**: xếp tầng F1 → F2 → F3/F4 → F5, mỗi tầng chỉ nhận phần mà tầng dưới không giải quyết được, và tầng trên dùng để **calibrate** tầng dưới.

Đây là hình thái mà cả PresentBench (checklist judge + human validation trên mẫu con), PPTAgent (MLLM judge + human agreement study), SlidesBench (rule-based + VLM rubric + human ICC), và NFR-53 (`checklist theo instance + human corr. trên mẫu con`) đều hội tụ về. **INFERENCE:** sự hội tụ này là bằng chứng mạnh nhất trong RQ06 — không có nguồn nghiêm túc nào trong domain slide dùng judge tự động *một mình* mà không có lớp human validation.

---

## 3. Trục 2 — Protocol

### 3.1 Pointwise (absolute) vs Pairwise vs Ranking

| Protocol | Bằng chứng | Ưu | Nhược |
| --- | --- | --- | --- |
| **Pointwise / absolute** | G-Eval ρ=0.514; PPTEval thang 1–5; PresentBench (nhị phân theo item) | Cho ra **con số tuyệt đối theo thời gian** → so được v0.2 vs v0.3 vs v0.9 mà không cần chạy lại v0.2 | Chen et al. (ICML 2024): scoring **lệch human rõ rệt**; điểm bị nén/ neo (anchoring), khó phân biệt hai output gần nhau |
| **Pairwise** | Chen et al.: pair comparison là task MLLM judge **giỏi nhất**; PairS (Liu et al., COLM 2024, arXiv 2403.16950) formulate evaluation thành ranking bằng pairwise local comparison + uncertainty-guided search, *"substantially outperforming traditional direct scoring"* | Bám human tốt nhất; nhạy với khác biệt nhỏ | **Position bias mạnh nhất ở đây** (JudgeSense 2026: pairwise *"consistently exhibit position bias"*); chi phí O(n²) hoặc cần search; **không cho điểm tuyệt đối** → khó vẽ đường tiến bộ theo thời gian |
| **Batch ranking** | Chen et al.: cùng nhóm "divergence from human" với scoring | Rẻ hơn pairwise đầy đủ | Kém tin cậy nhất trong 3 |

**INFERENCE — đây là một tension thật, không có lời giải sạch:** mission của Deck Agent (`EVALUATION_MISSION.md`) cần **cả hai**: "hôm nay có tốt hơn hôm qua không" (cần điểm tuyệt đối, so được xuyên thời gian) và "có tốt hơn baseline không" (pairwise hợp hơn). Protocol tốt nhất về reliability (pairwise) lại là protocol tệ nhất cho tracking theo thời gian.

**Lối thoát mà PresentBench dùng:** binary checklist là **pointwise nhưng ở mức item**, không phải mức deck. Judge không phải trả lời "deck này mấy điểm trên thang 5" (chủ quan, trôi) mà "yêu cầu cụ thể này có được đáp ứng không: có/không" (verifiable). Điểm deck là **tỷ lệ item pass** — vẫn là số tuyệt đối, so được xuyên thời gian, nhưng từng thành phần thì kiểm chứng được. Đây là lý do kỹ thuật thật sự khiến checklist đáng chú ý, không phải vì nó "chi tiết hơn".

### 3.2 Checklist-per-instance vs Generic rubric — câu hỏi task brief hỏi trực tiếp

**PROJECT SOURCE** — `03-research-evaluation.pdf`: *"Bộ case / checklist theo từng instance hay rubric chung? Ưu nhược?"*

**EXTERNAL EVIDENCE cho phía checklist-per-instance:**

| Nguồn | Bằng chứng cụ thể |
| --- | --- |
| **PresentBench** (E1, 2026) | Checklist per-instance (54.1 item/instance, binary) đạt ρ=**0.532** vs PPTEval (rubric chung) **0.303** và MLLM-as-a-Judge Ranking **0.258**. Trần người: 0.664 |
| **TICK** (E1, arXiv 2410.03608) | Checklist **do LLM tự sinh** theo từng instruction, dạng YES/NO. Exact agreement với human preference tăng **46.4% → 52.2%** so với để LLM chấm điểm trực tiếp |
| **HealthBench** (E1, OpenAI technical report) | 5.000 hội thoại, **mỗi hội thoại một rubric riêng do bác sĩ viết**, tổng **48.562 rubric criteria**, 262 bác sĩ, mỗi example được ≥3 bác sĩ adjudicate qua 3 phase; grader là model (GPT-4.1). Criteria có **trọng số điểm** theo mức quan trọng |
| **FActScore** (E1, EMNLP 2023) | Cùng nguyên lý ở mức thấp hơn: tách thành atomic fact nhị phân rồi verify từng cái, thay vì một phán đoán tổng thể |
| **UniPPTBench** (E1 preprint, 2026) | *"strong performance on generic presentation-quality metrics does not necessarily imply strong task fulfillment in grounded scenarios"* — rubric chung không phát hiện được failure về grounding |

**Bảng so sánh:**

| | Checklist per instance | Generic rubric |
| --- | --- | --- |
| **Cái nó đo được** | "Deck này có giữ đúng con số 12,4% ở trang 7 của PDF này không" — yêu cầu **phụ thuộc nội dung nguồn** | "Layout có cân đối không", "text có dễ đọc không" — yêu cầu **độc lập nội dung** |
| **Human alignment** | Cao hơn (0.532 vs 0.303 trong so sánh PresentBench; 52.2% vs 46.4% trong TICK) | Thấp hơn |
| **Chi phí soạn** | **Rất cao, one-time per instance.** PresentBench: expert soạn tay 54.1 item × 238 instance. HealthBench: 262 bác sĩ, 48.562 criteria | Gần 0, viết một lần dùng mãi |
| **Chi phí chạy** | Cao: PresentBench gọi judge **một call cho mỗi item** → ~54 call/deck | Thấp: 1–3 call/deck |
| **Chống trôi (drift) giữa các version** | Tốt: item cố định, item nào fail thấy ngay | Kém: điểm 1–5 trôi theo mood của judge/prompt |
| **Diagnosticity** | Cao — biết chính xác *cái gì* hỏng | Thấp — biết "3.5/5" mà không biết vì sao |
| **Khi thêm input mới vào benchmark** | Phải soạn checklist mới → benchmark khó mở rộng | Miễn phí |
| **Rủi ro** | Checklist tự nó có thể sai/thiên lệch; **đóng băng định nghĩa "tốt"** vào thời điểm soạn; over-fit vào những gì người soạn nghĩ tới | Nhạy prompt (§4.4); judge tự định nghĩa lại rubric mỗi lần |

**INFERENCE — trục phân chia đúng không phải "checklist vs rubric" mà là "material-dependent vs material-independent":**

PresentBench tự chia 5 dimension của nó thành 2 nhóm: **material-independent** (Presentation Fundamentals, Visual Design & Layout) và **material-dependent** (Content Completeness, Content Correctness, Content Fidelity). Ánh xạ sang Deck Agent:

- Dimension **material-dependent** (fidelity, coverage, không bịa — FR-03, NFR-53): **bắt buộc** phải per-instance, vì không có cách nào viết một rubric chung diễn đạt được "giữ đúng con số ở trang 7 của *tài liệu này*". Rubric chung về mặt logic **không thể** đo được nhóm này.
- Dimension **material-independent** (design consistency, readability, overflow — FR-22, NFR-52): rubric chung đủ, **và phần lớn còn đẩy được xuống F1 (rule)**, rẻ hơn cả rubric.

Đây là câu trả lời có evidence cho task brief: **không phải chọn một, mà phân theo bản chất dimension.**

**Đường thứ ba, quan trọng cho một đồ án 4 người:** TICK cho thấy checklist **sinh tự động bằng LLM** từ instruction/source cũng cải thiện agreement (46.4→52.2%), tuy mức cải thiện nhỏ hơn nhiều so với khoảng cách của PresentBench với checklist expert-authored. Đây là trade-off cost/quality có thật, có evidence ở cả hai đầu.

### 3.3 Single judge vs Panel (jury) — evidence MÂU THUẪN

| Phía ủng hộ panel | Phía phản đối panel |
| --- | --- |
| **PoLL** (Verga et al., Cohere, arXiv 2404.18796): panel gồm nhiều model **nhỏ** vượt một judge lớn, **ít intra-model bias hơn** nhờ các model thuộc **họ rời nhau (disjoint families)**, và **rẻ hơn >7×**. Test trên 3 judge setting × 6 dataset | **"Nine Judges, Two Effective Votes"** (arXiv 2605.29800, 2026): panel 9 LLM từ 7 họ, trên 3 dataset NLI (100 human annotation/item) → 9 judge chỉ cho lượng thông tin bằng **~2 vote độc lập**; ~75% độ độc lập danh nghĩa mất đi vì **các model sai cùng chỗ**; accuracy panel thấp hơn ideal 8–22 điểm phần trăm; **judge đơn tốt nhất bằng hoặc hơn cả panel**; thêm judge hoặc aggregation thông minh hơn chỉ bù được tối đa 11% khoảng cách |

**Đánh giá:** PoLL là paper từ một lab công nghiệp (Cohere), có nhiều tác giả, 2024. Paper phản bác là **preprint 2026 một tác giả, chưa peer-review**, và test trên NLI (task có ground truth khách quan) chứ không phải open-ended generation. **Chưa thể kết luận.** Ghi vào unresolved question — xem `recommendation.md` §3.

---

## 4. Bias, reliability, và cách giảm

### 4.1 Self-preference / self-enhancement bias

**PROJECT SOURCE** — §8: *"nêu rõ hạn chế self-preference bias của MLLM-judge (nên dùng model khác họ để generate và để judge)"*.

**EXTERNAL EVIDENCE — §8 đúng hướng nhưng chưa đủ:**

- **Zheng et al. (NeurIPS 2023)** đặt tên "self-enhancement bias" và liệt nó vào 3 bias chính.
- **Panickssery et al., NeurIPS 2024 (arXiv 2404.13076), "LLM Evaluators Recognize and Favor Their Own Generations":** LLM (GPT-4, Llama 2) có khả năng **nhận ra output của chính mình** ở mức trên ngẫu nhiên; khi fine-tune, tồn tại **tương quan tuyến tính giữa năng lực tự-nhận-diện và độ mạnh của self-preference**, và tương quan này chịu được các confounder đơn giản. → **Đây chính là bằng chứng E1 cho cơ chế mà §8 giả định**: nếu bias đến từ tự-nhận-diện, thì tách generator và judge sang họ model khác nhau tấn công đúng vào cơ chế đó.
- **Yang et al. 2026 (arXiv 2604.22891), "Quantifying and Mitigating Self-Preference Bias of LLM Judges":** khảo sát 20 LLM chủ lưu; phát hiện **năng lực model không tương quan (có khi tương quan âm) với việc SPB thấp** → *"dùng model mạnh nhất làm judge" không phải cách giảm SPB*. Mitigation họ đề xuất là **structured multi-dimensional evaluation** (phân rã theo cognitive load), giảm SPB trung bình **31.5%** → tức là **cách hỏi** quan trọng ngang **model nào hỏi**.
- **PoLL:** panel từ họ rời nhau giảm intra-model bias.

**INFERENCE — mức độ nguy hiểm của SPB với Deck Agent khác nhau theo use case:**

| Use case | SPB có nguy hiểm không |
| --- | --- |
| **Development tracking** (Deck Agent v0.2 vs v0.3, cùng generator model) | **Thấp.** SPB là một offset gần như hằng số áp lên cả hai nhánh → phần lớn triệt tiêu khi lấy hiệu. Nhưng không triệt tiêu nếu team **đổi model generator** giữa hai version — lúc đó SPB biến thành confounder trực tiếp |
| **Comparative evaluation** (Deck Agent vs single-shot LLM vs competitor như Gamma/NotebookLM) | **Cao.** Các nhánh dùng model khác nhau → SPB dịch chuyển kết quả theo hướng có lợi cho nhánh cùng họ với judge. Đây là chỗ claim của đồ án dễ bị phản biện nhất |
| **Absolute number trong báo cáo** ("fidelity 86.7") | **Cao** — con số tuyệt đối không có ý nghĩa nếu không nêu judge và giới hạn |

**Mitigation có evidence, xếp theo sức mạnh:**
1. Judge thuộc **họ model khác** generator (Panickssery et al. → tấn công đúng cơ chế).
2. **Phân rã evaluation thành checklist/dimension nhị phân** thay vì một phán đoán tổng thể (Yang et al. 2026: −31.5% SPB; cũng chính là điều PresentBench làm).
3. **Panel đa họ** (PoLL) — nhưng xem §3.3, evidence mâu thuẫn.
4. **Human validation trên mẫu con** — không giảm bias nhưng **đo được** nó còn lại bao nhiêu. Đây là mitigation duy nhất defensible trong báo cáo.

### 4.2 Position / order bias

**EXTERNAL EVIDENCE:**
- **Wang et al., ACL 2024 (arXiv 2305.17926), "Large Language Models are not Fair Evaluators":** thứ hạng có thể bị "hack" chỉ bằng cách **đổi thứ tự xuất hiện**; GPT-4 thiên vị vị trí đầu, ChatGPT thiên vị vị trí thứ hai. Đề xuất 2 chiến lược: **MEC (Multiple Evidence Calibration** — bắt model sinh evidence trước rồi mới cho điểm**)** và **BPC (Balanced Position Calibration** — chạy cả hai thứ tự rồi cân bằng**)**. Code: `github.com/i-Eval/FairEval`.
- **Zheng et al.:** cùng phát hiện, đề xuất swap.
- **JudgeSense (arXiv 2604.23478, 2026):** pairwise task *"consistently exhibit position bias"* — vẫn đúng ở thế hệ model mới.
- **Ngược lại, Soumik 2026 (arXiv 2604.23178):** đo được position bias **≤ 0.04**, nhỏ hơn nhiều so với style bias (0.10–0.76). → position bias có thể đã giảm ở model hiện đại, nhưng chỉ trong setting cụ thể của paper đó.

**Mitigation:** chỉ áp dụng khi dùng pairwise/ranking. Chạy cả 2 thứ tự (BPC) là biện pháp rẻ và gần như bắt buộc. **Với protocol binary-checklist pointwise thì bias này về cơ bản không tồn tại** — một lợi thế nữa của checklist mà §8 không nêu.

### 4.3 Verbosity / style / length bias

**EXTERNAL EVIDENCE:**
- **Zheng et al.:** verbosity bias có thật; chứng minh bằng "repetitive list attack" đánh lừa nhiều judge; GPT-4 chống đỡ tốt hơn các model khác nhưng không miễn nhiễm.
- **Soumik 2026 (arXiv 2604.23178)** — kết quả đáng chú ý nhất và **ít được nghiên cứu nhất**: **style bias là bias trội** (0.10–0.76 tùy model, **thiên vị markdown hơn văn xuôi thuần**), lớn hơn hẳn position bias (≤0.04). Verbosity bias thì **không đồng nhất**: một số model thích câu trả lời dài (+0.24 đến +0.44), một số thích ngắn (−0.12), một số trung tính (−0.04).

**INFERENCE — rủi ro cụ thể cho Deck Agent:** deck có mật độ chữ cao trông "đầy đủ" hơn với judge, trong khi FR-22/Ready-to-use và mục tiêu "dùng được ngay" muốn deck **súc tích**. Nếu judge có length bias dương, nó sẽ **thưởng cho đúng cái mà sản phẩm coi là lỗi**. Đây là một failure mode cụ thể của domain, không phải rủi ro chung chung — và nó **đo được**: sinh cặp deck cùng nội dung, khác độ dài chữ, xem judge có thiên lệch không.

### 4.4 Prompt & rubric sensitivity

**EXTERNAL EVIDENCE — JudgeSense (arXiv 2604.23478, 2026):** benchmark gồm các cặp prompt-paraphrase đã hand-validate (factuality, coherence, relevance, preference). Kết quả:
- Verdict của judge **thay đổi dưới các cách diễn đạt tương đương về ngữ nghĩa**.
- **Model lớn/mới hơn KHÔNG ổn định hơn** — *"model scale is not a reliable proxy for consistency; the largest and newest models are not the most consistent"*.
- Coherence là dimension bất ổn nhất; factuality ổn định nhất.

**INFERENCE:** điều này có hệ quả vận hành trực tiếp — **judge prompt phải được version hóa và đóng băng như một phần của benchmark**. Sửa một chữ trong rubric giữa v0.5 và v0.6 làm mọi so sánh trước đó vô hiệu. Đây là thứ dễ bị bỏ sót nhất trong một đồ án và là lý do chính khiến "điểm hôm nay so không được với điểm tuần trước".

**INFERENCE thêm:** binary item ổn định hơn thang Likert vì thang 1–5 cho phép trôi dưới ngưỡng (drift 4→3 vẫn là một thay đổi được ghi nhận), còn YES/NO chỉ đổi khi vượt ranh giới quyết định thật.

### 4.5 Judge variance / không reproducible

**EXTERNAL EVIDENCE — Tamba 2026 (arXiv 2606.26185), "Necessary but Not Sufficient: Temperature Control and Reproducibility in LLM-as-Judge Safety Evaluations"** (preprint 1 tác giả, test trên codebase safety-eval mã nguồn mở của Japan AISI):
- Harness không set temperature → provider mặc định 1.0 → item gần ranh giới quyết định **lật pass/fail giữa các lần chạy giống hệt nhau, disagreement lên tới ~50% qua 20 lần chạy**.
- **Pin temperature=0 giảm nhưng KHÔNG loại bỏ**: qua 690 API call trên 2 provider, 3 tier model, 5 cấu hình sampling, vẫn còn **1–2 trên 7 item borderline không reproducible ngay cả với greedy decoding (top_k=1)**.
- Khuyến nghị: **coi grader disagreement là health metric hạng nhất, báo cáo song song với điểm số**.
- Cảnh báo bổ sung: một số model thế hệ mới đã **bỏ hẳn tham số temperature**, làm mitigation chính không còn áp dụng được.

**INFERENCE — đây là phát hiện nguy hiểm nhất cho mission của Deck Agent.** Mission là "chứng minh cải thiện mỗi ngày". Nếu judge noise floor là ±X điểm mà team không biết X, thì mọi delta nhỏ hơn X đều là tiếng ồn được đọc thành tiến bộ. **Trước khi dùng bất kỳ judge nào để so version, phải đo noise floor của chính nó: chạy cùng một deck N lần, tính spread.** Không có bước này thì toàn bộ development tracking không có nền.

### 4.6 Tổng hợp bias

| Bias | Ảnh hưởng protocol nào | Mitigation có evidence | Còn lại rủi ro gì |
| --- | --- | --- | --- |
| Self-preference | Mọi protocol; nặng nhất ở comparative eval | Judge khác họ generator; phân rã checklist (−31.5%); panel đa họ | Không loại bỏ hết; phải đo bằng human sample |
| Position/order | Pairwise, batch ranking | BPC (chạy 2 thứ tự), MEC; **hoặc chuyển sang pointwise checklist** | Nhỏ ở model mới theo một nguồn, nhưng vẫn có |
| Verbosity/length | Mọi protocol | Kiểm soát độ dài; đo bias bằng cặp đối chứng | Không đồng nhất giữa các model → phải đo trên chính judge đang dùng |
| **Style** (markdown/format) | Mọi protocol | **Chưa có mitigation đã được validate** | Là bias trội theo Soumik 2026 nhưng ít được nghiên cứu — **rủi ro mở** |
| Prompt/rubric sensitivity | Mọi protocol | Đóng băng + version hóa judge prompt; dùng binary thay Likert | Model lớn hơn không cứu được |
| Run-to-run variance | Mọi protocol | Pin temperature/seed; chạy lặp; báo cáo disagreement | **Không loại bỏ được**; temperature đang bị bỏ ở model mới |

---

## 5. Validate judge bằng human: metric, ngưỡng, cỡ mẫu

### 5.1 Metric nào được dùng thực tế

| Loại | Metric | Ai dùng |
| --- | --- | --- |
| Tương quan judge ↔ human ranking | **Spearman ρ** | PresentBench (0.532 vs trần người 0.664), G-Eval (0.514) |
| Agreement giữa nhiều human rater | **Fleiss' κ** | PPTEval (≈0.59) |
| Agreement rater trên thang liên tục | **ICC** | SlidesBench (73.8–85.3%) |
| Agreement judge ↔ human trên nhãn phân loại | **Cohen's κ**, % agreement | Zheng et al. (>80% agreement); Soumik 2026 (κ=0.549) |
| Reliability dữ liệu coding nói chung | **Krippendorff's α** | Chuẩn trong content analysis |

### 5.2 Ngưỡng nào coi là "đủ tin"

**EXTERNAL EVIDENCE (E2 — trích qua nguồn thứ cấp, CHƯA đọc bản gốc):** Krippendorff (2004, *Reliability in Content Analysis: Some Common Misconceptions and Recommendations*, Human Communication Research 30(3):411–433 — **paper đã verify tồn tại**, nhưng bản PDF không truy cập được) được trích rộng rãi với ngưỡng: **α ≥ 0.800** = tin được; **0.667 ≤ α < 0.800** = chỉ rút kết luận **tạm thời**; **α < 0.667** = loại. ⚠ Ngưỡng này phải verify lại từ bản gốc trước khi trích vào báo cáo.

**INFERENCE — hiệu chỉnh kỳ vọng, rất quan trọng:** đối chiếu ngưỡng đó với thực tế domain: PPTEval đo được κ ≈ **0.59 giữa 4 human rater** — **dưới cả ngưỡng "tentative"**. Nghĩa là: trong đánh giá chất lượng presentation, **con người cũng không đồng thuận đủ để đạt chuẩn content-analysis**. Hệ quả: (a) không nên đặt mục tiêu judge đạt α ≥ 0.8 với human — đó là mục tiêu không khả thi khi trần người thấp hơn thế; (b) chỉ tiêu đúng là **so judge với trần người-người trên cùng dữ liệu**, không so với một hằng số tuyệt đối. PresentBench làm đúng điều này (0.532 so với trần 0.664).

**EXTERNAL EVIDENCE cảnh báo về chính cách validate này — Elangovan et al. (arXiv 2410.03775), "Beyond correlation":** khi tỷ lệ mẫu có **bất định trong nhãn người** cao, nhãn máy có thể **trông như** tương quan tương đương hoặc tốt hơn tương quan người–người, dù thực chất không phải. Các hệ số như Krippendorff α / Randolph κ vốn thiết kế để đo reliability của **người**, mang giả định về hành vi người mà hệ thống tự động không thỏa. → **Không được kết luận "judge đủ tốt" chỉ bằng một con số correlation.**

### 5.3 Cần bao nhiêu mẫu human? (câu hỏi task brief hỏi trực tiếp, gắn NFR-53)

Đây là chỗ có **một câu trả lời có phương pháp thật**, không phải ước lượng cảm tính.

**EXTERNAL EVIDENCE — Calderon, Reichart & Dror (arXiv 2501.10970), "The Alternative Annotator Test for LLM-as-a-Judge: How to Statistically Justify Replacing Human Annotators with LLMs":**

| Tham số | Khuyến nghị của paper |
| --- | --- |
| Số annotator | **≥ 3.** Lý do nêu rõ: với 2 người, thủ tục chỉ kiểm tra LLM giống người nào hơn, **không có tín hiệu consensus**; từ 3 người trở lên mới đánh giá được LLM có đại diện cho *nhóm* không |
| Số instance | **50–100** cho ε = 0.2; **100–150** cho ε = 0.1; **tối thiểu 30** để thỏa giả định normality của t-test |
| Tiêu chí pass | **winning rate ω ≥ 0.5** — LLM "thắng" đa số annotator người |
| Mức ý nghĩa | FDR **q = 0.05** |
| ε (điều chỉnh cost-benefit ưu ái LLM) | 0.2 với expert annotator; 0.15 với skilled; 0.1 với crowd-worker. **IAA thấp → phải dùng ε nhỏ hơn** |

**Đối chiếu với thực tế domain:** PresentBench dùng **~24 instance (~10% dataset)** cho human study — **thấp hơn khuyến nghị tối thiểu 30 của alt-test**. PPTEval dùng 250 presentation × 4 annotator (rộng rãi hơn nhiều, nhưng để đo agreement người–người chứ không phải để justify thay người bằng LLM).

**INFERENCE:** với Deck Agent, "50–100 instance × ≥3 annotator" là con số **có nguồn**, không phải phỏng đoán — và cũng là con số **khả thi** cho một team 4 người nếu mỗi instance chỉ chấm 3–5 dimension chứ không phải 54 checklist item. Lưu ý ε: nếu người chấm là sinh viên trong team (không phải expert), IAA sẽ thấp → phải dùng ε nhỏ → **cần nhiều mẫu hơn**, nghiêng về đầu 100–150.

---

## 6. Các candidate nội bộ rơi vào đâu trong landscape này

Bước này thực hiện **sau** khi đã dựng landscape, đúng thứ tự yêu cầu (tránh confirmation research).

| Candidate nội bộ (PROJECT SOURCE) | Vị trí trong landscape | Landscape nói gì |
| --- | --- | --- |
| **§8:** MLLM-as-judge cho cả 4 dimension | F4 × (protocol không nêu rõ) | **Quá rộng.** Ready-to-use (FR-22 overflow) và phần lớn Design consistency là F1. Content fidelity còn có F2b/F2c (AlignScore/SummaC/FActScore) mà §8 không xét |
| **§8:** "theo hướng PresentBench" | F4 × P4 | **Entity verified.** Nhưng §8 mô tả nó như một *hướng làm*, còn thực tế nó là một benchmark hoàn chỉnh với chi phí authoring rất lớn (54.1 item × 238 instance, expert-authored) |
| **§8:** "dùng model khác họ để generate và judge" | Mitigation SPB | **Đúng hướng, có E1 hậu thuẫn** (Panickssery et al.). Nhưng **không đủ**: không chạm tới style/verbosity/position bias, và Yang et al. 2026 cho thấy cách *hỏi* quan trọng ngang model nào hỏi |
| **NFR-52:** `% element dùng đúng token` | **F1** | **Phù hợp và rẻ nhất.** Được SlidesBench hậu thuẫn gián tiếp (design quality đo được bằng rule/color-distance) |
| **NFR-52:** checklist MLLM-judge cho design | F4 × P4 nhưng trên dimension **material-independent** | **Mismatch nhẹ.** Checklist per-instance đắt vì phải soạn theo từng input, trong khi design consistency **không phụ thuộc input** → rubric chung hoặc rule là đủ, checklist per-instance là overkill ở đây |
| **NFR-53:** checklist theo instance + human corr. mẫu con | **F6 hybrid**, F4×P4 + F5 validation | **Khớp nhất với landscape.** Đây là dimension material-dependent → per-instance là bắt buộc về mặt logic. Thiếu duy nhất: **cỡ mẫu human** — đã có câu trả lời ở §5.3 |

**Gap mà landscape phát hiện, không candidate nội bộ nào cover:**

1. **F2 (NLI/alignment/atomic-fact metric) hoàn toàn vắng mặt** trong cả §8 và NFR — dù đây là family rẻ nhất, ổn định nhất cho content fidelity và là family duy nhất chạy được ở tần suất daily mà vẫn chạm được vào fidelity.
2. **Không có yêu cầu đo noise floor của judge** (§4.5) — thiếu cái này thì không phân biệt được improvement với tiếng ồn.
3. **Không có yêu cầu version hóa/đóng băng judge prompt** (§4.4) — thiếu cái này thì điểm giữa các version không so được.
4. **Không nêu protocol** (pointwise/pairwise/checklist) — §8 nói "MLLM-as-judge" mà không nói lấy điểm ra kiểu gì, trong khi đây mới là biến quyết định độ tin cậy (§3).
5. **Không tách use case dev vs final** — cùng một method được ngầm giả định dùng cho cả hai.
6. **Style bias chưa được nhắc tới ở đâu** — theo Soumik 2026 đây có thể là bias trội.
