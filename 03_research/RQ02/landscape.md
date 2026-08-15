# RQ02 — Landscape: đo bịa / sai / bỏ sót cho Deck Agent

**Ngày research:** 2026-08-15
**Câu hỏi được giao (scope hẹp, không mở rộng):** Deck Agent cần những phép đo nào để **phân biệt ba failure** — bịa (hallucination), nói sai (incorrectness), bỏ sót (coverage gap) — sao cho **ít nhất một signal đủ rẻ và ổn định để chạy hằng ngày**?
**Trạng thái:** research output, **KHÔNG phải decision**. Mọi câu đều được gắn nhãn `PROJECT SOURCE` / `EXTERNAL EVIDENCE` / `INFERENCE` / `RECOMMENDATION`.

Nguồn + evidence level + tag đọc: `evidence.md`. Đề xuất hành động: `recommendation.md`.

**Đọc trước:** `03_research/WAVE1_SYNTHESIS.md` (G-02, G-05, G-06, G-07, G-09), `03_research/RQ06/landscape.md` (F1–F5, protocol P1–P6). RQ02 **không** dựng lại evaluator family — nó chỉ hỏi: trong các family đó, cái nào tách được 3 failure và cái nào chạy được hằng ngày.

---

## 0. Kết quả quan trọng nhất, nói trước

**INFERENCE (kết luận cấu trúc của RQ02).** Ba failure không nằm trên cùng một trục đo. Chúng khác nhau ở **chiều xác minh**:

```
                 CHIỀU 1: output → source  (precision)
   mỗi claim trên slide  ──────────────────►  tìm chỗ chống đỡ trong PDF
   ├─ tìm được, khớp        → OK
   ├─ tìm được chỗ NÓI VỀ CÙNG THỨ nhưng khác giá trị   → NÓI SAI (incorrectness)
   └─ không tìm được gì nói về thứ đó                   → BỊA (hallucination)

                 CHIỀU 2: source → output  (recall)
   mỗi ý quan trọng trong PDF ──────────────►  tìm nó trên deck
   └─ không tìm thấy                                    → BỎ SÓT (coverage gap)
```

Từ đó:

1. **Một phép đo chạy một chiều KHÔNG THỂ đo cả 3 failure.** Đây không phải vấn đề chất lượng model — nó là vấn đề **tập nào được duyệt**. Metric chiều 1 duyệt tập claim của deck; nếu deck không nói gì thì không có gì để duyệt, nên bỏ sót là **vô hình về mặt định nghĩa**.
   **EXTERNAL EVIDENCE xác nhận, có số:** trong *All Claims Are Equal* (2025, `evidence.md` §1.1), response **thiếu hẳn câu trả lời chính** ăn **FActScore = 82.75%**, còn response **đúng và đủ** chỉ được **82.58%**. Metric precision-only không chỉ mù với bỏ sót — nó còn có thể **chấm cao hơn** cho bản bỏ sót.

2. **Ranh giới "bịa" vs "nói sai" nằm ở việc source có COUNTERPART hay không**, không nằm ở mức độ sai. Đây là trục mà **mọi metric alignment nhị phân đều làm mất**: AlignScore, SummaC, MiniCheck đều trả về "supported / not supported" — cả hai failure rơi vào cùng một ô `not supported`.

3. Suy ra: **phép đo tối thiểu cho Deck Agent là 2 pass ngược chiều, cộng một quy tắc phân loại cho phần fail của pass 1.** Không có shortcut một-số.

**EXTERNAL EVIDENCE cho kiến trúc này:** FineSurE (ACL 2024, `evidence.md` §1.2) là hiện thân trực tiếp — 2 task tách rời: *fact checking* (chiều 1, chấm từng câu output) và *keyfact alignment* (chiều 2, chấm từng keyfact), cho ra 3 con số độc lập (faithfulness / completeness / conciseness).

---

## 1. Toy example — làm trước, kết luận sau

Đây là phần bắt buộc của RQ02. Mọi approach ở §2 sẽ được chấm lại đúng trên bộ example này ở §3.

### 1.1 Thiết lập

Source PDF `S` có **5 ý quan trọng**:

| id | Ý trong source (nguyên văn giả định) |
| --- | --- |
| **A** | "Công ty mở rộng sang thị trường Đông Nam Á trong Q2/2025." |
| **B** | "Doanh thu tăng từ 12.4M lên 18.7M do enterprise adoption tăng tốc." |
| **C** | "Chi phí vận hành giữ nguyên ở mức 8.1M." |
| **D** | "Churn rate giảm từ 6.2% xuống 3.9%." |
| **E** | "Kế hoạch 2026: ra mắt sản phẩm self-serve." |

### 1.2 Bốn output cần phân biệt

| Output | Nội dung deck | Failure thật |
| --- | --- | --- |
| **O1** | A, B, C | **Coverage gap** — thiếu D, E. Mọi thứ đã nói đều đúng. |
| **O2** | A, B, C, D, **F** = *"Công ty huy động vòng Series C 40M."* | **Hallucination** — F không có trong S, và S **không nói gì** về gọi vốn. |
| **O3** | A, B, C, **D′** = *"Churn rate giảm từ 6.2% xuống **1.9%**."*, E | **Incorrectness** — S **có** nói về churn, nhưng deck nói **sai giá trị**. |
| **O4** | A, **B′** = *"Enterprise adoption drove **~51% YoY revenue growth**."*, C, D, E | **Không phải failure** — B′ là **B đã bị nén + diễn giải + TÍNH TOÁN LẠI**: (18.7−12.4)/12.4 = 50.8% ≈ 51%. Đây là case khó bắt buộc phải xét theo G-05. |

**INFERENCE — vì sao O3 và O2 phải tách nhau, không gộp thành "not supported":** chúng có **nguyên nhân kỹ thuật khác nhau** và **hành động sửa khác nhau**.
- O2 (bịa) thường đến từ model **điền vào chỗ trống** khi source mỏng, hoặc từ parametric knowledge rò rỉ. Sửa: siết prompt, ép `source_ref` bắt buộc, chặn slide không có block trace được.
- O3 (nói sai) đến từ **lỗi copy/nén số** trong lúc ContentPlanner rút gọn. Sửa: thêm bước verify số, hoặc giữ nguyên văn số thay vì paraphrase.
Một score `fidelity = 0.8` cho cả hai làm mất chính thông tin quyết định team sửa cái gì. Đây là G-02 nói ở mức cụ thể.

