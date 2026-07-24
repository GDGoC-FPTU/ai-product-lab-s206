
## Lựa chọn bài toán của nhóm

Sau khi bàn bạc và chấm điểm các thẻ bài toán cá nhân, nhóm quyết định chọn **Bài toán xử lý sự cố sạc pin thực địa của Xanh SM** để làm tiếp.

### Tại sao lại chọn bài toán này và bỏ các bài toán khác?
*   **Chọn sự cố sạc pin Xanh SM:** Bài toán này ảnh hưởng trực tiếp đến doanh thu hàng ngày của hãng và thời gian chờ của tài xế. Trung bình mỗi ngày ở Hà Nội có tầm 80 cuộc gọi báo pin yếu cần xử lý gấp. Quy trình này nếu làm tự động soạn tin nhắn nháp chỉ đường sẽ giảm tải rất nhiều cho điều phối viên và tránh tình trạng xe hết sạch pin nằm đường gây cản trở giao thông.
*   **Bỏ bài toán Vinhomes CSKH:** Việc tự động trả lời khiếu nại cư dân tuy tốn thời gian nhưng chứa nhiều rủi ro về mặt pháp lý (như tranh chấp căn hộ hay phí dịch vụ). Nếu AI trả lời sai hoặc hứa hẹn bừa bãi sẽ rất rắc rối. Nhóm thấy chưa đủ dữ liệu sạch và các rule an toàn để làm cái này.
*   **Bỏ bài toán viết tóm tắt bệnh án Vinmec:** Lĩnh vực y tế đòi hỏi sự chính xác tuyệt đối 100%. Mặc dù có bác sĩ duyệt lại nhưng rủi ro AI dịch sai thông số lâm sàng có thể ảnh hưởng trực tiếp đến tính mạng người bệnh. Rào cản kiểm định y tế cũng rất phức tạp.

---

## Phân tích sâu bài toán (Deep-Dive)

### Sơ đồ quy trình thủ công hiện tại
*   **Bước 1:** Điều phối viên nhận cuộc gọi khẩn cấp từ tài xế báo hết pin (mất ~2 phút).
*   **Bước 2:** Mở app định vị của hãng để check xem xe đang đỗ ở tọa độ nào (mất ~2 phút).
*   **Bước 3 (Nút thắt cổ chai):** Mở danh sách trạm sạc VinFast để lọc xem trạm nào gần nhất, còn cổng sạc trống phù hợp với dòng xe của tài xế (mất ~5 phút).
*   **Bước 4 (Nút thắt cổ chai):** Ngồi viết thủ công tin nhắn SMS chỉ đường, hướng dẫn tài xế cắm cổng nào và gửi đi (mất ~5 phút).
*   **Bước 5:** Nếu pin xe đã dưới 5%, điều phối viên phải gọi luôn xe cứu hộ pin lưu động chứ không hướng dẫn đi sạc nữa để tránh chết máy dọc đường (mất ~1 phút).
*   **Tổng thời gian xử lý thủ công:** Mất khoảng 15 phút cho một ca sự cố.

### Problem Statement (6-field) của nhóm
1.  **Actor (Người thực hiện):** Điều phối viên tại Trung tâm Điều vận Xanh SM.
2.  **Current Workflow (Quy trình hiện tại):** Khi tài xế báo hết pin, điều phối viên tự check định vị xe, mở dashboard lọc trạm sạc VinFast trống phù hợp, soạn tin nhắn chỉ đường gửi tài xế và gọi cứu hộ nếu pin dưới 5%. Tất cả đều làm bằng tay trên nhiều công cụ khác nhau.
3.  **Bottleneck (Điểm nghẽn):** Khâu tra cứu trạm sạc trống phù hợp loại xe và gõ tin nhắn chỉ dẫn tiếng Việt chi tiết (mất đến 10 phút).
4.  **Business Impact (Tác động kinh doanh):** 80 ca sự cố/ngày ngốn khoảng 20 tiếng làm việc của điều phối viên. Tài xế nằm chờ lâu gây thất thoát khoảng 15% doanh thu cuốc xe và làm giảm uy tín dịch vụ.
5.  **Success Metric (Chỉ số thành công):** Giảm tổng thời gian xử lý một ca sự cố từ 15 phút xuống dưới 3 phút. Tỉ lệ gợi ý đúng trạm và đúng cổng sạc đạt trên 98%.
6.  **Operational Boundary (Ranh giới an toàn):** AI chỉ được phép tra cứu thông tin và soạn tin nhắn dạng nháp (Draft). Tuyệt đối cấm AI tự ý gửi thẳng tin nhắn cho tài xế mà chưa có điều phối viên duyệt (Bắt buộc phải có duyệt - Human-in-the-loop). Khi lượng pin dưới 5%, AI không được chỉ đường ra trạm sạc xa quá 5km mà phải tự động đề xuất lệnh gọi xe cứu hộ sạc di động (dispatch_mobile_charger).

### Quy trình mới khi áp dụng AI (Future-State Flow)
1.  **Bước 1:** Nhận thông tin sự cố từ tài xế.
2.  **Bước 2 (AI Step):** Hệ thống tự động pull tọa độ xe và danh sách trạm sạc trống gần nhất qua API.
3.  **Bước 3 (AI Step):** AI (Gemini 2.5) đọc dữ liệu, tự động kiểm tra lượng pin. Nếu pin >= 5% thì soạn tin nháp chỉ đường có gắn tag [DRAFT_ONLY]. Nếu pin < 5% thì sinh lệnh gọi xe cứu hộ sạc di động dạng JSON.
4.  **Bước 4 (Human Step - HITL):** Điều phối viên xem tin nhắn nháp hoặc lệnh cứu hộ trên màn hình, click phê duyệt để gửi đi.
5.  **Bước 5 (Fallback - Dự phòng):** Nếu hệ thống AI bị đơ hoặc không phản hồi trong 10 giây, màn hình tự động chuyển sang chế độ tra cứu thủ công như cũ để điều phối viên xử lý.

---

## Đánh giá độ sẵn sàng (Evaluate)

### Checklist tự đánh giá:
*   [x] Nhóm đã có sẵn danh sách trạm sạc VinFast và API định vị xe để chạy thử.
*   [x] Kiểm soát được rủi ro khi AI trả lời sai (nhờ có bước điều phối viên duyệt thủ công tin nháp và chế độ dự phòng fallback).
*   [x] Các bộ phận vận hành của GSM sẵn sàng sử dụng giao diện mới này để giảm tải công việc.

### Quyết định cuối cùng:
Nhóm quyết định chọn **GO (Triển khai bản mẫu)**.

### Lý do kỹ thuật và Ước lượng chi phí:
*   **Về mặt kỹ thuật:** Giải pháp sử dụng LLM Feature đơn giản, chạy nhanh (< 1 giây) và rất dễ tích hợp vào phần mềm điều vận hiện tại của Xanh SM mà không cần hệ thống multi-agent phức tạp.
*   **Về mặt chi phí:**
    *   Mỗi lượt gọi API tốn khoảng 1500 tokens input và 200 tokens output.
    *   Giá Gemini 2.5 Flash rất rẻ ($0.075/1M input tokens và $0.3/1M output tokens).
    *   Tính ra mỗi ca sự cố tốn khoảng 4 - 5 VNĐ tiền API.
    *   Với 80 ca/ngày ở Hà Nội, chi phí API chỉ khoảng 350 - 400 VNĐ/ngày. Đây là mức đầu tư quá rẻ so với việc giải quyết ùn tắc và giúp xe sớm quay lại đón khách kiếm doanh thu.
