--[[
    Age of Heroes - Premium (COMPLETO)
    Versão: 2.3.1
    Fast Punch + Teleport + Loop TP + Spawnpoint + Auto Farm Kill com Reset + Aura de Dano + FRZZ + Anti AFK
]]

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")

local LocalPlayer = Players.LocalPlayer

-- Variáveis de configuração
local CONFIG = {
    UI_NAME = "Age of Heroes Premium",
    FAST_PUNCH_ENABLED = false,
    FAST_PUNCH_SPEED = 0.05,
    STAMINA_THRESHOLD = 10,
    TELEPORT_DISTANCE = 5,
    ANIMATION_SPEED = 0.3,
    LOOP_TP_ENABLED = false,
    LOOP_TP_TARGET = nil,
    LOOP_TP_INTERVAL = 0.1,
    SPAWNPOINT = nil,
    AUTO_FARM_ENABLED = false,
    SHIELD_WAIT = 5,
    FRZZ_ENABLED = false,
    frozenCFrame = nil,
    AURA_ENABLED = false,
    AURA_INTERVAL = 5,
    ANTI_AFK_ENABLED = false,
    ANTI_AFK_INTERVAL = 180, -- 3 minutos
}

-- ==================== NOTIFICAÇÕES ====================
local NotificationSystem = {}
NotificationSystem.__index = NotificationSystem

function NotificationSystem.new()
    local self = setmetatable({}, NotificationSystem)
    return self
end

function NotificationSystem:Show(message, duration)
    duration = duration or 2
    pcall(function()
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Age of Heroes",
            Text = message,
            Duration = duration,
            Button1 = "OK"
        })
    end)
end

