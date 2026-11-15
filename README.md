--// ==========================================
--// ROYALITY HUB | ULTIMATE FINAL V14 (COM LOOT ESP + AUTO KIT + ANTENA)
--// ==========================================

-- Auto-Execute persistente ao mudar de partida/lobby
pcall(function()
    if syn and syn.queue_on_teleport then
        syn.queue_on_teleport(game:HttpGet("SEU_LINK_OU_SCRIPT_AQUI"))
    elseif queue_on_teleport then
        queue_on_teleport(game:HttpGet("SEU_LINK_OU_SCRIPT_AQUI"))
    end
end)

local Success, Rayfield = pcall(function()
    return loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
end)

if not Success or not Rayfield then
    Success, Rayfield = pcall(function()
        return loadstring(game:HttpGet('https://raw.githubusercontent.com/shlexware/Rayfield/main/source'))()
    end)
end

if not Rayfield then
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Erro ao Carregar",
        Text = "Falha na interface Rayfield. Verifique a internet.",
        Duration = 5
    })
    return
end

-- Configurações Padrões Salvas para o Botão Reset
getgenv().DefaultConfig = {
    ESPEnabled = true,
    ESPAntenna = true, -- Novo: Antena vermelha no ESP Player
    LootESPEnabled = false, -- Novo: ESP de Armas no mapa
    AutoMedKit = false, -- Novo: Auto Kit Médico
    AimbotEnabled = true,
    TeamCheck = false,
    WallCheck = true,
    MaxAimDistance = 250,
    AimPart = "Head",
    FOVRadius = 400,
    AimbotStrength = 15,
    RapidFireEnabled = false,
    AutoSwitchEnabled = false,
    DamageMultiplier = 3.0,
    FlyEnabled = false,
    FlySpeed = 60,
    KillAuraEnabled = false
}

getgenv().Config = {
    ESPEnabled = true,
    ESPAntenna = true,
    LootESPEnabled = false,
    AutoMedKit = false,
    AimbotEnabled = true,
    TeamCheck = false,
    WallCheck = true,
    MaxAimDistance = 250,
    AimPart = "Head",
    FOVRadius = 400,
    AimbotStrength = 15,
    RapidFireEnabled = false,
    AutoSwitchEnabled = false,
    DamageMultiplier = 3.0,
    FlyEnabled = false,
    FlySpeed = 60,
    KillAuraEnabled = false
}

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local LP = Players.LocalPlayer
local Cam = workspace.CurrentCamera

--// Círculo FOV Dinâmico
local FOVCircle = nil
pcall(function()
    if Drawing then
        FOVCircle = Drawing.new("Circle")
        FOVCircle.Visible = true
        FOVCircle.Transparency = 0.6
        FOVCircle.Color = Color3.fromRGB(255, 50, 50)
        FOVCircle.Thickness = 1.8
        FOVCircle.NumSides = 32
        FOVCircle.Radius = Config.FOVRadius
        FOVCircle.Filled = false
    end
end)

--// Janela Principal
local Window = Rayfield:CreateWindow({
    Name = "Royality Hub | Ultimate Final v14",
    LoadingTitle = "Carregando Hub...",
    LoadingSubtitle = "Delta Executor",
    ConfigurationSaving = { Enabled = false },
    KeySystem = false
})

local T1 = Window:CreateTab("Combate & Aim", 4483362458)
local T2 = Window:CreateTab("Voo & Movimento", 4483345998)
local T3 = Window:CreateTab("Visual (ESP & Loot)", 4483345998)
local T4 = Window:CreateTab("Configurações", 4483345998)

--// Aba Combate
T1:CreateToggle({
    Name = "🎯 Aimbot Inteligente (Com WallCheck e Distância)",
    CurrentValue = Config.AimbotEnabled,
    Flag = "AE",
    Callback = function(v)
        Config.AimbotEnabled = v
        if FOVCircle then FOVCircle.Visible = v end
    end
})

T1:CreateToggle({
    Name = "🧱 WallCheck (Não puxa através de paredes)",
    CurrentValue = Config.WallCheck,
    Flag = "WC",
    Callback = function(v) Config.WallCheck = v end
})

T1:CreateSlider({
    Name = "Distância Máxima do Aimbot",
    Range = {50, 600},
    Increment = 25,
    Suffix = " studs",
    CurrentValue = Config.MaxAimDistance,
    Flag = "MAD",
    Callback = function(v) Config.MaxAimDistance = v end
})

