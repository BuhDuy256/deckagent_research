# Prompt: Research một chủ đề

Dùng khi mở một research thread mới cho một RQ chưa có câu trả lời (vd. "các paper hiện có đo hallucination trong nội dung slide sinh tự động thế nào?").

```
Bạn đang research cho dự án evaluation của Deck Agent (xem CLAUDE.md / 00_context/ để có project context).

Chủ đề: <chủ đề>
RQ liên quan: <RQ0x trong 02_research_questions/>

Tìm và tóm tắt hiện trạng kiến thức về chủ đề này. Với mỗi nguồn:
- Nguồn đó claim/đề xuất gì
- Áp dụng trực tiếp thế nào vào bối cảnh Deck Agent (pipeline stage, quality dimension)
- Mức độ tin cậy (evidence level theo 04_evidence/EVIDENCE_POLICY.md) và caveat

Chưa đề xuất câu trả lời cuối cùng cho RQ — chỉ trình bày evidence. Lưu nguồn vào 04_evidence/ (papers/ hoặc products/ tùy loại) và cập nhật 04_evidence/EVIDENCE_INDEX.md. Không invent citation — nếu không verify được, ghi UNVERIFIED.
```
