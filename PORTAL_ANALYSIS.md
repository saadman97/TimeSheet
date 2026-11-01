# Timesheet Portal — Feature, Logic and UX Analysis

Date: 1 Nov 2026

Companion to `REPORT.md` (reference gathering). This report covers what the current SELISE Timesheet portal does, how it appears to work, and where its UX falls short.

## Sources and limits
- Live portal: SELISE portal (Corteza), namespace `STS`, explored in the browser as a normal (non-admin) user.
- Figma file "Timesheet": at the time of exploration it held only two empty pages (`Cover`, `Experiment`).
- The "GIT" link originally supplied was the portal page itself, so no portal code was reviewed.
- The user is not an admin, so listing modules is blocked. Fields, rules and workflows below are inferred from the UI and from the requests the pages make, not confirmed from the configuration.
- Only a narrow (about 800 px) layout was seen.
- Personal timesheet figures and customer data are intentionally left out of this report.

## Portal concept
- A Corteza low-code app: a Compose namespace (`STS`, "Timesheet"). Pages, forms, tables, charts and buttons are all configured in Corteza.
- SELISE Market-Based Management branding, signed in through company single sign-on.
- Shell: left sidebar (namespace switcher, page search, five pages), top bar with Menu, help and avatar.
  - User menu: Reminders, Profile, Change password, Light/Dark theme, Logout.
  - Help menu: Documentation, Send feedback.
- Purpose: each person logs work against customer projects, and the portal totals billable and non-billable hours per day, month and year.

## Features by page

| Page | What it does |
|---|---|
| Home | Buttons **Create Timesheet for Today** and **Add Logs for Past Days**. A table of daily timesheets (Date, Total Hours, Undertime; 30 per page, newest first). Stat tiles: Total Hours YTD, Total Billable YTD, Total Non-Billable YTD, Total Hours This Month. A chart of hours by customer for the year, with a refresh icon. |
| Daily timesheet | Opens as a pop-up from Home or as a full edit page. Holds the date, four totals (Total Hours, Billable, Non-billable, Undertime), an Edit button, an **Import all calendar logs** button and a table of log lines. |
| All Task | Flat table of every log line with search, per-column filters and sort. Columns: Date, Start Time, End Time, Client Name, Project Name, Description, Billable Unit, Billable Hours, General Admin, Sales & Marketing, Total Hours. Row menu offers only Reminder and Delete; rows are not clickable. |
| Report | List of saved report requests (Start Date, End Date, Billable/Non-billable). **+ Add** opens "Create Resource Report" with a Billable/Non-billable dropdown, Start Date and End Date, all required. |
| Customer and Project List | Read-only reference list of about 1,160 customer/project pairs (20 per page), with search and filters. Columns: Customer, Project Name, Business Unit. Inactive projects carry "(Inactive)" in the name. |
| Feedback | One-column list with **+ Add**. |

## Logic (inferred)

**Data model**
- Timesheet: one record per person per day. Fields: Resource Name (the user), Date, Holiday, LeaveTaken, Total Hours, Undertime.
- Log line: belongs to a timesheet. Fields: Start Time, End Time, Client Name, Project Name, Task Type, Description, Billable Unit, Billable Hours, General Admin hours, Sales & Marketing hours, Service Type (blocks/ITO only), On Site.
- Customer/Project: carries the Business Unit (Retail & Services, IT Operations, Marketing, Sourcing, Insurance & Banking, Signature).
- Resource Report and Feedback are separate record types.

**Behaviour**
- *Create Timesheet for Today* runs a server workflow. It creates today's timesheet if none exists, pre-fills one default zero-hour log line (General Admin / General Ops / Total Experience Lab / On Site = No) and opens the record in a pop-up. It does not first ask for confirmation, and it creates real data in one click.
- Editing: the log table is edited inline with an **+ Add** row button. Client and Project come from the customer list. Hours are number fields with arrows. The page prints tips (shift+scroll to move sideways, hold the arrows to change time faster, the customer list is at the bottom of the page).
- Totals: a day's total is the sum of its log hours. Undertime is probably 8 h minus the day total (logged days show 8.00 h and 0.00 h undertime). Billable is the sum of Billable Hours; non-billable is the remainder.
- Stat tiles run live sum queries filtered by the current user and by year (and month for the monthly tile).
- Sundays are normal working days, so the weekend looks like Friday and Saturday.
- A day can hold several log lines (for example two 4 h lines on different projects).
- Leave is logged as an 8 h line on a General Admin project with a leave description. The timesheet also has Holiday/LeaveTaken flags, and the Home list excludes days carrying either flag.
- "Import all calendar logs" points to a calendar integration; it was not run.
- Every query is filtered by the logged-in user, so people see only their own data.
- Deleting a timesheet is only possible from the Home table: tick the row, click the trash icon, then confirm with a small red check. The record page itself has no Delete button.

## Current UX

**Works well**
- Daily entry is quick: one click creates a pre-filled day.
- Inline log editing, the in-page customer list and the keyboard tips help.
- Large stat tiles read clearly. The type (Poppins), teal brand colour and rounded cards are consistent.
- A dark theme option exists.

**Problems**
1. Slow, spinner-only loading. Home can take 30 to 60 seconds to show data, with several spinners at once.
2. Misleading empty state: Home shows "0 records" or "no records" while data is still loading or after a failed query.
3. Dead-end row menus: All Task rows offer only Reminder and Delete, with no view or edit action.
4. Start Time and End Time columns are blank in the rows seen, but still take up width.
5. Column labels do not match content: "Billable Unit" holds the business unit, and "Description" sometimes holds task-type text.
6. Report and Feedback are bare tables. Nothing says what a report produces or where to get it; Feedback has no status.
7. All Task has 11 columns and forces horizontal scrolling; the shift+scroll tip shows this is a known problem.
8. The 1,160-row project list mixes inactive projects (marked only by text in the name) with active ones.
9. Nothing flags days with no timesheet or fewer than 8 hours, and there is no month calendar view.
10. The same daily data is reachable in three places (Home, All Task, the pop-up), and the pop-up and the edit page differ.
11. Destructive actions are hard to find and easy to miss: the timesheet delete flow is hidden behind a checkbox, and its confirm step is a tiny inline icon.

## Open questions
- Figma designs or a brief for what to change.
- Redesign of the existing screens, a rebuild outside Corteza, or both.
- Admin read access to the namespace for exact fields, validations and workflows.
