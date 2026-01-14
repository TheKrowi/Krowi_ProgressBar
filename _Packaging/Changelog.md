# Changelog
All notable changes to this project will be documented in this file.

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