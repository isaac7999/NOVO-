local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")
local noclip = false
local infiniteJump = false
local slowFall = false
local autoTeleport = false

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
ScreenGui.ResetOnSpawn = false
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 200, 0, 180)
Frame.Position = UDim2.new(0.1, 0, 0.1, 0)
Frame.BackgroundColor3 = Color3.new(0, 0, 0)
Frame.Active = true
Frame.Draggable = true
Frame.ClipsDescendants = true

-- Botão de Minimizar
local MinimizeButton = Instance.new("TextButton", Frame)
MinimizeButton.Size = UDim2.new(0, 200, 0, 20)
MinimizeButton.Position = UDim2.new(0, 0, 0, 0)
MinimizeButton.Text = "Minimizar"
MinimizeButton.BackgroundColor3 = Color3.new(1, 0, 0)
MinimizeButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

-- Botão Pulo Infinito
local JumpButton = Instance.new("TextButton", Frame)
JumpButton.Size = UDim2.new(0, 180, 0, 30)
JumpButton.Position = UDim2.new(0, 10, 0, 30)
JumpButton.Text = "Ativar Pulo Infinito"
JumpButton.BackgroundColor3 = Color3.new(0, 1, 0)
JumpButton.MouseButton1Click:Connect(function()
    infiniteJump = not infiniteJump
    JumpButton.Text = infiniteJump and "Desativar Pulo Infinito" or "Ativar Pulo Infinito"
end)
UserInputService.JumpRequest:Connect(function()
    if infiniteJump then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- Botão Noclip
local NoclipButton = Instance.new("TextButton", Frame)
NoclipButton.Size = UDim2.new(0, 180, 0, 30)
NoclipButton.Position = UDim2.new(0, 10, 0, 70)
NoclipButton.Text = "Ativar Noclip"
NoclipButton.BackgroundColor3 = Color3.new(0, 0, 1)
NoclipButton.MouseButton1Click:Connect(function()
    noclip = not noclip
    NoclipButton.Text = noclip and "Desativar Noclip" or "Ativar Noclip"
end)
RunService.Stepped:Connect(function()
    if noclip then
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end)

-- Botão Queda Lenta
local SlowFallButton = Instance.new("TextButton", Frame)
SlowFallButton.Size = UDim2.new(0, 180, 0, 30)
SlowFallButton.Position = UDim2.new(0, 10, 0, 110)
SlowFallButton.Text = "Ativar Queda Lenta"
SlowFallButton.BackgroundColor3 = Color3.new(1, 1, 0)
SlowFallButton.MouseButton1Click:Connect(function()
    slowFall = not slowFall
    SlowFallButton.Text = slowFall and "Desativar Queda Lenta" or "Ativar Queda Lenta"
end)
RunService.Heartbeat:Connect(function()
    if slowFall and humanoid.FloorMaterial == Enum.Material.Air then
        rootPart.Velocity = Vector3.new(rootPart.Velocity.X, math.max(rootPart.Velocity.Y, -5), rootPart.Velocity.Z)
    end
end)

-- Botão Auto Teleport
local AutoTeleportButton = Instance.new("TextButton", Frame)
AutoTeleportButton.Size = UDim2.new(0, 180, 0, 30)
AutoTeleportButton.Position = UDim2.new(0, 10, 0, 150)
AutoTeleportButton.Text = "Ativar Auto Teleport"
AutoTeleportButton.BackgroundColor3 = Color3.new(1, 0, 1)
AutoTeleportButton.MouseButton1Click:Connect(function()
    autoTeleport = not autoTeleport
    AutoTeleportButton.Text = autoTeleport and "Desativar Auto Teleport" or "Ativar Auto Teleport"
end)
RunService.Heartbeat:Connect(function()
    if autoTeleport then
        for _, otherPlayer in pairs(Players:GetPlayers()) do
            if otherPlayer ~= player and otherPlayer.Character then
                local otherHumanoid = otherPlayer.Character:FindFirstChild("Humanoid")
                local otherRootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                if otherHumanoid and otherRootPart and otherHumanoid.Health == 0 then
                    rootPart.CFrame = otherRootPart.CFrame
                    break
                end
            end
        end
    end
end)
