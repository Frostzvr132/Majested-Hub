--[[
    WARNING: Heads up! This script has not been verified by ScriptBlox. Use at your own risk!
]]
print("[1] Iniciando o Majested Hub...")

local successLib, redzlib = pcall(function()
    return loadstring(game:HttpGet("https://raw.githubusercontent.com/minhdepzai-v/LibraryRobloc/refs/heads/main/RedzLibrary.lua"))()
end)

if not successLib then
    warn("ERRO: O seu executor não conseguiu baixar a biblioteca Redz UI. Verifique sua internet ou tente outro executor.")
    return
end

print("[2] Biblioteca da UI carregada com sucesso!")

local Window = redzlib:MakeWindow({
    Title = "Majested Hub : Muscle Legends",
    SubTitle = "by frostzvr",
    SaveFolder = "MajestedHub_MuscleLegends"
})

Window:AddMinimizeButton({
    Button = { Image = "rbxassetid://71014873973869", BackgroundTransparency = 0 },
    Corner = { CornerRadius = UDim.new(35, 1) },
})

print("[3] Janela criada!")

-- ==========================================
-- VARIÁVEIS GLOBAIS E SERVIÇOS
-- ==========================================
local player = game:GetService("Players").LocalPlayer
local replicatedStorage = game:GetService("ReplicatedStorage")
local runService = game:GetService("RunService")
local ts = game:GetService("TeleportService")

local Config = {
    AutoWeight = false, AutoPushups = false, AutoSitups = false,
    AutoHandstands = false, AutoPunch = false, AutoRock = false,
    AutoPunchRock = false, AutoCrystal = false, AutoRebirth = false,
    AutoKill = false, NoClip = false,
    
    SelectedRock = "King Muscle Rock", SelectedCrystal = "Blue Crystal",
    SelectedPlayer = "None", SelectedTeleport = "Spawn",
    
    WalkSpeed = 16, JumpPower = 50
}

local RockPositions = {
    ["King Muscle Rock"] = CFrame.new(-8967, 1, -6127),
    ["Legend Rock"] = CFrame.new(-2273, 13, -1004),
    ["Inferno Rock"] = CFrame.new(-6807, 13, -1277),
    ["Mythical Rock"] = CFrame.new(2283, 13, 856),
    ["Frost Rock"] = CFrame.new(-2585, 13, -425)
}

local TeleportLocations = {
    ["Spawn"] = CFrame.new(8, 4, 114), ["Arena"] = CFrame.new(-120, 4, -305),
    ["Tiny Island"] = CFrame.new(-4, 221, 1963), ["Frost Gym"] = CFrame.new(-2752, 125, -386),
    ["Mythical Gym"] = CFrame.new(2386, 139, 1094), ["Legends Gym"] = CFrame.new(4298, 1121, -3898),
    ["Inferno Gym"] = CFrame.new(-6917, 182, -1336), ["King Muscle Rock"] = CFrame.new(-8967, 1, -6127)
}

local function EquipTool(toolName)
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        if player.Character:FindFirstChild(toolName) then return end
        local tool = player.Backpack:FindFirstChild(toolName)
        if tool then player.Character.Humanoid:EquipTool(tool) end
    end
end

-- ==========================================
-- ABAS
-- ==========================================
print("[4] Criando abas...")
local TabStats = Window:MakeTab({ Name = "Stats", Icon = "bar-chart" })
local TabAutoFarm = Window:MakeTab({ Name = "Auto Farm", Icon = "swords" })
local TabRebirth = Window:MakeTab({ Name = "Rebirth", Icon = "refresh-cw" })
local TabGlitch = Window:MakeTab({ Name = "Glitch", Icon = "zap" })
local TabPets = Window:MakeTab({ Name = "Pets", Icon = "star" })
local TabTeleports = Window:MakeTab({ Name = "Teleports", Icon = "map-pin" })
local TabKill = Window:MakeTab({ Name = "Kill", Icon = "skull" })
local TabPlayer = Window:MakeTab({ Name = "Player", Icon = "user" })
local TabSettings = Window:MakeTab({ Name = "Settings", Icon = "settings" })

-- STATS
TabStats:AddDiscordInvite({
    Name = "Majested Hub",
    Description = "Join server",
    Logo = "rbxassetid://18751483361",
    Invite = "Link discord invite",
})

