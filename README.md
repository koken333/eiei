local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- ===== GUI หลัก =====
local mainGui = Instance.new("ScreenGui", PlayerGui)
mainGui.Name = "SpectateESP_UI"
mainGui.ResetOnSpawn = false

-- ปุ่มเปิด/ปิดเมนูหลัก
local mainToggle = Instance.new("TextButton", mainGui)
mainToggle.Size = UDim2.new(0, 160, 0, 40)
mainToggle.Position = UDim2.new(0, 20, 0, 20)
mainToggle.BackgroundColor3 = Color3.fromRGB(30,30,40)
mainToggle.TextColor3 = Color3.new(1,1,1)
mainToggle.Font = Enum.Font.GothamBold
mainToggle.TextScaled = true
mainToggle.Text = "🔧 แสดงเมนู"
mainToggle.Draggable = true

-- เฟรมเมนู
local frame = Instance.new("Frame", mainGui)
frame.Size = UDim2.new(0, 240, 0, 400)
frame.Position = UDim2.new(0, 20, 0, 70)
frame.BackgroundColor3 = Color3.fromRGB(40,40,50)
frame.Visible = false

-- กดพับ/ขยายเมนู
mainToggle.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
    mainToggle.Text = frame.Visible and "❌ ซ่อนเมนู" or "🔧 แสดงเมนู"
end)

-- ===== ESP =====
getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

local toggleESP = Instance.new("TextButton", frame)
toggleESP.Size = UDim2.new(0, 200, 0, 40)
toggleESP.Position = UDim2.new(0, 20, 0, 10)
toggleESP.BackgroundColor3 = Color3.fromRGB(60,60,80)
toggleESP.TextColor3 = Color3.new(1,1,1)
toggleESP.Font = Enum.Font.GothamBold
toggleESP.TextScaled = true
toggleESP.Text = "🔍 เปิด ESP"

toggleESP.MouseButton1Click:Connect(function()
    getgenv().ESPEnabled = not getgenv().ESPEnabled
    toggleESP.Text = getgenv().ESPEnabled and "❌ ปิด ESP" or "🔍 เปิด ESP"
end)

-- สร้าง ESP
local function createESP(player)
    if player == LocalPlayer then return end
    local function onChar(char)
        local head = char:WaitForChild("Head", 10)
        if not head then return end
        local tag = Instance.new("BillboardGui")
        tag.Name = "ESP_Tag"
        tag.Adornee = head
        tag.Size = UDim2.new(0, 120, 0, 25)
        tag.StudsOffset = Vector3.new(0, 2.7, 0)
        tag.AlwaysOnTop = true
        tag.Parent = char

        local label = Instance.new("TextLabel", tag)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.TextColor3 = ESPColor
        label.TextStrokeTransparency = 0.4
        label.TextScaled = true
        label.Font = Enum.Font.GothamSemibold
        label.Text = player.Name

        RunService.RenderStepped:Connect(function()
            if getgenv().ESPEnabled and char.Parent then
                local hrp = char:FindFirstChild("HumanoidRootPart")
                local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if hrp and myHRP then
                    local dist = (hrp.Position - myHRP.Position).Magnitude
                    label.Text = player.Name .. " [" .. math.floor(dist) .. "m]"
                    tag.Enabled = true
                end
            else
                tag.Enabled = false
            end
        end)
    end

    player.CharacterAdded:Connect(onChar)
    if player.Character then onChar(player.Character) end
end

for _, p in pairs(Players:GetPlayers()) do createESP(p) end
Players.PlayerAdded:Connect(createESP)

-- ===== Spectate =====
local spectating = false
local dropdown = Instance.new("ScrollingFrame", frame)
dropdown.Size = UDim2.new(0, 200, 0, 260)
dropdown.Position = UDim2.new(0, 20, 0, 60)
dropdown.BackgroundColor3 = Color3.fromRGB(50,50,70)
dropdown.CanvasSize = UDim2.new(0, 0, 1, 0)
dropdown.Visible = true

local layout = Instance.new("UIListLayout", dropdown)
layout.SortOrder = Enum.SortOrder.Name

local stopBtn = Instance.new("TextButton", frame)
stopBtn.Size = UDim2.new(0,200,0,40)
stopBtn.Position = UDim2.new(0,20,0,330)
stopBtn.BackgroundColor3 = Color3.fromRGB(80,20,20)
stopBtn.TextColor3 = Color3.new(1,1,1)
stopBtn.Font = Enum.Font.GothamBold
stopBtn.TextScaled = true
stopBtn.Text = "✖ หยุดดู"
stopBtn.Visible = false

local function refreshDropdown()
    for _, child in pairs(dropdown:GetChildren()) do
        if child:IsA("TextButton") then child:Destroy() end
    end
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local b = Instance.new("TextButton", dropdown)
            b.Size = UDim2.new(1, -10, 0, 30)
            b.Position = UDim2.new(0, 5, 0, 0)
            b.BackgroundColor3 = Color3.fromRGB(70,70,90)
            b.TextColor3 = Color3.new(1,1,1)
            b.Font = Enum.Font.Gotham
            b.TextScaled = true
            b.Text = plr.Name
            b.MouseButton1Click:Connect(function()
                spectating = true
                stopBtn.Visible = true
                Camera.CameraSubject = plr.Character and plr.Character:FindFirstChild("Humanoid") or Camera.CameraSubject
            end)
        end
    end
end

stopBtn.MouseButton1Click:Connect(function()
    spectating = false
    stopBtn.Visible = false
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = LocalPlayer.Character.Humanoid
    end
end)

Players.PlayerAdded:Connect(function()
    refreshDropdown()
end)
Players.PlayerRemoving:Connect(function()
    refreshDropdown()
end)

-- เริ่มต้น
refreshDropdown()
