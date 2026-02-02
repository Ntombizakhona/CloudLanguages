Git in the Cloud
================

The Language of Version Control and Deployment
----------------------------------------------

You’ve built a beautiful, fully-functional portfolio with [HTML](https://medium.com/html-in-the-cloud-dc23a04bfe51), [CSS](https://medium.com/css-8c98ee3ec762), [JavaScript](https://medium.com/javascript-in-the-cloud-6dd068fecb77), and a [Python](https://medium.com/python-in-the-cloud-1462f61e3293) backend.

But here’s the problem: it only exists on your computer.

What happens if:

*   _Your laptop crashes?_ → **All your code is gone**
*   _You want to work from a different computer?_ → **No access**
*   _A potential employer asks to see your code?_ → **“Uh… let me email you the files?”**
*   _You make a change that breaks everything?_ → **No way to undo it**

Git solves all of these problems.

Git is the version control system that powers modern software development. It tracks every change you make, lets you collaborate with others, and enables you to deploy your code to the world.

In cloud computing, Git isn’t optional, it’s essential:

*   **AWS CodeCommit?** Git-based repository
*   **Azure DevOps?** Built on Git
*   **GitHub Actions?** Triggered by Git commits
*   **Infrastructure as Code?** Stored in Git
*   **CI/CD pipelines?** All start with Git

If you want to work in cloud computing, Git is non-negotiable…somewhat.

Topics
------

1.  Git fundamentals and core concepts
2.  Repository management and branching
3.  Collaboration with remote repositories (GitHub)
4.  Deploying your portfolio to GitHub Pages
5.  GitHub Actions for automated deployments
6.  Professional Git workflows

1 | Why Git Matters
-------------------

### The Problem Git Solves

Before version control, developers managed code like this:

```
portfolio/
├── index.html
├── index_backup.html
├── index_old.html
├── index_FINAL.html
├── index_FINAL_v2.html
├── index_FINAL_v2_FIXED.html
└── index_DONT_DELETE.html
```

Sound familiar? This approach has serious problems:

*   Which version is actually current?
*   What changed between versions?
*   How do you undo just ONE change?
*   How do two people work on the same file?

Git tracks every change, who made it, when, and why…**_forever._**

### Git vs. Other Systems

Git transforms the way developers manage their code by solving problems that once seemed unavoidable.

Without Git, backing up your work means manually copying files and hoping you remember which version is current, whereas Git automatically maintains a complete history of every change you’ve ever made.

When something breaks, developers without version control can only hope they saved a working backup somewhere, but Git lets you revert to any previous commit with a single command.

Collaboration is perhaps where the difference is most stark because instead of emailing files back and forth and trying to manually merge everyone’s changes, Git allows entire teams to work together seamlessly on the same codebase.

Tracking changes goes from a frustrating guessing game of **_“What did I even change?”_** to a clear, searchable history complete with descriptive messages explaining every modification.

Managing multiple versions no longer means drowning in folders named “final,” “final_v2,” and “final_REALLY_final,” but instead involves clean, organized branches that keep experimental work separate from stable code.

Even deployment improves dramatically: rather than manually uploading files via FTP and hoping nothing goes wrong, Git enables you to push your code with a single command, often triggering automatic deployment pipelines that handle everything else for you.

### Git in Cloud Computing

Git is the foundation of modern cloud development:

```
Developer writes code
       ↓
Git commit (save snapshot)
       ↓
Git push to GitHub
       ↓
GitHub Actions triggered (CI/CD)
       ↓
Automated tests run
       ↓
Deploy to AWS/Azure/GCP
       ↓
Your app is live! 🚀
```

Every major cloud platform integrates with Git:

*   **AWS:** CodeCommit, CodePipeline, CodeDeploy
*   **Azure:** Azure Repos, Azure Pipelines
*   **GCP:** Cloud Source Repositories, Cloud Build
*   **GitHub:** Actions, Pages, Packages

2 | Git Fundamentals
--------------------

### Installing Git

Check if Git is installed:

```
git --version
# Expected output: git version 2.52.0 (or similar)
```

If not installed:

*   **Windows:** Download from [git-scm.com](https://git-scm.com/download/win)
*   **Mac:** brew install git (if you have Homebrew) or download from git-scm.com
*   **Linux:** sudo apt install git (Ubuntu/Debian) or sudo yum install git (CentOS)

### First-Time Setup

Configure Git with your identity:

```
# Set your name (appears in commit history)
git config --global user.name "Your Name"
# Set your email (use same email as your GitHub account)
git config --global user.email "your.email@example.com"
# Verify settings
git config --list
```

### Understanding Git Concepts

Think of Git like a time machine for your code:

```
┌─────────────────────────────────────────────────────────────┐
│                     YOUR PROJECT TIMELINE                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Commit 1      Commit 2      Commit 3      Commit 4         │
│  "Initial      "Add CSS      "Add          "Fix             │
│   structure"    styling"      JavaScript"   bug"            │
│      │            │              │            │             │
│      ▼            ▼              ▼            ▼             │
│   ┌────┐       ┌────┐        ┌────┐       ┌────┐            │
│   │ v1 │ ───→  │ v2 │  ───→  │ v3 │ ───→  │ v4 │  (HEAD)    │
│   └────┘       └────┘        └────┘       └────┘            │
│                                                             │
│   You can go back to ANY point in this timeline!            │
└─────────────────────────────────────────────────────────────┘
```

### Key Concepts

**Term:** Repository (repo)
**Definition:** A project tracked by Git
**Analogy:** _A folder with superpowers that remembers everything that ever happened inside it_

**Term:** Commit
**Definition:** A snapshot of your code at a point in time
**Analogy:** _A save point in a video game, you can always return to any previous save if things go wrong_

**Term:** Branch
**Definition:** An independent line of development
**Analogy:** _A parallel universe where you can experiment freely without affecting the original timeline_

**Term:** Merge
**Definition:** Combining changes from different branches
**Analogy:** _Merging parallel universes back into one_

**Term:** Remote
**Definition:** A copy of your repository stored on a server like GitHub
**Analogy:** _Your cloud backup that ensures your code is never lost_

**Term:** Clone
**Definition:** Downloading a remote repository to your computer
**Analogy:** _Making a local copy of a project you can work with_

**Term:** Push
**Definition:** Uploading your commits to a remote
**Analogy:** _Syncing your work to the cloud_

**Term:** Pull
**Definition:** Downloading updates from a remote
**Analogy:** _Syncing from the cloud back to your machine_

**Term**: Staging Area (Index)
**Definition:** A holding area where you prepare changes before committing
**Analogy:** _A shopping cart, you add items before checking out_

**Term:** HEAD
**Definition:** A pointer to the current commit you’re working on
**Analogy:** _A_ **_“You Are Here”_** _marker on a map_

**Term:** Checkout
**Definition:** Switching between branches or commits
**Analogy:** _Time traveling to a different point in your project’s history_

**Term:** Fetch
**Definition:** Downloading updates from a remote without merging them
**Analogy:** _Checking your mailbox without opening the letters yet_

**Term:** Stash
**Definition:** Temporarily saving uncommitted changes to work on something else
**Analogy:** _Putting your work in a drawer to deal with later_

**Term:** Conflict
**Definition:** When Git can’t automatically merge changes because two people edited the same lines
**Analogy:** _Two people trying to write different things in the same spot on a shared document_

**Term:** Fork
**Definition:** Creating your own copy of someone else’s repository on GitHub
**Analogy:** _Making a photocopy of a recipe so you can modify it without changing the original_

**Term:** Pull Request (PR)
**Definition:** A request to merge your changes into another branch or repository
**Analogy:** _Raising your hand to ask permission before adding your work to the group project_

### The Three States of Git

Files in Git exist in three states:

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  WORKING     │     │   STAGING    │     │  REPOSITORY  │
│  DIRECTORY   │────▶│    AREA      │────▶│   (COMMITS) │
│              │     │              │     │              │
│  Your files  │     │  Ready to    │     │  Permanent   │
│  as you      │     │  be          │     │  snapshots   │
│  edit them   │     │  committed   │     │  of history  │
└──────────────┘     └──────────────┘     └──────────────┘
       │                    │                    │
       │   git add          │   git commit       │
       └────────────────────┴────────────────────┘
```

1.  **Working Directory:** Where you edit files normally
2.  **Staging Area:** Where you prepare changes for a commit
3.  **Repository:** Where permanent snapshots (commits) are stored

3 | Basic Git Commands
----------------------

### Starting a Repository

**Option 1:** Create a new repository

```
# Navigate to your project folder
cd portfolio-project
# Initialize Git (creates .git folder)
git init
# Output: Initialized empty Git repository in /path/to/portfolio-project/.git/
```

**Option 2:** Clone an existing repository

```
# Download a project from GitHub
git clone https://github.com/username/repository-name.git
# This creates a folder with the repo contents
cd repository-name
```

### The Basic Workflow

Here’s the workflow you’ll use daily:

```
# 1. Check what's changed
git status
# 2. Stage changes (add to staging area)
git add filename.html        # Add specific file
git add .                    # Add ALL changed files
# 3. Commit changes (create snapshot)
git commit -m "Describe what you changed"
# 4. View history
git log --oneline
```

### Understanding Each Command

**git status:** See what’s happening

```
git status
# Output:
# On branch main
# Changes not staged for commit:
#   modified:   index.html
#   modified:   styles/main.css
#
# Untracked files:
#   scripts/new-feature.js
```

**Color coding:**

🔴 **Red:** Changed but not staged

🟢 **Green:** Staged and ready to commit

**Untracked:** New files Git doesn’t know about yet

**git add:** Stage changes

```
# Stage one file
git add index.html
# Stage multiple files
git add index.html styles/main.css
# Stage all changes in current directory
git add .
# Stage all changes everywhere
git add -A
```

**git commit:** Create a snapshot

```
# Commit with a message
git commit -m "Add responsive navigation menu"
# Commit with detailed message (opens editor)
git commit
```

**Writing Good Commit Messages:**

```
# ❌ Bad commit messages
git commit -m "fix"
git commit -m "changes"
git commit -m "update"
git commit -m "asdfasdf"
# ✅ Good commit messages
git commit -m "Add responsive navigation for mobile devices"
git commit -m "Fix contact form validation bug"
git commit -m "Update hero section with gradient background"
git commit -m "Refactor CSS to use CSS variables"
```

**Commit Message Formula:**

```
[Action] [What] [Why/Where] (optional)
Examples:
- Add contact form validation
- Fix broken link in navigation
- Update skills section with Python progress
- Remove unused CSS styles
- Refactor JavaScript for better performance
```

**git log:** View commit history

```
# Simple one-line format
git log --oneline
# Output:
# a1b2c3d Add Python backend API
# e4f5g6h Update skills progress bars
# i7j8k9l Add contact form styling
# m0n1o2p Initial portfolio structure
# Detailed view
git log
# Visual branch graph
git log --oneline --graph --all
```

**git diff**: See what changed

```
# See unstaged changes
git diff
# See staged changes
git diff --staged
# See changes in specific file
git diff index.html
```

4 | Branching and Merging
-------------------------

### Why Branches?

Branches let you work on features without affecting the main code:

```
┌─── feature/dark-mode ───┐
                   /                           \
main: ────●────●────●────●────●────●────●────●────●
                         \                   /
                          └─ bugfix/nav ────┘
```

**Real-world scenarios:**

*   Add a new feature without breaking the live site
*   Fix a bug while someone else adds features
*   Experiment without fear
*   Work on multiple things simultaneously

### Branch Commands

Create and switch branches:

```
# See all branches (* = current branch)
git branch
# Create a new branch
git branch feature/contact-form
# Switch to a branch
git checkout feature/contact-form
# Create AND switch in one command (recommended)
git checkout -b feature/contact-form
# Modern alternative (Git 2.23+)
git switch -c feature/contact-form
```

Common branch naming conventions:

```
feature/add-dark-mode     # New feature
bugfix/fix-navigation     # Bug fix
hotfix/security-patch     # Urgent fix
refactor/clean-css        # Code cleanup
docs/update-readme        # Documentation
```

### Merging Branches

After finishing work on a branch, merge it back:

```
# 1. Switch to main branch
git checkout main
# 2. Merge the feature branch into main
git merge feature/contact-form
# 3. Delete the branch (optional, keeps things clean)
git branch -d feature/contact-form
```

**Visual representation:**

```
Before merge:
main:    ────●────●────●
                        \
feature:                 ●────●────●
After merge:
main:    ────●────●────●─────────────●  (merge commit)
                        \           /
feature:                 ●────●────●
```

### Handling Merge Conflicts

Sometimes Git can’t automatically merge changes:

```
git merge feature/dark-mode
# Output:
# Auto-merging styles/main.css
# CONFLICT (content): Merge conflict in styles/main.css
# Automatic merge failed; fix conflicts and then commit.
```

The conflict looks like this in your file:

```
<<<<<<< HEAD
background-color: #ffffff;
=======
background-color: #1a1a1a;
>>>>>>> feature/dark-mode
```

How to resolve:

1.  Open the file with conflicts
2.  Decide which code to keep (or combine them)
3.  Remove the conflict markers (<<<<<<<, =======, >>>>>>>)
4.  Save the file
5.  Stage and commit:

```
git add styles/main.css
git commit -m "Resolve merge conflict in CSS background color"
```

5 | Remote Repositories (GitHub)
--------------------------------

### Connecting to GitHub

**Step 01**: Create a GitHub account
Go to [github.com](https://github.com/) and sign up (free).

**Step 02:** Create a new repository on GitHub

1.  Click the “+” icon → “New repository”
2.  Name it portfolio (or similar)
3.  Keep it public (required for free GitHub Pages)
4.  Don’t initialize with README (you already have files)
5.  Click “Create repository”

**Step 03:** Connect your local repo to GitHub

```
# Add the remote (origin is the conventional name)
git remote add origin https://github.com/yourusername/portfolio.git
# Verify it was added
git remote -v
# Push your code to GitHub
git push -u origin main
```

The -u flag sets up tracking, so future pushes only need git push.

### Push and Pull

Push (upload your commits):

```
# Push current branch to remote
git push
# Push specific branch
git push origin feature/new-section
# Force push (⚠️ use carefully, overwrites remote)
git push --force
```

Pull (download updates):

```
# Pull changes from remote
git pull
# Pull from specific branch
git pull origin main
```

### Clone an Existing Repository

```
# Download a project from GitHub
git clone https://github.com/username/repository.git
# Clone to a specific folder
git clone https://github.com/username/repository.git my-folder-name
```

### SSH vs HTTPS

**HTTPS** (what we used above):

*   Works immediately
*   Asks for username/password (or token) each push

**SSH** (recommended for regular use):

```
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"
# Start SSH agent
eval "$(ssh-agent -s)"
# Add your key
ssh-add ~/.ssh/id_ed25519
# Copy public key to clipboard
# Windows:
clip < ~/.ssh/id_ed25519.pub
# Mac:
pbcopy < ~/.ssh/id_ed25519.pub
# Linux:
cat ~/.ssh/id_ed25519.pub
```

**Then add the key to GitHub: Settings → SSH and GPG keys → New SSH key**


7 | GitHub Actions (CI/CD)
--------------------------

GitHub Actions automates tasks when you push code.

### Optional Step: Create a Basic Workflow

Create .github/workflows/deploy.yml:

```
name: Deploy Portfolio
# Run on push to main branch
on:
  push:
    branches: [ main ]
jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    # Step 1: Get the code
    - name: Checkout code
      uses: actions/checkout@v4
    
    # Step 2: Validate HTML (optional)
    - name: Validate HTML
      run: |
        echo "✅ Checking HTML structure..."
        # Add HTML validation here if desired
    
    # Step 3: Deploy to GitHub Pages
    - name: Deploy to GitHub Pages
      uses: peaceiris/actions-gh-pages@v3
      if: github.ref == 'refs/heads/main'
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./
```

**Now every push to main automatically triggers deployment!**

### Understanding the Workflow

```
name: Deploy Portfolio      # Workflow name (shows in GitHub UI)
on:                         # Trigger conditions
  push:
    branches: [ main ]      # Only on main branch
jobs:                       # Tasks to run
  deploy:                   # Job name
    runs-on: ubuntu-latest  # Virtual machine type
    
    steps:                  # Sequential actions
    - name: Checkout code   # Step description
      uses: actions/checkout@v4  # Pre-built action
```

### View Your Actions

1.  Go to your GitHub repository
2.  Click **Actions** tab
3.  See all workflow runs
4.  Click on a run to see details

8 | Professional Git Workflows
------------------------------

### Feature Branch Workflow

The standard way teams work with Git:

```
# 1. Start on main, pull latest
git checkout main
git pull
# 2. Create feature branch
git checkout -b feature/add-blog-section
# 3. Make changes and commit
git add .
git commit -m "Add blog section HTML structure"
git commit -m "Add blog section styling"
git commit -m "Add blog post hover effects"
# 4. Push feature branch to GitHub
git push -u origin feature/add-blog-section
# 5. Create Pull Request on GitHub
# (Review, discuss, get approval)
# 6. Merge via GitHub UI or locally
git checkout main
git pull
git merge feature/add-blog-section
git push
# 7. Delete feature branch
git branch -d feature/add-blog-section
git push origin --delete feature/add-blog-section
```

### Useful Git Aliases

Add these to your Git config for faster commands:

```
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm "commit -m"
git config --global alias.lg "log --oneline --graph --all"
```

Now you can use:

```
git st          # instead of git status
git co main     # instead of git checkout main
git cm "msg"    # instead of git commit -m "msg"
git lg          # pretty log view
```

9 | Common Git Mistakes and How to Fix Them
-------------------------------------------

### Mistake 01: Committing to Wrong Branch

```
# ❌ Oops, committed to main instead of feature branch
# ✅ Fix: Move the commit to a new branch
git branch feature/new-stuff    # Create branch with current commits
git reset --hard HEAD~1         # Remove last commit from main
git checkout feature/new-stuff  # Switch to new branch
```

### Mistake 02: Commit Message Typo

```
# ❌ git commit -m "Add contct form"
# ✅ Fix: Amend the last commit
git commit --amend -m "Add contact form"
# ⚠️ Only use amend if you haven't pushed yet!
```

### Mistake 03: Forgot to Add a File

```
# ❌ Committed but forgot styles.css
# ✅ Fix: Add file and amend
git add styles.css
git commit --amend --no-edit
```

### Mistake 04: Need to Undo Last Commit

```
# Option 1: Keep changes, undo commit
git reset --soft HEAD~1
# Files are still staged, ready to recommit
# Option 2: Undo commit AND unstage
git reset HEAD~1
# Files are modified but not staged
# Option 3: Undo commit AND discard changes (⚠️ DANGEROUS)
git reset --hard HEAD~1
# Changes are GONE forever
```

### Mistake 05: Pull Rejected (Remote Ahead)

```
# ❌ git push rejected
# hint: Updates were rejected because the remote contains work that you do not have locally.
# ✅ Fix 1: Pull then push
git pull --rebase
git push
# ✅ Fix 2: If you're sure yours is correct
git push --force  # ⚠️ Use carefully!
```

### Mistake 06: Accidentally Committed Secrets

```
# ❌ Committed .env file with API keys!
# ✅ Fix: Remove from history (if not pushed yet)
git reset --soft HEAD~1
# Add .env to .gitignore
echo ".env" >> .gitignore
git add .gitignore
git commit -m "Add .gitignore, remove sensitive files"
# If already pushed, you need to rotate your secrets!
# The keys are compromised and visible in Git history
```

### Mistake 07: Merge Conflict Panic

```
# ❌ CONFLICT! Don't panic.
# ✅ Fix:
# 1. Open the conflicting file
# 2. Look for <<<<<<< HEAD
# 3. Decide what to keep
# 4. Remove conflict markers
# 5. Save, add, commit
git add conflicted-file.html
git commit -m "Resolve merge conflict in navigation"
```

10 | Update Your Learning Journal
---------------------------------

Add this to your learning-journal.md (which is now README.md):

```
## Day 7: Git - Version Control and Deployment
### Date: [Today's Date]
### What I Learned:
#### Git Fundamentals:
- **Repository**: A project folder tracked by Git
- **Commit**: A snapshot of code at a point in time
- **Branch**: Independent line of development
- **Remote**: Server copy of repository (GitHub)
- **The Three States**: Working Directory → Staging Area → Repository
#### Essential Commands Mastered:
```bash
git init          # Start tracking a project
git status        # Check what's changed
git add .         # Stage all changes
git commit -m ""  # Create a snapshot
git log --oneline # View history
git branch        # List branches
git checkout -b   # Create and switch branch
git merge         # Combine branches
git remote add    # Connect to GitHub
git push          # Upload to GitHub
git pull          # Download from GitHub
git clone         # Copy a repository
Aha! Moments:
💡 Git is like a time machine, I can go back to any point!
💡 Commits are cheap, commit often, with good messages
💡 Branches let me experiment without fear of breaking things
💡 GitHub Pages makes deployment FREE and automatic
💡 Every cloud platform uses Git, this skill transfers everywhere
Git Workflow Practiced:
Check status: git status
Stage changes: git add .
Commit with message: git commit -m "Description"
Push to remote: git push
Create branches for features: git checkout -b feature/name
Merge when complete: git merge feature/name
Skills Progress:
English: 100% ✅
Mathematics: 100% ✅
HTML: 100% ✅
CSS: 100% ✅
JavaScript: 75% ✅
Python: 75% ✅
Git: 75% ✅ (fundamentals + deployment mastered!)
Linux: 0% 📅
SQL: 0% 📅
Kubernetes: 0% 📅
Java: 0% 📅
Progress: 7/11 skills (63.6% of foundational languages!)
```

### Key Concepts:

1.  **Version Control:** Track every change, who made it, when, and why
2.  **Branching:** Work on features without affecting main code
3.  **Merge Conflicts:** When Git can’t auto-merge, you decide
4.  **GitHub Pages:** Free hosting for static websites
5.  **GitHub Actions:** Automated workflows triggered by Git events

Portfolio Project Progress Tracker:
-----------------------------------

✅ **Day 1:** English — _Technical communication_
✅ **Day 2:** Mathematics — _Logic and algorithms_
✅ **Day 3:** HTML — _Web structure_
✅ **Day 4:** CSS — _Styling and layout_
✅ **Day 5:** JavaScript — _Frontend interactivity_
✅ **Day 6:** Python — _Backend logic and APIs_
✅ **Day 7:** Git — _Version control and deployment_
⬜ **Day 8:** Linux — _Command line and servers_
⬜ **Day 9:** SQL — _Database management_
⬜ **Day 10:** Kubernetes — _Container orchestration_
⬜ **Day 11:** Java — _Enterprise applications_

### Final Thoughts

1.  **Git is your safety net:** Every change is tracked and reversible
2.  **Commit early, commit often:** Small, frequent commits are better than huge ones
3.  **Write good commit messages:** Future you will thank present you
4.  **Branches are free:** Use them for every feature
5.  **GitHub is your portfolio:** Recruiters look at your commit history
6.  **Push daily:** Your code should never only exist on your laptop
7.  **Git skills transfer everywhere:** Every cloud platform uses Git

Concluding Remarks
------------------

Your portfolio is no longer a local project, it’s a live website that anyone in the world can visit.

You’re now at 63.6% of your learning journey!

The foundation is complete:

*   🌐 Frontend (HTML, CSS, JavaScript) ✓
*   ⚙️ Backend (Python) ✓
*   🔄 Version Control (Git) ✓
*   🚀 Deployment (GitHub Pages) ✓

Next up: **Linux** — the operating system that powers the cloud. You’ll learn the command line skills that every cloud engineer needs.

Keep the momentum going! 💪☁️🚀


Additional Resources
--------------------

### [Cloud Glossary](https://medium.com/list/cloud-glossary-528956a3c181)

> Cloud Computing Simplified: A Cloud Glossary For Beginners

### [Cloud Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34703)

> In a world where nearly every industry is moving toward cloud-first strategies, having a baseline understanding of these principles is no longer optional but essential.

### [AI Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34979)

> Whether you’re an Executive, Developer, Engineer, or Project Manager, having a baseline understanding of AI principles is no longer a _nice to have_, it’s **absolutely essential.**

Git Learning Resources
----------------------

### [Pro Git Book](https://git-scm.com/book/en/v2)

> Free, comprehensive guide

### [GitHub Skills](https://skills.github.com/)

> Interactive tutorials

### [Oh Shit, Git!?!](https://ohshitgit.com/)

> Fixing common mistakes

### [Learn Git Branching](https://learngitbranching.js.org/)

> Visual, interactive

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Git in the Cloud](https://ntombizakhona.medium.com/git-in-the-cloud-d8fed43331fc)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**02 February 2026**
