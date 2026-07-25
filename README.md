# MeGaNeKo AI Workflows

MeGaNeKo AI Workflows bundles provider-neutral skills for coding, API design, Git workflow, frontend design, context hygiene, and agent workflow.

Environment-specific skills, such as Docker context rules, stay local and are not bundled here.

## Codex Installation

After this repo is pushed to GitHub:

```powershell
codex plugin marketplace add MeGaNeKoS/codex-personal-workflows
codex plugin marketplace upgrade
```

Then open `/plugins`, select the marketplace, and install `meganeko-ai-workflows`.

## Claude Code Installation

Claude Code can load the same portable skill directories from its `skills` directory. From a clone of this repository:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item ".\plugins\meganeko-ai-workflows\skills\*" "$HOME\.claude\skills" -Recurse -Force
```

On macOS or Linux:

```bash
mkdir -p ~/.claude/skills
cp -R plugins/meganeko-ai-workflows/skills/* ~/.claude/skills/
```

The provider-neutral skills are shared between both installations. The Codex marketplace manifest remains only for Codex discovery and installation.

## Update Codex

```powershell
codex plugin marketplace upgrade
```

Restart Codex or reload skills if the updated skills do not appear immediately.

## Release

Current release: `v0.3.0`
