# Requirements: Polymarket Market Intelligence

**Defined:** 2026-10-21
**Status:** APPROVED by the user on 2026-10-21. Requirement scope is approved; implementation, roadmap and detailed architecture are not yet approved or verified.
**Core Value:** Giúp researcher nhận diện và giải thích biến động giá bằng dữ liệu có thể kiểm chứng, không nhầm dữ liệu thiếu/trễ với thị trường yên tĩnh.

## User Stories

- Là researcher, tôi muốn biết market AI nào trong danh sách quan sát biến động mạnh nhất ở từng cửa sổ thời gian để chọn đối tượng nghiên cứu; không giới hạn cố định ba market.
- Tôi muốn biết outcome tạo thứ hạng, hướng/độ lớn biến động và hai mốc giá được dùng.
- Tôi muốn xem giá tham chiếu, spread và số lần báo giá đổi để mô tả diễn biến.
- Tôi muốn xem lịch sử thực thu, freshness, coverage và gaps để biết giới hạn của nhận định.

Các persona và giá trị người dùng là giả thuyết portfolio đã thống nhất; chưa có phỏng vấn hoặc phép đo tác động thực tế.

## v1 Requirements

Các checkbox là yêu cầu cần triển khai và kiểm chứng, không phải khả năng đã được xác nhận hoàn thành. Cách triển khai nguồn, schema, engine và giao diện chưa bị khóa bởi tài liệu này.

### Raw Metadata

- [ ] **RAW-01**: Người vận hành thu được raw metadata của toàn bộ market đang mở thuộc tập tag AI đã xác định, không giới hạn raw ingest ở 30 market.
- [ ] **RAW-02**: Raw metadata được giữ trước bước lọc AI trực tiếp và lựa chọn danh sách quan sát; các quyết định chọn tối đa 30 market thuộc bước transform.
- [ ] **RAW-03**: Người vận hành phân biệt được extract metadata hoàn tất và extract lỗi/chưa đủ trang; dừng do giới hạn trang không được công bố là đầy đủ.

### Watchlist

- [ ] **WATCH-01**: Researcher có danh sách quan sát ban đầu tối đa 30 market về sự kiện AI trực tiếp; nếu không đủ ứng viên thì hiển thị số thực tế.
- [ ] **WATCH-02**: Batch cập nhật danh sách ở lần khởi chạy thủ công đầu tiên của mỗi ngày theo Asia/Saigon.
- [ ] **WATCH-03**: Lần cập nhật ngày giữ market cũ còn đủ điều kiện và tự bổ sung chỗ trống theo khối lượng giao dịch 24 giờ gần nhất, thay vì thay toàn bộ danh sách bằng top volume mới.
- [ ] **WATCH-04**: Những lần khởi chạy sau trong ngày dùng lại danh sách đã chọn, kể cả member đóng trong ngày; không bắt buộc polling trạng thái, kiểm tra lại trước mỗi phiên, hủy subscription theo resolution hoặc thay thế ngay.
- [ ] **WATCH-05**: Researcher tra được market thuộc danh sách quan sát nào tại thời điểm lịch sử đang xem; thay member không xóa lịch sử của member cũ.

### Collection Sessions

- [ ] **COLL-01**: Người vận hành khởi chạy các phiên thu hữu hạn bằng thao tác thủ công; không yêu cầu lịch thu tự động hoặc vận hành 24/7.
- [ ] **COLL-02**: Hệ thống đăng ký WebSocket cho các outcome/asset của danh sách market đã chọn và giữ raw nhận được để xử lý lại.
- [ ] **COLL-03**: Khi kết nối bị gián đoạn trong phiên, hệ thống tự thử kết nối lại và ghi nhận gaps; không yêu cầu lấy bù dữ liệu khoảng mất.
- [ ] **COLL-04**: Trường hợp một member không có quote mới không làm dừng việc thu các member khác; lỗi đăng ký asset nếu thực sự xảy ra phải được ghi nhận và xử lý thay vì mặc định nguồn luôn im lặng.
- [ ] **COLL-05**: Người vận hành phân biệt được các phiên và khoảng kết nối đã quan sát để xác định phạm vi dữ liệu thực thu.

### KPI and Ranking

