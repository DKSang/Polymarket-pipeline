# Polymarket AI Markets Stream Pipeline

## What This Is

Một project portfolio data engineering trên Microsoft Fabric: pipeline end-to-end ở mức fresher, nhận dữ liệu stream thật từ Polymarket cho các market thuộc chủ đề AI (các model), biến đổi qua các lớp dữ liệu và phục vụ qua dashboard live cùng báo cáo lịch sử. Người xem chính là nhà tuyển dụng/interviewer đánh giá năng lực xây pipeline hoàn chỉnh; người dùng giả định của sản phẩm dữ liệu là người theo dõi thị trường dự đoán về AI. Cả hai chỉ là giả thuyết portfolio, chưa có phỏng vấn hay đo lường thật.

## Core Value

Cho thấy một pipeline stream thật chạy được từ nguồn đến dashboard trên Fabric, đủ rõ để người xem kiểm chứng từng bước (ingest → lưu → biến đổi → phục vụ) và tin vào số liệu hiển thị.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Nhận dữ liệu stream thật từ Polymarket (kênh streaming của nguồn; cơ chế cụ thể cần xác minh khi research) cho các market AI về model.
- [ ] Trả lời câu hỏi **Top movers**: trong các cửa sổ thời gian ngắn (ứng viên 5/15/60 phút), market/outcome nào đổi xác suất nhiều nhất và theo hướng nào.
- [ ] Trả lời câu hỏi **Sôi động hay không**: tần suất giao dịch, khối lượng và spread theo thời gian, phân biệt market đang được giao dịch thật với market chỉ có quote.
- [ ] Trả lời câu hỏi **Cú sốc giá**: phát hiện bước nhảy giá bất thường và cho xem spread/giao dịch phản ứng ra sao sau đó. Định nghĩa "cú sốc" (ngưỡng, cửa sổ) cần chốt ở requirements.
- [ ] Xác định tập market AI (về các model) cần theo dõi và cách cập nhật tập này; tiêu chí chọn cần chốt ở research/requirements.
- [ ] Dashboard live và báo cáo lịch sử theo ngày dựng từ dữ liệu đã thu trong các phiên.
- [ ] Chạy theo phiên thủ công, hữu hạn, ngắn; xử lý mất kết nối và cho thấy khoảng trống dữ liệu thay vì che đi.
- [ ] Có bằng chứng portfolio tái lập được: code, tài liệu thiết kế, định nghĩa KPI, hướng dẫn chạy, kết quả kiểm thử và demo.

### Out of Scope

- Đặt lệnh, giao dịch, khuyến nghị đầu tư — project mô tả dữ liệu quan sát, không đưa lời khuyên tài chính.
- Thu thập 24/7 liên tục — người dùng chọn chạy thủ công theo phiên ngắn ở giai đoạn này.
- Backfill lịch sử trước khi bắt đầu thu — lịch sử chỉ tích lũy từ các phiên thực tế (giả định hiện tại, xem lại ở requirements).
- Mọi market ngoài chủ đề AI về model — phạm vi đã chọn.
- Giải thích nhân quả từ tin tức — project chỉ mô tả biến động quan sát được.

## Context

- Đây là lần khởi tạo lại từ đầu. Một bộ planning cũ (PROJECT, 36 yêu cầu v1, roadmap 7 phase) từng tồn tại trong lịch sử git (commit `c56af41`) rồi bị xóa trong `712fb47`. Người dùng chọn làm lại từ đầu, nên bộ cũ **không** là baseline đã duyệt; chỉ coi là tham khảo nếu người dùng yêu cầu.
- Người dùng chưa có sẵn câu hỏi phân tích; ba câu hỏi trên được đề xuất và chọn trong phiên questioning ngày 2026-10-21.
- Fabric MCP (chỉ đọc) và Microsoft Learn MCP có trong môi trường làm việc; chưa kiểm tra đăng nhập/quyền thực tế. Chưa có thao tác nào trên cloud.
- Vault kiến thức data engineering nằm ở thư mục cha của repo (xem CLAUDE.md); sẽ tra cứu chế độ chỉ đọc trước khi research ngoài. Chưa tra cứu ở bước này.

## Constraints

- **Platform**: Microsoft Fabric bắt buộc — mục tiêu portfolio.
- **Budget**: Dùng Fabric trial; người dùng báo còn hơn 30 ngày (chưa xác minh hạn và mức capacity). Không giả định có ngân sách mua thêm.
- **Runtime**: Chạy thủ công theo phiên ngắn; thời lượng phiên và lịch cụ thể còn mở.
- **Level**: Độ phức tạp phải hợp với trình độ fresher — ưu tiên rõ ràng, kiểm chứng được hơn là phức tạp.
- **Authority**: Kiểm tra tài nguyên chỉ đọc không đồng nghĩa được tạo, triển khai hay chạy job trên cloud; các thao tác đó cần duyệt riêng.

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Nguồn là stream thật từ Polymarket | Người dùng muốn thể hiện xử lý dữ liệu stream thực sự | — Pending |
| Ba câu hỏi: top movers, sôi động, cú sốc giá | Người dùng chọn từ các đề xuất; chưa có câu hỏi riêng | — Pending |
| Chủ đề AI (các model) | Người dùng chọn phạm vi chủ đề | — Pending |
| Đầu ra: dashboard live + báo cáo lịch sử | Người dùng chọn | — Pending |
| Chạy thủ công theo phiên ngắn | Người dùng chọn cho giai đoạn hiện tại | — Pending |
| Làm lại từ đầu, không dùng bản planning cũ làm baseline | Người dùng chọn khi được hỏi | — Pending |

## Open Questions and Assumptions

- Tiêu chí "market AI về model" và cách cập nhật tập theo dõi chưa được chốt.
- Định nghĩa "cú sốc giá", cửa sổ thời gian, ngưỡng, xử lý market ít giao dịch chưa được chốt.
- Khả năng và giới hạn của nguồn (kênh stream, tốc độ, quy tắc dữ liệu) chưa được xác minh bằng tài liệu hiện hành.
- Công nghệ Fabric cụ thể cho từng lớp (ingest, lưu, xử lý, phục vụ) chưa chọn; sẽ so sánh ở research.
- Mức tiêu thụ capacity của trial chưa đo; thời lượng phiên phụ thuộc kết quả đo.
- Không có tuyên bố nào về hiệu quả với người dùng thật hay độ trễ cho tới khi có đo đạc.

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-10-21 after initialization*
