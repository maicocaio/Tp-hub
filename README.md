-- TELEPORT HUB PRO FINAL
-- Criar / Apagar Aba | Criar / Apagar TP | Mobile | Estável

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer

local gui = Instance.new("ScreenGui")
gui.Name = "TeleportHubPro"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- CORES
local NEON = Color3.fromRGB(180,0,255)
local BG = Color3.fromRGB(14,14,18)
local BG2 = Color3.fromRGB(22,22,30)
local BTN = Color3.fromRGB(40,40,55)
local SELECT = Color3.fromRGB(70,40,100)

local function round(o,r)
	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0,r)
	c.Parent = o
end

-- PAINEL
local panel = Instance.new("Frame", gui)
panel.Size = UDim2.new(0,520,0,380)
panel.Position = UDim2.new(0.5,-260,0.5,-190)
panel.BackgroundColor3 = BG
panel.BorderSizePixel = 0
panel.Active = true
round(panel,16)

-- TOPO
local top = Instance.new("Frame", panel)
top.Size = UDim2.new(1,0,0,40)
top.BackgroundTransparency = 1

local title = Instance.new("TextLabel", top)
title.Size = UDim2.new(1,-60,1,0)
title.Position = UDim2.new(0,16,0,0)
title.BackgroundTransparency = 1
title.Text = "Teleport HUB PRO"
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextXAlignment = Enum.TextXAlignment.Left
title.TextColor3 = NEON

local minimize = Instance.new("TextButton", top)
minimize.Size = UDim2.new(0,30,0,30)
minimize.Position = UDim2.new(1,-40,0,5)
minimize.Text = "—"
minimize.Font = Enum.Font.GothamBold
minimize.TextSize = 18
minimize.TextColor3 = NEON
minimize.BackgroundColor3 = BTN
minimize.BorderSizePixel = 0
round(minimize,8)

-- ABAS
local tabsFrame = Instance.new("ScrollingFrame", panel)
tabsFrame.Position = UDim2.new(0,10,0,50)
tabsFrame.Size = UDim2.new(0,130,1,-60)
tabsFrame.ScrollBarThickness = 6
tabsFrame.BackgroundColor3 = BG2
tabsFrame.BorderSizePixel = 0
round(tabsFrame,12)

local tabLayout = Instance.new("UIListLayout", tabsFrame)
tabLayout.Padding = UDim.new(0,6)
tabLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
tabLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	tabsFrame.CanvasSize = UDim2.new(0,0,0,tabLayout.AbsoluteContentSize.Y + 10)
end)

-- CONTEÚDO
local content = Instance.new("Frame", panel)
content.Position = UDim2.new(0,150,0,50)
content.Size = UDim2.new(1,-160,1,-60)
content.BackgroundColor3 = BG2
content.BorderSizePixel = 0
round(content,12)

-- CONTROLES
local controls = Instance.new("Frame", content)
controls.Position = UDim2.new(0,10,0,10)
controls.Size = UDim2.new(1,-20,0,180)
controls.BackgroundTransparency = 1

local grid = Instance.new("UIGridLayout", controls)
grid.CellSize = UDim2.new(0,160,0,34)
grid.CellPadding = UDim2.new(0,10,0,10)

local function box(ph)
	local t = Instance.new("TextBox")
	t.PlaceholderText = ph
	t.Font = Enum.Font.Gotham
	t.TextSize = 14
	t.TextColor3 = NEON
	t.BackgroundColor3 = BTN
	t.BorderSizePixel = 0
	round(t,8)
	return t
end

local function button(txt)
	local b = Instance.new("TextButton")
	b.Text = txt
	b.Font = Enum.Font.GothamBold
	b.TextSize = 14
	b.TextColor3 = NEON
	b.BackgroundColor3 = BTN
	b.BorderSizePixel = 0
	round(b,8)
	return b
end

local tabName = box("Nome da Aba")
tabName.Parent = controls

local createTabBtn = button("Criar Aba")
createTabBtn.Parent = controls

local deleteTabBtn = button("Apagar Aba")
deleteTabBtn.Parent = controls

local tpName = box("Nome do TP")
tpName.Parent = controls

local createTPBtn = button("Criar TP")
createTPBtn.Parent = controls

local deleteTPBtn = button("Apagar TP")
deleteTPBtn.Parent = controls

local xyzBox = box("X,Y,Z")
xyzBox.Parent = controls

local copyBtn = button("Copiar XYZ")
copyBtn.Parent = controls

