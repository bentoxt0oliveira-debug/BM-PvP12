loadstring([[
--[[
    PvP BM - AIMBOT + ESP + TELEPORTE + AUTO CLICK
    Blox Fruits - Script completo
]]

-- Variáveis Globais
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local VirtualInput = game:GetService("VirtualInputManager")
local Camera = workspace.CurrentCamera

-- Configurações
local AimbotEnabled = false
local ESPEnabled = false
local AutoClickEnabled = false
local SelectedPlayer = nil
local ESPObjects = {}
local Teleporting = false
local AutoClickConnection = nil

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PvPBM"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 220, 0, 430)
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -215)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
MainFrame.BackgroundTransparency = 0.08
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

-- Tornar o Frame arrastável
local Dragging = false
local DragStart = nil
local StartPos = nil

MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = true
        DragStart = input.Position
        StartPos = MainFrame.Position
    end
end)

MainFrame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if Dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local Delta = input.Position - DragStart
        MainFrame.Position = UDim2.new(StartPos.X.Scale, StartPos.X.Offset + Delta.X, StartPos.Y.Scale, StartPos.Y.Offset + Delta.Y)
    end
end)

-- Barra de Título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 32)
TitleBar.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = true
        DragStart = input.Position
        StartPos = MainFrame.Position
    end
end)

TitleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = false
    end
end)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -70, 1, 0)
Title.Position = UDim2.new(0, 8, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "⚔️ PvP BM ⚔️"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TitleBar

-- Botão Minimizar
local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Size = UDim2.new(0, 30, 1, 0)
MinimizeBtn.Position = UDim2.new(1, -60, 0, 0)
MinimizeBtn.BackgroundTransparency = 1
MinimizeBtn.Text = "−"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.TextSize = 22
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.Parent = TitleBar

-- Botão Fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 30, 1, 0)
CloseBtn.Position = UDim2.new(1, -30, 0, 0)
CloseBtn.BackgroundTransparency = 1
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 100, 100)
CloseBtn.TextSize = 16
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = TitleBar

-- Container do conteúdo
local ContentContainer = Instance.new("Frame")
ContentContainer.Size = UDim2.new(1, 0, 1, -32)
ContentContainer.Position = UDim2.new(0, 0, 0, 32)
ContentContainer.BackgroundTransparency = 1
ContentContainer.Parent = MainFrame

local ScrollFrame = Instance.new("ScrollingFrame")
ScrollFrame.Size = UDim2.new(1, 0, 1, 0)
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.BorderSizePixel = 0
ScrollFrame.ScrollBarThickness = 4
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 500)
ScrollFrame.Parent = ContentContainer

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Padding = UDim.new(0, 5)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Parent = ScrollFrame

-- ========== INTERFACE ==========

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(0.95, 0, 0, 30)
StatusLabel.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
StatusLabel.Text = "Status: OK"
StatusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.TextSize = 10
StatusLabel.Parent = ScrollFrame

local TargetLabel = Instance.new("TextLabel")
TargetLabel.Size = UDim2.new(0.95, 0, 0, 30)
TargetLabel.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
TargetLabel.Text = "🎯 Alvo: Nenhum"
TargetLabel.TextColor3 = Color3.fromRGB(255, 200, 100)
TargetLabel.Font = Enum.Font.Gotham
TargetLabel.TextSize = 10
TargetLabel.Parent = ScrollFrame

-- Botão Aimbot
local AimbotBtn = Instance.new("TextButton")
AimbotBtn.Size = UDim2.new(0.95, 0, 0, 32)
AimbotBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
AimbotBtn.BackgroundTransparency = 0.3
AimbotBtn.BorderSizePixel = 0
AimbotBtn.Text = "🎯 ATIVAR AIMBOT"
AimbotBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AimbotBtn.Font = Enum.Font.GothamSemibold
AimbotBtn.TextSize = 12
AimbotBtn.Parent = ScrollFrame

-- Botão ESP
local ESPBtn = Instance.new("TextButton")
ESPBtn.Size = UDim2.new(0.95, 0, 0, 32)
ESPBtn.BackgroundColor3 = Color3.fromRGB(50, 100, 200)
ESPBtn.BackgroundTransparency = 0.3
ESPBtn.BorderSizePixel = 0
ESPBtn.Text = "👁️ ATIVAR ESP"
ESPBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ESPBtn.Font = Enum.Font.GothamSemibold
ESPBtn.TextSize = 12
ESPBtn.Parent = ScrollFrame

-- Botão AUTO CLICK
local AutoClickBtn = Instance.new("TextButton")
AutoClickBtn.Size = UDim2.new(0.95, 0, 0, 32)
AutoClickBtn.BackgroundColor3 = Color3.fromRGB(200, 100, 50)
AutoClickBtn.BackgroundTransparency = 0.3
AutoClickBtn.BorderSizePixel = 0
AutoClickBtn.Text = "🖱️ ATIVAR AUTO CLICK"
AutoClickBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoClickBtn.Font = Enum.Font.GothamSemibold
AutoClickBtn.TextSize = 12
AutoClickBtn.Parent = ScrollFrame

