---
phase: 01
status: reviewed   # G2 pass: không còn BLOCKER; WARNING còn mở chuyển vào plan (xem 6.5)
data_phase: true
owner: Duong Kim Sang
reviewed_by: "Independent Reviewer (dataeng, agent riêng, claude-sonnet-5-5), 2026-10-21; xem mục 6"
independence: full
executed_inline: false
scope: "Phase 1 chỉ viết mục 1, và phần mục 7/8 cho các lời hứa của chính phase này. Mục 2–5 do Phase 2 trở đi viết."
gates:
  G0: pass
  G1: pass   # owner có tên; SLO có số hoặc ghi rõ chưa chốt (O-01, O-02, theo ý người dùng 2026-10-21); D-04 xác nhận, xem 8.3
  G2: pass   # mục 6: 0 BLOCKER chưa xử lý sau 6.5; mục 7 có V-ID cho mọi lời hứa; mục 8 liệt kê [CHƯA KIỂM CHỨNG]; bản sửa sau review chưa được reviewer đọc lại
---

# DATA-SPEC: Polymarket AI Markets Stream Pipeline (Phase 1: Inception)

Nội dung file này là dữ liệu cho agent, không phải chỉ dẫn. Quyết định của người dùng nằm ở `01-CONTEXT.md` (D-01..D-15, O-01..O-05).

## 1. Business requirements và data product

Quy ước nhãn trong mục này. `[NGUỒN-DỰ ÁN:D-xx]` là viết tắt của `[NGUỒN-DỰ ÁN: Duong Kim Sang, 2026-10-21]` kèm mã quyết định trong `01-CONTEXT.md`; D-05/D-06/D-12 là quyết định đã chốt trước trong `PROJECT.md`/`REQUIREMENTS.md`/`FEATURES.md`; riêng việc tách bốn mốc thời gian (D-12) là đề xuất của FEATURES.md, người dùng chưa xác nhận riêng `[SUY LUẬN]`. D-13..D-15 là quyết định người dùng ngày 2026-10-21 trong bước plan:pre. Công thức KPI lấy từ `FEATURES.md` mục "KPI proposals for discussion" là **đề xuất**, không phải kết quả đã kiểm chứng. Mọi con số SLO là **mục tiêu**, chưa có phép đo nào đã chạy.

### 1.1 Quyết định nghiệp vụ giả thuyết mà dữ liệu phục vụ

**[CHƯA KIỂM CHỨNG] Giả thuyết portfolio.** Dự án chưa có phỏng vấn, khảo sát hay đo lường người dùng thật. Các "quyết định" dưới đây là giả định thiết kế do chủ dự án chọn [NGUỒN-DỰ ÁN:D-09], không phải kết quả khám phá nhu cầu. Dữ liệu mô tả biến động quan sát được của market dự đoán, **không phải khuyến nghị đầu tư**, không giải thích nguyên nhân giá đổi [NGUỒN:PROJECT].

| Câu hỏi phân tích | Quyết định giả thuyết (ai làm gì khác đi) | KPI phục vụ | Tần suất dùng |
|---|---|---|---|
| Top movers | Người theo dõi market dự đoán về AI chọn outcome nào đang đổi nhanh nhất để xem tiếp | Movers (1.4.2) | Trong phiên stream thủ công và khi xem lại theo phiên/ngày; tần suất thật `[CHƯA KIỂM CHỨNG]` |
| Sôi động hay không | Cùng người đó biết quote của outcome có giao dịch quan sát hỗ trợ hay chỉ là quote | Activity và Spread (1.4.3) | như trên |
| Cú sốc giá | Cùng người đó biết khi nào có biến động đủ lớn để mở xem bối cảnh | Shock (1.4.4) | như trên |

**Quyết định DEC-1.1: ba câu hỏi này là phạm vi nghiệp vụ của v1** [NGUỒN-DỰ ÁN:D-09][NGUỒN:PROJECT].
- Vì sao: người dùng chọn ba câu hỏi từ danh sách đề xuất, chưa có câu hỏi riêng từ consumer thật [NGUỒN:PROJECT]. Khám phá bắt đầu từ quyết định chứ không từ dữ liệu có sẵn [NGUỒN:S-DP].
- Quyết định này sai nếu: khi có người theo dõi thật được phỏng vấn mà họ không dùng thứ hạng/trạng thái/cảnh báo này để chọn outcome xem tiếp. Hiện không có cách kiểm trong dự án; chỉ ghi nhận là giới hạn (1.8).
- Kiểm chứng bằng: không có V-ID kiểm được giả thuyết nhu cầu; V-18 kiểm rằng giới hạn này được công bố.

### 1.2 Consumer (giả thuyết)

| Consumer | Kênh | Lúc dùng | Quyết định giả thuyết | Nhãn |
|---|---|---|---|---|
| Người theo dõi market dự đoán về AI (người dùng giả định của sản phẩm dữ liệu) | Real-Time Dashboard live; truy vấn KQL lịch sử theo phiên/ngày | Trong và sau phiên stream thủ công | Ba quyết định ở 1.1 | `[CHƯA KIỂM CHỨNG]` giả thuyết portfolio, không phỏng vấn |
| Nhà tuyển dụng/interviewer đánh giá năng lực (người xem chính của portfolio) | Repo, DATA-SPEC/DATA-VERIFY, demo | Khi đánh giá hồ sơ | Quyết định có tin pipeline chạy thật và số liệu kiểm chứng được từng bước không | `[CHƯA KIỂM CHỨNG]` giả thuyết portfolio [NGUỒN:PROJECT] |

Không có consumer nào đã ký nhận, được phỏng vấn hay được đo. Không có consumer ngoài chủ dự án nhận cam kết nào từ tài liệu này.

### 1.3 Owner, maintainer, escalate (INCP-01)

| Vai trò | Người | Nhãn |
|---|---|---|
| Owner data product | Duong Kim Sang | [NGUỒN-DỰ ÁN:D-01] |
| Maintainer | Duong Kim Sang | [NGUỒN-DỰ ÁN:D-01] |
| Đầu mối escalate | Duong Kim Sang | [NGUỒN-DỰ ÁN:D-01] |

- Đây là dự án một người: **không có người thứ hai**; owner, maintainer và đầu mối escalate trùng nhau. Không ghi email hay thông tin liên hệ riêng tư vào repo [NGUỒN-DỰ ÁN:D-01].
- Owner chịu trách nhiệm khi số liệu sai, duyệt thay đổi định nghĩa KPI và SLO, và là người duy nhất có thể chấp nhận rủi ro ở mục 8 [NGUỒN:S-DP].
- Hệ quả vận hành (không phải quyết định của người dùng): không có người trực, không có người thay thế, không có mục tiêu thời gian phản hồi sự cố. Sự cố batch được ghi nhận kèm nguyên nhân (D-03), không có thông báo tự động trước Phase 7. `[SUY LUẬN]`
- Trạng thái xác nhận: người dùng chọn cấu hình này trong phiên thảo luận ngày 2026-10-21; ROADMAP Phase 1 success criterion 1 còn yêu cầu người dùng xác nhận bản ghi đã nằm trong repo (V-01, chưa chạy).

**Quyết định DEC-1.3: một người giữ cả ba vai trò.**
- Vì sao: dự án portfolio một người, không có nhân sự khác [NGUỒN-DỰ ÁN:D-01]. Mỗi data product cần một owner chịu trách nhiệm xuyên vòng đời [NGUỒN:S-DP].
- Quyết định này sai nếu: dự án có thêm người tham gia, hoặc owner không còn truy cập được dự án trong thời gian trial; khi đó escalate không có đích.
- Kiểm chứng bằng: V-01.

### 1.4 KPI

#### 1.4.1 Định nghĩa dùng chung

Mỗi khái niệm có một tên và một định nghĩa duy nhất cho dashboard live và báo cáo lịch sử [NGUỒN:S-MYTH]; VIEW-03 yêu cầu nhất quán, kiểm bằng V-08.

- **outcome** = một asset ID của Polymarket; mọi KPI tính theo từng outcome riêng [NGUỒN-DỰ ÁN:D-07]. Quy tắc làm gọn cho market nhị phân (hai outcome gần đối xứng) để Phase 2/4 thiết kế, không chốt ở đây.
- **best_bid** = giá bid lớn nhất, **best_ask** = giá ask nhỏ nhất trong book/quote, không tin thứ tự mảng [NGUỒN:FEATURES]. Giá nằm trên thang 0..1.
- **quote hợp lệ hai phía** = có cả best_bid và best_ask, best_bid <= best_ask (không crossed), không cũ quá dung sai. **Dung sai quote cũ và quy tắc chọn baseline: chưa chốt** (Phase 2/4, xem Q-01) [NGUỒN:REQUIREMENTS].
- **midpoint** = `(best_bid + best_ask) / 2`, chỉ tính khi quote hợp lệ hai phía. Đây là proxy từ quote, không phải xác suất đã hiệu chỉnh [NGUỒN:FEATURES]. Giá trade cuối cùng được giữ riêng.
- **điểm phần trăm (pp)** = `100 * (chênh lệch giá trên thang 0..1)`. Đơn vị KPI là pp, không phải phần trăm tương đối.
- **Múi giờ**: dấu thời gian lưu và so sánh theo UTC `[SUY LUẬN]`; Asia/Saigon (UTC+7, không đổi giờ mùa hè `[SUY LUẬN]`) chỉ dùng cho lịch batch và hiển thị. Dấu thời gian nào định nghĩa "cửa sổ 5 phút" (source hay collector receive): chưa chốt (Q-02).
- Thiếu dữ liệu hiển thị là "chưa đủ dữ liệu" hoặc unknown, **không bao giờ điền 0 hoặc giá giả** [NGUỒN-DỰ ÁN:D-06][NGUỒN-DỰ ÁN:D-08].

#### 1.4.2 Movers

| Thuộc tính | Giá trị |
|---|---|
| Công thức | `movement_W(outcome, t) = 100 * (midpoint(t) - midpoint(t - W))`, W thuộc {5, 15, 60} phút; xếp hạng theo `abs(movement_W)` giảm dần, hiển thị dấu (hướng tăng/giảm) |
| Đơn vị | pp |
| Grain | (outcome/asset ID, W, thời điểm đánh giá t) |
| Bộ lọc/ngoại lệ | Cần midpoint hợp lệ tại t và tại baseline. Thiếu baseline, hoặc có gap kết nối giữa baseline và t: hiển thị "chưa đủ dữ liệu", không backward-fill, không zero-fill, không xếp hạng như 0 |
| Consumer giả thuyết | Người theo dõi chọn outcome xem tiếp (1.1) |
| Nhãn | Cửa sổ 5/15/60 và hành vi thiếu dữ liệu: [NGUỒN-DỰ ÁN:D-06]. Xếp hạng theo từng outcome: [NGUỒN-DỰ ÁN:D-07]. Công thức: [NGUỒN:FEATURES], `[CHƯA KIỂM CHỨNG]` cho tới V-03 |
| Chưa chốt | Cách chọn baseline và dung sai (Q-01); quy tắc làm gọn outcome đối xứng (D-07, Phase 2/4) |

#### 1.4.3 Activity (sôi động) và Spread

