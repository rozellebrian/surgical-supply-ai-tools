Look up any HCPCS Level II code for any healthcare supply, device, equipment, or service across any hospital department. Trigger this skill whenever the user asks about billing or coding a supply item, asks what code applies to a product, asks "what's the HCPCS code for...", "how do I bill this", "what code covers X", or mentions a specific product and needs to know how it gets coded or billed. Covers all HCPCS Level II series: A-codes (supplies, wound care, DME), B-codes (enteral/ parenteral), C-codes (OPPS implants/devices), E-codes (DME equipment), G-codes (procedures/professional services), J-codes (drugs), K-codes (DME temp), L-codes (orthotics/prosthetics), M-codes (office services), Q-codes (misc/temporary), R-codes (radiology), S-codes (commercial payer), T-codes (Medicaid), and V-codes (vision/hearing). Do NOT wait for the word "HCPCS" — if the question is about coding or billing any supply, device, or equipment in a hospital setting, use this skill. Do NOT use this skill for CPT (procedure) codes.HCPCS Level II Lookup
Purpose
Identify the correct HCPCS Level II code for any healthcare supply, device, equipment,
or billable item across any hospital department or care setting.
This skill does NOT cover CPT codes (procedure codes billed by physicians or facilities
for services rendered). If a CPT code is what's needed, say so clearly.

Lookup Workflow
Step 1 — Identify the code series
Map the item to the correct HCPCS Level II series:
SeriesCoversCommon SettingsA-codesMedical/surgical supplies, wound care, DME supplies, transportED, wound care, nursing units, ORB-codesEnteral and parenteral nutrition suppliesICU, nutrition supportC-codesOPPS pass-through devices and implantsHospital outpatient (OPPS only)E-codesDurable medical equipment (DME)DME suppliers, rehab, home healthG-codesProcedures and professional services (Medicare-specific)Outpatient clinicsJ-codesInjectable/infusible drugs not self-administeredPharmacy, infusion, oncologyK-codesDME items without permanent codes (temporary)DME suppliersL-codesOrthotics and prostheticsOrtho, PT/OT, amputee careM-codesOffice/medical services (limited use)Physician officesQ-codesMiscellaneous/temporary items (CMS-assigned)VariesR-codesDiagnostic radiology (limited use)RadiologyS-codesCommercial payer codes (not Medicare)Non-Medicare claimsT-codesMedicaid state agency codesMedicaid billingV-codesVision and hearing itemsOptometry, audiology
Step 2 — Consult the Quick Reference Table
Check references/hcpcs-by-series.md for common codes in that series before going
to the web. Covers the most frequently encountered codes across hospital departments.
Step 3 — Live lookup (when item is ambiguous or unlisted)
If the reference table doesn't resolve it, fetch the live code list by series:
https://www.hcpcsdata.com/Codes/[LETTER]
Example: https://www.hcpcsdata.com/Codes/A for A-codes
For a specific known code:
https://www.hcpcsdata.com/Codes/[LETTER]/[CODE]
Example: https://www.hcpcsdata.com/Codes/A/A6216
For drug codes (J-codes), also check:
https://www.cms.gov/medicare/payment/fee-schedules/drug
For OPPS-specific status (C-codes, pass-through, device-intensive):
https://www.cms.gov/medicare/payment/prospective-payment-systems/hospital-outpatient/addendum
Step 4 — Deliver the answer
Format every response like this:
HCPCS CODE: [LETTER####]
Series: [A / C / E / J / L / Q / etc.]
Description: [Official CMS description]
Applies to: [Plain-English what this covers]
Payer scope: [Medicare / Medicaid / Commercial / OPPS-only]
Billing note: [Quantity units, modifier requirements, bundling rules, watch-outs]
Fallback: [If nothing fits, note the NOS/misc code for that series and why]
If multiple codes could apply, list all candidates and explain the distinctions.
If a CPT code is what's actually needed instead, say so and explain why.

Key Rules by Series
A-codes (Supplies)

Many A-codes are DME supplier codes — hospital inpatient supply is typically bundled
into MS-DRG and not separately billable via HCPCS on the inpatient claim.
Outpatient/HOPD: A-codes for surgical supplies may be separately reportable depending
on OPPS packaging rules. Check APC status.
Wound care supplies (A6xxx range) are commonly used in home health and wound centers.

C-codes (OPPS Devices/Implants)

OPPS outpatient only — do not use on inpatient MS-DRG claims.
Pass-through status (J7 indicator) is time-limited; verify quarterly via CMS addendum.
Device-intensive procedures (J8 indicator): report both CPT and C-code.
C1889 = NOS catch-all implant; C1890 = no implant used in a device-intensive case.
See the existing hcpcs-c-code-lookup skill for full C-code detail if needed.

E-codes (DME Equipment)

Billed primarily by DME suppliers (DMEPOS), not hospitals directly.
Hospital outpatient may bill E-codes for items dispensed at discharge in some cases.
Require Certificate of Medical Necessity (CMN) for many items.

J-codes (Drugs)

Billed per unit (vial, mg, ml) — always confirm the billing unit in the code descriptor.
Drugs not on the J-code list may use J3490 (unclassified drug) or J3590 (unclassified biologic).
NDC number must accompany the J-code on outpatient drug claims.

L-codes (Orthotics/Prosthetics)

Fabrication codes differ from off-the-shelf codes — check "custom fabricated" vs
"prefabricated" in the descriptor.
Require a physician order and documentation of medical necessity.
L-codes billed by O&P practitioners; hospital ortho departments may also bill.

Q-codes (Temporary/Miscellaneous)

Assigned by CMS for items that don't fit existing series or are under review.
May be converted to permanent codes (typically J or A series) or discontinued.
Always verify current Q-code status — they change more frequently than other series.


Common NOS / Unlisted Fallbacks by Series
SeriesNOS/Unlisted CodeAA9999 — Miscellaneous DME supplyCC1889 — Implantable device, NOSEE1399 — DME, miscellaneousJJ3490 — Unclassified drug; J3590 — Unclassified biologicLL4205 — Ankle orthosis, NOS (or nearest category NOS)QQ9999 — Miscellaneous (use rarely; document thoroughly)

Department Quick-Reference
DepartmentTypical Series UsedOR / Surgical ServicesC-codes (implants), A-codes (supplies)EDA-codes (supplies, splints), Q-codesICU / Nursing UnitsA-codes, B-codes (nutrition), J-codesPharmacy / InfusionJ-codes, Q-codes (drugs)Wound CareA-codes (A6xxx dressings and supplies)Ortho / PT / OTL-codes (orthotics), E-codes (DME)RadiologyR-codes, A-codes (contrast supplies)OncologyJ-codes, Q-codes (chemo drugs)Home HealthA-codes, E-codes, B-codesAudiology / OptometryV-codes

When to Escalate or Flag

Inpatient claim: Most HCPCS supply codes are bundled under MS-DRG. Only certain
pass-through drugs (J/Q codes) are separately billable on inpatient claims. Flag for
the billing team before coding.
Commercial payer: HCPCS Level II recognition varies. Commercial payers may use
their own internal codes or require different modifiers. Billing team should verify.
New or novel product: Vendor may have a pending CMS code application. Ask the rep
for documentation and verify at hcpcsdata.com before using NOS codes.
Compound or kit: Multi-component kits may need individual codes per component
rather than a single kit code. Check the descriptor carefully.
S-codes and T-codes: Not recognized by Medicare. Only use on Medicaid or commercial
payer claims where the payer specifically requires them.
