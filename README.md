-- Doki's Hub - Enhanced ESP & Aimbot UI
-- Made by @Dokiora

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")

-- Player references
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Camera = workspace.CurrentCamera

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
    }
}

-- Create main UI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "DokisHubUI"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false

-- Main frame
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 400, 0, 400)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -200)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MainFrame.BorderColor3 = Color3.fromRGB(40, 40, 40)
MainFrame.BorderSizePixel = 2

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
Title.TextColor3 = Color3.fromRGB(255, 165, 0) -- Orange
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
    button.BorderColor3 = Color3.fromRGB(20, 20, 20)
    button.BorderSizePixel = 2
    button.Text = name
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = 14
    button.Font = Enum.Font.Gotham
    button.Parent = MainFrame
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(1, 0) -- Pill shape
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
AimbotKeybindButton.BorderColor3 = Color3.fromRGB(20, 20, 20)
AimbotKeybindButton.BorderSizePixel = 1
AimbotKeybindButton.Text = "Key: " .. tostring(Settings.Aimbot.Keybind)
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
            AimbotKeybindButton.Text = "Key: " .. tostring(input.KeyCode)
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
BodyPartDropdown.BorderColor3 = Color3.fromRGB(20, 20, 20)
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
    BodyPartMenuFrame.BorderColor3 = Color3.fromRGB(20, 20, 20)
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

-- WalkSpeed Slider
local WalkSpeedLabel = Instance.new("TextLabel")
WalkSpeedLabel.Name = "WalkSpeedLabel"
WalkSpeedLabel.Size = UDim2.new(1, 0, 0, 20)
WalkSpeedLabel.Position = UDim2.new(0, 0, 0, 130)
WalkSpeedLabel.BackgroundTransparency = 1
WalkSpeedLabel.Text = "WalkSpeed: " .. Settings.Movement.WalkSpeed
WalkSpeedLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
WalkSpeedLabel.TextSize = 14
WalkSpeedLabel.Font = Enum.Font.Gotham
WalkSpeedLabel.TextXAlignment = Enum.TextXAlignment.Left
WalkSpeedLabel.Parent = MainContent

local WalkSpeedSlider = Instance.new("Frame")
WalkSpeedSlider.Name = "WalkSpeedSlider"
WalkSpeedSlider.Size = UDim2.new(1, 0, 0, 20)
WalkSpeedSlider.Position = UDim2.new(0, 0, 0, 150)
WalkSpeedSlider.BackgroundColor3 = Color3.fromHex("#3D3D3D")
WalkSpeedSlider.BorderColor3 = Color3.fromRGB(20, 20, 20)
WalkSpeedSlider.BorderSizePixel = 1
WalkSpeedSlider.Parent = MainContent

local WalkSpeedSliderCorner = Instance.new("UICorner")
WalkSpeedSliderCorner.CornerRadius = UDim.new(0, 5)
WalkSpeedSliderCorner.Parent = WalkSpeedSlider

local WalkSpeedSliderFill = Instance.new("Frame")
WalkSpeedSliderFill.Name = "Fill"
WalkSpeedSliderFill.Size = UDim2.new(0.16, 0, 1, 0)
WalkSpeedSliderFill.Position = UDim2.new(0, 0, 0, 0)
WalkSpeedSliderFill.BackgroundColor3 = Color3.fromRGB(255, 165, 0) -- Orange
WalkSpeedSliderFill.Parent = WalkSpeedSlider

local WalkSpeedSliderKnob = Instance.new("Frame")
WalkSpeedSliderKnob.Name = "Knob"
WalkSpeedSliderKnob.Size = UDim2.new(0, 10, 1.5, 0)
WalkSpeedSliderKnob.Position = UDim2.new(0.16, -5, -0.25, 0)
WalkSpeedSliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
WalkSpeedSliderKnob.BorderSizePixel = 0
WalkSpeedSliderKnob.Parent = WalkSpeedSlider

local WalkSpeedSliderCornerKnob = Instance.new("UICorner")
WalkSpeedSliderCornerKnob.CornerRadius = UDim.new(0, 5)
WalkSpeedSliderCornerKnob.Parent = WalkSpeedSliderKnob

local function updateWalkSpeed(positionX)
    local percent = math.clamp((positionX - WalkSpeedSlider.AbsolutePosition.X) / WalkSpeedSlider.AbsoluteSize.X, 0, 1)
    WalkSpeedSliderFill.Size = UDim2.new(percent, 0, 1, 0)
    WalkSpeedSliderKnob.Position = UDim2.new(percent, -5, -0.25, 0)
    
    local walkSpeed = math.floor(16 + percent * (1000 - 16))
    Settings.Movement.WalkSpeed = walkSpeed
    WalkSpeedLabel.Text = "WalkSpeed: " .. walkSpeed
    
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = walkSpeed
    end
