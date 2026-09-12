-- MM2 Hub v37.6 (Fixed Slider & Smooth Tween Auto Farm)
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

local getParent = gethui or function() return game:GetService("CoreGui") end
if getParent():FindFirstChild("MM2_UltimateHub_v38") then
    getParent().MM2_UltimateHub_v38:Destroy()
end

-- ============================================================
-- 1. MODERN GUI SETUP
-- ============================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MM2_UltimateHub_v38"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = getParent()

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 250, 0, 450)
MainFrame.Position = UDim2.new(0.05, 0, 0.2, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 17, 23)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 16)

local FrameStroke = Instance.new("UIStroke")
FrameStroke.Thickness = 1.8
FrameStroke.Color = Color3.fromRGB(45, 130, 245)
FrameStroke.Parent = MainFrame

local UIGradient = Instance.new("UIGradient")
UIGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(60, 150, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(150, 60, 255))
})
UIGradient.Rotation = 45
UIGradient.Parent = FrameStroke

local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 40)
Header.BackgroundColor3 = Color3.fromRGB(22, 25, 35)
Header.BorderSizePixel = 0
Header.Parent = MainFrame
Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 16)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(0.7, 0, 1, 0)
Title.Position = UDim2.new(0.06, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 14
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Text = "MM2 HUB v37.6"
Title.Parent = Header

local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Size = UDim2.new(0, 32, 0, 32)
MinimizeBtn.Position = UDim2.new(1, -38, 0.5, -16)
MinimizeBtn.BackgroundTransparency = 1
MinimizeBtn.TextColor3 = Color3.fromRGB(170, 175, 190)
MinimizeBtn.TextSize = 18
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.Text = "−"
MinimizeBtn.Parent = Header

local TabBar = Instance.new("Frame")
TabBar.Size = UDim2.new(1, -20, 0, 34)
TabBar.Position = UDim2.new(0, 10, 0, 48)
TabBar.BackgroundTransparency = 1
TabBar.Parent = MainFrame

local TabListLayout = Instance.new("UIListLayout")
TabListLayout.FillDirection = Enum.FillDirection.Horizontal
TabListLayout.SortOrder = Enum.SortOrder.LayoutOrder
TabListLayout.Padding = UDim.new(0, 6)
TabListLayout.Parent = TabBar

local function createTabBtn(text)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.333, -4, 1, 0)
    btn.BackgroundColor3 = Color3.fromRGB(25, 29, 39)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(150, 155, 175)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 11
    btn.Parent = TabBar
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    local stroke = Instance.new("UIStroke")
    stroke.Thickness = 1
    stroke.Color = Color3.fromRGB(40, 45, 60)
    stroke.Parent = btn
    return btn, stroke
end

local EspTabBtn, EspTabStroke = createTabBtn("ESP")
local FarmTabBtn, FarmTabStroke = createTabBtn("FARM")
local PlayerTabBtn, PlayerTabStroke = createTabBtn("PLAYER")

local function createContainer()
    local scroll = Instance.new("ScrollingFrame")
    scroll.Size = UDim2.new(1, -16, 1, -96)
    scroll.Position = UDim2.new(0, 8, 0, 88)
    scroll.BackgroundTransparency = 1
    scroll.ScrollBarThickness = 4
    scroll.ScrollBarImageColor3 = Color3.fromRGB(60, 130, 245)
    scroll.BorderSizePixel = 0
    scroll.Visible = false
    scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
    scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
    scroll.Parent = MainFrame
    
    local layout = Instance.new("UIListLayout")
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 8)
    layout.Parent = scroll
    
    return scroll
end

local EspContainer = createContainer()
local FarmContainer = createContainer()
local PlayerContainer = createContainer()
EspContainer.Visible = true

