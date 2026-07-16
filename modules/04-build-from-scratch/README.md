# Module 4: Build from Scratch (Replit)

**Duration**: 90 minutes  
**Type**: Hands-on Lab  
**Outcome**: Build a weather MCP server from scratch without an existing API

---

## 🎯 What You'll Build

By the end of this module, you'll have:
- ✅ A complete MCP server written from scratch (no scaffolding)
- ✅ Two working tools: `get_weather` and `get_forecast`
- ✅ Running on Replit (no local setup needed)
- ✅ Tested and connected to Claude

---

## 📚 What You'll Learn

1. **MCP server anatomy** — What every MCP server needs
2. **Tool implementation** — Writing tools from scratch
3. **Replit development** — Cloud-based rapid development
4. **Testing without scaffolding** — Manual testing with MCP Debugger
5. **Customization** — How to adapt this template for your own idea

---

## 🚀 Prerequisites

Before starting, have:

- [ ] **GitHub account** (to fork starter repo)
- [ ] **Replit account** (free at https://replit.com)
- [ ] **Claude** (web or desktop)
- [ ] ~90 minutes of uninterrupted time

**No local Node.js setup needed—Replit handles it.**

---

## 📖 How This Module Works

### Part 1: Understand the Approach (15 min)
[→ Read lesson.md](lesson.md)

Learn:
- Why building from scratch is different
- MCP server anatomy (3 essential parts)
- How to design tools before coding
- Replit workflow (edit → run → test)

### Part 2: Build and Test (70 min)
[→ Follow starter guide](building-guide.md)

Step-by-step:
1. Fork starter repo to Replit
2. Understand the code structure
3. Implement weather tools
4. Test with MCP Debugger
5. Connect Claude and verify

### Part 3: Customize (5 min)
Adapt the weather server for your own idea.

---

## 🛠 Code Templates

All code provided in starter repo:
- **server.mjs** — MCP server skeleton (you fill in tool implementations)
- **tools.mjs** — Tool definitions (already written)
- **weather-api.mjs** — API wrapper (to external weather service)

This is a real, working server. You're building on it, not from zero.

---

## 💡 Key Difference: Module 3 vs Module 4

| Aspect | Module 3 (Zuplo) | Module 4 (Replit) |
|--------|---|---|
| **Starting point** | Existing API (GitHub) | Just an idea |
| **Tool generation** | Scaffolded from OpenAPI | Written manually |
| **Complexity** | Simpler (config-driven) | More educational (you write code) |
| **Learning** | How to use existing APIs | How to design from scratch |
| **Platform** | Local or deployed | Replit (cloud) |

Module 3 is faster. Module 4 teaches you more.

---

## 🎯 Checkpoint

After Module 4, you should:

- [ ] Understand MCP server structure (3 components)
- [ ] Know how to write tool definitions
- [ ] Know how to implement tool logic
- [ ] Be comfortable with Replit workflow
- [ ] Have built a server from scratch (not scaffolded)
- [ ] Know how to customize for your own idea

---

## 📖 Next Steps

After you've built the weather MCP:

**[→ Continue to Capstone Project](../capstone/README.md)**

You've now built two MCP servers (one from API, one from scratch). The capstone is your chance to build a **third one** using either approach, for a product or idea you care about.

---

## ⚠️ Quick Troubleshooting

- **"Replit fork failed"** → Check GitHub connection; try forking manually
- **"Server won't start"** → Check for syntax errors; use Replit's console
- **"Claude can't connect to Replit"** → Replit servers are public-facing; they should work. Verify URL format
- **"Weather API not responding"** → API might be rate-limited; check documentation

Full troubleshooting: [resources/troubleshooting-faq.md](../../resources/troubleshooting-faq.md)

---

## 💡 Key Concepts

1. **MCP server anatomy** — Tool definitions + implementations + protocol handler
2. **Replit** — Cloud IDE; no local setup needed
3. **Tool design** — You choose what tools to expose
4. **Customization** — Easy to adapt for other ideas
5. **Testing** — MCP Debugger validates tool definitions

---

**Ready?** [Read the lesson](lesson.md)
