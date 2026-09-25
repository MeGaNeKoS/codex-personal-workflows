# MeGaNeKo AI Workflows

Personal agent workflow skills for Codex: coding practices, API contracts, Git hygiene, frontend delivery, and agent operating workflow.

Environment-specific skills, such as Docker context rules, stay local and are not bundled here.

## Layout

```text
.agents/plugins/marketplace.json         Codex discovery
plugins/meganeko-ai-workflows/
  .codex-plugin/plugin.json              Codex manifest
  skills/                                all five skills
```

### Skills

| Skill | Scope |
| --- | --- |
| `coding-practices` | Implementation architecture, ownership, dependency seams, testing strategy. Routes to area, language, framework, and protocol references. |
| `api` | HTTP/REST contracts, OpenAPI, RFC 9457 `application/problem+json`. |
| `git-command` | Staging, commits, conventional subjects, history edits, safety. |
| `frontend-delivery` | Frontend research, planning, implementation, and runtime verification under an evidence contract. |
| `agent-workflow` | Goal mode, delegation, and context hygiene, written provider neutral. |

## Install

```powershell
codex plugin marketplace add MeGaNeKoS/codex-personal-workflows
codex plugin marketplace upgrade
```

Then open `/plugins`, select the marketplace, and install `meganeko-ai-workflows`.

Restart Codex or reload skills if the updated skills do not appear immediately.

## Maintenance Invariant

The "non-trivial" scope test is intentionally duplicated word for word in `plugins/meganeko-ai-workflows/skills/coding-practices/SKILL.md` and `plugins/meganeko-ai-workflows/skills/frontend-delivery/SKILL.md`. If one changes, change both.

## Release

Codex manifest version: `v0.3.0`.
