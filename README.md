pcall(function()
    if getgenv().RoyalityLoop then
        getgenv().RoyalityLoop:Disconnect()
        getgenv().RoyalityLoop = nil
    end
    pcall(function()
        game:GetService("RunService"):UnbindFromRenderStep("RoyalityAimLock")
    end)
    if getgenv().RoyalityFOV then
        getgenv().RoyalityFOV:Remove()
        getgenv().RoyalityFOV = nil
    end
    if getgenv().RoyalityLootConn then
        getgenv().RoyalityLootConn:Disconnect()
        getgenv().RoyalityLootConn = nil
    end
    if getgenv().RoyalityBotScanner then
        getgenv().RoyalityBotScanner = false
    end
    if getgenv().RoyalityVisuals then
        for _, inst in ipairs(getgenv().RoyalityVisuals) do
            pcall(function()
                if inst and inst.Parent then inst:Destroy() end
            end)
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

if not Rayfield then return end

getgenv().DefaultConfig = {
    ESPEnabled = true,
    ESPAntenna = true,
    LootESPEnabled = false,
    LootWeapons = true,
    LootAmmo = true,
    LootMedKits = true,
    AutoMedKit = false,
    AimbotEnabled = true,
    AutoFireEnabled = false,
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
local VirtualUser = game:GetService("VirtualUser")
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
    Name = "Atirar Sozinho (Auto Fire)",
    CurrentValue = Config.AutoFireEnabled,
    Flag = "AF",
    Callback = function(v) Config.AutoFireEnabled = v end
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

local function isTargetAliveAndStanding(targetChar, hum)
    if not targetChar or not targetChar.Parent or not hum then return false end
    if hum.Health <= 0 then return false end

    local state = hum:GetState()
    if state == Enum.HumanoidStateType.Dead then
        return false
    end

    if hum.PlatformStand then
        return false
    end

    if targetChar:GetAttribute("Dead") == true
        or targetChar:GetAttribute("Downed") == true
        or targetChar:GetAttribute("Knocked") == true
        or targetChar:GetAttribute("Eliminated") == true
        or targetChar:GetAttribute("IsDead") == true then
        return false
    end

    local parentName = targetChar.Parent and targetChar.Parent.Name:lower() or ""
    if parentName:find("dead") or parentName:find("corpse") or parentName:find("ragdoll") then
        return false
    end

    return true
end

local botModels = {}
getgenv().RoyalityBotScanner = true

local function registerBotModel(model)
    if not model or not model:IsA("Model") then return end
    if model == LP.Character then return end
    if Players:GetPlayerFromCharacter(model) then return end
    local hum = model:FindFirstChildOfClass("Humanoid")
    local head = model:FindFirstChild("Head")
    if hum and head then
        botModels[model] = true
    end
end

task.spawn(function()
    while getgenv().RoyalityBotScanner do
        pcall(function()
            local descendants = workspace:GetDescendants()
            for i, obj in ipairs(descendants) do
                if not getgenv().RoyalityBotScanner then break end
                if obj:IsA("Humanoid") and obj.Parent and obj.Parent:IsA("Model") then
                    registerBotModel(obj.Parent)
                end
                if i % 350 == 0 then
                    task.wait()
                end
            end
            for model, _ in pairs(botModels) do
                if not model or not model.Parent or not model:FindFirstChildOfClass("Humanoid") then
                    botModels[model] = nil
                end
            end
        end)
        task.wait(1.5)
    end
end)

local trackedLoot = {}
local ignoredSpawners = {
    lootspot = true, loot = true, spawn = true, spawner = true,
    itemspawn = true, weaponspawn = true, dropzone = true,
    handle = true, part = true, meshpart = true, union = true,
    hitbox = true, root = true, primarypart = true, attachment = true
}

local function isPlayerCharacter(inst)
    if LP.Character and inst:IsDescendantOf(LP.Character) then return true end
    for _, p in ipairs(Players:GetPlayers()) do
        if p.Character and inst:IsDescendantOf(p.Character) then
            return true
        end
    end
    for model, _ in pairs(botModels) do
        if model and model.Parent and inst:IsDescendantOf(model) then
            return true
        end
    end
    return false
end

local function resolveRealItemName(obj)
    for _, desc in ipairs(obj:GetDescendants()) do
        if desc:IsA("TextLabel") and desc.Name ~= "RoyLootLabel" then
            local rawTxt = desc.Text:gsub("<[^>]+>", ""):gsub("^%s*(.-)%s*$", "%1")
            local low = rawTxt:lower()
            if rawTxt ~= "" and #rawTxt > 1 and not ignoredSpawners[low] and not low:find("^%[") and not low:find("^pegar") then
                return rawTxt
            end
        end
    end

    local curr = obj
    while curr and curr ~= workspace do
        local attrName = curr:GetAttribute("ItemName") or curr:GetAttribute("DisplayName") or curr:GetAttribute("Name")
        if type(attrName) == "string" and attrName ~= "" and not ignoredSpawners[attrName:lower()] then
            return attrName
        end

        for _, child in ipairs(curr:GetChildren()) do
            if child:IsA("StringValue") and (child.Name:lower():find("name") or child.Name:lower():find("item")) and child.Value ~= "" then
                return child.Value
            end
        end

        local prompt = curr:FindFirstChildWhichIsA("ProximityPrompt", true)
        if prompt then
            if prompt.ObjectText and prompt.ObjectText ~= "" and not ignoredSpawners[prompt.ObjectText:lower()] then
                return prompt.ObjectText
            elseif prompt.ActionText and prompt.ActionText ~= "" and not ignoredSpawners[prompt.ActionText:lower()] then
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

local function classifyRealLoot(obj)
    if not obj or not obj.Parent or isPlayerCharacter(obj) then return nil, nil, nil end
    if obj:IsA("Terrain") or obj:IsA("Camera") or obj:IsA("Folder") then return nil, nil, nil end

    local low = obj.Name:lower()
    if low == "lootspot" or low == "loot" or low == "spawner" then return nil, nil, nil end

    local isRealItem = false
    local targetPart = nil

    if obj:IsA("Tool") then
        targetPart = obj:FindFirstChild("Handle") or obj:FindFirstChildWhichIsA("BasePart")
        if targetPart then isRealItem = true end
    elseif obj:IsA("Model") or obj:IsA("BasePart") then
        local hasPrompt = obj:FindFirstChildWhichIsA("ProximityPrompt", true)
        local hasUI = obj:FindFirstChildWhichIsA("BillboardGui", true)
        local parentFolder = obj.Parent and obj.Parent.Name:lower() or ""
        local inLootContainer = parentFolder:find("loot") or parentFolder:find("drop") or parentFolder:find("item") or parentFolder:find("pickup") or parentFolder:find("weapon") or parentFolder:find("gun")
        if hasPrompt or hasUI or inLootContainer then
            if obj:IsA("BasePart") and obj.Parent and obj.Parent:IsA("Model") and trackedLoot[obj.Parent] then
                return nil, nil, nil
            end
            targetPart = obj:IsA("BasePart") and obj or (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart"))
            if targetPart and targetPart.Size.Magnitude < 25 then
                isRealItem = true
            end
        end
    end

    if not isRealItem or not targetPart then return nil, nil, nil end

    local realName = resolveRealItemName(obj)
    if not realName or ignoredSpawners[realName:lower()] then return nil, nil, nil end

    local nameLower = realName:lower()
    if nameLower:find("med") or nameLower:find("kit") or nameLower:find("heal") or nameLower:find("band") or nameLower:find("cura") or nameLower:find("vida") or nameLower:find("syringe") or nameLower:find("potion") or nameLower:find("medico") then
        return "Med", targetPart, realName
    elseif nameLower:find("ammo") or nameLower:find("bullet") or nameLower:find("mag") or nameLower:find("shell") or nameLower:find("munic") or nameLower:find("cart") then
        return "Ammo", targetPart, realName
    else
        return "Weapon", targetPart, realName
    end
end

local function clearLootVisuals()
    for obj, gui in pairs(trackedLoot) do
        if gui and gui.Parent then gui:Destroy() end
    end
    trackedLoot = {}
end

local function registerLootCandidate(obj)
    if not Config.LootESPEnabled then return end
    if trackedLoot[obj] then return end
    local category, part, realName = classifyRealLoot(obj)
    if not category or not part then return end
    if part:FindFirstChild("RoyLootText") then return end

    if (category == "Weapon" and not Config.LootWeapons)
    or (category == "Ammo" and not Config.LootAmmo)
    or (category == "Med" and not Config.LootMedKits) then
        return
    end

    local color = category == "Med" and Color3.fromRGB(0, 255, 110) or (category == "Ammo" and Color3.fromRGB(0, 220, 255) or Color3.fromRGB(255, 165, 0))
    local bill = trackVisual(Instance.new("BillboardGui"))
    bill.Name = "RoyLootText"
    bill.Adornee = part
    bill.Size = UDim2.new(0, 140, 0, 28)
    bill.StudsOffset = Vector3.new(0, 1.8, 0)
    bill.MaxDistance = 400
    bill.AlwaysOnTop = true
    bill.Parent = part

    local txt = Instance.new("TextLabel")
    txt.Name = "RoyLootLabel"
    txt.Size = UDim2.new(1, 0, 1, 0)
    txt.BackgroundTransparency = 1
    txt.Text = realName
    txt.TextColor3 = color
    txt.TextStrokeTransparency = 0
    txt.TextSize = 13
    txt.Font = Enum.Font.GothamBold
    txt.Parent = bill

    trackedLoot[obj] = bill
end

local function rebuildLootCache()
    clearLootVisuals()
    if not Config.LootESPEnabled then return end
    task.spawn(function()
        for i, obj in ipairs(workspace:GetDescendants()) do
            if not Config.LootESPEnabled then break end
            if obj:IsA("Tool") or obj:IsA("Model") or obj:IsA("BasePart") then
                registerLootCandidate(obj)
            end
            if i % 400 == 0 then task.wait() end
        end
    end)
end

getgenv().RoyalityLootConn = workspace.DescendantAdded:Connect(function(obj)
    if obj:IsA("Humanoid") and obj.Parent and obj.Parent:IsA("Model") then
        task.defer(function()
            registerBotModel(obj.Parent)
        end)
    end

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
    Name = "ESP Loot Real",
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
    Name = "Filtro: Armas",
    CurrentValue = Config.LootWeapons,
    Flag = "LWEP",
    Callback = function(v)
        Config.LootWeapons = v
        if Config.LootESPEnabled then rebuildLootCache() end
    end
})

T3:CreateToggle({
    Name = "Filtro: Municao",
    CurrentValue = Config.LootAmmo,
    Flag = "LAMM",
    Callback = function(v)
        Config.LootAmmo = v
        if Config.LootESPEnabled then rebuildLootCache() end
    end
})

T3:CreateToggle({
    Name = "Filtro: Kits Medicos",
    CurrentValue = Config.LootMedKits,
    Flag = "LMED",
    Callback = function(v)
        Config.LootMedKits = v
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
        task.spawn(function()
            local successId, targetUserId = pcall(function()
                return Players:GetUserIdFromNameAsync("gabecriadomal")
            end)
            if not successId or not targetUserId then return end

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
                TeleportService:TeleportToPlaceInstance(game.PlaceId, foundServer, LP)
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
    end
})

local function isVisible(targetPart, targetCharacter)
    if not Config.WallCheck then return true end
    local origin = Cam.CFrame.Position
    local direction = (targetPart.Position - origin)
    local ignoreList = {LP.Character, Cam}

    for _ = 1, 4 do
        local raycastParams = RaycastParams.new()
        raycastParams.FilterType = Enum.RaycastFilterType.Exclude
        raycastParams.FilterDescendantsInstances = ignoreList
        raycastParams.IgnoreWater = true

        local result = workspace:Raycast(origin, direction, raycastParams)
        if not result then
            return true
        end

        local hit = result.Instance
        if hit:IsDescendantOf(targetCharacter) then
            return true
        end

        if hit.Transparency >= 0.8 or not hit.CanCollide or hit:FindFirstAncestorOfClass("Accessory") then
            table.insert(ignoreList, hit)
        else
            return false
        end
    end

    return false
end

local function isMedTool(tool)
    if not tool or not tool:IsA("Tool") then return false end
    local n = tool.Name:lower()
    return n:find("med") or n:find("kit") or n:find("heal") or n:find("band") or n:find("cura") or n:find("vida") or n:find("syringe") or n:find("potion") or n:find("first") or n:find("medico")
end

local function triggerAutoFire(char)
    local tool = char and char:FindFirstChildOfClass("Tool")
    if tool and not isMedTool(tool) then
        pcall(function() tool:Activate() end)
        pcall(function()
            if mouse1click then mouse1click() end
        end)
        pcall(function()
            VirtualUser:Button1Down(Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2), Cam.CFrame)
            task.delay(0.03, function()
                pcall(function()
                    VirtualUser:Button1Up(Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2), Cam.CFrame)
                end)
            end)
        end)
        pcall(function()
            for _, rem in ipairs(tool:GetDescendants()) do
                if rem:IsA("RemoteEvent") then
                    local rName = rem.Name:lower()
                    if rName:find("fire") or rName:find("shoot") or rName:find("attack") or rName:find("hit") or rName:find("atirar") then
                        rem:FireServer()
                    end
                end
            end
        end)
    end
end

local function applyESPToCharacter(char)
    if not char or not char.Parent then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    local head = char:FindFirstChild("Head")
    local hl = char:FindFirstChild("RoyESP")
    local antenna = char:FindFirstChild("RoyAntenna")
    local alive = isTargetAliveAndStanding(char, hum)

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

        if alive then
            hl.FillColor = Color3.fromRGB(255, 0, 0)
        else
            hl.FillColor = Color3.fromRGB(0, 100, 255)
        end
    elseif hl then
        hl.Enabled = false
    end

    if Config.ESPAntenna and head and alive then
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

local function getAimPartForChar(targetChar)
    if Config.AimPart == "Head" then
        return targetChar:FindFirstChild("Head") or targetChar:FindFirstChild("UpperTorso") or targetChar:FindFirstChild("Torso")
    else
        return targetChar:FindFirstChild("UpperTorso") or targetChar:FindFirstChild("Torso") or targetChar:FindFirstChild("HumanoidRootPart") or targetChar:FindFirstChild("Head")
    end
end

local lockedChar = nil
local lockedPlayer = nil

local lastMedUse = 0
local lastShotTime = 0
local counter = 0

getgenv().RoyalityLoop = RunService.RenderStepped:Connect(function(dt)
    pcall(function()
        Cam = workspace.CurrentCamera
        if FOVCircle then
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end

        counter = counter + 1
        local runPlayerESP = (counter % 6 == 0)
        local runLootCleanup = (counter % 90 == 0)

        if runPlayerESP then
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    applyESPToCharacter(p.Character)
                end
            end
            for botChar, _ in pairs(botModels) do
                if botChar and botChar.Parent and botChar ~= LP.Character then
                    applyESPToCharacter(botChar)
                end
            end
        end

        if runLootCleanup and Config.LootESPEnabled then
            for obj, gui in pairs(trackedLoot) do
                if not obj or not obj.Parent or isPlayerCharacter(obj) then
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

        local lockedTarget = nil
        local screenDistToTarget = 9999
        if Config.AimbotEnabled or Config.AutoFireEnabled then
            local closestTarget = nil
            local closestCharFound = nil
            local closestPlayerFound = nil
            local shortestDist = Config.FOVRadius
            local screenCenter = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
            local myRoot = char and char:FindFirstChild("HumanoidRootPart")

            local function evaluateTarget(targetChar, targetPlayer, isCurrentLock)
                if not targetChar or not targetChar.Parent or targetChar == char or not myRoot then return end
                local pHum = targetChar:FindFirstChildOfClass("Humanoid")
                local part = getAimPartForChar(targetChar)
                local rp = targetChar:FindFirstChild("HumanoidRootPart") or part

                if isTargetAliveAndStanding(targetChar, pHum) and part and rp then
                    if Config.TeamCheck and targetPlayer and LP.Team and targetPlayer.Team == LP.Team then return end

                    local worldDist = (rp.Position - myRoot.Position).Magnitude
                    if worldDist <= Config.MaxAimDistance then
                        local screenPos, onScreen = Cam:WorldToViewportPoint(part.Position)
                        if onScreen and isVisible(part, targetChar) then
                            local dist = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                            local effectiveDist = isCurrentLock and (dist * 0.65) or dist
                            if dist <= Config.FOVRadius and effectiveDist < shortestDist then
                                shortestDist = effectiveDist
                                screenDistToTarget = dist
                                closestTarget = part
                                closestCharFound = targetChar
                                closestPlayerFound = targetPlayer
                            end
                        end
                    end
                end
            end

            if lockedChar then
                evaluateTarget(lockedChar, lockedPlayer, true)
            end

            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character and p.Character ~= lockedChar then
                    evaluateTarget(p.Character, p, false)
                end
            end

            for botChar, _ in pairs(botModels) do
                if botChar and botChar.Parent and botChar ~= lockedChar then
                    evaluateTarget(botChar, nil, false)
                end
            end

            lockedChar = closestCharFound
            lockedPlayer = closestPlayerFound
            lockedTarget = closestTarget

            if Config.AimbotEnabled then
                if closestTarget then
                    if FOVCircle then FOVCircle.Color = Color3.fromRGB(0, 255, 100) end
                    local targetCF = CFrame.new(Cam.CFrame.Position, closestTarget.Position)
                    Cam.CFrame = Cam.CFrame:Lerp(targetCF, math.clamp(Config.AimbotStrength * 0.15, 0.25, 1))
                else
                    if FOVCircle then FOVCircle.Color = Color3.fromRGB(255, 50, 50) end
                end
            end
        else
            lockedChar = nil
            lockedPlayer = nil
        end

        if Config.AutoFireEnabled and lockedTarget and char and screenDistToTarget <= 75 then
            local fireDelay = Config.RapidFireEnabled and 0.04 or 0.12
            if (tick() - lastShotTime) >= fireDelay then
                lastShotTime = tick()
                triggerAutoFire(char)
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
                    if isTargetAliveAndStanding(p.Character, pHum) and pRp then
                        if not Config.TeamCheck or p.Team ~= LP.Team then
                            if (rp.Position - pRp.Position).Magnitude <= 20 then
                                local tool = char:FindFirstChildOfClass("Tool")
                                if tool then pcall(function() tool:Activate() end) end
                            end
                        end
                    end
                end
            end
            for botChar, _ in pairs(botModels) do
                if botChar and botChar.Parent and botChar ~= LP.Character then
                    local pHum = botChar:FindFirstChildOfClass("Humanoid")
                    local pRp = botChar:FindFirstChild("HumanoidRootPart") or botChar:FindFirstChild("Head")
                    if isTargetAliveAndStanding(botChar, pHum) and pRp then
                        if (rp.Position - pRp.Position).Magnitude <= 20 then
                            local tool = char:FindFirstChildOfClass("Tool")
                            if tool then pcall(function() tool:Activate() end) end
                        end
                    end
                end
            end
        end
    end)
end)
