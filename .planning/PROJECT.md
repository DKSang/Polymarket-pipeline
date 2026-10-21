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
- [ ] Ingest batch metadata Polymarket có tag AI hằng ngày vào Lakehouse; lưu snapshot và ánh xạ market/outcome/asset ID để cấp danh sách đăng ký cho collector WebSocket.
- [ ] Dùng Eventhouse/KQL cho phân tích live và báo cáo lịch sử theo phiên/ngày; Lakehouse là nơi lưu metadata discovery bắt buộc.
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
- Vault kiến thức data engineering nằm ở thư mục cha của repo (xem CLAUDE.md); đã tra index và các trang liên quan ở chế độ chỉ đọc trước khi research ngoài.
- Research đã được lưu ở `.planning/research/`; đã tra wiki chỉ đọc, tài liệu Polymarket/Microsoft Learn và mẫu API công khai. Chưa chạy WebSocket hay cloud job.
- Người dùng chọn Eventhouse/KQL cho lịch sử và bổ sung Lakehouse + batch metadata hằng ngày (2026-10-21). Không suy ra yêu cầu Power BI/Spark cho lịch sử từ việc có Lakehouse metadata.
- Capability `C:/Users/sanbo/.gsd/capabilities/dataeng` v0.1.0 đã kiểm tra bằng GSD CLI: installed/enabled/active; design, verify, planning/execution guidance và ship gate đều active. Có đủ bốn file agent `dataeng-*` trong `C:/Users/sanbo/.codex/agents`. Áp dụng DATA-SPEC/DATA-VERIFY ở đúng bước phase; trạng thái capability không phải bằng chứng các kiểm tra đã chạy.

## Constraints

- **Platform**: Microsoft Fabric bắt buộc — mục tiêu portfolio.
- **Budget**: Dùng Fabric trial; người dùng báo còn hơn 30 ngày (chưa xác minh hạn và mức capacity). Không giả định có ngân sách mua thêm.
- **Runtime**: Stream chạy thủ công theo phiên ngắn; metadata có batch hằng ngày. Giờ chạy batch, thời lượng phiên, và chính sách khi metadata cũ/lỗi còn mở.
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
| Eventhouse/KQL phục vụ cả live và lịch sử | Người dùng chọn phương án 1 sau research | — Pending |
| Lakehouse metadata + batch discovery hằng ngày trước đăng ký WebSocket | Người dùng yêu cầu danh sách market lấy từ metadata có tag AI | — Pending |

## Open Questions and Assumptions

- Discovery tag AI được làm mới bằng batch hằng ngày trong Lakehouse; tiêu chí chọn market AI-model từ tập ứng viên vẫn cần chốt. Không tự mở rộng sang IPO/hardware chỉ vì chung tag AI.
- Định nghĩa "cú sốc giá", cửa sổ thời gian, ngưỡng, xử lý market ít giao dịch chưa được chốt.
- Đã xác minh tài liệu về kênh market WebSocket và discovery; rate, lateness, delivery/reconnect thực tế chưa đo. WebSocket đăng ký bằng outcome asset ID, không phải Gamma market ID.
- Đã chọn Lakehouse cho metadata và Eventhouse/KQL cho analytics. Cơ chế chạy batch, cách collector đọc metadata và chi tiết Fabric transport còn cần thiết kế/kiểm chứng.
- Batch chỉ công bố version hoàn chỉnh sau khi phân trang/kiểm tra thành công; chính sách dùng snapshot cũ hoặc dừng khi batch thất bại chưa được người dùng chọn.
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
*Last updated: 2026-10-21 after user selected KQL history and required daily Lakehouse metadata discovery*