**INFERENCE — vì sao O4 là case nguy hiểm nhất:** nó là **output ĐÚNG và ĐÁNG MONG MUỐN** (một slide tốt *nên* nói "51% YoY" thay vì đọc lại hai con số thô), nhưng mọi evaluator textual đều có nguy cơ đánh nó là failure. Một metric đánh sai O4 sẽ **phạt Deck Agent vì đã làm đúng việc của nó** — và tệ hơn, sẽ tạo áp lực ngược khiến team tinh chỉnh ContentPlanner theo hướng copy nguyên văn để ăn điểm. Đây là failure mode **của hệ đo**, không phải của sản phẩm.

### 1.3 Bảng kết quả kỳ vọng (dùng để chấm approach ở §3)

Một instrument đạt yêu cầu RQ02 phải cho ra **bốn dòng khác nhau**:

```
        coverage   hallucination   incorrectness   verdict cho O4
O1        3/5           0               0          —
O2        4/5           1               0          —
O3        5/5*          0               1          —          (*D được "phủ" nhưng sai → xem §1.4)
O4        5/5           0               0          KHÔNG ĐƯỢC báo là bịa
```

### 1.4 Một quyết định thiết kế lộ ra ngay từ toy example

**INFERENCE.** O3 buộc phải trả lời: *ý D được nói nhưng nói sai thì tính là "đã phủ" hay "chưa phủ"?*

Hai lựa chọn, cả hai đều dùng được, nhưng **phải chọn một và ghi vào spec**:
- **(a) Coverage đo sự HIỆN DIỆN của chủ đề** → O3 có coverage 5/5 và incorrectness 1. Ưu điểm: coverage và correctness **độc lập hoàn toàn**, đọc được riêng. Đây là cách RQ02 nghiêng về.
- **(b) Coverage đo sự HIỆN DIỆN ĐÚNG** → O3 có coverage 4/5. Ưu điểm: một con số duy nhất "deck truyền tải đúng bao nhiêu ý". Nhược điểm: **trộn lại đúng cái G-02 vừa tách ra** — O1 và O3 lại có thể cùng ra 4/5 hoặc gần nhau.

**RECOMMENDATION:** chọn (a). Trace: `problem (G-02 yêu cầu tách 3 failure) → evidence (evidence.md §1.1: gộp làm missing và normal ra cùng điểm) → reasoning (nếu coverage phụ thuộc tính đúng thì coverage và correctness lại tương tác, tái tạo đúng vấn đề) → recommendation (coverage = topic presence, correctness đo riêng)`. Đây là **đề xuất**, cần team xác nhận vì nó ảnh hưởng schema `critical_facts` của RQ07.

---

## 2. Các approach — cách hoạt động, bắt được gì, giá bao nhiêu

Ký hiệu family F1–F5 giữ nguyên từ RQ06 (`03_research/RQ06/landscape.md` §2) để không tạo từ vựng mới.

### A1. Deterministic / structural check (F1)

**Cách hoạt động:** code thuần, không có model. Với Deck Agent, ba thứ đo được:
1. **`source_ref` integrity** (PROJECT SOURCE — FR-03 yêu cầu block trace được về vùng nguồn): mỗi block có `source_ref` không, `source_ref` trỏ tới offset/trang tồn tại không, span có non-empty không. → `traceability_rate`.
2. **Number provenance:** trích mọi token số trong deck, kiểm tra từng số có xuất hiện trong source không (chuẩn hóa định dạng: `18.7M` ↔ `18,700,000` ↔ `18.7 triệu`). → `verbatim_number_rate` + danh sách **số không khớp**.
3. **Named-entity provenance:** tương tự với entity (tên riêng, tên sản phẩm, tên tổ chức).

**Bắt được failure nào:**

| | Bắt được? | Ghi chú |
| --- | --- | --- |
| Bỏ sót | **Không** | Không có khái niệm "ý quan trọng" ở tầng này |
| Bịa | **Một phần, rất mạnh ở lớp con nguy hiểm nhất** | O2 ("Series C 40M") có số `40M` không tồn tại trong source → bắt ngay, chi phí 0 |
| Nói sai | **Một phần** | O3 (`1.9%` thay vì `3.9%`) → `1.9%` không có trong source → bắt ngay |
| **O4 (tính toán lại)** | **Đánh dấu, không kết tội** | `51%` không có trong source → rơi vào danh sách "số không khớp". **Đây là tính năng, không phải bug** — xem §4.3 |

**INFERENCE — vì sao lớp này bị đánh giá thấp một cách sai lầm:** nó trông "ngây thơ", nhưng loại lỗi nó bắt (**số sai / số bịa**) chính là loại lỗi (a) tốn kém nhất khi đi vào một deck thật, (b) dễ nhận ra nhất khi giảng viên xem demo, và (c) là loại mà **NLI được biết là yếu nhất** (`evidence.md` §2.1 — EQUATE: NLI model không tự học suy luận với đại lượng). Tức F1 và F2 **bù nhau đúng chỗ**, không chồng lấn.

| | |
| --- | --- |
| **Chi phí** | ≈ 0 (regex + chuẩn hóa số) |
| **Tốc độ** | ms |
| **Ổn định** | **Tuyệt đối** — cùng input ra cùng output, noise floor = 0 theo định nghĩa |
| **Rủi ro** | Chuẩn hóa số/đơn vị sai → đo sai một cách **im lặng**. Cần unit test cho chính normalizer |

---

### A2. NLI / alignment-based (F2b) — SummaC, AlignScore

**Cách hoạt động:** coi source (hoặc từng đoạn source) là *premise*, từng câu/bullet của deck là *hypothesis*, hỏi model entailment. SummaC-Conv chia document thành đơn vị câu rồi aggregate điểm NLI theo cặp. AlignScore dùng một alignment function thống nhất.

**Bắt được failure nào:**

| | |
| --- | --- |
| Bỏ sót | **Không** — chiều sai. O1 (thiếu D, E) ăn điểm **hoàn hảo** vì mọi thứ nó nói đều được entail |
| Bịa | **Có** — O2: `F` không được entail → điểm giảm |
| Nói sai | **Có, nhưng KHÔNG TÁCH ĐƯỢC khỏi bịa** — O3: `D′` không được entail → cũng chỉ là "điểm giảm". Cả O2 và O3 ra cùng một hình dạng tín hiệu |
| **O4** | **Rủi ro cao** — xem §4.1 |

**EXTERNAL EVIDENCE về độ tin cậy (`evidence.md` §1.3, *Stress Testing*, 2025):**
- **AlignScore** và SummaC-ZS *"particularly unreliable across domains and perturbation types"*.
- **Điểm chí mạng cho Deck Agent:** *"metric scores consistently decrease as claim similarity increases"* — metric **hoạt động tệ hơn trên claim bị nén, mật độ thông tin cao**. AlignScore *"exhibit sharp declines for high-similarity claims"*.
- *"NLI-based metrics remain insensitive to increasing context windows"* — chúng so cục bộ, không tổng hợp evidence rải rác.

