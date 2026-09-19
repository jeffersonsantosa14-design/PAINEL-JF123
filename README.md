--========================================================
-- JF PAINEL
-- ESP + BARRA DE VIDA + FOV + MIRA + TP + TOOLS
-- SEM ArmaPdsecreta
--========================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

--========================================================
-- CONFIG
--========================================================

local ESP_ATIVADO = false
local FOV_ATIVADO = false
local BARRA_VIDA_ATIVADA = false
local MIRA_ATIVADA = false
local VIDA_INFINITA = false

local PARTE_MIRA = "Head"
local TAMANHO_FOV = 150
local SUAVIDADE_MIRA = 0.18

local spawnMarcado = nil
local marcadorSpawn = nil
local conexaoVida = nil
local personagemProtegido = nil

--========================================================
-- REMOVE PAINEL ANTIGO
--========================================================

local antigo = PlayerGui:FindFirstChild("JF_Painel")

if antigo then
	antigo:Destroy()
end

--========================================================
-- GUI
--========================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = "JF_Painel"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Gui.Parent = PlayerGui

--========================================================
-- PAINEL
--========================================================

local Painel = Instance.new("Frame")
Painel.Name = "Painel"
Painel.Size = UDim2.fromOffset(600, 390)
Painel.Position = UDim2.new(0.5, -300, 0.5, -195)
Painel.BackgroundColor3 = Color3.fromRGB(27,27,27)
Painel.BorderSizePixel = 0
Painel.Parent = Gui

local PCorner = Instance.new("UICorner")
PCorner.CornerRadius = UDim.new(0,10)
PCorner.Parent = Painel

--========================================================
-- TOPO
--========================================================

local Topo = Instance.new("Frame")
Topo.Size = UDim2.new(1,0,0,50)
Topo.BackgroundColor3 = Color3.fromRGB(35,35,35)
Topo.BorderSizePixel = 0
Topo.Parent = Painel

local TopoCorner = Instance.new("UICorner")
TopoCorner.CornerRadius = UDim.new(0,10)
TopoCorner.Parent = Topo

local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1,-100,1,0)
Titulo.Position = UDim2.fromOffset(15,0)
Titulo.BackgroundTransparency = 1
Titulo.Text = "JF PAINEL"
Titulo.TextColor3 = Color3.new(1,1,1)
Titulo.TextSize = 20
Titulo.Font = Enum.Font.GothamBold
Titulo.TextXAlignment = Enum.TextXAlignment.Left
Titulo.Parent = Topo

--========================================================
-- FECHAR
--========================================================

local Fechar = Instance.new("TextButton")
Fechar.Size = UDim2.fromOffset(38,38)
Fechar.Position = UDim2.new(1,-45,0,6)
Fechar.BackgroundColor3 = Color3.fromRGB(160,35,35)
Fechar.Text = "X"
Fechar.TextColor3 = Color3.new(1,1,1)
Fechar.TextSize = 16
Fechar.Font = Enum.Font.GothamBold
Fechar.Parent = Topo

Instance.new("UICorner",Fechar).CornerRadius = UDim.new(0,7)

Fechar.MouseButton1Click:Connect(function()
	Gui.Enabled = false
end)

--========================================================
-- MENU
--========================================================

local Menu = Instance.new("Frame")
Menu.Size = UDim2.new(0,145,1,-60)
Menu.Position = UDim2.fromOffset(10,55)
Menu.BackgroundColor3 = Color3.fromRGB(31,31,31)
Menu.BorderSizePixel = 0
Menu.Parent = Painel

Instance.new("UICorner",Menu).CornerRadius = UDim.new(0,8)

local MenuLayout = Instance.new("UIListLayout")
MenuLayout.Padding = UDim.new(0,6)
MenuLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
MenuLayout.SortOrder = Enum.SortOrder.LayoutOrder
MenuLayout.Parent = Menu

local MenuPadding = Instance.new("UIPadding")
MenuPadding.PaddingTop = UDim.new(0,10)
MenuPadding.PaddingLeft = UDim.new(0,7)
MenuPadding.PaddingRight = UDim.new(0,7)
MenuPadding.Parent = Menu

--========================================================
-- ÁREA
--========================================================

