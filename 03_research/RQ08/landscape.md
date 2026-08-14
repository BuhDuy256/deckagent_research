# RQ08 — Baseline Landscape (AGAINST WHAT)

**Ngày research: 2026-08-14.** Mọi capability/pricing/version của sản phẩm bên ngoài đều được ghi kèm ngày check — chúng thay đổi theo thời gian, không được coi là vĩnh viễn đúng.

Tài liệu này trả lời trục **AGAINST WHAT**: Deck Agent phải so với ai/cái gì thì con số "cải thiện" mới có nghĩa. Nó **không** chốt baseline nào sẽ dùng — xem `recommendation.md` cho đề xuất và `evidence.md` cho từng nguồn.

Phân loại claim theo `CLAUDE.md`: `PROJECT SOURCE` / `EXTERNAL EVIDENCE` / `INFERENCE` / `RECOMMENDATION`.

---

## 0. Khung đánh giá một baseline (dùng xuyên suốt file này)

**INFERENCE** — khung dưới đây do research này tổng hợp từ các nguồn ở `evidence.md`, không phải một framework có sẵn trong literature.

Một điểm so sánh chỉ đáng gọi là **baseline** nếu trả lời được cả 6 câu:

| # | Câu hỏi | Vì sao quan trọng |
| --- | --- | --- |
| B1 | **Nó trả lời câu hỏi nào?** | "Có tiến bộ không" ≠ "Kiến trúc có đáng không" ≠ "So với thị trường thì sao". Baseline sai → trả lời nhầm câu hỏi. |
| B2 | **Cùng input không?** | Khác input → delta không diễn giải được. |
| B3 | **Cùng output space không?** | Deck 10 slide PPTX vs. HTML one-pager không so trực tiếp được. |
| B4 | **Có kiểm soát được biến gây nhiễu không?** (model, prompt, seed, config, benchmark version) | Nếu không, delta có thể đến từ biến khác chứ không phải thay đổi ta quan tâm. |
| B5 | **Chạy lại được không?** (API/source/headless) | Baseline không reproduce được → không dùng cho regression, chỉ dùng một lần cho báo cáo. |
| B6 | **Có bị strawman/unfair không?** | Baseline yếu giả tạo làm hệ thống mình trông tốt hơn thực tế. |

**Degree of comparability** dùng trong file này có 4 mức:
- **FAIR** — B2–B4 kiểm soát được, delta diễn giải trực tiếp.
- **CONDITIONALLY FAIR** — so được nếu công bố rõ điều kiện và không claim vượt quá điều kiện đó.
- **CONTEXTUAL** — chỉ nói được "hệ thống ta ở đâu trên bản đồ", không nói được "kiến trúc ta tốt hơn".
- **NOT COMPARABLE** — bài toán/output khác nhau, so là sai.

---

## 1. FAMILY A — Historical baseline (current version vs. previous version)

**Trả lời:** "Hôm nay có tốt hơn hôm qua không?" (mục 1 của `EVALUATION_MISSION.md`).

### 1.1 Vì sao đây là baseline rẻ nhất nhưng dễ hỏng nhất

**INFERENCE.** Historical baseline không có vấn đề B2/B3 (cùng input, cùng output space, cùng codebase) nên trông có vẻ "miễn phí". Nhưng nó có một điểm yếu riêng mà 3 family kia không có: **cả hai phía của phép so đều là hệ thống stochastic**, nên noise có thể lớn hơn signal mà không ai nhận ra.

### 1.2 Những gì phải đóng băng để so sánh hợp lệ

**EXTERNAL EVIDENCE.** Biderman et al. 2024 (*Lessons from the Trenches on Reproducible Evaluation of Language Models*, arXiv:2405.14782 — E1) xác định 3 vấn đề phương pháp lặp đi lặp lại trong LM evaluation: (a) độ nhạy của model với evaluation setup, (b) khó so sánh đúng giữa các method, (c) thiếu reproducibility/transparency. Khuyến nghị trung tâm của họ là codify các quy ước ngầm và chia sẻ đủ chi tiết để chạy lại được.

**EXTERNAL EVIDENCE.** Sclar et al. 2023 (arXiv:2310.11324 — E1) đo được **chênh lệch tới 76 accuracy points** trên LLaMA-2-13B chỉ do thay đổi *format* của prompt (những thay đổi "vô hại" như dấu phân tách, khoảng trắng), và độ nhạy này **không biến mất** khi tăng kích thước model, tăng số few-shot example, hay instruction-tune.

**EXTERNAL EVIDENCE.** Mizrahi et al. 2024 (TACL 12:933–949 — E1) phân tích 6.5M instance trên 20 LLM và 39 task: instruction template khác nhau dẫn tới performance khác nhau **cả về giá trị tuyệt đối lẫn thứ hạng tương đối** giữa các hệ thống.

