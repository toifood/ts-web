ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:instruction 2026-06-14 08:29 → AnnouncementNote depends on `<ion-icon>` web components with no visible loader in source
## ISSUE:instruction 2026-06-15 20:02 → No README; OG image routing undocumented; split meta responsibility not explained

The repo has no README.md. A developer setting up from source has no documentation for:

1. Architecture: how frontend (Cloudflare Pages), og-worker (Cloudflare Worker), and the backend API (api.toifood.co.nz) relate to each other
2. OG image wiring: the Cloudflare Pages Function constructs OG image URLs as api.toifood.co.nz/recipes/public/:token/og-image, but the og-worker is deployed as a separate worker named 'toifood-og'. It is undocumented how requests from the Pages Function URL reach the og-worker.
3. Split meta responsibility: index.html has default OG tags, SharedRecipe.jsx updates them client-side, and the Cloudflare Pages Function injects them server-side for crawlers. This three-layer pattern is non-obvious and not documented.
4. Deploy steps: there are two separate wrangler.toml files (frontend and og-worker) requiring two separate deploys.

`AnnouncementNote.jsx` renders `<ion-icon name="...">` tags (Ionic Icons web component) for all note type icons. The captured source files contain no `<script src="...ionicons...">` or ESM import for the Ionic icon loader — if this is missing from `index.html` or is loaded from an external CDN, icon elements will silently render as empty on CDN failure or in environments that block third-party scripts. The six icon names used (`information-circle-outline`, `create-outline`, `bulb-outline`, `warning-outline`, `alert-circle-outline`, `megaphone-outline`, `chevron-forward`, `close`) would all need verified CDN availability.
