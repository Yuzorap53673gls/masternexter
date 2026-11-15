--// ==========================================
--// ROYALITY HUB | SILENT AIM + FLY MOBILE/PC + ESP FIX
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
    WallCheck=true, -- Não pega através de parede/outro jogador
    AimPart="Head",
    FOVRadius=120, -- Bem fechado para pegar só quem você apontar
    MaxDistance=150, -- Só funciona em oponentes perto
    ShowCursor=true,
    RapidFireEnabled=true,
    DamageMultiplier=2.0,
    FlyEnabled=false,
    FlySpeed=60
}

local P=game:GetService("Players")
local RS=game:GetService("RunService")
local UIS=game:GetService("UserInputService")
local LP=P.LocalPlayer
local Mouse=LP:GetMouse()
local Cam=workspace.CurrentCamera

UIS.MouseIconEnabled=Config.ShowCursor

--// Círculo FOV no centro da tela
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
    Name="Royality Hub | Definitivo Corrigido",
    LoadingTitle="Carregando...",
    LoadingSubtitle="Delta Executor",
    ConfigurationSaving={Enabled=false},
    KeySystem=false
})

local T1=W:CreateTab("Combate (Mira Livre)",4483362458)
local T2=W:CreateTab("Voo & Teleporte",4483345998)
local T3=W:CreateTab("Visual (ESP)",4483345998)

T1:CreateToggle({
    Name="🎯 Silent Aim (Tela Livre + Tiro na Cabeça)",
    CurrentValue=Config.SilentAimEnabled,
    Flag="SA",
    Callback=function(v)
        Config.SilentAimEnabled=v
        if FC then FC.Visible=v end
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
    Name="Distância Máxima (Só Perto)",
    Range={30,400},
    Increment=10,
    Suffix=" studs",
    CurrentValue=Config.MaxDistance,
    Flag="MD",
    Callback=function(v) Config.MaxDistance=v end
})

T1:CreateSlider({
    Name="Precisão da Mira (FOV)",
    Range={40,300},
    Increment=10,
    CurrentValue=Config.FOVRadius,
    Flag="FOV",
    Callback=function(v)
        Config.FOVRadius=v
        if FC then FC.Radius=v end
    end
})

T1:CreateToggle({
    Name="🧱 Só Quem Está Visível (WallCheck)",
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
    Name="Rapid Fire",
    CurrentValue=Config.RapidFireEnabled,
    Flag="RF",
    Callback=function(v) Config.RapidFireEnabled=v end
})

T1:CreateSlider({
    Name="Multiplicador de Dano",
    Range={1,5},
    Increment=0.5,
    Suffix="x",
    CurrentValue=Config.DamageMultiplier,
    Flag="DM",
    Callback=function(v) Config.DamageMultiplier=v end
})

