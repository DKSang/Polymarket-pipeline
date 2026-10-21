# Feature Research

**Researched:** 2026-10-21
**Scope:** Portfolio hypotheses derived from PROJECT.md; no customer validation.

## Expected capabilities

| Category | Proposed v1 capability | Complexity | Dependency |
|---|---|---|---|
| Metadata batch | Daily AI-tag discovery into Lakehouse, raw snapshots and published complete versions | Medium | Pagination, scheduled bounded ingestion and metadata validation |
| Market selection | AI-model eligibility from Lakehouse metadata; nested status validation; versioned market/outcome/asset mapping for WebSocket | Low–medium | Successful metadata batch and a live sample |
| Capture | Finite manual sessions, raw journal, application heartbeat, bounded reconnect/retry | Medium | Valid watchlist; sink readiness |
| Trust | Separate quotes/trade observations, invalid-record quarantine, capture IDs, coverage and gap indicators | Medium | Raw envelope and session manifest |
| Top movers | Rank outcome-level midpoint changes in percentage points across selected windows | Medium | Valid fresh two-sided quotes and sufficient covered history |
| Activity | Observed trade-message frequency, observed size/notional where available, and spread over time | Medium | Trade fields validated; do not label as complete market turnover |
| Shocks | Configurable threshold rule with timestamp, before/after context and coverage flags | Medium | Agreed metric, threshold, window and sparse-data behavior |
| Live view | Selected outcomes, movers, activity, shocks and source/pipeline freshness | Medium | Query layer and explicit refresh target |
| History | Session/day views in Eventhouse/KQL, selected by user; coverage and quality counts | Low–medium | Retained raw observations; session boundaries |
| Portfolio | Architecture rationale, KPI definitions, reproducible runbook, fixtures, evidence and demo | Medium | At least one verified end-to-end session |

Daily Lakehouse metadata discovery and Eventhouse/KQL history were selected by the user on 2026-10-21. Other categories remain proposed for scoping. Complexity estimates are qualitative judgments for this project.

## KPI proposals for discussion

- **Price basis:** `(best_bid + best_ask) / 2` only with valid two-sided quotes. Keep last observed trade price separately. A quote-derived price is a market proxy, not a calibrated forecast.
- **Movement:** `100 * (midpoint_now - midpoint_baseline)` percentage points; show direction and rank by absolute movement. Candidate windows remain 5/15/60 minutes. Mark warming up when the session lacks a baseline; never fill across a disconnect silently.
- **Activity:** report observed trade messages per covered minute, observed shares and `sum(price * size)` when size exists. Missing size is unknown, not zero. Market-level totals across outcomes require a proven counting convention; default to outcome-level measures.
- **Spread:** `100 * (ask - bid)` percentage points with missing/crossed/stale state visible. Use maximum bid and minimum ask rather than trusting array order.
- **Shock:** simple rule rather than ML; candidate discussion value is at least 5 percentage points within 5 minutes. This is a tunable portfolio hypothesis, not a validated anomaly threshold. Suppress or mark signals with inadequate coverage; post-shock context may remain pending at session end.
- **Freshness:** distinguish source event age, collector receive age, sink lag and dashboard refresh time. A quiet market can have old quotes with a healthy connection.

## Optional differentiators

| Capability | Benefit | Proposed disposition |
|---|---|---|
| Replay of captured sessions with stable IDs | Demonstrates recovery and reproducible analytical results | Recommend v1 scope |
| Full order-book depth reconstruction | Richer liquidity analysis | Defer; top-of-book answers agreed questions with less state |
| Spark bronze/silver/gold analytical history | Additional transformation path beyond metadata ingestion | Deferred; metadata Lakehouse is mandatory independently |
| Power BI historical report | Familiar interviewer-facing artifact | Deferred; user selected KQL history |
| External news enrichment / ML anomaly detection | More context | Defer; not needed for observational core |

## Anti-features / exclusions

Automated trading, investment recommendations, 24/7 operations, all-topic discovery, invented pre-capture history and claims of complete exchange volume are excluded from the proposal. Market data can demonstrate observed movement; it does not establish why prices changed or whether activity is economically genuine.

## Decisions still needed

1. Accept or adjust v1 capability groups and market-selection policy.
2. Choose windows, shock rule, finite session duration and a measurable live freshness target.
3. Choose daily metadata run time, freshness/failed-run policy, collector access mechanism and whether watchlists can change mid-session. Eventhouse/KQL history and Lakehouse metadata are already selected.
4. Confirm how unavailable/partial metrics should appear; propose unknown/warming-up/gap rather than synthetic zero.

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| PROJECT | Approved project context | ../PROJECT.md | Full local file read |
| SOURCE | Source assessment and measured sample | SOURCE-ASSESSMENT.md | Research synthesized in this session |
| P-PRICES | Prices and Order Books | https://docs.polymarket.com/market-data/prices-order-books | Official markdown fetched; book semantics inspected |
| W-QUALITY | Data-Quality Testing | ../../../wiki/concepts/data-quality-testing.md | Full canonical wiki page read |
