# Research Sources and Retrieval Evidence

**Collected:** 2026-10-21

Publisher dates are Unknown unless independently present and verified. SHA-256 identifies the exact local raw artifact including its retrieval metadata; it is not a publisher signature. Raw content was retrieved from official sources, not generated from summaries.

## Official Source Registry

| Source | Retrieved URL | Local raw artifact | SHA-256 |
|---|---|---|---|
| polymarket-markets | https://docs.polymarket.com/api-reference/markets/list-markets.md | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/polymarket/polymarket-markets.md` | `871349b870adcac4a2276b8c003bcb4aec5ce293a9b1a971dcc97fdcedf32157` |
| polymarket-keyset | https://docs.polymarket.com/api-reference/markets/list-markets-keyset-pagination.md | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/polymarket/polymarket-keyset.md` | `26e6b2ec027d7700c6787a986abea7acbeb8ad51041e93cf704381f7700e8432` |
| polymarket-market-channel | https://docs.polymarket.com/api-reference/wss/market.md | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/polymarket/polymarket-market-channel.md` | `f3a95bbe0f2e7c3d8d58bce8304a991601b5480638e40521e0a9a8045f1a93e2` |
| polymarket-realtime | https://docs.polymarket.com/market-data/realtime-data.md | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/polymarket/polymarket-realtime.md` | `865ad57b569df92b77dfbd44962c7ecbe6b34886ddf90ec12098996d5615be86` |
| polymarket-sdk-readme | https://raw.githubusercontent.com/Polymarket/py-sdk/main/README.md | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/polymarket/polymarket-sdk-readme.md` | `36ea6e3dbda43eed1c7a10595613685dc6a595ec7b77cf6fecf7625d9f9c1635` |
| polymarket-sdk-pyproject | https://raw.githubusercontent.com/Polymarket/py-sdk/main/pyproject.toml | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/polymarket/polymarket-sdk-pyproject.md` | `1b92cce3903d9873e2ab37244b64b4d7a09604bb33b9fbb016c409e0f339c3d9` |
| microsoft-fabric-trial-capacity | https://learn.microsoft.com/en-us/fabric/fundamentals/fabric-trial | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/microsoft-fabric/microsoft-fabric-trial-capacity.md` | `a57286a5a6182aaffb1cf2ba6e573707a3195ef2c0871e48c82b0f0910ed01f9` |
| power-bi-automatic-page-refresh | https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-automatic-page-refresh | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/microsoft-fabric/power-bi-automatic-page-refresh.md` | `4e2ddd9df76d76736d009c6ef083effad92d5e6ad578a814c0ba4eba1cc47776` |
| fabric-real-time-dashboard-live-refresh | https://learn.microsoft.com/en-us/fabric/real-time-intelligence/dashboard-live-refresh | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/microsoft-fabric/fabric-real-time-dashboard-live-refresh.md` | `c4bd04f41fca07c8cf5529d7e20f5fd559e99394efd57c324877a44d1909ca1c` |
| fabric-eventstream-custom-endpoint-source | https://learn.microsoft.com/en-us/fabric/real-time-intelligence/event-streams/add-source-custom-app | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/microsoft-fabric/fabric-eventstream-custom-endpoint-source.md` | `3ac5c1bfe4a5b954088648b70b3e422604c79267e574da27a815fc169f4b53e2` |
| fabric-eventstream-lakehouse-destination | https://learn.microsoft.com/en-us/fabric/real-time-intelligence/event-streams/add-destination-lakehouse | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/microsoft-fabric/fabric-eventstream-lakehouse-destination.md` | `b3b48c60dc80891f118751c4d6937898365b102026881e3814325bc8cb3b4c3d` |
| fabric-runtime-1-3 | https://learn.microsoft.com/en-us/fabric/data-engineering/runtime-1-3 | `C:/Users/sanbo/Code/fabric-agent-knowledge/raw/microsoft-fabric/fabric-runtime-1-3.md` | `92eea770df734fe828c76b79a7526dcdb59e68f9baa014317629d89fbf9b3973` |

## Retrieval Conditions

- Microsoft sources: Microsoft Learn MCP search followed by full-page fetch. Full content is preferred over search snippets when they conflict.
- Polymarket sources: official documentation pages and their Markdown exports; official py-sdk main README and pyproject. Mutable main snapshots are not immutable release tags or registry-latest checks.
- Fabric resources: read-only Fabric MCP workspace/capacity metadata and OneLake directory listing, with earlier CLI inventory and historical job statuses described in PROJECT.md. No item-data query or cloud execution.
- Source/API and SDK schema claims must be rechecked at dependency selection or upgrade time. Local snapshots support provenance, not permanent truth.
- SDK runtime metadata and sample endpoints/keys in documentation are not real project secrets or production configuration. No credential values were captured from authenticated resources.

## Wiki Ingestion Verification

- Hub status also contains changes in Obsidian graph/workspace state. These are outside this task's edits and were left untouched; no blanket Hub staging, revert or commit was performed.
- User authorized scoped Hub ingestion on 2026-10-21, overriding the prior read-only rule for this operation. Existing raw sources, installed skills and GSD Core were not modified.
- Twelve raw source files compiled sequentially into seven articles. Index and ingest log were updated. No Hub commit was created.
- Scoped check_evidence.py returned no evidence errors. Its seven date suspects refer to the locally recorded collection date, present in raw retrieval headers; no publisher date is inferred.
- Two unreferenced raw files were already present and are outside this task: raw/Data Engineering on Microsoft Azure/channel-membership-announcement.md and raw/Data Engineering on Microsoft Azure/SUMMARY.md. They were not repaired or deleted.
- This static verification is not a claim that notebook jobs, SDK installation, market-data completeness, replay or latency have been tested.

## Knowledge Sources

- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/discovery.md` — compiled source-scoped guidance used in the research dimensions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/realtime-quotes.md` — compiled source-scoped guidance used in the research dimensions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/polymarket/python-sdk.md` — compiled source-scoped guidance used in the research dimensions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/trial-capacity.md` — compiled source-scoped guidance used in the research dimensions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/realtime-serving.md` — compiled source-scoped guidance used in the research dimensions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/eventstream-ingestion-boundaries.md` — compiled source-scoped guidance used in the research dimensions.
- `C:/Users/sanbo/Code/fabric-agent-knowledge/wiki/microsoft-fabric/runtime-compatibility.md` — compiled source-scoped guidance used in the research dimensions.
