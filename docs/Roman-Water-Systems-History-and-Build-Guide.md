# Roman water systems: history and a beginner Unreal build guide

For ColosseumV2. “Aquatic system” here means the wider **water-supply and drainage system**, including the separate question of aquatic spectacles. This is a proposed educational game model, not a surveyed reconstruction or a real engineering design. No Unreal assets have been changed.

Companions: [First-century Rome and Colosseum guide](First-Century-Colosseum-Recreation-Guide.md) and [OFPA/World Partition guide](One-File-Per-Actor-Guide.md).

## 1. Understand the system before building

Think of three connected jobs: **bring water in, distribute/use it, take wastewater away**.

| Part | Plain-language job | What to build first |
| --- | --- | --- |
| Source/intake | Collect water from a suitable spring or river | A small source pool and intake |
| Aqueduct channel, or specus | Carry water along a controlled route | A covered trough, shown partly cut away |
| Tunnel/ground-level conduit/arcade | Support the channel through different terrain | One underground section and one arched crossing |
| Settling basin | Let some suspended sediment settle | A wider basin with an inspection stop |
| Distribution tank, often called a castellum | Divide the supply between destinations | A tank with separate outlets |
| Pipes | Deliver water to selected consumers | One fountain branch and one bath branch |
| Fountain/basin | Give people access to water | A spout and collection basin |
| Bath complex | Use water for bathing, with heating arrangements | A small interpreted bath and service area |
| Drain/sewer | Carry overflow, runoff and wastewater away | A separate channel below the street |

Frontinus distinguishes underground channels, structures and arches, and documents distributions to public works, fountains/basins, imperial and private uses. His administrative account also concerns maintenance and unauthorized diversions. It is a late-first/early-second-century witness, not an exact description of every earlier neighborhood. [R1, R2]

Do not make every aqueduct an endless row of arches. Do not return dirty sewer water directly to the drinking-water tank. Roman infrastructure was sophisticated, but it was not modern drinking-water treatment or universal household plumbing.

## 2. Put the history in the correct period

| Date/period | Development | Meaning for your project |
| --- | --- | --- |
| 312 BC | Aqua Appia, traditionally Rome's first aqueduct [R1] | Water supply long predates the apostles and the Colosseum |
| 144–140 BC | Construction of Aqua Marcia [R3] | An earlier supply still relevant to imperial Rome |
| AD 38–52 | Aqua Claudia begun under Caligula, completed under Claudius; Anio Novus also completed in AD 52 [R3] | Appropriate major infrastructure for both your AD 60s and AD 90 chapters |
| AD 60s | Peter and Paul's Roman context | Include water infrastructure, but no Colosseum; choose pre-/post-fire scenery carefully |
| After AD 64 | Nero's Domus Aurea included a lake in the future Colosseum valley [R4] | A distinct landscape phase, not the amphitheatre flooded before it existed |
| AD 80 | Opening of the Colosseum | Early aquatic-spectacle accounts may be discussed separately [R5] |
| Around AD 90 | Domitianic Colosseum setting | Show backstage drainage; do not casually flood a developed hypogeum |
| AD 97 and after | Frontinus's water administration and written account [R1, R2] | A valuable near-contemporary research source, used with chronology in mind |

Do not copy later monuments into the first century. The Baths of Caracalla, for example, are a later imperial complex. A modern ruin's surviving pipes or latrine fittings may also belong to later rebuilding.

## 3. Learn the minimum water math

### A. Open channels need a downhill water-surface gradient

For your first teaching model, use:

`Slope = vertical fall / horizontal distance`.

Example: a 100 m demonstration channel drops 0.20 m:

`0.20 / 100 = 0.002 = 0.2%`.

In the project's usual centimeter convention, that is a 10,000 cm run and a 20 cm fall. This is a **chosen teaching example**, not a universal Roman gradient. Real channel geometry, roughness, water depth and losses determine performance.

### B. Pressure pipes behave differently

A sealed pipe can go down and rise again using available pressure head, with losses. An open trough cannot magically climb a hill. For an optional inverted-siphon exhibit, keep the receiving outlet below the upstream water surface and explain pressure; do not present that exhibit as a verified feature of your chosen Colosseum route.

