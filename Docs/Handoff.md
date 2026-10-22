# Handoff

Update this file at the end of every session. Keep it under one page.

**Current milestone:** M7 · Save and load foundation — complete. Next: M8 · Machine base and the Campfire
**Last session:** 2026-10-21
**Last commit:** M5 commit f66ead7; M6 and M7 not yet committed

## M7 state (done, all "Done when" checks pass in PIE)
- **Save slot `AshfallSave`** (one file, `Saved/SaveGames/AshfallSave.sav`). `Core/BP_AshfallSave` (SaveGame): `InventorySlots`, `DiscoveredItems`, `UnlockedRegions`, `ActorRecords` (S_SavedActor array), `SavedAt`.
- **`BP_AshfallGameInstance`:** mirror variables of the same data; `SaveToDisk`, `LoadFromDisk` (called from Event Init), `StoreRecord(Record)` (replace by guid+level, else add), `FindRecord(Guid, LevelName) → Found, Record`. Event Shutdown calls `SaveToDisk` and is the final write on quit. Requires Project Settings Game Instance Class = BP_AshfallGameInstance (confirmed working).
- **`Core/BPC_Saveable`** (ActorComponent, add to any saveable actor): `SaveGuid` (Guid, instance editable), `LevelKeyCached`, `GetLevelKey`, `GetRecord`, `ApplyRecord` (teleport owner, zero physics velocity). BeginPlay caches the level key, registers with `BP_MainGameMode.RegisterSaveable`, looks up its record and applies it. **EndPlay stores the record in the GameInstance** (owner still exists then). Without a valid guid it prints one warning and does nothing.
- **GUID creation:** the owner's construction script sets `Saveable.SaveGuid` to `NewGuid` only when it is invalid; it persists once the level is saved (verified). See `World/BP_SaveTestActor`.
- **`BP_MainGameMode`:** `SaveableComponents`, `RegisterSaveable` (AddUnique), `CollectAndSave` (stores every registered record, then saves), called from EndPlay. Intended for level exits.
- **Inventory:** `BP_AshfallCharacter.RestoreInventory` (end of BeginPlay chain, after `InitGather`) copies saved slots into `Inventory.Slots` (up to `SlotCount`) and fires `OnSlotsChanged`. `PushInventoryToGameInstance` (called from the character's EndPlay) copies slots into the GameInstance and saves.
- **`World/BP_SaveTestActor`:** physics cube with `BPC_Saveable`, used for the Done-when check; delete or keep as a test prop.
- Hotbar text made smaller: `WBP_HotbarSlot` item label 18, count 14, key 15.
- All M7 `[DIAG]` prints removed.

## Traps found this session
- `Game|GetCurrentLevelName` returns an empty string once the world is tearing down: cache the level key at BeginPlay (done in `BPC_Saveable`).
- Impure nodes nested inside `(return ...)` in the DSL are not exec-wired and never run. Bind them as their own statement first.
- GameMode EndPlay fires too late to read owner actors reliably; use component EndPlay.
- New BP functions are not visible to other graphs (find_node_types) until the asset is saved; call them after a save, or put the caller in the same Blueprint.
- Known gap: on a real level change the last write may happen before the character's EndPlay pushes the inventory. Re-check when the first level transition exists (M12+ regions): call push-then-save explicitly from the transition code.
- Records are kept even for actors destroyed at runtime (their last transform stays saved). Spawned-at-runtime actors do not respawn yet; `actorClass` is saved for that.

## M6 state (done)
- Gathering: `IA_Gather` (LMB) → `StartGather` / `GatherTick` / `StopGather` / `CanGather` on the character; `World/BP_ResourceNodeBase` (`ItemID`, `Amount`, `GatherTime`, `RequiredToolType` E_ToolType, `MinTier`, `PromptText`), nodes Dirt, Water (well mesh), Wood, Berries, Stone (needs Pick). Tool check reads the selected hotbar slot via `BPC_Inventory.GetSelectedItemID`. Full hotbar shows "Hotbar full". `UI/WBP_GatherRing` (ring, message). `BPC_Inventory` unchanged otherwise.
- E opens menus only; `WBP_InteractPrompt.SetPrompt` shows the text as given (each interactable carries its own key hint).
- `Art/Placeholders/M_Placeholder` + `BP_PlaceholderCube` for anything without a real mesh; Dirt/Water use `UsePlaceholder` to switch to a real mesh.
- `StonePick` row in DT_Items. No way to obtain a pick until recipes exist (M9).

## State from earlier milestones
- M0–M5 done (see git log). `BPC_Inventory` (component `Inventory`): `Slots` (S_InventorySlot: `itemId`, `amount`; empty = `None`/0), `SlotCount` 5, `ItemsTable` = DT_Items, `SelectedIndex`, `ActiveRow`, dispatchers, `SelectSlot`, `SwitchRow`, `MoveSlot`, `GetStackMax`, `GetSelectedItemID`. Add is all-or-nothing. Implements BPI_ItemContainer.
- `BPFL_Items.TransferItem(Source, Destination, ItemID, Amount)`: add first, remove only on success.
- Decision 53: no held items; items are UI icons only.
- Input in `Player/Input/`: Move, Look, Jump, Interact (E), Gather (LMB), Slot1–10, SwitchRow (Ctrl), ToggleCursor (Tab), `IMC_Ashfall`.
- M3 interaction: `InteractReach` 250, `CurrentTarget`, `UpdateTarget` (0.05 s timer), `SetOutline` (Custom Stencil 1), `TryInteract`, `WBP_InteractPrompt`.
- `Core/BP_MainGameMode` (Default Pawn = BP_AshfallCharacter). Test level is still the template `Lvl_FirstPerson`.

## Known issues / bugs
- Template input actions still exist in `/Game/Input/Actions` with the same names as ours; MCP-created events with those names bind to the TEMPLATE action. Add such events by hand. (Unique names like `IA_Gather` are fine.)
- MCP graph read-back is lossy (dropped default args, wrong labels); trust pin checks, not the printed DSL. Calls with duplicate type ids (e.g. SetScalar/VectorParameterValue on MID vs collection) need `create_node` with `declaring_class`.
- MCP cannot create enum-typed variables, Data Tables, structs, interfaces or BPFLs: do these by hand.
- Nodes never deplete or respawn (rules still open in Decisions). `GetInteractPrompt` returns one Text.
- Not tested: 10/20-slot hotbar layouts and Ctrl row switch (M17).

## By hand steps waiting for Cris
- Save All, then commit M6 and M7 (suggest two commits). The Fantastic Village art (`Art/FantasticVillage/SM_PROP_well`, `Art/Materials/MI_*`, `M_Master_opaque*`) is used by the Water node and should get its own asset commit first; leave `Content/Fantastic_Village_Pack/` out.

## Next session starts with
- M8 · Machine base and the Campfire (confirm "Done when" with Cris first). Interactables that open menus use E and put "[E]  ..." in their prompt text. Machines that must persist add `BPC_Saveable` (guid generated in the owner's construction script) and store contents in the record's `contents` field.

## Decisions made during the build
- Inventory empty-slot convention is `itemId = None`, `amount = 0`. Interface inputs are `ItemID`/`Amount`; struct fields are lowercase `itemId`/`amount`.
- Item and recipe references are `Name` fields holding DT_Items row names. Recipe row names are `Machine_InputA[_InputB]`. Robot assembly uses `E_MachineID.RobotAssembly`.
- Not yet in data: RobotFuelValue (all 0), BuffID (blank), Clue (blank), robot hungry-speed field, robot stats and barters rows.
- Hotbar-upgrade Echo trades will use a pouch item as the reward.
- Saving: one slot, flat `ActorRecords` array keyed by guid + level name (no nested maps); saveable actors carry `BPC_Saveable` instead of a new interface or parent class.
- Outline = post-process on Custom Stencil (not depth), amber, 2px; `r.CustomDepth=3` in DefaultEngine.ini.
- Gathering is left mouse hold with a progress ring; E opens menus only.
