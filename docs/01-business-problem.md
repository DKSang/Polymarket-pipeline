# Polymarket Market Intelligence — Business Problem & Requirements

> **Phiên bản:** 2.0 (tối giản hoá từ v1.1) · **Ngày:** 2026-10-15
> **Mục đích:** Portfolio project cho vị trí intern/fresher Data Engineer (Microsoft Fabric).
> **Phạm vi tài liệu:** vấn đề, người dùng, KPI, quy tắc dữ liệu, phạm vi, tiêu chí hoàn thành. *Cách làm* nằm ở tài liệu [02](02-source-research-and-decisions.md) (nguồn dữ liệu), [03](03-architecture-design.md) (kiến trúc) và [04](04-data-modelling.md) (mô hình dữ liệu).

## 1. Tóm tắt một trang

| Hạng mục | Nội dung |
|---|---|
| **Vấn đề** | Khó biết nhanh market AI nào trên Polymarket đang biến động mạnh, mô tả bằng dữ liệu và đặt vào bối cảnh lịch sử; dữ liệu thiếu/trễ dễ bị hiểu nhầm là "thị trường yên tĩnh" |
| **Người dùng chính** | Market researcher / analyst (người theo dõi thông thường là người dùng phụ) |
| **Hành động được hỗ trợ** | Chọn tối đa **3 markets** đáng nghiên cứu và kiểm tra dữ liệu đằng sau nhận định |
| **Universe** | Watchlist cố định, **≤ 20 markets** về sự kiện AI trực tiếp |
| **Ranking** | Mỗi market một dòng; xếp theo **absolute price change** lớn nhất trong các outcome đủ điều kiện |
| **Cửa sổ** | 5, 15, 60 phút (theo event time) |
| **KPI cốt lõi** | Mid-price, price change, bid-ask spread, quote-change count, freshness, coverage |
| **Hai góc nhìn** | **Live** (trong phiên thu thập) và **Daily + History** (tổng hợp ngày, bối cảnh 7 ngày) |
| **Mục tiêu freshness** | p95 ≤ 60 giây trong phiên thu — *mục tiêu cần đo, không phải cam kết* |
| **Nguồn lực** | Fabric trial (F64) + Azure for Students; chạy thử hữu hạn |
| **Hoàn thành khi** | Demo kiểm chứng được (mục 8) + bằng chứng portfolio (code, tài liệu, video, Fabric items trong Git) |

**Problem statement**

> Người nghiên cứu các thị trường AI trên Polymarket cần một cách nhất quán để xác định markets có biến động giá lớn nhất trong phạm vi đang theo dõi, mô tả diễn biến bằng dữ liệu, đặt kết quả vào bối cảnh lịch sử và nhận biết giới hạn của dữ liệu hỗ trợ nhận định.

**Sản phẩm này mô tả và xếp hạng biến động.** Nó *không* gọi biến động là "bất thường", không suy ra nguyên nhân từ tin tức, không khuyến nghị đầu tư. Câu tuyên bố duy nhất được phép: *"Biến động lớn nhất trong tập markets đủ dữ liệu đang theo dõi."*

## 2. Mục tiêu và người dùng

| Mục tiêu | Giá trị | Bằng chứng trong MVP |
|---|---|---|
| **Identify** | Thu hẹp danh sách cần nghiên cứu | Ranking theo absolute price change, cửa sổ 5/15/60 phút |
| **Describe** | Biết điều gì thay đổi, khi nào, mạnh cỡ nào | Giá, spread, quote-change count, hai mốc giá và cách tính |
| **Contextualize** | Đặt diễn biến vào bối cảnh rộng hơn | Daily brief + lịch sử 7 ngày, hai phần tách biệt |
| **Trust** | Không nhầm dữ liệu thiếu/trễ với trạng thái thị trường | Freshness, coverage, gaps, trạng thái hợp lệ theo từng KPI/cửa sổ |

Các vai trò là **persona giả định cho portfolio**, chưa phỏng vấn người dùng thật. Khi nhu cầu xung đột, ưu tiên độ chính xác và khả năng kiểm chứng của researcher.

**User stories**

1. Là researcher, tôi muốn xem markets có thay đổi giá lớn nhất trong 5/15/60 phút để chọn đối tượng nghiên cứu.
2. Tôi muốn biết outcome nào tạo ra thứ hạng, giá tăng hay giảm, và hai mốc giá được dùng.
3. Tôi muốn xem giá, spread và nhịp thay đổi báo giá để mô tả diễn biến định lượng.
4. Tôi muốn xem bối cảnh lịch sử và biết khi nào dữ liệu không đủ để kết luận (kết quả trễ, gaps, KPI không đủ điều kiện).

