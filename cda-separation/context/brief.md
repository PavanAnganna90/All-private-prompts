# CDA separation from CDWP: context brief

Updated: 2026-10-21
Owner: Pavan (CDWP platform team)
Sources: platform team meeting "Discuss CDA repo separation" (written notes plus audio recording, shared 2026-10-21), and Pavan's clarifications.
Confidence: items tagged (rec) come only from a machine transcript of the audio. Treat them as likely but unverified. Everything else comes from the written notes or from Pavan.

## 1. Summary

- CDA is a customer team with two tenants on the CDWP platform: WAMI and CRIA. Their infrastructure is defined in the CDWP repo today.
- We are moving WAMI and CRIA out of the CDWP repo into new GCP projects defined in the CDW repo. PAML already made this exact move (CDWP to CDW) and is the pattern to follow.
- Approach: build a greenfield CDA environment in CDW in parallel while CDWP keeps running, CDA migrates, then we clean up CDA's resources in CDWP.
- The first deliverable is a CDWP tenant onboarding questionnaire: a reusable template (OADIP's structure, with questions drawn from every reference onboarding doc we have: SATP, CLZ and others) that we use to collect what we need from CDA before we build. Tracked under epic SFADP-8337.
- CDA's last word was to start in Q1 together with us (rec). Composer version, data migration, log sinks, identity approvals and DR are the big open items.

## 2. Glossary

| Term | Meaning |
|---|---|
| CDWP | Existing platform repo and its GCP environment. Today WAMI and CRIA are defined here and run on CDWP's existing subnets and services. |
| CDW | Newer repo. YAML-config driven. Tenants get their own new projects. PAML already lives here; not much else yet. |
| CDA | The customer team being separated. Owns two tenants: WAMI and CRIA. |
| WAMI, CRIA | CDA's tenants on CDWP. The exact identifiers used in the repos are still to be confirmed. |
| PAML | Tenant already moved from CDWP to CDW. The reference pattern for this work. |
| OADIP | Another platform. Its tenant onboarding doc sets the structure of our questionnaire. |
| BMODI | A tenant on OADIP that got a large dedicated Dataflow subnet. The precedent for CDA's Dataflow subnet. |
| SATP | Another platform with a tenant onboarding doc. One of the reference docs. |
| CLZ | Another platform the team works with. It is working through its own log sink transfer right now (rec). Full name not confirmed. |
| MODQ | A system inside one of CDA's tenants (rec) that has to keep its history. Full name not confirmed. |
| Plat team | The CDWP platform team (Pavan's team). Keeps owning CDA's Terraform after the split (rec). |
| Cloud, Cloud Networking | Central cloud teams. Own Project Factory approvals, network allocation and Composer v3 enablement. |
| Project Factory | Central mechanism that creates GCP projects. Changes need Cloud approval. |
| ESA, ESA committee | A class of service accounts whose access to CDW needs committee approval. Full name not confirmed. |
| GAR | Google Artifact Registry. |
| STS | Storage Transfer Service (GCS bucket-to-bucket copy). |
| CRR | BigQuery cross-region replication. |
| DR | Disaster recovery. |
| SFADP-nnnn | Jira epics in the SFADP project. |

## 3. Decisions so far

Agreed inside the platform team. Items marked "confirm with CDA" still need CDA's agreement.

| ID | Decision | Status |
|---|---|---|
| D-001 | CDA's new infrastructure goes in the CDW repo, in new GCP projects created through Project Factory. Nobody raised concerns. | Agreed internally, confirm with CDA |
| D-002 | Approach: set up new CDA infrastructure in parallel, support CDA moving over, then clean up the old CDWP resources (Composer, Dataflow, everything else). The epics are already laid out this way (rec). | Agreed internally, confirm with CDA |
| D-003 | CDA's new resources do not reuse CDWP's existing subnets. They get new subnets (rec). | Agreed internally |
| D-004 | The platform team keeps owning and running the Terraform for CDA after the split (rec). | Agreed internally |
| D-005 | Build CDA the CDW way (YAML configuration), not the CDWP way. The platform side decides internal implementation details. | Agreed internally |
| D-006 | Questionnaire: follow the OADIP onboarding doc's structure, and draw questions from every reference onboarding doc in context/templates/ (SATP, CLZ and any other platform or tenant). | Decided by Pavan, 2026-10-21 |
| D-007 | Confluence is where this work lives. Sources arrive as Confluence links at any time; deliverables are written Confluence-ready and published through the Confluence MCP when Pavan says so. The parent page is chosen at first publish. | Decided by Pavan, 2026-10-21 |

## 4. Topics

Every topic below must end up as a questionnaire question, a decision, or a plan task.

| ID | Topic | What we know | Open questions | Owner |
|---|---|---|---|---|
| T-01 | Sizing | We need sizing from CDA up front. It drives subnet size and the data transfer plan. | Data volumes per bucket and dataset, growth, Dataflow peak workers, Composer load (DAG count, concurrency). | CDA |
| T-02 | Dataflow subnet | Drew recommends a large Dataflow subnet, like the one built for BMODI on OADIP. CDA runs on CDWP's existing subnets today, including the current Dataflow subnet (rec). Cloud Networking has to allocate the new one. | What size? Does it still need to be routable to on-prem? | Platform, Cloud Networking, CDA (on-prem needs) |
| T-03 | Firewall and GAR | The new subnet needs its own firewall rules in the new repo: on-prem routing if still required, and access to Python packages through Artifact Registry (rec). | Which on-prem destinations and ports? Which GAR repos? | Platform, CDA |
| T-04 | Composer version | Big decision for CDA. Options: (a) v2, which we know works, and handle v3 separately under SFADP-767; (b) move to v3 as part of this; (c) v2 and v3 side by side, for example one per project, to validate results. Cloud said v3 support should land in Q4 (last heard). CDA has been asking for v3 for about two years (rec). v3 runs Composer and Airflow upgrades inside maintenance windows you must specify, may simplify the yearly image update but brings other complications, and is more Google-managed (rec). Suggestion: one team runs a v3 PoC first instead of everyone trying it; this migration could be that team (rec). Cost angle: SFADP-399. | Which option? Who runs the PoC? What validation does CDA need? | Platform and CDA, Cloud (v3 support) |
| T-05 | Composer stockout | The platform team has to work through Composer stockouts. The problem is initial provisioning; once an environment is up, it is fine (rec). Google recommended v3 for the stockouts; the team is unsure how much that helps and hopes the stockouts do not just move to Google's side (rec). | Mitigation if provisioning hits a stockout (retry, zone, sizing, reservation). | Platform |
| T-06 | Bucket names | Bucket names will change because GCS names are global. Naming idea: keep the same purpose-based names, with the last four characters varying per environment (rec). | Where do CDA's apps reference bucket names? | Platform (naming), CDA (app changes) |
| T-07 | Bucket contents | If data must move, use STS jobs. The team has done a large STS bucket migration before (rec). Size the buckets first. Metadata (creation time, version history) is separate from the data; STS can be configured to preserve it (rec). Google was working on a bucket rename feature; check if it exists now (rec). | Does CDA need the data moved, or can they start fresh? Is metadata or object versioning needed? | CDA (need), Platform (method) |
| T-08 | BigQuery | Datasets can keep the same names in the new projects. CRR config is affected (see Impacts). | Does the data need to move? Which datasets, how big, who consumes them? | CDA, Platform |
| T-09 | Log sinks | How do we move the sinks without losing data? CLZ is solving the same problem now: the log sink module can point at a pre-created dataset; the open part is keeping the data already in the old destination (rec). | Cutover approach with no gap. Reuse CLZ's answer. | Platform |
| T-10 | MODQ | Lives in one of CDA's tenants (rec). Must keep its history. Someone mentioned a centralized MODQ being discussed on OADIP (rec). | Migrate it, keep it where it is, or move to a centralized MODQ? | CDA, Platform |
| T-11 | Identity and access | New service accounts and ESAs. Cross-project service account access. ESA committee approval is needed to access CDW. | Which accounts, which cross-project grants, approval lead time? | Platform, ESA committee, CDA |
| T-12 | CLZ path | Unknown whether CDA must change anything to go through CLZ. | Does CDA need changes to go through CLZ, or is that already done with CDWP data? | CDA, Platform |
| T-13 | App breakage | Risk that CDA's own repos hard-code project, bucket or dataset names and break when moved (rec). CDA's config items likely live in a config table or Parameter Manager (rec). | What in CDA's apps changes when names change? | CDA |
| T-14 | Repo footprint | The team wants to keep repos light. No concern about putting CDA in CDW, which has little in it so far (rec). | None yet. | Platform |
| T-15 | Timeline and change calendar | CDA aims to start in Q1 2027. CDA's change calendar works differently from ours (rec). See section 7. | Do the two calendars line up? Any freeze periods? Is CDA's scope and commitment confirmed? | CDA, Platform |
| T-16 | DR | CDA should end up with its own DR plan but stay in CDWP's DR window, and must be DR-ready at go-live (rec). See section 7. | CDA's DR requirements per tenant? Which datasets tie CDA to CDWP's DR? Can CDA pause activity during CDWP's DR? | CDA, Platform |

## 5. External dependencies

- Cloud Networking: allocate networking for Dataflow and Composer.
- Cloud: approve the Project Factory changes that create the new projects.
- Cloud: Composer v3 support, plus a v3 PoC within our team.
- ESA committee: approve ESAs' access to CDW.

## 6. Impacts

- BigQuery CRR config.
- CDA DR plan. Goal: CDA ends up with its own DR plan (rec).
- CDWP DR plan.
- More to be identified. The notes end this list with "?".

## 7. Timeline and DR

- CDA's last word: start in Q1 2027 together with us. Scope is still being discussed, an October check-in was meant to confirm everyone is still on board, and one stakeholder was worried about how much work it is (rec).
- CDA's change calendar works differently from ours. Check that it lines up with this timeline (rec).
- DR (rec):
  - Starting at the beginning of the year suits DR planning.
  - Anything that goes live has to be DR-ready from day one.
  - Fallback: if the move is not finished before a DR exercise, run DR across both repos.
- DR window (rec): CDA stays in the same DR window as CDWP. PAML, already on CDW, still runs DR together with CDWP because of its BigQuery dependency (verify the exact dependency in the repos). CDA could be made DR-independent, except that it would still have to pause activity during CDWP's DR so CDWP's datasets stay consistent.
- Cadence (rec): the platform team meets weekly on this. Next steps were to keep adding impacts to the list during the week, then raise the bigger items in a joint meeting with CDA.

## 8. Doing it differently from CDWP

- Use the CDW repo's YAML configuration model.
- Epics to consider:
  - SFADP-8337 CDWP Onboarding and Tenant Documentation (home of the questionnaire)
  - SFADP-767 Update to Cloud Composer v3
  - SFADP-399 Explore reducing Composer cost

## 9. Reference material to load

- Confluence links: Pavan pastes them whenever he finds one. The agent saves a snapshot under context/ and logs it in work/sources.md.
- context/templates/: one file per reference onboarding doc, named `<platform>-<tenant>-onboarding.md`. OADIP first (it sets the structure), then SATP, CLZ and any other platform or tenant. Blank templates and filled docs both help.
- Most useful filled docs: BMODI on OADIP (closest match for the large Dataflow subnet) and anything written for PAML's move to CDW (closest match for the move itself).
- repos/cdwp and repos/cdw.
- Notes from the earlier large STS bucket migration, if they exist.
- CLZ's log sink transfer approach, once they settle it.