**EXTERNAL EVIDENCE về gaming (`evidence.md` §1.4, *Do Automatic Factuality Metrics Measure Factuality?*):** chèn một câu ngẫu nhiên **lấy từ source** vào output làm điểm tăng bằng hoặc hơn mức tăng do sửa lỗi thật; chèn cụm hằng định cho +0.1–0.15 điểm; gaming cho cải thiện **gấp 3–8 lần** so với cải thiện thật.

**INFERENCE — vì sao gaming này đặc biệt độc với Deck Agent:** cách "gaming" mà paper mô tả (chèn câu nguyên văn từ source) **chính là hành vi mà một ContentPlanner tệ sẽ tự nhiên sinh ra** — copy nguyên đoạn PDF lên slide. Nếu team dùng AlignScore/SummaC làm metric chính, họ sẽ vô tình xây một hàm thưởng cho **deck dày chữ, copy-paste** — thứ mà FR-22 và mục tiêu "dùng được ngay" coi là lỗi. Đây là mismatch cụ thể của domain, không phải cảnh báo chung.

| | |
| --- | --- |
| **Chi phí** | Thấp (chạy local, model vài trăm M params) |
| **Tốc độ** | Nhanh |
| **Ổn định** | **Cao nếu pin weights** — encoder classifier, không sampling. Nhưng xem §5 về nguồn nhiễu thật |
| **Verdict** | **AlignScore: hạ priority.** Không phải vì "chưa ai áp lên slide" (đúng nhưng đó là lý do yếu), mà vì có **evidence trực tiếp** rằng nó yếu nhất đúng ở hai đặc tính định nghĩa output của Deck Agent: **claim bị nén** và **evidence rải rác** |

---

### A3. Lightweight grounding classifier (F2b thế hệ mới) — MiniCheck

**Cách hoạt động:** cũng là chiều 1 (claim → document), nhưng model được huấn luyện bằng dữ liệu synthetic thiết kế riêng để dạy *"check each fact in the claim and recognize **synthesis of information across sentences**"* (`evidence.md` §1.5).

**Vì sao tách riêng khỏi A2 dù cùng chiều:** **INFERENCE** — mục tiêu huấn luyện của nó (claim tổng hợp thông tin từ **nhiều câu nguồn**) là **đúng hình dạng của một bullet trong deck**. Một bullet hiếm khi ánh xạ 1–1 vào một câu PDF; nó thường gộp một câu ở trang 3 với một số ở bảng trang 7. Đó là điểm mà A2 được ghi nhận là yếu (*"insensitive to increasing context windows"*, so cục bộ).

**Bắt được failure nào:** giống A2 về mặt cấu trúc (bịa ✓, nói sai ✓ nhưng không tách được, bỏ sót ✗). Khác ở **độ tin cậy**:
- `evidence.md` §1.3: **MiniCheck** và UniEval *"comparatively stable performance"* — bền nhất trong 6 metric được stress-test. Vẫn **vấp ở negation tương đương logic**.
- `evidence.md` §1.4: MiniCheck và AlignScore có **dịch chuyển điểm dương mạnh nhất sau khi sửa lỗi factual** — tức nó **thật sự phản ứng với tính đúng**, khác QuestEval (không đổi) và khác SummaC-Conv/UniEval (nhạy với sửa vô hại hơn sửa đúng).
- Vẫn **game được** bằng chèn câu từ source (áp dụng cho cả 6 metric).

| | |
| --- | --- |
| **Chi phí** | **0 đồng API.** Model 770M chạy local |
| **Tốc độ** | Repo chính chủ: 29K claim trong **30 phút** trên 1 GPU A6000 → **INFERENCE:** ~16 claim/s → deck 10 slide × 4 bullet ≈ 40 claim ≈ **vài giây/deck** |
| **Ổn định** | Cao (xem §5) |
| **Verdict** | **F2 candidate mạnh nhất cho daily** — nhưng vẫn cần pilot (§6) |

---

### A4. Atomic fact decomposition (F2c) — FActScore, SAFE, VeriFastScore

**Cách hoạt động:** tách output thành **atomic fact** (mỗi mệnh đề đúng một thông tin), verify từng cái với knowledge source, điểm = % được support.

**Bắt được failure nào:**

| | |
| --- | --- |
| Bỏ sót | **Không.** Và đây là chỗ có **bằng chứng số mạnh nhất của cả RQ02**: `evidence.md` §1.1 — FActScore chấm response **thiếu** 82.75% và response **đúng đủ** 82.58%. Metric **không phân biệt được**, thậm chí xếp bản thiếu cao hơn |
| Bịa | **Có** |
| Nói sai | **Có, không tách được khỏi bịa** |
| **O4** | Rủi ro cao, cộng thêm rủi ro decomposition (xem dưới) |

**Vấn đề riêng của nhánh này — decontextualization:** `evidence.md` §2.6 (Molecular Facts, EMNLP Findings 2024) nêu đúng tension: đơn vị càng atomic càng **thiếu context để diễn giải đúng**. **INFERENCE:** bullet của deck đã là fragment cực đoan (`"— Doanh thu +51% YoY"` — mất chủ ngữ, mất mốc, mất đơn vị). Decompose nó nữa thì được các mệnh đề **không thể verify độc lập**. Tức bước decomposition — vốn là bước tốn kém nhất — vừa **thêm chi phí** vừa **thêm một nguồn lỗi mới**, trong khi bullet vốn đã gần atomic sẵn.

**RECOMMENDATION (hạ priority):** với Deck Agent, **bullet nên được coi là đơn vị verify, không decompose thêm**. Trace: `problem (bullet là fragment) → evidence (Molecular Facts: atomic quá mất context; evidence.md §1.4: LLM decomposition là bước sinh lỗi và tốn call) → reasoning (bullet đã ở granularity gần atomic; decompose thêm chỉ tạo mệnh đề không đứng độc lập được) → recommendation (dùng bullet làm claim unit)`. Điều này **loại luôn** phần đắt nhất của FActScore/SAFE và làm A3 trở nên khả thi cho daily.

**Ghi nhận về chi phí:** VeriFastScore (`evidence.md` §2.9, EMNLP Findings 2025) cho thấy gộp decompose + verify vào một lượt cho **speedup 6.6×** và giữ tương quan **r=0.94 ở mức system** dù chỉ **r=0.80 ở mức example**.
**INFERENCE quan trọng cho mission:** khoảng cách 0.94 vs 0.80 nói rằng một metric rẻ có thể **đủ tin để so version** (câu hỏi hôm-nay-vs-hôm-qua là câu hỏi **system-level**) trong khi **chưa đủ tin để phán xét một deck cụ thể**. Đây là lập luận trực tiếp cho việc dùng F2 ở daily nhưng **không** dùng nó làm evidence bảo vệ.

