-- CACAZITO HUB - BETA
-- KILL ALL (Hitbox 5s) + Título colorido + Botão flutuante (Delta/Tablet)

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- Mensagem no chat ao executar
pcall(function()
	ReplicatedStorage.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(
		"CACAZITO HUB INJETADO",
		"All"
	)
end)

-- =========================
-- GUI PRINCIPAL
-- =========================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CacazitoHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui

local Main = Instance.new("Frame")
Main.Parent = ScreenGui
Main.Size = UDim2.new(0,320,0,200)
Main.Position = UDim2.new(0.5,-160,0.5,-100)
Main.BackgroundColor3 = Color3.fromRGB(25,25,25)
Main.BorderSizePixel = 0
Main.Visible = true
Main.Active = true
Main.Draggable = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0,18)

-- =========================
-- TÍTULO COLORIDO
-- =========================
local Title = Instance.new("TextLabel")
Title.Parent = Main
Title.Size = UDim2.new(1,0,0,50)
Title.BackgroundTransparency = 1
Title.Text = "CACAZITO HUB"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 26
Title.TextColor3 = Color3.fromRGB(255,255,255)

local Gradient = Instance.new("UIGradient", Title)
Gradient.Color = ColorSequence.new{
	ColorSequenceKeypoint.new(0, Color3.fromRGB(255,0,255)),
	ColorSequenceKeypoint.new(0.5, Color3.fromRGB(180,0,255)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(255,0,180)),
}

RunService.RenderStepped:Connect(function()
	Gradient.Rotation = (Gradient.Rotation + 1) % 360
end)

-- =========================
-- BOTÃO KILL ALL
-- =========================
local KillBtn = Instance.new("TextButton")
KillBtn.Parent = Main
KillBtn.Size = UDim2.new(0,260,0,60)
KillBtn.Position = UDim2.new(0.5,-130,0.5,-10)
KillBtn.BackgroundColor3 = Color3.fromRGB(120,30,30)
KillBtn.Text = "KILL ALL"
KillBtn.Font = Enum.Font.GothamBold
KillBtn.TextSize = 22
KillBtn.TextColor3 = Color3.fromRGB(255,255,255)
KillBtn.BorderSizePixel = 0
Instance.new("UICorner", KillBtn).CornerRadius = UDim.new(0,14)

local cooldown = false

KillBtn.MouseButton1Click:Connect(function()
	if cooldown then return end
	cooldown = true

	local char = LocalPlayer.Character
	if not char then cooldown = false return end

	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hrp then cooldown = false return end

	KillBtn.Text = "KILL ALL [ATIVO]"

	local oldSize = hrp.Size
	local oldTransparency = hrp.Transparency

	hrp.Size = Vector3.new(60,60,60)
	hrp.Transparency = 0.7
	hrp.CanCollide = false

	task.wait(5)

	if hrp then
		hrp.Size = oldSize
		hrp.Transparency = oldTransparency
	end

	KillBtn.Text = "KILL ALL"
	cooldown = false
end)

-- =========================
-- BOTÃO FLUTUANTE (IMAGEM)
-- =========================
local FloatBtn = Instance.new("ImageButton")
FloatBtn.Parent = ScreenGui
FloatBtn.Size = UDim2.new(0,70,0,70)
FloatBtn.Position = UDim2.new(0,20,0.5,-35)
FloatBtn.BackgroundTransparency = 1
FloatBtn.Image = "rbxassetid://132701404902042"
FloatBtn.Active = true
FloatBtn.AutoButtonColor = true
Instance.new("UICorner", FloatBtn).CornerRadius = UDim.new(1,0)

-- abrir / fechar hub
FloatBtn.Activated:Connect(function()
	Main.Visible = not Main.Visible
end)

-- arrastar (TOUCH - DELTA)
local dragging = false
local dragStart, startPos

FloatBtn.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = FloatBtn.Position
	end
end)

FloatBtn.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.Touch then
		local delta = input.Position - dragStart
		FloatBtn.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)
