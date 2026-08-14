# RQ06 — Recommendation: evaluator strategy cho Deck Agent

**Ngày research:** 2026-08-14
**Đọc trước:** `landscape.md` (evaluator family + protocol + bias), `evidence.md` (nguồn + evidence level)
**Trạng thái:** RESEARCH OUTPUT — **không phải decision**. Mọi đề xuất ở đây là candidate chờ team/giảng viên review. Xem §4.

---

## A. Từ landscape về Deck Agent — 4 điều chỉnh khung tư duy

Trước khi map, cần nêu 4 kết luận từ landscape làm thay đổi cách đặt vấn đề của §8. Không có 4 điều này thì recommendation bên dưới trông tùy tiện.

### A1. "Đo được deterministic hay không" không phải câu hỏi nhị phân theo dimension — mà theo **thành phần con** của dimension

**INFERENCE.** §8 xếp 4 dimension vào một rổ "cần MLLM-judge". Nhưng đối chiếu SlidesBench (`evidence.md` §3.13) với FR/NFR nội bộ cho thấy các dimension **lai**:

| Dimension §8 | Phần đo được bằng rule (F1) | Phần thực sự cần judge |
| --- | --- | --- |
| Ready-to-use | Text overflow, element overlap, out-of-bounds, margin, đếm slide vs constraint (FR-22, FR-23) | "Deck này có dùng được ngay không" ở mức tổng thể, edit effort |
| Design consistency | `% element dùng đúng token` (NFR-52), color distance (CIEDE2000), font/size conformance, diff token trước/sau regenerate (FR-10–13) | Coherence thị giác tổng thể, "áp token đúng nhưng trông vẫn xấu" |
| Content fidelity | `source_ref` có tồn tại / trỏ đúng block không (FR-03) | Nội dung có **đúng** nguồn không, có bịa không, có bỏ sót ý chính không |
| Structure | Số slide, phân bố độ dài, tỷ lệ text/ảnh theo `slide_type` (FR-20) | Cách chia slide có **hợp lý** không, `slide_type` có tạo khác biệt **có chủ đích** không (FR-02) |

**Hệ quả:** câu hỏi đúng không phải "dimension nào cần judge" mà **"trong dimension này, phần nào rơi xuống được F1?"** — vì mọi thứ rơi xuống F1 đều rẻ hơn, ổn định hơn, và chạy được mỗi commit.

### A2. Trục quyết định checklist vs rubric là **material-dependence**, không phải "chi tiết vs tổng quát"

**EXTERNAL EVIDENCE + INFERENCE.** PresentBench tự chia 5 dimension thành material-independent (Presentation Fundamentals, Visual Design & Layout) và material-dependent (Content Completeness, Correctness, Fidelity). Lý do là **logic, không phải kinh nghiệm**: một rubric chung **không thể** diễn đạt "deck có giữ đúng con số 12,4% ở trang 7 của *tài liệu nguồn này*" — yêu cầu đó chỉ tồn tại khi biết input cụ thể.

Ánh xạ sang Deck Agent:
- **Material-dependent** (fidelity, coverage, không bịa — FR-03, FR-18b, NFR-53): **buộc phải** per-instance.
- **Material-independent** (design consistency, readability, overflow — FR-22, NFR-52): rubric chung đủ, **và phần lớn còn đẩy xuống được F1**.

Đây là câu trả lời có evidence cho câu hỏi task brief *"Bộ case / checklist theo từng instance hay rubric chung? Ưu nhược?"*: **không chọn một — phân theo bản chất dimension.**

### A3. Vùng hoạt động thực tế của judge là ρ ≈ 0.5, và **trần người cũng thấp**

**EXTERNAL EVIDENCE.** Ba con số cần đặt cạnh nhau:

```
PresentBench (checklist per-instance)   ρ ≈ 0.532
PPTEval      (rubric chung 1–5)         ρ ≈ 0.303
Human ↔ Human upper bound               ρ ≈ 0.664      ← trần
G-Eval (summarization, được coi là mạnh) ρ ≈ 0.514
PPTEval: agreement giữa 4 human rater   Fleiss κ ≈ 0.59  ← dưới ngưỡng "tentative" của Krippendorff
```

**INFERENCE — hai hệ quả trực tiếp cho đồ án:**
1. **Đừng đặt mục tiêu judge đạt α ≥ 0.8 với human.** Đó là mục tiêu không khả thi khi trần người–người trong chính domain này chỉ ~0.6. Chỉ tiêu đúng là **so judge với trần người trên cùng dữ liệu** (PresentBench làm đúng: 0.532/0.664 ≈ 80% trần).
2. **Judge score là ranking signal, không phải ground truth.** Cách viết đúng trong báo cáo là "judge xếp hạng phù hợp với người ở mức X% của trần người–người", không phải "fidelity = 86.7".

