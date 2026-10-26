# Mihotel

A full-stack rebuild of the website for [MiHotel](https://www.mi-hotel.ca/), a cat boarding hotel in Scarborough, Ontario, which opened in 2024.

The current site runs on Wix and takes bookings through a Google Form. This project replaces it with a custom application: a public website, a booking system backed by a real database, an admin side for running the hotel, and possibly an AI assistant that answers customer questions.

It is also a learning project. I'm a data engineer learning full-stack development, so the repo records how the project was built as well as the code: the decisions, the alternatives that were rejected, and notes on concepts I learned along the way.

## Status

**Planning phase.** No application code yet.

## Repository layout

| Path | What's in it |
|---|---|
| `docs/journal/` | Dated session logs: what was discussed and done in each working session |
| `docs/decisions/` | Architecture Decision Records (ADRs): one file per significant decision, with context, options and consequences |
| `docs/learning/` | Concept notes written as I learn: networking, web fundamentals, backend, hosting, and so on |
| `docs/research/` | Background research, such as the audit of the existing site |
| `docs/superpowers/specs/` | Design specs for each part of the system (added once designs are approved) |

## Where to start reading

1. [Journal](docs/journal/) for the story in order
2. [Decisions](docs/decisions/) for why things are the way they are
3. [Learning notes](docs/learning/) for the concepts behind them
