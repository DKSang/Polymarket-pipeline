# 02 — Identify & Evaluate Data Sources

> **Dự án:** Polymarket Market Intelligence · **Phiên bản:** 2.0 (tối giản hoá từ v1)
> **Khảo sát:** 2026-10-13 → 2026-10-14 · **Tổng hợp lại:** 2026-10-15
> **Business authority:** [01 — Business Problem & Requirements](01-business-problem.md)
> **Phạm vi:** nguồn nào cung cấp metadata, quotes live và lịch sử; dữ liệu trông như thế nào; giới hạn gì phải nhớ khi dùng. Cách thiết kế pipeline nằm ở [03](03-architecture-design.md) và [04](04-data-modelling.md).

**Cách đọc nhãn bằng chứng**

| Nhãn | Nghĩa |
|---|---|
| ✅ **Đã quan sát** | Thấy trong mẫu/tài liệu; chỉ đúng cho mẫu và thời điểm được ghi, không phải bảo đảm của nguồn |
| ⚠️ **Giả định** | Suy luận hoặc giả thuyết, cần kiểm chứng trong spike |
| ❓ **Chưa biết** | Chưa có bằng chứng; không được lấp bằng zero hay số liệu bịa |

## 1. Kết luận: vai trò của từng nguồn

| Nguồn | Nhà cung cấp | Vai trò trong dự án | Trạng thái | Giới hạn chính |
|---|---|---|---|---|
| **Gamma public REST** | Polymarket | Tìm market, rules, trạng thái, ánh xạ event → market → outcome | ✅ Dùng | Tag/keyword và `active=true` chưa đủ để vào watchlist |
| **CLOB WebSocket (market channel)** | Polymarket | **Nguồn quote live chính** trong phiên | ✅ Dùng | Không có bảo đảm đầy đủ, replay, SLA hay 24/7 |
| **CLOB REST `/book`** | Polymarket | Đối chiếu/kiểm tra thủ công | 🔸 Ngoài pipeline MVP | Snapshot, không phải lịch sử thay đổi; reconnect WS đã có book snapshot ban đầu |
| **Pendulum Flow V3** (Parquet) | Pendulum Flow (capture + merge) | **Nguồn lịch sử ưu tiên** cho 7 ngày gần đây | ✅ Dùng (stretch) | Chưa chứng minh đủ cho cả watchlist/7 ngày; không chứng minh liên tục |
| **PMXT v2** (gốc + mirror Pendulum) | PMXT | — | ❌ **Loại** | Mirror kết thúc 2026-08-09, không phủ giai đoạn tháng 10/2026; docs gốc không truy cập được |
| **Polymarket price-history API** | Polymarket | — | ⏸ Chưa chọn | Có trong tài liệu nhưng chưa chạy thử; định nghĩa giá có thể khác mid-price tự tính |

Đủ bằng chứng để **kết thúc bước đánh giá nguồn**. Không probe thêm chỉ để ép ra độ đầy đủ/SLA mà nguồn không cung cấp — những phần chưa biết (mục 8) là giới hạn của kết quả, không được che bằng zero fill.

## 2. Quyết định đã chốt

| ID | Quyết định | Ghi chú |
|---|---|---|
| D1 | Vai trò nguồn như bảng mục 1 | Chấp nhận ở mức đánh giá, không chứng nhận độ tin cậy production |
| D2 | **Giá mốc (anchor) = quote hợp lệ gần nhất tại/trước T, trong cùng một đoạn kết nối liên tục** | Cụ thể hoá "carry-forward có điều kiện" của v1. Không cần TTL: "còn đáng tin" = kết nối không bị gián đoạn từ lúc quote đó đến T. Không dùng quote sau T |
| D3 | Late data xử lý bằng **chạy lại idempotent** theo ngày/cửa sổ bị ảnh hưởng | Thay cho cơ chế provisional + revision riêng của v1; Delta history đủ để audit |
| D4 | Watchlist duyệt **từng market ID** thủ công, lưu trong Git | 9 ứng viên ở mục 3 **chưa** được duyệt |
| D5 | Lịch sử = **7 ngày UTC trọn vẹn** trước một cutoff cố định khi bắt đầu backfill | Khoảng tháng 10 từng khảo sát chỉ là mẫu minh hoạ |
| D6 | Trần tải V3: **1.000.000.000 response-body bytes / ngày UTC** | Là trần, không phải chỉ tiêu; không tự thừa kế cho nguồn khác |
| D7 | Tính bytes: tổng mọi worker, byte thực của response (trước giải nén), gồm metadata, lỗi, tải dở, retry; cache cục bộ không tính | Công cụ tải chưa đo/chặn được bytes thì **không** dùng để tải bulk |
| D8 | Bước đánh giá nguồn đã xong | Giữ các giả định ở mục 8 |

