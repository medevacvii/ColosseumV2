# Integrate the aqueduct and Colosseum: a step-by-step beginner guide

Build one working connection first: **aqueduct supply → distribution tank → one Colosseum fountain → drain**. Then add service outlets, rain drainage and the underground gameplay connection.

This document describes proposed implementation work, not completed Unreal assets. It assumes ColosseumV2's agreed XR setup and a playable main map. Read [the water-system guide](Roman-Water-Systems-History-and-Build-Guide.md), [the Colosseum guide](First-Century-Colosseum-Recreation-Guide.md), and [the shared-map guide](One-File-Per-Actor-Guide.md) as needed.

## 1. Choose the historical mode

Use **approximately AD 90** for the first combined map. Keep the Peter/Paul-era Rome chapter separate: the Colosseum did not exist in their lifetime.

The Aqua Claudia and Anio Novus were completed in AD 52; the archaeological park describes branches serving the Caelian and Palatine areas. That is useful geographical context, **not proof of the precise supply route, tank location or pipe dimensions proposed below**. [A]

Treat the short aqueduct approach, local distribution tank, fountain placements and connecting pipes in this exercise as an educational interpretation. Do not label it “the excavated Colosseum water network.” Separate any early aquatic-spectacle scenario: ancient accounts and the actual flooding arrangements require qualification. [B]

## 2. Define the first successful result

You should be able to:

1. Walk from the Colosseum to the demonstration water tank.
2. Close the supply control.
3. Watch the tank gradually empty while the fountain runs.
4. See the fountain stop when storage reaches zero.
5. Reopen supply and see service recover.
6. Watch the drain receive the delivered fountain water and tank overflow.

These controls and operational rules are modern teaching devices. No combat, bath simulation or arena flooding is needed to prove this integration works.

## 3. Prepare the map and branch

1. Save existing work and close Unreal before switching branches.
2. Start from the team's agreed main-map baseline. Once that baseline is merged to main, use:

   ```powershell
   git fetch origin
   git switch main
   git pull --ff-only
   git switch -c feature/aqueduct-colosseum-integration
   git lfs pull
   ```

3. Reopen `ColosseumV2.uproject` and your tested main map. If the main map exists only on a setup branch, start from that agreed branch instead; do not assume the planning documents created it.
4. Confirm Play still spawns the XR pawn correctly.
5. Confirm OFPA is enabled. Keep World Partition runtime streaming off for this initial exercise.
6. In the Outliner add folders `Waterworks_Supply`, `Waterworks_Tank`, `Waterworks_Consumers`, `Waterworks_Drains`, and `Waterworks_Controls`.
7. Put new reusable assets in `Content/Colosseum/Waterworks`. Keep actors separately placed rather than enclosing the whole system in one huge Blueprint.

**Checkpoint:** the original Colosseum still plays before new water actors are added.

## 4. Place the physical connection

For this compressed teaching layout, assume arena center X/Y = 0 and arena floor Z = 0. The building's approximate long-axis extremity is X = 9,450 cm. All coordinates here are invented local blockout choices, not ancient survey coordinates.

| Marker | Proposed location, cm | Purpose |
| --- | --- | --- |
| Upstream channel water | X=22,000, Y=0, Z=1,200 | Start of short visible supply reach |
| Downstream channel water | X=12,000, Y=0, Z=1,180 | Supply arrives above tank |
| Tank base | X=11,500, Y=0, Z=850 | Outside the initial building envelope |
| Tank water range | Z=900 to 1,100 | Example empty/full surface elevations |
| Fountain base | X=10,000, Y=1,000, Z=0 | Proposed exterior visitor stop |
| Fountain nozzle | About Z=120 | Below the tank's operating water level |
| Drain inspection area | Below local fountain ground | Separate wastewater route |

