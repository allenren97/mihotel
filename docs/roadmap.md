# Roadmap

Agreed 2026-10-26. A 12-week plan at about 2 hours a day.

**Weekly rhythm:** five build days, one review day (journal, learning notes, catch-up), one day off. Side questions are welcome anytime.

Only the current week is planned day by day. Later weeks have a goal and a "done when" check, and get detailed when we reach them.

## Overview

| Week | Theme | What you'll learn | Done when… |
|---|---|---|---|
| 1 | Foundations | How the web works (DNS, HTTP, client and server), Git and GitHub, HTML and CSS, JavaScript for Python developers, Docker | Repo is on GitHub, and Postgres runs in Docker locally |
| 2 | First API | FastAPI, REST, JSON, request and response, pytest | An API returns MiHotel's suites and prices, with tests |
| 3 | Database | App data modeling (versus analytics), SQLAlchemy, Alembic migrations | Cats, owners, suites and bookings live in Postgres with the real prices |
| 4 | Frontend fundamentals | TypeScript, React components and state, Next.js, Tailwind | A suites page renders data from the API |
| 5 | Public website | Routing, responsive design, basic SEO | Home, Suites, FAQ, About and Contact pages work on a phone |
| 6 | First deployment | Domains, DNS records, hosting, HTTPS, environment variables and secrets | Live on a test subdomain |
| 7 | Booking: availability | Date-range overlap, pricing rules, preventing double-booking | The API answers "is suite X free for these dates, and what does it cost?" |
| 8 | Booking: the flow | Forms, validation, email, booking states | A customer can request a booking and get a confirmation email |
| 9 | Admin and login | Authentication, sessions and tokens, protected pages | The owner can log in and manage bookings |
| 10 | Hardening | Security basics, backups, logging and monitoring | Backups automated, errors visible |
| 11 | AI chatbot | LLM APIs, prompt design, grounding answers in FAQ data | A chat widget answers MiHotel questions accurately |
| 12 | Launch | Final testing, moving the domain from Wix, portfolio write-up | mi-hotel.ca points to the new app |

**Why this order:**
- **Deploy in week 6, not at the end.** Hosting is where beginners get stuck, so it's better to hit it early while the app is small.
- **Database before frontend.** Start on familiar ground before the most unfamiliar part.
- **Chatbot near the end.** It's much better once there's real data and pages for it to draw on.
- **Weeks 10–12 can slip.** If earlier weeks take longer, these stretch out without blocking anything else.

## Week 1: Foundations

| Day | Focus | Hands-on | Status |
|---|---|---|---|
| 1 | How the web works | Inspect mi-hotel.ca with dev tools and the terminal. First learning note. | ✅ Done (dev tools exercise carried to Day 6) |
| 2 | Git and GitHub | Create the GitHub repo and push. Branches and pull requests. | Next |
| 3 | HTML and CSS | Hand-build a static "Luxury Suite" card. | |
| 4 | JavaScript for Python developers | Translate Python snippets into JavaScript. | |
| 5 | Docker | Run Postgres in a container and connect with `psql`. | |
| 6 | Review | Journal, learning notes, quiz. | |
