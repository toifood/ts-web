ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:instruction 2026-06-14 08:29 → `useAnnouncementNoteManager` is correctly memoized on the exact recipe fields that produce notes
## ASSET:instruction 2026-06-15 20:02 → Create README.md covering architecture, deploy steps, and service wiring

Create frontend/README.md (or root README.md) with:

Architecture:
- frontend/ — React 18 + Vite SPA deployed to Cloudflare Pages
- og-worker/ — Cloudflare Worker rendering OG images with @resvg/resvg-wasm, deployed as 'toifood-og'
- frontend/functions/ — Cloudflare Pages Functions for server-side meta tag injection
- API: https://api.toifood.co.nz (separate backend repo)

Deploy:
1. cd frontend && npm install && npm run build (Cloudflare Pages CI handles this)
2. cd og-worker && npx wrangler deploy (deploys toifood-og worker)
3. Route api.toifood.co.nz/recipes/public/*/og-image to the toifood-og worker via Cloudflare routing rules

OG image flow: SharedRecipe page load → Pages Function injects server-side meta → og:image points to api.toifood.co.nz/.../:token/og-image → Cloudflare routing sends that to og-worker → og-worker fetches recipe + Twemoji → renders PNG

`useAnnouncementNoteManager` uses `useMemo` with a dependency array of `[recipe.descriptionNote, recipe.ingredientNote, recipe.methodNote, recipe.steps?.length, recipe.dietaryTags, dietaryInfo]` — these are exactly the fields consumed by the note resolvers. The `longRecipeNote` threshold (`steps.length > 8`) uses `.length` not the array reference, avoiding spurious recalculations. `resolveDietaryAllergyNote` and `resolveDietaryInfoNote` correctly partition tags by `criticalAllergen` vs `dietaryGuideline` fields in `DIETARY_INFO`, producing appropriately typed `warning` vs `headsup` notes.
