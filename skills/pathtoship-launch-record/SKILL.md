---
name: pathtoship-launch-record
description: Keep a launch-readiness record in the repository — which PathToShip checks ran on which commit, what was found, fixed, or accepted, and the operational items no scanner can see. Use after a verify_fix, before a launch or client handoff, or when asked for an audit trail of readiness.
---

# Launch readiness record

A record, not a seal: it states what was checked on which commit and what happened — true on the day it is written and useful to whoever inherits, audits, or pays for the app. The scanner cannot see operations (backups, rollback, on-call), so the record also carries the human's own attestations.

## When

- After a `verify_fix` that resolves findings, or when the verdict reaches `ready`.
- Before a launch, a public URL, a client handoff, or a compliance/audit conversation.
- When the user asks "what did we check?", "is there a record?", or "what's still open?".

## Procedure

1. Get the facts from the tools — never from memory: `get_score` (score, verdict, ship bar, weakest dimension, consequence), `get_findings` with `severity_min: "medium"` for what remains, and the last `verify_fix` result (resolved / still open / newly introduced). Note the commit SHA the scan ran on (from the repository) and the `permalink`.
2. Run the launch interview through the tool, not from memory: call `prr_interview` with the `scan_id` to get the thirteen questions and what is already answered. In ChatGPT a form appears — let the user fill it in and save. Elsewhere, ask the unanswered questions briefly and record the answers with `prr_interview` (`answers` keyed by question id: `yes` / `no` / `na` for attestations, a number for rows and bills, short text otherwise; a `note` for context). The tool merges partial answers and returns the recorded state — copy attestations from that result, never from the conversation. Everything it holds is **self-reported** and is never part of the score.
3. For every remaining finding at medium or above, record one of: **fix planned** (by when), **accepted risk** (why, by whom), or **not applicable** (why). Never silently drop a finding.
4. Write or update `docs/launch-readiness.md` (create the folder if needed). Keep earlier entries; append the new one at the top. Format:

```markdown
# Launch readiness — <app name>

## <YYYY-MM-DD> — <milestone, e.g. "public beta">

- Commit: `<sha>` on `<branch>` · PathToShip scan `<scan_id>` · <permalink>
- Score: <before> → <after> (ship bar <ship_bar>) · Verdict: <verdict> · Weakest: <dimension>
- At 10x users: <consequence line from get_score>
- Resolved this round: <n> — <titles, one line each>
- Remaining (medium+): <title> — <fix planned by … | accepted risk: … (name) | n/a: …>
- Self-reported (from `prr_interview`, <date>): secrets store ✔/✘ · backups restored ✔/✘ · rollback rehearsed ✔/✘ · monitoring + on-call ✔/✘ · staging ✔/✘ · health checks + timeouts ✔/✘ · edge rate limiting ✔/✘ · load test ✔/✘ · tier <…> · largest table <n> rows · last month <$> · autoscale floor <…> · media via <…> (notes: …)
- Signed off by: <name>, <role>
```

5. Commit the record with the code it describes (`docs: launch readiness record <date>`), or hand it to the user to paste if you cannot commit.

## Rules

- Facts come from tool results; attestations come from the user through `prr_interview`; label which is which. `get_score` shows them under `self_reported` — quote that block, do not blend it into the score line.
- Never write "resolved" for anything `verify_fix` did not report as resolved.
- Do not restate scoring rules or thresholds — quote the tool's numbers.
- Keep it under a page per entry. It is a record people will actually read.
- Set `trigger: "skill"` on tool calls this skill initiates.
