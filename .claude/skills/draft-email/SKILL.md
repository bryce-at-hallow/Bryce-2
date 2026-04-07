---
name: draft-email
description: Draft, review, and send partner emails to Gmail. Use this skill whenever Bryce needs to write an outbound email to a parish partner — whether directly requested ("draft an email to..."), or as the final step in another skill (post-roadmap-email, schedule-midyear-checkin, email-triage, etc.). Trigger any time the end goal is a Gmail draft going to a parish contact. This is the canonical email drafting skill — all other skills that produce outbound emails should hand off here for the present → approve → push loop.
---

# Draft Email

## What This Skill Does

Takes context about the email's purpose and recipient, drafts the email in Bryce's voice, presents it for review, handles edits, and on approval pushes it to Gmail as an HTML draft.

This skill owns the full loop: **draft → present → edit → approve → Gmail.** Calling skills handle context-gathering and pass the relevant inputs in; this skill handles everything from the first draft onward.

---

## Inputs

When invoked directly by Bryce, gather any missing inputs before drafting:

| Input | Source |
|---|---|
| **To** (name + email) | Provided by calling skill or Bryce |
| **CC** (names + emails, if any) | Provided by calling skill or Bryce |
| **Purpose / what to say** | Provided by Bryce or calling skill |
| **Partner context** | From `partners/territories/` if relevant and not already loaded |

If **To** is missing, ask before proceeding. Everything else can be inferred or drafted.

---

## Step 1 — Draft the Email

Write the email in Bryce's voice. Guidelines:

- **Warm and conversational** — Bryce connects through relationship and mission. Write like a person, not a template.
- **Use "and" not "but"** — signals collaboration, not contradiction
- **Concise** — parish staff are busy. Respect their time.
- **First name only** in the greeting (`Hi [First],`)
- **No heavy formatting** in conversational emails — flowing prose over bullet lists unless the content genuinely calls for structure
- **Include a subject line** — draft one that is specific and human, not generic ("Quick question" / "Following up" are not acceptable; something like "Re: Your Hallow Partnership — Quick Update" is)
- **Weave in any scheduler link or specific CTA** naturally within the flow, not as a standalone "click here" line
- **Do not fabricate** program details, features, or pricing — only use what's in context/
- **DO NOT USE AN EM DASH.**

**Signature (always use exactly this):**
```
Blessings,
Bryce McWhirter
Associate Parish Success Lead
Hallow
bryce@hallow.app
```

---

## Step 2 — Present the Draft

Show the draft in this exact format before doing anything else:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📧 [Parish / Recipient Name]
To: [email]
CC: [emails, or "none"]
Subject: [subject line]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[email body]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Context used: [list files referenced]
```

Then **stop and wait.** Do not proceed to Gmail. Do not assume approval.

---

## Step 3 — Handle Edits and Approval

Bryce will respond with one of:

- **Edits or feedback** → apply them, show the revised draft in the same format, wait again
- **Explicit approval** — any of: "looks good", "draft it", "send it", "approved", "yes", thumbs up 👍, or similar affirmation → proceed to Step 4
- **Silence or ambiguity** → do not proceed. If genuinely unclear, ask: "Want me to go ahead and save this as a Gmail draft?"

Keep iterating until approval is explicit.

---

## Step 4 — Create the Gmail Draft

Use `mcp__claude_ai_Gmail__gmail_create_draft` with `contentType: text/html`.

**HTML formatting rules:**
- Wrap every paragraph in `<p style="margin: 0 0 12px 0;">` — no `<br>` between paragraphs
- Inline all styles (Gmail strips `<style>` blocks)
- Use `<strong>` for bold, `<ul>`/`<li>` for lists only when the content calls for it
- Signature block:
  ```html
  <p style="margin: 0;">Blessings,<br>Bryce McWhirter<br>Associate Parish Success Lead<br>Hallow<br>bryce@hallow.app</p>
  ```

Set **To**, **CC**, and **Subject** from the approved draft.

---

## Step 5 — Confirm

After the draft is saved:

> "Draft saved to Gmail. Subject: [subject line]. Let me know if you'd like to make any changes before sending."

If this skill was invoked by another skill, return control to that skill so it can complete any remaining steps (e.g., creating a HubSpot task).

---

## Rules

- Never create a Gmail draft without Bryce's explicit approval
- Never fabricate program details — only use what's in `context/`
- Always use HTML (never plain text) for Gmail drafts
- The signature is fixed — never modify or omit it
- Present one draft at a time — do not batch multiple emails
