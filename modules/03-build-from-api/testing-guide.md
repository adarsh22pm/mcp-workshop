# Module 3: Hands-On Testing Guide

**Time**: 60 minutes  
**Goal**: Get a working GitHub MCP server running and connected to Claude

---

## Phase 1: Setup (15 minutes)

### Step 1.1: Clone Starter Repository

```bash
git clone https://github.com/adarsh22pm/mcp-github-starter
cd mcp-github-starter
```

Verify the structure:
```bash
ls -la
# You should see: server.mjs, github-client.mjs, tools.mjs, package.json, .env.example
```

### Step 1.2: Install Dependencies

```bash
npm install
```

This installs the MCP SDK and other dependencies.

### Step 1.3: Create GitHub Personal Access Token

1. Go to https://github.com/settings/tokens
2. Click **Generate new token** → **Generate new token (classic)**
3. Name it: `mcp-workshop`
4. **Scopes**: Check these boxes:
   - ✅ `repo` (full control of private/public repos)
   - ✅ `public_repo` (access public repos)
5. Click **Generate token**
6. **Copy the token** (you won't see it again)

### Step 1.4: Set Up Environment

```bash
# Copy the template
cp .env.example .env

# Open .env in your editor
nano .env  # or: code .env  (in VS Code)
```

Add your GitHub token:
```
GITHUB_TOKEN=ghp_xxxxxxxxxxxx
```

Save and close.

### Step 1.5: Verify Setup

```bash
node -v
# Should be 18+

npm -v
# Should be 8+

# Check if .env file exists and has token
cat .env | grep GITHUB_TOKEN
# Should output: GITHUB_TOKEN=ghp_xxxxxxxxxxxx
```

✅ **Setup complete!** You're ready to start the server.

---

## Phase 2: Start Your MCP Server (5 minutes)

### Step 2.1: Start the Server

```bash
npm start
```

You should see output like:
```
🚀 MCP Server starting...
📡 Listening on http://localhost:3000
✅ Tools loaded:
  - list_github_issues
  - create_github_issue
  - update_github_issue
Ready for connections.
```

**The server is now running.** Don't close this terminal. Keep it open.

### Step 2.2: Verify Server Health (New Terminal Tab/Window)

Open a **new terminal** (don't close the one running the server):

```bash
# Test if server is responding
curl http://localhost:3000/health

# Should respond with: {"status": "ok"}
```

✅ **Server is healthy!** You can now connect Claude.

---

## Phase 3: Connect Claude to Your MCP Server (5 minutes)

### Option A: Claude Web (claude.ai)

1. Open https://claude.ai
2. In a conversation, look for the **@** icon or **mention** button
3. Click it and select **MCP Servers** or **Manage MCP Servers**
4. Click **Add Server**:
   - **Name**: `github-mcp`
   - **URL**: `http://localhost:3000`
   - **Type**: `local` or `stdio`
5. Click **Connect**

You should see: "✅ Connected to github-mcp"

### Option B: Claude Desktop

1. Open Claude Desktop app
2. Click **Settings** → **MCP Servers**
3. Click **Add MCP Server**:
   - **Name**: `github-mcp`
   - **URL**: `http://localhost:3000`
   - **Type**: `local`
4. Click **Save**

Claude Desktop will automatically connect.

### Option C: Cursor IDE

1. Open Cursor settings
2. Go to **Extensions** → **MCP**
3. Click **Add New Server**:
   - **Name**: `github-mcp`
   - **URL**: `http://localhost:3000`
4. Save and reload

---

## Phase 4: Test Your MCP Server with Claude (20 minutes)

### Test 4.1: Verify Tool Discovery

In Claude, ask:
```
What tools are available from the MCP servers I have connected?
```

Claude should respond with something like:
```
I can see 3 tools from your GitHub MCP server:
1. list_github_issues - List open GitHub issues
2. create_github_issue - Create a new GitHub issue
3. update_github_issue - Update an existing issue
```

**If Claude doesn't see the tools**, check:
- [ ] Server is running (check terminal)
- [ ] Claude shows "Connected to github-mcp"
- [ ] No errors in server terminal
- [ ] URL is exactly `http://localhost:3000`

### Test 4.2: List Your Issues

In Claude, ask:
```
List my open GitHub issues from the adarsh22pm/mcp-workshop repository
```

Claude will:
1. Call the `list_github_issues` tool
2. Your MCP server calls GitHub API
3. GitHub returns your issues
4. Claude shows you the results

You should see something like:
```
Based on your GitHub MCP server, here are your open issues in adarsh22pm/mcp-workshop:

1. #42 - Add evals module
   Created 2 days ago
   
2. #43 - Improve error messages
   Created 1 day ago
   
3. #44 - Optimize performance
   Created 5 hours ago

You have 3 open issues total.
```

**Success!** Your MCP server is working.

### Test 4.3: Try Different Repositories

Ask Claude:
```
List issues from the nodejs/node repository
```

Claude will call the same tool with different parameters. This shows that your MCP tool is flexible and works with any repo.

### Test 4.4: Create an Issue (Optional, Advanced)

Ask Claude:
```
Create a GitHub issue in adarsh22pm/mcp-workshop with:
Title: "Test issue from MCP"
Description: "This was created via MCP server"
```

Claude will:
1. Call `create_github_issue` tool
2. Your MCP server calls GitHub API to create it
3. GitHub returns the created issue
4. Claude confirms creation

**You've just created a GitHub issue through Claude via your MCP server.** That's production-ready!

---

## Phase 5: Troubleshooting (If Things Go Wrong)

### Issue: "Cannot connect to MCP server"

**Checklist**:
```bash
# 1. Is the server running?
curl http://localhost:3000/health
# If you get connection refused, server is not running

# 2. Start it in a new terminal
npm start

# 3. Check the URL in Claude
# Should be: http://localhost:3000 (not https://)
```

### Issue: "GitHub token invalid"

```bash
# 1. Check token in .env
cat .env

# 2. Generate new token at https://github.com/settings/tokens
# 3. Update .env with new token
# 4. Restart server: Ctrl+C, then npm start
```

### Issue: "Rate limit exceeded"

GitHub API limits requests. Wait 10 minutes and try again.

To avoid: Don't call tools excessively. The starter code handles rate limits gracefully.

### Issue: "Port 3000 already in use"

```bash
# Option 1: Kill the process using port 3000
lsof -i :3000  # Find process ID
kill -9 <PID>  # Kill it

# Option 2: Use different port
PORT=3001 npm start
# Then connect Claude to http://localhost:3001
```

### Issue: Claude doesn't see the tools

```bash
# 1. Check server output for errors
# Look at the terminal running npm start
# You should see: "✅ Tools loaded"

# 2. Disconnect and reconnect Claude
# In Claude: Settings → MCP → Disconnect → Connect again

# 3. Restart server
# Ctrl+C, then npm start
```

---

## Phase 6: What You've Accomplished (10 minutes)

Congratulations! You've built and tested a production-grade MCP server:

✅ **Architecture**: Understand Claude ↔ MCP ↔ GitHub API  
✅ **Setup**: Cloned starter, configured token, started server  
✅ **Integration**: Connected Claude to your local MCP server  
✅ **Testing**: Called real tools and got real data from GitHub  
✅ **Scaling**: Know how to add more tools  

---

## 📋 Verification Checklist

Before moving to Module 4, verify:

- [ ] Server runs without errors (`npm start` works)
- [ ] Claude sees your MCP server (shows "Connected")
- [ ] Claude can list GitHub issues (tested with real prompt)
- [ ] Claude can access different repositories
- [ ] You understand the flow: Claude → MCP → GitHub → MCP → Claude
- [ ] Token is securely stored in `.env` (not committed)

---

## 🎯 Next Steps

**[→ Continue to Module 4: Build from Scratch](../04-build-from-scratch/README.md)**

You just built an MCP server from an **existing API**. Module 4 teaches you to build one from **just an idea**, without an existing API to scaffold from.

---

## 💡 Pro Tips

### Tip 1: Keep Terminal Visible
Keep the server terminal visible while testing. Errors appear there first.

### Tip 2: Use Separate Tokens
Use different GitHub tokens for different projects. Makes debugging easier.

### Tip 3: Test Tool Limits
Before exposing a tool to many users, test it with various inputs:
- Empty repositories
- Large repositories (10,000+ issues)
- Private repositories you don't have access to

### Tip 4: Monitor Rate Limits
GitHub API rate limit resets every hour. Check status:
```bash
curl -H "Authorization: token $GITHUB_TOKEN" https://api.github.com/rate_limit | jq
```

---

## 📚 Further Reading

- [GitHub API Docs](https://docs.github.com/en/rest)
- [MCP Protocol Spec](https://modelcontextprotocol.io/specification)
- [Node.js Best Practices](https://nodejs.org/en/docs/)

---

**Stuck?** Check [resources/troubleshooting-faq.md](../../resources/troubleshooting-faq.md)

**Ready to build from scratch?** [→ Module 4](../04-build-from-scratch/README.md)
