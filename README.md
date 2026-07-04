local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

--// Blur
local Blur = Instance.new("BlurEffect")
Blur.Size = 0
Blur.Parent = Lighting

--// ScreenGui (PlayerGui - legit)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BrainrotKillerV3_6_Legit"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

--// Main Window - a real solid rectangle with a subtle, clean rounded edge
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 960, 0, 460)
MainFrame.Position = UDim2.new(0.5, -480, 0.5, -230)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 12)
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true -- keeps everything inside the rectangle, so the border always shows
MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 14) -- small, clean rounding (matches reference)
local Stroke = Instance.new("UIStroke", MainFrame)
Stroke.Color = Color3.fromRGB(0, 170, 255)
Stroke.Thickness = 2
Stroke.Transparency = 0.1

local MainGradient = Instance.new("UIGradient", MainFrame)
MainGradient.Rotation = 90
MainGradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0, Color3.fromRGB(16, 16, 19)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(8, 8, 10)),
})

--// Top Bar - gets its own matching rounded corner (ClipsDescendants only clips
--// to a rectangle, so without this the TopBar's square top edge was painting
--// over MainFrame's rounded top corners).
local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 50)
TopBar.BackgroundColor3 = Color3.fromRGB(15, 15, 18)
TopBar.BorderSizePixel = 0
TopBar.Parent = MainFrame
Instance.new("UICorner", TopBar).CornerRadius = UDim.new(0, 14)

