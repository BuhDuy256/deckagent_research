# Evaluation Tool Selection Guide — Đo cái gì, bằng công cụ nào, và khi nào chọn công cụ nào

**Ngày lập:** 2026-08-20
**Trạng thái:** SYNTHESIS — **không phải Metric Registry, không phải Benchmark Spec, không phải kết quả pilot**
**Đọc trước:** `README.md` mục 2 (metric ≠ evaluator tool) và mục 6 (ba nguyên tắc đọc kết quả)

---

## 0. Cách đọc file này

Đây là file trung tâm của gói. Mỗi mục dưới đây trả lời đúng một câu hỏi thực tế:

> *"Tôi muốn biết điều X. Đo bằng gì? Cần dữ liệu gì? Công cụ nào đáng tin tới đâu?"*

### 0.1 Cấu trúc một record

| Trường | Nghĩa |
| --- | --- |
| **A. Ta muốn biết gì?** | Câu hỏi bằng ngôn ngữ đời thường, không thuật ngữ |
| **B. Metric / Signal** | Tên kỹ thuật của đại lượng |
| **C. Claim metric này CÓ THỂ hỗ trợ** | Nó chứng minh được điều gì |
| **D. Claim metric này KHÔNG THỂ tự chứng minh** | Ranh giới — trường bắt buộc, để không ai dùng metric quá tầm của nó |
| **E. Dữ liệu kiểm thử cần có** | Evaluator cần đúng input gì mới chạy được |
| **F. Bản chất phép đo** | Deterministic / semantic automatic / judge / human / hybrid |
| **G. Candidate evaluators** | Các ứng viên thật, có evidence trong hồ sơ research |
| **H. Best Current Candidate** | Ứng viên mạnh nhất hiện tại — hoặc `OPEN` / `PAUSED` |
| **Câu hỏi buộc lựa chọn** | Một câu hỏi duy nhất quyết định chọn ứng viên nào (chỉ có khi ≥2 ứng viên) |
| **Vì sao đáng xem xét** | Bằng chứng ủng hộ |
| **Có thể thất bại ở đâu** | Bằng chứng phản đối / giới hạn đã biết |
| **Transfer risk** | Rủi ro khi chuyển công cụ đó sang bối cảnh Deck Agent |
| **Chi phí / Độ trễ / Repeatability** | Ước lượng định tính |
| **Cần pilot riêng?** | YES / NO / TBD, kèm việc pilot phải chứng minh |
| **Phụ thuộc kiến trúc** | LOW / MEDIUM / HIGH theo định nghĩa ở `01_EVALUATION_DEPENDENCY_MAP.md` |
| **Status** | READY / CANDIDATE / OPEN / PAUSED |

### 0.2 Bốn status

- **READY** — câu hỏi rõ, công cụ đủ chắc để dùng mà không cần thêm một vòng research chọn công cụ. Vẫn cần viết mã và viết test, nhưng **không cần nghiên cứu**.
- **CANDIDATE** — có một hướng mạnh, nhưng phải chạy pilot trên dữ liệu Deck Agent trước khi tin.
- **OPEN** — chưa có ứng viên thắng đủ cơ sở. Đây là kết quả hợp lệ, không phải thiếu sót.
- **PAUSED** — không nên nghiên cứu tiếp cho tới khi một quyết định kiến trúc/sản phẩm cụ thể được chốt.

### 0.3 Hai quy tắc bao trùm, áp cho mọi record

**Quy tắc 1 — "Best Current Candidate" không có nghĩa là "tool tốt nhất".** Nó có nghĩa là: *dựa trên bằng chứng hiện có, đây là thứ đáng thử trước*. Một ứng viên đứng đầu bảng vẫn có thể fail pilot, và khi đó **không** tự động rơi xuống ứng viên thứ hai — phải quyết định lại.

**Quy tắc 2 — LLM/MLLM judge không phải là một tên model.** Xem mục 0.4.

### 0.4 Một judge là một cấu hình, không phải một model

Khi một record ghi `LLM-as-a-Judge` hoặc `MLLM-as-a-Judge`, evaluator thực tế là **toàn bộ tổ hợp**:

```text
Evaluator =
  model + version/snapshot
+ judge prompt (nguyên văn, từng ký tự)
+ rubric / checklist
+ định dạng input (text? ảnh render? cả hai?)
+ cấu hình sinh (nhiệt độ, top_p, seed nếu có)
+ giao thức hiệu chuẩn (đo lặp lại, đo thiên lệch, đối chiếu với người)
```

Viết `"dùng GPT-x làm judge"` là **chưa mô tả evaluator**. Có bằng chứng cụ thể cho việc này: một nghiên cứu về độ ổn định của judge cho thấy phán quyết **đổi khi diễn đạt lại prompt theo cách tương đương**, và **model lớn hơn không ổn định hơn**. Nghĩa là đổi một chữ trong rubric giữa hai phiên bản làm mọi so sánh trước đó mất hiệu lực.

Vì vậy mọi record có judge đều mang thêm một trường:

```text
Có cần custom prompt/rubric không? YES / NO / TBD
```

và nếu `YES` mà chưa có cấu hình cụ thể, giá trị đúng là:

```text
TBD — phải freeze khi pilot hoặc evaluator cuối được chọn.
```

---

## 1. Bảng tổng — trạng thái toàn bộ metric

| # | Metric / signal | Bản chất | Best Current Candidate | Pilot? | Arch. | Status |
| --- | --- | --- | --- | :-: | :-: | --- |
| 1 | source grounding | semantic automatic | MiniCheck (có điều kiện) | YES | LOW | CANDIDATE |
| 2 | coverage / important-fact presence | hybrid | OPEN | YES | LOW | OPEN |
| 3 | unsupported claim count | semantic automatic | MiniCheck (có điều kiện) | YES | LOW | CANDIDATE |
| 4 | hallucination (tách riêng) | hybrid | OPEN | YES | LOW | OPEN |
| 5 | incorrectness (tách riêng) | hybrid | OPEN | YES | LOW | OPEN |
| 6a | derived-number routing | deterministic | number normalizer + queue | NO | LOW | READY |
| 6b | derived-number correctness | hybrid | OPEN | YES | LOW | OPEN |
| 7 | source traceability | deterministic | kiểm con trỏ bằng mã | NO | MEDIUM | READY |
| 8 | schema validity | deterministic | validator của schema | NO | HIGH | PAUSED |
| 9 | artifact validity (mở được) | deterministic | mở file bằng thư viện/phần mềm | NO | LOW | READY |
| 10 | export success rate | deterministic | đếm trên run log | NO | LOW | READY |
| 11 | brief constraint compliance | deterministic | kiểm bằng mã | NO | LOW | READY |
| 12 | overflow | deterministic | kiểm hình học | NO | LOW | READY |
| 13 | overlap / out-of-bounds | deterministic | kiểm hình học | NO | LOW | READY |
| 14 | design-token conformance | deterministic | tỉ lệ phần tử đúng token | NO | MEDIUM | READY |
| 15 | geometric design correctness | deterministic | bộ kiểm hình học kiểu AeSlides | NO | LOW | READY |
| 16 | perceptual design quality | MLLM judge | judge + rubric chung, chấm trên ảnh render | YES | LOW | CANDIDATE |
| 17 | visual hierarchy / layout appropriateness | judge / human | OPEN | TBD | LOW | OPEN |
| 18 | editability | deterministic + human gate | thang phân cấp kiểu PEI | TBD | LOW | CANDIDATE |
| 19 | technical ready-to-use | deterministic (composite) | tổ hợp 9+11+12+13+18 | NO | LOW | READY |
| 20 | actual user edit effort | human | OPEN | TBD | MEDIUM | OPEN |
| 21 | same-source mode differentiation | deterministic + thống kê | thiết kế paired + WITHIN/BETWEEN | YES | MEDIUM | PAUSED |
| 22 | purpose-conditioned information selection | hybrid | PAUSED (thiếu định nghĩa sản phẩm) | YES | MEDIUM | PAUSED |
| 23 | surface diagnostic features | deterministic | tính thẳng từ output | NO | MEDIUM | READY |
| 24 | latency | telemetry | đo wall-clock | NO | LOW | READY |
| 25 | cost | telemetry | đếm token/lần gọi | NO | LOW | READY |
| 26 | reliability / retry & failure rate | telemetry | đếm trên run log | NO | LOW | READY |
| 27 | historical regression | giao thức | benchmark đóng băng + N lần chạy | NO | LOW | READY |
| 28 | external baseline comparison | giao thức | cùng input, chuẩn hóa output | TBD | MEDIUM | CANDIDATE |
| 29 | architecture baseline | giao thức | PAUSED | TBD | HIGH | PAUSED |
| 30 | component ablation | giao thức | PAUSED | TBD | HIGH | PAUSED |

---

# NHÓM 1 — Source / Content

## 1. Source grounding

**A. Ta muốn biết gì?**
Những điều xuất hiện trên slide có thực sự được tài liệu nguồn hỗ trợ hay không.

**B. Metric / Signal**
`source grounding` (mức độ nội dung được nguồn hỗ trợ) — vận hành dưới dạng: với mỗi đơn vị nội dung trên deck (thường là một bullet), nguồn có chống đỡ nó không.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng nội dung Deck Agent sinh ra bám vào tài liệu người dùng cung cấp, và rằng một thay đổi trong hệ thống không làm tăng số phát biểu không có chỗ dựa trong nguồn.

**D. Claim metric này KHÔNG THỂ tự chứng minh**

- Không cho biết deck có **bỏ sót** ý quan trọng nào không. Đây là giới hạn của **định nghĩa**, không phải của chất lượng công cụ: phép đo này chỉ duyệt những gì deck **đã nói**; những gì deck không nói thì không có mặt trong tập được duyệt. Có bằng chứng số cho điều này: một bản trả lời thiếu hẳn nội dung chính vẫn đạt điểm factuality cao hơn bản đúng và đủ (82,75% so với 82,58%).
- Không phân biệt được **bịa** với **nói sai** — cả hai đều rơi vào cùng một nhãn "không được hỗ trợ".
- Không cho biết deck có dễ hiểu, đẹp hay dùng được không.

**E. Dữ liệu kiểm thử cần có**
Văn bản nguồn đã trích xuất (dạng có thể cắt thành đoạn) **+** nội dung văn bản của deck, tách được thành từng đơn vị claim (bullet, câu trong ô nội dung, tiêu đề).
Không cần deck mẫu do người làm; không cần ảnh render.

**F. Bản chất phép đo**
Semantic automatic evaluator (bộ đánh giá tự động hiểu ngữ nghĩa).

**G. Candidate evaluators**

| Ứng viên | Cơ chế |
| --- | --- |
| **MiniCheck** | Mô hình nhỏ được huấn luyện chuyên để trả lời "claim này có được tài liệu chống đỡ không" |
| **AlignScore** | Mô hình alignment giữa hai đoạn văn bản |
| **SummaC (Conv / ZS)** | Suy diễn kéo theo (NLI) ở mức câu, tổng hợp lại |
| **FActScore / phân rã atomic fact** | Cắt nội dung thành mệnh đề nguyên tử rồi kiểm từng mệnh đề |
| **LLM-as-a-Judge chấm điểm fidelity** | Cho một mô hình đọc nguồn + deck và cho một điểm |

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: MiniCheck — có điều kiện pilot
```

**Câu hỏi buộc lựa chọn**

```text
Công cụ này phản ứng mạnh hơn với việc SỬA ĐÚNG một lỗi factual,
hay với việc sửa vô hại và với việc chèn thêm câu nguyên văn từ nguồn?
```

Nếu phản ứng mạnh hơn với sửa đúng → dùng được cho việc theo dõi tiến bộ.
Nếu phản ứng mạnh hơn với sửa vô hại → **loại**, vì nó sẽ thưởng cho những thay đổi không cải thiện gì.

Câu hỏi này chọn được ứng viên bằng bằng chứng đã có: trong một thí nghiệm phê phán trên sáu metric, **MiniCheck và AlignScore có dịch chuyển điểm dương mạnh nhất sau khi lỗi factual được sửa đúng**, còn **SummaC-Conv và UniEval lại nhạy với sửa vô hại hơn là với sửa đúng**. Tính chất sau phá hủy đúng mục đích theo dõi phiên bản, nên SummaC bị loại khỏi vai trò này.

Giữa MiniCheck và AlignScore, một thí nghiệm stress-test độc lập trên sáu metric xếp **MiniCheck vào nhóm tương đối ổn định**, còn **AlignScore vào nhóm rủi ro cao** ("particularly unreliable across domains and perturbation types"), và đặc biệt: **các metric này kém đi khi claim bị nén thông tin**, trong đó AlignScore tụt mạnh nhất. Bullet trên slide **về bản chất là claim bị nén** — nên đây đúng là đặc tính định nghĩa domain của Deck Agent.

**Vì sao MiniCheck đáng xem xét**

- Bài toán nó giải trùng bài toán ở đây: nhận diện xem output của mô hình có chỗ dựa trong một tài liệu cho trước không.
- Là mô hình nhỏ chạy cục bộ (bản dưới 1 tỉ tham số dựa trên Flan-T5-Large, ~770M), **không tốn phí API**. Kho mã chính chủ báo tốc độ: ~29 nghìn claim trong 30 phút trên một GPU A6000.
- Dữ liệu huấn luyện của nó được thiết kế để dạy mô hình nhận ra claim **tổng hợp thông tin từ nhiều câu nguồn** — đúng hình dạng của một bullet tóm tắt.

**Có thể thất bại ở đâu**

- **Chưa từng có ai áp nhóm công cụ này lên slide deck.** Hai vòng tìm kiếm độc lập cho kết quả **không có nguồn nào**. Toàn bộ kỳ vọng chuyển giao là suy luận, không phải bằng chứng.
- **Game được**: chèn một câu nguyên văn từ nguồn vào deck làm điểm tăng, mức tăng thường **bằng hoặc hơn** mức tăng do sửa đúng thật, và chiến lược khai thác cho mức cải thiện gấp 3–8 lần cải thiện thật. Với Deck Agent đây là rủi ro **cụ thể**: một deck dày chữ copy nguyên đoạn từ PDF sẽ ăn điểm cao ở metric này trong khi requirement nội bộ coi đó là lỗi. → **Bắt buộc đọc chỉ số này kèm một chỉ số nén** (tỉ lệ số chữ deck trên số chữ nguồn), không đọc một mình.
- Vấp ở phủ định tương đương logic.
- Một nghiên cứu phê phán cho thấy một mô hình nông chỉ dùng đặc trưng bề mặt cũng cạnh tranh được với các metric này ở một số tác vụ — nghĩa là một phần điểm số giải thích được bằng bề mặt, không phải bằng "hiểu tính đúng".

**Transfer risk: HIGH**
Benchmark gốc là văn bản dài → tóm tắt văn xuôi. Deck Agent là tài liệu → nhiều slide, bullet cụt không thành câu, mất chủ ngữ và mốc thời gian, có bảng và hình. Ngoài ra bullet là dạng **fragment cực đoan**, nên mọi evaluator văn bản phải giải bài toán khôi phục ngữ cảnh trước khi kiểm — và bước đó tự nó sinh lỗi.

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        LOW      (mô hình cục bộ, 0đ phí API)
Độ trễ:         LOW      (ước lượng vài giây/deck — CHƯA xác nhận trên hạ tầng của team)
Repeatability:  UNKNOWN  (chưa ai đo sàn nhiễu của nhóm công cụ này)
```

