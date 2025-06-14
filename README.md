-- 🌊 GokLeviathan | Ultimate Leviathan Hunter | By ChatGPT
-- ✅ Features: Kill Aura, Auto Heal, Drop Radar, No Fog, FPS Booster, Boat Fly

-- Services
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Lighting = game:GetService("Lighting")
local player = Players.LocalPlayer

-- Variables
local KillAura = false
local AutoAttack = false
local AutoHeal = false
local BoatMoving = false
local FPSBoost = false
local HealHeight = 150 -- Default safe height

-- GUI Setup
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "GokLeviathanGui"

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 500, 0, 400)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -200)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "🦑 GokLeviathan | Ultimate Leviathan Hunter"
Title.TextColor3 = Color3.new(1,1,1)
Title.BackgroundColor3 = Color3.fromRGB(20,20,20)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

-- Tabs
local Tabs = {}
local TabButtons = {}

local TabHolder = Instance.new("Frame", MainFrame)
TabHolder.Size = UDim2.new(0, 120, 1, -40)
TabHolder.Position = UDim2.new(0, 0, 0, 40)
TabHolder.BackgroundColor3 = Color3.fromRGB(25,25,25)

local ContentHolder = Instance.new("Frame", MainFrame)
ContentHolder.Size = UDim2.new(1, -120, 1, -40)
ContentHolder.Position = UDim2.new(0, 120, 0, 40)
ContentHolder.BackgroundColor3 = Color3.fromRGB(40,40,40)