local function createButton(parent, text, isAction)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -4, 0, 38)
    btn.BackgroundColor3 = isAction and Color3.fromRGB(35, 110, 220) or Color3.fromRGB(25, 29, 39)
    btn.AutoButtonColor = false
    btn.Text = ""
    btn.Parent = parent
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)
    local stroke = Instance.new("UIStroke")
    stroke.Thickness = 1
    stroke.Color = isAction and Color3.fromRGB(60, 140, 255) or Color3.fromRGB(40, 45, 60)
    stroke.Parent = btn
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -20, 1, 0)
    label.Position = UDim2.new(0, 12, 0, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(230, 235, 245)
    label.TextSize = 12
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Text = text
    label.Parent = btn
    local statusDot = nil
    if not isAction then
        statusDot = Instance.new("Frame")
        statusDot.Size = UDim2.new(0, 8, 0, 8)
        statusDot.Position = UDim2.new(1, -20, 0.5, -4)
        statusDot.BackgroundColor3 = Color3.fromRGB(80, 85, 100)
        statusDot.BorderSizePixel = 0
        statusDot.Parent = btn
        Instance.new("UICorner", statusDot).CornerRadius = UDim.new(1, 0)
    end
    return btn, label, statusDot, stroke
end

-- ESP кнопки
local HighlightBtn, HighlightLabel, HighlightDot, HighlightStroke = createButton(EspContainer, "Player Highlight", false)
local LineBtn, LineLabel, LineDot, LineStroke = createButton(EspContainer, "ESP Line", false)
local GunBtn, GunLabel, GunDot, GunStroke = createButton(EspContainer, "Gun ESP", false)
local TPGunBtn, TPGunLabel = createButton(EspContainer, "Teleport to Gun", true)

-- FARM кнопки
local TPCoinBtn, TPCoinLabel = createButton(FarmContainer, "Teleport to Coin", true)
local AutoFarmBtn, AutoFarmLabel, AutoFarmDot, AutoFarmStroke = createButton(FarmContainer, "Auto Farm (Tween Flight)", false)

-- PLAYER кнопки
local SpeedFrame = Instance.new("Frame")
SpeedFrame.Size = UDim2.new(1, -4, 0, 52)
SpeedFrame.Parent = PlayerContainer
SpeedFrame.BackgroundColor3 = Color3.fromRGB(25, 29, 39)
Instance.new("UICorner", SpeedFrame).CornerRadius = UDim.new(0, 10)
local SpeedStroke = Instance.new("UIStroke")
SpeedStroke.Thickness = 1
SpeedStroke.Color = Color3.fromRGB(40, 45, 60)
SpeedStroke.Parent = SpeedFrame

local SpeedTitle = Instance.new("TextLabel")
SpeedTitle.Size = UDim2.new(0.6, 0, 0.4, 0)
SpeedTitle.Position = UDim2.new(0, 12, 0, 4)
SpeedTitle.BackgroundTransparency = 1
SpeedTitle.Text = "WalkSpeed (16-100)"
SpeedTitle.TextColor3 = Color3.fromRGB(230, 235, 245)
SpeedTitle.Font = Enum.Font.GothamMedium
SpeedTitle.TextSize = 11
SpeedTitle.TextXAlignment = Enum.TextXAlignment.Left
SpeedTitle.Parent = SpeedFrame

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0, 54, 0, 20)
SpeedInput.Position = UDim2.new(1, -66, 0, 4)
SpeedInput.BackgroundColor3 = Color3.fromRGB(15, 17, 23)
SpeedInput.Text = "16"
SpeedInput.TextColor3 = Color3.fromRGB(60, 200, 255)
SpeedInput.Font = Enum.Font.GothamBold
SpeedInput.TextSize = 12
SpeedInput.Parent = SpeedFrame
Instance.new("UICorner", SpeedInput).CornerRadius = UDim.new(0, 6)

local SpeedSliderBg = Instance.new("Frame")
SpeedSliderBg.Size = UDim2.new(1, -24, 0, 6)
SpeedSliderBg.Position = UDim2.new(0, 12, 0, 34)
SpeedSliderBg.BackgroundColor3 = Color3.fromRGB(15, 17, 23)
SpeedSliderBg.Parent = SpeedFrame
Instance.new("UICorner", SpeedSliderBg).CornerRadius = UDim.new(1, 0)