-- ==================== UI PRINCIPAL ====================
local function createMainUI()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "AgeOfHeroesUI"
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    screenGui.ResetOnSpawn = false
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Frame Principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "MainFrame"
    mainFrame.Size = UDim2.new(0, 500, 0, 420)
    mainFrame.Position = UDim2.new(0.5, -250, 0.5, -210)
    mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    mainFrame.BackgroundTransparency = 0.05
    mainFrame.BorderSizePixel = 0
    mainFrame.ClipsDescendants = true
    mainFrame.Parent = screenGui
    
    local mainCorner = Instance.new("UICorner")
    mainCorner.CornerRadius = UDim.new(0, 16)
    mainCorner.Parent = mainFrame
    
    -- Barra de Título
    local titleBar = Instance.new("Frame")
    titleBar.Name = "TitleBar"
    titleBar.Size = UDim2.new(1, 0, 0, 45)
    titleBar.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    titleBar.BorderSizePixel = 0
    titleBar.Parent = mainFrame
    
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 16)
    titleCorner.Parent = titleBar
    
    local titleBottom = Instance.new("Frame")
    titleBottom.Size = UDim2.new(1, 0, 0.5, 0)
    titleBottom.Position = UDim2.new(0, 0, 0.5, 0)
    titleBottom.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    titleBottom.BorderSizePixel = 0
    titleBottom.Parent = titleBar
    
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(0.6, 0, 1, 0)
    titleLabel.Position = UDim2.new(0, 20, 0, 0)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = CONFIG.UI_NAME
    titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.TextSize = 16
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Parent = titleBar
    
    local minimizeButton = Instance.new("TextButton")
    minimizeButton.Name = "MinimizeButton"
    minimizeButton.Size = UDim2.new(0, 30, 0, 30)
    minimizeButton.Position = UDim2.new(1, -75, 0, 7)
    minimizeButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    minimizeButton.Text = "—"
    minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    minimizeButton.Font = Enum.Font.GothamBold
    minimizeButton.TextSize = 18
    minimizeButton.BorderSizePixel = 0
    minimizeButton.Parent = titleBar
    Instance.new("UICorner", minimizeButton).CornerRadius = UDim.new(0, 8)
    
    local closeButton = Instance.new("TextButton")
    closeButton.Name = "CloseButton"
    closeButton.Size = UDim2.new(0, 30, 0, 30)
    closeButton.Position = UDim2.new(1, -35, 0, 7)
    closeButton.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
    closeButton.Text = "✕"
    closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeButton.Font = Enum.Font.GothamBold
    closeButton.TextSize = 18
    closeButton.BorderSizePixel = 0
    closeButton.Parent = titleBar
    Instance.new("UICorner", closeButton).CornerRadius = UDim.new(0, 8)
    
    -- Container de Conteúdo
    local contentFrame = Instance.new("Frame")
    contentFrame.Name = "ContentFrame"
    contentFrame.Size = UDim2.new(1, 0, 1, -45)
    contentFrame.Position = UDim2.new(0, 0, 0, 45)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = mainFrame
    
    -- Barra de Navegação
    local navBar = Instance.new("Frame")
    navBar.Name = "NavBar"
    navBar.Size = UDim2.new(0, 150, 1, 0)
    navBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    navBar.BorderSizePixel = 0
    navBar.Parent = contentFrame
    
    local navCorner = Instance.new("UICorner")
    navCorner.CornerRadius = UDim.new(0, 16)
    navCorner.Parent = navBar
    
    local navRight = Instance.new("Frame")
    navRight.Size = UDim2.new(0.5, 0, 1, 0)
    navRight.Position = UDim2.new(0.5, 0, 0, 0)
    navRight.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    navRight.BorderSizePixel = 0
    navRight.Parent = navBar
    
    -- Área de Conteúdo Principal
    local mainContent = Instance.new("Frame")
    mainContent.Name = "MainContent"
    mainContent.Size = UDim2.new(1, -160, 1, -10)
    mainContent.Position = UDim2.new(0, 155, 0, 5)
    mainContent.BackgroundTransparency = 1
    mainContent.Parent = contentFrame
    
    -- Botões de Navegação
    local function createNavButton(text, position)
        local button = Instance.new("TextButton")
        button.Size = UDim2.new(1, -20, 0, 30)
        button.Position = UDim2.new(0, 10, 0, position)
        button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        button.Text = text
        button.TextColor3 = Color3.fromRGB(200, 200, 200)
        button.Font = Enum.Font.GothamMedium
        button.TextSize = 11
        button.BorderSizePixel = 0
        button.Parent = navBar
        Instance.new("UICorner", button).CornerRadius = UDim.new(0, 8)
        return button
    end
    
    -- Páginas
    local pages = {}
    
    -- ========== PÁGINA FAST PUNCH ==========
    local fastPunchPage = Instance.new("Frame")
    fastPunchPage.Size = UDim2.new(1, 0, 1, 0)
    fastPunchPage.BackgroundTransparency = 1
    fastPunchPage.Visible = true
    fastPunchPage.Parent = mainContent
    pages["FastPunch"] = fastPunchPage
    
    local fpTitle = Instance.new("TextLabel")
    fpTitle.Size = UDim2.new(1, 0, 0, 30)
    fpTitle.Position = UDim2.new(0, 0, 0, 10)
    fpTitle.BackgroundTransparency = 1
    fpTitle.Text = "⚡ Fast Punch System"
    fpTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    fpTitle.Font = Enum.Font.GothamBold
    fpTitle.TextSize = 18
    fpTitle.TextXAlignment = Enum.TextXAlignment.Center
    fpTitle.Parent = fastPunchPage
    
    local fpDesc = Instance.new("TextLabel")
    fpDesc.Size = UDim2.new(1, -20, 0, 40)
    fpDesc.Position = UDim2.new(0, 10, 0, 50)
    fpDesc.BackgroundTransparency = 1
    fpDesc.Text = "Ataque rápido como uma metralhadora!"
    fpDesc.TextColor3 = Color3.fromRGB(150, 150, 150)
    fpDesc.Font = Enum.Font.GothamMedium
    fpDesc.TextSize = 12
    fpDesc.TextWrapped = true
    fpDesc.TextXAlignment = Enum.TextXAlignment.Center
    fpDesc.Parent = fastPunchPage
    
    local fpToggle = Instance.new("TextButton")
    fpToggle.Size = UDim2.new(0.4, 0, 0, 50)
    fpToggle.Position = UDim2.new(0.3, 0, 0, 110)
    fpToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
    fpToggle.Text = "OFF"
    fpToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    fpToggle.Font = Enum.Font.GothamBold
    fpToggle.TextSize = 20
    fpToggle.BorderSizePixel = 0
    fpToggle.Parent = fastPunchPage
    Instance.new("UICorner", fpToggle).CornerRadius = UDim.new(0, 12)
    
    local staminaLabel = Instance.new("TextLabel")
    staminaLabel.Size = UDim2.new(1, -20, 0, 30)
    staminaLabel.Position = UDim2.new(0, 10, 0, 180)
    staminaLabel.BackgroundTransparency = 1
    staminaLabel.Text = "Stamina: --"
    staminaLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    staminaLabel.Font = Enum.Font.GothamMedium
    staminaLabel.TextSize = 14
    staminaLabel.TextXAlignment = Enum.TextXAlignment.Center
    staminaLabel.Parent = fastPunchPage
    
    -- ========== PÁGINA AUTO FARM KILL + AURA ==========
    local autoFarmPage = Instance.new("Frame")
    autoFarmPage.Size = UDim2.new(1, 0, 1, 0)
    autoFarmPage.BackgroundTransparency = 1
    autoFarmPage.Visible = false
    autoFarmPage.Parent = mainContent
    pages["AutoFarm"] = autoFarmPage
    
    local afTitle = Instance.new("TextLabel")
    afTitle.Size = UDim2.new(1, 0, 0, 22)
    afTitle.Position = UDim2.new(0, 0, 0, 3)
    afTitle.BackgroundTransparency = 1
    afTitle.Text = "🤖 Auto Farm Kill + Reset"
    afTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    afTitle.Font = Enum.Font.GothamBold
    afTitle.TextSize = 13
    afTitle.TextXAlignment = Enum.TextXAlignment.Center
    afTitle.Parent = autoFarmPage
    
    -- Botões: INICIAR + FRZZ + AURA
    local afToggle = Instance.new("TextButton")
    afToggle.Size = UDim2.new(0.3, 0, 0, 24)
    afToggle.Position = UDim2.new(0.02, 0, 0, 30)
    afToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
    afToggle.Text = "INICIAR"
    afToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    afToggle.Font = Enum.Font.GothamBold
    afToggle.TextSize = 9
    afToggle.BorderSizePixel = 0
    afToggle.Parent = autoFarmPage
    Instance.new("UICorner", afToggle).CornerRadius = UDim.new(0, 6)
    
    local frzzToggle = Instance.new("TextButton")
    frzzToggle.Size = UDim2.new(0.3, 0, 0, 24)
    frzzToggle.Position = UDim2.new(0.35, 0, 0, 30)
    frzzToggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    frzzToggle.Text = "FRZZ: OFF"
    frzzToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    frzzToggle.Font = Enum.Font.GothamBold
    frzzToggle.TextSize = 9
    frzzToggle.BorderSizePixel = 0
    frzzToggle.Parent = autoFarmPage
    Instance.new("UICorner", frzzToggle).CornerRadius = UDim.new(0, 6)
    
    local auraToggle = Instance.new("TextButton")
    auraToggle.Size = UDim2.new(0.3, 0, 0, 24)
    auraToggle.Position = UDim2.new(0.68, 0, 0, 30)
    auraToggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    auraToggle.Text = "AURA: OFF"
    auraToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    auraToggle.Font = Enum.Font.GothamBold
    auraToggle.TextSize = 9
    auraToggle.BorderSizePixel = 0
    auraToggle.Parent = autoFarmPage
    Instance.new("UICorner", auraToggle).CornerRadius = UDim.new(0, 6)
    
    -- Status
    local afStatus = Instance.new("TextLabel")
    afStatus.Size = UDim2.new(1, -20, 0, 16)
    afStatus.Position = UDim2.new(0, 10, 0, 60)
    afStatus.BackgroundTransparency = 1
    afStatus.Text = "Status: Parado"
    afStatus.TextColor3 = Color3.fromRGB(200, 200, 200)
    afStatus.Font = Enum.Font.GothamMedium
    afStatus.TextSize = 10
    afStatus.TextXAlignment = Enum.TextXAlignment.Center
    afStatus.Parent = autoFarmPage
    
    local afTargetLabel = Instance.new("TextLabel")
    afTargetLabel.Size = UDim2.new(1, -20, 0, 16)
    afTargetLabel.Position = UDim2.new(0, 10, 0, 78)
    afTargetLabel.BackgroundTransparency = 1
    afTargetLabel.Text = "Alvo: Nenhum"
    afTargetLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    afTargetLabel.Font = Enum.Font.GothamMedium
    afTargetLabel.TextSize = 10
    afTargetLabel.TextXAlignment = Enum.TextXAlignment.Center
    afTargetLabel.Parent = autoFarmPage
    
    -- Botão Anti AFK
    local antiAfkToggle = Instance.new("TextButton")
    antiAfkToggle.Size = UDim2.new(1, -20, 0, 22)
    antiAfkToggle.Position = UDim2.new(0, 10, 0, 96)
    antiAfkToggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    antiAfkToggle.Text = "🛡️ ANTI AFK: OFF (Pula a cada 3min)"
    antiAfkToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    antiAfkToggle.Font = Enum.Font.GothamBold
    antiAfkToggle.TextSize = 9
    antiAfkToggle.BorderSizePixel = 0
    antiAfkToggle.Parent = autoFarmPage
    Instance.new("UICorner", antiAfkToggle).CornerRadius = UDim.new(0, 6)
    
    local selectLabel = Instance.new("TextLabel")
    selectLabel.Size = UDim2.new(1, -20, 0, 16)
    selectLabel.Position = UDim2.new(0, 10, 0, 122)
    selectLabel.BackgroundTransparency = 1
    selectLabel.Text = "🎯 Selecione o alvo:"
    selectLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
    selectLabel.Font = Enum.Font.GothamMedium
    selectLabel.TextSize = 10
    selectLabel.TextXAlignment = Enum.TextXAlignment.Center
    selectLabel.Parent = autoFarmPage
    
    -- Lista de alvos
    local targetList = Instance.new("ScrollingFrame")
    targetList.Size = UDim2.new(1, -20, 0.45, 0)
    targetList.Position = UDim2.new(0, 10, 0, 140)
    targetList.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    targetList.BorderSizePixel = 0
    targetList.ScrollBarThickness = 3
    targetList.CanvasSize = UDim2.new(0, 0, 0, 0)
    targetList.Parent = autoFarmPage
    Instance.new("UICorner", targetList).CornerRadius = UDim.new(0, 8)
    
    local targetLayout = Instance.new("UIListLayout")
    targetLayout.Padding = UDim.new(0, 2)
    targetLayout.Parent = targetList
    
    -- ========== PÁGINA TELEPORT ==========
    local teleportPage = Instance.new("Frame")
    teleportPage.Size = UDim2.new(1, 0, 1, 0)
    teleportPage.BackgroundTransparency = 1
    teleportPage.Visible = false
    teleportPage.Parent = mainContent
    pages["Teleport"] = teleportPage
    
    local tpTitle = Instance.new("TextLabel")
    tpTitle.Size = UDim2.new(1, 0, 0, 25)
    tpTitle.Position = UDim2.new(0, 0, 0, 5)
    tpTitle.BackgroundTransparency = 1
    tpTitle.Text = "🌍 Player Teleport"
    tpTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    tpTitle.Font = Enum.Font.GothamBold
    tpTitle.TextSize = 16
    tpTitle.TextXAlignment = Enum.TextXAlignment.Center
    tpTitle.Parent = teleportPage
    
    local playerList = Instance.new("ScrollingFrame")
    playerList.Size = UDim2.new(1, -20, 0.5, 0)
    playerList.Position = UDim2.new(0, 10, 0, 35)
    playerList.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    playerList.BorderSizePixel = 0
    playerList.ScrollBarThickness = 4
    playerList.CanvasSize = UDim2.new(0, 0, 0, 0)
    playerList.Parent = teleportPage
    Instance.new("UICorner", playerList).CornerRadius = UDim.new(0, 12)
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 3)
    listLayout.Parent = playerList
    
    -- Loop TP
    local loopFrame = Instance.new("Frame")
    loopFrame.Size = UDim2.new(1, -20, 0, 110)
    loopFrame.Position = UDim2.new(0, 10, 0, 255)
    loopFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    loopFrame.BorderSizePixel = 0
    loopFrame.Parent = teleportPage
    Instance.new("UICorner", loopFrame).CornerRadius = UDim.new(0, 12)
    
    local loopTitle = Instance.new("TextLabel")
    loopTitle.Size = UDim2.new(1, 0, 0, 22)
    loopTitle.Position = UDim2.new(0, 0, 0, 5)
    loopTitle.BackgroundTransparency = 1
    loopTitle.Text = "🔄 Loop TP"
    loopTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    loopTitle.Font = Enum.Font.GothamBold
    loopTitle.TextSize = 13
    loopTitle.TextXAlignment = Enum.TextXAlignment.Center
    loopTitle.Parent = loopFrame
    
    local loopTargetLabel = Instance.new("TextLabel")
    loopTargetLabel.Size = UDim2.new(1, -10, 0, 18)
    loopTargetLabel.Position = UDim2.new(0, 5, 0, 30)
    loopTargetLabel.BackgroundTransparency = 1
    loopTargetLabel.Text = "Alvo: Nenhum"
    loopTargetLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
    loopTargetLabel.Font = Enum.Font.GothamMedium
    loopTargetLabel.TextSize = 11
    loopTargetLabel.TextXAlignment = Enum.TextXAlignment.Center
    loopTargetLabel.Parent = loopFrame
    
    local loopToggle = Instance.new("TextButton")
    loopToggle.Size = UDim2.new(0.6, 0, 0, 28)
    loopToggle.Position = UDim2.new(0.2, 0, 0, 58)
    loopToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
    loopToggle.Text = "INICIAR LOOP"
    loopToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    loopToggle.Font = Enum.Font.GothamBold
    loopToggle.TextSize = 11
    loopToggle.BorderSizePixel = 0
    loopToggle.Parent = loopFrame
    Instance.new("UICorner", loopToggle).CornerRadius = UDim.new(0, 8)
    
    -- ========== PÁGINA SPAWNPOINT ==========
    local spawnPage = Instance.new("Frame")
    spawnPage.Size = UDim2.new(1, 0, 1, 0)
    spawnPage.BackgroundTransparency = 1
    spawnPage.Visible = false
    spawnPage.Parent = mainContent
    pages["Spawn"] = spawnPage
    
    local spTitle = Instance.new("TextLabel")
    spTitle.Size = UDim2.new(1, 0, 0, 25)
    spTitle.Position = UDim2.new(0, 0, 0, 10)
    spTitle.BackgroundTransparency = 1
    spTitle.Text = "📍 Set Spawnpoint"
    spTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    spTitle.Font = Enum.Font.GothamBold
    spTitle.TextSize = 16
    spTitle.TextXAlignment = Enum.TextXAlignment.Center
    spTitle.Parent = spawnPage
    
    local spStatus = Instance.new("TextLabel")
    spStatus.Size = UDim2.new(1, -20, 0, 25)
    spStatus.Position = UDim2.new(0, 10, 0, 100)
    spStatus.BackgroundTransparency = 1
    spStatus.Text = "Spawnpoint: Não definido"
    spStatus.TextColor3 = Color3.fromRGB(200, 200, 200)
    spStatus.Font = Enum.Font.GothamMedium
    spStatus.TextSize = 13
    spStatus.TextXAlignment = Enum.TextXAlignment.Center
    spStatus.Parent = spawnPage
    
    local setSpawnBtn = Instance.new("TextButton")
    setSpawnBtn.Size = UDim2.new(0.5, 0, 0, 45)
    setSpawnBtn.Position = UDim2.new(0.25, 0, 0, 140)
    setSpawnBtn.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
    setSpawnBtn.Text = "🎯 DEFINIR SPAWN"
    setSpawnBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    setSpawnBtn.Font = Enum.Font.GothamBold
    setSpawnBtn.TextSize = 14
    setSpawnBtn.BorderSizePixel = 0
    setSpawnBtn.Parent = spawnPage
    Instance.new("UICorner", setSpawnBtn).CornerRadius = UDim.new(0, 10)
    
    local clearSpawnBtn = Instance.new("TextButton")
    clearSpawnBtn.Size = UDim2.new(0.5, 0, 0, 35)
    clearSpawnBtn.Position = UDim2.new(0.25, 0, 0, 200)
    clearSpawnBtn.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
    clearSpawnBtn.Text = "🗑️ LIMPAR SPAWN"
    clearSpawnBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    clearSpawnBtn.Font = Enum.Font.GothamBold
    clearSpawnBtn.TextSize = 12
    clearSpawnBtn.BorderSizePixel = 0
    clearSpawnBtn.Parent = spawnPage
    Instance.new("UICorner", clearSpawnBtn).CornerRadius = UDim.new(0, 8)
    
    -- Botões de Navegação
    local fastPunchBtn = createNavButton("⚡ Fast Punch", 10)
    local autoFarmBtn = createNavButton("🤖 Auto Farm", 45)
    local teleportBtn = createNavButton("🌍 Teleport", 80)
    local spawnBtn = createNavButton("📍 Spawnpoint", 115)
    
    -- Sistema de arrastar
    local dragging = false
    local dragStart = nil
    local startPos = nil
    
    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
    
    -- Navegação
    local function switchPage(pageName)
        for name, page in pairs(pages) do page.Visible = (name == pageName) end
        fastPunchBtn.BackgroundColor3 = Color3.fromRGB(pageName == "FastPunch" and 60 or 40, 40, 40)
        autoFarmBtn.BackgroundColor3 = Color3.fromRGB(pageName == "AutoFarm" and 60 or 40, 40, 40)
        teleportBtn.BackgroundColor3 = Color3.fromRGB(pageName == "Teleport" and 60 or 40, 40, 40)
        spawnBtn.BackgroundColor3 = Color3.fromRGB(pageName == "Spawn" and 60 or 40, 40, 40)
    end
    
    fastPunchBtn.MouseButton1Click:Connect(function() switchPage("FastPunch") end)
    autoFarmBtn.MouseButton1Click:Connect(function() switchPage("AutoFarm") end)
    teleportBtn.MouseButton1Click:Connect(function() switchPage("Teleport") end)
    spawnBtn.MouseButton1Click:Connect(function() switchPage("Spawn") end)
    
    -- Minimizar
    local minimized = false
    local originalSize = mainFrame.Size
    minimizeButton.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            TweenService:Create(mainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0, 500, 0, 45)}):Play()
            minimizeButton.Text = "□"
        else
            TweenService:Create(mainFrame, TweenInfo.new(0.3), {Size = originalSize}):Play()
            minimizeButton.Text = "—"
        end
    end)
    
    -- Fechar
    closeButton.MouseButton1Click:Connect(function()
        CONFIG.FAST_PUNCH_ENABLED = false
        CONFIG.LOOP_TP_ENABLED = false
        CONFIG.AUTO_FARM_ENABLED = false
        CONFIG.FRZZ_ENABLED = false
        CONFIG.AURA_ENABLED = false
        CONFIG.ANTI_AFK_ENABLED = false
        screenGui:Destroy()
    end)
    
    return {
        screenGui = screenGui, mainFrame = mainFrame, fpToggle = fpToggle, staminaLabel = staminaLabel,
        playerList = playerList, notificationSystem = NotificationSystem.new(), pages = pages,
        teleportPage = teleportPage, fastPunchPage = fastPunchPage, autoFarmPage = autoFarmPage,
        spawnPage = spawnPage, loopToggle = loopToggle, loopTargetLabel = loopTargetLabel,
        setSpawnBtn = setSpawnBtn, clearSpawnBtn = clearSpawnBtn, spStatus = spStatus,
        afToggle = afToggle, afStatus = afStatus, afTargetLabel = afTargetLabel,
        targetList = targetList, frzzToggle = frzzToggle, auraToggle = auraToggle,
        antiAfkToggle = antiAfkToggle,
    }
