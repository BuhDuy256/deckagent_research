# Knowledge Roadmap — tự xây một sample-deck agent

**Mục tiêu của file này:** từ toàn bộ research hiện có trong repository, xác định **cần hiểu knowledge nào, theo thứ tự nào, đọc paper nào, và lấy gì từ mỗi paper** để từng bước đủ nền tảng tự thiết kế và implement một sample-deck agent có capability tương tự OpenDesign / Claude Design / PPTAgent.

**Đi kèm:** `paper_inventory.md` (kiểm kê 97 paper). File này **không** lặp lại inventory — nó tổ chức lại theo **dependency của kiến thức**.

**Giả định về người đọc:** chưa có kiến thức nền về lĩnh vực này. Mọi thuật ngữ mới được giải thích ở lần xuất hiện đầu tiên. Nếu bạn thấy một từ chưa được giải thích, đó là lỗi của file này.

---

## Đọc trước: ba điều về hình dạng của roadmap này

**1. Bộ paper trong repo là corpus về ĐÁNH GIÁ deck, không phải về SINH deck.**
49% số paper thuộc nhóm "làm sao đo chất lượng". Chỉ 3 paper nói về nguyên lý thiết kế slide cho con người, và **0 paper** dạy cách *sinh* layout. Đây là ràng buộc thật, không sửa được bằng cách sắp xếp lại.

Hệ quả: roadmap này dạy bạn **hiểu và đo** một deck rất sâu, dạy bạn **planning và grounding** tốt, nhưng tới phần **layout/typography/visual generation** thì paper hết và bạn phải sang engineering knowledge bên ngoài. Tôi đánh dấu chính xác chỗ đó ở §6 (Knowledge gaps) thay vì giả vờ paper có sẵn.

**2. Thứ tự "học đo trước, học sinh sau" là cố ý, không phải hệ quả bị động của corpus.**
Lý do nhân quả: mọi cơ chế cải thiện chất lượng trong một agent — self-critique, iterative refinement, reward, retry — đều là **một vòng lặp quanh một phép đo**. Nếu phép đo sai, vòng lặp đẩy hệ thống đi sai hướng nhanh hơn. Bạn không thể xây self-critique loop trước khi biết critique thế nào là đáng tin. Đây là dependency thật, và nó tình cờ khớp với thế mạnh của corpus.

**3. Bốn nhãn dùng xuyên suốt file** (theo `CLAUDE.md`):
- `PROJECT SOURCE` — điều một tài liệu nội bộ của dự án nói.
- `EXTERNAL EVIDENCE` — điều một nguồn ngoài verify được support, có citation.
- `INFERENCE` — kết luận do tôi nối evidence với ngữ cảnh; **là suy luận, không phải sự thật đã chứng minh**.
- `RECOMMENDATION` — hành động đề xuất; truy được `vấn đề → evidence → lý lẽ → đề xuất`.

Mọi thông tin về paper trong file này **kế thừa nguyên trạng thái verify** mà `evidence.md` ghi. Chỗ nào repo ghi "chưa đọc bản gốc", tôi giữ nguyên cảnh báo đó.

---

# Phase 0 — Decompose bài toán trước khi làm roadmap

## 0.1 Ba hệ thống tham chiếu KHÔNG cùng một bài toán

Trước khi liệt kê capability, phải nói rõ điều này, vì đây là chỗ dễ sai nhất khi mới vào lĩnh vực: **ba hệ thống trong đề bài không giải cùng một bài toán**, nên "capability của một sample-deck agent" không phải là hợp của ba danh sách.

| | **OpenDesign** (`nexu-io/open-design`) | **Claude Design** | **PPTAgent** |
| --- | --- | --- | --- |
| **Trạng thái evidence trong repo** | `EXTERNAL EVIDENCE` E1 — repo + site + docs official, có file research riêng | ⚠ **Gần như không có.** Chỉ được nhắc gián tiếp một lần | `EXTERNAL EVIDENCE` E1 — paper EMNLP 2025 + repo |
| **Input** | Text brief mô tả nhu cầu thiết kế (+ design system/template tùy chọn) | Không xác định rõ từ evidence hiện tại | **Một presentation tham chiếu có sẵn** + nội dung |
| **Output chính** | `index.html` (deck HTML single-file); `slides.json` là **secondary**, sinh ra để export PPTX | Không xác định rõ | PPTX qua thao tác chỉnh sửa deck tham chiếu |
| **Vị trí của representation trung gian** | **HTML-first** — bản thật là HTML, JSON là phụ phẩm | Không xác định rõ | Schema/Structure nội bộ phục vụ việc edit |
| **Có evaluation công bố?** | **Không** — chỉ có thư mục `e2e/`, không có benchmark/metric chất lượng deck | Không xác định rõ | **Có** — PPTEval (3 chiều) + Success Rate |
| **Có agent/tool orchestration?** | Có — skill theo convention `SKILL.md`, MCP server, spawn 20+ coding-agent CLI, BYOK proxy | Không xác định rõ | Có ở mức pipeline component + self-correction |

⚠ **Về "PPTXAgent" trong đề bài:** repo không có entity nào tên chính xác như vậy. Entity gần nhất và gần như chắc chắn là thứ được nhắc tới là **`PPTAgent`** (arXiv 2501.03936, EMNLP 2025), xuất hiện ở 4/5 file evidence với tag MUST READ. Tôi dùng tên `PPTAgent` xuyên suốt vì đó là tên verify được, và ghi nhận khác biệt tên ở đây thay vì âm thầm gộp. Xem `paper_inventory.md` Appendix C.

⚠ **Về Claude Design:** repo **không có research nào** về nó. Nó chỉ xuất hiện trong một câu mô tả Open Design là *"open-source alternative cho Claude Design (công cụ design đóng của Anthropic)"*. Vì vậy **mọi phát biểu về capability của Claude Design trong file này đều là `INFERENCE` từ vị thế "công cụ design đóng mà OpenDesign nhắm thay thế", không phải evidence.** Đây tự nó là một knowledge gap của repo (§6, Gap 9).

### Điều rút ra được — và tại sao nó quyết định toàn bộ roadmap

Khác biệt **input** giữa ba hệ thống không phải chi tiết nhỏ. Nó thay đổi bài toán ở mức gốc:

- **Input = brief** (OpenDesign): agent **được phép sáng tạo nội dung**. Không có "sự thật gốc" để đối chiếu. → **Content fidelity không phải bài toán của họ.** (`EXTERNAL EVIDENCE`, `opendesign.md` §Điểm khác quan trọng)
- **Input = tài liệu nguồn dài** (Deck Agent, và là setting "long-document" của UniPPTBench): agent **bị cấm sáng tạo nội dung**. Mọi claim phải truy được về nguồn. → Toàn bộ Stage 2 của roadmap này tồn tại vì lý do đó.
- **Input = deck tham chiếu** (PPTAgent): agent **không phải nghĩ ra layout** — nó tái sử dụng layout đã có và chỉ thay nội dung. → Đây là lý do PPTAgent đạt chất lượng design cao mà không cần giải bài toán layout generation.

`INFERENCE` **quan trọng nhất của Phase 0:** nếu bạn muốn xây một agent "tương tự cả ba", bạn đang muốn một hệ thống mà **không hệ thống nào trong ba** đang làm — sinh từ tài liệu nguồn dài (như Deck Agent), với chất lượng design tự thân (như OpenDesign), và có evaluation thật (như PPTAgent). Biết điều đó **trước khi** bắt đầu quan trọng hơn mọi paper trong roadmap, vì nó cho bạn biết chỗ nào bạn có thể mượn, chỗ nào bạn phải tự giải.

---

## 0.2 Capability decomposition

Dưới đây là phân rã **do tôi tự xây dựa trên research thực tế trong repo** (không copy danh sách gợi ý trong đề bài). Nguyên tắc phân rã: **mỗi capability là một thứ có thể hỏng độc lập và có thể đo độc lập.** Nếu hai thứ luôn hỏng cùng nhau thì chúng là một capability.

Nhóm theo dòng chảy dữ liệu:

### Nhóm 1 — Từ yêu cầu và nguồn, tới nội dung

| # | Capability | Nó là gì (giải thích cho người mới) | Hỏng thì trông như thế nào |
| --- | --- | --- | --- |
| **C1** | **Spec intake** — hiểu và cấu trúc hoá yêu cầu | Biến một câu người dùng gõ ("làm deck 10 slide cho buổi họp 15 phút") thành các trường máy đọc được: chủ đề, số slide, thời lượng, đối tượng, mục đích | Deck 30 slide khi user xin 10 |
| **C2** | **Source ingestion & document understanding** | Đọc PDF/Word/web/video, chuẩn hoá về một dạng chung có text, ảnh, và **cấu trúc** (section nào, thứ tự nào) | Mất bảng biểu, mất thứ tự section, ảnh rớt |
| **C3** | **Salience — chọn cái gì đáng lên slide** | Từ 30 trang nguồn, quyết định ý nào quan trọng. Đây **không phải** tóm tắt: tóm tắt cố giữ nhiều thông tin, slide cố giữ **ít** thông tin nhưng đúng thứ đáng giữ | Deck đúng từng chữ nhưng bỏ mất kết luận chính |
| **C4** | **Grounding & fidelity** — không bịa, không nói sai | Mọi claim trên slide phải có counterpart trong nguồn. Gồm 3 lỗi **khác nhau**: bịa (không có trong nguồn), sai (có nhưng bị nói lệch), thiếu (có và quan trọng nhưng không lên slide) | Bullet ghi "+51% YoY" trong khi nguồn ghi 41% |

### Nhóm 2 — Từ nội dung, tới cấu trúc deck

| # | Capability | Nó là gì | Hỏng thì trông như thế nào |
| --- | --- | --- | --- |
| **C5** | **Deck-level planning (narrative)** | Quyết định deck có mấy phần, thứ tự nào, mạch dẫn dắt ra sao. "Narrative" ở đây = trình tự các ý sao cho người nghe hiểu được ý sau nhờ ý trước | 12 slide đúng nội dung nhưng nhảy cóc, không ai theo được |
| **C6** | **Slide-level planning** | Quyết định *một* slide chứa ý gì, vai trò gì (mở đầu / dữ liệu / so sánh / kết luận), bao nhiêu bullet | Một slide nhồi 3 ý lớn; slide khác chỉ có 1 câu |
| **C7** | **Purpose/audience conditioning** | Cùng một nguồn, đổi mục đích sử dụng thì deck phải khác **có chủ đích** (dạy học ≠ đọc một mình ≠ hỗ trợ người nói) | Đổi tham số nhưng deck ra gần như y hệt |

### Nhóm 3 — Từ cấu trúc, tới hình

| # | Capability | Nó là gì | Hỏng thì trông như thế nào |
| --- | --- | --- | --- |
| **C8** | **Structured representation (IR)** | Một cấu trúc dữ liệu trung gian mô tả deck **độc lập với định dạng xuất**. IR = Intermediate Representation | Sửa một chỗ trong code export làm hỏng logic nội dung |
| **C9** | **Layout selection & assignment** | Chọn khuôn bố cục cho từng slide (tiêu đề + bullet? hai cột? ảnh lớn + chú thích?) và đặt element vào | Text tràn ra ngoài khung, hai hộp chồng lên nhau |
| **C10** | **Design system & consistency** | Bộ quy tắc dùng chung cho cả deck: cỡ chữ, font, bảng màu, khoảng cách. "Design token" = tên đặt cho một giá trị thiết kế (`color.primary`) để mọi nơi tham chiếu cùng một chỗ | Slide 3 dùng font khác slide 4; 5 sắc xanh khác nhau |
| **C11** | **Visual asset selection/generation** | Chọn hoặc tạo ảnh, biểu đồ, sơ đồ cho slide | Ảnh không liên quan; bảng số đáng lẽ là biểu đồ |
| **C12** | **Rendering & executability** | Biến IR thành file thật và **file đó mở được** | Chương trình sinh ra crash; PPTX không mở được |
| **C13** | **Editability** | Người dùng mở file ra và sửa được như file do người làm: text là text, hình là vector, layout kế thừa từ master | Slide là một tấm ảnh phẳng — sửa một chữ cũng không được |

### Nhóm 4 — Vòng lặp và hạ tầng

| # | Capability | Nó là gì | Hỏng thì trông như thế nào |
| --- | --- | --- | --- |
| **C14** | **Self-critique & iterative refinement** | Agent tự xem lại output của mình, phát hiện lỗi, sửa | Lặp 3 vòng mà deck không tốt lên, chỉ tốn tiền |
| **C15** | **Agent/tool orchestration** | Điều phối nhiều bước, nhiều tool, giữ state, tôn trọng phần user đã khoá | Editor Loop sửa slide 2 làm hỏng slide 5 user đã chốt |
| **C16** | **Evaluation** | Đo chất lượng deck một cách lặp lại được | Không biết bản mới tốt hơn hay xấu hơn bản cũ |
| **C17** | **Reproducibility & variance control** | Biết hệ thống dao động bao nhiêu khi chạy lại cùng input | Ăn mừng vì điểm +0,05 mà thực ra là nhiễu |

---

## 0.3 Capability nào là core, capability nào là enhancement?

Tiêu chí phân loại (`RECOMMENDATION`, có lý do): **core = bỏ nó đi thì sản phẩm không còn là "agent sinh deck từ tài liệu" nữa, hoặc nó là chỗ AI-quality thật sự nằm.** Enhancement = bỏ đi thì sản phẩm vẫn đúng chức năng, chỉ kém hấp dẫn hoặc kém tiện.

### CORE (7)

| Capability | Vì sao core |
| --- | --- |
| **C2 Source ingestion** | Không đọc được nguồn thì không có gì để làm. Nhưng lưu ý: phần lớn là **engineering correctness**, không phải AI-quality (`PROJECT SOURCE`, `SYSTEM_MAP.md` gọi adapter/exporter là "đọc file đúng / mở file được") |
| **C3 Salience** | Đây là chỗ AI thật sự tạo giá trị. Một script cắt đoạn cũng "sinh được deck"; chọn *đúng ý* mới là phần khó |
| **C4 Grounding & fidelity** | Với input là tài liệu nguồn, đây là ràng buộc định nghĩa bài toán. `EXTERNAL EVIDENCE`: UniPPTBench thấy *"strong performance on generic presentation-quality metrics does not necessarily imply strong task fulfillment in grounded scenarios"* — tức làm đẹp không cứu được grounding |
| **C5 + C6 Planning** | `PROJECT SOURCE` gọi ContentPlanner là lõi. `EXTERNAL EVIDENCE` củng cố gián tiếp: PPTAgent ablation cho thấy bỏ Outline làm coherence tụt 4,48 → 3,36 và bỏ Structure → 3,45 |
| **C8 IR** | Không có IR thì mọi capability khác dính chặt vào nhau và không test riêng được thứ gì |
| **C12 Rendering & executability** | `EXTERNAL EVIDENCE` mạnh: AutoPresent gán **điểm 0** cho slide không execute được, và execution rate dao động 2,1%–89,2% giữa các model → đây là **biến chi phối**, không phải chi tiết |
| **C16 Evaluation** | Không phải vì "đo lường là tốt", mà vì C14 (self-critique) là một vòng lặp quanh phép đo. Không có C16 thì C14 không tồn tại được |

### CORE-CÓ-ĐIỀU-KIỆN (2) — core với *bài toán này*, không phải với mọi deck agent

| Capability | Điều kiện |
| --- | --- |
| **C9 Layout** | Core **nếu** bạn sinh layout từ đầu. **Không** core nếu bạn đi đường PPTAgent (tái dùng deck tham chiếu) hoặc đường template catalog (OpenDesign: *"populate slide từ layout catalog"*). Đây là **quyết định kiến trúc lớn nhất** của cả dự án — xem Stage 6 |
| **C13 Editability** | Core nếu sản phẩm hứa "xuất PPTX sửa được". Không core nếu output là ảnh/PDF. `EXTERNAL EVIDENCE`: SlidesGen-Bench PEI cho thấy đây là **thang 6 mức**, không phải có/không |

### ENHANCEMENT (8)

| Capability | Vì sao enhancement — và cảnh báo |
| --- | --- |
| **C1 Spec intake** | Ở mức tối thiểu là parse vài trường. Trở nên khó khi phải suy ra intent ẩn |
| **C7 Conditioning** | Sản phẩm chạy được mà không có nó. ⚠ Nhưng `EXTERNAL EVIDENCE` cảnh báo đây là enhancement **khó bất thường**: X+Slides đo thấy conditioning có thể **thất bại theo hướng ngược** (target advantage −0,117) và khi thành công chỉ ~+0,08 |
| **C10 Design system** | Nếu dùng một design system cố định do người thiết kế sẵn thì phần lớn "design consistency" là thuộc tính của **design system**, chứ không phải của AI (`INFERENCE`, khớp ghi chú RQ01 về SlidesGen-Bench) |
| **C11 Visual assets** | Bỏ được. `EXTERNAL EVIDENCE` gián tiếp: Garner 2009 đo thấy **dưới một nửa** slide trong deck học thuật thật có hình |
| **C14 Self-critique** | Bỏ được ở v1. `EXTERNAL EVIDENCE` mạnh chống việc làm sớm: SlideAudit đo LLM phát hiện lỗi design chỉ F1 0,331–0,655 |
| **C15 Orchestration** | Một pipeline tuần tự cứng chạy được. Agent-native là lựa chọn kiến trúc, không phải điều kiện cần |
| **C17 Variance control** | Không cần để **chạy**; cần để **biết mình có tiến bộ không**. Bỏ nó thì bạn build mù |

### Câu trả lời ngắn cho "nếu muốn xây hệ thống tương tự"

`RECOMMENDATION`: **Đường ngắn nhất tới một sample-deck agent thật là C2 → C3 → C5/C6 → C8 → C12, cộng C4 nếu input là tài liệu nguồn, cộng một phép đo tối thiểu của C16.** Layout (C9) nên **né** ở v1 bằng cách mượn template catalog thay vì sinh layout — vì đó là capability duy nhất trong danh sách mà **corpus paper hiện có không dạy được bạn** (xem §6, Gap 1). Mọi thứ còn lại là enhancement, và enhancement khó nhất không phải visual mà là **C7 conditioning**, vì hiệu ứng của nó nhỏ hơn nhiễu tự nhiên của hệ thống.

---

# ROADMAP — 10 Stage theo dependency của kiến thức

Mỗi Stage là **một bước tiến về khả năng suy nghĩ**, không phải một nhóm paper. Tên Stage mô tả *điều bạn làm được sau khi qua nó*.

```
S0 Bài toán này là gì và đã ai giải thế nào
S1 "Deck tốt" nghĩa là gì — và nó gồm mấy trục độc lập
S2 Từ nguồn tới nội dung: salience + ba failure mode
S3 Từ nội dung tới storyline: deck-level & slide-level planning
S4 Cùng nguồn, khác mục đích: conditioning và cách chứng minh nó hoạt động
S5 Representation: cái gì nằm giữa plan và pixel
S6 Layout & visual: cái gì đo được bằng hình học, cái gì không
S7 Self-critique: cho agent tự sửa mình mà không tự làm hỏng mình
S8 Evaluation như một hạng mục kỹ thuật: judge, checklist, độ tin cậy
S9 So sánh và ablation: làm sao biết hệ thống thật sự tiến bộ
```

---

## Stage 0 — `Bài toán "tài liệu → deck" là gì, và đã có mấy cách giải`

### 1. Tôi đang cố hiểu vấn đề gì?

Câu hỏi tưởng đơn giản: "cho một tài liệu, sinh ra một bộ slide". Nhưng ngay câu đó đã giấu **ba lựa chọn kiến trúc** mà nếu không nhìn thấy thì bạn sẽ chọn nhầm mà không biết mình đang chọn.

**Lựa chọn 1 — Input là gì?** Ba chế độ khác hẳn nhau:
- *brief* (vài câu mô tả ý muốn) → agent được sáng tạo nội dung;
- *tài liệu nguồn dài* (PDF 30 trang) → agent bị cấm sáng tạo nội dung;
- *deck tham chiếu* (một file PPTX có sẵn để bắt chước) → agent không phải nghĩ ra layout.

**Lựa chọn 2 — Output được sinh ra dưới dạng gì?** Đây là chỗ mới nhất với người ngoài ngành. Có ít nhất bốn cách:
- sinh **ảnh** trực tiếp (model vẽ ra một tấm ảnh trông giống slide);
- sinh **chương trình** (model viết code, chạy code ra file slide) — gọi là *program generation* hoặc *code generation*;
- sinh **HTML** rồi coi HTML là bản gốc;
- sinh một **cấu trúc dữ liệu** rồi để một bộ export riêng biến nó thành file.

**Lựa chọn 3 — Chất lượng được định nghĩa như thế nào?** Có "giống deck mẫu" (*reference-based*) và "tự thân nó tốt" (*reference-free*). Hai định nghĩa này cho kết luận **ngược nhau** trên cùng một deck.

### 2. Tại sao tôi cần biết điều này?

Vì ba lựa chọn trên **quyết định capability nào bạn phải tự giải và capability nào bạn được miễn**. Cụ thể:

- Nếu bạn chọn input = deck tham chiếu, bạn **được miễn toàn bộ Stage 6** (layout). PPTAgent làm đúng vậy.
- Nếu bạn chọn output = ảnh, bạn **mất toàn bộ C13** (editability) và không bao giờ lấy lại được — deck của bạn vĩnh viễn ở mức thấp nhất của thang PEI.
- Nếu bạn chọn định nghĩa chất lượng = reference-based mà không có deck mẫu do người làm, bạn **đang đo một thứ không tồn tại**.

Nói cách khác: **Stage 0 không dạy bạn làm gì cả — nó dạy bạn nhận ra mình đang đứng ở nhánh nào của cây quyết định.** Không có nó, bạn sẽ đọc một paper rồi cố áp kiến trúc của nó vào bài toán của mình mà không nhận ra hai bài toán khác nhau ở gốc.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn đọc PPTAgent, thấy nó đạt PPTEval 3,67 và Success Rate 95%, kết luận "kiến trúc này tốt, làm theo". Bạn implement, cho nó một PDF 30 trang, và nó không chạy — vì PPTAgent **cần một presentation tham chiếu làm input** mà bạn không có. Bạn mất hai tuần để nhận ra điều mà một dòng trong `RQ08/evidence.md` đã ghi: *"PPTAgent cần một reference presentation làm input, nên không phải hệ thống cùng bài toán với Deck Agent."*

Phiên bản tệ hơn: bạn *có* làm nó chạy bằng cách tự cấp một deck mẫu bất kỳ, rồi so điểm với hệ thống của mình và kết luận "kiến trúc của tôi kém hơn". Kết luận đó vô nghĩa vì hai hệ thống nhận input khác nhau.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Phân biệt **ba chế độ input** và biết chế độ nào **loại bỏ** hoàn toàn nhu cầu về content fidelity.
2. Phân biệt **program generation** với **direct image generation**, và biết vì sao lựa chọn này quyết định trần editability.
3. Phân biệt **reference-based** với **reference-free** evaluation, và biết vì sao reference-based không phân biệt được "khác" với "kém".
4. Biết rằng **"deck có sinh ra được không"** là một biến riêng, phải báo cáo tách khỏi "deck có đẹp không".
5. Đọc được một paper slide-generation bất kỳ và trong 5 phút trả lời được: input gì, output dạng gì, đo bằng cách nào, và **nó có cùng bài toán với mình không**.

### 5. Papers cần đọc

#### MUST-READ

##### `PPTAgent: Generating and Evaluating Presentations Beyond Text-to-Slides`

**Tại sao đọc paper này ở Stage này?**
Đây là paper duy nhất trong corpus chứa **cả ba thứ trong một bài**: một kiến trúc sinh deck có component tách rời, một định nghĩa "thành công" vận hành được, và một bộ đo. Đọc nó trước cho bạn một **anchor** (mỏ neo) để mọi paper sau này gắn vào: khi đọc paper khác bạn sẽ tự hỏi "chỗ này khác PPTAgent thế nào".

**Tôi cần lấy gì từ paper?**
- Kiến trúc ở mức **tên component**: Outline → Schema → Structure → CodeRender. Chưa cần hiểu từng cái làm gì chi tiết; cần thấy rằng **người ta tách nó ra**, và tách được nghĩa là test riêng được.
- Định nghĩa **Success Rate**: *"success requires the generation of all slides without execution errors after self-correction"*. Đây là câu quan trọng nhất của paper với người mới — nó cho bạn thấy "sinh được file" là một tiêu chí riêng, đo bằng đếm, không cần judge.
- Ý tưởng rằng deck được chấm bằng cách **render thành ảnh** rồi đưa cho model nhìn, vì presentation *"cannot be easily comprehended in a plain text format"*.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua toàn bộ bảng số PPTEval và bảng ablation — chúng sẽ quay lại ở Stage 1 và Stage 9 với mục đích khác. Bỏ qua chi tiết thuật toán ghép nội dung vào slide tham chiếu. Bỏ qua phần human correlation (để Stage 8).

##### `AutoPresent: Designing Structured Visuals from Scratch`

**Tại sao đọc paper này ở Stage này?**
Nó là **đối trọng kiến trúc** của PPTAgent: không có deck tham chiếu, sinh từ đầu, và output là **chương trình**. Đọc hai paper này cạnh nhau là cách nhanh nhất để thấy "bài toán sinh deck" thực ra là một họ bài toán chứ không phải một bài toán.

**Tôi cần lấy gì từ paper?**
- Kết luận so sánh paradigm: **phương pháp sinh chương trình cho slide chất lượng cao hơn ở dạng người dùng còn tương tác được** so với sinh ảnh trực tiếp. Đây là lập luận nhân quả bạn cần nhớ: sinh ảnh làm mất khả năng sửa.
- **§3.3 Executability** — và đặc biệt là quy tắc: slide không execute được bị gán **điểm 0** ở cột Overall. Con số Execution% dao động 2,1% (model nhỏ) → 89,2% (model lớn) cho bạn cảm giác về mức độ nghiêm trọng.
- Cấu trúc **3 nhóm metric tách biệt** (§3): reference-based / reference-free / executability. Chưa cần công thức, chỉ cần thấy chúng là **ba câu hỏi khác nhau**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua công thức chi tiết của reference-based metric (element matching, CIEDE2000, position similarity) — Stage 1 sẽ quay lại. Bỏ qua chi tiết training model AutoPresent 8B. Bỏ qua SlidesBench dataset statistics.

#### SHOULD-READ

##### `DOC2PPT: Automatic Presentation Slides Generation from Scientific Documents`

**Tại sao đọc paper này ở Stage này?**
Vì nó là **điểm gốc lịch sử** — bài đầu tiên biến "làm slide từ tài liệu" thành một bài toán có dataset và có metric. Đọc nó cho bạn thấy lĩnh vực này bắt đầu từ đâu và **vì sao thế hệ metric đầu tiên thất bại**, đó là kiến thức nhân quả chứ không phải kiến thức lịch sử suông.

