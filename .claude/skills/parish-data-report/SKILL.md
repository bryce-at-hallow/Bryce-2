---
name: parish-data-report
description: >
  Generates a branded Hallow parish engagement report (PDF + email) from two
  CSVs — Community Engagement Metrics and Parish Community Challenges. Use this
  skill whenever Bryce uploads parish data files, asks for a parish report, wants
  to send data to a parish POC, or mentions analyzing Church tab engagement.
  Trigger even if only one CSV is present — prompt for the missing one.
---

# Goal
Produce a visually polished, story-driven report for a parish point of contact (POC)
that communicates how their community is engaging with the Church tab on the
Hallow app. Then send that report alongside the raw data files to the parish POC
via email.

## Dependencies
Requires two CSVs at runtime:
- `Community_Engagement_Metrics.csv` — monthly prayer activity per community
- `Parish_Community_Challenges.csv` — challenge participation data per community

If either file is missing, ask the user to upload it before proceeding.

## Output
1. A branded PDF report generated via **WeasyPrint** (HTML/CSS → PDF).
   Do not use reportlab. Do not use the PDF skill. Write HTML + CSS directly,
   then convert with: `python3 -m weasyprint report.html report.pdf`
2. An email to the parish POC (see Email section below).

---

## Design Authority

This report uses **two layered design authorities**. Read both before writing any HTML/CSS:

1. **Hallow Brand Kit** — color and typography are non-negotiable.
   Read `/Users/brycemcwhirter/.claude/skills/hallow-brand-kit/SKILL.md` and
   `references/visual-identity.md`. This is a **Tier 1 asset**.
2. **design-taste-frontend** — drives layout quality, anti-slop rules, and visual craft.
   Read the skill at `projects/LBBtoDFW/.claude/skills/design-taste-frontend/SKILL.md`.
   Apply its design philosophy, Layout Diversification (Rule 3), Materiality (Rule 4),
   AI Tells (Section 7), and Creative Arsenal (Section 8) principles to every design decision.

**Where they conflict, Hallow Brand Kit wins:**
- Font: Inter (Hallow standard) — overrides design-taste-frontend's Inter ban
- Accent color: Purple `#6D0EC1` (Hallow brand) — overrides the Lila Ban
- Black: `#1A1A1A` (near-black, not `#000000`) for body text

**Design dials for this print context (overrides design-taste-frontend defaults):**
- `DESIGN_VARIANCE: 6` — offset asymmetry; headers left-aligned, stat cards varied
- `MOTION_INTENSITY: 1` — PDF/print; no animation; CSS hover/active states only
- `VISUAL_DENSITY: 5` — data-rich but airy; tables breathe, cards earn their borders

**Design philosophy:** "Sleek design with a dash of purple." Premium tech product report —
clean, white-dominant, modern. Not a spreadsheet dump. Apply design-taste-frontend's
taste to every component: avoid generic 3-card rows, avoid centered hero layouts,
avoid AI-tell patterns.

### Page Setup
- A4/Letter, white background, 48px margins (left/right), 40px (top/bottom)
- Max content width: 720px, centered
- Use `@page` CSS for print sizing; WeasyPrint respects it

### Header (Page 1 only)
- Hallow purple wordmark logo, left-aligned (`Hallow-Wordmark-Purple.png`)
  — logo height: 28px, preserve aspect ratio
- Right side: Parish name in Inter Bold 22px, `#1A1A1A`; "Church Tab Engagement Report"
  in Inter Regular 13px, `#888888`
- Below: 1.5px solid purple `#6D0EC1` rule, full width, 16px top margin

### Trend Chart (Section 1) — REQUIRED
Use `matplotlib` to generate an inline SVG:
- Line color: `#6D0EC1`, 2px stroke; fill `rgba(109,14,193,0.08)` under the line
- Axis labels: Inter Regular, grey; no chart border box — just axis lines
- Chart height: ~220px

### Footer (every page)
- 1px purple rule
- Left: "Hallow — Confidential" in Inter Light 10px, `#AAAAAA`
- Right: page number, same style

---

## Report Layout

### Section 1: Community Engagement Overview
Derived from `Community_Engagement_Metrics.csv`.

**Stat cards (5):**
- Total Prayers Started
- Total Prayers Completed + completion rate %
- Peak Avg Daily Users (highest single month, name the month)
- Total Intentions Posted
- New Members Joined

**Trend chart:** Average Daily Users Praying by month — this is the hero visual.

**Monthly activity table:** All months, columns: Month · Prayers Started ·
Prayers Completed · Completion % · Avg Daily Users

**Trend narrative (2–3 sentences):** Call out the inflection point month,
correlate to challenge launches. Warm, second-person voice.

---

### Section 2: Parish Community Challenges
Derived from `Parish_Community_Challenges.csv`.

#### 2A. Global Challenges
`Challenge Type = "Global Challenge"`. Emphasize scale — millions of Catholics
praying together. One card per challenge.

#### 2B. Community-Specific Challenges
`Challenge Type = "Community-Specific Challenge"`. Highlight completion rates —
these are built for this parish. One card per challenge.

#### 2C. Public Challenges
`Challenge Type = "Public Challenge"`. Summary table only, top 5 by Participated.
Frame as self-directed organic participation.

**Card metrics for 2A and 2B:**
Joined · Participated · 25% · 50% · 75% · Completed · Completion Rate

---

### Section 3: Interesting Insights
3–5 narrative bullets in warm second-person voice. Tell human stories, not stats.
Draw from completion standouts, engagement spikes, trajectory, and challenge
type comparisons.

---

## Email to Parish POC

Draft for Bryce to review before sending.

**Tone:** Warm, brief, ministry-minded. Per Hallow voice: welcoming, humble,
mission-centered. Not a sales email.

**Structure:**
- Personal greeting (use POC name if known)
- 1–2 sentences: "Here's a look at how [Parish] has been engaging..."
- One standout insight highlight
- Note that report + raw data are attached
- Warm sign-off ("God bless" or "In Christ")

**Attachments:** PDF report · Community_Engagement_Metrics.csv · Parish_Community_Challenges.csv

Ask for POC name and email if not already provided.
