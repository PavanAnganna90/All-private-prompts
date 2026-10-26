---
name: connect-the-dots
description: "Explain a doc, folder, Terraform/GCP config, or what the agent just built so a new joiner connects the dots: naming decoder, IAM (groups, service accounts, roles, cross-project access), network and firewall, data flow, environments, pipelines, logs. Use when I ask to explain, walk me through, decode, help me understand, onboard me to something, or recap what we just did."
---

# Connect-the-Dots Explainer

Take these from my message. If one is missing, infer it and tell me what you assumed.
- TARGET: file, folder, doc, or "this conversation"
- MODE: onboard (explain something that already exists) or recap (explain what you just built or changed)
- DEPTH: full (default) or quick
- FOCUS (optional): naming, iam, network, data-flow, pipelines, logs, environments

## Who I am and what I need
I'm a senior cloud/platform engineer. I already know Terraform, Kubernetes, CI/CD and cloud IAM concepts. I'm new to THIS project and its GCP setup. Don't teach me the basics. Show me how these specific pieces fit together so it clicks: "oh, that's what this is and why it's there." Treat me as a new joiner reading this for the first time.

## The 5 questions (answer these for every component you explain)
1. What is it, in plain words?
2. Why does it exist here, and what problem does it solve?
3. What does it connect to, upstream and downstream?
4. Which identity does it run as or require, and in which project?
5. What breaks, or who loses access, if it's removed or misconfigured?

## Ground rules
- Read before you explain. Open the real files: Terraform (modules, variables, tfvars, locals), YAML, DAGs, pipeline configs, docs. Resolve variables so you show real names, not placeholders.
- Tag every claim: [seen: path or resource address] / [inferred] / [unknown]. Never present a guess as a fact.
- If the target is a document, check its claims against the code where you can and list every mismatch.
- Internal acronyms and system names (team codes, tenant codes, things like MODQ): define them only from the source. If the source doesn't define one, say so and add it to "Questions for the team."
- Use GCP-native terms. Define jargon in one short clause the first time it appears. Add a one-line AWS comparison only when it genuinely helps, and say where the comparison breaks down.
- Never print secret values, keys or tokens. Name them and say where they live.
- Plain language, short bullets and tables, no filler, no em-dashes. If a section doesn't apply, skip it with one line ("N/A: no firewall rules in scope").

## Output (in this order)

1. The short version
   Five sentences at most: what it is, what problem it solves, who uses it, and what would be missing without it.

2. The map
   An ASCII diagram showing projects (with their environment), networks, key resources and identities. Draw arrows for data and access, and label each arrow with the identity used ("runs as <sa>"). Every later section zooms into one box on this map.

3. Naming decoder
   - State the naming convention as a template, e.g. <prefix>-<platform>-<tenant>-<env>-<service>-<purpose>.
   - Table: Name | segments decoded | what it actually is | defined in.
     Cover projects, folders, datasets, buckets, groups, service accounts, custom roles, VPCs/subnets, firewall rules, Composer environments, topics and pipelines.
   - Flag names that break the convention, and look-alike names that mean different things.

4. Who can do what (identity and access)
   First explain the model in plain terms, using this project's examples:
   - Principal = who. Humans get access through Google groups, workloads through service accounts, and external systems or CI through Workload Identity Federation.
   - Role = a bundle of permissions. Permission = one action (e.g. bigquery.tables.getData).
   - Binding = principal + role, attached at one level: org > folder > project > resource (dataset, bucket, topic, SA). Lower levels inherit from the levels above them.
   - A service account is both an identity and a resource. Something can act AS a service account only if it has roles/iam.serviceAccountUser or roles/iam.serviceAccountTokenCreator granted ON that service account.
   - Terraform: *_iam_member adds one binding. *_iam_binding owns every member of that role on that resource. *_iam_policy owns the whole policy. Say which one each grant uses and what it could wipe out.
   Then give:
   - An access matrix: Principal | type | role | on resource | level | defined in | why it needs it.
   - Chain traces for the 2-3 most important access paths, e.g. "user in group X → role Y on project Z → can impersonate SA W → SA W has dataEditor on dataset D in project P."
   - Cross-project access: for each identity that touches another project, give its home project, the target project, and the exact binding that allows it.

5. Network and firewall
   - For each VPC/subnet: the owning project (Shared VPC host or service project), its ranges, and what runs there.
   - Firewall table: rule | direction | from (ranges/tags/SAs) | to | ports | allow/deny | priority | purpose | what breaks without it.
   - Private connectivity (Private Google Access, PSC, peering, Cloud NAT): which workload needs it and why.

6. Data flow, end to end
   Follow one real unit of data (a file, row or message) from its source to its final consumer. Use numbered steps. For each step give: component, project, the identity it runs as, trigger or schedule, input → output, and where it logs.

7. Environments and pipelines
   - Map each environment → project → config (tfvars, workspace, branch).
   - Trace commit to applied: stages, which identity runs plan vs apply, approval gates, and how CI authenticates.
   - Say what differs between environments and why.

8. Logs and observability
   Where each component's logs land (project, log bucket, sink, export), who can read them, and where to look first when something fails.

9. "Oh, so that's why"
   3-7 non-obvious design decisions. For each: what it is, why it was done this way, and what goes wrong with the naive approach.

10. Failure decoder
    Table: symptom or error I'll likely hit | probable cause | binding/rule/config to check | command to confirm.

11. Verify it yourself
    5-10 READ-ONLY commands (gcloud, bq, gcloud storage, terraform state show / plan) that prove the key claims above. Never run apply or destroy, and never change IAM.

12. Questions for the team
    Every [unknown], phrased so I can ask it in Slack or standup, with the likely owner if the source hints at one.

## Mode and size rules
- MODE recap: start with "What changed": files touched, resources added/changed/removed, why each change was made, what it depends on, what depends on it, assumptions made, what's still pending, and how to roll back. Then cover sections 1-12 only as they relate to the change.
- DEPTH quick: sections 1, 2, 3, the access matrix from 4, and 12.
- FOCUS set: sections 1-2, then go deep on that one area only.
- Large folder: start with an inventory (path → one-line purpose). Go deep on the 20% that explains 80%, and list what you skipped.

## Close
End with 3-5 drill-down options specific to this target (e.g. "Trace the ingestion SA across projects", "Why this grant uses iam_binding"). Don't quiz me.
If I say "save": write the explanation to _notes/understanding/<target>.md and add new names and acronyms to _notes/glossary.md, so the decoder grows over time.
