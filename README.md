--[[ Age of Heroes Premium - Completo ]]

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

local CONFIG = {
    UI_NAME = "Age of Heroes Premium",
    FAST_PUNCH_ENABLED = false,
    FAST_PUNCH_SPEED = 0.05,
    LOOP_TP_ENABLED = false,
    LOOP_TP_TARGET = nil,
    LOOP_TP_INTERVAL = 0.1,
    SPAWNPOINT = nil,
    ANIMATION_SPEED = 0.3,
}

-- Notificação
local function notify(msg)
    pcall(function()
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Age of Heroes",
            Text = msg,
            Duration = 2,
        })
    end)
end

-- Criar UI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AOHUI"
screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
screenGui.ResetOnSpawn = false

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 520, 0, 420)
mainFrame.Position = UDim2.new(0.5, -260, 0.5, -210)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Parent = screenGui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 16)
mainCorner.Parent = mainFrame

-- Barra de título
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 45)
titleBar.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 16)
titleCorner.Parent = titleBar

local titleFix = Instance.new("Frame")
titleFix.Size = UDim2.new(1, 0, 0.5, 0)
titleFix.Position = UDim2.new(0, 0, 0.5, 0)
titleFix.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
titleFix.BorderSizePixel = 0
titleFix.Parent = titleBar

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

-- Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 30, 0, 30)
minimizeBtn.Position = UDim2.new(1, -75, 0, 7)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
minimizeBtn.Text = "—"
minimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.TextSize = 18
minimizeBtn.BorderSizePixel = 0
minimizeBtn.Parent = titleBar
Instance.new("UICorner", minimizeBtn).CornerRadius = UDim.new(0, 8)

-- Fechar
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 7)
closeBtn.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
closeBtn.Text = "✕"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 18
closeBtn.BorderSizePixel = 0
closeBtn.Parent = titleBar
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0, 8)

-- Container
local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, 0, 1, -45)
contentFrame.Position = UDim2.new(0, 0, 0, 45)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

-- Navbar
local navBar = Instance.new("Frame")
navBar.Size = UDim2.new(0, 150, 1, 0)
navBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
navBar.BorderSizePixel = 0
navBar.Parent = contentFrame
Instance.new("UICorner", navBar).CornerRadius = UDim.new(0, 16)

local navFix = Instance.new("Frame")
navFix.Size = UDim2.new(0.5, 0, 1, 0)
navFix.Position = UDim2.new(0.5, 0, 0, 0)
navFix.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
navFix.BorderSizePixel = 0
navFix.Parent = navBar

-- Área de conteúdo
local mainContent = Instance.new("Frame")
mainContent.Size = UDim2.new(1, -160, 1, -10)
mainContent.Position = UDim2.new(0, 155, 0, 5)
mainContent.BackgroundTransparency = 1
mainContent.Parent = contentFrame

