--========================================================
-- JF PAINEL
-- HOME + TP PLAYER
-- ESP + FOV + MIRA + SPAWN + VIDA
--========================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

--========================================================
-- CONFIGURAÇÕES
--========================================================

local ESP_ATIVADO = false
local FOV_ATIVADO = false
local MIRA_ATIVADA = false
local VIDA_INFINITA = false

local PARTE_MIRA = "Cabeça"
local TAMANHO_FOV = 150
local SUAVIDADE_MIRA = 0.18

local spawnMarcado = nil
local marcadorSpawn = nil
local conexaoVida = nil
local personagemProtegido = nil

--========================================================
-- REMOVER PAINEL ANTIGO
--========================================================

local antigo = PlayerGui:FindFirstChild("JF_Painel")

if antigo then
	antigo:Destroy()
end

--========================================================
-- SCREEN GUI
--========================================================

local Interface = Instance.new("ScreenGui")
Interface.Name = "JF_Painel"
Interface.ResetOnSpawn = false
Interface.IgnoreGuiInset = true
Interface.Parent = PlayerGui

--========================================================
-- TAMANHO
--========================================================

local viewport = Camera.ViewportSize

local larguraPainel = math.clamp(viewport.X * 0.82, 430, 600)
local alturaPainel = math.clamp(viewport.Y * 0.62, 300, 390)

--========================================================
-- PAINEL PRINCIPAL
--========================================================

local Painel = Instance.new("Frame")
Painel.Name = "PainelPrincipal"
Painel.Size = UDim2.fromOffset(larguraPainel, alturaPainel)
Painel.AnchorPoint = Vector2.new(0.5, 0.5)
Painel.Position = UDim2.fromScale(0.5, 0.5)
Painel.BackgroundColor3 = Color3.fromRGB(25,25,25)
Painel.BorderSizePixel = 0
Painel.ClipsDescendants = true
Painel.Parent = Interface

local pc = Instance.new("UICorner")
pc.CornerRadius = UDim.new(0,8)
pc.Parent = Painel

local ps = Instance.new("UIStroke")
ps.Color = Color3.fromRGB(55,55,55)
ps.Parent = Painel

--========================================================
-- TOPO
--========================================================

local Topo = Instance.new("Frame")
Topo.Size = UDim2.new(1,0,0,45)
Topo.BackgroundColor3 = Color3.fromRGB(30,30,30)
Topo.BorderSizePixel = 0
Topo.ZIndex = 10
Topo.Parent = Painel

local tc = Instance.new("UICorner")
tc.CornerRadius = UDim.new(0,8)
tc.Parent = Topo

local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1,-100,1,0)
Titulo.Position = UDim2.fromOffset(15,0)
Titulo.BackgroundTransparency = 1
Titulo.Text = "JF PAINEL"
Titulo.TextColor3 = Color3.fromRGB(255,255,255)
Titulo.Font = Enum.Font.GothamBold
Titulo.TextSize = 19
Titulo.TextXAlignment = Enum.TextXAlignment.Left
Titulo.Parent = Topo

local Minimizar = Instance.new("TextButton")
Minimizar.Size = UDim2.fromOffset(35,35)
Minimizar.Position = UDim2.new(1,-82,0,5)
Minimizar.BackgroundTransparency = 1
Minimizar.Text = "—"
Minimizar.TextColor3 = Color3.fromRGB(220,220,220)
Minimizar.Font = Enum.Font.GothamBold
Minimizar.TextSize = 20
Minimizar.ZIndex = 11
Minimizar.Parent = Topo

local Fechar = Instance.new("TextButton")
Fechar.Size = UDim2.fromOffset(35,35)
Fechar.Position = UDim2.new(1,-42,0,5)
Fechar.BackgroundTransparency = 1
Fechar.Text = "×"
Fechar.TextColor3 = Color3.fromRGB(220,220,220)
Fechar.Font = Enum.Font.GothamBold
Fechar.TextSize = 25
Fechar.ZIndex = 11
Fechar.Parent = Topo

--========================================================
-- ARRASTAR
--========================================================

local arrastando = false
local inicioMouse
local inicioPainel

Topo.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		arrastando = true
		inicioMouse = input.Position
		inicioPainel = Painel.Position
	end
end)

UserInputService.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		arrastando = false
	end
end)

UserInputService.InputChanged:Connect(function(input)

	if not arrastando then
		return
	end

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		local movimento = input.Position - inicioMouse

		local x = inicioPainel.X.Offset + movimento.X
		local y = inicioPainel.Y.Offset + movimento.Y

		local tela = Camera.ViewportSize

		local metadeX = Painel.AbsoluteSize.X/2
		local metadeY = Painel.AbsoluteSize.Y/2

		x = math.clamp(
			x,
			-metadeX + 20,
			tela.X - metadeX - 20
		)

		y = math.clamp(
			y,
			-metadeY + 20,
			tela.Y - metadeY - 20
		)

		Painel.Position = UDim2.fromOffset(x,y)
		Painel.AnchorPoint = Vector2.new(0,0)
	end
end)

--========================================================
-- MENU LATERAL
--========================================================

local Menu = Instance.new("Frame")
Menu.Size = UDim2.new(0,140,1,-61)
Menu.Position = UDim2.fromOffset(8,53)
Menu.BackgroundColor3 = Color3.fromRGB(30,30,30)
Menu.BorderSizePixel = 0
Menu.ClipsDescendants = true
Menu.Parent = Painel

local mc = Instance.new("UICorner")
mc.CornerRadius = UDim.new(0,6)
mc.Parent = Menu

local function CriarMenu(texto,y)

	local b = Instance.new("TextButton")

	b.Size = UDim2.new(1,-14,0,38)
	b.Position = UDim2.fromOffset(7,y)
	b.BackgroundColor3 = Color3.fromRGB(42,42,42)
	b.BorderSizePixel = 0
	b.Text = texto
	b.TextColor3 = Color3.fromRGB(210,210,210)
	b.Font = Enum.Font.GothamBold
	b.TextSize = 13
	b.Parent = Menu

	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0,5)
	c.Parent = b

	return b
end

local BotaoHome = CriarMenu("Home",8)
local BotaoTP = CriarMenu("TP Player",52)
local BotaoTools = CriarMenu("Tools",96)
local BotaoConfig = CriarMenu("Configurações",140)