-- Flatten the TopBar's bottom corners (which UICorner also rounded) so it
-- meets the body with a clean straight edge instead of a rounded notch.
local TopBarFiller = Instance.new("Frame")
TopBarFiller.Size = UDim2.new(1, 0, 0, 14)
TopBarFiller.Position = UDim2.new(0, 0, 1, -14)
TopBarFiller.BackgroundColor3 = Color3.fromRGB(15, 15, 18)
TopBarFiller.BorderSizePixel = 0
TopBarFiller.ZIndex = 0
TopBarFiller.Parent = TopBar

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -120, 1, 0)
Title.Position = UDim2.new(0, 16, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "BRAINROT KILLER"
Title.TextColor3 = Color3.fromRGB(0, 200, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TopBar

--// Close Button
local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 40, 0, 32)
Close.Position = UDim2.new(1, -46, 0, 9)
Close.Text = "X"
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.TextColor3 = Color3.fromRGB(255, 90, 90)
Close.BackgroundColor3 = Color3.fromRGB(25,25,28)
Close.Parent = TopBar
Instance.new("UICorner", Close).CornerRadius = UDim.new(0, 8)

--// Content Holder - Horizontal Categories
-- ScrollingFrame (not a plain Frame): if more categories get added later than fit
-- in the window width, they now scroll into view with a thin scrollbar instead of
-- silently overflowing past MainFrame's clip boundary and becoming invisible
-- (which is exactly what happened to the "Others" tab earlier).
local Holder = Instance.new("ScrollingFrame")
Holder.Size = UDim2.new(1, -20, 1, -60)
Holder.Position = UDim2.new(0, 10, 0, 55)
Holder.BackgroundTransparency = 1
Holder.BorderSizePixel = 0
Holder.ScrollingDirection = Enum.ScrollingDirection.X
Holder.AutomaticCanvasSize = Enum.AutomaticSize.X
Holder.CanvasSize = UDim2.new(0, 0, 0, 0)
Holder.ScrollBarThickness = 4
Holder.ScrollBarImageColor3 = Color3.fromRGB(0, 170, 255)
Holder.Parent = MainFrame

local HolderLayout = Instance.new("UIListLayout", Holder)
HolderLayout.FillDirection = Enum.FillDirection.Horizontal
HolderLayout.SortOrder = Enum.SortOrder.LayoutOrder
HolderLayout.Padding = UDim.new(0, 16)
HolderLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
HolderLayout.VerticalAlignment = Enum.VerticalAlignment.Top
-- UIListLayout (unlike UIGridLayout) respects each category's own AutomaticSize,
-- so panels stack side-by-side by their real height instead of a forced 0-height cell.

--// Notifications (bottom right)
local Notifications = Instance.new("Frame")
Notifications.Size = UDim2.new(0, 280, 1, 0)
Notifications.Position = UDim2.new(1, 20, 0, 0)
Notifications.BackgroundTransparency = 1
Notifications.Parent = ScreenGui

local NotifLayout = Instance.new("UIListLayout", Notifications)
NotifLayout.Padding = UDim.new(0, 8)
NotifLayout.SortOrder = Enum.SortOrder.LayoutOrder
NotifLayout.VerticalAlignment = Enum.VerticalAlignment.Bottom

local Library = {}

function Library:Notify(title, message, duration)
	duration = duration or 3
	local notif = Instance.new("Frame")
	notif.Size = UDim2.new(1, 0, 0, 70)
	notif.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
	notif.Parent = Notifications
	Instance.new("UICorner", notif).CornerRadius = UDim.new(0, 12)
	local nStroke = Instance.new("UIStroke", notif)
	nStroke.Color = Color3.fromRGB(0, 170, 255)
	nStroke.Thickness = 1
	nStroke.Transparency = 0.6

	local nTitle = Instance.new("TextLabel")
	nTitle.Size = UDim2.new(1, -16, 0, 24)
	nTitle.Position = UDim2.new(0, 12, 0, 8)
	nTitle.BackgroundTransparency = 1
	nTitle.Text = title
	nTitle.TextColor3 = Color3.fromRGB(0, 200, 255)
	nTitle.Font = Enum.Font.GothamBold
	nTitle.TextSize = 14
	nTitle.TextXAlignment = Enum.TextXAlignment.Left
	nTitle.Parent = notif

	local nMsg = Instance.new("TextLabel")
	nMsg.Size = UDim2.new(1, -16, 0, 30)
	nMsg.Position = UDim2.new(0, 12, 0, 32)
	nMsg.BackgroundTransparency = 1
	nMsg.Text = message
	nMsg.TextColor3 = Color3.fromRGB(220, 220, 220)
	nMsg.Font = Enum.Font.Gotham
	nMsg.TextSize = 13
	nMsg.TextXAlignment = Enum.TextXAlignment.Left
	nMsg.TextWrapped = true
	nMsg.Parent = notif

	task.delay(duration, function()
		if notif.Parent then
			TweenService:Create(notif, TweenInfo.new(0.5), {BackgroundTransparency = 1}):Play()
			task.wait(0.5)
			notif:Destroy()
		end
	end)
end

--// Drag
local dragging, dragStart, startPos
TopBar.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position
	end
end)
TopBar.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end
end)
UserInputService.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(
			startPos.X.Scale, startPos.X.Offset + delta.X,
			startPos.Y.Scale, startPos.Y.Offset + delta.Y
		)
	end
end)

--// RightShift Toggle (hides/shows the whole panel)
local open = true
UserInputService.InputBegan:Connect(function(input, gp)
	if gp then return end
	if input.KeyCode == Enum.KeyCode.RightShift then
		open = not open
		if open then
			MainFrame.Visible = true
			TweenService:Create(Blur, TweenInfo.new(0.25), {Size = 10}):Play()
			Library:Notify("GUI Shown", "Window opened with RightShift.", 2)
		else
			TweenService:Create(Blur, TweenInfo.new(0.25), {Size = 0}):Play()
			task.delay(0.25, function()
				MainFrame.Visible = false
			end)
			Library:Notify("GUI Hidden", "Window hidden with RightShift.", 2)
		end
	end
end)

Close.MouseButton1Click:Connect(function()
	open = false
	TweenService:Create(Blur, TweenInfo.new(0.2), {Size = 0}):Play()
	MainFrame.Visible = false
	Library:Notify("GUI Closed", "Window hidden.", 2)
end)

