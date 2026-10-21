# Stack Research

**Domain:** Session-based Polymarket AI market intelligence on Microsoft Fabric
**Researched:** 2026-10-21
**Confidence:** HIGH for cited documentation; MEDIUM for suitability; unverified runtime feasibility
**Execution:** Research performed inline, with sequential synthesis; no subagent execution or cloud jobs.

## Recommended Stack

Fabric is the confirmed platform constraint. Everything below is a candidate for review after requirements, not a locked architecture or installed dependency set.

| Need | Candidate | Evidence and applicability | Decision boundary |
|------|-----------|----------------------------|-------------------|
| Market discovery and metadata | Official Gamma API; evaluate unified Python SDK | Market schema includes nullable volume24hr and trading-state fields; keyset endpoint documents cursor paging | Verify AI candidate scope, field semantics, page termination and market/outcome mapping before reusing existing snapshots |
| Public quotes | Official market WebSocket through upstream SDK where suitable | Wire and SDK event envelopes differ; public feed does not itself require trading workflows | Keep exact received payload, timestamps and session evidence; fixture-test protocol/version adaptation |
| Python SDK | Polymarket unified py-sdk / package polymarket-client | Captured upstream main metadata says 0.13.0 and Python >=3.11; beta line may contain minor breaking changes | Candidate version, not a registry-latest or installation claim. Pin a tested published release/revision during implementation |
| Fabric operations | Existing Fabric CLI 1.7.0 and read-only Fabric MCP | Used successfully for inventory; CLI notebook metadata retrieval had a console error | Reuse maintained clients/APIs; do not build a new management framework |
| Raw persistence and batch recomputation | Existing lh_polymarket Lakehouse / OneLake candidate | Raw directories observed, contents and publication protocol not verified | Preserve original payload and explicit completed collection boundaries; table labels alone do not establish raw fidelity |
| Live ingestion route | Existing Eventstream custom endpoint candidate with supported upstream producer SDK | Microsoft documents Event Hubs, AMQP and Kafka endpoint protocols | External WebSocket adapter still needs a host and resource budget; endpoint presence does not prove Polymarket connector support |
| Live query and display | Existing Eventhouse + a Fabric Real-Time Dashboard candidate | Live refresh uses ingestion detection with rate limit/fallback | Test quiet-window behavior, visibility, query load and measured received-to-display latency |
| Alternative display | Power BI DirectQuery with automatic page refresh | Admin/capacity settings constrain refresh; Import does not support that APR mechanism | Inspect actual permissions/settings before claiming sub-minute capability |
| Transform and historical outputs | Fabric notebook/SQL/KQL according to final serving contract | Batch/history and live paths may share definitions without identical execution engines | Pick the smallest verified path; maintain a single KPI contract and compare outputs |

## Supporting Libraries and Version Scope

The upstream pyproject snapshot declares httpx[http2]>=0.27,<1, pydantic>=2,<3 and websockets>=13,<16. These are the SDK's declared ranges, not installation recommendations for a shared Fabric runtime. No packages were installed during initialization.

Official Runtime 1.3 documentation fetched in full reports Python 3.11 and an EOSA lifecycle status. A search snippet still described GA, and a comparison snippet disagreed on Delta version. Prefer the full runtime page for that specific runtime; verify the supported-runtime matrix again before implementation. Matching Python version does not establish full dependency compatibility or justify changing the workspace runtime.

## Installation

Deferred. No pip installs, environment changes, capacity changes or Fabric execution are part of this research. First inspect the actual target notebook environment and agree on dependency pins and resource scope.

## Alternatives Considered

| Alternative | Why considered | Why not selected now |
|-------------|----------------|---------------------|
| Existing Gamma pipeline and WebSocket probe | User already invested in raw ingest | Completed runs and folders do not prove complete extraction or support for the newly confirmed scope |
| Lakehouse-only processing and serving | Fewer services and straightforward batch/history reuse | Buffering, query visibility and refresh all consume latency budget; must demonstrate the live target |
| Eventstream -> Eventhouse for live, Lakehouse for history | Native Fabric streaming/query/display capabilities already have candidate items | Extra paths require reconciliation, lifecycle control, raw-fidelity checks and consumption measurement |
| Bespoke HTTP/WebSocket transport | Full control of envelope and reconnect behavior | Prefer maintained SDKs; custom transport only if a documented functional or runtime gap remains |
| Paid capacity, external VM, separate always-on services | Potentially stable runtime hosting | Outside the confirmed existing-resource budget and manually started session model |
| External historical provider | Could supply pre-start history | Explicitly outside v1; no backfill required |

## Upstream Reuse and Custom Code Boundary

Prefer the official SDK for public-data transport, Fabric CLI/MCP/native APIs for resource access, and documented endpoint producer SDKs. Project-specific code is justified for watchlist continuity, AI eligibility policy, capture envelopes, session/gap accounting, quote-state transitions, KPI eligibility and evidence correlation; those are domain contracts upstream transport cannot decide for us. Do not implement a new knowledge/orchestration framework.

The Hub's promoted run-evidence checker is an optional offline verifier under its own captured-evidence contract. The historical upstream comparison in codebase/upstream.md does not establish current SDK support or validate this project's data.

## Sources

- https://docs.polymarket.com/api-reference/markets/list-markets — metadata fields and nullability.
- https://docs.polymarket.com/api-reference/markets/list-markets-keyset-pagination — cursor and termination contract.
- https://docs.polymarket.com/api-reference/wss/market — wire feed contract.
- https://docs.polymarket.com/market-data/realtime-data — unified SDK event model.
- https://github.com/Polymarket/py-sdk — official SDK; source snapshots and SHA-256 are recorded in SOURCES.md.
- https://learn.microsoft.com/en-us/fabric/real-time-intelligence/event-streams/add-source-custom-app — producer protocols.
- https://learn.microsoft.com/en-us/fabric/real-time-intelligence/dashboard-live-refresh — detection and fallback behavior.
- https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-automatic-page-refresh — storage mode and admin constraints.
- https://learn.microsoft.com/en-us/fabric/data-engineering/runtime-1-3 — full runtime page, preferred over stale search snippets.

## Knowledge Sources

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric-lakehouse/guidance.md` — architecture before tool mapping and source semantics.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/python-sdk.md` — upstream package, version scope and compatibility gaps.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/realtime-serving.md` — display freshness trade-offs.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/runtime-compatibility.md` — runtime lifecycle/version distinction.

*Stack choices remain proposed; readiness requires requirements approval and later implementation verification.*
