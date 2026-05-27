# Study plan — *Building Applications with AI Agents*

**Book:** Michael Albada, O'Reilly (2025)  
**PDF in this folder:** `building-applications-with-ai-agents-designing-and-implementing-multiagent-systems-1_compress 1.pdf`  
**Hands-on project:** `..\MCP demo` (Jenkins + SSH MCP)

---

## How to use this with Cursor

1. Open **this folder** in Cursor (`AI Agentic learning`).
2. In **Agent** chat, type `@` and attach the PDF (or a chapter) when you want book-grounded help.
3. After each chapter, run the **Cursor lab** below and check the box.
4. For real tools, also open `MCP demo` in another window or switch folders.

---

## Chapter checklist

| Done | Ch | Topic | Cursor lab (copy into Agent) |
|:----:|---|-------|------------------------------|
| ☐ | 1 | Introduction to Agents | `Explain Ch 1 in 5 bullets for a Jenkins/ops engineer. Then: say hello and list files in this folder.` |
| ☐ | 2 | Designing Agent Systems | `From @BOOK_STUDY_PLAN.md and this project, sketch goal / tools / guardrails for a "failed build triage" agent.` |
| ☐ | 3 | Skills | Open `MCP demo`. `List MCP tools (Jenkins + SSH). Call one: list non-prod Jenkins jobs OR run hostname via SSH.` |
| ☐ | 4 | Orchestration | In `MCP demo`: `Last failed job on non-prod → last 40 console lines → 3-bullet summary → suggested fix.` |
| ☐ | 5 | Memory | Add 3 prompts that worked to `PROMPTS.md`. Rule: agent must cite job name + MCP server used. |
| ☐ | 6 | Learning from experience | After a wrong answer, update `.cursor/rules` or `PROMPTS.md` with the correction. |
| ☐ | 7 | From one agent to many | Same task, three roles in one chat: Analyst (logs) → Ops (SSH checks) → Scribe (runbook). |
| ☐ | 8 | Measurement & validation | `Quote exact log lines that prove the root cause. What would you check to verify the fix?` |
| ☐ | 9 | Monitoring in production | List what you'd log/alert on if this agent ran daily (jobs checked, errors, latency). |
| ☐ | 10 | Protecting agent systems | Review `MCP demo` prod rules: what must never be auto-run? Draft a 5-line safety checklist. |
| ☐ | 11 | Your new teammate | Pick one daily task (e.g. morning failed-build scan). Use the same prompt 5 days; refine once. |
| ☐ | 12 | Humans and agents | Document: agent proposes / human runs for prod. Template in `RUNBOOK_TEMPLATE.md`. |
| ☐ | 13 | Ethics & accountability | Who is accountable if agent misreads a log? Write 5 team norms for agent use. |

---

## Book terms → your stack

| Book | Simple | Your example |
|------|--------|--------------|
| Skill | One thing the agent can do | Jenkins MCP, SSH MCP, a shell script |
| API skill | Skill via HTTP/API | Jenkins REST via MCP |
| Local skill | Runs on your machine | `mcp-wrapper.ps1`, `.sh` / `.ksh` scripts |
| Orchestration | Multi-step workflow | Failed job → log → summary → fix |
| Memory | Past context | Chat, `@` files, rules, this repo |
| Multi-agent | Several specialists | Analyst + Ops + Scribe (roles in prompts) |
| Guardrails | Safety limits | Non-prod first; no auto prod bounce |

---

## Week-by-week (if you have ~30 min/day)

| Week | Chapters | Focus |
|------|----------|--------|
| 1 | 1–3 | What agents are + **skills** = MCP |
| 2 | 4–6 | Orchestration + memory + improving prompts |
| 3 | 7–10 | Multi-agent thinking + measure + protect |
| 4 | 11–13 | Daily use + human-in-the-loop + team norms |

---

## Hello world (do today)

**In this folder (no MCP):**

```
You are my study buddy for the Albada agent book.
Say hello, list files here, and tell me which chapter I should read first for a Jenkins/ops background.
```

**In MCP demo (real agent + skills):**

```
List Jenkins jobs on non-prod and explain which MCP tools you used. Do not touch prod.
```

---

## Files to add as you go

- `PROMPTS.md` — copy/paste prompts that worked
- `notes/ch-01-intro.md` — per-chapter notes
- `RUNBOOK_TEMPLATE.md` — after Ch 12

## Git / GitHub

Progress is tracked in this repo. See [SETUP_GITHUB.md](./SETUP_GITHUB.md) for push instructions. Update the progress table in [README.md](./README.md) when you finish a chapter.

---

## Rename tip (optional)

Shorter PDF name helps `@` mentions in Cursor:

`albada-building-ai-agents.pdf`
