# Benchmark Spec

Trạng thái: **chưa viết**. Trả lời RQ07. Định nghĩa tập input cố định (frozen) mà mọi version Deck Agent chạy qua để so sánh.

## Cần specify

- Loại input / category (candidate từ scoping, chưa validate: academic-short, academic-long, business-report, image-heavy, table-heavy, poor-structure)
- **`adversarial/` — bắt buộc có** (theo `05_decisions/DECISION_LOG.md` D-004): case test prompt-injection/nội dung độc hại trong tài liệu nguồn (NFR-42). Đánh giá bằng deterministic/task-success test (có sanitize đúng, có leak system instruction không), không cần MLLM-judge machinery như các dimension AI-quality khác.
- Độ khó (difficulty spread) trong mỗi category
- Số lượng case mỗi category
- Schema metadata mỗi case, vd:

```yaml
id: pdf_014
source: ...
slide_type: teaching
constraints:
  target_slides: 10
evaluation:
  expected_topics:
    - ...
  critical_facts:
    - ...
```

- Chính sách freeze/versioning: benchmark tự nó thay đổi theo thời gian thế nào mà không phá vỡ khả năng so sánh lịch sử
- Nơi lưu benchmark thật: `03_research/benchmark/`

## Phụ thuộc

- Cần review `01_source_docs/` để xác nhận các category input có khớp với những gì sản phẩm thực sự cần xử lý không.
