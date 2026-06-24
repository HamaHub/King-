-- // HAMA HUB ULTRA-MINI ANTI-DESYNC + SUPER FAST AUTO BAT
-- // Merged UI (Red/Black) + MAX SPEED Engine

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local HttpService = game:GetService("HttpService")
local LP = Players.LocalPlayer
local PlayerGui = LP:WaitForChild("PlayerGui")

-- // SUPER FAST CONFIG
local SPAM_DELAY = 0.001       -- 1ms
local BATCH_SIZE = 10          -- hits per frame
local DESYNC_OFFSET = 0.0001

-- // STATE
local State = {
    autoBatToggled = false,
    hittingCooldown = false,
    guiVisible = true,
    mobileMode = false,
    frameCount = 0,
}

local Keys = {
    autoBat = Enum.KeyCode.X,
    autoBatType = "Keyboard",
}

local h, hrp = nil, nil

-- // CLEANUP OLD GUI
for _, name in pairs({"HamaHubAutoBatGUI", "EnvyAutoBatDesyncGUI", "HamaMiniToggleAntiDesync"}) do
    local old = PlayerGui:FindFirstChild(name)
    if old then old:Destroy() end
end

-- // ========== UI (Ultra-Mini Toggle – Red/Black) ==========
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "HamaMiniToggleAntiDesync"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = PlayerGui