### A4. Judge có noise floor, và nếu không đo thì mọi so sánh version đều vô nghĩa

**EXTERNAL EVIDENCE** (`evidence.md` §3.6, preprint — claim định tính đáng tin, con số thì không): temperature=0 **không** đảm bảo determinism; item gần ranh giới quyết định lật pass/fail giữa các lần chạy giống hệt nhau.

**INFERENCE — đây là rủi ro số một cho mission "chứng minh cải thiện mỗi ngày".** Nếu noise floor của judge là ±X mà team không biết X, mọi delta < X sẽ bị đọc thành tiến bộ. §8 và NFR-52/53 **không nhắc gì** tới điều này.

---

## B. Bảng map: loại task → evaluator family

**RECOMMENDATION.** Trace: `problem (§A1–A4 + FR/NFR) → evidence (landscape.md) → reasoning → recommendation`. Cột "Dev" = dùng được cho development tracking (rẻ/nhanh/lặp lại được). Cột "Final" = dùng được làm evidence cho báo cáo/bảo vệ.

| # | Loại task / claim cần chứng minh | Ưu tiên evaluator | Dev | Final | Lý do (evidence) |
| --- | --- | :-: | :-: | :-: | --- |
| T1 | Deck IR hợp lệ; PPTX mở được; token propagate atomic; lock không bị đụng; slide khác không đổi khi regen (FR-01/05/10–13/15–17, NFR-50/80–81) | **F1 deterministic** | ✓ | ✓ (phụ lục) | Có định nghĩa hình thức. Không có lý do gì đưa vào judge |
| T2 | Không tràn chữ / overlap / out-of-bounds; số slide đúng constraint (FR-22, FR-23) | **F1 deterministic** | ✓ | ✓ | SlidesBench chứng minh Layout đo được bằng rule (align/overlap/margin/in-bounds). Rule **chính xác hơn** judge ở đây, không chỉ rẻ hơn |
| T3 | Design consistency: element dùng đúng design token (NFR-52) | **F1 deterministic** (`% element đúng token`) + CIEDE2000 color distance | ✓ | ✓ | NFR-52 đã có candidate F1; SlidesBench hậu thuẫn gián tiếp. **Candidate MLLM-checklist của NFR-52 KHÔNG được ưu tiên ở đây** — design consistency là material-independent nên checklist per-instance là overkill |
| T4 | Design *perceptual*: áp token đúng nhưng trông có coherent không | **F4 MLLM + rubric chung** (material-independent) | △ (nightly) | ✓ | Rubric chung đủ vì không phụ thuộc input. SlidesBench đo ICC 73.8–85.3% ở nhóm này — cao hơn hẳn dimension nội dung |
| T5 | **Content fidelity: không bịa, bám nguồn** (FR-03, NFR-53) — daily | **F2b/F2c** (NLI/alignment: SummaC, AlignScore; atomic-fact: FActScore) | ✓ | △ | **Family §8 bỏ sót hoàn toàn.** Chạy local → deterministic thật, rẻ, lặp lại được. Đây là thứ duy nhất chạm được fidelity ở tần suất daily |
| T6 | **Content fidelity + coverage** — final/thesis | **F6 hybrid: F4 × checklist per-instance + human validation mẫu con** | ✗ | ✓ | Material-dependent → per-instance là bắt buộc về logic (§A2). Là điểm khớp nhất giữa NFR-53 và landscape |
| T7 | Structure / planning quality: chia slide hợp lý, `slide_type` tạo khác biệt **có chủ đích** (FR-02, FR-20) | **F1 cho phần đo được** (phân bố slide/text/ảnh theo `slide_type`) + **F4 pairwise** cho phần phán đoán | △ | ✓ | FR-02 nói "khác biệt **có chủ đích**" — phần "có khác biệt" là F1; phần "có chủ đích" cần judge. JudgeSense cảnh báo coherence/structure là dimension **bất ổn nhất** → cần pairwise + human hơn các dimension khác |
| T8 | Editor instruction-following: lệnh tự nhiên được hiểu **đúng ý** (FR-14b) | **F4 checklist per-instance** (mỗi lệnh sinh 3–5 item YES/NO) | △ | ✓ | Mỗi lệnh là một instance với yêu cầu riêng → material-dependent. TICK là mẫu trực tiếp (checklist sinh từ instruction) |
| T9 | Ready-to-use tổng thể / edit effort | **F5 human** (không có lựa chọn khác đáng tin) | ✗ | ✓ | Không có định nghĩa hình thức, không có nguồn nào validate được judge trên dimension này |
| T10 | So sánh với baseline/competitor (single-shot LLM, ablation không ContentPlanner, hệ thống ngoài) | **F4 pairwise + BPC** + **F5 human trên mẫu con** | ✗ | ✓ | Đây là nơi self-preference bias nguy hiểm nhất (các nhánh dùng model khác nhau — §C1). Pairwise là protocol MLLM judge mạnh nhất (Chen ICML 2024) |
| T11 | Latency / cost / reliability (NFR-01–03, 20–22, 90–92) | **Telemetry** (không thuộc RQ06) | ✓ | ✓ | Đã được D-005 đưa thành Operational layer |

