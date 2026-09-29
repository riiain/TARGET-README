## Exports & API Reference

`osm-target` provides two interaction paradigms:
1. **Standard Targeting (ALT Mode)**: Traditional raycast targeting where players hold `ALT`, aim at an entity/zone, and click options. 100% drop-in compatible with `ox_target`.
2. **2-Stage Proximity Prompts ([E] Mode)**: Diegetic proximity interactions inspired by NoPixel 4.0. Displays a floating animated diamond indicator at distance (~3m) that seamlessly morphs into a Vertical Rail prompt with key `[E]` as players step closer (~1.5m).

You can call exports using `exports['osm-target']`, `exports.osm_target`, or `exports.ox_target`.

---

### 1. 2-Stage Proximity Prompt API (`addPrompt...`)

Proximity interactions trigger automatically when players approach without holding `ALT`.

#### Proximity Zones
```lua
-- Box Zone with proximity prompt
local zoneId = exports['osm-target']:addPromptBoxZone({
    coords = vec3(441.8, -982.5, 30.7),
    size = vec3(1.5, 1.5, 2.0),
    rotation = 45.0,
    distance = 1.5,           -- Distance in meters to open [E] prompt (default: 1.5)
    indicatorDistance = 3.0,  -- Distance in meters where diamond indicator appears (default: distance + 1.5)
    key = 38,                 -- Control ID (default: 38 / INPUT_PICKUP)
    keyLabel = 'E',           -- Display badge key label (default: 'E')
    options = {
        {
            name = 'bank_teller',
            label = 'Talk to Teller',
            icon = 'user-tie',
            onSelect = function(data)
                print('Interacted with teller at zone:', data.zone)
            end
        }
    }
})

-- Remove proximity zone
exports['osm-target']:removePromptZone(zoneId)
```

```lua
-- Sphere Zone with proximity prompt
local sphereId = exports['osm-target']:addPromptSphereZone({
    coords = vec3(441.8, -982.5, 30.7),
    radius = 2.0,
    distance = 1.5,
    indicatorDistance = 3.5,
    keyLabel = 'E',
    options = { ... }
})

-- Polygon Zone with proximity prompt
local polyId = exports['osm-target']:addPromptPolyZone({
    points = { vec3(x1, y1, z1), vec3(x2, y2, z2), vec3(x3, y3, z3) },
    thickness = 2.5,
    distance = 1.5,
    options = { ... }
})
```

#### Proximity Entities & Models
```lua
-- Local Entity (Client-side Ped / Prop / Object)
exports['osm-target']:addPromptLocalEntity(pedEntity, {
    distance = 1.5,
    indicatorDistance = 3.0,
    key = 38,
    keyLabel = 'E',
    options = {
        {
            name = 'bus_terminal_lobby',
            label = 'Open Bus Terminal Lobby',
            icon = 'bus',
            onSelect = function(data)
                print('Interacted with entity:', data.entity)
            end,
        },
        {
            name = 'work_schedule',
            label = 'Check Working Schedule',
            icon = 'clipboard',
            onSelect = function(data)
                TriggerEvent('bus:openSchedule')
            end,
        }
    }
})

-- Remove prompt from local entity
exports['osm-target']:removePromptLocalEntity(pedEntity)

-- Networked Entity (by Network ID)
exports['osm-target']:addPromptEntity(netId, { distance = 1.5, indicatorDistance = 3.0, options = { ... } })
exports['osm-target']:removePromptEntity(netId)

-- Models (Specific Prop / Object Hashes)
exports['osm-target']:addPromptModel({ `prop_atm_01`, `prop_atm_02` }, {
    distance = 1.2,
    indicatorDistance = 2.5,
    keyLabel = 'E',
    options = {
        {
            name = 'access_atm',
            label = 'Use ATM',
            icon = 'credit-card',
            onSelect = function(data)
                TriggerEvent('bank:openATM')
            end
        }
    }
})

-- Remove prompt from models
exports['osm-target']:removePromptModel({ `prop_atm_01`, `prop_atm_02` })
```

#### Proximity Globals
```lua
-- Global interaction prompt applied to all matching classes within proximity
exports['osm-target']:addPromptGlobalOption({ distance = 1.5, indicatorDistance = 3.0, options = { ... } })
exports['osm-target']:addPromptGlobalPed({ distance = 1.5, indicatorDistance = 3.0, options = { ... } })
exports['osm-target']:addPromptGlobalVehicle({ distance = 1.8, indicatorDistance = 3.5, options = { ... } })
exports['osm-target']:addPromptGlobalObject({ distance = 1.2, indicatorDistance = 2.5, options = { ... } })
exports['osm-target']:addPromptGlobalPlayer({ distance = 1.5, indicatorDistance = 3.0, options = { ... } })
```

