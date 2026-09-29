## Referensi Export & API

`osm-target` menyediakan dua paradigma interaksi:
1. **Targeting Standar (Mode ALT)**: Targeting raycast tradisional di mana pemain menahan tombol `ALT`, mengarahkan kursor ke entity/zone, lalu mengklik opsi. 100% kompatibel dan bisa langsung menggantikan (`drop-in`) `ox_target`.
2. **Proximity Prompt 2 Tahap (Mode [E])**: Interaksi proximity diegetik yang terinspirasi dari NoPixel 4.0. Menampilkan indikator berlian melayang dari jarak jauh (~3m) yang akan berubah secara halus menjadi tampilan prompt dengan tombol `[E]` saat pemain mendekat (~1.5m).

Kamu bisa memanggil export menggunakan `exports['osm-target']`, `exports.osm_target`, atau `exports.ox_target`.

---

### 1. API Proximity Prompt 2 Tahap (`addPrompt...`)

Interaksi proximity terpicu secara otomatis saat pemain mendekat tanpa perlu menekan tombol `ALT`.

#### Proximity Zones
```lua
-- Box Zone dengan prompt proximity
local zoneId = exports['osm-target']:addPromptBoxZone({
    coords = vec3(441.8, -982.5, 30.7),
    size = vec3(1.5, 1.5, 2.0),
    rotation = 45.0,
    distance = 1.5,           -- Jarak dalam meter untuk membuka prompt [E] (default: 1.5)
    indicatorDistance = 3.0,  -- Jarak dalam meter di mana indikator berlian muncul (default: distance + 1.5)
    key = 38,                 -- Control ID (default: 38 / INPUT_PICKUP)
    keyLabel = 'E',           -- Teks label tombol yang tampil di layar (default: 'E')
    options = {
        {
            name = 'bank_teller',
            label = 'Bicara dengan Teller',
            icon = 'user-tie',
            onSelect = function(data)
                print('Berinteraksi dengan teller di zone:', data.zone)
            end
        }
    }
})

-- Menghapus proximity zone
exports['osm-target']:removePromptZone(zoneId)
```

```lua
-- Sphere Zone dengan prompt proximity
local sphereId = exports['osm-target']:addPromptSphereZone({
    coords = vec3(441.8, -982.5, 30.7),
    radius = 2.0,
    distance = 1.5,
    indicatorDistance = 3.5,
    keyLabel = 'E',
    options = { ... }
})

-- Polygon Zone dengan prompt proximity
local polyId = exports['osm-target']:addPromptPolyZone({
    points = { vec3(x1, y1, z1), vec3(x2, y2, z2), vec3(x3, y3, z3) },
    thickness = 2.5,
    distance = 1.5,
    options = { ... }
})
```

#### Proximity Entities & Models
```lua
-- Entity Lokal (Ped / Prop / Objek di sisi client)
exports['osm-target']:addPromptLocalEntity(pedEntity, {
    distance = 1.5,
    indicatorDistance = 3.0,
    key = 38,
    keyLabel = 'E',
    options = {
        {
            name = 'bus_terminal_lobby',
            label = 'Buka Lobby Terminal Bus',
            icon = 'bus',
            onSelect = function(data)
                print('Berinteraksi dengan entity:', data.entity)
            end,
        },
        {
            name = 'work_schedule',
            label = 'Cek Jadwal Kerja',
            icon = 'clipboard',
            onSelect = function(data)
                TriggerEvent('bus:openSchedule')
            end,
        }
    }
})

-- Menghapus prompt dari entity lokal
exports['osm-target']:removePromptLocalEntity(pedEntity)

-- Networked Entity (berdasarkan Network ID)
exports['osm-target']:addPromptEntity(netId, { distance = 1.5, indicatorDistance = 3.0, options = { ... } })
exports['osm-target']:removePromptEntity(netId)

-- Model (Hash Prop / Objek Spesifik)
exports['osm-target']:addPromptModel({ `prop_atm_01`, `prop_atm_02` }, {
    distance = 1.2,
    indicatorDistance = 2.5,
    keyLabel = 'E',
    options = {
        {
            name = 'access_atm',
            label = 'Gunakan ATM',
            icon = 'credit-card',
            onSelect = function(data)
                TriggerEvent('bank:openATM')
            end
        }
    }
})

-- Menghapus prompt dari model
exports['osm-target']:removePromptModel({ `prop_atm_01`, `prop_atm_02` })
```

#### Proximity Globals
```lua
-- Prompt interaksi global yang diterapkan ke semua kelas yang cocok di jarak dekat
exports['osm-target']:addPromptGlobalOption({ distance = 1.5, indicatorDistance = 3.0, options = { ... } })
exports['osm-target']:addPromptGlobalPed({ distance = 1.5, indicatorDistance = 3.0, options = { ... } })
exports['osm-target']:addPromptGlobalVehicle({ distance = 1.8, indicatorDistance = 3.5, options = { ... } })
exports['osm-target']:addPromptGlobalObject({ distance = 1.2, indicatorDistance = 2.5, options = { ... } })
exports['osm-target']:addPromptGlobalPlayer({ distance = 1.5, indicatorDistance = 3.0, options = { ... } })
```

