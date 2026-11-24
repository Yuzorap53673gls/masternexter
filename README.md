--// ==========================================================
--// ROYALITY HUB | V20  (completo, sem Rayfield)
--// Interface propria: SEMPRE aparece na tela.
--// Funciona no PC e no celular (botoes de toque inclusos).
--// ==========================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local TPS = game:GetService("TeleportService")

local LP = Players.LocalPlayer

local Config = {
	Aimbot = false,
	AutoFire = false,
	WallCheck = true,
	FOV = 150,
	AutoMed = false,
	MedAt = 60,
	Fly = false,
	FlySpeed = 60,
	ESP = false,
	Antenna = false,
	LootESP = false,
	AntiLag = false,
	NoFog = false,
}

local State = {
	firing = false,
	flyUp = false,
	flyDown = false,
	esp = {},
	loot = {},
	bots = {},
	lastMed = 0,
	sizeChanged = true,
}

local function create(class, props)
	local o = Instance.new(class)
	if props then
		for k, v in pairs(props) do
			o[k] = v
		end
	end
	return o
end

local function notify(title, text, dur)
	pcall(function()
		game:GetService("StarterGui"):SetCore("SendNotification", {
			Title = tostring(title),
			Text = tostring(text),
			Duration = dur or 4,
		})
	end)
end

--// ---- raiz da interface (com 3 tentativas) -------------------
local gui = create("ScreenGui", {
	Name = "RoyalityHub",
	ResetOnSpawn = false,
	IgnoreGuiInset = true,
	DisplayOrder = 9999,
})

local parented = pcall(function()
	gui.Parent = (gethui and gethui()) or game:GetService("CoreGui")
end)
if not parented or not gui.Parent then
	pcall(function()
		gui.Parent = LP:WaitForChild("PlayerGui", 10)
	end)
end
if not gui.Parent then
	gui:Destroy()
	notify("Royality", "Nao consegui criar a interface neste executor.", 6)
	return
end

--// ---- painel -------------------------------------------------
local main = create("Frame", {
	Size = UDim2.new(0, 268, 0, 440),
	Position = UDim2.new(0, 24, 0, 80),
	BackgroundColor3 = Color3.fromRGB(20, 20, 26),
	BorderSizePixel = 0,
	Active = true,
	Parent = gui,
})
create("UICorner", { CornerRadius = UDim.new(0, 10), Parent = main })
create("UIStroke", {
	Color = Color3.fromRGB(52, 52, 64),
	Thickness = 1,
	Parent = main,
})

local title = create("TextLabel", {
	Size = UDim2.new(1, -110, 0, 44),
	Position = UDim2.new(0, 14, 0, 0),
	BackgroundTransparency = 1,
	Font = Enum.Font.GothamBold,
	TextSize = 14,
	Text = "ROYALITY HUB  v20",
	TextColor3 = Color3.fromRGB(255, 255, 255),
	TextXAlignment = Enum.TextXAlignment.Left,
	Parent = main,
})
create("TextLabel", {
	Size = UDim2.new(1, -110, 0, 14),
	Position = UDim2.new(0, 14, 0, 36),
	BackgroundTransparency = 1,
	Font = Enum.Font.Gotham,
	TextSize = 10,
	Text = "PC + celular",
	TextColor3 = Color3.fromRGB(140, 140, 155),
	TextXAlignment = Enum.TextXAlignment.Left,
	Parent = main,
})

local function miniBtn(text, x, color)
	local b = create("TextButton", {
		Size = UDim2.new(0, 26, 0, 26),
		Position = UDim2.new(1, x, 0, 9),
		BackgroundColor3 = color,
		BorderSizePixel = 0,
		Text = text,
		Font = Enum.Font.GothamBold,
		TextSize = 13,
		TextColor3 = Color3.fromRGB(255, 255, 255),
		AutoButtonColor = false,
		ZIndex = 3,
		Parent = main,
	})
	create("UICorner", { CornerRadius = UDim.new(0, 6), Parent = b })
	return b
end

local closeBtn = miniBtn("X", -34, Color3.fromRGB(70, 32, 36))
local minBtn = miniBtn("-", -66, Color3.fromRGB(38, 38, 46))

