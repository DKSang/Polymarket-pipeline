# Architecture Research

**Researched:** 2026-10-21
**Status:** Updated after user selection: Lakehouse daily metadata + Eventhouse/KQL analytics. Implementation details remain proposed.

## Drivers and unresolved constraints

Fabric is required; trial-only budget, manual finite streaming sessions and a fresher-sized portfolio are established. The user selected Eventhouse/KQL history and requires daily metadata ingestion into Lakehouse before WebSocket subscription, starting at 07:00 Asia/Saigon (UTC+7; 00:00 UTC). Numerical live freshness SLO, batch completion deadline, session duration and capacity headroom are **Open**. The cloud schedule has not been deployed. No benchmark or generic wiki decision is treated as project approval.

## Candidate component flow

```text
Daily Gamma AI-tag discovery → Lakehouse raw metadata snapshots
                           → validated market/outcome/asset mapping
                           → published metadata version → session watchlist
Public market WS → Python collector → local raw journal/outbox
                                      ↓ stable envelope
                              Fabric Eventstream
                                      ↓
                             Eventhouse raw table
                                      ↓ validated/deduplicated queries
                        KQL analytical views + health/coverage
                              ↙                    ↘
                    Real-Time Dashboard      session/day history

Lakehouse metadata also enriches Eventhouse analytics through a versioned
metadata projection; its transfer mechanism remains to be verified.

Deferred: separate Spark/Delta historical analytics and Power BI reporting.
```

Raw, cleaned and serving are data guarantees, not a requirement to deploy three engines. Raw retains payloads; cleaned validates and makes retry delivery idempotent; serving implements agreed KPI definitions with coverage metadata. Poison records remain inspectable and do not silently enter published metrics.

### ADR-R1: Live serving and historical storage

- **Status:** User selected A for analytics on 2026-10-21, with mandatory Lakehouse metadata discovery. Analytics acceptance targets and implementation are still open.
- **Alternatives:** A: Eventstream + Eventhouse + Real-Time Dashboard, historical queries in KQL. B: Eventstream + Eventhouse live path plus OneLake/Delta and bounded Spark historical path. C: Eventstream + Lakehouse + periodic transforms and a query/report layer, if a minutes-level freshness target is acceptable.

| Driver | A | B | C |
|---|---|---|---|
| Live querying | Native KQL + dashboard [M-KQL, M-REFRESH] | Same live path as A | Additional write/transform/refresh delay [M-LAKE] |
| Finite-session complexity | Fewest components [Synthesis] | Adds lake synchronization, Spark and reconciliation [Synthesis] | Avoids KQL but requires another responsive serving route [Synthesis] |
| Historical learning | KQL and retained observations | Adds Delta/Spark artifacts | Delta-centered implementation |
| Cost | Unmeasured; no claim cheapest | Additional compute and synchronization to measure | Spark/serving startup and refresh cost to measure |

- **Selected scope:** A for live and historical analytics, plus daily metadata snapshots/mapping in Lakehouse. The metadata Lakehouse is required independently of whether analytical history lives in KQL. B/C remain alternatives for reconsideration, not current requirements.
- **Trade-offs:** KQL/dashboard definitions are Fabric-specific. B needs reconciliation across paths and delayed OneLake availability. C must not promise seconds-level freshness without measurement.
- **Wrong if:** The selected route fails the agreed p95 receive-to-visible target in two representative sessions, lacks required historical output, or exceeds the subsequently agreed per-session capacity budget.
- **Verification:** A bounded cloud spike after approval, then measured sink/visual latency, query results and capacity evidence. No numeric pass threshold is invented before requirements.

### ADR-R2: Processing mode by flow

- **Status:** Proposed; the source capture mode follows the user's explicit real-stream objective, while serving targets remain open.
- **Alternatives:** Continuously receive source messages during each finite session; periodically sample REST state instead. For downstream history, compare session-end incremental transforms with continuous Spark transforms.
- **Selected cadence:** Daily REST metadata batch into Lakehouse, WebSocket capture during manual sessions, KQL validation/analytics for live and historical queries. Metadata batching does not replace the source stream. Daily discovery can miss newly listed markets until the next successful batch; whether that is acceptable remains a requirements question.
- **Trade-offs:** The local machine must stay online; disconnections lose uncaptured observations. Batch history can be delayed after session completion. Continuous Spark adds state and compute but may be justified by requirements later.
- **Wrong if:** Capture cannot keep up with measured source throughput, journal backlog grows beyond the agreed recovery time, or history cannot meet its agreed availability deadline.
- **Verification:** Measure events/s, bytes/s, source/receive/sink times, queue depth, drain time and complete-run capacity usage. Pick numerical bounds during requirements and phase planning.

### ADR-R3: Build the source bridge; use managed Fabric transport

- **Status:** Proposed.
- **Alternatives:** A: Local native-WebSocket bridge with Eventstream custom endpoint. B: Local bridge writing directly through supported Eventhouse ingestion APIs, omitting Eventstream. Both need source retry/journaling. A separately hosted service is a future hosting alternative if unattended runs become required.
- **Preference:** A, because a visible Fabric stream path supports the portfolio objective and the documented endpoint accepts Event Hubs-compatible producers. B is a viable simplification if Eventstream readiness, auth or measured overhead makes A unsuitable.
- **Trade-offs:** Own source reconnect, backpressure, outbox and optional-field handling. Eventstream and KQL are vendor dependencies; retain raw JSON and standalone KPI fixtures to reduce rewrite cost. Moving off Fabric requires a transport adapter, analytical queries and dashboard replacement, not just a connection-string change.
- **Wrong if:** The target workspace cannot expose the endpoint with acceptable scoped permissions, outbound network access fails, or measured overhead violates the agreed resource budget while B succeeds.
- **Verification:** Endpoint/auth preflight and a small approved end-to-end spike. No paid connector or additional Azure resource is assumed.

