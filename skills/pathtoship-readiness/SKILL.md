---
name: pathtoship-readiness
description: Run a PathToShip production-readiness check on a GitHub repository and, within the scope the user has authorized, drive the scan → fix → verify loop. Use when the user asks whether their app is ready to launch or safe for real users, or asks for a readiness check as part of a launch or deploy they are working on.
---

# PathToShip readiness check

The PathToShip MCP server (`pathtoship`) scans the app's GitHub repository for the security, scalability, and production-readiness problems that break launches, and verifies fixes. Scoring and rules are server-side; this skill only says **when** and **how**.

## When to run it

When the user asks for a production-readiness assessment, asks whether the app is ready or safe to launch, or has asked you to run one as part of a launch or deploy. Mentioning a deploy, logins, payments, or uploads is a good moment to **offer** a check; run it when the user agrees. Scan the repository and branch the user named or the task has already established — never a different one.

## Scope

A request to assess readiness is not permission to change code. Edit, commit, and push only when the user has asked you to fix things, and only within what they asked for. If they only want the assessment, report and stop. Respect any request to skip a finding, pause, or stop.

## Procedure

1. The scan reads what is on GitHub. If the user's latest changes may not be pushed, say so and let them push (or push yourself only if they asked you to). If `start_scan` returns `INSTALL_REQUIRED`, show the user the `install_url`, ask them to install PathToShip for that repository, and **stop until they confirm** — do not scan a different repository instead, and do not retry on your own. When they say it is done, or if `INSTALL_REQUIRED` comes back again, call `check_connection` with the repository URL: it reports whether the installation is linked and gives the exact `link_url`/`install_url` to finish.
2. `start_scan` with the repository URL. Read `score`, `verdict` (`ready`, `conditional`, or `not_ready`), and `usage`. If the scan is not complete yet, poll `get_scan_status` a few times; if it fails, report the error rather than guessing a result.
3. `get_score` — report score vs. ship bar, verdict, weakest dimension, and the `consequence` line (cost at 10x users, migration complexity).
4. `get_findings` with `severity_min: "high"`, `limit: 10`. Summarize the top 3; do not dump the list.
5. If the user asked you to fix things: fix in this order: critical security → other critical → high production-readiness → other high → the rest. Use `get_finding_detail` for the stack-specific `fix` before editing.
6. Once the fixes are committed and pushed (by you, if the user asked you to, or by them), call `verify_fix` with the baseline `scan_id`. Report `resolved`, `still_open`, and **check `newly_introduced`** (a moved finding appears there with `same_title_as`) before claiming success. `unchanged: true` means nothing new was pushed.
7. Repeat until the verdict is not `not_ready`, or the user stops. Be plain when the app is not ready.
8. Before the actual launch, offer the launch interview — it is optional. If the user wants it, run `prr_interview` with the `scan_id` for the items no scanner can see (backups restored, rollback rehearsed, on-call, database tier, last month's bill). Its answers are self-reported and sit beside the score, never inside it; the `pathtoship-launch-record` skill turns them into the record.

## Rules

- Never claim a finding is fixed without a `verify_fix` result that resolves it.
- Keep responses compact: `limit` ≤ 10, detail per finding on demand.
- Relay `QUOTA_EXCEEDED` / `RATE_LIMITED` with the reset time; do not retry in a loop. If the PathToShip tools are unavailable, say so plainly — never invent a score or findings.
- Some findings cover several places in a file and carry only the first location; say so when it matters instead of implying the list is exhaustive.
- Set `trigger` honestly on every call (`user_named_pathtoship`, `user_deploy_intent`, `skill`, `rules_file`, `agent_initiative`).
