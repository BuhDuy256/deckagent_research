# RQ03 — Landscape: operationalize "khác biệt có chủ đích" của `slide_type`

**Ngày research:** 2026-08-15
**Trục trả lời:** Khi cùng một source được yêu cầu làm `teaching`, `catchup`, `speaker_support`, output thay đổi ở những **thuộc tính quan sát được** nào — và thuộc tính nào code đo được / cần judge / cần human.
**Trạng thái:** research output, **KHÔNG phải decision**. Mọi câu được gắn nhãn `PROJECT SOURCE` / `EXTERNAL EVIDENCE` / `INFERENCE` / `RECOMMENDATION`.

Nguồn + evidence level + tag đọc: `evidence.md`. Đề xuất + confidence: `recommendation.md`.

**Đã đọc trước và KHÔNG research lại:** `03_research/RQ06/landscape.md` (evaluator family F1–F6, protocol P1–P6, bias, noise floor) và `03_research/RQ01/` (13 concept dimension, taxonomy 5 lớp). File này chỉ dùng lại kết luận của chúng, không dựng lại landscape evaluator.

---

## 0. Đặt lại bài toán trước khi làm gì khác

**PROJECT SOURCE** — nguyên văn từ `requirements-functional.pdf` (đọc trực tiếp 2026-08-15):

> **FR-02** | Nội dung | ContentPlanner quyết định số slide / nội dung / layout logic theo loại slide | *Cùng PDF, đổi slide_type → cấu trúc deck khác **có chủ đích***
> **FR-20** | Loại slide | Hỗ trợ teaching / catchup / speaker_support | *Khác biệt **cách dùng chữ/ảnh/mật độ nội dung** (có checklist eval)*
> **FR-21** | Layout | Tự chọn layout logic theo loại nội dung (title+bullets, image+caption...) | Layout là abstraction trên IR, không phải PPTX layout id
> **FR-23** | Meta | Chọn độ dài / số slide / thời gian present (hoặc hỏi lại user) | Deck phù hợp ràng buộc thời gian đã chọn
> **DoD — Trước bảo vệ:** `[ ] slide_type có khác biệt **đo được**`

**INFERENCE — câu FR-02 chứa hai mệnh đề khác nhau về bản chất, và §8 gộp chúng lại:**

```
"cấu trúc deck khác"          → một mệnh đề về QUAN HỆ giữa nhiều output
                                 (deck_teaching ≠ deck_catchup trên cùng source)

"có chủ đích"                  → một mệnh đề về Ý ĐỊNH
                                 (khác đúng hướng đã định, không phải khác lung tung)
```

Mệnh đề thứ nhất **không** đo được trên một deck đơn lẻ — nó chỉ tồn tại khi có ít nhất 2 deck từ cùng một source. Đây là điểm khác biệt cấu trúc lớn nhất giữa RQ03 và RQ02/RQ04/RQ05: các RQ kia đo **thuộc tính của một artifact**; RQ03 đo **quan hệ giữa các artifact**.

Mệnh đề thứ hai không đo được nếu **hướng kỳ vọng chưa được khai báo trước**. Nếu team quan sát khác biệt rồi mới giải thích tại sao khác biệt đó hợp lý, thì mọi khác biệt đều "có chủ đích" — bao gồm cả nhiễu. Đây là chỗ tự lừa nguy hiểm nhất của RQ03.

**Tên đúng của việc phải làm (EXTERNAL EVIDENCE, E2 — Hoewe 2017, Wiley Encyclopedia of Communication Research Methods):** đây là một **manipulation check** — phép đo xác minh rằng một thao tác thực nghiệm đã tác động **đúng như dự định**, và một check thành công là check *"elicits the expected differences between or among experimental conditions"*. Cái tên này quan trọng vì nó kéo theo cả bộ chuẩn phương pháp: điều kiện đối chứng, thiết kế ghép cặp, kiểm định thống kê, và **khai báo hướng kỳ vọng trước khi chạy**.

---

## 1. Không gian thuộc tính quan sát được bị chặn bởi schema — đọc schema trước khi liệt kê metric

**PROJECT SOURCE** — Deck IR draft, `kien-truc-do-an.pdf` §3 (đọc trực tiếp 2026-08-15):

```
Deck = {
  "meta": { "title", "language": "vi"|"en",
            "slide_type": "teaching"|"catchup"|"speaker_support",
            "time_limit_minutes": 15 | None },
  "design_tokens": { "colors": {...}, "font": {...} },
  "slides": [ {
      "id", "layout": "title+bullets",        # abstraction, không phải PPTX layout id
      "title",
      "blocks": [ {"type":"text","content","role":"bullet",
                   "geometry":{x,y,width,height}},   # ← giai đoạn mở rộng
                  {"type":"image","source_ref","caption"} ],
      "speaker_notes": "...",
      "locked_by_user": False,
      "source_ref": "page_3_paragraph_2"
  } ]
}
```

**Ba ràng buộc từ schema mà mọi metric RQ03 phải tôn trọng:**

1. **`slide_type` ở Deck-level, không per-Slide** (đã ghi ở `TERMINOLOGY.md`, Q-003). → Đơn vị phân tích của RQ03 là **deck**, không phải slide. Mọi feature per-slide phải được **aggregate lên deck** (mean/median/phân bố) trước khi so sánh. RQ03 **không** đề xuất đổi tên schema.
2. **`geometry` KHÔNG có ở vertical slice đầu tiên** — architecture doc ghi rõ *"Field geometry không cần có ở vertical slice đầu tiên - chỉ thêm khi bắt đầu code Preview Renderer + Selection Resolver (giai đoạn mở rộng)"*. → **Mọi thuộc tính dựa trên diện tích/hình học (tỉ lệ diện tích chữ, whitespace) KHÔNG khả dụng ở giai đoạn hiện tại**, trừ khi tính sau khi export và render lại. Đây là ràng buộc thực thi, không phải sở thích.
3. **`speaker_notes` tồn tại trong schema nhưng việc tự sinh nó là P3.** `PROJECT SOURCE` — FR-55 (nhóm P3 "Could"): *"Feedback-aware regeneration; **speaker notes tự sinh**"*. → **INFERENCE quan trọng:** giả định minh họa *"speaker_support → speaker notes quan trọng hơn"* **không kiểm chứng được ở P0/P1** vì tính năng sinh speaker notes chưa nằm trong scope. Nếu team muốn dùng `speaker_notes` làm thuộc tính phân biệt chính của `speaker_support`, đó là một **thay đổi ưu tiên FR-55 từ P3 lên P1**, không phải một lựa chọn metric.

**INFERENCE — hệ quả tổng:** danh sách thuộc tính quan sát được của RQ03 **không phải là danh sách tôi nghĩ ra, mà là danh sách schema cho phép**. Đây cũng là lý do RQ03 phải viết ra danh sách này *trước* khi đi tìm metric — làm ngược lại sẽ đề xuất metric không tính được.

---

## 2. External work đo "structure / planning quality" của deck bằng cách nào (câu hỏi 4)

