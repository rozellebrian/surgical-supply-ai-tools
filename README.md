[README.md](https://github.com/user-attachments/files/28650770/README.md)
# Surgical Supply AI Tools

A set of Claude-native skills and a companion app built to solve real, recurring problems in OR supply chain operations: implant traceability, recall response, billing accuracy, and inventory tracking. Built and used in a live 15-room OR environment managing consignment and specialty implant inventory.

This repo is a working portfolio piece for the intersection of supply chain operations and clinical/data informatics — every tool here was built to solve a problem that came up on the floor, not as a theoretical exercise. I'm a Lead Surgical Supply Coordinator transitioning into Healthcare Data Analyst, Clinical Informatics Analyst, and Supply Chain AI Specialist roles.

## Tools in This Repo

| Tool | What it does |
|---|---|
| HCPCS C-Code Lookup (OR-specific) | Resolves OR implant/device catalog items to their billing C-code |
| HCPCS Lookup (hospital-wide) | Resolves any HCPCS Level II code across any hospital department |
| Implant Name Formatter | Standardizes implant naming from a catalog/reference number |
| Surgical Recall Tracker | Pulls and triages FDA device recalls against a vendor watchlist |
| Supply Substitute Finder | Finds equivalent general supply items fast when something's backordered or recalled |
| OR Supply Ordering App | Shared ordering and tracking tool for OR supply requests |

---

### HCPCS C-Code Lookup (OR-specific)
`hcpcs-c-code-lookup/`

Takes an implant or device reference and resolves it to the correct C-code for OPPS billing — the codes used for OR implants and devices specifically (C1713, C1776, C1889/NOS, etc.).

**Why it matters:** Billing accuracy on implants directly affects reimbursement. A miscoded implant either gets denied or under-bills the case.

**Outcome:** Eliminates the need to manually scan the code book for the proper code, significantly reducing the time it takes to find a code.

---

### HCPCS Lookup (hospital-wide)
`hcpcs-lookup/`

A broader version of the above — covers every HCPCS Level II series (A, B, C, E, G, J, K, L, M, Q, R, S, T, V) for any supply, equipment, or service across any hospital department, not just the OR. Built for departments without a dedicated coding tool.

**Why it matters:** Most hospital departments outside the OR are coding supplies manually with no quick-reference system. This generalizes the OR-specific tool into something usable hospital-wide.

**Outcome:** Significantly reduces the time needed to find the correct code for any hospital supply item and removes the need to consult the coding guide.

---

### Implant Name Formatter
`implant-name-formatter/`

Takes a catalog or reference number and returns a standardized name following a consistent naming convention:

`[Item Type] [Modifiers] [Size/Spec] | [Vendor] | [Catalog #] | [HCPCS Code]`

**Why it matters:** Inconsistent implant naming across staff and systems creates errors in tracking, billing, and inventory counts.

**Outcome:** Eliminates mistakes in formatting new supply items according to entity naming conventions.

---

### Surgical Recall Tracker
`surgical-recall-tracker/`

Pulls FDA CDRH recall data on a recurring basis and triages results by urgency tier against a vendor watchlist (Arthrex, Stryker, Zimmer Biomet, Medtronic, Alphatec, Spinal Elements, Acumed, Precision Spine, Medline, Intuitive Surgical, and others).

**Why it matters:** Manual recall monitoring means checking the FDA site by hand and hoping nothing slips through. This automates the first pass so review time goes toward decisions, not searching.

**Outcome:** Provides early notification of recalls, enabling a quicker response in locating substitutes and minimizing disruption to scheduled cases.

---

### Supply Substitute Finder
`supply-substitute-finder/`

Finds the closest available substitute for a general hospital supply item (consumables — gloves, drapes, sutures, syringes, tubing, wound care, prep kits — not implants or hardware). Searches primary distributors first (Medline, McKesson, Henry Schein), then expands to secondary suppliers if no close match is found.

**Why it matters:** When an item goes on back order or gets pulled in a recall, speed matters more than almost anything else in this list. A case can't wait for a slow substitution search.

**Outcome:** A significant reduction in the time it takes to identify a substitute when there is a backorder or recall.

---

### OR Supply Ordering App
`or-supply-orders.jsx`

A shared ordering and tracking tool for surgical supply teams. Staff submit multi-line supply requests; the supply coordinator works the list, marks items as ordered and received, and gets early visibility into items aging toward back-order status. Built as a working React prototype, with a defined migration path to Microsoft 365 / Power Automate for production deployment.

**Why it matters:** In a multi-room OR suite, supply requests scattered across verbal asks, sticky notes, and email create three recurring failures: no shared visibility into order status, silent back-orders nobody catches until a case needs the item, and overordering that leads to expired stock loss.

**Outcome:** Streamlines the process for specialty orders from coordinators, allowing them to track the status of their order once placed, and streamlines notification when an order is backordered or has arrived. Reduces stress and time for everyone involved.

---

## Skills Demonstrated

- **Domain-driven tool design** — every tool here started from an actual OR supply chain bottleneck, not a tutorial
- **Data resolution & lookup logic** — AccessGUDID, FDA CDRH, catalog-to-code mapping
- **Workflow automation** — recurring, triaged, vendor-aware monitoring instead of manual checks
- **Frontend prototyping** — React/JS for operational tooling
- **Healthcare systems context** — built for direct use in Cerner Discern Reporting Portal and Workday workflows

## About This Repo

Built by a Lead Surgical Supply Coordinator (15-room OR suite, AHC Shady Grove Medical Center) transitioning into healthcare data, clinical informatics, and supply chain AI roles — applying hands-on OR operations experience (UDI tracking, consignment management, implant billing, recall response) directly to the design of these tools. 5+ years in surgical supply coordination, 10+ years in inventory control. MBA (Leadership focus).

**Connect:** [linkedin.com/in/brian-rozelle-a337381b](https://www.linkedin.com/in/brian-rozelle-a337381b/)
