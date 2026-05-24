# Automated Buildings

Three new passive production modules for [Space Haven](https://store.steampowered.com/app/979110/). Drop materials in, output comes out — no crew operator needed. Logistic bots haul I/O.

## Modules (v0.3.0)

| Module | mid | Output | Recipe | Footprint | Cloned from |
|---|---|---|---|---|---|
| **Auto Optronics Fabricator** | 7800001 | Optronic components / Energy Cells | recipe 1939 (vanilla) | 4×1 + 1×1 | Optronics Fabricator (mid 1989) |
| **Auto Advanced Assembler** | 7800002 | Tech blocks / Energy blocks | recipe 1937 (vanilla) | 3×1 | Advanced Assembler (mid 2002) |
| **Auto Grow Bed** | 7800003 | Root vegetables, fruits, grains, nuts | recipe 7800200 (new, multi-output) | 1×1 | Grow Bed (mid 160) |

Each module is the fully-automated variant of the vanilla counterpart: same recipe, same sprite, same shape — but the functional inner element has `<stateWatchdog autoproduce="true"/>` and `produceInNormal="true"`. The dock runs without a crew operator. Logistic bots haul `<needs>` items in and `<products>` items out automatically.

Tradeoff: a higher continuous power draw than the manual versions (`basicPowerUsage` and high-cap power roughly doubled) so the convenience costs grid budget.

## Localization

Each module ships with new text IDs (7800101-7800106) under the vanilla "facility names" category (`pid="121"`). Names and descriptions are localized in EN + CN (Simplified, matching the vanilla CN entries). Names also have JA / KO / DE / ES / FR / IT / PL / CS / PTBR / RU / TR; missing translations fall back to EN.

Vanilla supports 13 languages: EN, ES, DE, PL, KO, IT, CN, FR, CS, PTBR, RU, JA, TR. (No Traditional Chinese — the game's "CN" is Simplified.)

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

- Auto Optronics Fabricator → **RESOURCE** subcategory
- Auto Advanced Assembler → **RESOURCE** subcategory
- Auto Grow Bed → **FOOD** subcategory

## How it works

Each module is a multi-tile composite (or single tile, for Auto Greenhouse) cloning a vanilla facility. The visual sub-tiles are reused as-is from vanilla. Only the **functional inner tile** is replaced with a new mid (7800010 for Optronics, 7800011 for Adv Assembler) which differs from vanilla only in two flags:

```xml
<features ... produceInNormal="true" ...>
    <produces>
        <l valuePerSec="10" product="1939" basicPowerUsage="2.0" ... />
    </produces>
    <stateWatchdog autoproduce="true"/>
</features>
```

`produceInNormal="true"` lets the facility run in the Standby state (i.e. with no crew operator). `autoproduce="true"` keeps the production tick firing on its own. Same mechanism vanilla's Algae Kitchen, CO2 Scrubber, and Tool Generator already use.

The `product="..."` reference points at an existing vanilla `<product type="Process">` recipe in `library/haven`, so `<needs>` (inputs to consume) and `<products>` (outputs to emit) are inherited unchanged. Logistic bots will haul to/from these stations automatically.

## Project layout

```
automated_buildings/
├── info.xml
├── library/
│   ├── haven_automated_buildings.xml        # station + functional inner mids
│   └── texts_automated_buildings.xml        # localized names + descriptions
└── README.md
```

## Changelog

### v0.3.0
- Replaced Auto Greenhouse (algae vat) with **Auto Grow Bed** — clones the vanilla Grow Bed (mid 160) visual so it looks like a planted tile.
- New mod-defined recipe `eid=7800200` produces a mix of **root vegetables, fruits, grains, and nuts** from water + bio matter, non-interactive so it runs without crew.
- Note: vanilla's plant/tend/harvest cycle on Grow Beds is hardcoded in Java (`WorldObject$GrowPlace`, `Stations$FarmingStation`). We can't trigger it from XML alone, so Auto Grow Bed uses a passive `<produces>` recipe instead — same engine path the Algae Kitchen uses. Visually a grow bed, functionally a food generator.

### v0.2.0
- Replaced the experimental Auto-Builder and Auto-Fab with proper multi-tile **Auto Optronics Fabricator** (mid 7800001) and **Auto Advanced Assembler** (mid 7800002) cloning the actual vanilla composites.
- Added new text IDs (7800101-7800106) with full EN + CN localization.
- Auto Greenhouse now shows up under its own name instead of "Algae Kitchen".

### v0.1.0
- Initial scaffold: Auto-Builder (Tool Gen clone), Auto-Fab (Weaver clone), Auto-Greenhouse (Algae Kitchen clone). Single-tile, reused vanilla text IDs.

## License

MIT.

## Credits

- [spacehaven-modloader](https://github.com/Spacehaven-modding-tools/spacehaven-modloader) team for the patch system.
- Bugbyte for Space Haven.
