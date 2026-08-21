-- เคลียร์ UI เก่าทิ้งทันทีเพื่อป้องกันการซ้อนทับ
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

if CoreGui:FindFirstChild("CustomScriptUI") then
    CoreGui.CustomScriptUI:Destroy()
end

-- สร้าง ScreenGui หลัก
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CustomScriptUI"
ScreenGui.Parent = CoreGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- หน้าต่างแจ้งเตือนสถานะตอนเริ่ม (Loading Screen)
local LoadFrame = Instance.new("Frame")
LoadFrame.Name = "LoadFrame"
LoadFrame.Size = UDim2.new(0, 240, 0, 50)
LoadFrame.Position = UDim2.new(0.5, -120, 0.5, -25)
LoadFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
LoadFrame.BorderSizePixel = 0
LoadFrame.Parent = ScreenGui

local LoadCorner = Instance.new("UICorner")
LoadCorner.CornerRadius = UDim.new(0, 6)
LoadCorner.Parent = LoadFrame

local LoadStroke = Instance.new("UIStroke")
LoadStroke.Color = Color3.fromRGB(0, 162, 255)
LoadStroke.Thickness = 1.2
LoadStroke.Parent = LoadFrame

local LoadText = Instance.new("TextLabel")
LoadText.Name = "LoadText"
LoadText.Size = UDim2.new(1, 0, 1, 0)
LoadText.BackgroundTransparency = 1
LoadText.Font = Enum.Font.GothamBold
LoadText.Text = "กำลังโหลด"
LoadText.TextColor3 = Color3.fromRGB(255, 255, 255)
LoadText.TextSize = 13
LoadText.Parent = LoadFrame

-- ==================== ตัวแปรความเร็วตั้งต้น (0.01s - 1s) ====================
local sellDelay = 0.1
local harvestDelay = 0.1
local buyDelay = 0.1

-- ==================== ตัวแปรปรับแต่งตัวละคร ====================
local customWalkSpeed = 16
local customJumpPower = 50
local infJumpEnabled = false

-- ==================== ตัวแปรรักษาการกรองน้ำหนัก ====================
local weightFilterEnabled = false
local weightConditionMode = "greater" -- "greater" = สูงกว่า, "less" = ต่ำกว่า
local targetWeightValue = 0
local selectedWeightFruits = {}

-- ==================== ระบบ Cache ProximityPrompt แก้ปัญหาแล็ก ====================
local cachedPrompts = {}

local function registerPrompt(prompt)
    if prompt:IsA("ProximityPrompt") and not table.find(cachedPrompts, prompt) then
        table.insert(cachedPrompts, prompt)
    end
end

for _, v in ipairs(Workspace:GetDescendants()) do
    registerPrompt(v)
end

Workspace.DescendantAdded:Connect(registerPrompt)
Workspace.DescendantRemoving:Connect(function(v)
    if v:IsA("ProximityPrompt") then
        local idx = table.find(cachedPrompts, v)
        if idx then
            table.remove(cachedPrompts, idx)
        end
    end
end)

-- ==================== หน้าต่าง UI หลัก ====================
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 360, 0, 220)
MainFrame.Position = UDim2.new(0.5, -180, 0.5, -110)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false
MainFrame.BackgroundTransparency = 1
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 7)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(0, 162, 255)
MainStroke.Thickness = 1.2
MainStroke.Parent = MainFrame

-- แถบหัวข้อด้านบน (TopBar)
local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 24)
TopBar.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
TopBar.BorderSizePixel = 0
TopBar.Parent = MainFrame

local TopBarCorner = Instance.new("UICorner")
TopBarCorner.CornerRadius = UDim.new(0, 7)
TopBarCorner.Parent = TopBar

local FixTopBar = Instance.new("Frame")
FixTopBar.Size = UDim2.new(1, 0, 0, 5)
FixTopBar.Position = UDim2.new(0, 0, 1, -5)
FixTopBar.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
FixTopBar.BorderSizePixel = 0
FixTopBar.Parent = TopBar

-- โลโก้ตัว "G"
local LogoG = Instance.new("TextLabel")
LogoG.Size = UDim2.new(0, 18, 0, 18)
LogoG.Position = UDim2.new(0, 6, 0, 3)
LogoG.BackgroundTransparency = 1
LogoG.Font = Enum.Font.GothamBold
LogoG.Text = "G"
LogoG.TextColor3 = Color3.fromRGB(0, 162, 255)
LogoG.TextSize = 12
LogoG.Parent = TopBar

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, -65, 1, 0)
TitleLabel.Position = UDim2.new(0, 26, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.Text = "Black & White UI | Blue Theme"
TitleLabel.TextColor3 = Color3.fromRGB(240, 240, 240)
TitleLabel.TextSize = 10
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TopBar

-- ปุ่มปิด UI (X)
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 20, 0, 20)
CloseButton.Position = UDim2.new(1, -22, 0, 2)
CloseButton.BackgroundTransparency = 1
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(220, 60, 60)
CloseButton.TextSize = 11
CloseButton.Parent = TopBar

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

-- ปุ่มย่อหน้าต่าง (-)
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Size = UDim2.new(0, 20, 0, 20)
MinimizeButton.Position = UDim2.new(1, -42, 0, 2)
MinimizeButton.BackgroundTransparency = 1
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "-"
MinimizeButton.TextColor3 = Color3.fromRGB(200, 200, 200)
MinimizeButton.TextSize = 12
MinimizeButton.Parent = TopBar

