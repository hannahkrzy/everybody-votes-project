# Everybody Votes — Technical Specification

**CS 5001 Senior Design I · Assignment 9**  
**Team:** Hannah Krzywkowski and Aryan Balyan  

## 1. Version and change log

| Version | Date | Change | Author |
|---|---|---|---|
| 1.0 draft | 2026-11-04 | Consolidated D0/D1/D2 interfaces, selected stack, constraints, and proposed traceability/test IDs | Hannah Krzywkowski and Aryan Balyan (reviewed and approved by both team members)|
| 1.1 draft | 2026-11-10 | Expanded TC-001–TC-014 into A/B/C scenarios for Assignment 10; updated requirement traceability. No functional requirements or architecture changed. | Hannah Krzywkowski and Aryan Balyan |

This technical specification traces to Requirements_Document.md, version 1.1 draft, revised November 10, 2026.

This is a specification, not evidence of completed code or advisor approval. Proposed measurable targets in NFR-01 and NFR-03 are new to the requirements baseline and await approval.

## 2. Architecture summary

Everybody Votes uses a **single Python Flask application**, rendered as HTML pages with **Jinja2** and backed by a local **SQLite** file. A browser requests a page or submits a form; Flask checks the signed session, calls a Python service, reads/writes SQLite, then renders the response. JSON `/api/v1` endpoints call the **same service functions**, rather than a second implementation. The application is layered, not microservices: **C1 Web Interface** (Hannah), **C2 Account & Access** (Aryan), **C3 Poll Management** (Aryan), **C4 Vote & Prediction** (Hannah), **C5 Results & Statistics** (Hannah), and **C6 Data Store** (Aryan). Logical interfaces UI0 and I1–I8 retain their D0 meanings.

For a vote, C1 calls C4, C4 validates the poll and identity, and C6 commits the record under a uniqueness constraint. Only after a confirmed vote does C1 request results from C5. Admin publication goes through C2 authorization and C3 validation before C6 saves the poll and choices in one transaction. Poll state is calculated from UTC timestamps on each request; **no scheduler or background worker** is needed. The minimum deployment is localhost on the team's computer; public HTTPS hosting with persistent SQLite is conditional on instructor confirmation. This architecture follows the Spring Assignment 8 task plan, not a larger hosted platform.

**Existing diagrams:** [D0 block diagram](Design_Diagrams/D0_Block_Diagram.svg), [D0 data flow](Design_Diagrams/D0_Data_Flow.svg), [D1 data model](Design_Diagrams/D1_Data_Model.svg), [D2 vote/error flow](Design_Diagrams/D2_Vote_Error_Flow.svg), [D2 poll lifecycle](Design_Diagrams/D2_Poll_Lifecycle.svg). See the accompanying D0–D2 Markdown explanations in the same folder.

## 3. Component interfaces and data formats

**Conventions:** Browser-to-Flask transport is HTTP on localhost, HTTPS if publicly deployed. Form posts use ordinary form encoding; API requests and responses use JSON. Internal calls I1–I4 use Python typed records, not network protocols; I5–I8 use parameterized SQLite queries/transactions. UUIDs are strings in JSON. All API timestamps are timezone-aware ISO 8601; database timestamps are normalized to UTC. All JSON errors use `{"error":{"code":"...","message":"..."}}`. Protected POST routes require a CSRF token; account identity is read from the validated session, never supplied as a trusted JSON account ID.

