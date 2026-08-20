# Prompt khởi động chat Codex mới

Bạn đang tiếp tục **Deck Agent Evaluation Research** từ một session trước.

Tôi đã tạo một session handoff vì context cũ quá dài.

Trước khi làm bất kỳ task nào:

1. Đọc `HANDOFF_CURRENT_SESSION.md`.
2. Sau đó đọc **toàn bộ** danh sách **Files the new agent MUST read** trong handoff theo đúng thứ tự.
3. Coi handoff chỉ là navigation/context, không phải Source of Truth.
4. Đối chiếu mọi decision với `05_decisions/DECISION_LOG.md`.
5. Đối chiếu mọi open blocker/Q-status với `05_decisions/OPEN_QUESTIONS.md`.
6. Không suy luận trạng thái project chỉ từ tên file hoặc từ handoff.

Sau khi đọc xong, hãy trả lại cho tôi một **Context Reload Check**. Chưa làm task chính.

Context Reload Check phải gồm đúng các mục sau:

- Current phase;
- Completed waves/RQs;
- What is blocked;
- D-015→D-020, mỗi decision đúng một câu;
- Why presence ≠ correctness;
- Current role/status của MiniCheck;
- Current Option C policy;
- Why B0 is blocked;
- Difference giữa Track A và Track B;
- Exact next task options.

Không tóm tắt lại toàn bộ lịch sử research. Sau Context Reload Check, **dừng và chờ tôi confirm**.

Trước khi tôi confirm, không được:

- chạy pilot;
- research web;
- mở RQ04/RQ05/RQ07;
- sửa decision;
- sửa design file;
- tạo metric mới;
- bắt đầu thực thi Track A hoặc Track B.

Khi tôi confirm context đã load đúng, task tiếp theo sẽ là **một trong hai**, do tôi chọn:

- **Track A — Source Quality:** focused follow-up cho fact/topic presence detection, route qua RQ02/Q-016.
- **Track B — Slide Type:** product-definition workshop cho `teaching` / `catchup` / `speaker_support`, không dùng web research để quyết định product semantics.

Nếu authoritative files mâu thuẫn với handoff, hãy report conflict trong Context Reload Check; không tự sửa file hoặc decision.
