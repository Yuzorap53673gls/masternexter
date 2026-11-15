--// ==========================================
--// ROYALITY HUB | DEFINITIVO + SLIDER DE PRECISÃO AIMBOT (0-100%)
--// ==========================================

local s,R=pcall(function()return loadstring(game:HttpGet('https://sirius.menu/rayfield'))()end)
if not s or not R then
    local sA,RA=pcall(function()return loadstring(game:HttpGet('https://raw.githubusercontent.com/sirius-menu/rayfield/main/source'))()end)
    if sA and RA then R=RA else return end
end

getgenv().Config={
    ESPEnabled=true,
    SilentAimEnabled=true,
    TeamCheck=false,
    WallCheck=true,
    AimPart="Head",
    FOVRadius=220,
    MaxDistance=400,
    AimbotPrecision=100, -- Novo: Controle de precisão de 0 a 100%
    ShowCursor=true,
    RapidFireEnabled=true,
    DamageMultiplier=3.0,
    AntiRecoilPower=5.0,
    FlyEnabled=false,
    FlySpeed=70,
    KillAuraEnabled=false
}

local P=game:GetService("Players")
local RS=game:GetService("RunService")
local UIS=game:GetService("UserInputService")
local LP=P.LocalPlayer
local Mouse=LP:GetMouse()
local Cam=workspace.CurrentCamera

UIS.MouseIconEnabled=Config.ShowCursor

--// Círculo FOV
local FC=nil
pcall(function()
    if Drawing then
        FC=Drawing.new("Circle")
        FC.Visible=true
        FC.Transparency=0.6
        FC.Color=Color3.fromRGB(255,50,50)
        FC.Thickness=1.5
        FC.NumSides=64
        FC.Radius=Config.FOVRadius
        FC.Filled=false
    end
end)

--// Janela Rayfield
local W=R:CreateWindow({
    Name="Royality Hub | Ultimate Corrigido v3",
    LoadingTitle="Carregando...",
    LoadingSubtitle="Delta Executor",
    ConfigurationSaving={Enabled=false},
    KeySystem=false
})

local T1=W:CreateTab("Combate & Kill Aura",4483362458)
local T2=W:CreateTab("Voo & Teleporte",4483345998)
local T3=W:CreateTab("Visual (ESP)",4483345998)

--// Aba Combate
T1:CreateToggle({
    Name="🎯 Aimbot / Silent Aim Definitivo",
    CurrentValue=Config.SilentAimEnabled,
    Flag="SA",
    Callback=function(v)
        Config.SilentAimEnabled=v
        if FC then FC.Visible=v end
    end
})

T1:CreateSlider({
    Name="⚡ Precisão do Aimbot (Grudar na Cabeça)",
    Range={0, 100},
    Increment=5,
    Suffix="%",
    CurrentValue=Config.AimbotPrecision,
    Flag="AAP",
    Callback=function(v) Config.AimbotPrecision=v end
})

T1:CreateToggle({
    Name="✈️ Kill Aura Aéreo (Voa até os inimigos e mata)",
    CurrentValue=Config.KillAuraEnabled,
    Flag="KA",
    Callback=function(v)
        Config.KillAuraEnabled=v
    end
})

T1:CreateDropdown({
    Name="Onde o Tiro Vai",
    Options={"Cabeça","Peito","Pescoço"},
    CurrentOption="Cabeça",
    Flag="AP",
    Callback=function(o)
        Config.AimPart=o=="Cabeça" and "Head" or o=="Peito" and "UpperTorso" or "Neck"
    end
})

T1:CreateSlider({
    Name="Força Anti-Recoil",
    Range={1.0, 8.0},
    Increment=0.5,
    Suffix="x",
    CurrentValue=Config.AntiRecoilPower,
    Flag="ARP",
    Callback=function(v) Config.AntiRecoilPower=v end
})

T1:CreateSlider({
    Name="Distância Máxima",
    Range={50,600},
    Increment=25,
    Suffix=" studs",
    CurrentValue=Config.MaxDistance,
    Flag="MD",
    Callback=function(v) Config.MaxDistance=v end
})

T1:CreateSlider({
    Name="Raio de Mira (FOV)",
    Range={50,500},
    Increment=10,
    CurrentValue=Config.FOVRadius,
    Flag="FOV",
    Callback=function(v)
        Config.FOVRadius=v
        if FC then FC.Radius=v end
    end
})