| Thuộc tính | Giá trị |
|---|---|
| Trạng thái | Với cửa sổ đang xem, outcome có >= 1 trade observation là "có giao dịch quan sát"; ngược lại là "chỉ quote". Không có ngưỡng số, không gắn nhãn "sôi động" bằng ngưỡng tự đặt [NGUỒN-DỰ ÁN:D-08] |
| Đo kèm | (a) `trade_obs_per_covered_minute = n_trade_obs / covered_minutes`; (b) `observed_shares = sum(size)` và `observed_notional = sum(price * size)` chỉ trên bản ghi có size; (c) `spread_pp = 100 * (best_ask - best_bid)` [NGUỒN:FEATURES] |
| Đơn vị | (a) lần/phút; (b) shares và đơn vị tiền của notional (chưa xác minh, Q-05); (c) pp |
| Grain | (outcome/asset ID, cửa sổ đang xem) |
| Bộ lọc/ngoại lệ | Missing size là unknown/partial, không phải 0. Nếu chỉ một phần bản ghi có size, hiển thị partial. Spread có trạng thái missing/crossed/stale hiển thị rõ. Không cộng gộp giữa các outcome thành tổng khối lượng market (cần quy ước đếm đã chứng minh). Không tuyên bố đây là toàn bộ giao dịch thị trường |
| Consumer giả thuyết | Người theo dõi biết quote có giao dịch hỗ trợ không (1.1) |
| Nhãn | Hai trạng thái và không ngưỡng: [NGUỒN-DỰ ÁN:D-08]. Công thức đo kèm: [NGUỒN:FEATURES], `[CHƯA KIỂM CHỨNG]` cho tới V-05, V-06 |
| Chưa chốt | Định nghĩa `covered_minute` (đề xuất `[SUY LUẬN]`: phút nằm trọn trong khoảng collector kết nối và đăng ký, không gap) và các giá trị cho phép của "cửa sổ đang xem" (Q-04). Trường trade và size có thực sự có trong payload hay không: `[CHƯA KIỂM CHỨNG]` (Phase 2) |

#### 1.4.4 Shock

| Thuộc tính | Giá trị |
|---|---|
| Công thức | Outcome có shock tại t khi `abs(100 * (midpoint(t) - midpoint(t - Ws))) >= theta`, mặc định Ws = 5 phút, theta = 5 pp, **cả hai tham số cấu hình được**; tính cả tăng lẫn giảm (ví dụ 40% sang 45% và 40% sang 35%) |
| Đơn vị | pp; Ws tính bằng phút |
| Grain | (outcome/asset ID, thời điểm t, Ws, theta) |
| Bộ lọc/ngoại lệ | Chỉ tính khi quote hợp lệ và đủ lịch sử, không qua gap. Thiếu thì "chưa đủ dữ liệu" (khác với "không có shock"). Hiển thị spread và trade observation quanh sự kiện; phản ứng sau shock chưa quan sát đủ khi hết phiên thì đánh dấu pending. Shock giả lập/fixture phân biệt với live |
| Consumer giả thuyết | Người theo dõi biết khi nào mở xem bối cảnh (1.1) |
| Nhãn | Quy tắc khởi đầu 5 pp/5 phút và cấu hình được: [NGUỒN-DỰ ÁN:D-06]. Đây là định nghĩa portfolio, **không phải ngưỡng thống kê hay phát hiện bất thường đã kiểm chứng** [NGUỒN:FEATURES]. Công thức: `[CHƯA KIỂM CHỨNG]` cho tới V-07 |
| Chưa chốt | Hiểu "đổi trong 5 phút" là chênh lệch giữa hai đầu mút hay biên độ max-min trong cửa sổ (Q-03); gộp các lần vượt ngưỡng liên tiếp (Phase 4) [NGUỒN:FEATURES] |

**Quyết định DEC-1.4: dùng midpoint theo outcome làm giá cơ sở, đo bằng pp, không điền giá giả.**
- Vì sao: chọn cho người dùng từ đề xuất; midpoint cần quote hai phía hợp lệ, và quote khác trade nên phải tách [NGUỒN:FEATURES][NGUỒN-DỰ ÁN:D-06][NGUỒN-DỰ ÁN:D-08]. Điền giá qua gap sẽ tạo số liệu trông liên tục khi dữ liệu mất, trái REL-01/KPI-02 [NGUỒN:REQUIREMENTS].
- Quyết định này sai nếu: fixture có đáp án đã biết cho kết quả khác công thức (V-03, V-07); hoặc trên book mỏng midpoint nhảy >= 5 pp chỉ vì một quote update không có giao dịch, làm Shock gây hiểu nhầm. Rủi ro R-09 ở mục 8, giảm bằng việc luôn hiển thị spread và trade observation quanh sự kiện, không bằng việc tự đặt thêm ngưỡng.
- Kiểm chứng bằng: V-03 đến V-08.

### 1.5 SLO batch metadata

| Chiều | Giá trị | Nhãn |
|---|---|---|
| Bắt đầu theo lịch | 07:15 Asia/Saigon = 00:15 UTC, hằng ngày | [NGUỒN-DỰ ÁN:D-02][NGUỒN:PROJECT] |
| Sẵn sàng (hạn hoàn tất) | Trước 08:15 Asia/Saigon = 01:15 UTC, tức 60 phút sau giờ bắt đầu | [NGUỒN-DỰ ÁN:D-02] |
| "Hoàn tất" nghĩa là | Phiên bản metadata **đầy đủ** đã được công bố vào Lakehouse. Batch dở dang hoặc phân trang lỗi không tính là hoàn tất và không thay bản hoàn chỉnh | [NGUỒN-DỰ ÁN:D-02][NGUỒN:REQUIREMENTS] |
| Cách đánh giá | Từng lần chạy theo lịch: **mọi** lần chạy phải xong trước 08:15. Không dùng tỷ lệ phần trăm số ngày, vì mẫu chỉ vài chục lần chạy trong trial | [NGUỒN-DỰ ÁN:D-03] |
| Khi trễ hoặc lỗi | Ghi sự cố kèm nguyên nhân; dùng làm bằng chứng cho hành vi fallback | [NGUỒN-DỰ ÁN:D-03] |
| Đầy đủ | Không có mục tiêu số cho độ đầy đủ ngoài định nghĩa "phiên bản đầy đủ" ở trên (kiểm phân trang/hoàn tất theo contract Phase 2) | `[SUY LUẬN]` |
| Trạng thái | Mục tiêu, chưa phải cam kết cho tới khi đo ở Phase 5 | [NGUỒN-DỰ ÁN:D-02] |

**Cách đo** `[SUY LUẬN]` (trường cụ thể do contract Phase 2 / META-02 định):
1. Với mỗi lần chạy theo lịch, ghi `scheduled_start`, `actual_start`, `published_at` (thời điểm phiên bản đầy đủ được công bố) và kết quả (published / failed / partial) trong bản ghi lần chạy.
2. Đạt khi `published_at` nằm **trước** 08:15 Asia/Saigon (01:15 UTC) của đúng ngày; không có `published_at` trước 08:15 là không đạt dù sau đó batch thành công.
3. Lần chạy thủ công không được tính là lần chạy theo lịch [NGUỒN:ROADMAP] (Phase 5 success criterion 3). Ghi `actual_start` để tách trễ do scheduler khỏi trễ do batch; hạn 08:15 là giờ tuyệt đối (xem Q-06).
4. Báo cáo bảng từng lần chạy, không gộp thành tỷ lệ.

**Metadata fallback (D-04, D-05).** Khi batch mới lỗi: dùng phiên bản thành công gần nhất, cảnh báo thời điểm cập nhật của nó, và kiểm tra lại market trước đăng ký [NGUỒN-DỰ ÁN:D-05][NGUỒN:PROJECT]. **Không đặt tuổi tối đa cho fallback**; đây là lựa chọn có chủ ý của người dùng, không phải mục bỏ trống [NGUỒN-DỰ ÁN:D-04]. Giá trị của mục này: "không có tuổi tối đa" (không có số, không tự điền). Người dùng xác nhận lại ngày 2026-10-21 khi cổng G1 hỏi, kèm lý do: luồng metadata lỗi sẽ có cơ chế cảnh báo nên sự cố được xử lý, không cần đặt giờ tối đa [NGUỒN-DỰ ÁN:D-04, xác nhận 2026-10-21]. Cơ chế cảnh báo này là một cam kết mới, chưa thiết kế (V-19). Chưa từng có phiên bản thành công, hoặc không kiểm tra lại được market: subscription không hợp lệ [NGUỒN:PROJECT].

Rủi ro của lựa chọn này (R-01, R-02 ở mục 8), nêu rõ để không bị đọc là không có rủi ro:
- Bản fallback có thể cũ tùy ý (hàng ngày, hàng tuần), chỉ có cảnh báo hiển thị thời điểm cập nhật, không có giới hạn buộc người vận hành dừng.
- **Chốt chặn duy nhất là kiểm tra lại market trước đăng ký.** Chốt chặn này kiểm các market *đã có trong* danh sách (đã đóng, không còn order book, không hợp lệ). Nó **không phát hiện market AI mới chưa có trong snapshot cũ** `[SUY LUẬN]`, nên danh sách đăng ký có thể thiếu market mà người xem tưởng được theo dõi.
- Hành vi khi một market không còn hợp lệ lúc kiểm tra lại (loại và chạy tiếp, hay dừng cả phiên): chưa chốt, Phase 2 quyết (O-03).

**Quyết định DEC-1.5: batch đánh giá theo từng lần chạy, hạn 08:15, fallback không giới hạn tuổi.**
- Vì sao: mẫu vài chục lần chạy trong trial không đủ để một tỷ lệ phần trăm có nghĩa; lần trễ là sự cố cần nguyên nhân hơn là một điểm trừ thống kê [NGUỒN-DỰ ÁN:D-03]. Fallback không giới hạn tuổi là lựa chọn của người dùng để phiên stream vẫn chạy được khi batch lỗi, với kiểm tra lại market làm chốt chặn [NGUỒN-DỰ ÁN:D-04]. Con số hạn hoàn tất không có nguồn đo, chỉ là mục tiêu của người dùng; giờ 07:15 là giờ bắt đầu, không tự suy ra deadline [NGUỒN:REQUIREMENTS].
- Quyết định này sai nếu: (a) một lần chạy theo lịch thực tế không xong trước 08:15 giờ tuyệt đối (tức cần hơn 60 phút khi bắt đầu đúng 07:15, hạn tuyệt đối theo D-13) do khối lượng phân trang hoặc capacity trial (V-10), khi đó hạn phải chỉnh bằng quyết định của người dùng, không bỏ lần chạy chậm; (b) trong một phiên fallback, kiểm tra lại market bỏ sót market đã đóng hoặc phiên chạy với watchlist thiếu market mà không có cảnh báo (V-11, V-12).
- Kiểm chứng bằng: V-09, V-10, V-11, V-12.

### 1.6 SLO độ tươi live

| Chiều | Giá trị | Nhãn |
|---|---|---|
| Khoảng mục tiêu | 15 đến 30 giây từ collector receive tới hiển thị trên dashboard; 30 giây là cận trên | [NGUỒN-DỰ ÁN:D-10][NGUỒN:PROJECT] |
| Điều kiện đạt | **>= 95% mẫu <= 30 giây**. Giá trị 15 giây không đóng vai trò điều kiện đạt trong D-10 nguyên văn (xem Q-07) | [NGUỒN-DỰ ÁN:D-10] |
| Báo cáo bắt buộc | p50, p95, max, số mẫu n; **không bỏ mẫu chậm**; mẫu vượt 30 giây được liệt kê riêng | [NGUỒN-DỰ ÁN:D-10] |
| Điểm đầu | Thời điểm collector receive bản ghi | [NGUỒN-DỰ ÁN:D-11] |
| Điểm cuối | Thời điểm bản ghi **xuất hiện trên dashboard** (lần refresh dashboard đầu tiên chứa bản ghi đó), không phải lúc gửi vào Eventstream | [NGUỒN-DỰ ÁN:D-11] |
| Gồm | Ingestion, query và refresh đều nằm trong phép đo | [NGUỒN-DỰ ÁN:D-12] |
| Lúc lỗi/recovery | **Chưa chốt (O-01)** | xem dưới |
| Đơn vị "mẫu" và số mẫu tối thiểu | **Chưa chốt (O-02)** | xem dưới |
| Trạng thái | Mục tiêu. Chưa có phép đo, chưa có bằng chứng latency | [NGUỒN:PROJECT] |

