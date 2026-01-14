![Midnight](https://img.shields.io/badge/Midnight-12.0.0-a335ee?style=for-the-badge) ![Retail](https://img.shields.io/badge/Retail-11.2.7-008833?style=for-the-badge) ![Mists](https://img.shields.io/badge/Mists-5.5.3-28ae7e?style=for-the-badge) ![TBC](https://img.shields.io/badge/TBC-2.5.5-62c907?style=for-the-badge) ![Classic](https://img.shields.io/badge/Classic-1.15.8-c39361?style=for-the-badge)<br>
[![CurseForge](https://img.shields.io/badge/curseforge-download-F16436?style=for-the-badge&logo=curseforge&logoColor=white)](https://www.curseforge.com/wow/addons/krowi-progress-bar) [![Wago](https://img.shields.io/badge/Wago-Download-c1272d?style=for-the-badge)](https://addons.wago.io/addons/krowi-progress-bar)<br>
[![Discord](https://img.shields.io/badge/discord-join-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/mdBFQJYeQZ) [![PayPal](https://img.shields.io/badge/paypal-donate-002991.svg?style=for-the-badge&logo=paypal&logoColor=white)](https://www.paypal.com/donate/?hosted_button_id=9QEDV37APQ6YJ)

A reusable progress bar library for World of Warcraft addon development that can display up to 4 different values and colors in a single progress bar.

## Features

### Progress Bar Library (`Krowi_ProgressBar`)
- **Multi-Value Display**: Show up to 4 different values with individual colors in a single progress bar
- **Flexible Styling**: Customizable colors for each value segment
- **Easy Integration**: Simple API for quick implementation
- **LibStub Support**: Standard LibStub library structure for dependency management

### Game Tooltip Integration (`Krowi_GameTooltipWithProgressBar`)
- **Tooltip Progress Bars**: Easily add progress bars to game tooltips
- **Automatic Management**: Automatically handles tooltip show/hide events
- **Flexible Positioning**: Automatically positions within tooltips

## Usage Examples

### Basic Progress Bar
```lua
local ProgressBarLib = LibStub("Krowi_ProgressBar")
local progressBar = ProgressBarLib:GetNew(parentFrame)

progressBar:SetMinMaxValues(0, 100)
progressBar:SetValues(50, 25, 15, 10)  -- Four segments
progressBar:SetColors(
    {R = 0, G = 1, B = 0},  -- Green
    {R = 1, G = 1, B = 0},  -- Yellow
    {R = 1, G = 0.5, B = 0}, -- Orange
    {R = 1, G = 0, B = 0}   -- Red
)
progressBar:UpdateTextures()
progressBar:Show()
```

### Tooltip Integration
```lua
local TooltipProgressBar = LibStub("Krowi_GameTooltipWithProgressBar-2.0")

-- Add progress bar to tooltip
TooltipProgressBar:Show(
    GameTooltip,
    0, 100,           -- min, max
    50, 25, 15, 10,   -- value1, value2, value3, value4
    {R = 0, G = 1, B = 0},  -- color1 (green)
    {R = 1, G = 1, B = 0},  -- color2 (yellow)
    {R = 1, G = 0.5, B = 0}, -- color3 (orange)
    {R = 1, G = 0, B = 0},  -- color4 (red)
    "Progress: 100/100"      -- text label
)
```

## API Reference

### Krowi_ProgressBar

#### Creating a Progress Bar
```lua
local ProgressBarLib = LibStub("Krowi_ProgressBar")
local progressBar = ProgressBarLib:GetNew(parentFrame)
```

#### Progress Bar Functions

| Function | Parameters | Description |
|----------|------------|-------------|
| `GetNew(parent)` | `parent` (frame, optional) | Creates a new progress bar frame. Parent defaults to UIParent |
| `SetMinMaxValues(min, max)` | `min` (number), `max` (number) | Sets the minimum and maximum values for the progress bar scale |
| `SetValues(v1, v2, v3, v4)` | `v1, v2, v3, v4` (numbers) | Sets up to 4 values to display as stacked segments |
| `SetColors(c1, c2, c3, c4)` | `c1, c2, c3, c4` (color tables) | Sets colors for each segment. Color format: `{R=r, G=g, B=b}` (0-1 range) |
| `UpdateTextures()` | - | Recalculates and redraws the progress bar. Call after changing values or colors |
| `Reset()` | - | Resets all values to 0 and colors to black |
| `AdjustOffsets(x, y)` | `x, y` (numbers) | Adjusts internal rendering offsets. Default is 4, 5 |
| `Show()` | - | Shows the progress bar frame |
| `Hide()` | - | Hides the progress bar frame |

#### Color Format
Colors must be tables with R, G, B keys (values 0-1):
```lua
{R = 0, G = 1, B = 0}     -- Green
{R = 1, G = 0, B = 0}     -- Red
{R = 1, G = 1, B = 0}     -- Yellow
{R = 1, G = 0.5, B = 0}   -- Orange
```

### Krowi_GameTooltipWithProgressBar

#### Tooltip Integration
```lua
local TooltipProgressBar = LibStub("Krowi_GameTooltipWithProgressBar")
```

#### Tooltip Functions

| Function | Parameters | Description |
|----------|------------|-------------|
| `Show(tooltip, min, max, v1, v2, v3, v4, c1, c2, c3, c4, text)` | See below | Adds and shows a progress bar on the specified tooltip |

**Show() Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `tooltip` | frame | The tooltip frame (usually GameTooltip) |
| `min` | number | Minimum value for the progress bar scale |
| `max` | number | Maximum value for the progress bar scale |
| `v1, v2, v3, v4` | numbers | Up to 4 values to display as stacked segments |
| `c1, c2, c3, c4` | color tables | Colors for each segment (format: `{R=r, G=g, B=b}`) |
| `text` | string | Optional text label to display with the progress bar |

**Note:** The tooltip progress bar automatically hides when the tooltip hides.

## Use Cases
- Achievement progress tracking
- Quest completion status
- Multi-currency displays
- Reputation gains
- Item quality breakdowns
- Any scenario requiring visual representation of multiple values

## Requirements
- Krowi_LibMan