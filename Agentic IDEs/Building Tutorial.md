🏗️ Let’s Build
---------------

8 | Use an Agentic IDE on Your Portfolio
----------------------------------------

This is the hands-on section. But it’s _obviously_ different from every other post in the series. Instead of writing code line by line, you’re going to **direct an agent to do it.**

### Exercise 1: Set Up Steering for Your Portfolio

Create a `.kiro/steering/portfolio-standards.md` file:

```
  - **Backend:** Python 3.11 with Flask, matching backend/Dockerfile
- **Frontend:** Vanilla HTML/CSS/JavaScript (no frameworks)
- **Database:** MySQL 8.0
- **Infrastructure:** Terraform on AWS (EC2), following infrastructure.md
- **Orchestration:** Kubernetes (Minikube or Docker Desktop)
- **API responses keep the existing format:** { "success": true, "data": ..., "message": "..." },
  and errors use { "success": false, "error": "..." }. The frontend depends on it.
- All **database** queries use parameterized statements
- All **Kubernetes** deployments include resource limits and health checks
- All **Terraform** resources include a Name tag; Project and ManagedBy come from default_tags
- **Commit messages follow:** [Action] [What] [Why] format
```

> **_Steering has to describe the code as it really is. If it says one thing and the code does another, Kiro may “fix” working code to match the steering, for example by changing an API format your frontend relies on._**

### Exercise 2: Create a Spec for a New Feature

Open your agentic IDE and describe a feature:

```
**"I want to add a 'Learning Progress' page to my portfolio that shows
my progress through all 13 languages in this blog series. It should:
- Pull skill data from the database
- Show a visual progress bar for each language
- Group skills by category (communication, frontend, backend, devops, cloud, AI)
- Include the blog post link for each language
- Be responsive and match the existing portfolio design"**
```

> **_Let the agent generate the spec, review it, refine it, then let it execute._**

### Exercise 3: Create a Hook

Set up a hook that validates your Terraform every time you save a `.tf` file. Section 9, Step 4 shows how to create it in the Agent Hooks panel and which settings to check.

### Exercise 4: Prompt Engineering for Development

Practice these prompts with your agentic IDE.

**💡Notice how the quality of your English directly impacts the quality of the output:**

**Vague prompt** (poor results):

```
**"Add a page to my portfolio"**
```

**Specific prompt** (good results):

```
**"Add an 'About Me' page to the portfolio frontend that includes:**
- A professional bio section with my name and role
- A timeline of my learning journey through the 13 languages
- Links to my GitHub and LinkedIn profiles
- A downloadable PDF resume link
- Responsive design matching the existing styles.css
- Navigation link added to the existing header"
```

**Expert prompt** (great results):

```
**"Add an 'About Me' page to the portfolio. Reference the existing
index.html for layout patterns and styles.css for design tokens.
The page should be accessible (semantic HTML, ARIA labels where needed,
keyboard navigable). Add the route to the Flask backend if needed for
any dynamic content. Update the navigation in all existing HTML pages
to include the new link."**
```

English skills from are the foundation of effective agentic development.

9 | Configure Kiro to Build Your Cloud Infrastructure
-----------------------------------------------------

