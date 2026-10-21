# Roadmap: Polymarket AI Markets Stream Pipeline

**Milestone:** v1.0
**Status:** Draft — remapped to the delivery process on 2026-10-21; awaiting user review before commit.
**Structure:** Theo quy trình triển khai 7 giai đoạn (Inception → Design → Build → Operationalize → Publish → Run & govern → Scale & retire), với Build tách thành hai phase (Ingest, Transform & KPI) theo hai giai đoạn ingest và transform của pipeline lifecycle. Người dùng chọn cấu trúc này ngày 2026-10-21, thay thế lựa chọn vertical MVP trước đó.
**Execution:** Authored inline; no roadmapper subagent dispatched.

## Overview

Chốt owner, KPI và SLO bằng con số, rồi thiết kế nguồn, contract và kiến trúc trước khi viết pipeline. Sau đó xây ingest (metadata Lakehouse → collector → Eventstream → Eventhouse), xây transform và KPI có kiểm thử, chính thức hóa Git/CI/lịch chạy, phát hành dashboard và truy vấn lịch sử, vận hành với giám sát và runbook, và cuối cùng đánh giá readiness, scale theo tín hiệu và kế hoạch gỡ bỏ trước khi trial hết hạn.

Vì thứ tự theo giai đoạn, luồng xuyên suốt đầu tiên chỉ chạy ở Phase 3. Phase 3 vì vậy ưu tiên một phiên ngắn đi hết đường từ metadata tới Eventhouse trước khi mở rộng. Tài liệu quy trình ghi các giai đoạn không tuần tự cứng; Git và test bắt đầu từ Phase 1 chứ không chờ Phase 5.

## Phases

- [ ] **Phase 1: Inception** — Chốt owner, KPI và SLO bằng con số.
- [ ] **Phase 2: Design** — Source inventory, data contract và quyết định kiến trúc trước khi viết code.
- [ ] **Phase 3: Build — Ingest** — Metadata Lakehouse, collector và đường stream tới Eventhouse chạy được trên một phiên thật.
- [ ] **Phase 4: Build — Transform và KPI** — Làm sạch, kiểm soát chất lượng, replay và các KPI movers, spread, activity, shocks.
- [ ] **Phase 5: Operationalize** — Git, môi trường tham số hóa, CI, lịch 07:00 và hành vi khi batch lỗi.
- [ ] **Phase 6: Publish** — Dashboard live, truy vấn lịch sử và phát hành có phiên bản cùng quyền truy cập.
- [ ] **Phase 7: Run và govern** — Giám sát, đối soát, hiển thị gap/fallback và runbook.
- [ ] **Phase 8: Scale và retire** — Checklist readiness, đánh giá scale theo tín hiệu, kế hoạch gỡ bỏ và gói portfolio.

## Phase Details

### Phase 1: Inception
**Goal:** Có owner và mục tiêu đo được cho data product, đủ để thiết kế không bị chặn bởi cổng G1 của dataeng (thiếu owner hoặc SLA không có con số).
**Depends on:** Nothing (first phase)
**Requirements:** INCP-01, INCP-02
**Success Criteria**:
1. Owner, người bảo trì và đầu mối escalate được ghi trong repo và người dùng xác nhận.
2. Ba câu hỏi phân tích (movers, activity, shocks) gắn với KPI có công thức, đơn vị và consumer giả định; consumer được đánh dấu là giả thuyết portfolio, không phải kết quả phỏng vấn.
3. SLO có con số và cách đo: tỷ lệ đạt cùng điểm đo của mục tiêu 15–30 giây, hạn hoàn tất batch, tuổi tối đa metadata fallback, đánh giá lúc lỗi/recovery. Mục người dùng chưa chốt được ghi là chưa chốt, không tự điền.
4. Giá trị portfolio và giới hạn (không có đo lường người dùng thật, không có bằng chứng latency) ghi rõ. Chưa có hành động nào trên cloud.
**Plans:** TBD during phase planning.
**Research needed:** Không cần nghiên cứu ngoài; cần câu trả lời từ người dùng. DATA-SPEC mục 1 được viết ở phase này.

