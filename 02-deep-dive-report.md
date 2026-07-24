# Phase 3 & 5 — DEEP-DIVE & EVALUATION REPORT

**Tên nhóm:** S206  
**Thành viên nhóm:**  
1. Nguyễn Anh Quân — 2A202601251

---

## 3.1. Quyết định lựa chọn bài toán
**Tên bài toán:** Tối ưu hóa quy trình xử lý sự cố hết pin thực địa cho tài xế Xanh SM thông qua tự động hóa tra cứu trạm sạc và định hướng.

---

## 3.2. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) tại Trung tâm Điều vận Xanh SM. |
| **2. Current Workflow** | (1) Nghe điện thoại từ tài xế ──> (2) Mở tool nội bộ tra GPS ──> (3) Mở Dashboard VinFast tra trạm sạc trống phù hợp ──> (4) Gõ tin nhắn SMS chỉ đường ──> (5) Liên hệ xe cứu hộ nếu cần. |
| **3. Bottleneck** | Bước 3 (Tra cứu trạm sạc) và Bước 4 (Soạn SMS) là nút thắt cổ chai. Phải chuyển đổi ngữ cảnh liên tục giữa các màn hình và soạn văn bản thủ công dễ sai sót. |
| **4. Business Impact** | Lãng phí trung bình 20 giờ làm việc/ngày của team Dispatcher (tính trên 80-100 sự cố/ngày). Khách hàng hủy chuyến do tài xế kẹt chờ, gây rò rỉ ~15% doanh thu ca hoạt động của tài xế đó. |
| **5. Success Metric** | - **Efficiency:** Giảm SLA (thời gian xử lý sự cố) từ 15 phút xuống dưới 3 phút/lượt.<br>- **Quality:** 98% tin nhắn điều hướng đúng chuẩn loại cổng sạc (VF5/VF8) và đúng địa chỉ. |
| **6. Operational Boundary** | - **Quyền hạn (Allowed):** AI được phép truy xuất API tọa độ, API trạm sạc và tự động soạn thảo bản nháp (Draft SMS).<br>- **Vùng cấm (Strictly Prohibited):** Tuyệt đối KHÔNG được tự động gửi tin nhắn cho tài xế khi chưa qua bước duyệt của người thật. KHÔNG được đề xuất trạm sạc cách xa quá 5km nếu pin hiện tại < 5%. |

---

## 3.3. Future-State Flow & AI Fit

* **Mức độ ứng dụng AI (AI Fit):** `[x] LLM Feature` (Kết hợp Rule-based API fetching. Không dùng Agentic Loop để đảm bảo tính an toàn và deterministic cao nhất).

**Quy trình tương lai (Future-State Flow):**
1. **Trigger:** Hệ thống nhận ticket sự cố từ App tài xế kèm tọa độ GPS và % pin (Bỏ qua khâu gọi điện).
2. 🔵 **AI Step (LLM Feature):** Hệ thống gọi API trạm sạc VinFast lấy data thô ──> LLM tự động tổng hợp thông tin, draft tin nhắn chỉ dẫn bằng tiếng Việt cực kỳ rõ ràng, bắt buộc dán nhãn `[DRAFT_ONLY]`. Nếu pin < 5%, LLM sinh JSON kích hoạt hàm `dispatch_mobile_charger`.
3. 🟢 **Human Step (HITL):** Dispatcher đọc bản nháp trên màn hình, click "Phê duyệt" (Approve) để gửi thẳng cho tài xế.
4. 🔄 **Handoff:** Hệ thống gửi SMS chỉ đường/hoặc lệnh cứu hộ đến App của tài xế.
5. ↩️ **Fallback (Kế hoạch dự phòng):** Nếu API của Gemini bị timeout hoặc trả về sai định dạng, UI sẽ tự động fallback về màn hình tra cứu thủ công cũ, yêu cầu Dispatcher tự gõ tin nhắn.

---

## 3.4. Evaluate & Quyết định cuối cùng

### AI Readiness Checklist:
- [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? (Có sẵn format log sự cố và data trạm sạc API).
- [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát? (Có, vì đã khóa chặt bằng HITL - Người duyệt cuối).
- [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? (Có, Dispatcher cực kỳ muốn giảm tải giờ cao điểm).

### Quyết định của Ban Giám Đốc:
`[x] GO (Bắt đầu xây dựng Prototype)`

**Justification (Lý giải quyết định):**
Dự án có tính khả thi kỹ thuật cực kỳ cao do bản chất bài toán là xử lý ngôn ngữ tự nhiên (NLU) ở mức độ hẹp. Việc kết hợp một System Prompt khắt khe (chỉ định định dạng đầu ra cứng) cùng cơ chế HITL giúp loại bỏ hoàn toàn rủi ro ảo giác (hallucination) của AI. Hơn nữa, chi phí token (API Cost) cho một lệnh phân tích text ngắn rẻ hơn rất nhiều so với chi phí vận hành (nhân sự, doanh thu thất thoát) do Dispatcher bị quá tải.
