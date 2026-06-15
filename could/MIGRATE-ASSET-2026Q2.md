ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:migrate 2026-06-14 08:29 → Stack is current; Cloudflare Pages + Workers architecture is well-matched
## ASSET:migrate 2026-06-15 20:02 → Migration checklist: React 19 + Vite 6 + react-router-dom v7

Packages to upgrade:
- react + react-dom: 18.3.1 → 19.x
- vite: 5.4.8 → 6.x
- @vitejs/plugin-react: 4.3.1 → latest
- react-router-dom: 6.26.2 → 7.x

Breaking change checklist:
- react-router v7: BrowserRouter still supported but createBrowserRouter is now preferred
- react-router v7: verify no useNavigate replace-option usage
- React 19: StrictMode double-invokes effects — verify SharedRecipe.jsx timer does not double-start
- Vite 6: test cold-start with optimizeDeps defaults

Steps: cd frontend && npm update, run vite build, open /, /recipe/test-token, /faq. Confirm Cloudflare Pages deploy succeeds. Consider bumping compatibility_date from 2024-09-23.

Vite 5.4, React 18.3, and React Router 6.26 are all recent stable releases. The split of concerns between Cloudflare Pages (hosting + SSR Pages Function) and a separate `toifood-og` Cloudflare Worker (image generation) is architecturally clean. `@resvg/resvg-wasm` for server-side SVG→PNG rendering in the OG worker is a solid edge-compatible choice. No legacy dependencies or deprecated APIs were found.
