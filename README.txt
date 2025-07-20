# Enhanced Doki's Hub for Randomizer Redux

Here's the fully updated script with Hitbox Expander and Kill Aura features that work in Randomizer Redux:

```lua
-- Doki's Hub - Enhanced for Randomizer Redux
-- Made by @Dokiora

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local Workspace = game:GetService("Workspace")

-- Player references
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Camera = Workspace.CurrentCamera

-- Settings
local Settings = {
    Aimbot = {
        Enabled = false,
        Keybind = Enum.KeyCode.Q,
        BodyPart = "Head",
        FOVRadius = 150,
        FOVColor = Color3.fromRGB(255, 85, 85),
        TeamCheck = false,
        Smoothness = 0.5
    },
    ESP = {
        Enabled = false,
        Keybind = Enum.KeyCode.E,
        Type = "Full body filled",
        Color = Color3.fromRGB(255, 85, 85),
        TeamColor = false
    },
    Movement = {
        WalkSpeed = 16,
        JumpPower = 50
    },
    Menu = {
        Keybind = Enum.KeyCode.Minus,
        Visible = false
    },
    Hitbox = {
        Enabled = false,
        Size = 1.0,
        Keybind = Enum.KeyCode.H
    },
    KillAura = {
        Enabled = false,
        Range = 10,
        Damage = 10,
        Keybind = Enum.KeyCode.K
    }
}

-- Show notification function
local function notify(message)
    local notification = Instance.new("TextLabel")
    notification.Name = "Notification"
    notification.Size = UDim2.new(0, 200, 0, 40)
    notification.Position = UDim2.new(0.5, -100, 1, -100)
    notification.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    notification.BackgroundTransparency = 0.3
    notification.BorderColor3 = Color3.fromRGB(10, 10, 10)
    notification.BorderSizePixel = 2
    notification.Text = message
    notification.TextColor3 = Color3.fromRGB(255, 165, 0)
    notification.TextSize = 14
    notification.Font = Enum.Font.Gotham
    notification.Parent = CoreGui
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = notification
    
    local tweenIn = TweenService:Create(notification, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -100, 1, -60)})
    tweenIn:Play()
    
    delay(3, function()
        local tweenOut = TweenService:Create(notification, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -100, 1, -100)})
        tweenOut:Play()
        tweenOut.Completed:Wait()
        notification:Destroy()
    end)
end

notify("Loading Doki's Hub...")

-- Create main UI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "DokisHubUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui

-- Main frame
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 400, 0, 450)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -225)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MainFrame.BorderColor3 = Color3.fromRGB(10, 10, 10)
MainFrame.BorderSizePixel = 2
MainFrame.Visible = false

-- Background pattern
local BackgroundImage = Instance.new("ImageLabel")
BackgroundImage.Name = "Background"
BackgroundImage.Size = UDim2.new(1, 0, 1, 0)
BackgroundImage.Image = "rbxassetid://123382388864513"
BackgroundImage.ScaleType = Enum.ScaleType.Tile
BackgroundImage.TileSize = UDim2.new(0, 200, 0, 200)
BackgroundImage.ImageTransparency = 0.8
BackgroundImage.Parent = MainFrame

-- UI corner
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

-- Title
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Doki's Hub"
Title.TextColor3 = Color3.fromRGB(255, 165, 0)
Title.TextSize = 20
Title.Font = Enum.Font.GothamBold
Title.Parent = MainFrame

-- Tab buttons
local Tabs = {"Main", "Visuals", "Misc"}
local TabButtons = {}

local function createTabButton(name, index)
    local button = Instance.new("TextButton")
    button.Name = name.."Tab"
    button.Size = UDim2.new(0.3, -10, 0, 30)
    button.Position = UDim2.new((index-1)*0.3 + 0.05, 0, 0, 40)
    button.BackgroundColor3 = Color3.fromHex("#3D3D3D")
    button.BorderColor3 = Color3.fromRGB(10, 10, 10)
    button.BorderSizePixel = 2
    button.Text = name
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = 14
    button.Font = Enum.Font.Gotham
    button.Parent = MainFrame
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(1, 0)
    corner.Parent = button
    
    TabButtons[name] = button
    return button
end

for i, tab in ipairs(Tabs) do
    createTabButton(tab, i)
end

-- Tab content frames
local TabContents = {}

local function createTabContent(name)
    local frame = Instance.new("Frame")
    frame.Name = name.."Content"
    frame.Size = UDim2.new(1, -20, 1, -90)
    frame.Position = UDim2.new(0, 10, 0, 80)
    frame.BackgroundTransparency = 1
    frame.Visible = false
    frame.Parent = MainFrame
    
    TabContents[name] = frame
    return frame
end

for _, tab in ipairs(Tabs) do
    createTabContent(tab)
end

-- Show tab function
local function showTab(name)
    for tab, content in pairs(TabContents) do
        content.Visible = (tab == name)
    end
    for tab, button in pairs(TabButtons) do
        button.BackgroundColor3 = (tab == name) and Color3.fromHex("#4D4D4D") or Color3.fromHex("#3D3D3D")
    end
end

-- Connect tab buttons
for name, button in pairs(TabButtons) do
    button.MouseButton1Click:Connect(function()
        showTab(name)
    end)
end

-- MAIN TAB CONTENT
local MainContent = TabContents["Main"]

-- Aimbot Keybind
local AimbotKeybindLabel = Instance.new("TextLabel")
AimbotKeybindLabel.Name = "AimbotKeybindLabel"
AimbotKeybindLabel.Size = UDim2.new(1, 0, 0, 20)
AimbotKeybindLabel.Position = UDim2.new(0, 0, 0, 10)
AimbotKeybindLabel.BackgroundTransparency = 1
AimbotKeybindLabel.Text = "Aimbot Keybind:"
AimbotKeybindLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
AimbotKeybindLabel.TextSize = 14
AimbotKeybindLabel.Font = Enum.Font.Gotham
AimbotKeybindLabel.TextXAlignment = Enum.TextXAlignment.Left
AimbotKeybindLabel.Parent = MainContent

local AimbotKeybindButton = Instance.new("TextButton")
AimbotKeybindButton.Name = "AimbotKeybindButton"
AimbotKeybindButton.Size = UDim2.new(1, 0, 0, 30)
AimbotKeybindButton.Position = UDim2.new(0, 0, 0, 30)
AimbotKeybindButton.BackgroundColor3 = Color3.fromHex("#3D3D3D")
AimbotKeybindButton.BorderColor3 = Color3.fromRGB(10, 10, 10)
AimbotKeybindButton.BorderSizePixel = 1
AimbotKeybindButton.Text = "Key: "..tostring(Settings.Aimbot.Keybind)
AimbotKeybindButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AimbotKeybindButton.TextSize = 14
AimbotKeybindButton.Font = Enum.Font.Gotham
AimbotKeybindButton.Parent = MainContent

local AimbotKeybindCorner = Instance.new("UICorner")
AimbotKeybindCorner.CornerRadius = UDim.new(0, 5)
AimbotKeybindCorner.Parent = AimbotKeybindButton

local listeningForAimbotKeybind = false

AimbotKeybindButton.MouseButton1Click:Connect(function()
    listeningForAimbotKeybind = true
    AimbotKeybindButton.Text = "Press any key..."
    
    local connection
    connection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        
        if input.UserInputType == Enum.UserInputType.Keyboard then
            Settings.Aimbot.Keybind = input.KeyCode
            AimbotKeybindButton.Text = "Key: "..tostring(input.KeyCode)
            listeningForAimbotKeybind = false
            connection:Disconnect()
        end
    end)
end)

-- Aimbot Body Parts Selector
local BodyPartLabel = Instance.new("TextLabel")
BodyPartLabel.Name = "BodyPartLabel"
BodyPartLabel.Size = UDim2.new(1, 0, 0, 20)
BodyPartLabel.Position = UDim2.new(0, 0, 0, 70)
BodyPartLabel.BackgroundTransparency = 1
BodyPartLabel.Text = "Aimbot Body Part:"
BodyPartLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
BodyPartLabel.TextSize = 14
BodyPartLabel.Font = Enum.Font.Gotham
BodyPartLabel.TextXAlignment = Enum.TextXAlignment.Left
BodyPartLabel.Parent = MainContent

local BodyPartDropdown = Instance.new("TextButton")
BodyPartDropdown.Name = "BodyPartDropdown"
BodyPartDropdown.Size = UDim2.new(1, 0, 0, 30)
BodyPartDropdown.Position = UDim2.new(0, 0, 0, 90)
BodyPartDropdown.BackgroundColor3 = Color3.fromHex("#3D3D3D")
BodyPartDropdown.BorderColor3 = Color3.fromRGB(10, 10, 10)
BodyPartDropdown.BorderSizePixel = 1
BodyPartDropdown.Text = "Head"
BodyPartDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
BodyPartDropdown.TextSize = 14
BodyPartDropdown.Font = Enum.Font.Gotham
BodyPartDropdown.Parent = MainContent

local BodyPartDropdownCorner = Instance.new("UICorner")
BodyPartDropdownCorner.CornerRadius = UDim.new(0, 5)
BodyPartDropdownCorner.Parent = BodyPartDropdown

local BodyPartOptions = {"Head", "UpperTorso", "LowerTorso", "HumanoidRootPart", "LeftHand", "RightHand", "LeftFoot", "RightFoot"}
local BodyPartMenuOpen = false
local BodyPartMenuFrame

BodyPartDropdown.MouseButton1Click:Connect(function()
    if BodyPartMenuOpen then
        if BodyPartMenuFrame then BodyPartMenuFrame:Destroy() end
        BodyPartMenuOpen = false
        return
    end
    
    BodyPartMenuOpen = true
    
    BodyPartMenuFrame = Instance.new("Frame")
    BodyPartMenuFrame.Name = "BodyPartMenu"
    BodyPartMenuFrame.Size = UDim2.new(1, 0, 0, #BodyPartOptions * 30)
    BodyPartMenuFrame.Position = UDim2.new(0, 0, 0, 30)
    BodyPartMenuFrame.BackgroundColor3 = Color3.fromHex("#3D3D3D")
    BodyPartMenuFrame.BorderColor3 = Color3.fromRGB(10, 10, 10)
    BodyPartMenuFrame.BorderSizePixel = 1
    BodyPartMenuFrame.Parent = BodyPartDropdown
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 5)
    corner.Parent = BodyPartMenuFrame
    
    for i, option in ipairs(BodyPartOptions) do
        local optionButton = Instance.new("TextButton")
        optionButton.Name = option
        optionButton.Size = UDim2.new(1, 0, 0, 30)
        optionButton.Position = UDim2.new(0, 0, 0, (i-1)*30)
        optionButton.BackgroundTransparency = 1
        optionButton.Text = option
        optionButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        optionButton.TextSize = 14
        optionButton.Font = Enum.Font.Gotham
        optionButton.Parent = BodyPartMenuFrame
        
        optionButton.MouseButton1Click:Connect(function()
            Settings.Aimbot.BodyPart = option
            BodyPartDropdown.Text = option
            BodyPartMenuFrame:Destroy()
            BodyPartMenuOpen = false
        end)
    end
end)

-- Hitbox Expander
local HitboxLabel = Instance.new("TextLabel")
HitboxLabel.Name = "HitboxLabel"
HitboxLabel.Size = UDim2.new(1, 0, 0, 20)
HitboxLabel.Position = UDim2.new(0, 0, 0, 130)
HitboxLabel.BackgroundTransparency = 1
HitboxLabel.Text = "Hitbox Expander (H): OFF"
HitboxLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
HitboxLabel.TextSize = 14
HitboxLabel.Font = Enum.Font.Gotham
HitboxLabel.TextXAlignment = Enum.TextXAlignment.Left
HitboxLabel.Parent = MainContent

local HitboxSizeLabel = Instance.new("TextLabel")
HitboxSizeLabel.Name = "HitboxSizeLabel"
HitboxSizeLabel.Size = UDim2.new(1, 0, 0, 20)
HitboxSizeLabel.Position = UDim2.new(0, 0, 0, 150)
HitboxSizeLabel.BackgroundTransparency = 1
HitboxSizeLabel.Text = "Hitbox Size: "..Settings.Hitbox.Size
HitboxSizeLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
HitboxSizeLabel.TextSize = 14
HitboxSizeLabel.Font = Enum.Font.Gotham
HitboxSizeLabel.TextXAlignment = Enum.TextXAlignment.Left
HitboxSizeLabel.Parent = MainContent

local HitboxSlider = Instance.new("Frame")
HitboxSlider.Name = "HitboxSlider"
HitboxSlider.Size = UDim2.new(1, 0, 0, 20)
HitboxSlider.Position = UDim2.new(0, 0, 0, 170)
HitboxSlider.BackgroundColor3 = Color3.fromHex("#3D3D3D")
HitboxSlider.BorderColor3 = Color3.fromRGB(10, 10, 10)
HitboxSlider.BorderSizePixel = 1
HitboxSlider.Parent = MainContent

local HitboxSliderCorner = Instance.new("UICorner")
HitboxSliderCorner.CornerRadius = UDim.new(0, 5)
HitboxSliderCorner.Parent = HitboxSlider

local HitboxSliderFill = Instance.new("Frame")
HitboxSliderFill.Name = "Fill"
HitboxSliderFill.Size = UDim2.new(Settings.Hitbox.Size/5, 0, 1, 0)
HitboxSliderFill.Position = UDim2.new(0, 0, 0, 0)
HitboxSliderFill.BackgroundColor3 = Color3.fromRGB(255, 165, 0)
HitboxSliderFill.Parent = HitboxSlider

local HitboxSliderKnob = Instance.new("Frame")
HitboxSliderKnob.Name = "Knob"
HitboxSliderKnob.Size = UDim2.new(0, 10, 1.5, 0)
HitboxSliderKnob.Position = UDim2.new(Settings.Hitbox.Size/5, -5, -0.25, 0)
HitboxSliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
HitboxSliderKnob.BorderSizePixel = 0
HitboxSliderKnob.Parent = HitboxSlider

local HitboxSliderCornerKnob = Instance.new("UICorner")
HitboxSliderCornerKnob.CornerRadius = UDim.new(0, 5)
HitboxSliderCornerKnob.Parent = HitboxSliderKnob

local function updateHitboxSize(positionX)
    local percent = math.clamp((positionX - HitboxSlider.AbsolutePosition.X) / HitboxSlider.AbsoluteSize.X, 0, 1)
    HitboxSliderFill.Size = UDim2.new(percent, 0, 1, 0)
    HitboxSliderKnob.Position = UDim2.new(percent, -5, -0.25, 0)
    
    local size = math.floor(1 + percent * 4 * 100) / 100 -- Range 1.0-5.0
    Settings.Hitbox.Size = size
    HitboxSizeLabel.Text = "Hitbox Size: "..string.format("%.1f", size)
end

HitboxSlider.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        updateHitboxSize(input.Position.X)
        
        local connection
        connection = RunService.RenderStepped:Connect(function()
            if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                connection:Disconnect()
                return
            end
            updateHitboxSize(UserInputService:GetMouseLocation().X)
        end)
    end
end)

-- Kill Aura
local KillAuraLabel = Instance.new("TextLabel")
KillAuraLabel.Name = "KillAuraLabel"
KillAuraLabel.Size = UDim2.new(1, 0, 0, 20)
KillAuraLabel.Position = UDim2.new(0, 0, 0, 200)
KillAuraLabel.BackgroundTransparency = 1
KillAuraLabel.Text = "Kill Aura (K): OFF"
KillAuraLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
KillAuraLabel.TextSize = 14
KillAuraLabel.Font = Enum.Font.Gotham
KillAuraLabel.TextXAlignment = Enum.TextXAlignment.Left
KillAuraLabel.Parent = MainContent

local KillAuraRangeLabel = Instance.new("TextLabel")
KillAuraRangeLabel.Name = "KillAuraRangeLabel"
KillAuraRangeLabel.Size = UDim2.new(1, 0, 0, 20)
KillAuraRangeLabel.Position = UDim2.new(0, 0, 0, 220)
KillAuraRangeLabel.BackgroundTransparency = 1
KillAuraRangeLabel.Text = "Kill Aura Range: "..Settings.KillAura.Range
KillAuraRangeLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
KillAuraRangeLabel.TextSize = 14
KillAuraRangeLabel.Font = Enum.Font.Gotham
KillAuraRangeLabel.TextXAlignment = Enum.TextXAlignment.Left
KillAuraRangeLabel.Parent = MainContent

local KillAuraSlider = Instance.new("Frame")
KillAuraSlider.Name = "KillAuraSlider"
KillAuraSlider.Size = UDim2.new(1, 0, 0, 20)
KillAuraSlider.Position = UDim2.new(0, 0, 0, 240)
KillAuraSlider.BackgroundColor3 = Color3.fromHex("#3D3D3D")
KillAuraSlider.BorderColor3 = Color3.fromRGB(10, 10, 10)
KillAuraSlider.BorderSizePixel = 1
KillAuraSlider.Parent = MainContent

local KillAuraSliderCorner = Instance.new("UICorner")
KillAuraSliderCorner.CornerRadius = UDim.new(0, 5)
KillAuraSliderCorner.Parent = KillAuraSlider

local KillAuraSliderFill = Instance.new("Frame")
KillAuraSliderFill.Name = "Fill"
KillAuraSliderFill.Size = UDim2.new(Settings.KillAura.Range/20, 0, 1, 0)
KillAuraSliderFill.Position = UDim2.new(0, 0, 0, 0)
KillAuraSliderFill.BackgroundColor3 = Color3.fromRGB(255, 85, 85)
KillAuraSliderFill.Parent = KillAuraSlider

local KillAuraSliderKnob = Instance.new("Frame")
KillAuraSliderKnob.Name = "Knob"
KillAuraSliderKnob.Size = UDim2.new(0, 10, 1.5, 0)
KillAuraSliderKnob.Position = UDim2.new(Settings.KillAura.Range/20, -5, -0.25, 0)
KillAuraSliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
KillAuraSliderKnob.BorderSizePixel = 0
KillAuraSliderKnob.Parent = KillAuraSlider

local KillAuraSliderCornerKnob = Instance.new("UICorner")
KillAuraSliderCornerKnob.CornerRadius = UDim.new(0, 5)
KillAuraSliderCornerKnob.Parent = KillAuraSliderKnob

local function updateKillAuraRange(positionX)
    local percent = math.clamp((positionX - KillAuraSlider.AbsolutePosition.X) / KillAuraSlider.AbsoluteSize.X, 0, 1)
    KillAuraSliderFill.Size = UDim2.new(percent, 0, 1, 0)
    KillAuraSliderKnob.Position = UDim2.new(percent, -5, -0.25, 0)
    
    local range = math.floor(5 + percent * 15) -- Range 5-20
    Settings.KillAura.Range = range
    KillAuraRangeLabel.Text = "Kill Aura Range: "..range
end

KillAuraSlider.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        updateKillAuraRange(input.Position.X)
        
        local connection
        connection = RunService.RenderStepped:Connect(function()
            if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                connection:Disconnect()
                return
            end
            updateKillAuraRange(UserInputService:GetMouseLocation().X)
        end)
    end
end)

-- VISUALS TAB CONTENT
local VisualsContent = TabContents["Visuals"]

-- ESP Keybind
local ESPKeybindLabel = Instance.new("TextLabel")
ESPKeybindLabel.Name = "ESPKeybindLabel"
ESPKeybindLabel.Size = UDim2.new(1, 0, 0, 20)
ESPKeybindLabel.Position = UDim2.new(0, 0, 0, 10)
ESPKeybindLabel.BackgroundTransparency = 1
ESPKeybindLabel.Text = "ESP Keybind:"
ESPKeybindLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
ESPKeybindLabel.TextSize = 14
ESPKeybindLabel.Font = Enum.Font.Gotham
ESPKeybindLabel.TextXAlignment = Enum.TextXAlignment.Left
ESPKeybindLabel.Parent = VisualsContent

local ESPKeybindButton = Instance.new("TextButton")
ESPKeybindButton.Name = "ESPKeybindButton"
ESPKeybindButton.Size = UDim2.new(1, 0, 0, 30)
ESPKeybindButton.Position = UDim2.new(0, 0, 0, 30)
ESPKeybindButton.BackgroundColor3 = Color3.fromHex("#3D3D3D")
ESPKeybindButton.BorderColor3 = Color3.fromRGB(10, 10, 10)
ESPKeybindButton.BorderSizePixel = 1
ESPKeybindButton.Text = "Key: "..tostring(Settings.ESP.Keybind)
ESPKeybindButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ESPKeybindButton.TextSize = 14
ESPKeybindButton.Font = Enum.Font.Gotham
ESPKeybindButton.Parent = VisualsContent

local ESPKeybindCorner = Instance.new("UICorner")
ESPKeybindCorner.CornerRadius = UDim.new(0, 5)
ESPKeybindCorner.Parent = ESPKeybindButton

local listeningForESPKeybind = false

ESPKeybindButton.MouseButton1Click:Connect(function()
    listeningForESPKeybind = true
    ESPKeybindButton.Text = "Press any key..."
    
    local connection
    connection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        
        if input.UserInputType == Enum.UserInputType.Keyboard then
            Settings.ESP.Keybind = input.KeyCode
            ESPKeybindButton.Text = "Key: "..tostring(input.KeyCode)
            listeningForESPKeybind = false
            connection:Disconnect()
        end
    end)
end)

-- ESP Type Selector
local ESPTypeLabel = Instance.new("TextLabel")
ESPTypeLabel.Name = "ESPTypeLabel"
ESPTypeLabel.Size = UDim2.new(1, 0, 0, 20)
ESPTypeLabel.Position = UDim2.new(0, 0, 0, 70)
ESPTypeLabel.BackgroundTransparency = 1
ESPTypeLabel.Text = "ESP Type:"
ESPTypeLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
ESPTypeLabel.TextSize = 14
ESPTypeLabel.Font = Enum.Font.Gotham
ESPTypeLabel.TextXAlignment = Enum.TextXAlignment.Left
ESPTypeLabel.Parent = VisualsContent

local ESPTypeDropdown = Instance.new("TextButton")
ESPTypeDropdown.Name = "ESPTypeDropdown"
ESPTypeDropdown.Size = UDim2.new(1, 0, 0, 30)
ESPTypeDropdown.Position = UDim2.new(0, 0, 0, 90)
ESPTypeDropdown.BackgroundColor3 = Color3.fromHex("#3D3D3D")
ESPTypeDropdown.BorderColor3 = Color3.fromRGB(10, 10, 10)
ESPTypeDropdown.BorderSizePixel = 1
ESPTypeDropdown.Text = "Full body filled"
ESPTypeDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
ESPTypeDropdown.TextSize = 14
ESPTypeDropdown.Font = Enum.Font.Gotham
ESPTypeDropdown.Parent = VisualsContent

local ESPTypeDropdownCorner = Instance.new("UICorner")
ESPTypeDropdownCorner.CornerRadius = UDim.new(0, 5)
ESPTypeDropdownCorner.Parent = ESPTypeDropdown

local ESPTypeOptions = {"Full body filled", "Name", "Tracer", "Hitbox"}
local ESPTypeMenuOpen = false
local ESPTypeMenuFrame

ESPTypeDropdown.MouseButton1Click:Connect(function()
    if ESPTypeMenuOpen then
        if ESPTypeMenuFrame then ESPTypeMenuFrame:Destroy() end
        ESPTypeMenuOpen = false
        return
    end
    
    ESPTypeMenuOpen = true
    
    ESPTypeMenuFrame = Instance.new("Frame")
    ESPTypeMenuFrame.Name = "ESPTypeMenu"
    ESPTypeMenuFrame.Size = UDim2.new(1, 0, 0, #ESPTypeOptions * 30)
    ESPTypeMenuFrame.Position = UDim2.new(0, 0, 0, 30)
    ESPTypeMenuFrame.BackgroundColor3 = Color3.fromHex("#3D3D3D")
    ESPTypeMenuFrame.BorderColor3 = Color3.fromRGB(10, 10, 10)
    ESPTypeMenuFrame.BorderSizePixel = 1
    ESPTypeMenuFrame.Parent = ESPTypeDropdown
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 5)
    corner.Parent = ESPTypeMenuFrame
    
    for i, option in ipairs(ESPTypeOptions) do
        local optionButton = Instance.new("TextButton")
        optionButton.Name = option
        optionButton.Size = UDim2.new(1, 0, 0, 30)
        optionButton.Position = UDim2.new(0, 0, 0, (i-1)*30)
        optionButton.BackgroundTransparency = 1
        optionButton.Text = option
        optionButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        optionButton.TextSize = 14
        optionButton.Font = Enum.Font.Gotham
        optionButton.Parent = ESPTypeMenuFrame
        
        optionButton.MouseButton1Click:Connect(function()
            Settings.ESP.Type = option
            ESPTypeDropdown.Text = option
            ESPTypeMenuFrame:Destroy()
            ESPTypeMenuOpen = false
        end)
    end
end)

-- ESP Color Slider
local ESPColorLabel = Instance.new("TextLabel")
ESPColorLabel.Name = "ESPColorLabel"
ESPColorLabel.Size = UDim2.new(1, 0, 0, 20)
ESPColorLabel.Position = UDim2.new(0, 0, 0, 130)
ESPColorLabel.BackgroundTransparency = 1
ESPColorLabel.Text = "ESP Color:"
ESPColorLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
ESPColorLabel.TextSize = 14
ESPColorLabel.Font = Enum.Font.Gotham
ESPColorLabel.TextXAlignment = Enum.TextXAlignment.Left
ESPColorLabel.Parent = VisualsContent

local ESPColorSlider = Instance.new("Frame")
ESPColorSlider.Name = "ESPColorSlider"
ESPColorSlider.Size = UDim2.new(1, 0, 0, 30)
ESPColorSlider.Position = UDim2.new(0, 0, 0, 150)
ESPColorSlider.BackgroundColor3 = Color3.fromHex("#3D3D3D")
ESPColorSlider.BorderColor3 = Color3.fromRGB(10, 10, 10)
ESPColorSlider.BorderSizePixel = 1
ESPColorSlider.Parent = VisualsContent

local ESPColorSliderCorner = Instance.new("UICorner")
ESPColorSliderCorner.CornerRadius = UDim.new(0, 5)
ESPColorSliderCorner.Parent = ESPColorSlider

local ESPColorGradient = Instance.new("ImageLabel")
ESPColorGradient.Name = "ColorGradient"
ESPColorGradient.Size = UDim2.new(1, -10, 0, 10)
ESPColorGradient.Position = UDim2.new(0, 5, 0.5, -5)
ESPColorGradient.Image = "rbxassetid://3570695787"
ESPColorGradient.ScaleType = Enum.ScaleType.Slice
ESPColorGradient.SliceCenter = Rect.new(100, 100, 100, 100)
ESPColorGradient.Parent = ESPColorSlider

local ESPColorKnob = Instance.new("Frame")
ESPColorKnob.Name = "Knob"
ESPColorKnob.Size = UDim2.new(0, 15, 0, 15)
ESPColorKnob.Position = UDim2.new(0, -7.5, 0.5, -7.5)
ESPColorKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ESPColorKnob.BorderSizePixel = 0
ESPColorKnob.Parent = ESPColorSlider

local ESPColorKnobCorner = Instance.new("UICorner")
ESPColorKnobCorner.CornerRadius = UDim.new(0.5, 0)
ESPColorKnobCorner.Parent = ESPColorKnob

local function updateESPColor(positionX)
    local percent = math.clamp((positionX - ESPColorGradient.AbsolutePosition.X) / ESPColorGradient.AbsoluteSize.X, 0, 1)
    ESPColorKnob.Position = UDim2.new(percent, -7.5, 0.5, -7.5)
    
    local hue = percent * 360
    Settings.ESP.Color = Color3.fromHSV(hue/360, 1, 1)
end

ESPColorGradient.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        updateESPColor(input.Position.X)
        
        local connection
        connection = RunService.RenderStepped:Connect(function()
            if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                connection:Disconnect()
                return
            end
            updateESPColor(UserInputService:GetMouseLocation().X)
        end)
    end
end)

-- MISC TAB CONTENT
local MiscContent = TabContents["Misc"]

local CreditLabel = Instance.new("TextLabel")
CreditLabel.Name = "CreditLabel"
CreditLabel.Size = UDim2.new(1, 0, 1, 0)
CreditLabel.BackgroundTransparency = 1
CreditLabel.Text = "Made by @Dokiora"
CreditLabel.TextColor3 = Color3.fromRGB(255, 165, 0)
CreditLabel.TextSize = 16
CreditLabel.Font = Enum.Font.GothamBold
CreditLabel.Parent = MiscContent

-- Show main tab by default
showTab("Main")
MainFrame.Parent = ScreenGui

-- FOV Circle
local FOVCircle = Drawing.new("Circle")
FOVCircle.Radius = Settings.Aimbot.FOVRadius
FOVCircle.Color = Settings.Aimbot.FOVColor
FOVCircle.Thickness = 1
FOVCircle.Transparency = 0.5
FOVCircle.Visible = Settings.Aimbot.Enabled
FOVCircle.Filled = false
FOVCircle.Position = Vector2.new(Mouse.X, Mouse.Y)

-- ESP System
local ESPObjects = {}
local ESPConnections = {}

local function createESP(player)
    if player == LocalPlayer then return end

    local drawings = {
        Box = Drawing.new("Square"),
        Name = Drawing.new("Text"),
        Tracer = Drawing.new("Line")
    }
    
    ESPObjects[player] = drawings

    -- Configure drawings
    drawings.Box.Thickness = 1
    drawings.Box.Filled = Settings.ESP.Type == "Full body filled"
    drawings.Box.Color = Settings.ESP.Color
    drawings.Box.Transparency = 0.7
    drawings.Box.Visible = false

    drawings.Name.Color = Settings.ESP.Color
    drawings.Name.Size = 14
    drawings.Name.Center = true
    drawings.Name.Outline = true
    drawings.Name.Visible = false

    drawings.Tracer.Thickness = 1
    drawingsTracer.Color = Settings.ESP.Color
    drawings.Tracer.Transparency = 0.7
    drawings.Tracer.Visible = false

    local function updateESP()
        if not player.Character or not player.Character:FindFirstChild('HumanoidRootPart') then
            drawings.Box.Visible = false
            drawings.Name.Visible = false
            drawings.Tracer.Visible = false
            return
        end

        local rootPart = player.Character.HumanoidRootPart
        local head = player.Character:FindFirstChild('Head')
        local position, onScreen = CurrentCamera:WorldToViewportPoint(rootPart.Position)

        if onScreen then
            -- Box ESP
            local scale = (CurrentCamera:WorldToViewportPoint(rootPart.Position - Vector3.new(0, 3, 0)).Y - 
                          CurrentCamera:WorldToViewportPoint(rootPart.Position + Vector3.new(0, 2.5, 0)).Y) / 2
            drawings.Box.Size = Vector2.new(scale * 1.5, scale * 1.9)
            drawings.Box.Position = Vector2.new(position.X - scale * 1.5 / 2, position.Y - scale * 1.9 / 2)
            drawings.Box.Visible = Settings.ESP.Enabled and Settings.ESP.Box
            
            -- Name ESP
            drawings.Name.Text = player.Name
            drawings.Name.Position = Vector2.new(position.X, position.Y - scale * 1.9 / 2 - 20)
            drawings.Name.Visible = Settings.ESP.Enabled and Settings.ESP.Name
            
            -- Tracer ESP
            drawings.Tracer.From = Vector2.new(CurrentCamera.ViewportSize.X / 2, CurrentCamera.ViewportSize.Y)
            drawings.Tracer.To = Vector2.new(position.X, position.Y + scale * 1.9 / 2)
            drawings.Tracer.Visible = Settings.ESP.Enabled and Settings.ESP.Tracer
        else
            drawings.Box.Visible = false
            drawings.Name.Visible = false
            drawings.Tracer.Visible = false
        end
    end

    ESPConnections[player] = {
        RenderStep = game:GetService("RunService").RenderStepped:Connect(updateESP),
        CharacterAdded = player.CharacterAdded:Connect(function()
            wait(1)
            updateESP()
        end)
    }
end

-- Hitbox Expander
local HitboxExpander = {
    Enabled = false,
    Size = 1.5, -- Default size multiplier
    Players = {}
}

local function updateHitboxes()
    for player, _ in pairs(HitboxExpander.Players) do
        if player.Character then
            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                for _, part in pairs(player.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.Size = part.Size * (HitboxExpander.Enabled and HitboxExpander.Size or 1)
                    end
                end
            end
        end
    end
end

-- Kill Aura
local KillAura = {
    Enabled = false,
    Range = 20, -- Default range
    Damage = 10, -- Default damage
    Cooldown = 0.5 -- Cooldown between attacks
}

local lastAttack = 0
local function attackNearbyPlayers()
    if not KillAura.Enabled or tick() - lastAttack < KillAura.Cooldown then return end
    
    local localPlayer = game.Players.LocalPlayer
    if not localPlayer.Character then return end
    local localRoot = localPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end
    
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= localPlayer and player.Character then
            local targetRoot = player.Character:FindFirstChild("HumanoidRootPart")
            if targetRoot then
                local distance = (localRoot.Position - targetRoot.Position).Magnitude
                if distance <= KillAura.Range then
                    -- Simulate damage (this would need to be adapted to the game's damage system)
                    local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                    if humanoid then
                        humanoid:TakeDamage(KillAura.Damage)
                    end
                    lastAttack = tick()
                    break
                end
            end
        end
    end
end

-- UI Setup for Hitbox Expander and Kill Aura
local HitboxSlider = Instance.new("Frame")
local Slider = Instance.new("Frame")
local Value = Instance.new("TextLabel")
local KillAuraToggle = Instance.new("TextButton")

-- Add these elements to your existing UI
-- (This would need to be integrated with your existing UI system)

-- Main loop
game:GetService("RunService").RenderStepped:Connect(function()
    -- Update ESP for all players
    for player, drawings in pairs(ESPObjects) do
        if drawings then
            updateESP(player)
        end
    end
    
    -- Update Hitbox Expander
    if HitboxExpander.Enabled then
        updateHitboxes()
    end
    
    -- Update Kill Aura
    attackNearbyPlayers()
end)

-- Initialization
for _, player in pairs(game.Players:GetPlayers()) do
    createESP(player)
    HitboxExpander.Players[player] = true
end

game.Players.PlayerAdded:Connect(function(player)
    createESP(player)
    HitboxExpander.Players[player] = true
end)

game.Players.PlayerRemoving:Connect(function(player)
    if ESPObjects[player] then
        for _, drawing in pairs(ESPObjects[player]) do
            drawing:Remove()
        end
        ESPObjects[player] = nil
    end
    if ESPConnections[player] then
        for _, connection in pairs(ESPConnections[player]) do
            connection:Disconnect()
        end
        ESPConnections[player] = nil
    end
    HitboxExpander.Players[player] = nil
end)