local Status = Instance.new("TextLabel")
Status.Size = UDim2.new(1,-20,0,25)
Status.Position = UDim2.new(0,10,1,-32)
Status.BackgroundTransparency = 1
Status.Text = "JF • Sistema de teste"
Status.TextColor3 = Color3.fromRGB(120,120,120)
Status.Font = Enum.Font.Gotham
Status.TextSize = 11
Status.TextXAlignment = Enum.TextXAlignment.Left
Status.Parent = Menu

--========================================================
-- ÁREA DIREITA HOME
--========================================================

local Home = Instance.new("ScrollingFrame")
Home.Name = "Home"
Home.Size = UDim2.new(1,-157,1,-61)
Home.Position = UDim2.fromOffset(149,53)
Home.BackgroundColor3 = Color3.fromRGB(30,30,30)
Home.BorderSizePixel = 0
Home.ScrollBarThickness = 5
Home.ScrollBarImageColor3 = Color3.fromRGB(150,0,0)
Home.CanvasSize = UDim2.fromOffset(0,730)
Home.Parent = Painel

local hc = Instance.new("UICorner")
hc.CornerRadius = UDim.new(0,6)
hc.Parent = Home

--========================================================
-- TELA TP
--========================================================

local TelaTP = Instance.new("Frame")
TelaTP.Name = "TPPlayer"
TelaTP.Size = UDim2.new(1,-157,1,-61)
TelaTP.Position = UDim2.fromOffset(149,53)
TelaTP.BackgroundColor3 = Color3.fromRGB(30,30,30)
TelaTP.BorderSizePixel = 0
TelaTP.Visible = false
TelaTP.Parent = Painel

local tpc = Instance.new("UICorner")
tpc.CornerRadius = UDim.new(0,6)
tpc.Parent = TelaTP

--========================================================
-- TÍTULO TP
--========================================================

local TituloTP = Instance.new("TextLabel")
TituloTP.Size = UDim2.new(1,-24,0,32)
TituloTP.Position = UDim2.fromOffset(12,7)
TituloTP.BackgroundTransparency = 1
TituloTP.Text = "TP PLAYER"
TituloTP.TextColor3 = Color3.fromRGB(255,255,255)
TituloTP.Font = Enum.Font.GothamBold
TituloTP.TextSize = 18
TituloTP.TextXAlignment = Enum.TextXAlignment.Left
TituloTP.Parent = TelaTP

--========================================================
-- PESQUISA
--========================================================

local Pesquisa = Instance.new("TextBox")
Pesquisa.Size = UDim2.new(1,-24,0,38)
Pesquisa.Position = UDim2.fromOffset(12,45)
Pesquisa.BackgroundColor3 = Color3.fromRGB(42,42,42)
Pesquisa.BorderSizePixel = 0
Pesquisa.Text = ""
Pesquisa.PlaceholderText = "Pesquisar jogador..."
Pesquisa.PlaceholderColor3 = Color3.fromRGB(130,130,130)
Pesquisa.TextColor3 = Color3.fromRGB(235,235,235)
Pesquisa.Font = Enum.Font.Gotham
Pesquisa.TextSize = 13
Pesquisa.ClearTextOnFocus = false
Pesquisa.Parent = TelaTP

local pq = Instance.new("UICorner")
pq.CornerRadius = UDim.new(0,6)
pq.Parent = Pesquisa

--========================================================
-- LISTA
--========================================================

local Lista = Instance.new("ScrollingFrame")
Lista.Size = UDim2.new(1,-24,1,-94)
Lista.Position = UDim2.fromOffset(12,90)
Lista.BackgroundTransparency = 1
Lista.BorderSizePixel = 0
Lista.ScrollBarThickness = 5
Lista.ScrollBarImageColor3 = Color3.fromRGB(150,0,0)
Lista.CanvasSize = UDim2.fromOffset(0,0)
Lista.Parent = TelaTP

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0,6)
Layout.SortOrder = Enum.SortOrder.Name
Layout.Parent = Lista

--========================================================
-- TELEPORTAR
--========================================================

local function TeleportarParaPlayer(alvo)

	if not alvo then
		return
	end

	if alvo == LocalPlayer then
		return
	end

	local meuChar = LocalPlayer.Character
	local alvoChar = alvo.Character

	if not meuChar or not alvoChar then
		return
	end

	local minhaRaiz = meuChar:FindFirstChild("HumanoidRootPart")
	local raizAlvo = alvoChar:FindFirstChild("HumanoidRootPart")

	if minhaRaiz and raizAlvo then

		minhaRaiz.CFrame =
			raizAlvo.CFrame *
			CFrame.new(0,0,3)
	end
end

--========================================================
-- ATUALIZAR JOGADORES
--========================================================

local function AtualizarPlayers()

	for _,obj in ipairs(Lista:GetChildren()) do

		if obj:IsA("Frame") then
			obj:Destroy()
		end
	end

	local busca = string.lower(Pesquisa.Text)
	local quantidade = 0

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then

			local nome = string.lower(player.Name)
			local display = string.lower(player.DisplayName)

			if busca == ""
				or string.find(nome,busca,1,true)
				or string.find(display,busca,1,true) then

				quantidade += 1

				local Linha = Instance.new("Frame")
				Linha.Size = UDim2.new(1,-5,0,48)
				Linha.BackgroundColor3 = Color3.fromRGB(42,42,42)
				Linha.BorderSizePixel = 0
				Linha.Parent = Lista

				local lc = Instance.new("UICorner")
				lc.CornerRadius = UDim.new(0,6)
				lc.Parent = Linha

				local Nome = Instance.new("TextLabel")
				Nome.Size = UDim2.new(1,-75,1,0)
				Nome.Position = UDim2.fromOffset(10,0)
				Nome.BackgroundTransparency = 1
				Nome.Text = "👤  "..player.DisplayName.."  @"..player.Name
				Nome.TextColor3 = Color3.fromRGB(235,235,235)
				Nome.Font = Enum.Font.GothamBold
				Nome.TextSize = 11
				Nome.TextXAlignment = Enum.TextXAlignment.Left
				Nome.TextTruncate = Enum.TextTruncate.AtEnd
				Nome.Parent = Linha

				local TP = Instance.new("TextButton")
				TP.Size = UDim2.fromOffset(52,32)
				TP.Position = UDim2.new(1,-60,0.5,-16)
				TP.BackgroundColor3 = Color3.fromRGB(120,0,0)
				TP.BorderSizePixel = 0
				TP.Text = "TP"
				TP.TextColor3 = Color3.fromRGB(255,255,255)
				TP.Font = Enum.Font.GothamBold
				TP.TextSize = 12
				TP.Parent = Linha

				local tpc2 = Instance.new("UICorner")
				tpc2.CornerRadius = UDim.new(0,5)
				tpc2.Parent = TP

				TP.MouseButton1Click:Connect(function()
					TeleportarParaPlayer(player)
				end)
			end
		end
	end

	Lista.CanvasSize =
		UDim2.fromOffset(0,math.max(0,quantidade * 54))
