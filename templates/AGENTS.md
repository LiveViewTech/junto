# templates

## Context

Canonical junto agent system-prompt template, POSIX/PowerShell renderers, and overlay starters. Injected at Claude launch via `--append-system-prompt-file`.

## Tech

- Base: `junto-system-prompt.md.tmpl` (`{{var}}` placeholders)
- Renderers: `render.sh` (bash 3.2+), `render.ps1` (PowerShell 5.1/7, explicit UTF-8)
- Overlays: `overlays/<project>.md` (optional); starter `overlays/example.md`; setup uses `overlays/first-run.md`

## Architecture

1. Launcher resolves agent/project/role/URL/cwd/(api-key)/(plugin-present)
2. Renderer concatenates base + optional overlay + optional extras
3. Substitutes `{{var}}`; injects derived `{{auth_block}}` and conditional `{{plugin_session_block}}`
4. Fails with exit 3 if any `{{...}}` remains
5. Claude loads the rendered file; agent calls junto-memory MCP per template contract

## Patterns

- DO keep cross-cutting rules in the base template (`junto-system-prompt.md.tmpl`)
- DO put project-only additive rules in `overlays/<project>.md` (see `overlays/example.md`)
- DO pass `--cwd` explicitly — renderer pwd is rarely the agent working dir (`render.sh`, `templates/README.md`)
- DON'T leave unresolved `{{tokens}}` — both renderers refuse to ship them (`render.sh` exit 3)
- DON'T duplicate identity / `memory_start_session` / park checklist into project `CLAUDE.md` (see `../docs/claude-md-migration.md`)
- DON'T contradict base from an overlay — change base (or file a junto backlog item) instead

## Key Files

- `junto-system-prompt.md.tmpl` — identity, session contract, go/park/status, park checklist, markers
- `render.sh` / `render.ps1` — substitution + safety net
- `overlays/example.md` — annotated overlay starter
- `overlays/first-run.md` — onboarding overlay used by `../junto-setup.sh`
- `README.md` — variable surface and wire conventions
