# Fabric CLI notes

- Verified Fabric CLI version: 1.7.0.
- Verified sign-in method: service principal authentication through fab auth login.
- Logged-in status and identity match with Azure CLI verified; identity IDs are
  recorded in environment.md.
- Read-only Fabric API listing, workspace existence and workspace ID checks passed.
- Docs: https://aka.ms/fabric-cli
- Check fab <command> --help before first use in a session.
- Follow AGENTS.md guardrails; examples are not execution approval.

## Paths

- Workspace: "Polymarket-DEV.Workspace"
- Item: "<Workspace>.Workspace/<Item>.<Type>"
- Quote every Fabric path.

## Read-only checks

| Task | Command |
| --- | --- |
| Version | fab --version |
| Visible workspaces | fab ls |
| Workspace visibility | fab exists "Polymarket-DEV.Workspace" |
| Workspace ID | fab get "Polymarket-DEV.Workspace" -q id |
| Workspace items | fab ls "Polymarket-DEV.Workspace" -l |

Visibility does not prove write permission.
Do not test access by creating, changing or deleting an item.

## Authentication

The user runs fab auth login in their own Windows terminal and selects
service principal authentication using the agreed principal and tenant.
Never put a secret in chat, a project file or a command-line argument.

Fabric CLI and Azure CLI keep separate sign-ins.
Preserve the existing Azure CLI sign-in unless a mismatch is established
and the user approves changing it.

When inspecting auth status, suppress any token fields from displayed output.

## Writes

Show exact commands and effects, then obtain confirmation for each write.
Use a dry run when supported. Use force only for an approved exact operation.
Never use fab rm --hard.

## MCP package checks

- Fabric MCP package version observed: 1.5.0.
- Package help and server start help both exited successfully.
- Server help confirms `--mode all` and `--read-only`; the latter is
  documented as disallowing write operations.
- CLI docs list-item-types succeeded, including lakehouse and notebook.
- CLI onelake list-workspaces succeeded with
  `AZURE_TOKEN_CREDENTIALS=AzureCliCredential`, returning Polymarket-DEV
  with the same workspace object ID as Fabric CLI.
- Azure CLI obtained Fabric credentials successfully; only expiry was displayed.
- MCP protocol verification passed on 2026-10-15: Microsoft Learn search returned
  official documentation; Fabric MCP docs listed lakehouse and notebook; live
  OneLake workspace listing returned the agreed workspace object ID.
- The exposed fabric-mcp tool list contained no create, upload or delete tools;
  project configuration retains --read-only and AzureCliCredential.
- A separate claude mcp list invocation still reported Pending approval for the
  project servers, despite successful calls in this VS Code session. That CLI
  launch needs its own approval; the current session's MCP calls are verified.
- Optional remote MCP checks: FabricIQ discovery succeeded with an empty result;
  SQL endpoint connected, but no target-item query was tested; hosted Power BI
  modeling failed with HTTP 403 FeatureNotAvailable. Its cause is unverified;
  do not change credentials, tenant settings or registrations without approval.
