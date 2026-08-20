# Evaluation Dependency Map — Điều gì còn ý nghĩa khi kiến trúc thay đổi

**Ngày lập:** 2026-08-20
**Trạng thái:** SYNTHESIS — không phải decision, không phải final framework
**Đọc trước:** `README.md` mục 1 (mental model) và mục 2 (metric ≠ tool)

---

## 0. File này giải quyết vấn đề gì

Kiến trúc của Deck Agent chưa được chốt. Nếu bây giờ ta đóng băng toàn bộ hệ đo, một phần công sức sẽ đo những cơ chế có thể không còn tồn tại sau khi kiến trúc thay đổi.

Nhưng ngược lại cũng sai: **không phải mọi phép đo đều phụ thuộc kiến trúc.** Câu hỏi *"file PPTX xuất ra có mở được không"* đúng với mọi kiến trúc. Câu hỏi *"bước lập outline có đóng góp gì không"* chỉ tồn tại nếu kiến trúc thực sự có bước lập outline.

File này chia mọi evaluation target theo mức phụ thuộc đó, để sau khi chốt kiến trúc có thể **giữ lại phần vẫn đúng** thay vì làm lại từ đầu.

---

## 1. Định nghĩa Architecture Dependence — dùng đúng ba mức này, không tự diễn giải

### LOW — phụ thuộc kiến trúc thấp

Câu hỏi vẫn tồn tại gần như nguyên vẹn dù bên trong hệ thống thay đổi thế nào. Nó chỉ nhìn vào:

```text
user input
→ final system output
```

Ví dụ: PPTX có mở được không? Deck có bịa thông tin so với nguồn không? Có chữ tràn khung không? Mất bao lâu? Tốn bao nhiêu tiền?

Đặc điểm nhận biết: nếu ta thay toàn bộ ruột hệ thống bằng một hệ khác hoàn toàn, **vẫn hỏi được đúng câu hỏi đó và vẫn so sánh được kết quả**.

### MEDIUM — phụ thuộc kiến trúc vừa

Câu hỏi gắn với một **capability** (khả năng) hoặc **workflow** của sản phẩm, không gắn với cách cài đặt cụ thể.

Nếu capability đó vẫn được giữ thì vẫn phải đánh giá nó — nhưng **cách đo hoặc ý nghĩa của phép đo có thể phải điều chỉnh** khi cơ chế bên dưới đổi.

Ví dụ: ba chế độ `teaching` / `catchup` / `speaker_support`. Nếu sản phẩm vẫn giữ khả năng này thì vẫn phải chứng minh nó tạo khác biệt thật. Nhưng nếu cách hệ thống điều kiện hóa theo chế độ thay đổi (ví dụ chuyển từ một tham số trong prompt sang một bước lập kế hoạch riêng), thì observable dùng để đo có thể phải đổi theo.

### HIGH — phụ thuộc kiến trúc cao

Phép đo trực tiếp nhắm vào:

- một **component** cụ thể;
- một **intermediate artifact** (sản phẩm trung gian, ví dụ một cấu trúc dữ liệu nội bộ);
- một **cơ chế kiến trúc**;
- hoặc **đóng góp nhân quả** của một lựa chọn thiết kế cụ thể.

Ví dụ: bước lập outline có tạo giá trị không? Component lập kế hoạch nội dung có đóng góp gì không? Cấu trúc trung gian có đáng tồn tại không? Nhiều tác tử có tốt hơn một tác tử không?

**Quy tắc:** target HIGH **không được freeze** trước khi Candidate Architecture (kiến trúc ứng viên) được chốt. Không phải vì chúng không quan trọng — chúng thường là phần quan trọng nhất của một đồ án — mà vì câu hỏi chưa tồn tại cho tới khi biết kiến trúc có cơ chế đó hay không.

---

## 2. Trạng thái hiện tại (Current action)