local Area = Instance.new("Frame")
Area.Name = "Area"
Area.Size = UDim2.new(1,-170,1,-60)
Area.Position = UDim2.fromOffset(160,55)
Area.BackgroundTransparency = 1
Area.Parent = Painel

--========================================================
-- CRIAR BOTÃO
--========================================================

local function CriarBotaoMenu(texto)

	local b = Instance.new("TextButton")
	b.Size = UDim2.new(1,0,0,42)
	b.BackgroundColor3 = Color3.fromRGB(45,45,45)
	b.BorderSizePixel = 0
	b.Text = texto
	b.TextColor3 = Color3.new(1,1,1)
	b.TextSize = 13
	b.Font = Enum.Font.GothamBold
	b.Parent = Menu

	Instance.new("UICorner",b).CornerRadius = UDim.new(0,7)

	return b
end

local HomeButton = CriarBotaoMenu("HOME")
local TPButton = CriarBotaoMenu("TP PLAYER")
local ToolsButton = CriarBotaoMenu("TOOLS")
local ConfigButton = CriarBotaoMenu("CONFIGURAÇÕES")

--========================================================
-- LIMPAR ÁREA
--========================================================

local function LimparArea()

	for _,v in ipairs(Area:GetChildren()) do
		v:Destroy()
	end
end

--========================================================
-- TÍTULO DA ÁREA
--========================================================

local function TituloArea(texto)

	local t = Instance.new("TextLabel")
	t.Size = UDim2.new(1,0,0,35)
	t.BackgroundTransparency = 1
	t.Text = texto
	t.TextColor3 = Color3.new(1,1,1)
	t.TextSize = 18
	t.Font = Enum.Font.GothamBold
	t.TextXAlignment = Enum.TextXAlignment.Left
	t.Parent = Area

	return t
end

--========================================================
-- TOGGLE
--========================================================

local function CriarToggle(texto, estado, callback, posY)

	local botao = Instance.new("TextButton")
	botao.Size = UDim2.new(1,-10,0,38)
	botao.Position = UDim2.fromOffset(5,posY)
	botao.BackgroundColor3 = estado
		and Color3.fromRGB(45,120,65)
		or Color3.fromRGB(45,45,45)

	botao.BorderSizePixel = 0
	botao.Text = texto .. " : " .. (estado and "ON" or "OFF")
	botao.TextColor3 = Color3.new(1,1,1)
	botao.TextSize = 13
	botao.Font = Enum.Font.GothamBold
	botao.Parent = Area

	Instance.new("UICorner",botao).CornerRadius = UDim.new(0,7)

	botao.MouseButton1Click:Connect(function()

		estado = not estado

		botao.BackgroundColor3 = estado
			and Color3.fromRGB(45,120,65)
			or Color3.fromRGB(45,45,45)

		botao.Text = texto .. " : " .. (estado and "ON" or "OFF")

		callback(estado)
	end)

	return botao
end

--========================================================
-- ESP
--========================================================

local ESPs = {}

local function RemoverESP(player)

	local data = ESPs[player]

	if data then

		if data.Gui then
			data.Gui:Destroy()
		end

		ESPs[player] = nil
	end
end

local function CriarESP(player)

	if player == LocalPlayer then
		return
	end

	RemoverESP(player)

	local Billboard = Instance.new("BillboardGui")
	Billboard.Name = "JF_ESP"
	Billboard.Size = UDim2.fromOffset(70,100)
	Billboard.AlwaysOnTop = true
	Billboard.StudsOffset = Vector3.new(0,0,0)
	Billboard.Enabled = ESP_ATIVADO
	Billboard.Parent = PlayerGui

	local Caixa = Instance.new("Frame")
	Caixa.Size = UDim2.fromScale(1,1)
	Caixa.BackgroundTransparency = 1
	Caixa.BorderSizePixel = 0
	Caixa.Parent = Billboard

	local Stroke = Instance.new("UIStroke")
	Stroke.Color = Color3.fromRGB(255,50,50)
	Stroke.Thickness = 2
	Stroke.Parent = Caixa

	local VidaFundo = Instance.new("Frame")
	VidaFundo.Size = UDim2.new(0,5,1,0)
	VidaFundo.Position = UDim2.new(0,-8,0,0)
	VidaFundo.BackgroundColor3 = Color3.fromRGB(60,60,60)
	VidaFundo.BorderSizePixel = 0
	VidaFundo.Parent = Caixa

	local Vida = Instance.new("Frame")
	Vida.Name = "Vida"
	Vida.AnchorPoint = Vector2.new(0,1)
	Vida.Position = UDim2.new(0,0,1,0)
	Vida.Size = UDim2.new(1,0,1,0)
	Vida.BackgroundColor3 = Color3.fromRGB(50,220,80)
	Vida.BorderSizePixel = 0
	Vida.Parent = VidaFundo

	ESPs[player] = {
		Gui = Billboard,
		Caixa = Caixa,
		Vida = Vida
	}
