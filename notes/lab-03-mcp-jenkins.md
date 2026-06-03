# Lab 3 — MCP, wrapper, and Jenkins (study notes)

**Hands-on workspace:** `..\MCP demo` (open this folder in Cursor for the lab)  
**Live ops docs there:** `MCP demo\LAB3-NOTES.md`, `MCP demo\PROMPTS.md`  
**Book tie-in:** Chapter 1 preview + Chapter 4 (tools) — MCP = how agents get **skills**

---

## Lab 3 in simple words

1. **Setup** — Make sure Jenkins MCP (`jenkins`) is **green** in Cursor.  
2. **Test** — Ask the Agent to **list non-prod Jenkins jobs** using MCP tools (read-only).  
3. **Confirm** — Agent says it used `jenkins` MCP and URL `http://10.100.145.98:8080`.

You are proving: **Cursor → mcp.json → wrapper → Jenkins MCP → Jenkins API**.

---

## What you did in MCP demo (documented here)

| Topic | What it means |
|-------|----------------|
| `mcp.json` | Recipe Cursor uses to **start** MCP servers (not MCP itself) |
| `mcp-wrapper.ps1` | Decodes `b64:` secrets in memory, then runs `npx` + MCP package |
| `@kud/mcp-jenkins@latest` | Current Jenkins MCP npm package (works) |
| Old package | `@modelcontextprotocol/server-jenkins` — **broken**, do not use |
| Env vars | `MCP_JENKINS_URL`, `MCP_JENKINS_USER`, `MCP_JENKINS_API_TOKEN` |
| Global config | `C:\Users\swarajt\.cursor\mcp.json` — **main file Cursor loads** |
| Project copy | `MCP demo\mcp.json` — keep in sync with global |

---

## MCP server map (your environments)

| MCP server name | Environment | URL / host |
|-----------------|-------------|------------|
| `jenkins` | **Non-prod (Lab 3 default)** | `http://10.100.145.98:8080` |
| `jenkins-dr` | DR | `http://10.100.130.153:8080` |
| `jenkins-prod` | PROD | `http://10.100.2.216:8080` |
| `ssh-ilceusc097` | SSH jump | `10.234.182.125` (`tooladm`) |
| `SSH_to_Remote_Server_2_0_0` | SSH (Python, Downloads) | Separate project — **not** Lab 3 |

**Lab 3 rule:** use **`jenkins`** only (not `jenkins-prod` unless you explicitly say prod).

---

## End-to-end flow

```text
Cursor reads C:\Users\swarajt\.cursor\mcp.json
        |
        v
powershell -File "...\MCP demo\mcp-wrapper.ps1"  npx  --yes  @kud/mcp-jenkins@latest
        |
        v
wrapper decodes b64: MCP_JENKINS_URL / USER / API_TOKEN
        |
        v
npx starts Jenkins MCP server
        |
        v
MCP server calls Jenkins REST API (10.100.145.98:8080)
        |
        v
Tools appear in Settings -> Tools & MCP
        |
        v
Agent chat: "list jobs" -> Cursor calls MCP tools
```

**One sentence:** Cursor starts the wrapper, wrapper decodes credentials and runs `npx`, Jenkins MCP exposes tools, Agent uses those tools to talk to Jenkins.

---

## What is `mcp-wrapper.ps1`? (11 lines)

1. Find env vars starting with `b64:`  
2. Decode to plain text (URL, user, token) **in memory only**  
3. Run `npx` (or other command) with remaining args  

**Security:** Base64 is obfuscation, not encryption. Never commit real tokens to GitHub.

**Full path in config:**

`C:\Users\swarajt\Desktop\CURSOR\MCP demo\mcp-wrapper.ps1`

---

## Before running the Agent prompt

### 1) Edit the right file

Use **`C:\Users\swarajt\.cursor\mcp.json`** (global), not an old copy with:

- `@modelcontextprotocol/server-jenkins`
- `JENKINS_URL` (wrong — use `MCP_JENKINS_*`)

### 2) Reload MCP

`Ctrl+Shift+P` → **MCP: Reload Servers**  
Or **restart Cursor** (what you planned next).

### 3) Verify in UI

**Settings → Tools & MCP**

- Scroll — `jenkins` may be below `SSH_to_Remote_Server_2_0_0`
- Toggle **on** for `jenkins`, `jenkins-dr`, `ssh-ilceusc097` if needed
- **Green** = connected · **Red** = click server and read log

### 4) PowerShell smoke tests (optional)

```powershell
Test-Path "C:\Users\swarajt\Desktop\CURSOR\MCP demo\mcp-wrapper.ps1"
# Expect: True

$b64 = "aHR0cDovLzEwLjEwMC4xNDUuOTg6ODA4MA=="
[Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($b64))
# Expect: http://10.100.145.98:8080

cd "C:\Users\swarajt\Desktop\CURSOR\MCP demo"
powershell -ExecutionPolicy Bypass -File .\mcp-wrapper.ps1 npx --version
```

### 5) VPN

Connect office VPN if required to reach `10.100.145.98:8080`.

---

## Lab 3 Agent prompt (copy in MCP demo)

