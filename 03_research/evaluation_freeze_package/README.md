# Evaluation Freeze Package — Gói tổng hợp cuối trước khi tạm dừng evaluation research

**Ngày lập:** 2026-08-20
**Trạng thái:** FINAL SYNTHESIS PACKAGE — **không phải Final Evaluation Framework của Deck Agent**
**Phạm vi:** toàn bộ kiến thức evaluation đã tích lũy, viết lại thành dạng tự chứa

---

## 0. Đọc file này nếu bạn chưa biết gì về evaluation

Deck Agent là hệ thống sinh bộ slide (deck) từ tài liệu nguồn. Câu hỏi tự nhiên là: **làm sao biết nó làm tốt?**

Câu trả lời "mở file ra xem thấy đẹp" không dùng được, vì ba lý do:

1. Hôm nay xem thấy đẹp, tuần sau sửa prompt rồi xem lại — **không nhớ được** cái nào tốt hơn.
2. Mỗi lần chạy lại, mô hình ngôn ngữ có thể cho kết quả khác nhau; **khác không có nghĩa là tốt hơn**.
3. Khi bảo vệ đồ án, "chúng tôi thấy nó tốt" không phải bằng chứng.

Vì vậy cần một **hệ thống đo lường**: chọn trước những thứ cần chứng minh, chọn trước cách đo, đóng băng bộ dữ liệu kiểm thử, rồi đo lại y hệt qua từng phiên bản.

Gói tài liệu này là **kết quả nghiên cứu về việc nên đo gì và đo bằng gì** — được viết lại để một người chưa từng theo dõi quá trình research vẫn đọc hiểu và dùng được.

### Gói này KHÔNG phải cái gì

- Không phải danh sách metric cuối cùng sẽ implement.
- Không phải benchmark (bộ bài kiểm thử chuẩn hóa để so sánh giữa các phiên bản).
- Không phải kết quả thí nghiệm — **chưa có pilot nào được chạy**.
- Không phải quyết định kiến trúc.

Gói này là **bản đồ**: khi kiến trúc và các claim của sản phẩm được chốt, mở bản đồ này ra sẽ biết ngay phải đo gì, đo bằng gì, cái gì đã đủ chắc, cái gì còn phải thử nghiệm, và cái gì phải nghiên cứu lại.

---

## 1. Mental model — chuỗi suy nghĩ xuyên suốt cả gói

Đây là chuỗi quan trọng nhất trong gói tài liệu này. Mọi file còn lại đều là một lát cắt của chuỗi này.

```text
Product / System Claim
(điều sản phẩm tuyên bố mình làm được)
        ↓
Ta cần bằng chứng gì để tin claim đó?
        ↓
Metric / Signal nào biểu diễn được bằng chứng đó?
        ↓
Cần loại dữ liệu kiểm thử nào để tính metric đó?
        ↓
Evaluator / Tool nào có thể thực sự đo?
        ↓
Tool đó đã đủ đáng tin trong bối cảnh Deck Agent chưa?
        ↓
Nếu chưa → chạy pilot (thí nghiệm nhỏ, rẻ, có thể fail)
        ↓
Chỉ sau đó mới đưa vào Benchmark / Evaluation Harness
```

**Điểm mấu chốt:** chuỗi này bắt đầu từ **claim**, không bắt đầu từ **tool**.

Cách làm sai thường gặp:

> "Paper này có metric hay quá, mình dùng thử xem sao."

Cách làm đúng:

> "Chúng ta tuyên bố deck không bịa thông tin. Muốn tin điều đó cần bằng chứng gì? Bằng chứng đó biểu diễn thành con số nào? Con số đó cần dữ liệu gì? Có tool nào tính được không?"

Khi đi từ tool ngược lên, ta hay đo được thứ dễ đo thay vì thứ cần chứng minh.

---

## 2. Metric ≠ Evaluator Tool

Đây là phân biệt bị nhầm nhiều nhất, và nó quyết định cách đọc file `02_EVALUATION_TOOL_SELECTION_GUIDE.md`.

- **Metric / signal** = *điều ta muốn biết*, đã được phát biểu thành một đại lượng.
- **Evaluator / tool** = *cơ chế cụ thể* dùng để tính ra đại lượng đó.

Một metric có thể được đo bằng nhiều evaluator khác nhau, với chi phí và độ tin cậy rất khác nhau:

```text
Metric:
coverage (mức độ bao phủ các ý cần có)

Evaluator có thể là:
- so khớp ngữ nghĩa (semantic matching)
- đặt câu hỏi thăm dò rồi xem deck trả lời được không (question/probe based)
- LLM-as-a-Judge (dùng mô hình ngôn ngữ làm giám khảo)
- người đọc và đối chiếu (human annotation)
```

