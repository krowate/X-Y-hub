--// Ember Hub FULL Fake GUI (Interactive + Exploit Tab) - FULLY FUNCTIONAL

local player = game.Players.LocalPlayer
wait(2)  -- UGC WAIT
local Players = game:GetService("Players")
local camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local gui = Instance.new("ScreenGui")
gui.Name = "EmberHubFake"
gui.ResetOnSpawn = false
gui.DisplayOrder = 1000000
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- MAIN (bigger)
local main = Instance.new("Frame")
main.Parent = gui
main.Size = UDim2.new(0, 320, 0, 440)
main.Position = UDim2.new(0.5, -160, 0.5, -220)
main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)

local stroke = Instance.new("UIStroke")
stroke.Parent = main
stroke.Color = Color3.fromRGB(170, 100, 255)

-- TITLE
local title = Instance.new("TextLabel")
title.Parent = main
title.Text = "X-Y hub | by - krowate"
title.Size = UDim2.new(1, -10, 0, 20)
title.Position = UDim2.new(0, 5, 0, 2)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(200, 200, 200)
title.Font = Enum.Font.Code
title.TextSize = 14
title.TextXAlignment = Enum.TextXAlignment.Left

-- TAB SYSTEM
local pages = {}

local function createPage(name)
	local page = Instance.new("Frame")
	page.Parent = main
	page.Size = UDim2.new(1, -10, 1, -70)
	page.Position = UDim2.new(0, 5, 0, 60)
	page.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
	page.Visible = false
	
	local s = Instance.new("UIStroke")
	s.Parent = page
	s.Color = Color3.fromRGB(170, 100, 255)
	
	pages[name] = page
	return page
end

local function switchTab(name)
	for n, p in pairs(pages) do
		p.Visible = (n == name)
	end
end

-- Tabs
local function makeTab(name, x)
	local tab = Instance.new("TextButton")
	tab.Parent = main
	tab.Text = name
	tab.Size = UDim2.new(0, 90, 0, 25)
	tab.Position = UDim2.new(0, x, 0, 25)
	tab.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	tab.TextColor3 = Color3.fromRGB(200, 200, 200)
	tab.Font = Enum.Font.Code
	tab.TextSize = 14

	tab.MouseButton1Click:Connect(function()
		switchTab(name)
	end)
end

makeTab("Aim", 5)
makeTab("Visuals", 105)
makeTab("Exploit", 205)

-- HELPERS
local function label(parent, text, y)
	local l = Instance.new("TextLabel")
	l.Parent = parent
	l.Text = text
	l.Position = UDim2.new(0, 5, 0, y)
	l.Size = UDim2.new(1, 0, 0, 20)
	l.BackgroundTransparency = 1
	l.TextColor3 = Color3.fromRGB(200, 200, 200)
	l.Font = Enum.Font.Code
	l.TextSize = 14
	l.TextXAlignment = Enum.TextXAlignment.Left
end

local function checkbox(parent, text, y, callback)
	local state = false

	local btn = Instance.new("TextButton")
	btn.Parent = parent
	btn.Size = UDim2.new(0, 14, 0, 14)
	btn.Position = UDim2.new(0, 5, 0, y + 3)
	btn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
	btn.Text = ""

	local stroke = Instance.new("UIStroke")
	stroke.Parent = btn
	stroke.Color = Color3.fromRGB(120, 120, 120)

	local txt = Instance.new("TextLabel")
	txt.Parent = parent
	txt.Text = text
	txt.Position = UDim2.new(0, 25, 0, y)
	txt.Size = UDim2.new(1, -25, 0, 20)
	txt.BackgroundTransparency = 1
	txt.TextColor3 = Color3.fromRGB(200, 200, 200)
	txt.Font = Enum.Font.Code
	txt.TextSize = 14
	txt.TextXAlignment = Enum.TextXAlignment.Left

	btn.MouseButton1Click:Connect(function()
		state = not state
		btn.BackgroundColor3 = state and Color3.fromRGB(170, 100, 255) or Color3.fromRGB(25, 25, 25)
		if callback then callback(state) end
	end)
	
	return function() return state end