- **KEEP** — giữ nguyên; câu hỏi và cách đo không phụ thuộc quyết định kiến trúc sắp tới.
- **REVISIT AFTER ARCHITECTURE** — câu hỏi nhiều khả năng vẫn còn, nhưng cách đo/ý nghĩa phải xem lại sau khi chốt kiến trúc.
- **PAUSE** — không nên đầu tư thêm cho tới khi một điều kiện cụ thể xảy ra.

Mọi mục `PAUSE` hoặc `REVISIT AFTER ARCHITECTURE` đều **bắt buộc** có `Điều kiện mở lại` viết cụ thể, không được ghi "xem lại sau".

---

## 3. Bảng tổng hợp

| # | Evaluation target | Arch. dependence | Current action |
| --- | --- | :---: | --- |
| 1 | source grounding | LOW | KEEP |
| 2 | coverage / important-fact presence | LOW | KEEP |
| 3 | hallucination | LOW | KEEP |
| 4 | incorrectness | LOW | KEEP |
| 5 | derived-number correctness | LOW | KEEP |
| 6 | artifact validity (file mở được / export thành công) | LOW | KEEP |
| 7 | schema validity của intermediate representation | HIGH | PAUSE |
| 8 | overflow | LOW | KEEP |
| 9 | design-token conformance | MEDIUM | REVISIT AFTER ARCHITECTURE |
| 10 | perceptual design quality | LOW | KEEP |
| 11 | editability | LOW | KEEP |
| 12 | ready-to-use | LOW (phần kỹ thuật) / MEDIUM (phần edit effort) | REVISIT AFTER ARCHITECTURE |
| 13 | `slide_type` differentiation | MEDIUM | REVISIT AFTER ARCHITECTURE / REQUIREMENT REVIEW |
| 14 | latency | LOW | KEEP |
| 15 | cost | LOW | KEEP |
| 16 | reliability | LOW | KEEP |
| 17 | historical regression | LOW | KEEP |
| 18 | architecture baseline (so với cách làm đơn giản nhất) | HIGH | PAUSE |
| 19 | component ablation | HIGH | PAUSE |
| 20 | source traceability | MEDIUM | REVISIT AFTER ARCHITECTURE |

Mục 20 không nằm trong danh sách tối thiểu được yêu cầu review nhưng được thêm vào vì nó là một requirement nội bộ rõ ràng và có mức phụ thuộc kiến trúc riêng.

---

## 4. Chi tiết từng target

### 4.1 Source grounding

**1. Ta muốn biết điều gì?**
Những điều xuất hiện trên slide có thực sự được tài liệu nguồn hỗ trợ hay không.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là lời hứa trung tâm của sản phẩm: deck được sinh **từ tài liệu của người dùng**, không phải từ kiến thức chung của mô hình. Requirement nội bộ yêu cầu block nội dung phải truy được về nguồn và không bịa số liệu hay ý chính. Nếu deck nói những điều nguồn không nói, sản phẩm mất lý do tồn tại so với việc gõ thẳng một câu lệnh vào một chatbot bất kỳ.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên. Dù bên trong là một lần gọi mô hình hay mười bước tác tử, câu hỏi "deck có bám nguồn không" vẫn hỏi được y hệt trên cặp `(tài liệu nguồn, deck cuối)`.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng (đang KEEP). Cần mở lại phần *phương pháp* nếu nguồn đầu vào mở rộng sang dạng phi văn bản nặng (video, bảng biểu, biểu đồ) — khi đó phép đo văn bản-với-văn bản không còn phủ hết.

---

### 4.2 Coverage / important-fact presence

**1. Ta muốn biết điều gì?**
Những ý quan trọng trong tài liệu nguồn có được đưa lên deck hay không.

**2. Vì sao Deck Agent cần biết điều này?**
Một deck có thể hoàn toàn không bịa gì mà vẫn vô dụng, vì nó bỏ mất đúng những ý người dùng cần. Đây cũng là chỗ có một phát hiện quan trọng từ nghiên cứu bên ngoài: **các metric chỉ kiểm "điều đã nói có đúng không" hoàn toàn mù với việc bỏ sót**. Trong một thí nghiệm trên tác vụ hỏi–đáp dạng dài, bản trả lời **thiếu hẳn câu trả lời chính** lại đạt điểm factuality **cao hơn** bản trả lời đúng và đủ (82,75% so với 82,58%). Nói cách khác: bỏ bớt nội dung có thể **làm tăng** điểm của loại metric chỉ chạy một chiều.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn. Đây là quan hệ giữa nguồn và output cuối.

