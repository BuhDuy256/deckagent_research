# Prompt: Verify Evidence

Dùng để sanity-check một claim đã ghi trong 04_evidence/ hoặc 00_context/ trước khi dựa vào nó cho một design decision.

```
Bạn đang verify một claim cho dự án evaluation của Deck Agent.

Claim: <claim, và nó đang được ghi ở đâu>
Nguồn: <evidence mà claim này dựa vào>

Kiểm tra:
- Nguồn có thực sự support claim như đã nêu, hay đang bị overgeneralize?
- Nguồn có còn current không (với external tool/product/API, phải re-check thay vì tin note cũ)?
- Có áp dụng đúng vào bối cảnh thực tế của Deck Agent không, hay chỉ đúng trong một bối cảnh khác?

Báo cáo: confirmed / needs qualification / contradicted, kèm lý do cụ thể. Cập nhật evidence entry, hoặc flag trong 05_decisions/OPEN_QUESTIONS.md nếu nó làm mất hiệu lực một điều đã được giả định trước đó.
```
