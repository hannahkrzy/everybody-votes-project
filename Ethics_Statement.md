# Everybody Votes — Ethics and Responsible-Use Statement

**CS 5001 Senior Design I — Assignment 11**  
**Team members:** Hannah Krzywkowski and Aryan Balyan  
**Status:** Fall 2026 design statement; implementation and testing planned for Spring 2027.

Everybody Votes is a small polling website using Python Flask, Jinja2, and SQLite. Guests can vote without registering; registered-user history and predictions are planned Should features after the core voting workflow. Administrators publish scheduled polls. The design is guided by the ACM Code of Ethics principles of avoiding harm, respecting privacy, protecting confidentiality, and building secure systems. The safeguards below are **design commitments**, not claims that the Spring implementation is finished.

## 1. Privacy summary

Only data needed for core voting and accounts, plus optional features if implemented, will be collected. The browser sends form fields or JSON to Flask (D0 UI0/I1–I4); Flask modules read and write the SQLite records (I5–I8). The following table covers the planned D1/Week 9 schema and session data.

| Data item | Source | Purpose | Consent | Storage | Access | Retention |
|---|---|---|---|---|---|---|
| Account ID, username, email, creation time | Registration/server | Identify account, login, history | Registration notice and affirmative form submission | SQLite Account | Owner and account service; admin only as needed | Until project-end deletion |
| Password / password hash | Registration | Authenticate | Registration notice and submit | Password transient; scrypt hash in SQLite | Account service only | Hash until project-end deletion |
| Role, active/disabled status | Server/admin | Enforce permissions | Explained at registration | SQLite Account | Account service/admin | Until project-end deletion |
| Age range, gender, broad region | Optional profile form | Single-category comparison | Separate optional fields, blank/decline allowed | Nullable Account fields | Owner; results service uses aggregates | Until project-end deletion |
| Signed session cookie containing account/guest identifiers; CSRF token submitted with forms or requests | Flask/browser | Login, guest uniqueness, CSRF protection | Notice before participation; required for session-based voting | Signed Flask session cookie (not encrypted); CSRF token carried in protected form/header and validated server-side | Browser and Flask session/CSRF validation | Session expires after configured lifetime; browser can clear |
| Poll ID, question, open/close times, featured, creator ID | Admin form/server | Publish and schedule official polls | Authorized admin submits poll | SQLite Poll | Public poll details; admin edits/publishes | Until project-end deletion |
| Choice ID, poll ID, choice text, display order | Admin form/server | Show valid answers | Admin submits poll | SQLite Choice | Public | Until project-end deletion |
| Vote ID, poll ID, choice ID, account ID OR hashed guest session ID | Voter/server | Record vote, reject duplicates, calculate totals | Vote-screen notice and affirmative submission | SQLite Vote | Voting/results services; owner history for account votes | Until project-end deletion |
| Prediction ID, poll/account/choice IDs | Registered voter | Check predicted winner after close | Separate prediction notice and submission | SQLite Prediction | Owner and results service | Until project-end deletion |
| Requested poll/filter, aggregate counts and percentages | Browser/SQLite query | Show permitted results | Requested by user | Computed response, not separate persistent table | After voting or poll closure; subgroup checks | Response only |
| Minimal error logs: time, route, status/error code | Flask server | Diagnose failed requests | Disclosed in privacy notice | Local restricted log, if enabled | Two maintainers | Rotate and delete entries within 30 days (planned) |

**Consent and limits.** Before registration or voting, a short notice will explain the information collected, that poll responses are stored, and that this is a student demonstration. The notice will be visible before the user submits a registration or vote, and the user must actively submit the relevant form; demographic fields remain optional and have a decline/blank choice. The notice will also explain that session cookies are required for the session-based voting design and that clearing them can reset guest duplicate protection. No third-party analytics, uploads, sensors, email notifications, or external datasets are part of the minimum version. Raw passwords, individual answers, demographics, and session tokens must never appear in logs. IP addresses and full request bodies will not be intentionally logged. Results are hidden before voting unless a poll has closed. For demographic comparisons, **the entire single-dimension breakdown is hidden if any nonempty group has fewer than five votes** (REQ-09).

