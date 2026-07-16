# MCP Workshop: Build Servers for AI Agents

An interactive, self-paced course teaching you how to build Model Context Protocol (MCP) servers so AI agents (Claude, ChatGPT, Cursor) can use your product.

**Walk in curious about MCP. Walk out with two published servers and the skills to build the next one.**

---

## 🎯 What You'll Learn

- **MCP is the universal interface for AI agents** — how it works, why agents need it
- **Tool descriptions are UX for agents** — how to write names and descriptions so agents pick the right tool
- **Build an MCP server from an API** — take GitHub's API, scaffold it, deploy it
- **Build an MCP server from scratch** — start with just an idea, use Replit + Claude to build it
- **Test your servers with evals** — generate test cases, run them against real models, iterate until agents always pick your tool
- **Deploy to production** — handle authentication, security, and token costs

---

## 📚 Course Structure

This is a **~6–8 hour, self-paced course** broken into short lessons (30–60 minutes each). Each module includes:
- **Video walkthrough** (text + screen recording transcripts)
- **Interactive lesson** (concepts + examples + code)
- **Hands-on exercise** or **quiz** (validate your learning)
- **Code templates** (copy-paste ready)

### Week 1: Core Fundamentals + 2 Projects
| Module | Title | Duration | What You Build |
|--------|-------|----------|---|
| [1. Fundamentals](#module-1-mcp-fundamentals) | What is MCP and why agents need it | 45 min | Understanding |
| [2. Tool Design](#module-2-tool-design-as-ux) | Naming & describing tools so agents pick right | 60 min | Your first tool spec |
| [3. Build from API](#module-3-build-from-api-zuplo) | GitHub API → MCP server with Zuplo | 90 min | **Working GitHub MCP** ✅ |
| [4. Build from Scratch](#module-4-build-from-scratch-replit) | Idea → MCP server with Replit | 90 min | **Working Weather MCP** ✅ |
| [Capstone](#capstone-project) | Build your own MCP server | — | **Your own product's MCP** |

### Weeks 2–4: Advanced Topics (Coming Soon)
- **Module 5**: Testing & Evals (MCPJam, running evals, establishing baselines)
- **Module 6**: Production Challenges (OAuth2, security, token costs)
- **Module 7**: Publishing to Claude & ChatGPT Directories
- **Module 8**: Capstone Project (full lifecycle)

---

## 🚀 Getting Started

### Prerequisites
- **Node.js 18+** installed locally
- Familiarity with **TypeScript** (helpful but not required — code is straightforward)
- A **GitHub account** (for Module 3)
- **Text editor** (VS Code recommended)

### How to Use This Course
1. Start with **Module 1** to understand MCP concepts
2. Do **Module 2** to learn tool design patterns
3. Follow **Module 3** step-by-step to build your first MCP server
4. Build **Module 4** from a template, then customize it
5. Submit your **Capstone project** (optional, for feedback)

Each module is standalone, but they build on each other. **No skipping around.**

---

## 📖 Modules

### Module 1: MCP Fundamentals
**Duration**: 45 min | **Type**: Concept + Quiz

[Open Module 1](modules/01-fundamentals/README.md)

**What you'll learn:**
- What is Model Context Protocol?
- How do agents discover and use MCP servers?
- Why naming and describing tools matters
- Real examples of agent picking the right tool (and wrong tool)

**Quiz**: 5 multiple-choice questions to validate your understanding

---

### Module 2: Tool Design as UX
**Duration**: 60 min | **Type**: Concept + Exercise

[Open Module 2](modules/02-tool-design/README.md)

**What you'll learn:**
- MCP primitives: resources, tools, prompts
- The tool lifecycle: discovery → description → invocation
- Writing tool names and descriptions that agents understand
- When to create a tool vs. when NOT to
- Common mistakes and how to fix them

**Exercise**: Rename 5 poorly-described tools, explain your choices

---

### Module 3: Build from API (Zuplo)
**Duration**: 90 min | **Type**: Hands-on Lab

[Open Module 3](modules/03-build-from-api/README.md)

**What you'll build:**
A working MCP server that exposes GitHub's API to Claude and other agents.

**Steps:**
1. Set up Zuplo (3 min)
2. Import GitHub's OpenAPI spec (5 min)
3. Generate MCP server config (5 min)
4. Connect to Claude (10 min)
5. Test live with Claude (15 min)
6. Deploy (10 min)

**Outcome**: Your GitHub MCP is live and Claude can list issues, create PRs, etc.

---

### Module 4: Build from Scratch (Replit)
**Duration**: 90 min | **Type**: Hands-on Lab

[Open Module 4](modules/04-build-from-scratch/README.md)

**What you'll build:**
A weather MCP server from scratch using Replit. No complex setup—just code.

**Steps:**
1. Clone starter template (1 min)
2. Implement weather tools (20 min, template provided)
3. Test with MCP Debugger (15 min)
4. Connect to Claude (10 min)
5. Customize for your use case (20 min)

**Outcome**: Your own MCP server, running locally, talking to Claude

---

### Capstone Project
**Type**: Open-ended

[Open Capstone](modules/capstone/README.md)

**The Challenge**: Pick a product or API you use daily, and build an MCP server for it.

**Rubric**:
- ✅ Server runs locally without errors
- ✅ At least 2 tools, well-named and described
- ✅ Works with Claude (tested)
- ✅ Code is clear and documented

**Submission**: Push your code to a repo, share the link. Get feedback.

---

## 🛠 Resources

### MCP Basics
Quick reference docs:
- [MCP Protocol Overview](resources/mcp-basics/protocol-overview.md)
- [Tool Naming Guide](resources/mcp-basics/naming-guide.md)
- [MCP SDK Reference](resources/mcp-basics/sdk-reference.md)

### Code Templates
Ready-to-use starter code:
- [Minimal MCP Server](resources/code-templates/minimal-server.mjs)
- [GitHub API MCP](resources/code-templates/github-mcp.mjs)
- [Weather API MCP](resources/code-templates/weather-mcp.mjs)

### Troubleshooting
Common errors and fixes:
- [FAQ & Troubleshooting](resources/troubleshooting-faq.md)
- [How to debug MCP servers](resources/debugging-guide.md)

---

## ❓ FAQ

**Q: Do I need to know TypeScript?**  
A: Helpful but not required. All code examples are straightforward and well-commented. If you can read JavaScript, you'll be fine.

**Q: Can I do this on Windows / Mac / Linux?**  
A: Yes to all. Node.js runs everywhere.

**Q: How long does each module take?**  
A: 30–60 min for concepts, 90 min for hands-on labs. Go at your own pace.

**Q: What if I get stuck?**  
A: Check [troubleshooting-faq.md](resources/troubleshooting-faq.md). If not answered, open an issue on GitHub.

**Q: Can I publish my MCP after this course?**  
A: Yes! Module 7 (coming Week 3) covers publishing to Claude & ChatGPT directories. For now, local testing is the goal.

---

## 📋 Checklist: Ready to Start?

- [ ] Node.js 18+ installed (`node -v` to check)
- [ ] Text editor open (VS Code recommended)
- [ ] GitHub account ready
- [ ] ~45 min of uninterrupted time
- [ ] Curiosity about how AI agents work 🧠

**[→ Start with Module 1](modules/01-fundamentals/README.md)**

---

## 🤝 Contributing

Found a typo? Unclear step? Have a better explanation?  
[See CONTRIBUTING.md](.github/CONTRIBUTING.md) to submit feedback or improvements.

---

## 📄 License

This course is open source. Build freely, share your MCP servers.

---

**Last updated**: July 16, 2024  
**Status**: Week 1 live (Modules 1–4 + Capstone)