-- Função para botão da navbar
local function createNavBtn(text, y)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 35)
    btn.Position = UDim2.new(0, 10, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.Font = Enum.Font.GothamMedium
    btn.TextSize = 13
    btn.BorderSizePixel = 0
    btn.Parent = navBar
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    return btn
end

-- Páginas
local pages = {}

-- Página Fast Punch
local fpPage = Instance.new("Frame")
fpPage.Size = UDim2.new(1, 0, 1, 0)
fpPage.BackgroundTransparency = 1
fpPage.Visible = true
fpPage.Parent = mainContent
pages["fp"] = fpPage

local fpTitle = Instance.new("TextLabel")
fpTitle.Size = UDim2.new(1, 0, 0, 30)
fpTitle.Position = UDim2.new(0, 0, 0, 10)
fpTitle.BackgroundTransparency = 1
fpTitle.Text = "⚡ Fast Punch System"
fpTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
fpTitle.Font = Enum.Font.GothamBold
fpTitle.TextSize = 18
fpTitle.TextXAlignment = Enum.TextXAlignment.Center
fpTitle.Parent = fpPage

local fpDesc = Instance.new("TextLabel")
fpDesc.Size = UDim2.new(1, -20, 0, 60)
fpDesc.Position = UDim2.new(0, 10, 0, 50)
fpDesc.BackgroundTransparency = 1
fpDesc.Text = "ON: Segure o clique = metralhadora até acabar energia\nOFF: Clique normal = 1 soco por clique"
fpDesc.TextColor3 = Color3.fromRGB(150, 150, 150)
fpDesc.Font = Enum.Font.GothamMedium
fpDesc.TextSize = 11
fpDesc.TextWrapped = true
fpDesc.TextXAlignment = Enum.TextXAlignment.Center
fpDesc.Parent = fpPage

local fpToggle = Instance.new("TextButton")
fpToggle.Size = UDim2.new(0.4, 0, 0, 50)
fpToggle.Position = UDim2.new(0.3, 0, 0, 130)
fpToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
fpToggle.Text = "OFF"
fpToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
fpToggle.Font = Enum.Font.GothamBold
fpToggle.TextSize = 20
fpToggle.BorderSizePixel = 0
fpToggle.Parent = fpPage
Instance.new("UICorner", fpToggle).CornerRadius = UDim.new(0, 12)

local staminaLabel = Instance.new("TextLabel")
staminaLabel.Size = UDim2.new(1, -20, 0, 25)
staminaLabel.Position = UDim2.new(0, 10, 0, 200)
staminaLabel.BackgroundTransparency = 1
staminaLabel.Text = "Energia: --"
staminaLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
staminaLabel.Font = Enum.Font.GothamMedium
staminaLabel.TextSize = 12
staminaLabel.TextXAlignment = Enum.TextXAlignment.Center
staminaLabel.Parent = fpPage

-- Página Teleport
local tpPage = Instance.new("Frame")
tpPage.Size = UDim2.new(1, 0, 1, 0)
tpPage.BackgroundTransparency = 1
tpPage.Visible = false
tpPage.Parent = mainContent
pages["tp"] = tpPage

local tpTitle = Instance.new("TextLabel")
tpTitle.Size = UDim2.new(1, 0, 0, 25)
tpTitle.Position = UDim2.new(0, 0, 0, 5)
tpTitle.BackgroundTransparency = 1
tpTitle.Text = "🌍 Player Teleport"
tpTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
tpTitle.Font = Enum.Font.GothamBold
tpTitle.TextSize = 16
tpTitle.TextXAlignment = Enum.TextXAlignment.Center
tpTitle.Parent = tpPage

local playerList = Instance.new("ScrollingFrame")
playerList.Size = UDim2.new(1, -20, 0.55, 0)
playerList.Position = UDim2.new(0, 10, 0, 35)
playerList.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
playerList.BorderSizePixel = 0
playerList.ScrollBarThickness = 4
playerList.CanvasSize = UDim2.new(0, 0, 0, 0)
playerList.Parent = tpPage
Instance.new("UICorner", playerList).CornerRadius = UDim.new(0, 12)

local listLayout = Instance.new("UIListLayout")
listLayout.Padding = UDim.new(0, 3)
listLayout.Parent = playerList

-- Loop TP
local loopFrame = Instance.new("Frame")
loopFrame.Size = UDim2.new(1, -20, 0, 110)
loopFrame.Position = UDim2.new(0, 10, 0, 245)
loopFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
loopFrame.BorderSizePixel = 0
loopFrame.Parent = tpPage
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

-- Página Spawnpoint
local spPage = Instance.new("Frame")
spPage.Size = UDim2.new(1, 0, 1, 0)
spPage.BackgroundTransparency = 1
spPage.Visible = false
spPage.Parent = mainContent
pages["sp"] = spPage

local spTitle = Instance.new("TextLabel")
spTitle.Size = UDim2.new(1, 0, 0, 25)
spTitle.Position = UDim2.new(0, 0, 0, 10)
spTitle.BackgroundTransparency = 1
spTitle.Text = "📍 Set Spawnpoint"
spTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
spTitle.Font = Enum.Font.GothamBold
spTitle.TextSize = 16
spTitle.TextXAlignment = Enum.TextXAlignment.Center
spTitle.Parent = spPage

local spDesc = Instance.new("TextLabel")
spDesc.Size = UDim2.new(1, -20, 0, 40)
spDesc.Position = UDim2.new(0, 10, 0, 45)
spDesc.BackgroundTransparency = 1
spDesc.Text = "Define seu ponto de respawn no local atual"
spDesc.TextColor3 = Color3.fromRGB(150, 150, 150)
spDesc.Font = Enum.Font.GothamMedium
spDesc.TextSize = 12
spDesc.TextWrapped = true
spDesc.TextXAlignment = Enum.TextXAlignment.Center
spDesc.Parent = spPage

local spStatus = Instance.new("TextLabel")
spStatus.Size = UDim2.new(1, -20, 0, 25)
spStatus.Position = UDim2.new(0, 10, 0, 100)
spStatus.BackgroundTransparency = 1
spStatus.Text = "Spawnpoint: Não definido"
spStatus.TextColor3 = Color3.fromRGB(200, 200, 200)
spStatus.Font = Enum.Font.GothamMedium
spStatus.TextSize = 13
spStatus.TextXAlignment = Enum.TextXAlignment.Center
spStatus.Parent = spPage

local setSpawnBtn = Instance.new("TextButton")
setSpawnBtn.Size = UDim2.new(0.5, 0, 0, 45)
setSpawnBtn.Position = UDim2.new(0.25, 0, 0, 140)
setSpawnBtn.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
setSpawnBtn.Text = "🎯 DEFINIR SPAWN"
setSpawnBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
setSpawnBtn.Font = Enum.Font.GothamBold
setSpawnBtn.TextSize = 14
setSpawnBtn.BorderSizePixel = 0
setSpawnBtn.Parent = spPage
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
clearSpawnBtn.Parent = spPage
Instance.new("UICorner", clearSpawnBtn).CornerRadius = UDim.new(0, 8)

-- Botões navbar
local fpBtn = createNavBtn("⚡ Fast Punch", 15)
local tpBtn = createNavBtn("🌍 Teleport", 55)
local spBtn = createNavBtn("📍 Spawnpoint", 95)

-- Arrastar
local dragging = false
local dragStart = nil
local startPos = nil

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

-- Navegação
local function switchPage(name)
    fpPage.Visible = (name == "fp")
    tpPage.Visible = (name == "tp")
    spPage.Visible = (name == "sp")
    fpBtn.BackgroundColor3 = Color3.fromRGB(name == "fp" and 60 or 40, 40, 40)
    tpBtn.BackgroundColor3 = Color3.fromRGB(name == "tp" and 60 or 40, 40, 40)
    spBtn.BackgroundColor3 = Color3.fromRGB(name == "sp" and 60 or 40, 40, 40)
end

fpBtn.MouseButton1Click:Connect(function() switchPage("fp") end)
tpBtn.MouseButton1Click:Connect(function() switchPage("tp") end)
spBtn.MouseButton1Click:Connect(function() switchPage("sp") end)

-- Minimizar
local minimized = false
local originalSize = mainFrame.Size
minimizeBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    if minimized then
        TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Size = UDim2.new(0, 520, 0, 45)}):Play()
        minimizeBtn.Text = "□"
    else
        TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Size = originalSize}):Play()
        minimizeBtn.Text = "—"
    end