local SpeedSliderFill = Instance.new("Frame")
SpeedSliderFill.Size = UDim2.new(16/100, 0, 1, 0)
SpeedSliderFill.BackgroundColor3 = Color3.fromRGB(60, 140, 255)
SpeedSliderFill.BorderSizePixel = 0
SpeedSliderFill.Parent = SpeedSliderBg
Instance.new("UICorner", SpeedSliderFill).CornerRadius = UDim.new(1, 0)

local NoclipBtn, NoclipLabel, NoclipDot, NoclipStroke = createButton(PlayerContainer, "Noclip", false)
local FlyBtn, FlyLabel, FlyDot, FlyStroke = createButton(PlayerContainer, "Fly", false)
local KillAllBtn, KillAllLabel = createButton(PlayerContainer, "Kill All", true)
local TPMapBtn, TPMapLabel = createButton(PlayerContainer, "Teleport to Map", true)
local TPSpawnBtn, TPSpawnLabel = createButton(PlayerContainer, "Teleport to Spawn", true)

local function switchTab(selected)
    EspContainer.Visible = (selected == "ESP")
    FarmContainer.Visible = (selected == "FARM")
    PlayerContainer.Visible = (selected == "PLAYER")

    local function styleTab(btn, stroke, active)
        btn.BackgroundColor3 = active and Color3.fromRGB(35, 42, 58) or Color3.fromRGB(25, 29, 39)
        btn.TextColor3 = active and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(150, 155, 175)
        stroke.Color = active and Color3.fromRGB(60, 130, 245) or Color3.fromRGB(40, 45, 60)
    end

    styleTab(EspTabBtn, EspTabStroke, selected == "ESP")
    styleTab(FarmTabBtn, FarmTabStroke, selected == "FARM")
    styleTab(PlayerTabBtn, PlayerTabStroke, selected == "PLAYER")
end

EspTabBtn.MouseButton1Click:Connect(function() switchTab("ESP") end)
FarmTabBtn.MouseButton1Click:Connect(function() switchTab("FARM") end)
PlayerTabBtn.MouseButton1Click:Connect(function() switchTab("PLAYER") end)
switchTab("ESP")

local dragging, dragStart, startPos
Header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
    end
end)

Header.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

local isMinimized = false
MinimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    MinimizeBtn.Text = isMinimized and "+" or "−"
    TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
        Size = isMinimized and UDim2.new(0, 250, 0, 40) or UDim2.new(0, 250, 0, 450)
    }):Play()
end)

-- ============================================================
-- 2. LOGIC & FUNCTIONS
-- ============================================================
local highlightEnabled = false
local lineEnabled = false
local gunEspEnabled = false
local autoFarmEnabled = false
local noclipEnabled = false
local flyEnabled = false
local customSpeed = 16

local playerStorage = {}
local gunHighlights = {}

local function updateToggle(state, label, dot, stroke, baseText)
    label.Text = baseText
    local targetColor = state and Color3.fromRGB(60, 220, 130) or Color3.fromRGB(80, 85, 100)
    local strokeColor = state and Color3.fromRGB(40, 180, 100) or Color3.fromRGB(40, 45, 60)
    dot.BackgroundColor3 = targetColor
    stroke.Color = strokeColor
end

local function applySpeed(val)
    val = math.clamp(tonumber(val) or 16, 16, 100)
    customSpeed = val
    SpeedInput.Text = tostring(val)
    SpeedSliderFill.Size = UDim2.new((val - 16) / 84, 0, 1, 0)
    
    local char = LocalPlayer.Character
    if char then
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = customSpeed
        end
    end
end

SpeedInput.FocusLost:Connect(function()
    applySpeed(SpeedInput.Text)
end)

