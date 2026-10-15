<!-- fabric-engineering-skills:start -->
# Fabric brain

Canonical project instructions for Polymarket Market Intelligence.
Read this file at the start of each session.

## Start of session

- Read reference/environment.md and reference/naming-conventions.md
  before Fabric work; open other references only when relevant.
- Read the applicable project documents before proposing changes.
- If instructions or verified facts conflict, explain the conflict
  and ask before replacing an approved project constraint.

## Knowledge and stage boundaries

- Preserve the parent CLAUDE.md wiki-first policy.
- Vault root: ../
- Wiki index: ../wiki/index.md
- Maintenance convention: ../schema/wiki-maintenance.md
- For DE consultation, inspect project context, then the wiki index
  and relevant canonical pages before external research.
- Identify the unanswered or current-verification claim before
  external lookup. Cite inspected sources and preserve evidence labels.
- Wiki consultation is read-only unless maintenance is explicitly requested.
- docs/01-business-problem.md governs business requirements.
- docs/02-source-research-and-decisions.md records source review,
  not approval of ingestion, architecture or cloud implementation.
- Setup, naming approval and workspace scope do not authorize
  architecture selection, ingestion design, deployment or bulk collection.

## Guardrails

- Only Polymarket-DEV is eligible for creating or modifying Fabric items.
  Every other workspace is read-only; expanding scope needs separate approval.
- Before each Fabric write, show the exact command or definition change,
  target and expected effect. Use a dry run when supported.
- Obtain explicit confirmation for every Fabric write or destructive action.
  Plan approval is not blanket approval for later operations.
- Never delete, overwrite, move or change permissions without confirmation
  for that exact operation. Never use fab rm --hard.
- Use a force flag only for the exact operation already approved.
- Never print, log, commit or store secrets or access tokens in project files.
  Authentication secrets are entered by the user in their own terminal.
- Use the agreed service principal for fab and Azure CLI.
  Verify both the tenant and identity; report unexpected access.
- Fabric MCP must remain read-only. Fabric changes go through fab.
- Written scope and shell permissions are not substitutes for Fabric RBAC.

## Verify before claiming

After wiki consultation, verify current Fabric features, APIs, schemas
and limits against Microsoft Learn and cite the source.
Use microsoft-learn MCP when connected; otherwise state the fallback
source and what remains unverified.
Before editing an item definition, inspect Fabric MCP's schema tools
when available; never invent a schema or silently drop safety flags.

## Build, verify, test

Only when the user requests a build:
1. Load build-in-fabric when available and plan from approved references.
2. Get plan approval and confirmation for the specific Fabric writes.
3. Build only within the writable scope.
4. Verify item existence and definitions with read-only checks.
5. Offer an end-to-end test separately; obtain approval before running it.

## Keep the brain current

Use fabric-brain to record confirmed durable Fabric facts and corrections
in reference/, preserving its formats. Mark unverified environment facts.
Do not copy business contracts or source evidence into competing records.
Reference updates do not authorize wiki maintenance or new project stages.
Never record secrets; do not commit or push without being asked.

## Reference

- reference/environment.md: environment, identity and workspace scope
- reference/naming-conventions.md: agreed naming rules and examples
- reference/fabric-cli.md: CLI usage and authentication notes

Architecture patterns, lessons, glossary, decisions and workspace inventories
are added and indexed only when there is approved, substantive content.

## Folders and tools

- data/: development samples only; no production or personal data.
- fab: inspect and, after confirmation, change Fabric items.
  Execute agent-driven fab commands through Bash so the configured
  Bash permission rules apply; do not route through another tool to avoid them.
- microsoft-learn: current Microsoft documentation.
- fabric-mcp: API/schema documentation and read-only environment access.
- Keep existing plugins and unrelated configuration unchanged.
<!-- fabric-engineering-skills:end -->