1. Place temporary markers at the first five locations through **Details > Transform > Location**.
2. Add a short channel between upstream and downstream markers. It falls 20 cm over 100 m: a chosen 0.2% teaching slope.
3. Support it with arch meshes where needed. Avoid driving the arcade through the Colosseum facade.
4. Put a tank below its outlet. Show a small falling inlet stream to bridge the height difference.
5. Add a pipe from the tank outlet toward the fountain. Model it as a closed pipe; its route is distinct from the open aqueduct channel.
6. Build a separate drain beneath the fountain outflow.
7. Check site terrain and access routes. Move this whole interpreted installation if it blocks the tested entrance or overlaps future buildings.

The source is actually represented off-map; do not imply Rome's distant springs were only 100 m away. A height difference makes this layout intelligible, but the simple model below does not calculate hydraulic pressure or pipe losses.

## 5. Reuse assets inside the same project

1. Open `L_RomanWater_Demo` if you built the previous exercise.
2. Select the tested channel meshes/materials and note their asset paths.
3. Reopen the main Colosseum map and place those assets from the Content Browser.
4. Place or create the tested tank Blueprint. Create a child Blueprint named `BP_ColosseumWaterTank` if you want a project-specific variant of `BP_WaterTank_Demo`.
5. Keep shared meshes/materials in their existing paths. Moving files in Windows Explorer can break dependencies.
6. Do not leave two overlapping copies of the main map's lighting, player starts or XR pawn while copying scenery.

Within one project, the same reusable assets can serve several maps. You do not need to migrate the entire demo map into the main map.

## 6. Give the tank two explicit output values

The water guide already describes tank storage accounting. Extend that actor rather than creating a second actor that also owns the same water.

1. Open `BP_ColosseumWaterTank` or its chosen parent.
2. Add floats `ActualFountainFlowM3s` and `ActualServiceFlowM3s`, initially 0.
3. Keep `CapacityM3`, `StoredM3`, supply rate, outlet demand and overflow variables from the earlier guide.
4. For this test choose capacity 10 m³, storage 5 m³, incoming supply 0.02 m³/s and fountain demand 0.01 m³/s. Leave the second outlet demand at zero.
5. After each storage calculation, divide the **delivered fountain volume** by the step duration and store the result in `ActualFountainFlowM3s`.
6. Do the same for the optional service branch. Actual delivery must be zero when no water is available.
7. Keep overflow separate from both outputs. Expose its actual rate as `OverflowM3PerSecond`.
8. Reject invalid negative inputs, zero/negative capacity and zero/negative step duration.
9. Compile and save.

Rename a prior `BathDemand` field only if you update every existing reference. It is fine to retain that field as the optional second outlet and display it as “Service branch” in this exercise.

**One clock:** keep the tank's existing looping update timer as the only supply/storage clock. Do not also update it in Tick, a second timer or a map controller. That would create or remove water twice.

## 7. Create the fountain receiver

1. In the Waterworks Blueprint folder, right-click **Blueprint Class > Actor**. Name it `BP_ColosseumFountain`.
2. Open it and add a basin mesh, spout mesh and a simple visible stream component. A small mesh with an animated water material is enough initially.
3. Set the stream component to No Collision.
4. Add a float named `DeliveredFlowM3s`, default 0.
5. Add a custom event named `ApplyDeliveredFlow` with a Float input named `FlowM3s`.
6. In that event, set `DeliveredFlowM3s` to the greater of the input and zero.
7. Compare that value with a small threshold, such as 0.00001, and feed the Boolean into the stream component's **Set Visibility** node.
8. Add an Audio Component with a looping water sound if available; disable Auto Activate. Start it only when transitioning from no flow to flow, and stop it when flow ends. Do not restart the sound on every water update.
9. Compile and save; drag one instance into the main map.
10. Name the instance `Fountain_Colosseum_01` in the Outliner.

This first basin does not store a separate volume: delivered water is treated as through-flow to the drain. State that simplification in the tour. If you later simulate basin storage, send only its measured outflow to the drain rather than counting the same water twice.

## 8. Connect the tank to the placed fountain

