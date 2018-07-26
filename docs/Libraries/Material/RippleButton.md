## Button
Material Design Buttons!

### API
Instantiate a new button with `userdata Button.new(string BUTTON_TYPE, RbxObject Parent)`. It returns a userdata that serves as a wrapper for the [TextButton](http://wiki.roblox.com/index.php?title=API:Class/TextButton) Object. Simply declare it and use it like you normally would!

Example:
```lua
local Resources = require(game:GetService("ReplicatedStorage"):WaitForChild("Resources"))
local Color = Resources:LoadLibrary("Color")
local Enumeration = Resources:LoadLibrary("Enumeration")
local PseudoInstance = Resources:LoadLibrary("PseudoInstance")
local NewSnackbar = Resources:LoadLibrary("Snackbar").new
local SnackbarText = "You clicked the %s style button!"

local Player = game:GetService("Players").LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "RippleButtonExample"
ScreenGui.Parent = PlayerGui

local Frame = Instance.new("Frame")
Frame.AnchorPoint = Vector2.new(0.5, 0.5)
Frame.BorderSizePixel = 0
Frame.BackgroundColor3 = Color.White
Frame.Size = UDim2.new(0.2, 0, 0.3, 0)
Frame.Position = UDim2.new(0.5, 0, 0.5, 0)
Frame.Name = "ButtonFrame"
Frame.Parent = ScreenGui

local TitleLabel = Instance.new("TextLabel")
TitleLabel.AnchorPoint = Vector2.new(0.5, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Name = "TitleLabel"
TitleLabel.Size = UDim2.new(1, -20, 0, 35)
TitleLabel.Position = UDim2.new(0.5, 0, 0, 10)
TitleLabel.Font = Enum.Font.SourceSansSemibold
TitleLabel.Text = "RippleButton Example"
TitleLabel.TextColor3 = Color.Black
TitleLabel.TextScaled = true
TitleLabel.TextTransparency = 0.129
TitleLabel.Parent = Frame

local FlatButton, OutlinedButton, ContainedButton, DisabledCheckbox do
	FlatButton = PseudoInstance.new("RippleButton")
	FlatButton.AnchorPoint = Vector2.new(0.5, 0.5)
	FlatButton.Size = UDim2.new(1, -30, 0, 35)
	FlatButton.Position = UDim2.new(0.5, 0, 0.5, -40)
	FlatButton.PrimaryColor3 = Color.Teal[500]
	FlatButton.Text = "FLAT BUTTON"
	FlatButton.Font = Enum.Font.SourceSansBold
	FlatButton.Style = Enumeration.ButtonStyle.Flat
	FlatButton.Parent = Frame
	
	OutlinedButton = PseudoInstance.new("RippleButton")
	OutlinedButton.AnchorPoint = Vector2.new(0.5, 0.5)
	OutlinedButton.Size = UDim2.new(1, -30, 0, 35)
	OutlinedButton.Position = UDim2.new(0.5, 0, 0.5, 0)
	OutlinedButton.PrimaryColor3 = Color.Teal[500]
	OutlinedButton.Text = "OUTLINED BUTTON"
	OutlinedButton.Font = Enum.Font.SourceSansBold
	OutlinedButton.Style = Enumeration.ButtonStyle.Outlined
	OutlinedButton.Parent = Frame
	
	ContainedButton = PseudoInstance.new("RippleButton")
	ContainedButton.AnchorPoint = Vector2.new(0.5, 0.5)
	ContainedButton.Size = UDim2.new(1, -30, 0, 35)
	ContainedButton.Position = UDim2.new(0.5, 0, 0.5, 40)
	ContainedButton.PrimaryColor3 = Color.Teal[500]
	ContainedButton.Text = "CONTAINED BUTTON"
	ContainedButton.Font = Enum.Font.SourceSansBold
	ContainedButton.BorderRadius = 4
	ContainedButton.Style = Enumeration.ButtonStyle.Contained
	ContainedButton.Parent = Frame
	
	local DisabledLabel = Instance.new("TextLabel")
	DisabledLabel.AnchorPoint = Vector2.new(0, 1)
	DisabledLabel.BackgroundTransparency = 1
	DisabledLabel.Name = "DisabledLabel"
	DisabledLabel.Size = UDim2.new(1, -55, 0, 35)
	DisabledLabel.Position = UDim2.new(0, 20, 1, -5)
	DisabledLabel.Font = Enum.Font.SourceSans
	DisabledLabel.Text = "DISABLED BUTTONS"
	DisabledLabel.TextColor3 = Color.Black
	DisabledLabel.TextSize = 18
	DisabledLabel.TextXAlignment = Enum.TextXAlignment.Left
	DisabledLabel.TextTransparency = 0.4
	DisabledLabel.Parent = Frame
	
	DisabledCheckbox = PseudoInstance.new("Checkbox")
	DisabledCheckbox.PrimaryColor3 = Color.Teal[500]
	DisabledCheckbox.Checked = false
	DisabledCheckbox.AnchorPoint = Vector2.new(1, 1)
	DisabledCheckbox.Position = UDim2.new(1, -10, 1, -10)
	DisabledCheckbox.Theme = Enumeration.MaterialTheme.Light
	DisabledCheckbox.ZIndex = 12
	DisabledCheckbox.Parent = Frame
end

FlatButton.OnPressed:Connect(function()
	NewSnackbar(SnackbarText:format("Flat"), ScreenGui)
end)

OutlinedButton.OnRightPressed:Connect(function()
	NewSnackbar(SnackbarText:format("Outlined"), ScreenGui)
end)

ContainedButton.OnMiddlePressed:Connect(function()
	NewSnackbar(SnackbarText:format("Contained"), ScreenGui)
end)

DisabledCheckbox.OnChecked:Connect(function(On)
	if not On then
		FlatButton.Disabled, OutlinedButton.Disabled, ContainedButton.Disabled = false, false, false
	else
		FlatButton.Disabled, OutlinedButton.Disabled, ContainedButton.Disabled = true, true, true
	end
end)
```
There is also a `Ripple` method which just plays a Ripple.
```lua
Submit:Ripple() -- Ripples :D
```

### Button Types
The three button types are "Flat", "Custom", and "Raised". The "Raised" type isn't perfect, as it is difficult to faithfully render in Roblox.

#### Flat
A `FlatButton` has one descendant by default, called `Corner`. This is the [ImageLabel](http://wiki.roblox.com/index.php?title=API:Class/ImageLabel) that overlays a 2dp corner image over your `FlatButton`. It's [ImageColor3](http://wiki.roblox.com/index.php?title=API:Class/GuiObject/ImageColor3) property is automatically set to `FlatButton.Parent.BackgroundColor3`. Whenever you set the `Parent` property, it will attempt to update to the aforementioned value. You can set it manually with the following:
```lua
FlatButton.Corner.ImageColor3 = Color3.fromRGB(255, 255, 255)
```

#### Custom
A `CustomButton` is exactly the same as a `FlatButton`, except its `Corner` object has its [ImageTransparency](http://wiki.roblox.com/index.php?title=API:Class/GuiObject/ImageTransparency) set to 0. Use this if you don't want visible corner overlays.

#### Raised
For when you want your buttons to lift.
