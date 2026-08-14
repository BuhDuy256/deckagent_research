# RQ08 — Baseline Strategy cho Deck Agent (RECOMMENDATION)

**Ngày research: 2026-08-14.** Đọc cùng `landscape.md` (4 baseline family + external system) và `evidence.md` (từng nguồn support điều gì).

**Đây là RECOMMENDATION, không phải decision.** Không có mục nào trong file này đã được team/giảng viên chốt. Mọi đề xuất trace theo `problem → evidence → reasoning → recommendation`. Việc promote sang `06_design/BASELINE_SPEC.md` hay `DECISION_LOG.md` cần user xác nhận rõ ràng.

---

## 1. Nguyên tắc dẫn đường: mỗi baseline trả lời đúng một câu hỏi

**INFERENCE.** `EVALUATION_MISSION.md` (PROJECT SOURCE) đặt 2 câu xuyên suốt, và `PROJECT_CONTEXT.md` tách thành 3 mục đích. Ánh xạ sang baseline:

| Câu hỏi team cần trả lời | Baseline family duy nhất trả lời được | Không thể thay bằng |
| --- | --- | --- |
| "Hôm nay có tốt hơn hôm qua không?" | A — Historical | Không family nào khác |
| "Kiến trúc (ContentPlanner + Deck IR) có đáng độ phức tạp không?" | B — Naive single-shot | Ablation **không** trả lời được câu này |
| "Component X đóng góp gì?" | C — Ablation | Naive baseline **không** trả lời được câu này |
| "Ta đứng ở đâu so với thị trường?" | D — External | Chỉ trả lời được ở mức *contextual* |

Sai lầm tốn kém nhất mà RQ08 muốn ngăn: **dùng một baseline để trả lời câu hỏi của baseline khác** — đặc biệt là gộp B và C (xem `landscape.md` §3.4), và dùng D để claim điều mà chỉ B/C mới chứng minh được.

---

## 2. Đề xuất theo từng family

### 2.1 Family A — Historical (bắt buộc, mức ưu tiên cao nhất)

**Problem.** Không có historical baseline thì không có câu trả lời cho mục đích #1 của mission, và mọi metric khác trở thành ảnh chụp một lần.

**Evidence.** Atil et al. 2024 (E1): 5 LLM × 8 task × 10 run dưới cấu hình "deterministic" cho **accuracy dao động tới 15% giữa các run tự nhiên**, best–worst tới 70%. Sclar et al. 2023 (E1): đổi format prompt gây chênh tới **76 accuracy points**. Mizrahi et al. 2024 (E1): template khác nhau đổi **cả thứ hạng tương đối** giữa các hệ thống. Biderman et al. 2024 (E1): độ nhạy với evaluation setup + thiếu reproducibility là 2 trong 3 vấn đề phương pháp lặp lại của LM evaluation.

**Reasoning.** Cả hai phía của phép so version đều stochastic. Nếu chỉ chạy 1 run/version, delta quan sát được có thể hoàn toàn là noise, và team sẽ merge/revert dựa trên số vô nghĩa — tức là evaluation-driven development chạy sai hướng.

**RECOMMENDATION.**
- Coi **run manifest** là artifact bắt buộc của mọi lần chạy benchmark, ghi tối thiểu: benchmark version (hash input set), model ID + snapshot, prompt version (byte-exact, cả system lẫn user), decoding config (temperature/top_p/seed), số run, Deck IR schema version, judge model + judge prompt version (nếu có), commit hash của Deck Agent.
- **N ≥ 3 run/config** cho các metric AI-quality, báo cáo trung vị + khoảng (min–max hoặc IQR). Deterministic metric (schema validation, overflow, export success) có thể N = 1 nếu pipeline đã chứng minh là ổn định.
- **Một thay đổi một lần** khi so version (Lipton & Steinhardt 2018, E1, trend ii). Nếu buộc phải merge nhiều thay đổi, ghi rõ trong report rằng delta **không** quy được cho nguyên nhân nào.
- Có **held-out set** tách khỏi bộ dùng để tune prompt, để phát hiện overfit vào benchmark (chi tiết thuộc RQ07).

**Cost:** thấp — dùng lại chính benchmark và harness đã có.

### 2.2 Family B — Naive single-shot (bắt buộc cho thesis, không bắt buộc chạy hằng ngày)

**Problem.** Toàn bộ luận điểm "kiến trúc của chúng tôi đáng tồn tại" không có evidence nếu thiếu family này.

