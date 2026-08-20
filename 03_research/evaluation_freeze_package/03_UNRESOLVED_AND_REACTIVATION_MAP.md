# Unresolved & Reactivation Map — Ta còn chưa biết gì, và khi nào nên quay lại

**Ngày lập:** 2026-08-20
**Trạng thái:** SYNTHESIS — không đóng, không mở, không quyết định câu hỏi nào
**Đọc trước:** `02_EVALUATION_TOOL_SELECTION_GUIDE.md` nếu muốn biết mỗi câu hỏi ảnh hưởng metric nào

---

## 0. Vì sao file này tồn tại

Một hồ sơ research trung thực phải ghi lại cả **những chỗ chưa biết**, không chỉ những chỗ đã kết luận. Nếu không, người đọc sau sẽ tưởng mọi ô trống là do quên, rồi lấp bằng phỏng đoán.

File này liệt kê từng vấn đề còn mở, và với mỗi vấn đề trả lời đủ tám câu:

```text
Vấn đề
Tại sao hiện chưa thể chốt
Điều gì đã biết
Điều gì còn thiếu
Có đáng research ngay không?
Trạng thái: OPEN / PAUSED
Điều kiện mở lại
Khi mở lại cần trả lời đúng câu hỏi nào
```

**Phân biệt hai trạng thái:**

- **OPEN** — câu hỏi hợp lệ và đã sẵn sàng để nghiên cứu; chỉ là **chưa tới lượt**.
- **PAUSED** — nghiên cứu bây giờ sẽ lãng phí, vì thứ cần đo hoặc chưa tồn tại, hoặc phụ thuộc một quyết định chưa được đưa ra.

Không mục nào trong file này là "cần làm ngay". Toàn bộ đang chờ kiến trúc và system claim cuối được chốt.

---

## 1. Bảng tổng

| # | Vấn đề | Trạng thái | Chặn cái gì |
| --- | --- | --- | --- |
| 1 | Phát hiện fact/topic có được đề cập hay không, độc lập với đúng/sai | OPEN | Toàn bộ nhánh coverage |
| 2 | Định nghĩa sản phẩm cho ba chế độ trình bày | PAUSED | Toàn bộ nhánh `slide_type` |
| 3 | Tách "bịa" khỏi "nói sai" | OPEN | Việc báo cáo hai con số riêng |
| 4 | Evaluator hiện đại xử lý được suy luận số tới đâu | OPEN | Mức độ tự động của nhánh số suy ra |
| 5 | Nhóm công cụ factuality có chuyển giao sang slide deck không | OPEN | Độ tin của toàn bộ nhánh source quality |
| 6 | Sàn nhiễu thật của hệ sinh deck và của giám khảo | OPEN | Mọi phép so sánh giữa hai phiên bản |
| 7 | Tiêu chí do người viết so với do mô hình sinh: mất bao nhiêu chất lượng | OPEN | Chi phí xây benchmark |
| 8 | Cần bao nhiêu người chấm và bao nhiêu deck | OPEN | Kế hoạch đánh giá cuối |
| 9 | Đo công sức chỉnh sửa của người dùng bằng gì | OPEN | Claim "dùng được ngay" |
| 10 | Bằng chứng mâu thuẫn về cách đo mức đầy đủ | OPEN | Lựa chọn phương pháp coverage |
| 11 | Trật tự thị giác và sự phù hợp của bố cục | OPEN | Phần cuối của nhánh design |
| 12 | Cách tổ chức toàn bộ hệ evaluation thành các nhóm | PAUSED | Cấu trúc tài liệu framework |
| 13 | Mỗi tài liệu cần bao nhiêu fact quan trọng | OPEN | Thiết kế benchmark |
| 14 | Nội dung nằm trong bảng và hình của tài liệu nguồn | OPEN | Phạm vi hiệu lực của coverage |
| 15 | Xử lý tiếng Việt trong các công thức đo | OPEN | Mọi metric có yếu tố đếm chữ |
| 16 | Đơn vị để kiểm: cả bullet hay mệnh đề nhỏ hơn | OPEN | Cách cài đặt nhánh source quality |
| 17 | Một số con số trích dẫn chưa đối chiếu bản gốc | OPEN | Việc đưa số vào báo cáo bảo vệ |

---

## 2. Chi tiết từng vấn đề

### 2.1 Phát hiện fact/topic có được đề cập hay không, độc lập với đúng/sai

**Vấn đề**
Cần một cách xác định: với một fact đã được đóng băng trước (ví dụ *"churn thay đổi thế nào"*), deck **có nhắc tới nó hay không** — kể cả khi deck diễn đạt lại, nén rất ngắn, hoặc **nói sai giá trị**.

**Tại sao hiện chưa thể chốt**
Cách hấp dẫn nhất về mặt hạ tầng là tái dùng đúng một công cụ cho cả hai chiều: lấy văn bản deck làm tài liệu chống đỡ và hỏi xem phát biểu chuẩn có được deck chống đỡ không. Nhưng khi làm vậy, **"deck nói sai về churn"** và **"deck không nhắc tới churn"** đều trả về cùng một nhãn phủ định. Tín hiệu đó đo *fact có được nói đúng không*, không đo *fact có được nhắc tới không*. Dùng nó làm coverage sẽ gộp lại đúng hai trục vừa được tách ra.

**Điều gì đã biết**

