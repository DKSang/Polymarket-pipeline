# Feature Research

**Domain:** Researcher-facing Polymarket AI watchlist intelligence
**Researched:** 2026-10-21
**Confidence:** HIGH for user-confirmed goals; MEDIUM for source feasibility

## Feature Landscape

This is a portfolio product with hypothetical researcher/analyst personas. No customer interviews, competitor study or measured market-demand claim is implied. The feature categories below come from confirmed discovery, not invented generic product table stakes.

### Confirmed v1 Capabilities

| Category | User capability | Complexity / dependency |
|----------|-----------------|-------------------------|
| Watchlist | Follow up to 30 direct-AI markets initially; retain eligible members and fill vacancies daily using recent 24-hour volume | Needs candidate discovery, AI eligibility, market-state interpretation and deterministic membership history |
| Identify | Rank markets by greatest absolute eligible outcome mid-price change in 5/15/60-minute event-time windows | Needs valid quote states, reliable anchors and per-window eligibility |
| Describe | Inspect outcome, sign/magnitude, both anchors, mid, spread and quote-change activity | Needs correct state-change counting rather than message counting |
| Contextualize | View daily brief and history from actual observed sessions | Depends on retained raw, session intervals and consistent KPI logic |
| Trust | See freshness, coverage, gaps, invalid quote reasons and insufficient history | Needs independent connection/session evidence, processing timestamps and clear status definitions |
| Operate | Manually start finite sessions; automatically reconnect within a session without historical backfill | Needs bounded lifecycle behavior and an explicit retry/exhaustion policy |
| Recover | Recompute derived results from already persisted raw after downstream failures | Needs reproducible input boundaries and replay verification |
| Demonstrate | Show the complete data flow before longer measured collection | Short fixtures/sessions first; live duration and performance evidence later |

### Confirmed After Research

- Ingest raw metadata for all open markets in the AI-tag candidate set. Select up to 30 watched AI markets during downstream transformation; the raw ingest is not capped at 30. Exact selection implementation is deferred to transform planning.
- Refresh status/membership in the daily batch on the first manual start per Asia/Saigon day. Keep that day's selected WebSocket subscriptions even if a member closes; do not introduce intra-day status polling, pre-session revalidation or immediate replacement. No new quote messages from that member is acceptable, subject to honest observation timestamps and handling actual subscription errors.

### Candidate Additions Requiring Confirmation

- Explain why each market is classified as a direct AI event and why it was selected/skipped; exact automation rule needs approval.
- Define a single evaluation day/timezone and when daily review occurs when sessions start manually.
- Separate connection health, price observation age, processing delay and display refresh in the UI; avoid a single ambiguous stale flag.
- Record stop/retry exhaustion distinctly from normal session end; exact limits remain open.

These clarify existing goals. They are proposed contract details, not automatically approved extra features.

### Differentiators and Later Scope

Data trust, reconstructible calculations and honest gaps are portfolio strengths rather than evidence of competitive advantage. Expansion beyond 30 markets is a future direction; no expansion target or workload promise is agreed.

## Feature Dependencies

Candidate metadata -> direct-AI eligibility -> versioned watchlist -> per-outcome subscriptions -> quote state plus continuity -> window KPIs -> market ranking -> live/daily/history outputs -> measured end-to-end verification.

Raw preservation and session metadata support both the live interpretation and recomputation. Building a historical chart without known session coverage does not satisfy the context requirement. A daily brief cannot call itself a full-day market summary when only manual sessions were observed.

## MVP Definition

First demonstrate the complete path using a short session, showing insufficient-history states honestly. Then use longer sessions to establish real 5/15/60-minute results and measured freshness. Fixture evidence can verify semantics before long collection; it cannot replace actual received-to-display measurements.

## Anti-Features

| Feature | Reason for exclusion |
|---------|----------------------|
| Trading and investment recommendations | Confirmed descriptive analytics scope |
| External historical backfill | User chose actual collected history only |
| Fixed top-three result limit | Explicitly left open |
| Universe-wide or continuous 24/7 claims | Watchlist and manual observation sessions only |
| Quote count labeled volume | Different measurement; duplicates and state repeats can inflate messages |
| Paid infrastructure and unlimited scaling | Existing-resource budget |
| Mandatory separate lineage system | User clarified lineage means the complete working data flow here |

## Sources

- `.planning/PROJECT.md` — confirmed discovery baseline, not proven user impact.
- `C:/Users/sanbo/Code/data-engineer/docs/01-business-problem.md` — prior reference; current confirmations override old scope.
- https://docs.polymarket.com/api-reference/markets/list-markets — fields need semantic validation rather than assumptions from names.
- https://docs.polymarket.com/api-reference/wss/market — snapshots, updates and lifecycle are distinct events.

## Knowledge Sources

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-source-assessment/guidance.md` — source contract and completeness questions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-source-assessment/gotchas.md` — sample success is not completeness.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/event-stream-processing/guidance.md` — choose event windows from analytical questions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/discovery.md` — metadata versus business selection policy.
