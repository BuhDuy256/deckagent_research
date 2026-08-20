# PPTAgent Architecture Review Frame — Khung để review một kiến trúc xuất phát

**Ngày lập:** 2026-08-20
**Trạng thái:** FRAMEWORK CHO TASK SAU — **không phải quyết định kiến trúc, không phải thiết kế lại Deck Agent**
**Phạm vi:** cách đặt câu hỏi khi xem xét PPTAgent như một kiến trúc xuất phát

---

## 0. File này làm gì và không làm gì

**Làm:** đưa ra một khung suy nghĩ để task kiến trúc tiếp theo dùng, cùng với những gì hồ sơ evaluation đã biết chắc về PPTAgent và về giới hạn của việc mượn kiến trúc.

**Không làm:**

- không quyết định giữ hay bỏ bất kỳ component nào;
- không thiết kế lại Deck Agent;
- không coi PPTAgent là chuẩn mực hay là Source of Truth;
- không biến các yêu cầu nội bộ của dự án thành bằng chứng bên ngoài, và ngược lại.

---

## 1. Sai lầm cần tránh, nói thẳng

Cách làm sai:

```text
PPTAgent có bước lập Outline
→ Deck Agent phải có bước lập Outline
```

Vì sao sai: nó bỏ qua **toàn bộ phần lý do**. Một cơ chế tồn tại trong một hệ thống vì hệ thống đó gặp một vấn đề cụ thể, trong một bài toán cụ thể, với những ràng buộc cụ thể. Nếu Deck Agent không gặp đúng vấn đề đó, cơ chế sẽ mang theo chi phí mà không mang theo lợi ích — và tệ hơn, nó sẽ trở thành một thứ **phải bảo vệ khi phản biện** mà không có lý do thật để bảo vệ.

Có một ví dụ rất cụ thể về sự khác biệt bài toán: **PPTAgent cần một bản trình chiếu tham chiếu làm đầu vào** — nó hoạt động bằng cách phân tích một deck có sẵn rồi tái sử dụng cấu trúc và cách trình bày của deck đó. Deck Agent, theo mô tả hiện tại, không có ràng buộc này; vai trò tương đương được đảm nhận bởi hệ design token. "Cùng sinh slide" **không** có nghĩa là "cùng bài toán", và điều này đúng cả khi so sánh kiến trúc lẫn khi so sánh kết quả.

---

## 2. Chuỗi câu hỏi bắt buộc cho mỗi cơ chế

Với **mỗi** cơ chế của PPTAgent, đi đủ sáu bước sau. Không được nhảy bước.

```text
PPTAgent có mechanism X
        ↓
X giải problem gì?
(vấn đề cụ thể trong bài toán của họ, không phải mô tả chức năng)
        ↓
Paper có evidence nào cho thấy X hữu ích?
(số cụ thể, đo trên trục nào — hay chỉ là mô tả thiết kế?)
        ↓
Deck Agent có CÙNG problem đó không?
(không phải "có giống chức năng không" — mà "có gặp cùng khó khăn không")
        ↓
Product Goal / FR / NFR nào của Deck Agent tạo ra nhu cầu đó?
(nếu không chỉ ra được, nhu cầu đó có thể không tồn tại)
        ↓
KEEP / ADAPT / DROP
```

Sau đó, chiều ngược lại:

```text
Deck Agent có requirement mà PPTAgent không giải
        ↓
ADD candidate mechanism
```

Chiều ngược lại **quan trọng ngang chiều thuận**. Một kiến trúc mượn về mà không bổ sung sẽ chỉ giải được các yêu cầu của hệ thống gốc.

---

## 3. Mỗi quyết định kiến trúc phải mang theo năm thứ

Đây là ràng buộc định dạng cho task kiến trúc tiếp theo. Một quyết định thiếu bất kỳ dòng nào là một quyết định chưa xong:

```text
Problem                → vấn đề thật đang giải là gì
Mechanism              → cơ chế được chọn
Trade-off              → cái giá phải trả (độ trễ, chi phí, độ phức tạp, rủi ro hỏng)
Claim                  → điều đồ án sẽ tuyên bố nhờ cơ chế này
Evaluation implication → tuyên bố đó cần bằng chứng gì, và bằng chứng đó đo bằng cách nào
```