**Evidence.** Ferrari Dacrema et al. 2019 (E1): **6/7 neural method reproduce được thường bị heuristic đơn giản đánh bại khi baseline được tune tử tế** — baseline yếu tạo ảo giác tiến bộ ở quy mô cả lĩnh vực. Musgrave et al. 2020 (E1): cải thiện được claim nhiều năm là "marginal at best" khi cân backbone/embedding size. Ge et al. 2025 / AutoPresent (E1): lựa chọn output space của baseline (image generation vs program generation) làm đổi kết luận.

**Reasoning.** Rủi ro không phải là "baseline quá mạnh" mà là **strawman**: nếu naive baseline thua vì lý do không liên quan tới planning (JSON hỏng vì không validate, model rẻ hơn, prompt viết một lần, không được cho biết `target_slides`/`slide_type`), thì delta đo được là delta của *effort*, không phải của *architecture* — và phần đánh giá kiến trúc trong báo cáo sẽ không đứng vững trước phản biện.

**RECOMMENDATION.**
- Dựng **B-1 làm baseline chính**: single-shot LLM → JSON đúng Deck IR schema → dùng **chính exporter của Deck Agent**. Lý do: giữ nguyên downstream nên delta cô lập được phần planning; và mọi metric hiện có áp dụng được không cần sửa.
- **Checklist cân biến bắt buộc** (nếu thiếu bất kỳ dòng nào thì ghi rõ là điều kiện không cân trong report):

| Biến | Quy tắc |
| --- | --- |
| Model tier | Giống hệt Deck Agent (cùng model ID + snapshot) |
| Context | Baseline nhận **cùng extractor output**, không nhận PDF thô cắt cụt |
| Constraint | Baseline được cho biết `target_slides` và `slide_type` như Deck Agent |
| Validation/retry | Baseline có cùng chính sách validate + retry (NFR-21) — nếu không, baseline "fail" vì JSON hỏng chứ không phải vì planning kém |
| Prompt effort | Chạy **≥ 3 prompt variant** cho baseline, lấy variant **tốt nhất** làm baseline; hoặc tối thiểu báo cáo khoảng dao động (Sclar 2023; Mizrahi 2024) |
| Output format | Không ép baseline sinh định dạng khó hơn (vd. PPTX XML thô) trong khi Deck Agent chỉ sinh JSON |

- **Ghi trước khi chạy** rằng kết quả "naive baseline mạnh bất ngờ" cũng là finding hợp lệ — khi đó đóng góp thật của kiến trúc nằm ở controllability / source traceability / editability / cost, không ở chất lượng slide thô. Cam kết này (pre-registration nhẹ) giúp tránh việc sau đó đi làm yếu baseline để cứu kết luận.

**Cost:** trung bình — chủ yếu là công dựng + tune prompt baseline, chạy lại rẻ.

### 2.3 Family C — Ablation (bắt buộc cho thesis, chọn lọc cho dev)

**Problem.** Cần trả lời "ContentPlanner đóng góp gì", vốn là claim trung tâm của architecture doc (PROJECT SOURCE: ContentPlanner = "LÕI của đồ án").

**Evidence.** PPTAgent (E1, EMNLP 2025) công bố ablation trong đúng domain: full 95.0% SR / 3.67 PPTEval; `w/o Schema` 78.8 / 3.45; `w/o CodeRender` **74.6 / 3.66**. Chen et al. 2024 (E1): performance của compound system có thể **tăng rồi giảm** theo số lần gọi LM (non-monotone). Lipton & Steinhardt 2018 (E1): *failure to identify the sources of empirical gains*.

**Reasoning.** Bảng PPTAgent chứng minh một điều rất cụ thể: `w/o CodeRender` gần như **không đổi** PPTEval (3.66 vs 3.67) nhưng **sập** success rate (74.6 vs 95.0). Nếu đọc trên một metric tổng hợp, kết luận sẽ là "component này vô dụng" — sai hoàn toàn. Đây chính là rủi ro Deck Agent sẽ gặp với `w/o Deck IR validation` và `w/o ContentPlanner`.