local isMinimized = false
MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        MinimizeButton.Text = "+"
        TweenService:Create(MainFrame, TweenInfo.new(0.25), {Size = UDim2.new(0, 360, 0, 24)}):Play()
    else
        MinimizeButton.Text = "-"
        TweenService:Create(MainFrame, TweenInfo.new(0.25), {Size = UDim2.new(0, 360, 0, 220)}):Play()
    end
end)

-- ==================== ฝั่งซ้าย: หมวดหมู่ ====================
local LeftSide = Instance.new("ScrollingFrame")
LeftSide.Size = UDim2.new(0.28, 0, 1, -28)
LeftSide.Position = UDim2.new(0, 4, 0, 26)
LeftSide.BackgroundColor3 = Color3.fromRGB(24, 24, 24)
LeftSide.BorderSizePixel = 0
LeftSide.ScrollBarThickness = 1.5
LeftSide.Parent = MainFrame

local LeftCorner = Instance.new("UICorner")
LeftCorner.CornerRadius = UDim.new(0, 4)
LeftCorner.Parent = LeftSide

local LeftLayout = Instance.new("UIListLayout")
LeftLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
LeftLayout.SortOrder = Enum.SortOrder.LayoutOrder
LeftLayout.Padding = UDim.new(0, 4)
LeftLayout.Parent = LeftSide

local LeftPadding = Instance.new("UIPadding")
LeftPadding.PaddingTop = UDim.new(0, 4)
LeftPadding.Parent = LeftSide

LeftLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    LeftSide.CanvasSize = UDim2.new(0, 0, 0, LeftLayout.AbsoluteContentSize.Y + 8)
end)

-- ==================== ฝั่งขวา: ฟังก์ชัน ====================
local RightSide = Instance.new("Frame")
RightSide.Size = UDim2.new(0.69, 0, 1, -28)
RightSide.Position = UDim2.new(0.295, 0, 0, 26)
RightSide.BackgroundColor3 = Color3.fromRGB(24, 24, 24)
RightSide.BorderSizePixel = 0
RightSide.Parent = MainFrame

local RightCorner = Instance.new("UICorner")
RightCorner.CornerRadius = UDim.new(0, 4)
RightCorner.Parent = RightSide

local FunctionContainers = {}

local function createContainer(name)
    local container = Instance.new("ScrollingFrame")
    container.Name = name
    container.Size = UDim2.new(1, 0, 1, 0)
    container.BackgroundTransparency = 1
    container.BorderSizePixel = 0
    container.ScrollBarThickness = 1.5
    container.Visible = false
    container.Parent = RightSide

    local layout = Instance.new("UIListLayout")
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 4)
    layout.Parent = container

    local padding = Instance.new("UIPadding")
    padding.PaddingTop = UDim.new(0, 4)
    padding.Parent = container

    layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        container.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 8)
    end)

    FunctionContainers[name] = container
    return container
end

-- Helper: ฟังก์ชันสร้างสวิตช์ เปิด/ปิด (Toggle Component)
local function createToggle(parent, titleText, callback)
    local RowFrame = Instance.new("Frame")
    RowFrame.Size = UDim2.new(0.92, 0, 0, 24)
    RowFrame.BackgroundColor3 = Color3.fromRGB(32, 32, 32)
    RowFrame.BorderSizePixel = 0
    RowFrame.Parent = parent

    local RowCorner = Instance.new("UICorner")
    RowCorner.CornerRadius = UDim.new(0, 4)
    RowCorner.Parent = RowFrame

    local RowStroke = Instance.new("UIStroke")
    RowStroke.Color = Color3.fromRGB(50, 50, 50)
    RowStroke.Thickness = 1
    RowStroke.Parent = RowFrame

    local RowLabel = Instance.new("TextLabel")
    RowLabel.Size = UDim2.new(0.65, 0, 1, 0)
    RowLabel.Position = UDim2.new(0, 8, 0, 0)
    RowLabel.BackgroundTransparency = 1
    RowLabel.Font = Enum.Font.GothamBold
    RowLabel.Text = titleText
    RowLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    RowLabel.TextSize = 9
    RowLabel.TextXAlignment = Enum.TextXAlignment.Left
    RowLabel.Parent = RowFrame

    local ToggleBackground = Instance.new("TextButton")
    ToggleBackground.Size = UDim2.new(0, 34, 0, 15)
    ToggleBackground.Position = UDim2.new(1, -40, 0.5, -7.5)
    ToggleBackground.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    ToggleBackground.BorderSizePixel = 0
    ToggleBackground.Text = ""
    ToggleBackground.AutoButtonColor = false
    ToggleBackground.Parent = RowFrame

    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(1, 0)
    ToggleCorner.Parent = ToggleBackground

    local ToggleCircle = Instance.new("Frame")
    ToggleCircle.Size = UDim2.new(0, 11, 0, 11)
    ToggleCircle.Position = UDim2.new(0, 2, 0.5, -5.5)
    ToggleCircle.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
    ToggleCircle.BorderSizePixel = 0
    ToggleCircle.Parent = ToggleBackground

    local CircleCorner = Instance.new("UICorner")
    CircleCorner.CornerRadius = UDim.new(1, 0)
    CircleCorner.Parent = ToggleCircle

    local toggled = false

    local function setVisualState(state, triggerCallback)
        toggled = state
        if toggled then
            TweenService:Create(ToggleBackground, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(0, 140, 255)}):Play()
            TweenService:Create(ToggleCircle, TweenInfo.new(0.15), {Position = UDim2.new(1, -13, 0.5, -5.5), BackgroundColor3 = Color3.fromRGB(255, 255, 255)}):Play()
        else
            TweenService:Create(ToggleBackground, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(50, 50, 50)}):Play()
            TweenService:Create(ToggleCircle, TweenInfo.new(0.15), {Position = UDim2.new(0, 2, 0.5, -5.5), BackgroundColor3 = Color3.fromRGB(200, 200, 200)}):Play()
        end
        if triggerCallback ~= false then
            callback(toggled)
        end
    end

    ToggleBackground.MouseButton1Click:Connect(function()
        setVisualState(not toggled, true)
    end)

    return {
        Frame = RowFrame,
        SetState = function(state, fireCallback)
            setVisualState(state, fireCallback)
        end,
        GetState = function()
            return toggled
        end
    }