| Boundary | Inputs and format | Outputs and format | Protocol | Error behavior |
|---|---|---|---|---|
| UI0 Browser ↔ C1 | Keyboard/mouse actions, HTML form fields | HTML page, labeled text/errors | Browser HTTP(S) | Field error, 403/404, or safe 503 message |
| I1 C1 ↔ C2 | Credentials or signed session context (Python record) | Current account/role or guest identity | Internal Python call | 401 invalid login; 403 forbidden; no secret disclosure |
| I2 C1 ↔ C3 | `PollDraft` or list request (Python record) | Poll ID, status, or list | Internal Python call | 400 invalid dates/choices; 403 non-admin; 503 storage |
| I3 C1 ↔ C4 | Poll ID, choice ID, server-derived identity | Vote/prediction ID and acceptance status | Internal Python call | 409 duplicate/closed; 422 wrong-poll choice; 503 uncertain write |
| I4 C1 ↔ C5 | Poll ID, session proof, optional single dimension | Aggregated counts/percentages or groups | Internal Python call | 403 before-vote results; 400 filter; 503 read failure |
| I5 C2 ↔ C6 | Account record, lookup, password hash | Account record or commit status | Parameterized SQLite SQL | Unique conflict; rollback; never return hash to browser |
| I6 C3 ↔ C6 | Poll and 2–6 Choice rows | Atomic commit/poll ID | SQLite transaction | Roll back all rows on failed publish |
| I7 C4 ↔ C6 | Vote or Prediction row | Committed ID/status | SQLite transaction | UNIQUE/FK check; bounded lock retry; 409/422/503 |
| I8 C5 ↔ C6 | Poll/account/filter read | Grouped SQL rows | SQLite query | Read failure → 503, never fabricated zero results |

### 3.1 Browser page and JSON endpoint table

HTML page routes use GET to render and POST for forms; page POST handlers call the same services as the JSON endpoints. API responses are JSON; no JavaScript framework is required.

| Method and route | Request | Success | Errors |
|---|---|---|---|
| GET `/`, `/polls/<id>`, `/archive` | URL and session | 200 HTML featured, poll, or archive | 404, 503 |
| GET/POST `/register`, `/login`, `/logout` | Username/email/password form or logout action | HTML redirect/session | 400, 401, 409; CSRF 403 |
| GET/POST `/profile`; GET `/history` | Session; optional demographic fields | HTML profile/history | 400, 401, 403 |
| GET/POST `/admin/polls/new` | Admin session; PollDraft form | HTML form/redirect | 400, 401, 403, 409, 503 |
| GET `/api/v1/polls?view=featured\|archive` | `view` enum | 200 `{polls:[...]}` | 400, 503 |
| GET `/api/v1/polls/{poll_id}` | UUID path | 200 poll with choices | 400, 404, 503 |
| POST `/api/v1/admin/polls` | `PollDraft` JSON, admin session, CSRF | 201 `{poll_id,status}` | 400, 401, 403, 409, 503 |
| POST `/api/v1/polls/{poll_id}/votes` | `{choice_id:UUID}`, session, CSRF | 201 `{vote_id,accepted:true,reason:"recorded"}` | 400, 404, 409, 422, 503 |
| GET `/api/v1/polls/{poll_id}/results` | Optional `dimension` | 200 aggregate or privacy-filtered result | 400, 403, 404, 503 |
| POST `/api/v1/polls/{poll_id}/predictions` | `{choice_id:UUID}`, registered session, CSRF | 201 `{prediction_id,accepted:true}` | 400, 401, 403, 404, 409, 422, 503 |

**Data shapes:** `PollDraft = {question:string, choices:string[2..6], opens_at:ISO8601, closes_at:ISO8601, featured:boolean}`. Poll choice strings must be distinct after trimming. Unfiltered results: `{poll_id, total:integer, options:[{choice_id,count:integer,percent:number}], suppressed:false}`. Filtered results: `{poll_id, dimension:"age_range"|"gender"|"region", groups:[{value,total,options:[...]}], suppressed:boolean}`. A filtered response **never** includes the overall total. If any nonempty demographic group has 1–4 votes, return `groups:[]` and `suppressed:true`; otherwise return every nonempty group. An empty dimension returns `groups:[]`, `suppressed:false`. Do not allow combined dimensions.

### 3.2 SQLite schema and service behavior