Open folder **`MCP demo`** → **Agent** chat → paste:

```text
Non-prod only. Use the jenkins MCP server (not jenkins-prod).

1. List Jenkins jobs (name and last build color/status if available).
2. Tell me which MCP tools you called and what Jenkins URL/environment that maps to.
3. Do not trigger builds, change configs, or run SSH commands.

If Jenkins MCP is not connected, tell me how to check Cursor MCP settings.
```

**Expected:** server `jenkins` → `http://10.100.145.98:8080` (non-prod).

---

## Lab 3+ (after list works)

```text
Non-prod only. Use jenkins MCP.

Job: [PASTE_JOB_NAME]

1. Last build number and result
2. Last 50 lines of console log
3. Three-bullet summary in plain English
4. Suggested read-only checks (disk, connectivity) — do not run SSH unless I say yes
5. List MCP tools used

Do not trigger builds or change anything.
```

Full prompts also in: `..\MCP demo\PROMPTS.md`

---

## Jenkins aliases (memory — from Lab 2)

| You say | MCP server |
|---------|------------|
| non-prod / nonprod jenkins | `jenkins` |
| DR jenkins | `jenkins-dr` |
| prod jenkins | `jenkins-prod` (only when you say **prod**) |

---

## Guardrails (all MCP ops)

- Read-only triage unless you ask to change something  
- No builds, bounces, or config writes without approval  
- Prod: read-only triage; **you** run destructive actions  
- Agent must explain commands and impact before risky/prod steps  

---

## Troubleshooting

| Symptom | Check |
|---------|--------|
| Only `SSH_to_Remote_Server` in UI | Scroll down; reload MCP; edit **global** `mcp.json` |
| No `jenkins` at all | Global config missing jenkins block; restart Cursor |
| `jenkins` red | MCP server log; npm/network; token; VPN |
| Wrapper not found | Full path to `mcp-wrapper.ps1` in global JSON |
| Agent only talks, no tools | Ask: "Use jenkins MCP tools now"; confirm **Agent** mode |
| PROD Jenkins fails from laptop | Often expected; use UI or jump host |

---

## Lab 3 checklist (tick after restart)

- [x] Restarted Cursor (or MCP: Reload Servers)
- [x] **Tools & MCP** shows `jenkins`, `jenkins-dr`, `jenkins-prod`, `ssh-ilceusc097`, `SSH_to_Remote_Server_2_0_0`
- [x] `jenkins` is **green** (38 tools enabled)
- [x] Ran Lab 3 prompt in **MCP demo** Agent chat
- [x] Got job list + MCP tools named + non-prod URL
- [x] Updated **My results** below
- [x] `git commit` this file to study repo (pending your push)

---

## My results — Lab 3 PASS (2026-05-27)

**Summary:** Jenkins MCP connected. **62 jobs** listed on non-prod. Tools documented. Last-build **color/status** needs `jenkins_get_job_status` per job (one MCP call each).

### MCP tools used

| # | MCP server | Tool | Purpose |
|---|------------|------|---------|
| 1 | `jenkins` | `jenkins_list_instances` | Confirm Jenkins URL |
| 2 | `jenkins` | `jenkins_list_jobs` | List all job names (62 jobs) |
| 3 | `jenkins` | `jenkins_get_job_status` | Last build result (sample jobs only) |
| 4 | `jenkins` | `jenkins_list_views` | Views + jobs (no color in response) |

**Not used:** `jenkins-prod`, `jenkins-dr`, SSH tools, `jenkins_trigger_build`, config/write tools.

### Environment mapping

| Item | Value |
|------|--------|
| MCP server | `jenkins` |
| Jenkins URL | `http://10.100.145.98:8080` |
| Environment | Non-prod (`mwhlvuscdoc301`) |
| Hostname in job URLs | `http://mwhlvuscdoc301:8080/...` (same instance) |

**Note:** `jenkins_list_jobs` returns name + URL only, not ball color. Status = `jenkins_get_job_status` (one call per job).

### Sample build status (this run)

| Job | Last build | Result | Ball |
|-----|------------|--------|------|
| AME_BOUNCE | #12 | SUCCESS | Blue |
| AEM_CONTENT_DEPLOYMENT | #5 | SUCCESS | Blue |

Additional status calls were skipped in UI; other jobs have no status in this run.

### Ops-relevant jobs (from workspace notes)

`AME_BOUNCE`, `AME_SSH_TEST`, `DXPPTE_RPEAR_SYNC`, `J-Boot`, `DeploySSOHF`, `DIGITAL_CREATE_AEM_HOTFIX`, `DIGITAL_INSTALL_SSO_PACKAGE`

### Jenkins views (3)

| View | Jobs |
|------|------|
| `all` | 62 |
| Digital Automations | 21 |
| Digital Checklist | 11 |

### Full job list (62) — non-prod

<details>
<summary>Click to expand all job names</summary>