end

-- Helper: ฟังก์ชันสร้างช่องกรอกตัวเลข
local function createNumberInput(parent, titleText, defaultVal, callback, unitText)
    unitText = unitText or ""
    local RowFrame = Instance.new("Frame")
    RowFrame.Size = UDim2.new(0.92, 0, 0, 24)
    RowFrame.BackgroundColor3 = Color3.fromRGB(32, 32, 32)
    RowFrame.BorderSizePixel = 0
    RowFrame.Parent = parent

    local RowCorner = Instance.new("UICorner")
    RowCorner.CornerRadius = UDim.new(0, 4)
    RowCorner.Parent = RowFrame

    local RowStroke = Instance.new("UIStroke")
    RowStroke.Color = Color3.fromRGB(50, 50, 50)
    RowStroke.Thickness = 1
    RowStroke.Parent = RowFrame

    local RowLabel = Instance.new("TextLabel")
    RowLabel.Size = UDim2.new(0.65, 0, 1, 0)
    RowLabel.Position = UDim2.new(0, 8, 0, 0)
    RowLabel.BackgroundTransparency = 1
    RowLabel.Font = Enum.Font.GothamBold
    RowLabel.Text = titleText
    RowLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    RowLabel.TextSize = 9
    RowLabel.TextXAlignment = Enum.TextXAlignment.Left
    RowLabel.Parent = RowFrame

    local TextBox = Instance.new("TextBox")
    TextBox.Size = UDim2.new(0, 50, 0, 16)
    TextBox.Position = UDim2.new(1, -55, 0.5, -8)
    TextBox.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    TextBox.BorderSizePixel = 0
    TextBox.Font = Enum.Font.GothamBold
    TextBox.Text = tostring(defaultVal) .. unitText
    TextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    TextBox.TextSize = 9
    TextBox.ClearTextOnFocus = false
    TextBox.Parent = RowFrame

    local TextCorner = Instance.new("UICorner")
    TextCorner.CornerRadius = UDim.new(0, 3)
    TextCorner.Parent = TextBox

    TextBox.FocusLost:Connect(function()
        local num = tonumber(TextBox.Text:match("[%d%.]+"))
        if num then
            TextBox.Text = tostring(num) .. unitText
            callback(num)
        else
            TextBox.Text = tostring(defaultVal) .. unitText
            callback(defaultVal)
        end
    end)

    return RowFrame
end

-- Helper: ฟังก์ชันสร้างสลับโหมดกด (Mode Button)
local function createModeSelector(parent, titleText, defaultMode, callback)
    local RowFrame = Instance.new("Frame")
    RowFrame.Size = UDim2.new(0.92, 0, 0, 24)
    RowFrame.BackgroundColor3 = Color3.fromRGB(32, 32, 32)
    RowFrame.BorderSizePixel = 0
    RowFrame.Parent = parent

    local RowCorner = Instance.new("UICorner")
    RowCorner.CornerRadius = UDim.new(0, 4)
    RowCorner.Parent = RowFrame

    local RowStroke = Instance.new("UIStroke")
    RowStroke.Color = Color3.fromRGB(50, 50, 50)
    RowStroke.Thickness = 1
    RowStroke.Parent = RowFrame

    local RowLabel = Instance.new("TextLabel")
    RowLabel.Size = UDim2.new(0.5, 0, 1, 0)
    RowLabel.Position = UDim2.new(0, 8, 0, 0)
    RowLabel.BackgroundTransparency = 1
    RowLabel.Font = Enum.Font.GothamBold
    RowLabel.Text = titleText
    RowLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    RowLabel.TextSize = 9
    RowLabel.TextXAlignment = Enum.TextXAlignment.Left
    RowLabel.Parent = RowFrame

    local ModeBtn = Instance.new("TextButton")
    ModeBtn.Size = UDim2.new(0, 75, 0, 16)
    ModeBtn.Position = UDim2.new(1, -80, 0.5, -8)
    ModeBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
    ModeBtn.BorderSizePixel = 0
    ModeBtn.Font = Enum.Font.GothamBold
    ModeBtn.Text = (defaultMode == "greater") and "สูงกว่า (>=)" or "ต่ำกว่า (<=)"
    ModeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ModeBtn.TextSize = 8
    ModeBtn.Parent = RowFrame

    local ModeCorner = Instance.new("UICorner")
    ModeCorner.CornerRadius = UDim.new(0, 3)
    ModeCorner.Parent = ModeBtn

    local currentMode = defaultMode
    ModeBtn.MouseButton1Click:Connect(function()
        if currentMode == "greater" then
            currentMode = "less"
            ModeBtn.Text = "ต่ำกว่า (<=)"
            ModeBtn.BackgroundColor3 = Color3.fromRGB(220, 120, 0)
        else
            currentMode = "greater"
            ModeBtn.Text = "สูงกว่า (>=)"
            ModeBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
        end
        callback(currentMode)
    end)

    return RowFrame
