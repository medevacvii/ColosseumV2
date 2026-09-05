# Contributing to ColosseumV2

Thank you for contributing to the Roman Colosseum project.

This document defines the shared development workflow for the Unreal
Engine 5.8.x project created from the **Simulation Blank** template.

## 1. Read These First

Before making changes, read:

- `README.md` — project structure and setup
- `PROJECT_PLAN.md` — roadmap and milestones
- `TROUBLESHOOTING.md` — diagnostics and recovery
- `.gitattributes` — Git LFS rules
- `.gitignore` — files that must not be committed
- `.gitmessage` — shared commit-message template

## 2. Initial Setup

Install the approved project toolchain, Git, and Git LFS.

After cloning:

```bash
git lfs install
git lfs pull
git config commit.template .gitmessage
```

Verify the template:

```bash
git config --get commit.template
```

Expected:

```text
.gitmessage
```

Before beginning development:

1. Verify the approved Unreal Engine version.
2. Verify the approved Visual Studio/workspace tooling.
3. Verify required plugins.
4. Generate project files if needed.
5. Open `ColosseumV2.uproject`.
6. Build the project.
7. Open the current integration/test map.
8. Run Play In Editor.
9. Check the Output Log.

If the baseline project does not work, fix or report that before adding
new feature work.

## 3. Project Directory Rules

The Unreal-generated directory layout is the baseline.

Do not reorganize the Simulation template only to make it match a custom
folder diagram.

### Unreal-managed content

All Unreal-managed assets stay under:

```text
Content/
```

New Colosseum-specific content belongs under:

```text
Content/Colosseum/
```

Recommended structure:

```text
Content/Colosseum/
├── Architecture/
│   ├── Exterior/
│   ├── Seating/
│   ├── Arena/
│   ├── Interior/
│   └── Underground/
├── Blueprints/
├── Characters/
├── Materials/
├── Meshes/
├── Textures/
├── UI/
└── Audio/
```

Template folders may remain beside `Content/Colosseum/`.

### Source assets

The root `Assets/` directory is for source files outside Unreal:

```text
Assets/
├── Blender/
├── Reference/
├── Historical/
├── Photogrammetry/
└── SourceTextures/
```

Do not place `.uasset` or `.umap` files in this directory.

### Documentation

Use:

```text
Documentation/
├── References/
├── Dimensions/
├── Decisions/
├── HistoricalSources/
├── Guides/
└── Troubleshooting/
```

Do not create a second `Docs/` hierarchy unless the project formally
decides to rename `Documentation/`.

## 4. Moving Unreal Assets

Move existing Unreal assets through the Unreal Editor Content Browser.

Do not move `.uasset` or `.umap` files with:

- File Explorer
- Git Bash
- PowerShell file moves
- an IDE file browser

After a significant move:

1. Save affected assets.
2. Fix up redirectors in the Content Browser.
3. Reopen affected maps/Blueprints.
4. Check references.
5. Run validation/testing.
6. Review the Git diff before committing.

A tidy folder tree is not worth broken asset references.

## 5. Generated Files

Do not commit generated Unreal data unless the repository explicitly
documents an exception.

Normally ignored:

```text
Binaries/
DerivedDataCache/
Intermediate/
Saved/
```

Generated IDE solution/workspace files are treated as regeneratable
development files unless the repository policy says otherwise.

Within plugins, generated `Binaries/` and `Intermediate/` directories
should normally remain ignored.

Always follow `.gitignore`.

## 6. Git LFS and Binary Assets

Files matched by `.gitattributes` must stay in Git LFS.

Unreal `.uasset` and `.umap` files are shared binary assets and should
use the project's LFS/locking policy.

Before editing a shared lockable asset:

```bash
git pull
git lfs pull
git lfs locks
git lfs lock path/to/asset.uasset
```

Normal sequence:

```text
Pull latest work
      ↓
Check LFS locks
      ↓
Lock shared binary asset
      ↓
Create/switch to focused branch
      ↓
Edit
      ↓
Test
      ↓
Commit
      ↓
Push
      ↓
Open Pull Request
      ↓
Unlock after the work is safely pushed
```

Unlock:

```bash
git lfs unlock path/to/asset.uasset
```

Do not force-unlock another contributor's asset without team
confirmation.

## 7. Branching Policy

Do not develop directly on `main`.

Use focused branches.

Recommended prefixes:

```text
asset/
level/
blueprint/
feature/
code/
docs/
fix/
test/
build/
upgrade/
```

Examples:

```text
asset/exterior-arches
asset/seating-meshes
level/integration-map
blueprint/door-system
code/player-controller
docs/project-structure
fix/unreal-build-tool
upgrade/ue-5-8-x
```

### Separate LFS and non-LFS work

As the default team workflow:

- Git LFS/binary asset changes go on their own focused branch.
- Code, configuration, documentation, and other text changes go on a
  separate focused branch.
- Unrelated work must never be bundled into the same branch just because
  it was done at the same time.

Avoid a branch containing unrelated changes such as:

```text
BP_OuterWall.uasset
M_Colosseum.umap
PlayerController.cpp
README.md
Visual Studio configuration
```

Prefer:

```text
asset/exterior-wall
docs/exterior-workflow
code/exterior-controller
```

If one feature genuinely requires binary and text changes to function,
use coordinated branches and Pull Requests when practical.

Document dependencies clearly, for example:

```text
PR A: asset/exterior-wall
PR B: code/exterior-wall-placement

PR B depends on PR A.
```

This rule exists because Unreal binary assets cannot be meaningfully
line-merged like source code.

## 8. Pull Request Policy

