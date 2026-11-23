# Git Exercise — Answers

All commands below were run for real (Git Bash on Windows, Git 2.53). Outputs are trimmed copies of the actual terminal output.

---

## Part 1: Setup & config

1. Git was already installed.
2. Check the version:
   ```bash
   git --version
   # git version 2.53.0.windows.2
   ```
3. Configure username and email:
   ```bash
   git config --global user.name "Mathis"
   git config --global user.email "your-email@example.com"
   ```

---

## Part 2: Create your first repository

| Task | Command |
|---|---|
| Create a folder `my-first-repo` | `mkdir my-first-repo` |
| Navigate into it | `cd my-first-repo` |
| Initialize a Git repository | `git init` |
| Create `readme.txt` | `touch readme.txt` |

> Note: I also ran `git branch -M main` so the default branch is called `main`.

---

## Part 3: Your first commit

1. Show the current state of the repository: `git status`
   ```
   On branch main
   No commits yet
   Untracked files:
           readme.txt
   ```
2. Stage `readme.txt`: `git add readme.txt`
3. Commit: `git commit -m "Add readme file"`
   ```
   [main (root-commit) db5de75] Add readme file
    1 file changed, 1 insertion(+)
   ```
4. Show commit history: `git log`

---

## Part 4: Make changes

1. Added a line:
   ```bash
   echo "Learning Git step by step." >> readme.txt
   ```