end

-- 1. หมวด "เมนูหลัก"
local MainMenuContainer = createContainer("เมนูหลัก")

local DiscordBtn = Instance.new("TextButton")
DiscordBtn.Size = UDim2.new(0.92, 0, 0, 24)
DiscordBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
DiscordBtn.BorderSizePixel = 0
DiscordBtn.Font = Enum.Font.GothamBold
DiscordBtn.Text = "คัดลอกลิงก์ Discord"
DiscordBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
DiscordBtn.TextSize = 9
DiscordBtn.Parent = MainMenuContainer

local DiscordBtnCorner = Instance.new("UICorner")
DiscordBtnCorner.CornerRadius = UDim.new(0, 4)
DiscordBtnCorner.Parent = DiscordBtn

local DiscordBtnStroke = Instance.new("UIStroke")
DiscordBtnStroke.Color = Color3.fromRGB(0, 140, 255)
DiscordBtnStroke.Thickness = 0.8
DiscordBtnStroke.Parent = DiscordBtn

DiscordBtn.MouseButton1Click:Connect(function()
    pcall(function()
        if setclipboard then
            setclipboard("https://discord.gg/Ugssja4wqQ")
        end
    end)
end)

-- --- ส่วนตั้งค่าความเร็วของระบบ ---
local SpeedHeader = Instance.new("TextLabel")
SpeedHeader.Size = UDim2.new(0.92, 0, 0, 16)
SpeedHeader.BackgroundTransparency = 1
SpeedHeader.Font = Enum.Font.GothamBold
SpeedHeader.Text = "--- ตั้งค่าความเร็วระบบ (0.01s - 1s) ---"
SpeedHeader.TextColor3 = Color3.fromRGB(0, 162, 255)
SpeedHeader.TextSize = 8
SpeedHeader.Parent = MainMenuContainer

createNumberInput(MainMenuContainer, "ความเร็วขายของ", 0.1, function(val)
    sellDelay = math.clamp(val, 0.01, 1.0)
end, "s")

createNumberInput(MainMenuContainer, "ความเร็วเก็บผลไม้", 0.1, function(val)
    harvestDelay = math.clamp(val, 0.01, 1.0)
end, "s")

createNumberInput(MainMenuContainer, "ความเร็วซื้อเมล็ด", 0.1, function(val)
    buyDelay = math.clamp(val, 0.01, 1.0)
end, "s")

-- --- ส่วนปรับแต่งตัวละคร ---
local PlayerHeader = Instance.new("TextLabel")
PlayerHeader.Size = UDim2.new(0.92, 0, 0, 16)
PlayerHeader.BackgroundTransparency = 1
PlayerHeader.Font = Enum.Font.GothamBold
PlayerHeader.Text = "--- ปรับแต่งตัวละคร ---"
PlayerHeader.TextColor3 = Color3.fromRGB(0, 162, 255)
PlayerHeader.TextSize = 8
PlayerHeader.Parent = MainMenuContainer

createNumberInput(MainMenuContainer, "ความเร็วเดิน (WalkSpeed)", 16, function(val)
    customWalkSpeed = val
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = customWalkSpeed
    end
end, "")

createNumberInput(MainMenuContainer, "แรงกระโดด (JumpPower)", 50, function(val)
    customJumpPower = val
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        hum.UseJumpPower = true
        hum.JumpPower = customJumpPower
    end
end, "")

createToggle(MainMenuContainer, "กระโดดไม่จำกัด (Inf Jump)", function(state)
    infJumpEnabled = state
end)

-- Loop อัปเดตค่าตัวละคร
task.spawn(function()
    while task.wait(0.1) do
        if LocalPlayer.Character then
            local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if hum then
                if customWalkSpeed ~= 16 then
                    hum.WalkSpeed = customWalkSpeed
                end
                if customJumpPower ~= 50 then
                    hum.UseJumpPower = true
                    hum.JumpPower = customJumpPower
                end
            end
        end
    end
end)

UserInputService.JumpRequest:Connect(function()
    if infJumpEnabled and LocalPlayer.Character then
        local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if hum then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)

-- 2. หมวด "ช่วยเล่น"
local HelpPlayContainer = createContainer("ช่วยเล่น")

-- --- ปุ่ม: ขายทั้งหมด ---
local RowFrameSell = Instance.new("Frame")
RowFrameSell.Size = UDim2.new(0.92, 0, 0, 26)
RowFrameSell.BackgroundColor3 = Color3.fromRGB(32, 32, 32)
RowFrameSell.BorderSizePixel = 0
RowFrameSell.Parent = HelpPlayContainer

local RowCornerSell = Instance.new("UICorner")
RowCornerSell.CornerRadius = UDim.new(0, 4)
RowCornerSell.Parent = RowFrameSell

local RowStrokeSell = Instance.new("UIStroke")
RowStrokeSell.Color = Color3.fromRGB(50, 50, 50)
RowStrokeSell.Thickness = 1
RowStrokeSell.Parent = RowFrameSell

