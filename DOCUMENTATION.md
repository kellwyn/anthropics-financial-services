# DOCUMENTATION — anthropics/financial-services

Reference catalogue of every named agent and skill in the vendored
[`financial-services/`](financial-services/) tree. Source of truth for skills is
`plugins/vertical-plugins/<vertical>/skills/`; agents bundle synced copies of
the skills they need under `plugins/agent-plugins/<slug>/skills/`.

- **Agents (named, end-to-end workflows):** 10
- **Skills (canonical sources):** 66 — 53 in 7 first-party verticals + 13 across 2 partners
- **Slash commands:** ~50 (one per skill that exposes a `/`-trigger)
- **MCP connectors:** 11 third-party data providers (centralised in `financial-analysis`)

Each agent ships **two ways from one source**:

- Cowork plugin → `plugins/agent-plugins/<slug>/`
- Claude Managed Agent cookbook → `managed-agent-cookbooks/<slug>/agent.yaml`

Both reference the same `agents/<slug>.md` system prompt.

---

## 1. Named agents (10)

| Agent | Plugin | Cookbook | Purpose |
|---|---|---|---|
| **Earnings Reviewer** | [agent-plugins/earnings-reviewer](financial-services/plugins/agent-plugins/earnings-reviewer/agents/earnings-reviewer.md) | [cookbook](financial-services/managed-agent-cookbooks/earnings-reviewer/agent.yaml) | Reads call transcript + filings, updates the coverage model, drafts the post-earnings note. Single-name interactively or fanned out across coverage. |
| **GL Reconciler** | [agent-plugins/gl-reconciler](financial-services/plugins/agent-plugins/gl-reconciler/agents/gl-reconciler.md) | [cookbook](financial-services/managed-agent-cookbooks/gl-reconciler/agent.yaml) | Reconciles GL → subledger across asset classes for a trade date; finds breaks, traces root cause, routes for sign-off. (Not for posting JEs — use Month-End Closer.) |
| **KYC Screener** | [agent-plugins/kyc-screener](financial-services/plugins/agent-plugins/kyc-screener/agents/kyc-screener.md) | [cookbook](financial-services/managed-agent-cookbooks/kyc-screener/agent.yaml) | Parses onboarding packet, runs KYC/AML rules, screens sanctions/PEP, flags gaps. New-client or periodic refresh — not transaction monitoring. |
| **Market Researcher** | [agent-plugins/market-researcher](financial-services/plugins/agent-plugins/market-researcher/agents/market-researcher.md) | [cookbook](financial-services/managed-agent-cookbooks/market-researcher/agent.yaml) | Sector/thematic research — industry overview, competitive landscape, peer comps, ideas shortlist, optional slides. (Not for single-name updates — use Earnings Reviewer.) |
| **Meeting Prep Agent** | [agent-plugins/meeting-prep-agent](financial-services/plugins/agent-plugins/meeting-prep-agent/agents/meeting-prep-agent.md) | [cookbook](financial-services/managed-agent-cookbooks/meeting-prep-agent/agent.yaml) | Briefing pack before a client meeting — CRM history, holdings, recent activity, market context, suggested agenda. |
| **Model Builder** | [agent-plugins/model-builder](financial-services/plugins/agent-plugins/model-builder/agents/model-builder.md) | [cookbook](financial-services/managed-agent-cookbooks/model-builder/agent.yaml) | Builds DCF / LBO / 3-statement / comps models live in Excel from a ticker + assumptions. Clean models from scratch. (Not for updating existing — use Earnings Reviewer.) |
| **Month-End Closer** | [agent-plugins/month-end-closer](financial-services/plugins/agent-plugins/month-end-closer/agents/month-end-closer.md) | [cookbook](financial-services/managed-agent-cookbooks/month-end-closer/agent.yaml) | Runs entity month-end — accruals, roll-forwards, variance commentary; stages close package for controller sign-off. (Not for daily recon — use GL Reconciler.) |
| **Pitch Agent** | [agent-plugins/pitch-agent](financial-services/plugins/agent-plugins/pitch-agent/agents/pitch-agent.md) | [cookbook](financial-services/managed-agent-cookbooks/pitch-agent/agent.yaml) | End-to-end IB pitch — pulls comps/precedents, builds DCF + football-field, generates branded pitch deck. First-draft pitch on a name. (Not for editing existing decks — use the `pitch-deck` skill directly.) |
| **Statement Auditor** | [agent-plugins/statement-auditor](financial-services/plugins/agent-plugins/statement-auditor/agents/statement-auditor.md) | [cookbook](financial-services/managed-agent-cookbooks/statement-auditor/agent.yaml) | Audits pre-generated LP capital-account statements against the fund NAV pack before distribution — ties out balances, allocations, fees. Final check before send. |
| **Valuation Reviewer** | [agent-plugins/valuation-reviewer](financial-services/plugins/agent-plugins/valuation-reviewer/agents/valuation-reviewer.md) | [cookbook](financial-services/managed-agent-cookbooks/valuation-reviewer/agent.yaml) | Ingests GP valuation packages, runs the valuation template, stages LP reporting. Quarter-end portfolio valuation review. (Not for deal-time underwriting — use Model Builder.) |

