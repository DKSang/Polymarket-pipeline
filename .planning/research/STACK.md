# Stack Research

**Domain:** Session-based market-data analytics on Microsoft Fabric
**Researched:** 2026-10-21
**Status:** Proposed stack; cloud environment not verified.

## Recommended stack

| Component | Proposed choice | Reason | Confidence |
|---|---|---|---|
| Collector | Local Python 3.12, native public WebSocket | Small explicit bridge, manual finite sessions, visible raw protocol; avoids depending on a trading SDK for read-only collection | Medium; Python baseline proposed, socket path documented but not exercised |
| Metadata HTTP | `httpx==0.28.1` | Discovery and session-start metadata snapshots | High for version lookup; integration untested |
| WebSocket | `websockets==17.3` | Async receive loop; implement documented application heartbeat explicitly | High for version lookup; integration untested |
| Fabric producer | `azure-eventhub==5.15.1` | Eventstream custom endpoint supports Event Hubs SDK/AMQP; avoid a separately provisioned broker for v1 | High for documented endpoint support; target auth/network untested |
| Raw landing / live query | Fabric Eventstream → Eventhouse / KQL database | Preserve a stable envelope, query validated observations and session health | High for capabilities; latency/cost unknown |
| Live serving | Fabric Real-Time Dashboard | Native Eventhouse queries, refresh controls and visual refresh timestamps | High for capability; freshness target pending |
| Historical serving | Session/day aggregates in Eventhouse initially | Meets a historical analytical view without requiring a second compute engine | Medium; exact deliverable still needs scoping |
| Optional lake learning path | Eventhouse OneLake availability + bounded Spark notebook | Delta access and explicit historical transformations if the user wants a lakehouse component | High for support; medium for project fit |
| Validation | `pytest==9.1.1`, deterministic captured fixtures | KPI boundaries, retry/replay, malformed records, reconnect and gap behavior | High for version lookup; no tests implemented yet |

Package versions above were read from each project's live PyPI JSON metadata on 2026-10-21, not installed or compatibility-tested. Minimum Python reported: websockets >=3.11, azure-eventhub >=3.9, httpx >=3.8, pytest >=3.10. Resolve a tested lockfile during implementation; latest versions alone do not establish compatibility.

## Choices to avoid in the initial proposal

- Trading SDK, wallet/key setup: not needed for public market observation. Current Polymarket docs expose unified SDKs as well as native API; use one protocol boundary consistently.
- Separately operated Kafka, Azure Event Hubs namespace, Kubernetes: no demonstrated portfolio need for the added operational surface. Eventstream already exposes compatible endpoints.
- Continuous Spark just for low-volume session analytics: compare against Eventhouse queries first; no capacity benchmark supports the extra engine yet.
- Power BI Import refresh as the definition of live: use an explicit query/refresh/freshness contract. Power BI historical reports remain an optional scoped output.
- Heterogeneous source frames directly flattened into one Lakehouse destination table: schema projection can silently omit fields. Preserve `payload_raw` inside a stable envelope.

## Configuration and lifecycle

Run collector from the local machine; it must remain online during capture. Read sink credentials from the environment or an approved secret store, never source control or session evidence. Do not copy documentation's broad tenant-setting examples into project changes without verifying the actual auth requirement.

Session start should validate watchlist and sink readiness before opening source subscriptions. Session stop should stop receiving, flush the journal, drain bounded outbound retries, write a manifest and close the dashboard refresh. Explicitly inspect remaining cloud activity; stopping a producer does not prove zero capacity consumption.

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
