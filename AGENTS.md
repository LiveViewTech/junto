# Junto

## Purpose

Umbrella for the Junto multi-agent coordination system: ships the canonical Claude system-prompt template and adopter setup/launch/check scripts so agents share persistent memory and operating rules via junto-memory.

## Project Snapshot

- Type: single repo (umbrella docs + shell tooling + templates; runtime lives in sibling repos)
- Tech: Bash, Python 3 (`check-auth.py`), PowerShell (`templates/render.ps1`), Markdown templates/docs
- External: junto-memory MCP (`/mcp`, health `/health`), optional junto-inbox channel plugin
- Subguides: [templates/AGENTS.md](templates/AGENTS.md)

## Commands

Clone path is arbitrary (scripts are relative); docs assume `~/.junto`.

```sh
~/.junto/junto-setup.sh
cd <project-dir> && ~/.junto/junto-launch.sh
~/.junto/junto-launch.sh --no-plugin
~/.junto/junto-check.sh
~/.junto/junto-check.sh --fix
python3 ~/.junto/check-auth.py URL API_KEY PROJECT
cd ~/.junto/templates && ./render.sh --agent NAME --project NAME --role "ROLE" --shared-memory-url URL
# Windows: templates/render.ps1 (same {{var}} surface)
```

## Conventions

- Operational rules → rendered system prompt; project `CLAUDE.md` is orientation only (`docs/claude-md-migration.md`)
- `{{agent}}` / `{{project}}` / `{{role}}` / `{{shared_memory_url}}` / … is the public contract; breaking renames need CHANGELOG
- Overlays additive only — never contradict the base template
- Local `config` (API key, memory URL) is gitignored; setup `chmod 600`
- Track `main` unless pinning a commit; no package manifest or CI here

## Directory Map

- `templates/` → [templates/AGENTS.md](templates/AGENTS.md)
- `docs/` → adopter guides (`getting-started.md`, `claude-md-migration.md`, `team-member-guide.md`)
- Root scripts → `junto-setup.sh`, `junto-launch.sh`, `junto-check.sh`, `check-auth.py`

## Gotchas

- **API key in rendered prompt:** `templates/render.sh` embeds `--api-key` into `{{auth_block}}`; launch writes `/tmp/junto-*-prompt.md`
- **Launch creates `CLAUDE.md`:** interactive `junto-launch.sh` can write identity into cwd — run from the intended project directory
- **`junto-check.sh` LVT defaults:** hardcodes `spg-junto-central` and LVT Tailscale checks; non-LVT deploys will see false failures
- **Wire coupling:** `state:<agent>`, `[REQUIRES REVIEW]` / `[SYSTEM NOTICE]`, and `memory_start_session` arg names must stay in sync with junto-memory
