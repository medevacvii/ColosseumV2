# Recreate the first-century Roman Colosseum in Unreal Engine

A beginner's build guide for ColosseumV2, with historical uses and significant events.

**Recommended target: approximately AD 90, during Domitian's reign.** This suits a working underground backstage area, lifts and trapdoors. An AD 80 opening-day reconstruction is a different historical phase. This guide proposes a playable interpretation, not an archaeological survey. No Unreal assets are created by this document.

Companion: [One main map, OFPA and World Partition](One-File-Per-Actor-Guide.md).

## 1. Pick your historical version before building

| Version | What to build | What to avoid |
| --- | --- | --- |
| AD 80, opening under Titus | New amphitheatre, intact seating and arena; early underground arrangements | Copying the fully developed surviving masonry hypogeum into opening day |
| Around AD 90, under Domitian — recommended | Intact amphitheatre plus a plausible masonry backstage system | Claiming all surviving underground walls and lift arrangements belong to this exact year |
| Modern ruins | Exposed underground masonry, missing arena and damaged outer wall | Presenting today's ruin as its first-century appearance |

The archaeological park describes wooden underground structures at inauguration, transformed into masonry under Domitian. Later rebuilding changed the monument further. Do not interpret “Domitian added the hypogeum” as proof that nothing existed beneath the original floor. [S1]

**Your underground prison:** build the historical area as backstage corridors, holding spaces and machinery. If your game needs a large prison, put it in a clearly labelled **fictional extension**. Its exact cells, torture rooms, escape tunnels and trap locations should not be presented as excavated historical fact.

**First decision:** write `Target year: approximately AD 90` in your project notes. Every teammate uses that same target.

## 2. Learn what the building was for

| Use | What visitors would experience | Suggested game/learning scene |
| --- | --- | --- |
| Gladiatorial contests, or munera | Trained combatants facing one another before a crowd | A staged bout with an explanation of equipment and the possibility of reprieve |
| Animal hunts and displays, or venationes | Animals and specialist performers in arena spectacles | A narrated backstage/lift demonstration; animated animals can be added later |
| Public punishment and executions | State violence presented as spectacle, sometimes with mythological framing | An optional historical explanation rather than making graphic reenactment necessary |
| Imperial presentation | The emperor sponsoring spectacles and appearing before the people | A ceremonial arrival and narrated explanation of patronage |
| Social hierarchy | Access and seating reflecting status | A route comparing a privileged seat with an upper-tier view |
| Backstage operations | Movement of performers, animals, scenery and workers | Operate a simplified winch and raise an empty platform |
| Aquatic spectacles in the early period | Ancient accounts describe water displays; location and engineering remain debated | A separate, labelled interpretation of the AD 80 phase |

For gladiator types, armor and the connection with the Ludus Magnus training complex, use the park's archaeological exhibition. Its eastern underground gallery connected the arena with the barracks. Do not turn the amphitheatre into a chariot racetrack: that is a different building type. [S2]

Aquatic displays need particular care: ancient accounts support discussion of them, but their exact arena arrangement and water system are uncertain. Do not routinely flood an AD 90 masonry hypogeum as though it were an established operating mode. [S3]

## 3. Gather a small reference set

1. Make a project reference folder outside the packaged game assets.
2. Collect one plan, one cross-section, an exterior view, a seating view and an underground plan.
3. Record each source URL, the date/phase represented, and permission to reuse the image.
4. Treat photographs of ruins as evidence of surviving fabric, not a complete image of AD 90.
5. Add a simple evidence label to your notes: **supported**, **interpreted**, or **fictional**.
6. Use archaeological plans and published reconstructions for final geometry. Film screenshots and AI images can suggest mood; they are not measurement evidence.

Start with the sources at the end of this guide. Do not download a modern 3D model and assume its missing walls or exposed basement are first-century features.

## 4. Set up the Unreal workspace

