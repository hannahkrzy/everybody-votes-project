# Everybody Votes — Test and Validation Plan Draft

**CS 5001 Senior Design I — Assignment 10**

**Team members:** Hannah Krzywkowski and Aryan Balyan

**Test lead:** Hannah Krzywkowski; component test ownership follows D0 and Assignment 8.

**Version:** 1.0 draft | **Planning date:** November 2026 | **Execution:** Spring 2027 (CS 5002)

**Planning note:** This is a proposed plan, not a record of executed tests. The two team members have confirmed the project plan and proposed test targets; any required advisor/instructor approval remains separate. The project is designed in Fall 2026 and built in Spring 2027 by two students, using a single Flask/Jinja2/SQLite application.

## 1. Scope of Testing

The Must release is tested first. Should features are tested if implemented after the Must workflow is stable. The six logical components are C1 Web Interface (Hannah), C2 Account & Access (Aryan), C3 Poll Management (Aryan), C4 Vote & Prediction (Hannah), C5 Results & Statistics (Hannah), and C6 SQLite Data Store (Aryan).
| Requirement IDs | Priority | Components | In-scope behavior |
|---|---|---|---|
| REQ-01 | Must | C2/C3/C6 | Admin poll publication, 2–6 choices, valid times |
| REQ-02 | Must | C3/C4 | UTC lifecycle and maximum three open featured polls |
| REQ-03 | Must | C2/C4/C6 | Guest session vote and duplicate rejection |
| REQ-04 | Must | C2/C4/C6 | Registration, login/logout, account vote uniqueness |
| REQ-05 | Must | C5/C6 | Gated results, counts, percentages and zero counts |
| REQ-06 | Must | C2/C3/C5 | Admin role and private history authorization |
| REQ-10 | Must | C1/C4/C6 | Safe errors and uncertain vote writes |
| NFR-02 | Must | C2/C6 | Hashing, authorization and CSRF |
| NFR-04 | Must | C4/C6 | Concurrent duplicates and storage failure |
| REQ-07 | Should | C4/C5/C6 | Predictions and winner/tie evaluation |
| REQ-08 | Should | C3/C5 | History, participation statistics and archive |
| REQ-09 | Should | C2/C5/C6 | Optional demographics and all-or-nothing suppression |
| NFR-01 | Should | C1/C3/C5/C6 | Local request latency |
| NFR-03 | Should | C1 | Usability and keyboard access |

**Out of scope:** Could-only cosmetic polish and optional JavaScript; Won’t items (user-submitted/published polls, password-reset email, account deletion, editing published polls, recurring rotation, notifications, AI features, advanced search, multi-dimension filters, paid services); external public deployment unless required by the instructor. The test plan does not re-test Flask, Jinja2, SQLite, Werkzeug, or Flask-WTF internals; it tests our integration and configuration. Guest uniqueness is limited to a retained browser session, not a unique human across browsers. No hardware, sensors, or machine learning are used.

## 2. Verification and Validation Strategy

**Verification:** Hannah and Aryan run repeatable tests against REQ-01–REQ-10, NFR-01–NFR-04, the Week 9 technical specification, and D2 error paths. Component owners write and run their tests; the other teammate reviews high-risk cases. Database constraints, permissions, CSRF, time boundaries, and vote integrity are verified before UI polish. Automated Python tests use Flask’s test client and a disposable SQLite test database; browser checks cover the actual forms.

**Validation:** Five volunteer users (including a keyboard-only trial) attempt the basic poll workflow without coaching; the advisor reviews the demo against stakeholder goals, neutral poll wording, and the two-person scope. Feedback may identify usability changes even when the code passes verification. Stakeholder validation does not substitute for database/security tests.
| Test pyramid layer | Approx. planned checks | Focus |
|---|---:|---|
| Unit | 18–24 | Input validation, UTC state, percentages, permission decisions |
| Integration | 12–16 | Flask services with SQLite, uniqueness, sessions, CSRF, rollback |
| System / browser | 10–14 | End-to-end admin, guest, account, results, archive |
| Acceptance / usability | 5 participant sessions + advisor demo | User goals, navigation, readability, scope |

Counts are estimates, not an extra requirement; the traceable cases below may be reused at more than one layer.