local list = create("ScrollingFrame", {
	Size = UDim2.new(1, -16, 1, -66),
	Position = UDim2.new(0, 8, 0, 56),
	BackgroundTransparency = 1,
	BorderSizePixel = 0,
	ScrollBarThickness = 4,
	ScrollBarImageColor3 = Color3.fromRGB(80, 80, 95),
	CanvasSize = UDim2.new(0, 0, 0, 0),
	Parent = main,
})
local layout = create("UIListLayout", {
	Padding = UDim.new(0, 6),
	SortOrder = Enum.SortOrder.LayoutOrder,
	Parent = list,
})
layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	list.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 12)
end)

--// ---- arrastar -------------------------------------------------
local dragging = false
local dragStart, startPos

local header = create("Frame", {
	Size = UDim2.new(1, 0, 0, 52),
	BackgroundTransparency = 1,
	Active = true,
	ZIndex = 2,
	Parent = main,
})
title.Parent = header

header.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = main.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

UIS.InputChanged:Connect(function(input)
	if not dragging then return end
	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then
		local d = input.Position - dragStart
		main.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + d.X,
			startPos.Y.Scale,
			startPos.Y.Offset + d.Y
		)
	end
end)

closeBtn.MouseButton1Click:Connect(function()
	gui:Destroy()
	notify("Royality", "Hub fechado. Execute o script de novo para abrir.", 3)
end)

local minimized = false
minBtn.MouseButton1Click:Connect(function()
	minimized = not minimized
	if minimized then
		main.Size = UDim2.new(0, 268, 0, 52)
		list.Visible = false
	else
		main.Size = UDim2.new(0, 268, 0, 440)
		list.Visible = true
	end
end)

--// ---- linhas --------------------------------------------------
local order = 0

local function headerRow(text)
	order = order + 1
	return create("TextLabel", {
		Size = UDim2.new(1, -6, 0, 24),
		BackgroundTransparency = 1,
		Text = text,
		Font = Enum.Font.GothamBold,
		TextSize = 11,
		TextColor3 = Color3.fromRGB(110, 170, 255),
		TextXAlignment = Enum.TextXAlignment.Left,
		LayoutOrder = order,
		Parent = list,
	})
end

local function baseRow(h)
	order = order + 1
	local row = create("TextButton", {
		Size = UDim2.new(1, -6, 0, h),
		BackgroundColor3 = Color3.fromRGB(34, 34, 42),
		BorderSizePixel = 0,
		Text = "",
		AutoButtonColor = false,
		LayoutOrder = order,
		Parent = list,
	})
	create("UICorner", { CornerRadius = UDim.new(0, 7), Parent = row })
	return row
end

local function rowLabel(row, text)
	return create("TextLabel", {
		Size = UDim2.new(1, -80, 1, 0),
		Position = UDim2.new(0, 10, 0, 0),
		BackgroundTransparency = 1,
		Text = text,
		Font = Enum.Font.GothamMedium,
		TextSize = 13,
		TextColor3 = Color3.fromRGB(235, 235, 240),
		TextXAlignment = Enum.TextXAlignment.Left,
		Parent = row,
	})
end

local function toggle(text, key, onChange)
	local row = baseRow(36)
	rowLabel(row, text)
	local pill = create("Frame", {
		Size = UDim2.new(0, 44, 0, 20),
		Position = UDim2.new(1, -54, 0.5, -10),
		BackgroundColor3 = Color3.fromRGB(70, 70, 82),
		BorderSizePixel = 0,
		Parent = row,
	})
	create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = pill })
	local knob = create("Frame", {
		Size = UDim2.new(0, 16, 0, 16),
		BackgroundColor3 = Color3.fromRGB(255, 255, 255),
		BorderSizePixel = 0,
		Parent = pill,
	})
	create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = knob })

	local function paint()
		local on = Config[key]
		knob.Position = on and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
		pill.BackgroundColor3 = on and Color3.fromRGB(0, 150, 100)
			or Color3.fromRGB(70, 70, 82)
	end

	row.MouseButton1Click:Connect(function()
		Config[key] = not Config[key]
		paint()
		if onChange then pcall(onChange, Config[key]) end
	end)
	paint()