end

Pesquisa:GetPropertyChangedSignal("Text"):Connect(function()
	AtualizarPlayers()
end)

Players.PlayerAdded:Connect(function()
	task.wait()
	AtualizarPlayers()
end)

Players.PlayerRemoving:Connect(function()
	task.wait()
	AtualizarPlayers()
end)

--========================================================
-- HOME - TÍTULO
--========================================================

local TituloHome = Instance.new("TextLabel")
TituloHome.Size = UDim2.new(1,-24,0,35)
TituloHome.Position = UDim2.fromOffset(12,8)
TituloHome.BackgroundTransparency = 1
TituloHome.Text = "Home"
TituloHome.TextColor3 = Color3.fromRGB(255,255,255)
TituloHome.Font = Enum.Font.GothamBold
TituloHome.TextSize = 18
TituloHome.TextXAlignment = Enum.TextXAlignment.Left
TituloHome.Parent = Home

--========================================================
-- FUNÇÃO OPÇÃO
--========================================================

local function CriarOpcao(texto,y)

	local botao = Instance.new("TextButton")

	botao.Size = UDim2.new(1,-24,0,48)
	botao.Position = UDim2.fromOffset(12,y)
	botao.BackgroundColor3 = Color3.fromRGB(42,42,42)
	botao.BorderSizePixel = 0
	botao.Text = ""
	botao.Parent = Home

	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0,6)
	c.Parent = botao

	local txt = Instance.new("TextLabel")
	txt.Size = UDim2.new(1,-65,1,0)
	txt.Position = UDim2.fromOffset(12,0)
	txt.BackgroundTransparency = 1
	txt.Text = texto
	txt.TextColor3 = Color3.fromRGB(235,235,235)
	txt.Font = Enum.Font.GothamBold
	txt.TextSize = 13
	txt.TextXAlignment = Enum.TextXAlignment.Left
	txt.Parent = botao

	local toggle = Instance.new("Frame")
	toggle.Size = UDim2.fromOffset(30,17)
	toggle.Position = UDim2.new(1,-42,0.5,-8)
	toggle.BackgroundColor3 = Color3.fromRGB(15,15,15)
	toggle.BorderSizePixel = 0
	toggle.Parent = botao

	local tc = Instance.new("UICorner")
	tc.CornerRadius = UDim.new(1,0)
	tc.Parent = toggle

	local bola = Instance.new("Frame")
	bola.Size = UDim2.fromOffset(11,11)
	bola.Position = UDim2.fromOffset(3,3)
	bola.BackgroundColor3 = Color3.fromRGB(80,80,80)
	bola.BorderSizePixel = 0
	bola.Parent = toggle

	local bc = Instance.new("UICorner")
	bc.CornerRadius = UDim.new(1,0)
	bc.Parent = bola

	return botao,toggle,bola
end

local function Alternar(toggle,bola,ligado)

	if ligado then

		toggle.BackgroundColor3 =
			Color3.fromRGB(100,0,0)

		bola.BackgroundColor3 =
			Color3.fromRGB(255,0,0)

		bola.Position =
			UDim2.new(1,-14,0,3)

	else

		toggle.BackgroundColor3 =
			Color3.fromRGB(15,15,15)

		bola.BackgroundColor3 =
			Color3.fromRGB(80,80,80)

		bola.Position =
			UDim2.fromOffset(3,3)
	end
end

--========================================================
-- ESP
--========================================================

local BotaoESP,ToggleESP,BolaESP =
	CriarOpcao("ESP • Caixa",50)

BotaoESP.MouseButton1Click:Connect(function()

	ESP_ATIVADO = not ESP_ATIVADO

	Alternar(
		ToggleESP,
		BolaESP,
		ESP_ATIVADO
	)
end)

--========================================================
-- FOV
--========================================================

local BotaoFOV,ToggleFOV,BolaFOV =
	CriarOpcao("FOV",106)

BotaoFOV.MouseButton1Click:Connect(function()

	FOV_ATIVADO = not FOV_ATIVADO

	Alternar(
		ToggleFOV,
		BolaFOV,
		FOV_ATIVADO
	)
end)

--========================================================
-- TELA TOOLS - LOCAL SCRIPT ÚNICO
--========================================================

local TelaTools = Instance.new("Frame")
TelaTools.Name = "TelaTools"
TelaTools.Size = UDim2.new(1,-157,1,-61)
TelaTools.Position = UDim2.fromOffset(149,53)
TelaTools.BackgroundColor3 = Color3.fromRGB(30,30,30)
TelaTools.BorderSizePixel = 0
TelaTools.Visible = false
TelaTools.Parent = Painel

local tc = Instance.new("UICorner")
tc.CornerRadius = UDim.new(0,6)
tc.Parent = TelaTools

local TituloTools = Instance.new("TextLabel")
TituloTools.Size = UDim2.new(1,-150,0,32)
TituloTools.Position = UDim2.fromOffset(12,10)
TituloTools.BackgroundTransparency = 1
TituloTools.Text = "TOOLS DO SERVIDOR"
TituloTools.TextColor3 = Color3.fromRGB(235,235,235)
TituloTools.Font = Enum.Font.GothamBold
TituloTools.TextSize = 15
TituloTools.TextXAlignment = Enum.TextXAlignment.Left
TituloTools.Parent = TelaTools