### C. Flow and storage

`Flow Q = volume / time` and, for a simplified channel section, `Q = area × average velocity`.

Example: 0.20 m² of flowing cross-section at 0.50 m/s gives 0.10 m³/s, or 100 L/s. These are hypothetical values.

A 10 m³ tank receiving 0.02 m³/s with no outflow fills in `10 / 0.02 = 500 seconds`, about 8 minutes 20 seconds. With consumers drawing water, use **net** inflow instead. At equal inflow and outflow, the level stays steady.

Sediment settling is not sterilization. A convincing clean-looking material is not proof that ancient water was safe by modern standards.

## 4. Set up one small educational map

1. Start from your team's tested baseline and create `feature/roman-water-demo`.
2. Open the agreed Unreal version and retain the existing XR game mode/input setup.
3. Create a demonstration map named `L_RomanWater_Demo` through Unreal. Keep it separate from the historically dated Rome maps initially.
4. Enable OFPA using the companion guide. World Partition streaming is unnecessary for the first small demonstration.
5. Create `Content/Colosseum/Waterworks` with `Meshes`, `Materials`, `Blueprints` and `Audio` subfolders.
6. Make Outliner folders named `Supply`, `Distribution`, `Consumers`, `Drainage` and `Tour`.
7. Choose a compact walkable route: source → channel → tank → fountain/bath → drain.
8. Place a mannequin and check scale before making repeated arches.

This map compresses a city-scale network for learning. Display that fact at its entrance; do not imply that the source springs were beside the Colosseum.

## 5. Build a channel and a short arcade

1. Place the source marker at X = 0 and the downstream marker at X = 10,000 cm.
2. Choose a visible channel elevation, such as Z = 1,000 cm upstream and Z = 980 cm downstream. These are local demo coordinates.
3. Make a trough from a bottom and two side meshes, then add a removable roof for the cutaway view.
4. Fit the channel's slope between your markers; support it with independently placed piers/arch meshes.
5. Keep the water surface below the walls and roof. A trough with water flowing over the sides is overflowing, not operating normally.
6. Add one short covered/underground segment to show that arches are only part of the route.
7. Add a service-access marker and a narration stop explaining maintenance.

Reuse an arch mesh, but keep placed sections separately editable where teammates need ownership. Assign one owner to shared mesh changes.

## 6. Show flowing water with a simple material

Start with a visual demonstration before adding simulation.

1. Make a narrow plane mesh inside the trough and set its collision to No Collision.
2. Create a material named `M_ChannelWater_Demo`.
3. Add a Texture Coordinate node, a Panner and a small tiling water texture or normal texture you have permission to use.
4. Connect the panned coordinates to the texture's UV input. Keep animation subtle.
5. Apply the material to the plane and align UVs/animation with downstream flow.
6. Adjust tiling so the same small feature is not stretched across 100 m.
7. Add quiet running-water audio near the channel; test stereo appearance in the headset.

A scrolling material **does not simulate flow, depth, collisions or conservation of water**. Keep the numerical model in the next steps separate from the appearance.

For a larger lake or river, Unreal's optional Water plugin is another tool: enable **Water** through **Edit > Plugins**, restart if requested, and follow the engine's Water Body documentation. [R6] Do not expect placing a Water Body to solve a pipe network or determine historical flood behavior. Check terrain effects, rendering and XR cost on a disposable test map before integrating it.

## 7. Add a distribution tank with understandable behavior

1. Create Actor Blueprint `BP_WaterTank_Demo`.
2. Add fixed tank meshes plus a water-surface plane as a movable component.
3. Create floats `CapacityM3`, `StoredM3`, `InflowM3PerSecond`, `FountainDemandM3PerSecond`, `BathDemandM3PerSecond` and `OverflowM3PerSecond`.
4. Create booleans `SupplyOpen`, `FountainOpen` and `BathOpen`.
5. For the first model, set capacity to 10 m³ and store 5 m³ initially. Label all chosen flow values as demonstration settings.
6. Update once per second using a looping timer. Apply actual elapsed simulation time if the timer can be delayed.
7. Compute available volume, actual delivered water and overflow in this order:

   ```text
   available = stored + enabledInflow * dt
   requested = enabledOutletDemand * dt
   delivered = min(requested, available)
   remaining = available - delivered
   overflow = max(remaining - capacity, 0)
   newStored = clamp(remaining, 0, capacity)
   ```

