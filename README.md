THIS DONT WORK ON VERSION OF BEAMNG DRIVE - 0.36 and later 
Why the Previous Method No Longer Works

In earlier versions, modifying the global environment using getfenv allowed for interception of functions like deactivateMod. However, with the updates in BeamNG 0.36 and BeamMP, the Lua environment has been secured, and such modifications are restricted to prevent potential exploits and ensure game stability.

-- Attempt to hook mod deactivation safely
local success, cm = pcall(function() return rawget(_G, "core_modmanager") end)
if success and cm then
    local originalDeactivate = cm.deactivateMod
    cm.deactivateMod = function(name, ...)
        -- Only block if called from MPModManager.lua
        local info = debug.getinfo(2, "S")
        if info and info.short_src and info.short_src:find("MPModManager.lua") then
            print("[Hook] BeamMP tried to deactivate mod: " .. name .. " (blocked)")
            return -- skip deactivation
        end
        return originalDeactivate(name, ...)
    end
    print("[Hook] Mod deactivation hook installed")
else
    print("[Hook] core_modmanager not found")
end

