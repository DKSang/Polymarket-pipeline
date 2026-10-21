# Project Research Summary

**Project:** Polymarket Market Intelligence
**Domain:** Session-based AI-market quote intelligence on Microsoft Fabric
**Researched:** 2026-10-21
**Confidence:** MEDIUM overall
**Workflow:** Interactive, standard granularity, sequential execution, model inherit. Four research dimensions and synthesis were completed inline; no subagents or cloud jobs were run.

## Executive Summary

The confirmed business goal is feasible to investigate with official public metadata/quote interfaces and existing Fabric resources, but no end-to-end implementation has yet been proven. Source fidelity, watchlist membership, session continuity and event-time KPI semantics are more important initial design boundaries than choosing a specific Fabric serving stack.

Evaluate official Polymarket SDKs before custom transport and assess the existing Gamma pipeline/WebSocket probe before reusing them. Keep the live serving decision open between a Lakehouse-centered path and an Eventhouse/Real-Time Dashboard path until source requirements and measurable latency feasibility are clear. Both must preserve observed scope and avoid claiming complete history or current data from a refreshed visual alone.

## Key Findings

### Recommended Stack Direction

Fabric is mandatory; component choices remain candidates. The official unified Python SDK's captured main metadata says package polymarket-client 0.13.0 and Python >=3.11. This is not a registry-latest or installation result. Its public-data and streaming interfaces are worth evaluating before building custom REST/WebSocket transport. See [STACK.md](STACK.md).

### Expected Features

The user-confirmed core is: up to 30 direct-AI markets, retain eligible members and fill vacancies daily using 24-hour volume, quote-based 5/15/60-minute ranking, descriptive metrics, honest coverage/gaps, manual sessions, automatic reconnect without backfill, and live/daily/history outputs. Research found necessary contract questions, not approved extra product scope. See [FEATURES.md](FEATURES.md).

### Architecture Approach

Separate original observations and session evidence from reconstructed quote state, KPI evaluation and serving. Measure the full receipt-to-visible path. Eventstream Lakehouse projection can lose fields, and dashboard ingestion detection is not event-time evaluation. These documented boundaries favor testing the smallest complete vertical path first, then adding depth and long-session evidence. This is a proposal; project structure mode and final stack remain to be approved. See [ARCHITECTURE.md](ARCHITECTURE.md).

### Critical Pitfalls

1. Page-capped Gamma snapshots, nullable volume fields and automatic AI classification can undermine the watchlist before KPI work starts.
2. SDK and wire event envelopes differ; snapshots, duplicate updates and state transitions cannot be counted interchangeably.
3. Eventstream projection/buffering and route startup need explicit readback/continuity checks; raw labels do not prove fidelity.
4. p95 <=60 seconds is an end-to-end measurement target, not a dashboard interval setting. Quiet-window behavior needs testing too.
5. Trial resources expire, and Runtime 1.3's full page now reports EOSA despite a GA search snippet. Verify the supported target environment before implementation. See [PITFALLS.md](PITFALLS.md).

## Read-Only Environment Evidence

Fabric MCP returned workspace Polymarket-DEV assigned to capacity 2fe5a565-7fc3-4254-98e2-ece6c093e468, SKU FTL64, state Active, region Southeast Asia. It confirmed Gamma and WebSocket directories in lh_polymarket. This does not establish remaining trial days, utilization, raw contents, code correctness, schema completeness or new-project ownership of every item. The earlier CLI job-history observations remain historical statuses, not data verification. See [captured MCP evidence](evidence/fabric-mcp-inspection.json).

No cloud job, deployment, resource mutation, package install or runtime change was performed. All research and documentation changes were local.

## Implications for Roadmap

Proposed dependency groups, not an approved ROADMAP:

1. **Source contracts and reuse assessment:** decide ingest scope, candidate policy and actual artifact ownership; inspect existing ingest definitions/raw and runtime support.
2. **First complete short-session slice:** source -> preserved raw -> minimal valid/insufficient KPI result -> display, with declared scope and stop behavior.
3. **Watchlist and quote correctness:** daily membership continuity, official pagination, event adaptation, transitions, gaps and deterministic input fixtures.
4. **Window analytics and live trust:** explainable 5/15/60-minute outcomes, ranking and honest freshness/status behavior.
5. **Daily/history and replay:** coherent outputs on observed intervals and reproducible derived recomputation.
6. **Longer measured verification and portfolio evidence:** only once the full data flow works; agree session duration/sample sizes, measure end-to-end freshness, preserve evidence before trial expiry.

