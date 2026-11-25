# System architecture

The browser loads a Vite-built React client from Vercel. React Router selects a workspace route and RTK Query sends HTTPS requests to the Render-hosted Express modular monolith. Atlas holds durable data. Domain modules own identity, workspaces, projects, tasks, sprints and reporting.

## Request lifecycle

1. Assign a request ID, enforce HTTPS at the edge and match an allowlisted origin. Apply request-size and rate limits before parsing JSON.
2. Verify JWT signature, issuer, audience and expiry on protected routes. Check Origin and CSRF token on cookie-authenticated writes. Validate params, query and body with Zod.
3. Load current membership and required role. Resolve each parent by both its ID and tenant. Return 404 for hidden or missing resources and 403 for a known tenant with insufficient rights.
4. Controller passes validated values and identity to the domain service. Data-access methods require workspace and project scope. Service coordinates transactions and returns a DTO excluding private fields.
5. Map domain errors to the common API envelope. Record route template, request ID, latency and outcome. Monitor 5xx, refresh reuse, conflict rates and Atlas pool saturation.

Login issues a short-lived access JWT and a random refresh token. Store only its hash. Refresh atomically consumes the old session and creates its successor in the same family. Replay revokes the family. Logout revokes the family and expires its cookie. The client serializes refresh attempts.

Use indexed cursor reads and bounded page sizes before horizontal scaling. Multiple API instances share Atlas and will require shared rate-limit counters. Optional P2 live events would invalidate scoped RTK queries after commit. No event service is required for P0.

## Configuration

Only API origin is public build configuration. API secrets and configuration include MONGODB_URI, JWT_SIGNING_KEY, JWT_ISSUER, JWT_AUDIENCE, ALLOWED_ORIGINS and cookie settings. Separate development, preview and production values. Restrict Atlas access to the backend and rotate signing keys using key IDs.