-- LISTA TPS
local tpList = Instance.new("ScrollingFrame", content)
tpList.Position = UDim2.new(0,10,0,200)
tpList.Size = UDim2.new(1,-20,1,-210)
tpList.ScrollBarThickness = 6
tpList.BackgroundTransparency = 1

local tpLayout = Instance.new("UIListLayout", tpList)
tpLayout.Padding = UDim.new(0,6)
tpLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	tpList.CanvasSize = UDim2.new(0,0,0,tpLayout.AbsoluteContentSize.Y + 10)
end)

-- SISTEMA
local tabs = {}
local currentTab = nil
local selectedTP = nil

local function teleportSafe(pos)
	local char = player.Character
	if not char then return end
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if hrp then
		hrp.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
	end
end

local function selectTab(tab)
	if currentTab then
		currentTab.frame.Visible = false
		currentTab.button.BackgroundColor3 = BG
	end
	currentTab = tab
	selectedTP = nil
	tab.frame.Visible = true
	tab.button.BackgroundColor3 = BTN
end

local function createTab(name)
	local btn = button(name)
	btn.Size = UDim2.new(1,-12,0,32)
	btn.Parent = tabsFrame
	btn.BackgroundColor3 = BG

	local frame = Instance.new("Frame", tpList)
	frame.Size = UDim2.new(1,0,1,0)
	frame.BackgroundTransparency = 1
	frame.Visible = false

	local tab = {button = btn, frame = frame, name = name}
	table.insert(tabs, tab)

	btn.MouseButton1Click:Connect(function()
		selectTab(tab)
	end)

	if not currentTab then
		selectTab(tab)
	end
end

-- TAB INICIAL
createTab("Principal")

-- AÇÕES
createTabBtn.MouseButton1Click:Connect(function()
	if tabName.Text ~= "" then
		createTab(tabName.Text)
		tabName.Text = ""
	end
end)

deleteTabBtn.MouseButton1Click:Connect(function()
	if not currentTab or currentTab.name == "Principal" then return end
	currentTab.button:Destroy()
	currentTab.frame:Destroy()
	for i,v in ipairs(tabs) do
		if v == currentTab then
			table.remove(tabs,i)
			break
		end
	end
	currentTab = nil
	if tabs[1] then selectTab(tabs[1]) end
end)

copyBtn.MouseButton1Click:Connect(function()
	local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
	if hrp then
		local p = hrp.Position
		local txt = math.floor(p.X)..","..math.floor(p.Y)..","..math.floor(p.Z)
		xyzBox.Text = txt
		if setclipboard then setclipboard(txt) end
	end
end)

createTPBtn.MouseButton1Click:Connect(function()
	if not currentTab or tpName.Text == "" then return end
	local c = xyzBox.Text:split(",")
	if #c ~= 3 then return end
	local pos = Vector3.new(tonumber(c[1]),tonumber(c[2]),tonumber(c[3]))

	local b = button(tpName.Text)
	b.Size = UDim2.new(1,-12,0,32)
	b.Parent = currentTab.frame

	b.MouseButton1Click:Connect(function()
		selectedTP = b
		for _,v in ipairs(currentTab.frame:GetChildren()) do
			if v:IsA("TextButton") then
				v.BackgroundColor3 = BTN
			end
		end
		b.BackgroundColor3 = SELECT
		teleportSafe(pos)
	end)

	tpName.Text = ""
end)

deleteTPBtn.MouseButton1Click:Connect(function()
	if selectedTP then
		selectedTP:Destroy()
		selectedTP = nil
	end
end)

-- MINIMIZAR
local openSize = panel.Size
local minSize = UDim2.new(0,220,0,44)
local minimized = false

minimize.MouseButton1Click:Connect(function()
	minimized = not minimized
	TweenService:Create(panel,TweenInfo.new(0.2),{
		Size = minimized and minSize or openSize
	}):Play()
	tabsFrame.Visible = not minimized
	content.Visible = not minimized
end)

-- DRAG
local dragging,startPos,startPanel
panel.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.Touch or i.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		startPos = i.Position
		startPanel = panel.Position
		local mv; mv = UIS.InputChanged:Connect(function(m)
			if dragging and m == i then
				local d = m.Position - startPos
				panel.Position = UDim2.new(startPanel.X.Scale,startPanel.X.Offset+d.X,startPanel.Y.Scale,startPanel.Y.Offset+d.Y)
			end
		end)
		i.Changed:Connect(function()
			if i.UserInputState == Enum.UserInputState.End then
				dragging = false
				mv:Disconnect()
			end
		end)
	end
end)
