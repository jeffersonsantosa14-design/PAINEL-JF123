--========================================================
-- JF PAINEL
-- LOCAL SCRIPT ÚNICO
--========================================================

--// SERVIÇOS
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

--// PLAYER
local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

--========================================================
-- CONFIGURAÇÕES
--========================================================

local ESP_ATIVADO = false
local FOV_ATIVADO = false
local BARRA_VIDA_ATIVADA = false
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
-- REMOVE PAINEL ANTIGO
--========================================================

local antigo = PlayerGui:FindFirstChild("JF_Painel")

if antigo then
	antigo:Destroy()
end

--========================================================
-- GUI PRINCIPAL
--========================================================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "JF_Painel"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = PlayerGui

--========================================================
-- PAINEL
--========================================================

local Painel = Instance.new("Frame")
Painel.Name = "Painel"
Painel.Size = UDim2.new(0, 500, 0, 350)
Painel.Position = UDim2.new(0.5, -250, 0.5, -175)
Painel.BackgroundColor3 = Color3.fromRGB(24, 24, 27)
Painel.BorderSizePixel = 0
Painel.Parent = ScreenGui

local PainelCanto = Instance.new("UICorner")
PainelCanto.CornerRadius = UDim.new(0, 8)
PainelCanto.Parent = Painel

--========================================================
-- TOPO
--========================================================

local Topo = Instance.new("Frame")
Topo.Name = "Topo"
Topo.Size = UDim2.new(1, 0, 0, 45)
Topo.BackgroundColor3 = Color3.fromRGB(31, 31, 35)
Topo.BorderSizePixel = 0
Topo.Parent = Painel

local TopoCanto = Instance.new("UICorner")
TopoCanto.CornerRadius = UDim.new(0, 8)
TopoCanto.Parent = Topo

local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1, -100, 1, 0)
Titulo.Position = UDim2.new(0, 15, 0, 0)
Titulo.BackgroundTransparency = 1
Titulo.Text = "JF PAINEL"
Titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
Titulo.Font = Enum.Font.GothamBold
Titulo.TextSize = 18
Titulo.TextXAlignment = Enum.TextXAlignment.Left
Titulo.Parent = Topo

local Minimizar = Instance.new("TextButton")
Minimizar.Name = "Minimizar"
Minimizar.Size = UDim2.new(0, 35, 0, 30)
Minimizar.Position = UDim2.new(1, -75, 0, 7)
Minimizar.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
Minimizar.Text = "-"
Minimizar.TextColor3 = Color3.fromRGB(255, 255, 255)
Minimizar.Font = Enum.Font.GothamBold
Minimizar.TextSize = 20
Minimizar.BorderSizePixel = 0
Minimizar.Parent = Topo

local MinimizarCanto = Instance.new("UICorner")
MinimizarCanto.CornerRadius = UDim.new(0, 5)
MinimizarCanto.Parent = Minimizar

local Fechar = Instance.new("TextButton")
Fechar.Name = "Fechar"
Fechar.Size = UDim2.new(0, 35, 0, 30)
Fechar.Position = UDim2.new(1, -38, 0, 7)
Fechar.BackgroundColor3 = Color3.fromRGB(170, 45, 45)
Fechar.Text = "X"
Fechar.TextColor3 = Color3.fromRGB(255, 255, 255)
Fechar.Font = Enum.Font.GothamBold
Fechar.TextSize = 15
Fechar.BorderSizePixel = 0
Fechar.Parent = Topo

local FecharCanto = Instance.new("UICorner")
FecharCanto.CornerRadius = UDim.new(0, 5)
FecharCanto.Parent = Fechar

--========================================================
-- ARRASTAR PAINEL
--========================================================

local arrastando = false
local inicioMouse
local inicioPos

Topo.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		arrastando = true
		inicioMouse = input.Position
		inicioPos = Painel.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				arrastando = false
			end
		end)
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if not arrastando then
		return
	end

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		local delta = input.Position - inicioMouse

		Painel.Position = UDim2.new(
			inicioPos.X.Scale,
			inicioPos.X.Offset + delta.X,
			inicioPos.Y.Scale,
			inicioPos.Y.Offset + delta.Y
		)
	end
end)

--========================================================
-- MENU LATERAL
--========================================================

local Menu = Instance.new("Frame")
Menu.Name = "Menu"
Menu.Size = UDim2.new(0, 115, 1, -55)
Menu.Position = UDim2.new(0, 10, 0, 50)
Menu.BackgroundColor3 = Color3.fromRGB(29, 29, 33)
Menu.BorderSizePixel = 0
Menu.Parent = Painel

local MenuCanto = Instance.new("UICorner")
MenuCanto.CornerRadius = UDim.new(0, 7)
MenuCanto.Parent = Menu

local Conteudo = Instance.new("Frame")
Conteudo.Name = "Conteudo"
Conteudo.Size = UDim2.new(1, -135, 1, -55)
Conteudo.Position = UDim2.new(0, 125, 0, 50)
Conteudo.BackgroundColor3 = Color3.fromRGB(27, 27, 30)
Conteudo.BorderSizePixel = 0
Conteudo.Parent = Painel

local ConteudoCanto = Instance.new("UICorner")
ConteudoCanto.CornerRadius = UDim.new(0, 7)
ConteudoCanto.Parent = Conteudo

--========================================================
-- FUNÇÃO BOTÃO MENU
--========================================================

local function CriarBotaoMenu(nome, texto, ordem)
	local botao = Instance.new("TextButton")

	botao.Name = nome
	botao.Size = UDim2.new(1, -10, 0, 40)
	botao.Position = UDim2.new(0, 5, 0, 5 + ((ordem - 1) * 45))
	botao.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
	botao.BorderSizePixel = 0
	botao.Text = texto
	botao.TextColor3 = Color3.fromRGB(235, 235, 235)
	botao.Font = Enum.Font.GothamBold
	botao.TextSize = 13
	botao.Parent = Menu

	local canto = Instance.new("UICorner")
	canto.CornerRadius = UDim.new(0, 5)
	canto.Parent = botao

	return botao
end

