# Integration Contracts

## HTTP Broker

`target` is the only transport-owned field. All remaining query parameters are target-owned.

```text
GET /command?target=radio_control&user=alice&command=jr35

Broker route:
  target = radio_control

Queued parameters:
  user = alice
  command = jr35
```

```csharp
// CommandQueueRegistry
public bool Register(string target);

public bool Enqueue(
    string target,
    IReadOnlyDictionary<string, string> parameters);

public bool TryDequeue(
    string target,
    out IReadOnlyDictionary<string, string> parameters);

// StateProviderRegistry
public bool Register(string target, Func<string> provider);
```

State providers run on the HTTP thread and may only serialize thread-safe
target-owned state.

The dictionary is a case-insensitive immutable copy. The Broker does not validate target-owned fields.

## Local Multiplayer API v3

```csharp
public static int GetApiVersion();
public static bool IsActive();
public static PlayerEntity ResolvePlayer(string user);
public static bool IsPlayerInCurrentView(PlayerEntity player);
```

In Single Player mode, a missing user resolves to Player 1. In multiplayer modes, a missing user resolves to no player. Exact names take priority over patterns. Otherwise, the first matching allow list from Player 1 through Player 4 wins, so one user never resolves to multiple players.

`ResolvePlayer` is the feature boundary. Player numbers and routing masks are internal implementation details and must not leak into consumers.

Local Multiplayer also owns the `local_multiplayer` Broker target:

```text
GET /command?target=local_multiplayer&user=alice&command=p2
```

`p1` through `p4` move the exact user into that player's allow list for the currently selected mode and persist the XML settings.

## Optional Dependency Pattern

Consumers do not add a hard assembly reference. They locate `LocalMultiplayerMod.LocalMultiplayerApi`, bind the required static methods to typed delegates, and retain those delegates. A failed lookup is repeated only when the loaded assembly count changes, so load order is supported without per-frame reflection scans.

If the API is absent, the consumer's resolver returns the normal Player 1 entity. If the API is present, its result is authoritative; an empty result remains empty.

## Feature Semantics

- **Radio Control Mod** resolves a request once, then owns an independent command channel for the returned `PlayerEntity`.
- **Super Saiyan** resolves a request once, then owns independent multi-frame effect state for the returned `PlayerEntity`.
- **Keyed Save States** stores and loads the returned player's state.
- **Emote Mod** consumes the common Broker parameter dictionary but is not player-routed.

## Processing Rule

```text
HTTP thread:
  copy request parameters -> target queue

Feature Mod game-thread update:
  dequeue one request
  -> validate target-owned fields
  -> resolve user when needed
  -> append work to target-owned player state

Every game frame:
  advance all independent player states
```

The Broker queue serializes request acceptance per target. It does not serialize ongoing work inside a feature mod. Parallel per-player behavior is owned by the feature runtime.