**Đọc bảng theo chiều dev vs final:**

```
DEV (mỗi commit / PR / nightly)          FINAL (milestone / thesis)
──────────────────────────────           ──────────────────────────────
T1  F1  ✓ mỗi commit                     Tất cả dev metric, cộng thêm:
T2  F1  ✓ mỗi commit                     T6  F4 checklist per-instance + human
T3  F1  ✓ mỗi commit                     T7  F4 pairwise (structure)
T5  F2  ✓ mỗi PR (local model, rẻ)       T9  F5 human (ready-to-use)
T4  F4  △ nightly (tốn tiền)             T10 F4 pairwise + human (baseline)
T7  F1-part ✓ / F4-part △ nightly
T8  F4  △ nightly
```

**Evaluator nào phù hợp cả hai, cái nào chỉ một:**

| Family | Dev | Final | Ghi chú |
| --- | :-: | :-: | --- |
| F1 deterministic | ✓✓ | ✓ | Phù hợp **cả hai**. Ở final chỉ ở mức supporting/phụ lục, không phải bằng chứng trọng tâm |
| F2 NLI/atomic-fact | ✓✓ | △ | Phù hợp dev rất tốt. Ở final chỉ dùng được **nếu** đã calibrate với human — chưa có nguồn nào validate F2 trên slide deck |
| F3 LLM-judge text | △ | △ | Ít giá trị riêng cho Deck Agent: cái cần đo hoặc là text↔source (F2 rẻ hơn) hoặc cần nhìn (F4) |
| F4 MLLM-judge | △ | ✓✓ | **Không phù hợp daily** (đắt, chậm, có noise floor). Là công cụ chính cho final |
| F5 human | ✗ | ✓✓ | **Chỉ final.** Không lặp lại được, quá chậm cho tracking |
| F6 hybrid | — | ✓✓ | Là kiến trúc, không phải evaluator. Bắt buộc cho mọi claim trọng tâm |

---

## C. Bias mitigation — cụ thể cho Deck Agent

### C1. Self-preference: §8 đúng hướng nhưng chưa đủ

**PROJECT SOURCE** §8: *"nên dùng model khác họ để generate và để judge"*.

**EXTERNAL EVIDENCE:** Panickssery et al. (NeurIPS 2024) cho thấy self-preference gắn với **năng lực tự-nhận-diện output của chính mình** (tương quan tuyến tính khi fine-tune). → Tách generator/judge sang họ khác **tấn công đúng cơ chế**. §8 đúng.

**Nhưng chưa đủ, vì 3 lý do có evidence:**
1. Không chạm tới **style / verbosity / position bias** — những bias này không liên quan tự-nhận-diện.
2. Yang et al. 2026: năng lực model **không tương quan (có khi âm)** với SPB thấp → "chọn model mạnh nhất làm judge" không phải mitigation. Mitigation của họ là **phân rã evaluation thành nhiều chiều/item** (−31.5% SPB) — tức **cách hỏi** quan trọng ngang **model nào hỏi**, và trùng đúng cơ chế checklist.
3. Panel đa họ (PoLL) từng được coi là giải pháp, nhưng bị một preprint 2026 phản bác (correlated errors) → **chưa dùng được** (§3).

**INFERENCE — mức nguy hiểm khác nhau theo use case, đây là điểm §8 không phân biệt:**