### Agent → skill bundle map

Each named agent bundles a sync'd copy of these source skills:

| Agent | Bundled skills |
|---|---|
| Earnings Reviewer | `audit-xls`, `earnings-analysis`, `earnings-preview`, `model-update`, `morning-note`, `xlsx-author` |
| GL Reconciler | `audit-xls`, `break-trace`, `gl-recon`, `xlsx-author` |
| KYC Screener | `kyc-doc-parse`, `kyc-rules`, `xlsx-author` |
| Market Researcher | `competitive-analysis`, `comps-analysis`, `idea-generation`, `pptx-author`, `sector-overview` |
| Meeting Prep Agent | `client-report`, `client-review`, `investment-proposal`, `pptx-author` |
| Model Builder | `3-statement-model`, `audit-xls`, `comps-analysis`, `dcf-model`, `lbo-model`, `xlsx-author` |
| Month-End Closer | `accrual-schedule`, `audit-xls`, `roll-forward`, `variance-commentary`, `xlsx-author` |
| Pitch Agent | `3-statement-model`, `audit-xls`, `comps-analysis`, `dcf-model`, `deck-refresh`, `ib-check-deck`, `lbo-model`, `pitch-deck`, `pptx-author`, `sector-overview`, `xlsx-author` |
| Statement Auditor | `audit-xls`, `nav-tieout`, `xlsx-author` |
| Valuation Reviewer | `ic-memo`, `portfolio-monitoring`, `returns-analysis`, `xlsx-author` |

---

## 2. Skills by vertical

### 2.1 financial-analysis (core — 13 skills)

Shared modeling, Excel, and deck-QC primitives. Every other vertical and most agents pull from here.

