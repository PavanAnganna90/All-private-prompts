# Handoff: Copilot token efficiency (2026-10-06)

## Goal
- Cut GitHub Copilot token use in VS Code (company cap: 80k tokens/month) without losing working context.

## Key decisions
- MCP servers off by default. Their tool definitions ride along on every request, so turn them on only for a task that needs one.
- One task per session. Start a New Session when the task changes. Pinned sessions stay as reference: unopened ones cost zero tokens, continuing one resends its whole history.
- Move context out of chat logs into files: standing rules go in .github/instructions/, task state goes in a handoff summary.
- Instruction files hold rules, not tasks. Audit-log checks, gcloud work and Jira automation are asked for per session.
- Terraform boundary: we own our child modules only. Cross-team modules are read-only, and ?ref pins never change unless asked.
- Log queries get filtered at the source (time window, log filter, resource, row limit). Tool and terminal results count as input tokens even when nothing is pasted.

## Files (in this repo)
- copilot/instructions/project.instructions.md: repo-wide rules (terse, diffs only, named files only). Bracketed lines still to fill.
- copilot/instructions/terraform.instructions.md: Terraform-only rules, loads just for .tf/.tfvars. Rules duplicated with the project file removed.
- copilot/prompts/handoff.prompt.md (run as /handoff) and copilot/vscode-settings-terraform.jsonc (keeps .terraform, state, plans and lock file out of agent search).

## Open items
- Fill the bracketed lines in project.instructions.md: repo purpose, secret manager layout, naming.
- Confirm with the admin whether the 80k cap is tokens or premium requests/credits. It decides how much agent mode is affordable.
- Decide MCP on or off per task. Log checks run through gcloud in the terminal don't need MCP; a Cloud Logging MCP server does.

## Gotchas
- /compact is itself a model call, Agent mode costs several times Ask mode per turn, and the work laptop can't import files, so everything goes in by paste.