end

WalkSpeedSlider.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        updateWalkSpeed(input.Position.X)
        
        local connection
        connection = RunService.RenderStepped:Connect(function()
            if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                connection:Disconnect()
                return
            end
            updateWalkSpeed(UserInputService:GetMouseLocation().X)
        end)
    end
end)

-- JumpPower Slider
local JumpPowerLabel = Instance.new("TextLabel")
JumpPowerLabel.Name = "JumpPowerLabel"
JumpPowerLabel.Size = UDim2.new(1, 0, 0, 20)
JumpPowerLabel.Position = UDim2.new(0, 0, 0, 180)
JumpPowerLabel.BackgroundTransparency = 1
JumpPowerLabel.Text = "JumpPower: " .. Settings.Movement.JumpPower
JumpPowerLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
JumpPowerLabel.TextSize = 14
JumpPowerLabel.Font = Enum.Font.Gotham
JumpPowerLabel.TextXAlignment = Enum.TextXAlignment.Left
JumpPowerLabel.Parent = MainContent

local JumpPowerSlider = Instance.new("Frame")
JumpPowerSlider.Name = "JumpPowerSlider"
JumpPowerSlider.Size = UDim2.new(1, 0, 0, 20)
JumpPowerSlider.Position = UDim2.new(0, 0, 0, 200)
JumpPowerSlider.BackgroundColor3 = Color3.fromHex("#3D3D3D")
JumpPowerSlider.BorderColor3 = Color3.fromRGB(20, 20, 20)
JumpPowerSlider.BorderSizePixel = 1
JumpPowerSlider.Parent = MainContent

local JumpPowerSliderCorner = Instance.new("UICorner")
JumpPowerSliderCorner.CornerRadius = UDim.new(0, 5)
JumpPowerSliderCorner.Parent = JumpPowerSlider

local JumpPowerSliderFill = Instance.new("Frame")
JumpPowerSliderFill.Name = "Fill"
JumpPowerSliderFill.Size = UDim2.new(0.05, 0, 1, 0)
JumpPowerSliderFill.Position = UDim2.new(0, 0, 0, 0)
JumpPowerSliderFill.BackgroundColor3 = Color3.fromRGB(255, 165, 0) -- Orange
JumpPowerSliderFill.Parent = JumpPowerSlider

local JumpPowerSliderKnob = Instance.new("Frame")
JumpPowerSliderKnob.Name = "Knob"
JumpPowerSliderKnob.Size = UDim2.new(0, 10, 1.5, 0)
JumpPowerSliderKnob.Position = UDim2.new(0.05, -5, -0.25, 0)
JumpPowerSliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
JumpPowerSliderKnob.BorderSizePixel = 0
JumpPowerSliderKnob.Parent = JumpPowerSlider

local JumpPowerSliderCornerKnob = Instance.new("UICorner")
JumpPowerSliderCornerKnob.CornerRadius = UDim.new(0, 5)
JumpPowerSliderCornerKnob.Parent = JumpPowerSliderKnob

local function updateJumpPower(positionX)
    local percent = math.clamp((positionX - JumpPowerSlider.AbsolutePosition.X) / JumpPowerSlider.AbsoluteSize.X, 0, 1)
    JumpPowerSliderFill.Size = UDim2.new(percent, 0, 1, 0)
    JumpPowerSliderKnob.Position = UDim2.new(percent, -5, -0.25, 0)
    
    local jumpPower = math.floor(50 + percent * (1000 - 50))
    Settings.Movement.JumpPower = jumpPower
    JumpPowerLabel.Text = "JumpPower: " .. jumpPower
    
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.JumpPower = jumpPower
    end
end

