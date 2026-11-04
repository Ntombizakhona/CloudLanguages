### 🏗️ Let’s Build

7 | Rebuild The Server as Code
------------------------------

We launched an EC2 server by clicking through the AWS console. Here’s how each click becomes code:

*   Instead of manually **choosing an Ubuntu image** in the console, you use a `data "aws_ami" "ubuntu"` block to automatically find the newest Ubuntu LTS image.
*   Instead of **creating a key pair and downloading a .pem file**, the `aws_key_pair` resource uploads a public key that you generate yourself securely.
*   Instead of clicking through settings to configure a **security group with SSH, port 80, 443, and 5000**, you define an `aws_security_group` resource alongside individual rules for each port.
*   Instead of walking through the wizard steps to **launch a t2.micro instance**, you simply declare an `aws_instance` resource in your configuration.
*   Instead of **manually SSHing into the server to install Docker**, you pass a cloud-init script directly into the `user_data` argument to handle installation automatically on first boot.

### What We’re Building

```
Your laptop                          AWS (us-west-2)
───────────                          ───────────────
terraform apply  ──────────────►     Default VPC
ssh, browser     ──────────────►       └── Security group "languages-portfolio-web"
                                             inbound: 22 (your IP only), 80, 443
                                             └── EC2: Ubuntu 24.04 LTS, t3.micro, Docker
                                                   ├── frontend (Nginx, port 80)
                                                   └── backend-service (Flask, port 5000)
```

Every AWS account has a **default VPC** in each region: a ready-made network with public subnets.

Using it keeps this build to seven resources that take a couple of minutes to create. Production setups usually add a custom VPC, a managed Kubernetes cluster (EKS), and a managed database (RDS).

Those are more resource blocks of the same kind, but they bill by the hour, so we start with one server.

We use Ubuntu 24.04 LTS, the newer long-term-support release.

### Prerequisites

