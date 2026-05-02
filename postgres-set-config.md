# Postgres: `SET LOCAL` doesn't accept bind parameters — use `set_config()`

I tried to set a session var per-transaction for an RLS policy:

```ts
await tx.execute(sql`SET LOCAL app.tenant_id = ${tenantId}`);
```

Postgres responds with `syntax error at or near "$1"`. SET commands aren't parameterizable — they expect literal values, and the postgres-js client substitutes `${}` as a bind param.

The fix is `set_config(name, value, is_local)` — a regular function call that accepts parameters cleanly:

```ts
await tx.execute(sql`SELECT set_config('app.tenant_id', ${tenantId}, true)`);
```

The third arg is `is_local` — `true` makes it transaction-scoped (same effect as `SET LOCAL`), `false` is session-scoped (`SET`). Behavior is identical to `SET LOCAL`/`SET` in every other way.

Comes up most often when:
- Setting tenant context for RLS policies (the policy reads `current_setting('app.tenant_id', true)`)
- Toggling `statement_timeout` per-transaction with a value from app config
- Anything else where the value is computed at runtime

The `, true` (missing_ok) on `current_setting()` is also worth knowing — it returns `NULL` instead of erroring when the var was never set, which is what you want for "fail closed" RLS:

```sql
CREATE POLICY tenant_isolation ON memberships
  USING (tenant_id = NULLIF(current_setting('app.tenant_id', true), '')::uuid);
```

Without `, true` you get `unrecognized configuration parameter` if the app forgets to set it.