**4. Architecture dependence: LOW**
Lưu ý một biến thể có mức cao hơn: nếu sản phẩm giữ khả năng chọn lọc thông tin theo chế độ trình bày (xem 4.13), thì **coverage có điều kiện theo chế độ** là MEDIUM, vì lúc đó "thiếu ý" và "cố ý bỏ ý" là hai chuyện khác nhau.

**5. Current action: KEEP**

**6. Reactivation trigger:** phần *có điều kiện theo chế độ* phải mở lại nếu và chỉ nếu sản phẩm xác nhận vẫn giữ nhiều chế độ trình bày.

---

### 4.3 Hallucination

**1. Ta muốn biết điều gì?**
Deck có nói ra điều mà nguồn hoàn toàn không đề cập hay không.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là failure nguy hiểm nhất về mặt uy tín: người dùng mang deck đi trình bày và phát biểu một điều tài liệu của chính họ không hề nói. Requirement nội bộ nêu "không bịa" như một mục tiêu tường minh.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng. (Việc **tách** hallucination khỏi incorrectness thì chưa giải quyết được — xem `03_UNRESOLVED_AND_REACTIVATION_MAP.md`.)

---

### 4.4 Incorrectness

**1. Ta muốn biết điều gì?**
Với những điều nguồn **có** đề cập, deck có nói sai giá trị/chiều/phạm vi không.

**2. Vì sao Deck Agent cần biết điều này?**
Sai một con số trong báo cáo tài chính hay một kết quả thí nghiệm là lỗi nghiêm trọng, nhưng **cách sửa khác hẳn** với sửa bịa: bịa thường là vấn đề của việc mô hình tự bổ sung, còn nói sai thường là vấn đề của việc trích xuất/nén nội dung. Gộp hai lỗi này vào một con số làm mất luôn thông tin cần để sửa.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng.

---

### 4.5 Derived-number correctness

**1. Ta muốn biết điều gì?**
Khi slide đưa ra một con số **không có trong nguồn nhưng suy ra từ nguồn** (ví dụ nguồn ghi doanh thu tăng từ `12,4M` lên `18,7M`, slide ghi `tăng ~51%`), con số đó tính đúng hay sai.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là loại nội dung mà một hệ sinh deck **nên** tạo ra — tóm tắt tốt thường là tóm tắt có tính toán lại. Nhưng nó tạo ra một cái bẫy đo lường: nếu coi mọi con số không khớp nguyên văn là bịa, hệ đo sẽ **phạt đúng hành vi tốt**; nếu bỏ qua, lỗi tính toán sẽ lọt.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên, và nó độc lập với cách hệ thống sinh ra con số đó.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng.

---

### 4.6 Artifact validity (file mở được / export thành công)

**1. Ta muốn biết điều gì?**
File xuất ra có mở được bằng phần mềm trình chiếu thật không, và tỉ lệ lần chạy tạo ra được file hoàn chỉnh là bao nhiêu.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là điều kiện cần tuyệt đối: một deck đẹp nhưng không mở được thì bằng không. Nghiên cứu bên ngoài cho thấy đây **không** phải chuyện nhỏ — trong một benchmark sinh slide bằng chương trình, tỉ lệ chạy được dao động từ khoảng 2% tới khoảng 89% tùy mô hình, và benchmark đó cho slide không chạy được điểm 0 ở cột tổng. Trong một hệ thống sinh presentation khác, việc bỏ một component khiến tỉ lệ thành công rơi từ 95,0% xuống 74,6% **trong khi điểm chất lượng gần như không đổi** — nghĩa là nếu chỉ nhìn điểm chất lượng, ta sẽ kết luận sai rằng component đó vô dụng.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên; đây là thuộc tính của file cuối.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng.

---

