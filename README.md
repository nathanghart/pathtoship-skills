# PathToShip skills

Companion instructions for the [PathToShip](https://pathtoship.com) MCP server — a production-readiness check for apps built with AI tools (Lovable, Bolt, v0, Replit, Base44, Cursor). The server scans the GitHub repository your builder syncs to and reports the security, scalability, and production-readiness problems that break launches, then verifies your fixes.

The MCP server gives your assistant the *ability* to run a check. These files give it the *reason to*: when to check (before launch, before a public URL, before real user data), how to fix in the right order, and how to verify honestly. They contain methodology only — scoring, rules, and thresholds live on the server.

## What's here

| Path | For | How to use |
|---|---|---|
| `chat/CHAT-INSTRUCTIONS.md` | Claude.ai (Projects) · ChatGPT (Projects / custom GPT) | Paste into the project's instructions after adding the PathToShip connector |
| `skills/pathtoship-readiness/SKILL.md` | Claude Code · Codex · any agent that reads `SKILL.md` | `npx skills add nathanghart/pathtoship-skills --skill pathtoship-readiness`, or copy into `.claude/skills/pathtoship-readiness/` |
| `skills/pathtoship-launch-record/SKILL.md` | Optional add-on | Keeps `docs/launch-readiness.md` — which checks ran on which commit, what was found, fixed, or accepted, plus the operational items no scanner sees. `npx skills add nathanghart/pathtoship-skills --skill pathtoship-launch-record` |
| `skills/pathtoship-badge/SKILL.md` | Optional add-on | Adds the live readiness badge to the README/site once a scan clears the ship bar. `npx skills add nathanghart/pathtoship-skills --skill pathtoship-badge` |
| `rules/CLAUDE.md.snippet` | Claude Code (`CLAUDE.md`), Codex (`AGENTS.md`) | Append to the file |
| `rules/pathtoship.mdc` | Cursor | Copy into `.cursor/rules/` |

## Connecting the server

**Claude.ai** (Pro/Max): Settings → Connectors → Add custom connector → URL `https://pathtoship.com/api/mcp`.
**ChatGPT** (Plus/Pro/Business): Settings → Apps & Connectors → Developer Mode → Create → same URL.
**Claude Code** (run on the machine where Claude Code runs — not in a remote/cloud session; on a phone use the Claude.ai connector): `claude mcp add --transport http pathtoship https://pathtoship.com/api/mcp --header "Authorization: Bearer <your API key>"`
**Cursor:** `.cursor/mcp.json` → `{ "mcpServers": { "pathtoship": { "url": "https://pathtoship.com/api/mcp", "headers": { "Authorization": "Bearer <your API key>" } } } }`

During the pilot, credentials (a connector URL or an API key) come from us. Self-service sign-up follows.

## The tools

`start_scan` · `get_score` · `get_findings` · `get_finding_detail` · `verify_fix` · `get_scan_status`

## Privacy

Read-only. Your repository is read through GitHub (a GitHub App installation you control for private repos), analyzed in memory, and only results, file names, and a content hash are kept.

## License

MIT — copy, adapt, and redistribute these instructions freely.
