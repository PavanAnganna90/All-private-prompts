# CDA separation agent space: setup

## 1. Build the folder

```
cda-separation/
  AGENTS.md              paste from this pack
  context/
    brief.md             paste from this pack
    templates/           one file per reference onboarding doc (OADIP first, then any others)
    meetings/            empty for now
    answers/             empty for now
  repos/
    cdwp/                clone of the CDWP repo
    cdw/                 clone of the CDW repo
```

- Templates: one file per reference onboarding doc, named `<platform>-<tenant>-onboarding.md`, for example `oadip-bmodi-onboarding.md`, `satp-<tenant>-onboarding.md`, `clz-<tenant>-onboarding.md`. Plain pasted text is fine. Optional first line: `Source: <link>, as of YYYY-MM-DD`. Blank templates and filled docs both help; filled ones show the level of detail to expect.
- Already have CDWP and CDW open as workspace roots? Skip `repos/` and use the kickoff prompt in `.github/prompts/cda-separation-kickoff.prompt.md` instead of the one below.
- Repos: clone fresh into `repos/`, or point at existing clones. On Windows, a junction needs no admin rights: `New-Item -ItemType Junction -Path repos\cdwp -Target C:\path\to\cdwp`.
- If you put `cda-separation` itself under git, add `repos/` to `.gitignore`.

## 2. Connect your agent

- **Copilot (VS Code, agent mode):** open the `cda-separation` folder. Recent VS Code builds read `AGENTS.md` at the root automatically. If yours does not, copy the same text into `.github/copilot-instructions.md`.
- **Claude Code:** create `CLAUDE.md` containing one line: `@AGENTS.md`, then start it in `cda-separation`.

## 3. Kickoff prompt

```
Read AGENTS.md and context/brief.md. Run init, then run discover paml.
Stop after discover paml and give me a 10-line summary plus the new open questions.
Do not start discover cda until I say go.
```

Need the questionnaire before discovery is done (for example for the CDA meeting)? Send `init` then `draft questionnaire`. It fills what it can from the brief and marks repo pre-fill as pending.

## 4. Commands

| You type | What happens |
|---|---|
| `discover cda` | Cited inventory of WAMI and CRIA in CDWP, plus everything that depends on them |
| `compare references` | Matrix of what every reference onboarding doc asks; rerun after adding one |
| `draft questionnaire` | Reusable template (OADIP structure, questions from all references) plus the pre-filled CDA copy |
| `decide composer` (or any topic) | Options, trade-offs, recommendation; logged as PROPOSED until you confirm |
| `plan` | Phased migration plan with owners, approvals, validation and rollback |
| `ingest` + pasted notes or answers | Files them, updates questions, decisions and plan, reports what changed |
| `status` | Ten-line snapshot |
| `review` | Self-audit: uncited claims, invented values, coverage gaps |
| `wrap` | Saves state for next session |

## 5. Weekly loop

1. After each platform or CDA meeting: `ingest` the notes.
2. When CDA sends answers: `ingest` them.
3. Before anything goes to CDA: `review`.
4. End of every session: `wrap`.
