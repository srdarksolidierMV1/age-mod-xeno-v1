--[[
    Age of Heroes - Premium (COMPLETO) - VANTABLACK EDITION
    Versão: 3.2.0
    Design preto absoluto profissional
]]

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer

local CONFIG = {
    UI_NAME = "Age of Heroes",
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
    AURA_INTERVAL = 7,
    ANTI_AFK_ENABLED = false,
}

-- ==================== CORES VANTABLACK ====================
local C = {
    BG = Color3.fromRGB(5, 5, 5),
    HEADER = Color3.fromRGB(8, 8, 8),
    NAV = Color3.fromRGB(6, 6, 6),
    BTN = Color3.fromRGB(15, 15, 15),
    BTN_HOVER = Color3.fromRGB(25, 25, 25),
    ACCENT = Color3.fromRGB(180, 180, 180),
    RED = Color3.fromRGB(180, 50, 50),
    GREEN = Color3.fromRGB(50, 160, 70),
    TEXT = Color3.fromRGB(180, 180, 180),
    TEXT2 = Color3.fromRGB(100, 100, 100),
    BORDER = Color3.fromRGB(20, 20, 20),
    WHITE = Color3.fromRGB(255, 255, 255),
}

-- ==================== IDs DE IMAGENS (LOGO) ====================
-- Para usar suas próprias logos, substitua os números abaixo pelos IDs das imagens que você upar no Roblox
local IMAGES = {
    LOGO_MAIN = "rbxassetid://18876119190", -- Ícone pequeno no título (substitua pelo seu)
    ICON_PUNCH = "rbxassetid://18876119190", -- Ícone Fast Punch
    ICON_FARM = "rbxassetid://18876119190", -- Ícone Auto Farm
    ICON_SYNC = "rbxassetid://18876119190", -- Ícone Sync Dupla
    ICON_TP = "rbxassetid://18876119190", -- Ícone Teleport
    ICON_SPAWN = "rbxassetid://18876119190", -- Ícone Spawnpoint
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
    screenGui.Name = "AOH_Vanta"
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    screenGui.ResetOnSpawn = false
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Frame Principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 490, 0, 400)
    mainFrame.Position = UDim2.new(0.5, -245, 0.5, -200)
    mainFrame.BackgroundColor3 = C.BG
    mainFrame.BorderSizePixel = 1
    mainFrame.BorderColor3 = C.BORDER
    mainFrame.ClipsDescendants = true
    mainFrame.Parent = screenGui
    
    -- Sombra externa
    local shadow = Instance.new("ImageLabel")
    shadow.Size = UDim2.new(1, 24, 1, 24)
    shadow.Position = UDim2.new(0, -12, 0, -12)
    shadow.BackgroundTransparency = 1
    shadow.Image = "rbxassetid://6014261993"
    shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
    shadow.ImageTransparency = 0.5
    shadow.ScaleType = Enum.ScaleType.Slice
    shadow.SliceCenter = Rect.new(49, 49, 450, 450)
    shadow.ZIndex = -1
    shadow.Parent = mainFrame
    
    Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 8)
    
    -- Barra de Título
    local titleBar = Instance.new("Frame")
    titleBar.Size = UDim2.new(1, 0, 0, 40)
    titleBar.BackgroundColor3 = C.HEADER
    titleBar.BorderSizePixel = 0
    titleBar.Parent = mainFrame
    Instance.new("UICorner", titleBar).CornerRadius = UDim.new(0, 8)
    
    local titleFix = Instance.new("Frame")
    titleFix.Size = UDim2.new(1, 0, 0.5, 0)
    titleFix.Position = UDim2.new(0, 0, 0.5, 0)
    titleFix.BackgroundColor3 = C.HEADER
    titleFix.BorderSizePixel = 0
    titleFix.Parent = titleBar
    
    -- Logo no título
    local logoIcon = Instance.new("ImageLabel")
    logoIcon.Size = UDim2.new(0, 18, 0, 18)
    logoIcon.Position = UDim2.new(0, 14, 0.5, -9)
    logoIcon.BackgroundTransparency = 1
    logoIcon.Image = IMAGES.LOGO_MAIN
    logoIcon.Parent = titleBar
    
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(0.7, 0, 1, 0)
    titleLabel.Position = UDim2.new(0, 40, 0, 0)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Text = CONFIG.UI_NAME
    titleLabel.TextColor3 = C.TEXT
    titleLabel.Font = Enum.Font.GothamMedium
    titleLabel.TextSize = 13
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Parent = titleBar
    
    -- Linha decorativa abaixo do título
    local titleLine = Instance.new("Frame")
    titleLine.Size = UDim2.new(1, 0, 0, 1)
    titleLine.Position = UDim2.new(0, 0, 1, -1)
    titleLine.BackgroundColor3 = C.BORDER
    titleLine.BorderSizePixel = 0
    titleLine.Parent = titleBar
    
    -- Botões da barra
    local minimizeButton = Instance.new("TextButton")
    minimizeButton.Size = UDim2.new(0, 26, 0, 22)
    minimizeButton.Position = UDim2.new(1, -58, 0.5, -11)
    minimizeButton.BackgroundColor3 = C.BTN
    minimizeButton.Text = "—"
    minimizeButton.TextColor3 = C.TEXT2
    minimizeButton.Font = Enum.Font.GothamMedium
    minimizeButton.TextSize = 12
    minimizeButton.BorderSizePixel = 0
    minimizeButton.Parent = titleBar
    Instance.new("UICorner", minimizeButton).CornerRadius = UDim.new(0, 4)
    minimizeButton.MouseEnter:Connect(function() minimizeButton.BackgroundColor3 = C.BTN_HOVER end)
    minimizeButton.MouseLeave:Connect(function() minimizeButton.BackgroundColor3 = C.BTN end)
    
    local closeButton = Instance.new("TextButton")
    closeButton.Size = UDim2.new(0, 26, 0, 22)
    closeButton.Position = UDim2.new(1, -28, 0.5, -11)
    closeButton.BackgroundColor3 = C.BTN
    closeButton.Text = "✕"
    closeButton.TextColor3 = C.TEXT2
    closeButton.Font = Enum.Font.GothamMedium
    closeButton.TextSize = 11
    closeButton.BorderSizePixel = 0
    closeButton.Parent = titleBar
    Instance.new("UICorner", closeButton).CornerRadius = UDim.new(0, 4)
    closeButton.MouseEnter:Connect(function() closeButton.BackgroundColor3 = C.RED closeButton.TextColor3 = C.WHITE end)
    closeButton.MouseLeave:Connect(function() closeButton.BackgroundColor3 = C.BTN closeButton.TextColor3 = C.TEXT2 end)
    
    -- Container
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, 0, 1, -40)
    contentFrame.Position = UDim2.new(0, 0, 0, 40)
    contentFrame.BackgroundTransparency = 1
    contentFrame.ClipsDescendants = true
    contentFrame.Parent = mainFrame
    
    -- Navbar
    local navBar = Instance.new("Frame")
    navBar.Size = UDim2.new(0, 140, 1, 0)
    navBar.BackgroundColor3 = C.NAV
    navBar.BorderSizePixel = 0
    navBar.Parent = contentFrame
    Instance.new("UICorner", navBar).CornerRadius = UDim.new(0, 8)
    
    local navFix = Instance.new("Frame")
    navFix.Size = UDim2.new(0.5, 0, 1, 0)
    navFix.Position = UDim2.new(0.5, 0, 0, 0)
    navFix.BackgroundColor3 = C.NAV
    navFix.BorderSizePixel = 0
    navFix.Parent = navBar
    
    -- Área de conteúdo
    local mainContent = Instance.new("Frame")
    mainContent.Size = UDim2.new(1, -150, 1, -10)
    mainContent.Position = UDim2.new(0, 145, 0, 5)
    mainContent.BackgroundColor3 = C.BG
    mainContent.BorderSizePixel = 0
    mainContent.Parent = contentFrame
    
    -- Função para criar botões de navegação (com ícone de imagem)
    local function createNavButton(text, position, iconId)
        local button = Instance.new("TextButton")
        button.Size = UDim2.new(1, -14, 0, 28)
        button.Position = UDim2.new(0, 7, 0, position)
        button.BackgroundColor3 = C.BTN
        button.Text = "     " .. text
        button.TextColor3 = C.TEXT2
        button.Font = Enum.Font.GothamMedium
        button.TextSize = 10
        button.TextXAlignment = Enum.TextXAlignment.Left
        button.BorderSizePixel = 0
        button.AutoButtonColor = false
        button.Parent = navBar
        Instance.new("UICorner", button).CornerRadius = UDim.new(0, 5)
        
        -- Ícone do botão
        if iconId then
            local icon = Instance.new("ImageLabel")
            icon.Size = UDim2.new(0, 16, 0, 16)
            icon.Position = UDim2.new(0, 8, 0.5, -8)
            icon.BackgroundTransparency = 1
            icon.Image = iconId
            icon.Name = "Icon"
            icon.Parent = button
        end
        
        button.MouseEnter:Connect(function() button.BackgroundColor3 = C.BTN_HOVER end)
        button.MouseLeave:Connect(function() button.BackgroundColor3 = C.BTN end)
        
        return button
    end
    
    -- Função para criar botões de ação
    local function createActionButton(text, sizeX, posX, posY, color, parent)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(sizeX, 0, 0, 28)
        btn.Position = UDim2.new(posX, 0, 0, posY)
        btn.BackgroundColor3 = color or C.BTN
        btn.Text = text
        btn.TextColor3 = C.TEXT
        btn.Font = Enum.Font.GothamBold
        btn.TextSize = 10
        btn.BorderSizePixel = 0
        btn.AutoButtonColor = false
        btn.Parent = parent
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 5)
        
        btn.MouseEnter:Connect(function() btn.BackgroundColor3 = C.BTN_HOVER end)
        btn.MouseLeave:Connect(function() btn.BackgroundColor3 = color or C.BTN end)
        
        return btn
    end
    
    local pages = {}
    
    -- ========== FAST PUNCH ==========
    local fastPunchPage = Instance.new("Frame")
    fastPunchPage.Size = UDim2.new(1, 0, 1, 0)
    fastPunchPage.BackgroundColor3 = C.BG
    fastPunchPage.BorderSizePixel = 0
    fastPunchPage.Visible = true
    fastPunchPage.Parent = mainContent
    pages["FastPunch"] = fastPunchPage
    
    -- Linha decorativa
    local fpLine = Instance.new("Frame")
    fpLine.Size = UDim2.new(1, -24, 0, 1)
    fpLine.Position = UDim2.new(0, 12, 0, 28)
    fpLine.BackgroundColor3 = C.BORDER
    fpLine.BorderSizePixel = 0
    fpLine.Parent = fastPunchPage
    
    local fpTitle = Instance.new("TextLabel")
    fpTitle.Size = UDim2.new(1, 0, 0, 20)
    fpTitle.Position = UDim2.new(0, 14, 0, 6)
    fpTitle.BackgroundTransparency = 1
    fpTitle.Text = "FAST PUNCH"
    fpTitle.TextColor3 = C.TEXT
    fpTitle.Font = Enum.Font.GothamBold
    fpTitle.TextSize = 11
    fpTitle.TextXAlignment = Enum.TextXAlignment.Left
    fpTitle.Parent = fastPunchPage
    
    local fpToggle = Instance.new("TextButton")
    fpToggle.Size = UDim2.new(0.4, 0, 0, 40)
    fpToggle.Position = UDim2.new(0.3, 0, 0, 100)
    fpToggle.BackgroundColor3 = C.BTN
    fpToggle.Text = "DISABLED"
    fpToggle.TextColor3 = C.TEXT2
    fpToggle.Font = Enum.Font.GothamBold
    fpToggle.TextSize = 12
    fpToggle.BorderSizePixel = 0
    fpToggle.AutoButtonColor = false
    fpToggle.Parent = fastPunchPage
    Instance.new("UICorner", fpToggle).CornerRadius = UDim.new(0, 5)
    fpToggle.MouseEnter:Connect(function() fpToggle.BackgroundColor3 = C.BTN_HOVER end)
    fpToggle.MouseLeave:Connect(function() fpToggle.BackgroundColor3 = C.BTN end)
    
    -- ========== AUTO FARM ==========
    local autoFarmPage = Instance.new("Frame")
    autoFarmPage.Size = UDim2.new(1, 0, 1, 0)
    autoFarmPage.BackgroundColor3 = C.BG
    autoFarmPage.BorderSizePixel = 0
    autoFarmPage.Visible = false
    autoFarmPage.Parent = mainContent
    pages["AutoFarm"] = autoFarmPage
    
    local afLine = Instance.new("Frame")
    afLine.Size = UDim2.new(1, -24, 0, 1)
    afLine.Position = UDim2.new(0, 12, 0, 24)
    afLine.BackgroundColor3 = C.BORDER
    afLine.Parent = autoFarmPage
    
    local afTitle = Instance.new("TextLabel")
    afTitle.Size = UDim2.new(1, 0, 0, 18)
    afTitle.Position = UDim2.new(0, 14, 0, 4)
    afTitle.BackgroundTransparency = 1
    afTitle.Text = "AUTO FARM"
    afTitle.TextColor3 = C.TEXT
    afTitle.Font = Enum.Font.GothamBold
    afTitle.TextSize = 11
    afTitle.TextXAlignment = Enum.TextXAlignment.Left
    afTitle.Parent = autoFarmPage
    
    local afToggle = createActionButton("START", 0.28, 0.02, 34, C.BTN, autoFarmPage)
    local frzzToggle = createActionButton("FREEZE: OFF", 0.28, 0.33, 34, C.BTN, autoFarmPage)
    local auraToggle = createActionButton("AURA: OFF", 0.28, 0.64, 34, C.BTN, autoFarmPage)
    
    local afStatus = Instance.new("TextLabel")
    afStatus.Size = UDim2.new(1, -20, 0, 14)
    afStatus.Position = UDim2.new(0, 10, 0, 68)
    afStatus.BackgroundTransparency = 1
    afStatus.Text = "Status  Idle"
    afStatus.TextColor3 = C.TEXT2
    afStatus.Font = Enum.Font.GothamMedium
    afStatus.TextSize = 9
    afStatus.TextXAlignment = Enum.TextXAlignment.Center
    afStatus.Parent = autoFarmPage
    
    local afTargetLabel = Instance.new("TextLabel")
    afTargetLabel.Size = UDim2.new(1, -20, 0, 14)
    afTargetLabel.Position = UDim2.new(0, 10, 0, 84)
    afTargetLabel.BackgroundTransparency = 1
    afTargetLabel.Text = "Target  None"
    afTargetLabel.TextColor3 = C.TEXT2
    afTargetLabel.Font = Enum.Font.GothamMedium
    afTargetLabel.TextSize = 9
    afTargetLabel.TextXAlignment = Enum.TextXAlignment.Center
    afTargetLabel.Parent = autoFarmPage
    
    local auraCdLabel = Instance.new("TextLabel")
    auraCdLabel.Size = UDim2.new(0.35, 0, 0, 14)
    auraCdLabel.Position = UDim2.new(0.02, 0, 0, 100)
    auraCdLabel.BackgroundTransparency = 1
    auraCdLabel.Text = "Aura CD (s)"
    auraCdLabel.TextColor3 = C.TEXT2
    auraCdLabel.Font = Enum.Font.GothamMedium
    auraCdLabel.TextSize = 9
    auraCdLabel.TextXAlignment = Enum.TextXAlignment.Left
    auraCdLabel.Parent = autoFarmPage
    
    local auraCdBox = Instance.new("TextBox")
    auraCdBox.Size = UDim2.new(0.2, 0, 0, 20)
    auraCdBox.Position = UDim2.new(0.28, 0, 0, 97)
    auraCdBox.BackgroundColor3 = C.BTN
    auraCdBox.Text = tostring(CONFIG.AURA_INTERVAL)
    auraCdBox.TextColor3 = C.TEXT
    auraCdBox.Font = Enum.Font.GothamBold
    auraCdBox.TextSize = 11
    auraCdBox.BorderSizePixel = 0
    auraCdBox.PlaceholderText = "7"
    auraCdBox.PlaceholderColor3 = C.TEXT2
    auraCdBox.Parent = autoFarmPage
    Instance.new("UICorner", auraCdBox).CornerRadius = UDim.new(0, 5)
    
    local antiAfkBtn = createActionButton("ANTI AFK  OFF", 0.3, 0.52, 97, C.BTN, autoFarmPage)
    
    local selectLabel = Instance.new("TextLabel")
    selectLabel.Size = UDim2.new(1, -20, 0, 14)
    selectLabel.Position = UDim2.new(0, 10, 0, 120)
    selectLabel.BackgroundTransparency = 1
    selectLabel.Text = "Select target"
    selectLabel.TextColor3 = C.TEXT2
    selectLabel.Font = Enum.Font.GothamMedium
    selectLabel.TextSize = 9
    selectLabel.TextXAlignment = Enum.TextXAlignment.Center
    selectLabel.Parent = autoFarmPage
    
    local targetList = Instance.new("ScrollingFrame")
    targetList.Size = UDim2.new(1, -20, 0.40, 0)
    targetList.Position = UDim2.new(0, 10, 0, 136)
    targetList.BackgroundColor3 = C.BTN
    targetList.BorderSizePixel = 0
    targetList.ScrollBarThickness = 2
    targetList.ScrollBarImageColor3 = C.BORDER
    targetList.CanvasSize = UDim2.new(0, 0, 0, 0)
    targetList.Parent = autoFarmPage
    Instance.new("UICorner", targetList).CornerRadius = UDim.new(0, 5)
    
    local targetLayout = Instance.new("UIListLayout")
    targetLayout.Padding = UDim.new(0, 2)
    targetLayout.Parent = targetList
    
    -- ========== SYNC DUPLA ==========
    local syncPage = Instance.new("Frame")
    syncPage.Size = UDim2.new(1, 0, 1, 0)
    syncPage.BackgroundColor3 = C.BG
    syncPage.BorderSizePixel = 0
    syncPage.Visible = false
    syncPage.Parent = mainContent
    pages["Sync"] = syncPage
    
    local syncLine = Instance.new("Frame")
    syncLine.Size = UDim2.new(1, -24, 0, 1)
    syncLine.Position = UDim2.new(0, 12, 0, 24)
    syncLine.BackgroundColor3 = C.BORDER
    syncLine.Parent = syncPage
    
    local syncTitle = Instance.new("TextLabel")
    syncTitle.Size = UDim2.new(1, 0, 0, 18)
    syncTitle.Position = UDim2.new(0, 14, 0, 4)
    syncTitle.BackgroundTransparency = 1
    syncTitle.Text = "SYNC DUPLA"
    syncTitle.TextColor3 = C.TEXT
    syncTitle.Font = Enum.Font.GothamBold
    syncTitle.TextSize = 11
    syncTitle.TextXAlignment = Enum.TextXAlignment.Left
    syncTitle.Parent = syncPage
    
    local syncPartnerLabel = Instance.new("TextLabel")
    syncPartnerLabel.Size = UDim2.new(1, -20, 0, 14)
    syncPartnerLabel.Position = UDim2.new(0, 10, 0, 30)
    syncPartnerLabel.BackgroundTransparency = 1
    syncPartnerLabel.Text = "Partner  None"
    syncPartnerLabel.TextColor3 = C.TEXT
    syncPartnerLabel.Font = Enum.Font.GothamMedium
    syncPartnerLabel.TextSize = 9
    syncPartnerLabel.TextXAlignment = Enum.TextXAlignment.Center
    syncPartnerLabel.Parent = syncPage
    
    local syncPlayerList = Instance.new("ScrollingFrame")
    syncPlayerList.Size = UDim2.new(1, -20, 0.36, 0)
    syncPlayerList.Position = UDim2.new(0, 10, 0, 46)
    syncPlayerList.BackgroundColor3 = C.BTN
    syncPlayerList.BorderSizePixel = 0
    syncPlayerList.ScrollBarThickness = 2
    syncPlayerList.ScrollBarImageColor3 = C.BORDER
    syncPlayerList.CanvasSize = UDim2.new(0, 0, 0, 0)
    syncPlayerList.Parent = syncPage
    Instance.new("UICorner", syncPlayerList).CornerRadius = UDim.new(0, 5)
    
    local syncListLayout = Instance.new("UIListLayout")
    syncListLayout.Padding = UDim.new(0, 2)
    syncListLayout.Parent = syncPlayerList
    
    local agressorBtn = createActionButton("AGRESSOR", 0.42, 0.06, 210, C.BTN, syncPage)
    local vitimaBtn = createActionButton("VICTIM", 0.42, 0.52, 210, C.BTN, syncPage)
    local syncStopBtn = createActionButton("STOP", 0.42, 0.29, 244, C.BTN, syncPage)
    
    local syncStatus = Instance.new("TextLabel")
    syncStatus.Size = UDim2.new(1, -20, 0, 14)
    syncStatus.Position = UDim2.new(0, 10, 0, 278)
    syncStatus.BackgroundTransparency = 1
    syncStatus.Text = "Status  Idle"
    syncStatus.TextColor3 = C.TEXT2
    syncStatus.Font = Enum.Font.GothamMedium
    syncStatus.TextSize = 9
    syncStatus.TextXAlignment = Enum.TextXAlignment.Center
    syncStatus.Parent = syncPage
    
    -- ========== TELEPORT ==========
    local teleportPage = Instance.new("Frame")
    teleportPage.Size = UDim2.new(1, 0, 1, 0)
    teleportPage.BackgroundColor3 = C.BG
    teleportPage.BorderSizePixel = 0
    teleportPage.Visible = false
    teleportPage.Parent = mainContent
    pages["Teleport"] = teleportPage
    
    local tpLine = Instance.new("Frame")
    tpLine.Size = UDim2.new(1, -24, 0, 1)
    tpLine.Position = UDim2.new(0, 12, 0, 24)
    tpLine.BackgroundColor3 = C.BORDER
    tpLine.Parent = teleportPage
    
    local tpTitle = Instance.new("TextLabel")
    tpTitle.Size = UDim2.new(1, 0, 0, 18)
    tpTitle.Position = UDim2.new(0, 14, 0, 4)
    tpTitle.BackgroundTransparency = 1
    tpTitle.Text = "TELEPORT"
    tpTitle.TextColor3 = C.TEXT
    tpTitle.Font = Enum.Font.GothamBold
    tpTitle.TextSize = 11
    tpTitle.TextXAlignment = Enum.TextXAlignment.Left
    tpTitle.Parent = teleportPage
    
    local playerList = Instance.new("ScrollingFrame")
    playerList.Size = UDim2.new(1, -20, 0.55, 0)
    playerList.Position = UDim2.new(0, 10, 0, 30)
    playerList.BackgroundColor3 = C.BTN
    playerList.BorderSizePixel = 0
    playerList.ScrollBarThickness = 2
    playerList.ScrollBarImageColor3 = C.BORDER
    playerList.CanvasSize = UDim2.new(0, 0, 0, 0)
    playerList.Parent = teleportPage
    Instance.new("UICorner", playerList).CornerRadius = UDim.new(0, 5)
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 2)
    listLayout.Parent = playerList
    
    local loopFrame = Instance.new("Frame")
    loopFrame.Size = UDim2.new(1, -20, 0, 85)
    loopFrame.Position = UDim2.new(0, 10, 0, 248)
    loopFrame.BackgroundColor3 = C.BTN
    loopFrame.BorderSizePixel = 0
    loopFrame.Parent = teleportPage
    Instance.new("UICorner", loopFrame).CornerRadius = UDim.new(0, 5)
    
    local loopTitle = Instance.new("TextLabel")
    loopTitle.Size = UDim2.new(1, 0, 0, 18)
    loopTitle.Position = UDim2.new(0, 0, 0, 5)
    loopTitle.BackgroundTransparency = 1
    loopTitle.Text = "LOOP TP"
    loopTitle.TextColor3 = C.TEXT
    loopTitle.Font = Enum.Font.GothamBold
    loopTitle.TextSize = 10
    loopTitle.TextXAlignment = Enum.TextXAlignment.Center
    loopTitle.Parent = loopFrame
    
    local loopTargetLabel = Instance.new("TextLabel")
    loopTargetLabel.Size = UDim2.new(1, 0, 0, 14)
    loopTargetLabel.Position = UDim2.new(0, 0, 0, 24)
    loopTargetLabel.BackgroundTransparency = 1
    loopTargetLabel.Text = "Target  None"
    loopTargetLabel.TextColor3 = C.TEXT2
    loopTargetLabel.Font = Enum.Font.GothamMedium
    loopTargetLabel.TextSize = 9
    loopTargetLabel.TextXAlignment = Enum.TextXAlignment.Center
    loopTargetLabel.Parent = loopFrame
    
    local loopToggle = Instance.new("TextButton")
    loopToggle.Size = UDim2.new(0.5, 0, 0, 26)
    loopToggle.Position = UDim2.new(0.25, 0, 0, 46)
    loopToggle.BackgroundColor3 = C.BTN
    loopToggle.Text = "START LOOP"
    loopToggle.TextColor3 = C.TEXT
    loopToggle.Font = Enum.Font.GothamBold
    loopToggle.TextSize = 10
    loopToggle.BorderSizePixel = 0
    loopToggle.AutoButtonColor = false
    loopToggle.Parent = loopFrame
    Instance.new("UICorner", loopToggle).CornerRadius = UDim.new(0, 5)
    
    -- ========== SPAWNPOINT ==========
    local spawnPage = Instance.new("Frame")
    spawnPage.Size = UDim2.new(1, 0, 1, 0)
    spawnPage.BackgroundColor3 = C.BG
    spawnPage.BorderSizePixel = 0
    spawnPage.Visible = false
    spawnPage.Parent = mainContent
    pages["Spawn"] = spawnPage
    
    local spLine = Instance.new("Frame")
    spLine.Size = UDim2.new(1, -24, 0, 1)
    spLine.Position = UDim2.new(0, 12, 0, 24)
    spLine.BackgroundColor3 = C.BORDER
    spLine.Parent = spawnPage
    
    local spTitle = Instance.new("TextLabel")
    spTitle.Size = UDim2.new(1, 0, 0, 18)
    spTitle.Position = UDim2.new(0, 14, 0, 4)
    spTitle.BackgroundTransparency = 1
    spTitle.Text = "SPAWNPOINT"
    spTitle.TextColor3 = C.TEXT
    spTitle.Font = Enum.Font.GothamBold
    spTitle.TextSize = 11
    spTitle.TextXAlignment = Enum.TextXAlignment.Left
    spTitle.Parent = spawnPage
    
    local spStatus = Instance.new("TextLabel")
    spStatus.Size = UDim2.new(1, -20, 0, 14)
    spStatus.Position = UDim2.new(0, 10, 0, 85)
    spStatus.BackgroundTransparency = 1
    spStatus.Text = "Spawnpoint  Not set"
    spStatus.TextColor3 = C.TEXT2
    spStatus.Font = Enum.Font.GothamMedium
    spStatus.TextSize = 9
    spStatus.TextXAlignment = Enum.TextXAlignment.Center
    spStatus.Parent = spawnPage
    
    local setSpawnBtn = createActionButton("SET SPAWN", 0.5, 0.25, 125, C.BTN, spawnPage)
    local clearSpawnBtn = createActionButton("CLEAR", 0.5, 0.25, 168, C.BTN, spawnPage)
    
    -- ========== NAVEGAÇÃO ==========
    local fastPunchBtn = createNavButton("Fast Punch", 10, IMAGES.ICON_PUNCH)
    local autoFarmBtn = createNavButton("Auto Farm", 43, IMAGES.ICON_FARM)
    local syncBtn = createNavButton("Sync Dupla", 76, IMAGES.ICON_SYNC)
    local teleportBtn = createNavButton("Teleport", 109, IMAGES.ICON_TP)
    local spawnBtn = createNavButton("Spawnpoint", 142, IMAGES.ICON_SPAWN)
    
    -- ARRASTAR
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
    
    -- NAVEGAÇÃO
    local function switchPage(pageName)
        for name, page in pairs(pages) do page.Visible = (name == pageName) end
        fastPunchBtn.BackgroundColor3 = pageName == "FastPunch" and C.BTN_HOVER or C.BTN
        autoFarmBtn.BackgroundColor3 = pageName == "AutoFarm" and C.BTN_HOVER or C.BTN
        syncBtn.BackgroundColor3 = pageName == "Sync" and C.BTN_HOVER or C.BTN
        teleportBtn.BackgroundColor3 = pageName == "Teleport" and C.BTN_HOVER or C.BTN
        spawnBtn.BackgroundColor3 = pageName == "Spawn" and C.BTN_HOVER or C.BTN
    end
    
    fastPunchBtn.MouseButton1Click:Connect(function() switchPage("FastPunch") end)
    autoFarmBtn.MouseButton1Click:Connect(function() switchPage("AutoFarm") end)
    syncBtn.MouseButton1Click:Connect(function() switchPage("Sync") end)
    teleportBtn.MouseButton1Click:Connect(function() switchPage("Teleport") end)
    spawnBtn.MouseButton1Click:Connect(function() switchPage("Spawn") end)
    
    -- MINIMIZAR
    local minimized = false
    local originalSize = mainFrame.Size
    minimizeButton.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            TweenService:Create(mainFrame, TweenInfo.new(0.2), {Size = UDim2.new(0, 490, 0, 40)}):Play()
            minimizeButton.Text = "+"
        else
            TweenService:Create(mainFrame, TweenInfo.new(0.2), {Size = originalSize}):Play()
            minimizeButton.Text = "—"
        end
    end)
    
    -- FECHAR
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
        screenGui = screenGui, mainFrame = mainFrame, fpToggle = fpToggle,
        playerList = playerList, notificationSystem = NotificationSystem.new(), pages = pages,
        teleportPage = teleportPage, fastPunchPage = fastPunchPage, autoFarmPage = autoFarmPage,
        spawnPage = spawnPage, loopToggle = loopToggle, loopTargetLabel = loopTargetLabel,
        setSpawnBtn = setSpawnBtn, clearSpawnBtn = clearSpawnBtn, spStatus = spStatus,
        afToggle = afToggle, afStatus = afStatus, afTargetLabel = afTargetLabel,
        targetList = targetList, frzzToggle = frzzToggle, auraToggle = auraToggle,
        auraCdBox = auraCdBox, antiAfkBtn = antiAfkBtn,
        syncPage = syncPage, syncPlayerList = syncPlayerList, syncPartnerLabel = syncPartnerLabel,
        agressorBtn = agressorBtn, vitimaBtn = vitimaBtn, syncStopBtn = syncStopBtn,
        syncStatus = syncStatus,
    }