- Coverage và correctness **phải** là hai trục riêng: một fact được nhắc tới nhưng nói sai vẫn là *đã nhắc tới*, và lỗi giá trị được báo riêng.
- Hỏi mở kiểu *"deck này bỏ sót gì?"* là hướng yếu có tính cấu trúc: một benchmark chuyên về phát hiện thiếu sót cho thấy mô hình mạnh chỉ đạt khoảng 69,6% F1 ngay ở một tác vụ **dễ hơn** (được cấp cả bản gốc lẫn bản đã bị xóa bớt), với giải thích cơ chế rằng attention không có gì để chú ý vào ở chỗ trống.
- Đối chiếu theo **danh sách đóng** hiệu quả hơn hẳn rubric chung: trong một framework đánh giá tóm tắt, cách đối chiếu từng key fact đạt tương quan với người 0,688 trong khi judge rubric chung chỉ 0,314.
- Đo bằng độ tương đồng embedding là hướng yếu: nó không phân biệt "nói đúng ý quan trọng" với "chép nhiều chữ".

**Điều gì còn thiếu**
Một phương pháp cụ thể, có thể chạy tự động, phân biệt được *đề cập* với *đề cập đúng*. Chưa có ứng viên nào được chọn, và **không được** tự ý lấp bằng so khớp từ khóa, ngưỡng embedding, hay một biến thể hỏi–đáp mà không có kiểm chứng.

**Có đáng research ngay không?**
Không phải bây giờ — nhưng đây là **mục có giá trị cao nhất** trong danh sách này, và nó **không phụ thuộc kiến trúc**. Nếu chỉ được chọn một việc để làm khi quay lại evaluation, đây là việc đó.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Kiến trúc và system claim cuối vẫn giữ **mức bao phủ nội dung nguồn** như một điều cần theo dõi qua các phiên bản. Điều này gần như chắc chắn đúng với mọi kiến trúc sinh deck từ tài liệu, nên đây là mục dễ tái kích hoạt nhất.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Với một fact đã đóng băng, phương pháp nào xác định được deck có đề cập tới nó
hay không — kể cả khi deck diễn đạt lại, nén ngắn, hoặc nói sai giá trị —
mà KHÔNG biến phép đo thành một phép kiểm tính đúng?
```

Kèm điều kiện: phải có một bộ ca thử nhỏ với **hai nhãn do người gán tách rời** (một nhãn "có đề cập không", một nhãn "nói đúng không"), và phương pháp phải được chấm trên nhãn thứ nhất.

---

### 2.2 Định nghĩa sản phẩm cho ba chế độ trình bày

**Vấn đề**
Sản phẩm dự kiến có ba chế độ: hỗ trợ học/hiểu, đọc nhanh bắt kịp phần đã bỏ lỡ, và hỗ trợ người thuyết trình khi nói. Nhưng chưa có định nghĩa cụ thể cho từng chế độ: nó phục vụ mục đích gì, **bắt buộc phải giữ** loại thông tin nào, **được phép bỏ** loại thông tin nào.

**Tại sao hiện chưa thể chốt**
Đây **không phải câu hỏi nghiên cứu** — đây là **quyết định sản phẩm**. Các vòng tìm kiếm không tìm thấy nguồn bên ngoài nào định nghĩa đúng bộ ba chế độ này hoặc một bộ tương đương. Nguồn gần nhất dùng trục **đối tượng người nghe** (chuyên gia / người học / người ra quyết định), là một trục khác với trục **cách deck được dùng**. Cơ chế đo của nguồn đó tham khảo được; **nội dung từng hồ sơ thì không được mượn**.

Nếu định nghĩa được viết **sau khi** nhìn output, mọi khác biệt quan sát được đều giải thích được là "có chủ đích" — và phép đo mất khả năng fail. Đó là thiên lệch xác nhận ở tầng giao thức, không phải chi tiết kỹ thuật.

**Điều gì đã biết**

- Phần **lõi** của sự khác biệt giữa các chế độ nên là **mục đích** và **việc chọn thông tin nào**; các đại lượng bề mặt (mật độ chữ, tỉ lệ ảnh, kiểu bố cục, độ nén) là **chẩn đoán**, không phải định nghĩa. Lý do: đại lượng bề mặt dễ bị tối ưu rẻ tiền — nhồi chữ vào chế độ này, cắt chữ ở chế độ kia — nên "đạt" mà không chứng minh được gì về mục đích.
- Có một **mâu thuẫn nội tại cần biết trước khi định nghĩa**: yêu cầu nội bộ mô tả khác biệt nằm ở cách dùng chữ/ảnh/mật độ, trong khi nguồn bên ngoài gần nhất kết luận rằng thích ứng theo đối tượng **chủ yếu thay đổi việc chọn thông tin nào**. Hai mệnh đề trỏ vào hai chỗ khác nhau.
- Việc điều kiện hóa **có thể thất bại**: có precedent bên ngoài cho thấy prompt theo đối tượng không tự động khớp với hồ sơ mong muốn. Kết quả "chưa đạt" ở lần đo đầu là khả năng thật.
- Ghi chú phạm vi: phần ghi chú cho người thuyết trình hiện là hạng mục ưu tiên thấp trong danh sách yêu cầu, nên **không** dùng nó làm dấu hiệu phân biệt chính cho chế độ hỗ trợ thuyết trình — làm vậy là âm thầm đổi thứ tự ưu tiên của sản phẩm bằng một lựa chọn về đo lường.

**Điều gì còn thiếu**
Một bảng do team và giảng viên viết: mỗi chế độ phục vụ ai, trong tình huống nào, bắt buộc giữ gì, được phép bỏ gì — và với mỗi nghĩa vụ đó, một dấu hiệu quan sát được cụ thể để kiểm.

**Có đáng research ngay không?**
**Không — và nghiên cứu thêm cũng không giải quyết được.** Đây là việc phải ngồi lại quyết định, không phải việc phải đọc thêm tài liệu.

**Trạng thái: PAUSED**

**Điều kiện mở lại**
Team xác nhận ba chế độ **vẫn là một product claim chính thức** sau khi kiến trúc và phạm vi được chốt. Nếu chế độ bị bỏ, gộp, hoặc thay bằng một trục khác, toàn bộ nhánh này viết lại từ đầu.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Mỗi chế độ phục vụ mục đích gì, và mục đích đó buộc deck phải GIỮ loại thông tin nào
cũng như CHO PHÉP bỏ loại thông tin nào?
```