All shared changes enter `main` through Pull Requests.

Required flow:

```text
Working branch
      ↓
Commit
      ↓
Push
      ↓
Pull Request
      ↓
Review / checks
      ↓
Merge
      ↓
main
```

Do not normally merge shared feature work directly into `main` from a
local workstation.

### Pull Request checklist

Before merge:

- [ ] Scope is focused.
- [ ] Branch name matches the work type.
- [ ] LFS rules are satisfied.
- [ ] Required LFS locks were respected.
- [ ] No unrelated generated files are committed.
- [ ] Unreal project opens.
- [ ] Affected maps and Blueprints load.
- [ ] No unexplained new Output Log errors appear.
- [ ] Relevant Editor/PIE/Standalone testing is recorded.
- [ ] Plugin/config changes are documented.
- [ ] Simulation-template dependencies are documented.
- [ ] Asset moves were performed through Unreal.
- [ ] Redirectors were fixed where required.
- [ ] Known limitations are recorded.
- [ ] `main` will remain usable after merge.

## 9. Commit Message Template

The repository includes the shared template:

```text
.gitmessage
```

Do not duplicate the template in documentation.

Configure Git once per clone:

```bash
git config commit.template .gitmessage
```

Then commit with:

```bash
git commit
```

### Commit-message rules

- Aim for a subject around 50 characters.
- Use imperative mood.
- Do not end the subject with a period.
- Leave one blank line between subject and body.
- Wrap body lines at no more than 72 characters.
- Explain why the change was necessary.
- Identify important Unreal assets/code/config changes.
- Record how the change was tested.

Example:

```text
feat(exterior): add modular outer wall

Why:
- Establish the reusable exterior bay used around the main ellipse.

Changes:
- Add the first modular outer-wall bay.
- Add reusable arch and pier geometry.
- Align the module to the shared Colosseum dimensions.

Assets:
- Content/Colosseum/Architecture/Exterior/Meshes/SM_OuterWall.uasset
- Content/Colosseum/Architecture/Exterior/Blueprints/BP_OuterWall.uasset

Testing:
- Editor: tested
- PIE: tested
- Standalone: not tested
- Packaged build: not tested

Notes:
- Entrance-specific bay variants are still required.
```

For a small self-explanatory change:

```text
docs(readme): clarify template structure
```

## 10. Unreal Content Practices

### Naming

Use stable type prefixes where appropriate, for example:

```text
BP_   Blueprint
SM_   Static Mesh
SK_   Skeletal Mesh
M_    Material
MI_   Material Instance
T_    Texture
WBP_  Widget Blueprint
NS_   Niagara System
S_    Sound
M_    Map only when project naming makes it unambiguous
```

Do not rename large numbers of assets casually. Renames can create
redirectors and noisy binary changes.

### Ownership boundary

New project content should normally live under:

```text
Content/Colosseum/
```

Do not copy template assets into the Colosseum namespace merely to make
the directory tree look cleaner.

Copy or migrate template content only when there is a functional reason
and the dependency has been tested.

## 11. Testing

For important changes, record which of the following were tested:

```text
Editor
PIE
Standalone
Packaged build
```

Suggested smoke-test progression:

```text
COL.Basic.ProjectLoads
COL.Basic.MainMapLoads
COL.Player.Spawns
COL.Player.Move
COL.Player.CameraSwitch
COL.Input.ExplorationContext
```

Use Unreal Data Validation as the project grows.

Suggested validation areas:

```text
asset naming
folder placement
missing references
invalid dependencies
required metadata
oversized textures
project-specific rules
```

Progression:

```text
Manual validation
    ↓
Repeatable checklist
    ↓
Custom validators
    ↓
Command-line validation
    ↓
CI / PR validation
```

## 12. Historical Sources and Asset Provenance

Important reconstruction decisions belong under `Documentation/`.

For architectural decisions, record:

```text
Element:
Source:
Dimension / value:
Confidence:
Reasoning:
Notes:
Last reviewed:
```

Suggested confidence values:

```text
HIGH
MEDIUM
LOW
SPECULATIVE
```

For externally created assets, record:

```text
Unreal asset:
Source file:
Creator:
License / usage rights:
Import settings:
Scale:
Last imported:
Notes:
```

Do not rely on a `.uasset` as the only surviving source copy.

## 13. Engine and Toolchain Upgrades

Never upgrade `main` directly.

Use a controlled branch:

```text
main
 ↓
upgrade/<version>
 ↓
Tag/record known-good baseline
 ↓
Upgrade
 ↓
Regenerate project files
 ↓
Compile
 ↓
Run Data Validation
 ↓
Run smoke tests
 ↓
Open key maps
 ↓
Test PIE / Standalone
 ↓
Test packaged build
 ↓
Pull Request
 ↓
Review
 ↓
Merge
```

Unreal upgrades can resave many binary assets. Treat an engine upgrade as
a migration, not a casual editor setting change.

## 14. Troubleshooting

If setup, build, asset, Git LFS, plugin, or editor problems occur, use:

```text
TROUBLESHOOTING.md
```

When adding a new troubleshooting entry, include:

```text
Symptom:
Environment:
Exact error:
Likely cause:
Diagnostic steps:
Fix:
Verification:
Related files/settings:
Date confirmed:
```

Avoid documenting a workaround as a permanent fix unless it has been
verified.

## 15. Final Pre-Push Check

Before pushing:

```bash
git status
git diff
git lfs status
```

Confirm:

- only intended files changed
- binary files are tracked correctly
- no generated cache/build output slipped into Git
- required testing was performed
- commit message follows `.gitmessage`
- branch contains one coherent unit of work