local InfoTools = Instance.new("TextLabel")
InfoTools.Size = UDim2.new(1,-24,0,25)
InfoTools.Position = UDim2.fromOffset(12,42)
InfoTools.BackgroundTransparency = 1
InfoTools.Text = "Tools visíveis no mapa e armazenadas nos serviços replicados"
InfoTools.TextColor3 = Color3.fromRGB(145,145,145)
InfoTools.Font = Enum.Font.Gotham
InfoTools.TextSize = 10
InfoTools.TextXAlignment = Enum.TextXAlignment.Left
InfoTools.Parent = TelaTools

local ListaTools = Instance.new("ScrollingFrame")
ListaTools.Name = "ListaTools"
ListaTools.Size = UDim2.new(1,-24,1,-86)
ListaTools.Position = UDim2.fromOffset(12,78)
ListaTools.BackgroundColor3 = Color3.fromRGB(24,24,24)
ListaTools.BorderSizePixel = 0
ListaTools.ScrollBarThickness = 5
ListaTools.ScrollBarImageColor3 = Color3.fromRGB(150,0,0)
ListaTools.CanvasSize = UDim2.fromOffset(0,0)
ListaTools.AutomaticCanvasSize = Enum.AutomaticSize.Y
ListaTools.Parent = TelaTools

local ltc = Instance.new("UICorner")
ltc.CornerRadius = UDim.new(0,6)
ltc.Parent = ListaTools

local ListaLayout = Instance.new("UIListLayout")
ListaLayout.Padding = UDim.new(0,6)
ListaLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
ListaLayout.SortOrder = Enum.SortOrder.Name
ListaLayout.Parent = ListaTools

local ListaPadding = Instance.new("UIPadding")
ListaPadding.PaddingTop = UDim.new(0,8)
ListaPadding.PaddingBottom = UDim.new(0,8)
ListaPadding.PaddingLeft = UDim.new(0,8)
ListaPadding.PaddingRight = UDim.new(0,8)
ListaPadding.Parent = ListaTools

local AtualizarTools = Instance.new("TextButton")
AtualizarTools.Size = UDim2.fromOffset(120,30)
AtualizarTools.Position = UDim2.new(1,-132,0,10)
AtualizarTools.BackgroundColor3 = Color3.fromRGB(42,42,42)
AtualizarTools.BorderSizePixel = 0
AtualizarTools.Text = "Atualizar"
AtualizarTools.TextColor3 = Color3.fromRGB(235,235,235)
AtualizarTools.Font = Enum.Font.GothamBold
AtualizarTools.TextSize = 11
AtualizarTools.Parent = TelaTools

local atc = Instance.new("UICorner")
atc.CornerRadius = UDim.new(0,5)
atc.Parent = AtualizarTools

-- Guarda o caminho físico de cada Tool encontrada.
local ToolsEncontradas = {}

local function LerLocalizacao(tool)
	local parent = tool.Parent
	if parent == workspace or tool:IsDescendantOf(workspace) then
		return "Workspace"
	end

	if tool:IsDescendantOf(ReplicatedStorage) then
		return "ReplicatedStorage"
	end

	local starterPack = game:GetService("StarterPack")
	if tool:IsDescendantOf(starterPack) then
		return "StarterPack"
	end

	return parent and parent:GetFullName() or "Desconhecido"
end

local function ColetarTools()
	local resultado = {}
	local usados = {}

	-- Workspace = Tools espalhadas pelo mapa.
	for _,obj in ipairs(workspace:GetDescendants()) do
		if obj:IsA("Tool") and obj.Archivable then
			local chave = obj:GetFullName()
			if not usados[chave] then
				usados[chave] = true
				table.insert(resultado, {
					Name = obj.Name,
					Location = "Workspace",
					Instance = obj,
				})
			end
		end
	end

	-- ReplicatedStorage = Tools replicadas para o cliente.
	for _,obj in ipairs(ReplicatedStorage:GetDescendants()) do
		if obj:IsA("Tool") and obj.Archivable then
			local chave = obj:GetFullName()
			if not usados[chave] then
				usados[chave] = true
				table.insert(resultado, {
					Name = obj.Name,
					Location = "ReplicatedStorage",
					Instance = obj,
				})
			end
		end
	end

	-- StarterPack = Tools que o jogo entrega normalmente ao spawnar.
	local starterPack = game:GetService("StarterPack")
	for _,obj in ipairs(starterPack:GetDescendants()) do
		if obj:IsA("Tool") and obj.Archivable then
			local chave = obj:GetFullName()
			if not usados[chave] then
				usados[chave] = true
				table.insert(resultado, {
					Name = obj.Name,
					Location = "StarterPack",
					Instance = obj,
				})
			end
		end
	end

	table.sort(resultado,function(a,b)
		local an = string.lower(a.Name)
		local bn = string.lower(b.Name)
		if an == bn then
			return a.Location < b.Location
		end
		return an < bn
	end)

	return resultado
end

local function LimparListaTools()
	for _,obj in ipairs(ListaTools:GetChildren()) do
		if obj:IsA("GuiObject") and obj ~= ListaLayout and obj ~= ListaPadding then
			obj:Destroy()
		end
	end
end

local function ColocarNoInventario(info,botao)
	local tool = info.Instance
	if not tool or not tool.Parent then
		botao.Text = "NÃO ENCONTRADA"
		return false
	end

	local backpack = LocalPlayer:FindFirstChildOfClass("Backpack")
	if not backpack then
		botao.Text = "BACKPACK NÃO ACHADA"
		return false
	end

	local jaTem = false
	for _,obj in ipairs(backpack:GetChildren()) do
		if obj:IsA("Tool") and obj.Name == tool.Name then
			jaTem = true
			break
		end
	end

	if LocalPlayer.Character then
		for _,obj in ipairs(LocalPlayer.Character:GetChildren()) do
			if obj:IsA("Tool") and obj.Name == tool.Name then
				jaTem = true
				break
			end
		end
	end

	if jaTem then
		botao.Text = "JÁ POSSUI"
		task.delay(1.2,function()
			if botao.Parent then
				botao.Text = "PEGAR"
			end
		end)
		return false
	end

	local clone
	local ok = pcall(function()
		clone = tool:Clone()
	end)

	if not ok or not clone then
		botao.Text = "NÃO PODE COPIAR"
		return false
	end

	clone.Parent = backpack
	botao.Text = "ADQUIRIDA"
	botao.BackgroundColor3 = Color3.fromRGB(30,120,55)

	task.delay(1.5,function()
		if botao.Parent then
			botao.Text = "PEGAR"
			botao.BackgroundColor3 = Color3.fromRGB(150,0,0)
		end
	end)

	return true