T1:CreateToggle({
    Name="🧱 WallCheck (Apenas Visíveis)",
    CurrentValue=Config.WallCheck,
    Flag="WC",
    Callback=function(v) Config.WallCheck=v end
})

T1:CreateToggle({
    Name="Ignorar Time (TeamCheck)",
    CurrentValue=Config.TeamCheck,
    Flag="TC",
    Callback=function(v) Config.TeamCheck=v end
})

T1:CreateToggle({
    Name="Rapid Fire Agressivo",
    CurrentValue=Config.RapidFireEnabled,
    Flag="RF",
    Callback=function(v) Config.RapidFireEnabled=v end
})

T1:CreateSlider({
    Name="Multiplicador de Dano",
    Range={1,10},
    Increment=0.5,
    Suffix="x",
    CurrentValue=Config.DamageMultiplier,
    Flag="DM",
    Callback=function(v) Config.DamageMultiplier=v end
})

--// Aba Voo & Teleporte
T2:CreateToggle({
    Name="✈️ Ativar Voo Perfeito",
    CurrentValue=Config.FlyEnabled,
    Flag="FT",
    Callback=function(v)
        Config.FlyEnabled=v
        local c=LP.Character
        if c and c:FindFirstChildOfClass("Humanoid") then
            c:FindFirstChildOfClass("Humanoid").PlatformStand=false
        end
    end
})

T2:CreateSlider({
    Name="Velocidade do Voo",
    Range={20,300},
    Increment=10,
    CurrentValue=Config.FlySpeed,
    Flag="FS",
    Callback=function(v) Config.FlySpeed=v end
})

T2:CreateButton({
    Name="📍 Salvar Posição Atual",
    Callback=function()
        if LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then
            getgenv().SavedPos=LP.Character.HumanoidRootPart.CFrame
            R:Notify({Title="Salvo!",Content="Posição guardada com sucesso.",Duration=2})
        end
    end
})

T2:CreateButton({
    Name="🚀 Ir para Posição Salva",
    Callback=function()
        if getgenv().SavedPos and LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then
            LP.Character.HumanoidRootPart.CFrame=getgenv().SavedPos
        end
    end
})

local pNames={}
for _,p in ipairs(P:GetPlayers()) do if p~=LP then table.insert(pNames,p.Name) end end
P.PlayerAdded:Connect(function(p) if p~=LP then table.insert(pNames,p.Name) end end)
P.PlayerRemoving:Connect(function(p) for i,n in ipairs(pNames) do if n==p.Name then table.remove(pNames,i) end end end)

T2:CreateDropdown({
    Name="Teleportar para Jogador",
    Options=pNames,
    CurrentOption="",
    Flag="TPJ",
    Callback=function(o)
        local tp=P:FindFirstChild(o)
        if tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart") and LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then
            LP.Character.HumanoidRootPart.CFrame=tp.Character.HumanoidRootPart.CFrame+Vector3.new(0,3,0)
        end
    end
})

--// Aba Visual (ESP)
T3:CreateToggle({
    Name="👁️ Ativar ESP Completo (Seta + Destaque)",
    CurrentValue=Config.ESPEnabled,
    Flag="ESP",
    Callback=function(v) Config.ESPEnabled=v end
})

T3:CreateToggle({
    Name="🖱️ Forçar Cursor Visível",
    CurrentValue=Config.ShowCursor,
    Flag="CUR",
    Callback=function(v)
        Config.ShowCursor=v
        UIS.MouseIconEnabled=v
    end
})

--// ==========================================
--// LÓGICA DE ALVO E VISIBILIDADE
--// ==========================================
local function isVisible(targetPart, targetChar)
    if not Config.WallCheck then return true end
    local origin=Cam.CFrame.Position
    local direction=(targetPart.Position-origin)
    local params=RaycastParams.new()
    params.FilterType=Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances={LP.Character,Cam}
    local result=workspace:Raycast(origin,direction,params)
    if result then
        return result.Instance:IsDescendantOf(targetChar)
    end
    return true
end

