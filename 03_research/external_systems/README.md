# External Systems Research

Track nghiên cứu các hệ thống bên ngoài có bài toán gần với Deck Agent. **Không mặc định gọi một hệ thống là "competitor"** — tùy trường hợp nó có thể là: related system, architecture reference, evaluation inspiration, benchmark inspiration, hoặc potential baseline. Kết luận đó thuộc về sau khi research xong, không phải trước.

## Nguyên tắc

- Nếu system có source code public: đọc README, architecture doc (nếu có), evaluation/test setup, issues/discussion liên quan, benchmark/test fixture, design decision, và implementation nếu cần — **không chỉ đọc marketing page**.
- Mục tiêu không phải copy architecture của họ. Mục tiêu là học: họ giải bài toán thế nào, họ test thế nào, họ định nghĩa quality thế nào, họ lưu intermediate representation thế nào (nếu có), họ đánh giá output thế nào, điểm nào có thể thành baseline/inspiration cho Deck Agent.
- Tuân thủ `04_evidence/EVIDENCE_POLICY.md`: không invent repository/capability, ghi rõ thời điểm research + nguồn + version.
- Nếu tên một entity (project/paper/benchmark) không chắc chắn: search để verify tên → tìm canonical/original source → nếu vẫn không xác định được, ghi `UNVERIFIED ENTITY`, đưa vào `05_decisions/OPEN_QUESTIONS.md`, **không dùng làm evidence**, không đoán.

## Format mỗi entry (một file riêng, vd. `opendesign.md`)

```
System:
Nguồn (repo/docs):
Version/ngày check:

Bài toán giải quyết:
Input:
Output:
Architecture:
Intermediate representation (nếu có):
Agent workflow (nếu có):
Evaluation/testing:
Quality control:
Open-source artifact:
Điểm giống Deck Agent:
Điểm khác quan trọng:
Bài học có thể rút ra:
Có thể làm baseline không? Vì sao?
Có thể inspire benchmark không? Vì sao?
```

## Candidate hiện tại

| System | Trạng thái | Ghi chú |
| --- | --- | --- |
| [OpenDesign](opendesign.md) | Chưa research | Được user đề xuất; đã được nhắc tới trong `01_source_docs/requirements/requirements-non-functional.pdf` (mục 0.1, so sánh mô hình BYOK) — đây là PROJECT SOURCE gợi ý, chưa phải external evidence đã verify. Cần xác định canonical repo trước khi research sâu. |
| "test hapkin slide" | **UNVERIFIED ENTITY** | Tên do user nhớ lại từ trao đổi với team, có thể sai chính tả/chưa chính xác. Xem `05_decisions/OPEN_QUESTIONS.md` Q-004 — không được đoán project này là gì, không dùng làm evidence cho tới khi verify được tên chính xác. |