| Skill | Slash | Purpose |
|---|---|---|
| [3-statement-model](financial-services/plugins/vertical-plugins/financial-analysis/skills/3-statement-model/SKILL.md) | `/3-statement-model` | Populate IS/BS/CF templates with linked, integrated statements. |
| [audit-xls](financial-services/plugins/vertical-plugins/financial-analysis/skills/audit-xls/SKILL.md) | `/debug-model` | Audit a sheet/range/model — formulas, errors, BS balance, cash tie-out, sanity checks. |
| [clean-data-xls](financial-services/plugins/vertical-plugins/financial-analysis/skills/clean-data-xls/SKILL.md) | — | Trim, dedupe, standardize dates, fix numbers-as-text, flag mixed columns. |
| [competitive-analysis](financial-services/plugins/vertical-plugins/financial-analysis/skills/competitive-analysis/SKILL.md) | `/competitive-analysis` | Competitive landscape decks — positioning, peer deep-dives, market maps. |
| [comps-analysis](financial-services/plugins/vertical-plugins/financial-analysis/skills/comps-analysis/SKILL.md) | `/comps` | Public-company trading comps with operating metrics, multiples, statistical benchmarking. |
| [dcf-model](financial-services/plugins/vertical-plugins/financial-analysis/skills/dcf-model/SKILL.md) | `/dcf` | DCF valuation — projections, WACC, sensitivity, executive summary. |
| [deck-refresh](financial-services/plugins/vertical-plugins/financial-analysis/skills/deck-refresh/SKILL.md) | — | Roll-forward / re-link numbers across an existing deck without rebuilding. |
| [ib-check-deck](financial-services/plugins/vertical-plugins/financial-analysis/skills/ib-check-deck/SKILL.md) | — | QC a pitch deck — number consistency, narrative alignment, IB language polish. |
| [lbo-model](financial-services/plugins/vertical-plugins/financial-analysis/skills/lbo-model/SKILL.md) | `/lbo` | Fill in LBO templates — formulas, validation, IB-grade formatting. |
| [ppt-template-creator](financial-services/plugins/vertical-plugins/financial-analysis/skills/ppt-template-creator/SKILL.md) | `/ppt-template` | Convert a user's PPT template into a reusable PPT-template skill. |
| [pptx-author](financial-services/plugins/vertical-plugins/financial-analysis/skills/pptx-author/SKILL.md) | — | Headlessly produce a `.pptx` (for managed-agent runs with no live Office). |
| [skill-creator](financial-services/plugins/vertical-plugins/financial-analysis/skills/skill-creator/SKILL.md) | — | Guide for authoring/updating skills. |
| [xlsx-author](financial-services/plugins/vertical-plugins/financial-analysis/skills/xlsx-author/SKILL.md) | — | Headlessly produce a `.xlsx`. |

### 2.2 equity-research (9)

| Skill | Slash | Purpose |
|---|---|---|
| [catalyst-calendar](financial-services/plugins/vertical-plugins/equity-research/skills/catalyst-calendar/SKILL.md) | `/catalysts` | Calendar of upcoming catalysts (earnings, conferences, regulatory, macro) across coverage. |
| [earnings-analysis](financial-services/plugins/vertical-plugins/equity-research/skills/earnings-analysis/SKILL.md) | `/earnings` | 8–12 page post-earnings update — beat/miss, key metrics, revised estimates and thesis. |
| [earnings-preview](financial-services/plugins/vertical-plugins/equity-research/skills/earnings-preview/SKILL.md) | `/earnings-preview` | Pre-earnings model + bull/bear scenarios + what-to-watch metrics. |
| [idea-generation](financial-services/plugins/vertical-plugins/equity-research/skills/idea-generation/SKILL.md) | `/screen` | Quant + thematic stock screens to surface new long/short ideas. |
| [initiating-coverage](financial-services/plugins/vertical-plugins/equity-research/skills/initiating-coverage/SKILL.md) | `/initiate` | 5-task workflow → institutional initiation report (research → model → valuation → charts → assembly). |
| [model-update](financial-services/plugins/vertical-plugins/equity-research/skills/model-update/SKILL.md) | `/model-update` | Plug new earnings / guidance / macro into an existing model; flag material changes. |
| [morning-note](financial-services/plugins/vertical-plugins/equity-research/skills/morning-note/SKILL.md) | `/morning-note` | Tight 7am morning-meeting note — overnight moves, ideas, events. |
| [sector-overview](financial-services/plugins/vertical-plugins/equity-research/skills/sector-overview/SKILL.md) | `/sector` | Industry/sector landscape — dynamics, players, themes. |
| [thesis-tracker](financial-services/plugins/vertical-plugins/equity-research/skills/thesis-tracker/SKILL.md) | `/thesis` | Maintain investment theses with new data points and milestones. |

