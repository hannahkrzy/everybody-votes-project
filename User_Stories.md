# Everybody Votes — User Stories and Use Cases

**Course:** CS 5001 — Senior Design I  
**Assignment:** 4 — Requirements Elicitation  
**Team:** Everybody Votes  
**Team members:** Hannah Krzywkowski and Aryan Balyan  

## 1. Stakeholder Map

| Type | Stakeholder | Main need |
| --- | --- | --- |
| Primary | Guest voter | Vote and see results without registering. |
| Primary | Registered voter | Save participation, predictions, and personal statistics. |
| Primary | Poll administrator | Publish and schedule official polls. |
| Secondary | Application maintainer | Diagnose basic errors without accessing private responses. |
| Hidden | Keyboard-only/assistive-technology voter | Vote and read results without a mouse or color cues. |
| Hidden | Privacy-conscious voter | Skip demographics and avoid disclosure through small-group statistics. |

Administrators are primary stakeholders because they use the application directly. Maintainers are secondary stakeholders because they support it. Accessibility and privacy needs can be overlooked if only typical voters are considered.

## 2. User Stories

**US-01 — Guest voting (primary):** As a guest voter, I want to answer an open poll without registering, so that I can participate without sharing account information.

**US-02 — Troubleshooting (secondary):** As an application maintainer, I want to identify common reasons a vote was rejected, so that I can troubleshoot errors without viewing private answers.

**US-03 — Poll publishing (primary):** As a poll administrator, I want to publish polls with choices and opening/closing times, so that voters see current questions and voting ends on schedule.

**US-04 — Accessible voting (hidden):** As a keyboard-only voter, I want to submit a vote and understand the results without a mouse or color cues, so that I can participate independently.

**US-05 — Demographic privacy (hidden):** As a privacy-conscious registered voter, I want to leave demographic questions unanswered, so that I can participate without disclosing information I prefer to keep private.

These five stories sample different stakeholder needs; they are not the complete backlog for the application.

## 3. Use Cases

### UC-01 — Submit a vote and view results

**Related stories:** US-01, US-04  
**Actor:** Guest voter; registered voters follow the same basic path.  
**Secondary actors:** None. The application and database are internal system components, not separate human actors.  
**Trigger:** Voter opens an active poll.  
**Preconditions:** Poll is open, has 2–6 choices, and the voter has not already voted in the current guest session or registered account.

**Main flow**
1. **Actor:** Voter opens a poll.
2. **System:** Displays its question and choices without current results.
3. **Actor:** Voter selects one answer and submits.
4. **System:** Verifies that the poll is open and the answer is valid, records the vote once, and displays counts and percentages for each choice, identifying the selected answer.

**Alternate flow:** For a signed-in voter, the vote is associated with the account. A second vote for the same poll is rejected; changing an existing vote is not part of the minimum version.

**Exceptions:** If the poll has closed or the answer is invalid, the system rejects the vote with an explanation. If storage fails, it reports that the vote was not confirmed; retrying must not create duplicate votes in the same guest session.

**Postconditions:** A successful submission adds one response and displays updated results. A rejected submission adds none. Guest duplicate prevention is session-based, not proof of one vote per person.

### UC-02 — Publish a scheduled poll

**Related story:** US-03  
**Actor:** Authorized poll administrator.  
**Secondary actors:** None. The application and database are internal system components, not separate human actors.  
**Trigger:** Administrator creates an official poll.  
**Preconditions:** Administrator is signed in and authorized to manage polls.

**Main flow**
1. **Actor:** Administrator enters a question, 2–6 distinct answer choices, and opening and closing times.
2. **System:** Validates the choice count and checks that closing time follows opening time.
3. **Actor:** Administrator confirms publication.
4. **System:** Saves the poll and schedule and shows a confirmation. Voting is permitted only during the scheduled window.

**Alternate flow:** A poll with a future opening time remains scheduled until it opens. Choosing whether it appears among up to three featured polls is a separate administrative action.

**Exception:** Invalid choice counts or times prevent publication and produce a field-specific error.

**Postconditions:** A valid poll is stored; an invalid poll is not published.

## 4. Given/When/Then Acceptance Criteria

UC-01 and UC-02 cover the main voting and publishing workflows. AC-03.1 through AC-03.3 provide additional tests for maintenance and privacy requirements that do not need separate use cases.

