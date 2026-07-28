# Jump King Mod Workspace

Architecture and workflow documentation for the `eski4869` Jump King mod
ecosystem.

## Repository map

See [`repositories.json`](repositories.json) for the canonical repository and
project-folder mapping. Working-copy locations are environment-specific and
are intentionally not recorded in this repository.

## Documentation

- [Architecture](docs/architecture.md)
- [Integration contracts](docs/integration-contracts.md)
- [Development and release workflow](docs/development-workflow.md)
- [Human verification checklist](docs/human-verification.md)

## Current shared infrastructure

- **EskiUI**: in-process command input and notification output
- **Jump King Http Command Broker**: local HTTP transport and command queues
- **Local Multiplayer Mod**: local player instances, cameras, user routing,
  and virtual input channels

Feature mods keep their own game rules. Shared infrastructure is optional and
resolved once during startup; repeated reflection scans are not permitted in
per-frame code.
