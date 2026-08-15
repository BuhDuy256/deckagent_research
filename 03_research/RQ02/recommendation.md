# RQ02 — Recommendation: đo bịa / sai / bỏ sót cho Deck Agent

**Ngày research:** 2026-08-15
**Đọc trước:** `landscape.md` (toy example + bảng chấm approach), `evidence.md` (nguồn + evidence level)
**Trạng thái:** RESEARCH OUTPUT — **không phải decision**. Mọi đề xuất ở đây là candidate chờ team/giảng viên review. Xem §4.

---

## A. Ba kết luận làm thay đổi cách đặt vấn đề (đọc trước khi đọc §1)

### A1. Ba failure nằm trên **hai chiều xác minh ngược nhau**, không trên một thang

**INFERENCE + EXTERNAL EVIDENCE.** "Bịa" và "nói sai" là failure của **tập claim trên deck** (duyệt deck → tìm chống đỡ trong source). "Bỏ sót" là failure của **tập ý quan trọng trong source** (duyệt source → tìm trên deck). Một phép đo chạy một chiều **không thể** thấy failure của chiều kia — đây là giới hạn của **định nghĩa**, không phải của chất lượng model.

Bằng chứng số (`evidence.md` §1.1, *All Claims Are Equal*, 2025): response **thiếu hẳn câu trả lời chính** ăn **FActScore 82.75%**; response **đúng và đủ** chỉ **82.58%**. Metric precision-only không chỉ mù với bỏ sót — nó có thể **chấm cao hơn** cho bản bỏ sót.

**Hệ quả:** mọi câu hỏi dạng "nên chọn metric fidelity nào" đều đặt sai vấn đề. Câu hỏi đúng là "**hai pass nào**, chạy chiều nào".

### A2. Ranh giới bịa/sai là "source có counterpart hay không" — và **không metric nào hiện có tách được**

Mọi metric alignment (AlignScore, SummaC, MiniCheck) trả về **nhị phân supported/not supported**; cả hai failure rơi vào cùng một ô. FineSurE có 7 error type nhưng theo trục **ngôn ngữ học** (entity/predicate/circumstance…), không theo trục "source có nói về thứ đó không" — và ngay cả nó cũng chỉ đạt **42.2% accuracy khi phải nói loại lỗi nào**, dù đạt 86.4% khi chỉ detect có/không (`evidence.md` §1.2).

→ Việc tách bịa/sai phải là **một bước thiết kế thêm**, và bước đó **chưa có precedent** (`landscape.md` §3.2). Đây là chỗ RQ02 đề xuất cái mới, và cũng là chỗ dễ sai nhất.

### A3. Coverage phải được hỏi dưới dạng **danh sách đóng**, không phải câu hỏi mở

Hai nguồn độc lập về phương pháp chỉ cùng một hướng:
- FineSurE (ACL 2024): completeness đo bằng **keyfact alignment** đạt Pearson **0.688**; hỏi LLM judge (G-Eval) chỉ **0.314**; UniEval **0.134**.
- AbsenceBench (2025): model tốt nhất chỉ **69.6% F1** ở task phát hiện phần bị xóa — *dù được cấp cả bản gốc lẫn bản đã xóa*, tức dễ hơn hẳn bài toán của Deck Agent. Cơ chế: attention *"cannot easily attend to 'gaps'"*.

→ Prompt kiểu *"deck này có bỏ sót ý quan trọng nào không?"* là hướng yếu **có tính cấu trúc**. Đây là lý do kỹ thuật thật khiến per-instance criteria (G-06) bắt buộc cho coverage — mạnh hơn lý do "material-dependent" mà Wave 1 nêu.

⚠ **Có một nguồn đi ngược:** `evidence.md` §2.5 báo metric end-to-end đơn giản hiệu quả bất ngờ. Chưa trích xuất được số của nguồn đó → RQ02 nghiêng về nhóm 2 nguồn trên nhưng **không coi là đã đóng** (§3).

---

## B. Bộ đo đề xuất — 3 phép đo, 2 chiều, 1 quy tắc phân loại

**RECOMMENDATION.** Trace: `problem (§A1–A3 + G-02) → evidence (landscape.md §2–§3) → reasoning → recommendation`.

