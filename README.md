Surgical Supply AI Tools

A portfolio of tools and applications built for surgical supply chain and operating-room inventory work, combining hands-on OR domain expertise with applied AI and low-code development.

The work here reflects a real operating-room supply environment: implant and consignment tracking, HCPCS coding, UDI/GUDID identification, FDA recall monitoring, and expired-supply waste management. The goal across all of it is the same — take slow, manual, error-prone supply tasks and turn them into fast, repeatable, well-documented workflows.

Background

Built by a surgical supply coordinator with 5+ years in OR supply coordination and 10+ years in inventory control, transitioning toward clinical informatics and supply-chain analytics. These tools come out of daily OR work — the problems are real, and so are the constraints (Cerner workflows, consignment billing, UDI tracking, vendor variety).

The repository is organized into two kinds of work: Skills (functional, executable Claude tools) and Projects (full applications designed and built end to end).


Skills

Executable tools that perform a specific supply-chain task. Each is self-contained with its own instructions.

HCPCS C-Code Lookup (OR-specific)

Looks up HCPCS Level II C-codes for OR implants and devices billed under OPPS. Built for the implant and consignment items handled daily in a surgical setting.

HCPCS Lookup (hospital-wide)

General HCPCS Level II lookup across all code series (A, B, C, E, G, J, K, L, M, Q, R, S, T, V) for supplies, devices, equipment, and services across any hospital department.

Implant Name Formatter

Takes a reference or catalog number and returns a standardized implant name following a consistent OR naming convention, including vendor, catalog number, and HCPCS code.

Surgical Recall Tracker

Pulls and summarizes FDA medical device recalls for surgical supply purchasing staff, with vendor-aware filtering for the suppliers used in the OR.

Supply Substitute Finder

Finds equivalent or substitute products for general (non-implant) hospital supply items when something is backordered or discontinued.


Projects

Full applications designed and built end to end. These are documented as case studies, with architecture, design decisions, and outcomes.

Expired Supply Waste Tracker

A mobile-first Power Apps + SharePoint application for capturing, costing, and reporting expired general medical supplies in an OR. Staff scan an expired item on a phone, price it later from a desk, and the app produces a clean monthly cost figure that feeds a hospital waste-reporting template. Demonstrates requirements scoping, data modeling, barcode/UDI data analysis, and workflow design balancing system constraints against human process.

OR Supply Ordering App

A tool for tracking Bill Only / consignment special-order requests, with backorder flagging and status tracking. Originally prototyped as a React app, then migrated to a Microsoft Lists + Power Automate backend with automated backorder-digest and arrival-notification flows.


Skills Demonstrated Across the Repository


Surgical supply chain and OR inventory domain expertise
HCPCS Level II coding and UDI/GUDID device identification
Low-code application development (Power Apps, Power Automate, SharePoint)
Data modeling and workflow design
Barcode/GS1 data structure analysis
Requirements gathering and project scoping
FDA recall and regulatory monitoring workflows



All examples are generalized for public sharing. No proprietary hospital data, patient information, or organization-specific details are included.
