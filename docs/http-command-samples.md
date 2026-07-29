# HTTP Command Samples

Jump King Http Command Broker listens on `127.0.0.1:8081`. All command
requests use `GET /command`. The Broker owns only the `target` parameter; each
target mod defines and validates the remaining parameters.

In Streamer.bot, add `Core > Network > Fetch URL` to an action and use these
settings:

| Setting | Value |
| --- | --- |
| URL | One of the target URLs below |
| Parse Results as JSON | Off |
| Auto-Type non-JSON Result | Off, when shown |
| Variable Name | `brokerResult` |

A successful command stores the plain text response `queued` in
`%brokerResult%`.

The examples use these Twitch trigger variables:

| Variable | Use |
| --- | --- |
| `%user%` | Login name used by Local Multiplayer player routing |
| `%userId%` | Stable Twitch account ID suitable for a save-state key |
| `%message%` | Chat message used as a Radio Control program |

## Local Multiplayer Mod

Target: `local_multiplayer`

Required parameters:

| Parameter | Values |
| --- | --- |
| `user` | Exact user name to assign |
| `command` | `p1`, `p2`, `p3`, or `p4` |

The assignment updates `UserOverrides` for the currently selected mode. The
requested player must exist in that mode.

```text
http://127.0.0.1:8081/command?target=local_multiplayer&user=%user%&command=p1
http://127.0.0.1:8081/command?target=local_multiplayer&user=%user%&command=p2
```

Create one Streamer.bot action per destination player, or substitute `p1`
through `p4` from a trusted action argument.

## Radio Control Mod

Target: `radio_control`

| Parameter | Requirement | Description |
| --- | --- | --- |
| `command` | Required | Frame-based input program |
| `user` | Optional | Player-routing user name |

```text
http://127.0.0.1:8081/command?target=radio_control&user=%user%&command=%message%
```

For a fixed channel-point action, replace `%message%` with a program such as
`jr35%20w10%20l5`.

Common command forms:

| Command | Meaning |
| --- | --- |
| `j35` | Hold jump for 35 frames |
| `jr35` | Hold jump and right for 35 frames |
| `jl35` | Hold jump and left for 35 frames |
| `r10` | Hold right for 10 frames |
| `l10` | Hold left for 10 frames |
| `w60` | Wait for 60 frames |
| `o` | Press Snake |
| `p` | Press Boots |

Target: `menu_control`

Required parameter: `command`

Supported commands: `up`, `down`, `space`, `confirm`, `jump`, `esc`, `pause`,
and `cancel`.

```text
http://127.0.0.1:8081/command?target=menu_control&command=down
http://127.0.0.1:8081/command?target=menu_control&command=space
http://127.0.0.1:8081/command?target=menu_control&command=esc
```

## Keyed Save States

Target: `keyed_save_states`

| Parameter | Requirement | Description |
| --- | --- | --- |
| `command` | Required | `save` or `load` |
| `key` | Required | Storage key using letters, numbers, `_`, or `-` |
| `user` | Optional | Player-routing user name |

```text
http://127.0.0.1:8081/command?target=keyed_save_states&command=save&key=%userId%&user=%user%
http://127.0.0.1:8081/command?target=keyed_save_states&command=load&key=%userId%&user=%user%
```

`%userId%` identifies stored data. `%user%` selects the current player and does
not replace the key. In single-player use, `user=%user%` may be omitted.

## Super Saiyan

Target: `super_saiyan`

| Parameter | Requirement | Description |
| --- | --- | --- |
| `command` | Required | Effect command |
| `user` | Optional | Player-routing user name |

```text
http://127.0.0.1:8081/command?target=super_saiyan&user=%user%&command=activate
http://127.0.0.1:8081/command?target=super_saiyan&user=%user%&command=deactivate
http://127.0.0.1:8081/command?target=super_saiyan&user=%user%&command=kamehameha
http://127.0.0.1:8081/command?target=super_saiyan&user=%user%&command=genkidama
http://127.0.0.1:8081/command?target=super_saiyan&user=%user%&command=dragon-ball
```

The `user` parameter may be omitted in single-player use.

## Emote Mod

Target: `emote`

Required parameter: `command`

```text
http://127.0.0.1:8081/command?target=emote&command=random
http://127.0.0.1:8081/command?target=emote&command=happy
http://127.0.0.1:8081/command?target=emote&command=sad
http://127.0.0.1:8081/command?target=emote&command=thinking
http://127.0.0.1:8081/command?target=emote&command=angry
```

## Broker Inspection

List currently registered command targets:

```text
http://127.0.0.1:8081/targets
```

Read all registered state providers or one target:

```text
http://127.0.0.1:8081/states
http://127.0.0.1:8081/states?target=super_saiyan
```

Command requests return `202 queued` when accepted. Use `/targets` when a
request returns `400 unknown target`.