Và ngay sau đó:

```text
Mỗi nghĩa vụ trên biểu hiện thành dấu hiệu quan sát được nào,
để một người thứ ba kiểm được là đạt hay không đạt?
```

Cảnh báo bắt buộc khi mở lại: **không** thay thế bước này bằng một điểm số kiểu "mức phù hợp mục đích = 4/5". Một điểm số cảm tính che mất chính thứ cần định nghĩa.

---

### 2.3 Tách "bịa" khỏi "nói sai"

**Vấn đề**
Khi một phát biểu trên deck không tìm được chỗ dựa trong nguồn, cần biết đó là **bịa hẳn** (nguồn không nói gì về chuyện đó) hay **nói sai** (nguồn có nói, deck nói khác).

**Tại sao hiện chưa thể chốt**
Các công cụ hiện có đều trả về nhị phân "có/không được chống đỡ" — cả hai lỗi rơi vào cùng một ô. Việc tách chúng đòi hỏi **một bước thiết kế thêm**, và bước đó chưa có tiền lệ nào kiểm chứng.

**Điều gì đã biết**

- Có một quy tắc ứng viên: tra trong nguồn xem có phát biểu **đối ứng** khớp đồng thời bốn trường (chủ thể, thuộc tính, phạm vi, mốc thời gian) mà giá trị khác không. Có → *nói sai*. Chắc chắn không có → *bịa*. Bất kỳ trường nào không rõ → **không gán nhãn**.
- Quy tắc đó là **suy luận của chính dự án**, không có nguồn bên ngoài nào xác nhận.
- Có tín hiệu cảnh báo mạnh từ lĩnh vực lân cận: cùng một hệ thống đạt 86,4% khi chỉ cần phát hiện *có lỗi hay không*, nhưng chỉ 42,2% khi phải nói *lỗi thuộc loại nào*. Phân loại chi tiết khó hơn phát hiện rất nhiều.
- Ranh giới mập mờ là có thật và cụ thể: nguồn nói "doanh thu", deck nói "ARR" — cùng chủ thể hay khác chủ thể?

**Điều gì còn thiếu**
Một bộ ca có nhãn người cho cả ba lớp (bịa rõ, nói sai rõ, mập mờ), và bằng chứng rằng quy tắc **từ chối phán quyết** đúng chỗ thay vì ép nhãn.

**Có đáng research ngay không?**
Không gấp. Có một đường đi an toàn không cần chờ: **chỉ báo một con số "không có chỗ dựa" kèm một hàng đợi cần người xem**. Con số đó đã đủ để theo dõi tiến bộ.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Khi nhóm cần báo cáo **hai con số riêng** trong tài liệu bảo vệ, hoặc khi hàng đợi cần người xem trở nên quá lớn để xử lý thủ công.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Quy tắc đối ứng bốn trường có tách đúng hai lớp trên các ca RÕ RÀNG,
và có TỪ CHỐI PHÁN QUYẾT trên các ca mập mờ thay vì tạo ra sự chắc chắn giả, hay không?
```

---

### 2.4 Evaluator hiện đại xử lý được suy luận số tới đâu

**Vấn đề**
Deck thường chứa những con số **được tính lại** từ nguồn. Chưa biết các công cụ kiểm tính đúng hiện nay xử lý loại này tới đâu.

**Tại sao hiện chưa thể chốt**
Bằng chứng duy nhất có trong hồ sơ về việc "mô hình học kéo theo không tự nhiên học được thao tác trên đại lượng" đến từ một benchmark **thế hệ trước**, đo trên các mô hình cũ. Nó **không** chứng minh điều gì về công cụ hiện nay — nó chỉ là lý do **kiến trúc** để tách phép kiểm số ra khỏi phép kiểm ngôn ngữ.

**Điều gì đã biết**

- Suy luận định lượng là **một trục năng lực tách rời** khỏi suy diễn ngôn ngữ.
- Vì chưa biết, giải pháp an toàn hiện tại là **định tuyến**: mọi con số không khớp nguyên văn đi vào một hàng đợi riêng, không bị kết tội và cũng không bị bỏ qua.

**Điều gì còn thiếu**
Một phép đo lại trên công cụ hiện nay, với các loại biến đổi cụ thể: suy ra đúng, suy ra sai, làm tròn, đổi đơn vị, nhầm phần trăm với điểm phần trăm.

**Có đáng research ngay không?**
Đây là câu hỏi **rẻ nhất trong cả danh sách để tự trả lời**: dựng 20–30 cặp có đáp án và đếm. Nhưng nó chỉ có ý nghĩa sau khi đã chọn được công cụ ở mục 2.1 và 2.5.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Ngay sau khi một công cụ kiểm tính đúng được chọn và vượt qua bài kiểm tra cơ bản.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Công cụ đang dùng phạt nhầm bao nhiêu phép biến đổi số ĐÚNG,
và bỏ lọt bao nhiêu phép biến đổi số SAI?
```