### 2.3 investment-banking (9)

| Skill | Slash | Purpose |
|---|---|---|
| [buyer-list](financial-services/plugins/vertical-plugins/investment-banking/skills/buyer-list/SKILL.md) | `/buyer-list` | Strategic + financial buyer universe for sell-side mandates. |
| [cim-builder](financial-services/plugins/vertical-plugins/investment-banking/skills/cim-builder/SKILL.md) | `/cim` | Draft Confidential Information Memorandums for sell-side processes. |
| [datapack-builder](financial-services/plugins/vertical-plugins/investment-banking/skills/datapack-builder/SKILL.md) | — | Extract + normalize financials from CIMs/SEC/MCP into IC-ready Excel data packs. |
| [deal-tracker](financial-services/plugins/vertical-plugins/investment-banking/skills/deal-tracker/SKILL.md) | `/deal-tracker` | Live-deal pipeline — milestones, deadlines, action items. |
| [merger-model](financial-services/plugins/vertical-plugins/investment-banking/skills/merger-model/SKILL.md) | `/merger-model` | Pro-forma EPS, accretion/dilution, synergy + PPA. |
| [pitch-deck](financial-services/plugins/vertical-plugins/investment-banking/skills/pitch-deck/SKILL.md) | — | Populate IB pitch templates from source data (not from scratch). |
| [process-letter](financial-services/plugins/vertical-plugins/investment-banking/skills/process-letter/SKILL.md) | `/process-letter` | IOI / final-bid / management-meeting process letters. |
| [strip-profile](financial-services/plugins/vertical-plugins/investment-banking/skills/strip-profile/SKILL.md) | `/one-pager` | 1–4 slide company strip profiles for pitch books / client decks. |
| [teaser](financial-services/plugins/vertical-plugins/investment-banking/skills/teaser/SKILL.md) | `/teaser` | Anonymous one-page sell-side teaser, pre-NDA. |

### 2.4 private-equity (10)

| Skill | Slash | Purpose |
|---|---|---|
| [ai-readiness](financial-services/plugins/vertical-plugins/private-equity/skills/ai-readiness/SKILL.md) | `/ai-readiness` | Rank AI quick wins across the portfolio for operating-partner deployment. |
| [dd-checklist](financial-services/plugins/vertical-plugins/private-equity/skills/dd-checklist/SKILL.md) | `/dd-checklist` | Workstream-tailored due-diligence checklists with status + red flags. |
| [dd-meeting-prep](financial-services/plugins/vertical-plugins/private-equity/skills/dd-meeting-prep/SKILL.md) | `/dd-prep` | Question lists / benchmarks / red flags for management, expert, and customer calls. |
| [deal-screening](financial-services/plugins/vertical-plugins/private-equity/skills/deal-screening/SKILL.md) | `/screen-deal` | Quick pass/fail on inbound CIMs and teasers — one-page screening memo. |
| [deal-sourcing](financial-services/plugins/vertical-plugins/private-equity/skills/deal-sourcing/SKILL.md) | `/source` | Find target companies, check CRM, draft personalized founder outreach. |
| [ic-memo](financial-services/plugins/vertical-plugins/private-equity/skills/ic-memo/SKILL.md) | `/ic-memo` | Draft structured IC memo synthesizing diligence + financials + terms. |
| [portfolio-monitoring](financial-services/plugins/vertical-plugins/private-equity/skills/portfolio-monitoring/SKILL.md) | `/portfolio` | Ingest portco financial packages, extract KPIs, flag variance, build dashboards. |
| [returns-analysis](financial-services/plugins/vertical-plugins/private-equity/skills/returns-analysis/SKILL.md) | `/returns` | IRR/MOIC sensitivity tables across entry, leverage, exit, growth, hold. |
| [unit-economics](financial-services/plugins/vertical-plugins/private-equity/skills/unit-economics/SKILL.md) | `/unit-economics` | ARR cohorts, LTV/CAC, net retention, payback, revenue quality. |
| [value-creation-plan](financial-services/plugins/vertical-plugins/private-equity/skills/value-creation-plan/SKILL.md) | `/value-creation` | Post-close 100-day plan, EBITDA bridge, KPI/accountability framework. |

