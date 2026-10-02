---
name: hermes-layered-setup
description: Use when rebuilding or auditing a Hermes setup.
version: 1.0.0
author: chumpuckai-devteam
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [hermes, setup, rebuild, audit, layers]
    related_skills: [hermes-agent]
---

# Hermes Layered Setup

## Overview

Rebuild or review a Hermes setup by deciding what deserves to exist. Do not reinstall every skill, open a dozen profiles, or reconnect every service on day one.

Walk 14 layers. For each one, classify it `now`, `wait`, or `earned`, and name the signal that would move a waiting layer to `now`. Inspect the live setup before recommending changes. Do not edit config, install integrations, or create profiles until the user approves the ranked list.

This is a decision procedure distilled from the HermesWatcher article "If I Had to Build My Hermes Setup From Zero Today, I'd Start Here" (https://x.com/hermeswatcher/status/2101884812189684015). It is not a copy of that article. For command and config details, load `hermes-agent` and the matching reference, and confirm flags with `hermes <command> --help` before running them.

## When to Use

- A clean install, a vanished setup, or "rebuild Hermes from zero".
- "Is this overbuilt?" or "what should I add next?"
- An audit after real use.
- Deciding whether a new profile, skill, MCP server, plugin, cron job, fallback, or external memory has earned a place.

Do not use for:

- Implementing one Hermes feature. Load `hermes-agent` and the matching reference instead.
- Authoring a skill file. Use the skill-authoring workflow.
- Silent reconfiguration. This skill stops for approval before it changes anything.

## Rules that do not bend

1. One primary profile, one known workspace, and one trusted primary model until a later layer earns durable state.
2. A profile separates Hermes-managed state (config, memory, sessions, skills, cron, logs). It is not a filesystem sandbox. Profile identity and working directory are separate decisions.
3. Temporary independence is delegation. A separate profile is for a role that still needs its own memory, skills, schedule, credentials, or model tomorrow.
4. Do not schedule a workflow that still needs babysitting. Cron starts a fresh session and cannot depend on today's hidden chat context.
5. Measure cost before deleting or rerouting. `hermes prompt-size` is the fresh-session baseline. In-session `/usage` is what this conversation spent. `/compress` shrinks old history. None of those is a reason to rip out a layer that has a job.
6. Recovery is part of the setup, not a later project. A backup that lives only on the disk you are protecting is not a recovery plan.
7. Secrets stay in `.env`, `auth.json`, or a supported secret store. Never put them in prompts, project context, memory, or this report. Report locations, never values.
8. Change config with `hermes config set` / `unset`. Do not hand-edit `config.yaml`.
9. The self-audit is read-only until the user approves a specific change.

## Procedure

### 1. Inspect, do not edit

Done when you can name the active profile, workspace, primary model, and whether `hermes doctor` reports a material failure. Do not fix anything in this step except a doctor failure that blocks inspection itself, and say so.

Run, and do not paste secret-bearing output:

- `hermes status`
- `hermes doctor`
- `hermes config path` and `hermes config env-path` (paths only)
- `hermes config check`
- `hermes tools --summary`
- `hermes prompt-size`
- `hermes skills list`
- `hermes plugins list`
- `hermes mcp list`
- `hermes cron list` and `hermes cron doctor`
- `hermes fallback list`
- `hermes memory status`
- `hermes profile list`
- `hermes security audit` when third-party plugins, MCP servers, or hub skills are installed

If a command is missing on this install, say so. Do not invent a replacement flag.

### 2. Classify all 14 layers

Done when every layer below has exactly one of `now`, `wait`, or `earned`, plus a one-line reason. A `wait` line must name the signal that would earn it. Do not mark a later layer `now` to compensate for a weak earlier one.

### 3. Build only what is classified `now`

Follow the build order below. Stop when the first-hour bar is met unless the user already has a week of real use to justify the next band. Load `hermes-agent` before any config or install change.

### 4. Audit, rank, and wait

Done when the user has a ranked list and you have not applied it.

Separate verified facts from recommendations. Rank by practical impact. Wait for approval before modifying anything. After approval, change one ranked item, re-check the command that proves it, then stop or continue only if the user asked for the rest.

## The 14 layers

### 1. Foundation

Job: one clean Hermes that can finish a normal conversation.

Now: one install, one primary profile, one known working directory, one provider and model, and a stated location for config vs credentials.

Wait: extra profiles. Signal: a specialist that still needs its own durable state tomorrow.

Check: `hermes setup` on a fresh install, then `hermes status` and `hermes doctor`.

### 2. Models

Job: one brain for the main conversation.

Now: one primary model you trust.

Wait: auxiliary overrides, fallbacks, and local inference. Signals: measured cost or latency on side jobs; a qualifying provider failure that should not stop the workflow; hardware and a workflow that actually justify local inference.

Auxiliary slots (compression, vision, titles, skill search, approvals) stay on their default automatic behavior until one of those signals exists. Fallbacks are for continuity, not for collecting providers. Order: primary model, then cheap or fast auxiliary routing, then fallbacks, then local.

Check: current model from config, `hermes fallback list`. Do not run interactive `hermes model` unless the user is at a prompt and asked to pick.

### 3. Memory and context

Job: put information where it belongs. These stores are not interchangeable.

- SOUL: who the agent is and how it should generally behave.
- Built-in memory: a few durable facts and preferences. Small on purpose.
- Project context (`HERMES.md`, `.hermes.md`, `AGENTS.md`, and other supported context files): rules that belong to the work, not the person.
- Session history: a conversation that can be resumed or searched. Not permanent memory.
- External memory: only when built-in memory cannot do a named job (larger semantic retrieval, deliberate shared memory, or a provider-specific behavior you can point at).

A preference such as "keep summaries short" can be memory. A rule such as "reports in this repo cite primary sources" is project context. The repeatable procedure is a skill. The long chat where you figured that out stays a session.

Now: built-in memory plus tight project context for the first real workspace.

Wait: an external provider (`hermes memory status` shows the active one). Signal: a missing job you can name.

### 4. Agent structure

Job: one owner until another owner earns a permanent job.

Now: one primary profile. Use delegation for parallel research, an independent review, a separate investigation, or work that would flood the main conversation.

Wait: another profile, Bot Mode, nested delegation, Kanban, or a standing specialist team. Signal: the role still needs its own memory, skills, schedule, credentials, or model tomorrow.

Rule: temporary independence is delegated. Durable state is separated.

### 5. Tools and permissions

Job: the smallest set of actions the current workflow must perform.

Ask, for each enabled capability: what must it read, what must it change, does it need shell or code execution, where do those commands run, and which actions should stop for approval?

Local terminal execution runs as the OS user. That is not an isolated sandbox. Capability and isolation are separate decisions. Approvals reduce risk. They do not turn host-local execution into a sandbox. Unattended runs (cron and other non-interactive work) need their own approval story, not a copy of the interactive one.

Now: only the tools the first real job needs.

Wait: extra toolsets and isolated or remote execution. Signal: a workflow that fails without that capability, or a boundary the host user should not have.

Check: `hermes tools --summary`. If you cannot explain why a capability is enabled, classify it as a removal candidate, not as a default.

### 6. Skills

Job: reusable procedures, loaded when the task needs them.

Progression:

1. Use bundled skills first. Learn what is already installed before adding packs.
2. Add an external skill when a real capability is missing.
3. Create a custom skill when a procedure specific to this work repeats.
4. Auto-load (`skills.auto_load`, set with `hermes config set`) only for a procedure that belongs in almost every session of that profile. Auto-load puts the full skill in the prompt and gives up task-specific loading.
5. One-session preload is `hermes --skills <name>`, not a config change.

Now: bundled skills that match the first job.

Wait: external packs and auto-load. Signal: a repeated gap, or a profile whose every session is the same job.

Check: `hermes skills list`. Periodically prune. A skill folder is not a junk drawer.

### 7. Plugins, MCP, and integrations

Job: the smallest extension layer that solves the job.

Order:

1. Built-in or direct integration, if Hermes already supports it.
2. Skill, if the tools exist and the missing piece is a procedure.
3. MCP, if an external server already exposes the tools. Filter tools. Do not expose everything the server offers.
4. Plugin, if you need something native to Hermes (custom tools, hooks, commands, providers, or desktop behavior). Review what it asks permission to do (`hermes plugins capabilities`).

Connect email, calendar, GitHub, Notion, browser tooling, or cloud storage when that service enters a real workflow. A connector existing is not a reason to connect it.

Check: `hermes plugins list`, `hermes mcp list`.

### 8. Projects and knowledge

Job: serious recurring work has a permanent place to live.

Now: one workspace with tight project context for rules Hermes should have every time it works there.

Keep instructions close to the work. Keep references available as files Hermes reads when needed. Do not paste the knowledge base into the always-loaded context.

Wait: named Projects, multi-folder workspaces, Desktop grouping, or worktree conventions (`hermes project`). Signal: more than one durable workspace, or a coordination need those features actually solve.

### 9. Automation

Job: repeat a workflow that already succeeds from a fresh session.

Before scheduling, run it manually until instructions, project context, skill, inputs, output location, and failure conditions make sense with no leftover chat context.

Then pick the lightest path:

- Agent cron, if the job needs research, judgment, writing, or tool use.
- `hermes cron create --script ... --no-agent`, if the job is mechanical and should not wake a model.
- `hermes webhook`, if an outside system can already say that something changed. Do not poll forever.

Rule: automate the stable part, not the confusion. One remembered job beats twenty forgotten ones.

Check: `hermes cron list`, `hermes cron doctor`, and recent `hermes cron runs` for failures.

### 10. Multi-agent workflows

Job: independent context or parallel reasoning, not a headcount.

Useful patterns, not special modes: supervisor and worker, researcher and writer, maker and reviewer, parallel specialists on independent parts.

Do not spawn an agent for mechanical work. Use code or a no-agent script. If an action is public, expensive, destructive, sensitive, or preference-dependent, keep a human approval point. Do not bury that decision in a worker chain.

Wait until the independent work is worth the coordination cost.

### 11. Cost and token optimization

Job: stop spending intelligence where a cheaper path already works.

Measure first (`hermes prompt-size`, `/usage`, `/compress`). Then look for architectural waste:

- Main model doing side jobs a faster auxiliary model should do.
- Unused toolsets adding schema weight.
- Far more skills than you use, or auto-loaded skills that should stay task-specific.
- An LLM doing a deterministic check that should be `--no-agent`.
- One giant conversation carrying work that belongs in a project, a skill, a fresh session, or delegation.

The cheapest token is the one the workflow never needed. Do not delete a layer because the setup "feels expensive" before these views exist.

### 12. Reliability and recovery

Job: get the system back. Do not leave this until later.

These are different jobs. Do not substitute one for another.

- Provider fallbacks: primary inference route fails under qualifying conditions (`hermes fallback list`).
- Sessions and logs: what actually happened (`hermes sessions`, `hermes logs`).
- `hermes doctor`: setup and dependency problems.
- `hermes backup` / `hermes import`: full Hermes-home backup, including credentials. Treat the zip as secret. Keep at least one current copy off the machine or disk you are protecting.
- `hermes profile export` / `hermes profile import`: one profile. Export force-redacts secret-shaped text. It is not a full machine backup.
- Checkpoints and `/rollback`: supported project or file changes. Not a Hermes-home backup.

Now, once the first real setup exists: one full backup stored off-box. Do not create or upload a backup unless the user asked. Never print archive contents, `.env`, or `auth.json`.

### 13. Security and maintenance

Job: know what has access to what, and keep third-party pieces maintained.

Now: secrets in supported stores, intentional approvals, a known execution backend, and a reason you can state for every third-party skill, plugin, and MCP server.

Maintenance surfaces: `hermes update`, `hermes config check`, `hermes config migrate`, `hermes security audit`, `hermes skills audit`, `hermes plugins list`, `hermes mcp list`.

`hermes security audit` is a bounded supply-chain scan of supported dependency surfaces (venv, plugin Python deps, pinned npx/uvx MCP servers). It is not a certificate that the setup is secure.

If you cannot remember why a plugin is installed, which MCP server owns a tool, where a credential came from, or what a recurring job does, that item is already a maintenance finding.

### 14. Self-audit

Job: let real usage show what to change. Do not add another feature to finish.

Read-only pass over: profile and config, primary and auxiliary routing, fallbacks, prompt-size baseline, memory vs project-context placement, installed and auto-loaded skills, enabled toolsets, plugins and MCP servers, cron jobs and failures, logs and doctor findings, backup posture, approval settings, and update or security status.

Report what looks unused, duplicated, stale, unnecessarily expensive, missing, or in the wrong layer. Rank by practical impact. Wait.

## Build order

Do not configure the full map at once.

**First hour.** One install, one primary profile, one workspace, one primary model, basic SOUL / memory / project-context decisions, only the tools the first job needs, and a `hermes doctor` run with material issues resolved. Then stop configuring and use Hermes.

**After a week of real work.** Cleaner project context, a custom or external skill for a repeated procedure, one useful integration, the first stable automation, prompt and usage measurement, and a real full backup off-box.

**When earned, not before.** Auxiliary routing, fallback chains, external memory, skill auto-load, more plugins and MCP, specialist profiles, deeper multi-agent coordination, isolated or remote execution, event-driven automation, stronger checkpoint routines.

**Ongoing.** Updates, backups, audits, cleanup, and changes justified by actual use.

## Output format

Return this, and nothing that contains a secret:

1. Profile, workspace, primary model, doctor result (one line each).
2. Fourteen lines: `N. Name: now|wait|earned - reason`. Waiting lines include the earning signal.
3. Ranked changes, each marked fact or recommendation, with the command that would verify it.
4. Explicit stop: no edits until the user approves a named item.

## Common pitfalls

1. Multiplying profiles early. A second profile is a durable-state decision, not a way to feel organized.
2. Treating a profile as a sandbox. Terminal and file access still run as the OS user unless an isolated backend was chosen on purpose.
3. Calling every note "memory". Project rules, skills, and session history are different jobs.
4. Installing external memory, MCP, or plugins because the connector exists.
5. Auto-loading skills "so Hermes always knows". That spends the progressive-disclosure budget on every turn.
6. Scheduling a workflow you have only run inside a long chat. Cron will not see that chat.
7. Using a profile export, a checkpoint, or a same-disk zip as a disaster-recovery plan.
8. Optimizing cost before `hermes prompt-size` and `/usage` exist.
9. Letting the audit rewrite the system. Rank, then wait.
10. Hand-editing `config.yaml`, or printing `.env`, `auth.json`, or backup contents into the report.

## Verification checklist

- [ ] All 14 layers classified `now`, `wait`, or `earned`, with a signal on every `wait`.
- [ ] No later layer marked `now` to cover a missing earlier layer.
- [ ] Live inspection used the commands above, or a missing command was reported instead of invented.
- [ ] The report contains no secret values, archive contents, or raw `.env` / `auth.json` text.
- [ ] Recommended changes are ranked and unapplied until the user names one.
- [ ] Any approved change was made with `hermes config set` (or the matching `hermes` subcommand), then re-checked.
