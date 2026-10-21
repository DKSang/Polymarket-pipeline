# Source Assessment

**Project:** Polymarket AI Markets Stream Pipeline
**Researched:** 2026-10-21 (Asia/Saigon)
**Status:** Research evidence; not an approved data contract.

## Evidence boundaries

- **Stated:** Current official documentation describes discovery, public market streaming, and Fabric capabilities. See source table.
- **Measured:** The bounded, unauthenticated HTTP requests below succeeded in this research session.
- **Synthesis:** Proposed collector and KPI behavior follows from those facts and the portfolio goals.
- **Open:** No WebSocket capture, Fabric login, deployment, end-to-end latency, capacity measurement, or source delivery guarantee has been verified.

## Source inventory

| Source | Change semantics | Identity and ordering | Retention / replay | Volume and lateness | Implication |
|---|---|---|---|---|---|
| Gamma events, markets, tags | Mutable metadata queried through public REST | Persist event ID, market ID, condition ID, asset ID and outcome mapping; record fetch time and watchlist version | Historical metadata versions not established | Bounded HTTP samples only; rate limits not measured | Snapshot selected metadata at each session start; paginate with documented cursor; do not treat event status as market status |
| Public market WebSocket | Mixed book state, price-level changes, top-of-book updates and trade observations | Asset ID identifies outcome; timestamps are not a unique sequence; no universal event ID established | No market-feed cursor replay contract found in the inspected page; not proof that no other history API exists | Message rate, lateness, duplicates and reconnect behavior unmeasured | Preserve raw frames; keep connection epoch and receive sequence; mark disconnect intervals; reacquire book state after reconnect |
| Local collector journal (proposed) | Append-only received frames and session control events | Stable capture ID assigned once; replay retains that ID | Replay only captured records; durability limited by flush/fsync policy | Disk usage and throughput to be measured | Provides recovery from sink outage; cannot recover events never received |

Public market data is sufficient for the proposed observation use case. Wallets, authenticated user streams and order placement are outside scope. Do not add trader identities from other APIs merely to count observed trades.

## Bounded public API checks

Executed with PowerShell `Invoke-RestMethod`; no credentials used:

```powershell
Invoke-RestMethod 'https://gamma-api.polymarket.com/tags/slug/ai'
Invoke-RestMethod 'https://gamma-api.polymarket.com/events/keyset?tag_id=439&closed=false&limit=5'
Invoke-RestMethod 'https://gamma-api.polymarket.com/events/keyset?tag_id=439&closed=false&limit=100'
```

Results:

- Tag response: `id=439`, `label=AI`, `slug=ai`.
- The 100-event response exposes `$schema`, `events`, `next_cursor`. Only one page was inspected; this is not a full inventory.
- Candidate regex: `best.*model|model.*(best|release)|GPT-[0-9]|Claude [0-9]|Gemini [0-9]|DeepSeek|Grok [0-9]`.
- Across the 100 events, 108 nested markets matched this loose regex **before** applying nested-market status filters. This is neither 108 eligible markets nor a trading activity measurement.
- Example candidate `1370613`: DeepSeek / Chatbot Arena model question; `active=true`, `closed=false`, `enableOrderBook=true`.
- Example candidate `2414225`: Gemini release question; `active=true`, **`closed=true`** despite the enclosing event request using `closed=false`.
- `outcomes` and `clobTokenIds` in these sampled markets were JSON-encoded strings. Parse and validate matching lengths instead of assuming JSON arrays.
- The five-event sample included hardware, employment and IPO questions. The AI tag is broader than the agreed AI-model scope.

**Synthesis:** Discover candidates using tags plus model-related terms; manually review an allowlist, check each nested market's status/order-book availability, and freeze a versioned watchlist per session. Token IDs from a prior session are not assumed valid forever. Protocol V2 identifiers require validation against current market metadata.

## Stream contract findings

The official real-time page contains SDK-normalized events and native API frames. For a native collector, use the API section: `event_type`, `asset_id`, `assets_ids`, snake_case fields and source timestamp strings. Do not mix SDK field names into the wire parser. The native market endpoint is `wss://ws-subscriptions-clob.polymarket.com/ws/market`; its application heartbeat is text `PING` every 10 seconds with `PONG` responses. Extra top-of-book/lifecycle messages require `custom_feature_enabled`. [P-STREAM]

Book updates and trade observations have different meanings. An order-book hash is not a trade ID. A transaction hash can relate to multiple observations; it is not established as a unique row key. Preserve distinct observations even when price, size and timestamp match. A stable collector capture ID prevents double counting **the same captured message** during sink retries; it does not prove source-level trade deduplication. [Synthesis]

## Minimal envelope proposal

`schema_version`, `session_id`, `connection_epoch`, `receive_sequence`, `capture_id`, `received_at_utc`, `source_event_type`, `source_timestamp_raw`, `payload_raw`, `watchlist_version`.

Store raw content before flattening. If one frame contains multiple events, derive child IDs from capture ID plus array index. Maintain a separate session manifest with start/stop reason, requested duration, watchlist, reconnects, gaps, source/sink errors and capture/ack counts. This is a proposal for requirements, not a finalized schema.

## Gaps for phase planning

1. Capture a bounded live session for an eligible allowlist; measure event types, bytes, timestamp units/nulls, optional trade sizes and source-to-receive lag.
2. Inject disconnect and restart; verify new book baseline and visible unknown intervals. Never claim a snapshot recovers missed trades.
3. Inject duplicate sink delivery and replay; distinguish transport idempotency from source duplicate uncertainty.
4. Verify source limits using the current API reference before implementing pagination/retry budgets. No numeric rate limits are assumed here.
5. Verify workspace/capacity/permissions read-only before requesting cloud implementation approval.

## Nguồn tham khảo

| ID | Title | URL | Reading level |
|---|---|---|---|
| P-DISCOVERY | Discover Markets | https://docs.polymarket.com/market-data/discover-markets | Full official markdown fetched; endpoint excerpts inspected |
| P-STREAM | Real-Time Data | https://docs.polymarket.com/market-data/realtime-data | Official HTML and native API excerpts inspected |
| P-SAMPLE | Public AI events sample | https://gamma-api.polymarket.com/events/keyset?tag_id=439&closed=false&limit=100 | Live bounded response inspected, 2026-10-21 |
| P-TAG | Public AI tag | https://gamma-api.polymarket.com/tags/slug/ai | Live response inspected, 2026-10-21 |
| W-STREAM | Message and Stream Ingestion | ../../../wiki/frameworks/message-and-stream-ingestion.md | Full canonical wiki page read |
| W-TIME | Types of Time | ../../../wiki/frameworks/types-of-time.md | Full canonical wiki page read |
| W-REPLAY | Replay | ../../../wiki/concepts/Replay.md | Full canonical wiki page read |
