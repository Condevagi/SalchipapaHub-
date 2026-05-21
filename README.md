# SalchipapaHub-
Scripts by DoggyRV
-- ================================================
--   SalchipapaHub - Blox Fruits Auto Farm
--   by DoggyRV
--   Ejecutor: Delta
-- ================================================

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local GAME_ID = game.PlaceId

-- ============ CONFIGURACIÓN ============
local CONFIG = {
    AutoCollect = true,
    AutoStore = true,
    ESP = true,
    ServerHop = true,
    CollectRange = 50,
    CheckInterval = 1,
    MinFruitsToStay = 1
}

local Stats = {
    FruitsCollected = 0,
    ServerHops = 0,
    Status = "Iniciando..."
}

-- ============ GUI ============
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SalchipapaHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 280, 0, 390)
MainFrame.Position = UDim2.new(0, 20, 0.25, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 10)
Corner.Parent = MainFrame

-- Barra de título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 60)
TitleBar.BackgroundColor3 = Color3.fromRGB(220, 160, 0)
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 10)
TitleCorner.Parent = TitleBar

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(0.8, 0, 0, 32)
TitleLabel.Position = UDim2.new(0.05, 0, 0, 4)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextSize = 17
TitleLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
TitleLabel.Text = "🍟 SalchipapaHub"
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TitleBar

local CreatorLabel = Instance.new("TextLabel")
CreatorLabel.Size = UDim2.new(0.8, 0, 0, 20)
CreatorLabel.Position = UDim2.new(0.05, 0, 0, 36)
CreatorLabel.BackgroundTransparency = 1
CreatorLabel.Font = Enum.Font.Gotham
CreatorLabel.TextSize = 11
CreatorLabel.TextColor3 = Color3.fromRGB(40, 40, 40)
CreatorLabel.Text = "by DoggyRV"
CreatorLabel.TextXAlignment = Enum.TextXAlignment.Left
CreatorLabel.Parent = TitleBar

-- ============ BOTÓN MINIMIZAR ============
local isMinimized = false
local originalSize = MainFrame.Size

local MinButton = Instance.new("TextButton")
MinButton.Size = UDim2.new(0, 40, 0, 40)
MinButton.Position = UDim2.new(1, -45, 0, 10)
MinButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MinButton.BackgroundTransparency = 0.5
MinButton.Font = Enum.Font.GothamBold
MinButton.TextSize = 20
MinButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinButton.Text = "—"
MinButton.BorderSizePixel = 0
MinButton.Parent = TitleBar

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 6)
MinCorner.Parent = MinButton

MinButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        MainFrame.Size = UDim2.new(0, 280, 0, 60)
        MinButton.Text = "+"
    else
        MainFrame.Size = originalSize
        MinButton.Text = "—"
    end
end)

-- ============ ARRASTRE TÁCTIL ============
local dragging, dragStart, startPos

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
    end
end)

TitleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.Touch then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ============ BOTONES TOGGLE ============
local function createToggle(name, icon, yPos, configKey)
    local btn = Instance.new("Frame")
    btn.Size = UDim2.new(0.9, 0, 0, 48)
    btn.Position = UDim2.new(0.05, 0, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(25, 25, 40)
    btn.BorderSizePixel = 0
    btn.Parent = MainFrame

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btn

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.75, 0, 1, 0)
    label.Position = UDim2.new(0.05, 0, 0, 0)
    label.BackgroundTransparency = 1
    label.Font = Enum.Font.Gotham
    label.TextSize = 15
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Text = icon .. "  " .. name
    label.Parent = btn

    local statusDot = Instance.new("TextLabel")
    statusDot.Size = UDim2.new(0.2, 0, 1, 0)
    statusDot.Position = UDim2.new(0.78, 0, 0, 0)
    statusDot.BackgroundTransparency = 1
    statusDot.Font = Enum.Font.GothamBold
    statusDot.TextSize = 15
    statusDot.TextColor3 = CONFIG[configKey] and
        Color3.fromRGB(0, 255, 100) or Color3.fromRGB(255, 60, 60)
    statusDot.Text = CONFIG[configKey] and "ON" or "OFF"
    statusDot.Parent = btn

    local clickBtn = Instance.new("TextButton")
    clickBtn.Size = UDim2.new(1, 0, 1, 0)
    clickBtn.BackgroundTransparency = 1
    clickBtn.Text = ""
    clickBtn.Parent = btn

    clickBtn.MouseButton1Click:Connect(function()
        CONFIG[configKey] = not CONFIG[configKey]
        statusDot.Text = CONFIG[configKey] and "ON" or "OFF"
        statusDot.TextColor3 = CONFIG[configKey] and
            Color3.fromRGB(0, 255, 100) or Color3.fromRGB(255, 60, 60)
    end)
