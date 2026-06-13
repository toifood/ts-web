ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:usage 2026-06-14 08:29 → Single shared recipe URL triggers up to 3 separate fetches to the same API endpoint

For a single `/recipe/:token` page load, `api.toifood.co.nz/recipes/public/${token}` is called by: (1) the Cloudflare Pages Function at request time for SSR meta injection, (2) the `toifood-og` Worker when the browser/crawler fetches the OG image, and (3) the React client on mount in `SharedRecipe.jsx`. All three calls are independent with no shared cache or coordination. The Pages Function and OG Worker use `cf: { cacheTtl: 300 }`, but the client-side fetch has no CDN caching, so each real user visit produces at minimum one uncached call to the origin.
