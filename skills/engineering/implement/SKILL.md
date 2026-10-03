---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Use /tdd for behaviour the work adds or changes, at pre-agreed seams. Work that removes code deletes the tests of what it removed and adds none.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, review the work with the project's `review` skill when the project has one (a `review` skill in its `.claude/skills/` or `.agents/skills/`), and with /code-review otherwise.

Commit your work to the current branch.
