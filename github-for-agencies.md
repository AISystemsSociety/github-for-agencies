# GitHub for Service Businesses & Agencies

A practical guide to using GitHub as your deployment and version control system. Covers setup, daily workflow, folder structure, auto-saving, client project management, and GitHub Pages deployment.

No prior Git experience required. Written for service business owners who build websites, dashboards, tools, and client deliverables.

From the AI Systems Society - https://www.skool.com/aisystems

---

## Why GitHub?

You are building HTML files, dashboards, pitch decks, landing pages, client portals, and internal tools. Right now they probably live in random folders on your laptop. GitHub solves three problems:

1. **Version control** - every change is saved with a timestamp. You can undo anything, see what changed, and never lose work.
2. **Free hosting** - GitHub Pages turns any repo into a live website at `yourusername.github.io/repo-name`. Free. No server needed.
3. **Collaboration** - share repos with team members, freelancers, or clients. They can see the work, suggest changes, or just view the live site.

---

## Initial Setup (One Time)

### 1. Create a GitHub Account
Go to https://github.com/signup. Use your business email. Pick a professional username - clients might see this.

**Username tips:**
- `yourname` or `youragency` - clean and professional
- Avoid numbers, underscores, or joke names
- This becomes your domain: `yourusername.github.io`

### 2. Install Git

**Mac:**
```bash
# Open Terminal and run:
xcode-select --install
```

**Windows:**
Download from https://git-scm.com/download/win and run the installer. Use all default settings.

**Verify it worked:**
```bash
git --version
# Should show something like: git version 2.43.0
```

### 3. Install GitHub CLI (gh)

The GitHub CLI lets you create repos, deploy pages, and manage everything from your terminal.

**Mac:**
```bash
brew install gh
```

**Windows:**
```bash
winget install GitHub.cli
```

**Then authenticate:**
```bash
gh auth login
# Choose: GitHub.com > HTTPS > Login with a web browser
# It opens a browser, you log in, done.
```

### 4. Configure Git Identity

```bash
git config --global user.name "Your Name"
git config --global user.email "you@yourbusiness.com"
```

This tags your commits with your name. Do this once.

---

## Core Concepts (60-Second Version)

| Term | What It Means |
|------|--------------|
| **Repo** | A project folder tracked by Git. One repo per project. |
| **Commit** | A saved snapshot. Like "Save As" but it keeps every version. |
| **Push** | Upload your commits to GitHub (the cloud). |
| **Pull** | Download changes from GitHub to your computer. |
| **Branch** | A parallel version of your project. Useful for testing changes without breaking the live version. |
| **Clone** | Download a repo from GitHub to your computer for the first time. |
| **GitHub Pages** | Free hosting that turns a repo into a live website. |

---

## Daily Workflow

### Starting a New Project

```bash
# 1. Create the project folder
mkdir my-project
cd my-project

# 2. Initialize Git
git init

# 3. Create your files (or copy them in)
# ... build your HTML, add assets, etc.

# 4. Stage all files
git add .

# 5. Commit with a message
git commit -m "Initial build - client dashboard v1"

# 6. Create the GitHub repo and push (one command)
gh repo create your-username/my-project --public --source=. --push

# Done. Your code is on GitHub.
```

### Making Changes to an Existing Project

```bash
# 1. Make your changes (edit files, add images, etc.)

# 2. See what changed
git status

# 3. Stage the changes
git add .

# 4. Commit with a description of what you changed
git commit -m "Updated pricing section, fixed mobile layout"

# 5. Push to GitHub
git push
```

### The Golden Rule
**Commit early, commit often.** Every time you finish a piece of work - a section, a fix, a feature - commit it. Small commits are easy to understand and easy to undo. One giant commit at the end of the day is hard to review and impossible to partially undo.

---

## Folder Structure for Agencies

### Option A: One Repo Per Project (Recommended)

```
GitHub Account
  |
  |-- client-name-growth-plan/        # Client pitch deck
  |     |-- index.html
  |     |-- assets/
  |     |     |-- logo.png
  |     |     |-- screenshot-1.png
  |     |-- .gitignore
  |
  |-- client-name-portal/             # Client portal / wiki
  |     |-- index.html
  |     |-- assets/
  |     |-- pages/
  |
  |-- internal-dashboard/             # Your internal tool
  |     |-- index.html
  |     |-- assets/
  |
  |-- agency-website/                 # Your public website
        |-- index.html
        |-- about.html
        |-- assets/
```

