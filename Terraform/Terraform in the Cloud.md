Terraform in the Cloud
======================

Infrastructure as Code
----------------------

You’ve containerised your portfolio, orchestrated it with Kubernetes, and added a Java service next to your Python API.

But here’s the thing: **who created the cloud infrastructure your app runs on?**

**Think about it:**

*   _The server?_ → **You clicked through the AWS console to create it**
*   _The firewall rules (security groups)?_ → **Configured by hand**
*   _The database instance?_ → **Point-and-click**
*   Load balancers, DNS, SSL certificates? → **More clicking**

> **_Your application is automated. Your infrastructure is not._**

And that’s a problem:

*   _What if you need to recreate everything in a new region?_ → **Click through it all again**
*   _What if someone accidentally deletes a firewall rule?_ → **Hope you remember the settings**
*   _What if you need the same setup for staging AND production?_ → **Double the clicking**
*   _What if an auditor asks “what changed last Tuesday?”_ → **¯_(ツ)_/¯**

**This is where Terraform comes in.**

Terraform, from HashiCorp (now part of IBM), is an Infrastructure as Code (IaC) tool. Instead of clicking through cloud consoles, you write code that describes your infrastructure and Terraform builds it for you. Automatically. Repeatably. Every single time.

*   **Declarative**: You describe WHAT you want, not HOW to build it
*   **Cloud-Agnostic**: Works with AWS, Azure, GCP, and thousands of other providers
*   **Version Controlled**: Your infrastructure lives in Git, just like your app code
*   **Collaborative**: Teams can review infrastructure changes via pull requests
*   **Industry Standard**: Used by companies from startups to Fortune 500

_If Kubernetes is the operating system of the cloud, Terraform is the architect that designs and builds the data center._

Topics
------

1.  The ClickOps Problem
2.  Why Infrastructure as Code?
3.  How Terraform Works
4.  Install Terraform
5.  Local Deployment
6.  HCL Fundamentals
7.  Rebuild The Server as Code
8.  Deploy Your Portfolio on The Server
9.  Drift, Replacement, and Destroy
10.  State: Terraform’s Memory
11.  Modules: Reusable Infrastructure
12.  Best Practices and Troubleshooting

1 | The Clickops Problem
------------------------

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    THE CLICKOPS NIGHTMARE                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Without Terraform (ClickOps):                                         │
│   ─────────────────────────────                                         │
│                                                                         │
│   Developer: "I need a Kubernetes cluster with a database"              │
│                                                                         │
│   Step 1:  Log into AWS Console                                         │
│   Step 2:  Create VPC... click click click (15 minutes)                 │
│   Step 3:  Create subnets... click click click (10 minutes)             │
│   Step 4:  Configure security groups... click click (10 minutes)        │
│   Step 5:  Create EKS cluster... click click click (20 minutes)         │
│   Step 6:  Create RDS database... click click click (15 minutes)        │
│   Step 7:  Configure IAM roles... click click click (20 minutes)        │
│   Step 8:  Set up load balancer... click click (10 minutes)             │
│   Step 9:  Configure DNS... click click (5 minutes)                     │
│   Step 10: Document everything you did... (never happens 😅)            │
│                                                                         │
│   Total: ~2 hours of clicking, zero documentation, zero repeatability   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      WITH TERRAFORM                                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Developer: "I need a Kubernetes cluster with a database"              │
│                                                                         │
│   Step 1: Write infrastructure code (main.tf)                           │
│   Step 2: terraform plan  → "Here's what I'll create..."                │
│   Step 3: terraform apply → Infrastructure created automatically        │
│                                                                         │
│   ✓ Fully documented (the code IS the documentation)                    │
│   ✓ Version controlled in Git                                           │
│   ✓ Repeatable across environments                                      │
│   ✓ Reviewable via pull requests                                        │
│   ✓ Destroyable with one command: terraform destroy                     │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

2 | Why Infrastructure as Code?
-------------------------------

### Declarative vs. Imperative

```
IMPERATIVE (a script of steps)          DECLARATIVE (Terraform)
──────────────────────────────          ───────────────────────
1. Create security group "web"          "A security group named web should
2. Open port 80 to everyone              exist, with port 80 open to everyone."
Run it twice → error: "web" already     Run it twice → "No changes."
exists, or a duplicate group
```

Terraform compares what you declared with what actually exists and only does the difference. Running the same code twice is safe, because the second run changes nothing.

This property is called **idempotence**, and it’s what makes infrastructure code repeatable.

### IaC in the Cloud Ecosystem

