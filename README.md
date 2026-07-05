--[[
    Age of Heroes - Premium (CORRIGIDO)
    Versão: 1.0.3
    Fast Punch com evento correto + Loop TP + Spawnpoint
]]

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

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
    screenGui.Parent = game:GetService("CoreGui")  -- Mudado para CoreGui
    screenGui.ResetOnSpawn = false
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Frame Principal
    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "MainFrame"
    mainFrame.Size = UDim2.new(0, 520, 0, 420)
    mainFrame.Position = UDim2.new(0.5, -260, 0.5, -210)
    mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    mainFrame.BackgroundTransparency = 0.05
    mainFrame.BorderSizePixel = 0
    mainFrame.ClipsDescendants = true
    mainFrame.Active = true
    mainFrame.Parent = screenGui
    
    local mainCorner = Instance.new("UICorner")
    mainCorner.CornerRadius = UDim.new(0, 16)
    mainCorner.Parent = mainFrame
    
    local shadow = Instance.new("ImageLabel")
    shadow.Name = "Shadow"
    shadow.Size = UDim2.new(1, 20, 1, 20)
    shadow.Position = UDim2.new(0, -10, 0, -10)
    shadow.BackgroundTransparency = 1
    shadow.Image = "rbxassetid://6014261993"
    shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
    shadow.ImageTransparency = 0.6
    shadow.ScaleType = Enum.ScaleType.Slice
    shadow.SliceCenter = Rect.new(49, 49, 450, 450)
    shadow.ZIndex = -1
    shadow.Parent = mainFrame
    
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
    
    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(0, 8)
    minCorner.Parent = minimizeButton
    
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
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = closeButton
    
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
        button.Size = UDim2.new(1, -20, 0, 35)
        button.Position = UDim2.new(0, 10, 0, position)
        button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        button.Text = text
        button.TextColor3 = Color3.fromRGB(200, 200, 200)
        button.Font = Enum.Font.GothamMedium
        button.TextSize = 13
        button.BorderSizePixel = 0
        button.Parent = navBar
        
        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 8)
        btnCorner.Parent = button
        
        return button
    end
    
    -- Páginas
    local pages = {}
    
    -- ========== PÁGINA FAST PUNCH (MANTIDA ORIGINAL) ==========
    local fastPunchPage = Instance.new("Frame")
    fastPunchPage.Name = "FastPunchPage"
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
    fpToggle.Name = "FastPunchToggle"
    fpToggle.Size = UDim2.new(0.4, 0, 0, 50)
    fpToggle.Position = UDim2.new(0.3, 0, 0, 110)
    fpToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
    fpToggle.Text = "OFF"
    fpToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    fpToggle.Font = Enum.Font.GothamBold
    fpToggle.TextSize = 20
    fpToggle.BorderSizePixel = 0
    fpToggle.Parent = fastPunchPage
    
    local fpToggleCorner = Instance.new("UICorner")
    fpToggleCorner.CornerRadius = UDim.new(0, 12)
    fpToggleCorner.Parent = fpToggle
    
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
    
    -- ========== PÁGINA TELEPORT (MODIFICADA) ==========
    local teleportPage = Instance.new("Frame")
    teleportPage.Name = "TeleportPage"
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
    
    -- Lista de Jogadores (altura reduzida para caber o Loop TP)
    local playerList = Instance.new("ScrollingFrame")
    playerList.Name = "PlayerList"
    playerList.Size = UDim2.new(1, -20, 0.55, 0)
    playerList.Position = UDim2.new(0, 10, 0, 35)
    playerList.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    playerList.BorderSizePixel = 0
    playerList.ScrollBarThickness = 4
    playerList.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
    playerList.CanvasSize = UDim2.new(0, 0, 0, 0)
    playerList.Parent = teleportPage
    
    local listCorner = Instance.new("UICorner")
    listCorner.CornerRadius = UDim.new(0, 12)
    listCorner.Parent = playerList
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 3)
    listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    listLayout.SortOrder = Enum.SortOrder.Name
    listLayout.Parent = playerList
    
    -- Painel de Loop TP
    local loopFrame = Instance.new("Frame")
    loopFrame.Size = UDim2.new(1, -20, 0, 110)
    loopFrame.Position = UDim2.new(0, 10, 0, 245)
    loopFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    loopFrame.BorderSizePixel = 0
    loopFrame.Parent = teleportPage
    
    local loopCorner = Instance.new("UICorner")
    loopCorner.CornerRadius = UDim.new(0, 12)
    loopCorner.Parent = loopFrame
    
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
    
    local loopCorner2 = Instance.new("UICorner")
    loopCorner2.CornerRadius = UDim.new(0, 8)
    loopCorner2.Parent = loopToggle
    
    -- ========== PÁGINA SPAWNPOINT (NOVA) ==========
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
    
    local spDesc = Instance.new("TextLabel")
    spDesc.Size = UDim2.new(1, -20, 0, 50)
    spDesc.Position = UDim2.new(0, 10, 0, 45)
    spDesc.BackgroundTransparency = 1
    spDesc.Text = "Define seu ponto de respawn no local atual. Ao morrer, você renascerá aqui!"
    spDesc.TextColor3 = Color3.fromRGB(150, 150, 150)
    spDesc.Font = Enum.Font.GothamMedium
    spDesc.TextSize = 12
    spDesc.TextWrapped = true
    spDesc.TextXAlignment = Enum.TextXAlignment.Center
    spDesc.Parent = spawnPage
    
    local spStatus = Instance.new("TextLabel")
    spStatus.Size = UDim2.new(1, -20, 0, 25)
    spStatus.Position = UDim2.new(0, 10, 0, 110)
    spStatus.BackgroundTransparency = 1
    spStatus.Text = "Spawnpoint: Não definido"
    spStatus.TextColor3 = Color3.fromRGB(200, 200, 200)
    spStatus.Font = Enum.Font.GothamMedium
    spStatus.TextSize = 13
    spStatus.TextXAlignment = Enum.TextXAlignment.Center
    spStatus.Parent = spawnPage
    
    local setSpawnBtn = Instance.new("TextButton")
    setSpawnBtn.Size = UDim2.new(0.5, 0, 0, 45)
    setSpawnBtn.Position = UDim2.new(0.25, 0, 0, 150)
    setSpawnBtn.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
    setSpawnBtn.Text = "🎯 DEFINIR SPAWN"
    setSpawnBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    setSpawnBtn.Font = Enum.Font.GothamBold
    setSpawnBtn.TextSize = 14
    setSpawnBtn.BorderSizePixel = 0
    setSpawnBtn.Parent = spawnPage
    
    local spCorner = Instance.new("UICorner")
    spCorner.CornerRadius = UDim.new(0, 10)
    spCorner.Parent = setSpawnBtn
    
    local clearSpawnBtn = Instance.new("TextButton")
    clearSpawnBtn.Size = UDim2.new(0.5, 0, 0, 35)
    clearSpawnBtn.Position = UDim2.new(0.25, 0, 0, 210)
    clearSpawnBtn.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
    clearSpawnBtn.Text = "🗑️ LIMPAR SPAWN"
    clearSpawnBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    clearSpawnBtn.Font = Enum.Font.GothamBold
    clearSpawnBtn.TextSize = 12
    clearSpawnBtn.BorderSizePixel = 0
    clearSpawnBtn.Parent = spawnPage
    
    local spCorner2 = Instance.new("UICorner")
    spCorner2.CornerRadius = UDim.new(0, 8)
    spCorner2.Parent = clearSpawnBtn
    
    -- Botões de Navegação
    local fastPunchBtn = createNavButton("⚡ Fast Punch", 15)
    local teleportBtn = createNavButton("🌍 Teleport", 55)
    local spawnBtn = createNavButton("📍 Spawnpoint", 95)
    
    -- Sistema de arrastar
    local dragging = false
    local dragStart = nil
    local startPos = nil
    
    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or 
           input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or 
           input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(
                startPos.X.Scale, 
                startPos.X.Offset + delta.X,
                startPos.Y.Scale, 
                startPos.Y.Offset + delta.Y
            )
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or 
           input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    
    -- Navegação entre páginas
    local function switchPage(pageName)
        for name, page in pairs(pages) do
            page.Visible = (name == pageName)
        end
        
        fastPunchBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        teleportBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        spawnBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        
        if pageName == "FastPunch" then
            fastPunchBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        elseif pageName == "Teleport" then
            teleportBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        elseif pageName == "Spawn" then
            spawnBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        end
    end
    
    fastPunchBtn.MouseButton1Click:Connect(function() switchPage("FastPunch") end)
    teleportBtn.MouseButton1Click:Connect(function() switchPage("Teleport") end)
    spawnBtn.MouseButton1Click:Connect(function() switchPage("Spawn") end)
    
    -- Minimizar/Restaurar
    local isMinimized = false
    local originalSize = mainFrame.Size
    
    minimizeButton.MouseButton1Click:Connect(function()
        isMinimized = not isMinimized
        
        if isMinimized then
            TweenService:Create(mainFrame, 
                TweenInfo.new(CONFIG.ANIMATION_SPEED, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
                {Size = UDim2.new(0, 520, 0, 45)}
            ):Play()
            minimizeButton.Text = "□"
        else
            TweenService:Create(mainFrame, 
                TweenInfo.new(CONFIG.ANIMATION_SPEED, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
                {Size = originalSize}
            ):Play()
            minimizeButton.Text = "—"
        end
    end)
    
    -- Fechar
    closeButton.MouseButton1Click:Connect(function()
        CONFIG.FAST_PUNCH_ENABLED = false
        CONFIG.LOOP_TP_ENABLED = false
        TweenService:Create(mainFrame, 
            TweenInfo.new(CONFIG.ANIMATION_SPEED, Enum.EasingStyle.Quad, Enum.EasingDirection.In),
            {Size = UDim2.new(0, 0, 0, 0)}
        ):Play()
        task.wait(CONFIG.ANIMATION_SPEED)
        screenGui:Destroy()
    end)
    
    return {
        screenGui = screenGui,
        mainFrame = mainFrame,
        fpToggle = fpToggle,
        staminaLabel = staminaLabel,
        playerList = playerList,
        notificationSystem = NotificationSystem.new(),
        pages = pages,
        teleportPage = teleportPage,
        fastPunchPage = fastPunchPage,
        spawnPage = spawn
