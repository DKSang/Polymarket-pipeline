# Polymarket Market Intelligence — Business Problem & Requirements

> **Phiên bản:** 1.1  
> **Ngày xác nhận:** 2026-10-13  
> **Mục đích:** Portfolio project cho vị trí intern/fresher Data Engineer  
> **Trạng thái:** Đã thống nhất phạm vi business; tính khả thi của nguồn dữ liệu và các mục tiêu vận hành cần kiểm chứng  
> **Phạm vi tài liệu:** Business problem, người dùng, KPI, reporting, chất lượng dữ liệu, ràng buộc và tiêu chí hoàn thành. Không phê duyệt kiến trúc, công nghệ triển khai chi tiết hoặc hoạt động trên cloud.

## 1. Executive Summary

Người theo dõi nhiều thị trường Polymarket khó xác định nhanh market nào đang thay đổi mạnh, mô tả diễn biến bằng dữ liệu và kiểm tra lại trong bối cảnh lịch sử. Xem từng trang thị trường tốn công sức; chỉ xem báo cáo cuối ngày có thể bỏ qua diễn biến ngắn hạn. Dữ liệu thiếu hoặc trễ còn có thể bị hiểu nhầm là thị trường không có hoạt động.

**Polymarket Market Intelligence** là sản phẩm dữ liệu hỗ trợ nghiên cứu các thị trường trực tiếp về AI, kết hợp:

- **Session / near-real-time view:** xếp hạng và mô tả biến động trong những khoảng hệ thống thực sự thu thập dữ liệu.
- **Historical / daily view:** tổng hợp dữ liệu đã quan sát và cung cấp bối cảnh lịch sử từ nguồn bên ngoài, với định nghĩa và phạm vi tách biệt.

**Problem statement:**

> Người nghiên cứu các thị trường về AI trên Polymarket cần một cách nhất quán để xác định markets có biến động giá lớn nhất trong phạm vi đang theo dõi, mô tả diễn biến bằng dữ liệu, đặt kết quả vào bối cảnh lịch sử và nhận biết giới hạn của dữ liệu hỗ trợ nhận định.

**Giá trị hướng đến:** giảm công sức theo dõi thủ công và tăng khả năng kiểm chứng nhận định. Đây là mục tiêu sản phẩm, chưa phải hiệu quả đã được đo.

Sản phẩm mô tả và xếp hạng biến động; không mặc định gọi biến động đó là bất thường, không suy ra nguyên nhân từ tin tức và không khuyến nghị đầu tư.

## 2. Business Goals

| Mục tiêu | Giá trị cho người dùng | Bằng chứng trong MVP |
|---|---|---|
| **G1. Identify** | Thu hẹp danh sách markets cần nghiên cứu | Ranking theo absolute price change trong các cửa sổ 5, 15 và 60 phút |
| **G2. Describe** | Biết điều gì thay đổi, khi nào và mạnh đến mức nào | Giá, spread, số lần thay đổi best bid/ask, mốc thời gian và cách tính |
| **G3. Contextualize** | Đặt diễn biến trong bối cảnh rộng hơn | Daily brief gồm dữ liệu quan sát và dữ liệu lịch sử ở hai phần riêng |
| **G4. Verify** | Kiểm tra được dữ liệu hỗ trợ một nhận định | Định nghĩa KPI, outcome tạo thứ hạng, phạm vi nguồn và giới hạn đối chiếu |
| **G5. Trust** | Không nhầm dữ liệu thiếu hoặc trễ với trạng thái thị trường | Freshness, coverage, gaps và trạng thái hợp lệ theo từng KPI/cửa sổ |

**North-star outcome:** thời gian cần để một researcher xác định và mô tả tối đa ba markets đáng nghiên cứu trong một phiên quan sát.

Time to insight là mục tiêu đánh giá bổ sung. Không yêu cầu chứng minh mức cải thiện cụ thể để hoàn thành MVP và không công bố mức giảm 50% nếu chưa đo thực tế.

## 3. Stakeholders & User Decisions

Các vai trò là **persona giả định cho portfolio**, không phải danh sách khách hàng hoặc người dùng đã được phỏng vấn.