1. Fetch your team's latest `main` after the shared-map setup has been merged.
2. Create a feature branch, such as `feature/colosseum-blockout`.
3. Open ColosseumV2 using the team's agreed engine version and plugins. The checked-in project targets Unreal 5.8.
4. Open the tested main map from the companion guide; otherwise prepare a working copy of `L_XRTemplate` first.
5. Enable OFPA using that guide. If planning World Partition, adopt it before extensive parallel construction; begin with streaming off.
6. In the Content Browser, create `Content/Colosseum` and these subfolders: `Maps`, `Meshes`, `Materials`, `Blueprints`, `Audio`, `UI`.
7. In the Outliner create folders: `Reference`, `Arena`, `Facade`, `Seating`, `Circulation`, `Hypogeum`, `Machinery`, `Lighting`, `HistoricalTour`, `FictionalPrison`.
8. Keep existing XR input and game-mode configuration until the new map passes a play test.

Outliner folders organize actors; they do not independently save every component or prevent overlapping edits.

## 5. Establish scale with four markers

Use centimeters: **100 Unreal units = 1 metre** under the usual centimeter-based project convention.

| Feature | Initial blockout target | Unreal measurement | Status |
| --- | --- | --- | --- |
| Outer long axis | About 189 m | 18,900 cm | Rounded overall envelope [S4] |
| Outer short axis | About 156 m | 15,600 cm | Rounded overall envelope [S4] |
| Overall height | About 48 m | 4,800 cm | Initial approximation; verify chosen survey/reference |
| Playable arena long axis | 80 m | 8,000 cm | Proposed blockout value, not a survey claim |
| Playable arena short axis | 48 m | 4,800 cm | Proposed blockout value, not a survey claim |
| Initial service-floor depth | 6 m below arena | Z = -600 cm | Gameplay starting value; verify against section drawings |

Published arena measurements differ by source and boundary definition. Freeze one documented footprint before final art; do not silently combine incompatible plans. The oval below is a convenient mathematical ellipse, not a claim about the ancient setting-out method.

1. Set arena floor height to Z = 0.
2. Put the building center at X = 0, Y = 0.
3. Add four temporary marker actors at `(9450,0,0)`, `(-9450,0,0)`, `(0,7800,0)`, `(0,-7800,0)`.
4. Label them East/West/North/South according to your chosen local layout. These labels are not yet a surveyed geographic orientation.
5. Add a human-scale reference mannequin.
6. Walk in XR. Check that a metre feels like a metre before duplicating hundreds of pieces.

## 6. Draw the oval without starting with fancy modeling

Build a rough outline first. For a point at angle t:

`X = 9450 × cos(t)` and `Y = 7800 × sin(t)`.

The values below are marker locations in centimeters, rounded for blockout:

| Angle | X | Y |
| --- | --- | --- |
| 0° | 9450 | 0 |
| 30° | 8184 | 3900 |
| 60° | 4725 | 6755 |
| 90° | 0 | 7800 |
| 120° | -4725 | 6755 |
| 150° | -8184 | 3900 |
| 180° | -9450 | 0 |
| 210° | -8184 | -3900 |
| 240° | -4725 | -6755 |
| 270° | 0 | -7800 |
| 300° | 4725 | -6755 |
| 330° | 8184 | -3900 |

1. Add small marker cubes at those coordinates using **Details > Transform > Location**.
2. Join neighboring markers with rough wall blocks to check the silhouette. Twelve segments are deliberately coarse.
3. Mark a smaller arena oval using half-axes 4,000 cm and 2,400 cm.
4. Keep the space between the two ovals for seating and circulation.
5. Replace the rough outline with more points and actual modules after the first walking test.

Equal angle increments on an ellipse do **not** produce equal-length bays. Do not rotate one rectangular arch around the origin at a fixed radius and expect the exterior to stay oval. For final work, place bays against a measured plan or an approximately equal-distance spline layout, and align each bay along the local curve.

## 7. Make the arena floor playable

