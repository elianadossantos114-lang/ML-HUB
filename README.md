print("[Server Hop com Filtro de Pets] Iniciado")

local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local placeId = game.PlaceId

-- Lista dos pets GOD e SECRETO
local desiredPets = {
	["Cocofanto Elefanto"] = true,
	["Girafa Celestre"] = true,
	["Gattatino Neonino"] = true,
	["Matteo"] = true,
	["Tralalero Tralala"] = true,
	["Los Crocodillitos"] = true,
	["Espresso Signora"] = true,
	["Odin Din Din Dun"] = true,
	["Statutino Libertino"] = true,
	["Tukanno Bananno"] = true,
	["Trenostruzzo Turbo 3000"] = true,
	["Trippi Troppi Troppa Trippa"] = true,
	["Ballerino Lololo"] = true,
	["Los Tungtungtungcitos"] = true,
	["Piccione Macchina"] = true,
	["Tigroligre Frutonni (Lucky Block)"] = true,
	["Orcalero Orcala"] = true,
	["La Vacca Saturno Saturnita"] = true,
	["Chimpanzini Spiderini"] = true,
	["Agarrini la Palini"] = true,
	["Los Tralaleritos"] = true,
	["Las Tralaleritas"] = true,
	["Las Vaquitas Saturnitas"] = true,
	["Graipuss Medussi"] = true,
	["Chicleteira Bicicleteira"] = true,
	["La Grande Combinasion"] = true,
	["Los Combinasionas"] = true,
	["Nuclearo Dinossauro"] = true,
	["Garama and Madundung"] = true,
	["Dragon Cannelloni"] = true,
}

-- GUI Principal
local gui = player:WaitForChild("PlayerGui"):FindFirstChild("ServerHopGui")
if gui then gui:Destroy() end

gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "ServerHopGui"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 50)
frame.Position = UDim2.new(0, 20, 0, 20)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.3
frame.BorderSizePixel = 0

local button = Instance.new("TextButton", frame)
button.Size = UDim2.new(1, -10, 1, -10)
button.Position = UDim2.new(0, 5, 0, 5)
button.BackgroundTransparency = 1
button.TextColor3 = Color3.fromRGB(200, 200, 200)
button.Font = Enum.Font.Gotham
button.TextSize = 18
button.Text = "Server Hop: OFF"
button.AutoButtonColor = false

-- Arrastar GUI
local dragging, dragInput, dragStart, startPos = false

local function updatePosition(input)
	local delta = input.Position - dragStart
	frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

frame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = frame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

frame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		updatePosition(input)
	end
end)

-- Verifica se há pet desejado
local function hasDesiredPet()
	local petsFolder = workspace:FindFirstChild("Pets") or workspace:FindFirstChild("PetModels") or workspace
	for _, pet in pairs(petsFolder:GetChildren()) do
		if pet:IsA("Model") or pet:IsA("Folder") or pet:IsA("BasePart") then
			local petName = pet.Name
			if desiredPets[petName] then
				return true, petName, pet
			end
		end
	end
	return false
end

-- Destaque do pet
local function highlightPet(pet)
	if not pet:IsA("Model") and not pet:IsA("BasePart") then return end
	local root = pet:IsA("Model") and pet:FindFirstChildWhichIsA("BasePart") or pet
	if root then
		local highlight = Instance.new("Highlight")
		highlight.FillColor = Color3.fromRGB(255, 0, 255)
		highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
		highlight.OutlineTransparency = 0
		highlight.FillTransparency = 0.5
		highlight.Adornee = pet
		highlight.Parent = pet
	end
end

-- Segunda GUI de escolha
local function showChoiceGui(petName)
	local confirmGui = Instance.new("ScreenGui", player.PlayerGui)
	confirmGui.Name = "ConfirmGui"

	local bg = Instance.new("Frame", confirmGui)
	bg.Size = UDim2.new(0, 260, 0, 100)
	bg.Position = UDim2.new(0.5, -130, 0.5, -50)
	bg.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
	bg.BackgroundTransparency = 0.2

	local label = Instance.new("TextLabel", bg)
	label.Size = UDim2.new(1, 0, 0.5, 0)
	label.Position = UDim2.new(0, 0, 0, 0)
	label.Text = "Pet encontrado: " .. petName
	label.TextColor3 = Color3.fromRGB(255, 255, 255)
	label.Font = Enum.Font.GothamBold
	label.TextSize = 16
	label.BackgroundTransparency = 1

	local btnSkip = Instance.new("TextButton", bg)
	btnSkip.Size = UDim2.new(0.5, -5, 0.4, 0)
	btnSkip.Position = UDim2.new(0, 5, 0.55, 0)
	btnSkip.Text = "Pular servidor"
	btnSkip.Font = Enum.Font.Gotham
	btnSkip.TextSize = 14
	btnSkip.BackgroundColor3 = Color3.fromRGB(100, 20, 20)
	btnSkip.TextColor3 = Color3.fromRGB(255, 255, 255)

	local btnFicar = Instance.new("TextButton", bg)
	btnFicar.Size = UDim2.new(0.5, -5, 0.4, 0)
	btnFicar.Position = UDim2.new(0.5, 5, 0.55, 0)
	btnFicar.Text = "Ficar aqui"
	btnFicar.Font = Enum.Font.Gotham
	btnFicar.TextSize = 14
	btnFicar.BackgroundColor3 = Color3.fromRGB(20, 100, 20)
	btnFicar.TextColor3 = Color3.fromRGB(255, 255, 255)

	btnSkip.MouseButton1Click:Connect(function()
		confirmGui:Destroy()
		TeleportService:Teleport(placeId, player)
	end)

	btnFicar.MouseButton1Click:Connect(function()
		confirmGui:Destroy()
	end)
end

-- Loop do Server Hop
local hopping = false
local function startServerHop()
	if hopping then return end
	hopping = true
	button.Text = "Server Hop: ON (Procurando...)"

	coroutine.wrap(function()
		while hopping do
			for i = 3, 1, -1 do
				if not hopping then return end
				button.Text = "Verificando em " .. i .. "s..."
				wait(1)
			end

			local success, petFound, petName, petModel = pcall(hasDesiredPet)
			if success and petFound then
				button.Text = "Pet encontrado: " .. petName
				hopping = false
				highlightPet(petModel)
				showChoiceGui(petName)
				break
			else
				button.Text = "Nada encontrado. Indo para outro..."
				wait(1)
				TeleportService:Teleport(placeId, player)
				wait(5)
			end
		end
	end)()
end

local function stopServerHop()
	hopping = false
	button.Text = "Server Hop: OFF"
end

button.MouseButton1Click:Connect(function()
	if hopping then
		stopServerHop()
	else
		startServerHop()
	end
end)
