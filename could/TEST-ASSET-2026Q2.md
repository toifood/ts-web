ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:test 2026-06-14 08:29 → Utility functions and hooks are structured for easy unit testing
## ASSET:test 2026-06-20 19:23 → Analysis only — no files changed

Zero test coverage confirmed, three priority test targets identified (see corresponding ISSUE entry). No test files were created in this run. To set up:
- `frontend/`: add Vitest to `devDependencies`, add `"test": "vitest"` script to `package.json`
- `og-worker/`: add a `test/wrapTitle.test.js` file (no extra dependencies needed for pure function tests)
- Pages Function: can be tested with a Vitest environment or a plain Node test runner against the exported `onRequest` function with a mocked `context`
## ASSET:test 2026-06-15 20:02 → Add Vitest unit tests for utilities and hooks + Playwright E2E for SharedRecipe golden path

Step 1 — Install Vitest in frontend/package.json devDependencies:
"vitest": "^2.x", "@testing-library/react": "^16.x", "@testing-library/user-event": "^14.x", "jsdom": "^25.x"
Add script: "test": "vitest"

Step 2 — Unit tests to write first (highest ROI):
- announcementNote.test.js: resolveNote, resolveDietaryAllergyNote, resolveDietaryInfoNote with null/empty/valid inputs (would have caught the empty-file bug)
- og-worker/src/index.test.js: wrapTitle with 0/1/2/3-word titles and 26-char boundary; emojiCodepoint with and without variation selectors; bufToBase64 round-trip
- SharedRecipe.test.js: formatCookDuration at 0, 11, 12, 36 months; formatMemberSince formatting
- cloudflare-function.test.js: meta tag injection for all 7 replaced tags including twitter property/name mismatch

Step 3 — Playwright E2E:
- Load /recipe/:token with a known public token; verify title, emoji, ingredients, method steps render
- Verify OG tags are set in SSR HTML response
- Verify /recipe/invalid-token shows error state, not blank screen

The utility layer (`src/utils/announcementNote.js`) is composed of three pure functions with no side effects: `resolveNote`, `resolveDietaryAllergyNote`, `resolveDietaryInfoNote`. The `wrapTitle` and `escapeXml` functions in the OG worker are also pure. The `useReveal` hook only wraps `IntersectionObserver`, which can be mocked. `useAnnouncementNoteManager` takes `recipe` and `dietaryInfo` as plain arguments and returns a plain object — trivial to test with Vitest + `renderHook`. Adding Vitest to `frontend` and a simple Node test runner to `og-worker` would provide coverage with minimal setup.
