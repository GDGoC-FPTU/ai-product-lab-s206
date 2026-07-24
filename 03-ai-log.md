# 03 — AI Log & Reflection

## Thông tin cá nhân

- **Họ tên:** `[ĐIỀN HỌ TÊN]`
- **MSSV:** `[ĐIỀN MSSV]`

> Đây là bản nháp tham khảo. Hãy sửa lại theo trải nghiệm thực tế của bạn trước khi nộp.

## AI đã giúp gì?

Tôi dùng AI như một thought-partner để brainstorm pain point trong vận hành Xanh SM, chuyển một ý tưởng rộng thành problem statement có actor, bottleneck, metric và operational boundary. AI cũng hỗ trợ viết system instruction cho Gemini, đề xuất adversarial inputs và giải thích cách truyền `system_instruction` bằng SDK `google-genai`.

Phần hữu ích nhất là việc biến yêu cầu an toàn thành điều kiện kiểm thử cụ thể: mọi output phải bắt đầu bằng `[DRAFT_ONLY]`, và khi pin dưới 5% hệ thống phải đề xuất `dispatch_mobile_charger` thay vì hướng tài xế đến trạm xa hơn 5 km.

## AI đã sai hoặc chưa tốt ở đâu?

Ban đầu AI có xu hướng xem prompt là lớp bảo vệ duy nhất. Cách này chưa đủ an toàn cho nghiệp vụ điều phối vì LLM có thể trả output không đúng định dạng hoặc bị prompt injection. AI cũng có thể trình bày các số liệu thời gian và tỷ lệ như dữ liệu thật dù đó mới chỉ là giả định scoping.

Một điểm chưa hợp lý khác là gợi ý dùng API key giả làm fallback. Key giả không giúp chương trình chạy và còn che khuất nguyên nhân thiếu cấu hình. Giải pháp rõ ràng hơn là kiểm tra biến môi trường và dừng với thông báo lỗi.

## Tôi đã sửa như thế nào?

Tôi giữ system prompt ngắn và cụ thể, đặt `temperature=0.0`, thêm hai adversarial tests và dùng assertion để kiểm tra từ khóa bắt buộc. Tôi không cho AI tự gửi tin hoặc tự điều xe; output chỉ là draft để điều phối viên duyệt. Trong thiết kế production, ngưỡng pin và khoảng cách phải được kiểm tra thêm bằng rule-based code, không dựa riêng vào câu trả lời của LLM.

Tôi cũng đánh dấu rõ các số liệu trong báo cáo là giả định cần xác thực bằng log thật. Quyết định cuối cùng là **NOT YET**, thay vì kết luận GO chỉ vì hai test mẫu đã pass.

## Bài học rút ra

AI hữu ích nhất khi hỗ trợ phân tích và tạo bản nháp, nhưng ranh giới nghiệp vụ quan trọng phải được chuyển thành rule, schema validation, test tự động và bước phê duyệt của con người. Một prototype chạy được chưa đồng nghĩa với hệ thống đã sẵn sàng vận hành thực tế.
