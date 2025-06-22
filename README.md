local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- สร้าง ScreenGui + กรอบ Spectator UI
local screenGui = Instance.new("ScreenGui", PlayerGui)
screenGui.Name = "SpectatorUI"
screenGui.ResetOnSpawn = false

local frame = Instance.new("Frame", screenGui)
frame.Name = "SpectateFrame"
frame.Size = UDim2.new(0, 200, 0, 120)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
frame.Visible = false

local title = Instance.new("TextLabel", frame)
title.Name = "Title"
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Text = "Spectating: -"

-- ปุ่ม Prev, Next, Close
local prevBtn = Instance.new("TextButton", frame)
prevBtn.Name = "Prev"
prevBtn.Size = UDim2.new(0, 60, 0, 30)
prevBtn.Position = UDim2.new(0, 10, 0, 50)
prevBtn.Text = "<"
local stopBtn = Instance.new("TextButton", frame)
stopBtn.Name = "Stop"
stopBtn.Size = UDim2.new(0, 60, 0, 30)
stopBtn.Position = UDim2.new(0, 70, 0, 50)
stopBtn.Text = "✖"
local nextBtn = Instance.new("TextButton", frame)
nextBtn.Name = "Next"
nextBtn.Size = UDim2.new(0, 60, 0, 30)
nextBtn.Position = UDim2.new(0, 130, 0, 50)
nextBtn.Text = ">"

-- ตัวแปรควบคุม Spectate
local playerList = Players:GetPlayers()
local index = 1
local spectating = false

-- ฟังก์ชันอัปเดตรายชื่อผู้เล่น
local function updateList()
    playerList = Players:GetPlayers()
end

Players.PlayerAdded:Connect(updateList)
Players.PlayerRemoving:Connect(function(pl)
    updateList()
    if spectating and playerList[index] == pl then
        stopBtn:CaptureMouse() -- กดปิดอัตโนมัติ
    end
end)

-- ฟังก์ชันเปลี่ยนกล้อง
local function setCamera(plr)
    if plr and plr.Character and plr.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = plr.Character.Humanoid
        Camera.CameraType = Enum.CameraType.Custom
        title.Text = "Spectating: " .. plr.Name
    end
end

-- เปิด/ปิด Spectate UI
frame.Visible = false

stopBtn.MouseButton1Click:Connect(function()
    spectating = false
    frame.Visible = false
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = LocalPlayer.Character.Humanoid
    end
end)

prevBtn.MouseButton1Click:Connect(function()
    if #playerList < 2 then return end
    index = index - 1
    if index < 1 then index = #playerList end
    setCamera(playerList[index])
end)

nextBtn.MouseButton1Click:Connect(function()
    if #playerList < 2 then return end
    index = index + 1
    if index > #playerList then index = 1 end
    setCamera(playerList[index])
end)

-- ปุ่มเปิด UI
local openBtn = Instance.new("TextButton", screenGui)
openBtn.Name = "OpenSpectate"
openBtn.Size = UDim2.new(0, 120, 0, 40)
openBtn.Position = UDim2.new(0, 20, 0, 20)
openBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
openBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
openBtn.Font = Enum.Font.GothamBold
openBtn.TextScaled = true
openBtn.Text = "Spectate"

openBtn.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
    if frame.Visible then
        spectating = true
        updateList()
        index = 1
        setCamera(playerList[index])
    else
        stopBtn:CaptureMouse()
    end
end)

return

