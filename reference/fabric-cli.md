# Fabric CLI notes

- Installed version observed during setup: 1.7.0.
- Agreed authentication method: existing service principal.
- Successful fab authentication is not yet verified.
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
- Server connection, effective tool filtering and Azure CLI credential
  selection have not yet been verified.
