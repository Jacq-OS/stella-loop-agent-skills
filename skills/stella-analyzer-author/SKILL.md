---
name: stella-analyzer-author
description: Author, validate, publish, and install Stella Loop analyzers through repository config, the in-app builder, or the registry.
min_stella_version: "0.2.9"
---

# Stella Analyzer Author

## Bootstrap

Set `STELLA_API_URL`, `STELLA_API_KEY`, and `STELLA_PROJECT`, or store a key.
Resolve the project from flag, environment, `.stella/project.json`, then user
configuration before selecting an authoring mode.

```sh
printf %s "$STELLA_API_KEY" | stella auth login --with-key
stella auth status --json
```

## Choose one of three authoring modes

1. Repository config is the default: add
   `.stella/analyzers/<slug>.json` or `.stella/analyzers/<slug>.yaml`. Bump the
   analyzer version for every behavioral change, validate before commit, and
   review the diff like code.
2. The in-app builder is the human route for guided composition and preview.
3. The registry is the reuse route: browse, install, or publish a version.

```sh
stella analyzer validate --file .stella/analyzers/<slug>.yaml --json
stella analyzer create --file .stella/analyzers/<slug>.yaml --json
stella analyzer publish <analyzer-id> --file .stella/analyzers/<slug>.yaml --changelog "Explain the behavior change" --registry --json
stella analyzer registry --json
stella analyzer install <registry-id> --semver <version> --json
```

Validation must pass before commit. Preserve the fixed vocabulary for scores,
reports, areas, provenance, and versions. Never hide an incompatible schema by
silently dropping a field.

## Recovery

| Exit | Meaning                         | Recovery                                                                    |
| ---- | ------------------------------- | --------------------------------------------------------------------------- |
| 3    | Authentication failed           | Refresh the key or run `stella auth login --with-key`, then validate again. |
| 6    | Version or publication conflict | Fetch current analyzer state, increment from that version, and revalidate.  |
| 7    | Rate limited                    | Honor `retryAfterMs`; keep the local manifest unchanged while waiting.      |

Use `stella --help` for discovery and `stella api` only as the documented CLI
escape hatch. Never issue raw HTTP from this skill.
