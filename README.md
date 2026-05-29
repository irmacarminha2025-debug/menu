-- 🔥 ILHA BELA ROLEPLAY - MENU PREMIUM
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LP = Players.LocalPlayer

local Config = {
    ESP = true,
    Tracers = true,
    Distance = true,
    AutoInteract = true,
    Noclip = false,
}

local ESPs = {}
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui

-- ==================== MENU PREMIUM ====================
local Main = Instance.new("Frame")
Main.Size = UDim2.new(0, 340, 0, 520)
Main.Position = UDim2.new(0.5, -170, 0.2, 0)
Main.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Main.Parent = ScreenGui

local Stroke = Instance.new("UIStroke")
Stroke.Color = Color3.fromRGB(200, 20, 40)
Stroke.Thickness = 2.8
Stroke.Parent = Main

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 16)
Corner.Parent = Main

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 65)
Title.BackgroundColor3 = Color3.fromRGB(130, 0, 20)
Title.Text = "🔥 ILHA BELA PREMIUM"
Title.TextColor3 = Color3.new(1,1,1)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBlack
Title.Parent = Main

local TitleStroke = Instance.new("UIStroke")
TitleStroke.Color = Color3.fromRGB(255, 60, 60)
TitleStroke.Thickness = 1.5
TitleStroke.Parent = Title

local y = 80
local function AddToggle(name, default, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 52)
    btn.Position = UDim2.new(0.05, 0, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
    btn.Text = name .. ": " .. (default and "✅ ATIVADO" or "❌ DESATIVADO")
    btn.TextColor3 = Color3.new(1,1,1)
    btn.TextScaled = true
    btn.Font = Enum.Font.GothamSemibold
    btn.Parent = Main
    
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 12)

    local state = default
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = name .. ": " .. (state and "✅ ATIVADO" or "❌ DESATIVADO")
        callback(state)
    end)
    y = y + 60
end

AddToggle("ESP", true, function(v) Config.ESP = v end)
AddToggle("Tracers", true, function(v) Config.Tracers = v end)
AddToggle("Distância", true, function(v) Config.Distance = v end)
AddToggle("Auto Interagir", true, function(v) Config.AutoInteract = v end)
AddToggle("Noclip", false, function(v) Config.Noclip = v end)

-- ==================== BOTÃO ESCONDER MENU ====================
local HideBtn = Instance.new("TextButton")
HideBtn.Size = UDim2.new(0, 90, 0, 35)
HideBtn.Position = UDim2.new(1, -100, 0, 15)
HideBtn.BackgroundColor3 = Color3.fromRGB(180, 0, 30)
HideBtn.Text = "Esconder"
HideBtn.TextColor3 = Color3.new(1,1,1)
HideBtn.TextScaled = true
HideBtn.Parent = Main
Instance.new("UICorner", HideBtn).CornerRadius = UDim.new(0, 10)

local MenuVisible = true
HideBtn.MouseButton1Click:Connect(function()
    MenuVisible = not MenuVisible
    Main.Visible = MenuVisible
    HideBtn.Text = MenuVisible and "Esconder" or "Mostrar"
end)

-- ==================== VER POSIÇÃO ====================
local PosBtn = Instance.new("TextButton")
PosBtn.Size = UDim2.new(0.9, 0, 0, 50)
PosBtn.Position = UDim2.new(0.05, 0, 0, y)
PosBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 180)
PosBtn.Text = "📍 Ver Posição Atual"
PosBtn.TextColor3 = Color3.new(1,1,1)
PosBtn.TextScaled = true
PosBtn.Parent = Main

PosBtn.MouseButton1Click:Connect(function()
    local root = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
    if root then
        local p = root.Position
        print(string.format("📍 Posição: Vector3.new(%.2f, %.2f, %.2f)", p.X, p.Y, p.Z))
    end
end)

-- ==================== ESP ====================
RunService.RenderStepped:Connect(function()
    if not Config.ESP then
        for _, v in pairs(ESPs) do
            pcall(function()
                v.Box.Visible = false
                v.Tracer.Visible = false
                v.Text.Visible = false
            end)
        end
        return
    end

    for _, plr in ipairs(Players:GetPlayers()) do
        if plr == LP or not plr.Character or not plr.Character:FindFirstChild("Head") then continue end

        local head = plr.Character.Head
        local screen, onScreen = Camera:WorldToViewportPoint(head.Position)
        local dist = LP.Character and LP.Character:FindFirstChild("Head") and (head.Position - LP.Character.Head.Position).Magnitude or 0

        if not ESPs[plr] then
            ESPs[plr] = {
                Box = Drawing.new("Square"),
                Tracer = Drawing.new("Line"),
                Text = Drawing.new("Text")
            }
            local e = ESPs[plr]
            e.Box.Color = Color3.fromRGB(255, 50, 50)
            e.Box.Thickness = 1.5
            e.Tracer.Color = Color3.fromRGB(255, 100, 0)
            e.Tracer.Thickness = 1.4
            e.Text.Color = Color3.new(1,1,1)
            e.Text.Size = 14
            e.Text.Center = true
            e.Text.Outline = true
        end

        local e = ESPs[plr]
        if onScreen then
            local size = 1700 / screen.Z
            e.Box.Size = Vector2.new(size*0.6, size*1.7)
            e.Box.Position = Vector2.new(screen.X - e.Box.Size.X/2, screen.Y - e.Box.Size.Y/2)
            e.Box.Visible = true

            if Config.Tracers then
                e.Tracer.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y - 30)
                e.Tracer.To = Vector2.new(screen.X, screen.Y)
                e.Tracer.Visible = true
            else
                e.Tracer.Visible = false
            end

            if Config.Distance then
                e.Text.Text = math.floor(dist) .. "m"
                e.Text.Position = Vector2.new(screen.X, screen.Y + e.Box.Size.Y/2 + 5)
                e.Text.Visible = true
            else
                e.Text.Visible = false
            end
        else
            e.Box.Visible = false
            e.Tracer.Visible = false
            e.Text.Visible = false
        end
    end
end)

-- ==================== AUTO INTERACT ====================
RunService.Heartbeat:Connect(function()
    if not Config.AutoInteract then return end
    if not LP.Character then return end

    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") and obj.Enabled then
            local dist = (LP.Character.HumanoidRootPart.Position - obj.Parent.Position).Magnitude
            if dist < 15 then
                obj:InputHoldBegin()
                task.wait(0.5)
                obj:InputHoldEnd()
            end
        end
    end
end)

-- ==================== NOCLIP ====================
RunService.Stepped:Connect(function()
    if Config.Noclip and LP.Character then
        for _, part in pairs(LP.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end)

print("✅ Script Premium Ilha Bela Carregado!")
print("Use o menu para configurar")
