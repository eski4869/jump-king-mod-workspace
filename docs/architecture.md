# Architecture

## Layers

```text
External tools
    |
    v
Jump King Http Command Broker
    | target + user + command
    v
Feature Mod
    | resolve user when player-specific behavior is required
    v
Local Multiplayer Mod
    | player entity / input channel
    v
Jump King
```

EskiUI is a separate in-process UI adapter. It provides local command input and
notifications; it is not an HTTP transport and has no user-routing concept.

## Ownership

### Jump King Http Command Broker

- Owns the loopback HTTP listener.
- Validates registered targets.
- Queues opaque `user` and `command` strings.
- Does not know players or feature settings.

### Local Multiplayer Mod

- Owns Player 2 through Player 4.
- Owns split cameras and rendering.
- Owns loaded custom-block behavior replication.
- Owns first-winner routing into the native ending flow.
- Owns user-pattern to player-mask resolution.
- Owns additional-player virtual input state.
- Does not parse Radio Control commands or implement feature effects.

### Feature mods

- Own command parsing and feature state.
- Ask Local Multiplayer Mod for target players only when their behavior is
  player-specific.
- Continue normal Player 1 behavior when the optional multiplayer API is absent.

## Player 1

Normal keyboard and controller input remains attached to Player 1. Replacing
the native input path would require a broad patch and is outside the shared
multiplayer contract. Mod-generated input can target additional players through
the Local Multiplayer API.

Metrics, tower progress, and canonical game progression continue to observe
Player 1. They are not routed by user identity.
