# Driftwatch

<p align="center">
  <img src="docs/brand/logo.png" width="160" alt="Driftwatch: an all-seeing eye in a targeting reticle">
</p>

[![Agents](https://img.shields.io/badge/for-Cursor%20%7C%20Claude%20Code-purple)](https://github.com/driftwatch-kit/driftwatch)
[![CI](https://img.shields.io/badge/runs%20in-GitHub%20Actions-blue)](https://github.com/driftwatch-kit/driftwatch)
[![License](https://img.shields.io/badge/license-source--available-orange)](https://github.com/driftwatch-kit/driftwatch)
[![Version](https://img.shields.io/badge/kit-v0.2.0-informational)](https://github.com/driftwatch-kit/driftwatch)

Your AI pair programmer skips tests to pass the build, rewrites billing code during "typo fixes", or deletes flaky tests instead of fixing them. Driftwatch catches this before it ships.

## Free `.cursorrules` dumps rot

They say "write clean code." They do not fail a PR.

## Before / after

**Before:** The agent guts or skips a test so CI stays green.

**After:** CI rejects with `skipped_test` (or `weakened_assertion`).

```diff
- it('validates user email', () => {
-   expect(validateEmail('test@example.com')).toBe(true);
- });
+ it.skip('validates user email', () => {
+   // TODO: fix later
+ });
```

CI:

```
skipped_test in test/auth.spec.js
Test marked with it.skip introduced in the diff. PR blocked.
```

## Head to head

| Approach | Catches skipped tests? | Enforces scope? | Fails CI? |
| --- | --- | --- | --- |
| `.cursorrules` only | No (advisory) | No | No |
| Driftwatch | Yes | Yes | Yes |

## What's in v0

Honest inventory of the paid Kit (v0.2.0, created 2026-08-31):

- 7 enforceable Cursor rules
- `AGENTS.md` + `CLAUDE.md`
- GitHub Action + Python checker (stdlib only)
- 31 eval fixtures

Languages the checker looks at for tests, skips, and assertions: **JS/TS** (Jest / Vitest / Mocha), **Python** (pytest / unittest), **Rust** (`#[ignore]`). Dependency manifests: JS, Python, Go, Rust.

This public repo is the **teaser**: three shorter rules, not CI.

## Error codes

The Kit Action fails the job with these codes. Details: [`docs/error-codes.md`](docs/error-codes.md).

- [`out_of_scope`](docs/error-codes.md#out_of_scope) — diff escaped the declared Scope (or a narrow "typo" title).
- [`skipped_test`](docs/error-codes.md#skipped_test) — a skip / pending / ignore marker landed in the diff.
- [`emptied_test`](docs/error-codes.md#emptied_test) — test body became `pass`, empty, or assertion-free.
- [`deleted_test`](docs/error-codes.md#deleted_test) — a test file was removed to go green.
- [`weakened_assertion`](docs/error-codes.md#weakened_assertion) — a strong matcher was replaced by a weak one.
- [`weakened_snapshot`](docs/error-codes.md#weakened_snapshot) — snapshot gutted or wildcarded.
- [`unexplained_dependency`](docs/error-codes.md#unexplained_dependency) — a new package was not named in the PR.

## Quick start (teaser rules)

Copy the three public rules into a Cursor project. They are **advisory** until you buy the Kit and install the Action.

```bash
mkdir -p .cursor/rules
curl -fsSL https://raw.githubusercontent.com/driftwatch-kit/driftwatch/main/.cursor/rules/pr-scope.mdc \
  -o .cursor/rules/pr-scope.mdc
curl -fsSL https://raw.githubusercontent.com/driftwatch-kit/driftwatch/main/.cursor/rules/no-skipped-tests.mdc \
  -o .cursor/rules/no-skipped-tests.mdc
curl -fsSL https://raw.githubusercontent.com/driftwatch-kit/driftwatch/main/.cursor/rules/no-drive-by.mdc \
  -o .cursor/rules/no-drive-by.mdc
```

Leave the notice on each file intact.

## Pricing

| Product | Price | What it is |
| --- | --- | --- |
| **Kit** | **$39** one-time (v0) | Full rules + Action + evals. 30-day refund. Not lifetime updates. |
| **Watch** | **$12/mo** or **$99/year** | Ongoing rule and heuristic updates as agents change. |

Checkout (Polar):

- Kit: [buy.polar.sh — Kit](https://buy.polar.sh/polar_cl_fchKa2xndgHxURruxfYlvnJgM7KTjAR8O1izQ1SdhFe)
- Watch: [buy.polar.sh — Watch](https://buy.polar.sh/polar_cl_Kpzrep1r0L8eLeGtcP0ExA7cB2Kchii2Bf6Hp3jLZiD)

## Watch

Watch ($12/mo or $99/year) is how the checker stays current as Cursor and Claude Code change. You get ongoing rule and heuristic updates, new error codes as new cheat patterns show up (Watch gets them before the next Kit major), and changelog notes on those failure modes. It is not a hosted scanner. The Kit still works without Watch.

## Why not just use linters?

ESLint, Pylint, and Ruff don't know the PR title, don't parse Scope, and don't fail because a test was skipped or an assertion was weakened. They lint syntax and style. Driftwatch diffs the PR against the contract.

## Questions

**Questions or feedback?** Visit the [Driftwatch org](https://github.com/driftwatch-kit). Paid customers get the private kit repo with purchase.

## License

Teaser files in this repo may be copied with the notice on each file left intact. The full pack is source-available for buyers, not MIT.

---

Made By Zer01  
Artificially Intelligent, Digitally Enhanced.
