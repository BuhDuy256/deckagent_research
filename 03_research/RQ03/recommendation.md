# RQ03 — Recommendation: đo "khác biệt có chủ đích" của `slide_type`

**Ngày research:** 2026-08-15
**Đọc trước:** `landscape.md` (bảng thuộc tính §4, thiết kế thí nghiệm §5), `evidence.md` (nguồn + evidence level + mức verify)
**Trạng thái:** RESEARCH OUTPUT — **không phải decision**. Mọi đề xuất là candidate chờ team/giảng viên review. Xem mục 4.

---

## A. Ba điều chỉnh khung tư duy trước khi vào đề xuất

Không có ba điều này thì đề xuất bên dưới trông tùy tiện.

### A1. Câu hỏi của FR-02 không nằm cùng loại với các dimension khác

**INFERENCE.** RQ02/RQ04/RQ05 đo **thuộc tính của một deck**. RQ03 đo **quan hệ giữa nhiều deck sinh từ cùng một source**. Hệ quả vận hành, không phải chuyện học thuật:

- Không tồn tại một con số "slide_type score" cho **một** deck. Bất kỳ ai đề xuất một con số như vậy đang đo thứ khác.
- Benchmark phải chạy **mỗi source qua cả 3 mode** (điều RQ07 cần biết — `WAVE1_SYNTHESIS.md` đã dự đoán đúng điểm này).
- Đơn vị của regression tracking là **một bộ ba deck**, không phải một deck.

### A2. Cảnh báo "judge yếu ở structure" là đúng — nhưng nó đánh vào một câu hỏi mà RQ03 nên tránh hỏi

**EXTERNAL EVIDENCE (routed từ Wave 1):** PPTEval Coherence human correlation Pearson **0.55** (Design 0.90); PresentBench ρ **0.532** vs trần người–người **0.664**; JudgeSense xếp coherence là dimension **bất ổn nhất** dưới paraphrase.

**INFERENCE — đối diện trực tiếp:** ba con số này đo độ tin cậy của judge khi **chấm điểm chất lượng cấu trúc** ("cách chia nội dung có hợp lý không", §8 nguyên văn). Ở đúng câu hỏi đó, **kết luận là: judge không đủ tin để làm evidence, và RQ03 không khuyến nghị dùng nó làm metric theo dõi.** Không lảng tránh, không tìm cách cứu.

Nhưng FR-02/FR-20/DoD **không hỏi câu đó**. Chúng hỏi *"đổi `slide_type` có tạo cấu trúc khác có chủ đích không"* và *"khác biệt có đo được không"*. Câu này phân rã thành ba câu con, và **không câu nào trong ba yêu cầu chấm điểm chất lượng cấu trúc**:

```
(1) có khác biệt vượt nhiễu không?      → F1 + thống kê. KHÔNG có model trong vòng lặp.
(2) khác biệt có đúng hướng đã khai báo? → F1 + một bảng spec do người viết trước.
(3) khác biệt có nhận ra được không?     → phân loại forced-choice 3 lựa chọn (F1-classifier
                                            trên feature, và/hoặc F3/F4 judge).
```

**Đây là kết luận trung tâm của RQ03:** phần lớn yêu cầu của FR-02 **giải được mà không cần judge chấm structure**. Chỗ duy nhất judge được dùng là (3b) — và ở đó nó làm **phân loại**, không phải **chấm điểm**; theo Chen et al. (ICML 2024, routed) đây là vùng MLLM judge mạnh nhất, không phải yếu nhất. RQ03 **không** khẳng định 3b đáng tin — nó khẳng định 3b **phải được đo noise floor trước khi dùng** (G-09), và **phải được kiểm chéo với 3a** để biết judge có đang chỉ đọc độ dài hay không.

### A3. Precedent gần nhất báo rằng conditioning HAY THẤT BẠI — nên phép đo phải fail được

**EXTERNAL EVIDENCE (E1).** X+Slides: cross-audience "target advantage" của DeepPresenter cho specialist prompt là **−0.117**; tác giả kết luận *"audience prompts... do not consistently align with the intended profile without audience-conditioned evaluation"*. Hiệu ứng conditioning ở mọi nguồn đều **nhỏ** (X+Slides paired delta ≤ ~0,083; Hu & Collier: persona <10% variance).

**INFERENCE:** hai hệ quả. (a) Deck Agent **không được giả định** rằng truyền `slide_type` vào prompt là FR-02 tự thỏa — xác suất thực tế cao là lần đo đầu ra kết quả "chưa đạt", và đó là **kết quả đúng**, không phải lỗi phép đo. (b) Vì hiệu ứng nhỏ, phép đo phải **ghép cặp theo source** và phải có **noise floor**; so trung bình toàn benchmark sẽ chôn tín hiệu dưới phương sai giữa các source.

