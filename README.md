-- Script de Auto Farm para A Universal Time (Teste Anti-Hacker - Mobile)
-- AVISO: Use apenas em ambientes de teste autorizados!

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- Configurações
local farmRadius = 50 -- Raio para detectar NPCs
local targetNPCName = "Dummy" -- Nome do NPC alvo (substitua pelo nome correto do jogo)
local touchDelay = 0.3 -- Atraso entre toques simulados

-- Serviços
local VirtualInputManager = game:GetService("VirtualInputManager")
local UserInputService = game:GetService("UserInputService")

-- Função para encontrar o NPC mais próximo
local function getNearestNPC()
    local nearestNPC = nil
    local nearestDistance = farmRadius
    for _, npc in pairs(game.Workspace:GetChildren()) do
        if npc:IsA("Model") and npc.Name == targetNPCName and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 then
            local distance = (npc.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
            if distance < nearestDistance then
                nearestNPC = npc
                nearestDistance = distance
            end
        end
    end
    return nearestNPC
end

-- Função para simular toque na tela (ataque)
local function simulateTouch(position)
    local screenPos = Vector2.new(position.X, position.Y)
    VirtualInputManager:SendTouchEvent(true, screenPos, 1)
    wait(touchDelay)
    VirtualInputManager:SendTouchEvent(false, screenPos, 1)
end

-- Função para atacar o NPC
local function attackNPC(npc)
    if npc and npc:FindFirstChild("HumanoidRootPart") then
        -- Move o jogador até o NPC
        humanoidRootPart.CFrame = CFrame.new(npc.HumanoidRootPart.Position + Vector3.new(0, 0, 5))
        -- Simula toque na tela para ataque (ajuste a posição conforme necessário)
        local attackPosition = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y / 2)
        simulateTouch(attackPosition)
    end
end

-- Loop principal de auto farm
while true do
    local npc = getNearestNPC()
    if npc then
        attackNPC(npc)
    else
        print("Nenhum NPC encontrado no raio de " .. farmRadius .. " studs.")
    end
    wait(0.5) -- Intervalo para evitar lag ou detecção fácil
end
