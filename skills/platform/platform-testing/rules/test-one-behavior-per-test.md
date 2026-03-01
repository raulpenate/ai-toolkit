---
title: One Behavior Per Test
impact: MEDIUM
tags: test-design, assertions, structure
---

## One Behavior Per Test

Each test should verify one logical behavior. Multiple assertions are fine as long as they all verify the same single behavior. Testing multiple behaviors in one test obscures failures and makes debugging harder.

**Incorrect (multiple unrelated behaviors in one test):**

```typescript
// Bad - one test verifies creation, email sending, and role assignment
it('handles user registration', async () => {
  const result = await registerUser({ email: 'user@example.com', role: 'MEMBER' });

  // Behavior 1: user is created
  expect(result.id).toBeDefined();
  expect(result.email).toBe('user@example.com');

  // Behavior 2: welcome email is sent
  expect(emailSpy).toHaveBeenCalledOnce();

  // Behavior 3: default role is assigned
  expect(result.role).toBe('MEMBER');

  // Behavior 4: audit log is written
  const log = await db.query.auditLog.findFirst({ where: eq(auditLog.userId, result.id) });
  expect(log).toBeDefined();
});
```

**Correct (one behavior per test, grouped assertions are fine):**

```typescript
// Good - each test has one clear focus
it('creates user with provided email and role', async () => {
  const result = await registerUser({ email: 'user@example.com', role: 'MEMBER' });

  // Multiple assertions — all verify the same behavior: user was created correctly
  expect(result.id).toBeDefined();
  expect(result.email).toBe('user@example.com');
  expect(result.role).toBe('MEMBER');
});

it('sends welcome email after registration', async () => {
  await registerUser({ email: 'user@example.com', role: 'MEMBER' });
  expect(emailSpy).toHaveBeenCalledOnce();
});

it('writes audit log on registration', async () => {
  const result = await registerUser({ email: 'user@example.com', role: 'MEMBER' });
  const log = await db.query.auditLog.findFirst({ where: eq(auditLog.userId, result.id) });
  expect(log).toBeDefined();
});
```

**Why it matters:**
- When a multi-behavior test fails, you don't know which behavior broke without reading the full failure
- Splitting by behavior produces clearer failure messages and faster diagnosis
- Single-behavior tests are easier to name accurately, which doubles as documentation
- A failed test that covers one behavior is immediately actionable; a failed test covering four is not
