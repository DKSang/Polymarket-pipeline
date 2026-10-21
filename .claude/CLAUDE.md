<!-- GSD:project-start source:PROJECT.md -->

## Project

**Polymarket AI Markets Stream Pipeline**

Một project portfolio data engineering trên Microsoft Fabric: pipeline end-to-end ở mức fresher, nhận dữ liệu stream thật từ Polymarket cho các market thuộc chủ đề AI (các model), biến đổi qua các lớp dữ liệu và phục vụ qua dashboard live cùng báo cáo lịch sử. Người xem chính là nhà tuyển dụng/interviewer đánh giá năng lực xây pipeline hoàn chỉnh; người dùng giả định của sản phẩm dữ liệu là người theo dõi thị trường dự đoán về AI. Cả hai chỉ là giả thuyết portfolio, chưa có phỏng vấn hay đo lường thật.

**Core Value:** Cho thấy một pipeline stream thật chạy được từ nguồn đến dashboard trên Fabric, đủ rõ để người xem kiểm chứng từng bước (ingest → lưu → biến đổi → phục vụ) và tin vào số liệu hiển thị.

### Constraints

- **Platform**: Microsoft Fabric bắt buộc — mục tiêu portfolio.
- **Budget**: Dùng Fabric trial; người dùng báo còn hơn 30 ngày (chưa xác minh hạn và mức capacity). Không giả định có ngân sách mua thêm.
- **Runtime**: Stream chạy thủ công, người chạy truyền thời lượng hữu hạn cho từng phiên; collector tự dừng nhận khi hết thời lượng rồi đóng phiên có kiểm soát. Metadata batch bắt đầu lúc 07:00 hằng ngày theo Asia/Saigon (UTC+7), tương đương 00:00 UTC. Đây là lịch bắt đầu, không phải cam kết giờ hoàn tất. Batch lỗi thì dùng bản thành công gần nhất, cảnh báo thời điểm cập nhật và kiểm tra lại market trước đăng ký; lịch cloud chưa được triển khai.
- **Level**: Độ phức tạp phải hợp với trình độ fresher — ưu tiên rõ ràng, kiểm chứng được hơn là phức tạp.
- **Live freshness**: Người dùng chọn 15–30 giây từ collector receive đến dữ liệu được hiển thị trên dashboard; 30 giây là cận trên mục tiêu. Đo cả ingestion, query và refresh, không chỉ thời gian gửi vào Eventstream. Chưa chốt tỷ lệ đạt/percentile và cách đánh giá lúc lỗi/recovery.
- **Authority**: Kiểm tra tài nguyên chỉ đọc không đồng nghĩa được tạo, triển khai hay chạy job trên cloud; các thao tác đó cần duyệt riêng.

<!-- GSD:project-end -->

<!-- GSD:stack-start source:research/STACK.md -->

## Technology Stack

## Recommended stack

| Component | Proposed choice | Reason | Confidence |
|---|---|---|---|
| Collector | Local Python 3.12, native public WebSocket | Small explicit bridge, manual finite sessions, visible raw protocol; avoids depending on a trading SDK for read-only collection | Medium; Python baseline proposed, socket path documented but not exercised |
| Metadata HTTP | `httpx==0.28.1` | Discovery and session-start metadata snapshots | High for version lookup; integration untested |
| WebSocket | `websockets==17.3` | Async receive loop; implement documented application heartbeat explicitly | High for version lookup; integration untested |
| Fabric producer | `azure-eventhub==5.15.1` | Eventstream custom endpoint supports Event Hubs SDK/AMQP; avoid a separately provisioned broker for v1 | High for documented endpoint support; target auth/network untested |
| Metadata storage | Fabric Lakehouse, daily raw snapshots + validated market/outcome/asset mapping | User-required discovery source for WebSocket subscriptions; publish complete metadata versions | User requirement confirmed; persistence/read path untested |
| Metadata batch orchestration | Scheduled bounded Fabric notebook or pipeline + notebook | Daily start at 07:00 Asia/Saigon (00:00 UTC) selected; exact mechanism to compare during phase design | Medium; workspace/schedule unverified |
| Raw landing / live query | Fabric Eventstream → Eventhouse / KQL database | Preserve a stable envelope, query validated observations and session health | High for capabilities; latency/cost unknown |
| Live serving | Fabric Real-Time Dashboard | Native Eventhouse queries, refresh controls and visual refresh timestamps | High for capability; freshness target pending |
| Historical serving | Session/day aggregates in Eventhouse/KQL | User selected this route on 2026-10-21 | Selected; acceptance targets pending |
| Optional analytical lake extension | Eventhouse OneLake availability + bounded Spark history transforms | Separate from mandatory metadata Lakehouse; deferred analytical expansion | High for support; not in current selected scope |
| Validation | `pytest==9.1.1`, deterministic captured fixtures | KPI boundaries, retry/replay, malformed records, reconnect and gap behavior | High for version lookup; no tests implemented yet |

