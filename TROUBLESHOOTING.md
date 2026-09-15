# Troubleshooting

This document contains repeatable recovery steps for common Unreal Engine,
Visual Studio, Unreal Build Tool, Git, Git LFS, and project-integration
problems.

For the normal development workflow, see [`CONTRIBUTING.md`](CONTRIBUTING.md).

---

## Unreal Build Tool reports Workspace Generator 1.0.0

### Symptoms

Visual Studio may report something similar to:

```text
Detected Workspace Generator version: 1.0.0.
Detected Unreal Engine Version: 5.8.1.
```

The **Unreal Build Tool Status** section may also say that Unreal Build Tool
is not using the latest available Workspace Generator and offer an
**Update** button that appears to do nothing.

### Important distinction

`1.0.0` is the **Workspace Generator version reported by Unreal Build Tool**.
It is not the Unreal Engine version and it is not the Visual Studio version.

Do **not** manually change a version string from `1.0.0` to `1.x` merely to
make the status check turn green. Visual Studio needs the matching Workspace
Generator implementation, not just a different number in a source file.

The goal of the repair below is to make Unreal Build Tool actually use the
newer generator supplied/supported by Visual Studio.

---

### Step 1 - Close Unreal Engine and Visual Studio

Save all work, then close:

```text
Unreal Editor
Visual Studio
Epic Games Launcher dialogs that are modifying the engine
```

Open **Task Manager** and make sure there is no stuck process such as:

```text
UnrealBuildTool.exe
dotnet.exe
UnrealEditor.exe
devenv.exe
```

Do not terminate an active build that you still need.

---

### Step 2 - Check the Workspace Generator version manually

Open **PowerShell as Administrator**.

Set the Unreal Engine installation path. Adjust it if Unreal Engine is
installed somewhere else:

```powershell
$UE = "C:\Program Files\Epic Games\UE_5.8"
```

Confirm that the Unreal Build Tool launcher exists:

```powershell
Test-Path "$UE\Engine\Build\BatchFiles\RunUBT.bat"
```

Expected result:

```text
True
```

Ask Unreal Build Tool which Workspace Generator version it is using:

```powershell
& "$UE\Engine\Build\BatchFiles\RunUBT.bat" -WorkspaceGeneratorVersion
```

Record the result before changing anything.

For the problem covered by this section, the result will normally identify
version `1.0.0`, while Visual Studio expects a newer `1.x` generator.

---

### Step 3 - Update Visual Studio first

In Visual Studio:

```text
Help
  -> Check for Updates
```

Install the current approved Visual Studio update for the project.

Then open **Visual Studio Installer**:

```text
Visual Studio Installer
  -> Modify
  -> Workloads
  -> Game development with C++
```

Under the workload's optional/installation details, make sure the Unreal
integration components required by the team are installed, especially:

```text
Visual Studio Tools for Unreal Engine
Visual Studio debugger tools for Unreal Engine Blueprints
Unreal Engine Test Adapter
required Windows SDK
```

Apply any pending changes and restart Visual Studio when requested.

---

### Step 4 - Try the supported Workspace Generator update

Open `ColosseumV2.uproject` in Visual Studio.

Go to:

```text
Project
  -> Configure Tools for Unreal Engine
```

Find:

```text
Unreal Build Tool Status
```

Then:

```text
1. Click Refresh.
2. Wait for the status check to finish.
3. If Update appears, click Update.
4. Open View -> Output.
5. Watch the Unreal/Unreal Engine integration output until the update
   finishes and the workspace is ready.
6. Click Refresh again.
```

If the Workspace Generator is now reported as the required `1.x` version,
stop here.

---

## UE 5.8.2: Workspace Generator update stops at 51%

Use this section when the Visual Studio Workspace Generator update begins,
reaches approximately **51%**, and then fails while rebuilding Unreal Build
Tool. After the failure, Visual Studio continues to report:

```text
Detected Workspace Generator version: 1.0.0.
Detected Unreal Engine Version: 5.8.2.
```

### Typical compiler error

The Unreal Engine Integration output may contain an error similar to:

```text
error CS7036: There is no argument given that corresponds to the required
parameter 'Logger' of 'VCToolChain.GetVCIncludePaths(...)'
```

