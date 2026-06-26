# Expired Surgical Supply Waste Tracker

A mobile-first app for capturing, costing, and reporting expired general medical supplies in a hospital operating-room environment. Built solo using Microsoft Power Apps and SharePoint.

## Problem

A 15-room OR suite generates a steady stream of expired general supplies (non-implant consumables) that have to be logged, costed, and reported monthly for waste tracking. The existing process was manual and slow: items were recorded by hand, costs looked up separately, and the monthly total assembled by hand. There was no fast way to capture an expired item at the shelf, and pricing and reporting were disconnected from capture.

The goal was a tool that lets staff scan an expired item in seconds, fill in costs later from a desk, and produce a clean monthly cost figure that feeds the hospital's existing waste-reporting template.

## Solution

A two-screen Power Apps canvas app backed by a SharePoint List:

- **Capture screen** (used on a phone at the shelf): scan the item's barcode, enter the name and quantity expired, save. No cost entered here — capture is kept fast.
- **Pricing screen** (used later at a desk): shows every item still missing a price, with a cost box per row. Entering a unit cost calculates the extended cost (quantity × unit cost) and writes both back to the record. Priced items drop off the list automatically.
- **Backend**: a single SharePoint List storing all records, grouped by calendar month with cost and quantity totals.

The monthly totals feed a separate hospital reporting template maintained by the business director, so the app deliberately does **not** build its own reporting layer.

## Architecture

```
Phone (Power Apps mobile) ──scan──> Capture screen ──> SharePoint List
                                                            │
Desktop (Power Apps) ──price──> Pricing screen ────────────┘
                                                            │
                                          Monthly grouped view (cost + qty totals)
                                                            │
                                          Director's hospital reporting template
```

**Stack:** Microsoft Power Apps (canvas app) + SharePoint Online List. No external services or paid connectors.

### Data model (SharePoint List)

| Field | Type | Notes |
|---|---|---|
| Item Name | Single line of text | Primary field |
| Reference Number | Single line of text | Captured from barcode scan |
| Quantity | Number | Quantity expired |
| Unit Cost | Currency | Entered during pricing phase |
| Extended Cost | Currency | Calculated by the app (qty × unit cost) on save |
| Created | Date (built-in) | Auto-stamped; drives month grouping |
| Month | Calculated text | `TEXT([Created],"YYYY-MM")` for clean month-sorting in the grouped view |

## Key Design Decisions

**SharePoint List over a shared Excel file.** Multiple people needed concurrent read/write access. A shared Excel file risks file-lock conflicts; a SharePoint List handles simultaneous access cleanly and supports per-user permissions (full access for two users, read-only for report viewers).

**Capture and pricing split into separate workflows.** Scanning happens at the shelf in seconds; pricing requires looking each item up in a separate system and is done later at a desk. Forcing cost entry at capture time would have slowed the shelf work for no reason. The split lets each task run in its natural context.

**Pricing screen driven by "is the cost blank" rather than a status field.** An empty Unit Cost *is* the "needs pricing" signal, so the pricing gallery simply filters to records where Unit Cost is blank. As items are priced they disappear from the list — no extra status field to maintain.

**Calendar-month reporting over a rolling cycle.** The hospital pulls numbers on a shifting date each month. Building "rolling 30-day" or "third-Monday-to-third-Monday" logic into the report would have created overlap and gap errors as the cutoff date moved. Instead, the human process was adjusted — all of a month's items are captured before month-end close — and the report groups by clean calendar month. Complexity was moved to the easy-to-change part (when people scan) rather than baked into the fragile part (the report logic).

**App-optional viewing.** Report viewers read the monthly totals directly from the SharePoint List, not through the app. This means the monthly report is shareable even if app-sharing turns out to be restricted in the tenant — the core deliverable doesn't depend on every user having the app.

**Name-resolution deliberately scoped out.** An early idea was to auto-resolve scanned barcodes to product names. Investigation showed there's no reliable free lookup for general (non-implant) supply barcodes — GUDID covers devices only — and the waste data is mostly non-recurring items month to month, so a self-built item master would rarely auto-fill anyway. The feature was dropped as a poor effort-to-value tradeoff. Knowing what not to build kept the app simple.

## Challenges and Findings

**Barcode format reconnaissance.** Before building the capture logic, real expired packages were sampled across multiple vendors. Findings:
- Most items carry **GS1 barcodes** (1D and 2D DataMatrix) encoding a GTIN, lot, and expiration date.
- Scanner/reader quality matters significantly — a poor reader truncated a 2D code; a good reader returned the full structured data.
- At least one vendor used a **proprietary (non-GS1) alphanumeric code**, confirming the capture screen couldn't assume a single format.

The practical conclusion: for a waste log, the reference number only needs to be a consistent, recognizable identifier — not a normalized GTIN. The Power Apps barcode reader returns a clean value on a phone, so no custom parsing was required. A manual-entry fallback covers unreadable or proprietary codes.

**The scanner is mobile-only.** The Power Apps barcode reader does not function in the desktop browser preview; it only runs in the Power Apps mobile app. Capture testing was done on a phone; pricing (no camera needed) can be done on either.

**Gallery display behavior.** A blank-layout gallery renders no visible rows even when data is loading correctly — the rows are present but have no display controls. Adding display controls (or a layout) revealed the data was flowing the whole time.

## Outcome

A working, published app that:
- Captures expired items by barcode scan on a phone in seconds
- Separates fast shelf-capture from desk-side pricing
- Auto-calculates extended cost and produces a clean monthly total
- Feeds the hospital's existing waste-reporting template without duplicating it

The tool replaces a manual log with a two-phase scan-and-price workflow and gives the OR a reliable monthly expired-supply cost figure.

## Skills Demonstrated

- Requirements gathering and scoping (including deciding what *not* to build)
- Low-code application development (Power Apps canvas apps, Power Fx formulas)
- Data modeling and SharePoint List design
- Barcode/UDI data structure (GS1, GTIN) analysis
- Workflow design balancing system constraints against human process
- Multi-user access and permissions design
