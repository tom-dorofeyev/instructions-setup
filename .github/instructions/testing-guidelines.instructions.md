---
applyTo: "**/*.test.*,**/*.spec.*"
---
# Testing Guidelines

## Purpose
- Ensure tests are clean, readable, and resilient.
- Capture **behavior** and **business rules**, not implementation details.
- Encourage a **TDD/BDD** mindset to drive design and prevent regressions.

## Naming
- Use concise, descriptive names that state the **behavior or rule**.
- Avoid bloated prefixes like `should_`.
- Prefer patterns like: `returns {expectedOutcome}`, `{functionName} {expectedBehavior} given {context}`, `given {precondition}, {should behavior}`, `triggers {event} when {action}`

## SOLID in Tests
- **S (Single Responsibility):** One main behavior per test.
- **O (Open/Closed):** Prefer adding tests over modifying many; don’t overfit to internals.
- **L (Liskov):** Test via public contracts; derived types should pass the same contract tests.
- **I (Interface Segregation):** Keep helpers/fixtures small and focused.
- **D (Dependency Inversion):** Depend on abstractions (ports), replace externals with fakes/mocks at boundaries.

## TDD & BDD Mindset
- Aim for **Red → Green → Refactor**: write the failing test first.
- Phrase scenarios as **Given–When–Then** (explicitly in BDD tools or implicitly in structure).
- Let tests drive API shape; refactor tests and code together for clarity.

## Business Rules & Guardrails
- Before/while testing, **inspect business requirements**; encode them explicitly.
- If writing tests **after** production code, **raise a flag** for:
  - Missing edge cases or contradictory rules.
  - Areas where implementation influenced the test (overfitting).
  - Implicit assumptions not documented by product.

## Assertions
- Prefer **one primary assertion** per test; group related checks only if they clarify the behavior.
- Assert **observable outcomes** (state change, return value, interaction at boundaries) over internals.
- Use custom assertion helpers to express domain invariants succinctly.

## Test Independence & Non-Overlap
- Each change in production should ideally affect **one logical test area**.
- Avoid coupling tests to private details or shared mutable state.
- Isolate side effects; reset state between tests.

## Structure & Setup
- Keep setup **minimal and explicit**; only build what the test needs.
- Use factories/builders for domain objects; avoid massive fixtures.
- Prefer in-memory fakes for infrastructure (DB, queues) when behaviorally equivalent.

## Coverage & Case Selection
- Target **meaningful** coverage: happy paths, edge cases, and error handling.
- Include negative tests (invalid input, authorization failures, timeouts).
- Cover **regression cases** when bugs are found (add a test first).

## Doubles (Mocks/Fakes/Stubs)
- Mock **only** external boundaries (network, filesystem, clock, randomness).
- Favor **fakes** over heavy mocks when they better model real behavior.
- Keep interaction tests focused: verify essential calls, not every detail.

## Data & Determinism
- Make tests **deterministic**: control time, randomness, and env.
- Keep test data **small and named**; prefer examples that read like business cases.

## Performance & Flakiness
- Keep unit tests fast (<100ms typical); push slower checks to integration/e2e.
- Quarantine flaky tests; fix root cause (async timing, real network, race conditions).
- Parallel-safe: no hidden global state or port collisions.

## Maintainability
- Treat tests as first-class code: refactor ruthlessly for readability.
- Extract common patterns into helpers; avoid DRY-obsession that obscures intent.
- Keep test files small and cohesive by feature/behavior.

## CI & Reporting
- Run tests in CI on every change; fail fast with clear output.
- Track **coverage trend**, not just a % gate; don’t game the metric.
- Surface slowest tests and flaky tests in reports.

## Anti-Patterns (Avoid)
- Tests that mirror implementation line-by-line.
- Excessive assertions that dilute the main behavior.
- Copy-pasted tests with tiny diffs.
- Real network/DB in unit tests (unless it’s an integration test by design).
- Time-sensitive sleeps; prefer controlled clocks or await proper signals.

## Pull Request Checklist
- The test name clearly states the **behavior/rule**.
- One main behavior asserted; setup is minimal and obvious.
- No tight coupling to private internals.
- Business rule is explicit; edge/negative paths considered.
- Deterministic: controls time/randomness/I/O.
- Adds or updates **regression tests** when fixing a bug.
- Doesn’t cause unrelated tests to fail; overlaps kept minimal.
