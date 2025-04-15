local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/jensonhirst/Orion/main/source')))()

local Window = OrionLib:MakeWindow({
	Name = "Xenz Hub",
	HidePremium = false,
	SaveConfig = true,
	ConfigFolder = "Xenz Hub"
})

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- Variáveis globais
_G.ESPEnabled = false
_G.ESPShowName = true
_G.ESPShowHealth = true
_G.ESPShowDistance = true
_G.ESPTeamColor = true

-- Aba Visual
local VisualTab = Window:MakeTab({
	Name = "Visual",
	Icon = "rbxassetid://6031265976",
	PremiumOnly = false
})

-- Toggles da UI
VisualTab:AddToggle({
	Name = "Ativar ESP",
	Default = false,
	Callback = function(Value)
		_G.ESPEnabled = Value
	end
})

VisualTab:AddToggle({
	Name = "Mostrar Nome",
	Default = true,
	Callback = function(Value)
		_G.ESPShowName = Value
	end
})

VisualTab:AddToggle({
	Name = "Mostrar Vida",
	Default = true,
	Callback = function(Value)
		_G.ESPShowHealth = Value
	end
})

VisualTab:AddToggle({
	Name = "Mostrar Distância",
	Default = true,
	Callback = function(Value)
		_G.ESPShowDistance = Value
	end
})

VisualTab:AddToggle({
	Name = "Cor por Time",
	Default = true,
	Callback = function(Value)
		_G.ESPTeamColor = Value
	end
})

-- Tabela para armazenar ESPs ativos
local ESPObjects = {}

-- Função de desenhar ESP
function CreateESP(player)
	if ESPObjects[player] then return end
	local box = Drawing.new("Square")
	local name = Drawing.new("Text")
	local health = Drawing.new("Text")
	local distance = Drawing.new("Text")

	box.Thickness = 1
	box.Filled = false

	name.Size = 13
	health.Size = 13
	distance.Size = 13

	name.Center = true
	health.Center = true
	distance.Center = true

	name.Outline = true
	health.Outline = true
	distance.Outline = true

	ESPObjects[player] = {
		Box = box,
		Name = name,
		Health = health,
		Distance = distance
	}
end

function RemoveESP(player)
	if ESPObjects[player] then
		for _, drawing in pairs(ESPObjects[player]) do
			drawing:Remove()
		end
		ESPObjects[player] = nil
	end
end

Players.PlayerRemoving:Connect(RemoveESP)

-- ESP Loop
RunService.RenderStepped:Connect(function()
	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") then
			CreateESP(player)

			local esp = ESPObjects[player]
			local hrp = player.Character.HumanoidRootPart
			local hum = player.Character.Humanoid
			local pos, onscreen = Camera:WorldToViewportPoint(hrp.Position)

			if _G.ESPEnabled and onscreen then
				local size = Vector3.new(2, 3, 1.5)
				local topLeft = Camera:WorldToViewportPoint(hrp.Position + Vector3.new(-size.X, size.Y, 0))
				local bottomRight = Camera:WorldToViewportPoint(hrp.Position + Vector3.new(size.X, -size.Y, 0))
				local width = math.abs(topLeft.X - bottomRight.X)
				local height = math.abs(topLeft.Y - bottomRight.Y)

				local color = _G.ESPTeamColor and player.TeamColor.Color or Color3.new(1, 1, 1)

				-- Caixa
				esp.Box.Visible = true
				esp.Box.Color = color
				esp.Box.Size = Vector2.new(width, height)
				esp.Box.Position = Vector2.new(pos.X - width / 2, pos.Y - height / 2)

				-- Nome
				esp.Name.Visible = _G.ESPShowName
				esp.Name.Color = color
				esp.Name.Position = Vector2.new(pos.X, pos.Y - height / 2 - 15)
				esp.Name.Text = player.Name

				-- Vida
				esp.Health.Visible = _G.ESPShowHealth
				esp.Health.Color = Color3.fromRGB(0, 255, 0)
				esp.Health.Position = Vector2.new(pos.X, pos.Y + height / 2 + 5)
				esp.Health.Text = "HP: " .. math.floor(hum.Health)

				-- Distância
				local dist = (Camera.CFrame.Position - hrp.Position).Magnitude
				esp.Distance.Visible = _G.ESPShowDistance
				esp.Distance.Color = Color3.fromRGB(255, 255, 0)
				esp.Distance.Position = Vector2.new(pos.X, pos.Y + height / 2 + 20)
				esp.Distance.Text = "Dist: " .. math.floor(dist)
			else
				for _, v in pairs(esp) do
					v.Visible = false
				end
			end
		else
			RemoveESP(player)
		end
	end
end)
