---
name: parish-data-report
description: >
  Generates a branded Hallow parish engagement report (PDF) from two
  CSVs — Community Engagement Metrics and Parish Community Challenges. Use this
  skill whenever Bryce uploads parish data files, asks for a parish report, wants
  to analyze Church tab engagement, or mentions creating a report for a parish POC.
  Trigger even if only one CSV is present — prompt for the missing one.
---

# Goal
Produce a visually polished, story-driven PDF report for a parish point of contact (POC)
that communicates how their community is engaging with the Church tab on the Hallow app.

The report should leave the POC feeling **peace and clarity** — a reassuring update
from a trusted partner that shows their parishioners are praying more and praying often,
and gives them a clear picture of where their partnership with Hallow stands.

## Inputs Required at Runtime
- **Parish name** — Bryce will provide this when invoking the skill
- **Current total members** — the live member count on the parish community page (e.g., 619). Bryce will provide this. If not given, ask before proceeding.
- `Community_Engagement_Metrics.csv`
- `Parish_Community_Challenges.csv`

If the member count or either CSV is missing, ask for it before proceeding.

## Workflow — Two Phases with a Human Pause

This skill runs in two phases. Do not skip or combine them.

### Phase 1: Generate HTML & Ask for Insights
1. Read the CSVs, compute all stats, generate charts, and write the full HTML report
   to `output/[parish-name]-report.html`
2. Open the HTML in the browser: `open output/[parish-name]-report.html`
3. Analyze the data and prepare 3–5 suggested insights based on what stands out
   (growth trends, challenge standouts, engagement spikes, completion rates, etc.)
4. Ask Bryce:
   > "The HTML report is open in your browser — take a look through it.
   > Here are some insights I'd suggest for the Insights & What's Next section:
   > [list your suggestions]
   > What would you like to include? Feel free to use mine, modify them, or add your own."
5. Wait for Bryce's response before proceeding.

### Phase 2: Finalize HTML
1. Take Bryce's confirmed insights and write them into the Insights & What's Next section
   as polished, warm narrative bullets (replace the placeholder block entirely)
2. Regenerate the HTML with the completed insights section
3. Open the final HTML in the browser: `open output/[parish-name]-report.html`
4. Tell Bryce the report is ready at `output/[parish-name]-report.html` and remind him:
   **To save as PDF: Cmd+P → Save as PDF → Letter, no margins, Background graphics ON**

---

## Design Authority

This report uses the **Hallow Brand Kit** as its sole design authority.
Read `/Users/brycemcwhirter/.claude/skills/hallow-brand-kit/SKILL.md` before
writing any HTML/CSS. Apply Hallow colors, typography, and visual identity throughout.

**Core design constraints:**
- Font: Inter throughout
- Primary accent: Purple `#6D0EC1`
- Body text: `#1A1A1A` (near-black, not `#000000`)
- White-dominant, clean, premium tech product feel — not a spreadsheet dump
- `DESIGN_VARIANCE: 6` — asymmetric layouts, left-aligned headers, varied stat cards
- `MOTION_INTENSITY: 1` — PDF/print context; no animation
- Always include `@media print` rules: `print-color-adjust: exact`, `page-break-inside: avoid` on cards/charts/table rows, `page-break-before: always` on **each section title** (Section 1 is page 1, Section 2 is page 2, Section 3 is page 3), fixed footer at bottom of page, `thead { display: table-header-group }` to repeat headers across pages

### Page Setup
- A4/Letter, white background, 48px margins (left/right), 40px (top/bottom)
- Max content width: 720px, centered
- Use `@page` CSS for print sizing

### Header (Page 1 only)
- Hallow purple wordmark logo, left-aligned (`Hallow-Wordmark-Purple.png`) — height: 28px
- Right side: Parish name in Inter Bold 22px, `#1A1A1A`; "Church Tab Engagement Report"
  in Inter Regular 13px, `#888888`
- Below: 1.5px solid `#6D0EC1` rule, full width, 16px top margin

### Charts
Use `matplotlib` to generate inline SVGs embedded in the HTML:
- Line color: `#6D0EC1`, 2px stroke; fill `rgba(109,14,193,0.08)` under the line
- Axis labels: Inter Regular, grey; no chart border box — just axis lines

### Footer (every page)
- 1px purple rule
- Left: "Hallow — Confidential" in Inter Light 10px, `#AAAAAA`
- Right: page number, same style

---

## Report Layout

### Section 1: Community Engagement Overview
Derived from `Community_Engagement_Metrics.csv`. Show all months present in the data.

**Stat cards (6), displayed in a row:**
- **Current Community Members** — the live total member count Bryce provides (most prominent card; this is the headline number)
- Total Prayers Started
- Total Prayers Completed + overall completion rate %
- Peak Avg Daily Users (highest single month — name the month)
- Total Intentions Posted

**Two side-by-side trend charts (the hero visual):**
1. **Prayers Completed by Month** — bar or line chart, all months
2. **New Members Joined by Month** — bar or line chart, all months

Both charts share the same x-axis (months) and height. Label months on the x-axis.

**Monthly activity table:** All months, sorted chronologically.
Columns: Month · Prayers Started · Prayers Completed · Completion % · Avg Daily Users · New Members

---

### Section 2: Challenge Engagement
Derived from `Parish_Community_Challenges.csv`. All challenges present in the data.

Present challenges in this priority order:

#### 2A. Community-Specific Challenges *(most prominent)*
These are challenges the parish POC made available for their community — give these
the most visual weight. One card per challenge.

#### 2B. Global Challenges
These come with the Hallow partnership — millions of Catholics praying together.
Frame them as the broader mission the parish is participating in. One card per challenge.

#### 2C. Public Challenges *(de-emphasized)*
Show as a compact summary table — noticeable but not dominant.
Columns: Challenge Name · Start Date · Joined · Participated · Completed

**Card metrics for 2A and 2B:**
- Challenge name + start date
- Joined
- Participated
- Completed
- Completion Rate (Completed / Joined, as %)

Do not show the 25/50/75% milestone columns.

---

### Section 3: Insights & What's Next

**In Phase 1 (HTML draft):** Render this as a clearly marked placeholder so Bryce
can see where the section will live. Style it with a light purple background and
dashed border so it stands out as unfinished.

**In Phase 2 (final HTML):** Replace the placeholder entirely with Bryce's confirmed
insights, written as 3–5 warm, second-person narrative bullets. Tone: encouraging,
mission-centered, forward-looking. Not bullet-point data — human stories and direction.

---

## Notes
- Sort all months chronologically throughout the report
- Completion Rate = Completed / Joined (not Completed / Participated)
- Parish name comes from Bryce at runtime — do not pull it from the CSV