Cảnh báo: con số tốc độ gắn với một GPU cụ thể (A6000). Nếu team chạy trên CPU, ước lượng này sai.

**Cần pilot riêng? YES**

Pilot phải chứng minh:

1. Trên các ca dựng tay có đáp án biết trước, công cụ **phân biệt được** deck đúng, deck có claim không chống đỡ được, và deck có số bị tính lại đúng.
2. Chạy lặp lại cùng cấu hình **không đổi phán quyết**.
3. Thời gian chạy thật trên hạ tầng team chấp nhận được.

**Phụ thuộc kiến trúc: LOW**

**Status: CANDIDATE**

---

## 2. Coverage / important-fact presence

**A. Ta muốn biết gì?**
Những ý quan trọng trong tài liệu nguồn có được đưa lên deck hay không.

**B. Metric / Signal**
`coverage` (mức độ bao phủ các ý cần có) — tỉ lệ các fact/topic đã được đóng băng trước mà deck có **đề cập tới**.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng deck không bỏ mất những nội dung mà người dùng cần; và rằng một thay đổi trong hệ thống không âm thầm làm mất nội dung để đổi lấy điểm số ở trục khác.

**D. Claim metric này KHÔNG THỂ tự chứng minh**

- Không cho biết nội dung được đề cập có **đúng** không. Một deck nhắc tới churn nhưng ghi sai giá trị vẫn tính là đã đề cập tới churn; lỗi giá trị được báo riêng ở trục correctness.
- Không cho biết việc bỏ một ý là **cố ý** hay là **sót**. Đây là ranh giới thật, không phải chi tiết kỹ thuật: nếu sản phẩm có chế độ trình bày cho phép lược bớt phần nền, thì cùng một quan sát "phần nền vắng mặt" là *lỗi bỏ sót* dưới góc nhìn nguồn nhưng là *lược bỏ đúng ý đồ* dưới góc nhìn yêu cầu. Đo coverage mà không tính tới điều này sẽ **phạt hệ thống vì đã làm đúng**.
- Không nói gì về chất lượng trình bày.

**E. Dữ liệu kiểm thử cần có**

Đây là metric **tốn dữ liệu nhất** trong cả gói. Cần ba lớp tách rời cho mỗi case:

```text
1. fact identity / topic key
   → định danh nội dung đang nói tới (chủ thể + thuộc tính),
     KHÔNG chứa giá trị.        → dùng cho presence

2. gold claim
   → phát biểu chuẩn lấy từ nguồn, có giá trị/chiều/mốc thời gian
     và con trỏ về nguồn.        → dùng cho correctness

3. mức kỳ vọng theo chế độ (nếu sản phẩm có nhiều chế độ)
   → fact này là bắt buộc với mọi chế độ, bắt buộc với một chế độ,
     tùy chọn, hay không kỳ vọng.
```

Ví dụ cụ thể:

```yaml
fact_identity:  { subject: churn, attribute: change }
gold_claim:     "Churn giảm từ 6,2% xuống 3,9%"
```

Deck ghi *"Churn giảm từ 6,2% xuống 1,9%"* → `presence = true`, `correctness = fail`.

Nếu nhập chung thành một chuỗi đầy đủ và so cả chuỗi với deck, thì "có nhắc nhưng nói sai" sẽ bị tính thành "không đề cập" — và hai trục sập vào nhau.

Ngoài ra: các fact này phải được **viết và duyệt trước khi nhìn output**, và **đóng băng** thành dữ liệu của benchmark, không sinh lại mỗi lần chạy. Lý do là cấu trúc: thước đo không được co giãn theo vật cần đo.

**F. Bản chất phép đo**
Hybrid — dữ liệu do người viết/duyệt, việc đối chiếu do máy làm.

**G. Candidate evaluators**

| Ứng viên | Cơ chế | Trạng thái |
| --- | --- | --- |
| Đối chiếu từng item bằng NLI/alignment | Với mỗi fact, hỏi mô hình xem deck có chống đỡ nó không | Có vấn đề về ngữ nghĩa nhãn (xem dưới) |
| Question/probe based | Sinh câu hỏi từ nguồn, xem deck trả lời được không | Chưa được đánh giá cho bối cảnh này |
| Hỏi thẳng LLM "deck bỏ sót gì?" | Một lần gọi mô hình, trả về danh sách thiếu | Bằng chứng mâu thuẫn |
| So khớp ngữ nghĩa bằng embedding | Đo độ tương đồng vector | Yếu (xem dưới) |
| Người đối chiếu thủ công | Người đọc và tick từng fact | Đáng tin nhất, không chạy hằng ngày được |

**H. Best Current Candidate**

```text
OPEN — chưa có lựa chọn thắng đủ cơ sở
```

Đây là ô trống lớn nhất và quan trọng nhất trong cả gói tài liệu.

**Câu hỏi buộc lựa chọn**

```text
Phép đo này có phân biệt được "deck không nhắc tới fact"
với "deck có nhắc tới fact nhưng nói sai" hay không?
```

Nếu **có** → đó là ứng viên hợp lệ cho coverage.
Nếu **không** → nó đang đo correctness, **không** được dùng làm coverage, dù kết quả nhìn có vẻ hợp lý.

Chính câu hỏi này loại bỏ cách tiếp cận trông hấp dẫn nhất về mặt hạ tầng. Cách đó là: đảo chiều công cụ ở record 1, lấy văn bản deck làm tài liệu chống đỡ và lấy `gold claim` làm claim cần kiểm — tức là tái sử dụng đúng một mô hình cho cả hai chiều, không thêm hạ tầng. Vấn đề: khi làm vậy, **fact bị nói sai** và **fact hoàn toàn vắng mặt** đều trả về cùng một nhãn "không được deck chống đỡ". Signal đó đo *fact có được deck nói đúng không*, không đo *fact có được nhắc tới không*. Dùng nó làm coverage sẽ vi phạm trực tiếp nguyên tắc tách presence khỏi correctness.

**Vì sao vẫn nên đi theo hướng "danh sách đóng" thay vì hỏi mở**

Hai nguồn độc lập về phương pháp cùng chỉ một hướng:

- Một framework đánh giá tóm tắt tách hai tác vụ chạy **ngược chiều nhau** — kiểm tính đúng của những gì đã nói, và đối chiếu từng key fact xem có được nói không. Ở tác vụ thứ hai, cách đối chiếu từng item đạt tương quan với người **0,688** trong khi một judge rubric chung chỉ đạt **0,314**. Khoảng cách đó là bằng chứng mạnh nhất trong toàn bộ hồ sơ cho việc **không** giao coverage cho một prompt chung chung.
- Một benchmark về khả năng phát hiện thiếu sót cho thấy các mô hình mạnh **kém hẳn** khi phải chỉ ra phần bị bỏ, dù chúng rất giỏi nhớ thông tin bất ngờ; một mô hình hàng đầu chỉ đạt khoảng **69,6% F1** ở độ dài ngữ cảnh khiêm tốn ~5 nghìn token. Giải thích cơ chế của nhóm tác giả rất đáng nhớ: cơ chế attention **không có gì để chú ý vào** ở chỗ trống, vì chỗ trống không tương ứng với ký hiệu nào trong văn bản.

**Bằng chứng đi ngược — chưa giải quyết**
Một nghiên cứu so sánh ba cách đo mức đầy đủ (dựa trên NLI, dựa trên hỏi–đáp, và hỏi thẳng mô hình) báo rằng cách **hỏi thẳng lại hiệu quả một cách bất ngờ** so với các cách phức tạp hơn, đổi lại kém về độ bền và khả năng diễn giải. Hồ sơ hiện tại **chưa trích xuất được số cụ thể** của nguồn này. Vì vậy: nghiêng về hướng danh sách đóng, nhưng **không coi câu hỏi đã đóng**.

**Cách bị loại**
Đo coverage bằng **độ tương đồng embedding** giữa đoạn nguồn và slide. Một hệ thống document→slide có công bố đã dùng cách này; vấn đề là nó không phân biệt được "nói đúng ý quan trọng" với "chép lại nhiều chữ" — một deck copy nhiều nguyên văn sẽ ăn điểm cao.

**Transfer risk: HIGH**
Các phương pháp đối chiếu key fact hiện có lấy key fact từ **bản tóm tắt tham chiếu do người viết**, còn Deck Agent **không có deck mẫu** nên phải lấy fact **từ chính tài liệu nguồn**. Đó là một thay đổi thật: nó làm mất tính chất "đây là những ý mà người tóm tắt cho là quan trọng", và đưa bài toán **chọn ý nào là quan trọng** quay trở lại.

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        MEDIUM  (chi phí lớn nhất là công người viết và duyệt fact, một lần cho mỗi case)
Độ trễ:         LOW     (nếu chạy bằng mô hình nhỏ cục bộ)
Repeatability:  UNKNOWN
```

**Cần pilot riêng? YES**
Pilot phải chứng minh, trên ca dựng tay có đáp án hai nhãn tách rời (một nhãn presence do người gán, một nhãn correctness do người gán):

1. khi deck bỏ bớt fact, chỉ số coverage **giảm**;
2. khi deck nhắc tới fact nhưng nói sai giá trị, chỉ số coverage **không giảm** còn correctness **báo lỗi**;
3. hai tín hiệu không suy ra được từ nhau.

**Phụ thuộc kiến trúc: LOW** (bản có điều kiện theo chế độ trình bày: MEDIUM)

**Status: OPEN**

---

## 3. Unsupported claim count

**A. Ta muốn biết gì?**
Trên deck có bao nhiêu phát biểu **không tìm được chỗ dựa** trong tài liệu nguồn.

**B. Metric / Signal**
`unsupported_claim_count` — tín hiệu **trung gian**, đứng trước bước phân loại thành bịa hay nói sai.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng số phát biểu không có chỗ dựa không tăng lên giữa hai phiên bản. Đây là con số **an toàn nhất để báo cáo sớm**, vì nó không tuyên bố nhiều hơn những gì công cụ thực sự phân biệt được.

**D. Claim metric này KHÔNG THỂ tự chứng minh**
Không nói được lỗi thuộc loại nào. Ba tình huống rất khác nhau — deck bịa hẳn một sự kiện, deck nói sai một giá trị nguồn có nêu, deck đưa ra một con số được tính lại đúng — có thể cùng rơi vào con số này nếu không có bước xử lý riêng.

**E. Dữ liệu kiểm thử cần có**
Giống record 1: văn bản nguồn + nội dung deck tách thành đơn vị claim.

**F. Bản chất phép đo**
Semantic automatic evaluator.

**G. Candidate evaluators**
Cùng nhóm với record 1.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: MiniCheck — có điều kiện pilot (cùng ứng viên với record 1)
```

**Câu hỏi buộc lựa chọn**
Không có ứng viên riêng — trùng với record 1.

**Có thể thất bại ở đâu**
Ngoài các giới hạn của record 1, có một cái bẫy riêng: **con số được tính lại đúng** (ví dụ nguồn ghi hai giá trị, slide ghi phần trăm thay đổi) sẽ không khớp nguyên văn với nguồn và có thể bị đếm nhầm vào đây. Xem record 6a.

**Transfer risk: HIGH** (như record 1)

**Chi phí / Độ trễ / Repeatability:** như record 1.

**Cần pilot riêng? YES** — chung pilot với record 1.

**Phụ thuộc kiến trúc: LOW**

**Status: CANDIDATE**

---

## 4. Hallucination (tách riêng)

**A. Ta muốn biết gì?**
Trong số các phát biểu không có chỗ dựa, cái nào là **bịa hẳn** — nguồn hoàn toàn không nói về chuyện đó.

**B. Metric / Signal**
`hallucination_count`.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng hệ thống không tự bổ sung nội dung ngoài tài liệu. Đây là claim uy tín quan trọng nhất của sản phẩm.

**D. Claim metric này KHÔNG THỂ tự chứng minh**
Không đo mức đầy đủ, không đo chất lượng trình bày, và **không thay thế được** con số `unsupported_claim_count` — nó là một lát cắt của con số đó.

**E. Dữ liệu kiểm thử cần có**
Danh sách claim đã bị đánh dấu không có chỗ dựa **+** một chỉ mục các đoạn nguồn để tra ngược **+** một bộ ca đã được người gán nhãn ba lớp: bịa rõ, nói sai rõ, và ranh giới mập mờ.

