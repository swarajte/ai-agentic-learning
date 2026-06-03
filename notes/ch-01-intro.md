# Chapter 1 — Introduction to Agents

**Book:** Michael Albada, *Building Applications with AI Agents* (O'Reilly)  
**Read in PDF:** Chapter 1 (starts ~page 23 in most editions)  
**Your focus:** Ops / Jenkins / infra — map everything to “who does the work when things break”

---

## 1. What is an AI agent? (one sentence)

An **agent** is software that can **decide what to do next**, use **tools** (APIs, SSH, scripts), and keep going until a **goal** is met — not just answer one chat message.

**Book test:** Does it make real decisions, or only follow a fixed script? Scripts = workflow. Flexible planning + tools = agent.

---

## 2. Agentic system (bigger box)

The book uses **agentic system** for everything around the agent:

| Piece | Plain English | Your world |
|-------|---------------|------------|
| **Foundation model** | The “brain” (LLM) | Composer / Claude in Cursor |
| **Tools** | Things it can call | Jenkins MCP, SSH MCP, terminal |
| **Memory** | What it remembers | Chat, `@` files, rules, this repo |
| **Orchestration** | Order of steps | “Get job → read log → summarize” |
| **Infrastructure** | Where it runs | Cursor, MobaXterm, GitHub |

You already have a **partial agentic system** in `MCP demo` + Cursor.

---

## 3. Pretraining revolution (why agents are possible now)

**Old ML:** collect data → train model → deploy (months, specialists).

**Now:** one pretrained model + **prompt** + **tools** → useful app in hours.

For you: you don’t train a model to read Jenkins logs — you connect Jenkins MCP and let the agent interpret logs.

---

## 4. Types of agents (pick your lane)

| Type | What it does | Ops example |
|------|----------------|-------------|
| **Business-task** | Fixed steps, little reasoning | Scheduled Jenkins job, RPA |
| **Conversational** | Chat Q&A | “What does this error mean?” |
| **Research** | Find + summarize | Summarize release notes |
| **Analytics** | Data → insight | “Which jobs failed most this week?” |
| **Developer** | Code / IDE | **Cursor Agent** |
| **Domain-specific** | Expert in one field | AME/WebLogic runbooks |
| **Browser-using** | Acts on websites | Jenkins UI (MCP API is better for you) |

**Closest to your job:** mix of **IT help desk agent** + **SOC analyst agent** (book examples) — triage alerts, logs, escalate to human.

---

## 5. Workflows vs agents (most important for ops)

| Approach | When to use | Jenkins / ops example |
|----------|-------------|------------------------|
| **Plain script** | Input always same format, must be fast/auditable | `grep ERROR build.log` |
| **Workflow** | Known steps, branches, human checkpoints | Pipeline: build → test → deploy |
| **RAG / chatbot** | Q&A over docs, no many API steps | “What’s in the runbook?” |
| **Agent** | Messy input, many steps, must choose tools | “Why did prod deploy fail? investigate.” |

**Rule of thumb:** start with a **script or pipeline**; add an **agent** when steps change every time and you’re tired of updating the script.

---

## 6. Sync vs async

- **Sync:** step 2 waits for step 1 (classic shell script).
- **Async / agent:** many things in parallel, reprioritize (e.g. check Jenkins + SSH + docs at once).

Book idea: humans become **managers** — review agent output, don’t retype every command.

---

## 7. Principles (Chapter 1 checklist)

When you build agents at work, aim for:

1. **Scalability** — don’t break when 10 failed jobs become 100  
2. **Modularity** — MCP tools separate from prompts (like your `mcp.json`)  
3. **Continuous learning** — save good prompts in `PROMPTS.md`, fix rules when wrong  
4. **Resilience** — retries, fallbacks if Jenkins API is down  
5. **Future-proofing** — prefer open patterns (MCP) over one-vendor lock-in  

---

## 8. Frameworks (names only for now)

Book mentions: **LangGraph**, **AutoGen**, **CrewAI**, **OpenAI Agents SDK**.  
**Cursor** is your “developer agent” environment today; Chapter 2+ goes deeper on design.

---

## 9. MCP (preview — full chapter later)

Chapter 1 mentions **Model Context Protocol (MCP)** — standard way for agents to use remote tools.  
Your `MCP demo` = Jenkins + SSH as **skills** the agent can call. (Book covers MCP in more detail in the tools chapter.)

---

## Key vocabulary

| Term | Remember as |
|------|-------------|
| Agent | Brain + tools + loop |
| Skill / tool | One capability (list jobs, SSH, run script) |
| Orchestration | Multi-step plan |
| Agency | How “free” the agent is to choose steps |
| Foundation model | LLM |

---

## Labs (do in order)

### Lab 1 — Cursor hello (this folder) ✅ start here

Open `AI Agentic learning` in Cursor → **Agent** chat:

```text
I'm studying Chapter 1 of the Albada agent book (Introduction to Agents).
Explain in 5 simple bullets for a Jenkins/ops engineer.
Then list files in this folder and suggest one sentence I should add to notes/ch-01-intro.md.
```

### Lab 2 — Draw your agentic system ✅

See full design: **[lab-02-agent-design.md](./lab-02-agent-design.md)**

Quick summary:

| Piece | Your design |
|-------|-------------|
| **Goal** | Triage failed **non-prod** Jenkins builds; summarize cause + suggest next steps |
| **Brain** | Cursor Agent + Composer (you prompt, you approve) |
| **Tools** | `jenkins` MCP, `jenkins-dr` (read-only triage), `ssh-ilceusc097` MCP, local shell |
| **Memory** | Chat, `@` rules in `MCP demo`, `PROMPTS.md`, this repo |
| **Orchestration** | You or agent: job status → console tail → (optional) SSH check → summary |
| **Guardrails** | No `jenkins-prod` for auto-triage; no build triggers / bounces without explicit ask; human runs prod |

### Lab 3 — MCP preview (after Labs 1–2)

**Study notes (this repo):** [lab-03-mcp-jenkins.md](./lab-03-mcp-jenkins.md)  
**Hands-on in:** `../MCP demo` — `LAB3-NOTES.md`, `PROMPTS.md`

1. Restart Cursor → **Settings → Tools & MCP** → `jenkins` **green**  
2. Open **MCP demo** → Agent → prompt from [lab-03-mcp-jenkins.md](./lab-03-mcp-jenkins.md) or `MCP demo/PROMPTS.md`

---

## My notes (edit below)

**Date started:** 2026-05-27  

**One thing that clicked:**

An agent is not “smarter Jenkins” — it’s **brain + tools + loop**. My Jenkins pipelines are **workflows** (fixed stages). An agent is for when the path isn’t known upfront: “build failed, figure out if it’s disk, dependency, or config — then suggest next steps.” I already have the **tools** side via MCP (Jenkins API, SSH); Chapter 1 made me see Cursor as the **orchestrator**.

**One question for later:**

When does a failed-build triage cross the line from “good prompt + MCP” to needing a separate framework (LangGraph, etc.)? Chapter 2 might answer that.

**Ops use case I want to automate first:**

**Non-prod Jenkins failed-build triage:** After a red build, agent uses Jenkins MCP to get job name, build number, and last ~50 lines of console; summarizes root cause in plain English; suggests fix or rollback steps. Human still runs prod actions. Example: deploy job `ame-bounce-nonprod` fails at “SSH connection refused” — agent points to jump host / credential, not a generic “check logs.”

---

## Lab 1 — five bullets (Jenkins/ops)

1. **Agent vs script/pipeline** — Pipelines and shell scripts are best when steps are fixed and auditable. Use an **agent** when every incident looks different (messy logs, unknown root cause) and it must **choose** what to check next (Jenkins API, SSH, runbook).

2. **Agentic system** — Not just chat: **LLM + tools + memory + orchestration**. For you: Cursor + Jenkins/SSH MCP + rules (“non-prod only”) + this repo’s notes.

3. **Agency is a spectrum** — True agent = real decisions; many “agents” are just scripts with a chat UI. Test: “Can it pick a different tool if the first check fails?”

4. **Workflows vs agents (Table 1-2)** — Keep **grep/Ansible/Jenkinsfile** for predictable work. Add an agent for **triage and investigation** (parallel checks, summarize, draft runbook) — you review, you execute on prod.

5. **Ops takeaway** — You become a **manager**: agent drafts commands and interpretation; you approve prod. **MCP** (Chapter 1 preview) is how tools plug in — same idea as your `MCP demo` Jenkins + SSH servers.

---

## Done?

- [ ] Read Chapter 1 in PDF  
- [x] Lab 1 in Cursor  
- [x] Lab 2 filled in — [lab-02-agent-design.md](./lab-02-agent-design.md)  
- [ ] Lab 3 (MCP demo) — [lab-03-mcp-jenkins.md](./lab-03-mcp-jenkins.md)  
- [ ] `git add notes/ch-01-intro.md` → commit → push  