end

local function MostrarTools(lista)
	LimparListaTools()
	ToolsEncontradas = lista or {}

	if not lista or #lista == 0 then
		local vazio = Instance.new("TextLabel")
		vazio.Size = UDim2.new(1,-8,0,60)
		vazio.BackgroundTransparency = 1
		vazio.Text = "Nenhuma Tool encontrada nos locais visíveis."
		vazio.TextColor3 = Color3.fromRGB(150,150,150)
		vazio.Font = Enum.Font.Gotham
		vazio.TextSize = 12
		vazio.Parent = ListaTools
		return
	end

	for indice,info in ipairs(lista) do
		local item = Instance.new("Frame")
		item.Name = "Tool_" .. indice
		item.Size = UDim2.new(1,-8,0,56)
		item.BackgroundColor3 = Color3.fromRGB(42,42,42)
		item.BorderSizePixel = 0
		item.Parent = ListaTools

		local ic = Instance.new("UICorner")
		ic.CornerRadius = UDim.new(0,5)
		ic.Parent = item

		local nome = Instance.new("TextLabel")
		nome.Size = UDim2.new(1,-130,0,25)
		nome.Position = UDim2.fromOffset(10,5)
		nome.BackgroundTransparency = 1
		nome.Text = info.Name
		nome.TextColor3 = Color3.fromRGB(235,235,235)
		nome.Font = Enum.Font.GothamBold
		nome.TextSize = 12
		nome.TextXAlignment = Enum.TextXAlignment.Left
		nome.TextTruncate = Enum.TextTruncate.AtEnd
		nome.Parent = item

		local localizacao = Instance.new("TextLabel")
		localizacao.Size = UDim2.new(1,-130,0,20)
		localizacao.Position = UDim2.fromOffset(10,30)
		localizacao.BackgroundTransparency = 1
		localizacao.Text = info.Location
		localizacao.TextColor3 = Color3.fromRGB(145,145,145)
		localizacao.Font = Enum.Font.Gotham
		localizacao.TextSize = 10
		localizacao.TextXAlignment = Enum.TextXAlignment.Left
		localizacao.TextTruncate = Enum.TextTruncate.AtEnd
		localizacao.Parent = item

		local pegar = Instance.new("TextButton")
		pegar.Size = UDim2.fromOffset(105,34)
		pegar.Position = UDim2.new(1,-115,0.5,-17)
		pegar.BackgroundColor3 = Color3.fromRGB(150,0,0)
		pegar.BorderSizePixel = 0
		pegar.Text = "PEGAR"
		pegar.TextColor3 = Color3.fromRGB(255,255,255)
		pegar.Font = Enum.Font.GothamBold
		pegar.TextSize = 11
		pegar.Parent = item

		local pc = Instance.new("UICorner")
		pc.CornerRadius = UDim.new(0,5)
		pc.Parent = pegar

		pegar.MouseButton1Click:Connect(function()
			pegar.Text = "PEGANDO..."
			ColocarNoInventario(info,pegar)
		end)
	end
end

local function CarregarTools()
	AtualizarTools.Text = "Procurando..."

	local ok,lista = pcall(ColetarTools)
	if ok then
		MostrarTools(lista)
	else
		MostrarTools(nil)
	end

	AtualizarTools.Text = "Atualizar"
end

AtualizarTools.MouseButton1Click:Connect(CarregarTools)

--========================================================
-- MIRA
--========================================================

--========================================================
-- MIRA
--========================================================

local BotaoMira,ToggleMira,BolaMira =
	CriarOpcao("Mira Assistida",162)

BotaoMira.MouseButton1Click:Connect(function()

	MIRA_ATIVADA = not MIRA_ATIVADA

	Alternar(
		ToggleMira,
		BolaMira,
		MIRA_ATIVADA
	)
end)

--========================================================
-- LOCAL DA MIRA
--========================================================

local TextoParte = Instance.new("TextLabel")
TextoParte.Size = UDim2.new(1,-24,0,25)
TextoParte.Position = UDim2.fromOffset(12,218)
TextoParte.BackgroundTransparency = 1
TextoParte.Text = "LOCAL DA MIRA"
TextoParte.TextColor3 = Color3.fromRGB(160,160,160)
TextoParte.Font = Enum.Font.GothamBold
TextoParte.TextSize = 11
TextoParte.TextXAlignment = Enum.TextXAlignment.Left
TextoParte.Parent = Home

local Partes = {
	"Cabeça",
	"Torso",
	"Perna"
}

local BotoesPartes = {}

for i,parte in ipairs(Partes) do

	local b = Instance.new("TextButton")

	b.Size = UDim2.fromOffset(82,34)
	b.Position =
		UDim2.fromOffset(
			12 + ((i-1)*88),
			245
		)

	b.BackgroundColor3 = Color3.fromRGB(42,42,42)
	b.BorderSizePixel = 0
	b.Text = parte
	b.TextColor3 = Color3.fromRGB(220,220,220)
	b.Font = Enum.Font.GothamBold
	b.TextSize = 11
	b.Parent = Home

	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0,5)
	c.Parent = b

	BotoesPartes[parte] = b

	b.MouseButton1Click:Connect(function()

		PARTE_MIRA = parte

		for _,outro in pairs(BotoesPartes) do
			outro.BackgroundColor3 =
				Color3.fromRGB(42,42,42)
		end

		b.BackgroundColor3 =
			Color3.fromRGB(150,0,0)
	end)
end

--========================================================
-- FOV SLIDER
--========================================================

local TextoFOV = Instance.new("TextLabel")
TextoFOV.Size = UDim2.new(1,-24,0,25)
TextoFOV.Position = UDim2.fromOffset(12,288)
TextoFOV.BackgroundTransparency = 1
TextoFOV.Text = "TAMANHO DO FOV: 150"
TextoFOV.TextColor3 = Color3.fromRGB(160,160,160)
TextoFOV.Font = Enum.Font.GothamBold
TextoFOV.TextSize = 11
TextoFOV.TextXAlignment = Enum.TextXAlignment.Left
TextoFOV.Parent = Home

local FundoSlider = Instance.new("Frame")
FundoSlider.Size = UDim2.new(1,-24,0,7)
FundoSlider.Position = UDim2.fromOffset(12,322)
FundoSlider.BackgroundColor3 = Color3.fromRGB(15,15,15)
FundoSlider.BorderSizePixel = 0
FundoSlider.Parent = Home

