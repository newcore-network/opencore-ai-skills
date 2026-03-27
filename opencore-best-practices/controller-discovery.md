# Controller Discovery

OpenCore behavior is metadata-driven. Decorators register metadata at class definition time; the runtime scans that metadata during bootstrap.

## `@Controller()` is the discovery unit

Every class that contains framework handlers (`@Command`, `@OnNet`, `@OnRPC`, `@OnLibraryEvent`, `@Export`, etc.) must be decorated with `@Controller()`. Without it, the runtime will not process the class's metadata.

## Discovery requires the file to be loaded

If a decorated class is never imported (directly or through an autoload system), it will never be discovered — no error is thrown, the handlers simply don't exist.

When adding a new controller:
1. Decorate the class with `@Controller()`
2. Ensure the file is imported by the entrypoint **or** picked up by the repository's autoload system
3. Never rely on side effects from dead files

## Autoload vs explicit import

Some projects use a glob-based autoload that scans directories for controller files. If the project has this, placing the file in the correct directory is sufficient. If not, import the controller file explicitly before `Server.init()` / `Client.init()` completes.

When generating a new controller file, always mention this requirement to the user.
