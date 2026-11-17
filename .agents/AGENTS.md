# Repository Agent Metadata

This directory holds canonical repository contributor skills plus Codex plugin
marketplace metadata. `.claude/skills` is a relative symlink to this directory's
`skills/`; both hosts read one source tree. Claude settings/hooks remain in
`.claude/`.

## Rules

- Keep marketplace entries in `.agents/plugins/marketplace.json`.
- Keep `source.path` relative to the repo root, for example `./plugins/promptfoo`.
- Each plugin entry must include `policy.installation`, `policy.authentication`, and `category`.
- Keep marketplace root metadata limited to `name`, optional `interface.displayName`, and `plugins`.
- Do not add product gating unless explicitly requested.
- Keep repo-local project skills under `.agents/skills/<name>/SKILL.md`.
- Keep `.agents/skills` as a real directory and `.claude/skills` as the relative
  `../.agents/skills` symlink. Edit canonical skills and references only; do not
  restore separate platform copies. The three contributor skills remain separate
  from the four-skill published plugin bundle.

## Validation

When changing marketplace metadata, run:

```bash
npx vitest test/agentSkills/promptfooPlugin.test.ts --run
npx prettier --check .agents/AGENTS.md .agents/plugins/marketplace.json
```

When changing repo-local Codex skills, also run the bundled skill validator when available, plus the existing contract test. Resolve the installed validator path rather than changing `CODEX_HOME`; if it is absent, check metadata/references directly and report that limitation:

```bash
for skill in .agents/skills/*; do
  python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" "$skill"
done
npx vitest test/agentSkills/promptfooPlugin.test.ts --run
```
