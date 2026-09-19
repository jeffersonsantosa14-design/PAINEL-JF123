--========================================================
-- JF PAINEL - O MELHOR DA ATUALIDADE
-- TEM TUDO QUE SE MAIS PRECISA PARA JOGO DE PVP E DE RP
--========================================================

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

--========================================================
-- CONFIG
--========================================================

local GUI_NAME = "JF_TOOLS_FINDER"

--========================================================
-- APAGA GUI ANTIGA
--========================================================

local antiga = PlayerGui:FindFirstChild(GUI_NAME)

if antiga then
	antiga:Destroy()
end

--========================================================
-- GUI
--========================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = GUI_NAME
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Gui.Parent = PlayerGui

--========================================================
-- PAINEL
--========================================================

local Painel = Instance.new("Frame")
Painel.Name = "Painel"
Painel.Size = UDim2.fromOffset(700, 520)
Painel.Position = UDim2.new(0.5, -350, 0.5, -260)
Painel.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
Painel.BorderSizePixel = 0
Painel.Parent = Gui

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 10)
Corner.Parent = Painel

--========================================================
-- TITULO
--========================================================

local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1, -70, 0, 50)
Titulo.Position = UDim2.fromOffset(15, 0)
Titulo.BackgroundTransparency = 1
Titulo.Text = "JF PAINEL  •  TOOLS"
Titulo.TextColor3 = Color3.new(1, 1, 1)
Titulo.TextSize = 20
Titulo.Font = Enum.Font.GothamBold
Titulo.TextXAlignment = Enum.TextXAlignment.Left
Titulo.Parent = Painel

--========================================================
-- FECHAR
--========================================================

local Fechar = Instance.new("TextButton")
Fechar.Size = UDim2.fromOffset(40, 40)
Fechar.Position = UDim2.new(1, -50, 0, 5)
Fechar.BackgroundColor3 = Color3.fromRGB(170, 35, 35)
Fechar.Text = "X"
Fechar.TextColor3 = Color3.new(1, 1, 1)
Fechar.TextSize = 17
Fechar.Font = Enum.Font.GothamBold
Fechar.Parent = Painel

local CornerFechar = Instance.new("UICorner")
CornerFechar.CornerRadius = UDim.new(0, 7)
CornerFechar.Parent = Fechar

Fechar.MouseButton1Click:Connect(function()
	Gui:Destroy()
end)

--========================================================
-- BARRA DE PESQUISA
--========================================================

local Pesquisa = Instance.new("TextBox")
Pesquisa.Name = "Pesquisa"
Pesquisa.Size = UDim2.new(1, -145, 0, 42)
Pesquisa.Position = UDim2.fromOffset(15, 58)
Pesquisa.BackgroundColor3 = Color3.fromRGB(38, 38, 38)
Pesquisa.BorderSizePixel = 0
Pesquisa.Text = ""
Pesquisa.PlaceholderText = "🔎 Pesquisar qualquer Tool..."
Pesquisa.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
Pesquisa.TextColor3 = Color3.new(1, 1, 1)
Pesquisa.TextSize = 14
Pesquisa.Font = Enum.Font.Gotham
Pesquisa.ClearTextOnFocus = false
Pesquisa.TextXAlignment = Enum.TextXAlignment.Left
Pesquisa.Parent = Painel

local Padding = Instance.new("UIPadding")
Padding.PaddingLeft = UDim.new(0, 13)
Padding.Parent = Pesquisa

local CornerPesquisa = Instance.new("UICorner")
CornerPesquisa.CornerRadius = UDim.new(0, 7)
CornerPesquisa.Parent = Pesquisa

--========================================================
-- ATUALIZAR
--========================================================

local Atualizar = Instance.new("TextButton")
Atualizar.Size = UDim2.fromOffset(115, 42)
Atualizar.Position = UDim2.new(1, -130, 0, 58)
Atualizar.BackgroundColor3 = Color3.fromRGB(45, 120, 65)
Atualizar.BorderSizePixel = 0
Atualizar.Text = "↻ ATUALIZAR"
Atualizar.TextColor3 = Color3.new(1, 1, 1)
Atualizar.TextSize = 12
Atualizar.Font = Enum.Font.GothamBold
Atualizar.Parent = Painel

local CornerAtualizar = Instance.new("UICorner")
CornerAtualizar.CornerRadius = UDim.new(0, 7)
CornerAtualizar.Parent = Atualizar

--========================================================
-- CONTADOR
--========================================================

