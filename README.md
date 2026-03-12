------- สคริปบิน


local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = game.Players.LocalPlayer

local flying = false
local speed = 100
local bv, bg

-- ฟังก์ชันดึงตัวละคร (กันบัคตอนตาย)
local function getChar()
    local char = player.Character or player.CharacterAdded:Wait()
    local root = char:WaitForChild("HumanoidRootPart")
    return char, root
end

-- === [ สร้าง UI ] ===
local sg = Instance.new("ScreenGui", player.PlayerGui)
sg.Name = "FlyGui"
sg.ResetOnSpawn = false -- ให้ UI อยู่ต่อแม้จะตาย

local frame = Instance.new("Frame", sg)
frame.Size = UDim2.new(0, 150, 0, 100)
frame.Position = UDim2.new(0, 20, 0.5, -50)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true 

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 25)
title.Text = "Gohken "
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

local toggleBtn = Instance.new("TextButton", frame)
toggleBtn.Size = UDim2.new(0.9, 0, 0, 30)
toggleBtn.Position = UDim2.new(0.05, 0, 0.3, 0)
toggleBtn.Text = "Fly: OFF"
toggleBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
toggleBtn.TextColor3 = Color3.new(1, 1, 1)

local speedInput = Instance.new("TextBox", frame)
speedInput.Size = UDim2.new(0.9, 0, 0, 25)
speedInput.Position = UDim2.new(0.05, 0, 0.65, 0)
speedInput.Text = "100"
speedInput.PlaceholderText = "Speed"

-- === [ ระบบการบิน ] ===
local function toggleFly()
    local character, root = getChar()
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

toggleBtn.MouseButton1Click:Connect(toggleFly)
speedInput.FocusLost:Connect(function() speed = tonumber(speedInput.Text) or 100 end)

RunService.RenderStepped:Connect(function()
    if flying then
        local char, root = getChar() -- เช็ค Root ตลอดเวลา
        local camera = workspace.CurrentCamera
        local direction = Vector3.new(0, 0, 0)
        
        if UIS:IsKeyDown(Enum.KeyCode.W) then direction = direction + camera.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.S) then direction = direction - camera.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.A) then direction = direction - camera.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.D) then direction = direction + camera.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.Space) then direction = direction + Vector3.new(0, 1, 0) end
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then direction = direction - Vector3.new(0, 1, 0) end
        
        if bg then bg.CFrame = camera.CFrame end
        if bv then bv.Velocity = direction * speed end
    end
end)