Ngược lại, có metric mà evaluator gần như chỉ có một lựa chọn hợp lý:

```text
Metric:
overflow (chữ tràn ra ngoài khung/slide)

Evaluator:
kiểm tra hình học bằng chương trình (programmatic geometry check)
```

**Hệ quả thực tế:** khi một hàng trong Tool Selection Guide ghi `OPEN`, điều đó gần như luôn có nghĩa là **metric đã rõ nhưng evaluator chưa rõ** — chứ không phải "chưa biết muốn đo gì".

---

## 3. Từ vựng dùng trong cả gói

Giải nghĩa một lần, sau đó dùng thẳng thuật ngữ tiếng Anh:

- **content fidelity (mức độ nội dung bám đúng nguồn)** — nội dung trên slide có trung thành với tài liệu nguồn không.
- **source grounding (mức độ nội dung được nguồn hỗ trợ)** — từng câu/ý trên deck có tìm được chỗ chống đỡ trong nguồn không.
- **coverage (mức độ bao phủ các ý cần có)** — những ý quan trọng của nguồn có xuất hiện trên deck không.
- **hallucination (bịa thông tin không có trong nguồn)** — deck nói một điều mà nguồn hoàn toàn không đề cập.
- **incorrectness (nói sai thông tin mà nguồn có đề cập)** — nguồn có nói về điều đó, nhưng deck nói sai.
- **derived number (số được suy ra/tính lại)** — ví dụ nguồn ghi `12,4M → 18,7M`, slide ghi `tăng ~51%`. Con số `51%` không có trong nguồn nhưng có thể đúng.
- **baseline (mốc/hệ thống dùng để so sánh)** — thứ được đem ra so để biết mình hơn hay kém.
- **ablation (thí nghiệm bỏ hoặc thay một thành phần để đo đóng góp)** — tắt một phần hệ thống rồi đo xem kết quả xấu đi bao nhiêu.
- **regression (sự suy giảm so với phiên bản trước)** — phiên bản mới làm tệ đi một thứ vốn đang tốt.
- **LLM-as-a-Judge (dùng mô hình ngôn ngữ làm giám khảo)** — cho một mô hình đọc output rồi chấm điểm hoặc chọn.
- **MLLM-as-a-Judge (giám khảo đa phương thức)** — như trên nhưng nhìn được cả ảnh render của slide.
- **benchmark (bộ bài kiểm thử chuẩn hóa để so sánh)** — tập input cố định, đóng băng, chạy lại y hệt qua mọi phiên bản.
- **noise floor (sàn nhiễu)** — mức dao động tự nhiên khi chạy lại đúng cùng một thứ. Chênh lệch nhỏ hơn mức này **không được gọi là cải thiện**.
- **pilot (thí nghiệm nhỏ để kiểm tra công cụ trước khi tin nó)** — chạy trên vài ca dựng sẵn để xem công cụ có phân biệt được đúng/sai không.
- **pre-registration (khai báo trước)** — viết trước kỳ vọng và tiêu chí đạt/không đạt, **trước khi** nhìn kết quả.
- **manipulation check (kiểm tra can thiệp)** — thí nghiệm kiểm xem việc thay đổi một biến đầu vào có thực sự tạo ra thay đổi như dự định ở đầu ra không.
- **repeatability (độ ổn định khi chạy lại)** — chạy lại y hệt có ra kết quả giống không.

---

## 4. Các file trong gói

| File | Trả lời câu hỏi gì |
| --- | --- |
| `README.md` (file này) | Cách đọc cả gói; mental model; cái gì cố ý chưa chốt |
| `01_EVALUATION_DEPENDENCY_MAP.md` | Điều nào vẫn cần đo dù kiến trúc thay đổi, điều nào phải chờ kiến trúc chốt |
| `02_EVALUATION_TOOL_SELECTION_GUIDE.md` | Với mỗi thứ cần đo: đo bằng gì, cần dữ liệu gì, tool nào là ứng viên tốt nhất hiện tại, khi nào chọn tool nào |
| `03_UNRESOLVED_AND_REACTIVATION_MAP.md` | Ta còn chưa biết gì, vì sao chưa chốt được, điều kiện nào khiến phải mở lại |
| `04_PPTAGENT_ARCHITECTURE_REVIEW_FRAME.md` | Khung để review PPTAgent như kiến trúc xuất phát — chỉ là khung, không phải kết luận kiến trúc |

Ba file `01`, `02`, `03` là bộ ba gắn với nhau:

