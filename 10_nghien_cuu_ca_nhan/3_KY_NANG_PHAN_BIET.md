# 3 — 13 phân biệt cần nhận ra

Đây là phần quan trọng nhất. Mỗi mục có: **phân biệt là gì → vì sao nó quan trọng → ví dụ thật trong Deck Agent → câu hỏi tự kiểm**.

Đọc một lượt, rồi quay lại tra khi mắc kẹt trong một phiên.

---

## 1. Claim vs. Grounds vs. Warrant

**Phân biệt.** *Claim* là kết luận. *Grounds* là evidence được nêu ra. *Warrant* là nguyên tắc cho phép đi từ grounds tới claim — và nó gần như **không bao giờ được viết ra**.

**Vì sao quan trọng.** Trong research của dự án này, grounds thường tốt (agent tìm nguồn giỏi). Chỗ hỏng gần như luôn ở warrant. Nếu bạn kiểm grounds trước, bạn tốn thời gian sai chỗ.

**Ví dụ thật.**
> CLAIM: MiniCheck là F2 candidate duy nhất đi tiếp.
> GROUNDS: bền nhất trong 6 metric ở một stress-test; phản ứng đúng với sửa factual; repo chính chủ, local, 0đ.
> WARRANT (ẩn): *độ bền trên benchmark tóm tắt văn bản dự đoán được độ bền trên slide deck.*

Grounds chắc. Warrant trống — 0 nguồn sau 2 lần search độc lập.

**Tự kiểm:** *"Điều gì phải đúng thì evidence này mới nói được về Deck Agent?"* Nếu không trả lời được trong một câu, warrant đang ẩn.

---

## 2. Construct validity — thước có đo đúng thứ nó tưởng không?

**Phân biệt.** Một phép đo chạy được, ra số, ổn định — vẫn có thể đang đo sai thứ.

**Vì sao quan trọng.** Toàn bộ dự án này **là** một bài toán đo lường. Đây là khái niệm trung tâm, không phải khái niệm phụ.

**Ví dụ thật.** `critical_fact_coverage` được đề xuất đo "deck có giữ ý quan trọng không". Nhưng nó chỉ đo đúng nếu `critical_facts` thật sự nắm bắt được "quan trọng". RQ02 U-9 nêu thẳng: quá ít fact → coverage bão hòa ở 100%, mất khả năng phát hiện regression (**ceiling effect**); quá nhiều → phạt Deck Agent vì đã làm đúng việc chọn lọc, vì deck 10 slide **nên** bỏ bớt ý phụ.

Cùng một công thức, cùng một code — nhưng số lượng fact quyết định nó có đo được thứ cần đo hay không.

**Tự kiểm:** *"Nếu số này tăng, điều gì trong thực tế đã tốt lên? Có cách nào nó tăng mà thực tế không tốt lên không?"*

---

## 3. External validity — evidence có chuyển giao được không?

**Phân biệt.** Kết quả đúng trong bối cảnh A không tự động đúng trong bối cảnh B.

**Vì sao quan trọng.** Gần như mọi nguồn E1 trong dự án này đến từ bối cảnh khác: tóm tắt văn bản, tiểu sử, poster, slide sinh tự do. Deck Agent không phải cái nào trong số đó.

**Cách hỏi đúng — theo cơ chế, không theo cảm giác.** Đừng hỏi "có giống không". Hỏi: *cơ chế mà paper dựa vào có còn tồn tại trong bối cảnh của ta không?*

**Ví dụ thật.** FActScore được validate trên tiểu sử — văn xuôi đầy đủ câu. Deck Agent sinh:
- bullet cụt, không thành câu
- nội dung phân mảnh qua nhiều slide
- số liệu đã bị **tính toán lại** (`12.4 → 18.7` thành `+51%`)
- bảng và hình

Mỗi gạch đầu dòng là một điểm cơ chế có thể gãy. Đó mới là phân tích chuyển giao; "cũng là sinh text" thì không phải.

**Tự kiểm:** *"Input của họ khác input của ta ở điểm nào, và điểm khác đó có phá cơ chế họ dựa vào không?"*

---

## 4. Precision vs. Recall — một điểm số che mất bạn hỏng ở đâu