## 3. Gamma metadata

**Cái đã quan sát (R01–R02)**
- ✅ Tag AI có ID `439`. Hai trang tag chứa 40 event và 387 market riêng biệt; 276 market qua bộ lọc trạng thái sơ bộ, 18 đã đóng, 93 không nhận lệnh dù event còn mở.
- ✅ `outcomes` và `clobTokenIds` là **chuỗi JSON**, không phải mảng; parse xong có độ dài khớp nhau (index 0 = Yes, 1 = No).
- ✅ 327/387 market có `resolutionSource` rỗng nhưng `description` vẫn có thể nêu nguồn xác định kết quả → không loại chỉ vì trường rỗng.
- ✅ Gọi chi tiết trực tiếp một market không luôn có trường liên kết event → lấy quan hệ event–market từ danh sách/discovery.
- Bộ lọc trạng thái sơ bộ: `active`, không `closed`, không `archived`, `acceptingOrders`, `enableOrderBook`; trường thiếu = *chưa biết*. Qua bộ lọc **chưa** chứng minh có báo giá hai phía hay liên tục.

**Ví dụ response thật** (trích; market `3072353`, GET 2026-10-13T15:25:41Z, HTTP 200 — [raw](../data/research/gamma-metadata/2026-10-13-round-2-mbzjqacc/market-3072353.json))

```json
{
  "id": "3072353",
  "conditionId": "0x2b071ab38aad8c77a564168ea9d098df986c80d4642af4c002b300bd703c8163",
  "version": "v1",
  "outcomes": "[\"Yes\", \"No\"]",
  "clobTokenIds": "[\"98702525727507873090971630240388903632041189534811728534540010314099930030441\", \"56907067907293748413568175622799179363128473527944796726773537650077042437524\"]",
  "active": true, "closed": false, "archived": false,
  "acceptingOrders": true, "enableOrderBook": true,
  "createdAt": "2026-07-23T22:52:56.571827Z",
  "resolutionSource": "https://agi.safe.ai/"
}
```

**Ứng viên watchlist (chưa duyệt)** — 9 market / 18 outcome, đủ minh hoạ nhưng chưa tới 20:

| Market ID | Nội dung | Lưu ý khi duyệt |
|---|---|---|
| 3554657, 3554659 | Anthropic / Google xếp hạng Arena Text cuối tháng 10 | Xem thời điểm kiểm tra, loại AutoEval, luật hoà |
| 3072353 | OpenAI đạt HLE Accuracy ≥ 60% trong 2026 | Chỉ Accuracy, không phải Calibration Error |
| 2787890 | Chính sách Mỹ loại bỏ truy cập công khai model AI Trung Quốc | Phải đúng điều kiện trong rules |
| 5408226 | Gemini Argon public availability | ⚠️ **Đã `closed=true`** (preflight R04) — không chọn cho live |
| 5443473 | Haiku 5.6+ public availability | Quy ước tên/phiên bản, quyền truy cập công khai |
| 5419351 | Mistral Large 4.1+ public availability | Không tính rollout phiên bản cũ |
| 5230698 | Dots free access | Dùng thử trong gói trả phí không đủ |
| 5219371 | Gemini Argon HLE debut ≥ 55% | Khác market release/highest-in-year |

Đã loại khi review: IPO (`1345229`), TSM earnings (`5457258`), data-center moratorium chung (`5347188`) — ngoài phạm vi AI trực tiếp; `5420046` (domain/branding chưa phù hợp); `4412200`, `5635905` (điều kiện kích hoạt không phải bản thân AI/sự cố); `3072351` đã đóng; `5598611` không qua bộ lọc trạng thái.

## 4. CLOB — REST và WebSocket

### 4.1 REST `/book` (R03)

