# Current Evaluation Audit (RQ00 output)

Thực hiện theo `02_research_questions/RQ00_current_evaluation_audit.md`. Đây là **research routing map**, không phải metric registry — mục tiêu là biết Deck Agent thực sự cần đo gì, và phần nào cần external research để trả lời đúng. Không search literature, không chọn metric cụ thể, không sửa `06_design/`/`DECISION_LOG.md`, không tự unblock RQ01–RQ08 ở file này.

## Nguồn đã đọc (toàn bộ, không chỉ trang đầu)

| File | Số trang | Đọc đầy đủ? |
| --- | --- | --- |
| `01_source_docs/architecture/kien-truc-do-an.pdf` | 6 | ✓ |
| `01_source_docs/requirements/requirements-functional.pdf` | 5 | ✓ |
| `01_source_docs/requirements/requirements-non-functional.pdf` | 3 | ✓ |
| `01_source_docs/verification/00-cach-dung.pdf` | 1 | ✓ |
| `01_source_docs/verification/01-verify-workflow.pdf` | 1 | ✓ |
| `01_source_docs/verification/03-research-evaluation.pdf` | 1 | ✓ |

**PROJECT SOURCE** — mọi claim trong bảng dưới trích từ 6 file này, không phải external evidence.

## Bảng audit chính

Cột "Candidate Evaluation Method" chỉ là hướng khả dĩ (chưa phải formula). Cột "Needs External Research?" = YES nếu Evaluation Nature là "AI quality" hoặc phương pháp còn ambiguous; NO nếu đã rõ ràng deterministic/operational.

**Lưu ý về "Dev Harness?" và "Thesis Evidence?":** ✓ ở "Dev Harness?" nghĩa là candidate phù hợp chạy lặp lại trong quá trình dev (rẻ, nhanh). ✓ ở "Thesis Evidence?" nghĩa là candidate **có thể** dùng làm evidence cho báo cáo/bảo vệ — **không phải tất cả các dòng có ✓ đều có trọng số ngang nhau trong final comparison**. Vd. schema validation là ✓ Thesis Evidence chỉ ở mức phụ lục/supporting, trong khi source fidelity hay structure quality là ✓ ở mức trọng tâm. Việc phân trọng số cụ thể thuộc về Wave 2 (RQ02–RQ05) và `06_design/EVALUATION_FRAMEWORK.md` §Evaluation Rigor Policy, không quyết định ở đây.

