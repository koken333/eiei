local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- ===== GUI หลัก =====
local mainGui = Instance.new("ScreenGui", PlayerGui)
mainGui.Name = "MainSpectateESP_UI"
mainGui.ResetOnSpawn = false

-- ===== ปุ่มเปิด/ปิดเมนูหลัก =====
local mainToggle = Instance.new("TextButton", mainGui)
mainToggle.Size = UDim2.new(0, 160, 0, 40)
mainToggle.Position = UDim2.new(0, 20, 0, 20)
mainToggle.BackgroundColor3 = Color3.fromRGB(30,30,40)
mainToggle.TextColor3 = Color3.new(1,1,1)
mainToggle.Font = Enum.Font.GothamBold
mainToggle.TextScaled = true
mainToggle.Text = "🔧 แสดงเมนู"
mainToggle.Draggable = true

-- ===== เฟรม UI เมนู ESP + Spectate =====
local frame = Instance.new("Frame", mainGui)
frame.Size = UDim2.new(0, 240, 0, 300)
frame.Position = UDim2.new(0, 20, 0, 70)
frame.BackgroundColor3 = Color3.fromRGB(40,40,50)
frame.Visible = false

-- ===== ปุ่มสลับแสดง/ซ่อน UI =====
mainToggle.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
	mainToggle.Text = frame.Visible and "❌ ซ่อนเมนู" or "🔧 แสดงเมนู"
end)

-- ===== ปุ่ม ESP =====
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

-- ===== สร้าง ESP =====
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

-- ===== ระบบ Spectate =====
local spectating = false
local playerList = {}
local currentIndex = 1

local dropdown = Instance.new("TextButton", frame)
dropdown.Size = UDim2.new(0, 200, 0, 40)
dropdown.Position = UDim2.new(0, 20, 0, 70)
dropdown.BackgroundColor3 = Color3.fromRGB(70,70,90)
dropdown.TextColor3 = Color3.new(1,1,1)
dropdown.Font = Enum.Font.Gotham
dropdown.TextScaled = true
dropdown.Text = "👁 เลือกผู้เล่น"

-- ปุ่มหยุด
local stopBtn = Instance.new("TextButton", frame)
stopBtn.Size = UDim2.new(0, 200, 0, 40)
stopBtn.Position = UDim2.new(0, 20, 0, 120)
stopBtn.BackgroundColor3 = Color3.fromRGB(80,20,20)
stopBtn.TextColor3 = Color3.new(1,1,1)
stopBtn.Font = Enum.Font.GothamBold
stopBtn.TextScaled = true
stopBtn.Text = "✖ หยุดดู"

local function updateDropdown()
	playerList = {}
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer then
			table.insert(playerList, plr)
		end
	end

	if #playerList > 0 then
		dropdown.Text = "👁 เลือกผู้เล่น (" .. #playerList .. ")"
	else
		dropdown.Text = "ไม่มีผู้เล่นให้ดู"
	end
end

dropdown.MouseButton1Click:Connect(function()
	updateDropdown()
	if #playerList == 0 then return end

	currentIndex = (currentIndex % #playerList) + 1
	local target = playerList[currentIndex]
	if target and target.Character and target.Character:FindFirstChild("Humanoid") then
		Camera.CameraSubject = target.Character.Humanoid
		Camera.CameraType = Enum.CameraType.Custom
		dropdown.Text = "🎥 กำลังดู: " .. target.Name
		spectating = true
	end
end)

stopBtn.MouseButton1Click:Connect(function()
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
		Camera.CameraSubject = LocalPlayer.Character.Humanoid
	end
	spectating = false
	dropdown.Text = "👁 เลือกผู้เล่น"
end)

-- อัปเดตอัตโนมัติเมื่อมีผู้เล่นเข้า/ออก
Players.PlayerAdded:Connect(updateDropdown)
Players.PlayerRemoving:Connect(updateDropdown)
updateDropdown()
