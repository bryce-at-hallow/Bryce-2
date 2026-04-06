# Workflow: HubSpot Deal Sync

Re-sync deal data from HubSpot and update or create partner files in `partners/`.

---

## When to run this

- Periodically (monthly or quarterly) to refresh contact info, PQR scores, and deal details
- When a new deal is added to your HubSpot pipeline
- Before a partner review when you want to ensure data is current

---

## Step 1 — Pull deals from HubSpot

Use the HubSpot MCP (`mcp__claude_ai_HubSpot__get_crm_objects`) or export manually.

Save the result as `partners/bryce_deals.json`. Replace the existing file. Update the `pulled_at` field to today's date.

---

## Step 2 — Identify new deals

Compare the deal IDs in the updated JSON against existing partner files. Any deal ID not yet represented in a `.md` file needs a new file.

---

## Step 3 — Determine file path for a new deal

1. **Territory** - See [`context/hallow.md` Under Territory for Territory Table]

2. **State subfolder** — full lowercase state name (e.g., `wisconsin/`, `new-york/`)

3. **File name** — `[parish-name]_[city].md` in kebab-case
   - Parish name: clean the deal name (strip state, tier, year info)
   - City: infer from email domain, zip code in deal name, or contact phone area code

---

## Step 4 — Create the partner file

Use [`_template.md`](../partners/_template.md) as the base. Populate from JSON:

| Template field | JSON field |
|----------------|------------|
| Parish Name | Clean from `name` |
| Territory | Inferred from state |
| State | From `name` (two-letter state code) |
| Website | Infer from contact email domains |
| Primary Contact | Pastor/senior clergy from `contacts` |
| Secondary Contacts | Remaining entries in `contacts` |
| Partnership Tier | From `name`: Growth/GP/GR → Growth; Expansion/EP/EX → Expansion |
| Partnership Start Date | `closedate` |
| Staff Complimentary Code | `complimentary_promo_code` |
| Quality Rating | `pqr_score` |
| Parish Size | `parish_size` (if not null) |
| Community Dashboard | `tool_community_hyperlink` (if not null) |

Add `<!-- HubSpot community ID: [community_id] -->` at the bottom of the file.

---

## Step 5 — Update existing files (for re-syncs)

For deals that already have a `.md` file, compare JSON data against the file and update:
- PQR score (if changed)
- Contact info (if changed)
- Promo codes (if changed)

Do NOT overwrite Relationship History, Action Items, or Interaction Log — those are manually maintained.

---

## Step 6 — Update TASKS.md

If a new partner index file is needed (once 5+ partners per territory), note it in TASKS.md.
