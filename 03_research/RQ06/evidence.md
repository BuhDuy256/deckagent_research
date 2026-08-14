# RQ06 — Evidence Index

**Ngày research:** 2026-08-14 (mọi entry dưới đây được truy cập trong ngày này, trừ khi ghi khác)
**Evidence level theo `04_evidence/EVIDENCE_POLICY.md`:** E1 (paper gốc / official repo / official docs) > E2 (survey, technical report uy tín, hoặc claim trích qua nguồn thứ cấp chưa verify bản gốc) > E3 (blog/tutorial/community) > UNVERIFIED.

**Quy ước bổ sung dùng trong file này:** arXiv preprint là bản gốc (không qua trung gian) nên xếp **E1**, nhưng nếu **chưa peer-review** thì ghi rõ `E1 (preprint, chưa peer-review)`. Với preprint 1 tác giả, chưa peer-review, mang claim mạnh → có ghi chú cảnh báo riêng. Đây là cách áp dụng policy chứ không phải sửa policy.

**Tag đọc:**
- **MUST READ** — 5 primary source cho cả RQ06 (đúng giới hạn 3–5).
- **SHOULD READ** — cần cho một sub-question cụ thể.
- **DISCOVERY ONLY** — dùng để định vị landscape / route sang RQ khác, không dùng làm nền cho recommendation.

⚠ **Ghi chú về độ tin cậy của việc trích xuất:** phần lớn số liệu chi tiết dưới đây được đọc qua fetch tự động trang HTML/abstract của arXiv, không phải đọc PDF bản in. Những con số nào **sẽ được trích vào báo cáo đồ án** phải được người đọc lại từ bản PDF gốc trước khi dùng. Chỗ nào trích xuất cho kết quả **không nhất quán** thì đã đánh dấu tại chỗ.

---

## 1. Kết quả verify "PresentBench" (UNVERIFIED ENTITY Q-005) → **VERIFIED**

### 1.1 Đã search bằng cách nào

| # | Query | Công cụ | Kết quả |
| --- | --- | --- | --- |
| 1 | `PresentBench benchmark presentation generation` | WebSearch | Trả về arXiv 2603.07244, GitHub org `PresentBench`, HF dataset, project page |
| 2 | `"PresentBench" arxiv` (quoted, exact) | WebSearch | Cùng cụm kết quả, thêm `presentbench.github.io` |
| 3 | `"PresentBench" -slide OR "presentation benchmark" different project disambiguation` | WebSearch | **Không tìm thấy entity nào khác cùng tên.** Chỉ trả về đúng benchmark trên + các benchmark *khác tên* (UniPPTBench) |
| 4 | Fetch `arxiv.org/abs/2603.07244` | WebFetch | Lấy được title/tác giả/ngày/abstract nguyên văn |
| 5 | Fetch `github.com/PresentBench/PresentBench` | WebFetch | Repo chính chủ, README, license Apache-2.0, script `judge.py`/`judge_all.py` |
| 6 | Fetch `presentbench.github.io` | WebFetch | Project page, leaderboard 9 hệ thống, 5 dimension |
| 7 | Fetch `arxiv.org/html/2603.07244v1` | WebFetch | Chi tiết judge protocol, human validation, ablation |

**Chống nhầm entity:** search #3 được đặt riêng để tìm xem có thứ khác cùng tên không. Kết quả: **không có**. Các tên gần giống nhưng **khác hẳn** đã được phân biệt và ghi ở `landscape.md` §0: `UniPPTBench`, `SlidesBench` (trong AutoPresent), `PPTEval` (trong PPTAgent), `SlidesGen-Bench` (arXiv 2601.09487 — chỉ thấy trong kết quả search, **chưa verify sâu, không dùng làm evidence**). `SlideBench` (số ít) là cách viết sai của `SlidesBench`, không phải entity riêng.

### 1.2 PresentBench — entry chính thức

```
Tên: PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation
Tác giả: Xin-Sheng Chen, Jiayu Zhu, Pei-lin Li, Hanzheng Wang, Shuojin Yang, Meng-Hao Guo
Năm: 2026 (arXiv v1, nộp 2026-03-07)
Link: https://arxiv.org/abs/2603.07244
      https://github.com/PresentBench/PresentBench (Apache-2.0)
      https://presentbench.github.io/
      Hugging Face dataset: PresentBench/PresentBench
Level: E1 (preprint, chưa xác nhận peer-review; có repo + dataset + project page chính chủ)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (chính) — route thêm sang RQ01, RQ02, RQ04, RQ07, RQ08
Tag: MUST READ
```

**Nguồn này THỰC SỰ support điều gì:**

1. **§8 của architecture doc mô tả PresentBench chính xác về mặt cơ chế.** Paper đúng là dùng checklist riêng cho từng instance, mỗi item là **binary question**, judge trả verdict **kèm localized evidence** (vd. slide index). → PROJECT SOURCE §8 không bịa entity.
2. **Quy mô cụ thể:** 238 evaluation instance, mỗi instance kèm background material; **trung bình 54.1 checklist item/instance**, **do expert soạn tay**; 5 category (Academia, Education, Economics, Talk, Advertising).
3. **5 dimension:** Presentation Fundamentals; Visual Design and Layout; Content Completeness; Content Correctness; Content Fidelity. Paper tự chia thành **material-independent** (2 cái đầu) và **material-dependent** (3 cái sau).
4. **Judge protocol:** slide → PDF → đưa cho judge multimodal (paper báo dùng model họ Gemini flash-preview); **mỗi checklist item một call riêng** để giảm tải nhận thức cho judge; điểm dimension = trung bình các item nhị phân; điểm cuối = trung bình **không trọng số** của 5 dimension.
5. **Human validation:** ~10% dataset lấy mẫu đều = **~24 instance**; annotator xếp hạng output của 5 hệ thống; metric = **Spearman ρ**.
6. **Con số alignment (quan trọng nhất):** PresentBench ρ ≈ **0.532**; **trần human–human ≈ 0.664**; PPTEval baseline ≈ **0.303**; MLLM-as-a-Judge Ranking ≈ **0.258**.
7. **Giới hạn paper tự thừa nhận:** *"the evaluation still relies on multimodal LLMs as verifiers, and their capability may limit the reliability of the resulting scores."*

