# 1. Choosing and Setting Up an AI Agent

## Agent vs. chat website

Most people get stuck on this one.

- **A chat website** (claude.ai in the browser, ChatGPT in the browser) only sees what you paste or upload. It can't open your game folders, so you hit "file too large" errors and end up copy-pasting code by hand.
- **An agent** runs on your PC. It reads your game folders, creates and edits files, runs builds, and reads logs. Nothing to upload.

You want an agent. If you're using the Claude desktop app, look for the Code mode. Members say it's in the top left and easy to miss. Check the provider's current docs for exact steps.

## Which agent

Members mostly use these. Pick one and stick with it while you learn.

| Tool | Notes from the community |
|------|--------------------------|
| **Claude Code** | The most mentioned. Works from the desktop app, a terminal, or a VS Code extension |
| **Codex** | Also widely used, including to run Ghidra-based decompiling |
| **OpenCode** | Works with many models, including free ones. Members asked which free model is best and nobody answered yet |
| **VS Code + Roo Code + OpenRouter** | A pay-per-use route. One member uses it with a DeepSeek model because a Claude plan is too expensive for them |

### A note on "MCP"

Several people confused these. **Claude Code and Codex are agents.** MCP (Model Context Protocol) is a standard for plugging extra tools into an agent.

You do **not** need any MCP server to start. None of the example projects list one as a requirement.

If you go on to reverse engineer anything, that's when one becomes worth having. Ghidra and IDA both ship MCP servers, which means the agent can decompile and rename functions itself instead of you pasting disassembly into a chat. The official [Hex-Rays IDA MCP](https://github.com/HexRaysSA/ida-mcp) installs with one command, and [ghidra-mcp](https://github.com/bethington/ghidra-mcp) does the same for the free option.

### What the agent is

Almost all of these are a terminal program or a VS Code extension. The agent runs on your machine with your user account's file access, so what protects you depends on how you've configured it.

Claude Code asks before it acts, shows file edits as diffs for you to approve, and has a built-in sandbox you switch on with `/sandbox`. Codex has its own permission and sandbox settings. Protection drops when people switch to full-access modes, which members here describe doing. That is why the safety section at the bottom of this guide matters: the defaults help, and the failure mode is turning them off.

None of the agents will touch DRM or anti-cheat on their own, and online-only games are a no-go for all of them. If an agent refuses something, read the reason before assuming it's a limitation on what it can do.

You do not need an IDE, but one experienced member recommends VS Code so you get proper file views and diffs. The agent creates your files and runs your builds either way, so you never copy-paste code into folders by hand.

## Which model

Models change quickly, so check what's current. What members report:

- Most people doing this pair a top-tier Claude model with Claude Code. Several mention Claude Opus 5.5.
- Others use OpenAI's models through Codex.
- Cheaper models work for smaller tasks, but members say they need more babysitting (see the handoff trick in [guide 4](04-prompting-and-workflow.md)).
- **Local models** (running on your own GPU): one experienced member said they don't work well for this. If you have a 12 GB GPU and are wondering, this is the answer so far. If you've made it work, please add a write-up.
- Your hardware (a 5090, for example) doesn't matter for cloud models. The AI runs on the provider's servers.

## Cost and usage limits

Plans change often, so check the provider's current page. **[Guide 11](11-models-and-cost.md) has our actual recommendations**, including which model to use at each budget. The short version: OpenCode Go at about $10 is the best value, Claude Pro at $20 is the best single subscription, and upgrading goes $20 then $100 then $200.

What members report:

- Paid plans have a short reset window (about 5 hours) and a weekly limit.
- One member gets about 3-4 hours of constant use on the top Claude model out of each 5-hour window.
- Another said the $20 tier was "more than enough" for a from-scratch basketball game.
- **Free tiers:** nobody has confirmed whether you can do a real project on one. Expect to hit limits fast. A pay-per-use API key (OpenRouter and similar) is the other option.
- Long sessions use more of your limit, because the whole conversation is carried along. Start a fresh chat now and then with a short handoff note. See [guide 4](04-prompting-and-workflow.md).

## Setting up safely

Agents can read and delete files, and many members run them with broad access. That's convenient and risky at the same time. A few habits cut the risk:

1. **Make one folder for the project** and run the agent inside it.
2. **Use Git from day one.** Commit after each working step so you can undo mistakes.
3. **Back up your game saves** before testing.
4. **Be careful with "full access" modes.** Some members run agents with full PC access. It's more convenient, but a mistake can hit files you care about. A separate Windows user account, a virtual machine, or a container (one member mentioned Podman) limits the damage.
5. **Keep passwords and API keys out of files the agent can read**, and out of your repo.
6. **If the agent keeps failing to get access** (a common Codex complaint), read that tool's docs on permission and sandbox settings and grant it access to your project folder and game folders only.
7. **Write down the rules instead of trusting your memory.** A rules file in your project means the agent follows them in every session, including the ones you forget. See [`templates/AGENTS-starter.md`](../templates/AGENTS-starter.md).

## Helpful extras (optional)

- **VS Code** (or another editor) with your agent's extension. One experienced member recommends this so you get proper versioning and file views. It isn't required.
- **Git and a GitHub account.** You'll need these to share your project.
- **[universal-modder](https://github.com/rehan-remade/universal-modder):** an open-source set of ten agent skills plus a CLI, covering game recon, reverse engineering, asset generation, in-game testing, publishing, and a shared knowledge base of field notes. Install it with `npx skills add https://github.com/rehan-remade/universal-modder`, or clone the repo and start your agent inside it. Works with Claude Code, Codex, Cursor, Gemini CLI, Copilot and OpenCode. Its art tools need a separate fal API key, and it needs Python 3.10+ and ffmpeg. It limits itself to single-player or offline games you own and won't touch anti-cheat.

---

<sub>[Spot a mistake? [Edit this page on GitHub](https://github.com/trevaintdead/ai-game-modding-guides/edit/main/guides/01-choose-and-set-up-an-ai-agent.md).](https://github.com/trevaintdead/ai-game-modding-guides/edit/main/guides/01-choose-and-set-up-an-ai-agent.md) &middot; [Open an issue](https://github.com/trevaintdead/ai-game-modding-guides/issues/new) &middot; Part of [AI Game Modding Guides](https://github.com/trevaintdead/ai-game-modding-guides)</sub>
