# 02 — Identify & Evaluate Data Sources

> **Dự án:** Polymarket Market Intelligence  
> **Ngày research:** 2026-10-13–2026-10-14  
> **Ngày tổng hợp và xác nhận:** 2026-10-14  
> **Trạng thái:** Hoàn tất đánh giá/review nguồn theo phạm vi khảo sát; không phải chứng nhận nguồn đáp ứng toàn bộ KPI hoặc mọi điều kiện vận hành.  
> **Business authority:** [01 — Business Problem & Requirements v1.1](01-business-problem.md).  
> **Ranh giới:** Chỉ ghi lại research, evidence, source assessment và quyết định review. **Chưa thiết kế ingestion**, chưa chọn collector/cadence/storage/engine/Fabric items, chưa triển khai cloud và chưa cho phép tải bulk.

## 1. Mục tiêu, phạm vi và cách đọc

Bước này trả lời: nguồn nào cung cấp metadata, quotes trong phiên và historical context; dữ liệu có chất lượng/semantics gì; ai sản xuất hoặc phân phối; reliability đã được kiểm đến đâu; giới hạn nào phải giữ khi sử dụng.

Không yêu cầu nghiên cứu đủ mọi loại databases/APIs/SaaS/files/streams nếu dự án không dùng chúng. Các nguồn thực tế ở đây là **public REST APIs, WebSocket stream và Parquet archives**.

### Nhãn bằng chứng

- **Stated:** business contract, tài liệu/schema hoặc dữ liệu quan sát được nói điều này. Kết quả probes chỉ đúng cho mẫu/thời điểm được ghi.
- **Inferred:** suy luận từ evidence, nêu cơ sở; không là guarantee.
- **Synthesis:** tổng hợp/khuyến nghị của dự án, không tự trở thành fact hoặc source SLA.
- **Open:** chưa có đủ evidence hoặc chưa chốt chi tiết.
- **Hypothesis:** giả thuyết được dùng để thử/giải thích mẫu, chưa được xác minh thành quy tắc nguồn.
- **Quyết định đã xác nhận:** lựa chọn của người thực hiện trong source review ngày 2026-10-14; chỉ có hiệu lực trong phạm vi được nêu ở mục 8.

Numeric validity, identity, time interpretation, state trust, coverage, freshness và publication eligibility là những khía cạnh khác nhau. Không gộp thành một `source_valid=true` cho mọi market/KPI.

### Cơ sở wiki đã đối chiếu

Đã đọc [index](../../wiki/index.md), [Know the Source System](../../wiki/principles/know-the-source-system.md), [Data Quality](../../wiki/concepts/data-quality.md), [Data Definition and Data Logic](../../wiki/concepts/data-definition-and-data-logic.md), [Types of Time](../../wiki/frameworks/types-of-time.md), [Late-Arriving Data](../../wiki/concepts/late-arriving-data.md) và [SLA and SLO](../../wiki/concepts/sla-and-slo.md).

**Stated — wiki:** cần hiểu cách nguồn tạo dữ liệu; quality xét theo định nghĩa business; event/ingestion/process time khác nhau; tiêu chuẩn late data cần được quyết định, không có ngưỡng số dùng sẵn cho dự án. Wiki là nền tảng tư vấn, không là project approval hoặc thỏa thuận với upstream. Không sửa wiki hoặc gọi nguồn mới để tạo bản tổng hợp này.

## 2. Kết luận và vai trò nguồn

| Nguồn / interface | Producer/publisher theo evidence | Vai trò được chấp nhận trong source review | Đánh giá và giới hạn |
|---|---|---|---|
| Gamma public REST | Polymarket | Discovery, rules/metadata, lifecycle, event–market–outcome mapping | Có bounded discovery/detail evidence. Tags/keywords và `active=true` không tự xác định watchlist/live eligibility. |
| CLOB public market WebSocket | Polymarket | Quote observations chính trong các phiên đã khai báo | Subscription, initial books, heartbeat, positive top changes đã quan sát được. Chưa chứng minh completeness, replay, source SLA hoặc 24/7. |
| CLOB public REST book | Polymarket | Snapshot/reference checks; ứng viên initialization/resync | 18/18 outcomes có books hợp lệ trong mẫu. Snapshot không là lịch sử changes hoặc khôi phục path qua gap. |
| Pendulum Flow V3 | Pendulum Flow, capture/merge theo publisher | **Historical candidate ưu tiên** cho recent context | Có recent file metadata, joint-ID quotes và independent native decode. Chưa chứng minh toàn watchlist/bảy ngày hay continuous state. |
| PMXT original archive v2 | PMXT | Historical candidate ban đầu trong business document; hiện còn Open | Docs overview refused/timeout trong attempts đã thử. Không suy ra toàn upstream ngừng thu hoặc mất dữ liệu mới. |
| PMXT v2 qua Pendulum mirror | PMXT theo lời mirror; Pendulum phân phối | Chỉ cân nhắc old-period context nếu được review riêng | Mirror đã kiểm tra không phù hợp recent window tháng 10/2026. Chưa decode target rows; không recent fallback tự động. |
| Polymarket price-history API | Polymarket | Candidate bổ trợ theo định nghĩa riêng, chưa chọn | Có tham khảo documentation nhưng chưa execution-validate trong các probes lịch sử. Không tự thay historical midpoint/spread/count. |

**Synthesis:** có đủ evidence để kết thúc bước Identify & Evaluate ở mức source assessment và chấp nhận vai trò nguồn. Không cần tiếp tục probes vô hạn để ép ra completeness/SLA chưa được cung cấp. Những phần Open là giới hạn của kết quả đánh giá, không bị che bằng zero fill hoặc thêm positive samples.

## 3. Research register và bằng chứng giữ lại

Tài liệu này gom kết luận của các reports trước; raw responses/captures, manifests, profiles, native fixtures, outputs và các bản chụp tài liệu nguồn đi kèm có tại [data/research/](../data/research/); scripts ở [docs/research/scripts/](research/scripts/). Các gói bằng chứng được sao chép nguyên vẹn, không sửa nội dung. Đường dẫn cũ ghi bên trong manifests/profiles là provenance tại thời điểm khảo sát; khi tra dữ liệu ở vị trí mới, ánh xạ prefix `docs/research/` sang `data/research/` (không áp dụng cho scripts). Không coi ngày viết bản tổng hợp là freshness mới của dữ liệu.

