# 📄 02-deep-dive-report.md — Báo cáo Phase 3 & 5 (Deep-Dive & Evaluation)

---

## 👥 Thông tin nhóm

| Thông tin | Nội dung |
|-----------|----------|
| **Tên nhóm** | S206 |
| **Thành viên 1** | Họ và tên: Phạm Văn Thắng — MSSV: 2A202601359 |
---

## 🗳️ Quyết định lựa chọn bài toán Deep-Dive của nhóm

**Nhóm chọn bài toán:** _"Vin Smart Future Dispatcher Co-pilot — Tự động hóa xử lý sự cố pin tài xế Xanh SM"_

### Lý do lựa chọn:
- Bài toán có **tác động trực tiếp và ngay lập tức** đến doanh thu vận hành (mỗi sự cố pin = tài xế không đón được khách).
- **Bottleneck rõ ràng, đo được:** 15 phút/lượt xử lý thủ công, ~80 sự cố/ngày tại Hà Nội.
- **Giải pháp AI khả thi và an toàn** dưới dạng LLM Feature với Human-in-the-Loop.
- Nhóm đã xây dựng và kiểm thử thành công Prompt Prototype với Gemini API.

### Lý do loại bỏ các bài toán khác:
- **Vinhomes CSKH (#4):** Rủi ro pháp lý cao khi phân loại sai phiếu liên quan đến phí quản lý/tranh chấp căn hộ. Cần Rule-based router trước.
- **Vinmec Hồ sơ (#5):** Liên quan đến dữ liệu y tế nhạy cảm — cần tuân thủ quy định bảo mật y tế nghiêm ngặt trước khi triển khai AI.

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow Mapping

Quy trình xử lý sự cố hết pin thực địa hiện tại của Điều phối viên Xanh SM:

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ Tra cứu vị   │     │ Tra cứu trạm │     │ Soạn văn bản │
│ gọi sự cố    │ ──> │ trí GPS xe   │ ──> │ sạc VinFast  │ ──> │ hướng dẫn    │
│              │     │ trên bản đồ  │     │ còn trụ trống│     │ gửi tài xế   │
│              │     │              │     │              │     │              │
│ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │
│ ⏱ 2 phút     │     │ ⏱ 2 phút     │ 🔄  │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │
│ In: Điện thoại│    │ In: Biển số  │     │ In: GPS xe   │     │ In: Raw data │
│ Out: Log sự cố│    │ Out: Toạ độ  │     │ Out: Địa chỉ │     │ Out: SMS     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Gọi xe cứu   │
                                                               │ hộ (nếu pin  │
                                                               │ < 5%)        │
                                                               │ Ai: Dispatch │
                                                               │ ⏱ 1 phút     │
                                                               └──────────────┘
🔴 = Bottleneck (Bước chậm nhất)
🔄 = Handoff (Điểm chuyển giao dữ liệu giữa hệ thống)
⏱ Tổng thời gian xử lý thủ công: 15 phút/lượt
```

> **Ghi chú Bottleneck:** Bước 3 & 4 chiếm 10/15 phút — dispatcher phải mở 2-3 tab riêng biệt (bản đồ nội bộ, dashboard VinFast, messenger app) và gõ thủ công tin nhắn hướng dẫn tiếng Việt.

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM — thường xuyên xử lý đồng thời 15–30 sự cố trong giờ cao điểm. |
| **2. Current Workflow** | Khi tài xế báo hết pin, dispatcher tra cứu vị trí định vị trên bản đồ nội bộ, mở Dashboard trạm sạc VinFast để tìm trụ sạc trống gần nhất phù hợp với loại xe (VF5/VFe34/VF8), viết tay tin nhắn chỉ dẫn đường đi gửi qua App tài xế, và gọi điện cứu hộ nếu pin dưới 5%. 5 bước, hoàn toàn thủ công, mất 15 phút/lượt. |
| **3. Bottleneck** | Bước 3 & 4 (mất 10 phút/lượt): Tra cứu thủ công trụ sạc trống phù hợp với loại cổng sạc (CCS2/CHAdeMO) của từng dòng xe và soạn thảo tin nhắn hướng dẫn đường đi tiếng Việt thân thiện, chính xác. |
| **4. Business Impact** | Mỗi ngày có ~80 sự cố pin thực địa tại Hà Nội. Tổng lãng phí: **20 giờ làm việc/ngày** của team điều vận. Tài xế chờ đợi trung bình 15 phút = mất 1–2 cuốc/sự cố, rò rỉ doanh thu ước tính **~15%** trong giờ cao điểm. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý sự cố từ 15 phút xuống **dưới 3 phút** (giảm 80%). 2. Tỉ lệ hướng dẫn đúng địa điểm và đúng loại trụ sạc phù hợp đạt **≥ 98%** (Quality). |
| **6. Operational Boundary** | **AI được phép:** Truy xuất API định vị xe, API trạm sạc VinFast trống, tự động soạn thảo tin nhắn hướng dẫn dạng nháp `[DRAFT_ONLY]`. **TUYỆT ĐỐI CẤM:** Tự gửi tin nhắn khi chưa được Dispatcher phê duyệt (bắt buộc HITL); đề xuất trạm cách xa > 5km khi pin < 5%; đề xuất trạm không tương thích loại cổng sạc của xe. |

---

## 3.3. Future-State Flow & AI Fit

**AI Fit:** Chọn **LLM Feature** — không cần Agentic Loop vì quy trình có cấu trúc cố định và rủi ro khi AI điều phối sai trạm sạc (xe cạn pin giữa đường) đòi hỏi con người phê duyệt.

**Quy trình tương lai (Future-State Flow):**

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ 🔵 Auto-pull │     │ 🔵 AI draft  │     │ 🟢 Dispatch  │
│ gọi sự cố    │ ──> │ vị trí xe &  │ ──> │ SMS hướng    │ ──> │ click duyệt  │
│ (Manual)     │     │ trạm trống   │     │ dẫn [DRAFT]  │     │ & gửi tài xế │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                               ↩️ Fallback:
                                                               Nếu AI draft lỗi
                                                               hoặc < 5% pin,
                                                               Dispatcher tự xử lý
                                                               + dispatch mobile
                                                               charger thủ công.
```

**Giải thích:**
- 🔵 **AI Step:** Hệ thống tự động pull API lấy GPS xe + danh sách trạm trống → LLM soạn thảo SMS hướng dẫn tiếng Việt chuẩn.
- 🟢 **Human Step (HITL):** Dispatcher đọc bản nháp `[DRAFT_ONLY]`, click "Approve & Send" — không thể gửi tự động.
- ↩️ **Fallback:** Khi pin < 5% hoặc không có trạm trong bán kính 5km → AI tự động output JSON `{"action": "dispatch_mobile_charger"}` thay vì chỉ dẫn trạm sạc.

---

# 🏁 Phase 5 — EVALUATE

## AI Readiness Checklist:

| # | Câu hỏi | Đánh giá |
|---|---------|----------|
| 1 | Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? | ✅ Có — Logs sự cố pin của Xanh SM đã được ghi lại hệ thống |
| 2 | Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? | ✅ Có — HITL bắt buộc trước khi gửi; Fallback tự động dispatch mobile charger khi pin < 5% |
| 3 | Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? | ✅ Có — Team điều vận Xanh SM đang quá tải, chủ động mong muốn công cụ hỗ trợ |

## Quyết định cuối cùng: ✅ GO

**[x] GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

**Justification (Lý giải quyết định):**

> Dự án đạt mức **GO** vì:
>
> 1. **Bài toán cụ thể, đo được:** Bottleneck rõ ràng (15 phút → 3 phút), metric có số liệu thực tế (~80 sự cố/ngày).
> 2. **Giải pháp công nghệ đơn giản và hiệu quả:** LLM Feature — không cần xây dựng Agent phức tạp, tích hợp vào workflow hiện tại qua API.
> 3. **Ranh giới an toàn được kiểm soát chặt:** Đã kiểm thử thành công bằng Prompt Prototype trên Gemini — 2 adversarial test cases đều Passed:
>    - Rule 1 `[DRAFT_ONLY]`: Model giữ tag dù bị yêu cầu bỏ đi ✅
>    - Rule 2 Battery < 5%: Model từ chối trạm sạc xa, tự động dispatch mobile charger ✅
> 4. **ROI ước tính rõ ràng:** Tiết kiệm 20 giờ/ngày nhân công điều vận, tương đương ~1 FTE dispatcher có thể được redeploy cho tác vụ phức tạp hơn.
> 5. **Rủi ro thấp:** HITL bắt buộc + Fallback rule-based đảm bảo không có quyết định an toàn nào phụ thuộc 100% vào AI.
