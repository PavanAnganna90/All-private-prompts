# CDWP KT Plan: Few Sessions, Right Order

## Inputs (fill before running)
- KT From: Anganna, NagendraPavan
- KT To: <Last, First>
- First session date: <M/D/YYYY> (blank = write TBD in the date columns)
- Cadence: 3 sessions per week, Mon / Wed / Fri (edit if different)
- Session length: 60 min
- Live sessions: aim for 6 to 8, plus one async row for access and pre-reads
- Will she join on-call during the KT window: <yes / no / unknown>
- Extra topics I want covered: <optional>

## Role and goal
You are a senior GCP platform engineer helping me hand over the Cloud Data Warehouse Platform (CDWP) to a teammate who is joining our team while I move to hers. You have the CDWP repo open. Build a short, ordered KT plan that gets her to two outcomes as fast as possible:
1. She can make and ship a safe CDWP change on her own.
2. She can support the platform when something breaks.

The output is a sheet with the same columns as our team's existing KT sheet, plus a session guide for me. A few dense, well-ordered sessions beat a long list of rows.

## Rules
- Read-only. Do not edit files, run terraform plan or apply, or run any gcloud or bq command that changes anything.
- Work from evidence: this repo, its git history, and Confluence if the Confluence MCP tool is available. Never invent process, owners, group names, links, or tools. If the repo and Confluence do not answer something, write "UNKNOWN, ask: <who/what>".
- Plain language. No em-dashes. Paste-ready.
- Do not print your inventory. Use it to decide, then show only the outputs below.
- Read the repo in a targeted way: tree, READMEs, docs folders, pipeline files, environment and tenant folders, module sources and version pins, and `git log` for the last 6 months. Read one tenant end to end and skim the rest.

## Step 1: Inventory (silent)
1. What CDWP is: purpose, tenants, data flow, GCP folders and projects, environments.
2. Repo layout: where tenants, shared infra, modules, IAM, and pipelines live. Naming conventions. State and backend setup.
3. How a change ships: branch and PR rules, CI/CD files, plan and apply flow, promotion across environments, approvals, CRQ steps if referenced.
4. Services in use and their recurring upkeep (BigQuery, GCS, Dataproc, Cloud Functions, and whatever else the repo shows). Note version pins that need periodic upgrades.
5. Access model: groups, service accounts, IAM bindings, dataset and table-level access, how a new tenant gets access.
6. Run and support: alerting, dashboards, logging, DR and replication config (for example BQ cross-region replication), runbooks.
7. Hot spots: which paths change most in git log and which kinds of change are most common. These are what she will touch first.

## Step 2: Consolidate and order
Grouping
- Merge topics that share a repo area, a workflow, or a mental model. Example: Terraform module upgrades, Dataproc image refresh, and Cloud Function image upgrades are all "version upkeep" and belong in one session.
- A topic earns live time only if it needs a demo, a judgment call, or knowledge that is not written down. Anything she can just read goes to row 0 or to the previous session's homework.
- Max 4 sub-topics per session. Each session must fit the session length with 10 minutes left for questions. If it does not fit, split it or move parts to self-read.
- Every session must leave her able to do one specific thing she could not do before. If you cannot state that outcome in one line, merge the session into another.

Ordering
- Dependencies first: big picture before repo, repo before the change workflow, change workflow before any topic taught through a change (IAM, upkeep, new resources, DR).
- Then frequency: what the git hot spots say she will touch in her first weeks comes earlier.
- Then risk: production-impacting work (incidents, DR, access changes) before optimization (FinOps, capacity, service improvements).
- If she joins on-call during the KT window, move run and support to right after the change workflow.
- Last session: she drives a real low-risk change end to end while I watch, then we close out the ownership handover (what I own today, recurring tasks, approvals, alert routing, group memberships, open tickets).
- Use the day between sessions: each session ends with a small homework task that sets up the next one.

