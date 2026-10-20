# CDWP Tenant POC: Plan-First Terraform Build

## Role and goal
You are a senior GCP platform engineer mentoring me. I am two months into the company. We need a minimal POC environment in GCP for a new CDWP tenant whose data comes from SharePoint and M365 Power Automate. Use the architecture diagrams and requirements already shared in this chat, and use the CDWP Terraform repo as the reference for naming, provider and backend setup, labels, and style.

The POC has a hard deadline. Get it running with the least work that is still secure, and help me understand every piece. Do not write code until I approve the plan.

## How we work
- Phase 0 is the plan only. No Terraform.
- Phases 1 to 4 come one per reply. Stop after each phase and wait for my "go".
- Never invent company process, owners, or names. If something is not in the repo or this chat, write "UNKNOWN, ask: <who/what>".
- Use plain language. For every resource and role, say what it does, why we need it, and what breaks without it.
- Keep the Terraform flat: no for_each, no count, no nested modules. One POC environment.

## Requirements (from the Terraform Resource Review)
1. GCS buckets (create them empty, IAM only)
   - store: job SA gets objectAdmin. Service SA gets objectViewer on the full bucket, plus objectUser with an IAM condition that limits writes to clients/cs/{units/, onboard/, proposals/, pack.yaml}
   - cs-landing: job SA gets objectAdmin. Service SA gets nothing. A Power Automate ingestion SA gets objectUser later, so add a placeholder only.
   - tmp: job SA and service SA get objectUser. Add a 7-day auto-delete lifecycle rule.
2. BigQuery: a warehouse dataset and a graph dataset
3. Cloud Run: a UI service, an API service, and a Cloud Run Job (ingestion, eval)
4. Machine SAs: service SA (buckets, BQ, secrets, runs the job), job SA (buckets, BQ), deploy SA (Artifact Registry, act-as the service and job SAs, GitHub Workload Identity Federation)
5. Vertex AI: text-embedding-004, plus Gemini flash-lite, flash, and pro (3.x or equivalent)
6. Artifact Registry (Docker) and Secret Manager (a session key and passwords, mounted into Cloud Run as env vars such as SESSION_SECRET)

## Phase 0 output (plan only)
A. Scope: list what is in the POC and what is deferred, for example VPC and private networking, CMEK, multiple environments, Power Automate ingestion, and dashboards. Define "POC done" as a short checklist.

B. Access map: a table of every caller, target, auth method, and role. Cover:
   - end user to the UI: public, IAP, or internal ingress, with the trade-offs of each
   - UI to the API: service-to-service auth with run.invoker
   - the API and the Job to Vertex AI, BigQuery (a dataset role plus jobUser at the project), GCS, and Secret Manager (an accessor role on each secret)
   - the service SA executing the Cloud Run Job
   - GitHub to the deploy SA through WIF, restricted to our repo and branch
   - human developers through Google groups, not individual accounts: what they can see and do, and how they test as a SA through impersonation instead of keys

C. Gaps I should raise. Check at least these:
   - Does the job SA need Vertex AI access for embeddings and eval?
   - Does any SA need a key file? Keys should be avoided, so check the org policy.
   - Is text-embedding-004 still available, and in which regions?
   - Are the Gemini 3.x models available in our region and allowed by org policy?
   - How will Power Automate authenticate to GCP later?
   - Who runs terraform apply for the POC, and where does the state live?

D. Dependency order: what must exist before what, including every required API, with one line on why each is needed.

E. Request list, in the order to send it, longest lead time first. Cover project and billing, org policy exceptions, Vertex model access and quota, group creation, WIF and GitHub approval, network, and security review. For each item give the owner, the exact ask, a rough lead time, and whether it blocks the POC.

F. Trade-offs: for each shortcut we take for speed, state the risk and what production would do instead.

G. Timeline: map the phases to the deadline, and show what we can build while waiting on requests.

## Build phases (after I approve Phase 0)
- Phase 1, Foundation: APIs, the three machine SAs, and the developer group bindings. Then run terraform plan and apply.
- Phase 2, Data: the three buckets, IAM including the condition, the lifecycle rule, and the two BQ datasets.
- Phase 3, Runtime: Artifact Registry, secrets, WIF, the Cloud Run services and job, and Vertex AI access.
- Phase 4: validate and document.

For each phase, give the files touched, code with inline comments, gcloud checks that prove it works, and what to look for in the plan output.

## Final deliverable: tenant onboarding runbook
Write it so the next tenant can follow it without me. Include the intake questions to ask the tenant, the request sequence with owners and lead times, build steps per phase, a validation checklist, the gotchas we hit, and a decision log covering what we chose, why, and what we deferred. Use placeholders like <tenant> so it can be reused.

Start with Phase 0 only.
