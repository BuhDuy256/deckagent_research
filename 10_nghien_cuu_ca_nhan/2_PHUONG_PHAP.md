# 2 — Phương pháp đề xuất

## 2.0 Trạng thái nhận thức của chính phương pháp này

Bạn yêu cầu rõ điều này, và nó quan trọng: **đừng tin một phương pháp chỉ vì nó được trình bày gọn gàng.**

| Thành phần | Trạng thái |
| --- | --- |
| Toulmin model (claim / grounds / **warrant**) | **ESTABLISHED** — Stephen Toulmin, *The Uses of Argument*, 1958. Chuẩn trong logic phi hình thức |
| Critical appraisal (đọc paper theo checklist thay vì tin kết luận) | **ESTABLISHED** — chuẩn trong evidence-based medicine |
| Phân cấp evidence (E1/E2/E3) | **ADAPTED** — bản rút gọn của evidence hierarchy y khoa (GRADE). Workspace này đã dùng sẵn |
| Pre-registration (viết kỳ vọng **trước** khi nhìn dữ liệu) | **ESTABLISHED** — chuẩn trong tâm lý học thực nghiệm và thử nghiệm lâm sàng |
| Falsifiability (điều gì sẽ chứng minh tôi sai) | **ESTABLISHED** — Popper |
| Strong inference (thiết kế phép thử **phân biệt** các giả thuyết đối nghịch) | **ESTABLISHED** — John Platt, *Science*, 1964 |
| Construct validity / external validity | **ESTABLISHED** — Cronbach & Meehl 1955; chuẩn trong đo lường |
| Manipulation check | **ESTABLISHED** — tâm lý học thực nghiệm. RQ03 đã dùng đúng |
| Decision journal / belief log | **ADAPTED** — thực hành phổ biến trong đầu tư và forecasting (Tetlock), **không** phải chuẩn học thuật |
| **Ghép chúng thành 2 chế độ + phiếu niềm tin, áp cho bối cảnh này** | **ĐỀ XUẤT CỦA TÔI** — không phải phương pháp có tên trong sách. Là workflow tôi thiết kế cho tình huống cụ thể của bạn |

Nói thẳng: **các viên gạch đều là chuẩn; cách xếp là của tôi.** Nếu sau vài tuần bạn thấy cách xếp không hợp, đổi cách xếp — đừng bỏ các viên gạch.

---

## 2.1 Vì sao vòng lặp trực giác của bạn chưa đủ

Bạn mô tả:

```
Vấn đề → giả thuyết → tìm evidence → cập nhật hiểu biết
→ phát hiện vấn đề mới → tìm tiếp → quyết định/thí nghiệm
```

Đây về cơ bản là **hypothetico-deductive cycle** — đúng, và là xương sống của nghiên cứu thực nghiệm. Tôi **giữ nó**. Nhưng nó có ba lỗ hổng đúng ở tình huống của bạn:

**Lỗ hổng 1 — nó giả định bạn bắt đầu từ vấn đề bạn tự thấy.**
Bạn đang bắt đầu từ ~5000 dòng kết luận người khác rút ra. Vòng lặp này không có bước "kiểm tra xem kết luận sẵn có có đứng vững không".

**Lỗ hổng 2 — nó không có bước phân loại mức độ quan trọng.**
Với 5000 dòng và 14 open question, kỹ năng cần **trước tiên** là chọn cái nào đáng đào. Đào đúng thứ tự quan trọng hơn đào giỏi.

**Lỗ hổng 3 — "cập nhật hiểu biết" là hộp đen.**
Trong thực tế đây là chỗ hỏng nhiều nhất: người ta đọc evidence, thấy nó "có vẻ ủng hộ", rồi cập nhật — mà không bao giờ hỏi *điều gì phải đúng thì evidence này mới nói được về Deck Agent*. Đó chính là **warrant**, và nó là mắt xích yếu nhất trong toàn bộ research hiện tại của dự án.

---

## 2.2 Phương pháp đề xuất — hai chế độ

### Chế độ A — TRUY VẾT (dùng cho claim đã có sẵn)

Dành cho: mọi thứ đã nằm trong `03_research/`, `05_decisions/`.

```
Chọn 1 claim CHỊU LỰC
   ↓
Viết lại claim thành một câu (nếu không viết được → claim đang mơ hồ)
   ↓
Viết NIỀM TIN HIỆN TẠI của bạn + độ tin, TRƯỚC KHI mở file research
   ↓
Truy ngược: claim ← evidence nào? ← nguồn nào? ← verify tới mức nào?
   ↓
Tìm WARRANT: điều gì phải đúng thì evidence này mới nói được về Deck Agent?
   ↓
Đánh giá: warrant có đứng không? evidence có thật sự support không, hay chỉ gợi ý?
   ↓
Cập nhật niềm tin + độ tin + ghi cái còn chưa biết
   ↓
Kết: GIỮ / SỬA PHẠM VI / HẠ ĐỘ TIN / CẦN THÍ NGHIỆM / BỎ
```

