# Over the Hill — More Players

An unofficial mod that expands **over the hill** lobbies to **up to 8 players**, with public server discovery and additional player UI.

**Version:** 0.2.0 · **Platform:** Windows x64 / Steam · **Loader:** MelonLoader 0.7.3 x64

> **Experimental:** Hosting and a two-player join have been tested. More than four simultaneous players has **not** been tested, so eight-player stability is not yet confirmed.

## Features

- Select **2–8 players** when creating a server; the default is 8.
- Expanded network collections for player names, ordering, colors, status, map markers, tents, and winch owners.
- Additional lobby spawn points, name labels, player colors, and map indicators.
- Public lobbies use the normal regional server list and the name tag **`[8P MOD REQUIRED]`**.
- Compatibility metadata and connection checks refuse unmodded or incompatible clients before admission.
- Checks the game build and expected instructions before applying changes in memory. The mod does not modify `GameAssembly.dll` on disk.

## Requirements

**Every player needs:**

- The same supported Windows Steam game build.
- [MelonLoader v0.7.3, Windows x64](https://github.com/LavaGang/MelonLoader/releases/tag/v0.7.3).
- The same More Players mod version. Use **0.2.0** for public discovery.

The mod targets a specific game build. A game update may cause it to disable itself until compatibility is reviewed.

## Installation

1. Close the game.
2. In Steam, right-click **over the hill** → **Manage → Browse local files**.
3. Download `MelonLoader.x64.zip` from the official release linked above.
4. Extract it into the game folder, beside `over the hill.exe`.
5. Launch through Steam once. Let MelonLoader finish generating the game bindings, then close the game. The first launch can take longer.
6. Download `MorePlayers-0.2.0.zip` from this repository's **Releases** section, when available, or use a package provided by the maintainer.
7. Extract the package into the game folder. The DLL must end up at `Mods/OverTheHill.MorePlayers.dll`.
8. Launch through Steam again.

Your game folder should contain:

```text
over the hill/
├── over the hill.exe
├── version.dll
├── MelonLoader/
└── Mods/
    └── OverTheHill.MorePlayers.dll
```

To check that the mod loaded, open `MelonLoader/Latest.log` and look for:

```text
More Players v0.2.0
Eight-player network layout enabled
```

## Hosting and joining

### Host

1. Open **Multiplayer → Create Server**.
2. Select up to **8 players**.
3. Choose **Public** for discovery or **Private** for a private session. Public is the default.
4. Share the selected **server region** with your friends.

Hosted room names receive the `[8P MOD REQUIRED]` prefix.

### Join

1. Install the same game build and mod version as the host.
2. Select the host's server region.
3. Find the tagged lobby in the server browser and join it.

Public discovery is intended to let players without the mod see that a modded lobby exists. **They still need to install the mod to play.** The stock game does not provide an automatic download or installation prompt for this mod.

## Compatibility and limitations

- With More Players enabled, joining incompatible vanilla sessions is blocked. Restart without the mod to join those sessions.
- Direct or invite joins require compatible room metadata already obtained from the server list. If an invite cannot be validated, join through the server browser.
- Versions **0.1.x** used separate matchmaking and do not see 0.2.0 rooms.
- Public-list visibility from a separate unmodded client has not been independently confirmed. The host log has recorded incompatible connection refusals.
- Connection markers detect compatibility; they are not authentication against deliberately modified clients.
- Eight-player gameplay, reconnects, scene transitions, camping, winching, and UI layout still need testing with a full group.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| No eight-player option | Confirm the DLL is directly inside `Mods`, then check `MelonLoader/Latest.log` for loading errors. |
| Cannot find a friend's lobby | Use the same region and version 0.2.0 on both PCs. Confirm the host chose Public. |
| Connection refused | Confirm the game build and mod version match. Join through the server browser. |
| Unsupported game build | The game DLL fingerprint differs from the supported build. The mod needs a compatibility update. Do not bypass the check. |
| Antivirus alert | Keep the flagged file quarantined until the exact filename and detection are investigated. Use the official MelonLoader release source; do not assume a detection is a false positive. |

## Testing status

| Check | Status |
| --- | --- |
| Release build | Passed with no warnings or errors |
| Native patch and network layout validation | Passed: 21 instruction patches and 4 layouts |
| Connection protocol checks | Passed: 10 checks covering compatibility and identity preservation |
| Mod startup and host entering gameplay | Passed |
| Friend joining the host | Reported working with 2 players total |
| Incompatible connection refusal | Recorded in host logs |
| Public discovery from an unmodded PC | Not independently verified |
| 5–8 simultaneous players | Not tested |

Testing recorded during development on October 14, 2026. The map indicator fix was included in 0.1.1 and carried into 0.2.0; full-group map and UI testing remains outstanding.

## Reporting issues

Open a GitHub issue with:

- Mod version, MelonLoader version, and game build/version.
- Player count and whether the problem affected the host, a client, or both.
- Steps to reproduce it and the exact error message.
- Relevant excerpts from `MelonLoader/Latest.log` and Unity's `Player.log`.

On Windows, Unity's log is normally at:

```text
%USERPROFILE%\AppData\LocalLow\Funselektor\over the hill\Player.log
```

Review logs for personal information or private session details before posting them publicly.

## Building from source

Requires the **.NET 8 SDK**, .NET 6 targeting references restored by NuGet, and a game installation that has completed MelonLoader's first launch.

From the repository root:

```powershell
dotnet build src/MorePlayers.csproj -c Release
```

For a different game installation directory:

```powershell
dotnet build src/MorePlayers.csproj -c Release '-p:GamePath=D:\SteamLibrary\steamapps\common\over the hill'
```

Output:

```text
src/bin/Release/net6.0/OverTheHill.MorePlayers.dll
```

Run the connection protocol checks with:

```powershell
dotnet run --project tests/DiscoveryProtocol.Tests.csproj
```

### Game build maintenance

`src/manifest.json` contains the supported game DLL SHA-256, expected instruction bytes, patches, and expanded network layouts.

- `tools/build_manifest.py` generates a manifest from a local Il2CppDumper `analysis/dump.cs`.
- `tools/validate_manifest.py` checks the fingerprint, instructions, patch sizes, collection sizing, and layout overlap without modifying the game.
- These Python tools currently use the default Steam installation path and expect `capstone` and `pefile` under `tools/python`. Adjust the paths for a different setup.

Generated bindings, analysis dumps, downloaded tools, and game files are excluded from the repository. Building the mod uses the bindings in your local game installation. Supporting a game update requires reviewing the accessors and layouts, not simply replacing the stored hash.

## Uninstalling

Close the game and remove `Mods/OverTheHill.MorePlayers.dll`.

To disable MelonLoader as well, rename its `version.dll` in the game folder to `version.dll.disabled`. This disables other mods using that loader too.

## Acknowledgments

- [LavaGang/MelonLoader](https://github.com/LavaGang/MelonLoader) — mod loader.
- [Harmony](https://github.com/pardeike/Harmony) and [Il2CppInterop](https://github.com/BepInEx/Il2CppInterop) — runtime patching and IL2CPP interoperability.
- [Il2CppDumper](https://github.com/Perfare/Il2CppDumper) — local build analysis.
- [Expanded Lobbies](https://github.com/Danie1Winn/over-the-hill-multiplayer-mod) — reviewed as a reference during development; its source code is not incorporated into this implementation.

This is an unofficial community mod and is not affiliated with or endorsed by the game's developers.
