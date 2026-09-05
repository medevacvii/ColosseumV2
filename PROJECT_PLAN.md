# ColosseumV2 Project Plan

## 1. Project Goal

Build a historically informed, modular Roman Colosseum reconstruction
and simulation in Unreal Engine 5.8.x.

The project uses Unreal Engine's **Simulation Blank** template as its
technical foundation while keeping Colosseum-specific content clearly
separated under the project's own namespace.

The project should remain:

- buildable
- testable
- modular
- source-controlled
- understandable by multiple contributors
- traceable back to historical/design evidence
- safe to extend without breaking template dependencies

## 2. Baseline Architecture Decision

The Unreal-generated project structure is authoritative.

Do **not** refactor the Simulation template simply to force it into a
custom directory diagram.

Instead:

```text
Unreal-generated project
        +
Simulation template content
        +
Content/Colosseum/
        +
repository-level documentation/source assets
```

New project-owned Unreal content belongs under:

```text
Content/Colosseum/
```

Source/reference material outside Unreal belongs under:

```text
Assets/
Documentation/
```

This is the project's primary directory-reconciliation rule.

## 3. Target Repository Structure

```text
ColosseumV2/
├── Config/
├── Content/
│   ├── <Simulation template folders>/
│   ├── Colosseum/
│   │   ├── Architecture/
│   │   │   ├── Exterior/
│   │   │   ├── Seating/
│   │   │   ├── Arena/
│   │   │   ├── Interior/
│   │   │   └── Underground/
│   │   ├── Blueprints/
│   │   ├── Characters/
│   │   ├── Materials/
│   │   ├── Meshes/
│   │   ├── Textures/
│   │   ├── UI/
│   │   └── Audio/
│   └── Maps/
│       ├── Development/
│       ├── Testing/
│       └── Production/
├── Source/
├── Plugins/
├── Assets/
├── Documentation/
├── Script/
├── Tests/
├── Builds/
└── ColosseumV2.uproject
```

Generated Unreal directories such as `Binaries/`, `DerivedDataCache/`,
`Intermediate/`, and `Saved/` remain in their normal locations but are
not part of the authoritative project source.

## 4. Project Principles

### Preserve the template baseline

Template assets remain where Unreal created them unless migration has a
specific functional benefit.

### Separate project-owned content

Use:

```text
Content/Colosseum/
```

as the default namespace for new work.

### Build modularly

Architectural systems should be reusable rather than one giant mesh.

Examples:

- repeatable exterior bays
- reusable arches and piers
- modular seating wedges
- reusable stair/vomitorium components
- reusable arena boundary pieces

### Keep source evidence

Historical decisions, dimensions, references, and original source assets
must survive outside Unreal binary assets.

### Keep `main` usable

All shared work reaches `main` through Pull Requests after appropriate
validation.

## 5. Workstreams

The project is divided into parallel workstreams.

### A. Technical Foundation

Responsible for:

- Unreal/Visual Studio toolchain
- plugins
- build configuration
- Git/Git LFS
- smoke tests
- automation
- packaging
- integration maps

### B. Exterior Architecture

Responsible for:

- outer ellipse
- exterior wall modules
- arches
- piers
- orders/columns
- entrances
- exterior circulation
- facade materials

Primary area:

```text
Content/Colosseum/Architecture/Exterior/
```

### C. Seating and Interior Circulation

Responsible for:

- cavea/seating geometry
- seating tiers
- aisles
- stairs
- vomitoria
- internal circulation
- supporting interior geometry

Primary areas:

```text
Content/Colosseum/Architecture/Seating/
Content/Colosseum/Architecture/Interior/
```

### D. Arena and Underground

Responsible for:

- arena floor
- arena wall/boundary
- hypogeum
- tunnels
- lifts/trapdoor concepts
- underground circulation

Primary areas:

```text
Content/Colosseum/Architecture/Arena/
Content/Colosseum/Architecture/Underground/
```

### E. Simulation and Interaction

Responsible for:

- player/controller integration
- input
- interaction
- doors
- traversal
- template-derived simulation systems
- UI
- future simulation/gameplay features

### F. Historical Research and Validation

Responsible for:

- dimensions
- evidence
- assumptions
- reconstruction confidence
- reference images
- design decisions
- provenance

Primary area:

```text
Documentation/
Assets/Reference/
Assets/Historical/
```

## 6. Milestones

## Milestone 0 — Repository and Toolchain Baseline

### Goal

Create a reproducible project that every contributor can open and build.

### Deliverables