| ID | Given | When | Then |
| --- | --- | --- | --- |
| AC-01.1 | An open four-choice poll and a guest who has not voted | Guest submits one valid answer | Exactly one response is saved; counts and percentages appear with the selected choice identified. |
| AC-01.2 | A guest has not voted on an open poll | Guest views the question | Current counts and percentages are hidden. |
| AC-01.3 | A poll is closed | Voter submits an answer | Submission is rejected and zero responses are added. |
| AC-01.4 | A registered account or guest session has already voted on a poll | It submits another vote | Zero additional responses are recorded. |
| AC-01.5 | A keyboard-only voter opens an active poll | They select, submit, and review results | Every step is keyboard-accessible, focus remains visible, and counts/percentages are readable without color alone. |
| AC-01.6 | Vote storage fails | Voter submits or retries | No false success message appears; the failure is explained and retrying does not create duplicate responses in that session. |
| AC-02.1 | An authorized administrator enters 3 choices and valid times | They publish | Exactly one poll is saved with the specified choices and schedule. |
| AC-02.2 | A poll is scheduled to open in the future | Someone attempts to vote early | Zero votes are accepted. |
| AC-02.3 | An administrator enters 7 choices or a closing time at/before opening | They publish | No poll is published; the invalid field is identified. |
| AC-03.1 | A vote fails because of a closed poll, invalid input, or storage error | Maintainer checks the existing error message or basic log | The cause can be distinguished without exposing passwords, demographics, or individual votes. |
| AC-03.2 | A registered voter leaves age range, gender, and region blank | They save their profile and vote | Both actions succeed without requiring those fields. |
| AC-03.3 | Demographic groups for a completed poll have 4 and 5 valid responses | A voter requests a single-category comparison | The 4-response breakdown is hidden; the 5-response breakdown can be displayed. |

For AC-01.1, each percentage equals (answer count ÷ total valid responses) × 100, rounded to one decimal place. Displayed counts must add up to the poll's valid-response total. These criteria can become tests in Week 10.

## 5. INVEST Check

| Criterion | How the stories meet it |
| --- | --- |
| Independent | Voting, publication, diagnostics, accessibility, and privacy can each be checked as distinct outcomes, even when they share underlying components. |
| Negotiable | Stories describe needs rather than prescribing frameworks or page layouts. |
| Valuable | Each story addresses a specific stakeholder problem. |
| Estimable | Work is bounded to existing application features and simple checks. |
| Small | No separate monitoring dashboard, accessibility product, or demographic analytics platform is required. |
| Testable | AC-01.1–AC-03.3 provide observable success and failure conditions. |

## 6. Requirements Elicitation

### Method 1 — Semi-structured interviews

The following interviews were conducted to explore plausible stakeholder needs. The questions ask about behavior and concerns rather than prescribing an interface.

#### INT-01 — Former Wii Everybody Votes player (primary)
*Details: in-person interview; approximately 10 minutes.*

- **Q1. What did you enjoy about the original channel?** **P1:** “I liked the random questions and guessing what most people would pick.”
- **Q2. What was frustrating?** **P1:** “Sometimes neither of the two answers fit what I thought.”
- **Q3. Would you register before trying a poll?** **P1:** “Probably not. I'd want to vote first and make an account later if I liked it.”
- **Q4. When should results appear?** **P1:** “After I answer, so I don't get influenced by everybody else's votes.”
- **Q5. Would you return after a poll closes?** **P1:** “Yes, to see if my prediction was right.”

#### INT-02 — Occasional online poll participant (primary)
*Details: phone-call interview; approximately 8 minutes.*

- **Q1. What makes you answer an online poll?** **P2:** “A short question that interests me and doesn't take much effort.”
- **Q2. What would make you leave?** **P2:** “Having to give my email just to answer one question.”
- **Q3. What should results show?** **P2:** “Percentages and the number of people who voted. Eighty percent means less if it's only five people.”
- **Q4. Would you share demographic information?** **P2:** “Maybe my age range, but I want to skip anything I don't feel comfortable answering.”
- **Q5. What if submitting produces an error?** **P2:** “I'd want to know whether my vote counted before trying again.”

#### INT-03 — Website maintenance support (secondary)
*Details: video-call interview; approximately 12 minutes.*

- **Q1. What commonly causes publishing problems?** **S1:** “Wrong dates, missing fields, or changes that weren't saved.”
- **Q2. What helps you diagnose a failure?** **S1:** “Which poll failed, when it happened, and why. I don't need to see anyone's vote.”
- **Q3. What would reduce routine support work?** **S1:** “Reject invalid schedules and missing answer choices before publishing.”
- **Q4. How would you handle unreliable free hosting?** **S1:** “Show a clear failure instead of saying a vote saved when it didn't.”
- **Q5. Would you need a separate monitoring dashboard?** **S1:** “No. Clear errors and basic logs would be enough for a small project.”