2. `git status` now says `modified: readme.txt` under **"Changes not staged for commit"**.
   In my own words: Git knows this file already (it's tracked), it noticed the file changed since the last commit, but the change isn't staged yet, so it won't be included in the next commit until I `git add` it.
3. Stage and commit:
   ```bash
   git add readme.txt
   git commit -m "Add second line to readme"
   ```
4. I now have **2 commits** (`git rev-list --count HEAD` → `2`).

---

## Part 5: Exploration

- **`git diff`** shows the exact line-by-line changes between the working directory and the last staged/committed version. Added lines start with `+`, removed lines with `-`:
  ```
  @@ -1 +1,2 @@
   This is my first Git repository.
  +Learning Git step by step.
  ```
  (Once a change is staged, plain `git diff` shows nothing; `git diff --staged` shows it.)
- **`git log --oneline`** shows the commit history compactly, one commit per line (short hash + message):
  ```
  5feba23 Add second line to readme
  db5de75 Add readme file
  ```

---

## Part 6: Working with branches

1. List all branches: `git branch`
2. Create `feature-script`: `git branch feature-script`
3. Switch to it: `git switch feature-script` (older syntax: `git checkout feature-script`)
4. Create **and** switch to `dev` in one command: `git switch -c dev` (older: `git checkout -b dev`)
5. Switch back: `git switch feature-script`
6. Verify: `git branch` (the `*` marks the current branch) or `git branch --show-current`
   ```
     dev
   * feature-script
     main
   ```

---

## Part 7: Create a Bash script on a branch

1. `git branch --show-current` → `feature-script`
2. & 3. Created `install.sh`:
   ```bash
   #!/bin/bash
   # Simple installation script

   echo "Starting installation..."

   # Update package lists
   sudo apt update

   # Install a package (curl)
   sudo apt install -y curl

   echo "Installation complete!"
   ```
4. Make it executable: `chmod +x install.sh`
   (On Windows I also ran `git update-index --chmod=+x install.sh` so Git records the executable bit — the commit shows `create mode 100755`.)
5. Stage and commit:
   ```bash
   git add install.sh
   git commit -m "Add install script"
   ```
6. History on this branch (`git log --oneline`):
   ```
   d75e2f2 Add install script
   5feba23 Add second line to readme
   db5de75 Add readme file
   ```

---

## Part 8: Merge branches

1. `git switch main`
2. `ls` → only `readme.txt`. **`install.sh` is not there**, because it was committed on `feature-script` only. Each branch has its own version of the files, and `main` doesn't have that commit yet.
3. Merge: `git merge feature-script`
   ```
   Updating 5feba23..d75e2f2
   Fast-forward
    install.sh | 12 ++++++++++++
   ```
4. `ls` → `install.sh  readme.txt`. The script now appears on `main`.
5. `git log --oneline` on `main` now shows all 3 commits, including "Add install script". It was a **fast-forward** merge: `main` had no new commits of its own, so Git just moved the `main` pointer forward to the `feature-script` commit (no extra merge commit was created).
6. Delete the merged branch: `git branch -d feature-script`

---

## Part 9: Push to GitHub

1. & 2. Created an **empty** repo `my-first-repo` on GitHub (no README).
3. Link local repo to GitHub:
   ```bash
   git remote add origin https://github.com/mathislepoutre1/my-first-repo.git
   ```
4. Push:
   ```bash
   git push -u origin main
   ```
   (`-u` sets `origin/main` as the upstream, so later a plain `git push` works.)
5. After refreshing GitHub: the repo shows `install.sh` and `readme.txt`, the `main` branch, and 3 commits.

---

## Part 10: Delete and clone

1. `cd ..`
2. Delete the local repository folder: `rm -rf my-first-repo`
3. Clone it back: `git clone https://github.com/mathislepoutre1/my-first-repo.git`
4. `cd my-first-repo` then `ls` → `install.sh  readme.txt`, and `git log --oneline` shows all 3 commits. Everything came back from GitHub.

---

## Part 11: Full workflow practice

```bash
# 1. Delete local folder
rm -rf my-first-repo
# 2. Delete GitHub repo (or via Settings → Danger Zone on github.com)
gh repo delete mathislepoutre1/my-first-repo --yes
# 3-4. New project
mkdir bash-installer
cd bash-installer
git init
git branch -M main
# 5-6. README on main
#   (created README.md)
git add README.md
git commit -m "Add README explaining the project"
# 7. Feature branch
git switch -c feature-install
# 8-9. Bring in the previous script and commit it
cp ../install.sh.saved install.sh
chmod +x install.sh
git add install.sh
git commit -m "Add install script"
# 10-12. Merge and clean up
git switch main
git merge feature-install
git branch -d feature-install
# 13-14. Create GitHub repo and push
gh repo create bash-installer --public
git remote add origin https://github.com/mathislepoutre1/bash-installer.git
git push -u origin main
```

Result: https://github.com/mathislepoutre1/bash-installer

---

## Reflection questions

**1. Why is version control useful?**
It keeps a full history of every change, so you can see what changed, when, and why, and go back to any earlier version if something breaks. It also lets several people work on the same project without overwriting each other, and keeps a backup copy on a remote like GitHub.

**2. What is the difference between staging and committing?**
Staging (`git add`) chooses which changes go into the next snapshot. It's a preparation area. Committing (`git commit`) actually saves that snapshot permanently in the history with a message. Staging lets you commit only some of your changes and leave the rest for later.

**3. When should you make a commit?**
Whenever you finish one small, logical unit of work that works on its own, like "add the README" or "fix the install command". Commit often, but each commit should do one clear thing. Don't commit broken code to main, and don't let a whole day of unrelated changes pile up into one commit.

**4. What is the difference between `git init` and `git clone`?**
`git init` creates a brand-new, empty repository in the current folder. `git clone` copies an **existing** repository (usually from GitHub) with all its files, history and branches, and sets up the `origin` remote automatically.

**5. Why should you write good commit messages?**
The history is only useful if you can understand it later. A clear message ("Add install script") tells you and your teammates what a commit does without opening the code. That makes it much easier to find a bug, review changes or undo the right commit.

**6. What is the purpose of using branches?**
Branches let you work on something (a feature, a fix, an experiment) in isolation without affecting the main code. When the work is ready you merge it back. If it doesn't work out, you can just delete the branch.

**7. When would you create a new branch instead of working on main?**
Whenever you start a new feature, a bug fix or an experiment, basically any work that isn't finished and tested yet. `main` should always stay in a working, stable state. Branches are also needed when working in a team, so each person's changes can be reviewed (e.g. via a pull request) before being merged.