**Tôi cần lấy gì từ paper?**
Đúng hai điều: (a) tác giả tự thừa nhận *"no established evaluation metrics and baselines"* — tức lĩnh vực này sinh ra **không có sẵn thước đo**, và mỗi bài phải tự chế; (b) họ chọn **ROUGE-SL** (đo trùng lặp chữ với deck mẫu) và **mIoU** (đo trùng lặp hình học của layout với slide mẫu). Ghi nhớ hai cái tên này để Stage 1 hiểu vì sao chúng bị bác bỏ.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua toàn bộ kiến trúc model của họ (thế hệ pre-LLM, không chuyển giao được). Bỏ qua kết quả số.

##### `UniPPTBench: A Unified Benchmark for Presentation Generation Across Diverse Input Settings`

**Tại sao đọc paper này ở Stage này?**
Vì nó là nguồn duy nhất trong corpus **liệt kê tường minh các chế độ input** như một trục phân loại chính thức: vague-prompt / long-document / multimodal-document / multi-source. Đây chính là "Lựa chọn 1" ở mục 1 của Stage này, và có một paper đặt tên cho nó thì bạn dùng được cái tên đó về sau.

**Tôi cần lấy gì từ paper?**
- Bốn tên gọi của bốn setting, và ý tưởng **kiến trúc metric 2 tầng** (shared metric để so chéo + scenario-specific metric).
- Một câu kết luận: *"strong performance on generic presentation-quality metrics does not necessarily imply strong task fulfillment in grounded scenarios"*. Dịch sang tiếng người: **một deck đẹp và mạch lạc vẫn có thể sai hoàn toàn so với tài liệu nguồn, và metric chung sẽ không phát hiện ra.**

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua bảng xếp hạng hệ thống, bỏ qua chi tiết 5 metric của UniPPTEval. ⚠ Lưu ý trạng thái: RQ08 xếp nguồn này **DISCOVERY ONLY** (preprint chưa peer-review, code/data chưa xác nhận public) → đọc để lấy khung khái niệm, đừng trích số.

#### OPTIONAL / REFERENCE

- **`Enhancing Presentation Slide Generation by LLMs with a Multi-Staged End-to-End Approach` (DocPres)** — đọc nếu muốn thấy một pipeline document→slide nhiều tầng ở thế hệ trước PPTAgent. Lấy: ý tưởng gắn mỗi slide vào một section cụ thể của tài liệu. ⚠ **Không** lấy: cách họ đo "Coverage" bằng embedding similarity — Stage 2 sẽ giải thích vì sao cách đó không phát hiện được bỏ sót.
- **`Open Design` (repo, không phải paper)** — đọc `docs/skills-protocol.md` và file `03_research/external_systems/opendesign.md` trong repo này. Lấy: workflow deck thực tế của một sản phẩm đang chạy (*làm rõ topic + số slide → chọn theme → populate slide từ layout catalog → self-check theo rubric*) và contrast **HTML-first vs IR-first**. Đây là nguồn duy nhất cho bạn thấy một hệ thống production trông thế nào.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao "input là brief" và "input là PDF 30 trang" là **hai bài toán khác nhau**, chứ không phải hai mức độ khó của cùng một bài toán.
- Vì sao một hệ thống sinh slide dưới dạng ảnh **không bao giờ** đạt được editability, dù model có mạnh đến đâu.
- Vì sao "deck có sinh ra được không" phải là một con số báo cáo riêng, không được trộn vào điểm chất lượng.
- Vì sao PPTAgent đạt design tốt mà không cần giải bài toán layout.

### Implementation checkpoint

**Đã có thể làm:** vẽ sơ đồ kiến trúc cho hệ thống của mình và **khoanh tròn ba lựa chọn** (input mode / output mode / định nghĩa chất lượng) kèm lý do cho từng lựa chọn. Viết được một script khung: đọc file → gọi model → sinh ra một file output → **kiểm tra file đó mở được không** (đây chính là Success Rate ở dạng đơn giản nhất, và nó đã là một metric thật).

**Chưa nên làm:** chưa nên viết ContentPlanner, chưa nên chọn schema cho IR, chưa nên đụng vào layout. Chưa đủ kiến thức để biết mình đang tối ưu cho cái gì.

---

## Stage 1 — `"Deck tốt" nghĩa là gì — và nó gồm mấy trục độc lập`

### 1. Tôi đang cố hiểu vấn đề gì?

Bạn có một deck. Nó tốt hay không tốt? Câu trả lời trực giác — "nhìn thì biết" — là thứ **không lập trình được** và cũng không dùng để cải tiến hệ thống được.

Vấn đề thật là: **"chất lượng deck" không phải một đại lượng.** Nó là nhiều đại lượng **độc lập nhau**, có thể đi ngược chiều nhau. Một deck có thể đẹp mà sai nội dung. Có thể đúng nội dung mà lộn xộn thứ tự. Có thể đúng và đẹp mà thiếu mất kết luận quan trọng nhất của tài liệu.

Thêm một tầng khó nữa mà người mới hay bỏ qua: các trục này **không cùng một cấp**. "Bố cục slide có bị chồng chữ không" là câu hỏi về **một slide**. "Mạch dẫn dắt có hợp lý không" là câu hỏi về **cả deck**. Gộp chúng vào một bảng điểm phẳng là sai về mặt cấu trúc.

### 2. Tại sao tôi cần biết điều này?

Vì **đây là stage quyết định bạn có debug được hệ thống của mình hay không.**

Cụ thể: giả sử deck của bạn "kém". Nếu bạn chỉ có một con số chất lượng, bạn không biết phải sửa component nào. Nếu bạn có các trục tách bạch, bạn đọc được:
- điểm **fidelity** thấp mà **coherence** cao → lỗi ở khâu trích xuất/grounding, không phải khâu planning;
- **coherence** thấp mà **fidelity** cao → lỗi ở planning, không phải ở model viết chữ;
- cả hai cao mà **layout** thấp → lỗi ở exporter/design system, không phải ở AI.

`EXTERNAL EVIDENCE` cho thấy điều này không phải lý thuyết: PPTAgent ablation đo được `w/o CodeRender` làm **sập Success Rate xuống 74,6%** trong khi PPTEval gần như **không đổi (3,66 so với 3,67)**. Nếu chỉ nhìn một metric tổng hợp, bạn sẽ kết luận component đó vô dụng — trong khi thực ra nó giữ cho hệ thống chạy được.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể — và đây là bẫy có thật, có số.**

Bạn quyết định đo chất lượng nội dung bằng ROUGE-L (đo trùng lặp chữ với một deck mẫu). Bạn chạy hai hệ thống. Hệ thống A đạt ROUGE-L 16,76; hệ thống B đạt 14,25. Bạn kết luận A tốt hơn và đổ effort vào A.

`EXTERNAL EVIDENCE` (PPTAgent Table 3): đó **chính xác** là hai con số của KCTV và PPTAgent. Và khi chấm bằng judge có validate với người, KCTV đạt Content **2,55** còn PPTAgent đạt **3,28**. Tức **ROUGE-L đi ngược chiều với chất lượng nội dung** trên chính cặp hệ thống này. Bạn vừa tối ưu ngược suốt cả kỳ.

Phiên bản thứ hai của cùng cái bẫy: bạn gộp mọi thứ thành một điểm trung bình. Deck của bạn có layout hoàn hảo và nội dung bịa 30%. Điểm trung bình vẫn "khá". Không ai phát hiện ra vấn đề cho tới khi bảo vệ.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Kể được **ít nhất 3 bộ phân rã chất lượng khác nhau** từ external work, và giải thích vì sao chúng khác nhau (3 chiều của PPTEval, 5 chiều của PresentBench, 3 chiều của SlidesGen-Bench).
2. Phân biệt **granularity**: trục nào chấm ở mức slide, trục nào chấm ở mức deck — và vì sao trộn chúng là sai.
3. Giải thích được vì sao **"content" là ba khái niệm** chứ không phải một: đủ (completeness) ≠ đúng (correctness) ≠ không bịa (fidelity).
4. Nhận ra khi nào một metric là **proxy kém**: biết định nghĩa reference-based, biết nó không phân biệt được "khác deck mẫu" với "kém hơn deck mẫu".
5. Biết rằng **ngay cả deck do người làm cũng không đạt điểm tuyệt đối** — tức tồn tại một *human ceiling* để đặt kỳ vọng.
6. Biết một danh sách **lỗi thiết kế cụ thể** (không phải "xấu" chung chung) và bắt đầu phân loại được lỗi nào máy kiểm được, lỗi nào không.

### 5. Papers cần đọc

#### MUST-READ

##### `PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation`

**Tại sao đọc paper này ở Stage này?**
Vì nó là nguồn **duy nhất** trong toàn bộ corpus tách rõ ba khái niệm nội dung mà mọi nguồn khác gộp lại. Nếu chỉ đọc một paper cho Stage 1, đọc cái này.

**Tôi cần lấy gì từ paper?**
- **Định nghĩa 5 chiều**, và đặc biệt là ranh giới giữa 3 chiều nội dung:
  - *Content Completeness* — "verifies coverage using an instance-specific, instruction-derived checklist" (**đủ chưa**);
  - *Content Correctness* — "instruction-mandated content is presented correctly, i.e., consistent with the provided background materials" (**đúng chưa**);
  - *Content Fidelity* — "any unsupported, contradictory, or newly introduced detail is treated as hallucinated" (**có bịa không**).
- Cách paper tự chia 5 chiều thành **material-independent** (2 chiều đầu — đánh giá được mà không cần nhìn tài liệu nguồn) vs **material-dependent** (3 chiều sau). Đây là một phân đôi rất hữu dụng khi thiết kế hệ thống đo.
- Một finding về hành vi người: khi rank nhanh, người ưu tiên *"readily perceivable cues such as structure, layout, and overall coherence"* hơn là verify fidelity → **human preference ≠ fidelity**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua toàn bộ judge protocol (mỗi item một call, localized evidence) — Stage 8 sẽ quay lại đúng chỗ đó. Bỏ qua bảng xếp hạng 8 hệ thống (Stage 9). Bỏ qua ablation về Spearman.

##### `PPTAgent` — **lần 2** (Stage 0 đọc để hiểu kiến trúc; ở đây đọc để hiểu bộ đo)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 (Stage 0) tôi đọc để trả lời "hệ thống này làm gì". Lần này tôi đọc để trả lời "**họ định nghĩa chất lượng thế nào, và họ chứng minh định nghĩa đó đáng tin bằng cách nào**". Đó là hai phần khác nhau của cùng một bài.

**Tôi cần lấy gì từ paper?**
- **Table 1** — ba tiêu chí PPTEval nguyên văn (Content / Design / Coherence).
- **§3 + §4.4** — chi tiết quan trọng nhất của lần đọc này: **Content và Design chấm ở mức slide, Coherence chấm ở mức presentation.** Đây là bằng chứng cụ thể cho luận điểm "các trục không cùng granularity".
- **§5.5 + Figure 7 + Table 3** — bằng chứng số rằng reference-based là proxy kém (tương quan PPL/FID với Content ≈ 0; nghịch lý ROUGE-L đã nói ở mục 3).
- Phần Limitations: model vẫn tạo *"design flaws, such as overlapping elements"* → **overlap là failure mode thật**, và nó đo được bằng hình học.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua Table 5 (human correlation theo chiều) — dành cho Stage 8. Bỏ qua bảng ablation — dành cho Stage 9.

##### `SlidesGen-Bench: Evaluating Slides Generation via Computational and Quantitative Metrics`

**Tại sao đọc paper này ở Stage này?**
Vì nó đưa vào một trục mà **không nguồn nào khác có**: **Editability**. Hai nguồn trên nói về nội dung và hình thức; nguồn này nói về *"deck này sau khi giao cho người dùng thì họ sửa được tới đâu"* — một chiều chất lượng hoàn toàn khác, và là chiều mà một sản phẩm thật sống chết vì nó.

**Tôi cần lấy gì từ paper?**
- **Thang PEI 6 mức** với **knockout rule** (fail mức dưới thì không được tính mức trên): L0 Static (ảnh phẳng) → L1 Patchwork (text sửa được, ảnh vẫn raster) → L2 Vector → L3 Structural (master-based, `<p:sldMaster>`, cho phép lan truyền layout toàn hệ thống) → L4 Parametric (table/chart là data object thật) → L5 Cinematic.
- Ý tưởng rằng 3 chiều của họ là **Content / Aesthetics / Editability**, tức editability được nâng lên ngang hàng với hai chiều kia.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua 4 công thức aesthetic deterministic (Harmony/Engagement/Usability/Visual Rhythm) — Stage 6. Bỏ qua QuizBank — Stage 2. ⚠ Lưu ý trạng thái verify không nhất quán trong repo: RQ01 xếp E1, RQ06 xếp UNVERIFIED. Đọc để lấy **khung khái niệm PEI**, verify trước khi trích số.

#### SHOULD-READ

##### `SlideAudit: A Dataset and Taxonomy for Automated Evaluation of Presentation Slides`

**Tại sao đọc paper này ở Stage này?**
Ba paper trên cho bạn các **trục** chất lượng. Paper này cho bạn **danh sách lỗi cụ thể** bên dưới một trục. Đây là bước từ "design quality" (không làm gì được) sang "Content Overflow, Misaligned Elements, Insufficient Contrast" (làm được ngay).

**Tôi cần lấy gì từ paper?**
Toàn bộ **taxonomy 5 nhóm / 27 category**, đặc biệt nhóm *Composition & Layout* (Cluttered Layout, Occluded Content, Unbalanced Space, **Content Overflow**, Misaligned Elements, Lack of Visual Hierarchy) và *Typography* (Inappropriate Font Sizing, Inconsistent Text Styling, Illegible Typeface, Poor Text Alignment). Vừa đọc vừa tự phân loại: lỗi nào **đo được bằng toạ độ và thuộc tính** (overflow, misalignment, contrast), lỗi nào **đòi thẩm mỹ** (mismatched color combinations).

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua bảng F1 của LLM detection (0,331–0,655) — nó cực kỳ quan trọng nhưng thuộc Stage 6 và Stage 7, nơi bạn quyết định **ai** đi tìm lỗi. Bỏ qua chi tiết cách họ synthesize 1.800 slide lỗi.

##### `AutoPresent` — **lần 2** (Stage 0 đọc để hiểu paradigm; ở đây đọc để lấy tiêu chí)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 tôi đọc §3.3 để hiểu executability như một khái niệm. Lần này tôi đọc **§3.1 và §3.2** để lấy **nội dung cụ thể của tiêu chí** — tức các câu định nghĩa mà tôi có thể dùng lại làm rubric cho hệ thống của mình.

**Tôi cần lấy gì từ paper?**
- **Table 1** — 4 tiêu chí reference-free nguyên văn (Text / Image / Layout / Color), ví dụ *Layout*: "Elements should be aligned, do not overlap, and have sufficient margins to each other. All elements should not exceed the page." Câu này gần như là một đặc tả kỹ thuật.
- **Table 3** — con số human ceiling: reference slide **do người làm** đạt text 59,7 / image 81,5 / layout 73,5 / color 65,7. Ghi nhớ: kỳ vọng đúng không phải 100.
- Lời tự cảnh báo của tác giả về reference-based: điểm thấp *"could come from differences in text, color, and positions, or derivative errors caused by the inaccurate element-matching process"* → **reference-based không phân biệt được "khác" với "kém"**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết công thức CIEDE2000 và position similarity trừ khi bạn thực sự sẽ implement reference-based (mà bạn không nên, nếu chưa có deck mẫu do người làm).

#### OPTIONAL / REFERENCE

- **`Paper2Poster`** — đọc **chỉ vì PaperQuiz**: sinh câu hỏi từ tài liệu gốc, rồi cho model **chỉ đọc poster** và trả lời. Đây là một trục chất lượng khác hẳn — *extrinsic*, đo **hiệu quả truyền đạt** chứ không đo hình thức. Bỏ qua phần Visual Quality (poster = 1 canvas, không chuyển sang deck được). ⚠ Repo không đọc được full PDF; breakdown 6 tiêu chí Holistic **chưa quote-verified**.
- **`DOC2PPT`** — quay lại 5 phút để thấy ROUGE-SL/mIoU thuộc nhóm nào trong phân loại bạn vừa học, và vì sao chúng bị thay thế.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao một điểm số chất lượng duy nhất **làm mất khả năng debug** hệ thống, kèm ví dụ `w/o CodeRender` (Success Rate sập, PPTEval không đổi).
- Sự khác nhau giữa *completeness* / *correctness* / *fidelity*, và cho ví dụ một deck **đạt fidelity 100% nhưng completeness rất thấp** (chép đúng nhưng chép nhầm phần không quan trọng).
- Vì sao ROUGE cao có thể đi kèm chất lượng thấp.
- Editability là gì và vì sao nó có **thang bậc** chứ không phải có/không.
- Đọc một bảng lỗi thiết kế và chia được thành "kiểm bằng code" vs "cần phán đoán".

### Implementation checkpoint

**Đã có thể làm:** viết **quality spec** cho hệ thống của mình — một bảng liệt kê các trục chất lượng, mỗi trục ghi rõ *chấm ở mức slide hay mức deck*, *material-dependent hay không*, và *đo bằng code hay bằng phán đoán*. Đây là artifact có giá trị thật, và nó là input trực tiếp cho mọi Stage sau. Ngoài ra: viết được vài check deterministic đầu tiên trên file output (đếm slide, phát hiện text tràn khung, phát hiện bounding box chồng nhau) — Stage 6 sẽ làm sâu, nhưng khung đã dựng được từ đây.

**Chưa nên làm:** chưa nên implement LLM judge (chưa biết nó lệch ở đâu — Stage 8). Chưa nên implement metric fidelity (chưa hiểu ba failure mode — Stage 2). Chưa nên tin bất kỳ điểm số nào bạn tự đo, vì chưa biết noise floor (Stage 9).

---

## Stage 2 — `Từ nguồn tới nội dung: salience và ba failure mode`

### 1. Tôi đang cố hiểu vấn đề gì?

Bạn có 30 trang PDF và cần ra 10 slide, mỗi slide 3–4 dòng. Tức bạn phải **vứt đi ~95% nội dung**. Câu hỏi là: vứt cái nào, giữ cái nào, và làm sao biết mình giữ đúng?

Ba khái niệm mới cần tách bạch ngay:

- **Salience** (tầm quan trọng) — thuộc tính của một ý *trong tài liệu nguồn*: ý này có đáng lên slide không. Đây **không phải** tóm tắt. Tóm tắt cố nén nhiều thông tin nhất có thể; slide cố giữ **ít** thông tin nhưng đúng thứ quyết định.
- **Grounding** (neo vào nguồn) — mọi thứ trên slide phải có counterpart trong nguồn.
- **Ba failure mode khác nhau**, và chúng khác nhau thật:
  - **bịa** (*hallucination*) — trên slide có, trong nguồn không có;
  - **nói sai** (*incorrectness*) — trong nguồn có, nhưng slide diễn đạt lệch đi;
  - **bỏ sót** (*coverage gap*) — trong nguồn có và quan trọng, nhưng slide không nhắc.

Thêm một khái niệm kỹ thuật bạn sẽ gặp liên tục: **atomic fact** — một mệnh đề chỉ chứa đúng một thông tin, để có thể trả lời đúng/sai bằng nhị phân. Ví dụ câu "Doanh thu tăng 51% trong năm 2025 nhờ mở rộng thị trường" chứa **ba** atomic fact.

### 2. Tại sao tôi cần biết điều này?

Vì **ba failure mode trên cần ba cơ chế đo khác nhau, chạy theo hai chiều ngược nhau** — và nếu không biết điều đó, bạn sẽ xây một cơ chế và tưởng nó bắt được cả ba.

Cụ thể: kiểm "có bịa không" là đi từ **slide → nguồn** (lấy từng claim trên slide, tìm xem nguồn có support không). Kiểm "có bỏ sót không" là đi từ **nguồn → slide** (lấy từng ý quan trọng của nguồn, tìm xem slide có nhắc không). **Một cơ chế chỉ chạy một chiều thì mù hoàn toàn với chiều kia.**

Đây không phải lý luận. `EXTERNAL EVIDENCE` có bảng số trực tiếp: một response **thiếu hẳn câu trả lời chính** vẫn đạt FActScore **82,75**, trong khi response **đúng và đủ** chỉ đạt **82,58** — tức metric chấm cái thiếu *cao hơn* cái đủ. Vì FActScore chỉ chạy chiều output→source, và mọi thứ nó nhìn thấy đều đúng.

Và tại sao đây là stage **của người xây agent**, không chỉ của người đo? Vì **danh sách ý quan trọng mà bạn dùng để đo coverage chính là danh sách mà ContentPlanner của bạn phải sinh ra**. Định nghĩa được đơn vị đo là định nghĩa được đầu ra của planner. Hai bài toán là một.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể 1 — hệ thống được thưởng vì làm sai.**
Bạn đo fidelity bằng một metric kiểu NLI (kiểm mỗi câu trên slide có được nguồn entail không). Để tối ưu điểm đó, model học cách **chép nguyên văn nhiều câu dài từ PDF** lên slide. Điểm fidelity lên gần tuyệt đối. Nhưng slide dày đặc chữ chính là thứ FR-22 và tiêu chí "ready-to-use" coi là **lỗi**.

`EXTERNAL EVIDENCE`: đây không phải giả thuyết. *Do Automatic Factuality Metrics Measure Factuality?* đo được rằng **chèn một câu ngẫu nhiên lấy từ source làm điểm tăng bằng hoặc hơn mức tăng do sửa lỗi thật**, và chiến lược gaming cho cải thiện **gấp 3–8 lần** so với cải thiện thật. Tức metric fidelity **thưởng cho đúng cái sản phẩm coi là hỏng**.

**Ví dụ cụ thể 2 — hỏi thẳng LLM và tin câu trả lời.**
Bạn nghĩ: "đơn giản, đưa cả PDF và cả deck cho model, hỏi *có bỏ sót ý quan trọng nào không*". `EXTERNAL EVIDENCE` nói đây là hướng yếu, từ hai nguồn độc lập về phương pháp: (a) FineSurE đo G-Eval (một LLM judge tốt) chỉ đạt Pearson **0,314** trên completeness, so với **0,688** của cơ chế đối chiếu keyfact; (b) AbsenceBench giải thích **cơ chế**: attention *"cannot easily attend to 'gaps' in documents since these absences don't correspond to any specific keys"* — model mạnh nhất cũng chỉ đạt 69,6% F1 trên một task **dễ hơn nhiều** (cho xem cả bản gốc lẫn bản bị xoá).

### 4. Sau Stage này tôi phải hiểu được gì?

1. Phân biệt **salience** với **tóm tắt**, và nói được vì sao slide không phải là tóm tắt ngắn hơn.
2. Giải thích **kiến trúc hai chiều**: vì sao precision (không bịa) và recall (không sót) cần hai pass ngược nhau, không gộp được.
3. Định nghĩa được **đơn vị đo nội dung** và biết tension của nó: đơn vị lớn thì khó verify, đơn vị nhỏ thì mất context để diễn giải.
4. Biết ít nhất **hai họ công cụ** để kiểm grounding text↔text (NLI/alignment và atomic-fact decomposition), và biết giới hạn chung của cả hai: **mù hoàn toàn với layout/visual**.
5. Nhận diện được **cách gaming** một metric fidelity, và vì sao đó là rủi ro thiết kế chứ không phải chuyện lý thuyết.
6. Biết rằng **bullet trên slide là một đơn vị verify đặc biệt khó** — nó là fragment mất chủ ngữ, mất mốc thời gian, mất đơn vị.

### 5. Papers cần đọc

#### MUST-READ

##### `FineSurE: Fine-grained Summarization Evaluation using LLMs`

**Tại sao đọc paper này ở Stage này?**
Đây là **bản thiết kế** của toàn bộ Stage 2. Nó là nguồn duy nhất trong corpus có đúng cấu trúc ba failure mode, và cấu trúc đó chuyển giao được nguyên vẹn sang bài toán deck.

**Tôi cần lấy gì từ paper?**
- **Kiến trúc hai task ngược chiều**: *fact checking* (output→source) và *keyfact alignment* (keyfact→output, và phải chỉ ra **số dòng** khớp). Đây là thứ bạn sẽ copy vào hệ thống của mình.
- **Ba công thức**, vì chúng cho thấy ba trục thật sự độc lập:
  - Faithfulness = `|S_fact| / |S|` (tỷ lệ câu không lỗi)
  - Completeness = `|{k | (k,s) ∈ E}| / |K|` (tỷ lệ keyfact được align)
  - Conciseness = `|{s | (k,s) ∈ E}| / |S|` (tỷ lệ câu output có align với keyfact — tức đo **thừa**)
- **Nguồn của keyfact**: *"provided by humans; if unavailable, it can be automatically derived from the reference summary"* — precedent cho cả hai nhánh (người viết / máy sinh).
- Hai con số hiệu chỉnh kỳ vọng: GPT-4 đạt 86,4% khi **detect** lỗi nhị phân nhưng chỉ **42,2%** khi phải nói **loại** lỗi nào → đừng thiết kế hệ thống dựa vào việc evaluator phân loại đúng loại lỗi.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua taxonomy 7 loại lỗi ngôn ngữ học (out-of-context, entity, predicate, circumstance…) — nó là taxonomy *ngôn ngữ*, không phải trục "có counterpart trong nguồn hay không", và repo ghi rõ việc map nó sang ba failure mode của Deck Agent là **INFERENCE**, không phải điều paper nói. Bỏ qua so sánh với UniEval.

##### `All Claims Are Equal, but Some Claims Are More Equal Than Others`

**Tại sao đọc paper này ở Stage này?**
Vì nó cung cấp **bằng chứng số** cho luận điểm mà nếu chỉ nghe lý thuyết bạn sẽ không tin: precision-only metric **mù với bỏ sót**. Và nó đưa thêm một khái niệm mà FineSurE thiếu: **không phải mọi fact đều đáng cân bằng nhau**.

**Tôi cần lấy gì từ paper?**
- **Bảng số** (3 dòng, thuộc lòng luôn cũng được): normal 82,58 / **missing 82,75** / wrong 76,63 dưới FActScore. Missing > normal. Đó là toàn bộ luận điểm.
- Cơ chế **salience 3 mức**: phân rã response thành atomic subclaim, xếp hạng theo mức liên quan, gán nhãn **vital / okay / less-important**, rồi chỉ tính metric trên nhóm vital → khoảng cách "wrong" vs "normal" giãn từ ~6 lên **24 điểm**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết setup long-form QA của họ. ⚠ Cảnh báo transfer quan trọng: "importance" của họ neo vào **query người dùng hỏi**; với deck bạn phải neo vào **tài liệu nguồn + mục đích dùng deck**. Cơ chế phải thiết kế lại, chỉ kết luận là chuyển được. Paper cũng tự nói *human weighting preferences remain an acknowledged open question*.

