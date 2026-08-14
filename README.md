local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

local enabled = false
local fov = 150
local intensity = 0.25

--------------------------------------------------
-- GUI
--------------------------------------------------

local gui = Instance.new("ScreenGui")
gui.Name = "AimAssistGUI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Botão MENU
local menuButton = Instance.new("TextButton")
menuButton.Size = UDim2.fromOffset(65, 50)
menuButton.Position = UDim2.new(0, 15, 0.5, -25)
menuButton.Text = "DN SCRIPTS"
menuButton.TextScaled = true
menuButton.BackgroundColor3 = Color3.fromRGB(20,20,20)
menuButton.TextColor3 = Color3.new(1,1,1)
menuButton.Parent = gui

Instance.new("UICorner", menuButton).CornerRadius = UDim.new(0,10)

-- Menu
local menu = Instance.new("Frame")
menu.Size = UDim2.fromOffset(290, 250)
menu.Position = UDim2.new(0.5, -145, 0.5, -125)
menu.BackgroundColor3 = Color3.fromRGB(15,15,15)
menu.BorderSizePixel = 0
menu.Parent = gui

local menuCorner = Instance.new("UICorner")
menuCorner.CornerRadius = UDim.new(0,12)
menuCorner.Parent = menu

local border = Instance.new("UIStroke")
border.Color = Color3.fromRGB(255,255,255)
border.Thickness = 1
border.Parent = menu

--------------------------------------------------
-- TÍTULO / ÁREA DE ARRASTAR
--------------------------------------------------

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,45)
title.BackgroundTransparency = 1
title.Text = "🎯 AIMBOT"
title.TextColor3 = Color3.new(1,1,1)
title.TextScaled = true
title.Parent = menu

--------------------------------------------------
-- AIM ON/OFF
--------------------------------------------------

local aimButton = Instance.new("TextButton")
aimButton.Size = UDim2.new(1,-30,0,42)
aimButton.Position = UDim2.new(0,15,0,55)
aimButton.Text = "AIM ASSIST: OFF"
aimButton.TextScaled = true
aimButton.BackgroundColor3 = Color3.fromRGB(45,45,45)
aimButton.TextColor3 = Color3.new(1,1,1)
aimButton.Parent = menu

Instance.new("UICorner", aimButton).CornerRadius = UDim.new(0,8)

local function updateAimButton()
	if enabled then
		aimButton.Text = "AIM ASSIST: ON"
		aimButton.BackgroundColor3 = Color3.fromRGB(40,120,60)
	else
		aimButton.Text = "AIM ASSIST: OFF"
		aimButton.BackgroundColor3 = Color3.fromRGB(45,45,45)
	end
end

aimButton.MouseButton1Click:Connect(function()
	enabled = not enabled
	updateAimButton()
end)

--------------------------------------------------
-- FOV
--------------------------------------------------

local fovLabel = Instance.new("TextLabel")
fovLabel.Size = UDim2.new(1,-30,0,30)
fovLabel.Position = UDim2.new(0,15,0,105)
fovLabel.BackgroundTransparency = 1
fovLabel.Text = "FOV: 150"
fovLabel.TextColor3 = Color3.new(1,1,1)
fovLabel.TextScaled = true
fovLabel.Parent = menu

local fovMinus = Instance.new("TextButton")
fovMinus.Size = UDim2.fromOffset(55,35)
fovMinus.Position = UDim2.new(0,15,0,140)
fovMinus.Text = "-"
fovMinus.TextScaled = true
fovMinus.Parent = menu

local fovPlus = Instance.new("TextButton")
fovPlus.Size = UDim2.fromOffset(55,35)
fovPlus.Position = UDim2.new(1,-70,0,140)
fovPlus.Text = "+"
fovPlus.TextScaled = true
fovPlus.Parent = menu

fovMinus.MouseButton1Click:Connect(function()
	fov = math.max(50, fov - 10)
	fovLabel.Text = "FOV: "..fov
end)

fovPlus.MouseButton1Click:Connect(function()
	fov = math.min(500, fov + 10)
	fovLabel.Text = "FOV: "..fov
end)