## 3. Measurable Success Criteria
| Metric | Target threshold | Measurement method | Requirement |
|---|---|---|---|
| Poll validity | 2–6 distinct nonempty choices; close > open; 0 invalid drafts saved | Create valid/invalid drafts; inspect DB | REQ-01 |
| Poll availability | ≤3 open featured; 0 votes outside UTC window | Seed 4 featured polls; boundary requests | REQ-02 |
| Vote uniqueness | Exactly 1 committed vote per identity/poll | Count DB rows after repeat attempts | REQ-03, REQ-04, NFR-04 |
| Results accuracy | Counts sum to valid total; every option shown; each % within 0.05 percentage point of one-decimal rounding | Seed known votes and calculate expected values | REQ-05 |
| Access controls | 0 successful unauthorized admin/history accesses | Attempt forbidden requests | REQ-06, NFR-02 |
| Prediction accuracy | ≤1 prediction/account/poll; tie winners accepted | Seed poll with tie and close it | REQ-07 |
| History and archive | Own records only; 100% seeded closed polls listed | Compare pages with fixture data | REQ-08 |
| Privacy boundary | 0 groups returned if any nonempty group has 1–4 votes; groups shown when all ≥5 | Seed 4/5 group fixtures | REQ-09 |
| Write failures | 0 false success acknowledgments | Simulate DB failure and inspect status | REQ-10, NFR-04 |
| Local latency | 95th percentile ≤2.0 s for each of 3 page types, 100 requests each, 1 client, 1,000 seeded votes | Timed HTTP requests on demo computer | NFR-01 |
| Security | 0 plaintext passwords; 10/10 forbidden requests rejected; 10/10 invalid/missing CSRF POSTs rejected | Inspect DB and request results | NFR-02 |
| Usability | ≥4/5 finish core flow in ≤120 seconds; keyboard-only path usable | Observe five sessions; time each | NFR-03 |
| Concurrent duplicate | Exactly 1 stored vote after 10 duplicate requests | Ten simultaneous requests, then DB query | NFR-04 |

The two teammates have confirmed the Week 9 latency and usability targets; any required advisor approval remains pending. Percentages are displayed to one decimal place; the 0.05-point tolerance reflects rounding, with ordinary floating-point tolerance at exact half-way boundaries.

## 4. Test Cases

**Execution convention:** All statuses are **Not Run** until Spring 2027. Each group uses its Week 9 traceability ID as the base (TC-001 through TC-014), with A/B/C suffixes for individual scenarios. Before each test, reset a disposable SQLite database and load only the fixture data needed for that test. Test accounts are `admin@test.local`, `user1@test.local`, and `user2@test.local`, with known test-only passwords. The administrator has the admin role; all other accounts have the user role. Unless a case specifies otherwise, use a valid CSRF token for POST requests.

