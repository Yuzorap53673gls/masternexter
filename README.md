                FOVCircle.Color = Color3.fromRGB(255, 50, 50)
            end
        else
            State.lockChar = nil
            State.lockPlayer = nil
            if FOVCircle then FOVCircle.Color = Color3.fromRGB(255, 50, 50) end
        end

        --// ---------------- AUTO FIRE ----------------
        if Config.AutoFireEnabled and State.lockPart and char
            and State.screenDist <= 75 then
            local delay = Config.RapidFireEnabled and 0.04 or 0.12
            if os.clock() - State.lastShot >= delay then
                State.lastShot = os.clock()
                fireTool(char)
            end
        end

        --// ---------------- AUTO SWITCH ----------------
        if Config.AutoSwitchEnabled and char and backpack and hum then
            local tool = char:FindFirstChildOfClass("Tool")
            if tool and not isMedTool(tool) then
                local holder = tool:FindFirstChild("Ammo")
                    or tool:FindFirstChild("Clip")
                    or tool:FindFirstChild("Bullets")
                    or tool:FindFirstChild("StoredAmmo")
                    or tool:FindFirstChild("Mag")

                local empty = false
                if holder and (tonumber(holder.Value) or 1) <= 0 then
                    empty = true
                else
                    local n = tonumber(
                        tool:GetAttribute("Ammo") or tool:GetAttribute("Clip")
                    )
                    if n and n <= 0 then empty = true end
                end

                if empty then
                    for _, other in ipairs(backpack:GetChildren()) do
                        if other:IsA("Tool") and other ~= tool
                            and not isMedTool(other) then
                            pcall(function() hum:EquipTool(other) end)
                            break
                        end
                    end
                end
            end
        end

        --// ---------------- VOO ----------------
        if Config.FlyEnabled and root and hum then
            hum.PlatformStand = true
            local moveDir = hum.MoveDirection
            local dir = Vector3.zero

            if moveDir.Magnitude > 0 then
                dir = Cam.CFrame.LookVector * moveDir.Magnitude
            end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then
                dir += Vector3.new(0, 1, 0)
            end
            if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then
                dir += Vector3.new(0, -1, 0)
            end

            root.AssemblyLinearVelocity = Vector3.zero
            root.CFrame += dir * Config.FlySpeed * dt
        end

        --// ---------------- KILL AURA ----------------
        if Config.KillAuraEnabled and root and char then
            if os.clock() - State.lastAura >= 0.1 then
                State.lastAura = os.clock()
                local hit = false

                for _, p in ipairs(Players:GetPlayers()) do
                    if p ~= LP and p.Character then
                        local pHum = p.Character:FindFirstChildOfClass("Humanoid")
                        local pRoot = p.Character:FindFirstChild("HumanoidRootPart")
                        if pRoot and isTargetAliveAndStanding(p.Character, pHum) then
                            local teamOk = not Config.TeamCheck
                                or not LP.Team or p.Team ~= LP.Team
                            if teamOk and (root.Position - pRoot.Position).Magnitude <= 20 then
                                hit = true
                            end
                        end
                    end
                end

                if not hit then
                    for botChar in pairs(State.bots) do
                        if botChar.Parent and botChar ~= LP.Character then
                            local bHum = botChar:FindFirstChildOfClass("Humanoid")
                            local bRoot = botChar:FindFirstChild("HumanoidRootPart")
                                or botChar:FindFirstChild("Head")
                            if bRoot and isTargetAliveAndStanding(botChar, bHum)
                                and (root.Position - bRoot.Position).Magnitude <= 20 then
                                hit = true
                                break
                            end
                        end
                    end
                end

                if hit then fireTool(char) end
            end
        end
    end)
end)

table.insert(getgenv().RoyalityConns, getgenv().RoyalityLoop)

toast(
    "Royality Hub v18",
    usedFallback and "Rodando no painel simples (Rayfield falhou)."
        or "Carregado. Aimbot só puxa quando você atira.",
    5
)