**Nguồn này KHÔNG support điều gì (chống phóng đại):**
- **Không** phải một "judge protocol" đóng gói sẵn để gắn vào pipeline bất kỳ — nó là benchmark end-to-end cho hệ thống sinh slide.
- **Không** chứng minh checklist per-instance tốt hơn rubric chung *nói chung* — chỉ chứng minh trên setup của chính họ, với checklist expert-authored, so với PPTEval.
- **Không** báo cáo phân tích bias, variance, hay robustness của judge.
- **Không** công bố số lượng annotator người (project page không nêu; HTML paper chỉ nói "human annotators").
- ⚠ **Ablation có kết quả nghịch lý:** bỏ dimension Fidelity cho ρ **cao hơn** (0.673) so với dùng đủ 5 dimension (0.532); "material-dependent only" = 0.629. Paper giải thích đây là **giới hạn của protocol user study** (người xếp hạng trong ~3 phút, không verify tính chính xác thực tế), không phải bằng chứng Fidelity vô dụng. → **Không được trích con số 0.673 như "bằng chứng nên bỏ fidelity".**

**Route sang RQ khác** (ghi nhận, RQ06 không tự làm): RQ01 (5 dimension), RQ02 (tách Completeness/Correctness/Fidelity), RQ04 (Visual Design & Layout), RQ07 (238 instance, 5 category, background material), RQ08 (leaderboard 9 hệ thống, NotebookLM dẫn đầu).

---

## 2. MUST READ (4 nguồn còn lại)

### 2.1 Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena

```
Tác giả: Lianmin Zheng, Wei-Lin Chiang, Ying Sheng, Siyuan Zhuang, Zhanghao Wu,
         Yonghao Zhuang, Zi Lin, Zhuohan Li, Dacheng Li, Eric P. Xing, Hao Zhang,
         Joseph E. Gonzalez, Ion Stoica
Năm: 2023 (NeurIPS 2023 Datasets and Benchmarks Track)
Link: https://arxiv.org/abs/2306.05685
Level: E1 (peer-reviewed)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06
Tag: MUST READ
```

**Support:** (a) judge mạnh (GPT-4) đạt **>80% agreement** với human preference — *"the same level of agreement between humans"*; (b) đặt tên và đo 3 bias nền tảng: **position, verbosity, self-enhancement**, cộng **limited reasoning ability**; (c) position bias: đa số judge thiên vị vị trí đầu; (d) verbosity bias chứng minh bằng "repetitive list attack" đánh lừa nhiều judge, GPT-4 chống đỡ tốt hơn nhưng không miễn nhiễm.

**KHÔNG support:** con số >80% là trên **chat assistant response**, protocol pairwise, **không phải** trên deck/multimodal. Không được suy ra "judge sẽ đạt 80% agreement trên Deck Agent". Đây là nguồn cho **danh mục bias** và **cận trên lạc quan**, không phải cho kỳ vọng số cụ thể của domain này.

**Mức áp dụng cho Deck Agent:** cần thích nghi (khác modality, khác task).

### 2.2 MLLM-as-a-Judge: Assessing Multimodal LLM-as-a-Judge with Vision-Language Benchmark

```
Tác giả: Dongping Chen, Ruoxi Chen, Shilin Zhang, Yinuo Liu, Yaochen Wang, Huichi Zhou,
         Qihui Zhang, Yao Wan, Pan Zhou, Lichao Sun
Năm: 2024 (ICML 2024, Oral)
Link: https://arxiv.org/abs/2402.04788 · repo: https://github.com/Dongping-Chen/MLLM-Judge
Level: E1 (peer-reviewed)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06
Tag: MUST READ
```

**Support:** (a) so sánh trực tiếp 3 protocol cho judge **multimodal**: Scoring Evaluation, Pair Comparison, Batch Ranking; (b) kết luận định tính nhất quán: MLLM thể hiện *"remarkable human-like discernment in Pair Comparison"* nhưng *"significant divergence from human preferences in Scoring Evaluation and Batch Ranking"*; (c) failure mode của MLLM judge: *"diverse biases, hallucinatory responses, and inconsistencies in judgment"* — kể cả GPT-4V; (d) mỗi judgment được 3 annotator review độc lập, ghi nhận consensus.

**KHÔNG support / cảnh báo:** ⚠ **con số % agreement trích xuất KHÔNG nhất quán** giữa hai lần fetch (79.3% và 78% cho Pair Comparison; ~70% tổng thể). **Không dùng con số cụ thể nào cho tới khi đọc bản gốc.** Kết luận định tính (pairwise > scoring/ranking) thì nhất quán qua các lần trích xuất và là thứ RQ06 dựa vào.

**Mức áp dụng cho Deck Agent:** trực tiếp về mặt lựa chọn protocol (deck là artifact multimodal).

### 2.3 The Alternative Annotator Test for LLM-as-a-Judge

```
Tác giả: Nitay Calderon, Roi Reichart, Rotem Dror
Năm: 2025 (arXiv v1 2025-01-19; bản mới nhất 2025-08-08)
Link: https://arxiv.org/abs/2501.10970
Level: E1
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (sub-question "cỡ mẫu human"), liên kết NFR-53
Tag: MUST READ
```

**Support — đây là nguồn trả lời trực tiếp câu hỏi task brief "cần bao nhiêu mẫu human":**
- **≥ 3 annotator.** Lý do paper nêu rõ: với 2 người, thủ tục chỉ kiểm tra LLM nghiêng về người nào, **không có tín hiệu consensus**; từ 3 trở lên mới đánh giá được LLM có đại diện cho *nhóm* không.
- **50–100 instance** với ε = 0.2; **100–150** với ε = 0.1; **tối thiểu 30** để thỏa giả định normality của t-test.
- Tiêu chí pass: **winning rate ω ≥ 0.5**. Mức ý nghĩa: **FDR q = 0.05**.
- ε (điều chỉnh cost-benefit ưu ái LLM): **0.2** expert annotator, **0.15** skilled, **0.1** crowd-worker. Paper nói rõ: **IAA thấp → phải dùng ε nhỏ hơn**.

