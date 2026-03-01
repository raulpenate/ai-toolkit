# Sections

This file defines all sections, their ordering, impact levels, and descriptions.
The section ID (in parentheses) is the filename prefix used to group rules.

---

## 1. Components (component)

**Impact:** HIGH
**Description:** How you structure and compose components determines how well your UI scales. Components should be focused, composable, and isolate failures from spreading.

## 2. Data (data)

**Impact:** HIGH
**Description:** Data fetching, loading states, and form validation shape how your UI handles real-world conditions. Always handle loading, error, and empty states explicitly.

## 3. State (state)

**Impact:** HIGH
**Description:** State placement determines complexity. Start local, lift only when shared, and keep server cache separate from UI state.

## 4. Organization (org)

**Impact:** MEDIUM
**Description:** File and module organization affects build performance and refactorability. Avoid patterns that break tree-shaking or create hidden coupling.

## 5. Performance (perf)

**Impact:** MEDIUM
**Description:** Measure before optimizing. Route-level code splitting and targeted memoization are the highest-leverage frontend performance techniques.
