# Evidence Policy (Chính sách về Evidence)

File này quyết định: **agent được phép coi cái gì là evidence, và ở mức tin cậy nào.** Đọc file này trước khi ghi bất kỳ finding/recommendation nào vào `03_research/` hoặc `04_evidence/`.

## Các mức evidence (Evidence Levels)

### E1 — Primary / authoritative

- Original research paper (bản gốc, không qua trung gian).
- Official benchmark paper / official benchmark repository.
- Official open-source repository (code, README, docs chính chủ).
- Official technical documentation (API docs, product docs từ chính nhà phát triển).

→ Có thể support một technical decision quan trọng.

### E2 — Strong secondary

- Survey paper.
- Technical report uy tín.
- Secondary analysis chất lượng cao (ví dụ bài phân tích lại nhiều paper).

→ Dùng làm supporting evidence, ưu tiên tìm E1 gốc nếu có thể.

### E3 — Informal / exploratory

- Blog, tutorial, community discussion, demo video.

→ Hữu ích để discover ý tưởng. **Không được là evidence duy nhất cho một major technical recommendation.**

## Quy tắc bắt buộc

1. **Không được invent**: paper, metric, benchmark, dataset, repository, kết quả số liệu, hay capability của competitor/sản phẩm khác. Nếu không chắc một entity có tồn tại, phải search để verify — không đoán.
2. **Không verify được → đánh dấu `UNVERIFIED`**, đưa vào `05_decisions/OPEN_QUESTIONS.md`, không dùng làm evidence cho recommendation.
3. **Paper được nhắc qua secondary source** (blog, survey trích dẫn) → phải tìm bản gốc trước khi coi là E1. Nếu không tìm được bản gốc, giữ ở mức E2/E3 tùy nguồn trích và ghi rõ "trích qua [nguồn X], chưa verify bản gốc".
4. **Product/competitor capability có thể thay đổi theo thời gian** — mọi entry phải ghi: thời điểm research (ngày), nguồn, version/release nếu xác định được.
5. **Bốn loại thông tin phải phân biệt rõ** trong mọi research output (xem thêm `AGENTS.md`):
   - **PROJECT SOURCE** — điều nói trong `01_source_docs/` (internal, không phải sự thật đã kiểm chứng bên ngoài).
   - **EXTERNAL EVIDENCE** — điều được E1/E2 hỗ trợ, phải có citation.
   - **INFERENCE** — kết luận suy ra từ việc kết nối evidence với bối cảnh Deck Agent, phải nói rõ đây là suy luận.
   - **RECOMMENDATION** — đề xuất hành động, phải trace được `problem → evidence → reasoning → recommendation`, không viết như fact.

## Format ghi một evidence entry (trong `04_evidence/EVIDENCE_INDEX.md` hoặc note riêng)

```
### <tên nguồn>
Level: E1 | E2 | E3 | UNVERIFIED
Ngày research: YYYY-MM-DD
Link/nguồn: ...
Áp dụng cho RQ: RQ0x
Tóm tắt claim: ...
Mức áp dụng cho Deck Agent: <trực tiếp / cần thích nghi / chỉ mang tính tham khảo>
```
