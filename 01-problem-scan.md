# 01-problem-scan.md — Phase 1 & 2: AI Problem Scan & Quick Assessment

---

## 🔍 Phase 1 — SCAN (Bảng Quét Cơ Hội AI)

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | VinFast | Lặp lại / Pain từ người khác | Đối soát & xác thực hồ sơ bảo hành, thay pin dịch vụ cho xe điện |
| 2 | Xanh SM | Tốn thời gian / Pain từ người khác | Tra cứu log & xử lý khiếu nại cước phí, định vị chuyến đi của tài xế/khách hàng |
| 3 | Vinhomes | Tốn thời gian / AI-upgrade | Phân loại, điều phối & soạn phản hồi phản ánh cư dân trên Vinhomes Resident App |
| 4 | Vinmec | Tốn thời gian / AI-upgrade | Tóm tắt tiền sử bệnh án & tổng hợp đề xuất điều trị từ hồ sơ y tế đa khoa |
| 5 | Vinpearl | Lặp lại / Pain từ người khác | Dự báo & điều phối ca làm việc động cho nhân viên buồng phòng và vận hành trò chơi |

---

## 🃏 Phase 2 — QUICK-ASSESS (3 Quick Problem Cards)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #01                                                  │
│                                                                         │
│ Bài toán (1 câu): Tra cứu log & xử lý khiếu nại cước phí/định vị        │
│                   chuyến đi tự động cho tài xế và khách hàng.           │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes              │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________              │
│                                                                         │
│ Ai đang đau (Actor)? Chuyên viên CSKH Xanh SM & Tài xế / Khách hàng     │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Tiếp nhận ticket khiếu nại cước/lộ trình từ app                   │
│   ──> 2. Mở 3 hệ thống (App CSKH, Hệ thống GPS, Cổng thanh toán)         │
│   ──> 3. Tra cứu log chuyến đi & vẽ lại đường đi thủ công               │
│   ──> 4. Tính toán cước chênh lệch bằng bảng tính Excel                 │
│   ──> 5. Khởi tạo lệnh hoàn tiền & soạn email/tin nhắn phản hồi         │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (Tra cứu log 3 hệ thống     │
│ & đối soát cước thủ công) (⏱ 12 phút/lượt)                              │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4 (LLM đọc log chuyến đi,│
│ tự động phân tích điểm bất thường GPS, tính cước chuẩn xác & đề xuất)   │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   "Giảm thời gian xử lý 1 ticket từ 12 phút ──> dưới 2 phút"             │
│   "Tăng tỷ lệ xử lý khiếu nại đóng trong ngày từ 70% ──> 98%"           │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent             │
└─────────────────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #02                                                  │
│                                                                         │
│ Bài toán (1 câu): Phân loại tự động, gán nhãn ưu tiên & soạn thảo       │
│                   phản hồi phản ánh cư dân trên Vinhomes Resident App.  │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes              │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________              │
│                                                                         │
│ Ai đang đau (Actor)? Cán bộ Ban Quản Lý (BQL) đô thị & Cư dân Vinhomes   │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Tiếp nhận văn bản/hình ảnh phản ánh từ cư dân trên App             │
│   ──> 2. Cán bộ BQL đọc phản ánh & gán nhãn thủ công bộ phận xử lý       │
│   ──> 3. Chuyển tiếp yêu cầu đến đội ngũ thực địa (Kỹ thuật/Vệ sinh)     │
│   ──> 4. Soạn thảo phản hồi tiến độ xử lý cho cư dân                    │
│   ──> 5. Đóng ticket và ghi nhận đánh giá của cư dân                    │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 4 (Đọc phân loại văn bản      │
│ & soạn phản hồi cá nhân hóa cho cư dân) (⏱ 15 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 4 (AI phân tích ý định,  │
│ tự động gán nhãn bộ phận, độ ưu tiên & gợi ý dự thảo câu trả lời)       │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   "Giảm thời gian phản hồi ban đầu cho cư dân từ 4 giờ ──> dưới 15 phút" │
│   "Tăng chỉ số hài lòng cư dân (CSAT) từ 82% ──> trên 95%"              │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent             │
└─────────────────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #03                                                  │
│                                                                         │
│ Bài toán (1 câu): Đối soát & xác thực tự động hồ sơ bảo hành / thay pin  │
│                   dịch vụ cho xe điện VinFast.                          │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes              │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________              │
│                                                                         │
│ Ai đang đau (Actor)? Kỹ thuật viên (KTV) xưởng dịch vụ & Chuyên viên    │
│                      duyệt bảo hành VinFast                             │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. KTV chụp ảnh linh kiện/mã lỗi (DTC code) & nhập biên bản           │
│   ──> 2. Gửi hồ sơ về bộ phận kiểm duyệt trung ương VinFast             │
│   ──> 3. Chuyên viên trung ương đọc ảnh, kiểm tra điều kiện bảo hành     │
│   ──> 4. Ra quyết định Phê duyệt / Từ chối claim                        │
│   ──> 5. Xuất lệnh xuất kho linh kiện/pin thay thế                      │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (Chuyên viên đọc ảnh mã lỗi,    │
│ đối chiếu chính sách bảo hành thủ công) (⏱ 20 phút/hồ sơ)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 (Multimodal AI phân tích   │
│ ảnh mã lỗi, tự đối chiếu chính sách & đưa đề xuất duyệt + độ tin cậy)   │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   "Giảm thời gian duyệt claim bảo hành từ 3-5 ngày ──> dưới 2 giờ"       │
│   "Giảm tỷ lệ gian lận/sai sót bảo hành từ 6% ──> dưới 0.5%"            │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [x] Agent             │
└─────────────────────────────────────────────────────────────────────────┘
```