### 4.7 Schema validity của intermediate representation

**1. Ta muốn biết điều gì?**
Cấu trúc dữ liệu trung gian mà hệ thống dùng giữa bước lập kế hoạch và bước xuất file có hợp lệ theo schema đã định nghĩa không, và tỉ lệ hợp lệ ngay từ lần đầu là bao nhiêu.

**2. Vì sao Deck Agent cần biết điều này?**
Trong kiến trúc hiện đang được đề xuất, cấu trúc trung gian là "nguồn sự thật" nối hai nửa hệ thống, và có một requirement nội bộ bắt buộc validate output của mô hình. Nếu tỉ lệ hợp lệ thấp, chi phí retry tăng và độ tin cậy giảm.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
**Không chắc.** Nếu kiến trúc mới không có một intermediate representation tường minh (ví dụ sinh thẳng ra mã trình chiếu), câu hỏi này biến mất và được thay bằng "mã sinh ra có chạy không" — thuộc mục 4.6.

**4. Architecture dependence: HIGH** — nó đo một intermediate artifact.

**5. Current action: PAUSE**

**6. Điều kiện mở lại:** chỉ mở lại nếu Candidate Architecture cuối **vẫn có một intermediate representation tường minh** và đồ án coi nó là một phần của đóng góp hoặc của yêu cầu độ tin cậy. Khi mở lại, phép đo bản thân nó rất rẻ và không cần research thêm — chỉ cần biết schema là gì.

---

### 4.8 Overflow

**1. Ta muốn biết điều gì?**
Chữ có bị tràn ra ngoài khung hoặc ngoài slide không; các phần tử có đè lên nhau không.

**2. Vì sao Deck Agent cần biết điều này?**
Requirement nội bộ đặt "không chữ cắt/overlap thô" như một điều kiện của việc deck dùng được ngay. Đây cũng là một trong những lỗi mà nghiên cứu bên ngoài ghi nhận là **thật và phổ biến** ở các hệ sinh slide — một hệ thống được công bố tự nêu trong phần hạn chế rằng mô hình vẫn tạo ra lỗi thiết kế như phần tử chồng lên nhau làm giảm khả năng đọc.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên; đây là thuộc tính hình học của output cuối.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng. Lưu ý điều kiện tiên quyết kỹ thuật: phép đo cần **thông tin hình học** của slide (toạ độ, kích thước khung). Nếu giai đoạn đầu của sản phẩm chưa có hình học, phép đo chưa chạy được — đó là giới hạn công cụ, không phải giới hạn kiến trúc.

---

### 4.9 Design-token conformance

**1. Ta muốn biết điều gì?**
Các phần tử trên slide có dùng đúng bộ quy ước thiết kế (màu, font, cỡ chữ, khoảng cách) mà hệ thống định nghĩa hay không — và khi người dùng đổi một token thì thay đổi đó có lan đúng và đủ không.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là một capability được nêu rõ trong requirement nội bộ: có design token engine, lan truyền token phải nguyên tử, và có một ứng viên metric là *tỉ lệ phần tử dùng đúng token*.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Chỉ còn nếu sản phẩm **vẫn giữ khả năng** áp và lan truyền design token. Nếu kiến trúc mới để mô hình tự chọn style cho từng slide, khái niệm "đúng token" không còn định nghĩa được, và câu hỏi phải chuyển thành "các slide có nhất quán với nhau không" — một câu hỏi khác, đo bằng cách khác.

**4. Architecture dependence: MEDIUM**

**5. Current action: REVISIT AFTER ARCHITECTURE**

**6. Điều kiện mở lại:** kiến trúc cuối xác nhận vẫn có một hệ design token tường minh với danh sách token định nghĩa được. Ghi chú quan trọng cho lúc đó: một vòng tìm kiếm có chủ đích **không tìm thấy** metric bên ngoài nào đo "tuân thủ design token" theo nghĩa này — nghĩa là nếu làm, đây có thể là phần tự thiết kế, không có tiền lệ để dựa vào, nhưng cũng vì thế mà nó là một đóng góp có thể trình bày được.