local RowLabelSell = Instance.new("TextLabel")
RowLabelSell.Size = UDim2.new(0.6, 0, 1, 0)
RowLabelSell.Position = UDim2.new(0, 8, 0, 0)
RowLabelSell.BackgroundTransparency = 1
RowLabelSell.Font = Enum.Font.GothamBold
RowLabelSell.Text = "ขายทั้งหมด"
RowLabelSell.TextColor3 = Color3.fromRGB(255, 255, 255)
RowLabelSell.TextSize = 9
RowLabelSell.TextXAlignment = Enum.TextXAlignment.Left
RowLabelSell.Parent = RowFrameSell

local SellBtn = Instance.new("TextButton")
SellBtn.Size = UDim2.new(0, 42, 0, 18)
SellBtn.Position = UDim2.new(1, -46, 0.5, -9)
SellBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
SellBtn.BorderSizePixel = 0
SellBtn.Font = Enum.Font.GothamBold
SellBtn.Text = "ขาย"
SellBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
SellBtn.TextSize = 9
SellBtn.Parent = RowFrameSell

local SellBtnCorner = Instance.new("UICorner")
SellBtnCorner.CornerRadius = UDim.new(0, 4)
SellBtnCorner.Parent = SellBtn

SellBtn.MouseButton1Click:Connect(function()
    pcall(function()
        local remote = ReplicatedStorage:FindFirstChild("RequestSell")
        if remote then
            remote:InvokeServer("sellAll")
        end
    end)
end)

-- --- ฟังก์ชัน: ขายอัตโนมัติ ---
local autoSellEnabled = false
createToggle(HelpPlayContainer, "ขายอัตโนมัติ", function(state)
    autoSellEnabled = state
    if autoSellEnabled then
        task.spawn(function()
            while autoSellEnabled do
                pcall(function()
                    local remote = ReplicatedStorage:FindFirstChild("RequestSell")
                    if remote then
                        remote:InvokeServer("sellAll")
                    end
                end)
                task.wait(sellDelay)
            end
        end)
    end
end)

-- --- ฟังก์ชัน: ดึงค่าน้ำหนักของผลไม้ ---
local function getFruitWeight(part)
    local parentModel = part:FindFirstAncestorOfClass("Model") or part.Parent
    if not parentModel then return nil end

    local weightVal = parentModel:FindFirstChild("Weight") or parentModel:FindFirstChild("weight") or part:FindFirstChild("Weight")
    if weightVal then
        if weightVal:IsA("NumberValue") or weightVal:IsA("IntValue") then
            return weightVal.Value
        elseif weightVal:IsA("StringValue") then
            local num = tonumber(weightVal.Value:match("[%d%.]+"))
            if num then return num end
        end
    end

    for _, child in ipairs(parentModel:GetDescendants()) do
        if child:IsA("TextLabel") or child:IsA("SurfaceGui") or child:IsA("BillboardGui") then
            local text = child:IsA("TextLabel") and child.Text or ""
            if text == "" and child:FindFirstChildWhichIsA("TextLabel") then
                text = child:FindFirstChildWhichIsA("TextLabel").Text
            end
            
            local weightNum = tonumber(text:match("[%d%.]+"))
            if weightNum and (string.find(string.lower(text), "kg") or string.find(string.lower(text), "weight") or string.find(text, "น้ำหนัก")) then
                return weightNum
            end
        end
    end

    return nil
end

-- --- ฟังก์ชัน: เดินผ่านแล้วเก็บอัตโนมัติ ---
local autoHarvestEnabled = false
local shopBlacklist = {"npc", "shop", "merchant", "seller", "vendor", "store", "ร้าน", "ร้านค้า", "คุย", "talk", "buy", "ซื้อ", "dirt", "plot", "farm", "grass", "baseplate", "ground", "floor"}

local function isValidHarvestTarget(part, prompt)
    local parentModel = part:FindFirstAncestorOfClass("Model")
    if parentModel and parentModel:FindFirstChildOfClass("Humanoid") then
        return false
    end

    local partName = string.lower(part.Name)
    local parentName = parentModel and string.lower(parentModel.Name) or ""
    
    for _, badWord in ipairs(shopBlacklist) do
        if string.find(partName, badWord) or string.find(parentName, badWord) then
            return false
        end
    end

    if prompt then
        local actionText = string.lower(tostring(prompt.ActionText))
        local objectText = string.lower(tostring(prompt.ObjectText))
        for _, badWord in ipairs(shopBlacklist) do
            if string.find(actionText, badWord) or string.find(objectText, badWord) then
                return false
            end
        end
    end

    if weightFilterEnabled then
        local matchedSelectedFruit = false
        for fruitName, isSelected in pairs(selectedWeightFruits) do
            if isSelected then
                if string.find(partName, string.lower(fruitName)) or string.find(parentName, string.lower(fruitName)) then
                    matchedSelectedFruit = true
                    break
                end
            end
        end

        if matchedSelectedFruit then
            local weight = getFruitWeight(part)
            if weight then
                if weightConditionMode == "greater" and weight < targetWeightValue then
                    return false
                elseif weightConditionMode == "less" and weight > targetWeightValue then
                    return false
                end
            end
        end
    end

    return true
end