local draggingSlider = false
SpeedSliderBg.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        draggingSlider = true
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        draggingSlider = false
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if draggingSlider and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local pos = math.clamp((input.Position.X - SpeedSliderBg.AbsolutePosition.X) / SpeedSliderBg.AbsoluteSize.X, 0, 1)
        applySpeed(math.floor(pos * 84 + 16))
    end
end)

LocalPlayer.CharacterAdded:Connect(function(newChar)
    task.wait(0.6)
    local humanoid = newChar:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = customSpeed
    end
end)

RunService.Stepped:Connect(function()
    local char = LocalPlayer.Character
    if char and noclipEnabled then
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end)

local function setupFly(state)
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    if not hrp or not humanoid then return end

    if state then
        humanoid.PlatformStand = true
        if not hrp:FindFirstChild("MM2_FlyVelocity") then
            local bv = Instance.new("BodyVelocity")
            bv.Name = "MM2_FlyVelocity"
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.new(0, 0, 0)
            bv.Parent = hrp

            local bg = Instance.new("BodyGyro")
            bg.Name = "MM2_FlyGyro"
            bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            bg.CFrame = hrp.CFrame
            bg.Parent = hrp
        end
    else
        humanoid.PlatformStand = false
        if hrp:FindFirstChild("MM2_FlyVelocity") then hrp.MM2_FlyVelocity:Destroy() end
        if hrp:FindFirstChild("MM2_FlyGyro") then hrp.MM2_FlyGyro:Destroy() end
    end
end

RunService.RenderStepped:Connect(function()
    if flyEnabled then
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            local camCFrame = Camera.CFrame
            local moveDir = Vector3.new()
            
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + camCFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - camCFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - camCFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + camCFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir = moveDir - Vector3.new(0, 1, 0) end
            
            local flyVel = hrp:FindFirstChild("MM2_FlyVelocity")
            local flyGyro = hrp:FindFirstChild("MM2_FlyGyro")
            
            if flyVel and flyGyro then
                if moveDir.Magnitude > 0 then
                    flyVel.Velocity = moveDir.Unit * 50
                else
                    flyVel.Velocity = Vector3.new(0, 0, 0)
                end
                flyGyro.CFrame = camCFrame
            end
        end
    end
end)

local function getActiveMap()
    local normalMap = Workspace:FindFirstChild("NormalMap") or Workspace:FindFirstChild("Map")
    if normalMap then return normalMap end
    for _, obj in ipairs(Workspace:GetChildren()) do
        if obj.Name ~= "RegularLobby" and obj.Name ~= "Lobby" and (obj:FindFirstChild("Spawns") or obj:FindFirstChild("CoinContainer", true)) then
            return obj
        end
    end
    return nil
end

local function getRoleColor(player)
    if not player then return Color3.fromRGB(0, 220, 130) end
    local character = player.Character
    local backpack = player:FindFirstChildOfClass("Backpack")
    local hasKnife = (character and character:FindFirstChild("Knife")) or (backpack and backpack:FindFirstChild("Knife"))
    if hasKnife then return Color3.fromRGB(255, 60, 60) end
    local hasGun = (character and (character:FindFirstChild("Gun") or character:FindFirstChild("Revolver"))) or 
                   (backpack and (backpack:FindFirstChild("Gun") or backpack:FindFirstChild("Revolver")))
    if hasGun then return Color3.fromRGB(0, 150, 255) end
    return Color3.fromRGB(0, 220, 130)
end

-- ============================================================
-- 3. ESP LOGIC
-- ============================================================
local function clearESP(player)
    if playerStorage[player] then
        if playerStorage[player].Highlight then pcall(function() playerStorage[player].Highlight:Destroy() end) end
        if playerStorage[player].LineFrame then pcall(function() playerStorage[player].LineFrame:Destroy() end) end
        playerStorage[player] = nil
    end
end

