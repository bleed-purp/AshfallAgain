# Handoff

Update this file at the end of every session. Keep it under one page.

**Current milestone:** M3 · Interaction — complete. Next: M4 · Inventory core
**Last session:** 2026-10-20
**Last commit:** c12f91e M3: interaction (trace, outline, prompt, E)

## State
- M0, M1, M2 done (see git log). M3 done: all three "Done when" checks pass in PIE (outline + prompt on aim, E triggers, nothing past 250).
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
- Slot keys 1–0, Ctrl row switch, Tab/Esc: add when their consumers exist (M5 hotbar, UI milestones).
- Reach is a character variable, not data; move to a settings table if one is added.

## Next session starts with
- M4: BPC_Inventory component (positional slots, start 5, StackMax 50), implements BPI_ItemContainer, BPFL_Items.TransferItem, debug key. Confirm "Done when" with Cris first. Components implementing the interface and the BPFL are by-hand steps where the MCP can't.

## Decisions made during the build
- Item and recipe references are `Name` fields holding DT_Items row names (not Data Table Row Handles); CSV check enforces them.
- Recipe row names are `Machine_InputA[_InputB]`. Robot assembly uses `E_MachineID.RobotAssembly`.
- Not yet in data: RobotFuelValue (all 0), BuffID (blank), Clue (blank), robot hungry-speed field, robot stats and barters rows.
- Hotbar-upgrade Echo trades will use a pouch item as the reward; wire up when building Echoes.
- Player is a plain Character BP built via MCP (no template character). Jump velocity tuned to 450 by feel.
- Outline = post-process on Custom Stencil (not depth), amber, 2px; `r.CustomDepth=3` in DefaultEngine.ini.
