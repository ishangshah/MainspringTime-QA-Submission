# Scheduling test plan

## 1. Scope

Use one Manager account and one Employee account for the same employee record. Allow 2–3 hours for planning, execution, and reporting.

**Included:** Direct template assignment, manual shifts from both entry points, draft visibility, immediate publication, both generation modes, period publication with and without blocking conflicts, editing, cancellation, missing-employee validation, Employee creation restrictions, and assignment priority. These cover the required flows and protect existing shifts.

**Excluded:**
- Department and Position assignments: only Employee assignment is within the scope.
- Template edits after publication and template deactivation: establish assignment and generation behavior first.
- Availability saving: the UI states that saving is unavailable for employee availability.
- Search, repeated assignment displays, template settings, template selection in Add shift, and overnight presentation: prioritize the twelve selected cases.

## 2. Assumptions

- **A-01: Cycle anchor.** The reference date represents Day 1. Check the mapping before reporting incorrect shift dates.
- **A-02: Permissions.** The Employee cannot perform Manager scheduling actions. Check access rather than relying only on hidden controls.
- **A-03: Timezones.** Both roles see the same published shift, possibly in different timezones. Establish each view's timezone before comparing times.
- **A-04: Editing.** Published changes reach the Employee schedule. Check whether saving publishes the edit or requires publication.
- **A-05: Occupied dates.** Saved shifts make a date occupied for Keep existing. Establish whether recurring entries also count.

## 3. Test Data and Setup

Use the supplied Manager account (owner@demo.com)  and Employee account (employee@demo.com).

**Templates and dates: Not confirmed.** Create templates during setup. Select dates within assignment and pattern validity periods. Use future dates where required.

Before each case, record its actual data in **02-test-results.md**: template, pattern, cycle reference date, assignment dates and priority, shift times, and cancellation reason where applicable. 

Use separate sessions or sign out when changing roles. Check existing shifts, adjacent dates, and weekly hours before each case. Earlier tests must not introduce unrelated conflicts.

Scheduling rules: The Conflicts screen shows an eleven-hour minimum rest gap between shifts and a forty-eight-hour weekly limit. Select test data within these limits, except when deliberately testing a blocking conflict.

Workflows:
Manager: **Schedule**. Employee workspace: **My Time → Schedule**.
Assignments: **Administration → Scheduling → Shift templates → Assignments**.
Generation: **Schedule → three-dot menu → Generate from template**.

## 4. Test Cases

### TC-01: Assign a template directly to the employee

**Priority:** High. **Role:** Manager. **Assumption:** A-01.

**Preconditions:** Active template with recorded pattern and reference date. No competing direct assignment. Test dates fall within validity periods. Expected shifts satisfy overlap, rest-gap, and weekly-hours rules.

**Steps**
1. Record the starting schedule and calculate expected recurring shifts.
2. Open Assignments. Add a direct Employee assignment with the selected template, dates, and priority.
3. Save and refresh. Check the saved values.
4. Open Schedule for the same period. Compare recurring shifts with the expected pattern.

**Expected result:** The assignment persists. The employee appears with the expected recurring shifts. If unresolved cycle mapping prevents comparison, record completed checks and mark the case Blocked.

### TC-02: Add a roster-cell shift as a draft

**Priority:** High. **Roles:** Manager and Employee.

**Preconditions:** Empty future roster cell within the Employee view's date range. Proposed shift satisfies scheduling rules.

**Steps**
1. Employee: Record entries for the test date.
2. Manager: Select the empty cell. Check the employee and date. Enter valid times with **No template** and **Work**.
3. Select **Save draft**. Refresh and reopen the shift.
4. Employee: Refresh and compare with the original entries.

**Expected result:** One manual shift persists with the entered details and Draft status. The Manager can reopen it. The Employee cannot see it. Existing Employee entries remain unchanged.

### TC-03: Add and publish a toolbar shift

**Priority:** High. **Roles:** Manager and Employee.

**Preconditions:** Future date covered by the Employee view. No blocking conflicts apply.

**Steps**
1. Employee: Record the schedule for the test date.
2. Manager: Select toolbar **Add shift**. Enter the employee, date, and times with **No template** and **Work**.
3. Select **Save & publish**. Refresh and reopen the shift.
4. Employee: Refresh and compare dates and times using A-03.

**Expected result:** The shift persists with the entered details and Published status. The Employee sees it after refreshing.

### TC-04: Generate drafts with Keep existing

**Priority:** High. **Role:** Manager. **Assumption:** A-05.

**Preconditions:** Applicable template work on two dates. One date is empty. The other contains a manual shift with different times and duration.

**Steps**
1. Record both dates, including recurring entries. Establish which date counts as empty.
2. Open Generation. Check the scope includes the test employee and both dates.
3. Select **Keep existing**, then **Create draft shifts**.
4. Refresh and compare both dates.

**Expected result:** The empty date receives the applicable template shifts as Draft. The occupied date keeps its manual shift unchanged, with no additional shift or gap-filling.

### TC-05: Replace generated drafts

**Priority:** High. **Role:** Manager.

**Rule:** The generation tooltip protects manual and published shifts.

**Preconditions:** Three shifts within the range: a generated draft differing from its current template, a manual draft, and a published template-generated shift. Confirm each source and status.