**RECOMMENDATION.**
- **Ưu tiên 3 ablation cho thesis:** `A1 w/o ContentPlanner`, `A4 w/o slide_type conditioning`, `A3 w/o Deck IR validation`.
  - A1 trả lời câu hỏi kiến trúc trung tâm.
  - A4 **vừa là ablation vừa là system validation** cho FR-02/FR-20 (cùng PDF đổi `slide_type` phải cho cấu trúc khác có chủ đích) — hiệu suất kép, chi phí thấp.
  - A3 dự kiến hỏng theo trục reliability chứ không theo trục quality, nên là ví dụ tốt để chứng minh team hiểu đúng cách đọc ablation.
- **Đọc mọi ablation trên ≥ 3 trục cùng lúc:** một quality metric, một reliability metric (schema-validation/success rate), một operational metric (latency hoặc cost). Không bao giờ kết luận từ một điểm tổng.
- **Cấm cộng dồn đóng góp** giữa các ablation (Chen et al. 2024).
- **Giữ A1 và B-1 tách biệt** trong spec, ghi rõ mỗi cái giữ lại những gì (`landscape.md` §3.4).
- Chỉ đổi **một** component mỗi lần, giữ nguyên model/prompt/benchmark.

**Cost:** trung bình — chi phí chính là dựng cấu hình chạy được ở chế độ ablation, nên thiết kế pipeline để component bật/tắt được bằng config thay vì phải fork code.

### 2.4 Family D — External (optional, và phải viết claim đúng phạm vi)

**Problem.** "Competitor" là thứ hay bị mặc định coi là baseline quan trọng nhất, nhưng lại là loại khó fair nhất.

**Evidence.** PresentBench (E1) chạy protocol *"For each method, we provide the same background materials together with the corresponding instructions, and require the model to produce a complete slide deck (either in PDF or PPTX format)"* ở default setting, và đã công bố điểm 8 hệ thống (NotebookLM 62.5 > Manus 1.6 57.8 > Tiangong 54.7 > Zhipu 53.6 > PPTAgent v2 50.2 > Gamma 49.2 > Doubao 48.0 > Qwen 35.9). UniPPTBench (E2 preprint) dùng cùng nguyên tắc và bổ sung bước **chuẩn hóa output thành chuỗi ảnh slide** trước khi judge.

**Reasoning.** Protocol này giải quyết B2/B3 (cùng input, cùng output space sau chuẩn hóa) nhưng **không** giải quyết B4 (kiểm soát model/config bên trong sản phẩm đóng). Hệ quả logic: kết quả so với sản phẩm thương mại là **ảnh chụp tại một ngày ở cấu hình mặc định**, không phải phép đo lặp lại được, và **không** nói được gì về kiến trúc.

**RECOMMENDATION.**
- Nếu làm, làm theo đúng thứ tự ưu tiên khả thi: **Open Design** (open-source, BYOK → kiểm soát được model) → **PPTAgent** (open-source, MIT, kiểm soát được model) → **Gamma** (có API chính thức) → phần còn lại (chỉ chạy tay, chỉ định tính).
- Với mọi so sánh external, câu trong báo cáo phải có đủ 4 vế: *"tại ngày YYYY-MM-DD, ở cấu hình mặc định của sản phẩm, trên benchmark v.X, với judge Y"*.
- **Không** dùng external comparison để support claim về kiến trúc.

**Cost:** cao (tiền + công chạy tay + rủi ro hết hạn theo thời gian).

---

## 3. Ghi chú riêng về `slide_type` — cơ hội mà 4 family không tự nhiên bao phủ

**INFERENCE.** FR-02/FR-20 (PROJECT SOURCE) claim: cùng một PDF, đổi `slide_type` (`teaching | catchup | speaker_support`) phải tạo cấu trúc deck khác **có chủ đích**. Đây không phải câu hỏi "tốt hơn baseline nào" mà là câu hỏi **"hệ thống có phản ứng với biến điều khiển không"**.

Cách rẻ nhất để lấy evidence cho claim này là dùng chính Deck Agent làm baseline cho chính nó: cùng input, chỉ đổi `slide_type` → đo khác biệt cấu trúc. Đây là **self-comparison / controlled variation**, nằm giữa Family A và Family C. Không có external system nào giúp được ở đây (`landscape.md` §4.2: Open Design không có deck-level conditioning tương đương được document; PPTAgent điều kiện hóa qua reference deck chứ không qua mode).

**RECOMMENDATION:** đưa self-comparison theo `slide_type` vào bộ dev baseline — rẻ, không cần external, và trực tiếp phục vụ một FR đã có.

---

## 4. Bốn mục kết luận

