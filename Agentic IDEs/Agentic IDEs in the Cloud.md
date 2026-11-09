Agentic IDEs
============

The Final Frontier Of Cloud Engineering
---------------------------------------

You’ve learned 12 languages, _but there’s a thirteenth ghost!_

You can write frontend code, backend APIs, SQL queries, Kubernetes manifests, and Terraform configurations. You can deploy to the cloud, manage infrastructure as code, and orchestrate containers at scale.

But, **what if you didn’t have to do all of that manually…ever again?**

**Think about it:**

*   _Writing boilerplate Kubernetes YAML?_ → **An AI agent could generate it from a description**
*   _Debugging a Python traceback?_ → **An agent could read the error, find the file, and fix it**
*   _Setting up Terraform for a new environment?_ → **Describe what you need, agent builds it**
*   _Wiring up a new API endpoint?_ → **Agent scaffolds the route, service, model, and tests**

**This is the age of Agentic IDEs.**

Not autocomplete.

Not suggestions in a grey font.

We’re talking about AI agents that live inside your development environment, understand your entire codebase, use tools autonomously, and execute multi-step tasks on your behalf.

This is the thirteenth ghost. The invisible force that ties all twelve languages together and amplifies everything you’ve learned.

Topics
------

1.  From Text Editors To Agentic IDEs
2.  The Anatomy of An Agentic IDE
3.  Kiro
4.  The 12 Languages Through The Agentic Lens
5.  Practical Agentic Workflows
6.  The Agentic IDE Landscape
7.  The Future Of Development
8.  Use An Agentic IDE to Improve Your Portfolio
9.  Configure Kiro to Build Your Cloud Infrastructure
10.  (Re) Build Your Portfolio Site with Kiro on GitHub Pages
11.  The Ultimate Clean Up
12.  The Compound Effect
13.  Bonus: Kiroween 2026!

1 | From Text Editors to Agentic IDEs
-------------------------------------

### The Evolution

During the **1970s to the 1990s, the era of Text Editors** dominated software development. Using basic tools like Vim, Emacs, and Notepad, developers simply typed out their code with no assistance, operating under a “good luck” mentality with zero automation.

> **_You type._**
> 
> **_That’s it._**
> 
> **_Good luck!_**

By the **2000s, Integrated Development Environments (IDEs)** like Eclipse, IntelliJ, and Visual Studio emerged. These platforms truly understood a language’s grammar, introducing game-changing features such as syntax highlighting, robust debugging, and refactoring tools.

> **_The IDE understands your language’s grammar_**

The timeline advanced between **2015 and 2020 with the rise of Smart Autocomplete** utilities like IntelliSense, TabNine, and Kite. While these tools could predict the next few tokens a developer was about to type, their capabilities were still fundamentally limited to just filling in the blanks.

> **_Predicts the next few tokens you’ll type, still just filling in blanks_**

From **2021 to 2023, AI Code Assistants** like GitHub Copilot and Amazon CodeWhisperer took center stage. They achieved the impressive feat of generating entire functions straight from natural language comments, though they remained strictly reactive systems that waited for human input.

> **_Generates whole functions from comments._**
> 
> **_Impressive, but reactive: waits for you to type_**

From **2024 to the present day, Agentic IDEs** like Kiro, Cursor, AntiGravity, and Claude Code have completely redefined the landscape. These platforms embed active AI agents that understand an entire codebase, use local tools like the file system and terminal, and execute multi-step plans autonomously.

They can read documentation to learn patterns, create, test, and deploy code, and dynamically respond to system events. This final shift marks a transition away from autocomplete, effectively placing a fully functioning junior developer directly inside the IDE.

```
**✓ Understand your ENTIRE codebase                                     
✓ Use tools (file system, terminal, browser, APIs)                    
✓ Execute multi-step plans autonomously                               
✓ Read documentation and learn your patterns                          
✓ Create, edit, test, and deploy code                                 
✓ Respond to events (file saves, errors, deployments)**
```

### What Makes An IDE Agentic?

In modern software development, the word “agent” carries a very specific meaning. An AI agent is far more than a simple chatbot, representing a shift from a tool that merely suggests code to one that actively executes it.

While chatbots, AI assistants, and agentic IDEs can all answer general programming questions, their capabilities diverge sharply when it comes to taking action.

