    if obj:IsA("Terrain") or obj:IsA("Camera") or obj:IsA("Folder") then return nil end
    if isPlayerCharacter(obj) then return nil end

    local low = obj.Name:lower()
    if low == "lootspot" or low == "loot" or low == "spawner" then return nil end

    local targetPart = nil
    if obj:IsA("Tool") then
        targetPart = obj:FindFirstChild("Handle") or obj:FindFirstChildWhichIsA("BasePart")
    elseif obj:IsA("Model") or obj:IsA("BasePart") then
        local hasPrompt = obj:FindFirstChildWhichIsA("ProximityPrompt", true)
        local hasUI = obj:FindFirstChildWhichIsA("BillboardGui", true)
        local parentName = obj.Parent and obj.Parent.Name:lower() or ""
        local inContainer = parentName:find("loot") or parentName:find("drop")
            or parentName:find("item") or parentName:find("pickup")
            or parentName:find("weapon") or parentName:find("gun")

        if hasPrompt or hasUI or inContainer then
            if obj:IsA("BasePart") and obj.Parent and obj.Parent:IsA("Model") then
                if State.lootCatalog[obj.Parent] then return nil end
            end
            targetPart = obj:IsA("BasePart") and obj
                or (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart"))
            if targetPart and targetPart.Size.Magnitude >= 25 then targetPart = nil end
        end
    end

    if not targetPart or not targetPart.Parent then return nil end

    local realName = resolveRealItemName(obj)
    if not realName or ignoredNames[realName:lower()] then return nil end

    local n = realName:lower()
    local category = "Weapon"
    if n:find("med") or n:find("kit") or n:find("heal") or n:find("band")
        or n:find("cura") or n:find("vida") or n:find("syringe")
        or n:find("potion") or n:find("medico") or n:find("curativo") then
        category = "Med"
    elseif n:find("ammo") or n:find("bullet") or n:find("mag")
        or n:find("shell") or n:find("munic") or n:find("cart") then
        category = "Ammo"
    end

    return { category = category, part = targetPart, name = realName, source = obj }
end

local function categoryEnabled(category)
    if category == "Weapon" then return Config.LootWeapons end
    if category == "Ammo" then return Config.LootAmmo end
    if category == "Med" then return Config.LootMedKits end
    return false
end

local function makeLootLabel(obj, info)
    if State.loot[obj] then return end
    local part = info.part
    if not part or not part.Parent then return end

    local color
    if info.category == "Med" then
        color = Color3.fromRGB(0, 255, 110)
    elseif info.category == "Ammo" then
        color = Color3.fromRGB(0, 220, 255)
    else
        color = Color3.fromRGB(255, 165, 0)
    end

    local bill = Instance.new("BillboardGui")
    bill.Name = "RoyLootText"
    bill.Adornee = part
    bill.Size = UDim2.new(0, 150, 0, 28)
    bill.StudsOffset = Vector3.new(0, 1.8, 0)
    bill.MaxDistance = 400
    bill.AlwaysOnTop = true

    local txt = Instance.new("TextLabel")
    txt.Name = "RoyLootLabel"
    txt.Size = UDim2.new(1, 0, 1, 0)
    txt.BackgroundTransparency = 1
    txt.Text = info.name
    txt.TextColor3 = color
    txt.TextStrokeTransparency = 0
    txt.TextSize = 13
    txt.Font = Enum.Font.GothamBold
    txt.Parent = bill

    bill.Parent = part
    State.loot[obj] = bill
    table.insert(getgenv().RoyalityLootVisuals, bill)
end

local function dropLootLabel(obj)
    local bill = State.loot[obj]
    if bill then
        if bill.Parent then bill:Destroy() end
        State.loot[obj] = nil
    end
end

refreshLootLabels = function()
    if not Config.LootESPEnabled then
        for obj in pairs(State.loot) do dropLootLabel(obj) end
        return
    end

    for obj, info in pairs(State.lootCatalog) do
        local alive = obj and obj.Parent and info.part and info.part.Parent
        if not alive then
            dropLootLabel(obj)
            State.lootCatalog[obj] = nil
        elseif categoryEnabled(info.category) then
            makeLootLabel(obj, info)
        else
            dropLootLabel(obj)
        end
    end

    for obj in pairs(State.loot) do
        if not State.lootCatalog[obj] then dropLootLabel(obj) end
    end
end

local function registerLootCandidate(obj)
    if not obj or not obj.Parent then return end
    if State.lootCatalog[obj] then return end
    local info = classifyRealLoot(obj)
    if not info then return end
    State.lootCatalog[obj] = info
    if Config.LootESPEnabled and categoryEnabled(info.category) then
        makeLootLabel(obj, info)
    end
end

clearLootVisuals = function()
    for obj in pairs(State.loot) do dropLootLabel(obj) end
    for obj in pairs(State.lootCatalog) do State.lootCatalog[obj] = nil end
end

rebuildLootCache = function()
    local token = State.lootToken + 1
    State.lootToken = token

    task.spawn(function()
        local list = workspace:GetDescendants()
        for i, obj in ipairs(list) do
            if State.lootToken ~= token then return end
            if getgenv().RoyalityRunId ~= RUN_ID then return end
            if not Config.LootESPEnabled then return end
            if obj:IsA("Tool") or obj:IsA("Model") or obj:IsA("BasePart") then
                registerLootCandidate(obj)
            end
            if i % 400 == 0 then task.wait() end
        end
        if State.lootToken == token then refreshLootLabels() end
    end)
end

--// ---- conexões de loot --------------------------------------
getgenv().RoyalityLootConn = workspace.DescendantAdded:Connect(function(obj)
    if obj:IsA("Humanoid") and obj.Parent and obj.Parent:IsA("Model") then
        task.defer(function() registerBot(obj.Parent) end)
    end

    if not Config.LootESPEnabled then return end

    local isCandidate = obj:IsA("Tool") or obj:IsA("Model")
        or obj:IsA("BasePart") or obj:IsA("TextLabel")
    if not isCandidate then return end

    task.delay(0.2, function()
        if not Config.LootESPEnabled or not obj.Parent then return end
        local target = obj
        if obj:IsA("TextLabel") then
            target = obj:FindFirstAncestorWhichIsA("Model")
                or obj:FindFirstAncestorWhichIsA("BasePart")
        end
        if target then registerLootCandidate(target) end
    end)
end)
table.insert(getgenv().RoyalityConns, getgenv().RoyalityLootConn)

--// ---- efeitos reversíveis -----------------------------------
local function stopAllEffects(inst)
    for _, d in ipairs(inst:GetDescendants()) do
        if d:IsA("ParticleEmitter") or d:IsA("Trail")
            or d:IsA("Smoke") or d:IsA("Fire") or d:IsA("Sparkles") then
            if State.fx[d] == nil then State.fx[d] = d.Enabled end
            d.Enabled = false
        end
    end
end

local function restoreAllEffects()
    for obj, wasEnabled in pairs(State.fx) do
        if obj and obj.Parent then obj.Enabled = wasEnabled end
    end
    State.fx = {}
end

local function setAntiLag(on)
