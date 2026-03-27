# Imports & Entrypoints

Always use runtime-specific entrypoints. Never mix server and client imports.

## Correct entrypoints

| Runtime | Entrypoint |
|---------|-----------|
| Server  | `@open-core/framework/server` |
| Client  | `@open-core/framework/client` |
| Shared / kernel-level | `@open-core/framework` (only when the project explicitly uses it) |

## Key exports per entrypoint

**Server:**
```ts
import { init, Controller, Command, OnNet, Player, Guard, Throttle, RequiresState, Public } from '@open-core/framework/server'
import { setPrincipalProvider, setPersistenceProvider, setSecurityHandler } from '@open-core/framework/server'
```

**Client:**
```ts
import { init, Controller } from '@open-core/framework/client'
```

## Anti-patterns

- Do **not** import server decorators or runtime APIs from the root `@open-core/framework` unless the repository already does that intentionally.
- Do **not** import client APIs in server files or vice versa.
- Do **not** use `Server.init()` / `Client.init()` — the current API uses named `init()` imported from the entrypoint.
- Do **not** guess which entrypoint a symbol comes from — check the installed package exports.
