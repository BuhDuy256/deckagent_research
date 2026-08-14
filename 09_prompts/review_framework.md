# Prompt: Review Evaluation Framework

Dùng định kỳ (hoặc trước milestone/bảo vệ) để kiểm tra framework trong 06_design/ có nhất quán và đầy đủ, còn bám sát source docs không.

```
Bạn đang review evaluation framework của Deck Agent để kiểm tra tính nhất quán và đầy đủ.

Kiểm tra:
- Mọi pipeline stage trong 00_context/SYSTEM_MAP.md đã có evaluation target/method gán trong 06_design/EVALUATION_FRAMEWORK.md §2 chưa (hoặc có lý do rõ ràng vì sao bỏ qua)?
- Mọi metric trong 06_design/METRIC_REGISTRY.md có trace được về một quality dimension trong 00_context/TERMINOLOGY.md và một requirement trong 01_source_docs/requirements/ không?
- Có RQ nào trong 02_research_questions/ đã đánh dấu "Answered" mà chưa được đưa vào 06_design/ không?
- 06_design/BASELINE_SPEC.md và BENCHMARK_SPEC.md còn khớp với những gì 07_experiments/EXPERIMENT_REGISTRY.md thực sự đã chạy không?

Báo cáo gap dưới dạng punch list, không phải rewrite. KHÔNG sửa trực tiếp 06_design/ — ghi gap vào 05_decisions/OPEN_QUESTIONS.md để owner xử lý.
```
