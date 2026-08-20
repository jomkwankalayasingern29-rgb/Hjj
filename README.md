-- เคลียร์ UI เก่าทิ้งทันทีเพื่อป้องกันการซ้อนทับ
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

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

-- ==================== หน้าต่าง UI หลัก ====================
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 350, 0, 210)
MainFrame.Position = UDim2.new(0.5, -175, 0.5, -105)
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
        TweenService:Create(MainFrame, TweenInfo.new(0.25), {Size = UDim2.new(0, 350, 0, 24)}):Play()
    else
        MinimizeButton.Text = "-"
        TweenService:Create(MainFrame, TweenInfo.new(0.25), {Size = UDim2.new(0, 350, 0, 210)}):Play()
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
    ToggleBackground.MouseButton1Click:Connect(function()
        toggled = not toggled
        if toggled then
            TweenService:Create(ToggleBackground, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(0, 140, 255)}):Play()
            TweenService:Create(ToggleCircle, TweenInfo.new(0.15), {Position = UDim2.new(1, -13, 0.5, -5.5), BackgroundColor3 = Color3.fromRGB(255, 255, 255)}):Play()
        else
            TweenService:Create(ToggleBackground, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(50, 50, 50)}):Play()
            TweenService:Create(ToggleCircle, TweenInfo.new(0.15), {Position = UDim2.new(0, 2, 0.5, -5.5), BackgroundColor3 = Color3.fromRGB(200, 200, 200)}):Play()
        end
        callback(toggled)
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
    if setclipboard then
        setclipboard("https://discord.gg/Ugssja4wqQ")
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
        ReplicatedStorage:WaitForChild("RequestSell"):InvokeServer("sellAll")
    end)
end)

-- --- ฟังก์ชัน: ขายอัตโนมัติ (0.01s) ---
local autoSellEnabled = false
createToggle(HelpPlayContainer, "ขายอัตโนมัติ", function(state)
    autoSellEnabled = state
    if autoSellEnabled then
        task.spawn(function()
            while autoSellEnabled do
                pcall(function()
                    ReplicatedStorage:WaitForChild("RequestSell"):InvokeServer("sellAll")
                end)
                task.wait(0.01)
            end
        end)
    end
end)

-- --- ฟังก์ชัน: เดินผ่านแล้วเก็บอัตโนมัติ (กรองข้ามร้านค้า/NPC 100%) ---
local autoHarvestEnabled = false

-- รายการคำที่ไม่เอา (Blacklist ร้านค้า/NPC/ตัวละคร/พื้นดิน)
local shopBlacklist = {"npc", "shop", "merchant", "seller", "vendor", "store", "ร้าน", "ร้านค้า", "คุย", "talk", "buy", "ซื้อ", "dirt", "plot", "farm", "grass", "baseplate", "ground", "floor"}

local function isValidHarvestTarget(part, prompt)
    -- 1. เช็คว่าอยู่ใน NPC หรือผู้เล่นอื่นหรือไม่ (ถ้ามี Humanoid ใน Model บล็อกทันที)
    local parentModel = part:FindFirstAncestorOfClass("Model")
    if parentModel and parentModel:FindFirstChildOfClass("Humanoid") then
        return false
    end

    local partName = string.lower(part.Name)
    local parentName = parentModel and string.lower(parentModel.Name) or ""
    
    -- 2. เช็คชื่อ Part หรือ Model ตรงกับ Blacklist ร้านค้าไหม
    for _, badWord in ipairs(shopBlacklist) do
        if string.find(partName, badWord) or string.find(parentName, badWord) then
            return false
        end
    end

    -- 3. ถ้ามี ProximityPrompt ให้เช็คข้อความในปุ่มคุย
    if prompt then
        local actionText = string.lower(tostring(prompt.ActionText))
        local objectText = string.lower(tostring(prompt.ObjectText))
        for _, badWord in ipairs(shopBlacklist) do
            if string.find(actionText, badWord) or string.find(objectText, badWord) then
                return false
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
                    local overlapParams = OverlapParams.new()
                    overlapParams.FilterType = Enum.RaycastFilterType.Exclude
                    overlapParams.FilterDescendantsInstances = {char}

                    local parts = Workspace:GetPartBoundsInRadius(hrp.Position, 50, overlapParams)
                    
                    for _, part in ipairs(parts) do
                        if not autoHarvestEnabled then break end
                        
                        local prompt = part:FindFirstChildWhichIsA("ProximityPrompt") or (part.Parent and part.Parent:FindFirstChildWhichIsA("ProximityPrompt"))
                        
                        -- แตะเก็บเฉพาะเป้าหมายที่ไม่ใช่ NPC หรือ ร้านค้า
                        if isValidHarvestTarget(part, prompt) then
                            if firetouchinterest then
                                firetouchinterest(hrp, part, 0)
                                firetouchinterest(hrp, part, 1)
                            end
                            
                            if prompt then
                                if fireproximityprompt then
                                    fireproximityprompt(prompt)
                                elseif prompt.InputHoldBegin then
                                    prompt:InputHoldBegin()
                                    prompt:InputHoldEnd()
                                end
                            end
                        end
                    end
                end
                task.wait(0.01)
            end
        end)
    end
