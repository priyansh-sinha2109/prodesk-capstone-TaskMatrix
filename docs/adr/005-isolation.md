# Session and tenant isolation

Status: Proposed.

Decision: Short-lived access JWTs, rotated refresh sessions and live membership checks support revocation and per-workspace roles.

Alternatives and trade-offs: Global roles or long-lived role claims reduce lookup cost but permit stale authorization.
