# Scheduling QA: findings

**Total time spent: 4 hours 5 minutes.** Planning: 1 hour 20 minutes. Execution: 2 hours. Reporting: 45 minutes.

**Execution date:** November 4, 2026.

I recorded **12 Pass, 0 Fail, and 0 Blocked**, with coverage limits in [02-test-results.md](02-test-results.md). I identified two usability concerns but did not establish a functional failure.

## F-01: Schedule Total does not explain draft and replacement-entry hours

**Severity: Minor.** The unclear total can mislead managers about planned hours, although the recorded publication and Employee visibility checks passed.

**Role and context:** Manager login: owner@demo.com. Workspace: Schedule → Team grid at https://dev.time.themainspring.ai/. Employee: Em Rodriguez. Schedule period: November 16–22, 2026. Related cases: TC-02, TC-04, and TC-06.

**Steps to reproduce**

Scenario A starts with the TC-01 schedule: three eight-hour entries on November 16, 17, and 19, totaling 24 hours.

1. Add a manual Work shift on November 18 at 09:00–17:00.
2. Select Save draft.
3. Check Total, which increases to 32h.

Scenario B starts with the TC-04 schedule, totaling 48 hours. November 20 has a published manual shift at 09:00–17:00. November 21 has an eight-hour recurring entry from template Ishan-Template1, template ID 100, and no saved shift.

1. Generate November 20–21 with Keep existing.
2. Check the recurring entry and generated draft on November 21.
3. Check Saturday's 16h total and the 56h weekly total.

The generation dialog included all 48 accounts. I assigned the template only to Em Rodriguez and checked only that employee.

**Expected:** Total should explain whether it counts unpublished drafts and both entries in a pending replacement. This is a proposed usability expectation that needs product agreement.

**Actual:** The heading remains TOTAL as hours increase from 24h to 32h after saving a manual draft, then from 48h to 56h after generation. In TC-06, I saw No conflicts before publication and one Saturday shift with a 48h weekly total after publication. In TC-05, I changed the replacement shift's times before that check but kept its eight-hour duration.

**Evidence:**

- [tc-02-manager-roster.png](screenshots/tc-02-manager-roster.png): 32h total with an unpublished draft.
- [tc-04-roster-before-generation.png](screenshots/tc-04-roster-before-generation.png): 48h before generation.
- [tc-04-roster-after-generation.png](screenshots/tc-04-roster-after-generation.png): 56h total, including both Saturday entries.
- [tc-06-conflicts-before-publication.png](screenshots/tc-06-conflicts-before-publication.png): No conflicts before publication.
- [tc-06-manager-after-publication.png](screenshots/tc-06-manager-after-publication.png): 48h total and one Saturday shift after publication.

**Classification: Probably by design, but confusing.** Draft hours can help managers plan, and the brief explicitly permits a recurring entry beside its replacement draft. The product team should define which hours Total counts and explain that meaning in the UI. The 56h total alone does not prove a weekly-limit failure because the final schedule contained 48 hours.

**Status: Seen once.** I observed this behavior during one sequence. I did not repeat it from a clean starting state.

## F-02: Assignments list makes an employee's templates difficult to review

**Severity: Minor.** The layout adds work when managers compare assignments, but the recorded assignment and priority checks passed.

**Context:**. Workspace: Administration → Scheduling → Shift templates → Assignments at https://dev.time.themainspring.ai/. Employee: Em Rodriguez. Assignment periods: November 16–22 and November 30–December 6, 2026. Related case: TC-12.

**Steps to reproduce**

Starting state: Em Rodriguez has the three saved assignments shown in the TC-12 evidence, with different templates, priorities, and effective dates.

1. Open Assignments as Manager.
2. Enter Ishan in the search field.
3. Identify the assignments that apply to Em Rodriguez on November 30.

**Expected:** Managers should be able to review an employee's assignments for a selected date and distinguish earlier and future assignments. This is a proposed usability expectation.

**Actual:** The list repeats Em Rodriguez across three rows. Ishan-Template1, effective November 16–22, appears between Ishan-Priority-A and Ishan-Priority-B, both effective November 30–December 6. I had to compare dates across rows. Search narrows the list but does not group the employee's assignments in the captured view. I found the default view repetitive and unintuitive when reviewing all assignments for one employee.

**Evidence:** [tc-12-saved-assignments.png](screenshots/tc-12-saved-assignments.png) shows the three rows and their different dates and priorities.

**Classification: Needs a product decision.** I recommend grouping options by employee or template. Employee groups could open current assignments, future assignments, and history. Template groups could show the employees assigned to each template. These options would help managers review related assignments together instead of comparing repeated rows in the default list. The screenshot does not establish the sorting rule or prove that other filters are unavailable. Ten assignments would be a useful future usability test, but the captured example contains three.

**Status: Seen once.** I encountered this difficulty during assignment review. I did not run a separate usability test to measure its frequency or impact.

## Summary table

| ID | Title | Severity | Classification | Status |
| --- | --- | --- | --- | --- |
| F-01 | Schedule Total does not explain draft and replacement-entry hours | Minor | Probably by design, but confusing | Seen once |
| F-02 | Assignments list makes an employee's templates difficult to review | Minor | Needs a product decision | Seen once |

## Reflection

**If I had one more hour:** I would test whether Publish, Save & publish, and edits to published shifts enforce the same conflict rules. I checked a blocking conflict through Publish in TC-07. I used valid shifts in TC-03 and TC-08. I would check whether each action saves an invalid shift or shows it to the Employee. I would also check that rejected actions leave existing shifts unchanged. I would also extend a weekly template and its assignment across several weeks. I would check that shifts repeat on the correct cycle days and stop after the effective end date. The current cases cover only one week per assignment.

**Finding I am least sure is a bug:** F-01 remains uncertain because draft hours may help managers plan. The brief also permits a recurring shift beside its replacement draft. I need the product team to define Total and explain how it relates to conflict checks. This would clarify whether the label or the calculation needs to change. After publication, Saturday correctly showed one shift.

**UI element that most hindered testing:** Total did not explain which shifts it counted. The roster showed 56 hours and No conflicts, then 48 hours after publication. I had to compare individual shifts to understand the difference. The shift details helped because they showed each shift's source and status. An explanation of how Total counts drafts, published shifts, and pending replacements would make planning and testing easier.

**First automation:** I would use Playwright to automate TC-02, then publish the draft and check both accounts. The Manager should see the saved draft, and the Employee should see the shift only after publication. Next, I would automate TC-05 to check that regeneration updates the intended draft without changing manual or published shifts.