-- Botão Teleporte
local TeleportBtn = Instance.new("TextButton")
TeleportBtn.Size = UDim2.new(0.95, 0, 0, 32)
TeleportBtn.BackgroundColor3 = Color3.fromRGB(100, 50, 150)
TeleportBtn.BackgroundTransparency = 0.3
TeleportBtn.BorderSizePixel = 0
TeleportBtn.Text = "🚀 TELEPORTAR"
TeleportBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
TeleportBtn.Font = Enum.Font.GothamSemibold
TeleportBtn.TextSize = 11
TeleportBtn.Parent = ScrollFrame

-- Separador
local Separator = Instance.new("Frame")
Separator.Size = UDim2.new(0.95, 0, 0, 1)
Separator.BackgroundColor3 = Color3.fromRGB(100, 100, 150)
Separator.BorderSizePixel = 0
Separator.Parent = ScrollFrame

-- Configurações do Auto Click
local ClickDelayLabel = Instance.new("TextLabel")
ClickDelayLabel.Size = UDim2.new(0.95, 0, 0, 20)
ClickDelayLabel.BackgroundTransparency = 1
ClickDelayLabel.Text = "⚙️ Delay entre cliques (ms):"
ClickDelayLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
ClickDelayLabel.Font = Enum.Font.Gotham
ClickDelayLabel.TextSize = 10
ClickDelayLabel.TextXAlignment = Enum.TextXAlignment.Left
ClickDelayLabel.Parent = ScrollFrame

local ClickDelaySlider = Instance.new("TextBox")
ClickDelaySlider.Size = UDim2.new(0.95, 0, 0, 25)
ClickDelaySlider.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
ClickDelaySlider.Text = "100"
ClickDelaySlider.TextColor3 = Color3.fromRGB(255, 255, 255)
ClickDelaySlider.Font = Enum.Font.Gotham
ClickDelaySlider.TextSize = 11
ClickDelaySlider.PlaceholderText = "100"
ClickDelaySlider.Parent = ScrollFrame

-- Lista de Jogadores
local PlayerListTitle = Instance.new("TextLabel")
PlayerListTitle.Size = UDim2.new(0.95, 0, 0, 20)
PlayerListTitle.BackgroundTransparency = 1
PlayerListTitle.Text = "📋 JOGADORES:"
PlayerListTitle.TextColor3 = Color3.fromRGB(200, 200, 200)
PlayerListTitle.Font = Enum.Font.GothamBold
PlayerListTitle.TextSize = 11
PlayerListTitle.TextXAlignment = Enum.TextXAlignment.Left
PlayerListTitle.Parent = ScrollFrame

local PlayerListScroll = Instance.new("ScrollingFrame")
PlayerListScroll.Size = UDim2.new(0.95, 0, 0, 130)
PlayerListScroll.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
PlayerListScroll.BorderSizePixel = 0
PlayerListScroll.ScrollBarThickness = 3
PlayerListScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
PlayerListScroll.Parent = ScrollFrame

local PlayerListLayout = Instance.new("UIListLayout")
PlayerListLayout.Padding = UDim.new(0, 3)
PlayerListLayout.SortOrder = Enum.SortOrder.LayoutOrder
PlayerListLayout.Parent = PlayerListScroll

-- ========== FUNÇÃO - PEGAR CENTRO DO PEITO ==========
local function GetCenterPosition(player)
    local character = player.Character
    if not character then return nil end
    
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if hrp then
        return hrp.Position + Vector3.new(0, 2.5, 0)
    end
    
    local upperTorso = character:FindFirstChild("UpperTorso")
    if upperTorso then
        return upperTorso.Position
    end
    
    local torso = character:FindFirstChild("Torso")
    if torso then
        return torso.Position
    end
    
    local head = character:FindFirstChild("Head")
    if head then
        return head.Position - Vector3.new(0, 1.5, 0)
    end
    
    return nil
end

-- Função para obter a posição do jogador para teleporte
local function GetPlayerPosition(player)
    local character = player.Character
    if not character then return nil end
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
    return hrp.Position
end

