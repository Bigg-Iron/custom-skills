---
name: visual-verify-loop
description: Capture photo or video evidence of a feature or bug fix, compare it against the original task, iterate until complete or blocked, then write a short markdown summary. Use when verifying UI changes, demoing a fix, validating acceptance criteria visually, or when the user asks for screenshot/video proof of completed work.
---

# Visual Verify Loop

Verify features and bug fixes with visual evidence, compare against the original task, iterate until done, then document the outcome.

## When to use

- New UI feature shipped and needs visual proof
- Bug fix must be demonstrated before/after or in the fixed state
- User asks for screenshots, screen recording, or demo video
- Acceptance criteria are visual (layout, animation, interaction)

Skip when the change is backend-only with no observable UI and no user-facing behavior to demo.

## Inputs to capture first

Before capturing anything, restate:

1. **Original task** — copy or summarize the user request / issue / acceptance criteria
2. **Expected behavior** — what should be visible or happen
3. **Scope** — pages, flows, or states to exercise
4. **Capture type** — photo for static UI; video for flows, animations, or multi-step interactions

## Workflow

Copy this checklist and track progress:

```
Visual Verify Progress:
- [ ] Step 1: Restate task and expected behavior
- [ ] Step 2: Prepare environment (build, seed data, login)
- [ ] Step 3: Capture evidence (photo and/or video)
- [ ] Step 4: Compare evidence to original task
- [ ] Step 5: Decide — complete, improve, or blocked
- [ ] Step 6: Write summary markdown
```

### Step 1: Restate task and expected behavior

Write a short checklist from the original request:

```markdown
## Verification criteria
- [ ] Criterion 1 (from original task)
- [ ] Criterion 2
- [ ] Criterion 3
```

Every criterion must be observable in a screenshot or recording.

### Step 2: Prepare environment

1. Start the app or open the target URL (dev server, preview, or production as appropriate).
2. Seed or navigate to the state needed to exercise the change.
3. Confirm the build includes the change under test.

Use the `computerUse` subagent when browser interaction is required. Do not claim verification without actually running the app.

### Step 3: Capture evidence

**Photos (static UI, layout, single state)**

- Take screenshots at the relevant viewport(s).
- Name files descriptively: `feature-name-state.png`, `bugfix-after.png`.
- Save under `/opt/cursor/artifacts/screenshots/` when possible.

**Video (flows, animations, interactions)**

1. Start recording: `RecordScreen` with `mode: START_RECORDING`.
2. Perform the full user flow once, slowly and deliberately.
3. Stop and save: `RecordScreen` with `mode: SAVE_RECORDING` and a descriptive `save_as_filename` (no extension; saved as `.mp4` under artifacts).

Prefer video when any criterion involves motion, transitions, drag-and-drop, or multi-step navigation. Prefer photos when a single frame proves the outcome.

Capture **before** evidence only when comparing a bug fix and a prior state is available or reproducible.

### Step 4: Compare evidence to original task

Review each criterion against the captured media:

| Criterion | Pass / Fail | Evidence |
|-----------|-------------|----------|
| ...       | Pass        | screenshot or timestamp in video |

For video, note timestamps (e.g. `0:12 — modal opens correctly`).

Be strict: partial matches are **Fail** unless the original task explicitly allowed them.

### Step 5: Decide and loop

**Complete** — all criteria pass → go to Step 6.

**Needs improvement** — one or more criteria fail →

1. List concrete gaps (what is wrong vs what was requested).
2. Implement fixes (minimal scope; do not expand the task).
3. Return to Step 2.

**Blocked** — cannot verify (env broken, missing credentials, feature not reachable) →

1. Document the blocker.
2. Write the summary with status `blocked` and skip further loops.

**Loop limits:** Stop after **3** full verify cycles. If still failing, report remaining gaps and recommend next steps instead of looping indefinitely.

### Step 6: Write summary markdown

Write a short summary file:

- **Default path:** `/opt/cursor/artifacts/verification-summary.md`
- **Project alternative:** `docs/verification/<feature-or-fix-slug>.md` when the repo expects docs in-tree

Use [summary-template.md](summary-template.md). Keep the summary under **80 lines** — facts and links, not narrative.

## Summary quality bar

The summary must answer:

1. What was requested?
2. What was verified (with media links/paths)?
3. Pass or fail per criterion?
4. Final status: **complete**, **needs follow-up**, or **blocked**?
5. If incomplete, what specifically still fails?

## Presenting results to the user

When reporting back:

1. Link or embed the summary markdown path.
2. Reference screenshot paths or the demo video.
3. State final status in one sentence.
4. If looping occurred, note how many iterations and what changed.

Do not mark the task complete in chat unless Step 5 decision is **Complete**.

## Tool selection

| Situation | Tool |
|-----------|------|
| Browser/GUI manual test | `computerUse` subagent |
| Screen recording | `RecordScreen` |
| Video analysis of recording | `videoReview` subagent (use `recording_demo.mp4`) |
| Backend-only change | Skip this skill; use tests or logs instead |

## Anti-patterns

- Claiming verification without captured media
- Screenshots of code instead of the running UI
- Declaring success when criteria were never written down
- Infinite fix loops without documenting remaining gaps
- Summary longer than the evidence warrants

## Additional resources

- Summary format: [summary-template.md](summary-template.md)
- Example walkthrough: [examples.md](examples.md)