end

local function AtualizarESP()

	for player,data in pairs(ESPs) do

		if not player.Parent then
			RemoverESP(player)
			continue
		end

		local char = player.Character
		local humanoid = char and char:FindFirstChildOfClass("Humanoid")
		local root = char and char:FindFirstChild("HumanoidRootPart")

		if root and humanoid then

			data.Gui.Adornee = root
			data.Gui.Enabled = ESP_ATIVADO

			local porcentagem = math.clamp(
				humanoid.Health / math.max(humanoid.MaxHealth,1),
				0,
				1
			)

			data.Vida.Size = UDim2.new(
				1,
				0,
				porcentagem,
				0
			)

			data.Vida.BackgroundColor3 =
				porcentagem > 0.5
				and Color3.fromRGB(50,220,80)
				or porcentagem > 0.25
				and Color3.fromRGB(255,190,40)
				or Color3.fromRGB(220,40,40)

		else

			data.Gui.Enabled = false
		end
	end
end

for _,player in ipairs(Players:GetPlayers()) do
	CriarESP(player)
end

Players.PlayerAdded:Connect(function(player)

	CriarESP(player)

	player.CharacterAdded:Connect(function()
		task.wait(1)
		CriarESP(player)
	end)
end)

Players.PlayerRemoving:Connect(RemoverESP)

--========================================================
-- FOV
--========================================================

local FOVCircle = Instance.new("Frame")
FOVCircle.Name = "FOV"
FOVCircle.AnchorPoint = Vector2.new(0.5,0.5)
FOVCircle.Position = UDim2.fromScale(0.5,0.5)
FOVCircle.Size = UDim2.fromOffset(TAMANHO_FOV,TAMANHO_FOV)
FOVCircle.BackgroundTransparency = 1
FOVCircle.Visible = false
FOVCircle.Parent = Gui

local FOVCorner = Instance.new("UICorner")
FOVCorner.CornerRadius = UDim.new(1,0)
FOVCorner.Parent = FOVCircle

local FOVStroke = Instance.new("UIStroke")
FOVStroke.Color = Color3.fromRGB(255,60,60)
FOVStroke.Thickness = 2
FOVStroke.Parent = FOVCircle

--========================================================
-- HOME
--========================================================

