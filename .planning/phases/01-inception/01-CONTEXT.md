# Phase 1: Inception - Context

**Gathered:** 2026-10-21
**Status:** Ready for planning (còn một mục SLO chưa chốt, xem `<open>`)

<domain>
## Phase Boundary

Phase 1 ghi lại owner và mục tiêu đo được cho data product, đủ để thiết kế ở Phase 2 không bị cổng G1 của dataeng chặn (INCP-01, INCP-02). Đầu ra là tài liệu trong repo: bản ghi owner/maintainer/escalate và DATA-SPEC mục 1 (nghiệp vụ, KPI, SLO bằng con số kèm cách đo, giá trị và giới hạn portfolio).

Không viết pipeline, không chạm cloud, không đo độ trễ thật. Mọi con số SLO ở đây là mục tiêu, chưa phải kết quả đo. Phase này là data phase theo cổng G0 (ROADMAP "Cross-phase rules"): dataeng chạy DATA-SPEC mục 1 ở plan:pre.

</domain>

<decisions>
## Implementation Decisions

Nhãn: **[NGƯỜI DÙNG]** là quyết định người dùng chọn trong phiên thảo luận ngày 2026-10-21. **[ĐÃ CHỐT TRƯỚC]** là quyết định có sẵn trong PROJECT.md/REQUIREMENTS.md, không hỏi lại.

### Owner (INCP-01)
- **D-01 [NGƯỜI DÙNG]:** Owner, người bảo trì và đầu mối escalate là cùng một người: Duong Kim Sang (git user của repo). Dự án một người, ghi rõ không có người thứ hai. Không ghi email hay thông tin liên hệ riêng tư vào repo.

### SLO batch metadata (INCP-02)
- **D-02 [NGƯỜI DÙNG]:** Batch bắt đầu 07:00 Asia/Saigon (00:00 UTC) phải hoàn tất trước 08:00 (60 phút). "Hoàn tất" nghĩa là phiên bản metadata đầy đủ đã được công bố vào Lakehouse. Đây là mục tiêu, chưa là cam kết cho tới khi đo ở Phase 5.
- **D-03 [NGƯỜI DÙNG]:** Đánh giá theo từng lần chạy theo lịch: mọi lần chạy đều phải xong trước 08:00. Lần trễ hoặc lỗi được ghi là sự cố kèm nguyên nhân, và dùng làm bằng chứng cho hành vi fallback. Không dùng tỷ lệ phần trăm số ngày vì mẫu chỉ vài chục lần chạy trong trial.
- **D-04 [NGƯỜI DÙNG]:** Không đặt tuổi tối đa cho metadata fallback. Bản thành công gần nhất luôn được dùng khi batch mới lỗi, kèm cảnh báo thời điểm cập nhật và kiểm tra lại market trước đăng ký. Đây là lựa chọn có chủ ý, không phải mục bỏ trống: INCP-02 ghi "không đặt tuổi tối đa, theo lựa chọn của người dùng ngày 2026-10-21" và nêu rủi ro là chỉ còn bước kiểm tra lại market làm chốt chặn. Nếu cổng G1 coi đây là "SLA không có số", trả lại người dùng, không tự điền con số.
- **D-05 [ĐÃ CHỐT TRƯỚC]:** Batch lỗi thì dùng bản thành công gần nhất, cảnh báo thời điểm cập nhật, kiểm tra lại market trước đăng ký (PROJECT.md).

