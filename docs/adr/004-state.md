# Redux Toolkit and RTK Query

Status: Proposed.

Decision: One cache owns server entities. Feature slices retain only cross-page interaction state.

Alternatives and trade-offs: Context plus manual fetching reduces dependencies but requires bespoke invalidation and rollback.
