--// ==========================================
--// ROYALITY HUB | V25 (ESTABILIDADE DE LOBBY E MEMÓRIA)
--// ==========================================

-- 1. Limpeza de memória de execuções anteriores (FOV, Menus e ESPs antigos)
pcall(function()
    if getgenv().RoyalityFOV then
        getgenv().RoyalityFOV:Remove()
        getgenv().RoyalityFOV = nil
    end
    for _, v in ipairs(game:GetService("CoreGui"):GetChildren()) do
        if v.Name:lower():find("rayfield") or v.Name:lower():find("royality") then
            v:Destroy()
        end
    end
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj.Name == "RoyESP" or obj.Name == "RoyAntenna" or obj.Name == "RoyLootESP" or obj.Name == "RoyLootText" then
            obj:Destroy()
        end
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
        Title = "Erro",
        Text = "Falha ao carregar interface.",
        Duration = 5
    })
    return
end

getgenv().DefaultConfig = {
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
    KillAuraEnabled = false,
    AntiLagEnabled = false,
    NoFogEnabled = false
}

getgenv().Config = {}
for k, v in pairs(getgenv().DefaultConfig) do
    getgenv().Config[k] = v
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LP = Players.LocalPlayer
local Cam = workspace.CurrentCamera

-- Criação segura do Círculo FOV salva em variável global para limpeza futura
local FOVCircle = nil
pcall(function()
    if Drawing then
        FOVCircle = Drawing.new("Circle")
        FOVCircle.Visible = Config.AimbotEnabled
        FOVCircle.Transparency = 0.6
        FOVCircle.Color = Color3.fromRGB(255, 50, 50)
        FOVCircle.Thickness = 1.8
        FOVCircle.NumSides = 32
        FOVCircle.Radius = Config.FOVRadius
        FOVCircle.Filled = false
        getgenv().RoyalityFOV = FOVCircle
    end
end)

local Window = Rayfield:CreateWindow({
    Name = "Royality Hub",
    LoadingTitle = "Carregando...",
    LoadingSubtitle = "Delta",
    ConfigurationSaving = { Enabled = false },
    KeySystem = false
})

local T1 = Window:CreateTab("Combate", 4483362458)
local T2 = Window:CreateTab("Movimento", 4483345998)
local T3 = Window:CreateTab("Visual", 4483345998)
local T4 = Window:CreateTab("Sistema", 4483345998)

--// Aba Combate
T1:CreateToggle({
    Name = "Aimbot",
    CurrentValue = Config.AimbotEnabled,
    Flag = "AE",
    Callback = function(v)
        Config.AimbotEnabled = v
        if FOVCircle then FOVCircle.Visible = v end
    end
})

T1:CreateToggle({
    Name = "WallCheck",
    CurrentValue = Config.WallCheck,
    Flag = "WC",
    Callback = function(v) Config.WallCheck = v end
})

T1:CreateSlider({
    Name = "Distancia Aimbot",
    Range = {50, 600},
    Increment = 25,
    Suffix = " studs",
    CurrentValue = Config.MaxAimDistance,
    Flag = "MAD",
    Callback = function(v) Config.MaxAimDistance = v end
})

T1:CreateSlider({
    Name = "Força Puxada",
    Range = {1, 20},
    Increment = 1,
    CurrentValue = Config.AimbotStrength,
    Flag = "ABS",
    Callback = function(v) Config.AimbotStrength = v end
})

T1:CreateDropdown({
    Name = "Parte Alvo",
    Options = {"Cabeça", "Peito"},
    CurrentOption = "Cabeça",
    Flag = "AP",
    Callback = function(o)
        local choice = type(o) == "table" and o[1] or o
        Config.AimPart = choice == "Cabeça" and "Head" or "UpperTorso"
    end
})

T1:CreateSlider({
    Name = "Raio FOV",
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
    Name = "Rapid Fire",
    CurrentValue = Config.RapidFireEnabled,
    Flag = "RF",
    Callback = function(v) Config.RapidFireEnabled = v end
})

