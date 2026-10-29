# Requirements note – Loan Intake Automation

## Users
- **Client:** submits a loan application.
- **Loan assistant:** collects details and documents, prepares cases.
- **Reviewer:** decides whether a case goes forward.

## Pain points today
- Details arrive in WhatsApp and email and are retyped into a spreadsheet.
- Documents are checked by eye, so missing ones slip through.
- DSR is calculated by hand, with no consistent rule.
- Decisions are chased by message, so there is no status view or record of who decided what.

## Goals
- One place where every application and its status lives.
- No retyping: details flow from the form into the record.
- Incomplete applications never reach the reviewer.
- High-DSR cases are visible before review.
- Every decision is recorded with the reviewer's comment, and the client is told automatically.

## Business rules
| Rule | Detail |
|---|---|
| Required documents | IC copy, 3 months' payslips, 6 months' bank statements, EPF statement |
| DSR formula | (existing monthly commitments + new monthly instalment) ÷ net monthly income × 100 |
| Flag threshold | DSR above 60% (assumption for the demo, to confirm with the business) |
| Statuses | Submitted → Incomplete / Flagged → Approved / Rejected |
| Notifications | Client by email at each outcome; team summary in Teams |

## Out of scope (this version)
Document upload, multiple reviewers, reminders for late approvals.
