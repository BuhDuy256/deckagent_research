# RQ08 — Baselines

**Trạng thái: READY (Wave 1).** Trả lời trục AGAINST WHAT trong bộ 3 câu hỏi nền tảng (RQ01=WHAT, RQ06=HOW, RQ08=AGAINST WHAT) — xem `05_decisions/DECISION_LOG.md` D-002.

**Câu hỏi:** Baseline nào đáng để xây/so sánh — previous version, simplified baseline, ablation, external competitor — và loại nào thực sự trả lời được câu hỏi team cần?

## Vì sao câu hỏi này quan trọng

"Baseline" hay bị mặc định hiểu là "competitor bên ngoài", nhưng đó lại là loại khó làm fair nhất (input/output/control condition khác nhau) và chưa chắc hữu ích nhất. Previous-version và ablation baseline rẻ hơn để làm fair và trả lời câu hỏi sắc hơn ("có đang cải thiện không", "ContentPlanner có xứng đáng với độ phức tạp không"). RQ này ưu tiên baseline nào đáng đầu tư trước.

**Candidate external system cần research (chưa kết luận có phải baseline hay không):** OpenDesign — xem `03_research/external_systems/`. Lưu ý: NFR doc (`01_source_docs/requirements/requirements-non-functional.pdf`) đã nhắc tới Open Design khi so sánh mô hình trả phí token — đây là PROJECT SOURCE gợi ý, chưa phải external evidence đã verify.

## Liên quan

- `06_design/BASELINE_SPEC.md` (spec cuối cùng mà RQ này feed vào)
- `03_research/baselines/`
- `03_research/external_systems/` (research về hệ thống liên quan, không mặc định gọi là "competitor")

## Findings

_(chưa có — candidate baseline type: previous version vs. current, single-shot LLM như simplified baseline, Deck Agent without ContentPlanner như ablation, external system nếu so sánh công bằng khả thi)_
