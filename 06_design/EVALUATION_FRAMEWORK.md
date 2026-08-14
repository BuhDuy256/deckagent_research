# Evaluation Framework

Trạng thái: **chưa viết chính thức, khung đang được dựng dần.** RQ00 đã **ACCEPTED** (`05_decisions/DECISION_LOG.md` D-001 → D-006), Wave 1 (RQ01/RQ06/RQ08) đang chạy. Các section có structural decision đã accept (§0, §2's layer split, §Evaluation Rigor Policy skeleton) được viết ra ở đây theo đúng D-003/D-005 — nhưng **nội dung cụ thể của từng target/metric vẫn TBD, chờ Wave 2/3**. Không coi nội dung TBD là final.

## 0. Ba layer đánh giá (accepted — D-005)

§8 của architecture doc gốc chỉ nói về AI-quality, bỏ sót hoàn toàn operational concern dù NFR yêu cầu rõ. Framework này tách 3 layer:

```
Evaluation Framework
├── Quality Evaluation           — AI-quality, cần judge/human cho phần không deterministic được
│   (Content, Planning, Presentation, Usability — xem 00_context/TERMINOLOGY.md)
│
├── Correctness / Functional Evaluation  — deterministic, engineering correctness
│   (schema validity, file mở được, state invariants, cross-format consistency...)
│
└── Operational Evaluation       — đo bằng telemetry, không cần judge
    ├── Latency
    ├── Cost
    └── Reliability (failure rate, retry rate)
```

Mục đích tách: layer Quality mới thực sự cần đầu tư nghiên cứu (Wave 2). Correctness/Functional phần lớn test được ngay bằng CI. Operational đo chuẩn bằng telemetry, không cần external research nhiều — nhưng vẫn phải hiện diện chính thức vì mission cần trả lời "cải thiện quality có đánh đổi latency/cost/reliability không" (`00_context/EVALUATION_MISSION.md`).

## 1. Evaluation Goals

- Development tracking
- System validation
- External/baseline comparison

## 2. Evaluation Targets × Evaluation Methods

**Không dùng classification ngang hàng kiểu `TEST | METRIC | LLM-JUDGE | HUMAN`** — các category này không cùng abstraction level (test cũng sinh ra metric, human eval cũng sinh ra metric, LLM-judge cũng sinh ra metric, deterministic check cũng là một loại metric). "Metric" là **output measurement**, không phải một evaluator category song song với Human/LLM Judge.

Thay vào đó, tách 2 trục riêng, gắn với layer ở §0:

**Trục 1 — Evaluation Target:** đang đo cái gì? (component / quality dimension, vd. source fidelity, planner structure, design consistency, latency...)

**Trục 2 — Evaluation Method:** đo bằng cách nào?
- Deterministic check
- Automatic semantic evaluator
- LLM/MLLM judge
- Human evaluator
- Telemetry
- Hybrid
- TBD

| Layer | Target | Eval method | Dev Harness? | Thesis Evidence? | Priority |
| --- | --- | --- | :---: | :---: | --- |
| Correctness/Functional | Deck IR validity | Deterministic | ✓ | | Low |
| Quality | Source fidelity | TBD (candidate: hybrid — xem RQ02, NFR-53) | ✓ | ✓ | High |
| Quality | Planner structure (slide decomposition, slide_type appropriateness) | TBD (candidate: human/LLM judge — xem RQ03) | ✓ | ✓ | High |
| Quality (Technical readiness) | Overflow / layout | Deterministic | ✓ | ✓ | Medium |
| Quality | Design token consistency | Deterministic (diff) + TBD (perceptual — xem RQ04, NFR-52) | ✓ | ✓ | Medium |
| Operational | Latency | Telemetry | ✓ | ✓ | Medium |
| Operational | Cost/deck | Telemetry | ✓ | ✓ | Medium |
| Operational | Reliability (failure/retry rate) | Telemetry | ✓ | ✓ | Medium |
| Quality (Presentation readiness) | Ready-to-use / edit effort | TBD (candidate: human/judge — xem RQ05) | | ✓ | High |
| Correctness/Functional | Editor state invariants (lock respected, slide isolation) | Deterministic (state diff) | ✓ | ✓ | Medium |
| Quality | Editor instruction-following (NL command → đúng ý định) | TBD | ✓ | ✓ | Medium |

Bảng này populate dần trong Wave 2/3 (RQ02–RQ07); nguồn đầy đủ hơn ở `03_research/current_evaluation_audit.md`. Không tự sửa TBD thành giá trị cụ thể ngoài lúc research đã accepted.

### Development vs. Final evaluation — luôn xét riêng

Mọi metric phải cân nhắc theo 2 use case khác nhau, và ghi rõ nó phù hợp use case nào:

- **Development metric (Dev Harness)** — ưu tiên: rẻ, nhanh, lặp lại được, tự động hóa được, đủ nhạy để phát hiện regression.
- **Final evaluation (Thesis Evidence)** — ưu tiên: rigorous, defensible, có human validation mạnh hơn, so sánh baseline, reproducible, phù hợp để đưa vào báo cáo/bảo vệ. **Lưu ý:** ✓ ở cột này không nghĩa là trọng số ngang nhau trong final comparison — xem §Evaluation Rigor Policy.

Một metric có thể phù hợp cả hai, hoặc chỉ một — phải ghi rõ trong `06_design/METRIC_REGISTRY.md`.

### Cadence — khi nào chạy (design workstream riêng, D-006)

Metric nào chạy mỗi commit/PR/nightly/milestone là một câu hỏi thiết kế riêng ("Development Evaluation Protocol"), thuộc `06_design/EVALUATION_PIPELINE.md`, lên lịch sau Wave 1 + RQ07. Không lẫn vào bảng target × method ở trên.

## Evaluation Rigor Policy (skeleton — routed từ Q-006, D-003)

Trả lời: với mỗi quality, mức evidence tối thiểu chấp nhận được cho phạm vi đồ án là gì, mức tốt hơn là gì, mức nào là overkill không cần thiết. **Nội dung bảng dưới là ví dụ minh họa cấu trúc, chưa phải quyết định — điền thật trong Wave 2 khi từng RQ có câu trả lời.**

| Quality | Minimum viable evidence | Better | Overkill (không cần cho phạm vi đồ án) |
| --- | --- | --- | --- |
| Overflow/layout | Deterministic test trên bộ mẫu nhỏ | Deterministic test trên frozen benchmark đầy đủ | Human rating cho từng trường hợp |
| Content fidelity | LLM judge + spot-check human vài mẫu | Human correlation trên mẫu con đã tính cỡ mẫu (NFR-53) | Full human evaluation trên toàn bộ benchmark |
| Latency | P50/P95 trên vài chục run | P50/P95 + confidence interval | Queuing/load-test theory đầy đủ |

## 3. Quality Dimensions

Cái gì đáng đo (chưa phải công thức — xem `METRIC_REGISTRY.md`):

- Fidelity, Coverage, Structure, Slide-type appropriateness, Readability, Visual consistency, Ready-to-use, Reliability, Latency, Cost

**Candidate structure cho Ready-to-use (chưa accept, chờ RQ05 — xem `02_research_questions/RQ05_ready_to_use.md`):** tách "Technical readiness" (deterministic — overflow, broken image, file validity) khỏi "Presentation readiness" (human/judge — density, coherence, visual suitability, edit effort). Nguyên tắc chung nên áp dụng toàn framework: **dùng evaluator rẻ/deterministic nhất có thể trước; chỉ dùng LLM/human cho phần rule-based không đánh giá được.**

## 4. Comparison Strategy

- current vs. previous
- current vs. simplified baseline
- full vs. ablation
- current vs. external system (nếu khả thi)

Xem `BASELINE_SPEC.md`.

## 5. Benchmark Strategy

- Input types, độ khó, số lượng test case, frozen benchmark, run metadata, versioning
- `adversarial/` category bắt buộc có (D-004) — xem `BENCHMARK_SPEC.md`

Xem `BENCHMARK_SPEC.md`.

---

## Bước tiếp theo

Wave 2 (RQ02–RQ05) điền các dòng TBD trong §2 và §Evaluation Rigor Policy. Wave 3 (RQ07) điền §5. Không tự sửa TBD thành giá trị cụ thể ngoài lúc research đã accepted — xem quy tắc "research không tự sửa final design" trong `AGENTS.md` / `CLAUDE.md`.