Dòng cuối là dòng nối trực tiếp sang gói evaluation này. Nếu một quyết định kiến trúc **không** dẫn tới một claim nào, thì nó không cần bằng chứng — và cũng khó biện minh là một đóng góp. Nếu nó dẫn tới một claim mà claim đó **không có cách nào đo**, đó là dấu hiệu cần biết **trước khi** cài đặt, không phải sau.

---

## 4. Hồ sơ evaluation đã biết chắc gì về PPTAgent

Ghi lại đúng những gì đã kiểm chứng, để task kiến trúc không phải tìm lại — và cũng để không ai suy rộng quá mức.

### 4.1 Các cơ chế được nêu tên

PPTAgent công bố một bảng thí nghiệm tắt từng thành phần, giữ nguyên mô hình. Bảng đó cho biết hệ thống có ít nhất bốn cơ chế tách rời được:

| Cơ chế bị tắt | Tỉ lệ thành công (%) | Điểm chất lượng trung bình |
| --- | ---: | ---: |
| *(đầy đủ)* | 95,0 | 3,67 |
| bỏ bước lập outline | 91,0 | 3,30 |
| bỏ bước trích xuất schema | 78,8 | 3,45 |
| bỏ bước phân tích cấu trúc | 92,2 | 3,32 |
| bỏ bước render bằng mã | 74,6 | 3,66 |

Diễn giải của chính nhóm tác giả: bỏ trích xuất schema hại **tỉ lệ thành công** nhiều nhất; bỏ lập outline hại **mạch nội dung** nhiều nhất.

### 4.2 Ba điều bảng này thực sự cho biết

1. **Các cơ chế hỏng theo những trục khác nhau.** Bỏ render bằng mã gần như không đổi điểm chất lượng (3,66 so với 3,67) nhưng làm tỉ lệ thành công rơi hơn 20 điểm. Nếu đọc trên một điểm tổng, kết luận sẽ là "cơ chế này vô dụng" — sai hoàn toàn.
2. **Ablation làm đúng cách trông như thế nào**: tắt một cơ chế mỗi lần, giữ nguyên mô hình, và báo cáo trên nhiều trục.
3. **Định nghĩa vận hành của "thành công"**: sinh đủ mọi slide mà không lỗi thực thi, sau khi hệ thống đã tự sửa lỗi.

### 4.3 Ba điều bảng này KHÔNG cho biết

1. **Không cho biết cơ chế nào cần cho Deck Agent.** Bài toán khác (cần deck tham chiếu), đầu vào khác, ràng buộc khác.
2. **Không cho phép so số.** Điểm chất lượng của họ đo bằng thang của họ, trên dữ liệu của họ, với giám khảo của họ. Đặt nó cạnh bất kỳ con số nào của Deck Agent là so hai thang khác nhau.
3. **Không đánh giá chất lượng của bản thân outline.** Nó đo **tác động của việc có outline lên deck cuối**. Đây là một điểm quan trọng và dễ nhầm — xem mục 6.

### 4.4 Hệ đánh giá của PPTAgent và giới hạn của nó

PPTAgent đi kèm một hệ chấm ba chiều, thang 1–5: chất lượng nội dung trình bày, thiết kế, và mạch nội dung. Hai điều cần biết trước khi mượn:

- **Chiều "nội dung" của hệ này KHÔNG đo mức bám nguồn.** Tiêu chí của nó nói về việc chữ có súc tích, đúng ngữ pháp, có ảnh phù hợp hay không — tức chất lượng của văn bản **trên slide**. Nó **không** kiểm từng phát biểu có được tài liệu nguồn chống đỡ hay không. Dùng nó như một phép đo bám nguồn mà không thích nghi là một nhầm lẫn về ý nghĩa, không phải về độ chính xác.
- **Mức đồng thuận với người rất không đều giữa các chiều**: tương quan Pearson khoảng 0,90 cho thiết kế, 0,70 cho nội dung, và chỉ **0,55 cho mạch nội dung**. Đồng thuận giữa bốn người chấm ở mức khoảng 0,59 theo một hệ số chuẩn — tức **trần của chính con người cũng không cao**. Hệ quả: đừng lấy điểm mạch nội dung của một hệ chấm kiểu này làm tín hiệu theo dõi tiến bộ.