**KHÔNG support:** đây là thủ tục để **justify thay annotator người bằng LLM** trên một task annotation, không phải công thức tính cỡ mẫu cho "đo tương quan judge–người" nói chung. Áp vào Deck Agent là **thích nghi**, không phải áp thẳng. Con số 50–100 là điều kiện để *test có lực*, không đảm bảo LLM sẽ pass.

**Mức áp dụng cho Deck Agent:** cần thích nghi — nhưng là con số **có nguồn** duy nhất tìm được cho câu hỏi này.

### 2.4 PPTAgent: Generating and Evaluating Presentations Beyond Text-to-Slides (chứa PPTEval)

```
Tác giả: nhóm ICIP-CAS (repo: https://github.com/icip-cas/PPTAgent)
Năm: 2025 (arXiv 2501.03936, v2 2025-02-21)
Link: https://arxiv.org/abs/2501.03936
Level: E1
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (chính) — route sang RQ01, RQ04, RQ08
Tag: MUST READ
```

**Support:** (a) **PPTEval** = đối trọng "generic rubric" trong cùng domain: 3 dimension (Content / Design / Coherence), thang **1–5**, judge **GPT-4o**, **slide render thành ảnh** vì presentation *"cannot be easily comprehended in a plain text format"*; (b) **con số agreement người–người trong đúng domain này**: 4 graduate student chấm 250 presentation, **Fleiss' κ ≈ 0.59** trung bình (Content 0.61, Design 0.61, Coherence 0.54), paper gọi là "satisfactory"; (c) là baseline mà PresentBench so sánh và vượt (ρ 0.303 vs 0.532).

**Vì sao entry này quan trọng hơn vẻ ngoài:** con số **κ ≈ 0.59** là hiệu chỉnh kỳ vọng mạnh nhất trong cả RQ06 — người chấm presentation **cũng không** đạt ngưỡng reliability chuẩn của content analysis. Nó định nghĩa lại "đủ tin" là gì cho Deck Agent.

**KHÔNG support:** không phải bằng chứng rằng rubric chung "sai" — chỉ là nó yếu hơn checklist per-instance **trong so sánh của PresentBench**, một so sánh do chính bên đề xuất checklist thực hiện.

---

## 3. SHOULD READ

### 3.1 LLM Evaluators Recognize and Favor Their Own Generations

```
Tác giả: Arjun Panickssery, Samuel R. Bowman, Shi Feng (theo trang NeurIPS/OpenReview)
Năm: 2024 (NeurIPS 2024)
Link: https://arxiv.org/abs/2404.13076
Level: E1 (peer-reviewed)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (sub-question self-preference bias)
Tag: SHOULD READ
```
⚠ Danh sách tác giả lấy từ trang tổng hợp, **chưa đối chiếu bản PDF gốc** — verify trước khi trích.

**Support:** (a) GPT-4 và Llama 2 có độ chính xác **trên ngẫu nhiên** khi phân biệt output của chính mình với của model khác/của người; (b) khi fine-tune, tồn tại **tương quan tuyến tính giữa năng lực tự-nhận-diện và độ mạnh self-preference**; (c) thí nghiệm có kiểm soát cho thấy giải thích nhân quả này chịu được các confounder đơn giản.

**Vì sao đây là nguồn quyết định cho §8:** §8 khuyên *"dùng model khác họ để generate và để judge"* mà không nêu cơ sở. Paper này là **E1 hậu thuẫn đúng cơ chế đó** — nếu bias sinh ra từ tự-nhận-diện, thì tách generator/judge sang họ khác tấn công trực diện vào nguyên nhân.

**KHÔNG support:** không chứng minh tách họ **loại bỏ** bias, chỉ chứng minh cơ chế. Không nói gì về style/verbosity/position bias.

### 3.2 Quantifying and Mitigating Self-Preference Bias of LLM Judges

```
Tác giả: Jinming Yang, Zheng Hu, Chuxian Qiu, Zhenyu Deng, Xinshan Jiao, Tao Zhou
Năm: 2026 (arXiv v1 2026-04-24, revised 2026-06-02)
Link: https://arxiv.org/abs/2604.22891
Level: E1 (preprint, chưa peer-review)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (self-preference mitigation)
Tag: SHOULD READ
```

**Support:** (a) framework tự động lượng hóa SPB bằng cách dựng **cặp response chất lượng ngang nhau**, tách bạch discriminability khỏi bias propensity **không cần human gold standard**; (b) khảo sát **20 LLM chủ lưu**: *"advanced capabilities are often uncorrelated, or even negatively correlated, with low SPB"* → **chọn model mạnh nhất KHÔNG phải cách giảm SPB**; (c) mitigation đề xuất — **structured multi-dimensional evaluation** dựa trên cognitive load decomposition — giảm SPB trung bình **31.5%**.

**Vì sao quan trọng cho RQ06:** đây là evidence rằng **cách hỏi** (phân rã thành nhiều chiều/item) là một mitigation lever độc lập với **model nào hỏi** — tức là bổ sung, không thay thế, khuyến nghị "khác họ" của §8. Và nó trùng hướng với cơ chế checklist của PresentBench.

**KHÔNG support:** 31.5% là mức giảm trung bình trên setup của họ, không phải con số kỳ vọng cho Deck Agent. Preprint, chưa peer-review.

### 3.3 Large Language Models are not Fair Evaluators

```
Tác giả: Peiyi Wang, Lei Li, Liang Chen, và cộng sự
Năm: 2023 (arXiv 2305.17926) / ACL 2024 (aclanthology.org/2024.acl-long.511)
Link: https://arxiv.org/abs/2305.17926 · code: https://github.com/i-Eval/FairEval
Level: E1 (peer-reviewed — ACL 2024)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (position bias mitigation)
Tag: SHOULD READ
```

**Support:** (a) xếp hạng có thể bị "hack" **chỉ bằng đổi thứ tự xuất hiện**; GPT-4 thiên vị ứng viên hiển thị đầu tiên, ChatGPT thiên vị ứng viên thứ hai; (b) **2 mitigation có tên và có code**: **MEC (Multiple Evidence Calibration** — bắt model sinh evidence trước rồi mới chấm**)** và **BPC (Balanced Position Calibration** — chạy cả hai thứ tự rồi cân bằng**)**.