Ghi chú: kết quả tốt **không** cho phép bỏ hàng đợi — nó chỉ cho phép dùng mô hình để phân loại sơ bộ hàng đợi.

---

### 2.5 Nhóm công cụ factuality có chuyển giao sang slide deck không

**Vấn đề**
Toàn bộ nhóm công cụ đo tính bám nguồn được phát triển và kiểm chứng trên **tóm tắt văn xuôi**. Chưa ai áp chúng lên slide deck.

**Tại sao hiện chưa thể chốt**
Hai vòng tìm kiếm độc lập với truy vấn khác nhau đều cho kết quả **không có nguồn nào**. Kể cả một hệ thống document→slide chuyên biệt cũng không dùng nhóm công cụ này — nó đo mức bao phủ bằng độ tương đồng embedding, một cách yếu hơn hẳn. Đây là **negative finding**, không phải chỗ chưa tìm kỹ.

**Điều gì đã biết**

- Deck khác tóm tắt ở bốn điểm cụ thể: nội dung bị phân mảnh qua nhiều slide; bullet là **fragment cực đoan** (mất chủ ngữ, mất mốc thời gian, mất đơn vị); có bảng và hình; và bullet **về bản chất là claim bị nén**.
- Điểm cuối đặc biệt đáng lo: một nghiên cứu stress-test cho thấy các metric này **kém đi khi claim có mật độ thông tin cao**, tức đúng vùng mà Deck Agent hoạt động.
- Vẫn có lý do để hy vọng: công cụ đứng đầu nhóm được huấn luyện có chủ đích cho các claim **tổng hợp thông tin từ nhiều câu nguồn** — đúng hình dạng của một bullet tóm tắt.

**Điều gì còn thiếu**
Một pilot trên dữ liệu dựng tay có đáp án biết trước. Không có cách nào trả lời câu hỏi này bằng đọc tài liệu.

**Có đáng research ngay không?**
Không cần research thêm — cần **thí nghiệm**. Và thí nghiệm đó rẻ: vài ca dựng tay, chạy một lần.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Khi có một hệ thống chạy được sinh ra deck thật, hoặc sớm hơn — vì pilot đầu tiên **không cần** hệ thống thật, chỉ cần deck dựng tay.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Trên các ca dựng tay có đáp án, công cụ có phân biệt được
deck đúng · deck có phát biểu không chỗ dựa · deck có số được tính lại đúng
thành ba hình dạng kết quả khác nhau hay không?
```

Nếu **không** phân biệt được trên ca dựng sẵn, chắc chắn không phân biệt được trên dữ liệu thật — và toàn bộ hướng này bị loại. Đó là lý do đây là phép thử rẻ nhất và nên làm đầu tiên.

---

### 2.6 Sàn nhiễu thật của hệ sinh deck và của giám khảo

**Vấn đề**
Chưa biết chạy lại đúng cùng một cấu hình thì kết quả dao động bao nhiêu — cả ở phía sinh deck lẫn ở phía chấm điểm.

**Tại sao hiện chưa thể chốt**
Không nguồn bên ngoài nào báo cáo con số này cho lĩnh vực sinh slide. Phải tự đo.

**Điều gì đã biết**

- Tính bất định tồn tại **ngay trong cấu hình được kỳ vọng là tất định**; đặt nhiệt độ về 0 không loại bỏ được nó.
- Phán quyết của giám khảo mô hình **đổi khi diễn đạt lại prompt** theo cách tương đương, và mô hình lớn hơn không ổn định hơn.
- Hệ quả cứng: **chênh lệch nhỏ hơn mức dao động tự nhiên không được gọi là cải thiện**.

**Điều gì còn thiếu**
Con số thật, đo trên chính hệ thống và chính công cụ chấm của nhóm.

**Có đáng research ngay không?**
Không phải research — là một buổi làm việc kỹ thuật, và nó là **điều kiện tiên quyết** của mọi phép so sánh phiên bản.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Ngay khi có một hệ thống chạy được và trước lần so sánh phiên bản đầu tiên.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Chạy lại đúng cùng một đầu vào và cùng cấu hình N lần thì mỗi chỉ số dao động bao nhiêu?
```

Và với mỗi chỉ số, ghi con số đó lại như **ngưỡng tối thiểu để một chênh lệch được coi là tín hiệu**.

---

### 2.7 Tiêu chí do người viết so với do mô hình sinh

**Vấn đề**
Việc xây dữ liệu kiểm thử cho nhánh coverage tốn nhiều công người. Có thể để mô hình sinh danh sách fact rồi người chỉ duyệt — nhưng chưa biết cách rẻ này mất bao nhiêu chất lượng.

**Tại sao hiện chưa thể chốt**
Không nguồn nào đo trực tiếp khoảng cách đó bằng số.

**Điều gì đã biết**

- Mô hình hai lớp — **người định nghĩa tiêu chí, mô hình đối chiếu** — có tiền lệ nhất quán ở nhiều lĩnh vực chuyên môn khác nhau, và là mô hình khả thi nhất cho một nhóm nhỏ.
- Ở đầu kia của phổ chi phí: có benchmark dùng chuyên gia soạn tay hàng chục nghìn tiêu chí, và có benchmark dùng trung bình hơn 54 mục kiểm nhị phân cho **mỗi** instance với 238 instance. Mức đầu tư đó **ngoài tầm** một đồ án.
- Cảnh báo quan trọng từ một khảo sát: tiêu chí kém chất lượng **không chỉ vô dụng mà còn chủ động làm sai lệch** kết quả đánh giá.
- Một nhóm tác giả trong lĩnh vực văn bản chuyên môn dài phát biểu rằng tiêu chí chất lượng cao hiện **chưa thể sinh tự động** — nhưng đó là phát biểu định tính, **không kèm thí nghiệm đối chứng**, nên không được trích như một kết quả đo được.

