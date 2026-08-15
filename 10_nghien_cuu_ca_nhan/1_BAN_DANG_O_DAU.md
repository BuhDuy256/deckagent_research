# 1 — Bạn đang ở đâu

Đọc file này trước khi học phương pháp. Học phương pháp mà không biết mình đang thiếu gì thì sẽ học sai chỗ.

---

## 1.1 Bạn đã sở hữu gì (thật, không phải khách sáo)

Đây không phải lời động viên. Đây là dữ kiện quan sát được từ chính lịch sử làm việc của dự án này.

### Bạn sở hữu thiết kế quy trình research

Cấu trúc wave (RQ00 → Wave 1 → Gate → Wave 2A), cơ chế ACCEPT/HOLD/REJECT/OPEN, quy tắc "research không tự thành design", việc bắt agent chạy ở session độc lập để không anchor lẫn nhau — **đều là quyết định của bạn**. Không có cái nào do AI đề xuất trước.

Giá trị của nó không trừu tượng. Hai ví dụ đo được:

- **Gate G-05 (HOLD F2)** đã cứu dự án khỏi một sai lầm cụ thể. Wave 1 chỉ về AlignScore. Nếu bạn accept thẳng, team đã đi implement nó. Wave 2A tra lại và thấy AlignScore *"particularly unreliable"* và tệ nhất đúng ở đặc tính của deck (bullet là claim bị nén). **Cái HOLD của bạn là thứ chặn sai lầm đó.**
- **Bạn bác baseline B-1** vì nó vẫn dùng Deck IR ở cả hai nhánh nên không thể chứng minh gì về Deck IR. Đó là một lỗi logic thật, AI viết ra và không tự thấy.

### Bạn sở hữu tầng lập luận logic

Ba lần trong dự án này bạn đã audit đúng mà không cần đọc paper nào:
- B-1 không prove Deck IR (lỗi phạm vi claim)
- A1 ≡ B-1 (hai thí nghiệm trùng nhau được gọi hai tên)
- "80% human ceiling" là heuristic từ một setup, không phải chuẩn ngành

**Đây chính là kỹ năng research, đã hoạt động.** Cái bạn còn thiếu không phải năng lực suy luận — mà là làm điều tương tự ở **tầng evidence**, nơi cần mở paper ra và đọc bảng số.

### Bạn sở hữu bối cảnh sản phẩm

Bạn biết FR/NFR, biết kiến trúc, biết cái gì P0 cái gì P3, biết deadline. AI không biết những ràng buộc này trừ khi được nói. Ví dụ: chỉ bạn mới nhận ra rằng dùng `speaker_notes` làm thuộc tính phân biệt chính là **thay đổi ưu tiên FR**, không phải chọn metric — vì FR-55 đang ở P3.

---

## 1.2 Research bằng AI đã giải quyết được gì cho bạn

Nói công bằng, phần này làm rất tốt và không nên làm lại:

- **Bản đồ landscape.** 40+ nguồn được sàng, phân loại E1/E2/E3. Tự làm mất hàng tuần.
- **Negative finding.** Ba cái quan trọng: (a) không nguồn nào định nghĩa `teaching|catchup|speaker_support`; (b) không nguồn nào chấm điểm trực tiếp một planner/outline; (c) sau **hai** lần search độc lập, **0 nguồn** áp F2 (NLI/alignment) lên slide deck. Biết một thứ *không tồn tại* có giá trị ngang biết nó tồn tại — và tốn công y hệt.
- **Phát hiện lỗ hổng trong chính tài liệu nội bộ.** Ví dụ sắc nhất: checklist per-instance của NFR-53 **không thể bắt được hallucination**, vì nó là danh sách *đóng* các thứ phải có, còn bịa là tập *mở* các thứ không được có. §8 đặt "không bịa" làm mục tiêu nhưng method nó đề xuất không cover mục tiêu đó.
- **Verify entity.** PresentBench có thật (arXiv 2603.07244), Open Design có thật (`nexu-io/open-design`), `"test hapkin slide"` **không** verify được và đã bị đánh dấu UNVERIFIED thay vì đoán bừa.

---

## 1.3 Cái AI research KHÔNG giải quyết được cho bạn

### (a) Nó không làm bạn hiểu cơ chế

Bạn biết kết luận *"MiniCheck là candidate duy nhất đi tiếp"*. Nhưng để bảo vệ được kết luận đó, bạn cần trả lời được:

- Một mô hình NLI/entailment thực ra **tính cái gì**?
- "Premise" và "hypothesis" là gì, và vì sao **đảo chiều** chúng lại là chuyện lớn?
- "Ngoài phân phối huấn luyện" nghĩa là gì cụ thể ở đây — nó là phản đối chí mạng hay chi tiết sửa được?

Không có ba câu này, bạn không thể quyết định có nên đầu tư một tuần xây pipeline quanh nó hay không.

### (b) Nó không kiểm chứng được số liệu hộ bạn — và đây là rủi ro đang mở

`03_research/RQ03/recommendation.md` P-11 ghi thẳng: hai nguồn quan trọng nhất (X+Slides, Learning to Present) **chỉ được đọc qua tool summary của HTML, chưa mở PDF gốc**. Cụ thể chưa verify: `−0.117`, `+0.079`, `+0.082`, `+0.083`, công thức `M = 0,25·K + Words/130`, trọng số `0.40/0.25/0.15/0.20`, điểm `0.530/0.616`.

RQ02 còn có một đoạn số *"26/29/6 vs 20/22/7"* xuất hiện khi search mà **không truy được nguồn** — đã tự đánh dấu cấm dùng.

