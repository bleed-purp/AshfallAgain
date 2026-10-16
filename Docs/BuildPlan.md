# Ashfall Again — Build Plan

One milestone per session (1–2 hours). Each milestone ends with every **Done when** check passing in PIE, debug prints removed, `Handoff.md` updated, and a Git commit.

- **By hand** = steps Cris does in the editor because the MCP can't (structs, enums, Data Tables, interfaces).
- **Helper** = optional side job for a second session that only writes docs or CSV files, never Blueprints.
- Phases 1–5 reach the first playable goal: **full Era 1 in the Clearing**. Later phases are outlined and get detailed when reached.

---

## Phase 0 — Setup

### M0 · New project and tooling
- **Build:** New UE 5.8 project from the First Person template, named `AshfallAgain` (no space). Create the folder layout from CLAUDE.md. Add the Unreal MCP plugin; confirm the MCP server connects. Put `CLAUDE.md` in the project root and the four docs in `Docs/`.
- **By hand:** `git init`, Git LFS for `*.uasset *.umap *.fbx *.png *.wav`, Unreal `.gitignore`. Migrate only the needed Synty / Fab assets from a throwaway project (later, when needed).
- **Done when:** project opens; Claude Code can list assets through the MCP; first commit pushed.

### M1 · Data foundation
- **By hand (Claude gives exact tables):** enums `E_ItemCategory` (10 categories from Decisions #20), `E_Era`, `E_MachineID`, `E_RobotJob`, `E_RobotState`, `E_BookTier`. Structs `S_ItemRow`, `S_RecipeRow`, `S_RobotStatsRow`, `S_BarterRow`, `S_InventorySlot`. Data Tables `DT_Items`, `DT_Recipes`, `DT_RobotStats`, `DT_Barters`. Interfaces `BPI_Interactable`, `BPI_ItemContainer`.
- **Extra fields beyond the GDD:** S_ItemRow adds `UseType` (None / Consumable / Gear / Attachment / Decoration), `BuffID`, `RobotFuelValue`, `ToolType`, `ToolTier`. S_RecipeRow keeps InputA, InputB as DT_Items row names.
- **Helper:** write `Data/DT_Items_Era1.csv` and `Data/DT_Recipes_Era1.csv` from the GDD compendium for import.
- **Done when:** all four tables import without errors; every Era 1 item and recipe is a row; no recipe references a missing item row.

---

## Phase 1 — Player and inventory

### M2 · Player basics
- **Build:** BP_AshfallCharacter (from the template), Enhanced Input: WASD, mouse look, jump, 1–0 slot keys, Ctrl row switch, Tab/Esc close. Speed and jump values read from variables so gear and buffs can change them later. BP_MainGameMode, BP_AshfallGameInstance (empty for now).
- **Done when:** walk, look and jump feel right in PIE; holding W moves continuously (rule 10).

### M3 · Interaction
- **Build:** camera line trace (reach 250 from data), outline on the aimed actor, name + action prompt near the crosshair via BPI_Interactable. A test cube that prints when interacted.
- **Done when:** aim at the cube shows outline and prompt; E triggers it; nothing triggers past 250 units.

### M4 · Inventory core
- **Build:** BPC_Inventory component (positional slots, starts at 5, stack from StackMax = 50). Implements BPI_ItemContainer. BPFL_Items with `TransferItem`. Debug key adds test items.
- **Done when:** adding fills the same-item slot first, then the first empty slot; overflow is refused with no loss; emptying a slot leaves a gap; TransferItem never removes from the source when the destination refuses.

### M5 · Hotbar UI and drag-and-drop
- **Build:** WBP_Hotbar (icons, counts, selected highlight, placeable border), held item shown in hand, drag-and-drop between hotbar slots (swap and merge stacks). Supports 5/10/20 slots and two rows.
- **Done when:** number keys select; dragging swaps or merges correctly; counts never change during a drag.

### M6 · Gathering
- **Build:** BP_ResourceNodeBase (item, tool type and minimum tier from data), hold-left-mouse progress ring, Era 1 nodes: Wood, Dirt, Water, Berries, Stone. Bare hands work on soft nodes; Stone needs a Stone Pick (temporary debug pick until recipes exist).
- **Done when:** holding gathers into the hotbar; releasing early cancels; a full hotbar refuses with a message and nothing is lost.

### M7 · Save and load foundation
- **Build:** BP_AshfallSave (SaveGame). GameInstance stores inventory, discovery set, unlocked regions, and per-level actor records (GUID, class, transform, contents, timestamp). Save on quit and on level exit; load on start. Actors with a GUID restore themselves.
- **Done when:** gather items, quit PIE, restart: inventory and a moved test actor come back exactly.

---

## Phase 2 — Crafting

### M8 · Machine base and the Campfire
- **Build:** BP_MachineBase + WBP_MachineMenuBase: two input slots, output (one type, max 5), drag from hotbar into slots, Craft button lights only when inputs match a DT_Recipes row for this machine, process time from data, menu stays open while crafting, refusal popup when output is full or different. Collect/withdraw through TransferItem. BP_Campfire child placed as a found ruin.
- **Done when:** Wood → Charcoal works; wrong inputs keep the button dark; a full output refuses with inputs untouched; closing the menu mid-craft still finishes the craft.

### M9 · Recipe list, Mixing Bowl, Flat Stone
- **Build:** left recipe list (unlocked recipes in full, other recipes for this machine as tier-colored silhouettes with clues). BP_MixingBowl and BP_FlatStone children placed as ruins. All Era 1 recipes active.
- **Done when:** every Era 1 recipe can be crafted from gathered items; the list refreshes on every slot change (rule 6).

### M10 · Placement and chests
- **Build:** BP_PlaceableBase, ghost preview (valid green / invalid red, snap to ground, overlap check), right-click to place, pick up when empty. BP_ContainerBase + BP_Chest (unlimited list) with a drag-drop chest menu.
- **Done when:** a chest can be placed, filled, emptied and picked up; it cannot be picked up while holding items; placement refuses overlapping spots.

### M11 · Discovery and the Recipe Book
- **Build:** discovery set in GameInstance (Discovered / Crafted per item). Distance calculation for Close / Warmer / Locked. WBP_RecipeBook found as a pickup near the campfire; category and era filters; hot-cold colors; formula icons for crafted entries; clues.
- **Done when:** gathering marks Discovered; crafting marks Crafted with formula; tiers update correctly after each craft (spot-check 5 items by hand).

### M12 · Planting
- **Build:** placing Plant spawns a sapling; its menu takes Water (Tree in 2 min) or Berries (Berry Bush in 3 min). Grown trees and bushes become resource nodes. Growth time saves and continues after load.
- **Done when:** both growths work, survive a save/load mid-growth, and the grown node can be gathered.

---

## Phase 3 — Robots

### M13 · Robot base and the Clay Robot
- **Build:** BP_RobotBase with state machine (Idle, MovingToSource, AtSource, MovingToDest, AtDest, Paused). Placing a Clay Robot Body, feeding the Ash Rune activates it (eyes flicker on). Robot menu pauses the robot. Soft cap from DT_RobotStats.
- **Done when:** a robot activates, opens its menu, pauses and resumes; the cap blocks one robot too many with a message.

### M14 · Attachments, routes and the Gatherer
- **Build:** attachment slot sets the job (axe → Gatherer first). Route: aim at pickup, confirm; aim at drop-off, confirm; outlined targets. Loops until changed. Empty source / full destination waits and retries on a timer.
- **Done when:** a Gatherer harvests wood and delivers to a chest for 5 minutes with no errors in the log.

### M15 · Hauler, Planter, Miner; swapping
- **Build:** Hauler (sample item, consumed), Planter, Miner. Swapping attachments returns the old one to the player and clears the route. Reassigning mid-task returns the carried item to its source, else the player.
- **Done when:** all four jobs run; swapping or reassigning mid-carry never loses an item (test each).

### M16 · Robot food
- **Build:** fuel meter; accepted food list in data with values; slow mode when empty; optional boost item with timer.
- **Done when:** a robot slows when empty, recovers when fed, and a boost expires on time.

---

## Phase 4 — Echoes, gates and travel

### M17 · Echoes and bartering
- **Build:** BP_Echo reading DT_Barters; barter menu with trade list + drag items in; one-time vs repeatable trades; memory line as subtitle. Hotbar upgrade (5 → 10) as an Echo trade. One main Echo + one hidden Echo in the Clearing.
- **Done when:** a repeatable trade works twice; a one-time trade disappears after use; the hotbar grows to 10 and saves.

### M18 · Gates, level travel and waystones
- **Build:** key gate opened by the Echo's key; walking through loads `L_ReedMarsh` (blockout). Player inventory and state carry over through GameInstance. Waystone actor unlocks on first visit; waystone menu travels between unlocked regions.
- **Done when:** travel both ways keeps inventory; placed machines/chests in the Clearing are still there on return; waystone travel works.

### M19 · Leaving a region
- **Build:** on level exit, save each machine's in-progress craft and each robot's state, route and carried item. On return, any saved craft is completed (output added if there is room, otherwise it waits in the machine); robots resume from their saved state. No food is used while away.
- **Done when:** start a craft, leave, return: the output is there and no second craft started. A robot carrying an item when you leave still has it on return and finishes its route. Nothing is lost or duplicated.

### M20 · Era 1 shakedown — FIRST PLAYABLE
- **Build:** opening line at the lit campfire, the charcoal wall message, full Era 1 playthrough from a fresh save to the Reed Marsh gate. Fix bugs found. Remove all `[DIAG]` prints. Write a full Handoff.
- **Done when:** a fresh playthrough reaches the Reed Marsh with no blockers, no lost items and no log spam.

---

## Phase 5+ — Later eras (detail written when reached)

Each era repeats the same pattern, one milestone per bullet:

1. Region level blockout + resource nodes + that era's tool tier.
2. Found machine ruin(s) + craftable copies + the era's recipes (Helper writes the CSV first).
3. New items' uses: gear, buffs, decorations, Echo trades.
4. Robot tier, frame recipe, story item, robot dialogue beat.
5. Echoes (main + hidden), next gate, shakedown.

Era-specific extras:
- **Era 2 (Iron, Reed Marsh):** Sundial recipe restores day/night (20-min day); night events and night-only resources; Kiln, Forge; boots and first speed buffs.
- **Era 3 (Industrial, Old Works):** clearing-item gate; Steam Press, Wiring Bench; first mount/vehicle.
- **Era 4 (Alchemical, Grove of Lights):** pre-filled Recipe Book entries story beat; Cauldron, Alchemist Table, Crystal Lens.
- **Era 5 (Plasma, Spire):** Plasma Lab, Tech Bench; Mech Suit; all-robots-speak beat.
- **Finale:** First Memory, Omega Robot, ending choice (break the loop / start again), post-credits scene.
- **Late passes:** audio, art polish, balancing, performance.

---

## Helpers and subagents

- Start with **no subagents.**
- Add a read-only **blueprint-debugger** subagent (cheaper model) once M8 is done and bugs start needing deeper digging.
- A second session may run alongside only for Helper jobs (CSV data, next milestone's spec). Never two sessions editing Blueprints.
