ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-06-14 08:29 → Pages Function OG meta regex correctly exploits tag structure to avoid double-closing

The `og:image` replacement in `frontend/functions/recipe/[token].js` uses `/<meta property="og:image"[^>]*/` — `[^>]*` stops before the `>` character, so for `<meta property="og:image" content="old" />` it matches `<meta property="og:image" content="old" /` (consuming the `/` but leaving the `>`). The replacement string ends with `/`, so the result is `<meta property="og:image" content="new" />` — the original `>` closes the tag. This is a subtle but correct use of the regex; the tag is well-formed in the output.