| Vòng | Thời điểm / phạm vi | Stated — kết quả chính | Evidence local |
|---|---|---|---|
| R01 | 2026-10-13, khoảng 15:08:45–15:10:15 UTC; keyword/detail discovery | Keywords trả ngoài scope; open events chứa closed markets; event khác market grain | **Không lưu raw vòng 1.** Bốn candidates được đọc lại ở R02; không giả chúng là snapshot R01. |
| R02 | 2026-10-13, 15:23:28–15:25:50 UTC; Gamma tag/search/detail | 20 HTTP successes; tag pages có 387 distinct markets, 276 qua preliminary state gate; 9 candidates/18 outcomes | [Raw + manifest](../data/research/gamma-metadata/2026-10-13-round-2-mbzjqacc/), [profile](../data/research/gamma-metadata/round-2-profile.json), [candidates](../data/research/gamma-metadata/round-2-candidates.csv) |
| R03 | 2026-10-13, 15:43:25–15:44:47 UTC; Gamma preflight/CLOB REST | 33 HTTP 200; 18/18 IDs/books khớp, two-sided/non-crossed; 814/814 levels qua numeric checks; 6/6 midpoint/spread comparisons khớp | [Raw + manifest](../data/research/clob-feasibility/2026-10-13-round-3-dcp3q7ys/), [checks](../data/research/clob-feasibility/round-3-outcome-checks.csv), [profile](../data/research/clob-feasibility/round-3-profile.json) |
| R04 | 2026-10-14, 01:43:18.535–01:45:40.553 UTC; 2 sessions × 65s, 4 outcomes | Initial books, 12 PONG; 141 price_change events nhưng top không đổi; controlled gap 11.335s; preflight thấy 5408226 đã đóng | [Stopped preflight](../data/research/websocket-feasibility/2026-10-14-round-4-6xk09lcn/), [capture + manifest](../data/research/websocket-feasibility/2026-10-14-round-4-npl164xz/), [profile](../data/research/websocket-feasibility/round-4-profile.json) |
| R05 | 2026-10-14, 01:59:40.171–02:04:42.854 UTC; một session 300s, 6 outcomes | 82 BBA/positive outcome transitions; 29 PONG; SDK hỗ trợ epoch-ms; related BBA trước matching delta trong mẫu | [Raw capture](../data/research/stream-semantics/2026-10-14-round-5-hn6sp9mn/), [pinned refs](../data/research/stream-semantics/2026-10-14-reference-rvkkb7em/), [profile](../data/research/stream-semantics/round-5-profile.json), [quote trace](../data/research/stream-semantics/round-5-quote-trace.csv) |
| R06 | 2026-10-14; PMXT upstream attempts, mirror listing/HEAD/ranges/footer | Recent temporal mismatch; checksum entry có thể HEAD 404; actual Range/footer khả thi, **chưa decode data rows** | [Evidence + ledger](../data/research/historical-feasibility/2026-10-14-round-6-zejno6um/) |
| R07 | 2026-10-14; V3 recent-hour metadata và một projected BBA group | 150 checksum entries + 18 missing-list objects HEAD 200; 60 HLE joint-ID rows bằng research decoder | [Evidence + profiles](../data/research/v3-feasibility/2026-10-14-round-7-nsfgb97g/) |
| R08 | 2026-10-14; DuckDB independent check và 3 fixed sample hours | 60-row sample khớp exact/zero field mismatches; BBA matches theo sampled hours là 0/8/60 | [Evidence + engine outputs](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/) |
| R09 | 2026-10-14; HLE 3072353, 2026-10-07T00 UTC | 0 matching book rows nhưng 446 price_change rows có valid top quotes; chưa đủ interval trust | [Evidence + baseline profile](../data/research/v3-baseline/2026-10-14-round-9-66628vd0/) |

### Đo bytes trong các probes

| Phạm vi đo | Bytes được báo cáo | Giới hạn |
|---|---:|---|
| R02 Gamma bodies | 2,864,226 | Response bodies, không wire/account-wide traffic |
| R03 REST bodies | 74,656 | 33 runtime API requests; documentation transfers tách riêng |
| R04 WebSocket payload | 133,924 | Received application payload, không TCP/TLS/control-frame capture |
| R05 WebSocket payload | 430,672 | Received application payload; không publication-latency test |
| R06 instrumented bodies | 1,187,579 | 15 attempts: 13 responses 2xx, một HEAD 404, một upstream GET timeout; 214,091 bytes là Parquet prefix/tail/footer |
| R07 V3 bodies | 4,795,313 | 35 attempts, tất cả 2xx; gồm metadata/footer/projected chunks, cap 8,000,000 |
| R08 V3 new-source bodies | 7,789,138 | 24 requests; cached hour không tải lại; cap 8,000,000 giữ nguyên |
| R09 V3 new-source bodies | 5,947,975 | 14 GET ranges 206; cached metadata không transfer mới; cap 8,000,000 giữ nguyên |

Các scopes trên khác nhau, **không cộng thành một daily meter hoàn chỉnh** hoặc extrapolate thành chi phí vận hành. Research requests dùng body counters/Accept-Encoding identity, không gồm đầy đủ TLS/TCP, unknown prefetch hoặc traffic của services/sessions khác. DuckDB package được CLI báo khoảng 11.8 MiB, ghi riêng; không phải precise wire measurement.

## 4. Gamma metadata — assessment và data example

### 4.1 Discovery, business semantics và identity

**Stated — mẫu R01/R02:**

- Tag AI ID `439`; hai tag pages có 40 event IDs và 387 distinct market IDs. Tổng pages/searches có 51 distinct event IDs; pagination chưa đến cuối, searches còn `hasMore=true`.
- 387 nested markets là v1; arrays outcomes/token IDs parse được, chiều dài khớp. Trong đó 276 qua state gate, 18 closed và 93 inactive/non-order-accepting dù event chưa đóng.
- 327/387 `resolutionSource` rỗng; `description` có thể vẫn chỉ rõ resolution source. Không tự loại chỉ vì structured field rỗng.
- Direct detail không luôn có event-association fields; membership lấy từ nested event/discovery, không suy missing thành không có event.
- Metadata lifecycle times không phải quote event time. Rules do nguồn nêu không là xác minh độc lập các sự kiện ngoài thị trường.