end

local function stepper(text, key, step, min, max, onChange)
	local row = baseRow(36)
	rowLabel(row, text)
	local val = create("TextLabel", {
		Size = UDim2.new(0, 38, 0, 24),
		Position = UDim2.new(1, -100, 0.5, -12),
		BackgroundTransparency = 1,
		Text = tostring(Config[key]),
		Font = Enum.Font.GothamBold,
		TextSize = 13,
		TextColor3 = Color3.fromRGB(255, 255, 255),
		Parent = row,
	})
	local function smallBtn(txt, x)
		local b = create("TextButton", {
			Size = UDim2.new(0, 28, 0, 26),
			Position = UDim2.new(1, x, 0.5, -13),
			BackgroundColor3 = Color3.fromRGB(52, 52, 64),
			BorderSizePixel = 0,
			Text = txt,
			Font = Enum.Font.GothamBold,
			TextSize = 15,
			TextColor3 = Color3.fromRGB(255, 255, 255),
			AutoButtonColor = false,
			Parent = row,
		})
		create("UICorner", { CornerRadius = UDim.new(0, 6), Parent = b })
		return b
	end
	smallBtn("-", -64).MouseButton1Click:Connect(function()
		Config[key] = math.max(min, Config[key] - step)
		val.Text = tostring(Config[key])
		if onChange then pcall(onChange) end
	end)
	smallBtn("+", -32).MouseButton1Click:Connect(function()
		Config[key] = math.min(max, Config[key] + step)
		val.Text = tostring(Config[key])
		if onChange then pcall(onChange) end
	end)
end

local function action(text, fn)
	local row = baseRow(36)
	rowLabel(row, text)
	row.MouseButton1Click:Connect(function()
		local ok, err = pcall(fn)
		if not ok then warn("[Royality] " .. tostring(err)) end
	end)
end

--// ---- ESP -----------------------------------------------------
local function clearESP(char)
	local d = State.esp[char]
	if not d then return end
	State.esp[char] = nil
	if d.hl then d.hl:Destroy() end
	if d.bb then d.bb:Destroy() end
end

local function clearAllESP()
	for char in pairs(State.esp) do
		clearESP(char)
	end
end

local function applyESP(char)
	if not char or char == LP.Character or State.esp[char] then return end
	local head = char:FindFirstChild("Head")
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not head or not hum or hum.Health <= 0 then return end

	local isPlayer = Players:GetPlayerFromCharacter(char) ~= nil
	local color = isPlayer and Color3.fromRGB(255, 70, 70) or Color3.fromRGB(255, 175, 0)

	local hl = create("Highlight", {
		Name = "RoyalityHL",
		Adornee = char,
		FillColor = color,
		FillTransparency = 0.55,
		OutlineColor = Color3.fromRGB(255, 255, 255),
		OutlineTransparency = 0.25,
		Parent = char,
	})

	local bb = create("BillboardGui", {
		Name = "RoyalityBB",
		Adornee = head,
		Size = UDim2.new(0, 170, 0, 36),
		StudsOffset = Vector3.new(0, 2.4, 0),
		AlwaysOnTop = true,
		Parent = char,
	})

	local label = create("TextLabel", {
		Size = UDim2.new(1, 0, 1, 0),
		BackgroundTransparency = 1,
		Text = (isPlayer and char.Name or "BOT"),
		Font = Enum.Font.GothamBold,
		TextSize = 12,
		TextColor3 = color,
		TextStrokeTransparency = 0.45,
		Parent = bb,
	})

	local spike = create("Frame", {
		Size = UDim2.new(0, 3, 0, 46),
		Position = UDim2.new(0.5, -1, 0, -46),
		BackgroundColor3 = color,
		BorderSizePixel = 0,
		Visible = Config.Antenna,
		Parent = bb,
	})

	State.esp[char] = {
		hl = hl,
		bb = bb,
		label = label,
		spike = spike,
		hum = hum,
		name = isPlayer and char.Name or "BOT",
	}
end