> **Note on String-Keyed Aliases:** You can also call exports with the `3dText:` namespace prefix (e.g. `exports['osm-target']['3dText:addBoxZone'](...)` or `exports['osm-target']['3dText:addLocalEntity'](...)`).

---

### 2. Standard Targeting API (ALT Raycast Mode)

Standard interactions are activated when players hold the targeting key (default `Left Alt`) and look at entities or zones.

#### Entity & Model Targeting
```lua
-- Register options on a client-side entity handle
exports['osm-target']:addLocalEntity(entity, options)
exports['osm-target']:removeLocalEntity(entity, optionNames?)

-- Register options on a networked entity by Network ID
exports['osm-target']:addEntity(netId, options)
exports['osm-target']:removeEntity(netId, optionNames?)

-- Register options on model hashes
exports['osm-target']:addModel({ `prop_vend_soda_01`, `prop_vend_soda_02` }, options)
exports['osm-target']:removeModel({ `prop_vend_soda_01`, `prop_vend_soda_02` }, optionNames?)
```

#### Target Zones
```lua
-- Box Zone
local zoneId = exports['osm-target']:addBoxZone({
    coords = vec3(x, y, z),
    size = vec3(2.0, 2.0, 2.0),
    rotation = 45.0,
    debug = false,
    options = options
})

-- Sphere Zone
local sphereId = exports['osm-target']:addSphereZone({
    coords = vec3(x, y, z),
    radius = 1.5,
    options = options
})

-- Poly Zone
local polyId = exports['osm-target']:addPolyZone({
    points = { vec3(...), vec3(...), vec3(...) },
    thickness = 3.0,
    options = options
})

-- Remove zone
exports['osm-target']:removeZone(zoneId)

-- Check if zone exists
local exists = exports['osm-target']:zoneExists(zoneId) -- returns boolean
```

#### Global Target Classes
```lua
exports['osm-target']:addGlobalPed(options)        exports['osm-target']:removeGlobalPed(optionNames?)
exports['osm-target']:addGlobalVehicle(options)    exports['osm-target']:removeGlobalVehicle(optionNames?)
exports['osm-target']:addGlobalObject(options)     exports['osm-target']:removeGlobalObject(optionNames?)
exports['osm-target']:addGlobalPlayer(options)     exports['osm-target']:removeGlobalPlayer(optionNames?)
exports['osm-target']:addGlobalOption(options)     exports['osm-target']:removeGlobalOption(optionNames?)
```

#### Targeting State Controls
```lua
-- Toggle targeting state completely
exports['osm-target']:disableTargeting(true) -- disables targeting

-- Query if the targeting interface is currently open
local isTargeting = exports['osm-target']:isActive() -- returns boolean
```

---

### 3. Option Configuration Structure

Each entry in an `options` array accepts the following attributes:

| Field | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | Unique identifier for the option. Required for updating or selective removal. |
| `label` | `string` | The primary text displayed on the menu row (e.g. `"Search Trash"`). |
| `description` | `string?` | Optional explanatory or flavor text. |
| `icon` | `string?` | Icon identifier or FontAwesome icon name (e.g. `'hand'`, `'box'`, `'clipboard'`). |
| `iconColor` | `string?` | Hex or CSS color string for the icon. |
| `distance` | `number?` | Maximum interaction distance for this option in meters. |
| `groups` | `table?` | Job/gang access table (e.g. `{ ['police'] = 0, ['sheriff'] = 2 }` or `{ 'police', 'ambulance' }`). |
| `items` | `table?` | Required inventory item(s) (e.g. `'lockpick'` or `{ ['lockpick'] = 1 }`). |
| `canInteract` | `function?` | Function `(entity, distance, coords, name, bone) -> boolean, string?`. Return `false, "Reason"` to show a disabled row with a tooltip explanation! |
| `onSelect` | `function?` | Client callback executed when selected: `function(response)`. |
| `event` | `string?` | Client event triggered on click: `TriggerEvent(event, response)`. |
| `serverEvent` | `string?` | Server event triggered on click: `TriggerServerEvent(serverEvent, response)`. |
| `command` | `string?` | Client command executed on click. |

#### Response Payload (`onSelect` / `event` callback data)
When an option is clicked, the callback receives a table containing:
```lua
{
    name = 'option_name',
    label = 'Option Label',
    entity = 12345,         -- Entity handle (or NetID for server events)
    coords = vec3(x, y, z), -- 3D coordinates of the target
    distance = 1.42,        -- Distance from player to target
    zone = 1                -- Zone ID if inside a zone, or nil
}
```

---
