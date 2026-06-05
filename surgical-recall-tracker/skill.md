---
name: surgical-recall-tracker
description: >
  Pull and summarize FDA medical device recalls every morning for surgical supply purchasing staff.
  Trigger this skill whenever the user asks about recalls, new recall alerts, FDA safety notices,
  product safety updates, or says anything like "check recalls", "any new recalls", "recall report",
  "recall summary", or "what got recalled". Also trigger if the user mentions a specific vendor
  (Arthrex, Medline, Medtronic, Stryker, Acumed, Alphatec, Spinal Elements, Zimmer Biomet,
  Precision Spine) and asks about safety or product issues. Do NOT wait for the user to say
  "skill" or "FDA" — if the intent is recall awareness, use this skill.
---

# Surgical Recall Tracker

## Purpose
Pulls the latest FDA medical device recalls and formats a quick daily briefing for surgical supply
staff at a hospital OR. Focused on surgical implants, orthopedic/spinal hardware, biologicals,
and related OR supplies.

## Data Sources (in priority order)
1. **FDA Medical Device Recalls & Early Alerts** (primary):
   `https://www.fda.gov/medical-devices/medical-device-safety/medical-device-recalls-and-early-alerts`
   — Updated daily, lists Class I recalls and Early Alerts

2. **FDA Full Recall Database** (for searching by vendor/product):
   `https://www.accessdata.fda.gov/scripts/cdrh/cfdocs/cfres/res.cfm`

3. **openFDA API** (for programmatic lookup by UDI, product code, firm name):
   `https://api.fda.gov/device/recall.json?search=...&limit=10`
   Example: `https://api.fda.gov/device/recall.json?search=firm_name:"Arthrex"&limit=5`

## Daily Morning Briefing Workflow

### Step 1 — Fetch
Web-fetch the FDA Medical Device Recalls & Early Alerts page. Extract the recall table (Date, Issue, Product Area, Status).

### Step 2 — Filter for surgical relevance
Flag anything in these categories as HIGH PRIORITY for OR staff:
- Surgical implants (orthopedic, spinal, joint replacement, breast implants)
- Biologicals / bone grafts
- Surgical staplers, energy devices, power tools
- Trays, kits, convenience kits used in OR
- Any vendor on the user's watch list (see below)

Flag as MEDIUM PRIORITY:
- Catheters, drainage systems used perioperatively
- Sterilization-related items
- General surgical supplies (sutures, drapes, etc.)

Skip / note briefly:
- Non-surgical devices (insulin pumps, ventilators for ICU, consumer products)

### Step 3 — Classify urgency
- **Class I recall** = most urgent; product may cause serious injury or death. Quarantine immediately.
- **Early Alert** = FDA investigating; watch and hold pending confirmation.
- **Class II** = malfunction possible but injury unlikely; monitor and follow vendor instructions.
- **Class III** = unlikely to cause harm; low urgency.

### Step 4 — Format the briefing

Output this format every morning:

```
📋 SURGICAL RECALL BRIEF — [DATE]
Pulled from FDA Medical Device Recalls & Early Alerts

🔴 IMMEDIATE ACTION REQUIRED
[List Class I recalls affecting surgical items]
- Product: [name]
- Vendor: [firm]
- Issue: [one sentence]
- Action: Quarantine affected lots. Contact rep. Check inventory by UDI.
- Lot/UDI info: [if available]

🟡 WATCH — EARLY ALERTS / CLASS II
[List items under investigation or lower-class recalls]
- Product / Vendor / Issue / Recommended action

🟢 NOT SURGICAL — FYI ONLY
[One-line list of non-OR recalls for awareness]

No new recalls affecting surgical supply today. [if nothing found]
```

### Step 5 — Vendor-specific check (optional)
If the user asks about a specific vendor, run an openFDA API query:
`https://api.fda.gov/device/recall.json?search=firm_name:"[VENDOR]"&sort=recall_initiation_date:desc&limit=10`

Parse `recall_initiation_date`, `product_description`, `reason_for_recall`, `classification`, and `action` fields.

## Vendor Watch List
Prioritize mentions of these companies in any recall output:
- Arthrex
- Medline
- Medtronic
- Stryker
- Acumed
- Alphatec
- Spinal Elements
- Zimmer Biomet
- Precision Spine

## UDI Lookup
If the user provides a UDI or lot number, look it up via GUDID:
`https://accessgudid.nlm.nih.gov/devices/[UDI-DI]`

## Notes
- FDA classification date ≠ recall initiation date. A recall showing up today may have been initiated weeks ago by the vendor.
- Early Alerts are pre-classification — treat them as Class I until confirmed otherwise.
- For biologicals/HCT-P products, check CBER recalls separately: `https://www.fda.gov/vaccines-blood-biologics/biologics-recalls-withdrawals-safety-alerts`
- Always pull lot numbers from the implant tracking system before quarantining to confirm affected units are on hand.
