# Everybody Votes — Requirements Document

**CS 5001 Senior Design I · Assignment 9**  
**Team:** Hannah Krzywkowski and Aryan Balyan  

## 1. Version and change log

| Version | Date | Change | Author |
|---|---|---|---|
| 1.0 draft | 2026-11-04 | Consolidated user stories, D0–D2, constraints, and project plan; assigned stable requirement IDs and proposed test IDs | Hannah Krzywkowski and Aryan Balyan (reviewed and approved by both team members) |

This draft is prepared after the October 30 deadline; the date above is the document preparation date, **not** a claim of on-time submission or advisor approval. IDs are permanent: withdrawn requirements will retain their IDs and be logged rather than renumbered.

## 2. Purpose and scope

Everybody Votes is a small, responsive polling website inspired by the Wii Everybody Votes Channel. Visitors answer scheduled, administrator-created questions and see aggregate results after voting. Registered users can also make predictions, save their voting history, and optionally compare responses by broad demographics. The goal is a working, privacy-conscious project that **two team members can build and test in Spring 2027**.

The **minimum working release (Must)** includes admin-created polls, guest and registered voting, accounts, results, and basic protection against duplicate submissions. **Should** features add predictions, history, an archive, and demographic comparisons after the core workflow works. The system is one application, not a collection of microservices. Local demonstration is the committed zero-cost fallback; public hosting is not yet confirmed.

## 3. Stakeholders and user goals

| Stakeholder | Goal | Source |
|---|---|---|
| Guest voter (primary) | Vote without an account and see permitted results | US-01, UC-01 |
| Registered voter (primary) | Vote, save history, and optionally predict and compare | User Stories; D0–D2 |
| Poll administrator (primary) | Publish official questions and set voting windows | US-03, UC-02 |
| Application maintainer (secondary) | Understand rejected submissions without viewing private votes | US-02 |
| Keyboard-only voter (overlooked) | Vote and read results without a mouse or color cues | US-04 |
| Privacy-conscious voter (overlooked) | Skip demographics and avoid small-group disclosure | US-05 |

## 4. Glossary

| Term | Meaning |
|---|---|
| Poll | One official question with 2–6 predefined answer choices and an opening and closing time |
| Choice | One answer available within a poll |
| Guest | Visitor who participates without creating an account |
| Registered user | Person with a username, email, password, and saved account |
| Administrator | Authorized account allowed to publish official polls |
| Featured poll | Poll marked for possible homepage display; at most three open featured polls are shown |
| Vote | One recorded answer to a poll |
| Prediction | A registered voter's guess about the winning choice after voting and before closing |
| Archive | List of polls whose closing times have passed |
| Demographic group | Registered votes sharing one optional broad age, gender, or region value |
| Session | Signed browser cookie used by Flask to recognize the current visitor/account |
| Suppression | Withholding an entire demographic breakdown if any nonempty group has fewer than five votes |
| MVP | Minimum viable product: the small, complete Must feature set |
| UTC | Coordinated Universal Time, used for stored poll times |
| MoSCoW | Must, Should, Could, and Won't priorities for this release |

## 5. Functional requirements

All conditions below are **proposed acceptance criteria**, not claims that the software has been implemented. “Source” names the originating story or design when the five sample user stories do not cover a feature.

| ID | Requirement | Priority | Source | Verification |
|---|---|---|---|---|
| REQ-01 | The system shall let an administrator publish a poll with **2–6 distinct nonempty choices**, a question, and a closing time later than its opening time; invalid drafts shall not be saved. | Must | US-03 | TC-001: valid/invalid poll creation |
| REQ-02 | The system shall show **at most three** currently open featured polls, based on server UTC time, and reject votes outside `opens_at <= now < closes_at`. | Must | US-03; D2 | TC-002: poll timing and featured limit |
| REQ-03 | The system shall accept **one vote per poll per current guest session** without registration and reject a second submission from that session. | Must | US-01 | TC-003: guest vote and duplicate |
| REQ-04 | The system shall register users with unique username/email and hashed passwords, allow login/logout, and permit **one vote per account per poll**. | Must | UC-01; D2 C2 | TC-004: account and duplicate |
| REQ-05 | The system shall return **counts and percentages for every choice**, including zero-vote choices, after a confirmed vote or poll closure; it shall hide live results from nonvoters. | Must | US-01, US-04 | TC-005: results and access |
| REQ-06 | The system shall allow **only administrators** to publish polls and shall prevent users from viewing another account's private history. | Must | US-03, US-05; D2 | TC-006: permissions |
| REQ-07 | The system shall let a registered user who already voted submit **at most one prediction** for that poll before it closes and determine whether it matched a winning choice after closing (ties count). | Should | D0–D2 | TC-007: prediction lifecycle |
| REQ-08 | The system shall show a registered user their own voting history and basic participation statistics, and list closed polls in an archive. | Should | D0–D2; plan T08–T09 | TC-008: history and archive |
| REQ-09 | The system shall accept blank optional age range, gender, and region fields and, for any single-dimension comparison, return **no demographic groups** if any nonempty group contains fewer than five votes. | Should | US-05; D1/D2 | TC-009: blank fields, 4/5 boundary |
| REQ-10 | The system shall display a readable error when a vote is invalid, duplicate, closed, or unconfirmed, and shall not report a successful vote before its database transaction commits. | Must | US-02; D2 | TC-010: error and uncertain write |

