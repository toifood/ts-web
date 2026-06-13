ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:recovery 2026-06-14 08:29 → OG worker and Pages Function both implement graceful degradation with abort timeouts

The OG worker (`og-worker/src/index.js`) wraps both the recipe API fetch and the Twemoji CDN fetch in 3-second `AbortController` timeouts, falling back to a default title/emoji on any failure. The Pages Function (`frontend/functions/recipe/[token].js`) also wraps its recipe fetch in a try/catch, falling back to generic title/description defaults. Neither path throws an unhandled error to the user — both return a valid HTML response or PNG regardless of upstream API availability.
