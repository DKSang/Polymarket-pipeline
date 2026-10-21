# Project Research Summary

**Project:** Polymarket AI Markets Stream Pipeline
**Domain:** Session-based streaming market analytics on Microsoft Fabric
**Researched:** 2026-10-21
**Confidence:** Medium overall; documented capabilities verified, runtime feasibility unverified.
**Execution:** Research performed inline; no research subagents were dispatched.

## Executive Summary

The project is feasible enough to plan a bounded integration spike, but it has not demonstrated an operating stream or cloud pipeline. Public discovery calls confirmed an AI tag and model-related candidates. They also demonstrated that the tag includes unrelated AI-company topics and that event-level `closed=false` can include closed nested markets. A reviewed, versioned market allowlist is necessary.

The user selected Eventhouse/KQL for historical analytics and added mandatory daily AI-tag metadata ingestion into Lakehouse. The resulting route is daily REST discovery → Lakehouse raw snapshots and validated market/outcome/asset mapping → collector subscription, followed by Python raw journal → Eventstream → Eventhouse/KQL → live and historical views. A separate Spark/Power BI analytical history path is deferred; the metadata Lakehouse remains required.

## Key Findings

### Recommended Stack

See [STACK.md](STACK.md): daily Lakehouse metadata batch; native WebSocket + HTTP metadata in Python; managed Fabric stream transport; KQL for live/history. Batch orchestration and collector access to Lakehouse remain implementation decisions. Exact dependency versions were checked against live package metadata but have not been installed or tested together.

### Expected Features

See [FEATURES.md](FEATURES.md): daily metadata snapshots and valid subscription mapping; reviewed watchlist; finite sessions; raw capture/replay; visible gaps/quality; movers, activity, shocks and portfolio evidence. Metadata batch starts at 07:00 Asia/Saigon (00:00 UTC); the schedule is not deployed. Live freshness is selected as 15–30 seconds from collector receipt to visible dashboard update, with a 30-second upper target; measurement percentile/coverage remains open. Windows, shock parameters, metadata freshness and batch completion deadline still require user scoping. Historical serving is selected as KQL.

### Architecture Approach

See [ARCHITECTURE.md](ARCHITECTURE.md): the user selected Eventhouse-centered analytics plus a mandatory metadata Lakehouse. Comparisons remain for traceability; detailed implementation ADRs await targets and environment verification. Raw/cleaned/serving guarantees need not correspond to three engines.

### Critical Pitfalls

See [PITFALLS.md](PITFALLS.md): mixed protocol schemas; false trade counts from quote events; unsafe source dedup; silent reconnect gaps; schema projection loss; delayed OneLake writes; post-session capacity consumption. Source observation is not complete exchange volume or proof of genuine economic activity.

## Implications for Roadmap

The following dependency groups are suggestions for roadmap creation after requirements approval:

1. **Source and environment feasibility:** daily discovery semantics, reviewed candidates, bounded native capture, event-shape evidence, read-only workspace preflight, explicit acceptance targets. No cloud writes without separate authorization.
2. **Metadata and one outcome end to end:** daily Lakehouse batch → published complete metadata mapping → collector envelope/journal → approved Fabric route → visible live output. Measure latency/consumption and test failed batch publication before expanding.
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
| Architecture selection | Medium | Analytics/metadata routes and 15–30 second live target selected; runtime feasibility and acceptance sampling unverified |

### Gaps to Address

Agree the live-target measurement protocol, metadata completion/freshness/failure behavior, windows and shock rule. Session duration is supplied by the operator at launch, with automatic stop receiving and controlled shutdown; no fixed duration was selected. The user also selected a 15–30 second collector-to-dashboard target, daily metadata start at 07:00 Asia/Saigon (00:00 UTC), Eventhouse/KQL history and a mandatory metadata Lakehouse. Then verify source behavior and target Fabric capabilities in a bounded authorized spike. Remaining trial time is user-reported; no expiry date or capacity size was inferred.

### Data engineering capability readiness

On 2026-10-21, local GSD `capability list --json` and `capability state` report `dataeng` v0.1.0 installed, enabled and active, including design/verify steps, planning/execution contributions and ship gate. Four specialist agent TOML files exist. Its coordinator was inspected at `C:/Users/sanbo/.gsd/capabilities/dataeng/skills/de-coordinator/SKILL.md`. Apply DATA-SPEC before phase planning and evidence-based DATA-VERIFY during verification; do not claim these artifacts or checks exist yet. Inline execution must be disclosed, and inline independent review has degraded independence.

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
