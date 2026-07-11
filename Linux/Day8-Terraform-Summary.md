# Day 8 Terraform Learning Summary
### Finland DevOps Journey — Karthiveer
**GitHub:** github.com/Karthiveer
**Date:** July 10, 2026

---

## What You Accomplished on Day 8

- Installed Terraform on Mac via Homebrew
- Wrote first Infrastructure as Code file (main.tf)
- Initialized Terraform project with AWS provider
- Formatted and validated Terraform config
- Ran terraform plan — previewed infrastructure before creating
- Deployed real AWS S3 bucket using only code
- Created variables.tf — made infrastructure reusable
- Created outputs.tf — exposed resource values
- Understood Terraform state — how infrastructure is tracked
- Updated infrastructure safely — no downtime
- Destroyed infrastructure cleanly with one command
- Configured AWS CLI on Mac with access keys
- Learned ARN — Amazon Resource Name concept
- Handled security incident — deleted exposed access key immediately

---

## READY-MADE PROMPT FOR NEXT AI SESSION

Copy and paste this into any AI platform to continue from Day 9:

---

Hi! I am Karthiveer from Chennai, India. I am on a 6-month focused journey to get a Cloud DevOps job in Finland (Helsinki). Please continue my day-by-day learning from Day 9.

COMPLETED SO FAR:
- Day 1: Linux CLI — Navigation, Files, Permissions, Processes, Bash Scripting
- Day 2: grep, find, pipes, networking, GitHub push
- Day 3: AWS EC2 launch, SSH, Nginx web server, Security Groups, live webpage
- Day 4: Environment variables, Cron jobs, AWS CLI v2, AWS IAM basics
- Day 5: Advanced Bash scripting, AWS S3 storage, System monitoring, Nginx advanced
- Day 6: Docker install, images, containers, Dockerfile, custom image build, management
- Day 7: Docker Hub push, Docker networking, Docker volumes, Docker Compose
- Day 8: Terraform install, first IaC config, variables, outputs, state, deploy and destroy

CONTINUE FROM: Day 9 — Terraform advanced — deploy EC2 with Terraform, VPC, security groups as code, Terraform modules

MY COMPLETE SETUP:
- MacBook Air (Apple Silicon, zsh shell)
- Homebrew 6.0.5, Git 2.50.1 installed
- Terraform installed on Mac — v1.x.x
- AWS CLI configured on Mac with access keys
- Terraform project: ~/terraform-projects/finland-infra/
- Files: main.tf, variables.tf, outputs.tf
- SSH shortcut: ssh finland-server
- AWS EC2: finland-devops-server, Elastic IP, eu-north-1 Stockholm
- Ubuntu 24.04 LTS, t3.micro free tier
- Nginx running on port 80 — personal webpage live
- Docker 29.6.1 — custom image karthiveer-app:v1 on Docker Hub
- Docker Hub: hub.docker.com/r/karthiveer/karthiveer-app
- GitHub: github.com/Karthiveer/finland-journey
- Total XP earned: 4,795 XP across 8 days

LEARNING STYLE: Gamified with XP points and levels, theory first (2-3 mins), then hands-on terminal practice, screenshot errors for help, daily GitHub push.

Please start Day 9 with a quick recap of Day 8 then launch the gamified missions with interactive game widget!

---

END OF PROMPT

---

## Mission 1 — Install Terraform

### Installation on Mac

```bash
# Add HashiCorp repository to Homebrew
brew tap hashicorp/tap

# Install Terraform
brew install hashicorp/tap/terraform

# Verify installation
terraform --version
# Output: Terraform v1.x.x

# Create projects folder
mkdir ~/terraform-projects && cd ~/terraform-projects

# Create first project folder
mkdir finland-infra && cd finland-infra

# Explore available commands
terraform -help
```

### Key Terraform Commands Overview

