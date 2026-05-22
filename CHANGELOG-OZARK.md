# CHANGELOG-OZARK

All Ozark-side patches applied on top of the upstream fork point (`upstream/10968eae898cac1b6c5feedada0de793b1fed0bf`).

## 2026-05-22 — Initial Ozark fork

- **Type:** initial setup
- **Reference:** internal pilot — Ozark internal stdlib bring-up
- **Author:** @bjcorder
- **Notes:** Renamed package to `osl-minimatch`, version `0.0.1`. Removed `postversion: npm publish` and `prepublishOnly` scripts. Updated `repository.url`. Added `OZARK-NOTES.md`, `CHANGELOG-OZARK.md`, `LICENSE-UPSTREAM`, Ozark README header. No functional trim.

## 2026-05-22 — Aggressive public-surface trim

- **Type:** trim — public API reduction
- **Reference:** consumer surface audit: `deterministic-deps` uses only the `Minimatch` class with `new Minimatch(pattern, options?).match(filename)`
- **Author:** @bjcorder
- **Notes:** Reduced public runtime exports from ~10 to 3 (`Minimatch`, `sep`, `GLOBSTAR`) plus 6 type-only exports. Removed top-level functions `minimatch` (the helper), `filter`, `defaults`, `makeRe`, `match` from `src/index.ts`. Removed all `minimatch.X = X` attached property assignments (sep, GLOBSTAR, filter, defaults, braceExpand, makeRe, match, AST, Minimatch, escape, unescape). Removed the `escape`/`unescape`/`AST` re-exports. Removed the static `Minimatch.defaults` method (called the removed top-level `defaults`). Removed the `export` keyword from internal `braceExpand` helper (still used by `Minimatch.braceExpand` instance method). Deleted `src/escape.ts` (33 LOC). Deleted entire upstream `test/` directory (511 LOC), `tap-snapshots/`, `typedoc.json`, `benchmark.js`, `changelog.md`. Net src LOC: 2,721 → 2,561 (6% reduction in retained source — the `Minimatch` class itself is ~1,000 LOC and is preserved entirely since it's the consumer's sole entry point). Smoke test verified `new Minimatch('*.ts').match('a.ts')` round-trips correctly.