```text
01 nói: mục này còn ý nghĩa không?
   ↓ nếu còn
02 nói: đo nó bằng gì, đã đủ chắc chưa?
   ↓ nếu chưa đủ chắc
03 nói: còn thiếu gì, khi nào nên nghiên cứu tiếp
```

---

## 5. How to use this package later — quy trình dùng lại

Khi kiến trúc và requirement của Deck Agent được chốt, làm theo thứ tự sau:

```text
Architecture + requirements được chốt
        ↓
Xác định final system claims
(sản phẩm tuyên bố làm được gì, đồ án muốn chứng minh điều gì)
        ↓
Mở 01_EVALUATION_DEPENDENCY_MAP.md
        ↓
Chọn những evaluation target vẫn còn applicable
(bỏ những target đo một cơ chế kiến trúc đã không còn tồn tại)
        ↓
Mở 02_EVALUATION_TOOL_SELECTION_GUIDE.md
        ↓
READY      → dùng được ngay, không cần research thêm
CANDIDATE  → có hướng mạnh, nhưng phải chạy pilot trước khi tin
OPEN       → chưa có lựa chọn thắng; cần một đợt research hẹp
PAUSED     → chỉ mở lại nếu điều kiện mở lại (ghi trong 01 và 03) xảy ra
        ↓
Sau đó mới xây Metric Registry
(bảng đăng ký metric có công thức chính xác)
        ↓
Benchmark
(bộ input đóng băng + dữ liệu kiểm thử kèm theo)
        ↓
Evaluation Harness
(chương trình chạy benchmark và xuất báo cáo so sánh)
```

**Nhấn mạnh — bốn thứ này khác nhau, không được gộp:**

```text
Evaluation Tool Selection Guide
≠ Final Metric Registry
≠ Benchmark Spec
≠ Pilot Result
```

- **Tool Selection Guide** nói: *"để đo X thì ứng viên tốt nhất hiện tại là Y, và đây là điều kiện để tin Y."*
- **Metric Registry** nói: *"metric X được định nghĩa bằng đúng công thức này, chạy ở nhịp này."*
- **Benchmark Spec** nói: *"bộ input là đúng các case này, kèm đúng các dữ liệu kiểm thử này, đóng băng ở phiên bản này."*
- **Pilot Result** nói: *"trên các ca dựng sẵn, công cụ Y hành xử đúng/sai như sau."*

Đi tắt từ Tool Selection Guide thẳng sang Benchmark là cách nhanh nhất để có một hệ đo trông có vẻ nghiêm túc nhưng không thể fail — và một phép đo không thể fail thì không phải bằng chứng.

---

## 6. Ba nguyên tắc đọc kết quả, đúng cho mọi metric trong gói

Ba nguyên tắc này lặp lại ở nhiều file. Ghi một lần ở đây.

### 6.1 Presence và correctness là hai câu hỏi khác nhau

**coverage** đo việc một fact/topic **có được đề cập hay không**. **correctness** đo việc nội dung được đề cập **có đúng hay không**.

Ví dụ: nguồn ghi *"Churn giảm từ 6,2% xuống 3,9%"*, deck ghi *"Churn giảm từ 6,2% xuống 1,9%"*.

- Về coverage: deck **đã đề cập** tới churn → tính là present.
- Về correctness: giá trị **sai** → báo lỗi riêng ở trục correctness.

Nếu gộp hai trục, cùng một lỗi bị đếm hai lần và coverage mất tính độc lập: không còn phân biệt được "phiên bản mới bỏ sót nhiều hơn" với "phiên bản mới nói sai nhiều hơn" — hai lỗi cần hai cách sửa hoàn toàn khác nhau.

### 6.2 Đo được khác biệt không có nghĩa là đo được chất lượng

Nhiều đại lượng bề mặt (số chữ mỗi slide, tỉ lệ slide có ảnh, độ nén so với nguồn) rất dễ đo và rất dễ khác nhau. Nhưng một hệ thống có thể tạo ra khác biệt rõ rệt trên các đại lượng đó mà **cả ba kết quả đều tệ**. Vì vậy mọi phép đo khác biệt phải được báo cáo **kèm một sàn chất lượng** (nội dung có bám nguồn không, file có hợp lệ không), không bao giờ báo cáo một mình.

### 6.3 Không có noise floor thì không có kết luận

