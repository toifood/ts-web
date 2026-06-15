ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:bug 2026-06-14 08:29 → `wrapTitle` in OG worker silently drops trailing words when a title fills both lines exactly
## ISSUE:bug 2026-06-15 20:02 → Three bugs: empty utility crash, twitter meta regex mismatch, FAQ scroll timing

Bug 1 (CRITICAL) — frontend/src/utils/announcementNote.js is empty. useAnnouncementNoteManager imports resolveNote, resolveDietaryAllergyNote, resolveDietaryInfoNote from it. These exports do not exist. SharedRecipe throws TypeError on any recipe that has announcement note fields.

Bug 2 (MEDIUM) — In frontend/functions/recipe/[token].js, the regex replacements for twitter:title and twitter:description target the attribute 'property=' but then emit 'name=' in the replacement. The source index.html also uses 'property=' for twitter tags. This means the regex will match index.html's tags, but after replacement the new tags use 'name=' — creating an inconsistency. More critically, if the regex targets 'property="twitter:title"' but index.html uses 'name="twitter:title"', the replacement silently does nothing and leaves stale default tags.

Bug 3 (MINOR) — FAQ.jsx FAQItem deeplink scroll uses setTimeout(..., 300) which may fire before the component's layout is fully painted on slow connections, causing misaligned scroll position.

In `og-worker/src/index.js`, the `wrapTitle` function builds lines word-by-word. When `lines.length === 2` is reached mid-word, the code sets `current = ''` and breaks — the word that triggered the second-line overflow is discarded without being pushed to `lines`. For a title like `"Slow Roasted Lamb Shoulder With Herb Crust"` where the second line fills to exactly 26 chars and the next word triggers the break, that next word is lost from the OG image text with no ellipsis or truncation indicator. The fix is to push `current` before breaking when `lines.length === 2`.
