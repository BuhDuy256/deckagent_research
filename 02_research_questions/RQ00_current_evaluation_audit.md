# RQ00 — Audit toàn bộ evaluation proposal hiện tại

**Đây là task research đầu tiên, phải làm trước RQ01–RQ08.** RQ00 không dùng external literature để quyết định metric hoặc validate technical approach — phần đó thuộc RQ01–RQ08. Tuy nhiên, RQ00 phải **đánh dấu requirement/claim nào cần external validation ở các RQ tiếp theo**, biến audit thành một **research routing map**, không chỉ một bảng liệt kê nội bộ.

> Trả lời câu hỏi: Theo toàn bộ architecture + FR + NFR + verification docs hiện tại, Deck Agent thực sự có những gì cần được đo, và phần nào trong đó cần external research để trả lời đúng?

## Vì sao cần bước này trước

RQ01–RQ08 (xem `RQ_INDEX.md`) được suy ra từ một cuộc trao đổi scoping (`00_context/EVALUATION_MISSION.md`), chưa phải từ một audit có hệ thống trên toàn bộ source docs. Cần audit trước để biết RQ nào thực sự cần, RQ nào thiếu, RQ nào có thể merge/split/bỏ.

## Nguồn phải đọc (toàn bộ, không chỉ trang đầu)

- `01_source_docs/architecture/kien-truc-do-an.pdf` — kiến trúc, đặc biệt §8 (evaluation proposal hiện tại)
- `01_source_docs/requirements/requirements-functional.pdf` — toàn bộ FR (FR-01 trở đi)
- `01_source_docs/requirements/requirements-non-functional.pdf` — toàn bộ NFR
- `01_source_docs/verification/00-cach-dung.pdf`, `01-verify-workflow.pdf` — cách team verify hướng đi
- `01_source_docs/verification/03-research-evaluation.pdf` — task brief gốc cho chính vai trò evaluation research này, đọc kỹ trước tiên

### Lưu ý về authority scope của `03-research-evaluation.pdf`

File này có **authority mạnh về task scope / expected deliverable** — nó định nghĩa "done" nghĩa là gì cho chính công việc research evaluation này. Nó **KHÔNG có authority về technical validity** của các metric/method được đề xuất — những proposal kỹ thuật bên trong nó (vd. dùng model-as-judge, cỡ mẫu human) vẫn phải được external research validate ở RQ01–RQ08, không được coi là đúng chỉ vì nó nằm trong task brief. Xem thêm `01_source_docs/SOURCE_INDEX.md`.

## Câu hỏi cần trả lời, cho từng FR/NFR liên quan

1. Product/system đang claim điều gì?
2. Cần evidence gì để chứng minh claim đó đúng?
3. **Evaluation Nature** — bản chất của vấn đề đang được đánh giá là gì (KHÔNG phải đo bằng cách nào):
   - Deterministic correctness
   - Operational measurement
   - AI quality
   - Not an evaluation concern
   - TBD
4. **Candidate Evaluation Method** — cách đo khả dĩ (trục riêng, độc lập với Nature):
   - Automated deterministic check
   - Telemetry
   - Automatic semantic evaluator
   - Human / LLM judge candidate
   - Hybrid
   - TBD
5. Hữu ích cho mục đích nào: development tracking / system validation / comparative evaluation?
6. Kiến trúc hiện tại (§8) đã cover claim này chưa?
7. Nếu chưa, gap là gì?
8. **Needs external research?** — YES/NO. YES nếu cần literature/benchmark/external system để xác định method hoặc validate approach (điển hình: mọi thứ có Evaluation Nature = "AI quality"). NO nếu đã rõ ràng là deterministic/operational và không có ambiguity về cách đo (vd. schema validation, latency wall-clock).

**Chưa chọn metric cụ thể ở bước này** — cột "Candidate Evaluation Method" chỉ là hướng khả dĩ, không phải formula.

## Output bắt buộc

Tạo file: `03_research/current_evaluation_audit.md`

Với bảng ma trận:

| Requirement | Product/System Claim | Evidence cần có | Evaluation Nature | Candidate Evaluation Method | Dev Tracking? | Final Eval? | §8 hiện cover? | Gap | Needs External Research? |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

Sau bảng, đề xuất (dưới dạng proposal, không tự quyết): RQ01–RQ08 nên giữ, merge, split, remove, hay bổ sung thêm RQ nào — nhưng **không tự cập nhật `RQ_INDEX.md`**, chỉ đề xuất trong `05_decisions/OPEN_QUESTIONS.md` để review. Cũng **không tự cập nhật `06_design/` hay `05_decisions/DECISION_LOG.md`, không tự unblock RQ01–RQ08**.

## Trạng thái

**ACCEPTED** (2026-08-14, xem `05_decisions/DECISION_LOG.md` D-001 → D-006). RQ01/RQ06/RQ08 đã unblock (Wave 1). RQ02–RQ05 blocked by Wave 1, RQ07 blocked by Wave 2 — xem `RQ_INDEX.md` và `RESEARCH_STATUS.md`.

## Findings

Output đầy đủ: `03_research/current_evaluation_audit.md` (đọc toàn bộ 17 trang của 6 source doc, bảng audit theo Evaluation Nature × Candidate Evaluation Method × Needs External Research, cross-check với checklist trong `03-research-evaluation.pdf`).

Tóm tắt nhanh:
- RQ01–RQ08 hiện tại: audit không tìm thấy lý do merge/split/xóa — đề xuất giữ nguyên.
- Phát hiện 1 UNVERIFIED ENTITY ưu tiên cao: "PresentBench", được §8 trích dẫn làm hướng phương pháp nhưng chưa verify tồn tại — xem Q-005.
- Phát hiện gap chưa có RQ cover: ngưỡng đo được/định tính chấp nhận cho đồ án (Q-006), adversarial robustness NFR-42 (Q-008).
- NFR-52/NFR-53 đã có candidate method cụ thể sẵn trong nguồn (không phải research từ đầu) — RQ02/RQ04/RQ06 nên bắt đầu từ đó.
- Latency/cost/reliability hoàn toàn vắng mặt trong §8 dù cần cho mission — đề xuất đưa vào `EVALUATION_FRAMEWORK.md` như task thiết kế, không cần RQ riêng (Q-009).

Toàn bộ đề xuất nằm ở `05_decisions/OPEN_QUESTIONS.md` (Q-005 → Q-009), chờ user/team quyết định trước khi áp dụng vào `RQ_INDEX.md` hay `06_design/`.
