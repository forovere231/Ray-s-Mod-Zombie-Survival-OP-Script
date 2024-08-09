-- Main GUI Setup
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local leaderstats = player:WaitForChild("Leaderstats")

-- Create ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MainGUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Main Frame
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Position = UDim2.new(0, 10, 0.5, 50)
mainFrame.Size = UDim2.new(0, 300, 0, 401)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.AnchorPoint = Vector2.new(0, 0.5)
mainFrame.Parent = screenGui

-- Points Display
local pointsDisplay = Instance.new("TextLabel")
pointsDisplay.Name = "PointsDisplay"
pointsDisplay.Position = UDim2.new(0, 0, 0, -50)
pointsDisplay.Size = UDim2.new(0, 300, 0, 50)
pointsDisplay.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
pointsDisplay.TextColor3 = Color3.fromRGB(255, 255, 255)
pointsDisplay.Text = "Points: " .. (leaderstats:FindFirstChild("Points") and leaderstats.Points.Value or 0)
pointsDisplay.TextScaled = true
pointsDisplay.TextXAlignment = Enum.TextXAlignment.Left
pointsDisplay.Parent = mainFrame
-- Warning Frame
local warningFrame = Instance.new("Frame")
warningFrame.Name = "WarningFrame"
warningFrame.Size = UDim2.new(0, 300, 0, 100)
warningFrame.Position = UDim2.new(0.5, -150, 1, 20)
warningFrame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
warningFrame.Visible = false
warningFrame.Parent = mainFrame

local warningText = Instance.new("TextLabel")
warningText.Text = "Are you sure you want to delete the GUI?"
warningText.Size = UDim2.new(1, 0, 0.7, 0)
warningText.TextColor3 = Color3.fromRGB(255, 255, 255)
warningText.TextScaled = true
warningText.BackgroundTransparency = 1
warningText.Parent = warningFrame

local yesButton = Instance.new("TextButton")
yesButton.Text = "Yes"
yesButton.Size = UDim2.new(0.5, 0, 0.3, 0)
yesButton.Position = UDim2.new(0, 0, 0.7, 0)
yesButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
yesButton.TextColor3 = Color3.fromRGB(0, 0, 0)
yesButton.TextScaled = true
yesButton.Parent = warningFrame
yesButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

local noButton = Instance.new("TextButton")
noButton.Text = "No"
noButton.Size = UDim2.new(0.5, 0, 0.3, 0)
noButton.Position = UDim2.new(0.5, 0, 0.7, 0)
noButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
noButton.TextColor3 = Color3.fromRGB(255, 255, 255)
noButton.TextScaled = true
noButton.Parent = warningFrame
noButton.MouseButton1Click:Connect(function()
    warningFrame.Visible = false
end)

-- Category Buttons
local categories = {"Engineer", "Healer", "Support", "All", "Misc", "Zombie", "Player", "Map"}
local categoryButtons = {}

for i, cat in ipairs(categories) do
    local button = Instance.new("TextButton")
    button.Name = cat .. "Button"
    button.Position = UDim2.new(0, 0, 0, (i - 1) * 50)
    button.Size = UDim2.new(0, 100, 0, 50)
    button.Text = cat
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    button.TextScaled = true
    button.Parent = mainFrame
    categoryButtons[cat] = button
end

-- Panels
local panels = {}
for _, cat in ipairs(categories) do
    local panel = Instance.new("Frame")
    panel.Name = cat .. "Panel"
    panel.Position = UDim2.new(0, 110, 0, 0)
    panel.Size = UDim2.new(0, 180, 0, 300)
    panel.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    panel.Visible = cat == "All"
    panel.Parent = mainFrame
    panels[cat] = panel
end

-- Function to switch panels
local function switchPanel(selectedCategory)
    for cat, panel in pairs(panels) do
        panel.Visible = (cat == selectedCategory)
    end
end

-- Adding Button Click Events with Tooltips
local function addButtonToPanel(panel, name, position, size, text, tooltipText, func)
    local button = Instance.new("TextButton")
    button.Name = name .. "Button"
    button.Position = position
    button.Size = size
    button.Text = text
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    button.TextScaled = true
    button.Parent = panel
    
    -- Tooltip
    local tooltip = Instance.new("TextLabel")
    tooltip.Name = "Tooltip"
    tooltip.Size = UDim2.new(0, 200, 0, 50)
    tooltip.Position = UDim2.new(0, 0, 0, -50)
    tooltip.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    tooltip.TextColor3 = Color3.fromRGB(255, 255, 255)
    tooltip.Text = tooltipText
    tooltip.TextScaled = true
    tooltip.Visible = false
    tooltip.Parent = mainFrame
    
    button.MouseEnter:Connect(function()
        tooltip.Visible = true
    end)
    
    button.MouseLeave:Connect(function()
        tooltip.Visible = false
    end)
    
    button.MouseButton1Click:Connect(func)
