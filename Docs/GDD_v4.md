# Ashfall Again (formerly Alchemica: Origins) — Game Design Document v4

> The game is now titled **Ashfall Again**. See Decisions.md for all settled changes.

2026-10-15 · @Cris McCarthy

## About this document

v4 is the clean-rebuild baseline for Alchemica: Origins: the full game as it should exist at launch, written for a new project built from scratch. It supersedes Handoff v3, which documented the old top-down build.

The vision and the proven Blueprint patterns carry over. The systems are rebuilt cleaner, with every known v3 bug designed out. Milestones and Claude Code subagents are not defined here; they are planned separately against this document.

| Area | v3 | v4 |
| --- | --- | --- |
| Camera | Top-down | First person |
| Progression tension | Daily upkeep or machine shutdown | Regions reopened by bartering with Echoes or crafting clearing items |
| Economy | Upkeep Chest | Echoes: mysterious bartering figures across the map |
| Time | Always-on 10-minute day timer | Day/night cycle restored by a recipe or plot point; night has its own events |
| Robots | One hauler type | Four categories: Gatherer, Planter, Miner, Hauler |
| Recipes | Hidden "?????" list (never built) | Recipe Book with color tiers by how close each item is |
| Fuel | Coal and Charcoal | Charcoal only; Coal retired |
| Tone | Cozy, mechanical, mystery | Adds solarpunk |

Anything marked **Open** is deliberately unresolved for Cris to decide.

## Vision & pillars

Alchemica: Origins is a first-person solarpunk crafting and automation game: you wake in an overgrown world, rebuild it with machines and robot helpers, and slowly learn you have done this before.

**Pitch:** Stardew Valley's coziness meets Factorio's automation and Infinite Craft's combinations, wrapped in a gentle mystery.

**Tone:** cozy, mechanical, solarpunk, mysterious. Technology is warm and hopeful: moss-covered machines, robots that tend gardens, light from sun and crystal rather than smoke.

**Playtime:** 6 to 15 hours, depending on how much the player explores, customizes, and optimizes.

**Engine:** Unreal Engine 5, Blueprints only (no C++). Solo developer.

**Design pillars**

1. **Combine to discover.** Every new thing comes from putting two things together on the right machine surface.
2. **Build helpers, not grind.** Robots take over repetitive work so the player keeps exploring and creating.
3. **The world opens as you rebuild.** Progress is measured in regions reopened, not timers survived.
4. **The world feels like yours.** Room for layout, customization, and different play styles.
5. **Every craft is a clue.** The Recipe Book, the Echoes, and story items quietly tell the player who they are.

## Player experience & controls

The game is played in first person with an eye-level camera and a visible held item, so machines, robots, and the world are seen at human scale.

| Input | Action |
| --- | --- |
| WASD | Move |
| Mouse | Look |
| E | Interact with what you are looking at: machines, chests, robots, Echoes, resource nodes, saplings |
| Right mouse | Place the selected hotbar item, if it is placeable, where you are looking |
| Left mouse | Use the held tool (**Open:** whether gathering uses left click or E) |
| 1 to 0 | Select a hotbar slot |
| Ctrl | Switch hotbar row (at 20 slots) |
| Tab / Esc | Close the open menu |

- **Targeting:** interaction uses a line trace from the camera with a short reach (**Open:** about 250 units) and outlines the object you are aiming at, with its name and action shown near the crosshair.
- **Placement preview:** a ghost of the item follows the aim point; it reads valid or invalid before right-click confirms. Placement snaps to the ground and refuses spots that overlap other actors.
- **Menus:** any open menu stops movement, look, slot selection, and placement, and frees the mouse cursor. Closing it restores everything.

## Core gameplay loop

The minute-to-minute loop keeps v3's idea (gather, combine, discover, automate), but its payoff is now reopening the world instead of paying upkeep.

Loop: Gather → Combine on a machine → Discover in the Recipe Book → Automate with robots → Barter / clear a gate → New region.

Each pass ends in a new region, which brings new resources, a new robot tier, and the next piece of the story.

## World & map progression

The map is split into regions that start blocked; reopening them is the main measure of progress and replaces v3's daily upkeep as the source of goals.

Each region gate uses one of two unlock types:

- **Key gate:** an Echo barters a key for delivered materials. The key opens a door, gate, or barrier.
- **Clearing gate:** an obstacle (rubble, thorn wall, flooded path, dead machinery) is removed by crafting and using a specific item.

| Region (working title) | Era | Unlocked by | New resources |
| --- | --- | --- | --- |
| The Clearing (start) | 1 Clay | Open from the start | Wood, Dirt, Water, Berries, Stone |
| The Reed Marsh | 2 Iron | Key from an Echo | Reed, Copper |
| The Old Works | 3 Industrial | Clearing item (**Open**) | Rubber (**Open**) |
| The Grove of Lights | 4 Alchemical | Key from an Echo | Crystal, Salt |
| The Spire | 5 Plasma | Story clearing item (**Open**) | Plasma Ore |

Region names, order, and exact gate items are **Open**.

**Tools.** Better gathering tools are the second progression track. Tools are crafted or bartered and decide which nodes the player can harvest and how fast (for example, stone needs a pick, crystal needs a better one). The tool list is **Open**.

**Inside each region:** at least one Echo, environmental writing from past loops, the region's new resource nodes, and space for the player to build.

## The Echoes

Echoes are faded, flickering figures found across the map: memories of past loops given form. They are the game's economy and one of its main story voices.

**How bartering works**

- Each Echo stands at a fixed spot and offers a short list of trades: deliver items, receive something back.
- Rewards: region keys, tools, clues for the Recipe Book, rare materials, decorations, and memory fragments.
- One-time trades cover keys and story items; repeatable trades cover materials and decorations.
- The player delivers from the hotbar through the Echo's barter menu. Robots can later haul deliveries to an Echo's offering point (**Open**).
- Every completed trade unlocks a line of memory dialogue.

**Placement:** at least one Echo per region, plus hidden Echoes off the main path as optional discovery. Some appear only at night once the day/night cycle is restored.

**Story role:** each Echo remembers a different loop. Talked to in sequence, they piece together what happened. Exactly who they are is **Open**: the player's past selves, the people of the vanished civilization, or memories the Omega Robot has stored and projected outward.

## Crafting & machines

All crafting happens on machines, and the machine surface decides what a combination becomes: the same two items can make different things on different machines.

**Every machine follows one model**

- **Inputs:** two fixed slots, one item each. Single-input recipes leave slot 2 empty; same-item recipes such as Clay + Clay fill both.
- **Feeding:** the player feeds from the hotbar inside the machine menu; robots feed by depositing into the input slots.
- **Recipe list:** shows only recipes the current inputs satisfy, and refreshes the moment anything is added or withdrawn.
- **Processing:** the menu stays open while crafting; a second craft cannot start until the first finishes.
- **Output:** holds one item type, up to 5. Crafting is refused, with a popup and inputs untouched, when output is full or holds a different item.
- **Collect and withdraw:** output and unused inputs move to the player only after the player's inventory confirms it received them.
- **No upkeep:** machines never shut down for unpaid costs. Whether later-era machines burn fuel per craft (for example Charcoal in the Forge) is **Open**.

**Getting machines:** Era 1 machines are found in the Clearing as overgrown ruins; later machines are crafted and placed with right-click (**Open**: confirm).

**Recipe rule:** no two recipes on the same machine may share the same inputs, so every combination has exactly one result per machine.

## Discovery system & Recipe Book

The Recipe Book lists every item by category and uses color to show how close the player is to each one.

**Layout:** entries grouped by category (Resources, Materials, Fuel, Food, Tools, Machines, Robots, Story Items; list **Open**). A crafted entry shows its formula as icons, `item + item = item`, with the machine it was made on.

| State | How it looks | Rule |
| --- | --- | --- |
| Crafted | Full icon, gold/yellow border, full formula | The player has crafted it at least once |
| Discovered | Full icon, formula hidden | Obtained another way (gathered, bartered, picked up) but never crafted |
| Close | Bright tint, icon silhouetted | Every input is already discovered; one craft away |
| Warmer | Softer tint, silhouette | One or two crafts away using only discovered items |
| Locked | Grayed out with a ? | Its chain hasn't been entered, or it belongs to a locked region |

**Distance rule:** an entry's tier comes from the fewest crafts needed to make it from what the player has already discovered. Zero missing inputs is Close, one or two crafts away is Warmer, anything further is Locked. Tier colors are **Open**.