- ✅ 18/18 outcome có book khớp ID với Gamma, hai phía, không bị chéo (crossed); 814/814 mức giá qua kiểm tra số; 6/6 phép tính midpoint/spread tự tính khớp.
- ⚠️ **Tài liệu tự mâu thuẫn về thứ tự mức giá:** OpenAPI nói bids giảm dần/asks tăng dần; guide, payload thực và SDK lại là bids tăng dần/asks giảm dần → **không dựa vào phần tử đầu/cuối**; `best_bid = max(bids)`, `best_ask = min(asks)`.
- Ví dụ HLE (3072353) Yes: bid `0.37` (size 667), ask `0.38` (size 10.1) → mid = 0.375, spread = 0.01. `last_trade_price = "0.610"` **không** thay thế mid.
- ✅ Spread rộng là có thật: Mistral `5419351` spread 0.27; Gemini benchmark `5219371` spread 0.17. Gemini release `5408226` từng có mid 0.983 nhưng `last_trade_price = "0.035"` — chưa rõ nguyên nhân, không suy ra nguồn sai.

### 4.2 WebSocket market channel (R04, R05)

- ✅ Snapshot ban đầu có thể là **mảng JSON**; các update là object. `price_change` chứa nhiều asset trong một message; sự kiện `new_market` có thể thuộc market ngoài universe đã subscribe.
- ✅ R05 (300 giây, 6 outcome): 6 book ban đầu, 1.126 dòng `price_change`, 82 `best_bid_ask` (BBA), 112 dòng `size = 0`, 29 PONG, 18 sự kiện lifecycle ngoài universe. Sau khi gộp các biểu diễn trùng, có **82 lần đổi trạng thái** ở mức outcome — **không** cộng thành 164.
- ✅ R04: kết nối lại có book snapshot mới nhưng **không** trả lại đường đi giá trong lúc mất kết nối (gián đoạn có chủ đích 11,335 giây). PONG chỉ chứng minh kết nối còn sống.
- ✅ SDK mô tả timestamp là **epoch-ms**; `best_bid`/`best_ask` rỗng được chuẩn hoá thành null (không phải 0). Timestamp có thể null trong một số schema.
- ✅ Guide chính thức: `size = 0` nghĩa là xoá mức giá đó.

**Ví dụ một lần đổi giá, hai biểu diễn** (HLE `3072353`, outcome Yes, 2026-10-14 — [raw R05](../data/research/stream-semantics/2026-10-14-round-5-hn6sp9mn/session-1-messages.jsonl))

| Msg | Nhận lúc (UTC) | Source timestamp | Loại event | Bid / Ask | Mid |
|---|---|---|---|---|---:|
| 1 | 01:59:41.983 | `1791943179229` | `book` (trong mảng 6 book) | 0.37 / 0.41 | 0.390 |
| 4 | 01:59:46.998 | `1791943185373` | `best_bid_ask` | 0.36 / 0.41 | 0.385 |
| 6 | 01:59:46.999 | `1791943185373` | `price_change` (Yes và No) | 0.36 / 0.41 | 0.385 |

Phần tử tương ứng trong `price_changes` của msg 6:

```json
{ "asset_id": "98702525727507873090971630240388903632041189534811728534540010314099930030441",
  "price": "0.37", "size": "0", "side": "BUY",
  "hash": "8e64ae86f017e86d55aebbbdc27a11c89e619e35",
  "best_bid": "0.36", "best_ask": "0.41" }
```

⚠️ Suy ra từ mẫu: BBA và delta tương ứng mô tả **cùng một trạng thái mới**, không phải hai lần đổi giá. Không giả định BBA luôn đến trước.

### 4.3 Điều chưa có bảo đảm
- ❓ Server ghi thời gian ở bước nào (tạo/cập nhật/phát), độ chính xác đồng hồ, độ trễ nguồn.
- ❓ `timestamp` + `hash` **không** được xác minh là ID duy nhất của event; mẫu không có đảo thứ tự hay trùng hoàn toàn nhưng không phải bảo đảm "đúng thứ tự, đúng một lần".
- ❓ Top-of-book khớp không chứng minh dựng lại được toàn bộ độ sâu.
- Phiên 300 giây không chứng minh anchor 5 phút hay mục tiêu publish 1 phút.

## 5. Nguồn lịch sử

