---
name: supply-substitute-finder
description: Find substitute or equivalent products for general hospital supply items (non-implant). Trigger this skill whenever the user asks for an alternative to a supply item, a substitute product, an equivalent item, a replacement for something that's backordered or discontinued, or says things like "what can I use instead of", "find me a sub for", "we're out of X what else can we use", "backordered, need an alt", or "find a substitute for [item]". Do NOT use this skill for surgical implants, orthopedic hardware, spinal hardware, biologics, or any item that would require a HCPCS C-code. Do NOT check GUDID. Use this skill for general OR and hospital supplies: gloves, drapes, sutures, staples, catheters, syringes, tubing, wound care, gowns, prep kits, basins, irrigation supplies, and similar consumables.
---

# Supply Substitute Finder

## Purpose
Find the closest matching substitute for a general hospital supply item. This is for consumables and general OR/hospital supplies only — not implants, not orthopedic/spinal hardware, not biologics.

---

## Step 1: Clarify Before Searching

Do NOT search immediately. First confirm you have enough information to find a close match.

Ask targeted questions if any of the following are unknown:
- **Item name** — exact or common name (e.g., "blue towel drape", "10cc syringe luer lock")
- **Size or spec** — dimensions, gauge, French size, volume, etc. if applicable
- **Material or composition** — latex-free? sterile/non-sterile? specific material type?
- **Intended use** — what procedure or application is this for?
- **Brand/catalog # of the item being replaced** — if known, this is the most useful input

Stop and ask if the item description is too vague to find a close match. Do not guess. One or two targeted questions is better than a bad result.

---

## Step 2: Search Priority Order

Search in this exact order. Move to the next tier only if the previous tier yields no close match.

### Tier 1 — Primary Distributors
Search these first:
1. **Medline** — medline.com
2. **McKesson** — mckesson.com (or mms.mckesson.com for product catalog)
3. **Henry Schein** — henryschein.com

### Tier 2 — Secondary Distributors / Manufacturers
If Tier 1 yields nothing usable:
4. **Cardinal Health** — cardinalhealth.com
5. **Owens & Minor** — owens-minor.com
6. **Bound Tree / Bound Tree Medical** — boundtree.com
7. **Manufacturer direct** (e.g., BD, Halyard, Molnlycke, 3M, Covidien/Medtronic for general supply)

---

## Step 3: What to Return

For each substitute found, return:

| Field | Detail |
|---|---|
| **Item Name** | Full product name as listed by the supplier |
| **Reference / Catalog #** | Exact catalog or item number |
| **Supplier** | Where it was found (Medline, McKesson, etc.) |
| **Source URL** | Direct link to the product page |
| **Key Specs** | Size, material, sterility, quantity per box — whatever is relevant |
| **Match Notes** | Brief note on how closely it matches (exact equivalent, functional equivalent, minor difference) |

---

## Step 4: Output Format

Lead with a short statement of what was found and where.

List results in order of closest match first. If a Tier 1 supplier had a match, list that before any Tier 2 results.

If a result is a functional equivalent but not identical, flag the difference clearly so clinical staff can evaluate.

If nothing close is found, say so plainly. Do not invent or approximate catalog numbers.

---

## Rules
- **Never fabricate catalog numbers or product details.** If you can't confirm a ref number from a source, do not include it.
- **Source URL is required for every result.** If you can't link to it, don't list it.
- **Do not include pricing.** Contract pricing is handled separately.
- **Do not check GUDID** — this skill is for general supply, not implants.
- **Do not include HCPCS codes** — not needed for this workflow.
- **If the item is an implant** (bone anchor, screw, plate, cage, mesh, graft, breast implant, etc.) — stop and tell the user this skill is for general supply only.