end

-- All Panel Buttons
addButtonToPanel(panels["All"], "Heal", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "Heal (Must Have Health Vial)", "Heals the player if they have a Health Vial.", function()
    local healthVial = player.Backpack:FindFirstChild("Health Vial")
    if healthVial then
        healthVial.LocalScript.RemoteEvent:FireServer()
    end
end)

addButtonToPanel(panels["All"], "Spawn Small Ammo Box", UDim2.new(0, 0, 0, 50), UDim2.new(0, 160, 0, 50), "Spawn Small Ammo Box (Must have Smaller Ammo Box)", "Spawns a small ammo box if the player has a Smaller Ammo Box.", function()
    local smallerAmmoBox = player.Backpack:FindFirstChild("Smaller Ammo Box")
    if smallerAmmoBox then
        smallerAmmoBox.LocalScript.RemoteEvent:FireServer()
    end
end)

addButtonToPanel(panels["All"], "Infinite Health", UDim2.new(0, 0, 0, 100), UDim2.new(0, 160, 0, 50), "Infinite Health (Must have Health Vial, possible to die.)", "Grants infinite health while Health Vial is active. Possible to die.", function()
    while task.wait() do
        local healthVial = player.Backpack:FindFirstChild("Health Vial")
        if healthVial then
            healthVial.LocalScript.RemoteEvent:FireServer()
        end
    end
end)

-- Engineer Panel Buttons
addButtonToPanel(panels["Engineer"], "Spawn Turret", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "Spawn Turret (One Engineer or More must be alive.)", "Spawns a turret using the Wrench. (One Engineer or More must be alive.) ", function()
    -- Function to process each descendant
local function processDescendant(descendant)
    -- Check if the descendant is a player
    if descendant:IsA("Player") then
        local backpack = descendant:FindFirstChild("Backpack")
        if backpack then
            local wrench = backpack:FindFirstChild("Wrench")
            if wrench and wrench:FindFirstChild("placeturret") then
                wrench.placeturret:FireServer()
                print("Fired server event for player: " .. descendant.Name)
                return
            end
        end
    end
end

-- Get all descendants of game.Players
local function getAllDescendants()
    local players = game:GetService("Players")
    local descendants = players:GetDescendants()

    for _, descendant in ipairs(descendants) do
        processDescendant(descendant)
    end
end

-- Call the function
getAllDescendants()

end)

-- Healer Panel Buttons
addButtonToPanel(panels["Healer"], "Spawn Health Kit", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "Spawn Health Kit (One Healer or More must be alive.)", "Spawns a Health Kit if the player has one. (One Healer or More must be alive.)", function()
    -- Function to process each descendant
local function processDescendant(descendant)
    -- Check if the descendant is a player
    if descendant:IsA("Player") then
        local backpack = descendant:FindFirstChild("Backpack")
        if backpack then

            -- Check for Health Kit
            local healthKit = backpack:FindFirstChild("Health Kit")
            if healthKit and healthKit:FindFirstChild("LocalScript") and healthKit.LocalScript:FindFirstChild("RemoteEvent") then
                healthKit.LocalScript.RemoteEvent:FireServer()
                print("Fired server event for Health Kit for player: " .. descendant.Name)
            end
        end
    end
end

-- Get all descendants of game.Players
local function getAllDescendants()
    local players = game:GetService("Players")
    local descendants = players:GetDescendants()

    for _, descendant in ipairs(descendants) do
        processDescendant(descendant)
    end
end

-- Call the function
getAllDescendants()

end)