## Choices to avoid in the initial proposal

- Trading SDK, wallet/key setup: not needed for public market observation. Current Polymarket docs expose unified SDKs as well as native API; use one protocol boundary consistently.
- Separately operated Kafka, Azure Event Hubs namespace, Kubernetes: no demonstrated portfolio need for the added operational surface. Eventstream already exposes compatible endpoints.
- Continuous Spark just for low-volume session analytics: compare against Eventhouse queries first; no capacity benchmark supports the extra engine yet.
- Power BI Import refresh as the definition of live: use an explicit query/refresh/freshness contract. Power BI historical reports remain an optional scoped output.
- Heterogeneous source frames directly flattened into one Lakehouse destination table: schema projection can silently omit fields. Preserve `payload_raw` inside a stable envelope.

## Configuration and lifecycle

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| M-ENDPOINT | Add a custom endpoint source | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/add-source-custom-app | Full Learn fetch read |
| M-KQL | Add an Eventhouse destination | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/add-destination-kql-database | Full fetch; mode excerpts inspected |
| M-REFRESH | Live refresh in Real-Time Dashboard | https://learn.microsoft.com/fabric/real-time-intelligence/dashboard-live-refresh | Full fetch; refresh excerpts inspected |
| M-LAKE | Add a lakehouse destination | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/add-destination-lakehouse | Full Learn fetch read |
| M-ONELAKE | Eventhouse OneLake availability | https://learn.microsoft.com/fabric/real-time-intelligence/event-house-onelake-availability | Full fetch; batching/latency excerpts inspected |
| PY-WS | websockets metadata | https://pypi.org/pypi/websockets/json | Live version and requires_python inspected |
| PY-EH | azure-eventhub metadata | https://pypi.org/pypi/azure-eventhub/json | Live version and requires_python inspected |
| PY-HTTP | httpx metadata | https://pypi.org/pypi/httpx/json | Live version and requires_python inspected |
| PY-TEST | pytest metadata | https://pypi.org/pypi/pytest/json | Live version and requires_python inspected |
<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->

## Conventions

Conventions not yet established. Will populate as patterns emerge during development.
<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->

## Architecture

Architecture not yet mapped. Follow existing patterns found in the codebase.
<!-- GSD:architecture-end -->

<!-- GSD:skills-start source:skills/ -->

## Project Skills

No project skills found. Add skills to any of: `.claude/skills/`, `.agents/skills/`, `.cursor/skills/`, `.github/skills/`, or `.codex/skills/` with a `SKILL.md` index file.
<!-- GSD:skills-end -->

<!-- GSD:workflow-start source:GSD defaults -->

## GSD Workflow Enforcement

Before using Edit, Write, or other file-changing tools, start work through a GSD command so planning artifacts and execution context stay in sync.

Use these entry points:
- `/gsd-fast` for a trivial task inline, with no subagents and no PLAN.md
- `/gsd-quick` for small fixes, doc updates, and ad-hoc tasks
- `/gsd-debug` for investigation and bug fixing
- `/gsd-execute-phase` for planned phase work

Do not make direct repo edits outside a GSD workflow unless the user explicitly asks to bypass it.
<!-- GSD:workflow-end -->

<!-- GSD:profile-start -->

## Developer Profile

> Profile not yet configured. Run `/gsd-profile-user` to generate your developer profile.
> This section is managed by `generate-claude-profile` -- do not edit manually.
<!-- GSD:profile-end -->
