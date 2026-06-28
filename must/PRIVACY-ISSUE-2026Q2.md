MUST ISSUE LOG
prompt: PII fields without encryption or retention policy, missing deletion paths, undisclosed third-party data sharing
path: must/PRIVACY-ISSUE-2026Q2.md
target: toifood/-ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ISSUE:PRIVACY 2026-06-28 13:12 ▸ Deletion timeline contradiction and Cloudflare data-residency mismatch

Three material issues in Privacy.jsx (last updated 11 May 2026):
1. **Deletion contradiction** — Section 7 states data removed "within 30 days" but the delete-account banner and FAQ state "All data is permanently removed immediately upon deletion." One statement is false and must be reconciled.
2. **NZ residency claim vs Cloudflare** — Section 1 states AI runs "on infrastructure in New Zealand" and Section 5 states data is stored in a NZ database, but Section 6 discloses Cloudflare for security/networking. Cloudflare routes and caches globally; the NZ-only claim is overstated and potentially misleading under NZ Privacy Act 2020.
3. **Conditional AI provider language** — Section 4 uses hedged language ("if third-party APIs like OpenAI or Anthropic are used") but FAQ.jsx explicitly states Premium uses Claude by Anthropic. The policy must affirmatively name Anthropic as a data processor for Premium users.
