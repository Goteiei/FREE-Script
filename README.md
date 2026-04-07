local Supported = {

    Places = {
        [104715542330896] = true
    },

    Universes = {
        [6765805766] = true
    }

}

if not Supported.Places[game.PlaceId]
and not Supported.Universes[game.GameId] then

    game.Players.LocalPlayer:Kick("Game not supported")
    return

end

local function setupAutoBypass()
    local function generateRandomName()
        local chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
        local result = ""

        for i = 1, 16 do
            result = result .. string.sub(chars, math.random(1, #chars), 1)
        end
        return result
    end
    
    local fakeVars = {}
    for i = 1, 10 do
        fakeVars["_G" .. generateRandomName()] = {loaded = false, version = "1.0.0", authorized = true}
        fakeVars["Script" .. generateRandomName()] = {enabled = false, type = "utility"}
        fakeVars["Module" .. generateRandomName()] = {initialized = true}
    end
    
    for name, value in pairs(fakeVars) do
        getgenv()[name] = value
    end
    
    if hookfunction then
        pcall(function()
            local originalNamecall
            originalNamecall = hookfunction(debug.getmetatable(game).__namecall, function(self, ...)
                local method = getnamecallmethod()
                local args = {...}
                
                if method == "FindFirstChild" or method == "FindFirstChildWhichIsA" then
                    if args[1] and (string.find(string.lower(tostring(args[1])), "script") or 
                       string.find(string.lower(tostring(args[1])), "cheat") or
                       string.find(string.lower(tostring(args[1])), "hack") or
                       string.find(string.lower(tostring(args[1])), "exploit")) then
                        return nil
                    end
                end
                
                if method == "Kick" or method == "Crash" then
                    return nil
                end
                
                return originalNamecall(self, ...)
            end)
        end)
    end
end
setupAutoBypass()

repeat task.wait(3) until game:IsLoaded()

local WindUI
do
    local ok, result = pcall(function()
        return require("./src/Init")
    end)
    if ok then
        WindUI = result
    else
        WindUI = loadstring(game:HttpGet("https://raw.githubusercontent.com/Footagesus/WindUI/refs/heads/main/dist/main.lua"))()
    end
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Debris = game:GetService("Debris") 
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui = game:GetService("CoreGui")

local function createBulletTrail(startPos, endPos)
    local trailPart = Instance.new("Part")
    trailPart.Anchored = true
    trailPart.CanCollide = false
    trailPart.Size = Vector3.new(0.01, 0.01, (startPos - endPos).Magnitude)
    trailPart.CFrame = CFrame.new((startPos + endPos)/2, endPos)
    trailPart.Material = Enum.Material.Neon
    trailPart.Transparency = 0.01
 trailPart.Color = Color3.fromRGB(255, 0, 0)

    trailPart.Parent = workspace
    
    game:GetService("Debris"):AddItem(trailPart, 3.5)
end

local player = game.Players.LocalPlayer

repeat task.wait() until game:IsLoaded()
repeat task.wait() until player:FindFirstChild("PlayerGui")

local function characterAlreadyCreated()
    return player.Character ~= nil
end

local function clickPlayIfNeeded()
    if characterAlreadyCreated() then
        return
    end

    local gui = player.PlayerGui:FindFirstChild("SplashScreenGui")
    if not gui then return end

    local btn = gui:FindFirstChild("Frame")
        and gui.Frame:FindFirstChild("PlayButton")
    if not btn then return end

    pcall(function()
        firesignal(btn.MouseButton1Click)
    end)

    pcall(function()
        btn:Activate()
    end)
end

local function clickSkipIfNeeded()
   
    if characterAlreadyCreated() then
        return
    end

    local gui = player.PlayerGui:FindFirstChild("CharacterCreator")
    if not gui then return end

    local btn = gui:FindFirstChild("MenuFrame")
        and gui.MenuFrame:FindFirstChild("AvatarMenuSkipButton")
    if not btn then return end

    pcall(function()
        firesignal(btn.MouseButton1Click)
    end)

    pcall(function()
        btn:Activate()
    end)
end

-- ================== RUN ==================
task.wait(0.6)
clickPlayIfNeeded()

task.wait(1)
clickSkipIfNeeded()

WindUI:AddTheme({
    Name = "My Theme",

 Accent = Color3.fromRGB(0, 255, 255),     
   Background = Color3.fromRGB(12, 12, 18),
    Outline = Color3.fromRGB(255, 140, 0),
    Text = Color3.fromRGB(255, 230, 230),
    Placeholder = Color3.fromRGB(180, 120, 120),
    Button = Color3.fromRGB(255, 140, 0),      
    Icon = Color3.fromRGB(255, 140, 0),
})

local Window = WindUI:CreateWindow({
    Title = "Godzilla",
    Icon = "rbxassetid://127195015189533",
    Author = "Hello",
    Folder = "GodHub",
    Size = UDim2.fromOffset(500, 460),
    Theme = "My Theme",

    User = {
        Enabled = true,
       Anonymous = true,
        Callback = function()
            print("clicked")
        end,
        Thumbnail = {
            Image = "rbxthumb://type=AvatarHeadShot&id="..player.UserId.."&w=150&h=150",
            Title = player.Name,
        },
    },
})

do
    Window:Tag({
        Title = "Blockspin",
        Icon = "sword",
        Color = Color3.fromHex("#FF0000")
    })
end

Window:EditOpenButton({ Enabled = false })

local ScreenGui = Instance.new("ScreenGui")
local ToggleBtn = Instance.new("ImageButton")

ScreenGui.Name = "WindUI_Toggle"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui

ToggleBtn.Size = UDim2.new(0, 33, 0, 33)
ToggleBtn.Position = UDim2.new(1, -53, 0, 20)
ToggleBtn.BackgroundTransparency = 0.2
ToggleBtn.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
ToggleBtn.Image = "rbxassetid://127195015189533"
ToggleBtn.Active = true
ToggleBtn.Draggable = true
ToggleBtn.Parent = ScreenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = ToggleBtn

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(255, 140, 0)
stroke.Thickness = 2
stroke.Transparency = 0.2
stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
stroke.Parent = ToggleBtn

local glowTween = TweenService:Create(
    stroke,
    TweenInfo.new(
        1.2,
        Enum.EasingStyle.Sine,
        Enum.EasingDirection.InOut,
        -1,
        true
    ),
    { Transparency = 0.6 }
)

glowTween:Play()

local opened = true

local function toggle()
    opened = not opened
    if Window.UI then
        Window.UI.Enabled = opened
    else
        Window:Toggle()
    end
end

ToggleBtn.MouseButton1Click:Connect(function()
    ToggleBtn:TweenSize(
        UDim2.new(0, 33, 0, 33),
        Enum.EasingDirection.Out,
        Enum.EasingStyle.Quad,
        0.12,
        true,
        function()
            ToggleBtn:TweenSize(
                UDim2.new(0, 33, 0, 33),
                Enum.EasingDirection.Out,
                Enum.EasingStyle.Quad,
                0.12,
                true
            )
        end
    )
    toggle()
end)

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.T then
        toggle()
    end
end)

local SilentAimEnabled = false
local WallbangEnabled = false
local AttackAntiAimEnabled = false
local BlackholeEnabled = false
local shootingDistance = 1500
local CurrentTarget = nil
local HitPartChoice = "Head"
local ShowFOV = false
local BodyAimHealth = 26

local FOVRadius = 200

local FOVGui = Instance.new("ScreenGui")
FOVGui.Name = "FOVCircleGui"
FOVGui.ResetOnSpawn = false
FOVGui.DisplayOrder = 9999
FOVGui.IgnoreGuiInset = true
FOVGui.Parent = game:GetService("CoreGui")

local FOVCircle = Instance.new("Frame")
FOVCircle.Name = "FOVCircle"
FOVCircle.Size = UDim2.new(0, FOVRadius*2, 0, FOVRadius*2)
FOVCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
FOVCircle.AnchorPoint = Vector2.new(0.5, 0.5)
FOVCircle.BackgroundTransparency = 1
FOVCircle.BorderSizePixel = 0
FOVCircle.Visible = true
FOVCircle.Parent = FOVGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(1, 0)
corner.Parent = FOVCircle

local stroke = Instance.new("UIStroke")
stroke.Thickness = 0.5
stroke.Color = Color3.fromRGB(255, 140, 0)
stroke.Parent = FOVCircle

local Tracer = Drawing.new("Line")
Tracer.Thickness = 0.5
Tracer.Color = Color3.fromRGB(255, 0, 0)
Tracer.Transparency = 0.5
Tracer.Visible = false


local function getPing()
    local stats = LocalPlayer:FindFirstChild("PlayerGui"):FindFirstChild("NetworkStats")
    if stats then
        local pingText = stats:FindFirstChild("PingLabel")
        if pingText then
            local ping = tonumber(pingText.Text:match("%d+"))
            return ping and ping/1000 or 0.2
        end
    end
    return 0.2
end

local function IsPlayerSafe(player)
    if not player or not player.Character then return false end

    if player:GetAttribute("IsSafeZoneProtected") then
        return true
    end

    if player.Character:FindFirstChild("SpawnProtectionHighlight") then
        return true
    end

    return false
end

local IgnorePlayers = {}

local function getClosestTarget()
    local bestTarget = nil
    local bestScore = math.huge

    local cam = workspace.CurrentCamera
    local camPos = cam.CFrame.Position
    local camDir = cam.CFrame.LookVector
    local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)

   for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer
      and not IgnorePlayers[player.Name] 
      and not IsPlayerSafe(player)
      and player.Character
      and player.Character:FindFirstChild("Head")
      and player.Character:FindFirstChildOfClass("Humanoid") then
        
        local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid and humanoid.Health > 0 then
            local head = player.Character.Head
            local toTarget = (head.Position - camPos).Unit

            if toTarget:Dot(camDir) > 0 then
                local pos, onScreen = cam:WorldToViewportPoint(head.Position)
                if onScreen then
                    local screenPos = Vector2.new(pos.X, pos.Y)
                    local distFromCenter = (screenPos - center).Magnitude
                    if distFromCenter <= FOVRadius then
                        local distance3D = (head.Position - camPos).Magnitude
                        local score = distance3D * 0.2 + distFromCenter

                        if score < bestScore then
                            bestScore = score
                            bestTarget = player
                        end
                    end
                end
            end
        end
    end
end
    return bestTarget
end

local function HasObstacle(origin, target)
    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = {workspace.CurrentCamera, LocalPlayer.Character, CurrentTarget.Character}

    local direction = (target - origin).Unit
    local distance = (target - origin).Magnitude
    
    local result = workspace:Raycast(origin, direction * distance, params)

    return result ~= nil