---

## B. Bảng bàn giao: mỗi câu hỏi con → evaluator family → dùng ở đâu

**RECOMMENDATION.** Ký hiệu family theo RQ06. Cột **Dev** = chạy được ở nhịp nightly/weekly. Cột **Final** = dùng làm evidence báo cáo/bảo vệ.

| # | Câu cần chứng minh | Family | Dev | Final | Lý do |
| --- | --- | :-: | :-: | :-: | --- |
| S1 | Deck tuân thủ hard constraint của brief (`target_slides`, `time_limit_minutes`) | **F1** | ✓ | ✓ | IFEval (E1): verifiable instruction thì kiểm bằng code. Learning to Present `s_count = min/max`. X+Slides cho sẵn công thức thời lượng `M = 0,25·K + Words/130` |
| S2 | **Có** khác biệt giữa 3 mode, vượt nhiễu (Kiểm 1) | **F1 + thống kê** | ✓ | ✓ | Không có model trong vòng lặp → lặp lại được hoàn toàn. Dror et al. (ACL 2018) cho protocol chọn test; X+Slides dùng bootstrap 10.000 |
| S3 | Khác biệt **đúng hướng** đã khai báo (Kiểm 2) | **F1** + spec do người viết trước | ✓ | ✓ | Hướng không tra được từ literature (negative finding) → phải pre-register. Hoewe (E2): manipulation check thành công là check gợi ra *expected* differences |
| S4 | Khác biệt **nằm trong thuộc tính bề mặt nào** (Kiểm 3a) | **F1** (classifier nhỏ trên feature vector, CV nhóm theo source) | ✓ | ✓ | Trả lời câu hỏi 1 của RQ03 **bằng dữ liệu** thay vì bằng giả định; feature importance chỉ ra attribute nào mang tín hiệu |
| S5 | Khác biệt **nhận ra được** khi chỉ nhìn deck (Kiểm 3b) | **F3/F4 forced-choice 3 lớp** | △ (nightly, tập nhỏ) | ✓ | Learning to Present làm ở mức `audience`; Chen et al.: MLLM mạnh ở so sánh/lựa chọn. **Bắt buộc kèm noise floor (G-09)** |
| S6 | Mode có chọn **tập thông tin** khác nhau không (không chỉ độ dài) | **F2** (probe theo mode, weight theo `slide_type`) | ✗ | ✓ | X+Slides: *"audience adaptation mainly changes information selection"* — cơ chế "cùng probe, khác weight" lấy nguyên |
| S7 | `teaching` có **giải thích prerequisite** không | **F3/F4** | ✗ | △ | EffectivePresentationScorer chỉ ra đây là chỗ hệ thống hiện tại fail và metric hiện tại không bắt được. **Chưa có phương án deterministic nào tồn tại** |
| S8 | Background vắng mặt là **cố ý** hay **sót** | **F3/F4 + trọng số theo mode** | ✗ | ✓ | Va chạm RQ02↔RQ03 (`landscape.md` §6.3). Không giải bằng RQ03 một mình |
| S9 | **Spec 3 mode có đúng không** | **F5 human**, một lần | ✗ | ✓ | Không có literature định nghĩa 3 nhãn này. Là quyết định sản phẩm |
| S10 | Trần người của task phân loại 3 mode | **F5 human** | ✗ | ✓ | Cần trần để biết judge còn cách bao xa (nguyên tắc RQ06 §A3) |
| S11 | "Cách chia nội dung có **hợp lý** không" (§8 nguyên văn) | — | ✗ | ✗ | **RQ03 khuyến nghị KHÔNG dùng** làm metric theo dõi: PPTEval Coherence 0.55, JudgeSense bất ổn nhất. Xem mục 1.4 |

---

## C. Ba thứ dễ làm sai, nêu trước

**C1. Đo contrast mà không kèm sàn chất lượng = claim rỗng.**
Mọi feature ở `landscape.md` §4.A đều tối ưu hóa được rẻ tiền: nhồi chữ vào `catchup`, cắt chữ ở `speaker_support` → mọi phép kiểm contrast "đạt" trong khi cả ba deck đều tệ hơn. FR-02 dùng chữ *"có chủ đích"*, không phải *"khác nhau"*. → **Contrast test luôn phải báo cáo kèm fidelity/coverage (RQ02) và L0 artifact integrity.** Không bao giờ báo cáo một mình.

