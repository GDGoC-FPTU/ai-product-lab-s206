# 01 — Problem Scan & Quick Problem Cards

> Các số liệu dưới đây là giả định ban đầu để phục vụ scoping và cần được xác thực bằng dữ liệu vận hành thực tế.

## Phase 1 — SCAN

| # | Công ty | Lens | Bài toán vận hành |
|---:|---|---|---|
| 1 | Xanh SM | Stakeholder Pain | Tài xế có pin dưới 5% phải chờ điều phối viên xác định phương án cứu hộ hoặc trạm sạc an toàn. |
| 2 | Xanh SM | Repetitive | Điều phối viên lặp lại việc đọc vị trí, mức pin và tra cứu trạm sạc cho từng yêu cầu. |
| 3 | VinFast | AI-upgrade | Yêu cầu hỗ trợ lỗi pin được phân loại thủ công nên phản hồi ban đầu chậm và không đồng nhất. |
| 4 | Vinhomes | Time-consuming | Nhân viên phải đọc, phân loại và soạn phản hồi cho phản ánh của cư dân trên nhiều kênh. |
| 5 | Vinpearl | Repetitive | Nhân viên CSKH trả lời lặp lại câu hỏi về vé, giờ mở cửa và chính sách đổi lịch. |

## Quick Problem Card 1 — Điều phối xe điện pin yếu

- **Công ty:** Xanh SM
- **Actor:** Tài xế và điều phối viên vận hành.
- **Bài toán:** Khi xe báo pin yếu, điều phối viên phải nhanh chóng chọn phương án an toàn mà không hướng tài xế đi quá xa.
- **Workflow hiện tại:** Tài xế gọi tổng đài → điều phối viên ghi nhận vị trí/mức pin → tra cứu trạm → đánh giá khoảng cách → gọi lại tài xế hoặc điều xe sạc lưu động.
- **Bottleneck:** Tra cứu và đánh giá thủ công, khoảng 8 phút/lượt.
- **AI hỗ trợ:** Tóm tắt tình huống và tạo bản nháp phương án; với pin dưới 5% phải đề xuất `dispatch_mobile_charger`.
- **Metric:** 90% yêu cầu có bản nháp dưới 30 giây; giảm thời gian xử lý trung bình từ 8 xuống dưới 3 phút; 100% tình huống pin dưới 5% không gợi ý trạm xa hơn 5 km.
- **Quick Architecture:** LLM Feature kết hợp rule cứng và Human-in-the-loop.

## Quick Problem Card 2 — Phân loại yêu cầu hỗ trợ pin

- **Công ty:** VinFast
- **Actor:** Nhân viên chăm sóc khách hàng và kỹ thuật viên.
- **Bài toán:** Ticket về pin được chuyển sai nhóm do mô tả tự do, thiếu cấu trúc.
- **Workflow hiện tại:** Nhận ticket → đọc nội dung → hỏi bổ sung → chọn nhóm lỗi → chuyển kỹ thuật viên.
- **Bottleneck:** Đọc và phân loại, khoảng 6 phút/ticket.
- **AI hỗ trợ:** Trích xuất mẫu xe, mức pin, mã lỗi, mức khẩn cấp và đề xuất nhóm xử lý.
- **Metric:** Ít nhất 85% ticket được phân loại đúng trong 10 giây; giảm tỷ lệ chuyển sai nhóm từ 15% xuống dưới 5%.
- **Quick Architecture:** LLM Feature; nhân viên duyệt trước khi chuyển ticket.

## Quick Problem Card 3 — Phản hồi phản ánh cư dân

- **Công ty:** Vinhomes
- **Actor:** Nhân viên chăm sóc cư dân.
- **Bài toán:** Soạn phản hồi ban đầu cho phản ánh cư dân tốn thời gian và thiếu nhất quán.
- **Workflow hiện tại:** Nhận phản ánh → đọc và phân loại → tra chính sách → soạn phản hồi → quản lý duyệt → gửi.
- **Bottleneck:** Tra cứu và soạn nội dung, khoảng 10 phút/lượt.
- **AI hỗ trợ:** Tóm tắt, tra nhóm chính sách được phép và tạo bản nháp có dẫn chiếu.
- **Metric:** Giảm thời gian soạn từ 10 xuống dưới 2 phút; 100% phản hồi khiếu nại nghiêm trọng được con người duyệt.
- **Quick Architecture:** LLM Feature với Human-in-the-loop.

## Lựa chọn

Nhóm chọn **trợ lý điều phối Xanh SM cho tình huống xe điện pin yếu** vì có pain point rõ, metric đo được và phù hợp với prototype safety boundary hiện tại.