-- ========== FUNÇÃO AUTO CLICK ==========
local function StartAutoClick()
    if AutoClickConnection then
        AutoClickConnection:Disconnect()
        AutoClickConnection = nil
    end
    
    if not AutoClickEnabled then return end
    
    local delay = tonumber(ClickDelaySlider.Text) or 100
    delay = math.max(50, math.min(500, delay))
    
    StatusLabel.Text = string.format("🖱️ Auto Click ON (%.0f ms)", delay)
    
    AutoClickConnection = RunService.RenderStepped:Connect(function()
        if not AutoClickEnabled then return end
        if not SelectedPlayer then return end
        
        local targetChar = SelectedPlayer.Character
        if not targetChar then return end
        
        local targetHumanoid = targetChar:FindFirstChild("Humanoid")
        if not targetHumanoid or targetHumanoid.Health <= 0 then return end
        
        local character = LocalPlayer.Character
        if not character then return end
        
        -- Verificar distância
        local hrp = character:FindFirstChild("HumanoidRootPart")
        local targetHrp = targetChar:FindFirstChild("HumanoidRootPart")
        if hrp and targetHrp then
            local distance = (hrp.Position - targetHrp.Position).Magnitude
            if distance > 30 then
                StatusLabel.Text = "⚠️ Alvo muito longe para atacar"
                return
            end
        end
        
        -- Simular clique do mouse (ataque)
        pcall(function()
            -- Método 1: Simular clique esquerdo do mouse
            VirtualInput:SendMouseButtonEvent(0, 0, 0, true, game, 0)
            task.wait(0.01)
            VirtualInput:SendMouseButtonEvent(0, 0, 0, false, game, 0)
            
            -- Método 2: Ativar ferramenta equipada
            local tool = character:FindFirstChildWhichIsA("Tool")
            if tool then
                tool:Activate()
            end
            
            -- Método 3: Ativar arma no inventário
            local backpackTool = LocalPlayer.Backpack:FindFirstChildWhichIsA("Tool")
            if backpackTool and not tool then
                backpackTool.Parent = character
                task.wait(0.05)
                backpackTool:Activate()
            end
        end)
        
        task.wait(delay / 1000)
    end)
end

local function StopAutoClick()
    if AutoClickConnection then
        AutoClickConnection:Disconnect()
        AutoClickConnection = nil
    end
end

-- ========== FUNÇÃO DE TELEPORTE ==========
local function TeleportToPlayer(targetPlayer)
    if Teleporting then
        StatusLabel.Text = "⏳ Já está teleportando..."
        return false
    end
    
    if not targetPlayer then
        StatusLabel.Text = "❌ Nenhum alvo selecionado!"
        return false
    end
    
    local targetPos = GetPlayerPosition(targetPlayer)
    if not targetPos then
        StatusLabel.Text = "❌ Alvo não tem personagem no jogo!"
        return false
    end
    
    local character = LocalPlayer.Character
    if not character then
        StatusLabel.Text = "❌ Seu personagem não está no jogo!"
        return false
    end
    
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then
        StatusLabel.Text = "❌ Erro ao encontrar seu personagem!"
        return false
    end
    
    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid then
        StatusLabel.Text = "❌ Humanoid não encontrado!"
        return false
    end
    
    Teleporting = true
    
    local originalWalkSpeed = humanoid.WalkSpeed
    local originalJumpPower = humanoid.JumpPower
    local originalPlatformStand = humanoid.PlatformStand
    
    humanoid.PlatformStand = true
    humanoid.WalkSpeed = 30
    humanoid.JumpPower = 0
    
    local startPos = hrp.Position
    local distance = (startPos - targetPos).Magnitude
    local duration = math.min(math.max(distance / 25, 3), 10)
    
    StatusLabel.Text = string.format("🚀 Teleportando... (%.1f segundos)", duration)
    
    local tweenInfo = TweenInfo.new(duration, Enum.EasingStyle.Linear, Enum.EasingDirection.Out)
    local tween = TweenService:Create(hrp, tweenInfo, {CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))})
    
    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(100000, 100000, 100000)
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.Parent = hrp
    
    local trailAttachment = Instance.new("Attachment")
    trailAttachment.Parent = hrp
    
    local trail = Instance.new("Trail")
    trail.Parent = trailAttachment
    trail.Color = ColorSequence.new(Color3.fromRGB(255, 50, 50))
    trail.Lifetime = 0.8
    trail.WidthScale = NumberSequence.new(1)
    
    local particleAttachment = Instance.new("Attachment")
    particleAttachment.Parent = hrp
    
    local particles = Instance.new("ParticleEmitter")
    particles.Parent = particleAttachment
    particles.Texture = "rbxasset://textures/particles/sparkles_main.dds"
    particles.Rate = 150
    particles.Lifetime = NumberRange.new(0.5)
    particles.SpreadAngle = Vector2.new(360, 360)
    
    tween:Play()
    
    local connection
    connection = RunService.RenderStepped:Connect(function()
        if not hrp or not hrp.Parent then
            if connection then connection:Disconnect() end
            return
        end
        local direction = (targetPos - hrp.Position).Unit
        bodyVelocity.Velocity = direction * 25
    end)
    
    tween.Completed:Wait()
    
    if connection then connection:Disconnect() end
    bodyVelocity:Destroy()
    trail:Destroy()
    trailAttachment:Destroy()
    particles:Destroy()
    particleAttachment:Destroy()
    
    task.wait(0.3)
    pcall(function()
        hrp.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
    end)
    
    humanoid.PlatformStand = originalPlatformStand
    humanoid.WalkSpeed = originalWalkSpeed
    humanoid.JumpPower = originalJumpPower
    
    Teleporting = false
    StatusLabel.Text = "✅ Chegou em " .. targetPlayer.Name
    
    pcall(function()
        local arrivalParts = Instance.new("ParticleEmitter")
        arrivalParts.Parent = hrp
        arrivalParts.Texture = "rbxasset://textures/particles/sparkles_main.dds"
        arrivalParts.Rate = 200
        arrivalParts.Lifetime = NumberRange.new(0.5)
        arrivalParts.SpreadAngle = Vector2.new(360, 360)
        task.delay(0.5, function() arrivalParts:Destroy() end)
    end)
    
    return true