local fs = Instance.new("UICorner")
fs.CornerRadius = UDim.new(1,0)
fs.Parent = FundoSlider

local Preenchimento = Instance.new("Frame")
Preenchimento.Size = UDim2.new(0.285,0,1,0)
Preenchimento.BackgroundColor3 = Color3.fromRGB(255,0,0)
Preenchimento.BorderSizePixel = 0
Preenchimento.Parent = FundoSlider

local fpc = Instance.new("UICorner")
fpc.CornerRadius = UDim.new(1,0)
fpc.Parent = Preenchimento

local Pino = Instance.new("Frame")
Pino.Size = UDim2.fromOffset(16,16)
Pino.AnchorPoint = Vector2.new(0.5,0.5)
Pino.Position = UDim2.new(0.285,0,0.5,0)
Pino.BackgroundColor3 = Color3.fromRGB(255,255,255)
Pino.BorderSizePixel = 0
Pino.Parent = FundoSlider

local pinoC = Instance.new("UICorner")
pinoC.CornerRadius = UDim.new(1,0)
pinoC.Parent = Pino

local arrastandoFOV = false

local function AtualizarFOV(valor)

	valor = math.clamp(valor,0,1)

	TAMANHO_FOV =
		math.floor(50 + valor * 350)

	TextoFOV.Text =
		"TAMANHO DO FOV: "..TAMANHO_FOV

	Preenchimento.Size =
		UDim2.new(valor,0,1,0)

	Pino.Position =
		UDim2.new(valor,0,0.5,0)
end

Pino.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		arrastandoFOV = true
	end
end)

FundoSlider.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		local x = input.Position.X
		local inicio = FundoSlider.AbsolutePosition.X
		local largura = FundoSlider.AbsoluteSize.X

		AtualizarFOV(
			(x-inicio)/largura
		)

		arrastandoFOV = true
	end
end)

UserInputService.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		arrastandoFOV = false
	end
end)

UserInputService.InputChanged:Connect(function(input)

	if not arrastandoFOV then
		return
	end

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		local x = input.Position.X
		local inicio = FundoSlider.AbsolutePosition.X
		local largura = FundoSlider.AbsoluteSize.X

		AtualizarFOV(
			(x-inicio)/largura
		)
	end
end)

--========================================================
-- CÍRCULO FOV
--========================================================

local CirculoFOV = Instance.new("Frame")
CirculoFOV.Size =
	UDim2.fromOffset(
		TAMANHO_FOV*2,
		TAMANHO_FOV*2
	)

CirculoFOV.AnchorPoint =
	Vector2.new(0.5,0.5)

CirculoFOV.Position =
	UDim2.fromScale(0.5,0.5)

CirculoFOV.BackgroundTransparency = 1
CirculoFOV.Visible = false
CirculoFOV.Parent = Interface

local cfc = Instance.new("UICorner")
cfc.CornerRadius = UDim.new(1,0)
cfc.Parent = CirculoFOV

local cfs = Instance.new("UIStroke")
cfs.Color = Color3.fromRGB(255,0,0)
cfs.Thickness = 2
cfs.Parent = CirculoFOV

--========================================================
-- ESP
--========================================================

local ESPs = {}

local function CriarESP(player)

	if ESPs[player] then
		return ESPs[player]
	end

	local caixa = Instance.new("Frame")

	caixa.BackgroundTransparency = 1
	caixa.BorderSizePixel = 0
	caixa.Visible = false
	caixa.Parent = Interface

	local stroke = Instance.new("UIStroke")
	stroke.Color = Color3.fromRGB(255,0,0)
	stroke.Thickness = 2
	stroke.Parent = caixa

	ESPs[player] = caixa

	return caixa
end

Players.PlayerRemoving:Connect(function(player)

	if ESPs[player] then
		ESPs[player]:Destroy()
		ESPs[player] = nil
	end
end)

local function Limites(character)

	local cf,size =
		character:GetBoundingBox()

	local metade = size/2

	local minX = math.huge
	local maxX = -math.huge
	local minY = math.huge
	local maxY = -math.huge

	local achou = false

	for _,v in ipairs({
		Vector3.new(-metade.X,-metade.Y,-metade.Z),
		Vector3.new(-metade.X,-metade.Y,metade.Z),
		Vector3.new(-metade.X,metade.Y,-metade.Z),
		Vector3.new(-metade.X,metade.Y,metade.Z),
		Vector3.new(metade.X,-metade.Y,-metade.Z),
		Vector3.new(metade.X,-metade.Y,metade.Z),
		Vector3.new(metade.X,metade.Y,-metade.Z),
		Vector3.new(metade.X,metade.Y,metade.Z)
	}) do

		local world = cf:PointToWorldSpace(v)
		local screen = Camera:WorldToViewportPoint(world)

		if screen.Z > 0 then

			achou = true

			minX = math.min(minX,screen.X)
			maxX = math.max(maxX,screen.X)
			minY = math.min(minY,screen.Y)
			maxY = math.max(maxY,screen.Y)
		end
	end

	if not achou then
		return nil
	end

	return minX,maxX,minY,maxY
end

--========================================================
-- MIRA
--========================================================

local function PegarParte(character)

	if PARTE_MIRA == "Cabeça" then

		return character:FindFirstChild("Head")

	elseif PARTE_MIRA == "Torso" then

		return character:FindFirstChild("UpperTorso")
			or character:FindFirstChild("Torso")

	else

		return character:FindFirstChild("LeftLowerLeg")
			or character:FindFirstChild("LeftUpperLeg")
			or character:FindFirstChild("Left Leg")
	end
end

local function PegarAlvo()

	local centro = Vector2.new(
		Camera.ViewportSize.X/2,
		Camera.ViewportSize.Y/2
	)

	local alvoFinal = nil
	local menor = TAMANHO_FOV

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then

			local char = player.Character

			if char then

				local humanoid =
					char:FindFirstChildOfClass("Humanoid")

				local parte =
					PegarParte(char)

				if humanoid
					and humanoid.Health > 0
					and parte then

					local pos,visivel =
						Camera:WorldToViewportPoint(
							parte.Position
						)

					if visivel then

						local distancia =
							(
								Vector2.new(
									pos.X,
									pos.Y
								)-centro
							).Magnitude

						if distancia < menor then

							menor = distancia
							alvoFinal = parte
						end
					end
				end
			end
		end
	end

	return alvoFinal
