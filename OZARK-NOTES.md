# OZARK-NOTES

**Forked from:** isaacs/minimatch@10968eae898cac1b6c5feedada0de793b1fed0bf (2026-05-22)
**Anchor tag in this repo:** `upstream/10968eae898cac1b6c5feedada0de793b1fed0bf`

## Surface kept

Three runtime exports plus type re-exports:

| Export | Purpose | Consumer use |
|---|---|---|
| `Minimatch` (class) | Compile a glob pattern into a matcher with `.match(filename)`. | `deterministic-deps/src/rules/index.ts:4` — `import { Minimatch } from 'osl-minimatch'`. Used at lines 2788 (`new Minimatch(entry.file).match(finding.file)`) and 2801 (`new Minimatch(pattern, { dot: true })`). |
| `sep` (const) | Platform path separator (`/` or `\\`). | Not directly imported; preserved because it's a one-line cost and downstream type-level work may reference `Sep`. |
| `GLOBSTAR` (symbol) | Sentinel for `**` in parsed pattern trees. | Internal to Minimatch parse results; preserved as part of the type contract. |

Plus type-only exports: `Platform`, `MinimatchOptions`, `Sep`, `MMRegExp`, `ParseReturnFiltered`, `ParseReturn`.

## Surface removed

Public-surface trim from ~10 runtime exports down to 3.

**Removed top-level functions (`src/index.ts`):**
- `minimatch(p, pattern, options)` — helper that creates a Minimatch and calls match. Function definition + all `minimatch.X = X` attached properties (`minimatch.sep`, `.GLOBSTAR`, `.filter`, `.defaults`, `.braceExpand`, `.makeRe`, `.match`, `.AST`, `.Minimatch`, `.escape`, `.unescape`).
- `filter(pattern, options)` — wrapped `minimatch()` into a `.filter` predicate.
- `defaults(def)` — factory that returned a customized `minimatch` function (a deep nested factory pattern with its own embedded Minimatch and AST subclasses).
- `makeRe(pattern, options)` — convenience wrapper for `new Minimatch(pattern, opts).makeRe()`.
- `match(list, pattern, options)` — convenience wrapper that filtered a list.

**Removed re-exports:**
- `escape`, `unescape` (re-exported from `./escape.js` and `./unescape.js`).
- `AST` (re-exported from `./ast.js`).

**Removed class method:**
- `Minimatch.defaults` static method (called the removed top-level `defaults` function).

**Removed source files:**
- `src/escape.ts` (33 LOC) — only imported by the removed top-level `escape` re-export and `defaults` factory; no internal usage in `Minimatch` class.

**Source files kept (deliberately):**
- `src/index.ts` — still 1,358 LOC (was 1,485). The `Minimatch` class itself is ~1,000 LOC and we keep it entirely. The internal `braceExpand` helper is now non-exported (was `export const braceExpand`) but still defined because `Minimatch.braceExpand()` instance method depends on it.
- `src/ast.ts` — 977 LOC, used by Minimatch (`AST.fromGlob`). Kept entirely.
- `src/brace-expressions.ts` — 172 LOC, `parseClass` used by ast.ts. Kept entirely.
- `src/assert-valid-pattern.ts` — 12 LOC, used by Minimatch. Kept.
- `src/unescape.ts` — 42 LOC, imported by ast.ts. Kept (no longer imported from index.ts, but still needed by ast).

**Directory-level deletes:**
- `test/` (entire upstream test suite — exercised removed surface).
- `tap-snapshots/`, `typedoc.json`, `benchmark.js` — non-source noise.
- `changelog.md` — upstream's CHANGELOG, not relevant.

## Build / runtime notes

- Build is driven by upstream's `prepare` script (`tshy`). Produces `dist/commonjs/` and `dist/esm/`. `dist/` is gitignored upstream; the Ozark fork preserves that convention.
- ESM and CJS entry points are unchanged from upstream — consumers can `import { Minimatch } from 'osl-minimatch'` (ESM) or `require('osl-minimatch').Minimatch` (CJS).
- Own runtime dependency `brace-expansion ^5.0.5` is preserved (used by the kept `braceExpand` helper). Per pilot policy ("direct deps + selective vendoring"), it stays upstream.
- Publish scripts (`postversion: npm publish`, `prepublishOnly`) were removed earlier to defuse accidental registry publication.
- Smoke test verified: `new Minimatch('*.ts').match('a.ts') === true`, `new Minimatch('*.ts').match('a.js') === false`, `new Minimatch('**/*.test.ts', { dot: true }).match('foo/bar.test.ts') === true`.