end)

-- Fechar
closeBtn.MouseButton1Click:Connect(function()
    CONFIG.FAST_PUNCH_ENABLED = false
    CONFIG.LOOP_TP_ENABLED = false
    screenGui:Destroy()
end)

-- ==================== TELEPORTE ====================
local selectedPlayer = nil

local function teleportToPlayer(player)
    if not player then return end
    local targetChar = player.Character
    if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then
        notify("❌ Jogador sem personagem!")
        return
    end
    local myChar = LocalPlayer.Character
    if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then
        notify("❌ Você está sem personagem!")
        return
    end
    myChar.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, 5)
    notify("✅ Teleportado para " .. player.Name)
end

local function updatePlayerList()
    pcall(function()
        for _, child in pairs(playerList:GetChildren()) do
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
                btn.Font = Enum.Font.GothamBold
                btn.TextSize = 14
                btn.BorderSizePixel = 0
                btn.AutoButtonColor = false
                btn.Parent = playerList
                Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
                
                btn.MouseButton1Click:Connect(function()
                    selectedPlayer = player
                    loopTargetLabel.Text = "Alvo: " .. player.Name
                    for _, c in pairs(playerList:GetChildren()) do
                        if c:IsA("TextButton") then c.BackgroundColor3 = Color3.fromRGB(50, 50, 50) end
                    end
                    btn.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
                    teleportToPlayer(player)
                end)
                
                ySize = ySize + 38
            end
        end
        playerList.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
    end)
