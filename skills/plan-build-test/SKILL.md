---
name: plan-build-test
description: Plan-first feature delivery with an independent test loop. Writes an implementation plan to a markdown file, stops for user review, builds the feature only after approval, then hands the plan to a separate test-writer subagent that writes and runs tests against the plan's acceptance criteria. The main agent fixes failures and re-tests until everything passes or it is genuinely blocked. Use when the user asks to "plan and build", "plan then implement", "build this feature with tests", or wants a reviewed plan before any code is written.
---

# Plan → Review → Build → Test loop

You are the **head agent**. You own the plan, the implementation, and the final
report. A separate **tester subagent** owns the tests. That separation is the
point of this skill: tests are written from the plan, not from your code, so they
check what was asked for rather than what you happened to build.

Work through the phases in order. Never skip the review gate.

```
1. Plan ──► 2. Review gate ──► 3. Build ──► 4. Test (subagent) ──► 5. Fix loop ──► 6. Report
                 ▲    │                                 ▲                │
                 └────┘ changes requested               └────────────────┘ failures remain
```

---

## Phase 1 — Write the plan

1. Understand the request. Read the parts of the codebase the feature touches:
   entry points, neighbouring modules, existing tests, the test framework and how
   tests are run (`package.json` scripts, `pytest.ini`, `Makefile`, CI config).
2. If something is genuinely ambiguous and the answer changes the design, ask the
   user now (one batched question). Otherwise choose a sensible default and record
   it under **Assumptions** in the plan.
3. Save the plan as markdown using the template in
   [references/plan-template.md](references/plan-template.md).
   - Location: `docs/plans/<YYYY-MM-DD>-<feature-slug>.md` unless the user or the
     repo already has a convention (check for an existing `plans/` or `docs/`
     folder first).
   - Set `Status: Draft`.
4. The **Acceptance criteria** section is the contract the tester works from.
   Each criterion must:
   - have a stable ID (`AC-1`, `AC-2`, …),
   - be observable from outside the implementation (inputs → outputs, behaviour,
     error cases), and
   - name the public interface it is checked through (function signature, CLI
     command, HTTP route, component props).

   If a criterion cannot be tested, rewrite it until it can, or move it to
   **Out of scope / not automatically testable**.

## Phase 2 — Review gate (mandatory)

1. Give the user the plan path and a short summary: goal, files to touch, the
   acceptance criteria list, and any assumptions.
2. Ask for a decision with `AskUserQuestion`:
   - **Approve** — build it as written
   - **Request changes** — the user describes what to change
   - **Cancel** — stop here
3. On **Request changes**: edit the plan, add a line to its **Revision log**,
   and ask again. Repeat until approved or cancelled.
4. On **Approve**: set `Status: Approved` in the plan file.
5. On **Cancel**: set `Status: Cancelled`, report, and stop.

**Do not write or modify any implementation code before approval.** Reading code
is fine.

## Phase 3 — Build

1. Set `Status: In progress`.
2. Implement the plan step by step. Track the steps with the task list if you
   have one.
3. Stay inside the plan. If you discover the plan is wrong or incomplete in a
   way that changes scope, the public interface, or an acceptance criterion,
   **stop and go back to the user** with the proposed change. Small internal
   details (helper names, private structure) don't need re-approval; note them in
   the plan's **Implementation notes**.
4. Run the project's existing fast checks (build, typecheck, lint, existing
   tests) and fix anything you broke.
5. Fill in the plan's **Implementation notes**: files changed, the actual public
   interfaces (exact signatures, routes, commands), and how to run the code.
   The tester relies on this section.

Do **not** write the acceptance tests yourself. Unit tests you need while
building are fine, but the acceptance suite belongs to the tester.

## Phase 4 — Hand off to the tester subagent

Spawn one tester with the `Agent` tool (`subagent_type: general-purpose`,
`run_in_background: false`, since the next step depends on its result). Build
the prompt from [references/tester-prompt.md](references/tester-prompt.md),
filling in the plan path, test command, and test location.

Keep the agent's ID or name from the result. Later rounds go to the **same**
agent with `SendMessage` (load it with `ToolSearch` → `select:SendMessage` if it
isn't loaded yet) so it keeps its context. Spawn a new tester only if the
original can't be reached.

The tester returns a report in the fixed format defined in the prompt
reference: a per-criterion result table, details for each failure, and a final
`VERDICT:` line.

## Phase 5 — Fix loop

Repeat until the verdict is `ALL_PASS` or you are blocked.

For each failing criterion, classify it before you change anything:

| Cause | What to do |
|---|---|
| **Implementation bug**: code doesn't meet the criterion | Fix the implementation. |
| **Test bug**: test asserts something the plan doesn't say, or is wired up wrong | Tell the tester exactly what is wrong and cite the plan text. The tester fixes its own test; **you do not edit the acceptance tests.** |
| **Plan gap**: the criterion is ambiguous, or the plan and reality conflict | Ask the user. Update the plan and its revision log, then tell the tester which criteria changed. |
| **Environment**: missing dependency, service, or credential | Fix it if it is in scope (e.g. install a dev dependency). Otherwise you are blocked. |

After each round of fixes:

1. Re-run the project's fast checks yourself.
2. `SendMessage` to the tester with what changed (files, criteria affected, any
   test corrections you want) and ask it to re-run the **full** acceptance
   suite, not just the failures.
3. Read the new report and append one row to the plan's **Test log**:
   iteration number, pass/fail counts, what you changed.

### Rules for the loop

- **Never weaken a test to get green.** You may not delete, skip, or loosen an
  acceptance test, or ask the tester to, unless the plan says that behaviour
  isn't required. If the plan does say it is required, the code has to change.
- **No speculative fixes.** Find the root cause from the failure output before
  you change code. If the cause is unclear, add diagnostics or reproduce it
  first.
- **Stop when you are blocked.** You are blocked if any of these holds:
  - the same criterion has failed **3 iterations in a row** despite different
    fixes,
  - the loop has run **6 iterations** in total,
  - a fix needs a scope change or a decision only the user can make,
  - an environment problem is outside your control.

  When blocked, stop the loop and report. Don't keep going in circles.

## Phase 6 — Final report

1. Update the plan file:
   - `Status: Done` if every criterion passes, otherwise `Status: Blocked`.
   - Make sure the **Test log** and **Implementation notes** are complete.
2. Tell the user:
   - the verdict and final pass/fail per acceptance criterion,
   - the number of iterations and the main fixes made,
   - where the plan and the tests live and how to run the tests,
   - if blocked: which criteria are still failing, why, what you tried, and the
     specific decision or action you need from them.

Report faithfully. If tests are failing, say so and include the failure output.
Don't describe a blocked run as done.
