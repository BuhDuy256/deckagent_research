# Experiment Registry

Log các evaluation run. Mỗi hàng là một run — giữ append-only để việc so sánh lịch sử luôn trace được.

| ID | Ngày | System version | So sánh với | Benchmark version | Vị trí | Tóm tắt kết quả |
| --- | --- | --- | --- | --- | --- | --- |
| _(chưa có)_ | | | | | | |

## Quy ước

- `pilot/` — run thăm dò/quy mô nhỏ, trước khi harness hoặc metric được chốt.
- `final/` — run trên frozen benchmark dùng pipeline đã chốt, đủ tin cậy để trích dẫn trong báo cáo/bảo vệ.
- Mỗi run phải reproducible từ config đã log — link chính xác tới benchmark version và system commit/tag.
