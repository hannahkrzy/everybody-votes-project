# Everybody Votes — Peer Review Notes
**CS 5001 — Assignment 7**  
**Team:** Hannah Krzywkowski and Aryan Balyan  

## Review setting
This was an in-class peer review where teams reviewed D0, D1, and the draft D2 designs.

| ID | Criterion | Issue and reviewing team | Concrete action | Owner | Priority | Proposed due date | Draft disposition |
|---|---|---|---|---|---|---|---|
| PR-01 | Completeness | Team TBD: Account creation and role enforcement were only briefly defined in D1. | Add C2 fields, registration/login flow and `require_admin` contract. | Aryan | must-fix | Oct 18, 2026 | Documented in revised D2; implementation not yet verified |
| PR-02 | Feasibility | Team TBD: Automatic poll scheduling might require a background task. | Derive open/closed status from UTC timestamps at request time; show state diagram. | Aryan | must-fix | Oct 18, 2026 | Documented in revised D2; implementation not yet verified |
| PR-03 | Risk | Team TBD: A network timeout could leave voters unsure whether a vote was stored. | Show uncertain-write path and status check before retry; reference E16 and E41. | Hannah | must-fix | Oct 18, 2026 | Documented in revised D2; implementation not yet verified |
| PR-04 | Clarity | Team TBD: I2 publication call had many fields and could be hard to follow. | Define `PollDraft` input record and response errors. | Aryan | worth considering | Oct 18, 2026 | Documented in revised D2; implementation not yet verified |
| PR-05 | Risk | Team DevBits: The demographic privacy rule needs a clear boundary test for groups with four versus five responses. | Document the all-or-nothing suppression rule and add a boundary test for groups with four and five responses. | Hannah + Aryan | must-fix | Oct 19, 2026 | Rule documented in D1, D2, and User Stories AC-03.3; boundary test still planned |
| PR-06 | Feasibility | Team DevBits: Free public hosting with persistent SQLite is not yet established. | Ask the course instructor whether a public URL is required; keep the local $0 demo fallback. | Aryan | must-fix | Oct 19, 2026 | **Open G-02** |
| PR-07 | Completeness | Team DevBits: Error table needed consistent coverage of supporting services. | Add config, logs, authentication and notification edge cases for all four categories. | Hannah | must-fix | Oct 19, 2026 | Documented in revised D2; implementation not yet verified |
| PR-08 | Clarity | Team DevBits: Admins might think 'featured' guarantees homepage display. | State that only three currently open featured polls appear and document sort order. | Aryan | worth considering | Oct 19, 2026 | Documented in revised D2; implementation not yet verified |
| PR-09 | Feasibility | Team DevBits: A password-reset email flow could grow scope. | Explicitly exclude password-reset email from the minimum version; use administrator-created replacement demo accounts if needed. | Aryan | out of scope for now | Oct 19, 2026 | Excluded from MVP |

## Feedback summary
**Completeness** drew two issues, **clarity** drew two, **feasibility** drew three, and **risk** drew two; feasibility received the most feedback. The proposed PR-05 documentation fix is reflected in the referenced design drafts; its four-versus-five boundary test remains planned. PR-06 (public hosting requirement) is still pending course instructor confirmation. Most proposed fixes require documentation and ordinary Flask code, not additional infrastructure.

## Issues we raised when reviewing other teams
1. **Concurrency / risk:** "Your duplicate-submission check happens before insertion. What happens if two requests arrive simultaneously? Consider a database uniqueness constraint and a test with concurrent requests." 
2. **Completeness / clarity:** "Your flow diagram only shows success. Could you add the timeout and invalid-input paths, including what the user sees and whether retry is safe?"

## Additional review checks prompted by the D2 revision
- **Prediction boundaries (risk; Hannah; must-fix; proposed Oct 16):** TBD asks for explicit before-vote, duplicate, and after-close cases. D2 now documents E43–E45; tests are still planned.
- **History authorization (security; Aryan; must-fix; proposed Oct 16):** TBD asks how another user's history is protected. D2 now specifies session-derived ownership and E46; tests are still planned.
- **Session expiry and account disabling (completeness; Aryan; worth considering; proposed Oct 14):** D2 now documents E47–E48. No implementation claim is made.

## Required confirmation before submission
- AC-03.3 was checked in `User_Stories.md`; verify the planned privacy boundary test during implementation. Confirm with the course instructor whether a public URL is required for the final demonstration.
