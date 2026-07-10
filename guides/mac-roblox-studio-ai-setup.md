# Mac Setup: Claude Code & OpenAI Codex → Roblox Studio (MCP)

A follow-along guide for connecting AI coding agents (Claude Code and OpenAI Codex CLI) to Roblox Studio's built-in MCP server on macOS, based on Crusherfire's video ["10x Your Roblox Studio Productivity – Claude Code & OpenAI Codex Integration Guide"](https://www.youtube.com/watch?v=6kcWSMJFaS4) and the [official Roblox MCP docs](https://create.roblox.com/docs/studio/mcp).

Once connected, the AI can explore your game's data model, write and edit scripts, run Luau code directly in Studio, insert models, and help test your game — all from a terminal chat. Section 6 covers turning on autonomous ("auto") mode so it works without approval prompts.

**Estimated time:** 15–30 minutes.

---

## 1. Prerequisites

### Roblox Studio

Install (or update to) the latest Roblox Studio from [create.roblox.com](https://create.roblox.com/). The MCP server is built into recent versions of Studio — no plugins or extra servers needed.

### Homebrew and Node.js

Open **Terminal** (Cmd+Space → "Terminal") and check whether you have Homebrew:

```bash
brew --version
```

If that errors, install Homebrew first:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then install Node.js (needed for the npm installs below):

```bash
brew install node
```

### Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Then start it once and log in with your Anthropic account:

```bash
claude
```

### OpenAI Codex CLI (optional)

If you also want Codex, as shown in the video:

```bash
npm install -g @openai/codex
```

Then start it once and log in with your OpenAI/ChatGPT account:

```bash
codex
```

---

## 2. Enable the MCP server in Roblox Studio

1. Open Roblox Studio and open a place (a Baseplate is fine for testing).
2. Open the **Assistant** panel.
3. Click the **…** menu → **Manage MCP Servers**.
4. Toggle **"Enable Studio as MCP server"** on.

The panel will now show connection options and per-client instructions. A **green indicator** appears next to each client once it connects successfully.

---

## 3. Connect Claude Code / Codex to Studio

### Option A: Quick Connect (recommended)

Quick Connect supports Claude Code, Claude Desktop, Codex CLI, Cursor, Gemini CLI, VS Code, and Antigravity.

1. In Studio: **Assistant Settings → MCP Servers**.
2. Expand the **Quick connect** dropdown — it lists supported clients it found on your Mac.
3. Toggle on **Claude Code** (and **Codex CLI** if you installed it).

> If a client you installed doesn't appear in the list, quit and reopen Roblox Studio — it scans for installed clients at startup.

### Option B: Manual configuration (fallback)

If Quick Connect doesn't work, register the server manually. The Studio MCP binary on macOS lives at:

```
/Applications/RobloxStudio.app/Contents/MacOS/StudioMCP
```

**For Claude Code**, run in Terminal:

```bash
claude mcp add Roblox_Studio -- /Applications/RobloxStudio.app/Contents/MacOS/StudioMCP
```

**Or edit the MCP config JSON directly** (e.g. `~/.claude.json` for Claude Code, or `~/.codex/config.toml` equivalents for Codex — check each client's docs). The JSON form is:

```json
{
  "mcpServers": {
    "Roblox_Studio": {
      "command": "/Applications/RobloxStudio.app/Contents/MacOS/StudioMCP"
    }
  }
}
```

> Watch the JSON syntax — a missing comma will silently prevent the config from loading.

---

## 4. Verify it works

1. With Studio open, start a new terminal session and run `claude` (from any folder — ideally your game project folder).
2. Type `/mcp` in Claude Code — you should see **Roblox_Studio** listed as connected.
3. In Studio's **Manage MCP Servers** panel, confirm the **green indicator** next to your client.
4. Smoke test — ask Claude Code:

   > "Using the Roblox Studio tools, list the children of Workspace."

If it returns your place's instances, you're fully connected. 🎉

---

## 5. First things to try

- **Generate a script:** "Create a Script in ServerScriptService that spawns a part every 5 seconds at a random position."
- **Run Luau live:** "Run Luau code to print all players currently in the game."
- **Explore your game:** "Find every Instance in my place with 'Coin' in its name and list their properties."
- **Build things:** "Insert a spawn location and build a simple obby with 10 platforms."

Changes the AI makes go through Studio's **ChangeHistoryService**, so **Cmd+Z undoes them** like any manual edit.

---

## 6. Coding on auto (autonomous mode)

Three ways to let Claude Code work without approving every action, from safest to most hands-off:

1. **Allowlist just the Studio tools** — type `/permissions` in Claude Code and add `mcp__Roblox_Studio__*` to the allow list. Studio actions never prompt, but riskier things (shell commands, file deletes) still do. Good middle ground.
2. **Auto-accept mode (recommended)** — press **Shift+Tab** in Claude Code to cycle permission modes until it shows `auto-accept edits on`. Claude writes/edits files and drives Studio without stopping to ask.
3. **Full-auto ("yolo") mode** — start with `claude --dangerously-skip-permissions`. Zero prompts at all.

> **Before a long autonomous run:** save/publish your place (or commit it if you use Rojo). Cmd+Z undoes AI changes, but a save point is cheaper than untangling a big session.

---

## 7. Troubleshooting

| Problem | Fix |
| --- | --- |
| Client not in Quick Connect list | Install the client, then quit and reopen Studio |
| No green indicator | Restart both Studio and the client (exit `claude`, reopen Studio, run `claude` again) |
| Manual config not loading | Verify the binary path exists (`ls /Applications/RobloxStudio.app/Contents/MacOS/StudioMCP`) and check JSON for syntax errors |
| `claude` / `codex` command not found | Re-run the npm install; make sure `npm bin -g` is on your PATH (Homebrew's Node usually handles this) |

---

## ⚠️ Security note

From the official Roblox docs: **MCP clients can read and modify content in your open Roblox places.** Only connect clients you trust, and consider keeping a backup/committed copy of your place before letting an AI make large changes.

---

## Sources

- Video: [10x Your Roblox Studio Productivity – Claude Code & OpenAI Codex Integration Guide (Crusherfire)](https://www.youtube.com/watch?v=6kcWSMJFaS4)
- Official docs: [Connect to the Roblox Studio MCP server](https://create.roblox.com/docs/studio/mcp)
- Claude Code docs: [code.claude.com/docs](https://code.claude.com/docs)