Chạy lại đúng cùng một cấu hình vẫn cho kết quả khác nhau — cả ở phía hệ sinh deck lẫn ở phía công cụ chấm điểm. Việc đặt `temperature = 0` **không** đảm bảo kết quả giống hệt nhau. Vì vậy trước khi dùng bất kỳ con số nào để so hai phiên bản, phải chạy lặp lại nhiều lần trên cùng một đầu vào để biết mức dao động tự nhiên. Chênh lệch nhỏ hơn mức dao động đó không được gọi là cải thiện.

---

## 7. Những gì chưa được freeze

Gói này **cố ý không quyết định** những mục dưới đây:

- **final architecture** — kiến trúc cuối của Deck Agent.
- **final contribution** — đồ án cuối cùng tuyên bố đóng góp gì.
- **final metric set** — bộ metric chính thức sẽ implement.
- **final benchmark** — bộ input, số lượng case, cách phân tầng độ khó.
- **final evaluator stack** — chồng công cụ chấm điểm chính thức (model nào, prompt nào, cấu hình nào).
- **final threshold** — ngưỡng đạt/không đạt cho bất kỳ metric nào.
- **final weighting** — trọng số giữa các metric.
- **final overall score** — có hay không một điểm tổng, và nếu có thì tính thế nào.
- **final human sample size** — cần bao nhiêu người chấm, bao nhiêu deck.
- **final architecture ablation suite** — bộ thí nghiệm tắt/bật component để đo đóng góp kiến trúc.

**Vì sao hoãn:** phần lớn các quyết định trên phụ thuộc vào **kiến trúc cuối** và **system claim cuối** của Deck Agent, mà cả hai đều chưa được chốt. Chốt sớm sẽ tạo ra hai loại lãng phí: đo những cơ chế có thể không còn tồn tại, và đóng băng ngưỡng cho một hệ thống chưa biết hình dạng.

Một ngoại lệ đáng ghi: các con số ngưỡng đã xuất hiện trong các bản nháp trước đây (ví dụ `0,80`, `70%`, `80%`, `p<0,05`) chỉ là **ứng viên kỹ thuật**, không phải yêu cầu đã được chấp nhận và không phải chuẩn ngành. Không được trích chúng như tiêu chuẩn.

---

## 8. Điều đã đủ chắc để giữ dù kiến trúc thay đổi

Ngược lại với mục 7, năm điều dưới đây không phụ thuộc kiến trúc và nên được giữ:

1. **Tách phép đo tự động xác định (deterministic) khỏi phép đo cần phán đoán.** Thứ gì mã kiểm được thì để mã kiểm — rẻ hơn, ổn định hơn, và ở đúng loại việc đó thì chính xác hơn giám khảo mô hình.
2. **Không gộp bỏ sót / bịa / nói sai thành một điểm số.** Ba lỗi này cần ba hành động sửa khác nhau; gộp lại là mất khả năng chẩn đoán.
3. **Lưu run manifest (bản ghi đầy đủ mỗi lần chạy)** ngay từ bây giờ: phiên bản benchmark, model + snapshot, prompt (nguyên văn), cấu hình sinh, số lần chạy, phiên bản schema, commit của hệ thống. Không có nó thì so sánh giữa hai lần chạy vô nghĩa.
4. **Đóng băng tiêu chí trước khi nhìn output.** Tiêu chí viết sau khi xem kết quả sẽ luôn "đạt".
5. **Đo noise floor trước khi so sánh.** Xem mục 6.3.

---

## 9. Phụ lục — traceability nội bộ (chỉ dùng để đối chiếu, không cần đọc để hiểu gói này)

Phần này tồn tại để người quản lý research đối chiếu ngược với hồ sơ cũ. **Không có thông tin nào ở đây cần thiết để hiểu hoặc dùng gói tài liệu này**; toàn bộ nội dung đã được viết lại dạng tự chứa ở các file trên.

- Nguồn nội dung: kết quả research về evaluation dimension, source quality, `slide_type` differentiation, evaluator/judge landscape, baseline landscape; hai vòng gate review; và các quyết định đã được team xác nhận về việc tách coverage khỏi correctness, chính sách coverage theo mode, thứ bậc core/diagnostic của `slide_type`, cách xử lý trùng lặp giữa baseline và ablation, cấu trúc pilot source-quality, và nguyên tắc chạy smoke trước confirmatory.
- Gói này **không sửa** bất kỳ decision đã chốt nào, **không đóng** bất kỳ câu hỏi mở nào, và **không tạo** decision mới.
- Hai câu hỏi mở lớn của giai đoạn trước (phương pháp phát hiện presence độc lập với correctness; định nghĩa sản phẩm cho ba mode của `slide_type`) vẫn mở nguyên vẹn và được mô tả lại dạng tự chứa trong `03_UNRESOLVED_AND_REACTIVATION_MAP.md`.