end

createToggle("Auto Collect", "🏃", 70,  "AutoCollect")
createToggle("Auto Store",   "📦", 125, "AutoStore")
createToggle("ESP Frutas",   "👁️", 180, "ESP")
createToggle("Server Hop",   "🔄", 235, "ServerHop")

-- Separador
local separator = Instance.new("Frame")
separator.Size = UDim2.new(0.9, 0, 0, 1)
separator.Position = UDim2.new(0.05, 0, 0, 292)
separator.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
separator.BorderSizePixel = 0
separator.Parent = MainFrame

-- Stats
local fruitsLabel = Instance.new("TextLabel")
fruitsLabel.Size = UDim2.new(0.9, 0, 0, 28)
fruitsLabel.Position = UDim2.new(0.05, 0, 0, 300)
fruitsLabel.BackgroundTransparency = 1
fruitsLabel.Font = Enum.Font.Gotham
fruitsLabel.TextSize = 13
fruitsLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
fruitsLabel.TextXAlignment = Enum.TextXAlignment.Left
fruitsLabel.Text = "🍎 Frutas recogidas: 0"
fruitsLabel.Parent = MainFrame

local hopsLabel = Instance.new("TextLabel")
hopsLabel.Size = UDim2.new(0.9, 0, 0, 28)
hopsLabel.Position = UDim2.new(0.05, 0, 0, 328)
hopsLabel.BackgroundTransparency = 1
hopsLabel.Font = Enum.Font.Gotham
hopsLabel.TextSize = 13
hopsLabel.TextColor3 = Color3.fromRGB(100, 200, 255)
hopsLabel.TextXAlignment = Enum.TextXAlignment.Left
hopsLabel.Text = "🔄 Server hops: 0"
hopsLabel.Parent = MainFrame

local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(0.9, 0, 0, 28)
statusLabel.Position = UDim2.new(0.05, 0, 0, 356)
statusLabel.BackgroundTransparency = 1
statusLabel.Font = Enum.Font.Gotham
statusLabel.TextSize = 12
statusLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
statusLabel.TextXAlignment = Enum.TextXAlignment.Left
statusLabel.Text = "⚡ Estado: Iniciando..."
statusLabel.Parent = MainFrame

local function updateStats()
    fruitsLabel.Text = "🍎 Frutas recogidas: " .. Stats.FruitsCollected
    hopsLabel.Text = "🔄 Server hops: " .. Stats.ServerHops
    statusLabel.Text = "⚡ Estado: " .. Stats.Status
end

-- ============ VARIABLES ESP ============
local fruitLabels = {}

-- ============ UTILIDADES ============
local function getCharacterHRP()
    local char = LocalPlayer.Character
    if not char then return nil end
    return char:FindFirstChild("HumanoidRootPart")
end

local function getFruitName(obj)
    local nameTag = obj:FindFirstChild("FruitName") or
                    obj:FindFirstChild("Title")
    if nameTag and nameTag:IsA("StringValue") then
        return nameTag.Value
    end
    local name = obj.Name
    name = name:gsub("_", " ")
    name = name:gsub("(%a)([%u])", "%1 %2")
    return name
end

-- ============ ESP FRUTAS ============
local function createFruitESP(obj)
    if fruitLabels[obj] then return end
    local fruitName = getFruitName(obj)

    local billboard = Instance.new("BillboardGui")
    billboard.AlwaysOnTop = true
    billboard.Size = UDim2.new(0, 150, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 4, 0)
    billboard.Adornee = obj
    billboard.Parent = game.CoreGui

    local nameLabel = Instance.new("TextLabel")
    nameLabel.BackgroundTransparency = 0.3
    nameLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    nameLabel.Size = UDim2.new(1, 0, 0.6, 0)
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.TextSize = 14
    nameLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
    nameLabel.TextStrokeTransparency = 0
    nameLabel.Text = "🍎 " .. fruitName
    nameLabel.Parent = billboard

    local distLabel = Instance.new("TextLabel")
    distLabel.BackgroundTransparency = 1
    distLabel.Size = UDim2.new(1, 0, 0.4, 0)
    distLabel.Position = UDim2.new(0, 0, 0.6, 0)
    distLabel.Font = Enum.Font.Gotham
    distLabel.TextSize = 12
    distLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    distLabel.TextStrokeTransparency = 0
    distLabel.Text = ""
    distLabel.Parent = billboard

    fruitLabels[obj] = { billboard = billboard, distLabel = distLabel }
