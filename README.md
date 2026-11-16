game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "Royality Hub",
    Text = "Iniciando menu...",
    Duration = 3
})

pcall(function()
    if getgenv().RoyalityLoop then
        getgenv().RoyalityLoop:Disconnect()
        getgenv().RoyalityLoop = nil
    end
    if getgenv().RoyalityFOV then
        getgenv().RoyalityFOV:Remove()
        getgenv().RoyalityFOV = nil
    end
    for _, v in ipairs(game:GetService("CoreGui"):GetChildren()) do
        if v.Name:lower():find("rayfield") or v.Name:lower():find("royality") then
            v:Destroy()
        end
    end
end)

local Rayfield = nil
local urls = {
    "https://sirius.menu/rayfield",
    "https://raw.githubusercontent.com/shlexware/Rayfield/main/source",
    "https://raw.githubusercontent.com/UI-Interface/CustomFIeld/main/RayField.lua"
}

for _, url in ipairs(urls) do
    local ok, res = pcall(function()
        return loadstring(game:HttpGet(url))()
    end)
    if ok and res then
        Rayfield = res
        break
    end
end

if not Rayfield then
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Erro de Rede",
        Text = "Falha ao baixar o Rayfield.",
        Duration = 6
    })
    return
end

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

if not getgenv().Config then
    getgenv().Config = {}
    for k, v in pairs(getgenv().DefaultConfig) do
        getgenv().Config[k] = v
    end
end

local Config = getgenv().Config
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
    LoadingTitle = "Royality Hub",
    LoadingSubtitle = "Delta",
    ConfigurationSaving = { Enabled = false },
    KeySystem = false
})

local T1 = Window:CreateTab("Combate", 4483362458)
local T2 = Window:CreateTab("Movimento", 4483345998)
local T3 = Window:CreateTab("Visual & Loot", 4483345998)
local T4 = Window:CreateTab("Sistema & Owner", 4483345998)

T1:CreateToggle({
    Name = "Aimbot (Chao)",
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
    Name = "Forca Puxada",
    Range = {1, 20},
    Increment = 1,
    CurrentValue = Config.AimbotStrength,
    Flag = "ABS",
    Callback = function(v) Config.AimbotStrength = v end
})

T1:CreateDropdown({
    Name = "Parte Alvo",
    Options = {"Cabeca", "Peito"},
    CurrentOption = Config.AimPart == "Head" and "Cabeca" or "Peito",
    Flag = "AP",
    Callback = function(o)
        local choice = type(o) == "table" and o[1] or o
        Config.AimPart = choice == "Cabeca" and "Head" or "UpperTorso"
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
    Name = "Auto Kit Medico",
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

T2:CreateToggle({
    Name = "Voo Suave",
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
    Name = "ESP Loot Geral",
    CurrentValue = Config.LootESPEnabled,
    Flag = "LESP",
    Callback = function(v) Config.LootESPEnabled = v end
})

T3:CreateToggle({
    Name = "Filtro: Armas",
    CurrentValue = Config.LootWeapons,
    Flag = "LWEP",
    Callback = function(v) Config.LootWeapons = v end
})

T3:CreateToggle({
    Name = "Filtro: Municao",
    CurrentValue = Config.LootAmmo,
    Flag = "LAMM",
    Callback = function(v) Config.LootAmmo = v end
})

T3:CreateToggle({
    Name = "Filtro: Kits Medicos",
    CurrentValue = Config.LootMedKits,
    Flag = "LMED",
    Callback = function(v) Config.LootMedKits = v end
})

T3:CreateToggle({
    Name = "Remover Neblina",
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
    Name = "Find Owner (gabecriadomal)",
    Callback = function()
        Rayfield:Notify({
            Title = "Buscando",
            Content = "Procurando gabecriadomal...",
            Duration = 3
        })
        
        task.spawn(function()
            local targetName = "gabecriadomal"
            local successId, targetUserId = pcall(function()
                return Players:GetUserIdFromNameAsync(targetName)
            end)

            if not successId or not targetUserId then
                Rayfield:Notify({
                    Title = "Erro",
                    Content = "Usuario nao encontrado.",
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
                        foundServer = server.id
                        break
                    end
                end
            end

            if foundServer then
                Rayfield:Notify({
                    Title = "Encontrado",
                    Content = "Teleportando...",
                    Duration = 3
                })
                TeleportService:TeleportToPlaceInstance(game.PlaceId, foundServer, LP)
            else
                Rayfield:Notify({
                    Title = "Aviso",
                    Content = "Owner fora de server publico.",
                    Duration = 4
                })
            end
        end)
    end
})

T4:CreateButton({
    Name = "Resetar Configuracoes",
    Callback = function()
        for k, v in pairs(DefaultConfig) do
            Config[k] = v
        end
        Rayfield:Notify({
            Title = "Reset",
            Content = "Configuracoes restauradas.",
            Duration = 3
        })
    end
})

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

local counter = 0
getgenv().RoyalityLoop = RunService.RenderStepped:Connect(function(dt)
    pcall(function()
        if not LP or not LP.Character then return end
        Cam = workspace.CurrentCamera
        
        if FOVCircle then
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end

        counter = counter + 1
        local runHeavyTasks = (counter % 5 == 0)

        if runHeavyTasks then
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

            if Config.LootESPEnabled then
                for _, obj in ipairs(workspace:GetDescendants()) do
                    local nameLower = obj.Name:lower()
                    local isWeapon = Config.LootWeapons and (obj:IsA("Tool") or nameLower:find("gun") or nameLower:find("weapon") or nameLower:find("rifle") or nameLower:find("pistol"))
                    local isAmmo = Config.LootAmmo and (nameLower:find("ammo") or nameLower:find("bullet") or nameLower:find("box"))
                    local isMed = Config.LootMedKits and (nameLower:find("med") or nameLower:find("kit") or nameLower:find("heal") or nameLower:find("bandage"))

                    if isWeapon or isAmmo or isMed then
                        local part = obj:IsA("Model") and (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")) or (obj:IsA("BasePart") and obj)
                        if part and not part:FindFirstChild("RoyLootText") then
                            local color = isMed and Color3.fromRGB(0, 255, 0) or (isAmmo and Color3.fromRGB(0, 255, 255) or Color3.fromRGB(255, 255, 0))
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
                            txt.TextColor3 = color
                            txt.TextStrokeTransparency = 0
                            txt.TextSize = 13
                            txt.Font = Enum.Font.GothamBold
                            txt.Parent = bill
                        end
                    end
                end
            end
        end

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

        if Config.AutoSwitchEnabled and char and backpack then
            local currentTool = char:FindFirstChildOfClass("Tool")
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
        endlocal rp = char and char:FindFirstChild("HumanoidRootPart")
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
    Title = "Royality Hub",
    Content = "Carregado.",
    Duration = 4
})