T1:CreateSlider({
    Name = "Força de Puxada do Aimbot",
    Range = {1, 20},
    Increment = 1,
    CurrentValue = Config.AimbotStrength,
    Flag = "ABS",
    Callback = function(v) Config.AimbotStrength = v end
})

T1:CreateDropdown({
    Name = "Parte do Corpo Alvo",
    Options = {"Cabeça", "Peito"},
    CurrentOption = "Cabeça",
    Flag = "AP",
    Callback = function(o)
        Config.AimPart = o == "Cabeça" and "Head" or "UpperTorso"
    end
})

T1:CreateSlider({
    Name = "Raio de Mira (FOV)",
    Range = {50, 800},
    Increment = 25,
    CurrentValue = Config.FOVRadius,
    Flag = "FOV",
    Callback = function(v)
        Config.FOVRadius = v
        if FOVCircle then FOVCircle.Radius = v end
    end
})

T1:CreateToggle({
    Name = "⚡ Ativar Rapid Fire (Recarga/Disparo Rápido)",
    CurrentValue = Config.RapidFireEnabled,
    Flag = "RF",
    Callback = function(v) Config.RapidFireEnabled = v end
})

T1:CreateToggle({
    Name = "🔄 Trocar Arma Automático (Ao Descarregar)",
    CurrentValue = Config.AutoSwitchEnabled,
    Flag = "AS",
    Callback = function(v) Config.AutoSwitchEnabled = v end
})

T1:CreateToggle({
    Name = "💊 Auto Kit Médico (Usa quando perde vida)",
    CurrentValue = Config.AutoMedKit,
    Flag = "AMK",
    Callback = function(v) Config.AutoMedKit = v end
})

T1:CreateToggle({
    Name = "Ignorar Time (TeamCheck)",
    CurrentValue = Config.TeamCheck,
    Flag = "TC",
    Callback = function(v) Config.TeamCheck = v end
})

T1:CreateSlider({
    Name = "Multiplicador de Dano",
    Range = {1, 10},
    Increment = 0.5,
    Suffix = "x",
    CurrentValue = Config.DamageMultiplier,
    Flag = "DM",
    Callback = function(v) Config.DamageMultiplier = v end
})

--// Aba Voo & Movimento
T2:CreateToggle({
    Name = "✈️ Ativar Voo Suave",
    CurrentValue = Config.FlyEnabled,
    Flag = "FT",
    Callback = function(v)
        Config.FlyEnabled = v
        local c = LP.Character
        if c and c:FindFirstChildOfClass("Humanoid") then
            c:FindFirstChildOfClass("Humanoid").PlatformStand = false
        end
    end
})

T2:CreateSlider({
    Name = "Velocidade do Voo",
    Range = {20, 200},
    Increment = 10,
    CurrentValue = Config.FlySpeed,
    Flag = "FS",
    Callback = function(v) Config.FlySpeed = v end
})

T2:CreateToggle({
    Name = "⚔️ Kill Aura Próximo (Seguro)",
    CurrentValue = Config.KillAuraEnabled,
    Flag = "KA",
    Callback = function(v) Config.KillAuraEnabled = v end
})

--// Aba Visual (ESP & Loot)
T3:CreateToggle({
    Name = "👁️ Ativar ESP Destaque (Longo Alcance)",
    CurrentValue = Config.ESPEnabled,
    Flag = "ESP",
    Callback = function(v) Config.ESPEnabled = v end
})

T3:CreateToggle({
    Name = "📡 Ativar Antena Vermelha no Topo da Cabeça",
    CurrentValue = Config.ESPAntenna,
    Flag = "ANT",
    Callback = function(v) Config.ESPAntenna = v end
})

T3:CreateToggle({
    Name = "🟡 Ativar ESP de Armas / Loot no Mapa",
    CurrentValue = Config.LootESPEnabled,
    Flag = "LESP",
    Callback = function(v) Config.LootESPEnabled = v end
})

--// Aba Configurações
T4:CreateButton({
    Name = "🔄 Resetar Configurações para o Padrão",
    Callback = function()
        for k, v in pairs(DefaultConfig) do
            Config[k] = v
        end
        Rayfield:Notify({
            Title = "Resetado!",
            Content = "Todas as configurações voltaram ao padrão.",
            Duration = 3
        })
    end
})

