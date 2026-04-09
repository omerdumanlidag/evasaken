local GITHUB_BASE = ""

local SUPPORTED_GAMES = {
    ["Evade"] = {
        script = "/games/evade/main.lua",
        placeIds = {
            10324346056,     -- Big Team
            9872472334,      -- Evade
            10662542523,     -- Casual
            10324347967,     -- Social Space
            121271605799901, -- Player Nextbots
            10808838353,     -- VC Only
            11353528705,     -- Pro
            99214917572799,  -- Custom Servers
        }
    },
    ["Evade Legacy"] = {
        script = "/games/evadelegacy/main.lua",
        placeIds = { 96537472072550 }
    },
}

local UNIVERSAL_SCRIPT = "/universal/main.lua"

local function createLoadingUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "EvasakenLoader"
    ScreenGui.DisplayOrder = 999
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.Parent = game:GetService("CoreGui")
    
    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(0, 340, 0, 160) -- Biraz genişletildi
    Frame.Position = UDim2.new(0.5, -170, 0.5, -80)
    Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    Frame.BorderSizePixel = 0

    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 10)
    Corner.Parent = Frame

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -20, 0, 30)
    Title.Position = UDim2.new(0, 15, 0, 10)
    Title.BackgroundTransparency = 1
    Title.Text = "Evasaken Loader"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 20
    Title.Font = Enum.Font.GothamBold
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Frame

    local AuthorInfo = Instance.new("TextLabel")
    AuthorInfo.Size = UDim2.new(1, -20, 0, 15)
    AuthorInfo.Position = UDim2.new(0, 15, 0, 40)
    AuthorInfo.BackgroundTransparency = 1
    AuthorInfo.Text = "Crack: Necati Ömer"
    AuthorInfo.TextColor3 = Color3.fromRGB(138, 43, 226)
    AuthorInfo.TextSize = 12
    AuthorInfo.Font = Enum.Font.GothamMedium
    AuthorInfo.TextXAlignment = Enum.TextXAlignment.Left
    AuthorInfo.Parent = Frame

    local YoutubeLink = Instance.new("TextLabel")
    YoutubeLink.Size = UDim2.new(1, -20, 0, 15)
    YoutubeLink.Position = UDim2.new(0, 15, 0, 55)
    YoutubeLink.BackgroundTransparency = 1
    YoutubeLink.Text = "YT: youtube.com/@necati.omerdumanlidag"
    YoutubeLink.TextColor3 = Color3.fromRGB(200, 50, 50)
    YoutubeLink.TextSize = 10
    YoutubeLink.Font = Enum.Font.Gotham
    YoutubeLink.TextXAlignment = Enum.TextXAlignment.Left
    YoutubeLink.Parent = Frame

    local Status = Instance.new("TextLabel")
    Status.Size = UDim2.new(1, -20, 0, 20)
    Status.Position = UDim2.new(0, 15, 0, 85)
    Status.BackgroundTransparency = 1
    Status.Text = "Başlatılıyor..."
    Status.TextColor3 = Color3.fromRGB(220, 220, 220)
    Status.TextSize = 14
    Status.Font = Enum.Font.Gotham
    Status.TextXAlignment = Enum.TextXAlignment.Left
    Status.Parent = Frame

    local ProgressBG = Instance.new("Frame")
    ProgressBG.Size = UDim2.new(1, -30, 0, 5)
    ProgressBG.Position = UDim2.new(0, 15, 0, 130)
    ProgressBG.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    ProgressBG.BorderSizePixel = 0

    local Progress = Instance.new("Frame")
    Progress.Size = UDim2.new(0, 0, 1, 0)
    Progress.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    Progress.BorderSizePixel = 0
    Progress.Parent = ProgressBG
    ProgressBG.Parent = Frame

    local function updateStatus(text) Status.Text = text end
    local function updateProgress(percent)
        Progress:TweenSize(UDim2.new(percent, 0, 1, 0), "Out", "Quad", 0.3, true)
    end
    local function close() ScreenGui:Destroy() end

    Frame.Parent = ScreenGui
    return {updateStatus = updateStatus, updateProgress = updateProgress, close = close}
end

local function fetchScript(url)
    local cacheBuster = string.format("?v=%d", tick())
    local success, result = pcall(function() return game:HttpGet(url .. cacheBuster, true) end)
    return success and result or nil
end

local function main()
    local ui = createLoadingUI()
    ui.updateStatus("Oyun taranıyor...")
    ui.updateProgress(0.3)
    task.wait(0.5)

    local currentPlaceId = game.PlaceId
    local selectedGame = "Universal"
    local scriptPath = UNIVERSAL_SCRIPT

    for gameName, gameData in pairs(SUPPORTED_GAMES) do
        for _, placeId in ipairs(gameData.placeIds) do
            if currentPlaceId == placeId then
                selectedGame = gameName
                scriptPath = gameData.script
                break
            end
        end
    end

    ui.updateStatus("Script indiriliyor: " .. selectedGame)
    ui.updateProgress(0.6)
    
    local scriptContent = fetchScript(GITHUB_BASE .. scriptPath)

    if not scriptContent then
        ui.updateStatus("❌ İndirme başarısız!")
        task.wait(3)
        ui.close()
        return
    end

    ui.updateStatus("✅ Evasaken Yükleniyor...")
    ui.updateProgress(1)
    task.wait(1)

    local success, err = pcall(function() loadstring(scriptContent)() end)

    if success then
        ui.close()
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Evasaken",
            Text = "Hoş geldin! Script başarıyla yüklendi.",
            Duration = 5
        })
    else
        ui.updateStatus("❌ Hata oluştu!")
        warn("Evasaken Error: " .. tostring(err))
        task.wait(5)
        ui.close()
    end
end

main()