local function AbrirHome()

	LimparArea()

	TituloArea("CONTROLES")

	CriarToggle("ESP • CAIXA",ESP_ATIVADO,function(v)
		ESP_ATIVADO = v
	end,45)

	CriarToggle("FOV",FOV_ATIVADO,function(v)

		FOV_ATIVADO = v
		FOVCircle.Visible = v

	end,90)

	CriarToggle("BARRA DE VIDA",BARRA_VIDA_ATIVADA,function(v)

		BARRA_VIDA_ATIVADA = v

	end,135)

	CriarToggle("MIRA ASSISTIDA",MIRA_ATIVADA,function(v)

		MIRA_ATIVADA = v

	end,180)

	local FovTexto = Instance.new("TextLabel")
	FovTexto.Size = UDim2.new(1,-10,0,30)
	FovTexto.Position = UDim2.fromOffset(5,225)
	FovTexto.BackgroundTransparency = 1
	FovTexto.Text = "Tamanho FOV: "..TAMANHO_FOV
	FovTexto.TextColor3 = Color3.new(1,1,1)
	FovTexto.TextSize = 13
	FovTexto.Font = Enum.Font.GothamBold
	FovTexto.TextXAlignment = Enum.TextXAlignment.Left
	FovTexto.Parent = Area

	local Menos = Instance.new("TextButton")
	Menos.Size = UDim2.fromOffset(40,35)
	Menos.Position = UDim2.fromOffset(5,260)
	Menos.Text = "-"
	Menos.TextSize = 18
	Menos.TextColor3 = Color3.new(1,1,1)
	Menos.BackgroundColor3 = Color3.fromRGB(50,50,50)
	Menos.Parent = Area

	local Mais = Instance.new("TextButton")
	Mais.Size = UDim2.fromOffset(40,35)
	Mais.Position = UDim2.fromOffset(50,260)
	Mais.Text = "+"
	Mais.TextSize = 18
	Mais.TextColor3 = Color3.new(1,1,1)
	Mais.BackgroundColor3 = Color3.fromRGB(50,50,50)
	Mais.Parent = Area

	Menos.MouseButton1Click:Connect(function()

		TAMANHO_FOV = math.max(50,TAMANHO_FOV-10)

		FOVCircle.Size = UDim2.fromOffset(
			TAMANHO_FOV,
			TAMANHO_FOV
		)

		FovTexto.Text = "Tamanho FOV: "..TAMANHO_FOV
	end)

	Mais.MouseButton1Click:Connect(function()

		TAMANHO_FOV = math.min(400,TAMANHO_FOV+10)

		FOVCircle.Size = UDim2.fromOffset(
			TAMANHO_FOV,
			TAMANHO_FOV
		)

		FovTexto.Text = "Tamanho FOV: "..TAMANHO_FOV
	end)
end

--========================================================
-- TP PLAYER
--========================================================

local function AbrirTP()

	LimparArea()

	TituloArea("TELEPORTAR PARA PLAYER")

	local Busca = Instance.new("TextBox")
	Busca.Size = UDim2.new(1,-10,0,38)
	Busca.Position = UDim2.fromOffset(5,40)
	Busca.BackgroundColor3 = Color3.fromRGB(40,40,40)
	Busca.BorderSizePixel = 0
	Busca.PlaceholderText = "Pesquisar jogador..."
	Busca.Text = ""
	Busca.TextColor3 = Color3.new(1,1,1)
	Busca.PlaceholderColor3 = Color3.fromRGB(150,150,150)
	Busca.Parent = Area

	Instance.new("UICorner",Busca).CornerRadius = UDim.new(0,7)

	local Lista = Instance.new("ScrollingFrame")
	Lista.Size = UDim2.new(1,-10,1,-90)
	Lista.Position = UDim2.fromOffset(5,85)
	Lista.BackgroundTransparency = 1
	Lista.BorderSizePixel = 0
	Lista.ScrollBarThickness = 5
	Lista.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Lista.Parent = Area

	local Layout = Instance.new("UIListLayout")
	Layout.Padding = UDim.new(0,5)
	Layout.Parent = Lista

	local function Atualizar()

		for _,v in ipairs(Lista:GetChildren()) do

			if v:IsA("Frame") then
				v:Destroy()
			end
		end

		local texto = string.lower(Busca.Text)

		for _,player in ipairs(Players:GetPlayers()) do

			if player ~= LocalPlayer then

				if texto == ""
					or string.find(string.lower(player.Name),texto,1,true)
					or string.find(string.lower(player.DisplayName),texto,1,true)
				then

					local Item = Instance.new("Frame")
					Item.Size = UDim2.new(1,-5,0,45)
					Item.BackgroundColor3 = Color3.fromRGB(40,40,40)
					Item.BorderSizePixel = 0
					Item.Parent = Lista

					Instance.new("UICorner",Item).CornerRadius = UDim.new(0,6)

					local Nome = Instance.new("TextLabel")
					Nome.Size = UDim2.new(1,-100,1,0)
					Nome.Position = UDim2.fromOffset(10,0)
					Nome.BackgroundTransparency = 1
					Nome.Text = player.DisplayName.."  @"..player.Name
					Nome.TextColor3 = Color3.new(1,1,1)
					Nome.TextSize = 12
					Nome.Font = Enum.Font.GothamBold
					Nome.TextXAlignment = Enum.TextXAlignment.Left
					Nome.Parent = Item

					local TP = Instance.new("TextButton")
					TP.Size = UDim2.fromOffset(75,32)
					TP.Position = UDim2.new(1,-82,0.5,-16)
					TP.BackgroundColor3 = Color3.fromRGB(170,40,40)
					TP.Text = "TP"
					TP.TextColor3 = Color3.new(1,1,1)
					TP.Font = Enum.Font.GothamBold
					TP.Parent = Item

					TP.MouseButton1Click:Connect(function()

						local meuChar = LocalPlayer.Character
						local alvoChar = player.Character

						local meuRoot = meuChar
							and meuChar:FindFirstChild("HumanoidRootPart")

						local alvoRoot = alvoChar
							and alvoChar:FindFirstChild("HumanoidRootPart")

						if meuRoot and alvoRoot then

							meuRoot.CFrame =
								alvoRoot.CFrame
								* CFrame.new(0,0,3)

						end
					end)
				end
			end
		end
	end

	Busca:GetPropertyChangedSignal("Text"):Connect(Atualizar)

	Atualizar()
