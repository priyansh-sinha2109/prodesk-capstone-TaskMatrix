# Modular monolith

Status: Proposed.

Decision: A single API deployable keeps transaction boundaries and tracing simple while domain modules own their operations.

Alternatives and trade-offs: Microservices offer independent scaling but introduce network coordination and release overhead.
