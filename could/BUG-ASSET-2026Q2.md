ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-06-14 08:29 → Pages Function OG meta regex correctly exploits tag structure to avoid double-closing
## ASSET:web 2026-06-23 21:00 → API error handling and OG worker fallbacks are solid throughout

**1. SharedRecipe error state** (`frontend/src/pages/SharedRecipe.jsx` ~L476-485)
The recipe fetch is wrapped in `.catch(() => setError(true))` with a friendly full-page error UI ("Recipe not found" with a back link), rather than crashing or showing a blank page.

**2. og-worker abort timeouts** (`og-worker/src/index.js`)
Both the recipe API call and the Twemoji PNG fetch use explicit `AbortController` with 3-second timeouts. If either fails, the worker continues gracefully — recipes without emoji fall back to SVG text rendering, and the response is always a valid PNG.

**3. Pages Function fallthrough** (`frontend/functions/recipe/[token].js`)
If the recipe API is unavailable at SSR time, the function falls through to safe default values (`title = 'toifood'`, `description = 'A recipe shared on toifood.'`) rather than returning a 500 error.

**4. AnnouncementNote null guard** (`frontend/src/components/AnnouncementNote.jsx` L494)
`if (!config || !config.text) return null;` prevents rendering an empty note card if the backend returns a note with no text, or if `resolveNote()` returns null.
## ASSET:bug 2026-06-20 19:23 → Analysis only — no files changed

Four bugs identified (see corresponding ISSUE entry). No code was modified in this run. All bugs are in existing files:
- `frontend/functions/recipe/[token].js` — og:image URL, dimensions, twitter attribute convention
- `og-worker/src/index.js` — wrapTitle word-drop
## ASSET:bug 2026-06-15 20:02 → Fix stubs for announcementNote, fix twitter meta regexes, fix FAQ scroll timing

Fix 1 — announcementNote.js minimum stubs to prevent crash:
export const resolveNote = (n) => (n?.text ? { text: n.text, type: n.type ?? 'note' } : null);
export const resolveDietaryAllergyNote = () => null;
export const resolveDietaryInfoNote = () => null;
(Full implementation in RECOVERY-ASSET)

Fix 2 — Cloudflare Function twitter meta regexes in frontend/functions/recipe/[token].js:
Change all three twitter tag replacements to match either 'property=' or 'name=' attribute:
html = html.replace(/<meta (?:property|name)="twitter:title"[^>]*>/, `<meta name="twitter:title" content="${t}" />`);
Apply same pattern to twitter:description and twitter:image.

Fix 3 — FAQ deeplink scroll timing in FAQ.jsx FAQItem:
Replace setTimeout(() => scrollIntoView(...), 300) with:
requestAnimationFrame(() => requestAnimationFrame(() => itemRef.current?.scrollIntoView({ behavior: 'smooth', block: 'center' })));

The `og:image` replacement in `frontend/functions/recipe/[token].js` uses `/<meta property="og:image"[^>]*/` — `[^>]*` stops before the `>` character, so for `<meta property="og:image" content="old" />` it matches `<meta property="og:image" content="old" /` (consuming the `/` but leaving the `>`). The replacement string ends with `/`, so the result is `<meta property="og:image" content="new" />` — the original `>` closes the tag. This is a subtle but correct use of the regex; the tag is well-formed in the output.
