# Security

Treat security decorators as part of the feature design, not optional polish. Every handler that involves privilege, player data, or mutation needs appropriate protection.

## Security decorators

| Decorator | Purpose |
|-----------|---------|
| `@Guard({ rank })` | Authorize by player rank |
| `@Guard({ permission })` | Authorize by named permission |
| `@Throttle(limit, windowMs)` | Rate-limit — e.g. `@Throttle(1, 2000)` = 1 call per 2 s |
| `@RequiresState(...)` | Gate execution on player or game state |
| `@Public()` | Explicitly mark a handler as open with no auth |

## Execution order

Security checks run in this order before the handler fires:

1. `@RequiresState()`
2. `@Guard()`
3. `@Throttle()`
4. Schema validation
5. Handler execution

This means state must be valid before authorization is checked, and rate limits are enforced on already-authorized requests.

## Safe defaults

- Prefer protected handlers over public handlers. Handlers are not open by default, but always be explicit.
- Use `@Public()` only for intentionally open, low-risk endpoints.
- Do not expose privileged mutations through public commands or net events.
- Do not remove guards just to "make a feature work" — fix the underlying problem instead.
- When in doubt about the right rank or permission, ask the user rather than guessing.
