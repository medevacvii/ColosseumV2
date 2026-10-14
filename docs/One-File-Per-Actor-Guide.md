# ColosseumV2: one main map, many separately saved actors

Beginner walkthrough for the planning branch. Project inspection found EngineAssociation 5.8, startup/game map `/Game/XRFramework/Levels/L_XRTemplate`, and default game mode `BP_XRGameMode`. The editor is not available in this workspace: map conversion and play testing must be performed in Unreal on your computer. No map has been converted by this document.

## 1. Understand what you are building

Use **One File Per Actor (OFPA)**. Keep one main playable map, such as `L_Colosseum_Main.umap`, and let Unreal save supported placed actor instances into external actor packages.

| Item | Purpose | Suggested owner |
| --- | --- | --- |
| Main `.umap` | Shared level and level settings | Map coordinator |
| Arena wall actor instances | Placement and per-instance settings | Arena builder |
| Prison wall and cell actor instances | Underground layout | Prison builder |
| Trapdoor actor instances | Position and per-door settings | Trapdoor placer |
| `BP_Trapdoor.uasset` | Shared trapdoor components and behavior | Trapdoor programmer |
| Mesh/material assets | Reusable appearance and geometry | Assigned asset owner |

**An actor instance and its Blueprint class are different files.** Moving Trapdoor 01 changes that placed instance. Editing the Blueprint Event Graph changes the shared class and can affect every instance. OFPA helps with the first case; it does not make simultaneous edits to the same class mergeable.

Components inside one actor do not each get an independent OFPA file. A prison built as one enormous Blueprint remains one shared editing bottleneck. Prefer separate walls, doors and gameplay actors where separate ownership is useful.

External filenames are encoded. Keep their generated paths; never rename them to `PrisonWall01.uasset` in Windows Explorer. OFPA is an editor/source-control layout; cooking repackages actors for the game.

## 2. Agree on a starting point

1. Ask teammates to finish or pause current map work.
2. Ensure everyone's existing changes are committed on their own branches.
3. Pick one person to prepare the initial OFPA map.
4. Open a terminal in your Windows repository folder. Run:

   ```powershell
   git fetch origin
   git switch planning
   git pull --ff-only
   git lfs install
   git lfs pull
   git status
   ```

   If `planning` is not yet local, use `git switch --track origin/planning` instead of `git switch planning`.
5. Resolve any unfinished local changes before proceeding. Do not discard them merely to make these commands work.
6. Open `ColosseumV2.uproject` with the team's agreed Unreal version and required XR plugins.

The repository already has LFS rules for `*.uasset` and `*.umap`. Do not replace those rules.

## 3. Make a working copy of the map

1. Open `/Game/XRFramework/Levels/L_XRTemplate` in the Content Browser.
2. Press Play once and confirm the existing XR setup works. Stop Play.
3. Use **File > Save Current Level As** (the exact label may vary).
4. Create/select `Content/Maps` and save as `L_Colosseum_Main`.
5. Check the editor title/current level is the new map.
6. Open **Window > Levels** and inspect whether lighting or other sublevels are attached.
7. Keep those dependencies for now. Saving the parent under a new name does not necessarily duplicate its sublevels. Changes to a shared lighting sublevel can still affect the original map.

The repository includes `L_XRTemplate_Lighting.umap`. Confirm its relationship in the editor rather than assuming it was copied or flattened automatically.

## 4. Enable separate actor files

For the smallest change to the existing XR map, start with OFPA on a regular level. You do not need to add World Partition merely to save actor instances separately.

1. With the new map open, choose **Window > World Settings**.
2. Search for **Use External Actors**.
3. If available and unchecked, enable it.
4. Accept the prompt to convert the level's actors to external packaging.
5. Save All.
6. If you retained sublevels, load each one, make it the current level in the Levels panel, and repeat these steps where independent actor saving is needed. Enabling OFPA on the parent alone does not convert all sublevels.
7. If the option is already enabled/managed by World Partition, keep it enabled and continue to the verification exercise.

**If the option is unavailable:** check whether this map already uses World Partition. For a map that needs conversion, Epic provides **Tools > Convert Level**. Perform conversion on the working copy, inspect its handling of existing sublevels, and play-test the resulting map before adopting it. Do not repeatedly convert the original template or assume the output keeps the same filename.