| Vai trò | Ưu tiên | Hành động được hỗ trợ |
|---|---|---|
| **Market researcher / analyst** | Chính | Chọn tối đa ba markets cần tìm hiểu, mô tả diễn biến và kiểm tra dữ liệu hỗ trợ |
| **Người theo dõi Polymarket thông thường** | Phụ | Đọc watchlist và tóm tắt dễ hiểu |
| **Reviewer của kết quả** | Vai trò kiểm chứng | Kiểm tra định nghĩa, thời gian, nguồn và cách diễn giải |
| **Người phụ trách chất lượng dữ liệu** | Vai trò kiểm soát chất lượng | Nhận biết KPI thiếu dữ liệu, kết quả trễ và phạm vi có thể công bố |

Khi nhu cầu của researcher và observer xung đột, ưu tiên tính chính xác và khả năng kiểm chứng của researcher. Các vai trò không hàm ý phải xây các trải nghiệm hoặc màn hình riêng.

### User stories chính

1. Là researcher, tôi muốn xem markets có độ lớn thay đổi giá cao nhất trong 5, 15 và 60 phút để chọn đối tượng cần nghiên cứu.
2. Tôi muốn biết outcome nào tạo ra thứ hạng và các mốc giá được sử dụng.
3. Tôi muốn xem giá, spread và nhịp thay đổi báo giá để mô tả diễn biến định lượng.
4. Tôi muốn xem bối cảnh lịch sử và biết khi nào hai nguồn không thể đối chiếu trực tiếp.
5. Tôi muốn thấy rõ kết quả trễ, gaps và KPI không đủ điều kiện, thay vì bị dẫn đến kết luận sai.

## 4. Market Universe & Observation Scope

### Chủ đề

Chỉ chọn markets về **sự kiện trực tiếp liên quan đến AI**, chẳng hạn:

- Ra mắt mô hình hoặc sản phẩm AI.
- Năng lực, kết quả đánh giá hoặc mốc phát triển của mô hình AI.
- Chính sách trực tiếp về AI.

Không mở rộng mặc định sang cổ phiếu, chip, crypto hoặc hoạt động doanh nghiệp chỉ vì có liên hệ gián tiếp với AI. Keyword là phương tiện tìm ứng viên; nội dung và điều kiện resolution cần được xem xét trước khi đưa market vào watchlist.

### Watchlist

- Cố định trong mỗi đợt thử nghiệm, **tối đa 20 markets đủ tiêu chí**.
- Nếu chỉ có ít hơn 20 markets phù hợp, dùng tập nhỏ hơn và công bố số lượng thực tế.
- Công bố danh sách, tiêu chí chọn và thời gian hiệu lực của watchlist.
- Ghi nhận market đóng hoặc không còn dữ liệu; không âm thầm thay market và không xóa lịch sử của nó.
- Phân biệt event, market và outcome/token; số lượng watchlist tính theo market.

Ranking chỉ đại diện cho watchlist đủ điều kiện, không đại diện toàn Polymarket.

### Thời gian quan sát

Near-real-time chỉ được cam kết trong những khoảng có thu thập được khai báo rõ ràng. Lịch thu cụ thể phụ thuộc kết quả thử nghiệm; chưa cam kết vận hành 24/7. Ngoài thời gian thu hoặc trong gaps, không suy ra thị trường không có biến động.

## 5. Business Questions

### Trong phiên

- Markets nào có absolute price change lớn nhất trong 5, 15 và 60 phút?
- Outcome nào tạo thứ hạng, giá tăng hay giảm và hai mốc so sánh là gì?
- Spread và số lần thay đổi best bid/ask quan sát được diễn biến ra sao?
- KPI nào đủ điều kiện công bố, KPI nào thiếu dữ liệu hoặc bị trễ?

### Theo ngày và lịch sử

- Trong các khoảng đã quan sát của ngày, diễn biến nào nổi bật?
- Giá lịch sử của các markets trong watchlist diễn biến thế nào trong phạm vi mục tiêu bảy ngày?
- Có đủ dữ liệu tương thích để đối chiếu diễn biến trong phiên với lịch sử không?
- Những khoảng thiếu dữ liệu hoặc khác biệt định nghĩa giới hạn kết luận như thế nào?

Bảy ngày là phạm vi so sánh ngắn hạn, không tự đủ để tuyên bố một xu hướng có ý nghĩa thống kê.

## 6. KPI Definitions & Ranking

