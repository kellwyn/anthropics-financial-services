# AGENT.md — anthropics-financial-services

> Cross-agent reference. Claude Code reads `CLAUDE.md` which imports this file.
> Other agents (Cursor, Copilot, etc.) read this file directly.

Adapted from the canonical Dev-RZY ruleset (`c:\Users\kellw\OneDrive\Dev-RZY\AGENT.md`).
The Dev-RZY rules cover MetaTrader strategy projects; this file keeps the cross-cutting rules
(commit format, security, PR hygiene) and replaces the MT5-specific sections with the conventions
of this repo.

---

## Project Identity

Working directory `anthropics-financial-services/` is a personal vendoring + tailoring workspace
around Anthropic's open-source [`anthropics/financial-services`](https://github.com/anthropics/financial-services)
plugin set. Upstream content is committed under `financial-services/`; firm-specific tuning,
notes, and tooling live alongside it at the repo root.

```
anthropics-financial-services/      ← this repo (kellwyn/anthropics-financial-services)
├── AGENT.md                        ← cross-agent rules (this file)
├── CLAUDE.md                       ← Claude Code entrypoint, imports AGENT.md
├── financial-services/             ← vendored copy of anthropics/financial-services
│   ├── plugins/
│   │   ├── agent-plugins/          ← named end-to-end agents (Pitch Agent, GL Reconciler, …)
│   │   ├── vertical-plugins/       ← skill + command + MCP bundles per FSI vertical
│   │   └── partner-built/          ← LSEG, S&P Global partner plugins
│   ├── managed-agent-cookbooks/    ← Claude Managed Agent (CMA) deployment templates
│   ├── claude-for-msft-365-install/← Microsoft 365 add-in admin tooling
│   └── scripts/                    ← check.py, validate.py, deploy-managed-agent.sh, …
└── .gitignore
```

Upstream is the source of truth for plugin structure. Local edits should either (a) be tunings
that we plan to rebase forward when upstream changes, or (b) live outside `financial-services/`
so upstream pulls are clean.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Plugins | Markdown + JSON (`SKILL.md`, `plugin.json`, `marketplace.json`) — no build step |
| Slash commands | Markdown under `commands/<name>.md` |
| Connectors | MCP servers configured via `.mcp.json` |
| Managed Agents | YAML (`agent.yaml`, `subagents/*.yaml`) deployed via `/v1/agents` |
| Tooling scripts | Python 3.10+ (`check.py`, `validate.py`, `orchestrate.py`, `sync-agent-skills.py`) + bash (`deploy-managed-agent.sh`) |
| Runtime | Claude Code, Claude Cowork, or Anthropic Managed Agents API |

---

## Naming Conventions

- **Plugin slug**: lowercase-kebab (`pitch-agent`, `gl-reconciler`, `wealth-management`)
- **Skill ID**: lowercase-kebab matching directory name (`comps-analysis`, `dcf-model`)
- **Slash command**: `/<command>` (no plugin prefix in user-facing docs); files at `commands/<name>.md`
- **Plugin-prefixed invocation**: `/<plugin>:<command>` when disambiguation is needed
- **Env vars**: `UPPER_SNAKE_CASE` (`ANTHROPIC_API_KEY`)
- **MCP provider keys** in `.mcp.json`: lowercase, match brand (`daloopa`, `factset`, `lseg`, `pitchbook`)

---

## Dos & Don'ts

### DO
- Edit skills in `financial-services/plugins/vertical-plugins/<vertical>/skills/`, then run `python3 financial-services/scripts/sync-agent-skills.py` to propagate to agent bundles
- Run `python3 financial-services/scripts/check.py` before committing — it validates manifests, references, and skill drift
- Treat upstream `financial-services/` as a vendored snapshot; record the upstream commit when re-syncing
- Follow Apache-2.0 — keep the LICENSE intact and attribute upstream where redistributed
- Add `# Why:` comments only when the reason is non-obvious (per universal rule)
- Use `keyring` or env vars for any API key (Anthropic, MCP providers); never inline

