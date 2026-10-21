# CDA separation agent space: setup

## 1. Build the folder

```
cda-separation/
  AGENTS.md              paste from this pack
  context/
    brief.md             paste from this pack
    templates/           reference onboarding docs (or just paste their Confluence links to the agent)
    meetings/            empty for now
    answers/             empty for now
    pages/               empty for now
  repos/
    cdwp/                clone of the CDWP repo
    cdw/                 clone of the CDW repo
```

- Templates: the easy way is to paste each Confluence link to the agent; it saves the page as `context/templates/<platform>-<tenant>-onboarding.md`. You can also paste page text into a file with that name yourself.
- Already have CDWP and CDW open as workspace roots? Skip `repos/` and use the kickoff prompt in `.github/prompts/cda-separation-kickoff.prompt.md` instead of the one below.
- Repos: clone fresh into `repos/`, or point at existing clones. On Windows, a junction needs no admin rights: `New-Item -ItemType Junction -Path repos\cdwp -Target C:\path\to\cdwp`.
- If you put `cda-separation` itself under git, add `repos/` to `.gitignore`.

## 2. Connect your agent

- **Copilot (VS Code, agent mode):** open the `cda-separation` folder. Recent VS Code builds read `AGENTS.md` at the root automatically. If yours does not, copy the same text into `.github/copilot-instructions.md`.
- **Confluence MCP:** in Copilot Chat's tools picker, make sure your Confluence MCP server's tools are switched on for agent mode.
- **Claude Code:** create `CLAUDE.md` containing one line: `@AGENTS.md`, then start it in `cda-separation`.

## 3. Kickoff prompt

```
Read AGENTS.md and context/brief.md. Run init.
Ingest these Confluence links: <links, or delete this line>
Then run discover paml. Stop after it and give me a 10-line summary plus the new open questions.
Do not start discover cda until I say go.
```

Need the questionnaire before discovery is done (for example for the CDA meeting)? Send `init` then `draft questionnaire`. It fills what it can from the brief and marks repo pre-fill as pending.

## 4. Confluence

- **Links on the fly:** paste a link at any point. Add a word to steer it (`reference <link>`, `notes <link>`, `answers <link>`), or paste it bare and the agent tells you how it classified it.
- **Publish:** `publish deliverables/cda-onboarding-questionnaire.md under <parent page link>`. Publishing again updates the same page, and it pulls any Confluence edits first so nothing gets overwritten.
- **Answers typed into Confluence:** `pull deliverables/cda-onboarding-questionnaire.md` brings CDA's answers and comments back into the register and the plan.
- **MCP down:** open the file in VS Code's Markdown preview (Ctrl+Shift+V), select all, copy, and paste into the Confluence editor. Headings and tables carry over.

## 5. Commands

| You type | What happens |
|---|---|
| `discover cda` | Cited inventory of WAMI and CRIA in CDWP, plus everything that depends on them |
| `compare references` | Matrix of what every reference onboarding doc asks; rerun after adding one |
| `draft questionnaire` | Reusable template (OADIP structure, questions from all references) plus the pre-filled CDA copy |
| `decide composer` (or any topic) | Options, trade-offs, recommendation; logged as PROPOSED until you confirm |
| `plan` | Phased migration plan with owners, approvals, validation and rollback |
| `ingest` + link, notes or answers | Saves it, updates questions, decisions and plan, reports what changed |
| `publish <file> under <parent link>` | Creates or updates the Confluence page, never overwriting edits made there |
| `pull <file>` | Brings edits, answers and comments from the Confluence page back in |
| `status` | Ten-line snapshot |
| `review` | Self-audit: uncited claims, invented values, coverage gaps, Confluence format |
| `wrap` | Saves state for next session |

## 6. Weekly loop

1. After each platform or CDA meeting: paste the notes page link (or the notes).
2. When CDA answers on the Confluence page: `pull` the questionnaire.
3. Before anything goes to CDA: `review`, then `publish`.
4. End of every session: `wrap`.