**Steps**
1. Record all three shifts and the expected current template times.
2. Open Generation. Record its scope and enter the selected range.
3. Select **Replace generated drafts**, then **Create draft shifts**.
4. Refresh and compare all three records.

**Expected result:** The generated draft matches the current template and remains Draft, without its old version. Manual and published shifts retain their dates, times, and status.

Unchanged times alone cannot prove regeneration. Without observable replacement evidence, mark the case Blocked and record completed preservation checks.

### TC-06: Publish a conflict-free period

**Priority:** High. **Roles:** Manager and Employee.

**Preconditions:** Drafts exist. No blocking or warning conflicts affect the publication scope. The Employee view covers the dates.

**Steps**
1. Manager: Record draft details and any recurring shifts they replace.
2. Employee: Record existing entries. Check the new drafts are absent.
3. Manager: Record publication scope. Review **Conflicts** and **Needs attention**.
4. Select main **Publish** and complete its confirmation.
5. Refresh both views. Compare status, dates, and times using A-03.

**Expected result:** Drafts become Published and appear to the Employee. Replaced recurring entries do not remain as additional active shifts. Unrelated shifts remain unchanged.

### TC-07: Block publication for insufficient rest

**Priority:** High. **Role:** Manager.

**Rule:** The Conflicts screen identifies eight hours of rest as a blocking violation of the eleven-hour minimum.

**Preconditions:** Two non-overlapping shifts have less than eleven hours between them. At least one is Draft. No other blocking conflicts affect publication.

**Steps**
1. Record both shifts. Calculate the gap between the first end and next start.
2. Open Conflicts. Check the employee, affected shifts, gap, and severity.
3. Check main Publish. If enabled, attempt publication and record the response.
4. Refresh and check the draft's status.

**Expected result:** The correct gap appears as Blocking. Publication is prevented. The draft remains Draft.

### TC-08: Edit a published shift

**Priority:** High. **Roles:** Manager and Employee. **Assumption:** A-04.

**Preconditions:** Future published shift visible in both views. Proposed end-time change creates no blocking conflict.

**Steps**
1. Record the original shift in both views.
2. Manager: Open the shift, select **Edit**, and change only its end time.
3. Save. Record the action and resulting status. If Draft, publish the change through Schedule.
4. Refresh both views and compare the shift using A-03.

**Expected result:** The published shift has the revised end time. Employee, date, and start time remain unchanged. The Employee sees the update without an additional active copy showing the old time.

### TC-09: Cancel a published shift

**Priority:** High. **Roles:** Manager and Employee.

**Preconditions:** Future published manual shift visible in both views.

**Steps**
1. Record the shift and other entries on its date in both views.
2. Manager: Open the shift and select **Cancel shift**.
3. Enter the cancellation reason and confirm.
4. Refresh both views and check the same date.

**Expected result:** The shift no longer appears as active. It is absent or clearly marked Cancelled. Other shifts remain unchanged.

### TC-10: Reject a missing employee

**Priority:** Medium. **Role:** Manager.

**Preconditions:** Add shift has no employee selected. All other values are valid, with No template and Work.

**Steps**
1. Check **Save draft**. If enabled, select it and record the response.
2. Repeat for **Save & publish** with Employee still blank. Reopen the form if necessary.
3. Select Emily. Check that the missing-selection restriction clears. Close without saving.

**Expected result:** Neither action creates a shift without an employee. Saving is disabled or produces a clear validation message. Any successful save with Employee blank is a failure.

### TC-11: Restrict Employee shift creation

**Priority:** Medium. **Roles:** Manager and Employee. **Assumption:** A-02.

**Preconditions:** Manager Schedule address recorded. Employee session confirmed through its profile. Valid manual-shift data available.

**Steps**
1. Manager: Record the starting schedule.
2. Employee: Open the Manager Schedule address. Check whether Add shift is available.
3. If available, enter valid data and attempt **Save draft**. Record the response.
4. Manager: Refresh and check for a new shift.

**Expected result:** Creation is unavailable, disabled, or rejected. No shift appears. A validation error alone does not prove permission enforcement. This case covers creation only.

### TC-12: Apply the higher assignment priority

**Priority:** Medium. **Role:** Manager.

**Rule:** The assignment tooltip states that higher priority wins within the same level.

**Preconditions:** Two active templates have different times on the same date. No saved shifts or other direct assignments affect comparison.

**Steps**
1. Record each template's expected shifts.
2. Create the higher-priority direct assignment first.
3. Create the lower-priority direct assignment with identical effective dates.
4. Refresh Assignments and check both priorities. Compare Schedule with both patterns.

**Expected result:** Recurring shifts follow the higher-priority assignment. The lower-priority template contributes no additional recurring shifts on the test date.

## 5. Risks

- **Existing data:** Seeded shifts and earlier tests can introduce unrelated conflicts. Check adjacent shifts and weekly hours before each case.
- **Employee visibility:** Date range limits, timezone differences, or an outdated page can make shifts appear missing or incorrect. Check the date range and timezone. Refresh both views before comparing.
- **Cycle mapping:** An incorrect cycle reference date interpretation can produce incorrect expected shift dates. Check how cycle days map to calendar dates.
- **Action scope:** Generation and publication may affect more than the selected employee. Check the action’s scope before proceeding.