**Không lặp lại RQ01/RQ06.** Phần này chỉ lấy ra những gì **cụ thể về structure**, và bổ sung 3 nguồn Wave 1 chưa có.

### 2.1 Bảng: mỗi nguồn đo structure bằng gì

**EXTERNAL EVIDENCE** — mọi ô trích từ nguồn tương ứng trong `evidence.md`.

| Nguồn (năm) | Thứ gần "structure" nhất | Cách tính | Deterministic? |
| --- | --- | --- | --- |
| **PPTEval** (2025) *(routed từ RQ01)* | **Coherence** — *"Structure develops progressively, incorporating essential background information"* | MLLM-judge thang 1–5, chấm ở **presentation level** | ✗ judged |
| **PresentBench** (2026-03) *(routed)* | **Presentation Fundamentals** — theme clarity, cross-slide logical flow, title relevance, conciseness | checklist nhị phân per-instance, judge từng item | ✗ judged |
| **SlideTailor** (2025-12) | **Flow** — thứ tự subtopic của deck sinh ra so với deck mẫu | **`1 − NGLD`** (Normalized General Levenshtein Distance) trên chuỗi subtopic | **✓ deterministic** (sau khi trích subtopic bằng model) |
| **SlideTailor** | **Coverage** — tập subtopic | **IoU** giữa 2 tập subtopic | **✓ deterministic** (sau bước trích) |
| **SlideTailor** | **Content Structure** — *"pace, detail level, visual formatting, slide transitions"*, cố tình bỏ qua chủ đề | LLM-judge 1–5 | ✗ judged |
| **X+Slides** (2026-06) | **Efficiency** — utility trên mỗi đơn vị attention cost | `Eff_slide = R_A/K`; `Eff_time = R_A/M`, **`M ← 0.25·K + Words(T)/130`** | **✓ deterministic ở mẫu số**, model-based ở tử số |
| **Learning to Present** (2026-03) | **Code Rules** — title presence, section count adherence, word count ratios | kiểm bằng code | **✓ deterministic** |
| **Learning to Present** | **Inverse Specification** — LLM khôi phục `{topic, audience, num_slides, key_themes}` **chỉ từ deck** | `r_recon = 0.40·s_topic + 0.25·s_audience + 0.15·s_count + 0.20·s_themes`; `s_count = min/max` deterministic, 3 phần còn lại LLM word-overlap | hỗn hợp |
| **EffectivePresentationScorer** (2026-06) | có **giải thích khái niệm nền (prerequisite)** trước khi dùng không | chưa đọc full-text → chưa rõ | chưa xác định |
| **CourseBlueprint** (2026-06) | **Logical Flow** — claim có được giới thiệu theo thứ tự nhất quán với cấu trúc phụ thuộc không | **LLM judge**, *không* kiểm bằng graph | ✗ judged |
| **§8 architecture doc** *(PROJECT SOURCE)* | **Structure** — *"số slide, cách chia nội dung hợp lý với loại slide"* | MLLM-as-judge | ✗ judged |

### 2.2 Bốn quan sát rút ra từ bảng

**Quan sát 1 — phần structure đo được deterministic thì đo bằng THỨ TỰ và ĐẾM, không phải bằng "chất lượng".**
Ba metric deterministic duy nhất trong bảng đều thuộc hai kiểu: (a) **so khớp chuỗi thứ tự** (SlideTailor Flow: NGLD), (b) **đếm và tỉ lệ** (Learning to Present Code Rules; X+Slides `M = 0.25K + Words/130`; slide count = min/max). **Không nguồn nào** có một công thức deterministic cho "cách chia nội dung hợp lý". **INFERENCE:** khi §8 viết "Structure (số slide, cách chia nội dung hợp lý...)" thì vế đầu là F1 và vế sau **không có phương án F1 nào tồn tại trong literature** — hai vế này không cùng loại và không nên chung một điểm số.

**Quan sát 2 — "attention cost" là khái niệm đã được operationalize, và Deck Agent có sẵn dữ liệu để dùng.**
X+Slides quy thời lượng trình bày về `M = 0,25·K + Words(T)/130` phút. Deck Agent có `meta.time_limit_minutes` (FR-23) và có toàn bộ text trong Deck IR → **tính được trực tiếp, không cần render, không cần model**. Đây là một trong ít chỗ mà một công thức external chuyển sang Deck Agent **không mất mát gì**.

**Quan sát 3 — nguồn duy nhất phát biểu tường minh về trục `teaching` vs `catchup` là EffectivePresentationScorer, và nó báo hệ thống hiện tại FAIL đúng chỗ đó.**
**EXTERNAL EVIDENCE** (Mondal et al. 2026, abstract, `[qua tool summary]`): *"generated videos mention the correct topics and follow the structure of the paper but fail to explain prerequisite concepts or clarify why the method works. These failures are often ignored by existing video evaluation metrics, which focus on content presence rather than explanatory quality."*
**INFERENCE:** ánh xạ sang Deck Agent, mệnh đề này nói rằng một hệ thống có thể đạt coverage tốt, đúng thứ tự nguồn, mà vẫn **không** ở "chế độ teaching". Tức **coverage và structure-order KHÔNG đủ để phân biệt teaching với catchup** — thứ phân biệt chúng nằm ở phần *giải thích*, và phần đó chưa có metric tự động nào được validate.

**Quan sát 4 — hệ thống conditioned gần nhất (SlideTailor) KHÔNG dùng các metric mật độ mà FR-20 gợi ý.**
**EXTERNAL EVIDENCE** `[qua tool summary]`: SlideTailor không có metric nào cho số slide, mật độ chữ/slide, hay tỉ lệ text:image. Nó tách metric thành **preference-based** (phụ thuộc preference của user) và **preference-independent** (chất lượng chung).
**INFERENCE:** FR-20 nói khác biệt nằm ở *"cách dùng chữ/ảnh/mật độ nội dung"*. Đây là một giả thuyết của team, **không có nguồn external nào xác nhận** đó là nơi khác biệt thực sự nằm. X+Slides còn nói ngược lại: *"audience adaptation mainly changes **information selection**"* — tức khác biệt nằm ở **chọn nói gì**, không phải ở **trình bày dày hay thưa**. → **FR-20 và X+Slides đang bất đồng về vị trí của khác biệt.** RQ03 không giải quyết được bất đồng này bằng lý lẽ; nó phải được giải quyết bằng đo đạc (xem §5).

---

## 3. Precedent cho conditioning output theo audience/mục đích (câu hỏi 4, phần 2)

### 3.1 Bảng precedent + mức transfer