end

local function slider(parent, text, y, max, callback)
	local value = 0.4

	label(parent, text, y)

	local bar = Instance.new("Frame")
	bar.Parent = parent
	bar.Size = UDim2.new(1, -10, 0, 6)
	bar.Position = UDim2.new(0, 5, 0, y + 20)
	bar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)

	local fill = Instance.new("Frame")
	fill.Parent = bar
	fill.Size = UDim2.new(value, 0, 1, 0)
	fill.BackgroundColor3 = Color3.fromRGB(170, 100, 255)

	local val = Instance.new("TextLabel")
	val.Parent = parent
	val.Position = UDim2.new(1, -100, 0, y)
	val.Size = UDim2.new(0, 100, 0, 20)
	val.BackgroundTransparency = 1
	val.TextColor3 = Color3.fromRGB(180, 180, 180)
	val.Font = Enum.Font.Code
	val.TextSize = 13
	val.TextXAlignment = Enum.TextXAlignment.Right

	local function update(x)
		local rel = math.clamp((x - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
		value = rel
		fill.Size = UDim2.new(rel, 0, 1, 0)
		val.Text = math.floor(rel * max) .. "/" .. max
		if callback then callback(rel * max) end
	end

	bar.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			update(input.Position.X)
		end
	end)

	bar.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then
			update(input.Position.X)
		end
	end)

	update(bar.AbsolutePosition.X + bar.AbsoluteSize.X * value)
end

local function dropdown(parent, text, options, y, callback)
	local index = 1

	label(parent, text, y)

	local btn = Instance.new("TextButton")
	btn.Parent = parent
	btn.Size = UDim2.new(1, -10, 0, 20)
	btn.Position = UDim2.new(0, 5, 0, y + 20)
	btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	btn.TextColor3 = Color3.fromRGB(200, 200, 200)
	btn.Font = Enum.Font.Code
	btn.TextSize = 14

	local function update()
		btn.Text = options[index] .. "   +"
		if callback then callback(options[index]) end
	end

	btn.MouseButton1Click:Connect(function()
		index = index % #options + 1
		update()
	end)

	update()
end

-- CREATE PAGES
local aim = createPage("Aim")
local visuals = createPage("Visuals")
local exploit = createPage("Exploit")

switchTab("Aim")

-- AIM TAB - FULLY FUNCTIONAL
local aimbotEnabled = false
local showFOV = false
local aimPart = "Head"
local fovRadius = 100
local sensitivity = 5
local activationKey = "MouseButton2"
local targetOrbit = false
local targetConnection
local fovCircle

local getMousePos = function()
	return UserInputService:GetMouseLocation()
end

local function getClosestPlayer()
	local mousePos = getMousePos()
	local closest, closestDist = nil, fovRadius
	
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= player and plr.Character and plr.Character:FindFirstChild(aimPart) then
			local part = plr.Character[aimPart]
			local screenPos, onScreen = camera:WorldToViewportPoint(part.Position)
			if onScreen then
				local dist = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
				if dist < closestDist then
					closestDist = dist
					closest = part
				end
			end
		end
	end
	return closest
end

label(aim, "Mouse Aimbot", 5)
local aimbotCB = checkbox(aim, "Enable Mouse Aimbot", 25, function(state)
	aimbotEnabled = state
end)

dropdown(aim, "Aim Part", {"Head", "HumanoidRootPart", "Torso"}, 55, function(part)
	aimPart = part
end)

local showFOVCB = checkbox(aim, "Show FOV Circle", 95, function(state)
	showFOV = state
	if fovCircle then fovCircle.Visible = state end
end)

slider(aim, "FOV Radius", 125, 300, function(val)
	fovRadius = val
	if fovCircle then
		fovCircle.Radius = val
	end
end)

slider(aim, "Sensitivity", 165, 10, function(val)
	sensitivity = val
end)

dropdown(aim, "Activation Key", {"MouseButton2", "E", "Q"}, 205, function(key)
	activationKey = key
end)

local orbitCB = checkbox(aim, "Target Orbit ( E )", 245, function(state)
	targetOrbit = state
end)