**Phân biệt.** Đo "cái đã nói có đúng không" (precision) và "cái cần nói có được nói không" (recall) là **hai chiều ngược nhau**. Một phép đo chạy một chiều **không thể** thấy chiều kia — đây là giới hạn của định nghĩa, không phải của chất lượng model.

**Ví dụ thật, có số.** *All Claims Are Equal* (2025): một response **thiếu hẳn câu trả lời chính** ăn FActScore **82.75%**, trong khi response **đúng và đủ** chỉ **82.58%**. Metric precision-only không chỉ mù với bỏ sót — nó **chấm cao hơn** cho bản bỏ sót.

**Vì sao quan trọng cho bạn.** Trong development, kết quả `hallucination ↓ nhưng coverage ↓` **không** cho phép kết luận "version mới tốt hơn". Một score gộp sẽ giấu mất điều đó.

**Tự kiểm:** *"Metric này duyệt từ đâu sang đâu? Nếu deck bỏ sót một nửa nội dung, số này có đổi không?"*

---

## 5. Đọc con số tương quan — ρ = 0.532 nghĩa là gì

**Phân biệt.** ρ (Spearman/Pearson) **không phải** "đúng 53%". Nó đo mức đồng thuận về **thứ hạng**. Và quan trọng hơn: nó **vô nghĩa nếu không có trần để so**.

**Ví dụ thật, và đây là lập luận bạn cần sở hữu.**
```
PresentBench (checklist per-instance)   ρ ≈ 0.532
PPTEval      (rubric chung 1–5)         ρ ≈ 0.303
Người ↔ Người (trần)                    ρ ≈ 0.664   ← chú ý cái này
PPTEval: 4 người chấm, Fleiss κ ≈ 0.59
```

0.532 nghe thấp — cho tới khi bạn thấy **hai người thật cũng chỉ đồng thuận 0.664**. Judge đang đạt ~80% của trần người.

**Hệ quả trực tiếp:** đặt mục tiêu "judge phải đạt α ≥ 0.8 với người" là mục tiêu **không khả thi** trong domain này, vì trần chỉ ~0.6. Đây là lý do bạn bác ngưỡng 80% làm pass/fail (R-001) là đúng — nhưng lý do đúng không phải "80% quá cao", mà là **"nó là tỷ lệ suy ra từ một setup, không phải hằng số ngành"**.

**Tự kiểm:** *"Trần của bài toán này là bao nhiêu? Con số này so với trần thì thế nào?"* Không có trần → con số không diễn giải được.

---

## 6. Noise floor — bạn không thể phát hiện thay đổi nhỏ hơn nhiễu của chính mình

**Phân biệt.** Mọi phép đo có dao động nội tại. Chênh lệch nhỏ hơn dao động đó **không phải tín hiệu**.

**Vì sao đây là khái niệm thực dụng nhất trong toàn bộ file này.** Mission của bạn là "chứng minh cải thiện mỗi ngày". Nếu noise floor là ±3 mà bạn không biết, thì mọi delta +2 sẽ bị đọc thành tiến bộ. **Toàn bộ mission đứng trên con số này, và nó chưa được đo.**

**Ví dụ thật.** Cùng deck, cùng prompt, judge chạy 5 lần: 81 / 85 / 80 / 84 / 82. Đổi Planner, thấy 82 → 84. Không thể tuyên bố "+2 improvement".

**Điểm tinh tế mà RQ03 bổ sung:** không chỉ **judge** có noise floor — **generator** cũng có. Chạy lại cùng một `slide_type` hai lần cũng ra hai deck khác nhau. Nên khi so `teaching` vs `catchup`, bạn cần biết WITHIN (nhiễu cùng mode) trước khi đọc BETWEEN (khác giữa mode). Không có WITHIN thì không có nhóm đối chứng, và mọi khác biệt quan sát được **không phân biệt được với nhiễu**.

`temperature=0` **không** đảm bảo determinism.

**Tự kiểm:** *"Nếu tôi chạy lại y hệt, số này dao động bao nhiêu? Delta tôi đang mừng có lớn hơn con số đó không?"*

---

## 7. Confounding — bạn tưởng đổi một biến, thật ra đổi hai

**Phân biệt.** Nếu hai thứ thay đổi cùng lúc, không quy được kết quả cho cái nào.

