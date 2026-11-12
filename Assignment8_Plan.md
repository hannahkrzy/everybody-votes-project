# Everybody Votes — Assignment 8
**CS 5001 Senior Design I | Hannah Krzywkowski and Aryan Balyan**

**Planning basis:** D0–D2 six-module Flask/Jinja2/SQLite MVP. Spring 2027 dates are planning assumptions pending CS 5002 calendar and advisor confirmation. Fall course milestone dates below are provisional except the dates before Nov 11. “Complete” means the corresponding design artifact was supplied, not verified instructor acceptance.

## 1. Task List

T01. Configure C6 Data Store schema, constraints, and test fixtures. Owner: Aryan.

T02. Develop C1 Web Interface base templates, responsive layout, and accessible forms. Owner: Hannah.

T03. Develop C2 Account & Access registration, login, sessions, and role checks. Owner: Aryan.

T04. Develop C3 Poll Management publication, scheduling, and featured-poll queries. Owner: Aryan.

T05. Develop C4 Vote & Prediction guest and registered vote transactions. Owner: Hannah.

T06. Develop C5 Results & Statistics counts, percentages, and pre-vote restrictions. Owner: Hannah.

T07. Develop C4 Vote & Prediction prediction recording and vote-status recovery. Owner: Hannah.

T08. Develop C3 Poll Management closed-poll archive and admin page integration. Owner: Aryan.

T09. Develop C5 Results & Statistics account history and privacy-safe demographic comparisons. Owner: Hannah.

T10. Validate C2 Account & Access and C6 Data Store security, configuration, and error handling. Owner: Aryan.

T11. Test C1 Web Interface and C4/C5 end-to-end voting, accessibility, and result flows. Owner: Hannah.

T12. Validate C2/C3/C6 integration, deployment fallback, and final demonstration setup. Owner: Aryan.

## 2. Milestones List

| ID | Target | Status | Checkable outcome | Verification evidence | Verifier |
|---|---|---|---|---|---|
| M01 | 2026-09-02 | Complete | Team formation and contract | Signed contract, roster and repository | Both teammates / instructor |
| M02 | 2026-09-09 | Complete | Problem framing and topic | Topic statement and team biographies | Both teammates / instructor |
| M03 | 2026-09-18 | Complete | Advisor and constraints | Advisor identification and constraints essay | Both teammates / instructor |
| M04 | 2026-09-28 | Complete | Requirements elicitation | User stories, use cases, acceptance criteria | Both teammates / instructor |
| M05 | 2026-10-02 | Complete | D0 architecture | Six-module diagram, interfaces and data flow | Both teammates / instructor |
| M06 | 2026-10-08 | Complete | D1 detailed design | Data model, algorithms, API contract | Both teammates / instructor |
| M07 | 2026-10-14 | Complete | D2 and design review | Finished C1–C6 design and peer review notes | Both teammates / instructor |
| M08 | 2026-10-28 | Complete | Assignment 8 project plan | Task List, milestones, timeline, effort matrix, WBS and risks | Both teammates / instructor |
| M09 | 2026-10-30 | Planned | Requirements and technical specification | Requirements traceability and technical specifications | Both teammates / instructor |
| M10 | 2026-11-04 | Planned | Test and validation plan | Test cases and verification approach | Both teammates / instructor |
| M11 | 2026-11-11 | Planned | Ethics and constraints review | Ethics/privacy and constraint review document | Both teammates / instructor |
| M12 | 2026-11-25 | Planned | Technical communication | Project communication materials | Both teammates / instructor |
| M13 | 2026-12-02 | Planned | Oral presentation I | Slides and presentation rehearsal | Both teammates / instructor |
| M14 | 2026-12-09 | Planned | Week 14 final course report | Report, updated plan and presentation materials | Both teammates / instructor |
| M15 | 2027-01-22 | Planned | Database and interface foundation | Schema constraints pass and accessible template renders | Aryan (database), Hannah (UI) |
| M16 | 2027-02-05 | Planned | Account access ready | Registration/login/role tests pass | Aryan; Hannah cross-checks |
| M17 | 2027-02-19 | Planned | Poll creation and voting ready | Admin creates poll; guest/account votes stored once | Aryan (polls), Hannah (votes) |
| M18 | 2027-03-05 | Planned | Results and archive ready | Results permissions/counts and archive tests pass | Hannah (results), Aryan (archive) |
| M19 | 2027-03-19 | Planned | Predictions and security ready | Prediction boundaries, recovery and security tests pass | Hannah (predictions), Aryan (security) |
| M20 | 2027-04-02 | Planned | Complete MVP feature set | History and demographic four/five threshold tests pass | Hannah; Aryan cross-checks |
| M21 | 2027-04-16 | Planned | Integration and test completion | End-to-end, accessibility and fallback tests pass | Both teammates |
| M22 | 2027-04-30 | Planned | Demonstration-ready release | Live local demo, test evidence, final docs | Both teammates / advisor |

