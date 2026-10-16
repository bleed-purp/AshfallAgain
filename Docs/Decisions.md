# Ashfall Again — Design Decisions (v4.1)

Settled 2026-10-15 in a 50-question session. **Where this file and GDD_v4.md disagree, this file wins.** Anything still Open is listed at the end.

## Title

**Ashfall Again** (renamed from Alchemica: Origins). The Ash Rune is the first item crafted and the last item handed back. Unreal project and folders use `AshfallAgain` / `Ashfall`.

## Player & controls

| # | Topic | Decision |
| --- | --- | --- |
| 1 | Gathering input | Left mouse uses the held tool on resource nodes. E is for machines, chests, robots, Echoes, saplings. |
| 2 | Interaction reach | 250 units (stored in data, tunable). |
| 3 | Movement speed | No sprint key. Speed comes from items (see #5). |
| 4 | Gathering style | Hold left mouse; a progress ring fills; the item goes to the hotbar. |
| 5 | Movement items | All four: wearable speed gear (boots), timed speed buffs from consumables, mounts/vehicles (later eras), fast travel via waystones. |
| 6 | Jump | Basic jump from the start; gear can improve it. |
| 7 | Survival stats | None. No health, hunger or stamina. Food gives buffs and is a crafting/robot-fuel ingredient. |
| 8 | Item uses | Every item should have as many uses as possible: eat/drink for buffs, equip as gear, place as decoration, barter with Echoes, plus crafting. |

## Inventory & storage

| # | Topic | Decision |
| --- | --- | --- |
| 9 | Stack cap | 50 (DT_Items.StackMax default 50; tools and gear 1). |
| 10 | Hotbar upgrades | 5 → 10 → 20 slots, obtained only through Echo trades. |
| 11 | Drag-and-drop | Built from the start, with the first hotbar. |
| 12 | Chests | Unlimited types and amounts (a growing list). |
| 13 | Dropping items | Not allowed. Items only move through TransferItem. |

## Machines & crafting

| # | Topic | Decision |
| --- | --- | --- |
| 14 | Era 1 machines | Found in the Clearing as overgrown ruins. |
| 15 | Later machines | Mix: the first of each is found (as a ruin) in its region; more copies can be crafted and placed. |
| 16 | Machine fuel | None. Charcoal is only an ingredient. |
| 17 | Craft time | A few seconds, scaling by era (about 2s in Era 1 up to about 10s in Era 5), set per row in DT_Recipes. |
| 18 | Relocating | Machines and chests can be picked back up only when empty. |
| 19 | Craft flow | Recipe list on the left. Player drags items from the hotbar into the two input slots. If the inputs match a recipe for this machine, the Craft button lights; the player presses it. |
| 23 | Left recipe list | Shows recipes already unlocked on this machine (full formula) **and** this machine's other possible recipes (as silhouettes, colored by Recipe Book tier, with their clue if revealed). |

## Recipe Book

| # | Topic | Decision |
| --- | --- | --- |
| 20 | Grouping | Grouped by category **and** by era, with filters for both. Categories: Resources, Materials, Fuel, Food, Tools, Gear, Machines, Robots, Decorations, Story Items. |
| 21 | Tier colors | Hot-cold scale: Close = orange/red, Warmer = yellow, Locked = blue-gray. Crafted keeps its gold border. |
| 22 | Getting the book | Found near the campfire at the start (first pickup; ties to "the fire was already lit"). |

## Robots

| # | Topic | Decision |
| --- | --- | --- |
| 24 | Robot job | Set by an **attachment** item (e.g. axe → Gatherer, trowel → Planter, pick → Miner, basket → Hauler). One body per era tier. |
| 25 | Running cost | Robots consume food/fuel. When empty they keep working but slowly. Feeding better items gives an optional temporary speed boost. |
| 26 | Echo deliveries | Robots cannot deliver to Echoes. Bartering is always hands-on. |
| 27 | Swapping jobs | Attachment can be swapped anytime. The old attachment returns to the player (TransferItem), and the robot clears its route. |
| 28 | Robot food | Anything from an accepted list (data-driven); better items last longer. |
| 29 | Robot cap | Soft cap per era (e.g. 3 in Era 1, rising each era), stored in DT_RobotStats. |
| 30 | Routes | Aim at pickup, confirm; aim at drop-off, confirm (targets outlined). |

## World & travel

| # | Topic | Decision |
| --- | --- | --- |
| 31 | Region names | Keep working titles: The Clearing, The Reed Marsh, The Old Works, The Grove of Lights, The Spire. |
| 32 | Map layout | **Separate levels per region.** |
| 33 | Tools | One tool per type (Axe, Pick, Trowel, Sickle), each with Stone → Iron → Steel → Crystal → Plasma tiers. Higher tier = faster and can harvest harder nodes. |
| 34 | Durability | None. Tools last forever. |
| 35 | Off-level behavior | When the player leaves a region, any craft in progress **finishes** (output appears on return; no new craft starts). **Robots stop** where they are and resume their route on return; they use no food while away. |
| 36 | Travel | Walk through opened gates to load the next region. Waystones can be unlocked for fast travel. |

## Echoes

| # | Topic | Decision |
| --- | --- | --- |
| 37 | Who they are | The player's past selves, each from a different loop. |
| 38 | Barter menu | Trade list plus drag items in, same drag-drop as machines. |
| 39 | Hidden Echoes | 1 per region (5 total), plus the main Echo(es). |

## Time, story, art, audio

| # | Topic | Decision |
| --- | --- | --- |
| 40 | Restoring time | Crafting a **Sundial** in Era 2 restores the day/night cycle. |
| 41 | Day length | 20 minutes (about 14 day / 6 night). |
| 42 | Ending | The player chooses: break the loop or start again. |
| 43 | Dialogue | Subtitles plus a sound cue; no voice acting. |
| 44 | Machine names | Keep current names. Item/machine IDs never change; display names can. |
| 45 | Audio | No audio until late in the build. |

## Build process

| # | Topic | Decision |
| --- | --- | --- |
| 46 | Engine | Unreal Engine 5.8 (same as the last project and plugin). |
| 47 | Version control | Git + LFS. Commit at the end of every milestone and before any large change. |
| 48 | Save system | Built early, right after inventory. |
| 49 | First playable goal | Full Era 1 in the Clearing: gathering, all Era 1 recipes, Recipe Book, Clay Robot working, first Echo key opens the Reed Marsh gate. |
| 50 | Pace | A few evenings a week, one milestone per session (1–2 hours). |

## Still Open

- Why the player went back, and how and why they forgot.
- The final items and the Omega Robot's words.
- Exact key or clearing item for each region gate.
- Night event list and night-only resources.
- Sundial recipe (Era 2 machine and inputs).
- Recipes for tools, attachments, boots, mounts, waystones and decorations (new items not yet in the compendium).
- Robot food list and per-era soft cap numbers.
- Resource node respawn rules.
