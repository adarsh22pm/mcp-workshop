# Building an MCP Server from GitHub API: The Zuplo Approach

**Read time**: ~20 minutes  
**Prerequisites**: Modules 1–2

---

## 1. The Problem We're Solving

You have:
- **GitHub API** (REST, well-documented)
- **Claude** (powerful but doesn't know how to call GitHub API)
- **Goal**: Make Claude able to list your GitHub issues, create PRs, etc.

Without MCP, you'd need to:
1. Write documentation explaining the API to Claude
2. Claude struggles to format API requests correctly
3. Manual back-and-forth to get the right data

With MCP:
1. Scaffold an MCP server from GitHub's OpenAPI spec
2. Claude discovers and calls tools automatically
3. Done

---

## 2. The Architecture: Three Components

```
┌─────────────────┐
│    Claude       │
│   (in browser)  │
└────────┬────────┘
         │ "List my issues"
         │
         ▼
┌─────────────────────────────────────┐
│  Your MCP Server (localhost:3000)   │
│  ┌───────────────────────────────┐  │
│  │ Tool Definitions (from GitHub │  │
│  │ OpenAPI spec)                 │  │
│  │ - list_github_issues          │  │
│  │ - create_github_pull_request  │  │
│  │ - update_github_issue         │  │
│  └───────────────────────────────┘  │
│  ┌───────────────────────────────┐  │
│  │ Implementation (Node.js code) │  │
│  │ - Calls GitHub API            │  │
│  │ - Translates results          │  │
│  └───────────────────────────────┘  │
└────────┬────────────────────────────┘
         │ HTTP request to GitHub API
         │
         ▼
┌─────────────────┐
│  GitHub API     │
│ (api.github.com)│
└────────┬────────┘
         │ JSON response
         │
         ▼
┌─────────────────────────────────────┐
│  Your MCP Server (processes result) │
└────────┬────────────────────────────┘
         │ Formatted MCP response
         │
         ▼
┌─────────────────┐
│    Claude       │
│ "You have 3...  │
└─────────────────┘
```

**Three layers:**
1. **Claude** — The agent asking questions
2. **Your MCP Server** — The bridge (tool definitions + implementations)
3. **GitHub API** — The actual data source

---

## 3. How Zuplo Helps

**Zuplo** is an API gateway that scaffolds MCP servers from OpenAPI specs.

### Without Zuplo (The Hard Way):

You'd manually:
1. Read GitHub's OpenAPI spec
2. Write tool definitions for each endpoint
3. Write implementation code for each tool
4. Test it all
5. Deploy it

**This takes hours.**

### With Zuplo (The Easy Way):

1. Zuplo imports GitHub's OpenAPI spec
2. Zuplo auto-generates tool definitions
3. You provide a simple Node.js wrapper
4. Zuplo handles the MCP protocol
5. Done in ~30 minutes

---

## 4. Step-by-Step: What You'll Do

### Step 1: Clone Starter Repo (1 min)
```bash
git clone https://github.com/adarsh22pm/mcp-github-starter
cd mcp-github-starter
npm install
```

This gives you:
- Pre-configured MCP server
- GitHub API wrapper
- Environment setup

### Step 2: Set Up GitHub Token (2 min)
```bash
# Create token at https://github.com/settings/tokens
# Scopes needed: repo, public_repo

# Add to .env file
GITHUB_TOKEN=ghp_xxxxxxxxxxxx
```

This authenticates your MCP server to call GitHub API.

### Step 3: Start the Server (1 min)
```bash
npm start
# Server running on http://localhost:3000
```

Your MCP server is now live locally.

### Step 4: Connect Claude (5 min)
In Claude (web or desktop):

1. Click the **@** icon (mention a resource)
2. Select **MCP Servers**
3. Add new MCP server:
   - **URL**: `http://localhost:3000`
   - **Type**: `stdio` (local connection)
4. Save

Claude now has access to your GitHub tools.

### Step 5: Test Live (10 min)
Ask Claude: "List my open GitHub issues"

Claude will:
1. Read tool definitions from your MCP server
2. Call `list_github_issues`
3. Get results from GitHub API
4. Return formatted response to you

---

## 5. The MCP Protocol Handshake

Here's what happens behind the scenes:

### Connection Initialization

```
Claude: "Connect to MCP server at localhost:3000"
        ↓
MCP Server: "Connected! Here are my tools:"
        ↓
MCP Server sends tool list:
{
  "name": "list_github_issues",
  "description": "List all open GitHub issues",
  "inputSchema": {...}
}
```

### Tool Invocation

```
Claude: "User asked to list issues. I'll call list_github_issues"
        ↓
Claude sends: call_tool("list_github_issues", {owner: "adarsh22pm", repo: "mcp-workshop"})
        ↓
MCP Server receives call
        ↓
MCP Server calls GitHub API: GET /repos/adarsh22pm/mcp-workshop/issues
        ↓
GitHub returns: [{id: 1, title: "Add evals", ...}, ...]
        ↓
MCP Server returns: {success: true, data: [...]}
        ↓
Claude reads result and formats for user
        ↓
Claude: "You have 3 open issues: 'Add evals', 'Improve auth', ..."
```

This happens in milliseconds. From Claude's perspective, it's just calling a tool.

---

## 6. Understanding the Code Structure

Your starter repo has this structure:

```
mcp-github-starter/
├── server.mjs          // MCP server (listens for Claude)
├── github-client.mjs   // GitHub API wrapper (calls GitHub)
├── tools.mjs           // Tool definitions (from OpenAPI spec)
├── .env.example        // Copy to .env, fill in token
├── package.json        // Dependencies
└── README.md          // Detailed setup guide
```

### server.mjs (The Brain)

```javascript
import { Server } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server({
  name: "github-mcp",
  version: "1.0.0",
});

// Register tools from GitHub API
server.setRequestHandler(
  ListToolsRequestSchema,
  async () => ({
    tools: GITHUB_TOOLS, // Imported from tools.mjs
  })
);

// Handle tool calls
server.setRequestHandler(
  CallToolRequestSchema,
  async (request) => {
    const { name, arguments: args } = request.params;
    
    if (name === "list_github_issues") {
      return await githubClient.listIssues(args.owner, args.repo);
    }
    // ... other tools
  }
);
```

### github-client.mjs (The Worker)

```javascript
export const githubClient = {
  async listIssues(owner, repo) {
    const response = await fetch(
      `https://api.github.com/repos/${owner}/${repo}/issues`,
      {
        headers: {
          Authorization: `token ${process.env.GITHUB_TOKEN}`,
        },
      }
    );
    
    const issues = await response.json();
    return {
      content: [
        {
          type: "text",
          text: `Found ${issues.length} issues:\n${issues.map(i => `- ${i.title}`).join("\n")}`,
        },
      ],
    };
  },
};
```

The flow:
1. Claude calls `list_github_issues` tool
2. `server.mjs` receives the call
3. `server.mjs` calls `githubClient.listIssues()`
4. `github-client.mjs` calls GitHub API
5. Result is formatted and returned to Claude

---

## 7. Why This Matters

Before (without MCP):
- User: "List my issues"
- Claude: "I'm not sure how to call GitHub API"
- User: "Try GET /repos/owner/repo/issues"
- Claude: "I got 50 closed issues too. Let me filter..."
- Slow and error-prone

After (with MCP):
- User: "List my issues"
- Claude: "I see a tool `list_github_issues`. Calling it."
- MCP: Returns open issues only
- Claude: "You have 3 open issues"
- Fast and reliable

---

## 8. Scaling: Adding More Tools

The starter includes:
- `list_github_issues` (read)
- `create_github_issue` (create)
- `update_github_issue` (update)

To add more tools from GitHub API:

1. **Find the GitHub API endpoint** (e.g., `GET /repos/{owner}/{repo}/pulls`)
2. **Add to tools.mjs**:
   ```javascript
   {
     name: "list_github_pull_requests",
     description: "List all GitHub pull requests",
     inputSchema: {...}
   }
   ```
3. **Add implementation to server.mjs**:
   ```javascript
   if (name === "list_github_pull_requests") {
     return await githubClient.listPullRequests(args.owner, args.repo);
   }
   ```
4. **Implement in github-client.mjs**:
   ```javascript
   async listPullRequests(owner, repo) {
     // Call GitHub API
   }
   ```

Each tool takes ~5 minutes to add once the pattern is clear.

---

## 9. Common Pitfalls

### ❌ Pitfall 1: Exposing Sensitive Tools
Don't expose tools like:
- Delete user account
- Modify payment methods
- Change admin permissions

Unless absolutely necessary. Tools are UX for agents, and agents might call them unexpectedly.

### ❌ Pitfall 2: Ignoring Rate Limits
GitHub API has rate limits. Don't spam requests. In your `github-client.mjs`, respect them:

```javascript
if (response.status === 429) {
  return {
    isError: true,
    content: [{type: "text", text: "Rate limit exceeded. Try again later."}]
  };
}
```

### ❌ Pitfall 3: Not Validating Input
Always validate parameters:

```javascript
if (!owner || !repo) {
  return {
    isError: true,
    content: [{type: "text", text: "owner and repo are required"}]
  };
}
```

---

## 10. Summary: The Full Flow

1. **Setup** (5 min): Clone repo, add token, start server
2. **Configuration** (5 min): Tell Claude about your MCP server
3. **Discovery** (automatic): Claude reads tool definitions
4. **Invocation** (automatic): Claude calls tools when appropriate
5. **Execution** (automatic): MCP server calls GitHub API
6. **Response** (automatic): Results return to Claude and user

All automated. You just set it up once.

---

## ✅ What You've Learned

- ✅ How Zuplo scaffolds MCP servers from OpenAPI specs
- ✅ The architecture: Claude ↔ MCP Server ↔ GitHub API
- ✅ The MCP protocol handshake (discovery → invocation → response)
- ✅ Code structure (server.mjs, github-client.mjs, tools.mjs)
- ✅ How to add new tools

---

## 🚀 Next: Hands-On

**[→ Go to testing-guide.md](testing-guide.md)**

Follow the step-by-step instructions to:
1. Clone the starter repo
2. Set up your GitHub token
3. Start the MCP server
4. Connect Claude
5. Test with real prompts

Estimated time: 60 minutes from zero to working MCP server.