##### `MiniCheck: Efficient Fact-Checking of LLMs on Grounding Documents`

**Tại sao đọc paper này ở Stage này?**
Vì hai paper trên cho bạn **kiến trúc**; paper này cho bạn **công cụ chạy được thật, đủ rẻ để chạy mỗi ngày**. Đây là chỗ knowledge chuyển thành implementation.

**Tôi cần lấy gì từ paper?**
- Phát biểu bài toán: *"recognizing if LLM output can be grounded in evidence"* — đúng bài toán của bạn.
- Chi tiết kỹ thuật quan trọng nhất: dữ liệu train được thiết kế để dạy model *"check each fact in the claim and recognize **synthesis of information across sentences**"*. **Đây chính là hình dạng một bullet** — một câu gom thông tin từ nhiều câu nguồn.
- Thông tin vận hành: model dưới 1B (MiniCheck-FT5, ~770M) đạt mức GPT-4 với chi phí thấp hơn ~400×; repo báo inference 29K example trong 30 phút trên một GPU A6000 → `INFERENCE` của repo: ~16 claim/giây, tức **vài giây cho một deck 40 bullet, API cost = 0**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết benchmark LLM-AggreFact. Và nhớ **bốn giới hạn** repo ghi: không đo coverage, không phân biệt bịa với sai, vẫn game được bằng chèn câu từ source, vẫn vấp ở negation tương đương logic.

#### SHOULD-READ

##### `AbsenceBench: Language Models Can't Tell What's Missing`

**Tại sao đọc paper này ở Stage này?**
Vì nó trả lời câu hỏi bạn **chắc chắn sẽ hỏi**: "sao không hỏi thẳng model xem thiếu gì?" — và trả lời bằng **cơ chế**, không phải bằng số suông.

**Tôi cần lấy gì từ paper?**
Đúng hai thứ: (a) câu giải thích cơ chế — attention *"cannot easily attend to 'gaps' in documents since these absences don't correspond to any specific keys that can be attended to"*; (b) con số 69,6% F1 với context chỉ ~5K token, **trên một task dễ hơn** task của bạn (họ cho model xem cả bản gốc lẫn bản đã xoá, phần thiếu khớp chính xác). Đọc con số đó như **cận trên lạc quan**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua 3 domain thí nghiệm (dãy số, thơ, GitHub PR) — không liên quan.

##### `Stress Testing Factual Consistency Metrics for Long-Document Summarization`

**Tại sao đọc paper này ở Stage này?**
Vì nó là thứ ngăn bạn chọn nhầm công cụ. Có nhiều metric grounding; paper này stress-test chúng và xếp hạng độ bền.

**Tôi cần lấy gì từ paper?**
- Kết luận xếp hạng: **MiniCheck và UniEval tương đối bền**; **AlignScore và SummaC-ZS là nhóm rủi ro cao** (*"particularly unreliable across domains and perturbation types"*).
- Finding quan trọng nhất cho deck: *"metric scores consistently decrease as claim similarity increases"* — metric **hoạt động tệ hơn trên claim bị nén, mật độ thông tin cao**. Bullet trên slide *về bản chất* là claim bị nén → **deck rơi đúng vào vùng metric yếu nhất**.
- Kết luận của tác giả: *"no metric consistently maintains factual alignment under long-context conditions"*.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết 7 phép biến đổi và 3 domain. ⚠ Preprint chưa peer-review.

##### `Do Automatic Factuality Metrics Measure Factuality? A Critical Evaluation`

**Tại sao đọc paper này ở Stage này?**
Vì nó dạy bạn một thói quen tư duy quan trọng hơn cả nội dung: **trước khi tin một metric, hãy hỏi metric này bị game bằng cách nào**.

**Tôi cần lấy gì từ paper?**
- Kết quả gaming: chèn câu ngẫu nhiên từ source → điểm tăng bằng hoặc hơn sửa lỗi thật; chiến lược gaming cho cải thiện **gấp 3–8 lần** cải thiện thật.
- Kết quả "MLP feature bề mặt cạnh tranh được với SOTA" → một phần điểm số của các metric này giải thích được bằng đặc trưng bề mặt, không phải bằng "hiểu tính đúng".

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua so sánh chi tiết 6 metric. ⚠ Repo chưa xác nhận venue.

#### OPTIONAL / REFERENCE

- **`Molecular Facts`** — đọc để có **tên gọi** cho vấn đề bạn chắc chắn sẽ gặp: hai tiêu chí *decontextuality* (đứng một mình hiểu được) và *minimality* (thêm càng ít càng tốt). Bullet `"— Doanh thu +51% YoY"` là fragment cực đoan hơn cả atomic fact. ⚠ Repo `[SEARCH-ONLY]`, chưa fetch bản gốc → **không trích số**.
- **`RoSE / ACU`** và **`QAPyramid`** — đọc nếu bạn sắp tự định nghĩa danh sách "ý quan trọng". ACU cho bạn khái niệm đơn vị nội dung nguyên tử; QAPyramid cho bạn lý do vì sao cần chuẩn hoá **độ mịn** của đơn vị (*"lack of systematicity in the definition and granularity of the sub-units"*). ⚠ Cả hai neo vào **reference summary**, bạn thì neo vào **source** — khác biệt thật, làm **bài toán salience quay lại**.
- **`On Positional Bias of Faithfulness`** — đọc nếu nguồn của bạn dài: faithfulness theo vị trí có dạng **chữ U**, ý nằm giữa tài liệu bị đo kém hơn. Đây là lập luận ủng hộ **chia nhỏ + đối chiếu theo item** thay vì nhét cả PDF vào một prompt.
- **`QAFactEval`** và **`Comprehensiveness Metrics`** — đọc nếu muốn khảo sát nhánh QA-based. Lấy: QA-based và entailment-based cho **tín hiệu bổ sung nhau**. ⚠ *Comprehensiveness Metrics* kết luận "end-to-end hiệu quả bất ngờ", **mâu thuẫn trực tiếp** với AbsenceBench và FineSurE — repo ghi đây là **mâu thuẫn chưa giải quyết**, không dùng để biện minh cho việc hỏi thẳng LLM.
- **`VeriFastScore`** — đọc nếu chi phí là vấn đề. Lấy: gộp decompose + verify vào một lượt cho speedup 6,6×, và con số **r system-level 0,94 > example-level 0,80** → metric rẻ có thể đủ tin để **so version** dù chưa đủ tin để phán xét **một deck cụ thể**.
- **`FActScore`** — đọc để hiểu nguyên lý atomic decomposition. Nhưng đọc **sau** *All Claims Are Equal*, để bạn biết ngay giới hạn của nó thay vì phải học lại.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao "không bịa" và "không bỏ sót" **không thể** đo bằng cùng một cơ chế, kèm bảng số missing 82,75 > normal 82,58.
- Vì sao một danh sách "ý quan trọng" phải là **danh sách đóng, xác định trước**, chứ không thể để model tự nghĩ ra lúc chấm.
- Vì sao bullet trên slide khó verify hơn một câu văn xuôi bình thường.
- Metric fidelity của tôi có thể bị game bằng cách nào, và tôi phát hiện bằng cách nào.
- Vì sao đơn vị đo coverage của tôi **cũng chính là** đầu ra mà ContentPlanner phải sinh.

### Implementation checkpoint

**Đã có thể làm:**
1. Định nghĩa được **schema cho key fact** (mỗi item: nội dung, vị trí trong nguồn, mức salience) — đây là artifact dùng cho cả planner lẫn evaluator.
2. Implement pass 1 (**fidelity**): tách bullet trên deck thành claim, chạy MiniCheck hoặc tương đương với nguồn, ra tỷ lệ supported.
3. Implement pass 2 (**coverage**): với mỗi key fact trong danh sách, kiểm deck có nhắc không, ra tỷ lệ covered.
4. Chạy **thử nghiệm gaming của chính mình**: cố tình tạo một deck chép nguyên văn nhiều câu từ nguồn, xem điểm fidelity có tăng không. Nếu tăng — bạn vừa tự tìm ra lỗ hổng metric của mình trước khi nó làm hỏng cả kỳ.

**Chưa nên làm:** chưa nên tin điểm coverage nếu danh sách key fact do LLM tự sinh mà chưa ai kiểm — corpus **không có nguồn nào** so sánh bằng số chất lượng checklist người viết vs LLM sinh (xem Stage 8 và §6, Gap 8). Chưa nên gộp fidelity và coverage thành một điểm.

⚠ **Cảnh báo lớn nhất của Stage 2** (`EXTERNAL EVIDENCE`, negative finding của RQ02): **không tìm thấy nguồn nào áp dụng AlignScore / SummaC / MiniCheck / FActScore lên slide deck.** Toàn bộ là summarization, RAG, biomedical, long document. Nghĩa là **khả năng chuyển giao của nhóm metric này sang deck là `INFERENCE`, không phải `EXTERNAL EVIDENCE`.** Hãy chạy pilot trước khi cam kết, đừng implement thẳng.

---

## Stage 3 — `Từ nội dung tới storyline: deck-level và slide-level planning`

### 1. Tôi đang cố hiểu vấn đề gì?

Bạn đã có một danh sách ý quan trọng (Stage 2). Bây giờ: **sắp xếp chúng thành một deck**. Việc này gồm hai quyết định khác nhau mà người mới hay gộp:

- **Deck-level planning** — deck có mấy phần, thứ tự nào, mạch dẫn dắt ra sao. "Narrative structure" = trình tự sao cho người nghe hiểu ý sau **nhờ** ý trước. Ví dụ: bối cảnh → vấn đề → cách giải → kết quả → kết luận.
- **Slide-level planning** — *một* slide chứa ý gì, đóng vai trò gì trong mạch (mở đầu / trình bày dữ liệu / so sánh / chốt), và chứa bao nhiêu đơn vị nội dung.

Giữa hai cái đó có một thứ nữa gọi là **slide split**: quyết định ranh giới — ý nào ở cùng slide, ý nào tách ra. Đây là quyết định gây lỗi nhiều nhất trong thực tế.

Và một khái niệm mà bạn cần biết ngay vì nó là **negative finding lớn nhất của cả corpus**: **plan trung gian** (outline, kế hoạch, cấu trúc) là một artifact tồn tại bên trong hệ thống, nhưng **chưa ai chấm điểm nó như một đối tượng đánh giá riêng**.

### 2. Tại sao tôi cần biết điều này?

Vì đây là chỗ **quy trách nhiệm lỗi**. Câu hỏi thực tế bạn sẽ phải trả lời hàng tuần: *"deck này chia slide kém — là vì LLM viết chữ dở, hay vì tầng planning sắp xếp sai?"*

Nếu không có khái niệm tách bạch deck-level plan / slide-level plan / nội dung slide, bạn không trả lời được, và mọi lần "cải tiến" của bạn là đoán mò: bạn sửa prompt viết chữ trong khi lỗi nằm ở tầng chia section, hoặc ngược lại.

`EXTERNAL EVIDENCE` cho thấy tầng planning **thật sự có tác động đo được**: PPTAgent ablation đo `w/o Outline` làm coherence tụt **4,48 → 3,36** và `w/o Structure` → **3,45**. Tức bỏ tầng planning đi thì chất lượng mạch lạc sập, dù model viết chữ không đổi.

