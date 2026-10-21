# Requirements: Polymarket AI Markets Stream Pipeline

**Defined:** 2026-10-21
**Status:** Draft — remapped to the 7-stage delivery process on 2026-10-21; awaiting user review before commit.
**Core Value:** Cho thấy pipeline stream thật từ Polymarket đến dashboard trên Fabric, kiểm chứng được từng bước và số liệu.

Cấu trúc theo quy trình triển khai Inception → Design → Build → Operationalize → Publish → Run & govern → Scale & retire (tài liệu `Quy trình triển khai Data Engineering Platform theo Databricks (trung lập công nghệ).docx`). Quy trình đó là phần tổng hợp của tác giả tài liệu, không phải quy trình nguyên văn của nhà cung cấp; chỉ phần nhỏ phù hợp portfolio được dùng.

## v1 Requirements

### Inception

- [ ] **INCP-01**: Data product có owner được ghi tên, kèm người bảo trì và đầu mối escalate, lưu trong repo.
- [ ] **INCP-02**: KPI và SLO được ghi bằng con số kèm cách đo: tỷ lệ đạt và điểm đo của mục tiêu 15–30 giây, hạn hoàn tất batch 07:00, tuổi tối đa của metadata fallback, cách đánh giá lúc lỗi/recovery. Mục chưa được người dùng chốt ghi rõ là chưa chốt, không tự điền.

### Design

- [ ] **DSGN-01**: Source inventory cho metadata API và WebSocket: ngữ nghĩa thay đổi, khóa, thứ tự, retention, hành vi lỗi/phân trang, dựa trên mẫu đọc có giới hạn; phân biệt rõ điều đã quan sát với điều chỉ có trong tài liệu.
- [ ] **DSGN-02**: Data contract cho dữ liệu phục vụ: mô tả, schema, chính sách sử dụng, luật chất lượng, SLA, bảo mật, trách nhiệm và chính sách đổi phiên bản.
- [ ] **DSGN-03**: Quyết định kiến trúc ghi theo ADR (phương án, điều kiện, đánh đổi, khi nào quyết định sai), gồm phân lớp raw → cleaned → serving trên Fabric và chế độ xử lý.

### Build: Ingest — metadata và phiên stream

- [ ] **META-02**: Người vận hành truy xuất được snapshot metadata trong Lakehouse theo lần chạy, kèm thời điểm lấy và trạng thái hoàn tất; batch phân trang lỗi không thay thế phiên bản thành công.
- [ ] **META-03**: Người vận hành chọn được tập market AI về model từ metadata đã lưu, loại market con đã đóng/không hỗ trợ order book; lưu ánh xạ market → outcome → asset ID.
- [ ] **META-05**: Người vận hành thấy kết quả kiểm tra lại từng market được chọn trước đăng ký WebSocket; market không hợp lệ không được đăng ký âm thầm.
- [ ] **META-06**: Người xem truy được phiên bản metadata/watchlist và ánh xạ outcome mà từng phiên stream đã sử dụng.
- [ ] **CAP-01**: Người vận hành khởi chạy thủ công một phiên với thời lượng hữu hạn tự truyền; hết thời lượng thì collector dừng nhận và đóng phiên có kiểm soát.
- [ ] **CAP-02**: Người vận hành nhận stream market thật qua public WebSocket, đăng ký bằng outcome asset ID lấy từ metadata Lakehouse đã kiểm tra.
- [ ] **CAP-03**: Người vận hành truy được payload thô đã nhận và dấu thời gian nguồn/nhận cùng danh tính bản ghi phục vụ truy vết.
- [ ] **CAP-04**: Người vận hành thấy dữ liệu đi qua Python → Fabric Eventstream → Eventhouse/KQL và kiểm chứng được bằng một phiên chạy thật.
- [ ] **REL-01**: Người xem thấy khoảng mất kết nối và trạng thái thiếu dữ liệu; hệ thống thử kết nối lại có giới hạn, không thể hiện khoảng mất stream như dữ liệu liên tục.

### Build: Transform và KPI — correctness