| Command | Purpose |
|---------|---------|
| terraform init | Download provider plugins |
| terraform fmt | Format config files |
| terraform validate | Check syntax |
| terraform plan | Preview changes |
| terraform apply | Deploy infrastructure |
| terraform destroy | Remove infrastructure |
| terraform show | View current state |
| terraform output | Show output values |

---

## Mission 2 — First Terraform Config

### Your main.tf File

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "eu-north-1"
}

resource "aws_s3_bucket" "finland_bucket" {
  bucket = "karthiveer-terraform-finland"
  tags = {
    Name        = "Finland DevOps Bucket"
    Environment = "Learning"
    Owner       = "Karthiveer"
    Journey     = "Chennai-to-Helsinki"
  }
}
```

### Terraform File Structure Explained

```
terraform {}        = Terraform settings block
required_providers  = Which cloud providers to use
provider "aws" {}   = Configure AWS (region, credentials)
resource "type" "name" {} = Infrastructure to create
```

### Commands Run in Order

```bash
# Step 1 — Initialize project
terraform init
# Downloads AWS provider plugin
# Creates .terraform/ folder and .terraform.lock.hcl

# Step 2 — Format config
terraform fmt
# Auto-formats indentation and spacing

# Step 3 — Validate syntax
terraform validate
# Success! The configuration is valid.

# Step 4 — Preview changes
terraform plan
# Shows + create for each resource to be created
# No actual changes made

# Step 5 — Deploy!
terraform apply
# Type yes to confirm
# Creates real S3 bucket in eu-north-1

# Step 6 — View created infrastructure
terraform show
# Shows all attributes of created resources
```

### What terraform plan Output Means

```
+ resource "aws_s3_bucket" "finland_bucket" {
    + bucket = "karthiveer-terraform-finland"
    + tags   = {
        + "Journey" = "Chennai-to-Helsinki"
      }
  }

+ = will be created (green)
~ = will be modified (yellow)
- = will be destroyed (red)
```

---

## Mission 3 — Terraform Variables

### variables.tf

```hcl
variable "environment" {
  description = "Deployment environment"
  type        = string
  default     = "learning"
}

variable "aws_region" {
  description = "AWS region to deploy"
  type        = string
  default     = "eu-north-1"
}

variable "owner_name" {
  description = "Owner of the infrastructure"
  type        = string
  default     = "Karthiveer"
}

variable "project_name" {
  description = "Project name for tagging"
  type        = string
  default     = "finland-journey"
}
```

### outputs.tf

```hcl
output "bucket_name" {
  description = "Name of the S3 bucket created"
  value       = aws_s3_bucket.finland_bucket.bucket
}

output "bucket_arn" {
  description = "ARN of the S3 bucket"
  value       = aws_s3_bucket.finland_bucket.arn
}

output "aws_region" {
  description = "AWS region deployed to"
  value       = var.aws_region
}
```

### Using Variables

```bash
# Apply with default variables
terraform apply -auto-approve

# See output values
terraform output
# bucket_name = "karthiveer-terraform-finland"
# bucket_arn  = "arn:aws:s3:::karthiveer-terraform-finland"
# aws_region  = "eu-north-1"

# Override variable at runtime
terraform apply -var="environment=production" -auto-approve

# Override multiple variables
terraform apply \
  -var="environment=staging" \
  -var="owner_name=Karthiveer" \
  -auto-approve
```

### Variables vs Hardcoding

```hcl
# BAD — hardcoded, not reusable
resource "aws_s3_bucket" "bucket" {
  bucket = "my-bucket"
  tags = {
    Environment = "learning"  # Can't change without editing file
  }
}

# GOOD — uses variables, reusable across environments
resource "aws_s3_bucket" "bucket" {
  bucket = "my-bucket"
  tags = {
    Environment = var.environment  # Change with -var flag
  }
}
```

---

## Mission 4 — Terraform State

### What is Terraform State?

```
terraform.tfstate = Terraform's memory

Contains:
- Every resource Terraform created
- All resource attributes (IDs, ARNs, IPs)
- Relationships between resources
- Last known state of infrastructure

