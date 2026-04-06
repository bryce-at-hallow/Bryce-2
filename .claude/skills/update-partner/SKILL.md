---
name: update-partner
description: Update a partner's profile file with the latest meeting date, next meeting, PQR score, relationship history notes, and action items synced to HubSpot. Use when Bryce asks to update a partner record, log a call, or refresh a partner's file.
---

# Update Partner Skill

## When to Use
Invoke after a partner call, after reviewing a partner's status, or any time Bryce wants to refresh a partner record. Bryce will provide a partner name or parish name.

---

## Step-by-Step Instructions

### 1. Find the Partner File

Use `Glob` to find the partner file under `partners/territories/`:
- Pattern: `partners/territories/**/*.md`
- Match the parish name Bryce provided (fuzzy match on filename or parish name field)
- If multiple matches, show Bryce the options and ask which one

Load the file and keep all existing data in memory throughout this process.

### 2. Pull Recent Call from Attention

Search for the most recent call with this partner using `mcp__claude_ai_Attention__search_calls`.

**Important:** Search by the primary contact's **email address** from the partner file — do not search by parish name or contact name alone, as those searches are unreliable.

Pass the returned call ID to `mcp__claude_ai_Attention__ask_attention` with the following prompt:
> "Summarize this call in 2–3 sentences. What were the key topics discussed? What action items or next steps were mentioned for each party?"

If no call is found, note it and continue — Bryce may be updating the record manually.

### 3. Check Next Meeting on Google Calendar

Use `mcp__claude_ai_Google_Calendar__gcal_list_events` to search for any upcoming scheduled meeting with the parish contact. Look forward 60 days.

- If found: note the date and any meeting title
- If not found: leave Next Meeting blank and flag it for Bryce

### 4. Check HubSpot for Deal Context

Use `mcp__claude_ai_HubSpot__search_crm_objects` to find the partner's deal record. Search by parish name or contact email.

Pull:
- Deal stage
- Any open notes or tasks on the record
- Last activity date

### 5. Assess PQR Score

Based on what you've gathered (call tone, engagement level, action item follow-through, HubSpot activity), propose an updated PQR score using the rubric in `@context/program/pqr-scoring.md`.

Scores:
- 7–10: Strong — maintain and deepen
- 5–6: Developing — invest intentionally
- 1–4: At risk — triage

Present the proposed score and your reasoning to Bryce. Ask if he agrees or wants to adjust.

### 6. Present a Summary for Confirmation

Before writing anything, show Bryce a summary of proposed changes:

```
Partner: [Parish Name]
File: [file path]

Proposed updates:
- Last Meeting: [date from Attention call or Bryce's input]
- Next Meeting: [date from GCal or blank]
- PQR Score: [proposed score] → [label] (was: [current score])
- PQR Notes: [brief rationale]
- Relationship History: [new entry to add]
- Action Items to create in HubSpot: [list]
```

Ask: "Does this look right? Want me to apply these updates and create the HubSpot action items?"

### 7. Update the Partner File

If Bryce confirms, write the following updates to the partner file:

- **Meeting Cadence** — update Last Meeting and Next Meeting
- **PQR Score + PQR Notes** — update with new score and rationale
- **Relationship History** — prepend a new dated entry in this format:
  `- **MM/DD/YYYY** — [One sentence summary of what was discussed or decided.]`
- Do not touch any other sections

### 8. Create Action Items in HubSpot

Use `mcp__claude_ai_HubSpot__manage_crm_objects` to create tasks on the partner's deal record for each action item identified.

For each action item:
- Assign to Bryce (Bryce's action items) or leave unassigned (partner's items — for reference)
- Set a due date if one was mentioned; otherwise default to 30 days out
- Title format: `[Parish Name] — [Action Item]`

### 9. Confirm Completion

After saving, confirm:
> "Done. [Parish Name]'s profile is updated and [N] action items created in HubSpot. Next meeting: [date or 'not yet scheduled']."

---

## Rules
- Never invent data — only write what comes from Attention, GCal, HubSpot, or Bryce's direct input
- Always get confirmation before writing to the file or creating HubSpot tasks
- Use "and" instead of "but" in any partner-facing language
- If Bryce provides context verbally (e.g., "the call went well, they're excited about the challenge"), treat that as input for PQR reasoning and relationship history
