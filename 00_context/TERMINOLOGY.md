# Thuật ngữ (Terminology)

## Domain Deck Agent

- **Deck Agent** — sản phẩm đang được evaluate: hệ thống AI sinh deck PPTX từ tài liệu nguồn.
- **Adapter** — bước pipeline nhận input theo từng định dạng nguồn (PdfAdapter, WordAdapter, WebAdapter, YoutubeAdapter, ...).
- **Extractor** — bước pipeline chuẩn hóa nội dung thô từ adapter thành cấu trúc có phân cấp (heading, đoạn văn, ảnh + caption, bảng...). Đây là bước "hiểu tài liệu", chưa "quyết định slide".
- **ContentPlanner** — component AI quyết định số lượng slide, phân bổ nội dung mỗi slide, và `slide_type`. **PROJECT SOURCE:** architecture doc gọi đây là "LÕI của đồ án" và ghi chú nên tập trung nghiên cứu nhiều nhất ở đây — đây là mô tả từ internal doc, chưa phải kết luận đã kiểm chứng rằng nó xứng đáng nhận ưu tiên evaluation cao nhất (xem `05_decisions/OPEN_QUESTIONS.md`).
- **Deck IR** — intermediate representation, tách planning (ContentPlanner) khỏi rendering/export. Theo architecture doc: "format-agnostic", không biết gì về PPTX/Marp/HTML.
- **slide_type** — **[OPEN — xem ghi chú bên dưới]** phân loại chế độ trình bày của deck.
- **Design token** — giá trị design tái sử dụng (màu, font, spacing...) cần được giữ nguyên khi regenerate.
- **Ready-to-use** — mức độ deck sinh ra có thể dùng với ít/không cần chỉnh sửa thủ công.

### OPEN — `slide_type`

**PROJECT SOURCE:** Theo `01_source_docs/architecture/kien-truc-do-an.pdf` và `requirements-functional.pdf` (FR-02), `slide_type` hiện nhận giá trị `teaching | catchup | speaker_support` và được đặt ở **Deck-level metadata** (`Deck.meta.slide_type`), không phải ở từng Slide riêng lẻ.

Naming hiện có thể gây nhầm — tên gợi ý "loại của một slide" nhưng thực chất là "chế độ trình bày của cả deck". Đây là **open terminology/design question**, không phải việc evaluation workspace phải giải quyết hay tự ý rename schema — chỉ cần agent research hiểu đúng để không nhầm lẫn khi viết RQ/metric liên quan đến "slide_type appropriateness".

## Domain evaluation

- **FR / NFR** — Functional / Non-Functional Requirements, định nghĩa trong `01_source_docs/requirements/`.
- **Development evaluation** — evaluation chạy trong lúc dev để phát hiện regression/improvement giữa các version.
- **System validation** — evaluation chứng minh product thực sự đạt FR/NFR, là evidence chứ không chỉ "code đã implement".
- **Comparative evaluation** — evaluation so với baseline, ablation, hoặc external system, dùng để justify architectural choice.
- **Baseline** — một điểm so sánh. Không giới hạn ở external competitor: previous version, simplified baseline (vd. single-shot LLM), và ablation (vd. Deck Agent không có ContentPlanner) đều là baseline.
- **MLLM-as-judge** — dùng multimodal LLM để chấm điểm deck theo rubric, thường validate reliability bằng một sample human-annotated.
- **Frozen benchmark** — tập input cố định, có version, dùng để mọi version Deck Agent chạy qua và so sánh được theo thời gian.

## Taxonomy chất lượng (working hypothesis — CHƯA phải quyết định cuối)

```
QUALITY
├── Content Quality         — factual/source fidelity, coverage, hallucination
├── Planning Quality         — slide decomposition, information allocation, slide_type appropriateness
├── Presentation Quality      — readability, content density, layout appropriateness, visual coherence
└── Usability / Ready-to-use  — edit effort, overall presentation readiness
```

**Working Hypothesis / Candidate — chưa thay thế 4 dimension trong architecture proposal (§8: content fidelity / ready-to-use / design consistency / structure).** RQ01 (sau khi RQ00 hoàn tất) phải đánh giá bằng evidence xem việc restructure này có justified hay không, trước khi coi taxonomy trên là chính thức. Xem `05_decisions/OPEN_QUESTIONS.md` Q-001.