**F. Bản chất phép đo**
Hybrid: một quy tắc đối chiếu + một bước tra cứu nguồn + khả năng **từ chối phán quyết** khi mập mờ.

**G. Candidate evaluators**

| Ứng viên | Cơ chế |
| --- | --- |
| Quy tắc "có đối ứng trong nguồn hay không" | Nếu nguồn có một phát biểu cùng chủ thể + thuộc tính + phạm vi + mốc thời gian mà giá trị khác → *nói sai*; nếu không tìm được → *bịa* |
| NLI ba lớp | Coi nhãn "mâu thuẫn" ≈ nói sai, "trung tính" ≈ bịa |
| Người phán xét | Người đọc và gán nhãn |

**H. Best Current Candidate**

```text
OPEN — chưa có lựa chọn thắng đủ cơ sở
```

**Câu hỏi buộc lựa chọn**

```text
Với một phát biểu sai, ta có tra được trong nguồn một phát biểu ĐỐI ỨNG
khớp đồng thời bốn trường — chủ thể, thuộc tính, phạm vi, mốc thời gian — hay không?
```

Nếu tra được → *nói sai*.
Nếu chắc chắn không có → *bịa*.
Nếu **bất kỳ trường nào không đủ rõ** → **không gán nhãn**, đưa vào diện cần người xem.

Trường hợp thứ ba mới là điểm quyết định. Ép mọi ca vào hai lớp sẽ tạo ra sự chắc chắn giả. Ví dụ bắt buộc phải có trong bộ ca thử: nguồn nói *"doanh thu tăng"*, deck nói *"ARR tăng"* — cùng chủ thể hay khác chủ thể? Không có câu trả lời hiển nhiên.

**Vì sao đây vẫn là OPEN**

- Quy tắc đối ứng ở trên là **suy luận của chính dự án này**, **không có tiền lệ** nào trong tài liệu bên ngoài đã kiểm chứng nó.
- Có một tín hiệu cảnh báo mạnh từ lĩnh vực lân cận: trong một framework đánh giá tóm tắt, cùng một hệ thống đạt **86,4%** khi chỉ cần phát hiện *có lỗi hay không*, nhưng chỉ **42,2%** khi phải nói *lỗi thuộc loại nào*. Phân loại chi tiết khó hơn phát hiện rất nhiều.
- Các công cụ alignment hiện có đều trả về nhị phân "có/không được chống đỡ", nên bản thân chúng không tách được hai lớp này.

**Có thể thất bại ở đâu**
Ranh giới ngữ nghĩa lân cận: `Revenue` với `ARR`; toàn công ty với một đơn vị kinh doanh; cùng chỉ số nhưng khác kỳ. Tra cứu quá rộng thì bịa bị gán thành nói sai; tra cứu quá hẹp thì ngược lại.

**Transfer risk: HIGH**

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        MEDIUM   (tra cứu + công người gán nhãn ca thử)
Độ trễ:         LOW
Repeatability:  UNKNOWN
```

**Cần pilot riêng? YES**
Pilot phải chứng minh: ca bịa rõ và ca nói sai rõ đi đúng nhánh; **ca mập mờ được từ chối phán quyết thay vì bị ép nhãn**; và quy tắc được viết **trước** khi nhìn dự đoán, không sửa sau.
Cho tới khi có kết quả đó, hệ đo chỉ được báo `unsupported_claim_count` kèm một hàng đợi cần người xem — **không** được báo hai con số riêng.

**Phụ thuộc kiến trúc: LOW**

**Status: OPEN**

---

## 5. Incorrectness (tách riêng)

**A. Ta muốn biết gì?**
Trong số các phát biểu không có chỗ dựa, cái nào là **nói sai** một thông tin mà nguồn **có** đề cập.

**B. Metric / Signal**
`incorrectness_count`.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng hệ thống không làm sai lệch thông tin của người dùng trong quá trình nén và diễn đạt lại. Về mặt sửa lỗi, đây là tín hiệu trỏ vào bước trích xuất/nén, khác hẳn với bịa.

**D. Claim metric này KHÔNG THỂ tự chứng minh**
Không đo mức đầy đủ; không thay thế `unsupported_claim_count`; và **không tự động** bao gồm các con số được tính lại sai — phần đó cần một đường xử lý riêng (record 6).

**E. Dữ liệu kiểm thử cần có**
Giống record 4, cộng thêm: với mỗi ca nói sai, đoạn nguồn đối ứng phải được ghi lại làm bằng chứng.

**F. Bản chất phép đo**
Hybrid (chung cơ chế với record 4).

**G/H. Candidate evaluators và Best Current Candidate**

```text
OPEN — chưa có lựa chọn thắng đủ cơ sở
```

Record 4 và record 5 là **hai mặt của cùng một bước phân loại**; chúng đứng hoặc ngã cùng nhau. Câu hỏi buộc lựa chọn, bằng chứng, giới hạn và yêu cầu pilot: xem record 4.

**Phụ thuộc kiến trúc: LOW**

**Status: OPEN**

---

## 6a. Derived-number routing

**A. Ta muốn biết gì?**
Những con số trên deck **không khớp nguyên văn** với nguồn đang nằm ở đâu, để không âm thầm kết tội chúng và cũng không âm thầm bỏ qua chúng.

**B. Metric / Signal**
`unmatched_number_count` và `derived_number_queue_size` — kích thước hàng đợi các con số cần xem lại. Đây là **tín hiệu chẩn đoán**, không phải điểm chất lượng.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng hệ đo **không phạt nhầm** một hành vi tốt. Ví dụ: nguồn ghi doanh thu tăng từ `12,4M` lên `18,7M`, slide ghi *"tăng ~51%"*. Con số `51%` không có trong nguồn nhưng đúng (`(18,7−12,4)/12,4 ≈ 50,8%`). Nếu coi mọi số không khớp là bịa, hệ đo sẽ trừng phạt đúng loại tóm tắt mà sản phẩm muốn tạo ra.

**D. Claim metric này KHÔNG THỂ tự chứng minh**
**Không** chứng minh phép tính đúng hay sai. Hàng đợi chỉ là ranh giới an toàn giữa hai lỗi, không phải phán quyết.

**E. Dữ liệu kiểm thử cần có**
Nội dung deck **+** văn bản nguồn **+** một bộ chuẩn hóa số (đơn vị, cách viết thập phân, tỉ lệ phần trăm so với điểm phần trăm, dung sai làm tròn) có unit test riêng.

**F. Bản chất phép đo**
Deterministic.

**G. Candidate evaluators**
Bộ chuẩn hóa số viết bằng mã + đối chiếu nguyên văn. Không có ứng viên thứ hai đáng cân nhắc cho **việc định tuyến** — đây là một phép so khớp có định nghĩa rõ.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: bộ chuẩn hóa số + hàng đợi, viết bằng mã
```

**Vì sao đáng làm dù đơn giản**
Nó tách một lớp lỗi khó ra khỏi đường đi chính bằng một cơ chế hoàn toàn tất định, rẻ và ổn định. Việc suy luận định lượng là **một trục năng lực tách rời** khỏi suy diễn ngôn ngữ đã được chỉ ra từ lâu trong một benchmark chuyên về suy luận số trong tác vụ kéo theo: các mô hình học kéo theo **không tự nhiên học được** thao tác trên đại lượng. Cần nói rõ giới hạn của bằng chứng này: nó có tuổi đời và được đo trên thế hệ mô hình cũ, nên nó **không** chứng minh các mô hình hiện nay làm được hay không làm được — nó chỉ là lý do **kiến trúc** để tách phép kiểm số ra khỏi phép kiểm ngôn ngữ.

**Có thể thất bại ở đâu**
Bộ chuẩn hóa sai (đơn vị, `%` so với `điểm phần trăm`, dung sai làm tròn) sẽ đẩy nhầm số vào hoặc ra khỏi hàng đợi. Đây là lỗi mã, sửa được bằng unit test — không phải giới hạn phương pháp.

**Transfer risk: LOW**

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        LOW
Độ trễ:         LOW
Repeatability:  HIGH   (tất định theo định nghĩa — nếu chạy lại ra khác thì đó là lỗi)
```

**Cần pilot riêng? NO** (cần unit test, không cần pilot nghiên cứu)

**Phụ thuộc kiến trúc: LOW**

**Status: READY**

---

## 6b. Derived-number correctness

**A. Ta muốn biết gì?**
Những con số được tính lại đó **tính đúng hay sai**.

**B. Metric / Signal**
`derived_number_correct_rate` — tỉ lệ phép biến đổi số hợp lệ trong số các số đã vào hàng đợi.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng deck không đưa ra những con số tự tính sai — loại lỗi khó phát hiện bằng mắt nhất và dễ gây hậu quả nhất khi trình bày.

**D. Claim metric này KHÔNG THỂ tự chứng minh**
Không đo mức đầy đủ; không đo các lỗi số không đến từ phép biến đổi (ví dụ chép sai một con số có sẵn — đó là record 5).

**E. Dữ liệu kiểm thử cần có**
Các cặp dựng tay theo từng loại biến đổi, mỗi loại vài ca: suy ra đúng · suy ra sai · làm tròn · đổi đơn vị · nhầm `%` với `điểm phần trăm`. Mỗi ca kèm **đáp án tính bằng một công cụ độc lập**, không do chính evaluator sinh ra.

**F. Bản chất phép đo**
Hybrid: máy tính tất định cho phần tính được, người xem cho phần còn lại.

**G. Candidate evaluators**

| Ứng viên | Cơ chế |
| --- | --- |
| Máy tính tất định | Trích các đại lượng, tự tính lại, so với số trên slide |
| Evaluator ngữ nghĩa (như record 1) | Hỏi mô hình xem nguồn có chống đỡ phát biểu chứa con số đó không |
| Người xem hàng đợi | Người kiểm từng số trong hàng đợi |

**H. Best Current Candidate**

```text
OPEN — chưa có lựa chọn thắng đủ cơ sở
```

**Câu hỏi buộc lựa chọn**

```text
Phép biến đổi này có trích được thành một biểu thức số học đóng
từ các đại lượng đã có trong nguồn hay không?
```

Nếu **có** → dùng máy tính tất định; không giao cho mô hình ngôn ngữ.
Nếu **không** (ví dụ "tăng trưởng tăng tốc", "gần gấp đôi") → cần người xem hoặc một evaluator ngữ nghĩa, và phải chấp nhận rằng đây là vùng có sự mập mờ thật.

**Vì sao chưa chọn được**
Chưa ai đo lại năng lực suy luận định lượng của nhóm công cụ alignment/NLI **hiện đại** trên đúng loại biến đổi này. Kết luận "không làm được" chỉ có bằng chứng từ thế hệ mô hình cũ; kết luận "làm được" thì không có bằng chứng nào. Đây là câu hỏi **rẻ để tự trả lời** — dựng 20–30 cặp và đếm.

**Có thể thất bại ở đâu**
Hai hướng, mức nguy hiểm khác nhau: phạt nhầm một phép tính đúng (gây khó chịu, làm nhiễu tín hiệu) và **bỏ lọt một phép tính sai** (nguy hiểm hơn nhiều).

**Transfer risk: MEDIUM**

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        LOW      (bộ ca nhỏ)
Độ trễ:         LOW
Repeatability:  HIGH cho nhánh máy tính / UNKNOWN cho nhánh mô hình
```

**Cần pilot riêng? YES**
Pilot phải chứng minh: các phép biến đổi hợp lệ **không bị kết tội**, các phép sai **không được chấp nhận**, và **mọi** con số không khớp nguyên văn vẫn đi vào hàng đợi bất kể phán quyết. Ghi chú quan trọng: pilot **đạt** cũng **không** cho phép bỏ hàng đợi — nó chỉ cho phép dùng mô hình như tín hiệu hỗ trợ phân loại hàng đợi.

**Phụ thuộc kiến trúc: LOW**

**Status: OPEN**

---

## 7. Source traceability

**A. Ta muốn biết gì?**
Mỗi khối nội dung trên deck có con trỏ ngược về tài liệu nguồn không, và con trỏ đó có trỏ đúng chỗ không.

**B. Metric / Signal**
`traceability_rate` — tỉ lệ khối nội dung có con trỏ nguồn hợp lệ và trỏ tới vùng thực sự tồn tại.

**C. Claim metric này CÓ THỂ hỗ trợ**
Rằng người dùng kiểm chứng được từng ý trên deck mà không phải đọc lại toàn bộ tài liệu.

**D. Claim metric này KHÔNG THỂ tự chứng minh**
Con trỏ **tồn tại và trỏ đúng vùng** không có nghĩa là **nội dung đúng**. Một bullet có thể trỏ về đúng trang 7 mà vẫn diễn giải sai nội dung trang 7. Đây là **correctness requirement**, không phải phép đo chất lượng AI, và **không được trộn** vào nhóm chỉ số bịa/sai/bỏ sót.

**E. Dữ liệu kiểm thử cần có**
Output có mang trường con trỏ nguồn **+** tài liệu nguồn đã được đánh chỉ mục theo cùng hệ toạ độ (trang/đoạn/khối).

**F. Bản chất phép đo**
Deterministic.

**G. Candidate evaluators**
Kiểm bằng mã: con trỏ có tồn tại không, có trỏ tới vùng hợp lệ không, tỉ lệ khối không có con trỏ là bao nhiêu.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: kiểm con trỏ bằng mã
```

Không có ứng viên cạnh tranh — đây là một phép kiểm có định nghĩa hình thức.

**Vì sao đáng làm, và một cảnh báo**
Một vòng tìm kiếm có chủ đích **không tìm thấy** benchmark bên ngoài nào coi traceability của deck là một chiều đánh giá. Điều đó có hai mặt: đây có thể là điểm khác biệt đáng trình bày của sản phẩm, nhưng cũng có nghĩa **không có mốc bên ngoài để so**.

Cảnh báo cụ thể: một hệ thống document→slide có nêu rằng việc gắn mỗi slide vào một mục cụ thể của tài liệu giúp giảm bịa, **nhưng không có phép đo nào cô lập tác dụng đó**. Vì vậy **không được trích** traceability như bằng chứng rằng hệ thống ít bịa hơn — đó là hai chuyện khác nhau.

**Transfer risk: LOW**

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        LOW
Độ trễ:         LOW
Repeatability:  HIGH
```

