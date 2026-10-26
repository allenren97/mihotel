# 0002. Python backend, TypeScript/React frontend

- **Date:** 2026-10-26
- **Status:** Accepted (the specific libraries will be confirmed during system design)

## Context

The builder knows Python and SQL well and has little frontend experience. The project should teach full-stack development and look credible in a portfolio, without so much new material at once that progress stalls. The time budget is about 2 hours a day.

## Options considered

1. **Python backend plus a JavaScript/TypeScript frontend** (for example FastAPI, PostgreSQL and React/Next.js). The backend and database build on existing skills, so the new learning is concentrated in the frontend. This is a common industry combination.
2. **All Python with server-rendered pages** (for example Django and HTMX). This is the fastest to build with the least to learn, but it teaches less frontend and shows less range in a portfolio.
3. **All TypeScript** (Next.js for both frontend and backend). One language throughout, but the existing Python skills go unused and the most is new at once.

## Decision

Option 1. The working plan for the stack:

- **Backend:** FastAPI (Python)
- **Database:** PostgreSQL, using SQLAlchemy and Alembic for migrations
- **Frontend:** Next.js (React) with TypeScript

## Consequences

- There's one genuinely new language to learn: TypeScript.
- The frontend and backend are separate applications that talk over an HTTP API. That means learning about API design and CORS, but it also gives the system clean boundaries.
- Hosting means deploying two applications plus a database. This is more work than a single app, but it teaches more.
