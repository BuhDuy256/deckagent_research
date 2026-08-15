# 4 — Làm việc với paper

Giả định bạn ít kinh nghiệm đọc paper. Mục tiêu của file này là **hiểu**, không phải **sưu tầm**.

Nguyên tắc bao trùm: **bạn không đọc paper. Bạn đi tìm một con số hoặc một mệnh đề cụ thể, và kiểm xem nó có đứng vững không.**

---

## 4.1 Trước tiên: có nên đọc paper này không?

Phần lớn paper **không** đáng đọc toàn văn. Sàng bằng 3 câu, theo thứ tự:

**Câu 1 — Claim nào của Deck Agent đang dựa vào nó?**
Không trả lời được → không đọc. Đọc paper "cho biết" là cách tiêu thời gian dễ chịu nhất và vô ích nhất ở giai đoạn này.

**Câu 2 — Claim đó có chịu lực không?** (3 tiêu chí ở file 1 §1.5: sẽ phải bảo vệ / sai thì tốn ≥1 tuần / là mắt xích của claim trung tâm)
Không → ghi nhận và đi tiếp.

**Câu 3 — Nó được tag gì trong `evidence.md`?**
Mỗi RQ đã tag **MUST READ (3–5 nguồn)** / SHOULD READ / DISCOVERY ONLY. Bạn gần như chỉ cần đụng vào MUST READ.

> **Bạn không chọn từ 40 nguồn. Bạn chọn từ 3–5 nguồn của RQ đang đụng tới.** Việc sàng đã được làm rồi — dùng nó.

---

## 4.2 Thứ tự đọc — KHÔNG đọc tuần tự

Đây là điều khác trực giác nhất. Người mới thường đọc từ đầu đến cuối và kiệt sức ở phần Related Work. Thứ tự nên dùng:

**Bước 1 — Abstract (2 phút).** Tác giả claim gì? Ghi lại **bằng lời của bạn**, một câu.

**Bước 2 — Nhảy thẳng tới bảng/hình chứa con số bạn cần (10 phút).**
Đây là bước then chốt. Bạn đến vì một con số cụ thể — đi thẳng tới nó.

*Ví dụ:* bạn cần kiểm "PPTEval Coherence 0.55 vs Design 0.90". Đừng đọc PPTAgent từ đầu. Tìm bảng correlation, nhìn đúng hàng đó.

Ở bảng, kiểm 4 thứ: **trục là gì · đơn vị là gì · N bao nhiêu · so với baseline nào.**

**Bước 3 — Đọc Methods, nhưng chỉ phần sinh ra con số đó (15 phút).**
Không đọc cả Methods. Chỉ hỏi: *con số này được tạo ra thế nào?* Cụ thể: dữ liệu gì, ai/cái gì chấm, so với cái gì, có bao nhiêu mẫu.

**Bước 4 — Limitations (5 phút).**
Đây là phần **lợi nhất trên mỗi phút đọc**. Tác giả thường trung thực bất ngờ ở đây, và nó thường nêu sẵn đúng phản đối bạn đang mơ hồ cảm thấy.

**Bước 5 — Chỉ khi cần: Intro / Related Work.**
Chỉ đọc nếu bạn cần bối cảnh để hiểu vì sao bài toán tồn tại.

**Không bao giờ cần đọc:** phần chứng minh toán (trừ khi bạn định implement lại), phần khảo sát công trình liên quan không dính tới claim của bạn.

---

## 4.3 Tìm claim thật, không phải claim quảng cáo

Abstract viết để gây ấn tượng. Bảng số viết để chính xác. Chúng thường **không giống nhau**.

**Cách kiểm:** đọc claim trong abstract, rồi tìm chính xác con số hậu thuẫn nó. Rồi hỏi:

- Claim nói "robust" — robust trên **mấy trong mấy** điều kiện? (thường là 4/6, không phải 6/6)
- Claim nói "outperforms" — hơn **bao nhiêu**, và có khoảng tin cậy không?
- Claim nói "correlates with human" — **bao nhiêu** người, đồng thuận giữa họ ra sao?

**Ví dụ có thật trong dự án.** FineSurE đạt **86.4%** khi chỉ cần phát hiện *có lỗi hay không*, nhưng chỉ **42.2%** khi phải nói *lỗi loại nào*. Nếu chỉ đọc claim tổng "FineSurE phát hiện lỗi tốt", bạn sẽ bỏ mất chính xác cái quyết định: nó **không** tách được loại lỗi — mà đó lại là thứ Deck Agent cần (bịa vs. sai).

