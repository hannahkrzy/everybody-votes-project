# Everybody Votes — Presentation Run Sheet
CS 5001 | Fall 2026 | Hannah Krzywkowski and Aryan Balyan

**Faculty Advisor:** Dr. Hrishikesh Vinayak Bhide

**Deck:** Everybody_Votes_Presentation.pptx (21 slides; slides 12–21 are Q&A backups).

## Speaking roles and logistics
- Live: Hannah presents slides 1–11 in 4:00; reserve 1:00 for Q&A, per instructor-approved arrangement.
- Recorded: Hannah presents slides 1–6; Aryan presents slides 7–11. Each has 4:15; total 8:30. Final script: Hannah 912 words; Aryan 858 words. Planning pace for both: 234 words/minute; allow natural pauses and transitions within the 10-minute limit.
- The revised 8:30 recording schedule replaces the original 9:00 schedule in the presentation speaker notes, allowing an additional 30 seconds for pauses and transitions.
- Designated timekeeper: Hannah monitors her own time during the live presentation. Aryan keeps time during live practice. Hannah tracks the recorded rehearsal time, and Aryan checks the handoff.
- Record using PowerPoint Record Slide Show. Test audio and playback first. Hannah posts deck and video links to the Senior Design Team Table; share through the team drive.
- Keep an offline deck on USB or downloaded storage; test on another machine. Expo fallback: local application, then recorded walkthrough or screenshots if the application fails.

## Slide schedule

| Slide | Topic | Live speaker / start / duration | Recorded speaker / start / duration | Transition / handoff |
|---|---|---|---|---|
| 1 | Title | Hannah / 0:00 / 15s | Hannah / 0:00 / 20s | First, here's our team. |
| 2 | Team | Hannah / 0:15 / 10s | Hannah / 0:20 / 15s | Here's what we're building. |
| 3 | Abstract | Hannah / 0:25 / 20s | Hannah / 0:35 / 48s | So how does it differ from the Wii version? |
| 4 | Modernization | Hannah / 0:45 / 25s | Hannah / 1:23 / 42s | Let's look at who will use it. |
| 5 | User stories | Hannah / 1:10 / 23s | Hannah / 2:05 / 60s | Here's how those actions work behind the scenes. |
| 6 | Architecture | Hannah / 1:33 / 32s | Hannah / 3:05 / 70s | Recorded: Now Aryan will explain the constraints that shaped these decisions. Live: These decisions come with trade-offs. |
| 7 | Constraints | Hannah / 2:05 / 25s | Aryan / 4:15 / 57s | With those constraints in mind, here's our progress. |
| 8 | Progress | Hannah / 2:30 / 25s | Aryan / 5:12 / 52s | That gives us a foundation for spring. |
| 9 | Accomplishments | Hannah / 2:55 / 20s | Aryan / 6:04 / 50s | Here's how we've divided that work. |
| 10 | Division of work | Hannah / 3:15 / 15s | Aryan / 6:54 / 43s | Finally, here's what we want to demonstrate. |
| 11 | Expo | Hannah / 3:30 / 30s | Aryan / 7:37 / 53s | Live: Thank you; we're happy to take questions. Recorded: Thanks for watching. |
| 12–21 | Backup diagrams and Q&A | On demand / 0s scheduled | Not narrated / 0s | Open only as needed. |
| End | | 4:00; Q&A until 5:00 | 8:30 | |

## Anticipated questions

Possible applications include informal campus feedback and conversation starters; these are examples, not additional release requirements.

| Question | Owner | Short answer |
|---|---|---|
| Why revive the Wii channel? | Hannah | The original channel was discontinued in 2013. We want to bring back that experience in modern browsers while expanding its features and practical uses. |
| Why Flask and SQLite? | Aryan | A single application simplifies integration; SQLite offers transactions and relational constraints for our planned scale. |
| How do you prevent repeat votes? | Hannah | Validate the retained guest session or account and enforce database uniqueness within a transaction. Cross-device guest repeats remain a limitation. |
| What happens when someone logs in after voting as a guest? | Hannah | Preserve the guest identity and check prior voting before accepting another vote. |
| How do polls open and close? | Aryan | Compare server UTC time with opening and closing times on each request; no background scheduler required. |
| When are results visible? | Hannah | After an eligible vote or after the poll closes. |
| How is demographic privacy protected? | Hannah | Optional age, gender and region; suppress the entire breakdown when any nonempty group contains fewer than five votes. |
| Are these representative survey results? | Hannah | No; participation is voluntary and may be biased. |
| How will you verify security and usability? | Both | Test invalid and duplicate votes, access control and privacy thresholds; target four of five users completing the task within two minutes, plus a separate keyboard-only check. |
| Will it be publicly hosted? (open) | Aryan | Not finalized. Verify HTTPS and persistent storage before choosing hosting; retain a local fallback. |
| Will users submit polls or compare race/ethnicity? (open) | Hannah | Future scope, not committed. Evaluate moderation, consent and disclosure risks before adding either. |
| What if a database write fails? | Aryan | Roll back definite failures; check vote status after an uncertain write rather than blindly retrying. |