local Contador = Instance.new("TextLabel")
Contador.Size = UDim2.new(1, -30, 0, 28)
Contador.Position = UDim2.fromOffset(15, 105)
Contador.BackgroundTransparency = 1
Contador.Text = "Procurando Tools..."
Contador.TextColor3 = Color3.fromRGB(180, 180, 180)
Contador.TextSize = 12
Contador.Font = Enum.Font.Gotham
Contador.TextXAlignment = Enum.TextXAlignment.Left
Contador.Parent = Painel

--========================================================
-- LISTA
--========================================================

local Lista = Instance.new("ScrollingFrame")
Lista.Name = "Lista"
Lista.Size = UDim2.new(1, -30, 1, -145)
Lista.Position = UDim2.fromOffset(15, 135)
Lista.BackgroundColor3 = Color3.fromRGB(16, 16, 16)
Lista.BorderSizePixel = 0
Lista.ScrollBarThickness = 7
Lista.ScrollBarImageColor3 = Color3.fromRGB(180, 40, 40)
Lista.CanvasSize = UDim2.new()
Lista.AutomaticCanvasSize = Enum.AutomaticSize.Y
Lista.Parent = Painel

local CornerLista = Instance.new("UICorner")
CornerLista.CornerRadius = UDim.new(0, 7)
CornerLista.Parent = Lista

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0, 6)
Layout.SortOrder = Enum.SortOrder.LayoutOrder
Layout.Parent = Lista

local PaddingLista = Instance.new("UIPadding")
PaddingLista.PaddingTop = UDim.new(0, 7)
PaddingLista.PaddingBottom = UDim.new(0, 7)
PaddingLista.PaddingLeft = UDim.new(0, 7)
PaddingLista.PaddingRight = UDim.new(0, 7)
PaddingLista.Parent = Lista

--========================================================
-- DRAG DO PAINEL
--========================================================

local arrastando = false
local inicioMouse
local inicioPos

Titulo.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1 then

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
-- NORMALIZAR TEXTO
--========================================================

local function Normalizar(texto)

	texto = tostring(texto or "")

	texto = string.lower(texto)

	return texto
end

--========================================================
-- CAMINHO COMPLETO
--========================================================

local function PegarCaminho(obj)

	local caminho = {}
	local atual = obj

	while atual and atual ~= game do

		table.insert(caminho, 1, atual.Name)

		atual = atual.Parent
	end

	return table.concat(caminho, " > ")
end

--========================================================
-- PROCURA TODAS AS TOOLS
--========================================================

local TodasTools = {}

local function ProcurarTools()

	local encontradas = {}

	-- GetDescendants procura recursivamente
	-- em tudo que o cliente consegue enxergar.

	for _, objeto in ipairs(game:GetDescendants()) do

		if objeto:IsA("Tool") then

			table.insert(encontradas, objeto)
		end
	end

	table.sort(encontradas, function(a, b)

		return Normalizar(a.Name) < Normalizar(b.Name)
	end)

	TodasTools = encontradas
end

--========================================================
-- VERIFICA SE POSSUI
--========================================================

local function JaPossui(nome)

	local Backpack = Player:FindFirstChildOfClass("Backpack")

	if Backpack and Backpack:FindFirstChild(nome) then
		return true
	end

	local Character = Player.Character

	if Character and Character:FindFirstChild(nome) then
		return true
	end

	return false
end

--========================================================
-- TENTAR PEGAR
--========================================================

local function PegarTool(tool)

	if not tool then
		return false
	end

	if not tool:IsA("Tool") then
		return false
	end

	if JaPossui(tool.Name) then
		return false
	end

	local Backpack = Player:FindFirstChildOfClass("Backpack")

	if not Backpack then
		return false
	end

	local sucesso, copia = pcall(function()

		return tool:Clone()
	end)

	if sucesso and copia then

		copia.Parent = Backpack

		return true
	end

	return false
end

--========================================================
-- LIMPAR LISTA
--========================================================

local function Limpar()

	for _, objeto in ipairs(Lista:GetChildren()) do

		if objeto:IsA("Frame") then
			objeto:Destroy()
		end
	end
end

--========================================================
-- CRIAR ITEM
--========================================================

