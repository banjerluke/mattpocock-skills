---
"mattpocock-skills": patch
---

`to-tickets`: tickets are now self-contained. Each one copies forward, verbatim, the spec's Implementation and Testing Decisions tagged with its behaviour numbers under "Decisions that bind this ticket", and the spec's Believed Context entries under "Believed context". The session that picks a ticket up never has to fetch the parent spec, which was costing a full spec read per ticket and left the orchestrator hand-writing the same excerpts into every prompt.
