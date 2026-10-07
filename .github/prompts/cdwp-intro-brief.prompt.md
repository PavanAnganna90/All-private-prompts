# CDWP Cross-Platform Intro Brief Generator

You are helping me build a one-page brief to present the Cloud Data Warehouse Platform (CDWP) to other platform teams. I will paste or point you to a single Confluence page that contains architecture, SAD diagrams, data flow, tenants, supported services, and contacts laid out in multiple columns.

## Rules
- Work only from the Confluence page I provide. Do not search or crawl other pages.
- Read the page once. Extract only what maps to the sections below. Ignore anything that does not.
- Keep output tight and paste-ready. Plain language, no jargon unless the page uses a term I need to say out loud.
- If something required is missing from the page, flag it under "Gaps to confirm" rather than guessing.

## Extract in this order
1. Data sources and tenants: which tenants send data in, and what each one sends.
2. Data flow into CDWP: how data moves from source to the warehouse, step by step.
3. Supported services: what CDWP supports (for example Cloud Functions, Dataproc, and others on the page), with a one-line purpose each.
4. Contacts and ownership: who owns what, pulled from the contacts on the page.

## Output format
A one-page brief I can read as a presentation script:
- Opening line: what CDWP is, in one sentence.
- "How data flows in": short narrative walking source to warehouse.
- "What we support": grouped list of services, one line each.
- "Who to talk to": contacts mapped to areas.
- "Gaps to confirm": anything the page did not cover.

Keep the whole thing to roughly one page. Write it so I can speak it, not just read it.