**Cần pilot riêng? NO**

**Phụ thuộc kiến trúc: MEDIUM** — chỉ tồn tại nếu kiến trúc cuối vẫn mang liên kết nguồn tới tận output.

**Status: READY** (nhưng chỉ kích hoạt khi capability còn tồn tại)

---

# NHÓM 2 — Artifact / Correctness

Nhóm này có một đặc điểm chung: **mọi thứ ở đây đều kiểm được bằng mã**, và ở đúng loại việc này thì mã **chính xác hơn** giám khảo mô hình chứ không chỉ rẻ hơn. Vì vậy toàn nhóm không có "câu hỏi buộc lựa chọn" giữa deterministic và judge — câu hỏi đó đã có câu trả lời.

**Câu hỏi buộc lựa chọn chung cho cả nhóm 2:**

```text
Lỗi này có thể được xác định đúng/sai hoàn toàn từ dữ liệu cấu trúc
mà không cần hiểu ngữ nghĩa hay không?
```

Nếu **Có** → dùng deterministic, không đưa vào judge.
Nếu **Không** → mới xét tới evaluator ngữ nghĩa, judge, hoặc người.

Có bằng chứng trực tiếp cho việc đừng giao nhóm này cho mô hình: trong một nghiên cứu về phát hiện lỗi thiết kế slide với bộ phân loại 5 nhóm/27 loại lỗi và 2.400 slide, khả năng phát hiện của mô hình ngôn ngữ chỉ đạt **F1 từ 0,331 đến 0,655**, kể cả khi được cấp toàn bộ bảng phân loại lỗi trong prompt. Trong khi đó phần lớn các lỗi ở nhóm 2 có định nghĩa hình học chính xác.

---

## 8. Schema validity

**A. Ta muốn biết gì?** Cấu trúc dữ liệu trung gian mà hệ thống sinh ra có hợp lệ theo schema đã định nghĩa không, và tỉ lệ hợp lệ ngay lần đầu là bao nhiêu.

**B. Metric / Signal** `schema_validation_rate`, `first_pass_valid_rate`.

**C. Có thể hỗ trợ claim** Rằng hệ thống ổn định về mặt kỹ thuật và không phụ thuộc quá nhiều vào việc thử lại. Đây là tín hiệu bắt regression sớm và rẻ nhất.

**D. KHÔNG thể tự chứng minh** Không nói gì về chất lượng nội dung. Một deck hoàn toàn hợp lệ về schema vẫn có thể bịa toàn bộ nội dung.

**E. Dữ liệu kiểm thử cần có** Output có cấu trúc của hệ thống (JSON hoặc tương đương) **+** định nghĩa schema.

**F. Bản chất phép đo** Deterministic.

**G. Candidate evaluators** Trình validate của chính schema. Không có ứng viên cạnh tranh.

**H. Best Current Candidate**

```text
PAUSED — phụ thuộc architecture
```

Không phải vì công cụ khó, mà vì **đối tượng đo có thể không tồn tại**: nếu kiến trúc cuối không có một cấu trúc trung gian tường minh, phép đo này biến mất và được thay bằng "output cuối có hợp lệ không" (record 9).

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO**

**Phụ thuộc kiến trúc: HIGH**

**Status: PAUSED**

---

## 9. Artifact validity (file mở được)

**A. Ta muốn biết gì?** File xuất ra có mở được bằng phần mềm trình chiếu thật, đủ số slide, không hỏng cấu trúc.

**B. Metric / Signal** `open_success_rate`, và số slide thực tế so với số slide dự kiến.

**C. Có thể hỗ trợ claim** Rằng sản phẩm tạo ra artifact dùng được. Đây là điều kiện cần tuyệt đối.

**D. KHÔNG thể tự chứng minh** Không nói gì về nội dung, thiết kế hay tính hữu ích.

**E. Dữ liệu kiểm thử cần có** File output **+** một môi trường mở file được (thư viện đọc định dạng, hoặc phần mềm trình chiếu chạy tự động).

**F. Bản chất phép đo** Deterministic.

**G. Candidate evaluators** Mở file bằng thư viện; đối chiếu với bộ file mẫu đã biết là hợp lệ.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: mở file bằng thư viện + đối chiếu bộ mẫu cố định
```

**Vì sao đáng đo nghiêm túc thay vì coi là chuyện nhỏ**
Hai bằng chứng bên ngoài:

- Trong một benchmark sinh slide bằng chương trình, tỉ lệ chạy được dao động rất rộng giữa các mô hình (từ khoảng 2% tới khoảng 89%), và benchmark đó **cho điểm 0 ở cột tổng** cho slide không chạy được.
- Trong một hệ sinh presentation khác, việc bỏ một component làm tỉ lệ thành công rơi từ **95,0% xuống 74,6%** trong khi điểm chất lượng gần như **không đổi** (3,66 so với 3,67). Nếu chỉ đọc điểm chất lượng, kết luận sẽ là "component này vô dụng" — sai hoàn toàn.

Bài học: **luôn báo cáo tính hợp lệ của artifact song song với điểm chất lượng**, không bao giờ gộp.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO**

**Phụ thuộc kiến trúc: LOW**

**Status: READY**

---

## 10. Export success rate

**A. Ta muốn biết gì?** Trong N lần chạy, bao nhiêu lần cho ra một deck hoàn chỉnh mà không lỗi.

**B. Metric / Signal** `export_success_rate` — và nếu có nhiều định dạng xuất, báo riêng theo từng định dạng.

**C. Có thể hỗ trợ claim** Rằng hệ thống đủ tin cậy để dùng thật, và rằng một thay đổi không làm hỏng đường xuất file.

**D. KHÔNG thể tự chứng minh** Không nói gì về chất lượng. Xem lại ví dụ ở record 9: chất lượng và độ tin cậy có thể đi ngược chiều nhau.

**E. Dữ liệu kiểm thử cần có** Run log có ghi trạng thái kết thúc từng lần chạy, có phân biệt lỗi ở bước nào.

**F. Bản chất phép đo** Deterministic / telemetry.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: đếm trên run log, có phân loại nguyên nhân lỗi
```

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY**

---

## 11. Brief constraint compliance (số slide, thời lượng)

**A. Ta muốn biết gì?** Deck có tuân thủ các ràng buộc người dùng đặt ra không — số slide mong muốn, giới hạn thời gian trình bày.

**B. Metric / Signal** `slide_count_deviation`, `estimated_duration_deviation`.

**C. Có thể hỗ trợ claim** Rằng hệ thống nghe theo yêu cầu người dùng chứ không chỉ sinh ra thứ nó thích. Đây là dạng claim **dễ chứng minh nhất và dễ bị bỏ quên nhất**.

**D. KHÔNG thể tự chứng minh** Không nói gì về việc nội dung được chia có hợp lý hay không. Đúng 10 slide không có nghĩa là chia đúng chỗ.

**E. Dữ liệu kiểm thử cần có** Output đếm được số slide và số chữ **+** ràng buộc đã yêu cầu, lưu lại cùng lần chạy.

**F. Bản chất phép đo** Deterministic.

**G/H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: kiểm bằng mã
```

Nguyên tắc chung có hậu thuẫn bên ngoài: các chỉ thị **diễn đạt được thành một mệnh đề kiểm được** thì kiểm bằng mã, không giao cho mô hình chấm — đây là cách một benchmark về khả năng tuân thủ chỉ thị được xây dựng.

**Có thể thất bại ở đâu**
Phần ước lượng thời lượng cần một giả định về tốc độ nói. Có một công thức từ một benchmark sinh slide theo đối tượng người nghe, dạng `thời lượng ≈ 0,25 × số_slide + số_chữ / 130`. **Hai cảnh báo bắt buộc:** con số này trong hồ sơ hiện tại mới được đọc qua bản tóm tắt tự động của trang HTML, **chưa đối chiếu với bản gốc**; và hằng số 130 chữ/phút là cho tiếng Anh — **chưa biết có áp được cho tiếng Việt không**. Nếu dùng cho nhánh tiếng Việt mà không kiểm lại, mọi kết luận về thời lượng sẽ lệch có hệ thống.

**Transfer risk: LOW** (đếm slide) / **MEDIUM** (ước lượng thời lượng)

**Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** cho đếm slide; **TBD** cho công thức thời lượng nếu dùng cho tiếng Việt.

**Phụ thuộc kiến trúc: LOW** · **Status: READY**

---

## 12. Overflow

**A. Ta muốn biết gì?** Chữ có bị tràn ra ngoài khung chứa hoặc ngoài slide không.

**B. Metric / Signal** `overflow_rate` — tỉ lệ slide (hoặc phần tử) có hiện tượng tràn.

**C. Có thể hỗ trợ claim** Rằng deck dùng được ngay mà không phải chỉnh tay từng ô chữ. Đây là một trong những thành phần cụ thể và đo được nhất của lời hứa "chỉ sửa nhẹ".

**D. KHÔNG thể tự chứng minh** Không tràn không có nghĩa là đẹp, dễ đọc, hay đúng nội dung.

**E. Dữ liệu kiểm thử cần có**
**Thông tin hình học** của slide: toạ độ và kích thước khung, cỡ chữ, hoặc ảnh render kèm cây phần tử. Không có hình học thì không đo được — đây là điều kiện tiên quyết kỹ thuật, cần nêu sớm với người làm kiến trúc.

**F. Bản chất phép đo** Deterministic.

**G/H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: kiểm hình học bằng chương trình
```

**Vì sao không giao cho judge**
Ngoài kết quả F1 thấp đã nêu ở đầu nhóm 2, có một nguồn khác đi xa hơn: một hệ thống dùng phần thưởng kiểm chứng được cho bố cục slide lập luận rằng nhiều thuộc tính thẩm mỹ nền tảng của bố cục **vốn có cấu trúc và kiểm chứng chính xác được bằng phân tích chương trình**, và nhóm tác giả từ chối dùng phần thưởng dựa trên mô hình thị giác vì trong thử nghiệm của họ mô hình làm **tệ hơn đoán ngẫu nhiên** ở việc phát hiện lỗi bố cục. Cảnh báo trung thực: claim "tệ hơn đoán ngẫu nhiên" trong hồ sơ hiện tại mới được đọc qua bản tóm tắt tự động, **chưa đối chiếu bảng số gốc** — nên dùng nó như lý do củng cố, không như con số trích dẫn.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY** (kích hoạt khi có hình học)

---

## 13. Overlap / out-of-bounds

**A. Ta muốn biết gì?** Các phần tử có đè lên nhau không; có phần tử nào nằm ngoài ranh giới slide hoặc ngoài khung cha không.

**B. Metric / Signal** `overlap_rate`, `out_of_bounds_rate`.

**C. Có thể hỗ trợ claim** Cùng nhóm với record 12: deck sạch lỗi kỹ thuật.

**D. KHÔNG thể tự chứng minh** Không đè nhau không có nghĩa là bố cục hợp lý hay có trật tự thị giác tốt (xem record 17).

**E. Dữ liệu kiểm thử cần có** Như record 12: hình học hoặc cây phần tử có toạ độ.

**F. Bản chất phép đo** Deterministic.

**G/H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: kiểm va chạm hình học bằng chương trình
```

Có tiền lệ cụ thể về cách hình thức hóa: một hệ thống chia va chạm thành **ba loại** — hộp bao của hai phần tử không liên quan chồng nhau, phần tử tràn ra ngoài khung cha, và phần tử vượt ranh giới slide. Ba loại này cần đếm riêng vì cách sửa khác nhau.

**Có thể thất bại ở đâu** Định nghĩa "phần tử không liên quan" cần quy ước rõ (một nhãn nằm trên một hình nền không phải lỗi). Đây là công việc định nghĩa, không phải giới hạn phương pháp.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY** (kích hoạt khi có hình học)

---

# NHÓM 3 — Design

## 14. Design-token conformance

**A. Ta muốn biết gì?** Các phần tử trên slide có dùng đúng bộ quy ước thiết kế của hệ thống (màu, font, cỡ chữ) không; và khi đổi một token thì thay đổi có lan đúng và đủ không.

**B. Metric / Signal** `token_conformance_rate` (tỉ lệ phần tử dùng đúng token), `token_propagation_completeness` (đổi token thì bao nhiêu phần tử đổi theo).

**C. Có thể hỗ trợ claim** Rằng hệ thống có kiểm soát thiết kế thật, không phải mỗi slide một kiểu; và rằng người dùng đổi chủ đề một lần là toàn deck đổi theo.

**D. KHÔNG thể tự chứng minh** **Đúng token không có nghĩa là đẹp.** Một bộ token xấu được áp 100% chính xác vẫn cho ra deck xấu. Phần "trông có ổn không" là record 16.

**E. Dữ liệu kiểm thử cần có** Danh sách token đã định nghĩa **+** output có ghi lại thuộc tính style thực tế của từng phần tử **+** với phần lan truyền: hai ảnh chụp trạng thái trước và sau khi đổi token.

**F. Bản chất phép đo** Deterministic.

**G. Candidate evaluators**

| Ứng viên | Cơ chế |
| --- | --- |
| Tỉ lệ phần tử dùng đúng token | Đối chiếu thuộc tính thực tế với bảng token |
| Khoảng cách màu theo công thức tri giác | Đo độ lệch màu thay vì so bằng nhau tuyệt đối |
| Checklist do giám khảo mô hình chấm | Cho mô hình nhìn slide và trả lời từng mục về tính nhất quán |

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: tỉ lệ phần tử dùng đúng token (+ khoảng cách màu cho phần cần dung sai)
```

