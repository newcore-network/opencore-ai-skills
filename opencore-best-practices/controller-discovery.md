# Controller Discovery

OpenCore behavior is metadata-driven. Decorators register metadata at class definition time; the runtime scans that metadata during bootstrap.

## `@Controller()` is the discovery unit

Every class that contains framework handlers (`@Command`, `@OnNet`, `@OnRPC`, `@OnLibraryEvent`, `@Export`, etc.) must be decorated with `@Controller()`. Without it, the runtime will not process the class's metadata.

## Discovery via autoload

OpenCore includes a built-in autoload system that scans directories for controller files. **You do not need to import controllers manually.** Simply place the controller file in the correct directory and the runtime will discover it automatically.

When adding a new controller:
1. Decorate the class with `@Controller()`
2. Place the file in the directory scanned by the autoload system
3. No explicit import is needed — the framework handles discovery

Never import controller files manually in the entrypoint just to register them; the autoload system makes this unnecessary and redundant.
