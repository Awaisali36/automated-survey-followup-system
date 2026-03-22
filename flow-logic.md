# Flow Logic — Automated Survey System

## Trigger Conditions

| Folder | Event Type | CSV Format |
|---|---|---|
| `/ChatWalrus Events/` | Live events | Name, Email, Event Title, Event Date |
| `/OTR Zoom Exports/` | Online courses / webinars | Name, Email, Webinar Title, Date |

The system monitors both folders independently.
Each folder has its own workflow trigger — same logic, different metadata mapping.

---

## Data Normalization Rules

```
email     → lowercase + trim whitespace
name      → trim + normalize spacing
duplicate → keep first occurrence, discard subsequent
missing   → skip record, log for review
```

---

## Typeform Exclusion Logic

```
window     = last 48 hours from trigger time
api_call   = GET /responses?since={48_hours_ago}
comparison = attendee_email IN typeform_respondent_emails
result     = TRUE  → exclude from send list
             FALSE → include in send list
```

The 48-hour window ensures:
- Attendees who responded to a previous event's survey are NOT excluded
- Only recent responses (this event or last 48 hours) trigger exclusion
- The window resets on every workflow run

---

## Klaviyo Enrollment Payload

```json
{
  "email": "attendee@example.com",
  "first_name": "First",
  "last_name": "Last",
  "properties": {
    "event_title": "ChatWalrus Workshop — March 2025",
    "event_type": "ChatWalrus" | "OTR",
    "event_date": "2025-03-15",
    "survey_link": "https://typeform.com/to/xxxxx",
    "enrolled_at": "2025-03-15T10:00:00Z"
  }
}
```

---

## Multi-Stage Timing Logic

```
T+0h    → Initial survey email sent via Klaviyo flow
T+24h   → Typeform check: did this email submit a response?
            YES → End flow. No further contact.
            NO  → Schedule reminder for next 9 AM slot
T+9AM   → Reminder email sent
          → Flow ends. Hard stop. No further emails.
```

### Why 9 AM?
Research consistently shows morning emails (8–10 AM) achieve higher open rates than emails sent at arbitrary times. The system calculates the next 9 AM window from the 24-hour check and schedules accordingly.

---

## Anti-Spam Controls Summary

| Control | How It Works |
|---|---|
| 48-hour Typeform exclusion | Prevents re-surveying recent respondents |
| Email deduplication | Removes duplicates within each CSV |
| Hard flow stop after reminder | Maximum 2 emails per attendee per event |
| Response-aware gating | 24-hour check halts flow if response detected |

---

## Error Handling

| Scenario | Behaviour |
|---|---|
| CSV malformed or empty | Workflow logs error, sends alert, stops |
| Typeform API timeout | Retry 3x with 30s backoff, then skip exclusion check with alert |
| Klaviyo enrollment failure | Log failed record, continue with remaining attendees |
| Duplicate CSV upload | Deduplication prevents double-enrollment |
