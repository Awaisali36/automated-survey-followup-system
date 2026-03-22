# Automated Customer Survey System with Smart Follow-ups
### Built for ChatWalrus — Zero Manual Distribution. Zero Spam. Maximum Response Rates.

<div align="center">

![Type](https://img.shields.io/badge/Type-Delivered%20Client%20System-%233A7CFF?style=for-the-badge)
&nbsp;
![Trigger](https://img.shields.io/badge/Trigger-CSV%20Upload-%2300C853?style=for-the-badge)
&nbsp;
![Anti--Spam](https://img.shields.io/badge/Anti--Spam-48hr%20Exclusion%20Logic-%23FF6B35?style=for-the-badge)

</div>

<br/>

---

## What This Is

A fully automated survey distribution system delivered for **ChatWalrus** — handling post-event feedback collection across two event types with zero manual intervention, built-in anti-spam logic, and smart multi-stage follow-up timing.

The system activates the moment a CSV is uploaded to Google Drive. Everything else — parsing, deduplication, response checking, email sequencing, and follow-up timing — runs without a human involved.

<br/>

---

## The Problem

Event organizers and course providers running regular events face the same operational friction every time:

- Someone has to manually export the attendee list
- Someone has to send the survey email
- Someone has to track who responded and who didn't
- Someone has to send reminders — without accidentally emailing people who already responded
- If they run multiple event types, this process doubles

The result: surveys go out late, reminders get sent to people who already responded, response rates stay low, and the whole process eats hours that should be spent on the actual event.

<br/>

---

## How the System Works

```
┌─────────────────────────────────────────────────────────────────┐
│              CSV UPLOADED TO GOOGLE DRIVE                       │
│         (ChatWalrus Events folder OR OTR Zoom folder)           │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    PARSE & CLEAN                                 │
│   Download CSV · Parse attendees · Normalize names & emails     │
│   Deduplicate addresses · Enrich with event metadata            │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                 TYPEFORM RESPONSE CHECK                         │
│     Fetch all survey responses from last 48 hours               │
│     Cross-reference against attendee list                       │
│     Exclude anyone who already submitted → prevents spam        │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                  ┌─────────┴──────────┐
                  ▼                    ▼
           Already responded      Not yet responded
           → Skip entirely        → Add to Klaviyo list
                                  → Trigger survey flow
                                         │
                            ┌────────────▼────────────┐
                            │                         │
                     ┌──────▼──────┐         ┌───────▼──────┐
                     │  IMMEDIATE  │         │  24HR CHECK  │
                     │ Survey email│         │ Did they     │
                     │    sent     │         │ respond?     │
                     └─────────────┘         └──────┬───────┘
                                                    │
                                        ┌───────────┴───────────┐
                                        ▼                       ▼
                                   Responded             Not responded
                                   → Flow ends        → 9 AM reminder sent
                                                       → Flow ends completely
```

<br/>

---

## Key Capabilities

### Two Event Type Support
The system handles two distinct CSV formats from two separate Google Drive folders:
- **ChatWalrus Events** — live event attendees
- **OTR Zoom Exports** — online course and webinar participants

Each type carries different metadata (event title, type, date) that gets passed through to Klaviyo for personalized messaging.

### 48-Hour Anti-Spam Logic
Before any email is sent, the system queries the Typeform API for all survey responses submitted in the last 48 hours. Anyone who already responded — from any previous trigger — is excluded entirely. No duplicate survey requests. Ever.

### Smart Multi-Stage Follow-up
| Stage | Timing | Condition |
|---|---|---|
| Initial survey email | Immediately on trigger | Attendee not in 48hr exclusion list |
| Response check | 24 hours after initial send | Always runs |
| Reminder email | 9 AM if no response found | Only if check shows no submission |
| Flow end | After reminder | Hard stop — no further contact |

### Data Normalization
Every attendee record is cleaned before processing:
- Email addresses lowercased and trimmed
- Names normalized (consistent spacing and casing)
- Duplicates removed within the same CSV
- Event metadata (title, type, date) attached to every Klaviyo contact

<br/>

---

## Results Delivered

| Metric | Outcome |
|---|---|
| Manual survey distribution steps | **Zero** |
| Risk of emailing respondents twice | **Eliminated** |
| Survey follow-up process | **Fully automated** |
| Response check accuracy | **48-hour rolling window** |
| Event types supported | **2 (extensible)** |
| Communication cadence | **Timed, professional, non-intrusive** |
| Hours saved per event cycle | **2–4 hours** |

<br/>

---

## Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| **Orchestration** | n8n | Workflow automation and trigger handling |
| **Trigger Source** | Google Drive | CSV upload detection across 2 folders |
| **Response Checking** | Typeform API | 48-hour survey response exclusion logic |
| **Email Platform** | Klaviyo | List management, flow triggers, personalization |
| **Data Processing** | n8n native | CSV parsing, deduplication, normalization |
| **Metadata Enrichment** | Custom logic | Event title, type, date injection |
| **Timing Engine** | n8n scheduling | 24-hour check, 9 AM reminder logic |

<br/>

---

## System Flow — Detailed

### Step 1 — CSV Upload Trigger
A new attendee export CSV is dropped into the designated Google Drive folder. The system detects the upload and begins processing within minutes.

### Step 2 — Parse and Clean
The CSV is downloaded and parsed. All email addresses are lowercased and trimmed. Names are normalized. Duplicate email addresses within the file are removed — only the first occurrence is kept. Event metadata is extracted and stored for downstream enrichment.

### Step 3 — Typeform Exclusion Check
The system fetches all Typeform survey responses submitted in the last 48 hours. Each attendee's email is cross-referenced against this list. Anyone who already submitted a response is removed from the send list.

### Step 4 — Klaviyo Enrollment
Remaining eligible attendees are added to the appropriate Klaviyo email list with their full event metadata attached — event title, type, and date — enabling personalized survey messaging and dynamic survey link injection.

### Step 5 — Initial Survey Send
The Klaviyo flow is triggered immediately. The survey email goes out with personalized event context and the attendee's survey link.

### Step 6 — 24-Hour Response Check
24 hours after the initial send, the system queries Typeform again for that specific attendee. If a response is found — flow ends, no further contact.

### Step 7 — 9 AM Reminder (if needed)
If no response is found at the 24-hour mark, a reminder email is scheduled for 9 AM the following morning. After the reminder sends — the flow ends completely. No third email. No further follow-up.

<br/>

---

## Repository Structure

```
📁 automated-survey-followup-system/
├── 📄 README.md
├── 📁 workflow/
│   └── survey-automation.json        ← n8n workflow export
└── 📁 docs/
    └── flow-logic.md                 ← Detailed flow documentation
```

<br/>

---

## Built by Trilles AI

This system was designed and delivered by **[Awais Ali](https://www.linkedin.com/in/awais-ali-tillesai)**, CEO & Co-Founder of **[Trilles AI](https://www.trillesai.com)**.

If your business runs events, courses, or webinars and you're still manually managing survey distribution — this is exactly what we automate.

<div align="center">

[![Connect on LinkedIn](https://img.shields.io/badge/Connect%20on%20LinkedIn-%230A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/awais-ali-tillesai)
&nbsp;
[![Visit Trilles AI](https://img.shields.io/badge/Visit%20Trilles%20AI-%233A7CFF?style=for-the-badge&logoColor=white)](https://www.trillesai.com)
&nbsp;
[![Email](https://img.shields.io/badge/Email%20Me-%23EA4335?style=for-the-badge&logo=gmail&logoColor=white)](mailto:letsautomatewithawais@gmail.com)

</div>

<br/>

---

<div align="center">
<sub>Built with precision · Powered by Trilles AI · <code>www.trillesai.com</code></sub>
</div>
