# Baseline Spec

Trạng thái: **chưa viết**. Trả lời RQ08. Định nghĩa baseline nào được xây và so sánh công bằng thế nào.

## Loại baseline cần specify

| Loại | Ví dụ | Trả lời câu hỏi |
| --- | --- | --- |
| Previous version | v0.5 vs v0.6 | Có đang cải thiện không? |
| Simplified baseline | Single-shot LLM | Độ phức tạp của kiến trúc có đáng không? |
| Ablation | Deck Agent không có ContentPlanner | Component này đóng góp gì? |
| External system | Deck Agent vs. OpenDesign / Gamma / Canva / ... | So sánh với bên ngoài thế nào? (ưu tiên thấp nhất — khó đảm bảo fairness về input/output/control condition, xem `03_research/external_systems/`) |

## Cần specify cho từng baseline

- Cấu hình chính xác (cái gì giữ cố định, cái gì thay đổi)
- Chạy trên benchmark case nào (`BENCHMARK_SPEC.md`)
- Metric nào áp dụng (`METRIC_REGISTRY.md`)
- Kết quả lưu ở đâu: `08_results/baselines/`, `08_results/ablations/`

## Lưu ý

Không mặc định gọi external system là "competitor" — có thể chỉ là related system, architecture reference, evaluation inspiration, hoặc benchmark inspiration. Xem `03_research/external_systems/README.md`.