end)

-- 3. หมวด "ออโต้" (ระบบซื้อเมล็ดอัตโนมัติ)
local AutoContainer = createContainer("ออโต้")

local selectedSeeds = {}
local autoBuyEnabled = false

-- สวิตช์เปิด/ปิดการซื้ออัตโนมัติรวม
createToggle(AutoContainer, "ซื้อเมล็ดอัตโนมัติ", function(state)
    autoBuyEnabled = state
    if autoBuyEnabled then
        task.spawn(function()
            while autoBuyEnabled do
                for seedName, isSelected in pairs(selectedSeeds) do
                    if not autoBuyEnabled then break end
                    if isSelected then
                        pcall(function()
                            ReplicatedStorage:WaitForChild("RequestPurchase"):InvokeServer(seedName)
                        end)
                    end
                end
                task.wait(0.05)
            end
        end)
    end
end)

-- หัวข้อแนะนำ
local SubTitle = Instance.new("TextLabel")
SubTitle.Size = UDim2.new(0.92, 0, 0, 16)
SubTitle.BackgroundTransparency = 1
SubTitle.Font = Enum.Font.GothamBold
SubTitle.Text = "--- เลือกเมล็ดที่ต้องการซื้อ ---"
SubTitle.TextColor3 = Color3.fromRGB(0, 162, 255)
SubTitle.TextSize = 8
SubTitle.Parent = AutoContainer

-- รายชื่อเมล็ดภาษาอังกฤษทั้งหมดจากรูปภาพ (รวมทั้งหมด 24 ชนิด)
local seedList = {
    {name = "Carrot", label = "Carrot (แครอท)"},
    {name = "Miki", label = "Miki Seed"},
    {name = "MedTrad", label = "MedTrad Seed"},
    {name = "EmoeHair", label = "EmoeHair Seed"},
    {name = "Siw", label = "Siw Seed"},
    {name = "Nom", label = "Nom Seed"},
    {name = "Car", label = "Car (รถยนต์)"},
    {name = "House", label = "House (บ้าน)"},
    {name = "MysteryRocket", label = "MysteryRocket Seed"},
    {name = "MysteryStick", label = "MysteryStick Seed"},
    {name = "Bamboo", label = "Bamboo (ไม้ไผ่)"},
    {name = "Eggkapok", label = "Eggkapok Seed"},
    {name = "MrGreed", label = "MrGreed Seed"},
    {name = "OldMhoy", label = "OldMhoy Seed"},
    {name = "YoungMhoy", label = "YoungMhoy Seed"},
    {name = "Ong", label = "Ong Seed"},
    {name = "Budha", label = "Budha Seed"},
    {name = "NorTad", label = "NorTad Seed"},
    {name = "RobuxTree", label = "RobuxTree"},
    {name = "Glaed", label = "Glaed (เกล็ด)"},
    {name = "GoldFlower", label = "GoldFlower (ดอกไม้ทองคำ)"},
    {name = "Kee", label = "Kee Seed"},
    {name = "PCX150", label = "PCX150 Seed"},
    {name = "MysteryStickTree", label = "MysteryStickTree Seed"}
}

-- สร้างสวิตช์เลือกซื้อเมล็ดแต่ละชนิด
for _, seedData in ipairs(seedList) do
    createToggle(AutoContainer, seedData.label, function(state)
        selectedSeeds[seedData.name] = state
    end)
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

-- เปิดหน้าแรกตั้งต้นที่ "เมนูหลัก"
switchTab("เมนูหลัก")

-- ==================== ลำดับการทำงาน (Loading & Animation) ====================
task.spawn(function()
    -- 1. กำลังโหลด... 2 วินาที
    local count = 0
    while count < 6 do
        LoadText.Text = "กำลังโหลด" .. string.rep(".", (count % 4))
        task.wait(0.3)
        count += 1
    end
    
    -- 2. ดาวน์โหลดเสร็จแล้ว 1 วินาที
    LoadText.Text = "ดาวน์โหลดเสร็จแล้ว"
    task.wait(1)
    
    -- 3. คัดลอกลิงก์อัตโนมัติ
    LoadText.Text = "คัดลอกลิงก์ Discord เรียบร้อย"
    if setclipboard then
        setclipboard("https://discord.gg/Ugssja4wqQ")
    end
    task.wait(1)
    
    -- 4. ลบหน้าจอโหลดทิ้ง
    LoadFrame:Destroy()
    
    -- 5. เล่น Animation แสดง UI หลัก
    MainFrame.Visible = true
    MainFrame.Size = UDim2.new(0, 260, 0, 140)
    
    local info = TweenInfo.new(0.7, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
    local expandTween = TweenService:Create(MainFrame, info, {
        Size = UDim2.new(0, 350, 0, 210),
        BackgroundTransparency = 0
    })
    
    expandTween:Play()
end)
