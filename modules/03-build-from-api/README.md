# Module 3: Build from API (Zuplo)

**Duration**: 90 minutes  
**Type**: Hands-on Lab  
**Outcome**: Build a working GitHub MCP server and test it with Claude

---

## 🎯 What You'll Build

By the end of this module, you'll have:
- ✅ A Zuplo API gateway configured with GitHub's OpenAPI spec
- ✅ An MCP server running on your machine
- ✅ Claude connected to your MCP server
- ✅ A working tool that Claude can use to list GitHub issues

---

## 📚 What You'll Learn

1. **Zuplo basics** — API gateway that imports OpenAPI specs
2. **OpenAPI specs** — How to use them to scaffold MCP servers
3. **MCP server setup** — Minimal Node.js server that speaks MCP protocol
4. **Claude integration** — How to connect Claude to your local MCP server
5. **Testing workflows** — How to verify your MCP server works

---

## 🚀 Prerequisites

Before starting, make sure you have:

- [ ] **Node.js 18+** (`node -v` to check)
- [ ] **npm or yarn** (`npm -v`)
- [ ] **GitHub account** (for API access)
- [ ] **GitHub Personal Access Token** (create one [here](https://github.com/settings/tokens))
- [ ] **VS Code or text editor** (recommended: VS Code)
- [ ] **Claude (web or desktop)** to test your MCP server

**Estimated setup time**: 5 minutes

---

## 📖 How This Module Works

This module has two parts:

### Part 1: Understand the Flow (10 min read)
[→ Read lesson.md](lesson.md)

You'll understand:
- How Zuplo works (API gateway that scaffolds MCP servers)
- How to configure GitHub's API for MCP
- How to connect Claude to your local MCP server

### Part 2: Build and Test (60 min hands-on)
[→ Follow testing-guide.md](testing-guide.md)

Step-by-step instructions:
1. Clone the starter repo
2. Install dependencies
3. Set up your GitHub token
4. Start the MCP server
5. Connect Claude to it
6. Test with live Claude requests

### Part 3: Deploy (10 min)
[→ Read deployment.md](deployment.md)

Optional: Deploy your MCP server so it's always available (for production use).

---

## 📋 Quick Start Checklist

- [ ] Read [lesson.md](lesson.md) (understand the flow)
- [ ] Clone [starter repo](https://github.com/adarsh22pm/mcp-github-starter)
- [ ] Create GitHub Personal Access Token
- [ ] Run `npm install && npm start`
- [ ] Connect Claude to localhost:3000
- [ ] Ask Claude: "List my open GitHub issues"
- [ ] Test in action—verify Claude gets results

---

## 🛠 Code Templates

All code is provided:
- **MCP server starter**: `github-mcp-server.mjs` (ready to run)
- **Zuplo config**: `zuplo.yaml` (imports GitHub OpenAPI spec)
- **Environment template**: `.env.example` (fill in your GitHub token)

No complex coding—just configuration and testing.

---

## 🎯 Checkpoint

After completing Module 3, you should:

- [ ] Understand how Zuplo scaffolds MCP servers from OpenAPI specs
- [ ] Have a working MCP server running locally
- [ ] Know how to connect Claude to a local MCP server
- [ ] Have tested your MCP with real Claude prompts
- [ ] Be able to explain the request flow (user → Claude → MCP → GitHub API → MCP → Claude → user)

---

## ⚠️ Troubleshooting

Common issues and fixes:

- **"Port 3000 already in use"** → Change `PORT=3001` in `.env` or kill the process using port 3000
- **"GitHub token invalid"** → Check that your token has `repo` and `public_repo` scopes
- **"Claude can't connect"** → Verify the MCP server is running (`curl localhost:3000/health`)
- **"Permission denied on /tmp"** → MCP stores temp files; ensure write permissions

Full troubleshooting: [resources/troubleshooting-faq.md](../../resources/troubleshooting-faq.md)

---

## 📖 Next Steps

After you've built and tested your GitHub MCP:

**[→ Continue to Module 4: Build from Scratch](../04-build-from-scratch/README.md)**

Module 4 teaches you to build an MCP server **without an existing API**—just from an idea. You'll use Replit and Claude to scaffold it.

---

## 💡 Key Concepts

1. **Zuplo** — API gateway that can scaffold MCP servers from OpenAPI specs
2. **OpenAPI spec** — Machine-readable description of an API (GitHub provides this)
3. **MCP server** — Standalone Node.js app that exposes tools to Claude
4. **Local testing** — MCP server runs on localhost; Claude connects directly (no internet upload)
5. **Tool invocation** — Claude reads tool descriptions and calls the right one

---

## 📚 Resources

- [OpenAPI Spec Docs](https://spec.openapis.org/)
- [GitHub REST API](https://docs.github.com/en/rest)
- [MCP Protocol Spec](https://modelcontextprotocol.io/specification)
- [Zuplo Docs](https://zuplo.com/)

---

## 🚨 Important: Local Testing & Privacy

Your MCP server runs on `localhost:3000`. Claude connects directly—your data never leaves your machine.

**What Claude can see**: Only what you tell it (issue titles, PR descriptions, etc.).  
**What Claude cannot see**: Your environment variables, local files outside MCP scope, or other projects.

This is safe for local development and testing.

---

**Ready?** [Start with the lesson](lesson.md)
