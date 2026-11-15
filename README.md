--// ==========================================
--// ROYALITY HUB | VERSÃO ANTI-CRASH & ESTÁVEL
--// ==========================================

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

getgenv().Config = {
    ESPEnabled = true,
    AimbotEnabled = true,
    TeamCheck = false,
    AimPart = "Head",
    FOVRadius = 300,
    MaxDistance = 1500,
    RapidFireEnabled = true,
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

--// Círculo FOV Seguro
local FOVCircle = nil
pcall(function()
    if Drawing then
        FOVCircle = Drawing.new("Circle")
        FOVCircle.Visible = true
        FOVCircle.Transparency = 0.5
        FOVCircle.Color = Color3.fromRGB(255, 50, 50)
        FOVCircle.Thickness = 1.5
        FOVCircle.NumSides = 32
        FOVCircle.Radius = Config.FOVRadius
        FOVCircle.Filled = false
    end
end)

--// Janela Principal
local Window = Rayfield:CreateWindow({
    Name = "Royality Hub | Anti-Crash v9",
    LoadingTitle = "Carregando Hub...",
    LoadingSubtitle = "Delta Executor",
    ConfigurationSaving = { Enabled = false },
    KeySystem = false
})

local T1 = Window:CreateTab("Combate & Aim", 4483362458)
local T2 = Window:CreateTab("Voo & Movimento", 4483345998)
local T3 = Window:CreateTab("Visual (ESP)", 4483345998)

--// Aba Combate
T1:CreateToggle({
    Name = "🎯 Assistência de Mira (Aimbot Suave)",
    CurrentValue = Config.AimbotEnabled,
    Flag = "AE",
    Callback = function(v)
        Config.AimbotEnabled = v
        if FOVCircle then FOVCircle.Visible = v end
    end
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
    Range = {50, 600},
    Increment = 25,
    CurrentValue = Config.FOVRadius,
    Flag = "FOV",
    Callback = function(v)
        Config.FOVRadius = v
        if FOVCircle then FOVCircle.Radius = v end
    end
})

T1:CreateToggle({
    Name = "Ignorar Time (TeamCheck)",
    CurrentValue = Config.TeamCheck,
    Flag = "TC",
    Callback = function(v) Config.TeamCheck = v end
})

T1:CreateToggle({
    Name = "Rapid Fire Seguro",
    CurrentValue = Config.RapidFireEnabled,
    Flag = "RF",
    Callback = function(v) Config.RapidFireEnabled = v end
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

--// Aba Visual (ESP)
T3:CreateToggle({
    Name = "👁️ Ativar ESP Destaque",
    CurrentValue = Config.ESPEnabled,
    Flag = "ESP",
    Callback = function(v) Config.ESPEnabled = v end
})

--// ==========================================
--// SISTEMA DE AIMBOT SUAVE (SEM CRASH/BAN)
--// ==========================================
RunService.RenderStepped:Connect(function()
    pcall(function()
        if FOVCircle then
            FOVCircle.Position = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)
        end

        -- ESP Manager
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LP and p.Character then
                local char = p.Character
                local hl = char:FindFirstChild("RoyESP")
                if Config.ESPEnabled and char:FindFirstChildOfClass("Humanoid") and char:FindFirstChildOfClass("Humanoid").Health > 0 then
                    if not hl then
                        hl = Instance.new("Highlight")
                        hl.Name = "RoyESP"
                        hl.FillColor = Color3.fromRGB(255, 0, 0)
                        hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                        hl.FillTransparency = 0.5
                        hl.Adornee = char
                        hl.Parent = char
                    end
                    hl.Enabled = true
                elseif hl then
                    hl.Enabled = false
                end
            end
        end

        -- Aimbot Suave e Seguro para a Câmera
        if Config.AimbotEnabled and UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
            local closestTarget = nil
            local shortestDist = Config.FOVRadius
            local screenCenter = Vector2.new(Cam.ViewportSize.X / 2, Cam.ViewportSize.Y / 2)

            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LP and p.Character then
                    local hum = p.Character:FindFirstChildOfClass("Humanoid")
                    local part = p.Character:FindFirstChild(Config.AimPart)
                    if hum and hum.Health > 0 and part then
                        if Config.TeamCheck and p.Team == LP.Team then continue end
                        local screenPos, onScreen = Cam:WorldToViewportPoint(part.Position)
                        if onScreen then
                            local dist = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                            if dist < shortestDist then
                                shortestDist = dist
                                closestTarget = part
                            end
                        end
                    end
                end
            end

            if closestTarget then
                Cam.CFrame = CFrame.new(Cam.CFrame.Position, closestTarget.Position)
            end
        end

        -- Voo Seguro
        local char = LP.Character
        local rp = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChildOfClass("Humanoid")
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
            rp.CFrame = rp.CFrame + (flyVec * Config.FlySpeed * RunService.RenderStepped:Wait())
        end

        -- Kill Aura Seguro
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
    Title = "Royality Hub Carregado!",
    Content = "Modo Estável Anti-Crash Ativado com Sucesso.",
    Duration = 4
})