local function CreateTab(name)
    local Tab = Instance.new("Frame")
    Tab.Size = UDim2.new(1,0,1,0)
    Tab.BackgroundTransparency = 1
    Tab.Visible = false
    Tab.Parent = ContentHolder
    Tabs[name] = Tab

    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -10, 0, 30)
    Button.Position = UDim2.new(0, 5, 0, (#TabButtons * 35) + 5)
    Button.Text = name
    Button.TextColor3 = Color3.new(1,1,1)
    Button.BackgroundColor3 = Color3.fromRGB(50,50,50)
    Button.Font = Enum.Font.GothamBold
    Button.TextSize = 14
    Button.Parent = TabHolder

    Button.MouseButton1Click:Connect(function()
        for _, t in pairs(Tabs) do t.Visible = false end
        Tab.Visible = true
    end)

    table.insert(TabButtons, Button)
    return Tab
end

-- Tabs
local MainTab = CreateTab("Main")
local TeleportTab = CreateTab("Teleport")
local VisualTab = CreateTab("Visual")
local SettingsTab = CreateTab("Settings")

----------------------------------------------------------------
-- 🔥 Main Tab

local function Button(parent, text, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 200, 0, 35)
    b.Position = UDim2.new(0, 10, 0, (#parent:GetChildren() * 40))
    b.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    b.TextColor3 = Color3.new(1,1,1)
    b.Text = text
    b.Font = Enum.Font.Gotham
    b.TextSize = 14
    b.Parent = parent
    b.MouseButton1Click:Connect(callback)
    return b
end

local function Slider(parent, label, min, max, value, callback)
    local txt = Instance.new("TextLabel")
    txt.Size = UDim2.new(0, 200, 0, 20)
    txt.Position = UDim2.new(0, 10, 0, (#parent:GetChildren() * 40))
    txt.Text = label .. ": " .. tostring(value)
    txt.TextColor3 = Color3.new(1,1,1)
    txt.BackgroundTransparency = 1
    txt.Font = Enum.Font.Gotham
    txt.TextSize = 13
    txt.Parent = parent

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 200, 0, 20)
    btn.Position = txt.Position + UDim2.new(0, 0, 0, 20)
    btn.BackgroundColor3 = Color3.fromRGB(90,90,90)
    btn.Text = "Change"
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 12
    btn.Parent = parent

    btn.MouseButton1Click:Connect(function()
        local val = tonumber(game:GetService("Players").LocalPlayer.PlayerGui:Prompt("Set "..label, tostring(value)))
        if val and val >= min and val <= max then
            value = val
            txt.Text = label .. ": " .. tostring(value)
            callback(value)
        end
    end)
end

Button(MainTab, "🔍 Find Leviathan", function()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name:lower():find("leviathan") then
            player.Character.HumanoidRootPart.CFrame = v.Tail.CFrame + Vector3.new(0,5,0)
            print("[GokLeviathan] Leviathan Found!")
            break
        end
    end
end)

Button(MainTab, "🚢 Boat Fly (Sea 6)", function()
    local boat = workspace.Boats:FindFirstChild(player.Name)
    if boat and boat.PrimaryPart then
        BoatMoving = true
        while BoatMoving do
            boat:SetPrimaryPartCFrame(boat.PrimaryPart.CFrame + Vector3.new(0, 1.5, 5))
            task.wait(0.05)
        end
    end
end)

Button(MainTab, "🛑 Stop Boat", function()
    BoatMoving = false
end)

Button(MainTab, "⚔️ Toggle Kill Aura", function()
    KillAura = not KillAura
    task.spawn(function()
        while KillAura do
            for _, npc in pairs(workspace:GetDescendants()) do
                if npc:IsA("Model") and npc:FindFirstChild("Humanoid") and npc ~= player.Character then
                    player.Character.HumanoidRootPart.CFrame = npc.HumanoidRootPart.CFrame * CFrame.new(0,0,3)
                    keypress(0x5A)
                    task.wait(0.1)
                    keyrelease(0x5A)
                end
            end
            task.wait(1)
        end
    end)
end)

Button(MainTab, "💥 Toggle Auto Attack", function()
    AutoAttack = not AutoAttack
    task.spawn(function()
        while AutoAttack do
            for _, v in pairs(workspace:GetDescendants()) do
                if v.Name:lower():find("leviathan") and v:FindFirstChild("Tail") then
                    player.Character.HumanoidRootPart.CFrame = v.Tail.CFrame + Vector3.new(0,0,5)
                    keypress(0x5A)
                    task.wait(0.1)
                    keyrelease(0x5A)
                end
            end
            task.wait(1)
        end
    end)
end)

Button(MainTab, "❤️ Toggle Auto Heal", function()
    AutoHeal = not AutoHeal
    task.spawn(function()
        while AutoHeal do
            local char = player.Character
            if char and char:FindFirstChild("Humanoid") then
                if char.Humanoid.Health <= char.Humanoid.MaxHealth * 0.4 then
                    char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame + Vector3.new(0, HealHeight, 0)
                    repeat task.wait(0.5) until char.Humanoid.Health >= char.Humanoid.MaxHealth * 0.8
                end
            end
            task.wait(1)
        end
    end)
end)

Slider(MainTab, "Safe Height", 50, 500, HealHeight, function(v)
    HealHeight = v
end)

----------------------------------------------------------------
-- 🔭 Visual Tab

Button(VisualTab, "🌫️ Toggle No Fog", function()
    Lighting.FogEnd = 100000
    Lighting.FogStart = 100000
    print("[GokLeviathan] Fog removed")
end)

Button(VisualTab, "🚀 Toggle FPS Booster", function()
    FPSBoost = not FPSBoost
    if FPSBoost then
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") then
                v.Material = Enum.Material.SmoothPlastic
                v.Reflectance = 0
            end
        end
        print("[GokLeviathan] FPS Booster Activated")
    end
end)

Button(VisualTab, "📡 Toggle Drop Radar", function()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name:lower():find("leviathanisland") then
            local billboard = Instance.new("BillboardGui", v)
            billboard.Size = UDim2.new(0,100,0,40)
            billboard.AlwaysOnTop = true
            local label = Instance.new("TextLabel", billboard)
            label.Size = UDim2.new(1,0,1,0)
            label.BackgroundTransparency = 1
            label.Text = "🏝️ Leviathan Island"
            label.TextColor3 = Color3.new(1,1,1)
            label.Font = Enum.Font.GothamBold
            label.TextScaled = true
        end
    end
end)

----------------------------------------------------------------
-- 🗺️ Teleport Tab (pode ser expandido)
Button(TeleportTab, "🦑 To Leviathan Island", function()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name:lower():find("leviathanisland") then
            player.Character.HumanoidRootPart.CFrame = v.CFrame + Vector3.new(0,5,0)
        end
    end
end)

----------------------------------------------------------------

print("[GokLeviathan] Loaded Successfully ✅")
