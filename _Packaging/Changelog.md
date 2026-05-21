# Changelog
All notable changes to this project will be documented in this file.

## 2.3 - 2026-02-05
### Fixed
- Taint error `attempt to compare a secret number value` caused by the progress bar being a child of Blizzard's `GameTooltip`. The library now owns a dedicated `Krowi_ProgressBarTooltip` frame (`<GameTooltip inherits="GameTooltipTemplate" parent="UIParent">`), and the progress bar is a permanent child of that frame. Blizzard's `GameTooltip` is never touched.
### Changed
- `sub:Show()` signature changed: `ownerFrame, anchor, title, min, max, ...` (added `title` param before `min`)
- Added `sub:Hide()` to hide the owned tooltip
- Removed the `hooksecurefunc(GameTooltip, 'Hide', ...)` hook

## 2.2 - 2026-01-31
### Fixed
- Potential fix for `attempt to perform arithmetic on a secret value` error

## 2.1 - 2026-01-14
### Changed
- Refactored file naming structure, removing "-2.0" suffixes from all core files
- Updated Interface version to support 20505 (TBC Classic)
- Renamed core files:
  - `Krowi_GameTooltipWithProgressBar-2.0.lua` → `Krowi_GameTooltipWithProgressBar.lua`
  - `Krowi_ProgressBar-2.0.lua` → `Krowi_ProgressBar.lua`
  - `Krowi_ProgressBar-2.0.xml` → `Krowi_ProgressBar.xml`
  - `Krowi_ProgressBarMixin-2.0.lua` → `Krowi_ProgressBarMixin.lua`
- Updated TOC file structure and dependencies

### Fixed
- Corrected CurseForge and Wago addon URLs in README (Dec 28, 2025)

## 2.0.1 - 2025-12-08
### Changed
- Initial public release
- Complete library implementation with multi-value progress bar support
- Added GameTooltip integration library
- LibStub integration for proper dependency management