**KHÔNG support:** chỉ áp dụng cho protocol **pairwise/so sánh**. Không nói gì về pointwise binary checklist (protocol mà bias này về cơ bản không phát sinh).

### 3.4 TICKing All the Boxes: Generated Checklists Improve LLM Evaluation and Generation

```
Năm: 2024 (arXiv 2410.03608, nộp 2024-10-04)
Link: https://arxiv.org/abs/2410.03608
Level: E1 (preprint; có trang OpenReview)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (checklist per-instance vs rubric chung)
Tag: SHOULD READ
```
⚠ Danh sách tác giả **chưa trích xuất được** trong session này — verify trước khi trích vào báo cáo.

**Support:** (a) **TICK** = checklist **do LLM tự sinh** theo từng instruction, phân rã thành chuỗi câu hỏi **YES/NO**, mỗi câu hỏi kiểm tra một yêu cầu cụ thể của instruction; (b) dùng TICK làm tăng tần suất **exact agreement** giữa judgement của LLM và human preference từ **46.4% → 52.2%** so với để LLM chấm điểm trực tiếp; (c) biến thể **STICK** (self-TICK) dùng cho self-refinement và Best-of-N.

**Vì sao quan trọng:** đây là **đường thứ ba** giữa "checklist expert-authored đắt đỏ" (PresentBench, HealthBench) và "rubric chung rẻ nhưng yếu" — checklist per-instance **sinh tự động**. Cực kỳ liên quan tới một đồ án 4 người không thể thuê expert soạn 54 item × N instance.

**KHÔNG support:** mức cải thiện (+5.8 điểm phần trăm) **nhỏ hơn nhiều** so với khoảng cách PresentBench (0.532) vs PPTEval (0.303). Không được suy ra checklist LLM-sinh mạnh ngang checklist expert-authored — evidence hiện có gợi ý ngược lại.

### 3.5 HealthBench (OpenAI)

```
Tên: HealthBench: Evaluating Large Language Models Towards Improved Human Health
Năm: 2025
Link: https://openai.com/index/healthbench/
      https://cdn.openai.com/pdf/bd7a39d5-9e9f-47b3-903c-8b847ca650c7/healthbench_paper.pdf
Level: E1 (official technical report từ chính nhà phát triển)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (checklist/rubric per-instance ở quy mô sản xuất)
Tag: SHOULD READ
```

**Support:** ví dụ lớn nhất, có thật, về **rubric riêng cho từng instance**: 5.000 hội thoại, **mỗi hội thoại một rubric riêng do bác sĩ viết**, tổng **48.562 rubric criteria**, xây bởi **262 bác sĩ** ở 60 quốc gia; mỗi criterion có **trọng số điểm** theo mức quan trọng do bác sĩ đánh giá; **mọi criteria được ≥3 bác sĩ viết và adjudicate qua 3 phase**; grader là **model** (GPT-4.1) chấm từng criterion.

**Vì sao quan trọng:** chứng minh (a) per-instance rubric là pattern **được dùng thật ở quy mô lớn** khi độ tin cậy quan trọng, không phải ý tưởng học thuật; (b) **mô hình 2 lớp**: người viết tiêu chí — **model** chấm tiêu chí. Đây là kiến trúc khả thi hơn nhiều cho một team nhỏ so với "người chấm mọi thứ"; (c) criteria **có trọng số**, khác với PresentBench (trung bình không trọng số).

**KHÔNG support:** quy mô này ngoài tầm với hoàn toàn của một đồ án. Dùng làm **bằng chứng về pattern**, không phải mục tiêu quy mô.

### 3.6 Necessary but Not Sufficient: Temperature Control and Reproducibility in LLM-as-Judge Safety Evaluations

```
Tác giả: Hiroki Tamba
Năm: 2026 (arXiv 2606.26185, nộp 2026-06-24)
Link: https://arxiv.org/abs/2606.26185
Level: E1 (preprint, chưa peer-review, MỘT tác giả)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (judge variance/repeatability)
Tag: SHOULD READ
```

**Support:** (a) harness không set temperature → provider mặc định 1.0 → item gần ranh giới quyết định **lật pass/fail giữa các lần chạy giống hệt nhau, disagreement lên tới ~50% qua 20 lần chạy**; (b) pin temperature=0 **giảm nhưng không loại bỏ**: qua **690 API call** trên 2 provider, 3 tier model, 5 cấu hình sampling, vẫn còn **1–2 trên 7 item borderline** không reproducible ngay cả với greedy decoding (top_k=1); (c) khuyến nghị coi **grader disagreement là health metric hạng nhất**, báo cáo song song với điểm; (d) cảnh báo: một số model thế hệ mới đã **bỏ hẳn tham số temperature**, làm mitigation chính không áp dụng được.

⚠ **Cảnh báo độ tin cậy:** preprint 1 tác giả, chưa peer-review, chạy trên **một** codebase safety-eval (aisev của Japan AISI), mẫu nhỏ (7 borderline item). Claim định tính ("temperature=0 không đảm bảo determinism") **phù hợp với hiểu biết kỹ thuật đã biết** về non-determinism phía serving, nên đáng tin ở mức định hướng. **Con số cụ thể thì không nên trích như quy luật chung.**

**Hệ quả cho RQ06:** đủ để justify **một hành động bắt buộc** — đo noise floor của judge bằng cách chạy lặp — mà không cần tin vào con số cụ thể của paper.

### 3.7 JudgeSense: A Benchmark for Prompt Sensitivity in LLM-as-a-Judge Systems

```
Tác giả: Rohith Reddy Bellibatlu, Edward Raff, Wenbin Zhang
Năm: 2026 (arXiv 2604.23478, nộp 2026-04-26, revised 2026-05-07)
Link: https://arxiv.org/abs/2604.23478
Level: E1 (preprint, chưa peer-review)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (prompt/rubric sensitivity)
Tag: SHOULD READ
```

**Support:** (a) benchmark gồm **cặp prompt-paraphrase đã hand-validate** trên factuality, coherence, relevance, preference, kèm decision log đầy đủ; (b) verdict của judge **thay đổi dưới các cách diễn đạt tương đương ngữ nghĩa**; (c) **model lớn/mới hơn KHÔNG ổn định hơn** — *"model scale is not a reliable proxy for consistency; the largest and newest models are not the most consistent"*; (d) **pairwise task luôn thể hiện position bias**; (e) coherence là dimension bất ổn nhất, factuality ổn định nhất.

