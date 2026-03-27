# Common Mistakes & Checklist

## Anti-patterns to avoid

- Importing server decorators from `@open-core/framework` instead of `@open-core/framework/server`
- Using `Server.init()` / `Client.init()` instead of the named `init()` export from the entrypoint
- Forgetting to call `setPrincipalProvider(...)` before `init()` in CORE or STANDALONE mode (causes fatal bootstrap error)
- Forgetting `await init(...)` on server or client
- Choosing the wrong mode (`CORE` vs `RESOURCE` vs `STANDALONE`)
- Omitting `coreResourceName` in server `RESOURCE` mode where the project expects it
- Creating decorated classes without `@Controller()`
- Writing controllers that are never imported or autoloaded
- Using `@OnNet(...)` or `@Command(...)` without `Player` as the first server parameter
- Accepting complex payloads without schema validation
- Using `@OnLibraryEvent()` as if it listens to net events
- Overusing `@Public()` and bypassing security accidentally
- Writing Node.js-specific APIs (`fs`, `path`, etc.) in client runtime files
- Mixing transport concerns (net events) with domain-event concerns (library events)

## Pre-submission checklist

Before finishing OpenCore code, verify:

- [ ] Imports use the correct runtime entrypoint (`/server` or `/client`)
- [ ] Bootstrap exists (`await init({ mode: '...' })`) and uses the correct mode
- [ ] Server resource in `RESOURCE` mode includes `coreResourceName` when needed
- [ ] Every handler class has `@Controller()`
- [ ] New controller files are reachable by imports or autoload
- [ ] Server command/net handlers have `Player` as the first parameter
- [ ] Sensitive or structured inputs use `zod`
- [ ] Guards and throttles are present where abuse or privilege boundaries exist
- [ ] Client code avoids Node-only APIs
- [ ] Library events and net events are not conflated

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
