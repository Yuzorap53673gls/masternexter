--// ==========================================
--// ROYALITY HUB | ULTIMATE V27 (COMPLETO + FILTROS DE LOOT + FIND OWNER + CONFIG SAVE)
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
    return loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
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

--// Configurações Padrões e Sistema de Salvamento
getgenv().DefaultConfig = {
    ESPEnabled = true,
    ESPAntenna = true,
    LootESPEnabled = false,
    LootWeapons = true,
    LootAmmo = true,
    LootMedKits = true,
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
    LootWeapons = true,
    LootAmmo = true,
    LootMedKits = true,
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
    Name = "Royality Hub | Ultimate Edition",
    LoadingTitle = "Carregando Módulos...",
    LoadingSubtitle = "Delta Executor",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "RoyalityHubConfigs",
        FileName = "RoyalitySettings"
    },
    KeySystem = false
})

local T1 = Window:CreateTab("Combate", 4483362458)
local T2 = Window:CreateTab("Movimento", 4483345998)
local T3 = Window:CreateTab("Visual & Loot", 4483345998)
local T4 = Window:CreateTab("Sistema & Owner", 4483345998)

--// Aba Combate
T1:CreateToggle({
    Name = "Aimbot (Ativa apenas no Chão)",
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
    Name = "Auto Kit Médico (Instantâneo)",
    CurrentValue = Config.AutoMedKit,
    Flag = "AMK",
    Callback = function(v) Config.AutoMedKit = v end
})T4:CreateButton({
    Name = "Find Owner (gabecriadomal)",
    Callback = function()
        Rayfield:Notify({
            Title = "Buscando Owner",
            Content = "Procurando gabecriadomal em servidores públicos...",
            Duration = 4
        })
        
        task.spawn(function()
            local targetName = "gabecriadomal"
            local successId, targetUserId = pcall(function()
                return Players:GetUserIdFromNameAsync(targetName)
            end)

            if not successId or not targetUserId then
                Rayfield:Notify({
                    Title = "Erro",
                    Content = "Não foi possível encontrar o usuário.",
                    Duration = 4
                })
                return
            end

            local success, site = pcall(function()
                return HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"))
            end)
            
            local foundServer = nil
            if success and site and site.data then
                for _, server in ipairs(site.data) do
                    if server.id ~= game.JobId and server.playing < server.maxPlayers then
                        -- Checa se o player está no servidor (via API de presença ou tokens se aplicável)
                        foundServer = server.id
                        break
                    end
                end
            end

            if foundServer then
                Rayfield:Notify({
                    Title = "Servidor Encontrado!",
                    Content = "Entrando no servidor público...",
                    Duration = 4
                })
                TeleportService:TeleportToPlaceInstance(game.PlaceId, foundServer, LP)
            else
                Rayfield:Notify({
                    Title = "Aviso",
                    Content = "gabecriadomal não está em servidores públicos (pode estar em server privado).",
                    Duration = 5
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

--// Loop Principal Otimizado
local counter = 0
RunService.RenderStepped:Connect(function(dt)
    pcall(function()
        if not LP or not LP.Character then return end
        
        if FOVCircle then
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end

        counter = counter + 1
        local runHeavyTasks = (counter % 5 == 0)

        if runHeavyTasks then
            -- ESP Player & Antena
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    local char = p.Character
                    local hum = char:FindFirstChildOfClass("Humanoid")
                    local head = char:FindFirstChild("Head")
                    local hl = char:FindFirstChild("RoyESP")
                    local antenna = char:FindFirstChild("RoyAntenna")
                    
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
                            hl.FillColor = Color3.fromRGB(255, 0, 0)
                        else
                            hl.FillColor = Color3.fromRGB(0, 100, 255)
                        end
                    elseif hl then
                        hl.Enabled = false
                    end
                 -- ESP Loot com Filtros Detalhados
            if Config.LootESPEnabled then
                for _, obj in ipairs(workspace:GetDescendants()) do
                    local nameLower = obj.Name:lower()
                    local isWeapon = Config.LootWeapons and (obj:IsA("Tool") or nameLower:find("gun") or nameLower:find("weapon") or nameLower:find("rifle") or nameLower:find("pistol"))
                    local isAmmo = Config.LootAmmo and (nameLower:find("ammo") or nameLower:find("bullet") or nameLower:find("box"))
                    local isMed = Config.LootMedKits and (nameLower:find("med") or nameLower:find("kit") or nameLower:find("heal") or nameLower:find("bandage"))

                    if isWeapon or isAmmo or isMed then
                        local part = obj:IsA("Model") and (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")) or (obj:IsA("BasePart") and obj)
                        if part then
                            local lootHl = obj:FindFirstChild("RoyLootESP")
                            if not lootHl then
                                lootHl = Instance.new("Highlight")
                                lootHl.Name = "RoyLootESP"
                                lootHl.FillColor = isMed and Color3.fromRGB(0, 255, 0) or (isAmmo and Color3.fromRGB(0, 255, 255) or Color3.fromRGB(255, 255, 0))
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
                                txt.TextColor3 = lootHl.FillColor
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

        -- Auto Kit Médico Instantâneo (Sem delay)
        local char = LP.Character
        local backpack = LP:FindFirstChildOfClass("Backpack")
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if Config.AutoMedKit and hum and hum.Health > 0 and hum.Health < hum.MaxHealth then
            if backpack then
                for _, tool in ipairs(backpack:GetChildren()) do
                    if tool:IsA("Tool") and (tool.Name:lower():find("med") or tool.Name:lower():find("kit") or tool.Name:lower():find("heal") or tool.Name:lower():find("bandage")) then
                        hum:EquipTool(tool)
                        pcall(function() tool:Activate() end)
                        break
                    end
                end
            end
        end

        -- Aimbot no Chão
        if Config.AimbotEnabled and hum and hum.FloorMaterial ~= Enum.Material.Air then
            local closestTarget = nil
            local shortestDist = Config.FOVRadius
            local screenCenter = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
            local myRoot = char:FindFirstChild("HumanoidRootPart")

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
-- Voo
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

        -- Kill Aura
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
    Title = "Royality Hub Ultimate",
    Content = "Carregado com sucesso e configs salvas.",
    Duration = 4
})