-- Support Panel Buttons
addButtonToPanel(panels["Support"], "Spawn Ammo Box", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "Spawn Ammo Box (One Support must be Alive.)", "Spawns an ammo box if the player has one. (One Support must be Alive.)", function()
   -- Function to process each descendant
local function processDescendant(descendant)
    -- Check if the descendant is a player
    if descendant:IsA("Player") then
        local backpack = descendant:FindFirstChild("Backpack")
        if backpack then
            -- Check for Wrench
        

            -- Check for Ammo Box (only if Wrench and Health Kit were not found)
            local ammoBox = backpack:FindFirstChild("Ammo Box")
            if ammoBox and ammoBox:FindFirstChild("LocalScript") and ammoBox.LocalScript:FindFirstChild("RemoteEvent") then
                ammoBox.LocalScript.RemoteEvent:FireServer()
                print("Fired server event for Ammo Box for player: " .. descendant.Name)
                return  -- Exit after finding and processing Ammo Box
            end
        end
    end
end

-- Get all descendants of game.Players
local function getAllDescendants()
    local players = game:GetService("Players")
    local descendants = players:GetDescendants()

    for _, descendant in ipairs(descendants) do
        processDescendant(descendant)
    end
end

-- Call the function
getAllDescendants()

end)

-- Zombie Panel Buttons
addButtonToPanel(panels["Zombie"], "AutoHit", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "AutoHit", "Automatically damages survivors and turrets if touched.", function()
    -- Script to rename models containing a ZombineTool tool to "player"

-- Function to rename models
local function renameModelsContainingTool(parent)
    -- Iterate through all descendants of the given parent
    for _, descendant in ipairs(parent:GetDescendants()) do
        -- Check if the descendant is a model
        if descendant:IsA("Model") then
            -- Check if the model contains a ZombineTool
            local hasTool = false
            for _, child in ipairs(descendant:GetChildren()) do
                if child:IsA("Tool") and child.Name == "ZombineTool" then
                    hasTool = true
                    break
                end
            end
            -- Rename the model if it contains the ZombineTool
            if hasTool then
                descendant.Name = "player"
                for _, v in pairs(game.Workspace:GetChildren()) do
                    if v.Name == "player" then
                        v.ZombineTool.LocalScript.reg:FireServer()
                        print("Fired.")
                    end
                end
            end
        end
    end
end

-- Run the function on Workspace
while task.wait(0.1) do
   if game.ReplicatedStorage.RoundInProgress.Value == false then
        break
    end
end
end)

-- Misc Panel Buttons
addButtonToPanel(panels["Misc"], "Delete GUI", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "Delete GUI", "Deletes the GUI after confirmation.", function()
    warningFrame.Visible = true
end)

addButtonToPanel(panels["Misc"], "Open Shop", UDim2.new(0, 0, 0, 50), UDim2.new(0, 160, 0, 50), "Open Shop", "Opens the shop interface.", function()
    local shopFrame = playerGui:FindFirstChild("Shop"):FindFirstChild("ShopFrame")
    if shopFrame then
        shopFrame.Visible = true
    end
end)

addButtonToPanel(panels["Misc"], "Close Shop", UDim2.new(0, 0, 0, 100), UDim2.new(0, 160, 0, 50), "Close Shop", "Closes the shop interface.", function()
    local shopFrame = playerGui:FindFirstChild("Shop"):FindFirstChild("ShopFrame")
    if shopFrame then
        shopFrame.Visible = false
    end
end)

addButtonToPanel(panels["Player"], "Flashlight On", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "Flashlight On", "Turns on flashlight.", function()
    game.ReplicatedStorage.FlashlightOn:FireServer()
end)

addButtonToPanel(panels["Player"], "Flashlight Off", UDim2.new(0, 0, 0, 50), UDim2.new(0, 160, 0, 50), "Flashlight Off", "Turns off flashlight.", function()
   game.ReplicatedStorage.FlashlightOff:FireServer()
end)

addButtonToPanel(panels["Player"], "Crouch On", UDim2.new(0, 0, 0, 100), UDim2.new(0, 160, 0, 50), "Crouch On", "Makes you crouch down.", function()
     game.ReplicatedStorage.CrawlOn:FireServer()
end)

addButtonToPanel(panels["Player"], "Crouch Off", UDim2.new(0, 0, 0, 150), UDim2.new(0, 160, 0, 50), "Crouch Off", "Makes you stand up.", function()
     game.ReplicatedStorage.CrawlOff:FireServer()
end)

addButtonToPanel(panels["Zombie"], "Nade", UDim2.new(0, 0, 0, 50), UDim2.new(0, 160, 0, 50), "Activate Nade (G)", "As a zombie, Activates a nade without being a Zombine.", function()
     game.ReplicatedStorage.Events.ActivateNade:FireServer()
end)