For live Q&A, Hannah answers on behalf of the team and refers to the relevant backup slide; the owner column identifies technical responsibility, not additional live speakers.

## Rehearsal log

- Date/time: November 17, 2026, 6:00–6:30 p.m.
- Attendees: Hannah Krzywkowski and Aryan Balyan
- Format: Online call with PowerPoint screen sharing and timed narration
- Deck: Everybody_Votes_Presentation.pptx
- Timekeeper: Hannah; Aryan checks the handoff and elapsed time
- Objective: Complete a full two-person recorded practice within 5–10 minutes, then practice the solo live version and Q&A.

### Timed results

| Section | Recorded target | Recorded | Live target | Live |
|---|---:|---:|---:|---:|
| Slides 1–2: Introduction | 0:35 | 0:36 | 0:25 | 0:24 |
| Slides 3–5: Product and users | 2:30 | 2:29 | 1:08 | 1:06 |
| Slide 6: Architecture | 1:10 | 1:12 | 0:32 | 0:34 |
| Slides 7–8: Constraints and progress | 1:49 | 1:50 | 0:50 | 0:49 |
| Slides 9–10: Roadmap and roles | 1:33 | 1:34 | 0:35 | 0:36 |
| Slide 11: Demo | 0:53 | 0:52 | 0:30 | 0:28 |
| Hannah recorded subtotal | 4:15 | 4:17 | — | — |
| Aryan recorded subtotal | 4:15 | 4:16 | — | — |
| Total narration | 8:30 | 8:33 | 4:00 | 3:57 |
| Live Q&A | — | — | 1:00 | 0:55 |
| Total live slot | — | — | 5:00 | 4:52 |

### Observations and corrective actions

| Check | Observation | Action | Owner | Status |
|---|---|---|---|---|
| Timing | Recorded run lasted 8:33; speaking balance differed by one second | Keep revised 8:30 allocation | Both | Resolved |
| Speaking pace | Architecture explanation sounded rushed | Pause at the diagram arrows; shorten redundant wording | Hannah | Planned before recording |
| Handoff | Brief silence between slides 6 and 7 | Cue Aryan using the existing handoff line | Both | Practiced |
| Audio | Aryan initially sounded quieter | Adjust microphone distance and test a 20-second clip | Aryan | Retest passed |
| Slide playback | Main slides advanced correctly | Keep backups after closing slide; do not narrate them | Hannah | Check passed |
| Technical accuracy | Demographic privacy explanation needed clarification | Say the entire breakdown is hidden if any nonempty group has fewer than five votes | Both | Practiced |
| Demo explanation | Optional features could sound guaranteed | Explicitly distinguish core demo from planned enhancements | Aryan | Practiced |
| Offline backup | Offline playback on another computer has not been verified | Test on a different computer before class | Aryan | Open |

### Q&A practice

| Question | Speaker | Result / improvement |
|---|---|---|
| Can guests vote twice? | Hannah | Explained retained sessions and database uniqueness; acknowledged cross-device limitation. |
| Why SQLite? | Aryan | Explained transactional integrity and planned scale without claiming unlimited capacity. |
| Are demographic results representative? | Hannah | Explained voluntary participation bias and the suppression rule. |
| What if hosting is unavailable? | Aryan | Described local fallback; acknowledged hosting decision remains open. |

### Rehearsal summary

The full-group rehearsal lasted 8:33, with balanced speaking times. The solo practice stayed within the five-minute limit and left time for questions. We identified areas to improve, including speaking pace, audio consistency, transitions and explanations of optional features. Before recording, we will retest audio and practice the handoff. Before class, we will test the offline presentation on another computer.

### Open items (deadlines)

| Item | Owner | Deadline |
|---|---|---|
| Retest audio and handoff | Both | November 18, 2026 |
| Confirm final deck version and commit | Hannah | November 18, 2026 |
| Test offline backup on another machine | Aryan | November 18, 2026 |
| Record and review final video | Both | November 18, 2026 |
| Post deck and video links | Hannah | November 18, 2026 |