### 4.1 Recommendation

#### (a) Minimum baseline set cho **development** (chạy thường xuyên, rẻ)

| # | Baseline | Tần suất đề xuất | Vì sao ở tier này |
| --- | --- | --- | --- |
| D1 | **Historical: current vs. previous tagged version** trên frozen benchmark, N ≥ 3 run, có run manifest | Mỗi PR có ảnh hưởng AI-quality + nightly | Trả lời mục đích #1 của mission; là baseline duy nhất rẻ đủ để chạy liên tục |
| D2 | **Self-comparison theo `slide_type`** (cùng input, đổi mode) | Nightly hoặc theo milestone | Rẻ; trực tiếp phục vụ FR-02/FR-20; không cần external |
| D3 | **`A3 w/o Deck IR validation`** (ablation reliability) | Theo milestone, không cần mỗi PR | Chạy rẻ, phát hiện sớm khi retry/validation bị regress |

**KHÔNG đưa vào dev tier:** naive single-shot đầy đủ (tốn công tune prompt để fair), mọi external comparison (đắt + drift). Chúng thuộc tier thesis.

> Việc phân tier chi tiết (mỗi commit / PR / nightly / milestone) là workstream "Development Evaluation Protocol" đã được chốt ở D-006 — RQ08 chỉ đề xuất **baseline nào thuộc tier rẻ**, không thiết kế cadence.

#### (b) Minimum baseline set cho **thesis / bảo vệ** (defensible)

| # | Baseline | Trả lời câu hỏi | Bắt buộc? |
| --- | --- | --- | --- |
| T1 | Historical: đường cong qua các version chính (v0.1 → final), có variance | "Chúng tôi có cải thiện trong suốt đồ án không?" | **Bắt buộc** |
| T2 | **Naive single-shot B-1** (đã cân biến theo checklist §2.2) | "Kiến trúc có đáng độ phức tạp không?" | **Bắt buộc** — thiếu cái này thì phần đánh giá kiến trúc không có evidence |
| T3 | **Ablation `A1 w/o ContentPlanner`**, đọc trên ≥ 3 trục metric | "ContentPlanner đóng góp gì?" | **Bắt buộc** — đây là claim trung tâm của architecture doc |
| T4 | **Ablation `A4 w/o slide_type conditioning`** | FR-02/FR-20 có thật sự đạt không | **Bắt buộc** (rẻ, và là system validation) |
| T5 | Operational trade-off table (latency P50/P95, cost/deck, failure rate) đi kèm **mọi** so sánh trên | "Đổi lại chúng ta mất gì?" | **Bắt buộc** — NFR-92 yêu cầu rõ phải báo cáo trade-off số lần gọi LLM vs chất lượng (D-005 đã đưa Operational thành layer chính thức) |

**INFERENCE:** T1–T5 hoàn toàn nằm trong tầm kiểm soát của team, không phụ thuộc bên thứ ba, và đủ để trả lời cả 3 mục đích của mission. Đây là lý do RQ08 xếp external comparison là **optional**, không phải thiếu sót.

#### (c) Optional external comparison (nếu còn thời gian/ngân sách)

Xếp theo thứ tự **khả thi giảm dần**, chỉ làm nếu T1–T5 đã xong:

| Ưu tiên | System | Vì sao khả thi | Điều kiện bắt buộc kèm theo |
| --- | --- | --- | --- |
| O1 | **Open Design** (`nexu-io/open-design`, Apache-2.0, release `open-design-v0.19.1` ngày 2026-08-14, check 2026-08-14) | Open-source + BYOK → **chạy được cùng model với Deck Agent**, loại bỏ được biến model | Phải nói rõ input của nó là brief, không phải document-grounded → so ở mức **contextual**, và ghi rõ đây là **architecture contrast** (họ HTML-first, ta IR-first) |
| O2 | **PPTAgent** (`icip-cas/PPTAgent`, MIT, 4,918 stars, last push 2026-08-10, check 2026-08-14) | Source công khai, model tự chọn, có paper mô tả setup | Nó **cần một reference presentation làm input** — phải cấp reference deck và tuyên bố rõ, nếu không là strawman |
| O3 | **Gamma** (Generate API v1.0, GA từ 2025-11-05, check 2026-08-14) | External thương mại **duy nhất** có API chính thức → chạy lại được | Không chọn/pin được model bên dưới ("varies by the text generation model Gamma selects internally"); tốn credit (deck 10 card + 5 ảnh ≈ 20–60 credits theo doc chính thức); rate limit không được document |
| O4 | **Trích dẫn điểm PresentBench có sẵn** thay vì tự chạy | Rẻ nhất; PresentBench (E1) đã công bố điểm 8 hệ thống | **Chỉ hợp lệ nếu Deck Agent cũng được chấm bằng đúng PresentBench** trên đúng 238 instance của nó. Nếu không, đây là so số từ hai thang khác nhau — xem §4.1(d) |