local function getBestTarget()
    local closestPart=nil
    local shortestScreenDist=Config.FOVRadius
    local screenCenter=Vector2.new(Cam.ViewportSize.X/2,Cam.ViewportSize.Y/2)
    if FC then FC.Position=screenCenter end

    local myChar=LP.Character
    local myRoot=myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myRoot then return nil end

    for _,p in ipairs(P:GetPlayers()) do
        if p~=LP and p.Character then
            local hum=p.Character:FindFirstChildOfClass("Humanoid")
            local rp=p.Character:FindFirstChild("HumanoidRootPart")
            if hum and hum.Health>0 and rp then
                if Config.TeamCheck and p.Team==LP.Team then continue end

                local worldDist=(rp.Position-myRoot.Position).Magnitude
                if worldDist<=Config.MaxDistance then
                    local aimPart=p.Character:FindFirstChild(Config.AimPart) or p.Character:FindFirstChild("Head")
                    if aimPart then
                        local screenPos,onScreen=Cam:WorldToViewportPoint(aimPart.Position)
                        if onScreen and screenPos.Z>0 then
                            local distFromCrosshair=(Vector2.new(screenPos.X,screenPos.Y)-screenCenter).Magnitude
                            if distFromCrosshair<shortestScreenDist and isVisible(aimPart,p.Character) then
                                shortestScreenDist=distFromCrosshair
                                closestPart=aimPart
                            end
                        end
                    end
                end
            end
        end
    end
    return closestPart
end

--// ==========================================
--// SILENT AIM COM PRECISÃO AJUSTÁVEL (0-100%)
--// ==========================================
local CurrentTargetPart=nil

local oldIndex
oldIndex=hookmetamethod(game,"__index",newcclosure(function(self,key)
    if not checkcaller() and Config.SilentAimEnabled and CurrentTargetPart and math.random(0, 100) <= Config.AimbotPrecision then
        if self==Mouse then
            if key=="Hit" then
                local finalPos = CurrentTargetPart.Position - Vector3.new(0, Config.AntiRecoilPower * 0.1, 0)
                return CFrame.new(finalPos)
            elseif key=="Target" then
                return CurrentTargetPart.Parent:FindFirstChild("Head") or CurrentTargetPart
            end
        end
    end
    return oldIndex(self,key)
end))

local oldNamecall
oldNamecall=hookmetamethod(game,"__namecall",newcclosure(function(self,...)
    local method=getnamecallmethod()
    local args={...}
    if not checkcaller() and Config.SilentAimEnabled and CurrentTargetPart and math.random(0, 100) <= Config.AimbotPrecision then
        if self==workspace and method=="Raycast" and typeof(args[1])=="Vector3" and typeof(args[2])=="Vector3" then
            local origin=args[1]
            local targetPos = CurrentTargetPart.Position - Vector3.new(0, Config.AntiRecoilPower * 0.1, 0)
            local dir=(targetPos-origin).Unit * args[2].Magnitude
            args[2]=dir
            return oldNamecall(self,unpack(args))
        end
    end
    return oldNamecall(self,...)
end))

--// ==========================================
--// ESP COMPLETO
--// ==========================================
local function manageESP(p)
    if p==LP or not p.Character then return end
    local char=p.Character
    local head=char:FindFirstChild("Head")
    local hum=char:FindFirstChildOfClass("Humanoid")
    local shouldShow=Config.ESPEnabled and hum and hum.Health>0 and (not Config.TeamCheck or p.Team~=LP.Team)

    local hl=char:FindFirstChild("RoyalityESP_HL")
    if shouldShow then
        if not hl then
            hl=Instance.new("Highlight")
            hl.Name="RoyalityESP_HL"
            hl.FillColor=Color3.fromRGB(255,0,0)
            hl.OutlineColor=Color3.fromRGB(255,255,255)
            hl.FillTransparency=0.5
            hl.OutlineTransparency=0
            hl.DepthMode=Enum.HighlightDepthMode.AlwaysOnTop
            hl.Adornee=char
            hl.Parent=char
        end
        hl.Enabled=true
    elseif hl then
        hl.Enabled=false
    end

    if head then
        local bb=head:FindFirstChild("RoyalityESP_BB")
        if shouldShow then
            if not bb then
                bb=Instance.new("BillboardGui")
                bb.Name="RoyalityESP_BB"
                bb.Adornee=head
                bb.Size=UDim2.new(0,80,0,70)
                bb.StudsOffset=Vector3.new(0,4.5,0)
                bb.AlwaysOnTop=true
                bb.Parent=head

                local txt=Instance.new("TextLabel")
                txt.Name="Label"
                txt.Size=UDim2.new(1,0,0.4,0)
                txt.BackgroundTransparency=1
                txt.Text=p.Name
                txt.TextColor3=Color3.fromRGB(255,80,80)
                txt.TextStrokeTransparency=0
                txt.TextSize=12
                txt.Font=Enum.Font.GothamBold
                txt.Parent=bb

                local arrow=Instance.new("TextLabel")
                arrow.Name="Arrow"
                arrow.Size=UDim2.new(1,0,0.6,0)
                arrow.Position=UDim2.new(0,0,0.4,0)
                arrow.BackgroundTransparency=1
                arrow.Text="▼"
                arrow.TextColor3=Color3.fromRGB(255,0,0)
                arrow.TextStrokeTransparency=0
                arrow.TextSize=20
                arrow.Font=Enum.Font.GothamBold
                txt.Parent=bb
            end
            bb.Enabled=true
        elseif bb then
            bb.Enabled=false
        end
    end
