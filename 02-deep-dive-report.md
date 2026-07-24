# 02-deep-dive-report.md — Báo Cáo Phân Tích Sâu (Deep-Dive & Evaluate - Bài nhóm)

**Tên nhóm:** Vin Smart Future — AI Product Scoping Group 06  
**Thành viên nhóm:**  
1. Nguyễn Anh Quân — 2A202601251: — *Role: AI Product Engineer  

---

# 🗳️ 1. Quyết định lựa chọn bài toán cho Deep-Dive

Nhóm thống nhất chọn bài toán từ **Quick Problem Card #1 — Xanh SM: Gợi ý trạm sạc trống & Phân bổ khung giờ sạc cho Tài xế Taxi Điện** để thực hiện Deep-Dive.

### Lý do lựa chọn:
* **Tác động vận hành trực tiếp (Real-time):** Sự cố hết pin giữa đường hoặc chờ sạc quá lâu tại các trạm sạc gây ảnh hưởng trực tiếp đến khả năng đón khách của tài xế Xanh SM, dẫn đến rò rỉ doanh thu thực tế hằng ngày.
* **Tính khả thi kỹ thuật:** Dữ liệu GPS xe, định vị trạm sạc VinFast và trạng thái trụ sạc đã có sẵn trên hệ thống của Vingroup, cho phép triển khai giải pháp AI Feature kết hợp Rule-based Router một cách hiệu quả.

---

# 🏗️ 2. Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping (Quy trình thủ công hiện tại)

Quy trình xử lý sự cố hết pin và tra cứu trạm sạc hiện tại của tài xế Xanh SM & điều phối viên:

```text
┌────────────────┐     ┌────────────────┐     ┌────────────────┐     ┌────────────────┐
│ Bước 1         │     │ Bước 2         │     │ Bước 3         │     │ Bước 4         │
│ Báo cạn pin    │     │ Tra cứu định   │     │ Tra cứu trạm   │     │ Soạn văn bản   │
│ trên App/gọi   │ ──> │ vị GPS xe      │ ──> │ sạc VinFast    │ ──> │ hướng dẫn      │
│                │     │                │     │ còn trụ trống  │     │ gửi tài xế     │
│ Ai: Driver     │     │ Ai: Dispatcher │     │ Ai: Dispatcher │     │ Ai: Dispatcher │
│ ⏱ 2 phút       │     │ ⏱ 3 phút       │     │ ⏱ 15 phút 🔴   │     │ ⏱ 5 phút 🔴    │
│ In: Tín hiệu   │     │ In: GPS data   │     │ In: Vị trí GPS │     │ In: Raw data   │
│ Out: Ticket    │     │ Out: Toạ độ    │     │ Out: Địa chỉ   │     │ Out: SMS draft │
└────────────────┘     └────────────────┘     └────────────────┘     └────────────────┘
                                                                       │
                                                                       ▼
                                                                ┌────────────────┐
                                                                │ Bước 5         │
                                                                │ Gọi xe cứu     │
                                                                │ hộ sạc (nếu cạn│
                                                                │ pin < 5%)      │
                                                                │ Ai: Dispatcher │
                                                                │ ⏱ 20 phút      │
                                                                └────────────────┘
```
* **🔴 Bottleneck:** Bước 3 (Tra cứu thủ công trạm sạc còn trụ trống) và Bước 4 (Soạn văn bản chỉ dẫn thủ công).
* **🔄 Handoff:** Điểm chuyển giao từ Tài xế (App) ──> Điều phối viên (Web Admin) ──> Đội cứu hộ sạc di động.
* **⏱ Tổng thời gian xử lý hiện tại:** **Tổng cộng = 25 – 45 phút/lượt**.

---