---

### 4.10 Perceptual design quality

**1. Ta muốn biết điều gì?**
Nhìn bằng mắt thì deck có trông chỉn chu, dễ đọc, bố cục hợp lý không — phần **không** quy được về một luật hình học.

**2. Vì sao Deck Agent cần biết điều này?**
Một deck có thể tuân thủ đúng mọi token, không tràn chữ, mà vẫn trông xấu hoặc rối. Đây là phần cuối cùng còn lại sau khi đã bóc hết những gì kiểm được bằng mã.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên; đây là thuộc tính của ảnh render cuối.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng. Cần nhớ một điều khi dùng: theo nghiên cứu hiện có, đây là **chiều mà giám khảo mô hình đồng thuận với người tốt nhất** khi chấm tổng thể, nhưng lại **rất kém** khi phải chỉ ra từng lỗi thiết kế cụ thể. Chi tiết ở `02_EVALUATION_TOOL_SELECTION_GUIDE.md`.

---

### 4.11 Editability

**1. Ta muốn biết điều gì?**
File xuất ra có thực sự **sửa được** trong phần mềm trình chiếu không, và sửa được tới mức nào: text có phải là text thật hay chỉ là ảnh; hình khối có phải vector; có dùng cấu trúc master/layout để đổi một chỗ lan ra toàn bộ không; bảng/biểu đồ có phải đối tượng dữ liệu thật hay chỉ là hình vẽ.

**2. Vì sao Deck Agent cần biết điều này?**
Sản phẩm hứa deck "dùng được ngay hoặc chỉ sửa nhẹ". Nếu người dùng không sửa được thì lời hứa đó không thành. Có một benchmark bên ngoài đã hình thức hóa đúng ý này thành thang sáu mức, có quy tắc loại trừ: fail mức dưới thì không được tính mức trên.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên; đây là thuộc tính của file cuối. Đáng chú ý: nếu kiến trúc chuyển sang sinh HTML hoặc ảnh rồi mới đóng gói, mức editability có thể **tụt hẳn** — nên đây là một trong những phép đo nhạy nhất để phát hiện hệ quả xấu của một thay đổi kiến trúc.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng.

---

### 4.12 Ready-to-use

**1. Ta muốn biết điều gì?**
Hai câu hỏi khác nhau bị gộp dưới một cái tên:
(a) **kỹ thuật**: deck có sạch lỗi kỹ thuật tới mức dùng được ngay không (mở được, không tràn, đúng số slide, sửa được);
(b) **công sức người dùng**: người thật phải sửa bao nhiêu trước khi dám mang đi trình bày.

**2. Vì sao Deck Agent cần biết điều này?**
Đây gần như là mệnh đề giá trị của sản phẩm. Nhưng (a) và (b) khác nhau về bản chất: (a) đo được bằng mã, (b) hiện **không có phép đo bên ngoài nào** — một vòng tìm kiếm có chủ đích không tìm thấy metric nào lượng hóa công sức chỉnh sửa của người dùng trên deck sinh tự động; nguồn gần nhất chỉ mô tả định tính.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
(a) còn nguyên. (b) còn, nhưng nội dung của nó phụ thuộc vào việc sản phẩm có vòng chỉnh sửa (editor loop) hay không: "sửa nhẹ" trong một sản phẩm có editor tương tác khác hẳn "sửa nhẹ" khi người dùng phải mở PowerPoint tự sửa.

**4. Architecture dependence: LOW cho (a) / MEDIUM cho (b)**

**5. Current action: REVISIT AFTER ARCHITECTURE** (vì (b) quyết định phần lớn giá trị của mục này)

**6. Điều kiện mở lại:** kiến trúc và requirement cuối xác định rõ **người dùng sửa deck ở đâu** — trong sản phẩm hay ngoài sản phẩm. Trước khi có câu trả lời đó, việc thiết kế một phép đo edit effort là đầu tư vào một định nghĩa chưa tồn tại.

---

### 4.13 `slide_type` differentiation

