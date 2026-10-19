# Handoff

Update this file at the end of every session. Keep it under one page.

**Current milestone:** M2 · Player basics — complete. Next: M3 · Interaction
**Last session:** 2026-10-18
**Last commit:** see git log (M2 commit pending Cris)

## State
- M0, M1 done (see git log).
- M2 done: first-person walk, look, jump verified in PIE; held W is continuous.
- `Player/BP_AshfallCharacter` (Character): `FirstPersonCamera` at Z 64, variables `WalkSpeed` 400, `JumpVelocity` 450, `LookSensitivity` 1.0 applied in BeginPlay. Functions `ApplyMove`, `ApplyLook`.
- Input in `Player/Input/`: `IA_Move`, `IA_Look`, `IA_Jump`, `IMC_Ashfall` (no triggers on mappings).
- `Core/BP_MainGameMode` (Default Pawn = BP_AshfallCharacter) and `Core/BP_AshfallGameInstance` (empty). Both set in Project Settings.

## Known issues / bugs
- Template input actions still exist in `/Game/Input/Actions` (same names as ours). When adding input events in the BP, pick the `Ashfall/Player/Input` ones.

## By-hand steps waiting for Cris
- Commit M2.

## Not built (deferred from BuildPlan M2 text)
- Slot keys 1–0, Ctrl row switch, Tab/Esc: add when their consumers exist (M5 hotbar, UI milestones).

## Next session starts with
- M3: camera line trace (reach 250 from data), outline, prompt via BPI_Interactable, test cube. Confirm "Done when" with Cris first.

## Decisions made during the build
- Item and recipe references are `Name` fields holding DT_Items row names (not Data Table Row Handles); CSV check enforces them.
- Recipe row names are `Machine_InputA[_InputB]`. Robot assembly uses `E_MachineID.RobotAssembly`.
- Not yet in data: RobotFuelValue (all 0), BuffID (blank), Clue (blank), robot hungry-speed field, robot stats and barters rows.
- Hotbar-upgrade Echo trades will use a pouch item as the reward; wire up when building Echoes.
- Player is a plain Character BP built via MCP (no template character). Jump velocity tuned to 450 by feel.
