# Testing

Use tests to protect consequential behavior and prevent regressions. Choose them by the risk of a mistake, how easily that mistake could recur, and whether a test gives a trustworthy signal. No coverage percentage or test count is a target.

## Decide what needs protection

| Change | Useful verification |
| --- | --- |
| Async requests or reactive dependencies | Latest input wins; failures and empty results render correctly; disposal stops pending effects. |
| Business rules or a form submission | Relevant valid/invalid boundaries, submission prevention, and resulting action or payload. |
| A reported defect | Reproduce the failing observable behavior, then add a focused regression test when it would catch recurrence. |
| Component contract | Important rendering, user interaction, and emitted event payloads. |
| Trivial text, styling, or straightforward wiring | Existing checks and a focused manual inspection may suffice; add a test only for a meaningful regression risk. |
| A critical journey crossing routing, authentication, browser behavior, or multiple screens | A small E2E scenario when component/unit tests cannot establish the integration behavior. |

Avoid tests for trivial assignments, private ref names, watcher counts, arbitrary component structure, or a reimplementation of the production algorithm. Do not add tests solely because a file changed. Record a relevant verification gap when important behavior cannot currently be tested.

## Work at the smallest useful boundary

For components, interact as a user would and assert visible results or public events. Prefer accessible names, labels, and roles where the installed tools support them. Stable test IDs are reasonable when semantics cannot uniquely identify the target. Avoid selectors tied to incidental CSS, broad snapshots, and replacing every child with a stub.

For composables, assert the returned state and actions. Simple reactive logic can run directly; use a disposed `effectScope` when the test creates watchers, and a mounted host when lifecycle hooks or provide/inject behavior matters. An unmount regression should actually unmount a host. Vue's [composable testing guidance](https://vuejs.org/guide/scaling-up/testing.html#testing-composables) distinguishes reactive logic from logic needing a component instance.

Stub the external boundary needed for determinism, such as the existing API client or `fetch`, rather than each internal helper. Restore global replacements and dispose scopes, mounts, and fake timers after the test. Await Vue updates and controlled promise settlement; do not hide timing bugs with arbitrary sleeps. Reuse the project's established helpers.

## Race regression recipe

For [the request example](reliability.md), control two request promises independently:

1. Search A, then search B while A is pending.
2. Resolve B, then resolve A even if A's cancellation signal was ignored.
3. Assert that B's results remain visible and no stale error appears.
4. In a separate ordering, settle A while B is pending; assert that loading remains true for B.
5. Reject stale A while B is pending or already successful; assert that B's results, error, and loading state are unchanged.
6. Cover a current failure and a successful empty response. Where relevant, clear the input or unmount during a request, then settle it and verify that it cannot publish results afterward.

These cases protect ownership of results, errors, and loading state. A test that only asserts `abort()` was called does not establish that behavior. Keep the smallest set that covers the changed risk; the recipe is not a mandatory matrix for every request.

## Tooling and reporting

Use the application's configured test runner, lint, typecheck, and build commands where relevant. Vitest and Vue Test Utils are reasonable Vue references, not mandatory additions. If an important gap merits new tooling, explain its concrete benefit and cost and obtain approval under the dependency policy before adding it. E2E tools are likewise an explicit project choice. See Vue's [testing overview](https://vuejs.org/guide/scaling-up/testing.html) for the tradeoffs between test boundaries.

Report what ran, its outcome, and any relevant manual checks. Distinguish a command that passed from one that was unavailable, skipped, or failed. If no automated test or typecheck command exists, say so. A documentation example that has only been reviewed is not an executed Vue test.