Order/grouping will be adjusted after requirements and the GSD structure-mode choice. Do not mark previous ingest or Agent Gym experiments as a completed phase.

### Research Flags

- Source/runtime phase: recheck API/SDK contracts and supported Fabric runtime status before dependency selection.
- Serving phase: verify capacity/admin refresh permissions, query visibility and resource usage rather than inferring them from item existence.
- KPI/replay phase: agree anchor, tie, late/out-of-order and coverage semantics; fixtures do not prove actual source losslessness.
- Longer verification: duration/sample size is still open; do not invent acceptance thresholds beyond user-confirmed p95 <=60 seconds.

## Confidence Assessment

| Area | Confidence | Boundary |
|------|------------|----------|
| Business scope | HIGH | Explicit user confirmations; no user-impact study |
| Official source shapes | HIGH | Retrieved docs as of collection date; no live market-data test |
| SDK suitability | MEDIUM | Official code inspected; no install/import or dependency resolution |
| Fabric capacity identity/state | HIGH | Read-only metadata, not utilization/expiry |
| Architecture feasibility | MEDIUM | Candidate mappings, no performance trial |
| End-to-end correctness/freshness | UNVERIFIED | Requires implementation and separately approved measured sessions |

### Gaps to Address Before Freezing Requirements

- Resolved after research: raw metadata covers all open markets in the AI-tag candidate set; selection of up to 30 watched AI markets belongs to downstream transformation. Verify the exact source tag mapping during implementation; design the selection method in transform planning.
- Automatic AI-direct eligibility policy and ambiguous candidates.
- Meaning of market stopped trading; transient source failures must not silently cause replacement.
- Volume field, null/tie handling and daily cutoff/review semantics under manual operation.
- Distinction between quiet quotes, unhealthy connection, delayed processing and ended sessions.
- Required raw retention/evidence preservation and the precise long-session acceptance protocol.

## Knowledge Ingestion and Authority

The user's explicit request on 2026-10-21 authorized an exception to the previously read-only Hub rule, limited to ingesting the researched sources. Installed skills/GSD Core were not changed. Twelve official source snapshots were ingested sequentially into seven Wiki articles, with raw references, index and ingest log updates. No Hub commit was made.

Scoped evidence-check results: no evidence errors; retrieval-date fidelity suspects were reviewed as metadata, not claims about publisher dates. Two pre-existing unreferenced raw files were reported and left untouched. Link/provenance validation is distinct from production validation. Use Wiki retrieval first on future work; current external behavior still needs revalidation when a time-sensitive dependency is selected.

## Sources

The primary-source registry, snapshots, scope and hash evidence are listed in [SOURCES.md](SOURCES.md). Raw Microsoft pages were fetched through Microsoft Learn MCP; official Polymarket pages/SDK sources were retrieved directly. Full Runtime 1.3 content takes precedence over conflicting search snippets. No external historical provider or cloud test was used.

## Knowledge Sources

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-source-assessment/guidance.md` — discovery and completeness questions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/event-stream-processing/guidance.md` — align clocks/windows with analytical intent.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/event-stream-processing/gotchas.md` — limits of educational claims and timing evidence.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/discovery.md` — metadata and pagination contract.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/realtime-quotes.md` — wire/SDK and snapshot/state semantics.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/python-sdk.md` — upstream dependency scope.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/realtime-serving.md` — freshness and refresh mechanisms.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/eventstream-ingestion-boundaries.md` — raw fidelity and startup routing risks.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/trial-capacity.md` — evidence preservation before expiry.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/runtime-compatibility.md` — runtime lifecycle and compatibility limits.

*Research complete. Next gate: interactive feature scoping and unresolved contract decisions before REQUIREMENTS.md; final architecture and ROADMAP.md are not approved yet.*