end

--// ==========================================
--// MODIFICAÇÃO DE ARMAS
--// ==========================================
local function modTool(t)
    if not t:IsA("Tool") then return end
    for _,v in ipairs(t:GetDescendants()) do
        if v:IsA("NumberValue") or v:IsA("IntValue") then
            local n=v.Name:lower()
            if Config.RapidFireEnabled and (n:find("cooldown") or n:find("firerate") or n:find("delay")) then
                pcall(function() v.Value=v.Value/3 end)
            end
            if n:find("damage") or n:find("dmg") then
                pcall(function() v.Value=v.Value*Config.DamageMultiplier end)
            end
        end
    end
end

local function setupChar(c)
    c.ChildAdded:Connect(function(ch)
        if ch:IsA("Tool") then task.wait(0.05) modTool(ch) end
    end)
    for _,ch in ipairs(c:GetChildren()) do
        if ch:IsA("Tool") then modTool(ch) end
    end
end
if LP.Character then setupChar(LP.Character) end
LP.CharacterAdded:Connect(setupChar)

--// ==========================================
--// LOOP PRINCIPAL (FLY + KILL AURA AÉREO SUAVE)
--// ==========================================
RS.RenderStepped:Connect(function(dt)
    CurrentTargetPart=Config.SilentAimEnabled and getBestTarget() or nil

    for _,p in ipairs(P:GetPlayers()) do
        manageESP(p)
    end

    local char=LP.Character
    local rp=char and char:FindFirstChild("HumanoidRootPart")
    local hum=char and char:FindFirstChildOfClass("Humanoid")

    if rp and hum then
        -- 1. Fly Perfeito
        if Config.FlyEnabled then
            hum.PlatformStand=true
            rp.AssemblyLinearVelocity=Vector3.new(0,0,0)
            for _,part in ipairs(char:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide=false end
            end

            local moveDir=hum.MoveDirection
            local camCFrame=Cam.CFrame
            local flyVector=Vector3.new()

            if moveDir.Magnitude>0 then
                flyVector = Vector3.new(camCFrame.LookVector.X, camCFrame.LookVector.Y, camCFrame.LookVector.Z).Unit * moveDir.Magnitude
            end

            if UIS:IsKeyDown(Enum.KeyCode.Space) then
                flyVector = flyVector + Vector3.new(0, 1, 0)
            end
            if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then
                flyVector = flyVector + Vector3.new(0, -1, 0)
            end

            rp.CFrame = rp.CFrame + (flyVector * Config.FlySpeed * dt)
        else
            if not Config.KillAuraEnabled then
                hum.PlatformStand=false
            end
        end

        -- 2. Kill Aura Aéreo Suave
        if Config.KillAuraEnabled then
            hum.PlatformStand=true
            for _,part in ipairs(char:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide=false end
            end

            local targetFound = false
            for _,p in ipairs(P:GetPlayers()) do
                if p~=LP and p.Character then
                    local pHum=p.Character:FindFirstChildOfClass("Humanoid")
                    local pRp=p.Character:FindFirstChild("HumanoidRootPart")
                    if pHum and pHum.Health>0 and pRp then
                        if not Config.TeamCheck or p.Team~=LP.Team then
                            targetFound = true
                            local targetPos = pRp.CFrame + Vector3.new(0, 6, 0)
                            rp.CFrame = rp.CFrame:Lerp(targetPos, math.clamp(dt * 12, 0, 1))
                            
                            local tool = char:FindFirstChildOfClass("Tool")
                            if tool then
                                pcall(function() tool:Activate() end)
                            end
                            break
                        end
                    end
                end
            end
            if not targetFound and not Config.FlyEnabled then
                hum.PlatformStand = false
            end
        end
    end
end)

R:Notify({
    Title="Royality Hub Atualizado!",
    Content="Precisão do Aimbot (0-100%) Adicionada com Sucesso.",
    Duration=4
})