--// ---- LOOT ESP ------------------------------------------------
local LOOT_WORDS = {
	"cash", "money", "coin", "gem", "diamond", "jewel", "gold", "bar",
	"key", "crate", "safe", "bag", "briefcase", "vault", "atm",
	"register", "chest", "loot", "artifact", "relic", "prize",
}

local function isLootName(name)
	local n = name:lower()
	for _, w in ipairs(LOOT_WORDS) do
		if n:find(w, 1, true) then return true end
	end
	return false
end

local function addLoot(obj)
	local part = obj:IsA("BasePart") and obj or obj:FindFirstChildWhichIsA("BasePart", true)
	if not part then return end
	local bb = create("BillboardGui", {
		Name = "RoyalityLoot",
		Adornee = part,
		Size = UDim2.new(0, 130, 0, 22),
		StudsOffset = Vector3.new(0, 1.8, 0),
		AlwaysOnTop = true,
		Parent = part,
	})
	create("TextLabel", {
		Size = UDim2.new(1, 0, 1, 0),
		BackgroundTransparency = 1,
		Text = obj.Name,
		Font = Enum.Font.GothamBold,
		TextSize = 11,
		TextColor3 = Color3.fromRGB(120, 255, 170),
		TextStrokeTransparency = 0.4,
		Parent = bb,
	})
	State.loot[obj] = bb
end

local function clearLoot()
	for obj, bb in pairs(State.loot) do
		if bb then pcall(function() bb:Destroy() end) end
		State.loot[obj] = nil
	end
end

local function scanLoot()
	if not Config.LootESP then return end
	local count = 0
	local myChar = LP.Character
	for _, d in ipairs(workspace:GetDescendants()) do
		if count >= 120 then break end
		if not State.loot[d] and (d:IsA("BasePart") or d:IsA("Model")) then
			local okChar = true
			if myChar and (d == myChar or d:IsDescendantOf(myChar)) then okChar = false end
			if okChar and not Players:GetPlayerFromCharacter(d) and isLootName(d.Name) then
				addLoot(d)
				count = count + 1
			end
		end
	end
end

--// ---- MIRA ----------------------------------------------------
local function canSee(char, part)
	if not Config.WallCheck then return true end
	local cam = workspace.CurrentCamera
	if not cam then return false end
	local params = RaycastParams.new()
	params.FilterType = Enum.RaycastFilterType.Exclude
	local ignore = { cam }
	if LP.Character then table.insert(ignore, LP.Character) end
	params.FilterDescendantsInstances = ignore
	params.IgnoreWater = true
	local origin = cam.CFrame.Position
	local res = workspace:Raycast(origin, part.Position - origin, params)
	if not res then return true end
	return res.Instance:IsDescendantOf(char)
end

local function pickTarget()
	local cam = workspace.CurrentCamera
	if not cam then return nil end
	local center = cam.ViewportSize / 2
	local best, bestD = nil, Config.FOV

	local function consider(char, part)
		if not part or not part.Parent then return end
		local pos, onScreen = cam:WorldToViewportPoint(part.Position)
		if not onScreen or pos.Z <= 0 then return end
		local d = (Vector2.new(pos.X, pos.Y) - center).Magnitude
		if d < bestD and canSee(char, part) then
			bestD = d
			best = { char = char, part = part }
		end
	end

	for _, p in ipairs(Players:GetPlayers()) do
		local c = p.Character
		if p ~= LP and c then
			local hum = c:FindFirstChildOfClass("Humanoid")
			if hum and hum.Health > 0 then
				consider(c, c:FindFirstChild("Head"))
			end
		end
	end

	for _, b in ipairs(State.bots) do
		if b.char.Parent and b.part.Parent then
			consider(b.char, b.part)
		end
	end

	return best
end

local function fireWeapon()
	local char = LP.Character
	if not char then return end
	for _, t in ipairs(char:GetChildren()) do
		if t:IsA("Tool") then
			pcall(function() t:Activate() end)
		end
	end
end

--// ---- VOO -----------------------------------------------------
local function stopFly()
	local char = LP.Character
	local hum = char and char:FindFirstChildOfClass("Humanoid")
	if hum then hum.PlatformStand = false end
	local root = char and char:FindFirstChild("HumanoidRootPart")
	if root then root.AssemblyLinearVelocity = Vector3.zero end
