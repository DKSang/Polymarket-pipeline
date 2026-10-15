# Fabric environment

## Identity

- Agent identity: service principal, authenticated in both Fabric CLI and Azure CLI.
- Tenant ID: `370fa88c-de52-4400-b6cd-027ef6a8ddf2`.
- Application (client) ID: `b593a632-afcd-4c1b-b0bd-43080c0317ab`.
- Principal (object) ID reported by Fabric CLI: `a93074e9-54c0-4ced-b846-143d8a51162c`.
- Fabric CLI and Azure CLI tenant and application IDs were verified to match.
- Fabric API, Fabric MCP CLI and live Fabric MCP protocol workspace listing
  succeeded, returning only Polymarket-DEV with the same workspace object ID.
  Live MCP protocol access was verified on 2026-10-15; enumeration is not a
  complete audit of effective permissions.
- Workspace role and write permission have not been verified. If needed, confirm
  the role in the workspace Manage access pane; never test it by writing.
- Use the same principal and tenant for fab and Fabric MCP.
- Never store authentication secrets here.

## Scope

| Workspace | ID | Stage | Agent may write? |
| --- | --- | --- | --- |
| Polymarket-DEV | `89c341b0-55ba-4b20-ac0f-18de88049aad` | DEV | Only after dry run where supported and explicit confirmation for each operation |

Every other workspace is read-only, and may be read only when needed
for the requested task and permitted by the signed-in identity.

Scope approval is not authorization to create a missing workspace,
design ingestion, deploy items or run workloads.