1. Use simple floor meshes for the first pass. A rectangular collection inside the arena outline is acceptable during blockout.
2. Add a sand-colored material.
3. Divide the floor near each planned trapdoor into separate pieces around a real opening.
4. Add a temporary railing or visible marker around unfinished holes during testing.
5. Place an arena boundary wall and two broad main-axis access passages as initial placeholders; verify their final proportions against your plan.
6. Play-test walking, turning and teleporting. Check collisions at every floor seam.
7. Later replace the rough perimeter with a properly fitted oval mesh and a sand-over-timber interpretation.

Do not hide a solid floor beneath the trapdoors: the opening must exist in collision as well as in the image.

## 8. Build one seating section before the whole bowl

1. Choose one small wedge between two circulation routes.
2. Build a few rising rows from simple blocks.
3. Reserve separate walking space for the radial stair and horizontal circulation.
4. Place a viewpoint at the bottom, middle and top of this section.
5. Confirm spectators can see the arena and that the slope looks credible against your section reference.
6. Duplicate the section only after adjusting it to the oval. Inner and outer edges have different lengths.
7. Introduce accessible game routes deliberately; label modern accessibility adaptations rather than presenting them as ancient design.

Represent social seating divisions through your source plan and interpretation. Avoid filling the whole bowl with identical individual modern stadium chairs.

## 9. Build one exterior arch module

1. In `Meshes`, make/import a simple arch bay: two piers and a curved arch above the opening.
2. For a blockout, form the opening from separate blocks; add a proper curved mesh later in Unreal Modeling Mode or your modeling application.
3. Save reusable geometry as a Static Mesh, such as `SM_ArchBay_Blockout`.
4. Place separate Static Mesh Actors for bays that teammates need to edit independently.
5. Fit a short run to the oval and check for gaps before extending it.
6. Build the facade as three arcaded stories and an upper attic wall, following a documented elevation.
7. Use the broad architectural progression of lower Doric/Tuscan, middle Ionic and upper Corinthian treatments as an art-reference task; refine the capitals after the massing works.
8. Reconstruct an intact perimeter for AD 90. Do not reproduce the modern missing outer-wall section.

Do not put the entire facade into one giant Blueprint with hundreds of components if several builders need separate ownership. Shared mesh edits still affect every instance, so assign an owner for each module asset.

## 10. Connect entrances, stairs and galleries

1. Draw a simple visitor route: exterior entrance → gallery → stair → seating section.
2. Build that route at full scale before repeating it.
3. Connect service circulation separately from spectator routes where your reference supports it.
4. Use temporary color-coded materials to distinguish spectator, staff and restricted routes.
5. Walk from outside to a seat and back without flying through walls.
6. Replace temporary colors with stone, plaster and signage once the route works.

Use the term **vomitorium** for a spectator access passage, not a room for vomiting. Make the space work as circulation before adding decorative clutter.

## 11. Add the hypogeum and your fictional prison

1. Place the first service floor at approximately Z = -600 cm for gameplay testing.
2. Block out a main service passage, several side spaces and one lift shaft from your chosen archaeological plan.
3. Verify ceiling clearance against the arena-floor structure above it.
4. Add separate wall, floor and doorway actors; name them by location.
5. Keep the original documented layout distinguishable from invented gameplay corridors.
6. Add `FictionalPrison` beyond the reconstructed service area if your game needs cells and an escape objective.
7. Add an in-game note or tour narration identifying that extension as fictional.
8. Keep a return route to the arena for testing.

Historical holding spaces do not justify claiming a vast permanent prison complex beneath the arena. Retain the game idea; be honest about the evidence.

## 12. Make one lift and one trapdoor work

1. Follow the companion guide to make `BP_Trapdoor` with a hinge and moving panel.
2. Create an Actor Blueprint named `BP_ArenaLift`.
3. Add a stationary root and a movable platform mesh with suitable collision.
4. Create instance-editable `LowerHeight` and `UpperHeight` values, measured in centimeters relative to the lift root.
5. Add a `RaiseLift` event. Animate the platform's relative Z from the lower value to the upper value over several seconds using a Timeline.
6. Add a `LowerLift` event for the reverse trip. Refuse new commands while the platform is already moving.
7. Keep the first test empty. Verify clearance and collision before testing an actor riding on it.
8. Coordinate the door and lift: open the door, raise the platform, and prevent closure through an occupied opening.
9. Make the mechanism's state explicit: Lowered, Door Opening, Raising, Raised, Lowering, Door Closing.
10. Add a winch mesh and rope animation only after the movement is reliable. Label this as a simplified interpretation of the machinery, not an exact recovered mechanism.

