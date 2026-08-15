# RQ04 — Design Quality

**Trạng thái: Wave 2B — Blocked by Wave 2A (RQ02 + RQ03).**

## Câu hỏi — KHÔNG research "design score" chung

Tách thành 3 nhóm ngay từ đầu, vì chúng có bản chất và chi phí đo hoàn toàn khác nhau (D-007):

```
DESIGN
├── Conformance          → token đúng? font đúng? color đúng?
│                          candidate: DETERMINISTIC
│
├── Geometry             → overlap? overflow? element ra ngoài slide?
│                          candidate: DETERMINISTIC
│
└── Perceptual design    → layout có hợp với content? hierarchy? balance?
                           candidate: MLLM / human
```

Câu hỏi của RQ04 là: **ranh giới giữa 3 nhóm này nằm ở đâu, và phần perceptual còn lại có đáng đo bằng judge/human không** — sau khi đã đẩy tối đa xuống deterministic.

## Vì sao đặt vấn đề theo hướng này

**PROJECT SOURCE:** FR-10–13 (design token engine, lưu mẫu, lock, lan truyền atomic), NFR-11 (atomic propagate), NFR-52 (đo design consistency — nguồn tự đề xuất `% element dùng đúng token` HOẶC checklist MLLM-judge).

**Từ Wave 1:** RQ01 báo AeSlides làm overflow/overlap **100% programmatic**, và SlideAudit đo được LLM detect design flaw chỉ F1 0.331–0.655 — tức judge không chỉ đắt hơn mà **kém chính xác hơn** ở nhóm Geometry. Ngược lại PPTEval báo human-correlation ở Design ~0.90 (cao nhất trong các dimension) — tức nhóm Perceptual có thể là chỗ judge làm tốt nhất.

Hai dữ kiện này cùng nói: ranh giới Conformance/Geometry ↔ Perceptual chính là ranh giới deterministic ↔ judge. RQ04 phải xác nhận và định vị chính xác ranh giới đó.

## Ràng buộc từ Decision Gate

- **G-01/D-007:** tách deterministic khỏi judged, không gộp thành một "design score".
- **G-09/D-011:** evaluator model-based nào được đề xuất phải kèm cách đo noise floor.
- **NFR-52 là internal candidate đưa vào so sánh**, không phải preferred method.

## Liên quan

- `03_research/WAVE1_SYNTHESIS.md` — G-01, G-09
- `03_research/RQ06/` — evaluator family landscape
- RQ05 (Wave 2B, chạy cùng — ranh giới RQ04 Geometry ↔ RQ05 Technical readiness cần thống nhất, tránh trùng)

## Findings

_(chưa có)_
