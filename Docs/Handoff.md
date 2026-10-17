# Handoff

Update this file at the end of every session. Keep it under one page.

**Current milestone:** M1 · Data foundation — complete. Next: M2 · Player basics
**Last session:** 2026-10-17
**Last commit:** see git log (M1 commit pending Cris)

## State
- M0 done (project, Git + LFS, MCP plugin, docs).
- M1 done: 8 enums, 5 structs, 2 interfaces, 4 Data Tables in `Content/Ashfall/Data/` and `Interfaces/`.
- DT_Items (26 rows) and DT_Recipes (23 rows) imported from `Data/DT_Items_Era1.csv` and `Data/DT_Recipes_Era1.csv`. DT_RobotStats and DT_Barters exist and are empty.

## Done this session
- Added E_UseType and E_ToolType (needed by S_ItemRow).
- S_ItemRow has `IconText` after `Icon`; UI shows IconText while Icon is empty (Decisions #51, CLAUDE.md).
- CSV check: no missing item refs, no duplicate inputs per machine, IconText unique (2–4 chars).

## Known issues / bugs
- None.

## By-hand steps waiting for Cris
- Commit M1 (enums, structs, interfaces, tables, CSVs, doc edits).

## Next session starts with
- M2: BP_AshfallCharacter, Enhanced Input, BP_MainGameMode, BP_AshfallGameInstance. Confirm "Done when" with Cris first.

## Decisions made during the build
- Item and recipe references are `Name` fields holding DT_Items row names (not Data Table Row Handles); CSV check enforces them.
- Recipe row names are `Machine_InputA[_InputB]`. Robot assembly uses `E_MachineID.RobotAssembly`.
- Not yet in data: RobotFuelValue (all 0), BuffID (blank), Clue (blank), robot hungry-speed field, robot stats and barters rows.
- Hotbar-upgrade Echo trades will use a pouch item as the reward; wire up when building Echoes.
