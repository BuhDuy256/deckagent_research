# Evaluation Mission

> **Tài liệu gốc: đây là một cuộc trao đổi scoping (chat), KHÔNG phải kết luận đã chốt.** Nó giải thích vì sao evaluation framework này tồn tại và cần trả lời gì, nhưng các đề xuất cụ thể trong đó (vd. taxonomy 4 nhánh, phân bổ effort theo pipeline stage) chỉ là **working hypothesis** — chưa qua audit (`02_research_questions/RQ00_current_evaluation_audit.md`) hay external evidence. Xem `05_decisions/OPEN_QUESTIONS.md` cho các điểm đang chờ quyết định, và `06_design/EVALUATION_FRAMEWORK.md` cho bản spec chính thức (khi có).
>
> Nội dung bên dưới giữ nguyên tiếng Việt gốc (không dịch/paraphrase) để không làm sai lệch ý ban đầu.

Nói ngắn gọn, nhiệm vụ của bạn là tạo ra một hệ thống đo lường để team trả lời được 2 câu xuyên suốt đồ án:

> **1. Hôm nay Deck Agent có tốt hơn phiên bản hôm qua không?**
> **2. Deck Agent của team có tốt hơn baseline / cách làm khác ở những điểm mà team claim là thế mạnh không?**

Và giờ tôi thấy rõ hơn tại sao bạn bị rối: tài liệu hiện tại đã “nhảy cóc” sang một số metric/evaluation idea như MLLM-as-judge, content fidelity, ready-to-use, design consistency, structure, nhưng chưa dựng cho bạn **khung vận hành evaluation theo vòng đời product**. Trong kiến trúc hiện tại, ContentPlanner được xác định là phần lõi nơi AI quyết định nội dung, còn Deck IR là cấu trúc trung gian tách planning khỏi exporter.  

## Vậy task thật của bạn là gì?

Tôi sẽ viết lại task của bạn thành một câu:

> **Thiết kế một Evaluation System cho Deck Agent, dùng được từ lúc MVP còn rất thô cho tới trước bảo vệ, để đo regression, đo improvement, so sánh version, so sánh baseline/competitor, và cung cấp evidence định lượng cho các claim của team.**

Đây là khác biệt rất lớn so với:

> “Tìm vài metric cho AI Slide Deck Agent.”

Bạn cần **một hệ thống**, không phải một danh sách metric.

---

# Evaluation của bạn phải phục vụ 3 mục đích khác nhau

Đây là điểm tôi muốn bạn giữ rất chắc.

### 1. Development evaluation — “Có đang cải thiện mỗi ngày không?”

Dùng trong quá trình team build.

Ví dụ:

```text
Deck Agent v0.2
vs
Deck Agent v0.3
```

Team đổi prompt ContentPlanner.

Chạy cùng benchmark:

```text
v0.2 fidelity = 78
v0.3 fidelity = 84
```

Nhưng:

```text
v0.2 latency = 42s
v0.3 latency = 67s
```

→ Có improvement quality, nhưng đánh đổi performance.

Đó mới là evidence để team quyết định có merge/change hay không.

---

### 2. System validation — “Product hiện tại có đạt yêu cầu không?”

Cái này gắn trực tiếp với FR/NFR.

Ví dụ tài liệu đang yêu cầu:

* cùng PDF, đổi `slide_type` phải tạo cấu trúc deck khác có chủ đích; 
* block phải trace được về nguồn và không bịa ý chính; 
* design tokens phải được giữ khi regenerate; 
* slide_type phải có khác biệt đo được trước bảo vệ. 

Evaluation của bạn phải chứng minh những requirement này **thực sự đạt**, không chỉ “code đã implement”.

---

### 3. Comparative evaluation — “Tại sao Deck Agent đáng tồn tại?”

Đây là phần để bảo vệ / report.

Ví dụ:

```text
Deck Agent
vs
Single-shot LLM
```

hoặc:

```text
Deck Agent
vs
Deck Agent without ContentPlanner
```

hoặc nếu tìm được hệ thống phù hợp:

```text
Deck Agent
vs
existing slide-generation system
```

Lúc này bạn mới nói được:

> “Architecture của chúng tôi cải thiện X bao nhiêu, với trade-off Y.”

---

# Từ docs hiện tại, tôi thấy evaluation phải chia thành 2 lớp

## Lớp A — Product/System metrics

Đây là các thứ có thể đo tương đối khách quan.

Ví dụ:

* PPTX export success rate
* schema validation rate
* overflow rate
* lock violation rate
* token consistency rate
* latency P50/P95
* cost/deck
* failure rate
* retry rate

NFR hiện tại đã đặt sẵn một số hướng như latency cho deck ~10 slide, P50/P95, validation output LLM, design consistency, content fidelity và cost/deck.    

Những metric này rất phù hợp cho:

