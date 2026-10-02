# Plan: <Feature name>

- **Status:** Draft <!-- Draft | Approved | In progress | Done | Blocked | Cancelled -->
- **Created:** <YYYY-MM-DD>
- **Request:** <one or two sentences restating what the user asked for>

## Goal

<What the feature does and why, in a short paragraph.>

## Context

<Relevant existing code: modules, patterns to follow, constraints. Link files
as `path/to/file.ext:line`.>

## Approach

<The design. Keep it short, and record any alternatives you rejected with a
line on why.>

## Steps

1. <Concrete change, including the file(s) it touches>
2. <...>

## Public interface

<Exact signatures, CLI commands, routes, events, or component props that
callers (and the tester) will use. If the interface isn't final yet, write
what you intend and confirm it under Implementation notes.>

## Acceptance criteria

Each criterion is testable from outside the implementation.

| ID | Criterion | Checked through |
|---|---|---|
| AC-1 | Given <input/state>, when <action>, then <observable result>. | `<function / command / route>` |
| AC-2 | <Error case>: given <bad input>, <action> fails with <specific error / status / message>. | `<...>` |
| AC-3 | <Edge case> | `<...>` |

### Out of scope / not automatically testable

- <Item, and how it will be checked instead (manual check, follow-up)>

## Testing

- **Framework:** <e.g. pytest, vitest, go test>
- **Run command:** `<e.g. npm test -- tests/acceptance/feature-x>`
- **Test location:** `<e.g. tests/acceptance/test_feature_x.py>`

## Assumptions

- <Default you picked where the request was silent>

## Risks / open questions

- <...>

## Revision log

| Date | Change | Reason |
|---|---|---|
| <YYYY-MM-DD> | Initial draft | — |

---

<!-- Everything below is filled in after approval. -->

## Implementation notes

- **Files changed:** <list>
- **Final public interface:** <exact signatures / commands, as built>
- **How to run:** <...>
- **Deviations from plan:** <none, or what changed and why>

## Test log

| Iteration | Passed | Failed | Changes made |
|---|---|---|---|
| 1 | | | Initial implementation |
