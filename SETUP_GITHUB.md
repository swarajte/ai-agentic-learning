# Push this folder to GitHub (MobaXterm / Linux)

Run these in: `/home/mobaxterm/Desktop/CURSOR/AI Agentic learning`

## 1. One-time: install git + GitHub CLI (if missing)

```bash
# Debian/Ubuntu-style (adjust for your distro)
sudo apt update && sudo apt install -y git gh
gh auth login
```

## 2. Initialize repo and first commit

```bash
cd "/home/mobaxterm/Desktop/CURSOR/AI Agentic learning"

git init
git add .
git status   # PDF should NOT appear (gitignored)
git commit -m "Initial commit: Albada study plan, prompts, and notes structure"
```

## 3. Create GitHub repo and push

**Option A — GitHub CLI (easiest):**

```bash
gh repo create ai-agentic-learning --private --source=. --remote=origin --push
```

Use `--public` instead of `--private` if you want a public repo (still no PDF in git).

**Option B — GitHub website:**

1. https://github.com/new → name: `ai-agentic-learning` → **Private** recommended  
2. Do **not** add README (you already have one)

```bash
git remote add origin git@github.com:YOUR_USERNAME/ai-agentic-learning.git
git branch -M main
git push -u origin main
```

Replace `YOUR_USERNAME` with your GitHub handle.

## 4. Day-to-day workflow

```bash
git add BOOK_STUDY_PLAN.md PROMPTS.md notes/
git commit -m "Ch 1: completed intro lab and notes"
git push
```

## Including the PDF?

Default `.gitignore` excludes `*.pdf` (copyright + size). For a **private** repo only, you may remove `*.pdf` from `.gitignore` — only if your copy is licensed for personal use and you accept GitHub’s terms.

## SSH key for GitHub

If `git push` asks for a password, use SSH:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
cat ~/.ssh/id_ed25519.pub
```

Add the key at: https://github.com/settings/keys