**Synthesis:** tags/keywords chỉ tìm candidates; review question/description/rules ở cấp market. Preliminary state gate của khảo sát gồm active, not closed, not archived, acceptingOrders, enableOrderBook; missing flags là unknown. Passing gate chưa chứng minh two-sided quotes hoặc continuity.

**Stated — docs:** v1 dùng `clobTokenIds`, v2 dùng `positionIds`. Actual v2 markets chưa được thử; missing/unsupported version không được mặc định v1. IDs không dùng floating point.

### 4.2 Data example — selected fields từ response thật

[Raw market 3072353](../data/research/gamma-metadata/2026-10-13-round-2-mbzjqacc/market-3072353.json), [manifest](../data/research/gamma-metadata/2026-10-13-round-2-mbzjqacc/manifest.json): GET lúc `2026-10-13T15:25:41.199783+00:00`, completion `15:25:41.677257+00:00`, HTTP 200.

```json
{
  "id": "3072353",
  "conditionId": "0x2b071ab38aad8c77a564168ea9d098df986c80d4642af4c002b300bd703c8163",
  "version": "v1",
  "outcomes": "[\"Yes\", \"No\"]",
  "clobTokenIds": "[\"98702525727507873090971630240388903632041189534811728534540010314099930030441\", \"56907067907293748413568175622799179363128473527944796726773537650077042437524\"]",
  "active": true,
  "closed": false,
  "archived": false,
  "acceptingOrders": true,
  "enableOrderBook": true,
  "createdAt": "2026-07-23T22:52:56.571827Z",
  "resolutionSource": "https://agi.safe.ai/"
}
```

Đây là **excerpt**, không toàn response. `outcomes` và `clobTokenIds` trong raw là **JSON strings**, không phải arrays trực tiếp. Index 0=Yes, index 1=No sau parse. Event ID `740958` lấy từ discovery, không thêm vào excerpt như field của direct payload. Không chép field question vì raw có replacement character; không tự sửa chữ rồi gọi là verbatim. Flags chỉ là trạng thái lúc đọc, không giá trị hiện tại.

### 4.3 Candidate set — không phải approved watchlist

| Event ID | Market ID | Nội dung review ngắn | Giới hạn |
|---|---|---|---|
| 841230 | 3554657 | Anthropic Arena Text ranking cuối tháng 10 | Check time/config, loại AutoEval, tie/fallback rules; không “best AI mọi tiêu chí” |
| 841230 | 3554659 | Google Arena Text ranking cùng event | Computation/outcomes riêng từng market |
| 740958 | 3072353 | OpenAI HLE Accuracy ≥60% trong năm 2026 | Accuracy, không Calibration Error hoặc benchmark khác |
| 663848 | 2787890 | Chính sách Mỹ loại bỏ public access model AI Trung Quốc | Phải thực sự loại quyền truy cập theo rules, không chỉ thông báo/mất một kênh |
| 1149140 | 5408226 | Gemini Argon public availability | **Preflight R04 đã closed=true**, acceptingOrders=false, closedTime `2026-10-13 18:14:27+00`; không chọn mới cho live |
| 1156045 | 5443473 | Haiku 5.6+ public availability | Naming/version/public access rules, không chỉ announcement |
| 1150926 | 5419351 | Mistral Large 4.1+ public availability | Không tính rollout phiên bản cũ |
| 1124074 | 5230698 | Dots free access | Paid-plan free trial/promotion không đủ |
| 1121379 | 5219371 | Gemini Argon HLE debut Accuracy ≥55% | Check time sau lần đầu xuất hiện đủ điều kiện; khác release/highest-in-year market |

Chín candidates có 18 outcomes ở R02. Không lấp đủ 20, không áp event quota chưa được duyệt. [Candidate CSV](../data/research/gamma-metadata/round-2-candidates.csv) giữ IDs/provenance và lịch sử trạng thái; không sửa thành cấu hình live.

Các review exclusions đáng lưu: `1345229` IPO, `5457258` TSM earnings, `5347188` data-center moratorium chung ngoài scope AI trực tiếp; `5420046` domain/branding chưa chứng minh phù hợp; `4412200` trigger công bố của OpenAI không bắt buộc AI tự giải; `5635905` disclosure time khác incident time; `3072351` đã đóng và `5598611` không qua state gate. Không diễn giải các rules này thành sự kiện bên ngoài đã được xác minh.

## 5. CLOB — REST và WebSocket assessment/data examples

### 5.1 REST snapshot

**Stated — R03:** fresh Gamma mappings và books khớp cho 18 outcomes. Requests tuần tự, không tạo snapshot nguyên tử cho toàn tập. Price/size parse bằng Decimal; best bid=max validated bids, best ask=min validated asks.

**Documentation conflict:** captured OpenAPI mô tả bids descending/asks ascending; guide, payloads và SDK đã đọc mô tả bids ascending/asks descending. Không dựa implicit first/last element để tìm best quote.

[Raw HLE Yes book](../data/research/clob-feasibility/2026-10-13-round-3-dcp3q7ys/book-3072353-0.json), [manifest](../data/research/clob-feasibility/2026-10-13-round-3-dcp3q7ys/manifest.json): request `2026-10-13T15:43:34.961052+00:00`, completion `15:43:35.475470+00:00`, HTTP 200.

Selected source fields:

```json
{
  "market": "0x2b071ab38aad8c77a564168ea9d098df986c80d4642af4c002b300bd703c8163",
  "asset_id": "98702525727507873090971630240388903632041189534811728534540010314099930030441",
  "timestamp": "1791906209822",
  "hash": "3bad5ef7e6126f8dc98fded1b6edd26043e5d672",
  "last_trade_price": "0.610"
}
```

Các level thật được chọn từ arrays bằng max/min; không phải source fields tên best_bid/best_ask:

| Array | Selected price | Selected size |
|---|---:|---:|
| bids | `"0.37"` | `"667"` |
| asks | `"0.38"` | `"10.1"` |

**Derived:** midpoint=0.375; spread=0.01, đơn vị giá. `last_trade_price=0.610` không thay midpoint. REST snapshot không cấp price-change history, count hoặc continuity.