**Nếu một con số chưa verify lọt vào báo cáo bảo vệ và hội đồng kiểm tra, đó là sự cố không sửa được tại chỗ.** Đây là rủi ro cụ thể, có deadline, không phải lo xa.

### (c) Nó không thay bạn phán đoán chuyển giao

Câu hỏi *"evidence từ tóm tắt văn bản có áp được cho slide deck không"* **không có trong bất kỳ paper nào**. RQ02 đã search hai lần độc lập và xác nhận 0 nguồn. Đây là phán đoán phải do người hiểu cả hai phía đưa ra — và người đó là bạn, không phải AI.

### (d) Nó không giải quyết được mâu thuẫn giữa requirement và evidence

FR-20 (tài liệu nội bộ) nói khác biệt giữa các `slide_type` nằm ở **chữ/ảnh/mật độ**. X+Slides (E1, bên ngoài) kết luận audience adaptation chủ yếu đổi **information selection**.

Hai mệnh đề này **khác loại**: một cái là *team muốn gì*, một cái là *người khác quan sát được gì ở nơi khác*. Không thể giải bằng cách đọc thêm. Chỉ giải được bằng đo trên dữ liệu của chính Deck Agent — hoặc bằng một quyết định sản phẩm.

---

## 1.4 Rủi ro nếu tiếp tục chỉ tiêu thụ bản tóm tắt

Xếp theo mức độ cụ thể, không phải mức độ đáng sợ:

| Rủi ro | Vì sao nó thật trong dự án này |
| --- | --- |
| **Trích số sai vào báo cáo** | P-11 đang mở. Ít nhất 7 con số chưa verify đang nằm trong file research |
| **Không trả lời được câu hỏi cơ chế khi bảo vệ** | Hội đồng gần như chắc chắn hỏi "vì sao tin evaluation của em đáng tin" |
| **Xây một tuần lên nền sai** | Toàn bộ đề xuất daily tracking treo trên giả định "F2 chuyển giao được", mà RQ02 tự chấm confidence **THẤP**, 0 nguồn |
| **Không tự sinh được câu hỏi mới** | Khi implementation gặp chuyện chưa ai research, không có file nào để đọc. Đây là lúc kỹ năng mới lộ ra |
| **Đọc nhầm nhiễu thành tiến bộ** | Chưa ai đo noise floor. Nếu không hiểu vì sao phải đo, sẽ bỏ qua bước này và mọi con số "cải thiện" thành vô nghĩa |

---

## 1.5 Cái bạn PHẢI tự sở hữu

Dùng đúng ba tiêu chí này để lọc, không cố hiểu mọi thứ:

**Tiêu chí 1 — Bạn sẽ phải bảo vệ nó trước hội đồng.**
**Tiêu chí 2 — Sai thì tốn ≥ 1 tuần implementation.**
**Tiêu chí 3 — Nó là mắt xích chịu lực của claim trung tâm.**

Áp vào trạng thái hiện tại, chuỗi chịu lực của claim *"chúng tôi đo được sự cải thiện"* trông như sau:

```
"Chúng tôi đo được cải thiện"
 └── metric phát hiện được thay đổi thật
     ├── metric đo đúng thứ nó tưởng          (construct validity)
     │   ├── MiniCheck chuyển giao sang deck   ← confidence THẤP, 0 nguồn  ⚠ YẾU NHẤT
     │   ├── tách 3 failure là đúng            ← confidence CAO
     │   └── coverage cần danh sách đóng       ← confidence TB-CAO, có 1 nguồn ngược
     └── thay đổi vượt được nhiễu
         └── đã đo noise floor                 ← CHƯA LÀM  ⚠
```

Hai ô ⚠ là nơi cả tòa nhà đứng lên. **Đó là chỗ bạn phải tự hiểu.**

---

## 1.6 Cái bạn KHÔNG cần sở hữu ở cùng độ sâu

Quan trọng ngang phần trên — vì thời gian có hạn và cầu toàn ở sai chỗ cũng là một cách thất bại:

| Không cần đào sâu | Vì sao |
| --- | --- |
| Kiến trúc bên trong mô hình NLI (transformer layer, cách train) | Bạn cần biết nó **làm gì** và **hỏng ở đâu**, không cần biết nó được xây thế nào |
| Chứng minh toán của permutation test / bootstrap | Cần hiểu chúng trả lời câu hỏi gì. Công thức tra được lúc cần |
| Cả 40+ nguồn trong `evidence.md` | Chúng đã được tag MUST READ (3–5/RQ) / SHOULD READ / DISCOVERY ONLY. Chỉ đọc MUST READ, và chỉ của RQ đang đụng tới |
| Khảo sát external system của RQ08 | Gate đã accept G-13: external comparison chỉ ở mức contextual. Rủi ro thấp |
| Chi tiết CIEDE2000, PEI level, các công thức hòa sắc | Thuộc RQ04, còn BLOCKED, và phần lớn là deterministic — ít khả năng sai lệch |

**Quy tắc thực dụng:** nếu sai ở đó chỉ tốn một buổi sửa, không cần tự hiểu sâu. Để đó, đọc khi cần.

---

## 1.7 Kết luận của file này

Bạn không ở vạch xuất phát. Bạn ở một vị trí khá đặc thù:

> **Có một khối kết luận lớn, chất lượng tốt, mà bạn không tự rút ra — và một số trong đó sắp phải đem đi bảo vệ.**

Vấn đề không phải "học research từ đầu". Vấn đề là **giành lại quyền sở hữu những claim chịu lực**, và làm được điều đó đủ nhanh trước khi implementation + bảo vệ ập tới.

Đó là lý do phương pháp ở file 2 có **hai chế độ**, và chế độ dành cho claim có sẵn được đặt trước.