**Hệ quả cho Deck Agent:** justify việc **đóng băng + version hóa judge prompt như một phần của benchmark**. Cũng cảnh báo: dimension kiểu "coherence/structure" (chính là Structure trong §8) nằm trong nhóm bất ổn nhất.

**KHÔNG support:** không đưa ra công thức chọn prompt tốt nhất; chỉ đo độ bất ổn.

### 3.8 Judging the Judges: A Systematic Evaluation of Bias Mitigation Strategies in LLM-as-a-Judge Pipelines

```
Tác giả: Sadman Kabir Soumik
Năm: 2026 (arXiv 2604.23178, nộp 2026-04-25, revised 2026-06-24)
Link: https://arxiv.org/abs/2604.23178
Level: E1 (preprint, chưa peer-review, MỘT tác giả)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (bias landscape, cost)
Tag: SHOULD READ
```

**Support:** (a) so sánh **9 chiến lược debiasing** trên 5 judge model từ 4 họ (Google, Anthropic, OpenAI, Meta), 3 benchmark (MT-Bench n=400, LLMBar n=200, custom n=375), 4 loại bias; (b) **style bias là bias trội** (0.10–0.76 tùy model, **thiên vị markdown hơn văn xuôi thuần**), **vượt xa position bias (≤0.04)**, và *"rarely studied"*; (c) **verbosity bias không đồng nhất**: một số model thích dài (+0.24 đến +0.44), Claude thích ngắn (−0.12), GPT-4o trung tính (−0.04); trên truncation control thì mọi model đều đúng (0.88–1.00); (d) **cost**: một model tier trung + debiasing đạt agreement cao nhất trong toàn bộ cấu hình test (71.0%, κ=0.549) ở ~$0.001/evaluation, **rẻ hơn ~15×** so với cấu hình frontier tốt nhất (69.5%, ~$0.015).

⚠ **Cảnh báo:** preprint 1 tác giả, chưa peer-review, claim mạnh và mới ("style bias là bias trội"). **Không dùng một mình để chống đỡ một major recommendation.** Dùng như: (i) cảnh báo về một bias chưa được nghiên cứu kỹ; (ii) evidence rằng **judge đắt tiền không tự động tốt hơn** — trùng hướng với Yang et al. 2026 (§3.2) và JudgeSense (§3.7), tức là **có 3 nguồn độc lập cùng chỉ về một hướng**.

### 3.9 Replacing Judges with Juries: Evaluating LLM Generations with a Panel of Diverse Models (PoLL)

```
Tác giả: Pat Verga, Sebastian Hofstätter, Sophia Althammer, Yixuan Su, Aleksandra Piktus,
         Arkady Arkhangorodsky, Minjie Xu, Naomi White, Patrick Lewis (Cohere)
Năm: 2024 (arXiv 2404.18796)
Link: https://arxiv.org/abs/2404.18796
Level: E1 (preprint từ lab công nghiệp, nhiều tác giả)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (single judge vs panel)
Tag: SHOULD READ
```

**Support:** panel gồm nhiều model **nhỏ** vượt một judge lớn đơn lẻ, thể hiện **ít intra-model bias hơn** nhờ cấu thành từ **họ model rời nhau (disjoint families)**, và **rẻ hơn >7×**; test trên 3 judge setting × 6 dataset.

**KHÔNG support / bị phản bác:** xem §3.10 — kết quả này **mâu thuẫn trực tiếp** với một nguồn 2026. Không được dùng làm nền cho recommendation khi chưa giải quyết mâu thuẫn.

### 3.10 Nine Judges, Two Effective Votes: Correlated Errors Undermine LLM Evaluation Panels

```
Tác giả: Guneet Kohli
Năm: 2026 (arXiv 2605.29800, nộp 2026-05-28)
Link: https://arxiv.org/abs/2605.29800
Level: E1 (preprint, chưa peer-review, MỘT tác giả)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (single judge vs panel)
Tag: SHOULD READ
```

**Support:** panel 9 LLM frontier từ 7 họ, trên 3 dataset NLI (mỗi item 100 human annotation): (a) 9 judge chỉ cung cấp lượng thông tin bằng **~2 vote độc lập**; (b) ~**75%** độ độc lập danh nghĩa mất đi vì **các model sai cùng chỗ trên cùng item**; (c) accuracy thực của panel thấp hơn ideal **8–22 điểm phần trăm**; (d) **judge đơn tốt nhất bằng hoặc vượt cả panel** trong mọi điều kiện; (e) thêm judge hoặc aggregation thông minh hơn chỉ bù được **tối đa 11%** khoảng cách.

⚠ **Cảnh báo:** preprint 1 tác giả, chưa peer-review; test trên **NLI** (task có ground truth khách quan) chứ không phải open-ended generation — có thể không chuyển được sang đánh giá deck. **Mâu thuẫn với PoLL (§3.9) chưa được giải quyết** → ghi vào unresolved question.

### 3.11 Aligning with Human Judgement: The Role of Pairwise Preference in LLM Evaluators (PairS)

```
Tác giả: Liu et al. (Cambridge LTL)
Năm: 2024 (COLM 2024; arXiv 2403.16950, v5 2025-01-17)
Link: https://arxiv.org/abs/2403.16950 · code: https://github.com/cambridgeltl/PairS
Level: E1 (peer-reviewed — COLM 2024)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (pointwise vs pairwise)
Tag: SHOULD READ
```

**Support:** formulate evaluation thành bài toán **ranking**, dùng LLM so sánh **pairwise cục bộ** rồi xếp hạng toàn cục qua **uncertainty-guided search** (PairS); *"more effective and query-efficient than existing pairwise baselines while substantially outperforming traditional direct scoring evaluations"*; kiểm chứng trên summarization và open-ended generation.

**KHÔNG support:** pairwise **không cho điểm tuyệt đối** → không giải quyết được nhu cầu "so v0.2 với v0.9 mà không chạy lại v0.2" của mission. Là evidence cho **độ tin cậy**, không phải cho **khả năng tracking theo thời gian**.