**Chỉ số thành công (tuỳ chọn):** thời gian để một researcher chọn và mô tả được ≤ 3 markets. Chỉ công bố con số nếu đã đo thật.

## 3. Phạm vi quan sát

**Chủ đề — chỉ markets về sự kiện AI trực tiếp:** ra mắt mô hình/sản phẩm AI; năng lực hoặc kết quả benchmark của mô hình; chính sách trực tiếp về AI. Không tự mở rộng sang cổ phiếu, chip, crypto hay hoạt động doanh nghiệp chỉ vì liên quan gián tiếp. Keyword chỉ để *tìm ứng viên*; phải đọc nội dung và điều kiện resolution trước khi đưa vào watchlist.

**Watchlist**
- Cố định trong mỗi đợt chạy, tối đa 20 markets đủ tiêu chí (ít hơn thì dùng số thực tế và công bố số đó).
- Lưu dạng file cấu hình trong Git (`config/watchlist.csv`) gồm market ID, lý do chọn, ngày hiệu lực — mỗi thay đổi là một commit có review.
- Market đóng hoặc mất dữ liệu: ghi nhận trạng thái, không tự thay market khác, không xoá lịch sử.
- Đếm theo **market** (không theo event hay outcome/token).

**Phiên quan sát:** near-real-time chỉ được hứa trong các khoảng thu thập đã khai báo; không cam kết 24/7. Ngoài phiên hoặc trong gaps, không suy ra "thị trường không biến động".

Ranking chỉ đại diện cho watchlist, **không** đại diện toàn Polymarket.

## 4. KPI và quy tắc ranking

Tất cả giá nằm trong khoảng 0–1 (đơn vị giá). KPI tính **theo outcome**, không gộp YES và NO.

| KPI | Định nghĩa | Lưu ý |
|---|---|---|
| **Mid-price** | `(best_bid + best_ask) / 2`, khi cả hai phía hợp lệ | Giá tham chiếu từ báo giá, không phải giá giao dịch |
| **Bid-ask spread** | `best_ask − best_bid` | Chưa đủ để kết luận về thanh khoản |
| **Price change** | `mid(T) − mid(T − W)`, W ∈ {5, 15, 60} phút | `0.40 → 0.45` là `+0.05` đơn vị giá, **không** gọi là "+5%" |
| **Absolute price change** | `|price change|` | Đo độ lớn, không đo hướng |
| **Quote-change count** | Số lần cặp (best bid, best ask) của một outcome đổi trạng thái trong cửa sổ | Không phải trading volume hay số message |
| **Freshness** | Độ trễ từ lúc collector nhận dữ liệu đến lúc kết quả hiển thị, kèm mốc `data as of` | Refresh thành công ≠ dữ liệu mới |
| **Coverage** | Tỷ lệ thời gian được quan sát liên tục trong cửa sổ/phiên/ngày | Không suy ra từ số lượng quote |

**Quy tắc ranking**
1. Mỗi dòng là một market; lấy outcome có absolute price change lớn nhất trong các outcome đủ điều kiện, tính riêng từng cửa sổ.
2. Hiển thị: outcome tạo thứ hạng, price change có dấu, hai mốc giá (thời điểm + giá), trạng thái chất lượng.
3. Không outcome nào đủ điều kiện → market không có hạng ở cửa sổ đó nhưng vẫn hiện trạng thái và lý do.
4. Chấp nhận ít hơn 3 markets đủ điều kiện; không lấp chỗ trống bằng dữ liệu không hợp lệ.
5. Đồng hạng: xếp theo `market_id` tăng dần (quy tắc kỹ thuật cố định, ghi chú trong tài liệu 04).

**Cơ sở thời gian**
- **Event time** (nguồn gửi): dùng cho mọi KPI thị trường.
- **Received time** (collector nhận): dùng đo độ trễ/freshness.
- Event time thiếu hoặc không hợp lệ → loại quote khỏi KPI; **không** âm thầm thay bằng received time.
- Mọi timestamp lưu theo UTC.

## 5. Quy tắc chất lượng dữ liệu

Điều kiện hợp lệ xét **riêng từng KPI và từng cửa sổ**, không chỉ theo market.

