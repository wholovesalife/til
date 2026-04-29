# til

today i learned. tiny notes on things i didn't know yesterday.

loose, mostly one paragraph each. no particular order.

## entries

- [python: dict preserves insertion order since 3.7](python/dict-ordering.md)
- [git: `git reflog` saves you from almost anything](git/reflog.md)
- [unix: `xargs -P` does parallel in-place](unix/xargs-parallel.md)
- [git: `--autosquash` reorders fixup commits automatically](git/autosquash.md)
- [git: `git worktree add` lets you check out two branches simultaneously](git/worktree.md)
- [shell: `jq -n` (null input) lets you construct JSON without reading stdin](jq/null-input.md)
- [bash: process substitution `<(cmd)` passes command output as a filename](bash/proc-sub.md)
- [unix: `kill -0 <pid>` checks if a process exists without sending a real signal](unix/kill-zero.md)
- [bash: `set -euo pipefail` catches unset vars, failed pipes, and unexpected exits](bash/set-euo.md)
- [lua: `__index` metamethod enables prototype-style inheritance without copying methods](lua/metamethods.md)

## closed proposals

- [#9](https://github.com/wholovesalife/til/issues/9) — numeric prefix on filenames. nope, slug + git log are enough.