| Requirement | Product/System Claim | Evidence cần có | Evaluation Nature | Candidate Evaluation Method | Dev Harness? | Thesis Evidence? | §8 cover? | Gap | Needs External Research? |
| --- | --- | --- | --- | --- | :-: | :-: | --- | --- | :-: |
| FR-01 | Upload PDF → Deck IR hợp lệ | Schema validate pass | Deterministic correctness | Automated deterministic | ✓ | | Không (nhưng NFR-21/50 cover) | Không | NO |
| FR-02 | ContentPlanner quyết định số slide/nội dung/layout theo `slide_type`; cùng PDF đổi `slide_type` → cấu trúc khác có chủ đích | So sánh cấu trúc deck giữa các `slide_type` trên cùng input | AI quality | Human/LLM judge candidate | ✓ | ✓ | Một phần (Structure) | §8 không định nghĩa cách đo "khác biệt có chủ đích" cụ thể | **YES** |
| FR-03 | Block nội dung trace được nguồn (`source_ref`); không bịa số liệu/ý chính | Kiểm tra `source_ref` tồn tại + đúng; kiểm tra nội dung khớp nguồn | AI quality (có phần deterministic: presence check của `source_ref`) | Hybrid (deterministic presence check + judge/human cho accuracy) | ✓ | ✓ | Có (Content fidelity) | Method chấm "đúng/đủ, không bịa" chưa cụ thể | **YES** |
| FR-04 | Export .pptx mở được; nội dung đúng nguồn ở "mức chấp nhận demo" | Test mở file; kiểm tra nội dung | Deterministic (mở file) + AI quality (mức chấp nhận nội dung) | Automated deterministic (mở file) / TBD (nội dung) | ✓ | ✓ | Một phần | "Mức chấp nhận demo" không có threshold rõ | Một phần (NO cho mở file, YES cho threshold nội dung) |
| FR-05 | Deck IR (Pydantic) là nguồn sự thật trung gian, mọi module đọc/ghi đúng schema | Test validate | Deterministic correctness | Automated deterministic | ✓ | | Không (nhưng NFR-21/50 cover) | Không | NO |
| FR-06 | Báo lỗi rõ khi pipeline fail; không treo UI | Test lỗi + message | Deterministic/Operational | Automated deterministic | ✓ | | Không | Không phải AI-quality concern | NO |
| FR-07 | Có plan/outline kiểm tra được trước khi generate chi tiết | Artifact plan tồn tại + kiểm tra được | Operational (tồn tại) + AI quality (plan có tốt không) | Hybrid | ✓ | | Gián tiếp qua Structure | Chất lượng của plan tự nó chưa được tách ra đánh giá riêng khỏi deck cuối | **YES** |
| FR-10–13 | Design token engine, lưu mẫu, lock, lan truyền token atomic | Diff token trước/sau; test lock | Deterministic correctness | Automated deterministic (diff) | ✓ | ✓ | Có (Design consistency) + NFR-52 cho candidate metric cụ thể (`% element dùng đúng token`) | Phần perceptual (token áp dụng có "đẹp"/coherent không) chưa tách khỏi phần deterministic | Một phần (YES cho phần perceptual) |
| FR-14a | Editor correctness: lệnh chat áp dụng đúng lên đúng block/slide, session state giữ, vùng lock không bị đụng | State-diff test: vùng lock không đổi | Deterministic correctness | Automated deterministic (state diff) | ✓ | ✓ | Không — §8 không nhắc Editor Loop | Editor behaviour hoàn toàn vắng mặt trong §8, dù là P1 | NO |
| FR-14b | Editor instruction-following: agent hiểu đúng **ý nghĩa** lệnh tự nhiên (vd. "làm slide 3 ngắn gọn hơn nhưng giữ nguyên biểu đồ") — code có thể chạy đúng schema nhưng sửa sai semantics | So sánh kết quả sửa với ý định lệnh gốc | AI quality (task success trên natural-language instruction) | Human/LLM judge candidate | ✓ | ✓ | Không | Bị Claude gộp nhầm vào "deterministic" ở audit lần trước — đây thực chất là AI-quality, tách biệt khỏi FR-14a | **YES** |
| FR-15–17 | Annotate-lock, regen 1 slide không phá slide khác, add/remove/reorder đúng | State-diff test: slide khác giữ nguyên, thứ tự/số slide đúng | Deterministic correctness | Automated deterministic (state diff) | ✓ | ✓ | Không — §8 không nhắc Editor Loop | Editor behaviour hoàn toàn vắng mặt trong §8, dù có 4 FR (P1) cho nó | NO (test được bằng deterministic state-diff) |
| FR-18a | Adapter compatibility: input Word/web/YouTube được nhận, trả đúng schema chung cho Extractor | Test theo adapter mới | Deterministic (engineering) | Automated deterministic | ✓ | | Không | Adapter correctness, không phải AI-quality | NO |
| FR-18b | Content fidelity vẫn giữ được khi nguồn là Word/web/YouTube ("fidelity vẫn audit được") — claim riêng, không tự động đúng chỉ vì adapter hoạt động | So sánh fidelity trên input type mới vs. PDF (baseline đã có) | AI quality | Human/LLM judge candidate (cùng method với FR-03/RQ02, áp dụng chéo input type) | ✓ | ✓ | Không | Bị Claude gộp nhầm vào "deterministic" ở audit lần trước; đây là claim về giữ chất lượng, không phải claim về việc adapter chạy được | **YES** |
| FR-19 | Exporter thứ 2 (Marp/HTML); 1 Deck → 2 định dạng chứng minh IR đúng | Cross-format consistency check | Deterministic correctness | Automated deterministic | ✓ | | Không | Không | NO |
| FR-20 | `slide_type` (teaching/catchup/speaker_support) tạo khác biệt đo được về cách dùng chữ/ảnh/mật độ — **nguồn tự ghi "có checklist eval"** | Checklist theo `slide_type` | AI quality | Human/LLM judge candidate — checklist **đã được đề cập trong chính FR-20**, chưa thiết kế | ✓ | ✓ | Có (Structure) | Checklist cụ thể chưa tồn tại | **YES** |
| FR-21 | Layout logic tự chọn theo loại nội dung (abstraction trên IR) | So sánh layout chọn vs. loại content | AI quality | TBD | ✓ | ✓ | Một phần (Structure) | Chưa rõ cách đo "layout logic phù hợp" | **YES** |
| FR-22 | Ready-to-use: auto-fit/chống tràn text; không chữ cắt/overlap thô trên bộ test mẫu | Overflow/geometry check | Deterministic correctness — **§8 xếp Ready-to-use vào nhóm judge-scored, nhưng phần cụ thể này (overflow) lại deterministic-checkable** | Automated deterministic | ✓ | ✓ | Có (Ready-to-use) | §8 có thể đang gộp một dimension deterministic vào nhóm cần judge — xem ghi chú bên dưới | NO cho phần overflow; phần còn lại của Ready-to-use (RQ05) vẫn YES |
| FR-23 | Deck tuân thủ ràng buộc thời gian/độ dài đã chọn | Đếm slide/ước lượng thời gian vs. constraint | Deterministic correctness | Automated deterministic | ✓ | ✓ | Không | Không | NO |
| NFR-01–03 | Latency deck ~10 slide ≤ 90–120s; streaming; propagate token ≤2s | Đo wall-clock, P50/P95 | Operational | Telemetry | ✓ | ✓ | Không — §8 chỉ nói AI-quality, không nhắc latency | Latency/performance hoàn toàn ngoài phạm vi §8 | NO |
| NFR-10–12 | Token race-safety, atomic propagate, schema versioning | Test concurrency + rollback | Deterministic correctness | Automated deterministic | ✓ | | Chồng lấn rộng với Design consistency | Không | NO |
| NFR-20–22 | Retry có giới hạn; validate output LLM bắt buộc; partial failure giữ phần thành công | Đo failure rate, retry rate | Deterministic/Operational | Telemetry + automated deterministic | ✓ | ✓ | Không | Reliability metric (failure/retry rate) không nằm trong §8 dù được scoping ban đầu liệt kê là candidate | NO |
| NFR-30–31 | Nhiều user cùng lúc; soft cap slide ≤40 | Load test | Operational | Telemetry | | | Không | Ngoài phạm vi AI-quality | NO |
| NFR-40–41 | Cách ly file/user; không log nội dung nhạy cảm ra public | Audit log/config | Deterministic/Security | Automated deterministic | | ✓ | Không | Không phải evaluation concern của workspace này (security/privacy) | NO |
| NFR-42 | Prompt injection từ tài liệu: sanitize; **"eval có case adversarial nhẹ"** | Red-team test case | AI quality (adversarial robustness) | Human/LLM judge candidate hoặc red-team test set | | ✓ | Không — §8 hoàn toàn không nhắc adversarial robustness | Gap thật: NFR tự yêu cầu "eval adversarial" nhưng không RQ nào hiện cover | **YES** |
| NFR-43 | Share link không đoán được URL | Security test | Deterministic/Security | Automated deterministic | | | Không | Không phải evaluation concern | NO |
| NFR-50 | Module deterministic: unit + golden file (Adapters, schema, exporters) | Test suite | Deterministic correctness | Automated deterministic | ✓ | | Không explicit nhưng đồng hướng §8 | Không | NO |
| NFR-51 | LLM mock trong CI; structural check định kỳ, không gọi LLM mỗi commit | CI config | Operational (test infra) | Automated deterministic | ✓ | | Không | Ảnh hưởng tới thiết kế Evaluation Pipeline (khi nào chạy LLM-dependent eval) | NO |
| NFR-52 | Đo design consistency — **nguồn tự đề xuất candidate cụ thể**: `% element dùng đúng token` HOẶC checklist MLLM-judge | Diff token + checklist | AI quality (phần checklist) + Deterministic (phần %) | Hybrid — **candidate có sẵn trong NFR để đưa vào landscape comparison ở RQ04/RQ06, KHÔNG coi là preferred method trước khi review external landscape** (tránh confirmation research) | ✓ | ✓ | Có (Design consistency) | Chưa chọn giữa 2 candidate, chưa biết candidate nào phù hợp dev tracking vs final | **YES** |
| NFR-53 | Content fidelity — **candidate cụ thể**: checklist theo instance + human correlation trên mẫu con | Checklist + human sample | AI quality | Human/LLM judge candidate — **candidate có sẵn trong NFR để đưa vào landscape comparison ở RQ02/RQ06, KHÔNG coi là preferred method trước khi review external landscape** | ✓ | ✓ | Có (Content fidelity) | Cỡ mẫu human chưa xác định — đây chính là câu hỏi `03-research-evaluation.pdf` đặt ra ("cần bao nhiêu mẫu human") | **YES** |
| NFR-60–62 | Maintainability/extensibility (thêm layout/input/output không sửa core) | Code review / architecture test | Not an evaluation concern | — | | | Không | Đây là engineering quality, không phải product/AI quality | NO |
| NFR-70–71 | Xử lý lệnh mơ hồ (hỏi lại); loading state rõ ràng theo bước | UX test | AI quality (mơ hồ) + Deterministic (loading state) | TBD / Automated deterministic | | | Không | Nhỏ, chưa rõ có đáng một RQ riêng không | Một phần (YES cho phần "mơ hồ") |
| NFR-80–82 | Preview và PPTX cùng geometry/token source; mở được trên bộ mẫu cố định; animation tắt mặc định | Golden file + open-test trên fixed sample | Deterministic correctness | Automated deterministic | ✓ | ✓ | Chồng lấn với Ready-to-use | Không | NO |
| NFR-90–92 | Ước lượng/đo $/deck theo model routing; soft/hard cap; báo cáo trade-off số lần gọi LLM vs chất lượng | Đo cost/deck, log routing | Operational | Telemetry | ✓ | ✓ | Không — §8 hoàn toàn không nhắc cost | Cost hoàn toàn ngoài phạm vi §8 dù NFR yêu cầu rõ phải "báo cáo trade-off ... vs chất lượng" — nghĩa là cần liên kết cost với quality metric, hiện chưa có cầu nối | NO (đo cost dễ) nhưng liên kết cost↔quality trade-off cần RQ tồn tại trước |
| §8 (method) | MLLM-as-judge "theo hướng PresentBench" + human annotation mẫu con; lưu ý self-preference bias | — | — Đây là method proposal, không phải evaluation target | — | — | — | — | **"PresentBench" là tên cụ thể được nguồn nội bộ trích dẫn — CHƯA VERIFY đây có phải paper/benchmark thật, và nó thực sự đề xuất gì.** | **YES — ưu tiên cao** |