**1.** [**An AWS user for Terraform.**](https://medium.com/amazon-web-services-a8e57a9c6084) Use an IAM user, never your root account. Attach the `AmazonEC2FullAccess` policy and `AmazonS3FullAccess` then create an access key for the CLI.

**2. The AWS CLI**, which stores credentials where Terraform can find them:

```
winget install Amazon.AWSCLI
# Reopen the terminal, then:
aws configure                  # access key, secret, region us-west-2, output json
aws sts get-caller-identity    # proves the credentials work
```

The credentials are saved in `$HOME.aws\credentials`, and Terraform's AWS provider reads that same file. **No keys ever go in your Terraform code.**

**3. An SSH key pair:**

```
New-Item -ItemType Directory -Force "$HOME\.ssh" | Out-Null
ssh-keygen -t ed25519 -f "$HOME\.ssh\portfolio-key" -C "languages-portfolio"
```

This creates `portfolio-key` (private, stays on your laptop) and `portfolio-key.pub` (public, goes to AWS).

**4. Your public IP address**, so only you can SSH in:

```
(Invoke-RestMethod https://checkip.amazonaws.com).Trim()
```

> **_💰 Cost:_** _This build runs one small EC2 instance with a 20 GB disk and a public IPv4 address. Depending on your account’s Free Tier, that’s free or a few cents per hour. everything._
> 
> **_An AWS Budgets alert is a good habit too._**

### Step 01: Create the Folder

Infrastructure code lives next to the application it serves, in the same Git repository:

```
LanguagesPortfolio/
├── backend/
├── frontend/
├── k8s/
└── terraform/              ← NEW
    ├── providers.tf        ← Terraform version, AWS provider, default tags
    ├── variables.tf        ← Inputs
    ├── main.tf             ← The server, its firewall, and its key
    ├── cloud-init.yaml     ← First-boot setup
    ├── outputs.tf          ← Values printed after apply
    └── terraform.tfvars    ← Your personal values (not committed)
```

### Step 02: `providers.tf`

```
terraform {
  required_version = ">= 1.11"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
provider "aws" {
  region = var.aws_region
  # Added to every resource this configuration creates
  default_tags {
    tags = {
      Project   = var.project_name
      ManagedBy = "terraform"
    }
  }
}
```

*   `**required_version**` stops older Terraform versions from running this code. 1.11 is the minimum for the state locking in Section 9.
*   `**~> 6.0**` allows any 6.x release of the AWS provider, but never 7.0. Major versions can contain breaking changes, so you upgrade on purpose, not by accident.
*   `**default_tags**` labels everything. In the console and on your bill, you can see at a glance what Terraform created.

### Step 03: `variables.tf`

```
variable "aws_region" {
  description = "AWS region to build in"
  type        = string
  default     = "us-west-2"
}
variable "project_name" {
  description = "Prefix for resource names, and the Project tag"
  type        = string
  default     = "languages-portfolio"
}
variable "instance_type" {
  description = "EC2 size. x86 types (t3, t2) only; ARM types like t4g need an arm64 image."
  type        = string
  default     = "t3.micro"
}
variable "ssh_allowed_cidr" {
  description = "Your public IP in CIDR form, such as 203.0.113.25/32"
  type        = string
  validation {
    condition     = can(cidrhost(var.ssh_allowed_cidr, 0)) && var.ssh_allowed_cidr != "0.0.0.0/0"
    error_message = "Set ssh_allowed_cidr to your own IP address as a CIDR block, such as 203.0.113.25/32."
  }
}
variable "public_key_path" {
  description = "SSH public key to install on the server"
  type        = string
  default     = "~/.ssh/portfolio-key.pub"
}
```

*   A variable with a `default` is optional. `ssh_allowed_cidr` has none, so it's required, and Terraform asks for it if you don't supply it.
*   The **validation** block checks the value before Terraform calls AWS. `cidrhost` fails on anything that isn't a CIDR block, and `can` turns that failure into `false`. The second check refuses `0.0.0.0/0`, which would open SSH to the entire internet.
*   Post 8 used `t2.micro`. `t3.micro` is the newer generation with the same 1 GiB of memory. If your account's Free Tier covers different types, change the default. The EC2 console labels the eligible ones.

### Step 04: `main.tf`

```
# Find the newest Ubuntu 24.04 LTS image published by Canonical
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical's AWS account
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd*/ubuntu-noble-24.04-amd64-server-*"]
  }
}
# Upload your public key. The private key stays on your laptop.
resource "aws_key_pair" "portfolio" {
  key_name   = "${var.project_name}-key"
  public_key = file(pathexpand(var.public_key_path))
}
# The cloud firewall around the server
resource "aws_security_group" "web" {
  name        = "${var.project_name}-web"
  description = "Portfolio web server"
}
resource "aws_vpc_security_group_ingress_rule" "ssh" {
  security_group_id = aws_security_group.web.id
  description       = "SSH from my IP only"
  cidr_ipv4         = var.ssh_allowed_cidr
  ip_protocol       = "tcp"
  from_port         = 22
  to_port           = 22
}
resource "aws_vpc_security_group_ingress_rule" "web" {
  for_each = { http = 80, https = 443 }
  security_group_id = aws_security_group.web.id
  description       = upper(each.key)
  cidr_ipv4         = "0.0.0.0/0"
  ip_protocol       = "tcp"
  from_port         = each.value
  to_port           = each.value
}
resource "aws_vpc_security_group_egress_rule" "all" {
  security_group_id = aws_security_group.web.id
  description       = "All outbound traffic"
  cidr_ipv4         = "0.0.0.0/0"
  ip_protocol       = "-1" # all protocols and ports
}
```

The server goes at the bottom of the same file:

```
resource "aws_instance" "portfolio" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = var.instance_type
  key_name               = aws_key_pair.portfolio.key_name
  vpc_security_group_ids = [aws_security_group.web.id]
  # First-boot setup, applied by cloud-init on the server
  user_data                   = file("${path.module}/cloud-init.yaml")
  user_data_replace_on_change = true
  root_block_device {
    volume_size = 20 # GB; the default 8 GB fills up fast with Docker images
    volume_type = "gp3"
    encrypted   = true
  }
  metadata_options {
    http_tokens = "required" # only allow the more secure IMDSv2
  }
  tags = {
    Name = "${var.project_name}-server"
  }
}
```

What each part does:

*   `**data "aws_ami" "ubuntu"**` searches for images whose names match the pattern and picks the newest. `owners` restricts the search to Canonical, the company behind Ubuntu, so you can't pick up a look-alike image someone else published. Image IDs differ in every region, and the data source finds the right one for yours.
*   `**aws_key_pair**` reads your public key. `pathexpand` turns `~` into your home folder, and `file` reads the contents.
*   **The security group and its rules.** Each rule is a separate resource. `for_each` creates one rule per map entry, addressed as `aws_vpc_security_group_ingress_rule.web["http"]` and `web["https"]`. When Terraform creates a security group, it removes the allow-all outbound rule AWS normally adds, so the egress rule puts it back. Without it, the server couldn't download Docker.
*   **No VPC or subnet is specified,** so AWS uses your default VPC, and its default subnets give the server a public IP address.
*   `**user_data**` hands a setup file to cloud-init, the first-boot tool built into Ubuntu cloud images. `path.module` is the folder containing this `.tf` file, so Terraform still finds `cloud-init.yaml` when this code becomes a module in Section 10. The setup only runs on first boot, so `user_data_replace_on_change` tells Terraform to build a new server whenever you change it.
*   `**metadata_options**` requires session tokens for the instance metadata service (IMDSv2). This blocks a common attack where a vulnerable web app is tricked into fetching the server's metadata, which can include credentials.
*   `**encrypted = true**` encrypts the disk at rest at no extra cost.

### Step 05: `cloud-init.yaml`

```
#cloud-config
# cloud-init applies this once, the first time the server boots.
package_update: true    # apt-get update
packages:               # apt-get install
  - git
  - docker.io
runcmd:
  # Let the ubuntu user run docker without sudo
  - usermod -aG docker ubuntu
```

The first line must be exactly `#cloud-config`, which tells cloud-init this is configuration rather than a script.

Like Terraform, it's declarative: you list the packages you want and cloud-init installs them.

In Post 8 you used Docker's convenience script. Here we use Ubuntu's own `docker.io` package.

Terraform's job ends when AWS reports the server is running, and cloud-init keeps working in the background for a few more minutes.

### Step 06: `outputs.tf`

```
output "public_ip" {
  value = aws_instance.portfolio.public_ip
}
output "ssh_command" {
  description = "Paste this to connect (assumes the default key path)"
  value       = "ssh -i $HOME/.ssh/portfolio-key ubuntu@${aws_instance.portfolio.public_ip}"
}
output "website_url" {
  value = "http://${aws_instance.portfolio.public_ip}"
}
```

`${...}` inserts a value into a string. `$HOME` has no braces, so Terraform leaves it alone, and PowerShell or Bash expands it to your home folder when you paste the command.

### Step 07: `terraform.tfvars`

```
ssh_allowed_cidr = "203.0.113.25/32" # your IP from "Before You Start", plus /32
```

Terraform loads `terraform.tfvars` automatically. `/32` means "exactly this one address". The file holds personal details, so it stays out of Git.

### Step 08: Plan and Apply

```
# In LanguagesPortfolio\terraform
terraform init       # download the AWS provider, write the lock file
terraform fmt        # rewrite files in the standard style
terraform validate   # check syntax and references without calling AWS
terraform plan
```

`validate` prints `**Success! The configuration is valid**.`

Then `plan` reads the Ubuntu image and lists seven resources to create: the key pair, the security group, four rules (SSH, HTTP, HTTPS, outbound), and the instance.

```
data.aws_ami.ubuntu: Read complete after 1s [id=ami-0123456789abcdef0]
...
Plan: 7 to add, 0 to change, 0 to destroy.
```

If the plan looks right, apply it:

```
terraform apply
```

```
aws_key_pair.portfolio: Creating...
aws_security_group.web: Creating...
aws_security_group.web: Creation complete after 3s
aws_vpc_security_group_ingress_rule.ssh: Creating...
aws_instance.portfolio: Creating...
...
Apply complete! Resources: 7 added, 0 changed, 0 destroyed.
Outputs:
public_ip = "54.x.x.x"
ssh_command = "ssh -i $HOME/.ssh/portfolio-key ubuntu@54.x.x.x"
website_url = "http://54.x.x.x"
```

The key pair and security group start together because neither depends on the other. The rules and the instance wait for the security group. That’s the dependency graph at work.

> **_Your server exists. You can run the same command next month, in another region, or in another account and get an identical result._**

8 | Deploy Your Portfolio onto the Server
-----------------------------------------

Terraform built the infrastructure. Now deploy the application.

```
  terraform output -raw ssh_command
  # Copy the command it prints, paste it, and press Enter.
```

Type `yes` the first time to trust the server's fingerprint. Then wait for cloud-init to finish:

```
cloud-init status --wait     # prints "status: done" when setup is complete
exit
```

SSH in again, because your new membership in the `docker` group only applies to new logins. Then clone your repository from and start the containers:

```
git clone https://github.com/YOUR-USERNAME/LanguagesPortfolio.git
cd LanguagesPortfolio
# A private network where containers find each other by name
docker network create portfolio
# Backend first: Nginx looks up its name when it starts
docker build -t portfolio-backend ./backend
docker run -d --name backend-service --network portfolio --restart unless-stopped portfolio-backend
# Frontend, published on port 80
docker build -t portfolio-frontend ./frontend
docker run -d --name frontend --network portfolio --restart unless-stopped -p 80:80 portfolio-frontend
curl http://localhost/api/health
```

Why these names and this order:

*   `frontend/nginx.conf` forwards `/api/` to `http://backend-service:5000`, the Kubernetes Service name from Post 10. On a Docker network, containers reach each other by container name, so naming the backend container `backend-service` makes the same config work here. Docker's built-in DNS does the job Kubernetes DNS did in Post 10.
*   Nginx looks up `backend-service` when it starts and exits if the name doesn't exist yet, so the backend must start first.
*   `--restart unless-stopped` brings both containers back after a reboot.
*   Only the frontend publishes a port. Flask is reachable only through Nginx, which is why port 5000 is closed.

Back on your laptop, in the `terraform` folder:

```
curl.exe "$(terraform output -raw website_url)/api/health"
Start-Process (terraform output -raw website_url)
```

Your portfolio is live on a server Terraform built. Port 443 is already open for when you add an HTTPS certificate until then the site uses plain HTTP. If your repository is private, `git clone` needs a GitHub personal access token instead of your password.

**💡Notice the split: Terraform built the server, and you deployed the app.** Real teams split the work the same way. Terraform manages infrastructure, and a CI/CD pipeline, like the GitHub Actions workflow in your repository’s `.github/workflows` folder, deploys the application.

9 | Drift, Replacement, and Destroy
-----------------------------------

### Drift: Someone Changes Things by Hand

In the EC2 console, select `languages-portfolio-server` and rename it on the **Tags** tab. Then run `terraform plan`:

```
# aws_instance.portfolio will be updated in-place
  ~ resource "aws_instance" "portfolio" {
      ~ tags = {
          ~ "Name" = "renamed-in-console" -> "languages-portfolio-server"
        }
    }
Plan: 0 to add, 1 to change, 0 to destroy.
```

Terraform found the difference and plans to put it back. Run `terraform apply` to undo the manual change.

This is why teams change infrastructure only through code: the code stays the single source of truth, and `plan` exposes anything that doesn't match it.

### Replacement: Some Changes Need a New Server

Add a package to `cloud-init.yaml`, such as `- htop` under `packages`, and run `terraform plan`:

```
# aws_instance.portfolio must be replaced
-/+ resource "aws_instance" "portfolio" {
      ~ id        = "i-0abc123def4567890" -> (known after apply)
      ~ public_ip = "54.x.x.x" -> (known after apply)
      ~ user_data = "..." -> "..." # forces replacement
    }
Plan: 1 to add, 0 to change, 1 to destroy
```

The setup file only runs on first boot, so changing it on a running server would do nothing.

Because of `user_data_replace_on_change`, Terraform plans a new server instead.

The new server gets a new IP address and starts without your containers, so you'd repeat Section 7 after applying.

> **_That's the mindset IaC encourages: servers are replaceable, because the code can rebuild them at any time._**

⚠️**Undo the edit before continuing unless you want to try it.**

### Destroy: Pay Only for What You Use

```
terraform destroy
```

```
Plan: 0 to add, 0 to change, 7 to destroy.
...
Destroy complete! Resources: 7 destroyed.
```

The instance, security group, and key pair are gone. The Ubuntu image is untouched, because a data source only reads.

When you want the server back, run `terraform apply` and repeat previous steps. Your whole server is now a few files in Git that you can rebuild in minutes.

10 | State: Terraform’s Memory
------------------------------

The state file maps each resource in your code to a real object in AWS:

*   **In your code**, the resource `aws_instance.portfolio` corresponds to the ID `i-0abc123def4567890` **in the state file** and represents **The EC2 instance** **in AWS**.
*   **In your code**, the resource `aws_security_group.web` corresponds to the ID `sg-0123abcd4567ef890` **in the state file** and represents **The security group** **in AWS**.
*   **In your code**, the resource `aws_key_pair.portfolio` corresponds to the ID `languages-portfolio-key` **in the state file** and represents **The key pair** **in AWS**.

Without it, Terraform couldn’t tell “the server I created” from “a server someone else created”. Three rules:

1.  **Never edit it by hand.** Use `terraform state list` and `terraform state show aws_instance.portfolio` to inspect it.

**2. Never commit it to Git.** State stores every attribute in plain text, including passwords for resources such as databases. `sensitive = true` hides a value in plan output, but not in state.

**3. Don’t lose it.** Without state, Terraform forgets what it created. The next `apply` tries to create everything again and fails with "already exists" errors, while the old resources keep running and billing you.

Renaming a resource in code (say, `portfolio` to `web`) looks to Terraform like deleting one server and creating another. A `moved` block updates the state instead:

```
moved {
  from = aws_instance.portfolio
  to   = aws_instance.web
}
```

To bring something you created by hand under Terraform’s control, write a matching resource block and run `terraform import`. For example, you could import your original Post 8 security group instead of creating a new one.

### Remote State for Teams

Local state works for one person on one laptop. Teams and CI pipelines need state in a shared place, with **locking** so two people running `apply` at once can't corrupt it. On AWS, the standard is an S3 bucket. Create it once. Bucket names are global across all AWS accounts, so include your name:

```
$bucket = "yourname-portfolio-tfstate"
aws s3api create-bucket --bucket $bucket --region us-west-2 --create-bucket-configuration LocationConstraint=us-west-2
aws s3api put-bucket-versioning --bucket $bucket --versioning-configuration Status=Enabled
```

New S3 buckets block public access and encrypt objects by default. Versioning keeps every earlier copy of the state, so you can recover from a bad apply. (In `us-east-1`, leave out `--create-bucket-configuration`.)

Add a `backend` block inside the `terraform` block in `providers.tf`:

```
terraform {
  required_version = ">= 1.11"
  backend "s3" {
    bucket       = "yourname-portfolio-tfstate"
    key          = "languages-portfolio/terraform.tfstate"
    region       = "us-west-2"
    encrypt      = true
    use_lockfile = true # S3-native state locking
  }
  required_providers {
    # ...unchanged
  }
}
```

Then move your state into it. This works whether or not your server is running:

```
terraform init -migrate-state
```

Answer `yes` when Terraform asks to copy the existing state. From now on, every plan and apply reads and writes state in S3.

During a run, Terraform keeps a lock file ending in `.tflock` next to the state, and a second run at the same time stops with `Error acquiring the state lock` instead of corrupting anything.

Two details trip people up:

*   **The backend block can’t use variables.** Terraform reads it before anything else, so the values must be written out.
*   **This configuration doesn’t manage its own bucket.** Terraform needs somewhere to store state before it can create anything, so the bucket is created once, separately.

Older tutorials lock state with a DynamoDB table. That option is deprecated now that S3 can lock state by itself, which is why `required_version` is 1.11. To delete the bucket later, choose **Empty** in the S3 console (this removes every version), then delete it.

11 | Modules: Reusable Infrastructure
-------------------------------------

Every folder of `.tf` files is a **module**, and the folder you've been working in is the **root module**.

Modules work like functions: variables are the inputs, resources are the body, and outputs are the return values.

To get identical staging and production servers, move `main.tf`, `outputs.tf`, and `cloud-init.yaml` into `modules/web-server/`, give that folder a `variables.tf` with a `name` input (replacing `var.project_name`), and call it twice from the root:

```
# terraform/main.tf
module "staging" {
  source           = "./modules/web-server"
  name             = "portfolio-staging"
  instance_type    = "t3.micro"
  ssh_allowed_cidr = var.ssh_allowed_cidr
  public_key_path  = var.public_key_path
}
module "production" {
  source           = "./modules/web-server"
  name             = "portfolio-prod"
  instance_type    = "t3.small"
  ssh_allowed_cidr = var.ssh_allowed_cidr
  public_key_path  = var.public_key_path
}
output "staging_url" {
  value = module.staging.website_url
}
```

This is where `path.module` pays off: inside the module, it points to `modules/web-server/`, so each copy finds its own `cloud-init.yaml`.

Moving resources into a module changes their addresses (`aws_instance.portfolio` becomes `module.staging.aws_instance.portfolio`), so add `moved` blocks if you want to keep a server that's already running.

You don’t need modules for one server. They pay off when you repeat a pattern. The [Terraform Registry](https://registry.terraform.io/browse/modules) also has community modules for common building blocks, such as a complete VPC with public and private subnets.

### ⛔ End of Building Tutorial⛔

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Terraform in the Cloud](https://ntombizakhona.medium.com/terraform-in-the-cloud-337b9bdbbc0b)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**04 November 2026**
