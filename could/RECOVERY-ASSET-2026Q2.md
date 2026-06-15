ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:recovery 2026-06-14 08:29 → OG worker and Pages Function both implement graceful degradation with abort timeouts
## ASSET:recovery 2026-06-15 20:02 → Fix empty utility, add ErrorBoundary, add retry button to SharedRecipe error state

Priority 1 — Implement announcementNote.js (breaking fix):
export function resolveNote(note) { if (!note?.text) return null; return { text: note.text, type: note.type ?? 'note' }; }
export function resolveDietaryAllergyNote(tags, dietaryInfo) { const a = tags.filter(t => dietaryInfo[t]?.criticalAllergen); if (!a.length) return null; return { text: 'This recipe is designed to be free of: ' + a.map(t => dietaryInfo[t].criticalAllergen).join(', ') + '. Always check ingredient labels.', type: 'warning' }; }
export function resolveDietaryInfoNote(tags, dietaryInfo) { const g = tags.filter(t => dietaryInfo[t]?.dietaryGuideline); if (!g.length) return null; return { text: 'This recipe uses ' + g.map(t => dietaryInfo[t].dietaryGuideline).join(', ') + '.', type: 'info' }; }

Priority 2 — Add ErrorBoundary component wrapping Routes in App.jsx

Priority 3 — Add a 'Try again' button in SharedRecipe error state that calls window.location.reload()

The OG worker (`og-worker/src/index.js`) wraps both the recipe API fetch and the Twemoji CDN fetch in 3-second `AbortController` timeouts, falling back to a default title/emoji on any failure. The Pages Function (`frontend/functions/recipe/[token].js`) also wraps its recipe fetch in a try/catch, falling back to generic title/description defaults. Neither path throws an unhandled error to the user — both return a valid HTML response or PNG regardless of upstream API availability.