createToggle(HelpPlayContainer, "เดินเก็บผลไม้ออโต้", function(state)
    autoHarvestEnabled = state
    if autoHarvestEnabled then
        task.spawn(function()
            while autoHarvestEnabled do
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                
                if hrp then
                    local myPos = hrp.Position
                    
                    for i = #cachedPrompts, 1, -1 do
                        if not autoHarvestEnabled then break end
                        local prompt = cachedPrompts[i]
                        
                        if prompt and prompt.Parent and prompt.Enabled then
                            local part = prompt.Parent
                            if part:IsA("BasePart") then
                                local dist2D = math.sqrt((myPos.X - part.Position.X)^2 + (myPos.Z - part.Position.Z)^2)
                                
                                if dist2D <= 50 and isValidHarvestTarget(part, prompt) then
                                    if fireproximityprompt then
                                        fireproximityprompt(prompt)
                                    elseif prompt.InputHoldBegin then
                                        prompt:InputHoldBegin()
                                        prompt:InputHoldEnd()
                                    end
                                    
                                    if firetouchinterest then
                                        firetouchinterest(hrp, part, 0)
                                        firetouchinterest(hrp, part, 1)
                                    end
                                end
                            end
                        end
                    end
                end
                task.wait(harvestDelay)
            end
        end)
    end
end)

-- Helper: ฟังก์ชันยิงคำสั่งซื้อไอเทม (วนลูป 3 รูปแบบตามที่กำหนด)
local function buyItemSmart(candidates)
    local reqRemote = ReplicatedStorage:FindFirstChild("RequestPurchase") or ReplicatedStorage:FindFirstChild("PurchaseItem") or ReplicatedStorage:FindFirstChild("BuyItem")
    if not reqRemote then return end

    for _, itemName in ipairs(candidates) do
        local success = false
        pcall(function()
            if reqRemote:IsA("RemoteFunction") then
                reqRemote:InvokeServer(itemName)
                success = true
            elseif reqRemote:IsA("RemoteEvent") then
                reqRemote:FireServer(itemName)
                success = true
            end
        end)
        if success then 
            break -- ถ้ารันสำเร็จแบบแรกแล้ว จะไม่รันแบบถัดไป (ถ้าไม่สำเร็จจะวนรันตัวถัดไปจนครบ)
        end
    end
end

-- 3. หมวด "ออโต้"
local AutoContainer = createContainer("ออโต้")

local selectedSeeds = {}
local seedToggles = {}
local isUpdatingAllSeeds = false
local autoBuyEnabled = false

createToggle(AutoContainer, "ซื้อเมล็ดอัตโนมัติ", function(state)
    autoBuyEnabled = state
    if autoBuyEnabled then
        task.spawn(function()
            while autoBuyEnabled do
                for seedDataKey, isSelected in pairs(selectedSeeds) do
                    if not autoBuyEnabled then break end
                    if isSelected then
                        for _, seedData in ipairs(seedList) do
                            if seedData.key == seedDataKey then
                                buyItemSmart(seedData.candidates)
                                break
                            end
                        end
                    end
                end
                task.wait(buyDelay)
            end
        end)
    end
end)

local SubTitleSeeds = Instance.new("TextLabel")
SubTitleSeeds.Size = UDim2.new(0.92, 0, 0, 16)
SubTitleSeeds.BackgroundTransparency = 1
SubTitleSeeds.Font = Enum.Font.GothamBold
SubTitleSeeds.Text = "--- ตัวเลือกซื้อเมล็ดพันธุ์ ---"
SubTitleSeeds.TextColor3 = Color3.fromRGB(0, 162, 255)
SubTitleSeeds.TextSize = 8
SubTitleSeeds.Parent = AutoContainer

-- รายชื่อเมล็ดพันธุ์ (อัปเดต candidates ให้ครอบคลุมทั้ง 3 รูปแบบภาษาอังกฤษโดยอัตโนมัติ)
local seedList = {
    {key = "Carrot", label = "Carrot (แครอท)", candidates = {"Carrot", "CarrotSeed", "Carrot Seed"}},
    {key = "Miki", label = "Miki Seed", candidates = {"Miki", "MikiSeed", "Miki Seed"}},
    {key = "MedTrad", label = "MedTrad Seed", candidates = {"MedTrad", "MedTradSeed", "MedTrad Seed"}},
    {key = "EmoeHair", label = "EmoeHair Seed", candidates = {"EmoeHair", "EmoeHairSeed", "EmoeHair Seed"}},
    {key = "Siw", label = "Siw Seed", candidates = {"Siw", "SiwSeed", "Siw Seed"}},
    {key = "Nom", label = "Nom Seed", candidates = {"Nom", "NomSeed", "Nom Seed"}},
    {key = "Car", label = "Car (รถยนต์)", candidates = {"Car", "CarSeed", "Car Seed"}},
    {key = "House", label = "House (บ้าน)", candidates = {"House", "HouseSeed", "House Seed"}},
    {key = "MysteryRocket", label = "MysteryRocket Seed", candidates = {"MysteryRocket", "MysteryRocketSeed", "MysteryRocket Seed"}},
    {key = "MysteryStick", label = "MysteryStick Seed", candidates = {"MysteryStick", "MysteryStickSeed", "MysteryStick Seed"}},
    {key = "Bamboo", label = "Bamboo (ไม้ไผ่)", candidates = {"Bamboo", "BambooSeed", "Bamboo Seed"}},
    {key = "Eggkapok", label = "Eggkapok Seed", candidates = {"Eggkapok", "EggkapokSeed", "Eggkapok Seed"}},
    {key = "MrGreed", label = "MrGreed Seed", candidates = {"MrGreed", "MrGreedSeed", "MrGreed Seed"}},
    {key = "OldMhoy", label = "OldMhoy Seed", candidates = {"OldMhoy", "OldMhoySeed", "OldMhoy Seed"}},
    {key = "OldMhoyHair", label = "OldMhoyHair Seed (น้ำ)", candidates = {"OldMhoyHair", "OldMhoyWaterSeed", "OldMhoyHairSeed"}},
    {key = "YoungMhoy", label = "YoungMhoy Seed", candidates = {"YoungMhoy", "YoungMhoySeed", "YoungMhoy Seed"}},
    {key = "YoungMhoyHair", label = "YoungMhoyHair Seed (น้ำ)", candidates = {"YoungMhoyHair", "YoungMhoyWaterSeed", "YoungMhoyHairSeed"}},
    {key = "Ong", label = "Ong Seed", candidates = {"Ong", "OngSeed", "Ong Seed"}},
    {key = "Budha", label = "Budha Seed", candidates = {"Budha", "BudhaSeed", "Budha Seed"}},
    {key = "NorTad", label = "NorTad Seed", candidates = {"NorTad", "NorTadSeed", "NorTadWaterSeed"}},
    {key = "RobuxTree", label = "RobuxTree", candidates = {"RobuxTree", "RobuxTreeSeed", "Robux Tree"}},
    {key = "Glaed", label = "Glaed (เกล็ด)", candidates = {"Glaed", "GlaedSeed", "GlaedWaterSeed"}},
    {key = "GoldFlower", label = "GoldFlower (ดอกไม้ทองคำ)", candidates = {"GoldFlower", "GoldFlowerSeed", "GoldFlowerWaterSeed"}},
    {key = "Kee", label = "Kee Seed", candidates = {"Kee", "KeeSeed", "KeeWaterSeed"}},
    {key = "PCX150", label = "PCX150 Seed", candidates = {"PCX150", "PCX150Seed", "PCX150 Seed"}},
    {key = "MysteryStickTree", label = "MysteryStickTree Seed", candidates = {"MysteryStickTree", "MysteryStickTreeSeed", "MysteryStickTree Seed"}}
}

