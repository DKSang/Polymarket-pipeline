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
- [ ] Trả lời câu hỏi **Top movers**: xếp hạng outcome theo độ lớn thay đổi midpoint trong các cửa sổ 5/15/60 phút, hiển thị mức thay đổi theo điểm phần trăm và hướng tăng/giảm. Cửa sổ chưa đủ lịch sử hiển thị "chưa đủ dữ liệu".
- [ ] Trả lời câu hỏi **Sôi động hay không**: tần suất giao dịch, khối lượng và spread theo thời gian, phân biệt market đang được giao dịch thật với market chỉ có quote.
- [ ] Trả lời câu hỏi **Cú sốc giá**: phát hiện midpoint tăng hoặc giảm ít nhất 5 điểm phần trăm trong 5 phút, cho phép chỉnh ngưỡng/cửa sổ bằng cấu hình; cho xem spread/giao dịch quan sát được quanh sự kiện. Chỉ tính khi có dữ liệu hợp lệ và đủ lịch sử.
- [ ] Xác định tập market AI (về các model) cần theo dõi và cách cập nhật tập này; tiêu chí chọn cần chốt ở research/requirements.
- [ ] Ingest batch metadata Polymarket có tag AI vào Lakehouse lúc 07:15 hằng ngày, múi giờ Asia/Saigon (UTC+7), xong trước 08:15 giờ tuyệt đối; lưu snapshot và ánh xạ market/outcome/asset ID để cấp danh sách đăng ký cho collector WebSocket.
- [ ] Nếu batch metadata thất bại, dùng phiên bản thành công gần nhất, cảnh báo rõ thời điểm cập nhật và kiểm tra lại market trước khi đăng ký WebSocket.
- [ ] Dùng Eventhouse/KQL cho phân tích live và báo cáo lịch sử theo phiên/ngày; Lakehouse là nơi lưu metadata discovery bắt buộc.
- [ ] Dashboard live và báo cáo lịch sử theo ngày dựng từ dữ liệu đã thu trong các phiên.
- [ ] Dashboard phản ánh dữ liệu mới trong khoảng mục tiêu 15–30 giây từ lúc collector nhận dữ liệu; dùng 30 giây làm ngưỡng kiểm chứng, chưa có kết quả đo thực tế.
- [ ] Chạy theo phiên thủ công, hữu hạn, ngắn; xử lý mất kết nối và cho thấy khoảng trống dữ liệu thay vì che đi.
- [ ] Người chạy truyền thời lượng hữu hạn khi bắt đầu mỗi phiên stream; collector tự dừng nhận khi hết thời lượng và hoàn tất quy trình đóng phiên có kiểm soát.
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
- **Runtime**: Stream chạy thủ công, người chạy truyền thời lượng hữu hạn cho từng phiên; collector tự dừng nhận khi hết thời lượng rồi đóng phiên có kiểm soát. Metadata batch bắt đầu lúc 07:15 hằng ngày theo Asia/Saigon (UTC+7), tương đương 00:15 UTC, và mục tiêu xong trước 08:15 giờ tuyệt đối (mục tiêu, chưa là cam kết cho tới khi đo). Batch lỗi thì dùng bản thành công gần nhất, cảnh báo thời điểm cập nhật và kiểm tra lại market trước đăng ký; lịch cloud chưa được triển khai.
- **Level**: Độ phức tạp phải hợp với trình độ fresher — ưu tiên rõ ràng, kiểm chứng được hơn là phức tạp.
- **Live freshness**: Người dùng chọn 15–30 giây từ collector receive đến dữ liệu được hiển thị trên dashboard; 30 giây là cận trên mục tiêu. Đo cả ingestion, query và refresh, không chỉ thời gian gửi vào Eventstream. Đạt khi ≥95% mẫu ≤30 giây (người dùng chọn có chủ ý), báo cáo p50/p95/max. Cách đánh giá lúc lỗi/recovery chưa chốt.
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
| Batch metadata bắt đầu lúc 07:15 hằng ngày, Asia/Saigon (UTC+7), xong trước 08:15 tuyệt đối | Người dùng chọn 07:00 rồi đổi sang 07:15 ngày 2026-10-21 khi chốt SLO Phase 1 | — Pending |
| SLO live: đạt khi ≥95% mẫu ≤30 giây; metadata fallback không đặt tuổi tối đa, giảm thiểu bằng cảnh báo lỗi batch (META-07); R-01, R-02 do người dùng chấp nhận | Người dùng chọn có chủ ý ngày 2026-10-21 (Phase 1 discuss và plan:pre); cách tính lúc lỗi/recovery vẫn chưa chốt | — Pending |
| Dashboard cập nhật trong 15–30 giây từ collector receive | Người dùng chọn sau khi được hỏi mục tiêu độ trễ; cần đo kiểm end-to-end | — Pending |
| Thời lượng stream được truyền khi chạy từng phiên | Người dùng chọn phương án 3; không cố định 30 hay 90 phút | — Pending |
| Top movers dùng cửa sổ 5/15/60 phút; thiếu lịch sử hiển thị chưa đủ dữ liệu | Người dùng đồng ý cả ba cửa sổ và hành vi khi phiên ngắn | — Pending |
| Cú sốc: midpoint đổi ít nhất 5 điểm phần trăm trong 5 phút; cho phép cấu hình | Người dùng đồng ý quy tắc khởi đầu; đây là định nghĩa portfolio, chưa phải ngưỡng thống kê đã kiểm chứng | — Pending |
| Batch metadata lỗi: dùng bản thành công gần nhất, cảnh báo thời điểm cập nhật, kiểm tra lại market trước subscription | Người dùng chọn tiếp tục phiên với fallback có kiểm tra | — Pending |
| Roadmap theo quy trình triển khai 7 giai đoạn, Build tách đôi thành 8 phase | Người dùng chọn chia theo tài liệu quy trình ngày 2026-10-21, thay thế lựa chọn vertical MVP trước đó; thêm 10 yêu cầu tối thiểu cho các giai đoạn còn trống | — Pending |