--// Category (rounded square - unchanged)
function Library:CreateCategory(name, icon)
	local cat = Instance.new("Frame")
	cat.Size = UDim2.new(0, 296, 0, 0)
	cat.AutomaticSize = Enum.AutomaticSize.Y
	cat.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
	cat.Parent = Holder
	Instance.new("UICorner", cat).CornerRadius = UDim.new(0, 14)
	local s = Instance.new("UIStroke", cat)
	s.Color = Color3.fromRGB(0, 170, 255)
	s.Thickness = 1.1
	s.Transparency = 0.45

	cat.MouseEnter:Connect(function()
		TweenService:Create(s, TweenInfo.new(0.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Transparency = 0.15}):Play()
	end)
	cat.MouseLeave:Connect(function()
		TweenService:Create(s, TweenInfo.new(0.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Transparency = 0.45}):Play()
	end)

	local headerTextOffset = 12
	if icon then
		-- Big icon slot (10x a normal ~16px text glyph = ~48-56px box) to the left of the header text
		local iconLabel = Instance.new("TextLabel")
		iconLabel.Size = UDim2.new(0, 44, 0, 44)
		iconLabel.Position = UDim2.new(0, 8, 0, 4)
		iconLabel.BackgroundTransparency = 1
		iconLabel.Text = icon
		iconLabel.TextScaled = true
		iconLabel.Font = Enum.Font.GothamBold
		iconLabel.Parent = cat
		headerTextOffset = 58
	end

	local header = Instance.new("TextLabel")
	header.Size = UDim2.new(1, -20 - (headerTextOffset - 12), 0, 42)
	header.Position = UDim2.new(0, headerTextOffset, 0, 8)
	header.BackgroundTransparency = 1
	header.Text = name
	header.TextColor3 = Color3.fromRGB(255,255,255)
	header.Font = Enum.Font.GothamBold
	header.TextSize = 17
	header.TextXAlignment = Enum.TextXAlignment.Left
	header.Parent = cat

	local content = Instance.new("Frame")
	content.Size = UDim2.new(1, -24, 0, 0)
	content.Position = UDim2.new(0, 12, 0, 52)
	content.BackgroundTransparency = 1
	content.AutomaticSize = Enum.AutomaticSize.Y
	content.Parent = cat
	Instance.new("UIListLayout", content).Padding = UDim.new(0, 9)

	return content
end

--// Button with persistent blue lit circle (unchanged)
function Library:CreateButton(parent, text, callback)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(1, 0, 0, 52)
	b.BackgroundColor3 = Color3.fromRGB(22,22,26)
	b.Text = ""
	b.Parent = parent
	Instance.new("UICorner", b).CornerRadius = UDim.new(0, 12)

	local circle = Instance.new("Frame")
	circle.Size = UDim2.new(0, 28, 0, 28)
	circle.Position = UDim2.new(0, 12, 0.5, -14)
	circle.BackgroundColor3 = Color3.fromRGB(40,40,46)
	circle.Parent = b
	Instance.new("UICorner", circle).CornerRadius = UDim.new(1, 0)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -60, 1, 0)
	label.Position = UDim2.new(0, 52, 0, 0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.fromRGB(255,255,255)
	label.Font = Enum.Font.GothamSemibold
	label.TextSize = 15
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = b

	b.MouseEnter:Connect(function()
		TweenService:Create(b, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(30,30,36)}):Play()
	end)
	b.MouseLeave:Connect(function()
		TweenService:Create(b, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(22,22,26)}):Play()
	end)

	local circleOn = false
	local BTN_TWEEN = TweenInfo.new(0.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
	b.MouseButton1Click:Connect(function()
		TweenService:Create(b, BTN_TWEEN, {Size = UDim2.new(1, -4, 0, 50)}):Play()
		task.delay(0.12, function()
			TweenService:Create(b, BTN_TWEEN, {Size = UDim2.new(1, 0, 0, 52)}):Play()
		end)

		circleOn = not circleOn
		TweenService:Create(circle, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
			BackgroundColor3 = circleOn and Color3.fromRGB(0, 170, 255) or Color3.fromRGB(40,40,46)
		}):Play()

		if callback then
			callback(circleOn)
			Library:Notify("Action Executed", "Button '" .. text .. "': " .. (circleOn and "ON" or "OFF"), 2.5)
		end
	end)

	return b
