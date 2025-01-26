local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/miroeramaa/TurtleLib/main/TurtleUiLib.lua"))()
local window = library:Window("Nebula (Hide or die)", {Closeable = false}) -- GUI kann nicht geschlossen werden

-- Positionen für Hide, Autofarm (Coins), Parkour und die Stop-Position
local hidePosition = Vector3.new(-75014.2734375, 450600.0625, -1179.135498046875)
local autofarmPosition = Vector3.new(-29646.474609375, 2241.434814453125, -13384.462890625) -- Neue Autofarm-Position
local parkourPosition = Vector3.new(-4724.70361328125, 191.2919464111328, -4554.056640625) -- Parkour-Position
local stopPosition = Vector3.new(-4771.08203125, 165.11045837402344, -4329.64892578125) -- Neue Stop-Position für Autofarm
local chooseMapPosition = Vector3.new(-4661.45849609375, 153.90882873535156, -4330.8857421875)

-- Statusvariablen
local isAutofarmActive = false
local isAntiAfkActive = false

-- Button für Parkour
window:Button("Parkour", function()
    local character = game.Players.LocalPlayer.Character
    if character and character.PrimaryPart then
        character:SetPrimaryPartCFrame(CFrame.new(parkourPosition)) -- Neue Parkour-Position
    else
        warn("Character oder PrimaryPart nicht verfügbar!")
    end
end)

-- Toggle-Button für Autofarm (Coins) (hin und her teleportieren)
window:Toggle("Autofarm (Coins)", false, function(state)
    isAutofarmActive = state -- Setze den Status von Autofarm

    local character = game.Players.LocalPlayer.Character
    if character and character.PrimaryPart then
        if isAutofarmActive then
            -- Wenn Autofarm aktiviert wird, beginne mit der Teleportation
            task.spawn(function()
                while isAutofarmActive do
                    character:SetPrimaryPartCFrame(CFrame.new(hidePosition))
                    wait(0.1)
                    character:SetPrimaryPartCFrame(CFrame.new(autofarmPosition)) -- Neue Autofarm-Position
                    wait(0.1)
                end
            end)
        else
            -- Sofortige Teleportation zur Stop-Position, wenn Autofarm deaktiviert wird
            character:SetPrimaryPartCFrame(CFrame.new(stopPosition)) -- Neue Stop-Position
        end
    else
        warn("Character oder PrimaryPart nicht verfügbar!")
    end
end)

-- Toggle-Button für Anti AFK (Mausklick in der Bildschirmmitte)
window:Toggle("Anti AFK", false, function(state)
    isAntiAfkActive = state -- Setze den Status von Anti AFK

    if isAntiAfkActive then
        task.spawn(function()
            while isAntiAfkActive do
                wait(600) -- 10 Minuten warten
                local UIS = game:GetService("UserInputService")
                local viewportSize = workspace.CurrentCamera.ViewportSize
                local middleScreen = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2)

                -- Simuliere den Mausklick
                UIS.InputBegan:Fire({
                    KeyCode = Enum.UserInputType.MouseButton1,
                    Position = middleScreen,
                })

                wait(0.1) -- Kleiner Delay zwischen Klick
                UIS.InputEnded:Fire({
                    KeyCode = Enum.UserInputType.MouseButton1,
                    Position = middleScreen,
                })
            end
        end)
    end
end)

-- Button für Choose Map (Teleportiere den Spieler direkt zur Position)
window:Button("Choose Map", function()
    local character = game.Players.LocalPlayer.Character
    if character and character.PrimaryPart then
        -- Teleportiere den Spieler direkt zur Zielposition
        character:SetPrimaryPartCFrame(CFrame.new(chooseMapPosition))
    else
        warn("Character oder PrimaryPart nicht verfügbar!")
    end
end)

-- Keybind für GUI-Toggle
local isVisible = true
library:Keybind("ü", function()
    isVisible = not isVisible
    window:SetVisible(isVisible) -- GUI sichtbar/unsichtbar machen
end)