local function CriarItem(tool, numero)

	local Item = Instance.new("Frame")
	Item.Size = UDim2.new(1, -5, 0, 72)
	Item.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	Item.BorderSizePixel = 0
	Item.LayoutOrder = numero
	Item.Parent = Lista

	local CornerItem = Instance.new("UICorner")
	CornerItem.CornerRadius = UDim.new(0, 7)
	CornerItem.Parent = Item

	-- NOME

	local Nome = Instance.new("TextLabel")
	Nome.Size = UDim2.new(1, -125, 0, 28)
	Nome.Position = UDim2.fromOffset(10, 5)
	Nome.BackgroundTransparency = 1
	Nome.Text = "🔧 " .. tool.Name
	Nome.TextColor3 = Color3.new(1, 1, 1)
	Nome.TextSize = 14
	Nome.Font = Enum.Font.GothamBold
	Nome.TextXAlignment = Enum.TextXAlignment.Left
	Nome.TextTruncate = Enum.TextTruncate.AtEnd
	Nome.Parent = Item

	-- CAMINHO

	local Caminho = Instance.new("TextLabel")
	Caminho.Size = UDim2.new(1, -125, 0, 30)
	Caminho.Position = UDim2.fromOffset(10, 33)
	Caminho.BackgroundTransparency = 1
	Caminho.Text = "📍 " .. PegarCaminho(tool)
	Caminho.TextColor3 = Color3.fromRGB(145, 145, 145)
	Caminho.TextSize = 10
	Caminho.Font = Enum.Font.Gotham
	Caminho.TextXAlignment = Enum.TextXAlignment.Left
	Caminho.TextTruncate = Enum.TextTruncate.AtEnd
	Caminho.Parent = Item

	-- BOTÃO

	local Botao = Instance.new("TextButton")
	Botao.Size = UDim2.fromOffset(95, 34)
	Botao.Position = UDim2.new(1, -105, 0.5, -17)
	Botao.BackgroundColor3 = Color3.fromRGB(170, 40, 40)
	Botao.BorderSizePixel = 0
	Botao.Text = "PEGAR"
	Botao.TextColor3 = Color3.new(1, 1, 1)
	Botao.TextSize = 12
	Botao.Font = Enum.Font.GothamBold
	Botao.Parent = Item

	local CornerBotao = Instance.new("UICorner")
	CornerBotao.CornerRadius = UDim.new(0, 7)
	CornerBotao.Parent = Botao

	Botao.MouseButton1Click:Connect(function()

		if not tool or not tool.Parent then

			Botao.Text = "SUMIU"

			return
		end

		if JaPossui(tool.Name) then

			Botao.Text = "JÁ POSSUI"

			task.delay(1, function()

				if Botao.Parent then
					Botao.Text = "PEGAR"
				end

			end)

			return
		end

		if PegarTool(tool) then

			Botao.Text = "PEGOU ✓"
			Botao.BackgroundColor3 = Color3.fromRGB(40, 130, 65)

		else

			Botao.Text = "NÃO FOI"

			task.delay(1, function()

				if Botao.Parent then
					Botao.Text = "PEGAR"
				end

			end)
		end
	end)
end

--========================================================
-- MOSTRAR
--========================================================

local function Mostrar()

	Limpar()

	local pesquisa = Normalizar(Pesquisa.Text)

	local resultados = {}

	for _, tool in ipairs(TodasTools) do

		if tool and tool.Parent then

			local nome = Normalizar(tool.Name)

			if pesquisa == "" or string.find(nome, pesquisa, 1, true) then

				table.insert(resultados, tool)
			end
		end
	end

	for numero, tool in ipairs(resultados) do

		CriarItem(tool, numero)
	end

	Contador.Text =
		"Tools encontradas: "
		.. #TodasTools
		.. "  •  Exibindo: "
		.. #resultados
end

--========================================================
-- ATUALIZAR
--========================================================

local function AtualizarLista()

	Contador.Text = "🔎 Procurando todas as Tools..."

	task.wait()

	ProcurarTools()

	Mostrar()
end

--========================================================
-- PESQUISA EM TEMPO REAL
--========================================================

Pesquisa:GetPropertyChangedSignal("Text"):Connect(function()

	Mostrar()
end)

--========================================================
-- BOTÃO ATUALIZAR
--========================================================

Atualizar.MouseButton1Click:Connect(function()

	Atualizar.Text = "🔄 BUSCANDO..."

	AtualizarLista()

	Atualizar.Text = "↻ ATUALIZAR"
end)

--========================================================
-- NOVAS TOOLS
--========================================================

game.DescendantAdded:Connect(function(objeto)

	if objeto:IsA("Tool") then

		task.wait(0.2)

		AtualizarLista()
	end
end)

--========================================================
-- PRIMEIRA EXECUÇÃO
--========================================================

AtualizarLista()
