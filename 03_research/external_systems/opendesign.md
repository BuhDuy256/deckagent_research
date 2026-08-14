System: Open Design
Nguồn (repo/docs): `https://github.com/nexu-io/open-design` (official repo) · `https://open-design.ai/` (canonical site) · `https://open-design.ai/official/` (trang tự tuyên bố nguồn canonical + alias) · `docs/skills-protocol.md` trong repo
Version/ngày check: **Check 2026-08-14.** Latest release tag `open-design-v0.19.1`, published 2026-08-14. Repo created 2026-04-28; last push 2026-08-14; license Apache-2.0; 85,916 stars; 794 open issues (số liệu qua GitHub API, cùng ngày).

> **Trạng thái canonical repo: VERIFIED cho project mang tên "Open Design".**
> **Trạng thái liên kết với NFR mục 0.1: CHƯA VERIFY** — xem §Ghi chú quan trọng bên dưới. Đây là hai câu hỏi khác nhau và không được gộp.

Bài toán giải quyết: Open-source alternative cho Claude Design (công cụ design đóng của Anthropic). Cho phép người dùng sinh design artifact bằng chính coding agent chạy local, thay vì phụ thuộc dịch vụ cloud độc quyền. **EXTERNAL EVIDENCE (E1, official repo + site).**

Input: Text brief mô tả nhu cầu thiết kế, kèm design system / plugin / template tùy chọn. **Không phải** tài liệu nguồn dài cần trích trung thực.

Output: HTML artifact (single-page), **PPTX**, PDF, MP4, image — repo nhấn mạnh là "real files", không phải mockup. Với deck: *"Preview: `html` (single-file deck with in-page navigation). Primary output: `index.html`. Secondary output: `slides.json` (for PPTX export)"* (trích `docs/skills-protocol.md`).

Architecture: Desktop app local-first (macOS/Windows). Express daemon backed by SQLite; frontend Next.js; artifact render trong sandboxed iframe; daemon spawn 20+ coding-agent CLI phát hiện được trên PATH; expose REST API cho skill/template/plugin/design-system; có MCP server; có BYOK proxy tại `/api/proxy/` cho endpoint OpenAI-compatible (có SSRF protection).

Intermediate representation (nếu có): **Có, nhưng ở vị trí ngược với Deck Agent.** `slides.json` là **secondary output** sinh ra *để phục vụ* PPTX export, còn `index.html` mới là primary output. Tức là **HTML-first**: bản trình chiếu thật là HTML, JSON là phụ phẩm cho việc chuyển định dạng.

Agent workflow (nếu có): Skill theo convention `SKILL.md` của Claude Code — mỗi skill là một thư mục có manifest YAML frontmatter (`name`, `description`, `triggers`) + hướng dẫn workflow Markdown, kèm `assets/` và `references/` tùy chọn; có phần mở rộng riêng dưới khóa `od:`. Reference implementation cho deck nằm ở `design-templates/guizang-ppt/`.

Evaluation/testing: Repo có thư mục `e2e/` cho end-to-end test. **README không công bố benchmark, metric, hay evaluation framework nào cho chất lượng deck.** Đây là điểm khác biệt đáng chú ý: dự án rất lớn về mặt adoption nhưng không có evaluation artifact công khai để học theo.

Quality control: Theo mô tả workflow deck (nguồn thứ cấp, **chưa verify trực tiếp trong file skill**): làm rõ topic + số slide → chọn theme → populate slide từ layout catalog → **self-check theo một quality rubric**. Cần đọc trực tiếp file skill deck trong repo để verify chi tiết rubric này trước khi trích dẫn.

Open-source artifact: Có — Apache-2.0, source đầy đủ, CLI, MCP server, docs trong repo.

Điểm giống Deck Agent:
- Sinh deck bằng agent và xuất được **PPTX**.
- Có một cấu trúc JSON trung gian giữa "nội dung" và "file xuất".
- Mô hình **BYOK** (user tự cấp API key) — đúng phương án mà NFR mục 0.1 gọi là "User trả (BYOK)".
- Agent-native (skill/CLI/MCP), gần với hướng "agent-native" ở NFR mục 0.2.

Điểm khác quan trọng (**INFERENCE**, dựa trên tài liệu đã đọc):
1. **Bài toán đầu vào khác hẳn.** Input là brief mô tả ý tưởng, không phải tài liệu nguồn dài. Nghĩa là **content fidelity / source traceability (FR-03, NFR-53) không phải bài toán của họ** — đây là khác biệt quyết định, không phải chi tiết nhỏ.
2. **Vị trí của intermediate representation ngược nhau.** Open Design: HTML-first, `slides.json` là secondary cho export. Deck Agent: **IR-first** — Deck IR là contract trung tâm, "mọi thứ bên trái không biết output cuối là gì, mọi thứ bên phải không biết input gốc là gì" (PROJECT SOURCE, architecture doc). Đây là contrast kiến trúc đáng đưa vào báo cáo.
3. **Không có deck-level conditioning tương đương `slide_type`** được document (`teaching | catchup | speaker_support`).
4. **Không có evaluation framework công bố** để đối chiếu.
5. Phạm vi rộng hơn nhiều (prototype, dashboard, landing page, image, video, HyperFrames) — deck chỉ là một mode.

Bài học có thể rút ra:
- **Mô hình BYOK có tiền lệ thật và quy mô lớn** — hỗ trợ cho việc NFR mục 0.1 coi BYOK là phương án nghiêm túc (dù NFR kết luận "chốt kỳ này: team trả token").
- **Contrast IR-first vs HTML-first** là một luận điểm kiến trúc có giá trị cho báo cáo: Deck Agent có thể lập luận vì sao đặt IR ở trung tâm thay vì coi HTML là bản gốc.
- **Adoption lớn không đồng nghĩa có evaluation.** Một dự án 85.9k stars vẫn không công bố metric chất lượng deck nào — củng cố lý do tồn tại của workspace evaluation này.

