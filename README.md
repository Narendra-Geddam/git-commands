# Git All-in-One Handbook

This file contains **all Git commands**, their **flags**, and **real-world scenarios**.  
Important flags → detailed explanation.  
Non-important flags → short (5–10 words).  

---

## 📌 Section 1: Git Basics

### 1. `git init`
- **Command:**  
  ```bash
  git init
  ```
- **Flags:**  
  - `--bare` → Creates repo without working directory (for central repos).  
  - *(no flag)* → Normal repo with working directory.  
- **Use Case:** Start a new Git repository.  
- **Real Scenario:**  
  You’re starting a new project `portfolio-site` on your laptop, run `git init` to track it with Git.  

---

### 2. `git clone`
- **Command:**  
  ```bash
  git clone <repo_url>
  ```
- **Flags:**  
  - `--branch <name>` → Clone specific branch.  
  - `--depth <n>` → Shallow clone, last n commits.  
  - `--single-branch` → Clone only that branch.  
  - `--mirror` → Full copy incl. refs, tags, remotes.  
- **Use Case:** Copy a remote repository locally.  
- **Real Scenario:**  
  Developer joins team → clones `https://github.com/org/project.git` to begin work.  

---

### 3. `git status`
- **Command:**  
  ```bash
  git status
  ```
- **Flags:**  
  - `-s` / `--short` → Short format output.  
  - `-b` → Show branch info.  
- **Use Case:** Check repo changes.  
- **Real Scenario:**  
  Before commit → run `git status` to see staged/unstaged/untracked files.  

---

### 4. `git add`
- **Command:**  
  ```bash
  git add <file>
  ```
- **Flags:**  
  - `-A` → Add all files (tracked + untracked).  
  - `.` → Add current directory files.  
  - `-p` → Stage file in parts (interactive).  
  - `-n` → Dry run, shows what will be added.  
- **Use Case:** Stage files before committing.  
- **Real Scenario:**  
  Changed `index.html` & `style.css` → run `git add .` to stage both.  

---

### 5. `git commit`
- **Command:**  
  ```bash
  git commit -m "message"
  ```
- **Flags:**  
  - `-m "msg"` → Inline commit message.  
  - `-a` → Auto-stage modified files (not new).  
  - `--amend` → Modify last commit.  
  - `--allow-empty` → Create empty commit.  
- **Use Case:** Save staged changes to history.  
- **Real Scenario:**  
  After fixing bug in `login.js`, run:  
  ```bash
  git add login.js  
  git commit -m "Fix login validation bug"
  ```

---

### 6. `git config`
- **Command:**  
  ```bash
  git config [options]
  ```
- **Flags:**  
  - `--global` → Applies to user system-wide.  
  - `--local` → Only for current repo.  
  - `--system` → Applies to all users on system.  
  - `--list` → Show current config.  
- **Use Case:** Configure Git identity/settings.  
- **Real Scenario:**  
  Setup username/email once:  
  ```bash
  git config --global user.name "Narendra Geddam"
  git config --global user.email "narendra@example.com"
  ```

---

### 7. `git help`
- **Command:**  
  ```bash
  git help <command>
  ```
- **Flags:**  
  - `-a` → List all available commands.  
  - `-g` → List guides.  
- **Use Case:** Get help for commands.  
- **Real Scenario:**  
  If you forget syntax for `rebase`, run `git help rebase`.  

---