**Clues:** each undiscovered entry carries a one-line hint, revealed by Echo trades, notes found in the world, or reaching the Close tier. Clues hint at the machine or an ingredient without giving the full formula.

**Story hook:** in a later era some entries appear already filled in, in the player's own handwriting, for recipes they haven't made yet. The book remembers what the player forgot.

## Robot workers

Robots turn the player's repetitive work into automation, and each robot category does a different job.

| Robot | Job | Typical items |
| --- | --- | --- |
| Gatherer | Harvests trees, bushes, and reeds; carries the result to a set drop-off | Wood, Berries, Reed, Plant |
| Planter | Places saplings and feeds them Water or Berries to grow trees or bushes | Plant, Water, Berries |
| Miner | Mines stone, ore, and crystal nodes | Stone, Copper, Crystal, Plasma Ore |
| Hauler | Moves items between chests, machines, and Echo offering points | Whatever it is assigned |

**Building a robot:** craft a robot body, place it with right-click, then interact and feed it that era's story item to activate it. The first is the Clay Robot: Clay Robot Body + Ash Rune, and its eyes flicker on. Which body makes which category is **Open**.

**Assigning work**

- Interact with a robot to open its menu. It pauses while the menu is open and resumes the same task when closed.
- Set a route by aiming at a pickup and a drop-off and confirming each; selected targets are outlined.
- Haulers are told what to carry by feeding them one sample item, which is consumed. Higher tiers can carry a whole category or several types.
- Routes loop until changed. An empty source or full destination makes the robot wait and retry on a timer, quietly.
- Reassigning a robot mid-task returns any carried item to its source, or to the player if that fails. No item is ever destroyed.

**Upgrades:** each era tier raises Move Speed and Carry Capacity, and a robot can only handle items from its own era or earlier. The chain follows the story: Clay, Iron, Steel, Alchemical, Plasma, Omega. Stats live in data, never hardcoded.

**Running cost:** v3 robots ate Berry Syrup daily. With upkeep removed, whether robots need fuel or food at all is **Open**.

## Inventory, hotbar & placement

The player carries items in a positional hotbar: every item lives in a fixed slot, which is the foundation for drag-and-drop.

- **Capacity:** 5 slots at the start, upgraded to 10, then 20 (by crafted pouch or Echo trade; **Open**). At 20 there are two rows of 10; Ctrl switches the active row and number keys select within it.
- **Stacking:** the same item stacks in its slot (stack cap **Open**, e.g. 99). Emptying a slot leaves it empty in place; nothing shifts.
- **Selected slot:** the selected item is shown in the player's hand.
- **Placing:** right-click places the selected item if its data marks it placeable: Plant (as a sapling), robot bodies, machines, chests, decorations. Placeable items get a border on their hotbar icon.
- **Moving items:** drag-and-drop between hotbar slots, chests, machine slots, and Echo menus, Minecraft-style.
- **Storage:** chests hold unlimited items; the player can craft and place more.

**Planting:** placing a Plant makes a sapling that waits for a choice. Interact to open its menu and feed it: Water grows a Tree in 2 minutes, Berries grow a Berry Bush in 3 minutes. Later eras add more growth options.

## Day/night cycle & night events

Time does not pass at the start: the world sits in a still, dreamlike golden hour until the player restores the day/night cycle through a recipe or a plot point.

- **Restoring time is a story moment:** the sun moves for the first time, and the player realizes the world had been paused, waiting for them.
- **No penalties:** nothing is due at day's end and machines never shut down.
- **Night events:** Echoes that appear only after dark, night-only resources such as glowing plants, night-only recipes, and memory scenes that play out in the world. The event list is **Open**.
- **Dawn summary:** an optional readout of what robots gathered and crafted overnight (**Open**).

Which recipe or plot point restores time, and in which era, is **Open**.

## Eras & tech tree

Progress runs through five eras and a finale; each era opens with a new region and adds machines, materials, a story item, and a robot tier.