**Stated — wide-spread samples:** Mistral 5419351 spread=0.27, Gemini benchmark 5219371 spread=0.17 trên cả hai outcomes; không tự invalid khi chưa duyệt threshold. Guide nêu UI có thể fallback last trade khi spread>0.10, nhưng không kiểm UI thực tế. Gemini release 5408226 từng có Yes mid=0.983 nhưng `last_trade_price="0.035"` ở cả hai outcomes; semantics/nguyên nhân còn Open, không kết luận nguồn sai hoặc tự complement.

### 5.2 Stream shapes, ordering và baseline

**Stated — R04/R05:** initial snapshots có thể là JSON array; updates là objects, price_change có multiple asset rows. Lifecycle `new_market` có thể ngoài subscribed universe. Một transport message không luôn là một event/outcome; local message index chỉ receipt order, không venue sequence.

R04 có 8 initial book events, 282 per-asset price_change rows, zero BBA, quote top giữ nguyên trong captured observations. 12 PONG chỉ connection-level liveness. Controlled gap từ stop request đến baseline mới là 11.335s, không source outage; snapshot sau reconnect không trả path qua gap. Quiet outcome không tự là broken/stale hoặc complete zero-count.

R05 có 6 book baselines, 1,126 price_change rows, 82 BBA, 112 size-zero rows và 18 lifecycle events ngoài universe. Diagnostic có 82 outcome-level transitions sau hợp nhất repeated same-state representations; **không cộng thành 164**, không gộp Yes/No thành market count hoặc business complete KPI.

### 5.3 Data example — một transition, hai biểu diễn

[Raw R05 messages](../data/research/stream-semantics/2026-10-14-round-5-hn6sp9mn/session-1-messages.jsonl), [evidence manifest](../data/research/stream-semantics/2026-10-14-round-5-hn6sp9mn/evidence-manifest.json), [derived observations](../data/research/stream-semantics/round-5-event-observations.csv). Ví dụ HLE 3072353 **Yes**, cùng mapping ở mục 4.2:

| Message | Observed UTC ngày 2026-10-14 | Source timestamp raw | Event | Bid / Ask | Derived mid |
|---|---|---|---|---|---:|
| 1 | 01:59:41.983Z | `1791943179229` | book trong initial array 6 books | 0.37 / 0.41 | 0.390 |
| 4 | 01:59:46.998Z | `1791943185373` | best_bid_ask | 0.36 / 0.41 | 0.385 |
| 6 | 01:59:46.999Z | `1791943185373` | price_change chứa Yes và No | 0.36 / 0.41 | 0.385 |

Selected item thật trong `price_changes` của message 6:

```json
{
  "asset_id": "98702525727507873090971630240388903632041189534811728534540010314099930030441",
  "price": "0.37",
  "size": "0",
  "side": "BUY",
  "hash": "8e64ae86f017e86d55aebbbdc27a11c89e619e35",
  "best_bid": "0.36",
  "best_ask": "0.41"
}
```

**Stated — official guide:** size=0 xóa level. **Inferred từ mẫu:** BBA và matching delta diễn tả cùng new top, không phải hai changes. Không suy BBA luôn phải đến trước ở mọi lần.

### 5.4 Semantics đã được hỗ trợ và phần chưa có guarantee

[Official SDK/guide snapshots](../data/research/stream-semantics/2026-10-14-reference-rvkkb7em/): ts-sdk commit `efbc09bd7c0e652ce2c2e3632b9c8dc51505d864`; agent-skills commit `91ee44ae113e958affd20cd505c6e9d9d6100e0b`.

- **Stated — SDK:** REST/book/price_change/BBA timestamps dùng epoch-ms schema. Empty-string optional best_bid/best_ask normalize thành null, không price zero. Timestamp có thể optional/null trong event schemas.
- **Open:** server ghi time tại generation/update/broadcast, clock sync, reliability/completeness hoặc source-event latency. Unit evidence không giải hết các câu hỏi này; giữ labels Hypothesis của R03/R04 cũ.
- **Hypothesis — depth replay experiment:** size>0 như absolute replacement, gom consecutive same-asset timestamp/hash rows. Per-message replay có 2 temporary mismatches; grouped replay 1,066/1,066 top comparisons khớp, 54 groups qua nhiều messages. [Initial profile](../data/research/stream-semantics/round-5-depth-replay-profile.json) và [grouped profile](../data/research/stream-semantics/round-5-grouped-depth-profile.json) đều giữ lại.
- **Open:** timestamp/hash không là validated unique event ID, transaction/completion boundary hoặc watermark; khác depth updates có thể dùng cùng các giá trị. Top matches không chứng minh positive-size/full-depth semantics. Không gọi supplied top quote là full-depth reconstruction.
- No source regressions/exact duplicates trong mẫu không là always-ordered/exactly-once guarantee. Session 300s không chứng minh đúng event-time anchors 5m hoặc mục tiêu publish trong một phút.

## 6. Historical sources — assessment và data examples

### 6.1 PMXT original và v2 mirror

**Stated — R06:** upstream overview `https://archive.pmxt.dev/docs/v2-data-overview` connection refused qua WebFetch và timeout qua instrumented GET; không đọc được nội dung original. Pendulum mirror công bố 2,834 files, 2026-04-13T19–2026-08-09T23 UTC. Checksum list có 2,835 entries, bao gồm 2026-08-10T00 nhưng entry đó HEAD 404; hourly span thiếu 2026-06-11T04/T05/T06.

**Inferred:** 7/9 candidates tạo sau advertised archive end. HLE 3072353 và regulation 2787890 có temporal possibility với khoảng cũ, chưa chứng minh target rows. Recent October window không giao với mirror period đã kiểm; tăng budget không giải temporal mismatch.

#### Data example — footer thật, không fabricated quote row

Object kiểm tra: `pmxt/v2/polymarket_orderbook_2026-08-09T23.parquet`; HEAD Content-Length=507,501,824 bytes. Prefix/tail/footer ranges trả 206; không full GET hoặc decode data pages.

[Footer profile](../data/research/historical-feasibility/2026-10-14-round-6-zejno6um/parquet-footer-profile.json), [footer binary](../data/research/historical-feasibility/2026-10-14-round-6-zejno6um/parquet-footer.bin), [ledger](../data/research/historical-feasibility/2026-10-14-round-6-zejno6um/transfer-ledger.json). Selected **decoded metadata/profile fields**, không source event payload:

```json
{
  "footer_bytes": 214079,
  "footer_consumed_bytes": 214079,
  "parquet_version": 2,
  "created_by": "parquet-cpp-arrow version 24.0.0",
  "row_count": 85033410,
  "row_groups": 82,
  "received_timestamp_min_utc": "2026-08-09T23:00:00.073000+00:00",
  "received_timestamp_max_utc": "2026-08-09T23:59:59.858000+00:00"
}
```

| Field | Physical/logical schema trong footer |
|---|---|
| timestamp_received / timestamp | INT64 UTC TIMESTAMP(MILLIS) |
| market | FIXED_LEN_BYTE_ARRAY(66), condition-ID representation cần kiểm row values |
| event_type / asset_id | BYTE_ARRAY STRING |
| bids / asks | Nullable STRING chứa JSON depth, không nested Parquet lists |
| best_bid / best_ask / price | DECIMAL(9,4) |
| size | DECIMAL(18,6) |

Schema không có raw hash hoặc sequence; không tái áp grouping timestamp/hash của live. `timestamp_received` theo mirror là exporter receive time, không project download time. Null fields theo event type không tự là bad data.

**Chưa có decoded PMXT data row trong probes này.** Footer có rows không chứng minh market membership. Min/max bounds từng giữ cả IDs của markets chưa tồn tại lúc archive; range contains không là exact match. Full-file SHA và lời mirror “byte-identical PMXT” chưa được kiểm độc lập.

**Hypothesis — projected bytes từ một footer:** khoảng 7,574,469 chunk bytes cho hai possible markets, 33,939,978 cho chín predicates/giờ mẫu, cộng footer. Giả định 168 giờ giống nhau cho ra khoảng 1.31/5.74 GB; không là actual week benchmark hoặc daily transfer requirement.

### 6.2 Pendulum Flow V3 — nguồn khác PMXT v2

**Stated — R07:** cửa sổ khảo sát minh họa `[2026-10-07T00:00Z, 2026-10-14T00:00Z)` có 150 Parquet checksum entries; 18 giờ cuối thiếu entry nhưng direct HEAD đều 200. Đây là union availability metadata cho 168 giờ, **không HEAD cả 168**, không verify mọi file/per-market coverage và không historical period đã khóa.

File 2026-10-13T23 có HEAD length=1,548,932,856 bytes; footer=1,562,650 bytes, 137,353,056 rows, 529 groups. Chỉ tải chín columns của BBA group 7, tổng chunks=2,519,438 bytes; đọc 262,144 group rows, tìm 60 joint matches cho HLE: 30 Yes/30 No, numeric valid/spread đúng trong mẫu.

| Nội dung | V3 physical/logical schema đã đọc | Khác biệt cần giữ |
|---|---|---|
| timestamp_received | UTC TIMESTAMP(MICROS) | Archive collector clock; khác source ms và project receipt |
| timestamp | UTC TIMESTAMP(MILLIS) | Semantic instant/clock assurance vẫn Open |
| sequence | UINT64 | Exact integer/text khi export, không float/global venue order |
| market / asset_id | Binary; sample IDs 32 bytes | Condition hex→raw bytes; outcome decimal uint256→32-byte big-endian, không cast binary trực tiếp sang text |
| best_bid / best_ask / spread | DECIMAL(9,4) | Exact decimals; missing khác zero |
| bids / asks | Nested list/struct | Không parser JSON strings như v2 |
| witness_set / arrival_skew / source_witness | Capture/merge provenance | Không local project session hoặc venue sequencing |

Theo publisher, physical layout/sequence là capture/merge/collector-local, không một global socket receipt order. V3 không có raw event hash field. Product byte_range là half-open; HTTP Range inclusive dùng end−1; segments không mặc định là standalone Parquet files.

#### Data example — row được native engine kiểm độc lập

[Native matched CSV, dòng dữ liệu đầu](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/compact-fixture/duckdb-matched-quotes.csv), [provenance](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/compact-fixture/derived-fixture-provenance.json), [engine profile](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/compact-fixture/duckdb-validation-profile.json). Source hour 2026-10-13T23, BBA group 7:

| Exported field | Exact value | Loại thông tin |
|---|---|---|
| subset_row_number | 254670 | Row position trong projected group |
| market_id / outcome_label | 3072353 / No | Project mapping, không raw source fields |
| condition_id | 0x2b071ab38aad8c77a564168ea9d098df986c80d4642af4c002b300bd703c8163 | Decoded từ binary source ID |
| trading_id | 56907067907293748413568175622799179363128473527944796726773537650077042437524 | Decoded exact uint256; không float |
| timestamp_received_raw_us | 1791932746917569 | Source collector receive timestamp, microseconds |
| timestamp_raw_ms | 1791932746912 | Source timestamp, milliseconds |
| sequence | 11821949387386119 | Source UINT64; không global order |
| best_bid / best_ask | 0.5600 / 0.6300 | Source exact decimals |
| midpoint | 0.5950 | Derived |
| spread | 0.0700 | Source spread đã tải và đối chiếu ask−bid trong cached sample |
| numeric_validity | True | Derived numeric check, không publication approval |

**Stated — R08 independent check:** DuckDB 1.4.3 đọc unchanged compressed pages qua compact projected fixture, không custom-decode/re-encode cells. Fixture 2,520,232 bytes; 60 matches khớp exact IDs, row positions, timestamps, sequence và decimals, zero mismatches. Đây là native sample decoding, không full original-hour SHA, capture completeness hoặc production remote-reader benchmark. Tool tạm được phép, không production-engine approval.

Fixed-hour experiment cho HLE có BBA matches: 2026-10-07T00=0; 2026-10-10T12=8; 2026-10-13T23=60. Không biến ba sampled hours thành continuous bảy ngày. Projection ban đầu dự kiến 8,101,460 bytes vượt 8 MB nên dừng trước column requests; bỏ source spread của hai mẫu mới, giữ market/hours/cap và derive spread để đạt 7,789,138 bytes. [Stopped attempt](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/fixed-hour-capture-profile.json), [continuation](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/projection-continuation-profile.json) đều được giữ. Không claim validate omitted spread field.