end

-- ==================== FAST PUNCH ====================
local function setupFastPunch(ui)
    local isRunning = false
    local PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Events") and ReplicatedStorage.Events:FindFirstChild("Punch")
    if not PUNCH_EVENT then PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Punch") end
    
    local function doPunch()
        if PUNCH_EVENT and PUNCH_EVENT:IsA("RemoteEvent") then
            pcall(function() PUNCH_EVENT:FireServer(0, 0.1, 1) end)
        end
    end
    
    ui.fpToggle.MouseButton1Click:Connect(function()
        CONFIG.FAST_PUNCH_ENABLED = not CONFIG.FAST_PUNCH_ENABLED
        if CONFIG.FAST_PUNCH_ENABLED then
            ui.fpToggle.Text = "ENABLED"
            ui.fpToggle.TextColor3 = C.WHITE
            ui.fpToggle.BackgroundColor3 = C.GREEN
            if not isRunning then
                isRunning = true
                task.spawn(function()
                    while CONFIG.FAST_PUNCH_ENABLED do
                        doPunch()
                        task.wait(CONFIG.FAST_PUNCH_SPEED)
                    end
                    isRunning = false
                end)
            end
        else
            ui.fpToggle.Text = "DISABLED"
            ui.fpToggle.TextColor3 = C.TEXT2
            ui.fpToggle.BackgroundColor3 = C.BTN
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
        if not silent then ui.notificationSystem:Show("Teleported to " .. player.Name) end
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
                    btn.Size = UDim2.new(1, -10, 0, 28)
                    btn.BackgroundColor3 = C.BTN
                    btn.Text = player.Name
                    btn.TextColor3 = C.TEXT
                    btn.Font = Enum.Font.GothamMedium
                    btn.TextSize = 10
                    btn.BorderSizePixel = 0
                    btn.AutoButtonColor = false
                    btn.Parent = ui.playerList
                    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 5)
                    btn.MouseEnter:Connect(function() btn.BackgroundColor3 = C.BTN_HOVER end)
                    btn.MouseLeave:Connect(function() btn.BackgroundColor3 = C.BTN end)
                    btn.MouseButton1Click:Connect(function()
                        selectedPlayer = player
                        ui.loopTargetLabel.Text = "Target  " .. player.Name
                        for _, c in pairs(ui.playerList:GetChildren()) do
                            if c:IsA("TextButton") then c.BackgroundColor3 = C.BTN end
                        end
                        btn.BackgroundColor3 = C.BTN_HOVER
                        teleportToPlayer(player, false)
                    end)
                    ySize = ySize + 30
                end
            end
            ui.playerList.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
        end)
    end
    
    ui.loopToggle.MouseButton1Click:Connect(function()
        CONFIG.LOOP_TP_ENABLED = not CONFIG.LOOP_TP_ENABLED
        if CONFIG.LOOP_TP_ENABLED then
            if not selectedPlayer then ui.notificationSystem:Show("Select a player first!") CONFIG.LOOP_TP_ENABLED = false return end
            ui.loopToggle.Text = "STOP LOOP"
            ui.loopToggle.BackgroundColor3 = C.RED
            ui.loopToggle.TextColor3 = C.WHITE
            CONFIG.LOOP_TP_TARGET = selectedPlayer
            task.spawn(function()
                while CONFIG.LOOP_TP_ENABLED and CONFIG.LOOP_TP_TARGET do
                    teleportToPlayer(CONFIG.LOOP_TP_TARGET, true)
                    task.wait(CONFIG.LOOP_TP_INTERVAL)
                end
            end)
        else
            ui.loopToggle.Text = "START LOOP"
            ui.loopToggle.TextColor3 = C.TEXT
            ui.loopToggle.BackgroundColor3 = C.BTN
            CONFIG.LOOP_TP_TARGET = nil
        end
    end)
    
    ui.teleportPage:GetPropertyChangedSignal("Visible"):Connect(function() if ui.teleportPage.Visible then updatePlayerList() end end)
    Players.PlayerAdded:Connect(function() task.wait(0.2) updatePlayerList() end)
    Players.PlayerRemoving:Connect(function(p)
        task.wait(0.2) updatePlayerList()
        if selectedPlayer == p then selectedPlayer = nil ui.loopTargetLabel.Text = "Target  None"
            if CONFIG.LOOP_TP_ENABLED then CONFIG.LOOP_TP_ENABLED = false ui.loopToggle.Text = "START LOOP" ui.loopToggle.TextColor3 = C.TEXT ui.loopToggle.BackgroundColor3 = C.BTN end
        end
    end)
    updatePlayerList()
