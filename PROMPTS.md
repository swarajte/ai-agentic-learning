# Prompts that worked

Copy good Agent prompts here after each lab. The agent (and you) get better "memory" via this file.

## Template

```text
**Task:**
**Folder:** AI Agentic learning | MCP demo
**Result:** (what worked / what failed)
```

---

## Starters

### Study buddy (this folder)

```text
@BOOK_STUDY_PLAN.md
I'm on Chapter 3 (Skills). Explain the chapter in simple words for ops/Jenkins work, then give me one exercise using MCP demo.
```

### Lab 3 — first Jenkins MCP test (run in **MCP demo** folder)

See full setup: [notes/lab-03-mcp-jenkins.md](./notes/lab-03-mcp-jenkins.md)

```text
Non-prod only. Use the jenkins MCP server (not jenkins-prod).

1. List Jenkins jobs (name and last build color/status if available).
2. Tell me which MCP tools you called and what Jenkins URL/environment that maps to.
3. Do not trigger builds, change configs, or run SSH commands.
```

### Jenkins triage — Lab 3+ (MCP demo)

```text
Using Jenkins MCP on non-prod only: for job [JOB_NAME], last build status, last 40 lines of console, 3-bullet summary, suggested fix. Cite tool names used.
```
