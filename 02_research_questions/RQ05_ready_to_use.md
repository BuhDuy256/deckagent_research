# RQ05 — Ready-to-Use

**Trạng thái: Wave 2B — Blocked by Wave 2A (RQ02 + RQ03).**

## Câu hỏi — tách 3 tầng, không gộp thành một score

```
READY-TO-USE
├── Technical readiness   → PPTX mở được? overflow? image broken?
│                           candidate: DETERMINISTIC
│
├── Editability           → file có sửa được không? cấu trúc OOXML có sạch không?
│                           (shape có phải text box thật hay ảnh phẳng?
│                            có group lồng nhau vô lý không?)
│                           candidate: SEMI-DETERMINISTIC
│
└── Actual user effort    → người dùng thật phải sửa bao nhiêu?
                            candidate: HUMAN / task-based
```

**Không merge 3 tầng này thành một score quá sớm** — chúng có failure mode và chi phí đo rất khác nhau.

Tầng **Editability** là tầng dễ bị bỏ quên nhất nhưng liên quan trực tiếp tới mục tiêu sản phẩm: một deck "mở được và nhìn ổn" nhưng mọi slide là ảnh phẳng thì **không** ready-to-use theo nghĩa FR mong muốn.

## Vì sao câu hỏi này quan trọng

**PROJECT SOURCE:** Mục tiêu sản phẩm (FR doc, mục 0) nói rõ: "sinh deck **dùng được ngay** (hoặc chỉ cần chỉnh nhẹ)". FR-22 yêu cầu auto-fit/chống tràn, không chữ cắt/overlap thô. UX-01 nhắc lại "1 nguồn/prompt → deck trình bày được hoặc chỉ sửa nhẹ".

Đây là dimension gần nhất với giá trị thực tế của sản phẩm — và cũng là chỗ RQ00 phát hiện §8 có thể đang gộp một phần deterministic-checkable (FR-22 overflow) vào nhóm cần MLLM-judge.

## Nguyên tắc áp dụng (từ Wave 1, D-007)

**Dùng evaluator rẻ và deterministic nhất có thể trước; chỉ dùng LLM/human cho phần rule-based không đánh giá được.** RQ05 là ứng viên rõ nhất để áp dụng nguyên tắc này, vì phần lớn "technical readiness" đo được bằng geometry/OOXML inspection.

## Chỗ khó nhất — cảnh báo từ Wave 1

RQ01 báo **không tìm được bất kỳ metric external nào lượng hoá "edit effort"** — đây là chỗ yếu nhất của cả RQ01. Nghĩa là tầng "Actual user effort" nhiều khả năng phải tự thiết kế protocol (task-based study), không có precedent để mượn. Cần xác nhận lại và, nếu đúng, thiết kế protocol tối giản khả thi trong phạm vi đồ án.

## Ràng buộc từ Decision Gate

- **G-01/D-007:** tách deterministic khỏi judged.
- **G-07/Q-013:** **không chốt cỡ mẫu human**. Nếu đề xuất task-based study, ghi `TBD` kèm candidate scale và giải thích phụ thuộc gì.
- **G-09/D-011:** evaluator model-based nào được đề xuất phải kèm cách đo noise floor.

## Liên quan

- `03_research/WAVE1_SYNTHESIS.md` — G-01, G-07, G-09
- `03_research/RQ01/recommendation.md` — L5 Outcome/Usability, và negative finding về edit effort
- RQ04 (Wave 2B, chạy cùng — ranh giới RQ04 Geometry ↔ RQ05 Technical readiness cần thống nhất, tránh trùng)
- `01_source_docs/verification/03-research-evaluation.pdf` — "dùng được ngay" trong mục tiêu đánh giá

## Findings

_(chưa có)_