1. AEM_CONTENT_DEPLOYMENT  
2. AME_BOUNCE  
3. AME_SSH_TEST  
4. CB-Operations  
5. Change_Logical_date  
6. Couchbase_Cache_clean_job  
7. demo  
8. DeployDPHF  
9. DeploySSOHF  
10. DIGITAL_AEM_PACKAGES_FROM_HF_ORDER_CHECKER  
11. DIGITAL_AEM_ZIPS_HF_ORDER_CHECKER  
12. DIGITAL_CHECK_BACKUP_LOGS  
13. DIGITAL_CHECK_PRODUCTS_POST_EPC  
14. DIGITAL_CHECKLIST  
15. DIGITAL_CHECKLIST_AEM_PACKAGES_ORDER_CHECKER  
16. DIGITAL_CHECKLIST_AEM_VERSION_CHECKER  
17. DIGITAL_CHECKLIST_AEM_ZIPS_CHECKER  
18. DIGITAL_CHECKLIST_BV_INCOMING_CHECKER  
19. DIGITAL_CHECKLIST_COUCHBASE_CHECKER  
20. DIGITAL_CHECKLIST_FEEDS_CHECKER  
21. DIGITAL_CHECKLIST_MOBILE_APP_CHECKER  
22. DIGITAL_CHECKLIST_SOLR_CHECKER  
23. DIGITAL_CHECKLIST_STOCK_CHECKER  
24. DIGITAL_CHECKLIST_UXF_CHECKER  
25. DIGITAL_CLEAN_FILESYSTEM  
26. DIGITAL_CLEAN_OLD_JENKINS_CONTENT  
27. DIGITAL_CONTENT_SYNC  
28. DIGITAL_CREATE_AEM_HOTFIX  
29. DIGITAL_DAILY_HF_DOWNLOAD_PACKAGES  
30. DIGITAL_DAILY_HF_DOWNLOAD_PACKAGES_MULTIPLE_ENV  
31. DIGITAL_DEPLOY_AEM_CATALOG  
32. DIGITAL_EXPORT_CONTENT  
33. DIGITAL_INSTALL_LATEST_CONTENT  
34. DIGITAL_INSTALL_LATEST_CONTENT_PTE  
35. DIGITAL_INSTALL_SSO_PACKAGE  
36. DIGITAL_LIST_AEM_PACKAGES_FROM_HF  
37. DIGITAL_TRIGGER_PREPAID_BACKUP  
38. DIGITAL_UPDATE_INVENTORY  
39. DIGITAL_UPDATE_INVENTORY_AEM  
40. DIGITAL_UPDATE_INVENTORY_NOV  
41. DR_AEM_UPDATE_MAPPING_URL_FOR_CONTENT_PAGES  
42. DR_Enable_Disable_Splash_Page  
43. DXP_Ping_Switch  
44. DXPPTE_RPEAR_SYNC  
45. Enable_Disable_Splash_Page  
46. FS_Monitor  
47. inventory-notification-request_rest  
48. J-Boot  
49. J-Boot_Testing  
50. NON_PROD_AEM_UPDATE_MAPPING_URL_AND_DISP_CLEANUP  
51. NON_PROD_DEVICE_LIST_CHECK  
52. Pipeline J-Boot_BKP  
53. PROD_AEM_UPDATE_MAPPING_URL_FOR_CONTENT_PAGES  
54. Promotion_details_rest  
55. PTE_AEM_UPDATE_MAPPING_URL_FOR_CONTENT_PAGES  
56. PTE_DISP_CACHE_CLEAN  
57. PTE_Enable_Disable_Splash_Page  
58. SIT_AEM_UPDATE_MAPPING_URL_FOR_CONTENT_PAGES  
59. Solr_Indexing_Couchbase_Cache_Clean_PTE  
60. SolrIndexing  
61. WARMUP_PTE_NONPROD  

</details>

### Lab 3+ prompt — full status for all jobs

```text
Non-prod only. Use jenkins MCP. For each job from jenkins_list_jobs, call jenkins_get_job_status and return a table: job name, last build #, result, and Jenkins ball color (blue/red/yellow/grey). Do not trigger builds or change anything.
```

When Cursor asks to approve MCP tools, **Allow** (or auto-approve `jenkins_get_job_status`) so all 62 status calls can run.

### Notes

- **Lab 3 pass:** MCP connected, correct non-prod URL, 62 jobs listed, tools documented.  
- For a **complete status column**, re-run with approvals for `jenkins_get_job_status`.

---

## Links

| Resource | Path |
|----------|------|
| MCP demo Lab 3 guide | `..\MCP demo\LAB3-NOTES.md` |
| Ops prompts | `..\MCP demo\PROMPTS.md` |
| Lab 2 design | [lab-02-agent-design.md](./lab-02-agent-design.md) |
| Chapter 1 notes | [ch-01-intro.md](./ch-01-intro.md) |
| Global MCP config | `C:\Users\swarajt\.cursor\mcp.json` |

---

## Git (study repo)

```bash
cd "/home/mobaxterm/Desktop/CURSOR/AI Agentic learning"
git add notes/lab-03-mcp-jenkins.md notes/ch-01-intro.md README.md
git commit -m "Ch 1 Lab 3: MCP Jenkins setup notes from MCP demo"
git push
```
