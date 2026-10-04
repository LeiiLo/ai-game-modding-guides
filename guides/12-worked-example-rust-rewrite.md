# 12. Worked example: an AI-assisted Rust rewrite

This case study is based on [mw2-rust-rust-rewrite](https://github.com/Dj-Shortcut/mw2-rust-rust-rewrite), an unfinished standalone Rust/Bevy project. It is not a finished game and it does not contain the original game's assets.

## What this project actually is

The intended end product is an original game with its own authored models, materials, sounds and world content. The current development tree is also built on inherited public code:

| Part | Origin and role | What it needs |
|---|---|---|
| `crates/` and authored content | New project code and authored content, much of it written with AI coding agents | The default standalone path uses the repository's authored content |
| IW4L | [vladtrc's](https://github.com/vladtrc/iw4L) from-scratch Rust rewrite/runtime for IW4/MW2 | Its import modes read an MW2 installation supplied by the user |
| `skate/` | Engine crates inherited from [SK8-ENGINE/skate-3-rust-engine](https://github.com/SK8-ENGINE/skate-3-rust-engine), whose project is based on Skate 3 reverse-engineering research | The converter reads an extracted Skate 3 Xbox 360 `default.xex` and its neighbouring `data` folder |
| `third_party/minecraftoss/` | Five crates copied from MinecraftOSS commit `4013a68`, used for the optional Minecraft world mode | The launcher downloads Minecraft 26.3 files from Mojang; the included catalogs were exported by MinecraftOSS's harness |
| `2010-rust-rewrite-mashup` | [chasmlol's](https://github.com/chasmlol/2010-rust-rewrite-mashup) project combined IW4L, Skate and Minecraft work | It inherits the requirements of the relevant mode |

That distinction matters. The project's own authored survival code is not the same thing as the inherited engine/research code around it.

## Reverse engineering is part of the history

The blanket sentence "no decompiled code" was too broad. A more accurate description is:

- The repository does not intentionally ship decompiler output, Ghidra databases, `FUN_...` placeholders, extracted game assets, or a retail executable.
- IW4L was built from scratch in Rust using reverse-engineering research into MW2/IW4 formats and behaviour. Its documentation says this directly.
- `skate/` is inherited from SK8-ENGINE, which describes itself as based on Skate 3 reverse-engineering research. Some inherited comments and constants refer to Xbox 360 TU3 executable addresses and values learned from the executable.
- `third_party/minecraftoss/` is inherited engine source. Its data catalogs are not claimed here to be original authored game design; they were exported by the MinecraftOSS harness for the 26.3 data used by that engine.
- The new survival layer and authored assets are the project's own work, but that does not erase the provenance of the engines it uses.

Do not describe the whole repository as "free of reverse engineering". Say which layer is authored, which is inherited, and whether a file is source, a generated table, an extracted asset, or an unknown binary.

## Unresolved binary provenance

`crates/fx_iw4/data/fx_random_table.bin` is byte-for-byte the same file as the one in upstream IW4L. The repository history identifies it as inherited from IW4L, but neither repository currently documents whether it was authored, generated from public research, or copied from a game. That is an open provenance question, not evidence that it is safe to redistribute.

The same caution applies to any bundled binary or generated catalog whose source is not recorded. Before publishing or merging, ask the upstream author for its origin and permission, or remove/regenerate it from a documented source.

## Licences and notices

The top-level project declares Apache-2.0 for its own code, but that does not automatically relicense inherited code:

- IW4L: Apache-2.0 according to its repository.
- `skate/`: inherited from SK8-ENGINE/skate-3-rust-engine, whose repository states GPL-3.0-only. A GPL project must retain its licence obligations; an Apache-2.0 top-level declaration does not make the vendored code Apache-2.0.
- `third_party/minecraftoss/`: copied at commit `4013a68`, but the vendored snapshot has no licence file or licence declaration. Do not guess its licence. Record the missing licence and get confirmation from MinecraftOSS before distributing those crates.
- `fx_random_table.bin` and the Minecraft catalogs need provenance and licensing notes separate from the Rust source licence.

The project's `NOTICE` should distinguish its own Apache-2.0 code from inherited IW4L, SK8-ENGINE and MinecraftOSS material. If a dependency's licence is unknown, say so plainly and treat it as a release blocker.

## What a player must provide

"Players must supply any games they own" is too vague. The modes are different:

| Mode | Uses bundled authored content? | External files required |
|---|---:|---|
| Default standalone launcher | Yes | None from a commercial game; it is still an unfinished development path |
| IW4L/MW2 import modes | No, they read the selected game tree | A legally owned MW2 installation, selected through the IW4L game-data path |
| Skate converter | No | An extracted Skate 3 Xbox 360 game folder, including `default.xex` and its `data` folder; the converter does not accept an ISO directly |
| Minecraft mode | Partly | Mojang's Minecraft 26.3 client/assets are downloaded on first run; the bundled MinecraftOSS catalogs are generated support data, not the Mojang JAR or assets |

The default launcher path is the planned original game. The other modes are inherited research/integration paths and have their own external-data requirements.

## AI use and project rules

AI wrote a large part of the current tree. IW4L documents that it was written by an LLM. In the checkout reviewed for this guide, the history has 128 commits attributed to Claude and 16 to Codex out of 300 total; those counts will change as development continues. That is process context, not a reason to hide the human and upstream research that made the inherited parts possible.

The repository's `AGENTS.md` and `docs/AUTONOMY.md` describe how that project operates: issue-sized work, agent coordination, verification boundaries and standing authorization. They are project-specific operating rules, not a universal recommendation that every user should let an agent commit without asking. A different project may reasonably require approval before edits or commits.

The README, `TODO.md`, and some development documents such as `docs/RUST-MAPS.md` and `docs/BUILDINGS.md` are written partly or entirely in Dutch. That is a documentation-language detail, not a property of player-facing game text; the project rules require in-game text to remain English.

## Status and evidence

The project is unfinished. It records code-level, headless, graphical and release-readiness checks separately. Some survival, inventory, building, combat, skate and save/load paths have focused checks; the complete user journey, hardware-controller feel, audio playback and release packaging are not thereby proven.

That separation is the useful lesson for an AI-assisted rewrite: a passing compiler check does not prove a playable game, and a successful feature probe does not prove that every inherited dependency is publishable.

## Credits

- [IW4L](https://github.com/vladtrc/iw4L) by vladtrc: the from-scratch IW4/MW2 Rust runtime and research foundation.
- [2010 Rust Rewrite Mashup](https://github.com/chasmlol/2010-rust-rewrite-mashup) by chasmlol: the project that combined the IW4L, Skate and Minecraft directions.
- [SK8-ENGINE/skate-3-rust-engine](https://github.com/SK8-ENGINE/skate-3-rust-engine): inherited Skate 3 engine crates and reverse-engineering research.
- MinecraftOSS: inherited Rust engine crates and the harness-exported Minecraft 26.3 catalogs.

The project is unofficial and unaffiliated with the owners of MW2, Skate 3, Minecraft or their trademarks. A disclaimer does not by itself establish permission to use their logos: the README banner uses the Rust, MW2 and Skate 3 logos, so its clearance is unresolved. For a redistributable project, replace it with original artwork and plain text names unless the logo owners' terms clearly permit the use.

---

<sub>[Spot a mistake? [Edit this page on GitHub](https://github.com/trevaintdead/ai-game-modding-guides/edit/main/guides/12-worked-example-rust-rewrite.md).](https://github.com/trevaintdead/ai-game-modding-guides/issues/new) · Part of [AI Game Modding Guides](https://github.com/trevaintdead/ai-game-modding-guides)</sub>
