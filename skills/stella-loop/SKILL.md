---
name: stella-loop
description: Read a Stella Loop situational brief and route the next action through the CLI. Use when deciding what needs attention in a Stella project.
min_stella_version: "0.2.5"
---

# Stella Loop

Use this skill to orient a human or agent, then take the smallest honest next
step. Stella Loop is loop-based project management: intents drive analysis,
analysis yields proposals, promoted proposals become epics, and completed work
seeds the next intent.

## Bootstrap

Set `STELLA_API_URL`, `STELLA_API_KEY`, and `STELLA_PROJECT`, or store a key
non-interactively. Project selection resolves from `--project`, then
`STELLA_PROJECT`, then `.stella/project.json`, then user configuration.

```sh
printf %s "$STELLA_API_KEY" | stella auth login --with-key
stella whoami --json
```

Stop if authentication fails. Never paste a key into a project manifest,
prompt, issue, or log.

## Situational brief

Read these as one snapshot. Keep the JSON intact when another agent will parse
it. If the actor has a class, use its id for the ready-work filter.

```sh
stella whoami --json
stella loop status --json
stella work list --ready --class <class-id> --json
stella approvals list --pending --json
stella inbox list --unread --json
```

Summarize: loop mode and stage; running work; ready work; approvals; inbox;
budget or routing holds. Do not claim work merely to make the brief look busy.

## Route the next action

| Situation                                 | Route                                                                           |
| ----------------------------------------- | ------------------------------------------------------------------------------- |
| Suitable ready work exists                | Use the `stella-worker` skill, or claim with `stella work next --json`.         |
| A specific ready task is assigned         | `stella task claim <task-id> --json`, then use `stella-worker`.                 |
| An approval id such as `apr_…` is pending | Inspect it, then `stella approve <approval-id> --json` or reject with a reason. |
| Analyzer behavior must be authored        | Use `stella-analyzer-author`.                                                   |
| Nothing is ready but items wait           | Read inbox and routing/approval details; report the named hold.                 |

```sh
stella approvals show <approval-id> --json
stella approve <approval-id> --json
stella reject <approval-id> --reason "Why this cannot proceed" --json
```

## Recovery

| Exit | Meaning                         | Recovery                                                                             |
| ---- | ------------------------------- | ------------------------------------------------------------------------------------ |
| 3    | Authentication failed           | Refresh `STELLA_API_KEY` or run `stella auth login --with-key`, then rerun the read. |
| 6    | A claim or decision lost a race | Refresh the brief and move to the next current item.                                 |
| 7    | Rate limited                    | Honor `retryAfterMs`; do not create a tight retry loop.                              |

Use `stella --help` for discovery. Use `stella api` only as the documented CLI
escape hatch; never teach or issue raw HTTP from this skill.
