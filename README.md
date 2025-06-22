local Players = game:GetService("Players")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- สร้าง UI หลัก
local screenGui = Instance.new("ScreenGui", PlayerGui)
screenGui.Name = "SpectateListUI"
screenGui.ResetOnSpawn = false

-- ปุ่มเปิด UI
local openBtn = Instance.new("TextButton", screenGui)
openBtn.Name = "OpenSpectate"
openBtn.Size = UDim2.new(0, 120, 0, 40)
openBtn.Position = UDim2.new(0, 20, 0, 20)
openBtn.Text = "Spectate"
openBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
openBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
openBtn.Font = Enum.Font.GothamBold
openBtn.TextScaled = true

-- Frame แสดงลิสต์ผู้เล่น
local listFrame = Instance.new("ScrollingFrame", screenGui)
listFrame.Name = "ListFrame"
listFrame.Size = UDim2.new(0, 200, 0, 300)
listFrame.Position = UDim2.new(0, 20, 0, 70)
listFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
listFrame.Visible = false
listFrame.CanvasSize = UDim2.new(0, 0, 0, 0)

-- โครงสร้างชุดปุ่มภายในลิสต์
local UIListLayout = Instance.new("UIListLayout", listFrame)
UIListLayout.Padding = UDim.new(0, 5)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder

-- ปุ่มปิด Spectate
local stopBtn = Instance.new("TextButton", screenGui)
stopBtn.Name = "StopSpectate"
stopBtn.Size = UDim2.new(0, 120, 0, 40)
stopBtn.Position = UDim2.new(0, 150, 0, 20)
stopBtn.Text = "Stop"
stopBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
stopBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
stopBtn.Font = Enum.Font.GothamBold
stopBtn.TextScaled = true
stopBtn.Visible = false

-- Variables
local viewing = false

-- อัปเดตรายชื่อผู้เล่น
local function refreshList()
    -- ล้างลิสต์เก่า
    for _, child in ipairs(listFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    -- เพิ่มผู้เล่นใหม่
    local y = 0
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("Humanoid") then
            local btn = Instance.new("TextButton", listFrame)
            btn.Size = UDim2.new(1, -10, 0, 30)
            btn.Position = UDim2.new(0, 5, 0, y)
            btn.Text = plr.Name
            btn.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
            btn.TextColor3 = Color3.new(1, 1, 1)
            btn.Font = Enum.Font.Gotham
            btn.TextScaled = true
            y = y + 35

            btn.MouseButton1Click:Connect(function()
                if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                    Camera.CameraSubject = plr.Character.Humanoid
                    viewing = true
                    stopBtn.Visible = true
                    listFrame.Visible = false
                end
            end)
        end
    end
    listFrame.CanvasSize = UDim2.new(0, 0, 0, y)
end

-- ปุ่มเปิด/ปิด List UI และรีเฟรชรายชื่อ
openBtn.MouseButton1Click:Connect(function()
    listFrame.Visible = not listFrame.Visible
    if listFrame.Visible then refreshList() end
end)

-- ปุ่มหยุด Spectate กลับมาที่ตัวเอง
stopBtn.MouseButton1Click:Connect(function()
    viewing = false
    stopBtn.Visible = false
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = LocalPlayer.Character.Humanoid
    end
end)

-- รีเฟรชรายชื่อเมื่อผู้เล่นเข้า/ออก
Players.PlayerAdded:Connect(function()
    if listFrame.Visible then refreshList() end
end)
Players.PlayerRemoving:Connect(function()
    if listFrame.Visible then refreshList() end
end)
