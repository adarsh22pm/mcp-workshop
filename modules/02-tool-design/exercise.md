# Module 2 Exercise: Rename These Tools

**Time**: ~20 minutes  
**Type**: Hands-on practice  
**Goal**: Train your eye for good tool naming and descriptions

---

## Instructions

You have 5 poorly-named/described tools. Your job:

1. **Rename** each tool using the patterns from the lesson
2. **Rewrite** the description using the 3-part format (summary, when-to-use, output)
3. **Explain** why your version is better

## Tool 1: The Vague Getter

**Original**:
```
Name: get_data
Description: Get some data from the server
```

**Your Task**:
- What do you think this tool does? (Be honest—it's unclear)
- Rename it and rewrite the description
- Assume it actually retrieves a GitHub repository's metadata (name, stars, forks, last update)

**Example Format**:
```
New Name: get_github_repository
New Description: Retrieve a GitHub repository's metadata including name, description, number of stars, forks, and last update date. Use this when you need details about a specific repository.

Explanation: The original name 'get_data' is too vague. My new name uses the verb 'get' and clearly states the resource (github_repository). The description explains what it returns and when to use it.
```

<details>
<summary>Click to see our solution (but try first!)</summary>

```
Name: get_github_repository
Description: Retrieve a GitHub repository's metadata including name, description, number of stars, forks, and last update date. Use this when you need to understand a repository's popularity or activity.

Why it's better:
- Name is specific: Verb (get) + Resource (github_repository)
- Description explains what data is returned
- "Use when" clause guides agent decision-making
- Distinguishes from other GitHub tools (list_github_issues, create_github_issue, etc.)
```
</details>

---

## Tool 2: The Inconsistent Abbreviator

**Original**:
```
Name: create_pr
Description: Create pull request
```

**Your Task**:
- Spot the inconsistencies
- Rewrite to match module patterns
- Assume this creates a new GitHub pull request with a title, description, and target branch

**Hint**: What's inconsistent about the name? What's missing from the description?

<details>
<summary>Click to see our solution</summary>

```
Name: create_github_pull_request
Description: Create a new GitHub pull request. Provide a title, description, and target branch. Use this to propose code changes for review before merging to main.

Why it's better:
- Full spelling: "pull_request" not "pr" (consistency with other tools)
- Description clarifies what inputs are needed
- Explains the purpose (propose code changes for review)
- Consistent with our GitHub tool naming convention
```
</details>

---

## Tool 3: The Overloaded Manager

**Original**:
```
Name: manage_user
Description: Manage user account
```

**Your Task**:
- Identify why this name is bad (hint: overloaded)
- Split this into **two separate tools** with proper names and descriptions
- Assume: (1) reading user profile data, (2) updating user's Slack status

<details>
<summary>Click to see our solution</summary>

```
Tool 1:
Name: get_user_profile
Description: Retrieve a user's profile information including name, email, account creation date, and preferences. Use this when you need details about a specific user.

Tool 2:
Name: update_user_slack_status
Description: Update a user's Slack status and mood emoji. Returns confirmation of the new status. Use this to let team members know availability or current focus.

Why it's better:
- One tool = one job (specificity)
- Agent can now clearly choose which action it needs
- Less ambiguity, more confident tool selection
- Follows verb + resource pattern for both
```
</details>

---

## Tool 4: The Dangerous Understatement

**Original**:
```
Name: delete_thing
Description: Remove a GitHub issue
```

**Your Task**:
- Rewrite this to clearly communicate risk
- Use formatting (IRREVERSIBLE, etc.) to grab agent attention
- Remember: This is destructive, so agents should think twice

<details>
<summary>Click to see our solution</summary>

```
Name: delete_github_issue
Description: Permanently delete a GitHub issue (IRREVERSIBLE). The issue, all comments, and all associated data will be lost forever. Use only if absolutely certain—there is no undo. Requires explicit user confirmation before calling.

Why it's better:
- Clear verb + resource (delete_github_issue, not delete_thing)
- IRREVERSIBLE in all caps draws agent attention
- Explains consequences ("will be lost forever")
- Adds safety note ("Requires explicit user confirmation")
- Agent will only call this if user explicitly asks to delete
```
</details>

---

## Tool 5: The Assumption Maker

**Original**:
```
Name: get_list
Description: Get the list
```

**Your Task**:
- Rewrite assuming this retrieves a list of all Slack channels the user can access
- Include parameter descriptions
- Make it so an agent would clearly understand when to use it

<details>
<summary>Click to see our solution</summary>

```
Name: list_slack_channels
Description: List all Slack channels the current user can access. Returns channel name, ID, topic, and member count. Use this when you need to find a specific channel or see what channels are available.

Parameters:
- exclude_archived (optional): If true, exclude archived channels (default: true)

Why it's better:
- Specific verb (list) + specific resource (slack_channels)
- Describes output (name, ID, topic, member count)
- "Use when" clause explains why agent might need this
- Parameter is clearly named and has a sensible default
```
</details>

---

## Reflection

After completing all 5, answer these:

- [ ] **What patterns did you notice?** (e.g., all bad names lack verbs)
- [ ] **What's the most common mistake?** (Hint: It's vagueness)
- [ ] **Which rewrite was hardest?** Why?
- [ ] **Did naming a tool help you think about what it does?** (Powerful realization!)

---

## 🎯 Key Takeaways

By renaming these tools, you learned:

1. **Verbs matter** — list, get, create, delete, update tell the agent the action
2. **Specificity wins** — "github_issue" beats "thing"
3. **Consistency is trust** — use full words, not abbreviations
4. **Danger needs flags** — IRREVERSIBLE, destructive operations should be obvious
5. **One job per tool** — don't overload. Split when needed.

---

## ✅ Next Step

**[Continue to Module 3: Build from API](../03-build-from-api/README.md)**

You now have the skills to design tools. Module 3 teaches you how to build them.
