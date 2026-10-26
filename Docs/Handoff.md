# Handoff

Update this file at the end of every session. Keep it under one page.

**Current milestone:** M8 · Machine base and the Campfire — complete. Next: M9 · Recipe list, Mixing Bowl, Flat Stone
**Last session:** 2026-10-25
**Last commit:** M6 and M7 committed by Cris; M8 not yet committed

## M8 state (done, all "Done when" checks and persistence pass in PIE)
- **`Machines/BP_MachineBase`** (Actor, implements BPI_Interactable + BPI_ItemContainer, has `Saveable` BPC_Saveable). Vars: `MachineID` (E_MachineID, instance editable), `RecipesTable` = DT_Recipes, `InputA`/`InputB`/`OutputSlot`/`PendingSlot` (S_InventorySlot), `InputMax` 5, `OutputMax` 5, `IsCrafting`, `CraftingRecipe`, `PromptText` (instance editable), `MenuWidget`, scratch `TakeO/B/A`. Dispatcher `OnMachineChanged`.
- **Functions:** `FindRecipe(A,B)` (iterates DT_Recipes, order-independent, matches MachineID), `CanCraft`, `TryStartCraft → Started, Reason` (refuses before touching anything if output holds another item or is at OutputMax; consumes one of each input; reserves result in `PendingSlot`; timer `FinishCraft` with the row's processTime), `FinishCraft`, `NotifyChanged` (writes [A,B,Out,Pending] to `Saveable.Contents`, fires dispatcher), `LoadContents` (BeginPlay; restores slots; an unfinished craft completes at once), `SanitizeSlots`, `AcceptableAmount(ItemID)`, `OpenMenu`. Interface: `TryAddItem`/`HasRoomFor` (inputs), `TryRemoveItem` (draws output → B → A, all-or-nothing), `GetItemCount`, `GetInteractPrompt`, `Interact` event opens the menu.
- **`Machines/BP_Campfire`**: child, `MachineID = Campfire`, prompt "[E]  Use Campfire", mesh `SM_Prop_CampFire_01`. Placed in `Lvl_FirstPerson`.
- **`UI/WBP_MachineMenuBase`**: A/B/Out slot buttons (click = withdraw to hotbar via TransferItem), Craft button (dim and disabled unless `CanCraft`), status line, red refusal popup (2 s), Close. Whole panel is the drop target for hotbar drags (`OnDrop` override). Close returns A and B to the hotbar (3 passes), locks/unlocks look + move input and the mouse cursor.
- **`BPC_Saveable`** gained `Contents` (S_InventorySlot array); `GetRecord` writes it. Other saveables are unaffected (empty array).
- All M8 `[DIAG]` prints removed.

## Traps found this session
- A child Blueprint created before a parent default was set keeps its own old default (BP_Campfire `InputMax` stayed 0). Re-check child CDO defaults with ObjectTools after changing a parent default.
- Pure nodes re-run after variable sets: multi-slot math (TryRemoveItem) must be stored in variables first (rule 2). It produced `None x-13` before the fix.
- `write_graph_dsl` leaves old nodes behind as disconnected orphans (the DSL readback hides them). Delete stale debug nodes with `delete_node`; verify with `find_nodes` title "Print".
- Widget variables only exist for DSL after a widget compile, and are addressed as `Variables|<WidgetName>|GetX`. Interface events use `EventInteract`; widget function overrides work with `add_function_graph("OnDrop")`.
- Self-calls and external calls: use keyword args (`:self`, pin names); positional order puts `self` in different places.

## Known issues / bugs
- Clicking a slot (or Close) when A and B hold the same item can empty B first because `TryRemoveItem` goes output → B → A. Nothing is lost. A "remove from this slot" option would need a change to how items move.
- Menu opens with the cursor handled by the widget, not the character's `CursorOn` flag, so Tab while a menu is open can desync. Tab alone still locks look only.
- Dropping on slot A vs B does not matter; the machine picks the slot.
- Closing mid-craft with a full hotbar leaves returned inputs in the machine (not destroyed).
- Orphaned dead nodes remain in several rewritten graphs (harmless, cosmetic).
- Template input actions still exist in `/Game/Input/Actions` with the same names as ours; MCP-created events with those names bind to the TEMPLATE action. Add such events by hand.
- MCP graph read-back is lossy (dropped args, wrong labels); trust PIE tests, not the printed DSL. MCP cannot create enum-typed variables, Data Tables, structs, interfaces or BPFLs.
- Nodes never deplete or respawn (rules still open in Decisions). Not tested: 10/20-slot hotbar layouts and Ctrl row switch (M17).

## By hand steps waiting for Cris
- Save All, then commit M8 ("M8: machine base, campfire, machine menu"). The campfire mesh `Art/Polygon/Nature/SM_Prop_CampFire_01` is already in the repo.

## Next session starts with
- M9 · Recipe list, Mixing Bowl, Flat Stone (confirm "Done when" with Cris first). Reuse `BP_MachineBase` and `WBP_MachineMenuBase`; children set `MachineID`, mesh, prompt. Recipe rows are in `Data/DT_Recipes_Era1.csv`.

## Decisions made during the build
- Inventory empty-slot convention is `itemId = None`, `amount = 0`. Interface inputs are `ItemID`/`Amount`; struct fields are lowercase `itemId`/`amount`.
- Item and recipe references are `Name` fields holding DT_Items row names. Recipe row names are `Machine_InputA[_InputB]`. Robot assembly uses `E_MachineID.RobotAssembly`.
- Machines: inputs are consumed at craft start, the result is reserved in `PendingSlot` so a load can never find the output full; an unfinished craft completes at once on load. Output cap 5 and input cap 5 are instance-editable variables, not data rows.
- Saving: one slot, flat `ActorRecords` array keyed by guid + level name; saveable actors carry `BPC_Saveable`; machine contents are `[A, B, Out, Pending]` in the record's `contents`.
- Gathering is left mouse hold with a progress ring; E opens menus only. Outline = post-process on Custom Stencil, amber.
- Decision 53: no held items; items are UI icons only.
