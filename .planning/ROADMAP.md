# Roadmap: Polymarket Market Intelligence

## Overview

**Status:** APPROVED by the user on 2026-10-21. Phase execution and cloud operations require their own applicable workflow gates.
**Milestone:** v1.0 portfolio release.
**Structure:** Horizontal Layers, selected by the user. Execute phases sequentially with standard granularity; each phase completes a coherent technical responsibility before downstream integration.

Build the approved Fabric design and resource boundaries, complete raw metadata, transform the daily watchlist, complete session-based quote collection, implement analytical transformations, integrate the three views, then perform longer measured verification and preserve portfolio evidence. Metadata transformation precedes final quote subscriptions because the approved watchlist is selected downstream from raw metadata. This ordering does not turn each phase into a vertical MVP.

Existing cloud items are candidates for reuse, not completed phases. The architecture research document is background; Phase 1 must produce a reviewed project solution design. No component/runtime is selected by this roadmap alone, and no resource creation, deployment or cloud job is authorized by roadmap approval.

## Phases

- [ ] **Phase 1: Solution Design and Resource Boundaries** — Chốt thiết kế dự án và phạm vi Fabric hiện có trước implementation.
- [ ] **Phase 2: Raw Metadata Ingestion** — Hoàn thiện raw metadata toàn bộ market đang mở thuộc tập tag AI.
- [ ] **Phase 3: Daily Watchlist Transformation** — Chọn và quản lý danh sách tối đa 30 market từ raw metadata.
- [ ] **Phase 4: Raw Quote Collection** — Hoàn thiện thu WebSocket theo phiên, raw, thời gian và continuity.
- [ ] **Phase 5: Analytical Transformations and KPIs** — Hoàn thiện quote state, chất lượng, cửa sổ, ranking và replay.
- [ ] **Phase 6: Reporting and Short-Session Integration** — Nối live dashboard, daily brief và history vào toàn luồng chạy ngắn.
- [ ] **Phase 7: Measured Verification and Portfolio Delivery** — Chạy dài sau khi toàn luồng hoạt động, đo freshness và đóng gói bằng chứng.

## Phase Details

### Phase 1: Solution Design and Resource Boundaries
**Goal:** Có thiết kế solution được người dùng duyệt, ánh xạ yêu cầu sang nguồn, component, hợp đồng dữ liệu và ranh giới tài nguyên hiện có.
**Depends on:** Nothing (first phase)
**Requirements:** OPS-04
**Success Criteria**:
1. Reviewer đọc được thiết kế dự án gồm luồng batch/live, trách nhiệm component, grain/khóa/schema, đầu vào/đầu ra từng lớp, runtime/identity và ranh giới vận hành; phân biệt quyết định đã chốt với phương án nghiên cứu.
2. Phạm vi item được phép tái sử dụng, quyền và giới hạn tài nguyên được ghi dựa trên kiểm tra chỉ đọc hoặc đánh dấu chưa xác minh; không mặc định tất cả item cũ hay GYM thuộc dự án.
3. Lựa chọn SDK/API/công cụ upstream được đối chiếu với dependency thực tế và tài liệu chính thức hiện hành; mọi custom code có lý do, không tạo framework mới hoặc yêu cầu dịch vụ trả phí bổ sung.
4. Các hợp đồng cần thiết cho implementation được liệt kê theo phase chịu trách nhiệm; cách chọn 30 market chi tiết vẫn được giải quyết trong planning transform, không lọc raw ingest thành watchlist.
**Plans:** TBD during phase planning.

### Phase 2: Raw Metadata Ingestion
**Goal:** Có raw metadata đầy đủ trong phạm vi nguồn đã xác định, cùng bằng chứng extract hoàn tất hoặc chưa hoàn tất.
**Depends on:** Phase 1
**Requirements:** RAW-01, RAW-02, RAW-03
**Success Criteria**:
1. Người vận hành kiểm tra được tập tag AI/filter market đang mở được dùng, các trang/điều kiện kết thúc và raw metadata đã lưu; không cắt ingest ở 30 market.
2. Raw được giữ trước phân loại AI trực tiếp và chọn watchlist; có provenance đủ để đối chiếu bản nguồn và transform về sau.
3. Trường hợp lỗi nguồn, phân trang hoặc page cap được thể hiện là extract chưa hoàn tất, không xuất bản nhầm complete. Code/raw cũ chỉ được tái sử dụng sau kiểm tra phù hợp yêu cầu mới.
**Plans:** TBD during phase planning.