**EXTERNAL EVIDENCE.** Atil et al. 2024 (*Non-Determinism of "Deterministic" LLM Settings*, arXiv:2408.04667 — E1) chạy 5 LLM × 8 task × 10 run dưới cấu hình được coi là deterministic, và ghi nhận **accuracy dao động tới 15% giữa các run tự nhiên**, khoảng cách best–worst lên tới 70% ở một số cấu hình; **không model nào cho accuracy lặp lại nhất quán trên mọi task**.

**INFERENCE cho Deck Agent.** Ghép 4 nguồn trên: một "cải thiện +4 điểm fidelity giữa v0.2 và v0.3" trên **một** benchmark run là hoàn toàn có thể là noise. Danh sách cần cố định (và cần log) tối thiểu:

| Biến | Vì sao | Nguồn hỗ trợ |
| --- | --- | --- |
| Benchmark version (hash của input set) | Đổi input → không so được (`EVALUATION_MISSION.md` đã nêu) | PROJECT SOURCE |
| Model ID + version/snapshot | Provider đổi model dưới cùng một tên → delta giả | Biderman 2024 |
| Prompt version (byte-exact, cả system lẫn user) | 76 điểm chênh do format | Sclar 2023; Mizrahi 2024 |
| Decoding config (temperature, top_p, seed nếu provider hỗ trợ) | Giảm — **không loại bỏ** — variance | Atil 2024 |
| Số run per config + cách aggregate | Vì temperature=0 vẫn không deterministic | Atil 2024 |
| Judge model + judge prompt version (nếu dùng LLM judge) | Judge cũng là LLM, cũng drift | Biderman 2024 (route sang RQ06) |
| Deck IR schema version | Schema đổi → metric tính trên trường khác nhau | PROJECT SOURCE (NFR-12) |

### 1.3 Rủi ro làm phép so version thành vô hiệu

**INFERENCE**, dẫn từ evidence trên:

1. **So một run với một run.** Không có ước lượng variance → không phân biệt được improvement với noise. Atil 2024 cho thấy đây không phải rủi ro lý thuyết.
2. **Đổi nhiều thứ trong một lần merge** (prompt + model + retry policy). Delta không quy được về nguyên nhân nào — đúng trend (ii) mà Lipton & Steinhardt 2018 (arXiv:1807.03341 — E1) gọi là *failure to identify the sources of empirical gains*.
3. **Benchmark drift ngầm.** Thêm case mới vào benchmark rồi so với số cũ. Cần benchmark có version + freeze (PROJECT SOURCE `EVALUATION_MISSION.md`; chi tiết thuộc RQ07).
4. **Model provider drift.** Không phải thay đổi của team nhưng vẫn làm số đổi.
5. **Overfit vào benchmark.** Tune prompt lặp lại trên đúng bộ test → historical curve đẹp lên mà chất lượng thật không đổi. Cần held-out set (route sang RQ07).
6. **Judge drift** khi dùng LLM-as-judge cho cả hai phía (route sang RQ06).

**Degree of comparability: FAIR** — nếu và chỉ nếu bảng biến ở §1.2 được cố định và ghi log.

---

## 2. FAMILY B — Naive / direct baseline (single-shot LLM)

**Trả lời:** "Pipeline ContentPlanner → Deck IR → Exporter có đáng với độ phức tạp không?"

### 2.1 Định nghĩa trong ngữ cảnh Deck Agent

**INFERENCE.** Naive baseline = đưa thẳng nội dung nguồn cho một LLM và yêu cầu sinh deck trong một lượt, **không** có ContentPlanner, **không** có Deck IR như contract, **không** có editor loop. Có ít nhất 3 biến thể có ý nghĩa khác nhau:

| Biến thể | Output | So được với Deck Agent ở đâu |
| --- | --- | --- |
| B-1. Single-shot → JSON đúng Deck IR schema | Deck IR | Cô lập đóng góp của *planning*, giữ nguyên exporter → so được toàn bộ metric downstream |
| B-2. Single-shot → code/markup (HTML/Marp/python-pptx) | file trình chiếu | Gần với cách AutoPresent và PPTAgent đặt bài toán |
| B-3. Single-shot → PPTX qua exporter có sẵn | PPTX | So được end-to-end nhưng lẫn đóng góp của exporter |

**EXTERNAL EVIDENCE.** Ge et al. 2025 (*AutoPresent*, CVPR 2025, arXiv:2501.00912 — E1) benchmark hai họ method trên cùng SlidesBench: end-to-end **image generation** và **program generation**, và kết luận method sinh chương trình cho slide chất lượng cao hơn ở định dạng người dùng còn tương tác được. Điều này cho thấy việc **định nghĩa output space của baseline là một lựa chọn thiết kế có ảnh hưởng lớn tới kết luận**, không phải chi tiết kỹ thuật nhỏ.

### 2.2 Làm sao để naive baseline không thành strawman

Đây là phần quan trọng nhất của Family B, vì đây chính là chỗ dễ tự lừa mình nhất.

