# Copilot token-efficiency kit

Paste each file into the work repo at the path below.

| File here | Put it at |
|---|---|
| instructions/project.instructions.md | .github/instructions/project.instructions.md |
| instructions/terraform.instructions.md | .github/instructions/terraform.instructions.md |
| prompts/handoff.prompt.md | .github/prompts/handoff.prompt.md (type /handoff in chat) |
| vscode-settings-terraform.jsonc | merge into .vscode/settings.json |

## Notes
- If the repo already has .github/copilot-instructions.md, merge the project rules into it instead of adding project.instructions.md. Both load on every request, so duplicated rules cost tokens twice.
- Fill the [bracketed] lines in project.instructions.md once.
- Run /handoff before leaving a heavy session. Save the output under handoffs/ in this repo and paste it at the top of the new session.