### DON'T
- Don't hardcode `ANTHROPIC_API_KEY`, MCP provider keys, or firm credentials in any committed file
- Don't commit `.env` — only `.env.example` (with blank values) goes to git
- Don't drift `agent-plugins/<slug>/skills/` from `vertical-plugins/<vertical>/skills/` source — `check.py` will fail
- Don't push directly to `anthropics/financial-services` upstream — open a PR if contributing back
- Don't `git push --force` on `main` (this rule was bent once; do not normalize it)
- Don't amend published commits — create new ones
- Don't skip hooks (`--no-verify`) or bypass signing without explicit user instruction

---

## Security

Credentials are never in source. Priority order (mirrors Dev-RZY):

1. **Runtime**: `keyring.get_password(...)` for any password / persistent secret
2. **Config**: `.env` (gitignored) — non-sensitive params only (model name, provider URLs, slugs)
3. **Template**: `.env.example` — committed with blank credential fields

```python
# Correct credential access pattern
import keyring
api_key = keyring.get_password("anthropic", "api_key")
```

Store once:
```bash
python -c "import keyring; keyring.set_password('anthropic', 'api_key', 'sk-ant-…')"
```

**`.gitignore` must include:**
```
.env
*.local.md
__pycache__/
.venv/
```

When in doubt, run the security-reviewer subagent before pushing (`use the security-reviewer subagent`).

---

## Commands

```bash
# Lint + reference-check the whole plugin tree
python3 financial-services/scripts/check.py

# Sync vertical-plugin skills into agent-plugin bundles after edits
python3 financial-services/scripts/sync-agent-skills.py

# Deploy a named agent as a Claude Managed Agent
export ANTHROPIC_API_KEY=$(python -c "import keyring;print(keyring.get_password('anthropic','api_key'))")
financial-services/scripts/deploy-managed-agent.sh gl-reconciler

# Validate a single cookbook
python3 financial-services/scripts/validate.py managed-agent-cookbooks/<slug>

# Reference orchestrator event loop
python3 financial-services/scripts/orchestrate.py
```

---

## Versioning & Commit Format

**Commits**: `<short, imperative summary>` — one line, ≤72 chars.
Where versioning applies (e.g. firm-tuned plugin releases): `pitch-agent v1.02 — add LBO sensitivity table`.

**Co-author trailer (mandatory):** `Co-Authored-By: KSE`

Do NOT use the default `Co-Authored-By: Claude …` trailer — it has been explicitly overridden
for all of the user's repos.

Example:
```
Add anthropics/financial-services reference plugins

Vendored from upstream commit <sha>. Apache-2.0 retained.

Co-Authored-By: KSE
```

Increment minor for tuning / new skills; major for breaking restructures.

---

## PR Checklist

- [ ] `python3 financial-services/scripts/check.py` passes
- [ ] `sync-agent-skills.py` run if any `vertical-plugins/.../skills/` were edited
- [ ] No credentials, API keys, or firm-internal data in any committed file (`use the security-reviewer subagent`)
- [ ] Upstream `LICENSE` and attribution preserved
- [ ] Commit message uses `Co-Authored-By: KSE` trailer
- [ ] `MEMORY.md` updated if project context or conventions changed

---

## Subagents — Invoke When Relevant

| Task | Command |
|---|---|
| Review changed code or markdown | `use the code-reviewer subagent` |
| Security scan before commit | `use the security-reviewer subagent` |
| Broad codebase exploration | `use the Explore subagent` |
| Plan a non-trivial change | `use the Plan subagent` |

---

## Memory System

Memory lives at `C:\Users\kellw\.claude\projects\c--Users-kellw-OneDrive-Projects-anthropics-financial-services\memory\`,
with `MEMORY.md` as the index. Same structure as Dev-RZY — use feedback / project / reference / user
memory types. The KSE co-author rule is recorded there as a feedback memory.

---

## When Stuck

1. Read the upstream `financial-services/README.md` and `financial-services/CLAUDE.md` — they document the plugin model end-to-end
2. Check `financial-services/scripts/check.py` output for the actual contract being violated
3. For Claude Managed Agents deployment quirks, see `financial-services/managed-agent-cookbooks/<slug>/README.md`
4. MCP connector reference: `financial-services/plugins/vertical-plugins/financial-analysis/.mcp.json`
5. Upstream issues: https://github.com/anthropics/financial-services/issues