**C2. Gộp thành một điểm số sẽ xóa mất giá trị chẩn đoán.**
Bốn tổ hợp kết quả ở `landscape.md` §5.4 dẫn tới bốn hành động **khác nhau** ("planner bỏ qua `slide_type`" vs "khác đúng hướng nhưng quá nhỏ"). Một số duy nhất không phân biệt được chúng. Cùng logic G-02 đã accept cho content fidelity.

**C3. Chỉ đổi đúng một biến, và ghi vào manifest.**
X+Slides gắn thời lượng khác nhau cho từng audience (12/15/8 phút). Nếu Deck Agent để `time_limit_minutes` thay đổi theo `slide_type`, đó là **hai biến** — khác biệt quan sát được sẽ không quy được cho `slide_type`. Phải hoặc giữ cố định, hoặc khai báo tường minh là một phần của định nghĩa mode. Thuộc run manifest (G-14).

---

# 1. Recommendation

**RECOMMENDATION — đề xuất, chưa phải quyết định.**

## 1.1 Nguyên tắc bao trùm

**Chuyển FR-02 từ một câu hỏi chấm điểm thành một manipulation check.** Trace đầy đủ:

**Problem.** FR-02 yêu cầu *"cùng PDF, đổi `slide_type` → cấu trúc deck khác **có chủ đích**"*, DoD yêu cầu *"`slide_type` có khác biệt **đo được**"*, nhưng "đo được" chưa được định nghĩa. Cách đọc mặc định — giao cho MLLM-judge chấm "Structure" như §8 đề xuất — rơi đúng vào dimension mà judge yếu nhất, và vẫn không trả lời được câu hỏi vì "khác biệt" là quan hệ giữa nhiều output chứ không phải thuộc tính của một output.

**Evidence.**
- **X+Slides (E1, 2026):** đã có sẵn cơ chế đo contrast chéo giữa các condition trên cùng source, và đo được rằng conditioning **có thể đi sai hướng** (target advantage −0.117); tác giả kết luận prompt *"do not consistently align with the intended profile"*.
- **Learning to Present (E1 idea-source, 2026):** khôi phục spec (bao gồm `audience`) từ deck là một phép đo khả thi, đã được triển khai thật.
- **Atil et al. (E1):** nondeterminism ở cấu hình "deterministic" là thật và đáng kể; temperature=0 không loại bỏ được.
- **IFEval (E1):** phần yêu cầu diễn đạt được thành predicate thì kiểm bằng code, không giao cho evaluator model.
- **PPTEval / PresentBench / JudgeSense (E1, routed):** judge yếu nhất đúng ở structure/coherence.
- **Negative finding:** không nguồn nào định nghĩa `teaching | catchup | speaker_support`; không nguồn nào đo run-to-run variance của hệ sinh slide; không nguồn nào evaluate `speaker_notes`.

**Reasoning.**
1. Nếu "khác biệt" là quan hệ giữa các output, thì phép đo phải có **ít nhất 2 output từ cùng source** — kéo theo thiết kế paired, không phải một score.
2. Nếu chạy lại cùng điều kiện cũng ra khác nhau (Atil et al.), thì mọi tuyên bố về khác biệt **không có nghĩa** cho tới khi có nhóm đối chứng WITHIN. Đây là điều kiện chặn, không phải cải tiến.
3. Nếu hướng kỳ vọng không tra được từ literature, nó phải do người khai báo — và phải khai báo **trước**, nếu không phép đo mất khả năng bác bỏ.
4. Nếu judge yếu ở **chấm điểm** structure nhưng mạnh hơn ở **so sánh/lựa chọn**, thì hãy đặt câu hỏi dưới dạng lựa chọn.

**Recommendation.** Đo FR-02 bằng **ba phép kiểm tách rời trên một thiết kế three-armed paired theo source**, báo cáo **ba kết quả riêng, không gộp**:

```
Với mỗi source S:  teaching × R  |  catchup × R  |  speaker_support × R
                   (chỉ meta.slide_type thay đổi; mọi thứ khác cố định)

Kiểm 1  CÓ khác không?         → effect size chuẩn hóa theo WITHIN + permutation test
                                  (hoán vị nhãn trong từng source) + bootstrap CI
Kiểm 2  ĐÚNG HƯỚNG không?      → % cặp (feature × cặp-mode) khớp bảng dấu ĐÃ KHAI BÁO TRƯỚC
Kiểm 3  NHẬN RA được không?    → 3a: classifier deterministic trên feature vector,
                                      cross-validation leave-one-source-out
                                  3b: judge forced-choice 3 lớp, chance = 1/3,
                                      kiểm định nhị thức, kèm noise floor N≥5
```

