# Stella Loop agent skills

This directory is the source of truth for Stella Loop's agent teaching pack.
The skills teach the `stella` CLI only; the CLI capability ledger remains the
behavioral source of truth.

The same immutable release is available through three doors:

1. Run `stella init --yes` in a repository for signature-verified, hash-pinned
   installation.
2. Pin the public `Jacq-OS/stella-loop-agent-skills` repository and verify the
   detached release manifest.
3. Install the `stella-loop` Claude Code plugin from the Stella marketplace.

Published release manifests and signatures are GitHub release assets. They
bind a tag and the projected content commit without creating a self-referential
commit hash. The release workflow checks that the tag, pack version, plugin
version, content tree, and signed coordinates agree before publication.