The percentage itself is not the problem. At roughly 51%, Visual Studio is
building `UnrealBuildTool.csproj`. The build fails because the Workspace
Generator patch contains a call written for an older
`GetVCIncludePaths()` signature.

In UE 5.8.2, the matching overload requires an additional
`Microsoft.Extensions.Logging.ILogger` argument. Because Unreal Build Tool
cannot compile, the Workspace Generator update never completes and the
reported version remains `1.0.0`.

This is an **Unreal Build Tool / Visual Studio patch compatibility problem**.
It is not fixed by changing the displayed version number, repeatedly clicking
**Update**, reinstalling the project, or changing `ColosseumV2` source code.

### Step A - Stop retrying the failed update

Save the Visual Studio Output showing the compile error.

Close Unreal Editor. Close Visual Studio before editing engine source. If the
engine is installed under `C:\Program Files`, use an administrator account for
the repair.

Do not keep clicking **Update**. The same source error will fail at the same
build stage until the patched caller is corrected.

### Step B - Locate every `GetVCIncludePaths()` call in Unreal Build Tool

Open **PowerShell as Administrator** and run:

```powershell
$UE = "C:\Program Files\Epic Games\UE_5.8"
$UBT = "$UE\Engine\Source\Programs\UnrealBuildTool"

Get-ChildItem $UBT -Recurse -Filter *.cs |
    Select-String "GetVCIncludePaths\(" |
    Select-Object Path, LineNumber, Line
```

The Visual Studio Workspace Generator patch commonly modifies a file under:

```text
Engine\Source\Programs\UnrealBuildTool\ProjectFiles\VisualStudioWorkspace\
```

A common affected file is:

```text
VSWorkspaceProjectFile.cs
```

Use the compiler error's file name and line number as the final authority.
Patch layouts can change between Visual Studio releases.

### Step C - Back up the affected source file

Before editing the file, make a backup. For example:

```powershell
$File = "$UBT\ProjectFiles\VisualStudioWorkspace\VSWorkspaceProjectFile.cs"
Copy-Item $File "$File.pre-ue582-fix.bak" -Force
```

If the compiler identified a different file, set `$File` to that exact path.

### Step D - Fix the caller, not the UE 5.8.2 method declaration

Do **not** remove the `ILogger` parameter from `VCToolChain.cs`. UE 5.8.2's
method declaration is the newer API. The stale Workspace Generator caller is
what needs to be updated.

Find the failing call. The important change is to pass `Logger` as the final
argument.

Conceptually, change:

```csharp
VCToolChain.GetVCIncludePaths(
    ModuleCompileEnvironment.Platform,
    WindowsCompiler.VisualStudio2022,
    null,
    null)
```

to:

```csharp
VCToolChain.GetVCIncludePaths(
    ModuleCompileEnvironment.Platform,
    WindowsCompiler.VisualStudio2022,
    null,
    null,
    Logger)
```

Do not blindly replace the first four arguments. Keep the arguments already
used by the installed Visual Studio patch and append the required `Logger`
argument in the position required by UE 5.8.2.

### Step E - If `Logger` is not in scope

Some Workspace Generator patch revisions may also use an older helper method
that does not receive a logger. If the next compiler error says:

```text
The name 'Logger' does not exist in the current context
```

thread the existing logger through the helper rather than creating a fake or
null logger.

For example, change the helper call from:

```csharp
ExportModule(ModuleCpp, TargetToolChain, ModuleCompileEnvironment)
```

to:

```csharp
ExportModule(ModuleCpp, TargetToolChain, ModuleCompileEnvironment, Logger)
```

and change the helper signature from:

```csharp
private static ExportedModuleInfo ExportModule(
    UEBuildModuleCPP Module,
    UEToolChain TargetToolChain,
    CppCompileEnvironment ModuleCompileEnvironment)
```

to:

```csharp
private static ExportedModuleInfo ExportModule(
    UEBuildModuleCPP Module,
    UEToolChain TargetToolChain,
    CppCompileEnvironment ModuleCompileEnvironment,
    ILogger Logger)
```

Then pass that `Logger` into `GetVCIncludePaths(...)`.

`VSWorkspaceProjectFile.cs` normally already imports:

```csharp
using Microsoft.Extensions.Logging;
```

If the affected file does not, add that `using` directive rather than using a
fully qualified type throughout the file.