| Use case | Nguy hiểm | Vì sao |
| --- | --- | --- |
| Dev tracking (v0.2 vs v0.3, **cùng generator model**) | **Thấp** | SPB là offset gần hằng số trên cả hai nhánh → phần lớn triệt tiêu khi lấy hiệu. ⚠ **Nhưng không triệt tiêu nếu team đổi model generator giữa 2 version** — lúc đó SPB thành confounder trực tiếp |
| Comparative eval (vs single-shot LLM, vs competitor) | **Cao** | Các nhánh dùng model khác nhau → judge nghiêng về nhánh cùng họ. Đây là chỗ claim của đồ án dễ bị phản biện nhất khi bảo vệ |
| Con số tuyệt đối trong báo cáo | **Cao** | Vô nghĩa nếu không nêu judge + giới hạn (đúng như §8 tự cảnh báo) |

**Mitigation đề xuất, xếp theo sức mạnh evidence:** (1) judge khác họ generator; (2) phân rã thành checklist/dimension nhị phân; (3) human validation trên mẫu con — **không giảm bias nhưng đo được nó còn lại bao nhiêu, và là mitigation duy nhất defensible khi bảo vệ**; (4) panel đa họ — **hoãn** cho tới khi mâu thuẫn evidence được giải quyết.

### C2. Position bias — phần lớn né được bằng lựa chọn protocol

Chỉ phát sinh ở pairwise/ranking. Với **pointwise binary checklist thì về cơ bản không tồn tại** — một lợi thế của checklist mà §8 không nêu. Ở những chỗ **buộc** dùng pairwise (T7, T10): áp **BPC** (chạy cả 2 thứ tự, cân bằng) và **MEC** (bắt judge sinh evidence trước rồi mới chấm) theo Wang et al. (ACL 2024). Cả hai đều rẻ.

### C3. Verbosity/length bias — failure mode cụ thể của domain này

**INFERENCE:** deck nhiều chữ trông "đầy đủ" hơn với judge, trong khi FR-22 và mục tiêu "dùng được ngay" muốn deck **súc tích**. Nếu judge có length bias dương, nó **thưởng cho đúng cái sản phẩm coi là lỗi**. Soumik 2026 cho thấy bias này **không đồng nhất giữa các model** (từ +0.44 đến −0.12) → **không suy đoán được, phải đo trên chính judge đang dùng**: sinh cặp deck cùng nội dung khác độ dài chữ, xem judge có lệch không. Rẻ, làm được trong 1 buổi.

### C4. Prompt/rubric sensitivity — hệ quả vận hành

**EXTERNAL EVIDENCE** (JudgeSense 2026): verdict đổi dưới paraphrase tương đương; **model lớn hơn không ổn định hơn**. → **Judge prompt / checklist phải được version hóa và đóng băng như một phần của frozen benchmark.** Sửa một chữ trong rubric giữa v0.5 và v0.6 làm mọi so sánh trước đó vô hiệu. Đây là thứ dễ bỏ sót nhất trong đồ án. Ưu tiên **binary YES/NO hơn thang Likert** vì Likert cho phép trôi dưới ngưỡng.

### C5. Noise floor — bước bắt buộc trước khi dùng bất kỳ judge nào

**RECOMMENDATION:** trước khi dùng judge để so version, chạy **cùng một deck N lần** (N ≥ 5) qua đúng pipeline judge đó, tính spread. Con số này là **ngưỡng tối thiểu để một delta được coi là tín hiệu**. Báo cáo grader disagreement song song với điểm. Chi phí: một buổi. Giá trị: phân biệt được improvement với tiếng ồn — tức là nền của toàn bộ mission.

---

## D. Cỡ mẫu human — trả lời câu hỏi task brief, gắn NFR-53

**PROJECT SOURCE** — task brief: *"Cần bao nhiêu mẫu human để tin tương quan judge-người (dù ước lượng)?"*; NFR-53: *"Checklist theo instance + human corr. trên mẫu con"* (không nêu cỡ mẫu).

**EXTERNAL EVIDENCE** — alt-test (Calderon, Reichart & Dror 2025): **≥3 annotator**; **50–100 instance** (ε=0.2, expert) hoặc **100–150** (ε=0.1, crowd-worker); **tối thiểu 30** để thỏa normality của t-test; pass khi **ω ≥ 0.5**; FDR q=0.05; **IAA thấp → ε nhỏ hơn → cần nhiều mẫu hơn**.

**RECOMMENDATION cho Deck Agent (candidate, cần team review):**

