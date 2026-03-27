# Validation

Prefer explicit validation. Never trust raw client payloads.

## Schema patterns

| Input type | Schema pattern |
|------------|---------------|
| Positional command args | `z.tuple([...])` |
| DTO-style event payload | `z.object({...})` |
| User-supplied strings/numbers | Use coercion (`z.coerce.number()`, etc.) intentionally |

## When to add a schema

Add a schema when:
- the input is complex or structured
- the input crosses a security or privilege boundary
- the input could be abused if malformed

If the input is trivial and single-valued, a schema is still preferred but lower priority.

## Coercion rule

Use coercion deliberately for user-facing input (commands), where the player types strings that represent numbers. Do **not** use coercion blindly on internal structured payloads — validate exact types instead.

## Example

```ts
import { z } from 'zod'

// Positional command args
const TransferSchema = z.tuple([
  z.coerce.number().int().positive(), // target player ID
  z.coerce.number().min(1),           // amount
])

// DTO payload
const PayloadSchema = z.object({
  action: z.string(),
  amount: z.number().int().positive(),
})
```

Pass the schema directly to the decorator option — OpenCore validates before invoking the handler.