```
 SOURCE PDF                                            DECK OUTPUT
     │                                                      │
     │  ┌────────── CHIỀU 2: coverage ──────────────────────┤
     │  │  premise = toàn bộ text deck                       │
     │  │  hypothesis = từng critical_fact (đã FROZEN)       │
     │  │  → critical_fact_coverage                          │
     │  │  → BỎ SÓT = các fact không được entail             │
     │  │                                                    │
     ├──┘                                                    │
     │                                                       │
     │  ┌────────── CHIỀU 1: precision ─────────────────────┐│
     │  │  premise = source     hypothesis = từng bullet    ││
     │  │  → unsupported_bullet_rate                        ││
     │  │         │                                         ││
     │  │         └─► phân loại (§3.2 landscape):           ││
     │  │              source có counterpart? ─ CÓ → NÓI SAI││
     │  │                                     └ KHÔNG→ BỊA  ││
     │  └───────────────────────────────────────────────────┘│
     │                                                       │
     └────────── TẦNG 0: deterministic (chạy trước) ─────────┘
          traceability_rate · verbatim_number_rate
          unmatched_number → derived_number_queue
```

**Ba tầng, mỗi tầng chỉ nhận phần tầng dưới không giải được** — cùng hình thái F6 hybrid mà RQ06 kết luận, nhưng ở đây được cụ thể hoá theo failure thay vì theo dimension.

---

# 1. Recommendation

**RECOMMENDATION — đề xuất, chưa phải quyết định.**

## 1.1 Phép đo nào cho failure nào

| Failure | Phép đo đề xuất | Family | Vì sao (evidence) |
| --- | --- | --- | --- |
| **Bỏ sót** (coverage gap) | `critical_fact_coverage` = tỷ lệ `critical_facts` (frozen trong benchmark) được entail bởi text của deck | A5 keyfact alignment, chạy bằng model của A3 **đảo chiều** | FineSurE: 0.688 vs G-Eval 0.314 (`evidence.md` §1.2). Không approach nào khác đo trực tiếp được (`landscape.md` §3) |
| **Bịa** (hallucination) | `hallucination_count` = số bullet `not supported` **và** không tìm được counterpart trong source | A3 (MiniCheck) + bước phân loại §3.2 | MiniCheck bền nhất trong 6 metric stress-test (`evidence.md` §1.3), phản ứng đúng với sửa factual (§1.4) |
| **Nói sai** (incorrectness) | `incorrectness_count` = số bullet `not supported` **và** có counterpart trong source · **cộng với** `unmatched_number_count` từ tầng deterministic | A1 + A3 + bước phân loại | Số sai là lớp con nguy hiểm nhất và A1 bắt được **miễn phí**; NLI được biết là yếu nhất đúng ở suy luận định lượng (`evidence.md` §2.1) |
| **Case bullet tính toán lại** (O4) | **Không giao cho evaluator textual.** Cô lập thành `derived_number_queue`; daily chỉ theo dõi `derived_number_rate` | A1 | EQUATE + Stress Testing (`landscape.md` §4.1) |
| **Traceability** (FR-03) | `traceability_rate` — `source_ref` tồn tại và trỏ đúng | A1 deterministic | PROJECT SOURCE FR-03; đây là **correctness requirement**, không phải AI-quality — không trộn vào 3 số trên |

**Nguyên tắc bắt buộc kèm theo:** ba con số này **KHÔNG được cộng lại thành một "Content Fidelity score"**. Đó chính là thứ G-02 vừa tách ra. Báo cáo dưới dạng vector 3 chiều.

## 1.2 Cái nào chạy **daily**, cái nào chỉ **milestone**