local function createESPForPlayer(player, character)
    if player == LocalPlayer then return end
    if not character then return end
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    if playerStorage[player] and playerStorage[player].Character == character then return end
    clearESP(player)
    local color = getRoleColor(player)
    local hl = Instance.new("Highlight")
    hl.Name = "MM2_Ultra_HL"
    hl.Adornee = character
    hl.FillColor = color
    hl.OutlineColor = color
    hl.FillTransparency = 0.4
    hl.OutlineTransparency = 0
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    hl.Enabled = false
    hl.Parent = character
    local line = Instance.new("Frame")
    line.Name = "MM2_Ultra_Line"
    line.BorderSizePixel = 0
    line.BackgroundColor3 = color
    line.AnchorPoint = Vector2.new(0.5, 0.5)
    line.Visible = false
    line.Parent = ScreenGui
    playerStorage[player] = {
        Character = character,
        HRP = hrp,
        Highlight = hl,
        LineFrame = line
    }
end

local function attachESP(player)
    if player == LocalPlayer then return end
    if player.Character then createESPForPlayer(player, player.Character) end
    player.CharacterAdded:Connect(function(character)
        task.wait(0.5)
        createESPForPlayer(player, character)
    end)
    player.CharacterRemoving:Connect(function() clearESP(player) end)
end

for _, p in ipairs(Players:GetPlayers()) do attachESP(p) end
Players.PlayerAdded:Connect(attachESP)
Players.PlayerRemoving:Connect(clearESP)

-- ============================================================
-- 4. GUN ESP & TWEEN AUTO FARM
-- ============================================================
local function clearGunESP()
    for _, highlight in pairs(gunHighlights) do
        if highlight then pcall(function() highlight:Destroy() end) end
    end
    gunHighlights = {}
end

local function updateGunESP()
    if not gunEspEnabled then
        clearGunESP()
        return
    end
    local map = getActiveMap()
    local searchTarget = map or Workspace
    for _, obj in ipairs(searchTarget:GetChildren()) do
        local name = obj.Name
        if name == "GunDrop" or name == "Gun" or name == "Revolver" or name == "Knife" then
            if not gunHighlights[obj] then
                local highlight = Instance.new("Highlight")
                highlight.Name = "GunESP"
                highlight.Adornee = obj
                highlight.FillColor = Color3.fromRGB(255, 200, 0)
                highlight.OutlineColor = Color3.fromRGB(255, 255, 0)
                highlight.FillTransparency = 0.4
                highlight.OutlineTransparency = 0
                highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                highlight.Parent = obj
                gunHighlights[obj] = highlight
            end
        end
    end
end

task.spawn(function()
    while true do
        task.wait(1.5)
        if gunEspEnabled then
            pcall(updateGunESP)
        end
    end
end)

local function getDroppedGun()
    for _, v in ipairs(Workspace:GetChildren()) do
        if v.Name == "GunDrop" then return v end
    end
    return nil
end

local function getNearestCoin()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
    local map = getActiveMap()
    if not map then return nil end
    local container = map:FindFirstChild("CoinContainer")
    if not container then return nil end
    
    local closestCoin = nil
    local shortestDist = math.huge
    for _, coin in ipairs(container:GetChildren()) do
        local part = coin:IsA("BasePart") and coin or coin:FindFirstChild("CoinVisual") or coin:FindFirstChildOfClass("BasePart")
        if part then
            local dist = (hrp.Position - part.Position).Magnitude
            if dist < shortestDist then
                shortestDist = dist
                closestCoin = coin
            end
        end
    end
    return closestCoin
end

