# Automated Buildings

Three new passive production modules for [Space Haven](https://store.steampowered.com/app/979110/). Drop materials in, output comes out — no crew operator needed. Logistic bots haul I/O.

## Modules

| Module | mid | Output | Input (vanilla recipe) | Pattern cloned from |
|---|---|---|---|---|
| **Auto-Builder** | 7800001 | Build Tools | recipe 1446 (element 162) | Tool Generator (mid 1448) |
| **Auto-Fab** | 7800002 | Refined materials | recipe 1934 (weaver inputs) | Weaver (mid 1959) |
| **Auto-Greenhouse** | 7800003 | Algae food | recipe 2651 (water + bio matter) | Algae Kitchen (mid 2643) |

All three run the same way: place the module, connect it to the power grid and a logistics network, and it runs `<produces>` with `<stateWatchdog autoproduce="true"/>` — exactly the mechanism the vanilla Algae Kitchen, Tool Generator, and CO2 Scrubber already use. No Java, no AI, no AspectJ.

Each module is **2× the throughput** of its vanilla counterpart and uses Industry-tier power (so they're noticeable on your grid budget but always reliable).

## Requirements

- **Space Haven** v1.0.2
- **[spacehaven-modloader](https://github.com/Spacehaven-modding-tools/spacehaven-modloader)** v0.12.1 or newer

## Install

Clone into the game's mods folder, or junction your dev workspace in:

```powershell
cd "<SpaceHaven>\mods"
git clone https://github.com/mingchen3563/automated_buildings.git
```

Or for development:

```powershell
git clone https://github.com/mingchen3563/automated_buildings.git C:\path\to\workspace
cmd /c 'mklink /J "<SpaceHaven>\mods\automated_buildings" "C:\path\to\workspace"'
```

Then launch `spacehaven-modloader`, enable **Automated Buildings**, and apply.

> ⚠ **Do not put the mod folder under the Steam Workshop directory** (`steamapps/workshop/content/979110/`). Steam re-validates that folder and deletes anything that isn't a registered workshop item.

## Where they appear in the build menu

- Auto-Builder → **RESOURCE** subcategory
- Auto-Fab → **RESOURCE** subcategory
- Auto-Greenhouse → **FOOD** subcategory

## How it works

The vanilla `<produces>` + `<stateWatchdog autoproduce="true"/>` combo is what makes a station run without a crew operator. The output rate is set by `valuePerSec` on the `<produces>` entry. Logistic bots will haul `<needs>` items into the station's input hatch and pull produced output items out — same mechanic that fills any vanilla auto-producer.

```xml
<features addFacilityIcons="false" produceInNormal="true" inUseState="true">
    <produces>
        <l valuePerSec="20" product="1446" basicPowerUsage="2.0"
           useHighCapPowerPerSec="10.0" powerCategory="Industry" suction="false"/>
    </produces>
    <stateWatchdog autoproduce="true"/>
</features>
```

`product="..."` is the eid of a vanilla `<product type="Process">` recipe in `library/haven`. The recipe defines what's consumed (`<needs>`) and what's produced (`<products>`). For v0.1.0 we reuse existing vanilla recipes; future versions will add custom recipes if we want non-vanilla outputs.

## Known limitations (v0.1.0)

- **Names and icons are reused from the vanilla template stations.** Auto-Builder shows up as "Tool Generator" in the UI; Auto-Fab as "Weaver"; Auto-Greenhouse as "Algae Kitchen". Future versions will add distinct names, descriptions, and icons via `library/texts_automated_buildings.xml`.
- **Sprites are reused too.** The modules look identical to their vanilla counterparts.
- **Auto-Fab uses the Weaver's recipe list,** which is normally crew-interactive. `autoproduce="true"` should make it run anyway (vanilla Algae Kitchen does the same), but if it doesn't trigger we'll add a non-interactive recipe variant.
- **Phase 2 candidates:** distinct sprites + names, configurable per-instance output (so one Auto-Fab makes Electronics and another makes Materials), in-game research gating.

## Project layout

```
automated_buildings/
├── info.xml
├── library/
│   └── haven_automated_buildings.xml
└── README.md
```

## License

MIT.

## Credits

- [spacehaven-modloader](https://github.com/Spacehaven-modding-tools/spacehaven-modloader) team for the mod system.
- Bugbyte for Space Haven.
