# Chỉ mục tài liệu nguồn (Source Document Index)

> **Các tài liệu trong `01_source_docs/` KHÔNG phải Source of Truth.** Đây là các internal AI-assisted project document — được hình thành từ trao đổi ý tưởng giữa giảng viên/team và sau đó được AI hỗ trợ formalize thành văn bản. Chúng thể hiện **current internal proposal / project hypothesis** tại thời điểm viết, không phải kết luận đã được external evidence xác nhận. Nhiệm vụ của research workspace này là verify, challenge, refine hoặc reject chúng khi cần — xem `00_context/PROJECT_CONTEXT.md`.

Đã xác nhận tồn tại (2026-08-14): tất cả 6 file dưới đây đều có mặt trên đĩa, đọc trực tiếp 1-2 trang đầu mỗi file để xác nhận nội dung khớp tên file. Không có nội dung nào bị copy/rewrite ra chỗ khác — chỉ đọc tại chỗ khi cần trích dẫn.

| File | Vị trí | Nội dung | Trạng thái |
| --- | --- | --- | --- |
| `kien-truc-do-an.pdf` | `architecture/` | "Kiến trúc tổng thể - AI Slide Deck Agent". Pipeline: Input Adapters → Extractor → ContentPlanner ("LÕI của đồ án") → Deck IR → DesignSystem/Editor Loop → Selection Resolver → Preview Renderer → Exporters. Có giải thích "tại sao" cho từng phần. | Đã có — Internal proposal |
| `requirements-functional.pdf` | `requirements/` | Functional Requirements, ưu tiên theo P0 (vertical slice + demo thầy) / P1 / P2 / P3. FR-01 → FR-14+ (nhập liệu, ContentPlanner, source trace, export, schema Deck IR, error feedback, plan/outline). | Đã có — Internal proposal |
| `requirements-non-functional.pdf` | `requirements/` | Non-functional Requirements: mô hình trả phí token (team trả / BYOK / hybrid — có so sánh với **Open Design**), agent-native vs. app độc lập, latency/performance (NFR-01..03), consistency của design token (NFR-10..12). | Đã có — Internal proposal |
| `00-cach-dung.pdf` | `verification/` | "Definition of Done - Verify bằng câu hỏi". Giải thích cách dùng bộ câu hỏi verify: **không dựa trên kiến trúc đã chốt**, dùng để verify hướng làm & research **trước khi khóa thiết kế**. | Đã có — Internal proposal (meta/process doc) |
| `01-verify-workflow.pdf` | `verification/` | "Verify workflow - Ai cũng làm". Checklist: luồng end-to-end, ranh giới trách nhiệm giữa các bước, có tôn trọng "dùng nguồn đã cung cấp" và "dùng được ngay / chỉ sửa nhẹ" không. | Đã có — Internal proposal |
| `03-research-evaluation.pdf` | `verification/` | **"Research evaluation - Bảo Duy"** — đây chính là task brief / Definition of Done cho vai trò evaluation research (vai trò của workspace này). Có 3 mục: Mục tiêu đánh giá, Phương pháp (human/model-as-judge/metric tự động, bias, checklist vs rubric, cỡ mẫu human), Thực thi & báo cáo. | Đã có — Internal proposal (**task brief cho chính research này**) |

## Ghi chú

- Không tìm thấy file "02-*" trong `verification/` — có thể là task brief của thành viên khác trong team, không thuộc phạm vi của workspace này. Không suy đoán nội dung file này.
- `03-research-evaluation.pdf` nên được đọc kỹ trước khi bắt đầu RQ00 — nó định nghĩa "done" cho chính công việc research evaluation, khác với `EVALUATION_MISSION.md` (vốn là bản diễn giải/mở rộng qua trao đổi, không phải bản gốc).
- Khi một source doc thay đổi (team cập nhật), ghi lại tác động vào `05_decisions/DECISION_LOG.md` nếu nó ảnh hưởng một decision đã chốt.

## Authority scope của `03-research-evaluation.pdf`

Cần phân biệt rõ hai loại authority, không được gộp chung:

- **Strong internal authority cho task scope / expected deliverable** — file này định nghĩa đúng "bạn (Bảo Duy) được giao làm gì" và "done nghĩa là gì" cho vai trò evaluation research. Ở khía cạnh này nó gần như là Source of Truth cho chính assignment.
- **KHÔNG có authority về technical validity của metric/method đề xuất bên trong nó** — ví dụ nó đặt câu hỏi "dùng người, model-as-judge, metric tự động, hay kết hợp?" nhưng **không tự trả lời đúng** câu hỏi đó. Đây vẫn là câu hỏi cần external evidence (RQ06), không phải điều đã được xác nhận chỉ vì nó xuất hiện trong task brief.

Nói ngắn gọn: file này là SoT về **"phải làm gì"**, không phải SoT về **"cái gì đúng về mặt kỹ thuật/khoa học"**. Cùng nguyên tắc này áp dụng cho mọi file khác trong `01_source_docs/` — xem `00_context/PROJECT_CONTEXT.md`.