--------------------------------------------------
-- INTENSIDADE
--------------------------------------------------

local intensityLabel = Instance.new("TextLabel")
intensityLabel.Size = UDim2.new(1,-30,0,30)
intensityLabel.Position = UDim2.new(0,15,0,180)
intensityLabel.BackgroundTransparency = 1
intensityLabel.Text = "INTENSIDADE: 25%"
intensityLabel.TextColor3 = Color3.new(1,1,1)
intensityLabel.TextScaled = true
intensityLabel.Parent = menu

local intensityMinus = Instance.new("TextButton")
intensityMinus.Size = UDim2.fromOffset(55,35)
intensityMinus.Position = UDim2.new(0,15,0,215)
intensityMinus.Text = "-"
intensityMinus.TextScaled = true
intensityMinus.Parent = menu

local intensityPlus = Instance.new("TextButton")
intensityPlus.Size = UDim2.fromOffset(55,35)
intensityPlus.Position = UDim2.new(1,-70,0,215)
intensityPlus.Text = "+"
intensityPlus.TextScaled = true
intensityPlus.Parent = menu

intensityMinus.MouseButton1Click:Connect(function()
	intensity = math.max(0.05, intensity - 0.05)
	intensityLabel.Text =
		"INTENSIDADE: "..math.floor(intensity * 100).."%"
end)

intensityPlus.MouseButton1Click:Connect(function()
	intensity = math.min(1, intensity + 0.05)
	intensityLabel.Text =
		"INTENSIDADE: "..math.floor(intensity * 100).."%"
end)

--------------------------------------------------
-- ABRIR / FECHAR
--------------------------------------------------

menuButton.MouseButton1Click:Connect(function()
	menu.Visible = not menu.Visible
end)

--------------------------------------------------
-- TECLA Q NO PC
--------------------------------------------------

UserInputService.InputBegan:Connect(function(input, processed)

	if processed then
		return
	end

	if input.KeyCode == Enum.KeyCode.Q then
		enabled = not enabled
		updateAimButton()
	end
end)

--------------------------------------------------
-- ARRASTAR NO PC E CELULAR
--------------------------------------------------

local dragging = false
local dragStart
local startPosition

title.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		dragging = true
		dragStart = input.Position
		startPosition = menu.Position

		input.Changed:Connect(function()

			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

UserInputService.InputChanged:Connect(function(input)

	if not dragging then
		return
	end

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		local delta = input.Position - dragStart

		menu.Position = UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + delta.X,
			startPosition.Y.Scale,
			startPosition.Y.Offset + delta.Y
		)
	end
end)

--------------------------------------------------
-- ENCONTRAR ALVO
--------------------------------------------------

local function getTarget()

	local closestTarget = nil
	local closestDistance = fov

	local screenCenter = Vector2.new(
		camera.ViewportSize.X / 2,
		camera.ViewportSize.Y / 2
	)

	for _, targetPlayer in ipairs(Players:GetPlayers()) do

		if targetPlayer ~= player and targetPlayer.Character then

			local humanoid =
				targetPlayer.Character:FindFirstChildOfClass("Humanoid")

			local root =
				targetPlayer.Character:FindFirstChild("HumanoidRootPart")

			if humanoid and root and humanoid.Health > 0 then

				local screenPosition, visible =
					camera:WorldToViewportPoint(root.Position)

				if visible then

					local distance =
						(Vector2.new(
							screenPosition.X,
							screenPosition.Y
						) - screenCenter).Magnitude

					if distance < closestDistance then
						closestDistance = distance
						closestTarget = root
					end
				end
			end
		end
	end

	return closestTarget
end

--------------------------------------------------
-- AIM ASSIST
--------------------------------------------------

RunService.RenderStepped:Connect(function()

	if not enabled then
		return
	end

	local target = getTarget()

	if target then

		local cameraPosition = camera.CFrame.Position

		local targetCFrame =
			CFrame.lookAt(cameraPosition, target.Position)

		camera.CFrame =
			camera.CFrame:Lerp(targetCFrame, intensity)
	end
end)
