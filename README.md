game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "Royality Hub",
    Text = "Iniciando...",
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
    if getgenv().RoyalityLootConn then
        getgenv().RoyalityLootConn:Disconnect()
        getgenv().RoyalityLootConn = nil
    end
    if getgenv().RoyalityScanActive then
        getgenv().RoyalityScanActive = false
    end
    if getgenv().RoyalityVisuals then
        for _, inst in ipairs(getgenv().RoyalityVisuals) do
            pcall(function() if inst and inst.Parent then inst:Destroy() end end)
        end
    end
    getgenv().RoyalityVisuals = {}
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
        Title = "Erro",
        Text = "Falha ao baixar Rayfield.",
        Duration = 5
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
    LootArmor = true,
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
local T3 = Window:CreateTab("Visual e Loot", 4483345998)
local T4 = Window:CreateTab("Sistema e Owner", 4483345998)

T1:CreateToggle({
    Name = "Aimbot Inteligente",
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

local function trackVisual(inst)
    table.insert(getgenv().RoyalityVisuals, inst)
    return inst
end

local cachedNPCs = {}
getgenv().RoyalityScanActive = true

task.spawn(function()
    while getgenv().RoyalityScanActive do
        pcall(function()
            local found = {}
            local playerChars = {}
            for _, p in ipairs(Players:GetPlayers()) do
                if p.Character then
                    playerChars[p.Character] = true
                end
            end

            for _, child in ipairs(workspace:GetChildren()) do
                if child:IsA("Model") and not playerChars[child] then
                    local hum = child:FindFirstChildOfClass("Humanoid")
                    if hum and child:FindFirstChild("Head") then
                        table.insert(found, {
                            char = child,
                            player = nil,
                            name = child.Name
                        })
                    end
                elseif child:IsA("Folder") or child:IsA("Model") then
                    local n = child.Name:lower()
                    if n:find("bot") or n:find("npc") or n:find("enem") or n:find("player") or n:find("char") or n:find("ai") then
                        for _, sub in ipairs(child:GetChildren()) do
                            if sub:IsA("Model") and not playerChars[sub] then
                                local hum = sub:FindFirstChildOfClass("Humanoid")
                                if hum and sub:FindFirstChild("Head") then
                                    table.insert(found, {
                                        char = sub,
                                        player = nil,
                                        name = sub.Name
                                    })
                                end
                            end
                        end
                    end
                end
            end
            cachedNPCs = found
        end)
        task.wait(1.2)
    end
end)

local function getAllEnemyCharacters()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LP and p.Character then
            table.insert(list, {
                char = p.Character,
                player = p,
                name = p.DisplayName or p.Name
            })
        end
    end
    for _, npc in ipairs(cachedNPCs) do
        if npc.char and npc.char.Parent then
            table.insert(list, npc)
        end
    end
    return list
end

local trackedLoot = {}
local ignoredSpawners = {
    lootspot = true, loot = true, spawn = true, spawner = true,
    itemspawn = true, weaponspawn = true, dropzone = true,
    handle = true, part = true, meshpart = true, union = true,
    hitbox = true, root = true, primarypart = true, attachment = true
}

local function isAnyCharacterDescendant(inst)
    if LP.Character and inst:IsDescendantOf(LP.Character) then return true end
    for _, p in ipairs(Players:GetPlayers()) do
        if p.Character and inst:IsDescendantOf(p.Character) then return true end
    end
    for _, npc in ipairs(cachedNPCs) do
        if npc.char and inst:IsDescendantOf(npc.char) then return true end
    end
    return false
end

local function extractTrueItemName(obj)
    for _, desc in ipairs(obj:GetDescendants()) do
        if desc:IsA("TextLabel") and desc.Name ~= "RoyLootLabel" then
            local rawTxt = desc.Text:gsub("<[^>]+>", ""):gsub("^%s*(.-)%s*$", "%1")
            local low = rawTxt:lower()
            if rawTxt ~= "" and #rawTxt > 1 and not ignoredSpawners[low] and not low:find("^%[") and not low:find("^pegar") and not low:find("^e %-") then
                return rawTxt
            end
        end
    end

    local curr = obj
    while curr and curr ~= workspace do
        local attr = curr:GetAttribute("ItemName") or curr:GetAttribute("DisplayName") or curr:GetAttribute("WeaponName") or curr:GetAttribute("Title") or curr:GetAttribute("Name")
        if type(attr) == "string" and attr ~= "" and not ignoredSpawners[attr:lower()] then
            return attr
        end

        for _, child in ipairs(curr:GetChildren()) do
            if child:IsA("StringValue") and child.Value ~= "" and not ignoredSpawners[child.Value:lower()] then
                return child.Value
            end
        end

        local prompt = curr:FindFirstChildWhichIsA("ProximityPrompt", true)
        if prompt then
            if prompt.ObjectText ~= "" and not ignoredSpawners[prompt.ObjectText:lower()] then
                return prompt.ObjectText
            end
            if prompt.ActionText ~= "" and not ignoredSpawners[prompt.ActionText:lower()] then
                return prompt.ActionText
            end
        end

        if not ignoredSpawners[curr.Name:lower()] and curr ~= obj then
            return curr.Name
        end

        curr = curr.Parent
    end

    if not ignoredSpawners[obj.Name:lower()] then
        return obj.Name
    end

    return nil
end

local function classifyLootByRealName(realName)
    local n = realName:lower()

    if n:find("med") or n:find("kit") or n:find("heal") or n:find("band") or n:find("cura") or n:find("vida") or n:find("syringe") or n:find("potion") or n:find("first") or n:find("medico") then
        return "Med", Color3.fromRGB(0, 255, 110)
    end

    if n:find("munic") or n:find("ammo") or n:find("bullet") or n:find("mag") or n:find("shell") or n:find("cart") or n:find("bala") or n:find("9mm") or n:find("556") or n:find("762") then
        return "Ammo", Color3.fromRGB(0, 220, 255)
    end

    if n:find("colete") or n:find("armor") or n:find("vest") or n:find("shield") or n:find("escudo") or n:find("capacete") or n:find("helmet") or n:find("granada") or n:find("grenade") or n:find("mochila") or n:find("bag") then
        return "Armor", Color3.fromRGB(255, 240, 50)
    end

    return "Weapon", Color3.fromRGB(255, 145, 0)
end

local function clearLootVisuals()
    for obj, gui in pairs(trackedLoot) do
        if gui and gui.Parent then gui:Destroy() end
    end
    trackedLoot = {}
end

local function registerLootCandidate(obj)
    if not Config.LootESPEnabled then return end
    if not obj or not obj.Parent or trackedLoot[obj] then return end
    if isAnyCharacterDescendant(obj) then return end

    local lowName = obj.Name:lower()
    if lowName == "lootspot" or lowName == "loot" or lowName == "spawner" then
        return
    end

    local isCandidate = false
    local targetPart = nil

    if obj:IsA("Tool") then
        targetPart = obj:FindFirstChild("Handle") or obj:FindFirstChildWhichIsA("BasePart")
        isCandidate = (targetPart ~= nil)
    elseif obj:IsA("Model") or obj:IsA("BasePart") then
        if obj:IsA("BasePart") and obj.Parent and obj.Parent:IsA("Model") and trackedLoot[obj.Parent] then
            return
        end
        local parentLow = obj.Parent and obj.Parent.Name:lower() or ""
        local hasPrompt = obj:FindFirstChildWhichIsA("ProximityPrompt", true) ~= nil
        local hasGameUI = obj:FindFirstChildWhichIsA("BillboardGui", true) ~= nil
        local inLootFolder = parentLow:find("loot") or parentLow:find("drop") or parentLow:find("item") or parentLow:find("pickup") or parentLow:find("weapon")

        if hasPrompt or hasGameUI or inLootFolder then
            targetPart = obj:IsA("BasePart") and obj or (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart"))
            if targetPart and targetPart.Size.Magnitude < 25 then
                isCandidate = true
            end
        end
    end

    if not isCandidate or not targetPart then return end
    if targetPart:FindFirstChild("RoyLootText") then return end

    local realName = extractTrueItemName(obj)
    if not realName or ignoredSpawners[realName:lower()] then return end

    local category, color = classifyLootByRealName(realName)

    if (category == "Weapon" and not Config.LootWeapons)
    or (category == "Ammo" and not Config.LootAmmo)
    or (category == "Med" and not Config.LootMedKits)
    or (category == "Armor" and not Config.LootArmor) then
        return
    end

    local bill = trackVisual(Instance.new("BillboardGui"))
    bill.Name = "RoyLootText"
    bill.Adornee = targetPart
    bill.Size = UDim2.new(0, 160, 0, 26)
    bill.StudsOffset = Vector3.new(0, 2.3, 0)
    bill.MaxDistance = 400
    bill.AlwaysOnTop = true
    bill.Parent = targetPart

    local txt = Instance.new("TextLabel")
    txt.Name = "RoyLootLabel"
    txt.Size = UDim2.new(1, 0, 1, 0)
    txt.BackgroundTransparency = 1
    txt.Text = realName
    txt.TextColor3 = color
    txt.TextStrokeTransparency = 0
    txt.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    txt.TextSize = 13
    txt.Font = Enum.Font.GothamBold
    txt.Parent = bill

    trackedLoot[obj] = bill
end

local function rebuildLootCache()
    clearLootVisuals()
    if not Config.LootESPEnabled then return end
    task.spawn(function()
        local descendants = workspace:GetDescendants()
        for i, obj in ipairs(descendants) do
            if not Config.LootESPEnabled then break end
            if obj:IsA("Tool") or obj:IsA("Model") or obj:IsA("BasePart") then
                registerLootCandidate(obj)
            end
            if i % 400 == 0 then
                task.wait()
            end
        end
    end)
end

getgenv().RoyalityLootConn = workspace.DescendantAdded:Connect(function(obj)
    if Config.LootESPEnabled and (obj:IsA("Tool") or obj:IsA("Model") or obj:IsA("BasePart") or obj:IsA("TextLabel")) then
        task.delay(0.15, function()
            local target = obj:IsA("TextLabel") and obj:FindFirstAncestorWhichIsA("Model") or obj
            if target then
                registerLootCandidate(target)
            end
        end)
    end
end)

T3:CreateToggle({
    Name = "ESP Player e Bots",
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
    Name = "ESP Loot Real (Nomes e Cores)",
    CurrentValue = Config.LootESPEnabled,
    Flag = "LESP",
    Callback = function(v)
        Config.LootESPEnabled = v
        if v then
            rebuildLootCache()
        else
            clearLootVisuals()
        end
    end
})

T3:CreateToggle({
    Name = "Filtro: Armas (Laranja)",
    CurrentValue = Config.LootWeapons,
    Flag = "LWEP",
    Callback = function(v)
        Config.LootWeapons = v
        if Config.LootESPEnabled then rebuildLootCache() end
    end
})

T3:CreateToggle({
    Name = "Filtro: Municao (Azul)",
    CurrentValue = Config.LootAmmo,
    Flag = "LAMM",
    Callback = function(v)
        Config.LootAmmo = v
        if Config.LootESPEnabled then rebuildLootCache() end
    end
})

T3:CreateToggle({
    Name = "Filtro: Kits Medicos (Verde)",
    CurrentValue = Config.LootMedKits,
    Flag = "LMED",
    Callback = function(v)
        Config.LootMedKits = v
        if Config.LootESPEnabled then rebuildLootCache() end
    end
})

T3:CreateToggle({
    Name = "Filtro: Coletes e Granadas (Amarelo)",
    CurrentValue = Config.LootArmor,
    Flag = "LARM",
    Callback = function(v)
        Config.LootArmor = v
        if Config.LootESPEnabled then rebuildLootCache() end
    end
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
            task.spawn(function()
                for i, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Smoke") or obj:IsA("Fire") then
                        obj.Enabled = false
                    end
                    if i % 500 == 0 then task.wait() end
                end
            end)
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
        clearLootVisuals()
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

local function isMedTool(tool)
    if not tool or not tool:IsA("Tool") then return false end
    local n = tool.Name:lower()
    return n:find("med") or n:find("kit") or n:find("heal") or n:find("band") or n:find("cura") or n:find("vida") or n:find("syringe") or n:find("potion") or n:find("first") or n:find("medico")
end

local lastMedUse = 0
local counter = 0

getgenv().RoyalityLoop = RunService.RenderStepped:Connect(function(dt)
    pcall(function()
        Cam = workspace.CurrentCamera
        if FOVCircle then
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end

        counter = counter + 1
        local runPlayerESP = (counter % 8 == 0)
        local runLootCleanup = (counter % 90 == 0)

        local enemies = getAllEnemyCharacters()

        if runPlayerESP then
            for _, entry in ipairs(enemies) do
                local char = entry.char
                if char and char.Parent then
                    local hum = char:FindFirstChildOfClass("Humanoid")
                    local head = char:FindFirstChild("Head")
                    local hl = char:FindFirstChild("RoyESP")
                    local antenna = char:FindFirstChild("RoyAntenna")
                    local nameTag = head and head:FindFirstChild("RoyNameTag")

                    if Config.ESPEnabled then
                        if not hl then
                            hl = trackVisual(Instance.new("Highlight"))
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

                        if head then
                            if not nameTag then
                                nameTag = trackVisual(Instance.new("BillboardGui"))
                                nameTag.Name = "RoyNameTag"
                                nameTag.Adornee = head
                                nameTag.Size = UDim2.new(0, 140, 0, 24)
                                nameTag.StudsOffset = Vector3.new(0, 2.5, 0)
                                nameTag.AlwaysOnTop = true
                                nameTag.Parent = head

                                local lbl = Instance.new("TextLabel")
                                lbl.Name = "Lbl"
                                lbl.Size = UDim2.new(1, 0, 1, 0)
                                lbl.BackgroundTransparency = 1
                                lbl.TextStrokeTransparency = 0
                                lbl.TextSize = 12
                                lbl.Font = Enum.Font.GothamBold
                                lbl.Parent = nameTag
                            end
                            nameTag.Enabled = true
                            local lbl = nameTag:FindFirstChild("Lbl")
                            if lbl and hum then
                                local hp = math.max(0, math.floor(hum.Health))
                                lbl.Text = entry.name .. " [" .. hp .. "]"
                                lbl.TextColor3 = hp > 0 and Color3.fromRGB(255, 70, 70) or Color3.fromRGB(80, 160, 255)
                            end
                        end
                    else
                        if hl then hl.Enabled = false end
                        if nameTag then nameTag.Enabled = false end
                    end

                    if Config.ESPAntenna and head and hum and hum.Health > 0 then
                        if not antenna then
                            antenna = trackVisual(Instance.new("BillboardGui"))
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
        end

        if runLootCleanup and Config.LootESPEnabled then
            for obj, gui in pairs(trackedLoot) do
                if not obj or not obj.Parent or isAnyCharacterDescendant(obj) then
                    if gui and gui.Parent then gui:Destroy() end
                    trackedLoot[obj] = nil
                end
            end
        end

        local char = LP.Character
        local backpack = LP:FindFirstChildOfClass("Backpack")
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if Config.AutoMedKit and hum and hum.Health > 0 and hum.Health < hum.MaxHealth then
            if (tick() - lastMedUse) >= 0.25 then
                local equipped = char:FindFirstChildOfClass("Tool")
                local medTool = nil

                if isMedTool(equipped) then
                    medTool = equipped
                elseif backpack then
                    for _, tool in ipairs(backpack:GetChildren()) do
                        if isMedTool(tool) then
                            medTool = tool
                            break
                        end
                    end
                end

                if medTool then
                    lastMedUse = tick()
                    if medTool.Parent ~= char then
                        hum:EquipTool(medTool)
                    end
                    pcall(function() medTool:Activate() end)
                    pcall(function()
                        if mouse1click then mouse1click() end
                    end)
                    pcall(function()
                        local rem = medTool:FindFirstChildWhichIsA("RemoteEvent", true)
                        if rem then rem:FireServer() end
                    end)
                end
            end
        end

        -- Aimbot Inteligente com WallCheck e Distancia Maxima (Exato da versao_certa)
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

        if Config.AutoSwitchEnabled and char and backpack then
            local currentTool = char:FindFirstChildOfClass("Tool")
            if currentTool then
                local ammoVal = currentTool:FindFirstChild("Ammo") or currentTool:FindFirstChild("Clip") or currentTool:FindFirstChild("Bullets")
                if ammoVal and ammoVal.Value <= 0 then
                    for _, tool in ipairs(backpack:GetChildren()) do
                        if tool:IsA("Tool") and not isMedTool(tool) then
                            char.Humanoid:EquipTool(tool)
                            break
                        end
                    end
                end
            end
        end

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
    Content = "Carregado com sucesso.",
    Duration = 4
})
