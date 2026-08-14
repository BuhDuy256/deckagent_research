# RQ07 — Benchmark Dataset

**Trạng thái: Wave 3 — Blocked by Wave 2 (RQ02–RQ05).** Benchmark phải biết đo dimension gì trước khi thiết kế input set — xem `05_decisions/DECISION_LOG.md` D-002.

**Câu hỏi:** Frozen benchmark dataset nên có hình dạng gì — loại input, độ khó, số lượng case, schema metadata mỗi case, và cách versioning?

## Category bắt buộc phải có: adversarial (từ D-004)

Theo `05_decisions/DECISION_LOG.md` D-004: NFR-42 (prompt injection từ tài liệu nguồn) không có RQ riêng — được route thành một **benchmark category** thay vì một dimension cần judge/human đầy đủ. Khi thiết kế benchmark, RQ07 phải bao gồm nhóm case `adversarial/` bên cạnh các nhóm input thông thường, đánh giá bằng deterministic/task-success test (vd. có sanitize đúng, có leak system instruction không), không cần machinery MLLM-judge như các dimension AI-quality khác.

## Vì sao câu hỏi này quan trọng

Không có frozen benchmark thì score của các ngày/PR khác nhau không so sánh được — điều này phá vỡ mục đích #1 của evaluation (development tracking). RQ này cần chốt một dataset spec cụ thể, không chỉ nguyên tắc chung chung.

## Liên quan

- `06_design/BENCHMARK_SPEC.md` (spec cuối cùng mà RQ này feed vào)
- `03_research/benchmark/`

## Findings

_(chưa có — sketch ban đầu về candidate input category, chưa validate: academic-short, academic-long, business-report, image-heavy, table-heavy, poor-structure)_