| Era | Theme | Machines | Story item | Robot tier |
| --- | --- | --- | --- | --- |
| 1 Clay | Earth and fire | Campfire, Mixing Bowl, Flat Stone | Ash Rune | Clay Robot |
| 2 Iron | Metal and kilns | Kiln, Forge | Memory Shard | Iron Robot |
| 3 Industrial | Steam and wiring | Steam Press, Wiring Bench | Blueprint Scroll | Steel Robot |
| 4 Alchemical | Crystal and essence | Alchemist Table, Crystal Lens, Cauldron | Philosopher's Stone | Alchemical Robot |
| 5 Plasma | Light and energy | Plasma Lab, Tech Bench | Resonance Crystal | Plasma Robot |
| Finale | Memory | Robot assembly | The First Memory (**Open**) | Omega Robot |

**Charcoal replaces Coal** everywhere as the unified fuel and gate material.

**Robot assembly** is not a separate machine: a robot frame is placed in the world and the story item is fed to it through its own menu, the same way the Clay Robot is activated.

**Solarpunk direction:** later eras should read as clean energy (sun, water, crystal, light) rather than smoke and soot. Renaming machines to fit is **Open**.

## Recipe compendium

Every recipe has at most two inputs and one output, matching the two-slot machines; process times are tuned during the build.

Fixed from v3: Coal became Charcoal; three-input robot recipes were split into two steps; undefined inputs (Clay Rune, Fire, Antenna) were replaced or given recipes; duplicate inputs on one machine were resolved.

### Era 1: Clay

| Machine | Input A | Input B | Output |
| --- | --- | --- | --- |
| Campfire | Wood |  | Charcoal |
| Campfire | Plant |  | Charcoal |
| Campfire | Charcoal |  | Ash |
| Campfire | Ash |  | Dust |
| Campfire | Dust |  | Sand |
| Campfire | Mud |  | Clay |
| Campfire | Clay Slab |  | Fired Clay Plate |
| Campfire | Moss |  | Dried Herb |
| Campfire | Berry Juice |  | Berry Syrup |
| Mixing Bowl | Sand | Water | Mud |
| Mixing Bowl | Dirt | Water | Mud |
| Mixing Bowl | Wood | Water | Moss |
| Mixing Bowl | Berries | Water | Berry Juice |
| Mixing Bowl | Moss | Dirt | Soil |
| Mixing Bowl | Soil | Water | Plant |
| Mixing Bowl | Ash | Water | Lye |
| Mixing Bowl | Charcoal | Water | Ink |
| Mixing Bowl | Dried Herb | Water | Herbal Brew |
| Flat Stone | Clay | Clay | Clay Slab |
| Flat Stone | Clay Slab | Ash | Ash Rune (story) |
| Flat Stone | Clay Slab | Fired Clay Plate | Clay Robot Body |
| Flat Stone | Dried Herb | Dried Herb | Herb Powder |
| Robot assembly | Clay Robot Body | Ash Rune | Clay Robot |

### Era 2: Iron

| Machine | Input A | Input B | Output |
| --- | --- | --- | --- |
| Flat Stone | Reed | Reed | Papyrus |
| Flat Stone | Papyrus | Ash | Parchment |
| Flat Stone | Ink | Parchment | Written Note |
| Flat Stone | Written Note | Written Note | Recipe Page |
| Kiln | Clay |  | Brick |
| Kiln | Sand |  | Glass |
| Kiln | Clay | Clay | Earthen Brick |
| Kiln | Clay | Water | Pottery |
| Kiln | Glass | Water | Crystal Vial |
| Kiln | Brick | Brick | Stone Block |
| Kiln | Clay Slab |  | Ceramic Tile |
| Forge | Charcoal | Stone | Iron |
| Forge | Iron | Charcoal | Steel |
| Forge | Iron | Copper | Bronze |
| Forge | Steel | Stone | Blade |
| Forge | Iron | Stone | Nail |
| Forge | Iron | Iron | Chain |
| Forge | Steel | Steel | Iron Pipe |
| Forge | Iron | Clay | Iron Mold |
| Forge | Iron | Ash Rune | Memory Shard (story) |
| Forge | Clay Robot | Iron | Iron Robot Frame |
| Robot assembly | Iron Robot Frame | Memory Shard | Iron Robot |

### Era 3: Industrial