local buyAllSeedsToggleObj = nil

local function checkAllSeedsState()
    local allOn = true
    for _, seedData in ipairs(seedList) do
        if not selectedSeeds[seedData.key] then
            allOn = false
            break
        end
    end
    
    if buyAllSeedsToggleObj then
        isUpdatingAllSeeds = true
        buyAllSeedsToggleObj.SetState(allOn, false)
        isUpdatingAllSeeds = false
    end
end

buyAllSeedsToggleObj = createToggle(AutoContainer, "ซื้อเมล็ดทั้งหมด", function(state)
    if isUpdatingAllSeeds then return end
    isUpdatingAllSeeds = true
    
    for _, seedData in ipairs(seedList) do
        selectedSeeds[seedData.key] = state
        if seedToggles[seedData.key] then
            seedToggles[seedData.key].SetState(state, false)
        end
    end
    
    isUpdatingAllSeeds = false
end)

for _, seedData in ipairs(seedList) do
    selectedSeeds[seedData.key] = false
    local tObj = createToggle(AutoContainer, seedData.label, function(state)
        selectedSeeds[seedData.key] = state
        if not isUpdatingAllSeeds then
            checkAllSeedsState()
        end
    end)
    seedToggles[seedData.key] = tObj
end

-- ==================== ส่วน: ซื้อเกียร์อัตโนมัติ ====================
local selectedGears = {}
local gearToggles = {}
local isUpdatingAllGears = false
local autoBuyGearsEnabled = false

local SubTitleGears = Instance.new("TextLabel")
SubTitleGears.Size = UDim2.new(0.92, 0, 0, 16)
SubTitleGears.BackgroundTransparency = 1
SubTitleGears.Font = Enum.Font.GothamBold
SubTitleGears.Text = "--- ตัวเลือกซื้ออุปกรณ์/เกียร์ ---"
SubTitleGears.TextColor3 = Color3.fromRGB(0, 162, 255)
SubTitleGears.TextSize = 8
SubTitleGears.Parent = AutoContainer

createToggle(AutoContainer, "ซื้อเกียร์อัตโนมัติ", function(state)
    autoBuyGearsEnabled = state
    if autoBuyGearsEnabled then
        task.spawn(function()
            while autoBuyGearsEnabled do
                for gearKey, isSelected in pairs(selectedGears) do
                    if not autoBuyGearsEnabled then break end
                    if isSelected then
                        for _, gearData in ipairs(gearList) do
                            if gearData.key == gearKey then
                                buyItemSmart(gearData.candidates)
                                break
                            end
                        end
                    end
                end
                task.wait(buyDelay)
            end
        end)
    end
end)

local gearList = {
    {key = "PlantDestroyer", label = "อุปกรณ์กำจัดพืชผล", candidates = {"PlantDestroyer", "Plant Destroyer", "PlantDestroyerGear"}},
    {key = "PlantMover", label = "Plant Mover Gear", candidates = {"PlantMover", "Plant Mover", "PlantMoverGear"}},
    {key = "FartGear", label = "Fart Gear (อุปกรณ์เร่ง)", candidates = {"FartGear", "Fart Gear", "FartGearGear"}},
    {key = "SprayWater", label = "Water Spray (อุปกรณ์รดน้ำ)", candidates = {"SprayWater", "Water Spray", "WaterSpray"}}
}

local buyAllGearsToggleObj = nil

