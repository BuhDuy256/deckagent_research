# RQ02 — Source Quality (fidelity / correctness / coverage)

**Trạng thái: READY (Wave 2A).** Unblock sau Wave 1 Decision Gate (`03_research/WAVE1_SYNTHESIS.md`, 2026-08-14).

## Câu hỏi (scope hẹp — KHÔNG phải "research content fidelity metrics")

> **Deck Agent cần những phép đo nào để phân biệt ba failure khác nhau — bịa thông tin, nói sai thông tin, và bỏ sót thông tin quan trọng — sao cho ít nhất một signal đủ rẻ và ổn định để chạy thường xuyên?**

Hai vế đều bắt buộc: (a) **phân biệt được 3 failure**, (b) **ít nhất một signal đủ rẻ/ổn định cho daily development**. Một phương pháp chính xác nhưng chỉ chạy được ở milestone thì không giải quyết được mục tiêu "chứng minh cải thiện mỗi ngày".

## Vì sao tách 3 failure (G-02, ACCEPTED)

Một score gộp không cho biết hỏng ở đâu. Ví dụ source có 10 ý quan trọng:

- **Deck A:** nói 5 ý, cả 5 đều đúng, không bịa
- **Deck B:** nói đủ 10 ý, 2 ý sai

Cả hai có thể cùng ra `Content Fidelity = 7/10` nhưng là hai loại lỗi hoàn toàn khác nhau. Trong development, kết quả `hallucination ↓ nhưng coverage ↓` **không** cho phép kết luận "version mới tốt hơn".

**PROJECT SOURCE:** FR-03 yêu cầu block trace được về vùng nguồn (`source_ref`) và không bịa số liệu/ý chính. NFR-53 đề xuất checklist theo instance + human correlation trên mẫu con — đây là **internal candidate đưa vào so sánh, không phải preferred method** (xem `WAVE1_SYNTHESIS.md` G-06).

## Phải so sánh ít nhất các approach

- NLI / alignment-based (vd. SummaC, AlignScore)
- Atomic fact verification (vd. FActScore)
- LLM-generated per-instance checklist
- Expert / manual checklist (kiểu PresentBench)
- Question / probe based
- LLM judge (pointwise và pairwise)
- Human
- Hybrid

## Ràng buộc bắt buộc từ Decision Gate

- **G-05 (HOLD):** F2 (NLI/AlignScore/FActScore) **chưa được accept**. Các method này validate chủ yếu trên summarization/factuality/biography, **chưa có nguồn nào áp lên slide deck**. Nhiệm vụ của RQ02 là trả lời "F2 nào **thực sự usable** cho Deck Agent", không phải "đã tìm thấy AlignScore → implement AlignScore". Chú ý case khó: bullet đã bị nén/diễn giải/tính toán lại (source *"Revenue increased from 12.4M to 18.7M because enterprise adoption accelerated"* → slide *"Enterprise adoption drove ~51% YoY revenue growth"*).
- **G-06 (ACCEPT principle, method TBD):** criteria phụ thuộc từng instance là đúng, nhưng **không mặc định** phải là expert viết tay. So sánh các cách **tạo** criteria: automatic critical-fact extraction, question generation, atomic facts, LLM-generated checklist, NLI probes.
- **G-07 (HOLD):** không chốt cỡ mẫu human. Ghi `TBD` kèm candidate scale.
- **G-09 (ACCEPT):** mọi model-based evaluator đề xuất phải kèm cách đo noise floor/repeatability.

## Bắt buộc có toy example của Deck Agent

Không kết luận chỉ bằng lý thuyết. Với source có các ý `A B C D E`:

- **Output 1:** `A B C` → thiếu D, E (coverage gap)
- **Output 2:** `A B C D F` → bịa F (hallucination)

Phải chỉ ra approach nào nhận diện được sự khác nhau giữa hai loại failure này. **Approach nào không phân biệt được → loại hoặc hạ priority.**

## Liên quan

- `03_research/WAVE1_SYNTHESIS.md` — G-02, G-05, G-06, G-07, G-09
- `03_research/RQ06/` — evaluator family landscape (F1–F5), đọc trước để không research lại
- `03_research/RQ01/` — vị trí của source-grounding trong taxonomy (đang HOLD, G-03)
- RQ03 (chạy song song, Wave 2A)
- RQ07 (Wave 3 — benchmark phải chứa dữ liệu mà metric RQ02 chọn cần)

## Findings

_(chưa có)_