Standard chatbots are highly restricted, as they cannot generate code snippets, read or edit your files, run terminal commands, or use external tools.

They also lack the ability to handle multi-step planning, react to system events, follow project-specific guidelines, or execute structured feature development.

AI assistants offer a slight step forward by generating code snippets and reading a limited portion of your files, but they still cannot directly edit files, interact with the terminal, or utilize external APIs and browsers.

Furthermore, like chatbots, they are entirely incapable of autonomous planning, event reaction, project-specific rule adherence, or structured feature development.

In contrast, an agentic IDE possesses the full spectrum of these advanced capabilities. It maintains an understanding of your entire codebase, allows the AI to directly edit files, and grants it the power to run terminal commands.

By leveraging protocols like the Model Context Protocol (MCP), an agentic IDE can connect to external tools like APIs and web browsers. It operates with a high degree of **autonomy**, utilising multi-step planning, reacting dynamically to system events via hooks, and adhering closely to project-specific steering rules.

Finally, it can manage structured feature development from the ground up based on your exact specifications. Ultimately, the defining difference is that an agentic IDE does not just suggest code: _it acts._

2 | The Anatomy of an Agentic IDE
---------------------------------

### Core Components

```
┌─────────────────────────────────────────────────────────────────────────┐
│                  **  AGENTIC IDE ARCHITECTURE **                            │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   ┌─────────────────────────────────────────────────────────────┐       │
│   │                 **    THE AGENT **                              │       │
│   │                                                             │       │
│   │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐     │       │
│   │  │  **LLM**     │  │ **Context**  │  │  **Tools**   │  │ **Memory**   │     │       │
│   │  │  **Brain**   │  │ **Window**   │  │          │  │          │     │       │
│   │  │          │  │          │  │ • Files  │  │ • Chat   │     │       │
│   │  │ Reasons  │  │ Sees     │  │ • Shell  │  │   history│     │       │
│   │  │ about    │  │ relevant │  │ • Search │  │ • Project│     │       │
│   │  │ code     │  │ code     │  │ • Web    │  │   context│     │       │
│   │  │          │  │          │  │ • MCP    │  │ • Specs  │     │       │
│   │  └──────────┘  └──────────┘  └──────────┘  └──────────┘     │       │
│   └─────────────────────────────────────────────────────────────┘       │
│                              │                                          │
│                              ▼                                          │
│   ┌─────────────────────────────────────────────────────────────┐       │
│   │                   **YOUR PROJECT**                              │       │
│   │                                                             │       │
│   │  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐           │       │
│   │  │ .py  │  │ .tf  │  │ .yaml│  │ .html│  │ .sql │           │       │
│   │  │files │  │files │  │files │  │files │  │files │           │       │
│   │  └──────┘  └──────┘  └──────┘  └──────┘  └──────┘           │       │
│   │                                                             │       │
│   │  The agent reads, writes, and understands ALL of these      │       │
│   └─────────────────────────────────────────────────────────────┘       │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

The underlying structure of an **agentic IDE architecture relies on a continuous loop between a central agent and your project environment**.

At the core of the system sits the agent, which is powered by four primary pillars working in tandem: an LLM brain, a context window, an array of functional tools, and a dedicated memory bank.

The LLM brain serves as the reasoning engine that actively analyzes and thinks about the code. It is fed directly by the context window, which acts as the system’s eyes to ensure the agent always sees the most relevant portions of the codebase.

To execute its decisions, the agent utilises a rich suite of built-in tools. These tools grant it the power to manipulate files, interact directly with the shell, execute searches, browse the web, and connect to external systems via the Model Context Protocol (MCP).

Supporting this execution is the agent’s memory bank, which keeps track of active chat histories, overarching project context, and precise system specifications.

This sophisticated agent architecture is focused entirely downward onto your project.

Rather than being restricted to specific languages or configurations, the agent natively interacts with a diverse ecosystem of project components, including Python scripts (`.py`), Terraform infrastructure files (`.tf`), configuration files (`.yaml`), front-end mockups (`.html`), and database schemas (`.sql`).

Ultimately, the agent operates with a holistic view of this workspace, maintaining the unique ability to read, write, and deeply understand all of these files simultaneously to manage your development workflow.

3 | Kiro
--------

Let’s look at how an agentic IDE like Kiro works in practice. Kiro is built by Amazon and represents the agentic IDE philosophy: an AI partner that understands your project deeply and can act on it.

### Specs (Structured Feature Development)

Instead of jumping straight into code, Kiro uses Specs to formalize the design process:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    **THE SPEC WORKFLOW**                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   **1. REQUIREMENTS**                                                       │
│      "I need a contact form that saves to the database"                 │
│      → Agent generates user stories and acceptance criteria             │
│                                                                         │
│  ** 2. DESIGN  **                                                           │
│      → Agent proposes architecture, API endpoints, database schema      │
│      → You review and refine                                            │
│                                                                         │
│  ** 3. IMPLEMENTATION TASKS**                                               │
│      → Agent breaks the work into ordered tasks                         │
│      → Each task is a concrete, testable unit of work                   │
│                                                                         │
│   **4. EXECUTION**                                                         │
│      → Agent works through tasks one by one                             │
│      → You review each step, provide feedback                           │
│                                                                         │
│   _This is how professional software teams work, _                        │
│   _now with an AI partner at every step._                                 │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

> **_This is where your_** [**_English_**](https://medium.com/english-in-the-cloud-a312c095acc9?postPublishedType=repub) **_skills pay massive dividends. The clearer you describe what you want, the better the spec, the better the code._**

### Steering (Teaching the Agent Your Standards)

Every team has conventions. Steering files let you encode them:

```
<!-- .kiro/steering/coding-standards.md -->
## Our Coding Standards
- Use Python 3.12+ with type hints on all functions
- Follow PEP 8 naming conventions
- All API endpoints must return JSON with a consistent envelope:
  { "status": "success|error", "data": ..., "message": "..." }
