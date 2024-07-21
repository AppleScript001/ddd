local Material = loadstring(game:HttpGet("https://raw.githubusercontent.com/Kinlei/MaterialLua/master/Module.lua"))()
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Function to find and populate mob names
local function PopulateMobsTable()
    local Mobs_Table = {}
    for _, v in ipairs(workspace.Mob:GetDescendants()) do
        if v:IsA("Model") and v:FindFirstChild("HumanoidRootPart") and not table.find(Mobs_Table, v.Name) then
            table.insert(Mobs_Table, v.Name)
            table.sort(Mobs_Table)
        end
    end
    return Mobs_Table
end

local Mobs_Table = PopulateMobsTable()
local PlayerTP1

local X = Material.Load({
    Title = "🗡️ Rock Fruit 🎉",
    Style = 2,
    SizeX = 320,
    SizeY = 320,
    Theme = "Dark",
    ColorOverrides = {
        MainFrame = Color3.fromRGB(0, 9, 55)
    }
})

local Y = X.New({
    Title = "Main"
})

local ii = Y.Dropdown({
    Text = "Select Mobs!",
    Callback = function(t)
        PlayerTP1 = t
    end,
    Options = Mobs_Table
})

local function AutoFarm()
    _G.Attack = true
    while _G.Attack do
        task.wait(0.1)
        pcall(function()
            for _, v in ipairs(workspace.Mob:GetDescendants()) do
                if v.Name == PlayerTP1 and v:IsA("Model") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
                    local toolName = "Combat"  -- Replace with your tool name
                    local toolFound = false
                    
                    -- Check and equip the tool if not already equipped
                    for _, tool in pairs(LocalPlayer.Backpack:GetChildren()) do
                        if tool:IsA('Tool') and tool.Name == toolName then
                            tool.Parent = LocalPlayer.Character
                            toolFound = true
                            break
                        end
                    end
                    
                    -- Activate the tool
                    local combatTool = LocalPlayer.Character:FindFirstChild(toolName)
                    if combatTool then
                        combatTool:Activate()
                    end
                    
                    -- Teleport to the mob's position with offset and rotation
                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        LocalPlayer.Character.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0) * CFrame.Angles(math.rad(-90), math.rad(-10), math.rad(-4))
                    end
                    
                    wait()  -- Wait briefly before checking again
                end
            end
        end)
    end
end

local ToggleButton = Y.Toggle({
    Text = "Auto Farm/Mobs",
    Callback = function(Value)
        _G.Attack = Value
        if _G.Attack then
            AutoFarm()
        end
    end,
    Enabled = false  -- Change to true if you want it enabled by default
})

-- Optionally, you can call AutoFarm() immediately if Enabled is set to true
-- if ToggleButton.Enabled then
--     AutoFarm()
-- end
