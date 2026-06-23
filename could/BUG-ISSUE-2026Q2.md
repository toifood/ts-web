ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:bug 2026-06-14 08:29 → `wrapTitle` in OG worker silently drops trailing words when a title fills both lines exactly
## ISSUE:bug 2026-06-24 09:00 → Three runtime bugs in SharedRecipe and og-worker need fixing

**1. SharedRecipe.jsx — fetch calls have no AbortController (lines 212–244)**
The recipe fetch and the subsequent author-profile fetch both run without an `AbortController`. If the user navigates away before either resolves, `setRecipe`, `setAuthor`, `setFullProfile`, `setLoading`, and `setError` are called on an unmounted component. React 18 silences the warning but the state update still runs, can interfere on remount, and wastes network bandwidth.

**2. SharedRecipe.jsx — `isPremium` badge flashes FREE → PREMIUM on load (line 296)**
`fullProfile` starts as `null`, so `authorRole` falls back to `author?.role`. If `author.role` is `'free'` but the profile endpoint returns a paid role, the badge visibly flips from FREE to PREMIUM during the second render. This is a UX regression visible on every shared-recipe page load for premium users.

**3. og-worker/src/index.js — Twemoji CDN failure produces a blank emoji in the OG image**
When the `cdnjs.cloudflare.com` PNG fetch fails or times out, the fallback is an SVG `<text>` element with a font emoji. `@resvg/resvg-wasm` renders SVG without a bundled emoji font, so the fallback renders as an empty glyph — the OG image shows no emoji at all. The WASM init (`wasmInitPromise`) is also awaited *after* all async work is complete, meaning cold-start concurrent requests each burn two round-trips before hitting a potential init failure.
## ISSUE:bug 2026-06-23 22:01 → Three confirmed bugs: dead utility function, broken emoji OG fallback, and inconsistent Twitter meta attribute rewrite

**1. `formatMemberSince` is dead code** (`frontend/src/pages/SharedRecipe.jsx:136`)
Defined at the top of the file but never called anywhere in the JSX. `formatCookDuration` is used, but `formatMemberSince` is not. Causes confusion and will drift out of sync with any future date-formatting changes.

**2. OG-image emoji fallback silently broken** (`og-worker/src/index.js:60–75`)
When the Twemoji CDN fetch fails or returns non-OK (e.g. for compound/unknown emoji), the SVG falls back to a native `<text>` element containing the raw emoji character. `resvg-wasm` is a Rust-based SVG renderer with no system emoji font; it will render the glyph as a blank box or tofu. The catch block swallows the error silently, so the OG image is delivered with no emoji and no signal to the caller.

**3. Cloudflare Pages function rewrites Twitter `property=` to `name=`** (`frontend/functions/recipe/[token].js:27–32`)
`index.html` declares all Twitter card tags with `property=` (matching the OG convention used throughout the file). The Pages function regexes correctly match on `property=`, but the replacement strings switch to `name=` for `twitter:title`, `twitter:description`, and `twitter:image`. The page therefore leaves the request with a mixed-attribute set. Both attributes are accepted by Twitter's crawler in practice, but the inconsistency creates a maintenance trap — the next developer who aligns to spec by switching `index.html` to `name=` will break the Pages function match silently.
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
