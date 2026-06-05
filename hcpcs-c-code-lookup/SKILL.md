[SKILL (4).md](https://github.com/user-attachments/files/28650862/SKILL.4.md)
---
name: hcpcs-c-code-lookup
description: >
  Look up HCPCS C Codes for any medical supply, surgical implant, or device. Trigger this
  skill whenever the user asks anything about HCPCS codes, C-codes, or how to bill/code a
  specific supply or implant — including orthopedic hardware, spinal implants, breast
  implants, biologicals, bone grafts, mesh, anchors, screws, plates, rods, cages,
  stimulators, or any OR supply item. Also trigger when the user asks things like "what code
  do I use for...", "what's the C code for...", "how do I bill this implant", "is there a
  pass-through code for...", or "does [product] have its own code". Do NOT wait for the
  words "HCPCS" or "C-code" — if the question is about coding or billing a supply or
  implant, use this skill.
---

# HCPCS C Code Lookup

## Purpose
Identify the correct HCPCS C Code(s) for any surgical supply, implant, or device in an
OPPS (outpatient) facility billing context for OR cases.

Product universe: orthopedic implants (anchors, screws, plates, joint replacements),
spinal hardware (rods, cages, artificial discs, interspinous devices), biologicals/bone grafts,
breast implants, mesh, and consigned items from vendors including Arthrex, Medtronic, Stryker,
Synthes/DePuy, Acumed, Alphatec, Spinal Elements, Zimmer Biomet, Precision Spine.

---

## Lookup Workflow

### Step 1 — Identify the product category
Map the item to one of these buckets:
- Orthopedic anchor/screw/fixation hardware
- Orthopedic plate/rod/nail (long bone)
- Joint device (hip, knee, shoulder, etc.)
- Spinal interbody cage / spacer
- Spinal rod/screw system
- Interspinous process device
- Biological / bone graft / matrix
- Breast implant (prosthesis)
- Mesh (implantable)
- Neurostimulator / SCS component
- Pass-through drug or biological
- NOS / not otherwise specified

### Step 2 — Consult the Quick Reference Table
Check `references/surgical-c-codes.md` for codes matching the product category. This covers
the most common codes encountered in daily OR purchasing and billing.

### Step 3 — Live lookup (when product is ambiguous or unlisted)
If the Quick Reference Table doesn't resolve it, web-fetch the live HCPCS code list:
`https://www.hcpcsdata.com/Codes/C`
Search for matching terms. For a specific code, fetch:
`https://www.hcpcsdata.com/Codes/C/[CODE]`

Also check CMS OPPS Addendum for pass-through status and device-intensive indicators:
`https://www.cms.gov/medicare/payment/prospective-payment-systems/hospital-outpatient/addendum`

### Step 4 — Deliver the answer

Format every response like this:

```
HCPCS CODE: C####
Description: [Official CMS description]
Applies to: [Plain-English what this covers]
Status: [Pass-through (J7) / Device-intensive (J8) / Bundled / NOS]
Billing note: [Any pairing requirements, modifier flags, or watch-outs]
Fallback: [If nothing fits perfectly, note C1889 NOS and why]
```

If multiple codes could apply, list all candidates and explain the distinctions.

---

## Key Rules for HCPCS C Code Assignment

1. **Implant must remain in the body** — if it's removed before the patient leaves the OR,
   the C code is not reportable. The device is considered waste.

2. **Operative report drives the code** — if the surgeon didn't document using the implant,
   it doesn't get billed. Reference the implant log or intraoperative record.

3. **Device-intensive procedures (J8)** — when the CPT has a J8 indicator, report both the
   CPT (procedure) AND the C code (device). These are typically procedures where the device
   cost is >40% of the total payment.

4. **Pass-through codes (J7)** — paid separately from the procedure. These are time-limited
   (usually 2-3 years) and reset to bundled after the pass-through period expires. Always
   verify current pass-through status — CMS updates quarterly.

5. **C1889 is the catch-all** — "Implantable/insertable device, not otherwise classified."
   Use when no specific code exists. Pair with C1890 when NO implant was used in a
   device-intensive procedure (this tells the payer not to expect an implant add-on).

6. **One code per implant unit** — multi-component constructs (e.g., a spinal rod system)
   may need multiple C codes reported for each component type.

---

## Common Scenarios

| Scenario | Action |
|---|---|
| Arthrex anchor used in shoulder scope | C1713 (anchor/screw, soft-tissue-to-bone) |
| Breast implant used in mastectomy recon | C1789 (prosthesis, breast, implantable) |
| Allograft / bone matrix for void filling | C1734 or C1602 (see reference table) |
| Stryker/Zimmer joint replacement | C1776 (joint device, implantable) |
| Spinal cage / TLIF / ALIF interbody | C1822 or check reference table |
| Interspinous spacer (e.g., X-STOP) | C1821 |
| Consigned item used, no specific code | C1889 NOS |
| Device-intensive case, no implant used | C1890 |
| New pass-through device from rep | Web-fetch CMS OPPS addendum to verify |

---

## When to Escalate or Verify Externally

- **New or novel devices**: vendors often submit new C-code applications to CMS. Ask the rep
  for the CMS OPPS Addendum reference and verify at hcpcsdata.com before using it.
- **State or commercial payer**: C-codes are CMS/OPPS codes. Commercial payers may not
  recognize them or may have their own code requirements. Flag for the billing team.
- **Inpatient cases**: C-codes are OPPS (outpatient) codes. Inpatient cases use MS-DRG
  bundled payment — C-codes don't apply there.
- **Biologicals/HCT-P products**: may also need to verify with CBER.

---

## Reference Files
- `references/surgical-c-codes.md` — Master quick-reference table of codes by product category.
  Read this first for any lookup before going to the web.
