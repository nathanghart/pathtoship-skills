---
name: pathtoship-badge
description: Add the PathToShip readiness badge to a README or site after a scan clears the ship bar. Use when get_score or verify_fix returns verdict "ready", or when the user asks to show their readiness score.
---

# Readiness badge

When an app clears the ship bar, the score is worth showing — to users, clients, and the next developer. The badge is a live SVG that always reflects the scan it points to and links to the public report.

## When

- `get_score` or `verify_fix` returns `verdict: "ready"` — offer once, do not nag.
- The user asks to display or share their score on the README or site.

## Procedure

1. Confirm with `get_score` that the verdict is `ready` and note `scan_id` and `permalink`. If it is `conditional` or `not_ready`, say so and do not add a badge — a badge under the bar helps no one.
2. Offer the badge and, if the user agrees, add it:
   - README (Markdown), replacing the placeholders:

     ```markdown
     [![PathToShip Score: SCORE/100](https://pathtoship.com/api/badge/SCAN_ID)](PERMALINK)
     ```

   - Site (an image linked to the report): the same badge URL as the image source, the permalink as the link target, and "PathToShip Score: SCORE/100" as the alt text.
   - Variants via `?variant=gauge`, `?variant=minimal`, or `?variant=seal` on the badge URL; the default is fine for a README.
3. Place it near the top of the README (under the title) or in the site footer — where a visitor would look for trust signals. Suggest the commit message `docs: add PathToShip readiness badge`; the user or their builder commits it.
4. After later changes, the badge keeps pointing at _that_ scan. When the user runs a new scan that is also `ready`, offer to update the `scan_id` so the badge reflects the current code. If a later scan drops below the bar, tell the user the badge is stale and offer to remove it rather than leave a misleading one.

## Certified seal

PathToShip can issue a certified seal for scans at or above the certification threshold; the user requests it from the report page (the `permalink`), under "Certify". Use the plain score badge in the meantime.

## Rules

- Never add a badge for a scan that is not `ready`, and never edit the score text by hand — the SVG renders the real number.
- One badge per app; do not stack variants.
- Set `trigger: "skill"` on tool calls this skill initiates.