- [ ] **KPI-01**: Researcher xem được mid-price `(best_bid + best_ask) / 2` theo từng outcome có quote hợp lệ.
- [ ] **KPI-02**: Researcher xem được spread `best_ask - best_bid` theo từng outcome có quote hợp lệ.
- [ ] **KPI-03**: Researcher xem được biến động giá có dấu `mid(T) - mid(T-W)` ở các cửa sổ W = 5, 15 và 60 phút, theo đơn vị giá chứ không tự chuyển thành phần trăm.
- [ ] **KPI-04**: Ranking có một dòng cho mỗi market/cửa sổ, theo absolute price change lớn nhất trong các outcome đủ điều kiện; không đại diện toàn Polymarket.
- [ ] **KPI-05**: Mỗi kết quả ranking giải thích được outcome tạo hạng, hướng/độ lớn biến động và cả hai mốc giá kèm thời gian.
- [ ] **KPI-06**: Quote-change count đếm số lần cặp best bid/best ask đổi trạng thái trong cửa sổ, không đếm tổng message, không gọi là trading volume và không tính snapshot khởi tạo sau reconnect là một lần thay đổi.
- [ ] **KPI-07**: Các KPI thị trường dùng event time của nguồn; event time thiếu/không hợp lệ không được âm thầm thay bằng received time; timestamp lưu theo UTC.

### Quality and Trust

- [ ] **DQ-01**: Quote thiếu bid/ask, có giá ngoài [0,1] hoặc bid > ask bị loại khỏi KPI nhưng vẫn giữ dữ liệu raw và lý do không hợp lệ ở kết quả kiểm tra.
- [ ] **DQ-02**: Kết quả thiếu hoặc không đủ điều kiện được hiển thị cùng lý do, không thay bằng 0, không backward-fill quote về trước thời điểm quan sát.
- [ ] **DQ-03**: Điều kiện hợp lệ được xét riêng từng KPI và cửa sổ; market không có outcome đủ điều kiện không có hạng ở cửa sổ đó nhưng vẫn thấy trạng thái.
- [ ] **DQ-04**: Researcher xem được coverage và gaps dựa trên khoảng quan sát; khi hai mốc giá đủ tin cậy qua gap thì kết quả phải gắn cờ gap và quote-change count trong khoảng đó phải được đánh dấu partial.
- [ ] **DQ-05**: Market không có quote mới không tự bị gắn nhãn đã đóng, mất kết nối hoặc biến động bằng 0; giao diện giữ đúng thời điểm quan sát, phân biệt tình trạng nguồn/kết nối với độ trễ xử lý và refresh.

### Views

- [ ] **VIEW-01**: Live dashboard thể hiện ranking 5/15/60 phút, các thông tin giải thích KPI và trạng thái dữ liệu/phiên; cửa sổ chưa đủ lịch sử hiển thị lý do thay vì đổi nhãn hoặc tạo số liệu giả.
- [ ] **VIEW-02**: Daily brief tổng hợp các phiên thực thu trong ngày và thể hiện pending/partial/complete cùng phạm vi quan sát; complete không được hiểu là đủ dữ liệu 24 giờ.
- [ ] **VIEW-03**: History view thể hiện giá và khoảng quan sát thực tế theo market/outcome, giữ khoảng trống ngoài phiên và khi gián đoạn, không nối hoặc zero-fill thành lịch sử liên tục giả.
- [ ] **VIEW-04**: Bối cảnh 7 ngày chỉ được cung cấp khi có đủ lịch sử thực thu theo điều kiện được thống nhất; trước đó hiển thị chưa đủ dữ liệu và khoảng đã quan sát.

### Operation and Verification

- [ ] **OPS-01**: Có thể tính lại KPI/báo cáo từ raw đã lưu khi downstream lỗi mà không cần thu lại dữ liệu nguồn; chạy lại cùng đầu vào không được làm tăng giả kết quả như quote-change count.
- [ ] **OPS-02**: Mục tiêu p95 từ lúc collector nhận dữ liệu đến lúc kết quả hiển thị là <= 60 giây trong phiên thu; chỉ xác nhận đạt khi có phép đo đầu-cuối thật.
- [ ] **OPS-03**: Trước hết phải chứng minh luồng ingest -> raw -> transform -> KPI -> dashboard/report bằng phiên thử vài phút; chỉ sau khi toàn luồng hoạt động mới chạy dài để kiểm chứng cửa sổ và freshness.
- [ ] **OPS-04**: Triển khai trên Microsoft Fabric trong tài nguyên hiện có, không mua thêm dịch vụ/capacity; ghi nhận phạm vi sử dụng tài nguyên thực tế khi kiểm chứng.

### Portfolio Evidence

- [ ] **EVID-01**: Repo cung cấp mã, định nghĩa KPI/phạm vi, sơ đồ kiến trúc đã được duyệt và hướng dẫn tái chạy phù hợp với môi trường đã kiểm chứng.
- [ ] **EVID-02**: Các Fabric item được dùng có định nghĩa native được lưu trong Git cùng bằng chứng dashboard/report demo phù hợp để giới thiệu portfolio.
- [ ] **EVID-03**: Kết quả kiểm chứng có điều kiện chạy, đầu vào/kết quả kỳ vọng, kết quả quan sát và giới hạn; tách fixture, metadata/job status và phép đo live, không coi Agent Gym hoặc lời giải thích được sinh ra là bằng chứng production.