- Database queries must use parameterized statements (no string concatenation)
- All Terraform resources must include Name, Environment, and ManagedBy tags
- Kubernetes manifests must include resource limits and health checks
```

> **_The agent reads these before every interaction. It’s like onboarding a new team member except they actually remember everything._**

### Hooks (Event-Driven Automation)

Hooks run a shell command, or send Kiro a prompt, when something happens: you save a file, the agent is about to use a tool, or a spec task finishes.

Since Kiro IDE 1.0, hooks live in JSON files in `.kiro/hooks/`, and the same file works in the IDE and the CLI:

```
{
  "version": "v1",
  "hooks": [    {
      "name": "Validate Terraform on save",
      "trigger": "PostFileSave",
      "matcher": "\\.tf$",
      "action": { "type": "command", "command": "terraform -chdir=terraform validate" }
    },
    {
      "name": "Check Kubernetes manifests",
      "trigger": "PostFileSave",
      "matcher": "k8s.*\\.ya?ml$",
      "action": { "type": "agent", "prompt": "Check that the saved Kubernetes manifest sets resource limits and health checks." }
    }
  ]
}
```

*   `**trigger**` is the event. Others include `PreToolUse`, which runs before the agent uses a tool and can block it, and `PostTaskExec`, which runs after a spec task finishes.
*   `**matcher**` is a regular expression. For file events, Kiro tests it against the file's path. `\.tf$` is the expression `.tf$`, "ends in `.tf`", with its backslash doubled, as JSON requires.
*   `**action**` is either a `command`, which runs in a shell, or an `agent` prompt, which Kiro acts on.

> **_Think of hooks as GitHub Actions, but inside your IDE, running as you work. You rarely write hook files by hand, though. Describe the hook in Kiro's Agent Hooks panel and Kiro generates the file._**

### MCP (Extending the Agent’s Reach)

The Model Context Protocol (MCP) lets agentic IDEs connect to external tools and services:

```
                    **           MCP: EXTENDING THE AGENT **                             │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│ **  Agent**                                                                 │
│     │                                                                   │
│     ├── MCP Server: AWS Documentation                                   │
│     │   → Agent can search and read AWS docs in real-time               │
│     │                                                                   │
│     ├── MCP Server: Database                                            │
│     │   → Agent can query your database schema directly                 │
│     │                                                                   │
│     ├── MCP Server: Kubernetes                                          │
│     │   → Agent can check pod status, read logs                         │
│     │                                                                   │
│     ├── MCP Server: GitHub                                              │
│     │   → Agent can create PRs, read issues                             │
│     │                                                                   │
│     └── MCP Server: Your Custom API                                     │
│         → Agent can interact with your own services                     │
│                                                                         │
│  _ MCP is an open standard. Anyone can build servers for it. _            │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Beyond the IDE

