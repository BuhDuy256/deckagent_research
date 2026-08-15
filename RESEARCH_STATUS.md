# Research Status

Mở file này để biết ngay "đang ở đâu".

## Wave 2A — RESEARCH COMPLETE, HUMAN REVIEW APPLIED, PILOT NOT RUN

| RQ | Chủ đề | Trạng thái | Output |
| --- | --- | --- | --- |
| [RQ02](02_research_questions/RQ02_content_fidelity.md) | Source quality — phân biệt **bịa / sai / bỏ sót** | **ANSWERED — chờ review** | `03_research/RQ02/` — landscape 462 / evidence 552 / recommendation 253 dòng |
| [RQ03](02_research_questions/RQ03_structure_planning.md) | Structure / `slide_type` differentiation | **ANSWERED — chờ review** | `03_research/RQ03/` — landscape 425 / evidence 312 / recommendation 255 dòng |

### Kết quả nổi bật cần chú ý khi review

- **HOLD G-05 (F2) đã đảo chiều khuyến nghị của Wave 1.** RQ02 báo AlignScore là lựa chọn **kém nhất** trong nhóm (không phải candidate); SummaC-Conv nhạy với sửa vô hại hơn sửa đúng. MiniCheck chỉ còn candidate grounding/`correctly_supported_fact` auxiliary, vẫn HOLD; reverse MiniCheck không phải presence detector theo D-015.
- **RQ02 tìm ra lỗ hổng logic trong NFR-53:** checklist per-instance là danh sách **đóng** các thứ *phải có*, còn hallucination là tập **mở** các thứ *không được có* → checklist **không bắt được bịa**, dù §8 đặt "không bịa" làm mục tiêu.
- **RQ03 từ chối xác nhận bảng minh họa** teaching/catchup/speaker_support, có 3 lý do kèm evidence. Đáng chú ý: **X+Slides (E1) mâu thuẫn trực tiếp với FR-20** — FR-20 nói khác biệt ở "chữ/ảnh/mật độ", X+Slides kết luận audience adaptation chủ yếu đổi **information selection**.
- **RQ03 trả lời thẳng cảnh báo judge yếu:** khuyến nghị **không xây "Structure score" của §8 làm metric theo dõi**. Nhưng FR-02 không hỏi câu đó — nó hỏi về *khác biệt*, giải được bằng F1 + thống kê (manipulation check, three-armed paired design, permutation test, **không có model trong vòng lặp** ở phép kiểm lõi).
- **⚠️ Va chạm RQ02 ↔ RQ03 cần điều phối:** "background vắng mặt" là *coverage gap* dưới frame nguồn nhưng *intentional omission* dưới frame brief. Nếu RQ02 đo coverage **không điều kiện hóa theo `slide_type`**, nó sẽ **phạt `catchup` vì đã làm đúng**.
- **G-03 stress-test có kết quả:** RQ03 tìm được 3 chỗ taxonomy 5 lớp gãy — thiếu **contrastive reference frame** (so với output anh em), mà lớp đó chứa cả regression tracking lẫn ablation, tức trung tâm mission. RQ03 đề xuất **giữ HOLD**.
- **⚠️ Rủi ro verify lớn nhất (P-11):** hai nguồn quan trọng nhất của RQ03 (X+Slides, Learning to Present) chỉ đọc qua tool summary HTML, **chưa đọc PDF gốc**. Mọi con số phải verify lại trước khi vào báo cáo.

### Đã áp dụng từ human review ngày 2026-08-15

- Promote D-015…D-020: coverage presence tách correctness; Option C là working pilot policy; semantic/core ưu tiên purpose + information selection; resolve Q-014; đổi Pilot A1–A4 thành SQ-P1–SQ-P4; tách B0 smoke khỏi B1 confirmatory và giữ threshold ở mức candidate.
- Q-014 đã resolve qua D-018: ablation A1 giống B-1 thì merge; ablation riêng chỉ tồn tại nếu có intervention/câu hỏi khác rõ ràng.
- `speaker_notes` giữ FR-55 = P3 và không phải core feature của `speaker_support` trong pilot hiện tại.
- Chưa chạy Pilot SQ, B0 hoặc B1. MiniCheck vẫn **HOLD**, không phải ACCEPT.
- **Wave 2B (RQ04, RQ05) và RQ07 vẫn chưa mở.**

### Consistency review trước pilot

- Đã tách conceptual fact record thành `fact_identity/topic_key` (presence), `gold_claim` (correctness/grounding), và `mode_expectation` (Option C obligation).
- SQ-P2 chỉ test reverse MiniCheck cho candidate `correctly_supported_fact`; contradicted và absent đều có thể `NOT_SUPPORTED_BY_DECK`, nên output này không được dùng làm presence coverage.
- Presence detector tự động hiện **OPEN/HOLD**. SQ-P1 chưa sẵn sàng prepare/run cho tới khi team chọn component presence và freeze oracle contract.
- B0 không có `purpose_alignment` fuzzy score. Purpose chỉ được diễn giải từ obligation/observable pre-registered; definition/obligation hiện chưa freeze.
- Không thay đổi meaning D-015…D-020; đây là traceability/authority correction.

---

## Artifact Wave 2A decision/pilot đã tạo