| Tham số | Đề xuất | Lý do |
| --- | --- | --- |
| Số annotator | **3** (3 thành viên team, chấm độc lập, mù version) | Tối thiểu của alt-test; dưới 3 thì không có tín hiệu consensus |
| Số instance | **50–100 deck**, nghiêng về **100** | Người chấm là sinh viên trong team, không phải expert → ε nhỏ → cần nhiều hơn. PresentBench chỉ dùng ~24 (**dưới ngưỡng 30**, là điểm yếu của họ) |
| Đơn vị chấm | Ranking/pairwise ở mức deck, hoặc 3–5 dimension thang thô — **KHÔNG** bắt người chấm 54 checklist item | Giữ workload khả thi; item nhị phân để **model** chấm (mô hình 2 lớp của HealthBench: người viết tiêu chí, model chấm tiêu chí) |
| Báo cáo | ρ(judge, human) **cùng với** trần ρ(human, human) trên **cùng** dữ liệu, cộng phân bố bất đồng của người | Elangovan et al.: correlation đơn lẻ gây hiểu nhầm khi human uncertainty cao — mà domain này đúng là như vậy (κ≈0.59) |
| Ngưỡng "đủ tin" | Judge đạt **≥ ~80% của trần người–người** | Neo theo PresentBench (0.532/0.664). **Không** dùng ngưỡng tuyệt đối α≥0.8 (§A3) |

**INFERENCE về khối lượng thực tế:** 100 deck × 3 người, mỗi deck ~3–5 phút → ~5 giờ/người. Khả thi **một lần** ở milestone, **không** khả thi lặp lại → khẳng định lại: human chỉ dùng cho final, không cho dev.

---

## E. Những claim BẮT BUỘC phải có human validation

**RECOMMENDATION.** Không phải claim nào cũng cần. Danh sách tối thiểu:

1. **Bất kỳ claim nào dựa trên điểm judge được đưa vào báo cáo/bảo vệ.** Không có human sample thì con số judge không defensible — đây là điều §8 tự nhận và landscape xác nhận (mọi nguồn nghiêm túc trong domain đều có lớp human validation).
2. **"Deck Agent tốt hơn baseline/competitor ở dimension X"** (T10) — nơi self-preference bias nguy hiểm nhất.
3. **"ContentPlanner đóng góp giá trị"** (ablation) — claim kiến trúc trung tâm của đồ án.
4. **"Ready-to-use / dùng được ngay"** (T9) — không có evaluator tự động nào được validate cho dimension này.
5. **"`slide_type` tạo khác biệt có chủ đích"** (FR-02, T7) — phần "có chủ đích" là phán đoán người; JudgeSense xếp structure/coherence vào nhóm judge bất ổn nhất.

**KHÔNG cần human validation:** T1, T2, T3 (deterministic — chúng *là* ground truth trong phạm vi định nghĩa của mình), và mọi metric operational.

---

# 1. Recommendation

**RECOMMENDATION — đề xuất, chưa phải quyết định.**

**Nguyên tắc bao trùm:** không chọn *một* evaluator cho Deck Agent. Chọn một **kiến trúc xếp tầng (F6 hybrid)**, trong đó mỗi tầng chỉ nhận phần mà tầng dưới không giải quyết được, và tầng trên dùng để **calibrate** tầng dưới:

```
F1 deterministic  →  F2 semantic/NLI  →  F4 MLLM-judge  →  F5 human
   (mỗi commit)       (mỗi PR)           (nightly/milestone)  (milestone)
   ────────────────────────────────────────────────────────────────────►
   rẻ, ổn định, hẹp                              đắt, nhiễu, rộng
   ◄────────────────────────────────────────────────────────────────────
                     calibrate ngược
```

Sự hội tụ về kiến trúc này là bằng chứng mạnh nhất của RQ06: PresentBench, PPTAgent, SlidesBench, HealthBench và NFR-53 — 5 nguồn độc lập — đều dùng hình thái *evaluator tự động + lớp human validation*, **không nguồn nào dùng judge tự động một mình**.

### 1.1 Theo loại task

| Loại task | Ưu tiên evaluator family |
| --- | --- |
| Correctness có định nghĩa hình thức (schema, export, lock, overflow, token conformance) | **F1 deterministic**, không dùng judge |
| Content fidelity — **daily** | **F2b/F2c** (NLI/alignment + atomic-fact decomposition), chạy local |
| Content fidelity + coverage — **final** | **F6: F4 × checklist per-instance + human validation** |
| Design consistency — **cấu trúc** | **F1** (`% element đúng token` + color distance) |
| Design — **perceptual** | **F4 × rubric chung** (material-independent → không cần per-instance) |
| Structure / planning quality | **F1** cho phần đo được + **F4 pairwise (BPC/MEC)** cho phần phán đoán + human |
| Editor instruction-following (FR-14b) | **F4 × checklist per-instance sinh từ lệnh** (kiểu TICK) |
| Ready-to-use tổng thể / edit effort | **F5 human** — không có lựa chọn thay thế đáng tin |
| So với baseline / competitor / ablation | **F4 pairwise + BPC** + **F5 human bắt buộc** |

