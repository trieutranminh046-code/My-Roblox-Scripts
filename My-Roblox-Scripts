-- Delta Executor - Script Hack Blox Fruit (Tối ưu cho Delta)
-- Kiểm tra tương thích: Delta Executor v2.0+ 
-- Lưu ý: Script này sử dụng các hàm đặc thù của Delta

local player = game:GetService("Players").LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")
local hum = char:WaitForChild("Humanoid")

-- === CẤU HÌNH DELTA ===
local DeltaConfig = {
    FarmMode = "Level", -- Level | Boss | SeaBeast | Dungeon
    AutoFarm = true,
    AutoCollect = true,
    AutoStats = true,
    AutoSkill = true,
    AutoHeal = true,
    AutoTeleport = false, -- Tự động bay đến đảo tiếp theo khi hết quái
    HealThreshold = 35, -- % máu để tự hồi
    SkillDelay = 0.3,
    FarmRadius = 400,
    UseGodMode = false, -- Chế độ bất tử (có thể bị kick)
    UseSpeedHack = false, -- Tăng tốc di chuyển (nguy hiểm)
    SpeedMultiplier = 3
}

-- === GOD MODE CHO DELTA ===
if DeltaConfig.UseGodMode then
    game:GetService("RunService").Stepped:Connect(function()
        pcall(function()
            if hum then hum.Health = hum.MaxHealth end
        end)
    end)
end

-- === SPEED HACK ===
if DeltaConfig.UseSpeedHack then
    local oldSpeed = hum.WalkSpeed
    hum.WalkSpeed = oldSpeed * DeltaConfig.SpeedMultiplier
    hum.JumpPower = hum.JumpPower * 1.5
end

-- === AUTO FARM TỐI ƯU CHO DELTA ===
local function DeltaGetTargets()
    local targets = {}
    local ignoreList = {player.Name, "NPC_Shop", "NPC_Quest", "Boat", "Ship"}
    
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Model") and v:FindFirstChild("Humanoid") and v:FindFirstChild("HumanoidRootPart") then
            local humanoid = v:FindFirstChild("Humanoid")
            if humanoid and humanoid.Health > 0 and humanoid.MaxHealth > 10 then
                local isIgnored = false
                for _, name in pairs(ignoreList) do
                    if v.Name:find(name) then isIgnored = true end
                end
                if not isIgnored and v ~= char then
                    local dist = (hrp.Position - v.HumanoidRootPart.Position).Magnitude
                    if dist < DeltaConfig.FarmRadius then
                        table.insert(targets, {
                            Model = v,
                            Root = v.HumanoidRootPart,
                            Distance = dist,
                            Health = humanoid.Health
                        })
                    end
                end
            end
        end
    end
    
    table.sort(targets, function(a, b) return a.Distance < b.Distance end)
    return targets
end

local function DeltaCombo(target)
    if not target or not target.Root then return end
    
    -- Teleport đến mục tiêu (kiểu Delta)
    hrp.CFrame = CFrame.new(target.Root.Position + Vector3.new(0, 3, 0), target.Root.Position)
    wait(0.1)
    
    -- Combo tấn công: Click + Z + X + C + V (nếu có)
    local keys = {Enum.KeyCode.MouseButton1, Enum.KeyCode.Z, Enum.KeyCode.X, Enum.KeyCode.C}
    for _, key in pairs(keys) do
        if DeltaConfig.AutoSkill then
            game:GetService("VirtualInputManager"):SendKeyEvent(true, key, false, game)
            wait(DeltaConfig.SkillDelay)
            game:GetService("VirtualInputManager"):SendKeyEvent(false, key, false, game)
        end
    end
    
    -- Sử dụng V (Ultimate) nếu có
    if player:FindFirstChild("PlayerGui"):FindFirstChild("SkillUI") then
        game:GetService("VirtualInputManager"):SendKeyEvent(true, Enum.KeyCode.V, false, game)
        wait(0.5)
        game:GetService("VirtualInputManager"):SendKeyEvent(false, Enum.KeyCode.V, false, game)
    end
end

-- === AUTO HEAL DELTA ===
local function DeltaHeal()
    if not DeltaConfig.AutoHeal then return end
    if hum.Health / hum.MaxHealth * 100 < DeltaConfig.HealThreshold then
        -- Tìm vật phẩm hồi máu trong Backpack
        for _, item in pairs(player.Backpack:GetChildren()) do
            if item:IsA("Tool") and (item.Name:find("Potion") or item.Name:find("Food") or item.Name:find("Fruit")) then
                item.Parent = char
                wait(0.2)
                game:GetService("VirtualInputManager"):SendKeyEvent(true, Enum.KeyCode.MouseButton1, false, game)
                wait(0.5)
                game:GetService("VirtualInputManager"):SendKeyEvent(false, Enum.KeyCode.MouseButton1, false, game)
                item.Parent = player.Backpack
                break
            end
        end
    end
