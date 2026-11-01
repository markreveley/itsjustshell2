# `derive_threads.exs` reads `deps:` from record bodies, so no thread is ever derived

#agent-authored
Provenance: no exchange — found by a Claude Code session reading this repo on 2026-11-01, recorded in agent-files `docs/related-repos.md` ([link](https://github.com/markreveley/agent-files/blob/main/docs/related-repos.md)).

## What is wrong

`first_dep/1` in `tools/derive_threads.exs` (line 56) scans **every line of the file**, not just the frontmatter, and uses `Enum.find_value`. When the frontmatter `deps:` is blank, the callback returns `nil` for that line, so `find_value` keeps going into the body. It returns the first later line that starts with `deps:`.

`exchanges/2026-08-15-bootstrap-thread.md` has a blank `deps:` on line 9, and its body has `deps: input/exchanges/exchanges/2026-08-14-capture-selftest-validator.md` on line 670 (quoted transcript text). The bootstrap record therefore gets a parent that is not an exchange. It is the head of every chain (each of the other dependent records traces back to it), so no record is both parentless and has children, and `roots == []`.

## How to see it

On a copy of `exchanges/` and `tools/` (the deriver deletes and rewrites `threads/`):

```sh
elixir tools/derive_threads.exs
# derive_threads: no deps chains yet; nothing to derive
```

7 of the 9 records have a non-empty `deps:`, and all 7 chains lead back to the bootstrap record, so one thread rooted there (8 records) is expected. `threads/` does not exist on `main` (checked at `9ff8321`).

## Suggested fix

Read `deps:` only from the frontmatter: take the lines between the opening `---` at byte zero and the next `---`, then look for `deps:` there. A blank value then yields `nil` for that record, and the scan stops at the fence. `check_views.exs` regenerates and diffs, so after the fix the derivation job's first run on `main` will commit the missing `threads/` view.

`check_deps.exs` and `ask.exs` may parse `deps:` the same way; check them in the same cycle.
