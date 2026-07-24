# Báo cáo cá nhân: Quét cơ hội ứng dụng AI - Vin Smart Future

*   **Học viên:** ĐOÀN NGỌC CHUNG
*   **MSSV:** 2A202601869
*   **Email:** 26ai.chungdn@vinuni.edu.vn

---

## Phase 1 — SCAN: Tìm kiếm bài toán thực tế

Em có quét qua hoạt động của mấy công ty trong Vin để tìm xem chỗ nào có thể áp dụng AI tối ưu quy trình được:

1.  **Xanh SM (Mảng di chuyển):** Khi khách đang đi mà đổi ý muốn đổi điểm đến giữa chừng, hệ thống phải phân bổ lại xe và tính lại tiền. Hiện tại việc này chạy lặp đi lặp lại rất nhiều. (Lens: Lặp lại)
2.  **Xanh SM (Vận hành thực địa):** Tài xế gọi về hotline báo sự cố khẩn cấp như va chạm hay xe gần hết pin. Điều phối viên đang phải mò bản đồ tra cứu trạm sạc trống và nhắn tin chỉ đường thủ công, mất rất nhiều thời gian. (Lens: Tốn thời gian)
3.  **VinFast (Mảng tài chính):** Hàng tuần phải so khớp hàng nghìn hóa đơn sạc điện từ các trạm sạc đối tác ngoài gửi về với log sạc thực tế của hệ thống để đối chiếu thanh toán. Tác vụ này lặp đi lặp lại rất nhàm chán. (Lens: Lặp lại)
4.  **Vinhomes (Mảng CSKH):** Cư dân gửi phản ánh lên ứng dụng Vinhomes Resident (hỏng bóng đèn, mất nước, ô nhiễm tiếng ồn...). Nhân viên tổng đài đọc rồi chuyển tiếp thủ công đến ban quản lý tòa nhà, thường mất cả buổi mới xong. (Lens: AI-upgrade)
5.  **Vinmec (Mảng y tế):** Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện sau khi điều trị xong (20-30 phút/ca). Việc này làm bác sĩ bị quá tải việc hành chính, bệnh nhân thì phải xếp hàng chờ lâu. (Lens: Stakeholder Pain)

---

## Phase 2 — QUICK-ASSESS: 3 Thẻ bài toán nhanh

### 1. Bài toán sự cố sạc pin thực địa của Xanh SM
*   **Bài toán:** Tài xế gọi báo xe sắp hết pin giữa đường, cần tìm trạm sạc trống gần nhất hoặc gọi cứu hộ khẩn cấp.
*   **Bên gặp khó khăn:** Tài xế (phải nằm chờ) và Điều phối viên (bị quá tải điều phối).
*   **Quy trình thủ công hiện tại:**
    *   Tài xế gọi điện lên tổng đài báo xe sắp hết pin.
    *   Điều phối viên mở bản đồ định vị xem xe đang ở đâu.
    *   Mở dashboard trạm sạc VinFast để tìm trạm trống gần nhất phù hợp dòng xe.
    *   Soạn tin nhắn chỉ đường chi tiết gửi cho tài xế.
    *   Gọi xe sạc pin lưu động nếu xe đã cạn sạch pin dưới 5%.
*   **Chỗ tốn thời gian nhất:** Đoạn check trạm trống và gõ tin nhắn hướng dẫn (mất khoảng 12 phút/lượt).
*   **AI hỗ trợ ở đâu:** Tự động lấy định vị -> tìm trạm trống phù hợp -> tự soạn tin nhắn nháp chỉ đường.
*   **Đo lường thành công:** Giảm thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút.
*   **Công nghệ đề xuất:** LLM Feature (AI soạn sẵn tin nháp để duyệt).

---

### 2. Phân loại phản ánh cư dân trên App Vinhomes
*   **Bài toán:** Tự động phân loại và chuyển phản ánh của cư dân đến đúng ban quản lý tòa nhà.
*   **Bên gặp khó khăn:** Nhân viên CSKH (mất công lọc hàng nghìn tin nhắn mỗi ngày) và Cư dân (chờ lâu).
*   **Quy trình thủ công hiện tại:**
    *   Cư dân chụp ảnh và viết nội dung khiếu nại lên app.
    *   CSKH đọc và xem ảnh để hiểu vấn đề.
    *   Phân loại xem là lỗi gì (điện, nước, vệ sinh, an ninh...).
    *   Tra cứu thông tin ban quản lý tòa nhà đó để chuyển tiếp tin nhắn.
    *   Tạo ticket theo dõi.
*   **Chỗ tốn thời gian nhất:** Đoạn đọc tin nhắn, phân loại và chuyển tiếp (mất tầm 10 phút/tin).
*   **AI hỗ trợ ở đâu:** Đọc hiểu văn bản và ảnh -> tự động gán nhãn phân loại -> tự động route đến đúng bộ phận.
*   **Đo lường thành công:** Giảm thời gian điều phối phản ánh từ 12 tiếng xuống dưới 5 phút.
*   **Công nghệ đề xuất:** LLM Feature.

---

### 3. Tự động viết tóm tắt hồ sơ xuất viện tại Vinmec
*   **Bài toán:** Tự động tổng hợp thông tin lâm sàng từ bệnh án để làm tóm tắt xuất viện dễ hiểu cho bệnh nhân.
*   **Bên gặp khó khăn:** Bác sĩ điều trị (mất nhiều thời gian viết hồ sơ) và Bệnh nhân.
*   **Quy trình thủ công hiện tại:**
    *   Bác sĩ ra quyết định cho xuất viện.
    *   Bác sĩ mở bệnh án điện tử của bệnh nhân.
    *   Đọc lại toàn bộ ghi chú khám, xét nghiệm máu, X-quang, đơn thuốc đã dùng.
    *   Gõ tay tóm tắt quá trình điều trị và viết dặn dò xuất viện bằng từ ngữ phổ thông.
    *   Ký tên và in gửi bệnh nhân.
*   **Chỗ tốn thời gian nhất:** Đoạn đọc tổng hợp tài liệu và soạn tóm tắt dễ hiểu (mất 20-30 phút/ca).
*   **AI hỗ trợ ở đâu:** Trích xuất nhanh các chỉ số xét nghiệm và ghi chú chính -> tự soạn tin nhắn dặn dò xuất viện nháp.
*   **Đo lường thành công:** Giảm thời gian bác sĩ soạn tài liệu từ 25 phút xuống dưới 5 phút.
*   **Công nghệ đề xuất:** LLM Feature.
