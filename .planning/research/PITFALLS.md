# Pitfalls Research

**Domain:** Polymarket quotes, finite observation sessions and Fabric analytics
**Researched:** 2026-10-21
**Confidence:** HIGH for documented risks; MEDIUM for their workload impact

## Critical Pitfalls

| Pitfall | Warning sign | Prevention / verification | Proposed stage |
|---------|--------------|---------------------------|----------------|
| Incomplete candidate snapshot presented as complete | Page cap or cursor failure stops extraction | Record page/cursor/request scope and successful termination; test partial publication | Source assessment and capture |
| AI tag interpreted as direct-AI eligibility | Indirect company/chip events occupy watchlist slots | Explicit classification policy, examples and selection evidence before automatic replacement | Watchlist contract |
| Freshness target replaced by report-refresh setting | Refresh works but displayed receipt time lags | Measure receipt-to-visible result across the complete path; distinguish quiet-source states | Serving and measured verification |
| Raw fields silently dropped by landing table | Mixed event types fit only the initial schema | Preserve original payload and verify readback; inspect projection/conversion failures | Capture and ingestion |
| Raw wire and SDK events parsed interchangeably | Missing event type/asset/time after SDK upgrade | Pin protocol/version, use an explicit adapter and captured fixtures | Source adapter |
| Quote-change count becomes message count | Duplicate BBA and price-change events inflate activity | Count changes in the bid/ask pair; define duplicate/ordering handling | Quote reconstruction |
| Reconnect snapshot counted as a market movement | Activity jumps after disconnection | Record continuity boundaries, restart trusted state and exclude snapshot initialization from transitions | Recovery and KPI |
| Historical chart hides manual-session gaps | Continuous line crosses unobserved hours | Draw breaks and show per-market/outcome observed intervals | History/reporting |
| Ingestion detection leaves quiet-market windows stale | No new row triggers no visual update while T-W advances | Test time-driven recalculation/status behavior and display refresh fallback | Serving |
| First events arrive before routing is ready | Source has events absent from sink | Verify sink readiness/start boundaries; official Lakehouse destination docs describe this race | Capture lifecycle |
| Trial expiry loses portfolio reproducibility | Definitions or evidence remain only in Fabric | Save native definitions, code and approved evidence before expiry; verify the actual expiry separately | Delivery |
| Runtime lifecycle or SDK dependency mismatch | Search says GA but full page says EOSA; imports conflict | Prefer full version-scoped docs, pin/test environment separately; no silent runtime change | Feasibility |

## Technical Debt Patterns

| Shortcut | Consequence | Acceptability |
|----------|-------------|---------------|
| One short probe as proof of reliable collection | No evidence for sustained windows, recovery or freshness | Useful only as a scoped short test |
| Separate live/batch KPI logic without shared fixtures | Same market/window gets different answers | Require equivalence verification before acceptance |
| Null volume silently treated as zero | Candidate priority acquires an unapproved business meaning | Define policy with user first |
| Replacing every market with today's top volume | Violates retain-members-and-fill-vacancies rule | Not acceptable under current scope |
| Carrying a quote through a connection gap without flag | Missing observations look like no movement | Not acceptable |

## Integration Gotchas

- Gamma keyset and offset endpoints have different pagination contracts. Schema presence of a field is not proof it is a valid sort expression.
- Snapshot/public stream semantics are not equivalent to an append-only trade ledger. Do not substitute last trade or sampled history for missing quote states.
- Eventstream custom endpoint documentation supports producer protocols; it does not remove the external subscription/adapter responsibility.
- A workspace setting and a historical job's actual runtime are different evidence classes.

## Security and Resource Scope

Read-only inspection authorization is not deployment authorization. Keep credential values and real endpoint keys out of Git, reports and Wiki; source documentation examples are not live credentials. Do not query GYM items as project validation or inherit ownership from their names. Existing resources must be individually assessed before reuse.

## Looks Done But Is Not

- A Completed pipeline lacks independently checked raw contents, page completion and selected-market coverage.
- A valid table schema lacks original-payload fidelity, deduplication and a publication contract.
- A chart refresh lacks measured receipt-to-visible latency and a true data-as-of timestamp.
- A few-minute run lacks observed 60-minute anchors; fixture and live evidence must be labeled separately.
- A saved raw file lacks demonstrated downstream replay correctness.

## Recovery Strategies

Preserve already published raw, mark failed/incomplete extracts explicitly, reconnect without pretending gaps were filled, and recompute derived artifacts from known input units. Retries must not duplicate derived transitions. Exact retry count/backoff and failure presentation remain requirements to agree; no unrequested RPO/RTO or perfect-delivery guarantee is invented.

## Sources

- https://docs.polymarket.com/api-reference/markets/list-markets-keyset-pagination — page contract.
- https://docs.polymarket.com/api-reference/wss/market — quote/snapshot distinction.
- https://learn.microsoft.com/en-us/fabric/real-time-intelligence/event-streams/add-destination-lakehouse — field loss and routing start caveat.
- https://learn.microsoft.com/en-us/fabric/real-time-intelligence/dashboard-live-refresh — quiet-source refresh implications are an inference to test.
- https://learn.microsoft.com/en-us/fabric/fundamentals/fabric-trial — trial lifecycle.
- https://learn.microsoft.com/en-us/fabric/data-engineering/runtime-1-3 — lifecycle status from full page.

## Knowledge Sources

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/event-stream-processing/gotchas.md` — window membership, timestamps and limits of educational demos.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-source-assessment/gotchas.md` — completeness and recovery evidence gaps.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/data-storage/gotchas.md` — raw and retention/recovery contracts.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/eventstream-ingestion-boundaries.md` — actual documented projection and routing risks.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/runtime-compatibility.md` — full-page versus search evidence.