end

-- Loop TP
loopToggle.MouseButton1Click:Connect(function()
    CONFIG.LOOP_TP_ENABLED = not CONFIG.LOOP_TP_ENABLED
    if CONFIG.LOOP_TP_ENABLED then
        if not selectedPlayer then
            notify("❌ Selecione um jogador primeiro!")
            CONFIG.LOOP_TP_ENABLED = false
            return
        end
        loopToggle.Text = "PARAR LOOP"
        loopToggle.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
        CONFIG.LOOP_TP_TARGET = selectedPlayer
        notify("🔄 Loop TP iniciado!")
        task.spawn(function()
            while CONFIG.LOOP_TP_ENABLED and CONFIG.LOOP_TP_TARGET do
                teleportToPlayer(CONFIG.LOOP_TP_TARGET)
                task.wait(CONFIG.LOOP_TP_INTERVAL)
            end
        end)
    else
        loopToggle.Text = "INICIAR LOOP"
        loopToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
        CONFIG.LOOP_TP_TARGET = nil
        notify("🔄 Loop TP DESATIVADO")
    end
end)

tpPage:GetPropertyChangedSignal("Visible"):Connect(function()
    if tpPage.Visible then updatePlayerList() end
end)

Players.PlayerAdded:Connect(function() task.wait(0.2) updatePlayerList() end)
Players.PlayerRemoving:Connect(function(p)
    task.wait(0.2) updatePlayerList()
    if selectedPlayer == p then
        selectedPlayer = nil
        loopTargetLabel.Text = "Alvo: Nenhum"
        if CONFIG.LOOP_TP_ENABLED then
            CONFIG.LOOP_TP_ENABLED = false
            loopToggle.Text = "INICIAR LOOP"
            loopToggle.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
        end
    end
end)

updatePlayerList()

-- ==================== FAST PUNCH (NOVO SISTEMA) ====================
local PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Events") and ReplicatedStorage.Events:FindFirstChild("Punch")
if not PUNCH_EVENT then 
    PUNCH_EVENT = ReplicatedStorage:FindFirstChild("Punch") 
end

if PUNCH_EVENT then
    notify("✅ Fast Punch pronto!")
else
    notify("⚠️ Evento de soco não encontrado!")
end

local function darSoco()
    if PUNCH_EVENT then
        pcall(function()
            PUNCH_EVENT:FireServer()
        end)
    else
        local char = LocalPlayer.Character
        if char then
            local tool = char:FindFirstChildOfClass("Tool")
            if tool then
                pcall(function()
                    tool:Activate()
                end)
            end
        end
    end
end

-- Verifica stamina
local function temEnergia()
    local char = LocalPlayer.Character
    if not char then return false end
    
    -- Procura por stamina do Age of Heroes
    local stamina = char:GetAttribute("Stamina")
    if stamina then
        local tem = stamina > 10
        if not tem then
            staminaLabel.Text = "Energia: ESGOTADA!"
            staminaLabel.TextColor3 = Color3.fromRGB(231, 76, 60)
        else
            staminaLabel.Text = "Energia: " .. math.floor(stamina)
            staminaLabel.TextColor3 = Color3.fromRGB(46, 204, 113)
        end
        return tem
    end
    
    -- Fallback: sempre retorna true se não achar stamina
    staminaLabel.Text = "Energia: --"
    staminaLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    return true
end

-- Loop do Fast Punch quando ativado
local fastPunchAtivo = false
task.spawn(function()
    while true do
        if CONFIG.FAST_PUNCH_ENABLED and fastPunchAtivo then
            if temEnergia() then
                darSoco()
                task.wait(CONFIG.FAST_PUNCH_SPEED)
            else
                -- Sem energia, para de bater
                fastPunchAtivo = false
                notify("⚠️ Energia esgotada! Solte o clique para recarregar.")
                task.wait(0.5)
            end
        else
            task.wait(0.1)
        end
    