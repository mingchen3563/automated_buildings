# Automated Buildings — post-mortem

> **Status: abandoned at v0.5.0.** All modules removed. Repo retained as a documented negative result.

## What this mod tried to do

Add three player-buildable "automated" versions of vanilla Space Haven facilities that normally require crew operation:

- **Auto Optronics Fabricator** — clone of the vanilla Optronics Fabricator (mid 1989).
- **Auto Advanced Assembler** — clone of the vanilla Advanced Assembler (mid 2002).
- **Auto Grow Bed** — clone of the vanilla Grow Bed (mid 160), with a new selectable-output recipe (water + fertilizer → root vegetables / fruits / grains / nuts).

The plan was to set `<stateWatchdog autoproduce="true"/>` and `produceInNormal="true"` on cloned functional inner elements, expecting them to run without a crew operator the way vanilla Algae Kitchen, CO2 Scrubber, and Tool Generator do.

## What actually happens

**Nothing.** Built stations sit idle. The `<produces>` tick never fires, no inputs are consumed, no outputs appear. Logistic bots don't haul materials because the station never registers a demand.

We verified the patches *did* land — the modloader merged the library file successfully, and the in-game library XML reflected the new mids with `autoproduce="true"`. The engine just refused to run the production cycle.

## Why

Vanilla Space Haven has two distinct classes of `<produces>` facility:

| Class | Examples | Behavior without crew |
|---|---|---|
| **Passive** | Algae Kitchen, CO2 Scrubber, Tool Generator, Water Purifier | Production tick runs autonomously. `autoproduce="true"` actually means something. |
| **Crew-gated** | Optronics Fabricator, Advanced Assembler, Material Fab, Greenhouse Grow Bed | Production tick is gated on a Java-side check that requires a crew member to be in the `StandWork` task at the facility. `autoproduce="true"` has no effect. |

The gating lives in compiled Java classes — `Stations$FarmingStation`, `Production$FoodCrop`, the AdvancedAssembler/OptronicFab work loops in `WorldObjectHelper`. There is **no XML attribute that flips a crew-gated facility into the passive class**. The two classes look identical in the data files; the difference is purely in code.

We also tried:
- `produceInNormal="true"` on the functional inner element. No effect on crew-gated facilities.
- Removing the `<tasks>` block (the crew task definition). The facility wouldn't render correctly and still didn't produce.
- An interactive container recipe with `<list><processes>` and sub-recipes (vanilla Energy Refinery pattern). Selectable-output worked at the data layer, but the crew gate still applied.
- For the Grow Bed: the planting loop runs in `WorldObject$GrowPlace` / `Stations$FarmingStation` and is entirely crew-driven. Even adding a passive `<produces>` recipe on top of the grow tile didn't bypass the gate.

## What would actually work

Same conclusion drones_plus Phase 2 reached: **AspectJ code injection.** Write a Java aspect that intercepts the work-loop check in `Stations$FarmingStation`/Production work classes and treats the new mids as passive (skip the crew check, advance the production tick on its own).

That's a multi-week reverse-engineering project per facility class. Out of scope here.

## What's left in this repo

- `info.xml` — empty mod metadata (so the modloader doesn't error if you have the junction still in place).
- `README.md` — this post-mortem.

Git history preserves the v0.1.0 - v0.4.0 attempts so future modders can see exactly which XML approaches were tried and ruled out.

## Sibling project

[drones_plus](https://github.com/mingchen3563/drones_plus) — Phase 1 successfully tuned existing player bot stations (passive-class facilities). Phase 2 (new bot AI) was abandoned for the same crew-gate reason.

## License

MIT.