**Why one repo per project:**
- Each project gets its own GitHub Pages URL
- You can make some repos public and others private
- Clean separation of client work
- Easy to share a single project without exposing everything else

### Option B: Monorepo (All Clients in One Repo)

```
agency-projects/
  |-- clients/
  |     |-- client-a/
  |     |-- client-b/
  |-- internal/
  |     |-- dashboard/
  |     |-- tools/
  |-- templates/
```

**When to use this:** Only if you are NOT using GitHub Pages for individual projects. A monorepo can only have one Pages site.

### Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Client pitch deck | `client-growth-plan` | `acme-growth-plan` |
| Client portal | `client-portal` | `acme-portal` |
| Internal tool | `descriptive-name` | `deliverability-monitor` |
| Template | `template-type` | `growth-plan-template` |
| Public resource | `descriptive-name` | `podcast-guest-research` |

Rules:
- All lowercase
- Hyphens between words (never underscores or spaces)
- Client name first for client projects
- No abbreviations that only you understand

---

## GitHub Pages (Free Hosting)

Turn any repo into a live website in 30 seconds.

### Enable Pages on a Repo

```bash
# After pushing your repo:
gh api repos/YOUR-USERNAME/REPO-NAME/pages -X POST \
  -f "source[branch]=main" -f "source[path]=/"
```

Your site will be live at: `https://YOUR-USERNAME.github.io/REPO-NAME/`

### Custom Domains

If you own a domain, you can point it at your GitHub Pages site:

1. In your DNS provider, add a CNAME record:
   - Name: `projects` (or whatever subdomain you want)
   - Value: `YOUR-USERNAME.github.io`

2. In the repo, create a file called `CNAME` containing just your domain:
   ```
   projects.yourdomain.com
   ```

3. Push it:
   ```bash
   echo "projects.yourdomain.com" > CNAME
   git add CNAME && git commit -m "Add custom domain" && git push
   ```

GitHub automatically provisions an SSL certificate. Give it 10-15 minutes.

---

## Password Protection (StatiCrypt)

For client deliverables you do not want publicly accessible. StatiCrypt encrypts your HTML with AES-256 and adds a password gate. The content is unreadable in source - not just hidden behind JavaScript.

### How to Encrypt

```bash
# 1. Build your page normally as index.html

# 2. Copy it to a temp location (StatiCrypt overwrites the input file)
cp index.html /tmp/to-encrypt.html

# 3. Encrypt it
cd /tmp && npx staticrypt@3.5.4 to-encrypt.html \
  -p "clientpassword" \
  -d enc-out \
  --short \
  --remember 30 \
  --template-color-primary "#7c3aed" \
  --template-color-secondary "#09090b"

# 4. Copy the encrypted version back
cp /tmp/enc-out/to-encrypt.html ./index.html

# 5. Keep your unencrypted source as a backup (DO NOT commit this)
cp index.html .src.html
echo ".src.html" >> .gitignore
```

### Rules
- Password is typically the client's first name, lowercase
- `--remember 30` means the browser remembers the password for 30 days
- Always keep `.src.html` as your editable source. Edit that, re-encrypt, commit.
- NEVER commit the unencrypted `.src.html` to GitHub
- NEVER run StatiCrypt from inside the repo directory (it can overwrite files unpredictably)
- Change `--template-color-primary` to your brand color

### To Edit a Protected Page Later
1. Edit `.src.html` (your local unencrypted copy)
2. Re-run the encrypt steps above
3. Commit the new encrypted `index.html`
4. Push

---

## .gitignore (What NOT to Commit)

Create a `.gitignore` file in every repo. This tells Git which files to skip.

```gitignore
# Secrets - NEVER commit these
.env
*.key
credentials.*
config.json

# Unencrypted source files
.src.html
.staticrypt.json

# OS files
.DS_Store
Thumbs.db

# Editor files
.vscode/
*.swp
*.swo

# Dependencies (if using npm)
node_modules/
```

### The #1 Rule of Git
**NEVER commit secrets.** No API keys, no passwords, no tokens, no credentials. Not even to private repos. Once a secret is in Git history, it is there forever (even if you delete the file later). If you accidentally commit a secret, consider it compromised and rotate it immediately.

Before every commit, scan for:
- `api_key`, `token`, `secret`, `password` in any file
- `.env` files
- Hardcoded URLs with credentials in them
- Anything that looks like `sk_live_...`, `pk_...`, `ghp_...`

---

## Working with a Team

### Give Someone Access to a Repo