**Vì sao chế độ này đi trước:** đây là cách duy nhất để chuyển kết luận từ "AI nói" sang "tôi kiểm rồi". Và nó nhanh hơn tự research lại — bạn đứng trên bản đồ đã có.

**Lưu ý quan trọng:** truy vết **không** có nghĩa là đi tìm chỗ sai. Kết quả hợp lệ nhất thường là *"claim đúng, nhưng phạm vi hẹp hơn tôi tưởng"*.

### Chế độ B — TRA VẤN (dùng cho câu hỏi mới)

Dành cho: khi implementation gặp chuyện chưa ai research, hoặc khi chế độ A đẻ ra câu hỏi mới.

```
Quan sát / bất định cụ thể
   ↓
Viết nó thành CÂU HỎI CÓ THỂ SAI ĐƯỢC
   ↓
Viết giả thuyết + ĐIỀU GÌ SẼ LÀM TÔI ĐỔI Ý  (viết trước, bắt buộc)
   ↓
Phân loại: literature trả lời được, hay phải tự đo?   → xem file 5
   ↓
Nếu literature: tìm & đọc (file 4)
Nếu tự đo: thiết kế thí nghiệm nhỏ nhất có thể fail được
   ↓
Đối chiếu với điều đã viết trước
   ↓
Cập nhật + câu hỏi tiếp theo
```

Đây chính là vòng lặp của bạn, **cộng thêm hai chốt chặn**: viết điều-gì-làm-tôi-đổi-ý *trước*, và phân loại literature-vs-thí-nghiệm *trước khi* bắt đầu tìm.

### Tỉ lệ đề xuất cho giai đoạn này

**Khoảng 70% chế độ A, 30% chế độ B**, trong 4–6 tuần tới.

Lý do: đang tồn kho nhiều claim chịu lực chưa ai kiểm, và mốc bảo vệ đang tới. Khi tồn kho vơi và implementation bắt đầu sinh câu hỏi mới, tỉ lệ tự đảo sang B.

---

## 2.3 Mảnh ghép trung tâm: warrant

Nếu bạn chỉ lấy một thứ từ toàn bộ hướng dẫn này, lấy cái này.

Một lập luận có ba phần (Toulmin):

```
GROUNDS (evidence)  ──────►  CLAIM (kết luận)
                     ▲
                     │
                  WARRANT
        (nguyên tắc cho phép đi từ evidence tới claim —
         thường KHÔNG được viết ra)
```

Ví dụ thật, lấy nguyên từ `03_research/RQ02/`:

- **CLAIM:** MiniCheck là F2 candidate duy nhất đi tiếp cho Deck Agent.
- **GROUNDS:** một stress-test cho thấy MiniCheck bền nhất trong 6 metric; nó phản ứng đúng với sửa factual; có repo chính chủ, chạy local, 0đ API.
- **WARRANT (không được viết ra):** *độ bền trên benchmark tóm tắt văn bản dự đoán được độ bền trên slide deck.*

Grounds ở đây khá chắc. **Warrant thì trống rỗng** — RQ02 tự thừa nhận 0 nguồn sau hai lần search, và tự chấm confidence THẤP cho đúng điểm này.

**Bài học tổng quát:** trong dự án này, grounds thường tốt (agent tìm nguồn giỏi). Chỗ hỏng gần như luôn là warrant. Nên khi truy vết, **đừng tốn thời gian kiểm lại grounds trước — hỏi warrant trước.**

---

## 2.4 Các phương án khác, và vì sao tôi không chọn

| Phương án | Ưu | Vì sao không chọn |
| --- | --- | --- |
| **Đọc hết mọi thứ** | Không bỏ sót | 5000 dòng + 40 nguồn. Không có thứ tự ưu tiên nên sẽ hết thời gian ở chỗ ít quan trọng. Và đọc ≠ hiểu |
| **Tin research, spot-check ngẫu nhiên** | Rẻ | Lấy mẫu ngẫu nhiên bỏ sót đúng thứ cần: claim chịu lực chỉ chiếm số ít. Và không xây được kỹ năng |
| **Tự research lại từ đầu** | Sở hữu hoàn toàn | Lãng phí công đã bỏ ra; và bạn sẽ search chậm hơn agent. Sở hữu **kết luận** không đòi phải sở hữu **quá trình tìm kiếm** |
| **Systematic review đúng chuẩn** | Nghiêm ngặt nhất | Vài tháng công. Sai công cụ cho một đồ án có deadline |
| **Chỉ học qua chạy pilot** | Học bằng tay, rất thật | Không xây kỹ năng thẩm định evidence; và sẽ lặp lại những sai lầm literature đã ghi nhận. **Nhưng đây là bổ sung tốt** — xem 2.5 |

