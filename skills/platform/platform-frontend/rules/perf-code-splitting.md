---
title: Split Code at Route Boundaries, Not Component Boundaries
impact: MEDIUM
tags: performance, code-splitting, lazy-loading, bundling
---

## Split Code at Route Boundaries, Not Component Boundaries

Use route-level lazy loading to reduce initial bundle size. Splitting by individual component adds complexity without meaningful size savings — routes are the right granularity.

**Incorrect (no splitting — entire app in one bundle):**

```typescript
// Bad - all routes eagerly imported, entire app loads upfront
import { Dashboard } from './pages/Dashboard';
import { Settings } from './pages/Settings';
import { Reports } from './pages/Reports';
import { AdminPanel } from './pages/AdminPanel';

const routes = [
  { path: '/', component: Dashboard },
  { path: '/settings', component: Settings },
  { path: '/reports', component: Reports },
  { path: '/admin', component: AdminPanel },
];
```

**Correct (lazy per route with loading fallback):**

```typescript
// Good - each route is a separate chunk, loaded on demand
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));
const Reports = lazy(() => import('./pages/Reports'));
const AdminPanel = lazy(() => import('./pages/AdminPanel'));

// Wrap the router outlet in Suspense with a meaningful fallback
function AppRoutes() {
  return (
    <Suspense fallback={<PageSkeleton />}>
      <Routes>
        <Route path="/" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
        <Route path="/reports" element={<Reports />} />
        <Route path="/admin" element={<AdminAdmin />} />
      </Routes>
    </Suspense>
  );
}

// Skeleton preserves layout and avoids layout shift
function PageSkeleton() {
  return <div className="page-skeleton" aria-busy="true" aria-label="Loading page" />;
}
```

**When to split below route level:**

- Heavy third-party libraries used conditionally (chart libraries, rich text editors, PDF renderers)
- Feature flags that hide large feature surfaces
- Never split small UI components — the network round-trip costs more than the savings

**Why it matters:**
- Initial bundle size directly affects Time to Interactive — every byte matters on slow connections
- Route-level splits are the highest-leverage cut: each route is a discrete user destination
- Splitting at component granularity fragments the bundle without reducing what any page needs to load
