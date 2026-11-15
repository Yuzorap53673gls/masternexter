--// ==========================================
--// ROYALITY HUB | ULTIMATE + BLINDADO + DEFINITIVO (COMPLETO)
--// ==========================================

local s,R=pcall(function()return loadstring(game:HttpGet('https://sirius.menu/rayfield'))()end)
if not s or not R then
local sA,RA=pcall(function()return loadstring(game:HttpGet('https://raw.githubusercontent.com/sirius-menu/rayfield/main/source'))()end)
if sA and RA then R=RA else game:GetService("StarterGui"):SetCore("SendNotification",{Title="Erro",Text="Falha ao carregar Rayfield.",Duration=5})return end end

getgenv().Config={ESPEnabled=true,AimbotEnabled=true,TeamCheck=false,AimPart="Head",FOVSemRadius=300,MaxDistance=500,AntiRecoilPower=2.0,ShowCursor=true,RageModeEnabled=false,RapidFireEnabled=true,DamageMultiplier=2.0,FlyEnabled=false,FlySpeed=80}

local P=game:GetService("Players")
local RS=game:GetService("RunService")
local UIS=game:GetService("UserInputService")
local LP=P.LocalPlayer
local Cam=workspace.CurrentCamera

UIS.MouseIconEnabled=Config.ShowCursor

local FC=nil
pcall(function()
if Drawing then
FC=Drawing.new("Circle")
FC.Visible=true
FC.Transparency=0.5
FC.Color=Color3.fromRGB(255,255,255)
FC.Thickness=1
FC.NumSides=64
FC.Radius=Config.FOVSemRadius
FC.Filled=false end end)

local W=R:CreateWindow({Name="Royality Hub | Shooter Ultimate Definitivo",LoadingTitle="Carregando...",LoadingSubtitle="por Delta Executor",ConfigurationSaving={Enabled=false},KeySystem=false})
local T1=W:CreateTab("Principal",4483362458)
local T2=W:CreateTab("Teleporte & Fly",4483345998)
local T3=W:CreateTab("Visual (ESP)",4483345998)

T1:CreateToggle({Name="Ativar Aimbot",CurrentValue=Config.AimbotEnabled,Flag="AT",Callback=function(v)Config.AimbotEnabled=v if FC then FC.Visible=v end end})
T1:CreateToggle({Name="🔥 Modo Rage",CurrentValue=Config.RageModeEnabled,Flag="RT",Callback=function(v)Config.RageModeEnabled=v end})
T1:CreateDropdown({Name="Foco do Aimbot",Options={"Cabeça","Peito","Pescoço"},CurrentOption="Cabeça",Flag="AF",Callback=function(o)Config.AimPart=o=="Cabeça" and "Head" or o=="Peito" and "Chest" or "Neck" end})
T1:CreateSlider({Name="Força Anti-Recoil",Range={0.5,5.0},Increment=0.1,Suffix="x",CurrentValue=Config.AntiRecoilPower,Flag="AR",Callback=function(v)Config.AntiRecoilPower=v end})
T1:CreateToggle({Name="Rapid Fire",CurrentValue=Config.RapidFireEnabled,Flag="RF",Callback=function(v)Config.RapidFireEnabled=v end})
T1:CreateSlider({Name="Multiplicador de Dano",Range={1,5},Increment=0.5,Suffix="x",CurrentValue=Config.DamageMultiplier,Flag="DM",Callback=function(v)Config.DamageMultiplier=v end})
T1:CreateSlider({Name="Distância Máxima",Range={50,1000},Increment=25,Suffix=" studs",CurrentValue=Config.MaxDistance,Flag="MD",Callback=function(v)Config.MaxDistance=v end})
T1:CreateSlider({Name="Raio de Detecção (FOV)",Range={50,800},Increment=25,CurrentValue=Config.FOVSemRadius,Flag="FR",Callback=function(v)Config.FOVSemRadius=v if FC then FC.Radius=v end end})
T1:CreateToggle({Name="TeamCheck",CurrentValue=Config.TeamCheck,Flag="TC",Callback=function(v)Config.TeamCheck=v end})

T2:CreateToggle({Name="Ativar Voo (Fly)",CurrentValue=Config.FlyEnabled,Flag="FT",Callback=function(v)Config.FlyEnabled=v end})
T2:CreateSlider({Name="Velocidade do Voo",Range={20,300},Increment=10,CurrentValue=Config.FlySpeed,Flag="FS",Callback=function(v)Config.FlySpeed=v end})
T2:CreateButton({Name="📍 Salvar Localização",Callback=function()if LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then getgenv().SCP=LP.Character.HumanoidRootPart.CFrame R:Notify({Title="Salvo!",Content="Posição guardada.",Duration=3}) end end})
T2:CreateButton({Name="🚀 Teleportar para Salvo",Callback=function()if getgenv().SCP and LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then LP.Character.HumanoidRootPart.CFrame=getgenv().SCP R:Notify({Title="Teleportado!",Content="Voltou ao local salvo.",Duration=3}) else R:Notify({Title="Erro",Content="Nenhum local salvo.",Duration=3}) end end})
T2:CreateButton({Name="🏠 Teleportar para Base/Spawn",Callback=function()if LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then for _,s in ipairs(workspace:GetDescendants()) do if s:IsA("SpawnLocation") then LP.Character.HumanoidRootPart.CFrame=s.CFrame+Vector3.new(0,3,0) break end end end end})

