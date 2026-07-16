# Module 1 Quiz: MCP Fundamentals

**Time**: ~10 minutes  
**Passing score**: 4 out of 5  
**Format**: Multiple choice

Read each question carefully. The correct answer is the **most complete and accurate** one.

---

## Question 1: What is the main problem MCP solves?

**A)** It lets AI agents access databases faster  
**B)** It's a standard interface so one tool works with all AI agents (Claude, ChatGPT, Cursor) without rebuilding for each  
**C)** It replaces REST APIs  
**D)** It encrypts data when agents make requests  

**Correct answer**: **B**

MCP solves the fragmentation problem. Before MCP, you'd build one integration for Claude, another for ChatGPT, another for Cursor. MCP is the standard that eliminates this.

---

## Question 2: How do AI agents discover which tools to use?

**A)** The agent reads the configuration file and picks randomly  
**B)** The agent reads tool names and descriptions and decides which tools match the user's request  
**C)** The agent asks the user which tool to use  
**D)** The agent calls all tools and picks the one with the fastest response  

**Correct answer**: **B**

Agents are smart. They read your tool's name and description to understand what it does, then decide if it matches the user's request. This is why naming and descriptions matter.

---

## Question 3: Why is a tool's description more important than its name?

**A)** Names are for humans, descriptions are for agents  
**B)** The name tells the agent what the tool is, but the description tells the agent **when to use it**  
**C)** Descriptions are always longer, so agents think they're better  
**D)** Descriptions are used for security, not discovery  

**Correct answer**: **B**

Both matter, but descriptions are where you guide the agent's decisions. A name like `list_issues` is clear, but a description like "List all open GitHub issues for a repository. Use this when you need to see what's currently broken" tells the agent exactly when to use it.

---

## Question 4: You have a tool called `delete_user_account` with description "Delete a user account (irreversible)". When a user asks "Show me how to delete my account", should Claude use this tool?

**A)** Yes, always. It's exactly what the user asked for.  
**B)** No, the user asked "how to delete", not "delete it". The tool's description makes clear it's a destructive action.  
**C)** Maybe, depending on how often it's used  
**D)** Only on Mondays  

**Correct answer**: **B**

Good tool descriptions tell agents when NOT to use them. A destructive tool should have a clear warning. Agents read these warnings and only use the tool if the user explicitly asks for the action, not just information about it.

---

## Question 5: MCP has three primitives. Which one is a "function that an agent can call"?

**A)** Resources  
**B)** Prompts  
**C)** Tools  
**D)** Endpoints  

**Correct answer**: **C**

- **Tools** = callable functions (what we focus on in this course)
- **Resources** = data the agent can read
- **Prompts** = templates or instructions

---

## Scoring

**5 correct**: Perfect! You understand the fundamentals. Move to Module 2.  
**4 correct**: Great! You've got the core concepts. Re-read the section for the one you missed, then continue.  
**3 or fewer**: Go back to [lesson.md](lesson.md) and re-read carefully. The quiz is testing core concepts you'll need for Module 2.

---

## Review Your Answers

Unsure about any answer? Here's where to re-read:

- **Q1**: [Section 1: What is MCP?](lesson.md#1-what-is-mcp)
- **Q2**: [Section 4: How Agents Discover Tools](lesson.md#4-how-agents-discover-and-use-mcp-servers)
- **Q3**: [Section 5: Tool Naming and Description](lesson.md#5-why-tool-naming-and-description-matter)
- **Q4**: [Section 6, Example 3: When NOT to Use](lesson.md#example-3-when-not-to-use-a-tool)
- **Q5**: [Section 3: MCP Primitives](lesson.md#3-the-three-mcp-primitives)

---

## 🎯 Next Steps

- **Passed?** Great! [Continue to Module 2: Tool Design](../02-tool-design/README.md)
- **Need to review?** Go back to [lesson.md](lesson.md), re-read, and come back to retake the quiz.

Remember: There's no penalty for retaking quizzes. The goal is understanding, not rushing.
