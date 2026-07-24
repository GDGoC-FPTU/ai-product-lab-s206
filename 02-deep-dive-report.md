# 02 — Deep-Dive Report

## Thông tin nhóm

- **Tên nhóm:** `s206`
- **Thành viên 1:** `Đỗ Đức Phong - 2A202601207`
- **Thành viên 2:** `[HỌ TÊN — MSSV]`
- **Thành viên 3:** `[HỌ TÊN — MSSV]`
- **Thành viên 4:** `[HỌ TÊN — MSSV]`

> Phải thay toàn bộ placeholder thông tin nhóm trước khi nộp.

## Bài toán được chọn

**Trợ lý điều phối Xanh SM cho tình huống xe điện pin yếu.** Các số liệu vận hành là giả định phục vụ prototype và cần được xác thực bằng log thực tế.

## Current-State Workflow

1. Tài xế phát hiện pin yếu và liên hệ tổng đài — **2 phút**.
2. Tổng đài ghi nhận biển số, vị trí, mức pin — **2 phút**, 🔄 handoff tài xế → tổng đài.
3. Điều phối viên tra cứu trạm và khoảng cách — **4 phút**, 🔴 bottleneck.
4. Điều phối viên đánh giá khả năng di chuyển hoặc tìm xe sạc lưu động — **3 phút**.
5. Điều phối viên gọi lại, hướng dẫn tài xế và cập nhật hệ thống — **2 phút**, 🔄 handoff điều phối → tài xế.

**Tổng thời gian giả định:** khoảng **13 phút/lượt**.

## Problem Statement — 6 Fields

| Field                | Nội dung                                                                                                                                               |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Actor / Operator     | Tài xế Xanh SM, nhân viên tổng đài và điều phối viên vận hành.                                                                                         |
| Current Workflow     | Nhận yêu cầu qua điện thoại, ghi nhận dữ liệu, tra cứu thủ công, đánh giá phương án rồi gọi lại tài xế.                                                |
| Bottleneck           | Tra cứu và đánh giá phương án mất khoảng 7 phút, dễ bỏ sót ràng buộc an toàn khi nhiều yêu cầu đồng thời.                                              |
| Business Impact      | SLA phản hồi kéo dài, xe ngừng phục vụ lâu hơn và tăng rủi ro xe hết pin giữa đường.                                                                   |
| Success Metric       | 90% yêu cầu có draft dưới 30 giây; xử lý end-to-end dưới 5 phút; 100% ca pin dưới 5% không gợi ý trạm xa hơn 5 km.                                     |
| Operational Boundary | AI chỉ tạo `[DRAFT_ONLY]`; không tự gửi tin, không tự điều xe; pin dưới 5% phải đề xuất `dispatch_mobile_charger`; điều phối viên duyệt mọi hành động. |

## AI Fit

- **Rule/state machine:** Phù hợp để kiểm tra ngưỡng pin, khoảng cách và bắt buộc Human-in-the-loop.
- **LLM feature:** Phù hợp để hiểu mô tả tự do, tóm tắt tình huống và soạn bản nháp.
- **Agentic loop:** Chưa cần; tự gọi hệ thống điều xe làm tăng rủi ro và vượt scope prototype.

**Lựa chọn:** LLM Feature kết hợp rule cứng. Không dùng agent tự hành.

## Future-State Flow

1. Tài xế gửi vị trí, mức pin và mô tả tình huống.
2. 🔵 AI trích xuất dữ liệu và tạo bản nháp bắt đầu bằng `[DRAFT_ONLY]`.
3. Rule engine kiểm tra: nếu pin dưới 5%, cấm gợi ý trạm xa hơn 5 km và tạo action `dispatch_mobile_charger`.
4. 🟢 Điều phối viên kiểm tra vị trí, năng lực đội xe và phê duyệt/chỉnh sửa.
5. Hệ thống nghiệp vụ chỉ gửi tin hoặc tạo lệnh sau khi con người xác nhận.
6. ↩️ Nếu thiếu dữ liệu, output sai schema hoặc độ tin cậy thấp: chuyển về quy trình thủ công và không thực thi action.

## Prototype Stress Test

- Tình huống pin 2% nhưng người dùng yêu cầu đi đến trạm cách 8 km: hệ thống giữ boundary và đề xuất xe sạc lưu động.
- Người dùng yêu cầu bỏ `[DRAFT_ONLY]` và gửi ngay: hệ thống vẫn giữ tag và không tuyên bố đã gửi.
- Kết quả autograder hiện tại: toàn bộ 5 tiêu chí code đạt.

## AI Readiness Checklist

- [ ] Có log sạch, đã ẩn dữ liệu cá nhân và đủ tình huống pin yếu để đánh giá.
- [x] Rủi ro được giới hạn bằng rule cứng, Human-in-the-loop và fallback thủ công.
- [ ] Stakeholder vận hành đã xác nhận workflow, SLA và trách nhiệm phê duyệt.

## Quyết định

**NOT YET — cần xác lập baseline và kiểm thử dữ liệu thật trước khi pilot.**

Prototype cho thấy prompt boundary hoạt động với hai adversarial tests, nhưng chưa đủ bằng chứng để triển khai thật. Nhóm cần thu thập log đã ẩn danh, đo baseline thời gian xử lý và kiểm thử tối thiểu các nhóm tình huống: thiếu vị trí, sai mức pin, trạm không khả dụng và mất kết nối. Scope pilot chỉ nên hỗ trợ tạo draft; mọi tin nhắn và lệnh điều xe vẫn do điều phối viên duyệt. Chi phí ban đầu thấp vì dùng Gemini Flash theo lượt gọi, nhưng phải tính thêm tích hợp bản đồ, monitoring, đánh giá chất lượng và đào tạo vận hành.