## Phát hiện quan trọng cần chú ý riêng

### 1. `PresentBench` — UNVERIFIED ENTITY, ưu tiên verify trước

**PROJECT SOURCE:** Architecture doc §8 viết: *"Phương pháp: kết hợp MLLM-as-judge (theo hướng PresentBench - checklist chi tiết riêng cho từng input, judge chấm từng mục kèm bằng chứng)..."*. Đây là tên riêng cụ thể (không phải mô tả chung chung), nhiều khả năng trỏ tới một paper/benchmark thật — nhưng **workspace này chưa verify nó tồn tại, càng chưa verify nó thực sự đề xuất gì**.

Verify việc này nên là **việc đầu tiên khi Wave 1 bắt đầu** (không gắn cứng vào riêng RQ06) — nếu PresentBench thật sự là benchmark cho presentation generation, nó có thể liên quan tới cả RQ01 (dimensions nó dùng), RQ06 (judge protocol), RQ07 (benchmark design), và có thể cả RQ02/RQ04 (cách nó chấm fidelity/design). Sau khi verify xong, route evidence sang đúng RQ liên quan thay vì gói gọn trong RQ06. Theo đúng `04_evidence/EVIDENCE_POLICY.md`: không dùng tên chưa verify làm evidence.

### 2. Có mismatch nhỏ trong cách §8 nhóm "Ready-to-use"

