# 8. Mod Loaders and Script Extenders (Reference)

This is the most repeated question on the Discord: *"what do I even install to put my code inside this game?"*

Read this page once and you can skip the hunting. It lists what each engine family gives you and how hard the job looks.

A passthrough mod needs one thing: **a way to run your own code inside the host game.** Everything else here is a nice-to-have.

## The one table that matters

| Host game | Engine | Loader / extender | Language | Difficulty |
|-----------|--------|-------------------|----------|-----------|
| Skyrim / Skyrim SE / AE | Creation Engine | SKSE ([skse.silverlock.org](https://skse.silverlock.org/)) | C++ + Papyrus | Easy |
| Fallout 4 | Creation Engine | F4SE ([f4se.silverlock.org](https://f4se.silverlock.org/)) | C++ + Papyrus | Easy |
| Starfield | Creation Engine 2 | Same approach as F4SE | C++ | Medium |
| Minecraft: Java | n/a | Fabric, Forge, or [NeoForge](https://neoforged.net) | Java / Kotlin | Easy |
| Outer Wilds | Unity | [Outer Wilds Mod Loader](https://outerwildsmods.com/) | C# | Medium |
| GTA San Andreas / Vice City / GTA III | RenderWare | [plugin-sdk](https://github.com/DK22Pac/plugin-sdk) (ASI / CLEO plugins) | C++ / C | Medium |
| Most Unity games | Unity | [BepInEx](https://github.com/BepInEx/BepInEx) or [MelonLoader](https://github.com/LavaGang/MelonLoader) | C# | Easy or Medium |
| Most Unreal games | Unreal | [UE4SS](https://github.com/UE4SS-RE/RE-UE4SS) | Lua | Medium |
| GameMaker Studio 1.4 and 2 | GameMaker | [UndertaleModTool](https://github.com/UnderminersTeam/UndertaleModTool) | GML + tool | Medium on Windows only |
| Ren'Py visual novels | Ren'Py | [Ren'Py SDK](https://www.renpy.org/doc/html/developer_tools.html) | Python | Easy |

The Bethesda script extenders come from `afkmods.com`, and the silverlock.org links above are what SkyCraft and FalloutCraft point people at.

On GameMaker: there is no GameMaker 3. UndertaleModTool covers GameMaker Studio 1.4 and GameMaker Studio 2, bytecode versions 13 through 17. It can't touch YYC-compiled games, and there's no official way to run its GUI on macOS or Linux, so on those platforms you need Wine.

## What the words mean

- **Script extender**: a third-party DLL that loads alongside the game and gives mods a scripting language and an API. SKSE and F4SE are the classic examples. You write a plugin, it runs in-process.
- **Mod loader / mod API**: a supported framework the modding community built for a game. Fabric and NeoForge for Minecraft. Same idea, more formal.
- **ASI loader**: the smallest possible thing, loading `.asi` DLLs from a folder and doing nothing else. plugin-sdk gives you a real SDK on top of it.
- **Mod manager**: a tool for installing and versioning other people's mods (MO2, Vortex, r2modman). Useful, not required.

**Key idea:** with a loader, the agent writes your mod against its API and you never touch the original binaries. That's the good path. Without one, you have more options than "reverse engineer everything". Most games ship their gameplay logic in a data file you can edit directly, even when there's no mod API for it.

## Which route is cheapest?

A loader is the comfortable route, not the only one. Work down this list and take the first one that reaches your idea.

| Route | When it fits | Examples |
|---|---|---|
| **Data or assets only** | The idea fits the game's own data files, no code needed | Bethesda ESP and ESL files, Paradox scripts, JSON content packs |
| **Loader API** | A loader exists and exposes hooks | tModLoader, SMAPI, BepInEx, UE4SS, REFramework, SKSE, Fabric |
| **Managed-code patching** | .NET, Mono, IL2CPP or Java, but no API for your idea | Harmony, MonoMod, Mixin |
| **Native hooks** | C/C++ engine with no loader | Proxy DLLs plus MinHook or SafetyHook, signature scans |
| **Reimplement or decompile** | You want total control, or it's a retro console | N64 and Xbox decomp projects, or a Rust rewrite like IW4L |
| **Mashup or passthrough** | You're putting one game inside another | See [guide 2](02-passthrough-mods.md) |

The order matters. Plenty of ideas that look like they need a native hook are really a data-file edit, and data edits don't need a loader at all.

Before you commit to any of this, search whether anyone has already done it. A field-note knowledge base exists for exactly this: [universal-modder](https://github.com/rehan-remade/universal-modder) ships one with notes per game covering the versions that worked, the route chosen, and the gotchas, searchable with `um kb search "<game>"`.

## Why this decides whether your idea is realistic

Before you commit to a game pair, check this table:

1. **Does the host game have a loader?** With one, a passthrough mod is realistic this weekend. Without one, expect a research project.
2. **Does the gameplay game have a mod API or an SDK?** Same question. Minecraft (Fabric) is easy. A closed-source game with nothing is hard.
3. **Is there an existing mod that already does something similar?** If so, read its source. You aren't starting from zero.
4. **Is it single-player and offline?** If not, stop. See [guide 6](06-rules-legal-and-publishing.md).

The combination "host has a loader + gameplay game has an API" is what makes SkyCraft-style projects work in hours rather than months. SkyCraft is Skyrim (SKSE) + Minecraft (Fabric): two of the best-documented modding targets in existence.

## Engine families, in more detail

The table above lists loaders by host game. These sections explain what each engine family gives you.

### Creation Engine (Skyrim, Fallout 4)

The best-documented modding family for native-code work, and what SkyCraft and FalloutCraft are built on.

- **SKSE / F4SE** load a plugin DLL and expose a scripting layer (Papyrus) alongside it. Address Library gives plugins access to game functions.
- Mods usually split into two halves: a native plugin (C++) and a Papyrus script. A passthrough mod needs the native side, because it has to run every frame.
- Fallout 4 and Skyrim share enough architecture that SkyCraft's design ports across with modest changes. FalloutCraft did it by keeping SkyCraft's Fabric mod with its own changes and writing a new F4SE plugin, which is why some SkyCraft features were never ported: digging, lighting, water, NPC pathing around blocks, skill training, and multiplayer.

Outer Wilds is Unity, not Creation Engine. OWCraft, the third project in this family, is Unity with the Outer Wilds Mod Loader.

### Windows is the common denominator

Every passthrough project referenced in these guides targets Windows: SkyCraft, FalloutCraft, OWCraft, and GTA San AnSkateas.

That splits two ways:

- **Passthrough mods** need the host game running, so they're bound to the platform the game runs on. Mod loaders are Windows tools. Running the game under Wine or Proton is untested territory and nobody here has reported getting one working.
- **Rust rewrites** are cross-platform, since the engine is your own code. IW4L documents Linux and macOS build steps, so it builds on both. Be aware it ships a prebuilt Windows release as the easy path, and that a *completed* rewrite still won't save you if the game only runs on Windows: your own install has to be readable from whatever OS you're on.

If you've gotten a passthrough mod running on Linux or macOS, that's genuinely useful and the page should say so.

### Unity

The most common engine in modern indie games, and the easiest to get into.

- **BepInEx** patches the game at load and loads your C# assemblies. Works with both Mono and IL2CPP builds.
- **MelonLoader** does the same job with a different API and better support for more title variants. Pick one; don't install both.
- **Mono or IL2CPP matters more than which loader you pick.** On Mono builds the game's code is real C#, so [ILSpy](https://github.com/icsharpcode/ilspy) decompiles it directly and you can read the game's own logic. On IL2CPP builds the method bodies are native, so you recover types and signatures with [Cpp2IL](https://github.com/SamboyCoding/Cpp2IL) and then read the bodies in Ghidra. If you don't know which you have, that's the first thing to check.

### GameMaker

- **UndertaleModTool** reads the game's data files and code as text, edits them, and writes them back. It's the most approachable modding target on this list, and a good one to learn on if you want to see how a game works internally.
- Limits worth knowing: GameMaker Studio 1.4 and GameMaker Studio 2 only (bytecode 13 to 17), no YYC-compiled games, and no official GUI build for macOS or Linux.

### Unreal Engine

- **UE4SS** injects a Lua scripting layer, generates a live SDK dump, and gives you a property editor for poking at a running game. The property editor alone makes it good for exploration: you can read the value of anything and find out what a variable does.
- Games ship in Unreal 4 and Unreal 5 with very different internals. UE4SS support varies by title, so check before you commit.

### Open-source engine reimplementations

Not loaders, but the same family of work as a rewrite. Worth reading because they are finished, licensed, and documented:

| Project | Original | What it shows |
|---------|----------|----------------|
| [OpenMW](https://github.com/OpenMW/openmw) | Morrowind | A full engine reimplementation with a long public history |
| [OpenRCT2](https://github.com/OpenRCT2/OpenRCT2) | RollerCoaster Tycoon 2 | A polished reimplementation that improved on the original |
| [OpenTTD](https://github.com/OpenTTD/OpenTTD) | Transport Tycoon Deluxe | Long-running, mature, well-documented |

These took years and many contributors. Read them for structure, not as a template for a weekend project.

## How to install a loader

The process is almost always the same four steps. Ask your agent to walk you through it, but it looks like:

1. Find the loader's official site and download the version matching **your game version exactly**.
2. Extract it into the game's install folder. There is usually no installer.
3. Start the game once. The loader writes a log and creates a plugins or mods folder.
4. Put your file in that folder and start the game again.

The log file is your friend. When something doesn't load, the loader almost always says why.

### Version mismatch is the number one problem

Loaders and mod APIs are pinned to specific game versions. A loader built for Skyrim 1.5.97 won't load correctly in 1.6.1170. Members have lost whole evenings to this.

- Write your exact game version in your README and in every issue you file.
- Keep a downgrade tool around if the game is old. GTA San AnSkateas needs GTA San Andreas at **version 1.0 US**, which is neither the current Steam release nor the Definitive Edition, so it uses an open-source downgrader ([gtasa-open-downgrader](https://github.com/xxanqw/gtasa-open-downgrader)).

A downgrader is a version-matching tool, not a DRM tool. It exists so a copy you already own reaches the build a mod was written against. See [guide 6](06-rules-legal-and-publishing.md) for where that line sits.

## Disc-based and console games

Some projects need a game you can't install from Steam. GTA San AnSkateas needs Skate 3 for Xbox 360, extracted from your own disc's ISO with a tool like [extract-xiso](https://github.com/XboxDev/extract-xiso), or from a Games on Demand copy with Velocity.

What that means in practice:

- **Extract from your own disc or your own dump.** The ISO is your copy of a game you bought, so it's fair game for personal use.
- **Never take an ISO from a download site or a torrent.** That's the one thing that puts you on the wrong side of the rules in [guide 6](06-rules-legal-and-publishing.md), and it taints the whole project.
- **Keep the extracted files outside your repo.** They're game data, so they belong in your gitignored folder like everything else.

## Checklist before you pick a host game

- [ ] I know the engine (Creation Engine, Unity, Unreal, GameMaker, custom)
- [ ] There is a loader or mod API for it, and it's current
- [ ] It's single-player or offline
- [ ] I own it and the loader is a legitimate public tool
- [ ] I know the exact game version and whether I need to downgrade
- [ ] I've found at least one existing mod for this game, so I can read real code

Can't tick all six? Look at a different game. That isn't giving up, and it gets something on screen sooner.

---

<sub>[Spot a mistake? [Edit this page on GitHub](https://github.com/trevaintdead/ai-game-modding-guides/edit/main/guides/08-mod-loaders-and-script-extenders.md).](https://github.com/trevaintdead/ai-game-modding-guides/edit/main/guides/08-mod-loaders-and-script-extenders.md) &middot; [Open an issue](https://github.com/trevaintdead/ai-game-modding-guides/issues/new) &middot; Part of [AI Game Modding Guides](https://github.com/trevaintdead/ai-game-modding-guides)</sub>
