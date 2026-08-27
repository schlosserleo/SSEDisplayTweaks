# SSEDisplayTweaks

Fork of [SlavicPotato/SSEDisplayTweaks](https://github.com/SlavicPotato/SSEDisplayTweaks)
with support for Skyrim Special Edition **1.7.99/1.7.104** (v0.5.17+). Supported
runtimes: 1.5.23–1.5.97, 1.6.317–1.6.1179, 1.7.99 and 1.7.104.

Original mod page: <https://www.nexusmods.com/skyrimspecialedition/mods/34705>

Releases: <https://github.com/schlosserleo/SSEDisplayTweaks/releases>

The 1.7.99 port was produced with heavy AI assistance (reverse engineering,
offset re-derivation and verification by an AI coding agent, directed and
tested by a human). See the commit history for exactly what changed.

## Build deps

* [SKSE64](https://skse.silverlock.org)
* [sse-build-resources](https://github.com/schlosserleo/sse-build-resources) —
  modernized fork (the original repo was deleted); run its `setup.ps1` once
  after cloning
* [DirectXTK](https://github.com/Microsoft/DirectXTK)
* [sparse-map](https://github.com/Tessil/sparse-map)

See [BUILDING.md](BUILDING.md) for the directory layout and the exact
MSBuild invocation.