If you choose a World Partition map, start with **World Settings > World Partition > Enable Streaming** disabled for this small prototype. That keeps distance-based runtime streaming out of the first gameplay test while retaining OFPA. It is a starting recommendation, not a performance guarantee for the eventual XR build.

## 5. Prove OFPA works with two cubes

1. Add two cube actors to the new map.
2. Rename them in the Outliner to `TEST_ArenaCube` and `TEST_PrisonCube`.
3. Save All, then run `git status --short`.
4. Look for new external actor packages, normally beneath `Content/__ExternalActors__/...`. Also retain generated external object packages if present.
5. Record this initial set of files or commit the setup baseline after review.
6. Move only `TEST_ArenaCube` and save again.
7. Inspect the changed files. Its external actor package should change. Ordinary movement should not require resaving all map actors into the main `.umap`.
8. If only the main map changes, stop and verify OFPA is enabled on the level that actually owns the cube. Merely making an Outliner folder does not enable OFPA.
9. If several files change, inspect what else was edited; saving level settings, generated data or folders can add changes. Do not delete unfamiliar packages to force a one-file result.
10. Remove the test cubes in Unreal when finished and save their deletions.

Do not ignore `__ExternalActors__` or `__ExternalObjects__`. They are project content, not a disposable cache.

## 6. Organize the arena and underground prison

1. In the Outliner, create folders named `Arena`, `UndergroundPrison`, `Trapdoors`, `Lighting`, and `Gameplay`.
2. Put arena walls, seating and floor pieces into `Arena`.
3. Put prison floors, walls, bars and stairs into `UndergroundPrison`.
4. Put placed trapdoors into `Trapdoors`.
5. Give important actors clear labels, such as `Prison_Cell01_Door` and `Trapdoor_Arena01`.
6. Agree that arena floor height is approximately Z = 0. For an initial blockout, place the prison floor around Z = -600 cm. Adjust after testing ceiling clearance, stairs and the fall distance.
7. Construct a real opening in the arena floor above the prison. Use separate floor pieces around it. A closed collision surface beneath a visually open trapdoor will still block the player.

Outliner folders are for finding things. They do not create submaps or ownership locks. Build the prison from independently editable actors rather than one giant component collection if multiple people need to work there.

## 7. Make a reusable trapdoor

1. Create `Content/Colosseum/Blueprints/Trapdoors` in the Content Browser.
2. Right-click there, choose **Blueprint Class > Actor**, and name it `BP_Trapdoor`.
3. Open it and add a **Scene Component** named `Hinge`.
4. Add a **Static Mesh Component** named `DoorPanel` as a child of `Hinge`.
5. Choose a simple panel mesh. Set DoorPanel mobility to **Movable** and give it collision that blocks your player.
6. Offset DoorPanel relative to Hinge so the hinge sits along the panel's edge. Rotating a panel around its middle produces a revolving flap rather than a hinged door.
7. Add a Boolean `IsOpen`, initially false.
8. Add a custom event `OpenDoor`. For the first test, use it to set Hinge's relative rotation to an open angle, such as 90 degrees on the appropriate axis, then set IsOpen true. Adjust the sign/axis to suit your mesh.
9. Add `CloseDoor` to restore the closed rotation and set IsOpen false. Use absolute closed/open rotations rather than adding another 90 degrees on every call.
10. Compile and save. Add a Timeline later if you want smooth opening.
11. Drag two instances into the main map and label them `Trapdoor_Arena01` and `Trapdoor_Arena02`.
12. Place them over separate openings. Set per-instance variables for behavior you intend to vary; mark those variables **Instance Editable** in the Blueprint.

Keep trapdoor behavior in `BP_Trapdoor`, not in the Level Blueprint. Agree on the Blueprint's interface and one owner before teammates start placing it.

## 8. Test the opening and the XR player

1. For a disposable local test, temporarily call OpenDoor from BeginPlay in BP_Trapdoor.
2. Press Play. Confirm the door rotates around its edge and the opening is unobstructed.
3. Remove the temporary BeginPlay call, compile and save before publishing gameplay work.
4. Connect your intended switch/lever actor to the door. A simple first design is an instance-editable BP_Trapdoor object reference named TargetDoor on the switch. Assign the placed door with the eyedropper; call OpenDoor only after an Is Valid check.
5. Test both placed doors independently.
6. Check prison collision, lighting, head clearance and a way back up.