### Phase 3: Daily Watchlist Transformation
**Goal:** Từ raw metadata tạo danh sách AI trực tiếp tối đa 30 market với membership theo ngày có thể kiểm chứng.
**Depends on:** Phase 2
**Requirements:** WATCH-01, WATCH-02, WATCH-03, WATCH-04, WATCH-05
**Success Criteria**:
1. Reviewer đối chiếu được quy tắc AI trực tiếp và tiêu chí giao dịch đã duyệt với kết quả chọn tối đa 30 market; thiếu ứng viên thì danh sách nhỏ hơn và không thêm market ngoài tiêu chí.
2. Fixture chứng minh giữ member hợp lệ và chỉ bổ sung chỗ trống theo volume 24h với field/đơn vị/null/tie đã thống nhất, không chọn lại toàn bộ top volume mỗi ngày.
3. Lần khởi chạy đầu ngày Asia/Saigon kích hoạt cập nhật; các lần trong cùng ngày dùng lại danh sách, không polling trạng thái hoặc thay member đóng trong ngày.
4. Có thể tra membership tại thời điểm lịch sử và giữ dữ liệu market cũ sau khi thay member.
**Plans:** TBD during phase planning.

### Phase 4: Raw Quote Collection
**Goal:** Thu được raw WebSocket của watchlist theo phiên thủ công, với timestamp, quote validation và dấu vết kết nối/gaps.
**Depends on:** Phase 3
**Requirements:** COLL-01, COLL-02, COLL-03, COLL-04, COLL-05, KPI-07, DQ-01
**Success Criteria**:
1. Phiên hữu hạn khởi chạy thủ công đăng ký đúng outcome/asset của danh sách ngày và lưu được payload gốc với receipt time, session và mapping market/outcome; bảo toàn raw được kiểm tra qua readback.
2. Mất kết nối được ghi thành gap và có thử reconnect trong phiên, không giả lập dữ liệu bù; định danh phiên và khoảng kết nối đủ để kiểm tra quan sát thực tế.
3. Market không gửi quote mới không làm dừng thu các market khác; hành vi subscribe asset đã đóng và cách xử lý lỗi được kiểm tra/ghi đúng kết quả, không coi im lặng là guarantee của nguồn.
4. Quote lỗi về timestamp, thiếu bid/ask, giá ngoài phạm vi hoặc bid > ask được giữ raw, có lý do loại; event time không được thay âm thầm bằng receipt time và timestamp chuẩn hóa về UTC.
**Plans:** TBD during phase planning.

### Phase 5: Analytical Transformations and KPIs
**Goal:** Có kết quả quote state, KPI và ranking đúng trên đầu vào đã biết, kèm chất lượng theo cửa sổ và khả năng tính lại từ raw.
**Depends on:** Phase 4
**Requirements:** KPI-01, KPI-02, KPI-03, KPI-04, KPI-05, KPI-06, DQ-02, DQ-03, DQ-04, OPS-01
**Success Criteria**:
1. Với fixture độc lập có kết quả kỳ vọng, mid-price, spread, signed change ở 5/15/60 phút và thứ hạng từng market khớp hợp đồng anchors/tie/out-of-order đã duyệt; outcome tạo hạng và hai mốc giá được giải thích.
2. Duplicate messages không nhân đôi quote-change count; thay đổi cặp bid/ask được đếm đúng và reconnect snapshot không được tính là một lần đổi báo giá.
3. Kết quả thiếu/không đủ cửa sổ có lý do theo từng KPI, không zero-fill hoặc backward-fill; market không có outcome đủ điều kiện không có hạng tương ứng.
4. Coverage/gaps và partial count khớp phạm vi quan sát trong fixture, gồm trường hợp hai anchors đủ tin cậy qua gap.
5. Chạy lại cùng raw tái tạo kết quả kỳ vọng mà không sửa raw hoặc nhân đôi kết quả; fixture chứng minh ngữ nghĩa, không thay phép đo latency hoặc live windows.
**Plans:** TBD during phase planning.

### Phase 6: Reporting and Short-Session Integration
**Goal:** Có live dashboard, daily brief và history view được nối với toàn luồng thực tế trong một phiên ngắn.
**Depends on:** Phase 5
**Requirements:** DQ-05, VIEW-01, VIEW-02, VIEW-03, VIEW-04, OPS-03
**Success Criteria**:
1. Một phiên thử vài phút được phê duyệt chứng minh đường đi ingest -> raw -> transform -> KPI -> dashboard/report, với trạng thái chưa đủ dữ liệu thật cho cửa sổ không thể quan sát đủ.
2. Live dashboard hiển thị kết quả, thông tin giải thích và thời điểm dữ liệu/phiên; market im lặng không tự thành closed, gap hoặc freshness giả; phân biệt độ trễ xử lý với quote không thay đổi.
3. Daily brief thể hiện phạm vi phiên thực thu và trạng thái pending/partial/complete theo hợp đồng đã duyệt; complete không đồng nghĩa đủ 24h.
4. History view giữ các khoảng không quan sát và lịch sử member cũ; chỉ có bối cảnh 7 ngày khi đạt điều kiện đủ dữ liệu đã duyệt, nếu chưa đủ phải nêu rõ.
5. Reviewer xác nhận toàn luồng đã hoạt động trước khi lên lịch phiên dài; phase này không tuyên bố đạt live 60 phút hoặc p95 chỉ từ fixture hay thời lượng chạy ngắn.
**Plans:** TBD during phase planning.