**INFERENCE:** §8 xếp "Ready-to-use" vào nhóm 4 dimension cần MLLM-as-judge, nhưng FR-22 (auto-fit/chống tràn) — phần chiếm tỷ trọng lớn của "ready-to-use" — lại là thứ **deterministic-checkable** (geometry overflow detection), không cần judge. Điều này gợi ý rằng §8 có thể đang gộp một phần deterministic vào nhóm cần judge, làm evaluation tốn kém hơn cần thiết cho development tracking. Đây là INFERENCE, chưa phải kết luận — cần RQ05 xác nhận phần nào của "ready-to-use" thực sự cần judge/human, phần nào rule-based là đủ.

### 3. NFR-52 và NFR-53 đã có candidate method cụ thể — không phải khoảng trống hoàn toàn

Khác với suy đoán ban đầu (RQ02/RQ06 "chưa có gì"), NFR doc thực ra **đã đề xuất method cụ thể**:
- NFR-52: `% element dùng đúng token` HOẶC checklist MLLM-judge cho design consistency.
- NFR-53: checklist theo instance + human correlation trên mẫu con cho content fidelity.

Đây vẫn là **PROJECT SOURCE, chưa phải external evidence**. RQ02/RQ04/RQ06 vẫn phải research toàn bộ landscape từ đầu (what methods exist → what do they measure → where do our internal candidates fit → are they sufficient → what's missing) — **2 candidate này chỉ được đưa vào so sánh như một trong các ứng viên, không được coi là preferred method trước khi review xong landscape bên ngoài.** Nếu đảo ngược thứ tự (lấy candidate nội bộ rồi đi tìm paper support nó) sẽ thành confirmation research, không phải research thật.

### 4. Layer "Operational" (latency, cost, reliability) hoàn toàn vắng mặt trong §8

§8 chỉ nói về AI-quality (Content fidelity, Ready-to-use, Design consistency, Structure). Latency (NFR-01-03), reliability (NFR-20-22), và đặc biệt cost (NFR-90-92, vốn NFR yêu cầu rõ phải "báo cáo trade-off số lần gọi LLM vs chất lượng") **không nằm trong architecture proposal cho evaluation** dù đây chính là dữ liệu cần để trả lời câu hỏi #6 trong `EVALUATION_MISSION.md` ("Improvement về quality có đánh đổi latency, cost, hay reliability không?"). Đây không cần external research nhiều (đo lường operational khá chuẩn), nhưng **cần được thêm vào `06_design/EVALUATION_FRAMEWORK.md` như một phần chính thức**, không chỉ nằm rải rác trong scoping ban đầu.

### 5. Editor Loop (FR-14–17) hoàn toàn vắng mặt trong §8

4 FR (P1, Must giai đoạn 2) mô tả hành vi Editor Loop (chat edit, annotate-lock, regen isolation, thao tác slide), nhưng §8 không nhắc gì tới việc evaluate phần này. Test được bằng deterministic state-diff (không cần AI-quality machinery), nên rủi ro thấp nếu bị bỏ qua — nhưng vẫn là gap đáng ghi nhận vì đây là P1 Must.

### 6. Adversarial robustness (NFR-42) là gap thật, chưa RQ nào cover

NFR-42 tự yêu cầu: *"Prompt injection từ tài liệu: sanitize... eval có case adversarial nhẹ"*. Đây là yêu cầu evaluation rõ ràng từ chính NFR doc, nhưng không nằm trong §8 và không RQ01–08 nào hiện tại cover nó.

## Cross-check với `03-research-evaluation.pdf` (task brief — authority về scope, không phải technical truth)

| Câu hỏi trong task brief | RQ hiện tại cover? | Ghi chú |
| --- | --- | --- |
| Evaluation trả lời câu hỏi nghiên cứu/thầy nào? | Có — `00_context/EVALUATION_MISSION.md` | — |
| Khía cạnh chất lượng cần chấm là gì? | RQ01 | — |
| **Cái nào đo được, cái nào chỉ quan sát định tính — chấp nhận mức nào cho đồ án?** | **Chưa có RQ nào cover trực tiếp** | Đây là câu hỏi về ngưỡng rigor chấp nhận được cho phạm vi đồ án, khác với "đo cái gì" (RQ01). Đề xuất bên dưới. |
| Dùng người/model-as-judge/metric tự động/kết hợp? Vì sao? | RQ06 | — |
| Bias tự động cần nêu (self-preference...) và cách giảm? | RQ06 (nhưng chưa explicit) | §8 đã tự nhắc self-preference bias — nên RQ06 explicit hóa việc này thay vì generic |
| **Checklist theo từng instance hay rubric chung? Ưu nhược?** | **Chưa có RQ explicit** | Sub-question hợp lý của RQ06/RQ07, hiện chưa tách riêng |
| Cần bao nhiêu mẫu human để tin tương quan judge-người? | RQ06 (liên kết trực tiếp với NFR-53) | — |
| Chạy evaluation khi nào trong timeline? | Không phải RQ — thuộc `06_design/EVALUATION_PIPELINE.md` | Process/design question, không phải research question |
| Output evaluation (báo cáo) cần loại số liệu/bảng nào? | Không phải RQ — thuộc `06_design/EVALUATION_PIPELINE.md` | — |
| Giới hạn phương pháp viết vào báo cáo thế nào? | Không phải RQ — thuộc report template trong `06_design/EVALUATION_PIPELINE.md` | — |
| Có 3–5 checklist mẫu trên input giả định chưa? | Deliverable cụ thể, phụ thuộc RQ02/RQ03/RQ04/RQ07 hoàn tất | Chưa có, đúng như kỳ vọng ở giai đoạn này |

## Đề xuất (PROPOSAL — chưa tự áp dụng, chờ review trong `05_decisions/OPEN_QUESTIONS.md`)

Không tự cập nhật `RQ_INDEX.md`. Đề xuất cụ thể đã ghi thành Q-005..Q-008 trong `05_decisions/OPEN_QUESTIONS.md`:

1. Giữ nguyên RQ01–RQ08 — audit không tìm thấy lý do để merge/split/xóa bất kỳ RQ nào trong 8 câu hiện tại.
2. Thêm mới: một RQ (hoặc mở rộng RQ01) cho câu hỏi "đo được vs định tính, ngưỡng rigor chấp nhận cho phạm vi đồ án" — task brief đặt câu hỏi này trực tiếp mà chưa RQ nào cover.
3. Explicit hóa trong RQ06: (a) bias mitigation cho self-preference, (b) checklist-per-instance vs rubric chung, (c) validate 2 candidate method đã có sẵn trong NFR-52/NFR-53 thay vì research từ đầu.
4. Thêm ghi chú ưu tiên cao: verify "PresentBench" là gì trước khi RQ06 đi xa hơn.
5. Cân nhắc (không bắt buộc) một RQ cho adversarial robustness (NFR-42) — hoặc quyết định đây thuộc phạm vi security testing của team, ngoài phạm vi evaluation workspace này.
6. Latency/cost/reliability (Layer Operational) cần được đưa vào `06_design/EVALUATION_FRAMEWORK.md` như một phần chính thức — không cần RQ riêng (ít cần external research) nhưng cần thêm task loại "design" chứ không phải "research".
