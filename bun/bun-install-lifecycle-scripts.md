# `bun install` only runs a subset of npm lifecycle scripts

I had a `package.json` that worked fine under npm but silently broke after switching to bun:

```json
{
  "scripts": {
    "dependencies": "node scripts/sync-native-deps.mjs"
  }
}
```

`bun add foo` would not invoke `dependencies`. No warning. The native-side sync just stopped happening. I spent a while convincing myself the script wasn't broken before realising bun never called it.

## What bun actually runs

For `bun install` / `bun add` / `bun remove`, the runtime lifecycle hooks bun fires are exactly:

```
preinstall
install
postinstall
preprepare
prepare
postprepare
```

That's it. Five other npm hooks (`dependencies`, `preuninstall`, `postuninstall`, `prerestart`, `prestop`) get parsed, stored, and never executed.

The lifecycle subset bun honors lives in `src/install/lockfile.zig` if you want to verify against current main rather than trust me.

## Why npm has `dependencies` in the first place

npm added the `dependencies` script in v7 specifically for the post-`add`/-`remove` case: rerun after `node_modules` has been mutated. Capacitor projects use it to mirror added packages into native iOS/Android dependency files. Same for monorepos that regenerate workspace metadata after each install.

## Workaround

Two options that actually work under bun:

1. **`postinstall`** — runs after `bun install`. Catches `bun add foo` because that command does an install at the end. It also runs on `bun install --no-save`, which `dependencies` would not, so you may be doing extra work.

2. **A wrapper script** — alias `bun add` to `bun add "$@" && node scripts/sync-native-deps.mjs`. Explicit, no surprise, no dependency on bun deciding to honor more hooks later.

`postinstall` is the lower-friction choice if you don't mind it firing on plain `bun install` runs as well.

## Takeaway

bun's npm-compat surface is shaped like npm's but trimmed silently. If a script you depend on lives in the trimmed set, the failure mode is "the script just didn't run", which is the worst kind of failure mode for build-step lifecycle scripts. Read `src/install/lockfile.zig` once and write down which hooks you actually have.
