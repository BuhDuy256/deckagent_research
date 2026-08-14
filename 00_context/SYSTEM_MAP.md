# Sơ đồ hệ thống (System Map)

**PROJECT SOURCE**, trích từ `01_source_docs/architecture/kien-truc-do-an.pdf`.

## Deck Agent pipeline

```
                 Tài liệu nguồn (PDF, Word, Web, YouTube...)
                          │
                          ▼
                   Input Adapters      (PdfAdapter, WordAdapter, WebAdapter, YoutubeAdapter, ...)
                          │
                          ▼
                      Extractor        ← chuẩn hóa mọi input về 1 dạng chung (text, ảnh, cấu trúc)
                          │
                          ▼
                  ContentPlanner       ← "LÕI của đồ án" (mô tả từ project source)
                          │
                          ▼
                      Deck IR          ← cấu trúc trung gian, format-agnostic
                          │
                          ▼
          DesignSystem / Editor Loop   ← agentic, có state, respect lock
                          │
                          ▼
        Selection Resolver / Preview Renderer   ← giai đoạn mở rộng, chưa thuộc scope P0
                          │
                          ▼
                     Exporters         (PptxExporter, MarpExporter, HtmlExporter)
                          │
                          ▼
                    PPTX output
```

Ghi chú từ chính architecture doc: "Điểm mấu chốt của toàn bộ sơ đồ là Deck IR nằm giữa. Mọi thứ bên trái Deck (input, extract, plan nội dung) không được biết output cuối là gì. Mọi thứ bên phải Deck (export) không được biết input gốc là gì."

## Giả thuyết phân bổ effort evaluation (working hypothesis, KHÔNG phải quyết định cuối)

**PROJECT SOURCE:** Architecture doc ghi chú tại mục 2.3 (ContentPlanner): *"Đây là phần bạn nên tập trung nghiên cứu nhiều nhất"*. Đây là gợi ý từ internal doc, chưa phải kết luận evaluation đã được audit hay external evidence xác nhận.

**Current project hypothesis (chưa validate):** Adapter/Exporter chủ yếu là engineering correctness (đọc file đúng / mở file được), không phải câu hỏi AI-quality. ContentPlanner có thể là AI-quality target quan trọng nhất vì kiến trúc hiện tại giao phần lớn quyết định nội dung cho nó. RQ00/RQ01 phải kiểm tra assumption này trước khi coi đây là allocation chính thức.

```
ContentPlanner quality       ██████████   (giả thuyết, chưa validate)
End-to-end deck quality      ████████
Design consistency           ████
Editor behaviour             ███
Exporter correctness         ██
Adapter correctness          ██
```

## Việc cần làm

- [ ] RQ00: audit toàn bộ FR/NFR để xác nhận/điều chỉnh phân bổ effort này bằng evidence thay vì suy luận từ 1 ghi chú trong architecture doc.
- [ ] Map từng pipeline stage với FR/NFR liên quan (`01_source_docs/requirements/`).
- [ ] Đánh dấu mỗi stage theo Evaluation Target / Evaluation Method trong `06_design/EVALUATION_FRAMEWORK.md` (không dùng phân loại TEST/METRIC/LLM-JUDGE/HUMAN ngang hàng — xem lý do trong file đó).