local BotaoHome = CriarBotaoMenu("Home", "HOME", 1)
local BotaoTP = CriarBotaoMenu("TPPlayer", "TP PLAYER", 2)
local BotaoTools = CriarBotaoMenu("Tools", "TOOLS", 3)
local BotaoConfig = CriarBotaoMenu("Config", "CONFIG", 4)

--========================================================
-- FUNÇÃO LIMPAR CONTEÚDO
--========================================================

local function LimparConteudo()
	for _, obj in ipairs(Conteudo:GetChildren()) do
		obj:Destroy()
	end
end

--========================================================
-- CRIAR TOGGLE
--========================================================

local function CriarToggle(texto, estado, callback, y)
	local botao = Instance.new("TextButton")

	botao.Size = UDim2.new(1, -20, 0, 40)
	botao.Position = UDim2.new(0, 10, 0, y)
	botao.BackgroundColor3 = estado and Color3.fromRGB(35, 130, 70) or Color3.fromRGB(45, 45, 50)
	botao.BorderSizePixel = 0
	botao.TextColor3 = Color3.fromRGB(255, 255, 255)
	botao.Font = Enum.Font.GothamBold
	botao.TextSize = 13
	botao.Text = texto .. ": " .. (estado and "ATIVADO" or "DESATIVADO")
	botao.Parent = Conteudo

	local canto = Instance.new("UICorner")
	canto.CornerRadius = UDim.new(0, 5)
	canto.Parent = botao

	botao.MouseButton1Click:Connect(function()
		estado = not estado

		botao.BackgroundColor3 = estado
			and Color3.fromRGB(35, 130, 70)
			or Color3.fromRGB(45, 45, 50)

		botao.Text = texto .. ": " .. (estado and "ATIVADO" or "DESATIVADO")

		callback(estado)
	end)

	return botao
end

--========================================================
-- HOME
--========================================================

local function AbrirHome()
	LimparConteudo()

	local titulo = Instance.new("TextLabel")
	titulo.Size = UDim2.new(1, -20, 0, 35)
	titulo.Position = UDim2.new(0, 10, 0, 10)
	titulo.BackgroundTransparency = 1
	titulo.Text = "CONTROLE DO PAINEL"
	titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
	titulo.Font = Enum.Font.GothamBold
	titulo.TextSize = 17
	titulo.TextXAlignment = Enum.TextXAlignment.Left
	titulo.Parent = Conteudo

	CriarToggle("ESP", ESP_ATIVADO, function(valor)
		ESP_ATIVADO = valor
	end, 55)

	CriarToggle("FOV", FOV_ATIVADO, function(valor)
		FOV_ATIVADO = valor
	end, 100)

	CriarToggle("BARRA DE VIDA", BARRA_VIDA_ATIVADA, function(valor)
		BARRA_VIDA_ATIVADA = valor
	end, 145)

	CriarToggle("VIDA INFINITA", VIDA_INFINITA, function(valor)
		VIDA_INFINITA = valor
	end, 190)
end

--========================================================
-- TP PLAYER
--========================================================

local CaixaBuscaPlayer = nil
local ListaPlayers = nil

local function AtualizarPlayers()
	if not ListaPlayers then
		return
	end

	for _, obj in ipairs(ListaPlayers:GetChildren()) do
		if obj:IsA("TextButton") then
			obj:Destroy()
		end
	end

	local texto = CaixaBuscaPlayer and CaixaBuscaPlayer.Text:lower() or ""

	local y = 0

	for _, alvo in ipairs(Players:GetPlayers()) do
		if alvo ~= Player then
			if texto == "" or alvo.Name:lower():find(texto, 1, true) then

				local botao = Instance.new("TextButton")
				botao.Size = UDim2.new(1, -5, 0, 35)
				botao.Position = UDim2.new(0, 0, 0, y)
				botao.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
				botao.BorderSizePixel = 0
				botao.Text = alvo.Name
				botao.TextColor3 = Color3.fromRGB(255, 255, 255)
				botao.Font = Enum.Font.Gotham
				botao.TextSize = 13
				botao.Parent = ListaPlayers

				local canto = Instance.new("UICorner")
				canto.CornerRadius = UDim.new(0, 5)
				canto.Parent = botao

				botao.MouseButton1Click:Connect(function()
					if alvo.Character
						and alvo.Character:FindFirstChild("HumanoidRootPart")
						and Player.Character
						and Player.Character:FindFirstChild("HumanoidRootPart") then

						Player.Character.HumanoidRootPart.CFrame =
							alvo.Character.HumanoidRootPart.CFrame
					end
				end)

				y += 40
			end
		end
	end

	ListaPlayers.CanvasSize = UDim2.new(0, 0, 0, y)
end

local function AbrirTP()
	LimparConteudo()

	local titulo = Instance.new("TextLabel")
	titulo.Size = UDim2.new(1, -20, 0, 30)
	titulo.Position = UDim2.new(0, 10, 0, 8)
	titulo.BackgroundTransparency = 1
	titulo.Text = "TELEPORTAR PARA PLAYER"
	titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
	titulo.Font = Enum.Font.GothamBold
	titulo.TextSize = 16
	titulo.TextXAlignment = Enum.TextXAlignment.Left
	titulo.Parent = Conteudo

	CaixaBuscaPlayer = Instance.new("TextBox")
	CaixaBuscaPlayer.Size = UDim2.new(1, -20, 0, 35)
	CaixaBuscaPlayer.Position = UDim2.new(0, 10, 0, 42)
	CaixaBuscaPlayer.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
	CaixaBuscaPlayer.BorderSizePixel = 0
	CaixaBuscaPlayer.PlaceholderText = "Pesquisar player..."
	CaixaBuscaPlayer.Text = ""
	CaixaBuscaPlayer.TextColor3 = Color3.fromRGB(255, 255, 255)
	CaixaBuscaPlayer.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
	CaixaBuscaPlayer.Font = Enum.Font.Gotham
	CaixaBuscaPlayer.TextSize = 13
	CaixaBuscaPlayer.Parent = Conteudo

	local cantoBusca = Instance.new("UICorner")
	cantoBusca.CornerRadius = UDim.new(0, 5)
	cantoBusca.Parent = CaixaBuscaPlayer

	ListaPlayers = Instance.new("ScrollingFrame")
	ListaPlayers.Size = UDim2.new(1, -20, 1, -90)
	ListaPlayers.Position = UDim2.new(0, 10, 0, 85)
	ListaPlayers.BackgroundTransparency = 1
	ListaPlayers.BorderSizePixel = 0
	ListaPlayers.ScrollBarThickness = 5
	ListaPlayers.CanvasSize = UDim2.new(0, 0, 0, 0)
	ListaPlayers.Parent = Conteudo

	CaixaBuscaPlayer:GetPropertyChangedSignal("Text"):Connect(AtualizarPlayers)

	AtualizarPlayers()
