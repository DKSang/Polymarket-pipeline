# Fabric environment

## Identity

- Agreed identity: the existing Azure CLI service principal.
- Use the same principal and tenant for fab and Fabric MCP.
- Fabric authentication and workspace roles have not yet been verified.
- Never store authentication secrets here.

## Scope

| Workspace | Stage | Agent may write? |
| --- | --- | --- |
| Polymarket-DEV (existence unverified) | DEV | Only after dry run where supported and explicit confirmation for each operation |

Every other workspace is read-only, and may be read only when needed
for the requested task and permitted by the signed-in identity.

Scope approval is not authorization to create a missing workspace,
design ingestion, deploy items or run workloads.
