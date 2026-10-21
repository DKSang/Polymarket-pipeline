# Pitfalls Research

**Researched:** 2026-10-21
**Status:** Source-grounded risks and proposed verification, not measured failures in this project.

| Pitfall | Warning sign | Prevention / recovery | Address when |
|---|---|---|---|
| AI tag treated as AI-model allowlist | Hardware/IPO questions included; closed nested markets selected | Review criteria, filter individual market status, version allowlist and metadata; retain exclusions | Source feasibility |
| Partial daily batch replaces valid metadata | Pagination failure makes markets disappear from subscriptions | Publish completed versions only; retain last complete snapshot and mark failed/stale status | Metadata batch |
| Market ID passed to WebSocket | Subscription has no expected observations | Persist market/outcome/asset mapping; send outcome asset IDs and retain metadata version per session | Metadata-to-stream integration |
| Daily discovery assumed current after failures | Collector uses old IDs without warning | Define freshness threshold and explicit stop/degraded behavior; monitor last successful batch | Requirements and operations |
| SDK and wire schema mixed | `assetId` parser receives `asset_id`, source epoch strings interpreted as SDK datetimes | Pin native protocol boundary; fixtures for arrays, unknown types, optional fields and timestamp units | Source feasibility |
| Book array order assumed | Spread changes sign or best price is wrong | Use max bid/min ask; validate crossed/empty books before serving | Data correctness |
| Quote changes counted as trades | Activity rises on order cancels alone | Separate event families and outcome-level observed metrics | KPI implementation |
| Timestamp/hash dedup removes real events | Identical-value messages disappear | Use stable capture IDs for retry delivery; do not infer source uniqueness from sample rows | Data correctness |
| Reconnect appears gap-free | Flat chart continues through disconnect | Record gap; obtain fresh baseline; suppress/flag gap-crossing deltas; no invented missed trades | Recovery |
| Unknown size becomes zero | Trade count rises while zero volume is reported as complete | Track missing-size count; label volume partial/unknown | KPI implementation |
| Outcome observations summed as complete market volume | Double counting across complementary outcomes | Default outcome-level views; prove aggregation convention before market totals | KPI implementation |
| First event defines a lossy Lakehouse schema | Some event fields disappear after ingestion | Stable envelope with original payload; quarantine parse errors; inspect persisted payload round trip | Fabric spike |
| Initial events lost during route activation | Source capture count exceeds durable rows without an explanation | Verify sink route readiness before collector; stable IDs, journal replay, startup reconciliation | Fabric spike |
| OneLake availability used as low-latency live sink | Sparse session does not appear in Delta promptly | Keep live queries on Eventhouse; measure lake write lag separately; wait/drain or export through a verified path | History |
| Live-refresh timestamp mistaken for fresh source | Dashboard refreshed but last event predates a disconnect | Separate capture/source/sink/query times; configured refresh for time-dependent health | Serving |
| Session stop assumed to stop all compute | Capacity activity continues after Python exits | Document resource-specific stop behavior; inspect post-stop consumption; no automatic capacity changes | Operations |
| Short sessions used to promise 60-minute movers | Missing baseline gets backfilled by a fabricated value | Show warming-up; choose session duration consistent with windows; label history coverage | Requirements |
| Trial expiry destroys portfolio evidence | Working demo is the only retained artifact | Save code, metadata/queries, representative non-secret captures and screenshots locally before expiry | Portfolio delivery |

## Looks done but isn't

- [ ] A live source session has been captured for an eligible AI-model watchlist; documentation alone is not evidence of streaming success.
- [ ] A sink outage and replay of the same capture do not increase serving counts.
- [ ] Distinct identical-content observations remain distinct unless the source proves uniqueness.
- [ ] Reconnection creates an explicit unknown interval and a new baseline.
- [ ] Malformed/late/unknown records have observable disposition and cannot silently contaminate published KPIs.
- [ ] Short sessions, inactive markets, missing size and incomplete post-shock windows produce honest states.
- [ ] Source/journal/sink/serving counts reconcile under defined filters; counts need not be identical across different grains.
- [ ] Live and historical views use the same definitions and can be reproduced from retained observations.
- [ ] An approved cloud run has measured latency and capacity, including post-stop activity.

## Recovery principles

Captured records can be replayed with their original IDs; uncaptured source intervals remain unknown. A fresh snapshot repairs current book state but not missed trade history. A parse fix should replay raw records into an isolated result and compare KPI fixtures before replacement. Resume from Fabric retention only within the verified retention window; keep it separate from Polymarket replay claims.

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| SOURCE | Source findings and live sample | SOURCE-ASSESSMENT.md | Session research evidence |
| ARCH | Architecture alternatives and platform conditions | ARCHITECTURE.md | Session synthesis with linked official evidence |
| M-LAKE | Lakehouse destination schema/startup behavior | https://learn.microsoft.com/fabric/real-time-intelligence/event-streams/add-destination-lakehouse | Full Learn fetch read |
| M-TRIAL | When a Fabric trial ends | https://learn.microsoft.com/fabric/fundamentals/fabric-trial#when-your-fabric-trial-ends | Microsoft Learn search excerpt inspected |
| W-QUALITY | Data-Quality Testing | ../../../wiki/concepts/data-quality-testing.md | Full canonical wiki page read |
