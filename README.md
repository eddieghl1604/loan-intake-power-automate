# Loan Intake Automation (Power Automate)

**▶ Demo video (5 min):** \[https://youtu.be/cwCUd5LRXTo]

A loan application intake process that used to run on WhatsApp, retyping and a calculator, rebuilt as one Power Automate cloud flow. Applications arrive through a form, the debt service ratio (DSR) is calculated automatically, incomplete or high-risk cases are caught before a reviewer opens them, and every decision updates the record and notifies the client.

Based on a manual process I worked with as a part-time mortgage consultant assistant. All applicants and figures shown are test data.

## The problem

|Before|Pain point|
|-|-|
|Client sends details by WhatsApp / email|Details scattered and often incomplete|
|Assistant retypes them into a spreadsheet|Typing errors, repeated for every case|
|Documents checked by eye|Missing documents easily overlooked|
|DSR worked out on a calculator|Calculation mistakes, no consistent rule|
|Reviewer chased by message|Delays, no status view, no audit trail|

!\[Before](docs/process\_map\_before.png)

## The solution

!\[After](docs/process\_map\_after.png)

1. **Microsoft Forms** collects the application.
2. The flow saves it to a **SharePoint list** and calculates **DSR = (existing commitments + new instalment) ÷ net income × 100**.
3. **Document check:** if any of the four required documents is missing, status becomes *Incomplete* and the client is emailed a checklist. The case never reaches the reviewer.
4. **Risk flag:** if DSR is above 60%, status becomes *Flagged* so the reviewer sees the warning.
5. **Approvals:** the reviewer approves or rejects in Teams or Outlook, with the DSR and a link to the record on the card.
6. The flow writes the decision and the reviewer's comment back to the list, emails the client, and posts a summary in Teams.

**Tools:** Power Automate, Microsoft Forms, SharePoint Lists, Approvals, Outlook, Teams

## Business rules

|Rule|Value|Note|
|-|-|-|
|Required documents|IC copy, 3 months' payslips, 6 months' bank statements, EPF statement|All four needed to proceed|
|DSR flag threshold|Above 60%|Demo assumption; banks set their own limits. Exactly 60% is not flagged|
|Reviewer|One approver, first to respond|Single reviewer for the demo|

## Test results

|#|Scenario|Inputs (income / existing / new)|Expected|Result|
|-|-|-|-|-|
|1|Missing document|6,000 / 1,200 / 2,100, EPF missing|Incomplete + checklist email|✅|
|2|Normal, awaiting review|6,000 / 1,200 / 2,100|DSR 55.0%, Submitted|✅|
|3|High DSR, awaiting review|4,000 / 1,500 / 1,500|DSR 75.0%, Flagged|✅|
|4|Normal, approved|6,000 / 1,200 / 2,100|Approved + comment, email, Teams|✅|
|5|High DSR, rejected|4,000 / 1,500 / 1,500|Rejected + comment, email, Teams|✅|

Screenshots of each are in [`screenshots/`](screenshots/).

## Problems I hit and how I fixed them

* **Numbers arrived as text.** Forms sends every answer as text, so SharePoint number columns wouldn't accept them. Wrapped each in `float()`.
* **Approval failed to create.** I had assigned it to the applicant's email, which is outside the organisation. Approvals only work inside the tenant, so I separated the reviewer from the applicant.
* **Empty fields after a branch.** The decision email's "To" came from a step inside a branch that didn't run. Switched to a step that always executes.
* **Floating-point display.** DSR showed as 55.00000000000001%. Used `formatNumber(..., '0.0')` for display only, leaving the value used in the logic unchanged.
* **Restricted permissions.** Site creation was blocked on my account, so the data lives in a personal Microsoft List rather than a team site.

## Limitations and next steps

* Approving a flagged case overwrites *Flagged* with *Approved*, so the flag isn't kept. A separate Yes/No "HighDSR" column would preserve it for audit.
* Documents are ticked, not uploaded. A next version would use a file-upload question and store files in a SharePoint folder per applicant.
* No reminder if the reviewer doesn't respond; an approval timeout plus escalation would fix that.

## Estimated impact (assumption, not measured)

Time saved per month = (manual minutes per case − automated minutes per case) × cases per month
= (20 − 5) × 40 = 600 minutes ≈ **10 hours/month**. The 20, 5 and 40 are my own assumptions for a small mortgage team.

## Repo contents

```
docs/requirements.md        requirements note
docs/user-guide.md          one-page guide for the reviewer
docs/process\_map\_\*.png      before/after maps (.drawio files are editable)
screenshots/                test evidence
solution/                   exported Power Automate solution (.zip)
```

**To import:** Power Automate → Solutions → Import solution → choose the .zip, then reconnect the Forms, SharePoint, Approvals, Outlook and Teams connections and point the actions at your own form and list.

