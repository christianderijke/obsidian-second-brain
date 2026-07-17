---
description: Regenerate vault-status.html - the status page showing whether every vault pipeline runs, which devices and programs write where, and what still needs doing
category: meta
triggers_en: ["vault status", "vault dashboard", "status page", "regenerate vault status"]
---

Use the obsidian-second-brain skill. Execute `/vaultdb`:

Regenerates `vault-status.html` in the vault root - a self-contained status page that answers "does the whole vault process still work, and what remains to be done?" at a glance.

1. Read `_CLAUDE.md` first if it exists in the vault root
2. Gather fresh data - never reuse numbers from a previous version of the page:
   - **Heartbeats**: parse the agent/run lines from `log.md` of this vault AND of every other vault/brain the user's global `~/.claude/CLAUDE.md` routes to (for Christian: the Datalign company brain under the `GoogleDrive-christian@datalign.nl` mount) - per trigger the last run, exit code and payload, plus the run history of the last 8 days
   - **Schedules & writers**: the trigger tables in `Knowledge/Vault Maintenance Setup.md` (personal vault) and `_setup/README.md` (brain) are the source of truth for what SHOULD run, where and when
   - **Last interactive writes**: newest note mtimes per vault (exclude `.obsidian`, `.trash`, `_trash`)
   - **Live checks on the current machine**: `date`, `scutil --get ComputerName`, `launchctl list | grep -iE 'obsidian|datalign|telegram'`, `DATALIGN_BRAIN_PATH` in `~/.zshenv`, the brain staging dir (`datalign/_staging/sessions/`), open ADR notes (`Knowledge/ADR-*`) and unchecked tasks in `openspec/changes/*/tasks.md` of both vaults
3. Evaluate per writer (program x device) and assign one status: **OK** (last run exit 0, on schedule) / **Storing** (FAILED heartbeat or missed window) / **Ontbreekt/Gat** (pipeline planned but not built or not installed) / **In aanbouw** (mid-implementation per openspec tasks) / **Check** (docs contradict observed reality). Runs scheduled later today are "gepland", never a failure. Build the 8-day run matrix per scheduled trigger
4. Rewrite `vault-status.html` in the vault root, preserving the existing file's design and sections: verdict tiles, one card per vault with a writer table, the 8-day run matrix, a prioritized to-do list (hoog/middel/laag), known failure classes, and a sources footer with generation timestamp + machine name. If the file is missing, rebuild those same sections from scratch: fully self-contained HTML (no external requests), system font, light + dark via `prefers-color-scheme`, status colors always paired with a text label, page copy in Dutch
5. Append `## [YYYY-MM-DD] vaultdb | pagina ververst (N storingen, M gaten)` to `log.md` and report a one-line verdict in chat

The page is a static snapshot; this command IS the refresh mechanism, so it must leave the file openable offline and the verdict honest - a pipeline that silently stopped is exactly what the page exists to catch.

---

**Anti-fabrication:** Search exhaustively before claiming any note, person, or file is absent - false absence is the most common failure mode - and never invent facts, entities, or dates (mark unknowns as `TBD`). See the anti-fabrication and search-completeness hard rules in `references/ai-first-rules.md`.