| Table | Essential fields and rules |
|---|---|
| Account | `account_id` UUID PK; unique normalized `email`; unique `username`; `password_hash`; `role` user/admin; active/disabled status; nullable age_range/gender/region; UTC creation time |
| Poll | `poll_id` PK; question; opens_at; closes_at; featured flag; created_by account FK; `closes_at > opens_at` |
| Choice | `choice_id` PK; poll_id FK; text; display_order; UNIQUE(poll_id, choice_id); publication creates 2–6 rows |
| Vote | `vote_id` PK; poll_id; choice_id; account_id nullable; guest_session_hash nullable; exactly one identity set; UNIQUE(poll_id,account_id); UNIQUE(poll_id,guest_session_hash); composite poll/choice FK |
| Prediction | `prediction_id` PK; poll_id; account_id; prediction_choice_id; UNIQUE(poll_id,account_id); FK to existing registered Vote(poll_id,account_id) and poll/choice |

Enable `PRAGMA foreign_keys=ON` for every connection. Use SQLite transactions (including `BEGIN IMMEDIATE` for vote writes), parameterized queries, and indexes on Vote(poll_id,choice_id), Vote(account_id,poll_id), Prediction(account_id,poll_id), and Poll(open/close times). An opaque random guest ID lives in the existing **signed, not encrypted** Flask session cookie; only its hash is stored in Vote. Preserve the guest identifier on login so a guest cannot vote again in the same browser by logging in. Clearing cookies or changing devices can bypass guest-only duplicate protection; do not claim one vote per person.

**Vote flow:** Check server UTC time, poll, choice ownership, account/guest prior vote, then insert atomically. Return success only after commit. A network timeout may hide a successful commit; the internal `get_vote_status(poll_id,identity)` returns `recorded`, `not_recorded`, or `unknown` before any manual retry. No automatic resubmission. **Results flow:** Verify the viewer voted or the poll closed; use SQL `GROUP BY` and include zero-vote choices; ties count as winners for predictions. **Poll flow:** Compute `scheduled/open/closed` from timestamps; list at most three open featured polls; archive closed polls. These are ordinary functions, not additional services.

## 4. Technology stack

| Layer | Choice | Why it fits / linked IDs |
|---|---|---|
| Presentation | Jinja2 HTML templates, CSS, minimal optional JavaScript | One small responsive website; accessible native forms; REQ-03, NFR-03, CON-04 |
| Server/API | Python 3 + Flask | Small single application, familiar language, simple routes; REQ-01–REQ-10, CON-01 |
| Authentication | Flask signed sessions; Werkzeug scrypt password hashing | No external identity service; REQ-04, REQ-06, NFR-02, CON-03 |
| Form protection | Flask-WTF CSRFProtect | Protect cookie-authenticated writes; NFR-02, CON-03 |
| Storage | SQLite via Python `sqlite3` | Free file database with FK/UNIQUE/transactions; REQ-03–REQ-05, NFR-04, CON-01 |
| Scheduling | UTC comparisons using Python `datetime` | No background worker; REQ-02, CON-01 |
| Deployment | Local Flask/SQLite demo; optional verified free HTTPS host | Meets $0 baseline; CON-01; OQ-01 unresolved |
| Tests | Python unittest or pytest (free), Flask test client, manual keyboard testing | Repeatable TC-001A-C through TC-014A-C; NFR-01–NFR-04, CON-04 |

The team will pin supported package versions before implementation. No React app, PostgreSQL server, Redis, cloud queue, or paid API is needed. SQLite concurrency is deliberately limited to a small senior-design demo; load testing will establish actual limits.

## 5. Known constraints and consequences

| ID | Constraint applied in design | What it rules out or forces |
|---|---|---|
| CON-01 | $0 required budget | Free Flask/SQLite, local fallback; excludes paid APIs/hosting/trial credits |
| CON-02 | Neutral polls and honest result visibility | Admin-created neutral questions, real stored counts, pre-vote result gate |
| CON-03 | Account and demographic privacy | Hashed passwords, role checks, optional broad values, entire breakdown hidden for any group under five |
| CON-04 | Inclusive answers and accessibility | 2–6 choices, decline/unsure option where appropriate, labeled keyboard-usable HTML |

**Implementation limits:** no public hosting commitment before OQ-01 is answered; no email/push, password reset, account deletion, user-suggested polls, recurring rotation, or extra services. Spring 2027 work follows Assignment 8 T01–T12, with testing and protected stabilization time. Should features are built only after the Must workflow passes.