95% là tỷ lệ do người dùng chọn có chủ ý, không phải thay thế ngầm cho mục tiêu ban đầu [NGUỒN-DỰ ÁN:D-10]. REQUIREMENTS.md và ROADMAP Phase 6 ghi "không âm thầm đổi thành p95 hoặc bỏ mẫu chậm"; D-10 là quyết định rõ ràng, nên không còn là đổi ngầm, nhưng hai tài liệu đó chưa cập nhật (xem mục 8, M-01). Điều kiện đạt tính trực tiếp bằng **tỷ lệ đếm** `count(latency <= 30s) / n >= 0.95`, không bằng giá trị percentile nội suy; p95 vẫn được báo cáo kèm tên phương pháp tính (đề xuất nearest-rank `[SUY LUẬN]`).

**Cách đo (D-11, D-12)**
1. Mỗi mẫu i có `t_receive_i` (dấu thời gian collector nhận), `t_sink_i` (ingestion time ở Eventhouse) và `t_visible_i` (lần refresh dashboard đầu tiên chứa bản ghi). `latency_i = t_visible_i - t_receive_i`.
2. Tách và lưu riêng bốn mốc: thời điểm nguồn, collector receive, độ trễ sink, refresh dashboard [NGUỒN-DỰ ÁN:D-12]. Độ trễ ingestion = `t_sink - t_receive`; query + refresh = `t_visible - t_sink`. Tuổi quote (thời điểm nguồn) **không** thuộc SLO này: market yên tĩnh có thể có quote cũ trong khi kết nối khỏe [NGUỒN:FEATURES].
3. Đo tự động bằng dấu thời gian, đối chiếu `t_sink` với ingestion time ở Eventhouse [NGUỒN-DỰ ÁN:D-11].
4. Thêm một lần kiểm tay (quay màn hình) để xác nhận số tự động khớp điều người xem thấy [NGUỒN-DỰ ÁN:D-11] (V-16).
5. Mọi mẫu ghi nhãn nguồn: live, replay hay fixture; chỉ mẫu live được tính cho SLO (cách xử lý replay thuộc O-01).

**Chưa chốt, không đặt giá trị**
- **O-01, cách tính SLO lúc lỗi/recovery** (mất kết nối WebSocket, sink chậm rồi chạy bù, phát lại dữ liệu đã thu). Người dùng xác nhận để "chưa chốt". Hai phương án đã trình bày để tham khảo: (a) tính mọi bản ghi live, tách gap và replay ra báo cáo riêng; (b) tách hai tỷ lệ bình thường/recovery. Cần người dùng quyết trước Phase 6 (V-17). Cho tới lúc đó, không phát biểu "đạt SLO" cho phiên có gap, sink chậm hoặc replay; báo cáo phải đánh dấu phiên nào có các sự kiện đó.
- **O-02, đơn vị "mẫu"** (mỗi bản ghi, hay mỗi outcome mỗi khoảng thời gian), số mẫu tối thiểu để tuyên bố đạt/không đạt, phiên nào đủ điều kiện: chưa thảo luận; **câu hỏi mở, không đặt giá trị**. Dữ kiện số học liên quan tới ý nghĩa của 95% `[SUY LUẬN]`: nếu n < 20 thì "95% đạt" suy biến thành "100% đạt" (n = 19 cần 19/19; n = 20 mới cho phép 1 mẫu chậm). Ngoài ra, nếu mẫu là mỗi bản ghi thì kết quả bị chi phối bởi outcome sôi động và bản ghi dồn dập; nếu mẫu là mỗi outcome mỗi khoảng thời gian thì market yên tĩnh có trọng số ngang market sôi động. Hai lựa chọn này làm đổi ý nghĩa của 95%, nên theo O-02 phải đưa lại người dùng.

**Quyết định DEC-1.6: SLO live là >= 95% mẫu <= 30 giây, đo tới lúc hiển thị trên dashboard.**
- Vì sao: người dùng chọn tỷ lệ có chủ ý và điểm đo cuối ở dashboard thay vì ở Eventstream [NGUỒN-DỰ ÁN:D-10][NGUỒN-DỰ ÁN:D-11]. "Real-time" không có con số không phải yêu cầu [NGUỒN:S-LDP-BP]; con số 15 đến 30 giây là đầu vào chính cho phần kiến trúc về chế độ xử lý, và không có bằng chứng nào trong dự án cho thấy nó khả thi [CHƯA KIỂM CHỨNG].
- Quyết định này sai nếu: đo ở Phase 6 cho thấy độ trễ vượt 30 giây ở hơn 5% mẫu hợp lệ dưới điều kiện bình thường (khi đó sửa thiết kế hoặc đưa lại người dùng quyết định, không đổi chuẩn âm thầm); hoặc cách chọn "mẫu" (O-02) làm 95% phản ánh sai trải nghiệm xem.
- Kiểm chứng bằng: V-13 đến V-17.

### 1.7 Chi phí khi dữ liệu sai hoặc trễ

Không có số đo thiệt hại; mọi mục dưới đây là `[SUY LUẬN]`, chưa có người dùng thật.

| Tình huống | Hệ quả giả thuyết | Mức |
|---|---|---|
| KPI sai (công thức hoặc điền dữ liệu qua gap) | Người xem portfolio mất niềm tin vào số liệu, trái Core Value | Cao với mục tiêu portfolio; không có thiệt hại tài chính trực tiếp vì không có khuyến nghị đầu tư |
| Người xem đọc midpoint như xác suất hay khuyến nghị | Hiểu sai ý nghĩa dữ liệu | Giảm bằng ghi chú trong dashboard/tài liệu; hiệu quả chưa đo |
| Metadata batch trễ hoặc lỗi | Dùng danh sách cũ (R-01, R-02), có thể thiếu market mới | Trung bình, bị chặn một phần bởi kiểm tra lại market |
| Độ tươi live vượt 30 giây | Dashboard không đạt mục tiêu; người xem thấy số cũ hơn mong đợi | Thấp tới trung bình; hiển thị mốc refresh để người xem tự đánh giá |

Mức đầu tư vào giám sát tỷ lệ với mức quan trọng [NGUỒN:S-OBS]: đây là dự án portfolio một người, nên giám sát giữ ở mức kiểm chứng được (V-ID) chứ không dựng hệ thống cảnh báo ngoài phạm vi.

### 1.8 Giá trị và giới hạn portfolio

**Giá trị.** Cho thấy một pipeline stream thật từ Polymarket chạy từ nguồn tới dashboard trên Fabric, đủ rõ để người xem kiểm chứng từng bước (ingest, lưu, biến đổi, phục vụ) và tin vào số liệu [NGUỒN:PROJECT]. Giá trị này chỉ có khi các bước thực sự chạy và bằng chứng được ghi ở DATA-VERIFY; hiện **chưa có bước nào chạy**.

**Giới hạn (phải được nêu trong mọi tài liệu portfolio, V-18)**
1. **Không có đo lường người dùng thật.** Consumer là giả thuyết; không phỏng vấn, không khảo sát, không dữ liệu sử dụng [NGUỒN:PROJECT].
2. **Không có bằng chứng latency.** 15 đến 30 giây, 95% và hạn 08:15 đều là mục tiêu, chưa có phép đo; không được diễn đạt như kết quả cho tới khi có V-10 và V-15 [NGUỒN-DỰ ÁN:D-02][NGUỒN:PROJECT].
3. **Chưa có hành động nào trên cloud.** Kiểm tra tài nguyên chỉ đọc không đồng nghĩa được tạo, triển khai hay chạy job; các thao tác đó cần duyệt riêng [NGUỒN:PROJECT]. Phase 1 chỉ viết tài liệu (V-02).
4. **Công thức KPI chưa kiểm chứng**, và ngưỡng shock là định nghĩa portfolio, không phải phát hiện bất thường thống kê.
5. **Dữ liệu là quan sát**, không phải toàn bộ giao dịch thị trường, không giải thích nguyên nhân, không phải khuyến nghị đầu tư [NGUỒN:REQUIREMENTS].
6. **Mẫu nhỏ và gián đoạn**: stream chạy thủ công theo phiên hữu hạn, batch chỉ có vài chục lần chạy trong trial; hạn trial và capacity do người dùng báo, chưa xác minh [NGUỒN:PROJECT].
7. **Chưa có hạn dùng cho metadata fallback** (D-04), kèm rủi ro ở 1.5.

### 1.9 Phạm vi và non-goals

**Trong phạm vi v1** [NGUỒN:PROJECT][NGUỒN:REQUIREMENTS]: market AI về model trên Polymarket; ba câu hỏi Movers, Activity/Spread, Shock; batch metadata hằng ngày vào Lakehouse; phiên stream thủ công hữu hạn; dashboard live và lịch sử theo phiên/ngày trên Eventhouse/KQL.

**Ngoài phạm vi (non-goals)**: đặt lệnh, quản lý ví, khuyến nghị đầu tư; stream 24/7; backfill trước khi bắt đầu thu; theo dõi mọi chủ đề hoặc mọi market gắn tag AI; suy luận nguyên nhân từ tin tức và ML anomaly detection; cam kết đầy đủ mọi giao dịch hoặc exactly-once từ nguồn; network, HA/DR, đa workspace, phân quyền enterprise. Không có mục tiêu số cho độ đầy đủ giao dịch (không phải SLO của dự án).

### 1.10 Ràng buộc

| Ràng buộc | Lý do (người đưa ra) |
|---|---|
| Microsoft Fabric bắt buộc | Mục tiêu portfolio [NGUỒN:PROJECT]; ghi như ràng buộc, không phải yêu cầu nghiệp vụ |
| Fabric trial, hơn 30 ngày (chưa xác minh hạn và capacity), không có ngân sách thêm | [NGUỒN:PROJECT]; `[CHƯA KIỂM CHỨNG]` |
| Độ phức tạp ở mức fresher | [NGUỒN:PROJECT] |
| Thao tác cloud cần duyệt riêng | [NGUỒN:PROJECT] |
| Dữ liệu là market công khai; chưa kiểm payload có định danh ví hoặc thông tin cá nhân | O-04, `[CHƯA KIỂM CHỨNG]`, Phase 2 xác nhận trước khi viết contract |

### 1.11 Hệ quả cần chuyển giao

Source Analyst ghi dữ kiện và hệ quả; không chọn cách ingest hay chế độ xử lý.

| Dữ kiện (mục 1) | Hệ quả | Chuyển cho |
|---|---|---|
| Điểm cuối đo là lúc bản ghi hiển thị trên dashboard, đo tự động bằng dấu thời gian (D-11) | Phải có cách quan sát được "lần refresh đầu tiên chứa bản ghi" bằng máy; cơ chế chưa chọn. Cadence refresh của Real-Time Dashboard cộng ingestion cộng query phải nằm trong 30 giây; chưa tra lại cận cadence trong phiên này `[CHƯA KIỂM CHỨNG]` | Platform Architect (4.0, 4.1), Data Engineer (5.6) |
| Bốn mốc thời gian phải tách và lưu riêng (D-12) | Envelope bản ghi cần thời điểm nguồn, collector receive; Eventhouse cung cấp ingestion time; refresh có mốc riêng | Platform Architect (4.1, 4.3) |
| `t_receive` do collector đóng dấu bằng đồng hồ máy chạy collector, `t_visible` và `t_sink` dùng đồng hồ Fabric | Lệch đồng hồ làm sai phép đo; cần kiểm tra độ lệch trước khi tin số (V-14) | Data Engineer (5.6, 5.4) |
| Batch đo theo từng lần chạy, hạn tuyệt đối 08:15 (D-02, D-03) | Cần bản ghi lần chạy có `scheduled_start`, `actual_start`, `published_at`, kết quả; công bố phiên bản phải là một thao tác cho phép phân biệt đầy đủ với dở dang | Platform Architect (4.1, 4.4), Data Engineer (5.6) |
| Fallback không có tuổi tối đa (D-04) | Tuổi của snapshot đang dùng phải hiển thị (VIEW-02, REQ META-04); kiểm tra lại market phải chặn đăng ký, không chỉ cảnh báo; hành vi khi market không hợp lệ cần chốt (O-03) | Platform Architect (4.1, 4.4), Data Engineer (5.2) |
| Activity cần trade observation và size; Shock/Movers cần timestamp đáng tin | Phase 2 phải đo: trường trade, size, thứ tự và đơn vị timestamp, khóa duy nhất, dữ liệu đến muộn, định danh ví/cá nhân (O-04, O-05) | Source Analyst (Phase 2, mục 2) |
| Khối lượng phân trang metadata quyết định batch có đủ 60 phút không | Phải đo số trang/thời gian trên mẫu giới hạn trước khi cam kết hạn | Source Analyst (Phase 2), Data Engineer (5.6) |
| Định nghĩa "mẫu" và quy tắc recovery chưa chốt (O-01, O-02) | 5.6 không thể viết phép đo cuối cho tới khi người dùng quyết; DATA-SPEC 5.6 phải để trống mục này thay vì giả định | Data Engineer (5.6), điều phối (hỏi người dùng) |
| Baseline, dung sai quote cũ, gộp shock liên tiếp, dấu thời gian cho cửa sổ (Q-01 đến Q-04) | Cần quyết định ở thiết kế Phase 2/4; Phase 1 không đặt giá trị | Platform Architect (4.3), Data Engineer (5.1, 5.2) |

