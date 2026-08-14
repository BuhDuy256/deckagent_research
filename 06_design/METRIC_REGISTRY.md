# Metric Registry

Trạng thái: **trống**. Đây là registry cụ thể các metric — mỗi entry một metric, có formula/procedure — mà câu trả lời của RQ02–RQ06 sẽ điền vào.

## Format mỗi metric

```
### <tên metric>
Dimension: <thuộc quality dimension nào, theo 00_context/TERMINOLOGY.md>
Loại: deterministic | LLM-judge | human
Định nghĩa: <formula hoặc scoring procedure chính xác>
Input cần: <dữ liệu gì>
Dùng cho: development tracking | system validation | comparative evaluation
Dev metric hay Final metric hay cả hai: <xem 06_design/EVALUATION_FRAMEWORK.md §2>
Source RQ: <RQ0x>
Evidence level: <E1/E2/E3/UNVERIFIED — xem 04_evidence/EVIDENCE_POLICY.md, nếu formula dựa trên external research>
```

---

_(chưa có metric nào được đăng ký)_

## Candidate metric name từ scoping ban đầu (chưa verify, chưa có formula)

- PPTX export success rate
- Schema validation rate
- Overflow rate
- Lock violation rate
- Token consistency rate
- Latency P50 / P95
- Cost/deck
- Failure rate
- Retry rate

Đây mới chỉ là **tên** metric ở tầng operational/deterministic (Layer A trong `00_context/EVALUATION_MISSION.md`) — chưa có formula. Promote từng cái lên thành entry đầy đủ ở trên khi đã specify xong. Không tự fill 50 metric cùng lúc — populate dần theo từng RQ đã research xong.