The park describes freight elevators, winches, pulleys and trapdoors supporting surprise appearances. Specific machinery layouts changed over time. [S1]

For XR, a moving mesh does not guarantee the pawn rides correctly. Test your actual locomotion system; use a controlled transition if continuous vertical motion proves uncomfortable. Do not use lift movement to induce forced head motion.

## 13. Add materials, color and shade

1. Make a small material set: pale stone, brick, plaster, marble, timber, sand and metal.
2. Apply them to one finished bay and one seating wedge first.
3. Use references for decorative color; do not assume the original building looked like uniformly weathered modern ruins.
4. Add the upper awning system, the **velarium**, as a separately researched reconstruction. Start with static canvas sectors and supports.
5. Keep the arena open to the sky; do not add a modern solid stadium roof.
6. Tune lighting on the target device. Check underground readability without making every corridor brightly sunlit.

Exact paint, textile patterns, statue placement and awning rigging require their own evidence records. Mark conjectural details rather than hiding the guesswork beneath attractive textures.

## 14. Add significant events as a narrated timeline

| Date/period | Historical significance | Buildable scene | Evidence note |
| --- | --- | --- | --- |
| Around AD 70–72 | Construction began under Vespasian | A timeline card and simplified construction comparison | Approximate starting date; do not invent one precise day [S4, S5] |
| AD 80 | Titus inaugurated the amphitheatre with a long program of games, traditionally described as 100 days | Opening ceremony, crowd sound and an arena tour | The park describes the centenary-length opening program [S6] |
| Opening games, conventionally AD 80 | Martial celebrates the evenly matched contest of Priscus and Verus and awards to both | A narrated tableau ending in both receiving wooden swords | Literary praise of imperial generosity, not a modern match transcript [S7] |
| AD 81–96 | Domitian's reign brought development of the underground masonry system | A comparison of early wooden arrangements with your later backstage interpretation | Do not date every surviving wall to his reign [S1] |
| AD 217 — later context only | A major fire damaged the building; the park describes closure until AD 222 | An optional timeline entry outside the first-century playable world | Do not add this destruction to an AD 90 reconstruction [S1, S8] |

Keep the first-century playable map internally consistent. Use separate maps or explicitly switched historical presentations for different eras. Do not put an AD 80 inauguration, a mature Domitianic underground system and later ruin damage into one scene and label it a single historical moment.

The aquatic-spectacle question belongs in an **interpretation panel** or separate early-phase scenario. Explain uncertainty instead of presenting a precise flooding machine as established fact. [S3]

Do not add Commodus as the reigning emperor in a first-century scene: his reign belongs to the second century. Do not present specific named Christian martyrdoms in this building as established without event-specific evidence.

## 15. Turn the research into a short learning tour

Create these stops before attempting a full combat game:

1. **Outside:** name the Flavian patrons and identify the chosen year.
2. **Entrance/gallery:** explain how people reached different seats.
3. **Seating:** compare status, sightlines and crowd scale.
4. **Arena:** explain gladiatorial contests, animal spectacles and public punishment.
5. **Backstage:** demonstrate one empty lift and a trapdoor.
6. **History station:** explain the opening games and show Priscus/Verus as a sourced literary episode.
7. **Optional extension:** identify the fictional prison before the player enters it.

For a simple implementation, create `BP_HistoryStop` with a visible marker and an overlap trigger. Give it instance-editable title, explanation and optional audio fields. On a deliberate interaction, show the text and play the narration; include stop/replay controls. Keep content in data or instance settings where possible so changing narration does not require editing the shared Level Blueprint.

Include audio plus short captions. The proposed narration and tour order are modern teaching choices, not an ancient event schedule.