Có một kết quả nữa từ chính hệ thống này, đáng nhớ khi chọn cách đo: các phép đo dựa trên **so khớp với một bản mẫu** là proxy kém — trong bảng kết quả của họ, một hệ thống đạt điểm so khớp văn bản **cao nhất** lại có điểm nội dung **thấp**, còn PPTAgent điểm so khớp thấp hơn nhưng nội dung cao hơn. Nghĩa là chỉ số đó **đi ngược chiều** với chất lượng nội dung.

---

## 5. Bốn câu hỏi lọc, dùng trước khi phân tích từng cơ chế

Trước khi đi vào chi tiết từng cơ chế, bốn câu hỏi này loại được phần lớn các nhánh không đáng theo:

**Câu 1 — Bài toán có thực sự giống không?**

```text
PPTAgent nhận: tài liệu nguồn + MỘT DECK THAM CHIẾU
Deck Agent nhận: tài liệu nguồn + yêu cầu của người dùng (+ hệ design token của chính nó)
```

Mọi cơ chế của PPTAgent phục vụ việc **khai thác deck tham chiếu** đều cần được xét lại từ đầu, không phải điều chỉnh.

**Câu 2 — Cơ chế này giải vấn đề nội dung hay vấn đề độ tin cậy kỹ thuật?**
Bảng ở mục 4.1 cho thấy hai loại tồn tại song song và không thay thế nhau. Trả lời sai câu này dẫn tới việc đo sai trục khi cần chứng minh giá trị của cơ chế.

**Câu 3 — Deck Agent có yêu cầu nào mà PPTAgent không có?**
Ít nhất bốn thứ được nêu trong yêu cầu nội bộ mà bài toán của PPTAgent không đặt ra: truy vết từng khối nội dung về nguồn; nhiều chế độ trình bày từ cùng một tài liệu; vòng chỉnh sửa có trạng thái và tôn trọng vùng khóa; nhiều loại nguồn đầu vào ngoài tài liệu văn bản. Đây là vùng **ADD**, và cũng là vùng có khả năng chứa đóng góp riêng của đồ án cao nhất.

**Câu 4 — Nếu giữ cơ chế này, ta sẽ tuyên bố điều gì, và bằng chứng đến từ đâu?**
Nếu không trả lời được, đừng giữ nó vì "paper có".

---

## 6. Một cảnh báo cụ thể về việc đánh giá các bước trung gian

Điều này ảnh hưởng trực tiếp tới cách task kiến trúc lập luận, nên ghi riêng.

Một vòng tìm kiếm có chủ đích **không tìm thấy** công trình bên ngoài nào **chấm điểm trực tiếp một bản kế hoạch, outline hay cấu trúc trung gian** như một đối tượng đánh giá riêng. Kể cả PPTAgent cũng không: nó tắt bước outline rồi đo **tác động lên deck cuối**, chứ không chấm chất lượng của outline.

Hai hệ quả cho task kiến trúc:

1. **Nếu kiến trúc mới có một bước trung gian và đồ án muốn tuyên bố bước đó có giá trị**, cách chứng minh có tiền lệ là **tắt nó đi và đo deck cuối**, không phải chấm điểm bản thân nó. Muốn chấm điểm bản thân nó thì phải tự thiết kế phương pháp và tự bảo vệ — không có mốc để dựa.
2. **Việc lưu lại artifact trung gian vẫn nên làm ngay**, độc lập với chuyện có chấm điểm nó hay không. Lý do khác hẳn: khi output cuối hỏng, cần dựng lại được **hệ thống đã quyết định gì ở bước giữa**. Đó là nhu cầu chẩn đoán, không phải nhu cầu đo lường.

---

## 7. Mẫu ghi cho từng cơ chế

Dùng mẫu này cho mỗi cơ chế trong task kiến trúc. Ba dòng đầu là mô tả; bốn dòng sau là phán đoán; dòng cuối nối sang evaluation.