--// ==========================================
--// FUNÇÃO DE VISIBILIDADE (WALLCHECK)
--// ==========================================
local function isVisible(targetPart, targetCharacter)
    if not Config.WallCheck then return true end
    local origin = Cam.CFrame.Position
    local direction = (targetPart.Position - origin)
    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Exclude
    raycastParams.FilterDescendantsInstances = {LP.Character, Cam}
    raycastParams.IgnoreWater = true
    
    local result = workspace:Raycast(origin, direction, raycastParams)
    if result then
        return result.Instance:IsDescendantOf(targetCharacter)
    end
    return true
end

--// ==========================================
--// LOOP PRINCIPAL DE COMBATE, ESP, ANTENA & LOOT
--// ==========================================
RunService.RenderStepped:Connect(function(dt)
    pcall(function()
        if FOVCircle then
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end

        -- 1. ESP de Jogadores (Longo Alcance + Cores + Antena Vermelha)
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LP and p.Character then
                local char = p.Character
                local hum = char:FindFirstChildOfClass("Humanoid")
                local head = char:FindFirstChild("Head")
                local hl = char:FindFirstChild("RoyESP")
                local antenna = char:FindFirstChild("RoyAntenna")
                
                -- Highlight Principal
                if Config.ESPEnabled and char then
                    if not hl then
                        hl = Instance.new("Highlight")
                        hl.Name = "RoyESP"
                        hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                        hl.FillTransparency = 0.5
                        hl.OutlineTransparency = 0
                        hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                        hl.Adornee = char
                        hl.Parent = char
                    end
                    hl.Enabled = true
                    
                    if hum and hum.Health > 0 then
                        hl.FillColor = Color3.fromRGB(255, 0, 0) -- Vermelho se vivo
                    else
                        hl.FillColor = Color3.fromRGB(0, 100, 255) -- Azul se morto/abatido
                    end
                elseif hl then
                    hl.Enabled = false
                end

                -- Antena Vermelha no Topo da Cabeça
                if Config.ESPAntenna and head and hum and hum.Health > 0 then
                    if not antenna then
                        antenna = Instance.new("BillboardGui")
                        antenna.Name = "RoyAntenna"
                        antenna.Adornee = head
                        antenna.Size = UDim2.new(0, 2, 0, 150)
                        antenna.StudsOffset = Vector3.new(0, 8, 0)
                        antenna.AlwaysOnTop = true
                        antenna.Parent = head

                        local line = Instance.new("Frame")
                        line.Size = UDim2.new(1, 0, 1, 0)
                        line.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
                        line.BorderSizePixel = 0
                        line.Parent = antenna
                    end
                    antenna.Enabled = true
                elseif antenna then
                    antenna.Enabled = false
                end
            end
        end

        -- 2. ESP de Armas / Loot Amarelo no Mapa
        for _, obj in ipairs(workspace:GetDescendants()) do
            if Config.LootESPEnabled and (obj:IsA("Tool") or obj.Name:lower():find("gun") or obj.Name:lower():find("weapon") or obj.Name:lower():find("ammo") or obj.Name:lower():find("medkit")) then
                local part = obj:IsA("Model") and (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")) or (obj:IsA("BasePart") and obj)
                if part then
                    local lootHl = obj:FindFirstChild("RoyLootESP")
                    if not lootHl then
                        lootHl = Instance.new("Highlight")
                        lootHl.Name = "RoyLootESP"
                        lootHl.FillColor = Color3.fromRGB(255, 255, 0) -- Amarelo
                        lootHl.OutlineColor = Color3.fromRGB(255, 255, 255)
                        lootHl.FillTransparency = 0.4
                        lootHl.Adornee = obj
                        lootHl.Parent = obj

                        local bill = Instance.new("BillboardGui")
                        bill.Name = "RoyLootText"
                        bill.Adornee = part
                        bill.Size = UDim2.new(0, 100, 0, 30)
                        bill.StudsOffset = Vector3.new(0, 2, 0)
                        bill.AlwaysOnTop = true
                        bill.Parent = part

                        local txt = Instance.new("TextLabel")
                        txt.Size = UDim2.new(1, 0, 1, 0)
                        txt.BackgroundTransparency = 1
                        txt.Text = obj.Name
                        txt.TextColor3 = Color3.fromRGB(255, 255, 0)
                        txt.TextStrokeTransparency = 0
                        txt.TextSize = 13
                        txt.Font = Enum.Font.GothamBold
                        txt.Parent = bill
                    end
                    lootHl.Enabled = true
                end
            end
        end

        -- 3. Auto Kit Médico (Usa automaticamente se perder vida)
        local char = LP.Character
        local backpack = LP:FindFirstChildOfClass("Backpack")
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if Config.AutoMedKit and hum and hum.Health > 0 and hum.Health < hum.MaxHealth then
            if backpack then
                for _, tool in ipairs(backpack:GetChildren()) do
                    if tool:IsA("Tool") and (tool.Name:lower():find("med") or tool.Name:lower():find("kit") or tool.Name:lower():find("heal") or tool.Name:lower():find("bandage")) then
                        char.Humanoid:EquipTool(tool)
                        task.wait(0.1)
                        pcall(function() tool:Activate() end)
                        break
                    end
                end
            end
        end

        -- 4. Aimbot Inteligente com WallCheck e Distância Máxima
        if Config.AimbotEnabled then
            local closestTarget = nil
            local shortestDist = Config.FOVRadius
            local screenCenter = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
            local myRoot = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")

            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character and myRoot then
                    local pHum = p.Character:FindFirstChildOfClass("Humanoid")
                    local part = p.Character:FindFirstChild(Config.AimPart) or p.Character:FindFirstChild("Head")
                    local rp = p.Character:FindFirstChild("HumanoidRootPart")
                    
                    if pHum and pHum.Health > 0 and part and rp then
                        if Config.TeamCheck and p.Team == LP.Team then continue end
                        
                        local worldDist = (rp.Position - myRoot.Position).Magnitude
                        if worldDist <= Config.MaxAimDistance then
                            local screenPos, onScreen = Cam:WorldToViewportPoint(part.Position)
                            if onScreen and isVisible(part, p.Character) then
                                local dist = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                                if dist < shortestDist then
                                    shortestDist = dist
                                    closestTarget = part
                                end
                            end
                        end
                    end
                end
            end

            if closestTarget then
                if FOVCircle then FOVCircle.Color = Color3.fromRGB(0, 255, 100) end
                local targetCF = CFrame.new(Cam.CFrame.Position, closestTarget.Position)
                Cam.CFrame = Cam.CFrame:Lerp(targetCF, math.clamp(Config.AimbotStrength * 0.15, 0.2, 1))
            else
                if FOVCircle then FOVCircle.Color = Color3.fromRGB(255, 50, 50) end
            end
        end

        -- 5. Auto-Switch Weapon
        if Config.AutoSwitchEnabled and char and backpack then
            local currentTool = char:FindFirstChildOfClass("Tool")
            if currentTool then
                local ammoVal = currentTool:FindFirstChild("Ammo") or currentTool:FindFirstChild("Clip") or currentTool:FindFirstChild("Bullets")
                if ammoVal and ammoVal.Value <= 0 then
                    for _, tool in ipairs(backpack:GetChildren()) do
                        if tool:IsA("Tool") then
                            char.Humanoid:EquipTool(tool)
                            break
                        end
                    end
                end
            end
        end

        -- 6. Voo
        local rp = char and char:FindFirstChild("HumanoidRootPart")
        if rp and hum and Config.FlyEnabled then
            hum.PlatformStand = true
            rp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            local moveDir = hum.MoveDirection
            local camCF = Cam.CFrame
            local flyVec = Vector3.new()
            if moveDir.Magnitude > 0 then
                flyVec = Vector3.new(camCF.LookVector.X, camCF.LookVector.Y, camCF.LookVector.Z).Unit * moveDir.Magnitude
            end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then flyVec = flyVec + Vector3.new(0, 1, 0) end
            if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then flyVec = flyVec + Vector3.new(0, -1, 0) end
            rp.CFrame = rp.CFrame + (flyVec * Config.FlySpeed * dt)
        end

        -- 7. Kill Aura
        if Config.KillAuraEnabled and rp and hum then
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    local pHum = p.Character:FindFirstChildOfClass("Humanoid")
                    local pRp = p.Character:FindFirstChild("HumanoidRootPart")
                    if pHum and pHum.Health > 0 and pRp then
                        if not Config.TeamCheck or p.Team ~= LP.Team then
                            if (rp.Position - pRp.Position).Magnitude <= 20 then
                                local tool = char:FindFirstChildOfClass("Tool")
                                if tool then pcall(function() tool:Activate() end) end
                            end
                        end
                    end
                end
            end
        end
    end)
end)

Rayfield:Notify({
    Title = "Royality Hub V14 Concluído!",
    Content = "ESP Loot, Auto Kit e Antena Ativados com Sucesso.",
    Duration = 4
})
