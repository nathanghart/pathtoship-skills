---
name: pathtoship-badge
description: Add the PathToShip readiness badge to the README and the app's site once a scan clears the ship bar. Use when get_score or verify_fix returns verdict "ready", or when the user asks to show their readiness score or credential.
---

# Readiness badge

When an app clears the ship bar, the score is worth showing — to users, clients, and the next developer. The badge is a live SVG that always reflects the scan it points to and links to the public report.

## When

- `get_score` or `verify_fix` returns `verdict: "ready"` — offer once, do not nag.
- The user asks to display or share their score, or to add a "credential" to the README or site.

## Procedure

1. Confirm with `get_score` that the verdict is `ready` and note `scan_id` and `permalink`. If it is `conditional` or `not_ready`, say so and do not add a badge — a badge under the bar helps no one.
2. Offer the badge and, if the user agrees, add it:
   - **README (Markdown):**
     `[![PathToShip Score: <score>/100](https://pathtoship.com/api/badge/<scan_id>)](<permalink>)`
   - **HTML / site:**
     `<a href="<permalink>"><img src="https://pathtoship.com/api/badge/<scan_id>" alt="PathToShip Score: <score>/100"></a>`
   - Variants via `?variant=gauge`, `?variant=minimal`, or `?variant=seal` on the badge URL; default is fine for a README.
3. Place it near the top of the README (under the title) or in the site footer — where a visitor would look for trust signals. Commit as `docs: add PathToShip readiness badge`.
4. After later changes, the badge keeps pointing at _that_ scan. When the user runs a new scan that is also `ready`, offer to update the `scan_id` so the badge reflects the current code. If a later scan drops below the bar, tell the user the badge is stale and offer to remove it rather than leave a misleading one.

## Certified seal

PathToShip can issue a certified seal (`/api/badge/certified/<id>`) for scans at or above the certification threshold after an email confirmation on the report page. Until that is a one-click step from your assistant, point the user to the report `permalink` → "Certify" and use the plain score badge in the meantime.

## Rules

- Never add a badge for a scan that is not `ready`, and never edit the score text by hand — the SVG renders the real number.
- One badge per app; do not stack variants.
- Set `trigger: "skill"` on tool calls this skill initiates.
