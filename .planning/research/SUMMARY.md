# Project Research Summary

**Project:** Polymarket AI Markets Stream Pipeline
**Domain:** Session-based streaming market analytics on Microsoft Fabric
**Researched:** 2026-10-21
**Confidence:** Medium overall; documented capabilities verified, runtime feasibility unverified.
**Execution:** Research performed inline; no research subagents were dispatched.

## Executive Summary

The project is feasible enough to plan a bounded integration spike, but it has not demonstrated an operating stream or cloud pipeline. Public discovery calls confirmed an AI tag and model-related candidates. They also demonstrated that the tag includes unrelated AI-company topics and that event-level `closed=false` can include closed nested markets. A reviewed, versioned market allowlist is necessary.

The smallest proposed Fabric route is a local Python WebSocket collector with a raw journal, a custom Eventstream endpoint, Eventhouse validation/analytics and a Real-Time Dashboard. Historical KQL views can answer session/day questions. A separate OneLake/Spark/Power BI route is an optional learning/output decision, not an assumed requirement. Preserve raw payloads and honest coverage states before building KPI rankings.

## Key Findings

### Recommended Stack

See [STACK.md](STACK.md): native WebSocket + HTTP metadata in Python; managed Fabric stream transport; KQL for live metrics; bounded historical work. Exact dependency versions were checked against live package metadata but have not been installed or tested together.

### Expected Features

See [FEATURES.md](FEATURES.md): reviewed watchlist; finite sessions; raw capture and retry/replay; visible gaps/quality; top movers, observed activity and rule-based shocks; live and historical views; portfolio evidence. Windows, shock parameters, freshness target and separate historical tools still require user scoping.

### Architecture Approach

See [ARCHITECTURE.md](ARCHITECTURE.md): compare Eventhouse-centered serving, a dual live/lake path, and a Lakehouse-centered route with looser freshness. All architecture ADRs remain proposed pending numerical targets and environment verification. Raw/cleaned/serving guarantees need not correspond to three engines.

### Critical Pitfalls

See [PITFALLS.md](PITFALLS.md): mixed protocol schemas; false trade counts from quote events; unsafe source dedup; silent reconnect gaps; schema projection loss; delayed OneLake writes; post-session capacity consumption. Source observation is not complete exchange volume or proof of genuine economic activity.

## Implications for Roadmap

The following dependency groups are suggestions for roadmap creation after requirements approval:

1. **Source and environment feasibility:** reviewed candidates, bounded native capture, event-shape evidence, read-only workspace preflight, explicit acceptance targets. No cloud writes without separate authorization.
2. **One outcome end to end:** collector envelope/journal → approved Fabric route → query and simple visible live output. Measure latency and consumption before expanding.
3. **Correctness and recovery:** retry idempotency, quarantine, reconnect coverage and replay evidence before metrics are trusted.
4. **Analytical questions:** top movers, observed trade activity/spread and configurable shock context with deterministic fixtures.
5. **History and portfolio:** session/day output, cross-view reconciliation, repeatable runbook, demo evidence and retained exports before trial expiry.

This is not an approved five-phase roadmap. Final phase count, vertical versus horizontal structure and requirement mappings remain open.

### Research Flags

- Source spike: actual event rates/size, timestamp fields, missing optional fields, reconnect behavior and current API rate limits.
- Fabric spike: workspace rights, endpoint auth/network, payload persistence, sink activation, query freshness and per-session capacity.
- History: OneLake lag/export strategy and Spark/Power BI scope if selected.

## Confidence Assessment

| Area | Confidence | Evidence / limit |
|---|---|---|
| Source discovery | High for bounded sample | Public HTTP succeeded; one page is not the universe |
| Source stream | Medium | Native API documented; no live socket sample yet |
| Fabric capabilities | High for documentation | Learn pages fetched; tenant permissions/support not checked |
| Runtime latency and cost | Low | No cloud jobs or benchmarks executed |
| Feature fit | Medium | Consistent with PROJECT.md; no external user validation |
| Architecture selection | Medium | Real alternatives compared; numerical SLO and output scope pending |

### Gaps to Address

Agree live freshness, windows, session duration, shock rule and historical deliverables. Then verify source behavior and target Fabric capabilities in a bounded authorized spike. Remaining trial time is user-reported; no expiry date or capacity size was inferred.

## Sources

Local knowledge consulted first: the parent wiki index, maintenance conventions, Message and Stream Ingestion, Types of Time, Replay, Stream-to-Batch Storage Architecture, Micro-Batch Versus True Streaming and Data-Quality Testing. Those pages establish general principles, not project-approved technology choices. External checks addressed current API/schema and Fabric-specific behavior that the inspected wiki did not establish. The wiki was not modified.

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| SOURCE | Detailed source assessment | SOURCE-ASSESSMENT.md | Includes bounded HTTP commands/results and source links |
| STACK | Stack and dependency versions | STACK.md | Includes official capability and package sources |
| FEATURES | Feature/KPI proposals | FEATURES.md | Project-context synthesis with source links |
| ARCH | Architecture alternatives | ARCHITECTURE.md | Conditional ADRs and official references |
| PITFALLS | Pitfalls and verification | PITFALLS.md | Source-specific risks and evidence links |