#### INT-04 — Keyboard-only and privacy-conscious voter (hidden)
*Details: video-call interview; approximately 11 minutes.*

- **Q1. What barriers have you experienced with polls?** **H1:** “Sometimes I can't select an answer or submit without using a mouse.”
- **Q2. How should results be displayed?** **H1:** “Include the answer labels, counts, and percentages instead of only colored bars.”
- **Q3. How should demographic questions work?** **H1:** “I should be able to skip them, including when editing my profile.”
- **Q4. What worries you about small comparison groups?** **H1:** “If only one or two people are in a group, it can reveal too much about their answers.”
- **Q5. What happens if keyboard focus disappears?** **H1:** “I might not know whether my vote worked or where to go next.”

### Findings from the interviews

| Finding | Interview evidence | Requirement supported |
| --- | --- | --- |
| Registration discourages first-time participation | P1 Q3; P2 Q2 | US-01; AC-01.1 |
| Results shown before voting may influence answers | P1 Q4 | AC-01.2 |
| Counts help put percentages in context | P2 Q3 | AC-01.1 |
| Unclear submission failures encourage retries | P2 Q5; S1 Q4 | AC-01.6 |
| Poll validation prevents maintenance problems | S1 Q1, Q3 | US-03; AC-02.3 |
| Basic diagnostic messages are sufficient | S1 Q2, Q5 | US-02; AC-03.1 |
| Keyboard access must work throughout voting | H1 Q1, Q5 | US-04; AC-01.5 |
| Demographics should remain optional | P2 Q4; H1 Q3 | US-05; AC-03.2 |
| Small demographic groups need protection | H1 Q4 | AC-03.3 |

**Interpretation:** The interviews favor quick voting, understandable results, reliable publication, accessibility, and privacy. They do not justify additional social features or monitoring tools. 

### Method 2 — Historical product comparison

The original Nintendo Wii Everybody Votes Channel is a reference for the application's basic experience.

| Reference feature | Scoped design implication |
| --- | --- |
| Rotating questions | Display up to three featured active polls. |
| Simple voting | Offer 2–6 predefined choices, with a neutral option when appropriate. |
| Predictions | Allow registered users to predict the most popular answer and check the result after closing. |
| Results | Show aggregate counts and percentages after voting. |
| Console-based experience | Adapt the workflow to a responsive, keyboard-accessible website. |

The interviews suggest *why* users might need these behaviors; the comparison suggests *how to preserve the original concept*.

## 7. Traceability and Scope

| Story | Supporting evidence | Verification |
| --- | --- | --- |
| US-01 | P1 Q3–Q4; P2 Q2 | UC-01; AC-01.1–AC-01.4, AC-01.6 |
| US-02 | S1 Q2, Q4–Q5 | AC-03.1 |
| US-03 | S1 Q1, Q3 | UC-02; AC-02.1–AC-02.3 |
| US-04 | H1 Q1–Q2, Q5 | AC-01.5 |
| US-05 | P2 Q4; H1 Q3–Q4 | AC-03.2–AC-03.3 |

**Required minimum product:** A responsive website with guest voting, registered accounts, administrator-created timed polls, up to three featured polls, 2–6 answer choices, results, simple predictions, saved voting history, basic personal statistics, and a completed-poll archive. Only administrators publish official polls. A registered account may cast one vote and make one prediction per poll; editing either is not required. Predictions are checked after closing.

**Privacy and accessibility:** Demographic fields (age range, gender, broad region) are optional. Only single-category demographic comparisons are required, and a group's breakdown appears only when it has at least five valid responses to that poll. This reduces disclosure risk but does not guarantee anonymity. Voting and results must be usable by keyboard and understandable without color alone.

**Excluded or optional:** User-submitted poll suggestions, advanced statistics, combined demographic filters, notifications, automatic recurring schedules, social features, public poll creation, AI-generated polls, separate monitoring dashboards, and native mobile apps.

**Constraints:** $0 budget; free/open-source or free-tier tools; no plaintext passwords; account-specific data protected by authorization. Guest vote limits apply per session, not per human.

**Validation plan:** In Week 9, refine these draft requirements without adding features. In Week 10, test guest voting, poll publication, error handling, keyboard access, and the four-versus-five-response privacy boundary.