Without state: Terraform cannot update or destroy
With state: Terraform knows exactly what exists
```

### State Commands

```bash
# Read raw state file
cat terraform.tfstate

# List all resources in state
terraform state list
# aws_s3_bucket.finland_bucket

# Show detailed state of one resource
terraform state show aws_s3_bucket.finland_bucket
# Shows every attribute — ID, ARN, region, tags

# Move resource in state (rename)
terraform state mv old_name new_name

# Remove resource from state without destroying
terraform state rm aws_s3_bucket.finland_bucket
```

### Update Infrastructure Safely

```bash
# Edit main.tf — add new tag UpdatedBy = "Terraform"
# Then run plan to see what changes
terraform plan
# Shows ~ modify — tilde means UPDATE not recreate

# Apply the update
terraform apply -auto-approve
# Only updates tags — no downtime, no deletion
```

### Update vs Recreate

```
~ modify   = Update in place — no downtime ✅
-/+ replace = Destroy and recreate — brief downtime ⚠️
- destroy  = Permanently deleted ❌

Terraform always chooses least disruptive change
```

### Destroy Infrastructure

```bash
# Remove everything Terraform created
terraform destroy -auto-approve

# Verify deletion
aws s3 ls | grep karthiveer-terraform
# Returns nothing — bucket gone ✅
```

---

## Key Concepts Learned Day 8

### ARN — Amazon Resource Name

```
arn:aws:iam::438740431852:root
 │    │   │       │         │
 │    │   │       │         └── Resource type (root user)
 │    │   │       └──────────── Account ID
 │    │   └──────────────────── Service (iam)
 │    └──────────────────────── Cloud (aws)
 └───────────────────────────── Always "arn"

Real examples:
arn:aws:s3:::karthiveer-terraform-finland
arn:aws:ec2:eu-north-1:438740431852:instance/i-00bf77c994894938f
arn:aws:iam::438740431852:user/karthiveer
```

### Terraform vs Manual AWS Console

```
Manual Console:          Terraform:
Click 45 minutes         Write code 10 minutes
Hard to repeat           terraform apply = identical every time
No history               Git history forever
Easy to make mistakes    Validated before applying
Hard to destroy cleanly  terraform destroy = clean removal
```

### IaC Benefits Finnish Companies Use

| Benefit | How Finnish Teams Use It |
|---------|------------------------|
| Repeatability | Same infra for dev/staging/production |
| Version control | Git history of all infra changes |
| Code review | Team reviews infra changes like code |
| Automation | CI/CD pipelines run terraform apply |
| Cost control | terraform destroy dev environments overnight |

---

## Struggles and Solutions — Day 8

### Struggle 1 — Ran Homebrew on Server Instead of Mac
**Error:** `Command 'brew' not found`
**Cause:** SSH'd into Stockholm server — Homebrew only exists on Mac
**Fix:** Type `exit` to return to Mac, then run brew commands
**Lesson:** Always check your prompt — `ubuntu@ip` = server, `veersaru@Mac` = your Mac

### Struggle 2 — No Valid Credential Sources Found
**Error:** `Error: No valid credential sources found`
**Cause:** AWS CLI not configured on Mac — only configured on server
**Fix:**
```bash
brew install awscli
aws configure
# Enter Access Key ID and Secret Access Key
```
**Lesson:** Terraform runs on Mac — needs AWS credentials on Mac too

### Struggle 3 — SignatureDoesNotMatch Error
**Error:** `SignatureDoesNotMatch when calling GetCallerIdentity`
**Cause:** Same value entered for both Access Key ID AND Secret Access Key
**Fix:** They are two completely different values:
```
Access Key ID:     AKIA... (20 chars, starts with AKIA)
Secret Access Key: wJalr... (40 chars, completely different)
```
**Lesson:** Always copy both values separately from AWS Console

### Struggle 4 — Accidentally Exposed Access Key
**What happened:** Real AWS Access Key ID shared publicly in chat
**Immediate fix:**
1. AWS Console → Security Credentials
2. Find exposed key → Delete immediately
3. Create new key pair
4. Run aws configure with new keys
**Lesson:** Never share access keys anywhere — treat like passwords
Add to .gitignore: `**/.aws/credentials`

---

## AWS CLI Setup on Mac

```bash
# Install AWS CLI
brew install awscli