1. Open the tank Blueprint.
2. Add a variable `TargetFountain`.
3. Set its type to **BP_ColosseumFountain Object Reference**, not Class Reference.
4. Enable **Instance Editable**, then Compile.
5. At the end of the tank's water update, get `TargetFountain` and use **Is Valid**.
6. From the valid execution pin, call `ApplyDeliveredFlow` on that object. Connect `ActualFountainFlowM3s` to its flow input.
7. For an invalid target, print a clear diagnostic such as `Assign TargetFountain on the tank instance`. Avoid printing it repeatedly every frame.
8. Compile and save.
9. Return to the map, select the tank actor and find **Target Fountain** in Details.
10. Use the eyedropper and click `Fountain_Colosseum_01` in the Outliner.
11. Save All and Play.

**Checkpoint:** with supply and fountain enabled, the tank sends actual delivery to that fountain. If the reference field is missing, Compile and make sure you selected the placed tank actor rather than its asset.

## 9. Add the supply-control interaction

First prove a callable event; then connect your existing XR interaction system.

1. In the tank Blueprint, add custom event `ToggleSupply`.
2. Get `SupplyOpen`, pass it through **NOT Boolean**, then set `SupplyOpen` to that result.
3. Add custom event `ToggleFountain` using the same pattern on `FountainOpen`.
4. Compile and save.
5. Create Actor Blueprint `BP_WaterSupplyControl` with a visible handle or lever mesh.
6. Add an instance-editable tank Object Reference called `TargetTank`.
7. Add a custom event `ActivateControl`; validate TargetTank and call its `ToggleSupply` event.
8. Place the control near the tank and assign its TargetTank eyedropper to the placed tank.
9. For a desktop-only first check, temporarily call ActivateControl once from the control's BeginPlay and verify supply stops. Remove that temporary call after the check.
10. Connect ActivateControl to the project's actual XR interaction event. Use the existing project's interaction pattern; adding a mesh alone does not make a controller button activate it.
11. Use a second appropriately named control for ToggleFountain if desired. Label the controls clearly.

Do not put the whole plumbing system in the Level Blueprint just to make one lever work. Actor events and assigned instance references are easier to reuse and divide between teammates.

## 10. Add a drain receiver and blocked-drain demonstration

1. Create `BP_ColosseumDrain` with a drain-channel mesh and a visible inspection marker.
2. Add a Float `IncomingM3s` and custom event `ApplyIncomingFlow(FlowM3s)` that clamps its input to at least zero and stores it.
3. Add instance-editable `TargetDrain` of that Object Reference type to the tank, and assign the placed drain in the map.
4. At the end of the same tank update, call ApplyIncomingFlow with `ActualFountainFlowM3s + OverflowM3PerSecond`.
5. Leave service-branch wastewater out until you define where it goes. Add it only once, when appropriate.
6. For a visible blocked-drain puzzle, add `Blocked`, `BacklogM3` and an outlet-capacity value. Let one drain timer calculate `backlog + inflow × dt - actual outflow × dt`, never below zero.
7. When Blocked is true, actual outflow is zero. When clear, limit actual outflow by both capacity and available water.
8. Map backlog to a small inspection-basin surface. If the basin fills, report excess as spill volume; do not silently clamp it away.
9. Provide a Reset event that restores tank, control and drain state, and resets all dependent visuals.

This is an abstract reservoir/drain demonstration. It omits pipe travel times and water-pressure feedback, and it does not represent the actual Colosseum sewer route.

## 11. Connect the underground area without flooding the arena

1. Keep the ordinary fountain/drain network separate from your fictional prison puzzle.
2. Add a separate variable `RainInflowM3s` for interpreted storm runoff if needed; rain is not drawn from the clean-water tank.
3. Add that runoff to the drain's incoming rate once, through the chosen owner of that calculation.
4. Put an inspection point near the hypogeum where the player can identify a blockage.
5. Make drain-failure water collect only in your defined demonstration basin or fictional puzzle area.
6. Keep lifts, trapdoors and required escape routes playable. A drain puzzle should not accidentally unload the prison or strand the XR pawn.
7. Label invented hydraulic traps as fiction. Drainage evidence alone does not establish historical water-operated prison traps.