| Cadence | Chạy gì | Ra số gì | Chi phí |
| --- | --- | --- | --- |
| **Mỗi commit** (Tier 0) | A1 deterministic | `traceability_rate`, `verbatim_number_rate`, `unmatched_number_count`, `derived_number_rate` | ≈0đ, ms. **Noise floor = 0 theo định nghĩa** |
| **Mỗi commit / PR** (Tier 1) | A3 hai chiều — cùng một model MiniCheck (~770M, local) | `unsupported_bullet_rate`, `critical_fact_coverage` | **0đ API**; repo chính chủ báo 29K claim / 30 phút trên 1 GPU A6000 → **INFERENCE:** ~vài giây/deck |
| **Mỗi PR** (Tier 1) | Bước phân loại bịa/sai (`landscape.md` §3.2) | `hallucination_count`, `incorrectness_count` | Rẻ (retrieval + so khớp) |
| Nightly (Tier 2) | A6b probe/QA sinh từ source (nếu bộ câu hỏi đã frozen) | `probe_answerable_rate`, `probe_correct_rate` | Trung bình |
| Milestone (Tier 3) | A7/A8 checklist per-instance + human trên mẫu con | Evidence cho báo cáo/bảo vệ | Cao. **Cỡ mẫu = TBD (§4, P-05)** |

**Signal daily được chọn: `unsupported_bullet_rate` + `critical_fact_coverage` chạy bằng MiniCheck local, cộng tầng deterministic A1.** Đây là vế (b) bắt buộc của RQ02.

**INFERENCE — điểm khiến nó khả thi:** coverage dùng **đúng cùng một model** với precision, chỉ đảo vai premise/hypothesis. Thêm coverage **không thêm hạ tầng**, chỉ thêm ~10–20 lần gọi model nhỏ mỗi deck.

## 1.3 F2 nào **thực sự** usable (trả lời G-05)

| Candidate | Verdict | Lý do quyết định |
| --- | --- | --- |
| **AlignScore** | **HẠ PRIORITY — không dùng làm signal chính** | `evidence.md` §1.3: *"particularly unreliable across domains and perturbation types"*; **tụt điểm mạnh trên claim mật độ thông tin cao** — mà bullet của Deck Agent *về bản chất* là claim nén |
| **SummaC (Conv/ZS)** | **KHÔNG dùng** | §1.4: SummaC-Conv **nhạy với sửa vô hại hơn là với sửa đúng** — tính chất phá hủy toàn bộ mục đích tracking |
| **FActScore / SAFE** | **KHÔNG dùng ở vai trò daily** | §1.1: chứng minh bằng số rằng nó không phân biệt thiếu với đủ; bước decompose vừa đắt vừa thêm lỗi trên fragment (§2.6) |
| **MiniCheck** | **CANDIDATE ĐI TIẾP — có điều kiện pilot** | §1.3 bền nhất trong 6 metric; §1.4 phản ứng đúng với sửa factual; §1.5 E1 peer-reviewed + repo + 0đ API + huấn luyện cho claim **tổng hợp nhiều câu nguồn** |

**Phải nói thẳng về G-05:** kết quả research độc lập cho thấy **AlignScore là lựa chọn kém nhất trong nhóm** đúng ở đặc tính định nghĩa domain của Deck Agent. Nếu Wave 2 đi theo tên metric mà Wave 1 nêu, team sẽ implement đúng metric yếu nhất. Đây là lý do gate G-05 tồn tại.

**Điều kiện đi kèm — MiniCheck vẫn có 3 rủi ro đã biết:**
1. **Game được** bằng cách chèn câu nguyên văn từ source (`evidence.md` §1.4, áp dụng cho cả 6 metric). **INFERENCE:** với Deck Agent đây là rủi ro *cụ thể*, vì "copy nguyên đoạn PDF lên slide" vừa là hành vi mà ContentPlanner tệ sẽ sinh ra, vừa là thứ được metric thưởng, vừa là thứ FR-22 coi là lỗi. → **Bắt buộc** đọc `unsupported_bullet_rate` **cùng với** một chỉ số nén (vd. tỷ lệ ký tự deck/source) — không đọc một mình.
2. **Vấp negation tương đương logic** (§1.3).
3. **Chưa ai áp lên slide deck** (`evidence.md` §4, negative finding sau 2 vòng search độc lập).

## 1.4 Cách **tạo** per-instance criteria (trả lời G-06)

**Đề xuất: automatic critical-fact extraction + human review một lần + đóng băng vào benchmark.** Cụ thể: LLM trích ~10–20 `critical_facts` **từ source** cho mỗi benchmark case → một người trong team đọc và sửa (bổ sung/xóa/sửa giá trị) → lưu vào metadata của case → **không sinh lại lúc chạy**.

