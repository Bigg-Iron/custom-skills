# Examples

## Example 1: Bug fix — login button disabled state

**Original task:** Fix login button staying disabled after valid email is entered.

**Capture:** One screenshot after typing a valid email.

**Loop 1:** Fail — button still gray. Fix validation hook.  
**Loop 2:** Pass — button enabled. Write summary, status `complete`.

**Summary excerpt:**

```markdown
## Criteria results
| 1 | Login button enables when email is valid | Pass | login-enabled.png |

## Outcome
Fix verified; button enables correctly after valid email input.
```

---

## Example 2: New feature — animated onboarding carousel

**Original task:** Add three-slide onboarding with swipe and dot indicators.

**Capture:** Screen recording walking through all three slides and swiping back.

**Loop 1:** Fail — dots do not update on slide 2. Fix indicator state.  
**Loop 2:** Pass — full flow matches spec.

**Summary excerpt:**

```markdown
| Video | /opt/cursor/artifacts/onboarding-demo.mp4 | 0:00–0:08 full carousel |

## Outcome
All three slides, swipe, and dot indicators behave as specified.
```

---

## Example 3: Blocked — missing staging credentials

**Original task:** Verify admin dashboard chart renders.

**Outcome:** Could not log in to staging; no screenshot of authenticated view.

```markdown
**Status:** blocked

## Outcome
Verification blocked: staging admin credentials unavailable in this environment.
```
