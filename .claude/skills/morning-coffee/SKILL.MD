---
name: morning-coffee
description: produces a daily summary and logs it to Notion. Use this skill whenever Bryce says "morning coffee", "morning report", "daily summary", "what's on my plate today", "start my day", or asks for a morning briefing. Also trigger when Bryce opens the day and wants an overview of tasks, meetings, and emails.
---

## Goal
Produce a morning report providing an overview of the day and a list of action items, then create a page in the Notion Daily Reports database capturing the full report.

## Step 1: Gather Data (run in parallel where possible)

Pull all data sources simultaneously:
- **ClickUp**: Tasks assigned to me (due today, overdue, or scheduled for today)
- **Calendar**: Today's meetings from Google Calendar
- **Gmail**: New partner emails (prioritize low PQR score partners); tool notification emails from ClickUp, HubSpot, Ramp, Rippling labels; newsletters from `label:2.-areas-newsletters`

## Step 2: Build the Report

Structure the report in this order:

### 1. Header
`# Morning Coffee Overview (MM/DD/YYYY)`

### 2. Today's Action Items (from ClickUp)
- Pull tasks assigned to me
- Flag any that are overdue or outstanding

### 3. Today's Meetings
- List all calendar events with time, title, and attendees

### 4. Partner Inbox
- Summarize new emails from parish partners
- Lead with lowest PQR score partners first

### 5. Tool Updates
Search each label for messages from the last 24 hours and summarize what came in. Use a `##` heading per tool. If a label has no new messages, write "No updates." — don't skip the section.

- **ClickUp** — `label:2.-areas-tool-updates-clickup newer_than:1d`
- **HubSpot** — `label:2.-areas-tool-updates-hubspot newer_than:1d`
- **Ramp** — `label:2.-areas-tool-updates-ramp newer_than:1d`
- **Rippling** — `label:2.-areas-tool-updates-rippling newer_than:1d`

For each email found, summarize: what changed, what needs action (if anything). Keep each summary to 1–2 sentences per notification.

### 6. Newsletters
Summary of recent messages with label `label:2.-areas-newsletters`

### 7. Daily Agenda
Use the `daily-agenda` skill logic to build this section. All task data has already been gathered in Step 1 — pass it directly rather than re-fetching. The output is a time-blocked table with deep work protected in the morning, a consolidated HubSpot Tasks block, a 45-min Email block, and realistic time estimates.

## Step 3: Create Notion Page

After generating the full report, create a new page in the **☕ Morning Coffee** Notion database using `notion-create-pages`:

- **Parent**: `data_source_id: 33c9f080-0098-8085-9374-000bc6e06c69`
- **Properties**: `{"Name": "MM/DD/YYYY"}` — today's date as the page title
- **Content**: The full report from Step 2 in Notion-flavored Markdown

Use standard Markdown: headings (`##`, `###`), bullet lists, and tables. This renders correctly in Notion.

After creating the page, share the Notion URL with Bryce so he can open it directly.