**Ví dụ thật, rất cụ thể.** X+Slides gắn thời lượng khác nhau cho từng audience (12/15/8 phút). Nếu Deck Agent để `time_limit_minutes` thay đổi theo `slide_type`, thì khi so `teaching` vs `speaker_support`, bạn đang đổi **hai** biến. Khác biệt quan sát được không quy cho `slide_type` được nữa.

Đây là lý do RQ03 nhấn: **chỉ đổi đúng `meta.slide_type`, mọi thứ khác cố định, và ghi vào run manifest.**

**Tự kiểm:** *"Giữa hai nhánh đang so, chính xác có bao nhiêu thứ khác nhau?"* Nếu >1, kết luận nào cũng mơ hồ.

---

## 8. Goodhart — metric có thể thưởng đúng cái sản phẩm coi là lỗi

**Phân biệt.** Khi một phép đo thành mục tiêu, nó thôi là phép đo tốt.

**Ví dụ thật, và đây là ví dụ đẹp nhất trong dự án.** Mọi metric alignment (cả 6 cái được stress-test) **game được** bằng cách chèn câu nguyên văn từ source lên slide. Bây giờ ghép ba dữ kiện:

1. Chèn nguyên văn PDF lên slide là thứ một ContentPlanner **tệ** sẽ làm
2. Metric alignment **thưởng** cho điều đó
3. FR-22 coi chữ tràn/nhồi chữ là **lỗi**

→ Metric sẽ chấm cao cho đúng hành vi mà sản phẩm coi là hỏng.

Đó là lý do RQ02 bắt buộc: `unsupported_bullet_rate` **không được đọc một mình**, phải đọc cùng một chỉ số nén (tỷ lệ ký tự deck/source).

**Tự kiểm:** *"Cách rẻ nhất để làm số này đẹp mà không cải thiện sản phẩm là gì?"* Luôn hỏi câu này khi đề xuất một metric.

---

## 9. Falsifiability — giả thuyết phải có khả năng sai

**Phân biệt.** Một phát biểu không thể sai thì không phải evidence, dù nghe hợp lý.

**Ví dụ thật.** DoD ghi *"`slide_type` có khác biệt đo được"*. Nếu team **nhìn kết quả trước rồi mới viết hướng kỳ vọng**, thì bất kỳ kết quả nào cũng "khớp kỳ vọng" → mệnh đề không thể bác bỏ → không phải evidence.

Đó là toàn bộ lý do RQ03 P-02 yêu cầu bảng hướng kỳ vọng phải viết **trước** và được version hóa. Không phải hình thức — nó là ranh giới giữa evidence và tự thuyết phục.

**Tự kiểm:** *"Kết quả nào sẽ khiến tôi nói giả thuyết này sai?"* Không trả lời được → chưa phải giả thuyết.

---

## 10. Requirement vs. Research claim — hai loại phát biểu khác nhau

**Phân biệt.** *"Team muốn X"* và *"người khác quan sát được X ở nơi khác"* là hai loại mệnh đề. Chúng không mâu thuẫn theo nghĩa thông thường và **không giải được bằng cách đọc thêm**.

**Ví dụ thật.**
- FR-20 (PROJECT SOURCE): khác biệt giữa các `slide_type` nằm ở **chữ/ảnh/mật độ**.
- X+Slides (EXTERNAL EVIDENCE, E1): audience adaptation chủ yếu đổi **information selection**.

FR-20 là *ý định thiết kế*. X+Slides là *quan sát ở một hệ thống khác*. Cách giải: **đo trên chính Deck Agent** (Kiểm 3a của RQ03 sẽ chỉ ra feature nào thực sự mang tín hiệu), **hoặc** ra một quyết định sản phẩm.

**Hệ quả quan trọng:** nếu hóa ra khác biệt nằm ở information selection, thì phần lớn bộ feature đếm/tỉ lệ đang **nhắm sai chỗ**.

**Tự kiểm:** *"Đây là điều ta muốn, hay điều ai đó đo được?"*

---

## 11. Negative result — biết một thứ không tồn tại cũng là kết quả

**Phân biệt.** "Tìm không ra" khác "chưa tìm". Cái đầu, nếu tìm nghiêm túc, là dữ liệu.