end

--========================================================
-- TOOLS
--========================================================

local TodasTools = {}

local function Caminho(obj)

	local partes = {}
	local atual = obj

	while atual and atual ~= game do

		table.insert(partes,1,atual.Name)
		atual = atual.Parent

	end

	return table.concat(partes," > ")
end

local function ProcurarTools()

	TodasTools = {}

	for _,obj in ipairs(game:GetDescendants()) do

		if obj:IsA("Tool") then

			table.insert(TodasTools,obj)

		end
	end

	table.sort(TodasTools,function(a,b)

		return string.lower(a.Name) < string.lower(b.Name)

	end)
end

local function PossuiTool(nome)

	local Backpack = LocalPlayer:FindFirstChildOfClass("Backpack")

	if Backpack and Backpack:FindFirstChild(nome) then
		return true
	end

	local Character = LocalPlayer.Character

	if Character and Character:FindFirstChild(nome) then
		return true
	end

	return false
end

local function PegarTool(tool)

	if not tool or not tool.Parent then
		return false
	end

	if PossuiTool(tool.Name) then
		return false
	end

	local Backpack = LocalPlayer:FindFirstChildOfClass("Backpack")

	if not Backpack then
		return false
	end

	local sucesso,copia = pcall(function()
		return tool:Clone()
	end)

	if sucesso and copia then

		copia.Parent = Backpack
		return true

	end

	return false
end

