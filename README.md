--[[
    Age of Heroes Premium UI - Versão Xeno Executor
    Compatível com Xeno, Synapse X, Script-Ware, KRNL
]]

-- Proteção contra execução múltipla
if getgenv().AgeOfHeroesLoaded then
    return
end
getgenv().AgeOfHeroesLoaded = true

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Variáveis
local FastPunchEnabled = false
local FastPunchConnection = nil

-- Função de notificação simplificada
local function notify(title, text, duration)
    duration = duration or 3
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = title,
        Text = text,
        Duration = duration,
        Button1 = "OK"
    })
end

-- Criar UI
local function createUI()
    -- ScreenGui
    local gui = Instance.new("ScreenGui")
    gui.Name = "AgeOfHeroes"
    gui.Parent = game:GetService("CoreGui")
    gui.ResetOnSpawn = false
    gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    -- Frame Principal
    local main = Instance.new("Frame")
    main.Name = "Main"
    main.Size = UDim2.new(0, 450, 0, 350)
    main.Position = UDim2.new(0.5, -225, 0.5, -175)
    main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    main.BorderSizePixel = 0
    main.Active = true
    main.Draggable = false
    main.Parent = gui
    
    -- Cantos arredondados
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 12)
    corner.Parent = main
    
    -- Barra de título
    local titleBar = Instance.new("Frame")
    titleBar.Size = UDim2.new(1, 0, 0, 40)
    titleBar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    titleBar.BorderSizePixel = 0
    titleBar.Parent = main
    
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 12)
    titleCorner.Parent = titleBar
    
    local titleFix = Instance.new("Frame")
    titleFix.Size = UDim2.new(1, 0, 0.5, 0)
    titleFix.Position = UDim2.new(0, 0, 0.5, 0)
    titleFix.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    titleFix.BorderSizePixel = 0
    titleFix.Parent = titleBar
    
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(0.7, 0, 1, 0)
    title.Position = UDim2.new(0, 15, 0, 0)
    title.BackgroundTransparency = 1
    title.Text = "⚔️ Age of Heroes Premium"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.Font = Enum.Font.GothamBold
    title.TextSize = 14
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.Parent = titleBar
    
    -- Botão Minimizar
    local minimizeBtn = Instance.new("TextButton")
    minimizeBtn.Size = UDim2.new(0, 28, 0, 28)
    minimizeBtn.Position = UDim2.new(1, -65, 0, 6)
    minimizeBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    minimizeBtn.Text = "─"
    minimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    minimizeBtn.Font = Enum.Font.GothamBold
    minimizeBtn.TextSize = 16
    minimizeBtn.BorderSizePixel = 0
    minimizeBtn.Parent = titleBar
    
    local minCorner = Instance.new("UICorner")
    minCorner.CornerRadius = UDim.new(0, 6)
    minCorner.Parent = minimizeBtn
    
    -- Botão Fechar
    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 28, 0, 28)
    closeBtn.Position = UDim2.new(1, -32, 0, 6)
    closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
    closeBtn.Text = "✕"
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.Font = Enum.Font.GothamBold
    closeBtn.TextSize = 14
    closeBtn.BorderSizePixel = 0
    closeBtn.Parent = titleBar
    
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 6)
    closeCorner.Parent = closeBtn
    
    -- Sistema de Arrastar Manual
    local dragging = false
    local dragStartPos = nil
    local frameStartPos = nil
    
    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or 
           input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStartPos = input.Position
            frameStartPos = main.Position
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging then
            local delta = input.Position - dragStartPos
            main.Position = UDim2.new(
                frameStartPos.X.Scale, 
                frameStartPos.X.Offset + delta.X,
                frameStartPos.Y.Scale, 
                frameStartPos.Y.Offset + delta.Y
            )
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or 
           input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    
    -- Container Principal
    local container = Instance.new("Frame")
    container.Size = UDim2.new(1, 0, 1, -40)
    container.Position = UDim2.new(0, 0, 0, 40)
    container.BackgroundTransparency = 1
    container.Parent = main
    
    -- Barra Lateral
    local sidebar = Instance.new("Frame")
    sidebar.Size = UDim2.new(0, 140, 1, 0)
    sidebar.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    sidebar.BorderSizePixel = 0
    sidebar.Parent = container
    
    local sidebarCorner = Instance.new("UICorner")
    sidebarCorner.CornerRadius = UDim.new(0, 12)
    sidebarCorner.Parent = sidebar
    
    local sidebarFix = Instance.new("Frame")
    sidebarFix.Size = UDim2.new(0.5, 0, 1, 0)
    sidebarFix.Position = UDim2.new(0.5, 0, 0, 0)
    sidebarFix.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    sidebarFix.BorderSizePixel = 0
    sidebarFix.Parent = sidebar
    
    -- Área de Conteúdo
    local content = Instance.new("Frame")
    content.Size = UDim2.new(1, -145, 1, -10)
    content.Position = UDim2.new(0, 145, 0, 5)
    content.BackgroundTransparency = 1
    content.Parent = container
    
    -- Páginas
    local pages = {}
    
    -- Função para criar páginas
    local function createPage(name)
        local page = Instance.new("Frame")
        page.Name = name
        page.Size = UDim2.new(1, 0, 1, 0)
        page.BackgroundTransparency = 1
        page.Visible = false
        page.Parent = content
        return page
    end
    
    -- Página Fast Punch
    local fastPunchPage = createPage("FastPunch")
    
    local fpTitle = Instance.new("TextLabel")
    fpTitle.Size = UDim2.new(1, 0, 0, 25)
    fpTitle.Position = UDim2.new(0, 0, 0, 5)
    fpTitle.BackgroundTransparency = 1
    fpTitle.Text = "⚡ Sistema Fast Punch"
    fpTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    fpTitle.Font = Enum.Font.GothamBold
    fpTitle.TextSize = 16
    fpTitle.TextXAlignment = Enum.TextXAlignment.Center
    fpTitle.Parent = fastPunchPage
    
    local fpDesc = Instance.new("TextLabel")
    fpDesc.Size = UDim2.new(1, -20, 0, 35)
    fpDesc.Position = UDim2.new(0, 10, 0, 35)
    fpDesc.BackgroundTransparency = 1
    fpDesc.Text = "Ataque extremamente rápido! Remove o delay entre socos."
    fpDesc.TextColor3 = Color3.fromRGB(180, 180, 180)
    fpDesc.Font = Enum.Font.Gotham
    fpDesc.TextSize = 11
    fpDesc.TextWrapped = true
    fpDesc.TextXAlignment = Enum.TextXAlignment.Center
    fpDesc.Parent = fastPunchPage
    
    local fpToggle = Instance.new("TextButton")
    fpToggle.Size = UDim2.new(0.5, 0, 0, 45)
    fpToggle.Position = UDim2.new(0.25, 0, 0, 90)
    fpToggle.BackgroundColor3 = Color3.fromRGB(45, 160, 45)
    fpToggle.Text = "CLIQUE PARA ATIVAR"
    fpToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
    fpToggle.Font = Enum.Font.GothamBold
    fpToggle.TextSize = 13
    fpToggle.BorderSizePixel = 0
    fpToggle.Parent = fastPunchPage
    
    local fpCorner = Instance.new("UICorner")
    fpCorner.CornerRadius = UDim.new(0, 10)
    fpCorner.Parent = fpToggle
    
    local statusLabel = Instance.new("TextLabel")
    statusLabel.Size = UDim2.new(1, -20, 0, 25)
    statusLabel.Position = UDim2.new(0, 10, 0, 150)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Text = "Status: DESATIVADO"
    statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    statusLabel.Font = Enum.Font.Gotham
    statusLabel.TextSize = 13
    statusLabel.TextXAlignment = Enum.TextXAlignment.Center
    statusLabel.Parent = fastPunchPage
    
    -- Instruções
    local instructionLabel = Instance.new("TextLabel")
    instructionLabel.Size = UDim2.new(1, -20, 0, 60)
    instructionLabel.Position = UDim2.new(0, 10, 0, 185)
    instructionLabel.BackgroundTransparency = 1
    instructionLabel.Text = "📌 Como usar:\n• Ative o Fast Punch\n• Segure o botão de ataque\n• Solte quando quiser parar"
    instructionLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
    instructionLabel.Font = Enum.Font.Gotham
    instructionLabel.TextSize = 11
    instructionLabel.TextWrapped = true
    instructionLabel.TextXAlignment = Enum.TextXAlignment.Left
    instructionLabel.Parent = fastPunchPage
    
    -- Página Teleport
    local teleportPage = createPage("Teleport")
    
    local tpTitle = Instance.new("TextLabel")
    tpTitle.Size = UDim2.new(1, 0, 0, 25)
    tpTitle.Position = UDim2.new(0, 0, 0, 5)
    tpTitle.BackgroundTransparency = 1
    tpTitle.Text = "🌍 Teleporte para Jogadores"
    tpTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
    tpTitle.Font = Enum.Font.GothamBold
    tpTitle.TextSize = 16
    tpTitle.TextXAlignment = Enum.TextXAlignment.Center
    tpTitle.Parent = teleportPage
    
    local tpDesc = Instance.new("TextLabel")
    tpDesc.Size = UDim2.new(1, -20, 0, 25)
    tpDesc.Position = UDim2.new(0, 10, 0, 35)
    tpDesc.BackgroundTransparency = 1
    tpDesc.Text = "Clique no nome de um jogador para teleportar atrás dele"
    tpDesc.TextColor3 = Color3.fromRGB(180, 180, 180)
    tpDesc.Font = Enum.Font.Gotham
    tpDesc.TextSize = 11
    tpDesc.TextWrapped = true
    tpDesc.TextXAlignment = Enum.TextXAlignment.Center
    tpDesc.Parent = teleportPage
    
    local playerList = Instance.new("ScrollingFrame")
    playerList.Size = UDim2.new(1, -20, 1, -75)
    playerList.Position = UDim2.new(0, 10, 0, 65)
    playerList.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    playerList.BorderSizePixel = 0
    playerList.ScrollBarThickness = 4
    playerList.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
    playerList.CanvasSize = UDim2.new(0, 0, 0, 0)
    playerList.Parent = teleportPage
    
    local plCorner = Instance.new("UICorner")
    plCorner.CornerRadius = UDim.new(0, 8)
    plCorner.Parent = playerList
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 3)
    listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    listLayout.SortOrder = Enum.SortOrder.Name
    listLayout.Parent = playerList
    
    -- Botões da Sidebar
    local function createSidebarButton(text, posY)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -20, 0, 38)
        btn.Position = UDim2.new(0, 10, 0, posY)
        btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
        btn.Text = text
        btn.TextColor3 = Color3.fromRGB(220, 220, 220)
        btn.Font = Enum.Font.GothamMedium
        btn.TextSize = 13
        btn.BorderSizePixel = 0
        btn.Parent = sidebar
        
        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 8)
        btnCorner.Parent = btn
        
        return btn
    end
    
    local fpBtn = createSidebarButton("⚡ Fast Punch", 15)
    local tpBtn = createSidebarButton("🌍 Teleport", 60)
    
    -- Navegação
    local currentPage = nil
    
    local function switchToPage(pageName)
        -- Esconder todas as páginas
        fastPunchPage.Visible = false
        teleportPage.Visible = false
        
        -- Mostrar página selecionada
        if pageName == "FastPunch" then
            fastPunchPage.Visible = true
            fpBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            tpBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
        elseif pageName == "Teleport" then
            teleportPage.Visible = true
            fpBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
            tpBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        end
        
        currentPage = pageName
    end
    
    fpBtn.MouseButton1Click:Connect(function()
        switchToPage("FastPunch")
    end)
    
    tpBtn.MouseButton1Click:Connect(function()
        switchToPage("Teleport")
    end)
    
    -- Sistema Fast Punch
    local function getTool()
        local char = LocalPlayer.Character
        if not char then return nil end
        return char:FindFirstChildOfClass("Tool")
    end
    
    local function activateTool()
        local tool = getTool()
        if tool then
            -- Tenta ativar de várias formas
            pcall(function()
                tool:Activate()
            end)
            pcall(function()
                if tool.Parent and tool.Parent:FindFirstChild("Humanoid") then
                    tool.Parent.Humanoid:EquipTool(tool)
                end
            end)
        end
    end
    
    fpToggle.MouseButton1Click:Connect(function()
        FastPunchEnabled = not FastPunchEnabled
        
        if FastPunchEnabled then
            fpToggle.Text = "DESATIVAR FAST PUNCH"
            fpToggle.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
            statusLabel.Text = "Status: ATIVADO ⚡"
            statusLabel.TextColor3 = Color3.fromRGB(45, 220, 45)
            notify("Fast Punch", "Sistema ativado! Segure o clique para atacar rapidamente", 2)
            
            -- Inicia o loop de fast punch
            task.spawn(function()
                while FastPunchEnabled do
                    if Mouse.Button1Down or UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                        activateTool()
                        task.wait(0.01) -- Velocidade extremamente rápida
                    else
                        task.wait(0.05)
                    end
                end
            end)
        else
            fpToggle.Text = "CLIQUE PARA ATIVAR"
            fpToggle.BackgroundColor3 = Color3.fromRGB(45, 160, 45)
            statusLabel.Text = "Status: DESATIVADO"
            statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
            notify("Fast Punch", "Sistema desativado", 2)
        end
    end)
    
    -- Sistema de Teleporte
    local function updatePlayerList()
        -- Limpar lista
        for _, child in pairs(playerList:GetChildren()) do
            if child:IsA("TextButton") then
                child:Destroy()
            end
        end
        
        -- Adicionar jogadores
        local players = Players:GetPlayers()
        local ySize = 0
        
        for _, player in pairs(players) do
            if player ~= LocalPlayer then
                local btn = Instance.new("TextButton")
                btn.Size = UDim2.new(1, -10, 0, 35)
                btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
                btn.Text = "👤 " .. player.Name
                btn.TextColor3 = Color3.fromRGB(255, 255, 255)
                btn.Font = Enum.Font.GothamMedium
                btn.TextSize = 13
                btn.BorderSizePixel = 0
                btn.Parent = playerList
                
                local btnCorner = Instance.new("UICorner")
                btnCorner.CornerRadius = UDim.new(0, 6)
                btnCorner.Parent = btn
                
                -- Efeito hover
                btn.MouseEnter:Connect(function()
                    btn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
                end)
                
                btn.MouseLeave:Connect(function()
                    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
                end)
                
                -- Teleporte
                btn.MouseButton1Click:Connect(function()
                    local targetChar = player.Character
                    local localChar = LocalPlayer.Character
                    
                    if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then
                        notify("Erro", "Jogador não está no mapa", 2)
                        return
                    end
                    
                    if not localChar or not localChar:FindFirstChild("HumanoidRootPart") then
                        notify("Erro", "Seu personagem não está carregado", 2)
                        return
                    end
                    
                    local targetPos = targetChar.HumanoidRootPart.CFrame
                    local behindPos = targetPos * CFrame.new(0, 0, 5)
                    
                    localChar.HumanoidRootPart.CFrame = behindPos
                    notify("Teleporte", "Teleportado para " .. player.Name, 2)
                end)
                
                ySize = ySize + 38
            end
        end
        
        playerList.CanvasSize = UDim2.new(0, 0, 0, ySize + 10)
    end
    
    -- Atualizar lista de jogadores
    updatePlayerList()
    
    Players.PlayerAdded:Connect(function()
        task.wait(0.2)
        updatePlayerList()
    end)
    
    Players.PlayerRemoving:Connect(function()
        task.wait(0.2)
        updatePlayerList()
    end)
    
    -- Atualização periódica
    task.spawn(function()
        while true do
            task.wait(3)
            pcall(updatePlayerList)
        end
    end)
    
    -- Minimizar
    local minimized = false
    local originalSize = main.Size
    
    minimizeBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            main.Size = UDim2.new(0, 450, 0, 40)
            minimizeBtn.Text = "□"
        else
            main.Size = originalSize
            minimizeBtn.Text = "─"
        end
    end)
    
    -- Fechar
    closeBtn.MouseButton1Click:Connect(function()
        FastPunchEnabled = false
        gui:Destroy()
    end)
    
    -- Mostrar primeira página
    switchToPage("FastPunch")
    
    return gui
end

-- Iniciar
pcall(function()
    createUI()
    notify("Age of Heroes", "Script carregado com sucesso!", 3)
end)# age-mod-xeno-v1