## Open Questions and Assumptions

- Discovery tag AI được làm mới bằng batch hằng ngày trong Lakehouse; tiêu chí chọn market AI-model từ tập ứng viên vẫn cần chốt. Không tự mở rộng sang IPO/hardware chỉ vì chung tag AI.
- Đã chốt top movers 5/15/60 phút và shock midpoint >=5 điểm phần trăm trong 5 phút (cả tăng/giảm), cấu hình được. Không coi ngưỡng này là bằng chứng bất thường thống kê. Chi tiết xử lý quote cũ, gap, market ít giao dịch và gộp các lần vượt ngưỡng còn cần thiết kế.
- Đã xác minh tài liệu về kênh market WebSocket và discovery; rate, lateness, delivery/reconnect thực tế chưa đo. WebSocket đăng ký bằng outcome asset ID, không phải Gamma market ID.
- Đã chọn Lakehouse cho metadata và Eventhouse/KQL cho analytics. Cơ chế chạy batch, cách collector đọc metadata và chi tiết Fabric transport còn cần thiết kế/kiểm chứng.
- Batch chỉ công bố version hoàn chỉnh sau khi phân trang/kiểm tra thành công. Người dùng chọn dùng bản thành công gần nhất khi batch lỗi, cảnh báo rõ thời điểm cập nhật và kiểm tra lại market trước đăng ký. Không có snapshot thành công hoặc không kiểm tra được market thì không thể coi subscription là hợp lệ; hành vi lỗi cụ thể cần thiết kế trong phase. Không đặt tuổi tối đa cho snapshot fallback (lựa chọn có chủ ý của người dùng, 2026-10-21); rủi ro được giảm thiểu bằng cảnh báo lỗi batch (META-07).
- Mức tiêu thụ capacity của trial chưa đo; cần đo để hướng dẫn chọn thời lượng an toàn. Người chạy chủ động truyền thời lượng từng phiên; giới hạn kỹ thuật và thời gian drain khi đóng phiên còn cần thiết kế.
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
*Last updated: 2026-10-21 after user chose to split the roadmap by the 7-stage delivery process (8 phases)*