local function AbrirTools()

	LimparArea()

	TituloArea("TOOLS DO SERVIDOR")

	local Busca = Instance.new("TextBox")
	Busca.Size = UDim2.new(1,-125,0,38)
	Busca.Position = UDim2.fromOffset(5,40)
	Busca.BackgroundColor3 = Color3.fromRGB(40,40,40)
	Busca.BorderSizePixel = 0
	Busca.PlaceholderText = "🔎 Pesquisar Tool..."
	Busca.Text = ""
	Busca.TextColor3 = Color3.new(1,1,1)
	Busca.PlaceholderColor3 = Color3.fromRGB(150,150,150)
	Busca.Parent = Area

	Instance.new("UICorner",Busca).CornerRadius = UDim.new(0,7)

	local Atualizar = Instance.new("TextButton")
	Atualizar.Size = UDim2.fromOffset(110,38)
	Atualizar.Position = UDim2.new(1,-115,0,40)
	Atualizar.BackgroundColor3 = Color3.fromRGB(45,120,65)
	Atualizar.Text = "↻ ATUALIZAR"
	Atualizar.TextColor3 = Color3.new(1,1,1)
	Atualizar.Font = Enum.Font.GothamBold
	Atualizar.TextSize = 11
	Atualizar.Parent = Area

	Instance.new("UICorner",Atualizar).CornerRadius = UDim.new(0,7)

	local Contador = Instance.new("TextLabel")
	Contador.Size = UDim2.new(1,-10,0,25)
	Contador.Position = UDim2.fromOffset(5,82)
	Contador.BackgroundTransparency = 1
	Contador.TextColor3 = Color3.fromRGB(170,170,170)
	Contador.TextSize = 11
	Contador.Font = Enum.Font.Gotham
	Contador.TextXAlignment = Enum.TextXAlignment.Left
	Contador.Parent = Area

	local Lista = Instance.new("ScrollingFrame")
	Lista.Size = UDim2.new(1,-10,1,-120)
	Lista.Position = UDim2.fromOffset(5,110)
	Lista.BackgroundColor3 = Color3.fromRGB(18,18,18)
	Lista.BorderSizePixel = 0
	Lista.ScrollBarThickness = 6
	Lista.AutomaticCanvasSize = Enum.AutomaticSize.Y
	Lista.Parent = Area

	Instance.new("UICorner",Lista).CornerRadius = UDim.new(0,7)

	local Layout = Instance.new("UIListLayout")
	Layout.Padding = UDim.new(0,6)
	Layout.Parent = Lista

	local function Mostrar()

		for _,v in ipairs(Lista:GetChildren()) do

			if v:IsA("Frame") then
				v:Destroy()
			end

		end

		local pesquisa = string.lower(Busca.Text)
		local quantidade = 0

		for _,tool in ipairs(TodasTools) do

			if tool and tool.Parent then

				local nome = string.lower(tool.Name)

				if pesquisa == ""
					or string.find(nome,pesquisa,1,true)
				then

					quantidade += 1

					local Item = Instance.new("Frame")
					Item.Size = UDim2.new(1,-5,0,70)
					Item.BackgroundColor3 = Color3.fromRGB(32,32,32)
					Item.BorderSizePixel = 0
					Item.Parent = Lista

					Instance.new("UICorner",Item).CornerRadius = UDim.new(0,7)

					local Nome = Instance.new("TextLabel")
					Nome.Size = UDim2.new(1,-110,0,28)
					Nome.Position = UDim2.fromOffset(10,5)
					Nome.BackgroundTransparency = 1
					Nome.Text = "🔧 "..tool.Name
					Nome.TextColor3 = Color3.new(1,1,1)
					Nome.TextSize = 13
					Nome.Font = Enum.Font.GothamBold
					Nome.TextXAlignment = Enum.TextXAlignment.Left
					Nome.Parent = Item

					local Local = Instance.new("TextLabel")
					Local.Size = UDim2.new(1,-110,0,30)
					Local.Position = UDim2.fromOffset(10,32)
					Local.BackgroundTransparency = 1
					Local.Text = "📍 "..Caminho(tool)
					Local.TextColor3 = Color3.fromRGB(145,145,145)
					Local.TextSize = 9
					Local.Font = Enum.Font.Gotham
					Local.TextXAlignment = Enum.TextXAlignment.Left
					Local.TextTruncate = Enum.TextTruncate.AtEnd
					Local.Parent = Item

					local Pegar = Instance.new("TextButton")
					Pegar.Size = UDim2.fromOffset(90,32)
					Pegar.Position = UDim2.new(1,-98,0.5,-16)
					Pegar.BackgroundColor3 = Color3.fromRGB(170,40,40)
					Pegar.Text = "PEGAR"
					Pegar.TextColor3 = Color3.new(1,1,1)
					Pegar.TextSize = 11
					Pegar.Font = Enum.Font.GothamBold
					Pegar.Parent = Item

					Instance.new("UICorner",Pegar).CornerRadius = UDim.new(0,6)

					Pegar.MouseButton1Click:Connect(function()

						if PossuiTool(tool.Name) then

							Pegar.Text = "JÁ TEM"

							task.delay(1,function()

								if Pegar.Parent then
									Pegar.Text = "PEGAR"
								end

							end)

							return
						end

						if PegarTool(tool) then

							Pegar.Text = "PEGOU ✓"
							Pegar.BackgroundColor3 = Color3.fromRGB(40,130,65)

						else

							Pegar.Text = "ERRO"

						end
					end)
				end
			end
		end

		Contador.Text =
			"Tools encontradas: "
			..#TodasTools
			.."  •  Exibindo: "
			..quantidade
	end

	Busca:GetPropertyChangedSignal("Text"):Connect(Mostrar)

	Atualizar.MouseButton1Click:Connect(function()

		Atualizar.Text = "BUSCANDO..."

		ProcurarTools()
		Mostrar()

		Atualizar.Text = "↻ ATUALIZAR"
	end)

	ProcurarTools()
	Mostrar()
