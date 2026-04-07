---
name: schedule-midyear-checkin
description: Drafts a mid-year check-in email to a Hallow parish partner and creates a HubSpot follow-up task. Use this skill whenever Bryce mentions scheduling a mid-year check-in, wants to reach out to a partner about their partnership check-in, says "send a mid-year email", or provides a partner name alongside any intent to check in on the partnership. Also trigger when Bryce provides relationship context or notes alongside a partner name and wants an outreach email drafted.
---

# Schedule Mid-Year Check-In

## What This Skill Does
1. Finds the partner's file and loads their contact and relationship context
2. Incorporates any context Bryce provides on the fly (call notes, recent interactions, things to mention)
3. Drafts an HTML email to the parish in Gmail — warm, conversational, no heavy formatting
4. Creates a HubSpot follow-up task on the partner's deal, due one week from today

---

## Step 1 — Find the Partner File

Use `Glob` to find the partner file under `partners/territories/**/*.md`. Match on the parish name Bryce provided. If multiple matches, ask Bryce which one.

Load the file. Pull:
- Primary contact (name + email) → **To** field
- All secondary contacts (names + emails) → **CC** field
- PQR score and notes
- Relationship history (last few entries)
- Partnership tier and start date
- Any notable context

---

## Step 2 — Find the HubSpot Deal

Use `mcp__claude_ai_HubSpot__search_crm_objects` to find the deal. Search by parish name.

Look for the active (closed-won) deal. Pull the deal ID — you'll need it to create the follow-up task.

If multiple deals exist, use the most recent closed-won one that matches the CA/parish location.

---

## Step 3 — Hand Off to Draft Email Skill

Pass the following to the **`draft-email` skill** to handle drafting, approval, and Gmail creation:

**To:** Primary contact name + email
**CC:** All secondary contacts
**Purpose and content guidance:**
- Warm and conversational — write like Bryce is reaching out to a ministry partner he genuinely cares about, not sending a templated check-in notice
- Flowing prose, not bullet lists
- Acknowledge the relationship and where they are in the partnership (use the relationship history and PQR context naturally, don't recite it)
- Weave in any context Bryce provided on the fly — make it feel personal and specific to this parish
- Include the mid-year scheduler link naturally in the flow of the email, not as a standalone "click here" line
- The goal is to get this parish to schedule time on the calendar

**Mid-year scheduler link:** https://meetings.hubspot.com/bryce-mcwhirter/mid-year-check-in?uuid=4181fcbc-edf8-4e1c-8193-99b8ace32f9f

**What the email should cover:**
- Coming up on (or reaching) a milestone in the partnership — make it feel celebratory, not administrative
- The call will walk through community engagement metrics, set a quarterly rhythm, and look ahead at what the next year could include (prayer challenges, parish events, etc.)
- Invite them to grab a time via the scheduler link

The `draft-email` skill will present the draft for Bryce's approval before pushing to Gmail. Once the draft is saved, return here to complete Step 4.

---

## Step 4 — Create the HubSpot Follow-Up Task

Use `mcp__claude_ai_HubSpot__manage_crm_objects` to create a task on the deal record.

- **Title:** `[Parish Name] — Follow up on mid-year check-in email`
- **Due date:** 7 days from today
- **Associated deal:** the deal ID from Step 2
- **Assign to Bryce**

You do not need my confirmation to make this task. Go ahead and make the task

---

## Step 5 — Confirm

After both actions complete, confirm:
> "Done. Draft saved to Gmail and a follow-up task created in HubSpot for [date]. Let me know if you'd like to adjust anything in the email before sending."