T1:CreateToggle({
    Name = "Auto Trocar Arma",
    CurrentValue = Config.AutoSwitchEnabled,
    Flag = "AS",
    Callback = function(v) Config.AutoSwitchEnabled = v end
})

T1:CreateToggle({
    Name = "Auto Kit Médico",
    CurrentValue = Config.AutoMedKit,
    Flag = "AMK",
    Callback = function(v) Config.AutoMedKit = v end
})

T1:CreateToggle({
    Name = "Ignorar Time",
    CurrentValue = Config.TeamCheck,
    Flag = "TC",
    Callback = function(v) Config.TeamCheck = v end
})

T1:CreateSlider({
    Name = "Dano Multiplicador",
    Range = {1, 10},
    Increment = 0.5,
    Suffix = "x",
    CurrentValue = Config.DamageMultiplier,
    Flag = "DM",
    Callback = function(v) Config.DamageMultiplier = v end
})

--// Aba Movimento
T2:CreateToggle({
    Name = "Voo",
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
    Name = "Velocidade Voo",
    Range = {20, 200},
    Increment = 10,
    CurrentValue = Config.FlySpeed,
    Flag = "FS",
    Callback = function(v) Config.FlySpeed = v end
})

T2:CreateToggle({
    Name = "Kill Aura",
    CurrentValue = Config.KillAuraEnabled,
    Flag = "KA",
    Callback = function(v) Config.KillAuraEnabled = v end
})

--// Aba Visual
T3:CreateToggle({
    Name = "ESP Player",
    CurrentValue = Config.ESPEnabled,
    Flag = "ESP",
    Callback = function(v) Config.ESPEnabled = v end
})

T3:CreateToggle({
    Name = "Antena Player",
    CurrentValue = Config.ESPAntenna,
    Flag = "ANT",
    Callback = function(v) Config.ESPAntenna = v end
})

T3:CreateToggle({
    Name = "ESP Loot / Armas",
    CurrentValue = Config.LootESPEnabled,
    Flag = "LESP",
    Callback = function(v) Config.LootESPEnabled = v end
})

T3:CreateToggle({
    Name = "Remover Neblina",
    CurrentValue = Config.NoFogEnabled,
    Flag = "NFOG",
    Callback = function(v)
        Config.NoFogEnabled = v
        if v then
            Lighting.FogEnd = 100000
            Lighting.FogStart = 99999
        else
            Lighting.FogEnd = 1000
            Lighting.FogStart = 0
        end
    end
})

--// Aba Sistema
T4:CreateToggle({
    Name = "Anti-Lag",
    CurrentValue = Config.AntiLagEnabled,
    Flag = "ALAG",
    Callback = function(v)
        Config.AntiLagEnabled = v
        if v then
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Smoke") or obj:IsA("Fire") then
                    obj.Enabled = false
                end
            end
        end
    end
})

T4:CreateButton({
    Name = "Resetar Configurações",
    Callback = function()
        for k, v in pairs(DefaultConfig) do
            Config[k] = v
        end
        Rayfield:Notify({
            Title = "Reset",
            Content = "Configurações restauradas.",
            Duration = 3
        })
    end
})

--// Função WallCheck
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

--// Gerenciador de Conexão Única (Evita loops duplicados ao reexecutar)
if getgenv().RoyalityLoop then
    getgenv().RoyalityLoop:Disconnect()
    getgenv().RoyalityLoop = nil
end

