--========================================================
-- PAINEL JF O MELHOR DA ATUALIDADE
--========================================================

local ToolsEncontradas = {}

local function CaminhoSeguro(obj)
	local ok, caminho = pcall(function()
		return obj:GetFullName()
	end)

	if ok then
		return caminho
	end

	return obj.Name
end

local function NormalizarNome(nome)
	nome = tostring(nome or ""):lower()
	nome = nome:gsub("[^%w]", "")
	return nome
end

local function DistanciaTexto(a,b)
	a = NormalizarNome(a)
	b = NormalizarNome(b)

	local la = #a
	local lb = #b

	if la == 0 then return lb end
	if lb == 0 then return la end

	local anterior = {}

	for j = 0, lb do
		anterior[j] = j
	end

	for i = 1, la do
		local atual = {[0] = i}
		local ca = a:sub(i,i)

		for j = 1, lb do
			local custo = (ca == b:sub(j,j)) and 0 or 1

			atual[j] = math.min(
				atual[j-1] + 1,
				anterior[j] + 1,
				anterior[j-1] + custo
			)
		end

		anterior = atual
	end

	return anterior[lb]
end

local function PontuacaoNome(nome, alvo)

	local n = NormalizarNome(nome)
	local a = NormalizarNome(alvo)

	if n == "" or a == "" then
		return -1
	end

	if n == a then
		return 1000
	end

	if n:find(a,1,true) then
		return 900 - math.abs(#n - #a)
	end

	if a:find(n,1,true) then
		return 850 - math.abs(#n - #a)
	end

	local distancia = DistanciaTexto(n,a)
	local tamanho = math.max(#n,#a)

	if tamanho <= 0 then
		return -1
	end

	local semelhanca = 1 - (distancia / tamanho)

	if semelhanca >= 0.55 then
		return math.floor(semelhanca * 700)
	end

	return -1
end

local function DescobrirOrigem(tool)

	if tool:IsDescendantOf(workspace) then
		return "Mapa / Workspace"

	elseif tool:IsDescendantOf(ReplicatedStorage) then
		return "ReplicatedStorage"

	elseif tool:IsDescendantOf(game:GetService("StarterPack")) then
		return "StarterPack"

	elseif LocalPlayer.Character
		and tool:IsDescendantOf(LocalPlayer.Character) then

		return "Seu personagem"

	elseif LocalPlayer:FindFirstChildOfClass("Backpack")
		and tool:IsDescendantOf(
			LocalPlayer:FindFirstChildOfClass("Backpack")
		) then

		return "Seu Backpack"
	end

	return CaminhoSeguro(tool)
end

--========================================================
-- ENCONTRAR TODAS AS TOOLS
--========================================================

local function ColetarTools()

	local resultado = {}
	local encontrados = {}

	for _,obj in ipairs(game:GetDescendants()) do

		if obj:IsA("Tool") and obj.Archivable then

			local caminho = CaminhoSeguro(obj)

			if not encontrados[caminho] then

				encontrados[caminho] = true

				table.insert(resultado,{
					Name = obj.Name,
					Location = DescobrirOrigem(obj),
					Path = caminho,
					Instance = obj,
					Score = 0
				})
			end
		end
	end

	return resultado
end

--========================================================
-- ENCONTRAR TOOL PELO NOME
--========================================================

local function LocalizarMelhorTool(nomeProcurado)

	local melhor = nil
	local melhorScore = -1

	for _,obj in ipairs(game:GetDescendants()) do

		if obj:IsA("Tool") and obj.Archivable then

			local score =
				PontuacaoNome(
					obj.Name,
					nomeProcurado
				)

			if score > melhorScore then

				melhorScore = score

				melhor = {
					Name = obj.Name,
					Location = DescobrirOrigem(obj),
					Path = CaminhoSeguro(obj),
					Instance = obj,
					Score = score
				}
			end
		end
	end

	return melhor,melhorScore
end

--========================================================
-- TOOLS PARECIDAS
--========================================================

local function LocalizarToolsParecidas(nomeProcurado)

	local resultado = {}
	local vistos = {}

	for _,obj in ipairs(game:GetDescendants()) do

		if obj:IsA("Tool") and obj.Archivable then

			local caminho = CaminhoSeguro(obj)

			if not vistos[caminho] then

				local score =
					PontuacaoNome(
						obj.Name,
						nomeProcurado
					)

				if score >= 450 then

					vistos[caminho] = true

					table.insert(resultado,{
						Name = obj.Name,
						Location = DescobrirOrigem(obj),
						Path = caminho,
						Instance = obj,
						Score = score
					})
				end
			end
		end
	end

	table.sort(resultado,function(a,b)

		if a.Score ~= b.Score then
			return a.Score > b.Score
		end

		return string.lower(a.Name)
			< string.lower(b.Name)
	end)

	return resultado
end

--========================================================
-- LIMPAR LISTA
--========================================================

local function LimparListaTools()

	for _,obj in ipairs(ListaTools:GetChildren()) do

		if obj:IsA("GuiObject")
			and obj ~= ListaLayout
			and obj ~= ListaPadding then

			obj:Destroy()
		end
	end
end

--========================================================
-- RESETAR BOTÃO
--========================================================

local function ResetarBotao(pegar)

	if not pegar or not pegar.Parent then
		return
	end

	pegar.Text = "PEGAR"
	pegar.BackgroundColor3 =
		Color3.fromRGB(150,0,0)
end

--========================================================
-- VERIFICAR SE JÁ POSSUI
--========================================================

local function JaPossuiTool(nome)

	local backpack =
		LocalPlayer:FindFirstChildOfClass("Backpack")

	if backpack then

		for _,obj in ipairs(backpack:GetChildren()) do

			if obj:IsA("Tool")
				and obj.Name == nome then

				return true
			end
		end
	end

	local character = LocalPlayer.Character

	if character then

		for _,obj in ipairs(character:GetChildren()) do

			if obj:IsA("Tool")
				and obj.Name == nome then

				return true
			end
		end
	end

	return false
end

--========================================================
-- PEGAR TOOL
--========================================================

local function ColocarNoInventario(info,botao)

	local tool =
		info and info.Instance

	-- Se a referência antiga não existir,
	-- procura novamente pelo nome.
	if (not tool or not tool.Parent)
		and info
		and info.Name then

		local localizada =
			LocalizarMelhorTool(info.Name)

		if localizada then
			tool = localizada.Instance
		end
	end

	if not tool or not tool.Parent then

		botao.Text = "NÃO LOCALIZADA"

		task.delay(1.8,function()
			ResetarBotao(botao)
		end)

		return false
	end

	local backpack =
		LocalPlayer:FindFirstChildOfClass("Backpack")

	if not backpack then

		botao.Text = "SEM BACKPACK"

		task.delay(1.5,function()
			ResetarBotao(botao)
		end)

		return false
	end

	if JaPossuiTool(tool.Name) then

		botao.Text = "JÁ POSSUI"

		task.delay(1.2,function()
			ResetarBotao(botao)
		end)

		return false
	end

	local clone

	local ok = pcall(function()
		clone = tool:Clone()
	end)

	if not ok or not clone then

		botao.Text = "NÃO PODE COPIAR"

		task.delay(1.5,function()
			ResetarBotao(botao)
		end)

		return false
	end

	clone.Parent = backpack

	task.defer(function()

		if clone.Parent == backpack then

			botao.Text = "NO INVENTÁRIO"

			botao.BackgroundColor3 =
				Color3.fromRGB(30,120,55)

		else

			botao.Text = "FALHOU"
		end
	end)

	task.delay(2,function()
		ResetarBotao(botao)
	end)

	return true
end

--========================================================
-- CRIAR ITEM
--========================================================

local function CriarItemTool(info,indice)

	local item = Instance.new("Frame")

	item.Name = "Tool_" .. indice
	item.LayoutOrder = indice
	item.Size = UDim2.new(1,-8,0,64)
	item.BackgroundColor3 =
		Color3.fromRGB(42,42,42)

	item.BorderSizePixel = 0
	item.Parent = ListaTools

	local ic = Instance.new("UICorner")
	ic.CornerRadius = UDim.new(0,5)
	ic.Parent = item

	local nome = Instance.new("TextLabel")

	nome.Size = UDim2.new(1,-140,0,25)
	nome.Position = UDim2.fromOffset(10,5)
	nome.BackgroundTransparency = 1
	nome.Text = info.Name
	nome.TextColor3 =
		Color3.fromRGB(235,235,235)

	nome.Font = Enum.Font.GothamBold
	nome.TextSize = 12
	nome.TextXAlignment =
		Enum.TextXAlignment.Left

	nome.TextTruncate =
		Enum.TextTruncate.AtEnd

	nome.Parent = item

	local localizacao = Instance.new("TextLabel")

	localizacao.Size =
		UDim2.new(1,-140,0,30)

	localizacao.Position =
		UDim2.fromOffset(10,29)

	localizacao.BackgroundTransparency = 1

	localizacao.Text =
		info.Location or "Origem desconhecida"

	localizacao.TextColor3 =
		Color3.fromRGB(145,145,145)

	localizacao.Font = Enum.Font.Gotham
	localizacao.TextSize = 9

	localizacao.TextXAlignment =
		Enum.TextXAlignment.Left

	localizacao.TextTruncate =
		Enum.TextTruncate.AtEnd

	localizacao.Parent = item

	local pegar = Instance.new("TextButton")

	pegar.Size =
		UDim2.fromOffset(105,34)

	pegar.Position =
		UDim2.new(1,-115,0.5,-17)

	pegar.BackgroundColor3 =
		Color3.fromRGB(150,0,0)

	pegar.BorderSizePixel = 0
	pegar.Text = "PEGAR"

	pegar.TextColor3 =
		Color3.fromRGB(255,255,255)

	pegar.Font = Enum.Font.GothamBold
	pegar.TextSize = 11

	pegar.Parent = item

	local pc = Instance.new("UICorner")
	pc.CornerRadius = UDim.new(0,5)
	pc.Parent = pegar

	pegar.MouseButton1Click:Connect(function()

		pegar.Text = "PEGANDO..."

		ColocarNoInventario(
			info,
			pegar
		)
	end)
end

--========================================================
-- MOSTRAR TOOLS
--========================================================

local function MostrarTools(lista)

	LimparListaTools()

	ToolsEncontradas = lista or {}

	if not lista or #lista == 0 then

		local vazio = Instance.new("TextLabel")

		vazio.Size =
			UDim2.new(1,-8,0,75)

		vazio.BackgroundTransparency = 1

		vazio.Text =
			"Nenhuma Tool encontrada. Digite o nome acima."

		vazio.TextColor3 =
			Color3.fromRGB(150,150,150)

		vazio.Font = Enum.Font.Gotham
		vazio.TextSize = 12
		vazio.TextWrapped = true

		vazio.Parent = ListaTools

		return
	end

	local alvo =
		NormalizarNome(
			CaixaBuscaTool.Text
		)

	table.sort(lista,function(a,b)

		local sa =
			PontuacaoNome(
				a.Name,
				alvo
			)

		local sb =
			PontuacaoNome(
				b.Name,
				alvo
			)

		if sa ~= sb then
			return sa > sb
		end

		return string.lower(a.Name)
			< string.lower(b.Name)
	end)

	for indice,info in ipairs(lista) do
		CriarItemTool(info,indice)
	end
end

--========================================================
-- ATUALIZAR TOOLS
--========================================================

local function CarregarTools()

	AtualizarTools.Text = "BUSCANDO..."

	local ok,lista =
		pcall(ColetarTools)

	if ok then

		MostrarTools(lista)

		InfoTools.Text =
			tostring(#lista)
			.." Tool(s) encontrada(s)"

	else

		MostrarTools({})

		InfoTools.Text =
			"A busca automática falhou."
	end

	AtualizarTools.Text = "ATUALIZAR"
end

--========================================================
-- PROCURAR PELO NOME
--========================================================

local function ProcurarPorNome()

	local alvo =
		CaixaBuscaTool.Text
		:gsub("^%s+","")
		:gsub("%s+$","")

	if alvo == "" then

		InfoTools.Text =
			"Digite o nome da Tool que deseja procurar."

		return
	end

	BuscarTool.Text = "BUSCANDO..."

	LimparListaTools()

	local parecidas =
		LocalizarToolsParecidas(alvo)

	if #parecidas > 0 then

		InfoTools.Text =
			tostring(#parecidas)
			.." Tool(s) encontrada(s) para: "
			..alvo

		for indice,info in ipairs(parecidas) do

			CriarItemTool(
				info,
				indice
			)
		end

	else

		local manual = {

			Name = alvo,

			Location =
				"Busca manual / não localizada ainda",

			Instance = nil,

			Path = "",

			Score = 0
		}

		CriarItemTool(
			manual,
			1
		)

		InfoTools.Text =
			"Nenhuma Tool parecida foi localizada."
	end

	BuscarTool.Text = "PROCURAR"
end

--========================================================
-- EVENTOS DE PESQUISA
--========================================================

BuscarTool.MouseButton1Click:Connect(
	ProcurarPorNome
)

CaixaBuscaTool:GetPropertyChangedSignal(
	"Text"
):Connect(function()

	local texto =
		CaixaBuscaTool.Text

	if texto == "" then

		MostrarTools(
			ToolsEncontradas
		)

		InfoTools.Text =
			"Digite o nome da Tool para filtrar a lista"

		return
	end

	local filtradas =
		LocalizarToolsParecidas(texto)

	if #filtradas > 0 then

		MostrarTools(filtradas)

		InfoTools.Text =
			tostring(#filtradas)
			.." Tool(s) encontrada(s) para: "
			..texto

	else

		local manual = {

			Name = texto,

			Location =
				"Busca manual / não localizada ainda",

			Instance = nil,

			Path = "",

			Score = 0
		}

		MostrarTools({
			manual
		})

		InfoTools.Text =
			"Nenhuma Tool encontrada."
	end
end)

CaixaBuscaTool.FocusLost:Connect(
	function(enterPressed)

		if enterPressed then
			ProcurarPorNome()
		end
	end
)

AtualizarTools.MouseButton1Click:Connect(
	CarregarTools
)

--========================================================
-- ATUALIZAÇÃO AUTOMÁTICA
--========================================================

game.DescendantAdded:Connect(function(obj)

	if obj:IsA("Tool")
		and TelaTools.Visible then

		task.delay(0.2,function()

			if not TelaTools.Visible then
				return
			end

			local termo =
				NormalizarNome(
					CaixaBuscaTool.Text
				)

			if termo ~= "" then
				ProcurarPorNome()
			else
				CarregarTools()
			end
		end)
	end
end)
