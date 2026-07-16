# Tool Design: Naming and Descriptions That Agents Understand

**Read time**: ~35 minutes  
**Prerequisites**: Module 1 (MCP Fundamentals)

---

## 1. The Tool Lifecycle

When an agent uses your tool, it goes through these phases:

### Phase 1: Discovery
Agent initializes MCP connection and reads all available tools.

```
Agent: "What tools are available?"
MCP Server: "Here's the list of tools..."
```

### Phase 2: Decision
Agent reads tool names and descriptions and decides if any match the user's request.

```
Agent: "User asked 'List my GitHub issues'. Do I have a tool for that?"
Agent: [Reading tool list...]
Agent: "Yes! There's a tool called 'list_github_issues'. Description says 'List all open issues in a repository'. That's what I need."
```

### Phase 3: Invocation
Agent calls the tool with appropriate parameters.

```
Agent: "Calling list_github_issues(owner='adarsh22pm', repo='mcp-workshop')"
```

### Phase 4: Results Processing
Agent receives results and responds to the user.

```
MCP Server: {"issues": [{"id": 1, "title": "Add docs"}, ...]}
Agent: "You have 3 open issues: 'Add docs', 'Fix auth bug', 'Optimize performance'"
```

**Your job as a tool designer is to make Phases 1-2 crystal clear.** If the agent can't discover your tool or understand what it does, Phase 3 and 4 never happen.

---

## 2. Tool Naming Patterns

Good tool names follow patterns. Here are the most effective:

### Pattern 1: Verb + Resource

```
list_issues
create_pull_request
delete_comment
update_status
get_repository
```

**Why it works**: The verb tells the agent the action (list, create, delete, update, get). The resource tells the agent what it's acting on (issues, pull request, comment, status, repository).

**For an agent**: "Ah, this **lists** **issues**. That's what I need."

### Pattern 2: Question Format (Less Common)

```
is_user_authorized
has_repository_permission
```

**Why it works**: Agents can recognize these as "yes/no questions."

### Pattern 3: Gerund (Verb + -ing)

```
filtering_issues
sorting_repositories
validating_credentials
```

**Why it works**: Describes the action in progress. Useful for tools that do processing.

---

## 3. What Makes a Tool Name Bad

### ❌ Bad Pattern 1: Too Vague

```
get_data
process_info
do_thing
tool_function_v2
```

**Why it fails**: Agents can't tell what resource or action this applies to. Is `get_data` getting GitHub issues? User profiles? Random data?

### ❌ Bad Pattern 2: Acronyms Without Context

```
ghi  (get_issues_help?)
cfp  (create_file_post?)
udp  (update_data_process?)
```

**Why it fails**: Agents don't know what these mean. You might, but agents haven't seen your codebase.

### ❌ Bad Pattern 3: No Verb

```
github_issues
user_profile
repository_list
```

**Why it fails**: No action. Is this reading data, writing it, deleting it? Agents are unsure.

### ❌ Bad Pattern 4: Inconsistency

```
list_issues
create_pr
delete_comments
update_repo_status
get_user_info
```

**Why it fails**: Sometimes you use `list`, sometimes `get`. Sometimes you write the full word (`pull_request`), sometimes you abbreviate (`pr`). Agents notice inconsistency and distrust the tool.

---

## 4. Tool Descriptions: The Real UX

A tool **name** tells the agent what exists. A **description** tells the agent **when to use it.**

### Format for Strong Descriptions

```
<One-line summary>. <When to use this>. <Supported actions or edge cases>.
```

**Example**:
```
List all open GitHub issues in a repository. Use this when you need to see what problems are currently reported. Returns issue ID, title, state, and creation date.
```

Let's break this down:
- **One-line summary**: "List all open GitHub issues in a repository"
- **When to use**: "Use this when you need to see what problems are currently reported"
- **Edge cases/output**: "Returns issue ID, title, state, and creation date"

### Why This Format Works

**One-line summary** tells the agent what the tool does.  
**When to use** tells the agent when it's relevant (critical for decision-making).  
**Output description** helps the agent understand what it will get back.

---

## 5. Common Description Mistakes

### ❌ Mistake 1: Too Technical

```
Query the GitHub REST API v3 /repos/{owner}/{repo}/issues endpoint with optional filters for state, assignee, and label via the OctoKit library wrapper.
```

**Why it fails**: Agents don't care about implementation details. They care about what the tool does for the user.

**Better**:
```
List all open GitHub issues in a repository. Returns issue title, number, and creation date.
```

### ❌ Mistake 2: Assumes Context

```
Gets the thing
```

**Why it fails**: What thing? No context.

**Better**:
```
Retrieve a GitHub repository's README file and return its content as markdown.
```

### ❌ Mistake 3: Missing "When to Use"

```
Create a GitHub issue
```

**Why it fails**: The agent knows what it does, but not when. Is this for bug reports? Feature requests? Any issue?

**Better**:
```
Create a new GitHub issue. Use this to report bugs, request features, or log tasks. Provide a clear title and description.
```

### ❌ Mistake 4: No Output Description

```
Update user status
```

**Why it fails**: The agent doesn't know what to expect back. Success/failure? A confirmation? New status object?

**Better**:
```
Update a user's status and mood emoji in Slack. Returns the updated status as confirmation.
```

---

## 6. Tool Parameters: The Interface

Each tool parameter also needs clarity.

### Good Parameter Naming