**1. Ta muốn biết điều gì?**
Khi giữ nguyên tài liệu nguồn và **chỉ đổi** chế độ trình bày (`teaching` / `catchup` / `speaker_support`), deck sinh ra có khác nhau **một cách có chủ đích** không — chứ không phải khác nhau ngẫu nhiên.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là một claim sản phẩm tường minh trong requirement nội bộ (cùng tài liệu, đổi chế độ thì cấu trúc deck phải khác có chủ đích, và khác biệt phải đo được).

Ba điều cần biết trước khi đo:

- Câu hỏi này **không phải** thuộc tính của một deck. Không tồn tại "điểm slide_type" cho một deck đơn lẻ; đơn vị đánh giá là **bộ deck sinh từ cùng một nguồn**.
- Vì mỗi lần chạy đã tự khác nhau sẵn, phải có **nhóm đối chứng**: chạy lặp lại **trong cùng một chế độ** để biết mức khác nhau tự nhiên là bao nhiêu, rồi mới đọc khác biệt **giữa các chế độ**.
- Precedent gần nhất bên ngoài cho thấy việc điều kiện hóa theo đối tượng người nghe **có thể thất bại**: trong một benchmark sinh slide theo đối tượng, một hệ thống cho lợi thế âm khi được yêu cầu nhắm đối tượng chuyên gia, và nhóm tác giả kết luận rằng prompt theo đối tượng không tự động khớp với hồ sơ mong muốn nếu không có evaluation điều kiện hóa tương ứng. Nghĩa là: kết quả "chưa đạt" ở lần đo đầu là **khả năng thật**, không phải lỗi phép đo.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn **nếu và chỉ nếu** sản phẩm vẫn giữ ba chế độ như một claim chính thức. Nếu chế độ bị bỏ, gộp, hay đổi thành một trục khác (ví dụ độ dài trình bày), toàn bộ phép đo này phải viết lại.

**4. Architecture dependence: MEDIUM**

**5. Current action: REVISIT AFTER ARCHITECTURE / REQUIREMENT REVIEW**

**6. Điều kiện mở lại:** team xác nhận `teaching` / `catchup` / `speaker_support` **vẫn là một product claim chính thức**. Khi đó việc đầu tiên **không phải** là chọn metric, mà là viết định nghĩa sản phẩm cho ba chế độ (mỗi chế độ phục vụ mục đích gì, bắt buộc phải giữ/được phép bỏ loại thông tin nào) — vì không có định nghĩa thì không có kỳ vọng, và không có kỳ vọng thì phép đo không thể fail.

---

### 4.14 Latency

**1. Ta muốn biết điều gì?**
Sinh một deck mất bao lâu, đo ở mức trung vị và ở mức đuôi (chậm nhất trong nhóm phần trăm cao).

**2. Vì sao Deck Agent cần biết điều này?**
Có requirement nội bộ về thời gian cho deck khoảng 10 slide và về thời gian lan truyền token. Ngoài ra, mục đích lớn hơn: khi chất lượng tăng, cần biết **đã đánh đổi bằng gì**.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên — và trở nên **quan trọng hơn**, vì kiến trúc nhiều bước thường đổi chất lượng lấy thời gian.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng.

---

### 4.15 Cost

**1. Ta muốn biết điều gì?**
Chi phí gọi mô hình cho mỗi deck, và chi phí đó phân bổ vào những bước nào.

**2. Vì sao Deck Agent cần biết điều này?**
Requirement nội bộ yêu cầu ước lượng/đo chi phí mỗi deck và **báo cáo đánh đổi giữa số lần gọi mô hình và chất lượng**. Đây cũng là con số quyết định một kiến trúc nhiều bước có khả thi về mặt sản phẩm hay không.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên, và cũng trở nên quan trọng hơn với kiến trúc nhiều bước.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng.

---

### 4.16 Reliability

**1. Ta muốn biết điều gì?**
Tỉ lệ lần chạy thất bại, tỉ lệ phải thử lại, và cách hệ thống hành xử khi một phần thất bại.

