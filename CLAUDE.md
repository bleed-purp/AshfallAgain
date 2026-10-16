# Ashfall Again — Claude Code rules

First-person solarpunk crafting + automation game. **Unreal Engine 5.8, Blueprints only (no C++).** Solo developer: Cris. Built through Epic's Unreal MCP plugin.

## Sources of truth (read only what the task needs)

1. `Docs/Handoff.md` — read FIRST every session: current milestone, state, known issues.
2. `Docs/BuildPlan.md` — milestone list. Work only on the milestone named in Handoff.
3. `Docs/Decisions.md` — settled design. **Overrides GDD_v4.md** where they differ.
4. `Docs/GDD_v4.md` — full design and recipe compendium. Look things up; don't read it whole.

## Session protocol

1. Read `Docs/Handoff.md`. Confirm the milestone and its "Done when" checks with Cris before building.
2. Plan first (short list of assets + steps). Wait for OK on anything that changes an existing parent class or interface.
3. Build in small steps. After each step: compile, check pins, ask Cris to PIE test. Do not stack untested steps.
4. If a step needs something the MCP cannot make (see below), stop and give Cris exact by-hand instructions.
5. Close the milestone only when every "Done when" check passes in PIE. Then: remove debug prints, update `Docs/Handoff.md`, remind Cris to commit.
6. Never start the next milestone in the same session.

## MCP limits — Cris makes these by hand

Structs, enums, Data Tables, Blueprint Interfaces and interface implementations. Give field names, types and default values exactly, in a table. CSV data for Data Tables may be written as files in `Data/` for Cris to import.

## Architecture (do not deviate without asking)

- **Data:** DT_Items, DT_Recipes, DT_RobotStats, DT_Barters (+ later tables named in BuildPlan). Items are referenced by DT_Items row name (Data Table Row Handle / Name), **never a free-typed string**. No hardcoded stats, times or amounts.
- **Parents:** BP_MachineBase, BP_RobotBase, BP_ContainerBase, BP_ResourceNodeBase, BP_PlaceableBase, WBP_MachineMenuBase. Children only set IDs, meshes and overrides.
- **Interfaces:** BPI_Interactable (Interact, GetInteractPrompt), BPI_ItemContainer (TryAddItem, TryRemoveItem, GetItemCount, HasRoomFor). No cast chains.
- **TransferItem(Source, Destination, Item, Amount)** in BPFL_Items is the only way items move: add to destination first, remove from source only on success.
- **State:** BP_AshfallGameInstance holds cross-level state (inventory, discovery, unlocked regions, per-level saved actors, timestamps). BP_MainGameMode handles the current level. Every saveable actor has a persistent GUID.
- **Inventory:** positional slots (S_InventorySlot array); emptying a slot leaves it empty in place.

## Blueprint rules (law)

1. In widgets, drag Get nodes off the cast result pin; never a loose Get. Never give a widget a variable with the same name as its owner's.
2. Compute a value once and SET it into a variable before downstream reads; pure nodes re-evaluate.
3. Branch before ADD or REMOVE so exactly one runs.
4. Change the source only after the destination confirms it received the item.
5. Never destroy an item without an explicit disposal path, including robot reassignment and attachment swaps.
6. Clear any filtered list at the start of every refresh.
7. Delays only in Custom Events or timers, never in Functions.
8. ADD on a Map replaces the value; compute the new total yourself.
9. Every actor that belongs in a GameMode list registers itself in BeginPlay.
10. Movement inputs use the default trigger; a Pressed trigger breaks held movement.
11. Retries run on timers, never tight loops, and never spam the log.
12. A clean compile is not proof of correct wiring: check pins, then PIE test.

## Naming & folders

`Content/Ashfall/` → `Core/` (GameMode, GameInstance, SaveGame, BPFL), `Player/`, `Items/`, `Machines/`, `Robots/`, `World/` (nodes, gates, waystones), `Echoes/`, `UI/`, `Data/` (DTs, structs `S_`, enums `E_`), `Interfaces/`, `Maps/` (one level per region), `Art/`.
Prefixes: BP_, WBP_, BPI_, BPFL_, BPC_ (components), S_, E_, DT_, M_/MI_, SM_/SK_, L_ (levels).
Debug prints start with `[DIAG]` so they're easy to find and remove.

## Cost habits

- Don't explore the whole project; ask Cris for the exact asset if unsure.
- Read GDD sections by heading, not the whole file.
- Keep replies short; no recaps of unchanged work.
- One Claude Code session edits Blueprints at a time. Helpers may write docs/CSVs only.
