# GitBook — Locale & Version Index

> Authoritative pointer for which version of each locale is **active**. Older versions are kept for git history but **do not consume** unless writing a migration note.
>
> **Last updated**: 2026-05-11. Maintain manually when promoting a new version to active.

## Active versions (consume these)

| Locale | Active directory | Status |
|---|---|---|
| English | [`en_v7/`](en_v7/) | **ACTIVE** — current production docs. |
| Vietnamese | [`vi_v6/`](vi_v6/) | **ACTIVE** — current production docs. |
| Japanese | [`ja/`](ja/) | **ACTIVE** — only Japanese version. |
| Korean | [`ko_3/`](ko_3/) | **ACTIVE** — current Korean version. |

## Archived versions (do not consume; preserved for git history)

These exist for reference + git history only. Do **not** read them when answering user questions, copying patterns, or publishing — they are superseded.

| Directory | Reason | Superseded by |
|---|---|---|
| [`en/`](en/) | Earliest English draft | `en_v7/` |
| [`en_v2/`](en_v2/) | Pre-v7 English iteration | `en_v7/` |
| [`vi/`](vi/) | Earliest Vietnamese draft | `vi_v6/` |
| [`vi_3/`](vi_3/) | Earlier Vietnamese iteration | `vi_v6/` |
| [`vi_v2/`](vi_v2/) | Earlier Vietnamese iteration | `vi_v6/` |
| [`vi_v4/`](vi_v4/) | Earlier Vietnamese iteration | `vi_v6/` |
| [`vi_v5/`](vi_v5/) | Earlier Vietnamese iteration | `vi_v6/` |
| [`ko/`](ko/) | Earliest Korean draft | `ko_3/` |

## Reading guide

Agent / contributor working in GITBOOK:

1. **Edit only active versions.** Changes to archived directories are wasted effort and risk publishing the wrong version.
2. **When localizing a new feature**: write English first in `en_v7/`, then translate to `vi_v6/`, `ja/`, `ko_3/` in parallel.
3. **When auditing brand voice or product positioning**: read `en_v7/README.md` + `en_v7/SUMMARY.md` first — that's the canonical narrative. Then check sync against other active locales.
4. **When investigating doc history**: archived versions are fair game. Diff against active to understand what changed.

## Cross-subtree references

- [../SC/README.md](../SC/README.md) — protocol architecture + deployed addresses (source of truth for "Smart Contracts" section in active locales).
- [../LANDING/](../LANDING/) — marketing copy. Keep landing CTAs in sync with active GITBOOK voice.
- [../CLAUDE.md](../CLAUDE.md) — monorepo root rules (brand mentions, AI-scrub when committing docs).

## Maintenance — promoting a new version

When you stage a new version (e.g., `vi_v7/` once `vi_v6` is superseded):

1. Build new version directory alongside (e.g., copy `vi_v6/` → `vi_v7/`, edit).
2. **Update this INDEX.md** — move `vi_v6` row from "Active" to "Archived", add `vi_v7` row to "Active".
3. Commit INDEX.md + new directory in **one logical commit**.
4. Do NOT delete the old directory. Do NOT `git mv` rename old → archive — the directory name encodes the version chronology.

## Future cleanup (out of scope for current `DECISIONS_CLEANUP_2026-05-11.md` plan)

When team comfortable with consolidation:
- Move archived directories into `_archive/<locale>_<version>/` (preserve via `git mv`).
- Active dirs keep current paths (`en_v7/`, `vi_v6/`, etc.).
- Or: rename active to canonical names (`en`, `vi`, `ja`, `ko`) and archive everything else.

Both options require coordinating with whatever GitBook deploy pipeline reads the directories (don't break the publish path).
