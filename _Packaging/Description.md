A reusable progress bar library for World of Warcraft addon development that can display up to 4 different values and colors in a single progress bar.

## Features

### Progress Bar Library (`Krowi_ProgressBar-2.0`)
- **Multi-Value Display**: Show up to 4 different values with individual colors in a single progress bar
- **Flexible Styling**: Customizable colors for each value segment
- **Easy Integration**: Simple API for quick implementation
- **LibStub Support**: Standard LibStub library structure for dependency management

### Game Tooltip Integration (`Krowi_GameTooltipWithProgressBar-2.0`)
- **Tooltip Progress Bars**: Easily add progress bars to game tooltips
- **Automatic Management**: Automatically handles tooltip show/hide events
- **Flexible Positioning**: Automatically positions within tooltips

### API Usage

#### Basic Progress Bar
```lua
local ProgressBarLib = LibStub("Krowi_ProgressBar-2.0")
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

#### Tooltip Integration
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

## Use Cases
- Achievement progress tracking
- Quest completion status
- Multi-currency displays
- Reputation gains
- Item quality breakdowns
- Any scenario requiring visual representation of multiple values

## Usage Examples

### String Variable Replacement
```lua
local util = LibStub("Krowi_Util-1.0")
local text = util.Strings.ReplaceVars("Hello {name}, you have {count} items", {
    name = "Player",
    count = 5
})
-- Result: "Hello Player, you have 5 items"
```

### Color Management
```lua
local colors = LibStub("Krowi_Util-1.0").Colors
local coloredText = colors.SetTextColor("Warning!", colors.Red)
-- Or use string method:
local epicText = "Legendary Item".SetColorEpic(self)
```

### Table Operations
```lua
local util = LibStub("Krowi_Util-1.0")
local data = {player = {stats = {health = 100}}}
local health = util.SafeGet(data, {"player", "stats", "health"}) -- Returns 100
local missing = util.SafeGet(data, {"player", "items", "sword"}) -- Returns nil safely
```

### Version Detection
```lua
local util = LibStub("Krowi_Util-1.0")
if util.IsMainline then
    -- Mainline-specific code
elseif util.IsMistsClassic then
    -- Mists Classic-specific code
end
```

## Requirements
- LibStub
- AceLocale-3.0
- AceConfig-3.0 (for options)
- AceConfigDialog-3.0 (for options UI)
- LibDBIcon-1.0 (for minimap icon)
- LibDataBroker-1.1 (for minimap icon)