| KPI | Định nghĩa nghiệp vụ | Grain / đơn vị | Giới hạn diễn giải |
|---|---|---|---|
| **Mid-price** | `(best_bid + best_ask) / 2` khi hai phía hợp lệ | Outcome × thời điểm; đơn vị giá | Giá tham chiếu từ báo giá, không phải giá giao dịch |
| **Bid-ask spread** | `best_ask - best_bid` | Outcome × thời điểm; đơn vị giá | Không tự đủ để kết luận thanh khoản thực |
| **Price change** | Chênh lệch mid-price giữa hai mốc đủ tin cậy | Outcome × 5m/15m/60m; đơn vị giá | Công bố hai mốc và quy tắc chọn giá |
| **Absolute price change** | Trị tuyệt đối của price change | Outcome × cửa sổ | Đo độ lớn, không biểu diễn hướng và không tự chứng minh bất thường |
| **Observed best-bid/ask change count** | Số lần thay đổi hợp lệ của best bid/ask thực sự quan sát được | Outcome × cửa sổ; số lần | Không phải số giao dịch, trading volume hoặc số messages orderbook |
| **Freshness** | Độ trễ từ dữ liệu hệ thống nhận được đến kết quả công bố, cùng thời gian của dữ liệu mà kết quả đại diện | Kết quả / KPI | Refresh thành công không tự chứng minh dữ liệu mới |
| **Coverage** | Phạm vi đối tượng và thời gian được quan sát theo định nghĩa đã công bố | Outcome/market × cửa sổ/phiên/ngày | Không suy ra từ số lượng quote updates đơn thuần |

### Quy tắc ranking

1. Tính KPI theo outcome/token; không tùy tiện gộp YES và NO.
2. Một dòng ranking là một market.
3. Thứ hạng dựa trên **absolute price change lớn nhất trong các outcomes đủ điều kiện** của market, xét riêng từng cửa sổ.
4. Hiển thị outcome tạo thứ hạng, price change có hướng, hai mốc giá và trạng thái chất lượng.
5. Nếu không có outcome đủ điều kiện, market không có thứ hạng trong cửa sổ đó nhưng vẫn hiện trạng thái và lý do.
6. Chấp nhận có ít hơn ba markets đủ điều kiện; không lấp danh sách bằng dữ liệu không hợp lệ.

Tuyên bố được phép: **“Biến động lớn nhất trong tập markets đủ dữ liệu đang theo dõi.”** Không thay bằng “bất thường”, “cơ hội giao dịch” hoặc “biến động do tin tức” nếu không có cơ sở riêng.

### Cơ sở thời gian và đơn vị

- **Event time:** dùng cho KPI thị trường, sau khi xác minh ý nghĩa và đơn vị timestamp của nguồn.
- **Observed time:** thời điểm hệ thống nhận dữ liệu, dùng đánh giá độ trễ.
- **Published time:** thời điểm công bố kết quả.
- Không âm thầm thay event time thiếu/không hợp lệ bằng observed time trong cùng ranking.
- Phân biệt chênh lệch giá tuyệt đối với thay đổi tương đối. Ví dụ, `0.40 → 0.45` là `+0.05` đơn vị giá, không gọi là tăng 5% nếu không định nghĩa cách biểu diễn.

## 7. Data Quality & Publication Rules

Điều kiện hợp lệ được xét **riêng theo KPI và theo cửa sổ**, không chỉ theo toàn market.

| Trường hợp | Hành vi nghiệp vụ |
|---|---|
| Không có quote update nhưng việc quan sát vẫn hoạt động và trạng thái báo giá còn tin cậy | Không tự động kết luận thiếu dữ liệu hoặc coi báo giá không đổi là stale |
| Hai mốc giá đủ tin cậy, nhưng có gap ở giữa | Price change có thể được ranking; gap vẫn hiển thị, không tuyên bố biết đầy đủ đường đi của giá |
| Khoảng đếm có gap chưa được khắc phục | Quote change count không được trình bày như số đếm đầy đủ |
| Thiếu mốc giá hoặc trạng thái báo giá không đủ tin cậy | Loại KPI khỏi ranking tương ứng; hiện lý do |
| Có dữ liệu cho 5 phút nhưng chưa đủ cho 60 phút | Chỉ công bố cửa sổ đủ điều kiện; không đổi nhãn cửa sổ |
| Không có dữ liệu | Không thay bằng số 0 hoặc suy ra không có hoạt động |
| Có snapshot sau reconnect | Không mặc định coi chênh lệch qua gap là các thay đổi báo giá đã quan sát đầy đủ |

