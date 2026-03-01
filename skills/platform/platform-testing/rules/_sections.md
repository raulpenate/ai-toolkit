# Sections

This file defines all sections, their ordering, impact levels, and descriptions.
The section ID (in parentheses) is the filename prefix used to group rules.

---

## 1. Test Design (test)

**Impact:** HIGH
**Description:** How you structure, name, and focus tests determines whether they catch real bugs and survive refactors. Well-designed tests are readable, targeted, and cover both happy and error paths.

## 2. Mocking (mock)

**Impact:** HIGH
**Description:** Mock only at system boundaries — external APIs, time, randomness. Mocking internal code gives false confidence and makes refactoring painful.

## 3. Test Data (data)

**Impact:** MEDIUM
**Description:** Each test must own its data. Shared or hardcoded test state causes flaky tests, ordering dependencies, and cross-test contamination.

## 4. Reliability (rel)

**Impact:** HIGH
**Description:** Tests must be deterministic. Non-determinism from async code, timing, or shared state is the primary source of flaky tests in CI.