Nhưng — và đây là phần khó chịu — `EXTERNAL EVIDENCE` cũng cho thấy **cách duy nhất người ta đo được điều đó là qua ảnh hưởng lên deck cuối**, chứ không phải bằng cách chấm chính cái outline. Repo ghi negative finding này ở **hai RQ độc lập** (RQ01 #1 và RQ03 #6): *"không có external work nào chấm điểm trực tiếp một intermediate plan / outline / IR như một evaluation target riêng."*

`INFERENCE` (của tôi, và cần nói rõ là suy luận): điều đó có nghĩa nếu bạn muốn đo chất lượng ContentPlanner **trực tiếp**, bạn đang ở vùng **không có precedent** — vừa là cơ hội đóng góp, vừa là rủi ro vì không có ai để đối chiếu.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn cho model một prompt duy nhất: "đọc PDF này và sinh 10 slide". Model trả về 10 slide đúng nội dung. Nhưng slide 2 nói về kết quả thí nghiệm, slide 7 mới giới thiệu phương pháp, và slide 9 quay lại bối cảnh. Người xem không theo được.

Bạn thử sửa bằng cách thêm câu "hãy sắp xếp mạch lạc" vào prompt. Kết quả cải thiện chút ít rồi lại hỏng ở lần chạy khác. Lý do: bạn đang cố sửa một **quyết định cấu trúc** bằng một **gợi ý ngôn ngữ**, và không có bất kỳ artifact trung gian nào để kiểm tra xem cấu trúc được quyết định ra sao. Không có outline để nhìn, không có gì để test, không có gì để sửa ngoài prompt.

Phiên bản thứ hai: bạn *có* làm outline, nhưng vì bạn chưa biết negative finding ở trên, bạn giả định "chấm outline dễ thôi, đưa cho LLM chấm điểm 1–5". Rồi bạn phát hiện điểm outline không tương quan gì với chất lượng deck cuối — vì bạn vừa tự chế một metric chưa ai validate.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Phân biệt **deck-level planning**, **slide-level planning**, và **content generation** như ba tầng có thể hỏng độc lập.
2. Biết rằng **representation nào tồn tại giữa hai bước** quyết định bạn debug được hay không — nếu không có artifact trung gian thì không có gì để kiểm.
3. Biết **một metric structure deterministic có thật**: khoảng cách chỉnh sửa trên **chuỗi thứ tự chủ đề**.
4. Hiểu vì sao **"deck để dạy" khác "deck để tóm tắt"** ở chỗ giải thích khái niệm nền — và vì sao khác biệt đó **không đo được bằng coverage**.
5. Biết một thuộc tính cấu trúc **đếm được bằng code** có nền học thuật: title là câu khẳng định hoàn chỉnh hay cụm danh từ.
6. Biết rằng **chấm plan trung gian là vùng không có precedent** — và biết mình đang tự chế khi làm điều đó.

### 5. Papers cần đọc

#### MUST-READ

##### `PPTAgent` — **lần 3** (Stage 0: kiến trúc; Stage 1: bộ đo; ở đây: bằng chứng rằng planning quan trọng)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần này tôi chỉ đọc **bảng ablation**, và đọc nó như một thí nghiệm về planning: *nếu bỏ tầng lập kế hoạch đi thì cái gì hỏng, và hỏng theo trục nào*.

**Tôi cần lấy gì từ paper?**
- Ba dòng: full 95,0% SR / 3,67 PPTEval; **`w/o Outline` 91,0 / 3,30**; **`w/o Structure` 92,2 / 3,32**. Và coherence cụ thể tụt 4,48 → 3,36 / 3,45.
- Cách đọc đúng: họ **không** chấm outline. Họ **xoá** outline và đo deck cuối. Đây là mô hình thí nghiệm bạn có thể copy khi muốn chứng minh một component của mình có giá trị.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua `w/o Schema` và `w/o CodeRender` (thuộc Stage 5 và Stage 9). Bỏ qua PPTEval definitions (đã lấy ở Stage 1).

##### `SlideTailor: Personalized Presentation Slide Generation for Scientific Papers`

**Tại sao đọc paper này ở Stage này?**
Vì nó chứa **metric structure deterministic cụ thể nhất trong toàn bộ landscape** — thứ duy nhất cho phép bạn đo "cấu trúc deck" mà không cần gọi model.

**Tôi cần lấy gì từ paper?**
- **`Flow = 1 − NGLD`** trên **chuỗi thứ tự subtopic**. NGLD = Normalized General Levenshtein Distance — khoảng cách chỉnh sửa giữa hai chuỗi, chuẩn hoá về [0,1]. Dịch sang tiếng người: *coi deck như một dãy chủ đề, so dãy đó với dãy tham chiếu, đếm số phép chèn/xoá/đổi cần thiết*. Đây là **code thuần**, chạy trong micro giây.
- **`Coverage = IoU`** giữa tập subtopic của deck sinh ra và deck mẫu (IoU = giao chia hợp).
- Cách họ tách **preference-based** vs **preference-independent** metric — dùng lại được ở Stage 4.
- **Negative finding cần biết:** họ **không có** metric nào cho số slide, mật độ chữ/slide, hay tỉ lệ text:image.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua Aesthetic MLLM-judged. Bỏ qua cơ chế conditioning bằng deck mẫu (Stage 4). ⚠ Lưu ý: `Flow` và `Coverage` của họ neo vào **deck mẫu của user**; nếu bạn không có deck mẫu thì phải neo vào **thứ tự section trong tài liệu nguồn** — thay đổi thật, chưa ai validate.

##### `A Good Talk Does not Look Like a Summary, It Teaches You!` (EffectivePresentationScorer)

**Tại sao đọc paper này ở Stage này?**
Vì nó là nguồn external **duy nhất** trong corpus phát biểu tường minh **điều gì làm một trình bày khác một bản tóm tắt** — và câu trả lời của nó là một thuộc tính **cấu trúc**, không phải thuộc tính nội dung.

**Tôi cần lấy gì từ paper?**
Ba câu, và chúng đáng đọc kỹ:
1. Vấn đề: metric hiện tại chỉ đo *"whether key points from the paper appear in the video without assessing whether the video actually helps viewers understand the ideas"*.
2. Đề xuất: chấm xem sản phẩm có *"introduces needed background concepts, and connects technical details to the main contribution"*.
3. Finding: hệ thống hiện tại *"mention the correct topics and follow the structure of the paper but **fail to explain prerequisite concepts** or clarify why the method works"*.

Câu 3 là câu quan trọng nhất Stage 3: **coverage cao + thứ tự đúng vẫn có thể là một deck không dạy được ai cái gì.** Đây là trục chất lượng thứ ba mà Stage 1 và Stage 2 chưa chạm tới.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua toàn bộ phần đặc thù video (timing, speech). ⚠ Repo chỉ verify ở mức abstract, **chưa đọc full-text** → không biết metric của họ là deterministic hay judged.

#### SHOULD-READ

##### `How the design of presentation slides affects audience comprehension` (Garner & Alley 2013)

**Tại sao đọc paper này ở Stage này?**
Vì nó cho bạn **một thuộc tính cấu trúc slide đếm được bằng code, có nền nghiên cứu thực nghiệm** — hiếm, vì corpus chỉ có 3 paper loại này.

**Tôi cần lấy gì từ paper?**
Đúng một ý: cấu trúc **assertion-evidence** — headline là một **câu khẳng định đầy đủ** (ví dụ: "Doanh thu tăng nhờ mở rộng thị trường phía Nam") thay vì cụm danh từ ("Doanh thu"), với evidence chủ yếu bằng hình — cho comprehension và recall tốt hơn, rõ nhất ở khái niệm phức tạp. Trên `Slide.title` của bạn, "có phải câu hoàn chỉnh không" là một predicate parse được.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua thiết kế thực nghiệm. ⚠ **Cảnh báo mạnh của repo:** literature này nói về *slide tốt nói chung*, **không** nói gì về việc phân biệt các mode dùng deck → dùng nó để lập luận "deck dạy học phải có nhiều assertion-title hơn" là **suy diễn vượt nguồn**. Repo chưa đọc full-text.

##### `CourseBlueprint`

**Tại sao đọc paper này ở Stage này?**
Vì nó là nơi bạn học được rằng ý tưởng hiển nhiên nhất về mạch dẫn dắt — *"kiểm xem khái niệm có được giới thiệu đúng thứ tự phụ thuộc không"* — **chưa ai giải được bằng phương pháp deterministic**.

**Tôi cần lấy gì từ paper?**
Một quan sát: ngay cả khi họ **có** dependency structure suy ra từ tài liệu nguồn, họ vẫn kiểm thứ tự bằng **LLM judge**, không phải bằng thuật toán trên graph. Cộng với ý tưởng metric **level-aware** (một số metric phụ thuộc trình độ audience, một số không) — trùng cách tách của SlideTailor.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua Flesch-Kincaid — ⚠ repo cảnh báo **không hợp lệ cho tiếng Việt**. ⚠ Preprint, nhóm nhỏ, authority thấp.

#### OPTIONAL / REFERENCE

- **`OutlineSpark`** — đọc nếu bạn muốn thấy một hệ thống outline-driven cụ thể. ⚠ Repo **chưa đọc**, chỉ ghi nhận vì liên quan FR-07.
- **`DocPres`** — quay lại để xem cách họ đo Coverage ở **hai mức** (paragraph↔slide và sentence↔bullet). Ý tưởng hai mức là đúng; cách đo (embedding similarity) là sai — bạn đã biết vì sao từ Stage 2.
- **`PaperX` (Scholar DAG)** — chỉ ghi tên. ⚠ Repo **chưa đọc**; tên gọi gợi ý một biểu diễn phụ thuộc giữa các ý nhưng đó là **suy đoán của tôi**, không phải evidence.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao "deck chia slide kém" là một triệu chứng chứ không phải một chẩn đoán, và tôi cần artifact gì để chẩn đoán được.
- Vì sao bằng chứng duy nhất hiện có về giá trị của tầng planning đến từ **ablation**, không phải từ việc chấm plan.
- Một cách đo cấu trúc deck **không cần gọi model** (`1 − NGLD` trên chuỗi chủ đề), và điều kiện để nó dùng được.
- Vì sao một deck có coverage cao và thứ tự đúng vẫn có thể **không dạy được ai cái gì**.
- Tôi đang ở vùng có precedent hay không khi tự chấm điểm outline.

### Implementation checkpoint

**Đã có thể làm:** implement một **deck planner** thật — nhận danh sách key fact từ Stage 2, sinh ra một **outline có cấu trúc** (danh sách section → danh sách slide → mỗi slide gồm những key fact nào). Kèm hai kiểm tra deterministic chạy được ngay: (a) `1 − NGLD` giữa thứ tự chủ đề trên deck và thứ tự trong tài liệu nguồn; (b) đếm tỉ lệ title là câu hoàn chỉnh.

**Chưa nên làm:** chưa nên implement **layout generator** — bạn chưa biết cái gì đo được bằng hình học (Stage 6), và nếu làm bây giờ bạn sẽ tối ưu mù. Chưa nên tin bất kỳ điểm số nào bạn tự chấm cho outline; hãy đo tác động của planner lên **deck cuối** (kiểu ablation) thay vì chấm plan trực tiếp — đó là cách duy nhất có precedent.

---

## Stage 4 — `Cùng nguồn, khác mục đích: conditioning và cách chứng minh nó hoạt động`

### 1. Tôi đang cố hiểu vấn đề gì?

**Conditioning** = bắt hệ thống thay đổi output theo một tham số đầu vào. Cùng một PDF, nếu người dùng nói "deck để dạy sinh viên" thì phải khác với "deck để người vắng họp đọc lại" và khác với "deck chiếu sau lưng khi tôi nói".

Vấn đề thật **không phải** "làm sao bắt nó khác" — thêm một câu vào prompt là nó khác rồi. Vấn đề là **hai câu hỏi khó hơn nhiều**:

1. **Nó có thực sự khác không, hay tôi đang nhìn thấy nhiễu?** Chạy lại cùng một cấu hình hai lần cũng ra hai deck khác nhau. Vậy khác biệt bạn quan sát được giữa hai chế độ là **hiệu ứng** hay là **dao động tự nhiên**?
2. **Nó khác đúng hướng không?** Deck "dạy học" của bạn dài hơn 20%. Nhưng dài hơn có phải là *dạy tốt hơn* không, hay chỉ là *nhiều chữ hơn*?

Hai khái niệm cần biết:
- **Noise floor** (sàn nhiễu) — biên độ dao động khi chạy lại **cùng** input, **cùng** cấu hình. Mọi khác biệt nhỏ hơn nó là vô nghĩa.
- **Manipulation check** — thuật ngữ từ khoa học thực nghiệm: phép đo xác minh rằng thao tác bạn làm **đã thực sự tác động như dự định**.

### 2. Tại sao tôi cần biết điều này?

Vì `EXTERNAL EVIDENCE` cho thấy conditioning là capability mà **trực giác sai nhiều nhất**, theo ba hướng cùng lúc:

- **Hiệu ứng nhỏ hơn bạn tưởng.** X+Slides đo paired delta lớn nhất chỉ ~**+0,083**. Hu & Collier đo persona chỉ giải thích **<10% variance**.
- **Hiệu ứng có thể âm.** X+Slides Table 9: deck sinh cho *specialist* khi chấm bằng utility function của chính specialist cho target advantage **−0,117** — tức prompt "làm cho chuyên gia" khiến deck **kém hợp** với chuyên gia hơn. Tác giả kết luận: *"While audience prompts are effective, they do not consistently align with the intended profile without audience-conditioned evaluation."*
- **Nhiễu lớn hơn bạn tưởng.** Atil et al. đo accuracy dao động tới ~15% giữa các run ở cấu hình được coi là deterministic.

Ghép ba điều đó: **hiệu ứng ~0,08 nằm chìm trong nhiễu nếu bạn không thiết kế phép đo cẩn thận.** Đây là lý do Stage 4 nằm sau Stage 3 chứ không nằm chung — nó không phải "thêm một tính năng", nó là một bài toán thống kê.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn thêm `slide_type` vào prompt. Bạn chạy PDF A với chế độ "teaching", được deck 14 slide, 41 từ/slide. Chạy lại với "speaker_support", được 11 slide, 22 từ/slide. Bạn kết luận: *"conditioning hoạt động, khác biệt rõ ràng"*, và ghi vào báo cáo.

Sai ở ba chỗ:
1. Bạn chưa chạy **cùng một chế độ hai lần**. Có thể "teaching" chạy lần nữa cũng ra 11 slide. Bạn chưa đo noise floor.
2. Bạn dùng **một tài liệu**. Khác biệt có thể là đặc thù tài liệu đó.
3. Bạn đo **words/slide** rồi ngầm coi nó **là** "mức độ phù hợp để dạy". `EXTERNAL EVIDENCE` phản bác trực tiếp: *Evaluating the Evaluators* đo thấy phần lớn metric readability **tương quan kém với phán đoán người**, kể cả FKGL — tức một proxy bề mặt có thể **đo được** mà **không tương ứng** với thứ con người cảm nhận.

Kết quả: bạn bảo vệ một claim không đứng vững, và hội đồng chỉ cần hỏi "các bạn chạy mấy lần?" là sập.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Phân biệt **"có khác biệt"** với **"khác biệt đúng hướng"** — hai claim khác nhau, cần hai loại evidence khác nhau.
2. Biết vì sao phải đo **noise floor trước**, và biết một hình thái metric để đo nó (agreement rate qua N run).
3. Biết cơ chế **recovery test / inverse specification**: đưa deck cho một bên độc lập và bắt nó **đoán lại** tham số đã dùng.
4. Biết vì sao thiết kế **ghép cặp theo tài liệu nguồn** (paired design) là bắt buộc khi hiệu ứng nhỏ.
5. Biết ranh giới giữa **verifiable instruction** (kiểm bằng code) và phần không verifiable được của một yêu cầu.
6. Biết rằng **một proxy deterministic chứng minh được "có khác biệt" nhưng không chứng minh được "khác biệt có giá trị"**.

### 5. Papers cần đọc

#### MUST-READ

##### `X+Slides: Benchmarking Audience-Conditioned Slide Generation`

**Tại sao đọc paper này ở Stage này?**
Đây là nguồn duy nhất trong corpus đo conditioning **trên đúng artifact slide deck**, đo **contrast chéo giữa các chế độ trên cùng nguồn**, và báo cáo rằng conditioning **có thể thất bại theo hướng ngược**. Không có nguồn thay thế.

**Tôi cần lấy gì từ paper?**
- **Cơ chế conditioning**: sinh một bộ **probe chung** từ tài liệu nguồn (audience-agnostic), rồi gán **utility weight khác nhau theo audience** (rubric 0–1: 1.0 essential / 0.6 important / 0.3 useful background / 0.0 irrelevant). Điểm hay của cơ chế: **không phải định nghĩa lại metric cho từng chế độ** — cùng probe, khác trọng số.
- **Table 9, cross-audience scoring** — thiết kế thí nghiệm bạn nên copy: chấm mỗi deck bằng utility function của **cả ba** audience, rồi tính "target advantage".
- **Công thức Efficiency**: `Eff_time = R_A / M` với `M ← 0.25·K + Words(T)/130` (0,25 phút/slide + tốc độ nói 130 từ/phút). Đây là cách biến "deck có vừa thời lượng không" thành một con số.
- Xử lý bất định: **bootstrap 10.000 lần resample**, báo cáo khoảng 2,5–97,5 percentile.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết 113 topic / 7 scene. ⚠ **Cảnh báo transfer quan trọng**: audience của họ (specialist / learner / decision-maker) phân theo *người nghe là ai*; nếu chế độ của bạn phân theo *deck được dùng vào việc gì* thì đó là **trục khác**. **Cơ chế đo chuyển được; profile thì không.** ⚠ Repo chỉ đọc HTML qua tool summary, chưa đọc PDF gốc.

##### `Non-Determinism of "Deterministic" LLM Settings` (Atil et al.)

**Tại sao đọc paper này ở Stage này?**
Vì nó là **điều kiện tiên quyết** để mọi kết luận ở Stage này có nghĩa. Không biết hệ thống dao động bao nhiêu thì không phát biểu được gì về khác biệt.

**Tôi cần lấy gì từ paper?**
- Mệnh đề định tính: ở cấu hình **được kỳ vọng là deterministic**, accuracy vẫn dao động tới ~15% giữa các run, khoảng cách best–worst tới ~70%, và *"none of the LLMs consistently delivers repeatable accuracy across all tasks"*.
- **Hình thái metric**: TARr@N (total agreement rate trên raw output qua N run) và TARa@N (trên parsed answer). Bạn không dùng đúng công thức của họ, nhưng bạn dùng **ý tưởng**: chạy N lần, báo cáo mức đồng thuận.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết 8 task. ⚠ **Không mượn con số 15%** — họ đo trên task có đáp án đúng; deck generation không có accuracy như vậy. Mượn mệnh đề và hình thái metric, không mượn magnitude.

##### `Learning to Present: Inverse Specification Rewards`

**Tại sao đọc paper này ở Stage này?**
Vì nó đưa ra **cách trả lời câu hỏi "nó có khác đúng hướng không"** mà không cần định nghĩa trước "đúng hướng nghĩa là gì": nếu từ deck mà không khôi phục lại được tham số đã yêu cầu, thì claim "khác biệt có chủ đích" chưa đứng vững.

**Tôi cần lấy gì từ paper?**
- **Cơ chế inverse task**: *"an LLM attempts to reconstruct the original specification from the generated slides alone"* — cho model xem deck (HTML + PNG render), **không cho xem brief gốc**, bắt nó đoán `topic`, `audience`, `num_slides`, `key_themes`.
- `s_count = min(predicted, actual) / max(predicted, actual)` — **deterministic thuần**, dùng được ngay.
- Cách họ tách deterministic (Code Rules, Render Quality) khỏi model-judged trong cùng một hệ reward.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua phần RL training. ⚠ **Repo hạ mức tin cậy**: affiliation nhỏ, không phải benchmark cộng đồng dùng → **idea source, không phải authoritative benchmark**. Và repo chỉ ra điểm yếu cần sửa: họ chấm `s_audience` bằng **word-overlap**; nếu bạn chỉ có **3 nhãn đóng** thì nên dùng **forced-choice 3 lớp** (chance = 1/3, kiểm định nhị thức được) — chặt hơn hẳn và rẻ hơn.

#### SHOULD-READ

##### `Instruction-Following Evaluation for Large Language Models` (IFEval)

**Tại sao đọc paper này ở Stage này?**
Vì nó dạy bạn **chia đôi yêu cầu của user**: phần nào kiểm được bằng code, phần nào không. Đây là nguyên tắc tiết kiệm nhất trong cả roadmap.

**Tôi cần lấy gì từ paper?**
Khái niệm **verifiable instruction** và vài ví dụ ("write exactly four paragraphs", "write between 100–150 words", "write in JSON format"). Chuyển sang deck: `target_slides`, `time_limit_minutes`, "mỗi slide tối đa 5 bullet" là **verifiable** → viết `assert`, đừng gọi judge.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết 25 loại instruction. ⚠ Repo ghi rõ giới hạn: IFEval **không** giải được phần khó — "khác biệt có chủ đích" là quan hệ giữa **nhiều output**, không phải thuộc tính của **một** output.

##### `Evaluating the Evaluators: Are readability metrics good measures of readability?`

**Tại sao đọc paper này ở Stage này?**
Vì đây là **counter-evidence trực tiếp** cho cái bẫy bạn chắc chắn sẽ rơi vào: lấy một proxy deterministic (words/slide, FKGL) rồi coi nó *là* thuộc tính muốn đo.

**Tôi cần lấy gì từ paper?**
Một câu: *"most correlate poorly with human judgments, including the most popular metric, FKGL"*, và LM judge tốt nhất cũng chỉ đạt Pearson **0,56**. Hệ quả cho bạn: **deterministic feature dùng để chứng minh CÓ khác biệt, không dùng để chứng minh khác biệt ĐÚNG HƯỚNG.**

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết 8 metric.

##### `The Hitchhiker's Guide to Testing Statistical Significance in NLP` + `Manipulation Check` (Hoewe)

**Tại sao đọc hai nguồn này ở Stage này?**
Vì chúng cho bạn **tên gọi đúng và bộ chuẩn đi kèm** cho việc bạn đang làm. Khi biết "cái mình cần là một manipulation check", bạn mượn luôn được cả quy trình: điều kiện đối chứng, ghép cặp, kiểm định, và **khai báo hướng kỳ vọng TRƯỚC khi chạy**.

**Tôi cần lấy gì?**
Từ Hoewe: định nghĩa manipulation check — phép đo xác minh thao tác *"elicits the expected differences between or among experimental conditions"*. Từ Dror et al.: nguyên tắc **chọn test theo đặc điểm của task và measure**, và ý tưởng dùng **paired design + permutation/bootstrap + effect size** thay vì so hai số trung bình.

**Không cần tập trung vào gì ở thời điểm này?**
Không cần đọc hết Dror et al. — cần đủ để biết chọn test nào là một quyết định có phương pháp, không phải mặc định t-test. ⚠ Hoewe là **E2** (mục từ bách khoa) → dùng làm khung khái niệm, không phải evidence kỹ thuật đơn lẻ.

#### OPTIONAL / REFERENCE

- **`Quantifying the Persona Effect in LLM Simulations`** — đọc để hiệu chỉnh kỳ vọng: persona giải thích **<10% variance**. Hội tụ với X+Slides. Lấy đúng một hệ quả: **thiết kế ghép cặp là bắt buộc, không phải tùy chọn.**
- **`Generating Summaries with Controllable Readability Levels`** — đọc để thấy mô hình "đặt mức mục tiêu → đo khoảng cách tới mức đó". ⚠ Khác biệt quyết định: readability **có thang đo sẵn**, chế độ deck của bạn **không có** — đó chính là phần bạn phải tự dựng.
- **`SlideTailor`** — **lần 2** (Stage 3 đọc để lấy `Flow`; ở đây đọc để lấy **cách tách preference-based vs preference-independent metric**, tức ánh xạ "metric nào phụ thuộc chế độ, metric nào không").
- **`PPLM`** — chỉ để biết **gốc phương pháp** của recovery test là **external attribute classifier accuracy**. ⚠ Repo ghi rõ: đã fetch abstract và trang đó **KHÔNG xác nhận** tên metric → **không trích số hay quote**.
- **`ReadCtrl`** — đọc nếu bạn cân nhắc làm chế độ thành **thang liên tục** thay vì nhãn rời rạc. ⚠ Repo chưa đọc.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao "chạy 2 lần thấy khác nhau" **không** chứng minh conditioning hoạt động.
- Cách đo noise floor của chính hệ thống mình, và vì sao phải làm việc đó **trước** khi so sánh các chế độ.
- Cơ chế "cùng probe, khác trọng số" và vì sao nó tốt hơn việc định nghĩa metric riêng cho từng chế độ.
- Vì sao recovery test là bằng chứng mạnh hơn "so hai con số trung bình".
- Vì sao words/slide chứng minh được *có khác biệt* nhưng không chứng minh được *khác biệt tốt*.

### Implementation checkpoint

**Đã có thể làm:**
1. **Đo noise floor**: chạy cùng một tài liệu, cùng cấu hình, N ≥ 5 lần, ghi lại phân bố của mọi feature deterministic bạn có (số slide, words/slide, thứ tự chủ đề). Đây là artifact **có giá trị độc lập** và không ai làm hộ được — repo ghi rõ: *"noise floor cho deck generation là con số chưa ai có; phải tự đo"*.
2. Implement **paired experiment**: cùng tập tài liệu × các chế độ, so sánh **trong từng tài liệu** rồi mới gộp.
3. Implement **recovery test** dạng forced-choice: đưa deck cho một model không biết brief, bắt chọn 1 trong N nhãn, so với chance level.
4. Viết `assert` cho mọi verifiable instruction.

**Chưa nên làm:** chưa nên tuyên bố chế độ nào "tốt hơn" cho mục đích của nó — đó cần human evaluation, và bạn chưa học cách thiết kế nó (Stage 8). Chưa nên tinh chỉnh prompt theo điểm số khi chưa biết noise floor: bạn sẽ đuổi theo nhiễu.

---

## Stage 5 — `Representation: cái gì nằm giữa plan và pixel`

### 1. Tôi đang cố hiểu vấn đề gì?

Bạn có một kế hoạch (Stage 3) và cuối cùng cần một file PPTX. Ở giữa phải có **một cấu trúc dữ liệu**. Câu hỏi: cấu trúc đó là gì, và **ai được biết về nó**?

**Intermediate Representation (IR)** = một mô tả deck **độc lập với định dạng xuất**. Ví dụ: một JSON ghi "slide 1: tiêu đề X, 3 bullet, một ảnh, dùng layout kiểu two-column" — chưa nói gì về toạ độ pixel, chưa nói gì về XML của PowerPoint.

Có ít nhất **ba vị trí** khác nhau để đặt representation, và chúng dẫn tới ba hệ thống khác nhau:

- **IR-first** — cấu trúc dữ liệu là bản gốc; mọi định dạng xuất (PPTX, HTML, PDF) đều là dẫn xuất. `PROJECT SOURCE` mô tả Deck Agent theo hướng này: *"mọi thứ bên trái Deck IR không được biết output cuối là gì; mọi thứ bên phải không được biết input gốc là gì"*.
- **HTML-first** — bản trình chiếu thật là HTML; JSON chỉ là phụ phẩm để export. `EXTERNAL EVIDENCE`: đây đúng là Open Design — *"Primary output: index.html. Secondary output: slides.json (for PPTX export)"*.
- **Code-first** — model sinh chương trình, chạy chương trình ra file; representation là chính đoạn code (AutoPresent).

Khái niệm thứ hai của Stage này: **editability**. Không phải "sửa được hay không" mà là **sửa được tới mức nào** — và điều đó do representation quyết định, không do model quyết định.

### 2. Tại sao tôi cần biết điều này?

Hai lý do, cả hai đều mang tính **không thể sửa về sau**:

**Lý do 1 — representation quyết định trần editability, và quyết định vĩnh viễn.** `EXTERNAL EVIDENCE`: thang PEI của SlidesGen-Bench có **knockout rule** — fail mức dưới thì không được tính mức trên. Nếu output của bạn là ảnh phẳng (L0), không model nào cứu được. Nếu bạn không có tổ chức master-based (L3), người dùng không thể đổi theme cho cả deck bằng một thao tác — mãi mãi, trừ khi viết lại tầng export.

**Lý do 2 — representation quyết định bạn test được cái gì.** Nếu không có IR, "planning" và "rendering" dính vào nhau: bạn không thể cho planner một input và kiểm output của nó, vì output của nó không tồn tại như một artifact riêng. Mọi thứ ở Stage 3 và Stage 4 **giả định** bạn có một artifact trung gian để nhìn vào.

`EXTERNAL EVIDENCE` gián tiếp cho lý do 2: PPTAgent ablation `w/o Schema` làm Success Rate tụt 95,0% → **78,8%** — tức tầng biểu diễn có schema **giữ cho hệ thống chạy được**, không chỉ cho đẹp.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn để model sinh thẳng ra code `python-pptx` từ PDF trong một lượt. Chạy được, deck ra ổn. Ba tuần sau, bạn cần thêm tính năng "đổi theme". Bây giờ bạn phát hiện: theme nằm rải rác trong code sinh ra — mỗi slide model tự viết `RGBColor(0x1F, 0x4E, 0x79)` ở chỗ nó thích. Không có chỗ nào để đổi một lần. Bạn phải hoặc sửa post-hoc bằng regex (dễ vỡ), hoặc viết lại tầng sinh.

Phiên bản đắt hơn: user báo lỗi "slide 4 sai nội dung". Bạn muốn sửa **chỉ slide 4** mà giữ nguyên phần còn lại. Nhưng không có artifact nào mô tả slide 4 tách biệt — chỉ có một đoạn code dài và một file nhị phân. Bạn phải sinh lại cả deck, và lần sinh lại đó thay đổi **cả những slide user đã hài lòng**. Đây chính xác là lý do Editor Loop cần IR, và là lý do `PROJECT SOURCE` nhấn mạnh ranh giới hai bên của Deck IR.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Phân biệt **IR-first / HTML-first / code-first** và nêu được đánh đổi của mỗi hướng.
2. Giải thích **thang editability 6 mức** và vì sao nó có **knockout rule**.
3. Biết vì sao **master-based (L3)** là ngưỡng quan trọng nhất trong thực tế: đó là chỗ "đổi một lần, áp cả deck" trở nên khả thi.
4. Hiểu **executability** là thuộc tính của tầng representation + rendering, và phải báo cáo riêng.
5. Nêu được **cái gì được biết cái gì** (ranh giới thông tin): tầng trái không biết định dạng xuất, tầng phải không biết nguồn gốc.
6. Biết rằng chọn representation là quyết định **khó đảo ngược nhất** trong cả hệ thống.

### 5. Papers cần đọc

#### MUST-READ

##### `SlidesGen-Bench` — **lần 2** (Stage 1 đọc để biết editability tồn tại như một trục; ở đây đọc như một đặc tả kỹ thuật)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 tôi đọc để biết "editability là một chiều chất lượng". Lần này tôi đọc **PEI như một bản đặc tả cho representation của mình** — mỗi mức là một yêu cầu kỹ thuật cụ thể phải thiết kế từ đầu, không thêm được về sau.

**Tôi cần lấy gì từ paper?**
Toàn bộ 6 mức, đọc như checklist thiết kế:
- **L0 Static** — slide là ảnh phẳng → representation của bạn **không được** dừng ở đây.
- **L1 Patchwork** — text tách rời sửa được; ảnh vẫn raster.
- **L2 Vector** — visual element là vector, sửa shape được.
- **L3 Structural** — **có tổ chức master-based phân cấp** (vd. `<p:sldMaster>`), cho phép **lan truyền layout toàn hệ thống**. ← đây là ngưỡng quyết định.
- **L4 Parametric** — table/chart là **native data object có dữ liệu nền**, không phải hình vẽ giả.
- **L5 Cinematic** — animation/multimedia hoạt động như narrative.
Cộng **knockout rule** và cách họ gate bằng 5 test T1–T5.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua 4 metric aesthetic (Stage 6) và QuizBank (đã dùng ở Stage 2). ⚠ Trạng thái verify không nhất quán trong repo (RQ01 E1 / RQ06 UNVERIFIED) → dùng **khung khái niệm**, verify trước khi trích số.

##### `AutoPresent` — **lần 3** (Stage 0: paradigm; Stage 1: tiêu chí; ở đây: hệ quả của việc chọn code làm representation)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần này tôi đọc nó như một **case study về một lựa chọn representation cụ thể** (code) và hậu quả đo được của lựa chọn đó.

**Tôi cần lấy gì từ paper?**
- Lập luận nhân quả: sinh **chương trình** cho kết quả *người dùng còn tương tác được*, sinh ảnh thì không. Đây là lý do representation quan trọng, phát biểu bằng kết quả thực nghiệm.
- **Executability** như một con số riêng, và quy tắc gán **0** cho slide không chạy được. Con số 2,1% → 89,2% giữa các model cho thấy: khi representation là code, **năng lực model trực tiếp trở thành rủi ro vận hành**. Đó là đánh đổi của hướng code-first.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua metric design (đã lấy ở Stage 1).

#### SHOULD-READ

##### `PPTAgent` — **lần 4** (ở đây: schema như một cơ chế giữ hệ thống chạy được)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Ba lần trước tôi đọc kiến trúc, bộ đo, và ablation-về-planning. Lần này tôi đọc **đúng một dòng ablation** và hỏi nó nói gì về representation.

**Tôi cần lấy gì từ paper?**
Dòng `w/o Schema`: SR **78,8%** (so với 95,0% khi đủ) / PPTEval 3,45. Đọc như sau: **bỏ tầng schema đi thì hệ thống hỏng ở trục "sinh ra được file", không phải ở trục "file có đẹp không"** — tức schema là hạ tầng độ tin cậy, không phải hạ tầng thẩm mỹ. Đối chiếu với `w/o CodeRender` (SR 74,6% nhưng PPTEval 3,66 gần như không đổi) để thấy cùng một mẫu hình.

**Không cần tập trung vào gì ở thời điểm này?**
Không cần chi tiết schema của họ trông ra sao — nó gắn với bài toán edit-deck-tham-chiếu của họ, không chuyển thẳng sang bạn.

##### `Open Design` — `docs/skills-protocol.md` (repo, **không phải paper**)

**Tại sao đọc nguồn này ở Stage này?**
Vì nó là **ví dụ ngược** duy nhất có thật và đang chạy ở quy mô lớn: một hệ thống chọn HTML-first và vẫn xuất được PPTX. Đọc nó để lựa chọn của bạn là một **quyết định** chứ không phải mặc định.

**Tôi cần lấy gì từ nguồn này?**
Đúng một câu trích: *"Preview: html... **Primary output: index.html. Secondary output: slides.json (for PPTX export)**"*. Và hệ quả kiến trúc mà repo rút ra: **HTML-first thì bản trình chiếu thật là HTML, JSON là phụ phẩm** — ngược hoàn toàn với IR-first. Tự trả lời: nếu bản gốc là HTML thì PPTX xuất ra nằm ở mức PEI nào?

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua BYOK proxy, MCP server, CLI spawning (thuộc Stage 7 về orchestration). ⚠ Repo ghi rõ **chưa verify** cơ chế chuyển `slides.json` → PPTX; và đây là **hệ thống**, không phải publication có peer review.

#### OPTIONAL / REFERENCE

- **`MemSlides`** — ghi tên vì multi-turn local revision là bài toán mà representation phải hỗ trợ (sửa cục bộ không phá phần còn lại). ⚠ Repo **chưa đọc** → không có nội dung để lấy, chỉ là con trỏ.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao lựa chọn representation là **khó đảo ngược nhất**, và ví dụ một tính năng bị chặn vĩnh viễn bởi lựa chọn sai.
- Ý nghĩa thực tế của **L3 master-based**: nó cho phép người dùng làm gì mà L2 không cho.
- Vì sao knockout rule tồn tại (đạt L4 mà fail L1 là vô nghĩa).
- Vì sao `w/o Schema` làm hỏng **success rate** chứ không phải **điểm thẩm mỹ**.
- Đánh đổi giữa IR-first và HTML-first, phát biểu bằng **cái gì bạn được và cái gì bạn mất**, không phải bằng "cái nào tốt hơn".

### Implementation checkpoint

**Đã có thể làm:** thiết kế và implement **schema IR** thật — slide, block, vai trò của block, tham chiếu tới key fact nguồn (từ Stage 2), tham chiếu tới design token (chưa cần giá trị màu thật, chỉ cần tên token). Cộng với: viết **exporter tối thiểu** đi từ IR ra PPTX **có dùng slide master**, và viết một test kiểm PEI ở mức bạn nhắm tới (mở file, kiểm text là text object, kiểm layout kế thừa từ master).

**Chưa nên làm:** chưa nên tối ưu chất lượng thị giác của exporter — Stage 6 sẽ cho bạn biết đo cái gì. Chưa nên xây Editor Loop (Stage 7) dù bây giờ IR đã sẵn sàng cho nó: bạn còn thiếu phần "critique thế nào là đáng tin".

---

## Stage 6 — `Layout & visual: cái gì đo được bằng hình học, cái gì không`

> ⚠ **Đây là Stage mà corpus paper hiện có yếu nhất.** Nó dạy bạn rất tốt cách **phát hiện** layout hỏng, và gần như không dạy gì về cách **sinh** layout đẹp. Tôi nói rõ ranh giới đó bên trong Stage, và mở rộng ở §6 Gap 1–3.

### 1. Tôi đang cố hiểu vấn đề gì?

Một slide "trông xấu". Câu hỏi: **xấu vì cái gì?** Và câu hỏi thật sự hữu ích: **cái nào trong đó máy tính kiểm được?**

Hoá ra "chất lượng thị giác" tách được thành hai loại rất khác nhau:

- **Lỗi hình học** — thuộc tính tính được từ **toạ độ và kích thước** của các element: chữ tràn ra ngoài khung, hai hộp chồng lên nhau, element lệch hàng, lề không đều, tỉ lệ khung sai, khoảng trắng thừa quá lớn, trọng tâm thị giác lệch. Những cái này là **bất đẳng thức trên số**, không phải chuyện thẩm mỹ.
- **Vấn đề thẩm mỹ** — bảng màu có hợp không, font có hợp ngữ cảnh không, bố cục có "sang" không. Những cái này cần phán đoán.

Một vài thuật ngữ sẽ gặp:
- **Bounding box** — hình chữ nhật bao quanh một element, cho bởi (x, y, rộng, cao). Gần như mọi kiểm tra hình học đều là phép toán trên bounding box.
- **Visual hierarchy** — mức độ rõ ràng của thứ tự "nhìn cái gì trước": tiêu đề phải nổi hơn nội dung.
- **Contrast** — độ tương phản giữa chữ và nền, tính được từ độ sáng tương đối (relative luminance) của hai màu.

### 2. Tại sao tôi cần biết điều này?

Vì đây là chỗ **rẻ nhất và chắc chắn nhất** để cải thiện chất lượng cảm nhận — và cũng là chỗ dễ giao nhầm việc cho model nhất.

`EXTERNAL EVIDENCE` cực kỳ rõ ràng, và từ **hai nguồn độc lập**:

- **SlideAudit**: LLM phát hiện lỗi thiết kế đạt F1 chỉ **0,331–0,655**. Zero-shot 0,476–0,519; kể cả khi cấp full taxonomy + ảnh + object description thì tốt nhất cũng chỉ **0,589–0,655**. Tức **cấu trúc hoá prompt giúp nhưng không cứu được**.
- **AeSlides**: tác giả lập luận nhiều thuộc tính layout *"are inherently structured and can be precisely verified through programmatic analysis"* và **bác bỏ VLM-based reward** vì trong test của họ model *"performed worse than random guessing on layout detection"*. (⚠ repo ghi claim này mới đọc qua tool summary, **chưa quote-verified**.)

Ghép lại thành một nguyên tắc thiết kế (`RECOMMENDATION`): **cái gì quy được về predicate hình học thì viết code, đừng gọi model.** Không phải vì code "tốt hơn AI" nói chung, mà vì trên đúng lớp bài toán này, số đo đúng còn model thì đoán.

Và có lý do thứ hai, sâu hơn: **những kiểm tra hình học này là thứ bạn cắm vào self-critique loop ở Stage 7.** Một reward tính bằng code thì tin được và miễn phí; một reward do VLM chấm thì vừa đắt vừa dưới mức ngẫu nhiên.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn muốn bảo đảm slide không bị tràn chữ. Bạn viết prompt: *"đảm bảo text vừa trong khung, không chồng lấn"*, rồi thêm một bước cho MLLM xem ảnh render và hỏi *"slide này có lỗi layout không?"*.

Kết quả: model trả lời "không có lỗi" trên những slide **có** lỗi, ở tỉ lệ khớp với con số F1 0,331–0,655 của SlideAudit. Bạn giao 100 deck cho người dùng, một phần đáng kể có chữ tràn ra ngoài, và **hệ thống kiểm tra của bạn đã bảo là ổn**.

Trong khi đó, cùng lỗi ấy, AeSlides cho thấy phát hiện được bằng heuristic trên cây element: bounding box chồng nhau giữa element không liên quan, element tràn ra ngoài container cha, và element vượt ranh giới slide. Ba phép so sánh toạ độ. Không tốn một token nào, và không sai.

**Cái bẫy đối xứng** cũng cần nói: nếu bạn tưởng *mọi* thứ đều quy về hình học được, bạn sẽ viết một bộ rule và tuyên bố "deck của tôi đạt chuẩn thiết kế". Không. Bộ rule chỉ chứng minh **không có lỗi thô**. Nó không chứng minh deck đẹp — và corpus này không có nguồn nào dạy bạn phần còn lại.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Chia được một danh sách lỗi thiết kế thành **kiểm-bằng-code** vs **cần-phán-đoán**, và biết lý do cho từng cái.
2. Biết **ít nhất 4 phép kiểm hình học cụ thể** và cách tính chúng.
3. Biết bằng chứng số rằng **MLLM kém ở việc phát hiện lỗi thiết kế**, và không giao việc đó cho model.
4. Phân biệt **"không có lỗi"** với **"đẹp"** — và biết corpus hiện tại chỉ dạy được vế đầu.
5. Biết rằng nếu dùng một **design system cố định**, phần lớn "design consistency" là thuộc tính của design system chứ không phải của AI.
6. Biết chính xác **chỗ paper hết** và engineering knowledge bắt đầu.

### 5. Papers cần đọc

#### MUST-READ

##### `AeSlides: Incentivizing Aesthetic Layout in LLM-Based Slide Generation via Verifiable Rewards`

**Tại sao đọc paper này ở Stage này?**
Vì đây là nguồn duy nhất trong corpus đưa ra **một bộ kiểm layout tính hoàn toàn bằng chương trình**, kèm lập luận vì sao **không** dùng VLM cho việc này. Đây là paper trung tâm của Stage 6.

**Tôi cần lấy gì từ paper?**
Bốn metric, đọc như bốn hàm cần implement:
- **Distorted Aspect Ratio** — so tỉ lệ render thật với target (vd. 16:9).
- **Excessive Whitespace** — local variance map trên ảnh grayscale + box filter + threshold. (Ý tưởng: vùng nào biến thiên gần bằng 0 trên diện rộng là vùng trống.)
- **Element Collision** — heuristic trên cây element, **3 loại**: bounding box chồng nhau giữa element không liên quan, element tràn ra ngoài container cha, element vượt ranh giới slide.
- **Visual Imbalance** — độ lệch của **visual centroid** so với tâm canvas.

Cộng lập luận nền: *"many fundamental aesthetic properties of slide layouts are inherently structured and can be precisely verified through programmatic analysis"*, và động cơ trong abstract: *"the generation process is text-centric, whereas its quality is governed by visual aesthetics"*.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua phần RL/reward training (Stage 7 sẽ dùng lại). ⚠ Claim *"VLM performed worse than random guessing"* — repo đọc qua tool summary, **chưa quote-verified**; dùng làm định hướng, đừng trích như số liệu.

##### `SlideAudit` — **lần 2** (Stage 1 đọc để lấy taxonomy; ở đây đọc để lấy **bảng số về ai nên đi tìm lỗi**)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 tôi lấy **danh sách lỗi**. Lần này tôi lấy **kết quả thực nghiệm về năng lực của LLM trong việc phát hiện những lỗi đó** — tức câu trả lời cho "giao việc này cho ai".

**Tôi cần lấy gì từ paper?**
- Dải F1 **0,331–0,655** và cách nó phân bố: zero-shot baseline 0,476–0,519; prompt kèm full taxonomy tốt nhất 0,589–0,655 (GPT-4o + image + object description); CrowdCrit 7-principles 0,577; CrowdCrit 70-critique **0,331**; UICrit adapted 0,477.
- Quan sát quan trọng: **cho nhiều tiêu chí hơn không làm tốt hơn** (70-critique tệ nhất). Đây là bằng chứng chống lại phản xạ "viết rubric dài hơn thì judge chính xác hơn".
- Dùng lại taxonomy 27 category, nhưng lần này **gán nhãn cho từng cái**: `code` / `judge` / `chưa rõ`.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết dataset và quy trình annotate Prolific.

#### SHOULD-READ

##### `SlidesGen-Bench` — **lần 3** (Stage 1: editability tồn tại; Stage 5: PEI như spec; ở đây: 4 metric aesthetic deterministic)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Hai lần trước tôi hoàn toàn bỏ qua phần Aesthetics. Lần này đó là phần duy nhất tôi đọc — vì nó là bộ metric thị giác **deterministic** thứ hai, độc lập với AeSlides, và chúng bổ sung nhau (AeSlides thiên về **hình học**, cái này thiên về **màu và nhịp**).

**Tôi cần lấy gì từ paper?**
- **Harmony Score** — mức khớp của bảng màu với các hue template trong không gian HSV.
- **Engagement Score** — dùng opponent color channel (rg / yb) và biến thiên của chúng **theo chuỗi slide**.
- **Usability Score** — tương phản figure-ground tính bằng **relative luminance BT.709**. Đây là cái thực dụng nhất: nó là tiêu chí *đọc được hay không*, không phải tiêu chí thẩm mỹ.
- **Visual Rhythm / VisualHRV** — subband entropy đo mức lộn xộn (clutter) + RMSSD đo biến thiên **giữa các slide**.

Điều đáng học nhất không phải công thức mà là **hai metric cuối đo trên CHUỖI slide**, không đo trên một slide. Chất lượng thị giác của deck có một thành phần **liên slide** mà bạn sẽ bỏ sót nếu chỉ kiểm từng slide riêng lẻ.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua PEI và QuizBank. ⚠ `INFERENCE` của repo cần nhớ: nếu bạn dùng **một design system cố định** thì phần lớn các metric màu này đo **thuộc tính của design system**, không phải năng lực của AI — chúng gần như hằng số qua các phiên bản, nên **kém hữu dụng để tracking tiến bộ**.

##### `AutoPresent` — **lần 4** (ở đây: tiêu chí layout/color như một rubric có sẵn)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần này tôi chỉ lấy **hai ô trong Table 1** và đọc chúng như đặc tả kỹ thuật, không như tiêu chí chấm điểm.

**Tôi cần lấy gì từ paper?**
- *Layout*: "Elements should be aligned, do not overlap, and have sufficient margins to each other. All elements should not exceed the page." → bốn predicate: aligned / not overlap / margin ≥ ngưỡng / trong khung.
- *Color*: "Use high-contrast color especially between the text and the background. Avoid using high-glaring colors." → phần đầu tính được (luminance contrast), phần sau ("glaring") **không** tính được — một ví dụ đẹp về ranh giới code/judge nằm ngay giữa một câu.
- Con số **human ceiling**: reference slide do người làm chỉ đạt layout **73,5** / color **65,7**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua reference-based metric.

#### OPTIONAL / REFERENCE

- **`Application of multimedia theory to PowerPoint slides` (Garner et al. 2009)** — đọc để có **mốc thực nghiệm** cho mật độ nội dung: trung bình **7,5 dòng / 33,4 từ mỗi slide**, ~35 từ/phút người xem tiếp nhận, **dưới một nửa** slide có hình. ⚠ Đây là **mô tả thực trạng, không phải chuẩn** — khác corpus, khác năm 2009, khác ngôn ngữ → **không** dùng làm ngưỡng pass/fail.
- **`EfficientPosterGen`** — chỉ là con trỏ: có phần "violation detection" nghe như kiểm layout deterministic, có thể củng cố AeSlides. ⚠ Repo **chưa đọc**.
- **`Advancing the Knowledge Base on Effective Presentation Slide Design` (Wolfe et al. 2024)** — con trỏ tới khả năng **phản bác** các guideline phổ biến (6×6 rule, "ít chữ luôn tốt hơn"). ⚠ Repo **fetch thất bại, chưa verify nội dung** — nêu ở đây vì nếu bạn định dùng words/slide làm tiêu chí chất lượng thì đây là nguồn đầu tiên cần đọc.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao "không chồng lấn" là bài toán so sánh toạ độ chứ không phải bài toán thị giác.
- Bằng chứng số vì sao **không** giao việc phát hiện lỗi layout cho MLLM, và vì sao rubric dài hơn không cứu được.
- Vì sao có metric thị giác phải đo **trên chuỗi slide** chứ không trên từng slide.
- Sự khác nhau giữa "deck không có lỗi thô" và "deck đẹp" — và vì sao corpus này chỉ giúp được vế đầu.
- Vì sao một design system cố định làm phần lớn metric màu trở nên **vô dụng để tracking tiến bộ**.

### Implementation checkpoint

**Đã có thể làm:** implement một **layout linter** hoàn chỉnh chạy trên IR hoặc trên file xuất — collision (3 loại), overflow ngoài slide, alignment, margin tối thiểu, aspect ratio, contrast chữ/nền theo relative luminance, tỉ lệ khoảng trắng. Cộng: một báo cáo lỗi theo taxonomy SlideAudit để biết deck của mình hỏng ở đâu nhiều nhất. Đây là **thành phần có ROI cao nhất trong toàn roadmap** — rẻ, chắc chắn, và tái dùng được ngay ở Stage 7 làm reward.

**Chưa nên làm:** ⚠ **chưa nên tự viết layout generator**, và không phải vì bạn chưa đủ giỏi — mà vì **corpus paper hiện có không dạy điều đó** (xem §6, Gap 1). Ở giai đoạn này, đường đi có evidence là **mượn layout**: template catalog cố định (kiểu Open Design: *"populate slide từ layout catalog"*) hoặc deck tham chiếu (kiểu PPTAgent). Sinh layout từ đầu là một bài toán riêng thuộc lĩnh vực khác, và bạn cần đọc ngoài repo này trước khi chạm vào nó.

---

## Stage 7 — `Self-critique: cho agent tự sửa mình mà không tự làm hỏng mình`

### 1. Tôi đang cố hiểu vấn đề gì?

**Self-critique loop** = agent sinh ra một bản nháp, tự xem lại, tự tìm lỗi, tự sửa, lặp lại. Nghe hiển nhiên là tốt. Trong thực tế nó là một trong những chỗ dễ làm hỏng hệ thống nhất.

Cấu trúc của một vòng lặp như vậy luôn gồm ba phần, và mỗi phần hỏng theo cách riêng:
- **Signal** — cái gì nói cho agent biết "chỗ này sai";
- **Localization** — sai **ở đâu** (slide nào, element nào);
- **Repair** — sửa thế nào mà **không phá** những phần đang đúng.

Một khái niệm liên quan bạn sẽ gặp: **verifiable reward** — tín hiệu phản hồi tính được bằng chương trình, đối lập với **model-judged reward** — tín hiệu do một model khác chấm.

### 2. Tại sao tôi cần biết điều này?

Vì **chất lượng của vòng lặp bị chặn trên bởi chất lượng của tín hiệu**, và bạn đã có bằng chứng số về chất lượng tín hiệu từ ba Stage trước:

- Tín hiệu **hình học** (Stage 6): chính xác, miễn phí, nhưng chỉ phủ lỗi thô.
- Tín hiệu **design từ MLLM** (Stage 6): F1 0,331–0,655 → **dưới mức dùng được cho một vòng lặp tự động**.
- Tín hiệu **fidelity** (Stage 2): dùng được, nhưng **game được** — và một vòng lặp tối ưu chính là một cỗ máy tìm cách game.

`INFERENCE` (rất quan trọng, và tôi nói rõ đây là suy luận): **nếu bạn cắm một tín hiệu game được vào một vòng lặp tối ưu, bạn không tạo ra một agent tốt hơn — bạn tạo ra một agent giỏi game metric hơn.** Ví dụ cụ thể ghép từ Stage 2 và Stage 6: reward fidelity thưởng cho việc chép nguyên văn câu từ nguồn (`EXTERNAL EVIDENCE`: gaming cho cải thiện gấp 3–8 lần cải thiện thật) → vòng lặp sẽ **nhồi chữ vào slide**, và linter hình học của bạn sẽ báo overflow, và hai tín hiệu đánh nhau.

Lý do thứ hai: **nhiều vòng lặp hơn không đơn điệu tốt hơn.** `EXTERNAL EVIDENCE`: Chen, Zaharia & Zou đo thấy performance của compound system có thể **tăng rồi giảm** theo số lần gọi model, vì độ khó không đồng nhất giữa các query — thêm call giúp case dễ nhưng **hại** case khó. (⚠ Repo ghi rõ: kiến trúc họ nghiên cứu là voting/filtering trên **cùng một task**, nên áp sang pipeline nhiều component là **INFERENCE mở rộng**.)

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn thêm một vòng self-critique: sau khi sinh deck, đưa ảnh render cho MLLM và hỏi *"tìm lỗi và đề xuất sửa"*. Model trả về 5 nhận xét. Bạn áp dụng cả 5.

Điều thực tế xảy ra, theo đúng những con số đã biết: khoảng một nửa số "lỗi" là **false positive** (F1 0,331–0,655 nghĩa là cả precision lẫn recall đều kém), nên bạn vừa **sửa những chỗ vốn đúng**. Deck vòng 2 tệ hơn deck vòng 1. Bạn chạy thêm vòng 3, nó dao động tiếp. Bạn tốn 3× chi phí để có kết quả tệ hơn và **không có cách nào biết** vì bạn đang dùng chính model đó để đánh giá.

Phiên bản đắt hơn cho sản phẩm thật: vòng lặp sửa slide 4 và **vô tình đổi cả bố cục slide 7** mà người dùng đã hài lòng và đã khoá. Đây là lý do khả năng **localization** và cơ chế **lock** không phải tính năng phụ.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Phân biệt **verifiable reward** với **model-judged reward**, và biết cái nào cắm được vào vòng lặp tự động.
2. Biết vì sao **chất lượng vòng lặp bị chặn bởi chất lượng tín hiệu**, và tại sao Stage 1/2/6 phải đến trước.
3. Hiểu ba phần signal / localization / repair và biết phần nào yếu nhất trong evidence hiện có (**localization**).
4. Biết rằng **thêm vòng lặp không đơn điệu tốt hơn**, và cần một tiêu chí dừng.
5. Biết một self-correction **có định nghĩa vận hành được**: "sinh đủ mọi slide không lỗi thực thi **sau self-correction**".
6. Nhận diện được **reward hacking** trong chính hệ thống của mình.

### 5. Papers cần đọc

#### MUST-READ

##### `AeSlides` — **lần 2** (Stage 6 đọc 4 metric như bộ kiểm; ở đây đọc chúng như **reward**)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 tôi lấy 4 metric để **phát hiện** lỗi. Lần này tôi đọc phần mà lần trước tôi bỏ qua: **họ dùng chính 4 metric đó làm tín hiệu huấn luyện/cải thiện**, và họ **cố tình từ chối** dùng VLM cho vai trò này. Đây là câu trả lời trực tiếp cho "signal của vòng lặp nên là gì".

**Tôi cần lấy gì từ paper?**
- Ý tưởng **verifiable reward**: dùng tín hiệu tính bằng chương trình làm phản hồi thay vì tín hiệu do model chấm.
- Lý do họ nêu trong abstract: giải pháp hiện có *"typically rely either on heavy visual reflection, which incurs high inference cost yet yields limited gains"* — dịch: **vòng lặp reflection bằng model vừa đắt vừa ít tác dụng**. Đó chính là cái bẫy ở mục 3.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết thuật toán RL nếu bạn không train model — bạn dùng cùng tín hiệu đó trong một vòng lặp prompt-based cũng được.

##### `SlideAudit` — **lần 3** (Stage 1: taxonomy; Stage 6: ai đi tìm lỗi; ở đây: hệ quả cho vòng lặp)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần này tôi không đọc để biết LLM kém — tôi đã biết. Tôi đọc để **quyết định kiến trúc vòng lặp**: với F1 0,331–0,655, một critic MLLM tạo ra bao nhiêu false positive, và tôi chịu được bao nhiêu?

**Tôi cần lấy gì từ paper?**
Chỉ dải F1 và một suy luận số học đơn giản bạn tự làm: nếu critic của bạn có precision ~0,5 thì **một nửa số sửa chữa là sửa nhầm**. Một vòng lặp áp dụng mọi đề xuất của critic đó sẽ **hội tụ về đâu**? Đây là câu hỏi bạn phải tự trả lời trước khi viết dòng code đầu tiên của loop.

**Không cần tập trung vào gì ở thời điểm này?**
Không cần gì thêm — paper này ở Stage 7 chỉ đóng vai một ràng buộc số.

##### `AutoPresent` — **lần 5** (ở đây: self-refinement có tác dụng, và trong điều kiện nào)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Bốn lần trước tôi đọc paradigm, tiêu chí, executability, và representation. Lần này tôi đọc **kết quả về self-refinement** — vì đây là nguồn trong corpus cho thấy vòng lặp **có** hiệu quả, và cần đọc nó cạnh AeSlides/SlideAudit để không kết luận một chiều.

**Tôi cần lấy gì từ paper?**
Kết luận rằng **self-refinement lặp cải thiện chất lượng** trong setup của họ — và đặc biệt là **bối cảnh** của kết luận đó: output là **chương trình**, nên vòng lặp có một tín hiệu cực mạnh và cực rẻ là **chương trình có chạy không**. `INFERENCE`: vòng lặp hiệu quả khi tín hiệu là nhị phân và khách quan (chạy/không chạy), và kém hiệu quả khi tín hiệu là phán đoán thẩm mỹ. Đây là hoà giải giữa AutoPresent và AeSlides, không phải mâu thuẫn.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua mọi thứ khác.

#### SHOULD-READ

##### `PPTAgent` — **lần 5** (ở đây: self-correction như một phần định nghĩa thành công)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần này tôi chỉ đọc **định nghĩa Success Rate** và để ý một cụm từ mà bốn lần trước tôi lướt qua.

**Tôi cần lấy gì từ paper?**
Cụm từ đó: *"success requires the generation of all slides without execution errors **after self-correction**"*. Nghĩa là self-correction được coi là **một phần của hệ thống**, không phải một tính năng phụ — và thành công được đo **sau** khi nó đã chạy. Đây là cách đặt vấn đề đúng: vòng lặp không phải để "làm đẹp thêm", mà để **đạt tới trạng thái hợp lệ**.

**Không cần tập trung vào gì ở thời điểm này?**
Không gì thêm.

##### `Learning to Present` — **lần 2** (Stage 4 đọc để lấy inverse specification; ở đây đọc cấu trúc reward)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 tôi chỉ lấy cơ chế "khôi phục spec từ deck". Lần này tôi đọc **toàn bộ hệ 6 reward** như một ví dụ về **cách trộn tín hiệu deterministic và tín hiệu model-judged trong một hệ**, kèm trọng số.

**Tôi cần lấy gì từ paper?**
Cấu trúc và trọng số, đọc như một mẫu thiết kế:
- Code Rules (w 1.0) — **deterministic**: có title, số section, tỉ lệ word count, section được điền.
- Render Quality (w 2.0) — **deterministic**: số slide, render PNG thành công, HTML element hợp lệ.
- Aesthetic HTML (w 1.5) — model-judged trên HTML/CSS thô.
- Aesthetic Visual (w 1.5) — model-judged trên ảnh render.
- Content Quality (w 2.0) — hỗn hợp.
- Inverse Specification (w 2.0) — model-judged.

Điều đáng học: **hai trọng số cao nhất (2.0) gồm một cái thuần deterministic (Render Quality)**, và họ **tách bạch tường minh** cái nào deterministic cái nào không. Đó là kỷ luật thiết kế bạn nên copy.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua con số spec_reconstruction. ⚠ Repo: **idea source, không phải authoritative benchmark**; và chính tác giả **thừa nhận rủi ro gaming nhưng thảo luận rất ít**.

##### `Are More LLM Calls All You Need? Towards Scaling Laws of Compound Inference Systems`

**Tại sao đọc paper này ở Stage này?**
Vì nó là nguồn duy nhất trong corpus nói về **hình dạng của đường cong "thêm bước xử lý"** — và nó không phải đường cong tăng đơn điệu.

**Tôi cần lấy gì từ paper?**
Một kết quả và một cơ chế: performance có thể **tăng rồi giảm** theo số lần gọi; nguyên nhân là **độ khó không đồng nhất giữa các query** — thêm call giúp query dễ, hại query khó. Hệ quả cho bạn: **phải có tiêu chí dừng**, và phải kiểm tra vòng lặp có làm **case khó tệ đi** không, chứ không chỉ nhìn trung bình.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua scaling model của họ. ⚠ Repo ghi rõ: kiến trúc họ nghiên cứu là **voting/filtering trên cùng một task**, không phải pipeline nhiều component khác chức năng → dùng nó ở đây là **INFERENCE mở rộng**, phải nói rõ khi trích.

#### OPTIONAL / REFERENCE

- **`Presenting a Paper is an Art` / EvoPresent** — con trỏ tới một kiến trúc self-improvement có **Checker Agent** riêng dựa trên model chấm thẩm mỹ. ⚠ Repo **chưa đọc paper** → chỉ là hướng tham khảo, không phải evidence. Đọc nếu bạn muốn xem một hệ thống đặt critic thành component độc lập.
- **`Open Design` — deck workflow** — precedent production cho self-check: *làm rõ topic + số slide → chọn theme → populate slide từ layout catalog → **self-check theo một quality rubric***. ⚠ Repo ghi rõ chi tiết rubric này đến từ **nguồn thứ cấp, chưa verify trực tiếp trong file skill**.
- **`MemSlides`** — con trỏ tới **multi-turn local revision**, tức phần "repair mà không phá chỗ khác". ⚠ Repo chưa đọc. Đây cũng là chỗ để nhắc: **localization là phần yếu nhất của evidence hiện có** — FineSurE đo GPT-4 chỉ đạt **42,2%** khi phải nói *loại lỗi nào* dù detect nhị phân đạt 86,4%.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao một vòng lặp tự sửa có thể làm deck **tệ đi**, kèm số học từ F1 của critic.
- Vì sao tín hiệu tốt nhất cho vòng lặp là tín hiệu **nhị phân, khách quan, rẻ** (chạy được / không chạy được, overflow / không overflow).
- Reward hacking là gì và hệ thống của tôi sẽ hack theo hướng nào nếu tôi cắm metric fidelity vào vòng lặp.
- Vì sao cần **tiêu chí dừng**, và vì sao nhìn điểm trung bình không phát hiện được vòng lặp đang hại case khó.
- Vì sao **localization** khó hơn **detection**, và điều đó ràng buộc thiết kế Editor Loop ra sao.

### Implementation checkpoint

**Đã có thể làm:** implement một **self-correction loop hẹp và đáng tin** — chỉ dùng tín hiệu deterministic: (1) export thất bại → sửa; (2) linter hình học báo overflow/collision → sửa đúng element đó; (3) verifiable instruction vi phạm (sai số slide) → sửa. Kèm: giới hạn số vòng, log lại **mọi** thay đổi giữa các vòng, và một test kiểm loop **không** đụng vào slide đã lock.

**Chưa nên làm:** chưa nên cắm critic thẩm mỹ bằng MLLM vào vòng lặp tự động — bằng chứng nói nó dưới ngưỡng dùng được. Nếu vẫn muốn dùng, hãy để nó ở chế độ **gợi ý cho người**, không phải **tự động áp dụng**. Và chưa nên cắm metric fidelity làm reward cho tới khi bạn đã tự chạy thử nghiệm gaming ở Stage 2.

---

## Stage 8 — `Evaluation như một hạng mục kỹ thuật: judge, checklist, độ tin cậy`

### 1. Tôi đang cố hiểu vấn đề gì?

Tới đây bạn đã đo được nhiều thứ bằng code. Nhưng vẫn còn một lớp câu hỏi mà code không trả lời được: *"deck này có mạch lạc không?"*, *"nội dung có súc tích không?"*, *"nó có phù hợp với người nghe không?"*. Cách duy nhất hiện có là dùng **một model khác để chấm** — gọi là **LLM-as-a-judge** (và **MLLM-as-a-judge** khi model được nhìn ảnh).

Vấn đề: judge **không phải một thước đo, nó là một hệ thống AI khác**, với sai số, thiên lệch, và độ dao động riêng. Ba khái niệm cần biết:

- **Protocol** — cách hỏi judge. Ba dạng chính: *pointwise scoring* (cho điểm 1–5 cho một deck), *pairwise comparison* (so hai deck, chọn cái tốt hơn), *batch ranking* (xếp hạng nhiều deck).
- **Rubric vs checklist** — *rubric* là tiêu chí chung áp cho mọi deck ("nội dung phải súc tích, thang 1–5"). *Checklist per-instance* là danh sách câu hỏi nhị phân **soạn riêng cho từng đầu vào** ("deck có nhắc tới kết quả thí nghiệm ở bảng 3 không? Có/Không").
- **Agreement / correlation** — mức khớp giữa điểm của judge và điểm của người. Đây là cách duy nhất biết judge có đáng tin không.

### 2. Tại sao tôi cần biết điều này?

Vì nếu không biết, bạn sẽ **đặt kỳ vọng sai ở mức sai vài lần**, và mọi kết luận về hệ thống của bạn sẽ dựa trên một thước đo mà bạn tưởng chính xác hơn thực tế.

`EXTERNAL EVIDENCE` hiệu chỉnh kỳ vọng, xếp theo thứ tự gây sốc:

1. **Người chấm người còn không đồng ý với nhau.** PPTEval: 4 nghiên cứu sinh chấm 250 presentation, **Fleiss' κ ≈ 0,59** (Content 0,61 / Design 0,61 / Coherence 0,54) — **dưới cả ngưỡng "tentative" 0,667** của Krippendorff. Tức trong chính domain này, "sự thật" đã mờ sẵn.
2. **Judge tốt nhất đạt khoảng ρ ≈ 0,5.** G-Eval 0,514 trên summarization; PresentBench 0,532 trên slide deck với **trần human–human 0,664**. Không phải 0,9.
3. **Trục "coherence/structure" là trục judge yếu nhất.** PPTEval human correlation: Design **0,90** / Content 0,70 / **Coherence 0,55**. Và JudgeSense độc lập báo coherence là dimension **bất ổn nhất** dưới paraphrase prompt.
4. **Protocol quan trọng hơn model.** MLLM-as-a-Judge: MLLM thể hiện *"remarkable human-like discernment in Pair Comparison"* nhưng *"significant divergence... in Scoring Evaluation and Batch Ranking"*.
5. **Model mạnh hơn không ổn định hơn.** JudgeSense: *"the largest and newest models are not the most consistent"*. Yang et al.: năng lực cao *"often uncorrelated, or even negatively correlated, with low SPB"*. Soumik: model tier trung + debiasing đạt agreement cao nhất ở chi phí rẻ hơn ~15×. **Ba nguồn độc lập cùng một hướng.**

Ghép lại: nếu bạn viết "hệ thống của chúng tôi đạt 4,2/5 theo LLM judge" mà không biết những điều trên, con số đó **không mang thông tin**.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn dựng judge: đưa ảnh render của deck cho một model mạnh, xin điểm 1–5 trên 4 chiều. Chạy trên 20 deck của v0.3 → trung bình 3,8. Sau hai tuần cải tiến, chạy v0.4 → 4,0. Bạn kết luận cải tiến thành công.

Ba lỗ hổng, mỗi cái đủ để lật kết luận:
1. **Bạn chưa đo noise của judge.** `EXTERNAL EVIDENCE` (Tamba 2026): item gần ranh giới quyết định **lật pass/fail giữa các lần chạy giống hệt nhau**, và pin temperature = 0 **giảm nhưng không loại bỏ**. Delta 0,2 của bạn có thể nằm hoàn toàn trong nhiễu của judge.
2. **Bạn dùng protocol yếu nhất.** Scoring evaluation là đúng protocol mà MLLM lệch human nhiều nhất.
3. **Bạn có thể tự chấm mình.** Nếu model sinh deck và model chấm deck cùng họ, `EXTERNAL EVIDENCE` (Panickssery et al.) cho thấy có cơ chế nhân quả để nó **ưu ái output của chính mình**.

**Lỗ hổng thứ tư, tinh vi hơn:** bạn quyết định validate judge bằng cách cho 2 bạn cùng nhóm chấm 20 deck rồi tính correlation. `EXTERNAL EVIDENCE` (alt-test) nói **2 người là không đủ** — với 2 người, thủ tục chỉ kiểm LLM nghiêng về ai, **không có tín hiệu consensus**; cần **≥ 3**. Và Elangovan et al. cảnh báo thêm: khi nhãn người bất định cao (mà κ ≈ 0,59 nghĩa là **rất** bất định), nhãn máy có thể **trông như** tương quan tốt ngang người dù thực chất không phải.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Biết **vùng hoạt động thực tế** của LLM judge (ρ ≈ 0,5) và **trần người** trong domain này (κ ≈ 0,59 / ρ ≈ 0,664).
2. Chọn được **protocol** phù hợp với mục đích, và biết đánh đổi: pairwise tin hơn nhưng **không cho điểm tuyệt đối** → không tracking theo thời gian được.
3. Giải thích được vì sao **checklist nhị phân per-instance** đánh bại rubric holistic, và **cái giá** của nó.
4. Kể được **danh mục bias** (position, verbosity, self-preference, style) và biết mitigation nào ứng với bias nào.
5. Biết **cỡ mẫu và số annotator tối thiểu** để validate judge, và vì sao 2 người là không đủ.
6. Biết **đo noise của chính judge** như một health metric hạng nhất, báo cáo song song với điểm.
7. Biết những chỗ evidence **mâu thuẫn** và không giả vờ đã giải quyết.

### 5. Papers cần đọc

#### MUST-READ

##### `PresentBench` — **lần 2** (Stage 1 đọc để lấy taxonomy; ở đây đọc **judge protocol**)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 tôi lấy **5 chiều**. Lần này tôi lấy **cơ chế chấm** — phần mà lần trước tôi cố ý bỏ qua — vì đây là protocol judge tốt nhất có evidence trong đúng domain slide.

**Tôi cần lấy gì từ paper?**
- Cơ chế: mỗi instance có **checklist riêng do expert soạn** (trung bình **54,1 item**), mỗi item là **câu hỏi nhị phân**, **mỗi item một call judge riêng** (để giảm tải nhận thức), judge trả verdict **kèm localized evidence** (vd. slide index). Điểm dimension = trung bình các item; điểm cuối = trung bình **không trọng số** của 5 chiều.
- Chuẩn hoá đầu vào: slide → **PDF/ảnh** trước khi đưa cho judge multimodal.
- Con số: ρ ≈ **0,532** vs PPTEval **0,303** vs MLLM-as-a-Judge ranking **0,258**; trần human–human **0,664**. Human validation trên ~10% dataset (~24 instance).
- Giới hạn tác giả tự nhận: *"the evaluation still relies on multimodal LLMs as verifiers, and their capability may limit the reliability of the resulting scores."*

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua bảng xếp hạng hệ thống (Stage 9). ⚠ **Không được trích ablation của họ như bằng chứng nên bỏ Fidelity**: repo ghi rõ bỏ Fidelity cho ρ **cao hơn** (0,673) nhưng chính tác giả giải thích đây là **giới hạn của protocol user study** (người rank trong ~3 phút, không verify tính chính xác thực tế), không phải bằng chứng Fidelity vô dụng.

##### `Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena`

**Tại sao đọc paper này ở Stage này?**
Vì đây là **danh mục bias gốc** mà mọi thảo luận sau đó dựa vào. Không đọc nó thì bạn sẽ gặp lại các bias này dưới tên khác trong 5 paper khác mà không nhận ra chúng là cùng một thứ.

**Tôi cần lấy gì từ paper?**
Ba bias + một giới hạn, và **cơ chế** của từng cái: **position bias** (thiên vị vị trí xuất hiện), **verbosity bias** (thiên vị câu trả lời dài — chứng minh bằng "repetitive list attack"), **self-enhancement bias** (thiên vị output của chính mình), cộng **limited reasoning ability**. Và con số **>80% agreement** như một **cận trên lạc quan**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết Chatbot Arena. ⚠ Con số >80% là trên **chat response, protocol pairwise** — **không** suy ra được cho deck/multimodal.

##### `MLLM-as-a-Judge` — **lần 2** (nếu bạn đã lướt qua ở Stage 4; ở đây đọc kỹ để chọn protocol)

**Tại sao đọc paper này ở Stage này?**
Vì deck là artifact **multimodal**, và đây là nguồn peer-reviewed duy nhất so sánh trực tiếp ba protocol trên đúng loại artifact đó.

**Tôi cần lấy gì từ paper?**
Kết luận định tính (và **chỉ** kết luận định tính): MLLM mạnh ở **Pair Comparison**, lệch human rõ ở **Scoring Evaluation** và **Batch Ranking**. Cộng danh sách failure mode: *"diverse biases, hallucinatory responses, and inconsistencies in judgment"* — kể cả với model mạnh nhất thời điểm đó.

**Không cần tập trung vào gì ở thời điểm này?**
⚠ **Không dùng con số % nào**: repo ghi rõ trích xuất **không nhất quán giữa hai lần fetch** (79,3% vs 78%). Chỉ kết luận định tính là nhất quán.

#### SHOULD-READ

##### `TICKing All the Boxes` (TICK)

**Tại sao đọc paper này ở Stage này?**
Vì PresentBench dùng checklist **do expert soạn tay** (54 item × 238 instance) — ngoài tầm với của một team nhỏ. TICK là **đường thứ ba**: checklist per-instance **sinh tự động**.

**Tôi cần lấy gì từ paper?**
Cơ chế TICK (LLM tự phân rã instruction thành chuỗi câu hỏi YES/NO), con số cải thiện **46,4% → 52,2%** exact agreement, và biến thể **STICK** dùng chính checklist đó cho self-refinement — tức nối thẳng sang Stage 7.

**Không cần tập trung vào gì ở thời điểm này?**
⚠ **Hiệu chỉnh kỳ vọng bắt buộc**: +5,8 điểm phần trăm **nhỏ hơn nhiều** so với khoảng cách PresentBench (0,532) vs PPTEval (0,303). **Không** suy ra checklist LLM-sinh mạnh ngang expert-authored — evidence hiện có gợi ý ngược lại. ⚠ Repo chưa trích xuất được danh sách tác giả.

##### `HealthBench` + `ExpertLongBench / CLEAR`

**Tại sao đọc hai nguồn này ở Stage này?**
Vì cả hai chứng minh cùng một **mô hình kiến trúc** mà bạn nên copy: **người/nguồn định nghĩa *cái gì* phải có — model làm việc *đối chiếu*.**

**Tôi cần lấy gì?**
Từ HealthBench: quy mô chứng minh pattern này dùng thật (5.000 hội thoại, 48.562 criteria, 262 bác sĩ, criteria **có trọng số**, grader là **model**). Từ CLEAR: phân vai rõ hơn nữa — expert viết rubric, LLM chỉ làm **checklist mapper**, trả `N/A` nếu vắng.

**Không cần tập trung vào gì ở thời điểm này?**
Quy mô HealthBench ngoài tầm với — dùng làm bằng chứng về **pattern**, không phải mục tiêu. ⚠ CLEAR: câu *"existing LLMs cannot yet replicate"* là **phát biểu định tính của tác giả**, không kèm thí nghiệm đối chứng → không trích như kết quả đo được.

##### `The Alternative Annotator Test` (alt-test)

**Tại sao đọc paper này ở Stage này?**
Vì nó là nguồn **duy nhất** trong corpus có con số thực hành cho câu hỏi "cần bao nhiêu người, bao nhiêu mẫu".

**Tôi cần lấy gì từ paper?**
- **≥ 3 annotator** — và **lý do**: với 2 người, thủ tục chỉ kiểm LLM nghiêng về ai, **không có tín hiệu consensus**.
- **50–100 instance** với ε = 0,2; **100–150** với ε = 0,1; **tối thiểu 30**.
- ε theo loại annotator: 0,2 expert / 0,15 skilled / 0,1 crowd-worker; và **IAA thấp → phải dùng ε nhỏ hơn**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chứng minh thống kê. ⚠ Đây là thủ tục cho **annotation task**; áp vào deck là **thích nghi**. 50–100 là điều kiện để *test có lực*, **không** đảm bảo LLM sẽ pass.

##### `Beyond correlation: The Impact of Human Uncertainty...` (Elangovan et al.)

**Tại sao đọc paper này ở Stage này?**
Vì nó ngăn bạn kết luận sai từ chính con số validation mà bạn vừa học cách tạo ra ở nguồn trên.

**Tôi cần lấy gì từ paper?**
Một mệnh đề: khi tỷ lệ mẫu có **bất định trong nhãn người** cao, nhãn máy có thể **trông như** tương quan tương đương hoặc tốt hơn tương quan người–người **dù thực chất không phải**; và hệ số kiểu Krippendorff α mang giả định về **hành vi người** mà hệ thống tự động không thoả. Hệ quả: **báo cáo phân bố bất đồng của người kèm với correlation**, không bao giờ chỉ một con số.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua chi tiết toán học.

##### `JudgeSense` + `G-Eval` + `PPTAgent` (**lần 6**, chỉ để lấy κ)

**Ba nguồn này ở Stage này dùng để làm gì?**
Chúng cùng phục vụ **một** việc: đặt kỳ vọng đúng, và biết trục nào bất ổn nhất.

**Tôi cần lấy gì?**
- G-Eval: **ρ = 0,514** của một method được coi là mạnh → **ρ ≈ 0,5 là vùng hoạt động thực tế**.
- JudgeSense: verdict đổi dưới paraphrase tương đương ngữ nghĩa; **coherence bất ổn nhất, factuality ổn định nhất**; model lớn hơn **không** ổn định hơn. → **đóng băng và version hoá judge prompt như một phần của benchmark**.
- PPTAgent (lần 6, chỉ một con số): **Fleiss' κ ≈ 0,59** giữa 4 người chấm 250 presentation, và human correlation theo chiều Design 0,90 / Content 0,70 / **Coherence 0,55**.

**Không cần tập trung vào gì ở thời điểm này?**
Không đọc lại gì khác từ ba nguồn này.

#### OPTIONAL / REFERENCE

- **`LLM Evaluators Recognize and Favor Their Own Generations`** — đọc để biết **cơ chế** của self-preference (tự-nhận-diện → ưu ái), tức lý do tách generator và judge sang **họ model khác nhau**. ⚠ Không chứng minh tách họ **loại bỏ** bias.
- **`Quantifying and Mitigating Self-Preference Bias of LLM Judges`** — bổ sung một lever **độc lập**: **cách hỏi** (phân rã đa chiều) giảm bias trung bình 31,5%. ⚠ Preprint.
- **`Large Language Models are not Fair Evaluators` (FairEval)** — hai mitigation có tên và có code cho position bias: **MEC** (sinh evidence trước rồi chấm) và **BPC** (chạy cả hai thứ tự rồi cân bằng). ⚠ Chỉ áp dụng cho protocol **pairwise**.
- **`Necessary but Not Sufficient` (Tamba)** — lý do bắt buộc **đo noise floor của judge bằng cách chạy lặp**, và báo cáo grader disagreement như health metric. ⚠ Preprint 1 tác giả, mẫu nhỏ → dùng mệnh đề định tính, **không trích số**.
- **`Judging the Judges` (Soumik)** — cảnh báo về **style bias** (thiên vị markdown hơn văn xuôi — rất liên quan vì deck *là* nội dung có cấu trúc) và bằng chứng judge đắt không tự động tốt hơn. ⚠ Preprint 1 tác giả → **không dùng một mình**.
- **`PoLL` vs `Nine Judges, Two Effective Votes`** — đọc **cả hai cùng lúc hoặc không đọc cái nào**. PoLL: panel model nhỏ vượt judge lớn đơn lẻ, rẻ hơn >7×. Kohli: panel 9 model chỉ bằng **~2 vote độc lập** vì sai cùng chỗ, và **judge đơn tốt nhất bằng hoặc vượt panel**. ⚠ **Mâu thuẫn chưa giải quyết** trong repo → đừng xây kiến trúc panel dựa trên một trong hai.
- **`Krippendorff 2004`** + **`Bonett & Wright 2000`** — tra cứu khi cần ngưỡng agreement và cách tính cỡ mẫu cho CI của correlation. ⚠ Ngưỡng Krippendorff trong repo chỉ ở mức **E2** (chưa đọc bản gốc), và **mâu thuẫn với thực tế domain** (κ ≈ 0,59 < ngưỡng tentative 0,667) → áp thẳng 0,8 là đặt mục tiêu không khả thi. ⚠ Bonett & Wright: repo **không trích xuất được bảng n cụ thể**.
- **`From Holistic Evaluation to Structured Criteria` (survey)** — bản đồ 3 paradigm dựng rubric (human expert / automated LLM 5 nhánh / human-in-the-loop) kèm cảnh báo *"low-quality rubrics... can actively mislead"*. ⚠ E2 survey → các hệ thống nó nhắc **chưa verify**, giữ ở mức discovery.
- **`From Generation to Judgment` (survey)** — bản đồ để **tìm** E1 gốc. Không dùng làm evidence.

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Vì sao **ρ ≈ 0,5** là kết quả bình thường chứ không phải thất bại, và trần thực tế nằm ở đâu.
- Vì sao **2 người chấm là không đủ** để validate judge, kèm lý do (không có tín hiệu consensus).
- Đánh đổi giữa pairwise và pointwise, phát biểu bằng **use case** chứ không bằng "cái nào tốt hơn".
- Vì sao checklist nhị phân per-instance mạnh hơn rubric holistic, và cái giá phải trả là gì.
- Bốn bias của judge, và mitigation ứng với từng cái.
- Vì sao một con số correlation cao **không** đủ để kết luận judge đáng tin.
- Chỗ nào trong evidence đang **mâu thuẫn** và tôi đang không kết luận.

### Implementation checkpoint

**Đã có thể làm:**
1. Dựng **judge harness** hoàn chỉnh: prompt được **version hoá và đóng băng**, protocol chọn có lý do, mỗi tiêu chí một call, output có evidence localization.
2. **Đo noise floor của judge**: chấm cùng một deck N lần, báo cáo phân bố — và báo cáo con số này **kèm** mọi điểm số về sau.
3. Dựng pipeline **checklist per-instance**: sinh checklist từ tài liệu nguồn (đường TICK), người rà lại một mẫu, model chấm từng item.
4. Thiết kế **human validation study đúng chuẩn**: ≥ 3 người, ≥ 50 instance, báo cáo cả correlation lẫn phân bố bất đồng giữa người.

**Chưa nên làm:** chưa nên công bố bất kỳ so sánh version nào dựa trên judge — bạn còn thiếu Stage 9 (biết delta bao nhiêu mới có nghĩa). Chưa nên xây panel nhiều judge (evidence mâu thuẫn). Chưa nên dùng cùng họ model cho generate và judge.

---

## Stage 9 — `So sánh và ablation: làm sao biết hệ thống thật sự tiến bộ`

### 1. Tôi đang cố hiểu vấn đề gì?

Bạn có v0.3 và v0.4. v0.4 đo được điểm cao hơn. Câu hỏi: **v0.4 có thật sự tốt hơn không?**

Ba loại so sánh khác nhau, và mỗi loại có cái bẫy riêng:
- **So version của chính mình theo thời gian** — cái bẫy: nhiễu.
- **So với một baseline** (một cách làm đơn giản hơn, để chứng minh phần phức tạp của bạn có giá trị) — cái bẫy: **strawman**, tức baseline yếu giả tạo.
- **So với hệ thống bên ngoài** — cái bẫy: hai hệ thống không cùng bài toán, không cùng model, không cùng cấu hình.

Và một công cụ riêng: **ablation** — bỏ đi một component rồi đo xem hệ thống tệ đi bao nhiêu, để biết component đó đóng góp gì.

### 2. Tại sao tôi cần biết điều này?

Vì đây là stage biến "tôi có cảm giác hệ thống tốt lên" thành "tôi chứng minh được". Và vì mỗi cái bẫy ở trên đều có `EXTERNAL EVIDENCE` cho thấy **cả một lĩnh vực** đã rơi vào nó:

- **Nhiễu**: Atil et al. — accuracy dao động tới ~15% giữa các run ở cấu hình deterministic.
- **Strawman**: Ferrari Dacrema et al. reproduce 18 neural method ở hội nghị top và thấy **6/7 method bị đánh bại bởi heuristic đơn giản khi baseline được tune tử tế**. Đây là ảo giác tiến bộ ở **quy mô cả một lĩnh vực**.
- **Biến không cân**: Musgrave et al. — cải thiện nhiều năm của deep metric learning là *"marginal at best"* khi so công bằng; nguyên nhân gồm backbone khác nhau, embedding size khác nhau, thủ thuật không được nhắc.
- **Prompt là biến ẩn**: FormatSpread — chênh **76 accuracy points** chỉ do đổi **format** prompt. Mizrahi et al. — template khác nhau đổi **cả thứ hạng tương đối** giữa các model.
- **Quy sai nguyên nhân**: Lipton & Steinhardt — *"emphasizing unnecessary modifications to neural architectures when gains actually stem from hyper-parameter tuning"*.

Và cái bẫy đặc thù của lĩnh vực này, đã gặp ở Stage 1 nhưng giờ mới dùng được: **đọc ablation trên một metric tổng hợp là sai.** PPTAgent `w/o CodeRender`: Success Rate sập **95,0 → 74,6%** trong khi PPTEval gần như **không đổi (3,67 → 3,66)**. Một metric gộp sẽ nói component đó vô dụng.

### 3. Nếu bỏ qua knowledge này thì hệ thống sẽ gặp vấn đề gì?

**Ví dụ cụ thể.** Bạn muốn chứng minh ContentPlanner của mình có giá trị. Bạn dựng baseline: một prompt duy nhất "đọc PDF này, sinh 10 slide", viết trong 5 phút. Baseline đạt 2,9; hệ thống của bạn đạt 3,6. Bạn viết vào báo cáo: "kiến trúc có planning vượt baseline 24%".

Hội đồng hỏi: *"các bạn đã thử tune prompt của baseline chưa?"*. Bạn chưa. `EXTERNAL EVIDENCE` nói rằng câu hỏi đó là chí mạng: Mizrahi et al. cho thấy đổi template đổi **cả thứ hạng tương đối**, và Ferrari Dacrema cho thấy đây là cách cả một lĩnh vực tự lừa mình suốt nhiều năm.

**Ví dụ thứ hai.** Bạn chạy ablation: bỏ ContentPlanner → điểm tổng giảm 0,3. Bỏ DesignSystem → giảm 0,2. Bạn cộng lại và nói "hai component đóng góp 0,5". `EXTERNAL EVIDENCE` (Chen, Zaharia & Zou) cảnh báo đóng góp **không cộng tuyến tính**; và PPTAgent cho thấy **các component hỏng theo trục khác nhau** nên "điểm tổng" giấu mất thông tin quan trọng nhất.

**Ví dụ thứ ba.** Bạn so với một sản phẩm thương mại đóng. Ba tháng sau họ đổi model bên dưới. Kết quả của bạn không tái lập được, và bạn **không biết** vì họ không công bố version.

### 4. Sau Stage này tôi phải hiểu được gì?

1. Biết **noise floor của hệ thống mình** và dùng nó làm ngưỡng: delta nhỏ hơn nó thì không phát biểu gì.
2. Biết một baseline **công bằng** cần những gì: cùng model tier, cùng ngân sách prompt effort, nhiều prompt paraphrase, cùng retry policy.
3. Biết **đọc ablation đúng cách**: theo từng trục, không trên metric tổng hợp, một thay đổi một lần.
4. Biết đóng góp component **không cộng tuyến tính**.
5. Biết những gì phải **freeze và version hoá** để kết quả tái lập được: prompt, model version, seed/temperature, cấu hình retry, phiên bản judge prompt, tài liệu nguồn.
6. Biết giới hạn của việc so với **hệ thống đóng**, và cách phát biểu claim cho đúng phạm vi.

### 5. Papers cần đọc

#### MUST-READ

##### `Non-Determinism of "Deterministic" LLM Settings` — **lần 2** (Stage 4 đọc để đo noise floor của conditioning; ở đây đọc như tiền đề của mọi so sánh)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Lần 1 tôi cần noise floor để biết khác biệt **giữa các chế độ** có thật không. Lần này tôi cần đúng khái niệm đó cho khác biệt **giữa các phiên bản** — cùng công cụ, câu hỏi khác.

**Tôi cần lấy gì từ paper?**
Chỉ hai thứ: mệnh đề *"none of the LLMs consistently delivers repeatable accuracy across all tasks"*, và ý tưởng **agreement rate qua N run** như một con số phải báo cáo. Từ đó rút ra quy tắc vận hành của riêng bạn: *mọi so sánh version phải chạy N lần và báo cáo spread*.

**Không cần tập trung vào gì ở thời điểm này?**
⚠ Vẫn **không mượn con số 15%**.

##### `Are We Really Making Much Progress?` (Ferrari Dacrema et al.)

**Tại sao đọc paper này ở Stage này?**
Vì nó là bằng chứng cứng nhất trong corpus rằng **baseline yếu tạo ra tiến bộ giả ở quy mô cả một lĩnh vực** — không phải lỗi cá biệt của một nhóm cẩu thả.

**Tôi cần lấy gì từ paper?**
Kết quả: reproduce 18 method publish ở hội nghị top; **6/7 method reproduce được thường bị đánh bại bởi heuristic đơn giản (nearest-neighbor / graph-based) khi baseline được tune tử tế**; method còn lại không nhất quán vượt được một linear method non-neural đã tune tốt. Đọc như một câu hỏi tự vấn: *baseline của tôi đã được tune bằng bao nhiêu effort so với hệ thống của tôi?*

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua toàn bộ nội dung recommender system. ⚠ Khác domain hoàn toàn → **argument chuyển được, số liệu không**.

##### `PPTAgent` — **lần 7 và cuối** (ở đây: bảng ablation như một mẫu phương pháp)

**Ở Stage này tôi đang đọc paper với mục đích gì khác với lần trước?**
Sáu lần trước tôi đọc kiến trúc, bộ đo, planning, schema, self-correction, và κ. Lần này tôi đọc **toàn bộ bảng ablation cùng lúc** — không phải để biết component nào quan trọng, mà để học **cách trình bày một ablation cho đúng**.

**Tôi cần lấy gì từ paper?**
Cả bảng, và đọc theo **cột**, không theo dòng:
| | SR (%) | PPTEval |
| --- | --- | --- |
| full | 95,0 | 3,67 |
| `w/o Outline` | 91,0 | 3,30 |
| `w/o Schema` | 78,8 | 3,45 |
| `w/o Structure` | 92,2 | 3,32 |
| `w/o CodeRender` | **74,6** | **3,66** |

Bài học: **mỗi component hỏng theo một trục khác nhau.** `w/o CodeRender` gần như không đổi chất lượng nhưng sập độ tin cậy. Nếu bạn chỉ có một cột, bạn mất thông tin đó. Thiết kế bảng ablation của mình theo đúng mẫu này: **giữ mọi thứ khác cố định (cùng LM/VM), bỏ một component mỗi lần, báo cáo nhiều trục**.

**Không cần tập trung vào gì ở thời điểm này?**
Không gì thêm — đây là lần đọc cuối của paper này trong roadmap.

#### SHOULD-READ

##### `State of What Art? A Call for Multi-Prompt LLM Evaluation` (Mizrahi et al.)

**Tại sao đọc paper này ở Stage này?**
Vì nó là evidence **trực tiếp nhất** cho việc "một prompt duy nhất cho baseline là thiết kế không đủ" — cái bẫy cụ thể nhất mà bạn sẽ rơi vào.

**Tôi cần lấy gì từ paper?**
Kết quả trên 6,5M instance × 20 LLM × 39 task: instruction template khác nhau đổi performance **cả tuyệt đối lẫn thứ hạng tương đối**. Hệ quả thực hành: baseline của bạn phải chạy trên **nhiều paraphrase** và báo cáo khoảng, không phải một con số.

**Không cần tập trung vào gì ở thời điểm này?**
⚠ Paper **không** đưa ra số paraphrase tối thiểu cho một dự án nhỏ — bạn phải tự quyết và ghi rõ.

##### `Quantifying Language Models' Sensitivity to Spurious Features in Prompt Design` (FormatSpread)

**Tại sao đọc paper này ở Stage này?**
Vì nó mở rộng cảnh báo trên xuống một tầng nữa: không chỉ **nội dung** prompt, mà cả **format** (dấu phân cách, cách xuống dòng, ký hiệu) cũng là biến.

**Tôi cần lấy gì từ paper?**
Con số **76 accuracy points** chênh lệch chỉ do đổi format, và mệnh đề rằng độ nhạy này **không mất đi** khi tăng model size / số few-shot / instruction tuning. Khuyến nghị: **báo cáo khoảng trên nhiều format**.

**Không cần tập trung vào gì ở thời điểm này?**
⚠ Đo trên task phân loại few-shot → **con số 76 không chuyển thẳng** sang generation task dài như sinh deck. Support argument định tính, không support magnitude.

##### `A Metric Learning Reality Check` + `Troubling Trends in Machine Learning Scholarship`

**Hai nguồn này ở Stage này dùng để làm gì?**
Chúng cho bạn hai quy tắc vận hành ngắn gọn, và mỗi cái chống một lỗi khác nhau.

**Tôi cần lấy gì?**
- Musgrave: danh sách các dạng **so sánh không công bằng** (backbone khác, embedding size khác, thủ thuật không khai báo) → dịch sang bài toán của bạn thành checklist biến phải cân: model tier, prompt effort, context budget, retry policy. ⚠ Repo ghi rõ danh sách này là **INFERENCE ánh xạ**, không phải điều paper nói.
- Lipton & Steinhardt: trend *failure to identify the sources of empirical gains* → quy tắc **một thay đổi một lần**. ⚠ Paper **nêu vấn đề, không đưa protocol**.

**Không cần tập trung vào gì ở thời điểm này?**
Bỏ qua nội dung domain của cả hai.

##### `Lessons from the Trenches on Reproducible Evaluation of Language Models` (Biderman et al.)

**Tại sao đọc nguồn này ở Stage này?**
Vì nó là nguồn gần nhất với câu hỏi "**phải freeze những gì**" để evaluation tái lập được.

**Tôi cần lấy gì?**
Ba vấn đề lặp lại mà họ đặt tên: độ nhạy với evaluation setup, khó so sánh đúng giữa các method, thiếu reproducibility/transparency; cùng ý tưởng **codify các quy ước ngầm thành công cụ** thay vì để chúng nằm trong đầu người chạy.

**Không cần tập trung vào gì ở thời điểm này?**
⚠ Repo ghi rõ: trong phần đọc được **không** trích ra được checklist cụ thể "phải freeze biến nào" — danh sách biến trong repo là **INFERENCE**, không phải trích nguyên văn.

#### OPTIONAL / REFERENCE

- **`PresentBench` — lần 3** (Stage 1: taxonomy; Stage 8: protocol; ở đây: **protocol so nhiều hệ thống**). Lấy: *"For each method, we provide the same background materials together with the corresponding instructions"*, mọi hệ thống chạy ở **default setting**, output chuẩn hoá về PDF/ảnh. Cộng bảng điểm 8 hệ thống làm mốc tham chiếu. ⚠ Đây là protocol so **sản phẩm ở cấu hình mặc định**, **không** phải protocol so **kiến trúc**.
- **`UniPPTBench` — lần 2**. Lấy đúng một chi tiết phương pháp: *"Each method is used according to its default or officially recommended configuration"* và việc convert output thành **chuỗi ảnh slide có thứ tự** trước khi judge — trùng khớp với PresentBench, nên argument đó vẫn đứng được nhờ nguồn E1. ⚠ Repo giữ ở **DISCOVERY ONLY**.
- **`Are More LLM Calls All You Need?`** — **lần 2** (Stage 7 đọc để có tiêu chí dừng; ở đây để nhớ **không cộng dồn kết quả ablation**).
- **`SummEval`** — đọc nếu muốn thấy một meta-evaluation quy mô lớn trông thế nào (14 metric, 23 hệ thống, annotation expert + crowd). ⚠ Repo **không trích xuất được con số correlation của ROUGE** → không viết "ROUGE tương quan r = 0,x".

### Knowledge checkpoint

Sau Stage này, tôi phải có khả năng tự giải thích:
- Ngưỡng delta tối thiểu để tôi được phép nói "phiên bản này tốt hơn", và tôi lấy ngưỡng đó từ đâu.
- Baseline của tôi có phải strawman không, và tôi chứng minh nó không phải bằng cách nào.
- Vì sao đọc ablation trên metric tổng hợp là sai, kèm ví dụ `w/o CodeRender`.
- Vì sao không được cộng dồn đóng góp của các component.
- Danh sách biến tôi phải freeze để một kết quả tái lập được sau 3 tháng.
- Vì sao so với sản phẩm đóng cho claim **hẹp hơn** so với đo trên hệ thống mở, và tôi phát biểu claim đó thế nào cho đúng.

### Implementation checkpoint

**Đã có thể làm:** dựng **experiment harness** hoàn chỉnh — chạy N lần, log mọi biến (model version, prompt version hash, seed/temperature, cấu hình retry, danh sách tài liệu, phiên bản judge prompt), báo cáo mọi metric kèm **spread**, và một bảng ablation nhiều trục theo mẫu PPTAgent. Cộng: một **baseline được tune tử tế** (nhiều paraphrase prompt, cùng model tier) mà bạn dám mang ra bảo vệ.

**Chưa nên làm:** chưa nên công bố so sánh với hệ thống thương mại đóng như bằng chứng về **kiến trúc** — mọi delta đo được ở đó chỉ diễn giải được ở mức "sản phẩm ở cấu hình mặc định, tại ngày X, version Y".

---
---

# 1. Dependency map

```text
                          S0  Bài toán & paradigm
                              (input mode / output mode /
                               định nghĩa chất lượng)
                                        │
                                        ▼
                          S1  "Deck tốt" = nhiều trục độc lập
                              (content ≠ design ≠ editability;
                               slide-level ≠ deck-level)
                    ┌───────────────────┼───────────────────┐
                    ▼                   ▼                   ▼
        S2  Nội dung & grounding   (trục editability)  S6  Layout & hình học
            salience · 3 failure          │                predicate hình học
            mode · 2 pass ngược           │                MLLM kém ở đây
                    │                     │                        ▲
                    ▼                     │                        │
        S3  Planning & narrative          │                        │
            deck-level vs slide-level     │                        │
            outline như artifact          │                        │
                    │                     │                        │
          ┌─────────┴─────────┐           │                        │
          ▼                   ▼           ▼                        │
S4  Conditioning        S5  Representation (IR)  ──────────────────┘
    noise floor ·           IR-first / HTML-first / code-first
    manipulation check      thang PEI · executability
          │                             │
          └──────────────┬──────────────┘
                         ▼
              S7  Self-critique loop
                  signal ← S2 (fidelity) + S6 (hình học)
                  chất lượng loop ≤ chất lượng signal
                         │
                         ▼
              S8  Judge & độ tin cậy
                  protocol · checklist · bias · agreement
                         │
                         ▼
              S9  So sánh & ablation
                  noise floor · anti-strawman ·
                  đọc ablation theo trục
```

**Ba cạnh quan trọng nhất trong sơ đồ này — nếu chỉ nhớ ba điều thì nhớ ba cái này:**

1. **S1 → mọi thứ.** Không phân rã được chất lượng thành các trục độc lập thì không debug được component nào, và mọi Stage sau đều mất neo.
2. **S2 + S6 → S7.** Vòng lặp self-critique **không phải** một component độc lập — nó là hàm của chất lượng tín hiệu mà S2 và S6 cung cấp. Xây S7 trước S2/S6 là xây một cỗ máy khuếch đại lỗi.
3. **S4 và S9 dùng chung một tiền đề: noise floor.** Đây là lý do khái niệm "chạy N lần, báo cáo spread" xuất hiện hai lần trong roadmap ở hai vai trò khác nhau (chứng minh conditioning có thật / chứng minh version tốt hơn).

**Một cạnh ngược đáng chú ý (`INFERENCE`):** S5 (representation) về mặt khái niệm có thể học sớm hơn, nhưng đặt sau S3 là cố ý — bạn không biết IR **phải chứa gì** cho tới khi biết planner sinh ra gì và evaluator cần gì. Thiết kế IR trước khi biết hai điều đó gần như luôn phải làm lại.

---

# 2. Paper-to-Knowledge Matrix

Priority: **P0** = cần để xây được hệ thống · **P1** = cần để xây tốt · **P2** = tra cứu khi gặp vấn đề cụ thể.

| Paper | Knowledge cung cấp | Stage sử dụng | Priority |
| --- | --- | --- | --- |
| **PPTAgent / PPTEval** | Kiến trúc pipeline có component tách rời; Success Rate như metric riêng | S0 | **P0** |
| PPTAgent | 3 chiều chất lượng; **granularity khác nhau** (slide vs deck); reference-based là proxy kém | S1 | **P0** |
| PPTAgent | Bằng chứng ablation rằng tầng planning có tác động đo được | S3 | **P0** |
| PPTAgent | Schema giữ hệ thống chạy được (`w/o Schema` → SR 78,8%) | S5 | P1 |
| PPTAgent | Self-correction là **một phần của định nghĩa thành công** | S7 | P1 |
| PPTAgent | κ ≈ 0,59 giữa người chấm; correlation theo chiều (Coherence yếu nhất 0,55) | S8 | **P0** |
| PPTAgent | Mẫu trình bày ablation nhiều trục | S9 | **P0** |
| **AutoPresent / SlidesBench** | Program generation vs image generation; executability như biến chi phối | S0 | **P0** |
| AutoPresent | 4 tiêu chí reference-free nguyên văn; human ceiling; 3 nhóm metric tách biệt | S1 | **P0** |
| AutoPresent | Hệ quả của việc chọn code làm representation | S5 | P1 |
| AutoPresent | Tiêu chí layout/color như đặc tả kỹ thuật | S6 | P1 |
| AutoPresent | Self-refinement hiệu quả **khi tín hiệu nhị phân & khách quan** | S7 | P1 |
| **PresentBench** | Tách 3 khái niệm content (completeness/correctness/fidelity); material-dependent vs independent | S1 | **P0** |
| PresentBench | Judge protocol: checklist nhị phân per-instance, mỗi item một call, localized evidence | S8 | **P0** |
| PresentBench | Protocol so nhiều hệ thống ở cấu hình mặc định | S9 | P1 |
| **SlidesGen-Bench** | Editability như một trục chất lượng độc lập | S1 | P1 |
| SlidesGen-Bench | **Thang PEI 6 mức + knockout rule** như spec cho representation | S5 | **P0** |
| SlidesGen-Bench | 4 metric aesthetic deterministic; metric đo **trên chuỗi slide** | S6 | P1 |
| **SlideAudit** | Taxonomy 27 lỗi thiết kế cụ thể | S1 | P1 |
| SlideAudit | Bằng chứng số MLLM kém phát hiện lỗi design (F1 0,331–0,655); rubric dài hơn không cứu | S6 | **P0** |
| SlideAudit | Ràng buộc số cho kiến trúc critic trong vòng lặp | S7 | **P0** |
| **AeSlides** | 4 phép kiểm layout tính bằng chương trình (collision 3 loại, whitespace, aspect ratio, imbalance) | S6 | **P0** |
| AeSlides | **Verifiable reward**; lý do từ chối VLM reward trong vòng lặp | S7 | **P0** |
| **FineSurE** | **Kiến trúc 2 pass ngược chiều**; 3 công thức faithfulness/completeness/conciseness; detect ≫ localize (86,4% vs 42,2%) | S2 | **P0** |
| **All Claims Are Equal** | Bằng chứng số precision-only mù với coverage gap; salience 3 mức | S2 | **P0** |
| **MiniCheck** | Công cụ grounding chạy được, đủ rẻ cho daily; claim tổng hợp từ nhiều câu nguồn | S2 | **P0** |
| **AbsenceBench** | **Cơ chế** vì sao LLM kém phát hiện thiếu sót (attention không có key cho khoảng trống) | S2 | P1 |
| **Stress Testing Factual Consistency Metrics** | Xếp hạng độ bền metric; metric kém đi trên **claim bị nén** | S2 | P1 |
| **Do Automatic Factuality Metrics Measure Factuality?** | Metric fidelity **game được**; gaming 3–8× cải thiện thật | S2 | **P0** |
| **X+Slides** | Cơ chế "cùng probe, khác trọng số"; cross-audience scoring; công thức Efficiency; conditioning có thể **âm** | S4 | **P0** |
| **Atil et al. — Non-Determinism** | Noise floor tồn tại và lớn; hình thái metric agreement-qua-N-run | S4 | **P0** |
| Atil et al. | Tiền đề cho mọi so sánh version | S9 | **P0** |
| **Learning to Present** | **Inverse specification** — khôi phục spec từ deck | S4 | P1 |
| Learning to Present | Mẫu hệ reward trộn deterministic + model-judged, có trọng số | S7 | P1 |
| **SlideTailor** | **`Flow = 1 − NGLD`** trên chuỗi chủ đề — metric structure deterministic | S3 | **P0** |
| SlideTailor | Tách preference-based vs preference-independent metric | S4 | P1 |
| **EffectivePresentationScorer** | "Dạy" ≠ "tóm tắt" ở chỗ **giải thích prerequisite**; coverage không bắt được điều đó | S3 | P1 |
| **IFEval** | **Verifiable instruction** — chia đôi yêu cầu thành kiểm-bằng-code vs không | S4 | **P0** |
| **Evaluating the Evaluators (readability)** | Proxy bề mặt đo được nhưng **không tương ứng** với cảm nhận người | S4 | P1 |
| **MT-Bench / Chatbot Arena** | Danh mục bias gốc (position, verbosity, self-enhancement); cận trên lạc quan | S8 | **P0** |
| **MLLM-as-a-Judge (Chen et al.)** | Chọn protocol: pairwise > scoring/ranking cho artifact multimodal | S8 | **P0** |
| **TICK** | Checklist per-instance **sinh tự động** — đường thứ ba giữa expert và rubric chung | S8 | **P0** |
| **HealthBench** | **Mô hình 2 lớp**: người viết tiêu chí, model chấm tiêu chí; criteria có trọng số | S8 | P1 |
| **ExpertLongBench / CLEAR** | Phân vai chặt hơn: LLM chỉ làm checklist **mapper** | S8 | P1 |
| **alt-test** | Con số thực hành: **≥ 3 annotator**, 50–100 instance | S8 | **P0** |
| **Beyond correlation (Elangovan)** | Correlation cao **không** đủ để kết luận judge tin được khi nhãn người bất định | S8 | P1 |
| **JudgeSense** | Judge nhạy với paraphrase prompt; **coherence bất ổn nhất**; model lớn hơn không ổn định hơn | S8 | P1 |
| **G-Eval** | Neo kỳ vọng: ρ ≈ 0,5 là vùng hoạt động thực tế | S8 | P1 |
| G-Eval | Ví dụ giới hạn: rubric chung chỉ đạt 0,314 trên completeness | S2 | P1 |
| **Ferrari Dacrema et al.** | Baseline yếu tạo tiến bộ giả ở quy mô cả lĩnh vực | S9 | **P0** |
| **Mizrahi et al.** | Một prompt duy nhất cho baseline là thiết kế không đủ; template đổi cả thứ hạng | S9 | **P0** |
| **FormatSpread** | Format prompt là biến ẩn; báo cáo khoảng thay vì một con số | S9 | P1 |
| **Musgrave et al.** | Checklist "cân biến" khi so sánh | S9 | P1 |
| **Lipton & Steinhardt** | Quy tắc **một thay đổi một lần** | S9 | P1 |
| **Biderman et al.** | Phải freeze và codify quy ước evaluation | S9 | P1 |
| **Are More LLM Calls All You Need?** | Thêm bước xử lý **không đơn điệu tốt hơn**; cần tiêu chí dừng | S7 | P1 |
| Are More LLM Calls | Không cộng dồn đóng góp ablation | S9 | P1 |
| **UniPPTBench** | Taxonomy 4 chế độ input; metric 2 tầng; đẹp-mà-sai không bị rubric chung bắt | S0 | P1 |
| UniPPTBench | Chuẩn hoá output về chuỗi ảnh slide trước khi judge | S9 | P2 |
| **DOC2PPT** | Gốc lịch sử; thế hệ metric reference-based đầu tiên và vì sao nó hỏng | S0, S1 | P2 |
| **DocPres** | Coverage đo ở 2 mức (ý đúng) bằng embedding similarity (cách sai) | S0, S3 | P2 |
| **Paper2Poster** | **PaperQuiz** — đo hiệu quả truyền đạt (extrinsic), không đo hình thức | S1 | P1 |
| **Molecular Facts** | Tension decontextuality vs minimality — đặt tên cho vấn đề của bullet | S2 | P1 |
| **RoSE / ACU**, **QAPyramid** | Đơn vị đo coverage và vì sao cần chuẩn hoá độ mịn | S2 | P2 |
| **On Positional Bias of Faithfulness** | Hình chữ U theo vị trí → chia nhỏ thay vì một prompt lớn | S2 | P1 |
| **QAFactEval**, **Comprehensiveness Metrics** | Nhánh QA-based; tín hiệu bổ sung nhau; ⚠ mâu thuẫn chưa giải quyết về end-to-end | S2 | P2 |
| **VeriFastScore** | Gộp decompose + verify; system-level r cao hơn example-level | S2 | P2 |
| **FActScore** | Nguyên lý atomic decomposition | S2 | P1 |
| **Garner & Alley 2013** | Assertion-evidence — thuộc tính title **đếm được bằng code** | S3 | P1 |
| **Garner et al. 2009** | Mốc thực nghiệm mật độ nội dung (~33 từ/slide) | S6 | P2 |
| **CourseBlueprint** | Metric level-aware; **negative finding**: prerequisite-ordering chưa ai kiểm deterministic | S3 | P2 |
| **Persona Effect** | Conditioning tạo hiệu ứng nhỏ (<10% variance) → paired design bắt buộc | S4 | P1 |
| **Controllable Readability Levels** | Mô hình "đặt mức mục tiêu → đo khoảng cách" | S4 | P2 |
| **Dror et al.** + **Hoewe** | Manipulation check + chọn significance test + paired design | S4 | P1 |
| **PPLM** | Gốc phương pháp của recovery test (external attribute classifier) | S4 | P2 |
| **Panickssery**, **Yang**, **FairEval**, **Tamba**, **Soumik** | Bias cụ thể + mitigation tương ứng | S8 | P2 |
| **PoLL** vs **Kohli** | Single judge vs panel — ⚠ **mâu thuẫn chưa giải quyết** | S8 | P2 |
| **Krippendorff**, **Bonett & Wright** | Ngưỡng agreement, cỡ mẫu cho CI của correlation | S8 | P2 |
| **Rubric survey (2606.08625)** | Taxonomy 3 paradigm dựng rubric; cảnh báo rubric kém **gây hại** | S8 | P1 |
| **SummEval** | Meta-evaluation quy mô lớn trông thế nào | S9 | P2 |
| **Open Design** (repo, không phải paper) | Workflow deck production; contrast HTML-first vs IR-first; self-check rubric | S0, S5, S7 | P1 |
| **EvoPresent**, **MemSlides**, **OutlineSpark**, **PaperX**, **EfficientPosterGen**, **Wolfe et al.** | Con trỏ tới hướng chưa đọc | S3, S6, S7 | P2 |

---

# 3. Capability-to-Knowledge Matrix

| Agent capability | Knowledge cần có | Papers hỗ trợ | Roadmap Stage |
| --- | --- | --- | --- |
| **C1 Spec intake** | Phân biệt yêu cầu verifiable vs không verifiable; cấu trúc hoá brief | IFEval; Learning to Present (`s_count`) | S4 |
| **C2 Source ingestion** | ⚠ **Corpus gần như không phủ.** Chỉ có taxonomy chế độ input; không có paper nào về parse PDF/layout-aware extraction | UniPPTBench (taxonomy input); DOC2PPT (đặt bài toán) | S0 · **Gap 7** |
| **C3 Salience** | Ý quan trọng là gì; trọng số tầm quan trọng; đơn vị nội dung và độ mịn của nó | All Claims Are Equal; FineSurE (keyfact); RoSE/ACU; QAPyramid; X+Slides (probe + weight) | S2, S4 |
| **C4 Grounding & fidelity** | 3 failure mode; 2 pass ngược chiều; công cụ kiểm text↔text; gaming | FineSurE; MiniCheck; All Claims; Do Automatic Factuality; Stress Testing; AbsenceBench; Molecular Facts; PresentBench | S2 |
| **C5 Deck-level planning** | Narrative structure; thứ tự chủ đề đo được; ablation như cách chứng minh giá trị | PPTAgent (ablation); SlideTailor (`1−NGLD`); EffectivePresentationScorer; Garner & Alley | S3 |
| **C6 Slide-level planning** | Slide split; vai trò slide; mật độ nội dung | Garner et al. 2009; SlideTailor (negative finding: không có metric mật độ); DocPres (2 mức) | S3 |
| **C7 Conditioning** | Manipulation check; noise floor; recovery test; hiệu ứng nhỏ | X+Slides; Learning to Present; Atil; Persona Effect; Controllable Readability; Hoewe; Dror | S4 |
| **C8 Representation (IR)** | IR-first vs HTML-first vs code-first; thang editability; ranh giới thông tin | SlidesGen-Bench (PEI); AutoPresent; PPTAgent (`w/o Schema`); Open Design | S5 |
| **C9 Layout** | Predicate hình học; taxonomy lỗi; ⚠ **cách SINH layout: không có paper** | AeSlides; SlideAudit; AutoPresent (Table 1); SlidesGen-Bench | S6 · **Gap 1** |
| **C10 Design system** | Contrast tính được; harmony; nhịp thị giác liên slide; ⚠ **typography: chỉ có tên lỗi, không có lý thuyết** | SlidesGen-Bench (Usability/Harmony); SlideAudit (Typography category); AutoPresent (Color) | S6 · **Gap 2, 3** |
| **C11 Visual assets** | ⚠ **Corpus gần như không phủ** | AutoPresent (Image criterion, CLIP score); Paper2Poster (figure trong poster, chưa đọc sâu) | S6 · **Gap 6** |
| **C12 Rendering & executability** | Executability là biến chi phối; báo cáo riêng; gán 0 khi fail | AutoPresent (§3.3); PPTAgent (Success Rate) | S0, S5 |
| **C13 Editability** | Thang PEI 6 mức; knockout rule; master-based là ngưỡng | SlidesGen-Bench; ⚠ **cơ chế OOXML/PPTX: không paper nào dạy** | S5 · **Gap 4** |
| **C14 Self-critique** | Verifiable reward vs model-judged; chất lượng loop ≤ chất lượng signal; tiêu chí dừng; localization | AeSlides; SlideAudit; AutoPresent; PPTAgent; Learning to Present; Are More LLM Calls; FineSurE (42,2% localize) | S7 |
| **C15 Orchestration** | ⚠ **Corpus không có paper agent-architecture nào** | Open Design (skills protocol — hệ thống, không phải paper) | S7 · **Gap 5** |
| **C16 Evaluation** | Trục chất lượng; protocol judge; checklist; bias; agreement; cỡ mẫu | Toàn bộ nhóm S1 + S8 | S1, S8 |
| **C17 Reproducibility** | Noise floor; freeze biến; anti-strawman; đọc ablation theo trục | Atil; Ferrari Dacrema; Mizrahi; FormatSpread; Musgrave; Lipton; Biderman | S9 |

**Đọc bảng này theo chiều dọc thì thấy ngay hình dạng của vấn đề:** các capability về **nội dung và đo lường** (C3, C4, C5, C16, C17) có 5–8 paper mỗi cái. Các capability về **hình ảnh và hạ tầng** (C2, C9-sinh, C10-typography, C11, C13-cơ chế, C15) có **0–2 paper**, và phần lớn chỉ là paper *đo*, không phải paper *làm*. Đó là nội dung của §6.

---

# 4. Minimum Reading Path

**Giả định:** bạn không có thời gian đọc 97 paper. Đây là **12 paper** đủ để nắm ~80% concept quan trọng cần để *bắt đầu xây*.

**Tiêu chí chọn (nói rõ để bạn kiểm được):** mỗi paper phải mở khoá một **loại kiến thức** mà không paper nào trước nó trong danh sách cung cấp. Nổi tiếng không phải tiêu chí — MT-Bench nổi tiếng hơn AeSlides rất nhiều nhưng đứng sau, vì với người **xây** deck agent thì kiểm layout bằng code có giá trị vận hành cao hơn danh mục bias của judge.

| # | Paper | Paper này unlock knowledge nào mà paper trước chưa cung cấp? |
| --- | --- | --- |
| **1** | **PPTAgent / PPTEval** | Toàn bộ bài toán trong một bài: một kiến trúc có component tách rời, một định nghĩa "thành công" vận hành được (Success Rate), và một bộ đo được validate với người. Đây là **anchor** để mọi paper sau gắn vào. |
| **2** | **AutoPresent / SlidesBench** | Rằng đây là một **họ bài toán**, không phải một bài toán: sinh từ đầu thay vì sửa deck mẫu, và output là **chương trình** thay vì thao tác chỉnh sửa. Cộng khái niệm **executability** như biến chi phối — thứ PPTAgent có nhưng không nhấn mạnh bằng. |
| **3** | **PresentBench** | Rằng "content" là **ba** khái niệm chứ không phải một (đủ / đúng / không bịa), và rằng chấm bằng **checklist nhị phân per-instance** đo được tốt hơn rubric chung. Hai paper trước đều gộp content thành một chiều. |
| **4** | **SlidesGen-Bench** | Một trục chất lượng mà ba paper trước **không có**: **editability**, và nó có **thang 6 mức với knockout rule**. Đây là paper biến "chọn representation" từ chuyện kỹ thuật thành quyết định sản phẩm. |
| **5** | **FineSurE** | **Kiến trúc đo nội dung 2 pass ngược chiều** — PresentBench cho bạn biết *có ba khái niệm*, paper này cho bạn biết *đo chúng bằng cơ chế gì*, với ba công thức cụ thể. Và vì danh sách keyfact vừa là input của evaluator vừa là output của planner, nó cũng là **spec cho ContentPlanner**. |
| **6** | **MiniCheck** | Một **công cụ chạy được thật, đủ rẻ để chạy mỗi ngày** cho việc kiểm grounding. Năm paper trước đều là khái niệm hoặc benchmark; đây là cái bạn `pip install` và cắm vào pipeline. |
| **7** | **AeSlides** | Rằng **phần lớn "layout xấu" là bất đẳng thức trên toạ độ**, cộng 4 phép kiểm cụ thể. Không paper nào trước đó nói cho bạn cách kiểm chất lượng thị giác **mà không gọi model**. |
| **8** | **SlideAudit** | Hai thứ paper trên không có: **danh sách 27 lỗi thiết kế cụ thể** (để biết mình đang bỏ sót lỗi nào), và **bằng chứng số rằng MLLM kém ở việc này** (F1 0,331–0,655) — thứ quyết định bạn giao việc cho ai trong vòng lặp self-critique. |
| **9** | **X+Slides** | Cách **conditioning** hoạt động và cách **chứng minh** nó hoạt động: cùng probe khác trọng số, cross-audience scoring, và cảnh báo rằng hiệu ứng nhỏ và **có thể âm**. Tám paper trước không đụng tới việc "cùng nguồn, khác mục đích". |
| **10** | **Atil et al. — Non-Determinism** | Rằng **mọi con số ở chín paper trên đều có sai số chạy-lại**, và bạn phải đo sai số đó trước khi phát biểu bất cứ điều gì về khác biệt. Đây là paper thay đổi cách bạn đọc lại chín paper kia. |
| **11** | **MLLM-as-a-Judge (Chen et al.)** | Rằng **cách hỏi judge quan trọng hơn model nào làm judge**: pairwise đáng tin, scoring và ranking thì lệch. Deck là artifact multimodal nên đây là nguồn đúng modality. |
| **12** | **Judging LLM-as-a-Judge (MT-Bench)** | **Danh mục bias gốc** (position / verbosity / self-enhancement) — tức *vì sao* judge lệch, không chỉ *lệch bao nhiêu*. Đọc sau #11 để có cơ chế cho hiện tượng vừa thấy. |

**Nếu bạn phải bảo vệ kết quả trước hội đồng**, thêm **#13: `Are We Really Making Much Progress?` (Ferrari Dacrema et al.)** — nó unlock thứ không paper nào trên có: bằng chứng rằng **baseline yếu tạo tiến bộ giả ở quy mô cả một lĩnh vực**, và đó là câu hỏi đầu tiên một người phản biện giỏi sẽ hỏi.

**Điều 12 paper này KHÔNG cho bạn** (nói rõ để bạn không tưởng đã đủ): cách **sinh** layout, kiến thức typography, cách parse PDF có cấu trúc, cơ chế OOXML để xuất PPTX master-based, và kiến trúc agent/tool orchestration. Xem §6.

---

# 5. Deep Research Path

Sau Minimum Reading Path, nếu muốn hiểu ở mức research. Chia theo **loại** kiến thức chứ không theo thứ tự đọc — mỗi lớp trả lời một loại câu hỏi khác nhau.

### 5.1 Foundation knowledge — *"bài toán này rốt cuộc là gì"*

Trả lời câu hỏi về **bản chất và lịch sử** của lĩnh vực.

- `DOC2PPT` — bài toán được đặt ra lần đầu ra sao, và metric thế hệ đầu (ROUGE-SL, mIoU) hỏng thế nào.
- `UniPPTBench` — taxonomy chế độ input; metric 2 tầng.
- `DocPres` — một pipeline document→slide thế hệ trước LLM-agent.
- `Garner et al. 2009`, `Garner & Alley 2013` — nền human-factors: mật độ nội dung thực tế, assertion-evidence.
- `EffectivePresentationScorer` — vì sao "trình bày" ≠ "tóm tắt".
- ⚠ `Wolfe et al. 2024` — khả năng phản bác các guideline phổ biến. Repo **chưa đọc được**.

### 5.2 Architecture knowledge — *"đặt cái gì ở đâu"*

- `SlidesGen-Bench` (PEI) — representation quyết định trần editability.
- `AutoPresent` — code làm representation: được gì, mất gì.
- `Open Design` (`docs/skills-protocol.md`) — HTML-first trong một sản phẩm thật; skill/MCP/BYOK.
- `PPTAgent` (ablation) — đóng góp của từng component, đo theo trục.
- `Are More LLM Calls All You Need?` — hình dạng đường cong khi thêm bước xử lý.
- `Learning to Present` — cách trộn tín hiệu deterministic và model-judged trong một hệ có trọng số.

### 5.3 Implementation knowledge — *"viết cái gì"*

- `AeSlides` — 4 hàm kiểm layout.
- `SlidesGen-Bench` (aesthetics) — 4 metric màu/nhịp, gồm cả metric **liên slide**.
- `MiniCheck` — model + repo chạy được.
- `SlideTailor` — `1 − NGLD` trên chuỗi chủ đề; IoU subtopic.
- `X+Slides` — công thức Efficiency (`M ← 0.25·K + Words/130`); bootstrap 10.000 resample.
- `IFEval` — verifiable instruction thành `assert`.
- `VeriFastScore` — gộp decompose + verify để giảm chi phí.
- ⚠ **Lỗ hổng cần biết trước:** không paper nào trong nhóm này dạy bạn **sinh** layout, **xuất** PPTX master-based, hay **parse** PDF có cấu trúc. Đó là engineering knowledge nằm ngoài corpus — §6, Gap 1/4/7.

### 5.4 Evaluation knowledge — *"tin con số của mình tới đâu"*

- `PresentBench` (protocol), `MT-Bench`, `MLLM-as-a-Judge`, `G-Eval` — nền.
- `TICK`, `HealthBench`, `CLEAR`, rubric survey `2606.08625` — làm sao **tạo** tiêu chí.
- `alt-test`, `Krippendorff`, `Bonett & Wright`, `Beyond correlation` — validate judge và cỡ mẫu.
- `JudgeSense`, `Tamba`, `FairEval`, `Panickssery`, `Yang`, `Soumik` — bias và độ ổn định.
- `Atil`, `Biderman`, `FormatSpread`, `Mizrahi`, `Musgrave`, `Lipton & Steinhardt`, `Ferrari Dacrema` — so sánh công bằng và tái lập.
- `FineSurE`, `All Claims`, `Stress Testing`, `Do Automatic Factuality`, `AbsenceBench`, `Comprehensiveness Metrics`, `QAFactEval`, `RoSE`, `QAPyramid`, `SummEval` — đo nội dung.

### 5.5 Frontier / experimental — *"chỗ chưa ai chắc"*

Đây là các hướng mà evidence **mỏng, mâu thuẫn, hoặc chưa tồn tại**. Đọc nếu bạn muốn đóng góp research chứ không chỉ xây sản phẩm.

| Hướng | Trạng thái | Nguồn liên quan |
| --- | --- | --- |
| Chấm điểm **plan/outline trung gian** như evaluation target | **Không có precedent.** Negative finding ghi ở **hai RQ độc lập** | (không có) |
| Kiểm **prerequisite-ordering deterministic** (graph thay vì judge) | Chưa ai làm được | CourseBlueprint (thử, nhưng vẫn dùng judge) |
| Lượng hoá **edit effort** của người dùng trên deck sinh tự động | Chỉ có mô tả định tính | `AI-Generated Slides: Are They Good?` |
| Metric cho **design token conformance** | Không có precedent | (gần nhất: PresentBench "design uniformity" — judged) |
| Đánh giá **speaker notes** như artifact có chất lượng riêng | Không có precedent | SlideTailor (sinh nhưng **không** evaluate) |
| Trục "deck đọc một mình" vs "deck hỗ trợ người nói" | **Lỗ hổng literature thật** — chỉ có tài liệu nghề nghiệp E3 | (không có nguồn học thuật) |
| **Chuyển giao** metric fidelity (NLI/atomic-fact) sang slide deck | **0 nguồn.** Thuần INFERENCE | negative finding RQ02 §4 |
| **Noise floor** của một hệ thống sinh deck | Chưa ai đo | Atil (task khác) |
| Single judge vs **panel** | ⚠ **Mâu thuẫn chưa giải quyết** | PoLL vs Kohli |
| Chất lượng rubric **LLM sinh** vs **người viết**, đo bằng số | Không nguồn nào có so sánh head-to-head | rubric survey; CLEAR (chỉ định tính); `Can LLMs Write Reliable Rubrics?` (repo chưa parse được) |
| **Suy luận định lượng** của metric hiện đại (2024–2026) | Chưa ai đo lại | EQUATE (2019, model thế hệ cũ) |
| Metric coverage **end-to-end** có thật sự hiệu quả không | ⚠ **Mâu thuẫn chưa giải quyết** | Comprehensiveness Metrics vs AbsenceBench/FineSurE |

---

# 6. Knowledge gaps trong evidence hiện tại

## 6.1 Câu trả lời thẳng

> **Các paper hiện có có đủ để học cách xây một system tương tự OpenDesign / Claude Design / PPTAgent không?**

**Không đủ — và thiếu theo một hướng rất cụ thể.**

Phân tích theo capability (§3), corpus hiện tại:

- **Đủ, thậm chí dư**, cho: C3 salience, C4 grounding, C5/C6 planning, C16 evaluation, C17 reproducibility, C8 representation (ở mức *đánh giá* representation), C12 executability, C14 self-critique (ở mức *biết khi nào không nên làm*).
- **Đủ để PHÁT HIỆN, không đủ để SINH**, cho: C9 layout, C10 design system. Bạn biết cách nói "layout này hỏng"; bạn không biết cách nói "layout này nên trông thế nào".
- **Gần như không có gì**, cho: C2 document understanding, C11 visual assets, C13 (cơ chế kỹ thuật của editability), C15 agent orchestration.

`INFERENCE` giải thích hình dạng này: repo được xây để trả lời *"làm sao đo Deck Agent"*, và mọi RQ đã research (RQ01, RQ02, RQ03, RQ06, RQ08) đều là RQ về evaluation. Không có RQ nào hỏi *"làm sao sinh layout"*, nên không ai đi tìm. **Đây là lỗ hổng do phạm vi câu hỏi, không phải do lĩnh vực thiếu paper.**

## 6.2 Các gap, xếp theo mức độ chặn

### Gap 1 — Layout generation

**Missing knowledge:** cách **sinh** một bố cục slide — chọn hoặc tạo sự sắp xếp các element trên canvas sao cho vừa đủ chỗ, cân đối, và phản ánh đúng thứ tự quan trọng.

**Tại sao đây là gap:** corpus có **4 nguồn độc lập** dạy bạn *phát hiện* layout hỏng (AeSlides, SlideAudit, SlidesGen-Bench, AutoPresent Table 1) và **0 nguồn** dạy bạn *sinh* layout tốt. Ba hệ thống tham chiếu đều **né** bài toán này theo ba cách khác nhau: PPTAgent mượn deck tham chiếu, OpenDesign dùng **layout catalog** có sẵn, AutoPresent để LLM viết code đặt toạ độ (và trả giá bằng execution rate 2,1%–89,2%). Việc cả ba đều né là tín hiệu mạnh rằng đây là bài toán riêng, không giải kèm được.

**Nó block capability nào:** **C9** hoàn toàn; **C10** một phần (không có bố cục thì không có visual hierarchy); và gián tiếp **C14**, vì self-critique chỉ nói được "hỏng" mà không đề xuất được "nên thế nào".

**Loại research cần tìm thêm:** literature về **layout generation / document layout synthesis** — nơi bố cục được mô hình hoá như bài toán **sinh có ràng buộc** hoặc **tối ưu**, thay vì như bài toán sinh văn bản. Từ khoá tìm: *constraint-based layout*, *content-aware layout generation*, *graphic layout synthesis*, *layout optimization*. Cũng cần literature về **template/layout catalog design** — cách xây một bộ layout đủ phủ các loại nội dung.

> `External recommendation — not currently referenced by evidence.md`
> Các hướng cụ thể đáng search (⚠ **chưa verify theo `EVIDENCE_POLICY` trong repo này**, phải verify trước khi cite): dòng *LayoutTransformer / LayoutDM / LayoutPrompter* (sinh layout bằng model sinh), và dòng constraint-solving cho UI/document layout. Lý do chúng lấp được gap: chúng mô hình hoá layout như **phân bố trên tập bố cục hợp lệ** hoặc **nghiệm của hệ ràng buộc**, tức đúng cái mà predicate hình học của AeSlides chỉ **kiểm tra** chứ không **tạo ra**.

### Gap 2 — Typography

**Missing knowledge:** lý thuyết chữ — thang cỡ chữ (type scale), phân cấp thị giác bằng cỡ/đậm/khoảng cách, ghép font, độ dài dòng, line-height, và ngưỡng đọc được ở khoảng cách xa (điều kiện thật của một buổi trình chiếu).

**Tại sao đây là gap:** corpus có **đúng một** nguồn nhắc typography — SlideAudit, và nó chỉ cho **tên lỗi** (Inappropriate Font Sizing, Inconsistent Text Styling, Illegible Typeface Usage, Poor Text Alignment). Biết tên lỗi không cho bạn biết **giá trị đúng là bao nhiêu**. Không có nguồn nào trả lời "cỡ chữ tối thiểu cho slide chiếu là bao nhiêu" hay "một deck nên có mấy cấp chữ".

**Nó block capability nào:** **C10** design system (không tự viết được token typography có cơ sở); **C6** slide-level planning (không biết một slide chứa được bao nhiêu chữ ở cỡ đọc được — tức không đặt được ngân sách nội dung).

**Loại research cần tìm thêm:** literature về **typography for presentation/large-format display**, **legibility research** (khoảng cách đọc, cỡ chữ tối thiểu), và **typographic scale systems**. Đây phần lớn là **design knowledge và standards**, không phải ML paper — nên tìm ở sách chuyên ngành thiết kế và tài liệu chuẩn, chứ không ở arXiv.

### Gap 3 — Color system & accessibility

**Missing knowledge:** cách **xây** một bảng màu (không phải cách chấm điểm nó), và tiêu chuẩn tiếp cận cho tương phản.

**Tại sao đây là gap:** corpus **có** phần đo — SlidesGen-Bench Usability Score dùng relative luminance BT.709, AutoPresent dùng CIEDE2000, Harmony Score fit hue template. Nhưng cả ba đều là **hàm chấm điểm trên một bảng màu đã có**. Không nguồn nào nói cách sinh ra bảng màu đó, và không nguồn nào nêu một **ngưỡng tương phản chuẩn** để pass/fail.

**Nó block capability nào:** **C10**; và block khả năng đưa ra claim về **accessibility** — thứ mà một sản phẩm thật cần.

**Loại research cần tìm thêm:** **tiêu chuẩn tương phản cho nội dung số** (đây là chuẩn kỹ thuật có sẵn, không phải research question mở), và literature/thực hành về **palette generation** trong không gian màu perceptual.

> `External recommendation — not currently referenced by evidence.md`
> Chuẩn tương phản của W3C (WCAG) là nguồn hiển nhiên cho ngưỡng pass/fail. ⚠ **Chưa verify trong repo này.** Lý do nó lấp được gap: nó biến "tương phản đủ chưa" từ một câu hỏi thẩm mỹ thành **một ngưỡng số có thẩm quyền**, cắm thẳng được vào linter của Stage 6.

### Gap 4 — Cơ chế kỹ thuật của editable PPTX

**Missing knowledge:** cách **thực sự tạo ra** một file PPTX đạt PEI L3 — cấu trúc OOXML, slide master / slide layout / placeholder, kế thừa theme, và cách một thư viện sinh PPTX ánh xạ sang những thứ đó.

**Tại sao đây là gap:** SlidesGen-Bench cho bạn **thang đo** và nhắc `<p:sldMaster>`, nhưng nó là paper **đánh giá**. Nó nói bạn cần đạt L3; nó không nói bạn đạt L3 bằng cách nào. Và `opendesign.md` ghi rõ repo **chưa verify** cơ chế `slides.json` → PPTX của Open Design.

**Nó block capability nào:** **C13** editability, và qua đó block **C10** (không có master thì không có "đổi một lần, áp cả deck").

**Loại research cần tìm thêm:** **không phải research — đây là engineering knowledge**, và đây là ví dụ rõ nhất của nguyên tắc 8 (research knowledge ≠ implementation detail). Cái bạn cần là **đặc tả định dạng** và **tài liệu thư viện**, đọc kèm việc mở file PPTX ra xem XML bên trong. Không paper nào sẽ dạy bạn điều này, và đó không phải thiếu sót của lĩnh vực.

> `External recommendation — not currently referenced by evidence.md`
> Đặc tả OOXML (ECMA-376) và tài liệu của thư viện sinh PPTX bạn chọn. ⚠ **Chưa verify trong repo này.** Lý do lấp được gap: PEI L3 được định nghĩa bằng chính các phần tử của định dạng đó, nên đọc spec là con đường trực tiếp nhất.

### Gap 5 — Agent planning & tool orchestration

**Missing knowledge:** kiến trúc agent — vòng lặp suy nghĩ/hành động, gọi tool, giữ state qua nhiều lượt, xử lý thất bại, và thiết kế giao thức skill.

**Tại sao đây là gap:** corpus có **0 paper** về agent architecture. Nguồn duy nhất là `Open Design` — một **hệ thống**, không phải publication, và repo chỉ đọc docs chứ chưa chạy thử. `Are More LLM Calls All You Need?` chạm vào compound system nhưng repo ghi rõ đó là voting/filtering trên **cùng một task**, và dùng cho pipeline nhiều component là **INFERENCE mở rộng**.

**Nó block capability nào:** **C15** hoàn toàn; và block phần **repair** của **C14** (Editor Loop cần state và cần tôn trọng lock).

**Loại research cần tìm thêm:** literature về **LLM agent architecture** — reasoning-acting loop, self-reflection, tool use, memory/state. Cộng với **thực hành kỹ thuật** về giao thức tool (đây là engineering, không phải research).

> `External recommendation — not currently referenced by evidence.md`
> Dòng *ReAct* (xen kẽ reasoning và acting) và *Reflexion* (self-reflection có bộ nhớ) là hai điểm khởi đầu chuẩn cho lớp kiến trúc này. ⚠ **Chưa verify theo `EVIDENCE_POLICY` trong repo này.** Lý do lấp được gap: chúng mô tả **vòng lặp agent như một cấu trúc điều khiển**, thứ mà mọi paper slide-generation trong corpus đều **giả định có sẵn** và không mô tả.

### Gap 6 — Visual asset selection & generation

**Missing knowledge:** khi nào slide cần hình; chọn/tạo hình gì; khi nào bảng số nên thành biểu đồ và biểu đồ loại nào; sinh sơ đồ từ quan hệ trong nội dung.

**Tại sao đây là gap:** corpus chỉ có **tiêu chí chấm** hình (AutoPresent *Image*: "high-quality images with a reasonable proportion"; CLIP score cho độ tương đồng ảnh) và một mốc thống kê (Garner 2009: dưới một nửa slide có hình). Không nguồn nào nói **chọn hình bằng cách nào**. Paper2Poster có xử lý figure từ paper nhưng repo **không đọc được full PDF**.

**Nó block capability nào:** **C11** hoàn toàn.

**Loại research cần tìm thêm:** hai nhánh riêng biệt — (a) **retrieval/generation ảnh có điều kiện ngữ nghĩa**; (b) **sinh biểu đồ từ dữ liệu**, tức ngữ pháp đồ hoạ (grammar of graphics) và quy tắc chọn loại biểu đồ theo kiểu dữ liệu. Nhánh (b) thực dụng hơn nhiều cho deck từ tài liệu, vì nguồn thường có sẵn bảng số.

### Gap 7 — Document understanding / layout-aware parsing

**Missing knowledge:** trích xuất **có cấu trúc** từ PDF — nhận biết section, tiêu đề, bảng, hình, chú thích, thứ tự đọc; xử lý bố cục nhiều cột.

**Tại sao đây là gap:** corpus **giả định** bạn đã có nội dung nguồn ở dạng dùng được. UniPPTBench đặt tên cho các chế độ input, DOC2PPT đặt ra bài toán — nhưng không nguồn nào nói cách **đọc** một PDF cho đúng. Và điều này quan trọng hơn vẻ ngoài: `EXTERNAL EVIDENCE` từ Stage 2 cho thấy **vị trí** thông tin trong tài liệu ảnh hưởng tới việc nó có bị bỏ sót không (hình chữ U của positional bias) — tức chất lượng parsing tác động thẳng lên coverage.

**Nó block capability nào:** **C2** hoàn toàn, và qua đó làm suy yếu **C3** và **C4** (không có cấu trúc nguồn thì `source_ref` và salience đều mất neo).

**Loại research cần tìm thêm:** **document AI / layout-aware document understanding** — model và dataset cho việc phân tích bố cục tài liệu và trích xuất có cấu trúc, bao gồm cả xử lý bảng.

> `External recommendation — not currently referenced by evidence.md`
> Dòng *LayoutLM* (model hiểu tài liệu có kèm thông tin bố cục), *DocLayNet* (dataset phân tích bố cục), và các công cụ trích xuất PDF sang markdown/cấu trúc. ⚠ **Chưa verify theo `EVIDENCE_POLICY` trong repo này.** Lý do lấp được gap: chúng cung cấp chính cái mà corpus giả định có sẵn — một biểu diễn tài liệu **có cấu trúc và có toạ độ**, điều kiện cần để `source_ref` truy ngược được về vị trí thật.

### Gap 8 — Chất lượng của criteria do LLM sinh

**Missing knowledge:** khi bạn để LLM tự sinh checklist/rubric thay vì thuê expert soạn, bạn **mất bao nhiêu** — đo bằng số.

**Tại sao đây là gap:** đây là gap mà **chính repo đã đi tìm và không tìm thấy**. Rubric survey cho taxonomy nhưng **không có so sánh head-to-head**; CLEAR chỉ có **phát biểu định tính** của tác giả (*"existing LLMs cannot yet replicate"*) không kèm thí nghiệm đối chứng; TICK cho +5,8 điểm phần trăm nhưng khoảng cách PresentBench (0,532) vs PPTEval (0,303) lớn hơn nhiều. Repo kết luận: *"không nguồn nào có so sánh số head-to-head"*.

**Nó block capability nào:** **C16** ở mức quyết định ngân sách — với một team nhỏ, đây là câu hỏi *"chúng tôi có được phép đi đường rẻ không"*, và hiện **không ai trả lời được bằng số**.

**Loại research cần tìm thêm:** meta-evaluation so sánh **trực tiếp** rubric expert-authored vs LLM-generated trên cùng task, cùng judge, có human ground truth. Repo đã xác định một ứng viên đọc tiếp: `Can LLMs Write Reliable Rubrics?` (arXiv 2607.12835) — ⚠ **PDF không parse được, mọi con số chưa verify**.

### Gap 9 — Claude Design

**Missing knowledge:** capability, kiến trúc, hoặc bất cứ thông tin verify được nào về Claude Design.

**Tại sao đây là gap:** một trong ba hệ thống tham chiếu của đề bài, và repo có **đúng một câu gián tiếp** về nó (Open Design tự mô tả là alternative cho nó). Không có evidence entry, không có capability analysis, không có nguồn.

**Nó block capability nào:** không block capability kỹ thuật nào. Nhưng nó **block một claim**: bạn không thể nói "hệ thống của tôi có capability tương tự Claude Design" vì không ai trong repo biết capability đó là gì.

**Loại research cần tìm thêm:** **official product documentation** của chính nhà phát triển. ⚠ Theo `EVIDENCE_POLICY`, marketing claim trên trang sản phẩm **không** phải benchmark evidence — dùng được để mô tả capability được công bố, không dùng được để so sánh chất lượng.

## 6.3 Thứ tự lấp gap (`RECOMMENDATION`)

Nếu chỉ lấp được vài gap, lấp theo thứ tự này, và đây là lý do:

1. **Gap 7 (document understanding)** — nằm ở **đầu** pipeline. Mọi thứ downstream kế thừa lỗi của nó, và nó âm thầm làm hỏng coverage.
2. **Gap 4 (cơ chế PPTX)** — quyết định **khó đảo ngược nhất**, và là engineering knowledge nên lấp nhanh.
3. **Gap 1 (layout generation)** — chặn capability lớn nhất; nhưng **có đường vòng có evidence** (mượn template catalog), nên không cấp bách bằng hai cái trên.
4. **Gap 5 (agent orchestration)** — cần khi bạn chuyển từ pipeline tuần tự sang Editor Loop có state.
5. **Gap 2 + 3 (typography, color)** — lấp bằng **design knowledge và chuẩn kỹ thuật**, không phải bằng paper; rẻ và nhanh.
6. **Gap 6, 8, 9** — lấp khi chạm tới, không chặn v1.

---

# 7. Peripheral papers — có trong inventory nhưng KHÔNG thuộc roadmap

Theo nguyên tắc 7 (*không ép paper vào roadmap*), các paper sau nằm trong `paper_inventory.md` nhưng **không được gán vào Stage nào**. Ghi rõ lý do để bạn không băn khoăn liệu mình có bỏ sót gì.

| Paper | Vì sao peripheral với việc **xây** một sample-deck agent |
| --- | --- |
| `PosterForest`, `Any2Poster`, `EfficientPosterGen` | Artifact là poster (một canvas), không phải chuỗi slide. Repo **chưa đọc** cả ba. `EfficientPosterGen` có thể chuyển thành non-peripheral nếu phần "violation detection" đúng như tên gọi |
| `OmniPresent`, `DeepSlide`, `PaperX`, `MemSlides`, `SlideTailor`(một phần), `OutlineSpark`, `EvoPresent`, `Auto-Slides` | Repo **chưa đọc** — không có nội dung để đưa vào Stage. Chúng là **con trỏ hướng**, không phải nguồn kiến thức. (SlideTailor là ngoại lệ: phần đã đọc đủ để dùng ở S3/S4) |
| `MM-JudgeBias`, `MLLM-as-a-Judge Exhibits Model Preference Bias` | Trùng lặp về mặt kiến thức với MT-Bench + Chen et al. + Panickssery, mà repo **chưa đọc** hai cái này. Đọc chỉ khi bias multimodal trở thành vấn đề đo được của bạn |
| `EQUATE` | 2019, model thế hệ trước. Giữ lại **một ý** (suy luận định lượng là trục năng lực tách rời khỏi entailment) nhưng không đủ để làm một Stage, và repo cấm suy ra kết luận về model hiện đại |
| `SAFE / LongFact` | Repo **đã xét rồi loại**: recall neo vào "số fact lý tưởng" chứ không vào key fact của nguồn → không giải bài toán coverage của deck |
| `ACUEval`, `QuanTemp / CheckThat!`, `TriQua`, `VeriFact`, `DnDScore`, `LongSumEval`, `Can LLMs Write Reliable Rubrics?` | ⚠ **Chưa verify hoặc chưa trích xuất được nội dung.** Không được dùng làm evidence → không đưa vào roadmap. (`LongSumEval` và `Can LLMs Write Reliable Rubrics?` là hai ứng viên đọc tiếp có giá trị cao nhất) |
| `UniSumEval`, `SummEval` | Meta-evaluation của summarization. Hữu ích cho người **nghiên cứu metric**, không cần cho người **xây agent**. Repo cũng không trích xuất được số cụ thể |
| `Krippendorff 2004`, `Bonett & Wright 2000`, `Hoewe 2017` | **Tài liệu tra cứu**, không phải kiến thức tuyến tính. Đọc khi cần con số/khái niệm cụ thể, không đọc theo roadmap. (Hoewe được nhắc ở S4 đúng ở vai trò đó) |
| `Stable Behavior, Limited Variation` (urban sentiment) | Domain rất xa. Chỉ là tín hiệu hội tụ với Persona Effect — không thêm kiến thức mới |
| `ReadCtrl`, `Readability Controllable Biomedical Summarization` | Repo chưa đọc/chưa verify. Chỉ liên quan nếu bạn chọn thiết kế chế độ deck thành **thang liên tục** |
| `PPLM` | Chỉ dùng để **đặt tên** cho phương pháp recovery test. ⚠ Repo ghi rõ trang abstract **không xác nhận** metric — không trích được gì thêm |
| `From Generation to Judgment` (survey) | Bản đồ để tìm E1, không phải nguồn kiến thức trực tiếp |
| `AI-Generated Slides: Are They Good?` | Giá trị chính là **negative evidence** (không ai lượng hoá được edit effort) — đã được phản ánh ở §5.5, không cần một Stage |
| `Slide Deck Q&A Quality Assurance App` | Repo chưa đọc; "Scaffolding" của họ là của **câu hỏi**, không phải của slide → transfer chưa rõ |

---

# Cách dùng file này

1. **Đừng đọc tuần tự 97 paper.** Bắt đầu bằng **§4 Minimum Reading Path** (12 paper). Mỗi paper đọc đúng phần được chỉ ở Stage tương ứng, không đọc hết.
2. **Sau mỗi Stage, làm Implementation checkpoint trước khi sang Stage kế.** Roadmap này được thiết kế để research dẫn tới engineering capability; nếu bạn chỉ đọc mà không build, thứ tự Stage mất phần lớn giá trị.
3. **Dùng Knowledge checkpoint như một bài tự kiểm.** Nếu không tự giải thích được một gạch đầu dòng, quay lại phần "Tôi cần lấy gì từ paper" của Stage đó.
4. **Khi gặp một problem mới**, chạy nó qua chuỗi: `Problem → Bottleneck → Required Knowledge → Possible Representation → Architecture → Evaluation → Iteration`. Roadmap được sắp xếp đúng theo chuỗi đó — S0/S1 cho *problem/bottleneck*, S2–S4 cho *required knowledge*, S5 cho *representation*, S6–S7 cho *architecture*, S8 cho *evaluation*, S9 cho *iteration*.
5. **Trước khi trích bất cứ con số nào vào báo cáo bảo vệ**, mở `paper_inventory.md` kiểm trạng thái verify của nguồn đó. Rất nhiều số trong repo được đánh dấu `[qua tool summary]`, `[SEARCH-ONLY]`, hoặc "chưa quote-verified".
6. **File này là research output, không phải decision.** Theo `CLAUDE.md`, nó nằm ở `03_research/` và **không** tự động trở thành design. Mọi mục `RECOMMENDATION` ở đây là đề xuất chờ team xác nhận, không phải quyết định đã chốt.