end

--========================================================
-- AÇÕES
--========================================================

local function CriarAcao(texto,y)

	local b = Instance.new("TextButton")

	b.Size = UDim2.new(1,-24,0,48)
	b.Position = UDim2.fromOffset(12,y)
	b.BackgroundColor3 = Color3.fromRGB(42,42,42)
	b.BorderSizePixel = 0
	b.Text = texto
	b.TextColor3 = Color3.fromRGB(235,235,235)
	b.Font = Enum.Font.GothamBold
	b.TextSize = 13
	b.Parent = Home

	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0,6)
	c.Parent = b

	return b
end

local TituloJogador = Instance.new("TextLabel")
TituloJogador.Size = UDim2.new(1,-24,0,25)
TituloJogador.Position = UDim2.fromOffset(12,350)
TituloJogador.BackgroundTransparency = 1
TituloJogador.Text = "JOGADOR"
TituloJogador.TextColor3 = Color3.fromRGB(160,160,160)
TituloJogador.Font = Enum.Font.GothamBold
TituloJogador.TextSize = 11
TituloJogador.TextXAlignment = Enum.TextXAlignment.Left
TituloJogador.Parent = Home

local BotaoSpawn =
	CriarAcao(
		"Marcar Spawn Neste Local",
		380
	)

BotaoSpawn.MouseButton1Click:Connect(function()

	local char = LocalPlayer.Character
	if not char then return end

	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end

	spawnMarcado = root.CFrame

	if marcadorSpawn then
		marcadorSpawn:Destroy()
	end

	marcadorSpawn = Instance.new("Part")
	marcadorSpawn.Size = Vector3.new(4,0.25,4)
	marcadorSpawn.CFrame =
		root.CFrame * CFrame.new(0,-3,0)

	marcadorSpawn.Anchored = true
	marcadorSpawn.CanCollide = false
	marcadorSpawn.CanTouch = false
	marcadorSpawn.CanQuery = false
	marcadorSpawn.Material = Enum.Material.Neon
	marcadorSpawn.Color = Color3.fromRGB(255,0,0)
	marcadorSpawn.Transparency = 0.25
	marcadorSpawn.Parent = workspace

	BotaoSpawn.Text = "✓  Spawn Marcado!"

	task.delay(1.5,function()

		if BotaoSpawn.Parent then
			BotaoSpawn.Text =
				"Marcar Spawn Neste Local"
		end
	end)
end)

local BotaoVoltar =
	CriarAcao(
		"Voltar Para Spawn",
		436
	)

BotaoVoltar.MouseButton1Click:Connect(function()

	if not spawnMarcado then

		BotaoVoltar.Text =
			"Marque um Spawn Primeiro"

		task.delay(1.5,function()

			if BotaoVoltar.Parent then
				BotaoVoltar.Text =
					"Voltar Para Spawn"
			end
		end)

		return
	end

	local char = LocalPlayer.Character
	if not char then return end

	local root = char:FindFirstChild("HumanoidRootPart")

	if root then

		root.CFrame =
			spawnMarcado *
			CFrame.new(0,3,0)
	end
end)

--========================================================
-- VIDA INFINITA
--========================================================

local BotaoVidaInfinita =
	CriarAcao(
		"Vida Infinita: DESLIGADA",
		492
	)

local function DesconectarVida()

	if conexaoVida then

		conexaoVida:Disconnect()
		conexaoVida = nil
	end

	personagemProtegido = nil
end

local function AtivarVida(character)

	DesconectarVida()

	local humanoid =
		character:FindFirstChildOfClass("Humanoid")

	if not humanoid then
		return
	end

	personagemProtegido = character

	humanoid.Health =
		humanoid.MaxHealth

	conexaoVida =
		humanoid:GetPropertyChangedSignal(
			"Health"
		):Connect(function()

			if not VIDA_INFINITA then
				return
			end

			if humanoid.Parent
				and humanoid.Health < humanoid.MaxHealth then

				humanoid.Health =
					humanoid.MaxHealth
			end
		end)
end

BotaoVidaInfinita.MouseButton1Click:Connect(function()

	VIDA_INFINITA =
		not VIDA_INFINITA

	if VIDA_INFINITA then

		BotaoVidaInfinita.Text =
			"Vida Infinita: LIGADA"

		if LocalPlayer.Character then
			AtivarVida(LocalPlayer.Character)
		end

	else

		BotaoVidaInfinita.Text =
			"Vida Infinita: DESLIGADA"

		DesconectarVida()
	end
end)

--========================================================
-- REGENERAR
--========================================================

local BotaoVida =
	CriarAcao(
		"Regenerar Vida",
		548
	)

BotaoVida.MouseButton1Click:Connect(function()

	local char = LocalPlayer.Character
	if not char then return end

	local humanoid =
		char:FindFirstChildOfClass("Humanoid")

	if humanoid then

		humanoid.Health =
			humanoid.MaxHealth

		BotaoVida.Text =
			"✓  Vida Completa!"

		task.delay(1.5,function()

			if BotaoVida.Parent then
				BotaoVida.Text =
					"Regenerar Vida"
			end
		end)
	end
end)


--========================================================
-- ENCHER BARRA DE FOME
--========================================================

local BotaoFome =
	CriarAcao(
		"Encher Barra de Fome",
		604
	)

local function EncherFome()
	-- Procura um NumberValue/IntValue chamado Fome no Player
	-- ou no Character.
	local fome = LocalPlayer:FindFirstChild("Fome")

	if not fome and LocalPlayer.Character then
		fome = LocalPlayer.Character:FindFirstChild("Fome")
	end

	if fome and (fome:IsA("NumberValue") or fome:IsA("IntValue")) then
		fome.Value = 100
	end

	-- Também suporta sistemas que usam Attribute.
	if LocalPlayer:GetAttribute("Fome") ~= nil then
		LocalPlayer:SetAttribute("Fome",100)
	end

	if LocalPlayer.Character
		and LocalPlayer.Character:GetAttribute("Fome") ~= nil then

		LocalPlayer.Character:SetAttribute("Fome",100)
	end
end

