# Server Patterns

Use server decorators and entities from `@open-core/framework/server`.

## Decorator reference

| Decorator | Purpose |
|-----------|---------|
| `@Controller()` | Discovery unit — required on every handler class |
| `@Command(...)` | Register a chat/console command |
| `@OnNet(...)` | Listen for a network event from a client |
| `@OnRPC(...)` | Expose an RPC endpoint |
| `@OnTick()` | Run on every game tick |
| `@OnFrameworkEvent(...)` | Listen for a framework-level event |
| `@OnLibraryEvent(...)` | Listen for a domain library event |
| `@OnRuntimeEvent(...)` | Listen for a runtime lifecycle event |
| `@Export(...)` | Expose a function as a CitizenFX export |
| `@Guard(...)` | Authorization check (rank or permission) |
| `@Throttle(...)` | Rate limiting |
| `@RequiresState(...)` | Gate execution on player/game state |
| `@Public()` | Mark a handler as intentionally open (no auth) |

## Handler signature rules

- `@Command(...)`: first runtime argument must be `Player`
- `@OnNet(...)`: first runtime argument must be `Player`
- Security decorators (`@Guard`, `@RequiresState`) assume the player context is available

Never omit `Player` as the first parameter on server command and net handlers.

## Command example

```ts
import { Controller, Command, Guard, Player, Throttle } from '@open-core/framework/server'
import { z } from 'zod'

const TransferSchema = z.tuple([
  z.coerce.number().int().positive(),
  z.coerce.number().min(1),
])

@Controller()
export class BankController {
  @Command({
    command: 'transfer',
    usage: '/transfer <id> <amount>',
    schema: TransferSchema,
  })
  @Guard({ rank: 1 })
  @Throttle(1, 2000)
  async transfer(player: Player, args: z.infer<typeof TransferSchema>) {
    const [targetId, amount] = args
    player.emit('chat:message', `transfer -> ${targetId} (${amount})`)
  }
}
```

## Net event example

```ts
import { Controller, OnNet, Player } from '@open-core/framework/server'
import { z } from 'zod'

const PayloadSchema = z.object({
  action: z.string(),
  amount: z.number().int().positive(),
})

@Controller()
export class BankEventsController {
  @OnNet('bank:action', { schema: PayloadSchema })
  async onBankAction(player: Player, payload: z.infer<typeof PayloadSchema>) {
    player.emit('chat:message', `action=${payload.action} amount=${payload.amount}`)
  }
}
```