Use a fixed UTC test clock of `2027-03-01T12:00:00Z` for timing tests. Poll P is open from February 28 through March 2, poll S opens March 2, and poll X closed February 28. Each poll has choices A, B, and C. A fresh guest session has no previous votes. For database checks, query the relevant table and count matching rows. Record the actual HTTP status, displayed message, and database result in the test log. All dates, accounts, and poll IDs are test fixtures, not production data.
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-001A | **REQ-01: Valid publication** | 1. Reset fixture. 2. Sign in as admin; open new poll; enter question, A/B/C, open now, close tomorrow; publish. 3. Inspect page/API and DB. | One Poll and 3 Choices saved; success shown. | Not Run |
| TC-001B | **REQ-01: Invalid choice count and content** | 1. Reset database. 2. Log in as admin. 3. Submit separate poll drafts containing 1 choice, 7 choices, duplicate choice labels, and an empty choice. 4. Check the publication response and Poll/Choice tables after each attempt. | All 4 drafts are rejected with field-specific errors. Exactly 0 new polls and 0 new choices are saved. | Not Run |
| TC-001C | **REQ-01: Invalid time** | 1. Reset fixture. 2. As admin, submit 2 choices with close equal to open. 3. Inspect page/API and DB. | Time error; 0 Poll rows saved. | Not Run |
| TC-002A | **REQ-02: Featured limit** | 1. Reset fixture. 2. Seed 4 currently open featured polls; open homepage. 3. Inspect page/API and DB. | Exactly 3 shown in defined open-time/ID order. | Not Run |
| TC-002B | **REQ-02: Early vote** | 1. Reset fixture. 2. Open S and submit valid choice with valid CSRF. 3. Inspect page/API and DB. | Rejected; 0 votes added. | Not Run |
| TC-002C | **REQ-02: Close boundary** | 1. Reset fixture. 2. Set X closing time to current fixed UTC instant; attempt vote at that instant. 3. Inspect page/API and DB. | Rejected; 0 votes added; boundary is exclusive. | Not Run |
| TC-003A | **REQ-03: Guest vote** | 1. Reset fixture. 2. Start new guest session; submit A on P. 3. Inspect page/API and DB. | 201 or success page; exactly 1 vote recorded. | Not Run |
| TC-003B | **REQ-03: Guest duplicate** | 1. Reset database. 2. Start a fresh guest session and vote A on poll P. 3. Using the same session, attempt to vote B on P. 4. Check the response and count the stored votes for that session and poll. | 409 or duplicate page; still 1 vote. | Not Run |
| TC-003C | **REQ-03: Guest login transition** | 1. Reset fixture. 2. Vote as guest; register/login in same browser; try voting P again. 3. Inspect page/API and DB. | Rejected; still 1 vote in that session. | Not Run |
| TC-004A | **REQ-04: Account lifecycle** | 1. Reset fixture. 2. Register user1; inspect password_hash; log out and log in. 3. Inspect page/API and DB. | Account created; hash differs from password; login works. | Not Run |
| TC-004B | **REQ-04: Duplicate registration** | 1. Reset database. 2. Register user1 with `user1@test.local`. 3. Attempt registration using a different username but the same email with different capitalization. 4. Attempt registration using the same username but a different email. 5. Count matching Account records. | Both duplicate registrations are rejected. Exactly 1 account retains the original username/email. | Not Run |
| TC-004C | **REQ-04: Account duplicate vote** | 1. Reset fixture. 2. Log in user1; vote A on P; submit B on P. 3. Inspect page/API and DB. | Second vote rejected; exactly 1 user1 vote. | Not Run |
| TC-005A | **REQ-05: Correct totals** | 1. Reset database. 2. Seed poll P with three votes: user1 votes A, user2 votes A, and a guest session votes B. Choice C receives zero votes. 3. Log in as user1 and open the results for P. 4. Compare displayed counts and percentages with the seeded values. | All 3 choices appear. Counts are 2, 1, and 0; total is 3; percentages are 66.7%, 33.3%, and 0.0%. | Not Run |
| TC-005B | **REQ-05: Pre-vote gate** | 1. Reset fixture. 2. Open P results in fresh guest session before voting. 3. Inspect page/API and DB. | 403/withheld; no live counts or percentages. | Not Run |
| TC-005C | **REQ-05: Closed results** | 1. Reset fixture. 2. Open X results without prior vote; X has seeded votes. 3. Inspect page/API and DB. | Final aggregate counts and percentages visible. | Not Run |
| TC-006A | **REQ-06: Admin authorized** | 1. Reset fixture. 2. Log in admin; publish valid P2. 3. Inspect page/API and DB. | Publication succeeds; one poll saved. | Not Run |
| TC-006B | **REQ-06: Ordinary user forbidden** | 1. Reset fixture. 2. Log in user1; POST valid admin poll draft. 3. Inspect page/API and DB. | 403; 0 new polls. | Not Run |
| TC-006C | **REQ-06: History isolation** | 1. Reset fixture. 2. Log in user2; request user1 private history by altered URL/ID. 3. Inspect page/API and DB. | 403/404 or own history only; no user1 records exposed. | Not Run |
| TC-007A | **REQ-07: Prediction after vote** | 1. Reset fixture. 2. User1 votes P; before close predict B. 3. Inspect page/API and DB. | Exactly 1 prediction saved. | Not Run |
| TC-007B | **REQ-07: Prediction without vote** | 1. Reset fixture. 2. User2 has not voted; try predicting A. 3. Inspect page/API and DB. | Rejected; 0 predictions for user2. | Not Run |
| TC-007C | **REQ-07: Tie and closed-poll restrictions** | 1. Reset database. 2. Seed a poll with A=5 and B=5, including a vote by user1. 3. Record user1's prediction for B before the poll closes. 4. Advance the test clock beyond closing and check the prediction outcome. 5. Attempt another prediction after closing. | The existing prediction is marked correct because B is tied for first. The new prediction is rejected, and no additional prediction is saved. | Not Run |
| TC-008A | **REQ-08: Own history** | 1. Reset fixture. 2. User1 votes P; open /history as user1. 3. Inspect page/API and DB. | P and one participation record shown. | Not Run |
| TC-008B | **REQ-08: History isolation** | 1. Reset fixture. 2. Log in user2; open /history. 3. Inspect page/API and DB. | No user1 vote or statistics displayed. | Not Run |
| TC-008C | **REQ-08: Archive boundary** | 1. Reset fixture. 2. Seed X closed and S scheduled; open /archive. 3. Inspect page/API and DB. | X shown; S not shown. | Not Run |
| TC-009A | **REQ-09: Optional fields** | 1. Reset fixture. 2. Register user1 with age/gender/region blank; save profile; vote P. 3. Inspect page/API and DB. | Both save and vote succeed; fields remain null. | Not Run |
| TC-009B | **REQ-09: Four-vote suppression** | 1. Reset fixture. 2. Seed one dimension with groups of 4 and 5 votes; request filtered results. 3. Inspect page/API and DB. | suppressed=true and groups=[]; no filtered overall total. | Not Run |
| TC-009C | **REQ-09: Five-vote boundary** | 1. Reset fixture. 2. Seed groups of 5 and 5 votes; request same dimension. 3. Inspect page/API and DB. | suppressed=false; both groups returned with correct counts. | Not Run |
| TC-010A | **REQ-10: Invalid choice** | 1. Reset fixture. 2. Submit nonexistent choice ID on P. 3. Inspect page/API and DB. | Readable rejection; 0 new votes; no success. | Not Run |
| TC-010B | **REQ-10: Closed and duplicate errors** | 1. Reset database. 2. Attempt to vote on closed poll X. 3. Confirm no vote was added. 4. Vote successfully on open poll P. 5. Attempt a second vote on P using the same session. 6. Check both error messages and database counts. | The closed vote and duplicate vote are rejected with distinguishable readable messages. Exactly 1 vote exists for P and 0 new votes exist for X. | Not Run |
| TC-010C | **REQ-10: Unconfirmed storage** | 1. Reset fixture. 2. Force test DB write failure; submit vote. 3. Inspect page/API and DB. | 503/unknown or failure message; never confirmed-success; inspect status before retry. | Not Run |
| TC-011A | **NFR-01: Homepage latency** | 1. Reset fixture. 2. Seed 1,000 votes; time 100 GET / requests sequentially. 3. Inspect page/API and DB. | 95th percentile ≤2.0 seconds. | Not Run |
| TC-011B | **NFR-01: Poll page latency** | 1. Reset fixture. 2. Same setup; time 100 GET /polls/P requests. 3. Inspect page/API and DB. | 95th percentile ≤2.0 seconds. | Not Run |
| TC-011C | **NFR-01: Results latency** | 1. Reset fixture. 2. Use eligible session; time 100 GET results requests. 3. Inspect page/API and DB. | 95th percentile ≤2.0 seconds. | Not Run |
| TC-012A | **NFR-02: Password storage** | 1. Reset fixture. 2. Register 2 test accounts; inspect SQLite Account table. 3. Inspect page/API and DB. | 0 plaintext passwords; hashes present. | Not Run |
| TC-012B | **NFR-02: Unauthorized access** | 1. Reset fixture. 2. Send 10 unauthorized admin/history requests across guest and user roles. 3. Inspect page/API and DB. | 10/10 rejected; 0 private records leaked. | Not Run |
| TC-012C | **NFR-02: CSRF enforcement** | 1. Reset fixture. 2. Send 10 POSTs missing or using invalid CSRF tokens to protected forms/API. 3. Inspect page/API and DB. | 10/10 rejected; 0 unauthorized writes. | Not Run |
| TC-013A | **NFR-03: Usability task** | 1. Reset fixture. 2. Ask 5 volunteers individually to find P, vote, read results; time without help. 3. Inspect page/API and DB. | At least 4/5 complete in ≤120 s. | Not Run |
| TC-013B | **NFR-03: Keyboard navigation** | 1. Reset fixture. 2. With mouse disconnected, Tab/Shift+Tab/Enter/Space through P vote and results. 3. Inspect page/API and DB. | All actions possible; focus visible; text counts readable. | Not Run |
| TC-013C | **NFR-03: Accessible validation error** | 1. Reset database. 2. Open poll P using keyboard navigation only. 3. Submit the voting form without selecting an answer. 4. Check the displayed error and keyboard focus. | No vote is saved. A readable text error identifies the missing answer, keyboard focus remains usable, and the error is understandable without relying on color. | Not Run |
| TC-014A | **NFR-04: Simultaneous duplicates** | 1. Reset database. 2. Log in as user1 and prepare ten valid vote requests for poll P using the same authenticated account, valid CSRF tokens, and the same choice. 3. Send all ten requests concurrently using separate test clients configured with the same account identity. 4. Check the responses and count stored votes for user1 on P. | Exactly 1 Vote row; no more than 1 confirmed success. | Not Run |
| TC-014B | **NFR-04: Guest duplicate burst** | 1. Reset fixture. 2. Use same guest session to send 10 repeated requests for P. 3. Inspect page/API and DB. | Exactly 1 Vote row. | Not Run |
| TC-014C | **NFR-04: Database outage** | 1. Reset fixture. 2. Make SQLite temporarily unavailable; attempt vote and check status after restoration. 3. Inspect page/API and DB. | No false confirmed success; at most 1 vote after safe retry. | Not Run |

