ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:bug 2026-06-14 08:29 → `wrapTitle` in OG worker silently drops trailing words when a title fills both lines exactly
## ISSUE:web 2026-06-23 21:00 → Four bugs — timer no-reset, OG tag regex fragility, window.location in render, og-worker twemoji CDN age

**Bug 1 — Timer cannot be restarted after reaching zero** (`frontend/src/pages/SharedRecipe.jsx` ~L441-451)
When `timeLeft` reaches 0, `setTimerActive(false)` is called but `timeLeft` stays at 0. The play button reappears but clicking it starts an interval that immediately fires `setTimerActive(false)` again. No reset mechanism exists — the timer is permanently broken after one full countdown.

**Bug 2 — Fragile OG tag regex in Pages Function** (`frontend/functions/recipe/[token].js` ~L35)
`html.replace(/<meta property="og:image"[^>]*/` strips everything from the opening tag up to the last char before `>`, then appends `/`. This works only if `index.html` contains exactly one such tag with no nested quotes. Any change to the base HTML structure will silently corrupt the injected tag.

**Bug 3 — `window.location` read during render** (`frontend/src/pages/FAQ.jsx` L1059)
`const deepLink = window.location.origin + '/faq#' + id;` is computed in the function body of `FAQItem` on every render. While this works in the current browser-only SPA, it will throw if any SSR or prerendering is ever added.

**Bug 4 — Twemoji CDN URL pinned to 14.0.2** (`og-worker/src/index.js` ~L55)
The OG worker fetches emoji PNGs from `cdnjs.cloudflare.com/ajax/libs/twemoji/14.0.2/72x72/`. Newer emoji codepoints (Unicode 15+) added to the app will produce 404 responses, silently falling back to SVG text rendering which may render as empty boxes in rsvg.
## ISSUE:bug 2026-06-20 19:23 → Three live bugs: og:image URL regression, 2× dimension mismatch, and wrapTitle word-drop still unfixed

**Bug 1 (HIGH) — og:image URL regression** `frontend/functions/recipe/[token].js`
`ogImage` is currently set to `https://api.toifood.co.nz/recipes/public/${token}/og-image`, but the 2026-04-27 ASSET entry confirmed the intent was `https://og.toifood.co.nz/${token}` (the dedicated resvg-wasm Worker). The og-worker is still deployed at `og.toifood.co.nz` (wrangler.toml: `name = "toifood-og"`) and is not being referenced by the Pages Function. Either a regression occurred or the backend added its own `/og-image` route — currently unverified. Until resolved, social crawlers may receive a different (or missing) OG image than the Worker generates.

**Bug 2 (MEDIUM) — og:image dimensions 2× actual** `frontend/functions/recipe/[token].js`
The injected meta tags declare `og:image:width=2400` and `og:image:height=1260`, but the og-worker renders at `W=1200, H=630` (`og-worker/src/index.js` lines `const W = 1200, H = 630`). Declared dimensions are 2× the actual PNG output. Some social platforms (e.g. LinkedIn) reject or distort images where declared and actual dimensions disagree.

**Bug 3 (MEDIUM) — wrapTitle word-drop still unfixed** `og-worker/src/index.js`
Previously logged 2026-06-14. When a title's second line fills to exactly ≤26 chars and the next word triggers `lines.length === 2`, the code sets `current = ''` and breaks — that word is silently discarded with no ellipsis. Example: `"Slow Roasted Lamb Shoulder With Herb Crust"` — the word `"Crust"` is dropped from the OG image text. Fix: replace `current = ''; break;` with `lines.push(current); break;` before resetting.

**Bug 4 (LOW) — twitter meta attribute convention flip** `frontend/functions/recipe/[token].js`
`index.html` uses `property="twitter:title"` / `property="twitter:description"` / `property="twitter:image"`. The Pages Function regex correctly matches these but replaces them with `name="twitter:..."`. After processing, the served HTML has a mix: `og:*` tags use `property=`, twitter tags use `name=`. No functional breakage (both are accepted by crawlers), but the inconsistency with the source convention is confusing.
## ISSUE:bug 2026-06-15 20:02 → Three bugs: empty utility crash, twitter meta regex mismatch, FAQ scroll timing

Bug 1 (CRITICAL) — frontend/src/utils/announcementNote.js is empty. useAnnouncementNoteManager imports resolveNote, resolveDietaryAllergyNote, resolveDietaryInfoNote from it. These exports do not exist. SharedRecipe throws TypeError on any recipe that has announcement note fields.

Bug 2 (MEDIUM) — In frontend/functions/recipe/[token].js, the regex replacements for twitter:title and twitter:description target the attribute 'property=' but then emit 'name=' in the replacement. The source index.html also uses 'property=' for twitter tags. This means the regex will match index.html's tags, but after replacement the new tags use 'name=' — creating an inconsistency. More critically, if the regex targets 'property="twitter:title"' but index.html uses 'name="twitter:title"', the replacement silently does nothing and leaves stale default tags.

Bug 3 (MINOR) — FAQ.jsx FAQItem deeplink scroll uses setTimeout(..., 300) which may fire before the component's layout is fully painted on slow connections, causing misaligned scroll position.

In `og-worker/src/index.js`, the `wrapTitle` function builds lines word-by-word. When `lines.length === 2` is reached mid-word, the code sets `current = ''` and breaks — the word that triggered the second-line overflow is discarded without being pushed to `lines`. For a title like `"Slow Roasted Lamb Shoulder With Herb Crust"` where the second line fills to exactly 26 chars and the next word triggers the break, that next word is lost from the OG image text with no ellipsis or truncation indicator. The fix is to push `current` before breaking when `lines.length === 2`.