- [ ] Unreal Engine version documented.
- [ ] Visual Studio/workspace requirements documented.
- [ ] `ColosseumV2.uproject` opens.
- [ ] Required plugins load.
- [ ] Project compiles.
- [ ] Git LFS installed and configured.
- [ ] `.gitignore` reviewed.
- [ ] `.gitattributes` reviewed.
- [ ] `.gitmessage` committed and documented.
- [ ] `CONTRIBUTING.md` present.
- [ ] `TROUBLESHOOTING.md` present.
- [ ] Initial smoke-test procedure documented.

### Exit criteria

A clean clone can be configured, built, opened, and tested from the
documented instructions.

---

## Milestone 1 — Reconcile Simulation Template and Project Structure

### Goal

Establish a clean boundary between template content and Colosseum-owned
content without destabilizing the generated project.

### Deliverables

- [ ] Keep Unreal-generated root structure intact.
- [ ] Inventory template-owned `Content/` directories.
- [ ] Inventory template/plugin dependencies.
- [ ] Create `Content/Colosseum/` through the Unreal Editor.
- [ ] Create architectural subfolders.
- [ ] Create Development/Testing/Production map areas as needed.
- [ ] Create repository-level `Assets/`.
- [ ] Create `Documentation/`.
- [ ] Create `Script/`, `Tests/`, and ignored `Builds/` as needed.
- [ ] Document the ownership boundary.
- [ ] Verify no template references were broken.

### Exit criteria

A contributor can identify whether an asset is template-owned or
Colosseum-owned without guessing.

---

## Milestone 2 — Dimensions and Modular Standards

### Goal

Define the dimensional and modular rules before producing large amounts
of geometry.

### Deliverables

- [ ] Coordinate/world-origin convention documented.
- [ ] Unreal unit convention documented.
- [ ] Major/minor ellipse dimensions recorded.
- [ ] Arena dimensions recorded.
- [ ] Floor/elevation reference heights recorded.
- [ ] Exterior bay/module strategy defined.
- [ ] Seating wedge/module strategy defined.
- [ ] Pivot/origin rules documented.
- [ ] Grid/snapping rules documented.
- [ ] Historical confidence recorded for major dimensions.

### Exit criteria

Exterior and seating contributors can build independently while still
producing geometry that fits together.

---

## Milestone 3 — Exterior Structural Prototype

### Goal

Create the first reusable exterior system.

### Deliverables

- [ ] Outer ellipse guide.
- [ ] First reusable exterior bay.
- [ ] Arch module.
- [ ] Pier/support module.
- [ ] Floor/elevation alignment.
- [ ] Repeated curved placement test.
- [ ] Entrance-specific strategy.
- [ ] Placeholder materials.
- [ ] Development test map.
- [ ] LOD/Nanite strategy reviewed.

### Exit criteria

A representative section of the exterior can be repeated around the
ellipse without manual per-piece correction.

---

## Milestone 4 — Seating and Interior Prototype

### Goal

Create seating geometry that aligns with the same shared dimensions.

### Deliverables

- [ ] Seating tier profile.
- [ ] Reusable seating wedge.
- [ ] Aisle/stair strategy.
- [ ] Vomitorium openings.
- [ ] Interior circulation prototype.
- [ ] Connection test with exterior geometry.
- [ ] Development test map.
- [ ] Collision/traversal prototype.

### Exit criteria

A representative seating/interior section fits the exterior structure
and supports intended traversal.

---

## Milestone 5 — Arena and Underground Prototype

### Goal

Establish the arena and hypogeum as separate modular systems.

### Deliverables

- [ ] Arena boundary.
- [ ] Arena-floor strategy.
- [ ] Hypogeum blockout.
- [ ] Tunnels/corridors.
- [ ] Stair/access connections.
- [ ] Lift/trapdoor placeholders where applicable.
- [ ] Integration with seating/interior geometry.

### Exit criteria

Exterior, seating, arena, and underground blockouts align in one
integration level.

---

## Milestone 6 — Simulation Integration

### Goal

Integrate useful Simulation-template systems without coupling all project
content directly to template folders.

### Deliverables

- [ ] Player spawn/navigation.
- [ ] Approved input system.
- [ ] Camera behavior.
- [ ] Basic interaction.
- [ ] Reusable door/interactable approach.
- [ ] Collision review.
- [ ] Integration map.
- [ ] Smoke tests.
- [ ] Template dependencies documented.

### Exit criteria

A user can enter the integrated environment, move through it, and test
core interactions without unexplained errors.

---

## Milestone 7 — Materials, Lighting, Detail, and Optimization

### Goal

Move from blockout/prototype quality toward a coherent environment.

### Deliverables

- [ ] Material library.
- [ ] Reusable material instances.
- [ ] Texture/source provenance.
- [ ] Lighting strategy.
- [ ] Detail-pass rules.
- [ ] Nanite/LOD review.
- [ ] Collision optimization.
- [ ] Performance profiling.
- [ ] Asset validation.

### Exit criteria