**Constraint verification cases:** These checks use the existing Week 9 constraint test IDs and are completed during the final integration and acceptance phase.
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-015 | **CON-01: Zero-cost operation** | 1. Review the dependency list and required services. 2. Start the application locally. 3. Complete one poll publication and voting demonstration. | All required functionality runs locally with $0 in required licenses, subscriptions, or paid services. | Not Run |
| TC-016 | **CON-02: Neutral wording and results integrity** | 1. Review 3 sample polls for leading wording. 2. Open an active poll without voting. 3. Vote and inspect the results. | All 3 reviewed polls use neutral wording according to the team's review checklist; live results are hidden before voting and match stored vote counts afterward. | Not Run |
| TC-017 | **CON-03: Privacy and security** | 1. Inspect Account and Vote schema fields. 2. Check that passwords are hashed. 3. Request a demographic comparison with groups of 4 and 5. | No real names, exact birth dates, street addresses, or precise GPS locations are collected; no plaintext passwords are stored; the demographic breakdown is suppressed. | Not Run |
| TC-018 | **CON-04: Inclusive participation** | 1. Review 3 sample polls and optional demographic fields. 2. Check that appropriate unsure/decline choices are available. 3. Complete the voting workflow using only a keyboard. | All 3 polls pass the team's inclusivity checklist; optional demographics can be skipped; voting and results are keyboard-accessible. | Not Run |