Keep an optional aquatic-spectacle map separate from this AD 90 backstage map. A water plane over the later hypogeum is not sufficient historical reconstruction. [B]

## 12. Add a readable inspection display

For the first desktop test, use Print String from the simulation update, not every frame. Later replace it with a small world-space Widget Component near the tank.

Display these fields:

| Field | Example | What it means |
| --- | --- | --- |
| Supply | OPEN | Control state |
| Stored water | 5.00 / 10.00 m³ | Actual tank volume |
| Actual supply | 0.020 m³/s | Enabled inflow |
| Fountain delivery | 0.010 m³/s | Delivered flow, not requested demand |
| Overflow | 0.000 m³/s | Excess leaving a full tank |
| Drain backlog | 0.00 m³ | Undrained demonstration water |

Use large labels, strong contrast, replayable narration and a visible Reset control. The unit conversion is 0.01 m³/s = 10 L/s.

## 13. Test expected numbers before adding decoration

Using the initial values above, with no service demand:

| Test | Expected result |
| --- | --- |
| Supply 0.02, fountain 0.01 m³/s | Storage rises by about 0.01 m³ each second until full |
| Supply off, starting at 5 m³, fountain 0.01 | Tank empties in about 500 simulated seconds; then fountain delivery stops |
| Tank full, supply 0.02, fountain 0.01 | Storage stays 10 m³; overflow is 0.01 m³/s |
| Tank empty, supply off | No fountain delivery or overflow |
| Tank empty, supply 0.02, fountain requests 0.01 | Simplified model supplies the fountain and begins storing the surplus |
| Drain blocked with incoming water | Backlog rises by the incoming volume |
| Reset | Stored volume, controls, outputs and visuals return to the same baseline |

If you accelerate simulation time for testing, apply the same time scale to tank and drain. Otherwise one part of the network will apparently gain or lose water.

Test an invalid target reference, too. Fix the assignment instead of interpreting a missing actor as a water shortage.

## 14. Expand only after the first connection passes

1. Add a second consumer by giving it its own actual-delivery output and target reference. Do not send the entire network flow to every fountain.
2. Coordinate proportional allocation when water is scarce, following the previous water guide's simplified rule.
3. Add multiple drains only after defining which outlet reaches which drain.
4. Add historical tour stops at supply, tank, fountain and underground inspection point.
5. Add better stonework, pipe materials, sound and effects after behavior is reliable.
6. If enabling World Partition streaming later, decide what continues simulating while distant actors are unloaded. Do not let unloading the tank silently reset the network.
7. For the first version, keep the small referenced network available together. Direct actor references and Data Layer states require a loading review before scaling up.
8. Test a packaged build on the target headset, including loading the map fresh and returning from another chapter.

## 15. Share the integrated result

1. Save All and close Unreal.
2. Review the changed map/settings, Blueprints, meshes/materials and external actor/object packages.
3. Stage the intended files and actor deletions. Exclude local IDE/workspace files.
4. Commit the working fountain/tank connection separately from large art changes where practical.
5. Push your feature branch and open a PR into the agreed integration branch.
6. Ask a teammate to fetch, download LFS content and repeat the numerical checks in section 13.

Suggested ownership: one person owns the tank simulation Blueprint, another the aqueduct placement, another fountain/drain geometry, and another historical narration. OFPA helps different placed actors; coordinate shared Blueprint and material edits.

## Historical references

**[A] Appia Antica archaeological park:** Aqua Claudia/Anio Novus chronology and branch geography. https://www.parcoarcheologicoappiaantica.it/luoghi/acquedotti-claudio-marcio-e-novus/

**[B] Encyclopaedia Romana, University of Chicago-hosted:** ancient aquatic-spectacle accounts and unresolved engineering. https://penelope.uchicago.edu/encyclopaedia_romana/gladiators/naumachiae.html

The companion guides contain further historical and Unreal references. All coordinates, capacities, controller designs, flows and demo connections here are proposed implementation choices, not archaeological measurements.
