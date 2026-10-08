# Code Review Standards

Code review is not a quality gate bolted onto the end of development. It is how the team shares context, catches assumptions, and prevents the kind of drift that turns a coherent codebase into a collection of individually reasonable decisions that do not fit together.

These standards apply to all repositories where Kornspire engineers are writing or reviewing code, regardless of client or project.

---

## What a reviewer is responsible for

A reviewer is not a compiler. The following are the reviewer's job:

- **Correctness** — Does the change do what it says? Are there inputs or states that would cause it to behave incorrectly?
- **Scope** — Does the change touch only what the task requires? A PR that also improves adjacent code is harder to review, harder to revert, and harder to bisect when something breaks later.
- **Test coverage** — Is the behaviour tested? A bug fix with no regression test will be fixed again by someone else in six months.
- **Security** — Does the change introduce any of the standard vulnerability classes? (See [security/practices.md](../security/practices.md) for the checklist.)
- **Understandability** — Can an engineer unfamiliar with this area read the change and understand what it does and why? If not, what is missing — a comment explaining a non-obvious constraint, a better variable name, or a simpler approach?

The following are **not** the reviewer's job, and a review that blocks on them is adding cost without adding value:

- Style preferences that are not enforced by the linter
- Rewrites of code that is correct and clear but written differently than the reviewer would have written it
- Suggestions for future improvements that are unrelated to the current change

---

## Pull request expectations

**Size:** A PR should be reviewable in one sitting. As a guide, 400 lines of changed application code is a reasonable upper limit. Infrastructure, generated code and lock file changes do not count. If a task genuinely requires more, break it into a series of PRs that each leave the system in a working state.

**Title and description:** The title names the change. The description explains why — the problem being solved, the approach chosen, and any non-obvious decisions. A description that says "fixes bug" tells the reviewer nothing they cannot read from the diff.

**Draft PRs:** Use draft PRs for work in progress that needs early feedback on direction. Do not merge a draft.

**One concern per PR:** A PR that fixes a bug and also refactors a module is two PRs. Mixing concerns obscures intent and makes rollbacks destructive.

---

## Review SLA

First review within **one business day** of a PR being marked ready. If this is not possible, say so and give an expected time. A PR waiting in silence is a blocked engineer.

---

## Merge criteria

A PR may be merged when:

1. At least one approving review from an engineer who has read the code (not rubber-stamped)
2. CI passes (type check, lint, tests)
3. All unresolved comments are either addressed or explicitly deferred with a tracked issue

Branch protection on `main` enforces criteria 1 and 2. Criterion 3 is enforced by the team.

---

## What we will not merge

- Code with hardcoded credentials, API keys, tokens or passwords, regardless of whether they are "just for testing"
- Disabled tests without a comment explaining why and a linked issue to re-enable them
- `console.log` / debug output left in production paths
- TODO comments that are not linked to a tracked issue
- Changes that reduce test coverage of a critical path without a documented reason