1.  **Terraform** was created by HashiCorp (now part of IBM) and utilizes a declarative approach with HashiCorp Configuration Language (HCL) to offer multi-cloud support across AWS, Azure, GCP, and thousands of other providers.
2.  **OpenTofu** is hosted by the Linux Foundation as an open-source, multi-cloud fork of Terraform that also relies on a declarative approach using HCL.
3.  **CloudFormation** is a declarative tool created by AWS that uses YAML or JSON configurations and exclusively supports the AWS cloud platform.
4.  **Bicep** is a declarative domain-specific language developed by Microsoft designed specifically for provisioning resources within Azure.
5.  **Pulumi** is a multi-cloud provisioning platform created by Pulumi that enables infrastructure management using general-purpose programming languages like TypeScript, Python, and Go.
6.  **CDK** (Cloud Development Kit) was developed by AWS to allow infrastructure provisioning through general-purpose languages, focusing primarily on AWS support.

### The Terraform Advantage

**Terraform’s main advantage is reach:** one tool and one language cover every major cloud, plus services such as GitHub, Cloudflare, and Kubernetes.

Since 2023 Terraform has used the Business Source License rather than an open-source license, which is why the community created OpenTofu.

Both use the same language, so almost everything in this post also works with OpenTofu’s `tofu` command.

**What about Ansible?** Ansible mainly configures servers that already exist: installing packages, editing config files, restarting services. Terraform creates the infrastructure itself. Many teams use both. In this post, a small cloud-init file does Ansible’s job.

3 | How Terraform Works
-----------------------

### The Terraform Workflow

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    WRITE     │ ──▶      PLAN       ──▶       APPLY      ──▶    DESTROY   
│  .tf files   │     │   Preview    │     │    Create    │     │  Tear down   │
│              │     │   changes    │     │    infra     │     │    infra     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
   terraform            terraform            terraform            terraform
     init                 plan                 apply               destroy
```

Terraform itself doesn’t know how to talk to AWS.

**Providers** do: plugins that translate your code into API calls for one platform (`aws`, `azurerm`, `google`, `github`, `kubernetes`, and many more).

Every `terraform plan` does three things:

1.  **Reads your** `**.tf**` **files.** This is the _desired state_: what you want to exist.
2.  **Refreshes the state.** For each resource in its state file, Terraform asks the provider what exists in the cloud right now. This is the _actual state_.
3.  **Computes the difference** and shows it as a plan: what it would create, change, or destroy to make reality match your code.

`terraform apply` does the same, asks you to confirm, and then makes the changes.

### Key Concepts

*   A **Provider** is a plugin that communicates with a specific platform’s API, with common examples including `aws`, `azurerm`, and `google`.
*   A **Resource** represents an individual piece of infrastructure created and managed by Terraform, such as an `aws_instance` or an `aws_security_group`.
*   A **Data Source** allows Terraform to read external information without modifying it, such as looking up the latest available Ubuntu image.
*   A **Variable** serves as an input parameter to customise configurations, often used to define things like instance types or regions.
*   An **Output** is a value printed to the terminal after a successful deployment, such as the newly created server’s IP address.
*   A **Module** functions as a reusable group of configured resources designed to package a standard pattern, like a pre-built web server stack.
*   The **State** file (`terraform.tfstate`) serves as the official record of what infrastructure Terraform is actively managing.
*   The **Lock file** (`.terraform.lock.hcl`) locks down the exact provider versions and checksums to ensure consistency across environments.

4 | Install Terraform
---------------------

**Windows:**

```
winget install Hashicorp.Terraform
```

Close and reopen your terminal, then check:

```
terraform -version
```

If you don’t have `winget`, download the Windows zip from [developer.hashicorp.com/terraform/install](https://developer.hashicorp.com/terraform/install), extract `terraform.exe` into a folder such as `C:\Tools\terraform`, and add that folder to your `PATH`. Chocolatey users can run `choco install terraform`.

**macOS:** `brew tap hashicorp/tap` then `brew install hashicorp/tap/terraform`.

**Linux:** follow the APT or RPM instructions on the same install page.

Also install the **HashiCorp Terraform** extension in VS Code or Kiro for syntax highlighting, autocompletion, and format-on-save.

5 | Local Deployment
--------------------

Learn the workflow with the `local` provider first. Its resources are files on your own computer, so mistakes cost nothing.

Create a folder called `terraform-hello` anywhere outside your portfolio, and add `main.tf`:

```
terraform {
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.0"
    }
  }
}
variable "learner" {
  type    = string
  default = "Cloud Learner"
}
resource "local_file" "greeting" {
  filename = "${path.module}/greeting.txt"
  content  = "Hello, ${var.learner}! Terraform created this file."
}
```

**Now run the whole workflow:**

```
terraform init      # download the "local" provider
terraform plan      # preview: "Plan: 1 to add, 0 to change, 0 to destroy."
terraform apply     # type "yes" to create greeting.txt
Get-Content greeting.txt
```

*   `**init**` downloads the provider into a hidden `.terraform` folder and writes `.terraform.lock.hcl`, which pins the exact provider version so everyone on a team gets the same plugin.
*   `**plan**` shows `+ resource "local_file" "greeting"`. Nothing has happened yet. Values marked `(known after apply)` only exist once the resource is created.
*   `**apply**` creates the file and writes `terraform.tfstate`. Open it: it's JSON recording every resource Terraform created. That's how Terraform remembers what it owns.

Run `terraform apply` again. Terraform replies `No changes. Your infrastructure matches the configuration.` That's idempotence.

Now experiment:

```
# 1. Change an input
terraform apply -var="learner=YourName"
# 2. Change reality behind Terraform's back
Remove-Item greeting.txt
terraform plan
# 3. Clean up
terraform destroy
```

1.  The plan says `local_file.greeting must be replaced` and marks `content` with `# forces replacement`. This provider can't edit a file in place, so it deletes it and creates a new one. Cloud resources behave the same way: some settings change in place, others force a replacement. A replaced database loses its data, so **always read the plan before typing** `**yes**`**.**
2.  Terraform notices the file is gone and plans to create it again. A change made outside Terraform is called **drift**, and `plan` is how you find it.
3.  `destroy` deletes everything recorded in the state.