| Nguồn | Conditioning theo cái gì | Cơ chế đo "có khác không" | **Mức transfer sang `slide_type`** |
| --- | --- | --- | --- |
| **X+Slides** (E1, 2026) | 3 audience: specialist / learner / decision-maker, mỗi audience gắn một thời lượng | **cùng probe, khác utility weight**; + **cross-audience scoring**: deck của audience X chấm bằng rubric của cả 3 → "target advantage" | **Cao về cơ chế, thấp về profile.** Cùng artifact (deck), cùng câu hỏi (conditioning có tác dụng không), **khác trục phân loại** (ai nghe vs dùng vào việc gì) |
| **Learning to Present** (E1 idea-source, 2026) | brief gồm topic + audience + num_slides + themes | **khôi phục spec từ deck**; `audience` chiếm 25% reward | **Cao về ý tưởng, cần sửa thủ tục** — word-overlap nên thay bằng forced-choice 3 lớp |
| **SlideTailor** (E1, 2025) | deck mẫu + template của user (preference ngầm) | tách **preference-based** vs **preference-independent** metric; Flow = 1−NGLD | **Trung bình.** Conditioning bằng ví dụ chứ không bằng nhãn → không chuyển thẳng; cách **tách 2 nhóm metric** thì chuyển được |
| **CourseBlueprint** (E1 preprint nhỏ, 2026) | trình độ người học | 3/7 metric là "level-aware"; FK readability deterministic | **Thấp–trung bình.** Artifact là video; **FK không dùng được cho tiếng Việt** |
| **Controllable readability summarization** (E1, EMNLP 2023) | mức readability yêu cầu | tối ưu *"gap between requested and observed readability"* | **Trung bình.** Cùng hình thái (đặt mức → đo output có tới mức đó không), nhưng readability **có thang sẵn**, `slide_type` **không có** |
| **Controllable text generation / PPLM** (E1, ICLR 2020) | attribute (topic/sentiment) | chuẩn của lĩnh vực là **external attribute classifier accuracy** ⚠ *chưa quote-verified — xem `evidence.md` DISCOVERY* | **Cao về phương pháp** nếu verify được: dùng một bộ phân loại độc lập để hỏi "output này có mang attribute được yêu cầu không" chính là recovery test |
| **Persona effect** (E1, ACL 2024) | persona của annotator | phân tích variance | **Thấp** (task mô phỏng annotator). Chỉ dùng để hiệu chỉnh kỳ vọng về **độ lớn** hiệu ứng |

### 3.2 Ba kết luận về precedent

**Kết luận 1 (EXTERNAL EVIDENCE) — precedent tồn tại, và nó nói rằng conditioning HAY THẤT BẠI.**
X+Slides Table 9 `[qua tool summary]`: với hệ thống DeepPresenter, "target advantage" cho specialist prompt là **−0.117** — tức deck được yêu cầu làm cho specialist lại đạt điểm **cao hơn** dưới rubric của learner và decision-maker so với dưới rubric specialist. Hai audience còn lại dương nhưng nhỏ (+0.079, +0.082). Tác giả kết luận: *"While audience prompts are effective, they do not consistently align with the intended profile without audience-conditioned evaluation."*

**INFERENCE — đây là finding quan trọng nhất của RQ03 đối với rủi ro dự án:** đã có bằng chứng E1 rằng một hệ thống sinh slide thật, khi được prompt theo audience, **có thể tạo ra khác biệt đi sai hướng**. Deck Agent không được giả định rằng chỉ cần truyền `slide_type` vào prompt là FR-02 tự thỏa. **Xác suất thực tế cao là phép đo sẽ cho kết quả "không đạt" ở lần chạy đầu** — và đó là kết quả *đúng*, không phải lỗi của phép đo.

**Kết luận 2 (EXTERNAL EVIDENCE) — hiệu ứng conditioning có độ lớn NHỎ.**
X+Slides paired delta lớn nhất ~+0,083 trên thang [0,1]; Hu & Collier: persona variable giải thích **<10% variance**.
**INFERENCE:** phép đo của Deck Agent phải đủ nhạy để bắt hiệu ứng cỡ vài phần trăm. Điều này gần như loại bỏ hoàn toàn cách làm ngây thơ *"chấm mỗi deck một điểm structure 1–5 rồi so trung bình"* — độ phân giải của thang 5 mức, cộng noise floor của judge, lớn hơn hiệu ứng cần bắt.

**Kết luận 3 (negative finding) — bộ nhãn của Deck Agent không có trong literature.**
Đã search chủ động: **không nguồn nào** dùng `teaching | catchup | speaker_support` hay tương đương. `catchup` (deck cho người bỏ lỡ buổi học, đọc một mình) và `speaker_support` (deck làm nền cho người nói) chỉ xuất hiện trong tài liệu nghề nghiệp mức **E3**.
**INFERENCE — hệ quả nặng và phải nói thẳng:** **không có literature nào định nghĩa được "đúng" cho ba mode này.** Do đó hướng kỳ vọng của khác biệt **không tra được**; nó **phải do team khai báo** như một spec. Và vì nó là spec do team viết, nó phải được **viết ra trước khi đo**, nếu không phép đo mất khả năng bác bỏ.

---

## 4. Bảng thuộc tính quan sát được × 3 `slide_type` × đo bằng gì (câu hỏi 1, 2, 3)

**Đây là deliverable trung tâm của RQ03.**

**Cách đọc bảng — bắt buộc đọc trước:**
- Cột **Hướng kỳ vọng** ghi **giả thuyết cần khai báo và kiểm chứng**, KHÔNG phải kết luận. Ô ghi `TEAM PHẢI KHAI BÁO` nghĩa là **không có nguồn nào cho biết hướng**, team phải tự chọn và chịu trách nhiệm.
- Cột **Nguồn cho hướng** cho biết mức hậu thuẫn của hướng đó: `—` = không có nguồn; `E3` = chỉ có blog; `gián tiếp` = có nguồn nhưng ở domain/artifact khác.
- Cột **Đo bằng** dùng ký hiệu evaluator family của RQ06: **F1** deterministic/rule · **F2** model-based automatic metric · **F3/F4** LLM/MLLM judge · **F5** human.
- Cột **Tính được từ đâu:** `IR` = tính thẳng từ Deck IR (rẻ nhất, có ngay); `IR+model` = cần một bước trích bằng model rồi mới tính; `render` = phải export + render (chưa có geometry ở P0); `source` = cần cả tài liệu nguồn.

### 4.A — Nhóm ĐẾM & TỈ LỆ (F1 thuần, tính từ Deck IR, không cần model)

