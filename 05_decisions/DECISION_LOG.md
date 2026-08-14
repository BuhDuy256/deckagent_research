# Decision Log

Ghi lại (append-only) các decision **đã thực sự được chốt** (bởi team/giảng viên, không chỉ được đề xuất) trong dự án này. Mỗi entry phải giúp người đọc sau này hiểu *vì sao* mà không cần đọc lại toàn bộ cuộc thảo luận.

## Format

```
## D-001 — <tên ngắn>
Ngày: YYYY-MM-DD
Decision: <đã quyết định gì>
Vì sao: <lý do / evidence dẫn tới quyết định>
Phương án khác đã xét: <nếu có — link REJECTED_APPROACHES.md nếu chi tiết>
Ảnh hưởng: <RQ / design doc / component nào bị ảnh hưởng>
```

---

## D-001 — RQ00 accepted; giữ nguyên RQ01–RQ08

Ngày: 2026-08-14
Decision: Accept output của RQ00 (`03_research/current_evaluation_audit.md`). Không merge/split/xóa bất kỳ RQ nào trong RQ01–RQ08 — audit không tìm thấy lý do để làm vậy.
Vì sao: User review trực tiếp bảng audit + đề xuất routing từ RQ00, xác nhận 8 RQ hiện tại đều hợp lý về phạm vi.
Ảnh hưởng: `RQ_INDEX.md`, `RESEARCH_STATUS.md`

## D-002 — Research theo Wave, không theo thứ tự số RQ01→RQ08

Ngày: 2026-08-14
Decision: Research theo 3 wave thay vì tuần tự RQ01→RQ02→...→RQ08:
- **Wave 1 (song song, unblock ngay):** RQ01 (dimensions — trả lời WHAT), RQ06 (judge/evaluator landscape — trả lời HOW), RQ08 (baseline landscape — trả lời AGAINST WHAT)
- **Wave 2 (sau Wave 1):** RQ02, RQ03, RQ04, RQ05 — cần output của RQ01 (dimension nào cần đo) và RQ06 (method nào khả dụng) để bớt mơ hồ
- **Wave 3 (sau Wave 2):** RQ07 — benchmark design cần biết trước dimension nào phải đo (từ Wave 2)
Vì sao: RQ01/RQ06/RQ08 là 3 trục nền tảng (WHAT/HOW/AGAINST WHAT) mà RQ02–RQ05 phụ thuộc vào; RQ07 (benchmark) cần biết đo gì trước khi thiết kế input set.
Ảnh hưởng: `RQ_INDEX.md`, `RESEARCH_STATUS.md`, status của từng RQ0x

## D-003 — Câu hỏi "ngưỡng đo được/định tính chấp nhận cho đồ án" (Q-006) là design policy, không phải RQ

Ngày: 2026-08-14
Decision: Không tạo RQ09 cho câu hỏi này. Thay vào đó, thêm section "Evaluation Rigor Policy" vào `06_design/EVALUATION_FRAMEWORK.md` (bảng Minimum / Better / Overkill cho từng quality), nội dung được điền dần trong Wave 2.
Vì sao: Đây là quyết định về mức độ rigor/scope, không phải một technical research question kiểu "phương pháp nào đúng" — không cần literature review riêng, chỉ cần một khung phân loại evidence tối thiểu/tốt hơn/dư thừa.
Ảnh hưởng: `06_design/EVALUATION_FRAMEWORK.md`, RQ01 (không nhồi câu hỏi rigor vào RQ01)

## D-004 — Adversarial/prompt-injection (NFR-42, Q-008) route vào benchmark category, không tạo RQ riêng

Ngày: 2026-08-14
Decision: Không tạo RQ riêng cho adversarial robustness ở giai đoạn này. Route thành một benchmark category (`benchmark/adversarial/`) trong `06_design/BENCHMARK_SPEC.md`, đánh giá bằng deterministic/task-success test thay vì judge/human machinery đầy đủ.
Vì sao: Tạo RQ riêng cho scope này dễ làm phình research scope ra ngoài mission chính (đo improvement, so sánh version/baseline). NFR-42 vẫn là yêu cầu evaluation hợp lệ nhưng độ ưu tiên thấp hơn 4 dimension AI-quality chính.
Revisit nếu: Giảng viên/team coi security/prompt-injection là contribution quan trọng của đồ án — khi đó cần RQ riêng.
Ảnh hưởng: `06_design/BENCHMARK_SPEC.md`, `02_research_questions/RQ07_benchmark.md`

## D-005 — Thêm Operational Evaluation làm layer chính thức trong Evaluation Framework

Ngày: 2026-08-14
Decision: Accept Q-009. Restructure `06_design/EVALUATION_FRAMEWORK.md` thành 3 layer: Quality Evaluation, Correctness/Functional Evaluation, Operational Evaluation (Latency, Cost, Reliability).
Vì sao: §8 (architecture doc) chỉ nói AI-quality, bỏ sót hoàn toàn latency/cost/reliability dù NFR-01-03/20-22/90-92 yêu cầu rõ và mission cần dữ liệu này để trả lời "improvement quality có đánh đổi latency/cost/reliability không". Không cần external research nhiều — đo lường operational khá chuẩn.
Ảnh hưởng: `06_design/EVALUATION_FRAMEWORK.md`

## D-006 — Development Evaluation Protocol (Q-010) là design workstream, lên lịch sau Wave 1

Ngày: 2026-08-14
Decision: Thêm workstream "Development Evaluation Protocol" — phân tier metric nào chạy mỗi commit / PR / nightly / milestone (Tier 0–3). Không phải RQ literature-heavy; là design task, lên lịch sau khi Wave 1 (RQ01, RQ06) và RQ07 (benchmark) có output.
Vì sao: Mission đặc thù là chứng minh cải thiện "mỗi ngày" — một metric tốt cho final report chưa chắc usable hằng ngày (vd. human evaluation là evidence tốt cho thesis nhưng quá chậm/đắt cho mỗi commit). Audit hiện chỉ đánh dấu Dev Harness? ✓/trống, chưa biến thành protocol/tiering thật.
Ảnh hưởng: `06_design/EVALUATION_PIPELINE.md` (nơi protocol này cuối cùng sẽ nằm)