**Data removed or excluded.** Real names, exact dates of birth, street addresses, phone numbers, and precise GPS locations were excluded because they are not necessary. Account deletion is outside the minimum release, so the notice must not promise an in-app deletion button. The local demo will use synthetic accounts and votes wherever practical. At project end, the maintainers will stop the app, delete the SQLite database and any backups, remove local logs and exported test data, and clear test browser sessions. The proposed cleanup mechanism is to stop Flask, delete the local `.sqlite`/`.db` files and any backup copies, remove logs and exported test fixtures, and clear test browsers; both members will record completion on a project-end checklist. This has not yet been performed; public hosting, if later required, will need an equivalent host-specific deletion procedure.

## 2. Security summary

**Default credentials:** No shipped default admin password or shared test account. An administrator is created through a documented local setup step with a unique password; seeded demo accounts use nonproduction credentials and are not exposed on a public deployment. **Unauthenticated endpoints:** Public poll listings, voting, and permitted aggregate results are intentionally accessible, but profile, history, prediction, and admin actions check identity and role on the server. Public results still enforce vote/closure rules. **Secrets in source control:** Flask `SECRET_KEY`, any deployment secrets, and local `.env` files stay outside Git; `.env` and database/log files are now excluded by the repository `.gitignore` (commit `5e73f97`). No secrets are hard-coded into examples.

The design uses Flask signed sessions (not JWT), Werkzeug scrypt password hashing, Flask-WTF CSRF tokens on state-changing requests, Secure/HttpOnly/SameSite cookies for HTTPS deployment, and parameterized SQLite queries. Set a **proposed 8-hour maximum session lifetime**, with logout and disabled-account checks; confirm the final configuration during implementation. Localhost uses HTTP only for the local demonstration; any public deployment must use HTTPS and secure cookies. Add a simple login rate limit (proposed five failed attempts per account identifier per 15 minutes, with a generic error); choose an in-process implementation appropriate to the single-instance demo and test it. No paid authentication service or separate infrastructure is needed.

**Git-history audit (November 12, 2026; Hannah Krzywkowski):** Using Git Bash, we searched all available Git history for `SECRET_KEY`, `API_KEY`, `PASSWORD`, `TOKEN`, and `PRIVATE KEY`. The reviewed matching lines discussed password hashing, session tokens, and Flask configuration in design documents; no actual credential was identified in those results. A separate all-history filename check found no matching `.env`, `.pem`, `.key`, `.db`, `.sqlite`, or `.sqlite3` files. We discovered that the repository had no `.gitignore`, so we created one to exclude environment files, private keys, local databases, Python-generated files, and logs. This change was committed and pushed to `main` as **`5e73f97`** (`Add gitignore for secrets and local files`). The final `git status` showed a clean working tree synchronized with `origin/main`. These targeted checks are not a comprehensive automated secret scan; they do not guarantee that every possible secret pattern was absent. If a real secret is ever committed, it must be revoked or rotated, not merely deleted from the latest revision.

| Self-audit question | Answer / check |
|---|---|
| Are there default credentials? | Not allowed in deployment; inspect initialization and test accounts in Spring. |
| Can anonymous users call protected routes? | Server-side `require_login`/`require_admin`; verify forbidden-request tests TC-006 and security tests. |
| Are passwords and secrets exposed? | Scrypt hashes; secret from environment; verify Git history and logs before release. |
| Are unsafe writes or duplicate votes possible? | CSRF and database UNIQUE constraints; run concurrency/rollback tests. |
| Are small demographic groups exposed? | All-or-nothing five-vote rule; test groups of four and five. |
| Is public transport encrypted? | Local demo only by default; require HTTPS before any public deployment. |

## 3. Licensing summary

The Week 9 stack lists **planned** technologies; Fall 2026 contains design documentation, not an installed application or dependency manifest. Versions below are therefore marked **to pin**, not invented. License identifiers are expected upstream licenses and must be checked against the actual selected releases and transitive packages during Spring setup.

| Dependency, dataset, or asset | Version | SPDX license | Type | Obligation / decision |
|---|---|---|---|---|
| Python | 3.x, to pin | Python-2.0 | Permissive | Keep Python license/notices if redistributing runtime. |
| Flask | To pin | BSD-3-Clause | Permissive | Retain copyright/license notice if redistributed. |
| Jinja2 | To pin | BSD-3-Clause | Permissive | Retain license notice. |
| Werkzeug | To pin | BSD-3-Clause | Permissive | Retain license notice. |
| Flask-WTF | To pin | BSD-3-Clause | Permissive | Retain license notice; audit its dependencies. |
| SQLite | Bundled with Python / runtime version to record | Public-domain (no SPDX license identifier) | Public domain | No copyleft obligations; verify distribution details. |
| Python `sqlite3`, `datetime`, `unittest` | Included with selected Python 3.x version | Python-2.0 | Permissive | Covered by Python distribution license. |
| pytest (only if selected) | To pin / optional | MIT | Permissive | Retain notice if distributed. |
| Original poll questions, CSS, diagrams | Team-created | MIT (team-selected; LICENSE provided separately) | Permissive | Credit both team members; add LICENSE to repository. |
| External datasets, images, icons, fonts | None required for MVP | Not applicable | None | Do not add without a license/author entry. |

