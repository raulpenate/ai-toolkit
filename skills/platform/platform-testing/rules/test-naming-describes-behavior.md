---
title: Name Tests to Describe Behavior
impact: MEDIUM
tags: test-design, naming, documentation
---

## Name Tests to Describe Behavior

Test names are documentation. Use the pattern `"[action] when [condition] should [outcome]"` or a natural-language equivalent. Avoid generic names like `"works"`, `"test1"`, or duplicating the function name.

**Incorrect (vague or implementation-focused names):**

```typescript
// Bad - names that describe nothing useful
it('works', ...);
it('test1', ...);
it('inviteUser', ...);
it('should work correctly', ...);
it('handles the case', ...);
it('inviteUser error', ...);
```

**Correct (behavior-describing names):**

```typescript
// Good - names that read like specifications
describe('inviteUser', () => {
  it('creates a pending invitation when called by an admin', ...);
  it('throws FORBIDDEN when caller is not an admin', ...);
  it('throws VALIDATION_ERROR when email format is invalid', ...);
  it('throws ALREADY_MEMBER when target is already in the organization', ...);
  it('sends a welcome email after the invitation is created', ...);
});

// Also acceptable: natural-language BDD style
describe('inviteUser', () => {
  it('allows admins to invite new members', ...);
  it('rejects non-admin callers with a forbidden error', ...);
  it('rejects malformed email addresses', ...);
});
```

**Naming formula:**

| Part | Example |
|------|---------|
| Action | `creates`, `returns`, `throws`, `sends`, `rejects` |
| Condition | `when called by admin`, `when email is invalid`, `when user already exists` |
| Outcome | `a pending invitation`, `FORBIDDEN`, `VALIDATION_ERROR` |

**Why it matters:**
- Failing tests with descriptive names tell you what broke without reading the test body
- Good names prevent duplicate tests — if you can't name the behavior, you may be testing the same thing twice
- Test names become the specification: they describe what the system is supposed to do
- CI output with descriptive names is self-explanatory; vague names require opening the source file