### 3.12 Beyond correlation: The Impact of Human Uncertainty in Measuring the Effectiveness of Automatic Evaluation and LLM-as-a-Judge

```
Tác giả: Aparna Elangovan, Lei Xu, Jongwoo Ko, Mahsa Elyasi, Ling Liu, Sravan Bodapati, Dan Roth
Năm: 2024/2025 (arXiv 2410.03775, nộp 2024-10-03, revised 2025-01-27)
Link: https://arxiv.org/abs/2410.03775
Level: E1 (preprint)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (phương pháp validate judge)
Tag: SHOULD READ
```

**Support:** phê phán phương pháp luận trực tiếp vào chính cách NFR-53 đề xuất validate: khi tỷ lệ mẫu có **bất định/biến thiên trong nhãn người** cao, nhãn máy có thể **trông như** tương quan tương đương hoặc tốt hơn tương quan người–người dù thực chất không phải; các hệ số như Krippendorff's α / Randolph's κ vốn thiết kế để đo reliability của **người**, mang giả định về hành vi người mà hệ thống tự động không thỏa; hiệu năng của evaluator tự động **suy giảm rõ rệt khi độ nhất quán nhãn người tăng lên**.

**Hệ quả cho Deck Agent:** **không được** kết luận "judge đủ tin cậy" chỉ bằng một con số correlation. Phải báo cáo kèm phân bố bất đồng của người. Kết hợp với PPTEval κ≈0.59 (human uncertainty cao trong domain này) → cảnh báo này **áp dụng mạnh** cho Deck Agent.

### 3.13 AutoPresent / SlidesBench

```
Tên: AutoPresent: Designing Structured Visuals from Scratch
Tác giả: Jiaxin Ge, Zora Zhiruo Wang, Xuhui Zhou, Yi-Hao Peng, Sanjay Subramanian, Qinyue Tan,
         Maarten Sap, Alane Suhr, Daniel Fried, Graham Neubig, Trevor Darrell
Năm: 2025 (arXiv 2501.00912, nộp 2025-01-01, revised 2025-06-19)
Link: https://arxiv.org/abs/2501.00912
Level: E1
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (deterministic evaluator cho slide) — route sang RQ04, RQ07
Tag: SHOULD READ
```

**Support — nguồn tốt nhất tìm được cho "phần nào của deck đo được bằng rule":**
- **SlidesBench**: 7k training + 585 testing example từ 310 deck, 10 domain.
- **Reference-based metric (thuần code)**: Element Matching (tổng kích thước element khớp / tổng kích thước mọi element); Content Similarity (cosine similarity qua sentence-transformer cho text, **CLIP score** cho ảnh); Color Similarity (công thức **CIEDE2000** cho font/background/slide background); Position Similarity (Manhattan distance chuẩn hóa về [0,1]).
- **Reference-free design metric**, thang 0–5 (scale lên 0–100): **Text** (title rõ, nội dung súc tích, font size/style/color nhất quán và đọc được); **Image** (chất lượng cao, tỷ lệ hợp lý); **Layout** (element căn thẳng, **không chồng lấn**, đủ margin, **nằm trong khung slide**); **Color** (tương phản cao, đặc biệt text/background).
- Reference-free metric được chấm bằng **GPT-4o** (0–5), và validate bằng human study với **ICC 73.8%–85.3%**, paper mô tả là "well within the range of what is typically considered high agreement".

**Vì sao quan trọng cho RQ06:** (a) chứng minh phần lớn "design quality" của slide có định nghĩa hình thức đo được bằng code — hậu thuẫn cho candidate deterministic của NFR-52; (b) **ICC 73.8–85.3%** là con số agreement **cao hơn hẳn** PPTEval κ≈0.59 → gợi ý (INFERENCE) rằng dimension **material-independent** dễ đạt đồng thuận hơn dimension đòi phán đoán nội dung.

**KHÔNG support:** SlidesBench giả định có **reference slide** cho nhóm metric thứ nhất — Deck Agent thường không có. Chỉ nhóm reference-free chuyển sang được trực tiếp.

### 3.14 FActScore

```
Tên: FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation
Tác giả: Sewon Min, Kalpesh Krishna, Xinxi Lyu, Mike Lewis, Wen-tau Yih, Pang Wei Koh,
         Mohit Iyyer, Luke Zettlemoyer, Hannaneh Hajishirzi
Năm: 2023 (EMNLP 2023)
Link: https://arxiv.org/abs/2305.14251 · code: https://github.com/shmsw25/FActScore
Level: E1 (peer-reviewed)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (family F2c) — route sang RQ02
Tag: SHOULD READ
```

**Support:** tách generation thành **atomic fact** (mỗi mệnh đề chứa đúng một thông tin), gán **nhãn nhị phân** cho từng atomic fact theo một knowledge source, điểm = **% atomic fact được support**; estimator tự động (retrieval + LM mạnh) báo **sai số < 2%** so với FActScore do người chấm.

**Vì sao quan trọng:** cùng **nguyên lý** với checklist per-instance (phân rã thành mệnh đề nhị phân verify được), nhưng checklist **sinh tự động từ output** thay vì **soạn tay từ input** → chi phí authoring ≈ 0. Là ứng viên trực tiếp cho content fidelity ở tần suất daily. Đây là family §8 và NFR **bỏ sót hoàn toàn**.

**KHÔNG support:** validate trên biography generation với knowledge source có cấu trúc (Wikipedia), không phải trên PDF tùy ý → mức áp dụng cho Deck Agent là **cần thích nghi**, chưa có evidence trực tiếp trên domain này.

### 3.15 AlignScore và SummaC (NLI/alignment-based factual consistency)

```
AlignScore: Evaluating Factual Consistency with a Unified Alignment Function
  Năm: 2023 (ACL 2023) · https://arxiv.org/abs/2305.16739 · https://github.com/yuh-zha/AlignScore
SummaC: Re-Visiting NLI-based Models for Inconsistency Detection in Summarization
  Tác giả: Laban et al. · Năm: 2022 (TACL) · https://aclanthology.org/2022.tacl-1.10/
Level: E1 (cả hai peer-reviewed)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (family F2b) — route sang RQ02
Tag: SHOULD READ
```

