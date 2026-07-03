# Personal Codex Workflows

Personal Codex plugin bundling portable skills for coding, API design, Git workflow, and Codex workflow.

Environment-specific skills, such as Docker context rules, stay local and are not bundled here.

## Install As Marketplace

After this repo is pushed to GitHub:

```powershell
codex plugin marketplace add MeGaNeKoS/codex-personal-workflows
codex plugin marketplace upgrade
```

Then open `/plugins`, select the marketplace, and install `personal-codex-workflows`.

## Update

```powershell
codex plugin marketplace upgrade
```

Restart Codex or reload skills if the updated skills do not appear immediately.
