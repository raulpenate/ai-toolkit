---
title: Isolate Test Data — No Shared Mutable State
impact: MEDIUM
tags: test-data, isolation, state-management, reliability
---

## Isolate Test Data — No Shared Mutable State

Each test must create and own its data. Never rely on data created by a previous test, global fixtures seeded once, or module-level mutable variables. Use per-test setup and teardown (truncate or rollback) to guarantee a clean slate.

**Incorrect (shared global state and cross-test dependencies):**

```typescript
// Bad - shared state at module level
let testUser: User;

beforeAll(async () => {
  // Created once — mutated or deleted by any test
  testUser = await db.insert(users).values({ email: 'shared@example.com' }).returning()[0];
});

it('updates user name', async () => {
  await updateUser(testUser.id, { name: 'Alice' });
  // testUser.name is now 'Alice' — next test may see wrong state
});

it('deletes user', async () => {
  await deleteUser(testUser.id);
  // testUser no longer exists — every subsequent test breaks
});
```

**Correct (per-test data with cleanup):**

```typescript
// Good - each test creates its own data; cleanup happens after every test
afterEach(async () => {
  await db.delete(users); // or use transactions with rollback
});

it('updates user name', async () => {
  const user = await db.insert(users)
    .values(createUser({ name: 'Original' }))
    .returning()
    .then((r) => r[0]);

  await updateUser(user.id, { name: 'Alice' });

  const updated = await db.query.users.findFirst({ where: eq(users.id, user.id) });
  expect(updated?.name).toBe('Alice');
});

it('deletes user', async () => {
  const user = await db.insert(users)
    .values(createUser())
    .returning()
    .then((r) => r[0]);

  await deleteUser(user.id);

  const deleted = await db.query.users.findFirst({ where: eq(users.id, user.id) });
  expect(deleted).toBeUndefined();
});
```

**Isolation strategies:**

| Strategy | When to use |
|----------|-------------|
| `afterEach` truncate | Simple, works for most cases |
| Transaction rollback | Faster for large schemas — wrap each test in a transaction, rollback after |
| In-memory store reset | For non-DB state (e.g., in-memory queues, caches) |

**Why it matters:**
- Tests that share state have implicit ordering dependencies — they can only run in a specific sequence
- A single mutating test contaminates every test that follows it
- Flaky ordering-dependent tests are the hardest to debug — failures appear random
- Isolated tests can run in parallel, making the suite faster and more reliable