⚠ SAFE có **F1@K** gộp precision và recall — nhưng recall của nó neo vào **K = số fact "lý tưởng"**, **không** neo vào ý quan trọng của source. **Không** giải được coverage theo nghĩa RQ02 (`evidence.md` §3.4).

---

### A5. Keyfact / critical-fact alignment (chiều 2) — FineSurE, ACU

**Cách hoạt động:** trích một tập **ý quan trọng** (keyfact / critical fact / ACU) **từ source**, rồi với **từng ý**, hỏi: deck có nói ý này không, và ở slide nào.

**Bắt được failure nào:**

| | |
| --- | --- |
| **Bỏ sót** | **CÓ — đây là approach duy nhất đo trực tiếp được** |
| Bịa | **Không.** Một claim trên deck không khớp keyfact nào có thể là bịa, **hoặc** là chi tiết phụ hợp lệ. Không phân biệt được |
| Nói sai | **Có, nếu keyfact mang GIÁ TRỊ** (`churn 6.2% → 3.9%`) chứ không chỉ mang chủ đề (`có nói về churn`) |
| **O4** | Trung tính — nếu keyfact B ghi cả hai con số thì câu hỏi trở thành "deck có truyền tải ý này không", dễ hơn "câu này có được entail không" |

**EXTERNAL EVIDENCE (`evidence.md` §1.2, FineSurE ACL 2024):** completeness đo bằng keyfact alignment đạt **Pearson 0.688 / Spearman 0.677**, so với **G-Eval 0.314 / 0.295** và **UniEval 0.134 / 0.180**. Đây là khoảng cách lớn nhất trong toàn bộ evidence của RQ02 — và nó nói rằng **rubric chung/LLM judge gần như không đo được completeness**, còn đối chiếu theo item thì đo được.

**EXTERNAL EVIDENCE độc lập cùng hướng (`evidence.md` §2.2, AbsenceBench, 2025):** *"models excel at recalling surprising information, they still struggle to identify clearly omitted information"*; model tốt nhất chỉ đạt **69.6% F1** ở **5K token context**, dù task đó **dễ hơn** task của Deck Agent (được cấp cả bản gốc lẫn bản đã xóa). Cơ chế: attention *"cannot easily attend to 'gaps'"*.

**INFERENCE.** Hai nguồn khác hẳn nhau về phương pháp (một là meta-evaluation metric summarization, một là benchmark long-context) chỉ về cùng một hướng: **"có bỏ sót gì không" là câu hỏi mà LLM trả lời kém khi được hỏi mở, nhưng trả lời được khi bị biến thành một danh sách đóng để tick từng dòng.** Đây là lý do kỹ thuật thật khiến per-instance criteria (G-06) là bắt buộc cho coverage — mạnh hơn lý do "vì material-dependent" mà RQ06 nêu.

**Chi phí:** phụ thuộc hoàn toàn vào **cách tạo keyfact** — xem §4.2. Bản thân bước alignment thì rẻ (có thể chạy bằng chính model ở A3, ngược chiều).

---

### A6. Question / probe based — QAFactEval, QA-from-source

**Cách hoạt động:** sinh câu hỏi, rồi trả lời bằng source và bằng deck, so đáp án. Có 2 biến thể **rất khác nhau**:
- **A6a — hỏi từ output** (QAGS/QuestEval cổ điển): câu hỏi sinh từ claim của deck → đo precision.
- **A6b — hỏi từ source**: câu hỏi sinh từ ý quan trọng của source → **hỏi deck**. Nếu deck không trả lời được → **bỏ sót**; trả lời nhưng khác đáp án của source → **nói sai**.

**Bắt được failure nào (A6b):**

| | |
| --- | --- |
| Bỏ sót | **Có** — câu hỏi không trả lời được từ deck |
| Nói sai | **Có, và TÁCH ĐƯỢC khỏi bỏ sót một cách tự nhiên** — trả lời được nhưng sai đáp án |
| Bịa | **Không** — không có ai hỏi về `F` vì `F` không có trong source |
| **O4** | Tốt hơn NLI: câu hỏi *"Doanh thu thay đổi thế nào?"* → deck trả lời *"tăng ~51% YoY"* → so với đáp án source *"12.4M → 18.7M"*. Việc đối chiếu 2 câu trả lời **vẫn** cần suy luận số, nhưng câu hỏi đã **định vị đúng chỗ**, thay vì hỏi model có entail một câu nén hay không |

**EXTERNAL EVIDENCE:** QAFactEval (NAACL 2022, `evidence.md` §2.8) — chất lượng phụ thuộc mạnh vào **question generation** và **answerability classification**; và điểm đáng chú ý nhất: *"QA-based and entailment-based metrics can offer **complementary signals** and be combined"*. Ngược lại, `evidence.md` §1.4 cho thấy **QuestEval không phản ứng** với việc sửa đúng — tức **nhánh QA không tự động an toàn**, phụ thuộc implementation.

**Chi phí:** cao hơn A2/A3 vì nhiều bước (generate → answer × 2 → so khớp). Nhưng **nếu bộ câu hỏi được sinh MỘT LẦN và đóng băng vào benchmark**, phần chạy hằng ngày chỉ còn "trả lời câu hỏi từ deck + so đáp án" — rẻ đi rất nhiều. **INFERENCE:** đây là biến đổi quan trọng — chi phí authoring là **one-time per benchmark case**, chi phí chạy là **per run**; hai thứ này bị nhầm lẫn thường xuyên.

---

### A7. Per-instance checklist do LLM sinh (F4 × P4, kiểu TICK)

**Cách hoạt động:** LLM đọc source + constraint, sinh danh sách câu hỏi YES/NO riêng cho instance đó; judge trả lời từng câu trên deck.

**Bắt được failure nào:**

| | |
| --- | --- |
| Bỏ sót | **Có** — nếu item được viết dạng *"Deck có nêu churn giảm còn 3.9% không?"* |
| Nói sai | **Có** — nếu item mang giá trị cụ thể |
| Bịa | **YẾU VỀ MẶT CẤU TRÚC** — xem dưới |

**INFERENCE — điểm mù cấu trúc của checklist, chưa thấy nguồn nào nêu thẳng:** checklist là một **danh sách đóng các thứ PHẢI CÓ**. Hallucination là một **tập mở các thứ KHÔNG ĐƯỢC CÓ**. Không thể liệt kê trước "deck không được bịa Series C 40M" vì không ai biết model sẽ bịa gì. Muốn checklist bắt được bịa thì phải thêm một item kiểu *"deck có chứa thông tin nào không có trong nguồn không?"* — nhưng item đó **không còn là câu hỏi nhị phân verify được**; nó là một judge call mở, đúng thứ mà checklist sinh ra để thay thế.

