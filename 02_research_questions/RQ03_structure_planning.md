# RQ03 — Structure / `slide_type`

**Trạng thái: READY (Wave 2A).** Unblock sau Wave 1 Decision Gate (`03_research/WAVE1_SYNTHESIS.md`, 2026-08-14).

## Câu hỏi (scope hẹp — KHÔNG phải "structure quality metric nào tốt?")

> **Khi cùng một source được yêu cầu làm `teaching`, `catchup`, `speaker_support`, ta kỳ vọng output thay đổi ở những thuộc tính quan sát được nào?**

Sau khi có danh sách thuộc tính quan sát được, mới hỏi tiếp:

```
Thuộc tính nào code đo được?
Thuộc tính nào cần judge?
Thuộc tính nào cần human?
```

Đây chính là cách operationalize (biến thành thứ đo được) yêu cầu **"khác nhau có chủ đích"** của FR-02/FR-20 — thay vì đi tìm một "structure score" trừu tượng.

## Vì sao đặt câu hỏi theo hướng này

**PROJECT SOURCE:** FR-02 yêu cầu cùng một PDF, đổi `slide_type` phải cho cấu trúc deck khác **có chủ đích**. FR-20 yêu cầu `teaching | catchup | speaker_support` khác biệt về cách dùng chữ/ảnh/mật độ nội dung, và tự ghi "có checklist eval". DoD trước bảo vệ có mục "`slide_type` có khác biệt đo được".

Vấn đề: "khác biệt đo được" hiện chưa được định nghĩa. Nếu không operationalize, nó sẽ trượt thành một điểm số chủ quan không defensible.

**Lưu ý terminology (open question):** `slide_type` hiện nằm ở **Deck-level metadata** (`Deck.meta.slide_type`), không phải per-Slide — xem `00_context/TERMINOLOGY.md`. Không tự rename schema.

## Ví dụ CHỈ để minh họa cách suy nghĩ (KHÔNG phải expected truth)

```
teaching        → giải thích nhiều hơn, progression rõ hơn, có scaffolding
catchup         → ưu tiên coverage, density cao hơn, bỏ background cơ bản
speaker_support → ít text, visual cue nhiều, speaker notes quan trọng hơn
```

**Đây là giả định để minh họa dạng câu trả lời, chưa được validate.** RQ03 phải kiểm tra bằng external evidence + lý lẽ, không được coi bảng này là kết luận. Nếu evidence nói khác, ghi khác.

## Ràng buộc bắt buộc từ Decision Gate

- **G-04 (ACCEPT có phân vai):** được phép đề xuất signal ở tầng planner, nhưng phải ghi rõ đó là **diagnostic signal cho development** (deck xấu → truy ngược planner bỏ mất gì), **không** phải evidence chứng minh giá trị kiến trúc. Evidence về contribution của ContentPlanner đến từ **end-to-end + ablation**, không từ một "ContentPlanner Score".
- **G-03 (HOLD):** reference-frame taxonomy 5 lớp đang bị giữ để stress-test. Structure/brief-compliance là chỗ RQ01 cho rằng §8 không cover — RQ03 là một trong hai RQ có cơ hội kiểm chứng taxonomy đó gãy ở đâu.
- **G-09 (ACCEPT):** model-based evaluator nào được đề xuất phải kèm cách đo noise floor/repeatability.
- **Cảnh báo từ Wave 1:** RQ01 báo PPTEval human-correlation ở Coherence chỉ ~0.55 (so với Design ~0.90); RQ06 báo PresentBench ρ=0.532 so với trần người–người 0.664. Cả hai gợi ý **structure/coherence chính là chỗ judge yếu nhất** — đúng dimension Deck Agent claim giá trị nhất. RQ03 phải đối diện trực tiếp với vấn đề này, không lảng tránh.

## Phải trả lời

1. Thuộc tính quan sát được nào phân biệt 3 `slide_type`? (danh sách cụ thể, không phải tính từ chung chung)
2. Thuộc tính nào deterministic-measurable (vd. số slide, mật độ chữ/slide, tỉ lệ text:image, độ dài speaker_notes, số bullet/slide)?
3. Thuộc tính nào cần judge/human, và với độ tin cậy nào?
4. External work đo "structure/planning quality" của slide deck thế nào? Có precedent cho conditioning theo audience/mục đích không?
5. Làm sao phân biệt "**khác biệt có chủ đích**" với "khác biệt ngẫu nhiên do LLM sinh khác nhau mỗi lần"? (Đây là chỗ dễ tự lừa nhất: chạy 2 lần cùng `slide_type` cũng ra khác nhau.)
6. Có cần ground truth cho "cấu trúc đúng" không, hay chỉ cần đo **contrast giữa các `slide_type`**?

## Liên quan

- `03_research/WAVE1_SYNTHESIS.md` — G-03, G-04, G-09
- `03_research/RQ06/` — evaluator family landscape, đọc trước để không research lại
- `03_research/RQ01/` — Brief Compliance (L3) được RQ01 báo là không nằm trong RQ02–RQ05 nào; RQ03 là ứng viên tự nhiên nhận phần này
- RQ02 (chạy song song, Wave 2A)
- RQ07 (Wave 3 — nếu RQ03 cần test `slide_type`, mỗi benchmark source phải chạy cả 3 mode)

## Findings

_(chưa có)_
