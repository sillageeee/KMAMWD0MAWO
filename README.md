-- Carrega Orion Library
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Orion/main/source"))()

-- Variáveis para configuração
_G.AimbotEnabled = false
_G.ShowFOV = true
_G.Smoothness = 0.1
_G.TeamCheck = true
_G.WallCheck = true
_G.AimPart = "Head"

-- Criar janela
local Window = OrionLib:MakeWindow({Name = "Tryhard Aimbot", HidePremium = false, SaveConfig = true, ConfigFolder = "TryhardAimbot"})

-- Criar aba Aimbot
local AimbotTab = Window:MakeTab({
	Name = "Aimbot",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})

-- Toggle do Aimbot
AimbotTab:AddToggle({
	Name = "Aimbot Ativado",
	Default = false,
	Save = true,
	Callback = function(Value)
		_G.AimbotEnabled = Value
	end    
})

-- Toggle FOV
AimbotTab:AddToggle({
	Name = "Mostrar FOV",
	Default = true,
	Save = true,
	Callback = function(Value)
		_G.ShowFOV = Value
	end    
})

-- Slider Suavidade
AimbotTab:AddSlider({
	Name = "Suavidade do Aimbot",
	Min = 0,
	Max = 1,
	Default = 0.1,
	Increment = 0.01,
	ValueName = "",
	Callback = function(Value)
		_G.Smoothness = Value
	end    
})

-- Toggle Team Check
AimbotTab:AddToggle({
	Name = "Team Check",
	Default = true,
	Save = true,
	Callback = function(Value)
		_G.TeamCheck = Value
	end    
})

-- Toggle WallCheck
AimbotTab:AddToggle({
	Name = "Wall Check",
	Default = true,
	Save = true,
	Callback = function(Value)
		_G.WallCheck = Value
	end    
})

-- Dropdown AimPart
AimbotTab:AddDropdown({
	Name = "Parte para Mirar",
	Default = "Head",
	Options = {"Head", "UpperTorso", "HumanoidRootPart"},
	Callback = function(Value)
		_G.AimPart = Value
	end    
})