## 3.2. Problem Statement (6-field) & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Tài xế xe taxi điện GSM / Xanh SM và Điều phối viên tại Trung tâm Điều vận (Dispatch Center). |
| **2. Current Workflow** | Tài xế báo cạn pin ──> Điều phối viên tra vị trí GPS xe ──> Tra cứu thủ công các trạm sạc VinFast lân cận ──> Soạn tin hướng dẫn đường đi ──> Nếu cạn pin hoàn toàn thì liên hệ đội xe sạc di động. |
| **3. Bottleneck** | Bước tra cứu thủ công tình trạng trụ sạc trống và bước soạn tin nhắn chỉ dẫn tốn quá nhiều thời gian (15–20 phút/cuộc), gây quá tải cho điều phối viên vào giờ cao điểm. |
| **4. Business Impact** | Thất thoát từ 15% – 25% thời lượng hoạt động đón khách hằng ngày của tài xế; gia tăng tỉ lệ hủy chuyến và khiếu nại của tài xế về tình trạng xếp hàng chờ sạc. |
| **5. Success Metric** | **Metric chính:** Giảm tổng thời gian điều phối từ 45 phút xuống dưới 5 phút/lượt. **Metric phụ:** Đạt tỉ lệ tài xế sạc thành công tại trạm được gợi ý > 92%. |
| **6. Operational Boundary** | **AI Tuyệt đối KHÔNG:** Tự động gửi tin nhắn cho tài xế mà chưa qua duyệt của Dispatcher; KHÔNG được gợi ý trạm sạc xa > 5km khi pin xe < 5%. **Human-in-the-loop (HITL):** Dispatcher phải nhấn nút [Phê duyệt] trước khi tin nhắn được gửi đi. |

---

## 3.3. Future-State Flow & AI Fit

* **Phân loại AI Fit:** **`[x] LLM Feature (Drafting & Context Parser) + Rule-Based Safety Router`**

```text
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│ 🔵 AI Step 1     │     │ 🔵 AI Step 2     │     │ 🟢 Human (HITL)  │
│ Trích xuất GPS & │ ──> │ Dự báo trạm sạc  │ ──> │ Dispatcher review│
│ dung lượng pin   │     │ trống & soạn draft│     │ tin nhắn draft   │
│ từ thông điệp    │     │ tin nhắn chỉ dẫn │     │ và bấm [Gửi]     │
└──────────────────┘     └──────────────────┘     └──────────────────┘
                                 │                         │
                         (Nếu pin < 5%)              (Nếu LLM lỗi)
                                 ▼                         ▼
                         ┌──────────────────┐     ┌──────────────────┐
                         │ ↩️ Fallback 1    │     │ ↩️ Fallback 2    │
                         │ Tự động kích hoạt│     │ Chuyển sang quy  │
                         │ lệnh điều xe sạc │     │ trình gọi điện   │
                         │ pin di động      │     │ thủ công cũ      │
                         └──────────────────┘     └──────────────────┘
```

---

# 🏁 3. Phase 5 — EVALUATE: Đánh Giá Độ Sẵn Sàng & Quyết Định

### AI Readiness Checklist:
1. [x] **Dữ liệu mẫu/logs:** Hệ thống đã có sẵn log vị trí GPS xe GSM và API định vị trạm sạc VinFast real-time.
2. [x] **Tầm kiểm soát rủi ro:** Rủi ro sai sót được kiểm soát hoàn toàn thông qua cơ chế duyệt của Dispatcher (HITL) và Rule chặn khi pin < 5%.
3. [x] **Sự sẵn sàng của Stakeholders:** Khối Vận hành Xanh SM rất mong muốn giảm tải áp lực cho tổng đài điều vận vào giờ cao điểm.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
* [x] **GO (Bắt đầu xây dựng Prototype):** Triển khai xây dựng phiên bản thử nghiệm hẹp cho 50 xe taxi điện tại địa bàn Hà Nội.

**Justification (Lý giải quyết định):**
* **Chi phí triển khai:** Thấp nhờ sử dụng Gemini 2.5 Flash API cho tác vụ parser/drafting (chi phí ước tính < 0.002$/lượt).
* **Giá trị kinh tế mang lại:** Tiết kiệm trung bình 35 phút/lượt sạc cho tài xế, giúp mỗi xe tăng thêm 2–3 cuốc chạy mỗi ngày, mang lại hiệu quả doanh thu lớn cho Xanh SM.