> “Version mới có regression không?”

---

# Lớp B — AI Quality metrics

Đây mới là phần khó.

Hiện doc kiến trúc đề xuất 4 chiều:

```text
Content fidelity
Ready-to-use
Design consistency
Structure
```

và muốn dùng MLLM-as-judge + human annotation sample để kiểm tra reliability. 

Nhưng tôi sẽ **không giữ nguyên 4 mục này như 4 metric ngang hàng**.

Tôi sẽ restructure thành:

```text
QUALITY
├── Content Quality
│   ├── factual/source fidelity
│   ├── important information coverage
│   └── hallucination
│
├── Planning Quality
│   ├── slide decomposition
│   ├── information allocation
│   └── slide_type appropriateness
│
├── Presentation Quality
│   ├── readability
│   ├── content density
│   ├── layout appropriateness
│   └── visual coherence
│
└── Usability / Ready-to-use
    ├── edit effort
    └── overall presentation readiness
```

Sau đó mới map sang metric cụ thể.

---

# Một điểm quan trọng: bạn KHÔNG cần evaluate mọi module

Nhìn architecture:

```text
Adapter
↓
Extractor
↓
ContentPlanner
↓
Deck IR
↓
Design / Editor
↓
Exporter
```

Bạn không cần một “AI metric” cho từng box.

Ví dụ Adapter đọc PDF đúng hay không chủ yếu là engineering correctness.

Exporter tạo file mở được hay không cũng là correctness.

Nhưng ContentPlanner mới là chỗ rất đáng đầu tư evaluation vì requirements đã xác định nó quyết định:

* bao nhiêu slide;
* slide nào nói gì;
* slide_type ảnh hưởng ra sao;
* layout logic nào phù hợp. 

Do đó nếu tôi chia effort của bạn:

```text
ContentPlanner quality       ██████████
End-to-end deck quality      ████████
Design consistency           ████
Editor behaviour             ███
Exporter correctness         ██
Adapter correctness          ██
```

Không cần chia đều.

---

# Bạn cần build một “Evaluation Harness”

Đây mới là deliverable kỹ thuật thật sự của bạn.

Không chỉ viết Markdown.

Conceptually:

```text
                 BENCHMARK DATASET
                       │
                       ▼
             ┌──────────────────┐
             │ Evaluation Runner│
             └────────┬─────────┘
                      │
          ┌───────────┴────────────┐
          ▼                        ▼
   Deck Agent v1             Deck Agent v2
          │                        │
          └──────────┬─────────────┘
                     ▼
               generated runs
                     │
       ┌─────────────┼──────────────┐
       ▼             ▼              ▼
 deterministic    LLM Judge       Human
   metrics          metrics       sample
       │             │              │
       └─────────────┴──────────────┘
                     ▼
                 RESULTS
                     │
                     ▼
            Comparison / Report
```

Ví dụ command sau này có thể conceptually giống:

```bash
evaluate \
  --benchmark benchmark-v1 \
  --system deck-agent-v0.4 \
  --baseline deck-agent-v0.3
```

Output:

```text
Content Fidelity       82.1 → 86.7   +4.6
Structure Quality      71.5 → 79.3   +7.8
Overflow Rate           8.2% → 2.1%  -6.1%
Latency P50             48s → 55s    +7s
Cost / deck            $0.08 → $0.11
```

**Đây mới là thứ giúp bạn chứng minh “chúng tôi đang cải thiện product mỗi ngày”.**

---

# Và bạn cần một benchmark cố định

Nếu hôm nay test PDF A, mai test PDF B thì:

> score hôm nay và score ngày mai không so được.

Do đó bạn cần một tập input **đóng băng**.

Ví dụ:

```text
benchmark/
  pdf/
    academic-short/
    academic-long/
    business-report/
    image-heavy/
    table-heavy/
    poor-structure/
```

Mỗi case có metadata:

```yaml
id: pdf_014

source: ...
slide_type: teaching

constraints:
  target_slides: 10

evaluation:
  expected_topics:
    - ...
    - ...

  critical_facts:
    - ...
```

Sau đó **mọi version đều chạy lại cùng set đó**.

Đây là nền tảng cho regression testing.

---

# Development loop của team nên trở thành như thế này

Trước đây có thể đang là:

```text
Code
↓
nhìn output
↓
"trông khá hơn"
↓
merge
```

Bạn phải biến thành:

```text
CHANGE
↓
run benchmark
↓
compare with previous version
↓
metric report
↓
inspect failures
↓
decision
```

Ví dụ:

```text
Change:
Planner prompt v7 → v8

Impact:

             v7      v8
Fidelity     82      86     ↑
Structure    74      83     ↑
Latency      45s     52s    ↓
Cost         .07     .09    ↓
Overflow     3%      3%     =

Decision:
Keep v8.
```

Đây chính là evaluation-driven development.

