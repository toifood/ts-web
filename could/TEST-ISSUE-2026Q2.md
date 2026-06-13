ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-06-14 08:29 → Zero tests across all packages; no testing framework installed

`frontend/package.json` has only `@vitejs/plugin-react` and `vite` as devDependencies — no Vitest, Jest, Testing Library, or Playwright. `og-worker/package.json` has only `@resvg/resvg-wasm` as a dependency and no test tooling. No test files exist anywhere in the codebase. Critical untested paths include: `wrapTitle` edge cases in the OG worker, `resolveNote` / `resolveDietaryAllergyNote` / `resolveDietaryInfoNote` in `announcementNote.js`, the Pages Function meta-tag regex replacements, and the `useAnnouncementNoteManager` hook logic.
