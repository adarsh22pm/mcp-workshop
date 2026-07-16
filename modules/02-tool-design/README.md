# Module 2: Tool Design as UX

**Duration**: 60 minutes  
**Type**: Concept + Hands-on Exercise  
**Outcome**: Master tool naming and descriptions; design your first tool spec

---

## 📚 What You'll Learn

1. **The tool lifecycle** — discovery → description → invocation → results
2. **Tool naming patterns** — verbs, clarity, consistency
3. **Description best practices** — what agents need to know
4. **Edge cases** — when to create a tool vs. not to
5. **Common mistakes** — and how to avoid them

---

## 🚀 Start Here

**[→ Read the Lesson](lesson.md)** (~35 min read)

This lesson teaches you how to think like an agent. You'll learn:
- The tool lifecycle (how agents use tools step-by-step)
- Naming patterns that make agents pick your tool
- Descriptions that eliminate ambiguity
- How to spec a tool so agents use it correctly every time

**Key concept**: Every tool should answer three questions:
1. **What does it do?** (name)
2. **When should I use it?** (description)
3. **What do I pass in and get back?** (parameters and output)

---

## ✍️ Hands-On Exercise

**[→ Do the Exercise](exercise.md)** (~20 min hands-on)

You'll rename 5 poorly-designed tools and explain your choices.

**Example:**
```
BAD:  tool_function_v2
GOOD: list_github_issues

Explanation: The original name is vague and doesn't describe what it does. 
The new name uses a verb (list) and clearly states the resource (github_issues).
```

This exercise trains your eye for good naming. You'll use these skills in Modules 3 and 4.

---

## 📋 Tool Design Checklist

Before moving to Module 3, make sure every tool you design passes this checklist:

- [ ] **Name is clear**: Uses a verb and is specific (e.g., `list_issues`, not `get_data`)
- [ ] **Description is complete**: Explains what it does AND when to use it
- [ ] **Parameters are named**: Each parameter has a clear, specific name (not `data` or `params`)
- [ ] **Description matches name**: No contradictions
- [ ] **Destructive actions are marked**: If a tool deletes/modifies, the description makes this clear
- [ ] **Output is predictable**: The agent knows what it will get back

---

## 🎯 Checkpoint

Before moving to Module 3, you should be able to:

- [ ] Explain why agent UX is different from human UX
- [ ] Write a tool description that guides an agent's decision
- [ ] Identify 3+ naming patterns that work well
- [ ] Spot bad tool naming in real code
- [ ] Design a tool spec (name, description, inputs) from scratch

If you're unsure about any of these, re-read [lesson.md](lesson.md).

---

## 📖 Next Steps

After you've read the lesson and completed the exercise:

**[→ Continue to Module 3: Build from API](../03-build-from-api/README.md)**

Module 3 takes these tool design skills and applies them to a real API (GitHub). You'll build a working MCP server and see how good tool design drives agent behavior.

---

## 💡 Key Principles

1. **Clarity over cleverness** — agents prefer obvious names and descriptions
2. **Consistency** — name and description should match perfectly
3. **Completeness** — describe both what the tool does AND when to use it
4. **Safety** — destructive actions should be marked clearly
5. **Specificity** — avoid vague parameter names or descriptions
