# Baseline Spec

Trạng thái: **WORKING DRAFT — RQ08 research complete; implementation/final benchmark mapping pending.** File này định nghĩa baseline nào được xây và so sánh công bằng thế nào.

## Loại baseline cần specify

| Loại | Ví dụ | Trả lời câu hỏi |
| --- | --- | --- |
| A. Historical | v0.5 vs v0.6 | Có đang cải thiện không? |
| B. Naive / simplified — **"Single-shot planning baseline"** | `source → one LLM call → Deck IR → same exporter` | **Tách riêng planning stage có đáng không?** |
| C. Ablation | Candidate: full ContentPlanner nhưng bỏ explicit plan/outline artifact | Artifact rõ ràng này đóng góp gì vượt phần planner còn lại? |
| D. External system | Deck Agent vs. Open Design / PPTAgent / Gamma | Ta ở đâu trên bản đồ? (contextual — xem D-013) |

Bốn family này là **bốn câu hỏi khác nhau, không thay thế nhau** (D-012).

### ⚠️ B-1 KHÔNG chứng minh giá trị của Deck IR (Wave 1 Gate G-11 / `REJECTED_APPROACHES.md` R-002; làm rõ tiếp bởi D-018)

B-1 **vẫn dùng Deck IR ở cả hai nhánh** — nó so `source → ContentPlanner → Deck IR` với `source → one LLM call → Deck IR`. Vì vậy:

- ✅ Được phép claim: "tách riêng planning stage có đáng không"
- ❌ **KHÔNG** được claim: "Deck IR có đáng không" / "kiến trúc ContentPlanner + Deck IR có đáng không"

Muốn claim về Deck IR cần một experiment khác (IR-first pipeline vs. direct generation pipeline), khó làm fair hơn đáng kể — chưa thiết kế.

### ✅ Q-014 đã resolve: merge intervention trùng, ablation riêng phải hỏi câu khác (D-018)

Nếu A1 (`without ContentPlanner`) cũng là `Extractor output → ONE prompt → Deck IR`, A1 **được merge vào B-1** dưới tên **Single-shot planning baseline**. Chỉ implement/run/report một experiment; không gọi hai tên rồi dùng như hai evidence độc lập.

**Câu hỏi B-1 / A1 merged trả lời:**

> Việc tách một planning stage rõ ràng có cải thiện end-to-end output so với một LLM call trực tiếp từ Extractor output sang Deck IR không?

Nếu team cần một ablation riêng, intervention phải khác rõ ràng. Candidate hợp lệ:

```text
full ContentPlanner, nhưng bỏ explicit plan/outline artifact
```

**Câu hỏi candidate ablation riêng trả lời:**

> Trong một full ContentPlanner, explicit plan/outline artifact có đóng góp vượt phần planner còn lại không?

Candidate này **chưa phải experiment bắt buộc implement**. Trước implementation phải specify chính xác phần nào bị bỏ, phần nào giữ nguyên và bảo đảm nó không collapse trở lại B-1. Xem D-018 và Q-014 resolved.

## Cần specify cho từng baseline

- Cấu hình chính xác (cái gì giữ cố định, cái gì thay đổi)
- Chạy trên benchmark case nào (`BENCHMARK_SPEC.md`)
- Metric nào áp dụng (`METRIC_REGISTRY.md`)
- Kết quả lưu ở đâu: `08_results/baselines/`, `08_results/ablations/`

## Lưu ý

Không mặc định gọi external system là "competitor" — có thể chỉ là related system, architecture reference, evaluation inspiration, hoặc benchmark inspiration. Xem `03_research/external_systems/README.md`.
