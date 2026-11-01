# GatherMate2_Data (forked third-party addon)

Upstream addon by Nevcairiel: the Wowhead node data that GatherMate2 imports. This repo is a real GitHub fork: `origin` = github.com/icouldonlygetthisname/GatherMate2_Data, `upstream` = github.com/Nevcairiel/GatherMate2_Data. Local work lives on `fixes` (currently a TOC update only). See README.md for the change list, the retail workflow and why there's no Forever support.

## How this differs from the other patched addons

- Upstream's own git history is the baseline. There are no `Baseline: ... (upstream, unmodified)` commits. Rebase or merge onto upstream `master`. Upstream doesn't tag data updates consistently (old `v9.x` tags, `git describe` says `v41.0`).
- No `Libs/`. It depends on GatherMate2 (`## Dependencies`) and uses its AceAddon instance.
- The retail game folder is a copy of `fixes` with `@project-version@` and `@project-revision@` replaced by hand. Dry-run robocopy (`/L`) first.
- `@project-revision@` is `git rev-list --count HEAD` and is the import version. Raising it makes Auto Import re-import at login, and with the Overwrite style that clears the user's database first. Only raise it when the data changed. Docs-only commits still raise the count, so keep the deployed number for those.

## Data files

- `MiningData.lua` and `HerbalismData.lua` are ~2.3 MB / ~100k lines each, `TimberData.lua` ~25k lines. Never Read them whole. Grep, or load them with `lua` and query the tables.
- Format: `GatherMateData2<Type>DB[uiMapID][coord] = nodeID`. `coord` is `XXXXYYYY00` (GatherMate2's encoding), and `nodeID` comes from `GatherMate2\Constants.lua`. A node ID missing there won't show up after import.
- `GatherMateData.lua` holds the import code and the per-expansion zone filters (`bcZones` … `mnZones`). A new zone needs adding to its expansion's table, or "Expansion Data Only" imports skip it.
- The import calls `GatherMate:InjectNode2(zone, coords, nodeType, nodeID)` and `GatherMate:ClearDB(type)`. If GatherMate2 changes either, this addon breaks.

## WoW Forever

Don't add `16001` to the TOC. Forever uses Classic Era uiMapIDs (`1411`-`1458`), and this data has no nodes on them. Its vanilla zones are retail map IDs with post-Cataclysm spawns, so they can't be remapped. Forever data would need a new vanilla-era source.

## Rules

- Match upstream style: tabs, no `;`.
- Data changes go in their own commits, separate from code or TOC changes.
- Run `luac -p` on changed files. Upstream CI has luacheck commented out.
- The fork is public. Pushing branches to `origin` is fine. Opening PRs against `upstream` is outward-facing, so ask first.