### 2.5 wealth-management (6)

| Skill | Slash | Purpose |
|---|---|---|
| [client-report](financial-services/plugins/vertical-plugins/wealth-management/skills/client-report/SKILL.md) | `/client-report` | Quarterly/annual client-facing performance + allocation + commentary. |
| [client-review](financial-services/plugins/vertical-plugins/wealth-management/skills/client-review/SKILL.md) | `/client-review` | Meeting-ready review pack — performance, allocation, talking points, action items. |
| [financial-plan](financial-services/plugins/vertical-plugins/wealth-management/skills/financial-plan/SKILL.md) | `/financial-plan` | Retirement / education / estate / cash-flow planning + scenarios. |
| [investment-proposal](financial-services/plugins/vertical-plugins/wealth-management/skills/investment-proposal/SKILL.md) | `/proposal` | Prospect proposal — approach, allocation, expected outcomes, fees. |
| [portfolio-rebalance](financial-services/plugins/vertical-plugins/wealth-management/skills/portfolio-rebalance/SKILL.md) | `/rebalance` | Drift analysis + tax-aware rebalance trades across accounts. |
| [tax-loss-harvesting](financial-services/plugins/vertical-plugins/wealth-management/skills/tax-loss-harvesting/SKILL.md) | `/tlh` | Surface TLH opportunities, suggest replacements, track wash-sale windows. |

### 2.6 fund-admin (6)

| Skill | Slash | Purpose |
|---|---|---|
| [accrual-schedule](financial-services/plugins/vertical-plugins/fund-admin/skills/accrual-schedule/SKILL.md) | — | Period-end accruals — compute entry, cite support, draft JE for controller approval. |
| [break-trace](financial-services/plugins/vertical-plugins/fund-admin/skills/break-trace/SKILL.md) | — | Trace a recon break to its source transaction; explain what differs and why. |
| [gl-recon](financial-services/plugins/vertical-plugins/fund-admin/skills/gl-recon/SKILL.md) | — | Match GL ↔ subledger at position/transaction level, surface and classify breaks. |
| [nav-tieout](financial-services/plugins/vertical-plugins/fund-admin/skills/nav-tieout/SKILL.md) | — | Recompute LP capital account from NAV components; flag any non-tie line. |
| [roll-forward](financial-services/plugins/vertical-plugins/fund-admin/skills/roll-forward/SKILL.md) | — | BoP + activity − reversals = EoP roll-forward, each component tied to GL. |
| [variance-commentary](financial-services/plugins/vertical-plugins/fund-admin/skills/variance-commentary/SKILL.md) | — | P&L / BS flux commentary vs prior period and budget, drivers explained. |

### 2.7 operations (2)

| Skill | Slash | Purpose |
|---|---|---|
| [kyc-doc-parse](financial-services/plugins/vertical-plugins/operations/skills/kyc-doc-parse/SKILL.md) | — | Parse onboarding packet → structured KYC fields (identity, ownership, control, SoF). |
| [kyc-rules](financial-services/plugins/vertical-plugins/operations/skills/kyc-rules/SKILL.md) | — | Apply KYC/AML rules grid; assign risk rating; cite each rule outcome; flag escalations. |

---

## 3. Partner-built skills (11)

Plugins maintained by external data providers; require that provider's MCP / API access.

### 3.1 LSEG (8)

