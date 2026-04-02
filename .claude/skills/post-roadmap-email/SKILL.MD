---
name: post-roadmap-email
description: Drafts a structured follow-up email after a parish roadmap meeting by pulling the most recent call from Attention MCP and generating a summary, action items for both parties, and a ready-to-send template with placeholders for Bryce's personal touches.
---

# Post-Roadmap Email Skill

## When to Use
Invoke this skill after a roadmap meeting with a parish partner when Bryce provides a contact name (and optionally a parish name). It pulls the most recent roadmap call from Attention and drafts a structured follow-up email.

---

## Step-by-Step Instructions

### 1. Identify the Contact
Bryce will provide a contact name (and optionally a parish). Use the partner profile in `partners/` as a reference for spelling, title, and relationship context if the file exists.

### 2. Pull the Most Recent Roadmap Call from Attention
Use `mcp__claude_ai_Attention__search_calls` to find the most recent call matching:
- The contact name Bryce provided
- Call type or title that includes "roadmap" (or the most recent call if none are explicitly labeled)

Then pass the returned call ID to `mcp__claude_ai_Attention__ask_attention` to retrieve a detailed summary and transcript analysis. **Do not loop through `get_call_details` repeatedly — use `ask_attention` with the call ID.**

Ask Attention for:
- A 2–3 sentence summary of what was discussed
- Key commitments or next steps mentioned by either party

### 3. Draft the Email

Structure the email exactly as follows. Use the tone from `@context/me.md` — warm, service-oriented, mission-aligned, respectful of the contact's time.

---

**Subject:** Our Roadmap Conversation — [Parish Name]

Hi [First Name],

[OPENING PLACEHOLDER — Bryce will write a personal opening line here.]

Here's a quick recap of our conversation:

**Summary**
[2–3 sentence summary of what was discussed, drawn from the Attention transcript. Keep it concise and conversational — not a transcript dump.]

**Your Next Steps**
1. [Action item for the parish contact]
2. [Action item for the parish contact]
3. [Action item for the parish contact]

**My Next Steps**
1. [Action item for Bryce]
2. [Action item for Bryce]
3. [Action item for Bryce]

[CLOSING PLACEHOLDER — Bryce will write a closing line here.]

Blessings,
Bryce McWhirter
Associate Parish Success Lead, Hallow
bryce@hallow.app

---

### 4. Formatting Rules
- Use "and" instead of "but" in all partner-facing copy (see `@context/me.md`)
- If Bryce's scheduler is relevant (e.g., scheduling a next meeting is an action item), use: meetings.hubspot.com/bryce-mcwhirter/roadmapping
- Keep action items short and specific — one clear task per bullet, no vague language
- Leave the `[OPENING PLACEHOLDER]` and `[CLOSING PLACEHOLDER]` lines clearly marked so Bryce knows exactly what to fill in
- Do not fabricate details not present in the transcript

### 5. After Drafting
Ask Bryce:
> "Want me to update [Parish Name]'s partner profile with these action items, log today's roadmap call in the interaction log, and create a gmail draft?"

If yes, update the partner profile in `partners/` by:
- Adding the call to the **Interaction Log** (format: `MM/DD/YYYY — Roadmap call. [One-sentence summary.]`)
- Replacing or adding action items under **Outstanding Action Items**
- Create a gmail draft with the email you just created