### Step F - Rebuild Unreal Build Tool manually

Still in an elevated PowerShell or command prompt, rebuild UBT directly:

```powershell
dotnet.exe build `
    "$UE\Engine\Source\Programs\UnrealBuildTool\UnrealBuildTool.csproj" `
    -c Development
```

The important result is:

```text
Build succeeded.
```

If it still fails, fix the **first C# compiler error** before doing anything
else. Do not troubleshoot project targets until `UnrealBuildTool.csproj`
compiles successfully.

### Step G - Reopen Visual Studio and verify 1.0.1

Reopen `ColosseumV2.uproject` in Visual Studio and go to:

```text
Project
  -> Configure Tools for Unreal Engine
  -> Unreal Build Tool Status
```

Click **Refresh**.

For the repaired UE 5.8.2 setup, the output should report the newer Workspace
Generator, for example:

```text
Detected Workspace Generator version: 1.0.1.
Detected Unreal Engine Version: 5.8.2.
Unreal Build Tool Workspace Generator includes latest features.
```

Then allow Visual Studio to finish preparing the workspace and verify the
normal Editor target, such as:

```text
ColosseumV2Editor | Development | Win64
```

### Step H - If Visual Studio applies the bad patch again

A Visual Studio update, Unreal integration repair, or Epic Games Launcher
**Verify** can replace modified engine files. If the 51% failure returns:

```text
1. Re-check the compiler error.
2. Search for GetVCIncludePaths() again.
3. Confirm that the final ILogger argument has not been removed.
4. Reapply the compatibility fix only if the same signature error is present.
5. Prefer an updated Microsoft/Epic patch once one is available.
```

Do not automatically reapply this workaround to a later Unreal Engine or
Visual Studio version. First check the current method signature and the
actual compiler error; the API may have changed again.

### Quick diagnosis table

| Observation | Meaning |
|---|---|
| Update stops near 51% | Visual Studio is rebuilding Unreal Build Tool |
| `CS7036` mentions `GetVCIncludePaths` and `Logger` | Workspace Generator caller uses an older API signature |
| Workspace Generator remains `1.0.0` | Update never completed successfully |
| `dotnet build ... UnrealBuildTool.csproj` succeeds | The source-level compatibility error is repaired |
| Refresh reports `1.0.1` | Visual Studio is detecting the updated Workspace Generator |

---

## Manual repair when the Update button does nothing

Use this sequence when Visual Studio continues to detect Workspace Generator
`1.0.0` after **Refresh** and **Update**.

### Step 5 - Verify the Unreal Engine installation

Close Visual Studio and Unreal Editor.

Open **Epic Games Launcher** and go to the installed Unreal Engine version:

```text
Library
  -> Unreal Engine 5.8.x
  -> ... / Options
  -> Verify
```

Let verification finish completely.

This restores missing or damaged launcher-installed engine files. It also
prevents a half-applied Workspace Generator change from being mistaken for
a working installation.

Do not copy a single `UnrealBuildTool.dll` from another computer as a first
repair step. Unreal Build Tool consists of multiple related assemblies and
source/build files that must remain compatible with the installed engine.

---

### Step 6 - Repair the Visual Studio Unreal component

Open **Visual Studio Installer** again.

First use:

```text
Modify
  -> Game development with C++
```

Confirm that **Visual Studio Tools for Unreal Engine** is installed.

If the component is already installed but the Workspace Generator update
still fails, use the Visual Studio Installer's **Repair** option for that
Visual Studio installation.

After repair, restart Windows before testing again. This avoids testing
against an old Visual Studio extension or locked Unreal Build Tool file that
is still loaded in memory.

---

### Step 7 - Run Visual Studio elevated once

If Unreal Engine is installed under a protected directory such as:

```text
C:\Program Files\Epic Games\UE_5.8
```

right-click Visual Studio and choose:

```text
Run as administrator
```

This is a troubleshooting step, not the normal day-to-day way to run Visual
Studio.

Open `ColosseumV2.uproject`, then repeat:

```text
Project
  -> Configure Tools for Unreal Engine
  -> Unreal Build Tool Status
  -> Refresh
  -> Update
```

Watch **View -> Output** for the actual error if the update fails.

---

### Step 8 - Clear stale project workspace data