**Support:** **SummaC** = benchmark gồm 6 dataset inconsistency detection; `SummaC-Conv` chia document thành sentence unit và aggregate điểm NLI theo cặp câu; operationalize consistency là "mỗi câu của summary được **entail** bởi source và **không bị mâu thuẫn**". **AlignScore** = alignment function thống nhất train trên ~**4.7M example** từ 7 task (NLI, QA, paraphrase, fact verification, IR, STS, summarization), đạt SOTA trên benchmark SummaC và TRUE.

⚠ Chi tiết AlignScore lấy qua trang tổng hợp + repo chính chủ, **chưa đọc PDF gốc**. Con số 4.7M cần verify trước khi trích.

**KHÔNG support:** chỉ áp dụng cho quan hệ **text ↔ text**. Mù hoàn toàn với layout/visual. Không đo coverage (bỏ sót ý quan trọng) — chỉ đo consistency (không nói sai).

### 3.16 G-Eval

```
Tên: G-Eval: NLG Evaluation using GPT-4 with Better Human Alignment
Tác giả: Yang Liu et al.
Năm: 2023 (EMNLP 2023)
Link: https://arxiv.org/abs/2303.16634 · https://aclanthology.org/2023.emnlp-main.153/
Level: E1 (peer-reviewed)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (pointwise LLM judge, generic rubric)
Tag: SHOULD READ
```

**Support:** framework LLM judge dạng **form-filling + chain-of-thought**; đạt **Spearman 0.514** với human trên task summarization, vượt các method trước đó "by a large margin"; **CoT cải thiện tương quan trên mọi dimension**, rõ nhất ở fluency; probability normalization cho điểm liên tục mịn hơn và tương quan cao hơn.

**Vì sao quan trọng cho hiệu chỉnh kỳ vọng:** 0.514 là con số của một method **được coi là mạnh** ở thời điểm công bố. Đối chiếu: PresentBench 0.532 với trần người 0.664. → **Spearman ~0.5 là vùng hoạt động thực tế của LLM judge**, không phải 0.9. Team không nên đặt kỳ vọng sai.

### 3.17 Krippendorff (2004) — ngưỡng reliability

```
Tên: Reliability in Content Analysis: Some Common Misconceptions and Recommendations
Tác giả: Klaus Krippendorff
Năm: 2004 · Human Communication Research 30(3): 411–433
Link: https://academic.oup.com/hcr/article/30/3/411/4331534
      DOI: 10.1111/j.1468-2958.2004.tb00738.x
Level: PAPER = E1 (đã verify tồn tại, có DOI, có trang publisher)
       NGƯỠNG SỐ = E2 (trích qua nguồn thứ cấp — CHƯA đọc bản gốc)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (ngưỡng agreement)
Tag: SHOULD READ
```

**Trạng thái verify:** paper **tồn tại**, đã xác nhận qua trang publisher (Oxford Academic / Wiley) và ERIC. **Bản PDF không truy cập được** trong session này (link faculty.washington.edu trả 404).

**Support (ở mức E2, theo đúng quy tắc 3 của EVIDENCE_POLICY):** ngưỡng được trích rộng rãi: **α ≥ 0.800** = tin được; **0.667 ≤ α < 0.800** = chỉ rút kết luận **tạm thời**; **α < 0.667** = loại bỏ. ⚠ **Phải verify lại từ bản gốc trước khi đưa vào báo cáo đồ án.**

**Cảnh báo áp dụng (INFERENCE):** ngưỡng này đến từ content analysis, nơi giả định có "mã đúng". PPTEval đo được κ≈0.59 giữa 4 human rater trên chính domain presentation — **dưới cả ngưỡng tentative**. Nên áp thẳng ngưỡng 0.8 vào Deck Agent là đặt mục tiêu không khả thi. Xem `landscape.md` §5.2.

### 3.18 Bonett & Wright (2000) — cỡ mẫu cho ước lượng correlation

```
Tên: Sample size requirements for estimating Pearson, Kendall and Spearman correlations
Tác giả: Douglas G. Bonett, Thomas A. Wright
Năm: 2000 · Psychometrika 65(1): 23–28 · DOI: 10.1007/BF02294183
Link: https://link.springer.com/article/10.1007/BF02294183
Level: E1 (peer-reviewed — đã verify tồn tại qua Springer, Cambridge Core, ERIC, IDEAS/RePEc)
Ngày research: 2026-08-14
Áp dụng cho RQ: RQ06 (cỡ mẫu human)
Tag: SHOULD READ
```

**Support:** review các interval estimate cho Pearson / Kendall tau-a / Spearman, đề xuất standard error cải tiến cho Spearman, và khảo sát **cỡ mẫu cần để khoảng tin cậy đạt độ rộng mong muốn**; xấp xỉ 2 giai đoạn cho kết quả chính xác.

**KHÔNG support:** **không trích xuất được bảng n cụ thể** trong session này. → Dùng nguồn này để trả lời "cần bao nhiêu mẫu" là **chưa đủ**; RQ06 dựa vào alt-test (§2.3) cho con số thực hành, và giữ Bonett & Wright như **phương pháp đúng** để tính CI width nếu team muốn con số riêng cho ρ mục tiêu của mình.

---

## 4. DISCOVERY ONLY

### 4.1 UniPPTBench: A Unified Benchmark for Presentation Generation Across Diverse Input Settings
```
Tác giả: Bo Zhao, Maosheng Pang, Chen Zhang, Huan Yang, Yixin Cao, Wei Ji
Năm: 2026 (arXiv 2605.17356, nộp 2026-05-17)
Link: https://arxiv.org/abs/2605.17356
Level: E1 (preprint, chưa peer-review) · Ngày research: 2026-08-14 · Tag: DISCOVERY ONLY
```
4 input setting (vague-prompt, long-document, multimodal-document, multi-source); **UniPPTEval** = protocol scenario-aware kết hợp shared metric + scenario-specific metric. Kết luận đáng chú ý cho RQ06: *"strong performance on generic presentation-quality metrics does not necessarily imply strong task fulfillment in grounded scenarios"* → rubric chung có thể **không phát hiện** failure về grounding. **Chưa trích xuất được tên metric cụ thể hay chi tiết human validation.** → route **RQ07** (input taxonomy), **RQ01** (scenario-specific dimension).