end

--========================================================
-- CONFIGURAÇÕES
--========================================================

local function AbrirConfig()

	LimparArea()

	TituloArea("CONFIGURAÇÕES")

	CriarToggle(
		"VIDA INFINITA",
		VIDA_INFINITA,
		function(v)

			VIDA_INFINITA = v

			local char = LocalPlayer.Character
			local hum = char and char:FindFirstChildOfClass("Humanoid")

			if hum then

				if v then
					hum.MaxHealth = math.huge
					hum.Health = math.huge
				else
					hum.MaxHealth = 100
					hum.Health = math.min(hum.Health,100)
				end

			end
		end,
		45
	)

	local Marcar = Instance.new("TextButton")
	Marcar.Size = UDim2.new(1,-10,0,38)
	Marcar.Position = UDim2.fromOffset(5,100)
	Marcar.BackgroundColor3 = Color3.fromRGB(45,45,45)
	Marcar.Text = "MARCAR SPAWN"
	Marcar.TextColor3 = Color3.new(1,1,1)
	Marcar.Font = Enum.Font.GothamBold
	Marcar.Parent = Area

	Instance.new("UICorner",Marcar).CornerRadius = UDim.new(0,7)

	Marcar.MouseButton1Click:Connect(function()

		local char = LocalPlayer.Character
		local root = char and char:FindFirstChild("HumanoidRootPart")

		if root then

			spawnMarcado = root.CFrame

			Marcar.Text = "SPAWN MARCADO ✓"

		end
	end)

	local Voltar = Instance.new("TextButton")
	Voltar.Size = UDim2.new(1,-10,0,38)
	Voltar.Position = UDim2.fromOffset(5,145)
	Voltar.BackgroundColor3 = Color3.fromRGB(45,45,45)
	Voltar.Text = "VOLTAR AO SPAWN"
	Voltar.TextColor3 = Color3.new(1,1,1)
	Voltar.Font = Enum.Font.GothamBold
	Voltar.Parent = Area

	Instance.new("UICorner",Voltar).CornerRadius = UDim.new(0,7)

	Voltar.MouseButton1Click:Connect(function()

		if not spawnMarcado then
			return
		end

		local char = LocalPlayer.Character
		local root = char and char:FindFirstChild("HumanoidRootPart")

		if root then
			root.CFrame = spawnMarcado
		end
	end)

	local Curar = Instance.new("TextButton")
	Curar.Size = UDim2.new(1,-10,0,38)
	Curar.Position = UDim2.fromOffset(5,190)
	Curar.BackgroundColor3 = Color3.fromRGB(45,120,65)
	Curar.Text = "REGENERAR VIDA"
	Curar.TextColor3 = Color3.new(1,1,1)
	Curar.Font = Enum.Font.GothamBold
	Curar.Parent = Area

	Instance.new("UICorner",Curar).CornerRadius = UDim.new(0,7)

	Curar.MouseButton1Click:Connect(function()

		local char = LocalPlayer.Character
		local hum = char and char:FindFirstChildOfClass("Humanoid")

		if hum then
			hum.Health = hum.MaxHealth
		end
	end)

	local Fome = Instance.new("TextButton")
	Fome.Size = UDim2.new(1,-10,0,38)
	Fome.Position = UDim2.fromOffset(5,235)
	Fome.BackgroundColor3 = Color3.fromRGB(45,45,45)
	Fome.Text = "ENCHER FOME"
	Fome.TextColor3 = Color3.new(1,1,1)
	Fome.Font = Enum.Font.GothamBold
	Fome.Parent = Area

	Instance.new("UICorner",Fome).CornerRadius = UDim.new(0,7)

	Fome.MouseButton1Click:Connect(function()

		local char = LocalPlayer.Character

		if char then

			local valor = char:FindFirstChild("Fome",true)

			if valor and valor:IsA("NumberValue") then
				valor.Value = 100
			end

		end
	end)

	local Sede = Instance.new("TextButton")
	Sede.Size = UDim2.new(1,-10,0,38)
	Sede.Position = UDim2.fromOffset(5,280)
	Sede.BackgroundColor3 = Color3.fromRGB(45,45,45)
	Sede.Text = "ENCHER SEDE"
	Sede.TextColor3 = Color3.new(1,1,1)
	Sede.Font = Enum.Font.GothamBold
	Sede.Parent = Area

	Instance.new("UICorner",Sede).CornerRadius = UDim.new(0,7)

	Sede.MouseButton1Click:Connect(function()

		local char = LocalPlayer.Character

		if char then

			local valor = char:FindFirstChild("Sede",true)

			if valor and valor:IsA("NumberValue") then
				valor.Value = 100
			end

		end
	end)