BotaoFome.MouseButton1Click:Connect(function()

	EncherFome()

	BotaoFome.Text = "✓  Fome Completa!"

	task.delay(1.5,function()

		if BotaoFome.Parent then
			BotaoFome.Text =
				"Encher Barra de Fome"
		end
	end)
end)


--========================================================
-- ENCHER BARRA DE SEDE
--========================================================

local BotaoSede =
	CriarAcao(
		"Encher Barra de Sede",
		660
	)

local function EncherSede()
	-- Procura um NumberValue/IntValue chamado Sede no Player
	-- ou no Character.
	local sede = LocalPlayer:FindFirstChild("Sede")

	if not sede and LocalPlayer.Character then
		sede = LocalPlayer.Character:FindFirstChild("Sede")
	end

	if sede and (sede:IsA("NumberValue") or sede:IsA("IntValue")) then
		sede.Value = 100
	end

	-- Também suporta sistemas que usam Attribute.
	if LocalPlayer:GetAttribute("Sede") ~= nil then
		LocalPlayer:SetAttribute("Sede",100)
	end

	if LocalPlayer.Character
		and LocalPlayer.Character:GetAttribute("Sede") ~= nil then

		LocalPlayer.Character:SetAttribute("Sede",100)
	end
end

BotaoSede.MouseButton1Click:Connect(function()

	EncherSede()

	BotaoSede.Text = "✓  Sede Completa!"

	task.delay(1.5,function()

		if BotaoSede.Parent then
			BotaoSede.Text =
				"Encher Barra de Sede"
		end
	end)
end)

--========================================================
-- RESPAWN
--========================================================

LocalPlayer.CharacterAdded:Connect(function(character)

	task.wait(0.2)

	if VIDA_INFINITA then
		AtivarVida(character)
	end

	if spawnMarcado then

		local root =
			character:WaitForChild(
				"HumanoidRootPart",
				10
			)

		if root then

			task.wait(0.2)

			root.CFrame =
				spawnMarcado *
				CFrame.new(0,3,0)
		end
	end
end)

--========================================================
-- TROCA DE TELAS
--========================================================

local function AbrirHome()

	Home.Visible = true
	TelaTP.Visible = false
	TelaTools.Visible = false

	Titulo.Text = "JF PAINEL"

	Home.CanvasPosition =
		Vector2.new(0,0)
end

local function AbrirTP()

	Home.Visible = false
	TelaTP.Visible = true
	TelaTools.Visible = false

	Titulo.Text =
		"JF PAINEL • TP PLAYER"

	AtualizarPlayers()
end

BotaoHome.MouseButton1Click:Connect(function()
	AbrirHome()
end)

BotaoTP.MouseButton1Click:Connect(function()
	AbrirTP()
end)

BotaoTools.MouseButton1Click:Connect(function()
	Home.Visible = false
	TelaTP.Visible = false
	TelaTools.Visible = true

	Titulo.Text = "JF PAINEL • TOOLS"
	CarregarTools()
end)

BotaoConfig.MouseButton1Click:Connect(function()

	AbrirHome()

	task.defer(function()

		Home.CanvasPosition =
			Vector2.new(0,600)
	end)
end)

--========================================================
-- MINIMIZAR
--========================================================

local minimizado = false

Minimizar.MouseButton1Click:Connect(function()

	minimizado =
		not minimizado

	Menu.Visible =
		not minimizado

	Home.Visible =
		not minimizado

	TelaTP.Visible = false

	if minimizado then

		Painel.Size =
			UDim2.fromOffset(
				larguraPainel,
				45
			)

		Minimizar.Text = "+"

	else

		Painel.Size =
			UDim2.fromOffset(
				larguraPainel,
				alturaPainel
			)

		Minimizar.Text = "—"

		AbrirHome()
	end
end)

--========================================================
-- FECHAR
--========================================================

Fechar.MouseButton1Click:Connect(function()

	if marcadorSpawn then
		marcadorSpawn:Destroy()
	end

	if conexaoVida then
		conexaoVida:Disconnect()
	end

	for _,caixa in pairs(ESPs) do

		if caixa then
			caixa:Destroy()
		end
	end

	Interface:Destroy()
end)

--========================================================
-- ATUALIZAÇÃO
--========================================================

RunService.RenderStepped:Connect(function()

	-- FOV

	CirculoFOV.Visible =
		FOV_ATIVADO

	CirculoFOV.Size =
		UDim2.fromOffset(
			TAMANHO_FOV*2,
			TAMANHO_FOV*2
		)

	-- VIDA

	if VIDA_INFINITA then

		local char = LocalPlayer.Character

		if char == personagemProtegido then

			local humanoid =
				char:FindFirstChildOfClass("Humanoid")

			if humanoid
				and humanoid.Parent
				and humanoid.Health < humanoid.MaxHealth then

				humanoid.Health =
					humanoid.MaxHealth
			end
		end
	end

	-- ESP

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then

			local caixa =
				CriarESP(player)

			local char =
				player.Character

			if ESP_ATIVADO and char then

				local humanoid =
					char:FindFirstChildOfClass("Humanoid")

				if humanoid
					and humanoid.Health > 0 then

					local minX,maxX,minY,maxY =
						Limites(char)

					if minX then

						caixa.Visible = true

						caixa.Position =
							UDim2.fromOffset(
								minX,
								minY
							)

						caixa.Size =
							UDim2.fromOffset(
								maxX-minX,
								maxY-minY
							)

					else

						caixa.Visible = false
					end

				else

					caixa.Visible = false
				end

			else

				caixa.Visible = false
			end
		end
	end

	-- MIRA

	if MIRA_ATIVADA
		and FOV_ATIVADO then

		local alvo =
			PegarAlvo()

		if alvo then

			local destino =
				CFrame.lookAt(
					Camera.CFrame.Position,
					alvo.Position
				)

			Camera.CFrame =
				Camera.CFrame:Lerp(
					destino,
					SUAVIDADE_MIRA
				)
		end
	end
end)

--========================================================
-- INSERT
--========================================================

UserInputService.InputBegan:Connect(function(input,processado)

	if processado then
		return
	end

	if input.KeyCode == Enum.KeyCode.Insert then

		Painel.Visible =
			not Painel.Visible
	end
end)

--========================================================
-- INICIAR
--========================================================

AbrirHome()
AtualizarPlayers()