local counter = 0
getgenv().RoyalityLoop = RunService.RenderStepped:Connect(function(dt)
    Cam = workspace.CurrentCamera
    if not Cam then return end

    if FOVCircle then
        pcall(function()
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end)
    end

    local character = LP.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return end
    local hum = character:FindFirstChildOfClass("Humanoid")

    counter = counter + 1
    local runHeavyTasks = (counter % 10 == 0)

    -- 1. ESP & Loot (Isolado em pcall próprio)
    if runHeavyTasks then
        pcall(function()
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    local pChar = p.Character
                    local pHum = pChar:FindFirstChildOfClass("Humanoid")
                    local head = pChar:FindFirstChild("Head")
                    local hl = pChar:FindFirstChild("RoyESP")
                    local antenna = pChar:FindFirstChild("RoyAntenna")
                    
                    if Config.ESPEnabled then
                        if not hl then
                            hl = Instance.new("Highlight")
                            hl.Name = "RoyESP"
                            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                            hl.FillTransparency = 0.5
                            hl.OutlineTransparency = 0
                            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                            hl.Adornee = pChar
                            hl.Parent = pChar
                        end
                        hl.Enabled = true
                        hl.FillColor = (pHum and pHum.Health > 0) and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(0, 100, 255)
                    elseif hl then
                        hl.Enabled = false
                    end

                    if Config.ESPAntenna and head and pHum and pHum.Health > 0 then
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

            -- ESP Loot usando BillboardGui leve (não estoura o limite de 31 Highlights do Roblox)
            if Config.LootESPEnabled then
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("Tool") or obj.Name:lower():find("gun") or obj.Name:lower():find("weapon") or obj.Name:lower():find("ammo") or obj.Name:lower():find("medkit") then
                        local part = obj:IsA("Model") and (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")) or (obj:IsA("BasePart") and obj)
                        if part and not part:FindFirstChild("RoyLootText") then
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
                            txt.Text = "[LOOT] " .. obj.Name
                            txt.TextColor3 = Color3.fromRGB(255, 255, 0)
                            txt.TextStrokeTransparency = 0
                            txt.TextSize = 12
                            txt.Font = Enum.Font.GothamBold
                            txt.Parent = bill
                        end
                    end
                end
            end
        end)
    end

    -- 2. Auto Kit Médico (Isolado)
    pcall(function()
        local backpack = LP:FindFirstChildOfClass("Backpack")
        if Config.AutoMedKit and hum and hum.Health > 0 and hum.Health < hum.MaxHealth and backpack then
            for _, tool in ipairs(backpack:GetChildren()) do
                if tool:IsA("Tool") and (tool.Name:lower():find("med") or tool.Name:lower():find("kit") or tool.Name:lower():find("heal") or tool.Name:lower():find("bandage")) then
                    hum:EquipTool(tool)
                    tool:Activate()
                    break
                end
            end
        end
    end)

    -- 3. Aimbot no Chão (Isolado)
    pcall(function()
        if Config.AimbotEnabled and hum and hum.FloorMaterial ~= Enum.Material.Air then
            local closestTarget = nil
            local shortestDist = Config.FOVRadius
            local screenCenter = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
            local myRoot = character:FindFirstChild("HumanoidRootPart")

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
        else
            if FOVCircle then FOVCircle.Color = Color3.fromRGB(255, 50, 50) end
        end
    end)

    -- 4. Auto-Switch, Voo e Kill Aura (Isolados)
    pcall(function()
        local backpack = LP:FindFirstChildOfClass("Backpack")
        if Config.AutoSwitchEnabled and backpack and hum then
            local currentTool = character:FindFirstChildOfClass("Tool")
            if currentTool then
                local ammoVal = currentTool:FindFirstChild("Ammo") or currentTool:FindFirstChild("Clip") or currentTool:FindFirstChild("Bullets")
                if ammoVal and ammoVal.Value <= 0 then
                    for _, tool in ipairs(backpack:GetChildren()) do
                        if tool:IsA("Tool") then
                            hum:EquipTool(tool)
                            break
                        end
                    end
                end
            end
        end

        local rp = character:FindFirstChild("HumanoidRootPart")
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

        if Config.KillAuraEnabled and rp and hum then
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    local pHum = p.Character:FindFirstChildOfClass("Humanoid")
                    local pRp = p.Character:FindFirstChild("HumanoidRootPart")
                    if pHum and pHum.Health > 0 and pRp then
                        if not Config.TeamCheck or p.Team ~= LP.Team then
                            if (rp.Position - pRp.Position).Magnitude <= 20 then
                                local tool = character:FindFirstChildOfClass("Tool")
                                if tool then tool:Activate() end
                            end
                        end
                    end
                end
            end
        end
    end)
end)

Rayfield:Notify({
    Title = "Royality Hub",
    Content = "Pronto e limpo para múltiplas partidas.",
    Duration = 4
})
