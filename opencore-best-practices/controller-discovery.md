# Controller Discovery

OpenCore behavior is metadata-driven. Decorators register metadata at class definition time; the runtime scans that metadata during bootstrap.

## `@Controller()` is the discovery unit

Every class that contains framework handlers (`@Command`, `@OnNet`, `@OnRPC`, `@OnLibraryEvent`, `@Export`, etc.) must be decorated with `@Controller()`. Without it, the runtime will not process the class's metadata.

## Discovery via autoload

OpenCore discovery is metadata-driven and may rely on compiler-generated auto-imports in many projects. Some setups still use explicit imports in entrypoints.

When adding a new controller:
1. Decorate the class with `@Controller()`
2. Follow the project's discovery strategy (autoload/auto-import or explicit entrypoint imports)
3. Verify the controller is actually loaded in your runtime build

Do not assume discovery behavior blindly — check current project/compiler configuration and existing patterns before adding or removing imports.
