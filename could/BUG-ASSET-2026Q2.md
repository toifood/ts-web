ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-06-14 08:29 → Pages Function OG meta regex correctly exploits tag structure to avoid double-closing
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
