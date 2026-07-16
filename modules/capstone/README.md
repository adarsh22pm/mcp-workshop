# Capstone Project: Build Your Own MCP Server

**Duration**: Open-ended  
**Type**: Project  
**Outcome**: A production-ready MCP server for a product or idea you care about

---

## 🎯 The Challenge

Pick a product, API, or idea you use daily. Build an MCP server for it.

**Examples:**
- Your favorite API (Stripe, Slack, Notion, Gmail, Linear, Jira)
- A internal tool your company uses
- A SaaS platform you manage
- A local service (restaurant API, weather data, sports stats)

---

## 📋 Requirements

Your MCP server must:

### 1. **Runs Locally or on Replit**
- [ ] Server starts without errors
- [ ] Stays running for at least 10 minutes
- [ ] Can be accessed by Claude

### 2. **Has At Least 2 Tools**
- [ ] Tool 1: Read data (e.g., `get_user`, `list_items`)
- [ ] Tool 2: Action (create, update, delete, or complex query)
- [ ] Both tools have clear names and descriptions
- [ ] Both tools work when Claude calls them

### 3. **Integrates with Claude**
- [ ] Claude can discover your tools (sees them in tool list)
- [ ] Claude can call your tools without errors
- [ ] Claude gets meaningful results back

### 4. **Code is Documented**
- [ ] README.md explains what your server does
- [ ] Each tool has a clear one-line comment explaining it
- [ ] `.env.example` shows what credentials are needed

