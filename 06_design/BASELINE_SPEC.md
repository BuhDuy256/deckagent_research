# Baseline Spec

Trạng thái: **chưa viết**. Trả lời RQ08. Định nghĩa baseline nào được xây và so sánh công bằng thế nào.

## Loại baseline cần specify

| Loại | Ví dụ | Trả lời câu hỏi |
| --- | --- | --- |
| A. Historical | v0.5 vs v0.6 | Có đang cải thiện không? |
| B. Naive / simplified — **"Single-shot planning baseline"** | `source → one LLM call → Deck IR → same exporter` | **Tách riêng planning stage có đáng không?** |
| C. Ablation | Deck Agent không có ContentPlanner | Component này đóng góp gì? |
| D. External system | Deck Agent vs. Open Design / PPTAgent / Gamma | Ta ở đâu trên bản đồ? (contextual — xem D-013) |

Bốn family này là **bốn câu hỏi khác nhau, không thay thế nhau** (D-012).

### ⚠️ B-1 KHÔNG chứng minh giá trị của Deck IR (D-011 gate G-11, `REJECTED_APPROACHES.md` R-002)

B-1 **vẫn dùng Deck IR ở cả hai nhánh** — nó so `source → ContentPlanner → Deck IR` với `source → one LLM call → Deck IR`. Vì vậy:

- ✅ Được phép claim: "tách riêng planning stage có đáng không"
- ❌ **KHÔNG** được claim: "Deck IR có đáng không" / "kiến trúc ContentPlanner + Deck IR có đáng không"

Muốn claim về Deck IR cần một experiment khác (IR-first pipeline vs. direct generation pipeline), khó làm fair hơn đáng kể — chưa thiết kế.

### 🚧 CHẶN IMPLEMENTATION: A1 và B-1 đang trùng nhau (Q-014)

Nếu A1 (`without ContentPlanner`) cũng là `Extractor output → ONE prompt → Deck IR` thì nó **chính là** B-1. Nguy cơ: chạy hai experiment giống hệt nhau rồi báo cáo như hai evidence độc lập.

Phải chọn **trước khi implement**: merge chúng, HOẶC định nghĩa A1 khác (vd. A1 = full pipeline nhưng bypass explicit plan/outline artifact; B-1 = one-shot hoàn toàn). Xem `05_decisions/OPEN_QUESTIONS.md` Q-014.

## Cần specify cho từng baseline

- Cấu hình chính xác (cái gì giữ cố định, cái gì thay đổi)
- Chạy trên benchmark case nào (`BENCHMARK_SPEC.md`)
- Metric nào áp dụng (`METRIC_REGISTRY.md`)
- Kết quả lưu ở đâu: `08_results/baselines/`, `08_results/ablations/`

## Lưu ý

Không mặc định gọi external system là "competitor" — có thể chỉ là related system, architecture reference, evaluation inspiration, hoặc benchmark inspiration. Xem `03_research/external_systems/README.md`.