# Configure credentials
aws configure
# AWS Access Key ID: AKIA... (from AWS Console)
# AWS Secret Access Key: different longer value
# Default region: eu-north-1
# Default output format: json

# Verify it works
aws sts get-caller-identity
# Returns your account ID and ARN

# List S3 buckets
aws s3 ls

# List EC2 instances
aws ec2 describe-instances --region eu-north-1
```

---

## Your Terraform Project Structure

```
~/terraform-projects/
└── finland-infra/
    ├── main.tf           ← Infrastructure definition
    ├── variables.tf      ← Reusable variables
    ├── outputs.tf        ← Values to expose
    ├── terraform.tfstate ← State file (never commit!)
    └── .terraform/       ← Provider plugins (never commit!)
        └── providers/
```

### Add to .gitignore

```bash
# Add these to your .gitignore
echo "*.tfstate" >> .gitignore
echo "*.tfstate.backup" >> .gitignore
echo ".terraform/" >> .gitignore
echo "*.tfvars" >> .gitignore
```

---

## Day 8 XP Earned

| Mission | Topic | XP |
|---------|-------|-----|
| Mission 1 | Install Terraform | 240 XP |
| Mission 2 | First Terraform Config | 405 XP |
| Mission 3 | Variables and Outputs | 280 XP |
| Mission 4 | Terraform State | 320 XP |
| **Day 8 Total** | | **720 XP** |

---

## Cumulative XP — 8 Days

| Day | Topic | XP |
|-----|-------|-----|
| Day 1 | Linux CLI | 555 XP |
| Day 2 | grep, Pipes, GitHub | 480 XP |
| Day 3 | AWS EC2, Nginx | 560 XP |
| Day 4 | Env Vars, Cron, AWS CLI, IAM | 560 XP |
| Day 5 | Bash Scripts, S3, Monitoring | 600 XP |
| Day 6 | Docker Fundamentals | 640 XP |
| Day 7 | Docker Advanced | 680 XP |
| Day 8 | Terraform | 720 XP |
| **Total** | | **4,795 XP** |

---

## Day 9 Preview — Terraform Advanced 🏗️

| Topic | What You Will Build |
|-------|-------------------|
| Terraform EC2 | Deploy EC2 instance with code |
| Terraform VPC | Create network infrastructure as code |
| Security Groups | Firewall rules in Terraform |
| Terraform Modules | Reusable infrastructure components |
| Remote State | Store state in S3 — team collaboration |

---

## LinkedIn Milestone Post

> Infrastructure as Code — Day 8 complete 🏗️
>
> Today on my Finland DevOps journey I wrote code
> that deployed real AWS infrastructure:
>
> — Wrote main.tf → terraform apply → S3 bucket appeared in Stockholm
> — Made it reusable with variables.tf
> — Exposed values with outputs.tf
> — Updated infrastructure with zero downtime
> — Destroyed everything cleanly with one command
>
> No clicking in AWS Console.
> Just code, plan, apply.
>
> This is how Finnish DevOps teams manage
> infrastructure at Nokia, Wolt, and Reaktor.
>
> Chennai → Helsinki 🇫🇮
> github.com/Karthiveer
>
> #Terraform #IaC #DevOps #AWS #Finland #LearningInPublic

---

## Push to GitHub

```bash
cd ~/finland-journey/linux
git add .
git commit -m "Day 8 - Terraform IaC fundamentals complete"
git push
```

---

*Finland DevOps Journey — Karthiveer*
*github.com/Karthiveer*
*Chennai → Helsinki 🇫🇮*
*Day 8 completed: July 10, 2026*
