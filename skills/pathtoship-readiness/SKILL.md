---
name: pathtoship-readiness
description: Run a PathToShip production-readiness check before deploying, exposing a public URL, or handling real user data, then drive the scan → fix → verify loop. Use when the user mentions launching, deploying, going live, shipping, adding auth/payments/uploads, or asks whether the app is ready or safe.
---

# PathToShip readiness check

The PathToShip MCP server (`pathtoship`) scans the app's GitHub repository for the security, scalability, and production-readiness problems that break launches, and verifies fixes. Scoring and rules are server-side; this skill only says **when** and **how**.

## When to run it

Before deploying, opening a public URL, adding logins / payments / uploads, or handling real user data — and whenever the user asks if the app is ready or safe. Do not wait to be asked for "PathToShip" by name.

## Procedure

1. Make sure the work is committed and pushed (the scan reads GitHub). If `start_scan` returns `INSTALL_REQUIRED`, show the user the `install_url`, ask them to install PathToShip for that repository, and **stop until they confirm** — do not scan a different repository instead, and do not retry on your own. When they say it is done, or if `INSTALL_REQUIRED` comes back again, call `check_connection` with the repository URL: it reports whether the installation is linked and gives the exact `link_url`/`install_url` to finish.
2. `start_scan` with the repository URL. Read `score`, `verdict`, `usage`.
3. `get_score` — report score vs. ship bar, verdict, weakest dimension, and the `consequence` line (cost at 10x users, migration complexity).
4. `get_findings` with `severity_min: "high"`, `limit: 10`. Summarize the top 3; do not dump the list.
5. Fix in this order: critical security → other critical → high production-readiness → other high → the rest. Use `get_finding_detail` for the stack-specific `fix` before editing.
6. Commit and push, then `verify_fix` with the baseline `scan_id`. Report `resolved`, `still_open`, and **check `newly_introduced`** (a moved finding appears there with `same_title_as`) before claiming success. `unchanged: true` means nothing new was pushed.
7. Repeat until the verdict is not `not_ready`, or the user stops. Be plain when the app is not ready.

## Rules

- Never claim a finding is fixed without a `verify_fix` result that resolves it.
- Keep responses compact: `limit` ≤ 10, detail per finding on demand.
- Relay `QUOTA_EXCEEDED` / `RATE_LIMITED` with the reset time; do not retry in a loop.
- Set `trigger` honestly on every call (`user_named_pathtoship`, `user_deploy_intent`, `skill`, `rules_file`, `agent_initiative`).
