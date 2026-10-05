# 3. Rust Rewrites and Ports

A rewrite or port rebuilds a game's engine from scratch, so it runs on its own instead of inside the original. The new engine reads models, textures, maps, and sounds from **your own installed copy** of the game at runtime. The repo contains only your code.

## Examples to study

| Project | What it shows |
|---------|---------------|
| [IW4L](https://github.com/vladtrc/iw4L) | A Call of Duty: Modern Warfare 2 (2009) runtime in Rust and Bevy. Experimental: gameplay is incomplete, and it says so. Reads your own install in place and ships no assets |
| [gang-beasts-rust](https://github.com/muffinmxn/gang-beasts-rust) | Python tools extract your game's data into formats a Rust/Bevy engine loads. A whitelist `.gitignore` keeps extracted files out of the repo |
| [benilla](https://github.com/samwhosung/benilla) | A WoW 1.12.1 client in Rust and Bevy. A big project with hundreds of commits, readers for the game's file formats, and a generated map of the code |
| [2010 Rust Rewrite Mashup](https://github.com/chasmlol/2010-rust-rewrite-mashup) | A rewrite combined with other games |

The good ones share some habits: a clear "what works / what's missing" list, no game files, credits, and an `AGENTS.md` or development log so the AI's work can be followed.

## Why Rust and Bevy?

You don't have to use them. C and C++ work fine. People pick Rust and Bevy because:

- Rust catches memory mistakes before the game runs, so you get fewer random crashes
- it's easy to set up
- Bevy is a free engine that's all code, with no editor to learn
- AI is good at fixing Rust, because the compiler's error messages say what's wrong

Not every project uses Bevy. It is the most common choice in this space, but you'd pick a different one if you wanted to. IW4L also uses wgpu for rendering on top of Bevy, and translates the original game's Direct3D 9 shader bytecode to WGSL.

## Be realistic about size

A rewrite is a big job. IW4L is around 138 commits in and still describes itself as experimental, with missing behaviour, bugs and desyncs. Benilla is described as complete, with hundreds of commits behind it. Start with a goal that fits in a sentence, like "load and show the first level and walk around in it." Grow from there.

## How these projects are usually built

1. **Extract.** Tools (often Python) read the player's own install and convert models, textures, and maps into formats the engine can load. Some projects read the original formats directly at runtime instead.
2. **Engine.** A Rust engine (Bevy is common) plus a physics library draws and simulates the world.
3. **Rebuild the rules.** Movement first, then maps, then weapons and interactions, then everything else.
4. **Compare with the real game.** Play the original next to your build and note differences.
5. **Write down what works and what's missing.**

If you have a working game and want to link a second one to it, you want [guide 2](02-passthrough-mods.md). Much smaller job.

## Do I need to decompile?

**Usually not, and check before you assume you do.** In order of preference:

1. **The file formats are already documented.** Lots of games have community specs, and some have open-source readers already written. If one exists, use it. This is the free path.
2. **The game has a source release.** Some studios shipped their engines or games as source, legally and publicly. Check.
3. **You need the executable's logic.** Only then is decompiling on the table.

Members routinely skip steps 1 and 2 and lose whole evenings to it. Two separate people on the Discord said the same thing: *look for an existing decomp or format project before you start.* Ask the agent to search first. It's good at finding community projects.

### When you do need it

Tools, in the order members mention them:

| Tool | Cost | Notes |
|------|------|-------|
| **Ghidra** | Free, open source | The one people use. Needs a Java runtime, and a processor module for some older consoles |
| **IDA Pro** | Commercial, expensive | The industry standard. Free tier is limited. Ghidra is the default recommendation |
| **Binary Ninja** | Commercial, cheaper than IDA | Worth knowing about, though nobody here has reported using it |

One member asked the agent to decompile a folder "using the correct tools," and it identified the platform and format, installed Ghidra with the right processor module, and ran the process. That's a realistic workflow.

### A realistic prompt

```
I want to understand how [Game] stores its [maps / models / animation data].

Before installing anything: research whether there is existing community
documentation, an open-source library, or a decomp project for this game's
file formats. Tell me what you found first.

If nothing exists and we do need to look at the executable, use Ghidra.
Work in [gitignored folder]. Do not write anything into this repo except
a notes file describing what you learned.
```

### Rules for the output

- **Everything stays on your machine.** Never commit decompiled code, Ghidra databases, or extracted assets. See [guide 6](06-rules-legal-and-publishing.md).
- **Use a whitelist `.gitignore` from day one** so an extracted file can never be committed by accident.
- **Write down what you learned as documentation**, not as code. That documentation is the shareable part. This is how open-source engine reimplementations like [OpenMW](https://github.com/OpenMW/openmw) and [OpenRCT2](https://github.com/OpenRCT2/OpenRCT2) exist.
- **Single-player, offline games you own only.** Leave DRM alone. Leave anti-cheat alone. Don't target anything to get around access controls. See [guide 6](06-rules-legal-and-publishing.md).
- **Don't redistribute the output.** Personal study of a game you own is the scope. Publishing extracted assets or decompiled source is not.
- **Keep your research local.** IW4L used Ghidra to inspect the original binaries and records what it learned in `docs/provenance/`, with the dumps and databases themselves kept out of the repo.

Read [guide 6](06-rules-legal-and-publishing.md) before going down this path. It's not legal advice, but it lists what the community's own tooling refuses to do.

## Step by step

1. Pick a game and a one-sentence goal.
2. Look for existing research, tools, and decomp projects.
3. Open your agent in a new project folder with Rust installed (rustup.rs). On Windows you'll also need the Visual Studio C++ build tools.
4. Send the starter prompt.
5. Build in small steps, playtesting each one.
6. Keep a log of what works and what doesn't. Update the README's "what works / what's missing" list.
7. Share it as a GitHub repo with no game files. See [guide 10](10-posting-your-project.md).

## Starter prompt

```
I want to build a Rust rewrite of [Game] that reads its data from my own installed copy at [path] at runtime. Use [IW4L / gang-beasts-rust / benilla] as a reference for structure: [links].

Rules: never copy game assets or decompiled code into the repo. Use a whitelist .gitignore. Credit anything we learn from and keep licenses.

First, look for existing documentation, file format specs, and decomp projects for this game, and tell me what's out there before you start building.
```

## Passthrough or rewrite?

| | Passthrough | Rewrite |
|--|-------------|---------|
| Goal | Mix two games' gameplay | A standalone engine you control |
| Needs | Both games running together | Only your game files |
| Size | Often smaller | Often much bigger |
| Good first project? | Usually | Only with a small goal |

---

<sub>[Spot a mistake? [Edit this page on GitHub](https://github.com/trevaintdead/ai-game-modding-guides/edit/main/guides/03-rust-rewrites-and-ports.md).](https://github.com/trevaintdead/ai-game-modding-guides/edit/main/guides/03-rust-rewrites-and-ports.md) &middot; [Open an issue](https://github.com/trevaintdead/ai-game-modding-guides/issues/new) &middot; Part of [AI Game Modding Guides](https://github.com/trevaintdead/ai-game-modding-guides)</sub>