### 1.2 Tách dev vs final

**Development evaluation** — tiêu chí: rẻ, nhanh, **lặp lại được**, đủ nhạy bắt regression.
- **Xương sống là F1 + F2**, không phải judge. Đây là kết luận khác §8 rõ nhất.
- F4 chỉ chạy ở nhịp **nightly/weekly** trên tập nhỏ, không phải mỗi commit (khớp NFR-51: *"không gọi LLM mỗi commit"*).
- **Human = 0** trong vòng lặp dev.
- **Điều kiện tiên quyết trước khi dùng bất kỳ số judge nào để so version:** đo noise floor (§C5). Không có bước này thì dev tracking không có nền.

**Final / thesis evaluation** — tiêu chí: rigorous, defensible, có human validation.
- **F4 × checklist per-instance** cho dimension material-dependent; **F4 × rubric chung** cho material-independent.
- **F5 human**: 3 annotator × 50–100 deck, mù version, báo cáo ρ(judge,human) **kèm** trần ρ(human,human).
- Mọi claim ở §E phải có human backing.
- Báo cáo phải nêu giới hạn phương pháp (§8 tự yêu cầu điều này — đúng, và landscape cho phép nêu cụ thể chứ không chung chung).

### 1.3 Về checklist vs rubric (câu hỏi task brief)

**Phân theo material-dependence, không chọn một:**
- Material-**dependent** (fidelity, coverage, correctness, instruction-following) → **checklist per-instance**. Rubric chung về mặt logic không diễn đạt được yêu cầu phụ thuộc nguồn.
- Material-**independent** (design, layout, readability) → **rubric chung**, và phần lớn còn đẩy xuống F1.
- **Đường trung gian cho team 4 người:** checklist per-instance **sinh tự động bằng LLM** từ source + constraint (kiểu TICK, +5.8pp agreement), có người rà soát trên tập nhỏ. Không đạt chất lượng expert-authored của PresentBench, nhưng khả thi. Chi phí authoring theo cách PresentBench (54.1 item × N instance, expert) là **ngoài tầm** đồ án.

### 1.4 KHÔNG chọn judge model/vendor

**Evidence chưa đủ để chọn.** Ba nguồn độc lập cùng chỉ một hướng: **model mạnh nhất/đắt nhất không tự động là judge tốt nhất** (Yang et al. 2026: năng lực không tương quan với SPB thấp; JudgeSense 2026: model lớn/mới không ổn định hơn; Soumik 2026: model tier trung + debiasing vượt frontier ở 1/15 chi phí).

Thay vì chọn model, đề xuất **tiêu chí chọn** + **thủ tục kiểm tra** để team tự chạy khi đến lúc:
1. **Khác họ với generator model** (mitigation SPB có E1 hậu thuẫn).
2. **Đo noise floor** trên chính judge candidate (§C5) — loại thẳng candidate có spread lớn.
3. **Đo length/style bias** bằng cặp đối chứng (§C3) — vì bias này không đồng nhất giữa model.
4. **Đo ρ với human** trên mẫu 50–100 (§D), so với trần người–người.
5. Chọn theo **agreement/chi phí**, không theo tier model.

Ghi nhận như **fact**, không phải khuyến nghị: PresentBench dùng model họ Gemini (flash-preview tier), PPTEval dùng GPT-4o, HealthBench dùng GPT-4.1, SlidesBench dùng GPT-4o.

---

# 2. Confidence level

