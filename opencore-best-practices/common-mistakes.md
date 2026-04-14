# Common Mistakes & Checklist

## Anti-patterns to avoid

- Importing server decorators from `@open-core/framework` instead of `@open-core/framework/server`
- Using `Server.init()` / `Client.init()` instead of the named `init()` export from the entrypoint
- Forgetting `await init(...)` on server or client
- Choosing the wrong mode (`CORE` vs `RESOURCE` vs `STANDALONE`)
- Omitting `coreResourceName` in server `RESOURCE` mode where the project expects it
- Creating decorated classes without `@Controller()`
- Calling setup APIs (`setPrincipalProvider`, `setPersistenceProvider`, etc.) after `init()` (too late)
- Assuming controller discovery works without checking the project/compiler setup (autoload vs explicit imports)
- Using `@OnNet(...)` or `@Command(...)` without `Player` as the first server parameter
- Accepting complex payloads without schema validation
- Using `@OnLibraryEvent()` as if it listens to net events
- Overusing `@Public()` and bypassing security accidentally
- Writing Node.js-specific APIs (`fs`, `path`, etc.) in client runtime files
- Mixing browser/Vite view code with client runtime code
- Mixing transport concerns (net events) with domain-event concerns (library events)
- Importing DI dependencies as `type-only` when OpenCore must resolve them during controller or service metadata scan on either server or client (for example `EventsAPI`, `RpcAPI`, `IClientRuntimeBridge`, or other runtime tokens)

## Pre-submission checklist

Before finishing OpenCore code, verify:

- [ ] Imports use the correct runtime entrypoint (`/server` or `/client`)
- [ ] Bootstrap exists (`await init({ mode: '...' })`) and uses the correct mode
- [ ] Server resource in `RESOURCE` mode includes `coreResourceName` when needed
- [ ] Any setup API calls (`set*Provider`) happen before `init()`
- [ ] Every handler class has `@Controller()`
- [ ] Controller discovery strategy matches the project setup (autoload or explicit imports)
- [ ] Server command/net handlers have `Player` as the first parameter
- [ ] Sensitive or structured inputs use `zod`
- [ ] Guards and throttles are present where abuse or privilege boundaries exist
- [ ] Client code avoids Node-only APIs
- [ ] View (Vite/NUI) code and client runtime code stay separated by environment
- [ ] Library events and net events are not conflated
- [ ] Constructor dependencies that OpenCore resolves at runtime are imported as real tokens, not `type-only`

## Quick decision guide

| Question | Answer |
|----------|--------|
| Need authoritative shared runtime? | Use `CORE` |
| Need a modular resource attached to core? | Use `RESOURCE` |
| Need an isolated runtime? | Use `STANDALONE` |
| Need server handlers? | Import from `@open-core/framework/server` |
| Need client handlers? | Import from `@open-core/framework/client` |
| Need internal domain events? | Use library events |
| Need network transport? | Use net events or RPC |
| Need safety? | Add schema validation and security decorators |