task.spawn(function()
    while true do
        task.wait(0.3)
        if autoFarmEnabled then
            pcall(function()
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if not hrp then return end
                
                local map = getActiveMap()
                if not map then return end
                
                local container = map:FindFirstChild("CoinContainer")
                if not container or #container:GetChildren() == 0 then return end
                
                local originalPos = hrp.CFrame
                
                for _, coin in ipairs(container:GetChildren()) do
                    if not autoFarmEnabled then break end
                    
                    local part = coin:IsA("BasePart") and coin or coin:FindFirstChild("CoinVisual") or coin:FindFirstChildOfClass("BasePart")
                    if part then
                        local targetCFrame = part.CFrame
                        local distance = (hrp.Position - targetCFrame.Position).Magnitude
                        
                        local speed = 80 
                        local duration = math.clamp(distance / speed, 0.05, 0.6)
                        
                        local tweenInfo = TweenInfo.new(duration, Enum.EasingStyle.Linear)
                        local tween = TweenService:Create(hrp, tweenInfo, {CFrame = targetCFrame})
                        tween:Play()
                        
                        local elapsed = 0
                        while elapsed < duration and autoFarmEnabled and hrp.Parent do
                            elapsed = elapsed + RunService.RenderStepped:Wait()
                        end
                        
                        local touch = coin:FindFirstChildOfClass("TouchInterest") or (coin:FindFirstChild("Coin_Server") and coin.Coin_Server:FindFirstChildOfClass("TouchInterest"))
                        if firetouchinterest and touch and touch.Parent then
                            firetouchinterest(hrp, touch.Parent, 0)
                            firetouchinterest(hrp, touch.Parent, 1)
                        end
                    end
                end
                
                if hrp and hrp.Parent then
                    hrp.CFrame = originalPos
                end
            end)
        end
    end
end)

-- ============================================================
-- 5. ESP LINES RENDERING
-- ============================================================
RunService.RenderStepped:Connect(function()
    local viewportSize = Camera.ViewportSize
    local screenCenter = Vector2.new(viewportSize.X / 2, viewportSize.Y)

    for player, data in pairs(playerStorage) do
        local character = data.Character
        local hrp = data.HRP

        if character and character.Parent and hrp and hrp.Parent then
            local roleColor = getRoleColor(player)

            if data.Highlight and data.Highlight.Parent then
                data.Highlight.Enabled = highlightEnabled
                data.Highlight.FillColor = roleColor
                data.Highlight.OutlineColor = roleColor
            end

            if lineEnabled and data.LineFrame then
                local screenPos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
                if onScreen then
                    local targetPos = Vector2.new(screenPos.X, screenPos.Y)
                    local distance = (targetPos - screenCenter).Magnitude
                    local angle = math.atan2(targetPos.Y - screenCenter.Y, targetPos.X - screenCenter.X)
                    data.LineFrame.Size = UDim2.new(0, distance, 0, 2)
                    data.LineFrame.Position = UDim2.new(0, (screenCenter.X + targetPos.X) / 2, 0, (screenCenter.Y + targetPos.Y) / 2)
                    data.LineFrame.Rotation = math.deg(angle)
                    data.LineFrame.BackgroundColor3 = roleColor
                    data.LineFrame.Visible = true
                else
                    data.LineFrame.Visible = false
                end
            elseif data.LineFrame then
                data.LineFrame.Visible = false
            end
        else
            if data.LineFrame then data.LineFrame.Visible = false end
        end
    end
end)

-- ============================================================
-- 6. BUTTON CONNECTIONS
-- ============================================================
HighlightBtn.MouseButton1Click:Connect(function()
    highlightEnabled = not highlightEnabled
    updateToggle(highlightEnabled, HighlightLabel, HighlightDot, HighlightStroke, "Player Highlight")
end)

LineBtn.MouseButton1Click:Connect(function()
    lineEnabled = not lineEnabled
    updateToggle(lineEnabled, LineLabel, LineDot, LineStroke, "ESP Line")
end)

GunBtn.MouseButton1Click:Connect(function()
    gunEspEnabled = not gunEspEnabled
    updateToggle(gunEspEnabled, GunLabel, GunDot, GunStroke, "Gun ESP")
    updateGunESP()
end)

TPGunBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local targetGun = getDroppedGun()
    if targetGun and hrp then
        local originalCFrame = hrp.CFrame
        local gunCFrame = targetGun:IsA("BasePart") and targetGun.CFrame or targetGun:GetPivot()
        if gunCFrame then
            hrp.CFrame = gunCFrame + Vector3.new(0, 2, 0)
            task.wait(0.15)
            if hrp and hrp.Parent then
                hrp.CFrame = originalCFrame
            end
        end
    end