## 3. Table 1 — Timeline
Each two-week task window includes two reserved working days (20% of its 10 working days) for testing, rework, and verification. These days are listed in the Task Buffer column and are protected from new feature development. The final two working days of each task window are protected for rework and milestone verification, with no new feature development scheduled. These periods provide the planned 20% task buffers and milestone preparation time. They are not additional days beyond the scheduled task windows. Milestone M22 has a separate protected stabilization period from April 19–30, with no new features scheduled. Winter break (December 10–January 10) is excluded from implementation. Exam and co-op conflicts will be monitored weekly and addressed through the reserved buffers or task reassignment. Spring dates will be confirmed against the CS 5002 calendar.

| ID | Task statement | Owner | Start | Finish | Dependencies | Task buffer (within dates) |
|---|---|---|---|---|---|---|
| T01 | Configure C6 Data Store schema, constraints, and test fixtures. | Aryan | 2027-01-11 | 2027-01-22 | None | 2 days (20%): Jan 21–Jan 22 |
| T02 | Develop C1 Web Interface base templates, responsive layout, and accessible forms. | Hannah | 2027-01-11 | 2027-01-22 | None | 2 days (20%): Jan 21–Jan 22 |
| T03 | Develop C2 Account & Access registration, login, sessions, and role checks. | Aryan | 2027-01-25 | 2027-02-05 | T01 | 2 days (20%): Feb 04–Feb 05 |
| T04 | Develop C3 Poll Management publication, scheduling, and featured-poll queries. | Aryan | 2027-02-08 | 2027-02-19 | T01, T03 | 2 days (20%): Feb 18–Feb 19 |
| T05 | Develop C4 Vote & Prediction guest and registered vote transactions. | Hannah | 2027-02-08 | 2027-02-19 | T01, T03 | 2 days (20%): Feb 18–Feb 19 |
| T06 | Develop C5 Results & Statistics counts, percentages, and pre-vote restrictions. | Hannah | 2027-02-22 | 2027-03-05 | T05 | 2 days (20%): Mar 04–Mar 05 |
| T07 | Develop C4 Vote & Prediction prediction recording and vote-status recovery. | Hannah | 2027-03-08 | 2027-03-19 | T05, T06 | 2 days (20%): Mar 18–Mar 19 |
| T08 | Develop C3 Poll Management closed-poll archive and admin page integration. | Aryan | 2027-02-22 | 2027-03-05 | T02, T04 | 2 days (20%): Mar 04–Mar 05 |
| T09 | Develop C5 Results & Statistics account history and privacy-safe demographic comparisons. | Hannah | 2027-03-22 | 2027-04-02 | T03, T06, T07 | 2 days (20%): Apr 01–Apr 02 |
| T10 | Validate C2 Account & Access and C6 Data Store security, configuration, and error handling. | Aryan | 2027-03-08 | 2027-03-19 | T03, T04, T05 | 2 days (20%): Mar 18–Mar 19 |
| T11 | Test C1 Web Interface and C4/C5 end-to-end voting, accessibility, and result flows. | Hannah | 2027-04-05 | 2027-04-16 | T02, T05–T09, T10 | 2 days (20%): Apr 15–Apr 16 |
| T12 | Validate C2/C3/C6 integration, deployment fallback, and final demonstration setup. | Aryan | 2027-04-05 | 2027-04-16 | T04, T08–T10 | 2 days (20%): Apr 15–Apr 16 |

**Milestone rows (planned completion dates):**

| Milestone | Date |
|---|---|
| M01 — Team formation and contract | 2026-09-02 |
| M02 — Problem framing and topic | 2026-09-09 |
| M03 — Advisor and constraints | 2026-09-18 |
| M04 — Requirements elicitation | 2026-09-28 |
| M05 — D0 architecture | 2026-10-02 |
| M06 — D1 detailed design | 2026-10-08 |
| M07 — D2 and design review | 2026-10-14 |
| M08 — Assignment 8 project plan | 2026-10-28 |
| M09 — Requirements and technical specification | 2026-10-30 |
| M10 — Test and validation plan | 2026-11-04 |
| M11 — Ethics and constraints review | 2026-11-11 |
| M12 — Technical communication | 2026-11-25 |
| M13 — Oral presentation I | 2026-12-02 |
| M14 — Week 14 final course report | 2026-12-09 |
| M15 — Database and interface foundation | 2027-01-22 |
| M16 — Account access ready | 2027-02-05 |
| M17 — Poll creation and voting ready | 2027-02-19 |
| M18 — Results and archive ready | 2027-03-05 |
| M19 — Predictions and security ready | 2027-03-19 |
| M20 — Complete MVP feature set | 2027-04-02 |
| M21 — Integration and test completion | 2027-04-16 |
| M22 — Demonstration-ready release | 2027-04-30 |