8. If both outlets compete for insufficient water, split the delivered volume proportionally to their requests for this simple demo. State that this is your game rule, not a claim about Roman legal allocation.
9. Divide overflow volume by dt to report overflow rate. Do not count it as ordinary consumer supply.
10. Set the surface's relative Z by interpolating between the tank's empty/full visual heights using `StoredM3 / CapacityM3`. Reject capacity values at or below zero.
11. Display storage and **actual** outlet delivery on a small panel.
12. Test full, empty, supply-off and both-outlets-open cases. Consumers must stop receiving water when it runs out.

This is a simplified storage model: no pipe pressure, travel time or head-dependent outlet flow. Add those only if the learning goal requires them.

## 8. Add a fountain, bath and controls

1. Build one fountain basin with a spout and an overflow lip.
2. Link its visible stream/audio to actual delivered flow from the tank, not merely the valve's requested state.
3. Add one bath pool with a clearly separate service/heating area. Describe its architecture as an interpretation unless based on a dated survey.
4. Provide three deliberate XR interactions: toggle supply, toggle fountain, toggle bath.
5. Show how opening the bath branch changes tank storage when combined demand exceeds supply.
6. Keep cold-water supply distinct from the furnace/heating system. A hypocaust concerns heated floors/spaces; it is not a magic water pump.
7. Explain that public and private access differed; do not install a modern tap in every apartment.

Ostia's archaeological park documents bronze valves in lead water pipes supplying fountains, baths and wealthier homes. Use this as comparative evidence, not proof of an identical first-century Colosseum fitting. [R7]

## 9. Add drainage without turning it into a drinking supply

1. Put a separate drain below the fountain overflow and bath outlet.
2. Use a distinct inspection color, such as brown, for the wastewater path.
3. Add street runoff and a simple outfall as separate visual elements.
4. Label the outfall as a simplified end of the demo, rather than drawing an invented precise sewer connection onto historical Rome.
5. Add a blockage interaction that makes the simulated drain back up. Cap any visual flooding at the demo boundary and provide a Reset button.
6. Explain that drains, sewers, cesspits and sanitation arrangements varied; do not assert that every home was connected or every drain was walkable.

The Cloaca Maxima is a major Roman drainage/sewer example, not a name for every underground passage. For a historically located recreation of it, select a dated archaeological plan before building its course.

If using Ostia's well-preserved Forum Baths latrine as a reference, label its chronology: the park dates the adapted premises to the Hadrianic period and its present appearance to the fourth century. Do not copy the whole surviving room into AD 60s Rome unchanged. [R8]

## 10. Connect the lessons to your Colosseum

1. Keep the water demo independent until its controls and accounting work.
2. Add modest water-service and rain-drainage features to the AD 90 map, recording which placements are interpretations.
3. Ensure the hypogeum stays dry in the ordinary backstage scenario. Give drain failure a visible, reversible test mode if desired.
4. Put an explanation of water supply and drainage at a backstage tour stop.
5. Keep your fictional prison's water puzzles marked as game mechanics; do not infer historical hydraulic traps from the existence of drains.

### Optional early aquatic-spectacle scene

Ancient accounts describe aquatic spectacles associated with Titus and Domitian, but the precise location, waterproofing, filling arrangements and chronology remain debated. A later masonry hypogeum complicates a flooded-arena interpretation. [R5]

1. Use a separate early-phase map, such as `L_Colosseum_Aquatic_Interpretation`.
2. Display an introduction: **Interpretation of ancient aquatic-spectacle accounts; exact engineering uncertain**.
3. Leave the later hypogeum out of this scene rather than hiding it under a water plane.
4. Raise a water surface visually and drive a labelled hypothetical volume calculation.
5. Keep the stage empty for the first test; add small demonstration craft only after scale and collision checks.
6. Do not claim that modern-looking sluices, pumps, sharks or a particular ship fleet are documented features.