end

-- ========== AIMBOT ==========
local function UpdateAimbot()
    if not AimbotEnabled or not SelectedPlayer then return end
    
    local character = LocalPlayer.Character
    if not character then return end
    
    local chestPos = GetCenterPosition(SelectedPlayer)
    if not chestPos then return end
    
    local cameraPos = Camera.CFrame.Position
    local direction = (chestPos - cameraPos).Unit
    local newCFrame = CFrame.lookAt(cameraPos, cameraPos + direction)
    Camera.CFrame = Camera.CFrame:Lerp(newCFrame, 0.35)
end

-- ========== ESP ==========
local function CreateBox(player)
    local character = player.Character
    if not character then return end
    if ESPObjects[player] then ClearESP(player) end
    
    local box = Instance.new("BoxHandleAdornment")
    box.Size = Vector3.new(3.5, 4.5, 2.5)
    box.Color3 = Color3.fromRGB(255, 0, 0)
    box.Transparency = 0.4
    box.AlwaysOnTop = true
    box.ZIndex = 10
    box.Adornee = character
    box.Parent = ScreenGui
    
    local billboard = Instance.new("BillboardGui")
    billboard.Size = UDim2.new(0, 150, 0, 35)
    billboard.StudsOffset = Vector3.new(0, 2.5, 0)
    billboard.AlwaysOnTop = true
    billboard.Parent = character
    
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Size = UDim2.new(1, 0, 0, 18)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = player.Name
    nameLabel.TextColor3 = player == SelectedPlayer and Color3.fromRGB(255, 255, 0) or Color3.fromRGB(255, 255, 255)
    nameLabel.TextStrokeTransparency = 0.2
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.TextSize = 11
    nameLabel.Parent = billboard
    
    local distanceLabel = Instance.new("TextLabel")
    distanceLabel.Size = UDim2.new(1, 0, 0, 15)
    distanceLabel.Position = UDim2.new(0, 0, 1, -15)
    distanceLabel.BackgroundTransparency = 1
    distanceLabel.Text = "0"
    distanceLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    distanceLabel.Font = Enum.Font.Gotham
    distanceLabel.TextSize = 9
    distanceLabel.Parent = billboard
    
    ESPObjects[player] = {Box = box, Billboard = billboard, NameLabel = nameLabel, DistanceLabel = distanceLabel}
end

local function ClearESP(player)
    if ESPObjects[player] then
        if ESPObjects[player].Box then ESPObjects[player].Box:Destroy() end
        if ESPObjects[player].Billboard then ESPObjects[player].Billboard:Destroy() end
        ESPObjects[player] = nil
    end
end

local function ClearAllESP()
    for player, _ in pairs(ESPObjects) do
        ClearESP(player)
    end
end

local function UpdateESP()
    if not ESPEnabled then ClearAllESP(); return end
    
    local character = LocalPlayer.Character
    local hrp = character and character:FindFirstChild("HumanoidRootPart")
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local playerChar = player.Character
            if playerChar and playerChar:FindFirstChild("HumanoidRootPart") then
                if not ESPObjects[player] then
                    CreateBox(player)
                elseif hrp and ESPObjects[player].DistanceLabel then
                    local playerHrp = playerChar:FindFirstChild("HumanoidRootPart")
                    if playerHrp then
                        local distance = (hrp.Position - playerHrp.Position).Magnitude
                        ESPObjects[player].DistanceLabel.Text = string.format("%.0f", distance)
                        if ESPObjects[player].NameLabel then
                            ESPObjects[player].NameLabel.TextColor3 = player == SelectedPlayer and Color3.fromRGB(255, 255, 0) or Color3.fromRGB(255, 255, 255)
                        end
                        if ESPObjects[player].Bo
