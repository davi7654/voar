local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = game.Players.LocalPlayer

local flying = false
local flySpeed = 50

local moveDirection = Vector3.new()
local bodyGyro, bodyVelocity, connStepped

function startFlying()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.P = 9e4
    bodyGyro.MaxTorque = Vector3.new(9e4, 9e4, 9e4)
    bodyGyro.CFrame = humanoidRootPart.CFrame
    bodyGyro.Parent = humanoidRootPart

    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = Vector3.new(0,0,0)
    bodyVelocity.MaxForce = Vector3.new(9e4, 9e4, 9e4)
    bodyVelocity.Parent = humanoidRootPart

    connStepped = RunService.RenderStepped:Connect(function()
        if flying then
            bodyGyro.CFrame = workspace.CurrentCamera.CFrame
            bodyVelocity.Velocity = (workspace.CurrentCamera.CFrame.lookVector * moveDirection.Z +
                                     workspace.CurrentCamera.CFrame.RightVector * moveDirection.X) * flySpeed
        end
    end)
end

function stopFlying()
    if bodyGyro then bodyGyro:Destroy() end
    if bodyVelocity then bodyVelocity:Destroy() end
    if connStepped then connStepped:Disconnect() end
    moveDirection = Vector3.new()
end

UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end
    if input.KeyCode == Enum.KeyCode.F then
        flying = not flying
        if flying then
            startFlying()
        else
            stopFlying()
        end
    end
    if flying then
        if input.KeyCode == Enum.KeyCode.W then
            moveDirection = Vector3.new(moveDirection.X, 0, 1)
        elseif input.KeyCode == Enum.KeyCode.S then
            moveDirection = Vector3.new(moveDirection.X, 0, -1)
        elseif input.KeyCode == Enum.KeyCode.A then
            moveDirection = Vector3.new(-1, 0, moveDirection.Z)
        elseif input.KeyCode == Enum.KeyCode.D then
            moveDirection = Vector3.new(1, 0, moveDirection.Z)
        end
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if flying then
        if input.KeyCode == Enum.KeyCode.W or input.KeyCode == Enum.KeyCode.S then
            moveDirection = Vector3.new(moveDirection.X, 0, 0)
        elseif input.KeyCode == Enum.KeyCode.A or input.KeyCode == Enum.KeyCode.D then
            moveDirection = Vector3.new(0, 0, moveDirection.Z)
        end
    end
end)