### Phase 7: Measured Verification and Portfolio Delivery
**Goal:** Có bằng chứng live cho các claim v1 và bộ portfolio tái kiểm tra được trước khi trial hết hạn.
**Depends on:** Phase 6
**Requirements:** OPS-02, EVID-01, EVID-02, EVID-03
**Success Criteria**:
1. Protocol đo và phạm vi phiên dài được thống nhất trước khi chạy; bằng chứng đối chiếu receipt time tới kết quả thực sự hiển thị cho phép đánh giá p95 <=60 giây. Không gắn nhãn đạt nếu phép đo không đạt hoặc chưa đủ bằng chứng.
2. Live windows 5/15/60 phút được kiểm tra trong phạm vi phiên đủ dữ liệu, đối chiếu với anchors/quality/coverage; kết quả fixture và live được ghi tách biệt, không lấy thiếu dữ liệu làm số 0.
3. Repo có code, kiến trúc đã duyệt, định nghĩa KPI/phạm vi và hướng dẫn tái chạy khớp môi trường đã thử; Fabric definitions native được lưu trong Git cùng demo dashboard/report.
4. Báo cáo nghiệm thu nêu đầu vào/kỳ vọng, commands/conditions, kết quả, resource-use evidence và giới hạn; giữ bằng chứng portfolio trước expiry, không nhận kết quả Agent Gym hoặc job Completed làm production validation.
**Plans:** TBD during phase planning.

## Traceability and Verification Boundaries

Mỗi yêu cầu v1 có đúng một phase chịu trách nhiệm nghiệm thu, được liệt kê trong REQUIREMENTS.md. Các constraint như chỉ dùng tài nguyên hiện có, bảo toàn raw, không lộ secret và không mutate cloud khi chưa được phép áp dụng xuyên phase dù được gán một phase chính.

Phase 1–5 dùng kiểm tra cục bộ/fixture và các phiên thử ngắn được phê duyệt khi cần; Phase 6 tích hợp toàn luồng chạy ngắn; Phase 7 mới chạy dài. Kiểm tra tích hợp có thể kiểm tra lại capability của phase trước nhưng không tạo thêm ownership hoặc đánh dấu hoàn thành khi chưa có bằng chứng.

Các tiêu chí trên không tạo thêm số giờ chạy, tỷ lệ mất dữ liệu, cadence polling, RPO/RTO hoặc deadline chưa được người dùng đồng ý. Với đường nguồn chưa được thử, planning phải giải quyết evidence gaps trước khi đưa vào implementation.

## Upstream Reuse and Open Decisions

Đánh giá official Polymarket SDK/API, Microsoft-maintained Fabric tools và các ingest item hiện có trước custom code. Không mặc định SDK branch snapshot là phiên bản cần cài, hay workspace runtime setting là runtime của mọi job. Schema, runtime và serving components sẽ được đề xuất cụ thể trong thiết kế/phase planning với bằng chứng hiện hành.

Historical upstream comparisons và promoted offline evidence checker trong Hub chỉ có giá trị theo contract đã ghi. Chúng không thay kiểm tra raw thật, không bảo đảm production hoặc chọn sẵn component.

Các quyết định planning còn mở trong REQUIREMENTS.md phải được giải quyết trước phase phụ thuộc, gồm tag mapping, chọn AI trực tiếp, volume/null/tie, KPI anchors, coverage, ngày của brief, retention và protocol đo. Không yêu cầu phỏng vấn lại toàn bộ phạm vi đã được duyệt.

## Progress

**Execution Order:** 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7. No phase has started; Phase 1 is ready for discussion and planning.

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Solution Design and Resource Boundaries | 0/TBD | Not started | - |
| 2. Raw Metadata Ingestion | 0/TBD | Not started | - |
| 3. Daily Watchlist Transformation | 0/TBD | Not started | - |
| 4. Raw Quote Collection | 0/TBD | Not started | - |
| 5. Analytical Transformations and KPIs | 0/TBD | Not started | - |
| 6. Reporting and Short-Session Integration | 0/TBD | Not started | - |
| 7. Measured Verification and Portfolio Delivery | 0/TBD | Not started | - |

## Knowledge Sources

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-engineering/design.md` — raw là ranh giới thu thập/biến đổi và điều tra; giữ raw không tự chứng minh dữ liệu đủ cho mọi câu hỏi.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-storage/gotchas.md` — công bố raw, retry, replay và retention là các hợp đồng riêng cần kiểm chứng.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-pipeline-delivery/guidance.md` — ownership/dependency và artifact provenance; rollback definition khác phục hồi dữ liệu, ví dụ ADF không tự áp dụng thành contract Fabric.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/discovery.md` — source schema không quyết định business policy; chọn watchlist cần sau raw metadata.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/realtime-quotes.md` — snapshot/transition/time và source guarantee chưa kiểm thử.

Nguồn phạm vi là REQUIREMENTS.md đã duyệt, PROJECT.md và lựa chọn Horizontal Layers của người dùng. Research cung cấp trade-offs có phạm vi, không phải thiết kế hoặc kết quả thực thi đã duyệt. Hub chỉ được đọc trong bước roadmap này.