**Primary critical path:** T01 → T03 → T05 → T06 → T07 → T09 → T11 → M21 → M22. Parallel T04 → T08 → T12 and T10 → T11/T12 are also required for M21; delays there can become critical if their available slack is used. The Apr 19–30 protected stabilization block precedes M22; high-impact delays trigger a feature freeze and use of this block. Milestone verification is reserved on Jan 21–22 (M15), Feb 4–5 (M16), Feb 18–19 (M17), Mar 4–5 (M18), Mar 18–19 (M19), Apr 1–2 (M20), and Apr 15–16 (M21). These are not extra days beyond the task buffers.

## 4. Table 2 — Effort Matrix
Hours include development, review, tests and task-level rework. **P** marks primary owner.

| Task | Hannah (hours) | Aryan (hours) | Total |
|---|---:|---:|---:|
| T01 | 4 | 24 P | 28 |
| T02 | 24 P | 4 | 28 |
| T03 | 6 | 26 P | 32 |
| T04 | 6 | 26 P | 32 |
| T05 | 27 P | 6 | 33 |
| T06 | 25 P | 5 | 30 |
| T07 | 25 P | 5 | 30 |
| T08 | 5 | 23 P | 28 |
| T09 | 27 P | 6 | 33 |
| T10 | 6 | 24 P | 30 |
| T11 | 26 P | 8 | 34 |
| T12 | 8 | 26 P | 34 |
| **Total** | **189** | **183** | **372** |

**Balance:** Hannah 189 hours (50.8%), Aryan 183 hours (49.2%). This near-even split leaves primary ownership clear. Review actual hours weekly; if the gap exceeds 10%, transfer test-writing, documentation or bug-fix support without changing primary accountability.

## 5. Work Breakdown Structure

1. Foundation (T01 C6, T02 C1, T03 C2)
2. Polling core (T04 C3, T05 C4, T06 C5, T08 C3)
3. User features and protections (T07 C4, T09 C5, T10 C2/C6)
4. Integration and release (T11 C1/C4/C5, T12 C2/C3/C6)

## 6. Risk Register

| ID | Category | Risk | Likelihood | Impact | Strategy | Mitigation / timeline allowance | Owner | Trigger |
|---|---|---|---|---|---|---|---|---|
| R1 | Technical | Vote duplicates or SQLite lock failures | Medium | High | Reduce | T05/T10 tests; 15–20% task buffer; Apr stabilization | Hannah | Concurrent vote test fails |
| R2 | Technical | Authentication or CSRF flaw | Medium | High | Reduce | T03/T10 permission and CSRF tests; freeze before M21 | Aryan | Unauthorized action succeeds |
| R3 | Technical | Demographic privacy leakage | Medium | High | Avoid | T09 four-vs-five suppression tests; T11 review | Hannah | Any group of 1–4 appears |
| R4 | Team | Exam, co-op or workload conflict | High | High | Reduce | Winter break excluded; weekly check-ins; Apr 19–30 protected block | Both | Task slips over two working days |
| R5 | External | Public hosting requirement or free-tier limitation | Medium | High | Reduce | Confirm hosting requirements with instructor before Spring 2027 implementation; retain local $0 Flask/SQLite demo fallback in T12 | Aryan | Public URL required or host storage fails |
| R6 | Technical | Integration mismatch across modules | Medium | High | Reduce | Shared interfaces; T11/T12 integration and final protected block | Both | End-to-end flow fails |
| R7 | Team | AI-assisted code adds unverified dependencies | Medium | Medium | Reduce | Check package registry and review code in T10/T11 | Aryan | Unknown dependency proposed |

**Scope exclusions:** user-submitted poll suggestions, account deletion, password-reset email, automatic recurring rotation, external notifications, AI, advanced search, public hosting unless required. No extra deployed services.

**Acceptance:** each milestone requires demonstrable evidence; no future implementation work is represented as already completed.