end

-- ==================== FAST PUNCH ====================
local function setupFastPunch(ui)
    local isRunning = false
    local PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Events") and ReplicatedStorage.Events:FindFirstChild("Punch")
    if not PUNCH_EVENT then PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Punch") end
    
    local PUNCH_ARGS = {0, 0.1, 1}
    
    local function doPunch()
        if PUNCH_EVENT and PUNCH_EVENT:IsA("RemoteEvent") then
            pcall(function() PUNCH_EVENT:FireServer(unpack(PUNCH_ARGS)) end)
        end
    end
    
    local function fastPunchLoop()
        isRunning = true
        while CONFIG.FAST_PUNCH_ENABLED and isRunning do
            doPunch()
            task.wait(CONFIG.FAST_PUNCH_SPEED)
        end
    end
    
    ui.fpToggle.MouseButton1Click:Connect(function()
        CONFIG.FAST_PUNCH_ENABLED = not CONFIG.FAST_PUNCH_ENABLED
        if CONFIG.FAST_PUNCH_ENABLED then
            ui.fpToggle.Text = "ON"
            ui.fpToggle.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
            if not isRunning then task.spawn(fastPunchLoop) end
        else
            ui.fpToggle.Text = "OFF"
            ui.fpToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
            isRunning = false
        end
    end)