end

-- === AUTO COLLECT DELTA ===
local function DeltaCollect()
    if not DeltaConfig.AutoCollect then return end
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Tool") and v:FindFirstChild("Handle") then
            local dist = (hrp.Position - v.Handle.Position).Magnitude
            if dist < 30 then
                hrp.CFrame = CFrame.new(v.Handle.Position + Vector3.new(0, 2, 0))
                wait(0.2)
                fireclickdetector(v:FindFirstChildOfClass("ClickDetector"))
            end
        end
    end
end

-- === AUTO TELEPORT ĐẢO ===
local function DeltaTeleportToIsland()
    local islands = {"Jungle", "PirateVillage", "MarineBase", "Ice", "Desert", "Sky", "SeaOfTreats"}
    local currentLevel = tonumber(player:FindFirstChild("DataLevel") and player.DataLevel.Value or 0)
    local targetIsland = ""
    
    -- Xác định đảo dựa theo level
    if currentLevel < 100 then targetIsland = "Jungle"
    elseif currentLevel < 200 then targetIsland = "PirateVillage"
    elseif currentLevel < 400 then targetIsland = "MarineBase"
    elseif currentLevel < 600 then targetIsland = "Ice"
    elseif currentLevel < 900 then targetIsland = "Desert"
    elseif currentLevel < 1200 then targetIsland = "Sky"
    else targetIsland = "SeaOfTreats" end
    
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Model") and v.Name:find(targetIsland) and v:FindFirstChild("SpawnLocation") then
            hrp.CFrame = v.SpawnLocation.CFrame + Vector3.new(0, 15, 0)
            break
        end
    end
end

-- === MAIN LOOP DELTA ===
spawn(function()
    while task.wait(0.3) do
        pcall(function()
            if not DeltaConfig.AutoFarm then return end
            
            local targets = DeltaGetTargets()
            if #targets > 0 then
                DeltaCombo(targets[1])
            else
                if DeltaConfig.AutoTeleport then
                    DeltaTeleportToIsland()
                end
            end
            
            DeltaHeal()
            DeltaCollect()
            
            -- Auto Stats
            if DeltaConfig.AutoStats then
                local stats = player:FindFirstChild("PlayerStats") or player:FindFirstChild("Stats")
                if stats then
                    local points = stats:FindFirstChild("Points")
                    if points and points.Value > 0 then
                        for _, statName in pairs({"Melee", "Defense", "Sword", "Gun"}) do
                            if points.Value > 0 then
                                game:GetService("ReplicatedStorage").Remotes.BuyStat:InvokeServer(statName)
                                wait(0.1)
                            end
                        end
                    end
                end
            end
        end)
    end
end)

-- === ESP DELTA NÂNG CAO ===
local function DeltaESP()
    local espGui = Instance.new("ScreenGui", player.PlayerGui)
    espGui.Name = "DeltaESP"
    
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Model") and v:FindFirstChild("HumanoidRootPart") and v ~= char then
            local isEnemy = v:FindFirstChild("Humanoid") and v.Humanoid.MaxHealth > 50
            local isFruit = v:FindFirstChild("Handle") and v.Name:find("Fruit")
            local isPlayer = v:FindFirstChild("Humanoid") and v:IsA("Model") and v:FindFirstChild("DisplayName")
            
            if isEnemy or isFruit or isPlayer then
                local bill = Instance.new("BillboardGui", v)
                bill.Size = UDim2.new(0, 150, 0, 30)
                bill.AlwaysOnTop = true
                bill.StudsOffset = Vector3.new(0, 3, 0)
                
                local label = Instance.new("TextLabel", bill)
                label.Size = UDim2.new(1, 0, 1, 0)
                label.BackgroundTransparency = 1
                label.TextStrokeTransparency = 0
                label.TextStrokeColor3 = Color3.new(0, 0, 0)
                
                if isPlayer then
                    label.TextColor3 = Color3.new(0, 0.8, 1)
                    label.Text = "⚔ " .. v.DisplayName.Value .. " | " .. math.floor(v.Humanoid.Health) .. "HP"
                elseif isFruit then
                    label.TextColor3 = Color3.new(1, 0.7, 0)
                    label.Text = "🍎 " .. v.Name
                elseif isEnemy then
                    label.TextColor3 = Color3.new(1, 0.2, 0)
                    label.Text = "👹 " .. v.Name .. " | " .. math.floor(v.Humanoid.Health) .. "HP"
                end
                label.TextScaled = true
            end
        end
    end
end
DeltaESP()

-- === INJECTOR DELTA ===
print("Delta Blox Fruit Script Loaded - bản chỉnh sửa bởi Doi")
print("Farm Mode: " .. DeltaConfig.FarmMode)
print("God Mode: " .. tostring(DeltaConfig.UseGodMode))
print("Speed Hack: " .. tostring(DeltaConfig.UseSpeedHack))
