---
title: Assert on Behavior, Not Implementation
impact: HIGH
tags: test-design, assertions, mocking
---

## Assert on Behavior, Not Implementation

Test what a function produces (return value, state change, side effect), not how it produces it internally. Assertions on internal calls couple tests to implementation details and break on safe refactors.

**Incorrect (asserting on internal calls):**

```typescript
// Bad - test verifies which internal method was called
test('sends invitation', async () => {
  const emailSpy = vi.spyOn(emailService, 'send');

  await inviteUser({ email: 'new@example.com', orgId: 'org-1' });

  // This passes even if the invitation is never saved to the DB
  expect(emailSpy).toHaveBeenCalledWith(
    expect.objectContaining({ to: 'new@example.com' })
  );
});
```

**Correct (asserting on observable outcome):**

```typescript
// Good - test verifies the real outcome: invitation exists in DB and email was sent
test('sends invitation and persists it', async () => {
  server.use(
    http.post('https://api.resend.com/emails', () =>
      HttpResponse.json({ id: 'email_123' })
    )
  );

  const result = await inviteUser({ email: 'new@example.com', orgId: org.id });

  // Assert on return value
  expect(result.status).toBe('PENDING');

  // Assert on persisted state
  const stored = await db.query.invitations.findFirst({
    where: eq(invitations.id, result.id),
  });
  expect(stored).toBeDefined();
});
```

**Why it matters:**
- Tests that assert on internal calls break every time the implementation is refactored, even when behavior is correct
- Observable-behavior tests survive implementation changes — they only break when behavior changes
- Mocking internals hides integration bugs that real assertions would catch
- Behavior-focused tests serve as living documentation of what the code actually does
