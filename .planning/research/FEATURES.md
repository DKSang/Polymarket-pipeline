# Feature Research

**Researched:** 2026-10-21
**Scope:** Portfolio hypotheses derived from PROJECT.md; no customer validation.

## Expected capabilities

| Category | Proposed v1 capability | Complexity | Dependency |
|---|---|---|---|
| Metadata batch | AI-tag discovery into Lakehouse starting daily at 07:00 Asia/Saigon (00:00 UTC), raw snapshots and published complete versions | Medium | Pagination, scheduled bounded ingestion and metadata validation |
| Market selection | AI-model eligibility from Lakehouse metadata; nested status validation; versioned market/outcome/asset mapping for WebSocket | Low–medium | Successful metadata batch and a live sample |
| Capture | User-supplied finite duration per manual session, automatic stop receiving at expiry, controlled shutdown, raw journal, heartbeat, bounded reconnect/retry | Medium | Valid watchlist; sink readiness; validated duration parameter |
| Trust | Separate quotes/trade observations, invalid-record quarantine, capture IDs, coverage and gap indicators | Medium | Raw envelope and session manifest |
| Top movers | Rank outcome-level midpoint changes in percentage points over approved 5/15/60-minute windows; show insufficient-history state | Medium | Valid fresh two-sided quotes and sufficient covered history |
| Activity | Observed trade-message frequency, observed size/notional where available, and spread over time | Medium | Trade fields validated; do not label as complete market turnover |
| Shocks | Approved initial rule: absolute midpoint change >=5 percentage points in 5 minutes; configurable threshold/window; timestamp and reaction context | Medium | Valid two-sided quotes, adequate history and coverage flags |
| Live view | Selected outcomes, movers, activity, shocks and freshness; user-selected 15–30 seconds from collector receive to visible dashboard update | Medium | Query/ingestion/refresh budget and end-to-end measurement against the 30-second upper target |
| History | Session/day views in Eventhouse/KQL, selected by user; coverage and quality counts | Low–medium | Retained raw observations; session boundaries |
| Portfolio | Architecture rationale, KPI definitions, reproducible runbook, fixtures, evidence and demo | Medium | At least one verified end-to-end session |

Daily Lakehouse metadata discovery and Eventhouse/KQL history were selected by the user on 2026-10-21. Other categories remain proposed for scoping. Complexity estimates are qualitative judgments for this project.

## KPI proposals for discussion

- **Price basis:** `(best_bid + best_ask) / 2` only with valid two-sided quotes. Keep last observed trade price separately. A quote-derived price is a market proxy, not a calibrated forecast.
- **Movement:** `100 * (midpoint_now - midpoint_baseline)` percentage points; show direction and rank by absolute movement. User approved 5/15/60-minute windows and insufficient-history states. Never fill across a disconnect silently.
- **Activity:** report observed trade messages per covered minute, observed shares and `sum(price * size)` when size exists. Missing size is unknown, not zero. Market-level totals across outcomes require a proven counting convention; default to outcome-level measures.
- **Spread:** `100 * (ask - bid)` percentage points with missing/crossed/stale state visible. Use maximum bid and minimum ask rather than trusting array order.
- **Shock:** user approved a simple initial rule, absolute midpoint change >=5 percentage points within 5 minutes, with threshold/window configurable. Includes both rises and falls (for example 40% → 45% or 40% → 35%). This is a portfolio definition, not a validated statistical anomaly threshold. Suppress or mark signals with inadequate coverage; post-shock context may remain pending at session end. Exact baseline sampling and repeated-alert grouping are phase-design details.
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
2. Windows (5/15/60 minutes) and the configurable shock rule (absolute midpoint change >=5 percentage points in 5 minutes) are approved. Session duration is supplied at launch, with a finite automatic stop. Live freshness is 15–30 seconds from collector receive to visible update; 30 seconds is the upper target. Measurement coverage/percentile and outage/recovery treatment remain open.
3. Failed-batch policy is approved: use last successful metadata, warn with its update time, and revalidate markets before subscription. Batch completion/freshness details, collector access and mid-session changes remain phase-design questions. Daily start is 07:00 Asia/Saigon (00:00 UTC); KQL history and metadata Lakehouse are selected.
4. Confirm how unavailable/partial metrics should appear; propose unknown/warming-up/gap rather than synthetic zero.

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| PROJECT | Approved project context | ../PROJECT.md | Full local file read |
| SOURCE | Source assessment and measured sample | SOURCE-ASSESSMENT.md | Research synthesized in this session |
| P-PRICES | Prices and Order Books | https://docs.polymarket.com/market-data/prices-order-books | Official markdown fetched; book semantics inspected |
| W-QUALITY | Data-Quality Testing | ../../../wiki/concepts/data-quality-testing.md | Full canonical wiki page read |