local function checkAllGearsState()
    local allOn = true
    for _, gearData in ipairs(gearList) do
        if not selectedGears[gearData.key] then
            allOn = false
            break
        end
    end
    
    if buyAllGearsToggleObj then
        isUpdatingAllGears = true
        buyAllGearsToggleObj.SetState(allOn, false)
        isUpdatingAllGears = false
    end
end

buyAllGearsToggleObj = createToggle(AutoContainer, "ซื้อเกียร์ทั้งหมด", function(state)
    if isUpdatingAllGears then return end
    isUpdatingAllGears = true
    
    for _, gearData in ipairs(gearList) do
        selectedGears[gearData.key] = state
        if gearToggles[gearData.key] then
            gearToggles[gearData.key].SetState(state, false)
        end
    end
    
    isUpdatingAllGears = false
end)

for _, gearData in ipairs(gearList) do
    selectedGears[gearData.key] = false
    local tObj = createToggle(AutoContainer, gearData.label, function(state)
        selectedGears[gearData.key] = state
        if not isUpdatingAllGears then
            checkAllGearsState()
        end
    end)
    gearToggles[gearData.key] = tObj
end

-- 4. หมวด "ขั้นต่ำน้ำหนัก"
local WeightContainer = createContainer("ขั้นต่ำน้ำหนัก")

createToggle(WeightContainer, "เปิดใช้งานกรองน้ำหนัก", function(state)
    weightFilterEnabled = state
end)

createModeSelector(WeightContainer, "โหมดการเก็บ", "greater", function(mode)
    weightConditionMode = mode
end)

createNumberInput(WeightContainer, "น้ำหนักเป้าหมาย", 0, function(val)
    targetWeightValue = val
end, "kg")

local WeightSubTitle = Instance.new("TextLabel")
WeightSubTitle.Size = UDim2.new(0.92, 0, 0, 16)
WeightSubTitle.BackgroundTransparency = 1
WeightSubTitle.Font = Enum.Font.GothamBold
WeightSubTitle.Text = "--- เลือกผลไม้ที่ต้องการกรอง ---"
WeightSubTitle.TextColor3 = Color3.fromRGB(0, 162, 255)
WeightSubTitle.TextSize = 8
WeightSubTitle.Parent = WeightContainer

local weightFruitToggles = {}
local isUpdatingAllWeightFruits = false
local selectAllWeightToggleObj = nil

local function checkAllWeightFruitsState()
    local allOn = true
    for _, seedData in ipairs(seedList) do
        if not selectedWeightFruits[seedData.key] then
            allOn = false
            break
        end
    end
    
    if selectAllWeightToggleObj then
        isUpdatingAllWeightFruits = true
        selectAllWeightToggleObj.SetState(allOn, false)
        isUpdatingAllWeightFruits = false
    end
end

selectAllWeightToggleObj = createToggle(WeightContainer, "เลือกผลไม้ทั้งหมด", function(state)
    if isUpdatingAllWeightFruits then return end
    isUpdatingAllWeightFruits = true
    
    for _, seedData in ipairs(seedList) do
        selectedWeightFruits[seedData.key] = state
        if weightFruitToggles[seedData.key] then
            weightFruitToggles[seedData.key].SetState(state, false)
        end
    end
    
    isUpdatingAllWeightFruits = false
end)

for _, seedData in ipairs(seedList) do
    selectedWeightFruits[seedData.key] = false
    local tObj = createToggle(WeightContainer, seedData.label, function(state)
        selectedWeightFruits[seedData.key] = state
        if not isUpdatingAllWeightFruits then
            checkAllWeightFruitsState()
        end
    end)
    weightFruitToggles[seedData.key] = tObj
end

-- ฟังก์ชันสลับหมวดหมู่
local function switchTab(tabName)
    for name, container in pairs(FunctionContainers) do
        container.Visible = (name == tabName)
    end
end

-- สร้างปุ่มเลือกหมวดหมู่ทางซ้าย
local function createCategoryButton(name)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 22)
    btn.BackgroundColor3 = Color3.fromRGB(32, 32, 32)
    btn.BorderSizePixel = 0
    btn.Font = Enum.Font.GothamBold
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(220, 220, 220)
    btn.TextSize = 9
    btn.Parent = LeftSide

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 3)
    corner.Parent = btn

    btn.MouseButton1Click:Connect(function()
        switchTab(name)
    end)
end

createCategoryButton("เมนูหลัก")
createCategoryButton("ช่วยเล่น")
createCategoryButton("ออโต้")
createCategoryButton("ขั้นต่ำน้ำหนัก")

switchTab("เมนูหลัก")

-- ==================== ลำดับการทำงาน (Loading & Animation) ====================
task.spawn(function()
    local count = 0
    while count < 6 do
        LoadText.Text = "กำลังโหลด" .. string.rep(".", (count % 4))
        task.wait(0.3)
        count += 1
    end
    
    LoadText.Text = "ดาวน์โหลดเสร็จแล้ว"
    task.wait(1)
    
    LoadText.Text = "คัดลอกลิงก์ Discord เรียบร้อย"
    pcall(function()
        if setclipboard then
            setclipboard("https://discord.gg/Ugssja4wqQ")
        end
    end)
    task.wait(1)
    
    LoadFrame:Destroy()
    
    MainFrame.Visible = true
    MainFrame.Size = UDim2.new(0, 260, 0, 140)
    
    local info = TweenInfo.new(0.7, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
    local expandTween = TweenService:Create(MainFrame, info, {
        Size = UDim2.new(0, 360, 0, 220),
        BackgroundTransparency = 0
    })
    
    expandTween:Play()
end)