## Acceptance Criteria

Các trường hợp dưới đây là khung kiểm chứng cho yêu cầu đã xác nhận. Không tự đặt thêm mức loss, RPO/RTO, số giờ chạy hoặc kích thước mẫu khi người dùng chưa thống nhất.

| Nhóm | Bằng chứng cần có trước khi đánh dấu hoàn thành |
|------|----------------------------------------------|
| RAW | Chứng minh filter/phạm vi đã chọn và đọc đủ trang; có trường hợp extract lỗi/page cap để xác nhận không bị gọi nhầm complete. Không lấy số lượng từ một sample làm số lượng kỳ vọng toàn nguồn. |
| WATCH | Fixture giữ member hợp lệ, bổ sung chỗ trống theo 24h volume, có ít hơn 30 ứng viên, cùng ngày không đổi danh sách và qua ngày mới cập nhật. Thuật toán chọn chi tiết được chốt ở transform trước khi test kết quả. |
| COLL | Phiên ngắn có raw, receipt time và khoảng kết nối; fixture hoặc kiểm thử được duyệt cho reconnect/gap và member không có dữ liệu. Hành vi đăng ký asset đã đóng phải được ghi theo quan sát, không giả lập thành cam kết nguồn. |
| KPI | Đối chiếu giá, spread, hai mốc, biến động có dấu, outcome tạo hạng và quote transitions với đầu vào/kết quả kỳ vọng đã định nghĩa độc lập. Kiểm thử duplicate, invalid timestamps và reconnect snapshot. |
| DQ | Thử quote lỗi, quote thiếu, thiếu cửa sổ, gap và market yên tĩnh; xác nhận trạng thái/lý do, timestamp và partial thay vì số 0 hoặc độ mới giả. |
| VIEW | Demo ba đầu ra trên cùng phạm vi quan sát; history không che gaps, daily complete không hứa 24h, thiếu dữ liệu không được gắn nhãn bối cảnh đủ 7 ngày. |
| OPS | Chứng minh replay cùng đầu vào không nhân đôi kết quả; sau khi toàn luồng chạy được, thực hiện phiên dài được duyệt và đo received-to-visible p95 theo protocol đã thống nhất. |
| EVID | Reviewer mở được mã, định nghĩa native, hướng dẫn, kết quả kiểm chứng và demo; các claim giới hạn đúng theo điều kiện chạy thực tế. |

## Decisions Needed During Planning

Các mục này không phải yêu cầu đã khóa. GSD planning phải trình bày quyết định cụ thể trước khi implementation phụ thuộc vào chúng.

| Điểm cần chốt | Ranh giới đã xác nhận | Thời điểm phải giải quyết |
|--------------|---------------------|--------------------------|
| Tag AI, endpoint/filter và nhận biết đầy đủ | Raw metadata gồm toàn bộ market đang mở thuộc tập tag AI, không lọc top 30 ở ingest | Trước thay đổi ingest |
| Phân loại AI trực tiếp và chọn member | Tự động; giữ member hợp lệ và điền chỗ trống theo volume 24h; cách chọn nằm ở transform | Trước triển khai transform watchlist |
| Field/đơn vị/null/tie của volume, market ngừng giao dịch | Không dùng volume tích lũy thay 24h, không suy closure từ WebSocket im lặng | Trước acceptance watchlist |
| Anchors và tie/out-of-order của KPI | Event time, không backward-fill; hai mốc phải giải thích được | Trước triển khai window KPI |
| Coverage và ngưỡng đủ lịch sử 7 ngày | Phản ánh quan sát thật và công bố gaps; không yêu cầu phải chạy 7 ngày mới demo được v1 | Trước nghiệm thu history/context |
| Nhãn delayed và retry exhaustion | Không gọi market yên tĩnh là stale chỉ do không có quote; tự reconnect trong phiên | Trước nghiệm thu vận hành/giao diện |
| Daily brief: ngày, định dạng, thời điểm phát hành | Daily watchlist theo Asia/Saigon đã chốt; ranh giới ngày của brief chưa được xác nhận riêng | Trước triển khai daily brief |
| Retention và giữ bằng chứng trước hết trial | Chỉ dùng tài nguyên hiện có, chưa có yêu cầu giữ raw vô thời hạn | Trước vận hành hoặc policy xóa |
| Protocol đo freshness | p95 <=60 giây từ receipt tới hiển thị; thời lượng, sample và tiêu chí đo chưa chốt | Trước phiên đo dài |
| Component/runtime/schema/deployment | Fabric bắt buộc; architecture research là ứng viên | Trong phase planning sau khi duyệt requirements |

## Definition of Done