**EXTERNAL EVIDENCE.** Ferrari Dacrema, Cremonesi & Jannach 2019 (RecSys '19, arXiv:1907.06902 — E1) reproduce 18 neural recommender được publish ở hội nghị top; **6 trong 7 method reproduce được thường bị đánh bại bởi heuristic đơn giản (nearest-neighbor / graph-based) khi baseline được tune tử tế**; method còn lại không nhất quán vượt được một linear ranking method non-neural đã tune tốt. Nguyên nhân chính họ chỉ ra là baseline yếu và không được tune.

**EXTERNAL EVIDENCE.** Musgrave, Belongie & Lim 2020 (*A Metric Learning Reality Check*, ECCV 2020 — E1) cho thấy nhiều paper deep metric learning claim cải thiện lớn so với method 10 năm trước, nhưng cải thiện thật là "marginal at best" khi so công bằng. Các dạng bất công cụ thể họ liệt kê: **backbone khác nhau** (ResNet50 vs. GoogleNet/BN-Inception), **embedding size khác nhau** (512/1536 vs. 64 của baseline), và các thủ thuật training không được nhắc trong paper.

**INFERENCE — bản dịch sang Deck Agent.** Backbone/embedding size của họ tương đương với **model tier, prompt effort, context budget, số lần gọi LLM, và retry policy** của ta. Deck Agent thua strawman nếu:

| Anti-pattern | Biểu hiện cụ thể ở Deck Agent |
| --- | --- |
| Model không cân | Deck Agent chạy model mạnh, naive baseline chạy model rẻ |
| Prompt effort không cân | Prompt Deck Agent qua 8 vòng tinh chỉnh, prompt baseline viết một lần |
| Context không cân | Deck Agent nhận full extractor output, baseline nhận PDF thô cắt cụt |
| Retry/validation không cân | Deck Agent có validate + retry (NFR-21), baseline không có → baseline "fail" vì JSON hỏng chứ không phải vì planning kém |
| Output format thiên vị | Ép baseline sinh thẳng PPTX XML (khó) trong khi Deck Agent chỉ cần sinh JSON |
| Constraint không cân | Deck Agent biết `target_slides`/`slide_type`, baseline không được cho biết |

**EXTERNAL EVIDENCE hỗ trợ mục "prompt effort".** Sclar et al. 2023 và Mizrahi et al. 2024 (đã dẫn ở §1.2) cho thấy chỉ riêng format/paraphrase của prompt đã đủ đổi cả thứ hạng tương đối giữa các hệ thống. **INFERENCE:** vì vậy so Deck Agent (prompt đã tune) với naive baseline (prompt viết một lần) là so *effort tuning*, không phải so *architecture*. Cách giảm thiểu tối thiểu: chạy naive baseline trên nhiều prompt variant và **lấy variant tốt nhất** làm baseline, hoặc ít nhất báo cáo khoảng dao động.

### 2.3 Vì sao vẫn nên có naive baseline dù nó tốn công làm cho fair

**INFERENCE.** Kết quả của Ferrari Dacrema 2019 gợi ý rủi ro thật cho đồ án: nếu Deck Agent **không** so với single-shot baseline mạnh, phần "kiến trúc của chúng tôi đóng góp gì" trong báo cáo là claim không có evidence. Ngược lại, nếu naive baseline hóa ra rất mạnh, đó **cũng** là finding có giá trị khoa học (nó chỉ ra đóng góp thật của kiến trúc nằm ở chỗ khác — vd. controllability, editability, source traceability, cost — chứ không ở chất lượng slide thô).

**Degree of comparability: FAIR** — cùng input, cùng benchmark, biến kiểm soát được vì baseline do chính team dựng. Đây là baseline duy nhất ngoài Family A mà team kiểm soát 100% điều kiện.

---

## 3. FAMILY C — Simplified-pipeline baseline & Ablation

**Trả lời:** "Component X đóng góp gì?" (vd. Deck Agent không có ContentPlanner).

### 3.1 Một ablation study thật trong đúng domain này trông thế nào

**EXTERNAL EVIDENCE.** Zheng et al. 2025, *PPTAgent* (arXiv:2501.03936; EMNLP 2025 Main, ACL Anthology 2025.emnlp-main.728 — E1) công bố ablation trên chính bài toán sinh presentation. Bảng (trích từ paper, cấu hình Qwen2.5 LM + Qwen2-VL VM):

| Cấu hình | Success Rate (%) | Avg. PPTEval |
| --- | --- | --- |
| PPTAgent (full) | 95.0 | 3.67 |
| w/o Outline | 91.0 | 3.30 |
| w/o Schema | 78.8 | 3.45 |
| w/o Structure | 92.2 | 3.32 |
| w/o CodeRender | 74.6 | 3.66 |

Diễn giải của chính paper: bỏ schema extraction hại **success rate** nhiều nhất (78.8%), còn bỏ outline hại **coherence** nhiều nhất (3.36 vs 4.48).

**INFERENCE — vì sao bảng này quan trọng với Deck Agent hơn là một ví dụ minh họa.** Nó cho thấy **các component khác nhau hỏng theo các trục metric khác nhau**: `w/o CodeRender` gần như không đổi PPTEval (3.66 vs 3.67) nhưng sập success rate (74.6 vs 95.0). Nếu chỉ nhìn một metric tổng hợp, kết luận sẽ là "CodeRender không đóng góp gì" — sai hoàn toàn. Với Deck Agent, `w/o ContentPlanner` nhiều khả năng cũng hỏng khác nhau trên schema-validation rate, structure quality, và latency/cost. **Ablation phải đọc trên nhiều metric cùng lúc, không đọc trên một điểm tổng.**

Ngoài ra, ablation của PPTAgent chỉ đổi **một** component mỗi lần và giữ nguyên LM/VM — đây là điều kiện B4 ở §0.

### 3.2 Ablation candidate cho Deck Agent

**INFERENCE**, dựa trên pipeline trong `00_context/SYSTEM_MAP.md` (PROJECT SOURCE):

| Ablation | Bỏ/thay gì | Cô lập được điều gì | Ghi chú |
| --- | --- | --- | --- |
| A1. `w/o ContentPlanner` | Extractor → (LLM một lượt) → Deck IR | Đóng góp của bước planning riêng biệt | Trùng một phần với naive B-1 — xem cảnh báo §3.4 |
| A2. `w/o Outline/plan artifact` | Bỏ FR-07 plan trung gian, sinh thẳng slide | Đóng góp của việc có plan kiểm tra được | Tương ứng `w/o Outline` của PPTAgent |
| A3. `w/o Deck IR validation` | Bỏ Pydantic validate + retry (NFR-21) | Đóng góp của validation/retry với reliability | Kỳ vọng hại success rate, ít hại quality — giống pattern `w/o CodeRender` |
| A4. `w/o slide_type conditioning` | Bỏ `Deck.meta.slide_type` khỏi prompt | `slide_type` có thật sự tạo khác biệt không (FR-02/FR-20) | Đây vừa là ablation vừa là **system validation** cho một claim FR |
| A5. `w/o DesignSystem tokens` | Bỏ token engine, để LLM tự chọn style | Đóng góp của design token với consistency (NFR-52) | — |
| A6. `w/o Editor loop` | Chỉ lấy first-pass output | Đóng góp của editor với ready-to-use | Chỉ có nghĩa sau khi FR-14–17 tồn tại |

### 3.3 Lỗi thường gặp khi diễn giải kết quả ablation

**EXTERNAL EVIDENCE.** Lipton & Steinhardt 2018 (arXiv:1807.03341; đăng lại ACM Queue 17(1) — E1) liệt kê 4 trend có vấn đề, trong đó trend (ii) là *failure to identify the sources of empirical gains* — nhấn mạnh nhiều modification được đề xuất cùng lúc mà không có ablation đúng, khiến gain thật sự đến từ chỗ khác (vd. hyper-parameter tuning) bị che đi.

**EXTERNAL EVIDENCE.** Chen, Zaharia & Zou 2024 (*Are More LLM Calls All You Need? Towards Scaling Laws of Compound Inference Systems*, arXiv:2403.02419 — E1) chứng minh trong compound LLM system rằng performance có thể **tăng rồi giảm** theo số lần gọi LM (non-monotone), và lý giải rằng nguyên nhân là độ khó không đồng nhất giữa các query: thêm call giúp query "dễ" nhưng hại query "khó".

**INFERENCE — 6 lỗi diễn giải mà team cần tránh:**

1. **Cộng dồn đóng góp.** "Bỏ ContentPlanner mất 8 điểm, bỏ Editor mất 5 điểm → hai cái đóng góp 13 điểm." Sai: đóng góp không cộng tuyến tính. Chen et al. 2024 cho thấy quan hệ giữa lượng "machinery" và performance thậm chí không đơn điệu.
2. **Đọc một metric tổng.** Xem bảng PPTAgent §3.1 — `w/o CodeRender` "vô hại" theo PPTEval nhưng thảm họa theo success rate.
3. **Nhầm ablation với naive baseline.** `w/o ContentPlanner` **vẫn còn** Extractor, Deck IR schema, validation, exporter. Nó trả lời "component này đóng góp gì *bên trong* kiến trúc", không trả lời "kiến trúc có đáng so với không kiến trúc". Hai câu hỏi khác nhau, cần hai baseline khác nhau.
4. **Ablation biến thành strawman.** Bỏ ContentPlanner mà không cho phần thay thế cơ hội công bằng (vd. không cho nó prompt tương đương) → lặp lại đúng lỗi Musgrave 2020 mô tả, chỉ khác là tự làm với chính mình.
5. **Đổi nhiều hơn một thứ.** Bỏ ContentPlanner *và* đổi model → không quy được nguyên nhân (Lipton & Steinhardt trend ii).
6. **Kết luận từ một run.** Ablation cũng chịu variance như §1.2 (Atil et al. 2024).

### 3.4 Lưu ý ranh giới B ↔ C

**INFERENCE.** A1 (`w/o ContentPlanner`) và B-1 (single-shot → Deck IR) rất giống nhau về mặt cơ chế nhưng **khác nhau về câu hỏi và về những gì được giữ lại**. Nếu team gộp hai cái làm một, sẽ mất khả năng phân biệt "planning đóng góp gì" với "toàn bộ pipeline đóng góp gì". Đề xuất giữ riêng và ghi rõ trong spec cái nào giữ lại những gì.

**Degree of comparability: FAIR** — cùng codebase, cùng input, biến kiểm soát hoàn toàn.

---

## 4. FAMILY D — External related systems

Nguyên tắc áp dụng ở đây (`03_research/external_systems/README.md`): **không gọi một hệ thống là "competitor"/"baseline" chỉ vì nó sinh slide.**

### 4.1 Đã có protocol chuẩn cho việc so với hệ thống ngoài — và nó do người khác chứng minh, không phải ta tự nghĩ ra

**EXTERNAL EVIDENCE.** Chen et al. 2026 (*PresentBench*, arXiv:2603.07244, nộp 2026-03-07 — E1) mô tả protocol: *"For each method, we provide the same background materials together with the corresponding instructions, and require the model to produce a complete slide deck (either in PDF or PPTX format)"*, mọi hệ thống dùng default setting.

**EXTERNAL EVIDENCE.** Zhao et al. 2026 (*UniPPTBench*, arXiv:2605.17356, 2026-05-17 — E2, preprint chưa peer review) dùng đúng nguyên tắc đó và nói rõ hơn: *"For all systems, we provide the same user intent and background materials... Each method is used according to its default or officially recommended configuration"*, và **chuẩn hóa output**: hệ thống nào xuất PPTX/PDF thì đều được convert thành chuỗi ảnh slide có thứ tự trước khi judge chấm.

**INFERENCE.** Đây là lời giải cho vấn đề B3/B4 với sản phẩm đóng: **không cần kiểm soát model bên trong họ**, chỉ cần (a) cùng input, (b) cùng instruction, (c) chuẩn hóa output về một dạng chung (ảnh slide), (d) **tuyên bố rõ rằng ta so *sản phẩm ở cấu hình mặc định*, không so *kiến trúc*.** Điều này biến external comparison từ NOT COMPARABLE thành **CONDITIONALLY FAIR / CONTEXTUAL** — với điều kiện claim được viết đúng phạm vi.

### 4.2 Hệ thống đã verify (check 2026-08-14)

#### D-1. Open Design (nexu-io/open-design) — **VERIFIED**

| Trường | Giá trị (check 2026-08-14) |
| --- | --- |
| Canonical source | `https://github.com/nexu-io/open-design`; site canonical `https://open-design.ai` (trang `/official/` tự tuyên bố GitHub repo + open-design.ai là hai nguồn canonical duy nhất) |
| License | Apache-2.0 |
| Repo created | 2026-04-28 |
| Stars | 85,916 |
| Last push | 2026-08-14 |
| Latest release | tag `open-design-v0.19.1`, published 2026-08-14 |
| Input | Text brief mô tả nhu cầu thiết kế (+ design system, plugin, template tùy chọn) |
| Output | HTML artifact, **PPTX**, PDF, MP4, image — file thật, không phải mockup |
| Architecture | Desktop app local-first; Express daemon + SQLite; frontend Next.js; spawn các coding-agent CLI có sẵn trên PATH; MCP server; BYOK proxy cho endpoint OpenAI-compatible |
| Intermediate representation | Deck mode: preview `html`, **primary output `index.html`**, **secondary output `slides.json` (for PPTX export)** — trích `docs/skills-protocol.md` |
| Reproducibility | Có CLI + MCP server + source; BYOK nên **model do người chạy chọn** |
| Availability | Open source, miễn phí (bản release note 2026-08-14 có nhắc gói trả phí cho một số model) |
| Kiểm soát model/config? | **Có** — đây là điểm khác biệt lớn nhất so với Gamma/NotebookLM |
| Evaluation/testing | Có thư mục `e2e/`; **README không có benchmark/metric chính thức** cho chất lượng deck |

**Đối chiếu với PROJECT SOURCE.** `requirements-non-functional.pdf` mục 0.1 (bảng "Ai trả tiền token?") ghi đúng một dòng: *"User trả (BYOK) | Chi phí = 0 cho team; gần Open Design | Onboarding khó; không thống nhất model khi eval | Could sau bảo vệ / soft launch"*. **INFERENCE:** mô tả BYOK + local-first của nexu-io/open-design khớp với ngữ cảnh câu này, nhưng NFR doc **không** nêu URL/repo, nên việc "Open Design trong NFR = nexu-io/open-design" vẫn là **suy luận, chưa được team xác nhận** (xem `recommendation.md` §4).

**Cảnh báo trùng tên — đây là bẫy thật, không phải giả định.** Ít nhất 4 entity khác nhau mang tên gần giống, đều tồn tại thật (check 2026-08-14):

| Entity | Là gì | Có phải cái đang nói không? |
| --- | --- | --- |
| `nexu-io/open-design` | AI design workspace, BYOK, xuất PPTX | Ứng viên khớp nhất |
| GitHub org `opendesigndev` | Org khác, "Open Design" — **không** verify sâu trong RQ này | Không dùng làm evidence |
| `open-design-kit/opendesignkit` | Toolkit về quy trình design cộng tác phân tán, không sinh slide | Không |
| `OpenCoworkAI/open-codesign` | "Open CoDesign", BYOK, local-first, MIT — sản phẩm khác | Không |
| `manalkaff/opendesign` | **Unofficial**, community; port Claude Design thành markdown skill; 239 stars, MIT; có skill `make-a-deck` | Không — repo tự nhận unofficial |

**Điểm giống Deck Agent:** sinh deck từ agent, xuất PPTX, có JSON trung gian trước export, BYOK.
**Điểm khác quan trọng (INFERENCE):** (1) Input là **brief mô tả ý tưởng**, không phải **tài liệu nguồn dài cần trích trung thực** — nghĩa là bài toán "content fidelity / source traceability" (FR-03, NFR-53) **không phải bài toán của họ**; (2) họ **HTML-first** (`index.html` là primary, `slides.json` là secondary cho PPTX) trong khi Deck Agent **IR-first** (Deck IR là contract, exporter là consumer) — hai triết lý ngược nhau về vị trí của intermediate representation; (3) không có `slide_type`-style deck-level conditioning được document; (4) không có evaluation framework công bố.

**Kết luận:** **CONTEXTUAL**, không phải fair baseline cho câu hỏi kiến trúc. Giá trị lớn nhất của nó với đồ án là **architecture/design contrast** (IR-first vs HTML-first) và **evidence rằng mô hình BYOK là lựa chọn có tiền lệ thật** — chứ không phải một điểm số để so.

---

#### D-2. PPTAgent (icip-cas/PPTAgent) — **VERIFIED**

| Trường | Giá trị (check 2026-08-14) |
| --- | --- |
| Canonical source | arXiv:2501.03936; EMNLP 2025 Main (2025.emnlp-main.728); repo `https://github.com/icip-cas/PPTAgent` |
| License / stars / activity | MIT; 4,918 stars; created 2025-01-04; last push 2026-08-10 |
| Input | Tài liệu nguồn **+ một reference presentation** (để trích slide-level functional type và content schema) |
| Output | PPTX qua edit-based generation |
| Architecture | 2 giai đoạn: phân tích reference deck → sinh outline → sinh editing action lặp trên reference slide đã chọn |
| Reproducibility | **Cao** — source công khai, model tự chọn (paper chạy cả Qwen2.5 + Qwen2-VL) |
| Kiểm soát model/config? | Có |
| Evaluation riêng | **PPTEval** — 3 chiều Content/Design/Coherence, thang 1–5, kèm rationale |
| Human validation của PPTEval | Pearson trung bình **0.71** (Content 0.70, Design 0.90, Coherence **0.55**); 250 presentation; 4 nghiên cứu sinh; Fleiss' Kappa trung bình 0.59 |
| Baseline họ so | **DocPres** (Bandyopadhyay et al. 2024) và **KCTV** (Cachola et al. 2024) — cả hai chạy không có vision model |

**Điểm khác quan trọng (INFERENCE):** PPTAgent **cần một reference presentation làm input**. Deck Agent không có ràng buộc này (design token engine đóng vai trò đó). Cho PPTAgent một reference deck mà Deck Agent không có → lợi thế cho PPTAgent; không cho → làm PPTAgent chạy ngoài điều kiện thiết kế của nó → strawman. **Đây là ví dụ điển hình của việc "cùng sinh slide" ≠ "cùng bài toán".**

**Kết luận:** **CONDITIONALLY FAIR** cho end-to-end deck quality **nếu** chấp nhận cấp reference deck và tuyên bố rõ; **NOT COMPARABLE** cho câu hỏi kiến trúc của Deck Agent. Giá trị cao nhất: **template ablation** (§3.1) và **PPTEval** như một điểm so trong RQ06.

---

#### D-3. AutoPresent / SlidesBench — **VERIFIED**

- **Canonical:** arXiv:2501.00912, CVPR 2025; tác giả Jiaxin Ge, Zora Zhiruo Wang, Xuhui Zhou, Yi-Hao Peng, Sanjay Subramanian, Qinyue Tan, Maarten Sap, Alane Suhr, Daniel Fried, Graham Neubig, Trevor Darrell. Nộp 2025-01-01, revised 2025-06-19.
- **Input:** natural-language instruction (không phải tài liệu dài).
- **Output:** slide qua code generation; model AutoPresent là 8B Llama-based, train trên 7k cặp instruction–code.
- **Benchmark:** SlidesBench — 7k train / 585 test, từ 310 deck, 10 domain; **reference-based** (giống deck đích) + **reference-free** (chất lượng design tự thân).
- **Kết luận comparability:** **NOT COMPARABLE như baseline** — input là instruction, không phải document-grounded generation; Deck Agent's fidelity-to-source claim không tồn tại trong bài toán này. **Giá trị: benchmark design inspiration** (đặc biệt cặp reference-based/reference-free) → route sang RQ07/RQ01.

---

#### D-4. EvoPresent, Auto-Slides — **VERIFIED tồn tại, chưa research sâu**

| System | Canonical | Ghi chú (check 2026-08-14) |
| --- | --- | --- |
| EvoPresent | `https://github.com/UCSB-AI/EvoPresent`; site `evopresent.github.io`; repo tự ghi **ICLR 2026**, paper *"Presenting a Paper is an Art: Self-Improvement Aesthetic Agents for Academic Presentations"* | Có Checker Agent dựa trên model RL đa nhiệm `PresAesth` để phê bình và tự cải thiện draft |
| Auto-Slides | `https://github.com/Westlake-AGI-Lab/Auto-Slides`; repo tự ghi **ICME 2026** | Multi-agent tương tác, sinh presentation cho research paper |

**INFERENCE:** cả hai đều nhắm **academic paper → presentation**, hẹp hơn Deck Agent (PDF/Word/web/YouTube, 3 `slide_type` mode). Xếp **CONTEXTUAL**; EvoPresent đáng đọc như **design inspiration** cho self-critique loop (liên quan Editor Loop FR-14–17), không phải baseline.

---

#### D-5. Sản phẩm thương mại (Gamma, NotebookLM/Gemini Notebook, Manus, Kimi, Skywork, Doubao, Zhipu, Tiangong, Canvas)

**EXTERNAL EVIDENCE — chúng đã được benchmark bởi bên thứ ba, ta không cần tự chạy hết.** PresentBench (E1) công bố điểm tổng của 8 hệ thống: NotebookLM 62.5 > Manus 1.6 57.8 > Tiangong 54.7 > Zhipu 53.6 > PPTAgent v2 50.2 > Gamma 49.2 > Doubao 48.0 > Qwen 35.9. UniPPTBench (E2) đánh giá 9 hệ thống gồm NotebookLM, Manus, Canvas (Gemini 3 Flash Canvas interface), Kimi (Agent PPT mode), Skywork (PPT generation mode), AutoSlides, AutoPresent, EvoPresent + 2 biến thể của chính họ.

> **Cảnh báo về độ tin cậy của các con số trên:** chúng được đọc qua fetch tự động trang arXiv HTML và trang Hugging Face paper của PresentBench, hai lần fetch cho kết quả khớp nhau. Trước khi đưa vào báo cáo/bảo vệ, **phải mở bảng gốc trong PDF và đối chiếu tay** — quy tắc "không dùng số chưa tự kiểm chứng" trong `04_evidence/EVIDENCE_POLICY.md`.

**Khả năng chạy lại (reproducibility) — điểm quyết định:**

| System | API/programmatic? | Kiểm soát model/config? | Ghi chú (check 2026-08-14) |
| --- | --- | --- | --- |
| **Gamma** | **Có** — Generate API v1.0, GA từ 2025-11-05; v0.2 sunset 2026-01-16; base `https://public-api.gamma.app/v1.0/`, header `X-API-KEY` | **Không** — doc ghi rõ "varies by the text generation model Gamma selects internally"; theme phải tạo sẵn trong Gamma rồi tham chiếu bằng ID | Cần gói **Pro/Ultra/Teams/Business**. Credit: text 1–3 credits/card; image 2–15 (standard) → 30–125 (ultra). Ví dụ trong doc: deck 10 card + 5 ảnh model cơ bản ≈ **20–60 credits**. Rate limit **không** được document. **Quy đổi credit → USD chưa verify trong research này.** |
| **NotebookLM / Gemini Notebook** | **Một phần** — Google Cloud có doc chính thức *Gemini Notebook Enterprise* với Notebooks API (`notebooks.create`, `notebooks.get`, quản lý source), cần license + role Cloud NotebookLM User | Không | **Chưa verify** rằng API này expose chức năng **sinh slide deck**. Doc chính thức chỉ xác nhận quản lý notebook/source. Có thư viện **unofficial** `teng-lin/notebooklm-py` reverse-engineer API nội bộ và tự mô tả có tải PPTX/PDF — **UNOFFICIAL, rủi ro ToS, E3 — không dùng làm baseline chính thức.** |
| Manus / Kimi / Skywork / Doubao / Zhipu / Tiangong / Canvas | Chưa research trong RQ này | Không | UniPPTBench chạy chúng ở **default/officially recommended configuration**, gợi ý là chạy qua UI/mode có sẵn chứ không phải API tự do cấu hình |

**INFERENCE.** Với sản phẩm đóng, **B4 (kiểm soát biến) là bất khả thi về nguyên tắc**: model bên dưới có thể đổi bất kỳ lúc nào mà không thông báo, và không có version pin. Hệ quả: **một con số so với Gamma/NotebookLM là ảnh chụp tại một ngày cụ thể, không phải một phép đo lặp lại được.** Nó có thể vào báo cáo dưới dạng *"tại ngày X, ở cấu hình mặc định, trên benchmark Y"* — không được viết thành *"Deck Agent tốt hơn Gamma"*.

**Degree of comparability:** Gamma = **CONDITIONALLY FAIR** (có API, chạy lại được, nhưng không kiểm soát model, tốn tiền, và drift). NotebookLM = **CONTEXTUAL** (đường API cho slide chưa verify). Các sản phẩm còn lại = **CONTEXTUAL**.

### 4.3 Thứ KHÔNG phải hệ thống — benchmark/evaluation framework

Bốn artifact dưới đây thường bị nhầm là "competitor" nhưng thực chất là **hạ tầng đo**, và giá trị của chúng cho RQ08 nằm ở chỗ chúng **định nghĩa sẵn protocol so sánh nhiều hệ thống**:

| Artifact | Là gì | Dùng được cho RQ08 ở đâu |
| --- | --- | --- |
| **PresentBench** (arXiv:2603.07244; repo `PresentBench/PresentBench`, Apache-2.0, 17 stars, created 2026-03-20, push 2026-07-29; site `presentbench.github.io`) | 238 instance, checklist nhị phân trung bình 54.1 item/instance, 5 chiều: presentation fundamentals / visual design & layout / content completeness / content correctness / content fidelity. Spearman với human **0.532** vs PPTEval **0.303** | **Protocol so sánh nhiều hệ thống** + bộ điểm tham chiếu sẵn có của 8 sản phẩm. **Đây chính là "PresentBench" mà architecture doc §8 nhắc tới — Q-005 nay đã VERIFY được là entity thật.** |
| **PPTEval** (trong PPTAgent) | 3 chiều, thang 1–5 | Baseline method để so trong RQ06; đã bị PresentBench báo cáo là kém tương quan human hơn |
| **SlidesBench** (trong AutoPresent) | 585 test case, reference-based + reference-free | Benchmark design inspiration (RQ07) |
| **UniPPTBench** (arXiv:2605.17356 — E2 preprint) | 126 task, 4 input setting (vague-prompt / long-document / multimodal-document / multi-source), 6 domain; UniPPTEval 5 metric chung + metric theo scenario; code/data "will be publicly available" | 4 input setting này gần với các loại input Deck Agent nhận (FR-18a) → inspiration cho RQ07; **chưa peer review, chưa xác nhận code đã public** |

---

## 5. UNVERIFIED ENTITY

### 5.1 "test hapkin slide"

**Trạng thái: UNVERIFIED ENTITY. Không dùng làm evidence. Không đoán nó là gì.**

Đã search (2026-08-14): `"hapkin" slide OR presentation OR deck tool`; `"hapkin" AI tool software`; `hapkin github repository`. Không có kết quả nào khớp tên "hapkin" ở dạng một project/tool/benchmark về slide generation. Các kết quả trả về là các entity **khác tên** (không phải biến thể chính tả đã xác nhận): Haiku Deck, Napkin-like tool, Hapie AI, Haptik, harpin AI, HAPP, HapPhi, Vidvox/hap, HaplyHaptics.

**Không kết luận** entity này là bất kỳ cái nào ở trên. Cần user/team cung cấp tên chính xác hoặc nguồn gốc (Q-004 trong `05_decisions/OPEN_QUESTIONS.md` vẫn OPEN).

### 5.2 Ghi chú về "PresentBench" (Q-005)

**Q-005 nay đã có câu trả lời từ EXTERNAL EVIDENCE E1** (repo chính chủ + arXiv:2603.07244 + project page). Việc route evidence này sang RQ01/RQ06/RQ07 nằm ngoài phạm vi RQ08 — RQ08 chỉ dùng nó cho khía cạnh **protocol so sánh giữa các hệ thống**. Việc cập nhật trạng thái Q-005 trong `OPEN_QUESTIONS.md` để session khác/user thực hiện (RQ08 không được sửa file đó).

---

## 6. Bảng tổng hợp 4 family

| Family | Trả lời câu hỏi | Cost | Comparability | Kiểm soát biến | Chạy lại được | Rủi ro lớn nhất |
| --- | --- | --- | --- | --- | --- | --- |
| **A. Historical** | Có tiến bộ không? | Thấp | FAIR | Đầy đủ | Có | Noise > signal nếu chỉ 1 run (Atil 2024) |
| **B. Naive single-shot** | Kiến trúc có đáng không? | Trung bình | FAIR | Đầy đủ | Có | Strawman (Ferrari Dacrema 2019; Musgrave 2020) |
| **C. Ablation** | Component nào đóng góp gì? | Trung bình | FAIR | Đầy đủ | Có | Diễn giải sai: cộng dồn / một metric / lẫn với B (Lipton & Steinhardt 2018; Chen 2024) |
| **D. External** | Ta đứng ở đâu trên bản đồ? | Cao | CONDITIONALLY FAIR → CONTEXTUAL | **Không** | Chỉ với Gamma / open-source | Claim vượt quá điều kiện đo; số hết hạn theo thời gian |