TabStats:AddButton({
    Name = "Copiar Discord",
    Callback = function()
        pcall(function() setclipboard("Link discord invite") end)
    end
})

TabStats:AddButton({
    Name = "Mostrar Info do Hub",
    Callback = function()
        print("Majested Hub : Muscle Legends | by frostzvr")
    end
})

-- AUTO FARM
TabAutoFarm:AddToggle({ Name = "Auto Weight", Default = false, Callback = function(Value) Config.AutoWeight = Value end })
TabAutoFarm:AddToggle({ Name = "Auto Punch", Default = false, Callback = function(Value) Config.AutoPunch = Value end })
TabAutoFarm:AddToggle({ Name = "Auto Pushups", Default = false, Callback = function(Value) Config.AutoPushups = Value end })
TabAutoFarm:AddToggle({ Name = "Auto Situps", Default = false, Callback = function(Value) Config.AutoSitups = Value end })
TabAutoFarm:AddToggle({ Name = "Auto Handstands", Default = false, Callback = function(Value) Config.AutoHandstands = Value end })

-- REBIRTH
TabRebirth:AddToggle({ Name = "Auto Rebirth", Default = false, Callback = function(Value) Config.AutoRebirth = Value end })
TabRebirth:AddButton({ Name = "Rebirth Manual", Callback = function() pcall(function() replicatedStorage.rEvents.rebirthRemote:InvokeServer("rebirthRequest") end) end })
TabRebirth:AddDropdown({ Name = "Modo Rebirth", Options = {"Normal", "Fast", "Safe"}, Default = "Normal", Callback = function(Value) Config.RebirthMode = Value end })

-- GLITCH
TabGlitch:AddDropdown({ Name = "Selecionar Pedra", Options = {"King Muscle Rock", "Legend Rock", "Inferno Rock", "Mythical Rock", "Frost Rock"}, Default = "King Muscle Rock", Callback = function(Value) Config.SelectedRock = Value end })
TabGlitch:AddToggle({ 
    Name = "Auto Rock (Teleport)", Default = false, 
    Callback = function(Value) 
        Config.AutoRock = Value 
        if Value and Config.SelectedRock then
            local targetCFrame = RockPositions[Config.SelectedRock]
            if targetCFrame and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                player.Character.HumanoidRootPart.CFrame = targetCFrame
            end
        end
    end 
})
TabGlitch:AddToggle({ Name = "Auto Punch Rock", Default = false, Callback = function(Value) Config.AutoPunchRock = Value end })

-- PETS
TabPets:AddDropdown({ Name = "Selecionar Crystal", Options = {"Blue Crystal", "Green Crystal", "Red Crystal", "Purple Crystal", "Yellow Crystal", "Legends Crystal", "Mythical Crystal", "Inferno Crystal", "Frost Crystal"}, Default = "Blue Crystal", Callback = function(Value) Config.SelectedCrystal = Value end })
TabPets:AddToggle({ Name = "Auto Open Crystal", Default = false, Callback = function(Value) Config.AutoCrystal = Value end })
TabPets:AddButton({ Name = "Equip Best Pets", Callback = function() print("Equip Best Pets") end })
TabPets:AddButton({ Name = "Unequip Pets", Callback = function() print("Unequip Pets") end })

-- TELEPORTS
TabTeleports:AddDropdown({ Name = "Selecionar Local", Options = {"Spawn", "Arena", "Tiny Island", "Frost Gym", "Mythical Gym", "Legends Gym", "Inferno Gym", "King Muscle Rock"}, Default = "Spawn", Callback = function(Value) Config.SelectedTeleport = Value end })
TabTeleports:AddButton({ 
    Name = "Teleportar", Callback = function() 
        local target = TeleportLocations[Config.SelectedTeleport]
        if target and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then player.Character.HumanoidRootPart.CFrame = target end
    end 
})
TabTeleports:AddButton({ Name = "Rejoin", Callback = function() ts:Teleport(game.PlaceId, player) end })
TabTeleports:AddButton({ Name = "Server Hop", Callback = function() ts:Teleport(game.PlaceId, player) end })

-- KILL
local playerList = {"None"}
for _, v in pairs(game.Players:GetPlayers()) do if v ~= player then table.insert(playerList, v.Name) end end