A representative integrated scene meets the project's visual and
performance targets on the agreed test hardware.

---

## Milestone 8 — Validation and Packaging

### Goal

Produce a reproducible distributable build.

### Deliverables

- [ ] Data Validation completed.
- [ ] Smoke tests completed.
- [ ] Key maps load.
- [ ] PIE test completed.
- [ ] Standalone test completed.
- [ ] Packaged-build test completed.
- [ ] Known issues documented.
- [ ] Troubleshooting guide updated.
- [ ] Release notes prepared.
- [ ] Version/tag procedure followed.

### Exit criteria

A clean packaged build can be produced from the documented repository
state.

## 7. Branch and Integration Strategy

The project uses focused branches and Pull Requests.

Default separation:

```text
LFS/binary asset work
        ↓
asset/, level/, blueprint/ branches

text/code/config/docs work
        ↓
code/, docs/, fix/, feature/ branches
```

Do not mix unrelated binary and text work.

When a feature requires both, coordinate separate Pull Requests and
document dependencies when practical.

All shared work merges to `main` through Pull Requests.

See `CONTRIBUTING.md` for the complete workflow.

## 8. Map Strategy

Use maps according to purpose.

```text
Content/Maps/Development/
```

For isolated work, experiments, and contributor-owned prototypes.

```text
Content/Maps/Testing/
```

For repeatable integration/smoke/validation tests.

```text
Content/Maps/Production/
```

For approved integrated project levels.

Avoid using the production map as everyone's scratchpad.

## 9. Testing Strategy

Start simple and increase automation as the project stabilizes.

### Phase 1

Manual checks:

- project opens
- compile succeeds
- map opens
- player spawns
- movement works
- key interactions work
- Output Log has no unexplained new errors

### Phase 2

Repeatable smoke tests:

```text
COL.Basic.ProjectLoads
COL.Basic.MainMapLoads
COL.Player.Spawns
COL.Player.Move
COL.Player.CameraSwitch
COL.Input.ExplorationContext
```

### Phase 3

Add:

- Unreal Data Validation
- command-line tests
- CI/PR checks where practical
- packaged-build validation

## 10. Research and Decision Tracking

For every important architectural assumption:

```text
Element:
Source:
Dimension / value:
Confidence:
Reasoning:
Notes:
Last reviewed:
```

Confidence:

```text
HIGH
MEDIUM
LOW
SPECULATIVE
```

Important assumptions should not live only in someone's memory or inside
a Blueprint comment.

## 11. Primary Risks

| Risk | Mitigation |
|---|---|
| Breaking template references by reorganizing content | Keep template structure intact; move assets only through Unreal |
| Binary merge conflicts | Git LFS locks and focused branches |
| Large/unreviewable PRs | Separate LFS and non-LFS work; small PRs |
| Geometry from different contributors does not align | Shared dimensions, pivots, grid and modular standards |
| Lost source art | Keep original files under `Assets/` |
| Historical assumptions become "facts" | Record source and confidence |
| Engine upgrade resaves many assets | Dedicated `upgrade/` branch and validation |
| Generated files pollute Git | Enforce `.gitignore` and pre-push review |
| Template/plugin dependency becomes hidden | Document dependencies and verify integration maps |
| Performance problems arrive late | Profile representative modules before full-detail pass |

## 12. Immediate Next Actions

1. Preserve the current Simulation-template root structure.
2. Inventory the existing `Content/` template folders.
3. Create `Content/Colosseum/` in the Unreal Content Browser.
4. Create the Architecture subfolders.
5. Establish Development/Testing/Production map conventions.
6. Add repository-level `Assets/` and `Documentation/`.
7. Verify `.gitignore` for generated Unreal directories.
8. Verify `.gitattributes` and LFS lockable rules.
9. Configure `.gitmessage` on each contributor clone.
10. Create a clean integration/smoke-test baseline.
11. Document the shared Colosseum dimensions.
12. Begin exterior and seating prototypes from those shared dimensions.

## 13. Definition of Done for a Feature

A feature is not done merely because it looks correct in one editor
session.

A feature is done when applicable items are satisfied:

- [ ] Work is in the correct project directory.
- [ ] Template ownership boundary is respected.
- [ ] LFS assets are correctly tracked.
- [ ] Shared binary assets followed the locking workflow.
- [ ] Unreal references remain valid.
- [ ] Redirectors were fixed if assets moved.
- [ ] Project compiles.
- [ ] Relevant map opens.
- [ ] Feature works in Editor/PIE.
- [ ] Standalone/package testing is recorded when required.
- [ ] No unexplained new errors are introduced.
- [ ] Source assets are retained.
- [ ] Historical/design decisions are documented.
- [ ] Testing is described in the commit/PR.
- [ ] Pull Request is reviewed.
- [ ] `main` remains usable after merge.