-- Main Frame
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 195, 0, 38)
MainFrame.Position = UDim2.new(0.5, -97, 0.4, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(12, 12, 14)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Parent = ScreenGui

-- Pill corners
local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(1, 0)
Corner.Parent = MainFrame

-- Border (starts white, turns red when active)
local Stroke = Instance.new("UIStroke")
Stroke.Thickness = 1.5
Stroke.Color = Color3.fromRGB(255, 255, 255)
Stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
Stroke.Parent = MainFrame

-- Target Icon
local TargetIcon = Instance.new("TextLabel")
TargetIcon.Size = UDim2.new(0, 18, 0, 18)
TargetIcon.Position = UDim2.new(0, 12, 0.5, -9)
TargetIcon.BackgroundTransparency = 1
TargetIcon.Text = "◎"
TargetIcon.TextColor3 = Color3.fromRGB(255, 255, 255)
TargetIcon.TextSize = 16
TargetIcon.Font = Enum.Font.GothamBold
TargetIcon.Parent = MainFrame

-- Interactive Toggle Button
local StatusBtn = Instance.new("TextButton")
StatusBtn.Size = UDim2.new(0, 100, 0, 18)
StatusBtn.Position = UDim2.new(0, 34, 0.5, -9)
StatusBtn.BackgroundTransparency = 1
StatusBtn.Text = "ANTI DESYNC"
StatusBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
StatusBtn.TextSize = 12
StatusBtn.Font = Enum.Font.GothamBold
StatusBtn.TextXAlignment = Enum.TextXAlignment.Left
StatusBtn.Parent = MainFrame

-- Right Button Group (Lock only)
local ButtonGroup = Instance.new("Frame")
ButtonGroup.Size = UDim2.new(0, 25, 0, 20)
ButtonGroup.Position = UDim2.new(1, -35, 0.5, -10)
ButtonGroup.BackgroundTransparency = 1
ButtonGroup.Parent = MainFrame

local Layout = Instance.new("UIListLayout")
Layout.FillDirection = Enum.FillDirection.Horizontal
Layout.HorizontalAlignment = Enum.HorizontalAlignment.Right
Layout.VerticalAlignment = Enum.VerticalAlignment.Center
Layout.Parent = ButtonGroup

-- Lock Button
local LockBtn = Instance.new("TextButton")
LockBtn.Size = UDim2.new(0, 20, 0, 20)
LockBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
LockBtn.Text = "🔓"
LockBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
LockBtn.TextSize = 10
LockBtn.Font = Enum.Font.GothamBold
LockBtn.Parent = ButtonGroup

local LockCorner = Instance.new("UICorner")
LockCorner.CornerRadius = UDim.new(1, 0)
LockCorner.Parent = LockBtn

-- // ========== UI LOGIC (Toggle, Lock, Drag) ==========
local isLocked = false
local dragging = false
local dragInput, dragStart, startPos

-- Toggle Auto Bat (Red/White)
StatusBtn.MouseButton1Click:Connect(function()
    State.autoBatToggled = not State.autoBatToggled
    local targetColor = State.autoBatToggled and Color3.fromRGB(255, 68, 85) or Color3.fromRGB(255, 255, 255)
    TweenService:Create(Stroke, TweenInfo.new(0.15), {Color = targetColor}):Play()
    TweenService:Create(TargetIcon, TweenInfo.new(0.15), {TextColor3 = targetColor}):Play()
    TweenService:Create(StatusBtn, TweenInfo.new(0.15), {TextColor3 = targetColor}):Play()
end)

-- Lock Toggle
LockBtn.MouseButton1Click:Connect(function()
    isLocked = not isLocked
    if isLocked then
        LockBtn.Text = "🔒"
        Stroke.Transparency = 0.5
        TargetIcon.TextTransparency = 0.3
        StatusBtn.TextTransparency = 0.3
    else
        LockBtn.Text = "🔓"
        Stroke.Transparency = 0
        TargetIcon.TextTransparency = 0
        StatusBtn.TextTransparency = 0
    end
end)

-- Drag (mobile/PC)
local function update(input)
    if isLocked then return end
    local delta = input.Position - dragStart
    local targetPos = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    TweenService:Create(MainFrame, TweenInfo.new(0.06, Enum.EasingStyle.Linear), {Position = targetPos}):Play()
end

MainFrame.InputBegan:Connect(function(input)
    if (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
        if isLocked then return end
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

MainFrame.InputChanged:Connect(function(input)
    if (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        dragInput = input
    end
end)

UIS.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)

-- // ========== SUPER FAST AUTO BAT ENGINE ==========
local function getBat()
    local char = LP.Character
    if not char then return nil end
    local tool = char:FindFirstChild("Bat")
    if tool then return tool end
    local bp = LP:FindFirstChild("Backpack")
    if bp then
        tool = bp:FindFirstChild("Bat")
        if tool then
            tool.Parent = char
            return tool
        end
    end
    return nil
end

local function tryHitBat()
    if State.hittingCooldown then return end
    local bat = getBat()
    if not bat then return end
    State.hittingCooldown = true

    pcall(function() bat:Activate() end)
    pcall(function()
        local ev = bat:FindFirstChildWhichIsA("RemoteEvent")
        if ev then ev:FireServer() end
    end)
    pcall(function()
        for _, child in pairs(bat:GetChildren()) do
            if child:IsA("RemoteEvent") then child:FireServer() end
        end
    end)

    local desyncDelay = SPAM_DELAY + (math.random() * DESYNC_OFFSET)
    task.delay(desyncDelay, function() State.hittingCooldown = false end)
end

local function batchHit()
    if not State.autoBatToggled or not h or not hrp then return end
    for i = 1, BATCH_SIZE do
        tryHitBat()
    end
end

local function getClosestPlayer()
    if not hrp then return nil, math.huge end
    local cp, cd = nil, math.huge
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LP and p.Character then
            local tr = p.Character:FindFirstChild("HumanoidRootPart")
            if tr then
                local d = (hrp.Position - tr.Position).Magnitude
                if d < cd then cd = d; cp = p end
            end
        end
    end
    return cp, cd
end

-- Character setup
local function setupChar(char)
    task.wait(0.1)
    h = char:WaitForChild("Humanoid", 5)
    hrp = char:WaitForChild("HumanoidRootPart", 5)
end

LP.CharacterAdded:Connect(setupChar)
if LP.Character then task.spawn(function() setupChar(LP.Character) end) end

-- // MULTI-LAYER HIT LOOPS (every frame)
RunService.Heartbeat:Connect(function()
    if not (State.autoBatToggled and h and hrp) then return end
    local target = getClosestPlayer()
    if target and target.Character then
        local tr = target.Character:FindFirstChild("HumanoidRootPart")
        if tr then
            -- Aimbot (undetected)
            local targetPos = tr.Position + Vector3.new(0, 1.2, 0)
            if (hrp.Position - targetPos).Magnitude > 5 then
                hrp.CFrame = CFrame.new(targetPos)
            end
            local cam = workspace.CurrentCamera
            pcall(function()
                cam.CFrame = CFrame.lookAt(cam.CFrame.Position, tr.Position + Vector3.new(0, 0.8, 0))
            end)
            if sethiddenproperty then
                pcall(function() sethiddenproperty(hrp, "PhysicsRepRootPart", tr) end)
            end
            batchHit()
        end
    end
end)

RunService.Stepped:Connect(function()
    if not (State.autoBatToggled and h and hrp) then return end
    batchHit()
end)

RunService.RenderStepped:Connect(function()
    if not (State.autoBatToggled and h and hrp) then return end
    batchHit()
end)

-- // KEYBOARD TOGGLE (X key)
UIS.InputBegan:Connect(function(inp, gp)
    if gp then return end
    if inp.UserInputType == Enum.UserInputType.Keyboard and inp.KeyCode == Keys.autoBat then
        State.autoBatToggled = not State.autoBatToggled
        local targetColor = State.autoBatToggled and Color3.fromRGB(255, 68, 85) or Color3.fromRGB(255, 255, 255)
        TweenService:Create(Stroke, TweenInfo.new(0.15), {Color = targetColor}):Play()
        TweenService:Create(TargetIcon, TweenInfo.new(0.15), {TextColor3 = targetColor}):Play()
        TweenService:Create(StatusBtn, TweenInfo.new(0.15), {TextColor3 = targetColor}):Play()
    end
end)

print("[Hama Hub] Ultra-Mini Anti-Desync + MAX SPEED loaded! Tap the button or press X.")