Example only: an elliptical basin 80 m by 48 m with a uniform 1 m depth has approximate volume `pi × 40 × 24 × 1 = 3,016 m³`. At an assumed net inflow of 0.20 m³/s, filling takes about 4 hours 11 minutes. Neither the chosen depth nor inflow establishes how a historical display worked. Exclude sloped sides, leakage and displaced volume only by explicitly stating those simplifications.

## 11. Make a short audio-friendly tour

1. **Source:** Where did the water come from?
2. **Channel cutaway:** Why does the route need a gradient?
3. **Arches and tunnel:** Why not build arches everywhere?
4. **Tank:** Who receives water when supply is limited?
5. **Fountain/bath:** How did people use it?
6. **Drain:** Where does the used water go?
7. **Colosseum panel:** What is known, and what remains uncertain?

At each stop use a short explanation, replayable audio and a visible demonstration. Add one hands-on task, such as restoring fountain supply after the tank empties. Keep invented maintenance characters and dialogue labelled as fiction.

## 12. Verify and share

- The chosen era is visible; AD 60s Rome has no Colosseum.
- Supply channels, consumer pipes and drains are distinguishable.
- The visual downstream direction matches the chosen channel slope.
- Storage never becomes negative or exceeds capacity after overflow.
- Consumer delivery cannot exceed available water.
- Water effects stop or change when actual flow changes.
- There is a working reset and no forced XR camera motion.
- Aquatic spectacle scenes state the uncertainty.
- All maps, Blueprint dependencies and external actor packages survive a teammate's fresh checkout.
- The packaged scene runs acceptably on the target headset.

Build in this order: **dry channel → visual water → tank accounting → one fountain → bath/drain → historical tour → optional aquatic spectacle**.

## Sources

**[R1] Frontinus, Water Supply of Rome, Book I (ancient primary source, translated).** Aqueduct chronology, routes and supply administration. https://penelope.uchicago.edu/Thayer/E/Roman/Texts/Frontinus/De_Aquis/Bennett/1%2A.html

**[R2] Frontinus, Book II.** Distribution, maintenance and regulation. https://penelope.uchicago.edu/Thayer/E/Roman/Texts/Frontinus/De_Aquis/Bennett/2%2A.html

**[R3] Appia Antica archaeological park: Aqua Marcia, Claudia and Anio Novus.** Official chronology and route descriptions. https://www.parcoarcheologicoappiaantica.it/luoghi/acquedotti-claudio-marcio-e-novus/

**[R4] Colosseum archaeological park: Domus Aurea.** Post-fire development and Nero's lake. https://colosseo.it/en/area/domus-aurea/

**[R5] University of Chicago-hosted Encyclopaedia Romana: Naumachiae of Titus and Domitian.** Ancient accounts and engineering uncertainties. https://penelope.uchicago.edu/encyclopaedia_romana/gladiators/naumachiae.html

**[R6] Epic Games: Unreal Engine Water System.** Rendering/plugin capabilities, not an aqueduct hydraulics solver. https://dev.epicgames.com/documentation/unreal-engine/water-system-in-unreal-engine?lang=en-US

**[R7] Ostia archaeological park: bronze water valve.** Comparative evidence for distribution fittings and consumers. https://www.ostiaantica.beniculturali.it/it/ostia-racconta/ostia-racconta-il-rubinetto-di-bronzo/

**[R8] Ostia archaeological park: latrine by the Forum Baths.** Architecture and later chronology. https://ostiaantica.cultura.gov.it/pannelli-didattici-e-mappe/larea-centrale-e-i-complessi-di-rappresentanza/latrina-presso-le-terme-del-foro/

Research checked 14 October 2026. All local coordinates, demonstration dimensions, flow values, branch names and gameplay rules are proposed teaching conventions, not measurements recovered from the Roman network.