| Tình huống | Hành vi |
|---|---|
| Thiếu một phía bid/ask, giá ngoài [0,1], bid > ask | Quote không dùng cho KPI; vẫn lưu kèm lý do |
| Không có dữ liệu | Hiển thị "không có dữ liệu" — **không** thay bằng 0 |
| Không có quote mới nhưng kết nối vẫn liên tục | Giá cũ vẫn là giá hiện tại (market yên tĩnh), không coi là stale |
| Mất kết nối giữa hai mốc giá | Vẫn xếp hạng được nếu cả hai mốc đủ tin cậy, nhưng gắn cờ `has_gap`; quote-change count đánh dấu `partial` |
| Sau reconnect có snapshot | Snapshot là mốc khởi đầu mới, **không** phải một lần thay đổi giá |
| Có dữ liệu cho 5 phút nhưng chưa đủ 60 phút | Chỉ công bố cửa sổ đủ điều kiện; không đổi nhãn cửa sổ |
| Quote đầu tiên xuất hiện sau mốc T | Không điền ngược (backward-fill) về T |
| Spread rộng | Hiển thị như ngữ cảnh; không loại, không thay bằng last-trade price |

## 6. Báo cáo và freshness

| Đầu ra | Nội dung | Yêu cầu |
|---|---|---|
| **Live dashboard** | Ranking 5/15/60, outcome tạo hạng, giá/spread/count, freshness và trạng thái phiên | Mục tiêu p95 ≤ 60 giây từ lúc nhận đến lúc hiển thị, chỉ trong phiên thu. Không có dữ liệu mới > 2 phút → gắn nhãn `delayed` và giữ kết quả cũ với mốc thời gian thật. Hết phiên → `session ended`. Không cập nhật giả thời gian |
| **Daily brief** | **Phần A — observed:** tổng hợp dữ liệu hệ thống thực sự quan sát (kèm khoảng thời gian và gaps). **Phần B — history:** dữ liệu lịch sử từ nguồn ngoài, kèm nguồn và định nghĩa riêng | Trạng thái `pending / partial / complete`; "complete" nghĩa là xử lý xong phạm vi đã khai báo, không phải đủ 24 giờ. Không cam kết giờ ra báo cáo cố định |
| **History view** | Giá lịch sử mục tiêu 7 ngày của watchlist | Coverage thực tế riêng từng market; không tạo số 0 trước khi market tồn tại hay khi nguồn thiếu; chỉ so sánh trực tiếp với dữ liệu observed khi định nghĩa giá tương thích — nếu chưa chứng minh, hiển thị hai góc nhìn bổ trợ |

Bảy ngày là bối cảnh ngắn hạn, không đủ để tuyên bố xu hướng có ý nghĩa thống kê. Ngưỡng 2 phút cho `delayed` là giá trị mặc định, chỉnh lại sau khi đo.

## 7. Phạm vi và ràng buộc

**In scope — MVP**
- Watchlist ≤ 20 markets AI; ranking theo outcome → market ở 5/15/60 phút.
- Chi tiết mid-price, spread, quote-change count; chỉ báo chất lượng và coverage.
- Luồng live trong phiên, daily brief, bối cảnh lịch sử 7 ngày.

**Out of scope**
- Đặt lệnh, giao dịch tự động, khuyến nghị đầu tư.
- Phát hiện bất thường đã xác thực; giải thích nguyên nhân bằng tin tức.
- Bao phủ toàn Polymarket hoặc thu 24/7 toàn bộ orderbook.
- Dùng quote-change count làm trading volume.
- Hệ thống on-premise/offline hoặc deployment thứ hai.

**Nguồn lực và ràng buộc**
- Fabric trial **F64** + Azure for Students. Trial có thời hạn riêng → lưu bằng chứng trước khi hết hạn.
- Không có deadline cứng; ưu tiên chất lượng phù hợp fresher. Chạy cloud vài ngày là đủ.
- Thử nghiệm theo bậc: chạy ngắn → chạy ~24 giờ với cấu hình cố định → kéo dài nếu đạt.
- Đo riêng Fabric capacity (CU) và chi phí/credit Azure; hai cơ chế không dùng chung một "ví".
- **Ngân sách tải dữ liệu lịch sử: tối đa 1 GB response-body/ngày UTC**, tính cả metadata, lỗi, retry. Vượt trần → dừng và đánh giá lại, không âm thầm tăng. Lịch sử 7 ngày có thể phải tải rải nhiều ngày.

## 8. Tiêu chí hoàn thành

