## Support me
<a href="https://www.paypal.com/donate/?hosted_button_id=NYWTBA4XM6ZS6" alt="Paypal">
  <img src="https://www.paypalobjects.com/en_US/BE/i/btn/btn_donateCC_LG.gif" />
</a>
<a href="https://www.patreon.com/Krowi" alt="Patreon">
  <img src="https://raw.githubusercontent.com/codebard/patron-button-and-widgets-by-codebard/master/images/become_a_patron_button.png" />
</a>
<a href='https://ko-fi.com/E1E6G64LS' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://storage.ko-fi.com/cdn/kofi2.png?v=3' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>

## Purpose
This library was created to make a reusable progress bar that can show up to 4 different values and colors. This library was initially created for personal use so documentation is lacking.

## Example
```lua
local progressBar = LibStub("Krowi_ProgressBar-1.1"):New(GameTooltip);
local preHookFunction = progressBar.Show;
function progressBar:Show(gameTooltip, min, max, value1, value2, value3, value4, color1, color2, color3, color4, text)
	self:Reset();
	self:Add(gameTooltip, min, max, value1, value2, value3, value4, color1, color2, color3, color4, text);
	preHookFunction(self);
end
hooksecurefunc(GameTooltip, "Hide", function(self)
	if progressBar then
		progressBar:Hide();
	end
end);

progressBar:Show(GameTooltip, 0, 100, 50, 10, 0, 0, GreenRGB, RedRGB, nil, nil, "text");
GameTooltip:Show();
```