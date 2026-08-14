# Deck Agent — Evaluation Research

Repo này thiết kế và xây dựng evaluation framework cho **Deck Agent**, hệ thống AI biến tài liệu nguồn thành deck trình bày (PPTX). Nó phải trả lời được các câu hỏi xuyên suốt đồ án:

1. Deck Agent hôm nay có tốt hơn phiên bản hôm qua không?
2. Một thay đổi (prompt/model/architecture/component) tạo ra improvement hay regression?
3. Deck Agent hiện tốt/xấu ở dimension nào?
4. Các architectural decision quan trọng có thực sự mang lại value không?
5. Deck Agent tốt hơn/kém hơn baseline hoặc alternative system ở đâu?
6. Improvement về quality có đánh đổi latency, cost, hay reliability không?

## Cấu trúc

| Thư mục | Mục đích |
| --- | --- |
| `00_context/` | Vì sao dự án tồn tại, sơ đồ hệ thống, thuật ngữ |
| `01_source_docs/` | PDF nguồn (architecture, requirements, verification) từ team Deck Agent — **không phải Source of Truth**, xem `SOURCE_INDEX.md` |
| `02_research_questions/` | Research question đang mở, dẫn dắt việc thiết kế framework — bắt đầu từ RQ00 |
| `03_research/` | Tài liệu research theo từng chủ đề (metrics, benchmark, baselines, judges, human eval, external systems) |
| `04_evidence/` | Paper, sản phẩm liên quan, note thu thập làm evidence — có `EVIDENCE_POLICY.md` quy định mức tin cậy |
| `05_decisions/` | Decision log, open questions, rejected approaches |
| `06_design/` | Spec chính thức: evaluation framework, metric registry, benchmark/baseline spec, pipeline design |
| `07_experiments/` | Đăng ký experiment và các run pilot/final |
| `08_results/` | Dữ liệu kết quả: development tracking, baseline, ablation, final run |
| `09_prompts/` | Prompt tái sử dụng cho các research task trong project này |

## Nguyên tắc quan trọng nhất

**Source docs trong `01_source_docs/` không phải Source of Truth** — chúng là internal proposal cần được research workspace này verify/challenge. Xem `00_context/PROJECT_CONTEXT.md` và `AGENTS.md` / `CLAUDE.md` trước khi bắt đầu bất kỳ research task nào.

**Đang ở đâu ngay bây giờ:** xem `RESEARCH_STATUS.md`. Task đầu tiên là RQ00 (audit toàn bộ evaluation proposal hiện tại), chưa phải research metric cụ thể.