**Trace:**
- `problem`: G-06 nói cách tạo criteria vẫn mở; NFR-53 (PROJECT SOURCE) là candidate, không phải preferred method.
- `evidence`: (a) FineSurE cho phép keyfact *"provided by humans; if unavailable… automatically derived"* và đo được completeness 0.688 (`evidence.md` §1.2); (b) mô hình 2 lớp "người định nghĩa tiêu chí — model đối chiếu" có precedent nhất quán ở CLEAR/ExpertLongBench (§2.4) và HealthBench (Wave 1); (c) survey rubric (§2.3) xếp human-in-the-loop là paradigm cân bằng chi phí/chất lượng; (d) chi phí expert-authored kiểu PresentBench (54.1 item × 238 instance) **ngoài tầm đồ án**.
- `reasoning`: **trục quyết định không phải "ai viết" mà là "criteria có đóng băng được không"**. Criteria sinh từ **output** (atomic fact) đổi mỗi run → thước đo co giãn theo vật cần đo. Criteria sinh từ **source** là hàm của benchmark case → cố định qua mọi version. Với mission "hôm nay tốt hơn hôm qua không", tính cố định **quan trọng hơn** chất lượng tuyệt đối.
- `recommendation`: như trên. Chi phí người ≈ đọc/sửa một danh sách ngắn, **một lần cho toàn bộ vòng đời benchmark**.

**Yêu cầu vận hành kèm theo (mạnh hơn "đóng băng judge prompt" của RQ06):** đóng băng **cả bộ tiêu chí** như dữ liệu của benchmark, không chỉ đóng băng câu hỏi. → Route sang RQ07.

**Hệ quả cho NFR-53 mà team nên biết ngay:** checklist per-instance **không bắt được hallucination** — nó là danh sách đóng các thứ *phải có*, còn bịa là tập mở các thứ *không được có* (`landscape.md` §A7). §8 gọi tên "không bịa" là mục tiêu nhưng method NFR-53 đề xuất **không cover nó**. Finding này hành động được ngay, độc lập với việc chọn F2 nào.

## 1.5 Cách đo noise floor (trả lời G-09)