- [ ] **REL-02**: Người vận hành phát lại được dữ liệu đã thu để phục hồi sink; gửi lại cùng bản ghi đã thu không làm tăng số liệu phục vụ. Không tuyên bố dedup sự kiện nguồn nếu thiếu khóa duy nhất đã xác minh.
- [ ] **REL-03**: Người vận hành xem được bản ghi lỗi/không diễn giải được và lý do; bản ghi đó không âm thầm đi vào KPI đã công bố.
- [ ] **KPI-01**: Người xem xếp hạng outcome tăng/giảm theo độ lớn thay đổi midpoint trong cửa sổ 5/15/60 phút, hiển thị mức đổi bằng điểm phần trăm.
- [ ] **KPI-02**: Người xem thấy "chưa đủ dữ liệu" khi thiếu baseline/lịch sử hợp lệ; KPI không tự điền giá giả qua khoảng mất kết nối.
- [ ] **KPI-03**: Người xem phân biệt quote updates với trade observations và xem được tần suất giao dịch quan sát cùng khối lượng/giá trị khi đủ trường dữ liệu; không coi thiếu size là 0 hoặc tuyên bố đây là toàn bộ giao dịch thị trường.
- [ ] **KPI-04**: Người xem xem được bid/ask spread theo thời gian, kèm trạng thái quote thiếu/cũ/không hợp lệ.
- [ ] **KPI-05**: Người xem phát hiện midpoint tăng/giảm ít nhất 5 điểm phần trăm trong 5 phút, chỉnh được ngưỡng/cửa sổ bằng cấu hình và xem spread/trade observations quanh sự kiện; đánh dấu phần phản ứng chưa quan sát đủ.

### Operationalize

- [ ] **OPER-01**: Code nằm trong Git, cấu hình (tên workspace/item, đường dẫn, lịch, ngưỡng) tham số hóa theo môi trường dev/prod, secret ngoài repo; chạy bằng danh tính dịch vụ nếu tenant cho phép, nếu không ghi rõ giới hạn.
- [ ] **OPER-02**: CI chạy unit test cho các phép biến đổi thuần và kiểm tra cấu hình/đồ thị trước khi triển khai; kết quả test nằm trong bằng chứng, check chưa chạy ghi NOT-RUN.
- [ ] **META-01**: Người vận hành có batch lấy metadata có tag AI bắt đầu lúc 07:00 hằng ngày, múi giờ Asia/Saigon (UTC+7; 00:00 UTC).
- [ ] **META-04**: Khi batch mới thất bại, người vận hành vẫn dùng được phiên bản metadata thành công gần nhất với cảnh báo rõ thời điểm cập nhật.

### Publish

- [ ] **VIEW-01**: Người xem dùng dashboard live trên Fabric để xem ba câu hỏi phân tích; mục tiêu dữ liệu hiển thị sau 15–30 giây từ collector receive, dùng 30 giây làm cận trên đo kiểm, không thay bằng thời gian ingestion riêng lẻ.
- [ ] **VIEW-03**: Người xem dùng Eventhouse/KQL phân tích lịch sử theo phiên/ngày từ dữ liệu đã thu, có phạm vi bao phủ và định nghĩa KPI nhất quán với dashboard live.
- [ ] **PUBL-01**: Đối tượng phục vụ (bảng/view/dashboard) được phát hành có phiên bản, quyền truy cập khớp contract và tìm thấy được trong catalog nếu Fabric hỗ trợ; chưa phát hành thì không gọi là data product.

### Run and govern

- [ ] **REL-04**: Người vận hành xem được trạng thái phiên, số lượng nhận/ghi/lỗi và kết quả đối soát theo grain; phân biệt lỗi nguồn, collector và sink.
- [ ] **VIEW-02**: Người xem phân biệt thời điểm nguồn, lần nhận mới nhất, lần refresh và khoảng mất dữ liệu; cảnh báo metadata fallback thể hiện rõ thời điểm cập nhật.
- [ ] **DOC-02**: Người vận hành làm theo runbook để chạy batch/stream, chọn thời lượng, xử lý lỗi/fallback/replay và đóng phiên, không đưa credentials vào repo hoặc bằng chứng demo.

### Scale and retire

- [ ] **RETI-01**: Checklist readiness production chạy có bằng chứng (ràng buộc chất lượng, thông báo lỗi, lịch tự động, môi trường tham số hóa, chi phí/capacity thực đo); mục chưa chạy ghi NOT-RUN. Đánh giá scale chỉ dựa trên tín hiệu đo được (thời lượng chạy tăng, capacity chạm trần, chi phí), không scale phòng xa.
- [ ] **RETI-02**: Kế hoạch gỡ bỏ có kiểm soát: thông báo deprecate, xuất bằng chứng, truy vấn, cấu hình và dữ liệu mẫu ra ngoài trial trước khi hết hạn, ghi provenance và phần nào là fixture.
- [ ] **DOC-01**: Người xem portfolio đọc được kiến trúc, lý do chọn công nghệ, định nghĩa KPI và giới hạn dữ liệu trong repo.
- [ ] **DOC-03**: Người xem portfolio kiểm tra được kết quả test và bằng chứng end-to-end, gồm latency, retry/replay, gap, failed batch và đối soát; DATA-SPEC/DATA-VERIFY áp dụng theo capability dataeng, kiểm tra chưa chạy ghi rõ NOT-RUN.