“Phép tính trả về một con số” không đủ chứng minh KPI hợp lệ. Quy tắc chọn mốc giá, bằng chứng về trạng thái báo giá, xử lý sự kiện đến muộn và điều kiện coverage cụ thể phải được xác định trong data requirements và kiểm thử trước khi công bố.

## 8. Reporting & Freshness Requirements

### Session Watchlist & Market Detail

- Ranking 5/15/60 phút, outcome tạo thứ hạng và diễn biến định lượng.
- Mục tiêu công bố kết quả **trong vòng một phút sau khi hệ thống nhận dữ liệu**, chỉ trong khoảng đang thu; cần kiểm chứng thực tế.
- Khi trễ, giữ kết quả gần nhất với nhãn `delayed / stale` rõ ràng và các mốc thời gian thật.
- Không cập nhật giả thời gian kết quả khi chưa có kết quả mới.
- Khi phiên kết thúc, hiển thị “phiên đã kết thúc”, không tiếp tục trình bày như đang live.

### Daily Market Brief

Hai phần phải được phân biệt:

1. **Daily observed summary:** tổng hợp dữ liệu hệ thống thực sự quan sát, gồm phạm vi thời gian và gaps.
2. **Historical context:** dữ liệu lịch sử từ nguồn ngoài, kèm định nghĩa, nguồn và phạm vi riêng.

Báo cáo xuất hiện sau khi xử lý hoàn tất, có trạng thái `pending / partial / complete`. Chưa cam kết một giờ T+1 cố định. “Complete” nghĩa là hoàn tất phạm vi đã công bố, không mặc nhiên là quan sát đầy đủ cả ngày.

### Historical Context

- Phạm vi mục tiêu đầu tiên: **bảy ngày**, với coverage thực tế riêng cho từng market.
- Không tạo số 0 cho thời gian trước khi market tồn tại hoặc khi nguồn thiếu dữ liệu.
- Chỉ đối chiếu trực tiếp khi outcome, thời gian và định nghĩa KPI đủ tương thích.
- Nếu chưa chứng minh tính tương thích, trình bày hai góc nhìn bổ trợ; không tự coi chênh lệch là lỗi pipeline.

### Thông tin chất lượng đi kèm

Mỗi kết quả quan trọng cần có market/outcome, cửa sổ hoặc phạm vi ngày, giá trị và đơn vị KPI, mốc thời gian, nguồn, freshness, coverage, gaps và trạng thái hợp lệ. Không bắt buộc xây một màn hình Data Health riêng nếu các thông tin này được thể hiện đầy đủ trong báo cáo.

## 9. Scope, Constraints & Non-Goals

### In scope — MVP

- Watchlist cố định, tối đa 20 markets trực tiếp về AI.
- Ranking market theo price change ở mức outcome, trong 5/15/60 phút.
- Chi tiết mid-price, spread và observed best-bid/ask change count.
- Một luồng theo dõi trong phiên, daily brief và bối cảnh lịch sử mục tiêu bảy ngày.
- Minh bạch chất lượng, nguồn và giới hạn diễn giải.

### Nguồn lực và thử nghiệm

- Microsoft Fabric trial **F64**, theo thông tin người thực hiện cung cấp, cùng Azure for Students là nguồn lực dự kiến.
- Không có deadline cá nhân cứng; ưu tiên chất lượng phù hợp fresher DE. Môi trường trial vẫn có thời hạn riêng.
- Chấp nhận chỉ vận hành cloud vài ngày để thử nghiệm và tạo bằng chứng portfolio; không bắt buộc duy trì dịch vụ online lâu dài.
- Thử nghiệm theo bậc: chạy ngắn để kiểm tra, chạy cấu hình cố định khoảng 24 giờ, sau đó kéo dài nếu đạt điều kiện.
- Đo mức sử dụng Fabric capacity riêng với chi phí/credit của các dịch vụ Azure; không coi hai cơ chế là một ví credit chung.

### Historical source & data budget

