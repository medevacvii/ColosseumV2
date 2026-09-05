# ColosseumV2

A Roman Colosseum reconstruction and simulation project built with
Unreal Engine 5.8.x.

The project was created from Unreal Engine's **Simulation Blank**
template. The Unreal-generated project structure is treated as the
engine-owned baseline. Colosseum-specific organization is layered on top
of that structure rather than replacing it.

## Project Structure

```text
ColosseumV2/
├── Config/                         # Unreal configuration
├── Content/                        # Unreal-managed assets
│   ├── <Simulation template>/      # Keep template content in place
│   ├── Colosseum/                  # Project-owned Unreal content
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
├── Source/                         # Unreal C++ modules
├── Plugins/                        # Project and template plugins
├── Assets/                         # Original/source assets outside UE
│   ├── Blender/
│   ├── Reference/
│   ├── Historical/
│   ├── Photogrammetry/
│   └── SourceTextures/
├── Documentation/
│   ├── References/
│   ├── Dimensions/
│   ├── Decisions/
│   ├── HistoricalSources/
│   ├── Guides/
│   └── Troubleshooting/
├── Script/                         # Repository/helper scripts
├── Tests/                          # External test data/scripts
├── Builds/                         # Packaged output; normally ignored
├── .gitignore
├── .gitattributes
├── .gitmessage
├── CONTRIBUTING.md
├── PROJECT_PLAN.md
├── TROUBLESHOOTING.md
└── ColosseumV2.uproject
```

## Important Directory Rule

`Content/` is Unreal Engine territory.

Unreal assets such as the following belong under `Content/`:

- `.uasset`
- `.umap`
- Blueprints
- materials and material instances
- static and skeletal meshes
- imported textures
- Niagara systems
- animations
- sounds

The top-level `Assets/` directory is for **source material outside
Unreal**, for example:

```text
Assets/Blender/Colosseum_OuterWall.blend
```

which may produce an Unreal asset such as:

```text
Content/Colosseum/Architecture/Exterior/Meshes/SM_OuterWall.uasset
```

Do not use `Assets/` as a second Unreal Content directory.

## Simulation Template Policy

Do **not** reorganize the Simulation template simply to make it match the
Colosseum directory design.

Keep template folders where Unreal created them unless there is a
specific, tested reason to migrate them.

Use:

```text
Content/Colosseum/
```

as the ownership boundary for new Colosseum-specific content.

This makes it easier to distinguish:

```text
Epic/template content
        versus
Colosseum project content
```

When an existing Unreal asset must be moved, move it through the Unreal
Editor Content Browser and fix redirectors afterward. Do not move
`.uasset` or `.umap` files with File Explorer or Git Bash.

## Generated Unreal Directories

The following directories are generated and should normally remain
outside version control:

```text
Binaries/
DerivedDataCache/
Intermediate/
Saved/
```

Generated Visual Studio solution/workspace files are also treated as
regeneratable development files rather than part of the permanent
project architecture.

Always follow the repository `.gitignore`.

## Plugins

Keep `Plugins/` in the project because the Simulation template and the
project may depend on plugins.

Within a plugin, source/config/content may need to be committed while
generated directories such as `Binaries/` and `Intermediate/` normally
do not.

Follow `.gitignore` and the plugin's documentation.

## Git LFS

The project uses Git LFS for Unreal binary assets.

After cloning:

```bash
git lfs install
git lfs pull
```

Files covered by `.gitattributes` must remain in LFS.

Shared lockable Unreal assets should be locked before editing:

```bash
git lfs locks
git lfs lock path/to/asset.uasset
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for the complete locking,
branching, and Pull Request workflow.

## Branching Summary

Do not develop directly on `main`.

As a default rule, keep Git LFS/binary asset work separate from
code/documentation/configuration work.

Examples:

```text
asset/exterior-wall
level/integration-map
blueprint/door-system

code/player-controller
docs/project-structure
fix/build-configuration
```

When a feature genuinely requires both binary and text changes, use
coordinated branches and Pull Requests when practical and document the
dependency.

All shared work enters `main` through a Pull Request.

## Commit Message Template

The repository includes:

```text
.gitmessage
```

Configure it once after cloning:

```bash
git config commit.template .gitmessage
```

Then use:

```bash
git commit
```

Commit subjects should be concise, around 50 characters when practical.
Commit body lines should not exceed 72 characters.

The full commit policy and examples are in
[CONTRIBUTING.md](CONTRIBUTING.md).

## Quick Start

```bash
git clone <repository-url>
cd ColosseumV2

git lfs install
git lfs pull

git config commit.template .gitmessage
```

Then:

1. Verify the approved Unreal Engine and Visual Studio toolchain.
2. Verify required plugins.
3. Generate project files if required.
4. Open `ColosseumV2.uproject`.
5. Compile the project.
6. Open the current integration/test map.
7. Run Play In Editor.
8. Check the Output Log for unexplained errors.

## Documentation

| Document | Purpose |
|---|---|
| `README.md` | Project overview and setup |
| `CONTRIBUTING.md` | Team workflow, Git LFS, branches, PRs, testing |
| `PROJECT_PLAN.md` | Roadmap, milestones, workstreams, risks |
| `TROUBLESHOOTING.md` | Build, asset, editor, Git, and recovery help |
| `Documentation/` | Research, dimensions, decisions, guides, references |

## Core Rules

1. Keep Unreal's generated project structure intact.
2. Put new project-owned Unreal content under `Content/Colosseum/`.
3. Do not move existing Unreal assets outside the Unreal Editor.
4. Do not commit generated Unreal cache/build directories.
5. Use Git LFS and locks for applicable binary assets.
6. Keep LFS/binary work separate from unrelated text/code work.
7. Merge shared work into `main` through Pull Requests.
8. Keep source art and historical evidence outside `.uasset` files.
9. Record important architectural decisions and dimensions.
10. Keep `main` buildable and usable.
