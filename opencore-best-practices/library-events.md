# Library Events & Domain Communication

Use library wrappers for internal domain events. Use net events and RPC for transport across runtime boundaries.

## Creating a library

```ts
import { Server } from '@open-core/framework/server'
import { Client } from '@open-core/framework/client'

// Server side
const characters = Server.createServerLibrary('characters')

// Client side
const inventory = Client.createClientLibrary('inventory')
```

## Emitting and listening

```ts
import { Controller, OnLibraryEvent, Server } from '@open-core/framework/server'

const characters = Server.createServerLibrary('characters')

@Controller()
export class CharacterListeners {
  @OnLibraryEvent('characters', 'session:created')
  onSessionCreated(payload: { sessionId: string; playerId: number }) {
    // react to domain event
  }
}

// Emit from anywhere in the same runtime
characters.emit('session:created', { sessionId: 's-1', playerId: 10 })
```

## Key rule

`@OnLibraryEvent()` listens only to `library.emit(...)` calls from the same library. It does **not** listen to arbitrary net events, CitizenFX events, or external emits unless the framework docs explicitly say so.

## When to use what

| Use case | Mechanism |
|----------|-----------|
| Internal domain events within server or client | Library events |
| Communication across server ↔ client boundary | Net events (`@OnNet`) or RPC (`@OnRPC`) |
| External game engine events | `@OnFrameworkEvent` / `@OnRuntimeEvent` |

Never conflate library events with net transport — they serve different layers.
