# Phase 1 — SCAN (Tìm kiếm cơ hội)

Dưới đây là 5 bài toán/bottleneck thực tế được quét qua các hoạt động vận hành của các công ty thành viên Vingroup.

### 📝 List bài toán của tôi:
| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công phản hồi khẩn cấp từ tài xế về sự cố sạc pin/hết pin thực địa (mất 15-20 min/lượt). |
| 2 | **Vinmec** | Pain từ người khác | Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện (Discharge Summary), dẫn đến quá tải và bệnh nhân phải chờ đợi. |
| 3 | **Vinhomes** | AI-upgrade | Hệ thống CSKH phân loại thủ công các phản hồi/khiếu nại của cư dân trên App Vinhomes Resident để chuyển cho ban quản lý tòa nhà, thường xuyên chậm trễ. |
| 4 | **VinFast** | Lặp lại | So khớp dữ liệu sạc điện hằng tuần từ hàng nghìn trụ sạc đối tác bên ngoài với hóa đơn thực tế. |
| 5 | **Xanh SM** | Pain từ người khác | Tự động nghe ghi âm cuộc gọi hủy chuyến và ghi chú của tài xế để phân loại 10 lý do phổ biến nhất gây rò rỉ cuốc xe. |

---

# Phase 2 — QUICK-ASSESS (Đánh giá nhanh)

Lựa chọn top 3 bài toán từ danh sách trên để phân tích sâu thành các thẻ bài toán (Problem Cards).

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tài xế Xanh SM báo cáo sự cố hết pin giữa │
│ đường cần điều phối cứu hộ hoặc tìm trạm sạc gần nhất.      │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Tài xế (chờ đợi), Điều phối viên.      │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Nhận điện thoại ──> 2. Tra cứu định vị GPS ──>         │
│   3. Tra cứu trạm sạc trống ──> 4. Soạn SMS chỉ đường ──>   │
│   5. Gọi cứu hộ (nếu cạn pin).                              │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4 (Tự động   │
│ lấy tọa độ, tra trạm trống và draft sẵn tin nhắn SMS).      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút.      │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Bác sĩ cần tổng hợp hàng loạt thông tin   │
│ bệnh án rải rác để soạn bản Tóm tắt xuất viện cho bệnh nhân.│
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ điều trị.                       │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Mở hồ sơ bệnh án ──> 2. Đọc notes & xét nghiệm ──>     │
│   3. Đánh máy tóm tắt lâm sàng ──> 4. Trưởng khoa duyệt.    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 25 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 (Đọc data  │
│ thô từ EMR và draft sẵn bản tóm tắt lâm sàng).              │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian soạn tóm tắt từ 30 phút ──> dưới 5 phút.     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Phân loại tự động hàng trăm ticket khiếu  │
│ nại hằng ngày của cư dân gửi qua App Vinhomes.              │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH (quá tải).              │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Nhận ticket trên hệ thống ──> 2. Đọc nội dung ──>      │
│   3. Xác định mảng (kỹ thuật, vệ sinh...) ──> 4. Chuyển ban.│
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 5 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 (Phân tích │
│ ngữ nghĩa nội dung và gắn tag tự động để route ticket).     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ 85% ticket khiếu nại được phân loại tự động dưới 10 giây.   │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