| Kết luận | Confidence | Cơ sở / điều làm nó lung lay |
| --- | --- | --- |
| PresentBench tồn tại, §8 mô tả đúng cơ chế | **Cao** | E1: paper + repo Apache-2.0 + dataset + project page; search disambiguation không tìm thấy entity trùng tên |
| Kiến trúc hybrid xếp tầng (không dùng judge đơn độc) | **Cao** | 5 nguồn độc lập hội tụ; không tìm thấy phản chứng |
| Đẩy tối đa xuống F1 trước khi dùng judge | **Cao** | SlidesBench + FR/NFR nội bộ + audit RQ00 mục 2 đều cùng hướng; là lập luận cấu trúc, ít phụ thuộc một nguồn |
| Trục checklist/rubric = material-dependence | **Trung bình–cao** | Logic vững + 4 nguồn E1 cùng hướng; nhưng phân loại material-dependent/independent là của **PresentBench**, chưa có nguồn thứ hai xác nhận cách chia này |
| Danh mục bias + mitigation position/self-preference | **Cao** | E1 peer-reviewed (Zheng NeurIPS'23, Wang ACL'24, Panickssery NeurIPS'24, Chen ICML'24) |
| ρ ≈ 0.5 là vùng hoạt động thực tế; trần người thấp | **Trung bình–cao** | 4 con số độc lập nhất quán; nhưng đều là setup khác nhau, không phải đo lại trên Deck Agent |
| Pairwise > pointwise về reliability cho MLLM judge | **Trung bình** | Kết luận định tính nhất quán (Chen ICML'24, Liu COLM'24); ⚠ con số cụ thể của Chen trích xuất **không nhất quán**, chưa đọc bản gốc |
| Cỡ mẫu human 3 annotator × 50–100 instance | **Trung bình** | Chỉ **một** nguồn E1 cho con số (alt-test); là thủ tục cho annotation task, áp vào Deck Agent phải thích nghi |
| F2 (NLI/atomic-fact) là xương sống fidelity cho daily | **Trung bình–thấp** | Các metric là E1 mạnh, **nhưng không nguồn nào validate chúng trên slide deck**. Đây là INFERENCE về khả năng chuyển giao, chưa có evidence trực tiếp. **Cần một thí nghiệm nhỏ để xác nhận trước khi cam kết** |
| Phải đo noise floor trước khi so version | **Trung bình** | Chỉ 1 preprint 1 tác giả, mẫu nhỏ; nhưng claim định tính phù hợp hiểu biết kỹ thuật đã biết, và **chi phí làm theo rất thấp so với rủi ro bỏ qua** |
| Ngưỡng agreement "đủ tin" | **Thấp** | Ngưỡng Krippendorff ở E2 (chưa đọc bản gốc) và **mâu thuẫn với thực tế domain** (κ≈0.59) |
| Style bias là bias trội | **Thấp** | 1 preprint 1 tác giả. Ghi nhận là **rủi ro mở**, không dùng làm nền recommendation |
| Panel/jury judge | **Không kết luận** | Evidence mâu thuẫn trực tiếp (§3) |

---

# 3. Unresolved questions

**U-1. Panel (jury) judge có đáng không?** PoLL (2024, Cohere, nhiều tác giả, 6 dataset) nói panel model nhỏ đa họ vượt judge lớn đơn lẻ, ít intra-model bias, rẻ hơn 7×. "Nine Judges, Two Effective Votes" (2026, preprint 1 tác giả) nói ngược: 9 judge ≈ 2 vote độc lập, judge đơn tốt nhất bằng hoặc hơn panel. Khác biệt có thể do task (NLI có ground truth vs open-ended) — **chưa giải quyết được từ evidence hiện có.** → Không đưa panel vào recommendation.

**U-2. F2 (NLI/alignment/atomic-fact) có chuyển giao được sang slide deck không?** Không tìm thấy **bất kỳ** nguồn nào áp dụng AlignScore/SummaC/FActScore cho presentation generation. Deck khác summary ở chỗ: nội dung bị phân mảnh qua nhiều slide, có bullet cụt không thành câu, có ảnh/bảng. Cần thí nghiệm nhỏ (20–30 deck, đối chiếu với human) trước khi cam kết F2 làm xương sống daily fidelity.

**U-3. Noise floor thật của judge trên deck là bao nhiêu?** Không nguồn nào báo cáo cho domain này. Phải tự đo. Nếu noise floor lớn hơn delta điển hình giữa 2 version thì **toàn bộ chiến lược dev tracking bằng judge phải xét lại**, không chỉ điều chỉnh.

**U-4. Checklist LLM-sinh có đủ tốt không?** TICK cho +5.8pp; PresentBench (expert-authored) hơn PPTEval ~23 điểm ρ. Khoảng cách giữa hai mức này **chưa có nguồn nào đo trực tiếp**. Team cần biết mất bao nhiêu chất lượng khi chọn đường rẻ.

**U-5. Ngưỡng "đủ tin" nên là bao nhiêu cho một đồ án?** Ngưỡng Krippendorff (E2, chưa verify bản gốc) mâu thuẫn với thực tế domain (κ≈0.59 giữa người). Đề xuất neo theo **tỷ lệ với trần người–người** thay vì hằng số tuyệt đối — nhưng "≥80% của trần" là con số **suy ra từ PresentBench**, không phải chuẩn đã có.

