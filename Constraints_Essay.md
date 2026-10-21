# Everybody Votes — Design Constraints Essay

**Team Members:** Hannah Krzywkowski and Aryan Balyan

## Economic

Everybody Votes has a $0 project budget, so all required development tools, libraries, APIs, database services, and hosting must be free, open source, or available through a free tier throughout the two-semester senior design project. No required feature may depend on paid APIs, commercial software licenses, subscriptions, temporary trial credits, or recurring hosting fees. The selected technologies must support user accounts, voting, predictions, results, personal statistics, and response comparisons during development, testing, and the final demonstration without exceeding the $0 budget.

## Ethical

Because Everybody Votes collects users' opinions, poll questions and their two to six predefined answer choices must use neutral wording that does not intentionally influence users toward a particular response. Current poll results will remain hidden until a user submits a vote, and displayed percentages will be calculated from valid recorded responses without selectively excluding or weighting votes to favor a particular outcome. Demographic questions used for response comparisons, including age range, gender, and broad geographic region, will remain optional so users can participate without disclosing this information.

## Security

Everybody Votes will allow guests to vote and view results but require authentication for account-specific features, store passwords using secure one-way password hashing rather than plaintext, and enforce authorization controls to prevent users from accessing or modifying another user's account information, saved votes, predictions, or personal statistics. To minimize privacy risks, the application will not collect real names, exact birth dates, street addresses, phone numbers, or precise GPS locations, and demographic comparison results will only be displayed for subgroups containing at least five recorded responses to the relevant poll. This creates a trade-off between Security and Diversity and Cultural considerations: collecting more detailed demographic information could support more specific comparisons across user groups, but would increase privacy risks, so the team will collect only optional, broad demographic categories and suppress results for groups with fewer than five responses.

## Diversity & Cultural

Because Everybody Votes is intended for users with different cultural backgrounds, lifestyles, and experiences, each poll will support two to six predefined answer choices rather than the original Wii Everybody Votes Channel's two-choice format, allowing a broader range of opinions to be represented. Poll questions and answer choices must avoid unnecessary assumptions about users' cultures, beliefs, or personal experiences, and every optional demographic field will provide a way to decline to answer. When a poll's subject could leave users without an accurate response, its answer choices will include an appropriate alternative such as "Unsure," "Neither," or "Prefer not to answer" rather than forcing users to select an option that misrepresents their opinion.
