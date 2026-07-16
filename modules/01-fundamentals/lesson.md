# MCP Fundamentals: The Universal Interface for AI Agents

**Read time**: ~30 minutes  
**Prerequisites**: None. No coding knowledge required for this lesson.

---

## 1. What is MCP?

**Model Context Protocol** (MCP) is a standardized way for AI agents to interact with tools, data sources, and services.

Think of it like an electrical outlet. Your home has outlets (a standard). Appliances (agents) plug into them without knowing what's on the other side. A toaster doesn't care if the outlet is powering a refrigerator elsewhere—it just knows how to ask for power.

MCP works the same way:
- **Appliances** = AI agents (Claude, ChatGPT, Cursor)
- **Outlets** = MCP servers (your tools and data)
- **Electricity** = tool descriptions and results
- **Standards** = the MCP protocol (how they talk)

### Why Does MCP Exist?

Before MCP, every AI agent had its own way of connecting to tools:
- Claude had one API
- ChatGPT had another
- Cursor had yet another

If you wanted your product to work with all three agents, you'd build three integrations. That's expensive and slow.

**MCP fixes this**: Build once, work everywhere. One MCP server talks to Claude, ChatGPT, Cursor, and future agents, all without changes.

---

## 2. MCP vs. Alternatives

### MCP vs. API

**API**: You make HTTP requests. The server responds with data.  
**MCP**: The agent reads your tool definitions, decides if it should use them, then asks to call one.

**Example**: You have a GitHub API.

**With API alone:**
1. Agent asks Claude: "Can you list my GitHub issues?"
2. Claude: "I don't know how to do that. You need to ask an API."
3. You manually tell Claude: "There's a GET /repos/{owner}/{repo}/issues endpoint"
4. Claude formats the request and calls it

**With MCP:**
1. Agent asks Claude: "Can you list my GitHub issues?"
2. Claude: "I see a tool called 'list_github_issues'. Let me use it."
3. Claude calls the tool automatically
4. Done

The agent **discovers** your tools from descriptions, not from you manually telling it.

### MCP vs. Webhooks

**Webhooks**: You send data to the agent when something happens.  
**MCP**: The agent asks for data when it needs it.

Webhooks are "push." MCP is "pull." For AI workflows, pull (MCP) is usually better because agents control the flow.

### MCP vs. SDK/Plugin

**SDK**: Requires code changes to your app (tight coupling).  
**MCP**: Works standalone. Your app doesn't need to know about MCP.

If you build an MCP server for GitHub, you don't need to modify GitHub. The MCP server is separate, and agents use it as a bridge.

---

## 3. The Three MCP Primitives

MCP servers can offer three types of resources to agents:

### 1. Tools (The Most Common)
**Definition**: A function the agent can call.

**Example**: `list_github_issues`
```
Tool name: list_github_issues
Description: List all open issues in a GitHub repository
Parameters:
  - owner (required): GitHub username or org name
  - repo (required): Repository name
```

Agents see this definition, understand what it does, and call it when relevant.

### 2. Resources
**Definition**: Data the agent can read but not execute.

**Example**: A document, a file, a config.

```
Resource: /repos/{owner}/{repo}/README
Type: markdown
Description: The README file for a GitHub repository
```

Agents can fetch resources to understand context, but they don't "call" resources—they just read them.

### 3. Prompts
**Definition**: Pre-written instructions or templates for agents.

**Example**: A code review prompt or a customer support template.

```
Prompt: github_code_review
Description: Template for reviewing a GitHub PR
```

Agents can invoke prompts to get consistent, domain-specific instructions.

**For this course, we focus on Tools.** They're the most powerful and most common MCP primitive.

---

## 4. How Agents Discover and Use MCP Servers

Here's the step-by-step flow:

### Step 1: Agent Initialization
When you open Claude (or ChatGPT, or Cursor), the agent loads its configured MCP servers.

You might tell Claude: "Connect to my_company_mcp_server (running on localhost:3000)"

Claude connects to that server and reads all available tools.

### Step 2: Agent Reads Tool Definitions
The MCP server sends a list of all its tools:

```json
[
  {
    "name": "list_issues",
    "description": "List all open GitHub issues in a repo",
    "inputSchema": {
      "type": "object",
      "properties": {
        "owner": {"type": "string", "description": "GitHub owner"},
        "repo": {"type": "string", "description": "Repository name"}
      }
    }
  }
]
```

### Step 3: Agent Decides When to Use Tools
You ask Claude: "Show me my open issues."

Claude reads the tool definitions and thinks:
- "There's a tool called `list_issues`. The user wants to see open issues. This tool seems relevant."
- Claude decides to call `list_issues`
- Claude formats the input (owner="adarsh22pm", repo="mcp-workshop")
- Claude calls the tool

### Step 4: Agent Gets Results and Responds
The MCP server executes the tool and returns results:

```json
{
  "issues": [
    {"id": 1, "title": "Add evals module", "state": "open"},
    {"id": 2, "title": "Improve error messages", "state": "open"}
  ]
}
```

Claude reads the results and tells you: "You have 2 open issues: 'Add evals module' and 'Improve error messages'."

---

