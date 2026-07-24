# 01-problem-scan.md — Problem Scan & Quick Cards (Bài cá nhân)

**Họ và tên:** Nguyễn Anh Quân  
**MSSV:** (Cập nhật MSSV tại đây)  

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Tài xế taxi điện phải tự tìm trạm sạc trống, xếp hàng chờ (đặc biệt giờ cao điểm/ban đêm) và xử lý thủ công tranh chấp ưu tiên sạc với khách hàng cá nhân tại cùng trạm, gây thất thoát giờ chạy xe. |
| 2 | **VinFast** | Lặp lại | Đối soát hóa đơn tiền điện và sản lượng tiêu thụ hằng tuần/tháng giữa hệ thống trạm sạc đối tác (Vincom, bên thứ ba) với dữ liệu vận hành GSM/Xanh SM, hiện làm thủ công qua Excel. |
| 3 | **Vinhomes** | AI-upgrade | Ban quản lý tòa nhà tiếp nhận lượng lớn phản ánh/khiếu nại từ cư dân qua App Vinhomes Resident (sự cố tiện ích, tranh chấp thẻ ra/vào, lỗi hệ thống...) nhưng phân loại và định tuyến đến đúng bộ phận xử lý vẫn phần lớn thủ công. |
| 4 | **Vinmec** | Stakeholder Pain | Trước khi có công cụ hỗ trợ, bác sĩ mất nhiều thời gian tổng hợp hồ sơ bệnh án, kết quả xét nghiệm cho hội chẩn đa chuyên khoa và làm thủ tục xuất viện — Vinmec đã thí điểm AI (DrAid™/Vinmec Copilot) ở một số khoa nhưng chưa phủ toàn hệ thống. |
| 5 | **Vinpearl** | Tốn thời gian | Đội ngũ vận hành phải tự đọc và tổng hợp đánh giá khách hàng rải rác trên nhiều nền tảng (Booking, Agoda, Google Reviews) để phát hiện lỗi dịch vụ lặp lại theo từng cơ sở, thay vì có báo cáo tự động tổng hợp. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tài xế taxi điện mất thời gian tự tìm     │
│ trạm sạc trống, xếp hàng chờ và tranh chấp chỗ sạc.         │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế GSM (thất thoát giờ chạy xe),   │
│                      Điều phối viên trạm sạc (quá tải).     │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Báo cạn pin ──> 2. Tìm trạm sạc ──> 3. Xếp hàng chờ    │
│   ──> 4. Xử lý tranh chấp sạc                               │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 45 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3            │
│ (Dự báo trụ trống real-time, gợi ý trạm sạc & giữ chỗ)      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   VD: "Giảm thời gian tìm & chờ sạc từ 45 min ──> under 10 min"│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Tự động phân loại và định tuyến phản ánh │
│ cư dân trên App Vinhomes Resident đến đúng bộ phận.         │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Cư dân (chờ lâu),                      │
│                      Ban quản lý / CSKH (quá tải phân loại).│
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Gửi phản ánh ──> 2. Đọc & phân loại ──> 3. Chuyển ticket│
│   ──> 4. Xử lý & trả lời                                    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 120 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3            │
│ (Trích xuất ý định, phân loại sự cố & tự động route ticket) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   VD: "Giảm thời gian định tuyến từ 120 min ──> under 5 min"│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Trợ lý AI tự động đối soát hóa đơn điện & │
│ sản lượng trạm sạc đối tác với dữ liệu GSM qua Excel.       │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Chuyên viên đối soát VinFast & GSM     │
│                      (quá tải, dễ sai lệch dữ liệu).        │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhận báo cáo điện ──> 2. Đọc file Excel/PDF            │
│   ──> 3. Đối chiếu log GSM ──> 4. Lập báo cáo chênh lệch    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 300 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3            │
│ (Trích xuất bảng dữ liệu tự động -> So sánh & cảnh báo lỗi) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   VD: "Giảm thời gian đối soát từ 300 min ──> under 15 min" │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```
