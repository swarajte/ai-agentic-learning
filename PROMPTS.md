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

**Result (2026-05-27):** PASS — 62 jobs on `http://10.100.145.98:8080`. Tools: `jenkins_list_instances`, `jenkins_list_jobs`, `jenkins_get_job_status` (sample), `jenkins_list_views`. Details: [lab-03-mcp-jenkins.md](./notes/lab-03-mcp-jenkins.md#my-results--lab-3-pass-2026-05-27).

### Lab 3+ — full status table (all jobs)

```text
Non-prod only. Use jenkins MCP. For each job from jenkins_list_jobs, call jenkins_get_job_status and return a table: job name, last build #, result, and Jenkins ball color. Do not trigger builds or change anything.
```

### Jenkins triage — one failed job (MCP demo)

```text
Using Jenkins MCP on non-prod only: for job [JOB_NAME], last build status, last 40 lines of console, 3-bullet summary, suggested fix. Cite tool names used.
```