You wrote every line of [Terraform](https://medium.com/terraform-in-the-cloud-337b9bdbbc0b) yourself.

Now you’ll set Kiro up to write and change it for you, safely. An agent that can use your terminal and your AWS credentials can create real infrastructure, and delete it, so the setup matters as much as the prompts.

1.  **Steering** loads your infrastructure rules into every conversation.
2.  **MCP servers** give Kiro access to current AWS and Terraform documentation, so it does not have to work from memory.
3.  **Hooks** run automatic checks, such as `terraform validate`, whenever you save a `.tf` file.
4.  **Specs** give you a plan to review before Kiro writes any code.
5.  **Supervised mode and command approval** let you approve every file change and every command.

### Prerequisites

*   **Terraform and the AWS CLI**, set up as in the [Terraform](https://medium.com/terraform-in-the-cloud-337b9bdbbc0b) post.

> **_💡Kiro’s terminal uses the credentials you saved with_** `**_aws configure_**`**_, so Kiro can do anything your IAM user can do._**
> 
> **_That's why that user only has EC2 and S3 permissions._**
> 
> _⚠️_ **_Never give an agent an administrator account._**

*   **uv**, a Python tool that downloads and runs many MCP servers:

```
winget install --id=astral-sh.uv -e
```

*   **Docker Desktop**, running, for the Terraform documentation
*   **A clean Git working tree.** Run `git status` and commit anything pending. Afterwards, `git diff` shows everything Kiro changed, and `git restore <file>` puts a file back.

Open your `LanguagesPortfolio` folder in Kiro with **File → Open Folder**.

### Step 1: Write Your Infrastructure Rules (Steering)

Create `.kiro/steering/infrastructure.md` inside `LanguagesPortfolio`:

```
**---
inclusion: always
---**
# Infrastructure rules
- Terraform code lives in terraform/. AWS region: us-west-2.
- Use Terraform >= 1.11 and the AWS provider ~> 6.0. Check resource arguments
  in the current provider documentation before writing them.
- Every resource gets a Name tag. Project and ManagedBy come from default_tags.
- Stay inside the AWS Free Tier where possible. Ask me before adding anything
  that bills by the hour, such as NAT gateways, load balancers, EKS, or RDS.
- SSH (port 22) is only open to var.ssh_allowed_cidr, never 0.0.0.0/0.
  Port 5000 stays closed: Nginx forwards /api/ to Flask inside the server.
- Never put credentials, tokens, or passwords in .tf files, user data, or chat.
- After editing .tf files, run terraform -chdir=terraform fmt and
  terraform -chdir=terraform validate.
- Never run terraform apply or terraform destroy. Run terraform plan, summarize
  what it will create, change, and destroy, and wait for me to apply it.
- Never commit terraform.tfstate, terraform.tfvars, or the .terraform folder.
```

> **_The front matter between the_** `**_---_**` **_lines controls when Kiro loads the file. Set_** `**_inclusion_**` **_to_** `**_always_**` **_to load the file in every conversation in this workspace._**
> 
> **_Use_** `**_fileMatch_**` **_to load it when Kiro reads files matching_** `**_fileMatchPattern_**`**_, such as_** `**_terraform/**_**`**_._**
> 
> **_Use_** `**_manual_**` **_to load it only when you add it to a chat with_** `**_#_**`**_._**
> 
> **_Use_** `**_auto_**` **_to let Kiro load it when your request matches the file's_** `**_description_**`**_._**

Safety rules should always apply, so this file uses `**always**`.

Write rules the way you'd brief a new teammate: specific, checkable, and with a reason when the reason isn't obvious.

The most important line is the one about `apply` and `destroy`. Kiro proposes changes, You decide what happens to your AWS account.

### Step 2: Connect Documentation Servers (MCP)

A language model learns from a snapshot of the internet, so it can suggest settings that have since changed.

MCP servers give Kiro tools to look things up while it works.

Create `.kiro/settings/mcp.json` inside `LanguagesPortfolio`:

```
{
  "mcpServers": {
    "aws-docs": {
      "command": "uvx",
      "args": ["awslabs.aws-documentation-mcp-server@latest"],
      "env": { "FASTMCP_LOG_LEVEL": "ERROR" },
      "disabled": false
    },
    "terraform": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "hashicorp/terraform-mcp-server"],
      "disabled": false
    }
  }
}
```

*   **aws-docs**, from AWS Labs, searches and reads the AWS documentation.
*   **terraform**, from HashiCorp, looks up providers, resources, and modules in the Terraform Registry.

Both only read public documentation. Neither receives your AWS credentials, and neither can change your account. Kiro connects to them when you save the file, and the first start takes a minute while uv and Docker download them. Open the Kiro panel and check that both show as connected in the MCP servers view.

This file only applies to this project. Servers you want in every project go in your user-level file, `~/.kiro/settings/mcp.json`. If you later add a server that needs AWS access, set `AWS_PROFILE` in its `env` to a profile name.

Never put the keys themselves in an MCP config, because these files often end up in Git.

### Step 3: Keep Yourself in the Loop

Kiro has two autonomy modes, and you switch between them in the chat panel:

*   **Autopilot**, the default: Kiro edits files without stopping, and you review the result afterwards.
*   **Supervised**: after each turn that edits files, Kiro stops and shows every change as a separate hunk that you accept or reject.

Use **Supervised** for infrastructure. A wrong line in a web page breaks a layout. A wrong line in Terraform can delete a server or open a port to the whole internet.

Kiro also asks before it runs a terminal command, so read each command before you allow it. Kiro can trust a command so it stops asking. Only do that for read-only commands such as `terraform validate` or `terraform plan`, never for `terraform apply` or `terraform destroy`.

### Step 4: Validate on Every Save (Hook)

Open the Kiro panel, find **Agent Hooks**, and click **+**. You can also press `Ctrl+Shift+P` and run **Open Kiro Hook UI**. Describe the hook in plain English:

```
**When I save a .tf file in the terraform folder, run terraform validate in that folder.**
```

Kiro turns the description into a hook and saves it in `.kiro/hooks/`, so it's versioned with your code. Check the settings it chose:

*   **Trigger:** a file is saved
*   **Files:** `terraform/**/*.tf`
*   **Action:** run the command `terraform -chdir=terraform validate`

`-chdir=terraform` runs Terraform in the `terraform` folder, even though hooks start in the project root. Choose an action that runs a command, not one that asks Kiro to act. A command just runs. An agent action starts a Kiro session, and spends credits, every time you save.

Test it: change `var.instance_type` to `var.instance_typo` in `main.tf` and save. The hook should report `Reference to undeclared input variable`. Change it back and save again.

### Step 5: Build Infrastructure from a Spec

In Post 12, `terraform apply` gave you an empty server, and you deployed the portfolio over SSH. Have Kiro close that gap, so one `apply` produces a working website.

Start a **Spec** session in the Kiro chat and describe the feature:

```
**Change the Terraform in terraform/ so a new server deploys the portfolio by itself
on first boot.**
- cloud-init clones my public GitHub repository, then builds and starts the backend
  and frontend containers exactly as in Post 12, Section 7: a Docker network named
  portfolio, the backend container named backend-service, the frontend published on
  port 80, and both with --restart unless-stopped.
- The repository URL is a Terraform variable named repo_url, with
  https://github.com/YOUR-USERNAME/languagesportfolio.git as its default.
- Don't change the security group rules, the instance size, or the outputs.
- No secrets in user data
```

Kiro writes three documents in `.kiro/specs/` and stops after each one for your review:

1.  **requirements.md:** user stories with acceptance criteria, written as `WHEN ... THE SYSTEM SHALL ...` statements. Check that every bullet from your prompt became a criterion.
2.  **design.md:** how Kiro will do it, probably by passing `repo_url` into `cloud-init.yaml` with Terraform's `templatefile()` function. Check that port 5000 stays closed and that nothing secret goes into user data. Anyone who can view the instance in the EC2 console can read its user data.
3.  **tasks.md:** a checklist of small steps. Click **Start task** next to each one, and review the changes before you accept them.

If something is wrong, say so in the chat, for example “the frontend must start after the backend”. Kiro updates the document before it writes any code. Fixing a sentence in a design is cheaper than fixing a broken server.

### Step 6: Review the Plan, Then Apply It Yourself

When the tasks are done, Kiro runs `terraform validate` and `terraform plan` and summarises the plan, as your steering file asks. Read the plan yourself as well. It should replace only the server:

```
# aws_instance.portfolio must be replaced
-/+ resource "aws_instance" "portfolio" {
      ~ user_data = "..." -> "..." # forces replacement
    }
Plan: 1 to add, 0 to change, 1 to destroy.
```

The server is replaced because its user data changed and the new server gets a new IP address. If the plan touches the security group or the key pair, ask Kiro why before you go any further.

The new server builds from GitHub, so first make sure GitHub has your latest `backend` and `frontend` folders. Then apply the plan yourself:

```
# In LanguagesPortfolio\terraform
terraform apply
```

Give cloud-init about five minutes to install Docker and build both images, then open the site:

```
Start-Process (terraform output -raw website_url)
```

No SSH this time. The server and the application both come from code now, and Kiro wrote the new parts while you made the decisions.

If the site doesn’t load, give Kiro context with `#` and ask, for example: `#Terminal The site isn't up. Find out why using read-only commands.`

> **_Kiro can read the server's cloud-init log over SSH and explain what failed. Other context keys work the same way:_** `**_#File_**`**_,_** `**_#Folder_**`**_,_** `**_#Problems_**`**_, and_** `**_#Git Diff_**`**_. Before you commit, try_** `**_#Git Diff Review these changes for security problems._**`

10 | (Re) Build Your Portfolio Site with Kiro, Free on GitHub Pages
-------------------------------------------------------------------

Your EC2 server costs money for every hour it runs. Your portfolio needs a permanent home that costs nothing. **GitHub Pages** hosts static websites straight from a GitHub repository, free for public repositories, with HTTPS included.

Static means GitHub only serves files: HTML, CSS, JavaScript, and images. No Python, Java, or database runs behind the site. Your articles live in Markdown files and a JSON file, and JavaScript in the visitor’s browser does the rest.

> **_💡 This is also where the portfolio becomes yours. Kiro can build whatever you can describe, so your taste is the most important input. Here’s how I built mine._**

### What You’ll Build

The file names depend on what Kiro designs, but the result looks roughly like this:

```
YOUR-USERNAME.github.io/      ← new public repository
├── index.html                ← profile header + masonry grid of articles
├── post.html                 ← one article, rendered from Markdown
├── about.html, 404.html
├── css/, js/, images/
├── articles.json             ← title, date, tags, cover, and link for each article
├── posts/                    ← your articles as Markdown
├── .nojekyll
├── README.md                 ← what the site is and how to change it
└── .kiro/steering/design.md  ← your design rules
```

A repository named exactly `YOUR-USERNAME.github.io` becomes your **user site**, served at `https://YOUR-USERNAME.github.io`.

Use your GitHub username in lowercase. Keep this repository separate from `LanguagesPortfolio`: everything in a Pages repository is public, so it should only hold the website.

### Step 1: Create the Repository and Gather Your Content

On GitHub, click **New repository**. Name it `YOUR-USERNAME.github.io`, make it **Public**, and leave it empty, with no README or license. Then, on your laptop:

```
cd $HOME\Downloads
New-Item -ItemType Directory -Path YOUR-USERNAME.github.io
cd YOUR-USERNAME.github.io
git init -b main
New-Item -ItemType Directory -Path posts, images
```

The site needs three kinds of content:

1.  **Articles:** each article as a Markdown file in `posts/`, named after the post, such as `posts/07-git.md`.
2.  **Images:** your profile photo as `images/profile.jpg`, plus any cover images. Only use images you own or have the rights to.
3.  **Links:** a `links.txt` file with the URL of each published article, one per line.

Kiro can do the copying. Open the folder in Kiro with **File → Open Folder** and ask, for example:

> _⚠️ This is mine. Write_ **_yours_**

```
Copy each README.md from the folders in C:\path\to\blog-posts into posts\, named after
its folder, such as posts\07-git.md. Copy any .png in those folders to images\covers\,
also named after the folder, such as images\covers\09-sql.png.
```

### Step 2: Describe Your Taste (Steering)

Design preferences belong in a steering file, so every later request, such as “add a projects page”, keeps the same look. Create `.kiro/steering/design.md`.

> _⚠️ This is mine. Write_ **_yours_**

```
---
inclusion: always
---
# Design rules
- The look: Instagram meets WordPress. Instagram for browsing: a profile header
  and an image-led grid. WordPress for reading: a comfortable article layout,
  categories, tags, and search.
- Light theme by default, with a dark mode toggle that remembers the choice.
- A clean sans-serif font for the interface and a serif font for article text.
- Plain HTML, CSS, and JavaScript. No frameworks and no build step: GitHub Pages
  serves the files exactly as they are in the repository.
- Small libraries are fine if they're pinned to an exact version and saved in vendor/.
- Relative links (css/style.css, not /css/style.css), so the site still works
  if it ever moves into a subfolder.
- Accessible: semantic HTML, alt text on every image, visible keyboard focus,
  and text contrast of at least 4.5:1.
- Check every change at 375 px (phone) and 1280 px (laptop) wide.
```

Not sure what you like?

Take screenshots of three or four sites whose look you admire, drag them into the Kiro chat, and ask: “Describe the visual style these sites share as a list of design rules.” Kiro can read images. Edit its answer into your own `design.md`.

### Step 3: Ask Kiro to Build It (Spec)

Start a **Spec** session and describe the site.

> _⚠️ This is my prompt. Write_ **_yours_**

```
**Build my portfolio website for GitHub Pages in this folder.**
**Home page, Instagram-style:**
- A profile header: round photo (images/profile.jpg), my name, a one-line bio,
  counts for articles, languages, and projects, and buttons for Medium, GitHub,
  and LinkedIn.
- A row of story-style circles, one per language in my Cloud Languages series.
  Clicking one filters the grid to that language's articles.
- My articles as a masonry grid: cards of different heights, 1 column on phones,
  2 on tablets, 3 to 4 on laptops. Each card shows its cover image, or a colourful
  gradient tile with the language name when there's no cover. On hover, show the
  title, reading time, and tags over the image, the way Instagram shows likes.
**Reading, WordPress-style:**
- Clicking a card opens post.html, which renders that article's Markdown from
  posts/ with the title, date, reading time, tags, a table of contents, previous
  and next links, and a "Read on Medium" link.
- A sidebar on wide screens, moving below the article on phones: about me,
  categories, tags, recent posts, and a search box that filters by title and tag.
**Data:** build articles.json from the files in posts/ and the links in links.txt.
Ask me for anything you can't find, such as publication dates. Don't invent them.
Also add an About page, a 404 page, a favicon, and Open Graph tags so links
shared on LinkedIn show a preview.
```

Copy the structure, not the words: what the home page shows, what happens when someone clicks, and where the data comes from.

A Pinterest-style board, a magazine layout, or a one-page résumé all work the same way.

The line “Don’t invent them” matters: without it, an agent may fill gaps with plausible-looking dates.

Kiro turns the prompt into requirements, a design, and tasks, following `design.md`. In the design, check two decisions:

*   **How the masonry grid works.** The usual approach is CSS columns: `columns: 4 260px` on the grid and `break-inside: avoid` on each card. It works in every browser, but cards fill each column from top to bottom, so your newest articles run down the left side instead of across the top. If you want left-to-right order, ask for a small JavaScript layout instead.
*   **How Markdown becomes HTML.** A small library such as marked converts Markdown in the browser. Ask Kiro to sanitize the result with a library such as DOMPurify, so HTML inside a post can’t run scripts on your site.

### Step 4: Preview and Polish

The site loads `articles.json` with `fetch()`, and browsers block that for pages opened straight from disk. Serve the folder with Python's built-in web server instead:

```
# In YOUR-USERNAME.github.io
python -m http.server 8000
```

Open [http://localhost:8000](http://localhost:8000/). To see the phone layout, press F12, then Ctrl+Shift+M for the device toolbar. Press Ctrl+C in the terminal to stop the server.

Polish the site in a normal vibe chat. The fastest feedback is a screenshot: press Win+Shift+S, drag the screenshot into the chat, and say what to change:

```
**This is my home page on a phone. The story circles are cut off on the right.
Make that row scroll sideways like Instagram's, and give the cards rounder corners.**
```

Small, specific requests with a screenshot get better results than “make it prettier”.

Commit each version you like with `git add .` and `git commit`, so you can always go back.

### Step 5: Write a Good README

`README.md` is the first thing people see when they open your repository on GitHub, often before they visit the site. Recruiters read it to judge how you explain your work.

A good README answers four questions on the first screen: what is this, where can I see it, how does it work, and how do I run or change it.

**Section:** What it says

*   **Title and one sentence:** What the project is, in plain words
*   **Live link and screenshot:** Proof that it works, without cloning anything
*   **Features:** What it does, as a short list
*   **How it works:** The main files and how data moves through them
*   **Run it locally:** Commands someone can copy and paste
*   **Update it:** How to add an article or change the profile
*   **Credits:**Tools, libraries, and image sources

A few habits make a README better:

1.  **Lead with what the project is, not how you built it.** “My portfolio and blog: 13 articles on the languages of the cloud” tells a reader more than “This project uses HTML, CSS, and JavaScript”.
2.  **Make commands copyable.** Put each command in a code block, exactly as you’d type it.
3.  **Show it.** Save a screenshot as `images/screenshot.png` and link it with a relative path, `![Home page](images/screenshot.png)`, so it shows on GitHub and on your laptop.
4.  **Keep it current.** Update the README in the same commit as the change it describes.

Kiro can write the first draft, because it can read every file in the repository:

```
Write README.md for this repository. Start with one sentence on what the site is
and the live link https://YOUR-USERNAME.github.io, then cover the features, how it
works (a table of the main files), how to run it locally, how to add an article,
and how it deploys. Only describe what's in the repository. Keep it under 80 lines.
```

“Only describe what’s in the repository” stops Kiro from listing features you haven’t built. Then edit the draft into your own voice: it’s your project’s front door.

### Step 6: Publish It

Add an empty `.nojekyll` file first. GitHub Pages normally runs every site through Jekyll, a site generator that leaves out files and folders whose names start with `_`.

This file tells Pages to publish your files exactly as they are.

```
# In YOUR-USERNAME.github.io
New-Item .nojekyll
git add .
git status        # website files only, nothing secret
git commit -m "First version of my portfolio site"
git remote add origin https://github.com/YOUR-USERNAME/YOUR-USERNAME.github.io.git
git push -u origin main
```

On GitHub, open the repository’s **Settings → Pages**. Under **Build and deployment**, set **Source** to **Deploy from a branch**, the branch to `main`, and the folder to `/ (root)`, then click **Save**. The **Actions** tab shows a run called "pages build and deployment". When it finishes, usually within a couple of minutes, your site is live at `https://YOUR-USERNAME.github.io`. Every later `git push` to `main` updates it.

Free comes with a few conditions: on a free GitHub plan the repository must be public, a published site can be at most 1 GB, and Pages isn’t meant for running an online shop. You can add a custom domain later on the same settings page.

### Optional: Add New Articles Automatically

When you publish more articles, ask Kiro:

```
Add a GitHub Actions workflow that runs once a day, reads my Medium RSS feed
(https://medium.com/feed/@YOUR-MEDIUM-USERNAME), and adds new articles to
articles.json. Commit only when something changed.
```

The workflow runs on GitHub’s servers. That matters because JavaScript on another website can’t read Medium’s feed, so your visitors’ browsers couldn’t fetch it. The feed only lists your 10 most recent posts, which is enough to catch new ones. If Medium blocks the workflow’s requests, keep adding articles to `articles.json` by hand.

### ⛔ End of Building Tutorial⛔

11 | The Ultimate Clean-Up
--------------------------

Over 13 posts you created servers, clusters, container images, keys, and accounts. Some of them bill by the hour whether you use them or not. Others are keys that open your accounts.

Finishing a project properly means removing what you no longer need, in an order that doesn’t leave anything behind.

**Keep:** your GitHub repositories, your new GitHub Pages site, your articles, and your Terraform code. The code lets you rebuild everything in minutes whenever you want it back.

### Step 1: Take Inventory with Kiro (Read-Only)

Before you delete anything, find out what’s still there. In a vibe chat in your `LanguagesPortfolio` workspace, ask:

```
List every AWS resource from my Cloud Languages series that could still cost money,
in every region. Use read-only commands only: AWS CLI commands that describe, list,
or get, plus terraform state list in terraform/. Don't create, change, or delete
anything. Check EC2 instances, EBS volumes and snapshots, Elastic IPs, security
groups, key pairs, load balancers, NAT gateways, EKS clusters, RDS databases,
and S3 buckets. Show the results as a table: region, type, ID, name or tags,
and how to delete it.
```

Approve each command only if it really is read-only, such as `aws ec2 describe-instances` or `aws s3 ls`.

Keep the table: it's your checklist for the steps below, and the deleting is yours to do.

### Step 2: Destroy What Terraform Built

```
# In LanguagesPortfolio\terraform
terraform destroy
terraform state list      # prints nothing once everything is gone
```

Read the plan before you type `yes`. For the Post 12 server it says `Plan: 0 to add, 0 to change, 7 to destroy.` If you built more with Kiro, the number is higher, but every item should be yours. If you split environments into separate folders (Post 12, Section 11), run `destroy` in each one.

Moved your state to S3 in Post 12, Section 9? Delete the bucket only after `destroy` succeeds, because the bucket holds the state. In the S3 console, select the bucket, click **Empty** to remove every version of every file, then click **Delete**.

### Step 3: Delete What You Built by Hand

Terraform only knows about what it created. The Post 8 server came from the AWS console, so delete it there. Each region is separate: check the region selector at the top of the console, or open **EC2 Global View** to see every region at once.

1.  **Instances:** select the Post 8 instance, then **Instance state → Terminate instance**. Terminated instances stay visible for about an hour. That’s normal.
2.  **Elastic IPs:** release any you allocated. AWS charges for public IPv4 addresses, including unused ones.
3.  **Volumes:** delete any whose state is **Available**. They’re disks that no instance uses.
4.  **Snapshots** and **AMIs:** delete any you made.
5.  **Key pairs:** delete `portfolio-key`.
6.  **Security groups:** delete the group created with the Post 8 instance, often named `launch-wizard-1`. You can't delete a VPC's `default` group, and you don't need to.

If you created an RDS database for Post 9, open the RDS console, select it, and choose **Actions → Delete**. Only take a final snapshot if you want to keep the data, because snapshots are billed too.

### Step 4: Kubernetes

```
kubectl delete namespace languages-portfolio   # every object in it, including the MySQL data
minikube delete                                # Minikube only: removes the whole local cluster
```

Deleting the namespace removes the Deployments, Services, Jobs, Secrets, and the MySQL volume claim from Posts 10 and 11 in one step. On Docker Desktop, you can also turn Kubernetes off under **Settings → Kubernetes**.

If you created a cloud cluster in Post 10, delete it now. Managed clusters bill by the hour, and they’re usually the most expensive thing in this series:

```
eksctl delete cluster --name portfolio-cluster --region YOUR-REGION     # AWS EKS
gcloud container clusters delete portfolio-cluster --zone us-central1-a  # Google GKE
az group delete --name portfolio-rg
```

### Step 5: Docker on Your Laptop

```
docker ps -a                                  # list containers
docker rm -f frontend backend-service         # remove any you ran locally
docker network rm portfolio
# Preview, then remove, every image with "portfolio" in its name
docker image ls --format "{{.Repository}}:{{.Tag}}" | Where-Object { $_ -like "*portfolio*" }
docker image ls --format "{{.Repository}}:{{.Tag}}" | Where-Object { $_ -like "*portfolio*" } | ForEach-Object { docker image rm $_ }
docker builder prune      # build cache, often several GB
docker system df          # what Docker still uses
```

Base images such as `python`, `nginx`, `mysql`, and `eclipse-temurin` don't match that filter. Remove them with `docker image rm` if no other project uses them. `docker system prune -a --volumes` removes every unused container, image, network, and volume on your machine, including other projects' data, so only use it if this series is all you've used Docker for.

### Step 6: Keys, Tokens, and Access

Deleting resources stops the bills. Deleting keys closes the doors.

*   **AWS access keys:** in IAM, open the user Terraform used, go to **Security credentials**, then deactivate and delete its access key. If you created the user only for this series, delete the user. Then remove the key from your laptop: delete its profile from `$HOME.aws\credentials`, or the whole `$HOME.aws` folder if AWS was only for this series.
*   **The old Pages deployment:** `LanguagesPortfolio/.github/workflows/deploy.yml` publishes the entire repository, including the `k8s` folder, to GitHub Pages on every push. Your new site replaces it, so delete the workflow file and push, then delete the repository's `gh-pages` branch to take that copy offline.
*   **Passwords that reached Git:** if `k8s/secrets.yaml`, or any file with a real password, was ever pushed, deleting the file isn't enough, because Git history keeps it. Change that password wherever it's used.
*   **GitHub tokens:** delete any personal access tokens you created for cloning, under **Settings → Developer settings → Personal access tokens**.
*   **SSH keys:** remove each server from your known hosts, then delete the keys, including the Post 8 `portfolio-key.pem` wherever you saved it:

```
ssh-keygen -R YOUR-SERVER-IP
  Remove-Item "$HOME\.ssh\portfolio-key", "$HOME\.ssh\portfolio-key.pub"
```

*   **Kiro:** remove MCP servers you no longer use from `.kiro/settings/mcp.json`, and review the commands you've trusted in Kiro's settings.

### Step 7: Local Files

Only after `terraform destroy` has succeeded:

```
# In LanguagesPortfolio
Remove-Item -Recurse -Force terraform\.terraform -ErrorAction SilentlyContinue   # provider download, hundreds of MB
Remove-Item terraform\terraform.tfstate* -ErrorAction SilentlyContinue            # state can hold sensitive values
Remove-Item -Recurse -Force backend\venv, java\insights\target, java\health-check\classes -ErrorAction SilentlyContinue
Remove-Item java\health-check\*.jar -ErrorAction SilentlyContinue
```

Keep the `.tf` files, `cloud-init.yaml`, and `.terraform.lock.hcl`. With them, `terraform init` and `terraform apply` rebuild the server whenever you want it back. `terraform.tfvars` only holds your IP address, so you can keep it too.

### Step 8: Check the Bill

Billing data lags behind, so check now and again in two days:

1.  **Billing and Cost Management → Bills:** this month’s charges by service. They should stop growing.
2.  **Resource Groups & Tag Editor → Tag Editor:** choose **All regions** and **All supported resource types**, add the tag `Project` with the value `languages-portfolio`, and click **Search resources**. Nothing should come back, because everything Terraform created carried that tag.
3.  **EC2 Global View:** no instances, volumes, or Elastic IPs in any region.
4.  **AWS Budgets:** keep a small monthly budget alert, so anything you missed emails you before it gets expensive.

If you used Google Cloud or Azure , check their billing pages the same way. Closing a cloud account is optional. Deleting the resources is what stops the charges.

When every check comes back clean, you’re done. Nothing is billing you, no forgotten key opens your accounts, and your portfolio lives on at `https://YOUR-USERNAME.github.io`, for free.

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Agentic IDEs in the Cloud](https://ntombizakhona.medium.com/agentic-ides-9c49fe3e3cc4?postPublishedType=repub)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**09 November 2026**