## 16. Divide the work and test the result

| Work area | Suggested branch | Shared files to coordinate |
| --- | --- | --- |
| Main footprint and scale | `feature/colosseum-blockout` | Main map settings and reference plan |
| Exterior modules | `feature/roman-facade` | Shared arch mesh/materials |
| Seating and routes | `feature/cavea-circulation` | Seating module meshes |
| Underground | `feature/hypogeum` | Shared wall modules and historic plan |
| Lift/door behavior | `feature/arena-machinery` | BP_ArenaLift and BP_Trapdoor |
| Tour and narration | `feature/historical-tour` | History-stop Blueprint and source notes |

Merge scale and map setup before extensive parallel actor placement. OFPA reduces overlapping instance edits; it does not merge simultaneous Blueprint-graph edits for you.

### A practical sequence of small milestones

1. **Scale:** two ovals, human reference and one walkable arena.
2. **Circulation:** one complete entrance-to-seat route.
3. **Underground:** one corridor and a tested return route.
4. **Machinery:** one working door/lift pair.
5. **Appearance:** one finished facade bay and seating wedge.
6. **Expansion:** repeat tested modules around the bowl.
7. **Interpretation:** seven tour stops with sources and audio.
8. **Optimization:** measure on the target headset, then trial streaming if warranted.

Do not begin by spawning 50,000 skeletal spectators. Start with a small nearby crowd and cheaper distant representations; measure the result. Use instancing for repeated static details where useful, balancing rendering efficiency against the team's need for independent edits.

### Completion checks

- Target year appears in the project notes and tour introduction.
- The main exterior and arena are oval, at the agreed scale.
- A player can enter, reach seating, visit backstage and return.
- Floor openings and moving machinery have correct collision.
- The XR pawn's stairs, teleportation and lift behavior are tested.
- Historical features, interpretations and fictional prison areas are distinguished.
- Significant events carry dates and source references.
- The map works after a teammate's fresh checkout and LFS download.
- Packaged performance is acceptable on the intended device.

## Sources and how to use them

**[S1] Parco archeologico del Colosseo, underground levels:** wooden initial structures, Domitianic masonry, machinery and later fire. https://colosseo.it/en/marvels/the-underground-levels-of-the-colosseum/

**[S2] Parco archeologico del Colosseo, Spectacles in the Colosseum Arena:** gladiator archaeology and the underground link with the Ludus Magnus. https://colosseo.it/en/digital_exhibitions/spectacles-colosseum-arena/

**[S3] Encyclopaedia Romana, University of Chicago-hosted, The Naumachiae of Titus and Domitian:** ancient accounts and the uncertainty of flooding arrangements. https://penelope.uchicago.edu/encyclopaedia_romana/gladiators/naumachiae.html

**[S4] Encyclopaedia Romana, University of Chicago-hosted, Colosseum:** construction context and overall dimensions. https://penelope.uchicago.edu/~grout/Encyclopaedia_romana/romanurbs/colosseum.html

**[S5] Oxford Classical Dictionary, Colosseum:** construction chronology and surveyed overall dimensions; rounded dimensions in this guide are blockout approximations. https://academic.oup.com/edited-volume/61673/chapter-abstract/548064030

**[S6] Parco archeologico del Colosseo, Blood and the Arena:** opening games under Titus in AD 80. https://colosseo.it/en/event/blood-and-the-arena/

**[S7] Martial, On Spectacles, selections hosted by Pomona College:** Priscus and Verus. Poem numbering varies between editions. Read as an ancient literary source with an imperial patronage context. https://pages.pomona.edu/~cmc24747/sources/martial_selections.htm

**[S8] Parco archeologico del Colosseo, Capitals:** later restorations following the AD 217 fire. https://colosseo.it/en/opere/capitals/

Research checked 14 October 2026. Building dimensions for final art, bay divisions, seat counts and machinery arrangements require a selected archaeological survey beyond this beginner blockout. Coordinate values, module names, game logic and milestone order are proposed project conventions.
