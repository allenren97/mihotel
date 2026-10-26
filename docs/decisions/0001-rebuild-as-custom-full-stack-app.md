# 0001. Rebuild the site as a custom full-stack application

- **Date:** 2026-10-26
- **Status:** Accepted

## Context

MiHotel's current site is built on Wix, and bookings come in through a Google Form. The form can't check availability or confirm a booking, so every request has to be handled by hand. See the [site audit](../research/current-site-audit.md).

The owner is a data engineer who wants to learn full-stack development and have a portfolio project to show for it.

## Options considered

1. **Keep Wix and improve the content.** This is cheap and fast, but it teaches nothing and adds no real booking system.
2. **Keep Wix and add a booking plugin.** This solves the booking problem, but there's little to learn and nothing to show in a portfolio.
3. **Build a custom application** (frontend, backend, database, booking system). This takes far more work, but it's the most to learn, it's fully owned and customizable, and it can go in a portfolio.

## Decision

Option 3: a full custom rebuild. An AI chatbot is a possible later addition.

## Consequences

- Hosting, security, backups and maintenance become the owner's job instead of Wix's.
- Wix stays live until the new site is ready to replace it, so the business isn't affected while it's being built.
- The project is large, so it will be split into sub-projects and built in stages.
- Learning is a goal, so the documentation (journal, decisions, learning notes) is part of the deliverable, not an afterthought.