## 6. Non-functional requirements

The numerical targets below are **new proposed verification thresholds** for this formal specification. Earlier design documents intentionally did not promise response times or load levels; these targets require advisor approval and measurement in Spring 2027.

| ID | Requirement | Priority | Source | Verification |
|---|---|---|---|---|
| NFR-01 | The system shall return the homepage, poll page, and unfiltered results in **2 seconds or less at the 95th percentile** across **100 requests per page** on the local demo computer with **1,000 seeded votes** and one client. | Should | D0 performance discussion; Week 9 spec | TC-011: timed local requests |
| NFR-02 | The system shall store **0 plaintext passwords**, reject **100% of 10 tested unauthorized admin/history requests**, and reject **100% of 10 POST requests with missing/invalid CSRF tokens**. | Must | Constraints Essay—Security; D2 | TC-012: security checks |
| NFR-03 | The system shall allow **4 of 5 test participants** to find an open poll, select an answer, submit, and read results within **2 minutes**, using labeled controls and keyboard-only navigation in a separate check. | Should | US-04; Constraints Essay—Diversity | TC-013: usability session |
| NFR-04 | The system shall store **exactly one vote** for each identity/poll when **10 duplicate requests** are sent, and shall return a failure or unknown status rather than a false success if the database cannot confirm a write. | Must | D1/D2 concurrency; US-02 | TC-014: duplicates and storage outage |

## 7. MoSCoW summary

- **Must:** REQ-01, REQ-02, REQ-03, REQ-04, REQ-05, REQ-06, REQ-10, NFR-02, NFR-04. These form the smallest complete and protected polling workflow.
- **Should:** REQ-07, REQ-08, REQ-09, NFR-01, NFR-03. Implement after the core workflow; they remain in the Spring 2027 plan but can be deferred if needed.
- **Could:** Small visual improvements or optional JavaScript updates **only after** required tests pass; no new committed requirements.
- **Won't (this release):** User-submitted poll suggestions or user-published polls; password-reset email; account deletion; editing published polls; automatic recurring poll rotation; email/push notifications; AI features; advanced search; multi-dimension demographic filters; paid services; public deployment unless the instructor requires it.

## 8. Inherited constraints

These four IDs reflect the **four headings actually present** in the Week 3 Constraints Essay; no extra constraint category is presented as inherited.

| ID | Constraint | Consequence |
|---|---|---|
| CON-01 | **Economic:** $0 required development, testing, and demonstration budget. | Use free/open-source software and local demo fallback; no paid API, subscription, or expiring trial dependency. |
| CON-02 | **Ethical:** Neutral poll wording, accurate unweighted counts, and results hidden until voting. | Validate wording during admin review; count stored valid votes; gate live results. |
| CON-03 | **Security:** Protect accounts and votes; collect only optional broad demographics; suppress small groups. | Hash passwords, check permissions, avoid precise personal data, and suppress an entire demographic breakdown when any nonempty group has fewer than five votes. |
| CON-04 | **Diversity & Cultural:** Inclusive answer options and accessible participation. | Allow 2–6 choices, a suitable unsure/decline option when needed, readable labels, and keyboard operation. |

## 9. Assumptions and open questions

| ID | Assumption or question | Owner | Target date / status |
|---|---|---|---|
| OQ-01 | Does CS 5002 require a publicly reachable URL, or is a local live demo acceptable? | Aryan (ask instructor/advisor) | Before Spring implementation; **open** |
| OQ-02 | Approve the newly proposed 2-second performance and 4-of-5 usability targets, and confirm test hardware. | Advisor | Before Week 10 test plan is finalized; **open** |
| OQ-03 | Confirm Must/Should priorities and whether predictions, history, and comparisons fit the Spring schedule. | Advisor | Before implementation kickoff; **open** |
| OQ-04 | Confirm advisor review of both v1.0 drafts | Hannah (coordinate) | Date not yet provided; **pending** |

**Design references:** `User_Stories.md`; `Constraints_Essay.md`; `Design_Diagrams/D0_High_Level_Design.md`; `Design_Diagrams/D1_Detailed_Design.md`; `Design_Diagrams/D2_Detailed_Design.md`; `Tasklist.md`; `Assignment8_Plan.md`. Diagram images remain in `Design_Diagrams/` and are linked, not duplicated.
