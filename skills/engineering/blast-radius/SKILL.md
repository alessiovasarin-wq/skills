---
name: blast-radius
description: "Find what a change could break somewhere else before it ships, beyond the diff, and prove the one fact it's safe because of by running real code instead of writing it up. Use for 'blast radius of X', 'what could this break', reviewing a small diff you don't trust, or a brief that asserts something about existing code ('make X public', 'X already handles Y') before you design against that assertion."
---

# Blast radius

Find what a change breaks somewhere else, before it ships. Use for "blast radius of X", "what could this break", or reviewing a small diff you don't trust yet. Run it before design, not only before shipping, when a brief asserts something about existing code ("make endpoint X public"): the assertion is a hypothesis, and the consumer census is what decides the design.

`/code-review` judges the diff against standards and spec. Blast radius judges what the diff does to everything **outside** it. Run them as a pair; neither replaces the other.

Listing the callers is not the job. The agent can grep those in a second. The job is the breakage grep won't show you.

Read `CONTEXT.md` (if it exists) and the ADRs in the area you're touching first: a term or a recorded decision often names the consumer the code doesn't.

## Don't trust your own writeup

A blast-radius writeup that sounds right is worthless. It reads as convincing whether or not it's true. So don't hand back the writeup. Find the one or two facts the whole thing depends on and prove them by running code.

### How sure are you

For each fact the change's safety depends on, get it as far down this list as is cheap, and say where it stopped.

1. You said so. Worthless on its own.
2. You pointed at the line. A real `file:line`, or the library's own source.
3. You showed the bad case can't happen. You walked the failure step by step and it doesn't reach.
4. You ran it. A script or test that calls the real code and fails loud if you're wrong.
5. You reproduced it in the running app. If the repo has a project `verify` skill (see `/create-verification-skill`), drive the affected feature with it.

Any safety fact you can't get to step 4, say so. Don't write it up as settled. Step 4 is usually one small script that imports the same library the app ships and calls the exact function you're worried about.

## Steps

1. **Read the change.** The diff, the symbols it adds, changes, and deletes, and what it now does differently, including the part the diff doesn't spell out. Pull the history with `git log --follow -- <file>` and `git blame` on the touched lines; fetch linked issues through `docs/agents/issue-tracker.md` if it exists.
2. **Find the one fact it's safe because of.** Most changes that look risky are safe because of a single fact, like "this call only drops already-dead cache entries and does nothing else". Find that fact. If it holds, most risky cases are cleared at once. Spend your time here, not on a long list of maybes.
3. **Look where grep stops.** Read the source of the library you call, and check its pinned version and any local patch. Work out when things run: microtasks, unmount and teardown, event ordering. Follow what a symbol search misses: the JSON an API returns, a DB column, a wire format, another language reading the same bytes, a feature flag, an env var, a cron job, code three hops downstream.
4. **Be honest about each risk.** Give it a real chance of happening and a real cost if it does. Keep the risks you confirmed; list the ones you checked and cleared separately. Cite a real `file:line`; a search that finds nothing is still an answer; never make up a caller or an API.
5. **Prove the one fact.** Write a script or test that runs the real code, run it, and paste what happened (redact secrets). If you can't prove it cheaply, mark it unproven. Don't overstate. If there is no seam where a test could hold the fact, that is itself a finding: hand it to `/improve-codebase-architecture`.
6. **For a big or wide change**, split step 3 into parallel read-only sub-agents, one per consumer surface (API clients, storage, jobs, UI), each returning cited risks. Merge their answers yourself; don't trust a summary you haven't spot-checked.

## What to hand back

- **What it does.** What changed, including the part that isn't obvious.
- **The one fact it's safe because of.** State it, say which step you got it to, and show the proof. If you couldn't prove it, write unproven.
- **Risks.** Only the real ones. Each names how it breaks, the `file:line`, how likely and how bad, and how to check. Paste the proof for the ones that matter.
- **Cleared.** What you checked and why it's fine.
- **Before you merge.** The cheapest test or repro that catches the real bug, including the script you wrote. If it belongs in the suite, write it test-first with `/tdd`.

Use `CONTEXT.md` vocabulary, plain words, no filler (the `humanizer` skill applies). Cite real code, and strip anything private before it goes anywhere public.
