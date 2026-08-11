# OR Supply Orders

A shared ordering and tracking tool for surgical supply teams. Staff submit multi-line supply requests; the supply coordinator works the list, marks items as ordered and received, leaves notes back to staff, and gets early warning on items that are aging into back-order territory.

Built as a working prototype for a hospital OR supply workflow, with a defined migration path to Microsoft 365 / SharePoint for production use.

## The problem it solves

In a multi-room OR suite, supply requests come in verbally, on sticky notes, and through scattered emails. That creates three recurring failures:

- **No shared view** — staff don't know if something they asked for was actually ordered or is still sitting on someone's desk.
- **Silent back-orders** — an item gets ordered, never arrives, and nobody notices until it's needed in a case.
- **Expired loss** — without usage visibility, stock gets over-ordered and times out before it's used.

This app puts every request in one shared, status-tracked list so the coordinator and the floor are looking at the same information.

## What it does

- **Multi-line ordering** — submit several items in one order (reference #, quantity, comments per line) under the requester's name.
- **Shared live tracking** — every order lands in one shared list visible to staff and the coordinator; status updates sync across users.
- **Coordinator controls** — a role gate lets only the coordinator mark items **Ordered** and **Received**, add notes back to staff, or remove lines. Staff see a read-only view.
- **Back-order flagging** — any item marked ordered but not yet received shows a running age counter, then trips a red "Back-order?" flag at 10 days so nothing slips silently.
- **Status summary** — top-line counts of Awaiting / Ordered / Received for a fast read on the queue.
- **Email draft** — placing an order generates a ready-to-copy notification email for the purchasing inbox.

## How it works

- **React** single-component app, styled with Tailwind utility classes.
- **Shared persistent storage** keeps the order list in sync across everyone using the app — no backend required for the prototype.
- **Role gating** (Coordinator / Brian) controls edit permissions. In the prototype this is honor-system; in the production version it becomes real user permissions.

## Running it

This is a single React component (`or-supply-orders.jsx`) intended to run inside an artifact/React environment. To run it standalone, drop it into a Vite or Create React App project as the root component and provide the `window.storage` API (or swap in your own persistence layer).

## Production migration path (Microsoft 365)

The prototype is deliberately structured to port cleanly to M365:

- **SharePoint list** — one list with columns mirroring the app fields: Ordered by, Reference #, Quantity, Staff note, Coordinator note, Ordered (Y/N), Received (Y/N). Created date is automatic.
- **Power Automate flow** — "when an item is created → send email" replaces the manual email-draft step with a real automated notification.
- **List permissions** — staff get add/view, coordinator gets full edit, making the role split enforced rather than honor-system.
- **Power App (optional)** — wraps the multi-line ordering form for a cleaner submit experience than the default SharePoint form.

## Context

Built by a surgical supply coordinator managing implant, consignment, and specialty inventory across a multi-room OR suite, as part of a move toward healthcare data and clinical informatics work. The design reflects real OR supply constraints: UDI/reference-number tracking, back-order risk, and expired-stock reduction.
