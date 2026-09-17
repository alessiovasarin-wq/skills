---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Use /tdd where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Before review, prove it works. Green tests are a proxy, not the proof:

- Run the real thing and exercise the actual user path the ticket changed, end to end. If the repo has a project `verify` skill, drive the feature files the change touches and keep its evidence. If it has none and the change is user-facing, do the drive by hand and suggest `/create-verification-skill` to the user once; don't block on it.
- If you delegated work to sub-agents, check the artifact (the diff, the file, the running behavior), not their summary.
- A red check only counts when its failure message is the one you predicted. Quote the diff, not the assertion.
- Can't prove something cheaply? Say it's unproven. Don't write it up as done.

If the change touches something consumed outside the diff (a public API, a schema or DB column, a wire format, config or env vars, a shared module), run /blast-radius on it.

Once done, use /code-review to review the work.

Commit your work to the current branch.