end

local function predictPosition(head, hrp)
    local ping = getPing()
    local velocity = hrp and hrp.Velocity or Vector3.zero
    return head.Position + (velocity * ping * 1.15)
end

local function isUsingAntiAim(player)

    local char = player.Character
    if not char then return false end

    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end

   
    local hum = char:FindFirstChildOfClass("Humanoid")
    if hum and hum.SeatPart then
        return false
    end

    local vel = hrp.Velocity

    local horizontalSpeed = Vector3.new(vel.X,0,vel.Z).Magnitude
    local ySpeed = math.abs(vel.Y)

    if horizontalSpeed > 85 then
        return true
    end

    if ySpeed > 40 then
        return true
    end

    return false
end

local function setupSilentAim()
    local remotes = game:GetService("ReplicatedStorage"):FindFirstChild("Remotes")
    if not remotes then return end
    local send = remotes:FindFirstChild("Send") or remotes:FindFirstChild("FireServer")
    if not send then return end
    if type(hookfunction) == "function" then
        local oldFire
        oldFire = hookfunction(send.FireServer, function(self, ...)
            local args = {...}
           if SilentAimEnabled
   and args[2] == "shoot_gun"
   and CurrentTarget
   and not IsPlayerSafe(CurrentTarget) then
             

   local targetPart
local humanoid = CurrentTarget.Character:FindFirstChildOfClass("Humanoid")

if humanoid and humanoid.Health < 26 then
    targetPart = CurrentTarget.Character:FindFirstChild("HumanoidRootPart")

else
   
    if HitPartChoice == "Head" then
        targetPart = CurrentTarget.Character:FindFirstChild("Head")
    else
        targetPart = CurrentTarget.Character:FindFirstChild("HumanoidRootPart")
    end
end

local hrp = CurrentTarget.Character:FindFirstChild("HumanoidRootPart")

if targetPart and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Head") then
    local myHead = LocalPlayer.Character.Head

  
    local aimPos
    if AttackAntiAimEnabled then
        aimPos = targetPart.Position
    else
        aimPos = predictPosition(targetPart, hrp)
    end
               
                    createBulletTrail(myHead.Position, aimPos)
                    local myPos = myHead.Position
                    local targetPos = aimPos
local NoWallbang = {
["Double Barrel"] = true,
["Sawnoff"] = true,
["Remington"] = true,
}

local gunName = tostring(args[3]) or ""
if NoWallbang[gunName] then
        args[4] = CFrame.new(myPos, targetPos)
else
    if HasObstacle(myPos, targetPos) then
        args[4] = CFrame.new(math.huge, math.huge, math.huge)
    else
        args[4] = CFrame.new(myPos, targetPos)
    end
end
                    args[5] = {}
                    for i = 1, 6 do
                        table.insert(args[5], {
                            [1] = {
                                ["Instance"] = targetPart,
                                ["Normal"] = Vector3.new(0,1,0),
                                ["Position"] = aimPos
                            }
                        })
                        createBulletTrail(myHead.Position, aimPos)
                    end
                end
            end
            return oldFire(self, unpack(args))
        end)
    end
end

RunService.RenderStepped:Connect(function()
   FOVCircle.Visible = ShowFOV
    FOVCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
    FOVCircle.Size = UDim2.new(0, FOVRadius * 2, 0, FOVRadius * 2)
  
    if SilentAimEnabled then
        CurrentTarget = getClosestTarget()
    else
        CurrentTarget = nil
    end

if CurrentTarget then
    AttackAntiAimEnabled = isUsingAntiAim(CurrentTarget)
else
    AttackAntiAimEnabled = false
end

    if CurrentTarget and CurrentTarget.Character and CurrentTarget.Character:FindFirstChild("Head") then
        local targetHead = CurrentTarget.Character.Head
        local myHead = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Head")
        if myHead then
            local myScreenPos, myOnScreen = Camera:WorldToViewportPoint(myHead.Position)
            local targetScreenPos, targetOnScreen = Camera:WorldToViewportPoint(targetHead.Position)
            if myOnScreen and targetOnScreen then
                Tracer.Visible = true
                local centerScreen = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                Tracer.From = centerScreen
                Tracer.To = Vector2.new(targetScreenPos.X, targetScreenPos.Y)
            else
                Tracer.Visible = false
            end
        end
    else
        Tracer.Visible = false
    end
end)

  
   

local Tab_Silent = Window:Tab({Title = "Silent Aim", Icon = "crosshair"})

Window:Divider()

Tab_Silent:Section({
    Title = "Combat:"
})

Tab_Silent:Toggle({
    Title = "Silent Aim",
   Flag = "Silent Aim",
    Default = false,
    Callback = function(state)
        SilentAimEnabled = state
        if state then
            setupSilentAim()
        end
    end
})

Tab_Silent:Toggle({
    Title = "Show FOV",
    Flag = "ShowFOV",
    Default = ShowFOV,
    Callback = function(state)
        ShowFOV = state
        FOVCircle.Visible = state
    end
})

Tab_Silent:Slider({
    Title = "FOV: ",
    Step = 1,
    Value = {
        Min = 20,
        Max = 300,
        Default = FOVRadius,
    },
    Callback = function(value)
        FOVRadius = tonumber(value) or 120
        print("FOV", FOVRadius)
    end
})

Tab_Silent:Slider({
    Title = "Shooting Distance",
    Step = 50,
    Value = {Min=500, Max=1500, Default=shootingDistance},
    Callback = function(value)
        shootingDistance = tonumber(value) or 1000
    end
})

local Dropdown = Tab_Silent:Dropdown({
    Title = "Aim Part",
    Values = { "Head", "HumanoidRootPart" },
    Value = "Head", 
    Multi = false,
    AllowNone = false,
    Callback = function(option)
        HitPartChoice = option
    end
})

local function getPlayerNames()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            table.insert(list, p.Name)
        end
    end
    return list
end

Tab_Silent:Dropdown({
    Title = "Ignore player",
    Values = getPlayerNames(),
    Multi = true,
    AllowNone = true,
    Callback = function(selected)
        IgnorePlayers = {}
        for _, name in ipairs(selected or {}) do
            IgnorePlayers[name] = true
        end
    end
})

local Tab_ESP = Window:Tab({
    Title = "ESP",
    Icon = "eye",
})

Window:Divider()

local boxESPEnabled = false
local nameESPEnabled = false
local distanceESPEnabled = false


Tab_ESP:Section({
    Title = "ESP:"
})

Tab_ESP:Toggle({
    Title = "Box ESP",
    Flag = "BoxESP",
    Default = false,
    Callback = function(state)
        boxESPEnabled = state
    end
})

Tab_ESP:Toggle({
    Title = "Name ESP",
    Flag = "NameESP",
    Default = false,
    Callback = function(state)
        nameESPEnabled = state
    end
})

Tab_ESP:Toggle({
    Title = "Distance ESP",
    Flag = "DistanceESP",
    Default = false,
    Callback = function(state)
        distanceESPEnabled = state
    end
})

local espConnections = {}

local CHARACTER_SIZE = Vector3.new(3.0, 6.0, 2)

local function IsSpawnProtected(character)
    return character and character:FindFirstChild("SpawnProtectionHighlight") ~= nil
end

local function createESP(player)
    local lines = {}
    for i = 1, 12 do
        local line = Drawing.new("Line")
        line.Color = Color3.new(1, 1, 1)
        line.Thickness = 1
        line.Visible = false
        table.insert(lines, line)
    end

    local nameText = Drawing.new("Text")
    nameText.Size = 12
    nameText.Center = true
    nameText.Outline = true
    nameText.Color = Color3.new(1,1,1)
    nameText.Visible = false

    local distanceText = Drawing.new("Text")
    distanceText.Size = 10
    distanceText.Center = true
    distanceText.Outline = true
    distanceText.Color = Color3.fromRGB(255,255,255)
    distanceText.Visible = false

    local conn = RunService.RenderStepped:Connect(function()
        if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
            for _, line in pairs(lines) do line.Visible = false end
            nameText.Visible = false
            distanceText.Visible = false
            return
        end

        local hrp = player.Character.HumanoidRootPart
        local cframe = hrp.CFrame
        local halfSize = CHARACTER_SIZE / 2

       
        local corners = {
            cframe * Vector3.new(-halfSize.X,  halfSize.Y, -halfSize.Z),
            cframe * Vector3.new( halfSize.X,  halfSize.Y, -halfSize.Z),
            cframe * Vector3.new( halfSize.X,  halfSize.Y,  halfSize.Z),
            cframe * Vector3.new(-halfSize.X,  halfSize.Y,  halfSize.Z),
            cframe * Vector3.new(-halfSize.X, -halfSize.Y, -halfSize.Z),
            cframe * Vector3.new( halfSize.X, -halfSize.Y, -halfSize.Z),
            cframe * Vector3.new( halfSize.X, -halfSize.Y,  halfSize.Z),
            cframe * Vector3.new(-halfSize.X, -halfSize.Y,  halfSize.Z),
        }

        local screenCorners = {}
        local onScreenAll = true
        for i, corner in ipairs(corners) do
            local screenPos, onScreen = Camera:WorldToViewportPoint(corner)
            if not onScreen or screenPos.Z < 0 then
                onScreenAll = false
            end
            screenCorners[i] = Vector2.new(screenPos.X, screenPos.Y)
        end

        if not onScreenAll then
            for _, line in pairs(lines) do line.Visible = false end
            nameText.Visible = false
            distanceText.Visible = false
            return
        end

       
        local edges = {
            {1,2},{2,3},{3,4},{4,1},
            {5,6},{6,7},{7,8},{8,5},
            {1,5},{2,6},{3,7},{4,8} 
        }

       if boxESPEnabled then
    for i, edge in ipairs(edges) do
        local startP = screenCorners[edge[1]]
        local endP = screenCorners[edge[2]]
        local line = lines[i] 
        if SilentAimEnabled and CurrentTarget == player then
            line.Color = Color3.fromRGB(255,0,0)
        else
            line.Color = Color3.fromRGB(255,255,255)
        end

        line.From = startP
        line.To = endP
        line.Visible = true
    end
else
    for _, line in pairs(lines) do line.Visible = false end
end

       
    if nameESPEnabled and player.Character and player.Character:FindFirstChild("Head") then
    local headPos, headOnScreen = Camera:WorldToViewportPoint(player.Character.Head.Position + Vector3.new(0, 0.5, 0))
    if headOnScreen then
        nameText.Text = player.Name
        nameText.Position = Vector2.new(headPos.X, headPos.Y - 16)

       
      
local isSafeZone = player:GetAttribute("IsSafeZoneProtected")
local isSpawnProt = IsSpawnProtected(player.Character)

if isSafeZone then
   
    nameText.Color = Color3.fromRGB(0, 255, 0)

elseif isSpawnProt then
   
    nameText.Color = Color3.fromRGB(0, 170, 255)

elseif SilentAimEnabled and CurrentTarget == player then
    nameText.Color = Color3.fromRGB(255, 0, 0)

else
    nameText.Color = Color3.fromRGB(255, 255, 255)
end

        nameText.Visible = true
    else
        nameText.Visible = false
    end
else
    nameText.Visible = false
end

       
        if distanceESPEnabled then
            local dist = (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart"))
                and (hrp.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude or 0
            local bottomPos, bottomOnScreen = Camera:WorldToViewportPoint(hrp.Position - Vector3.new(0, 15, 0))
            if bottomOnScreen then
                distanceText.Text = string.format("%.0f Distance", dist)
                distanceText.Position = Vector2.new(bottomPos.X, bottomPos.Y + 4)
                distanceText.Visible = true
            else
                distanceText.Visible = false
            end
        else
            distanceText.Visible = false
        end
    end)

    table.insert(espConnections, conn)
end

local function loadESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            createESP(player)
        end
    end

    Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function()
            wait(1)
            createESP(player)
        end)
    end)
