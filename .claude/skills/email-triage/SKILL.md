---
name: email-triage
description: Triage Bryce's Gmail inbox for partner emails and draft responses. Use this skill when Bryce says "check my inbox", "triage my emails", "draft replies to my partners", "go through my email", or anything that involves reading and responding to partner emails. Always trigger when Bryce wants to process, review, or respond to emails from parish partners.
---

# Email Triage Skill

## What This Does
Goes through emails Bryce has labeled for follow-up one at a time, reads the full thread, drafts a reply grounded in the context directory, and gets Bryce's approval before sending to Gmail as a draft.

---

## Step 1 — Fetch Labeled Emails

Use `mcp__claude_ai_Gmail__gmail_search_messages` with this query:

```
label:1.-projects-respond-to-parish-emails -from:*@hallow.app
```

Bryce manually labels emails he wants responses to — every email under this label should be processed. Fetch up to 20 at a time. Process them in order — oldest first.

---

## Step 2 — Read the Full Thread

For each email, use `mcp__claude_ai_Gmail__gmail_read_thread` with the thread ID. Read every message — not just the latest one. You need the full context to understand what's been discussed, what's been promised, and what's still unresolved.

Also load:
- `context/program/partnership-features.md` — tier features and program details
- Any partner file from `partners/territories/` if you can identify the sender's parish by name or domain
- Any other context file that seems directly relevant based on what they're asking

---

## Step 3 — Identify the Question(s)

Read the most recent message carefully. Determine:
1. What is the sender actually asking or needing?
2. Is there anything unresolved from earlier in the thread?
3. Is this answerable from the context directory, or does it require human judgment?

---

## Step 4 — Draft the Response

Write a reply grounded in what you found. Tone guidelines (from `context/about/me.md`):
- Warm, mission-aligned, service-oriented
- Use "and" instead of "but"
- Concise — respect their time
- First name only in the greeting

**If the question is fully answerable:** Draft a complete response.

**If partially answerable:** Draft what you can and add a clearly marked placeholder:
> `[BRYCE TO FILL IN: ...]`

**If not answerable at all:** Do not draft a response. Flag it:
> "⚠️ Flagged: [sender name] — [one sentence on what they asked]. Needs your input before I can draft a reply."

Then move to the next email.

**If the next step involves scheduling a meeting**, include Bryce's scheduler link:
`meetings.hubspot.com/bryce-mcwhirter/roadmapping`

---

## Step 5 — Present the Draft to Bryce

Show the draft in this format before doing anything else:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📧 [Parish / Sender Name]
Subject: Re: [original subject]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[draft email body]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Context used: [list the files you referenced]
```

Then wait. Bryce will respond directly in the chat with one of:
- Edits or changes to make → apply them, show the revised draft, and wait again
- Explicit approval ("Draft it", "Looks good", "Send it") → proceed to Step 6
- Nothing / silence → do not proceed. Never assume approval.

---

## Step 6 — Handle Edits and Approval

- If Bryce gives edits: apply them, show the revised draft, and wait for approval again.
- Only create the Gmail draft after Bryce explicitly approves. Hand off to the **`draft-email` skill** to create the Gmail draft — pass in the approved body, To, CC, and subject. Do not save to `output/`.
- After creating the draft: confirm with "Draft saved. Moving to the next email." then continue.

---

## Step 7 — End of Triage

When all emails have been processed, give Bryce a summary:

```
Triage complete.
✅ Drafts created: N
⚠️ Flagged for your input: N
```

---

## Rules
- Never write a Gmail draft without Bryce's explicit approval
- Never fabricate features, pricing, or program details — only use what's in context/
- Process one email at a time — do not batch drafts
- Read the whole thread before drafting, not just the latest message
- Every labeled email gets processed — Bryce's label is the only filter needed