## 5. Why Tool Naming and Description Matter

This is the critical insight. **Agents pick tools based on their name and description.**

### Example 1: Good Description

```
Name: get_user_profile
Description: Retrieve a user's profile information including name, email, account creation date, and preferences. Use this when you need details about a specific user.
```

When you ask Claude: "What's Sarah's email?"  
Claude thinks: "I need a user's details. `get_user_profile` sounds right. Let me use it."

### Example 2: Bad Description

```
Name: user_data
Description: User function
```

Same question: "What's Sarah's email?"  
Claude thinks: "Hmm, is `user_data` for reading or writing? Does it get profile info or purchase history? I'm not sure. Let me not use it."

Result: Claude won't use your tool, even though it could solve the problem.

### Example 3: Wrong Name, Right Description

```
Name: user_password_reset
Description: Retrieve a user's profile information including name, email, account creation date, and preferences.
```

Claude reads this and is confused. The name says "password_reset" but the description says "retrieve profile." Claude might not use it because the name and description don't match.

### The Rule

**Name + Description = Agent UX**

Agents read these two things and decide:
1. Does this tool exist? (name)
2. Does it solve my problem? (description)
3. Do the name and description agree? (consistency check)

If any answer is "no," agents skip your tool.

---

## 6. Real-World Examples

### Example 1: Slack MCP Server

Imagine Anthropic built a Slack MCP server with these tools:

```
Tool: send_message
Description: Send a message to a Slack channel or DM

Tool: list_messages
Description: List recent messages in a Slack channel

Tool: set_status
Description: Set your Slack status and mood emoji
```

When you tell Claude: "Post a message to #engineering saying the deploy is done."

Claude thinks:
- "The user wants to send a Slack message."
- "There's a tool called `send_message` with description 'Send a message to a Slack channel or DM'."
- "That's exactly what I need."
- Claude calls `send_message` with channel="#engineering" and message="The deploy is done."

### Example 2: GitHub MCP Server (Good vs. Bad)

**Good**:
```
Tool: create_pull_request
Description: Create a new pull request in a GitHub repository. Provide a title, description, and the base/head branches.
```

**Bad**:
```
Tool: pr
Description: GitHub tool
```

With the good description, Claude knows exactly when to use it. With the bad one, Claude might not even try.

### Example 3: When NOT to Use a Tool

You have a tool:
```
Name: delete_account
Description: Permanently delete a user account (irreversible)
```

When you ask Claude: "Show me how to delete an account."

Claude reads the description and thinks:
- "This actually **deletes** an account. It's irreversible and destructive."
- "I should NOT call this unless the user explicitly asks me to delete their account."
- "The user asked how to delete, not to delete it."
- Claude skips the tool and instead explains the process in text.

Good tool descriptions also tell agents when NOT to use them.

---

## 7. The Three Things Agents Look For

When an agent sees your MCP server, it evaluates:

### 1. **Relevance** (Description)
"Does this tool solve the user's problem?"

Clear descriptions help agents decide fast.

### 2. **Safety** (Name + Description)
"Is it safe to call this? Are there risks?"

If a tool deletes data, the description should make that clear.

### 3. **Confidence** (Name consistency)
"Does the name match the description? Can I trust this?"

If the name and description contradict, agents avoid the tool.

---

## 8. MCP Server Components

An MCP server has three main parts:

### 1. Tool Definitions
```json
{
  "name": "list_issues",
  "description": "List open issues",
  "inputSchema": {...}
}
```

These tell agents what tools exist.

### 2. Tool Implementations
```javascript
async function list_issues(owner, repo) {
  return fetch(`https://api.github.com/repos/${owner}/${repo}/issues`);
}
```

This actually does the work.

### 3. MCP Protocol Handler
The code that:
- Listens for agent requests
- Reads tool definitions
- Calls the right implementation
- Returns results to the agent

You'll build all three in Modules 3 and 4.

---

## 9. The MCP Landscape

Who's building MCP servers?

- **Anthropic** (Claude native): GitHub, Slack, Gmail, Figma, Linear
- **OpenAI** (ChatGPT): Starting to support MCP
- **Cursor**: AI code editor with built-in MCP support
- **Companies**: Teams are building MCP servers for their own products

Why? Because agents are the new interface. If your product doesn't have an MCP server, agents can't use it.

---

## 10. Summary: What You Now Know

- ✅ MCP is the standard interface for agents to discover and use tools
- ✅ Agents read tool names and descriptions to decide when to use them
- ✅ Tool descriptions are UX for agents—clarity matters
- ✅ One MCP server works with Claude, ChatGPT, Cursor, and future agents
- ✅ MCP servers have three components: definitions, implementations, protocol handler
- ✅ Good tool naming and descriptions lead to agents using your tools. Bad ones lead to agents ignoring them.

---

## 🎯 Your Takeaway

**"Tool descriptions are UX for agents."**

This is the core insight of this entire course. Every module builds on this idea. Get this right, and agents will use your tools reliably. Get it wrong, and agents will skip them.

---

## ✅ Next Step

Go to **[quiz.md](quiz.md)** and take the 5-question quiz to validate your understanding.

Once you pass, move to **Module 2: Tool Design**.
