# Evaluation Pipeline

Trạng thái: **chưa viết**. Đây là thiết kế kỹ thuật của harness — deliverable thật sự, không chỉ là tài liệu.

## Hình dạng conceptual (từ scoping ban đầu)

```
                 BENCHMARK DATASET
                       │
                       ▼
             ┌──────────────────┐
             │ Evaluation Runner│
             └────────┬─────────┘
                      │
          ┌───────────┴────────────┐
          ▼                        ▼
   Deck Agent v1             Deck Agent v2
          │                        │
          └──────────┬─────────────┘
                     ▼
               generated runs
                     │
       ┌─────────────┼──────────────┐
       ▼             ▼              ▼
 deterministic    LLM Judge       Human
   metrics          metrics       sample
       │             │              │
       └─────────────┴──────────────┘
                     ▼
                 RESULTS
                     │
                     ▼
            Comparison / Report
```

CLI shape conceptual:

```bash
evaluate \
  --benchmark benchmark-v1 \
  --system deck-agent-v0.4 \
  --baseline deck-agent-v0.3
```

## Cần specify

- Runner implementation: ngôn ngữ/framework, cách nó invoke các version Deck Agent
- Cách orchestrate và merge deterministic metric, LLM-judge call, và human-sample collection
- Report format (xem bảng ví dụ trong `00_context/EVALUATION_MISSION.md`)
- Nơi lưu output của run: `08_results/`
- Cách đăng ký experiment: `07_experiments/EXPERIMENT_REGISTRY.md`

## Phụ thuộc

Cần `EVALUATION_FRAMEWORK.md` §2 (Evaluation Targets × Methods) và `METRIC_REGISTRY.md` được điền trước khi có thể thiết kế interface thu thập metric của runner.