- **pmxt archive v2 là nguồn lịch sử ứng viên**, chưa phải nguồn đã xác minh đáp ứng toàn bộ yêu cầu.
- Có thể chấp nhận đọc dư rồi lọc nếu tổng lượng tải nằm trong giới hạn và kết quả thử nghiệm chứng minh giá trị.
- Theo phạm vi câu hỏi ngân sách nguồn, trần đề xuất đã thống nhất là **1 GB dữ liệu tải từ pmxt mỗi ngày**, không phải kích thước bảng sau lọc hay mục tiêu phải đạt.
- Các lượt đọc metadata và đọc lại/retry cần được tính trong phép đo; cách đo bytes và ranh giới ngày phải xác định trước thử nghiệm.
- Bảy ngày lịch sử không có nghĩa phải lấy xong trong một ngày. Khả năng lấy đủ trong giới hạn cần kiểm chứng.
- Nếu vượt giới hạn, dừng để đánh giá lại nguồn hoặc phạm vi; không âm thầm tăng ngân sách.
- Mức 500 MB–2 GB/ngày trong bản v1.0 không còn được dùng làm chỉ tiêu ingest cần đạt.

### Out of scope — MVP

- Đặt lệnh, tự động giao dịch, khuyến nghị đầu tư hoặc bảo đảm lợi nhuận.
- Phát hiện bất thường đã được xác thực hoặc giải thích nguyên nhân biến động bằng tin tức.
- Bao phủ toàn Polymarket và toàn bộ orderbook events 24/7.
- Dùng quote change count làm trading volume.
- Một deployment target thứ hai hoặc hệ thống offline đầy đủ.

Việc chọn kiến trúc, nơi chạy collector, item types, engine xử lý và môi trường triển khai nằm ngoài tài liệu này. Những phương án từng được nêu trong trao đổi kỹ thuật chưa được phê duyệt.

## 10. MVP Acceptance Criteria & Portfolio Evidence

Một demo đạt yêu cầu khi người xem có thể:

1. Xem ranking 5/15/60 phút của watchlist được công bố, hoặc thấy lý do cửa sổ chưa đủ dữ liệu.
2. Chọn tối đa ba markets đủ điều kiện và xác định outcome, độ lớn/hướng biến động, hai mốc giá và cách tính.
3. Xem giá, spread và nhịp thay đổi best bid/ask với trạng thái chất lượng tương ứng.
4. Xem daily brief và bối cảnh lịch sử; biết khi nào không thể đối chiếu hoặc kết luận.
5. Nhận biết kết quả trễ, gaps và KPI không đủ điều kiện, không nhầm dữ liệu thiếu với số 0.
6. Kiểm tra định nghĩa KPI, universe, thời gian, nguồn và bằng chứng của kết quả.

**Điều kiện bắt buộc:** hoàn thành kịch bản nghiệp vụ có thể kiểm chứng trên.

**Đánh giá bổ sung:** thử nghiệm người dùng, đo time to insight và thu phản hồi. Không yêu cầu các hoạt động này để tuyên bố hoàn thành MVP; không công bố tác động chưa đo.

**Bằng chứng giữ lại sau đợt chạy cloud:**

- Code và tài liệu giải thích phạm vi, định nghĩa, kết quả và giới hạn.
- Video demo.
- Source và định nghĩa Fabric items theo định dạng native trong Git integration: `.platform` dạng JSON **cùng** các file định nghĩa riêng theo item. `.platform` không thay thế toàn bộ code/định nghĩa và Git không được coi là bản sao dữ liệu vận hành.

Lưu bằng chứng trước khi trial hết hạn. Không cam kết reviewer có thể chạy toàn bộ project mà không có môi trường Fabric phù hợp.

## 11. Open — Những điều cần kiểm chứng trước triển khai

