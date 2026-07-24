# 📄 01-problem-scan.md — Báo cáo Phase 1 & 2 (Scan & 3 Quick Cards)

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội AI (Cá nhân)

## 4 Lenses tìm bài toán:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày.
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công.
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ hiện tại còn chậm, phản hồi rập khuôn.
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng/nhân viên phàn nàn.

## 📝 Bảng quét cơ hội AI — Vin Smart Future:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công phản hồi sự cố sạc pin của tài xế qua điện thoại — mỗi lượt mất 12–15 phút tra cứu vị trí, tìm trạm sạc, soạn tin nhắn hướng dẫn. |
| 2 | **Xanh SM** | Lặp lại | So khớp và phân bổ lại cuốc xe khi khách hàng yêu cầu thay đổi điểm đến giữa chừng — điều phối viên phải tính toán lại thủ công mỗi lần. |
| 3 | **VinFast** | Lặp lại | So khớp hóa đơn sạc điện và đối chiếu số liệu trạm sạc đối tác hằng tuần — kế toán mất 2 ngày/tuần xử lý thủ công. |
| 4 | **Vinhomes** | AI-upgrade | Phân loại và route tự động các phản hồi/khiếu nại của cư dân trên App Vinhomes Resident — CSKH phản hồi rập khuôn, mất trung bình 12 tiếng/phiếu. |
| 5 | **Vinmec** | Stakeholder Pain | Bác sĩ mất 20–30 phút/bệnh nhân để viết tóm tắt hồ sơ xuất viện thủ công — gây quá tải và giảm thời gian khám trực tiếp. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn **top 3 bài toán** từ danh sách SCAN: **#1 (Xanh SM - Sự cố pin), #4 (Vinhomes - CSKH), #5 (Vinmec - Hồ sơ).**

---

## Quick Problem Card #1 — Xanh SM: Xử lý sự cố sạc pin thực địa

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM báo hết pin giữa đường, cần điều  │
│ phối cứu hộ hoặc tìm trạm sạc VinFast gần nhất.            │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   Điều phối viên (Dispatcher) — quá tải; Tài xế — chờ đợi  │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế gọi tổng đài điều vận báo hết pin              │
│   → 2. Dispatcher tra cứu vị trí GPS xe trên bản đồ nội bộ  │
│   → 3. Mở Dashboard tìm trạm sạc VinFast trống gần nhất    │
│   → 4. Soạn tin nhắn hướng dẫn đường đi gửi qua App tài xế │
│   → 5. Gọi xe cứu hộ nếu pin dưới 5%                       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                           │
│   Bước 3 & 4 (⏱ 10 phút/lượt) — Tra cứu thủ công và soạn  │
│   tin nhắn tiếng Việt thân thiện, dễ sai thông tin.        │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 3-4: Auto-pull vị trí & trạm trống → AI draft SMS   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút.  │
│   Tỉ lệ chỉ dẫn đúng trạm/loại cổng sạc đạt >= 98%.       │
│                                                             │
│ Quick Architecture: [x] LLM Feature                        │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Problem Card #2 — Vinhomes: CSKH tự động phân loại khiếu nại cư dân

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Tự động phân loại và route phiếu khiếu nại/yêu   │
│ cầu của cư dân Vinhomes đến đúng bộ phận xử lý.            │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   Nhân viên CSKH Vinhomes; Cư dân chờ phản hồi quá lâu     │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi phiếu qua App Vinhomes Resident            │
│   → 2. CSKH đọc nội dung phiếu thủ công                    │
│   → 3. Phân loại loại vấn đề (kỹ thuật/phí/an ninh/...)   │
│   → 4. Chuyển route đến bộ phận phụ trách                  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                           │
│   Bước 2-3 (⏱ 10-12 tiếng/phiếu) — Đọc thủ công và phân   │
│   loại sai category dẫn đến chuyển nhầm bộ phận.           │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 2-3: LLM phân loại tự động & gợi ý route            │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   85% phiếu được phân loại đúng và route trong vòng 10 giây │
│   (giảm từ 12 tiếng xuống dưới 10 giây).                   │
│                                                             │
│ Quick Architecture: [x] LLM Feature                        │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Problem Card #3 — Vinmec: Tóm tắt hồ sơ xuất viện bằng AI

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Tự động tóm tắt hồ sơ bệnh án và tạo bản tóm tắt │
│ xuất viện cho bác sĩ tại Vinmec.                            │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   Bác sĩ lâm sàng — quá tải; Bệnh nhân — chờ xuất viện lâu │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ mở hồ sơ bệnh án điện tử (HIS)                 │
│   → 2. Đọc toàn bộ lịch sử điều trị, xét nghiệm, đơn thuốc │
│   → 3. Viết tóm tắt xuất viện bằng tay (1-2 trang)         │
│   → 4. Ký và phát hành hồ sơ cho bệnh nhân                 │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                           │
│   Bước 2-3 (⏱ 20-30 phút/bệnh nhân) — Đọc và tổng hợp     │
│   thông tin từ nhiều nguồn dữ liệu rải rác.                 │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 2-3: LLM tổng hợp tự động bản nháp tóm tắt          │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm thời gian soạn tóm tắt từ 25 phút xuống dưới 5 phút │
│   Bác sĩ chỉ cần review & ký — tiết kiệm 80% thời gian.    │
│                                                             │
│ Quick Architecture: [x] LLM Feature (HITL bắt buộc)        │
└─────────────────────────────────────────────────────────────┘
```