```
list_github_issues(
  owner: string,        // GitHub username or org name
  repo: string,         // Repository name
  state?: "open"|"closed",  // Filter by issue state (default: "open")
  limit?: number        // Max results to return (default: 10)
)
```

### Bad Parameter Naming

```
list_issues(
  a: string,            // What is 'a'?
  b: string,            // What is 'b'?
  opts?: any            // What's in opts? Unclear.
)
```

**Rule**: Every parameter should have a clear name and a description (especially if it's optional or has specific allowed values).

---

## 7. Destructive Actions: Be Extra Clear

Tools that **delete**, **modify**, or **overwrite** data need crystal-clear descriptions.

### Good: Clear Warning

```
Name: delete_github_issue
Description: Delete a GitHub issue (IRREVERSIBLE). The issue and all its comments will be permanently removed. Use only if absolutely necessary and the user explicitly requests deletion.
```

### Bad: No Warning

```
Name: delete_issue
Description: Delete issue
```

**Why the good version works**: Agents read "IRREVERSIBLE" and "permanently removed" and think twice before calling this. They only call it if the user explicitly says "delete."

---

## 8. Tool Specificity: One Job Per Tool

Each tool should do **one thing well**, not multiple things.

### Good: Specific Tools

```
list_github_issues         // Lists issues
create_github_issue        // Creates one issue
update_github_issue        // Updates one issue
delete_github_issue        // Deletes one issue
```

### Bad: Overloaded Tool

```
manage_issues              // Could mean list, create, update, delete. Unclear.
```

**Why it matters**: Agents make better decisions when tools are specific. If you have one `manage_issues` tool that does four things, agents will be confused about which one to call and might pick wrong.

---

## 9. Tool Descriptions: Real Examples

### Example 1: GitHub Issue Tool (Good)

```
Name: list_github_issues
Description: List all open GitHub issues in a repository. Returns issue ID, title, state, labels, and creation date. Use this when you need to see the current problems or tasks in a project.

Parameters:
  - owner (required): GitHub username or organization name
  - repo (required): Repository name  
  - state (optional): Filter by "open" or "closed" (default: "open")
  - limit (optional): Maximum number of issues to return (default: 10)
```

**Why it works**:
- Name clearly indicates action (list) and resource (github_issues)
- Description explains what it returns and when to use it
- Parameters are specific with clear types and defaults
- Optional fields are marked

### Example 2: Slack Message Tool (Good)

```
Name: send_slack_message
Description: Send a message to a Slack channel or direct message. The message can include formatted text, links, and emoji. Use this to notify team members, log alerts, or update shared channels.

Parameters:
  - channel (required): Slack channel name (e.g., "#engineering") or user ID for DMs
  - message (required): Message text (supports Slack markdown)
  - thread_ts (optional): Message timestamp if replying to a thread
```

**Why it works**:
- Name indicates action (send) and platform (slack) and resource (message)
- Description tells agent when to use it (notifications, alerts, updates)
- Includes formatting note (supports markdown)
- All parameters are clear

---

## 10. Testing Your Tool Design

Before you build Module 3, test your tool descriptions by asking: **Would an agent pick this tool?**

### Mental Test 1: Clarity
User: "I want to see my GitHub issues."  
You: "Here's `list_github_issues`. Would you pick it?"  
Mental agent: "Yes, obviously. The name and description make it clear."

✅ Passes

### Mental Test 2: Specificity
User: "I need to know if I have permission to delete this repo."  
You: "Here's a tool called `manage_repo`."  
Mental agent: "Hmm, manage_repo could mean create, delete, update, or read. Not sure which one to use."

❌ Fails. You need separate tools: `delete_repository`, `update_repository`, etc.

### Mental Test 3: Destructiveness
User: "Remove my old repo."  
You: "Here's `delete_repository`. Description: Permanently delete a repository (irreversible). All code, issues, and history will be lost. Requires explicit user confirmation."  
Mental agent: "This is destructive. I'll only call it if the user explicitly asks for deletion. User said 'Remove'—that's clear enough."

✅ Passes

---

## 11. Checklist: Is Your Tool Well-Designed?

Before coding (Modules 3 and 4), every tool should pass this checklist:

- [ ] **Name follows verb + resource pattern** (e.g., `list_issues`, not `get_data`)
- [ ] **Description is 1-3 sentences** (clear and concise)
- [ ] **Description includes when to use** ("Use this when...")
- [ ] **All parameters have descriptive names** (not `a`, `b`, `opts`)
- [ ] **Optional parameters are marked and have defaults** (not required by default)
- [ ] **Destructive tools are clearly marked** ("IRREVERSIBLE", "permanently deleted")
- [ ] **Output is described** ("Returns issue ID, title, state...")
- [ ] **One tool does one job** (not overloaded)
- [ ] **Name and description match** (no contradictions)

---

## 12. Summary

Tool design is about thinking like an agent:

1. **Names** are discovery. Make them verb + resource (e.g., `list_issues`).
2. **Descriptions** are decision-making. Explain what it does AND when to use it.
3. **Parameters** are precision. Name them clearly and describe what each does.
4. **Safety** is trust. Mark destructive actions clearly.
5. **Specificity** is clarity. One tool, one job.

Get these right, and agents will use your tools. Get them wrong, and agents will skip them.

---

## ✅ Next Step

Go to **[exercise.md](exercise.md)** and practice renaming 5 poorly-designed tools. This hands-on work trains your eye for good design.

After the exercise, you're ready for **Module 3: Build from API**, where you'll design and build a real MCP server.