**Không có precedent** cho F2 metric (`evidence.md` §4 #2) → đây là RECOMMENDATION suy ra từ tính chất kỹ thuật. Ba phép đo, chạy **trước** khi dùng bất kỳ số nào để so version:

1. **Within-run repeatability** — cùng deck, cùng pipeline, `N ≥ 5` lần. A1 phải **bit-identical** (khác đi = bug). A3: đo spread, ghi `noise_floor`. **Quy tắc đọc:** delta < noise floor **không được gọi là improvement**.
2. **Criteria stability** — sinh `critical_facts` cho cùng một source **2 lần độc lập**, đo mức trùng. Thấp → **cấm** sinh criteria lúc runtime. Con số này cũng là ước lượng trực tiếp cho U-4 của RQ06 trên chính dữ liệu của team.
3. **Discrimination smoke test** — dựng tay đúng 3 deck O1/O2/O3 của `landscape.md` §1 và chạy evaluator. Nếu không tách được **trên case dựng sẵn**, chắc chắn không tách được trên dữ liệu thật.

**RECOMMENDATION về thứ tự:** làm (3) **trước tiên** — 3 deck dựng tay, chạy một lần, cho biết ngay instrument có hoạt động không, **trước khi** đầu tư vào benchmark, human study, hay pilot. Đây là phép thử rẻ nhất trong toàn bộ RQ02.

## 1.6 Cái gì bị **loại hoặc hạ priority** (yêu cầu của RQ02)

| Approach | Xử lý | Vì sao |
| --- | --- | --- |
| **LLM judge pointwise (một điểm fidelity)** | **LOẠI khỏi vai trò instrument của RQ02** | Không phân biệt được failure nào. O1/O2/O3 đều có thể ra cùng một điểm. G-Eval completeness chỉ 0.314 |
| **LLM judge pairwise** | Ngoài phạm vi RQ02 | Không phân rã failure; vai trò của nó (so baseline) thuộc RQ06/RQ08 |
| **AlignScore, SummaC** | Hạ priority / không dùng | §1.3 |
| **Atomic fact decomposition trên bullet** | Hạ priority | Bullet đã là fragment cực đoan; decompose thêm tạo mệnh đề không verify độc lập được (`evidence.md` §2.6) + tốn call. **Đề xuất: bullet = đơn vị verify, không decompose** |
| **Checklist per-instance làm công cụ bắt hallucination** | Loại khỏi *vai trò đó* | Điểm mù cấu trúc (`landscape.md` §A7). Vẫn giữ cho coverage/correctness ở milestone |
| **Hỏi thẳng LLM "có bỏ sót gì không"** | Hạ priority | §A3 — 2 nguồn độc lập; ⚠ 1 nguồn đi ngược, chưa đóng |

---

# 2. Confidence level

| Kết luận | Confidence | Cơ sở / điều làm nó lung lay |
| --- | --- | --- |
| Precision-only metric **không** thấy coverage gap → cần 2 pass ngược chiều | **Cao** | `evidence.md` §1.1 có bảng số trực tiếp (missing 82.75 > normal 82.58); §1.2 xác nhận độc lập bằng khoảng cách 0.688 vs 0.314. Đây là **lập luận cấu trúc**, ít phụ thuộc một nguồn |
| Coverage phải hỏi dưới dạng danh sách đóng, không hỏi mở | **Trung bình–cao** | 2 nguồn độc lập về phương pháp (FineSurE ACL 2024 peer-reviewed + AbsenceBench). ⚠ **Bị `evidence.md` §2.5 phản bác một phần** — nguồn đó chưa trích xuất được số |
| Không nên gộp 3 failure thành một score | **Cao** | G-02 đã ACCEPT; RQ02 chỉ thêm bằng chứng số cho nó |
| **AlignScore hạ priority** | **Trung bình** | §1.3 nói thẳng, nhưng là **preprint 3 tác giả, chưa peer-review**, và §1.4 lại cho AlignScore điểm tốt ở phản ứng với sửa đúng → **hai nguồn không hoàn toàn cùng chiều** |
| **SummaC-Conv không dùng** | **Trung bình–cao** | §1.4 là thí nghiệm trực tiếp, và tính chất "nhạy với sửa vô hại hơn sửa đúng" phá hủy đúng mục đích tracking — hệ quả rõ ràng không cần suy diễn |
| **MiniCheck là F2 candidate tốt nhất** | **Trung bình** | §1.5 E1 peer-reviewed + repo chính chủ; §1.3 xếp bền nhất. Nhưng vẫn game được, vấp negation, và **chưa ai áp lên slide** |
| Mọi F2 game được bằng chèn câu từ source | **Trung bình–cao** | §1.4, một nguồn nhưng thí nghiệm trực tiếp trên 6 metric, kết quả nhất quán |
| Metric kém đi trên claim **bị nén** (rất quan trọng cho deck) | **Trung bình** | §1.3, **một nguồn duy nhất**, preprint. Nếu sai thì phần lớn lập luận hạ priority AlignScore yếu đi |
| **Không giao suy luận số học cho evaluator textual** | **Trung bình** | §2.1 (EQUATE) là E1 peer-reviewed **nhưng 2019**, test model thế hệ cũ. Điều nó support đúng mức là *lý do kiến trúc*, không phải kết luận về model 2026. **Chưa ai đo lại** |
| Bullet = đơn vị verify, không decompose thêm | **Trung bình–thấp** | Dựa trên §2.6 ([SEARCH-ONLY], chưa fetch bản gốc) + INFERENCE. Hợp lý nhưng **chưa có ai test trên bullet slide** |
| **Bước phân loại bịa/sai** (`landscape.md` §3.2) | **Thấp** | **INFERENCE thuần, KHÔNG có nguồn nào.** Là đóng góp riêng của RQ02 và cũng là chỗ dễ sai nhất. Phải pilot trước khi tin |
| criteria phải đóng băng thành dữ liệu | **Trung bình–cao** | Lập luận cấu trúc (thước đo không được co giãn theo vật đo) + cùng hướng với RQ06 §C4. Không nguồn nào phát biểu thẳng |
| Automatic extraction + human review là cách tạo criteria đúng cho đồ án | **Trung bình** | Precedent nhất quán cho mô hình 2 lớp (CLEAR, HealthBench, FineSurE). ⚠ **Khoảng cách chất lượng người-viết vs LLM-sinh chưa có nguồn nào đo bằng số** |
| Chi phí đủ rẻ cho daily | **Trung bình** | Số tốc độ từ repo chính chủ nhưng gắn với GPU A6000 — **chưa xác nhận team có hạ tầng tương đương**. Nếu chạy CPU, con số này sai |
| **F2 chuyển giao được sang slide deck** | **THẤP** | `evidence.md` §4: **0 nguồn**. Thuần INFERENCE. Đây là giả định nền của toàn bộ §1.2 |
| Dùng deck ngắn làm premise cho MiniCheck (chiều 2) | **Thấp** | Chế độ dùng **ngoài phân phối huấn luyện** (model train với document dài làm premise). **Rủi ro số 1 của cả đề xuất** |

**Tổng thể:** confidence **cao** cho phần *chẩn đoán* (3 failure cần 2 chiều; precision-only mù với coverage; AlignScore/SummaC không phải lựa chọn tốt); confidence **trung bình–thấp** cho phần *đơn thuốc* (MiniCheck 2 chiều + bước phân loại). Phần đơn thuốc **phải qua pilot** trước khi vào `06_design/`.

---

# 3. Unresolved questions

**U-1. MiniCheck có hoạt động khi premise là TEXT CỦA DECK (ngắn, dạng bullet) không?**
Đây là rủi ro lớn nhất của đề xuất. Chiều 2 (coverage) đảo vai premise/hypothesis, đưa model vào chế độ ngoài phân phối huấn luyện. **Không có nguồn nào.** Nếu không hoạt động, phải tìm cơ chế coverage khác (A6b probe là đường lùi) và toàn bộ lập luận "thêm coverage không thêm hạ tầng" sụp.

**U-2. Bước phân loại bịa/sai (`landscape.md` §3.2) có chính xác không?**
INFERENCE thuần, không precedent. Case biên rõ: deck nói "ARR" trong khi source nói "revenue" — cùng subject hay khác? Cần đối chiếu với nhãn người trên một mẫu nhỏ. Đường lùi: NLI 3 lớp (`contradict` ≈ sai / `neutral` ≈ bịa) — cũng chưa được validate.

**U-3. F2 có chuyển giao sang slide deck không? (U-2 của RQ06, VẪN MỞ)**
RQ02 đã search độc lập lần 2 với query khác Wave 1 → **vẫn 0 nguồn** (`evidence.md` §4). Kể cả một paper document→slide chuyên biệt (§3.3) cũng không dùng nhóm metric này và đo "coverage" bằng embedding similarity — cách yếu hơn hẳn. **RQ02 không đóng được câu hỏi này bằng literature; chỉ đóng được bằng pilot.**

**U-4. NLI/alignment model **hiện đại** (2024–2026) làm được suy luận định lượng tới đâu?**
EQUATE là 2019 với 9 model thế hệ cũ. **Chưa ai đo lại.** Nếu model hiện đại thật sự làm được, `derived_number_queue` là biện pháp thừa và có thể bỏ. Nếu không, nó là biện pháp bắt buộc. Đây là câu hỏi **rẻ để tự trả lời**: dựng 20–30 cặp (source có 2 con số, bullet nêu % thay đổi), chạy MiniCheck, đếm.

**U-5. Coverage nên đo "hiện diện chủ đề" hay "hiện diện đúng"?** (`landscape.md` §1.4)
RQ02 nghiêng về **hiện diện chủ đề** (giữ coverage độc lập với correctness), nhưng đây là quyết định thiết kế ảnh hưởng schema `critical_facts` → phải chốt **trước** RQ07.

**U-6. Mâu thuẫn evidence về coverage.** `evidence.md` §2.5 báo metric end-to-end (hỏi thẳng LLM) hiệu quả bất ngờ, ngược chiều FineSurE + AbsenceBench. Chưa trích xuất được số của §2.5 → **chưa giải quyết được**. Nếu §2.5 đúng, phần lớn lập luận §A3 phải xét lại.

**U-7. Khoảng cách chất lượng giữa criteria người viết và criteria LLM sinh là bao nhiêu?**
Vẫn là U-4 của RQ06, **RQ02 không đóng được**: survey (§2.3) không có số, ExpertLongBench (§2.4) chỉ phát biểu định tính, và nguồn hứa hẹn nhất (`evidence.md` §3.2, *Can LLMs Write Reliable Rubrics?*) **không parse được PDF**. Phép đo (2) ở §1.5 cho một ước lượng thay thế trên dữ liệu của team.

**U-8. Ý quan trọng nằm trong BẢNG/HÌNH của PDF thì đo thế nào?**
Toàn bộ RQ02 là text ↔ text. Nếu `critical_fact` đến từ một biểu đồ, không approach nào chạm tới. Giới hạn ý nghĩa của `critical_fact_coverage` — cần nêu rõ trong báo cáo.

**U-9. Bao nhiêu `critical_facts` cho một PDF?**
Quá ít → coverage bão hòa ở 100%, mất khả năng phát hiện regression. Quá nhiều → phạt Deck Agent vì đã làm đúng việc chọn lọc (một deck 10 slide **nên** bỏ bớt ý phụ). Liên quan trực tiếp tới salience-weighting của `evidence.md` §1.1 (vital / okay / less-important). **Chưa giải quyết** → RQ07.

**U-10. Cỡ mẫu human để validate 3 phép đo này = TBD** (G-07, không chốt).
Phụ thuộc: validate metric nào (3 metric khác nhau có thể cần cỡ mẫu khác nhau), đơn vị là deck hay bullet, variance thực tế đo được ở §1.5, và budget. Candidate scale từ literature: **hàng chục tới ~100 instance, ≥3 annotator** (alt-test, verify ở RQ06). **Không** ghi thành requirement.

**U-11. Chưa verify, cần đọc bản gốc trước khi trích vào báo cáo:** ACU/RoSE và QAPyramid (§2.10, [SEARCH-ONLY]); Molecular Facts (§2.6, [SEARCH-ONLY]); venue của *Do Automatic Factuality Metrics…* (trang poster NeurIPS 2025 chưa fetch); ngày chính xác của *Stress Testing* (arXiv metadata mâu thuẫn); dimension cụ thể của UniSumEval (§2.11 không trích xuất được); toàn bộ số của LongSumEval và *Can LLMs Write Reliable Rubrics?* (§3.1, §3.2). **Đoạn số "26/29/6 vs 20/22/7" xuất hiện trong search KHÔNG truy được nguồn — cấm dùng.**

---

# 4. Proposed decisions cần human/team review

> Không ghi vào `05_decisions/DECISION_LOG.md` hay `05_decisions/OPEN_QUESTIONS.md` (RQ03 đang chạy song song). Các mục dưới đây là **đề xuất chờ review**, viết ở đúng format để người review chuyển sang sau nếu chấp nhận.

**P-01 — Chốt rằng "Content Fidelity" của Deck Agent là **vector 3 chiều**, không phải một score.**
Ba số: `critical_fact_coverage` · `hallucination_count` · `incorrectness_count`. Cấm cộng lại. Đây là G-02 được cụ thể hoá thành spec đo lường. Confidence: **cao**. Ảnh hưởng: `06_design/EVALUATION_FRAMEWORK.md`, `METRIC_REGISTRY`.

**P-02 — Đóng G-05 theo hướng: AlignScore/SummaC/FActScore KHÔNG được chọn; MiniCheck là candidate duy nhất đi tiếp, có điều kiện pilot.**
Evidence: `evidence.md` §1.3, §1.4, §1.1. Đây là **kết quả đi ngược hàm ý của Wave 1** (Wave 1 nêu AlignScore) → cần người review xác nhận rõ ràng chứ không lặng lẽ chuyển. Confidence: trung bình.

**P-03 — Chạy PILOT trước khi đưa bất kỳ metric nào vào `06_design/`.** Đề xuất phạm vi tối thiểu, xếp theo thứ tự rẻ→đắt:
- **(a) Discrimination smoke test** — dựng tay 3 deck O1/O2/O3 (`landscape.md` §1), chạy bộ đo, xem có ra 3 hình dạng khác nhau không. *Chi phí: một buổi.* **Không qua bước này thì không làm gì tiếp.**
- **(b) Premise-đảo-chiều test (U-1)** — kiểm tra MiniCheck có hoạt động khi premise là text deck ngắn.
- **(c) Numeric reasoning probe (U-4)** — 20–30 cặp (source 2 con số → bullet nêu %), đếm bao nhiêu bị đánh sai thành unsupported.
- **(d) Đối chiếu với nhãn người** trên mẫu nhỏ để kiểm tra bước phân loại bịa/sai (U-2).
Cần quyết định: ai làm, khi nào, và **có chấp nhận rằng nếu (a) fail thì toàn bộ hướng F2 bị loại** không.

**P-04 — Sửa/bổ sung NFR-53: checklist per-instance KHÔNG cover được failure "bịa".**
§8 gọi tên "không bịa" là mục tiêu nhưng method NFR-53 đề xuất là danh sách đóng các thứ *phải có*, trong khi bịa là tập mở các thứ *không được có* (`landscape.md` §A7). Đây là **challenge trực tiếp vào NFR-53**, hành động được ngay, độc lập với việc chọn F2 nào. Confidence: **cao** (lập luận cấu trúc). Cần team/giảng viên xác nhận trước khi ảnh hưởng `06_design/`.

**P-05 — Cỡ mẫu human: giữ `TBD`.**
Ghi trong `METRIC_REGISTRY`/`BENCHMARK_SPEC` là `Human sample size = TBD; candidate scale from literature: tens to ~100 instance, ≥3 annotator`. **Không** ghi thành requirement (G-07). Phụ thuộc kết quả §1.5 phép đo (1). Cần team xác nhận không chốt sớm.

**P-06 — Thêm yêu cầu vận hành: criteria (`critical_facts`) phải được sinh MỘT LẦN, review, và lưu như DỮ LIỆU của benchmark.**
Mạnh hơn yêu cầu "đóng băng judge prompt" của RQ06 P-6(a) — ở đó đóng băng câu hỏi, ở đây đóng băng **cả bộ tiêu chí**. Lý do: thước đo không được co giãn theo vật cần đo. Nếu chấp nhận → thuộc `06_design/EVALUATION_PIPELINE.md` + `BENCHMARK_SPEC.md`, và **route sang RQ07** (schema của benchmark case).

**P-07 — Thêm 3 phép đo noise floor/health làm điều kiện tiên quyết (G-09 cụ thể hoá cho RQ02):**
(a) within-run repeatability `N ≥ 5`; (b) **criteria stability** (sinh criteria 2 lần độc lập, đo trùng); (c) **discrimination smoke test**. Quy tắc đọc kèm theo: **delta < noise floor không được gọi là improvement**. Phép đo (b) là mới so với RQ06 và riêng cho RQ02. Nếu chấp nhận → `06_design/EVALUATION_PIPELINE.md`.

**P-08 — Quyết định thiết kế: coverage đo "hiện diện chủ đề", correctness đo riêng (U-5).**
Đề xuất chọn phương án (a) của `landscape.md` §1.4. Ảnh hưởng trực tiếp schema `critical_facts` → **phải chốt trước RQ07**, không phải sau.

**P-09 — Ghi nhận: `derived_number_queue` là đánh đổi có ý thức, không phải giải pháp đầy đủ.**
Nó đảm bảo bullet-tính-toán-lại (O4) **không bị đếm nhầm thành hallucination** và **không bị bỏ qua**, nhưng **không** tự động phân biệt "51% tính đúng" với "51% tính sai". Cần team đồng ý với mức rigor này, hoặc quyết định đầu tư thêm (vd. tool tính toán riêng). Phụ thuộc kết quả P-03(c).

**P-10 — Route sang RQ07 (benchmark) — 4 thứ RQ02 sinh ra mà benchmark phải chứa:**
(1) `critical_facts` per case, **có giá trị cụ thể** (không chỉ chủ đề), đã review, đóng băng;
(2) quyết định về **số lượng** critical fact/case (U-9) và có gán trọng số salience không (`evidence.md` §1.1: vital/okay/less-important);
(3) bộ probe/question (nếu chọn A6b) cũng đóng băng;
(4) 3 deck dựng tay O1/O2/O3 làm **fixture regression cho chính evaluator** — không phải test case của sản phẩm mà là test case của hệ đo.
</content>