Kèm bắt buộc: **sàn chất lượng** (RQ02 fidelity/coverage + L0 integrity) báo cáo song song. Không có sàn thì kết quả contrast không phát biểu được.

## 1.2 Thuộc tính quan sát được — trả lời câu hỏi 1 và 2

Danh sách đầy đủ ở `landscape.md` §4. Tóm tắt phân loại:

**Deterministic, tính thẳng từ Deck IR, không cần model, không cần render (ưu tiên cao nhất — rẻ, lặp lại được, chạy mỗi nightly):**
số slide · tổng từ · từ/slide (mean, median, **và độ lệch chuẩn**) · bullet/slide · từ/bullet · block/slide · tỉ lệ slide có ảnh · tỉ lệ block text:image · histogram `Slide.layout` + entropy · độ dài & tỉ lệ `speaker_notes` · độ dài title · tỉ lệ title là câu khẳng định · thời lượng ước lượng `0,25·K + Words/130` và sai lệch so với `time_limit_minutes` · sai lệch số slide so với `target_slides`.

**Deterministic nhưng cần tài liệu nguồn (qua `source_ref`):**
compression ratio · độ phủ vùng nguồn · phân bố `source_ref` theo vị trí trong nguồn · trật tự trình bày so với trật tự nguồn (`1 − NGLD` hoặc Kendall τ — công thức từ SlideTailor Flow) · non-redundancy.

**Cần model (F2):** tập chủ đề được chọn (IoU giữa các mode) · coverage có trọng số theo mode (cơ chế X+Slides).

**Cần judge (F3/F4):** có giải thích prerequisite không · omission cố ý hay sót · thân slide đứng một mình được không · recovery test 3b.

**Cần human (F5):** spec 3 mode có đúng không · trần người của task phân loại.

## 1.3 Điều RQ03 KHÔNG kết luận, và nói rõ vì sao

**RQ03 không xác nhận bảng minh họa** (`teaching` → giải thích nhiều hơn; `catchup` → density cao hơn, bỏ background; `speaker_support` → ít text, notes quan trọng hơn). Ba lý do có evidence:

1. **Không nguồn nào hậu thuẫn các hướng đó.** Bộ nhãn này không tồn tại trong literature; nguồn gần nhất cho trục `catchup` vs `speaker_support` là **blog (E3)**, mà `EVIDENCE_POLICY.md` cấm dùng một mình.
2. **X+Slides nói khác biệt nằm ở chỗ khác:** *"audience adaptation mainly changes **information selection**"* — tức ở **chọn nói gì**, không phải ở mật độ trình bày. Điều này **mâu thuẫn trực tiếp** với FR-20 (*"khác biệt cách dùng chữ/ảnh/mật độ nội dung"*). Mâu thuẫn này không giải được bằng lý lẽ, chỉ giải được bằng đo (Kiểm 3a sẽ chỉ ra feature nào thực sự mang tín hiệu).
3. **Nhánh `speaker_notes` không kiểm chứng được ở P0/P1:** `PROJECT SOURCE` FR-55 xếp *"speaker notes tự sinh"* vào **P3 (Could)**. Dùng `speaker_notes` làm thuộc tính phân biệt chính của `speaker_support` là một **thay đổi ưu tiên FR**, không phải một lựa chọn metric.

→ **RECOMMENDATION:** bảng hướng kỳ vọng phải do team viết như một **spec sản phẩm được version hóa**, đóng băng cùng frozen benchmark (giống judge prompt theo yêu cầu của RQ06 §C4). Đổi spec = mọi so sánh trước đó vô hiệu.

## 1.4 Về "Structure score" của §8 — đề xuất không xây

**RECOMMENDATION.** Không xây một metric "Structure" theo nghĩa §8 (*"số slide, cách chia nội dung hợp lý với loại slide"*) làm một điểm số dùng để theo dõi. Lý do, tách theo ba vế của chính §8:

| Vế của §8 | Xử lý đề xuất |
| --- | --- |
| "số slide" | → **F1**, thuộc brief compliance (S1). Đếm được. Không cần judge |
| "hợp lý với loại slide" | → **thành ba phép kiểm ở 1.1**. Đây là phần RQ03 giải quyết |
| "cách chia nội dung **hợp lý**" | → **không xây làm metric theo dõi.** Đây là đúng chỗ PPTEval Coherence đo được human correlation **0.55** và JudgeSense xếp bất ổn nhất. Nếu vẫn cần cho báo cáo cuối, dùng **pairwise + BPC/MEC + human trên mẫu con** (đúng bảng T7 của RQ06), **không** dùng pointwise 1–5 và **không** dùng cho dev tracking |

