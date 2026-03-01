---
title: Test Error Paths, Not Just the Happy Path
impact: HIGH
tags: test-design, error-handling, coverage
---

## Test Error Paths, Not Just the Happy Path

Every feature must have tests for its failure modes. At minimum: one validation error, one authorization error, and one not-found case. Happy-path-only tests give false confidence and miss the most common production failures.

**Incorrect (happy path only):**

```typescript
// Bad - only tests the success case
describe('inviteUser', () => {
  it('creates an invitation', async () => {
    const admin = createUser({ role: 'ADMIN' });
    const result = await inviteUser(admin, 'new@example.com');
    expect(result.status).toBe('PENDING');
  });
});
```

**Correct (happy path + error paths):**

```typescript
describe('inviteUser', () => {
  // Happy path
  it('creates invitation when called by admin', async () => {
    const admin = createUser({ role: 'ADMIN' });
    const result = await inviteUser(admin, 'new@example.com');
    expect(result.status).toBe('PENDING');
  });

  // Authorization error
  it('throws when caller is not an admin', async () => {
    const member = createUser({ role: 'MEMBER' });
    await expect(inviteUser(member, 'new@example.com')).rejects.toThrow('FORBIDDEN');
  });

  // Validation error
  it('throws when email is invalid', async () => {
    const admin = createUser({ role: 'ADMIN' });
    await expect(inviteUser(admin, 'not-an-email')).rejects.toThrow('VALIDATION_ERROR');
  });

  // Not-found / conflict
  it('throws when user is already a member', async () => {
    const admin = createUser({ role: 'ADMIN' });
    const existing = createUser({ email: 'existing@example.com' });
    await seedUser(existing);
    await expect(inviteUser(admin, existing.email)).rejects.toThrow('ALREADY_MEMBER');
  });
});
```

**Minimum error coverage checklist per feature:**
- [ ] At least one input validation failure
- [ ] At least one unauthorized / forbidden case
- [ ] At least one not-found or conflict case

**Why it matters:**
- Production failures overwhelmingly occur in error paths, not the happy path
- Auth and validation bugs are security issues, not just UX issues
- Error-path tests document the contract for callers — what they must handle
- LLMs generating code often skip error paths entirely without explicit tests enforcing them
