# Polymarket Market Intelligence

## What This Is

A Microsoft Fabric portfolio project for an intern/fresher Data Engineer, serving market researchers and analysts who follow direct AI events on Polymarket. It identifies and describes the largest observed price movements within a declared watchlist, provides historical context from actual collection sessions, and makes data freshness, coverage and gaps visible. Personas are portfolio hypotheses, not evidence of interviews or measured user impact.

## Core Value

Help a researcher identify and explain price movements using traceable observations, without mistaking missing or delayed data for a quiet market.

## Requirements

### Validated

(None yet — confirmed scope is not a shipped, validated capability. Existing Fabric items and Completed jobs have not validated this project's requirements.)

### Active

- [ ] Answer four analytical questions: which watched AI markets moved most; which outcome drove each movement and in which direction; how spread and quote-change activity evolved; and what observed history and data quality support the result.
- [ ] Maintain up to 30 watched markets initially, allowing later expansion. Include only direct AI events such as model/product releases, benchmarks and AI policy; tags/keywords identify candidates, not final eligibility.
- [ ] Review the watchlist daily. Retain existing eligible markets and fill vacancies, including those created when a market closes or stops trading. Do not replace a market solely because its expected end date passed.
- [ ] Automatically prioritize eligible replacement candidates by trading volume over the most recent 24 hours. The exact source field, tie-breaking and eligibility automation remain to be researched.
- [ ] Keep the watchlist market-based, with outcome-level calculations and one ranked row per market/window. Preserve historical observations when membership changes and record the observation scope over time.
- [ ] Calculate mid-price as (best_bid + best_ask) / 2 and spread as best_ask - best_bid. Calculate signed price change as mid(T) - mid(T-W), with W = 5, 15 or 60 minutes; rank by the greatest absolute change among eligible outcomes in each market.
- [ ] Show the outcome driving the rank, signed change, both price/time anchors, spread, quote-change count and quality status. Price change 0.40 to 0.45 means +0.05 price units, not +5 percent. Quote-change count is not trading volume or message count.
- [ ] Use source event time for market KPIs, received time for processing freshness, and UTC storage timestamps. Invalid/missing event time is not silently replaced with received time.
- [ ] Start collection sessions manually and accumulate history only from actual collection. Automatically reconnect within a session and expose missing intervals; no gap backfill is required.
- [ ] Deliver a live dashboard, daily brief and history view. Show declared collection intervals and gaps; provide seven-day context only when enough observed history exists. Daily completeness refers to processing the declared observation scope, not observing 24 hours.
- [ ] Target p95 received-to-display latency <= 60 seconds during collection sessions, with measured evidence before claiming achievement.
- [ ] Apply quality per KPI/window. Retain invalid quotes with reasons but exclude them from calculations; reject missing bid/ask, out-of-range prices or bid > ask. Missing results stay missing rather than becoming zero.
- [ ] Explain insufficient window history. Do not backward-fill a quote to an earlier anchor. Expose gaps, mark quote-change counts partial across gaps, and do not count a reconnect snapshot as a price change. Retain a quiet market's valid quote while connection continuity is established.
- [ ] Recompute derived results/reports from retained raw when downstream processing fails; replay correctness still needs verification.
- [ ] First build and test the complete ingest -> raw -> transformations -> KPI -> dashboard/report flow with collection sessions of a few minutes. Only after that flow works, run longer sessions to validate time windows and latency.
- [ ] Produce reproducible portfolio evidence: code, documentation, KPI definitions, execution guidance, dashboard/report demonstration and verification results. Native Fabric definitions and resource-use evidence remain relevant deliverables from the supplied business document.

### Out of Scope

- Pre-start historical ingestion and external backfill, including the former V3 history flow — user chose history accumulated from actual sessions.
- Continuous 24/7 collection — sessions are manually started and finite.
- Trading/order placement, investment recommendations, validated anomaly detection or news-based causal explanations — the product describes observed price movements.
- Claims covering all Polymarket markets — ranking represents only the declared eligible watchlist.
- A fixed top-three research limit — explicitly left open by the user.
- Indirect AI topics such as chip stocks or crypto solely because of an AI association — user selected direct AI events.
- Purchasing additional capacity/services — constrained to currently available resources.
- Cloud resource creation, deployment or job execution during initialization — separate explicit approval is required.
- Modifying installed skills or GSD Core, or making unrelated changes to the shared Knowledge Hub — excluded. The user later authorized scoped source ingestion into the Hub; see the knowledge exception below.

## Context

### Discovery and source boundaries

Business reference supplied by the user: `C:/Users/sanbo/Code/data-engineer/docs/01-business-problem.md`, read during discovery. It includes prior-project approvals and revisions through 2026-10-20; those do not establish new-project approval or ownership. Current conversational confirmations take precedence: 30 watched markets rather than 20; no fixed three-market selection limit; daily vacancy replacement prioritized by 24-hour volume; manual sessions; Fabric required; existing resources only.

The user retained the four analytical questions, 5/15/60-minute windows, quote-based KPI/ranking approach, all three outputs and the document's quality rules. This is still a greenfield requirements baseline. Previous implementation is a reuse candidate to inspect, not a completed phase inherited from another project.

### Read-only Fabric inspection on 2026-10-21

The user identified `Polymarket-DEV` as the workspace containing previous raw ingestion and authorized inspection with the already authenticated Fabric CLI. Fabric CLI 1.7.0 was used for read-only inventory, workspace properties, directory listings and job history. No cloud jobs or mutations were performed.

- Workspace ID: `89c341b0-55ba-4b20-ac0f-18de88049aad`; reported region Southeast Asia, capacity assignment Completed, workspace Spark runtime setting 1.3. A workspace setting does not establish the runtime of a particular historical job.
- Lakehouse: `lh_polymarket`, ID `8d330969-9f75-4974-ac37-988194246f64`.
- Existing items include `nb_snapshot_gamma`, `nb_probe_websocket`, `pl_snapshot_gamma`, initialization/validation notebooks, `eh_polymarket` and `es_polymarket_ws`.
- Observed Files directories include `market_intelligence/polymarket/gamma_events`, `gamma_markets/snapshot`, and `ws_probe/stream`; the bronze Tables directory contains `gamma_responses` and `schema.json.gz`.
- Returned job history includes Completed Gamma pipeline runs on 2026-10-19 and Completed WebSocket probe runs on 2026-10-18/19. Earlier failures include an incomplete Gamma snapshot due to a page cap and a pipeline expression type error.
- These observations establish item/directory existence and reported job status only. Notebook definitions, raw contents, pagination completeness, replay, 30-market support and received-to-display latency were not verified. CLI notebook-property reads encountered `No Windows console found`; no code inspection is claimed.
- `GYM_*` items are present in the same workspace. They are not project assets or production-readiness evidence by default. Existing Eventhouse/Eventstream presence does not select them for the architecture.

Evidence collection used `fab ls`, selected `fab get ... -q ...`, and `fab job run-list`. Historical Completed statuses are not independent data-quality evidence.

### Reuse before implementation

Keep authentication/transport/resource operations in maintained upstream tools where suitable. The installed Microsoft Fabric CLI is a candidate operational interface; Polymarket maintained APIs/SDKs and existing ingest definitions must be assessed after requirements. No final ingestion, storage, processing or serving stack has been selected beyond the Fabric constraint.

The Hub's `codebase/README.md` and `codebase/upstream.md` were consulted. Their promoted offline run-evidence validator checks captured-evidence consistency within an explicit contract; it is not an ingest framework, live verifier, or production guarantee. Recorded comparisons with fabric-cli, fabric-cicd and Semantic Link Labs are version-specific history, not proof of current support. Reuse requires checking applicability and current documentation.

## Constraints

- **Platform:** Microsoft Fabric is mandatory for the portfolio; component choices remain open until requirements and source feasibility are established.
- **Budget:** Use existing available resources only. User reports 40 days of trial remaining as of this discovery on 2026-10-21; expiry, available headroom and consumption were not independently verified. Do not infer availability of separate Azure credits.
- **Runtime:** Manual collection, initially a few minutes for testing. Longer collection begins after the complete data flow works; exact durations and schedules remain open.
- **Performance:** p95 <= 60 seconds from collector receipt to displayed result is a target requiring measured verification, not an established result.
- **Evidence:** Short sessions cannot establish live 60-minute-window correctness or sustained freshness. Use appropriate deterministic fixtures early and later measured sessions, subject to agreed verification scope.
- **Knowledge:** Shared Hub `C:/Users/sanbo/Code/fabric-agent-knowledge` is read-only by default. On 2026-10-21 the user explicitly authorized the karpathy-llm-wiki Ingest workflow for sources gathered in this research, including raw snapshots, compiled articles, index and ingest log. This scoped exception does not authorize unrelated edits, changes to installed skills or automatic future ingestion. Consult index and concept/synonym searches before technical decisions; verify time-sensitive API/SDK behavior against current official sources when it becomes a dependency.
- **Authority:** Resource inspection does not authorize deployment, mutation or job execution. Do not inherit old resource ownership or prior approvals.
- **Delivery:** Interactive GSD initialization; present significant assumptions and decisions for confirmation. No claims of measured user impact without actual measurement.

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Preserve researcher/analyst and intern/fresher portfolio goals | User confirmed the business direction from the supplied document | Confirmed scope; outcome pending |
| Direct AI events; initial maximum 30 watched markets | User selected scope and initial capacity with later expansion | Confirmed scope; feasibility pending |
| Daily vacancy replacement by recent 24-hour volume | User wants to retain eligible markets and replace closed/non-trading markets | Confirmed direction; exact contract pending |
| Keep 5/15/60-minute quote-based ranking and quality rules | User retained the document's KPI/ranking approach | Confirmed scope; implementation pending |
| Live, daily and historical outputs from collected sessions | User confirmed all three outputs and no historical backfill | Confirmed scope; delivery pending |
| Manual start, automatic reconnect, visible gaps | User selected recovery without taking on external backfill | Confirmed scope; recovery verification pending |
| Fabric mandatory; existing resources only | Portfolio objective and available trial resources | Confirmed constraint; capacity measurement pending |
| Short tests until the complete data flow works, then longer sessions | User-defined order of experimentation | Confirmed; longer-session acceptance pending |
| Assess existing raw ingest as a reuse candidate | User authorized read-only workspace inspection | Inventory observed; reuse not yet validated |

## Open Questions and Assumptions

- Exact candidate eligibility/classification, 24-hour volume field and units, ties, missing volume handling, daily timezone/cutoff and watchlist versioning need explicit definitions. No silent replacement of these gaps with a custom business contract.
- Ingest universe versus the 30-market analytical watchlist has not been reconfirmed. The old document's all-open-AI-tag raw ingest is not automatically inherited.
- How long to retain raw/derived evidence, and how to preserve deliverables before trial expiry, remain open within the existing-resource budget.
- Complete quote-state reconstruction, timestamp behavior, duplicate/out-of-order handling and continuity evidence require source research and tests.
- The old document's delayed threshold and the distinction between no quote changes and processing delay need precise acceptance wording; a quiet connected market must not become stale solely from no new quote.
- Daily brief format, day boundary, UI technology, recovery retry limits, user-visible failure reporting and measurable acceptance-test duration/sample size remain to be defined in requirements/planning.
- No real-user study or time-to-insight target has been agreed. No final top-N research limit has been set.
- Exact KPI anchors and ranking ties should be carried into requirements for review, without importing unreviewed historical implementation details.

## Knowledge Sources

### Research Progress

Workflow preferences are saved in `.planning/config.json`: interactive, standard granularity, sequential, Git tracking, model inherit, research/plan check/verifier enabled, full instruction content, and all four optional PR sections enabled. Initial research is recorded in `.planning/research/SUMMARY.md` with the source registry and read-only Fabric MCP evidence. Requirements scoping and roadmap approval remain pending; no final stack has been selected.

The user-authorized Wiki ingestion created seven source-scoped articles from twelve official snapshots. This updates the earlier Query-only boundary for that explicit operation; current external behavior still needs rechecking when selected as an implementation dependency.

### Articles Consulted

The following exact local Wiki articles were consulted during discovery/initialization. They provide conceptual guidance with the evidence boundaries stated in each article; their educational examples are not current API contracts.

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-engineering/guidance.md` — start with consumers, source semantics and intended outputs before tool selection.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-source-assessment/guidance.md` — source scope, volume, pagination, change detection, ownership and reuse questions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-source-assessment/gotchas.md` — a sample or successful connection does not prove complete extraction or recovery.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric-lakehouse/prerequisites.md` — workspace identity and runtime binding; scoped distinction between metadata and execution evidence.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric-lakehouse/operate.md` — operational evidence and independent readback rather than status-only conclusions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-storage/gotchas.md` — raw publication/retry, retention and recovery are separate contracts; storing raw alone is not replay correctness.

Hub index-first retrieval and English/Vietnamese concept searches were performed using the Hub's read-only karpathy-llm-wiki Query steps. No external reference linked from the business document is claimed as independently verified here. Current official source documentation will be checked during research before technology-dependent recommendations.

## Evolution

This document evolves at phase transitions and milestone boundaries.

After each phase transition: move invalidated requirements out of scope with reasons, move verified capabilities to Validated with evidence, add newly confirmed requirements and decisions, and review What This Is.

After each milestone: review all sections, check Core Value and exclusions, and update context with actual users, feedback and measured results.

---
*Last updated: 2026-10-21 after interactive discovery and explicit approval to create PROJECT.md.*