**Điều gì còn thiếu**
Một phép đo trên dữ liệu của chính nhóm: sinh danh sách fact cho **cùng một tài liệu hai lần độc lập**, đo mức trùng nhau sau khi so khớp ngữ nghĩa.

**Có đáng research ngay không?**
Không cần đọc thêm; cần đo. Và phép đo này có lợi ích kép: nó vừa trả lời câu hỏi chi phí, vừa cho biết **có được phép sinh tiêu chí lúc chạy hay không** — nếu mức trùng thấp thì tuyệt đối không được, vì thước đo sẽ co giãn theo vật cần đo.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Khi bắt đầu xây bộ dữ liệu kiểm thử cho coverage.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Sinh danh sách fact cho cùng một tài liệu hai lần độc lập thì trùng nhau bao nhiêu,
và người phải sửa bao nhiêu phần trăm số mục?
```

---

### 2.8 Cần bao nhiêu người chấm và bao nhiêu deck

**Vấn đề**
Đánh giá cuối cần người chấm để đối chiếu với công cụ tự động. Chưa biết cần bao nhiêu.

**Tại sao hiện chưa thể chốt**
Con số ứng viên duy nhất tìm được (khoảng ba người chấm, vài chục tới khoảng một trăm mẫu) đến từ một thủ tục thống kê trả lời một câu hỏi **khác**: nó hỏi liệu một mô hình có thay thế được người chú thích trong một tác vụ chú thích hay không. Câu hỏi của nhóm là: cần bao nhiêu deck để tin tương quan giữa công cụ chấm và người. Cỡ mẫu thật phụ thuộc: đang xác nhận metric nào, đơn vị là deck hay slide, chấm điểm hay so cặp, và phương sai thực tế.

**Điều gì đã biết**

- Cần **ít nhất ba người chấm độc lập** để có tín hiệu đồng thuận.
- **Trần đồng thuận giữa người với người trong lĩnh vực này không cao**: một hệ đánh giá presentation ghi nhận mức đồng thuận giữa bốn người chấm ở khoảng 0,59 theo một hệ số chuẩn; một benchmark khác ghi trần người–người khoảng 0,664 theo hệ số tương quan hạng.
- Hệ quả quan trọng cho cách phát biểu: **đừng đặt mục tiêu công cụ đạt tương quan tuyệt đối cao với người**. Cách đúng là báo cáo tương quan của công cụ **cùng với** trần người–người trên **cùng dữ liệu**.
- Ghi lại đúng trạng thái: `cỡ mẫu người = chưa xác định; thang tham khảo từ tài liệu: vài chục tới khoảng một trăm mẫu, ít nhất ba người chấm`. **Không** ghi thành yêu cầu.

**Điều gì còn thiếu**
Phương sai thực tế trên dữ liệu của nhóm, và quyết định về việc xác nhận metric nào.

**Có đáng research ngay không?**
Không. Chốt sớm ở đây tạo ra một con số nghe có vẻ khoa học nhưng trả lời sai câu hỏi.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Khi có ít nhất một công cụ chấm tự động cần được xác nhận bằng người, và đã biết phương sai của nó.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Với ĐÚNG metric nào, ở ĐÚNG đơn vị nào, cần bao nhiêu mẫu để ước lượng
tương quan giữa công cụ và người với sai số chấp nhận được — biết rằng
trần người–người trong lĩnh vực này vốn đã thấp?
```

---

### 2.9 Đo công sức chỉnh sửa của người dùng bằng gì

**Vấn đề**
Sản phẩm hứa deck "dùng được ngay hoặc chỉ sửa nhẹ". Chưa có cách đo "sửa nhẹ".

**Tại sao hiện chưa thể chốt**
Đây là **chỗ bằng chứng yếu nhất của toàn bộ hồ sơ**. Một vòng tìm kiếm có chủ đích không tìm thấy metric bên ngoài nào lượng hóa công sức chỉnh sửa trên deck sinh tự động. Ngoài ra, định nghĩa "công sức" phụ thuộc vào việc **người dùng sửa deck ở đâu** — trong sản phẩm hay ngoài sản phẩm — mà điều đó chưa được chốt.

**Điều gì đã biết**

- Nguồn gần nhất là một nghiên cứu với người dạy học thật, và nó chỉ **mô tả định tính**: người dạy phải sửa định dạng, xóa bớt slide, chỉnh cỡ chữ cho phần mã nguồn; công sức được mô tả là "ít" nhưng không lượng hóa.
- Cùng nghiên cứu đó có một quan sát theo hướng khác: người học **không phân biệt được** slide do AI sinh với slide do người làm.
- Các lĩnh vực lân cận (dịch máy, tóm tắt) có truyền thống đo công sức hậu kỳ, nhưng chưa thấy ai chuyển sang deck — và deck khác ở chỗ công sức nằm ở cả nội dung, hình thức lẫn thứ tự.

**Điều gì còn thiếu**
Một định nghĩa vận hành của "công sức", và một cách ghi lại thao tác người dùng.

