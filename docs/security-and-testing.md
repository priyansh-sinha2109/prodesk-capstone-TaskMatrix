# Security and verification plan

Use bcrypt with a benchmarked work factor and a 72-byte password ceiling. Rate-limit registration, login, refresh and invitation redemption. Return generic login failures. Hash high-entropy session and invitation secrets. Set refresh cookies Secure, HttpOnly, host-only and SameSite=Lax for a same-site API deployment. Verify Origin and a session-bound CSRF token on refresh/logout. Test credentialed CORS with the exact frontend origin. Prefer same-site custom domains for Vercel and Render. Avoid logging credentials, cookies, invitation links, emails or free-text task bodies.

| Test level | Scenario | Required outcome |
| --- | --- | --- |
| Vitest | Role matrix, task transitions, rank insertion, sprint completion | Deterministic results including empty columns and no unfinished work |
| Supertest + isolated Atlas-compatible replica set | Substitute foreign workspace, project, sprint and task IDs | No tenant disclosure or mutation |
| Supertest | Race invitation redemption, refresh reuse, concurrent starts, move versus completion | At most one accepted transition, consistent related records |
| Supertest | Viewer writes, expired session, CSRF mismatch, malformed filters and oversize bodies | Expected error envelope without stack traces |
| Playwright | Two accounts join a workspace, plan a sprint, move a task via keyboard, complete sprint | Counts, backlog and histories agree after reload |
| Playwright | Mobile navigation, focus restoration, empty and error screens | Core controls usable at narrow widths and by keyboard |

Before deployment run unit, integration and browser suites, sample p95 on the PRD dataset, check bundle configuration for secrets and rehearse rollback. Deploy compatibility-preserving indexes first, then API and frontend. Smoke-test login/refresh, membership denial and one complete task journey. Roll back to previous builds if checks fail and retain compatible data/indexes.