## 5. Test Environment
| Environment | Hardware / software | Data and configuration | Purpose |
|---|---|---|---|
| Development | Each teammate’s laptop; Python 3.12 (planned), Flask/Jinja2, SQLite, pytest, Werkzeug, Flask-WTF, and Chrome or Firefox. Record exact installed versions before testing | Isolated SQLite file, test-only accounts, CSRF enabled; debug tools local only | Unit and service tests during coding |
| Staging | One designated demo laptop running Windows 11 or Ubuntu 24.04 LTS (final choice pending); Chrome and Firefox; desktop and narrow/mobile browser viewports. Record exact installed versions before staging | Fresh test DB; P open, S scheduled, X closed; 1,000 seeded votes for performance; UTC times, fixed clock in boundary tests; local loopback network | Full integration, browser, reliability and timing |
| Pilot / acceptance | Same demo laptop and browser, five volunteer users; keyboard-only check | Separate non-personal sample data; no real user records; normal local network | Usability and advisor walkthrough |

Before executing, record exact Python, Flask, SQLite, pytest, browser, OS and hardware versions in the test log; pin package versions in `requirements.txt`. Use a local-only database and disposable test identities. Simulate outages in tests rather than disabling a real production database. The demo assumes localhost unless the instructor requires public hosting. No physical sensors or devices are needed.