### Phase 2: Design
**Goal:** Có source inventory, data contract và quyết định kiến trúc đã được review để các phase Build không phải tự quyết định kiến trúc dữ liệu.
**Depends on:** Phase 1
**Requirements:** DSGN-01, DSGN-02, DSGN-03
**Success Criteria**:
1. Source inventory cho metadata API và WebSocket nêu ngữ nghĩa thay đổi, khóa, thứ tự, retention, phân trang và hành vi lỗi, mỗi điều có nhãn đã quan sát (mẫu giới hạn) hay chỉ từ tài liệu.
2. Data contract cho dữ liệu phục vụ có schema, luật chất lượng, SLA, trách nhiệm và chính sách đổi phiên bản; mỗi lời hứa có V-ID kiểm chứng trong kế hoạch.
3. Mỗi quyết định kiến trúc (phân lớp raw → cleaned → serving trên Fabric, chế độ xử lý, đường Python → Eventstream → Eventhouse, cách collector đọc Lakehouse) có phương án, điều kiện, đánh đổi và "sai nếu".
4. DATA-SPEC được reviewer độc lập đọc; không còn phát hiện BLOCKER chưa xử lý. Nếu chạy nội tuyến, ghi `executed_inline` và `independence: degraded`.
5. Chỉ dùng mẫu đọc công khai có giới hạn; không có ghi hay chạy job trên cloud nếu chưa được duyệt riêng.
**Plans:** TBD during phase planning.
**Research needed:** Mẫu live của nguồn, timestamp, trường trade, khả năng đọc Lakehouse của collector, baseline capacity.

### Phase 3: Build — Ingest
**Goal:** Người vận hành chạy batch metadata có giới hạn vào Lakehouse, chọn outcome AI-model hợp lệ, mở phiên stream hữu hạn và truy được dữ liệu thô trong Eventhouse từ một phiên thật.
**Depends on:** Phase 2
**Requirements:** META-02, META-03, META-05, META-06, CAP-01, CAP-02, CAP-03, CAP-04, REL-01
**Success Criteria**:
1. Một phiên ngắn đi hết đường Polymarket → Python → Eventstream → Eventhouse; người vận hành truy vấn KQL thấy danh tính outcome và bản ghi nhận gần nhất. Đây là bằng chứng ingest, chưa phải dashboard hay bằng chứng latency.
2. Batch chạy thủ công cho snapshot raw và mapping market/outcome/asset từ một phiên bản hoàn chỉnh trong Lakehouse; batch phân trang lỗi không thay bản thành công; market không hợp lệ bị loại kèm lý do.
3. Collector kiểm tra lại market trước đăng ký, đăng ký bằng asset ID từ phiên bản metadata đã chọn và lưu phiên bản/watchlist vào phiên chạy.
4. Hết thời lượng truyền vào, collector dừng nhận và đóng phiên có kiểm soát; payload thô cùng source/receive timestamps và capture IDs truy xuất được qua readback.
5. Ngắt kết nối có kiểm soát tạo gap quan sát được và reconnect có giới hạn, không giả lập dữ liệu đã mất.
6. Kiểm thử mapping, phân trang và thời lượng chạy trong phase này. Preflight cloud chỉ đọc diễn ra trước; ghi và chạy cloud cần quyền duyệt theo PROJECT.md.
**Plans:** TBD during phase planning.
**Research needed:** Cơ chế chạy batch Lakehouse, endpoint/auth Eventstream, sink Eventhouse, tốc độ nhận thực tế.

### Phase 4: Build — Transform và KPI
**Goal:** Có dữ liệu đã làm sạch kèm kiểm soát chất lượng, khả năng replay và các KPI movers, spread, activity, shocks đúng trên đầu vào đã biết.
**Depends on:** Phase 3
**Requirements:** REL-02, REL-03, KPI-01, KPI-02, KPI-03, KPI-04, KPI-05
**Success Criteria**:
1. Ở ranh giới raw → cleaned, mỗi luật chất lượng có mức warn, drop hoặc fail được chọn có chủ đích; bản ghi loại có lý do và nằm trong quarantine, không vào KPI đã công bố; có số đếm pass/fail.
2. Phát lại cùng capture và gửi lại cùng bản ghi không làm tăng số liệu phục vụ; không tuyên bố dedup nguồn khi thiếu khóa duy nhất đã xác minh.
3. Với fixture có đáp án đã biết, thứ hạng midpoint theo cửa sổ 5/15/60 phút, hướng và điểm phần trăm khớp; cửa sổ thiếu baseline hoặc qua gap hiện "chưa đủ dữ liệu", không backward-fill hay zero-fill.
4. Spread và trạng thái quote thiếu/crossed/stale theo quy tắc ghi trong DATA-SPEC; quote updates và trade observations tách biệt, size thiếu hiện partial/unknown thay vì 0.
5. Midpoint đổi đúng 5 điểm phần trăm trong 5 phút kích hoạt shock khi đủ dữ liệu, dưới ngưỡng thì không; đổi cấu hình ngưỡng/cửa sổ đổi kết quả đúng kỳ vọng; fixture/simulated shock được phân biệt với dữ liệu live.
6. Unit test cho các phép biến đổi thuần chạy ngoài engine; kết quả cũng được chứng minh trên dữ liệu đã thu.
**Plans:** TBD during phase planning.
**Research needed:** Chính sách baseline và quote-age, nhóm shock liên tiếp, cách cài đặt cửa sổ trong KQL, trường trade có sẵn.

