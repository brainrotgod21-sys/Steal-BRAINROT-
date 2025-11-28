-- HUB PARA PVP - Steal a Brainrot PvP
-- Autor: brainrotgod21-sys | Data: 2025-11-28

-- Serviços Roblox
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Controle das habilidades
local HubAtivo = true
local VelocidadeAtiva = false
local MedusaImune = false
local InfJumpAtivo = false
local ShiftLockAtivo = false
local MiraAssistidaAtiva = false

-- Interface do Hub
local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.Name = "PvPHub"

local frame = Instance.new("Frame", ScreenGui)
frame.Position = UDim2.new(0, 50, 0, 100)
frame.Size = UDim2.new(0, 270, 0, 270)
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.BorderSizePixel = 2

local titulo = Instance.new("TextLabel", frame)
titulo.Text = "HUB PARA PVP"
titulo.Size = UDim2.new(1, 0, 0, 45)
titulo.BackgroundTransparency = 1
titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
titulo.Font = Enum.Font.GothamBold
titulo.TextScaled = true

local function criarBotao(nome, ordem, callback)
    local botao = Instance.new("TextButton", frame)
    botao.Text = nome
    botao.Size = UDim2.new(0.9, 0, 0, 30)
    botao.Position = UDim2.new(0.05, 0, 0, 50 + ((ordem - 1) * 35))
    botao.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    botao.TextColor3 = Color3.new(1,1,1)
    botao.Font = Enum.Font.Gotham
    botao.TextScaled = true
    botao.MouseButton1Click:Connect(callback)
    return botao
end

-- Funções das habilidades

-- AUMENTO DE VELOCIDADE
local function setVelocidade(status)
    VelocidadeAtiva = status
    if VelocidadeAtiva then
        humanoid.WalkSpeed = 28
    else
        humanoid.WalkSpeed = 16
    end
end

-- IMUNIDADE À MEDUSA
local function setMedusaImunidade(status)
    MedusaImune = status
    -- Implementação depende do sistema de 'Medusa' do jogo.
    -- Exemplo: Ignorar ou remover status effects
    if MedusaImune then
        if character:FindFirstChild("MedusaEffect") then
            character.MedusaEffect:Destroy()
        end
    end
end

-- INF JUMP
local infJumpEnabled = false
local function infJumpLoop()
    UserInputService.JumpRequest:Connect(function()
        if InfJumpAtivo then
            if humanoid then
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end
    end)
end
infJumpLoop()

local function setInfJump(status)
    InfJumpAtivo = status
end

-- SHIFT LOCK APRIMORADO
local function setShiftLock(status)
    ShiftLockAtivo = status
    if ShiftLockAtivo then
        LocalPlayer.CameraMode = Enum.CameraMode.LockFirstPerson
    else
        LocalPlayer.CameraMode = Enum.CameraMode.Classic
    end
end

-- MIRA ASSISTIDA
local function setMiraAssistida(status)
    MiraAssistidaAtiva = status
end

-- Função de mira automática (Exemplo simples: centraliza o mouse no target mais próximo dentro de um raio visível)
local function assistAim()
    RunService.RenderStepped:Connect(function()
        if MiraAssistidaAtiva then
            -- Encontra o oponente mais próximo
            local closest
            local dist = math.huge
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local hrp = player.Character.HumanoidRootPart
                    local screenPos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(hrp.Position)
                    if onScreen then
                        local mousePos = UserInputService:GetMouseLocation()
                        local d = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
                        if d < dist then
                            dist = d
                            closest = hrp
                        end
                    end
                end
            end
            -- Centraliza mira (simulado - ajuste para sistema do seu jogo)
            if closest then
                local screenPos = workspace.CurrentCamera:WorldToViewportPoint(closest.Position)
                -- Por questões de segurança, não mova o mouse do usuário automaticamente.
                -- Implemente aqui o sistema de 'soft aim' conforme necessidade.
            end
        end
    end)
end
assistAim()

-- Botões na interface
criarBotao("Aumento de Velocidade", 1, function()
    setVelocidade(not VelocidadeAtiva)
end)

criarBotao("Imunidade à Medusa", 2, function()
    setMedusaImunidade(not MedusaImune)
end)

criarBotao("Inf Jump", 3, function()
    setInfJump(not InfJumpAtivo)
end)

criarBotao("SHIFT LOCK Aprimorado", 4, function()
    setShiftLock(not ShiftLockAtivo)
end)

criarBotao("Mira Assistida", 5, function()
    setMiraAssistida(not MiraAssistidaAtiva)
end)

-- Infos wrapper central
local info = Instance.new("TextLabel", frame)
info.Size = UDim2.new(0.9, 0, 0, 60)
info.Position = UDim2.new(0.05, 0, 0, 220)
info.BackgroundTransparency = 1
info.TextColor3 = Color3.fromRGB(220,220,220)
info.Font = Enum.Font.Gotham
info.TextScaled = true
info.Text = "Use o HUB PARA PVP para ativar/desativar suas habilidades durante o combate!"

-- Exemplo: Ativar auto ao pegar cérebro (substitua "BrainItem" pelo nome correto do item)
local function onCollectBrain()
    setVelocidade(true)
    setMedusaImunidade(true)
    setInfJump(true)
end

-- Detectar coleta do cérebro (substitua "BrainItem" pelo nome real)
if character:FindFirstChild("BrainItem") then
    onCollectBrain()
end
character.ChildAdded:Connect(function(child)
    if child.Name == "BrainItem" then
        onCollectBrain()
    end
end)
character.ChildRemoved:Connect(function(child)
    if child.Name == "BrainItem" then
        setVelocidade(false)
        setMedusaImunidade(false)
        setInfJump(false)
    end
end)

-- Dicas finais: Ajuste os nomes dos objetos conforme a estrutura do seu jogo para plena funcionalidade!

print("PvP HUB local ativado!")