**Câu hỏi buộc lựa chọn**

```text
Yêu cầu này có phụ thuộc vào NỘI DUNG của từng tài liệu nguồn không?
```

Nếu **có** (ví dụ "deck có giữ đúng con số ở trang 7 của tài liệu này không") → phải dùng tiêu chí riêng cho từng case.
Nếu **không** — và tính nhất quán thiết kế đúng là **không** phụ thuộc nội dung nguồn — → dùng quy tắc chung, và phần lớn còn đẩy xuống được thành phép kiểm bằng mã.

Câu hỏi này loại bỏ phương án checklist-do-mô-hình-chấm khỏi vai trò chính: xây một checklist riêng cho từng tài liệu chỉ để kiểm màu và font là tốn kém không cần thiết.

**Vì sao đây là chỗ không có tiền lệ**
Một vòng tìm kiếm có chủ đích **không tìm thấy** metric bên ngoài nào đo tuân thủ design token theo nghĩa này. Thứ gần nhất là tiêu chí "tính đồng nhất thiết kế (font, màu, bố cục)" trong một benchmark được chấm bằng giám khảo, và một điểm hòa sắc tính bằng chương trình trong một benchmark khác — nhưng điểm hòa sắc đo **sự hài hòa của bảng màu**, không đo **việc tuân thủ một bảng token cho trước**. Hệ quả: nếu làm, đây là phần tự thiết kế, không có mốc bên ngoài để so, nhưng cũng là thứ có thể trình bày như một đóng góp.

**Transfer risk: LOW** (vì không mượn công cụ bên ngoài)

**Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: MEDIUM** (chỉ tồn tại nếu vẫn có hệ token tường minh)

**Status: READY** (kích hoạt khi capability còn tồn tại)

---

## 15. Geometric design correctness

**A. Ta muốn biết gì?** Ngoài chuyện tràn và đè nhau, bố cục có những lỗi hình học đo được nào khác không: tỉ lệ khung hình sai, khoảng trắng thừa quá mức, trọng tâm thị giác lệch.

**B. Metric / Signal** một nhóm chỉ số tính bằng chương trình, báo riêng từng cái.

**C. Có thể hỗ trợ claim** Rằng bố cục đạt một mức cơ bản có thể kiểm chứng được, không cần ai chấm cảm tính.

**D. KHÔNG thể tự chứng minh** Không nói được deck "đẹp" hay "chuyên nghiệp". Đây là **sàn**, không phải trần.

**E. Dữ liệu kiểm thử cần có** Ảnh render của slide **+** cây phần tử có toạ độ.

**F. Bản chất phép đo** Deterministic.

**G/H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: bộ chỉ số hình học tính bằng chương trình
```

Có một tiền lệ cụ thể với bốn chỉ số kiểm chứng được: **tỉ lệ khung hình méo** (so tỉ lệ render thật với tỉ lệ đích), **khoảng trắng thừa** (bản đồ phương sai cục bộ trên ảnh xám), **va chạm phần tử** (ba loại, xem record 13), và **mất cân bằng thị giác** (độ lệch của trọng tâm thị giác so với tâm khung).

**Câu hỏi buộc lựa chọn**
Trùng với câu hỏi chung của nhóm 2 (lỗi có xác định được từ dữ liệu cấu trúc không). Với bốn chỉ số trên, câu trả lời là **có** → deterministic.

**Có thể thất bại ở đâu** Các chỉ số này đo **thuộc tính của bố cục**, và trong Deck Agent phần lớn bố cục đến từ hệ thiết kế cố định chứ không từ quyết định của mô hình. Nghĩa là chúng có thể đo **chất lượng của hệ design system** nhiều hơn là đo chất lượng của phần AI — vẫn hữu ích, nhưng phải diễn giải đúng.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW–MEDIUM (cần render) · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY** (kích hoạt khi có render + hình học)

---

## 16. Perceptual design quality

**A. Ta muốn biết gì?** Nhìn bằng mắt thì deck có trông chỉn chu, dễ đọc, bố cục hợp lý không — phần còn lại sau khi đã bóc hết những gì kiểm được bằng mã.

**B. Metric / Signal** một điểm hoặc một nhóm điểm tri giác, chấm trên **ảnh render** của slide.

**C. Có thể hỗ trợ claim** Rằng deck đạt mức trình bày chấp nhận được với người xem; và rằng thay đổi hệ thiết kế làm nó tốt lên hay xấu đi.

**D. KHÔNG thể tự chứng minh** Không nói gì về nội dung có đúng nguồn không; không nói được deck có dùng được ngay không; và **không đáng tin ở mức từng lỗi cụ thể** (xem dưới).

**E. Dữ liệu kiểm thử cần có** **Ảnh render của từng slide** — đây là điểm khác biệt lớn nhất so với các record nhóm 1: cần một đường render ổn định, và phải cố định phiên bản render vì đổi renderer là đổi vật được chấm.

**F. Bản chất phép đo** MLLM-as-a-Judge.

**G. Candidate evaluators**

| Ứng viên | Cơ chế |
| --- | --- |
| Judge + rubric chung, chấm trên ảnh render | Vài tiêu chí cố định, thang điểm ngắn |
| Judge + checklist riêng cho từng tài liệu | Sinh danh sách mục kiểm riêng, chấm nhị phân từng mục kèm bằng chứng |
| Người chấm | Người xếp hạng hoặc chấm điểm |
| Chỉ số hình học tất định | Xem record 15 |

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: judge + rubric CHUNG, chấm trên ảnh render — có điều kiện pilot
```

**Câu hỏi buộc lựa chọn**

```text
Yêu cầu cần kiểm có phụ thuộc vào NỘI DUNG của từng tài liệu nguồn không?
```

Nếu **không** (thẩm mỹ, bố cục, độ tương phản — đúng trường hợp này) → **rubric chung là đủ**, và checklist riêng theo từng tài liệu là tốn kém không cần thiết.
Nếu **có** → mới cần checklist riêng theo case (xem nhóm 1).

Đây không phải suy đoán: một benchmark có tiêu chí riêng cho từng input cũng **tự chia** các chiều của mình thành nhóm phụ thuộc tài liệu và nhóm không phụ thuộc tài liệu, và xếp thiết kế/bố cục vào nhóm **không** phụ thuộc.

**Vì sao judge đáng dùng ở đây — trong khi bị loại ở nhóm 2**
Đây là chiều mà giám khảo mô hình **đồng thuận với người tốt nhất**:

- Trong một hệ đánh giá presentation ba chiều, tương quan Pearson với người là **0,90 cho Design**, **0,70 cho Content** và chỉ **0,55 cho Coherence**. Design là chiều mạnh nhất.
- Trong một benchmark sinh slide khác, độ đồng thuận giữa hai người chấm và mô hình ở nhóm tiêu chí không cần tham chiếu đạt **73,8%–85,3%**, mức mà nhóm tác giả gọi là cao.

**Có thể thất bại ở đâu**

- **Chấm tổng thể thì được, chỉ ra từng lỗi thì không.** Nghiên cứu về phát hiện lỗi thiết kế slide cho **F1 0,331–0,655**. Nghĩa là dùng judge để hỏi "deck này trông thế nào" là hợp lý; dùng judge để hỏi "liệt kê các lỗi thiết kế" thì không.
- **Người chấm nhanh thiên về tín hiệu dễ thấy.** Một benchmark ghi nhận rằng khi xếp hạng nhanh, người ưu tiên các dấu hiệu dễ nhận biết như cấu trúc, bố cục và mạch tổng thể hơn là kiểm chứng chi tiết mức độ bám nguồn. Hệ quả: **nếu chỉ đo bằng xếp hạng của người, phần bám nguồn sẽ bị đánh giá nhẹ đi**.
- **Trần của chính con người không cao.** Trong hệ ba chiều nói trên, đồng thuận giữa bốn người chấm ở mức Fleiss κ ≈ 0,59. Vì vậy **không** đặt mục tiêu judge đạt tương quan tuyệt đối cao với người; cách phát biểu đúng là so judge với **trần người–người trên cùng dữ liệu**.
- **Điểm tuyệt đối không có ý nghĩa nếu thiếu ngữ cảnh.** Trong một benchmark, ngay cả slide do người làm cũng **không đạt trần điểm** ở các tiêu chí không cần tham chiếu — nên đừng kỳ vọng 100%, hãy đặt kỳ vọng theo mốc người.

**Có cần custom prompt/rubric không? YES**

```text
TBD — phải freeze khi pilot hoặc evaluator cuối được chọn.
```

Bắt buộc kèm ba việc khi chọn: đóng băng prompt/rubric theo phiên bản; ưu tiên câu hỏi nhị phân hơn thang điểm nhiều mức (thang điểm cho phép trôi dần dưới ngưỡng); và **không dùng chung họ mô hình với mô hình sinh deck**, vì có bằng chứng rằng thiên lệch ưu ái bản thân gắn với khả năng tự nhận ra output của chính mình.

**Transfer risk: MEDIUM**
Bối cảnh gần (đều là slide, đều chấm trên ảnh render), nhưng các con số tương quan ở trên đến từ các thiết lập khác nhau, không phải đo lại trên Deck Agent.

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        HIGH     (gọi mô hình đa phương thức trên từng slide)
Độ trễ:         HIGH
Repeatability:  LOW–MEDIUM  (phán quyết đổi khi diễn đạt lại prompt; phải tự đo sàn nhiễu)
```

Vì vậy: **không chạy mỗi lần commit.** Nhịp phù hợp là hằng đêm hoặc theo mốc, trên tập nhỏ.

**Cần pilot riêng? YES**
Pilot phải chứng minh: chạy lặp cùng một deck N lần cho **mức dao động chấp nhận được**; judge **không** bị đánh lừa bởi độ dài/độ dày chữ (dựng cặp deck cùng nội dung khác lượng chữ và xem điểm có lệch không); và có một mẫu nhỏ do người chấm để biết judge cách trần người bao xa.

**Phụ thuộc kiến trúc: LOW**

**Status: CANDIDATE**

---

## 17. Visual hierarchy / layout appropriateness

**A. Ta muốn biết gì?** Slide có trật tự thị giác rõ ràng không (mắt biết nhìn đâu trước), và kiểu bố cục được chọn có hợp với loại nội dung đang trình bày không.

**B. Metric / Signal** chưa có đại lượng nào được chọn.

**C. Có thể hỗ trợ claim** Rằng hệ thống không chỉ đặt nội dung vào slide mà còn **chọn cách trình bày phù hợp** với nội dung đó.

**D. KHÔNG thể tự chứng minh** Không thay thế các phép kiểm hình học; không nói gì về nội dung.

**E. Dữ liệu kiểm thử cần có** Ảnh render **+** nhãn loại nội dung của từng slide (so sánh? quy trình? số liệu? định nghĩa?) — mà nhãn này hiện **chưa tồn tại** dưới dạng dữ liệu.

**F. Bản chất phép đo** Judge hoặc human.

**G/H. Best Current Candidate**

```text
OPEN — chưa có lựa chọn thắng đủ cơ sở
```

**Vì sao OPEN**
Hai lý do độc lập:

1. "Thiếu trật tự thị giác" nằm trong bảng phân loại lỗi thiết kế của nghiên cứu đã nêu, nhưng đó chính là bảng mà mô hình chỉ đạt F1 0,331–0,655 khi phát hiện. Tức là **cách đo tự động cho đúng lỗi này chưa đáng tin**.
2. Phần "bố cục có hợp với loại nội dung không" đòi hỏi một **bảng phân loại loại nội dung** mà dự án chưa có. Chưa có nhãn thì chưa có câu hỏi đo được.

**Không thể viết một câu hỏi buộc lựa chọn rõ ràng cho record này** — điều đó có nghĩa là sự khác biệt giữa các phương án hiện **chưa được hiểu đủ**, và đây là một kết quả hợp lệ cần ghi nhận chứ không cần lấp.

**Transfer risk: HIGH** · **Chi phí UNKNOWN · Độ trễ UNKNOWN · Repeatability UNKNOWN**

**Cần pilot riêng? TBD** (chưa đủ điều kiện để thiết kế pilot)

**Phụ thuộc kiến trúc: LOW** · **Status: OPEN**

---

# NHÓM 4 — Usability

## 18. Editability

**A. Ta muốn biết gì?** File xuất ra có **sửa được** trong phần mềm trình chiếu không, và sửa được tới mức nào.

**B. Metric / Signal** một **thang phân cấp** thay vì một điểm số, với quy tắc loại trừ: không đạt mức dưới thì không được tính mức trên.

**C. Có thể hỗ trợ claim** Rằng deck thực sự là điểm khởi đầu để người dùng làm việc tiếp, không phải một ảnh chụp đẹp. Đây là **phép đo nhạy nhất** để phát hiện hệ quả xấu của một thay đổi kiến trúc: nếu chuyển sang sinh HTML hoặc ảnh rồi mới đóng gói, mức editability tụt ngay.

**D. KHÔNG thể tự chứng minh** Sửa được không có nghĩa là **ít phải sửa** — đó là record 20, một câu hỏi hoàn toàn khác.

**E. Dữ liệu kiểm thử cần có** File output **+** khả năng mở và kiểm tra cấu trúc bên trong (text có phải đối tượng text không, hình có phải vector không, có cấu trúc master/layout không, bảng/biểu đồ có dữ liệu nền không).

**F. Bản chất phép đo** Hybrid: phần lớn kiểm được bằng mã, nhưng tiền lệ hiện có dùng **cổng kiểm tra do người thực hiện**.

**G/H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: thang phân cấp nhiều mức có quy tắc loại trừ — cần thích nghi
```

