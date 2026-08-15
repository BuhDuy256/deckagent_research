# Nghiên cứu cá nhân — hướng dẫn cho người dẫn dắt Deck Agent

Thư mục này **không phải research output**. Nó là hệ thống để **bạn** tự xây hiểu biết về những vấn đề quan trọng của Deck Agent, thay vì chỉ đọc kết luận do AI sinh ra.

Nó không sửa gì trong `03_research/`, `05_decisions/`, `06_design/`. Chỉ trỏ tới chúng.

## Vấn đề mà thư mục này giải quyết

Bạn có ~5000 dòng research chất lượng tốt, 14 decision đã log, 14 open question, 2 decision gate. Nhưng phần lớn **lập luận** nằm trong file, không nằm trong đầu bạn.

Điều đó ổn cho tới khi:
- Hội đồng hỏi *"vì sao chọn metric này?"* và câu trả lời trung thực là *"AI bảo thế"*;
- Bạn phải quyết một chuyện chưa ai research và không có file nào để đọc;
- Một con số trong báo cáo bị kiểm tra và nó không có trong paper gốc (rủi ro này **đang có thật** — xem `03_research/RQ03/recommendation.md` P-11).

## Cách dùng

Đọc theo thứ tự lần đầu. Sau đó chỉ mở file 6 (quy trình phiên) và file 8 (bắt đầu từ đâu).

| File | Dùng khi nào |
| --- | --- |
| [1_BAN_DANG_O_DAU.md](1_BAN_DANG_O_DAU.md) | Đọc một lần. Bạn đang sở hữu gì, chưa sở hữu gì, và **cái gì không cần** sở hữu |
| [2_PHUONG_PHAP.md](2_PHUONG_PHAP.md) | Đọc một lần. Phương pháp đề xuất, nó đến từ đâu, điểm yếu của nó |
| [3_KY_NANG_PHAN_BIET.md](3_KY_NANG_PHAN_BIET.md) | Đọc một lần, quay lại tra khi mắc kẹt. 13 phân biệt quan trọng, mỗi cái có ví dụ thật từ Deck Agent |
| [4_DOC_PAPER.md](4_DOC_PAPER.md) | Mở ra mỗi khi sắp đọc một paper |
| [5_DUNG_LAI_O_DAU.md](5_DUNG_LAI_O_DAU.md) | Khi không biết nên đọc tiếp, làm thí nghiệm, hay chốt |
| [6_PHIEN_NGHIEN_CUU.md](6_PHIEN_NGHIEN_CUU.md) | **Mở mỗi phiên.** Quy trình 90 phút + cách dùng AI |
| [7_VI_DU_DAY_DU.md](7_VI_DU_DAY_DU.md) | Khi muốn thấy phương pháp chạy thật trên case Deck Agent |
| [8_BAT_DAU_TU_DAU.md](8_BAT_DAU_TU_DAU.md) | **Đọc ngay sau file 1–2.** Ba việc cụ thể bạn nên tự làm trước |
| [mau/](mau/) | Template copy ra dùng mỗi phiên |

## Nguyên tắc nền

**Ba câu này quyết định mọi thứ còn lại:**

1. **Kết luận không phải hiểu biết.** Biết "MiniCheck tốt hơn AlignScore" ≠ hiểu vì sao. Cái thứ hai mới dùng được khi gặp tình huống mới.
2. **AI làm việc rẻ để kiểm chứng; bạn làm việc đắt để kiểm chứng.** Tìm paper là rẻ (có hoặc không có). Phán đoán *"evidence từ tóm tắt văn bản có áp được cho slide không"* là đắt — không ai kiểm hộ bạn được, nên bạn phải tự làm.
3. **Không bao giờ để AI sinh ra niềm tin của bạn. Chỉ để nó tấn công niềm tin bạn đã tự viết ra.**

## Sản phẩm phụ có giá trị

Mỗi phiên nghiên cứu để lại một **phiếu niềm tin** trong `nhat_ky/`. Sau vài tháng, tập phiếu đó chính là nguyên liệu cho phần *"vì sao chúng tôi quyết định như vậy"* của báo cáo — thứ khó viết nhất và thường bị viết ngược (chốt trước, bịa lý do sau).

Nói cách khác: việc này không lấy thêm thời gian của báo cáo. Nó **là** một phần của báo cáo.
