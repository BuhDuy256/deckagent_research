# RQ03 — Structure & Planning Quality

**Trạng thái: Wave 2 — Blocked by RQ01 + RQ06.**

**Câu hỏi:** Structure/planning quality — phân rã slide (slide decomposition), phân bổ thông tin, và mức độ phù hợp của `slide_type` — nên đo thế nào?

## Vì sao câu hỏi này quan trọng

**PROJECT SOURCE:** Architecture doc gọi ContentPlanner là "LÕI của đồ án" và ghi chú rõ đây là phần nên tập trung nghiên cứu nhiều nhất (`01_source_docs/architecture/kien-truc-do-an.pdf`, mục 2.3). FR-02 yêu cầu: cùng một PDF nguồn, đổi `slide_type` phải tạo ra cấu trúc deck khác có chủ đích.

**INFERENCE (chưa validate):** Vì ContentPlanner được project source mô tả là component quyết định nhiều nhất, có khả năng nó xứng đáng nhận đầu tư evaluation lớn nhất. Đây vẫn là **giả thuyết dựa trên internal doc**, chưa phải kết luận đã kiểm chứng — RQ00/RQ01 cần xác nhận allocation này có hợp lý không.

**OPEN (terminology):** `slide_type` hiện được đặt ở Deck-level metadata (`teaching | catchup | speaker_support`), không phải ở từng Slide — xem `00_context/TERMINOLOGY.md` mục slide_type.

## Liên quan

- RQ00, RQ01 (taxonomy cha — "Planning Quality")
- RQ08 (baseline/ablation để tách riêng đóng góp của ContentPlanner)
- `01_source_docs/requirements/`

## Findings

_(chưa có)_
