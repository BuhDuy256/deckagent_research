# Research Status

Mở file này để biết ngay "đang ở đâu".

## Wave 2A — XONG, chờ human review

| RQ | Chủ đề | Trạng thái | Output |
| --- | --- | --- | --- |
| [RQ02](02_research_questions/RQ02_content_fidelity.md) | Source quality — phân biệt **bịa / sai / bỏ sót** | **ANSWERED — chờ review** | `03_research/RQ02/` — landscape 462 / evidence 552 / recommendation 253 dòng |
| [RQ03](02_research_questions/RQ03_structure_planning.md) | Structure / `slide_type` differentiation | **ANSWERED — chờ review** | `03_research/RQ03/` — landscape 425 / evidence 312 / recommendation 255 dòng |

### Kết quả nổi bật cần chú ý khi review

- **HOLD G-05 (F2) đã đảo chiều khuyến nghị của Wave 1.** RQ02 báo AlignScore là lựa chọn **kém nhất** trong nhóm (không phải candidate); SummaC-Conv nhạy với sửa vô hại hơn sửa đúng. Chỉ MiniCheck đi tiếp. Nếu Wave 1 được promote nguyên xi, team đã implement đúng thứ tệ nhất.
- **RQ02 tìm ra lỗ hổng logic trong NFR-53:** checklist per-instance là danh sách **đóng** các thứ *phải có*, còn hallucination là tập **mở** các thứ *không được có* → checklist **không bắt được bịa**, dù §8 đặt "không bịa" làm mục tiêu.
- **RQ03 từ chối xác nhận bảng minh họa** teaching/catchup/speaker_support, có 3 lý do kèm evidence. Đáng chú ý: **X+Slides (E1) mâu thuẫn trực tiếp với FR-20** — FR-20 nói khác biệt ở "chữ/ảnh/mật độ", X+Slides kết luận audience adaptation chủ yếu đổi **information selection**.
- **RQ03 trả lời thẳng cảnh báo judge yếu:** khuyến nghị **không xây "Structure score" của §8 làm metric theo dõi**. Nhưng FR-02 không hỏi câu đó — nó hỏi về *khác biệt*, giải được bằng F1 + thống kê (manipulation check, three-armed paired design, permutation test, **không có model trong vòng lặp** ở phép kiểm lõi).
- **⚠️ Va chạm RQ02 ↔ RQ03 cần điều phối:** "background vắng mặt" là *coverage gap* dưới frame nguồn nhưng *intentional omission* dưới frame brief. Nếu RQ02 đo coverage **không điều kiện hóa theo `slide_type`**, nó sẽ **phạt `catchup` vì đã làm đúng**.
- **G-03 stress-test có kết quả:** RQ03 tìm được 3 chỗ taxonomy 5 lớp gãy — thiếu **contrastive reference frame** (so với output anh em), mà lớp đó chứa cả regression tracking lẫn ablation, tức trung tâm mission. RQ03 đề xuất **giữ HOLD**.
- **⚠️ Rủi ro verify lớn nhất (P-11):** hai nguồn quan trọng nhất của RQ03 (X+Slides, Learning to Present) chỉ đọc qua tool summary HTML, **chưa đọc PDF gốc**. Mọi con số phải verify lại trước khi vào báo cáo.

### Chưa làm — cố ý

- Chưa consolidate ~20 proposed decision (RQ02 P-01…P-10, RQ03 gồm P-06/P-11) vào `OPEN_QUESTIONS.md`.
- Chưa tạo Wave 2A decision gate. Theo quy trình: user đọc `recommendation.md` trước → rồi mới gate.
- **Wave 2B (RQ04, RQ05) vẫn BLOCKED.**

## Đã xong: Wave 1 + Decision Gate

| RQ | Chủ đề | Trạng thái | Output |
| --- | --- | --- | --- |
| [RQ00](02_research_questions/RQ00_current_evaluation_audit.md) | Audit nội bộ | ACCEPTED | `03_research/current_evaluation_audit.md` |
| [RQ01](02_research_questions/RQ01_evaluation_dimensions.md) | Dimensions (WHAT) | ANSWERED, qua gate | `03_research/RQ01/` |
| [RQ06](02_research_questions/RQ06_llm_judge.md) | Evaluator (HOW) | ANSWERED, qua gate | `03_research/RQ06/` |
| [RQ08](02_research_questions/RQ08_baselines.md) | Baselines (AGAINST WHAT) | ANSWERED, qua gate | `03_research/RQ08/` |

**Decision Gate: `03_research/WAVE1_SYNTHESIS.md`** — 8 ACCEPT (→ D-007..D-014), 3 HOLD (→ Q-011..Q-013), 2 REJECT (→ R-001, R-002), 1 mục chặn implementation (Q-014).

## Chờ

| RQ | Chủ đề | Trạng thái |
| --- | --- | --- |
| [RQ04](02_research_questions/RQ04_design_quality.md) | Design (Conformance/Geometry/Perceptual) | Wave 2B — chờ Wave 2A |
| [RQ05](02_research_questions/RQ05_ready_to_use.md) | Ready-to-use (Technical/Editability/User effort) | Wave 2B — chờ Wave 2A |
| [RQ07](02_research_questions/RQ07_benchmark.md) | Benchmark design | Wave 3 — chờ Wave 2 |

## Việc chặn implementation — cần giải quyết

- **Q-014: A1 và B-1 đang trùng nhau.** Phải merge hoặc định nghĩa lại A1 **trước khi chạy experiment**, nếu không sẽ có hai experiment giống hệt nhau được báo cáo như hai evidence độc lập.

## Đã freeze, gửi team implementation

- **D-014 Run Manifest** (`06_design/EVALUATION_PIPELINE.md` §Run Manifest) — lưu từ bây giờ, không chờ Wave 2. Đặc biệt `plan_outline_artifact` phải persist.

## Lộ trình

```
WAVE 1 (xong) → [Decision Gate] (xong) → WAVE 2A (đang chạy) → WAVE 2B → RQ07 Benchmark
                                                                              ↓
                                                                    PILOT EVALUATION
                                                          (metric có hợp lý? judge noise?
                                                           human agreement? cost? failure cases?)
                                                                              ↓
                                                                   FREEZE Evaluation v1
                                                                              ↓
                                                                       BUILD HARNESS
                                                                              ↓
                                                          change → benchmark → compare
                                                          → failure analysis → keep/revert
```

## Legend

`READY` · `IN PROGRESS` · `BLOCKED` · `ANSWERED` (chờ review) · `ACCEPTED` (đã qua gate)