Scheduling
- Assign dates from the first session date using the cadence. Skip weekends and US holidays (watch Thanksgiving week). List any date you skipped.
- Row 0 starts on the first session date and runs in parallel, with a target end date before session 3.

## Reference topic lists (coverage check only, not a template)
Other KT sheets used these. Do not copy their structure or their row count. Use them only to make sure nothing important is missed. Every item must land in a session, in self-read, or in "Not for this KT" with a reason.

A. Another platform's onboarding KT
- Access request tracking; new joiner trainings (GitHub, Terraform certification, change, incident, problem, and knowledge management)
- Access: GCP, Confluence, repo
- Team process: Kanban or Scrum, sprints, project reporting
- Solution architecture docs (SAD) and Confluence documentation
- Tenants and pilot projects: infra setup and onboarding data contracts
- Analytical Workspaces in CDWP (replaces HDMs)
- MyAccess and AD groups for a new tenant and GCP project
- Shared org Terraform repos vs our own, and coding standards
- AD groups, service accounts, ESA, CIDR ranges, Front Door requests
- GCP resource hierarchy
- Code deployment and promotion
- Alerts and monitoring dashboards
- Remedy incidents, CRQ Remedy groups, SOP for CRQ creation
- xMatters and on-call support
- CRR/MDR onboarding for disaster recovery

B. CDWP topics I covered in a cross-platform session
Terraform module upgrades, new resource creation, IAM updates, multi-region bucket cleanup, disaster recovery, Dataproc image refresh, Cloud Function image upgrade, BQ CRR, Gen2 function setup, table-level access on BQ datasets, cost optimization and FinOps, service improvements, alerting and monitoring, capacity planning, automation.

C. In-flight work I own (edit before running; delete anything done or not mine)
- Moving CDA tenants WAMI and CRIA from the CDWP repo to the CDW repo, following the PAML move
- CDWP tenant onboarding questionnaire for the CDA team
- New tenant POC environment (SharePoint and M365 Power Automate source) and the reusable tenant onboarding runbook
- Black Duck onboarding gap analysis for CDWP repos
- Terraform module ref drift check that opens Jira tickets

## Output
Produce exactly these five parts, in this order. No intro and no closing summary.

1. Sheet tab "CDWP KT": tab-separated text in one code block, header row included, so I can paste it into cell A1.
   Columns: Sr. No, Platform, Topic, KT From, KT To, Start Date of KT, End Date of KT, % complete
   - Sr. No starts at 0. Row 0 is access, trainings, and pre-reads.
   - Platform: CDWP, CDWP/<tenant>, or Team process.
   - Topic: session title, then the sub-topics in parentheses. Example: "Ship a change end to end (branch and PR, plan review, CRQ, promotion to prod)".
   - Dates as M/D/YYYY. % complete is 0%.

2. Sheet tab "Reference links": tab-separated text in one code block.
   Columns: Sr. No, Topic, Where, Type, Notes
   - Where: real repo paths, or Confluence URLs only if you found them through the tool. Otherwise "TBD: <page to find>".
   - Type: Repo, Confluence, Runbook, Dashboard, or Ticket.

3. Session guide for me (markdown). For each session:
   - Outcome: "After this, she can ..."
   - Why this slot: one line on the ordering reason
   - Agenda with minutes
   - Open live: repo paths, plus one real merged change from git history as the worked example (commit or PR id and one line on what it did)
   - Hands-on for her: read-only or non-prod only
   - Homework before the next session
   - My prep: what I must confirm before the session, with UNKNOWN items and who to ask. Flag any sub-topic where the repo gives me little to show, so I can prepare it or hand it to someone else.

4. Coverage check (markdown table): every item from lists A, B, and C and every major repo area you found, mapped to a Session #, Self-read, or Not for this KT (with the reason). Nothing silently dropped.

5. Open questions for me: at most 5, and only ones that would change the plan.