**2. Vì sao Deck Agent cần biết điều này?**
Requirement nội bộ giới hạn số lần retry, bắt buộc validate output của mô hình, và yêu cầu giữ lại phần đã thành công khi có lỗi cục bộ. Với mục đích theo dõi hằng ngày, đây là loại tín hiệu bắt được regression sớm nhất và rẻ nhất.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Còn nguyên.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng.

---

### 4.17 Historical regression

**1. Ta muốn biết điều gì?**
Phiên bản hôm nay có tốt hơn phiên bản hôm qua không — và nếu tệ hơn thì tệ ở chỗ nào.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là một trong hai câu hỏi xuyên suốt của cả đồ án. Không có nó thì mọi metric khác chỉ là một tấm ảnh chụp một lần.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Câu hỏi còn nguyên, nhưng **chuỗi so sánh bị đứt** tại điểm đổi kiến trúc: nếu benchmark, prompt, model hay schema đổi cùng lúc, hai bên của phép so không còn cùng thang. Cách xử lý là đánh dấu điểm đứt và bắt đầu một chuỗi mới, không phải nối liền đường biểu diễn.

**4. Architecture dependence: LOW**

**5. Current action: KEEP**

**6. Reactivation trigger:** không áp dụng. Điều kiện tiên quyết: phải có benchmark đóng băng và run manifest — nếu thiếu, đây không phải vấn đề research mà là việc kỹ thuật phải làm.

---

### 4.18 Architecture baseline (so với cách làm đơn giản nhất)

**1. Ta muốn biết điều gì?**
Kiến trúc nhiều bước có thực sự tốt hơn cách làm đơn giản nhất — đưa toàn bộ nội dung đã trích xuất vào **một** lần gọi mô hình rồi lấy kết quả — hay không.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là câu hỏi phản biện nặng nhất khi bảo vệ: "phức tạp thế để làm gì?". Nghiên cứu bên ngoài về phương pháp luận cho thấy đây là rủi ro thật ở quy mô cả lĩnh vực: trong một phân tích tái lập nổi tiếng, phần lớn phương pháp mới được tái lập lại **thua các heuristic đơn giản khi baseline được tinh chỉnh tử tế** — nghĩa là baseline yếu tạo ra ảo giác tiến bộ.

Đi kèm là một cái bẫy ngược: nếu baseline thua vì lý do **không liên quan tới kiến trúc** (không được cho biết ràng buộc, không có validate/retry, prompt viết một lần), thì chênh lệch đo được là chênh lệch của **công sức**, không phải của **thiết kế**.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Câu hỏi còn, nhưng **nội dung của nó đổi hoàn toàn**: "đơn giản nhất" là gì phụ thuộc vào kiến trúc cuối trông thế nào. Không thể định nghĩa baseline trước khi biết mình đang so với cái gì.

**4. Architecture dependence: HIGH**

**5. Current action: PAUSE**

**6. Điều kiện mở lại:** Candidate Architecture cuối được chốt và đồ án tuyên bố rõ **đóng góp kiến trúc là gì**. Khi mở lại, việc đầu tiên là viết ra một câu duy nhất: *"baseline này thiếu đúng cơ chế nào so với hệ thống của chúng ta?"* — nếu không trả lời được bằng một câu, baseline chưa được định nghĩa đúng.

---

### 4.19 Component ablation

**1. Ta muốn biết điều gì?**
Một component cụ thể đóng góp bao nhiêu: tắt nó đi thì kết quả xấu đi theo trục nào.

**2. Vì sao Deck Agent cần biết điều này?**
Đây là cách chuẩn để chứng minh một lựa chọn thiết kế có giá trị. Có precedent trực tiếp trong đúng lĩnh vực sinh presentation: một hệ thống công bố bảng ablation bỏ từng component một, giữ nguyên mô hình, và kết quả cho thấy **các component hỏng theo những trục khác nhau** — bỏ bước trích xuất schema làm tỉ lệ thành công rơi mạnh nhất, bỏ bước lập outline làm mạch nội dung tệ nhất, còn bỏ bước render bằng mã thì gần như không đổi điểm chất lượng nhưng làm sập tỉ lệ thành công.