**U-6. Style bias (markdown/format) ảnh hưởng judging deck thế nào?** Chỉ 1 preprint hậu thuẫn, nhưng nếu đúng thì nó là bias trội và **chưa có mitigation nào được validate**. Với deck (artifact có format mạnh), rủi ro này có thể lớn hơn ở text thuần.

**U-7. Metric nào đo được "khác biệt có chủ đích" của `slide_type` (FR-02/FR-20)?** RQ06 xác định được **evaluator family** (F1 cho phần đo được + F4 pairwise cho phần phán đoán) nhưng **không** xác định được cách operationalize "có chủ đích". Thuộc RQ01/RQ03, không thuộc RQ06.

**U-8. Con số cụ thể trong `evidence.md` cần verify lại từ PDF gốc** trước khi trích vào báo cáo — đặc biệt: % agreement của MLLM-as-a-Judge (trích xuất không nhất quán), ngưỡng Krippendorff, danh sách tác giả TICK và Panickssery, con số 4.7M của AlignScore.

---

# 4. Proposed decisions cần human/team review

> Không ghi vào `05_decisions/DECISION_LOG.md` hay `05_decisions/OPEN_QUESTIONS.md` (3 session đang chạy song song). Các mục dưới đây là **đề xuất chờ review**, viết ở đúng format để người review chuyển sang sau nếu chấp nhận.

**P-1. Đóng Q-005 — "PresentBench" đã VERIFIED.** Không còn là UNVERIFIED ENTITY. Cần route evidence sang RQ01 (5 dimension), RQ02 (tách Completeness/Correctness/Fidelity), RQ04 (Visual Design & Layout), RQ07 (238 instance / 5 category / background material), RQ08 (leaderboard 9 hệ thống). Cần một người quyết định ai làm việc route này — RQ06 không tự làm.

**P-2. Sửa lại phạm vi của §8: 4 dimension KHÔNG cùng thuộc về MLLM-judge.** §8 xếp cả 4 vào một rổ; landscape cho thấy Ready-to-use (phần overflow) và Design consistency (phần token) chủ yếu là F1. Đây là **challenge trực tiếp vào §8**, cần team/giảng viên xác nhận trước khi ảnh hưởng `06_design/`.

**P-3. Bổ sung F2 (NLI/alignment/atomic-fact metric) như một evaluator family chính thức.** Family này vắng mặt hoàn toàn trong §8 và NFR. Cần quyết định: có chạy thí nghiệm U-2 để xác nhận khả năng chuyển giao không, và ai làm.

**P-4. NFR-52: chọn giữa 2 candidate.** Landscape nghiêng về `% element dùng đúng token` (F1) cho phần cấu trúc, và **rubric chung** (không phải checklist per-instance) cho phần perceptual — vì design consistency là material-independent. Đây là **candidate, không phải preferred approach đã chốt**; cần team review.

**P-5. NFR-53: chốt cỡ mẫu human.** Đề xuất **3 annotator × 50–100 deck** (nghiêng 100 vì annotator không phải expert), báo cáo ρ(judge,human) kèm trần ρ(human,human). NFR-53 hiện để trống cỡ mẫu. Cần team xác nhận khả năng bố trí ~5 giờ/người ở milestone.

**P-6. Thêm 3 yêu cầu vận hành chưa có trong bất kỳ NFR nào:**
- (a) **Judge prompt / checklist phải được version hóa và đóng băng** cùng frozen benchmark (JudgeSense — thiếu cái này thì điểm giữa các version không so được).
- (b) **Đo noise floor của judge** (chạy lặp N≥5 trên cùng deck) trước khi dùng judge để so version.
- (c) **Báo cáo grader disagreement song song với điểm**, không chỉ báo điểm.
→ Nếu chấp nhận, thuộc `06_design/EVALUATION_PIPELINE.md`, không phải RQ mới.

**P-7. Quyết định về panel judge: HOÃN.** Evidence mâu thuẫn (U-1). Đề xuất không đưa vào design cho tới khi có evidence mới. Ghi nhận để không ai vô tình đưa vào.

**P-8. Chuẩn viết số trong báo cáo.** Đề xuất **không** báo cáo điểm judge như con số tuyệt đối ("fidelity = 86.7") mà báo cáo dưới dạng: điểm + judge model/version + noise floor + ρ với human + trần người–người. Đây là hệ quả trực tiếp của §A3 và của chính cảnh báo trong §8 (*"không chỉ báo cáo con số mà không nhắc giới hạn"*). Cần team đồng ý vì nó ảnh hưởng format báo cáo.
