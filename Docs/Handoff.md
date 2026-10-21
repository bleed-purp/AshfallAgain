# Handoff

Update this file at the end of every session. Keep it under one page.

**Current milestone:** M5 · Hotbar UI and drag-and-drop — complete. Next: M6 · Gathering
**Last session:** 2026-10-21
**Last commit:** M4 commit (see git log); M5 not yet committed

## M5 state (done, all "Done when" checks pass in PIE)
- **Decision 53: no held items.** Items show only as UI icons; placeables appear only as the M10 placement ghost. The held-item display (BPC_HeldItem, SetHeldItem, UpdateHeld/BindHeld/InitHeld, inventory GetSelectedItem) was built and then removed; nothing references it. If M10 needs the selected item, re-add a `GetSelectedItem` on `BPC_Inventory`.
- `BPC_Inventory`: `SelectedIndex`, `ActiveRow`, dispatchers `OnSlotsChanged` / `OnSelectionChanged` (add/remove/move fire `OnSlotsChanged`), `SelectSlot(Index)`, `SwitchRow` (only above 10 slots), `MoveSlot(From, To)` (swap or merge to stack max; no-op for empty source, same slot, out-of-range or full destination).
- Input: `IA_Slot1`–`IA_Slot10` (keys 1–0), `IA_SwitchRow` (Left Ctrl), `IA_ToggleCursor` (Tab: show cursor, freeze look; GameAndUI input mode). All in `IMC_Ashfall`.
- UI: `WBP_HotbarSlot` (90x90 square: `iconText`, count if above 1, key number, amber selection frame, green placeable border; owns drag start `OnMouseButtonDown`/`OnDragDetected` and `OnDrop`, which calls `MoveSlot`), `WBP_Hotbar` (`Setup(Inventory)`, `Build`, `Refresh`; two rows, row 2 only above 10 slots), `BP_HotbarDragOp` (source index/widget). Character: `InitHotbar`, `SelectHotbarSlot`, `SwitchHotbarRow`, `ToggleCursor`.
- All `[DIAG]` test code removed (numpad keys, debug functions, selection prints). No way to add items for testing now; M6 gathering supplies it, or add a temporary numpad key. Placeable test items in DT_Items: `Plant`, `ClayRobotBody`, `ClayRobot`.
- Not tested in PIE: 10- and 20-slot layouts and the Ctrl row switch at 20 slots (logic is in place; test when the hotbar upgrade exists in M17).
- `WBP_HotbarSlot` sizes via a SizeBox: its "override" switches (`bOverride_WidthOverride` / `bOverride_HeightOverride`) must be on or the size is ignored.

## State
- M0–M3 done (see git log). M4 done: all "Done when" checks pass in PIE (same-item slot fills first, overflow refused with no loss, emptied slot leaves a gap, TransferItem leaves source untouched on refusal). Debug code removed.
- `Player/BPC_Inventory` (component, on `BP_AshfallCharacter` as `Inventory`): `Slots` (S_InventorySlot array: `itemId` Name, `amount` Integer; empty = `None`/0), `SlotCount` 5, `ItemsTable` = DT_Items. BeginPlay resizes Slots. Implements BPI_ItemContainer: all four functions return only Success/count/bool (no Remaining). Add is all-or-nothing (partial fit is refused). Unknown item or Amount <= 0 is refused. `GetStackMax(ItemID)` reads `stackMax` per item from DT_Items (0 if missing).
- `Core/BPFL_Items.TransferItem(Source, Destination, ItemID, Amount) → Success`: Source/Destination are Object refs called through interface messages; checks source count, adds to destination, removes from source only after the add succeeds.
- `Player/BP_AshfallCharacter` (Character): `FirstPersonCamera` at Z 64; `WalkSpeed` 400, `JumpVelocity` 450, `LookSensitivity` 1.0; functions `ApplyMove`, `ApplyLook`.
- M3 interaction on the character: `InteractReach` 250 (variable, no settings table yet), `CurrentTarget`, `UpdateTarget` (0.05s looping timer from BeginPlay; only BPI_Interactable actors become target), `SetOutline(Actor,bool)` (Render CustomDepth + Stencil 1 on all primitive components), `TryInteract` (calls BPI_Interactable.Interact with self as Interactor), `InitPrompt` + `PromptWidget`.
- Input in `Player/Input/`: `IA_Move`, `IA_Look`, `IA_Jump`, `IA_Interact` (E, Started), `IMC_Ashfall`.
- `Art/Materials/M_PP_Outline`: post process, CustomStencil==1 edge, params `OutlineColor` (amber), `Thickness` 2; set as a blendable on the camera component (not in BeginPlay).
- `UI/WBP_InteractPrompt`: centre `+` and `PromptLabel` ("[E]  <prompt>"), `SetPrompt(Text,bool)`.
- `World/BP_Interactable`: test cube implementing BPI_Interactable; Interact prints the interactor name; prompt "Test Cube". Delete or replace when real interactables exist.
- `Core/BP_MainGameMode` (Default Pawn = BP_AshfallCharacter) and `Core/BP_AshfallGameInstance` (empty); project default GameMode. `Lvl_FirstPerson` has no World Settings override.

## Known issues / bugs
- Template input actions still exist in `/Game/Input/Actions` with the same names as ours. The MCP cannot tell them apart: new Enhanced Input events created through it bind to the TEMPLATE action. Add Move/Look/Jump-style events by hand and pick the `Ashfall/Player/Input` one. (New unique names like `IA_Interact` work fine.)
- `GetInteractPrompt` returns one Text; no separate name/action.
- Test level is still the template `Lvl_FirstPerson`.

## By-hand steps waiting for Cris
- None.

## Not built (deferred)
- Esc (close menus): add when menus exist. Tab currently only toggles the cursor for hotbar dragging; revisit when menus open.
- Reach is a character variable, not data; move to a settings table if one is added.

## Next session starts with
- M6 · Gathering (confirm "Done when" with Cris first). Gathered items go in through `BPI_ItemContainer.TryAddItem` on `BP_AshfallCharacter.Inventory`; a refusal (full hotbar) must show a message and lose nothing. Use numpad keys for any test items (F1–F12 clash with editor view-mode shortcuts; the number row is for the hotbar).

## Decisions made during the build
- Inventory empty-slot convention is `itemId = None`, `amount = 0`. Interface inputs are `ItemID`/`Amount`; struct fields are lowercase `itemId`/`amount`.
- A destination-full TransferItem refusal was not tested in PIE (the numpad test only covered the source-lacks-item refusal); logic is add-then-remove so a refused add never touches the source.
- MCP cannot create Blueprint Function Libraries; BPFL_Items was created by hand.
- Item and recipe references are `Name` fields holding DT_Items row names (not Data Table Row Handles); CSV check enforces them.
- Recipe row names are `Machine_InputA[_InputB]`. Robot assembly uses `E_MachineID.RobotAssembly`.
- Not yet in data: RobotFuelValue (all 0), BuffID (blank), Clue (blank), robot hungry-speed field, robot stats and barters rows.
- Hotbar-upgrade Echo trades will use a pouch item as the reward; wire up when building Echoes.
- Player is a plain Character BP built via MCP (no template character). Jump velocity tuned to 450 by feel.
- Outline = post-process on Custom Stencil (not depth), amber, 2px; `r.CustomDepth=3` in DefaultEngine.ini.
