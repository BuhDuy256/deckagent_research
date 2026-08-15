# Chỉ mục Research Question

> **Wave 1 đã qua Decision Gate** (`03_research/WAVE1_SYNTHESIS.md`, 2026-08-14). Không phải mọi recommendation của Wave 1 đều được accept — 3 mục HOLD, 2 mục REJECT, 1 mục chặn implementation. Wave 2 **phải đọc Decision Gate trước**, không được coi output Wave 1 là truth.

## Wave 1 — nền tảng (ĐÃ XONG, đã qua gate)

| ID | Câu hỏi | Trạng thái |
| --- | --- | --- |
| [RQ00](RQ00_current_evaluation_audit.md) | Audit evaluation proposal nội bộ | ACCEPTED |
| [RQ01](RQ01_evaluation_dimensions.md) | Evaluation dimensions (**WHAT**) | ANSWERED — taxonomy 5 lớp đang HOLD (Q-011) |
| [RQ06](RQ06_llm_judge.md) | Judge/evaluator landscape (**HOW**) | ANSWERED — F2 đang HOLD (Q-012), cỡ mẫu human HOLD (Q-013) |
| [RQ08](RQ08_baselines.md) | Baseline landscape (**AGAINST WHAT**) | ANSWERED — B-1 claim bị thu hẹp (R-002), A1/B-1 trùng nhau (Q-014) |

## Wave 2A — core quality (ĐANG CHẠY)

Ưu tiên trước vì FR đặt ContentPlanner, bám nguồn, `slide_type` và plan ở core flow.

| ID | Câu hỏi | Trạng thái |
| --- | --- | --- |
| [RQ02](RQ02_content_fidelity.md) | Phép đo nào phân biệt được **bịa / sai / bỏ sót**, và cái nào đủ rẻ chạy hằng ngày? | IN PROGRESS |
| [RQ03](RQ03_structure_planning.md) | Đổi `slide_type` thì output thay đổi ở **thuộc tính quan sát được** nào, đo bằng gì? | IN PROGRESS |

## Wave 2B — sau Wave 2A

Nhiều phần đã biết sẽ rơi xuống deterministic check, nên độ bất định thấp hơn Wave 2A.

| ID | Câu hỏi | Trạng thái |
| --- | --- | --- |
| [RQ04](RQ04_design_quality.md) | Ranh giới Conformance / Geometry / Perceptual nằm ở đâu? | Blocked — chờ Wave 2A |
| [RQ05](RQ05_ready_to_use.md) | Technical readiness / Editability / Actual user effort đo thế nào? | Blocked — chờ Wave 2A |

## Wave 3 — benchmark

| ID | Câu hỏi | Trạng thái |
| --- | --- | --- |
| [RQ07](RQ07_benchmark.md) | Frozen benchmark dataset nên có hình dạng gì? | Blocked — chờ Wave 2 |

**Vì sao RQ07 ở cuối:** metric quyết định benchmark phải chứa dữ liệu gì. Nếu RQ02 chọn critical-fact coverage → mỗi case phải lưu `critical_facts`; nếu RQ03 cần test `slide_type` → mỗi source phải chạy cả 3 mode; nếu RQ05 cần edit effort → benchmark phải có protocol cho user sửa deck. Thiết kế benchmark trước metric rất dễ phải làm lại.

## Không còn là RQ riêng

Xem `05_decisions/DECISION_LOG.md`: rigor threshold (D-003) · adversarial robustness (D-004) · development evaluation cadence (D-006) đã route thành design workstream trong `06_design/`.

## Chú giải

`READY` · `IN PROGRESS` · `Blocked` · `ANSWERED` (có proposal, chờ review) · `ACCEPTED` (đã qua gate, promote vào `06_design/`)

Xem `RESEARCH_STATUS.md` ở root cho bảng gộp.
