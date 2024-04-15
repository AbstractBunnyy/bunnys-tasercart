# Bunnys-TaserCartridges
# Features
 • Reloading Animation with Taser Cartridges
 
 • Custom Sounds - coming soon
 
 • Removed Cooldown Indicator - coming soon
 
 • Taser Prons - coming soon
 
----------------------

# Preview
- https://streamable.com/603anr
----------------------

# Image installation
- Add the image file from the zip to your `qb-inventory > html > images` folder

----------------------

# Item installation
- Add this line to your qb-core > shared > item.lua
```lua
     ["taser_cardridge"]                      = {["name"] = "taser_cardridge",                        ["label"] = "Taser Cartridge",            ["weight"] = 3000,        ["type"] = "item",        ["image"] = "taserammo.png",              ["unique"] = false,        ["useable"] = true,        ["shouldClose"] = true,    ["combinable"] = nil,   ["description"] = "No More Spamming. lul"},  
```
----------------------

# Snippet installation

- in smallresources > client > ignore

- add to line 1 of ignore.lua
```lua
     local QBCore = exports['qb-core']:GetCoreObject()
```

- add anyware in ignore.lua
```lua
     maxTaserCarts = 2 -- The amount of taser cartridges a person can shoot until reload.

     local taserCartsLeft = maxTaserCarts
     
     RegisterNetEvent("FillTaser")
     AddEventHandler("FillTaser",function(source, args, rawCommand)
         
         QBCore.Functions.Progressbar("load_Taser", "Reloading Taser..", 2000, false, true, {
             disableMovement = false,
             disableCarMovement = false,
             disableMouse = false,
             disableCombat = true,
         }, {
             animDict = "anim@weapons@pistol@singleshot_str",
             anim = "reload_aim",
             flags = 48,
         }, {}, {}, function() -- Done
         
             
             taserCartsLeft = maxTaserCarts
         end)
     end)  
```

- add this in the same area right below
```lua
local taserModel = GetHashKey("WEAPON_STUNGUN")

CreateThread(function()
    while true do
        Wait(0)
        local ped = PlayerPedId(-1)

        if GetSelectedPedWeapon(ped) == taserModel then
            if IsPedShooting(ped) then
                DisplayAmmoThisFrame(true)
                taserCartsLeft = taserCartsLeft - 1
            end
        end

        if taserCartsLeft <= 0 then
            if GetSelectedPedWeapon(ped) == taserModel then
                SetPlayerCanDoDriveBy(ped, false)
                DisablePlayerFiring(ped, true)
                if IsControlJustReleased(0, 106) then
                    QBCore.Functions.Notify("You need to reload your taser!", "error")
                end
            end
        end

        if longerTazeTime then
            SetPedMinGroundTimeForStungun(ped, longerTazeSecTime * 1000)
        end
    end
end)

RegisterCommand('test', function()
    TriggerEvent("FillTaser")
end) 
```

- this goes in smallresoureces>server>consumables
```lua
 QBCore.Functions.CreateUseableItem("taser_cardridge", function(source, item)
    local src = source
    local Player = QBCore.Functions.GetPlayer(source)
    if not Player.Functions.RemoveItem(item.name, 1, item.slot) then return end
        TriggerClientEvent("FillTaser", source)
    end
end) 
```
