# Adapters

Adapters connect OpenCore to a specific multiplayer platform. They implement framework contracts for platform-specific APIs and determine the build target, output layout, and manifest generation. The architecture is:

```
Your Game Code → OpenCore Framework → Contracts (Interfaces) → Adapter → Platform
```

Changing the adapter migrates between platforms without rewriting gameplay code.

## Available adapters

| Package | Platform | Status |
|---------|----------|--------|
| `@open-core/fivem-adapter` | GTA V — FiveM / CitizenFX | Stable |
| `@open-core/ragemp-adapter` | GTA V — RageMP | Stable |
| `@open-core/redm-adapter` | RDR3 — RedM | Coming Soon (use FiveM adapter with `gameProfile: 'rdr3'` for now) |
| Node (built-in) | Development / testing | Default |

## Configuration

Adapters are configured in the CLI config file (`opencore.config.ts`), **not** inside `init()`:

```ts
import { defineConfig } from '@open-core/cli'
import { FiveMServerAdapter } from '@open-core/fivem-adapter/server'
import { FiveMClientAdapter } from '@open-core/fivem-adapter/client'

export default defineConfig({
  name: 'my-server',
  destination: '/path/to/server/resources',
  adapter: {
    server: FiveMServerAdapter(),
    client: FiveMClientAdapter(),
  },
})
```

## FiveM adapter

**Install:** `pnpm add @open-core/fivem-adapter`

```ts
import { FiveMServerAdapter } from '@open-core/fivem-adapter/server'
import { FiveMClientAdapter } from '@open-core/fivem-adapter/client'
```

What the adapter handles for you:
- Auto-generates `fxmanifest.lua`
- NUI / WebView integration (access via framework contracts, not raw platform APIs)
- State Bags
- Event transport layer
- Build targets: ES2020, CommonJS (server) / ES2020, IIFE, Neutral (client)
- Max players: 1024

## RageMP adapter

**Install:** `pnpm add @open-core/ragemp-adapter`

```ts
import { RageMPServerAdapter } from '@open-core/ragemp-adapter/server'
import { RageMPClientAdapter } from '@open-core/ragemp-adapter/client'
```

What the adapter handles for you:
- Output layout:
  ```
  ragemp-server/
  ├── packages/my-server/index.js          # Server bundle
  └── client_packages/my-server/index.js   # Client bundle
  ```
- Chat, WebView, key mapping, and notification integration (via framework contracts)
- Centralized exports registry
- Player identifier types: `socialClub`, `hwid`, `ip`
- Build target: Node 14 compatible (server)
- Max players: 5000

## RedM (interim)

RedM adapter is in development. In the meantime, use the FiveM adapter with a `gameProfile: 'rdr3'` override and add the required `rdr3_warning` to `fxmanifest.lua`. Max 32 players.

## Rules

- Do **not** invent adapter APIs. Only use what is exported by the installed adapter package.
- Do **not** use raw platform APIs (e.g. `mp.gui.chat.push`, `AddStateBagChangeHandler`, `SetNuiFocus`) directly in game code. If the framework exposes that capability, use the framework's abstraction — that is what the adapter is for.
- Check `package.json` to confirm which adapter is installed before generating adapter code.
- Adapters go in `defineConfig` in the CLI config, not inside `init()`.
- Run `opencore adapter check` in CI to validate that the adapter still covers all required framework contracts after a framework upgrade.
