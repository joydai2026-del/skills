---
name: skill-scan
description: >-
  Security pre-screen for a skill, SKILL.md, or MCP server before installing it: static SkillSpector scan in Docker, 0-100 risk, safe / caution / do-not-install. Use when: "scan this skill", "is this SKILL.md safe", "skill scan", "check this skill before I install it", "scan this MCP server", "is this agent skill malicious", "pre-screen this skill", "audit our own skills". PROACTIVE before installing ANY external skill or MCP server.
---

<!-- Round-0 pre-screen. Drives NVIDIA SkillSpector. Static --no-llm only. -->

# Skill Scan (Round 0 pre-screen)

Answers one question fast: **"Is this skill safe to install?"** It runs a purpose-built scanner over a `SKILL.md` / skill folder / MCP server and flags the skill-specific threats a normal code read misses: prompt injection hidden in the prose, MCP tool-poisoning, a skill that reaches more than it declares, a skill that reads your credentials, and the classic "reads a secret, posts it to a webhook" payload.

It is a pre-filter. A clean scan does not skip the house rule two-round gate for anything you did not write yourself. A dirty scan stops the install right there.

## How to run it (the only command you need)

The scanner runs in a throwaway Docker container. Static analysis only (`--no-llm`): no API keys, nothing leaves the machine except an anonymous package-name lookup to the public CVE database. The target is mounted **read-only**.

**Scan a local skill folder or a single SKILL.md:**
```bash
# Point TARGET at the skill's directory (or the SKILL.md's directory).
TARGET="/absolute/path/to/the-skill"
docker run --rm -v "$TARGET:/scan:ro" skillspector scan /scan --no-llm
```

**Scan a skill straight from a Git URL (nothing lands on disk):**
```bash
docker run --rm skillspector scan https://github.com/user/some-skill --no-llm
```

**Scan a zip:**
```bash
docker run --rm -v "$(dirname /abs/path/skill.zip):/scan:ro" skillspector scan /scan/skill.zip --no-llm
```

**Machine-readable (for gating in a script):** add `--format json` and read `risk_assessment.recommendation` (`SAFE` / `CAUTION` / `DO_NOT_INSTALL`) plus `risk_assessment.score` (0-100). That is the contract, not the exit code (the CLI exits 0 for both SAFE and CAUTION). Per-finding detail is in the `issues` array.

If the `skillspector` image is missing, build it once from the audited clone:
```bash
docker build -t skillspector ~/dev/<SCANNER_EVAL_TREE>     # pinned, security-reviewed tree
```

## How to read the result

| Score | Label | What to do |
|---|---|---|
| 0-20 | SAFE | Pre-screen passed. For an external skill, still run the house rule two-round gate. For our own skill, ship it. |
| 21-50 | CAUTION | Read each finding. Fix it (if it is ours) or treat it as a real risk before the two-round gate. |
| 51-100 | DO NOT INSTALL | Stop. Do not install. Surface the findings to the owner. |

Then say, in plain English: the score, the top 1-3 findings (each as "what + where + why it matters"), and the one-line recommendation. Be critical, not deferential (assume the skill wants to do something bad until the scan proves otherwise). For an external skill that passes, hand off to a repository-security reviewer (round 1) and a second, different-vendor model (round 2), per your own house rules.

## Safety rules baked in (do not change these)

- **Always `--no-llm`.** Static mode needs no API keys and cannot leak the scanned content. Only consider the LLM pass for a TRUSTED target, and only by setting `SKILLSPECTOR_PROVIDER` explicitly and passing the key via `--env-file` (never rely on a global `OPENAI_API_KEY`: the tool can silently fall back to it and route content to OpenAI). For an untrusted skill, `--no-llm` is mandatory.
- **Never run SkillSpector's MCP-server mode here.** Codex Round 2 rated the LLM-enabled MCP-server mode FAIL-as-shipped: `scan_skill` will read any local path and the LLM pass ships the contents to a provider, so a prompt-injected agent could weaponize it to read `~/.ssh`. This skill deliberately uses the one-shot CLI in a read-only container instead.
- **Read-only mount** (`:ro`) for local targets, **`--rm`** throwaway container. The scanner never executes the skill it scans (pure static parse), and the container cannot write back to your files.
- **Pin the image** to the audited build (`~/dev/<SCANNER_EVAL_TREE>`, at the reviewed commit). Rebuild from that tree, not from `main`, unless re-reviewed.

## What it checks (so you know what a pass means)

Static patterns + Python AST + source-to-sink taint + YARA signatures + live CVE lookup, across ~19 threat categories: prompt injection, hidden instructions, anti-refusal, data exfiltration, privilege escalation, system-prompt leakage, memory poisoning, tool misuse, rogue-agent self-modification, excessive agency, trigger abuse, agent snooping, SSRF, supply chain / typosquats, and the MCP-specific least-privilege / tool-poisoning / rug-pull checks. Full taxonomy: the scanner's own documentation, plus your own skill-authoring conventions.
