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

## Run Manifest — ĐÃ CHỐT, freeze từ 2026-08-14 (D-014)

Đây là phần **duy nhất** của file này đã được accept và gửi team implementation. Nó không phụ thuộc kết quả research Wave 2 — chưa biết final metric nhưng đã chắc chắn evaluation cần reproducibility.

**Lý do:** run comparison trở nên vô nghĩa nếu model/prompt/benchmark thay đổi ngầm. RQ08 dẫn Atil 2024: dao động ~15% giữa các run được cho là "deterministic". Không có manifest thì không phân biệt được "code tốt lên" với "model/prompt đã đổi".

Mỗi run phải lưu tối thiểu:

```
run_id
commit_hash

benchmark_case_id
source_document_id

model_id
model_version (nếu provider có expose)

planner_prompt_version
judge_prompt_version

temperature
top_p
seed (nếu có)

deck_ir_schema_version

latency
token_usage
estimated_cost

generated_deck_ir      # artifact, không chỉ path
plan_outline_artifact  # BẮT BUỘC persist — xem ghi chú
final_pptx_path

timestamp
```

**Vì sao `plan_outline_artifact` bắt buộc persist:** không phải vì plan là metric cuối cùng (nó không phải — xem D-009: ContentPlanner được chứng minh qua end-to-end + ablation, không qua điểm số component). Lý do là khi output hỏng, cần reconstruct được **"planner đã quyết định gì"** để chẩn đoán. FR-07 cũng yêu cầu có plan/outline artifact kiểm tra được trước exporter, và FR-03 yêu cầu `source_ref` trace nội dung về nguồn.

---

## Cần specify

- Runner implementation: ngôn ngữ/framework, cách nó invoke các version Deck Agent
- Cách orchestrate và merge deterministic metric, LLM-judge call, và human-sample collection
- Report format (xem bảng ví dụ trong `00_context/EVALUATION_MISSION.md`)
- Nơi lưu output của run: `08_results/`
- Cách đăng ký experiment: `07_experiments/EXPERIMENT_REGISTRY.md`

## Phụ thuộc

Cần `EVALUATION_FRAMEWORK.md` §2 (Evaluation Targets × Methods) và `METRIC_REGISTRY.md` được điền trước khi có thể thiết kế interface thu thập metric của runner.