**Ví dụ thật, ba cái đều có giá trị hành động:**
- **Không nguồn nào định nghĩa `teaching|catchup|speaker_support`** → đây **không phải** câu hỏi research. Đây là **quyết định sản phẩm**. Nhận ra điều này tiết kiệm hàng tuần tìm kiếm vô ích.
- **Không external work nào chấm điểm trực tiếp một planner/outline** → cách chuẩn để chứng minh giá trị của ContentPlanner là **ablation trên điểm deck cuối**, không phải phát minh "ContentPlanner Score".
- **0 nguồn áp F2 lên slide deck** (sau 2 lần search độc lập) → câu hỏi này **chỉ đóng được bằng pilot**, không đóng được bằng đọc.

**Tự kiểm:** *"Tôi đã tìm đủ nghiêm túc để coi 'không có' là dữ liệu chưa, hay chỉ mới tìm hời hợt?"*

---

## 12. Khi nào một nhánh nên bị bỏ

**Phân biệt.** Bỏ một nhánh cần **một quan sát phân biệt**, không cần cảm giác chán.

**Ví dụ thật.** RQ01 loại toàn bộ nhánh reference-based similarity (ROUGE, FID...) bằng một dữ kiện sắc: PPTAgent Table 3 cho thấy hệ có **ROUGE-L cao nhất (16.76)** lại có **Content thấp nhất (2.55)**; và PPL/FID tương quan với chất lượng ≈ 0.

Đó là cách bỏ đúng: metric đi **ngược chiều** thứ cần đo → không cứu được bằng tinh chỉnh.

So sánh với cách bỏ sai: "cái này phức tạp quá" hoặc "không thấy ai dùng".

**Tự kiểm:** *"Quan sát cụ thể nào khiến nhánh này không cứu được?"*

---

## 13. "Nguồn nói X" vs. "Tôi đã kiểm nguồn nói X"

**Phân biệt.** Đọc tóm tắt do tool sinh ra **không phải** đã đọc paper. Đây không phải chuyện cẩn thận thái quá — nó đang là rủi ro mở của dự án.

**Ví dụ thật.** RQ03 P-11 tự ghi: X+Slides và Learning to Present **chỉ đọc qua tool summary HTML, chưa mở PDF gốc**. Các số `−0.117`, `+0.083`, công thức `M = 0,25·K + Words/130`, trọng số `0.40/0.25/0.15/0.20` đều ở trạng thái đó. RQ02 còn có đoạn số *"26/29/6 vs 20/22/7"* không truy được nguồn, đã tự cấm dùng.

**Quy ước bắt buộc khi bạn ghi phiếu — luôn gắn một trong ba nhãn:**
```
[ĐỌC BẢNG GỐC]   — đã mở paper, đã nhìn đúng bảng chứa số đó
[ĐỌC ABSTRACT]   — chỉ đọc phần tóm tắt của tác giả
[QUA TÓM TẮT AI] — chưa verify, KHÔNG được trích vào báo cáo
```

**Tự kiểm:** *"Tôi đã tự nhìn thấy con số này ở đâu?"* Nếu câu trả lời là "trong file research của mình", thì bạn chưa verify.

---

## Bảng tra nhanh

| Triệu chứng | Nghi ngờ | Mục |
| --- | --- | --- |
| Evidence tốt mà kết luận vẫn thấy chông chênh | Warrant ẩn | 1 |
| Số đẹp lên nhưng sản phẩm không khá hơn | Construct validity / Goodhart | 2, 8 |
| Paper hay nhưng bối cảnh khác ta | External validity | 3 |
| Một điểm số cho nhiều loại lỗi | Precision/Recall | 4 |
| Không biết con số tương quan là tốt hay tệ | Thiếu trần | 5 |
| Mừng vì delta nhỏ | Chưa biết noise floor | 6 |
| So sánh mà kết quả khó diễn giải | Confounding | 7 |
| Giả thuyết nghe kiểu gì cũng đúng | Không falsifiable | 9 |
| Tài liệu nội bộ mâu thuẫn paper | Requirement vs claim | 10 |
| Search mãi không ra | Có thể là negative result | 11 |
| Nhánh này còn nên theo không | Cần quan sát phân biệt | 12 |
| Sắp trích một con số vào báo cáo | Mức verify | 13 |
