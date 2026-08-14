# Bối cảnh dự án (Project Context)

## Deck Agent là gì

Deck Agent là hệ thống AI sinh deck trình bày (PPTX) từ tài liệu nguồn (vd. PDF). Đây là đồ án/capstone của team ("đồ án"). Repo research này **không phải codebase sản phẩm** — nó là evaluation function chạy song song với quá trình phát triển sản phẩm.

## Pipeline (theo hiểu biết hiện tại từ project source)

```
Input Adapters → Extractor → ContentPlanner → Deck IR → DesignSystem/Editor Loop → Selection Resolver → Preview Renderer → Exporters
```

- **Input Adapters** — nhận tài liệu nguồn theo từng định dạng (PDF, Word, Web, YouTube...).
- **Extractor** — chuẩn hóa nội dung thô thành cấu trúc phân cấp.
- **ContentPlanner** — component AI quyết định: chia bao nhiêu slide, slide nào nói gì, `slide_type` nào phù hợp, layout logic nào. Theo architecture doc, đây là phần được mô tả là lõi/khó nhất — nhưng đây là mô tả từ internal source, chưa phải kết luận evaluation đã kiểm chứng (xem `SYSTEM_MAP.md` và `05_decisions/OPEN_QUESTIONS.md`).
- **Deck IR** — intermediate representation tách planning khỏi export.
- **DesignSystem / Editor Loop** — áp design token, layout, chỉnh sửa của user (agentic, có state, respect lock).
- **Selection Resolver / Preview Renderer** — module thuộc giai đoạn mở rộng (sau khi vertical slice đầu tiên chạy ổn), chưa nằm trong scope P0.
- **Exporters** — xuất file cuối (PptxExporter, MarpExporter, HtmlExporter).

Chi tiết hơn: `SYSTEM_MAP.md`. Định nghĩa thuật ngữ: `TERMINOLOGY.md`.

## Vì sao repo này tồn tại

Team cần trả lời, bằng evidence chứ không phải cảm giác:

1. **Development tracking** — thay đổi vừa rồi giúp hay hại?
2. **System validation** — product có thực sự đạt FR/NFR không, không chỉ "code đã implement"?
3. **Comparative evaluation** — kiến trúc (vd. ContentPlanner, Deck IR) có thực sự đáng với độ phức tạp so với baseline đơn giản hơn hoặc competitor không?

Lý do đầy đủ: `EVALUATION_MISSION.md`.

## QUAN TRỌNG — Source docs KHÔNG phải Source of Truth

Toàn bộ tài liệu trong `01_source_docs/` (architecture, FR, NFR, verification docs) **chủ yếu được AI hỗ trợ formalize từ trao đổi ý tưởng giữa giảng viên/team**. Chúng là:

> **CURRENT INTERNAL PROPOSAL / PROJECT HYPOTHESIS**

không phải kiến trúc/metric/threshold đã chốt. Chính `00-cach-dung.pdf` (verification docs) nói rõ: bộ câu hỏi verify "không dựa trên kiến trúc đã chốt — dùng để verify hướng làm & research trước khi khóa thiết kế".

**Nhiệm vụ của research workspace này là verify, challenge, refine hoặc reject các đề xuất đó nếu external evidence cho thấy cần làm khác** — kể cả evaluation proposal ở §8 của architecture doc.

Khi viết bất kỳ research output nào, phải phân biệt rõ 4 loại thông tin — xem `AGENTS.md` / `CLAUDE.md` và `04_evidence/EVIDENCE_POLICY.md`:

- **PROJECT SOURCE** — điều nói trong `01_source_docs/`.
- **EXTERNAL EVIDENCE** — điều được paper/benchmark/repo/docs bên ngoài hỗ trợ, có citation.
- **INFERENCE** — kết luận suy ra khi kết nối evidence với Deck Agent, phải nói rõ đây là suy luận.
- **RECOMMENDATION** — đề xuất hành động, phải trace được `problem → evidence → reasoning → recommendation`.

## External research là bắt buộc

Source docs chỉ giúp hiểu **Deck Agent hiện định build cái gì và team đang nghĩ gì**. Research phải tìm thêm **external evidence** để đánh giá các assumption đó — research literature, open-source system liên quan (vd. OpenDesign, xem `03_research/external_systems/`), test/benchmark có sẵn. Không giới hạn trong 5-6 file nội bộ. Xem `04_evidence/EVIDENCE_POLICY.md` cho quy tắc chi tiết.