### Phase 5: Operationalize
**Goal:** Cùng một code chạy được qua các môi trường từ Git, kiểm thử tự động trước khi triển khai, và batch metadata chạy theo lịch với hành vi lỗi rõ ràng.
**Depends on:** Phase 4
**Requirements:** OPER-01, OPER-02, META-01, META-04
**Success Criteria**:
1. Toàn bộ code và cấu hình nằm trong Git; tên workspace/item, đường dẫn, lịch và ngưỡng tham số hóa theo dev/prod; không có secret trong repo hay bằng chứng.
2. CI chạy unit test và kiểm tra cấu hình/đồ thị trước khi triển khai; kết quả được lưu, check chưa chạy ghi NOT-RUN.
3. Batch được cấu hình bắt đầu 07:00 Asia/Saigon hằng ngày; có bằng chứng cấu hình lịch và một scheduled run thực tế. Manual trigger không được coi là bằng chứng lịch đã chạy.
4. Khi cố tình làm batch mới thất bại, phiên mới dùng bản thành công gần nhất, cảnh báo thời điểm cập nhật và kiểm tra lại market trước đăng ký; batch dở dang không thay bản hoàn chỉnh.
5. Chạy bằng danh tính dịch vụ nếu tenant cho phép; nếu không, giới hạn được ghi rõ. Triển khai lịch trên cloud cần quyền duyệt riêng.
**Plans:** TBD during phase planning.
**Research needed:** Ngữ nghĩa scheduler và múi giờ, Git integration và deployment pipelines của Fabric trên trial, danh tính dịch vụ.

### Phase 6: Publish
**Goal:** Người xem dùng dashboard live và truy vấn lịch sử đã phát hành có phiên bản, quyền truy cập khớp contract và có thể tìm thấy.
**Depends on:** Phase 5
**Requirements:** VIEW-01, VIEW-03, PUBL-01
**Success Criteria**:
1. Dashboard live phục vụ cả ba câu hỏi (movers, activity, shocks) từ cùng định nghĩa KPI với Phase 4.
2. Phép đo collector-receive → visible-update kiểm cận trên 30 giây theo giao thức đã định ở INCP-02 và DATA-SPEC; không đổi thành p95 hay bỏ mẫu chậm. Nếu chưa đạt thì sửa hoặc đưa lại quyết định cho người dùng.
3. Người xem chọn phiên/ngày bằng KQL, xem ba câu hỏi với metadata version và coverage tương ứng; kết quả lịch sử đối soát được với live cho cùng khoảng và định nghĩa.
4. Đối tượng phục vụ có phiên bản, quyền truy cập khớp contract và xuất hiện trong catalog nếu Fabric hỗ trợ; nếu không hỗ trợ, giới hạn được ghi.
5. Bằng chứng ghi nhận capacity, độ trễ ingestion/query/refresh và nguồn dữ liệu của mẫu test; fixture phân biệt với live.
**Plans:** TBD during phase planning.
**UI hint:** yes
**Research needed:** Real-Time Dashboard refresh, đo độ trễ toàn đường, catalog và endorsement trên trial.

### Phase 7: Run và govern
**Goal:** Người vận hành theo dõi, đối soát và xử lý sự cố của pipeline bằng giám sát và runbook, với trạng thái dữ liệu minh bạch cho người xem.
**Depends on:** Phase 6
**Requirements:** REL-04, VIEW-02, DOC-02
**Success Criteria**:
1. Báo cáo phiên đối soát được số nhận/ghi/lỗi theo grain đã định nghĩa và phân biệt lỗi nguồn, collector, sink.
2. Người xem phân biệt thời điểm nguồn, lần nhận mới nhất, lần refresh, gap, metadata fallback kèm thời điểm cập nhật và lỗi nguồn/collector/sink.
3. Có thông báo lỗi và theo dõi độ tươi/thời lượng chạy; ngưỡng ban đầu thận trọng và được thử bằng một sự cố cố ý.
4. Người vận hành làm theo runbook từ repo để lấy metadata, chạy phiên với thời lượng tự truyền, dùng fallback/replay và đóng phiên; credentials không có trong repo hay bằng chứng demo.
**Plans:** TBD during phase planning.
**UI hint:** yes
**Research needed:** Alert và Activator trên trial, nơi lưu event log truy vấn được, retention.