**Ghi rõ:** đây là một **challenge trực tiếp vào §8**, cần team/giảng viên review trước khi ảnh hưởng `06_design/`.

## 1.5 Signal ở tầng planner (G-04)

Tính **đúng bộ feature §4** trên **plan/outline artifact** (đã bắt buộc persist theo G-14), song song với trên deck cuối. Không tạo điểm số mới. Dùng **chỉ để chẩn đoán** khi contrast ở deck cuối không đạt: contrast có ở plan mà mất ở deck → downstream xóa mất; không có ở cả hai → planner bỏ qua `slide_type`. Bảng đầy đủ ở `landscape.md` §8.

**Ghi rõ theo G-04:** đây **KHÔNG** phải evidence rằng ContentPlanner đóng góp giá trị kiến trúc. Evidence đó đến từ end-to-end + ablation (RQ08). RQ03 **không** đề xuất "ContentPlanner Score".

## 1.6 Về ground truth (câu hỏi 6)

- **Không cần** gold deck. Nhãn `slide_type` đã biết theo cách xây dựng → setup tự gán nhãn. Nhất quán với P-10 của RQ01 (reference-based similarity không phân biệt "khác" với "kém").
- **Cần** một **bảng hướng kỳ vọng do team khai báo trước** — rẻ (một bảng), nhưng bắt buộc và phải viết trước.
- **Cần** human một lần để validate rằng spec đó đúng (S9) và để lấy trần người (S10).

---

# 2. Confidence level

| Kết luận | Confidence | Cơ sở / điều làm nó lung lay |
| --- | --- | --- |
| FR-02 là bài toán **quan hệ giữa nhiều output**, không phải thuộc tính của một deck | **Cao** | Lập luận cấu trúc từ chính câu chữ FR-02 + thiết kế của X+Slides (cross-audience scoring trên cùng source). Ít phụ thuộc một nguồn |
| **Phải có nhóm đối chứng WITHIN (R>1) trước khi phát biểu bất kỳ khác biệt nào** | **Cao** | Atil et al. (E1, 13 tác giả, 5 model × 8 task × 10 run) + RQ06 §4.5. Chi phí làm theo thấp so với rủi ro bỏ qua. **Không** dựa trên con số 15% (task khác) mà dựa trên mệnh đề định tính |
| Thiết kế **paired theo source** là bắt buộc, không phải tùy chọn | **Cao** | Hai nguồn độc lập cho thấy hiệu ứng conditioning nhỏ (X+Slides ≤0,083; Hu & Collier <10% variance) — nếu hiệu ứng nhỏ thì phải khử phương sai source |
| Phần lớn FR-02 giải được **không cần judge chấm structure** | **Trung bình–cao** | Lập luận phân rã + IFEval + bảng T7 của RQ06. Điểm yếu: đây là **INFERENCE của RQ03**, chưa nguồn nào phát biểu cách phân rã này |
| Danh sách thuộc tính deterministic §4.A tính được từ Deck IR | **Cao** | Đọc trực tiếp schema trong `kien-truc-do-an.pdf` §3. Ràng buộc `geometry` (giai đoạn mở rộng) và FR-55 P3 cũng đọc trực tiếp |
| Công thức thời lượng `M = 0,25·K + Words/130` dùng được cho FR-23 | **Trung bình–cao** | Lấy nguyên từ X+Slides (E1), tính được từ IR không mất mát. ⚠ Lấy `[qua tool summary]`, **chưa đọc PDF gốc**; và 130 wpm là hằng số cho tiếng Anh — **chưa biết có hợp tiếng Việt không** |
| **Conditioning có thể đi sai hướng** (rủi ro thật, không lý thuyết) | **Trung bình–cao** | X+Slides Table 9 target advantage −0.117 + kết luận nguyên văn của tác giả. ⚠ Con số lấy `[qua tool summary]`, **phải verify từ PDF gốc trước khi trích vào báo cáo** |
| Recovery test forced-choice đáng tin hơn chấm điểm structure | **Trung bình** | Chen et al. ICML 2024 (định tính nhất quán) + precedent Learning to Present. ⚠ **Không nguồn nào đo trực tiếp** độ tin cậy của judge ở task phân loại `slide_type`. Phải tự đo |
| Bảng minh họa 3 mode **chưa được xác nhận** | **Cao** (về việc *chưa xác nhận*) | Negative finding có chủ đích: search không ra nguồn nào dùng bộ nhãn này; nguồn duy nhất cho trục catchup/speaker_support là E3 |
| FR-20 ("khác biệt ở chữ/ảnh/mật độ") có thể sai chỗ | **Trung bình** | X+Slides nói *"mainly changes information selection"*; SlideTailor **không** có metric density nào. Nhưng cả hai đều ở profile khác → transfer không chắc |
| Ba chỗ gãy của taxonomy 5 lớp (G-03) | **Trung bình** | Gãy 1 (thiếu contrastive reference frame) và gãy 3 (L4 bị điều kiện hóa bởi L3) là lập luận logic khá chắc. Gãy 2 mang tính triết học hơn, dễ bị phản biện là "chỉ cần ghi chú thêm" |
| Attribute "title là câu khẳng định" (A13) | **Thấp** | Assertion-evidence có E1 hậu thuẫn cho việc *nó tốt*, **không** cho việc *nó phân biệt 3 mode*. Heuristic phát hiện câu khẳng định lại brittle và phụ thuộc ngôn ngữ |
| `speaker_notes` làm thuộc tính phân biệt `speaker_support` | **Thấp** | FR-55 là P3; **không precedent external nào** evaluate speaker notes (SlideTailor sinh nhưng không đo) |
| Đề xuất route trọng số coverage theo `slide_type` sang RQ02 | **Trung bình** | Cơ chế có E1 (X+Slides "cùng probe, khác weight"), nhưng RQ02 đang chạy song song — RQ03 **không** biết RQ02 đang kết luận gì |