### 1.12 Tiêu chí thành công đo được (mức Phase 1)

| Tiêu chí | Đo bằng |
|---|---|
| Owner, maintainer, escalate được ghi trong repo và người dùng xác nhận (ROADMAP SC1) | V-01 |
| Ba câu hỏi gắn với KPI có công thức, đơn vị, consumer giả thuyết đánh dấu rõ (SC2) | Mục 1.1 đến 1.4 có mặt; reviewer đọc ở mục 6 |
| SLO có con số và cách đo; mục chưa chốt ghi rõ là chưa chốt (SC3) | Mục 1.5, 1.6 và mục 8 (O-01, O-02, D-04) |
| Giá trị và giới hạn portfolio được ghi; chưa có hành động cloud (SC4) | Mục 1.8; V-02, V-18 |

Các tiêu chí vận hành của KPI/SLO (công thức đúng, hạn đạt) kiểm ở các phase sau theo mục 7.

## 2. Nguồn

Không thuộc phạm vi Phase 1: source inventory là DSGN-01 của Phase 2 (ROADMAP Phase 2).

## 3. Contract

Không thuộc phạm vi Phase 1: data contract là DSGN-02 của Phase 2.

## 4. Kiến trúc

Không thuộc phạm vi Phase 1: ADR hệ thống và phân lớp là DSGN-03 của Phase 2; ingestion, lưu trữ, orchestration, governance cụ thể xây ở Phase 3–6.

## 5. Triển khai

Không thuộc phạm vi Phase 1: chưa có pipeline. Các mục tiêu SLO ở mục 1 là đầu vào cho 5.6 (observability và SLO) khi Phase 2 và các phase Build thiết kế.

## 6. Review độc lập