end

-- ==================== AUTO FARM ====================
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
    
    ui.auraCdBox.FocusLost:Connect(function()
        local num = tonumber(ui.auraCdBox.Text)
        if num and num >= 1 then CONFIG.AURA_INTERVAL = num else ui.auraCdBox.Text = tostring(CONFIG.AURA_INTERVAL) end
    end)
    
    -- INICIAR
    ui.afToggle.MouseButton1Click:Connect(function()
        CONFIG.AUTO_FARM_ENABLED = not CONFIG.AUTO_FARM_ENABLED
        if CONFIG.AUTO_FARM_ENABLED then
            if not selectedTarget then ui.notificationSystem:Show("Select a target first!") CONFIG.AUTO_FARM_ENABLED = false return end
            ui.afToggle.Text = "STOP"
            ui.afToggle.TextColor3 = C.WHITE
            ui.afToggle.BackgroundColor3 = C.RED
            ui.afStatus.Text = "Status  Running..."
            
            task.spawn(function()
                while CONFIG.AUTO_FARM_ENABLED do
                    if isPlayerAlive(selectedTarget) then
                        teleportToTarget()
                        ui.afStatus.Text = "Status  Killing " .. selectedTarget.Name
                        while CONFIG.AUTO_FARM_ENABLED and isPlayerAlive(selectedTarget) do
                            doPunch()
                            task.wait(0.05)
                        end
                        if not CONFIG.AUTO_FARM_ENABLED then break end
                        
                        ui.afStatus.Text = "Status  Resetting..."
                        resetCharacter()
                        task.wait(0.5)
                        
                        ui.afStatus.Text = "Status  Waiting respawn..."
                        while CONFIG.AUTO_FARM_ENABLED and not isPlayerAlive(selectedTarget) do task.wait(0.5) end
                        if not CONFIG.AUTO_FARM_ENABLED then break end
                        
                        for i = CONFIG.SHIELD_WAIT, 1, -1 do
                            if not CONFIG.AUTO_FARM_ENABLED then break end
                            ui.afStatus.Text = "Status  Shield " .. i .. "s..."
                            task.wait(1)
                        end
                    else
                        while CONFIG.AUTO_FARM_ENABLED and not isPlayerAlive(selectedTarget) do task.wait(1) end
                        if CONFIG.AUTO_FARM_ENABLED then
                            for i = CONFIG.SHIELD_WAIT, 1, -1 do
                                if not CONFIG.AUTO_FARM_ENABLED then break end
                                ui.afStatus.Text = "Status  Shield " .. i .. "s..."
                                task.wait(1)
                            end
                        end
                    end
                end
            end)
        else
            ui.afToggle.Text = "START"
            ui.afToggle.TextColor3 = C.TEXT
            ui.afToggle.BackgroundColor3 = C.BTN
            ui.afStatus.Text = "Status  Idle"
        end
    end)
    
    -- FRZZ
    local freezeConnection = nil
    ui.frzzToggle.MouseButton1Click:Connect(function()
        CONFIG.FRZZ_ENABLED = not CONFIG.FRZZ_ENABLED
        if CONFIG.FRZZ_ENABLED then
            ui.frzzToggle.Text = "FREEZE  ON"
            ui.frzzToggle.TextColor3 = C.WHITE
            ui.frzzToggle.BackgroundColor3 = C.ACCENT
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("HumanoidRootPart") then CONFIG.frozenCFrame = char.HumanoidRootPart.CFrame end
            freezeConnection = RunService.Heartbeat:Connect(function()
                if not CONFIG.FRZZ_ENABLED then return end
                local char = LocalPlayer.Character
                if char and char:FindFirstChild("HumanoidRootPart") and CONFIG.frozenCFrame then
                    char.HumanoidRootPart.CFrame = CONFIG.frozenCFrame
                    char.HumanoidRootPart.Velocity = Vector3.new(0, 0, 0)
                end
            end)
        else
            ui.frzzToggle.Text = "FREEZE  OFF"
            ui.frzzToggle.TextColor3 = C.TEXT
            ui.frzzToggle.BackgroundColor3 = C.BTN
            CONFIG.frozenCFrame = nil
            if freezeConnection then freezeConnection:Disconnect() end
        end
    end)
    
    -- AURA
    ui.auraToggle.MouseButton1Click:Connect(function()
        CONFIG.AURA_ENABLED = not CONFIG.AURA_ENABLED
        if CONFIG.AURA_ENABLED then
            local num = tonumber(ui.auraCdBox.Text)
            if num and num >= 1 then CONFIG.AURA_INTERVAL = num end
            ui.auraCdBox.Text = tostring(CONFIG.AURA_INTERVAL)
            ui.auraToggle.Text = "AURA  ON"
            ui.auraToggle.TextColor3 = C.WHITE
            ui.auraToggle.BackgroundColor3 = C.YELLOW
            
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
            ui.auraToggle.Text = "AURA  OFF"
            ui.auraToggle.TextColor3 = C.TEXT
            ui.auraToggle.BackgroundColor3 = C.BTN
        end
    end)
    
    -- ANTI AFK
    ui.antiAfkBtn.MouseButton1Click:Connect(function()
        CONFIG.ANTI_AFK_ENABLED = not CONFIG.ANTI_AFK_ENABLED
        if CONFIG.ANTI_AFK_ENABLED then
            ui.antiAfkBtn.Text = "ANTI AFK  ON"
            ui.antiAfkBtn.TextColor3 = C.WHITE
            ui.antiAfkBtn.BackgroundColor3 = C.GREEN
            task.spawn(function()
                while CONFIG.ANTI_AFK_ENABLED do
                    task.wait(180)
                    if CONFIG.ANTI_AFK_ENABLED then
                        pcall(function()
                            local char = LocalPlayer.Character
                            if char and char:FindFirstChild("HumanoidRootPart") then
                                local root = char.HumanoidRootPart
                                local originalCF = root.CFrame
                                root.CFrame = root.CFrame + (root.CFrame.LookVector * 3)
                                task.wait(0.2)
                                root.CFrame = originalCF
                            end
                        end)
                    end
                end
            end)
        else
            ui.antiAfkBtn.Text = "ANTI AFK  OFF"
            ui.antiAfkBtn.TextColor3 = C.TEXT
            ui.antiAfkBtn.BackgroundColor3 = C.BTN
        end
    end)
    
    -- LISTA DE ALVOS
    local function updateTargetList()
        pcall(function()
            for _, child in pairs(ui.targetList:GetChildren()) do if child:IsA("TextButton") then child:Destroy() end end
            local ySize = 0
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local btn = Instance.new("TextButton")
                    btn.Size = UDim2.new(1, -10, 0, 26)
                    btn.BackgroundColor3 = C.BTN
                    btn.Text = player.Name
                    btn.TextColor3 = C.TEXT
                    btn.Font = Enum.Font.GothamMedium
                    btn.TextSize = 10
                    btn.BorderSizePixel = 0
                    btn.AutoButtonColor = false
                    btn.Parent = ui.targetList
                    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 5)
                    btn.MouseEnter:Connect(function() btn.BackgroundColor3 = C.BTN_HOVER end)
                    btn.MouseLeave:Connect(function() btn.BackgroundColor3 = C.BTN end)
                    btn.MouseButton1Click:Connect(function()
                        selectedTarget = player
                        ui.afTargetLabel.Text = "Target  " .. player.Name
                        for _, c in pairs(ui.targetList:GetChildren()) do if c:IsA("TextButton") then c.BackgroundColor3 = C.BTN end end
                        btn.BackgroundColor3 = C.BTN_HOVER
                    end)
                    ySize = ySize + 28
                end
            end
            ui.targetList.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
        end)
    end
    
    ui.autoFarmPage:GetPropertyChangedSignal("Visible"):Connect(function() if ui.autoFarmPage.Visible then updateTargetList() end end)
    Players.PlayerAdded:Connect(function() task.wait(0.2) updateTargetList() end)
    Players.PlayerRemoving:Connect(function(p)
        task.wait(0.2) updateTargetList()
        if selectedTarget == p then selectedTarget = nil ui.afTargetLabel.Text = "Target  None"
            if CONFIG.AUTO_FARM_ENABLED then CONFIG.AUTO_FARM_ENABLED = false ui.afToggle.Text = "START" ui.afToggle.TextColor3 = C.TEXT ui.afToggle.BackgroundColor3 = C.BTN end
        end
    end)
    updateTargetList()
