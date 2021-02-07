local enabled = ui.new_checkbox("VISUALS", "Player ESP", "Head point")
local color = ui.new_color_picker("VISUALS", "Player ESP", "Head color", 255, 255, 255, 255)
local vis_check = ui.new_checkbox("VISUALS", "Player ESP", "Head point wall detection")

local function handle_menu()
    local state = ui.get(enabled)
    ui.set_visible(vis_check, state)
end

handle_menu()
ui.set_callback(enabled, handle_menu)

local function visible(ent, skip, lx, ly, lz, x, y, z)
    local fraction, entity = client.trace_line(skip, lx, ly, lz, x, y, z)
    if entity == ent then
        return true
    end
    return false
end

client.set_event_callback("paint", function()
    if not ui.get(enabled) then
        return
    end
    local visual = ui.get(vis_check)
    local r, g, b, a = ui.get(color)
    local lp = entity.get_local_player()
    local players = entity.get_players(true)

    local lx, ly, lz = entity.get_prop(lp, "m_vecOrigin")
    lz = lz + entity.get_prop(lp, "m_vecViewOffset[2]")

    for i=1, #players do
        local x, y, z = entity.hitbox_position(players[i], 0)
        local sx, sy = renderer.world_to_screen(x, y, z)
        if sx ~= nil and sy ~= nil then
            if visual then
                if not visible(players[i], lp, lx, ly, lz, x, y, z) then
                    renderer.circle(sx, sy, r, g, b, a, 2.5, 0, 1.0)
                end
            else
                renderer.circle(sx, sy, r, g, b, a, 2.5, 0, 1.0)
            end
        end
    end
end)