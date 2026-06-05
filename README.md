[README.md](https://github.com/user-attachments/files/28650770/README.md)
# surgical-supply-ai-tools# Surgical Supply AI Tools

AI-powered workflow tools built for hospital OR supply chain operations.
Developed by Brian Rozelle, Lead Surgical Supply Coordinator with expertise in
OR purchasing, implant tracking, OPPS billing, and medical device compliance.

---

## Tools

### Surgical Recall Tracker
Pulls daily FDA medical device recall alerts and formats a prioritized briefing
for OR purchasing staff. Filters by surgical relevance (implants, biologicals,
spinal/orthopedic hardware) and a vendor watch list. Cross-references lot numbers
via UDI for quarantine decisions.

**Vendors monitored:** Arthrex, Medline, Medtronic, Stryker, Acumed, Alphatec,
Spinal Elements, Zimmer Biomet, Precision Spine

**Data sources:** FDA Medical Device Recalls & Early Alerts, openFDA API, CBER

### HCPCS C Code Lookup
Four-step lookup workflow for identifying HCPCS C codes in an OPPS outpatient
facility billing context. Covers orthopedic, spinal, biological, breast implant,
mesh, and neurostimulator coding. Includes a surgical quick-reference table and
vendor-to-code mapping.

**Built for:** OR purchasing staff who need fast, accurate code identification
before billing consigned or implanted items.

---

## Background

These tools were built to solve real daily workflow problems in hospital OR supply
chain management — reducing time spent on manual recall monitoring and eliminating
coding errors on implantable device claims. Both tools use structured AI prompting
against live FDA and CMS data sources.

**Domain coverage:** Orthopedic and spinal implants, biologicals, breast implants,
mesh, neurostimulators, consignment billing, UDI tracking, OPPS outpatient facility billing.

---

## Repository Structure

```
surgical-supply-ai-tools/
├── README.md
├── surgical-recall-tracker/
│   └── SKILL.md
└── hcpcs-c-code-lookup/
    ├── SKILL.md
    └── references/
        └── surgical-c-codes.md
```
