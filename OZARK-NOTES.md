# OZARK-NOTES

**Forked from:** isaacs/minimatch@10968eae898cac1b6c5feedada0de793b1fed0bf (2026-05-22)
**Anchor tag in this repo:** `upstream/10968eae898cac1b6c5feedada0de793b1fed0bf`

## Surface kept

v0.0.1 first-pass fork — no functional trim. Concrete consumer surface to date:

- `Minimatch` (named export, the class) — used by `deterministic-deps/src/rules/index.ts`.

## Surface removed

None in this initial fork.

## Build / runtime notes

- Built with `tshy` (`npm run prepare` → `dist/esm/` and `dist/commonjs/`). Dist artifacts not committed; consumers run `npm install` to trigger `prepare`.
- `engines.node`: `18 || 20 || >=22`. Compatible with consumer Node `>=24`.
- Own deps: `brace-expansion ^5.0.5` (transitive from consumer's POV — stays upstream per pilot policy).
- Publish scripts (`postversion: npm publish`, `prepublishOnly`) removed to defuse accidental registry publication.