```bash
# Add a collaborator (they get push access)
gh api repos/YOUR-USERNAME/REPO-NAME/collaborators/THEIR-USERNAME \
  -X PUT -f permission=push
```

### Read-Only Access
Make the repo public. They can see everything but cannot push changes. If you need private read-only access, use a GitHub Organization with team permissions.

### Organizations
If you have a team or a community, create a GitHub Organization:

1. Go to https://github.com/organizations/plan
2. Pick the Free plan
3. Name it (e.g., `your-agency-name`)
4. Create repos under the org instead of your personal account

**Why orgs matter:** When you share a repo link, people can click your username and see ALL your public repos. If you have client pitch decks with client names in the repo titles, that is a problem. An org keeps community/public repos separate from your client work.

---

## Auto-Deploy Workflow

The fastest workflow for building and deploying client deliverables:

```bash
# Build or edit your file
# ... make changes ...

# One-liner: stage, commit, push
git add . && git commit -m "Updated hero section" && git push

# If Pages is enabled, the site updates automatically in ~60 seconds
```

### Alias for Speed
Add this to your `~/.zshrc` or `~/.bashrc`:

```bash
alias deploy='git add . && git commit -m "update" && git push'
```

Now you just type `deploy` after any change. (Use proper commit messages for important changes - the alias is for rapid iteration.)

---

## Common Problems & Fixes

### "fatal: not a git repository"
You are not inside a Git project folder. Either `cd` to the right folder or run `git init` to start tracking.

### "error: failed to push some refs"
Someone else pushed changes (or you pushed from another computer). Pull first:
```bash
git pull --rebase && git push
```

### "Your branch is ahead of 'origin/main'"
You have local commits that have not been pushed. Just run:
```bash
git push
```

### "Changes not staged for commit"
You edited files but did not stage them. Run:
```bash
git add .
```

### Accidentally Committed a Secret
1. **Rotate the secret immediately.** Generate a new API key/token/password.
2. Remove it from the file and commit the fix.
3. The old secret is still in Git history. For private repos this is usually acceptable after rotation. For public repos, consider using `git filter-branch` or BFG Repo-Cleaner to scrub history.

### Want to Undo the Last Commit (Before Pushing)
```bash
git reset --soft HEAD~1
# Your changes are still there, just uncommitted
```

### Want to See What Changed
```bash
git diff              # Unstaged changes
git diff --staged     # Staged changes (ready to commit)
git log --oneline -10 # Last 10 commits
```

---

## Using GitHub with Claude Code / AI Tools

If you use Claude Code, Cursor, or similar AI coding tools:

1. **Let the AI handle Git.** Most AI tools can stage, commit, and push for you. Just say "commit and push this."
2. **Use `.gitignore` religiously.** AI tools can accidentally stage sensitive files. A good `.gitignore` prevents this.
3. **Review before pushing.** Always run `git diff` or `git status` before pushing AI-generated changes.
4. **One repo per project.** AI tools work better with focused repos than monorepos.

### Claude Code Specific
If you are using Claude Code (Anthropic's CLI tool):
- Claude can create repos, commit, and push directly
- Say "push this to GitHub" and it handles the git commands
- Use `CLAUDE.md` in your project root to give Claude context about the project
- Claude respects `.gitignore` - configure it before your first session

---

## Quick Reference Card

```
# NEW PROJECT
mkdir project && cd project && git init
# ... create files ...
git add . && git commit -m "Initial build"
gh repo create username/project --public --source=. --push

# DAILY WORKFLOW
git add . && git commit -m "Description of changes" && git push

# ENABLE GITHUB PAGES
gh api repos/username/project/pages -X POST -f "source[branch]=main" -f "source[path]=/"

# CHECK STATUS
git status          # What changed?
git log --oneline   # Recent commits
git remote -v       # Where does this push to?

# UNDO LAST COMMIT (before push)
git reset --soft HEAD~1

# CLONE AN EXISTING REPO
git clone https://github.com/username/repo.git
cd repo
```

---

## Checklist: Before You Push Any Project

- [ ] `.gitignore` exists and covers secrets, `.env`, `.src.html`, OS files
- [ ] No API keys, tokens, or passwords in any committed file
- [ ] Repo name is lowercase with hyphens
- [ ] Commit messages describe WHAT changed (not "update" or "fix")
- [ ] If client-facing: encrypted with StatiCrypt, `.src.html` is gitignored
- [ ] If public: check that no other public repos expose client names
- [ ] GitHub Pages enabled if this needs to be a live site
