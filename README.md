------- สคริปบิน




local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local root = character:WaitForChild("HumanoidRootPart")
local camera = workspace.CurrentCamera

-- ตัวแปรควบคุม
local flying = false
local speed = 100
local bv, bg

-- === [ สร้าง UI ] ===
local sg = Instance.new("ScreenGui", player.PlayerGui)
sg.Name = "FlyGui"

local frame = Instance.new("Frame", sg)
frame.Size = UDim2.new(0, 150, 0, 100)
frame.Position = UDim2.new(0, 20, 0.5, -50)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true -- ลากย้ายที่ได้

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 25)
title.Text = "บิน"
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

-- ปุ่ม เปิด/ปิด
local toggleBtn = Instance.new("TextButton", frame)
toggleBtn.Size = UDim2.new(0.9, 0, 0, 30)
toggleBtn.Position = UDim2.new(0.05, 0, 0.3, 0)
toggleBtn.Text = "Fly: OFF"
toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
toggleBtn.TextColor3 = Color3.new(1, 1, 1)

-- ช่องปรับความเร็ว
local speedInput = Instance.new("TextBox", frame)
speedInput.Size = UDim2.new(0.9, 0, 0, 25)
speedInput.Position = UDim2.new(0.05, 0, 0.65, 0)
speedInput.PlaceholderText = "Speed (100)"
speedInput.Text = "100"
speedInput.BackgroundColor3 = Color3.fromRGB(255, 255, 255)

-- === [ ระบบการบิน ] ===
local function toggleFly()
    flying = not flying
    character.Humanoid.PlatformStand = flying
    if flying then
        toggleBtn.Text = "Fly: ON"
        toggleBtn.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
        bv = Instance.new("BodyVelocity", root)
        bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bv.Velocity = Vector3.new(0, 0, 0)
        bg = Instance.new("BodyGyro", root)
        bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        bg.P = 90000
    else
        toggleBtn.Text = "Fly: OFF"
        toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        if bv then bv:Destroy() end
        if bg then bg:Destroy() end
        character.Humanoid.PlatformStand = false
    end
end

-- อัปเดตความเร็วจาก TextBox
speedInput.FocusLost:Connect(function()
    speed = tonumber(speedInput.Text) or 100
end)

toggleBtn.MouseButton1Click:Connect(toggleFly)

-- ควบคุมทิศทาง
RunService.RenderStepped:Connect(function()
    if flying and root then
        local direction = Vector3.new(0, 0, 0)
        if UIS:IsKeyDown(Enum.KeyCode.W) then direction = direction + camera.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.S) then direction = direction - camera.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.A) then direction = direction - camera.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.D) then direction = direction + camera.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.Space) then direction = direction + Vector3.new(0, 1, 0) end
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then direction = direction - Vector3.new(0, 1, 0) end

        bg.CFrame = camera.CFrame
        bv.Velocity = direction * speed
    end
end)
