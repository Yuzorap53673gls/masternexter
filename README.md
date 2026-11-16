--// ==========================================
--// ROYALITY HUB | ULTIMATE V21 (FIX DEFINITIVO: PERSISTÊNCIA APÓS MORTE)
--// ==========================================

pcall(function()
    if syn and syn.queue_on_teleport then
        syn.queue_on_teleport(game:HttpGet(script_url or ""))
    elseif queue_on_teleport then
        queue_on_teleport([[
            loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Rayfield/main/source"))()
        ]])
    end
end)

local Success, Rayfield = pcall(function()
    return loadstring(game:HttpGet('https://raw.githubusercontent.com/shlexware/Rayfield/main/source'))()
end)

if not Success or not Rayfield then
    Success, Rayfield = pcall(function()
        return loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
    end)
end

if not Rayfield then
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Erro",
        Text = "Falha ao carregar Rayfield.",
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
    KillAuraEnabled = false,
    AntiLagEnabled = false,
    NoFogEnabled = false
}

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local Lighting = game:GetService("Lighting")
local LP = Players.LocalPlayer
local Cam = workspace.CurrentCamera

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
        Config.AimPart = o == "Cabeça" and "Head" or "UpperTorso"
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
    Name = "Remover Neblina (No Fog)",
    CurrentValue = Config.NoFogEnabled,
    Flag = "NFOG",
    Callback = function(v)
        Config.NoFogEnabled = v
        if v then
            Lighting.FogEnd = 999999
            pcall(function() Lighting.Atmosphere:Destroy() end)
        end
    end
})

--// Aba Sistema
T4:CreateToggle({
    Name = "Anti-Lag Booster",
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
    Name = "Find Owner",
    Callback = function()
        Rayfield:Notify({
            Title = "Buscando",
            Content = "Procurando gabecriadomal...",
            Duration = 3
        })
        
        task.spawn(function()
            local success, site = pcall(function()
                return HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"))
            end)
            
            local found = false
            if success and site and site.data then
                for _, server in ipairs(site.data) do
                    if server.id ~= game.JobId then
                        found = true
                        TeleportService:TeleportToPlaceInstance(game.PlaceId, server.id, LP)
                        break
                    end
                end
            end

            if not found then
                Rayfield:Notify({
                    Title = "Owner",
                    Content = "Owner not found",
                    Duration = 4
                })
            end
        end)
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

--// Loop Principal Persistente com Reconexão Automática de Personagem
local counter = 0
RunService.RenderStepped:Connect(function(dt)
    pcall(function()
        local character = LP.Character
        if not character or not character:FindFirstChild("HumanoidRootPart") then return end
        
        if FOVCircle then
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end

        counter = counter + 1
        local runHeavyTasks = (counter % 5 == 0)

        if runHeavyTasks then
            -- ESP Player & Antena
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    local pChar = p.Character
                    local hum = pChar:FindFirstChildOfClass("Humanoid")
                    local head = pChar:FindFirstChild("Head")
                    local hl = pChar:FindFirstChild("RoyESP")
                    local antenna = pChar:FindFirstChild("RoyAntenna")
                    
                    if Config.ESPEnabled and pChar then
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
                        
                        if hum and hum.Health > 0 then
                            hl.FillColor = Color3.fromRGB(255, 0, 0)
                        else
                            hl.FillColor = Color3.fromRGB(0, 100, 255)
                        end
                    elseif hl then
                        hl.Enabled = false
                    end

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

            -- ESP Loot
            if Config.LootESPEnabled then
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("Tool") or obj.Name:lower():find("gun") or obj.Name:lower():find("weapon") or obj.Name:lower():find("ammo") or obj.Name:lower():find("medkit") then
                        local part = obj:IsA("Model") and (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")) or (obj:IsA("BasePart") and obj)
                        if part then
                            local lootHl = obj:FindFirstChild("RoyLootESP")
                            if not lootHl then
                                lootHl = Instance.new("Highlight")
                                lootHl.Name = "RoyLootESP"
                                lootHl.FillColor = Color3.fromRGB(255, 255, 0)
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
            end
        end

        -- Auto Kit Médico
        local backpack = LP:FindFirstChildOfClass("Backpack")
        local hum = character:FindFirstChildOfClass("Humanoid")
        if Config.AutoMedKit and hum and hum.Health > 0 and hum.Health < hum.MaxHealth then
            if backpack then
                for _, tool in ipairs(backpack:GetChildren()) do
                    if tool:IsA("Tool") and (tool.Name:lower():find("med") or tool.Name:lower():find("kit") or tool.Name:lower():find("heal") or tool.Name:lower():find("bandage")) then
                        hum:EquipTool(tool)
                        task.wait(0.1)
                        pcall(function() tool:Activate() end)
                        break
                    end
                end
            end
        end

        -- Aimbot (Ativa apenas no chão, ignorando avião/ar)
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

        -- Auto-Switch
        if Config.AutoSwitchEnabled and character and backpack then
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

        -- Voo
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

        -- Kill Aura
        if Config.KillAuraEnabled and rp and hum then
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    local pHum = p.Character:FindFirstChildOfClass("Humanoid")
                    local pRp = p.Character:FindFirstChild("HumanoidRootPart")
                    if pHum and pHum.Health > 0 and pRp then
                        if not Config.TeamCheck or p.Team ~= LP.Team then
                            if (rp.Position - pRp.Position).Magnitude <= 20 then
                                local tool = character:FindFirstChildOfClass("Tool")
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
    Title = "Royality Hub",
    Content = "Carregado com persistência total.",
    Duration = 4
})
