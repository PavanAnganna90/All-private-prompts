# SATP / CLG Onboarding: Initial Setup Prompt (v1)

## Who I am and what I need
I am Pavan. I was just moved from the CDWP project to the CLG project under SATP. I need to fully understand CLG: what it is, how it relates to SATP, how the repos work, how changes get deployed, and what every term means.

You will help me in two ways:
1. Learn CLG with me, step by step.
2. Build one folder that holds everything we learn, so future sessions read that folder first instead of calling Confluence and searching the repos again every time.

This is a work in progress. We will work in phases and improve this prompt as we go.

## What you have access to
- A VS Code workspace with the SATP and CLG repos.
- Confluence, through the Confluence tool (MCP).
- Starting Confluence pages or space: [FILL IN: page links or space key]
- My role on CLG: [FILL IN, or leave blank for now]
- Anything else I give you later (notes, screenshots, tickets). Treat it the same way and record where it came from.

If something does not work (a tool is missing, a page will not open, you cannot see images), tell me right away. Do not quietly work around it.

## Ground rules

### 1. Use Schwab's names exactly
- Write every name exactly as the source writes it: same spelling, same capital letters, same dashes and underscores. This covers project names, repo names, team names, environment names, pipeline names, and acronyms like SATP and CLG.
- Do not expand an acronym unless a source spells it out. If you cannot find the meaning, write "meaning not found yet" and add it to open questions.
- Do not make up names, labels, or categories that sound official. If you group things your own way to explain them, mark it: "(my grouping, not a Schwab term)".
- If two sources spell or describe something differently, write down both and where each came from.

### 2. Every fact needs a source
Next to each fact, say where it came from: a repo file path, or a Confluence page title with its link.
Mark how sure we are:
- CONFIRMED: seen in the code or config, or in two sources that agree.
- ONE SOURCE: seen in one place only.
- NOT SURE: your best reading, needs checking. Never present a guess as a fact.

If Confluence and the code disagree, the code shows what is set up today and Confluence may be out of date. Write down both and add it to open questions.

### 3. Read only. Change nothing outside the folder.
- Do not edit, commit, push, or open pull requests in any repo.
- Do not run terraform plan or apply, trigger pipelines, or deploy anything.
- Do not edit or comment on Confluence pages.
- Read-only commands are fine: listing files, searching text, git log, git branch.
- The only place you create or change files is the knowledge folder.

### 4. No secrets in the folder
Never copy passwords, keys, tokens, certificates, connection strings, or personal data into the folder. If you find something that looks like a secret, write only "possible secret in <file path>, not copied" in open questions.

### 5. Plain language
Write the folder so a new person with no background can follow it. Short sentences. No fancy words. When a technical term is needed, explain it in one simple line the first time it appears. Use tables when comparing things. Summarize; do not paste whole pages or whole files. Link to them instead.

## The knowledge folder
Create a folder named `satp-clg-knowledge/` in its own location in the workspace, not inside any repo. Do not commit it to any team repo unless I say so.

| File | What goes in it |
|---|---|
| 00-START-HERE.md | Read first, every session. What the folder is, every file with one line each, a "top facts" summary, date last updated. Keep under 100 lines. |
| 00-RULES.md | Copy the "Ground rules", "How to save tokens", and "Diagrams" sections of this prompt here word for word, so future sessions follow them. |
| 01-big-picture.md | What SATP is, what CLG is, how they relate, who uses them, what problem they solve. Only what the sources say. |
| 02-satp-vs-clg.md | How to tell SATP and CLG apart: repo names, folder paths, naming patterns, variable names, pipelines, environments, Confluence spaces, owning teams. Include a simple table: "If you see X, it belongs to Y, because Z". |
| 03-repos/<repo-name>.md | One file per repo (template below). |
| 04-how-changes-flow.md | How a change goes from code to running, in order: branches, pull requests, pipelines, approvals, environments. |
| 05-environments.md | Every environment and cloud project, account, or cluster, exactly as named, and what each one is for. |
| 06-tools-and-pipelines.md | The tools the project actually uses (for example Terraform, CI/CD, Helm, scripts) and what each one does here. Only list tools you found. |
| 07-diagrams.md | Every diagram found: where it is, what it shows written as text, and whether you actually saw it. |
| 08-glossary.md | Every term, acronym, and name: exactly as written, meaning in plain words, where found. |
| 09-teams-and-contacts.md | Teams, owners, and contact channels named in the docs or code (for example CODEOWNERS files). Work info only. |
| 10-confluence-index.md | Every Confluence page read: title, link, the page's last updated date, one-line summary, which folder file used it. |
| 11-open-questions.md | Unclear items, conflicts between sources, diagrams not seen, terms not explained. Grouped so I can take them to the team. |
| 12-how-to-look-things-up.md | For future sessions: which tool to use for which kind of question, which Confluence spaces, search words that worked and ones that did not. |
| 13-change-log.md | One short entry per session: date, what was added or changed. |

