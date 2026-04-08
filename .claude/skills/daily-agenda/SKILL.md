---
name: daily-agenda
description: Build a Cal Newport-inspired daily agenda from tasks, HubSpot, and calendar. Use when Bryce asks to plan his day, wants a daily schedule, says "what should I work on today", or when generating the Daily Agenda section inside morning-coffee. Protects morning deep work, consolidates HubSpot tasks into a single block, and uses realistic time estimates instead of defaulting everything to 30 minutes.
---

## Goal
Build a smart, time-blocked daily agenda that protects deep work in the morning, batches shallow work in the afternoon, and gives realistic time estimates for every task.

## The Framework (Cal Newport)

**Deep Work** — sustained cognitive focus, no interruptions. Examples for Bryce:
- Anything relevant to what I'm working on in the current quarter. 
- 

**Shallow Work** — low-cognitive, logistical. Examples for Bryce:
- Quick email confirmations or short replies
- Submitting Ramp memos
- Booking logistics or short check-ins
- Logging notes

**Batched Blocks** — recurring admin that belongs in its own time box:
- **Email Block**: 45 min fixed. All email responses go here.
- **HubSpot Block**: sized by task count (see below). All HubSpot tasks go here.

## Step 1: Gather Inputs

If this skill is called from within morning-coffee, the data is already available — use it directly. If called standalone, pull fresh data in parallel:

- **ClickUp**: All open tasks assigned to me (`assignees: [me]`)
- **HubSpot**: Tasks due today or overdue (from the HubSpot daily digest or task list)
- **Calendar**: Today's events (Google Calendar, `primary`, today's date range)
- **Gmail partner inbox**: Unread partner emails needing a reply

## Step 2: Classify Each Task

For each task, assign:
- **Type**: `deep` or `shallow`
- **Time estimate**: realistic, not blanket 30 min

### Time Estimate Guide

| Task Type | Estimate |
|---|---|
| Ramp memo or quick form submission | 5 min |
| Short email confirmation (no context needed) | 10 min |
| Partner email reply (needs context or nuance) | 15–20 min |
| Short check-in call | 20–30 min |
| Strategy, planning, or project task | 60–90 min |
| Data pull or analysis | 30–60 min |
| Complex partner call with prep | 45–60 min |

### HubSpot Block Sizing

Count the number of HubSpot tasks due today:
- **1–4 tasks** → 15 min block labeled "HubSpot Tasks"
- **5–10 tasks** → 30 min block labeled "HubSpot Tasks"
- **11–20 tasks** → 45 min block labeled "HubSpot Tasks"

Do NOT list individual HubSpot tasks in the agenda — just the single consolidated block. You can note the task count in the Source column.

## Step 3: Build the Schedule

### Rules
1. **Lock in calendar events first** — these are immovable.
2. **Morning = Deep Work** — schedule the deep work block as early as possible after any early-morning calendar events (e.g., after Hallow Prayers). Aim for 2–4 hours uninterrupted.
3. **Email Block** — place at 9:00–9:30 AM by default, right after any early-morning calendar events. This anchors the morning before deep work begins.
4. **HubSpot Block** — place in the afternoon, after deep work.
5. **Shallow work** — fill remaining gaps in the afternoon with shallow tasks.
6. **Use 15-min increments** (5, 10, 15, 20, 30, 45, 60, 90).
7. **Don't overschedule** — if the day is full, flag which tasks should roll to tomorrow.

### Ideal Day Shape
```
Morning:   [Early calendar] → Deep Work block (2–4 hrs)
Midday:    Email Block (45 min) → Lunch
Afternoon: HubSpot Tasks block → Shallow work → [Late calendar]
```

## Step 4: Output the Agenda

Produce a table:

| Time Block | Activity | Type | Source |
|---|---|---|---|
| 9:00–9:15 AM | Email Triage | Shallow | Calendar |
| 9:15–11:15 AM | Deep Work: Begin Spanish Translation Project | Deep | ClickUp |
| ... | ... | ... | ... |

After the table, add a short **"Rolled to Tomorrow"** section if any tasks didn't fit.

## Notes
- If morning-coffee is calling this skill, replace the Daily Agenda section output with this skill's output.
- Deep work tasks should be specific — don't just say "deep work block", name what goes in it.
- The Email Block absorbs all partner email replies. Don't schedule individual email replies as separate line items unless they're outside the email block for a specific reason (e.g., a time-sensitive reply before the block).