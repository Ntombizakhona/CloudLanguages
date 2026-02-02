### 🏗️ Let’s Build

6 | Deploy Your Portfolio to GitHub Pages
-----------------------------------------

Now let’s deploy your portfolio for the world to see!

### Step 01: Prepare Your Portfolio

Make sure your portfolio project has this structure:

**💡Note:** Rename your learning-journal.md file to README

```
portfolio-project/
├── index.html          ← GitHub Pages looks for this
├── styles/
│   └── main.css
├── scripts/
│   └── main.js
├── assets/
│   └── images/
├── python-backend/     ← We'll exclude this from Pages
└── README.md
```

### Step 02: Initialize Git

```
cd portfolio-project
# Initialize Git
git init
# Check status
git status
```

### Step 03: Create .gitignore

Create a .gitignore file to exclude files you don’t want tracked:

```
# .gitignore
# Python
__pycache__/
*.py[cod]
venv/
.env
# IDE
.vscode/
.idea/
# OS files
.DS_Store
Thumbs.db
# Dependencies
node_modules/
# Logs
*.log
```

### Step 04: Make Your First Commit

```
# Stage all files
git add .
# Commit with descriptive message
git commit -m "Initial portfolio with HTML, CSS, JavaScript, and Python backend"
```

### Step 05: Create GitHub Repository

1.  Go to [github.com](https://github.com/)
2.  Click **+** → **New repository**
3.  **Name:** languagesportfolio (or yourusername.github.io for a special URL)
4.  **Description:** My Cloud Languages for Beginners Portfolio
5.  **Public** (required for free GitHub Pages)
6.  **Don’t** add README, .gitignore, or license (you have files already)
7.  Click **Create repository**

### Step 06: Push to GitHub

```
# Add remote origin
git remote add origin https://github.com/yourusername/languagesportfolio.git
# Rename branch to main (if needed)
git branch -M main
# Push and set upstream
git push -u origin main
```

### Step 07: Enable GitHub Pages

1.  Go to your repository on **GitHub**
2.  Click **Settings** tab
3.  Scroll to **Pages** in the left sidebar
4.  Under **Source**, select:

Deploy from branch **▼**

Branch: main

Folder: / (root)

Click **Save**

✅ GitHub Pages source saved.

Wait 1–2 minutes, then visit:

```
https://yourusername.github.io/languagesportfolio
```

**🎉 Your portfolio is now live on the internet!**

### Step 08: Update Your Portfolio

Every time you make changes:

```
# 1. Make your changes to files
# 2. Check what changed
git status
# 3. Stage changes
git add .
# 4. Commit with message
git commit -m "Update skills section with Git progress"
# 5. Push to GitHub
git push
# GitHub Pages automatically deploys the new version!
```

### ⛔ End of Building Tutorial⛔


---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Git in the Cloud](https://ntombizakhona.medium.com/git-in-the-cloud-d8fed43331fc)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**02 February 2026**
