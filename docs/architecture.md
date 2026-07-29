# Architecture

## Layers

```text
External tool
    |
    | GET target + target-owned parameters
    v
Jump King Http Command Broker
    |
    | immutable parameter dictionary in a target FIFO queue
    v
Feature Mod
    |
    | optional ResolvePlayer(user)
    v
Local Multiplayer Mod
    |
    | concrete PlayerEntity instances
    v
Jump King
```

EskiUI is a separate in-process UI adapter. It provides local command input and notifications. It is not an HTTP transport and has no user-routing concept.

## Ownership

### Jump King Http Command Broker

- Owns the loopback HTTP listener.
- Interprets only the reserved `target` query parameter.
- Validates explicit target registration.
- Copies every other query parameter into a case-insensitive immutable dictionary.
- Owns one FIFO command queue per target.
- Invokes registered target-owned JSON state providers without storing feature state.
- Does not know commands, users, players, keys, durations, or feature settings.

### Local Multiplayer Mod

- Owns Player 2 through Player 4.
- Owns split cameras and rendering.
- Owns loaded custom-block behavior replication.
- Owns first-winner routing into the native ending flow.
- Owns deterministic user-pattern resolution.
- Returns zero or one concrete `PlayerEntity` to optional consumers.
- Persists exact user assignments received through its Broker target.
- Reports whether a concrete player belongs to the active split-screen view.
- Does not parse feature commands or own feature effects.

### Feature mods

- Own parameter validation, command parsing, and feature state.
- Read `user` only when their operation is player-specific.
- Resolve `user` once to a concrete `PlayerEntity`.
- Apply the operation to that entity without Player 1 through Player 4 branches.
- Keep multi-frame work in independent state keyed by `PlayerEntity`.
- Continue normal Player 1 behavior when Local Multiplayer Mod is absent.

## Player 1

Normal keyboard and controller input remains attached to Player 1. Replacing the native input path would require a broad game patch and is outside this integration contract.

Radio Control applies virtual input directly to each resolved player's `InputComponent`. Other feature mods operate on the resolved entity itself. Metrics, tower progress, and canonical game progression continue to observe Player 1 unless a feature explicitly defines otherwise.
