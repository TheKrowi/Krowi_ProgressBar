### Fixed (2.3)
- Taint error `attempt to compare a secret number value` caused by the progress bar being a child of Blizzard's `GameTooltip`. The library now owns a dedicated `Krowi_ProgressBarTooltip` frame (`<GameTooltip inherits="GameTooltipTemplate" parent="UIParent">`), and the progress bar is a permanent child of that frame. Blizzard's `GameTooltip` is never touched.
### Changed (2.3)
- `sub:Show()` signature changed: `ownerFrame, anchor, title, min, max, ...` (added `title` param before `min`)
- Added `sub:Hide()` to hide the owned tooltip
- Removed the `hooksecurefunc(GameTooltip, 'Hide', ...)` hook