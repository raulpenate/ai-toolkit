---
title: Colocate Validation Rules With the Form Schema
impact: MEDIUM
tags: data, forms, validation, ux
---

## Colocate Validation Rules With the Form Schema

Define validation rules alongside the form schema, validate on submit (not on every keystroke), and return structured field-level errors. Scattered validation logic creates inconsistent UX and makes rules hard to update.

**Incorrect (validation scattered across handlers and UI):**

```typescript
// Bad - validation split between handler, component state, and inline checks
function RegistrationForm() {
  const [email, setEmail] = useState('');
  const [emailError, setEmailError] = useState('');
  const [password, setPassword] = useState('');
  const [passwordError, setPasswordError] = useState('');

  // Keystroke validation — expensive and poor UX
  function handleEmailChange(e: React.ChangeEvent<HTMLInputElement>) {
    setEmail(e.target.value);
    if (!e.target.value.includes('@')) setEmailError('Invalid email');
    else setEmailError('');
  }

  async function handleSubmit() {
    // Duplicate validation at submit — not the source of truth
    if (!email || !password) return;
    await register({ email, password });
  }
}
```

**Correct (schema owns the rules, errors are structured):**

```typescript
// Good - schema defines all rules in one place
const registrationSchema = {
  email: {
    required: 'Email is required',
    pattern: { value: /\S+@\S+\.\S+/, message: 'Enter a valid email' },
  },
  password: {
    required: 'Password is required',
    minLength: { value: 8, message: 'Password must be at least 8 characters' },
  },
};

// Validate on submit, return structured field errors
async function handleSubmit(values: FormValues) {
  const errors = validate(values, registrationSchema);
  if (Object.keys(errors).length > 0) {
    setErrors(errors); // { email: 'Enter a valid email', password: '...' }
    return;
  }
  await register(values);
}

// UI reads from structured error map — no logic here
function FieldError({ name, errors }: { name: string; errors: Record<string, string> }) {
  return errors[name] ? <span role="alert">{errors[name]}</span> : null;
}
```

**Rules for validation placement:**

- Schema definition: colocate with the form component or in a sibling `schema.ts`
- Trigger: on submit (first attempt), then on blur after first submission
- Error shape: flat `{ fieldName: errorMessage }` — one error per field at a time
- Server errors: merge into the same error map so UI handles them uniformly

**Why it matters:**
- Keystroke validation causes input lag and annoys users before they've finished typing
- Scattered rules mean changing a constraint requires edits in multiple places
- Structured errors decouple validation logic from rendering, making both testable in isolation
