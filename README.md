-- Load
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/jensonhirst/Orion/main/source"))()

-- Main
local Window = OrionLib:MakeWindow({
    Name = "Day X Admin", 
    HidePremium = false, 
    SaveConfig = true, 
    ConfigFolder = "DayXAdminConfig"
})

-- Valores
_G.Fly = false
local flying = false
_G.Noclip = false
_G.Aimbot = false

local aimbotConnection = nil

-- Funções

function Aimbot()
    local player = game.Players.LocalPlayer
    local camera = game.Workspace.CurrentCamera

    local function findClosestPlayer()
        local closestPlayer = nil
        local shortestDistance = math.huge
        for _, otherPlayer in pairs(game.Players:GetPlayers()) do
            if otherPlayer ~= player and otherPlayer.Character then
                local humanoidRootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    local distance = (player.Character.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestPlayer = otherPlayer
                    end
                end
            end
        end
        return closestPlayer
    end

    aimbotConnection = game:GetService("RunService").Heartbeat:Connect(function()
        if _G.Aimbot then
            local targetPlayer = findClosestPlayer()
            if targetPlayer and targetPlayer.Character then
                local humanoidRootPart = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    camera.CFrame = CFrame.new(camera.CFrame.Position, humanoidRootPart.Position)
                end
            end
        end
    end)
end

function Noclip()
    while _G.Noclip do
        if not game:GetService('Players').LocalPlayer.Character:FindFirstChild("LowerTorso") then
            while _G.Noclip do
                game:GetService("RunService").Stepped:Wait()
                game.Players.LocalPlayer.Character.Head.CanCollide = false
                game.Players.LocalPlayer.Character.Torso.CanCollide = false
            end
        else
            if _G.InitNC ~= true then     
                _G.NCFunc = function(part)      
                    local pos = game:GetService('Players').LocalPlayer.Character.LowerTorso.Position.Y  
                    if _G.Noclip then             
                        if part.Position.Y > pos then                 
                            part.CanCollide = false             
                        end        
                    end    
                end      
                _G.InitNC = true 
            end 
            game:GetService('Players').LocalPlayer.Character.Humanoid.Touched:Connect(_G.NCFunc) 
        end
        wait(0.1)
    end
end

function Fly()
    while _G.Fly do
        repeat wait() until game.Players.LocalPlayer 
            and game.Players.LocalPlayer.Character 
            and game.Players.LocalPlayer.Character:FindFirstChild("Torso") 
            and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
        local mouse = game.Players.LocalPlayer:GetMouse()
        repeat wait() until mouse
        local plr = game.Players.LocalPlayer
        local torso = plr.Character.Torso
        flying = true
        local ctrl = {f = 0, b = 0, l = 0, r = 0}
        local lastctrl = {f = 0, b = 0, l = 0, r = 0}
        local maxspeed = 50
        local speed = 0

        local function Flyy()
            local bg = Instance.new("BodyGyro", torso)
            bg.P = 9e4
            bg.maxTorque = Vector3.new(9e9, 9e9, 9e9)
            bg.cframe = torso.CFrame
            local bv = Instance.new("BodyVelocity", torso)
            bv.velocity = Vector3.new(0, 0.1, 0)
            bv.maxForce = Vector3.new(9e9, 9e9, 9e9)
            repeat wait()
                plr.Character.Humanoid.PlatformStand = true
                if ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0 then
                    speed = speed + 0.5 + (speed / maxspeed)
                    if speed > maxspeed then
                        speed = maxspeed
                    end
                elseif speed ~= 0 then
                    speed = speed - 1
                    if speed < 0 then
                        speed = 0
                    end
                end
                if (ctrl.l + ctrl.r) ~= 0 or (ctrl.f + ctrl.b) ~= 0 then
                    bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (ctrl.f + ctrl.b))
                        + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(ctrl.l + ctrl.r, (ctrl.f + ctrl.b) * 0.2, 0).p)
                        - game.Workspace.CurrentCamera.CoordinateFrame.p)) * speed
                    lastctrl = {f = ctrl.f, b = ctrl.b, l = ctrl.l, r = ctrl.r}
                elseif speed ~= 0 then
                    bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (lastctrl.f + lastctrl.b))
                        + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(lastctrl.l + lastctrl.r, (lastctrl.f + lastctrl.b) * 0.2, 0).p)
                        - game.Workspace.CurrentCamera.CoordinateFrame.p)) * speed
                else
                    bv.velocity = Vector3.new(0, 0.1, 0)
                end
                bg.cframe = game.Workspace.CurrentCamera.CoordinateFrame * CFrame.Angles(-math.rad((ctrl.f + ctrl.b) * 50 * speed / maxspeed), 0, 0)
            until not flying
            ctrl = {f = 0, b = 0, l = 0, r = 0}
            lastctrl = {f = 0, b = 0, l = 0, r = 0}
            speed = 0
            bg:Destroy()
            bv:Destroy()
            plr.Character.Humanoid.PlatformStand = false
        end

        mouse.KeyDown:Connect(function(key)
            if key:lower() == "e" then
                _G.Fly = not _G.Fly
                if _G.Fly then
                    Flyy()
                else
                    flying = false
                end
            elseif key:lower() == "w" then
                ctrl.f = 1
            elseif key:lower() == "s" then
                ctrl.b = -1
            elseif key:lower() == "a" then
                ctrl.l = -1
            elseif key:lower() == "d" then
                ctrl.r = 1
            end
        end)

        mouse.KeyUp:Connect(function(key)
            if key:lower() == "w" then
                ctrl.f = 0
            elseif key:lower() == "s" then
                ctrl.b = 0
            elseif key:lower() == "a" then
                ctrl.l = 0
            elseif key:lower() == "d" then
                ctrl.r = 0
            end
        end)
        Flyy()
    end
