# ops-control-tower
Enterprise Operations &amp; Incident Management Dashboard
Ops Bridge - Production Incident Command Center
A single operating view for managing production incidents end to end - from the moment an issue is raised through RCA, root cause routing, and closure. Built on GitHub Pages with GitHub Actions handling data persistence. No server, no database, no cost.
Live URL: https://vijay5412.github.io/ops-control-tower/
---
Access
There are two ways to use this dashboard.
Read-only (team view)  
Open the URL and click "View Dashboard (Read-only)". No login, no token. You can view all incidents, filter, search, and export. This is what most people on the team will use.
Admin access  
Open the URL, enter the admin password and your GitHub Actions token, then sign in. Admin can add, edit, and delete incidents. Contact Vijay for the admin password.
The GitHub Actions token is a fine-grained PAT with actions:write permission on this repo. It is never stored in the code - you enter it at login and it stays in browser memory for that session only. When you close the tab it's gone.
---
How to log a production incident
Step 1 - Incident raised
Log in as admin and click Add Incident. Fill in the title, problem statement, criticality (High or Medium), assignee (tech lead), and reporter (PO or Risk Criteria team). Add a first log entry noting who raised the issue and what initial actions were taken. Leave Root Cause Type blank - you won't know this yet. Save it. The SLA clock starts from this moment.
For High criticality incidents the dashboard will warn you if there is no SRE incident number linked. Make sure the SRE team has been engaged and paste their ticket reference in the SRE Incident field. Type the number and it will auto-format to SRE-XXXX.
Step 2 - 24hr placeholder call
Within 24 hours of the incident being logged, PO schedules a placeholder call with Tech, Risk Criteria, and SRE. Edit the incident and add a log entry documenting the call - who attended, what was discussed, initial RCA direction, and whether more time is needed. Status stays as Analysis in Progress.
If the SLA indicator turns amber (lightning bolt), the 24hr window has passed and the call is overdue.
Step 3 - RCA complete
After the 48hr follow-up call, the root cause should be clear. Edit the incident and set the Root Cause Type:
Bug or code issue: Create a Jira defect and paste the reference (e.g. DEF-4821) in the Jira/Intake Ref field. Change status to Validation in Progress.
Requirement gap: Submit an Intake to the Risk Criteria team and paste the reference (e.g. INT-0892) in the Jira/Intake Ref field. Set the Release Quarter once PBR is complete. Change status to Validation in Progress.
Add a log entry documenting the root cause, what was found, and what action has been taken.
Step 4 - Fix or intake delivered
Once the fix is deployed to production or the intake has been worked on and deployed in a release cycle, edit the incident and change status to Resolved. Add a log entry confirming what was done.
Step 5 - Verified and closed
PO and Risk Criteria team verify the fix in production. Edit the incident, change status to Closed, and add a final log entry confirming verification. The SLA clock stops.
---
SLA indicators
The SLA column in the table shows how long an incident has been open.
Green: within 24 hours, on track
Amber with lightning bolt: past 24 hours, follow-up RCA call is due
Red with warning triangle: past 48 hours, SLA breach, needs immediate attention
The SLA clock stops automatically once an incident is marked Resolved or Closed. The SLA Breaches KPI card at the top shows how many incidents are currently in breach.
---
Filters and export
Use the filter bar to narrow down incidents by status, criticality, root cause type, release quarter, or assignee. The search box searches across incident number, title, assignee, reporter, and Jira/Intake references.
Click Clear to reset all filters at once.
CSV export downloads an Excel-ready file with all fields including log count and age in days. Print opens a clean report formatted for management meetings. JSON export downloads the raw data file.
If you are logged in as admin, pressing N on the keyboard opens the Add Incident form.
---
Architecture
The dashboard is a single HTML file served via GitHub Pages from the main branch. All incident data is stored in data/issues.json in the same repo. When admin saves an incident, the dashboard calls the GitHub Actions API to trigger the save-data.yml workflow. That workflow runs on GitHub's servers and commits the updated JSON file back to main using GITHUB_TOKEN. The dashboard auto-refreshes every 30 seconds so all users see the latest data without doing anything.
The admin password is hardcoded in index.html. The GitHub token is never in the code - it is entered at login only.
---
Repo structure
```
ops-control-tower/
    index.html                          dashboard UI and logic
    data/issues.json                    all incident data
    .github/workflows/deploy.yml        deploys HTML to GitHub Pages on push to main
    .github/workflows/save-data.yml     writes issues.json when admin saves
    README.md                           this file
```
---
When moving to the office org account
The setup is identical. Create a repo under the org, copy these files across, set the ADMIN_PASSWORD secret in repo settings, enable GitHub Pages from main branch, and generate a new fine-grained PAT under the org account. The only thing that changes is the GitHub Pages URL and the repo owner in index.html (REPO_OWNER variable).
---
Vijay Murugesh  
Senior MuleSoft Architect  
vijay5412 / ops-control-tower