end

-- ==================== TELEPORTE ====================
local function setupTeleportSystem(ui)
    local selectedPlayer = nil
    
    local function teleportToPlayer(player, silent)
        if not player then return end
        local targetChar = player.Character
        if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then return end
        local myChar = LocalPlayer.Character
        if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then return end
        myChar.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, 2.5)
        if not silent then ui.notificationSystem:Show("✅ Teleportado para " .. player.Name) end
    end
    
    local function updatePlayerList()
        pcall(function()
            for _, child in pairs(ui.playerList:GetChildren()) do
                if child:IsA("TextButton") then child:Destroy() end
            end
            local ySize = 0
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local btn = Instance.new("TextButton")
                    btn.Size = UDim2.new(1, -10, 0, 35)
                    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
                    btn.Text = player.Name
                    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
                    btn.Font = Enum.Font.GothamMedium
                    btn.TextSize = 14
                    btn.BorderSizePixel = 0
                    btn.AutoButtonColor = false
                    btn.Parent = ui.playerList
                    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
                    btn.MouseButton1Click:Connect(function()
                        selectedPlayer = player
                        ui.loopTargetLabel.Text = "Alvo: " .. player.Name
                        for _, c in pairs(ui.playerList:GetChildren()) do
                            if c:IsA("TextButton") then c.BackgroundColor3 = Color3.fromRGB(50, 50, 50) end
                        end
                        btn.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
                        teleportToPlayer(player, false)
                    end)
                    ySize = ySize + 38
                end
            end
            ui.playerList.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
        end)
    end
    
    ui.loopToggle.MouseButton1Click:Connect(function()
        CONFIG.LOOP_TP_ENABLED = not CONFIG.LOOP_TP_ENABLED
        if CONFIG.LOOP_TP_ENABLED then
            if not selectedPlayer then ui.notificationSystem:Show("❌ Selecione um jogador primeiro!") CONFIG.LOOP_TP_ENABLED = false return end
            ui.loopToggle.Text = "PARAR LOOP"
            ui.loopToggle.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
            CONFIG.LOOP_TP_TARGET = selectedPlayer
            task.spawn(function()
                while CONFIG.LOOP_TP_ENABLED and CONFIG.LOOP_TP_TARGET do
                    teleportToPlayer(CONFIG.LOOP_TP_TARGET, true)
                    task.wait(CONFIG.LOOP_TP_INTERVAL)
                end
            end)
        else
            ui.loopToggle.Text = "INICIAR LOOP"
            ui.loopToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
            CONFIG.LOOP_TP_TARGET = nil
        end
    end)
    
    ui.teleportPage:GetPropertyChangedSignal("Visible"):Connect(function() if ui.teleportPage.Visible then updatePlayerList() end end)
    Players.PlayerAdded:Connect(function() task.wait(0.2) updatePlayerList() end)
    Players.PlayerRemoving:Connect(function(p)
        task.wait(0.2) updatePlayerList()
        if selectedPlayer == p then selectedPlayer = nil ui.loopTargetLabel.Text = "Alvo: Nenhum"
            if CONFIG.LOOP_TP_ENABLED then CONFIG.LOOP_TP_ENABLED = false ui.loopToggle.Text = "INICIAR LOOP" ui.loopToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113) end
        end
    end)
    updatePlayerList()
