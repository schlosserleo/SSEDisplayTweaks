# Building SSEDisplayTweaks

State as of the 0.5.17 / Skyrim SE 1.7.99 port (2026-08). Upstream's original
build environment (SlavicPotato's `sse-build-resources` repo + vcpkg) is gone;
this documents the reconstructed, locally maintained setup.

## Directory layout

The solution expects these siblings next to the repo:

```
I:\source\
  SSEDisplayTweaks\        this repo
  sse-build-resources\     reconstructed support lib (local git repo, no remote)
  sparse-map\              https://github.com/Tessil/sparse-map (headers only)
  DirectXTK\               https://github.com/microsoft/DirectXTK
```

### sse-build-resources

Reconstructed from the `clayne/sse-build-resources` fork (upstream deleted) +
SKSE64 2.0.17 sources + the bundled `skse64-patch.zip`, then modernized locally:
format-5 Address Library support, SKSE 2.3.0 runtime constants and
`SKSEPluginVersionData` layout, versioned `OpenLog`/`GetLogPath`, and a number
of shim headers the fork was missing. See that repo's log for details. The
vendored `skse64/` tree is committed there — do not re-extract it from the
patch zip, it contains local fixes.

### DirectXTK

Build once before building the plugin:

1. Run `Src\Shaders\CompileShaders.cmd` (needs `fxc.exe` from the Windows SDK
   on PATH, e.g. `C:\Program Files (x86)\Windows Kits\10\bin\<ver>\x64`).
2. Build `DirectXTK_Desktop_2026.vcxproj`, Release x64.
3. The plugin includes headers as `<directxtk/...>` (vcpkg-style); a directory
   junction provides that layout:
   `mklink /J DirectXTK\include\directxtk DirectXTK\Inc`

## Building the plugin

Only the **Release MD | x64** configuration is maintained. From the repo root:

```
MSBuild SSEDisplayTweaks.sln -m ^
  -p:Configuration="Release MD" -p:Platform=x64 ^
  -p:PlatformToolset=v145 ^
  -p:PostBuildEventUseInBuild=false ^
  -p:VcpkgEnabled=false ^
  -p:ShouldUnsetParentConfigurationAndPlatform=false
```

- `PlatformToolset=v145`: VS 2026 toolset (projects predate it).
- `PostBuildEventUseInBuild=false`: skips upstream's copy-to-game-dir steps
  (they reference `$(SSEPath)`/`$(AE_CURRENT_PATH)` env vars).
- `VcpkgEnabled=false`: vcpkg is no longer used; its former role (DirectXTK)
  is covered by the sibling checkout.
- `ShouldUnsetParentConfigurationAndPlatform=false`: required so the skse64
  child projects (referenced by sse-build-resources but not present in the
  solution) inherit the configuration instead of defaulting to Debug|Win32.

Output: `x64\SSEDisplayTweaks\Release MD\SSEDisplayTweaks.dll`.

## Runtime support

| Runtime            | Mechanism                                            |
| ------------------ | ---------------------------------------------------- |
| 1.5.23 – 1.5.97    | SE address library (`version-*.bin`, format 1)       |
| 1.6.317 – 1.6.1179 | AE address library (`versionlib-*.bin`, format 2)    |
| 1.7.99             | new flat-table database (`versionlib-*.bin`, format 5) |

AE-side patch offsets are version-tiered in code (`VER_1_6_342` / `_629` /
`_1130` / `VER_1_7` gates in `data.h` and the drivers). The 1.6.1130 tier was
recovered from the compiled 0.5.16 release; the 1.7.99 tier was derived by
disassembly of the 1.7.99 executable and is verified against it site by site.