> **Catatan Alias Key String:** Kamu juga bisa memanggil export dengan awalan prefix namespace `3dText:` (contoh: `exports['osm-target']['3dText:addBoxZone'](...)` atau `exports['osm-target']['3dText:addLocalEntity'](...)`).

---

### 2. API Targeting Standar (Mode Raycast ALT)

Interaksi standar diaktifkan saat pemain menahan tombol targeting (default `Left Alt`) dan mengarahkan pandangan ke entity atau zone.

#### Targeting Entity & Model
```lua
-- Mendaftarkan opsi pada handle entity sisi client
exports['osm-target']:addLocalEntity(entity, options)
exports['osm-target']:removeLocalEntity(entity, optionNames?)

-- Mendaftarkan opsi pada networked entity menggunakan Network ID
exports['osm-target']:addEntity(netId, options)
exports['osm-target']:removeEntity(netId, optionNames?)

-- Mendaftarkan opsi pada hash model
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

-- Menghapus zone
exports['osm-target']:removeZone(zoneId)

-- Mengecek apakah zone ada
local exists = exports['osm-target']:zoneExists(zoneId) -- mengembalikan nilai boolean (true/false)
```

#### Kelas Target Global
```lua
exports['osm-target']:addGlobalPed(options)        exports['osm-target']:removeGlobalPed(optionNames?)
exports['osm-target']:addGlobalVehicle(options)    exports['osm-target']:removeGlobalVehicle(optionNames?)
exports['osm-target']:addGlobalObject(options)     exports['osm-target']:removeGlobalObject(optionNames?)
exports['osm-target']:addGlobalPlayer(options)     exports['osm-target']:removeGlobalPlayer(optionNames?)
exports['osm-target']:addGlobalOption(options)     exports['osm-target']:removeGlobalOption(optionNames?)
```

#### Kontrol Status Targeting
```lua
-- Menonaktifkan atau mengaktifkan status targeting secara keseluruhan
exports['osm-target']:disableTargeting(true) -- menonaktifkan targeting

-- Mengecek apakah tampilan/antarmuka targeting sedang terbuka
local isTargeting = exports['osm-target']:isActive() -- mengembalikan nilai boolean (true/false)
```

---

### 3. Struktur Konfigurasi Opsi (`options`)

Setiap entri dalam array `options` dapat menerima atribut berikut:

| Field | Tipe Data | Keterangan |
| :--- | :--- | :--- |
| `name` | `string` | Pengenal unik untuk opsi ini. Wajib diisi jika ingin memperbarui atau menghapus opsi secara selektif. |
| `label` | `string` | Teks utama yang ditampilkan pada baris menu (contoh: `"Geledah Tempat Sampah"`). |
| `description` | `string?` | Teks penjelasan opsional atau pemanis. |
| `icon` | `string?` | Pengenal ikon atau nama ikon FontAwesome (contoh: `'hand'`, `'box'`, `'clipboard'`). |
| `iconColor` | `string?` | Warna Hex atau string warna CSS untuk ikon. |
| `distance` | `number?` | Jarak interaksi maksimum untuk opsi ini dalam meter. |
| `groups` | `table?` | Tabel akses pekerjaan/gang (contoh: `{ ['police'] = 0, ['sheriff'] = 2 }` atau `{ 'police', 'ambulance' }`). |
| `items` | `table?` | Item inventaris yang diperlukan (contoh: `'lockpick'` atau `{ ['lockpick'] = 1 }`). |
| `canInteract` | `function?` | Fungsi `(entity, distance, coords, name, bone) -> boolean, string?`. Kembalikan `false, "Alasan"` untuk menampilkan baris nonaktif (disabled) beserta pesan penjelasan di tooltip! |
| `onSelect` | `function?` | Callback client yang dieksekusi saat opsi dipilih: `function(response)`. |
| `event` | `string?` | Event client yang dipicu saat diklik: `TriggerEvent(event, response)`. |
| `serverEvent` | `string?` | Event server yang dipicu saat diklik: `TriggerServerEvent(serverEvent, response)`. |
| `command` | `string?` | Command client yang dieksekusi saat diklik. |

#### Data Callback (`onSelect` / `event` response payload)
Saat sebuah opsi diklik, callback akan menerima tabel yang berisi:
```lua
{
    name = 'option_name',
    label = 'Option Label',
    entity = 12345,         -- Handle entity (atau NetID untuk event server)
    coords = vec3(x, y, z), -- Koordinat 3D dari target
    distance = 1.42,        -- Jarak dari pemain ke target
    zone = 1                -- ID Zone jika berada di dalam zone, atau nil
}
```