**Có đáng research ngay không?**
Không. Trước đó phải chốt nơi người dùng sửa deck.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Kiến trúc và phạm vi cuối xác định rõ có vòng chỉnh sửa trong sản phẩm hay không.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
"Chỉ sửa nhẹ" nghĩa là gì dưới dạng một đại lượng ghi lại được,
và ghi lại bằng cách nào trong đúng nơi người dùng thực sự sửa deck?
```

---

### 2.10 Bằng chứng mâu thuẫn về cách đo mức đầy đủ

**Vấn đề**
Hai nhóm bằng chứng chỉ ngược chiều nhau về việc có nên hỏi thẳng mô hình *"deck bỏ sót gì?"* hay không.

**Tại sao hiện chưa thể chốt**

- Chiều thứ nhất (hai nguồn độc lập về phương pháp): đối chiếu theo danh sách đóng hơn hẳn rubric chung (0,688 so với 0,314), và mô hình kém ở việc phát hiện thiếu sót ngay cả trong tác vụ dễ hơn.
- Chiều thứ hai (một nguồn): trong một so sánh ba cách đo mức đầy đủ, cách **hỏi thẳng** lại hiệu quả một cách bất ngờ so với các cách phức tạp hơn, đổi lại kém về độ bền và khả năng diễn giải.
- Hồ sơ hiện tại **chưa trích xuất được số cụ thể** của nguồn thứ hai, nên chưa cân được hai bên.

**Điều gì đã biết**
Nghiêng về hướng danh sách đóng, vì hai nguồn độc lập và vì lập luận cơ chế (không có "chỗ trống" nào để mô hình chú ý vào). Nhưng **không coi câu hỏi đã đóng**.

**Điều gì còn thiếu**
Đọc kỹ nguồn thứ hai và trích được số của nó.

**Có đáng research ngay không?**
Đây là mục **rẻ nhất** trong danh sách: đọc một tài liệu. Nên gộp vào lần mở lại của mục 2.1.

**Trạng thái: OPEN**

**Điều kiện mở lại** Cùng lúc với mục 2.1.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Nguồn báo rằng cách hỏi thẳng hiệu quả bất ngờ đã đo trên tác vụ nào, với số liệu nào,
và điều kiện đó có giống bối cảnh deck hay không?
```

---

### 2.11 Trật tự thị giác và sự phù hợp của bố cục

**Vấn đề**
Chưa có cách đo việc slide có trật tự thị giác rõ ràng không, và kiểu bố cục được chọn có hợp với loại nội dung không.

**Tại sao hiện chưa thể chốt**
Hai lý do độc lập: cách đo tự động cho đúng loại lỗi này chưa đáng tin (mô hình chỉ đạt F1 0,331–0,655 trên bộ phân loại lỗi thiết kế slide), và phần "hợp với loại nội dung" đòi hỏi một **bảng phân loại loại nội dung** mà dự án chưa có.

**Điều gì đã biết**
Phần lỗi bố cục **có định nghĩa hình học** thì kiểm được bằng mã và nên làm như vậy. Phần còn lại chưa có phương án.

**Điều gì còn thiếu**
Cả hai: một cách đo đáng tin, và một bảng phân loại nội dung.

**Có đáng research ngay không?**
Không. Đây là phần cuối cùng của nhánh design, và các phần dễ hơn đều chưa được làm.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Sau khi các phép kiểm hình học tất định đã chạy ổn định và vẫn còn một khoảng cách chất lượng chưa giải thích được.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Phần chất lượng bố cục còn LẠI sau khi đã trừ hết các lỗi kiểm được bằng mã
là gì, và nó có đủ lớn để đáng đo không?
```

---

### 2.12 Cách tổ chức toàn bộ hệ evaluation thành các nhóm

**Vấn đề**
Chưa chốt cách nhóm các phép đo thành một cấu trúc mạch lạc cho tài liệu framework.

**Tại sao hiện chưa thể chốt**
Có một đề xuất tổ chức theo **hệ quy chiếu** — tức là "deck đang được so với cái gì": so với chính nó (file có hợp lệ không), so với nguồn, so với yêu cầu người dùng, so với chuẩn trình bày, so với kết quả sử dụng. Cách nghĩ này có nền tảng tốt vì hệ quy chiếu thường dự đoán được phương pháp đo.

Nhưng có **một lỗ hổng đã được xác định**: cách nhóm đó **thiếu một hệ quy chiếu đối chiếu** — trường hợp deck được so với **các deck anh em của chính nó** (phiên bản trước, chế độ khác, cấu hình bị tắt bớt). Mà lớp thiếu đó chứa cả việc theo dõi tiến bộ lẫn việc đo đóng góp component — tức là **trung tâm của nhiệm vụ**.

**Điều gì đã biết**
Cách nhóm hiện có chưa đủ, và lý do đã được xác định cụ thể chứ không mơ hồ.

**Điều gì còn thiếu**
Một đề xuất tổ chức đã sửa lỗ hổng trên, và một vòng review của con người.

**Có đáng research ngay không?**
Không. Cách tổ chức tài liệu nên được quyết định **sau khi** biết cuối cùng có những phép đo nào — không phải trước.

**Trạng thái: PAUSED**

**Điều kiện mở lại**
Khi bắt đầu viết tài liệu framework chính thức, sau khi kiến trúc và bộ metric được chốt.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Cách tổ chức này có chỗ đứng cho việc so sánh giữa các output ANH EM
(phiên bản trước, chế độ khác, cấu hình bị tắt bớt) hay không?
```

---

### 2.13 Mỗi tài liệu cần bao nhiêu fact quan trọng