---

## 4.4 Kiểm chuyển giao — theo cơ chế, không theo cảm giác

Sau khi hiểu con số, hỏi 4 câu này **mỗi lần**:

| Câu hỏi | Ví dụ với FActScore |
| --- | --- |
| Input của họ là gì? | Tiểu sử — văn xuôi, câu đầy đủ |
| Output được chấm là gì? | Đoạn văn sinh ra |
| Bối cảnh ta khác ở đâu, **cụ thể**? | Bullet cụt không thành câu · nội dung phân mảnh qua nhiều slide · số bị tính toán lại · có bảng/hình |
| Cơ chế họ dựa vào còn tồn tại không? | Họ tách câu thành atomic fact — bullet **đã là** fragment cực đoan, tách nữa tạo mệnh đề không verify độc lập được |

Cột 3 và 4 mới là phân tích thật. "Cũng là sinh text nên chắc ổn" **không** phải phân tích.

Kết luận của bước này luôn thuộc một trong ba:
- **CHUYỂN GIAO ĐƯỢC** — cơ chế còn nguyên
- **CHUYỂN GIAO CÓ ĐIỀU KIỆN** — cần thích nghi X, và X kiểm được
- **KHÔNG BIẾT** — phải tự đo (→ file 5)

Đáp án **"KHÔNG BIẾT" là kết quả hợp lệ và thường gặp.** Ghi nó xuống, đừng ép thành có/không.

---

## 4.5 Khi nào lần theo citation, khi nào dừng

**Lần theo khi:** paper A nói *"như đã chứng minh ở B"* **và** claim đó chịu lực với bạn. Claim đi vay thì phải về tận chủ nợ.

**Dừng khi bất kỳ điều nào:**
- Bạn tới được nguồn sơ cấp (nơi thí nghiệm thật sự được chạy)
- Claim thôi chịu lực với quyết định của bạn
- Bạn đã đi 2 tầng — sâu hơn nữa thì gần như luôn là đào lạc đề

**Bẫy thường gặp:** mỗi paper trích 40 paper. Cảm giác "phải đọc thêm cái này nữa" là vô tận. Chốt chặn: *"Con số tôi cần có nằm trong paper tôi đang cầm không? Nếu có, dừng."*

---

## 4.6 Ghi gì lại

Đừng tóm tắt paper. Tóm tắt là việc AI làm tốt hơn bạn và không giúp bạn hiểu.

Ghi đúng 6 dòng này vào phiếu niềm tin:

```
Nguồn:            [tên, năm, link]
Mức verify:       [ĐỌC BẢNG GỐC | ĐỌC ABSTRACT | QUA TÓM TẮT AI]
Con số/mệnh đề:   [chính xác cái bạn tới lấy, kèm vị trí: "Table 5, hàng Coherence"]
Nó thật sự support: [phát biểu hẹp nhất mà con số này chứng minh]
Nó KHÔNG support:  [phát biểu rộng hơn mà người ta dễ suy ra nhầm]
Chuyển giao:      [ĐƯỢC | CÓ ĐIỀU KIỆN: ... | KHÔNG BIẾT]
```

Dòng **"nó KHÔNG support"** là dòng có giá trị nhất và hay bị bỏ. Ví dụ: PPTEval Design ρ=0.90 support *"judge chấm thẩm mỹ khá khớp người"*; nó **không** support *"judge đáng tin nói chung"* — vì cùng bảng đó Coherence chỉ 0.55.

---

## 4.7 Sai lầm hay gặp của người mới

| Sai lầm | Sửa |
| --- | --- |
| Đọc tuần tự từ đầu | Đi thẳng tới bảng chứa số cần |
| Tin abstract | Kiểm bằng bảng số |
| Sưu tầm paper để "đọc sau" | Nếu không gắn với claim chịu lực, không lưu |
| Bỏ qua Limitations | Đây là phần lợi nhất trên mỗi phút |
| Thấy paper mới là thấy đúng hơn | 7/13 nguồn của dự án là 2026, **chưa được cộng đồng replicate**. Mới ≠ vững |
| Nhận "khác nhau" thành "kém hơn" | Xem file 3 mục 12 |
| Coi tóm tắt AI là đã đọc | Xem file 3 mục 13 — đang là rủi ro mở của dự án |