Tiền lệ cụ thể: một benchmark đề xuất thang **sáu mức** cho khả năng chỉnh sửa của deck, từ mức thấp nhất là slide chỉ là ảnh phẳng không sửa được, lên các mức text tách rời sửa được, phần tử là vector, có tổ chức master phân cấp cho phép lan truyền bố cục toàn hệ thống, bảng/biểu đồ là đối tượng dữ liệu thật, tới mức cao nhất có hoạt cảnh/đa phương tiện hoạt động như một mạch kể. Thang này có **quy tắc loại trừ** và được đánh giá qua các cổng kiểm tra do người thực hiện.

**Vì sao thang này hợp với Deck Agent một cách bất thường**
Mức "có tổ chức master phân cấp, cho phép đổi một chỗ lan ra toàn hệ thống" **chính là** điều mà requirement nội bộ về design token và lan truyền nguyên tử mô tả. Nghĩa là thang này không chỉ đo được sản phẩm — nó còn diễn đạt đúng thứ sản phẩm đang hứa.

**Câu hỏi buộc lựa chọn**

```text
Ta muốn biết deck có thể sửa được tới mức nào (thuộc tính của file),
hay muốn biết người dùng thực tế phải sửa bao nhiêu (hành vi của người)?
```

Nếu là mức **sửa được** → dùng thang phân cấp; phần lớn tự động hóa được.
Nếu là **phải sửa bao nhiêu** → không dùng thang này, xem record 20, và chấp nhận rằng chưa có công cụ.

**Có thể thất bại ở đâu**
Tiền lệ dùng cổng kiểm tra do **người** thực hiện; chưa rõ phần nào tự động hóa được hoàn toàn cho định dạng mà Deck Agent xuất ra. Đó là lý do record này chưa phải READY.

**Transfer risk: MEDIUM** (khái niệm chuyển giao tốt; thủ tục kiểm cần dựng lại)

**Chi phí LOW–MEDIUM · Độ trễ LOW · Repeatability HIGH cho phần tự động, MEDIUM cho phần người**

**Cần pilot riêng? TBD**
Việc cần làm trước tiên không phải pilot mà là một bước kỹ thuật: xác định mức nào trong thang **kiểm được bằng mã** trên định dạng đầu ra của Deck Agent. Sau đó mới biết còn lại bao nhiêu phải giao cho người.

**Phụ thuộc kiến trúc: LOW** · **Status: CANDIDATE**

---

## 19. Technical ready-to-use

**A. Ta muốn biết gì?** Deck có sạch lỗi kỹ thuật tới mức mang đi dùng được ngay không.

**B. Metric / Signal** một **vector** gồm các chỉ số đã có, báo riêng: mở được · đúng ràng buộc số slide · không tràn · không đè nhau/ngoài khung · mức editability đạt được.

**C. Có thể hỗ trợ claim** Rằng phần "dùng được ngay" **về mặt kỹ thuật** đã đạt — phần này đo được đầy đủ và rẻ.

**D. KHÔNG thể tự chứng minh** Không nói gì về việc người dùng có **hài lòng** không, có phải viết lại nội dung không, có phải xóa slide không. Sạch lỗi kỹ thuật là điều kiện cần, không phải điều kiện đủ.

**E. Dữ liệu kiểm thử cần có** Không cần dữ liệu mới — tái sử dụng dữ liệu của các record 9, 11, 12, 13, 18.

**F. Bản chất phép đo** Deterministic (tổ hợp).

**G/H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: vector các chỉ số tất định đã có — KHÔNG gộp thành một điểm
```

**Câu hỏi buộc lựa chọn**

```text
Khi con số này xấu đi, ta có biết ngay phải sửa cái gì không?
```

Nếu **có** (vì báo theo từng chỉ số) → giữ dạng vector.
Nếu **không** (vì đã gộp thành một điểm) → việc gộp đã xóa mất chính thứ khiến metric hữu ích.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY**

---

## 20. Actual user edit effort

**A. Ta muốn biết gì?** Người dùng thật phải bỏ ra bao nhiêu công sức chỉnh sửa trước khi dám mang deck đi trình bày.

**B. Metric / Signal** chưa có đại lượng nào được chọn.

**C. Có thể hỗ trợ claim** Rằng lời hứa "dùng được ngay hoặc chỉ sửa nhẹ" là thật. Đây là claim gần với giá trị sản phẩm nhất trong cả gói.

**D. KHÔNG thể tự chứng minh** Không thay thế được các phép kiểm kỹ thuật; và một kết quả tốt trên một nhóm người dùng không suy rộng ra nhóm khác.

**E. Dữ liệu kiểm thử cần có** **Người dùng thật** làm việc thật, có ghi lại thao tác (số slide bị xóa, số khối bị viết lại, thời gian tới lúc "chấp nhận được"). Không có dữ liệu này thì không có metric.

**F. Bản chất phép đo** Human evaluation.

**G/H. Best Current Candidate**

```text
OPEN — chưa có lựa chọn thắng đủ cơ sở
```

**Vì sao OPEN**
Đây là **chỗ bằng chứng yếu nhất trong toàn bộ hồ sơ research**. Một vòng tìm kiếm có chủ đích **không tìm thấy** metric bên ngoài nào lượng hóa công sức chỉnh sửa của người dùng trên deck sinh tự động. Nguồn gần nhất là một nghiên cứu với người dạy học thật, và nó chỉ **mô tả định tính**: người dạy phải sửa định dạng, xóa bớt slide, chỉnh cỡ chữ cho phần mã nguồn — có ghi lại rằng công sức là "ít", nhưng **không lượng hóa**. Cùng nghiên cứu đó có một quan sát đáng chú ý theo hướng khác: người học **không phân biệt được** slide do AI sinh với slide do người làm.

Lĩnh vực dịch máy và tóm tắt văn bản có truyền thống đo công sức hậu kỳ, nhưng **chưa thấy ai chuyển nó sang deck** — và deck khác ở chỗ công sức nằm ở cả nội dung lẫn hình thức lẫn thứ tự.

**Không thể viết một câu hỏi buộc lựa chọn rõ ràng** giữa các phương án, vì hiện chưa có hai phương án cụ thể để chọn. Ghi nhận: distinction chưa được hiểu đủ.

**Transfer risk: HIGH** · **Chi phí HIGH · Độ trễ HIGH · Repeatability LOW**

**Cần pilot riêng? TBD** — trước khi bàn tới pilot cần chốt **người dùng sửa deck ở đâu** (trong sản phẩm hay ngoài sản phẩm), vì định nghĩa "công sức" phụ thuộc hoàn toàn vào điều đó.

**Phụ thuộc kiến trúc: MEDIUM** · **Status: OPEN**

---

# NHÓM 5 — `slide_type`

Ba record trong nhóm này chia sẻ một đặc điểm quan trọng: **chúng không đo thuộc tính của một deck, mà đo quan hệ giữa nhiều deck sinh từ cùng một nguồn.** Không tồn tại "điểm slide_type" cho một deck đơn lẻ; đơn vị đánh giá là một **bộ deck**.

## 21. Same-source mode differentiation

**A. Ta muốn biết gì?** Khi giữ nguyên tài liệu nguồn và chỉ đổi chế độ trình bày, output có thực sự khác nhau **vượt mức khác nhau tự nhiên giữa hai lần chạy** không.

**B. Metric / Signal** so sánh mức khác biệt **giữa các chế độ** với mức khác biệt **trong cùng một chế độ khi chạy lặp**.

**C. Có thể hỗ trợ claim** Rằng hệ thống thực sự phản ứng với biến điều khiển, chứ không bỏ qua nó. Đây là điều kiện cần cho mọi claim về chế độ trình bày.

**D. KHÔNG thể tự chứng minh**

- **Khác biệt không phải chất lượng.** Hệ thống có thể tạo khác biệt rõ rệt mà cả ba deck đều tệ — nhồi chữ vào chế độ này, cắt chữ ở chế độ kia. Vì vậy kết quả này **luôn** phải báo kèm sàn chất lượng.
- Không cho biết khác biệt có **đúng hướng mong muốn** không — đó là record 22.

**E. Dữ liệu kiểm thử cần có**

```text
Với mỗi tài liệu nguồn:
  chế độ A × R lần chạy
  chế độ B × R lần chạy
  chế độ C × R lần chạy

Chỉ đổi ĐÚNG MỘT biến: chế độ trình bày.
Mọi thứ khác giữ nguyên: số slide yêu cầu, giới hạn thời gian, ngôn ngữ,
model, prompt, quy tắc thử lại, hệ thiết kế, đường xuất file.
```

`R ≥ 2` là yêu cầu toán học tối thiểu để mô tả được mức dao động trong cùng chế độ.

**F. Bản chất phép đo** Deterministic + thống kê. **Không có mô hình nào trong vòng lặp** ở phép kiểm lõi — đây là ưu điểm lớn: hoàn toàn lặp lại được.

**G. Candidate evaluators**

| Ứng viên | Cơ chế |
| --- | --- |
| So sánh có ghép cặp theo nguồn | Tính khoảng cách giữa các chế độ trong cùng một nguồn, đối chiếu với khoảng cách giữa các lần chạy lặp |
| Bộ phân loại nhỏ trên vector đặc trưng | Xem có đoán lại được chế độ từ output không, chia tập theo nguồn |
| Judge chọn một trong ba chế độ | Giấu nhãn, cho giám khảo mô hình đoán |

**H. Best Current Candidate**

```text
PAUSED — phụ thuộc architecture / requirement
```

Thiết kế đo đã rõ; thứ còn thiếu là **định nghĩa sản phẩm** (record 22) và **xác nhận rằng ba chế độ vẫn là claim chính thức**.

**Câu hỏi buộc lựa chọn**

```text
Ta đang hỏi "có khác biệt không" hay "khác biệt có nhận ra được không"?
```

Nếu là **có khác biệt không** → so sánh ghép cặp + thống kê hoán vị; không cần mô hình nào.
Nếu là **có nhận ra được không** → mới cần bộ phân loại hoặc judge — và khi đó phải kiểm chéo hai cách, vì nếu bộ phân loại tất định làm ngang judge thì judge **không thêm bằng chứng ngữ nghĩa nào**, nó chỉ đang đọc tín hiệu bề mặt như độ dài.

**Vì sao thiết kế này, chứ không phải một điểm số**
Ba lý do có bằng chứng:

1. Mỗi lần chạy đã tự khác nhau sẵn. Một nghiên cứu quy mô lớn trên nhiều mô hình và nhiều tác vụ cho thấy tính bất định tồn tại **ngay trong cấu hình được kỳ vọng là tất định**. Không có nhóm đối chứng chạy lặp thì mọi khác biệt quan sát được không phân biệt được với nhiễu.
2. Hiệu ứng của việc điều kiện hóa theo đối tượng, ở các nghiên cứu gần nhất, đều **nhỏ**. Hiệu ứng nhỏ mà so trung bình trên nhiều tài liệu khác nhau thì tín hiệu sẽ bị chôn dưới phương sai giữa các tài liệu → **bắt buộc ghép cặp theo nguồn**.
3. Chấm điểm cấu trúc bằng giám khảo là chỗ **yếu nhất** của judge: tương quan với người ở chiều mạch nội dung chỉ **0,55**, thấp hơn hẳn chiều thiết kế **0,90**; và một nghiên cứu về độ ổn định xếp mạch nội dung vào nhóm bất ổn nhất khi diễn đạt lại prompt. Chuyển câu hỏi sang dạng **so sánh và lựa chọn** thay vì chấm điểm là cách né đúng điểm yếu này, vì giám khảo mô hình mạnh hơn ở so sánh.

**Có thể thất bại ở đâu**
Khả năng thật: mức dao động **trong cùng một chế độ** lớn hơn mức khác biệt **giữa các chế độ**. Nếu vậy thì không phải "điều chỉnh phép đo" — mà toàn bộ chiến lược đo chế độ phải xem lại. Precedent bên ngoài cho thấy điều kiện hóa theo đối tượng **có thể đi sai hướng**: trong một benchmark, một hệ thống cho lợi thế **âm** khi được yêu cầu nhắm đối tượng chuyên gia. Ghi chú trung thực: con số đó trong hồ sơ hiện tại mới đọc qua bản tóm tắt tự động, chưa đối chiếu bản gốc — nên dùng như dấu hiệu định tính, không dùng như số trích dẫn.

**Transfer risk: MEDIUM** (cơ chế chuyển giao tốt; các con số cụ thể thì không)

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        MEDIUM–HIGH   (chi phí nằm ở việc SINH deck: số nguồn × 3 chế độ × R lần)
Độ trễ:         MEDIUM
Repeatability:  HIGH cho phần tính toán / phần sinh deck thì chính là thứ đang được đo
```

**Cần pilot riêng? YES**
Pilot đầu tiên phải là loại **thăm dò**: chạy trên một tập nguồn nhỏ, mô tả mức dao động và hình dạng tín hiệu, **không** dùng kiểm định thống kê hay giá trị p làm bằng chứng ở bước này. Chỉ khi thấy tín hiệu đáng theo mới thiết kế một thí nghiệm khẳng định trên tập nguồn độc lập.

**Phụ thuộc kiến trúc: MEDIUM** · **Status: PAUSED**

---

## 22. Purpose-conditioned information selection

**A. Ta muốn biết gì?** Ba chế độ có **chọn tập thông tin khác nhau** không — tức là khác nhau ở *nói gì*, chứ không chỉ ở *trình bày dày hay mỏng*.

**B. Metric / Signal** mức phủ các fact **bắt buộc theo chế độ**, và danh sách fact bị lược bỏ theo từng nhóm kỳ vọng.

**C. Có thể hỗ trợ claim** Rằng khác biệt giữa các chế độ là **có chủ đích**, không phải ngẫu nhiên hay chỉ ở bề mặt.

