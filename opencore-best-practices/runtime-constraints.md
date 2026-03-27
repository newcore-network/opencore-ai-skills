# Runtime Constraints

OpenCore targets three distinct runtime environments. Keep code for each environment strictly separate.

## Environments

| Runtime | Environment | Notes |
|---------|-------------|-------|
| Server | Full Node.js | Persistence, external APIs, heavy logic |
| Client | Neutral JavaScript — no Node.js | Game interaction, natives, input, local presentation |
| NUI / Views | Browser | Separate concern from game client runtime |

## Client runtime rules

Do **not** use any of the following in client runtime code:
- `fs`, `path`, `http`, `https`, `crypto`, `child_process`, `os`, `stream`
- Any other Node.js built-in module
- Browser APIs (e.g. `document`, `window`, `fetch`) unless you are inside a NUI/view file

Client code should focus on:
- Receiving and reacting to server net events
- Calling game natives
- Handling local player input and UI state
- Emitting net events to the server

## Server runtime rules

Use the server runtime for:
- Persistence and database access
- Heavy computation or external HTTP calls
- Source-of-truth decisions and authoritative state
- Auth, identity, and permission checks

## NUI / Views

NUI runs in a browser embedded in the game client. It has full browser APIs but is a completely separate concern from game client code. Never mix game client natives with NUI browser code.