| Skill | Purpose |
|---|---|
| [bond-futures-basis](financial-services/plugins/partner-built/lseg/skills/bond-futures-basis/SKILL.md) | Price bond futures, identify CTD, compute implied repo, evaluate basis trades. |
| [bond-relative-value](financial-services/plugins/partner-built/lseg/skills/bond-relative-value/SKILL.md) | RV across pricing, curves, credit spreads, rate-shock scenarios. |
| [equity-research](financial-services/plugins/partner-built/lseg/skills/equity-research/SKILL.md) | LSEG equity snapshots — consensus, fundamentals, prices, macro context. |
| [fixed-income-portfolio](financial-services/plugins/partner-built/lseg/skills/fixed-income-portfolio/SKILL.md) | Portfolio pricing, duration/DV01, cashflow waterfall, rate stress. |
| [fx-carry-trade](financial-services/plugins/partner-built/lseg/skills/fx-carry-trade/SKILL.md) | Spot + forwards + IRD + vol surface → carry-to-vol assessment. |
| [macro-rates-monitor](financial-services/plugins/partner-built/lseg/skills/macro-rates-monitor/SKILL.md) | Macro/rates dashboards — curves, breakevens, swap rates, financial conditions. |
| [option-vol-analysis](financial-services/plugins/partner-built/lseg/skills/option-vol-analysis/SKILL.md) | Vol surface + Greeks + implied vs realized; vol-trade evaluation. |
| [swap-curve-strategy](financial-services/plugins/partner-built/lseg/skills/swap-curve-strategy/SKILL.md) | Multi-tenor swap pricing; gov + inflation overlays; steepener/flattener/butterfly ideas. |

### 3.2 S&P Global (3)

| Skill | Purpose |
|---|---|
| [earnings-preview-beta](financial-services/plugins/partner-built/spglobal/skills/earnings-preview-beta/SKILL.md) | 4–5 page single-name earnings preview HTML — transcript, peers, valuation, news. |
| [funding-digest](financial-services/plugins/partner-built/spglobal/skills/funding-digest/SKILL.md) | One-page PPTX recap of recent funding rounds + capital markets activity. |
| [tear-sheet](financial-services/plugins/partner-built/spglobal/skills/tear-sheet/SKILL.md) | Company tear sheet via Kensho/Capital IQ MCP — public or private, audience-tunable. |

---

## 4. Audience matrix — who each agent serves

| User type | Served? | By |
|---|---|---|
| **Investment bankers** (sell-side advisory) | ✅ primary | Pitch Agent, Meeting Prep, Model Builder, Market Researcher |
| **Equity research analysts** (sell-side) | ✅ primary | Earnings Reviewer, Market Researcher, Model Builder |
| **Private equity firms** | ✅ primary | Pitch (LBO), Model Builder, Valuation Reviewer, Meeting Prep, Market Researcher |
| **VC firms** | ◐ partial | `private-equity` vertical (deal-sourcing, dd-checklist, ic-memo, portfolio-monitoring) + PitchBook MCP |
| **Hedge funds** | ◐ partial | Earnings Reviewer, Market Researcher, Model Builder, GL Reconciler, Month-End Closer (no execution / signals) |
| **Institutional LPs / fund admins** | ✅ primary | Valuation Reviewer, GL Reconciler, Month-End Closer, Statement Auditor, KYC Screener |
| **Wealth managers / RIAs** | ✅ primary | `wealth-management` vertical (client-review, financial-plan, rebalance, TLH); Meeting Prep agent reusable |
| **Retail investors** | ❌ | — |
| **Day traders** | ❌ | — (no real-time execution, no intraday signals) |
| **Options traders** | ❌ | — (LSEG vol-analysis skill is institutional context, not a trading agent) |

---

## 5. MCP connectors (11)

Centralised in [`financial-services/plugins/vertical-plugins/financial-analysis/.mcp.json`](financial-services/plugins/vertical-plugins/financial-analysis/.mcp.json) and shared by every other plugin. Each requires the provider's own subscription / API key.