--// Aba Voo & Teleporte
T2:CreateToggle({
    Name="✈️ Ativar Voo (Funciona Mobile e PC)",
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
    Range={20,250},
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
            R:Notify({Title="Salvo!",Content="Posição salva com sucesso.",Duration=2})
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

--// Aba Visual (ESP Corrigido)
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
--// 1. VERIFICAÇÃO DE VISIBILIDADE E ALVO ÚNICO
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

                -- Checa distância física (só perto)
                local worldDist=(rp.Position-myRoot.Position).Magnitude
                if worldDist<=Config.MaxDistance then
                    local aimPart=p.Character:FindFirstChild(Config.AimPart) or p.Character:FindFirstChild("Head")
                    if aimPart then
                        local screenPos,onScreen=Cam:WorldToViewportPoint(aimPart.Position)
                        if onScreen and screenPos.Z>0 then
                            local distFromCrosshair=(Vector2.new(screenPos.X,screenPos.Y)-screenCenter).Magnitude
                            -- Pega apenas quem está mais próximo do centro da mira E visível na frente
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
--// 2. SILENT AIM (TELA LIVRE, TIRO VAI NA CABEÇA)
--// ==========================================
local CurrentTargetPart=nil

-- Hook no Mouse.Hit e Mouse.Target (para armas baseadas em Mouse)
local oldIndex
oldIndex=hookmetamethod(game,"__index",newcclosure(function(self,key)
    if not checkcaller() and Config.SilentAimEnabled and CurrentTargetPart then
        if self==Mouse then
            if key=="Hit" then
                return CFrame.new(CurrentTargetPart.Position)
            elseif key=="Target" then
                return CurrentTargetPart
            end
        end
    end
    return oldIndex(self,key)
end))

-- Hook em Raycast (para armas modernas de FPS/TPS no Roblox)
local oldNamecall
oldNamecall=hookmetamethod(game,"__namecall",newcclosure(function(self,...)
    local method=getnamecallmethod()
    local args={...}
    if not checkcaller() and Config.SilentAimEnabled and CurrentTargetPart then
        if self==workspace and method=="Raycast" and typeof(args[1])=="Vector3" and typeof(args[2])=="Vector3" then
            local origin=args[1]
            local dir=(CurrentTargetPart.Position-origin).Unit * args[2].Magnitude
            args[2]=dir
            return oldNamecall(self,unpack(args))
        end
    end
    return oldNamecall(self,...)
end))

--// ==========================================
--// 3. SISTEMA DE ESP ROBUSTO (ATUALIZAÇÃO CONTÍNUA)
--// ==========================================
local function manageESP(p)
    if p==LP or not p.Character then return end
    local char=p.Character
    local head=char:FindFirstChild("Head")
    local hum=char:FindFirstChildOfClass("Humanoid")
    local shouldShow=Config.ESPEnabled and hum and hum.Health>0 and (not Config.TeamCheck or p.Team~=LP.Team)

    -- Highlight
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

    -- Antena / Seta acima da cabeça (Funciona mesmo se o Highlight atingir limite do Roblox)
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
                arrow.Parent=bb
            end
            bb.Enabled=true
        elseif bb then
            bb.Enabled=false
        end
    end
end

--// ==========================================
--// 4. MODIFICAÇÃO DE ARMAS (RAPID FIRE + DANO)
--// ==========================================
local function modTool(t)
    if not t:IsA("Tool") then return end
    for _,v in ipairs(t:GetDescendants()) do
        if v:IsA("NumberValue") or v:IsA("IntValue") then
            local n=v.Name:lower()
            if Config.RapidFireEnabled and (n:find("cooldown") or n:find("firerate") or n:find("delay")) then
                pcall(function() v.Value=v.Value/2.5 end)
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
--// 5. LOOP PRINCIPAL (MIRA, ESP E FLY MOBILE/PC)
--// ==========================================
RS.RenderStepped:Connect(function(dt)
    -- Atualiza alvo do Silent Aim
    CurrentTargetPart=Config.SilentAimEnabled and getBestTarget() or nil

    -- Atualiza ESP de todos os jogadores
    for _,p in ipairs(P:GetPlayers()) do
        manageESP(p)
    end

    -- Sistema de Voo Universal (Funciona no Analógico Mobile e WASD PC)
    local char=LP.Character
    local rp=char and char:FindFirstChild("HumanoidRootPart")
    local hum=char and char:FindFirstChildOfClass("Humanoid")

    if rp and hum then
        if Config.FlyEnabled then
            -- Anula gravidade e permite atravessar barreiras
            rp.AssemblyLinearVelocity=Vector3.new(0,0.5,0)
            for _,part in ipairs(char:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide=false end
            end

            local moveDir=hum.MoveDirection
            local vertical=0
            if UIS:IsKeyDown(Enum.KeyCode.Space) then vertical=1 end
            if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then vertical=-1 end

            -- Se estiver no mobile ou andando com WASD, voa na direção da câmera
            if moveDir.Magnitude>0 then
                local camLook=Cam.CFrame.LookVector
                local camRight=Cam.CFrame.RightVector
                -- Projeta o movimento do analógico/WASD em 3D seguindo para onde você olha
                local flatLook=Vector3.new(camLook.X,0,camLook.Z).Unit
                local flatRight=Vector3.new(camRight.X,0,camRight.Z).Unit
                local forwardAmount=moveDir:Dot(flatLook)
                local rightAmount=moveDir:Dot(flatRight)
                local flyVector=(camLook*forwardAmount)+(camRight*rightAmount)+Vector3.new(0,vertical,0)
                rp.CFrame=rp.CFrame+(flyVector*Config.FlySpeed*dt)
            elseif vertical~=0 then
                rp.CFrame=rp.CFrame+Vector3.new(0,vertical*Config.FlySpeed*dt,0)
            end
        end
    end
end)

R:Notify({
    Title="Royality Hub Atualizado!",
    Content="Silent Aim, ESP e Voo Mobile/PC corrigidos.",
    Duration=4
})
