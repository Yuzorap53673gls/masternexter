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
    for _, v in ipairs(game:GetService("CoreGui"):GetChildren()) do
        if v.Name:lower():find("rayfield") or v.Name:lower():find("royality") then
            v:Destroy()
        end
    end
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj.Name == "RoyESP" or obj.Name == "RoyAntenna" or obj.Name == "RoyNameTag" or obj.Name == "RoyLootText" or obj.Name == "RoyLootESP" then
            obj:Destroy()
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

--// Sistema de Deteccao Universal de Inimigos (Players Reais + Bots/NPCs)
local cachedCharacters = {}
local lastCharScan = 0

local function getAllEnemyCharacters()
    if (tick() - lastCharScan) < 0.4 and #cachedCharacters > 0 then
        return cachedCharacters
    end
    lastCharScan = tick()
    local list = {}
    local seen = {}

    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LP and p.Character then
            local hum = p.Character:FindFirstChildOfClass("Humanoid")
            if hum then
                seen[p.Character] = true
                table.insert(list, {
                    char = p.Character,
                    player = p,
                    name = p.DisplayName or p.Name
                })
            end
        end
    end

    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Humanoid") and obj.Parent and obj.Parent:IsA("Model") then
            local model = obj.Parent
            if model ~= LP.Character and not seen[model] then
                local head = model:FindFirstChild("Head")
                local rp = model:FindFirstChild("HumanoidRootPart") or model:FindFirstChild("Torso") or model:FindFirstChild("UpperTorso")
                if head and rp then
                    seen[model] = true
                    table.insert(list, {
                        char = model,
                        player = Players:GetPlayerFromCharacter(model),
                        name = model.Name
                    })
                end
            end
        end
    end

    cachedCharacters = list
    return list
end

local function getTargetAimPart(char)
    if Config.AimPart == "Head" then
        return char:FindFirstChild("Head") or char:FindFirstChild("HumanoidRootPart")
    else
        return char:FindFirstChild("UpperTorso") or char:FindFirstChild("Torso") or char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Head")
    end
end

--// Sistema de ESP de Loot Real (Ignora LootSpot vazio e le o nome real do item)
local trackedLoot = {}
local ignoredSpawners = {
    lootspot = true, loot = true, spawn = true, spawner = true,
    itemspawn = true, weaponspawn = true, dropzone = true,
    handle = true, part = true, meshpart = true, union = true,
    hitbox = true, root = true, primarypart = true, attachment = true
}

local function isAnyCharacterDescendant(inst)
    if LP.Character and inst:IsDescendantOf(LP.Character) then return true end
    for _, entry in ipairs(cachedCharacters) do
        if entry.char and inst:IsDescendantOf(entry.char) then
            return true
        end
    end
    return false
end

local function extractTrueItemName(obj)
    -- 1. Procura BillboardGui/SurfaceGui do proprio jogo dentro do item (ex: AK-47, MP5, Colete, Kit Medico, Municao)
    for _, desc in ipairs(obj:GetDescendants()) do
        if desc:IsA("TextLabel") and desc.Name ~= "RoyLootLabel" then
            local rawTxt = desc.Text:gsub("<[^>]+>", ""):gsub("^%s*(.-)%s*$", "%1")
            local low = rawTxt:lower()
            if rawTxt ~= "" and #rawTxt > 1 and not ignoredSpawners[low] and not low:find("^%[") and not low:find("^pegar") and not low:find("^e %-") then
                return rawTxt
            end
        end
    end

    -- 2. Procura atributos no objeto ou no modelo pai
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

    -- Kits Medicos / Cura -> Verde Neon
    if n:find("med") or n:find("kit") or n:find("heal") or n:find("band") or n:find("cura") or n:find("vida") or n:find("syringe") or n:find("potion") or n:find("first") or n:find("medico") then
        return "Med", Color3.fromRGB(0, 255, 110)
    end

    -- Municao -> Azul Ciano
    if n:find("munic") or n:find("ammo") or n:find("bullet") or n:find("mag") or n:find("shell") or n:find("cart") or n:find("bala") or n:find("9mm") or n:find("556") or n:find("762") then
        return "Ammo", Color3.fromRGB(0, 220, 255)
    end

    -- Coletes / Capacetes / Granadas / Equipamentos -> Amarelo Vibrante
    if n:find("colete") or n:find("armor") or n:find("vest") or n:find("shield") or n:find("escudo") or n:find("capacete") or n:find("helmet") or n:find("granada") or n:find("grenade") or n:find("mochila") or n:find("bag") then
        return "Armor", Color3.fromRGB(255, 240, 50)
    end

    -- Armas -> Laranja Dourado
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
    -- Ignora marcadores de spawner vazios (LootSpot, Loot, etc.)
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

    local bill = Instance.new("BillboardGui")
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
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Tool") or obj:IsA("Model") or obj:IsA("BasePart") then
            registerLootCandidate(obj)
        end
    end
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

        -- Aimbot Inteligente (Players Reais + Bots/NPCs + R6/R15)
        if Config.AimbotEnabled then
            local closestTarget = nil
            local shortestDist = Config.FOVRadius
            local screenCenter = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
            local myRoot = char and char:FindFirstChild("HumanoidRootPart")

            for _, entry in ipairs(enemies) do
                local eChar = entry.char
                local ePlayer = entry.player
                if eChar and eChar.Parent and myRoot then
                    local eHum = eChar:FindFirstChildOfClass("Humanoid")
                    local part = getTargetAimPart(eChar)
                    local rp = eChar:FindFirstChild("HumanoidRootPart") or part

                    if eHum and eHum.Health > 0 and part and rp then
                        if Config.TeamCheck and ePlayer and LP.Team and ePlayer.Team == LP.Team then
                            continue
                        end

                        local worldDist = (rp.Position - myRoot.Position).Magnitude
                        if worldDist <= Config.MaxAimDistance then
                            local screenPos, onScreen = Cam:WorldToViewportPoint(part.Position)
                            if onScreen and isVisible(part, eChar) then
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

        -- Auto-Switch Weapon
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

        -- Kill Aura (Players Reais + Bots/NPCs)
        if Config.KillAuraEnabled and rp and hum then
            for _, entry in ipairs(enemies) do
                local eChar = entry.char
                local ePlayer = entry.player
                if eChar and eChar.Parent then
                    local eHum = eChar:FindFirstChildOfClass("Humanoid")
                    local eRp = eChar:FindFirstChild("HumanoidRootPart") or eChar:FindFirstChild("Head")
                    if eHum and eHum.Health > 0 and eRp then
                        if not Config.TeamCheck or not ePlayer or not LP.Team or ePlayer.Team ~= LP.Team then
                            if (rp.Position - eRp.Position).Magnitude <= 20 then
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
