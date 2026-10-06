---
applyTo: "**/*.tf,**/*.tfvars"
---
- We are the platform team. We own our child modules only.
- Cross-team modules are referenced, not owned. Never edit them or suggest changes inside them. Treat them as read-only.
- Module refs are pinned by ?ref=vX.Y. Never change a pin unless I say so.
- When a referenced module's inputs are unclear, ask. Do not guess its variables.
- Never rename resources or modules without a moved block.
- Do not run terraform plan or apply unless I ask.