end

-- Criação de Tabs
local PlayerTab = Window:MakeTab({
    Name = "PlayerTab",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local PvPTab = Window:MakeTab({
    Name = "PvPTab",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local AdminTab = Window:MakeTab({
    Name = "Admin",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local BotTab = Window:MakeTab({
    Name = "Bot",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local HubTab = Window:MakeTab({
    Name = "Hub",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local PvPSection = PvPTab:AddSection({
    Name = "PvP"
})

local PlayerSection = PlayerTab:AddSection({
    Name = "Player"
})

local AdminSection = AdminTab:AddSection({
    Name = "Admin"
})

local BotSection = BotTab:AddSection({
    Name = "Bot"
})

local HubSection = HubTab:AddSection({
    Name = "Hub"
})

OrionLib:MakeNotification({
    Name = "Day X Admin",
    Content = "Day X Admin",
    Image = "rbxassetid://4483345998",
    Time = 5
})

-- Toggle de Aimbot na aba PvPTab
PvPTab:AddToggle({
    Name = "Aimbot!",
    Default = false,
    Callback = function(Value)
        _G.Aimbot = Value
        if _G.Aimbot then
            Aimbot()
        else
            if aimbotConnection then
                aimbotConnection:Disconnect()
                aimbotConnection = nil
            end
        end
    end    
})

PlayerTab:AddToggle({
    Name = "Fly!",
    Default = false,
    Callback = function(Value)
        _G.Fly = Value
        if _G.Fly then
            Fly()
        else
            flying = false
        end
    end    
})

PlayerTab:AddToggle({
    Name = "Noclip",
    Default = false,
    Callback = function(Value)
        _G.Noclip = Value
        if _G.Noclip then
            Noclip()
        end
    end    
})

PlayerTab:AddSlider({
    Name = "WalkSpeed",
    Min = 0,
    Max = 300,
    Default = 30,
    Color = Color3.fromRGB(255,255,255),
    Increment = 1,
    ValueName = "WalkSpeed",
    Callback = function(Value)
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.WalkSpeed = Value
        end
    end    
})

PlayerTab:AddSlider({
    Name = "JumpPower",
    Min = 0,
    Max = 300,
    Default = 16,
    Color = Color3.fromRGB(255,255,255),
    Increment = 1,
    ValueName = "PowerSpeed",
    Callback = function(Value)
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.JumpPower = Value
        end
    end    
})

-- Botões na aba Admin
AdminTab:AddButton({
    Name = "Dex",
    Callback = function()
        loadstring(game:HttpGet("https://cdn.wearedevs.net/scripts/Dex%20Explorer.txt"))()
    end
})

AdminTab:AddButton({
    Name = "Infinite Yield",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
    end
})

AdminTab:AddButton({
    Name = "RemoteFinder",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/OreOFDAY12/RemoteSpy/refs/heads/main/README.md"))()
    end
})

-- Botões na aba Bot
BotTab:AddButton({
    Name = "QuizBot",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Damian-11/quizbot/master/quizbot.luau"))()
    end
})

BotTab:AddButton({
    Name = "LunarBot",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/probablYnicKxD/ProjectLunar/main/LunarBot/Source.lua"))()
    end
})

-- Botões na aba Hub
HubTab:AddButton({
    Name = "Ore X Hub V4",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/kihc5TgS"))()
    end
})

HubTab:AddButton({
    Name = "WAzure",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Blox-Fruits-W-azure-v2-new-12336"))()
    end
})

HubTab:AddButton({
    Name = "RedZHub",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/realredz/BloxFruits/refs/heads/main/Source.lua"))()
    end
})
