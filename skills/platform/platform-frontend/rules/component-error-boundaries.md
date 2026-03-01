---
title: Use Error Boundaries to Isolate UI Failures
impact: HIGH
tags: component, error-handling, resilience
---

## Use Error Boundaries to Isolate UI Failures

Wrap independent UI subtrees in error boundaries so a single component failure doesn't crash the whole app. Place boundaries at route, section, and widget levels — not per-component.

**Incorrect (no isolation — one failure crashes everything):**

```typescript
// Bad - entire app unmounts if UserCard throws
function Dashboard() {
  return (
    <main>
      <UserCard userId={userId} />
      <ActivityFeed />
      <MetricsPanel />
    </main>
  );
}
```

**Correct (failures are contained to their subtree):**

```typescript
// Good - each section is independently resilient
function Dashboard() {
  return (
    <main>
      <ErrorBoundary fallback={<SectionError section="profile" />}>
        <UserCard userId={userId} />
      </ErrorBoundary>
      <ErrorBoundary fallback={<SectionError section="activity" />}>
        <ActivityFeed />
      </ErrorBoundary>
      <ErrorBoundary fallback={<SectionError section="metrics" />}>
        <MetricsPanel />
      </ErrorBoundary>
    </main>
  );
}

// Generic fallback that logs and displays a recovery UI
function SectionError({ section }: { section: string }) {
  return (
    <div role="alert">
      <p>Failed to load {section}. <button onClick={() => window.location.reload()}>Retry</button></p>
    </div>
  );
}
```

**Placement heuristic:**

- Route level: always — every page/route gets a boundary
- Section level: for independently-loaded widgets (feeds, charts, cards)
- Component level: only for high-risk, externally-driven data (user-generated content)

**Why it matters:**
- A thrown error inside an unguarded tree unmounts the entire React tree
- Error boundaries confine failures so the rest of the UI stays functional
- Users can recover from partial failures without a full page reload
- Error boundaries are the only way to catch render-phase errors in React
