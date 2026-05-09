# til

today i learned. tiny notes on things i didn't know yesterday.

loose, mostly one paragraph each. no particular order.

## entries

- [python: dict preserves insertion order since 3.7](python/dict-ordering.md)
- [git: `git reflog` saves you from almost anything](git/reflog.md)
- [unix: `xargs -P` does parallel in-place](unix/xargs-parallel.md)
- [unix: `xargs -P` and signal propagation to workers](xargs-signal.md)
- [zig: `@typeInfo` and friends - runtime-typed values without runtime overhead](zig-comptime-types.md)
- [postgres: `SET LOCAL` doesn't take bind params; use `set_config()`](postgres-set-config.md)
- [rust: `rustls`'s `prefer-post-quantum` feature is a no-op against the `ring` provider](rust/rustls-prefer-post-quantum-provider.md)
- [bun: `bun install` only runs a subset of npm lifecycle scripts](bun/bun-install-lifecycle-scripts.md)

## closed proposals

- [#9](https://github.com/wholovesalife/til/issues/9) - numeric prefix on filenames. nope, slug + git log are enough.

## removed entries

drafts that were referenced in past readme revisions but never landed as
files: `git/autosquash`, `git/worktree`, `jq/null-input`, `bash/proc-sub`,
`unix/kill-zero`, `bash/set-euo`, `lua/metamethods`. some are notes i still
want to write, some are duplicates of existing entries.
