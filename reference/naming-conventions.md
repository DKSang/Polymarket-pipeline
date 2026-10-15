# Naming conventions

Technical names use English. These patterns apply only to item types
chosen later; examples are not an approved architecture or item inventory.

## Items

| Item type | Pattern | Example |
| --- | --- | --- |
| Workspace | <Project>-<STAGE> | Polymarket-DEV |
| Lakehouse | lh_<purpose> | lh_market_data |
| Warehouse | wh_<purpose> | wh_analytics |
| Notebook | nb_<verb>_<object> | nb_validate_quotes |
| Data pipeline | pl_<verb>_<object> | pl_refresh_market_metadata |
| Dataflow Gen2 | df_<verb>_<object> | df_clean_market_metadata |
| Semantic model | sm_<subject> | sm_market_intelligence |
| Report | rpt_<subject> | rpt_market_overview |
| Variable library | vl_<scope> | vl_polymarket |

- Do not repeat polymarket in every item when the workspace supplies context.
- Do not add _dev to item names; the workspace identifies the environment.
- Do not use final, new or test2 as substitutes for a purpose.
- Agree a temporary-experiment naming rule when one is actually needed.

## Tables, columns and parameters

| Component | Pattern | Example |
| --- | --- | --- |
| Project-managed table | snake_case, plural noun | quote_observations |
| Project-created column | snake_case, explicit meaning | observed_at_utc |
| Pipeline-added technical metadata | leading underscore | _ingested_at_utc |
| Notebook or pipeline parameter | p_<name> | p_start_date |

These rules do not authorize renaming fields in original source payloads.
Metadata examples do not mandate adding those columns.
Schemas, layers, partitions and loading patterns remain undecided.

## Pipeline activities

Use <Verb> <object> with an initial capital:
Validate quotes; Run nb_validate_quotes.