## Metadata discovery dependency — added after user feedback

**User requirement:** Daily metadata with AI tag must land in Lakehouse and supply the streaming subscription. The batch starts at 07:00 Asia/Saigon (00:00 UTC); successful publication follows completion and validation, not merely the scheduled start. The following safeguards are design proposals, not yet an approved data contract:

1. Fetch all relevant pages with recorded run ID, request scope/cursor and fetch time; retain source responses as raw metadata snapshots.
2. Validate nested market status and one-to-many market/outcome/asset mapping. Retain AI-tag candidates even when not eligible for the narrower model watchlist.
3. Publish a completed version only after the run passes validation; a failed/partial scan must not replace the last complete version or imply missing markets were deleted.
4. Collector loads one published version at session start and records its version. The exact Lakehouse read/export mechanism is Open; do not assume local Python can read a Spark-managed table without authentication/network setup.
5. Translate selected markets into outcome asset IDs for native `assets_ids` subscription; retain market IDs for joins and traceability. [P-STREAM]
6. Define metadata freshness and failed-batch behavior before implementation. Daily refresh alone does not establish a 24-hour freshness guarantee when runs fail.
7. Propose freezing the watchlist during a finite session; hot reload and session-start status rechecks need explicit scoping. Preserve metadata versions used for historical labels; do not enrich all past observations with today's metadata silently.

**Alternatives for orchestration:** A scheduled Fabric pipeline invoking a bounded ingestion notebook versus a directly scheduled notebook. Both can satisfy the required cadence; runtime, permissions, retry/monitoring and cost must be checked before choosing.

**Wrong if:** Partial runs become visible, an eligible selected outcome lacks a valid subscription ID, or metadata age exceeds the eventual agreed SLA without an explicit stop/degraded state.

**Verification:** Force pagination failure; rerun the same batch; compare published mapping and prior version; validate collector subscriptions against the selected version. These are planned checks, not executed results.

## Platform conditions retained from research

1. A Lakehouse destination enforces the target schema; added fields can be omitted and incompatible records can fail. Keep heterogeneous source content as raw payload inside a stable envelope. [M-LAKE]
2. Eventhouse OneLake availability batches writes. Documentation describes default delay up to three hours for sparse streams, configurable target latency from five minutes to three hours. It is an optional historical path, not the live dashboard dependency. [M-ONELAKE]
3. Custom endpoint sources and direct-ingestion Eventhouse destinations do not support per-node pause in the inspected matrix. Do not promise that Deactivate All pauses every component. [M-PAUSE]
4. Eventstream consumption includes an activity-related flat component and data/processing operations; documentation states idle behavior after two hours without traffic. This is not evidence of zero total Fabric cost immediately after stopping Python. [M-COST]
5. Live refresh detects ingestion; use fallback/configured refresh and explicit query coverage for time-dependent freshness indicators, which can change without new data. [M-REFRESH; Synthesis]

## Suggested build dependencies

Prove source metadata and capture semantics, define numerical targets, then establish the Lakehouse discovery dependency before a complete scheduled-discovery-to-WebSocket demonstration. A bounded source-only spike may precede cloud setup. Demonstrate one outcome from the published metadata version through Fabric to a visible query; add correctness/recovery before trusting KPI rankings. These are dependency suggestions, not an approved roadmap.

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| M-KQL | Eventhouse destination modes | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/add-destination-kql-database | Full fetch; mode excerpts inspected |
| M-REFRESH | Dashboard live refresh | https://learn.microsoft.com/fabric/real-time-intelligence/dashboard-live-refresh | Full fetch; relevant excerpts inspected |
| M-LAKE | Lakehouse destination | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/add-destination-lakehouse | Full Learn fetch read |
| M-ONELAKE | Eventhouse OneLake availability | https://learn.microsoft.com/fabric/real-time-intelligence/event-house-onelake-availability | Full fetch; latency excerpts inspected |
| M-PAUSE | Pause and resume streams | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/pause-resume-data-streams | Full fetch; node support matrix inspected |
| M-COST | Eventstream capacity consumption | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/monitor-capacity-consumption | Full fetch; billing excerpts inspected |
| M-ENDPOINT | Custom endpoint source | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/add-source-custom-app | Full Learn fetch read |
| P-STREAM | Native market subscription uses outcome asset IDs | https://docs.polymarket.com/market-data/realtime-data | Official native API section rechecked, 2026-10-21 |
| USER | Daily metadata and KQL history selection | ../PROJECT.md | User instruction, 2026-10-21 |
| W-FANOUT | Stream-to-Batch Storage Architecture | ../../../wiki/architectures/stream-to-batch-storage-architecture.md | Full canonical wiki page read |
| W-MODE | Micro-Batch Versus True Streaming | ../../../wiki/decisions/micro-batch-versus-true-streaming.md | Full canonical wiki page read |
