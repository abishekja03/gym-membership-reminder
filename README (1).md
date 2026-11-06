# Gym Membership Expiry Reminder Automation

An n8n workflow that checks gym membership expiry dates, filters records using membership and reminder-status conditions, sends reminder emails through Gmail, and updates Google Sheets after sending.

## Tech Stack

- **n8n** — workflow orchestration and scheduled execution
- **Google Sheets** — membership data source and reminder-status tracking
- **JavaScript** — expiry-date calculation and reminder eligibility
- **Gmail** — email notifications

## Workflow

1. **Schedule Trigger** starts the workflow on the configured schedule.
2. **Get row(s) in sheet** reads membership records from Google Sheets.
3. **Code in JavaScript** calculates `days_until_expiry` and sets `should_remind` when the expiry date is between today and three days away (inclusive).
4. **If** checks `should_remind`.
5. **If1** applies the configured payment-status condition.
6. **If2** checks whether `Remainder_status` is empty, helping avoid sending a second reminder to records already marked as sent.
7. **Send a message** sends the HTML reminder through Gmail.
8. **Append or update row in sheet** updates the matching member row and sets `Remainder_status` to `SENT`.

## Google Sheets Columns

The workflow expects these columns:

- `Member_ID`
- `Name`
- `Email`
- `Phone`
- `Membership_plan`
- `Start_date`
- `Expiry_date`
- `Payment_status`
- `Remainder_status`

Use `Member_ID` as the unique matching key. The workflow uses the spelling `Remainder_status` in its current configuration.

## Setup

1. Import `gym-membership-expiry-reminder-public.json` into your n8n instance.
2. Create or select a Google Sheet with the columns listed above.
3. Connect your own Google Sheets account in the relevant n8n nodes and select your spreadsheet and worksheet.
4. Connect your Gmail account in the Gmail node.
5. Review the conditions in all three If nodes and confirm they match your sheet's actual values and capitalization.
6. Confirm the schedule and reminder window in the Schedule Trigger and JavaScript Code node.
7. Test with a sample row and an email address you control before enabling the workflow for real members.

## Important Configuration Note

The exported workflow's `If1` condition currently compares `Payment_status` to the lowercase text `pending`. The comparison is case-sensitive. If your sheet uses values such as `Pending`, update the condition or normalize the value before comparison.

The exported workflow is provided without account credentials and spreadsheet identifiers. Reconnect your own services after importing it.

## Security

- Do not commit real member names, email addresses, phone numbers, or membership records.
- Do not commit OAuth tokens, API keys, passwords, or credential exports.
- Use dummy data in screenshots and examples.
- Review the exported workflow JSON before publishing it.

## Repository Contents

- `README.md` — project documentation
- `gym-membership-expiry-reminder-public.json` — sanitized n8n workflow export
- `workflow-screenshot.png` — workflow overview screenshot (optional)

## Future Improvements

- Add configurable reminder intervals (for example, 7 days, 3 days, and 1 day before expiry).
- Add error handling and execution logging.
- Add a separate notification path for failed email delivery.
- Generate periodic membership and reminder reports.