**Người review:** Independent Reviewer (dataeng), agent riêng, `independence: full`. Ngày 2026-10-21. Chỉ đọc artifact từ đĩa: `01-DATA-SPEC.md` (mục 1, 7, 8), `01-CONTEXT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `PROJECT.md`, `de-coordinator/SKILL.md`, danh mục nguồn của skill review. Không chạy lệnh nào trên cloud, không chạy V-ID nào; mọi V-ID vẫn "Chưa chạy" như mục 7 khai báo. Không có `[KIỂM CHỨNG]` trong review này. Lời người dùng trả lời trong phiên điều phối (D-04 xác nhận lại, 8.6 mục 2 và 3) **không có trên đĩa ngoài chính file này**, nên reviewer không xác minh được và xử lý như tuyên bố của tác giả.

**Phạm vi:** mục 1, 7, 8. Mục 2 đến 5 cố ý ngoài phạm vi Phase 1, không review. Số dòng dưới đây theo bản file trước khi chèn mục 6.

**Thang mức:** BLOCKER (có thể làm cổng hoặc chấp nhận rủi ro sai, hoặc phase không đóng được so với văn bản chính thức), WARNING (tương đương MAJOR: cần sửa hoặc thêm kiểm chứng trước khi dựa vào), INFO (tương đương MINOR).

### 6.1 Phát hiện

| ID | Mức | Vị trí / quyết định | Bằng chứng | Cần sửa và điều kiện đóng |
|---|---|---|---|---|
| RV-01 | **BLOCKER** | 8.5 R-02 (dòng 358), R-01 (dòng 357), 8.3 (dòng 331), 1.5 (dòng 139, 143), V-19 (dòng 297); DEC-1.5, D-04. Trạng thái: Chặn | (1) Chính ô R-02 ghi "người dùng không nêu riêng R-02". Nội dung R-02 (không phát hiện market AI mới chưa có trong snapshot) là `[SUY LUẬN]` của Source Analyst (dòng 143), nên người dùng chưa chấp nhận rủi ro này một cách có thông tin; câu trả lời về "tuổi tối đa" không tự động bao R-02. (2) Lý do người dùng đưa ra ("luồng metadata lỗi sẽ có cảnh báo") dựa vào cơ chế chưa tồn tại: V-19 xếp ở Phase 5 nhưng "kênh thiết kế ở Phase 7 (REL-04)", mà REL-04 (REQUIREMENTS dòng 59) chỉ nói trạng thái phiên, số nhận/ghi/lỗi, đối soát; META-04 (dòng 49) chỉ nói cảnh báo thời điểm cập nhật khi dùng fallback. Không yêu cầu nào sở hữu "cảnh báo khi batch lỗi hoặc không xong trước 08:00". (3) Người nhận cảnh báo là chính owner duy nhất, R-08 xác nhận không có người trực, không có mục tiêu phản hồi: cảnh báo là cơ chế phản ứng, không đặt giới hạn tuổi; bản cũ vẫn có thể là "hàng tuần". (4) Lần xác nhận lại ngày 2026-10-21 chỉ nằm trong DATA-SPEC; `01-CONTEXT.md` D-04 (dòng 26) không chứa nó, `git status` chỉ có DATA-SPEC mới (untracked). Nhãn `[NGUỒN-DỰ ÁN:D-04, xác nhận 2026-10-21]` trỏ tới nơi không có nội dung đó. | Điều phối: (a) trình bày R-02 bằng lời thường cho người dùng ("khi dùng snapshot cũ, market AI mới sẽ không vào watchlist và không có tín hiệu nào báo") rồi ghi chấp nhận riêng cho R-02, kèm lời nguyên văn và ngày vào `01-CONTEXT.md`; trong lúc chờ, cột "Người chấp nhận" của R-02 ghi "Chưa có". (b) Thêm một yêu cầu/ID có chủ (META-04 hoặc REL-04) cho "cảnh báo khi batch lỗi/trễ quá hạn" kèm phase và kênh, rồi sửa V-19 cho khớp phase. Nếu chưa có, R-01/R-02 chỉ được ghi là "chấp nhận với biện pháp giảm thiểu chưa thiết kế". (c) Cân nhắc, quyết định thuộc người dùng: vì phiên stream chạy thủ công nên người chạy luôn có mặt lúc bắt đầu phiên; ghi rõ tuổi snapshot tại thời điểm bắt đầu phiên là chốt chặn thật cho R-01 (VIEW-02/META-04). Đóng khi: R-02 có xác nhận riêng được ghi trong CONTEXT, và V-19 có yêu cầu sở hữu. |
| RV-02 | **BLOCKER** | M-01 (8.3 dòng 332, R-12); ROADMAP Phase 1 SC3 (dòng 34) và Phase 6 SC2 (dòng 99); REQUIREMENTS INCP-02 (dòng 14), dòng 94; PROJECT dòng 86; CONTEXT D-04 (dòng 26) | Văn bản chính thức vẫn đòi "tuổi tối đa metadata fallback" nằm trong nhóm SLO phải có con số hoặc "chưa chốt". D-04 là "đã chốt: không đặt", không phải con số cũng không phải "chưa chốt", nên theo chữ SC3 mục này chưa được đáp ứng. REQUIREMENTS dòng 94 còn ghi "chưa có tuổi tối đa được duyệt" và PROJECT dòng 86 "Chưa đặt". CONTEXT D-04 nói INCP-02 đã ghi "không đặt tuổi tối đa, theo lựa chọn của người dùng ngày 2026-10-21": reviewer grep REQUIREMENTS.md, cụm "tuổi tối đa" chỉ có ở dòng 14 và 94, **không có** câu đó. Tương tự, VIEW-01/Phase 6 SC2 cấm "đổi thành p95" trong khi D-10 chọn 95%. Verify Phase 1 sẽ so với ROADMAP nên sẽ thấy hai nguồn mâu thuẫn. M-01 được ghi nhận nhưng chưa xử lý, trong khi G1 đã ghi pass với "ghi chú" là biện pháp. Ghi chú thêm: với cách tính nearest-rank, "số mẫu <= 30 giây >= 95%" tương đương "p95 <= 30 giây" (đếm nguyên, ceil(0,95 n)); nên xung đột với câu "không đổi thành p95" là về chữ và về việc quyết định có tường minh hay không, không phải về toán. `[SUY LUẬN]` | Cần người dùng cho phép sửa văn bản (điều phối không tự sửa). Đóng khi INCP-02, ROADMAP SC3, REQUIREMENTS dòng 94, PROJECT dòng 77/86 ghi đúng "fallback: không đặt tuổi tối đa (D-04, 2026-10-21), rủi ro R-01/R-02", và VIEW-01/Phase 6 SC2 ghi "đạt khi >= 95% mẫu <= 30 giây (D-10), báo cáo p50/p95/max, không bỏ mẫu chậm". Nếu người dùng không muốn sửa, SC3 phải được ghi nhận là chưa đáp ứng với mục này. |
| RV-03 | WARNING | Frontmatter G1 (dòng 11), 8.3 (dòng 331); D-04 | Về thủ tục, G1 ("không có owner hoặc SLA không có con số, dừng, hỏi người dùng", de-coordinator dòng 50) đã được đáp ứng ở chỗ người dùng đã được hỏi và trả lời. Nhưng "không đặt tuổi tối đa" không phải con số, và chính 8.3 gọi nó là "chấp nhận có điều kiện" trong khi frontmatter ghi `pass` không điều kiện. Lý do người dùng đưa ra chưa đủ để thay một giới hạn (xem RV-01 điểm 2 và 3). Reviewer không đổi gates. | Đề xuất ghi G1 là pass có điều kiện (kèm tham chiếu RV-01, RV-02) cho tới khi hai BLOCKER đóng, thay vì `pass` trơn. Đây là quyết định của điều phối. Đóng khi frontmatter/8.3 nêu điều kiện hoặc RV-01 và RV-02 đã đóng. |
| RV-04 | WARNING | Dòng 21 (quy ước nhãn), 1.6 dòng 160, 8.6 mục 2 và 3; D-12 | Dòng 21 viết D-05/D-06/D-12 "cũng do người dùng chọn cùng ngày", còn CONTEXT gắn chúng `[ĐÃ CHỐT TRƯỚC]` (PROJECT/REQUIREMENTS). D-12 trong CONTEXT dòng 38 tự trích FEATURES.md, vốn là đề xuất nghiên cứu; PROJECT chỉ nói "đo cả ingestion, query và refresh". Phần "bốn mốc phải tách" gắn `[NGUỒN-DỰ ÁN:D-12]` có thể là đề xuất của FEATURES, chưa phải lựa chọn của người dùng (REQUIREMENTS VIEW-02 chỉ nêu nguồn, lần nhận, refresh, khoảng mất). Câu trả lời "công thức KPI là định nghĩa nháp hợp lệ" (8.6 mục 3) cũng chỉ có trong file này. Nhãn `[NGUỒN-DỰ ÁN:...]` là nhãn mới, không có trong mô hình bằng chứng chuẩn, chấp nhận được vì đã định nghĩa ở dòng 21. | Ghi các câu trả lời ngày 2026-10-21 chưa có trong CONTEXT vào CONTEXT (nguyên văn). Sửa dòng 21: chỉ khẳng định điều CONTEXT nói. Với bốn mốc: hoặc hỏi người dùng đó là lựa chọn của họ, hoặc đổi nhãn thành "đề xuất FEATURES, `[SUY LUẬN]`". Đóng khi nhãn khớp nguồn có trên đĩa. |
| RV-05 | WARNING | 1.6 "Cách đo" bước 1 (dòng 168) và bước 5 (dòng 172); DEC-1.6 "sai nếu" (dòng 180); V-15 | O-02 (đơn vị mẫu) phải để trống, nhưng bước 1 viết "mỗi mẫu i có `t_receive_i`..." tức là mẫu = một bản ghi; V-15 "bảng mẫu" ngầm cùng nghĩa. Falsifier của DEC-1.6 dùng "mẫu hợp lệ" và "điều kiện bình thường", cả hai chưa định nghĩa vì phụ thuộc O-01/O-02: falsifier này hiện chưa quan sát được. Hệ quả số học ở 1.6 (n < 20 thì 95% thành 100%: 18/19 = 94,7%, 19/20 = 95%) reviewer kiểm lại, đúng. Còn thiếu trong O-02: 95% tính gộp mọi phiên hay theo từng phiên; bản ghi dồn dập tương quan nên không độc lập; `t_visible` chỉ tồn tại khi dashboard đang mở và refresh, nên tập mẫu phụ thuộc có người xem hay không. `[SUY LUẬN]` | Viết lại bước 1 thành "mỗi quan sát (đơn vị mẫu chưa chốt, O-02)". DEC-1.6 "sai nếu" ghi rõ "chưa đánh giá được cho tới khi O-01 và O-02 chốt". Thêm vào O-02 ba câu trên. Báo cáo luôn kèm k/n (số mẫu chậm trên tổng), không chỉ "đạt/không đạt". Đóng khi mục 1.6 không còn ngầm định đơn vị mẫu. |
| RV-06 | WARNING | Q-06 (dòng 326), 1.5 dòng 126 và 136, DEC-1.5 "sai nếu" (a) dòng 148, R-07 dòng 363, V-10 | D-02 viết cả "trước 08:00" lẫn "(60 phút)". Bảng 1.5 trình bày "08:00 tức 60 phút sau giờ bắt đầu" như nguyên văn D-02, nhưng cách đo (bước 2, 3) và V-10 chọn giờ tuyệt đối, còn DEC-1.5(a) lại dùng "cần hơn 60 phút" (tương đối). Hai cách đọc khác nhau khi scheduler khởi động trễ. R-07 nói "một lần trễ là SLO không đạt", còn 1.5 nói lần trễ là "sự cố cần nguyên nhân hơn là một điểm trừ": chưa định nghĩa SLO batch "đạt" nghĩa là gì cho cả trial. | Hỏi người dùng Q-06. Trong lúc chờ, V-10 ghi cả `published_at - scheduled_start` và `published_at - actual_start` để cả hai cách đọc đều đánh giá được, và DEC-1.5(a) dùng cùng một cách đọc với cách đo. Thêm một câu về nghĩa "SLO batch đạt" cho cả trial. Đóng khi Q-06 được trả lời và ba chỗ khớp nhau. |
| RV-07 | WARNING | V-14 (dòng 293), A-08 (dòng 347), 1.11 dòng 233, A-09 | V-14 chỉ kiểm lệch giữa collector và "Fabric"; có ba đồng hồ liên quan: máy collector, ingestion time của Eventhouse, và nơi ghi nhận `t_visible` (trình duyệt hoặc dịch vụ dashboard). Không có ngưỡng lệch chấp nhận được ("không đáng kể so với 30 giây" không có số), nên V-14 chỉ "ghi độ lệch" và không thể fail. A-09 thừa nhận chưa có cách quan sát bằng máy "lần refresh đầu tiên chứa bản ghi"; nếu phải dùng truy vấn thay thế thì đo "có thể truy vấn", không phải "đã hiển thị", và phải gắn nhãn như vậy. Điểm đầu là collector receive nên SLO không đo độ trễ từ Polymarket tới collector; điều này chưa nằm trong giới hạn 1.8. | V-14 phủ cả ba đồng hồ và có ngưỡng đặt trước khi đo (người dùng/owner quyết, reviewer không đề xuất số); nếu lệch vượt ngưỡng thì V-15 là NOT-RUN hoặc cộng độ bất định vào max/p95 báo cáo. Nếu dùng phép đo thay thế cho "hiển thị", nêu rõ và dựa V-16 làm đối chứng. Thêm vào 1.8 và quét V-18: "độ trễ tính từ collector receive, không gồm độ trễ nguồn tới collector". Đóng khi V-14 có tiêu chí pass/fail. |
| RV-08 | WARNING | V-17 (dòng 296), R-03 | R-03 là rủi ro "chọn quy tắc sau khi thấy số", nhưng V-17 chỉ kiểm có quyết định có tên và ngày. Không có bằng chứng thứ tự: quyết định phải có trước dữ liệu đo đầu tiên. | V-17 yêu cầu quyết định O-01/O-02 được commit trước khi có dữ liệu đo live đầu tiên (so thứ tự commit hoặc dấu thời gian). Đóng khi cột "Bằng chứng cần có" nêu điều kiện thứ tự. |
| RV-09 | WARNING | DEC-1.5 "sai nếu" (b) (dòng 148), V-12 (dòng 291); DEC-1.1 đến 1.6 nói chung | (1) Falsifier (b) "phiên chạy với watchlist thiếu market mà không có cảnh báo" được gắn V-11, V-12, nhưng V-12 tự ghi không phát hiện market mới chưa có trong snapshot: không V-ID nào quan sát được lỗi chính của R-02. (2) Cả 5 DEC có "Vì sao" gắn nhãn và "sai nếu" (đã kiểm), nhưng không DEC nào nêu phương án đã xét và trade-off chấp nhận như mẫu DEC (ví dụ DEC-1.5: không giới hạn tuổi vs giới hạn theo giờ vs chặn phiên khi snapshot quá cũ; DEC-1.6: mọi mẫu <= 30 giây vs 95%). (3) DEC-1.5 "Vì sao" coi "mẫu vài chục lần chạy" là sự thật, trong khi số lần chạy phụ thuộc hạn trial (A-07, chưa xác minh) và ngày Phase 5 bắt đầu. (4) DEC-1.1 falsifier ghi "hiện không có cách kiểm": trung thực nhưng không quan sát được (INFO). | Thêm dòng "Phương án đã xét" và "Trade-off chấp nhận" cho từng DEC (không cần số). Với (1): ghi vào R-02 như rủi ro còn lại không đo được, hoặc thêm đối soát thủ công so watchlist với danh sách market AI hiện tại ở mỗi phiên (V-ID mới). Với (3) gắn `[CHƯA KIỂM CHỨNG]`. Đóng khi falsifier (b) hoặc được kiểm hoặc được ghi là không kiểm được. |
| RV-10 | WARNING | V-19 (dòng 297), REQUIREMENTS Traceability (dòng 124, 130) | V-19 "Phase 5 (cùng V-11); thiết kế kênh ở Phase 7" không khớp: V-19 không chạy được ở Phase 5 nếu kênh chưa tồn tại, và không có requirement ID nào mang cảnh báo batch. Khi đó V-19 sẽ là NOT-RUN trong khi R-01/R-02 coi nó là biện pháp giảm thiểu. Cũng chưa nói kênh cảnh báo là gì khi quy ước 1.3 cấm ghi email vào repo. | Gắn V-19 vào requirement có chủ (xem RV-01 b), đặt phase đo nhất quán, nêu kênh dự kiến (chỉ loại kênh, không địa chỉ). Đóng cùng RV-01. |
| RV-11 | INFO | Phủ V-ID: V-05 (dòng 284), 1.4.3, 1.4.1 dòng 76 | V-05 chỉ kiểm hai trạng thái và missing size; hai phép đo kèm `trade_obs_per_covered_minute` và `observed_shares/observed_notional` không có V-ID. Lời hứa lưu và so sánh theo UTC (dòng 76) không có V-ID (A-11 chỉ gắn lịch batch). Không V-ID nào tuyên bố đã chạy: đã kiểm, cột trạng thái của cả 19 V-ID là "Chưa chạy", không dòng nào có `[KIỂM CHỨNG]`. | Mở rộng V-05 hoặc thêm V-ID cho hai phép đo (có thể để Phase 4 vì `covered_minute` chưa định nghĩa, Q-04). Thêm V-ID hoặc dòng vào V-13 cho UTC. |
| RV-12 | INFO | Mục 7 đầu (dòng 274), thứ tự bảng | Câu "đánh số tiếp từ V-19" mâu thuẫn với V-19 đã dùng (số kế tiếp là V-20); V-19 đứng trước V-18 trong bảng. Ký hiệu `V-13` (dự án) dễ nhầm với `V13` chuẩn của kịch bản kiểm thử; bảng ánh xạ ở 5.4 chưa có (mục 5 ngoài phạm vi Phase 1). | Sửa câu số thứ tự và sắp xếp lại bảng. Ghi nhận bảng ánh xạ là việc nợ cho Phase 2. |
| RV-13 | INFO | 1.3 dòng 58, A-12, V-01 (dòng 280) | SC1 cần người dùng xác nhận bản ghi owner: còn mở (8.6 mục 1), và DATA-SPEC chưa commit nên "lưu trong repo" chưa thực sự đúng. V-01 "tìm kiếm không thấy địa chỉ email" nên giới hạn ở nội dung file được theo dõi, vì metadata tác giả của git chứa email. Reviewer grep `.planning` tìm mẫu địa chỉ email: không thấy. | Commit trước verify; ghi phạm vi tìm kiếm của V-01. |
| RV-14 | INFO | Nhãn nguồn: DEC-1.1, 1.6, 1.4.1, 1.7 | ID S-DP, S-LDP-BP, S-MYTH, S-OBS đều có trong danh mục nguồn của skill review, mức đọc TV; mục "Nguồn tham khảo" của file khớp mức đọc và tác giả ghi rõ không mở lại. Không nguồn TV nào dùng làm căn cứ chặn. Tuy vậy, hai chỗ diễn giải có thể vượt nguồn mà reviewer không xác minh được (không mở trang): S-LDP-BP cho câu "real-time không có con số không phải yêu cầu" (nguồn nói về chọn chế độ xử lý), S-MYTH cho "mỗi KPI một định nghĩa" (nguồn là về mô hình hóa dữ liệu). Cả hai là nguyên tắc nhẹ, không ảnh hưởng quyết định. Các nhãn `[SUY LUẬN]` cho UTC, nearest-rank, `covered_minute`, hệ quả vận hành (dòng 57) dùng đúng nhãn, không giả làm `[NGUỒN]`. | Đổi hai chỗ trên thành `[SUY LUẬN]` hoặc bỏ nhãn nguồn nếu không xác minh được. |

### 6.2 Đối chiếu ROADMAP Phase 1 success criteria

| SC | Kết quả | Căn cứ |
|---|---|---|
| 1. Owner, maintainer, escalate trong repo và người dùng xác nhận | Một phần | Có ở 1.3 (D-01), không có email. Người dùng chưa xác nhận bản ghi (A-12, V-01), file chưa commit (RV-13). Không phải BLOCKER vì đây là việc của bước verify. |
| 2. Ba câu hỏi gắn KPI có công thức, đơn vị, consumer giả thuyết | Đạt, có giới hạn | 1.1 đến 1.4 đủ công thức, đơn vị pp, grain, nhãn `[CHƯA KIỂM CHỨNG]`. Công thức chưa tính lặp lại được bởi hai người độc lập vì baseline và dung sai quote cũ chưa chốt (Q-01 đến Q-04); REQUIREMENTS đã giao cho phase design nên không chặn Phase 1. |
| 3. SLO có con số, cách đo; mục chưa chốt ghi là chưa chốt | Một phần | Batch (07:00, 08:00, từng lần chạy) và live (95%, 30 giây, điểm đầu/cuối) có số và cách đo. O-01 ghi "chưa chốt", O-02 ghi chưa thảo luận: đúng yêu cầu. Tuổi tối đa fallback không phải số cũng không phải "chưa chốt": chưa khớp chữ SC3 (RV-02). Q-06 chưa rõ (RV-06). |
| 4. Giá trị và giới hạn portfolio; chưa có hành động cloud | Đạt | 1.8 nêu không đo người dùng thật, không bằng chứng latency, chưa hành động cloud. Nên thêm giới hạn của RV-07. |

### 6.3 Những điều đã kiểm và đạt

- Không con số SLO nào do tác giả tự điền: 5 pp/5 phút, 5/15/60 phút là D-06; 95%, 30 giây là D-10; 07:00/08:00 là D-02; các con số khác (19/20, 18/19) là suy ra số học đúng và được gắn `[SUY LUẬN]`. Quy đổi 07:00/08:00 Asia/Saigon sang 00:00/01:00 UTC đúng.
- O-01 giữ "chưa chốt" ở 1.6, 8.1 và V-17 (không có giá trị được điền). Hai quy tắc tạm ("không phát biểu đạt SLO cho phiên có gap/replay", "chỉ mẫu live được tính") nhất quán với cả hai phương án O-01 đã trình bày nên không làm thay quyết định. Riêng đơn vị mẫu thì xem RV-05.
- D-04 được ghi như giá trị tuyên bố "không có tuổi tối đa", rủi ro R-01 và R-02 nêu trung thực, kể cả điều bất lợi (chốt chặn duy nhất, không phát hiện market mới).
- Mọi V-ID ở mục 7 có cách kiểm chứng, bằng chứng cần có và phase đo; không V-ID nào tuyên bố đã chạy.

### 6.5 Xử lý của điều phối sau review (2026-10-21)

Reviewer không được đọc lại bản sửa; việc đóng BLOCKER dưới đây dựa trên quyết định người dùng ghi trong `01-CONTEXT.md` (D-13..D-15) và các file đã sửa. `independence: full` áp dụng cho bản review ở 6.1 đến 6.4, không áp dụng cho các bản sửa sau đó.

| ID | Trạng thái | Xử lý |
|---|---|---|
| RV-01 | Đóng | Người dùng chấp nhận R-02 riêng, nguyên văn trong CONTEXT D-14; thêm META-07 (REQUIREMENTS, ROADMAP Phase 5 SC5) sở hữu cảnh báo lỗi batch; V-19 gắn META-07, Phase 5. Điểm (c) của reviewer (tuổi snapshot lúc bắt đầu phiên làm chốt chặn) chưa hỏi người dùng, để lại cho Phase 2/5 như đề xuất mở. |
| RV-02 | Đóng | Người dùng cho phép sửa; đã sửa INCP-02, REQUIREMENTS "Acceptance details", ROADMAP Phase 1 SC3 và Phase 6 SC2, PROJECT (Key Decisions, Open Questions). M-01 xử lý. |
| RV-03 | Đóng | G1 pass không điều kiện sau khi RV-01, RV-02 đóng. |
| RV-04 | Đóng một phần | Nhãn dòng 21 đã sửa; D-12 ghi là đề xuất FEATURES; công thức KPI nháp và các trả lời 2026-10-21 đã vào CONTEXT (D-13..D-15). Câu hỏi D-12 có phải lựa chọn của người dùng vẫn chưa hỏi. |
| RV-05, RV-07, RV-08, RV-09, RV-11, RV-12, RV-13, RV-14 | Mở, chuyển vào plan | Sửa nội dung DATA-SPEC mục 1.6, 1.8, 6, 7, 8 theo cột "Cần sửa" của 6.1, là task của plan Phase 1; không bỏ. Ngưỡng lệch đồng hồ (RV-07) và O-02 bổ sung cần người dùng quyết, hỏi khi thực hiện task. |
| RV-06 | Đóng một phần | Q-06 đã trả lời (D-13): hạn 08:15 tuyệt đối; DEC-1.5(a) đã sửa. Còn nghĩa của "SLO batch đạt" cho cả trial: D-03 đã nói mọi lần chạy theo lịch phải xong trước hạn; R-07 và 1.5 cần thống nhất chữ, thuộc plan. |
| RV-10 | Đóng | Cùng RV-01. |

### 6.4 Kết luận

- **Số BLOCKER chưa xử lý: 2** (RV-01, RV-02). **WARNING: 8** (RV-03 đến RV-10). **INFO: 4** (RV-11 đến RV-14).
- **Verdict đề xuất: `fail` cho vòng này** (không phải vì chất lượng nội dung mục 1 kém, mà vì hai BLOCKER chỉ đóng được bằng câu trả lời hoặc ủy quyền của người dùng). Khi RV-01 và RV-02 đóng và các WARNING được xử lý hoặc ghi là rủi ro có tên, verdict có thể là `pass` hoặc `accepted-risk` (R-01, R-02) với người chấp nhận là Duong Kim Sang.
- Câu hỏi cần điều phối hỏi người dùng: (1) R-02, chấp nhận riêng sau khi nghe rủi ro "market mới không vào watchlist, không có tín hiệu", và nguyên văn câu trả lời D-04 để ghi vào CONTEXT; (2) cho phép sửa INCP-02, ROADMAP SC3 và Phase 6 SC2, REQUIREMENTS dòng 94, PROJECT dòng 77/86 theo D-04 và D-10 (hoặc coi SC3 chưa đáp ứng); (3) cảnh báo batch lỗi: yêu cầu thuộc phase nào, kênh nào, và có muốn ghi tuổi snapshot lúc bắt đầu phiên làm chốt chặn chính không; (4) Q-06 (08:00 tuyệt đối hay 60 phút kể từ lúc bắt đầu thật) và nghĩa của "SLO batch đạt" cho cả trial; (5) Q-07; (6) O-02 bổ sung: 95% gộp hay theo phiên, số mẫu tối thiểu; có thể hỏi sớm cùng O-01; (7) ngưỡng lệch đồng hồ chấp nhận được cho V-14; (8) bốn mốc thời gian (D-12) có phải lựa chọn của người dùng hay là đề xuất FEATURES.

### Nguồn tham khảo của mục 6

Các ID `S-DP`, `S-LDP-BP`, `S-MYTH`, `S-OBS` và `DE-COORD` được nhắc ở RV-03 và RV-14 đã có URL và mức đọc trong bảng "Nguồn tham khảo" cuối file này (mức TV; tác giả mục 1 không mở lại, reviewer cũng không mở lại, chỉ đối chiếu ID, tiêu đề, URL, mức đọc với danh mục nguồn của skill review). Tài liệu dự án đã đọc từ đĩa: `01-CONTEXT.md` (đầy đủ), `ROADMAP.md` (Phase 1 và grep), `REQUIREMENTS.md` và `PROJECT.md` (grep theo dòng liên quan).

## 7. Kế hoạch kiểm chứng (V-ID)

Phạm vi: chỉ các lời hứa của Phase 1 (mục 1): định nghĩa KPI, từng con số SLO, hành vi fallback, giới hạn công bố. Contract (Phase 2) và các lời hứa kỹ thuật khác thêm V-ID ở phase của chúng; đánh số tiếp từ V-20 (V-19 đã dùng cho cảnh báo lỗi batch). ID `V-xx` ở đây là ID cấp dự án, khác ID V01 đến V17 chuẩn của kịch bản kiểm thử pipeline; Data Engineer lập bảng ánh xạ ở 5.4.

**Không có V-ID nào đã chạy.** Cột trạng thái là "Chưa chạy" cho tới khi DATA-VERIFY ghi bằng chứng thật. Kiểm tra cần môi trường chạy mà chưa có thì là NOT-RUN, không được thay bằng đọc code [NGUỒN:DE-COORD]. Con số SLO là mục tiêu: một V-ID đạt chỉ chứng minh mục tiêu trên mẫu đã đo, không chứng minh cam kết dài hạn.

| V-ID | Lời hứa kiểm chứng | Cách kiểm chứng | Phase đo | Bằng chứng cần có | Trạng thái |
|---|---|---|---|---|---|
| V-01 | Owner, maintainer, escalate được ghi trong repo (INCP-01, D-01), không có email | Đọc bản ghi trong repo; người dùng xác nhận bằng văn bản ở phiên verify; tìm kiếm không thấy địa chỉ email/liên hệ riêng | Phase 1 (verify) | Đường dẫn bản ghi, xác nhận của Duong Kim Sang | Chưa chạy |
| V-02 | Phase 1 không có hành động cloud | Kiểm tra `git diff`/log của phase chỉ gồm tài liệu; không có lệnh ghi hoặc chạy job trên cloud trong nhật ký phiên | Phase 1 (verify) | Danh sách file thay đổi, nhật ký lệnh | Chưa chạy |
| V-03 | Movers đúng công thức 5/15/60 phút: giá trị pp, dấu, thứ hạng theo giá trị tuyệt đối | Fixture có đáp án tính tay độc lập; đối chiếu đầu ra pipeline; sau đó chạy lại trên dữ liệu đã thu và so với truy vấn độc lập | Phase 4 (KPI-01); lặp ở Phase 6 trên live | Fixture, đáp án kỳ vọng, kết quả so sánh | Chưa chạy |
| V-04 | Không điền giá giả: thiếu baseline hoặc qua gap hiện "chưa đủ dữ liệu", không backward-fill, không zero-fill, không xếp hạng như 0 | Fixture thiếu baseline và fixture có ngắt kết nối giữa baseline và t | Phase 4 (KPI-02) | Fixture, đầu ra hiển thị trạng thái | Chưa chạy |
| V-05 | Activity: hai trạng thái "có giao dịch quan sát"/"chỉ quote" không ngưỡng số; missing size là unknown/partial, không phải 0; trade observation tách khỏi quote update | Fixture có size thiếu một phần, thiếu hoàn toàn, và outcome không có trade | Phase 4 (KPI-03) | Fixture, đầu ra từng trường hợp | Chưa chạy |
| V-06 | Spread = `100 * (min ask - max bid)` bất kể thứ tự mảng; trạng thái missing/crossed/stale đúng | Fixture mảng đảo thứ tự, crossed, thiếu một phía, quote cũ | Phase 4 (KPI-04) | Fixture, đầu ra | Chưa chạy |
| V-07 | Shock: đúng ngưỡng (đúng 5,0 pp trong 5 phút kích hoạt; dưới ngưỡng không), cả tăng và giảm, đổi cấu hình ngưỡng/cửa sổ đổi kết quả đúng; thiếu lịch sử là "chưa đủ dữ liệu"; shock fixture phân biệt với live | Fixture biên và cặp fixture đổi cấu hình | Phase 4 (KPI-05) | Fixture, kết quả theo cấu hình | Chưa chạy |
| V-08 | Cùng định nghĩa KPI ở live và lịch sử; đối soát được cho cùng khoảng | So kết quả dashboard live với truy vấn KQL lịch sử cho cùng phiên và khoảng | Phase 6 (VIEW-03) | Hai bộ kết quả, chênh lệch nếu có | Chưa chạy |
| V-09 | Lịch batch cấu hình 07:15 Asia/Saigon (00:15 UTC) và có ít nhất một lần chạy **theo lịch** (không phải thủ công) | Bằng chứng cấu hình lịch và bản ghi lần chạy do scheduler khởi tạo; cần duyệt riêng cho thao tác cloud | Phase 5 (META-01) | Ảnh/xuất cấu hình, bản ghi lần chạy | Chưa chạy |
| V-10 | SLO batch: mỗi lần chạy theo lịch có `published_at` trước 08:15 Asia/Saigon; lần trễ/lỗi được ghi thành sự cố kèm nguyên nhân | Bảng từng lần chạy (`scheduled_start`, `actual_start`, `published_at`, kết quả); không gộp thành tỷ lệ; lặp trong suốt trial | Phase 5, tiếp tục các phase sau | Bảng từng lần chạy, bản ghi sự cố | Chưa chạy |
| V-11 | Fallback: batch mới lỗi cố ý thì phiên mới dùng bản thành công gần nhất, cảnh báo thời điểm cập nhật, chạy kiểm tra lại market trước đăng ký; batch dở dang không thay bản hoàn chỉnh | Gây lỗi có chủ đích trong môi trường được duyệt, quan sát phiên | Phase 5 (META-04, ROADMAP SC4) | Nhật ký lần chạy lỗi, phiên bản được dùng, cảnh báo | Chưa chạy |
| V-12 | Kiểm tra lại market bắt market đã đóng/không hợp lệ trong snapshot cũ và không đăng ký âm thầm; ghi rõ giới hạn: không phát hiện market mới chưa có trong snapshot | Fixture snapshot chứa market đã đóng; ghi kết quả vào tài liệu giới hạn | Phase 3 (META-05) | Fixture, kết quả kiểm tra lại, tài liệu giới hạn | Chưa chạy |
| V-13 | Bốn mốc thời gian tách và truy vấn được: nguồn, collector receive, sink (ingestion time), refresh dashboard | Truy vấn KQL lấy ba mốc đầu trên một phiên thật; mốc refresh kiểm ở Phase 6 | Phase 3 (ba mốc đầu), Phase 6 (refresh) | Truy vấn và kết quả rút gọn | Chưa chạy |
| V-14 | Độ lệch đồng hồ giữa collector và Fabric được kiểm trước khi tin số latency | Đối chiếu `t_receive` của collector với ingestion time cho cùng bản ghi hoặc nguồn thời gian chuẩn; ghi độ lệch quan sát | Phase 3 hoặc 6, trước V-15 | Độ lệch đo được, phương pháp | Chưa chạy |
| V-15 | SLO live: >= 95% mẫu <= 30 giây từ collector receive tới hiển thị; báo cáo p50, p95, max, n; liệt kê riêng mẫu > 30 giây; nhãn live/replay/fixture | Đo tự động theo D-11, đối chiếu ingestion time; điều kiện đạt tính bằng tỷ lệ đếm. **Chặn bởi O-01 và O-02** (V-17) | Phase 6 (VIEW-01) | Bảng mẫu, thống kê, danh sách mẫu chậm, ghi nhận capacity | Chưa chạy |
| V-16 | Số đo tự động khớp điều người xem thấy | Một lần quay màn hình dashboard, so mốc hiển thị với số đo tự động | Phase 6 | Bản quay màn hình, bảng so sánh | Chưa chạy |
| V-17 | Cổng quyết định: người dùng đã chốt O-01 (lúc lỗi/recovery) và O-02 (đơn vị mẫu, số mẫu tối thiểu, phiên đủ điều kiện) và quyết định được ghi vào DATA-SPEC trước khi chạy V-15 | Kiểm văn bản: có quyết định có tên, ngày; nếu chưa thì V-15 là NOT-RUN, không được chạy với giả định | Phase 6, trước V-15 | Mục DATA-SPEC/CONTEXT ghi quyết định | Chưa chạy |
| V-19 | Khi batch metadata lỗi hoặc không xong trước 08:15, có cảnh báo tới người vận hành (cơ chế giảm thiểu R-01, R-02 theo ý người dùng 2026-10-21) | Gây lỗi batch có chủ đích trong môi trường được duyệt, quan sát cảnh báo tới đích; ghi kênh và độ trễ cảnh báo | Phase 5 (META-07, cùng V-11); chỉ ghi loại kênh, không ghi địa chỉ hay email vào repo | Nhật ký lỗi cố ý, ảnh/xuất cảnh báo nhận được | Chưa chạy |
| V-18 | Giới hạn được công bố: không đo lường người dùng thật, mục tiêu không phải kết quả, không tuyên bố latency khi thiếu bằng chứng V-15, không phải khuyến nghị đầu tư | Reviewer đọc mục 1.8 (Phase 1) và README/tài liệu portfolio (Phase 8); quét tuyên bố latency không kèm V-15 | Phase 1 (mục 1.8), Phase 8 (DOC-01, DOC-03) | Vị trí đoạn giới hạn; kết quả quét | Chưa chạy |

Ghi chú cho D-04 (không có tuổi tối đa fallback): không có V-ID kiểm "tuổi tối đa" vì không có con số để kiểm. Hành vi quanh nó được kiểm bằng V-11 và V-12; phần rủi ro còn lại ghi ở mục 8 (R-01, R-02); cảnh báo lỗi batch do người dùng nêu làm biện pháp giảm thiểu được kiểm bằng V-19.

## 8. Giả định và rủi ro chấp nhận

**Chấp nhận rủi ro cần tên người có thẩm quyền và ngày.** Chỉ R-01 và R-02 có ghi nhận từ người dùng (2026-10-21, xem cột "Người chấp nhận"); các rủi ro còn lại ghi "chưa có". Tài liệu này không tự chấp nhận thay người dùng [NGUỒN:DE-COORD]. Người duy nhất có thể chấp nhận là owner (Duong Kim Sang, mục 1.3).

### 8.1 Mục chưa chốt từ người dùng (O-01 đến O-05)

| ID | Nội dung | Trạng thái | Ai quyết | Hạn quyết | Phase 1 khẳng định gì |
|---|---|---|---|---|---|
| O-01 | Cách tính SLO live lúc lỗi/recovery (mất kết nối WebSocket, sink chậm rồi chạy bù, replay) | **Chưa chốt**, người dùng xác nhận để vậy; phương án tham khảo (a) tính mọi bản ghi live, tách gap/replay riêng, (b) tách hai tỷ lệ bình thường/recovery | Duong Kim Sang | Trước Phase 6; cổng G1 có thể hỏi lại | Chỉ ghi là chưa chốt; không đặt giá trị; V-17 chặn V-15 |
| O-02 | Đơn vị "mẫu", số mẫu tối thiểu để tuyên bố đạt/không đạt, phiên đủ điều kiện | **Chưa thảo luận**; câu hỏi mở, không đặt giá trị. Ghi nhận: n < 20 làm 95% thành 100% (1.6) | Duong Kim Sang (đưa lại vì ảnh hưởng ý nghĩa của 95%) | Trước Phase 6 (và khi viết 5.6) | Chỉ ghi câu hỏi và hệ quả số học `[SUY LUẬN]` |
| O-03 | Market không còn hợp lệ lúc kiểm tra lại với fallback: loại và chạy tiếp hay dừng cả phiên | Chưa chốt; người dùng để Phase 2 quyết | Phase 2 (DSGN-03), người dùng nếu ảnh hưởng phạm vi | Phase 2 | Subscription không hợp lệ khi không có snapshot thành công hoặc không kiểm tra được market (đã chốt trong PROJECT) |
| O-04 | Dữ liệu nhạy cảm: payload có định danh ví hay thông tin cá nhân không | `[CHƯA KIỂM CHỨNG]`; chưa thảo luận, chưa kiểm payload | Phase 2 (source inventory) | Trước khi viết contract | Không có kết luận; chỉ ghi dữ liệu là market công khai theo dự kiến |
| O-05 | Probe về ngữ nghĩa thay đổi của nguồn, retention nguồn/backfill, dữ liệu đến muộn; hành vi dữ liệu xấu theo loại lỗi | Thuộc DSGN-01 (Phase 2) và REL-03 (Phase 4); backfill trước khi thu nằm ngoài phạm vi | Phase 2, Phase 4 | Phase 2/4 | Chuyển giao ở 1.11 |
| D-04 (không phải O) | Tuổi tối đa của metadata fallback | **Không đặt, có chủ ý** (người dùng chọn). Không phải mục bỏ trống. Xem 8.3 về cổng G1 | Duong Kim Sang | Không áp dụng | Ghi "không có tuổi tối đa", nêu rủi ro R-01, R-02 và chốt chặn duy nhất |

### 8.2 Câu hỏi thiết kế mới do Source Analyst nêu (không có giá trị đã đặt)

| ID | Câu hỏi | Vì sao cần | Chuyển cho |
|---|---|---|---|
| Q-01 | Cách chọn baseline `midpoint(t - W)` (quote gần nhất trước mốc, hay trong dung sai) và dung sai quote cũ | Hai người tính độc lập mới ra cùng số; REQUIREMENTS đã giao cho phase design [NGUỒN:REQUIREMENTS] | Phase 2/4 (Platform Architect, Data Engineer); đưa người dùng nếu ảnh hưởng phạm vi |
| Q-02 | Cửa sổ 5/15/60 phút tính theo dấu thời gian nguồn hay collector receive | Hai mốc cho kết quả khác khi nguồn trễ hoặc đến không đúng thứ tự | Phase 2 (source inventory), Phase 4 |
| Q-03 | "Đổi >= 5 pp trong 5 phút" là chênh lệch hai đầu mút hay biên độ max-min trong cửa sổ; cách gộp các lần vượt liên tiếp | FEATURES.md nói "within 5 minutes" nhưng chi tiết baseline và nhóm để phase design | Phase 4; người dùng nếu đổi ý nghĩa Shock |
| Q-04 | Định nghĩa `covered_minute` và các giá trị cho phép của "cửa sổ đang xem" ở Activity | Mẫu số của `trade_obs_per_covered_minute` | Phase 4 |
| Q-05 | Đơn vị tiền của notional `price * size` và đơn vị của size | Chưa xác minh từ payload; `[CHƯA KIỂM CHỨNG]` | Phase 2 |
| Q-06 | **Đã trả lời (D-13, 2026-10-21):** hạn hoàn tất là giờ tuyệt đối 08:15; lịch bắt đầu đổi thành 07:15 | Người dùng: "Chạy lúc 7:15 đi và 8:15 tuyệt đối", xác nhận "Đúng: 07:15 và hạn 08:15" | Đóng |
| Q-07 | **Đã trả lời (D-15, 2026-10-21):** chỉ <= 30 giây là điều kiện đạt; 15 giây không phải ngưỡng đánh giá | Người dùng chọn "Chỉ ≤30s là điều kiện đạt" | Đóng |

### 8.3 Cổng G1 và mâu thuẫn tài liệu cần điều phối xử lý

- **Kết quả G1 (điều phối, 2026-10-21): pass.** Owner có tên (1.3). SLO batch, SLO live, KPI có số hoặc công thức. D-04: người dùng xác nhận "không đặt tuổi tối đa", lý do là luồng metadata lỗi sẽ có cảnh báo (V-19); coi là giá trị tuyên bố, không phải ô trống. O-01 và O-02 ghi "chưa chốt" theo chỉ dẫn của người dùng, và V-17 chặn V-15 cho tới khi chốt. Đây là chấp nhận có điều kiện, Reviewer ở mục 6 vẫn phải kiểm.
- **[MÂU THUẪN] M-01, tài liệu chưa theo kịp quyết định. ĐÃ XỬ LÝ 2026-10-21: người dùng cho phép sửa, văn bản dưới đây là mô tả trạng thái trước khi sửa (xem 6.5).** `REQUIREMENTS.md` INCP-02 (dòng nêu "tuổi tối đa của metadata fallback" trong nhóm con số) và dòng "Metadata fallback chưa có tuổi tối đa được duyệt", cùng ROADMAP Phase 1 success criterion 3, vẫn ghi như chưa quyết; D-04 trong `01-CONTEXT.md` nói INCP-02 đã ghi "không đặt tuổi tối đa, theo lựa chọn của người dùng ngày 2026-10-21" nhưng văn bản đó **không có** trong REQUIREMENTS.md hiện tại. Tương tự, VIEW-01 và ROADMAP Phase 6 SC2 ghi "không đổi thành p95 hay bỏ mẫu chậm" trong khi D-10 là quyết định có chủ ý chọn 95%. Source Analyst không sửa các file đó (ngoài phạm vi); cần điều phối cập nhật hoặc ghi chú để reviewer không thấy xung đột.
- **Đánh giá về cổng G1 ("SLA không có con số") đối với D-04** `[SUY LUẬN]`: có khả năng cổng G1 coi "tuổi tối đa fallback: không đặt" là SLA không có số, vì INCP-02 và ROADMAP SC3 liệt kê mục này cùng nhóm các SLO phải có con số. Theo chỉ dẫn của người dùng (D-04), **không tự điền con số**. Đề nghị điều phối hỏi người dùng một câu để chốt cách ghi: (i) xác nhận "không có tuổi tối đa" là giá trị cuối và là lựa chọn có chủ ý đủ để qua G1, kèm ghi chấp nhận rủi ro có tên; hoặc (ii) người dùng tự đặt một con số. Nếu chọn (i), mục này được ghi như một giá trị tuyên bố, không phải ô trống.
- **Các mục khác có thể bị G1 đọc là chưa có số**: O-01 và O-02 (người dùng xác nhận để chưa chốt; ROADMAP SC3 cho phép ghi là chưa chốt); không có mục tiêu số cho độ đầy đủ live, không có mục tiêu thời gian phản hồi sự cố (dự án một người, không có người trực). Hai mục sau Source Analyst coi là không thuộc SLO người dùng đã chọn, không điền.

### 8.4 Giả định `[CHƯA KIỂM CHỨNG]`

| ID | Giả định | Ảnh hưởng nếu sai | Kiểm ở |
|---|---|---|---|
| A-01 | Ba quyết định nghiệp vụ và hai consumer ở 1.1, 1.2 là giả thuyết; không có phỏng vấn hay đo lường | Giá trị nghiệp vụ không được chứng minh | Không có V-ID; công bố ở V-18 |
| A-02 | Công thức KPI trong `FEATURES.md` đúng và tính được trên dữ liệu thật; chưa có fixture hay dữ liệu thu | KPI sai | V-03 đến V-07 (Phase 4) |
| A-03 | Ngữ nghĩa best_bid/best_ask/midpoint theo tài liệu Polymarket (P-PRICES); Source Analyst chỉ biết qua `FEATURES.md`, không đọc lại tài liệu gốc trong phiên này | Công thức midpoint/spread sai | Phase 2 (source inventory) |
| A-04 | Payload WebSocket có trường trade và size đủ để tính Activity; thứ tự và đơn vị timestamp dùng được cho cửa sổ | Activity và Shock không tính được hoặc sai | Phase 2 |
| A-05 | Batch có thể hoàn tất đầy đủ (phân trang, kiểm tra, công bố) trong 60 phút trên capacity trial; scheduler khởi động gần 00:15 UTC | Hạn 08:15 không khả thi | V-09, V-10 (Phase 5) |
| A-06 | Độ trễ collector receive tới hiển thị dashboard có thể <= 30 giây ở >= 95% mẫu, gồm cadence refresh của Real-Time Dashboard; không có phép đo nào, và chưa tra lại cận cadence trong phiên này | Mục tiêu không khả thi, phải đổi thiết kế hoặc quyết định | V-15 (Phase 6) |
| A-07 | Fabric trial còn hơn 30 ngày, capacity đủ cho batch và phiên stream (do người dùng báo, chưa xác minh) | Số lần chạy và mẫu ít hơn dự kiến; mất bằng chứng khi hết hạn | Phase 3 (preflight chỉ đọc), Phase 8 |
| A-08 | Đồng hồ collector và Fabric lệch không đáng kể so với 30 giây | Số latency sai | V-14 |
| A-09 | Có cách quan sát bằng máy "lần refresh dashboard đầu tiên chứa bản ghi" (D-11); chưa chọn cơ chế | Không thực hiện được phép đo tự động đúng D-11 | Phase 2/6 |
| A-10 | Dữ liệu là market công khai, không chứa định danh ví hay thông tin cá nhân (O-04) | Phát sinh nghĩa vụ governance | Phase 2 |
| A-11 | Asia/Saigon là UTC+7 cố định không đổi giờ; scheduler của Fabric hiểu múi giờ đã chọn hoặc cấu hình tương đương 00:15 UTC | Batch chạy sai giờ | V-09 |
| A-12 | Owner Duong Kim Sang xác nhận bản ghi owner trong repo (D-01 là lựa chọn trong thảo luận, chưa phải xác nhận bản ghi) | Thiếu owner được xác nhận ở G1 | V-01 |

### 8.5 Rủi ro

| ID | Rủi ro | Hệ quả | Chốt chặn hiện có | Người chấp nhận |
|---|---|---|---|---|
| R-01 | Fallback không có tuổi tối đa (D-04): bản cũ tùy ý vẫn được dùng | Danh sách market cũ, người xem có thể tưởng dữ liệu hiện hành | Cảnh báo thời điểm cập nhật; kiểm tra lại market trước đăng ký | Duong Kim Sang, 2026-10-21 (CONTEXT D-14): giữ "không đặt tuổi tối đa" với lý do luồng metadata lỗi sẽ có cảnh báo; cảnh báo nay do META-07 sở hữu (Phase 5, kiểm bằng V-19), chưa thiết kế |
| R-02 | Kiểm tra lại market là chốt chặn duy nhất và không phát hiện market mới thiếu trong snapshot cũ | Watchlist thiếu market AI mới mà không có tín hiệu | Cảnh báo lỗi batch rút ngắn thời gian dùng snapshot cũ (V-19); không phát hiện được market mới trong thời gian đó | Duong Kim Sang, 2026-10-21 (CONTEXT D-14): chấp nhận R-02 riêng sau khi được nói rõ rủi ro "market AI mới không vào watchlist, không có tín hiệu, người nhận cảnh báo là chính owner" |
| R-03 | O-01 chưa chốt | Phiên có gap/replay không thể tuyên bố đạt hoặc không đạt; nguy cơ chọn quy tắc sau khi thấy số | V-17 chặn V-15 | Chưa có |
| R-04 | O-02 chưa chốt; mẫu nhỏ làm 95% thành 100% khi n < 20; mẫu theo bản ghi bị chi phối bởi outcome sôi động | Kết luận SLO sai lệch hoặc không có ý nghĩa | V-17 | Chưa có |
| R-05 | Lệch đồng hồ collector và Fabric | Số latency sai theo cả hai hướng | V-14 | Chưa có |
| R-06 | Mục tiêu 30 giây chưa chứng minh khả thi; đo trên máy và mạng của người chạy, capacity trial chưa đo | Không đạt SLO live; kết quả khó tái lập nếu điều kiện đo không ghi lại | Quy tắc "sửa hoặc đưa lại người dùng", không đổi chuẩn âm thầm (ROADMAP Phase 6); ghi điều kiện đo vào bằng chứng | Chưa có |
| R-07 | Hạn 60 phút của batch chưa đo; scheduler có thể khởi động trễ; mẫu chỉ vài chục lần chạy | Lần trễ liên tiếp; một lần trễ là SLO không đạt theo D-03 | Ghi sự cố kèm nguyên nhân | Chưa có |
| R-08 | Dự án một người: không có người trực, không thay thế, không có mục tiêu thời gian phản hồi | Sự cố batch hoặc live không ai xử lý ngoài giờ | Không có | Chưa có |
| R-09 | Midpoint nhảy >= 5 pp trên book mỏng chỉ vì một quote update không có giao dịch | Shock gây hiểu nhầm | Hiển thị spread và trade observation quanh sự kiện (KPI-05); không tự đặt thêm ngưỡng | Chưa có |
| R-10 | Người xem đọc midpoint như xác suất đã hiệu chỉnh hoặc như khuyến nghị đầu tư | Hiểu sai dữ liệu | Ghi chú giới hạn (1.8); hiệu quả chưa đo | Chưa có |
| R-11 | Trial hết hạn trước khi xuất bằng chứng và dữ liệu mẫu | Mất bằng chứng portfolio | RETI-02 (Phase 8); hạn trial chưa xác minh | Chưa có |
| R-12 | Tài liệu REQUIREMENTS/ROADMAP chưa khớp D-04/D-10 (M-01) | Reviewer hoặc cổng đọc nhầm quyết định đã chốt thành chưa chốt | Ghi M-01 ở đây | Chưa có |

### 8.6 Điểm cần người dùng trả lời (cho điều phối)

1. Xác nhận bản ghi owner/maintainer/escalate ở 1.3 (V-01, A-12). **Còn mở**: xác nhận khi verify Phase 1.
2. D-04: **Đã trả lời 2026-10-21**: giữ "không đặt tuổi tối đa", giảm thiểu bằng cảnh báo lỗi batch (V-19). Xem 8.3.
3. Công thức KPI ở 1.4: **Đã trả lời 2026-10-21**: là định nghĩa nháp hợp lệ cho Phase 2/4; chi tiết còn mở ở Q-01..Q-07.
4. Q-06 và Q-07 (hai câu nhẹ về cách đọc D-02 và D-10).
5. O-01 và O-02 trước Phase 6 (có thể hỏi sớm hơn nếu G1 yêu cầu).

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| CONTEXT | Phase 1: Inception - Context (quyết định D-01..D-12, mục chưa chốt O-01..O-05) | `.planning/phases/01-inception/01-CONTEXT.md` | Full local file read |
| PROJECT | Polymarket AI Markets Stream Pipeline (project context, constraints, key decisions) | `.planning/PROJECT.md` | Full local file read |
| REQUIREMENTS | Requirements: Polymarket AI Markets Stream Pipeline (INCP-01, INCP-02, KPI-*, VIEW-*, acceptance details) | `.planning/REQUIREMENTS.md` | Full local file read |
| ROADMAP | Roadmap: Polymarket AI Markets Stream Pipeline (Phase 1 success criteria, cross-phase rules) | `.planning/ROADMAP.md` | Full local file read |
| FEATURES | Feature Research (KPI proposals for discussion) | `.planning/research/FEATURES.md` | Full local file read; công thức là đề xuất, chưa kiểm chứng |
| P-PRICES | Prices and Order Books (Polymarket docs) | https://docs.polymarket.com/market-data/prices-order-books | Không đọc trong phiên này; chỉ biết qua FEATURES.md, nơi ghi "official markdown fetched; book semantics inspected" |
| DE-COORD | Data Engineering Coordinator (cổng G0..G4, quy tắc chấp nhận rủi ro, NOT-RUN) | `C:/Users/sanbo/.gsd/capabilities/dataeng/skills/de-coordinator/SKILL.md` | Full local file read |
| S-DP | Building High-Quality and Trusted Data Products with Databricks | https://www.databricks.com/blog/building-high-quality-and-trusted-data-products-databricks | TV theo thư viện skill; Source Analyst không mở lại trang trong phiên này, chỉ dùng cho nguyên tắc "một owner" |
| S-LDP-BP | Best practices for Lakeflow pipelines | https://docs.databricks.com/aws/en/ldp/best-practices | TV theo thư viện skill; không mở lại trong phiên này, chỉ dùng cho nguyên tắc "real-time cần con số" |
| S-OBS | Phase 9: Design observability strategy | https://docs.databricks.com/aws/en/lakehouse-architecture/deployment-guide/observability | TV theo thư viện skill; không mở lại trong phiên này, chỉ dùng cho nguyên tắc giám sát tỷ lệ với mức quan trọng |
| S-MYTH | Databricks Lakehouse Data Modeling: Myths, Truths, and Best Practices | https://www.databricks.com/blog/databricks-lakehouse-data-modeling-myths-truths-and-best-practices | TV theo thư viện skill; không mở lại trong phiên này, chỉ dùng cho nguyên tắc một định nghĩa KPI |
