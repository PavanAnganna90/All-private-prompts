# Terraform Module Drift to Deduplicated Jira Tickets

Build a PowerShell script that detects Terraform module version drift and opens deduplicated Jira tickets. Follow this spec exactly and do not cut corners.

## Context
Our Terraform runs in Cloud Build, not locally. Do not run terraform init, plan, or anything that touches state. The script is a pure read. Module blocks pin a version via a ref in the source URL, for example `?ref=v5.5`. The upstream module repo on GitHub has newer tags, for example `v5.8`. Drift is the gap between the pinned ref and the latest upstream tag.

## What the script must do
1. Read our consuming repo and parse every module block to extract module name, the pinned ref version, and the file plus environment it appears in.
2. For each module, query the GitHub API for the upstream repo's latest release or tag.
3. Compare pinned ref to latest upstream tag. If they differ, that is a drift finding.
4. For each drift finding, compute a deterministic dedup key of the form moduleName:targetVersion, for example `terraform-aws-vpc:v5.8`.
5. Before creating a ticket, run a Jira JQL search for that exact key stored in a Jira label. If a matching issue exists, skip. If not, create the ticket and stamp the dedup key as a label on it.

## Hard requirements
- Dedup state must live in Jira only. Do not write a local state file. Do not rely on anything stored on the machine running the script. The script must be safe to run from any machine, by any team member, any number of times, producing exactly one ticket per module-version drift.
- The dedup key becomes a Jira label at create time and is read back via JQL `labels = "key"` on every future run. Jira labels cannot contain spaces, so sanitize the key the exact same way in both the create call and the search query. Any mismatch in sanitization breaks dedup.
- Secrets, the GitHub token and the Jira API token, must be read from environment variables, never hardcoded.
- The script must be idempotent. Running it repeatedly with no new upstream changes creates zero new tickets.
- Preserve the existing tabular output of drift rows and columns. That is the visualization and it stays.

## Prove the design before coding
Before writing the code, answer these questions in comments at the top of the script, and make the implementation consistent with your answers:
- Why does a Jira-based dedup key guarantee no duplicates even with concurrent runs from different machines?
- What happens when upstream moves from v5.8 to v5.9, and why is that correctly treated as a new ticket rather than a duplicate?
- What is the failure mode if the Jira search returns a false negative, and how does the label strategy minimize it?

## Output
Output a single self-contained PowerShell script plus a short README section listing the required environment variables and an example of scheduling it with a weekly scheduled task.