**Vấn đề**
Khi xây dữ liệu kiểm thử cho coverage, chưa biết nên chọn bao nhiêu fact cho mỗi tài liệu.

**Tại sao hiện chưa thể chốt**
Hai phía đều hỏng: quá ít thì mức bao phủ bão hòa ở mức tối đa và mất khả năng phát hiện suy giảm; quá nhiều thì hệ đo **phạt sản phẩm vì đã làm đúng việc chọn lọc** — một deck 10 slide **nên** bỏ bớt ý phụ.

**Điều gì đã biết**

- Có tiền lệ về việc **không coi mọi fact ngang nhau**: một nghiên cứu chia các mệnh đề thành ba mức quan trọng và chỉ tính chỉ số trên nhóm quan trọng nhất, và khoảng cách giữa "trả lời sai" và "trả lời đúng" giãn rộng hẳn khi làm vậy.
- Nhưng "quan trọng" trong nguồn đó neo vào **câu hỏi của người dùng**, còn ở đây nó phải neo vào **tài liệu nguồn** và có thể cả **chế độ trình bày** — một cơ chế khác, phải thiết kế lại.
- Có một vấn đề về **đơn vị** đã được lĩnh vực này ghi nhận: các giao thức đo mức bao phủ cũ bị phê phán vì thiếu tính hệ thống trong việc định nghĩa và chọn độ mịn của đơn vị.

**Điều gì còn thiếu**
Một quy tắc chọn độ mịn, và một quyết định về việc có gán mức quan trọng hay không.

**Có đáng research ngay không?**
Không tách rời được — nó gắn với mục 2.1 và với việc thiết kế benchmark.

**Trạng thái: OPEN**

**Điều kiện mở lại** Khi bắt đầu thiết kế benchmark.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Chọn bao nhiêu fact và ở độ mịn nào để chỉ số vừa KHÔNG bão hòa,
vừa KHÔNG phạt việc chọn lọc hợp lý?
```

---

### 2.14 Nội dung nằm trong bảng và hình của tài liệu nguồn

**Vấn đề**
Toàn bộ nhánh source quality hiện là văn bản đối chiếu văn bản. Nếu một ý quan trọng nằm trong một biểu đồ hoặc một bảng, không phương pháp nào hiện có chạm tới.

**Tại sao hiện chưa thể chốt**
Chưa có phương án; và mức độ nghiêm trọng phụ thuộc vào loại tài liệu mà sản phẩm nhắm tới.

**Điều gì đã biết**
Đây là một **giới hạn phạm vi phải được nêu rõ** khi báo cáo bất kỳ con số coverage nào, chứ không được im lặng bỏ qua.

**Điều gì còn thiếu**
Hoặc một cách xử lý, hoặc một tuyên bố phạm vi tường minh.

**Có đáng research ngay không?**
Không. Nhưng **phải ghi vào phần giới hạn** ngay khi báo cáo số đầu tiên.

**Trạng thái: OPEN**

**Điều kiện mở lại**
Khi loại tài liệu nguồn chính của sản phẩm được xác định là nhiều bảng/biểu đồ.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Bao nhiêu phần trăm các ý quan trọng trong tập tài liệu thật của chúng ta
nằm ngoài phần văn bản thuần?
```

---

### 2.15 Xử lý tiếng Việt trong các công thức đo

**Vấn đề**
Nhiều công thức đo có yếu tố đếm chữ hoặc ước lượng thời gian đọc/nói, và chúng được thiết kế cho tiếng Anh.

**Tại sao hiện chưa thể chốt**
Chưa kiểm chứng. Ví dụ cụ thể: công thức ước lượng thời lượng trình bày lấy từ một benchmark bên ngoài dùng hằng số tốc độ nói của tiếng Anh. Nếu áp thẳng cho nhánh tiếng Việt, kết quả sẽ **lệch có hệ thống** — nghĩa là sai một cách nhất quán, khó phát hiện hơn nhiễu ngẫu nhiên.

**Điều gì đã biết**

- Bộ tách từ phải được cố định và **báo kết quả tách theo từng ngôn ngữ**, không trộn nhiều ngôn ngữ trong cùng một bảng so sánh.
- Không dùng công thức dễ đọc của tiếng Anh cho văn bản tiếng Việt.

**Điều gì còn thiếu**
Kiểm chứng hằng số tốc độ nói và quy ước đếm từ cho tiếng Việt.

**Có đáng research ngay không?**
Rẻ và cụ thể, nhưng chỉ cần thiết khi các metric có yếu tố đếm chữ được đưa vào dùng thật.

**Trạng thái: OPEN**