**Copyleft decision:** No copyleft component or asset has been selected; there is currently nothing to replace or relicense. **Project license decision:** The team selected **MIT** for its original source and assets. A root `LICENSE` file has been prepared with this submission and must be committed to the repository to publish that license. The listed planned permissive dependencies do not impose a copyleft conflict with MIT; upstream notices still apply. No copyleft dependency is planned. If a GPL/AGPL or other copyleft package is introduced, review redistribution and hosting obligations first and document whether to replace it. Review actual `requirements.txt`/lockfile, transitive packages, and any assets before treating this inventory as complete. No outside dataset is required: any collected votes will come from people who voluntarily submit them for the class demo, or from synthetic team-generated test fixtures. No third-party dataset license is therefore applicable.

## 4. Attribution list

This is the project attribution inventory. The accompanying README section 5 update links to this statement **and reproduces the attribution inventory**, satisfying the repository attribution requirement once committed. Update both when code or assets are added.

| Item | Source / author | Use and attribution |
|---|---|---|
| Python | Python Software Foundation — https://www.python.org/ | Application language and standard library; Python-2.0. |
| Flask | Pallets — https://github.com/pallets/flask | Web routes and sessions; BSD-3-Clause. |
| Jinja2 | Pallets — https://github.com/pallets/jinja | HTML templating; BSD-3-Clause. |
| Werkzeug | Pallets — https://github.com/pallets/werkzeug | Password hashing; BSD-3-Clause. |
| Flask-WTF | https://github.com/pallets-eco/flask-wtf | CSRF protection; BSD-3-Clause. |
| SQLite | SQLite project — https://www.sqlite.org/ | Local relational database; public domain. |
| pytest (if used) | pytest-dev — https://github.com/pytest-dev/pytest | Optional tests; MIT. |
| Original diagrams, UI and poll text | Hannah Krzywkowski and Aryan Balyan | Team-created; no outside creative assets currently identified. |
| Adapted tutorial/repository code | None identified in supplied design documents | Log exact URL, author, license, and changed lines if any code is adapted later. |
| Datasets | Volunteer poll responses or synthetic team fixtures | No imported dataset; explain voluntary collection and use only for class demo. |

The original Wii Everybody Votes Channel is inspiration for the general polling concept, **not** a source of copied code, artwork, music, or branding. No Wii images or logos will be used.

## 5. AI-use disclosure

> "Generative AI tools may be used for drafting and code assistance but not for the design decisions or written analysis that are assessed in this course."

The course distinguishes drafting assistance ("Generally OK") from using AI as a substitute for the team's own design decisions or assessed written analysis. The table records the reported use of AI; the team remains responsible for independently checking and authoring its assessed analysis.

| Tool | Used by | Task | Course framework | Verification / boundary |
|---|---|---|---|---|
| None reported | Aryan Balyan | Aryan confirmed he did not use AI tools for project work | N/A | Reported by Aryan to Hannah |
| None reported | Hannah Krzywkowski | Hannah confirmed she did not use AI tools for project work | N/A | Reported by Hannah |

**Team confirmation:** We confirm that our architecture, data model, feature priorities, and design rationale were determined and evaluated by our team. We have not used AI in place of our own assessed design decisions or analysis.

Hannah Krzywkowski — Signature: ____________________  Date: __________

Aryan Balyan — Signature: ____________________  Date: __________

**Cross-references:** Week 3 Constraints Essay (Ethical, Security, Diversity & Cultural; licensing and consent also address legal obligations); D0 UI0/I1–I8; D1 Account/Poll/Choice/Vote/Prediction model; D2 C1–C6 and error paths; Week 9 Requirements REQ-01–REQ-10 and NFR-02; Week 9 Technical Specification §§3–4; Week 10 Test Plan. All proposed implementation and verification work remains scheduled for Spring 2027.