**D. KHÔNG thể tự chứng minh**
Không chứng minh chế độ nào "tốt hơn"; không thay thế được sàn chất lượng; và **không tự động đúng** — nó chỉ đo được khi đã có một định nghĩa sản phẩm để so.

**E. Dữ liệu kiểm thử cần có**
Toàn bộ dữ liệu của record 2, **cộng thêm** một lớp: với mỗi fact, mức kỳ vọng theo từng chế độ (bắt buộc với mọi chế độ / bắt buộc với chế độ này / tùy chọn / không kỳ vọng). Lớp này phải được viết và duyệt **trước khi nhìn output**, có người viết, người duyệt, lý do và phiên bản.

**F. Bản chất phép đo** Hybrid.

**G/H. Best Current Candidate**

```text
PAUSED — phụ thuộc product definition
```

**Vì sao PAUSED chứ không phải OPEN**
Khác biệt quan trọng: `OPEN` nghĩa là *chưa tìm được công cụ*; `PAUSED` ở đây nghĩa là **chưa tồn tại thứ cần đo**. Ba chế độ hiện chưa có định nghĩa cụ thể về mục đích và nghĩa vụ chọn lọc thông tin. Không có định nghĩa thì không có kỳ vọng; không có kỳ vọng thì phép đo **không thể fail**; và một phép đo không thể fail thì không phải bằng chứng.

Điều này **không giải được bằng nghiên cứu tài liệu**. Các vòng tìm kiếm không tìm thấy nguồn bên ngoài nào định nghĩa đúng bộ ba chế độ này hay một bộ tương đương. Nguồn gần nhất dùng trục **đối tượng người nghe** (chuyên gia / người học / người ra quyết định) — đó là một trục khác với trục **cách deck được dùng**, nên cơ chế đo có thể tham khảo nhưng **nội dung từng hồ sơ không được mượn**.

**Một mâu thuẫn cần biết trước khi định nghĩa**
Requirement nội bộ mô tả khác biệt giữa các chế độ nằm ở **cách dùng chữ/ảnh/mật độ nội dung**. Nguồn bên ngoài gần nhất lại kết luận rằng việc thích ứng theo đối tượng **chủ yếu thay đổi việc chọn thông tin nào**. Hai mệnh đề này trỏ vào hai chỗ khác nhau và dẫn tới hai bộ đại lượng khác nhau. Mâu thuẫn này **không giải được bằng lý lẽ** — chỉ giải được bằng cách đo xem tín hiệu thật nằm ở đâu, sau khi đã có định nghĩa.

**Câu hỏi buộc lựa chọn** (dùng khi đã có định nghĩa)

```text
Việc một fact vắng mặt trên deck là LỖI BỎ SÓT hay LƯỢC BỎ ĐÚNG Ý ĐỒ?
```

Chỉ trả lời được nếu mức kỳ vọng của fact đó theo chế độ đã được viết ra **trước**. Nếu chưa, mọi lời giải thích sau khi nhìn output đều hợp lý — và đó chính là thiên lệch xác nhận ở tầng giao thức.

**Transfer risk: HIGH** · **Chi phí MEDIUM–HIGH (công viết và duyệt) · Độ trễ LOW · Repeatability HIGH nếu tiêu chí được đóng băng**

**Cần pilot riêng? YES** — nhưng chỉ sau khi có định nghĩa.

**Phụ thuộc kiến trúc: MEDIUM** · **Status: PAUSED**

---

## 23. Surface diagnostic features

**A. Ta muốn biết gì?** Các đại lượng bề mặt của deck: số slide, số chữ mỗi slide, số gạch đầu dòng mỗi slide, số chữ mỗi gạch đầu dòng, tỉ lệ slide có ảnh, phân bố kiểu bố cục, tỉ lệ nén so với nguồn, mức bám theo thứ tự trình bày của nguồn.

**B. Metric / Signal** một nhóm chỉ số, báo riêng từng cái.

**C. Có thể hỗ trợ claim**
Rất hạn chế — và đó là điểm quan trọng nhất của record này. Chúng hữu ích để **chẩn đoán** (tín hiệu khác biệt đang nằm ở đâu) và để **phát hiện hành vi bất thường** (ví dụ deck đột nhiên dày chữ gấp đôi sau một thay đổi prompt).

**D. KHÔNG thể tự chứng minh**
**Không chứng minh chất lượng, và không chứng minh chế độ trình bày hoạt động đúng.** Đây là nhóm dễ bị dùng quá tầm nhất: chúng dễ đo, dễ khác nhau, nên rất dễ bị trình bày như bằng chứng rằng "hệ thống phản ứng đúng". Một hệ thống có thể "đạt" mọi phép so sánh bề mặt bằng cách nhồi chữ vào chế độ này và cắt chữ ở chế độ kia mà không hiểu gì về mục đích.

**E. Dữ liệu kiểm thử cần có** Output có cấu trúc (đếm được chữ, khối, kiểu bố cục) **+** văn bản nguồn cho các chỉ số so với nguồn. Cần cố định bộ tách từ, và **tách riêng theo ngôn ngữ** — không dùng công thức đếm/đọc của tiếng Anh cho tiếng Việt.

**F. Bản chất phép đo** Deterministic.

**G/H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: tính thẳng từ output có cấu trúc
```

**Câu hỏi buộc lựa chọn**

```text
Con số này đang được dùng để CHẨN ĐOÁN, hay đang được dùng làm BẰNG CHỨNG
rằng hệ thống làm đúng?
```

Nếu **chẩn đoán** → hợp lệ, giữ nguyên.
Nếu **làm bằng chứng** → không hợp lệ, trừ khi đã có định nghĩa sản phẩm nói rõ chiều kỳ vọng của chính đại lượng đó, viết ra trước khi đo.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: MEDIUM** · **Status: READY** (với đúng vai trò chẩn đoán)

---

# NHÓM 6 — Operational

Ba record dưới đây có chung một đặc điểm: **đo bằng telemetry, không cần giám khảo, không cần research**. Chúng cũng là nhóm duy nhất trả lời câu hỏi *"đổi lại chúng ta mất gì?"* — và không có nhóm nào khác thay được.

## 24. Latency

**A. Ta muốn biết gì?** Sinh một deck mất bao lâu, ở mức trung vị và ở mức đuôi chậm.

**B. Metric / Signal** `latency_p50`, `latency_p95`, và phân rã theo từng bước trong đường xử lý.

**C. Có thể hỗ trợ claim** Rằng sản phẩm dùng được trong thực tế; và rằng một cải thiện chất lượng **có** hoặc **không** phải trả giá bằng thời gian.

**D. KHÔNG thể tự chứng minh** Không nói gì về chất lượng. Nhanh hơn không phải tốt hơn.

**E. Dữ liệu kiểm thử cần có** Run log có dấu thời gian ở từng bước.

**F. Bản chất phép đo** Telemetry.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: đo wall-clock có phân rã theo bước
```

**Có thể thất bại ở đâu** Nếu không phân rã theo bước, con số tổng không cho biết phải tối ưu ở đâu. Nếu không cố định môi trường đo, so sánh giữa hai ngày không có nghĩa.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability MEDIUM (phụ thuộc tải hệ thống và nhà cung cấp mô hình)**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY**

---

## 25. Cost

**A. Ta muốn biết gì?** Mỗi deck tốn bao nhiêu tiền gọi mô hình, và tiền đó tiêu vào bước nào.

**B. Metric / Signal** `cost_per_deck`, số lần gọi mô hình, số token vào/ra, phân rã theo bước.

**C. Có thể hỗ trợ claim** Rằng kiến trúc khả thi về mặt kinh tế; và — quan trọng cho phần bảo vệ — rằng nhóm **biết** cái giá của mỗi cải thiện chất lượng. Có một requirement nội bộ yêu cầu rõ việc báo cáo đánh đổi giữa số lần gọi mô hình và chất lượng.

**D. KHÔNG thể tự chứng minh** Không nói gì về chất lượng.

**E. Dữ liệu kiểm thử cần có** Run log ghi số lần gọi, số token, và mô hình được dùng ở mỗi lần.

**F. Bản chất phép đo** Telemetry.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: đếm token và số lần gọi, quy đổi theo bảng giá tại thời điểm đo
```

**Có thể thất bại ở đâu** Giá mô hình thay đổi theo thời gian → phải ghi lại **ngày đo và bảng giá đã dùng**, nếu không thì đường biểu diễn chi phí qua các phiên bản trộn lẫn hai loại thay đổi.

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH (số token) / MEDIUM (quy đổi tiền)**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY**

---

## 26. Reliability / retry & failure rate

**A. Ta muốn biết gì?** Bao nhiêu lần chạy thất bại, bao nhiêu lần phải thử lại, và hệ thống hành xử thế nào khi một phần thất bại.

**B. Metric / Signal** `failure_rate`, `retry_rate`, phân bố số lần thử lại, tỉ lệ thất bại một phần.

**C. Có thể hỗ trợ claim** Rằng hệ thống ổn định. Đây là nhóm tín hiệu **bắt regression sớm nhất** vì nó phản ứng ngay với các thay đổi làm hỏng đường xử lý.

**D. KHÔNG thể tự chứng minh** Không nói gì về chất lượng — và ví dụ ở record 9 cho thấy hai trục này có thể đi ngược chiều nhau.

**E. Dữ liệu kiểm thử cần có** Run log có phân loại nguyên nhân thất bại theo bước.

**F. Bản chất phép đo** Telemetry.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: đếm trên run log, phân loại theo bước và nguyên nhân
```

**Transfer risk: LOW** · **Chi phí LOW · Độ trễ LOW · Repeatability HIGH**

**Cần pilot riêng? NO** · **Phụ thuộc kiến trúc: LOW** · **Status: READY**

---

# NHÓM 7 — Comparative evidence

Bốn record dưới đây **không phải metric** mà là **giao thức so sánh**: chúng quyết định con số của các nhóm trên được đem so với cái gì. Nguyên tắc bao trùm: **mỗi loại so sánh trả lời đúng một câu hỏi, và không loại nào thay được loại nào.**

```text
So với phiên bản trước    → "Chúng ta có tiến bộ không?"
So với cách làm đơn giản  → "Kiến trúc có đáng độ phức tạp không?"
Tắt một component         → "Component này đóng góp gì?"
So với hệ thống bên ngoài → "Chúng ta đang ở đâu trên bản đồ?"
```

Sai lầm tốn kém nhất là dùng một loại để trả lời câu hỏi của loại khác.

## 27. Historical regression

**A. Ta muốn biết gì?** Phiên bản hôm nay có tốt hơn phiên bản hôm qua không, và nếu tệ hơn thì tệ ở chỗ nào.

**B. Metric / Signal** không phải một metric mới — là **giao thức chạy lại** toàn bộ các metric đã chọn trên một benchmark đóng băng.

**C. Có thể hỗ trợ claim** Rằng nhóm cải thiện sản phẩm liên tục và có bằng chứng cho từng bước. Đây là một trong hai câu hỏi xuyên suốt của cả đồ án.

**D. KHÔNG thể tự chứng minh** Không nói gì về việc sản phẩm có tốt **so với bên ngoài** không, và không nói gì về việc **thành phần nào** tạo ra cải thiện.

**E. Dữ liệu kiểm thử cần có**

```text
1. benchmark đóng băng (bộ input cố định, có mã băm phiên bản)
2. run manifest cho mỗi lần chạy, tối thiểu gồm:
   phiên bản benchmark · model + snapshot · prompt nguyên văn (cả hệ thống lẫn người dùng)
   · cấu hình sinh · số lần chạy · phiên bản schema · commit của hệ thống
   · model + prompt của giám khảo nếu có
3. N ≥ 3 lần chạy cho các metric phụ thuộc mô hình; báo trung vị + khoảng
4. một tập giữ riêng, tách khỏi tập dùng để tinh chỉnh prompt
```

