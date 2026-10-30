# my-skills

This is a public, English-language Claude Code plugin repository.

- Write documentation, README content, and commit messages in English.
- Do not commit machine-specific absolute paths or local setup instructions.
- Plugin source lives under `plugins/workflow-skills/`; keep each skill as `skills/<name>/SKILL.md`.
- Prefer plugin installation via `/plugin marketplace add` and `/plugin install`; do not add clone-and-run shell installers.
- Bump `plugins/workflow-skills/.claude-plugin/plugin.json` `version` on every user-visible plugin change. `claude plugin update` only loads a new version; an unchanged version keeps the old skill content.