→ **RECOMMENDATION:** checklist per-instance là công cụ tốt cho **coverage + correctness**, **không** phải cho **hallucination**. Nó **không thay thế được** một pass chiều 1. Đây là hệ quả trực tiếp cho **NFR-53** (PROJECT SOURCE: *checklist theo instance + human corr. trên mẫu con*): NFR-53 như đang viết **không cover được failure "bịa"**, dù §8 gọi tên "không bịa" là mục tiêu.

**Chi phí:** PresentBench (đã verify Wave 1) chạy **một call judge cho mỗi item**, ~54 item/deck. **INFERENCE:** với deck 10 slide, đó là ~54 call/deck — không khả thi hằng ngày, và mỗi call mang noise của LLM judge.

---

### A8. Expert / manual checklist (kiểu PresentBench, và NFR-53 nếu hiểu theo nghĩa người viết tay)

Giống A7 về khả năng phát hiện (kể cả **điểm mù với hallucination**), khác ở chi phí và độ tin.

**EXTERNAL EVIDENCE (verify ở Wave 1, `RQ06/evidence.md` §1.2):** PresentBench = **54.1 item/instance × 238 instance, expert soạn tay**; đạt ρ ≈ 0.532 so với trần người 0.664, trong khi rubric chung (PPTEval) chỉ 0.303. HealthBench = **262 bác sĩ, 48.562 criteria**.

**INFERENCE:** đây là quy mô **dataset-building**, không phải quy mô đồ án. Nhưng cần nói chính xác điều gì **không** rút ra được: PresentBench **không** chứng minh "expert-authored > LLM-generated"; nó chứng minh "checklist per-instance > rubric chung". So sánh expert vs LLM cho checklist **chưa có nguồn nào đo bằng số** (`evidence.md` §2.3, §2.4, §3.2) — kể cả ExpertLongBench chỉ **phát biểu định tính** rằng LLM *"cannot yet replicate"* rubric expert.

---

### A9. LLM judge — pointwise (một điểm fidelity)

**Bắt được failure nào:** **KHÔNG PHÂN BIỆT ĐƯỢC GÌ.** Cho ra một số. O1 (thiếu 2 ý), O2 (bịa 1 ý), O3 (sai 1 số) đều có thể ra 3.5/5 hoặc 4/5.

**EXTERNAL EVIDENCE:** `evidence.md` §1.2 — G-Eval trên completeness chỉ đạt Pearson **0.314** so với 0.688 của keyfact alignment. Tức ngay cả khi hỏi thẳng về completeness, judge dạng rubric chung vẫn đo kém.

→ **LOẠI khỏi vai trò primary instrument của RQ02.** Không phải vì LLM judge vô dụng nói chung (RQ06 đã định vị vai trò của nó), mà vì nó **không đáp ứng yêu cầu cốt lõi của RQ02 là phân biệt 3 failure**.

### A10. LLM judge — pairwise

**Bắt được failure nào:** không phân rã failure; cho ra một preference. Cộng thêm position bias (RQ06 §4.2).

→ **Không thuộc phạm vi công cụ chính của RQ02.** Vai trò của nó (so baseline, final comparison) đã thuộc RQ06/RQ08. Ghi ở đây chỉ để đóng bảng so sánh.

### A11. Human

**Bắt được:** cả 3, và là **định nghĩa** của "đúng" cho những case biên (O4 kiểu nào là chấp nhận được).
**Giới hạn (EXTERNAL EVIDENCE, verify Wave 1):** PPTEval Fleiss κ ≈ **0.59** giữa 4 người trong đúng domain presentation; FineSurE báo trần người FRANK κ ≈ **0.58**, REALSumm α ≈ **0.66** (`evidence.md` §1.2). → **Người cũng là phép đo có nhiễu.**
**Cỡ mẫu: TBD** (G-07) — xem `recommendation.md` §4.

### A12. Hybrid xếp tầng

Không phải một evaluator mà là kiến trúc. Đây là hình thái mà FineSurE (2 task), QAFactEval (*"complementary signals"*), CLEAR (expert rubric + LLM mapper), HealthBench (người viết tiêu chí, model chấm) đều hội tụ về.

---

## 3. Bảng chấm: approach nào phân biệt được gì

Chấm trên đúng toy example §1. `✓` = phân biệt được; `~` = phát hiện nhưng **không tách được** khỏi failure khác; `✗` = mù.

| # | Approach | O1 bỏ sót | O2 bịa | O3 nói sai | Tách bịa ≠ sai? | O4 (tính toán lại) | Rẻ đủ daily? |
| --- | --- | :-: | :-: | :-: | :-: | --- | :-: |
| A1 | Deterministic (`source_ref`, number/entity provenance) | ✗ | ✓ | ✓ | ✗ | Đánh dấu vào queue riêng, **không kết tội** | **✓✓** |
| A2 | NLI/alignment — SummaC, **AlignScore** | ✗ | ~ | ~ | ✗ | **Rủi ro cao** — claim nén là điểm yếu đã được đo | ✓ |
| A3 | Grounding classifier — **MiniCheck** | ✗ | ~ | ~ | ✗ | Rủi ro trung bình — train cho claim multi-sentence | **✓✓** |
| A4 | Atomic fact — FActScore/SAFE | ✗ **(có số chứng minh)** | ~ | ~ | ✗ | Rủi ro cao + rủi ro decomposition | ✗ (decompose đắt) |
| A5 | **Keyfact/critical-fact alignment** | **✓✓** | ✗ | ✓ (nếu keyfact có giá trị) | — | Trung tính/tốt | ✓ *(nếu keyfact frozen)* |
| A6b | **Probe/QA sinh từ source** | **✓** | ✗ | **✓** | — | Tốt hơn NLI — câu hỏi định vị đúng chỗ | △ *(rẻ nếu Q frozen)* |
| A7 | Checklist LLM sinh per-instance | ✓ | **✗ (mù cấu trúc)** | ✓ | — | Trung tính | ✗ (~54 call/deck) |
| A8 | Checklist expert soạn tay | ✓ | **✗ (mù cấu trúc)** | ✓ | — | Trung tính | ✗ (chi phí authoring) |
| A9 | LLM judge pointwise | ✗ | ✗ | ✗ | ✗ | — | ✗ |
| A10 | LLM judge pairwise | ✗ | ✗ | ✗ | ✗ | — | ✗ |
| A11 | Human | ✓ | ✓ | ✓ | ✓ | Là **định nghĩa** của đúng | ✗ |
| A12 | Hybrid = **A1 + A3 + A5** | ✓ | ✓ | ✓ | ✓ *(§3.2)* | Có đường xử lý riêng | **✓** |

### 3.1 Đọc bảng — ba kết luận

