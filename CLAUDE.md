@AGENT.md

<!-- Claude Code project instructions — imports AGENT.md for cross-agent compatibility -->

## Critical Rules

- **Co-author trailer is `Co-Authored-By: KSE`** — never the default Claude trailer
- Never hardcode credentials — use `keyring` for secrets, `.env` (gitignored) for non-sensitive config
- Don't drift `agent-plugins/<slug>/skills/` from the `vertical-plugins/<vertical>/skills/` source — edit the vertical, then run `sync-agent-skills.py`
- Run `python3 financial-services/scripts/check.py` before pushing — it lints manifests and resolves all references
- Don't `git push --force` on `main`; don't amend published commits — create new ones
- Treat `financial-services/` as vendored from upstream Apache-2.0; preserve `LICENSE` and attribution

## Commands

```bash
python3 financial-services/scripts/check.py                # lint + reference check
python3 financial-services/scripts/sync-agent-skills.py    # propagate vertical skills to agent bundles
financial-services/scripts/deploy-managed-agent.sh <slug>  # deploy named agent via Managed Agents API
```

## Verification — Required Before Marking Done

| Change | How to verify |
|---|---|
| Skill edit (vertical) | Run `sync-agent-skills.py`, then `check.py` — both must pass |
| Agent / cookbook edit | `python3 financial-services/scripts/validate.py managed-agent-cookbooks/<slug>` |
| MCP connector change | Confirm provider URL responds and `.mcp.json` schema validates |
| Credential/env change | `python -c "import keyring; print(keyring.get_password(<service>, <key>))"` |
| New file at repo root | Confirm it's referenced from `AGENT.md` or this file |

## Subagents — Invoke When Relevant

| Task | Command |
|---|---|
| Review changed code or markdown | `use the code-reviewer subagent` |
| Security scan before commit | `use the security-reviewer subagent` |
| Broad codebase exploration | `use the Explore subagent` |
| Plan a non-trivial change | `use the Plan subagent` |

## When Compacting

Preserve: vendored upstream commit SHA, list of locally-modified files under `financial-services/`,
any in-flight `check.py` failures, the active agent/cookbook slug being worked on.

## Non-Obvious Behaviours

- `financial-services/` was vendored by removing the inner `.git/` and committing files directly — there is no upstream remote tracked here. Re-syncing means re-cloning into a temp dir and copying over.
- The Apache-2.0 LICENSE inside `financial-services/` covers upstream content only — repo-root files (this one, AGENT.md) are not bound by it.
- `MEMORY.md` is loaded into every Claude Code session — keep it short (≤200 lines) and use it only as an index.
- The default Claude commit trailer is wrong here. Override every time. (See [feedback memory](C:\Users\kellw\.claude\projects\c--Users-kellw-OneDrive-Projects-anthropics-financial-services\memory\feedback_commit_coauthor.md).)