addButtonToPanel(panels["Map"], "", UDim2.new(0, 0, 0, 0), UDim2.new(0, 160, 0, 50), "Remove Anti-cheat Kill Bricks", "Removes Anti-cheat Kill Bricks so you can explore the map without dying.", function()
     -- Find the MAP folder
-- Get the "MAP" folder
local mapFolder = game.Workspace:FindFirstChild("MAP")

-- Check if the "MAP" folder exists
if mapFolder then
    -- Iterate through all descendants of the "MAP" folder
    for _, descendant in pairs(mapFolder:GetDescendants()) do
        -- Check if the descendant is a folder named "ledeath"
        if descendant:IsA("Folder") and descendant.Name == "ledeath" then
            -- Remove the "ledeath" folder
            descendant:Destroy()
        end
    end
else
    warn("The 'MAP' folder was not found in Workspace.")
end


end)



-- Add the Toggle Third Person button to the Misc panel


-- Category Button Click Events
for cat, button in pairs(categoryButtons) do
    button.MouseButton1Click:Connect(function()
        switchPanel(cat)
    end)
end

-- Warning Frame Close Button
yesButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

noButton.MouseButton1Click:Connect(function()
    warningFrame.Visible = false
end)

-- Mouse Lock/Unlock Feature


-- Function to rename models containing a ZombineTool
local function renameModelsContainingTool(parent)
    -- Iterate through all descendants of the given parent
    for _, descendant in ipairs(parent:GetDescendants()) do
        -- Check if the descendant is a model
        if descendant:IsA("Model") then
            -- Check if the model contains a ZombineTool
            local hasTool = false
            for _, child in ipairs(descendant:GetChildren()) do
                if child:IsA("Tool") and child.Name == "ZombineTool" then
                    hasTool = true
                    break
                end
            end
            -- Rename the model if it contains the ZombineTool
            if hasTool then
                descendant.Name = "player"
                -- Handle the renamed model
                for _, v in pairs(workspace:GetChildren()) do
                    if v:IsA("Model") and v.Name == "player" then
                        local zombineTool = v:FindFirstChild("ZombineTool")
                        if zombineTool and zombineTool:FindFirstChild("LocalScript") then
                            local localScript = zombineTool.LocalScript
                            if localScript:FindFirstChild("reg") then
                                localScript.reg:FireServer()
                            end
                        end
                    end
                end
            end
        end
    end
end

-- Create a function to handle key press
-- Function to handle key presses
local function onKeyPress(input, gameProcessed)
    if gameProcessed then
        return -- Ignore input if it's already processed by the game (e.g., typing in chat)
    end

    local player = game.Players.LocalPlayer
    local playerGui = player.PlayerGui

    if input.KeyCode == Enum.KeyCode.H then
        renameModelsContainingTool(workspace)
    elseif input.KeyCode == Enum.KeyCode.U then
        local wrench = player.Backpack:FindFirstChild("Wrench")
        if wrench then
            wrench.placeturret:FireServer()
        end
    elseif input.KeyCode == Enum.KeyCode.I then
        local healthKit = player.Backpack:FindFirstChild("Health Kit")
        if healthKit then
            healthKit.LocalScript.RemoteEvent:FireServer()
        end
    elseif input.KeyCode == Enum.KeyCode.O then
        local ammoBox = player.Backpack:FindFirstChild("Ammo Box")
        if ammoBox then
            ammoBox.LocalScript.RemoteEvent:FireServer()
        end
    elseif input.KeyCode == Enum.KeyCode.P then
        local smallerammobox = player.Backpack:FindFirstChild("Smaller Ammo Box")
        if smallerammobox then
            smallerammobox.LocalScript.RemoteEvent:FireServer()
        end
    elseif input.KeyCode == Enum.KeyCode.J then
        local shopFrame = playerGui:FindFirstChild("Shop"):FindFirstChild("ShopFrame")
        if shopFrame then
            shopFrame.Visible = true
        end
    elseif input.KeyCode == Enum.KeyCode.K then
        local shopFrame = playerGui:FindFirstChild("Shop"):FindFirstChild("ShopFrame")
        if shopFrame then
            shopFrame.Visible = false
        end
    end
end

-- Connect the key press function to UserInputService
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(onKeyPress)


local function updatePoint()
while task.wait() do
    pointsDisplay.Text = "Points: " .. (leaderstats:FindFirstChild("Points") and leaderstats.Points.Value or 0)
end
end

spawn(updatePoint())