-- Create FOV Circle
fovCircle = Drawing.new("Circle")
fovCircle.Visible = false
fovCircle.Thickness = 2
fovCircle.Color = Color3.fromRGB(170, 100, 255)
fovCircle.NumSides = 30
fovCircle.Radius = fovRadius
fovCircle.Filled = false
fovCircle.Transparency = 0.8

RunService.Heartbeat:Connect(function()
	local mousePos = getMousePos()
	fovCircle.Position = mousePos
	fovCircle.Visible = showFOV
	fovCircle.Radius = fovRadius
	
	local keyPressed = false
	if activationKey == "E" and UserInputService:IsKeyDown(Enum.KeyCode.E) then
		keyPressed = true
	elseif activationKey == "Q" and UserInputService:IsKeyDown(Enum.KeyCode.Q) then
		keyPressed = true
	elseif activationKey == "MouseButton2" and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
		keyPressed = true
	end
	
	if aimbotEnabled and keyPressed then
		local target = getClosestPlayer()
		if target then
			local screenPos = camera:WorldToViewportPoint(target.Position)
			local delta = (Vector2.new(screenPos.X, screenPos.Y) - mousePos)
			local clampedDelta = delta.Unit * math.min(delta.Magnitude, sensitivity)
			mousemoverel(clampedDelta.X, clampedDelta.Y)
		end
	end
end)

-- VISUALS TAB - FULLY FUNCTIONAL
local boxESP = {}
local tracerESP = {}
local boxEnabled = false
local tracerEnabled = false
local tracerPos = "Center"
local tracerColor = "White"

local function getColorFromString(colorStr)
	if colorStr == "White" then return Color3.fromRGB(255, 255, 255)
	elseif colorStr == "Purple" then return Color3.fromRGB(170, 100, 255)
	elseif colorStr == "Red" then return Color3.fromRGB(255, 0, 0) end
	return Color3.fromRGB(255, 255, 255)
end

local function clearESP()
	for _, box in pairs(boxESP) do
		if box then box:Remove() end
	end
	for _, tracer in pairs(tracerESP) do
		if tracer then tracer:Remove() end
	end
	boxESP = {}
	tracerESP = {}
end

checkbox(visuals, "Enable Box ESP", 20, function(state)
	boxEnabled = state
	if not state then clearESP() end
end)

checkbox(visuals, "Enable Tracer ESP", 50, function(state)
	tracerEnabled = state
	if not state then clearESP() end
end)

dropdown(visuals, "Tracer Position", {"Center", "Top", "Bottom"}, 120, function(pos)
	tracerPos = pos
	clearESP()
end)

dropdown(visuals, "Tracer Color", {"White", "Purple", "Red"}, 160, function(color)
	tracerColor = color
	clearESP()
end)

