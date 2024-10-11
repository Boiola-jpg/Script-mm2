local player = game.Players.LocalPlayer
local coins = workspace:WaitForChild("Coins")
local autoFarmEnabled = true

-- Função para coletar moedas
local function collectCoins()
    if autoFarmEnabled then
        for _, coin in pairs(coins:GetChildren()) do
            if coin:IsA("Part") and coin:FindFirstChild("TouchInterest") then
                firetouchinterest(player.Character.HumanoidRootPart, coin, 0)
                wait(0.1)
                firetouchinterest(player.Character.HumanoidRootPart, coin, 1)
            end
        end
    end
end

-- Função para criar ESP
local function createESP(player)
    local billboard = Instance.new("BillboardGui")
    billboard.Adornee = player.Character.Head
    billboard.Size = UDim2.new(0, 100, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.AlwaysOnTop = true

    local textLabel = Instance.new("TextLabel", billboard)
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = player.Name .. " - " .. player:GetRoleInMM2() -- Função fictícia para obter o papel
    textLabel.TextColor3 = Color3.new(1, 0, 0)
    textLabel.TextScaled = true

    billboard.Parent = player.Character.Head
end

-- Função para notificar quando o Sheriff for morto
local function notifySheriffKilled()
    game.StarterGui:SetCore("SendNotification", {
        Title = "Alerta!";
        Text = "O Murderer matou o Sheriff!";
        Duration = 5;
        Icon = "rbxassetid://12345678"; -- Substitua pelo ID do ícone desejado
        Callback = function()
            game:GetService("SoundService"):PlayLocalSound(Instance.new("Sound", {
                SoundId = "rbxassetid://12345678"; -- Substitua pelo ID do som desejado
                Volume = 1;
            }))
        end;
    })
end

-- Função para criar ESP na arma do Sheriff
local function createGunESP(gun)
    local billboard = Instance.new("BillboardGui")
    billboard.Adornee = gun
    billboard.Size = UDim2.new(0, 100, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.AlwaysOnTop = true

    local textLabel = Instance.new("TextLabel", billboard)
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = "Arma do Sheriff"
    textLabel.TextColor3 = Color3.new(0, 1, 0)
    textLabel.TextScaled = true

    billboard.Parent = gun
end

-- Função para exibir o nível do Sheriff e do Murderer
local function displayLevels()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player:GetRoleInMM2() == "Sheriff" then -- Função fictícia para verificar o papel
            print("Nível do Sheriff: " .. player.Level) -- Supondo que o nível do jogador seja acessível através de player.Level
        elseif player:GetRoleInMM2() == "Murderer" then -- Função fictícia para verificar o papel
            print("Nível do Murderer: " .. player.Level) -- Supondo que o nível do jogador seja acessível através de player.Level
        end
    end
end

-- Função para verificar a distância do Murderer
local function checkMurdererDistance()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player:GetRoleInMM2() == "Murderer" then -- Função fictícia para verificar o papel
            local distance = (player.Character.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
            if distance < 5 then
                autoFarmEnabled = false
                game.StarterGui:SetCore("SendNotification", {
                    Title = "Alerta!";
                    Text = "O Murderer está perto! Auto farm pausado.";
                    Duration = 5;
                    Icon = "rbxassetid://12345678"; -- Substitua pelo ID do ícone desejado
                    Callback = function()
                        game:GetService("SoundService"):PlayLocalSound(Instance.new("Sound", {
                            SoundId = "rbxassetid://12345678"; -- Substitua pelo ID do som desejado
                            Volume = 1;
                        }))
                    end;
                })
            else
                autoFarmEnabled = true
            end
        end
    end
end

-- Função para notificar o número de jogadores restantes
local function notifyPlayersRemaining()
    local playersRemaining = #game.Players:GetPlayers()
    game.StarterGui:SetCore("SendNotification", {
        Title = "Jogadores Restantes";
        Text = "Restam " .. playersRemaining .. " jogadores na partida.";
        Duration = 5;
        Icon = "rbxassetid://12345678"; -- Substitua pelo ID do ícone desejado
        Callback = function()
            game:GetService("SoundService"):PlayLocalSound(Instance.new("Sound", {
                SoundId = "rbxassetid://12345678"; -- Substitua pelo ID do som desejado
                Volume = 1;
            }))
        end;
    })
end

-- Função para fazer o jogador pular
local function jumpPlayer()
    player.Character.Humanoid.Jump = true
end

-- Função para detectar mensagens no chat
local function onChatted(message)
    if message == "jump Pedrinho" then
        jumpPlayer()
    end
end

-- Conectar a função de chat ao jogador local
player.Chatted:Connect(onChatted)

-- Loop principal para executar as funções
while wait(1) do
    collectCoins()
    checkMurdererDistance()
    notifyPlayersRemaining()
end

-- Adiciona ESP para todos os jogadores ao entrar no jogo
for _, player in pairs(game.Players:GetPlayers()) do
    createESP(player)
end

-- Adiciona ESP para novos jogadores que entrarem no jogo
game.Players.PlayerAdded:Connect(function(player)
    createESP(player)
end)

-- Notifica quando o Sheriff é morto
game.Players.PlayerRemoving:Connect(function(player)
    if player:GetRoleInMM2() == "Sheriff" then
        notifySheriffKilled()
    end
end)

-- Exibe os níveis do Sheriff e do Murderer
displayLevels()
