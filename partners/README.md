# Partner Profiles

Individual profile files for each active parish partner. Each file tracks contact info, partnership details, communication preferences, parish context, relationship history, and outstanding action items.

---

## Directory Structure

Files are organized by territory → state → `[parish-name]_[city].md`

```
partners/
  _template.md
  bryce_deals.json        ← HubSpot sync artifact (do not edit manually)
  territories/
    midwest/
      indiana/
      kansas/
      michigan/
      minnesota/
      missouri/
      north-dakota/
      ohio/
      south-dakota/
      wisconsin/
    northeast/
      delaware/
      maryland/
      massachusetts/
      new-jersey/
      new-york/
    south/
      alabama/
      georgia/
      mississippi/
      oklahoma/
      texas/
    west/
      arizona/
      california/
      colorado/
      new-mexico/
      washington/
      wyoming/
```

`bryce_deals.json` is the raw HubSpot export. It seeds new partner files but is not the working source of truth — the individual `.md` files are.

---

## Notes

- Always check a partner's file before drafting any communication
- Update the Relationship History section after every meaningful interaction
- Keep Outstanding Action Items current — check them at the start of each partner touchpoint