**Điểm yếu verification lớn nhất của cả RQ03:** hai nguồn quan trọng nhất (X+Slides, Learning to Present) tôi **chỉ đọc qua tool summary của HTML full-text, chưa đọc PDF gốc trang thật**. Mọi con số trích từ hai nguồn này (−0.117, +0.079, +0.082, +0.083, công thức `M`, trọng số 0.40/0.25/0.15/0.20, điểm 0.530/0.616) **phải verify lại trước khi đưa vào báo cáo bảo vệ**.

---

# 3. Unresolved questions

**U-1. Khác biệt giữa 3 mode thực sự nằm ở đâu — mật độ trình bày hay lựa chọn thông tin?**
FR-20 nói mật độ chữ/ảnh; X+Slides nói *"mainly changes information selection"*. Hai mệnh đề này dẫn tới hai bộ feature khác nhau. **Không giải được từ evidence hiện có** — chỉ giải được bằng Kiểm 3a trên dữ liệu thật của Deck Agent. Nếu hóa ra khác biệt nằm ở information selection thì phần lớn nhóm feature §4.A (đếm/tỉ lệ) là **sai chỗ**, và trọng tâm phải dời sang §4.B (quan hệ với nguồn).

**U-2. Noise floor của Deck Agent là bao nhiêu?** Không nguồn nào báo cáo run-to-run variance cho hệ sinh slide. Phải tự đo. **Nếu WITHIN lớn hơn BETWEEN thì toàn bộ chiến lược đo `slide_type` phải xét lại, không chỉ điều chỉnh** — và đó là một khả năng thật, không phải giả định thận trọng.

**U-3. Cần bao nhiêu source và bao nhiêu lần lặp?** `R ≥ 5` là con số mượn từ khuyến nghị noise-floor của RQ06 (§C5), **không** phải kết quả tính power cho thiết kế này. Số source cần thiết phụ thuộc effect size thật — mà effect size thật chưa biết (U-1, U-2). → Đề xuất chạy **pilot** rồi tính ngược, thay vì chốt số trước.

**U-4. `time_limit_minutes` có nên khác nhau theo `slide_type` không?** X+Slides gắn thời lượng riêng cho từng audience (12/15/8 phút). Nếu Deck Agent làm vậy thì thiết kế có **hai biến** và khác biệt không quy được cho `slide_type`. Nếu không làm vậy thì có thể đang bỏ mất một phần định nghĩa mode. Đây là **quyết định thiết kế sản phẩm**, không phải câu hỏi research.

**U-5. Hằng số 130 từ/phút có áp cho tiếng Việt không?** Công thức `M = 0,25·K + Words/130` giả định tốc độ nói tiếng Anh. Deck Agent có `language: "vi"|"en"`. Chưa verify. Nếu sai, mọi phép kiểm FR-23 trên nhánh tiếng Việt bị lệch hệ thống.

