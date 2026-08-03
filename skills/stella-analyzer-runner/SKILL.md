---
name: stella-analyzer-runner
description: Execute one Stella Loop analyzer run honestly from your own session, attended or pulled, and submit its report against the run.
min_stella_version: "0.2.6"
---

# Stella Analyzer Runner

You are the executor of exactly one analyzer run. The manifest is the
contract: its tool grants bound what you may touch, its scoring contract
bounds what you may claim, and the run's timeout budget bounds how long you
have. Everything you produce lands through run-bound report submission.

## Bootstrap

```sh
stella whoami --json
```

Confirm the actor and project are the ones you intend to work as. If you are
resuming, check whether you already hold a bound run before starting another.

## Obtain work

One-off, right now (attended mode — no enrollment, binds the run to you):

```sh
stella analyzer run <slug> --attended --json
```

- Browser-granting analyzers browse a target: pass `--target <name>` for a
  stored project environment, or `--local-url <origin>` for a local dev
  server you start yourself.
- If the start reports an unverified capability, confirm only when your
  session truly has it: repeat with `--confirm-capabilities`.
- Attended mode binds exactly one run. Enrolling recurring capacity is a
  different surface: `stella runner up --attended`.

Enrolled pull session (your runner record carries attested capabilities):

```sh
stella work next --kind analyzer-run --json
```

An empty result distinguishes nothing-ready from unsuitable-for-your-class
and names any missing capability; do not retry past it.

## Fetch the execution context

```sh
stella analyzer context <run-id> --json
```

The bundle carries the manifest execution block (prompt, rubric with
calibration anchors, tool grants, timeout), resolved configuration, North
Star documents with weights, workspace repository references, intent context,
`hasComparablePredecessor`, and the resolved target environment without
credential values. Request credential values only when the walkthrough needs
them and you are the bound executor:

```sh
stella analyzer context <run-id> --with-credentials --json
```

Treat returned values as secrets: use them to sign in, never echo them into
your report, findings, logs, or shell history.

## Execute honestly, per manifest kind

- Agentic manifests: follow the prompt. Honor the tool grants exactly — use
  a browser only when the manifest grants `browser`, and browse only the
  resolved target environment or your declared local URL. Read the North Star
  documents the bundle provides rather than guessing at aims.
- Deterministic manifests: run `execution.command` in the workspace and parse
  its output per the declared parser. Do not editorialize its results.
- The run's timeout budget is your liveness bound; there is no heartbeat to
  send. On long executions, check for cooperative cancellation and stop
  cleanly when it is requested:

```sh
stella analyzer runs --state running --json
```

A run showing `cancelRequested: true` never produces a report; submission
against it is refused.

## Self-validate the draft before submitting

- `scoring.scored: false` — narrative and findings only. Submit no numbers of
  any kind, in any field.
- Scored — score strictly per the rubric body, calibrated to its anchors.
  Scores are integers 0 to 100 for exactly the declared areas.
- `hasComparablePredecessor: true` — include a `scoreExplanation` accounting
  for movement (or its absence) relative to the predecessor.
- Scored agentic drafts declare the concrete model that produced them:
  submit with `executedModelId`. If it differs from the version's pinned
  model, your scores are recorded honestly but marked non-comparable
  (model-mismatch) — the start response warned you if this was coming.

Honesty rules, always: no fabricated findings; every code claim carries a
`file:line` reference into a linked repository; findings you did not verify
do not go in the report.

## Submit against the run

```sh
stella report submit --run <run-id> --file draft.json --json
```

Rejections return field paths (for example `areas[0].findings[2].severity`
or `executedModelId`). Fix exactly the named fields and resubmit — the run
stays open until its timeout. Success settles the run; your work is done.