If Unreal Build Tool was updated but Visual Studio still displays the old
status, close both Visual Studio and Unreal Editor.

From the `ColosseumV2` repository root, remove the Visual Studio cache:

```powershell
Remove-Item -Recurse -Force .vs -ErrorAction SilentlyContinue
```

If the project workspace is still corrupted, the generated Unreal
`Intermediate` directory can also be rebuilt:

```powershell
Remove-Item -Recurse -Force Intermediate -ErrorAction SilentlyContinue
```

`Intermediate` is generated data, but deleting it causes Unreal/Visual
Studio to regenerate build metadata and can make the next project load take
longer.

Do **not** delete:

```text
Config/
Content/
Plugins/
Source/
ColosseumV2.uproject
```

Reopen the `.uproject` and allow Visual Studio to rebuild the workspace.

---

### Step 9 - Verify the repair from the command line

Open PowerShell and run:

```powershell
$UE = "C:\Program Files\Epic Games\UE_5.8"
& "$UE\Engine\Build\BatchFiles\RunUBT.bat" -WorkspaceGeneratorVersion
```

Then check Visual Studio again:

```text
Project
  -> Configure Tools for Unreal Engine
  -> Unreal Build Tool Status
  -> Refresh
```

The command-line result and Visual Studio status should agree on the
Workspace Generator version.

Do not consider the issue fixed merely because a displayed version string
was edited. The Unreal workspace must load successfully and Visual Studio
must be able to generate/refresh targets.

---

### Step 10 - Regenerate project metadata and test

After the Workspace Generator status is healthy:

```text
1. Reopen ColosseumV2.uproject.
2. Let Visual Studio finish preparing the workspace.
3. Refresh Unreal Engine Targets.
4. Generate the required Editor target if it is missing.
5. Build the Development Editor target.
6. Open Unreal Editor.
7. Open the integration/smoke-test map.
8. Run PIE.
9. Check the Output Log for new build or module errors.
```

Do not commit `.vs/`, `Intermediate/`, `Binaries/`, `Saved/`, or other
machine-generated repair artifacts.

---

## What not to do

Do not use these as shortcuts:

```text
- Do not edit only a Workspace Generator version constant to fake 1.x.
- Do not copy one UnrealBuildTool DLL from a different engine version.
- Do not patch main directly.
- Do not commit generated Visual Studio or Unreal cache directories.
- Do not apply old UnrealBuildTool integration patches intended for
  Unreal Engine 5.3 or earlier to Unreal Engine 5.8.x.
```

Microsoft's legacy manual `UnrealBuildTool-5.x.patch` procedure is documented
for Unreal Engine versions **before 5.4**. Unreal Engine 5.8.x should use the
current Visual Studio Unreal integration/update path instead.

---

## If it still reports 1.0.0

Capture the following before making more changes:

```text
Unreal Engine version:
Unreal Engine install path:
Visual Studio exact version:
MSVC version:
Windows SDK version:
Visual Studio Tools for Unreal Engine installed: Yes / No
RunUBT.bat -WorkspaceGeneratorVersion output:
Visual Studio Unreal Build Tool Status message:
Relevant View -> Output errors:
```

Also compare the exact Visual Studio build and Unreal-related Visual Studio
Installer components with a known-good development computer. Matching only
the Unreal Engine version is not enough to prove both machines have the same
Visual Studio Unreal integration.

At this point, keep the `1.0.0` output as evidence rather than modifying it
manually. The remaining problem is an update/integration mismatch that needs
to be diagnosed from the Visual Studio Output log.

---

## References

- Microsoft Learn: Visual Studio Unreal Engine project support and Unreal
  Build Tool Status
  <https://learn.microsoft.com/en-us/visualstudio/gamedev/unreal/get-started/vs-tools-unreal-uproject>
- Microsoft Learn: Install Visual Studio Tools for Unreal Engine
  <https://learn.microsoft.com/en-us/visualstudio/gamedev/unreal/get-started/vs-tools-unreal-install>
- Epic Games: Unreal Engine 5.8 project-file generation
  <https://dev.epicgames.com/documentation/unreal-engine/how-to-generate-unreal-engine-project-files-for-your-ide>
- Epic Games: Unreal Engine 5.8 build configuration
  <https://dev.epicgames.com/documentation/unreal-engine/build-configuration-for-unreal-engine>