**U-6. Trần người của task phân loại 3 mode là bao nhiêu?** Chưa ai đo. PPTEval κ≈0.59 là cho **chấm điểm**, không cho **phân loại**; PresentBench lại cho thấy human **rất nhạy** với cue cấu trúc khi rank nhanh. Hai tín hiệu ngược chiều → không suy đoán được, phải đo.

**U-7. Judge ở Kiểm 3b có đang chỉ đọc độ dài không?** Kiểm chéo với 3a phát hiện được, nhưng chưa có ai làm thí nghiệm này trên deck. Liên quan tới cảnh báo length/style bias của RQ06 (§C3, U-6) — với deck thì rủi ro có thể lớn hơn text thuần.

**U-8. Phần "giải thích prerequisite" (S7) có phương án deterministic nào không?** CourseBlueprint có ý tưởng dependency structure nhưng vẫn giao cho LLM judge; không tìm được nguồn nào kiểm bằng graph. Nếu không có, đây là attribute **duy nhất** của RQ03 buộc phải phụ thuộc judge — và cũng là attribute có nguồn tường minh nhất cho trục `teaching` vs `catchup`.

**U-9. Chưa verify được:** Wolfe et al. 2024 (SAGE, fetch bị chặn) — có thể chứa evidence **phản bác** guideline slide-design phổ biến, ảnh hưởng tới việc coi `words/slide` là proxy gì; cơ chế eval của PPLM (external classifier accuracy — **chưa quote-verified**); readability metric cụ thể mà Ribeiro et al. dùng; finding "expert cảm nhận khác biệt readability nhỏ hơn layperson" (arXiv 2210.04705) — nếu đúng thì người chấm là sinh viên trong team có thể **không nhìn thấy** khác biệt mà người dùng thật nhìn thấy.

**U-10. Chưa đọc, có thể đổi kết luận:** EffectivePresentationScorer full-text (chưa biết metric của nó deterministic hay judged — nếu deterministic thì U-8 có lời giải); ReadCtrl (readability liên tục — liên quan nếu team muốn `slide_type` là thang thay vì 3 nhãn rời).

---

# 4. Proposed decisions cần human/team review

> Tất cả dưới đây là **ĐỀ XUẤT**, chưa quyết định. Không ghi vào `DECISION_LOG.md`, không sửa `OPEN_QUESTIONS.md` / `WAVE1_SYNTHESIS.md` / `RQ_INDEX.md` (nhiều session chạy song song). Người điều phối quyết định có promote hay không.

**P-01 — Đọc lại FR-02 như một manipulation check, không như một dimension chấm điểm.**
Hệ quả: không có "slide_type score" cho một deck; đơn vị đánh giá là **bộ ba deck từ cùng một source**; báo cáo **ba kết quả riêng** (có khác / đúng hướng / nhận ra được), không gộp. Ảnh hưởng: `06_design/EVALUATION_FRAMEWORK.md`, `EVALUATION_PIPELINE.md`, và RQ07 (benchmark phải chạy mỗi source qua cả 3 mode). Confidence: cao.

**P-02 — Team phải viết một bảng hướng kỳ vọng cho 3 mode TRƯỚC khi đo, và version hóa nó cùng frozen benchmark.**
Đây là **đề xuất quan trọng nhất và cũng là việc RQ03 không được phép làm thay.** Không có literature định nghĩa `teaching | catchup | speaker_support` (negative finding có chủ đích). Nếu hướng được viết sau khi nhìn kết quả, DoD *"slide_type có khác biệt đo được"* trở thành một mệnh đề không thể bác bỏ — tức không phải evidence. **Cần một người trong team nhận việc này, và cần giảng viên xác nhận định nghĩa 3 mode.**

**P-03 — Bảng minh họa trong `RQ03_structure_planning.md` KHÔNG được dùng làm expected truth.**
RQ03 kiểm tra và **không xác nhận** nó: (a) không nguồn nào hậu thuẫn hướng; (b) X+Slides nói khác biệt nằm ở information selection chứ không ở density, mâu thuẫn trực tiếp với FR-20; (c) nhánh `speaker_notes` phụ thuộc FR-55 hiện là **P3**. Đề xuất: giữ nó như **một ứng viên cho bảng P-02**, ghi rõ là chưa validate. Confidence: cao.

**P-04 — Thêm yêu cầu đo noise floor của GENERATOR (không chỉ của judge).**
G-09 hiện chỉ nói về model-based evaluator. RQ03 cho thấy với FR-02 thì **noise floor của generator mới là biến quyết định**: không có `R > 1` thì không tồn tại nhóm đối chứng, và mọi khác biệt quan sát được không phân biệt được với nhiễu. Đề xuất mở rộng G-09 thành: *mọi so sánh giữa các điều kiện sinh (`slide_type`, version, ablation) phải kèm ước lượng within-condition variance*. Nếu chấp nhận → thuộc `06_design/EVALUATION_PIPELINE.md`. Confidence: cao.