**Điều kiện mở lại** Khi bất kỳ metric nào phụ thuộc số chữ hoặc thời lượng được đưa vào bộ chính thức.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Các hằng số và quy ước đếm trong công thức này có giá trị cho tiếng Việt không,
và nếu không thì giá trị đúng là bao nhiêu?
```

---

### 2.16 Đơn vị để kiểm: cả bullet hay mệnh đề nhỏ hơn

**Vấn đề**
Khi kiểm tính bám nguồn, nên lấy nguyên một bullet làm đơn vị, hay cắt nhỏ thành các mệnh đề rồi kiểm từng cái?

**Tại sao hiện chưa thể chốt**
Có một sự đánh đổi đã được đặt tên trong lĩnh vực này: đơn vị càng lớn thì càng khó kiểm; đơn vị càng nhỏ thì càng **thiếu ngữ cảnh để diễn giải đúng**. Với bullet trên slide, vế thứ hai đặc biệt nặng vì bullet vốn đã là fragment cực đoan — cắt nhỏ nữa sẽ tạo ra những mệnh đề không thể kiểm độc lập.

**Điều gì đã biết**

- Hướng nghiêng hiện tại: **lấy bullet làm đơn vị kiểm, không cắt nhỏ thêm** — vì rẻ hơn và vì việc cắt nhỏ tự nó sinh lỗi.
- Nhưng nguồn đặt tên cho sự đánh đổi này trong hồ sơ mới ở mức trích qua kết quả tìm kiếm, **chưa đọc bản gốc**, nên chỉ dùng để gọi tên vấn đề, không dùng để chốt.

**Điều gì còn thiếu**
Một phép thử trên chính dữ liệu: cùng một bộ deck, kiểm theo hai cách, so kết quả.

**Có đáng research ngay không?**
Không tách rời — gộp vào pilot của mục 2.5.

**Trạng thái: OPEN**

**Điều kiện mở lại** Cùng lúc với mục 2.5.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Kiểm theo bullet nguyên vẹn so với kiểm theo mệnh đề cắt nhỏ
cho kết quả khác nhau ở đâu, và cách nào tạo ra ít lỗi giả hơn?
```

---

### 2.17 Một số con số trích dẫn chưa đối chiếu bản gốc

**Vấn đề**
Một phần các con số trong hồ sơ research được đọc qua bản tóm tắt tự động của trang web, chưa đối chiếu với tài liệu gốc.

**Tại sao hiện chưa thể chốt**
Chưa có ai làm bước đối chiếu.

**Điều gì đã biết**
Các nhóm số cần đối chiếu trước khi đưa vào tài liệu bảo vệ:

- các con số về hiệu ứng của việc điều kiện hóa theo đối tượng người nghe, và công thức ước lượng thời lượng trình bày;
- các con số của một hệ thống dùng phần thưởng kiểm chứng được cho bố cục, đặc biệt claim rằng mô hình thị giác làm tệ hơn đoán ngẫu nhiên ở việc phát hiện lỗi bố cục;
- một phần chi tiết của các benchmark được đọc qua bản tóm tắt trang HTML thay vì bản gốc.

Trong gói tài liệu này, mọi chỗ dùng các con số đó **đều đã kèm cảnh báo tại chỗ**, và **cơ chế** được dùng làm căn cứ thay cho **con số**.

**Điều gì còn thiếu**
Một buổi đối chiếu bản gốc.

**Có đáng research ngay không?**
Không phải research — là việc kiểm tra. Nhưng nó **bắt buộc** trước khi bất kỳ con số nào trong nhóm trên xuất hiện trong báo cáo bảo vệ.

**Trạng thái: OPEN**

**Điều kiện mở lại** Trước khi viết báo cáo cuối, hoặc sớm hơn nếu một trong các con số đó được dùng làm căn cứ cho một quyết định.

**Khi mở lại cần trả lời đúng câu hỏi nào**

```text
Con số này có xuất hiện đúng như vậy trong bản gốc không,
và nó được đo trong điều kiện nào?
```

---

## 3. Những vấn đề KHÔNG nên research tiếp trước khi kiến trúc được chốt

Tách riêng để tránh nhầm với nhóm "chưa tới lượt":

| Vấn đề | Vì sao dừng |
| --- | --- |
| Định nghĩa ba chế độ trình bày (2.2) | Là quyết định sản phẩm, không phải câu hỏi nghiên cứu; và có thể bị bỏ nếu phạm vi đổi |
| Cách tổ chức hệ evaluation thành nhóm (2.12) | Nên quyết định sau khi biết cuối cùng có những phép đo nào |
| Đo đóng góp của từng component | Câu hỏi chỉ tồn tại cho những component thực sự tồn tại |
| Định nghĩa baseline "cách làm đơn giản nhất" | Không định nghĩa được cái đơn giản trước khi biết cái phức tạp là gì |
| Tính hợp lệ của cấu trúc dữ liệu trung gian | Đối tượng đo có thể không tồn tại trong kiến trúc mới |
| Ngưỡng đạt/không đạt cho mọi metric | Ngưỡng cho một hệ thống chưa biết hình dạng là ngưỡng vô nghĩa |

**Nguyên tắc chung:** một câu hỏi chỉ đáng nghiên cứu khi **đối tượng của nó chắc chắn tồn tại**. Nếu đối tượng còn phụ thuộc một quyết định chưa đưa ra, nghiên cứu bây giờ là đầu tư vào một khả năng, không phải vào một sự thật.

---

## 4. Nếu chỉ được chọn ba việc khi quay lại

Xếp theo tỉ lệ **giá trị trên chi phí**, và cả ba đều **không phụ thuộc kiến trúc**:

1. **Kiểm tra xem công cụ đo tính bám nguồn có phân biệt được các ca dựng tay hay không** (2.5). Rẻ nhất, và nếu fail thì loại luôn cả một hướng — tiết kiệm rất nhiều công về sau.
2. **Đo sàn nhiễu** (2.6). Một buổi làm việc, và là điều kiện tiên quyết của mọi phép so sánh phiên bản.
3. **Tìm phương pháp phát hiện fact có được đề cập hay không** (2.1). Đắt hơn hai việc trên, nhưng là ô trống lớn nhất và chặn toàn bộ nhánh coverage.

Ba việc này cộng lại vẫn **không** yêu cầu biết kiến trúc cuối, và kết quả của chúng vẫn đúng dù kiến trúc thay đổi thế nào.