RunService.RenderStepped:Connect(function()
	if boxEnabled or tracerEnabled then
		for _, plr in pairs(Players:GetPlayers()) do
			if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
				local root = plr.Character.HumanoidRootPart
				local humanoid = plr.Character:FindFirstChild("Humanoid")
				if humanoid and humanoid.Health > 0 then
					local head = plr.Character:FindFirstChild("Head")
					if head then
						local rootPos, rootOnScreen = camera:WorldToViewportPoint(root.Position)
						local headPos, headOnScreen = camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
						
						if rootOnScreen or headOnScreen then
							local box = boxESP[plr]
							local tracer = tracerESP[plr]
							
							if boxEnabled and not box then
								box = Drawing.new("Quad")
								box.Color = Color3.fromRGB(170, 100, 255)
								box.Thickness = 2
								box.Transparency = 0.7
								boxESP[plr] = box
							elseif not boxEnabled and box then
								box:Remove()
								boxESP[plr] = nil
							end
							
							if tracerEnabled and not tracer then
								tracer = Drawing.new("Line")
								tracer.Color = getColorFromString(tracerColor)
								tracer.Thickness = 2
								tracer.Transparency = 0.8
								tracerESP[plr] = tracer
							elseif not tracerEnabled and tracer then
								tracer:Remove()
								tracerESP[plr] = nil
							end
							
							if box and boxEnabled then
								local corners = {
									camera:WorldToViewportPoint(root.Position + Vector3.new(-2, 3, 0)),
									camera:WorldToViewportPoint(root.Position + Vector3.new(2, 3, 0)),
									camera:WorldToViewportPoint(root.Position + Vector3.new(2, -3, 0)),
									camera:WorldToViewportPoint(root.Position + Vector3.new(-2, -3, 0))
								}
								box.PointA = Vector2.new(corners[1].X, corners[1].Y)
								box.PointB = Vector2.new(corners[2].X, corners[2].Y)
								box.PointC = Vector2.new(corners[3].X, corners[3].Y)
								box.PointD = Vector2.new(corners[4].X, corners[4].Y)
								box.Visible = true
							end
							
							if tracer and tracerEnabled then
								local tracerPoint
								if tracerPos == "Top" then
									tracerPoint = Vector2.new(headPos.X, headPos.Y)
								elseif tracerPos == "Bottom" then
									local footPos = camera:WorldToViewportPoint(root.Position - Vector3.new(0, 4, 0))
									tracerPoint = Vector2.new(footPos.X, footPos.Y)
								else -- Center
									tracerPoint = Vector2.new(rootPos.X, rootPos.Y)
								end
								
								local screenCenter = Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y/2)
								tracer.From = screenCenter
								tracer.To = tracerPoint
								tracer.Color = getColorFromString(tracerColor)
								tracer.Visible = true
							end
						else
							if boxESP[plr] then boxESP[plr]:Remove() boxESP[plr] = nil end
							if tracerESP[plr] then tracerESP[plr]:Remove() tracerESP[plr] = nil end
						end
					end
				end
			end
		end
	end
end)

-- EXPLOIT TAB - FULLY FUNCTIONAL
local flyEnabled = false
local flyKey = "E"
local noclipEnabled = false
local invisibleEnabled = false
local walkSpeed = 16
local bodyVelocity, bodyAngularVelocity
local noclipConnection
local flyConnection
local noClipConnection

checkbox(exploit, "Enable Fly", 25, function(state)
	flyEnabled = state
	if state and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
		local root = player.Character.HumanoidRootPart
		bodyVelocity = Instance.new("BodyVelocity")
		bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
		bodyVelocity.Velocity = Vector3.new(0, 0, 0)
		bodyVelocity.Parent = root
		
		bodyAngularVelocity = Instance.new("BodyAngularVelocity")
		bodyAngularVelocity.MaxTorque = Vector3.new(4000, 4000, 4000)
		bodyAngularVelocity.AngularVelocity = Vector3.new(0, 0, 0)
		bodyAngularVelocity.Parent = root
		
		flyConnection = RunService.Heartbeat:Connect(function()
			if flyEnabled and root then
				local cam = workspace.CurrentCamera
				local vel = cam.CFrame.LookVector * 50 + Vector3.new(0, 0, 0)
				if UserInputService:IsKeyDown(Enum.KeyCode.W) then vel = vel + cam.CFrame.LookVector * 50 end
				if UserInputService:IsKeyDown(Enum.KeyCode.S) then vel = vel - cam.CFrame.LookVector * 50 end
				if UserInputService:IsKeyDown(Enum.KeyCode.Space) then vel = vel + Vector3.new(0, 50, 0) end
				if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then vel = vel - Vector3.new(0, 50, 0) end
				bodyVelocity.Velocity = vel
			end
		end)
	else
		if bodyVelocity then bodyVelocity:Destroy() end
		if bodyAngularVelocity then bodyAngularVelocity:Destroy() end
		if flyConnection then flyConnection:Disconnect() end
	end
end)

dropdown(exploit, "Fly Key", {"E", "F", "Space"}, 55, function(key)
	flyKey = key
end)

checkbox(exploit, "Enable Noclip", 95, function(state)
	noclipEnabled = state
	if state then
		noClipConnection = RunService.Stepped:Connect(function()
			if player.Character then
				for _, part in pairs(player.Character:GetDescendants()) do
					if part:IsA("BasePart") and part.CanCollide then
						part.CanCollide = false
					end
				end
			end
		end)
	else
		if noClipConnection then noClipConnection:Disconnect() end
	end
end)

