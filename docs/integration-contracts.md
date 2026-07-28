# Integration Contracts

## HTTP command envelope

```text
target  required routing identifier
user    optional external identity
command required target-owned command text
```

The Broker transports these values without interpreting game behavior.

## Local Multiplayer API v1

```csharp
public static int GetApiVersion();
public static bool IsActive();
public static int GetPlayerCount();
public static int ResolvePlayerMask(string user);
public static PlayerEntity GetPlayer(int playerNumber);
public static int GetCurrentViewPlayerMask();
public static void SubmitInput(
    int playerNumber,
    InputComponent.State held,
    InputComponent.State pressed);
```

Player-mask bits are `1`, `2`, `4`, and `8` for Player 1 through Player 4.

In Single Player mode, a missing user resolves to Player 1. In multiplayer
modes, a missing user resolves to no player. Overlapping allow-list patterns may
resolve to multiple players.

## Optional dependency pattern

Consumers do not add a hard assembly reference. At startup they locate
`LocalMultiplayerMod.LocalMultiplayerApi`, bind matching static methods to typed
delegates, and retain those delegates. If the API is absent, the consumer uses
its normal Player 1 behavior. This is an intentional optional adapter, not a
runtime fallback chain.

## Feature semantics

- **Radio Control Mod** may target every bit in a resolved player mask.
- **Super Saiyan** keeps independent effect state for each resolved player.
- **Keyed Save States** requires exactly one resolved player because one key
  stores one player state. Zero or multiple matches are ignored.
- **Emote Mod** is intentionally not integrated yet.
