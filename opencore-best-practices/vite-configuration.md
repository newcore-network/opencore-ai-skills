# Vite Configuration (Views / NUI)

OpenCore CLI supports browser views in two modes:

- `vite` (recommended for React/Vue/Svelte, Tailwind/PostCSS, modern UI tooling)
- `vanilla` (simple HTML/CSS/JS/TS without Vite)

## Recommended setup

Prefer a shared root `vite.config.*` and build it from `@open-core/cli/vite`:

```ts
import { createOpenCoreViteConfig } from '@open-core/cli/vite'

export default createOpenCoreViteConfig()
```

This keeps view root/output resolution aligned with the OpenCore compiler and avoids manual path drift.

## Rules

- Configure UI framework concerns in Vite (plugins, aliases, PostCSS, CSS tooling), not in OpenCore runtime code.
- Keep game client code and browser view code separate (no natives in views, no DOM in client runtime files).
- Let the CLI resolve views as `vite` or `vanilla` from project config/detected Vite config.
- Use per-view scripts only when truly needed for local UI development.

## PostCSS compatibility (`postcss.config.mjs`)

FiveM and RageMP often run with older embedded CEF versions. Modern CSS features such as native nesting and `color-mix()` may not be fully available there.

Use a project-level `postcss.config.mjs` with `postcss-preset-env` to transpile modern CSS syntax. A working production-style example:

```js
export default {
  plugins: {
    '@tailwindcss/postcss': {},
    'postcss-preset-env': {
      browsers: 'chrome 97',
      stage: 1,
      preserve: false,
      features: {
        'cascade-layers': true,
        'color-functional-notation': true,
        'nesting-rules': true,
        'oklab-function': true,
      },
    },
  },
}
```

Notes:
- Add `postcss-preset-env` (and Tailwind PostCSS plugin if used) to dev dependencies.
- Keep this config at project root so OpenCore/Vite can resolve it consistently.
- Do not assume runtime CEF supports modern CSS even if local browser dev does.

## Common mistakes

- Writing custom output paths that conflict with OpenCore compiler output.
- Duplicating or overriding OpenCore view root resolution manually.
- Using browser APIs in client runtime files because "the project has Vite".
- Assuming Vite config replaces adapter/runtime configuration in `opencore.config.ts`.
- Relying on modern CSS features without PostCSS fallback for older CEF targets.

## Quick check

Before shipping UI changes:

- Root `vite.config.*` exists and is OpenCore-compatible
- View build still integrates into final resource artifacts
- No runtime boundary violations (server/client/view APIs mixed)