Bài học rút ra được: **đọc ablation trên một điểm tổng sẽ cho kết luận sai**. Phải đọc đồng thời ít nhất một trục chất lượng, một trục độ tin cậy và một trục vận hành.

Một cảnh báo nữa: **không được cộng dồn** đóng góp của các ablation ("bỏ A mất 8 điểm, bỏ B mất 5 điểm, vậy A+B đóng góp 13 điểm") — quan hệ giữa lượng cơ chế và kết quả thậm chí không đơn điệu.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Không — câu hỏi chỉ tồn tại cho những component thực sự tồn tại.

**4. Architecture dependence: HIGH**

**5. Current action: PAUSE**

**6. Điều kiện mở lại:** Candidate Architecture cuối liệt kê được các component tách rời và đồ án chọn component nào là đóng góp cần chứng minh. Yêu cầu kỹ thuật đi kèm nên được nêu **sớm với người làm kiến trúc**: thiết kế để bật/tắt component bằng cấu hình, thay vì phải tách nhánh mã — nếu không, mỗi ablation sẽ tốn gấp nhiều lần công sức.

---

### 4.20 Source traceability

**1. Ta muốn biết điều gì?**
Mỗi khối nội dung trên deck có con trỏ ngược về đúng vị trí trong tài liệu nguồn không, và con trỏ đó có trỏ đúng chỗ không.

**2. Vì sao Deck Agent cần biết điều này?**
Requirement nội bộ yêu cầu block nội dung trace được về nguồn. Về mặt sản phẩm, đây là thứ cho phép người dùng kiểm chứng nhanh thay vì phải tin.

Ghi chú đáng lưu ý: một vòng tìm kiếm có chủ đích **không tìm thấy** benchmark bên ngoài nào coi traceability của deck là một chiều đánh giá. Nghĩa là hai điều cùng lúc — đây có thể là điểm khác biệt của sản phẩm, và cũng có nghĩa **không có ai để so**.

**3. Nếu kiến trúc thay đổi mạnh, câu hỏi này còn không?**
Chỉ còn nếu sản phẩm vẫn giữ khả năng gắn con trỏ nguồn vào từng khối nội dung. Nếu kiến trúc mới sinh thẳng slide mà không mang theo liên kết nguồn, câu hỏi biến mất.

**4. Architecture dependence: MEDIUM**

**5. Current action: REVISIT AFTER ARCHITECTURE**

**6. Điều kiện mở lại:** kiến trúc cuối xác nhận vẫn mang thông tin liên kết nguồn tới tận output. Lưu ý phân biệt rạch ròi khi mở lại: **traceability là correctness requirement** (con trỏ có tồn tại và trỏ đúng không — kiểm bằng mã), **không phải** phép đo chất lượng AI, và không được trộn vào nhóm chỉ số bịa/sai/bỏ sót.

---

## 5. Tóm tắt: cái gì đang giữ, cái gì đang tạm dừng

**KEEP (13 mục)** — đo được ngay khi có hệ thống chạy, không phụ thuộc quyết định kiến trúc:
source grounding · coverage · hallucination · incorrectness · derived-number correctness · artifact validity · overflow · perceptual design quality · editability · latency · cost · reliability · historical regression.

**REVISIT AFTER ARCHITECTURE (4 mục)** — câu hỏi nhiều khả năng còn, nhưng phải xem lại cách đo:
design-token conformance · ready-to-use (phần edit effort) · `slide_type` differentiation · source traceability.

**PAUSE (3 mục)** — không đầu tư thêm cho tới khi kiến trúc chốt:
schema validity của intermediate representation · architecture baseline · component ablation.

Ba mục PAUSE có một điểm chung: chúng là những mục **có giá trị cao nhất cho phần bảo vệ đồ án** (chúng chính là phần chứng minh "kiến trúc của chúng tôi đáng tồn tại"), nhưng cũng là những mục **không thể định nghĩa** trước khi biết kiến trúc là gì. Đó không phải lý do để bỏ — đó là lý do để làm **đúng thứ tự**.
