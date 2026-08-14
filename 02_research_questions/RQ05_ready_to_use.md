# RQ05 — Ready-to-Use

**Trạng thái: Wave 2 — Blocked by RQ01 + RQ06.**

**Câu hỏi:** "Ready-to-use" — mức độ deck có thể dùng ngay với ít/không cần chỉnh sửa — nên đo thế nào?

## Candidate structure (từ audit RQ00, chưa validate) — KHÔNG merge quá sớm thành 1 score

Audit RQ00 phát hiện §8 xếp "Ready-to-use" nguyên khối vào nhóm 4 dimension cần MLLM-as-judge, nhưng FR-22 (auto-fit/chống tràn text, không chữ cắt/overlap) — phần chiếm tỷ trọng lớn của "ready-to-use" — thực ra **deterministic-checkable** (geometry overflow/overlap detection), không cần judge. Đây là **INFERENCE, candidate structure**, RQ05 phải xác nhận bằng research trước khi coi là chính thức:

```
Ready-to-use
├── Technical readiness (candidate: deterministic)
│   ├── overflow / text cắt
│   ├── broken image
│   ├── file validity
│   └── overlap thô
│
└── Presentation readiness (candidate: human / judge / hybrid)
    ├── information density
    ├── coherence
    ├── visual suitability
    └── edit effort
```

**Nguyên tắc áp dụng cho toàn framework (candidate, chờ accept chính thức sau khi RQ05 xong):** dùng evaluator rẻ và deterministic nhất có thể trước; chỉ dùng LLM/human cho phần mà rule-based không đánh giá được. Không merge Technical readiness và Presentation readiness thành 1 score duy nhất quá sớm — chúng có failure mode và chi phí đo rất khác nhau.

## Vì sao câu hỏi này quan trọng

**PROJECT SOURCE:** Mục tiêu sản phẩm (FR doc, mục 0) nói rõ: hệ thống phải "sinh deck dùng được ngay (hoặc chỉ cần chỉnh nhẹ)". Đây là dimension gần nhất với giá trị thực tế của sản phẩm.

## Liên quan

- RQ00 — hoàn tất, xem `03_research/current_evaluation_audit.md` (đặc biệt phát hiện #2 về Ready-to-use)
- RQ01, RQ06 (Wave 1 — output của cả hai unblock RQ05)
- `01_source_docs/verification/03-research-evaluation.pdf` — đề cập trực tiếp "dùng được ngay" trong mục tiêu đánh giá
- `06_design/EVALUATION_FRAMEWORK.md` — nơi nguyên tắc "rẻ nhất trước" sẽ được chính thức hóa sau khi accept

## Findings

_(chưa có)_