end

-- ==================== SYNC DUPLA ====================
local function setupSyncDupla(ui)
    local PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Events") and ReplicatedStorage.Events:FindFirstChild("Punch")
    if not PUNCH_EVENT then PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Punch") end
    
    local SYNC_ENABLED = false
    local SYNC_ROLE = nil
    local SYNC_PARTNER = nil
    
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
    
    local function teleportToPlayer(player)
        if not player then return end
        local targetChar = player.Character
        if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then return end
        local myChar = LocalPlayer.Character
        if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then return end
        myChar.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, 2.5)
    end
    
    local function resetCharacter()
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("Humanoid") then char.Humanoid.Health = 0 end
    end
    
    local function updateSyncPlayerList()
        pcall(function()
            for _, child in pairs(ui.syncPlayerList:GetChildren()) do if child:IsA("TextButton") then child:Destroy() end end
            local ySize = 0
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local btn = Instance.new("TextButton")
                    btn.Size = UDim2.new(1, -10, 0, 26)
                    btn.BackgroundColor3 = C.BTN
                    btn.Text = player.Name
                    btn.TextColor3 = C.TEXT
                    btn.Font = Enum.Font.GothamMedium
                    btn.TextSize = 10
                    btn.BorderSizePixel = 0
                    btn.AutoButtonColor = false
                    btn.Parent = ui.syncPlayerList
                    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 5)
                    btn.MouseEnter:Connect(function() btn.BackgroundColor3 = C.BTN_HOVER end)
                    btn.MouseLeave:Connect(function() btn.BackgroundColor3 = C.BTN end)
                    btn.MouseButton1Click:Connect(function()
                        SYNC_PARTNER = player
                        ui.syncPartnerLabel.Text = "Partner  " .. player.Name
                        for _, c in pairs(ui.syncPlayerList:GetChildren()) do if c:IsA("TextButton") then c.BackgroundColor3 = C.BTN end end
                        btn.BackgroundColor3 = C.BTN_HOVER
                    end)
                    ySize = ySize + 28
                end
            end
            ui.syncPlayerList.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
        end)
    end
    
    ui.syncPage:GetPropertyChangedSignal("Visible"):Connect(function() if ui.syncPage.Visible then updateSyncPlayerList() end end)
    Players.PlayerAdded:Connect(function() task.wait(0.2) updateSyncPlayerList() end)
    Players.PlayerRemoving:Connect(function(p)
        task.wait(0.2) updateSyncPlayerList()
        if SYNC_PARTNER == p then SYNC_PARTNER = nil ui.syncPartnerLabel.Text = "Partner  None"
            if SYNC_ENABLED then SYNC_ENABLED = false SYNC_ROLE = nil ui.syncStatus.Text = "Status  Idle" end
        end
    end)
    updateSyncPlayerList()
    
    -- AGRESSOR
    ui.agressorBtn.MouseButton1Click:Connect(function()
        if not SYNC_PARTNER then ui.notificationSystem:Show("Select a partner!") return end
        SYNC_ENABLED = true
        SYNC_ROLE = "agressor"
        ui.agressorBtn.TextColor3 = C.WHITE
        ui.agressorBtn.BackgroundColor3 = C.RED
        ui.syncStatus.Text = "Status  Killing " .. SYNC_PARTNER.Name
        task.spawn(function()
            while SYNC_ENABLED and SYNC_ROLE == "agressor" do
                if isPlayerAlive(SYNC_PARTNER) then
                    teleportToPlayer(SYNC_PARTNER)
                    while SYNC_ENABLED and isPlayerAlive(SYNC_PARTNER) do doPunch() task.wait(0.05) end
                    if not SYNC_ENABLED then break end
                    resetCharacter() task.wait(0.5)
                    SYNC_ROLE = "vitima"
                    ui.syncStatus.Text = "Status  Waiting death"
                    while SYNC_ENABLED and isPlayerAlive(LocalPlayer) do task.wait(0.5) end
                    while SYNC_ENABLED and not isPlayerAlive(LocalPlayer) do task.wait(0.5) end
                    for i = 5, 1, -1 do if not SYNC_ENABLED then break end ui.syncStatus.Text = "Shield  " .. i .. "s" task.wait(1) end
                    SYNC_ROLE = "agressor"
                    ui.syncStatus.Text = "Status  Killing " .. SYNC_PARTNER.Name
                else
                    while SYNC_ENABLED and not isPlayerAlive(SYNC_PARTNER) do task.wait(1) end
                    for i = 5, 1, -1 do if not SYNC_ENABLED then break end task.wait(1) end
                end
            end
            ui.agressorBtn.TextColor3 = C.TEXT
            ui.agressorBtn.BackgroundColor3 = C.BTN
        end)
    end)
    
    -- VITIMA
    ui.vitimaBtn.MouseButton1Click:Connect(function()
        if not SYNC_PARTNER then ui.notificationSystem:Show("Select a partner!") return end
        SYNC_ENABLED = true
        SYNC_ROLE = "vitima"
        ui.vitimaBtn.TextColor3 = C.WHITE
        ui.vitimaBtn.BackgroundColor3 = C.ACCENT
        ui.syncStatus.Text = "Status  Waiting death"
        task.spawn(function()
            while SYNC_ENABLED and SYNC_ROLE == "vitima" do
                while SYNC_ENABLED and isPlayerAlive(LocalPlayer) do task.wait(0.5) end
                while SYNC_ENABLED and not isPlayerAlive(LocalPlayer) do task.wait(0.5) end
                for i = 5, 1, -1 do if not SYNC_ENABLED then break end task.wait(1) end
                SYNC_ROLE = "agressor"
                if isPlayerAlive(SYNC_PARTNER) then
                    teleportToPlayer(SYNC_PARTNER)
                    while SYNC_ENABLED and isPlayerAlive(SYNC_PARTNER) do doPunch() task.wait(0.05) end
                end
                if not SYNC_ENABLED then break end
                resetCharacter() task.wait(0.5)
                SYNC_ROLE = "vitima"
                ui.syncStatus.Text = "Status  Waiting death"
            end
            ui.vitimaBtn.TextColor3 = C.TEXT
            ui.vitimaBtn.BackgroundColor3 = C.BTN
        end)
    end)
    
    ui.syncStopBtn.MouseButton1Click:Connect(function()
        SYNC_ENABLED = false
        SYNC_ROLE = nil
        ui.syncStatus.Text = "Status  Idle"
        ui.agressorBtn.TextColor3 = C.TEXT
        ui.agressorBtn.BackgroundColor3 = C.BTN
        ui.vitimaBtn.TextColor3 = C.TEXT
        ui.vitimaBtn.BackgroundColor3 = C.BTN
    end)
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
        if not char or not char:FindFirstChild("HumanoidRootPart") then ui.notificationSystem:Show("You need to be in game!") return end
        CONFIG.SPAWNPOINT = char.HumanoidRootPart.CFrame
        ui.spStatus.Text = "Spawnpoint  Set"
        ui.spStatus.TextColor3 = C.GREEN
        updateConnection()
    end)
    ui.clearSpawnBtn.MouseButton1Click:Connect(function()
        CONFIG.SPAWNPOINT = nil
        ui.spStatus.Text = "Spawnpoint  Not set"
        ui.spStatus.TextColor3 = C.TEXT2
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
        setupSyncDupla(ui)
        ui.notificationSystem:Show("Age of Heroes loaded!")
    end)
end
initialize()
