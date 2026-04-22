# xargs -P: signal propagation to parallel workers

When running jobs in parallel with `xargs -P N`, signals sent to the xargs process don't automatically reach child processes the way you'd expect.

```sh
# Spawns 4 parallel workers
echo {1..8} | tr ' ' '\n' | xargs -P4 -I{} sh -c 'sleep 10; echo done {}'
```

Sending `SIGINT` (Ctrl+C) to the xargs process will kill xargs, but the children may keep running — especially if they're in a different process group.

## What actually happens

- `xargs` itself exits on `SIGTERM`/`SIGINT`
- Children spawned with `-P` are in the **same process group** on Linux, so a terminal Ctrl+C (which sends SIGINT to the foreground process group) does reach them
- But if you `kill <xargs_pid>` from another terminal, children survive

## Reliable cleanup

Wrap in a shell that sets a trap:

```sh
cleanup() { kill 0; }
trap cleanup EXIT

echo {1..8} | tr ' ' '\n' | xargs -P4 -I{} ./worker.sh {}
```

`kill 0` sends SIGTERM to the entire process group, which takes down all children.

## With GNU parallel

`parallel` handles this better — it has `--halt` and built-in signal forwarding. Worth it if you're doing complex fan-out jobs.