```text
### <Tên cơ chế>

Mô tả trong hệ gốc:
  <cơ chế làm gì, ở bước nào>

Problem nó giải trong hệ gốc:
  <khó khăn cụ thể, không phải mô tả chức năng>

Evidence trong hệ gốc:
  <số cụ thể, đo trên trục nào — hoặc ghi rõ: chỉ là mô tả thiết kế, không có số>

Deck Agent có cùng problem không?
  CÓ / KHÔNG / MỘT PHẦN — vì sao

Nhu cầu này đến từ đâu trong Deck Agent?
  <product goal, hoặc yêu cầu chức năng/phi chức năng cụ thể — nếu không chỉ ra được, ghi rõ>

Quyết định:
  KEEP / ADAPT / DROP

Nếu ADAPT: thay đổi gì và vì sao
  <...>

Trade-off phải chấp nhận:
  <độ trễ / chi phí / độ phức tạp / rủi ro hỏng thêm>

Claim đồ án sẽ tuyên bố nhờ cơ chế này:
  <một câu — hoặc "không tuyên bố gì", đó cũng là câu trả lời hợp lệ>

Evaluation implication:
  <claim đó cần bằng chứng gì; bằng chứng đó nằm ở mục nào trong Tool Selection Guide;
   nếu chưa có cách đo, ghi rõ và đưa vào danh sách chưa giải quyết>
```

Ghi chú về mẫu này: ô **Claim** được phép để trống với nội dung "không tuyên bố gì". Nhiều cơ chế tồn tại chỉ vì lý do kỹ thuật, và ép chúng thành một đóng góp học thuật làm yếu cả phần bảo vệ.

---

## 8. Bốn giả định về Product Goal / FR / NFR cần được review khi bắt đầu task kiến trúc

Gói evaluation này được xây dựa trên một số giả định về sản phẩm. Nếu task kiến trúc thay đổi chúng, phần tương ứng của gói phải được xem lại. Liệt kê để không giả định nào đi ngầm:

**Giả định 1 — Sản phẩm là document-grounded.**
Toàn bộ nhánh source quality (bám nguồn, bao phủ, bịa, nói sai, số suy ra, truy vết nguồn) chỉ có nghĩa nếu deck được sinh **từ tài liệu người dùng cung cấp**. Nếu sản phẩm chuyển sang nhận một câu mô tả ngắn làm đầu vào chính, phần lớn nhánh này mất đối tượng đo.

**Giả định 2 — Sản phẩm giữ nhiều chế độ trình bày từ cùng một tài liệu.**
Toàn bộ nhánh `slide_type` phụ thuộc vào việc ba chế độ vẫn là một claim chính thức. Cần xác nhận rõ, vì đây cũng là nhánh cần nhiều công định nghĩa nhất.

**Giả định 3 — Sản phẩm giữ một hệ design token tường minh và output có thể chỉnh sửa được.**
Phép đo tuân thủ token chỉ định nghĩa được khi có một danh sách token. Phép đo khả năng chỉnh sửa chỉ có giá trị nếu sản phẩm hứa deck là điểm khởi đầu để làm việc tiếp, chứ không phải một kết quả cuối để xem.

**Giả định 4 — Ưu tiên hiện tại xếp phần ghi chú cho người thuyết trình ở mức thấp.**
Nếu task kiến trúc nâng nó lên thành tính năng chính, thì định nghĩa của chế độ hỗ trợ thuyết trình phải viết lại, và cần một cách đánh giá riêng cho phần ghi chú — hiện **không có tiền lệ bên ngoài** nào đánh giá phần này.

Ngoài bốn giả định trên, có một yêu cầu kỹ thuật nên được nêu **sớm** với người làm kiến trúc, vì nêu muộn thì tốn gấp nhiều lần:

> Thiết kế để **bật/tắt từng cơ chế bằng cấu hình**, và để **lưu lại đầy đủ thông tin mỗi lần chạy** (phiên bản, mô hình, prompt nguyên văn, cấu hình sinh, artifact trung gian). Không có hai thứ này thì việc đo đóng góp của từng cơ chế phải tách nhánh mã cho mỗi thí nghiệm, và việc so sánh giữa hai lần chạy không có cơ sở.

---

## 9. Ranh giới của file này

- PPTAgent là **một hệ thống liên quan và một mốc tham chiếu về phương pháp**, không phải chuẩn mực và không phải Source of Truth.
- Các con số ở mục 4 dùng để hiểu **phương pháp**, **không** dùng để so với bất kỳ con số nào của Deck Agent.
- Việc chọn PPTAgent làm điểm xuất phát là một quyết định của người dùng gói tài liệu này; gói này không xác nhận cũng không phản đối quyết định đó — nó chỉ cung cấp cách đặt câu hỏi để quyết định giữ/sửa/bỏ có căn cứ.
- File này **không** mở task kiến trúc. Task đó bắt đầu khi người dùng nói bắt đầu.