### KPI và consumer giả thuyết (INCP-02)
- **D-06 [ĐÃ CHỐT TRƯỚC]:** Movers theo cửa sổ 5/15/60 phút, thay đổi midpoint tính bằng điểm phần trăm. Shock là midpoint đổi tuyệt đối ≥5 điểm phần trăm trong 5 phút, cấu hình được. Thiếu baseline hiện "chưa đủ dữ liệu", không điền giá giả qua gap. Công thức đề xuất nằm ở `.planning/research/FEATURES.md` mục "KPI proposals for discussion".
- **D-07 [NGƯỜI DÙNG]:** Top movers xếp hạng theo từng outcome (asset ID) riêng. Market nhị phân có hai outcome gần đối xứng, nên quy tắc làm gọn (tránh lặp thông tin) để Phase 2/4 thiết kế, không chốt ở đây.
- **D-08 [NGƯỜI DÙNG]:** "Sôi động" có hai trạng thái, không có ngưỡng số: outcome có ít nhất một trade observation trong cửa sổ đang xem là "có giao dịch quan sát", ngược lại là "chỉ quote". Vẫn hiển thị số trade quan sát trên mỗi phút được phủ, khối lượng/giá trị khi có size, và spread để người xem tự đánh giá. Không gắn nhãn "sôi động" bằng ngưỡng tự đặt. Missing size là unknown, không phải 0.
- **D-09 [NGƯỜI DÙNG]:** Consumer giả thuyết là người theo dõi thị trường dự đoán về AI, với ba quyết định giả thuyết: Movers → chọn outcome nào đang đổi nhanh nhất để xem tiếp. Activity → biết quote của outcome đó có giao dịch hỗ trợ hay không. Shock → biết khi nào có biến động đủ lớn để mở xem bối cảnh. Ghi trong DATA-SPEC mục 1 dưới nhãn giả thuyết portfolio, nêu rõ chưa có phỏng vấn hay đo lường người dùng thật, và không phải khuyến nghị đầu tư.

### SLO độ tươi live (INCP-02)
- **D-10 [NGƯỜI DÙNG]:** Mục tiêu 15–30 giây từ collector receive tới hiển thị dashboard được coi là đạt khi ≥95% mẫu ≤30 giây. Báo cáo p50, p95 và max, không bỏ mẫu chậm; mẫu vượt 30 giây được liệt kê riêng. Đây là lựa chọn tỷ lệ có chủ ý của người dùng, không phải thay thế ngầm cho mục tiêu ban đầu.
- **D-11 [NGƯỜI DÙNG]:** Điểm đo cuối là lúc bản ghi xuất hiện trên dashboard, không phải lúc gửi vào Eventstream. Đo tự động bằng dấu thời gian: collector receive tới lần refresh dashboard đầu tiên chứa bản ghi đó, đối chiếu với ingestion time ở Eventhouse. Thêm một lần kiểm tay (quay màn hình) để xác nhận số tự động khớp với điều người xem thấy.
- **D-12 [ĐÃ CHỐT TRƯỚC]:** Độ tươi phải tách thời điểm nguồn, thời điểm collector nhận, độ trễ sink và thời điểm refresh dashboard (FEATURES.md). Ingestion, query và refresh đều nằm trong phép đo.

### Claude's Discretion
- Vị trí và định dạng bản ghi owner trong repo (file riêng hay mục trong DATA-SPEC), miễn nằm trong repo và người dùng xác nhận (success criterion 1).
- Ngôn ngữ và cách trình bày DATA-SPEC mục 1 (mặc định tiếng Việt như các tài liệu hiện có).
- Cách diễn đạt phần giá trị và giới hạn portfolio (success criterion 4).

</decisions>

<open>
## Chưa chốt (không tự điền)

- **O-01:** Cách tính SLO live lúc lỗi hoặc recovery: mất kết nối WebSocket, sink chậm rồi chạy bù, phát lại dữ liệu đã thu. Người dùng chọn "chưa chốt". INCP-02 phải ghi rõ mục này chưa chốt; phương án đã trình bày để tham khảo: (a) tính mọi bản ghi live, tách gap và replay ra báo cáo riêng, hoặc (b) tách hai tỷ lệ bình thường/recovery. Cần người dùng quyết trước Phase 6; cổng G1 có thể hỏi lại.
- **O-02:** Chi tiết đơn vị "mẫu" của SLO live (mỗi bản ghi hay mỗi outcome mỗi khoảng thời gian), số mẫu tối thiểu để tuyên bố đạt/không đạt, và phiên nào đủ điều kiện. Chưa được thảo luận; DATA-SPEC mục 1 và 5.6 phải định nghĩa, và đưa lại người dùng nếu ảnh hưởng ý nghĩa của 95%.
- **O-03:** Hành vi khi market không còn hợp lệ lúc kiểm tra lại với fallback (loại và chạy tiếp, hay dừng cả phiên). Người dùng để Phase 2 quyết. PROJECT.md đã chốt rằng subscription không hợp lệ khi không có snapshot thành công hoặc không kiểm tra được market.
- **O-04:** Dữ liệu nhạy cảm: chưa thảo luận. Dữ liệu là market công khai của Polymarket; chưa kiểm tra xem payload có chứa định danh ví hay thông tin cá nhân không. `[CHƯA KIỂM CHỨNG]`, Phase 2 (source inventory) phải xác nhận trước khi viết contract.
- **O-05:** Các probe dataeng về ngữ nghĩa thay đổi của nguồn, retention nguồn/backfill, dữ liệu đến muộn thuộc DSGN-01 (Phase 2). Hành vi khi dữ liệu xấu theo từng loại lỗi thuộc REL-03 (Phase 4). Backfill trước khi bắt đầu thu đã nằm ngoài phạm vi (REQUIREMENTS.md).