| Machine | Input A | Input B | Output |
| --- | --- | --- | --- |
| Steam Press | Steel | Charcoal | Engine Part |
| Steam Press | Engine Part | Iron Pipe | Steam Engine |
| Steam Press | Plant | Water | Fiber |
| Steam Press | Fiber | Fiber | Rope |
| Steam Press | Steel | Steel | Steel Beam |
| Steam Press | Steel | Iron | Armor Plate |
| Steam Press | Charcoal | Fiber | Carbon Rod |
| Steam Press | Armor Plate | Speed Circuit | Steel Plating |
| Steam Press | Iron Robot | Steel Plating | Steel Robot Frame |
| Wiring Bench | Copper | Steel | Wire |
| Wiring Bench | Wire | Crystal Vial | Circuit |
| Wiring Bench | Circuit | Wire | Electrical Core |
| Wiring Bench | Wire | Rubber | Insulated Wire |
| Wiring Bench | Insulated Wire | Iron | Coil |
| Wiring Bench | Circuit | Circuit | Speed Circuit |
| Wiring Bench | Wire | Steel Beam | Antenna |
| Wiring Bench | Parchment | Electrical Core | Blueprint Scroll (story) |
| Robot assembly | Steel Robot Frame | Blueprint Scroll | Steel Robot |

### Era 4: Alchemical

| Machine | Input A | Input B | Output |
| --- | --- | --- | --- |
| Cauldron | Plant | Water | Life Water |
| Cauldron | Berries | Ash | Berry Dye |
| Cauldron | Berry Juice | Dried Herb | Fermented Berry |
| Cauldron | Herb Powder | Life Water | Revival Serum |
| Cauldron | Charcoal | Salt | Activated Charcoal |
| Cauldron | Wild Yeast | Water | Fermentation Brew |
| Cauldron | Alchemic Ink | Life Water | Alchemic Essence |
| Alchemist Table | Crystal | Herbal Brew | Reagent |
| Alchemist Table | Reagent | Water | Tincture |
| Alchemist Table | Tincture | Life Water | Elixir Base |
| Alchemist Table | Elixir Base | Crystal | Strength Potion |
| Alchemist Table | Herb Powder | Ink | Alchemic Ink |
| Alchemist Table | Crystal Vial | Life Water | Soul Vessel |
| Alchemist Table | Fermented Berry | Herb Powder | Wild Yeast |
| Alchemist Table | Alchemic Ink | Parchment | Spell Scroll |
| Alchemist Table | Steel Robot | Alchemic Essence | Alchemical Robot Frame |
| Crystal Lens | Glass | Crystal | Lens |
| Crystal Lens | Lens | Lens | Focus Array |
| Crystal Lens | Crystal | Charcoal | Crystal Shard |
| Crystal Lens | Soul Vessel | Crystal Shard | Soul Anchor |
| Crystal Lens | Focus Array | Alchemic Ink | Cipher Key |
| Crystal Lens | Soul Anchor | Cipher Key | Philosopher's Stone (story) |
| Robot assembly | Alchemical Robot Frame | Philosopher's Stone | Alchemical Robot |

### Era 5: Plasma

| Machine | Input A | Input B | Output |
| --- | --- | --- | --- |
| Plasma Lab | Plasma Ore | Electrical Core | Refined Plasma Ore |
| Plasma Lab | Refined Plasma Ore | Focus Array | Plasma |
| Plasma Lab | Plasma | Steel Beam | Plasma Tool |
| Plasma Lab | Plasma | Carbon Rod | Plasma Welder |
| Plasma Lab | Plasma | Armor Plate | Plasma Shield |
| Plasma Lab | Plasma | Circuit | Plasma Flow Cell |
| Tech Bench | Steel | Plasma | Alloy |
| Tech Bench | Alloy | Alloy | Titanium |
| Tech Bench | Plasma Flow Cell | Circuit | Fuel Cell |
| Tech Bench | Titanium | Circuit | Exo-Frame |
| Tech Bench | Exo-Frame | Plasma Shield | Mech Suit |
| Tech Bench | Circuit | Antenna | AI Core |
| Tech Bench | AI Core | Philosopher's Stone | Resonance Crystal (story) |
| Tech Bench | Alchemical Robot | Mech Suit | Plasma Robot Frame |
| Robot assembly | Plasma Robot Frame | Resonance Crystal | Plasma Robot |

### Finale

