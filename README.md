local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GohkenUI"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- เฟรมหลัก (Main Frame)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 260, 0, 380)
MainFrame.Position = UDim2.new(0.5, -130, 0.5, -190)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18) -- ดำเข้มเกือบสนิท
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 4)
Corner.Parent = MainFrame

-- ชื่อเมนู (GOHKEN)
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -20, 0, 40)
Title.Position = UDim2.new(0, 15, 0, 5)
Title.BackgroundTransparency = 1
Title.Text = "GOHKEN"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.Parent = MainFrame

-- ปุ่มเมนูหลัก (Main Tab)
local MainButton = Instance.new("TextButton")
MainButton.Size = UDim2.new(1, -30, 0, 38)
MainButton.Position = UDim2.new(0, 15, 0, 50)
MainButton.BackgroundColor3 = Color3.fromRGB(235, 65, 30) -- สีส้มแดงสด
MainButton.Text = "Main"
MainButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MainButton.Font = Enum.Font.GothamMedium
MainButton.TextSize = 15
MainButton.AutoButtonColor = true
MainButton.Parent = MainFrame

local BtnCorner = Instance.new("UICorner")
BtnCorner.CornerRadius = UDim.new(0, 6)
BtnCorner.Parent = MainButton

--- ส่วนของหัวข้อ Settings ---
local function CreateSectionLabel(text, posY)
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -30, 0, 30)
    Label.Position = UDim2.new(0, 15, 0, posY)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(220, 220, 220)
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Font = Enum.Font.GothamMedium
    Label.TextSize = 14
    Label.Parent = MainFrame
    return Label
end

CreateSectionLabel("Aimbot Settings", 100)

-- ตัวอย่างปุ่มเปิด/ปิด (Toggle Style)
local EnableToggle = Instance.new("TextButton")
EnableToggle.Size = UDim2.new(1, -30, 0, 30)
EnableToggle.Position = UDim2.new(0, 15, 0, 135)
EnableToggle.BackgroundTransparency = 1
EnableToggle.Text = "     Enable"
EnableToggle.TextColor3 = Color3.fromRGB(200, 200, 200)
EnableToggle.TextXAlignment = Enum.TextXAlignment.Left
EnableToggle.Font = Enum.Font.Gotham
EnableToggle.TextSize = 14
EnableToggle.Parent = MainFrame

-- วงกลม Toggle ข้างหน้า
local Circle = Instance.new("Frame")
Circle.Size = UDim2.new(0, 18, 0, 18)
Circle.Position = UDim2.new(0, 0, 0.5, -9)
Circle.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
Circle.Parent = EnableToggle

local CircleStroke = Instance.new("UIStroke")
CircleStroke.Thickness = 2
CircleStroke.Color = Color3.fromRGB(235, 65, 30)
CircleStroke.Parent = Circle

local CircleCorner = Instance.new("UICorner")
CircleCorner.CornerRadius = UDim.new(1, 0)
CircleCorner.Parent = Circle
