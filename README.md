# skillz

Public Hermes Agent skills.

Skills live under `hermes-agent/skills/<name>/`. Each directory has a `SKILL.md`.

## Install one skill

```bash
hermes skills install chumpuckai-devteam/skillz/hermes-agent/skills/hermes-layered-setup
```

## Use the repo as a tap

`hermes skills tap add` defaults to a `skills/` path. This repo keeps skills under the Hermes agent folder, so set the tap path after adding it:

```bash
hermes skills tap add chumpuckai-devteam/skillz
```

In `~/.hermes/skills/.hub/taps.json`, set that tap's path to `hermes-agent/skills/`. Hermes lists one directory level under the tap path, so the default `skills/` path will not see this repo.

```bash
hermes skills search layered
hermes skills install chumpuckai-devteam/skillz/hermes-agent/skills/hermes-layered-setup
```

## Skills

- `hermes-layered-setup` - rebuild or audit a Hermes setup in layers, instead of installing everything on day one. Source: https://x.com/hermeswatcher/status/2101884812189684015
