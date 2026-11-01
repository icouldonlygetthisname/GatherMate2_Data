# GatherMate2_Data (local fork)

Fork of [GatherMate2_Data](https://github.com/Nevcairiel/GatherMate2_Data) by Nevcairiel: the Wowhead node dump that [GatherMate2](https://github.com/Nevcairiel/GatherMate2) imports from its Import options page. It is load-on-demand and only loads when you import. The data itself is upstream's, unchanged.

The sections from [Anatomy of the datafile](#anatomy-of-the-datafile) down are upstream's own documentation of the data format.

## Changes (branch `fixes`)

Based on upstream `master` at `024e5ef` (2026-03-25, data from #10). Upstream has no newer commits as of 2026-11-01.

| Commit | Change |
| --- | --- |
| `a7b8abc` | TOC: `## Interface: 120100, 120105` instead of `120001`. The Midnight node data (#12) was already added in #10, but the TOC didn't list current clients, so the addon showed as out of date. |

## Workflow (retail)

The retail game folder (`_retail_\Interface\AddOns\GatherMate2_Data`) is a copy of this branch, not a CurseForge package. Two packager tokens are replaced by hand:

| Token | Where | Value |
| --- | --- | --- |
| `@project-version@` | `GatherMate2_Data.toc` | `fork-fixes-<short hash>` |
| `@project-revision@` | `GatherMate2_Data.toc` (`X-Generated-Version`) and `GatherMateData.lua` (`generatedVersion`) | `git rev-list --count HEAD`, currently `219` |

The revision is the import version. With Auto Import on, GatherMate2 re-imports at login whenever `X-Generated-Version` is higher than the last imported one. With the import style set to Overwrite, that re-import clears the database first, including nodes you gathered yourself. Every commit raises the count, even one that only changes docs. So when no data changed, keep the version that's already deployed.

To update the game folder:

1. Dry run, to catch edits made in the game folder: `robocopy E:\VScode\GatherMate2_Data "<AddOns>\GatherMate2_Data" /E /XD .git .github /XF .pkgmeta CLAUDE.md /L`.
2. Run it again without `/L`.
3. Replace the tokens as above.

After an upstream data update, rebase or merge `fixes` onto upstream `master` and update this README.

## WoW Forever

Not supported, and not installed in `_forever_`. WoW Forever uses the Classic Era uiMapIDs (Elwynn Forest `1429`, The Barrens `1413`, roughly `1411`-`1458`), and none of the ~240,000 nodes here are on those maps. The vanilla zones in this data use retail map IDs with post-Cataclysm spawn points, so remapping the IDs wouldn't give correct positions. The TOC doesn't list `16001`, so on Forever GatherMate2's Import page shows the addon as unavailable. Forever support would need a separate vanilla-era data set.

# Anatomy of the datafile

Sample FishData.lua

```lua
GatherMateData2FishDB = {
    [ZONE_ID] = {
        [COORDINATES] = NODE_ID,
    }
}
```

`ZONE_ID` - can be found in-game by typing `/dump C_Map.GetBestMapForUnit("player")` in the chat.  This is the same zone id that the TomTom addon uses with their `/way` command.

`COORDINATES` - In `XXXXYYYY00` format.  Always 10-digits long.  Use zeros as a buffer.  Example: If you have a coordinate of 64.3 59.8, the coordinate value would be `6430598000`.  If you have a coordinate of 58.39 31.41, the coordinate value would be `5839314100`

`NODE_ID` - As defined by the [GatherMate2](https://www.curseforge.com/wow/addons/gathermate2) addon in the `GatherMate2\Constants.lua` file.

## Example

There's a `Prismatic Leaper` node in `Ohn'ahran Plains` at location 64.3, 59.8.

```lua
GatherMateData2FishDB = {
    [2023] = {
        [6430598000] = 1117,
    }
}
```

There's also `Prismatic Leaper` node in `The Azure Span` at 30.4, 25.0.

```lua
GatherMateData2FishDB = {
    [2023] = {
        [6430598000] = 1117,
    },
    [2024] = {
        [3040250000] = 1117,
    }
}
```

## Tips for populating data from WoWHead

1. Lookup the object/node you care about on wowhead and has the map indicating where to find it.

    [Prismatic Leaper](https://www.wowhead.com/item=200061/prismatic-leaper) on WoWHead

2. Click the pin on the map, and then click `Copy All -> TomTom Command`

    When you paste, you'll get something that looks like this:

    ``` text
    /way #2023 56.4 80.4 Prismatic Leaper
    /way #2023 56.4 80.5 Prismatic Leaper
    /way #2023 56.5 80.4 Prismatic Leaper
    /way #2023 56.5 80.5 Prismatic Leaper
    /way #2023 58.3 31.7 Prismatic Leaper
    /way #2023 58.4 31.4 Prismatic Leaper
    /way #2023 58.5 31.4 Prismatic Leaper
    /way #2023 58.5 31.5 Prismatic Leaper
    /way #2023 61.5 82.3 Prismatic Leaper
    /way #2023 64.3 38.5 Prismatic Leaper
    /way #2023 64.4 38.2 Prismatic Leaper
    /way #2023 64.6 38.3 Prismatic Leaper
    /way #2023 64.6 38.7 Prismatic Leaper
    /way #2023 86.2 51.9 Prismatic Leaper
    ```

3. Then use your preferred method to then translate those coordinates into entries to include in FishData.lua.  Don't forget to look up the constant for "Prismatic Leaper" in the `GatherMate2\Constants.lua` file.