| Nội dung | Cần xác minh |
|---|---|
| Universe AI | Có những markets nào đáp ứng nội dung, trạng thái và khả năng quan sát cần thiết? |
| Timestamp | Ý nghĩa, đơn vị và tính tương thích của event time từng nguồn |
| Trạng thái báo giá | Điều kiện đủ tin cậy để chọn mốc giá, xử lý gaps/reconnect và sự kiện đến muộn |
| Quote change count | Cách phân biệt thay đổi thực sự đã quan sát với snapshot, duplicate hoặc đọc lại |
| pmxt v2 | Khả năng truy cập, schema, IDs, timestamps và khả năng tái dựng KPI cần dùng |
| Đọc chọn lọc | Có giảm bytes thực tế truyền/đọc không, hay chỉ giảm số dòng đầu ra? |
| Lịch sử bảy ngày | Có đủ dữ liệu cho watchlist trong trần 1 GB/ngày không? |
| Historical comparison | Có cùng định nghĩa giá/spread và phạm vi thời gian để đối chiếu trực tiếp không? |
| Freshness một phút | Mức đạt được trong đợt thử nghiệm và cách đo tỷ lệ đáp ứng |
| Nguồn lực cloud | Thời hạn trial, quyền/điều kiện tài khoản và chi phí các tài nguyên thực sự cần dùng |
| Chia sẻ dữ liệu | Điều kiện sử dụng nguồn trước khi công khai dữ liệu mẫu hoặc kết quả |

Các mục Open không phải kết quả đã đạt hoặc cam kết của nguồn. Thay đổi ảnh hưởng business scope phải được thảo luận, không âm thầm thay thế yêu cầu.

## 12. One-page Business Brief

| Hạng mục | Nội dung đã thống nhất |
|---|---|
| **Problem** | Khó xác định, mô tả và kiểm chứng diễn biến giữa nhiều markets AI |
| **Primary user** | Researcher / analyst; observer là người dùng phụ |
| **Action** | Chọn tối đa ba markets cần nghiên cứu và kiểm tra dữ liệu hỗ trợ |
| **Universe** | Watchlist cố định, tối đa 20 markets trực tiếp về AI |
| **Ranking** | Mỗi market một dòng; lấy absolute price change lớn nhất trong outcomes đủ điều kiện |
| **Windows** | 5, 15 và 60 phút; dùng event time |
| **Core metrics** | Mid-price, price change, spread, observed best-bid/ask change count, freshness và coverage |
| **Historical view** | Mục tiêu bảy ngày; observed summary và nguồn lịch sử tách biệt |
| **Freshness** | Mục tiêu một phút trong khoảng đang thu, cần test; báo cáo ngày sau xử lý, chưa cam kết giờ cố định |
| **Quality rule** | Xét từng KPI/cửa sổ; loại kết quả không hợp lệ khỏi ranking, vẫn hiển thị gaps/lý do |
| **Resources** | Fabric trial F64 và Azure for Students; chạy thử hữu hạn |
| **Archive budget** | Trần tải pmxt 1 GB/ngày; không phải chỉ tiêu ingest |
| **Completion** | Demo nghiệp vụ kiểm chứng được; không yêu cầu chứng minh tác động chưa đo |
| **Evidence** | Code, tài liệu, video và native Fabric item definitions trong Git |

**Nguyên tắc:** bắt đầu từ quyết định của người dùng, định nghĩa dữ liệu và giới hạn diễn giải; chọn kiến trúc ở bước riêng.

## References

- Business Problem Definition v1.0 do người thực hiện cung cấp; v1.1 phản ánh các quyết định business đã xác nhận trong phiên trao đổi ngày 2026-10-13.
- [Polymarket — Markets & Events](https://docs.polymarket.com/concepts/markets-events): phân biệt event, market và outcome.
- [Polymarket — Prices & Order Books](https://docs.polymarket.com/market-data/prices-order-books): giá/báo giá và lịch sử; không mặc định historical price có cùng định nghĩa với mid-price tự tính.
- [Polymarket — Realtime Data](https://docs.polymarket.com/market-data/realtime-data): phân biệt book updates và best-bid/ask events.
- [Microsoft Learn — Fabric trial capacity](https://learn.microsoft.com/en-us/fabric/fundamentals/fabric-trial): trial có thời hạn; không dùng như cam kết vận hành dài hạn.
- [Azure — Azure for Students offer](https://azure.microsoft.com/en-us/pricing/offers/ms-azr-0170p/): cơ chế credit riêng; không tự xác nhận điều kiện mua Fabric capacity của tài khoản.
- [Microsoft Learn — Git source code format](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/source-code-format): metadata `.platform` và định nghĩa riêng theo item.

Các tài liệu sản phẩm đã được tham khảo trong phiên; tính khả thi thực tế vẫn cần kiểm chứng. Tài liệu pmxt v2 chính thức chưa truy cập được trong phiên, nên không dùng thông tin chưa xác minh để phê duyệt nguồn hoặc phương pháp lấy dữ liệu.
