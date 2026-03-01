---
name: platform-testing
description: >
  Framework-agnostic testing principles for test design, structure, mocking strategy, and
  reliability. Use when writing tests, reviewing test quality, debugging flaky tests, or
  deciding what to test and how to structure it. Triggers on: writing new tests, naming tests,
  mocking boundaries, async test patterns, test data setup, error path coverage, test isolation.

  Trigger scenarios:
  1. Writing or reviewing any test file
  2. Deciding what to mock and at which boundary
  3. Setting up test data or database fixtures
  4. Debugging flaky or non-deterministic tests
  5. Reviewing test naming or structure
  6. Ensuring error paths are covered

  Trigger phrases: "write a test", "add tests", "test this", "mock this", "how should I test",
  "test structure", "flaky test", "test isolation", "test data", "what to assert"
metadata:
  category: platform
  extends: core-coding-standards
  tags:
  - testing
  - mocking
  - test-design
  - assertions
  - integration
  - test-structure
  status: ready
  version: 4
---

# Principles

- Prefer integration tests over unit tests — test the whole behavior, not individual functions in isolation (Testing Trophy)
- Optimize for confidence, not coverage percentage — test what matters, not what's easy to count
- Mock at system boundaries only — external APIs, time, randomness — not between your own modules
- Always cover error paths — validation errors, auth errors, not-found cases are as important as the happy path

See rules for detailed patterns on naming, structure, assertions, async, and test data.

# Rules

See [rules index](rules/_sections.md) for detailed patterns.

## Examples

### Positive Trigger

User: "Write tests for this invitation endpoint — what should I cover and how should I structure them?"

Expected behavior: Use `platform-testing` guidance, apply naming/structure/error-path rules, and return concrete test skeletons covering happy path and error scenarios.

### Positive Trigger

User: "My test is flaky — it passes locally but fails in CI sometimes."

Expected behavior: Apply `rel-async-deterministic` and `data-test-isolation` rules to diagnose timing or shared-state issues.

### Non-Trigger

User: "Create a tRPC router for billing procedures."

Expected behavior: Do not prioritize `platform-testing`; choose a more relevant skill or proceed without it.

## Troubleshooting

### Skill Does Not Trigger

- Error: The skill is not selected when expected.
- Cause: Request wording does not clearly match the description trigger conditions.
- Solution: Rephrase with explicit domain/task keywords from the description and retry.

### Guidance Conflicts With Another Skill

- Error: Instructions from multiple skills conflict in one task.
- Cause: Overlapping scope across loaded skills.
- Solution: State which skill is authoritative for the current step and apply that workflow first. Framework skills (tech-vitest) extend this skill — prefer framework-specific guidance when available.

### Output Is Too Generic

- Error: Result lacks concrete, actionable detail.
- Cause: Task input omitted context, constraints, or target format.
- Solution: Add specific constraints (environment, scope, format, success criteria) and rerun.

## Workflow

1. Identify whether the request clearly matches `platform-testing` scope and triggers.
2. Apply the skill rules and referenced guidance to produce a concrete result.
3. Validate output quality against constraints; if gaps remain, refine once with explicit assumptions.