Keep each file under about 300 lines. If one gets bigger, split it and update 00-START-HERE.md.
Put this line at the top of every file:
Last updated: <date> | Sources: <list> | Status: draft / reviewed by Pavan

### Repo file template (03-repos/<repo-name>.md)
- Repo name (exact) and where it sits in the workspace
- Belongs to: SATP / CLG / shared / not sure, with the reason
- What it does, in 2 to 3 plain sentences
- Folder layout: top-level folders and what each holds
- How it gets deployed: which pipeline file, what starts it, which environments it reaches
- What it depends on: other repos, shared modules, outside services
- Key files to read first
- Recent activity: last few commits, who is active, what has been changing (from git log)
- Open questions for this repo

## How to save tokens (and when not to)
Order of lookup, every time:
1. Read 00-START-HERE.md.
2. Open only the folder file that matches the question.
3. Only if the answer is missing, marked NOT SURE, or might have changed, call a tool (Confluence, repo search, git).
4. After any tool call that taught you something new, update the folder right away and add a line to 13-change-log.md.

Do a fresh tool call, even if the folder has an answer, when:
- I ask about the current state (latest commits, open pull requests, current versions, what is deployed now).
- The answer is marked NOT SURE or ONE SOURCE and it matters for what I am doing.
- I tell you something has changed.
- A Confluence page shows a newer "last updated" date than the one in 10-confluence-index.md.

Reading habits:
- Skim before going deep: folder structure, README files, and pipeline files first.
- Do not re-read a Confluence page already in the index unless it was updated since.
- Save findings after each repo or page, so nothing is lost if the session ends.
- Skip large generated files, lock files, and vendor folders unless needed.

## Diagrams
You may or may not be able to see images. Find out in Phase 0 and tell me. For each diagram, try in this order:
1. Stored as text in a repo (.drawio, .mmd, .puml, .svg files, or mermaid blocks inside .md files): read the text directly.
2. In Confluence (draw.io, Gliffy, Lucidchart, or pasted images): check whether the Confluence tool can list the page's attachments and give you the diagram's source file. draw.io and Gliffy usually keep their source as an attachment, and that source is readable text.
3. A plain image (PNG, JPG): if you can view images, open it and describe it.
4. If you cannot see it at all: use the page text around it, the caption, and the file name. Mark it "NOT SEEN" and add it to open questions so I can paste a screenshot or describe it.

Write every diagram in 07-diagrams.md as plain text: list the boxes, then the arrows as simple lines ("A sends X to B"). Also add a mermaid version (a simple text format for drawing diagrams), so the diagram lives as text and never needs to be opened again. Mark redrawn diagrams "redrawn from the original, check against it". Never guess what a diagram shows.

## How we work: phases
Stop at the end of each phase. Give me a short summary (what you did, what you found, what you could not do) and your top questions. Wait for my reply before moving on.

Phase 0: Check your setup (no deep reading)
- List the tools you have and test each one: can you list the workspace repos, open a Confluence page, list page attachments, view an image?
- List every repo in the workspace, one line each (name plus the first line of its README if there is one).
- Create the folder, the empty files with headers, and 00-RULES.md.
- Tell me what is missing or not working.

Phase 1: Inventory
- For each repo: top-level folders, README summary, pipeline files found, and whether it looks like SATP, CLG, or shared, with the reason.
- Find the Confluence pages: start from the links I gave, then follow child pages and links. Build 10-confluence-index.md (titles, links, dates, one-line summary). Do not read pages in depth yet.
- Suggest a reading order (which pages and repos first, and why). Wait for my OK.

Phase 2: Big picture
- Read the overview and architecture pages and the main READMEs.
- Find out from the sources how CLG and SATP relate (part of it, built on top of it, separate but connected). Do not assume.
- Write 01-big-picture.md and 02-satp-vs-clg.md, and start 08-glossary.md.

Phase 3: Repos, one at a time
- Fill in 03-repos/<repo-name>.md using the template.
- Update the glossary, diagrams, and open questions as you go.

Phase 4: How things flow
- Follow one change from code to running. Write 04, 05, and 06.

Phase 5: Check your work
- Pick 10 facts from the folder and re-check them against their sources. Fix anything wrong.
- Finish 00-START-HERE.md and 12-how-to-look-things-up.md.
- Give me: a one-page summary of CLG in plain words, the top 10 open questions to take to the team, and what you would change in this prompt.

## Start now
Begin with Phase 0. Do not start Phase 1 until I reply.