JumpPowerSlider.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        updateJumpPower(input.Position.X)
        
        local connection
        connection = RunService.RenderStepped:Connect(function()
            if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                connection:Disconnect()
                return
            end
            updateJumpPower(UserInputService:GetMouseLocation().X)
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
ESPKeybindButton.BorderColor3 = Color3.fromRGB(20, 20, 20)
ESPKeybindButton.BorderSizePixel = 1
ESPKeybindButton.Text = "Key: " .. tostring(Settings.ESP.Keybind)
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
            ESPKeybindButton.Text = "Key: " .. tostring(input.KeyCode)
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
ESPTypeDropdown.BorderColor3 = Color3.fromRGB(20, 20, 20)
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
    ESPTypeMenuFrame.BorderColor3 = Color3.fromRGB(20, 20, 20)
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
ESPColorSlider.BorderColor3 = Color3.fromRGB(20, 20, 20)
ESPColorSlider.BorderSizePixel = 1
ESPColorSlider.Parent = VisualsContent

local ESPColorSliderCorner = Instance.new("UICorner")
ESPColorSliderCorner.CornerRadius = UDim.new(0, 5)
ESPColorSliderCorner.Parent = ESPColorSlider

local ESPColorSliderImage = Instance.new("ImageButton")
ESPColorSliderImage.Name = "ColorSlider"
ESPColorSliderImage.Size = UDim2.new(1, -10, 0, 10)
ESPColorSliderImage.Position = UDim2.new(0, 5, 0.5, -5)
ESPColorSliderImage.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ESPColorSliderImage.Image = "rbxassetid://3570695787" -- Rainbow gradient
ESPColorSliderImage.ScaleType = Enum.ScaleType.Slice
ESPColorSliderImage.SliceCenter = Rect.new(100, 100, 100, 100)
ESPColorSliderImage.Parent = ESPColorSlider

local ESPColorSliderKnob = Instance.new("Frame")
ESPColorSliderKnob.Name = "Knob"
ESPColorSliderKnob.Size = UDim2.new(0, 15, 0, 15)
ESPColorSliderKnob.Position = UDim2.new(0, -7.5, 0.5, -7.5)
ESPColorSliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ESPColorSliderKnob.BorderSizePixel = 0
ESPColorSliderKnob.Parent = ESPColorSlider

local ESPColorSliderKnobCorner = Instance.new("UICorner")
ESPColorSliderKnobCorner.CornerRadius = UDim.new(0.5, 0)
ESPColorSliderKnobCorner.Parent = ESPColorSliderKnob

local function updateESPColor(positionX)
    local percent = math.clamp((positionX - ESPColorSliderImage.AbsolutePosition.X) / ESPColorSliderImage.AbsoluteSize.X, 0, 1)
    ESPColorSliderKnob.Position = UDim2.new(percent, -7.5, 0.5, -7.5)
    
    local hue = percent * 360
    Settings.ESP.Color = Color3.fromHSV(hue/360, 1, 1)
end

ESPColorSliderImage.MouseButton1Down:Connect(function(x, y)
    local mouseLocation = UserInputService:GetMouseLocation()
    updateESPColor(mouseLocation.X)
    
    local connection
    connection = RunService.RenderStepped:Connect(function()
        if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
            connection:Disconnect()
            return
        end
        local newMouseLocation = UserInputService:GetMouseLocation()
        updateESPColor(newMouseLocation.X)
    end)
end)

-- MISC TAB CONTENT
local MiscContent = TabContents["Misc"]

local CreditLabel = Instance.new("TextLabel")
CreditLabel.Name = "CreditLabel"
CreditLabel.Size = UDim2.new(1, 0, 1, 0)
CreditLabel.BackgroundTransparency = 1
CreditLabel.Text = "Made by @Dokiora"
CreditLabel.TextColor3 = Color3.fromRGB(255, 165, 0) -- Orange
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
    drawings.Tracer.Color = Settings.ESP.Color
    drawings.Tracer.Transparency = 0.7
    drawings.Tracer.Visible = false

    local function updateESP()
        if not Settings.ESP.Enabled or not player.Character then
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
            return
        end

        local character = player.Character
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        local head = character:FindFirstChild("Head")

        if not humanoidRootPart or not head then
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
            return
        end

        local teamColor = Settings.ESP.TeamColor and player.Team and player.Team.TeamColor.Color or Settings.ESP.Color

        -- Box ESP
        if Settings.ESP.Type == "Full body filled" or Settings.ESP.Type == "Hitbox" then
            local position, visible = Camera:WorldToViewportPoint(humanoidRootPart.Position)
            if visible then
                local scale = 1 / (position.Z * math.tan(math.rad(Camera.FieldOfView / 2))) * 1000
                local width, height = 4 * scale, 5 * scale
                
                drawings.Box.Size = Vector2.new(width, height)
                drawings.Box.Position = Vector2.new(position.X - width / 2, position.Y - height / 2)
                drawings.Box.Color = teamColor
                drawings.Box.Visible = true
                drawings.Box.Filled = Settings.ESP.Type == "Full body filled"
            else
                drawings.Box.Visible = false
            end
        else
            drawings.Box.Visible = false
        end

        -- Name ESP
        if Settings.ESP.Type == "Name" then
            local headPosition, headVisible = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
            if headVisible then
                drawings.Name.Text = player.Name
                drawings.Name.Position = Vector2.new(headPosition.X, headPosition.Y) + Vector2.new(0, -30)
                drawings.Name.Color = teamColor
                drawings.Name.Visible = true
            else
                drawings.Name.Visible = false
            end
        else
            drawings.Name.Visible = false
        end

        -- Tracer ESP
        if Settings.ESP.Type == "Tracer" then
            local rootPosition, rootVisible = Camera:WorldToViewportPoint(humanoidRootPart.Position)
            if rootVisible then
                drawings.Tracer.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                drawings.Tracer.To = Vector2.new(rootPosition.X, rootPosition.Y)
                drawings.Tracer.Color = teamColor
                drawings.Tracer.Visible = true
            else
                drawings.Tracer.Visible = false
            end
        else
            drawings.Tracer.Visible = false
        end
    end

    -- Character added event
    local characterAddedConnection = player.CharacterAdded:Connect(function(character)
        local humanoid = character:WaitForChild("Humanoid")
        
        humanoid.Died:Connect(function()
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
        end)
        
        ESPConnections[player] = RunService.RenderStepped:Connect(updateESP)
    end)

    -- Existing character
    if player.Character then
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.Died:Connect(function()
                for _, drawing in pairs(drawings) do
                    drawing.Visible = false
                end
            end)
        end
        ESPConnections[player] = RunService.RenderStepped:Connect(updateESP)
    end

    -- Player leaving
    player.AncestryChanged:Connect(function(_, parent)
        if parent == nil then
            if ESPConnections[player] then
                ESPConnections[player]:Disconnect()
                ESPConnections[player] = nil
            end
            
            if ESPObjects[player] then
                for _, drawing in pairs(ESPObjects[player]) do
                    drawing:Remove()
                end
                ESPObjects[player] = nil
            end
            
            if characterAddedConnection then
                characterAddedConnection:Disconnect()
            end
        end
    end)
end

-- Initialize ESP for all players
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        createESP(player)
    end
end

-- New player handler
Players.PlayerAdded:Connect(function(player)
    createESP(player)
end)

-- Aimbot functionality
local function isVisible(part, model)
    local origin = Camera.CFrame.Position
    local _, onScreen = Camera:WorldToViewportPoint(part.Position)
    if not onScreen then return false end
    
    local ray = Ray.new(origin, (part.Position - origin).Unit * 1000)
    local hit, _ = workspace:FindPartOnRayWithIgnoreList(ray, {LocalPlayer.Character})
    
    return hit and hit:IsDescendantOf(model)
end

local function getClosestPlayer()
    local closest = nil
    local shortest = math.huge
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local humanoid = player.Character:FindFirstChild("Humanoid")
            local bodyPart = player.Character:FindFirstChild(Settings.Aimbot.BodyPart)
            
            if humanoid and humanoid.Health > 0 and bodyPart then
                if not Settings.Aimbot.TeamCheck or player.Team ~= LocalPlayer.Team then
                    local pos, onScreen = Camera:WorldToViewportPoint(bodyPart.Position)
                    if onScreen then
                        local dist = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                        if dist < Settings.Aimbot.FOVRadius and dist < shortest and isVisible(bodyPart, player.Character) then
                            closest = player
                            shortest = dist
                        end
                    end
                end
            end
        end
    end
    
    return closest
end

local aiming = false

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Settings.Aimbot.Keybind then
        aiming = true
    end
    
    if input.KeyCode == Settings.ESP.Keybind then
        Settings.ESP.Enabled = not Settings.ESP.Enabled
    end
end)