Sparse-fixture local attempt cũng bị allocation guard chặn trước decode; [failed profile](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/duckdb-validation-profile.json) khác native pass trong compact-fixture. Local fixture issue không phải source failure.

#### Data example bổ sung — zero BBA nhưng vẫn có quote observations

**Stated — R09, cùng market/giờ 2026-10-07T00:** native book identity check trên toàn 169,584 book rows trả zero target matches; không tải nested depth sau đó. Price_change candidate group có 446 matching quote-bearing rows, 223/outcome:

| Outcome | Observed rows | Best bid / ask | Derived midpoint / spread |
|---|---:|---|---|
| Yes | 223 | 0.4600 / 0.4900 | 0.4750 / 0.0300 |
| No | 223 | 0.5100 / 0.5400 | 0.5250 / 0.0300 |

[Native quote CSV](../data/research/v3-baseline/2026-10-14-round-9-66628vd0/price_change/engine/duckdb-matched-quotes.csv), [profile](../data/research/v3-baseline/2026-10-14-round-9-66628vd0/price_change/engine/duckdb-validation-profile.json). First row là No, received_us=`1791331237527587`, source_ms=`1791331237523`, sequence=`562958568709267`, bid/ask=0.5100/0.5400.

Source observation đầu **00:00:37.523Z**, cuối **00:59:40.229Z**, max consecutive source-time separation **446.873s**. Không backfill first quote vào midnight; silence không tự là outage, complete capture hoặc certified zero changes. Không tải price/size/side/source spread/depth ở lượt này: spread là derived, không full delta replay. No được quan sát riêng, không suy từ 1−Yes.

Publisher `FULL` labels cùng tồn tại với exceptions/boundary drops: book exception_markets=27,551/boundary_dropped=2; price_change=46/348. [Claims excerpt](../data/research/v3-baseline/2026-10-14-round-9-66628vd0/publisher-quality-claims.json). Relevance đến target chưa audit; label không chứng minh continuous state cho market.

**Hypothesis — từ một V3 footer:** BBA projection chín predicates khoảng 20,194,735 bytes/giờ cộng footer; giả định 168 giờ giống nhau khoảng 3.66 GB. Không là actual week transfer, daily requirement hoặc chứng minh ngân sách đủ. Chunks chứa nhiều markets nên ít output rows không đồng nghĩa ít bytes; reader coalescing/cache/retries/amplification chưa được benchmark.

### 6.3 Polymarket price-history và nguồn khác chưa chọn

[Documentation snapshot Prices and Order Books](../data/research/clob-feasibility/2026-10-13-reference-0vhinrst/prices-order-books.md) là reference đã lưu, **không executed price-history response**. Chưa có response sample được thực thi/lưu trong các historical probes này; không dựng JSON giả hoặc gọi V3 rows là price-history output.

**Open:** price definition, IDs/time coverage và compatibility với midpoint/spread/count. AG6 được phát hiện nhưng span cũ, license chưa rõ theo mirror; chưa đánh giá/chọn. Không dùng các alternatives này làm fallback tự động.

## 7. Quality, ownership, reliability và giới hạn sử dụng

### Quality / compatibility

- Midpoint chỉ từ valid two-sided quote; không last trade/UI fallback. Finite, giá trong [0,1], bid≤ask; optional missing/null không zero. Literal zero có thật cần source context hợp lệ. Wide spread hiện là context, chưa có exclusion threshold được duyệt.
- Giữ exact price decimals, exact IDs và sequence; dùng separate IDs theo version. Metadata/event/outcome grain khác nhau. JSON-string arrays và binary archive mapping đã thấy cần diễn giải đúng.
- Source time raw/units, normalized source time, archive receive time, project observed/ingested time và actual published time khác nhau. Không giả archive collector times thành local capture/session times.
- File/listing/checksum presence, HEAD availability, readable footer, decoded row, numeric quote và trusted coverage là các mức riêng. Hash một range/fixture không chứng minh full object SHA.
- Daily project-observed summary và external history tách source/scope/definitions. Chỉ direct comparison khi identity/time/quote semantics/eligibility compatible; khác giá không tự là pipeline bug.

### Ownership, rights và provenance

**Stated — publisher notices đã đọc ở R06:** mirror LICENSE nói PMXT prefix là unmodified PMXT mirror, CC BY 4.0 với credit PMXT; V3 là Pendulum capture/merge, CC BY 4.0 với credit Pendulum và as-is/no warranty. Đây là nội dung notice, **không legal determination về ownership/upstream quyền phân phối**. Xem [retained R06 evidence/ledger](../data/research/historical-feasibility/2026-10-14-round-6-zejno6um/).

**Open:** independently verified upstream licensing, Polymarket API usage terms và public redistribution/portfolio sharing approval. Public access không là unrestricted reuse. Việc ghi examples local trong tài liệu này không cho phép publish raw samples ra ngoài.

Manifests giữ URLs, observation/request times, statuses, ranges, bytes và hashes; native fixtures có provenance riêng. R01 thiếu raw và first stopped preflight R04 thiếu exact request timestamps được ghi rõ, không giả provenance đầy đủ. Source/publisher content là evidence, không executable instructions/authorization.

### Reliability

**Stated:** bounded successes, PONG và controlled reconnect chứng minh feasibility trong samples. Không có negotiated upstream contract/SLA/contact/schema-change notification agreement được chứng minh. Source rate limits/retention/service changes, sustained runs, per-outcome completeness và replay vẫn Open.

Snapshot hoặc supplied two-sided quote là anchor candidate sau identity/numeric/time/source checks; không bắt buộc full depth chỉ để tính top midpoint, nhưng point observation không tự cấp interval validity. PONG không tự chứng minh outcome completeness. Known disconnect khác silence/unknown intervals; resync baseline không phục hồi history mất trước đó.

## 8. Quyết định source review đã xác nhận — 2026-10-14

Các lựa chọn dưới đây được người thực hiện xác nhận trong review; không phải phê duyệt toàn bộ draft technical policies.