## 6. Traceability matrix

Statuses describe design documentation, not implementation. `Designed` means the named D0/D1/D2 component behavior has been specified; `Planned` means the verification threshold is newly proposed or not yet finalized. Test IDs are aligned with the Assignment 10 `Test_Plan.md`; A–C suffixes identify three scenarios for each REQ/NFR base ID. Every row has a named design component.

| Req ID | Description | Design Component (D0/D1/D2) | Status | Test Case |
|---|---|---|---|---|
| REQ-01 | Admin publishes valid poll | C2/C3/C6 (D2 poll publishing) | Designed | TC-001A-C |
| REQ-02 | Three featured polls; UTC open/close | C3/C4 (D2 lifecycle) | Designed | TC-002A-C |
| REQ-03 | One vote per guest session | C2/C4/C6 (D1 vote transaction) | Designed | TC-003A-C |
| REQ-04 | Account access and account vote uniqueness | C2/C4/C6 (D2 account; D1 Vote) | Designed | TC-004A-C |
| REQ-05 | Correct gated aggregate results | C5/C6 (D1 results) | Designed | TC-005A-C |
| REQ-06 | Admin/history authorization | C2/C3/C5 (D2 role checks) | Designed | TC-006A-C |
| REQ-07 | Prediction after vote, before close | C4/C5/C6 (D1 prediction) | Designed | TC-007A-C |
| REQ-08 | Personal history and archive | C3/C5 (D2 pages and queries) | Designed | TC-008A-C |
| REQ-09 | Optional demographics; suppression | C2/C5/C6 (D1 suppression) | Designed | TC-009A-C |
| REQ-10 | Safe rejection and uncertain vote outcome | C1/C4/C6 (D2 error flow) | Designed | TC-010A-C |
| NFR-01 | 95th percentile local latency threshold | C1/C3/C5/C6 (D0 performance; D1 queries) | Planned | TC-011A-C |
| NFR-02 | Password, permission, and CSRF checks | C2/C6 (D2 access/security) | Designed | TC-012A-C |
| NFR-03 | Usability and keyboard success threshold | C1 (D2 accessible templates) | Planned | TC-013A-C |
| NFR-04 | Duplicate and failure reliability | C4/C6 (D1 transaction; D2 E14/E16) | Designed | TC-014A-C |
| CON-01 | Zero-cost project | C1–C6 (D0 layered monolith; D1 stack) | Designed | TC-015 |
| CON-02 | Ethical wording and gated results | C3/C5 (D2 publication; D1 results) | Designed | TC-016 |
| CON-03 | Data minimization and privacy | C2/C5/C6 (D1/D2 privacy rules) | Designed | TC-017 |
| CON-04 | Inclusive and accessible participation | C1/C3 (D2 UI/poll validation) | Designed | TC-018 |

**Orphan audit:** No blank component mappings. TC-015–TC-018 are proposed constraint checks (budget/license inventory, neutral-wording/result review, privacy audit, and accessible-answer review), not tests already executed. NFR-01/NFR-03 remain Planned pending advisor approval of their new thresholds.

## 7. Advisor review record

| Item | Record |
|---|---|
| Faculty advisor | Dr. Hrishikesh Vinayak Bhide |
| Review date | **Not supplied / pending** |
| Materials to review | `Requirements_Document.md` v1.1 draft; `Technical_Specification.md` v1.1 draft; traceability matrix |
| Topics for discussion | Must/Should scope for two students; new latency/usability targets; local vs public demo; Spring 2027 implementation feasibility |
| Changes resulting from advisor review | **None documented yet.** Record actual feedback and specific changes here after review. |
| Follow-up owner | Hannah to coordinate; Aryan to check hosting feasibility |

**Prior review, not advisor approval:** The Week 7 peer review raised guest-vote recovery, demographic 4/5 boundaries, account permissions, and unconfirmed public hosting. Those changes are reflected in D2 and this draft. Peer feedback must not be represented as a completed faculty-advisor review.
