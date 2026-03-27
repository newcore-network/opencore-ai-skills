# Plugins & Extensions

The Plugin API is the **boot-time extension system**. Plugins are compiled-in and installed explicitly via `init({ plugins: [...] })`. They install before full runtime bootstrap, so decorators and services can rely on plugin-provided extensions.

Do not confuse Plugin API with Library API — they serve different purposes:

| API | Purpose | When |
|-----|---------|------|
| Plugin API | Boot-time DI wiring and runtime API extension | Before `init()` completes |
| Library API | Domain events and runtime communication | During runtime (`createServerLibrary`, `@OnLibraryEvent`) |

## Server plugin

```ts
import type { OpenCorePlugin } from '@open-core/framework/server'

const serverPlugin: OpenCorePlugin = {
  name: 'example-server-plugin',
  install(ctx) {
    // ctx.server — register API extensions
    // ctx.di     — access the DI container
    // ctx.config — read framework config
    ctx.server.registerApiExtension('NPC', NPCDecorator)
  },
}

await init({ mode: 'CORE', plugins: [serverPlugin] })
```

## Client plugin

```ts
import type { OpenCoreClientPlugin } from '@open-core/framework/client'

const clientPlugin: OpenCoreClientPlugin = {
  name: 'example-client-plugin',
  install(ctx) {
    ctx.client.registerApiExtension('Widget', WidgetFactory)
  },
}

await init({ mode: 'CORE', plugins: [clientPlugin] })
```

## `registerApiExtension` rules

- Keys must be non-empty
- Reserved keys are protected
- Duplicate keys are rejected
- Existing API members cannot be overwritten

## Module augmentation

Use TypeScript module augmentation only when the repository already follows that pattern, or the task explicitly asks for plugin APIs. If the project doesn't already do it, mention it as an explicit design decision before adding it.

## Plugin contracts from runtime entrypoints

Always import plugin types from the runtime-specific entrypoint:

```ts
// Server plugin type
import type { OpenCorePlugin } from '@open-core/framework/server'

// Client plugin type
import type { OpenCoreClientPlugin } from '@open-core/framework/client'
```

Never import plugin types from the root `@open-core/framework` package.
