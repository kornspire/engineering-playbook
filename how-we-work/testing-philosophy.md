# Testing Philosophy

Tests exist to let you change code with confidence. A test suite that cannot be changed without breaking is not protecting you — it is slowing you down. A test suite that never breaks regardless of what you do is also not protecting you.

These principles apply across all projects. The specific tooling varies by platform and client environment.

---

## The pyramid, and when to deviate from it

The classic test pyramid (many unit tests, fewer integration tests, few end-to-end tests) reflects a cost model: unit tests are fast and cheap to run, end-to-end tests are slow and brittle. It is not a rule.

**Prefer integration tests over unit tests for critical data paths.** A test that verifies the application against a real database catches a broken migration, a missing index, or a query that happens to work on the in-memory mock but fails against the actual engine. Unit tests with mocked databases have caught actual bugs — but the historical rate of mock/real divergence on data-layer changes is high enough that mocked database tests are a liability for anything critical.

**End-to-end tests for user-facing flows that cannot reasonably be verified any other way.** Authentication, payment flows, and multi-step forms are the typical cases. E2E tests are expensive to maintain; limit them to the paths where the failure cost is highest.

**Unit tests for pure logic with many variants.** Input validation, data transformation, calculation logic, state machines. Fast feedback, easy to write exhaustive cases.

---

## What must have tests

Any change that:

- Fixes a bug must include a test that would have caught the bug. Not "should have a test added later." The test is part of the fix.
- Introduces a new business rule must have at least one test for the rule and at least one for an input the rule should reject.
- Changes a data schema must have a migration test that verifies the schema before and after the migration.

---

## What we test in production code

Tests should verify **behaviour**, not implementation. A test that breaks when you rename a private method is testing the wrong thing. A test that breaks when the method returns incorrect results is testing the right thing.

This has a practical consequence: tests that reach into private internals to assert on intermediate state are a code smell. Reorganising the internals should not require rewriting tests. If you cannot test a behaviour without reaching into internals, the question is whether the interface needs rethinking, not whether the test should be written anyway.

---

## Assertions and error messages

Every assertion should have an error message that names the thing being tested and the expected state. `expect(result).toBe(true)` failing tells you nothing. `expect(isValid, 'invoice with zero line items should be invalid').toBe(false)` tells you exactly where to look.

---

## Coverage as a metric

Line coverage is a floor, not a target. 80% coverage can mean 80% of lines are executed by tests that assert nothing meaningful. We do not chase a coverage number; we review whether the tests cover the decisions the code makes, not just the lines it executes.

---

## Testing content and business rules

For systems where the content is generated from typed data (CMS-driven sites, configuration-driven platforms), the data should be tested as code. A broken internal link, a service that references a non-existent category, or a claim that contradicts the documented scope should fail a test at commit time, not be discovered by a user or a search crawler.

This is not a special category of testing — it is the same principle applied to a different kind of logic.