### 5.1 Pendulum Flow V3 — nguồn được chọn

- ✅ Cửa sổ khảo sát `[2026-10-07T00Z, 2026-10-14T00Z)` (168 giờ-file): 150 mục trong danh sách checksum; 18 giờ cuối thiếu mục nhưng HEAD trực tiếp đều 200. Chưa HEAD đủ 168 file, chưa kiểm coverage từng market.
- ✅ File `2026-10-13T23`: 1.548.932.856 bytes, 137.353.056 dòng, 529 row group, footer 1.562.650 bytes. Chỉ tải 9 cột của một row group BBA (2.519.438 bytes) → tìm được 60 dòng HLE (30 Yes / 30 No), số liệu hợp lệ.
- ✅ Kiểm tra độc lập bằng DuckDB 1.4.3 trên 60 dòng: khớp tuyệt đối ID, vị trí dòng, timestamp, sequence, decimal; 0 sai lệch. Đây là kiểm tra mẫu, không phải kiểm toán toàn file.
- ✅ Số dòng BBA khớp HLE ở 3 giờ mẫu: `2026-10-07T00` = 0; `2026-10-10T12` = 8; `2026-10-13T23` = 60. Giờ đầu có 0 BBA nhưng vẫn có 446 dòng `price_change` mang quote hợp lệ (223/outcome), quan sát đầu 00:00:37Z, cuối 00:59:40Z, khoảng lặng dài nhất 446,9 giây → **không điền ngược về 00:00, không coi im lặng là sự cố hay "0 thay đổi"**.
- ⚠️ Nhãn `FULL` của publisher cùng tồn tại với ngoại lệ (book: 27.551 market ngoại lệ; price_change: 46/348) → nhãn không chứng minh liên tục.

| Cột V3 | Kiểu vật lý/logic | Lưu ý |
|---|---|---|
| `timestamp_received` | TIMESTAMP(µs) UTC | Đồng hồ của *archive collector*, không phải thời điểm dự án nhận |
| `timestamp` | TIMESTAMP(ms) UTC | Event time từ nguồn; độ chính xác đồng hồ ❓ |
| `sequence` | UINT64 | Lưu `DECIMAL(20,0)`; chỉ có nghĩa cục bộ trong capture/merge, không phải thứ tự toàn cục |
| `market`, `asset_id` | BINARY (32 byte) | Cần đổi: condition → hex `0x…`; token → số thập phân uint256 (không ép binary sang text) |
| `best_bid`, `best_ask`, `spread` | DECIMAL(9,4) | Thiếu ≠ 0 |
| `bids`, `asks` | list/struct lồng nhau | Không phải chuỗi JSON như archive cũ |
| `witness_set`, `arrival_skew`, `source_witness` | Provenance capture/merge | Không phải thứ tự sự kiện của venue |

Ví dụ một dòng đã giải mã (giờ `2026-10-13T23`, BBA group 7): `market_id 3072353`, outcome `No`, `timestamp_received_raw_us = 1791932746917569`, `timestamp_raw_ms = 1791932746912`, `sequence = 11821949387386119`, bid/ask `0.5600 / 0.6300` → mid `0.5950`, spread `0.0700`.

**Ước tính ngân sách (⚠️ giả thuyết từ một footer, chưa đo thực tế):** chiếu 9 cột BBA ≈ 20,2 MB/giờ-file + footer ≈ 1,56 MB → 168 giờ ≈ **3,66 GB**. Với trần 1 GB/ngày, backfill 7 ngày cần **ít nhất 4 ngày** — hoàn toàn chấp nhận được vì không bắt buộc lấy xong trong một ngày. Chunk chứa nhiều market nên ít dòng đầu ra không có nghĩa ít bytes.

### 5.2 PMXT — vì sao loại
Docs gốc bị từ chối kết nối/timeout. Mirror công bố 2.834 file, từ `2026-04-13T19` đến `2026-08-09T23` UTC; 7/9 ứng viên được tạo sau ngày kết thúc đó và khoảng tháng 10 không giao với mirror → tăng ngân sách cũng không giải quyết được lệch thời gian. Schema mirror: `bids`/`asks` là chuỗi JSON, không có hash/sequence.