### Phase 8: Scale và retire
**Goal:** Đánh giá readiness có bằng chứng, scale chỉ khi có tín hiệu đo được, và bảo toàn gói portfolio cùng kế hoạch gỡ bỏ trước khi trial hết hạn.
**Depends on:** Phase 7
**Requirements:** RETI-01, RETI-02, DOC-01, DOC-03
**Success Criteria**:
1. Checklist readiness chạy có bằng chứng: ràng buộc chất lượng và tỷ lệ pass đã xem, thông báo lỗi đã thử, lịch tự động, môi trường tham số hóa, chi phí/capacity thực đo ít nhất một lần; mục chưa chạy ghi NOT-RUN.
2. Đánh giá scale nêu tín hiệu đo được (thời lượng chạy, capacity, chi phí) và kết luận; không có thay đổi scale nào thiếu tín hiệu.
3. Reviewer đọc được kiến trúc, lý do chọn công nghệ, định nghĩa KPI và giới hạn; ví dụ phiên ngắn, missing size, stale metadata và disconnect có bằng chứng rõ ràng.
4. Có gói bằng chứng end-to-end tái lập được cùng DATA-SPEC/DATA-VERIFY của từng data phase. Check chưa chạy ghi NOT-RUN; review code không được coi là runtime verification.
5. Kế hoạch gỡ bỏ có thông báo deprecate; bằng chứng, truy vấn, cấu hình và dữ liệu mẫu được lưu ngoài trial, ghi provenance và phần nào là fixture.
**Plans:** TBD during phase planning.
**Research needed:** Quy trình xuất bằng chứng và dữ liệu khỏi trial, ngày hết hạn trial đã xác minh.

## Cross-phase rules

- Phase 1–8 đều tạo hoặc kiểm chứng artifact dữ liệu (nghiệp vụ, contract, pipeline, lịch, phát hành). Dùng capability dataeng: DATA-SPEC trước khi lập plan, DATA-VERIFY với bằng chứng chạy thật khi verify. Phase 1–2 viết DATA-SPEC mục 1–3 và 4.0 ở cấp dự án; các phase Build kế thừa, không làm lại. Phase nào coordinator xác định không đụng dữ liệu thì bỏ qua theo cổng G0.
- Skill hay agent đã cài không chứng minh review độc lập đã xảy ra. Nêu rõ khi chạy nội tuyến và độc lập bị giảm.
- Mỗi requirement có một phase sở hữu. Phase sau mở rộng hoặc kiểm hồi quy khả năng trước mà không nhân đôi traceability.
- Ranh giới cloud trong PROJECT.md vẫn hiệu lực: duyệt roadmap là duyệt phạm vi và thứ tự, không phải duyệt triển khai.
- Dùng Git từ Phase 1; Phase 5 chính thức hóa CI/CD và môi trường. Kiểm thử bắt đầu ở phase tạo ra từng hành vi, không dồn sang cuối.
- Tài liệu quy trình là tổng hợp của tác giả và có thiên hướng nhà cung cấp; lấy nguyên lý, không lấy con số đặc thù công cụ.

## Progress

Execution order: 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8, sau khi requirements và roadmap được duyệt.

| Phase | Plans Complete | Status | Completed |
|---|---|---|---|
| 1. Inception | 0/TBD | Not started | - |
| 2. Design | 0/TBD | Not started | - |
| 3. Build — Ingest | 0/TBD | Not started | - |
| 4. Build — Transform và KPI | 0/TBD | Not started | - |
| 5. Operationalize | 0/TBD | Not started | - |
| 6. Publish | 0/TBD | Not started | - |
| 7. Run và govern | 0/TBD | Not started | - |
| 8. Scale và retire | 0/TBD | Not started | - |

**Coverage:** 35 v1 requirements mapped exactly once; 0 unmapped. No phase plans or implementations exist yet.

*Last updated: 2026-10-21 after remapping to the 7-stage delivery process*
