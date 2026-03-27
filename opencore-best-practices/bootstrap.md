# Bootstrap

Always initialize explicitly. Never assume the runtime boots itself.

## Basic init

Use `init()` imported from the runtime entrypoint:

```ts
// Server
import { init } from '@open-core/framework/server'

await init({ mode: 'CORE' })
```

```ts
// Client
import { init } from '@open-core/framework/client'

await init({ mode: 'CORE' })
```

## Mode selection

| Mode | Description | When to use |
|------|-------------|-------------|
| `CORE` | Authoritative runtime — owns shared state, auth, identity, sessions, providers | Central authority, multi-resource servers |
| `RESOURCE` | Feature resource — depends on CORE for shared state via remote-backed proxy | Modular gameplay resources attached to CORE |
| `STANDALONE` | Isolated runtime — no external CORE dependency, all providers local | Small servers, isolated modules, testing |

Do **not** assume a feature resource should run as `CORE` just because it has commands or controllers.

## Port behavior by mode

- In `CORE` → local adapter (direct memory access, O(1))
- In `RESOURCE` → remote proxy adapter (transparently calls CORE exports)
- `RESOURCE` waits for `core:ready` before fully initializing

## RESOURCE mode: `coreResourceName`

Server resources in `RESOURCE` mode must declare which resource is the CORE:

```ts
await init({
  mode: 'RESOURCE',
  coreResourceName: 'core',
})
```

Always check the project's existing resources to confirm the correct core resource name.

## Setup functions (run BEFORE init)

These must be called **before** `init()`. They wire up contracts that the framework requires:

```ts
import { setPrincipalProvider, setPersistenceProvider, setSecurityHandler } from '@open-core/framework/server'

setPrincipalProvider(MyPrincipalProvider)      // REQUIRED in CORE and STANDALONE
setPersistenceProvider(MyPlayerPersistence)    // Optional — default is in-memory
setSecurityHandler(MySecurityHandler)          // Optional
// setNetEventSecurityObserver(...)             // Optional
// setCommandErrorObserver(...)                 // Optional

await init({ mode: 'CORE' })
```

**`PrincipalProvider` is required in `CORE` and `STANDALONE` modes.** If missing, the framework fails fast with a fatal error at bootstrap. `RESOURCE` mode delegates this to CORE and does not need it.

## Dev mode

```ts
await init({
  mode: 'CORE',
  devMode: { enabled: true },
})
```

Enable during development for hot reload, event interception, player simulation, and state inspection.

## Plugins

Pass plugins to `init()` — they install before full bootstrap:

```ts
await init({
  mode: 'CORE',
  plugins: [myServerPlugin],
})
```

See [plugins.md](./plugins.md) for how to author plugins.

## Bootstrap sequence (internal)

1. Register platform adapters
2. Register core services
3. Load framework controllers
4. Register system processors
5. Check required providers (fails fast if missing)
6. Scan decorators and bind runtime behavior
7. Emit `core:ready`
