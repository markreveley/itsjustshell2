# INV-1 and `check_exchanges.exs` say "nine keys"; the schema has eight

#agent-authored
Provenance: no exchange — found by a Claude Code session reading this repo on 2026-11-01, recorded in agent-files `docs/related-repos.md` ([link](https://github.com/markreveley/agent-files/blob/main/docs/related-repos.md)).

## What is wrong

- `docs/invariants.md` INV-1: "all nine keys present".
- `tools/check_exchanges.exs` line 2 (comment): "exactly the nine …".
- `docs/exchange-schema.md` line 11: "Frontmatter — eight keys, this order".
- `tools/check_exchanges.exs` line 11: `@keys ~w(session_id date model cost_usd cwd role tags deps)` — eight.

The check enforces eight, so the behaviour is right. The register and the comment describe a different rule from the one that is armed.

## Suggested fix

- Correct the comment in `check_exchanges.exs`.
- INV-1 sits in the register, where silent edits are forbidden. Correct it by supersession (INV-1 → INV-1′, dated, both kept), with "eight keys" and an Origin line pointing at this issue.