end

local function clearESP()
    for _, conn in pairs(espConnections) do
        if typeof(conn) == "RBXScriptConnection" then
            conn:Disconnect()
        end
    end
    espConnections = {}
end

local healthESPEnabled = false

Tab_ESP:Toggle({
    Title = "Health ESP",
    Flag = "HealthESP",
    Default = false,
    Callback = function(state)
        healthESPEnabled = state
    end
})

local function NewLine(thickness, color)
    local line = Drawing.new("Line")
    line.Visible = false
    line.Color = color
    line.Thickness = thickness
    line.Transparency = 1
    return line
end

local function Visibility(state, lib)
    for _, v in pairs(lib) do
        v.Visible = state
    end
end

local HealthESPThickness = 7
local HealthESPHeightOffset = 14
local HealthESPWidth = 50

local function createHealthBar(player)
    local bg = NewLine(HealthESPThickness, Color3.fromRGB(100,100,100))
    local fill = NewLine(HealthESPThickness, Color3.fromRGB(0,255,0))

    game:GetService("RunService").RenderStepped:Connect(function()
        local char = player.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Humanoid") then
            bg.Visible = false
            fill.Visible = false
            return
        end

        local hrp = char.HumanoidRootPart
        local humanoid = char.Humanoid
        local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(hrp.Position - Vector3.new(0, HealthESPHeightOffset, 0))

        if not onScreen then
            bg.Visible = false
            fill.Visible = false
            return
        end

        local left = Vector2.new(pos.X - HealthESPWidth/2, pos.Y)
        local right = Vector2.new(pos.X + HealthESPWidth/2, pos.Y)
        bg.From = left
        bg.To = right

        local hpPercent = math.clamp(humanoid.Health / humanoid.MaxHealth, 0, 1)
        fill.From = left
        fill.To = Vector2.new(left.X + HealthESPWidth * hpPercent, left.Y)

        if hpPercent > 0.6 then
            fill.Color = Color3.fromRGB(0,255,0)
        elseif hpPercent > 0.3 then
            fill.Color = Color3.fromRGB(255,255,0)
        else
            fill.Color = Color3.fromRGB(255,0,0)
        end

        Visibility(healthESPEnabled, {bg, fill})
    end)
end

for _, plr in pairs(game:GetService("Players"):GetPlayers()) do
    if plr ~= game.Players.LocalPlayer then
        createHealthBar(plr)
    end
end

game.Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function()
        wait(1)
        createHealthBar(plr)
    end)
end)

local tracerEnabled = false
local tracerThickness = 1
local tracerColor = Color3.fromRGB(255, 255, 255)

Tab_ESP:Toggle({
    Title = "Tracer ESP",
    Flag = "TracerESP",
    Default = false,
    Callback = function(state)
        tracerEnabled = state
    end
})

local function createTracer(player)
    local line = Drawing.new("Line")
    line.Visible = false
    line.Color = tracerColor
    line.Thickness = tracerThickness
    line.Transparency = 1

    RunService.RenderStepped:Connect(function()
        if not tracerEnabled then
            line.Visible = false
            return
        end

        local char = player.Character
        if not char or not char:FindFirstChild("Humanoid") or not char:FindFirstChild("Head") then
            line.Visible = false
            return
        end

        local humanoid = char.Humanoid
        if humanoid.Health <= 0 then
            line.Visible = false
            return
        end

        local head = char.Head
        local screenPos, onScreen = Camera:WorldToViewportPoint(head.Position)
        if not onScreen then
            line.Visible = false
            return
        end

      
        local origin = Vector2.new(Camera.ViewportSize.X/2, 0)

        line.From = origin
        line.To = Vector2.new(screenPos.X, screenPos.Y)
        line.Visible = true
    end)
end

for _, plr in pairs(Players:GetPlayers()) do
    if plr ~= LocalPlayer then
        createTracer(plr)
    end
end

Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function()
        wait(1)
        createTracer(plr)
    end)
end)

local Tab_FPS = Window:Tab({
    Title = "Settings ",
    Icon = "settings",
    Locked = false,
})

Window:Divider()

Tab_FPS:Section({
    Title = "Settings:"
})

Tab_FPS:Button({
	Title= "Boost FPS",
	Callback = function()
		local Lighting = game:GetService("Lighting")
		local Terrain = workspace:FindFirstChildOfClass("Terrain")
		Lighting.GlobalShadows = false
		Lighting.FogEnd = 1e10
		Lighting.Brightness = 0
		for _, v in pairs(Lighting:GetChildren()) do
			if v:IsA("Atmosphere") or v:IsA("Sky") or v:IsA("SunRaysEffect") or v:IsA("BloomEffect") then v:Destroy() end
		end
		for _, v in pairs(workspace:GetDescendants()) do
			if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Fire") then v.Enabled = false
			elseif v:IsA("Decal") then v.Transparency = 1
			elseif v:IsA("Texture") then pcall(function() v:Destroy() end)
			elseif v:IsA("MeshPart") or v:IsA("BasePart") then v.Material = Enum.Material.SmoothPlastic v.Reflectance = 0 end
		end
		if Terrain then
			Terrain.WaterWaveSize = 0 Terrain.WaterWaveSpeed = 0 Terrain.WaterReflectance = 0 Terrain.WaterTransparency = 1
		end
	end
})

loadESP()

Tab_FPS:Button({
    Title = "Rejoin",
    Callback = function()
        local TeleportService = game:GetService("TeleportService")
        TeleportService:TeleportToPlaceInstance(
            game.PlaceId,
            game.JobId,
            game.Players.LocalPlayer
        )
    end
})

local function removeRoads(obj)
    if obj:IsA("Part") or obj:IsA("MeshPart") or obj:IsA("Model") then
        if obj.Name:lower():match("road") or obj.Name:lower():match("street") then
            obj:Destroy()
            return true
        end
    end
    if obj:IsA("Model") then
        for _, child in ipairs(obj:GetDescendants()) do
            if removeRoads(child) then
                break
            end
        end
    end
    return false
end

Tab_FPS:Button({
    Title = "Remove Roads!!",
    Callback = function()
        for _, obj in ipairs(workspace:GetDescendants()) do
            removeRoads(obj)
        end
        print("All roads removed!")
    end
})

local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local PlaceId = game.PlaceId