| Provider | URL |
|---|---|
| Daloopa | `https://mcp.daloopa.com/server/mcp` |
| Morningstar | `https://mcp.morningstar.com/mcp` |
| S&P Global (Kensho) | `https://kfinance.kensho.com/integrations/mcp` |
| FactSet | `https://mcp.factset.com/mcp` |
| Moody's | `https://api.moodys.com/genai-ready-data/m1/mcp` |
| MT Newswires | `https://vast-mcp.blueskyapi.com/mtnewswires` |
| Aiera | `https://mcp-pub.aiera.com` |
| LSEG | `https://api.analytics.lseg.com/lfa/mcp` |
| PitchBook | `https://premium.mcp.pitchbook.com/mcp` |
| Chronograph | `https://ai.chronograph.pe/mcp` |
| Egnyte | `https://mcp-server.egnyte.com/mcp` |

---

## 6. What runs without paid third-party data

Every agent still requires a Claude runtime (Claude Code, Cowork, or Anthropic API credits — none of those are free). Beyond that:

| Agent | Works on local files only? |
|---|---|
| GL Reconciler | ✅ — operates on your GL exports and sub-ledger files |
| Month-End Closer | ✅ — your trial balance + prior period |
| Statement Auditor | ✅ — your draft LP statements + NAV pack |
| KYC Screener | ✅ — your onboarding PDFs + rules grid |
| Model Builder | ✅ — works fine if you key in fundamentals manually |
| Earnings Reviewer | ⚠️ — needs Aiera (transcripts) and FactSet (estimates) to be useful |
| Market Researcher | ⚠️ — needs FactSet / CapIQ / Daloopa for comps and sector data |
| Pitch Agent | ⚠️ — comps, precedents, and LBO inputs typically come from FactSet / S&P / PitchBook |
| Valuation Reviewer | ⚠️ — designed around Chronograph for PE portfolio data |
| Meeting Prep Agent | ⚠️ — pulls client/portfolio context from CRM + market data |

---

## 7. Repository layout (vendored from upstream)

```
financial-services/
├── plugins/
│   ├── agent-plugins/<slug>/
│   │   ├── .claude-plugin/plugin.json
│   │   ├── agents/<slug>.md         ← canonical system prompt
│   │   └── skills/                  ← bundled copies, sync'd from vertical-plugins/
│   ├── vertical-plugins/<vertical>/
│   │   ├── .claude-plugin/plugin.json
│   │   ├── commands/                ← /comps, /dcf, /earnings, /ic-memo, …
│   │   ├── skills/                  ← canonical skill sources
│   │   └── .mcp.json                ← (financial-analysis only) all 11 connectors
│   └── partner-built/<partner>/     ← LSEG, S&P Global
├── managed-agent-cookbooks/<slug>/
│   ├── agent.yaml                   ← references ../../plugins/agent-plugins/<slug>/...
│   ├── subagents/*.yaml             ← depth-1 leaf workers
│   └── steering-examples.json
├── claude-for-msft-365-install/     ← Microsoft 365 add-in admin tooling
└── scripts/
    ├── check.py                     ← lint manifests + verify references resolve
    ├── validate.py                  ← validate a single cookbook
    ├── sync-agent-skills.py         ← propagate vertical skills into agent bundles
    ├── deploy-managed-agent.sh      ← POST agent.yaml to /v1/agents
    └── orchestrate.py               ← reference event loop for handoffs
```

---

## 8. Provenance

This file is generated by reading `description:` frontmatter from each `SKILL.md`
and `agents/<slug>.md` in `financial-services/`. To re-collate after upstream
changes, re-run the extraction and update sections 1–3 only — section 4
(audience), section 6 (paid-data dependencies), and the framing prose are
editorial and may need a manual pass.

Upstream: <https://github.com/anthropics/financial-services> · Apache-2.0.
