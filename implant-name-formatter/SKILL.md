name	implant-name-formatter
description	Look up a surgical implant or device by reference/catalog number and return a standardized formatted name following the OR naming convention: [Item Type] [Modifiers] [Size/Spec] | [Vendor] | [Catalog #] | [HCPCS Code]. Trigger this skill whenever the user types a reference number, catalog number, or part number and asks what the item is, what it's called, how to name it, or how to format it. Also trigger for queries like "look up ref #", "what is part number X", "format this item", "what do I call this", or any bare numeric/alphanumeric string that looks like a catalog or reference number in the context of surgical supply. Do NOT use this skill for HCPCS code-only lookups (use hcpcs-c-code-lookup) or for recall queries (use surgical-recall-tracker).
Implant Name Formatter
Purpose
Take a reference number (catalog number, part number, or UDI-DI) entered by the user and return a standardized formatted item listing for use in OR inventory records, consignment bills, implant logs, and Workday/Cerner entries.

Naming Convention
All items are formatted in this structure:

[ITEM TYPE], [MODIFIER(S)], [SIZE/SPEC] | [VENDOR] | [CAT #] | [HCPCS]
Item Type (required)
The primary device category. Use the shortest accurate term:

Screw, Rod, Plate, Nail, Pin, Wire, Staple
Cage (interbody), Spacer, Disc (artificial)
Anchor (suture anchor), Tack, Button
Implant (breast, joint, NOS)
Prosthesis (joint replacement component — femoral stem, tibial tray, acetabular cup, humeral head, glenoid, etc.)
Graft (bone, allograft, autograft)
Matrix (bone void filler, DBM)
Membrane, Mesh
Kit (multi-component tray or procedure kit)
Drill Bit, Tap, Awl, Guide Wire (instruments — note if included in implant kit)
System (when the item is a complete multi-component construct sold as one unit)
Modifiers (include all that apply, in this order)
Material: Titanium, Stainless Steel, PEEK, Cobalt-Chrome, Tantalum, Bioabsorbable, Allograft, Autograft, Synthetic
Bone type / region (if applicable): Cortical, Cancellous, Corticocancellous
Mechanical property: Locking, Non-Locking, Cannulated, Solid, Fenestrated, Polyaxial, Monoaxial, Expandable, Compressible
Surface / treatment: Porous, HA-Coated, Hydroxyapatite, Anodized, DBM (demineralized bone matrix)
Self-tapping / Self-drilling (screws only)
Approach / orientation (spine): TLIF, ALIF, PLIF, LLIF, XLIF, OLIF, Posterior, Anterior, Lateral
Fixation type (if relevant): Cemented, Cementless, Press-fit, Threaded
Size / Spec (include all available dimensions)
Screws: Diameter x Length (e.g., 4.5mm x 30mm)
Rods: Diameter x Length (e.g., 5.5mm x 100mm)
Plates: Length x Hole count (e.g., 8-hole, 120mm)
Cages/Spacers: Height x Width x Depth or footprint + lordosis (e.g., 10mm x 18mm x 22mm, 6° lordosis)
Nails: Diameter x Length (e.g., 10mm x 360mm)
Anchors: Diameter x Length (e.g., 5.5mm x 15.2mm)
Prosthesis components: Size designation as labeled (e.g., Size 3, 54mm, Medium)
Grafts/Matrix: Volume or weight (e.g., 5cc, 10g)
If size is unknown or not applicable: omit
Lookup Workflow
Step 1 — Determine reference number type
Assess what was entered:

Full UDI (starts with 00, 10, or contains parenthetical AI codes like (01), (10), (17)): parse out the DI portion for lookup
Catalog / part number (alphanumeric, 5–10 chars, no barcode prefix): search vendor databases and GUDID by catalog ref
Ambiguous: treat as catalog number and attempt both routes
Step 2 — Try AccessGUDID first
Web-search: site:accessgudid.nlm.nih.gov "[reference number]" Or fetch: https://accessgudid.nlm.nih.gov/devices/[number]

Extract from GUDID record:

brandName → use for vendor/product line identification
versionModelNumber → catalog/part number confirmation
deviceDescription → parse for item type, modifiers, size
companyName → vendor
implantFlag → confirm it's an implant
gmdnPTName → backup descriptor if deviceDescription is sparse
Step 3 — Try vendor web search if GUDID misses
Search: "[reference number]" [vendor name if known] implant OR screw OR plate OR cage OR anchor

Target sources (in priority order):

Vendor product pages (arthrex.com, stryker.com, zimmer.com, medtronic.com, acumed.com, alphatec.com, spinalelements.com, precisionspine.com, depuysynthes.com)
Vendor IFU / surgical technique PDFs
GPO/distributor catalog pages (Vizient, Premier, Provista)
Extract: product name, description, material, dimensions, catalog number confirmation.

Step 4 — Identify HCPCS Code
Once item type and description are known, apply the HCPCS C Code lookup logic (reference the hcpcs-c-code-lookup skill or the surgical-c-codes reference table).

Key mappings for common item types:

Item Type	Likely HCPCS
Suture anchor	C1713
Bone screw (orthopedic)	C1713
Spinal screw / pedicle screw	C1713
Spinal cage / interbody device	C1822
Spinal rod	C1713
Plate (orthopedic)	C1713
Intramedullary nail	C1713
Joint prosthesis (hip/knee/shoulder)	C1776
Breast implant	C1789
Bone graft / allograft	C1734
DBM / bone void filler	C1602
Interspinous device	C1821
Mesh (implantable)	C1781
NOS (no specific code)	C1889
Always verify pass-through status at: https://www.hcpcsdata.com/Codes/C/[CODE]

Step 5 — Format and deliver
Output every result in this exact format:

FORMATTED NAME:
[ITEM TYPE], [MODIFIER(S)], [SIZE] | [VENDOR] | [CAT #] | [HCPCS]

────────────────────────────────────────
ITEM TYPE:    [type]
MODIFIERS:    [modifier list or "None identified"]
SIZE/SPEC:    [dimensions or "See label"]
VENDOR:       [manufacturer name]
CATALOG #:    [confirmed catalog/part number]
HCPCS CODE:   [code + short description]
SOURCE:       [AccessGUDID / Vendor site / Web search / Not found]
────────────────────────────────────────
NOTES:        [Any caveats — multiple sizes, discontinued, consignment flag, etc.]
Handling Incomplete Lookups
If the reference number returns no results from GUDID or vendor search:

Note "Not found in GUDID or vendor search"
Ask the user: vendor name, product type, or any label text visible on the package
Once user provides context, apply naming convention manually based on their description
Flag as "MANUAL ENTRY — verify with rep before entering in Cerner/Workday"
If the number returns multiple matches (e.g., same catalog number across sizes):

List all variants in the output table
Flag: "CONFIRM SIZE — multiple variants exist for this catalog number"
Vendor Prefix Patterns (quick reference)
Use these to identify vendor from catalog number format when vendor is unknown:

Prefix / Pattern	Likely Vendor
AR-XXXXX or SCXXXX	Arthrex
4100-XXXX or 7XXX-XXXX	Stryker
XXXXXXXX (8-digit numeric)	Medtronic / Sofamor Danek
XXXXXXX (7-digit numeric)	Zimmer Biomet
2XXXXXXX	DePuy Synthes
AC-XXXXX	Acumed
ATXXXXX	Alphatec
SE-XXXXX	Spinal Elements
PS-XXXXX	Precision Spine
These are heuristics only — always confirm via lookup.

Notes
Size information is critical for OR records. If dimensions aren't returned by the lookup, flag "SIZE NOT CONFIRMED — pull from package label or implant sticker."
For consigned items, the formatted name feeds directly into the consignment bill. Accuracy matters.
If the item is a kit (multiple components), list the kit name as formatted, then note: "Multi-component — individual components may each require separate C code reporting."
Implant log entries in Cerner should use the FORMATTED NAME field output above.
