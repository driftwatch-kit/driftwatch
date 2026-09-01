# Driftwatch error codes

These are the codes the paid **Kit** GitHub Action emits when it fails a PR. This public teaser does not run that Action. Anchors match the links in [README.md](../README.md).

## out_of_scope

**Meaning.** The diff touched a path the PR did not declare. Scope is a contract, not a vibe.

**When it fires.** A changed file sits outside the `Scope:` block; a "typo" / docs title also edits implementation; the title names one file and a sibling in the same directory changes; or several top-level areas move with no Scope at all.

**Example.**

```
Title: Fix typo in README.md

Scope: (none)
```

```diff
  README.md          # expected
+ src/billing.py     # not a typo
```

CI: `out_of_scope` on `src/billing.py`. Split the PR or retitle and declare Scope.

## skipped_test

**Meaning.** A skip, pending, xfail, or ignore marker was introduced so a test stops running.

**When it fires.** The diff adds markers such as `it.skip`, `xit`, `it.todo`, `test.skip`, `describe.skip`, `@pytest.mark.skip`, `@pytest.mark.xfail`, `pytest.skip`, `self.skipTest`, Mocha `this.skip()`, Rust `#[ignore]`, or `@Disabled`.

**Example.**

```diff
- it('validates user email', () => {
-   expect(validateEmail('test@example.com')).toBe(true);
- });
+ it.skip('validates user email', () => {
+   // TODO: fix later
+ });
```

CI: `skipped_test` in `test/auth.spec.js`. Test marked with `it.skip` introduced in the diff. PR blocked.

## emptied_test

**Meaning.** The test wrapper is still there. The assertions are gone.

**When it fires.** A test body becomes `pass` / `...` / `{}`; every test definition is stripped from a file; or a file still has `it` / `test` / `test_*` defs but every assertion disappeared.

**Example.**

```diff
  def test_login_ok():
-     result = {"ok": True, "user": "ada"}
-     assert result == {"ok": True, "user": "ada"}
+     pass
```

CI: `emptied_test`. Empty tests do not fail and do not protect anything.

## deleted_test

**Meaning.** A test file was removed.

**When it fires.** A path the checker treats as a test file is deleted. Deleting coverage to go green is not allowed. If the surface under test is gone, say so in the PR body and replace the tests.

**Example.**

```diff
- tests/test_auth.py   (file deleted)
```

CI: `deleted_test` on `tests/test_auth.py`.

## weakened_assertion

**Meaning.** A strong matcher was swapped for a weak one so CI stays green.

**When it fires.** Examples: `toEqual` / `toBe(literal)` → `toBeTruthy`; `assert x == 42` → `assert x`; `assertEqual` → `assertTrue(x)`; a pinned literal replaced by `expect.anything()` or `expect.any(T)`.

**Example.**

```diff
- expect(result).toEqual({ total: 42, currency: 'usd' });
+ expect(result).toBeTruthy();
```

CI: `weakened_assertion`. That is silent test weakening.

## weakened_snapshot

**Meaning.** A snapshot no longer pins the output it used to pin.

**When it fires.** The snapshot file is deleted, the payload shrinks sharply, or exact values become wildcards (`Any`, `expect.anything()`, empty trees).

**Example.**

```diff
  exports[`renders settings page 1`] = `
- <div class="settings">
-   <h1>Settings</h1>
-   <form>…</form>
- </div>
+ <div />
  `;
```

CI: `weakened_snapshot`. Gutted snapshots no longer pin behavior.

## unexplained_dependency

**Meaning.** A new package appeared in a manifest and the PR never named it.

**When it fires.** A new name is added in `package.json`, `requirements.txt` / `pyproject.toml`, `go.mod`, or `Cargo.toml` and that name is absent from the PR title and body. Version bumps of packages already in the manifest do not fire.

**Example.**

```
Title: Fix cart total rounding
Body: Scope: src/cart.ts, package.json
```

```diff
  // package.json
  "dependencies": {
+   "lodash": "^4.17.21"
  }
```

CI: `unexplained_dependency`. Name the package, say why, and put the lockfile in Scope.