end

-- ==================== AUTO FARM + RESET + AURA + FRZZ + ANTI AFK ====================
local function setupAutoFarm(ui)
    local PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Events") and ReplicatedStorage.Events:FindFirstChild("Punch")
    if not PUNCH_EVENT then PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Punch") end
    local selectedTarget = nil
    
    local function doPunch()
        if PUNCH_EVENT and PUNCH_EVENT:IsA("RemoteEvent") then pcall(function() PUNCH_EVENT:FireServer(0, 0.1, 1) end) end
    end
    
    local function isPlayerAlive(player)
        if not player then return false end
        local char = player.Character
        if not char then return false end
        local humanoid = char:FindFirstChild("Humanoid")
        if not humanoid then return false end
        return humanoid.Health > 0
    end
    
    local function teleportToTarget()
        if not selectedTarget then return end
        local targetChar = selectedTarget.Character
        if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then return end
        local myChar = LocalPlayer.Character
        if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then return end
        myChar.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, 2.5)
    end
    
    local function resetCharacter()
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("Humanoid") then
            char.Humanoid.Health = 0
        end
    end
    
    -- INICIAR Auto Farm
    ui.afToggle.MouseButton1Click:Connect(function()
        CONFIG.AUTO_FARM_ENABLED = not CONFIG.AUTO_FARM_ENABLED
        if CONFIG.AUTO_FARM_ENABLED then
            if not selectedTarget then ui.notificationSystem:Show("❌ Selecione um jogador!") CONFIG.AUTO_FARM_ENABLED = false return end
            ui.afToggle.Text = "PARAR"
            ui.afToggle.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
            ui.afStatus.Text = "Status: Iniciando..."
            ui.afStatus.TextColor3 = Color3.fromRGB(46, 204, 113)
            
            task.spawn(function()
                while CONFIG.AUTO_FARM_ENABLED do
                    if isPlayerAlive(selectedTarget) then
                        teleportToTarget()
                        ui.afStatus.Text = "Status: Matando " .. selectedTarget.Name
                        while CONFIG.AUTO_FARM_ENABLED and isPlayerAlive(selectedTarget) do
                            doPunch()
                            task.wait(0.05)
                        end
                        if not CONFIG.AUTO_FARM_ENABLED then break end
                        
                        -- RESET após matar
                        ui.afStatus.Text = "Status: Resetando..."
                        ui.notificationSystem:Show("💀 " .. selectedTarget.Name .. " morreu! Resetando...")
                        resetCharacter()
                        task.wait(0.5)
                        
                        -- Espera renascer
                        ui.afStatus.Text = "Status: Aguardando renascer..."
                        while CONFIG.AUTO_FARM_ENABLED and not isPlayerAlive(selectedTarget) do task.wait(0.5) end
                        if not CONFIG.AUTO_FARM_ENABLED then break end
                        
                        -- Espera escudo
                        ui.afStatus.Text = "Status: Aguardando escudo..."
                        for i = CONFIG.SHIELD_WAIT, 1, -1 do
                            if not CONFIG.AUTO_FARM_ENABLED then break end
                            ui.afStatus.Text = "Status: Escudo " .. i .. "s..."
                            task.wait(1)
                        end
                    else
                        ui.afStatus.Text = "Status: Aguardando renascer..."
                        while CONFIG.AUTO_FARM_ENABLED and not isPlayerAlive(selectedTarget) do task.wait(1) end
                        if CONFIG.AUTO_FARM_ENABLED then
                            for i = CONFIG.SHIELD_WAIT, 1, -1 do
                                if not CONFIG.AUTO_FARM_ENABLED then break end
                                ui.afStatus.Text = "Status: Escudo " .. i .. "s..."
                                task.wait(1)
                            end
                        end
                    end
                end
            end)
        else
            ui.afToggle.Text = "INICIAR"
            ui.afToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
            ui.afStatus.Text = "Status: Parado"
            ui.afStatus.TextColor3 = Color3.fromRGB(200, 200, 200)
        end
    end)
    
    -- FRZZ
    local freezeConnection = nil
    ui.frzzToggle.MouseButton1Click:Connect(function()
        CONFIG.FRZZ_ENABLED = not CONFIG.FRZZ_ENABLED
        if CONFIG.FRZZ_ENABLED then
            ui.frzzToggle.Text = "FRZZ: ON"
            ui.frzzToggle.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("HumanoidRootPart") then CONFIG.frozenCFrame = char.HumanoidRootPart.CFrame end
            freezeConnection = RunService.Heartbeat:Connect(function()
                if not CONFIG.FRZZ_ENABLED then return end
                local char = LocalPlayer.Character
                if char and char:FindFirstChild("HumanoidRootPart") and CONFIG.frozenCFrame then
                    char.HumanoidRootPart.CFrame = CONFIG.frozenCFrame
                    char.HumanoidRootPart.Velocity = Vector3.new(0, 0, 0)
                    char.HumanoidRootPart.RotVelocity = Vector3.new(0, 0, 0)
                end
            end)
        else
            ui.frzzToggle.Text = "FRZZ: OFF"
            ui.frzzToggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
            CONFIG.frozenCFrame = nil
            if freezeConnection then freezeConnection:Disconnect() freezeConnection = nil end
        end
    end)
    
    -- AURA DE DANO
    ui.auraToggle.MouseButton1Click:Connect(function()
        CONFIG.AURA_ENABLED = not CONFIG.AURA_ENABLED
        if CONFIG.AURA_ENABLED then
            ui.auraToggle.Text = "AURA: ON"
            ui.auraToggle.BackgroundColor3 = Color3.fromRGB(241, 196, 15)
            ui.notificationSystem:Show("💥 Aura de Dano ATIVADA! (a cada " .. CONFIG.AURA_INTERVAL .. "s)")
            
            task.spawn(function()
                while CONFIG.AURA_ENABLED do
                    local myChar = LocalPlayer.Character
                    if myChar and myChar:FindFirstChild("HumanoidRootPart") then
                        for _, player in ipairs(Players:GetPlayers()) do
                            if player ~= LocalPlayer and isPlayerAlive(player) then
                                local targetChar = player.Character
                                if targetChar and targetChar:FindFirstChild("HumanoidRootPart") then
                                    local dist = (myChar.HumanoidRootPart.Position - targetChar.HumanoidRootPart.Position).Magnitude
                                    if dist <= 50 then
                                        targetChar.HumanoidRootPart.CFrame = myChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, -3)
                                        doPunch()
                                    end
                                end
                            end
                        end
                    end
                    task.wait(CONFIG.AURA_INTERVAL)
                end
            end)
        else
            ui.auraToggle.Text = "AURA: OFF"
            ui.auraToggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
        end
    end)
    
    -- ANTI AFK
    ui.antiAfkToggle.MouseButton1Click:Connect(function()
        CONFIG.ANTI_AFK_ENABLED = not CONFIG.ANTI_AFK_ENABLED
        if CONFIG.ANTI_AFK_ENABLED then
            ui.antiAfkToggle.Text = "🛡️ ANTI AFK: ON (Pula a cada 3min)"
            ui.antiAfkToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
            ui.notificationSystem:Show("🛡️ Anti AFK ATIVADO! (Pulo a cada 3min)")
            
            task.spawn(function()
                while CONFIG.ANTI_AFK_ENABLED do
                    task.wait(CONFIG.ANTI_AFK_INTERVAL)
                    if CONFIG.ANTI_AFK_ENABLED then
                        -- Simula pulo
                        pcall(function()
                            local char = LocalPlayer.Character
                            if char and char:FindFirstChild("Humanoid") then
                                char.Humanoid.Jump = true
                            end
                        end)
                        -- Move um pouco pra frente e volta
                        pcall(function()
                            local char = LocalPlayer.Character
                            if char and char:FindFirstChild("Humanoid") then
                                char.Humanoid:Move(Vector3.new(1, 0, 0), false)
                                task.wait(0.1)
                                char.Humanoid:Move(Vector3.new(-1, 0, 0), false)
                            end
                        end)
                    end
                end
            end)
        else
            ui.antiAfkToggle.Text = "🛡️ ANTI AFK: OFF (Pula a cada 3min)"
            ui.antiAfkToggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
            ui.notificationSystem:Show("🛡️ Anti AFK DESATIVADO!")
        end
    end)
    
    -- Lista de alvos
    local function updateTargetList()
        pcall(function()
            for _, child in pairs(ui.targetList:GetChildren()) do if child:IsA("TextButton") then child:Destroy() end end
            local ySize = 0
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local btn = Instance.new("TextButton")
                    btn.Size = UDim2.new(1, -10, 0, 25)
                    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
                    btn.Text = "🎯 " .. player.Name
                    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
                    btn.Font = Enum.Font.GothamMedium
                    btn.TextSize = 11
                    btn.BorderSizePixel = 0
                    btn.AutoButtonColor = false
                    btn.Parent = ui.targetList
                    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
                    btn.MouseButton1Click:Connect(function()
                        selectedTarget = player
                        ui.afTargetLabel.Text = "Alvo: " .. player.Name
                        for _, c in pairs(ui.targetList:GetChildren()) do if c:IsA("TextButton") then c.BackgroundColor3 = Color3.fromRGB(50, 50, 50) end end
                        btn.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
                    end)
                    ySize = ySize + 27
                end
            end
            ui.targetList.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
        end)
    end
    
    ui.autoFarmPage:GetPropertyChangedSignal("Visible"):Connect(function() if ui.autoFarmPage.Visible then updateTargetList() end end)
    Players.PlayerAdded:Connect(function() task.wait(0.2) updateTargetList() end)
    Players.PlayerRemoving:Connect(function(p)
        task.wait(0.2) updateTargetList()
        if selectedTarget == p then selectedTarget = nil ui.afTargetLabel.Text = "Alvo: Nenhum"
            if CONFIG.AUTO_FARM_ENABLED then CONFIG.AUTO_FARM_ENABLED = false ui.afToggle.Text = "INICIAR" ui.afToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113) end
        end
    end)
    updateTargetList()