**P-05 — Không xây "Structure score" theo nghĩa §8 làm metric theo dõi.**
Tách 3 vế: "số slide" → F1 brief compliance; "hợp với loại slide" → ba phép kiểm P-01; "cách chia nội dung **hợp lý**" → **không dùng cho dev tracking** (PPTEval Coherence 0.55, JudgeSense bất ổn nhất), nếu cần cho báo cáo thì pairwise + BPC/MEC + human. **Đây là challenge trực tiếp vào §8**, cần team/giảng viên xác nhận. Confidence: trung bình–cao.

**P-06 — Route sang RQ02: coverage cần trọng số theo `slide_type`.**
Cùng một quan sát ("background vắng mặt") là **coverage gap** dưới frame nguồn và **intentional omission** dưới frame brief. Nếu RQ02 đo coverage không điều kiện hóa theo `slide_type`, nó sẽ **phạt `catchup` vì đã làm đúng**. Cơ chế có precedent E1 (X+Slides: cùng probe, khác utility weight). **RQ03 không sửa RQ02** — cần người điều phối quyết định ai xử lý giao diện này. Confidence: trung bình (chưa biết RQ02 đang kết luận gì).

**P-07 — Ghi nhận 3 chỗ gãy của taxonomy 5 lớp (G-03), đề xuất chưa freeze.**
(1) RQ03 cần một **contrastive reference frame** thứ tư (so với output anh em) mà taxonomy không có — và lớp này bao gồm cả regression tracking lẫn ablation, tức trung tâm của mission. (2) Một quan sát rơi vào hai lớp vì reference frame là thuộc tính của **câu hỏi**, không phải của **quan sát**. (3) L4.2 ("mật độ chữ vừa phải") bị **điều kiện hóa bởi L3.2** — chấm L4 mà không biết `meta.slide_type` sẽ tạo sức ép ngược chiều FR-20. Chi tiết: `landscape.md` §7. Đề xuất: **giữ HOLD**, không promote vào `06_design/`. Confidence: trung bình.

**P-08 — Quyết định về `speaker_notes`: hoặc nâng FR-55 lên P1, hoặc bỏ nó khỏi định nghĩa `speaker_support`.**
Không thể vừa coi `speaker_notes` là thuộc tính phân biệt chính của `speaker_support`, vừa để việc sinh nó ở P3. **Đây là quyết định ưu tiên FR, không phải quyết định evaluation** — RQ03 chỉ nêu ra mâu thuẫn. Nếu giữ P3, `speaker_support` phải được phân biệt bằng các thuộc tính khác (tỉ lệ ảnh, từ/slide, độ dài title), và bảng P-02 phải phản ánh điều đó.

**P-09 — Quyết định `time_limit_minutes` có thay đổi theo `slide_type` không, và ghi vào run manifest.**
X+Slides gắn 12/15/8 phút cho 3 audience. Nếu Deck Agent làm tương tự mà không khai báo, thiết kế có **hai biến** và kết quả không quy được cho `slide_type`. Cần chốt trước khi chạy experiment — cùng loại rủi ro với G-12.

**P-10 — Chốt chi phí: benchmark của RQ03 nhân 3 (mode) × R (lặp).**
Ví dụ 30 source × 3 × 5 = **450 lần sinh deck** ở giai đoạn hiệu chuẩn. Đề xuất phân tầng: hiệu chuẩn WITHIN một lần trên 8–10 source × 3 × R=5; theo dõi thường xuyên toàn benchmark × 3 × R=1 dùng WITHIN đã hiệu chuẩn; judge (3b) + human chỉ ở milestone. **Cần team xác nhận budget thật** — con số này ảnh hưởng trực tiếp tới RQ07.

**P-11 — Verify lại số liệu X+Slides và Learning to Present từ PDF gốc trước khi vào báo cáo.**
Cụ thể: target advantage (−0.117 / +0.079 / +0.082), paired delta (+0.083), công thức `M = 0,25·K + Words/130`, trọng số `r_recon` (0.40/0.25/0.15/0.20), điểm spec_reconstruction (0.530 / 0.616), và các quote nguyên văn của tác giả. Hiện tất cả ở mức `[qua tool summary]`. Cần một người nhận việc verify — RQ03 không tự làm được trong session này.