end

--// Slider (matches the reference GUI's sliders: label + live value on top, draggable track below)
function Library:CreateSlider(parent, text, min, max, default, callback)
	local value = default or min

	local row = Instance.new("Frame")
	row.Size = UDim2.new(1, 0, 0, 46)
	row.BackgroundTransparency = 1
	row.Parent = parent

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -50, 0, 20)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.fromRGB(230,230,230)
	label.Font = Enum.Font.Gotham
	label.TextSize = 14
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = row

	local valueLabel = Instance.new("TextLabel")
	valueLabel.Size = UDim2.new(0, 50, 0, 20)
	valueLabel.Position = UDim2.new(1, -50, 0, 0)
	valueLabel.BackgroundTransparency = 1
	valueLabel.Text = tostring(value)
	valueLabel.TextColor3 = Color3.fromRGB(0, 200, 255)
	valueLabel.Font = Enum.Font.GothamBold
	valueLabel.TextSize = 14
	valueLabel.TextXAlignment = Enum.TextXAlignment.Right
	valueLabel.Parent = row

	local track = Instance.new("TextButton")
	track.Size = UDim2.new(1, 0, 0, 6)
	track.Position = UDim2.new(0, 0, 0, 30)
	track.BackgroundColor3 = Color3.fromRGB(35,35,40)
	track.Text = ""
	track.AutoButtonColor = false
	track.Parent = row
	Instance.new("UICorner", track).CornerRadius = UDim.new(1, 0)

	local fill = Instance.new("Frame")
	fill.Size = UDim2.new(0, 0, 1, 0)
	fill.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
	fill.Parent = track
	Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)

	local handle = Instance.new("Frame")
	handle.Size = UDim2.new(0, 14, 0, 14)
	handle.Position = UDim2.new(0, -7, 0.5, -7)
	handle.BackgroundColor3 = Color3.fromRGB(255,255,255)
	handle.Parent = track
	Instance.new("UICorner", handle).CornerRadius = UDim.new(1, 0)

	local function setFromAlpha(alpha)
		alpha = math.clamp(alpha, 0, 1)
		value = math.floor(min + (max - min) * alpha + 0.5)
		valueLabel.Text = tostring(value)
		local realAlpha = (value - min) / (max - min)
		fill.Size = UDim2.new(realAlpha, 0, 1, 0)
		handle.Position = UDim2.new(realAlpha, -7, 0.5, -7)
		if callback then callback(value) end
	end

	setFromAlpha((value - min) / (max - min))

	local dragging = false
	track.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			local alpha = (input.Position.X - track.AbsolutePosition.X) / track.AbsoluteSize.X
			setFromAlpha(alpha)
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			local alpha = (input.Position.X - track.AbsolutePosition.X) / track.AbsoluteSize.X
			setFromAlpha(alpha)
		end
	end)
	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)

	return row
end