local pNames={}
for _,p in ipairs(P:GetPlayers()) do if p~=LP then table.insert(pNames,p.Name) end end
P.PlayerAdded:Connect(function(p)if p~=LP then table.insert(pNames,p.Name) end end)
P.PlayerRemoving:Connect(function(p)for i,n in ipairs(pNames) do if n==p.Name then table.remove(pNames,i) end end end)

T2:CreateDropdown({Name="Teleportar para Jogador",Options=pNames,CurrentOption="",Flag="TPJ",Callback=function(o)local tp=P:FindFirstChild(o)if tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart") and LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then LP.Character.HumanoidRootPart.CFrame=tp.Character.HumanoidRootPart.CFrame+Vector3.new(0,3,0) end end})

T3:CreateToggle({Name="ESP (Vermelho + Antena)",CurrentValue=Config.ESPEnabled,Flag="ESPT",Callback=function(v)Config.ESPEnabled=v end})
T3:CreateToggle({Name="🖱️ Forçar Cursor Visível",CurrentValue=Config.ShowCursor,Flag="CT",Callback=function(v)Config.ShowCursor=v UIS.MouseIconEnabled=v end})

R:Notify({Title="Royality Hub Carregado!",Content="Pronto para uso.",Duration=5,Image=4483362458})

local function applyESP(p)
if p==LP then return end
local function uc(c)
task.wait(0.3)
if not c then return end
local h,r=c:FindFirstChild("Head"),c:FindFirstChild("HumanoidRootPart")
if not h or not r then return end
if c:FindFirstChild("R_HL") then c.R_HL:Destroy() end
local hl=Instance.new("Highlight")
hl.Name="R_HL"
hl.Adornee=c
hl.FillColor=Color3.fromRGB(255,0,0)
hl.OutlineColor=Color3.fromRGB(255,255,255)
hl.FillTransparency=0.4
hl.Enabled=Config.ESPEnabled
hl.Parent=c
local bb=h:FindFirstChild("R_Arr") or Instance.new("BillboardGui")
bb.Name="R_Arr"
bb.Adornee=h
bb.Size=UDim2.new(0,40,0,80)
bb.StudsOffset=Vector3.new(0,6,0)
bb.AlwaysOnTop=true
bb.Enabled=Config.ESPEnabled
bb.Parent=h
if not bb:FindFirstChild("Line") then
local l=Instance.new("Frame")
l.Name="Line"
l.Size=UDim2.new(0,2,0.7,0)
l.Position=UDim2.new(0.5,-1,0,0)
l.BackgroundColor3=Color3.fromRGB(255,0,0)
l.BorderSizePixel=0
l.Parent=bb
local a=Instance.new("TextLabel")
a.Size=UDim2.new(1,0,0.3,0)
a.Position=UDim2.new(0,0,0.7,0)
a.BackgroundTransparency=1
a.Text="▼"
a.TextColor3=Color3.fromRGB(255,0,0)
a.TextSize=18
a.Font=Enum.Font.GothamBold
a.Parent=bb end end
if p.Character then task.spawn(function()uc(p.Character)end) end
p.CharacterAdded:Connect(uc)
end
for _,p in ipairs(P:GetPlayers()) do applyESP(p) end
P.PlayerAdded:Connect(applyESP)

local function mw(t)
if t:IsA("Tool") then
for _,v in ipairs(t:GetDescendants()) do
if v:IsA("NumberValue") or v:IsA("IntValue") then
local n=v.Name:lower()
if n:find("cooldown") or n:find("firerate") or n:find("delay") or n:find("fire") then pcall(function()v.Value=v.Value/3 end) end
if n:find("damage") or n:find("dmg") then pcall(function()v.Value=v.Value*Config.DamageMultiplier end) end end end end end

local function hc(c)
c.ChildAdded:Connect(function(ch)if ch:IsA("Tool") then task.wait(0.05) mw(ch) end end)
for _,ch in ipairs(c:GetChildren()) do if ch:IsA("Tool") then mw(ch) end end end
if LP.Character then hc(LP.Character) end
LP.CharacterAdded:Connect(hc)

local isS=false
local lST=0
UIS.InputBegan:Connect(function(i,gp)if not gp and (i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch) then isS=true lST=tick() end end)
UIS.InputEnded:Connect(function(i)if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then isS=false end end)

local function getTarget()
local closest,shortest=nil,Config.FOVSemRadius
local sC=Vector2.new(Cam.ViewportSize.X/2,Cam.ViewportSize.Y/2)
if FC then FC.Position=sC end
if not LP.Character or not LP.Character:FindFirstChild("HumanoidRootPart") then return nil end
local lPos=LP.Character.HumanoidRootPart.Position
for _,p in ipairs(P:GetPlayers()) do
if p~=LP and p.Character then
local hum=p.Character:FindFirstChildOfClass("Humanoid")
local head=p.Character:FindFirstChild("Head")
local rp=p.Character:FindFirstChild("HumanoidRootPart")
if hum and hum.Health>0 and head and rp then
if Config.TeamCheck and p.Team==LP.Team then continue end
local dist=(rp.Position-lPos).Magnitude
if dist<=Config.MaxDistance then
local pos,onS=Cam:WorldToViewportPoint(head.Position)
if onS then
local d=(Vector2.new(pos.X,pos.Y)-sC).Magnitude
if d<=shortest then shortest=d closest=p end end end end end end
return closest
end

RS.RenderStepped:Connect(function()
for _,p in ipairs(P:GetPlayers()) do
if p~=LP and p.Character then
local sh=Config.ESPEnabled and (not Config.TeamCheck or p.Team~=LP.Team)
local hl=p.Character:FindFirstChild("R_HL")
if hl then hl.Enabled=sh end
local h=p.Character:FindFirstChild("Head")
if h then local bb=h:FindFirstChild("R_Arr") if bb then bb.Enabled=sh end end end end

local char=LP.Character
if char and char:FindFirstChild("HumanoidRootPart") and char:FindFirstChildOfClass("Humanoid") then
local rp,hum=char.HumanoidRootPart,char:FindFirstChildOfClass("Humanoid")
if Config.FlyEnabled then
hum.PlatformStand=true
pcall(function()for _,pt in ipairs(char:GetDescendants()) do if pt:IsA("BasePart") then pt.CanCollide=false end end end)
local bv,bg=rp:FindFirstChild("R_BV"),rp:FindFirstChild("R_BG")
if not bv then bv=Instance.new("BodyVelocity") bv.Name="R_BV" bv.MaxForce=Vector3.new(math.huge,math.huge,math.huge) bv.Parent=rp end
if not bg then bg=Instance.new("BodyGyro") bg.Name="R_BG" bg.MaxTorque=Vector3.new(math.huge,math.huge,math.huge) bg.P=15000 bg.Parent=rp end
local cC=Cam.CFrame
local mD=Vector3.new()
if UIS:IsKeyDown(Enum.KeyCode.W) or UIS:IsKeyDown(Enum.KeyCode.Up) then mD=mD+cC.LookVector end
if UIS:IsKeyDown(Enum.KeyCode.S) or UIS:IsKeyDown(Enum.KeyCode.Down) then mD=mD-cC.LookVector end
if UIS:IsKeyDown(Enum.KeyCode.A) or UIS:IsKeyDown(Enum.KeyCode.Left) then mD=mD-cC.RightVector end
if UIS:IsKeyDown(Enum.KeyCode.D) or UIS:IsKeyDown(Enum.KeyCode.Right) then mD=mD+cC.RightVector end
if UIS:IsKeyDown(Enum.KeyCode.Space) then mD=mD+Vector3.new(0,1,0) end
if UIS:IsKeyDown(Enum.KeyCode.LeftShift) or UIS:IsKeyDown(Enum.KeyCode.RightShift) then mD=mD-Vector3.new(0,1,0) end
bv.Velocity=mD*Config.FlySpeed
bg.CFrame=cC
else
hum.PlatformStand=false
pcall(function()for _,pt in ipairs(char:GetDescendants()) do if pt:IsA("BasePart") then pt.CanCollide=true end end end)
if rp:FindFirstChild("R_BV") then rp.R_BV:Destroy() end
if rp:FindFirstChild("R_BG") then rp.R_BG:Destroy() end end end

local target=getTarget()
if Config.RageModeEnabled and target and target.Character then
local tool=LP.Character and LP.Character:FindFirstChildOfClass("Tool")
if tool then pcall(function()tool:Activate()end) end end

local activeF=isS or (tick()-lST<0.2) or Config.RageModeEnabled
if Config.AimbotEnabled and activeF and target and target.Character then
local head=target.Character:FindFirstChild("Head")
local ut=target.Character:FindFirstChild("UpperTorso") or target.Character:FindFirstChild("Torso")
local rp=target.Character:FindFirstChild("HumanoidRootPart")
if head and rp then
local tPos=head.Position
if Config.AimPart=="Chest" then tPos=ut and ut.Position or (rp.Position+Vector3.new(0,0.8,0))
elseif Config.AimPart=="Neck" then tPos=head.Position-Vector3.new(0,0.3,0) end
tPos=tPos-Vector3.new(0,Config.AntiRecoilPower*0.5,0)
Cam.CFrame=Cam.CFrame:Lerp(CFrame.new(Cam.CFrame.Position,tPos),1.0) end end end)