The final items depend on the ending and are **Open**. v3's placeholder: five Memory Fragments, gathered from Echoes and the world, plus the Resonance Crystal make The First Memory; the Plasma Robot plus The First Memory becomes the Omega Robot.

## Story bible

The player has lived this loop before: the vanished civilization that built this world was them, and every craft is a memory coming back.

**Premise:** the player wakes alone in an overgrown solarpunk world full of dormant machines. With a campfire already burning and bare hands, they gather, craft, and rebuild era by era until they assemble the Omega Robot.

**The twist:** every run, every recipe, every robot fed memories into the Omega Robot. The Recipe Book is the breadcrumb the player left themselves. The Omega Robot was never the goal; it was always the messenger.

**How the story is told**

- **Story items:** one per era, each revealing more.
- **Robot dialogue:** robots grow more aware each era and begin to recognize the player.
- **Echoes and memories:** bartering figures who each remember a different loop.
- **Environmental writing:** messages in the player's own handwriting, a fire that was already lit, ruins that match things the player builds.
- **The Recipe Book:** entries that fill in before the player has made them.

| Era | Story item | What it reveals |
| --- | --- | --- |
| 1 Clay | Ash Rune | The robot wakes. The symbols feel familiar: not learned, remembered. |
| 2 Iron | Memory Shard | First robot speech: "You have been here before." "I just arrived." "Yes. You always say that." |
| 3 Industrial | Blueprint Scroll | The robot produces it unprompted, in the player's handwriting: "Don't stop this time." |
| 4 Alchemical | Philosopher's Stone | Recipe Book entries appear pre-filled. "Seventeen times. You always hesitate here." |
| 5 Plasma | Resonance Crystal | All robots speak at once: "We remember you." A hologram of a city: "Home." |
| Finale | The First Memory (**Open**) | Everything: the history, the loop, the player's identity. |

**Key beats carried from v3**

- Opening: "Strange. The fire was already lit when you arrived. You don't remember lighting it."
- A wall in charcoal, in the player's handwriting: "THE RECIPE BOOK REMEMBERS WHAT YOU MAKE YOURSELF FORGET."
- The Ash Rune is the first item crafted and the last item received, handed back by the Omega Robot.
- Post-credits: the campfire burns alone; a figure walks in and sits down; the opening line repeats.

### The ending (Open, for Cris to write)

**Why did the player go back and start over?**

**How and why did they forget?**

**Who or what are the Echoes?**

**What restores the day/night cycle, and what does it mean?**

**What are the final items, and what does the Omega Robot say?**

**Does the player break the loop, or choose it again?**

## Art & audio direction

The look is solarpunk ancient technology reclaimed by nature: machines and robots that seem to have always been here, dormant in the forest, with nature grown around them.

- **Palette and light:** warm golden-hour tones, greens and earth, with teal and amber glows from technology. No cold blue-white.
- **Solarpunk cues:** living roofs, vines on metal, sun-catching glass, crystal and water as power sources, gardens tended by robots.
- **First-person readability:** machines need clear silhouettes at eye level, a visible interaction outline, and readable in-world labels. Every item needs a hotbar and Recipe Book icon.
- **Asset packs owned:** Synty Polygon Nature Pack (environment) and Sci-Fi Robot + Attachments and Props from Fab (robots, later-era machine props).

| Robot tier | Look |
| --- | --- |
| Clay | Dull terracotta, mossy, primitive; nearly blends into rocks |
| Iron | Dark metal, patchy moss on joints, vines at the base |
| Steel | Cleaner silver, mechanical attachments |
| Alchemical | Glowing core, dark armor, alchemical symbols; glowing flora nearby |
| Plasma | Teal electric glow, crackling energy |
| Omega | All systems lit; the world reacts to it |

**Asset lessons from v3:** import a pack into a throwaway project and migrate only what is needed, to keep the main project and Git small. Duplicate a pack's base material before editing it. Aged clay comes from a noise-driven blend of two browns plus roughness between about 0.7 and 0.95; lower roughness reads as shiny black patches. Fix pivots at the base in Blender rather than offsetting them in Blueprints.

**Audio:** **Open**. Direction: soft nature ambience, warm machine hums, a distinct whisper or chime for Echoes, and a change in the soundscape when time is restored.

