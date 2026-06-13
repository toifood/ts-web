ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:bug 2026-06-14 08:29 → `wrapTitle` in OG worker silently drops trailing words when a title fills both lines exactly

In `og-worker/src/index.js`, the `wrapTitle` function builds lines word-by-word. When `lines.length === 2` is reached mid-word, the code sets `current = ''` and breaks — the word that triggered the second-line overflow is discarded without being pushed to `lines`. For a title like `"Slow Roasted Lamb Shoulder With Herb Crust"` where the second line fills to exactly 26 chars and the next word triggers the break, that next word is lost from the OG image text with no ellipsis or truncation indicator. The fix is to push `current` before breaking when `lines.length === 2`.