end

--========================================================
-- TOOLS
--========================================================

local CaixaBuscaTool
local ListaTools
local InfoTools

--========================================================
-- UTILITÁRIOS DAS TOOLS
--========================================================

local function CaminhoSeguro(obj)
	local caminho = {}

	local atual = obj

	while atual and atual ~= game do
		table.insert(caminho, 1, atual.Name)
		atual = atual.Parent
	end

	return table.concat(caminho, " > ")
end

local function NormalizarNome(texto)
	texto = tostring(texto or "")
	texto = texto:lower()

	texto = texto:gsub("[%s%p_]+", "")

	return texto
end

local function DistanciaTexto(a, b)
	a = NormalizarNome(a)
	b = NormalizarNome(b)

	if a == b then
		return 0
	end

	if a:find(b, 1, true) then
		return 1
	end

	if b:find(a, 1, true) then
		return 1
	end

	local maior = math.max(#a, #b)

	if maior == 0 then
		return 0
	end

	local iguais = 0

	for i = 1, math.min(#a, #b) do
		if a:sub(i, i) == b:sub(i, i) then
			iguais += 1
		end
	end

	return maior - iguais
end

local function PontuacaoNome(nome, alvo)
	local nomeN = NormalizarNome(nome)
	local alvoN = NormalizarNome(alvo)

	if nomeN == alvoN then
		return 10000
	end

	if nomeN:find(alvoN, 1, true) then
		return 8000 - math.abs(#nomeN - #alvoN)
	end

	if alvoN:find(nomeN, 1, true) then
		return 7000 - math.abs(#nomeN - #alvoN)
	end

	local distancia = DistanciaTexto(nomeN, alvoN)

	return math.max(0, 5000 - distancia * 100)
end

local function DescobrirOrigem(tool)
	if not tool or not tool.Parent then
		return "Desconhecido"
	end

	if Player.Backpack
		and tool:IsDescendantOf(Player.Backpack) then
		return "Backpack"
	end

	if Player.Character
		and tool:IsDescendantOf(Player.Character) then
		return "Personagem"
	end

	if tool:IsDescendantOf(workspace) then
		return "Workspace"
	end

	if tool:IsDescendantOf(ReplicatedStorage) then
		return "ReplicatedStorage"
	end

	return CaminhoSeguro(tool)
end

local function ColetarTools()
	local resultado = {}
	local vistos = {}

	for _, obj in ipairs(game:GetDescendants()) do
		if obj:IsA("Tool") then
			if not vistos[obj] then
				vistos[obj] = true

				table.insert(resultado, {
					Tool = obj,
					Name = obj.Name,
					Origem = DescobrirOrigem(obj),
					Caminho = CaminhoSeguro(obj)
				})
			end
		end
	end

	return resultado
end

local function LocalizarMelhorTool(nome)
	local alvo = NormalizarNome(nome)

	if alvo == "" then
		return nil
	end

	local melhor = nil
	local melhorPontuacao = -math.huge

	for _, info in ipairs(ColetarTools()) do
		local pontuacao = PontuacaoNome(info.Name, alvo)

		if pontuacao > melhorPontuacao then
			melhorPontuacao = pontuacao
			melhor = info.Tool
		end
	end

	return melhor
end

local function LocalizarToolsParecidas(nome)
	local lista = {}
	local alvo = NormalizarNome(nome)

	if alvo == "" then
		return lista
	end

	for _, info in ipairs(ColetarTools()) do
		local pontuacao = PontuacaoNome(info.Name, alvo)

		if pontuacao >= 1000 then
			table.insert(lista, {
				Tool = info.Tool,
				Name = info.Name,
				Origem = info.Origem,
				Caminho = info.Caminho,
				Pontuacao = pontuacao
			})
		end
	end

	table.sort(lista, function(a, b)
		return a.Pontuacao > b.Pontuacao
	end)

	return lista
end

local function LimparListaTools()
	if not ListaTools then
		return
	end

	for _, obj in ipairs(ListaTools:GetChildren()) do
		if obj:IsA("TextButton") or obj:IsA("Frame") then
			obj:Destroy()
		end
	end
end

local function ResetarBotao(botao)
	if botao and botao.Parent then
		botao.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	end
end

local function JaPossuiTool(nome)
	local backpack = Player:FindFirstChildOfClass("Backpack")

	if backpack and backpack:FindFirstChild(nome) then
		return true
	end

	if Player.Character and Player.Character:FindFirstChild(nome) then
		return true
	end

	return false
end

local function ColocarNoInventario(tool)
	if not tool then
		return false
	end

	local backpack = Player:FindFirstChildOfClass("Backpack")

	if not backpack then
		return false
	end

	if JaPossuiTool(tool.Name) then
		return true
	end

	local sucesso, clone = pcall(function()
		return tool:Clone()
	end)

	if not sucesso or not clone then
		return false
	end

	clone.Parent = backpack

	return true
end

local function CriarItemTool(info, y)
	local botao = Instance.new("TextButton")

	botao.Name = "Tool_" .. tostring(y)
	botao.Size = UDim2.new(1, -5, 0, 48)
	botao.Position = UDim2.new(0, 0, 0, y)
	botao.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	botao.BorderSizePixel = 0
	botao.Text = ""
	botao.Parent = ListaTools

	local canto = Instance.new("UICorner")
	canto.CornerRadius = UDim.new(0, 5)
	canto.Parent = botao

	local nome = Instance.new("TextLabel")
	nome.Size = UDim2.new(1, -110, 0, 22)
	nome.Position = UDim2.new(0, 8, 0, 3)
	nome.BackgroundTransparency = 1
	nome.Text = info.Name
	nome.TextColor3 = Color3.fromRGB(255, 255, 255)
	nome.Font = Enum.Font.GothamBold
	nome.TextSize = 13
	nome.TextXAlignment = Enum.TextXAlignment.Left
	nome.TextTruncate = Enum.TextTruncate.AtEnd
	nome.Parent = botao

	local origem = Instance.new("TextLabel")
	origem.Size = UDim2.new(1, -110, 0, 18)
	origem.Position = UDim2.new(0, 8, 0, 25)
	origem.BackgroundTransparency = 1
	origem.Text = info.Origem
	origem.TextColor3 = Color3.fromRGB(155, 155, 155)
	origem.Font = Enum.Font.Gotham
	origem.TextSize = 10
	origem.TextXAlignment = Enum.TextXAlignment.Left
	origem.TextTruncate = Enum.TextTruncate.AtEnd
	origem.Parent = botao

	local pegar = Instance.new("TextButton")
	pegar.Size = UDim2.new(0, 80, 0, 30)
	pegar.Position = UDim2.new(1, -88, 0.5, -15)
	pegar.BackgroundColor3 = Color3.fromRGB(35, 115, 65)
	pegar.BorderSizePixel = 0
	pegar.Text = "PEGAR"
	pegar.TextColor3 = Color3.fromRGB(255, 255, 255)
	pegar.Font = Enum.Font.GothamBold
	pegar.TextSize = 11
	pegar.Parent = botao

	local cantoPegar = Instance.new("UICorner")
	cantoPegar.CornerRadius = UDim.new(0, 5)
	cantoPegar.Parent = pegar

	pegar.MouseButton1Click:Connect(function()
		local sucesso = ColocarNoInventario(info.Tool)

		if sucesso then
			pegar.Text = "OK"
			pegar.BackgroundColor3 = Color3.fromRGB(40, 150, 75)

			task.delay(1, function()
				if pegar and pegar.Parent then
					pegar.Text = "PEGAR"
					pegar.BackgroundColor3 = Color3.fromRGB(35, 115, 65)
				end
			end)
		else
			pegar.Text = "ERRO"

			task.delay(1, function()
				if pegar and pegar.Parent then
					pegar.Text = "PEGAR"
				end
			end)
		end
	end)

	return botao
end

--========================================================
-- MOSTRAR TOOLS
--========================================================

local function MostrarTools(lista)
	LimparListaTools()

	if not ListaTools then
		return
	end

	local alvo = CaixaBuscaTool and NormalizarNome(CaixaBuscaTool.Text) or ""

	table.sort(lista, function(a, b)
		if alvo ~= "" then
			local pa = PontuacaoNome(a.Name, alvo)
			local pb = PontuacaoNome(b.Name, alvo)

			if pa ~= pb then
				return pa > pb
			end
		end

		return a.Name:lower() < b.Name:lower()
	end)

	local y = 0

	for _, info in ipairs(lista) do
		CriarItemTool(info, y)

		y += 53
	end

	ListaTools.CanvasSize = UDim2.new(0, 0, 0, y)
end

--========================================================
-- CARREGAR TOOLS
--========================================================

local function CarregarTools()
	if not ListaTools then
		return
	end

	local tools = ColetarTools()

	-- Procura especificamente pela ArmaPDSecreta
	local encontrouArma = false

	for _, info in ipairs(tools) do
		if NormalizarNome(info.Name) == NormalizarNome("ArmaPDSecreta") then
			encontrouArma = true
			break
		end
	end

	-- Se não encontrou exatamente, adiciona parecidas
	if not encontrouArma then
		local parecidas = LocalizarToolsParecidas("ArmaPDSecreta")

		for _, info in ipairs(parecidas) do
			if NormalizarNome(info.Name) == NormalizarNome("ArmaPDSecreta") then
				encontrouArma = true
				break
			end
		end
	end

	MostrarTools(tools)
end

--========================================================
-- PROCURAR TOOL
--========================================================

local function ProcurarPorNome(nome)
	nome = nome or "ArmaPDSecreta"

	local texto = NormalizarNome(nome)

	if texto == "" then
		CarregarTools()
		return
	end

	local resultados = LocalizarToolsParecidas(nome)

	-- Busca exata primeiro
	local exatas = {}

	for _, info in ipairs(ColetarTools()) do
		if NormalizarNome(info.Name) == texto then
			table.insert(exatas, {
				Tool = info.Tool,
				Name = info.Name,
				Origem = info.Origem,
				Caminho = info.Caminho,
				Pontuacao = 100000
			})
		end
	end

	for _, info in ipairs(resultados) do
		local jaExiste = false

		for _, exata in ipairs(exatas) do
			if exata.Tool == info.Tool then
				jaExiste = true
				break
			end
		end

		if not jaExiste then
			table.insert(exatas, info)
		end
	end

	MostrarTools(exatas)
end

--========================================================
-- TELA TOOLS
--========================================================

local function AbrirTools()
	LimparConteudo()

	local titulo = Instance.new("TextLabel")
	titulo.Size = UDim2.new(1, -20, 0, 30)
	titulo.Position = UDim2.new(0, 10, 0, 8)
	titulo.BackgroundTransparency = 1
	titulo.Text = "TOOLS DO SERVIDOR"
	titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
	titulo.Font = Enum.Font.GothamBold
	titulo.TextSize = 16
	titulo.TextXAlignment = Enum.TextXAlignment.Left
	titulo.Parent = Conteudo

	CaixaBuscaTool = Instance.new("TextBox")
	CaixaBuscaTool.Name = "BuscaTools"
	CaixaBuscaTool.Size = UDim2.new(1, -142, 0, 35)
	CaixaBuscaTool.Position = UDim2.new(0, 10, 0, 42)
	CaixaBuscaTool.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
	CaixaBuscaTool.BorderSizePixel = 0
	CaixaBuscaTool.PlaceholderText = "Pesquisar Tool..."
	CaixaBuscaTool.Text = ""
	CaixaBuscaTool.TextColor3 = Color3.fromRGB(255, 255, 255)
	CaixaBuscaTool.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
	CaixaBuscaTool.Font = Enum.Font.Gotham
	CaixaBuscaTool.TextSize = 12
	CaixaBuscaTool.ClearTextOnFocus = false
	CaixaBuscaTool.Parent = Conteudo

	local cantoBusca = Instance.new("UICorner")
	cantoBusca.CornerRadius = UDim.new(0, 5)
	cantoBusca.Parent = CaixaBuscaTool

	local BuscarTool = Instance.new("TextButton")
	BuscarTool.Name = "Procurar"
	BuscarTool.Size = UDim2.new(0, 60, 0, 35)
	BuscarTool.Position = UDim2.new(1, -125, 0, 42)
	BuscarTool.BackgroundColor3 = Color3.fromRGB(40, 110, 170)
	BuscarTool.BorderSizePixel = 0
	BuscarTool.Text = "BUSCAR"
	BuscarTool.TextColor3 = Color3.fromRGB(255, 255, 255)
	BuscarTool.Font = Enum.Font.GothamBold
	BuscarTool.TextSize = 9
	BuscarTool.Parent = Conteudo

	local cantoBuscar = Instance.new("UICorner")
	cantoBuscar.CornerRadius = UDim.new(0, 5)
	cantoBuscar.Parent = BuscarTool

	local AtualizarTools = Instance.new("TextButton")
	AtualizarTools.Name = "Atualizar"
	AtualizarTools.Size = UDim2.new(0, 60, 0, 35)
	AtualizarTools.Position = UDim2.new(1, -60, 0, 42)
	AtualizarTools.BackgroundColor3 = Color3.fromRGB(45, 130, 75)
	AtualizarTools.BorderSizePixel = 0
	AtualizarTools.Text = "ATUALIZAR"
	AtualizarTools.TextColor3 = Color3.fromRGB(255, 255, 255)
	AtualizarTools.Font = Enum.Font.GothamBold
	AtualizarTools.TextSize = 8
	AtualizarTools.Parent = Conteudo

	local cantoAtualizar = Instance.new("UICorner")
	cantoAtualizar.CornerRadius = UDim.new(0, 5)
	cantoAtualizar.Parent = AtualizarTools

	InfoTools = Instance.new("TextLabel")
	InfoTools.Size = UDim2.new(1, -20, 0, 25)
	InfoTools.Position = UDim2.new(0, 10, 0, 80)
	InfoTools.BackgroundTransparency = 1
	InfoTools.Text = "Digite o nome da Tool ou procure por ArmaPDSecreta."
	InfoTools.TextColor3 = Color3.fromRGB(150, 150, 150)
	InfoTools.Font = Enum.Font.Gotham
	InfoTools.TextSize = 10
	InfoTools.TextXAlignment = Enum.TextXAlignment.Left
	InfoTools.Parent = Conteudo

	ListaTools = Instance.new("ScrollingFrame")
	ListaTools.Name = "ListaTools"
	ListaTools.Size = UDim2.new(1, -20, 1, -115)
	ListaTools.Position = UDim2.new(0, 10, 0, 108)
	ListaTools.BackgroundTransparency = 1
	ListaTools.BorderSizePixel = 0
	ListaTools.ScrollBarThickness = 5
	ListaTools.CanvasSize = UDim2.new(0, 0, 0, 0)
	ListaTools.Parent = Conteudo

	BuscarTool.MouseButton1Click:Connect(function()
		ProcurarPorNome(CaixaBuscaTool.Text)
	end)

	AtualizarTools.MouseButton1Click:Connect(function()
		CarregarTools()
	end)

	CaixaBuscaTool.FocusLost:Connect(function(enterPressed)
		if enterPressed then
			ProcurarPorNome(CaixaBuscaTool.Text)
		end
	end)

	CarregarTools()
end

--========================================================
-- FOV
--========================================================

local FOVCircle = Instance.new("Frame")
FOVCircle.Name = "FOVCircle"
FOVCircle.AnchorPoint = Vector2.new(0.5, 0.5)
FOVCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
FOVCircle.Size = UDim2.new(0, TAMANHO_FOV * 2, 0, TAMANHO_FOV * 2)
FOVCircle.BackgroundTransparency = 1
FOVCircle.BorderSizePixel = 0
FOVCircle.Visible = false
FOVCircle.ZIndex = 50
FOVCircle.Parent = ScreenGui

local FOVStroke = Instance.new("UIStroke")
FOVStroke.Thickness = 1
FOVStroke.Color = Color3.fromRGB(255, 255, 255)
FOVStroke.Parent = FOVCircle

local FOVCorner = Instance.new("UICorner")
FOVCorner.CornerRadius = UDim.new(1, 0)
FOVCorner.Parent = FOVCircle

--========================================================
-- MIRA
--========================================================

local function PegarParte(character)
	if not character then
		return nil
	end

	if PARTE_MIRA == "Cabeça" then
		return character:FindFirstChild("Head")
	end

	if PARTE_MIRA == "Torso" then
		return character:FindFirstChild("UpperTorso")
			or character:FindFirstChild("Torso")
	end

	if PARTE_MIRA == "HumanoidRootPart" then
		return character:FindFirstChild("HumanoidRootPart")
	end

	return character:FindFirstChild("Head")
end

local function PegarAlvo()
	if not Camera then
		return nil
	end

	local centro = Vector2.new(
		Camera.ViewportSize.X / 2,
		Camera.ViewportSize.Y / 2
	)

	local melhor = nil
	local menorDistancia = TAMANHO_FOV

	for _, alvo in ipairs(Players:GetPlayers()) do
		if alvo ~= Player and alvo.Character then

			local humanoid = alvo.Character:FindFirstChildOfClass("Humanoid")
			local parte = PegarParte(alvo.Character)

			if humanoid and humanoid.Health > 0 and parte then

				local pos, visivel = Camera:WorldToViewportPoint(parte.Position)

				if visivel and pos.Z > 0 then
					local distancia = (
						Vector2.new(pos.X, pos.Y) - centro
					).Magnitude

					if distancia < menorDistancia then
						menorDistancia = distancia
						melhor = parte
					end
				end
			end
		end
	end

	return melhor
end

--========================================================
-- ESP
--========================================================

local function RemoverESP(character)
	if not character then
		return
	end

	local esp = character:FindFirstChild("JF_ESP")

	if esp then
		esp:Destroy()
	end
end

local function CriarESP(character)
	if not character then
		return
	end

	if character == Player.Character then
		return
	end

	local humanoid = character:FindFirstChildOfClass("Humanoid")

	if not humanoid then
		return
	end

	local antigoESP = character:FindFirstChild("JF_ESP")

	if antigoESP then
		antigoESP:Destroy()
	end

	local highlight = Instance.new("Highlight")
	highlight.Name = "JF_ESP"
	highlight.FillTransparency = 1
	highlight.OutlineTransparency = 0
	highlight.OutlineColor = Color3.fromRGB(255, 0, 0)
	highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	highlight.Parent = character
end

local function AtualizarESP()
	for _, alvo in ipairs(Players:GetPlayers()) do
		if alvo ~= Player and alvo.Character then
			if ESP_ATIVADO then
				CriarESP(alvo.Character)
			else
				RemoverESP(alvo.Character)
			end
		end
	end
end

--========================================================
-- BARRA DE VIDA
--========================================================

local function AtualizarBarraVida()
	for _, alvo in ipairs(Players:GetPlayers()) do
		if alvo ~= Player and alvo.Character then

			local character = alvo.Character
			local humanoid = character:FindFirstChildOfClass("Humanoid")

			if humanoid then

				local antigo = character:FindFirstChild("JF_Vida")

				if not BARRA_VIDA_ATIVADA then
					if antigo then
						antigo:Destroy()
					end
				else

					local gui = antigo

					if not gui then
						gui = Instance.new("BillboardGui")
						gui.Name = "JF_Vida"
						gui.Size = UDim2.new(0, 8, 0, 70)
						gui.StudsOffset = Vector3.new(-3, 0, 0)
						gui.AlwaysOnTop = true
						gui.Parent = character

						local fundo = Instance.new("Frame")
						fundo.Name = "Fundo"
						fundo.Size = UDim2.new(1, 0, 1, 0)
						fundo.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
						fundo.BorderSizePixel = 0
						fundo.Parent = gui

						local barra = Instance.new("Frame")
						barra.Name = "Barra"
						barra.AnchorPoint = Vector2.new(0, 1)
						barra.Position = UDim2.new(0, 0, 1, 0)
						barra.Size = UDim2.new(1, 0, 1, 0)
						barra.BackgroundColor3 = Color3.fromRGB(50, 220, 80)
						barra.BorderSizePixel = 0
						barra.Parent = fundo
					end

					local fundo = gui:FindFirstChild("Fundo")

					if fundo then
						local barra = fundo:FindFirstChild("Barra")

						if barra then
							local porcentagem = math.clamp(
								humanoid.Health / math.max(humanoid.MaxHealth, 1),
								0,
								1
							)

							barra.Size = UDim2.new(
								1,
								0,
								porcentagem,
								0
							)
						end
					end
				end
			end
		end
	end
end

--========================================================
-- SPAWN MARCADO
--========================================================

local function MarcarSpawn()
	if not Player.Character then
		return
	end

	local root = Player.Character:FindFirstChild("HumanoidRootPart")

	if not root then
		return
	end

	spawnMarcado = root.CFrame

	if marcadorSpawn then
		marcadorSpawn:Destroy()
	end

	marcadorSpawn = Instance.new("Part")
	marcadorSpawn.Name = "JF_MarcadorSpawn"
	marcadorSpawn.Size = Vector3.new(2, 0.2, 2)
	marcadorSpawn.Anchored = true
	marcadorSpawn.CanCollide = false
	marcadorSpawn.Transparency = 0.4
	marcadorSpawn.Color = Color3.fromRGB(0, 170, 255)
	marcadorSpawn.CFrame = root.CFrame * CFrame.new(0, -3, 0)
	marcadorSpawn.Parent = workspace
end

local function VoltarSpawn()
	if not spawnMarcado then
		return
	end

	if not Player.Character then
		return
	end

	local root = Player.Character:FindFirstChild("HumanoidRootPart")

	if root then
		root.CFrame = spawnMarcado
	end
end

--========================================================
-- VIDA INFINITA
--========================================================

local function AtivarVidaInfinita()
	if conexaoVida then
		conexaoVida:Disconnect()
		conexaoVida = nil
	end

	if not Player.Character then
		return
	end

	local humanoid = Player.Character:FindFirstChildOfClass("Humanoid")

	if not humanoid then
		return
	end

	personagemProtegido = Player.Character

	conexaoVida = humanoid.HealthChanged:Connect(function()
		if VIDA_INFINITA and humanoid.Health < humanoid.MaxHealth then
			humanoid.Health = humanoid.MaxHealth
		end
	end)

	humanoid.Health = humanoid.MaxHealth
end

local function DesativarVidaInfinita()
	if conexaoVida then
		conexaoVida:Disconnect()
		conexaoVida = nil
	end

	personagemProtegido = nil
end

--========================================================
-- AÇÕES CONFIG
--========================================================

local function ProcurarValor(nome)
	if not Player.Character then
		return nil
	end

	local valor = Player.Character:FindFirstChild(nome)

	if valor then
		return valor
	end

	valor = Player:FindFirstChild(nome)

	if valor then
		return valor
	end

	return nil
end

local function PreencherFome()
	local nomes = {
		"Fome",
		"fome",
		"Hunger",
		"hunger"
	}

	for _, nome in ipairs(nomes) do
		local valor = ProcurarValor(nome)

		if valor and valor:IsA("NumberValue") or valor and valor:IsA("IntValue") then
			valor.Value = 100
			return
		end
	end

	if Player.Character then
		Player.Character:SetAttribute("Fome", 100)
		Player.Character:SetAttribute("fome", 100)
		Player.Character:SetAttribute("Hunger", 100)
	end

	Player:SetAttribute("Fome", 100)
	Player:SetAttribute("fome", 100)
	Player:SetAttribute("Hunger", 100)
end

local function PreencherSede()
	local nomes = {
		"Sede",
		"sede",
		"Thirst",
		"thirst"
	}

	for _, nome in ipairs(nomes) do
		local valor = ProcurarValor(nome)

		if valor and valor:IsA("NumberValue") or valor and valor:IsA("IntValue") then
			valor.Value = 100
			return
		end
	end

	if Player.Character then
		Player.Character:SetAttribute("Sede", 100)
		Player.Character:SetAttribute("sede", 100)
		Player.Character:SetAttribute("Thirst", 100)
	end

	Player:SetAttribute("Sede", 100)
	Player:SetAttribute("sede", 100)
	Player:SetAttribute("Thirst", 100)
end

--========================================================
-- CONFIGURAÇÕES
--========================================================

local function AbrirConfig()
	LimparConteudo()

	local titulo = Instance.new("TextLabel")
	titulo.Size = UDim2.new(1, -20, 0, 30)
	titulo.Position = UDim2.new(0, 10, 0, 8)
	titulo.BackgroundTransparency = 1
	titulo.Text = "CONFIGURAÇÕES"
	titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
	titulo.Font = Enum.Font.GothamBold
	titulo.TextSize = 16
	titulo.TextXAlignment = Enum.TextXAlignment.Left
	titulo.Parent = Conteudo

	CriarToggle("MIRA ASSISTIDA", MIRA_ATIVADA, function(valor)
		MIRA_ATIVADA = valor
	end, 50)

	local parteTitulo = Instance.new("TextLabel")
	parteTitulo.Size = UDim2.new(1, -20, 0, 25)
	parteTitulo.Position = UDim2.new(0, 10, 0, 98)
	parteTitulo.BackgroundTransparency = 1
	parteTitulo.Text = "PARTE DA MIRA"
	parteTitulo.TextColor3 = Color3.fromRGB(180, 180, 180)
	parteTitulo.Font = Enum.Font.GothamBold
	parteTitulo.TextSize = 11
	parteTitulo.TextXAlignment = Enum.TextXAlignment.Left
	parteTitulo.Parent = Conteudo

	local ParteButton = Instance.new("TextButton")
	ParteButton.Size = UDim2.new(1, -20, 0, 35)
	ParteButton.Position = UDim2.new(0, 10, 0, 123)
	ParteButton.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	ParteButton.BorderSizePixel = 0
	ParteButton.Text = PARTE_MIRA
	ParteButton.TextColor3 = Color3.fromRGB(255, 255, 255)
	ParteButton.Font = Enum.Font.GothamBold
	ParteButton.TextSize = 12
	ParteButton.Parent = Conteudo

	local parteCanto = Instance.new("UICorner")
	parteCanto.CornerRadius = UDim.new(0, 5)
	parteCanto.Parent = ParteButton

	ParteButton.MouseButton1Click:Connect(function()
		if PARTE_MIRA == "Cabeça" then
			PARTE_MIRA = "Torso"
		elseif PARTE_MIRA == "Torso" then
			PARTE_MIRA = "HumanoidRootPart"
		else
			PARTE_MIRA = "Cabeça"
		end

		ParteButton.Text = PARTE_MIRA
	end)

	local fovTitulo = Instance.new("TextLabel")
	fovTitulo.Size = UDim2.new(1, -20, 0, 25)
	fovTitulo.Position = UDim2.new(0, 10, 0, 168)
	fovTitulo.BackgroundTransparency = 1
	fovTitulo.Text = "TAMANHO DO FOV: " .. tostring(TAMANHO_FOV)
	fovTitulo.TextColor3 = Color3.fromRGB(180, 180, 180)
	fovTitulo.Font = Enum.Font.GothamBold
	fovTitulo.TextSize = 11
	fovTitulo.TextXAlignment = Enum.TextXAlignment.Left
	fovTitulo.Parent = Conteudo

	local slider = Instance.new("TextButton")
	slider.Size = UDim2.new(1, -20, 0, 35)
	slider.Position = UDim2.new(0, 10, 0, 193)
	slider.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	slider.BorderSizePixel = 0
	slider.Text = "CLIQUE PARA AUMENTAR"
	slider.TextColor3 = Color3.fromRGB(220, 220, 220)
	slider.Font = Enum.Font.Gotham
	slider.TextSize = 11
	slider.Parent = Conteudo

	local sliderCanto = Instance.new("UICorner")
	sliderCanto.CornerRadius = UDim.new(0, 5)
	sliderCanto.Parent = slider

	slider.MouseButton1Click:Connect(function()
		TAMANHO_FOV += 25

		if TAMANHO_FOV > 300 then
			TAMANHO_FOV = 50
		end

		fovTitulo.Text = "TAMANHO DO FOV: " .. tostring(TAMANHO_FOV)

		FOVCircle.Size = UDim2.new(
			0,
			TAMANHO_FOV * 2,
			0,
			TAMANHO_FOV * 2
		)
	end)

	local spawn = Instance.new("TextButton")
	spawn.Size = UDim2.new(1, -20, 0, 35)
	spawn.Position = UDim2.new(0, 10, 0, 240)
	spawn.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	spawn.BorderSizePixel = 0
	spawn.Text = "MARCAR SPAWN"
	spawn.TextColor3 = Color3.fromRGB(255, 255, 255)
	spawn.Font = Enum.Font.GothamBold
	spawn.TextSize = 12
	spawn.Parent = Conteudo

	local spawnCanto = Instance.new("UICorner")
	spawnCanto.CornerRadius = UDim.new(0, 5)
	spawnCanto.Parent = spawn

	spawn.MouseButton1Click:Connect(MarcarSpawn)

	local voltar = Instance.new("TextButton")
	voltar.Size = UDim2.new(1, -20, 0, 35)
	voltar.Position = UDim2.new(0, 10, 0, 285)
	voltar.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	voltar.BorderSizePixel = 0
	voltar.Text = "VOLTAR AO SPAWN"
	voltar.TextColor3 = Color3.fromRGB(255, 255, 255)
	voltar.Font = Enum.Font.GothamBold
	voltar.TextSize = 12
	voltar.Parent = Conteudo

	local voltarCanto = Instance.new("UICorner")
	voltarCanto.CornerRadius = UDim.new(0, 5)
	voltarCanto.Parent = voltar

	voltar.MouseButton1Click:Connect(VoltarSpawn)

	local fome = Instance.new("TextButton")
	fome.Size = UDim2.new(0, 130, 0, 30)
	fome.Position = UDim2.new(1, -140, 0, 10)
	fome.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	fome.BorderSizePixel = 0
	fome.Text = "FOME 100"
	fome.TextColor3 = Color3.fromRGB(255, 255, 255)
	fome.Font = Enum.Font.GothamBold
	fome.TextSize = 11
	fome.Parent = Conteudo

	local fomeCanto = Instance.new("UICorner")
	fomeCanto.CornerRadius = UDim.new(0, 5)
	fomeCanto.Parent = fome

	fome.MouseButton1Click:Connect(PreencherFome)

	local sede = Instance.new("TextButton")
	sede.Size = UDim2.new(0, 130, 0, 30)
	sede.Position = UDim2.new(1, -140, 0, 45)
	sede.BackgroundColor3 = Color3.fromRGB(42, 42, 47)
	sede.BorderSizePixel = 0
	sede.Text = "SEDE 100"
	sede.TextColor3 = Color3.fromRGB(255, 255, 255)
	sede.Font = Enum.Font.GothamBold
	sede.TextSize = 11
	sede.Parent = Conteudo

	local sedeCanto = Instance.new("UICorner")
	sedeCanto.CornerRadius = UDim.new(0, 5)
	sedeCanto.Parent = sede

	sede.MouseButton1Click:Connect(PreencherSede)
end

--========================================================
-- NAVEGAÇÃO
--========================================================

BotaoHome.MouseButton1Click:Connect(AbrirHome)

BotaoTP.MouseButton1Click:Connect(AbrirTP)

BotaoTools.MouseButton1Click:Connect(AbrirTools)

BotaoConfig.MouseButton1Click:Connect(AbrirConfig)

--========================================================
-- MINIMIZAR
--========================================================

local painelMinimizado = false

Minimizar.MouseButton1Click:Connect(function()
	painelMinimizado = not painelMinimizado

	Menu.Visible = not painelMinimizado
	Conteudo.Visible = not painelMinimizado

	if painelMinimizado then
		Painel.Size = UDim2.new(0, 300, 0, 45)
	else
		Painel.Size = UDim2.new(0, 500, 0, 350)
	end
end)

--========================================================
-- FECHAR
--========================================================

Fechar.MouseButton1Click:Connect(function()
	ScreenGui.Enabled = false
end)

--========================================================
-- PERSONAGEM
--========================================================

Player.CharacterAdded:Connect(function(character)

	task.wait(1)

	if VIDA_INFINITA then
		AtivarVidaInfinita()
	end

	if spawnMarcado then
		local root = character:FindFirstChild("HumanoidRootPart")

		if root then
			root.CFrame = spawnMarcado
		end
	end
end)

--========================================================
-- PLAYER ADICIONADO
--========================================================

Players.PlayerAdded:Connect(function()
	task.wait(0.5)

	if ListaPlayers then
		AtualizarPlayers()
	end
end)

Players.PlayerRemoving:Connect(function()
	task.wait(0.5)

	if ListaPlayers then
		AtualizarPlayers()
	end
end)

--========================================================
-- DETECTAR TOOLS NOVAS
--========================================================

workspace.DescendantAdded:Connect(function(obj)
	if obj:IsA("Tool") then
		task.wait()

		if ListaTools then
			CarregarTools()
		end
	end
end)

ReplicatedStorage.DescendantAdded:Connect(function(obj)
	if obj:IsA("Tool") then
		task.wait()

		if ListaTools then
			CarregarTools()
		end
	end
end)

if Player.Backpack then
	Player.Backpack.ChildAdded:Connect(function(obj)
		if obj:IsA("Tool") then
			if ListaTools then
				task.wait()
				CarregarTools()
			end
		end
	end)
end

--========================================================
-- RENDER
--========================================================

RunService.RenderStepped:Connect(function()

	-- FOV
	FOVCircle.Visible = FOV_ATIVADO
	FOVCircle.Size = UDim2.new(
		0,
		TAMANHO_FOV * 2,
		0,
		TAMANHO_FOV * 2
	)

	-- VIDA INFINITA
	if VIDA_INFINITA then
		local character = Player.Character

		if character then
			local humanoid = character:FindFirstChildOfClass("Humanoid")

			if humanoid then
				if humanoid.Health < humanoid.MaxHealth then
					humanoid.Health = humanoid.MaxHealth
				end
			end
		end
	end

	-- ESP
	AtualizarESP()

	-- BARRA DE VIDA
	AtualizarBarraVida()

	-- MIRA ASSISTIDA
	if MIRA_ATIVADA and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then

		local alvo = PegarAlvo()

		if alvo then
			local direcao = alvo.Position - Camera.CFrame.Position

			if direcao.Magnitude > 0 then
				local novaCFrame = CFrame.lookAt(
					Camera.CFrame.Position,
					alvo.Position
				)

				Camera.CFrame = Camera.CFrame:Lerp(
					novaCFrame,
					SUAVIDADE_MIRA
				)
			end
		end
	end
end)

--========================================================
-- ABRIR PAINEL
-- INSERT = MOSTRAR/ESCONDER
--========================================================

UserInputService.InputBegan:Connect(function(input, processado)
	if processado then
		return
	end

	if input.KeyCode == Enum.KeyCode.Insert then
		ScreenGui.Enabled = not ScreenGui.Enabled
	end
end)

--========================================================
-- INICIALIZAÇÃO
--========================================================

AbrirHome()
AtualizarPlayers()
