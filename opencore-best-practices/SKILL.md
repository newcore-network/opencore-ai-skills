---
name: opencore-best-practices
description: OpenCore framework best practices - bootstrap, controllers, imports, security, events, adapters, validation, runtime constraints. Use this skill when writing, reviewing, refactoring, or explaining code built with @open-core/framework and related OpenCore adapters. Trigger on any question about OpenCore, CitizenFX runtimes using OpenCore, FiveM resources using @open-core/*, or when scaffolding controllers, commands, net events, RPC, guards, throttles, or library events in this ecosystem.
---

# OpenCore Framework Best Practices

Apply these rules when writing or reviewing code built with `@open-core/framework`.

OpenCore is a TypeScript multiplayer runtime framework for CitizenFX-style runtimes, such us FiveM, RedM or RageMP. It is built around explicit bootstrap, DI, decorators, validation, events, adapters, and security primitives. It is **not** a gamemode or RP ruleset — never guess bootstrap behavior or invent implicit platform patterns.

## Imports & Entrypoints

See [imports-entrypoints.md](./imports-entrypoints.md) for:
- Runtime-specific entrypoint rules (`/server`, `/client`)
- When to use the root package
- Import anti-patterns to avoid

## Bootstrap

See [bootstrap.md](./bootstrap.md) for:
- `init(...)` patterns for server/client runtime entrypoints
- Mode selection: `CORE`, `RESOURCE`, `STANDALONE`
- Setup API timing (`set*Provider` before `init`) and zero-config defaults

## Controller Discovery

See [controller-discovery.md](./controller-discovery.md) for:
- `@Controller()` as the discovery unit
- How metadata-driven bootstrap works
- Autoload and import requirements

## Server Patterns

See [server-patterns.md](./server-patterns.md) for:
- Full decorator reference (`@Command`, `@OnNet`, `@OnRPC`, `@Guard`, `@Throttle`, etc.)
- Handler signature conventions (`Player` as first arg)
- Annotated command and net event examples

## Validation

See [validation.md](./validation.md) for:
- `z.tuple` for positional command args
- `z.object` for DTO payloads
- Coercion rules and client-payload trust policy

## Security

See [security.md](./security.md) for:
- `@Guard`, `@Throttle`, `@RequiresState`, `@Public()` usage
- Execution order of security decorators
- Safe defaults and privilege-boundary rules

## Library Events & Domain Communication

See [library-events.md](./library-events.md) for:
- `createServerLibrary` / `createClientLibrary` patterns
- `@OnLibraryEvent` vs net events
- When to use library events vs transport

## Adapters

See [adapters.md](./adapters.md) for:
- FiveM adapter setup (`@open-core/fivem-adapter/server`, `/client`)
- `adapter` configuration in `opencore.config.ts`
- Don't invent adapter APIs rule

## Runtime Constraints

See [runtime-constraints.md](./runtime-constraints.md) for:
- Server (Node.js), Client (neutral JS), NUI (browser) environments
- APIs forbidden in client runtime
- Where persistence and heavy logic belong

## Vite Configuration (Views / NUI)

See [vite-configuration.md](./vite-configuration.md) for:
- When to use Vite vs vanilla views in OpenCore CLI
- Recommended `@open-core/cli/vite` helper pattern
- Common Vite anti-patterns in OpenCore projects

## Plugins & Extensions

See [plugins.md](./plugins.md) for:
- Plugin contracts from runtime entrypoints
- Typed API extensions and module augmentation rules

## Common Mistakes & Checklist

See [common-mistakes.md](./common-mistakes.md) for:
- Top anti-patterns to catch before submitting code
- Pre-submission implementation checklist
- Quick decision guide (mode, entrypoint, event type)