#### (d) Comparison nào **unfair hoặc infeasible**, và vì sao

Phần này quan trọng ngang phần recommend: nó bảo vệ team khỏi việc đưa kết luận sai vào báo cáo.

| # | Comparison | Trạng thái | Vì sao |
| --- | --- | --- | --- |
| U1 | So điểm Deck Agent (đo bằng metric của team) với **điểm PresentBench đã công bố** của NotebookLM/Gamma/... | **UNFAIR — không được làm** | Hai thang đo khác nhau, hai bộ input khác nhau, hai judge khác nhau. Số không so được. Chỉ hợp lệ nếu Deck Agent chạy đúng benchmark đó, đúng instance, đúng judge protocol. |
| U2 | "Deck Agent tốt hơn Gamma/NotebookLM" ở dạng câu khẳng định chung | **UNFAIR** | Model bên dưới không pin được và có thể đổi bất kỳ lúc nào → phép đo không lặp lại được. Chỉ được viết dạng *"tại ngày X, ở cấu hình mặc định, trên benchmark Y"*. |
| U3 | So với **NotebookLM / Gemini Notebook** qua đường lập trình | **INFEASIBLE ở mức chính thức** | Doc chính thức Google Cloud (check 2026-08-14) xác nhận Notebooks API cho **quản lý notebook/source** (`notebooks.create`, `notebooks.get`), cần license Gemini Notebook Enterprise + role Cloud NotebookLM User; **chưa verify** rằng API expose chức năng sinh slide deck. Thư viện `teng-lin/notebooklm-py` là **unofficial, reverse-engineered (E3)** → rủi ro ToS, không dùng làm baseline chính thức. Còn lại chỉ chạy tay được → không dùng cho regression. |
| U4 | Dùng **ablation `A1 w/o ContentPlanner`** để trả lời "kiến trúc có đáng không" | **SAI CÂU HỎI** | A1 vẫn giữ Extractor, Deck IR schema, validation, exporter → nó đo đóng góp của một component *bên trong* kiến trúc, không đo giá trị của cả kiến trúc so với không kiến trúc. Câu đó cần B-1. |
| U5 | Dùng **naive single-shot** để kết luận "ContentPlanner đóng góp N điểm" | **SAI CÂU HỎI** (chiều ngược lại của U4) | B-1 bỏ nhiều thứ cùng lúc; quy toàn bộ delta cho ContentPlanner là lỗi *failure to identify the sources of empirical gains* (Lipton & Steinhardt 2018, E1). |
| U6 | Cộng dồn kết quả nhiều ablation ("A1 mất 8 điểm + A5 mất 5 điểm = 13 điểm") | **DIỄN GIẢI SAI** | Đóng góp component không cộng tuyến tính; Chen et al. 2024 (E1) cho thấy quan hệ giữa lượng machinery và performance thậm chí không đơn điệu. |
| U7 | Kết luận ablation/version từ **một run** hoặc từ **một metric tổng hợp** | **KHÔNG ĐỦ EVIDENCE** | Atil et al. 2024 (E1): dao động tới 15% giữa các run "deterministic". Bảng PPTAgent (E1): `w/o CodeRender` gần như không đổi PPTEval nhưng sập success rate 20 điểm. |
| U8 | So với **AutoPresent / SlidesBench** như một baseline | **NOT COMPARABLE** | Input của nó là natural-language instruction, không phải document grounding. Bài toán content fidelity / source traceability (FR-03, NFR-53) **không tồn tại** trong bài toán của họ. Giá trị của nó là benchmark design inspiration (RQ07), không phải baseline. |
| U9 | So với **EvoPresent / Auto-Slides** như baseline | **CONTEXTUAL, chưa đủ căn cứ** | Cả hai nhắm academic paper → presentation, hẹp hơn Deck Agent (PDF/Word/web/YouTube + 3 `slide_type` mode). Chưa đọc paper/code trong RQ này. |
| U10 | Gọi bất kỳ hệ thống nào ở trên là **"competitor"** trong báo cáo | **KHÔNG NÊN** | Theo `03_research/external_systems/README.md`: không mặc định gọi một hệ thống là competitor chỉ vì nó sinh slide. Với Open Design/PPTAgent, mô tả đúng là *related system / architecture contrast*. |
| U11 | Dùng **"test hapkin slide"** làm bất cứ điều gì | **UNVERIFIED ENTITY** | Đã search 3 lần (xem `evidence.md` #20), không xác định được. Không dùng làm evidence. |

### 4.2 Confidence level

| Kết luận | Confidence | Căn cứ |
| --- | --- | --- |
| Family A/B/C là bộ baseline cốt lõi; external là optional | **Cao** | 4 nguồn E1 độc lập về phương pháp (Ferrari Dacrema 2019, Musgrave 2020, Lipton & Steinhardt 2018, Atil 2024) + phân tích trực tiếp mission/FR |
| Phải chạy N ≥ 3 run và báo cáo variance | **Cao** | Atil et al. 2024 (E1) đo trực tiếp; không có nguồn nào phản bác |
| Naive baseline phải được tune công bằng (≥ 3 prompt variant) | **Cao** | Ferrari Dacrema 2019 + Musgrave 2020 + Sclar 2023 + Mizrahi 2024, tất cả E1 |
| Ablation phải đọc trên nhiều trục metric | **Cao** | Bảng ablation PPTAgent (E1) là bằng chứng số trực tiếp trong đúng domain |
| External comparison chỉ đạt mức CONTEXTUAL / CONDITIONALLY FAIR | **Trung bình–cao** | Protocol PresentBench (E1) + UniPPTBench (E2) chứng minh làm được; nhưng kết luận "không kiểm soát được model ⇒ không lặp lại được" là INFERENCE của research này |
| Open Design (`nexu-io/open-design`) là hệ thống có thật với các thuộc tính đã ghi | **Cao** | E1: official repo + official site + GitHub API metadata + `docs/skills-protocol.md` |
| `nexu-io/open-design` chính là "Open Design" mà NFR mục 0.1 nhắc tới | **Thấp–trung bình** | INFERENCE. NFR chỉ ghi *"gần Open Design"* trong bảng mô hình trả token, **không có URL/repo**; có ≥ 5 entity trùng/gần tên |
| Các con số cụ thể (điểm PresentBench, bảng ablation PPTAgent, credit Gamma) | **Trung bình** | Lấy qua fetch tự động; PresentBench khớp qua 2 lần fetch độc lập, nhưng **chưa đối chiếu tay với PDF gốc** |
| Danh sách biến cần freeze ở §2.1 | **Trung bình** | INFERENCE tổng hợp từ 4 nguồn E1; **không** trích nguyên văn checklist từ paper nào |
| EvoPresent / Auto-Slides không phù hợp làm baseline | **Thấp** | Chỉ verify sự tồn tại repo, chưa đọc paper/code |

**Điểm yếu lớn nhất của RQ08 này (nói thẳng):**
1. Con số external chưa được đối chiếu tay với PDF gốc.
2. Chưa thử chạy thật bất kỳ external system nào → mọi ước lượng chi phí/khả thi là suy luận từ docs, không phải từ trải nghiệm.
3. Family C dựa nhiều vào **một** ví dụ trong domain (PPTAgent); các nguồn methodology về ablation nói chung mà search trả về phần lớn là E3 nên đã bị loại, khiến argument về interaction effect phải neo vào Chen et al. 2024 — vốn nghiên cứu voting/filtering, không phải pipeline nhiều component khác chức năng (**INFERENCE mở rộng, cần nói rõ trong báo cáo**).

### 4.3 Unresolved questions

Ghi ở đây theo yêu cầu — RQ08 **không** được sửa `05_decisions/OPEN_QUESTIONS.md`.

1. **U-01 — Deck Agent có nên tự chạy PresentBench không?** Nếu chạy được đúng 238 instance + judge protocol của họ, đó là external comparison mạnh nhất và rẻ nhất (không cần chạy sản phẩm đối thủ, chỉ cần chạy Deck Agent). Chưa kiểm tra: cost chạy judge, license dataset, và liệu input của họ (paper hội nghị, giáo trình đại học, báo cáo tài chính) có khớp scope Deck Agent không.
2. **U-02 — "Open Design" trong NFR mục 0.1 có phải `nexu-io/open-design` không?** Cần team/giảng viên xác nhận. Có ≥ 5 entity trùng/gần tên đã verify tồn tại.
3. **U-03 — Gemini Notebook Enterprise API có expose sinh slide deck không?** Quyết định việc so với hệ thống đứng đầu cả hai benchmark có khả thi hay không.
4. **U-04 — N run bao nhiêu là đủ cho benchmark của đồ án?** Atil et al. 2024 cho thấy variance là thật nhưng không cho con số N cho quy mô này. Phụ thuộc cost/deck (NFR-90) và kích thước benchmark (RQ07).
5. **U-05 — Baseline có được dùng judge nào?** Nếu dùng LLM-as-judge để chấm cả Deck Agent lẫn baseline, có rủi ro self-preference bias (§8 architecture doc đã tự nhắc). Thuộc RQ06 nhưng ảnh hưởng trực tiếp tính hợp lệ của mọi so sánh ở RQ08.
6. **U-06 — `slide_type` khác biệt "có chủ đích" đo bằng gì?** RQ08 đề xuất self-comparison như một baseline, nhưng metric đo khác biệt cấu trúc thuộc RQ01/RQ03.
7. **U-07 — Ngân sách cho external comparison?** Gamma cần gói Pro trở lên + credit; chưa quy đổi được credit → USD từ doc đã đọc.
8. **Q-004 vẫn OPEN** — "test hapkin slide" chưa verify được sau 3 lần search.

### 4.4 Proposed decisions cần human/team review

Các đề xuất dưới đây **chưa được áp dụng ở đâu cả**. Chúng cần user xác nhận trước khi vào `06_design/BASELINE_SPEC.md` hoặc `DECISION_LOG.md`.

| # | Đề xuất | Ảnh hưởng nếu accept |
| --- | --- | --- |
| P-01 | Chốt bộ baseline tối thiểu cho thesis là **T1–T5** (historical curve, naive B-1, ablation A1, ablation A4, operational trade-off table); external là optional | `06_design/BASELINE_SPEC.md`; định hình phần "đánh giá kiến trúc" của báo cáo |
| P-02 | Chốt bộ baseline dev là **D1–D3**; naive/external **không** chạy hằng ngày | `06_design/EVALUATION_PIPELINE.md`; workstream Development Evaluation Protocol (D-006) |
| P-03 | Coi **run manifest** (§2.1) là artifact bắt buộc của mọi lần chạy benchmark, và **N ≥ 3 run** cho metric AI-quality | `06_design/EVALUATION_PIPELINE.md`; ảnh hưởng cost/deck (NFR-90) |
| P-04 | Chốt **checklist cân biến cho naive baseline** (§2.2) như điều kiện bắt buộc; thiếu dòng nào phải khai báo trong report | `06_design/BASELINE_SPEC.md` |
| P-05 | Ghi quy tắc "**không cộng dồn ablation, không kết luận từ một metric tổng**" thành quy tắc đọc kết quả, không chỉ là ghi chú | `06_design/EVALUATION_FRAMEWORK.md` §Evaluation Rigor Policy (D-003) |
| P-06 | Chốt **wording policy cho external comparison**: mọi câu so sánh phải có đủ 4 vế (ngày, cấu hình, benchmark version, judge); cấm câu khẳng định chung | Report template trong `06_design/EVALUATION_PIPELINE.md` |
| P-07 | Đưa **self-comparison theo `slide_type`** vào dev baseline set như một mục riêng, không gộp vào ablation | `06_design/BASELINE_SPEC.md`; phục vụ FR-02/FR-20 |
| P-08 | Ưu tiên điều tra **U-01 (tự chạy PresentBench)** trước khi bỏ công dựng bất kỳ external comparison thủ công nào | Thứ tự công việc Wave 2/3; có thể tiết kiệm đáng kể công sức |
| P-09 | Yêu cầu **đối chiếu tay với PDF gốc** mọi con số external trước khi đưa vào báo cáo | `04_evidence/EVIDENCE_POLICY.md` (đã có nguyên tắc; đây là nhắc áp dụng) |
| P-10 | Thiết kế Deck Agent để **bật/tắt component bằng config** (phục vụ ablation) thay vì phải fork code | Ảnh hưởng sang team implementation — cần trao đổi với người làm architecture, không phải quyết định của evaluation workspace |