--// Toggle Switch (pill style, matches the reference GUI's on/off switches)
function Library:CreateToggle(parent, text, default, callback)
	local state = default or false

	local row = Instance.new("Frame")
	row.Size = UDim2.new(1, 0, 0, 40)
	row.BackgroundTransparency = 1
	row.Parent = parent

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -60, 1, 0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.fromRGB(230,230,230)
	label.Font = Enum.Font.Gotham
	label.TextSize = 14
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = row

	local track = Instance.new("TextButton")
	track.Size = UDim2.new(0, 44, 0, 24)
	track.Position = UDim2.new(1, -44, 0.5, -12)
	track.BackgroundColor3 = Color3.fromRGB(35,35,40)
	track.Text = ""
	track.AutoButtonColor = false
	track.Parent = row
	Instance.new("UICorner", track).CornerRadius = UDim.new(1, 0)

	local knob = Instance.new("Frame")
	knob.Size = UDim2.new(0, 18, 0, 18)
	knob.Position = UDim2.new(0, 3, 0.5, -9)
	knob.BackgroundColor3 = Color3.fromRGB(220,220,220)
	knob.Parent = track
	Instance.new("UICorner", knob).CornerRadius = UDim.new(1, 0)

	local TOGGLE_TWEEN = TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
	local function update(instant)
		local trackColor = state and Color3.fromRGB(0,170,255) or Color3.fromRGB(35,35,40)
		local knobPos = state and UDim2.new(1, -21, 0.5, -9) or UDim2.new(0, 3, 0.5, -9)
		if instant then
			track.BackgroundColor3 = trackColor
			knob.Position = knobPos
		else
			TweenService:Create(track, TOGGLE_TWEEN, {BackgroundColor3 = trackColor}):Play()
			TweenService:Create(knob, TOGGLE_TWEEN, {Position = knobPos}):Play()
		end
		if callback then callback(state) end
	end

	track.MouseButton1Click:Connect(function()
		state = not state
		update(false)
		Library:Notify("Toggle Switched", text .. ": " .. (state and "ON" or "OFF"), 1.8)
	end)

	update(true)
	return row
end

--// Checkbox (unchanged)
function Library:CreateCheckbox(parent, text, default, callback)
	local state = default or false
	local box = Instance.new("TextButton")
	box.Size = UDim2.new(1, 0, 0, 40)
	box.BackgroundTransparency = 1
	box.Text = ""
	box.Parent = parent

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -60, 1, 0)
	label.Position = UDim2.new(0, 40, 0, 0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.fromRGB(230,230,230)
	label.Font = Enum.Font.Gotham
	label.TextSize = 14
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = box

	local indicator = Instance.new("Frame")
	indicator.Size = UDim2.new(0, 20, 0, 20)
	indicator.Position = UDim2.new(0, 10, 0.5, -10)
	indicator.BackgroundColor3 = Color3.fromRGB(30,30,34)
	indicator.Parent = box
	Instance.new("UICorner", indicator).CornerRadius = UDim.new(0, 6)

	local function update()
		indicator.BackgroundColor3 = state and Color3.fromRGB(0,170,255) or Color3.fromRGB(30,30,34)
		if callback then callback(state) end
	end

	box.MouseButton1Click:Connect(function()
		state = not state
		update()
		Library:Notify("Checkbox Toggled", text .. ": " .. tostring(state), 1.8)
	end)

	update()
	return box
end

--// Categories - Side by Side
local farm = Library:CreateCategory("Farm", "🌾")
Library:CreateToggle(farm, "Auto Farm enabled", false, function(state)
	print("Auto Farm:", state)
end)
Library:CreateSlider(farm, "Farm Speed", 1, 100, 50, function(val)
	print("Farm Speed set to", val)
end)

local misc = Library:CreateCategory("Misc", "🔧")
Library:CreateToggle(misc, "Reset Character", false, function()
	local char = Player.Character
	if char then char:BreakJoints() end
end)

local others = Library:CreateCategory("Others", "❓")
Library:CreateToggle(others, "Test Action", false, function()
	print("Others action")
end)

--// Launch
TweenService:Create(Blur, TweenInfo.new(0.25), {Size = 10}):Play()
Library:Notify("GUI Loaded", "Legit version ready. RightShift to toggle.", 4)
print("BRAINROT KILLER v3.6 LEGIT LOADED")
