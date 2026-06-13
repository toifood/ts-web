ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:migrate 2026-06-14 08:29 → Stack is current; Cloudflare Pages + Workers architecture is well-matched

Vite 5.4, React 18.3, and React Router 6.26 are all recent stable releases. The split of concerns between Cloudflare Pages (hosting + SSR Pages Function) and a separate `toifood-og` Cloudflare Worker (image generation) is architecturally clean. `@resvg/resvg-wasm` for server-side SVG→PNG rendering in the OG worker is a solid edge-compatible choice. No legacy dependencies or deprecated APIs were found.