end)

TPCoinBtn.MouseButton1Click:Connect(function()
    local coin = getNearestCoin()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if coin and hrp then
        local targetPos = coin:IsA("BasePart") and coin.CFrame or coin:GetPivot()
        hrp.CFrame = targetPos
    end
end)

AutoFarmBtn.MouseButton1Click:Connect(function()
    autoFarmEnabled = not autoFarmEnabled
    updateToggle(autoFarmEnabled, AutoFarmLabel, AutoFarmDot, AutoFarmStroke, "Auto Farm (Tween Flight)")
end)

NoclipBtn.MouseButton1Click:Connect(function()
    noclipEnabled = not noclipEnabled
    updateToggle(noclipEnabled, NoclipLabel, NoclipDot, NoclipStroke, "Noclip")
end)

FlyBtn.MouseButton1Click:Connect(function()
    flyEnabled = not flyEnabled
    updateToggle(flyEnabled, FlyLabel, FlyDot, FlyStroke, "Fly")
    setupFly(flyEnabled)
end)

KillAllBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    local originalPos = hrp.CFrame
    task.spawn(function()
        local knife = char:FindFirstChild("Knife") or LocalPlayer.Backpack:FindFirstChild("Knife")
        if knife then knife.Parent = char end

        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local targetHrp = p.Character:FindFirstChild("HumanoidRootPart")
                local targetHumanoid = p.Character:FindFirstChildOfClass("Humanoid")
                if targetHrp and targetHumanoid and targetHumanoid.Health > 0 then
                    hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 1)
                    if knife then
                        pcall(function() knife:Activate() end)
                    end
                    task.wait(0.01)
                end
            end
        end
        task.wait(0.02)
        if hrp and hrp.Parent then
            hrp.CFrame = originalPos
        end
    end)
end)

TPMapBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local map = getActiveMap()
    if map and hrp then
        local spawnsFolder = map:FindFirstChild("Spawns") or map:FindFirstChild("Spawn", true)
        if spawnsFolder then
            local spawnsList = spawnsFolder:GetChildren()
            if #spawnsList > 0 then
                local randomSpawn = spawnsList[math.random(1, #spawnsList)]
                local targetPos = randomSpawn:IsA("BasePart") and randomSpawn.CFrame or randomSpawn:GetPivot()
                hrp.CFrame = targetPos + Vector3.new(0, 3, 0)
                return
            end
        end
        local coinContainer = map:FindFirstChild("CoinContainer", true)
        if coinContainer and #coinContainer:GetChildren() > 0 then
            local coin = coinContainer:GetChildren()[1]
            local targetPos = coin:IsA("BasePart") and coin.CFrame or coin:GetPivot()
            hrp.CFrame = targetPos + Vector3.new(0, 3, 0)
            return
        end
        hrp.CFrame = map:GetPivot() + Vector3.new(0, 4, 0)
    end
end)

TPSpawnBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        local lobby = Workspace:FindFirstChild("RegularLobby") or Workspace:FindFirstChild("Lobby")
        if lobby then
            local spawnsFolder = lobby:FindFirstChild("Spawns")
            if spawnsFolder then
                local spawnsList = spawnsFolder:GetChildren()
                if #spawnsList > 0 then
                    local randomSpawn = spawnsList[math.random(1, #spawnsList)]
                    local targetPos = randomSpawn:IsA("BasePart") and randomSpawn.CFrame or randomSpawn:GetPivot()
                    hrp.CFrame = targetPos + Vector3.new(0, 3, 0)
                    return
                end
            end
        end
        hrp.CFrame = CFrame.new(0, 0, 0)
    end
end)

print("[MM2 HUB v37.6] Исправлен слайдер скорости и плавный полет!")
