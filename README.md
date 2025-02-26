-- Criar tela principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PlayerESP"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Frame para botão ON/OFF
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.15, 0, 0.15, 0) -- Ajustado para largura de 15% e altura de 15%
Frame.Position = UDim2.new(0.4, 0, 0.85, 0)
Frame.BackgroundTransparency = 0.5
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0.45, 0, 1, 0) -- Largura reduzida
ToggleButton.BackgroundTransparency = 1
ToggleButton.TextScaled = true
ToggleButton.Text = "ON"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Parent = Frame

-- Botão para ligar/desligar Speed
local SpeedButton = Instance.new("TextButton")
SpeedButton.Size = UDim2.new(0.45, 0, 1, 0) -- Largura reduzida
SpeedButton.Position = UDim2.new(0.55, 0, 0, 0) -- Posição ao lado do botão ON/OFF
SpeedButton.BackgroundTransparency = 1
SpeedButton.TextScaled = true
SpeedButton.Text = "F" -- Inicialmente desativado
SpeedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedButton.Parent = Frame

-- Função para criar ESP no jogador
local function createESP(player)
    if player.Character and player.Character:FindFirstChild("Head") then
        -- Criar BillboardGui para nome do jogador
        local billboardGui = Instance.new("BillboardGui")
        billboardGui.Name = "ESP"
        billboardGui.Adornee = player.Character:FindFirstChild("Head")
        billboardGui.Size = UDim2.new(2, 0, 1, 0)
        billboardGui.AlwaysOnTop = true

        local nameLabel = Instance.new("TextLabel")
        nameLabel.Parent = billboardGui
        nameLabel.BackgroundTransparency = 1
        nameLabel.Size = UDim2.new(1, 0, 1, 0)
        nameLabel.Text = player.Name
        nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        nameLabel.TextScaled = true
        nameLabel.Font = Enum.Font.SourceSansBold

        billboardGui.Parent = player.Character:FindFirstChild("Head")

        -- Criar Highlight para linha em volta do jogador
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.Parent = player.Character
        highlight.FillTransparency = 1
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- Cor branca para melhor visibilidade
        highlight.OutlineTransparency = 0
    end
end

-- Adicionar ESP a todos os jogadores
local function addESPToPlayers()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            createESP(player)
        end
    end
end

-- Conectar eventos para novos jogadores
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        wait(1)
        createESP(player)
    end)
end)

-- Atualizar ESP de jogadores já presentes
addESPToPlayers()

-- Alternar visibilidade do ESP
local showESP = true
local function toggleESP()
    showESP = not showESP
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character then
            local esp = player.Character:FindFirstChild("Head"):FindFirstChild("ESP")
            if esp then esp.Enabled = showESP end

            local highlight = player.Character:FindFirstChild("ESP_Highlight")
            if highlight then highlight.Enabled = showESP end
        end
    end
    ToggleButton.Text = showESP and "ON" or "OFF"
end

ToggleButton.MouseButton1Click:Connect(toggleESP)

-- Ajuste de iluminação sem exagero
local function adjustLighting()
    local lighting = game.Lighting

    lighting.Brightness = 1.5
    lighting.Ambient = Color3.fromRGB(100, 100, 100)
    lighting.OutdoorAmbient = Color3.fromRGB(180, 180, 180)

    -- Criar luz no jogador para iluminar locais escuros
    local player = game.Players.LocalPlayer
    if player.Character then
        local head = player.Character:FindFirstChild("Head")
        if head then
            local light = head:FindFirstChild("ESP_Light")
            if not light then
                light = Instance.new("PointLight")
                light.Name = "ESP_Light"
                light.Parent = head
                light.Brightness = 2
                light.Range = 200 -- Aumentado alcance da luz para 200 unidades
                light.Color = Color3.fromRGB(255, 255, 255)
            end
        end
    end
end

-- Atualizar iluminação a cada 0.5s
game:GetService("RunService").RenderStepped:Connect(adjustLighting)

-- Ajustar velocidade do jogador
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- Função para ativar/desativar Speed
local speedEnabled = false
SpeedButton.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    if speedEnabled then
        character:WaitForChild("Humanoid").WalkSpeed = 24 -- Configura a velocidade para 24
        SpeedButton.Text = "S" -- Ativado
    else
        character:WaitForChild("Humanoid").WalkSpeed = 16 -- Velocidade padrão
        SpeedButton.Text = "F" -- Desativado
    end
end)