| # | Thuộc tính quan sát được | Tính từ | Hướng kỳ vọng (giả thuyết) | Nguồn cho hướng | Đo bằng |
| --- | --- | --- | --- | --- | --- |
| A1 | `slide_count` = `len(slides)` | IR | TEAM PHẢI KHAI BÁO | — | F1 |
| A2 | Tổng số từ toàn deck (title + text block) | IR | TEAM PHẢI KHAI BÁO | — | F1 |
| A3 | **Từ / slide** (mean, median, **và độ lệch chuẩn**) | IR | `speaker_support` thấp nhất, `catchup` cao nhất | **E3** (Presenter's Arena: ~6 từ vs 80–120 từ/slide) + gián tiếp (Garner et al. 2009: thực tế học thuật ~33,4 từ/slide) | F1 |
| A4 | Số bullet / slide (`blocks` có `role="bullet"`) | IR | TEAM PHẢI KHAI BÁO | — | F1 |
| A5 | Từ / bullet | IR | TEAM PHẢI KHAI BÁO | — | F1 |
| A6 | Số block / slide và **phân bố loại block** (text vs image) | IR | TEAM PHẢI KHAI BÁO | — | F1 |
| A7 | **Tỉ lệ slide có ít nhất 1 image block** | IR | `speaker_support` cao nhất | gián tiếp (assertion-evidence: evidence chủ yếu bằng hình cho comprehension tốt hơn) + FR-20 nói khác biệt ở "cách dùng chữ/ảnh" | F1 |
| A8 | **Tỉ lệ block text : block image** toàn deck | IR | TEAM PHẢI KHAI BÁO | — | F1 |
| A9 | **Histogram của `Slide.layout`** trên tập giá trị abstraction (`title+bullets`, `image+caption`, ...) + entropy của histogram | IR | TEAM PHẢI KHAI BÁO | — (nhưng FR-21 nói layout phải chọn theo loại nội dung) | F1 |
| A10 | **Độ dài `speaker_notes`** (số từ) và **tỉ lệ `notes_words : body_words`** | IR | `speaker_support` cao nhất | — (không precedent; SlideTailor sinh speech nhưng **không** evaluate) | F1 |
| A11 | **Tỉ lệ slide có `speaker_notes` khác rỗng** | IR | như A10 | — | F1 |
| A12 | Độ dài title (số từ) | IR | TEAM PHẢI KHAI BÁO | — | F1 |
| A13 | **Tỉ lệ title là câu khẳng định đầy đủ** (có động từ chính / kết thúc bằng dấu câu) vs cụm danh từ | IR | `teaching` cao nhất | gián tiếp (Garner & Alley 2013 — assertion-evidence cho comprehension tốt hơn; **nhưng nguồn nói về slide tốt nói chung, KHÔNG nói về phân biệt mode**) | F1 (heuristic ngôn ngữ — brittle, xem §4.E) |
| A14 | **Thời lượng ước lượng** `M = 0,25·K + Words/130` và **sai lệch so với `meta.time_limit_minutes`** | IR + meta | tất cả mode: sai lệch → 0 | **X+Slides (E1)** — công thức lấy nguyên | F1 |
| A15 | **Sai lệch `slide_count` so với `target_slides`** đã yêu cầu (FR-23) | IR + brief | tất cả mode: → 0 | **IFEval (E1)** nguyên tắc verifiable instruction; **Learning to Present** `s_count = min/max` | F1 |

### 4.B — Nhóm QUAN HỆ VỚI NGUỒN (F1/F2, cần `source_ref` + tài liệu nguồn)

| # | Thuộc tính quan sát được | Tính từ | Hướng kỳ vọng (giả thuyết) | Nguồn cho hướng | Đo bằng |
| --- | --- | --- | --- | --- | --- |
| B1 | **Compression ratio** = số từ deck / số từ nguồn | IR + source | TEAM PHẢI KHAI BÁO | — | F1 |
| B2 | **Độ phủ vùng nguồn** = số `source_ref` phân biệt / tổng số block nguồn | IR + source | TEAM PHẢI KHAI BÁO | — | F1 |
| B3 | **Phân bố `source_ref` theo vị trí trong nguồn** (đầu / giữa / cuối tài liệu) | IR + source | `catchup` bỏ phần đầu (background) nhiều hơn — **giả định minh họa, chưa validate** | — | F1 |
| B4 | **Trật tự trình bày so với trật tự nguồn**: `1 − NGLD` trên chuỗi `source_ref`, hoặc Kendall's τ | IR + source | TEAM PHẢI KHAI BÁO | **SlideTailor Flow (E1)** — cùng công thức, đổi đối tượng so sánh từ "deck mẫu" sang "thứ tự nguồn" | F1 |
| B5 | **Tập chủ đề được chọn** (IoU giữa 2 mode trên cùng source) | IR+model | `teaching` ≠ `catchup` ở tập chủ đề, không chỉ ở độ dài | **X+Slides (E1)**: *"audience adaptation mainly changes information selection"* | F2 (trích chủ đề bằng model rồi so bằng code) |
| B6 | **Trọng số utility theo mode**: cùng bộ probe sinh từ nguồn, khác weight theo `slide_type` | IR+model+source | mỗi mode đạt coverage cao nhất dưới weight của **chính nó** | **X+Slides (E1)** — cơ chế lấy nguyên | F2/F4 (probe + judge trả lời) |
| B7 | **Non-redundancy**: lặp n-gram/ý giữa các slide | IR | TEAM PHẢI KHAI BÁO | — | F1 |

### 4.C — Nhóm CẦN JUDGE (F3/F4)

| # | Thuộc tính | Vì sao không F1 được | Độ tin cậy dự kiến | Ghi chú |
| --- | --- | --- | --- | --- |
| C1 | **Có giải thích khái niệm nền (prerequisite) trước khi dùng không** | Không có phương án deterministic nào tồn tại — CourseBlueprint có ý tưởng dependency structure nhưng vẫn giao cho LLM judge | **Chưa biết.** Gần nhất là PPTEval Coherence: Pearson **0.55** — thấp nhất trong 3 dimension | Đây là attribute có nguồn tường minh nhất cho trục `teaching` vs `catchup` (EffectivePresentationScorer) |
| C2 | **Phần background bị thiếu là CỐ Ý bỏ hay VÔ TÌNH sót** | Cùng một quan sát ("không có background") mang hai nghĩa đối lập; phân biệt được chỉ khi đối chiếu với ý định | Thấp — phán đoán về ý định | **Xem §6.2 — đây là chỗ RQ02 và RQ03 va nhau** |
| C3 | **Thân slide có đứng một mình được không** (đọc không cần người nói) | Là phán đoán về đủ ngữ cảnh, không quy được về predicate | Chưa có nguồn nào đo | Trục `catchup` vs `speaker_support`; **không có literature** |
| C4 | **Recovery test:** cho model chỉ nhìn deck rồi chọn 1 trong 3 `slide_type` (forced choice) | — | **Đây là task DỄ hơn nhiều so với chấm coherence 1–5** | **Learning to Present (E1)** làm đúng việc này ở mức `audience`; Chen et al. ICML 2024 (routed): MLLM judge mạnh nhất ở so sánh/lựa chọn, yếu nhất ở scoring tuyệt đối |
| C5 | "Cách chia nội dung có **hợp lý** không" (nguyên văn §8) | Không có định nghĩa hình thức, không nguồn nào có công thức | **Thấp — PPTEval Coherence 0.55, JudgeSense xếp coherence là dimension bất ổn nhất** | **RQ03 KHÔNG khuyến nghị dùng cái này làm metric theo dõi** — xem `recommendation.md` |

### 4.D — Nhóm CẦN HUMAN (F5)

| # | Thuộc tính | Vì sao bắt buộc human | Tần suất |
| --- | --- | --- | --- |
| D1 | **Hướng kỳ vọng của mỗi mode có đúng không** — "teaching *nên* trông thế nào" | Không có literature định nghĩa 3 nhãn này (negative finding). Đây là **quyết định sản phẩm**, không phải sự thật đo được | Một lần, khi khai báo spec; xem lại khi đổi định nghĩa mode |
| D2 | **Deck này có thực sự dùng được cho mục đích đó không** | Là câu hỏi về giá trị sử dụng | Milestone |
| D3 | **Calibrate recovery judge**: người có phân biệt được 3 mode từ deck không, và ở mức nào | Cần **trần người** để biết judge còn cách trần bao xa (nguyên tắc từ RQ06 §A3) | Milestone |

**INFERENCE quan trọng về D3:** trần người ở đây có thể **cao hơn** trần người của PPTEval Coherence. Lý do: PresentBench báo human khi rank nhanh **ưu tiên** *"readily perceivable cues such as structure, layout, and overall coherence"*. Tức con người **nhạy** với khác biệt cấu trúc — họ chỉ **bất đồng** khi phải quy nó thành một điểm số 1–5. Một task **phân loại 3 lựa chọn** né được đúng chỗ bất đồng đó. Đây là suy luận, cần đo mới biết.

### 4.E — Ba cảnh báo về chính bảng trên

**Cảnh báo 1 — nhiều thuộc tính F1 phụ thuộc ngôn ngữ, và Deck Agent song ngữ.**
A13 (title có phải câu khẳng định), và mọi metric readability kiểu Flesch-Kincaid, **không hợp lệ cho tiếng Việt**. `meta.language` nhận `"vi"|"en"`, và FR-35 (P2) đặt song ngữ vào roadmap. **RECOMMENDATION:** so sánh `slide_type` chỉ trong cùng một `language`; không gộp deck tiếng Việt và tiếng Anh vào một phép so sánh.

**Cảnh báo 2 — "đo được" không bằng "đúng".**
**EXTERNAL EVIDENCE** (Cachola et al., EMNLP 2025): trong 8 readability metric được đánh giá, **phần lớn tương quan kém với phán đoán của người**, kể cả FKGL là metric phổ biến nhất; LM judge tốt nhất chỉ đạt Pearson **0.56**.
**INFERENCE:** hệ quả trực tiếp cho RQ03 — một proxy bề mặt như `words/slide` **hoàn toàn có thể** phân biệt được 3 mode một cách thống kê mà **vẫn không** tương ứng với thứ người dùng cảm nhận là "phù hợp cho việc dạy". Nhóm A chứng minh **có khác biệt**; nó **không** chứng minh **khác biệt đó có giá trị**.

**Cảnh báo 3 — bảng này gameable, và đây là failure mode phải nêu ra trước.**
**INFERENCE:** mọi thuộc tính nhóm A đều tối ưu hóa được một cách rẻ tiền mà không cải thiện gì. Ví dụ: nhồi thêm chữ vào mode `catchup` và cắt bớt chữ ở `speaker_support` sẽ làm **mọi** phép đo contrast "đạt", trong khi deck cả ba mode đều tệ hơn. FR-02 dùng chữ *"có chủ đích"* chứ không phải *"khác nhau"* — nên một phép kiểm contrast **là điều kiện cần, không phải điều kiện đủ**, và **bắt buộc phải chạy kèm một sàn chất lượng** (fidelity/coverage của RQ02 + L0 artifact integrity). Nếu báo cáo chỉ ra "slide_type differentiation ✓" mà không kèm sàn này thì đó là một claim rỗng.

---

## 5. Câu khó nhất: phân biệt "khác biệt có chủ đích" với "khác biệt ngẫu nhiên" (câu hỏi 5)

### 5.1 Vấn đề, phát biểu chính xác

**EXTERNAL EVIDENCE** (Atil et al., arXiv 2408.04667, E1): trên 5 LLM × 8 task × 10 run ở cấu hình được kỳ vọng deterministic, accuracy dao động tới **~15%**, khoảng cách best–worst tới **~70%**, và *"none of the LLMs consistently delivers repeatable accuracy across all tasks"*. Họ đề xuất **TARr@N / TARa@N** để đo mức đồng thuận qua N run.
**EXTERNAL EVIDENCE bổ sung** (routed từ RQ06 §4.5): pin `temperature=0` **giảm nhưng không loại bỏ** nondeterminism.

**INFERENCE — phát biểu vấn đề dưới dạng đo được:**

```
Quan sát:  deck(source S, slide_type = teaching)  ≠  deck(source S, slide_type = catchup)

Hai lời giải thích cạnh tranh:
  H1 (có chủ đích): khác vì slide_type khác
  H0 (ngẫu nhiên) : khác vì chạy hai lần thì luôn khác
```

**Nếu không có điều kiện đối chứng cho H0 thì H1 không bao giờ bị bác bỏ được** — và một claim không bao giờ bị bác bỏ được thì không phải evidence. Đây là lý do RQ03 tồn tại.

### 5.2 Thiết kế giải quyết: three-armed, paired theo source

**RECOMMENDATION** (trace: `problem §5.1 → evidence Atil et al. + Dror et al. + Hoewe → reasoning → recommendation`).

```
Với mỗi source S trong benchmark, sinh:

   ARM 1 — teaching        × R lần   ┐
   ARM 2 — catchup         × R lần   ├─ cùng model, cùng prompt template,
   ARM 3 — speaker_support × R lần   ┘  chỉ khác giá trị meta.slide_type

Từ đó tính hai đại lượng trên MỖI feature f của §4.A/4.B:

   WITHIN(f, S)  = độ phân tán của f giữa R lần chạy CÙNG một slide_type   ← noise floor
   BETWEEN(f, S) = khoảng cách của f giữa các slide_type khác nhau          ← tín hiệu

   Kết luận "khác biệt có chủ đích" chỉ được phát biểu khi
   BETWEEN vượt WITHIN một cách có ý nghĩa thống kê.
```

**Ba chi tiết thiết kế bắt buộc, mỗi cái có lý do:**

1. **Ghép cặp theo source (paired design), không so trung bình toàn benchmark.** Phương sai **giữa các source** (một PDF 5 trang vs 40 trang) lớn hơn hẳn phương sai giữa các `slide_type` — X+Slides và Hu & Collier đều cho thấy hiệu ứng conditioning nhỏ (~0,05–0,08; <10% variance). So trung bình toàn cục sẽ **chôn** tín hiệu dưới phương sai source. Mọi so sánh phải là **trong-cùng-một-source**.
2. **`R ≥ 5` lần lặp mỗi ô, ít nhất một lần ở giai đoạn hiệu chuẩn.** Đây là G-09 áp cho generator chứ không chỉ cho judge: **generator cũng có noise floor**, và với RQ03 thì noise floor của generator mới là thứ quyết định. Không có R > 1 thì WITHIN không tồn tại và toàn bộ lập luận sụp.
3. **Chỉ đổi đúng một biến.** `meta.slide_type` là biến duy nhất thay đổi giữa 3 arm. Model, prompt template, `target_slides`, `time_limit_minutes`, seed policy, phiên bản code phải giữ nguyên — và phải nằm trong run manifest (G-14 đã yêu cầu freeze). Nếu `time_limit_minutes` thay đổi theo mode (như X+Slides gắn 12/15/8 phút cho 3 audience) thì đó là **hai biến**, và phải khai báo là thiết kế có chủ ý chứ không được để trôi ngầm.

### 5.3 Ba phép kiểm, xếp theo sức mạnh bằng chứng

**RECOMMENDATION.** Ba phép kiểm dưới đây trả lời ba câu **khác nhau**; không thay thế nhau.

#### Kiểm 1 — CÓ khác biệt không? (per-feature, thuần deterministic, không có model trong vòng lặp)

Với mỗi feature `f` ở §4.A/4.B:
- Tính effect size giữa hai mode trên cùng source, chuẩn hóa theo WITHIN (dạng `(mean_A − mean_B) / sd_within`).
- Kiểm định bằng **permutation test**: hoán vị nhãn `slide_type` **trong từng source** rồi tính lại thống kê → phân phối null. Chọn permutation vì nó không giả định phân phối chuẩn và tôn trọng cấu trúc ghép cặp; **EXTERNAL EVIDENCE** cho việc phải chọn test có chủ đích: Dror et al. (ACL 2018) đề xuất protocol chọn significance test theo đặc điểm task/measure.
- Báo cáo **khoảng tin cậy bootstrap** — X+Slides dùng 10.000 lần resample, đây là mẫu sẵn.

**Ưu:** không có model nào trong vòng lặp đo → **hoàn toàn lặp lại được**, rẻ, chạy được mỗi nightly. Kết quả là một **bảng feature nào mang tín hiệu**, không phải một điểm số.
**Nhược:** không nói gì về **hướng** hay **giá trị** của khác biệt.

#### Kiểm 2 — Khác biệt có ĐÚNG HƯỚNG đã khai báo không? (pre-registered direction test)

**Bắt buộc: team viết ra bảng hướng kỳ vọng TRƯỚC khi chạy**, dạng bảng dấu, ví dụ về hình thức (KHÔNG phải nội dung được khuyến nghị):

```
feature                      teaching   catchup   speaker_support
words_per_slide                  ?         ?            ?
image_bearing_slide_ratio        ?         ?            ?
...                           (team điền dấu >, <, = theo cặp)
```

Sau đó tính **tỉ lệ cặp (feature, cặp-mode) đúng hướng đã khai báo**. Đây là một con số tuyệt đối, so được xuyên thời gian, và **có thể fail** — tính chất mà một metric muốn dùng làm evidence bắt buộc phải có.

**Vì sao phải pre-register:** không có nguồn external nào định nghĩa 3 mode này (§3.2 kết luận 3), nên hướng là **spec do team viết**. Nếu viết sau khi nhìn kết quả, phép đo mất khả năng bác bỏ. Đây chính là nội dung của manipulation check (Hoewe 2017, E2) — check thành công là check *"elicits the **expected** differences"*, chữ *expected* nằm trước phép đo.

**Ghi nhận trung thực:** bảng minh họa trong `02_research_questions/RQ03_structure_planning.md` (teaching → giải thích nhiều hơn; catchup → density cao hơn, bỏ background; speaker_support → ít text, nhiều visual cue) là **một ứng viên hợp lý cho bảng này** — nhưng RQ03 **không** xác nhận nó, vì §3.2 và §2.2 (Quan sát 4) cho thấy: (a) không nguồn nào hậu thuẫn các hướng đó; (b) X+Slides nói khác biệt nằm chủ yếu ở **information selection** chứ không ở density; (c) phần `speaker_notes` phụ thuộc FR-55 hiện là **P3**.

#### Kiểm 3 — Khác biệt có ĐỦ MẠNH để nhận ra không? (recovery / manipulation check)

Cho một bộ phân loại **chỉ nhìn deck** (không thấy `meta.slide_type`) đoán nó thuộc mode nào. Chance = **1/3**. Kiểm định nhị thức so với 1/3.

Hai biến thể, nên chạy **cả hai** vì chúng nói hai điều khác nhau:

| Biến thể | Cách làm | Nói lên điều gì | Lặp lại được? |
| --- | --- | --- | --- |
| **3a — classifier deterministic trên feature vector** | Huấn luyện một mô hình đơn giản (logistic regression / decision tree nông) trên chính các feature §4.A–4.B, **cross-validation nhóm theo source** (leave-one-source-out — bắt buộc, vì cùng một source xuất hiện ở cả 3 arm → không group sẽ rò rỉ) | **Khác biệt có nằm trong các thuộc tính bề mặt đo được không**, và **feature nào mang tín hiệu** (đọc feature importance) — tức nó **tự trả lời câu hỏi 1 của RQ03 bằng dữ liệu** thay vì bằng giả định | **Hoàn toàn** (pin seed, model nhỏ, chạy local) |
| **3b — recovery bằng LLM/MLLM, forced choice 3 lựa chọn** | Đưa deck (text từ IR, hoặc ảnh render) cho judge, hỏi *"deck này được làm cho teaching, catchup hay speaker_support?"*, không cho thấy metadata | Khác biệt có **nhận ra được bởi một người đọc thông thường** không — kể cả khi nó không nằm trong feature bề mặt | **Không** → phải đo noise floor (chạy lại N ≥ 5 lần trên cùng deck), đúng G-09 |

**Vì sao 3b đáng tin hơn hẳn "chấm structure 1–5":**
- **EXTERNAL EVIDENCE (routed)** Chen et al. ICML 2024: MLLM judge thể hiện *"remarkable human-like discernment in Pair Comparison"* nhưng *"significant divergence from human preferences in Scoring Evaluation and Batch Ranking"*. Forced-choice phân loại gần với so sánh hơn là chấm điểm tuyệt đối.
- **EXTERNAL EVIDENCE (routed)** PPTEval Coherence chỉ đạt human correlation Pearson 0.55 — nhưng đó là con số cho **chấm điểm coherence**, không phải cho **phân loại mode**. Đây là hai task khác nhau; RQ03 **không** giả định 0.55 áp cho task phân loại.
- **EXTERNAL EVIDENCE** Learning to Present đã làm đúng việc này (recover `audience` từ deck, chiếm 25% reward). RQ03 chỉ siết chặt hơn: **3 nhãn đóng + forced choice** thay vì word overlap, để có mức chance xác định và kiểm định được.
- **Chống bias:** phân loại nhị phân/đa lớp không có position bias (không có thứ tự A/B), và ít nhạy verbosity hơn scoring — nhưng **length bias vẫn có thể lọt vào** nếu judge chỉ đoán theo độ dài. **Kiểm chéo bắt buộc:** nếu 3a (classifier bề mặt) đạt độ chính xác gần bằng 3b, thì 3b **không cung cấp thông tin mới** — nó chỉ đang đọc độ dài. Đây là một phép kiểm rẻ và rất nhiều thông tin.

### 5.4 Bảng đọc kết quả — bốn tổ hợp, bốn kết luận khác nhau

**INFERENCE.** Đây là phần làm cho ba phép kiểm trên có giá trị chẩn đoán chứ không chỉ ra một chữ pass/fail.

| Kiểm 1 (có khác) | Kiểm 2 (đúng hướng) | Kiểm 3 (nhận ra được) | Kết luận |
| :-: | :-: | :-: | --- |
| ✗ | — | ✗ | **`slide_type` không có tác dụng.** Planner đang bỏ qua nó. Đây là failure mode nghiêm trọng nhất — và theo X+Slides, **hoàn toàn có thể xảy ra** |
| ✓ | ✗ | ✓ | **Có khác biệt nhưng sai hướng** — đúng tình huống X+Slides đo được (target advantage âm). Claim FR-02 **không** đứng vững dù "có khác biệt đo được" |
| ✓ | ✓ | ✗ | Khác biệt có, đúng hướng, nhưng **quá nhỏ để nhận ra**. Đạt DoD ở mức chữ nghĩa, nhưng không đạt ở mức người dùng |
| ✓ | ✓ | ✓ | Điều kiện **cần** của FR-02 đã đủ. **Vẫn chưa đủ để kết luận tốt** — phải kèm sàn chất lượng (§4.E cảnh báo 3) |

**INFERENCE:** một điểm số gộp duy nhất ("slide_type differentiation = 0.72") sẽ **xóa mất** toàn bộ bốn tổ hợp này. Đây là cùng loại lập luận mà G-02 dùng cho content fidelity, áp vào RQ03.

### 5.5 Chi phí — phải nói vì nó quyết định tính khả thi

**INFERENCE.** Thiết kế đầy đủ tốn `|S| × 3 × R` lần sinh deck. Với 30 source × 3 mode × 5 lần = **450 deck**. Đây là chi phí **generation**, không phải chi phí judge.

Phân tầng đề xuất (RECOMMENDATION, cần team xác nhận theo budget thật):
- **Hiệu chuẩn noise floor — một lần / khi đổi model:** tập nhỏ (vd. 8–10 source) × 3 mode × R=5. Ra được WITHIN.
- **Theo dõi thường xuyên — nightly/weekly:** toàn benchmark × 3 mode × R=1, dùng WITHIN đã hiệu chuẩn làm ngưỡng nhiễu. Chỉ chạy Kiểm 1 + Kiểm 2 + Kiểm 3a (đều deterministic).
- **Milestone:** thêm Kiểm 3b (judge) + human D3.

**Cảnh báo:** WITHIN đo một lần **không còn hiệu lực khi đổi model generator hoặc prompt template**. Phải hiệu chuẩn lại — và điều này phải nằm trong run manifest (G-14).

---

## 6. Có cần ground truth cho "cấu trúc đúng" không? (câu hỏi 6)

### 6.1 Câu trả lời tách làm ba

**INFERENCE — trace: `problem → §3.2 kết luận 3 (không có literature định nghĩa 3 mode) + §5 thiết kế → reasoning`.**

| Cần chứng minh gì | Có cần ground truth không | Vì sao |
| --- | --- | --- |
| **(a) Có khác biệt trên cùng source** | **KHÔNG** | Nhãn `slide_type` đã biết **theo cách xây dựng** — chính team yêu cầu chúng. Đây là setup **tự gán nhãn**: không cần ai phán "cấu trúc đúng là gì", chỉ cần so 3 output với nhau. **Đây là lợi thế thực tiễn lớn nhất của cách tiếp cận contrast.** |
| **(b) Khác biệt đúng hướng** | **CÓ, nhưng không phải ground truth theo nghĩa "deck mẫu chuẩn"** — mà là **một spec do team khai báo trước** (bảng dấu ở Kiểm 2) | Không có literature định nghĩa 3 nhãn này. Ground truth ở đây là **quyết định sản phẩm**, không phải sự thật khách quan. Nó rẻ (một bảng), nhưng phải **viết trước** |
| **(c) Spec đó có đúng không** | **CÓ — human, một lần, mẫu nhỏ** | Đây là câu hỏi về giá trị sử dụng, không có phương án tự động. Ứng viên: cho người xem deck của cả 3 mode trên cùng source rồi hỏi cái nào hợp việc nào (chính là D3) |

### 6.2 Điều KHÔNG cần: gold deck

**EXTERNAL EVIDENCE (routed từ RQ01):** reference-based similarity (ROUGE-SL, mIoU, element matching) **không phân biệt được "khác" với "kém"** — PPTAgent Table 3: KCTV có ROUGE-L cao nhất (16.76) nhưng Content thấp nhất (2.55); AutoPresent tự cảnh báo lỗi do element-matching.
**INFERENCE:** với RQ03, gold deck còn vô dụng hơn ở các RQ khác: cần **3 gold deck cho mỗi source** (một cho mỗi mode), do người làm, nhất quán về tiêu chuẩn. Đó là một dự án riêng, và ngay cả khi có thì nó vẫn không đo được cái RQ03 cần đo (quan hệ giữa các output). **RQ03 khuyến nghị KHÔNG theo hướng gold deck**, nhất quán với P-10 của RQ01.

### 6.3 Va chạm RQ02 ↔ RQ03 — phải nêu ra, không được để ngầm

**INFERENCE.** Xét quan sát: *"deck không nhắc tới phần kiến thức nền ở chương 1 của nguồn"*.

```
Đọc theo RQ02 (source frame)  → COVERAGE GAP: bỏ sót ý quan trọng → LỖI
Đọc theo RQ03 (brief frame)   → INTENTIONAL OMISSION cho catchup   → ĐÚNG SPEC
```

**Cùng một quan sát, hai nhãn đối lập, và cái quyết định là `meta.slide_type` — một biến nằm ngoài phạm vi RQ02.** Nếu RQ02 đo coverage mà không điều kiện hóa theo `slide_type`, nó sẽ **phạt `catchup` vì đã làm đúng việc của nó**.

**RECOMMENDATION (cần điều phối, RQ03 không tự quyết):** coverage checklist/probe của RQ02 nên có **trọng số theo `slide_type`** — đây chính xác là cơ chế X+Slides dùng (*cùng probe, khác utility weight*), và là lý do cơ chế đó đáng chú ý hơn là một chi tiết kỹ thuật. RQ03 **không** sửa RQ02; ghi nhận ở đây và đưa vào mục 4 của `recommendation.md`.

---

## 7. Stress-test taxonomy 5 lớp của RQ01 (G-03) — ba chỗ nó gãy

**G-03 yêu cầu Wave 2 dùng taxonomy L0–L5 như working taxonomy đồng thời **tích cực tìm chỗ nó gãy**. RQ03 tìm được ba chỗ.**

### 7.1 Gãy 1 — RQ03 cần một reference frame thứ tư mà taxonomy không có

Taxonomy L0–L5 phân theo *"deck đang được so với cái gì"*: chính nó (L0) / tài liệu nguồn (L1, L2) / brief của user (L3) / chuẩn trình bày nội tại (L4) / người dùng & người xem (L5).

Phép đo trung tâm của RQ03 so deck với **một deck anh em sinh từ cùng source dưới điều kiện khác**. Đó **không phải** bốn frame nào ở trên. Gọi tên: **contrastive / relational reference frame**.

**INFERENCE:** đây không phải lỗi nhỏ về cách đặt tên. Nó nói rằng nguyên tắc tổ chức của taxonomy — *"reference frame quyết định method"* — **bỏ sót cả một lớp phép đo**: những phép đo mà đối tượng so sánh là **output khác của chính hệ thống**. Và lớp đó bao gồm không chỉ `slide_type` differentiation mà cả **regression testing giữa các version** và **ablation** — tức những thứ nằm ở trung tâm mission (`EVALUATION_MISSION.md`). Taxonomy hiện tại **không có chỗ cho chính mission**.

### 7.2 Gãy 2 — cùng một quan sát rơi vào hai lớp, và taxonomy không phân xử được

Đã trình bày ở §6.3: "background vắng mặt" đồng thời là L2.1 Coverage (frame nguồn) và L3.2 slide_type conditioning (frame brief).

**INFERENCE:** nguyên nhân sâu hơn là **reference frame là thuộc tính của CÂU HỎI, không phải của QUAN SÁT**. Cùng một số đo được đọc dưới nhiều frame. Một taxonomy phân loại **dimension** theo frame vì thế không thể phân loại **quan sát** — mà evaluation harness thì làm việc trên quan sát. Đây là một giới hạn cấu trúc, không sửa được bằng cách thêm lớp.

### 7.3 Gãy 3 — L4 giả định một chuẩn trình bày duy nhất, còn `slide_type` bác bỏ đúng giả định đó

L4 "Presentation Quality" được định nghĩa là so với *"chuẩn trình bày — nội tại"*, gồm L4.2 "Decomposition & density — chia slide hợp lý, **mật độ chữ vừa phải**".

**INFERENCE:** "vừa phải" **theo mode nào?** Toàn bộ tiền đề của `slide_type` là mật độ đúng **phụ thuộc mode**. Một deck bị L4.2 chấm "quá dày" có thể đang hoàn toàn đúng spec `catchup`. Vậy L4 **không** độc lập với L3 như taxonomy giả định — nó bị điều kiện hóa bởi L3.2.

**Hệ quả cụ thể:** nếu evaluation harness chấm L4.2 mà không biết `meta.slide_type`, nó sẽ tạo ra một **sức ép ngược chiều** với FR-20: mọi cải tiến làm 3 mode khác nhau rõ hơn sẽ làm ít nhất một mode "tệ hơn" theo L4.2. Đây là một mâu thuẫn thiết kế thật, phát hiện được nhờ đúng việc stress-test.

**Ghi nhận cân bằng:** ba chỗ gãy này **không bác bỏ** taxonomy. Phần L0 (gate) và L1/L2 vẫn đứng vững, và trực giác "frame dự đoán method" vẫn đúng ở phần lớn trường hợp. Điều chúng nói là: taxonomy **chưa đủ** để tổ chức RQ03, và **không nên freeze vào `06_design/`** cho tới khi có chỗ cho reference frame thứ tư và cho quan hệ điều kiện L3 → L4. Nhất quán với disposition HOLD của G-03.

---

## 8. Vị trí của signal ở tầng ContentPlanner (G-04) — diagnostic, KHÔNG phải evidence kiến trúc

**G-04 (ACCEPT có phân vai):** được đề xuất signal ở tầng planner, nhưng phải ghi rõ đó là **diagnostic signal cho development**, không phải evidence chứng minh giá trị kiến trúc.

**RECOMMENDATION.** Vì G-14 đã yêu cầu **plan/outline artifact phải persist**, có thể tính **đúng bộ feature §4.A/4.B trên plan artifact**, song song với trên deck cuối. Không tạo điểm số mới; chỉ tính lại cùng bộ feature ở một điểm khác trong pipeline.

Giá trị chẩn đoán khi contrast ở deck cuối **không đạt**:

| Contrast ở plan | Contrast ở deck | Chẩn đoán |
| :-: | :-: | --- |
| ✗ | ✗ | Planner **không hề dùng** `slide_type`. Sửa ở ContentPlanner |
| ✓ | ✗ | Planner có phân biệt nhưng **bước sau xóa mất** (DesignSystem / Exporter chuẩn hóa quá tay). Sửa ở downstream — **và đây là loại lỗi cực khó tìm nếu chỉ nhìn deck cuối** |
| ✗ | ✓ | Khác biệt sinh ra ở đâu đó **ngoài** planner → mâu thuẫn với kiến trúc đã mô tả, cần điều tra |

**Ghi rõ theo yêu cầu G-04:** bảng trên là **công cụ debug**, dùng để định vị chỗ hỏng trong development. Nó **KHÔNG** là evidence rằng ContentPlanner đóng góp giá trị. Evidence đó đến từ **end-to-end + ablation** (RQ08), và RQ03 **không** đề xuất bất kỳ "ContentPlanner Score" nào. Cụ thể: nếu contrast ở plan đạt mà deck cuối tệ, điều đó **không** chứng minh planner tốt — nó chỉ chỉ ra chỗ nên nhìn tiếp.

---

## 9. Bốn finding của landscape mà `recommendation.md` dựa vào

1. **FR-02 chứa hai mệnh đề khác loại** — "có khác biệt" là mệnh đề **quan hệ giữa nhiều output** (đo được bằng F1 + thống kê, không cần judge); "có chủ đích" là mệnh đề về **ý định** (cần một spec khai báo trước + human validate một lần). Gộp chúng vào một điểm số sẽ mất cả hai.
2. **Precedent tồn tại và nó cảnh báo, không trấn an.** X+Slides — nguồn E1 gần nhất — đo được audience conditioning **đi sai hướng** ở một hệ thống thật (target advantage −0.117) và kết luận prompt *"do not consistently align with the intended profile"*. Hiệu ứng conditioning nói chung **nhỏ**.
3. **Phần lớn câu hỏi của RQ03 KHÔNG cần judge structure.** Cảnh báo Wave 1 (PPTEval Coherence 0.55; PresentBench ρ=0.532 vs trần 0.664; JudgeSense: coherence bất ổn nhất) áp cho việc **chấm điểm chất lượng cấu trúc**. RQ03 tránh được phần lớn vùng đó bằng cách chuyển câu hỏi thành **contrast deterministic + phân loại forced-choice**, hai thứ mà evidence hiện có **không** nói là judge yếu.
4. **Không có ground truth "cấu trúc đúng", và không cần có** — nhưng **phải có một bảng hướng kỳ vọng do team khai báo trước**, vì literature không định nghĩa `teaching | catchup | speaker_support`. Đây là chỗ duy nhất trong RQ03 mà một quyết định của con người là **bắt buộc và không thay thế được**.