end

--// ---- ANTI-LAG / NEBLINA --------------------------------------
local savedFog = { Start = Lighting.FogStart, End = Lighting.FogEnd, Color = Lighting.FogColor }
local savedQuality = Lighting.EnvironmentDiffuseScale
local savedParticles = {}

local function setAntiLag(on)
	pcall(function()
		Lighting.GlobalShadows = not on
		Lighting.EnvironmentDiffuseScale = on and 0 or savedQuality
		Lighting.EnvironmentSpecularScale = on and 0 or 1
		if on then
			for _, obj in ipairs(Lighting:GetChildren()) do
				if obj:IsA("BloomEffect") or obj:IsA("BlurEffect")
					or obj:IsA("SunRaysEffect") or obj:IsA("DepthOfFieldEffect") then
					if savedParticles[obj] == nil then
						savedParticles[obj] = obj.Enabled
					end
					obj.Enabled = false
				end
			end
		else
			for obj, was in pairs(savedParticles) do
				if obj.Parent then obj.Enabled = was end
			end
			savedParticles = {}
		end
	end)
end

local function setNoFog(on)
	pcall(function()
		if on then
			Lighting.FogStart = 0
			Lighting.FogEnd = 100000
			Lighting.FogColor = Color3.fromRGB(200, 200, 200)
		else
			Lighting.FogStart = savedFog.Start
			Lighting.FogEnd = savedFog.End
			Lighting.FogColor = savedFog.Color
		end
	end)
end

--// ---- CIRCULO DE FOV ------------------------------------------
local circle = create("Frame", {
	Name = "RoyalityFOV",
	Size = UDim2.new(0, Config.FOV * 2, 0, Config.FOV * 2),
	Position = UDim2.new(0.5, -Config.FOV, 0.5, -Config.FOV),
	BackgroundTransparency = 1,
	BorderSizePixel = 0,
	Visible = false,
	Parent = gui,
})
create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = circle })
create("UIStroke", {
	Color = Color3.fromRGB(0, 255, 140),
	Thickness = 1.5,
	Transparency = 0.25,
	Parent = circle,
})

--// ---- TOQUE (celular) -----------------------------------------
local touchGui = nil
local aimBtn, upBtn, downBtn

if UIS.TouchEnabled then
	local root = nil
	pcall(function() root = (gethui and gethui()) or game:GetService("CoreGui") end)
	if not root then
		pcall(function() root = LP:WaitForChild("PlayerGui", 5) end)
	end

	if root then
		touchGui = create("ScreenGui", {
			Name = "RoyalityTouch",
			ResetOnSpawn = false,
			IgnoreGuiInset = true,
			DisplayOrder = 99999,
			Parent = root,
		})

		local function touchBtn(text, x, y, size)
			local b = create("TextButton", {
				Size = UDim2.new(0, size, 0, size),
				Position = UDim2.new(1, x, 1, y),
				BackgroundColor3 = Color3.fromRGB(30, 30, 36),
				BackgroundTransparency = 0.25,
				BorderSizePixel = 0,
				Text = text,
				Font = Enum.Font.GothamBold,
				TextSize = 15,
				TextColor3 = Color3.fromRGB(255, 255, 255),
				AutoButtonColor = false,
				Parent = touchGui,
			})
			create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = b })
			return b
		end

		aimBtn = touchBtn("AIM", -92, -118, 76)
		upBtn = touchBtn("^", -186, -172, 56)
		downBtn = touchBtn("v", -186, -96, 56)
		upBtn.Visible = false
		downBtn.Visible = false

		aimBtn.MouseButton1Down:Connect(function()
			State.firing = true
			aimBtn.BackgroundColor3 = Color3.fromRGB(0, 145, 95)
		end)
		aimBtn.MouseButton1Up:Connect(function()
			State.firing = false
			aimBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 36)
		end)

		upBtn.MouseButton1Down:Connect(function()
			State.flyUp = true
			upBtn.BackgroundColor3 = Color3.fromRGB(0, 115, 155)
		end)
		upBtn.MouseButton1Up:Connect(function()
			State.flyUp = false
			upBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 36)
		end)

		downBtn.MouseButton1Down:Connect(function()
			State.flyDown = true
			downBtn.BackgroundColor3 = Color3.fromRGB(0, 115, 155)
		end)
		downBtn.MouseButton1Up:Connect(function()
			State.flyDown = false
			downBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 36)
		end)
	end