**XR-specific check:** an XR pawn may not have Character-style gravity. Opening a door does not automatically make a VR player fall. Decide whether your game uses gravity-based locomotion, a controlled descent, or a teleport transition, and implement/test that behavior separately. If teleport locomotion uses navigation, verify the prison landing area and return route have valid navigation.

If you later enable World Partition streaming, retest switches and their targets together. Direct actor references can affect loading relationships; a disabled Runtime Data Layer can also prevent actors from loading. Keep the first prototype's loading rules simple.

## 9. Choose the new default map after it passes

1. Open **Edit > Project Settings > Maps & Modes**.
2. Set **Editor Startup Map** to your tested main map.
3. Set **Game Default Map** to that same map.
4. Keep the existing XR game mode unless you deliberately replace it. Check the new level's World Settings for a GameMode Override as well.
5. Save, close Unreal, reopen the project and confirm the correct map loads and plays.

Expect `Config/DefaultEngine.ini` to change when you change project map defaults.

## 10. Publish the shared baseline before parallel editing

Keep setup/conversion in one PR first. Teammates should start actor work from the merged baseline; independently converting the same map on multiple branches creates needless reconciliation work.

1. Save All and close Unreal.
2. Run `git status --short`.
3. Review the map, all related external actor/object packages, Blueprint assets, dependencies and intended configuration changes.
4. Stage only those paths in your Git client. Do not stage local IDE or `.codex` workspace files. Include deletions made by Unreal.
5. Run:

   ```powershell
   git diff --cached --stat
   git diff --cached --name-status
   git commit -m "Set up shared Colosseum map with external actors"
   git push -u origin planning
   ```

6. Create a pull request from `planning` into `main`.
7. Have a teammate fetch the branch, run `git lfs pull`, open the map and test it before merging.

GitHub Desktop, Rider or another Git client can handle staging/commit/push. Do not rely on Perforce-only Unreal changelist features being available with your Git provider. Git LFS storage also does not mean files are automatically locked.

## 11. Daily teamwork after the baseline is merged

Start each feature from updated main, with Unreal closed during branch changes:

```powershell
git switch main
git pull --ff-only
git switch -c feature/prison-layout
```

Use other branches for arena construction or trapdoor behavior. Review each PR's changed-file list and test the combined result in Unreal.

| Concurrent work | Expected result |
| --- | --- |
| One person moves arena wall A; another moves prison wall B | Different actor packages usually combine cleanly |
| Two people move the same placed door | Same package can conflict; coordinate one final edit |
| Two people edit BP_Trapdoor's graph | Shared Blueprint asset can conflict |
| Two people edit map settings or Level Blueprint | Shared map can conflict |
| One edits a Blueprint class, another changes instance overrides | Separate files, but combined behavior still requires testing |
| One deletes an actor another person references | May combine in Git yet break gameplay; test references |

For a binary conflict, agree whose package to keep and recreate the other person's change inside Unreal. Do not text-merge `.uasset`/`.umap` files or choose all incoming/current files blindly. If using LFS locks, establish and verify a team locking workflow first; encoded external actor names make clear ownership especially valuable.

## Completion checklist

- The shared map opens after a fresh fetch and LFS download.
- Moving one test actor produces an external actor change.
- Arena and prison use clear, separately editable actors.
- Trapdoor instances share one deliberately owned Blueprint class.
- Open trapdoors expose a genuine collision opening.
- XR descent/teleport behavior and prison return route are tested.
- Default maps/game mode are correct.
- All required actor/object packages and dependencies are committed.
- Two teammate branches editing different actors merge and play correctly.

## Official references

- [Epic: One File Per Actor](https://dev.epicgames.com/documentation/en-us/unreal-engine/one-file-per-actor-in-unreal-engine) — enabling external actors, sublevel conversion, encoded package names and source-control considerations.
- [Epic: World Partition](https://dev.epicgames.com/documentation/en-us/unreal-engine/world-partition-in-unreal-engine) — optional conversion, streaming settings, actor reference/loading relationships and Blueprint guidance.

Coordinates, names, ownership rules and prototype behavior in this guide are proposed ColosseumV2 conventions. They are not existing implemented game features.