TabKill:AddDropdown({ Name = "Selecionar Player", Options = playerList, Default = "None", Callback = function(Value) Config.SelectedPlayer = Value end })
TabKill:AddToggle({ Name = "Auto Kill", Default = false, Callback = function(Value) Config.AutoKill = Value end })
TabKill:AddButton({ 
    Name = "Kill Player", Callback = function() 
        if Config.SelectedPlayer ~= "None" then
            local target = game.Players:FindFirstChild(Config.SelectedPlayer)
            if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and player.Character then
                player.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 2)
                EquipTool("Punch")
                player.muscleEvent:FireServer("punch", "leftHand")
                player.muscleEvent:FireServer("punch", "rightHand")
            end
        end
    end 
})
TabKill:AddButton({ Name = "Refresh Players", Callback = function() print("Lista atualizada apenas reabrindo o hub no momento") end })

-- PLAYER
TabPlayer:AddSlider({ Name = "WalkSpeed", MinValue = 16, MaxValue = 200, Default = 16, Callback = function(Value) Config.WalkSpeed = Value; if player.Character and player.Character:FindFirstChild("Humanoid") then player.Character.Humanoid.WalkSpeed = Value end end })
TabPlayer:AddSlider({ Name = "JumpPower", MinValue = 50, MaxValue = 250, Default = 50, Callback = function(Value) Config.JumpPower = Value; if player.Character and player.Character:FindFirstChild("Humanoid") then player.Character.Humanoid.JumpPower = Value end end })
TabPlayer:AddToggle({ Name = "NoClip", Default = false, Callback = function(Value) Config.NoClip = Value end })
TabPlayer:AddButton({ Name = "Reset Character", Callback = function() if player.Character and player.Character:FindFirstChild("Head") then player.Character.Head:Destroy() end end })

-- SETTINGS
TabSettings:AddToggle({ Name = "Anti Lag", Default = false, Callback = function(Value) print("AntiLag:", Value) end })
TabSettings:AddToggle({ Name = "Full Bright", Default = false, Callback = function(Value) print("FullBright:", Value) end })
TabSettings:AddButton({ Name = "Fechar Hub", Callback = function() print("Hub não pode ser destruído sem função nativa da UI") end })

print("[5] Iniciando Loops de AutoFarm...")

-- LOOPS
runService.Stepped:Connect(function()
    if Config.NoClip and player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid:ChangeState(11)
    end
end)

task.spawn(function()
    while task.wait() do
        pcall(function()
            if Config.AutoWeight then EquipTool("Weight") player.muscleEvent:FireServer("rep") end
            if Config.AutoSitups then EquipTool("Situps") player.muscleEvent:FireServer("rep") end
            if Config.AutoPushups then EquipTool("Pushups") player.muscleEvent:FireServer("rep") end
            if Config.AutoHandstands then EquipTool("Handstands") player.muscleEvent:FireServer("rep") end
            
            if Config.AutoPunch or Config.AutoPunchRock then
                EquipTool("Punch")
                player.muscleEvent:FireServer("punch", "leftHand")
                player.muscleEvent:FireServer("punch", "rightHand")
            end
            
            if Config.AutoKill and Config.SelectedPlayer ~= "None" then
                local target = game.Players:FindFirstChild(Config.SelectedPlayer)
                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and player.Character then
                    player.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
                    EquipTool("Punch")
                    player.muscleEvent:FireServer("punch", "leftHand")
                    player.muscleEvent:FireServer("punch", "rightHand")
                end
            end
            
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                if Config.WalkSpeed > 16 then player.Character.Humanoid.WalkSpeed = Config.WalkSpeed end
                if Config.JumpPower > 50 then player.Character.Humanoid.JumpPower = Config.JumpPower end
            end
        end)
    end
end)

task.spawn(function()
    while task.wait(0.5) do
        pcall(function()
            if Config.AutoRebirth then replicatedStorage.rEvents.rebirthRemote:InvokeServer("rebirthRequest") end
            if Config.AutoCrystal and Config.SelectedCrystal then replicatedStorage.rEvents.openCrystalRemote:InvokeServer("openCrystal", Config.SelectedCrystal) end
        end)
    end
end)

print("[6] TUDO CARREGADO COM SUCESSO!")
