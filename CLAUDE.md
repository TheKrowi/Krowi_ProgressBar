# CLAUDE.md

Krowi's Progress Bar (`Krowi_ProgressBar`) is a World of Warcraft library in Lua 5.1 + WoW XML: a bar that draws up to four stacked values in their own colours, and a library-owned tooltip that shows one such bar. Addons embed it as a `Libs/Krowi_ProgressBar` git submodule. The workspace-wide rules (tooling traps, the game loop, taint, git, release safety) load from `../../CLAUDE.md` and `Krowi_ClaudeKit/WowAddons.md`; this file adds only what is specific to this repo.

There is no build, lint or test step: the game, through a consumer, is the test harness. The `krowi` plugin (enabled in `.claude/settings.json`) syntax-checks every edited `.lua` with Lua 5.1 and guards the known tooling traps. The repo is public, but `.claude/settings.json` points at the private kit by absolute path, so the plugin loads only on Krowi's machine; that is intended.

## Clients and loading

- One TOC, `Krowi_ProgressBar.toc`: `## Interface: 120005, 50503, 20505, 11508` (Retail 12.0.5, Mists 5.5.3, Anniversary 2.5.5, Era 1.15.8; no 11.x). It counts only for a standalone install, the README badges and the upload game versions; an embedded copy loads through the consumer's XML.
- The TOC loads `Krowi_ProgressBar.xml`, which runs in order: `Krowi_ProgressBar.lua`, `Krowi_ProgressBarMixin.lua`, the virtual template `Krowi_ProgressBar_Template` (`:10`), the frame `Krowi_ProgressBarTooltip` (`:129`), `Krowi_GameTooltipWithProgressBar.lua` (`:133`). The main file stays first: the two submodules attach to the library LibMan last made current, which `NewLibrary` sets even when it returns nil (`Krowi_Util/LibMan.lua:23-25`).
- Needs `KROWI_LIBMAN` and LibStub from Krowi_Util loaded first (the consumer's `Libs/Files.xml` includes Krowi_Util earlier); the TOC declares no dependency. `SetUtil` gives `lib.Util`, Krowi_Util's main table, used for `DelayFunction`.
- One code path for every client. The only client-dependent line is the `issecretvalue` guard in `Resize` (`Krowi_ProgressBarMixin.lua:23`), skipped where that function does not exist.

## Versioning

Three LibStub entries, each with its own minor, registered through Krowi_Util's LibMan: `NewLibrary` wraps `LibStub:NewLibrary`, and `NewSubmodule` registers `<parent>_<sub>`, stores it as `parent[sub]` and returns nil when that minor is already loaded (`Krowi_Util/LibMan.lua:16-41`, `:61-77`).

| File | Registration | LibStub name |
|---|---|---|
| `Krowi_ProgressBar.lua` | `:8` `KROWI_LIBMAN:NewLibrary('Krowi_ProgressBar_2', 3, ...)` | `Krowi_ProgressBar_2`, minor 3 |
| `Krowi_ProgressBarMixin.lua` | `:8` `KROWI_LIBMAN:NewSubmodule('Mixin', 1)` | `Krowi_ProgressBar_2_Mixin`, minor 1 |
| `Krowi_GameTooltipWithProgressBar.lua` | `:8` `KROWI_LIBMAN:NewSubmodule('GameTooltipWithProgressBar', 1)` | `Krowi_ProgressBar_2_GameTooltipWithProgressBar`, minor 1 |

- A changed file needs its own minor bumped; the main minor does not carry the submodules. Every release so far also bumped the main minor (1 in 2.1, 2 in 2.2, 3 in 2.3); keep doing so.
- An upgrade cannot undo an older copy. The mixin file assigns a new global `Krowi_ProgressBarMixin` table (`Krowi_ProgressBarMixin.lua:11`), so bars built earlier keep the old methods and hooks placed on the old table are lost. The tooltip file builds a new bar (`Krowi_GameTooltipWithProgressBar.lua:12`); a copy of 2.2 or older that loaded first keeps its bar on `GameTooltip` and its `hooksecurefunc(GameTooltip, 'Hide', ...)`.
- XML is not versioned. Every embedded copy runs `Krowi_ProgressBar.xml`: the first copy to load defines `Krowi_ProgressBar_Template` and later definitions are dropped, and every copy creates another `Krowi_ProgressBarTooltip` (see Known debt). Keep the template and the mixin compatible in both directions.
- `_2` is the major. 2.1 renamed `Krowi_ProgressBar-2.0` to `Krowi_ProgressBar_2`, which LibStub treats as a separate library. 2.3 changed `Show`'s arguments within `_2`, which was safe only because Krowi_AchievementFilter is the sole consumer.

## Consumers

Only Krowi_AchievementFilter (`Libs/Krowi_ProgressBar`, at 2.3); no third-party copy is installed on Krowi's clients. A change ships by releasing the library and bumping that submodule. Its use, through `addon.ProgressBarLib = KROWI_LIBMAN:GetLibrary('Krowi_ProgressBar_2')` (`Libs/Libs.lua:11`):

- `GetNew(self)` for the summary bars (`Gui/WindowFrames/SummaryFrame/SummaryFrameMixin.lua:52`).
- `GameTooltipWithProgressBar:Show(frame, anchor, title, 0, total, completed, notObtainable, 0, 0, green, red, nil, nil, text)`, then extra lines on `.Tooltip` (`Gui/Gui.lua:293-301`); `:Hide()` (`Gui/WindowFrames/CategoryButton/CategoryButtonMixin.lua:20`, `Gui/WorldMapButton/WorldMapButtonMixin.lua:57`).
- The skins: `.GameTooltipWithProgressBar.ProgressBar` (`Plugins/ElvUI.lua:54`, `Plugins/GW2_UI/GW2_UI.lua:241`) and `hooksecurefunc(Krowi_ProgressBarMixin, "UpdateTextures", ...)` (`Plugins/EllesmereUI.lua:996`).

## Architecture and API

- `lib:GetNew(parent)` (`Krowi_ProgressBar.lua:15`) creates `Krowi_ProgressBar<n>` from `Krowi_ProgressBar_Template` on `parent` or `UIParent`. It is not a StatusBar: border textures (achievement-frame art), a half-transparent black background, four `Fill` textures and `TextLeft`/`TextRight` font strings (`Krowi_ProgressBar.xml:10-127`).
- `Krowi_ProgressBarMixin`: `SetMinMaxValues`, `SetValues(v1, v2, v3, v4)`, `SetColors(c1, c2, c3, c4)` with `{R, G, B}` tables (black when nil), `UpdateTextures` (calls `Resize`), `Reset`, `AdjustOffsets`. `Resize` lays the fills side by side, each `barWidth / (Max - Min) * value` wide. `OnSizeChanged` defers `Resize` by 0.01 s through `lib.Util.DelayFunction`.
- `lib.GameTooltipWithProgressBar`: `.Tooltip` is `Krowi_ProgressBarTooltip` (a `GameTooltipTemplate` frame on `UIParent`, `TOOLTIP` strata), `.ProgressBar` one bar permanently parented to it. `:Show(ownerFrame, anchor, title, min, max, v1..v4, c1..c4, text)` sets the owner (`ANCHOR_NONE` and a top-right placement when `anchor` is nil), the title and a blank line the bar is anchored to, then shows the tooltip. `:Hide()` hides it.
- No saved variables, no events, no localized strings.

## Taint-sensitive areas

Since 2.3 the library touches no Blizzard frame: the tooltip is its own, and it calls only `GameTooltip_SetTitle` and `GameTooltip_AddBlankLinesToTooltip` on it (both in every client's FrameXML). Run the `krowi:taint-reviewer` agent after changing any of these:

- `Resize` computes with `GetWidth()`. A secret width returns early (`Krowi_ProgressBarMixin.lua:23`, 2.2's fix for "attempt to perform arithmetic on a secret value"); `self.Background:GetWidth()` (`:30`) is not checked.
- A bar becomes a child of whatever frame the consumer passes to `GetNew`; a Blizzard frame would get a child.
- Consumers write into the owned tooltip (Krowi_AchievementFilter adds lines, `Gui/Gui.lua:297-301`).
- Never parent a bar to `GameTooltip` or hook it again: that caused 2.3's "attempt to compare a secret number value".

## Code style

Match the file you are editing:

- Tabs, except the copyright blocks, the `NewLibrary` options table (`Krowi_ProgressBar.lua:9-10`) and `Krowi_ProgressBarMixin.lua:23`, which use 4 spaces.
- Statements end with `;` in `Krowi_ProgressBarMixin.lua` and `Krowi_ProgressBar.lua:14-17`; not in `Krowi_GameTooltipWithProgressBar.lua` (rewritten in 2.3).
- Single quotes in Lua and in the template's XML attributes; the `Krowi_ProgressBarTooltip` element uses double quotes.
- Every `.lua` opens with the four-line `--[[ Copyright (c) 2023 Krowi ... ]]` block, a blank line, `---@diagnostic disable: undefined-global`, a blank line, then the `NewLibrary` or `NewSubmodule` call and its early return. The XML and the TOC carry the same notice as comments.
- Globals, frames and templates are named `Krowi_ProgressBar*`.
- CRLF on disk (LF in git, `core.autocrlf=true`), no BOM, no final newline. No `.editorconfig` and no `.vscode/settings.json`.

## Testing in game

Test through Krowi_AchievementFilter, not with `Deploy.ps1` from this repo: that installs a second copy (`AddOns\Krowi_ProgressBar`) that loads after Krowi_AchievementFilter, counts only with higher minors, never replaces the first-loaded template, and adds a second `Krowi_ProgressBarTooltip`.

1. Commit the change on this repo's branch.
2. In a Krowi_AchievementFilter worktree (`New-Worktree.ps1`), point the submodule at it: `git -C Libs\Krowi_ProgressBar fetch "E:\World of Warcraft Addon Development\Krowi_Libraries\Krowi_ProgressBar" <branch>`, then `git -C Libs\Krowi_ProgressBar checkout FETCH_HEAD`.
3. Deploy that worktree (Retail, then `-Client Classic`), ask Krowi to `/reload`, and run `Read-GameErrors.ps1` from it. Exercise the category tooltip (hover a category in the achievement window) and the summary bars.
4. Put the submodule pointer back before anything is committed there; the bump goes into Krowi_AchievementFilter's own PR after the release.

Krowi_AchievementFilter loads on Retail, Mists and Titan only; no Krowi addon loads this library on Era or Anniversary, which the TOC claims.

## Git and release

- `main` is the only branch and the release branch: every `Release x.y` commit and tag (2.0.1, 2.1, 2.2, 2.3) is on it. Branch from `main` and open PRs against it. `origin/fix/library-owned-tooltip` is merged (it is 2.3) and can be deleted.
- Release only with the plugin's `krowi:release` skill, from `E:\World of Warcraft Addon Development\Krowi_Libraries\Krowi_ProgressBar` (the library is in the addon manager's `LIBRARY_NAMES`). Every GitHub release so far is a full release: `--stability stable`.
- The addon manager writes `README.md` (badges from the TOC's `## Interface:` plus `_Packaging/Description.md`) and `_Packaging/ReleaseNotes.md` (for a library only the newest changelog entry). Edit `_Packaging/Description.md` and `_Packaging/Changelog.md`.
- The changelog's readers are addon authors: entries name API changes (2.3 lists the new `Show` arguments).
- After a release, bump `Libs/Krowi_ProgressBar` in Krowi_AchievementFilter.

## Known debt (verified 2026-10-17)

- Two copies of 2.3 or later make two `Krowi_ProgressBarTooltip` frames (`Krowi_ProgressBar.xml:129`). The submodule keeps the first in `.Tooltip` and parents its bar to it (`Krowi_GameTooltipWithProgressBar.lua:11-12`), but `Show`, `Add` and `Hide` use the global (`:16`, `:35`, `:31`), which is the last frame created: the bar then sits on a hidden frame, and Krowi_AchievementFilter's extra lines go to the other tooltip. Only a standalone install or a second consumer triggers it today. Read from the code, not seen in game.
- `OnSizeChanged` defers every bar under one key, `'Krowi_ProgressBar_OnSizeChanged'` (`Krowi_ProgressBarMixin.lua:19`), and `DelayFunction` ignores a call while one with that key is pending (`Krowi_Util/Krowi_Util.lua:100-103`): when several bars change size within 0.01 s, only the first is laid out again. Read from the code, not seen in game.
- The template's fill textures are named `KrowiAF_Fill1` to `KrowiAF_Fill4` without `$parent` (`Krowi_ProgressBar.xml:103-111`): every bar overwrites the same four globals, under Krowi_AchievementFilter's prefix.
- `_Packaging/Description.md`, and so `README.md`, documents the 2.0 API: `LibStub("Krowi_ProgressBar")`, `LibStub("Krowi_GameTooltipWithProgressBar-2.0")`, `Show(tooltip, min, max, ...)`, and a bar that hides with the tooltip. It names Krowi_LibMan as the requirement, which ships in Krowi_Util.
- `## Interface:` lags the live builds (Retail 120105, Mists 50504, Anniversary 20506, Era 11509).
- The TOC declares no dependency on Krowi_Util: a standalone install loads before it (alphabetical order) and errors on `KROWI_LIBMAN` unless an addon loaded earlier embeds Krowi_Util.