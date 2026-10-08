# Revise drift tickets: one compact table per module

Revise the existing drift script. Do not rebuild it. Keep everything already proven: the dedup key `module:targetVersion`, label sanitization, status-free JQL, env-var secrets, `-DryRun` payload preview, the changelog warning on major jumps, `-MaxTicketsPerRun`, and the full 15-column CSV/Excel output (that stays the complete record).

## Problem
Ticket descriptions list one row per module reference, per line, per branch. Storage hit 696 rows and had to be truncated. The engineer picking up the ticket needs to know where the pin lives and what it moves to, not every line.

## New ticket table
One ticket per module (dedup key unchanged). Inside it, one row per unique (repo, current version).

Columns:
| Repo | Current | Target | Environments | Files | Example file |

- Environments: comma list of every environment where that pin appears. Work out from the actual repos whether environment comes from the branch name or the folder path, use that, and state which in your summary. Do not show the same value twice as both branch and environment.
- Files: count of files with that pin.
- Example file: one path, taken from the lowest environment (dev first), so the engineer knows where to start.
- No line numbers. No per-branch or per-line rows.
- If a repo has mixed current versions for the same module, it gets one row per current version. Never fold different current versions into one row.

Below the table: total references covered, and where the full detail lives.

## Branch scope
List every branch you scanned and classify it: environment branch, default branch, or other (feature, stale). Only environment and default branches feed tickets. Exclude the rest by default and add `-IncludeBranches` to override.

## Full detail
Add an optional `-AttachReport` switch, off by default, that attaches that module's slice of the CSV to the ticket. A local file path in a ticket is useless to whoever picks it up. Keep it off until the basic create path is proven live.

## Guards
- Integrity: for each dedup key, the sum of references across the collapsed rows must equal that key's drift findings before collapsing. If not, fail the run and report. Nothing may be silently dropped.
- Size: if any ticket table exceeds 25 rows, stop and report it instead of truncating. Keep the char budget only as a backstop.
- Make Jira project and epic link parameters (`-JiraProject`, `-EpicKey`). No hardcoded project.

## Prove it before changing code
Answer in comments at the top of the script:
1. Why group by current version instead of showing one representative environment? Use network (`v6.0/v8.9 -> v10.0`) as the example.
2. Why is it safe that the dedup key and label do not change with this revision?
3. What does the integrity check catch that a plain row count would not?

## Verify
Rerun `-DryRun` against real GitHub and real Jira. Write nothing to Jira. Report per key: rows before, rows after, and description chars. Confirm the same 7 dedup keys come out. Show one full proposed ticket body (storage) so I can see the final format.