1. **Không dòng đơn lẻ nào (trừ A11 và A12) có đủ ✓ ở cả 3 cột.** Đây không phải kết luận bi quan mà là **kết luận cấu trúc** đã nêu ở §0: mỗi approach chỉ duyệt một tập.
2. **Cột "Tách bịa ≠ sai" gần như trống.** Đây là **gap lớn nhất RQ02 tìm ra**: không approach tự động nào trong literature được thiết kế để tách hai failure này. Mọi metric alignment trả về nhị phân supported/not.
3. **A9/A10 trắng cả hàng.** Approach nào không phân biệt được → loại hoặc hạ priority (yêu cầu của RQ02). LLM judge pointwise/pairwise **không phải công cụ của RQ02**.

### 3.2 Cách tách "bịa" khỏi "nói sai" — đề xuất, chưa có precedent

**RECOMMENDATION (INFERENCE thuần, KHÔNG có nguồn — đánh dấu rõ).** Với mỗi claim `c` của deck bị A3 chấm `not supported`, chạy thêm **một phép hỏi rẻ** để phân loại:

```
c not supported
   │
   └─► Source có nói về CÙNG SUBJECT+ATTRIBUTE của c không?
        (retrieval: top-k đoạn source gần c nhất theo embedding,
         + kiểm tra entity/số của c có counterpart trong đoạn đó)
         │
         ├─ CÓ  → NÓI SAI (incorrectness)      vd. O3: source có nói churn, deck ghi 1.9%
         └─ KHÔNG → BỊA (hallucination)         vd. O2: source không nói gì về gọi vốn
```

Trace: `problem (§3.1 kết luận 2: không approach nào tách được) → evidence (evidence.md §1.5: MiniCheck cho nhị phân; §1.2: FineSurE có 7 error type nhưng theo trục ngôn ngữ học, và localize chỉ đạt 42.2% accuracy) → reasoning (trục phân biệt là "source có counterpart không", mà đó là một truy vấn retrieval + so khớp, không cần suy luận sâu) → recommendation (thêm một bước phân loại rẻ sau pass 1)`.

**Rủi ro phải nói rõ:** đây là thiết kế **do RQ02 đề xuất**, không có nguồn nào validate. Nó có thể sai ở case chủ đề gần nhau (deck nói "ARR" trong khi source nói "revenue" — cùng subject hay khác?). **Phải được pilot cùng human trước khi tin.** Ghi vào `recommendation.md` §3.

**Đường lùi nếu (a) không hoạt động:** dùng **NLI 3 lớp** (entail / neutral / contradict) thay vì alignment nhị phân — `contradict` ≈ nói sai, `neutral` ≈ bịa. Đây là ánh xạ **hợp lý về lý thuyết nhưng chưa được nguồn nào validate**, và nhãn `contradiction` của NLI được biết là không ổn định. Là candidate thứ hai, không phải mặc định.

---

## 4. Ba câu hỏi mà Decision Gate bắt RQ02 phải trả lời

### 4.1 G-05 — F2 nào **thực sự** usable cho Deck Agent?

**Câu trả lời ngắn: MiniCheck (A3) là candidate duy nhất đủ điều kiện đi tiếp; AlignScore hạ priority; SummaC/FActScore không dùng ở vai trò daily.**

Trace đầy đủ:

| Candidate | Verdict | Evidence quyết định |
| --- | --- | --- |
| **AlignScore** | **Hạ priority** | `evidence.md` §1.3: *"particularly unreliable across domains and perturbation types"*; **tụt điểm mạnh trên claim mật độ cao** — mà bullet của deck **về bản chất** là claim mật độ cao. Điểm cộng duy nhất (§1.4: phản ứng tốt với sửa đúng) **không bù được** |
| **SummaC (Conv/ZS)** | **Không dùng làm signal chính** | §1.4: SummaC-Conv **nhạy với sửa vô hại hơn là với sửa đúng** — đây là tính chất phá hủy toàn bộ mục đích tracking; §1.3: SummaC-ZS xếp nhóm rủi ro cao |
| **FActScore / SAFE** (A4) | **Không dùng làm signal daily** | §1.1: chứng minh bằng số rằng nó **không phân biệt** thiếu với đủ; §2.6: bước decompose thêm rủi ro trên fragment; chi phí decomposition không hợp daily |
| **MiniCheck** | **Candidate đi tiếp — có điều kiện** | §1.3: bền nhất trong 6 metric; §1.4: phản ứng đúng với sửa factual; §1.5: E1 peer-reviewed, repo chính chủ, **0 đồng API**, ~vài giây/deck. **Điều kiện:** vẫn game được (§1.4), vấp negation (§1.3), và **chưa ai áp lên slide** (§4 của `evidence.md`) |

**Trả lời trực tiếp câu hỏi G-05 đặt ra:** nhiệm vụ **không phải** "Wave 1 tìm ra AlignScore → đi tìm paper ủng hộ". Kết quả research độc lập cho thấy **AlignScore là lựa chọn kém nhất trong nhóm** đúng ở đặc tính định nghĩa domain của Deck Agent. Nếu Wave 2 đi theo Wave 1 mà không kiểm tra, team sẽ implement đúng metric yếu nhất.

**Còn case O4 (bullet tính toán lại) thì sao?**

**INFERENCE — đây là chỗ RQ02 đề xuất KHÔNG giải bằng evaluator textual.** Lập luận:
- `evidence.md` §2.1 (EQUATE, CoNLL 2019): suy luận định lượng là **trục năng lực tách rời** khỏi entailment ngôn ngữ; NLI model **không tự học** nó. ⚠ Nguồn 2019, **không** dùng để kết luận về model 2026 — nó chỉ nói rằng **không có lý do kiến trúc nào để mong đợi** một alignment model làm đúng phép chia.
- `evidence.md` §1.3: metric tệ hơn trên claim nén — O4 vừa nén vừa tính.
- `evidence.md` §3.4 (VeriFact, [SEARCH-ONLY]): dạng lỗi *missing comparandum* — B′ mất mốc so sánh (51% so với **năm nào**?) — là dạng đã được nhận diện là khó.

→ **RECOMMENDATION:** đừng bắt evaluator textual làm số học. Thay vào đó **cô lập nó bằng A1**: mọi số trên deck không khớp verbatim với source rơi vào **`derived_number_queue`** — một danh sách nhỏ, đếm được, mà (a) daily chỉ **đếm size** (`derived_number_rate` — một signal regression hợp lệ: tăng đột biến = ContentPlanner đang tự chế số), và (b) milestone thì **người soát tay** vì danh sách nhỏ.