end

--// ---- BOTOES DE COMBATE (PC) ----------------------------------
local function overPanel()
	if not gui.Parent or not main.Visible then return false end
	local mp = UIS:GetMouseLocation()
	local p = main.AbsolutePosition
	local s = main.AbsoluteSize
	return mp.X >= p.X and mp.X <= p.X + s.X
		and mp.Y >= p.Y and mp.Y <= p.Y + s.Y
end

UIS.InputBegan:Connect(function(input, processed)
	if processed then return end
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.KeyCode == Enum.KeyCode.ButtonR2 then
		if not overPanel() then
			State.firing = true
		end
	end
end)

UIS.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.KeyCode == Enum.KeyCode.ButtonR2 then
		State.firing = false
	end
end)

--// ---- CONSTRUCAO DO MENU --------------------------------------
headerRow("COMBATE")
toggle("Aimbot (segure o botao)", "Aimbot", function(v)
	circle.Visible = v
end)
toggle("Atirar Automatico", "AutoFire")
toggle("Wall Check", "WallCheck")
stepper("FOV da mira", "FOV", 10, 40, 400, function()
	circle.Size = UDim2.new(0, Config.FOV * 2, 0, Config.FOV * 2)
	circle.Position = UDim2.new(0.5, -Config.FOV, 0.5, -Config.FOV)
end)

headerRow("VIDA")
toggle("Kit Medico Automatico", "AutoMed", function(v)
	if v then State.lastMed = 0 end
end)
stepper("Curar com vida abaixo de", "MedAt", 5, 10, 95)

headerRow("VOO")
toggle("Voar", "Fly", function(v)
	if not v then stopFly() end
	if upBtn then
		upBtn.Visible = v
		downBtn.Visible = v
	end
end)
stepper("Velocidade do voo", "FlySpeed", 10, 20, 300)

headerRow("VISUAL")
toggle("ESP Jogadores e Bots", "ESP", function(v)
	if not v then clearAllESP() end
end)
toggle("Antena (marca acima)", "Antenna")
toggle("ESP de Loot", "LootESP", function(v)
	if not v then
		clearLoot()
	else
		task.spawn(scanLoot)
	end
end)

headerRow("OTIMIZACAO")
toggle("Anti-Lag", "AntiLag", function(v) setAntiLag(v) end)
toggle("Remover Neblina", "NoFog", function(v) setNoFog(v) end)

headerRow("SISTEMA")
action("Copiar JobId do servidor", function()
	if setclipboard then setclipboard(game.JobId) end
	notify("Royality", "JobId copiado: " .. tostring(game.JobId), 4)
end)
action("Reconectar neste servidor", function()
	TPS:TeleportToPlaceInstance(game.PlaceId, game.JobId, LP)
end)
action("Resetar tudo", function()
	for k, v in pairs(Config) do
		if type(v) == "boolean" then Config[k] = false end
	end
	Config.FOV = 150
	Config.MedAt = 60
	Config.FlySpeed = 60
	clearAllESP()
	clearLoot()
	stopFly()
	circle.Visible = false
	if upBtn then
		upBtn.Visible = false
		downBtn.Visible = false
	end
	setAntiLag(false)
	setNoFog(false)
	notify("Royality", "Tudo resetado.", 3)
end)

--// ---- LOOPS ---------------------------------------------------
local function refreshBots()
	if not (Config.ESP or Config.Aimbot) then return end
	local out = {}
	for _, d in ipairs(workspace:GetDescendants()) do
		if d:IsA("Humanoid") and d.Health > 0 then
			local c = d.Parent
			if c and not Players:GetPlayerFromCharacter(c) then
				local head = c:FindFirstChild("Head")
				if head then
					table.insert(out, { char = c, part = head })
				end
			end
		end
	end
	State.bots = out