end

local function removeFruitESP(obj)
    if fruitLabels[obj] then
        fruitLabels[obj].billboard:Destroy()
        fruitLabels[obj] = nil
    end
end

RunService.RenderStepped:Connect(function()
    local hrp = getCharacterHRP()
    if not hrp then return end
    for obj, esp in pairs(fruitLabels) do
        if obj.Parent and esp.distLabel then
            local dist = math.floor((obj.Position - hrp.Position).Magnitude)
            esp.distLabel.Text = "📍 " .. dist .. " studs"
        end
    end
end)

-- ============ AUTO COLLECT ============
local function collectFruit(obj)
    local hrp = getCharacterHRP()
    if not hrp then return end
    hrp.CFrame = CFrame.new(obj.Position + Vector3.new(0, 3, 0))
    task.wait(0.3)
    pcall(function()
        obj.Parent:FindFirstChild("PickUp") and
        obj.Parent.PickUp:FireServer(obj)
    end)
    Stats.FruitsCollected = Stats.FruitsCollected + 1
    Stats.Status = "Recogiendo: " .. getFruitName(obj)
    updateStats()
end

-- ============ AUTO STORE ============
local function autoStore()
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj.Name:lower():find("store") or obj.Name:lower():find("chest") then
            local hrp = getCharacterHRP()
            if hrp then
                local dist = (obj.Position - hrp.Position).Magnitude
                if dist < 30 then
                    hrp.CFrame = CFrame.new(obj.Position + Vector3.new(0, 3, 0))
                    task.wait(0.5)
                    Stats.Status = "Guardando frutas..."
                    updateStats()
                end
            end
        end
    end
end

-- ============ SERVER HOP ============
local function hopServer()
    Stats.ServerHops = Stats.ServerHops + 1
    Stats.Status = "Cambiando servidor..."
    updateStats()
    pcall(function()
        TeleportService:TeleportToPlaceInstance(
            GAME_ID, HttpService:GenerateGUID(false)
        )
    end)
    task.wait(3)
    pcall(function()
        TeleportService:Teleport(GAME_ID)
    end)
end

-- ============ LOOP PRINCIPAL ============
local function mainLoop()
    while true do
        local hrp = getCharacterHRP()
        if hrp then
            local fruitsFound = 0

            for _, obj in ipairs(Workspace:GetDescendants()) do
                if obj:IsA("BasePart") or obj:IsA("MeshPart") then
                    local name = obj.Name:lower()
                    if name:find("fruit") or name:find("fruta") then
                        fruitsFound = fruitsFound + 1
                        if CONFIG.ESP then createFruitESP(obj) end
                        if CONFIG.AutoCollect then
                            local dist = (obj.Position - hrp.Position).Magnitude
                            if dist <= CONFIG.CollectRange then
                                collectFruit(obj)
                            end
                        end
                    end
                end
            end

            if CONFIG.AutoStore then autoStore() end

            for obj, _ in pairs(fruitLabels) do
                if not obj.Parent then removeFruitESP(obj) end
            end

            if CONFIG.ServerHop then
                if fruitsFound < CONFIG.MinFruitsToStay then
                    Stats.Status = "Sin frutas, hopeando..."
                    updateStats()
                    task.wait(2)
                    hopServer()
                else
                    Stats.Status = "Farmeando (" .. fruitsFound .. " frutas)"
                    updateStats()
                end
            end
        end

        task.wait(CONFIG.CheckInterval)
    end
end

-- ============ RESPAWN ============
LocalPlayer.CharacterAdded:Connect(function()
    task.wait(2)
    Stats.Status = "Respawn detectado"
    updateStats()
end)

-- ============ INICIO ============
Stats.Status = "Activo ✅"
updateStats()
print("🍟 SalchipapaHub by DoggyRV iniciado!")

mainLoop()Scripts by DoggyRV