UserInputService.InputEnded:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Settings.Aimbot.Keybind then
        aiming = false
    end
end)

-- Movement updates
LocalPlayer.CharacterAdded:Connect(function(character)
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.WalkSpeed = Settings.Movement.WalkSpeed
    humanoid.JumpPower = Settings.Movement.JumpPower
end)

-- Main loop
RunService.RenderStepped:Connect(function()
    -- Update FOV Circle
    FOVCircle.Position = Vector2.new(Mouse.X, Mouse.Y)
    FOVCircle.Radius = Settings.Aimbot.FOVRadius
    FOVCircle.Color = Settings.Aimbot.FOVColor
    FOVCircle.Visible = Settings.Aimbot.Enabled

    -- Aimbot
    if aiming and Settings.Aimbot.Enabled then
        local target = getClosestPlayer()
        if target and target.Character and target.Character:FindFirstChild(Settings.Aimbot.BodyPart) then
            local bodyPart = target.Character[Settings.Aimbot.BodyPart].Position
            Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, bodyPart), Settings.Aimbot.Smoothness)
        end
    end
end)

-- UI Drag functionality
local dragging = false
local dragInput, dragStart, startPos

MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

MainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Initial notification
local function notify(message)
    local notification = Instance.new("TextLabel")
    notification.Name = "Notification"
    notification.Size = UDim2.new(0, 200, 0, 40)
    notification.Position = UDim2.new(0.5, -100, 1, -100)
    notification.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    notification.BackgroundTransparency = 0.5
    notification.BorderColor3 = Color3.fromRGB(40, 40, 40)
    notification.BorderSizePixel = 2
    notification.Text = message
    notification.TextColor3 = Color3.fromRGB(255, 165, 0) -- Orange
    notification.TextSize = 14
    notification.Font = Enum.Font.Gotham
    notification.Parent = ScreenGui
    
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

notify("Doki's Hub loaded successfully!")
