# Architecture Decision Records

Each significant decision gets its own numbered file: `NNNN-short-title.md`. Records are never deleted. If a decision is reversed, a new record supersedes the old one and the old one's status is updated to say so.

## Template

```markdown
# NNNN. Title

- **Date:** YYYY-MM-DD
- **Status:** Proposed | Accepted | Superseded by NNNN

## Context
What situation or problem forced a decision?

## Options considered
The alternatives, with their trade-offs.

## Decision
What was chosen.

## Consequences
What becomes easier or harder because of this choice.
```

## Index

| # | Decision | Status |
|---|---|---|
| [0001](0001-rebuild-as-custom-full-stack-app.md) | Rebuild the site as a custom full-stack application | Accepted |
| [0002](0002-tech-stack-python-backend-react-frontend.md) | Python backend, TypeScript/React frontend | Accepted |