Kiro has grown well beyond the IDE features above. These all work from the same rules and repositories:

1.  **Kiro IDE:** Hands-on, code-heavy building, as in this post
2.  **Kiro CLI:** The agent in your terminal, for scripts, CI/CD, and DevOps automation
3.  **Kiro Web:** Longer, more autonomous sessions in the browser, including specs, on GitHub or GitLab repositories
4.  **Kiro Mobile:** Keeps your cloud sessions moving from your phone
5.  **Kiro Crew:** An open-source personal agent that runs on your own hardware, remembers across sessions, and takes requests from its dashboard, the CLI, Slack, or Discord
6.  **Powers:** Packages of MCP servers, steering, and best practices that Kiro loads when a task needs them, from partners such as Figma, Postman, Stripe, Supabase, and Netlify
7.  **Custom agents:** Specialized agents with their own instructions and tools that you can switch between
8.  **Property-based testing:** Tests your code with many generated inputs to check that it matches the properties in your spec
9.  **Checkpoints:** Rewind changes the agent made

> **_Kiro ships often, AI advances and evolves rapidly afterall._**
> 
> **_The_** [**_Kiro blog_**](https://kiro.dev/blog/) **_and_** [**_changelog_**](https://kiro.dev/changelog/) **_show what’s new, and_** [**_One year of Kiro_**](https://kiro.dev/blog/one-year/) **_recaps how it got here._**

4 | The 12 Languages Through The Agentic Lens
---------------------------------------------

Here’s the beautiful thing: every language you’ve learned in this series becomes more powerful with an agentic IDE. The agent doesn’t replace your knowledge. **It amplifies it.**

```
                       ** HOW AGENTIC IDEs AMPLIFY YOUR 12 LANGUAGES   **              
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ** 01 English**      → Your prompts ARE the code now. _Clarity = quality _   │
│   **02 Mathematics**  → _Agent handles computation, you handle logic_         │
│   **03 HTML**         → _"Add a responsive nav with accessibility"_ → done    │
│   **04 CSS**          → _"Make it dark mode with smooth transitions"_ → done  │
│   **05 JavaScript**   → _"Add form validation with error messages"_ → done    │
│   **06 Python**       → _"Create a Flask endpoint for /api/users"_ → done     │
│   **07 Git**          → _Agent commits, branches, creates PRs for you_        │
│   **08 Linux**        → _Agent runs shell commands, reads logs_               │
│   **09 SQL**          → _"Add a migration for the new users table"_ → done    │
│   **10 Kubernetes**   → _"Scale the backend to 5 replicas"_ → YAML generated  │
│   **11 Java**         → _"Add a health check endpoint"_ → done                │
│   **12 Terraform**    → _"Add a staging environment"_ → HCL generated         │ 
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

> **_But you still need to understand what the agent produces._**
> 
> **_You’re the architect._**
> 
> **_The agent is the builder._**

### ⚠️The Critical Point: You Still Need the Knowledge

This is worth emphasising repeatedly.

Agentic IDEs don’t make learning obsolete. They make it more important:

*   You need to **review** generated [_Terraform_](https://medium.com/terraform-in-the-cloud-337b9bdbbc0b) to catch security issues
*   You need to **understand** [_Kubernetes_](https://medium.com/kubernetes-in-the-cloud-987d8d5c50ee) manifests to debug production problems
*   You need to **read** [_SQL_](https://medium.com/sql-in-the-cloud-a2942dc80a32) queries to verify they’re performant
*   You need to **evaluate** [_Python_](https://medium.com/python-in-the-cloud-1462f61e3293) code to ensure it handles edge cases
*   You need [_English_](https://medium.com/english-in-the-cloud-a312c095acc9) **fluency** to write prompts that produce quality output

The agent is a force multiplier. But zero times anything is still zero.

5 | Practical Agentic Workflows
-------------------------------

### Workflow 1: Feature Development with Specs

```
**YOU:** "I need to add a blog section to my portfolio. It should pull posts
     from the database, support markdown rendering, and have pagination."
**AGENT (Spec - Requirements):**
  → User Story: As a visitor, I can browse blog posts with pagination
  → User Story: As an admin, I can create posts in markdown
  → Acceptance Criteria: Posts display with rendered markdown
  → Acceptance Criteria: Pagination shows 10 posts per page
**AGENT (Spec - Design):**
  → Database: blog_posts table (id, title, slug, content, published_at)
  → API: GET /api/posts?page=1&limit=10
  → API: GET /api/posts/:slug
  → Frontend: /blog route with post list and detail views
**AGENT (Spec - Tasks):**
  → Task 1: Create database migration for blog_posts table
  → Task 2: Add Flask API endpoints for posts
  → Task 3: Create blog list page with pagination
  → Task 4: Create blog detail page with markdown rendering
  → Task 5: Add navigation link to blog section
**YOU:** "Looks good, execute the tasks."
**AGENT:** *creates files, writes code, runs tests, commits*
```

### Workflow 2: Debugging with Context

```
**YOU:** "The /api/contacts endpoint is returning a 500 error"
**AGENT:**
  → Reads app.py, finds the contacts route
  → Reads the error logs from the terminal
  → Identifies: database connection string is wrong in production config
  → Reads config.py, finds the environment variable reference
  → Checks .env file, spots the typo
  → Fixes the typo
  → Runs the test suite to verify
  → "Fixed. The DATABASE_URL had a typo in the hostname."
```

### Workflow 3: Infrastructure Changes

```
**YOU:** "We need a Redis cache for the portfolio API. Add it to our
     Terraform and Kubernetes configs."
**AGENT:**
  → Adds aws_elasticache_cluster resource to Terraform
  → Adds security group rules for Redis port 6379
  → Creates Kubernetes ConfigMap with Redis connection string
  → Updates the backend deployment to include Redis env vars
  → Updates Python requirements.txt with redis package
  → Adds caching logic to the Flask API
  → "Done. Run terraform apply to create the Redis cluster,
     then kubectl apply to update the deployment."
```

6 | The Agentic IDE Landscape
-----------------------------

### Current Players In The Game

> **_This is a representative list, not an exhaustive one. With AI, what is today, might not be tomorrow, and whatever is coming is on the way…_**

It is currently November 2026, the category includes full IDEs, extensions, terminal agents, cloud agents, and command centers that coordinate several agents. Product names below link to official sources.

### [**Kiro**](https://kiro.dev/docs/)

is Amazon Web Services’ spec-driven development platform. It runs in the IDE, CLI, Web, mobile, and Crew, with steering, hooks, specs, and powers shared across those surfaces.

### [**Cursor**](https://cursor.com/blog/cursor-3)

built by Anysphere, has expanded from its VS Code-based editor into an agent-first desktop workspace connected to its CLI, web and mobile clients, and cloud agents. Its current focus is parallel work across repositories and smooth handoffs between local and cloud sessions.

### [**Google Antigravity**](https://antigravity.google/)

combines an agentic IDE with an app, CLI, editor extensions, and SDK. Its Manager Surface coordinates multiple agents in parallel, while artifacts and browser-based verification help you review work performed across the editor, terminal, and browser.

### [**Devin Desktop**](https://devin.ai/blog/windsurf-is-now-devin-desktop)

is Cognition’s successor to Windsurf, which changed names on June 2, 2026. It retains a full desktop IDE but makes the Agent Command Center its main surface, connects to Devin Cloud, CLI, and Review, and supports third-party agents through the Agent Client Protocol. Devin Local has succeeded Cascade as its primary local agent.

### [**Claude Code**](https://www.anthropic.com/product/claude-code)

is Anthropic’s coding agent for terminals, IDEs, the desktop app, and the browser. It is designed for substantial, long-running codebase work and can divide tasks among subagents or agent teams while you review its progress.

### [**GitHub Copilot**](https://github.com/features/copilot)

now reaches beyond autocomplete across GitHub, major IDEs, the CLI, its own app, mobile devices, and cloud sessions. Its strongest distinction is its GitHub-native workflow, which connects issues, coding agents, code review, pull requests, and sessions that continue across devices.

### [**OpenAI Codex**](https://openai.com/codex/)

works through its app, IDE extension, CLI, cloud service, and code-review integrations. It lets developers delegate work to several agents in parallel and move between local and cloud execution through one connected workflow.

### [**JetBrains Junie**](https://www.jetbrains.com/help/ai-assistant/junie-agent.html)

is integrated with JetBrains IDEs and their code intelligence. It can plan before editing, pursue an autonomous goal, run tests and commands, and use a live debugger to inspect a running program rather than reasoning only from source files.

### [**Replit Agent**](https://docs.replit.com/features/agent/overview)

runs in Replit’s browser-based cloud workspace. It focuses on taking an idea through planning, visual design, coding, data setup, testing, and deployment without requiring the user to assemble separate infrastructure tools.

7 | The Future of Development
-----------------------------

### What’s Changing

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    **THE SHIFT IN SOFTWARE DEVELOPMENT **                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ** BEFORE** (You do everything):                                           │
│   ────────────────────────────                                          │
│   Think → Design → Code → Test → Debug → Deploy → Monitor               │
│   100% manual effort at every step                                      │
│                                                                         │
│   **NOW** (Agent-assisted):                                                 │
│   ────────────────────                                                  │
│   Think → Describe → Review → Guide → Approve → Monitor                 │
│                                                                         │
│   You focus on:              Agent handles:                             │
│   • What to build            • How to build it                          │
│   • Architecture decisions   • Boilerplate and scaffolding              │
│   • Code review              • First drafts of code                     │
│   • Quality standards        • Repetitive tasks                         │
│   • Business logic           • Syntax and patterns                      │
│   • Security review          • Documentation                            │
│                                                                         │
│  ** Your role shifts from WRITER to ARCHITECT + REVIEWER.**                 │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### ⚠️ What’s NOT Changing

*   **You still need** to understand what good code looks like
*   **You still need** to know your cloud services and their trade-offs
*   **You still need** to think about security, performance, and cost
*   **You still need** to debug when things go wrong in production at 3:17 AM
*   **You still need** to communicate with _humans_ on your team
*   **You still need** to make architectural decisions the agent can’t

_The developers who thrive in the agentic era won’t be the ones who memorised the most syntax. They’ll be the ones who understand systems deeply, communicate clearly, and know how to direct an AI agent effectively._

12 | The Compound Effect
------------------------

### Your Complete Cloud Toolkit

Look at what you’ve built across 13 blog posts!

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    **YOUR COMPLETE CLOUD TOOLKIT**                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   **COMMUNICATION LAYER**                                                   │
│   ┌──────────────────────────────────────────────────────────┐          │
│   │  **01** English        → _How you think, communicate, prompt_  │          │
│   │  **02** Mathematics    → _How you reason and solve problems_   │          │
│   └──────────────────────────────────────────────────────────┘          │
│                                                                         │
│   APPLICATION LAYER                                                     │
│   ┌──────────────────────────────────────────────────────────┐          │
│   │  **03** HTML           → _Structure_                           │          │
│   │  **04** CSS            → _Presentation_                        │          │
│   │  **05** JavaScript     → _Interactivity_                       │          │
│   │  **06** Python         → _Backend logic_                       │          │
│   │  **11** Java           → _Enterprise systems_                  │          │
│   └──────────────────────────────────────────────────────────┘          │
│                                                                         │
│   DATA LAYER                                                            │
│   ┌──────────────────────────────────────────────────────────┐          │
│   │  **09** SQL            → _Data storage and retrieval _         │          │
│   └──────────────────────────────────────────────────────────┘          │
│                                                                         │
│   INFRASTRUCTURE LAYER                                                  │
│   ┌──────────────────────────────────────────────────────────┐          │
│   │  **07** Git            → _Version control and collaboration_   │          │
│   │  **08** Linux          → _The cloud operating system _         │          │
│   │  **10** Kubernetes     → _Container orchestration_             │          │
│   │  **12** Terraform      → _Infrastructure as Code_              │          │
│   └──────────────────────────────────────────────────────────┘          │
│                                                                         │
│   MULTIPLIER LAYER                                                      │
│   ┌──────────────────────────────────────────────────────────┐          │
│   │  **13** Agentic IDEs   → _AI that amplifies ALL of the above _ │          │
│   └──────────────────────────────────────────────────────────┘          │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

> **_Each language builds on the ones before it. And the thirteenth ghost the agentic IDE doesn’t add to the stack. It multiplies it._**

13 | Bonus: Kiroween 2026,
--------------------------

### ⚠️ For Readers in Real Time

> **_Reading this before Friday, December 4, 2026 at 2:00 PM Pacific Time? Then you can enter Kiroween 2026, Kiro’s annual Halloween-themed hackathon on_** [**_Devpost_**](https://kiroween.devpost.com/)**_. It has more than $100,000 in prizes, and first place wins $30,000. The judges score how well you use Kiro, and this post has given you plenty to use: steering, specs, hooks, MCP, and a good README._**

### The Rules That Matter Most

*   **Start something new.** Only projects built during the submission period, October 30 to December 4, 2026, are eligible. A portfolio you finished before October 30 doesn’t count, and neither do Kiroween 2025 projects.
*   **Build it with Kiro:** the IDE, CLI, Web, or Kiro Crew.
*   **Work alone or in a team** of up to three people.
*   **Claim free credits.** Request 4,000 free Kiro credits on the hackathon’s Resources tab by December 1 at 12:00 PM PT.

> **_Reading this after December 4, 2026? Kiroween returns every Halloween…well, until it doesn’t._**

Finally, Final Conclusion of the Cloud Languages Series
-------------------------------------------------------

When we started this series with English, I told you that the first language of the cloud is human language. Now, thirteen posts later, that statement is more true than ever.

Agentic IDEs are the convergence point. They’re where your ability to think clearly **(English)**, reason logically **(Mathematics)**, structure information **(HTML)**, present it well **(CSS)**, make it interactive **(JavaScript)**, automate it **(Python)**, version it **(Git)**, deploy it **(Linux)**, store it **(SQL)**, orchestrate it **(Kubernetes)**, scale it **(Java)**, codify it **(Terraform)…** all comes together through natural language interaction with an AI agent.

> **_The thirteenth ghost isn’t something you learn like the other twelve. It’s something you experience. It’s the invisible layer that connects everything._**

_The developers who will define the next decade of cloud computing aren’t just the ones who can write code. They’re the ones who can think clearly, communicate precisely, understand systems deeply, and direct AI agents effectively._

You now have all twelve languages, and the thirteenth ghost. The cloud is yours. **Goodbye!**

Languages Portfolio Progress
----------------------------

✅ **01 — English**: Technical Communication And Prompt Engineering

✅ **02 — Mathematics:** Logic And Problem-Solving Fundamentals

✅ **03 — HTML:** Web Structure And Semantic Markup

✅ **04 — CSS:** Styling And Responsive Design

✅ **05 — JavaScript:** Interactive Web Development

✅ **06 — Python:** Backend Logic And Cloud Automation

✅ **07 — Git:** Version Control And Deployment

✅ **08 — Linux:** The Cloud Operating System

✅ **09 — SQL:** Cloud Data Management

✅ **10 — Kubernetes:** Container Orchestration At Scale

✅ **11 — Java:** Enterprise Cloud Development

✅ **12 — Terraform:** Infrastructure as Code

✅ **Bonus — Agentic IDEs:** AI-Powered Cloud Development

Additional Resources
--------------------

### [Kiro Documentation](https://kiro.dev/docs/)

> Kiro is an AI-powered development environment that helps you build software from prototype to production.
> 
> One [unified agent harness](https://kiro.dev/docs/how-kiro-works/) powers every surface (IDE, CLI, Web, and Mobile) so your configuration, specs, and steering work everywhere.

### [Hooks](https://kiro.dev/docs/hooks/)

> Hooks run shell commands or agent prompts automatically when specific events happen in your session — the agent modifies a file, invokes a tool, or completes a task. You define the trigger and the action; Kiro handles the execution.

### [Kiro Blog](https://kiro.dev/blog/)

> News, tutorials, updates from the Kiro team — find it all here.

### [What Is The Model Context Protocol?](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)

> MCP (Model Context Protocol) is an open-source standard for connecting AI applications to external systems.
> 
> Using MCP, AI applications like Claude or ChatGPT can connect to data sources (e.g. local files, databases), tools (e.g. search engines, calculators) and workflows (e.g. specialized prompts) — enabling them to access key information and perform tasks.Think of MCP like a USB-C port for AI applications.
> 
> Just as USB-C provides a standardized way to connect electronic devices, MCP provides a standardized way to connect AI applications to external systems.

### [GitHub Pages documentation](https://docs.github.com/en/pages)

> GitHub Pages turns any GitHub repository into a live website — no separate hosting required.
---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Agentic IDEs in the Cloud](https://ntombizakhona.medium.com/agentic-ides-9c49fe3e3cc4?postPublishedType=repub)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**09 November 2026**
