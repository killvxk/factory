---
name: factory-health
description: "Audits Factory Codex plugin configuration for correctness, security, and effectiveness. Use when Codex ignores instructions, hooks malfunction, MCP needs auditing, or setup feels off. Triggers: health, check my setup, audit config, is my Codex setup right, something feels off."
---

# Factory Health Skill - Is Your Setup Working For You?

## Goal

Audit the Factory Codex setup and report issues by severity.
Optionally compute a weighted 0-10 code quality score.

## Constraints

- Never fabricate PASS items without evidence (run the actual checks)
- Credentials MUST be redacted before any agent receives data
- If a tool is unavailable (jq, python3), mark that area as [INSUFFICIENT DATA], not a finding
- Do not auto-apply fixes without user confirmation
- Treat repo-local Codex surfaces as the source of truth: `plugins/factory/.codex-plugin/plugin.json`, `.agents/plugins/marketplace.json`, `plugins/factory/skills/`, `plugins/factory/commands/` (legacy prompt wrappers only), `docs/memory/CODEX.local.md`, `.factory/learnings.jsonl`, and `docs/solutions/`

## Six-Layer Audit

1. **Plugin manifest** - Does `plugins/factory/.codex-plugin/plugin.json` exist? Is it valid and aligned with the published skill surface?
2. **Marketplace and install path** - Does `.agents/plugins/marketplace.json` point at the repo-local plugin? Is the expected install/discovery path documented correctly?
3. **Skills and legacy prompt docs** - Are the expected skills present? Are descriptions consistent? Do any legacy docs still claim unsupported Claude-style command paths?
4. **Hooks and runtime config** - Are hooks, MCP, and app/config files valid? Are scripts executable? Are any secrets exposed?
5. **Memory and knowledge** - Is `docs/memory/CODEX.local.md` present and current? Is `.factory/learnings.jsonl` valid JSONL? Are `docs/solutions/` entries still accurate?
6. **Behavior** - Recent Codex sessions: does the plugin follow its own instructions, skill flow, and verification posture?

## Tier Detection

| Tier | Signal | Expected Setup |
|------|--------|----------------|
| Simple | <500 files, 1 contributor, no CI | plugin manifest + core skills |
| Standard | 500-5K files, small team or CI | plugin manifest + skills + memory surfaces |
| Complex | >5K files, multi-contributor, CI | full Codex plugin surface and verification loop |

## Data Collection

Single shell block gathers: file count, plugin manifest, marketplace entry, command inventory, skill inventory, hooks/config, memory files, and startup context estimate (words * 1.3 tokens).

## Dual-Agent Audit (Standard/Complex tiers)

For Standard and Complex projects, dispatch TWO parallel agents for independent analysis:

**Agent 1 - Context + Security:**
- Plugin manifest quality and contradictions
- Marketplace placement and install path
- Skill descriptions and any legacy prompt-doc naming
- Memory surface hygiene: `docs/memory/CODEX.local.md`, `.factory/learnings.jsonl`, `docs/solutions/`
- MCP / hooks / app config token overhead and exposure
- Startup context budget (>30K tokens before first message = HIGH, >6 servers = flag)
- Skill security scan: prompt injection, data exfiltration, destructive commands, hardcoded credentials, obfuscation, safety override instructions

**Agent 2 - Control + Behavior:**
- hooks.json schema validation
- allowedTools hygiene (only genuinely dangerous ops)
- Credential exposure in config files
- Model/config validation for the current Codex environment
- Three-layer defense consistency (intent / knowledge / enforcement)
- Conversation and skill-invocation behavior patterns from recent sessions

Credentials MUST be redacted before any agent receives data.

For Simple projects, run all checks locally (no subagents needed).

## Severity Classification

- **[!] Critical** - Fix now. The setup is actively misbehaving or insecure.
- **[~] Structural** - Fix soon. Setup works but has gaps.
- **[-] Incremental** - Nice to have. Polish items.

## Three-Layer Defense Check

A critical rule should exist in all three layers:
- Plugin or project instructions declare intent
- A skill teaches the method
- A hook enforces deterministically

Single-layer rules are flagged as fragile.

## Code Quality Score (Optional)

Weighted 0-10 score across:
- Type checker (25%): `tsc --noEmit` / `cargo check` / `mypy`
- Linter (20%): `eslint` / `clippy` / `ruff`
- Test runner (30%): `npm test` / `cargo test` / `pytest`
- Dead code (15%): unused exports, unreachable branches
- Shell lint (10%): `shellcheck` on any .sh files

## Verification

Before closing, compare the audit findings against actual file contents and runtime checks; do not report anything as healthy without evidence.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "The setup looks fine from memory" | Run the data collection. Memory is not evidence. |
| "MCP overhead doesn't matter" | Context overhead is real; measure it before dismissing it. |
| "Only Complex projects need this" | Simple projects benefit most - one bad plugin surface wastes every session. |
| "Hooks are working, I can tell" | Can you? Run the schema check. Silent failures are the worst kind. |
| "This finding is too minor to report" | Report it as [-] incremental. Let the user decide. |

## Gotchas

- `jq` not installed: conversation extraction fails silently - treat as insufficient data
- `python3` not on PATH: MCP checks print "(unavailable)" - don't flag those
- Installed skills or commands may need a Codex restart or plugin reload before they appear
- `.next/`, `__pycache__/`, `.turbo/` inflate file counts - exclude generated dirs
- settings.local.json absent is normal for global-settings-only projects