### 5.3 Price-history API
Chỉ có tài liệu; chưa chạy thử response nào. ❓ Định nghĩa giá, độ phủ ID/thời gian, tương thích với mid/spread/count. Không dùng làm fallback tự động; không dựng JSON giả.

## 6. Những điều dễ sai khi dùng dữ liệu

| # | Điều cần nhớ | Cách xử lý (quy tắc ở tài liệu 04) |
|---|---|---|
| 1 | Gamma `outcomes`/`clobTokenIds` là chuỗi JSON | Parse rồi ghép theo index; độ dài lệch → market `unmapped` |
| 2 | Gamma market ID ≠ condition ID ≠ token ID | Ba cột riêng; join theo ID, không theo question/slug/label |
| 3 | Token ID là số uint256 | Lưu `STRING`, **không** qua float |
| 4 | v1 dùng `clobTokenIds`, v2 dùng `positionIds` | Phiên bản lạ → `unmapped`, không mặc định v1 |
| 5 | Thứ tự bids/asks không nhất quán giữa tài liệu | `max(bids)`, `min(asks)` |
| 6 | Giá/size là chuỗi | Parse sang `DECIMAL`, không float |
| 7 | `last_trade_price` ≠ mid | Không dùng làm fallback |
| 8 | best_bid/ask rỗng | `NULL`, không phải 0 |
| 9 | BBA + `price_change` cùng mô tả một trạng thái | Đếm theo *đổi trạng thái*, không đếm message |
| 10 | `size = 0` xoá mức giá | Không coi là giao dịch |
| 11 | PONG ≠ dữ liệu đầy đủ; snapshot sau reconnect không bù phần đã mất | Reconnect = đoạn kết nối mới; snapshot là mốc khởi đầu, không phải thay đổi |
| 12 | WS/REST timestamp là epoch-ms; V3 có ms (event) và µs (archive receive) | Lưu cả giá trị gốc và đơn vị; không lấy received time thay event time |
| 13 | `new_market` có thể ngoài universe | Ghi log, bỏ qua trong KPI |
| 14 | V3: binary ID, UINT64 sequence, HTTP Range có end *inclusive* còn `byte_range` của sản phẩm là nửa mở | Đổi ID theo bảng mục 5.1; sequence `DECIMAL(20,0)`; trừ 1 ở end khi gọi Range |
| 15 | Nhãn `FULL` và "file có mặt" ≠ coverage liên tục | Coverage tính từ dữ liệu thực đọc |

## 7. Quyền sử dụng, nguồn gốc và độ tin cậy

- **Giấy phép (theo thông báo của publisher, không phải kết luận pháp lý):** mirror PMXT là bản sao nguyên vẹn, CC BY 4.0, ghi công PMXT; V3 là capture/merge của Pendulum, CC BY 4.0, ghi công Pendulum, "as-is". Điều khoản API Polymarket và quyền chia sẻ lại công khai chưa xác minh → **không công khai dữ liệu thô**; chỉ công bố kết quả tổng hợp/ảnh chụp kèm ghi công.
- **Provenance:** mỗi vòng khảo sát lưu URL, thời điểm, status, range, bytes, hash trong manifest. R01 không lưu raw; preflight đầu của R04 thiếu timestamp request chính xác — đã ghi rõ.
- **Độ tin cậy:** chỉ có kết quả từ các lần thăm dò có giới hạn. Không có SLA, hợp đồng hay thông báo đổi schema từ upstream. Rate limit, retention, schema change, replay ❓.

## 8. Giả định và rủi ro còn mở

| Rủi ro | Hướng xử lý trong dự án |
|---|---|
| WS mất message, không replay | Gắn đoạn kết nối + cờ `has_gap`; không bù bằng REST/archive |
| Đồng hồ nguồn lệch/đơn vị sai | Quote có event time lỗi → loại khỏi KPI, lưu lý do |
| Rate limit/chặn truy cập | Retry có backoff, ghi nhận trong log; phiên ngắn |
| V3 không đủ dữ liệu 7 ngày cho watchlist | Báo coverage thực; có thể cắt phần history khỏi MVP |
| Đọc V3 tốn bytes hơn ước tính | Kiểm tra footer trước, ước lượng bytes, dừng khi chạm trần |
| Giấy phép/điều khoản | Chỉ dùng nội bộ; ghi công; không công khai raw |
| Schema nguồn thay đổi | Lưu payload gốc ở Bronze để xử lý lại |