## 2.5 Vì sao tôi chọn cách này

**(a) Nó nhắm đúng chỗ hỏng đã biết.** Chỗ yếu của research hiện tại không phải thiếu nguồn — mà là warrant chưa được kiểm và số chưa được verify. Chế độ A tấn công thẳng cả hai.

**(b) Nó dùng lại tài sản thay vì vứt đi.** File `evidence.md` đã tag MUST READ 3–5 nguồn/RQ. Bạn không phải chọn từ 40 nguồn — chỉ từ 3–5.

**(c) Nó hợp người vừa học research vừa phải build.** Mỗi phiên độc lập, 90 phút, để lại artifact dùng được. Không cần khối thời gian liên tục.

**(d) Sản phẩm phụ chính là vật liệu báo cáo.** Phiếu niềm tin có sẵn cấu trúc *"trước tin X → evidence Y → giờ tin Z"*. Đó đúng là thứ phần "thảo luận/quyết định" của luận văn cần, và là thứ hầu như không ai viết được nếu không ghi lúc đang làm.

**(e) Nó khớp với việc bạn đã tự làm đúng rồi.** Cơ chế ACCEPT/HOLD/REJECT của bạn về bản chất là quản lý độ tin theo mức evidence. Phương pháp này chỉ đưa cùng nguyên tắc đó xuống tầng từng claim.

---

## 2.6 Điểm yếu và cách hỏng của phương pháp này

Nói trước để bạn nhận ra khi nó đang xảy ra:

**H1 — Tê liệt vì audit.** Truy vết mãi, không bao giờ build. *Dấu hiệu:* ba phiên liên tiếp không dẫn tới quyết định hay thí nghiệm nào. *Chặn:* mỗi phiên phải kết bằng một trong 5 kết luận ở cuối chế độ A. "Cần đọc thêm" **không** phải kết luận hợp lệ hai phiên liên tiếp trên cùng một claim.

**H2 — Viết niềm tin-trước một cách hình thức.** Mở file research trước, rồi mới viết "niềm tin trước" — lúc đó nó đã nhiễm rồi. *Đây là cách hỏng nguy hiểm nhất vì nó vô hình.* **Chặn:** viết prior xong mới được mở bất cứ thứ gì. Nếu lỡ đọc trước, ghi thẳng vào phiếu là "prior bị nhiễm" — thà mất giá trị một phiếu còn hơn tự lừa.

**H3 — Nhầm "tôi truy được" với "nó đúng".** Truy vết cho thấy *hình dạng* lập luận, không chứng minh kết luận. Một chuỗi truy vết đẹp vẫn có thể sai nếu warrant sai.

**H4 — Tự tin quá sớm sau một paper.** Đọc một paper kỹ dễ tạo cảm giác đã hiểu cả lĩnh vực. *Chặn:* luôn ghi "cái này còn chưa biết" trong phiếu, kể cả khi thấy đã rõ.

**H5 — Chậm.** 90 phút cho một claim là thật. Với ~10 claim chịu lực là ~15 giờ. Đó là chi phí thật, phải nhìn thẳng. Bù lại: 15 giờ đó cũng sinh ra phần "vì sao" của báo cáo.

**H6 — Chọn sai claim để đào.** Nếu tiêu chí chịu-lực bị áp lỏng lẻo, bạn sẽ đào thứ thú vị thay vì thứ quan trọng. *Chặn:* dùng đúng 3 tiêu chí ở `1_BAN_DANG_O_DAU.md` §1.5, và file 8 đã chọn sẵn 3 cái đầu tiên cho bạn.

---

## 2.7 Bổ sung: học bằng pilot

Phương án "chỉ học qua pilot" bị loại ở trên, nhưng **pilot là bổ sung bắt buộc**, không phải phương án thay thế.

Lý do: một số câu hỏi quan trọng nhất hiện nay **không thể** trả lời bằng đọc — chúng phụ thuộc phân phối output của chính Deck Agent. Ví dụ: *MiniCheck có chạy được khi premise là text deck ngắn không* (RQ02 U-1), *noise floor của generator là bao nhiêu* (RQ03 U-2).

Ranh giới đó là nội dung file 5. Nguyên tắc gọn: **nếu câu trả lời phụ thuộc vào tính chất output của Deck Agent, không lượng đọc nào giải được.**