```

| Plan Symbol | Meaning                              |
|-------------|--------------------------------------|
| +           | Create                               |
| ~           | Update in place                      |
| -/+         | Destroy, then create a replacement   |
| -           | Destroy                              |
```

6 | HCL Fundamentals
--------------------

Terraform code is written in **HCL**. Everything is a **block**:

```
resource "local_file" "greeting" {
│         │            └── Name: your label, unique for this type
│         └── Type: provider ("local") + resource kind ("file")
└── Block type: terraform, provider, resource, data, variable, output, locals, module
  filename = "./greeting.txt"    ← Argument
}
```

You refer to a resource’s attributes as `TYPE.NAME.ATTRIBUTE`, for example `aws_key_pair.portfolio.key_name`.

Variables are `var.NAME`, data sources are `data.TYPE.NAME.ATTRIBUTE`, and module outputs are `module.NAME.OUTPUT`.

**References define the order.** If the server refers to the key pair, Terraform knows the key pair must exist first. It reads every reference, builds a **dependency graph**, and creates resources in the right order, running independent ones in parallel. You never write “step 1, step 2”.

**Folders matter, file names don’t.** Terraform loads every `.tf` file in a folder as one configuration. `main.tf`, `variables.tf`, and `outputs.tf` are just a convention. Each folder is a separate configuration with its own state.

```
# HCL quick reference
name     = "my-resource"                         # string
count    = 3                                     # number
enabled  = true                                  # boolean
ports    = [80, 443]                             # list
tags     = { Name = "web", Env = "dev" }         # map
label    = "${var.project_name}-web"             # string interpolation
size     = var.env == "prod" ? "t3.small" : "t3.micro"   # conditional
key      = file(pathexpand("~/.ssh/key.pub"))    # built-in functions
for_each = { http = 80, https = 443 }            # one resource per map entry
```

12 | Best Practices and Troubleshooting
---------------------------------------

### Do’s and Don’ts

*   Always **store team state remotely** (using S3 with `use_lockfile`) instead of keeping team state on a single laptop.
*   Make sure to **read the** `**terraform plan**` **before every** `**apply**` rather than applying changes blindly.
*   **Commit** `**.terraform.lock.hcl**` **and pin provider versions** so you don't let each machine pick its own random versions.
*   **Use variables for anything that differs between environments** instead of hardcoding values directly.
*   **Keep your credentials in the AWS CLI config or environment variables** and never put sensitive access keys in your `.tf` files.
*   Remember to **run** `**terraform fmt**` **and** `**terraform validate**` **before committing** rather than committing unchecked code to your repository.
*   **Tag every resource** using features like `default_tags` instead of leaving resources untagged.
*   **Change your infrastructure only through code** rather than fixing things manually in the AWS console and forgetting about them.
*   Remember to **run** `**terraform destroy**` **on learning environments** so you don't leave test servers running and incurring costs.

### .gitignore for Terraform

Add these lines to `LanguagesPortfolio/.gitignore`:

```
# Terraform
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
!example.tfvars
crash.log
crash.*.log
override.tf
override.tf.json
*_override.tf
*_override.tf.json
```

Don’t ignore `.terraform.lock.hcl`. It belongs in Git so everyone uses the same provider versions.

### Separate Environments

Calling a module twice from one folder, keeps staging and production in one state file. As projects grow, each environment gets its own folder and its own state, built from the same modules, so a mistake in staging can never touch production:

```
terraform/
├── modules/
│   └── web-server/
└── environments/
    ├── staging/        ← main.tf + terraform.tfvars, own state
    └── production/     ← same module, production settings, own state
```

### Troubleshooting

**Installation & Authentication Errors**

*   If the terminal reports `**terraform**` **is not recognised**, the likely cause is that the terminal was opened before installation was complete. You can fix this by opening a new terminal and checking the status with `Get-Command terraform`.
*   When you see `**No valid credential sources found**`, it means Terraform cannot locate your AWS credentials. To resolve this, run `aws configure` followed by `aws sts get-caller-identity`.
*   If you receive an `**UnauthorizedOperation**` error, your IAM user lacks the necessary permissions. Fix this by attaching `AmazonEC2FullAccess` to the user in the AWS console.

**Resource & Network Configuration**

*   When an `**Invalid value for variable**` **(**`**ssh_allowed_cidr**`**)** error occurs, you are likely missing the `/32` suffix or have incorrectly set it to `0.0.0.0/0`. The fix is to use your exact IP address followed by `/32`.
*   Seeing `**VPCIdNotSpecified: No default VPC for this user**` indicates that the default VPC for that specific region was deleted. You can fix it by recreating it using `aws ec2 create-default-vpc --region us-west-2`.
*   If you run into `**InvalidKeyPair.Duplicate**` **or** `**InvalidGroup.Duplicate**`, a key pair or security group with that exact name already exists (which often happens after losing a state file). You can resolve this by deleting the old resource in the AWS console or by changing the `project_name` variable in your code.
*   Getting **an error saying the instance type isn’t Free Tier eligible** means your account’s Free Tier benefits cover other types. To fix this, change your `instance_type` to a type that the EC2 console explicitly marks as eligible.

**SSH & Server Access**

*   If your **SSH hangs and then times out**, your public IP has likely changed. You can fix this by updating `ssh_allowed_cidr` in your `terraform.tfvars` file and running `terraform apply`.
*   Receiving a `**Permission denied (publickey)**` error means you are using the wrong user or the wrong key. Resolve this by logging in as the `ubuntu` user and ensuring you pass the private key (not the `.pub` file) to the `-i` flag.

**Docker & Container Operations**

*   If you see `**docker: command not found**` **on the server**, `cloud-init` hasn't finished execution or has failed entirely. Troubleshoot this by running `cloud-init status --long` and checking the logs in `/var/log/cloud-init-output.log`.
*   A `**permission denied**` **error on** `**docker.sock**` happens because the SSH session started before your user was successfully joined to the `docker` group. Simply log out and SSH back in to fix it.
*   When the `**frontend**` **container keeps restarting**, Nginx cannot locate the `backend-service`. You can fix this by checking `docker logs frontend` and ensuring that `backend-service` is running on the `portfolio` network.

**Terraform State Management**

*   Experiencing an `**Error acquiring the state lock**` means another Terraform run is currently in progress, or a previously crashed run left a stray lock behind. You should wait for it to finish, or if you are completely certain nothing else is running, use `terraform force-unlock` with the lock ID provided in the error message.

### Final Thoughts

Terraform closes a critical gap in your cloud journey. Before Terraform, you could build and deploy applications. But the infrastructure underneath was manual, undocumented, and fragile.

In this post, the server you clicked together became a handful of files. Terraform reads them, works out how reality differs, and makes it match. You can review those files in a pull request, rebuild the server in minutes, and delete it with one command.

Now your entire stack is code:

*   **Application code** → Git
*   **Container definitions** → Dockerfiles
*   **Orchestration** → Kubernetes YAML
*   **Infrastructure** → Terraform HCL

This is the modern cloud workflow:

1.  Everything is code.
2.  Everything is versioned.
3.  Everything is automated.

Concluding Remarks
------------------

You’ve now written every layer of a cloud application, from the content and logic to the containers and the infrastructure underneath. In the final post, we look at **Agentic IDEs**: AI assistants that read, write, and run this kind of code alongside you!

**So, are you ready for the final frontier?**

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

⬜ **Bonus — Agentic IDEs:** AI-Powered Cloud Development

Additional Resources
--------------------

### [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)

> Terraform is an infrastructure as code tool that lets you build, change, and version infrastructure safely and efficiently. This includes low-level components like compute instances, storage, and networking, as well as high-level components like DNS entries and SaaS features.

### [Cloud-init documentation](https://docs.cloud-init.io/en/latest/)

> `Cloud-init` is the _industry standard_ multi-distribution method for cross-platform cloud instance initialization. It is supported across all major public cloud providers, provisioning systems for private cloud infrastructure, and bare-metal installations.


---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Terraform in the Cloud](https://ntombizakhona.medium.com/terraform-in-the-cloud-337b9bdbbc0b)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**04 November 2026**