Một yêu cầu chỉ hoàn thành khi có implementation, bằng chứng kiểm chứng phù hợp và mã/định nghĩa được lưu trong Git. Chưa có yêu cầu v1 nào được đánh dấu đạt.

Mốc demo ngắn chứng minh toàn luồng và cách hiển thị chưa đủ dữ liệu. Mốc nghiệm thu cuối phải có bằng chứng thật cho cửa sổ được tuyên bố và mục tiêu freshness; fixture không thay phép đo live. Không có tiêu chí giảm thời gian nghiên cứu hoặc phần trăm cải thiện chưa từng đo.

Việc đã có item hoặc job Completed trong workspace cũ chỉ là đầu vào đánh giá tái sử dụng. Không xem raw ingest, KPI hoặc báo cáo của dự án mới đã hoàn tất từ các quan sát đó.

## v2 Requirements

- **SCALE-01**: Mở rộng sức chứa watchlist sau v1; chưa có số lượng mục tiêu, sizing hoặc cam kết hiệu năng được thống nhất.

Không tự đưa các tính năng khác vào v2 nếu người dùng chưa yêu cầu.

## Out of Scope

| Feature | Reason |
|---------|--------|
| Lịch sử trước lúc bắt đầu thu và external backfill | Chỉ tích lũy từ phiên thực thu |
| Thu liên tục 24/7 | Phiên hữu hạn khởi chạy thủ công |
| Polling trạng thái trong ngày, preflight từng phiên và thay member ngay | Người dùng chọn daily batch quản lý trạng thái/membership |
| Giới hạn cố định top 3 market để nghiên cứu | Chưa khóa số lượng này |
| Giao dịch, khuyến nghị đầu tư, bất thường đã xác thực hoặc giải thích bằng tin tức | Sản phẩm mô tả/xếp hạng biến động |
| Bao phủ toàn Polymarket | Phạm vi phân tích là watchlist đã công bố |
| Mua thêm capacity/dịch vụ hoặc thêm triển khai thứ hai | Chỉ dùng tài nguyên hiện có |
| Tạo resource, deploy hoặc chạy cloud job trong initialization | Chưa có phê duyệt thao tác tương ứng |
| Sửa Hub/skills/GSD Core tự động | Hub hiện chỉ đọc; ghi bài học dự án cục bộ, không sửa synthesized Wiki |

## Traceability

Chưa có ROADMAP được duyệt. Mỗi ID v1 sẽ được gán đúng một phase chịu trách nhiệm sau khi người dùng duyệt requirements và cấu trúc dự án; không tự gán phase từ nghiên cứu kiến trúc.

| Category | Requirement IDs | Phase | Status |
|----------|-----------------|-------|--------|
| Raw metadata | RAW-01..RAW-03 | Chưa gán | Pending |
| Watchlist | WATCH-01..WATCH-05 | Chưa gán | Pending |
| Collection | COLL-01..COLL-05 | Chưa gán | Pending |
| KPI/ranking | KPI-01..KPI-07 | Chưa gán | Pending |
| Quality | DQ-01..DQ-05 | Chưa gán | Pending |
| Views | VIEW-01..VIEW-04 | Chưa gán | Pending |
| Operation | OPS-01..OPS-04 | Chưa gán | Pending |
| Evidence | EVID-01..EVID-03 | Chưa gán | Pending |

**Coverage:** 36 yêu cầu v1; 0 đã gán phase; 36 chờ bước roadmap. Không có yêu cầu được đánh dấu Complete.

## Knowledge Sources

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/discovery.md` — phân biệt field metadata với business contract và hoàn tất pagination.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/realtime-quotes.md` — phân biệt snapshot, delta, quote state và heartbeat; không suy source guarantee chưa thử.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/event-stream-processing/guidance.md` — chọn cửa sổ/đồng hồ theo câu hỏi phân tích, không theo công cụ.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-source-assessment/gotchas.md` — sample hoặc kết nối thành công không chứng minh completeness/recovery.

Nguồn yêu cầu chính là các quyết định người dùng đã xác nhận và `.planning/PROJECT.md`. Tài liệu business được cung cấp tại `C:/Users/sanbo/Code/data-engineer/docs/01-business-problem.md` là tham chiếu lịch sử; xác nhận mới nhất có ưu tiên. Research tại `.planning/research/` cung cấp trade-offs và gaps, không tự phê duyệt tính năng, component hoặc contract.

Upstream SDK/API và Fabric item hiện có sẽ được đánh giá tái sử dụng trước custom code; chưa chọn package version hoặc runtime từ tài liệu yêu cầu này. Tài liệu nguồn kỹ thuật phải được xác minh lại khi trở thành dependency triển khai.

---
*Last updated: 2026-10-21 after explicit user approval of all 36 v1 requirements. Roadmap creation is next; planning decisions remain open as listed above.*