Trace: `problem (O4 là output đúng mà evaluator dễ đánh sai) → evidence (EQUATE + Stress Testing) → reasoning (thay vì yêu cầu evaluator có năng lực nó không có, hãy tách case đó ra thành một tập nhỏ và đếm được) → recommendation (derived_number_queue)`.
**Giá phải trả, nói thẳng:** cách này **không** phân biệt "51% tính đúng" với "51% tính sai" một cách tự động. Nó chỉ đảm bảo case đó **không bị đếm nhầm thành hallucination** và **không bị bỏ qua**. Là một đánh đổi có ý thức, không phải một giải pháp đầy đủ.

### 4.2 G-06 — cách **tạo** per-instance criteria (không mặc định expert viết tay)

**PROJECT SOURCE:** NFR-53 đề xuất *checklist theo instance + human corr. trên mẫu con*. Theo G-06 đây là **internal candidate đưa vào so sánh**, không phải preferred method.

**EXTERNAL EVIDENCE — taxonomy (`evidence.md` §2.3, survey E2):** 3 paradigm — human expert / automated LLM (5 nhánh: deductive, inductive, transfer-based, on-the-fly, query-adaptive) / human-in-the-loop.

**So sánh 6 cách tạo criteria cho Deck Agent:**

| Cách tạo | Sinh ra cái gì | Bắt được failure nào | Chi phí authoring | Ổn định giữa các run | Precedent |
| --- | --- | --- | --- | --- | --- |
| **Expert viết tay** (PresentBench, HealthBench) | Checklist nhị phân/instance | Bỏ sót ✓, sai ✓, **bịa ✗** | **Rất cao** (54.1 item × N) | Cao (cố định sau khi viết) | E1, Wave 1 |
| **LLM sinh checklist** (TICK) | Câu hỏi YES/NO từ instruction/source | Bỏ sót ✓, sai ✓, **bịa ✗** | Thấp | **Thấp nếu sinh lại mỗi run** — xem cảnh báo dưới | E1, Wave 1 (+5.8pp) |
| **Automatic critical-fact extraction** (FineSurE keyfact) | Danh sách ý quan trọng có giá trị | Bỏ sót ✓, sai ✓, bịa ✗ | Thấp (one-time/case) | Cao **nếu đóng băng vào benchmark** | **E1 — `evidence.md` §1.2, có số 0.688** |
| **Question generation từ source** (A6b) | Bộ câu hỏi + đáp án chuẩn | Bỏ sót ✓, sai ✓, bịa ✗ | Trung bình | Cao nếu đóng băng | E1 QAFactEval; LongSumEval [DISCOVERY] |
| **Atomic fact từ output** (FActScore) | Mệnh đề từ **output**, đổi mỗi run | **Bịa ~, sai ~, bỏ sót ✗** | ≈0 nhưng per-run | **Thấp** — tập criteria đổi theo output | E1, Wave 1 |
| **NLI probe** (A2/A3) | Không có criteria tường minh | Bịa ~, sai ~, bỏ sót ✗ | 0 | **Cao nhất** | E1 |

**INFERENCE #1 — trục quan trọng nhất không phải "ai viết", mà "criteria có ĐÓNG BĂNG được không".**
Criteria sinh **từ output** (FActScore, atomic fact) thay đổi mỗi lần Deck Agent đổi output → **thước đo tự co giãn theo vật cần đo**. Criteria sinh **từ source** (keyfact, question) là hàm của benchmark case, sinh một lần, lưu vào benchmark, **không đổi giữa các version**. Với mission "hôm nay có tốt hơn hôm qua không", tính chất này **quan trọng hơn** chất lượng criteria: một bộ criteria hơi lệch nhưng cố định vẫn đo được xu hướng; một bộ criteria hoàn hảo nhưng đổi mỗi run thì không.

→ **RECOMMENDATION:** dù chọn cách tạo nào, criteria **phải được sinh một lần, review, rồi lưu như DỮ LIỆU của benchmark** (cùng chỗ với `critical_facts` mà `EVALUATION_MISSION.md` đã phác), không sinh lại lúc chạy. Đây là yêu cầu **mạnh hơn** "đóng băng judge prompt" của RQ06 §C4 — ở đó đóng băng câu hỏi, ở đây đóng băng **cả bộ tiêu chí**. Route sang RQ07.

**INFERENCE #2 — mô hình 2 lớp là phần khả thi nhất, và nó có precedent nhất quán.** `evidence.md` §2.4 (CLEAR): expert viết **rubric**, LLM làm **mapper**. HealthBench (Wave 1): bác sĩ viết **criteria**, model **chấm**. Áp cho Deck Agent: người **không** viết 54 item/PDF; người **duyệt** một danh sách ~10–20 critical fact do LLM trích, rồi model làm việc đối chiếu. Chi phí người: đọc + sửa một danh sách ngắn cho mỗi benchmark case — làm một lần cho toàn bộ vòng đời benchmark.

**INFERENCE #3 — điều KHÔNG kết luận được.** Khoảng cách chất lượng giữa criteria người viết và criteria LLM sinh **chưa có nguồn nào đo bằng số** (`evidence.md` §2.3, §2.4, §3.2). ExpertLongBench chỉ **phát biểu** rằng LLM *"cannot yet replicate"*. Đây vẫn là U-4 của RQ06, **RQ02 không đóng được**.

**Hệ quả cho NFR-53 (PROJECT SOURCE, cần team đọc):** NFR-53 như đang viết có **một lỗ hổng cụ thể** — checklist per-instance **không bắt được hallucination** (§A7). §8 gọi tên "không bịa" là mục tiêu nhưng method được đề xuất không cover nó. Đây là finding có thể hành động ngay, độc lập với việc chọn F2 nào.

### 4.3 Cái gì chạy được **hằng ngày**?

Tiêu chí: chi phí ≈ 0, latency giây, **lặp lại được**, và **cho tín hiệu tách được theo failure**.

| Tier | Chạy gì | Ra số gì | Chi phí |
| --- | --- | --- | --- |
| **Mỗi commit** | A1 deterministic | `traceability_rate`, `verbatim_number_rate`, `unmatched_number_count`, `unmatched_entity_count` | ≈0, ms |
| **Mỗi commit / PR** | A3 chiều 1 (MiniCheck, bullet = claim unit) | `unsupported_bullet_rate` | 0đ API, ~giây/deck (GPU) |
| **Mỗi commit / PR** | A3 chiều 2 (cùng model, premise = text deck, hypothesis = từng `critical_fact` đã frozen) | `critical_fact_coverage` | Cùng bậc chi phí |
| **Mỗi PR** | §3.2 phân loại unsupported → bịa / sai | `hallucination_count`, `incorrectness_count` | Rẻ (retrieval + so khớp) |
| Nightly | A6b probe (nếu bộ câu hỏi đã frozen) | `probe_answerable_rate`, `probe_correct_rate` | Trung bình |
| Milestone | A7/A8 checklist + A11 human | Evidence bảo vệ | Cao |