### 4.2 SlidesGen-Bench: Evaluating Slides Generation via Computational and Quantitative Metrics
```
Link: https://arxiv.org/abs/2601.09487 · Năm: 2026
Level: UNVERIFIED (chỉ thấy trong kết quả search, CHƯA fetch, CHƯA đọc)
Ngày research: 2026-08-14 · Tag: DISCOVERY ONLY
```
**Không dùng làm evidence.** Ghi lại để RQ07 kiểm tra.

### 4.3 From Generation to Judgment: Opportunities and Challenges of LLM-as-a-judge (survey)
```
Tác giả: Dawei Li, Bohan Jiang, Liangjie Huang, Alimohammad Beigi, Chengshuai Zhao, Zhen Tan,
         Amrita Bhattacharjee, Yuxuan Jiang, Canyu Chen, Tianhao Wu, Kai Shu, Lu Cheng, Huan Liu
Năm: 2024 (arXiv 2411.16594, v7 2025-09-29)
Link: https://arxiv.org/abs/2411.16594
Level: E2 (survey) · Ngày research: 2026-08-14 · Tag: DISCOVERY ONLY
```
Taxonomy 3 chiều: **what to judge / how to judge / how to benchmark**. **Chưa trích xuất được danh mục bias chi tiết bên trong.** Dùng làm bản đồ để tìm E1 gốc, không dùng làm evidence trực tiếp.

### 4.4 SummEval: Re-evaluating Summarization Evaluation
```
Tác giả: Alexander R. Fabbri, Wojciech Kryściński, Bryan McCann, Caiming Xiong, Richard Socher,
         Dragomir Radev
Năm: 2021 · TACL 9: 391–409 · https://aclanthology.org/2021.tacl-1.24/
Level: E1 (peer-reviewed) · Ngày research: 2026-08-14 · Tag: DISCOVERY ONLY
```
Re-evaluate **14 automatic metric** trên output model neural + human annotation (expert và crowd), benchmark 23 model summarization, kèm toolkit. ⚠ **Không trích xuất được con số correlation cụ thể của ROUGE.** Điều verify được là **mục tiêu tường minh** của paper: *"advance research in developing evaluation metrics that better correlate with human judgments"* — tức thừa nhận metric sẵn có chưa đủ. **Không được trích như "ROUGE tương quan r=0.x với người".**

### 4.5 Awesome-LLMs-as-Judges (repo tổng hợp survey)
```
Link: https://github.com/CSHaitao/Awesome-LLMs-as-Judges
Level: E3 (repo tổng hợp cộng đồng) · Ngày research: 2026-08-14 · Tag: DISCOVERY ONLY
```
Chỉ dùng để **tìm** E1. Không được dùng làm evidence.

### 4.6 Nguồn E3 về cost/latency của LLM judge — ĐÃ LOẠI
Search `LLM-as-a-judge cost latency compared to human annotation` chỉ trả về **blog/vendor content** (Medium, Confident AI, Galileo, Maxim, Label Your Data) với các con số **mâu thuẫn nhau** (95%+ rẻ hơn / 500–5000× / 20× / ~10×) và không truy được về primary source. **Level E3, KHÔNG dùng để support recommendation nào** (theo EVIDENCE_POLICY: E3 không được một mình chống đỡ major recommendation).

Con số cost duy nhất dùng được trong RQ06 đến từ **§3.8 (Soumik 2026, E1 preprint)**: ~$0.001 vs ~$0.015 mỗi evaluation, chênh ~15×. Ngoài ra, chi phí judge cho Deck Agent được ước lượng bằng **INFERENCE từ protocol** (PresentBench: ~54 call/deck) đối chiếu với **NFR-90** (PROJECT SOURCE: ~$0.05–0.25/deck cho generation mid-tier), không phải từ nguồn E3.

---

## 5. Tổng kết độ mạnh/yếu của evidence base

| Sub-question | Độ mạnh evidence | Vì sao |
| --- | --- | --- |
| PresentBench là gì | **Mạnh** | E1 có paper + repo + dataset + project page chính chủ, mô tả §8 khớp cơ chế |
| Checklist per-instance vs rubric chung | **Mạnh** | 4 nguồn E1 độc lập cùng hướng (PresentBench, TICK, HealthBench, FActScore) + 1 phản chứng gián tiếp (UniPPTBench) |
| Danh mục bias của LLM/MLLM judge | **Mạnh** | E1 peer-reviewed cho position/verbosity/self-preference (Zheng, Wang, Panickssery, Chen) |
| Pointwise vs pairwise | **Trung bình–mạnh** | E1 peer-reviewed (Chen ICML 2024, Liu COLM 2024) nhưng ⚠ con số cụ thể của Chen trích xuất không nhất quán |
| Cỡ mẫu human | **Trung bình** | Có đúng **một** nguồn E1 cho con số thực hành (alt-test); là thủ tục cho annotation task, áp vào Deck Agent phải thích nghi |
| Ngưỡng agreement "đủ tin" | **Yếu–trung bình** | Ngưỡng Krippendorff chỉ ở E2 (chưa đọc bản gốc); và ngưỡng đó **mâu thuẫn với thực tế domain** (PPTEval κ≈0.59) |
| Judge variance / reproducibility | **Trung bình** | Chỉ 1 preprint 1 tác giả, mẫu nhỏ — nhưng claim định tính phù hợp hiểu biết kỹ thuật đã biết |
| Style bias | **Yếu** | Chỉ 1 preprint 1 tác giả (Soumik 2026). **Không đủ để chống đỡ recommendation riêng** — chỉ ghi nhận là rủi ro mở |
| Single judge vs panel | **Mâu thuẫn** | PoLL (2024, nhiều tác giả, lab CN) vs Kohli (2026, 1 tác giả, NLI task) **kết luận ngược nhau** → unresolved |
| Cost/latency của judge | **Yếu** | 1 con số E1 preprint; phần còn lại là E3 đã loại + INFERENCE từ protocol |
| Metric F2 (NLI/atomic-fact) áp cho slide deck | **Yếu về tính chuyển giao** | Bản thân các metric là E1 mạnh, nhưng **không có nguồn nào validate chúng trên domain slide deck** — đây là INFERENCE, không phải evidence trực tiếp |
