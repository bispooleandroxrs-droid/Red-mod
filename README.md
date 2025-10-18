loadstring([[
-- MZ V5 Loadstring Profissional Final

local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/RobloxHacks/UI-Library/main/YourUILib.lua"))()

-- Tela de Login
local LoginWindow = Library:CreateWindow("MZ V5 Login")
local loginTextBox = LoginWindow:CreateTextBox("Senha", function() end)
local loginButton = LoginWindow:CreateButton("Login", function()
    if loginTextBox.Text == "MZ" then
        Library:Notify("Login bem-sucedido!")
        LoginWindow:Destroy()
        createHub()
    else
        Library:Notify("Senha incorreta!")
    end
end)

function createHub()
    local Hub = Library:CreateWindow("MZ V5 Hub")
    local HubExpanded = true

    -- Botão de minimizar/expandir
    Hub:CreateButton("Minimizar/Expandir", function()
        HubExpanded = not HubExpanded
        if HubExpanded then
            for i = Hub.Frame.Size.Y.Scale, 1, 0.05 do
                Hub.Frame.Size = UDim2.new(Hub.Frame.Size.X.Scale, Hub.Frame.Size.X.Offset, i, Hub.Frame.Size.Y.Offset)
                wait(0.01)
            end
        else
            for i = Hub.Frame.Size.Y.Scale, 0.1, -0.05 do
                Hub.Frame.Size = UDim2.new(Hub.Frame.Size.X.Scale, Hub.Frame.Size.X.Offset, i, Hub.Frame.Size.Y.Offset)
                wait(0.01)
            end
        end
    end)

    -- Imagem de fundo
    Hub:SetBackground("https://yourimageurl.com/image.png")

    -- Bloco 1 - Básico
    local Block1 = Hub:CreateFolder("Bloco 1 - Básico")
    local WalkSpeedToggle, WalkSpeedValue = false, 16

    -- Barra deslizante WalkSpeed
    Block1:CreateSlider("WalkSpeed", 16, 500, function(value)
        WalkSpeedValue = value
        if WalkSpeedToggle then
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WalkSpeedValue
        end
    end)

    Block1:CreateToggle("WalkSpeed Ativo", false, function(state)
        WalkSpeedToggle = state
        if state then
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = WalkSpeedValue
        else
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 16
        end
    end)

    local InfiniteJumpToggle = false
    Block1:CreateToggle("Infinite Jump", false, function(state)
        InfiniteJumpToggle = state
    end)

    local AutoOpenChestsToggle = false
    Block1:CreateToggle("Auto Open Chests", false, function(state)
        AutoOpenChestsToggle = state
    end)

    game:GetService("UserInputService").JumpRequest:Connect(function()
        if InfiniteJumpToggle then
            game.Players.LocalPlayer.Character.Humanoid:ChangeState("Jumping")
        end
    end)

    -- Bloco 2 - Avançado
    local Block2 = Hub:CreateFolder("Bloco 2 - Avançado")
    local HeadshotToggle = false
    Block2:CreateToggle("Headshot", false, function(state)
        HeadshotToggle = state
    end)

    local ESPLineToggle = false
    Block2:CreateToggle("ESP Linha", false, function(state)
        ESPLineToggle = state
    end)

    local FOVToggle = false
    local FOVRadius = 120
    Block2:CreateSlider("FOV Radius", 50, 500, function(value)
        FOVRadius = value
    end)
    local FOVTextLabel = Instance.new("TextLabel")
    FOVTextLabel.Size = UDim2.new(0,200,0,50)
    FOVTextLabel.Position = UDim2.new(0.5,-100,0,50)
    FOVTextLabel.Text = "FOV: "..FOVRadius.." - Inativo"
    FOVTextLabel.TextColor3 = Color3.fromRGB(255,255,255)
    FOVTextLabel.BackgroundTransparency = 1
    FOVTextLabel.Parent = game.CoreGui
    Block2:CreateToggle("FOV Círculo", false, function(state)
        FOVToggle = state
        FOVTextLabel.Text = "FOV: "..FOVRadius.." - "..(state and "Ativo" or "Inativo")
    end)

    -- Criando ESP e FOV
    local ESPLines = {}
    local FOVCircle = Drawing.new("Circle")
    FOVCircle.Visible = false
    FOVCircle.Thickness = 2
    FOVCircle.NumSides = 100
    FOVCircle.Radius = FOVRadius
    FOVCircle.Transparency = 1
    FOVCircle.Filled = false

    -- Loop otimizado
    spawn(function()
        while true do
            local tickTime = tick()
            local camera = workspace.CurrentCamera

            -- ESP
            for i,v in pairs(game.Players:GetPlayers()) do
                if v ~= game.Players.LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
                    if not ESPLines[v] then
                        ESPLines[v] = Drawing.new("Line")
                        ESPLines[v].Thickness = 2
                        ESPLines[v].Visible = false
                    end
                    local headPos = camera:WorldToViewportPoint(v.Character.Head.Position)
                    local localPos = camera:WorldToViewportPoint(game.Players.LocalPlayer.Character.Head.Position)
                    if ESPLineToggle then
                        ESPLines[v].From = Vector2.new(localPos.X, localPos.Y)
                        ESPLines[v].To = Vector2.new(headPos.X, headPos.Y)
                        local r = math.floor((math.sin(tickTime*5)*127)+128)
                        local g = math.floor((math.sin(tickTime*5 + 2)*127)+128)
                        local b = math.floor((math.sin(tickTime*5 + 4)*127)+128)
                        ESPLines[v].Color = Color3.fromRGB(r,g,b)
                        ESPLines[v].Visible = true
                    else
                        ESPLines[v].Visible = false
                    end
                end
            end

            -- Headshot Insta-Aim
            if HeadshotToggle then
                for i,v in pairs(game.Players:GetPlayers()) do
                    if v ~= game.Players.LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
                        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(game.Players.LocalPlayer.Character.HumanoidRootPart.Position, v.Character.Head.Position)
                    end
                end
            end

            -- FOV
            if FOVToggle then
                local mouse = game.Players.LocalPlayer:GetMouse()
                FOVCircle.Position = Vector2.new(mouse.X, mouse.Y)
                FOVCircle.Radius = FOVRadius
                local r = math.floor((math.sin(tickTime*2)*127)+128)
                local g = math.floor((math.sin(tickTime*2 + 2)*127)+128)
                local b = math.floor((math.sin(tickTime*2 + 4)*127)+128)
                FOVCircle.Color = Color3.fromRGB(r,g,b)
                FOVCircle.Visible = true
            else
                FOVCircle.Visible = false
            end

            wait(0.03)
        end
    end)
end
]])()# Red-mod