end

-- ==================== SPAWNPOINT ====================
local function setupSpawnpoint(ui)
    local spawnConnection
    local function updateConnection()
        if spawnConnection then spawnConnection:Disconnect() spawnConnection = nil end
        spawnConnection = LocalPlayer.CharacterAdded:Connect(function(char)
            if not CONFIG.SPAWNPOINT then return end
            task.wait(0.2)
            local hrp = char:FindFirstChild("HumanoidRootPart") or char:WaitForChild("HumanoidRootPart", 5)
            if hrp and CONFIG.SPAWNPOINT then hrp.CFrame = CONFIG.SPAWNPOINT end
        end)
    end
    updateConnection()
    ui.setSpawnBtn.MouseButton1Click:Connect(function()
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then ui.notificationSystem:Show("❌ Você precisa estar no jogo!") return end
        CONFIG.SPAWNPOINT = char.HumanoidRootPart.CFrame
        ui.spStatus.Text = "Spawnpoint: ✅ Definido!"
        ui.spStatus.TextColor3 = Color3.fromRGB(46, 204, 113)
        updateConnection()
    end)
    ui.clearSpawnBtn.MouseButton1Click:Connect(function()
        CONFIG.SPAWNPOINT = nil
        ui.spStatus.Text = "Spawnpoint: Não definido"
        ui.spStatus.TextColor3 = Color3.fromRGB(200, 200, 200)
        updateConnection()
    end)
end

-- ==================== INICIALIZAÇÃO ====================
local function initialize()
    pcall(function()
        local ui = createMainUI()
        setupFastPunch(ui)
        setupAutoFarm(ui)
        setupTeleportSystem(ui)
        setupSpawnpoint(ui)
        ui.notificationSystem:Show("🚀 Age of Heroes Premium carregado!")
    end)
end
initialize()