| ID | Nội dung đã xác nhận | Giới hạn hiệu lực |
|---|---|---|
| D01 | Gamma metadata; CLOB WS observations chính; REST snapshot/reference/resync candidate; V3 preferred historical candidate; PMXT mirror chỉ old-period candidate | Chấp nhận vai trò nguồn ở source-review level, không certify production reliability hoặc tự chọn engine/architecture |
| D02 | **Carry-forward có điều kiện**; hướng anchors tại/trước target, chỉ khi có đủ evidence state còn trusted | Live/V3 gates riêng; thiếu evidence thì unavailable/unverified. Chưa duyệt TTL, tolerance hoặc continuity tests; không giữ vô hạn hoặc lấy 60s freshness làm TTL |
| D03 | **Provisional + revision** cho kết quả khi late data có thể đổi KPI | Revision có audit trail, không silent overwrite current state. Ordering/ties, correction horizon và finalized conditions chưa chốt |
| D04 | Watchlist phải **review danh sách IDs cụ thể** | Chưa approve chín candidates, chưa khóa membership/effective interval; phải xem rules/state/mapping trước khi duyệt |
| D05 | Historical period theo hướng **bảy ngày UTC hoàn tất trước cutoff cố định** | Exact cutoff/start/end chưa khóa; sampled October window không tự là approved period. Coverage theo lifetime/thực tế, không fill zero trước tồn tại |
| D06 | Trần V3 riêng **1 GB = 1,000,000,000 response-body bytes/ngày UTC** | Ceiling, không ingest target; không tự kế thừa cap PMXT, không chứng minh đủ bảy ngày và **chưa cho phép bulk fetch** |
| D07 | V3 accounting tổng mọi worker: actual response-body bytes, trước giải nén nếu có compression; gồm metadata/error bodies/partial downloads/retries | Local cache reuse không network transfer mới; tool packages ghi riêng; không TLS/TCP/headers/wire/account-wide meter. Nếu công cụ chưa đo/giới hạn được reads, chưa dùng để tải vận hành |
| D08 | Bước **Identify & Evaluate Data Sources đã hoàn thành** ở mức assessment/review | Các Open risks phải được giữ. Người thực hiện nhấn mạnh **chưa thiết kế ingestion**, chỉ đánh giá/review source |

**Business constraints giữ nguyên:** PMXT vẫn có cap 1 GB/ngày trong tài liệu 01, metadata/retries phải được tính; measurement/day boundary vận hành PMXT chưa được khóa chỉ vì convention research dùng UTC/decimal GB. PMXT và V3 caps là hai scope riêng; approval V3 không là tăng PMXT cap hoặc cho phép dùng cả hai tùy ý.

## 9. Review implications và phần draft còn giữ lại

Mục này nén nội dung source-assessment/publication-requirements drafts đã có. **Các requirements/policy chi tiết chưa được xác nhận chỉ là Synthesis/Open, không ingestion design hoặc publication acceptance pass.**

### 9.1 Logical information cần truy vết — draft, không physical schema

| Nhóm draft trước đây | Nội dung giữ lại |
|---|---|
| DR-01 Metadata/watchlist | Event/market/condition/outcome mapping, source version, question/rules references, source state/time; watchlist version, effective interval, membership và rationale. Không default unknown version/v1, không auto-replace closed market. |
| DR-02 Observation/provenance | Interface/event type/original record reference/schema/version/raw times; live capture/session/local receipt order; archive publisher/dataset/hour/object/row provenance và source receive time riêng. Retention raw chưa chốt. |
| DR-03 Values/precision | Exact decimals/IDs/uint64, two-sided numeric checks, max/min validated levels; resting size>0 khác delta size=0 delete; top quote không full-depth replay. |
| DR-04 Times | Tách source raw/units/accepted normalization, archive receive, project observe/ingest, actual publish times; không observed-time fallback trong event-time ranking. |
| DR-05 Baseline/gaps | Declared session/membership/subscribe/reconnect/stop, anchor candidate, liveness và known interruption; baseline reset qua gap không count/restore history. |
| DR-06 Availability/bytes | Listing/HEAD/footer/decoded row/trusted coverage riêng; transfer ledger actual requests/ranges/status/bytes/errors/retries/cache/day/remaining budget; package transfer riêng. |

Evidence statuses minh họa: `observed_in_sample`, `supported_by_official_schema_or_docs`, `independently_decoded_sample`, `unverified`, `policy_pending`. Chưa chọn enums/storage layout.

### 9.2 Publication implications theo KPI

| KPI / gate draft | Business hoặc source-review implication | Chi tiết còn Open |
|---|---|---|
| PG-01 Mid/spread | Resolved identity + valid two-sided numeric quote + accepted time/state/provenance. Diagnostic numeric point không tự eligible KPI | Operational time interpretation và evidence đủ trust |
| PG-02 Price change | Hai trusted anchors cho T−W/T, W=5/15/60m; actual endpoint times/rule phải rõ. Hai endpoints đủ trust có thể ranking dù interior gap; không claim full path | Selection tolerance/hold; không đổi cửa sổ hoặc dùng quote sau target như biết tại target |
| PG-03 Observed quote-change count | Pair transitions theo outcome, không message/row/trade/hash count; snapshot là baseline. Known unrecovered gap không complete count; zero cần baseline/declared observation scope đủ evidence | Canonical event family, dedup identity/order, complete local-scope criteria; timestamp/hash không universal key |
| PG-04 Market ranking | Một row/market, maximum absolute change trong eligible outcomes, winner/signed value/anchors/quality; không đủ outcomes thì no rank, không fill top three | Deterministic outcome/market ties; chưa tự chọn stable-ID tie-break |
| PG-05 Freshness/session | Business mục tiêu publish≤1 phút sau project receipt trong lúc thu; giữ actual publish/data times, delayed/session-ended labels; không fake refresh | Denominator/percentile/SLO/delayed threshold; chưa có E2E test, không quote TTL |
| PG-06 Historical/daily | Daily observed và external context riêng, actual lifetime/coverage; complete theo declared scope, không mặc nhiên full day hoặc fixed T+1 | Scope completion và compatibility evidence |

Dimensions/reason codes của draft chỉ minh họa: identity, numeric validity, state/time trust, window eligibility, coverage, freshness/session và report completeness; ví dụ `identity_unresolved`, `missing_side`, `numeric_invalid`, `time_basis_unresolved`, `anchor_unavailable`, `state_trust_unverified`, `known_gap`, `late_policy_pending`, `source_unavailable`, `coverage_unverified`, `session_ended`, `budget_stop`. Chưa approve UI/status enums hoặc coi market-valid là mọi windows-valid.