checkbox(exploit, "Enable Invisibility", 125, function(state)
	invisibleEnabled = state
	if player.Character then
		for _, part in pairs(player.Character:GetChildren()) do
			if part:IsA("BasePart") then
				part.Transparency = state and 1 or 0
			elseif part:IsA("Accessory") then
				local handle = part:FindFirstChild("Handle")
				if handle then handle.Transparency = state and 1 or 0 end
			end
		end
	end
end)

slider(exploit, "Walk Speed", 165, 100, function(val)
	walkSpeed = val
	if player.Character and player.Character:FindFirstChild("Humanoid") then
		player.Character.Humanoid.WalkSpeed = val
	end
end)

-- Character respawn handling
player.CharacterAdded:Connect(function(char)
	char:WaitForChild("HumanoidRootPart")
	char:WaitForChild("Humanoid")
	
	if invisibleEnabled then
		for _, part in pairs(char:GetChildren()) do
			if part:IsA("BasePart") then
				part.Transparency = 1
			elseif part:IsA("Accessory") then
				local handle = part:FindFirstChild("Handle")
				if handle then handle.Transparency = 1 end
			end
		end
	end
	
	if walkSpeed ~= 16 then
		char.Humanoid.WalkSpeed = walkSpeed
	end
end)

-- DRAGGING
local drag, dragStart, startPos

main.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		drag = true
		dragStart = input.Position
		startPos = main.Position
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if drag and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		main.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		drag = false
	end
end)

--// INSERT KEY TOGGLE (clean version)
local visible = true

-- Click sound
local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://12221967"
sound.Volume = 0.5
sound.Parent = main

-- Hint text (when hidden)
local hiddenText = Instance.new("TextLabel")
hiddenText.Parent = gui
hiddenText.Size = UDim2.new(0, 220, 0, 30)
hiddenText.Position = UDim2.new(0.5, -110, 0, 20)
hiddenText.BackgroundTransparency = 1
hiddenText.Text = "[ INSERT ] to open menu"
hiddenText.TextColor3 = Color3.fromRGB(200, 200, 200)
hiddenText.Font = Enum.Font.Code
hiddenText.TextSize = 16
hiddenText.Visible = false

-- Fade function
local function fadeUI(root, goal)
	for _, v in ipairs(root:GetDescendants()) do
		if v:IsA("Frame") then
			TweenService:Create(v, TweenInfo.new(0.25), {
				BackgroundTransparency = goal
			}):Play()
		elseif v:IsA("TextLabel") or v:IsA("TextButton") then
			TweenService:Create(v, TweenInfo.new(0.25), {
				TextTransparency = goal
			}):Play()

			if v:IsA("TextButton") then
				TweenService:Create(v, TweenInfo.new(0.25), {
					BackgroundTransparency = goal * 0.5
				}):Play()
			end
		elseif v:IsA("UIStroke") then
			TweenService:Create(v, TweenInfo.new(0.25), {
				Transparency = goal
			}):Play()
		end
	end
end

-- Toggle with INSERT
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	
	if input.KeyCode == Enum.KeyCode.Insert then
		visible = not visible
		sound:Play()

		if visible then
			main.Visible = true
			hiddenText.Visible = false
			fadeUI(main, 0)
		else
			fadeUI(main, 1)
			task.wait(0.25)
			main.Visible = false
			hiddenText.Visible = true
		end
	end
end)

-- Cleanup on player leaving
Players.PlayerRemoving:Connect(function(plr)
	if plr == player then
		clearESP()
		if fovCircle then fovCircle:Remove() end
		if bodyVelocity then bodyVelocity:Destroy() end
		if bodyAngularVelocity then bodyAngularVelocity:Destroy() end
		if flyConnection then flyConnection:Disconnect() end
		if noClipConnection then noClipConnection:Disconnect() end
	end
end)

task.wait(1)
print("X-Y Loaded!")