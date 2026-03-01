---
title: Make Async Tests Deterministic
impact: HIGH
tags: async, reliability, timing, flaky-tests
---

## Make Async Tests Deterministic

Always await async operations. Never use real `setTimeout` or `sleep` to wait for side effects. Use fake timers, resolved promises, or explicit awaits to make tests deterministic. Timing-based tests are the most common source of flaky CI failures.

**Incorrect (real timers and fire-and-forget):**

```typescript
// Bad - non-deterministic timing
it('sends email after a delay', async () => {
  triggerEmailAfterDelay(user, 500); // fire-and-forget

  await new Promise((r) => setTimeout(r, 600)); // real sleep - flaky in slow CI

  expect(emailSpy).toHaveBeenCalled(); // may or may not have run
});

// Bad - unawaited promise
it('saves the record', async () => {
  saveRecord(data); // missing await — test ends before save completes
  const record = await db.query.records.findFirst(...);
  expect(record).toBeDefined(); // intermittently undefined
});
```

**Correct (awaited operations and fake timers):**

```typescript
// Good - fake timers: advance time without real waiting
it('sends email after a delay', async () => {
  vi.useFakeTimers();

  triggerEmailAfterDelay(user, 500);
  await vi.runAllTimersAsync(); // advances fake clock, runs callbacks

  expect(emailSpy).toHaveBeenCalled();

  vi.useRealTimers();
});

// Good - always await async calls
it('saves the record', async () => {
  await saveRecord(data); // explicit await
  const record = await db.query.records.findFirst(...);
  expect(record).toBeDefined();
});

// Good - await the side effect explicitly when possible
it('publishes an event after creation', async () => {
  const { eventId } = await createOrder(payload);
  const event = await waitForEvent(eventBus, eventId); // explicit await with timeout
  expect(event.type).toBe('ORDER_CREATED');
});
```

**Checklist for async tests:**
- [ ] Every async call is awaited
- [ ] No real `setTimeout` / `sleep` / `delay` in test body
- [ ] Side effects are verified via awaited queries or explicit event listeners, not timing
- [ ] Fake timers are reset after the test (use `afterEach`)

**Why it matters:**
- Unawaited promises let tests complete before the code under test finishes — assertions run against wrong state
- Real sleeps slow the suite and fail under CPU contention in CI
- Flaky tests erode trust in the test suite — teams start ignoring failures
- Deterministic tests run in parallel without interference