## Technical architecture & Blueprint rules

The rebuild is data-driven and built on shared parent classes and interfaces, which removes the root causes of v3's bugs: string typos, duplicated per-machine code, cast chains, and inconsistent item transfers.

**Data (Data Tables, not hardcoded values)**

| Table | Row struct fields |
| --- | --- |
| DT_Items | ItemID (row name), DisplayName, Category, Era, Icon, Description, Clue, IsPlaceable, PlaceClass, StackMax |
| DT_Recipes | Machine, InputA, InputB, Output, ProcessTime |
| DT_RobotStats | Tier, MoveSpeed, CarryCapacity, MaxItemEra |
| DT_Barters | EchoID, GiveItem, GiveAmount, RewardItem, RewardAmount, OneTime, MemoryLine |

Items are referenced by their DT_Items row name, never a free-typed string. This ends v3's casing and spelling bugs ("Output Inventroy", "Fired clay plate").

**Class structure**

- **BP_MainGameMode:** the one GameMode. Region unlock state, discovery state, time state.
- **BP_MachineBase:** shared input slots, output, crafting, and menu logic. Each machine is a child that only sets its machine ID and mesh.
- **BP_RobotBase:** shared state machine (Idle, MovingToSource, AtSource, MovingToDest, AtDest), pause/resume, routes. Gatherer, Planter, Miner, and Hauler are children that change the action.
- **BP_ContainerBase:** chests, including any special chests, as children.
- **WBP_MachineMenuBase:** one parent menu widget; per-machine menus only restyle it.
- **BP_Echo:** reads its trades from DT_Barters.

**Interfaces (replace v3's cast chains)**

- **BPI_Interactable:** Interact, GetInteractPrompt.
- **BPI_ItemContainer:** TryAddItem, TryRemoveItem, GetItemCount, HasRoomFor. Implemented by the player, chests, machines, robots, and Echo offering points.
- **TransferItem(Source, Destination, Item, Amount):** the only way items move. It adds to the destination first and removes from the source only on success.

**Blueprint rules (law)**

1. In widgets, drag Get nodes off the cast result pin; never use a loose Get. Never give a widget a variable with the same name as its owner's.
2. Compute a value once and SET it into a variable before downstream nodes read it; pure nodes re-evaluate.
3. Branch before ADD or REMOVE so exactly one runs.
4. Change the source only after the destination confirms it received the item.
5. Never destroy an item without an explicit disposal path, including on robot reassignment.
6. Clear any filtered recipe list at the start of every refresh.
7. Delays only in Custom Events or timers, never in Functions.
8. ADD on a Map replaces the value; compute the new total yourself.
9. Every actor that belongs in a GameMode list registers itself in BeginPlay (v3's Mixing Bowl never did).
10. Movement inputs use the default trigger; a Pressed trigger fires once and breaks held movement.
11. Retries run on timers, not tight loops, and never spam the log. Remove debug prints before a milestone closes.
12. A clean compile is not proof of correct wiring; check pins after compiling and PIE test every step.

**Tooling notes:** Claude Code with Epic's Unreal MCP plugin. The MCP tools cannot create structs, enums, Data Tables, or interface implementations; make those by hand. One Claude Code session edits Blueprints at a time. Commit or back up before any large change.

## Open design questions

These are the decisions this baseline leaves to Cris; tick them off as they are settled.

**Story**

- [ ] Who or what the Echoes are
- [ ] Why the player went back, and how and why they forgot
- [ ] The final items, the Omega Robot's words, and whether the loop breaks
- [ ] Which recipe or plot point restores the day/night cycle, and in which era

**World & progression**

- [ ] Region names, order, and the exact key or clearing item for each gate
- [ ] Gathering tool list and tiers
- [ ] Night event list and night-only resources
- [ ] Whether Era 1 machines are found or crafted

**Systems**

- [ ] Whether gathering uses left click or E
- [ ] Interaction reach (about 250 units proposed)
- [ ] Hotbar stack cap and how capacity upgrades are obtained
- [ ] Recipe Book categories and tier colors
- [ ] Which robot body makes which category
- [ ] Whether robots or later machines need fuel or food
- [ ] Whether robots can deliver to Echo offering points
- [ ] Solarpunk renames for later-era machines
- [ ] Audio direction
