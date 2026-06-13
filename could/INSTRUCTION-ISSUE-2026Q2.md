ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:instruction 2026-06-14 08:29 → AnnouncementNote depends on `<ion-icon>` web components with no visible loader in source

`AnnouncementNote.jsx` renders `<ion-icon name="...">` tags (Ionic Icons web component) for all note type icons. The captured source files contain no `<script src="...ionicons...">` or ESM import for the Ionic icon loader — if this is missing from `index.html` or is loaded from an external CDN, icon elements will silently render as empty on CDN failure or in environments that block third-party scripts. The six icon names used (`information-circle-outline`, `create-outline`, `bulb-outline`, `warning-outline`, `alert-circle-outline`, `megaphone-outline`, `chevron-forward`, `close`) would all need verified CDN availability.