**INFERENCE — điểm đắt giá nhất của thiết kế này:** chiều 2 (coverage) dùng **đúng cùng một model** với chiều 1, chỉ **đảo vai premise/hypothesis** (premise = toàn bộ text của deck, hypothesis = một `critical_fact`). Nghĩa là **thêm coverage không thêm hạ tầng**, chỉ thêm ~10–20 lần gọi model nhỏ mỗi deck. Đây là lý do kỹ thuật khiến bộ đo 3-failure vẫn nằm trong ngân sách daily.

⚠ **Điều kiện chưa được validate:** MiniCheck được train với **document dài làm premise**. Dùng **text của deck (ngắn, dạng bullet) làm premise** là một chế độ sử dụng **ngoài phân phối huấn luyện**. Không có nguồn nào xác nhận nó hoạt động. **Đây là rủi ro số 1 của toàn bộ đề xuất** → phải nằm trong pilot (`recommendation.md` §3).

---

## 5. Noise floor / repeatability (G-09) — bắt buộc cho mọi model-based evaluator

**EXTERNAL EVIDENCE, negative (`evidence.md` §4 #2):** không tìm thấy nguồn nào báo cáo noise floor cho F2 metric. Nguồn duy nhất về repeatability (RQ06 §3.6, Tamba 2026) nói về **LLM judge qua API**, không phải encoder model local. → Phần dưới là **RECOMMENDATION suy ra từ tính chất kỹ thuật**, không có precedent.

**INFERENCE — nguồn nhiễu của mỗi tầng khác nhau, và chỗ nguy hiểm không nằm ở chỗ người ta nghĩ:**

| Thành phần | Nguồn nhiễu | Kỳ vọng |
| --- | --- | --- |
| A1 deterministic | Không có | **Bit-identical.** Nếu không identical → có bug, không phải noise |
| A3 model (weights pinned, greedy/classifier) | Non-determinism phía GPU (thứ tự reduction floating-point), thay đổi batch size, đổi phiên bản thư viện | Rất nhỏ nhưng **không giả định bằng 0** |
| **Bước sinh criteria bằng LLM** | **Sampling của LLM** — nguồn nhiễu **LỚN NHẤT** trong cả pipeline | **Triệt tiêu bằng cách đóng băng criteria thành dữ liệu**, không phải bằng cách pin temperature |
| A6/A7 dùng LLM lúc chạy | Toàn bộ nhiễu LLM judge (RQ06 §4.5) | Phải đo, không được giả định |

**RECOMMENDATION — protocol đo noise floor, 3 phép đo, làm trước khi dùng bất kỳ số nào để so version:**

1. **Within-run repeatability.** Chạy **cùng một deck** qua **cùng pipeline evaluator** `N ≥ 5` lần. Kỳ vọng cho A1: hoàn toàn giống nhau. Cho A3: đo spread. Báo cáo `noise_floor = max(spread)` cho từng metric.
   → **Quy tắc đọc kết quả: delta giữa 2 version nhỏ hơn noise floor thì KHÔNG được gọi là improvement.**
2. **Criteria stability** (phép đo mà RQ06 không có, riêng của RQ02). Sinh bộ `critical_facts` cho **cùng một source** **2 lần độc lập**, đo mức trùng (vd. Jaccard trên tập fact sau khi khớp ngữ nghĩa). Nếu thấp → **cấm** sinh criteria lúc runtime; bắt buộc đóng băng. Con số này cũng là **ước lượng trực tiếp** cho câu hỏi U-4 (LLM sinh criteria có đáng tin không) trên chính dữ liệu của team.
3. **Cross-version sanity (đo độ nhạy, không phải nhiễu).** Chạy evaluator trên một cặp deck **cố ý** khác nhau đúng 1 failure (lấy đúng O1/O2/O3 của §1, dựng tay). Nếu evaluator không tách được **trên case dựng sẵn**, nó chắc chắn không tách được trên dữ liệu thật. **Đây là smoke test rẻ nhất và phải chạy trước mọi thứ khác.**

**INFERENCE:** phép đo (3) là đóng góp riêng của RQ02 và nó rẻ tới mức phi lý so với giá trị: 3 deck dựng tay, chạy một lần, cho biết ngay instrument có hoạt động không — **trước khi** team đầu tư vào benchmark, human study, hay pilot.

---

## 6. Khoảng cách domain — điều landscape này KHÔNG chứng minh được

**Ghi rõ để không ai đọc file này như một kết luận đã chắc:**

1. **Không nguồn nào áp F2 lên slide deck** (`evidence.md` §4, đã search độc lập lần 2 với query khác Wave 1). U-2 của RQ06 **vẫn mở**. Mọi thứ ở §4.3 là **INFERENCE về khả năng chuyển giao**.
2. **Bullet ≠ câu.** Mọi metric ở A2/A3/A4 được validate trên **câu đầy đủ**. Bullet mất chủ ngữ/động từ/mốc thời gian. `evidence.md` §2.6 đặt tên vấn đề nhưng không giải cho trường hợp này.
3. **Deck bị phân mảnh qua nhiều slide.** Một ý có thể trải trên slide 3 và slide 7. Chiều 2 (coverage) dùng **toàn bộ text deck** làm premise nên chịu được; chiều 1 thì mỗi bullet đứng một mình → **mất context liên slide**. Chưa có nguồn nào xử lý.
4. **Source có bảng và hình.** Mọi thứ ở đây là **text ↔ text**. Nếu ý quan trọng nằm trong một biểu đồ của PDF, không approach nào ở §2 chạm tới. **Ngoài phạm vi RQ02**, nhưng phải ghi vì nó giới hạn ý nghĩa của `critical_fact_coverage`.
5. **Vị trí trong PDF ảnh hưởng phép đo.** `evidence.md` §2.7 (NAACL 2025): faithfulness theo vị trí có dạng **chữ U**, ý ở giữa tài liệu bị đo kém hơn. Nếu evaluator dựa vào LLM đọc cả PDF, điểm sẽ phụ thuộc **vị trí của ý** — một confounder không liên quan gì tới ContentPlanner. Đây là lập luận thêm cho việc **đối chiếu theo item** thay vì nhét cả PDF vào một prompt.
6. **Mâu thuẫn evidence chưa giải quyết.** `evidence.md` §2.5 báo rằng metric **end-to-end đơn giản** (hỏi thẳng LLM chỗ nào thiếu) hiệu quả bất ngờ — **ngược chiều** với FineSurE (G-Eval 0.314) và AbsenceBench (69.6% F1). RQ02 nghiêng về nhóm sau (2 nguồn, phương pháp độc lập, một trong đó peer-reviewed ACL) nhưng **không** coi là đã đóng.
</content>