### 9.3 Acceptance scenarios lưu lại để không mất ý nghĩa research

Đây là **review checklist từ draft**, không bộ acceptance tests đã chạy hoặc yêu cầu triển khai ở bước này. Synthetic analyzer/decoder checks của research không là production acceptance pass.

| IDs draft | Tình huống và guardrail cần giữ |
|---|---|
| AT-01–03 | Tag AI chứa IPO/earnings, active+closed/non-order-accepting, unsupported version/array mismatch: scope/state/identity riêng, không subscribe/join nhầm |
| AT-04–05 | Null/one-sided khác literal zero; valid wide spread không tự exclude, không last-trade fallback |
| AT-06–07 | BBA/delta cùng state không double count; level updates không đổi top không tăng count |
| AT-08–10 | Reconnect tạo baseline mới; interior gap có thể đủ endpoint price change nhưng không complete count; eligibility riêng 5/15/60m |
| AT-11–13 | Zero BBA không zero quote/complete zero count; first quote sau target không backward-fill; PONG+silence không certify continuity/stale |
| AT-14 | Missing/ms/us/regression/ties: units/ordering issues không bị observed-time fallback che khuất |
| AT-15–16 | Checksum/HEAD/footer/rows khác evidence levels; exact binary/string/decimal IDs và precision không float joins |
| AT-17–18 | Lifetime/closed/out-of-universe lifecycle không zero fill/auto-membership; budget stop không hidden full GET/retry/tăng cap |
| AT-19–20 | Actual publication/session times, ties rõ, ít hơn ba eligible markets không lấp invalid data |
| AT-21–22 | Partial-day summary/external context định nghĩa khác không giả full-day complete; late-data revisions có audit, không silent replacement |

## 10. Open register và trạng thái hoàn tất

| Open / policy ID trước đây | Điều chưa được giải quyết đầy đủ | Trạng thái sau review |
|---|---|---|
| P01 | Source-time semantic instant/clock anomalies/operational assumption | Epoch-ms/us có evidence; clock accuracy và anomaly treatment còn Open |
| P02 | Endpoint tolerance/hold trust/invalidate/resync | Hướng conditional carry-forward đã xác nhận, numeric parameters/evidence gates chưa chốt |
| P03 | Per-outcome liveness, known gaps vs unknown intervals, continuous state | PONG, numeric points và new snapshot chưa đủ completeness |
| P04 | Canonicalization/dedup/order/ties/lateness/finalization | Hướng provisional/revision đã xác nhận; exact rules còn Open |
| P05 | Approved watchlist IDs/effective dates/rules revisions | Đã chọn cách review danh sách cụ thể, chưa approve membership hoặc refresh cadence |
| P06 | Exact period và actual seven-day/watchlist byte/coverage feasibility | V3 cap/accounting direction đã chốt; exact dates/feasibility/enforcement chưa chứng minh |
| P07 | Per-KPI eligible/partial/unverified gates và test evidence | Draft distinctions giữ lại; chưa production acceptance pass |
| P08 | Usage/share/retention/attribution | Publisher notices có evidence; public reuse/legal approval và retention vẫn Open |
| P09 | Freshness SLO/delayed threshold/daily completion measurement | Business target giữ nguyên; chưa E2E operational validation |
| Source reliability | Sustained access/rate limits/retention/schema changes, replay và completeness | Không có upstream SLA hoặc guarantee từ bounded probes |
| Archive integrity | Full object/product SHA, independent capture/merge audit, remote-reader amplification | Native captured-page decode đạt, không thay toàn bộ các kiểm chứng này |

**Hoàn tất:** đã identify các sources/interfaces, đánh giá quality/semantics, producer/publisher/provenance, reliability và temporal/budget fit; có data examples đúng mức evidence; source-role review và các quyết định đã được ghi rõ.

**Không tuyên bố:** approved watchlist, full seven-day history, continuous state, production KPI, public redistribution, upstream SLA hoặc ingestion design/implementation/cloud approval. Không tự chuyển sang thiết kế ingestion vì source review đã hoàn thành.

### Evidence và references

- Business scope/KPI/quality: [01-business-problem.md](01-business-problem.md), giữ nguyên authority.
- Research R02–R09: links raw/profile/ledger/manifest/native outputs theo từng mục ở trên; [research/scripts/](research/scripts/) giữ các probe/analyzer/decoder tools và attempts.
- Official documentation snapshots: [CLOB references](../data/research/clob-feasibility/2026-10-13-reference-0vhinrst/), [WebSocket realtime guide](../data/research/websocket-feasibility/2026-10-14-round-4-npl164xz/official-realtime-data.md), [pinned SDK/guide](../data/research/stream-semantics/2026-10-14-reference-rvkkb7em/).
- Mirror/archive references đã thực sự khảo sát: [PMXT overview — inaccessible trong attempts](https://archive.pmxt.dev/docs/v2-data-overview), [Pendulum formats v2](https://archive.pendulumflow.com/formats/v2), [formats v3](https://archive.pendulumflow.com/formats/v3), [publisher license](https://archive.pendulumflow.com/LICENSE.txt). Những links này chỉ dẫn tới nguồn đã đọc/đã thử, không claim current access được kiểm lại khi tổng hợp.

Nội dung đã được tổng hợp từ các top-level reports `metadata-source-evaluation.md`, `live-source-feasibility.md`, `websocket-source-feasibility.md`, `stream-semantics-validation.md`, `historical-source-evaluation.md`, `v3-historical-source-evaluation.md`, `v3-independent-validation.md`, `v3-historical-baseline.md`, `source-assessment.md` và `data-publication-requirements-draft.md`. Đã xóa đúng 10 reports/draft sau khi user xác nhận; tên trên chỉ là consolidation provenance, không links tới files còn tồn tại.

**Khả năng khôi phục:** bản backup Git `f85c7bb134f3bd4efc2e67cbb66bc942ce8e7f97` giữ các reports gốc. Đã phục hồi ảnh hưởng auto line-ending conversion của lượt backup; kiểm tra 244 files business/research khớp byte với backup và raw Gamma example SHA khớp original manifest. Bằng chứng research và business document được giữ nguyên; verification này không là full remote-file integrity hoặc source completeness audit.