end

--========================================================
-- MIRA ASSISTIDA
--========================================================

local function EncontrarAlvo()

	local melhor = nil
	local menor = TAMANHO_FOV

	local centro = Camera.ViewportSize / 2

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then

			local char = player.Character
			local humanoid = char and char:FindFirstChildOfClass("Humanoid")
			local parte = char and char:FindFirstChild(PARTE_MIRA)

			if humanoid
				and humanoid.Health > 0
				and parte
			then

				local pos,visivel =
					Camera:WorldToViewportPoint(parte.Position)

				if visivel then

					local distancia =
						(Vector2.new(pos.X,pos.Y)-centro).Magnitude

					if distancia < menor then

						menor = distancia
						melhor = parte

					end
				end
			end
		end
	end

	return melhor
end

--========================================================
-- LOOP
--========================================================

RunService.RenderStepped:Connect(function()

	AtualizarESP()

	if FOV_ATIVADO then

		FOVCircle.Visible = true

		FOVCircle.Size = UDim2.fromOffset(
			TAMANHO_FOV,
			TAMANHO_FOV
		)

	else

		FOVCircle.Visible = false
	end

	if MIRA_ATIVADA and FOV_ATIVADO then

		local alvo = EncontrarAlvo()

		if alvo then

			local cameraPos = Camera.CFrame.Position

			local destino = CFrame.lookAt(
				cameraPos,
				alvo.Position
			)

			Camera.CFrame =
				Camera.CFrame:Lerp(
					destino,
					SUAVIDADE_MIRA
				)
		end
	end

	if VIDA_INFINITA then

		local char = LocalPlayer.Character
		local hum = char and char:FindFirstChildOfClass("Humanoid")

		if hum then

			hum.MaxHealth = math.huge
			hum.Health = math.huge

		end
	end
end)

--========================================================
-- ARRASTAR PAINEL
--========================================================

local arrastando = false
local inicioMouse
local inicioPos

Topo.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1 then

		arrastando = true
		inicioMouse = input.Position
		inicioPos = Painel.Position

	end
end)

UserInputService.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		arrastando = false
	end
end)

UserInputService.InputChanged:Connect(function(input)

	if not arrastando then
		return
	end

	if input.UserInputType ~= Enum.UserInputType.MouseMovement then
		return
	end

	local delta = input.Position - inicioMouse

	Painel.Position = UDim2.new(
		inicioPos.X.Scale,
		inicioPos.X.Offset + delta.X,
		inicioPos.Y.Scale,
		inicioPos.Y.Offset + delta.Y
	)
end)

--========================================================
-- TROCA DE PÁGINAS
--========================================================

HomeButton.MouseButton1Click:Connect(AbrirHome)
TPButton.MouseButton1Click:Connect(AbrirTP)
ToolsButton.MouseButton1Click:Connect(AbrirTools)
ConfigButton.MouseButton1Click:Connect(AbrirConfig)

--========================================================
-- PERSONAGEM
--========================================================

LocalPlayer.CharacterAdded:Connect(function(character)

	task.wait(1)

	if VIDA_INFINITA then

		local hum = character:FindFirstChildOfClass("Humanoid")

		if hum then

			hum.MaxHealth = math.huge
			hum.Health = math.huge

		end
	end

	if spawnMarcado then

		local root = character:WaitForChild(
			"HumanoidRootPart",
			5
		)

		if root then

			task.wait(0.3)

			root.CFrame = spawnMarcado

		end
	end
end)

--========================================================
-- ABRIR HOME
--========================================================

AbrirHome()