**Demo đạt yêu cầu khi người xem có thể:**
1. Xem ranking 5/15/60 phút của watchlist đã công bố, hoặc thấy lý do cửa sổ chưa đủ dữ liệu.
2. Chọn ≤ 3 markets đủ điều kiện và nêu được outcome, độ lớn/hướng, hai mốc giá và cách tính.
3. Xem giá, spread, quote-change count cùng trạng thái chất lượng.
4. Xem daily brief và bối cảnh lịch sử; biết khi nào không thể đối chiếu hay kết luận.
5. Nhận ra kết quả trễ, gaps, KPI không đủ điều kiện — không nhầm dữ liệu thiếu với số 0.
6. Tra được định nghĩa KPI, universe, thời gian và nguồn của từng kết quả.

**Bằng chứng portfolio (Definition of Done)**
- Repo: code, README có sơ đồ kiến trúc, tài liệu 01–04, hướng dẫn chạy lại.
- Video demo + ảnh chụp dashboard/report.
- Fabric items lưu trong Git theo định dạng native của Git integration (file `.platform` **cùng** các file định nghĩa riêng của từng item).
- Báo cáo kiểm thử chất lượng dữ liệu (kết quả test ở tài liệu 04, mục 10) và ghi chú đo CU/chi phí.

**Đánh giá bổ sung (không bắt buộc):** thử nghiệm người dùng, đo time-to-insight. Không công bố tác động chưa đo (ví dụ "giảm 50% thời gian").

## 9. Giả định và rủi ro cần kiểm chứng

| Nội dung | Cần xác minh | Kế hoạch |
|---|---|---|
| Universe AI | Có ≥ vài markets đủ điều kiện, đang nhận lệnh, có báo giá hai phía | Chạy discovery + review thủ công trước khi chốt watchlist |
| Timestamp | Đơn vị (ms/µs) và độ tin cậy của event time ở từng nguồn | Kiểm tra trong spike; quote lỗi giờ → loại khỏi KPI |
| Quote-change count | Đếm đúng khi có message trùng (BBA + delta) và sau reconnect | Test với fixture từ dữ liệu khảo sát (tài liệu 04, mục 10) |
| Lịch sử 7 ngày | Đủ dữ liệu cho watchlist trong trần 1 GB/ngày | Spike đo bytes thực trên 1–2 giờ-file trước khi tải bulk |
| Freshness 60 giây | Đạt được p95 ≤ 60 giây | Đo trong lần chạy 24 giờ |
| Nguồn lực Fabric/Azure | Hạn trial, quyền tài khoản, CU thực dùng | Theo dõi Capacity Metrics; dừng item khi hết phiên |
| Điều khoản dữ liệu | Quyền chia sẻ lại dữ liệu mẫu/kết quả | Ghi nguồn theo giấy phép CC BY 4.0 của publisher; **không công khai dữ liệu thô** cho tới khi xác minh |

Thay đổi ảnh hưởng phạm vi business phải được thảo luận và ghi vào changelog, không âm thầm thay yêu cầu.

## Thay đổi so với v1.1

- Gộp 12 mục thành 9; bỏ phần lặp (Executive Summary + One-page Brief hợp nhất thành mục 1).
- Gộp mục tiêu G4 *Verify* vào *Trust*; rút gọn user stories (5 → 4) và các câu "không phải X" lặp lại.
- Chốt tham số mặc định thay vì để "Open": ngưỡng `delayed` = 2 phút; đồng hạng xếp theo `market_id`; freshness đo bằng p95.
- Thêm Definition of Done kỹ thuật; watchlist lưu trong Git.
- Nguồn lịch sử: **bỏ PMXT v2** (archive mirror kết thúc 2026-08-09, không phủ giai đoạn tháng 10/2026), dùng ngân sách 1 GB/ngày cho nguồn lịch sử được chọn ở tài liệu 02 (Pendulum Flow V3).

## References

- [Polymarket — Markets & Events](https://docs.polymarket.com/concepts/markets-events): phân biệt event, market, outcome.
- [Polymarket — Prices & Order Books](https://docs.polymarket.com/market-data/prices-order-books): giá và báo giá.
- [Polymarket — Realtime Data](https://docs.polymarket.com/market-data/realtime-data): book updates vs best-bid/ask events.
- [Microsoft Learn — Fabric trial capacity](https://learn.microsoft.com/en-us/fabric/fundamentals/fabric-trial): trial có thời hạn.
- [Azure for Students](https://azure.microsoft.com/en-us/pricing/offers/ms-azr-0170p/): credit Azure riêng, không phải Fabric capacity.
- [Microsoft Learn — Git source code format](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/source-code-format): `.platform` và định nghĩa theo item.