| Artifact | Trạng thái | Vai trò |
| --- | --- | --- |
| `03_research/WAVE2A_DECISION_GATE.md` | **HUMAN REVIEW APPLIED — còn HOLD item** | 17 candidate W2A-01…W2A-17; ghi rõ phần đã promote và blocker còn lại |
| `07_experiments/pilot/PILOT_PLAN_WAVE2A.md` | **DESIGN ONLY — BLOCKED trước SQ-P1/B0** | Presence method OPEN/HOLD; SQ-P2 là correct-support auxiliary; B0 purpose obligations chưa freeze |
| `06_design/drafts/SLIDE_TYPE_BEHAVIOR_SPEC.md` | **CORE PRINCIPLE ACCEPTED — definition cụ thể còn draft** | Purpose/information selection là CORE; surface feature là SECONDARY; sign/definition mode chờ team review |
| `03_research/cross_rq/SOURCE_COVERAGE_VS_SLIDE_TYPE.md` | **OPTION C SELECTED CHO PILOT** | `universal_core` / `mode_required` / `optional/not_expected`; metric riêng, không aggregate |

## 👉 VIỆC TIẾP THEO — HUMAN/TEAM BLOCKERS, rồi DỪNG trước pilot

1. Chọn/review presence component cho `fact_identity/topic_key` và freeze manual oracle contract; không dùng reverse MiniCheck/gold-claim entailment làm proxy.
2. Chuyển SQ fixture thành fact record tách identity/gold claim, rồi review/freeze expected behavior + runtime/prerequisite. MiniCheck vẫn HOLD.
3. Team review/freeze definition cụ thể của ba mode và operationalize purpose thành obligation/observable; không dùng fuzzy score.
4. Author/review `fact_identity/topic_key`, `gold_claim`, và Option C `mode_expectation` **trước khi** xem output.
5. Chọn/freeze source set nhỏ, `R`, language stratum và invalid/retry/exclusion rule cho B0.
6. Chỉ sau khi B0 cho signal đáng theo và human review đồng ý mới thiết kế/freeze B1.

**Sequence hiện tại:** `resolve presence method/contract → prepare SQ-P1 → SQ-P1 → SQ-P2 → SQ-P3 → SQ-P4 → human review SQ → freeze mode obligations + separated Option C fact map → B0 → human review B0 → [nếu đáng theo] design/freeze B1 → B1`.

**Không mở RQ04/RQ05/RQ07 và không chạy pilot trong handoff này.**

## Đã xong: Wave 1 + Decision Gate

| RQ | Chủ đề | Trạng thái | Output |
| --- | --- | --- | --- |
| [RQ00](02_research_questions/RQ00_current_evaluation_audit.md) | Audit nội bộ | ACCEPTED | `03_research/current_evaluation_audit.md` |
| [RQ01](02_research_questions/RQ01_evaluation_dimensions.md) | Dimensions (WHAT) | ANSWERED, qua gate | `03_research/RQ01/` |
| [RQ06](02_research_questions/RQ06_llm_judge.md) | Evaluator (HOW) | ANSWERED, qua gate | `03_research/RQ06/` |
| [RQ08](02_research_questions/RQ08_baselines.md) | Baselines (AGAINST WHAT) | ANSWERED, qua gate | `03_research/RQ08/` |

**Decision Gate: `03_research/WAVE1_SYNTHESIS.md`** — 8 ACCEPT (→ D-007..D-014), 3 HOLD (→ Q-011..Q-013), 2 REJECT (→ R-001, R-002). Q-014 trước đây chặn implementation, nay đã resolve qua D-018.

## Chờ

| RQ | Chủ đề | Trạng thái |
| --- | --- | --- |
| [RQ04](02_research_questions/RQ04_design_quality.md) | Design (Conformance/Geometry/Perceptual) | Wave 2B — chờ Wave 2A human gate |
| [RQ05](02_research_questions/RQ05_ready_to_use.md) | Ready-to-use (Technical/Editability/User effort) | Wave 2B — chờ Wave 2A human gate |
| [RQ07](02_research_questions/RQ07_benchmark.md) | Benchmark design | Wave 3 — chờ Wave 2 |

## Việc chặn implementation — cần giải quyết

- **Q-014 đã RESOLVED → D-018:** B-1/A1 trùng nhau được merge; nếu team muốn ablation riêng thì phải freeze intervention khác, candidate là full planner nhưng bỏ explicit plan/outline artifact.
- **Pilot SQ:** presence method chưa được chọn; fact identity/gold claim oracle, fixture/runtime và expected-case behavior chưa freeze. MiniCheck vẫn HOLD và chỉ là correct-support auxiliary.
- **B0:** purpose obligations, separated Option C fact record/map, source set, repeat và invalid/retry/exclusion policy chưa freeze.
- **B1:** chưa được phép design/freeze cho tới khi B0 cho signal đáng theo; statistical design hiện tại chỉ là candidate và không chặn B0.

## Đã freeze, gửi team implementation

- **D-014 Run Manifest** (`06_design/EVALUATION_PIPELINE.md` §Run Manifest) — lưu từ bây giờ, không chờ Wave 2. Đặc biệt `plan_outline_artifact` phải persist.

## Lộ trình

```
WAVE 1 (xong) → WAVE 2A research (xong) → HUMAN REVIEW (đã áp dụng một phần)
                                                    ↓
                                 resolve presence method + freeze separated SQ fixtures
                                                    ↓
                                    SQ-P1 → SQ-P2 → SQ-P3 → SQ-P4
                                                    ↓ human review
                              freeze mode obligations + separated Option C map
                                                    ↓
                                              B0 smoke
                                                    ↓ human review: signal đáng theo?
                                      no → HOLD        yes → design/freeze B1
                                                                     ↓
                                                               B1 confirmatory
                                                                     ↓
                                                          quyết định research tiếp theo
```

## Legend

`READY` · `IN PROGRESS` · `BLOCKED` · `ANSWERED` (chờ review) · `ACCEPTED` (đã qua gate)