Có thể làm baseline không? Vì sao?
**Có thể, nhưng chỉ ở mức CONTEXTUAL — không phải fair baseline cho câu hỏi kiến trúc.**
- **Thuận lợi:** open-source + BYOK ⇒ **chạy được cùng model với Deck Agent**, loại bỏ được biến gây nhiễu lớn nhất khi so với sản phẩm đóng. Có CLI + MCP nên chạy lại được (reproducible). Đây là external system **khả thi nhất** trong tất cả candidate đã khảo sát (xem `03_research/RQ08/recommendation.md` §4.1(c), ưu tiên O1).
- **Giới hạn:** input là brief chứ không phải document grounding ⇒ delta đo được **không** diễn giải được thành "kiến trúc của ai tốt hơn". Nếu ép nó nhận PDF dài để so cho "công bằng" thì là chạy nó ngoài điều kiện thiết kế ⇒ **strawman**.
- **Kết luận đề xuất:** dùng như **related system / architecture contrast**, không gọi là "competitor". Nếu có chạy so sánh, câu trong báo cáo phải ghi rõ ngày check, version release, cấu hình, và phạm vi claim.

Có thể inspire benchmark không? Vì sao?
**Hạn chế.** Không có benchmark/metric công khai để mượn. Thứ có thể học là **workflow deck** (làm rõ topic + số slide → theme → populate từ layout catalog → self-check theo rubric) như một ví dụ về self-critique loop, liên quan tới Editor Loop (FR-14–17). Để inspire benchmark, các nguồn tốt hơn nhiều là PresentBench, SlidesBench, UniPPTBench (xem `03_research/RQ08/evidence.md`).

---

## Ghi chú quan trọng

### 1. PROJECT SOURCE nói gì — và không nói gì

`01_source_docs/requirements/requirements-non-functional.pdf` mục 0.1 ("Ai trả tiền token?") ghi đúng một dòng, đã đọc trực tiếp:

> *"User trả (BYOK) | Chi phí = 0 cho team; **gần Open Design** | Onboarding khó; không thống nhất model khi eval | Could sau bảo vệ / soft launch"*

Đây là **một câu so sánh về mô hình trả token**, nằm trong bảng quyết định chi phí. Nó **không** nêu URL, không nêu repository, không nói gì về kiến trúc/chất lượng deck của Open Design.

**INFERENCE (confidence thấp–trung bình):** mô tả BYOK + local-first của `nexu-io/open-design` khớp với ngữ cảnh câu này. Nhưng **chưa được team xác nhận**, và không được coi là đã verify. Đây là câu hỏi mở U-02 trong `03_research/RQ08/recommendation.md` §4.3.

### 2. Trùng tên là bẫy thật — đã verify có ít nhất 5 entity khác nhau

Search ngày 2026-08-14 (`"OpenDesign" open source design tool GitHub`) trả về nhiều project **đều tồn tại thật** nhưng **khác nhau**:

| Entity | Là gì | Có phải project ở file này không? |
| --- | --- | --- |
| `nexu-io/open-design` | AI design workspace, BYOK, local-first, xuất PPTX. Apache-2.0. Site canonical `open-design.ai` | **Đây là project được mô tả ở trên** |
| GitHub org `opendesigndev` | Org khác mang tên "Open Design" — **không** research sâu trong RQ08 | Không. Không dùng làm evidence |
| `open-design-kit/opendesignkit` | "Open Design Kit" — toolkit về quy trình design cộng tác phân tán. Không sinh slide | Không |
| `OpenCoworkAI/open-codesign` | "Open CoDesign" — desktop AI design tool, BYOK, local-first, MIT. Sản phẩm khác | Không |
| `manalkaff/opendesign` | **Tự nhận unofficial**, community-maintained; port Claude Design thành markdown skill cho coding agent; 239 stars; MIT; có skill `make-a-deck` | Không — repo tự tuyên bố unofficial |

Trang `https://open-design.ai/official/` của chính project tuyên bố các alias sau đều trỏ về cùng nó: *Open Design / OpenDesign / open-design / opendesign / Open Design AI / OD*, và *"Bookmark open-design.ai and the GitHub repo. Everything else points back to one of these two."* — **lưu ý đây là tuyên bố của chính project về chính nó (self-claim), hữu ích để xác định canonical source nhưng không phải bên thứ ba xác nhận.**

### 3. Những gì CHƯA verify trong file này

- Chưa chạy thử Open Design → mọi nhận định về chất lượng output là từ docs, không phải quan sát.
- Chưa đọc trực tiếp file skill deck trong repo → chi tiết "self-check theo quality rubric" đến từ nguồn thứ cấp, cần verify trước khi trích dẫn.
- Chưa verify cơ chế chuyển `slides.json` → PPTX (`docs/skills-protocol.md` nói rõ phần này nằm ngoài phạm vi spec đó).
- Chưa verify mô hình thương mại (release note 2026-08-14 có nhắc gói trả phí cho một số model, nhưng RQ08 không research sâu phần pricing này).
- **Không coi marketing claim trên site là benchmark evidence** — đúng theo `04_evidence/EVIDENCE_POLICY.md`.

### 4. Capability và version thay đổi theo thời gian

Mọi số liệu trong file này gắn với ngày **2026-08-14** và release **`open-design-v0.19.1`**. Repo được push cùng ngày check và có tốc độ release cao (created 2026-04-28 → v0.19.1 trong ~3.5 tháng), nên **thông tin ở đây hết hạn nhanh**. Phải re-check trước khi dùng trong báo cáo/bảo vệ.