## 6. Pass and Fail Criteria

A test **Passes** only when all its numbered actions are executed and its expected outcome is observed, including required database row counts and status/error behavior. It **Fails** when any expected outcome differs; **Blocked** means setup or a dependency prevents execution; **Not Run** means no execution occurred. Record date, tester, environment, actual result, evidence link, and defect ID in the Spring test log. A blocked or not-run test is never counted as passed.

**Project pass:** All Must groups (REQ-01–06, REQ-10, NFR-02, NFR-04) pass every A–C case; no open critical/high defects; no known unauthorized access, vote duplication, data corruption, or false success. Should groups are required to pass if their features are included in the final demo; if deferred, mark them explicitly Deferred/Not Run and obtain advisor agreement. The project must also meet applicable thresholds in Section 3 and pass the core guest/admin demo. Failure of any Must threshold or a critical defect blocks sign-off. Performance/usability targets are agreed by both teammates but remain subject to any required advisor confirmation; a change requires a documented baseline update, not silently relaxing a failed test.

**Test execution details:** A case passes only when all stated expected outcomes are observed. When a case contains multiple invalid inputs, each input must produce the expected rejection. For tests requiring database inspection, a correct page message alone is not enough; the stored records must also match the expected count. Any unexpected database write, unauthorized access, duplicate vote, or false success message is a failure. Failed tests are corrected and rerun before being marked Pass.

## 7. Test Schedule and Ownership
| Assignment 8 milestone / date | Test activity and layer | Owner | Evidence |
|---|---|---|---|
| M10 — Nov 4, 2026 | Draft plan and traceability; no execution | Hannah lead; Aryan review | Test_Plan.md, PDF |
| M15 — Jan 22, 2027 | C6 schema unit/integration; C1 keyboard smoke checks | Aryan C6; Hannah C1 | pytest log, schema checks, screenshots |
| M16 — Feb 5 | Account/security unit and integration | Aryan; Hannah cross-check | TC-004/006/012 results |
| M17 — Feb 19 | Poll creation, timing and voting integration | Aryan C3; Hannah C4 | TC-001/002/003/014 results |
| M18 — Mar 5 | Results and archive integration | Hannah C5; Aryan C3 | TC-005/008 results |
| M19 — Mar 19 | Predictions, write recovery and security | Hannah C4; Aryan C2/C6 | TC-007/010/012/014 results |
| M20 — Apr 2 | History, privacy boundary and feature system checks | Hannah; Aryan review | TC-008/009, seeded DB checks |
| M21 — Apr 16 | End-to-end, performance, accessibility, 5-person acceptance | Hannah UI/acceptance; Aryan staging/security | TC-011/013, test log, feedback |
| M22 — Apr 30 | Regression, advisor review, local live demonstration | Both teammates; advisor sign-off requested | Final pass matrix, defects, demo notes |

Testing occurs during each two-week sprint’s protected two-day buffer. April 19–30 is stabilization only, not a period for adding new features. Spring dates are provisional until CS 5002 calendar confirmation.

## 8. Definition of Done

**Feature checklist:**

- [ ] Requirement ID and component owner identified; no unapproved scope expansion.

- [ ] Code merged and reviewed by the other teammate.

- [ ] Relevant A–C tests and applicable unit/integration checks pass with saved evidence.

- [ ] Input validation, error paths, access rules, and data integrity checked.

- [ ] Documentation, traceability, and any changed interfaces updated.

- [ ] No open critical/high defects; lower-priority issues recorded and accepted.

**Project checklist:**

- [ ] All Must test groups pass and all applicable numeric thresholds are met.

- [ ] Any deferred Should features and unresolved hosting decision documented and advisor-reviewed.

- [ ] Core admin → publish → guest/account vote → results demo works on local machine.

- [ ] Five-user validation and keyboard-only check completed; feedback recorded.

- [ ] Regression run, final test log, README, repository source, and final report updated.

- [ ] Both teammates review the evidence and request advisor sign-off before final demonstration.

**Traceability/version note:** TC-001–TC-018 retain their original Week 9 base IDs. Each REQ/NFR test group uses A/B/C suffixes for three scenarios, while constraint tests remain TC-015–TC-018. The Week 9 requirements document and technical specification must use the same test IDs as this plan.
