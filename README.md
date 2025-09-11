-- Script ESP Toggle (Highlight em jogadores ao apertar F)
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local localPlayer = Players.LocalPlayer

local highlightEnabled = false
local highlights = {}

-- Função para criar highlight vermelho em um jogador
local function addHighlightToPlayer(player)
	if player == localPlayer then return end -- Ignora o próprio jogador
	if highlights[player] then return end   -- Já tem highlight

	local character = player.Character
	if character then
		local highlight = Instance.new("Highlight")
		highlight.FillColor = Color3.fromRGB(255, 0, 0)
		highlight.OutlineColor = Color3.fromRGB(255, 50, 50)
		highlight.FillTransparency = 0.5
		highlight.OutlineTransparency = 0
		highlight.Parent = character
		highlights[player] = highlight
	end
end

-- Função para remover highlight
local function removeHighlightFromPlayer(player)
	if highlights[player] and highlights[player].Parent then
		highlights[player]:Destroy()
		highlights[player] = nil
	end
end

-- Função para adicionar ou remover highlights em todos os jogadores
local function setHighlights(enabled)
	for _, player in ipairs(Players:GetPlayers()) do
		if enabled then
			addHighlightToPlayer(player)
		else
			removeHighlightFromPlayer(player)
		end
	end
end

-- Atualiza highlight caso um personagem seja recriado (ex: respawn)
Players.PlayerAdded:Connect(function(player)
	player.CharacterAdded:Connect(function()
		if highlightEnabled then
			addHighlightToPlayer(player)
		end
	end)
end)

-- Remove highlight se o jogador sair
Players.PlayerRemoving:Connect(function(player)
	removeHighlightFromPlayer(player)
end)

-- Toggle ao apertar F
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.F then
		highlightEnabled = not highlightEnabled
		setHighlights(highlightEnabled)
	end
end)

-- Também observa respawn do próprio jogador
localPlayer.CharacterAdded:Connect(function()
	if highlightEnabled then
		setHighlights(true)
	end
end)