## 9. Bằng chứng khảo sát

| Vòng | Thời điểm | Kết quả chính | Raw/evidence |
|---|---|---|---|
| R01 | 2026-10-13 | Keyword trả kết quả ngoài phạm vi; event mở chứa market đã đóng | *Không lưu raw* (đọc lại ở R02) |
| R02 | 2026-10-13 | Gamma tag/search/detail: 387 market, 9 ứng viên | [gamma-metadata](../data/research/gamma-metadata/2026-10-13-round-2-mbzjqacc/) |
| R03 | 2026-10-13 | REST book: 18/18 khớp, số liệu hợp lệ | [clob-feasibility](../data/research/clob-feasibility/2026-10-13-round-3-dcp3q7ys/) |
| R04 | 2026-10-14 | WS 2 phiên × 65 giây; gián đoạn có chủ đích 11,3 giây | [websocket-feasibility](../data/research/websocket-feasibility/2026-10-14-round-4-npl164xz/) |
| R05 | 2026-10-14 | WS 300 giây: 82 lần đổi trạng thái; ngữ nghĩa BBA/delta | [stream-semantics](../data/research/stream-semantics/2026-10-14-round-5-hn6sp9mn/) |
| R06 | 2026-10-14 | PMXT upstream/mirror: lệch thời gian, footer đọc được | [historical-feasibility](../data/research/historical-feasibility/2026-10-14-round-6-zejno6um/) |
| R07 | 2026-10-14 | V3: metadata 168 giờ, 60 dòng HLE | [v3-feasibility](../data/research/v3-feasibility/2026-10-14-round-7-nsfgb97g/) |
| R08 | 2026-10-14 | Kiểm tra độc lập DuckDB, 3 giờ mẫu | [v3-independent-validation](../data/research/v3-independent-validation/2026-10-14-round-8-lh7ohvto/) |
| R09 | 2026-10-14 | Giờ 0 BBA vẫn có 446 `price_change` | [v3-baseline](../data/research/v3-baseline/2026-10-14-round-9-66628vd0/) |

**Bytes đã tải:** từ ~75 KB (R03) đến ~7,8 MB (R08) mỗi vòng, đo bằng body counter — **không** cộng lại thành "chi phí hằng ngày" và không ngoại suy. Dùng các file này làm **fixture kiểm thử** cho tài liệu 04 (mục 10).

## Thay đổi so với v1

- Từ 437 dòng xuống ~190: bỏ phần lặp "Stated/Inferred/Synthesis/Hypothesis/Open" (còn 3 nhãn), bỏ bảng đo bytes từng vòng, bỏ footer PMXT, bỏ checklist AT-01–22 (chuyển thành test ở tài liệu 04), bỏ danh mục "wiki đã đọc" và lịch sử backup/xoá file.
- Quyết định D02/D03 được cụ thể hoá thành quy tắc dùng được ngay (D2, D3 ở mục 2); D6/D7 giữ nguyên số liệu.
- **PMXT chuyển từ "candidate" sang "loại"** để thống nhất với tài liệu 01 v2.0.
- Thêm bảng "Những điều dễ sai" (mục 6) làm cầu nối sang tài liệu 04.

## References

- Official docs: [Polymarket Markets & Events](https://docs.polymarket.com/concepts/markets-events) · [Prices & Order Books](https://docs.polymarket.com/market-data/prices-order-books) · [Realtime Data](https://docs.polymarket.com/market-data/realtime-data).
- Snapshot tài liệu đã lưu: [CLOB references](../data/research/clob-feasibility/2026-10-13-reference-0vhinrst/), [WebSocket guide](../data/research/websocket-feasibility/2026-10-14-round-4-npl164xz/official-realtime-data.md), [pinned SDK/guide](../data/research/stream-semantics/2026-10-14-reference-rvkkb7em/).
- Archive: [Pendulum formats v3](https://archive.pendulumflow.com/formats/v3) · [formats v2](https://archive.pendulumflow.com/formats/v2) · [LICENSE](https://archive.pendulumflow.com/LICENSE.txt) · [PMXT v2 overview](https://archive.pmxt.dev/docs/v2-data-overview) (không truy cập được khi khảo sát).