---

# Baseline cũng có nhiều loại, đừng nghĩ chỉ có competitor

Bạn cần ít nhất **3 loại comparison**.

| Loại                | Ví dụ                  | Trả lời câu hỏi                      |
| ------------------- | ---------------------- | ------------------------------------ |
| Previous version    | v0.5 vs v0.6           | Chúng ta có tiến bộ không?           |
| Simplified baseline | single-shot LLM        | Architecture phức tạp có đáng không? |
| Ablation            | without ContentPlanner | Component này đóng góp gì?           |

Competitor bên ngoài là loại thứ tư:

```text
Deck Agent vs Gamma / Canva / ...
```

nhưng **không nhất thiết là baseline quan trọng nhất**.

Nếu hệ thống bên ngoài không cùng input/output/control conditions thì comparison có thể rất khó công bằng.

---

# Từ đây tôi sẽ sửa lại objective của task bạn

Không phải:

> “Research bộ metric.”

Mà là:

> **Research + design + implement một evaluation framework cho Deck Agent.**

Framework đó phải trả lời được:

```text
                          EVALUATION FRAMEWORK

       ┌──────────────────────────────────────────┐
       │ 1. Product có hoạt động đúng không?      │
       │       → correctness / reliability        │
       ├──────────────────────────────────────────┤
       │ 2. Output AI có tốt không?               │
       │       → quality metrics                  │
       ├──────────────────────────────────────────┤
       │ 3. Version mới có tốt hơn version cũ?    │
       │       → regression / comparison          │
       ├──────────────────────────────────────────┤
       │ 4. Architecture có thực sự giúp không?   │
       │       → baseline / ablation              │
       ├──────────────────────────────────────────┤
       │ 5. Đổi lại chúng ta mất gì?              │
       │       → latency / cost / reliability     │
       └──────────────────────────────────────────┘
```

---

# Và bây giờ tôi biết bước tiếp theo của bạn là gì

**Chưa phải research từng metric riêng.**

Bạn nên dựng trước:

## `evaluation_framework_v0.md`

Nó chỉ cần có **5 phần**:

### 1. Evaluation Goals

Xác định 3 mục đích:

```text
Development tracking
System validation
External/baseline comparison
```

### 2. Evaluation Targets

Map pipeline:

```text
Extractor
ContentPlanner
Deck IR
Design
Editor
Exporter
End-to-end
```

và đánh dấu:

```text
TEST
METRIC
LLM-JUDGE
HUMAN
```

### 3. Quality Dimensions

Chốt **cái gì đáng đo**, chưa chốt công thức.

Ví dụ:

```text
Fidelity
Coverage
Structure
Slide-type appropriateness
Readability
Visual consistency
Ready-to-use
Reliability
Latency
Cost
```

### 4. Comparison Strategy

Định nghĩa:

```text
current vs previous
current vs baseline
full vs ablation
current vs external system (nếu feasible)
```

### 5. Benchmark Strategy

Xác định:

```text
input types
difficulty
number of test cases
frozen benchmark
run metadata
versioning
```

**Xong file này rồi mới đi nghiên cứu:**

> “Fidelity nên đo bằng metric nào?”

> “Structure nên dùng pairwise judge hay rubric?”

> “Ready-to-use đo tự động hay human?”

---

## Tôi cũng hiểu lại đúng vai trò của docs thầy giao

Những docs đó **không phải chân lý đã chốt**.

Chính DoD của team nói rõ đây là tài liệu để **verify hướng trước khi khóa design**, không phải kiến trúc mặc định phải giữ nguyên. 

Và workflow verification yêu cầu cả team phải xác định rõ bước nào quyết định nội dung, bước nào chỉ format/export, user can thiệp ở đâu và luồng có thực sự phục vụ mục tiêu “dùng được ngay / chỉ sửa nhẹ” hay không. 

Nên **task của bạn còn có quyền challenge chính phần Evaluation §8 hiện tại**.

Tức là bạn không phải:

> “Implement metric mà AI-generated docs đã ghi.”

Mà phải:

> “Kiểm tra xem evaluation proposal đó có đúng không, thiếu gì, metric nào thực sự usable cho development, metric nào chỉ phù hợp final report, rồi thiết kế lại thành framework mà team có thể chạy.”

**Đây mới là bài toán tôi hiểu bạn đang làm.**

Bước tiếp theo tôi đề nghị là chúng ta **không nói chung nữa**. Tôi sẽ lấy toàn bộ FR + NFR + architecture bạn vừa gửi và dựng cho bạn một bảng:

**`Requirement / component → cần chứng minh gì → test hay evaluation → candidate metric → dùng hằng ngày hay final benchmark → priority`**

Từ bảng đó chúng ta sẽ nhìn được ngay **bộ evaluation tối thiểu v0 phải build trước**, rồi mới research từng metric một.