**F. Bản chất phép đo** Giao thức.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: benchmark đóng băng + run manifest + N lần chạy có báo phương sai
```

**Câu hỏi buộc lựa chọn**

```text
Chênh lệch quan sát được có lớn hơn mức dao động khi chạy lại
đúng cùng một cấu hình hay không?
```

Nếu **có** → được phép gọi là tín hiệu.
Nếu **không** → **không được gọi là cải thiện**, dù con số có tăng.

**Vì sao chặt chẽ đến vậy**
Cả hai phía của phép so đều ngẫu nhiên. Một nghiên cứu quy mô lớn ghi nhận dao động đáng kể giữa các lần chạy được kỳ vọng là tất định. Ngoài ra: đổi cách diễn đạt prompt có thể đổi kết quả rất mạnh, và trong một nghiên cứu khác, đổi template thậm chí **đổi cả thứ hạng tương đối giữa các hệ thống**. Nếu chỉ chạy một lần mỗi phiên bản, quyết định gộp hay hoàn tác sẽ dựa trên số vô nghĩa.

Một nguyên tắc nữa: **mỗi lần chỉ đổi một thứ**. Nếu buộc phải gộp nhiều thay đổi, phải ghi rõ trong báo cáo rằng chênh lệch **không quy được** cho nguyên nhân nào.

**Có thể thất bại ở đâu** Nếu benchmark, model hay schema đổi cùng lúc với hệ thống, chuỗi so sánh đứt. Cách xử lý đúng là **đánh dấu điểm đứt và bắt đầu chuỗi mới**, không nối liền đường biểu diễn.

**Transfer risk: LOW** · **Chi phí LOW–MEDIUM · Độ trễ MEDIUM · Repeatability phụ thuộc metric bên dưới**

**Cần pilot riêng? NO** — nhưng cần benchmark và run manifest tồn tại trước. Đó là việc kỹ thuật, không phải research.

**Phụ thuộc kiến trúc: LOW** · **Status: READY**

---

## 28. External baseline comparison

**A. Ta muốn biết gì?** So với các hệ thống sinh slide khác, Deck Agent đang ở đâu.

**B. Metric / Signal** giao thức so sánh, không phải metric mới.

**C. Có thể hỗ trợ claim** Chỉ một loại claim, và phải phát biểu đúng phạm vi: *"tại ngày X, ở cấu hình mặc định của sản phẩm đó, trên bộ input Y, với cách chấm Z, hệ thống của chúng tôi ở vị trí này."*

**D. KHÔNG thể tự chứng minh**
**Không chứng minh được bất kỳ điều gì về kiến trúc.** Thắng một sản phẩm khác không có nghĩa là thiết kế của mình tốt hơn — có thể chỉ vì mô hình bên dưới mạnh hơn, hoặc vì bài toán không giống nhau.

**E. Dữ liệu kiểm thử cần có** Cùng bộ input cho mọi hệ thống **+** một bước chuẩn hóa output về cùng dạng trước khi chấm **+** ghi lại ngày, phiên bản/cấu hình của từng hệ thống.

**F. Bản chất phép đo** Giao thức + các metric đã chọn.

**G. Candidate evaluators**
Về mặt giao thức, có tiền lệ rõ: một benchmark cấp **cùng tài liệu nền và cùng chỉ dẫn** cho mọi phương pháp rồi yêu cầu tạo ra deck hoàn chỉnh ở định dạng chuẩn hóa; một benchmark khác bổ sung bước chuyển output về **chuỗi ảnh slide** trước khi chấm để mọi hệ thống được nhìn như nhau.

**H. Best Current Candidate**

```text
BEST CURRENT CANDIDATE: cùng input, chuẩn hóa output, ghi rõ ngày và cấu hình — có điều kiện
```

**Câu hỏi buộc lựa chọn**

```text
Ta có kiểm soát được mô hình và cấu hình bên trong hệ thống kia không?
```

Nếu **có** (hệ thống mã nguồn mở, tự chọn được mô hình) → so sánh chặt chẽ hơn được, vì loại bỏ được biến "mô hình khác nhau".
Nếu **không** (sản phẩm thương mại đóng) → kết quả chỉ là **một tấm ảnh chụp tại một thời điểm**, không lặp lại được, và câu trong báo cáo phải phản ánh đúng điều đó.

**Có thể thất bại ở đâu — bốn cái bẫy cụ thể**

1. **So điểm từ hai thang khác nhau.** Lấy điểm Deck Agent đo bằng metric của nhóm rồi đặt cạnh bảng điểm đã công bố của một benchmark khác là **không hợp lệ** — khác input, khác cách chấm, khác giám khảo. Chỉ hợp lệ nếu Deck Agent chạy đúng benchmark đó, đúng bộ instance, đúng giao thức chấm.
2. **Đặt hệ thống kia vào điều kiện nó không được thiết kế để chạy.** Ví dụ cụ thể: có một hệ sinh presentation mã nguồn mở **cần một deck tham chiếu làm đầu vào**. Không cấp deck tham chiếu là làm yếu nó một cách bất công; cấp deck tham chiếu mà Deck Agent không có là cho nó lợi thế. "Cùng sinh slide" **không** có nghĩa là "cùng bài toán".
3. **Thiên lệch ưu ái đồng loại khi dùng giám khảo mô hình.** Đây là chỗ nguy hiểm nhất, vì hai nhánh so sánh dùng mô hình khác nhau, và có bằng chứng rằng judge nghiêng về output cùng họ với mình.
4. **Gọi mọi hệ thống khác là "đối thủ".** Phần lớn chúng đúng hơn khi mô tả là *hệ thống liên quan* hoặc *đối chiếu kiến trúc*.

**Transfer risk: MEDIUM**

**Chi phí / Độ trễ / Repeatability**

```text
Chi phí:        HIGH        (tiền, công chạy tay, và rủi ro kết quả hết hạn theo thời gian)
Độ trễ:         HIGH
Repeatability:  LOW cho sản phẩm đóng / MEDIUM cho hệ thống mã nguồn mở
```

**Cần pilot riêng? TBD**
Có một hướng rẻ hơn nhiều cần cân nhắc **trước** khi bỏ công dựng so sánh thủ công: **tự chạy Deck Agent trên một benchmark công khai đã có bảng điểm nhiều hệ thống**. Khi đó không cần chạy sản phẩm của ai cả. Việc cần kiểm trước: chi phí chạy phần chấm, giấy phép dữ liệu, và liệu loại input của benchmark đó có khớp phạm vi Deck Agent không.

**Phụ thuộc kiến trúc: MEDIUM** · **Status: CANDIDATE**

---

## 29. Architecture baseline

**A. Ta muốn biết gì?** Kiến trúc nhiều bước có thực sự tốt hơn cách làm đơn giản nhất — một lần gọi mô hình từ nội dung đã trích xuất ra thẳng output — hay không.

**B. Metric / Signal** giao thức so sánh giữa hai nhánh, đọc trên nhiều trục metric cùng lúc.

**C. Có thể hỗ trợ claim** Rằng độ phức tạp của thiết kế được trả giá xứng đáng. Đây là claim mà phần bảo vệ đồ án cần nhất.

**D. KHÔNG thể tự chứng minh**
Không quy được toàn bộ chênh lệch cho **một** component cụ thể. Nhánh đơn giản bỏ nhiều thứ cùng lúc; nói "component X đóng góp N điểm" từ phép so này là sai loại suy luận — đó là câu hỏi của record 30.

**E. Dữ liệu kiểm thử cần có**
Cùng benchmark, cùng metric, **cộng thêm một danh sách cân biến** phải thỏa: cùng mô hình và snapshot · nhánh đơn giản nhận **cùng nội dung đã trích xuất** chứ không phải tài liệu thô bị cắt · được cho biết **cùng ràng buộc** · có **cùng chính sách validate và thử lại** · được tinh chỉnh prompt tử tế (chạy nhiều biến thể, lấy biến thể tốt nhất hoặc báo khoảng dao động) · không bị ép sinh định dạng khó hơn.

**F. Bản chất phép đo** Giao thức.

**H. Best Current Candidate**

```text
PAUSED — phụ thuộc architecture
```

Không thể định nghĩa "cách làm đơn giản nhất" trước khi biết cái phức tạp trông thế nào.

**Câu hỏi buộc lựa chọn** (dùng khi mở lại)

```text
Baseline này thiếu ĐÚNG cơ chế nào so với hệ thống của chúng ta?
```

Nếu trả lời được bằng **một câu** → baseline đã được định nghĩa đúng.
Nếu phải liệt kê nhiều thứ → phép so sẽ không quy được nguyên nhân, và cần tách thành nhiều thí nghiệm.

**Vì sao phải cẩn thận đến vậy**
Rủi ro không phải là baseline quá mạnh mà là **baseline bù nhìn**. Có bằng chứng ở quy mô cả một lĩnh vực: trong một phân tích tái lập, phần lớn phương pháp mới được tái lập lại **thua các heuristic đơn giản khi baseline được tinh chỉnh tử tế**. Nếu nhánh đơn giản của Deck Agent thua vì không được cho biết ràng buộc hay không có validate/retry, thì chênh lệch đo được là chênh lệch **công sức**, không phải **thiết kế** — và phần đánh giá kiến trúc trong báo cáo sẽ không đứng vững trước phản biện.

Một cam kết nên viết ra **trước khi chạy**: kết quả "cách làm đơn giản mạnh bất ngờ" cũng là một phát hiện hợp lệ. Khi đó đóng góp thật của thiết kế nằm ở chỗ khác — khả năng kiểm soát, truy vết nguồn, khả năng chỉnh sửa, chi phí — chứ không ở chất lượng slide thô. Viết cam kết này trước giúp tránh việc sau đó đi làm yếu baseline để cứu kết luận.

**Transfer risk: không áp dụng** (đây là giao thức tự dựng)

**Chi phí MEDIUM · Độ trễ MEDIUM · Repeatability phụ thuộc metric bên dưới**

**Cần pilot riêng? TBD** · **Phụ thuộc kiến trúc: HIGH** · **Status: PAUSED**

---

## 30. Component ablation

**A. Ta muốn biết gì?** Một component cụ thể đóng góp bao nhiêu: tắt nó đi thì kết quả xấu đi theo trục nào.

**B. Metric / Signal** giao thức so sánh có kiểm soát, đọc **đồng thời** trên ít nhất ba trục: một trục chất lượng, một trục độ tin cậy, một trục vận hành.

**C. Có thể hỗ trợ claim** Rằng một lựa chọn thiết kế cụ thể có giá trị đo được.

**D. KHÔNG thể tự chứng minh**
**Không** chứng minh giá trị của **cả kiến trúc** — nó chỉ đo một component bên trong, trong khi mọi thứ còn lại vẫn nguyên. Câu hỏi về cả kiến trúc là record 29.

**E. Dữ liệu kiểm thử cần có** Cùng benchmark, cùng mô hình, cùng prompt — chỉ tắt **một** component mỗi lần. Yêu cầu kỹ thuật đi kèm: hệ thống nên bật/tắt component **bằng cấu hình**, không phải bằng cách tách nhánh mã.

**F. Bản chất phép đo** Giao thức.

**H. Best Current Candidate**

```text
PAUSED — phụ thuộc architecture
```

**Câu hỏi buộc lựa chọn** (dùng khi mở lại)

```text
Hai thí nghiệm này có đang can thiệp vào ĐÚNG CÙNG một chỗ không?
```

Nếu **có** → chúng là **một** thí nghiệm, phải gộp lại; chạy và báo cáo như hai bằng chứng độc lập là tự nhân đôi bằng chứng một cách sai.
Nếu **không** → phải viết rõ mỗi cái hỏi câu hỏi nhân quả nào **trước khi** cài đặt.

**Vì sao đọc ablation trên một điểm tổng là sai**
Có precedent trực tiếp trong đúng lĩnh vực sinh presentation. Một hệ thống công bố bảng ablation bỏ từng component một, giữ nguyên mô hình:

| Cấu hình | Tỉ lệ thành công (%) | Điểm chất lượng trung bình |
| --- | ---: | ---: |
| đầy đủ | 95,0 | 3,67 |
| bỏ bước lập outline | 91,0 | 3,30 |
| bỏ bước trích xuất schema | 78,8 | 3,45 |
| bỏ bước phân tích cấu trúc | 92,2 | 3,32 |
| bỏ bước render bằng mã | 74,6 | 3,66 |

Đọc kỹ hàng cuối: bỏ bước render bằng mã làm điểm chất lượng gần như **không đổi** (3,66 so với 3,67) nhưng làm tỉ lệ thành công **rơi hơn 20 điểm**. Nếu chỉ nhìn điểm chất lượng, kết luận sẽ là "component này vô dụng" — sai hoàn toàn. Ngược lại, bỏ bước lập outline hại **mạch nội dung** nhiều nhất chứ không hại tỉ lệ thành công.

Kèm theo hai quy tắc đọc:

- **Không cộng dồn đóng góp** giữa các ablation. Quan hệ giữa lượng cơ chế và kết quả thậm chí không đơn điệu.
- **Không kết luận từ một lần chạy** — xem record 27.

**Transfer risk: không áp dụng** (giao thức tự dựng; precedent chỉ dùng cho **phương pháp**, không dùng để so số)

**Chi phí MEDIUM · Độ trễ MEDIUM · Repeatability phụ thuộc metric bên dưới**

**Cần pilot riêng? TBD** · **Phụ thuộc kiến trúc: HIGH** · **Status: PAUSED**

---

# 2. Tổng kết — đọc nhanh

## 2.1 Đã có Best Current Candidate đủ rõ

**Nhóm tất định, dùng được ngay khi hệ thống chạy (READY):**
`derived-number routing` · `source traceability` · `artifact validity` · `export success rate` · `brief constraint compliance` · `overflow` · `overlap / out-of-bounds` · `design-token conformance` · `geometric design correctness` · `technical ready-to-use` · `surface diagnostic features` · `latency` · `cost` · `reliability` · `historical regression`

**Nhóm có hướng mạnh nhưng phải pilot (CANDIDATE):**
`source grounding` và `unsupported claim count` → MiniCheck, có điều kiện
`perceptual design quality` → giám khảo đa phương thức + rubric chung trên ảnh render
`editability` → thang phân cấp nhiều mức có quy tắc loại trừ
`external baseline comparison` → cùng input, chuẩn hóa output, ghi rõ ngày và cấu hình

## 2.2 Chưa có winner đủ cơ sở (OPEN)

`coverage / important-fact presence` — **ô trống quan trọng nhất**
`hallucination` và `incorrectness` khi cần tách riêng
`derived-number correctness`
`visual hierarchy / layout appropriateness`
`actual user edit effort`

## 2.3 Không nên nghiên cứu tiếp lúc này (PAUSED)

`schema validity của intermediate representation` · `same-source mode differentiation` · `purpose-conditioned information selection` · `architecture baseline` · `component ablation`

Điều kiện mở lại của từng mục nằm ở `01_EVALUATION_DEPENDENCY_MAP.md` và `03_UNRESOLVED_AND_REACTIVATION_MAP.md`.

## 2.4 Ba cảnh báo cuối

1. **Không có metric nào ở đây được phép cộng thành một điểm tổng.** Mỗi lần gộp là một lần mất khả năng chẩn đoán, và chẩn đoán mới là thứ giúp sửa sản phẩm.
2. **Một ứng viên đứng đầu bảng vẫn có thể fail.** Nếu MiniCheck fail pilot, các công cụ bị loại ở trên **không** tự động quay lại — chúng bị loại vì lý do riêng, không phải vì xếp sau.
3. **Ba nhóm PAUSED chính là phần có giá trị cao nhất cho phần bảo vệ đồ án.** Chúng bị hoãn không phải vì kém quan trọng mà vì chưa thể định nghĩa. Làm đúng thứ tự rẻ hơn làm lại.