</open>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Dự án và phạm vi
- `.planning/PROJECT.md` — ràng buộc (Runtime, Live freshness, Authority), quyết định đã chốt, ranh giới cloud.
- `.planning/REQUIREMENTS.md` — INCP-01, INCP-02; mục "Acceptance details for phase design" (không âm thầm đổi sang p95 mà không có quyết định của người dùng; giờ batch là giờ bắt đầu, không tự suy ra deadline).
- `.planning/ROADMAP.md` — Phase 1 (Goal, Success Criteria 1–4); "Cross-phase rules" về dataeng, DATA-SPEC mục 1–3 và 4.0 ở cấp dự án.

### Nghiên cứu
- `.planning/research/FEATURES.md` — đề xuất công thức KPI (price basis, movement, activity, spread, shock, freshness).
- `.planning/research/SUMMARY.md` — bối cảnh tổng hợp và "Gaps to Address".
- `.planning/research/SOURCE-ASSESSMENT.md` — mẫu nguồn đã đo (dùng ở Phase 2, tham khảo cho O-04).

### Capability dataeng
- `C:/Users/sanbo/.gsd/capabilities/dataeng/skills/de-coordinator/SKILL.md` — cổng G0/G1/G2, cấu trúc DATA-SPEC, nhãn bằng chứng, quy tắc chạy nội tuyến (`executed_inline`, `independence: degraded`).
- `C:/Users/sanbo/.gsd/capabilities/dataeng/fragments/discuss-probes.md` — bảy probe vùng xám đã đối chiếu ở O-05.

### Vault tri thức
- `../CLAUDE.md` — chính sách wiki-first, chỉ đọc theo mặc định. Wiki được tra cho SLO/owner/freshness nhưng không có trang chuyên đề; không có trích dẫn wiki nào được dùng làm căn cứ quyết định ở trên.

</canonical_refs>

<code_context>
## Existing Code Insights

Repo chưa có mã nguồn; chỉ có tài liệu `.planning/` và `.claude/CLAUDE.md`. Không có tài sản tái sử dụng hay mẫu code nào ràng buộc Phase 1.

### Reusable Assets
- `.planning/research/FEATURES.md`: nguồn công thức KPI để chuyển vào DATA-SPEC mục 1.

### Established Patterns
- Tài liệu dự án viết bằng tiếng Việt, thuật ngữ kỹ thuật giữ nguyên tiếng Anh.
- Nhãn bằng chứng của dataeng (`[NGUỒN:..]`, `[SUY LUẬN]`, `[KIỂM CHỨNG:V-xx]`, `[CHƯA KIỂM CHỨNG]`) áp dụng cho DATA-SPEC.

### Integration Points
- Đầu ra Phase 1 (DATA-SPEC mục 1, bản ghi owner) là đầu vào của Phase 2 (source inventory, contract, ADR).

</code_context>

<specifics>
## Specific Ideas

- Con số SLO nào chưa được người dùng chốt thì ghi "chưa chốt", không điền giá trị gợi ý (đã nhấn mạnh trong REQUIREMENTS.md và ROADMAP success criterion 3).
- Phần giới hạn portfolio cần ghi rõ: không có đo lường người dùng thật, không có bằng chứng latency, chưa có hành động cloud.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 1-Inception*
*Context gathered: 2026-10-21*