### 5. **Follows Best Practices**
- [ ] Tool descriptions are useful (not vague)
- [ ] Error handling exists (doesn't crash on bad input)
- [ ] No secrets in code (uses `.env` for tokens)

---

## 🏗 Choose Your Approach

### Approach A: From an API (Like Module 3)
Use Zuplo or scaffolding:
- Pros: Faster, less coding
- Cons: Requires an existing API with good docs

**Best for**: Stripe, GitHub, Slack, Notion, OpenWeather

### Approach B: From Scratch (Like Module 4)
Write your own server:
- Pros: Full control, more learning
- Cons: More coding required

**Best for**: Internal tools, custom services, novel ideas

### Approach C: Hybrid
Start with scaffolding, then customize:
- Pros: Balance of speed and control
- Cons: Requires both skills

**Best for**: Most real-world projects

---

## 📝 Submission Requirements

Before you can say "done," submit:

1. **GitHub repository** with:
   - `README.md` (explains what your server does)
   - `server.mjs` or equivalent (your MCP server code)
   - `.env.example` (shows what credentials needed)
   - Working code (no syntax errors)

2. **Evidence of testing**:
   - Screenshots or video of Claude calling your tools
   - Terminal output showing server is running
   - Proof that tools return real data

3. **Optional: Code Review Submission**
   - Share your repo link
   - Instructors will review and give feedback
   - Feedback focuses on: clarity, tool naming, error handling

---

## 📊 Rubric: How Your Project Will Be Judged

| Criterion | Poor | Good | Excellent |
|-----------|------|------|-----------|
| **Runs without errors** | Won't start | Starts but crashes | Runs reliably 10+ min |
| **Tool naming** | Vague (e.g., `get_data`) | Clear (e.g., `list_issues`) | Clear + descriptive |
| **Tool descriptions** | 1 word | 1-2 sentences | Includes when to use |
| **Tool functionality** | Returns errors | Returns some data | Returns complete, useful data |
| **Claude integration** | Claude can't call tools | Claude calls tool but it fails | Claude calls tool successfully |
| **Documentation** | No README | Incomplete README | Clear README + setup guide |
| **Error handling** | Crashes on bad input | Some error checking | Validates input, graceful errors |

---

## 🎓 Learning Outcomes

By completing the capstone, you'll:

- ✅ Know how to pick an API or service worth exposing
- ✅ Design tools that agents will actually use
- ✅ Implement tools from scratch or scaffold them from APIs
- ✅ Test your MCP server thoroughly
- ✅ Deploy (locally or cloud) for real use
- ✅ Understand the full MCP lifecycle

---

## 💡 Idea Suggestions

**If you're stuck:**

### Easy (Using Existing APIs)
- Weather API (OpenWeatherMap, WeatherAPI)
- Sports stats (ESPN API, sports-reference)
- Quotes (Quotable API)
- Jokes (JokeAPI)
- Crypto prices (CoinGecko API)

### Medium (Requires Auth)
- Stripe (payments)
- Slack (messaging)
- GitHub (already did this—try your own repo!)
- Linear (issue tracking)
- Notion (note-taking)

### Hard (Custom/Internal)
- Your company's internal API
- A microservice you built
- A database you want to query
- A custom service unique to your use case

**Pro tip**: Pick something you use daily. That makes it more fun and useful.

---

## 🚀 Getting Started

1. **Pick your idea** (5 min)
   - What product/API do you want to expose to Claude?
   - Why would Claude calling this be useful?

2. **Design your tools** (10 min)
   - Tool 1: What should it do?
   - Tool 2: What should it do?
   - Write names and descriptions (use Module 2 patterns)

3. **Choose your approach** (5 min)
   - API existing? → Approach A (scaffolding)
   - Building from scratch? → Approach B (custom)

4. **Implement** (30-120 min depending on complexity)
   - Follow Module 3 or Module 4 as a template
   - Adapt the code for your service

5. **Test** (20 min)
   - Connect Claude
   - Call each tool multiple times
   - Test error cases

6. **Submit** (5 min)
   - Push to GitHub
   - Fill out submission form

---

## 📋 Checklist: Before You Submit

- [ ] Server runs without errors (`npm start` works)
- [ ] No secrets in code (all secrets in `.env`)
- [ ] README.md explains what your MCP does
- [ ] Tool names follow verb + resource pattern
- [ ] Tool descriptions include "use when" guidance
- [ ] Claude can discover your tools
- [ ] Claude can call each tool successfully
- [ ] Error handling exists (doesn't crash on bad input)
- [ ] `.env.example` shows what credentials are needed
- [ ] Code is on GitHub and repo is shareable

---

## 🎁 What Great Projects Look Like

### Example 1: Stripe MCP
```
Tool 1: get_payment_intent
Description: Retrieve payment intent details including amount, status, and metadata. Use this to check if a payment is processing or completed.

Tool 2: create_invoice
Description: Create a new Stripe invoice for a customer. Provide customer ID, items, and due date. Use this to generate invoices.
```

**Why good**: Clear tools, one responsibility each, real business value.

### Example 2: Notion MCP
```
Tool 1: query_database
Description: Query a Notion database by property filters. Returns page titles and metadata. Use this to search for information in your Notion workspace.

Tool 2: create_page
Description: Create a new Notion page in a database. Provide title and properties. Use this to add new entries (tasks, notes, etc.).
```

**Why good**: Simple, focused, useful to knowledge workers.

### Example 3: Internal Company Tool
```
Tool 1: get_deployment_status
Description: Get the status of your latest deployment (running, failed, pending). Use this to check if a deploy succeeded.

Tool 2: rollback_deployment
Description: Rollback to the previous deployment version (IRREVERSIBLE). Use only if current version is broken. Requires confirmation.
```

**Why good**: Specifically tailored to company workflow, clear risk warnings.

---

## ❓ FAQ

**Q: Can I use a tool/library?**  
A: Yes. Use `fetch`, `axios`, SDKs, anything you need.

**Q: Do I have to build something original?**  
A: No. Building an MCP for an existing service (GitHub, Stripe, Slack) counts. The value is in exposing it to Claude.

**Q: Can my server call other servers?**  
A: Yes. Your MCP server can call any API. That's often the best design.

**Q: What if my API has rate limits?**  
A: Handle gracefully. Return error message: "Rate limit exceeded. Try again in 1 hour."

**Q: Can I deploy this somewhere?**  
A: Yes! Replit, Heroku, Fly.io, or wherever Node.js runs. Deployed servers can be shared and used by others.

---

## 📚 Resources

- **Module 3**: How to scaffold from existing APIs
- **Module 4**: How to build from scratch
- [GitHub API Docs](https://docs.github.com/en/rest)
- [MCP Protocol Spec](https://modelcontextprotocol.io/specification)
- [Node.js Best Practices](https://nodejs.org/en/docs/)

---

## 🤔 Still Stuck?

1. **Can't pick an idea?** → Browse popular APIs on [RapidAPI](https://rapidapi.com)
2. **Don't know how to start coding?** → Follow Module 4 step-by-step
3. **Server not working?** → Check [troubleshooting-faq.md](../../resources/troubleshooting-faq.md)
4. **Need feedback?** → Submit your repo, get code review

---

## 🎉 Completion

**Submitting this capstone means:**
- You've built and tested your own MCP server
- You understand the full lifecycle (design → build → test → deploy)
- You're ready to build more MCP servers for other projects
- You can contribute to the MCP ecosystem

**Congratulations!** You're now an MCP server builder. 🚀

---

## Next Up (After Capstone)

Weeks 2–4 will add:
- **Module 5**: Testing & Evals (how to verify agents pick your tools correctly)
- **Module 6**: Production Challenges (auth, security, costs)
- **Module 7**: Publishing (how to publish to Claude & ChatGPT directories)

For now, focus on building and testing your capstone project.