end

task.spawn(function()
	while gui.Parent do
		task.wait(1)
		pcall(refreshBots)
	end
end)

task.spawn(function()
	while gui.Parent do
		task.wait(0.2)
		if Config.ESP then
			for _, p in ipairs(Players:GetPlayers()) do
				if p ~= LP then applyESP(p.Character) end
			end
			for _, b in ipairs(State.bots) do
				applyESP(b.char)
			end
			for char, d in pairs(State.esp) do
				if not char.Parent then
					clearESP(char)
				else
					local hum = d.hum
					if hum and d.label then
						d.label.Text = d.name .. "  " .. math.floor(hum.Health) .. "hp"
					end
					if d.spike then d.spike.Visible = Config.Antenna end
				end
			end
		elseif next(State.esp) then
			clearAllESP()
		end
	end
end)

task.spawn(function()
	while gui.Parent do
		task.wait(3)
		if Config.LootESP then
			pcall(scanLoot)
		elseif next(State.loot) then
			clearLoot()
		end
	end
end)

--// ---- KIT MEDICO ----------------------------------------------
local function isMed(tool)
	if not tool or not tool:IsA("Tool") then return false end
	local n = tool.Name:lower()
	return n:find("med", 1, true) or n:find("kit", 1, true)
		or n:find("heal", 1, true) or n:find("band", 1, true)
		or n:find("curar", 1, true) or n:find("vida", 1, true)
end

local function findMed(char)
	for _, t in ipairs(char:GetChildren()) do
		if isMed(t) then return t end
	end
	local bp = LP:FindFirstChildOfClass("Backpack")
	if bp then
		for _, t in ipairs(bp:GetChildren()) do
			if isMed(t) then return t end
		end
	end
	return nil
end

task.spawn(function()
	while gui.Parent do
		task.wait(0.5)
		if Config.AutoMed then
			local char = LP.Character
			local hum = char and char:FindFirstChildOfClass("Humanoid")
			if hum and hum.Health > 0 and hum.Health <= Config.MedAt
				and hum.MoveDirection.Magnitude < 0.1
				and os.clock() - State.lastMed > 1.8 then
				State.lastMed = os.clock()
				local tool = findMed(char)
				if tool then
					if tool.Parent ~= char then
						pcall(function() hum:EquipTool(tool) end)
						task.wait(0.3)
					end
					if tool.Parent == char then
						pcall(function() tool:Activate() end)
						task.wait(0.15)
						pcall(function() tool:Activate() end)
					end
				end
			end
		end
	end
end)

--// ---- LOOP PRINCIPAL ------------------------------------------
RunService.RenderStepped:Connect(function()
	if not gui.Parent then return end

	local cam = workspace.CurrentCamera
	local char = LP.Character
	local hum = char and char:FindFirstChildOfClass("Humanoid")
	local root = char and char:FindFirstChild("HumanoidRootPart")

	--// VOO
	if Config.Fly and hum and root then
		hum.PlatformStand = true
		local dir = Vector3.zero
		local mv = hum.MoveDirection
		if mv.Magnitude > 0.05 then
			dir = Vector3.new(mv.X, 0, mv.Z).Unit * Config.FlySpeed
		end
		if UIS:IsKeyDown(Enum.KeyCode.Space) or State.flyUp then
			dir = dir + Vector3.new(0, Config.FlySpeed, 0)
		end
		if UIS:IsKeyDown(Enum.KeyCode.LeftShift) or State.flyDown then
			dir = dir - Vector3.new(0, Config.FlySpeed, 0)
		end
		root.AssemblyLinearVelocity = dir
	end

	--// MIRA
	if Config.Aimbot and State.firing and cam and hum and hum.Health > 0 then
		circle.Visible = true
		local target = pickTarget()
		if target then
			cam.CFrame = CFrame.new(cam.CFrame.Position, target.part.Position)
			if Config.AutoFire then fireWeapon() end
		end
	else
		circle.Visible = Config.Aimbot
	end
end)

notify("Royality Hub v20", "Carregado. Segure o botao para mirar.", 5)