local function serverHop()
    local servers = {}
    local success, response = pcall(function()
        return game:HttpGet("https://games.roblox.com/v1/games/"..PlaceId.."/servers/Public?sortOrder=Asc&limit=100")
    end)

    if success then
        local data = HttpService:JSONDecode(response)
        if data and data.data then
            for _, server in ipairs(data.data) do
               
                if server.playing < server.maxPlayers then
                    table.insert(servers, server.id)
                end
            end
        end
    end

    if #servers > 0 then
       
        local serverId = servers[math.random(1, #servers)]
        print("[Server Hop] Hopping to server: "..serverId)
        TeleportService:TeleportToPlaceInstance(PlaceId, serverId, game.Players.LocalPlayer)
    else
    end
end

Tab_FPS:Button({
    Title = "Server Hop",
    Callback = function()
        serverHop()
    end
})

Tab_FPS:Button({
    Title = "Claim All Quests",
    Callback = function()
        task.spawn(function()
            local success, err = pcall(function()
                local ReplicatedStorage = game:GetService("ReplicatedStorage")
               local player = Players.LocalPlayer

                local function findCounter()
                    for _, obj in ipairs(getgc and getgc(true) or {}) do
                        if typeof(obj) == "table" and rawget(obj, "event") and rawget(obj, "func") then
                            return obj
                        end
                    end
                    return nil
                end
                
                local CounterTable = findCounter()
                if not CounterTable then 
                    warn("CounterTable ไม่พบ") 
                    return 
                end  
                local function NetGet(...)
                    local args = {...}
                    CounterTable.func = (CounterTable.func or 0) + 1
                    local GetRemote = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("Get")
                    return GetRemote:InvokeServer(CounterTable.func, table.unpack(args))
                end         
                local questFrame = player:WaitForChild("PlayerGui"):WaitForChild("Quests")
                    :WaitForChild("QuestsHolder"):WaitForChild("QuestsScrollingFrame")
                
                for _, child in ipairs(questFrame:GetChildren()) do
                    if child:IsA("Frame") or child:IsA("TextButton") or child:IsA("ImageButton") then
                        NetGet("claim_quest", child.Name)
                        task.wait(0.2)
                    end
                end
            end)
            if not success then
                warn("เกิดข้อผิดพลาด: "..tostring(err))
            end
        end)
    end
})

local showFPS = false
local fpsLabel
local fpsGui

Tab_FPS:Toggle({
    Title = "Show FPS",
    Default = false,
    Callback = function(state)
        showFPS = state

        if showFPS then
           
            if not fpsGui then
                fpsGui = Instance.new("ScreenGui")
                fpsGui.Name = "FPSGui"
                fpsGui.ResetOnSpawn = false
                fpsGui.Parent = game:GetService("CoreGui")

                fpsLabel = Instance.new("TextLabel")
                fpsLabel.Size = UDim2.new(0, 100, 0, 30)
                fpsLabel.Position = UDim2.new(1, -10, 0, 10)
                fpsLabel.AnchorPoint = Vector2.new(1, 0)
                fpsLabel.BackgroundTransparency = 1
                fpsLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                fpsLabel.TextScaled = true
                fpsLabel.Font = Enum.Font.SourceSans
                fpsLabel.FontFace = Font.new("rbxasset://fonts/families/SourceSansThin.json") -- ตัวบาง
                fpsLabel.Text = "FPS: 0"
                fpsLabel.Parent = fpsGui
            end
        else
           
            if fpsGui then
                fpsGui:Destroy()
                fpsGui = nil
                fpsLabel = nil
            end
        end
    end
})

local lastTime = tick()
local frameCount = 0
local updateInterval = 1

game:GetService("RunService").RenderStepped:Connect(function()
    if showFPS and fpsLabel then
        frameCount = frameCount + 1
        local currentTime = tick()
        if currentTime - lastTime >= updateInterval then
            local fps = frameCount / (currentTime - lastTime)
            fpsLabel.Text = "FPS: " .. math.floor(fps)
            frameCount = 0
            lastTime = currentTime
        end
    end
end)

local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PingDisplay"
screenGui.Parent = playerGui

local pingLabel = Instance.new("TextLabel")
pingLabel.Size = UDim2.new(0, 150, 0, 25)
pingLabel.Position = UDim2.new(0.5, 0, 0, 1)
pingLabel.AnchorPoint = Vector2.new(0.5, 0)
pingLabel.BackgroundTransparency = 1
pingLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
pingLabel.Font = Enum.Font.Gotham
pingLabel.TextSize = 16
pingLabel.TextStrokeTransparency = 0.7
pingLabel.Text = "Ping: ..."
pingLabel.Visible = false
pingLabel.Parent = screenGui

Tab_FPS:Toggle({
    Title = "Show Ping",
    Default = false,
    Callback = function(state)
        pingLabel.Visible = state
    end
})

RunService.RenderStepped:Connect(function()
    if pingLabel.Visible then
        local ping = math.floor(player:GetNetworkPing() * 1000)
        pingLabel.Text = "Ping: " .. ping .. " ms"
        if ping < 100 then
            pingLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
        elseif ping < 200 then
            pingLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
        else
            pingLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        end
    end
end)

local Tab_Players = Window:Tab({
    Title = "Players",
    Icon = "user",
})

Window:Divider()

local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")

LocalPlayer.CharacterAdded:Connect(function(char)
    Character = char
    HumanoidRootPart = char:WaitForChild("HumanoidRootPart")
    Humanoid = char:WaitForChild("Humanoid")
end)

Tab_Players:Section({
    Title = "Player:"
})

function respawn(plr)
    local char = plr.Character
    if not char then return end
    local faggot = gethiddenproperty(workspace, "RejectCharacterDeletions") ~= Enum.RejectCharacterDeletions.Disabled
    if faggot and replicatesignal then
        replicatesignal(plr.ConnectDiedSignalBackend)
     
        replicatesignal(plr.Kill)
    else
        local hum = char:FindFirstChildWhichIsA("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Dead) end
        char:ClearAllChildren()

        local newgen = Instance.new("Model")
        newgen.Parent = workspace
        plr.Character = newgen
        task.wait()
        plr.Character = plr.Character
        newgen:Destroy()
    end
end


Tab_Players:Button({
    Title = "Skip Spin",
    Callback = function()
        if _G.autoCratesEnabled then
            WindUI:Notify({Title="Skip Spin", Content="Already Enabled"})
            return
        end
        _G.autoCratesEnabled = true
        WindUI:Notify({Title="Skip Spin", Content="Auto Skip Spin Enabled"})

        local Players = game:GetService("Players")
        local player = Players.LocalPlayer

        local CrateModule = require(ReplicatedStorage.Modules.Game.CrateSystem.Crate)
        local Data_upvr = require(ReplicatedStorage.Modules.Core.Data)
        local crateUIRemote = ReplicatedStorage:FindFirstChild("Remotes") and ReplicatedStorage.Remotes:FindFirstChild("ShowCrateUI")

        if CrateModule.spin then
            CrateModule.spin = function(crateClass, reward, crateInstance)
                if crateClass.FinishSpin then
                    crateClass:FinishSpin(crateInstance, reward)
                else
                    crateInstance:SetAttribute("reward", reward)
                end
            end
        end

        local function AutoOpenCrate(crateInstance)
            if not crateInstance then return end

            local method = "money"
            local price = crateInstance:GetAttribute("price")
            local crateName = crateInstance.Parent.Name

            if Data_upvr.money.hand < price and Data_upvr.crate_tokens[crateName] and Data_upvr.crate_tokens[crateName] > 0 then
                method = "token"
            end

            local reward, err = Net_upvr.get("open_crate", crateInstance, method)
            if reward then
                if CrateModule.FinishSpin then
                    CrateModule.FinishSpin(crateInstance, reward)
                else
                    crateInstance:SetAttribute("reward", reward)
                end

                if crateUIRemote then
                    pcall(function()
                        crateUIRemote:InvokeServer(crateName)
                    end)
                end

                if crateInstance:FindFirstChild("Buy") and crateInstance.Buy:IsA("RemoteFunction") then
                    pcall(function()
                        crateInstance.Buy:InvokeServer()
                    end)
                end
            end
        end

        spawn(function()
            while _G.autoCratesEnabled do
                local InventoryFolder = player:WaitForChild("Inventory")
                for _, crate in ipairs(InventoryFolder:GetChildren()) do
                    if crate:GetAttribute("opened") ~= true then
                        AutoOpenCrate(crate)
                        task.wait(0.1) 
                    end
                end
                task.wait(0.3)
            end
        end)
    end
})


local flying = false
local floatPower = 40

getgenv().FlyJumpEnabled = false

Tab_Players:Toggle({
    Title = "Fly Jump",
    Flag = "FlyJump",
    Default = false,
    Callback = function(state)
        getgenv().FlyJumpEnabled = state
        flying = false
    end
})

local JumpButton
local isMobile = UserInputService.TouchEnabled

local function setupMobile()
    if not isMobile then return end

    local gui = LocalPlayer:WaitForChild("PlayerGui"):WaitForChild("TouchGui")
    local frame = gui:WaitForChild("TouchControlFrame")
    JumpButton = frame:WaitForChild("JumpButton")

    JumpButton.InputBegan:Connect(function()
        if getgenv().FlyJumpEnabled then
            flying = true
        end
    end)

    JumpButton.InputEnded:Connect(function()
        flying = false
    end)
end

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.Space and getgenv().FlyJumpEnabled then
        flying = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Space then
        flying = false
    end
end)

setupMobile()

LocalPlayer.CharacterAdded:Connect(function(char)
    Character = char
    HumanoidRootPart = char:WaitForChild("HumanoidRootPart")

    task.wait(1)
    setupMobile()
end)

RunService.RenderStepped:Connect(function()
    if getgenv().FlyJumpEnabled and flying and HumanoidRootPart then
        local hrpPos = HumanoidRootPart.Position

        local upRay = Ray.new(hrpPos, Vector3.new(0, 10, 0))
        local hitUp = workspace:FindPartOnRay(upRay, Character)

        local frontRay = Ray.new(hrpPos, HumanoidRootPart.CFrame.LookVector * 10)
        local hitFront = workspace:FindPartOnRay(frontRay, Character)

        if not hitUp and not hitFront then
            HumanoidRootPart.Velocity = Vector3.new(
                HumanoidRootPart.Velocity.X,
                floatPower,
                HumanoidRootPart.Velocity.Z
            )
        end
    end
end)

local SprintMod = require(ReplicatedStorage.Modules.Game.Sprint)
local Net = require(ReplicatedStorage.Modules.Core.Net)

local infinityStaminaEnabled = false

local orig_get = SprintMod.sprint_bar.get
local orig_set = SprintMod.sprint_bar.set
local orig_update = SprintMod.sprint_bar.update
local orig_sprinting_set = SprintMod.sprinting.set
local orig_send = Net.send

pcall(function()
    local kickF = debug.getupvalue(orig_send, 2)
    if kickF and typeof(hookfunction) == "function" then
        hookfunction(kickF, function(p12,p13,p14,p15,...) return p12(p13,p14,p15,...) end)
    end
end)

local fakeStamina = 1
local draining = true

local function fakeStopResume()
    if SprintMod.sprinting.get() then
        orig_send("set_sprinting_1", false)
        task.wait(0.02+math.random()*0.03)
        orig_send("set_sprinting_1", true)
    end
end

task.spawn(function()
    while true do
        task.wait(0.3+math.random()*0.4)
        if infinityStaminaEnabled then
            fakeStopResume()
        end
    end
end)

RunService.RenderStepped:Connect(function(dt)
    if infinityStaminaEnabled then
        if draining then
            fakeStamina -= dt*0.15
            if fakeStamina <= 0.95 then fakeStamina = 0.95 draining = false end
        else
            fakeStamina += dt*0.1
            if fakeStamina >= 1 then fakeStamina = 1 draining = true end
        end
    end
end)

Tab_Players:Toggle({
    Title = "Infinity Stamina",
    Flag = "InfinityStamina",
    Default = false,
    Callback = function(state)
        infinityStaminaEnabled = state

    
        local function findStaminaUI()
            local lp = Players.LocalPlayer
            for _, gui in pairs(lp.PlayerGui:GetDescendants()) do
              
                if gui:IsA("Frame") or gui:IsA("ImageLabel") then
                    if gui.Name:lower():match("stamina") or gui.Name:lower():match("sprint") then
                        return gui
                    end
                end
            end
        end
     
        local function setStaminaUI(visible)
            pcall(function()
                local ui = findStaminaUI()
                if ui then
                    ui.Visible = visible
                end
            end)
        end

        if infinityStaminaEnabled then
           
            setStaminaUI(false)

           
            SprintMod.sprint_bar.get = function(...) return 1 end
            SprintMod.sprint_bar.set = function(v,...) return orig_set(1, ...) end
            SprintMod.sprint_bar.update = function(fn,...) return orig_update(function() return 1 end, ...) end
            SprintMod.sprinting.set = function(value,...) return orig_sprinting_set(value,...) end

            Net.send = function(eventName,arg,...)
                if eventName == "set_sprinting_1" then
                    arg = SprintMod.sprinting.get()
                end
                return orig_send(eventName,arg,...)
            end
        else
           
            setStaminaUI(true)
            SprintMod.sprint_bar.get = orig_get
            SprintMod.sprint_bar.set = orig_set
            SprintMod.sprint_bar.update = orig_update
            SprintMod.sprinting.set = orig_sprinting_set
            Net.send = orig_send
        end
    end
})

local speedEnabled = false

Tab_Players:Toggle({
	Title = "Speed Hack",
      Flag = "SpeedHack",
	Default = false,
	Callback = function(state)
		speedEnabled = state
	end
})

local walkSpeed = 9
local runSpeed = 50
local isRunning = false

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if input.KeyCode == Enum.KeyCode.LeftShift then
		isRunning = true
	end
end)

UserInputService.InputEnded:Connect(function(input, gameProcessed)
	if input.KeyCode == Enum.KeyCode.LeftShift then
		isRunning = false
	end
end)

RunService.RenderStepped:Connect(function(dt)
	if not speedEnabled then return end
	if not (HumanoidRootPart and Humanoid and Humanoid.MoveDirection.Magnitude > 0) then return end

	local moveDir = Humanoid.MoveDirection.Unit
	local speed = isRunning and runSpeed or walkSpeed
	local distance = speed * dt

	HumanoidRootPart.CFrame = HumanoidRootPart.CFrame + (moveDir * distance)
end)
LocalPlayer.CharacterAdded:Connect(function(char)
    Character = char
    HumanoidRootPart = char:WaitForChild("HumanoidRootPart")
    Humanoid = char:WaitForChild("Humanoid")
end)

local hideNameEnabled = false

local function applyHideName(char)
    task.wait(0.2)

    local hrp = char:WaitForChild("HumanoidRootPart", 5)
    if not hrp then return end

    local gui = hrp:FindFirstChild("CharacterBillboardGui")
    if gui then
        gui.Enabled = not hideNameEnabled
    end

    hrp.ChildAdded:Connect(function(child)
        if child.Name == "CharacterBillboardGui" then
            task.wait() 
            child.Enabled = not hideNameEnabled
        end
    end)
end


Tab_Players:Toggle({
    Title = "Hide Name",
    Flag = "HideName",
    Default = false,
    Callback = function(state)
        hideNameEnabled = state

        local char = LocalPlayer.Character
        if char then
            applyHideName(char)
        end
    end
})


LocalPlayer.CharacterAdded:Connect(function(char)
    applyHideName(char)
end)

local LocalPlayer = game.Players.LocalPlayer
local HRP
LocalPlayer.CharacterAdded:Connect(function(char)
    char:WaitForChild("HumanoidRootPart")
    HRP = char.HumanoidRootPart
end)
if LocalPlayer.Character then
    HRP = LocalPlayer.Character:WaitForChild("HumanoidRootPart")
end

local PICKUP_DISTANCE = 200
local TOUCH_REPEAT = 30
local CLIENT_ZONE_SIZE = Vector3.new(120, 13, 120)

local pickupEnabled = false
local originalPositions = {}

local function firetouch(partA, partB)
    if not partA.Parent then return end
    for i = 1, TOUCH_REPEAT do
        firetouchinterest(partA, partB, 0)
        firetouchinterest(partA, partB, 1)
    end
    partA:SetAttribute("Picked", true)
end

local function setupZone(item)
    local zone = item:FindFirstChild("PickUpZone")
    if zone and zone:IsA("BasePart") then
        if not originalPositions[zone] then
            originalPositions[zone] = zone.Position
        end
        zone.Size = CLIENT_ZONE_SIZE
        zone.Transparency = 1
        zone.CanCollide = false
        zone.Anchored = true
        zone:SetAttribute("Picked", false)
    end
end

local function resetZones()
    for zone, pos in pairs(originalPositions) do
        if zone and zone.Parent then
            zone.Position = pos
            zone:SetAttribute("Picked", false)
        end
    end
end

workspace.DroppedItems.ChildAdded:Connect(function(item)
    if pickupEnabled then
        setupZone(item)
    end
end)

RunService.RenderStepped:Connect(function()
    if pickupEnabled then
        for _, item in pairs(workspace.DroppedItems:GetChildren()) do
            local zone = item:FindFirstChild("PickUpZone")
            if zone and zone:IsA("BasePart") and not zone:GetAttribute("Picked") then
                local dist = (HRP.Position - zone.Position).Magnitude
                if dist <= PICKUP_DISTANCE then
                    firetouch(zone, HRP)
                end
            end
        end
    else
        resetZones()
    end
end)

Tab_Players:Toggle({
    Title = "Pickup Item",
   Flag = "PickupItem",
    Default = false,
    Callback = function(state)
        pickupEnabled = state
        if state then
            for _, item in pairs(workspace.DroppedItems:GetChildren()) do
                setupZone(item)
            end
        end
    end
})

local player = Players.LocalPlayer
local COOLDOWN = 45

local ATMModule
local ATMUI
local ItemUtils
local Data

pcall(function()
    ATMModule = require(ReplicatedStorage.Modules.Game.ATM.ATM)
    ATMUI = require(ReplicatedStorage.Modules.Game.ATM.ATMUI)
    ItemUtils = require(ReplicatedStorage.Modules.Game.Inventory.ItemUtils)
    Data = require(ReplicatedStorage.Modules.Core.Data)
end)

if not ATMModule or not ATMUI then
    warn("ATM modules not found")
    return
end

local CounterTable
for _, obj in getgc(true) do
    if typeof(obj) == "table" and rawget(obj, "event") and rawget(obj, "func") then
        CounterTable = obj
        break
    end
end

if not CounterTable then
    warn("Network counter not found")
    return
end

local function NetSend(...)
    CounterTable.event += 1
    ReplicatedStorage.Remotes.Send:FireServer(CounterTable.event, ...)
end

local Config = {
    hackTime = 0.9,
    triggerDistance = 10
}

local State = {
    running = false,
    hacking = false,
    currentATM = nil,
    startTime = 0,
    hackedATMs = {}
}

local function getHackTool()
    for _, tool in pairs(ATMUI.valid_hack_tools) do
        local count = ItemUtils.get_item_count(Data, "misc", tool.Name)
        if count and count > 0 then
            return tool.Name
        end
    end
end

local function isHacked(atm)
    local t = State.hackedATMs[atm]
    if not t then
        return false
    end

   
    if tick() - t >= COOLDOWN then
        State.hackedATMs[atm] = nil
        return false
    end

    return true
end

local function markHacked(atm)
    State.hackedATMs[atm] = tick()
end

local function findNearbyATM()
    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    for _, atm in pairs(ATMModule.class.objects) do
        if atm and atm.instance then
            if isHacked(atm.instance) then continue end
            if atm.states.disabled:get() then continue end
            if atm.states.hacker:get() ~= nil then continue end

            local pos = atm.instance:GetPivot().Position
            local dist = (hrp.Position - pos).Magnitude

           
            if dist <= Config.triggerDistance then
                return atm.instance
            end
        end
    end
end

local function startHack(atmModel)
    if State.hacking then return end

    local tool = getHackTool()
    if not tool then return end

    State.hacking = true
    State.currentATM = atmModel
    State.startTime = tick()

    NetSend("request_begin_hacking_3", atmModel, tool)
end

local function updateHack()
    if not State.hacking then return end

    if tick() - State.startTime >= Config.hackTime then
        NetSend("atm_win_3", State.currentATM)
        markHacked(State.currentATM)

        State.hacking = false
        State.currentATM = nil
    end
end

RunService.Heartbeat:Connect(function()
    if not State.running then return end

    if State.hacking then
        updateHack()
        return
    end

    local atm = findNearbyATM()
    if atm then
        startHack(atm)
    end
end)

Tab_Players:Toggle({
    Title = "AutoHack ATM",
   Flag = "AutoHackATM",
    Default = false,
    Callback = function(Value)
        State.running = Value

        if not Value then
           
            State.hacking = false
            State.currentATM = nil
        end
    end
})

Tab_Players:Section({Title = "Anti"})

Tab_Players:Toggle({
    Title = "Anti Aim",
   Flag = "AntiAim",
    Desc = "only works when you run",
    Default = getgenv().Sky or true, 
    Callback = function(state)
        getgenv().Sky = state
    end
})

getgenv().Sky = false
getgenv().SkyAmount = 1200
 
game:GetService("RunService").heartbeat:Connect(function()
    if getgenv().Sky ~= false then 
    local vel = game.Players.LocalPlayer.Character.HumanoidRootPart.Velocity
    game.Players.LocalPlayer.Character.HumanoidRootPart.Velocity = Vector3.new(0,      getgenv().SkyAmount,0) 
    game:GetService("RunService").RenderStepped:Wait()
    game.Players.LocalPlayer.Character.HumanoidRootPart.Velocity = vel
    end 
end)

local antiKillEnabled = false

Tab_Players:Toggle({
    Title = "Anti-Kill",
   Flag = "AntiKill",
    Default = false,
    Callback = function(state)
        antiKillEnabled = state
        if not state then
            stopAntiKill()
        end
    end
})

local START_HEALTH = 15      
local STOP_HEALTH = 30        
local DEPTH = 50            
local RADIUS = 13    
local ANGULAR_SPEED = 50      
local RETURN_UP_ON_STOP = true

local antiKillActive = false
local flyConnection = nil
local centerPos = nil
local surfaceY = nil
local angle = 0

local function getCharacter()
	return LocalPlayer and LocalPlayer.Character or nil
end

local function getHRP()
	local char = getCharacter()
	return char and char:FindFirstChild("HumanoidRootPart") or nil
end

local function moveTo(pos)
	local hrp = getHRP()
	if hrp then
		hrp.CFrame = CFrame.new(pos, pos + hrp.CFrame.LookVector)
	end
end

function startAntiKill()
	local hrp = getHRP()
	if not hrp or antiKillActive then return end

	antiKillActive = true
	angle = 0
	surfaceY = hrp.Position.Y
	centerPos = Vector3.new(hrp.Position.X, hrp.Position.Y - DEPTH, hrp.Position.Z)
	moveTo(centerPos)

	flyConnection = RunService.RenderStepped:Connect(function(dt)
		if not antiKillActive then return end
		local hrpNow = getHRP()
		if not hrpNow then return end

		angle = angle + dt * ANGULAR_SPEED
		local x = math.cos(angle) * RADIUS
		local z = math.sin(angle) * RADIUS
		local targetPos = Vector3.new(centerPos.X + x, centerPos.Y, centerPos.Z + z)
		hrpNow.CFrame = CFrame.lookAt(targetPos, centerPos)
	end)

	print(string.format("[AntiKill] Activated: Depth=%d, Radius=%d, Speed=%.1f", DEPTH, RADIUS, ANGULAR_SPEED))
end

function stopAntiKill()
	if flyConnection then
		flyConnection:Disconnect()
		flyConnection = nil
	end

	if antiKillActive then
		antiKillActive = false
		print("[AntiKill] Stopped (health ≥ " .. tostring(STOP_HEALTH) .. ")")
	end

	if RETURN_UP_ON_STOP and surfaceY then
		local hrp = getHRP()
		if hrp then
			local safePos = Vector3.new(hrp.Position.X, surfaceY + 3, hrp.Position.Z)
			moveTo(safePos)
			print("[AntiKill] Returned to surface.")
		end
	end

	centerPos = nil
	surfaceY = nil
end

local function resetAntiKillState()
	if flyConnection then
		flyConnection:Disconnect()
		flyConnection = nil
	end
	antiKillActive = false
	centerPos = nil
	surfaceY = nil
	angle = 0
	print("[AntiKill] Reset on respawn")
end

local function setupWatcher(char)
	local humanoid = char:FindFirstChildOfClass("Humanoid")
	if not humanoid then return end

	humanoid.Died:Connect(function()
		resetAntiKillState()
	end)

	humanoid.HealthChanged:Connect(function(health)
		if not antiKillEnabled then return end

if health <= 0 then
        stopAntiKill()
        return
    end

		if health <= START_HEALTH and not antiKillActive then
			startAntiKill()
		elseif health >= STOP_HEALTH and antiKillActive then
			stopAntiKill()
		end
	end)
end

if LocalPlayer.Character then
	setupWatcher(LocalPlayer.Character)
end

LocalPlayer.CharacterAdded:Connect(function(char)
	resetAntiKillState()
	wait(0.5)
	setupWatcher(char)
end)

local _AntiRagdollEnabled = false

local function startAntiRagdoll()
    pcall(function()
       
       
       

       
        local function findCounter()
            for _, obj in ipairs(getgc(true)) do
                if typeof(obj) == "table" and rawget(obj, "event") and rawget(obj, "func") then
                    return obj
                end
            end
            return nil
        end

        local CounterTable = nil

       
        local function sendRemoteAction(action)
            if not CounterTable then return end
            CounterTable.event = (CounterTable.event or 0) + 1
            local SendRemote = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("Send")
            SendRemote:FireServer(CounterTable.event, action)
        end

       
        task.spawn(function()
            while true do
                if _AntiRagdollEnabled then                  
                    if not CounterTable then
                        CounterTable = findCounter()
                    end
                    if CounterTable then
                        sendRemoteAction("end_ragdoll_early")
                        task.wait(0.5)
                        sendRemoteAction("clear_ragdoll")
                        task.wait(0.5)
                    else
                        task.wait(1)
                    end
                else
                    task.wait(0.5)
                end
            end
        end)
        player.CharacterAdded:Connect(function()
            task.wait(1)
            CounterTable = findCounter()
        end)

       
        game.Loaded:Connect(function()
            task.wait(1)
            CounterTable = findCounter()
        end)

    end)
end

startAntiRagdoll()

Tab_Players:Toggle({
    Title = "Anti Ragdoll",
  Flag = "AntiRagdoll",
    Default = false,
    Callback = function(state)
        _AntiRagdollEnabled = state
    end
})

Tab_Players:Section({Title = "Misc"})

repeat
    task.wait()
until game:IsLoaded()

local LocalPlayer = Players.LocalPlayer

local Folder = Instance.new("Folder", workspace)
local Part = Instance.new("Part", Folder)
local Attachment1 = Instance.new("Attachment", Part)

Part.Anchored = true
Part.CanCollide = false
Part.Transparency = 1

if not getgenv().Network then
    getgenv().Network = {
        BaseParts = {},
        Velocity = Vector3.new(14.46262424,14.46262424,14.46262424)
    }

    Network.RetainPart = function(part)
        if part:IsA("BasePart") then
            table.insert(Network.BaseParts, part)
            part.CustomPhysicalProperties = PhysicalProperties.new(0,0,0,0,0)
            part.CanCollide = false
        end
    end

    RunService.Heartbeat:Connect(function()
        sethiddenproperty(LocalPlayer,"SimulationRadius",math.huge)

        for _, part in pairs(Network.BaseParts) do
            if part:IsDescendantOf(workspace) then
                part.Velocity = Network.Velocity
            end
        end
    end)
end

local function clearBlackhole()

    for _, part in pairs(Network.BaseParts) do

        if part and part.Parent then

            for _, obj in ipairs(part:GetChildren()) do

                if obj:IsA("AlignPosition")
                or obj:IsA("Torque")
                or obj:IsA("Attachment") then

                    obj:Destroy()

                end

            end

            part.CanCollide = true
            part.Massless = false
            part.CustomPhysicalProperties = nil

        end

    end

    Network.BaseParts = {}

end

local function isVehicle(part)

    local model = part:FindFirstAncestorOfClass("Model")
    if not model then return false end

   
    if model:FindFirstChildOfClass("VehicleSeat") then
        return true
    end
    
    if model:FindFirstChildOfClass("Seat") then
        return true
    end

   
    local name = model.Name:lower()

    if name:find("car")
    or name:find("vehicle")
    or name:find("drive")
    or name:find("truck")
    or name:find("bike") then
        return true
    end

    return false

end

local function ForcePart(v)
if isVehicle(v) then return end

    if v:IsA("BasePart")
    and not v.Anchored
    and not v.Parent:FindFirstChildOfClass("Humanoid")
and not v:FindFirstAncestorOfClass("Tool")
    and not v.Parent:FindFirstChild("Head")
    and v.Name ~= "Handle" then

        for _,x in ipairs(v:GetChildren()) do
            if x:IsA("BodyMover")
            or x:IsA("RocketPropulsion")
            or x:IsA("Torque")
            or x:IsA("AlignPosition")
            or x:IsA("Attachment") then
                x:Destroy()
            end
        end

        v.CanCollide = false

        local attachment2 = Instance.new("Attachment",v)

        local torque = Instance.new("Torque",v)
        torque.Attachment0 = attachment2
        torque.Torque = Vector3.new(100000,100000,100000)

        local align = Instance.new("AlignPosition",v)
        align.MaxForce = math.huge
        align.MaxVelocity = math.huge
        align.Responsiveness = 200
        align.Attachment0 = attachment2
        align.Attachment1 = Attachment1

        Network.RetainPart(v)
    end
end

local function getClosestPlayer()
    local closest
    local shortest = math.huge

    local myChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = myChar:WaitForChild("HumanoidRootPart")

    for _,p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local char = p.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")

            if hrp then
                local dist = (hrp.Position - myHRP.Position).Magnitude

                if dist < shortest then
                    shortest = dist
                    closest = p
                end
            end
        end
    end

    return closest
end

local currentTarget
local humanoidRootPart

RunService.Heartbeat:Connect(function()

    if not BlackholeEnabled then return end

    local target = getClosestPlayer()

    if target and target ~= currentTarget then
        currentTarget = target

        local char = target.Character or target.CharacterAdded:Wait()
        humanoidRootPart = char:WaitForChild("HumanoidRootPart")

        print("Target:",target.Name)
    end

    if humanoidRootPart then
        Attachment1.WorldCFrame = humanoidRootPart.CFrame
    end
end)

for _,v in ipairs(workspace:GetDescendants()) do
    ForcePart(v)
end

workspace.DescendantAdded:Connect(function(v)

    if BlackholeEnabled then
        ForcePart(v)
    end

end)

Tab_Players:Toggle({

    Title = "bring part",

    Default = false,

    Callback = function(state)

        BlackholeEnabled = state

        if state then

            for _,v in ipairs(workspace:GetDescendants()) do
                ForcePart(v)
            end

            print("Blackhole ON")

        else

            clearBlackhole()

            print("Blackhole OFF")

        end

    end

})

local freecamEnabled = false
local freecamSpeed = 300
local cameraCF
local oldCameraType, oldCameraSubject, oldCameraCFrame
local connection
local FLOAT_HEIGHT = 1

local function enableFreecam()
    if freecamEnabled then return end
    freecamEnabled = true

    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local root = character:WaitForChild("HumanoidRootPart")

    oldCameraType = Camera.CameraType
    oldCameraSubject = Camera.CameraSubject
    oldCameraCFrame = Camera.CFrame
    oldPlatformStand = humanoid.PlatformStand

    humanoid.PlatformStand = true
    root.Anchored = true
    root.CFrame = CFrame.new(root.Position.X, root.Position.Y + FLOAT_HEIGHT, root.Position.Z)

    Camera.CameraType = Enum.CameraType.Scriptable
    cameraCF = CFrame.new(root.Position + Vector3.new(0, FLOAT_HEIGHT, 0))

    UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
    UserInputService.MouseIconEnabled = false

    connection = RunService.RenderStepped:Connect(function(dt)
        local delta = UserInputService:GetMouseDelta()
        if delta.X ~= 0 or delta.Y ~= 0 then
            cameraCF = cameraCF * CFrame.Angles(-math.rad(delta.Y * 0.32), -math.rad(delta.X * 0.32), 0)
        end

        local moveDir = humanoid.MoveDirection
        if moveDir.Magnitude > 0 then
            local targetCF = cameraCF + (moveDir.Unit * freecamSpeed * dt)
            cameraCF = cameraCF:Lerp(targetCF, 0.5)
        end

        if humanoid:GetState() == Enum.HumanoidStateType.Jumping then
            cameraCF = cameraCF + Vector3.new(0, 110 * dt, 0)
        elseif humanoid:GetState() == Enum.HumanoidStateType.Seated then
            cameraCF = cameraCF + Vector3.new(0, -110 * dt, 0)
        end

        Camera.CFrame = cameraCF
        root.CFrame = CFrame.new(root.Position.X, root.Position.Y, root.Position.Z) * CFrame.Angles(0, select(2, Camera.CFrame:ToEulerAnglesYXZ()), 0)
    end)
end

local function disableFreecam()
    if not freecamEnabled then return end
    freecamEnabled = false

    if connection then connection:Disconnect() end

    UserInputService.MouseBehavior = Enum.MouseBehavior.Default
    UserInputService.MouseIconEnabled = true

    local character = LocalPlayer.Character
    if character then
        local humanoid = character:FindFirstChild("Humanoid")
        local root = character:FindFirstChild("HumanoidRootPart")
        if humanoid then humanoid.PlatformStand = oldPlatformStand or false end
        if root then root.Anchored = false end
    end

    Camera.CameraType = oldCameraType or Enum.CameraType.Custom
    if oldCameraSubject then Camera.CameraSubject = oldCameraSubject end
    Camera.CFrame = oldCameraCFrame or Camera.CFrame
end

local function toggleFreecam()
    if freecamEnabled then
        disableFreecam()
    else
        enableFreecam()
    end
end

Tab_Players:Toggle({
    Title = "Freecam",
    Default = false,
    Callback = function(state)
        if state then
            enableFreecam()
        else
            disableFreecam()
        end
    end
})

local undergroundEnabled = false

local targetDepth = 8     
local currentDepth = 0    
local baseY = nil

local SMOOTH_SPEED = 0.2

RunService.RenderStepped:Connect(function()
	if not undergroundEnabled then return end

	local char = LocalPlayer.Character
	if not char then return end

	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end

	
	if not baseY then
		baseY = hrp.Position.Y
		currentDepth = targetDepth
	end

	
	currentDepth = currentDepth + (targetDepth - currentDepth) * SMOOTH_SPEED

	local look = hrp.CFrame.LookVector
	local targetPos = Vector3.new(
		hrp.Position.X,
		baseY - currentDepth,
		hrp.Position.Z
	)

	hrp.CFrame = CFrame.new(targetPos, targetPos + look)
	hrp.AssemblyLinearVelocity =
		Vector3.new(hrp.AssemblyLinearVelocity.X, 0, hrp.AssemblyLinearVelocity.Z)
end)

Tab_Players:Toggle({
    Title = "snap",
    Default = false,
    Callback = function(state)
        undergroundEnabled = state
        baseY = nil
    end
})

Tab_Players:Slider({
    Title = "Depth",
    Step = 1,
    Value = {
        Min = 8,
        Max = 60,
        Default = 8,
    },
    Callback = function(value)
        targetDepth = math.abs(tonumber(value) or targetDepth)
    end
})

local itemESPEnabled = false
local ITEM_ESP_DISTANCE = 1500

Tab_ESP:Toggle({
    Title = "Item ESP",
    Flag = "ItemESP",
    Default = false,
    Callback = function(state)
        itemESPEnabled = state

        if not state then
            for item, txt in pairs(itemTexts) do
                txt:Remove()
                itemTexts[item] = nil
            end

            for item, hl in pairs(itemHighlights) do
                hl:Destroy()
                itemHighlights[item] = nil
            end
        end
    end
})

local Workspace = game:GetService("Workspace")

--================ PLAYER =================
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

--================ CONFIG =================

local itemFolder =
    Workspace:FindFirstChild("DroppedItems")
    or Workspace:FindFirstChild("Items")

if not itemFolder then
    return
end

--================ RARITY COLORS =================
local RARITY_COLORS = {
    Common     = Color3.fromRGB(180,180,180),
    Uncommon   = Color3.fromRGB(0,255,0),
    Rare       = Color3.fromRGB(0,170,255),
    Epic       = Color3.fromRGB(170,0,255),
    Legendary  = Color3.fromRGB(255,170,0),
   Omega      = Color3.fromRGB(255,0,0), 
}

local DEFAULT_COLOR = Color3.fromRGB(255,255,255)

local ITEM_RARITY_MAP = {

["anaconda"] = "Omaga",

    ["ak47"] = "Legendary",
    ["mp5"] = "Legendary",
    ["rpg"] = "Legendary",
    ["m16"] = "Legendary",
    ["crossbow"] = "Legendary",
    ["remington"] = "Legendary",
    ["tactical axe"] = "Legendary",
["tactical shovel"] = "Legendary",
["rocket"] = "Legendary",
["diamond pot"] = "Legendary",
["golden pot"] = "Legendary",
["golden tomato Seeds"] = "Legendary",


    ["double barrel"] = "Epic",
    ["energy shot"] = "Epic",
["molotov"] = "Epic",
   ["draco"] = "Epic",
    ["firework launcher"] = "Epic", 
   ["m24"] = "Epic", 
   ["sledge hammer"] = "Epic", 
["shovel"] = "Epic", 
["machette"] = "Epic", 
["combat axe"] = "Epic", 
["firework"] = "Epic", 
["grenade"] = "Epic", 
["tomato Seeds"] = "Epic", 
["premium soil"] = "Epic", 
["organic fertilizer"] = "Epic", 
["sledge hammer"] = "Epic", 

    ["p226"] = "Rare",
    ["sawnoff"] = "Rare",
    ["glock"] = "Rare",
    ["blood bag"] = "Rare",
    ["pre workout"] = "Rare",
    ["cinder block"] = "Rare",
    ["rifle ammo"] = "Rare",
    ["axe"] = "Rare",
    ["wrench"] = "Rare",
    ["metal baseballbat"] = "Rare",
   ["arrow"] = "Rare",
   ["uzi"] = "Rare",
["wrench"] = "Rare",
["switchblade"] = "Rare",
["frying pan"] = "Rare",
["butcher knife"] = "Rare",
["brainrot slap"] = "Rare",
["shotgun ammo"] = "Rare",
["crowbar"] = "Rare",
["diamond mop"] = "Rare",
["jerry can"] = "Rare",
["hunting rifle"] = "Rare",  
 ["corn seeds"] = "Rare",
["regular soil"] = "Rare",
["regular pot"] = "Rare",
["regular fetilizer"] = "Rare",
["fishingrodadvanced"] = "Rare",



    ["money"] = "Uncommon",
    ["bull energy"] = "Uncommon",
    ["pistol ammo"] = "Uncommon",
    ["baseball bat"] = "Uncommon",
    ["rusty"] = "Uncommon",
    ["c9"] = "Uncommon",
     ["g3"] = "Uncommon",
    ["nailed wooden board"] = "Uncommon",
["gold mop"] = "Uncommon",
["tire iron"] = "Uncommon",
["sunflower seeds"] = "Uncommon",
["hacktoolquantum"] = "Uncommon",
["fishingrodpro"] = "Uncommon",
["hacktoolultimate"] = "Uncommon",

}

--================ RARITY TEXT SIZE =================
local RARITY_TEXT_SIZE = {
    Common     = 5,
    Uncommon   = 10,
    Rare       = 13,
    Epic       = 17,
    Legendary  = 20,
   Omega      = 30,
}

--================ GET COLOR =================
local function getItemColor(itemName)
    local name = itemName:lower()
    for key, rarity in pairs(ITEM_RARITY_MAP) do
        if name:find(key) then
            return RARITY_COLORS[rarity] or DEFAULT_COLOR
        end
    end
    return DEFAULT_COLOR
end

--================ GET RARITY =================
local function getItemRarity(itemName)
    local name = itemName:lower()
    for key, rarity in pairs(ITEM_RARITY_MAP) do
        if name:find(key) then
            return rarity
        end
    end
    return nil
end

--================ ESP STORAGE =================
local itemTexts = {}
local itemHighlights = {}

--================ MAIN LOOP =================
RunService.RenderStepped:Connect(function()

if not itemESPEnabled then
       
        for _, txt in pairs(itemTexts) do
            txt.Visible = false
        end

       
        for _, hl in pairs(itemHighlights) do
            hl.Enabled = false
        end

        return
    end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    local rootPos = hrp.Position

    for _, item in ipairs(itemFolder:GetChildren()) do
       
        local part =
            item:IsA("BasePart") and item
            or item:IsA("Model") and item:FindFirstChildWhichIsA("BasePart")

        if part then
            local dist = (rootPos - part.Position).Magnitude
            local screenPos, onScreen = Camera:WorldToViewportPoint(part.Position)

            if dist <= ITEM_ESP_DISTANCE and onScreen then
               local rarity = getItemRarity(item.Name)
local color = rarity and RARITY_COLORS[rarity] or DEFAULT_COLOR
local textSize = rarity and RARITY_TEXT_SIZE[rarity] or 9

               
                if not itemTexts[item] then
                    local txt = Drawing.new("Text")
                    txt.Center = true
                    txt.Outline = true
                   txt.Size = textSize
                    itemTexts[item] = txt
                end

                local text = itemTexts[item]

              text.Text = item.Name
                text.Color = color
              text.Size = textSize
                text.Position = Vector2.new(screenPos.X, screenPos.Y - 15)
                text.Visible = true

               
                if not itemHighlights[item] then
                    local hl = Instance.new("Highlight")
                    hl.Parent = item
                    hl.Adornee = item  
                    hl.FillTransparency = 0.4
                    hl.OutlineTransparency = 0
                    itemHighlights[item] = hl
                end

                local hl = itemHighlights[item]
                hl.FillColor = color
                hl.OutlineColor = color
                hl.Enabled = true
            else
                if itemTexts[item] then
                    itemTexts[item].Visible = false
                end
                if itemHighlights[item] then
                    itemHighlights[item].Enabled = false
                end
            end
        end
    end

   
    for item, txt in pairs(itemTexts) do
        if not item.Parent then
            txt:Remove()
            itemTexts[item] = nil
        end
    end

    for item, hl in pairs(itemHighlights) do
        if not item.Parent then
            hl:Destroy()
            itemHighlights[item] = nil
        end
    end
end)

_G.InventoryViewerEnabled = false

if not _G.ViewerRunning then
    _G.ViewerRunning = true

    local function GetColorFromRarity(rarityName)
        local colors = {
            ["Common"] = Color3.fromRGB(255, 255, 255),
            ["UnCommon"] = Color3.fromRGB(99, 255, 52),
            ["Rare"] = Color3.fromRGB(51, 170, 255),
            ["Legendary"] = Color3.fromRGB(255, 150, 0),
            ["Epic"] = Color3.fromRGB(237, 44, 255),
            ["Omega"] = Color3.fromRGB(255, 20, 51)
        }
        return colors[rarityName] or Color3.fromRGB(255, 255, 255)
    end

    task.spawn(function()
        while task.wait(0.2) do
            if _G.InventoryViewerEnabled then
                pcall(function()
                    for _, player in pairs(Players:GetPlayers()) do
                        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                            local root = player.Character.HumanoidRootPart
                            local gui = root:FindFirstChild("ItemBillboard")

                            if not gui then
                                gui = Instance.new("BillboardGui")
                                gui.Name = "ItemBillboard"
                                gui.AlwaysOnTop = true
                                gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
                                gui.Size = UDim2.fromOffset(150, 40)
                                gui.StudsOffsetWorldSpace = Vector3.new(0, -6, 0)
                                gui.LightInfluence = 0
                                gui.MaxDistance = math.huge
                                gui.Parent = root

                                local bg = Instance.new("Frame")
                                bg.Name = "BG"
                                bg.BackgroundTransparency = 1
                                bg.Size = UDim2.fromOffset(150, 40)
                                bg.Parent = gui

                                local layout = Instance.new("UIListLayout")
                                layout.FillDirection = Enum.FillDirection.Horizontal
                                layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
                                layout.VerticalAlignment = Enum.VerticalAlignment.Center
                                layout.Padding = UDim.new(0, 4)
                                layout.Parent = bg
                            end

                            local bg = gui:FindFirstChild("BG")
                            if bg then
                                local Items = {}

                               
                                for _, child in pairs(bg:GetChildren()) do
                                    if child:IsA("Frame") then
                                        child:Destroy()
                                    end
                                end

                                for _, container in pairs({player:FindFirstChild("Backpack"), player.Character}) do
                                    if container then
                                        for _, tool in pairs(container:GetChildren()) do
                                            if tool:IsA("Tool") and not tool:GetAttribute("JobTool") and not tool:GetAttribute("Locked") then
                                                local itemFolder = tool:GetAttribute("AmmoType") and ReplicatedStorage.Items.gun or ReplicatedStorage.Items.melee

                                                for _, z in pairs(itemFolder:GetChildren()) do
                                                    if tool:GetAttribute("RarityName") == z:GetAttribute("RarityName")
                                                        and tool:GetAttribute("RarityPrice") == z:GetAttribute("RarityPrice") then

                                                        local imageId = z:GetAttribute("ImageId")
                                                        if imageId then
                                                            Items[z.Name] = true

                                                            local itemContainer = Instance.new("Frame")
                                                            itemContainer.Name = z.Name .. "_bg"
                                                            itemContainer.Size = UDim2.fromOffset(20, 20)
                                                            itemContainer.BackgroundTransparency = 1
                                                            itemContainer.BorderSizePixel = 0
                                                            itemContainer.Parent = bg

                                                            local rarityColor = GetColorFromRarity(z:GetAttribute("RarityName"))

                                                           
                                                            local iconBg = Instance.new("Frame")
                                                            iconBg.Name = "IconBg"
                                                            iconBg.Size = UDim2.fromOffset(20, 20)
                                                            iconBg.Position = UDim2.fromOffset(0, 0)
                                                            iconBg.BackgroundColor3 = Color3.fromRGB(235, 235, 235)
                                                            iconBg.BackgroundTransparency = 0.35
                                                            iconBg.BorderSizePixel = 0
                                                            iconBg.Parent = itemContainer

                                                           
                                                            local corner = Instance.new("UICorner")
                                                            corner.CornerRadius = UDim.new(0, 5)
                                                            corner.Parent = iconBg

                                                           
                                                            local stroke = Instance.new("UIStroke")
                                                            stroke.Color = rarityColor
                                                            stroke.Thickness = 1.8
                                                            stroke.Transparency = 0.05
                                                            stroke.Parent = iconBg

                                                           
                                                            local icon = Instance.new("ImageLabel")
                                                            icon.Name = z.Name
                                                            icon.Image = imageId
                                                            icon.BackgroundTransparency = 1
                                                            icon.BorderSizePixel = 0
                                                            icon.Size = UDim2.fromOffset(16, 16)
                                                            icon.Position = UDim2.fromOffset(2, 2)
                                                            icon.Parent = iconBg

                                                            local corner2 = Instance.new("UICorner")
                                                            corner2.CornerRadius = UDim.new(0, 4)
                                                            corner2.Parent = icon
                                                        end
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end

                                gui.Enabled = _G.InventoryViewerEnabled

                                for _, child in pairs(bg:GetChildren()) do
                                    if child:IsA("Frame") then
                                        local itemName = child.Name:gsub("_bg$", "")
                                        if not Items[itemName] then
                                            child:Destroy()
                                        end
                                    end
                                end
                            end
                        end
                    end
                end)
            else
                for _, player in pairs(Players:GetPlayers()) do
                    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                        local gui = player.Character.HumanoidRootPart:FindFirstChild("ItemBillboard")
                        if gui then
                            gui:Destroy()
                        end
                    end
                end
            end
        end
    end)
end

Tab_ESP:Toggle({
    Title = "Weapon Esp",
    Flag = "Weapon Esp",
    Default = false,
    Callback = function(value)
        _G.InventoryViewerEnabled = value
    end
})

local RS = game:GetService("ReplicatedStorage")

pcall(function()
    local BuyPromptUI = require(RS.Modules.Game.UI.BuyPromptUI)
    if not BuyPromptUI or not BuyPromptUI.loaded then return end

    local old_loaded = BuyPromptUI.loaded

    BuyPromptUI.loaded = function(...)
        local result = old_loaded(...)

        task.spawn(function()
            task.wait()

            local Util = require(RS.Modules.Core.Util)
            local UI = require(RS.Modules.Core.UI)
            if not Util or not Util.tween then return end

            local sellButton = UI.get("SellPromptSellButton")
            if not sellButton then return end

            local old_tween = Util.tween
            Util.tween = function(instance, tweenInfo, properties, ...)
                if instance
                    and instance:IsA("NumberValue")
                    and properties
                    and properties.Value == 1
                then
                    instance.Value = 1
                    return
                end

                return old_tween(instance, tweenInfo, properties, ...)
            end
        end)

        return result
    end
end)
pcall(function()
    local Util = require(RS.Modules.Core.Util)
    if not Util or not Util.tween then return end

    local old_tween = Util.tween

    Util.tween = function(instance, tweenInfo, properties, ...)
        if instance
            and instance:IsA("NumberValue")
            and properties
            and properties.Value == 1
        then
            instance.Value = 1
            return
        end

        return old_tween(instance, tweenInfo, properties, ...)
    end
end)

local Tab_GUNMODE = Window:Tab({
    Title = "Gunmode",
    Icon = "bolt",
    Locked = false,
})

Window:Divider()

Tab_GUNMODE:Section({
    Title = "Gun:"
})

local Automatic = false
local FireRate = 1000
local toolCache = {}

local function matchesTarget(name, target)
    if name:lower():find(target:lower()) then
        return true
    end
    if name:match("%d*_" .. target:lower() .. "_?%d*") then
        return true
    end
    return false
end

local function applyFireRate(gun)
    local prev = toolCache[gun]
    local changed = false
    if not prev or prev.FireRate ~= FireRate then
        changed = true
    end
    if not changed then return end

  
    local attrValue = gun:GetAttribute("fire_rate")
    if attrValue ~= nil then
        gun:SetAttribute("fire_rate", FireRate)
    end

    for _, child in ipairs(gun:GetDescendants()) do
        if child:IsA("NumberValue") or child:IsA("IntValue") then
            if matchesTarget(child.Name, "fire_rate") then
                child.Value = FireRate
            end
        end
    end

    toolCache[gun] = {FireRate = FireRate}
end

local function updateAllGuns()
    for _, tool in ipairs(player.Character:GetChildren()) do
        if tool:IsA("Tool") then applyFireRate(tool) end
    end
    for _, tool in ipairs(player.Backpack:GetChildren()) do
        if tool:IsA("Tool") then applyFireRate(tool) end
    end
end

player.CharacterAdded:Connect(function(char)
    if Automatic then updateAllGuns() end
    char.ChildAdded:Connect(function(child)
        if child:IsA("Tool") and Automatic then
            applyFireRate(child)
        end
    end)
end)

player.Backpack.ChildAdded:Connect(function(child)
    if child:IsA("Tool") and Automatic then
        applyFireRate(child)
    end
end)

Tab_GUNMODE:Toggle({
    Title = "Automatic",
    Default = false,
    Callback = function(state)
        Automatic = state
        if Automatic then updateAllGuns() end
    end
})

Tab_GUNMODE:Slider({
    Title = "Fire Rate",
    Value = {Min = 100, Max = 1200, Default = FireRate},
    Step = 1,
    Callback = function(value)
        FireRate = tonumber(value)
        updateAllGuns()
    end
})
local fastShootEnabled = false

Tab_GUNMODE:Toggle({
    Title = "No Recoil",
  Flag = "NoRecoil",
    Default = false,
    Callback = function(state)
        fastShootEnabled = state
    end
})

local function applyFastShoot(tool)
    if not tool:IsA("Tool") then return end
    if tool:GetAttribute("FireRate") then
        tool:SetAttribute("FireRate", 3000)
    end
    if tool:GetAttribute("Recoil") then
        tool:SetAttribute("Recoil", 0)
    end
end

local function applyFastShootToAll()
    local character = player.Character
    if not character then return end
    for _, tool in pairs(character:GetChildren()) do
        applyFastShoot(tool)
    end
end

game.Players.LocalPlayer.Character.ChildAdded:Connect(function(child)
    if fastShootEnabled then
        applyFastShoot(child)
    end
end)

RunService.RenderStepped:Connect(function()
    if fastShootEnabled then
        applyFastShootToAll()
    end
end)

local Tab_Vehicles = Window:Tab({
    Title = "Vehicles",
    Icon = "car",
    Locked = false,
})

Window:Divider()

Tab_Vehicles:Section({
    Title = "car:"
})

local vehicleFolders = {Workspace:FindFirstChild("Vehicles") or Workspace}

local noclipVehiclesEnabled = false

local function noclipVehicle(vehicle)
    if vehicle:IsA("Model") then
        for _, part in ipairs(vehicle:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end

local function collidVehicle(vehicle)
    if vehicle:IsA("Model") then
        for _, part in ipairs(vehicle:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
    end
end

Tab_Vehicles:Toggle({
    Title = "Noclip Vehicles",
  Flag = "NoclipVehicles",
    Default = false,
    Callback = function(state)
        noclipVehiclesEnabled = state

        for _, folder in ipairs(vehicleFolders) do
            for _, vehicle in ipairs(folder:GetChildren()) do
                if noclipVehiclesEnabled then
                    noclipVehicle(vehicle)
                else
                    collidVehicle(vehicle)
                end
            end
        end
    end
})

for _, folder in ipairs(vehicleFolders) do
    folder.ChildAdded:Connect(function(vehicle)
        if noclipVehiclesEnabled then
            noclipVehicle(vehicle)
        end
    end)
end

local ConfigManager = Window.ConfigManager
local myConfig = ConfigManager:CreateConfig("myConfig")

myConfig:Load()

local Tab_Config = Window:Tab({
    Title = "Config",
    Icon = "save",
    Locked = false,
})

Window:Divider()

Tab_Config:Section({Title = "Save"})

Tab_Config:Button({
    Title = "Save Config",
    Callback = function()
        myConfig:Save()
        WindUI:Notify({
            Title = "Success",
            Content = "Config save successfully!",
            Duration = 3
        })
    end
})