## v2 Requirements

- **EXT-01**: Thêm phân tích lịch sử riêng qua Spark/Delta và báo cáo Power BI.
- **EXT-02**: Tái dựng full order-book depth cho phân tích thanh khoản chi tiết.
- **EXT-03**: Làm mới watchlist trong phiên đang chạy để thu market mới trước batch kế tiếp.

Các mục v2 là đề xuất hoãn từ research, không phải cam kết phát hành tiếp theo.

## Out of Scope

| Feature | Reason |
|---|---|
| Đặt lệnh, quản lý ví, khuyến nghị đầu tư | Portfolio quan sát và phân tích dữ liệu |
| Stream 24/7 | Đã chọn phiên thủ công hữu hạn |
| Backfill thời kỳ trước khi bắt đầu thu | Lịch sử v1 tích lũy từ các phiên thực tế |
| Theo dõi mọi chủ đề / mọi market gắn tag AI | Watchlist tập trung AI model; metadata discovery có thể chứa ứng viên ngoài phạm vi |
| Suy luận nguyên nhân từ tin tức, ML anomaly detection | Chưa thuộc ba câu hỏi phân tích đã chọn |
| Cam kết đầy đủ mọi giao dịch / exactly-once từ nguồn | Chưa có delivery/uniqueness contract đã kiểm chứng |
| Network, HA/DR, đa workspace, phân quyền enterprise | Tài liệu quy trình khuyên bỏ qua với dự án nhỏ/portfolio |

## Acceptance details for phase design

Không hỏi thêm từng chi tiết triển khai trong new-project: phase design phải xác định baseline/stale-quote tolerance, nhóm shock liên tiếp, raw retention, giới hạn drain/retry, metadata publication contract và cách collector đọc Lakehouse. Những quyết định ảnh hưởng phạm vi/quyền/ngân sách phải đưa lại người dùng.

VIEW-01 là mục tiêu người dùng chọn, chưa có số đo hay cam kết percentile. INCP-02 và DATA-SPEC phải định nghĩa phép đo và điều kiện lỗi/recovery trước khi test; không âm thầm đổi thành p95 hoặc bỏ mẫu chậm. Giờ batch là giờ bắt đầu, không tự suy ra deadline hoàn tất. Metadata fallback chưa có tuổi tối đa được duyệt.

## Traceability

Ánh xạ theo quy trình triển khai 8 phase; requirements và roadmap còn chờ duyệt. Mỗi requirement có đúng một phase sở hữu.

| Requirement | Phase | Status |
|---|---|---|
| INCP-01 | Phase 1 | Pending |
| INCP-02 | Phase 1 | Pending |
| DSGN-01 | Phase 2 | Pending |
| DSGN-02 | Phase 2 | Pending |
| DSGN-03 | Phase 2 | Pending |
| META-02 | Phase 3 | Pending |
| META-03 | Phase 3 | Pending |
| META-05 | Phase 3 | Pending |
| META-06 | Phase 3 | Pending |
| CAP-01 | Phase 3 | Pending |
| CAP-02 | Phase 3 | Pending |
| CAP-03 | Phase 3 | Pending |
| CAP-04 | Phase 3 | Pending |
| REL-01 | Phase 3 | Pending |
| REL-02 | Phase 4 | Pending |
| REL-03 | Phase 4 | Pending |
| KPI-01 | Phase 4 | Pending |
| KPI-02 | Phase 4 | Pending |
| KPI-03 | Phase 4 | Pending |
| KPI-04 | Phase 4 | Pending |
| KPI-05 | Phase 4 | Pending |
| META-01 | Phase 5 | Pending |
| META-04 | Phase 5 | Pending |
| OPER-01 | Phase 5 | Pending |
| OPER-02 | Phase 5 | Pending |
| VIEW-01 | Phase 6 | Pending |
| VIEW-03 | Phase 6 | Pending |
| PUBL-01 | Phase 6 | Pending |
| REL-04 | Phase 7 | Pending |
| VIEW-02 | Phase 7 | Pending |
| DOC-02 | Phase 7 | Pending |
| DOC-01 | Phase 8 | Pending |
| DOC-03 | Phase 8 | Pending |
| RETI-01 | Phase 8 | Pending |
| RETI-02 | Phase 8 | Pending |

**Coverage:** 35 v1 requirements (25 from the earlier draft, 10 added on 2026-10-21 to cover Inception, Design, Operationalize, Publish and Scale & retire); 35 mapped; 0 unmapped; approval pending.

---
*Last updated: 2026-10-21 after remapping to the 7-stage delivery process (8 phases)*
